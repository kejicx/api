# Cloudflare API 接口文档

> **站点**：Cloudflare（cloudflare.com）
> **数据来源**：Cloudflare 官方公开 REST API 文档（developers.cloudflare.com/api，API v4）
> **整理时间**：2026-10-05
> **提取方式**：解析官方文档站全部资源页（1110 个 index.md），逐条提取 HTTP 方法与请求路径，按 (方法, 路径) 去重，并将英文接口名整理翻译为中文用途
> **接口总数**：2513 条（去重后唯一 方法+路径；官方文档原始列举 5872 条含父子资源重复列举）
> **唯一路径**：1532 条
> **方法分布**：GET 1199、POST 532、PUT 267、DELETE 336、PATCH 179
> **业务大类**：16 个（由 123 个官方资源组归并）
> **说明**：本文档仅对 Cloudflare 官方公开的 REST API 接口做整理与中文翻译，非前端逆向抓取；接口定义、参数与鉴权以 Cloudflare 官方文档（developers.cloudflare.com/api）为准。

## 一、通用约定

| 项目 | 约定 |
| --- | --- |
| 主 API 入口 | `https://api.cloudflare.com/client/v4`（当前稳定版本 v4） |
| 鉴权方式 | 推荐 `Authorization: Bearer <API Token>`；传统全局密钥用 `X-Auth-Email: <邮箱>` + `X-Auth-Key: <Global API Key>`；服务密钥用 `X-Auth-User-Service-Key` |
| 内容协商 | 请求与响应均为 `Content-Type: application/json` |
| 资源作用域 | 多数接口以 `/accounts/{account_id}/...` 或 `/zones/{zone_id}/...` 定位；`{accounts_or_zones}` 表示账户级与区域级二选一 |
| 统一响应信封 | `{"success": true/false, "errors": [], "messages": [], "result": ..., "result_info": {...}}` |
| 分页 | 查询参数 `page`、`per_page`、`order`、`direction`；返回 `result_info.total_count`；部分资源使用 `cursor` 游标分页 |
| 速率限制 | 认证请求约 1200 次 / 5 分钟 / IP；超限返回 HTTP 429 |
| 错误码 | HTTP 状态码 + `errors[].code`（如 1003 区域不存在、7003 未授权）与 `errors[].message` |
| 幂等语义 | GET 读取、POST 创建/触发、PUT 整体替换、PATCH 部分更新、DELETE 删除 |

**请求方法语义对照**：

| 方法 | 语义 | 典型请求格式 |
| --- | --- | --- |
| GET | 读取资源或列表 | Query |
| POST | 创建资源或触发操作 | JSON |
| PUT | 整体更新或替换资源 | JSON |
| PATCH | 部分更新资源字段 | JSON |
| DELETE | 删除资源 | Query |

> 注：下表"请求格式"按 HTTP 方法语义推断（GET/DELETE 走 Query 参数，POST/PUT/PATCH 走 JSON 请求体）；个别上传/批量类接口实际接受 multipart，请以官方文档为准。

## 零信任安全（Zero Trust）（438 条）

> Cloudflare One 零信任平台：访问控制 Access、安全 Web 网关 Gateway、浏览器隔离、设备管理、DLP、隧道连接器等。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/ai-controls/mcp/portals | GET | Query | JSON | 列出 MCP 门户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/ai-controls/mcp/portals | POST | JSON | JSON | 创建新 MCP 门户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/ai-controls/mcp/portals/{id} | DELETE | Query | JSON | 删除 MCP 门户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/ai-controls/mcp/portals/{id} | GET | Query | JSON | 读取详情的 MCP 门户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/ai-controls/mcp/portals/{id} | PUT | JSON | JSON | 更新 MCP 门户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/ai-controls/mcp/servers | GET | Query | JSON | 列出 MCP 服务器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/ai-controls/mcp/servers | POST | JSON | JSON | 创建新 MCP 服务器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/ai-controls/mcp/servers/{id} | DELETE | Query | JSON | 删除 MCP 服务器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/ai-controls/mcp/servers/{id} | GET | Query | JSON | 读取详情的 MCP 服务器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/ai-controls/mcp/servers/{id} | PUT | JSON | JSON | 更新 MCP 服务器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/ai-controls/mcp/servers/{id}/sync | POST | JSON | JSON | 同步 MCP 服务器 Capabilities |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/custom_pages | GET | Query | JSON | 列出自定义 pages |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/custom_pages | POST | JSON | JSON | 创建自定义寻呼 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/custom_pages/{custom_page_id} | DELETE | Query | JSON | 删除自定义寻呼 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/custom_pages/{custom_page_id} | GET | Query | JSON | 获取自定义寻呼 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/custom_pages/{custom_page_id} | PUT | JSON | JSON | 更新自定义寻呼 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/gateway_ca | GET | Query | JSON | 列出 SSH 证书权威 (CA) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/gateway_ca | POST | JSON | JSON | 添加新 SSH 证书权威 (CA) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/gateway_ca/{certificate_id} | DELETE | Query | JSON | 删除 SSH 证书权威 (CA) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/identity_providers/{identity_provider_id}/saml_certificate | POST | JSON | JSON | 创建 SAML encryption 证书用于身份提供方 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/identity_providers/{identity_provider_id}/scim/groups | GET | Query | JSON | 列出 SCIM 组资源 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/identity_providers/{identity_provider_id}/scim/users | GET | Query | JSON | 列出 SCIM 用户资源 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/idp_federation_grants | GET | Query | JSON | 列出 IdP 联合 grants |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/idp_federation_grants | POST | JSON | JSON | 创建 IdP 联合授予 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/idp_federation_grants/{grant_id} | DELETE | Query | JSON | 删除 IdP 联合授予 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/idp_federation_grants/{grant_id} | GET | Query | JSON | 获取 IdP 联合授予 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/keys | GET | Query | JSON | 获取访问键配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/keys | PUT | JSON | JSON | 更新访问键配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/keys/rotate | POST | JSON | JSON | 轮换访问密钥 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/logs/access_requests | GET | Query | JSON | 获取访问认证日志 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/logs/scim/updates | GET | Query | JSON | 列出访问 SCIM 更新日志 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/organizations/doh | GET | Query | JSON | 获取你的零信任组织 DoH 设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/organizations/doh | PUT | JSON | JSON | 更新你的零信任组织 DoH 设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/policies | GET | Query | JSON | 列出访问可复用策略 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/policies | POST | JSON | JSON | 创建访问可复用策略 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/policies/{policy_id} | DELETE | Query | JSON | 删除访问可复用策略 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/policies/{policy_id} | GET | Query | JSON | 获取访问可复用策略 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/policies/{policy_id} | PUT | JSON | JSON | 更新访问可复用策略 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/policy-tests | POST | JSON | JSON | 启动访问策略测试 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/policy-tests/{policy_test_id} | GET | Query | JSON | 获取当前状态的指定访问策略测试 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/policy-tests/{policy_test_id}/users | GET | Query | JSON | 获取访问策略测试用户寻呼 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/saml_certificates | GET | Query | JSON | 列出 SAML 证书设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/saml_certificates/{saml_cert_set_id} | GET | Query | JSON | 获取 SAML 证书设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/saml_certificates/{saml_cert_set_id}/pem | GET | Query | JSON | 下载当前证书内 PEM 格式 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/saml_certificates/{saml_cert_set_id}/rotate | POST | JSON | JSON | 轮换 SAML 证书 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/seats | PATCH | JSON | JSON | 更新用户席位 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/service_tokens/{service_token_id}/refresh | POST | JSON | JSON | 刷新服务令牌 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/service_tokens/{service_token_id}/rotate | POST | JSON | JSON | 轮换服务令牌 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/tags | GET | Query | JSON | 列出标签 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/tags | POST | JSON | JSON | 创建标签 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/tags/{tag_name} | DELETE | Query | JSON | 删除标签 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/tags/{tag_name} | GET | Query | JSON | 获取标签 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/tags/{tag_name} | PUT | JSON | JSON | 更新标签 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/users | GET | Query | JSON | 获取用户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/users | POST | JSON | JSON | 创建用户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/users/{user_id} | DELETE | Query | JSON | 删除用户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/users/{user_id} | GET | Query | JSON | 获取用户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/users/{user_id} | PUT | JSON | JSON | 更新用户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/users/{user_id}/active_sessions | GET | Query | JSON | 获取活跃会话 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/users/{user_id}/active_sessions/{nonce} | GET | Query | JSON | 获取单个活跃会话 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/users/{user_id}/failed_logins | GET | Query | JSON | 获取失败 logins |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/access/users/{user_id}/last_seen_identity | GET | Query | JSON | 获取最近 seen 身份 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cfd_tunnel | GET | Query | JSON | 列出 Cloudflare 隧道 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cfd_tunnel | POST | JSON | JSON | 创建 Cloudflare 隧道 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cfd_tunnel/{tunnel_id} | DELETE | Query | JSON | 删除 Cloudflare 隧道 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cfd_tunnel/{tunnel_id} | GET | Query | JSON | 获取 Cloudflare 隧道 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cfd_tunnel/{tunnel_id} | PATCH | JSON | JSON | 更新 Cloudflare 隧道 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cfd_tunnel/{tunnel_id}/configurations | GET | Query | JSON | 获取隧道配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cfd_tunnel/{tunnel_id}/configurations | PUT | JSON | JSON | 更新隧道配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cfd_tunnel/{tunnel_id}/connections | DELETE | Query | JSON | Clean up Cloudflare 隧道连接 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cfd_tunnel/{tunnel_id}/connections | GET | Query | JSON | 列出 Cloudflare 隧道连接 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cfd_tunnel/{tunnel_id}/connectors/{connector_id} | GET | Query | JSON | 获取 Cloudflare 隧道连接器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cfd_tunnel/{tunnel_id}/management | POST | JSON | JSON | 获取 Cloudflare 隧道管理令牌 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cfd_tunnel/{tunnel_id}/token | GET | Query | JSON | 获取 Cloudflare 隧道令牌 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/content | GET | Query | JSON | 列出 DLP 内容发现 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/content/export | POST | JSON | JSON | 创建内容导出 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/exports | GET | Query | JSON | 列出全部导出任务 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/exports/{id} | GET | Query | JSON | 获取单个导出任务 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/finding_types | GET | Query | JSON | 列出全部发现类型 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/finding_types/{finding_type_id} | GET | Query | JSON | 获取发现按 ID |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/finding_types/{finding_type_id}/remediation_types | GET | Query | JSON | 列出修复类型用于发现类型 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/findings | GET | Query | JSON | 列出态势发现 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/findings/export | POST | JSON | JSON | 创建新发现导出请求 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/findings/ignore | POST | JSON | JSON | 标记发现 as ignored |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/findings/unignore | POST | JSON | JSON | 移除忽略标记来自发现 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/findings/{finding_id} | GET | Query | JSON | 获取态势发现 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/findings/{finding_id}/instances | GET | Query | JSON | 列出实例的发现 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/findings/{finding_id}/instances/archive | POST | JSON | JSON | 归档发现 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/findings/{finding_id}/instances/unarchive | POST | JSON | JSON | 移除归档 marking 来自发现实例 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/findings/{finding_id}/instances/{instance_id} | GET | Query | JSON | 获取发现实例使用实例 ID |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/findings/{finding_id}/reset_finding_severity | POST | JSON | JSON | 重置严重性用于发现 back 给默认 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/findings/{finding_id}/tune_finding_severity | POST | JSON | JSON | 更新严重性用于发现 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/findings/{storage_namespace_id}/instances/export | POST | JSON | JSON | 创建发现实例导出 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/policies | GET | Query | JSON | 列出策略配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/policies | POST | JSON | JSON | 创建新策略配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/policies/{policy_id} | DELETE | Query | JSON | 删除策略配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/policies/{policy_id} | GET | Query | JSON | 获取策略配置按 ID |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/policies/{policy_id} | PUT | JSON | JSON | 更新策略配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/remediations/jobs | GET | Query | JSON | 列出修复任务 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/remediations/jobs | POST | JSON | JSON | 创建修复任务 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/remediations/jobs/export | POST | JSON | JSON | 创建修复任务导出 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/webhooks | GET | Query | JSON | 列出 webhook 配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/webhooks | POST | JSON | JSON | 创建新 webhook 配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/webhooks/evaluate | POST | JSON | JSON | 测试 webhook 配置 before creating it |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/webhooks/jobs | POST | JSON | JSON | 创建 webhook 任务 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/webhooks/{webhook_id} | DELETE | Query | JSON | 删除 webhook 配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/webhooks/{webhook_id} | GET | Query | JSON | 获取 webhook 配置按 ID |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/webhooks/{webhook_id} | PUT | JSON | JSON | 更新现有 webhook 配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/data-security/posture/webhooks/{webhook_id}/evaluate | POST | JSON | JSON | 测试现有 webhook 配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/deployment-groups | GET | Query | JSON | 列出部署组 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/deployment-groups | POST | JSON | JSON | 创建部署组 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/deployment-groups/{group_id} | DELETE | Query | JSON | 删除部署组 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/deployment-groups/{group_id} | GET | Query | JSON | 获取部署组 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/deployment-groups/{group_id} | PATCH | JSON | JSON | 更新部署组 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/ip-profiles | GET | Query | JSON | 列出 IP 配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/ip-profiles | POST | JSON | JSON | 创建 IP 配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/ip-profiles/{profile_id} | DELETE | Query | JSON | 删除 IP 配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/ip-profiles/{profile_id} | GET | Query | JSON | 获取 IP 配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/ip-profiles/{profile_id} | PATCH | JSON | JSON | 更新 IP 配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/networks | GET | Query | JSON | 列出你的设备托管 networks |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/networks | POST | JSON | JSON | 创建设备托管网络 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/networks/{network_id} | DELETE | Query | JSON | 删除设备托管网络 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/networks/{network_id} | GET | Query | JSON | 获取设备托管网络详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/networks/{network_id} | PUT | JSON | JSON | 更新设备托管网络 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/physical-devices | GET | Query | JSON | 列出设备 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/physical-devices/{device_id} | DELETE | Query | JSON | 删除设备 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/physical-devices/{device_id} | GET | Query | JSON | 获取设备 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/policies | GET | Query | JSON | 列出设备设置配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/policy | GET | Query | JSON | 获取默认设备设置配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/policy | PATCH | JSON | JSON | 更新默认设备设置配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/policy | POST | JSON | JSON | 创建设备设置配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/policy/exclude | GET | Query | JSON | 获取拆分隧道排除列出 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/policy/exclude | PUT | JSON | JSON | 设置拆分隧道排除列出 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/policy/fallback_domains | GET | Query | JSON | 获取你的本地域名回退列出 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/policy/fallback_domains | PUT | JSON | JSON | 设置你的本地域名回退列出 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/policy/include | GET | Query | JSON | 获取拆分隧道包含列出 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/policy/include | PUT | JSON | JSON | 设置拆分隧道包含列出 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/policy/{policy_id} | DELETE | Query | JSON | 删除设备设置配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/policy/{policy_id} | GET | Query | JSON | 获取设备设置配置文件按 ID |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/policy/{policy_id} | PATCH | JSON | JSON | 更新设备设置配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/policy/{policy_id}/exclude | GET | Query | JSON | 获取拆分隧道排除列出用于设备设置配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/policy/{policy_id}/exclude | PUT | JSON | JSON | 设置拆分隧道排除列出用于设备设置配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/policy/{policy_id}/fallback_domains | GET | Query | JSON | 获取本地域名回退列出用于设备设置配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/policy/{policy_id}/fallback_domains | PUT | JSON | JSON | 设置本地域名回退列出用于设备设置配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/policy/{policy_id}/include | GET | Query | JSON | 获取拆分隧道包含列出用于设备设置配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/policy/{policy_id}/include | PUT | JSON | JSON | 设置拆分隧道包含列出用于设备设置配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/posture | GET | Query | JSON | 列出态势规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/posture | POST | JSON | JSON | 创建态势规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/posture/integration | GET | Query | JSON | 列出态势集成 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/posture/integration | POST | JSON | JSON | 创建态势集成 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/posture/integration/{integration_id} | DELETE | Query | JSON | 删除态势集成 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/posture/integration/{integration_id} | GET | Query | JSON | 获取态势集成 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/posture/integration/{integration_id} | PATCH | JSON | JSON | 更新态势集成 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/posture/{rule_id} | DELETE | Query | JSON | 删除态势规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/posture/{rule_id} | GET | Query | JSON | 获取态势规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/posture/{rule_id} | PUT | JSON | JSON | 更新态势规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/registrations | DELETE | Query | JSON | 删除注册 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/registrations | GET | Query | JSON | 列出注册 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/registrations/{registration_id} | DELETE | Query | JSON | 删除注册 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/registrations/{registration_id} | GET | Query | JSON | 获取注册 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/registrations/{registration_id}/override_codes | GET | Query | JSON | 获取覆盖代码 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/resilience/disconnect | GET | Query | JSON | 获取全局断开 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/resilience/disconnect | POST | JSON | JSON | 设置全局断开 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/settings | DELETE | Query | JSON | 重置设备设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/settings | GET | Query | JSON | 获取设备设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/devices/settings | PATCH | JSON | JSON | 更新设备设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/colos | GET | Query | JSON | 列出 Cloudflare colos |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/commands | GET | Query | JSON | 列出账户命令 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/commands | POST | JSON | JSON | 创建账户命令 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/commands/devices | GET | Query | JSON | 列出设备 eligible 用于远程捕获 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/commands/quota | GET | Query | JSON | Returns 账户命令用量, 配额, 与重置时间 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/commands/{command_id}/downloads/{filename} | GET | Query | JSON | 下载命令输出文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/devices/dex_tests | GET | Query | JSON | 列出设备 DEX 测试 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/devices/dex_tests | POST | JSON | JSON | 创建设备 DEX 测试 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/devices/dex_tests/{dex_test_id} | DELETE | Query | JSON | 删除设备 DEX 测试 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/devices/dex_tests/{dex_test_id} | GET | Query | JSON | 获取设备 DEX 测试 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/devices/dex_tests/{dex_test_id} | PUT | JSON | JSON | 更新设备 DEX 测试 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/devices/{device_id}/fleet-status/live | GET | Query | JSON | 获取最新状态的设备. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/devices/{device_id}/isps | GET | Query | JSON | 列出设备 ISPs |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/fleet-status/devices | GET | Query | JSON | 列出详情的设备使用 WARP. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/fleet-status/live | GET | Query | JSON | 获取实时聚合设备详情按维度 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/fleet-status/over-time | GET | Query | JSON | 获取 over 时间聚合详情用于设备按维度 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/http-tests/{test_id} | GET | Query | JSON | 获取详情与聚合指标用于 http 测试 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/http-tests/{test_id}/percentiles | GET | Query | JSON | 获取 percentiles 用于 http 测试 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/rules | GET | Query | JSON | 列出 DEX 规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/rules | POST | JSON | JSON | 创建 DEX 规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/rules/{rule_id} | DELETE | Query | JSON | 删除 DEX 规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/rules/{rule_id} | GET | Query | JSON | 获取 DEX 规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/rules/{rule_id} | PATCH | JSON | JSON | 更新 DEX 规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/tests/overview | GET | Query | JSON | 列出 DEX 测试分析 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/tests/unique-devices | GET | Query | JSON | 获取统计的设备 targeted |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/traceroute-test-results/{test_result_id}/network-path | GET | Query | JSON | 获取详情用于特定路由追踪测试运行 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/traceroute-tests/{test_id} | GET | Query | JSON | 获取详情与聚合指标用于路由追踪测试 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/traceroute-tests/{test_id}/network-path | GET | Query | JSON | 获取网络路径 breakdown 用于路由追踪测试 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/traceroute-tests/{test_id}/percentiles | GET | Query | JSON | 获取 percentiles 用于路由追踪测试 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dex/warp-change-events | GET | Query | JSON | 列出 WARP 变更事件. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/custom_prompt_topics | GET | Query | JSON | 列出自定义提示词主题 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/custom_prompt_topics | POST | JSON | JSON | 创建自定义提示词主题 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/custom_prompt_topics/{entry_id} | DELETE | Query | JSON | 删除自定义提示词主题 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/custom_prompt_topics/{entry_id} | GET | Query | JSON | 获取自定义提示词主题 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/custom_prompt_topics/{entry_id} | PUT | JSON | JSON | 更新自定义提示词主题 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/data_classes | GET | Query | JSON | 获取全部数据类别内账户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/data_classes | POST | JSON | JSON | 创建新数据类别 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/data_classes/{data_class_id} | DELETE | Query | JSON | 删除单个数据类别 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/data_classes/{data_class_id} | GET | Query | JSON | 获取特定数据类别 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/data_classes/{data_class_id} | PUT | JSON | JSON | 更新属性的单个数据类别 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/data_tag_categories | GET | Query | JSON | 获取全部数据标签类别内账户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/data_tag_categories | POST | JSON | JSON | 创建新数据标签类别. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/data_tag_categories/{category_id} | DELETE | Query | JSON | 删除单个数据标签类别. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/data_tag_categories/{category_id} | GET | Query | JSON | 获取特定数据标签类别. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/data_tag_categories/{category_id} | PUT | JSON | JSON | 更新属性的单个数据标签类别. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/data_tag_categories/{category_id}/data_tags | GET | Query | JSON | 获取全部数据标签内数据标签类别 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/data_tag_categories/{category_id}/data_tags | POST | JSON | JSON | 创建新数据标签. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/data_tag_categories/{category_id}/data_tags/{tag_id} | DELETE | Query | JSON | 删除单个数据标签. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/data_tag_categories/{category_id}/data_tags/{tag_id} | GET | Query | JSON | 获取特定数据标签. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/data_tag_categories/{category_id}/data_tags/{tag_id} | PUT | JSON | JSON | 更新属性的单个数据标签. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/datasets | GET | Query | JSON | 获取全部数据集 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/datasets | POST | JSON | JSON | 创建新数据集 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/datasets/{dataset_id} | DELETE | Query | JSON | 删除数据集 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/datasets/{dataset_id} | GET | Query | JSON | 获取特定数据集 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/datasets/{dataset_id} | PUT | JSON | JSON | 更新详情关于数据集 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/datasets/{dataset_id}/upload | POST | JSON | JSON | 准备给上传新版本的数据集 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/datasets/{dataset_id}/upload/{version} | POST | JSON | JSON | 上传新版本的数据集 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/datasets/{dataset_id}/versions/{version} | POST | JSON | JSON | 设置列信息用于 multi-column 上传 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/datasets/{dataset_id}/versions/{version}/entries/{entry_id} | POST | JSON | JSON | 上传新版本的 multi-column 数据集 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/email/account_mapping | GET | Query | JSON | 获取映射 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/email/account_mapping | POST | JSON | JSON | 创建映射 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/email/rules | GET | Query | JSON | 列出全部邮箱扫描器规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/email/rules | PATCH | JSON | JSON | 更新邮箱扫描器规则 priorities |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/email/rules | POST | JSON | JSON | 创建邮箱扫描器规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/email/rules/{rule_id} | DELETE | Query | JSON | 删除邮箱扫描器规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/email/rules/{rule_id} | GET | Query | JSON | 获取邮箱扫描器规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/email/rules/{rule_id} | PUT | JSON | JSON | 更新邮箱扫描器规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/entries | GET | Query | JSON | 列出全部条目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/entries | POST | JSON | JSON | 创建自定义条目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/entries/custom/{entry_id} | PUT | JSON | JSON | 更新自定义条目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/entries/integration | POST | JSON | JSON | 创建集成条目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/entries/integration/{entry_id} | DELETE | Query | JSON | 删除集成条目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/entries/integration/{entry_id} | PUT | JSON | JSON | 更新集成条目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/entries/predefined | POST | JSON | JSON | 创建预定义条目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/entries/predefined/{entry_id} | DELETE | Query | JSON | 删除预定义条目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/entries/predefined/{entry_id} | PUT | JSON | JSON | 更新预定义条目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/entries/{entry_id} | DELETE | Query | JSON | 删除自定义条目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/entries/{entry_id} | GET | Query | JSON | 获取 DLP 条目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/entries/{entry_id} | PUT | JSON | JSON | 更新条目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/limits | GET | Query | JSON | 获取限制关联含 DLP 用于账户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/patterns/validate | POST | JSON | JSON | 校验 DLP regex 模式 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/profiles | GET | Query | JSON | 列出全部配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/profiles/custom | POST | JSON | JSON | 创建自定义配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/profiles/custom/{profile_id} | DELETE | Query | JSON | 删除自定义配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/profiles/custom/{profile_id} | GET | Query | JSON | 获取自定义配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/profiles/custom/{profile_id} | PUT | JSON | JSON | 更新自定义配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/profiles/predefined/{profile_id} | DELETE | Query | JSON | 删除预定义配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/profiles/predefined/{profile_id}/config | GET | Query | JSON | 获取预定义配置文件配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/profiles/predefined/{profile_id}/config | PUT | JSON | JSON | 更新预定义配置文件配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/profiles/{profile_id} | GET | Query | JSON | 获取 DLP 配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/sensitivity_groups | GET | Query | JSON | 获取全部敏感度组内账户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/sensitivity_groups | POST | JSON | JSON | 创建新敏感度组. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/sensitivity_groups/{sensitivity_group_id} | DELETE | Query | JSON | 删除单个敏感度组. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/sensitivity_groups/{sensitivity_group_id} | GET | Query | JSON | 获取特定敏感度组. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/sensitivity_groups/{sensitivity_group_id} | PUT | JSON | JSON | 更新属性的单个敏感度组. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/sensitivity_groups/{sensitivity_group_id}/level_order | GET | Query | JSON | 获取 ordered 列出的级别 IDs 用于敏感度组. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/sensitivity_groups/{sensitivity_group_id}/level_order | PUT | JSON | JSON | 设置 ordering 的级别 within 敏感度组. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/sensitivity_groups/{sensitivity_group_id}/levels | GET | Query | JSON | 获取全部敏感度级别内敏感度组 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/sensitivity_groups/{sensitivity_group_id}/levels | POST | JSON | JSON | 创建新敏感度级别. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/sensitivity_groups/{sensitivity_group_id}/levels/{sensitivity_level_id} | DELETE | Query | JSON | 删除单个敏感度级别. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/sensitivity_groups/{sensitivity_group_id}/levels/{sensitivity_level_id} | GET | Query | JSON | 获取特定敏感度级别. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/sensitivity_groups/{sensitivity_group_id}/levels/{sensitivity_level_id} | PUT | JSON | JSON | 更新属性的单个敏感度级别. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/settings | DELETE | Query | JSON | 删除 (重置) DLP 账户级设置给初始值. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/settings | GET | Query | JSON | 获取 DLP 账户级设置. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/settings | PATCH | JSON | JSON | 部分更新 DLP 账户级设置. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dlp/settings | PUT | JSON | JSON | 更新 DLP 账户级设置 (完整替换). |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway | GET | Query | JSON | 获取零信任账户信息 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway | POST | JSON | JSON | 创建零信任账户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/app_types | GET | Query | JSON | 列出应用与应用类型映射 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/audit_ssh_settings | GET | Query | JSON | 获取零信任 SSH 设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/audit_ssh_settings | PUT | JSON | JSON | 更新零信任 SSH 设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/audit_ssh_settings/rotate_seed | POST | JSON | JSON | 轮换零信任 SSH 账户填充 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/categories | GET | Query | JSON | 列出类别 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/certificates | GET | Query | JSON | 列出零信任证书 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/certificates | POST | JSON | JSON | 创建零信任证书 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/certificates/{certificate_id} | DELETE | Query | JSON | 删除零信任证书 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/certificates/{certificate_id} | GET | Query | JSON | 获取零信任证书详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/certificates/{certificate_id}/activate | POST | JSON | JSON | 激活零信任证书 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/certificates/{certificate_id}/deactivate | POST | JSON | JSON | 停用零信任证书 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/configuration | GET | Query | JSON | 获取零信任账户配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/configuration | PATCH | JSON | JSON | 修改零信任账户配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/configuration | PUT | JSON | JSON | 更新零信任账户配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/lists | GET | Query | JSON | 列出零信任列出 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/lists | POST | JSON | JSON | 创建零信任列出 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/lists/{list_id} | DELETE | Query | JSON | 删除零信任列出 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/lists/{list_id} | GET | Query | JSON | 获取零信任列出详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/lists/{list_id} | PATCH | JSON | JSON | 修改零信任列出. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/lists/{list_id} | PUT | JSON | JSON | 更新零信任列出 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/lists/{list_id}/items | GET | Query | JSON | 获取零信任列出条目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/locations | GET | Query | JSON | 列出零信任网关位置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/locations | POST | JSON | JSON | 创建零信任网关位置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/locations/{location_id} | DELETE | Query | JSON | 删除零信任网关位置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/locations/{location_id} | GET | Query | JSON | 获取零信任网关位置详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/locations/{location_id} | PUT | JSON | JSON | 更新零信任网关位置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/logging | GET | Query | JSON | 获取 logging 设置用于零信任账户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/logging | PUT | JSON | JSON | 更新零信任账户 logging 设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/pacfiles | GET | Query | JSON | 列出 PAC 文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/pacfiles | POST | JSON | JSON | 创建 PAC 文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/pacfiles/{pacfile_id} | DELETE | Query | JSON | 删除 PAC 文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/pacfiles/{pacfile_id} | GET | Query | JSON | 获取 PAC 文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/pacfiles/{pacfile_id} | PUT | JSON | JSON | 更新零信任网关 PAC 文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/proxy_endpoints | GET | Query | JSON | 列出代理端点 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/proxy_endpoints | POST | JSON | JSON | 创建代理端点 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/proxy_endpoints/{proxy_endpoint_id} | DELETE | Query | JSON | 删除代理端点 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/proxy_endpoints/{proxy_endpoint_id} | GET | Query | JSON | 获取代理端点 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/proxy_endpoints/{proxy_endpoint_id} | PATCH | JSON | JSON | 更新代理端点 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/rules | GET | Query | JSON | 列出零信任网关规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/rules | POST | JSON | JSON | 创建零信任网关规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/rules/tenant | GET | Query | JSON | 列出零信任网关规则 inherited 来自父账户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/rules/{rule_id} | DELETE | Query | JSON | 删除零信任网关规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/rules/{rule_id} | GET | Query | JSON | 获取零信任网关规则详情. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/rules/{rule_id} | PUT | JSON | JSON | 更新零信任网关规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/gateway/rules/{rule_id}/reset_expiration | POST | JSON | JSON | 重置过期的零信任网关规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/infrastructure/targets | GET | Query | JSON | 列出全部目标 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/infrastructure/targets | POST | JSON | JSON | 创建新目标 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/infrastructure/targets/batch | PUT | JSON | JSON | 创建新目标 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/infrastructure/targets/batch_delete | POST | JSON | JSON | 删除目标 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/infrastructure/targets/{target_id} | DELETE | Query | JSON | 删除目标 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/infrastructure/targets/{target_id} | GET | Query | JSON | 获取目标 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/infrastructure/targets/{target_id} | PUT | JSON | JSON | 更新目标 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/one/applications | GET | Query | JSON | 列出应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/one/applications/{application_id} | GET | Query | JSON | 获取应用详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/one/applications/{application_id}/auth-methods | GET | Query | JSON | 获取 auth 方法 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/one/integrations | GET | Query | JSON | 列出集成 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/one/integrations | POST | JSON | JSON | 创建集成 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/one/integrations/{id} | DELETE | Query | JSON | 删除集成 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/one/integrations/{id} | GET | Query | JSON | 获取集成详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/one/integrations/{id} | PATCH | JSON | JSON | 更新集成 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/one/integrations/{id}/pause | POST | JSON | JSON | 暂停集成 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/one/integrations/{id}/resume | POST | JSON | JSON | 恢复集成 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/resource-library/applications | GET | Query | JSON | 列出应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/resource-library/applications | POST | JSON | JSON | 创建应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/resource-library/applications/{id} | DELETE | Query | JSON | 删除应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/resource-library/applications/{id} | GET | Query | JSON | 获取应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/resource-library/applications/{id} | PATCH | JSON | JSON | 更新应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/resource-library/categories | GET | Query | JSON | 列出应用类别 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/resource-library/categories/{id} | GET | Query | JSON | 获取应用类别 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/teamnet/routes | GET | Query | JSON | 列出隧道路由 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/teamnet/routes | POST | JSON | JSON | 创建隧道路由 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/teamnet/routes/ip/{ip} | GET | Query | JSON | 获取隧道路由按 IP |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/teamnet/routes/{route_id} | DELETE | Query | JSON | 删除隧道路由 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/teamnet/routes/{route_id} | GET | Query | JSON | 获取隧道路由 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/teamnet/routes/{route_id} | PATCH | JSON | JSON | 更新隧道路由 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/teamnet/virtual_networks | GET | Query | JSON | 列出虚拟 networks |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/teamnet/virtual_networks | POST | JSON | JSON | 创建虚拟网络 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/teamnet/virtual_networks/{virtual_network_id} | DELETE | Query | JSON | 删除虚拟网络 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/teamnet/virtual_networks/{virtual_network_id} | GET | Query | JSON | 获取虚拟网络 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/teamnet/virtual_networks/{virtual_network_id} | PATCH | JSON | JSON | 更新虚拟网络 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/tunnels | GET | Query | JSON | 列出全部隧道 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/warp_connector | GET | Query | JSON | 列出网格节点 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/warp_connector | POST | JSON | JSON | 创建网格节点 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/warp_connector/{tunnel_id} | DELETE | Query | JSON | 删除网格节点 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/warp_connector/{tunnel_id} | GET | Query | JSON | 获取网格节点 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/warp_connector/{tunnel_id} | PATCH | JSON | JSON | 更新网格节点 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/warp_connector/{tunnel_id}/configurations | GET | Query | JSON | 获取网格节点 HA 配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/warp_connector/{tunnel_id}/configurations | PUT | JSON | JSON | 更新网格节点 HA 配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/warp_connector/{tunnel_id}/connections | GET | Query | JSON | 列出网格节点连接 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/warp_connector/{tunnel_id}/connectors/{connector_id} | GET | Query | JSON | 获取网格节点连接器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/warp_connector/{tunnel_id}/failover | PUT | JSON | JSON | 触发手册故障转移用于网格节点 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/warp_connector/{tunnel_id}/token | GET | Query | JSON | 获取网格节点令牌 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/zerotrust/connectivity_settings | GET | Query | JSON | 获取零信任连通性设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/zerotrust/connectivity_settings | PATCH | JSON | JSON | 更新零信任连通性设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/zerotrust/routes/hostname | GET | Query | JSON | 列出主机名路由 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/zerotrust/routes/hostname | POST | JSON | JSON | 创建主机名路由 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/zerotrust/routes/hostname/{hostname_route_id} | DELETE | Query | JSON | 删除主机名路由 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/zerotrust/routes/hostname/{hostname_route_id} | GET | Query | JSON | 获取主机名路由 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/zerotrust/routes/hostname/{hostname_route_id} | PATCH | JSON | JSON | 更新主机名路由 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/zerotrust/subnets | GET | Query | JSON | 列出子网 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/zerotrust/subnets/cloudflare_source/{address_family} | PATCH | JSON | JSON | 更新 Cloudflare 来源子网 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/zerotrust/subnets/initial_resolved_ip/{address_family} | GET | Query | JSON | 获取初始 Resolved IP 子网 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/zerotrust/subnets/initial_resolved_ip/{address_family} | PUT | JSON | JSON | 更新初始 Resolved IP 子网 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/zerotrust/subnets/warp | POST | JSON | JSON | 创建 WARP IP 子网 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/zerotrust/subnets/warp/{subnet_id} | DELETE | Query | JSON | 删除 WARP IP 子网 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/zerotrust/subnets/warp/{subnet_id} | GET | Query | JSON | 获取 WARP IP 子网 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/zerotrust/subnets/warp/{subnet_id} | PATCH | JSON | JSON | 更新 WARP IP 子网 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/zt_risk_scoring/behaviors | GET | Query | JSON | 获取全部行为与关联配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/zt_risk_scoring/behaviors | PUT | JSON | JSON | 更新配置用于风险行为 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/zt_risk_scoring/integrations | GET | Query | JSON | 列出全部风险评分集成用于账户. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/zt_risk_scoring/integrations | POST | JSON | JSON | 创建新风险评分集成. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/zt_risk_scoring/integrations/reference_id/{reference_id} | GET | Query | JSON | 获取风险评分集成按引用 id. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/zt_risk_scoring/integrations/{integration_id} | DELETE | Query | JSON | 删除风险评分集成. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/zt_risk_scoring/integrations/{integration_id} | GET | Query | JSON | 获取风险评分集成按 id. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/zt_risk_scoring/integrations/{integration_id} | PUT | JSON | JSON | 更新风险评分集成. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/zt_risk_scoring/summary | GET | Query | JSON | 获取风险评分信息用于全部用户内账户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/zt_risk_scoring/{user_id} | GET | Query | JSON | 获取风险事件/评分信息用于特定用户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/zt_risk_scoring/{user_id}/reset | POST | JSON | JSON | 清除风险评分用于特定用户 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/devices/policy/certificates | GET | Query | JSON | 获取设备证书 provisioning 状态 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/devices/policy/certificates | PATCH | JSON | JSON | 更新设备证书 provisioning 状态 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/apps | GET | Query | JSON | 列出访问应用 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/apps | POST | JSON | JSON | 添加访问应用 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/apps/ca | GET | Query | JSON | 列出短期证书 CAs |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/apps/{app_id} | DELETE | Query | JSON | 删除访问应用 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/apps/{app_id} | GET | Query | JSON | 获取访问应用 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/apps/{app_id} | PUT | JSON | JSON | 更新访问应用 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/apps/{app_id}/ca | DELETE | Query | JSON | 删除短期证书 CA |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/apps/{app_id}/ca | GET | Query | JSON | 获取短期证书 CA |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/apps/{app_id}/ca | POST | JSON | JSON | 创建短期证书 CA |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/apps/{app_id}/policies | GET | Query | JSON | 列出访问应用策略 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/apps/{app_id}/policies | POST | JSON | JSON | 创建访问应用策略 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/apps/{app_id}/policies/{policy_id} | DELETE | Query | JSON | 删除访问应用策略 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/apps/{app_id}/policies/{policy_id} | GET | Query | JSON | 获取访问应用策略 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/apps/{app_id}/policies/{policy_id} | PUT | JSON | JSON | 更新访问应用策略 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/apps/{app_id}/revoke_tokens | POST | JSON | JSON | 撤销应用令牌 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/apps/{app_id}/settings | PATCH | JSON | JSON | 更新访问应用设置 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/apps/{app_id}/settings | PUT | JSON | JSON | 更新访问应用设置 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/apps/{app_id}/user_policy_checks | GET | Query | JSON | 测试访问策略 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/certificates | GET | Query | JSON | 列出 mTLS 证书 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/certificates | POST | JSON | JSON | 添加 mTLS 证书 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/certificates/settings | GET | Query | JSON | 列出全部 mTLS 主机名设置 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/certificates/settings | PUT | JSON | JSON | 更新 mTLS certificate's 主机名设置 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/certificates/{certificate_id} | DELETE | Query | JSON | 删除 mTLS 证书 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/certificates/{certificate_id} | GET | Query | JSON | 获取 mTLS 证书 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/certificates/{certificate_id} | PUT | JSON | JSON | 更新 mTLS 证书 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/groups | GET | Query | JSON | 列出访问组 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/groups | POST | JSON | JSON | 创建访问分组 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/groups/{group_id} | DELETE | Query | JSON | 删除访问分组 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/groups/{group_id} | GET | Query | JSON | 获取访问分组 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/groups/{group_id} | PUT | JSON | JSON | 更新访问分组 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/identity_providers | GET | Query | JSON | 列出访问身份提供方 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/identity_providers | POST | JSON | JSON | 添加访问身份提供方 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/identity_providers/{identity_provider_id} | DELETE | Query | JSON | 删除访问身份提供方 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/identity_providers/{identity_provider_id} | GET | Query | JSON | 获取访问身份提供方 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/identity_providers/{identity_provider_id} | PUT | JSON | JSON | 更新访问身份提供方 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/organizations | GET | Query | JSON | 获取你的零信任组织 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/organizations | POST | JSON | JSON | 创建你的零信任组织 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/organizations | PUT | JSON | JSON | 更新你的零信任组织 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/organizations/revoke_user | POST | JSON | JSON | 撤销全部访问令牌用于用户 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/service_tokens | GET | Query | JSON | 列出服务令牌 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/service_tokens | POST | JSON | JSON | 创建服务令牌 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/service_tokens/{service_token_id} | DELETE | Query | JSON | 删除服务令牌 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/service_tokens/{service_token_id} | GET | Query | JSON | 获取服务令牌 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/access/service_tokens/{service_token_id} | PUT | JSON | JSON | 更新服务令牌 |

## Workers 与 Serverless 计算（256 条）

> Cloudflare Workers 运行时、脚本与版本、构建集成、平台 Workers、容器、工作流、队列、Pages 部署、Zaraz、API 网关、代码片段与流水线。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/account/limits | GET | Query | JSON | 获取 build-minute 可用性 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/builds | GET | Query | JSON | 获取构建按 Worker 版本 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/builds/latest | GET | Query | JSON | 获取最新构建按脚本 IDs |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/builds/{build_uuid} | GET | Query | JSON | 获取 Workers 构建 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/builds/{build_uuid}/cancel | PUT | JSON | JSON | 取消 Workers 构建 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/builds/{build_uuid}/logs | GET | Query | JSON | 获取 Workers 构建日志 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/repos/connections | PUT | JSON | JSON | 创建或更新仓库连接 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/repos/connections/{repo_connection_uuid} | DELETE | Query | JSON | 删除仓库连接 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/repos/{provider_type}/{provider_account_id}/{repo_id}/config_autofill | GET | Query | JSON | 获取仓库配置 autofill |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/tokens | GET | Query | JSON | 列出构建令牌 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/tokens | POST | JSON | JSON | 创建构建令牌 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/tokens/{build_token_uuid} | DELETE | Query | JSON | 删除构建令牌 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/triggers | POST | JSON | JSON | 创建构建触发 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/triggers/{trigger_uuid} | DELETE | Query | JSON | 删除构建触发 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/triggers/{trigger_uuid} | PATCH | JSON | JSON | 更新构建触发 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/triggers/{trigger_uuid}/builds | POST | JSON | JSON | 启动 Workers 构建 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/triggers/{trigger_uuid}/environment_variables | GET | Query | JSON | 列出构建变量 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/triggers/{trigger_uuid}/environment_variables | PATCH | JSON | JSON | 设置构建变量 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/triggers/{trigger_uuid}/environment_variables/{environment_variable_key} | DELETE | Query | JSON | 删除构建变量 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/triggers/{trigger_uuid}/purge_build_cache | POST | JSON | JSON | 清除 trigger's 构建缓存 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/workers/{external_script_id}/builds | GET | Query | JSON | 列出构建用于 Worker |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/workers/{external_script_id}/triggers | GET | Query | JSON | 列出触发用于 Worker |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/workers/{script_name}/deploy_hooks | GET | Query | JSON | 列出部署钩子 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/workers/{script_name}/deploy_hooks | POST | JSON | JSON | 创建部署钩子 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/workers/{script_name}/deploy_hooks/{deploy_hook_uuid} | DELETE | Query | JSON | 删除部署钩子 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/workers/{script_name}/deploy_hooks/{deploy_hook_uuid} | GET | Query | JSON | 获取部署钩子 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/builds/workers/{script_name}/deploy_hooks/{deploy_hook_uuid} | PUT | JSON | JSON | 更新部署钩子 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/containers/applications | GET | Query | JSON | 列出应用关联含你的账户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/containers/applications | POST | JSON | JSON | 创建新应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/containers/applications/{application_id} | DELETE | Query | JSON | 删除单个应用按 id |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/containers/applications/{application_id} | GET | Query | JSON | 获取单个应用按 id |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/containers/applications/{application_id} | PATCH | JSON | JSON | 修改应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/containers/applications/{application_id}/instances-v2 | GET | Query | JSON | 列出容器实例 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/containers/applications/{application_id}/instances/{instance_id} | GET | Query | JSON | 获取容器实例 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/containers/applications/{application_id}/rollouts | POST | JSON | JSON | 创建新推出用于应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/containers/applications/{application_id}/versions | GET | Query | JSON | 列出全部应用版本 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/containers/image-preparations | POST | JSON | JSON | 准备容器镜像 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/containers/registries | GET | Query | JSON | 获取列出的 configured 注册表内账户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/containers/registries | POST | JSON | JSON | 配置私有外部镜像注册表 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/containers/registries/{domain} | DELETE | Query | JSON | 删除注册表来自账户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/containers/registries/{domain}/credentials | POST | JSON | JSON | 生成 JWT 给 interact 含指定镜像注册表. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/event_subscriptions/subscriptions | GET | Query | JSON | 列出事件订阅 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/event_subscriptions/subscriptions | POST | JSON | JSON | 创建事件订阅 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/event_subscriptions/subscriptions/{subscription_id} | DELETE | Query | JSON | 删除事件订阅 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/event_subscriptions/subscriptions/{subscription_id} | GET | Query | JSON | 获取事件订阅 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/event_subscriptions/subscriptions/{subscription_id} | PATCH | JSON | JSON | 更新事件订阅 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pages/projects | GET | Query | JSON | 列出 Cloudflare Pages 项目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pages/projects | POST | JSON | JSON | 创建 Cloudflare Pages 项目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pages/projects/{project_name} | DELETE | Query | JSON | 删除 Cloudflare Pages 项目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pages/projects/{project_name} | GET | Query | JSON | 获取 Cloudflare Pages 项目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pages/projects/{project_name} | PATCH | JSON | JSON | 更新 Cloudflare Pages 项目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pages/projects/{project_name}/deployments | GET | Query | JSON | 列出 Pages 部署 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pages/projects/{project_name}/deployments | POST | JSON | JSON | 创建 Pages 部署 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pages/projects/{project_name}/deployments/{deployment_id} | DELETE | Query | JSON | 删除 Pages 部署 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pages/projects/{project_name}/deployments/{deployment_id} | GET | Query | JSON | 获取 Pages 部署 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pages/projects/{project_name}/deployments/{deployment_id}/history/logs | GET | Query | JSON | 获取 Pages 部署日志 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pages/projects/{project_name}/deployments/{deployment_id}/retry | POST | JSON | JSON | 重试 Pages 部署 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pages/projects/{project_name}/deployments/{deployment_id}/rollback | POST | JSON | JSON | 回滚 Pages 部署 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pages/projects/{project_name}/deployments/{deployment_id}/tails | POST | JSON | JSON | 创建部署尾部 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pages/projects/{project_name}/deployments/{deployment_id}/tails/{tail_id} | DELETE | Query | JSON | 删除部署尾部 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pages/projects/{project_name}/domains | GET | Query | JSON | 列出 Pages 自定义域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pages/projects/{project_name}/domains | POST | JSON | JSON | 添加 Pages 自定义域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pages/projects/{project_name}/domains/{domain_name} | DELETE | Query | JSON | 删除 Pages 自定义域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pages/projects/{project_name}/domains/{domain_name} | GET | Query | JSON | 获取 Pages 自定义域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pages/projects/{project_name}/domains/{domain_name} | PATCH | JSON | JSON | 重试自定义域名校验 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pages/projects/{project_name}/purge_build_cache | POST | JSON | JSON | 清除 Pages 构建缓存 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pages/projects/{project_name}/upload-token | GET | Query | JSON | 获取上传令牌 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pipelines/v1/pipelines | GET | Query | JSON | 列出流水线 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pipelines/v1/pipelines | POST | JSON | JSON | 创建流水线 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pipelines/v1/pipelines/{pipeline_id} | DELETE | Query | JSON | 删除流水线 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pipelines/v1/pipelines/{pipeline_id} | GET | Query | JSON | 获取流水线详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pipelines/v1/sinks | GET | Query | JSON | 列出 Sinks |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pipelines/v1/sinks | POST | JSON | JSON | 创建下沉 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pipelines/v1/sinks/{sink_id} | DELETE | Query | JSON | 删除下沉 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pipelines/v1/sinks/{sink_id} | GET | Query | JSON | 获取下沉详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pipelines/v1/streams | GET | Query | JSON | 列出 Streams |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pipelines/v1/streams | POST | JSON | JSON | 创建 Stream |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pipelines/v1/streams/{stream_id} | DELETE | Query | JSON | 删除 Stream |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pipelines/v1/streams/{stream_id} | GET | Query | JSON | 获取 Stream 详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pipelines/v1/streams/{stream_id} | PATCH | JSON | JSON | 更新 Stream |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pipelines/v1/validate_sql | POST | JSON | JSON | 校验 SQL |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/queues | GET | Query | JSON | 列出队列 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/queues | POST | JSON | JSON | 创建队列 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/queues/{queue_id} | DELETE | Query | JSON | 删除队列 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/queues/{queue_id} | GET | Query | JSON | 获取队列 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/queues/{queue_id} | PATCH | JSON | JSON | 更新队列 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/queues/{queue_id} | PUT | JSON | JSON | 更新队列 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/queues/{queue_id}/consumers | GET | Query | JSON | 列出队列消费者 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/queues/{queue_id}/consumers | POST | JSON | JSON | 创建队列消费者 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/queues/{queue_id}/consumers/{consumer_id} | DELETE | Query | JSON | 删除队列消费者 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/queues/{queue_id}/consumers/{consumer_id} | GET | Query | JSON | 获取队列消费者 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/queues/{queue_id}/consumers/{consumer_id} | PUT | JSON | JSON | 更新队列消费者 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/queues/{queue_id}/messages | POST | JSON | JSON | 推送消息 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/queues/{queue_id}/messages/ack | POST | JSON | JSON | 确认 + 重试队列消息 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/queues/{queue_id}/messages/batch | POST | JSON | JSON | 推送消息批次 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/queues/{queue_id}/messages/peek | POST | JSON | JSON | Peek 队列消息 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/queues/{queue_id}/messages/pull | POST | JSON | JSON | 拉取队列消息 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/queues/{queue_id}/messages/purge | POST | JSON | JSON | 清除 Peeked 队列消息 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/queues/{queue_id}/metrics | GET | Query | JSON | 获取队列指标 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/queues/{queue_id}/purge | GET | Query | JSON | 获取队列清除状态 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/queues/{queue_id}/purge | POST | JSON | JSON | 清除队列 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/account-settings | GET | Query | JSON | 获取 Workers 账户设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/account-settings | PUT | JSON | JSON | 配置 Workers 账户设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/assets/upload | POST | JSON | JSON | 上传 Worker 资产 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/dispatch/namespaces | GET | Query | JSON | 列出 Workers 平台调度命名空间 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/dispatch/namespaces | POST | JSON | JSON | 创建 Workers 平台调度命名空间 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/dispatch/namespaces/{dispatch_namespace} | DELETE | Query | JSON | 删除 Workers 平台调度命名空间 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/dispatch/namespaces/{dispatch_namespace} | GET | Query | JSON | 获取 Workers 平台调度命名空间 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/dispatch/namespaces/{dispatch_namespace}/scripts/{script_name} | DELETE | Query | JSON | 删除 Workers 平台脚本 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/dispatch/namespaces/{dispatch_namespace}/scripts/{script_name} | GET | Query | JSON | 获取 Workers 平台脚本详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/dispatch/namespaces/{dispatch_namespace}/scripts/{script_name} | PUT | JSON | JSON | 上传 Workers 平台脚本模块 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/dispatch/namespaces/{dispatch_namespace}/scripts/{script_name}/assets-upload-session | POST | JSON | JSON | 创建 Workers 平台资产上传会话 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/dispatch/namespaces/{dispatch_namespace}/scripts/{script_name}/bindings | GET | Query | JSON | 获取 Workers 平台脚本绑定 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/dispatch/namespaces/{dispatch_namespace}/scripts/{script_name}/content | GET | Query | JSON | 获取 Workers 平台脚本内容 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/dispatch/namespaces/{dispatch_namespace}/scripts/{script_name}/content | PUT | JSON | JSON | 替换 Workers 平台脚本内容 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/dispatch/namespaces/{dispatch_namespace}/scripts/{script_name}/secrets | GET | Query | JSON | 列出 Workers 平台脚本密钥 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/dispatch/namespaces/{dispatch_namespace}/scripts/{script_name}/secrets | PUT | JSON | JSON | 添加密钥给 Workers 平台脚本 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/dispatch/namespaces/{dispatch_namespace}/scripts/{script_name}/secrets-bulk | PATCH | JSON | JSON | 修改多个 Workers 平台脚本密钥 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/dispatch/namespaces/{dispatch_namespace}/scripts/{script_name}/secrets/{secret_name} | DELETE | Query | JSON | 删除 Workers 平台脚本密钥 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/dispatch/namespaces/{dispatch_namespace}/scripts/{script_name}/secrets/{secret_name} | GET | Query | JSON | 获取 Workers 平台脚本密钥 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/dispatch/namespaces/{dispatch_namespace}/scripts/{script_name}/settings | GET | Query | JSON | 获取 Workers 平台脚本设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/dispatch/namespaces/{dispatch_namespace}/scripts/{script_name}/settings | PATCH | JSON | JSON | 修改 Workers 平台脚本设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/dispatch/namespaces/{dispatch_namespace}/scripts/{script_name}/tags | GET | Query | JSON | 列出 Workers 平台脚本标签 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/dispatch/namespaces/{dispatch_namespace}/scripts/{script_name}/tags | PUT | JSON | JSON | 替换 Workers 平台脚本标签 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/dispatch/namespaces/{dispatch_namespace}/scripts/{script_name}/tags/{tag} | DELETE | Query | JSON | 删除 Workers 平台脚本标签 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/domains | GET | Query | JSON | 列出 Worker 域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/domains | PUT | JSON | JSON | 附加 Worker 域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/domains/{domain_id} | DELETE | Query | JSON | 分离 Worker 域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/domains/{domain_id} | GET | Query | JSON | 获取 Worker 域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/observability/destinations | GET | Query | JSON | 获取目的地 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/observability/destinations | POST | JSON | JSON | 创建目的地 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/observability/destinations/{slug} | DELETE | Query | JSON | 删除目的地 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/observability/destinations/{slug} | PATCH | JSON | JSON | 更新目的地 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/observability/queries | GET | Query | JSON | 列出查询 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/observability/queries | POST | JSON | JSON | Save 查询 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/observability/shared/query | POST | JSON | JSON | 创建 sharable 关联给查询结果 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/observability/shared/query/{id} | GET | Query | JSON | 查看查询已共享 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/observability/telemetry/keys | POST | JSON | JSON | 列出密钥 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/observability/telemetry/live-tail | POST | JSON | JSON | 准备实时尾部 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/observability/telemetry/live-tail/heartbeat | POST | JSON | JSON | 实时尾部 heartbeat |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/observability/telemetry/query | POST | JSON | JSON | 运行查询 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/observability/telemetry/values | POST | JSON | JSON | 列出值 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts | GET | Query | JSON | 列出 Worker 脚本 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts-search | GET | Query | JSON | 搜索 Worker 脚本 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name} | DELETE | Query | JSON | 删除 Worker |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name} | GET | Query | JSON | 下载 Worker 脚本 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name} | PUT | JSON | JSON | 上传 Worker 模块 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/assets-upload-session | POST | JSON | JSON | 创建 Worker 资产上传会话 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/content | PUT | JSON | JSON | 替换 Worker 脚本内容 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/content/v2 | GET | Query | JSON | 获取 Worker 脚本内容 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/deployments | GET | Query | JSON | 列出 Worker 部署 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/deployments | POST | JSON | JSON | 创建 Worker 部署 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/deployments/{deployment_id} | DELETE | Query | JSON | 删除 Worker 部署 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/deployments/{deployment_id} | GET | Query | JSON | 获取 Worker 部署 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/schedules | GET | Query | JSON | 获取 Worker 脚本计划 (Cron 触发) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/schedules | PUT | JSON | JSON | 更新 Worker 脚本计划 (Cron 触发) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/script-settings | GET | Query | JSON | 获取 Worker 脚本设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/script-settings | PATCH | JSON | JSON | 修改 Worker 脚本设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/secrets | GET | Query | JSON | 列出密钥 bound 给 Worker 脚本 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/secrets | PUT | JSON | JSON | 添加密钥给 Worker 脚本 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/secrets-bulk | PATCH | JSON | JSON | 修改多个 Worker 脚本密钥 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/secrets/{secret_name} | DELETE | Query | JSON | 删除 Worker 脚本密钥 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/secrets/{secret_name} | GET | Query | JSON | 获取密钥绑定 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/settings | GET | Query | JSON | 获取 Worker 脚本与版本设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/settings | PATCH | JSON | JSON | 修改 Worker 脚本与版本设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/subdomain | DELETE | Query | JSON | 删除 Worker 脚本子域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/subdomain | GET | Query | JSON | 获取 Worker 脚本子域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/subdomain | POST | JSON | JSON | 更新 Worker 脚本子域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/tails | GET | Query | JSON | 列出 Worker Tails |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/tails | POST | JSON | JSON | 启动 Worker 尾部 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/tails/{id} | DELETE | Query | JSON | 删除 Worker 尾部 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/versions | GET | Query | JSON | 列出版本 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/versions | POST | JSON | JSON | 上传版本 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/scripts/{script_name}/versions/{version_id} | GET | Query | JSON | 获取 Worker 脚本版本 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/subdomain | DELETE | Query | JSON | 删除 Workers 子域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/subdomain | GET | Query | JSON | 获取 Workers 子域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/subdomain | PUT | JSON | JSON | 创建 Workers 子域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/workers | GET | Query | JSON | 列出 Workers |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/workers | POST | JSON | JSON | 创建 Worker |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/workers/{worker_id} | DELETE | Query | JSON | 删除 Worker |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/workers/{worker_id} | GET | Query | JSON | 获取 Worker |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/workers/{worker_id} | PATCH | JSON | JSON | 编辑 Worker |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/workers/{worker_id} | PUT | JSON | JSON | 更新 Worker |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/workers/{worker_id}/versions | GET | Query | JSON | 列出 Worker 版本 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/workers/{worker_id}/versions | POST | JSON | JSON | 创建 Worker 版本 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/workers/{worker_id}/versions/{version_id} | DELETE | Query | JSON | 删除 Worker 版本 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/workers/{worker_id}/versions/{version_id} | GET | Query | JSON | 获取 Worker 版本 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workflows | GET | Query | JSON | 列出全部工作流 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workflows/{workflow_name} | DELETE | Query | JSON | 删除工作流 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workflows/{workflow_name} | GET | Query | JSON | 获取工作流详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workflows/{workflow_name} | PUT | JSON | JSON | 创建/修改工作流 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workflows/{workflow_name}/instances | GET | Query | JSON | 列出的工作流实例 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workflows/{workflow_name}/instances | POST | JSON | JSON | 创建新工作流实例 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workflows/{workflow_name}/instances/batch | POST | JSON | JSON | 批次创建新工作流实例 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workflows/{workflow_name}/instances/{instance_id} | GET | Query | JSON | 获取日志与状态来自实例 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workflows/{workflow_name}/instances/{instance_id}/events/{event_type} | POST | JSON | JSON | 发送事件给实例 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workflows/{workflow_name}/instances/{instance_id}/status | PATCH | JSON | JSON | 变更状态的实例 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workflows/{workflow_name}/instances/{instance_id}/step | GET | Query | JSON | 获取完整步骤输出来自实例 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workflows/{workflow_name}/versions | GET | Query | JSON | 列出 deployed 工作流版本 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workflows/{workflow_name}/versions/{version_id} | GET | Query | JSON | 获取工作流版本详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workflows/{workflow_name}/versions/{version_id}/graph | GET | Query | JSON | 获取工作流版本 graph |
| https://api.cloudflare.com/client/v4/pages/assets/check-missing | POST | JSON | JSON | 检查 missing 资产 |
| https://api.cloudflare.com/client/v4/pages/assets/upload | POST | JSON | JSON | 上传资产 |
| https://api.cloudflare.com/client/v4/pages/assets/upsert-hashes | POST | JSON | JSON | 插入或更新资产 hashes |
| https://api.cloudflare.com/client/v4/workers/builds/deploy_hooks/{deploy_hook_uuid} | POST | JSON | JSON | 触发部署钩子 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/configuration | GET | Query | JSON | 获取会话标识符设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/configuration | PUT | JSON | JSON | 更新会话标识符设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/discovery | GET | Query | JSON | 导出已发现 API 操作 as OpenAPI 模式 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/discovery/operations | GET | Query | JSON | 列出已发现 web 与 API 操作 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/discovery/operations | PATCH | JSON | JSON | 编辑已发现 web 与 API 操作 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/labels | GET | Query | JSON | 列出操作标签 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/labels/managed/{name} | GET | Query | JSON | 获取托管操作标签 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/labels/managed/{name}/resources/operation | PUT | JSON | JSON | 替换操作 attached 给托管标签 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/labels/user | DELETE | Query | JSON | 删除用户自定义操作标签 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/labels/user | POST | JSON | JSON | 创建用户自定义操作标签 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/labels/user/{name} | DELETE | Query | JSON | 删除用户自定义操作标签 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/labels/user/{name} | GET | Query | JSON | 获取用户自定义操作标签 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/labels/user/{name} | PATCH | JSON | JSON | 编辑用户自定义操作标签 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/labels/user/{name} | PUT | JSON | JSON | 更新用户自定义操作标签 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/labels/user/{name}/resources/operation | PUT | JSON | JSON | 替换操作 attached 给用户自定义标签 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/operations | DELETE | Query | JSON | 删除 web 或 API 操作 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/operations | GET | Query | JSON | 列出 web 与 API 操作 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/operations | POST | JSON | JSON | 创建 web 或 API 操作 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/operations/item | POST | JSON | JSON | 创建 web 或 API 操作 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/operations/labels | DELETE | Query | JSON | 移除标签来自 web 或 API 操作 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/operations/labels | POST | JSON | JSON | 附加标签给 web 或 API 操作 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/operations/labels | PUT | JSON | JSON | 替换标签上 web 或 API 操作 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/operations/{operation_id} | DELETE | Query | JSON | 删除 web 或 API 操作 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/operations/{operation_id} | GET | Query | JSON | 获取 web 或 API 操作 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/operations/{operation_id}/labels | DELETE | Query | JSON | 移除标签来自 web 或 API 操作 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/operations/{operation_id}/labels | POST | JSON | JSON | 附加标签给 web 或 API 操作 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/operations/{operation_id}/labels | PUT | JSON | JSON | 替换标签上 web 或 API 操作 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/api_gateway/schemas | GET | Query | JSON | 导出 web 与 API 操作 as OpenAPI 模式 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/zaraz/config | GET | Query | JSON | 获取 Zaraz 配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/zaraz/config | PUT | JSON | JSON | 更新 Zaraz 配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/zaraz/default | GET | Query | JSON | 获取默认 Zaraz 配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/zaraz/export | GET | Query | JSON | 导出 Zaraz 配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/zaraz/history | GET | Query | JSON | 列出 Zaraz 历史配置记录 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/zaraz/history | PUT | JSON | JSON | 还原 Zaraz 历史配置按 ID |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/zaraz/history/configs | GET | Query | JSON | 获取 Zaraz 历史配置按 ID(s) |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/zaraz/publish | POST | JSON | JSON | 发布 Zaraz 预览配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/zaraz/workflow | GET | Query | JSON | 获取 Zaraz 工作流 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/zaraz/workflow | PUT | JSON | JSON | 更新 Zaraz 工作流 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/snippets | GET | Query | JSON | 列出区域代码片段 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/snippets/snippet_rules | DELETE | Query | JSON | 删除区域代码片段规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/snippets/snippet_rules | GET | Query | JSON | 列出区域代码片段规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/snippets/snippet_rules | PUT | JSON | JSON | 更新区域代码片段规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/snippets/{snippet_name} | DELETE | Query | JSON | 删除区域代码片段 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/snippets/{snippet_name} | GET | Query | JSON | 获取区域代码片段 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/snippets/{snippet_name} | PUT | JSON | JSON | 更新区域代码片段 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/snippets/{snippet_name}/content | GET | Query | JSON | 获取区域代码片段内容 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/workers/routes | GET | Query | JSON | 列出 Worker 路由 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/workers/routes | POST | JSON | JSON | 创建 Worker 路由 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/workers/routes/{route_id} | DELETE | Query | JSON | 删除 Worker 路由 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/workers/routes/{route_id} | GET | Query | JSON | 获取 Worker 路由 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/workers/routes/{route_id} | PUT | JSON | JSON | 替换 Worker 路由 |

## Magic 网络与互联（234 条）

> Magic Transit 云网络防护、Magic Firewall、Magic 网络监控、网络互联（Magic Network/Magic WAN）、地址与 BGP、GRE/IPsec 隧道、Spectrum 四层代理、Argo 智能路由。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/address_maps | GET | Query | JSON | 列出地址 Maps |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/address_maps | POST | JSON | JSON | 创建地址映射 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/address_maps/{address_map_id} | DELETE | Query | JSON | 删除地址映射 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/address_maps/{address_map_id} | GET | Query | JSON | 地址映射详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/address_maps/{address_map_id} | PATCH | JSON | JSON | 更新地址映射 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/address_maps/{address_map_id}/accounts/{member_account_id} | DELETE | Query | JSON | 移除账户成员资格来自地址映射 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/address_maps/{address_map_id}/accounts/{member_account_id} | PUT | JSON | JSON | 添加账户成员资格给地址映射 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/address_maps/{address_map_id}/ips/{ip_address} | DELETE | Query | JSON | 移除 IP 来自地址映射 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/address_maps/{address_map_id}/ips/{ip_address} | PUT | JSON | JSON | 添加 IP 给地址映射 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/loa_documents | POST | JSON | JSON | 上传 LOA 文档 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/loa_documents/{loa_document_id}/download | GET | Query | JSON | 下载 LOA 文档 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/prefixes | GET | Query | JSON | 列出前缀 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/prefixes | POST | JSON | JSON | 添加前缀 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/prefixes/{prefix_id} | DELETE | Query | JSON | 删除前缀 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/prefixes/{prefix_id} | GET | Query | JSON | 前缀详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/prefixes/{prefix_id} | PATCH | JSON | JSON | 更新前缀描述 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/prefixes/{prefix_id}/bgp/prefixes | GET | Query | JSON | 列出 BGP 前缀 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/prefixes/{prefix_id}/bgp/prefixes | POST | JSON | JSON | 创建 BGP 前缀 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/prefixes/{prefix_id}/bgp/prefixes/{bgp_prefix_id} | DELETE | Query | JSON | 删除 BGP 前缀 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/prefixes/{prefix_id}/bgp/prefixes/{bgp_prefix_id} | GET | Query | JSON | 获取 BGP 前缀 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/prefixes/{prefix_id}/bgp/prefixes/{bgp_prefix_id} | PATCH | JSON | JSON | 更新 BGP 前缀 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/prefixes/{prefix_id}/bindings | GET | Query | JSON | 列出服务绑定 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/prefixes/{prefix_id}/bindings | POST | JSON | JSON | 创建服务绑定 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/prefixes/{prefix_id}/bindings/{binding_id} | DELETE | Query | JSON | 删除服务绑定 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/prefixes/{prefix_id}/bindings/{binding_id} | GET | Query | JSON | 获取服务绑定 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/prefixes/{prefix_id}/delegations | GET | Query | JSON | 列出前缀 Delegations |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/prefixes/{prefix_id}/delegations | POST | JSON | JSON | 创建前缀委托 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/prefixes/{prefix_id}/delegations/{delegation_id} | DELETE | Query | JSON | 删除前缀委托 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/prefixes/{prefix_id}/validate | POST | JSON | JSON | 校验前缀 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/regional_hostnames/regions | GET | Query | JSON | 列出地区 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/addressing/services | GET | Query | JSON | 列出服务 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/basin-catalog | GET | Query | JSON | 列出 Basin 目录 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/basin-catalog/{bucket_name} | GET | Query | JSON | 获取 Basin 目录详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/basin-catalog/{bucket_name}/credential | POST | JSON | JSON | 存储目录凭证 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/basin-catalog/{bucket_name}/delete | POST | JSON | JSON | 删除 Basin 目录元数据 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/basin-catalog/{bucket_name}/disable | POST | JSON | JSON | 禁用 Basin 目录 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/basin-catalog/{bucket_name}/enable | POST | JSON | JSON | 启用 R2 存储桶 as 目录 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/basin-catalog/{bucket_name}/maintenance-configs | GET | Query | JSON | 获取目录维护配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/basin-catalog/{bucket_name}/maintenance-configs | POST | JSON | JSON | 更新目录维护配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/basin-catalog/{bucket_name}/namespaces | GET | Query | JSON | 列出命名空间内目录 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/basin-catalog/{bucket_name}/namespaces/{namespace}/tables | GET | Query | JSON | 列出表内命名空间 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/basin-catalog/{bucket_name}/namespaces/{namespace}/tables/{table_name}/maintenance-configs | GET | Query | JSON | 获取表维护配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/basin-catalog/{bucket_name}/namespaces/{namespace}/tables/{table_name}/maintenance-configs | POST | JSON | JSON | 更新表维护配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cni/cnis | GET | Query | JSON | 列出现有 CNI 对象 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cni/cnis | POST | JSON | JSON | 创建新 CNI 对象 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cni/cnis/{cni} | DELETE | Query | JSON | 删除指定 CNI 对象 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cni/cnis/{cni} | GET | Query | JSON | 获取信息关于 CNI 对象 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cni/cnis/{cni} | PUT | JSON | JSON | 修改 stored 信息关于 CNI 对象 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cni/interconnects | GET | Query | JSON | 列出现有互联 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cni/interconnects | POST | JSON | JSON | 创建新互联 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cni/interconnects/{icon} | DELETE | Query | JSON | 删除互联对象 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cni/interconnects/{icon} | GET | Query | JSON | 获取信息关于互联对象 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cni/interconnects/{icon}/loa | GET | Query | JSON | 生成 Letter 的授权 (LOA) 用于指定互联 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cni/interconnects/{icon}/status | GET | Query | JSON | 获取当前状态的互联对象 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cni/settings | GET | Query | JSON | 获取当前设置用于活跃账户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cni/settings | PUT | JSON | JSON | 更新当前设置用于活跃账户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cni/slots | GET | Query | JSON | 获取列出的全部 slots 匹配指定参数 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cni/slots/{slot} | GET | Query | JSON | 获取信息关于指定 slot |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/connectivity/directory/services | GET | Query | JSON | 列出 Workers VPC 连通性服务 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/connectivity/directory/services | POST | JSON | JSON | 创建 Workers VPC 连通性服务 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/connectivity/directory/services/{service_id} | DELETE | Query | JSON | 删除 Workers VPC 连通性服务 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/connectivity/directory/services/{service_id} | GET | Query | JSON | 获取 Workers VPC 连通性服务 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/connectivity/directory/services/{service_id} | PUT | JSON | JSON | 更新 Workers VPC 连通性服务 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dls/regional_services/prefix_bindings | GET | Query | JSON | 列出 DLS 前缀绑定用于账户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dls/regional_services/prefix_bindings | POST | JSON | JSON | 创建 DLS 前缀绑定 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dls/regional_services/prefix_bindings/{binding_id} | DELETE | Query | JSON | 删除 DLS 前缀绑定 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dls/regional_services/prefix_bindings/{binding_id} | GET | Query | JSON | 获取 DLS 前缀绑定 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dls/regional_services/prefix_bindings/{binding_id} | PATCH | JSON | JSON | 更新 DLS 前缀绑定 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dls/regions | GET | Query | JSON | 列出 DLS 地区用于账户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dls/regions/{region_id} | GET | Query | JSON | 获取 DLS 区域 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/apps | GET | Query | JSON | 列出应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/apps | POST | JSON | JSON | 创建新应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/apps/{account_app_id} | DELETE | Query | JSON | 删除账户应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/apps/{account_app_id} | PATCH | JSON | JSON | 更新应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/apps/{account_app_id} | PUT | JSON | JSON | 更新应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/bgp/filter_profiles | GET | Query | JSON | 列出 BGP 过滤配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/bgp/filter_profiles | POST | JSON | JSON | 创建 BGP 过滤配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/bgp/filter_profiles/{profile_id} | DELETE | Query | JSON | 删除 BGP 过滤配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/bgp/filter_profiles/{profile_id} | GET | Query | JSON | 获取 BGP 过滤配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/bgp/filter_profiles/{profile_id} | PUT | JSON | JSON | 更新 BGP 过滤配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cf1_sites | GET | Query | JSON | 列出 CF1 站点 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cf1_sites | POST | JSON | JSON | 创建 CF1 站点 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cf1_sites/{cf1_site_id} | DELETE | Query | JSON | 删除 CF1 站点 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cf1_sites/{cf1_site_id} | GET | Query | JSON | 获取 CF1 站点 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cf1_sites/{cf1_site_id} | PATCH | JSON | JSON | 更新 CF1 站点 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cf1_sites/{cf1_site_id}/ramps | GET | Query | JSON | 列出 CF1 站点 Ramps |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cf1_sites/{cf1_site_id}/ramps | POST | JSON | JSON | 创建 CF1 站点 Ramps |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cf1_sites/{cf1_site_id}/ramps/{ramp_id} | DELETE | Query | JSON | 删除 CF1 站点 Ramp |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cf1_sites/{cf1_site_id}/ramps/{ramp_id} | GET | Query | JSON | 获取 CF1 站点 Ramp |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cf_interconnects | GET | Query | JSON | 列出互联 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cf_interconnects | PUT | JSON | JSON | 更新多个互联 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cf_interconnects/{cf_interconnect_id} | GET | Query | JSON | 列出互联详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cf_interconnects/{cf_interconnect_id} | PUT | JSON | JSON | 更新互联 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/catalog-syncs | GET | Query | JSON | 列出目录 Syncs |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/catalog-syncs | POST | JSON | JSON | 创建目录同步 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/catalog-syncs/prebuilt-policies | GET | Query | JSON | 列出 Prebuilt 策略 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/catalog-syncs/{sync_id} | DELETE | Query | JSON | 删除目录同步 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/catalog-syncs/{sync_id} | GET | Query | JSON | 读取目录同步 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/catalog-syncs/{sync_id} | PATCH | JSON | JSON | 修改目录同步 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/catalog-syncs/{sync_id} | PUT | JSON | JSON | 更新目录同步 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/catalog-syncs/{sync_id}/refresh | POST | JSON | JSON | 运行目录同步 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/onramps | GET | Query | JSON | 列出 On-ramps |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/onramps | POST | JSON | JSON | 创建接入 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/onramps/magic_wan_address_space | GET | Query | JSON | 读取 Magic WAN 地址空间 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/onramps/magic_wan_address_space | PATCH | JSON | JSON | 修改 Magic WAN 地址空间 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/onramps/magic_wan_address_space | PUT | JSON | JSON | 更新 Magic WAN 地址空间 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/onramps/{onramp_id} | DELETE | Query | JSON | 删除接入 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/onramps/{onramp_id} | GET | Query | JSON | 读取接入 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/onramps/{onramp_id} | PATCH | JSON | JSON | 修改接入 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/onramps/{onramp_id} | PUT | JSON | JSON | 更新接入 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/onramps/{onramp_id}/apply | POST | JSON | JSON | 应用接入 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/onramps/{onramp_id}/export | POST | JSON | JSON | 导出 as Terraform |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/onramps/{onramp_id}/plan | POST | JSON | JSON | 套餐接入 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/providers | GET | Query | JSON | 列出云集成 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/providers | POST | JSON | JSON | 创建云集成 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/providers/discover | POST | JSON | JSON | 运行 Discovery 用于全部集成 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/providers/{provider_id} | DELETE | Query | JSON | 删除云集成 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/providers/{provider_id} | GET | Query | JSON | 读取云集成 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/providers/{provider_id} | PATCH | JSON | JSON | 修改云集成 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/providers/{provider_id} | PUT | JSON | JSON | 更新云集成 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/providers/{provider_id}/discover | POST | JSON | JSON | 运行 Discovery |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/providers/{provider_id}/initial_setup | GET | Query | JSON | 获取云集成设置配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/resources | GET | Query | JSON | 列出资源 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/resources/export | GET | Query | JSON | 导出资源 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/resources/policy-preview | POST | JSON | JSON | 预览 Rego 查询 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/cloud/resources/{resource_id} | GET | Query | JSON | 读取资源 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/connectors | GET | Query | JSON | 列出连接器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/connectors | POST | JSON | JSON | 创建连接器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/connectors/{connector_id} | DELETE | Query | JSON | 删除连接器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/connectors/{connector_id} | GET | Query | JSON | 获取连接器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/connectors/{connector_id} | PATCH | JSON | JSON | 编辑连接器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/connectors/{connector_id} | PUT | JSON | JSON | 更新连接器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/connectors/{connector_id}/interrupts | GET | Query | JSON | 列出 Interrupts |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/connectors/{connector_id}/interrupts | POST | JSON | JSON | 创建 Interrupt |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/connectors/{connector_id}/telemetry/events | GET | Query | JSON | 列出事件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/connectors/{connector_id}/telemetry/events/latest | GET | Query | JSON | 获取最新事件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/connectors/{connector_id}/telemetry/events/{event_t}.{event_n} | GET | Query | JSON | 获取事件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/connectors/{connector_id}/telemetry/snapshots | GET | Query | JSON | 列出快照 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/connectors/{connector_id}/telemetry/snapshots/latest | GET | Query | JSON | 获取最新快照 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/connectors/{connector_id}/telemetry/snapshots/{snapshot_t} | GET | Query | JSON | 获取快照 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/gre_tunnels | GET | Query | JSON | 列出 GRE 隧道 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/gre_tunnels | POST | JSON | JSON | 创建 GRE 隧道 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/gre_tunnels | PUT | JSON | JSON | 更新多个 GRE 隧道 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/gre_tunnels/{gre_tunnel_id} | DELETE | Query | JSON | 删除 GRE 隧道 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/gre_tunnels/{gre_tunnel_id} | GET | Query | JSON | 列出 GRE 隧道详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/gre_tunnels/{gre_tunnel_id} | PUT | JSON | JSON | 更新 GRE 隧道 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/ipsec_tunnels | GET | Query | JSON | 列出 IPsec 隧道 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/ipsec_tunnels | POST | JSON | JSON | 创建 IPsec 隧道 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/ipsec_tunnels | PUT | JSON | JSON | 更新多个 IPsec 隧道 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/ipsec_tunnels/psk | POST | JSON | JSON | 设置 Pre-Shared 密钥 (PSK) 用于 IPsec 隧道 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/ipsec_tunnels/{ipsec_tunnel_id} | DELETE | Query | JSON | 删除 IPsec 隧道 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/ipsec_tunnels/{ipsec_tunnel_id} | GET | Query | JSON | 列出 IPsec 隧道详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/ipsec_tunnels/{ipsec_tunnel_id} | PUT | JSON | JSON | 更新 IPsec 隧道 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/ipsec_tunnels/{ipsec_tunnel_id}/psk_generate | POST | JSON | JSON | 生成 Pre-Shared 键 (PSK) 用于 IPsec 隧道 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/routes | DELETE | Query | JSON | 删除 Many 路由 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/routes | GET | Query | JSON | 列出路由 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/routes | POST | JSON | JSON | 创建路由 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/routes | PUT | JSON | JSON | 更新 Many 路由 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/routes/{route_id} | DELETE | Query | JSON | 删除路由 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/routes/{route_id} | GET | Query | JSON | 路由详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/routes/{route_id} | PUT | JSON | JSON | 更新路由 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites | GET | Query | JSON | 列出站点 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites | POST | JSON | JSON | 创建新站点 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id} | DELETE | Query | JSON | 删除站点 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id} | GET | Query | JSON | 站点详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id} | PATCH | JSON | JSON | 修改站点 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id} | PUT | JSON | JSON | 更新站点 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id}/acls | GET | Query | JSON | 列出站点 ACLs |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id}/acls | POST | JSON | JSON | 创建新站点 ACL |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id}/acls/{acl_id} | DELETE | Query | JSON | 删除站点 ACL |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id}/acls/{acl_id} | GET | Query | JSON | 站点 ACL 详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id}/acls/{acl_id} | PATCH | JSON | JSON | 修改站点 ACL |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id}/acls/{acl_id} | PUT | JSON | JSON | 更新站点 ACL |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id}/app_configs | GET | Query | JSON | 列出应用配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id}/app_configs | POST | JSON | JSON | 创建新应用配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id}/app_configs/{app_config_id} | DELETE | Query | JSON | 删除应用配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id}/app_configs/{app_config_id} | PATCH | JSON | JSON | 更新应用配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id}/app_configs/{app_config_id} | PUT | JSON | JSON | 更新应用配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id}/lans | GET | Query | JSON | 列出站点 LANs |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id}/lans | POST | JSON | JSON | 创建新站点 LAN |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id}/lans/{lan_id} | DELETE | Query | JSON | 删除站点 LAN |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id}/lans/{lan_id} | GET | Query | JSON | 站点 LAN 详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id}/lans/{lan_id} | PATCH | JSON | JSON | 修改站点 LAN |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id}/lans/{lan_id} | PUT | JSON | JSON | 更新站点 LAN |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id}/wans | GET | Query | JSON | 列出站点 WANs |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id}/wans | POST | JSON | JSON | 创建新站点 WAN |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id}/wans/{wan_id} | DELETE | Query | JSON | 删除站点 WAN |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id}/wans/{wan_id} | GET | Query | JSON | 站点 WAN 详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id}/wans/{wan_id} | PATCH | JSON | JSON | 修改站点 WAN |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/sites/{site_id}/wans/{wan_id} | PUT | JSON | JSON | 更新站点 WAN |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/mnm/config | DELETE | Query | JSON | 删除账户配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/mnm/config | GET | Query | JSON | 列出账户配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/mnm/config | PATCH | JSON | JSON | 更新账户配置字段 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/mnm/config | POST | JSON | JSON | 创建账户配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/mnm/config | PUT | JSON | JSON | 更新 entire 账户配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/mnm/config/full | GET | Query | JSON | 列出规则与账户配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/mnm/rules | GET | Query | JSON | 列出规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/mnm/rules | POST | JSON | JSON | 创建规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/mnm/rules | PUT | JSON | JSON | 更新规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/mnm/rules/{rule_id} | DELETE | Query | JSON | 删除规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/mnm/rules/{rule_id} | GET | Query | JSON | 获取规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/mnm/rules/{rule_id} | PATCH | JSON | JSON | 更新规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/mnm/rules/{rule_id}/advertisement | PATCH | JSON | JSON | 更新 advertisement 用于规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/mnm/vpc-flows/token | POST | JSON | JSON | 生成认证令牌用于 VPC 流日志导出. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pcaps | GET | Query | JSON | 列出数据包捕获请求 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pcaps | POST | JSON | JSON | 创建 PCAP 请求 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pcaps/ownership | GET | Query | JSON | 列出 PCAPs 存储桶所有权 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pcaps/ownership | POST | JSON | JSON | 添加存储桶用于完整数据包捕获 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pcaps/ownership/validate | POST | JSON | JSON | 校验存储桶用于完整数据包捕获 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pcaps/ownership/{ownership_id} | DELETE | Query | JSON | 删除存储桶用于完整数据包捕获 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pcaps/{pcap_id} | GET | Query | JSON | 获取 PCAP 请求 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pcaps/{pcap_id}/download | GET | Query | JSON | 下载 Simple PCAP |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pcaps/{pcap_id}/stop | PUT | JSON | JSON | 停止完整 PCAP |
| https://api.cloudflare.com/client/v4/ips | GET | Query | JSON | Cloudflare/JD 云 IP 详情 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/addressing/regional_hostnames | GET | Query | JSON | 列出区域化主机名 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/addressing/regional_hostnames | POST | JSON | JSON | 创建区域化主机名 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/addressing/regional_hostnames/{hostname} | DELETE | Query | JSON | 删除区域化主机名 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/addressing/regional_hostnames/{hostname} | GET | Query | JSON | 获取区域化主机名 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/addressing/regional_hostnames/{hostname} | PATCH | JSON | JSON | 更新区域化主机名 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/argo/smart_routing | GET | Query | JSON | 获取 Argo 智能路由设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/argo/smart_routing | PATCH | JSON | JSON | 修改 Argo 智能路由设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/argo/tiered_caching | GET | Query | JSON | 获取分层缓存设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/argo/tiered_caching | PATCH | JSON | JSON | 修改分层缓存设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/cloud_connector/rules | GET | Query | JSON | 规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/cloud_connector/rules | PUT | JSON | JSON | Put 规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/spectrum/analytics/aggregate/current | GET | Query | JSON | 获取当前 aggregated 分析 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/spectrum/analytics/events/bytime | GET | Query | JSON | 获取分析按时间 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/spectrum/analytics/events/summary | GET | Query | JSON | 获取分析摘要 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/spectrum/apps | GET | Query | JSON | 列出 Spectrum 应用 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/spectrum/apps | POST | JSON | JSON | 创建 Spectrum 应用使用名称用于源站 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/spectrum/apps/{app_id} | DELETE | Query | JSON | 删除 Spectrum 应用 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/spectrum/apps/{app_id} | GET | Query | JSON | 获取 Spectrum 应用配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/spectrum/apps/{app_id} | PUT | JSON | JSON | 更新 Spectrum 应用配置使用名称用于源站 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/spectrum/protocols | GET | Query | JSON | 列出 Spectrum 应用协议 |

## 安全防护（WAF/DDoS/机器人）（234 条）

> DDoS 防护、防火墙与规则集、WAF 托管转换、安全中心、页面防护 Page Shield、Bot 管理、僵尸网络信息流、内容扫描、泄露凭证检测、URL 扫描、CSAM 扫描、欺诈检测、品牌保护、漏洞扫描、模式校验、滥用举报、托管防御、Security TXT、智能盾、URL 归一化、字段提取器、Turnstile 人机验证、Google 标签网关。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/abuse-reports | GET | Query | JSON | 列出滥用报告 against 账户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/abuse-reports/submitted | GET | Query | JSON | 列出已提交滥用报告 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/abuse-reports/submitted/{report_id} | GET | Query | JSON | 获取已提交滥用报告 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/abuse-reports/submitted/{report_id}/emails | GET | Query | JSON | 列出 emails sent 给滥用报告 submitter |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/abuse-reports/{report_id}/mitigations | GET | Query | JSON | 列出滥用报告 mitigations |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/abuse-reports/{report_id}/mitigations/appeal | POST | JSON | JSON | 请求审核上 mitigations |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/abuse-reports/{report_param} | GET | Query | JSON | 获取滥用报告 against 账户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/abuse-reports/{report_param} | POST | JSON | JSON | 提交滥用报告 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/botnet_feed/asn/{asn_id}/day_report | GET | Query | JSON | 获取每日报告 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/botnet_feed/asn/{asn_id}/full_report | GET | Query | JSON | 获取完整报告 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/botnet_feed/configs/asn | GET | Query | JSON | 获取列出的 ASNs |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/botnet_feed/configs/asn/{asn_id} | DELETE | Query | JSON | 删除 ASN |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/brand-protection/logo-matches | GET | Query | JSON | 读取匹配用于 logo 查询按 ID |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/brand-protection/logo-matches/download | GET | Query | JSON | 下载匹配用于 logo 查询按 ID |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/brand-protection/logos | POST | JSON | JSON | 创建新已保存 logo 查询来自镜像文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/brand-protection/logos/{logo_id} | DELETE | Query | JSON | 删除已保存 logo 查询按 ID |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/brand-protection/matches | GET | Query | JSON | 读取匹配用于字符串查询按 ID |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/brand-protection/matches/download | GET | Query | JSON | 下载匹配用于字符串查询按 ID |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/brand-protection/queries | DELETE | Query | JSON | 删除已保存字符串查询按 ID |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/brand-protection/queries | POST | JSON | JSON | 创建新已保存字符串查询 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/brand-protection/queries/bulk | POST | JSON | JSON | 创建新已保存字符串查询内批量 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/brand-protection/submit | POST | JSON | JSON | 创建新 URL 提交 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/brand-protection/url-info | GET | Query | JSON | 读取已提交 URL 按 ID |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/challenges/widgets | GET | Query | JSON | 列出 Turnstile Widgets |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/challenges/widgets | POST | JSON | JSON | 创建 Turnstile 组件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/challenges/widgets/{sitekey} | DELETE | Query | JSON | 删除 Turnstile 组件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/challenges/widgets/{sitekey} | GET | Query | JSON | Turnstile 组件详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/challenges/widgets/{sitekey} | PUT | JSON | JSON | 更新 Turnstile 组件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/challenges/widgets/{sitekey}/rotate_secret | POST | JSON | JSON | 轮换密钥用于 Turnstile 组件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/brand-protection/domain/matches | GET | Query | JSON | 列出已保存查询匹配 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/brand-protection/domain/queries | GET | Query | JSON | 获取查询 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/brand-protection/logo/matches | GET | Query | JSON | 列出 logo 匹配 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/brand-protection/logo/queries | GET | Query | JSON | 获取 logo 查询 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/brand-protection/logo/queries | POST | JSON | JSON | Insert logo 查询 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/brand-protection/logo/queries/{query_id} | DELETE | Query | JSON | 删除 logo 查询 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/field_extractors/{extractor} | DELETE | Query | JSON | 删除字段提取器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/field_extractors/{extractor} | GET | Query | JSON | 获取字段提取器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/field_extractors/{extractor} | PUT | JSON | JSON | 更新字段提取器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/allowlist | DELETE | Query | JSON | 删除全部白名单前缀. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/allowlist | GET | Query | JSON | 列出全部白名单前缀. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/allowlist | POST | JSON | JSON | 创建白名单前缀. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/allowlist/{prefix_id} | DELETE | Query | JSON | 删除白名单前缀. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/allowlist/{prefix_id} | GET | Query | JSON | 获取白名单前缀. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/allowlist/{prefix_id} | PATCH | JSON | JSON | 更新白名单前缀. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/prefixes | DELETE | Query | JSON | 删除全部前缀. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/prefixes | GET | Query | JSON | 列出全部前缀. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/prefixes | POST | JSON | JSON | 创建前缀. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/prefixes/bulk | POST | JSON | JSON | 创建多个前缀. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/prefixes/{prefix_id} | DELETE | Query | JSON | 删除前缀. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/prefixes/{prefix_id} | GET | Query | JSON | 获取前缀. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/prefixes/{prefix_id} | PATCH | JSON | JSON | 更新前缀. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/syn_protection/filters | DELETE | Query | JSON | 删除全部 SYN 防护过滤. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/syn_protection/filters | GET | Query | JSON | 列出全部 SYN 防护过滤. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/syn_protection/filters | POST | JSON | JSON | 创建 SYN 防护过滤. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/syn_protection/filters/{filter_id} | DELETE | Query | JSON | 删除 SYN 防护过滤. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/syn_protection/filters/{filter_id} | GET | Query | JSON | 获取 SYN 防护过滤. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/syn_protection/filters/{filter_id} | PATCH | JSON | JSON | 更新 SYN 防护过滤. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/syn_protection/rules | DELETE | Query | JSON | 删除全部 SYN 防护规则. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/syn_protection/rules | GET | Query | JSON | 列出全部 SYN 防护规则. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/syn_protection/rules | POST | JSON | JSON | 创建 SYN 防护规则. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/syn_protection/rules/{rule_id} | DELETE | Query | JSON | 删除 SYN 防护规则. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/syn_protection/rules/{rule_id} | GET | Query | JSON | 获取 SYN 防护规则. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/syn_protection/rules/{rule_id} | PATCH | JSON | JSON | 更新 SYN 防护规则. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/tcp_flow_protection/filters | DELETE | Query | JSON | 删除全部 TCP 流保护过滤. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/tcp_flow_protection/filters | GET | Query | JSON | 列出全部 TCP 流保护过滤. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/tcp_flow_protection/filters | POST | JSON | JSON | 创建 TCP 流保护过滤. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/tcp_flow_protection/filters/{filter_id} | DELETE | Query | JSON | 删除 TCP 流保护过滤. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/tcp_flow_protection/filters/{filter_id} | GET | Query | JSON | 获取 TCP 流保护过滤. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/tcp_flow_protection/filters/{filter_id} | PATCH | JSON | JSON | 更新 TCP 流保护过滤. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/tcp_flow_protection/rules | DELETE | Query | JSON | 删除全部 TCP 流保护规则. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/tcp_flow_protection/rules | GET | Query | JSON | 列出全部 TCP 流保护规则. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/tcp_flow_protection/rules | POST | JSON | JSON | 创建 TCP 流保护规则. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/tcp_flow_protection/rules/{rule_id} | DELETE | Query | JSON | 删除 TCP 流保护规则. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/tcp_flow_protection/rules/{rule_id} | GET | Query | JSON | 获取 TCP 流保护规则. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/tcp_flow_protection/rules/{rule_id} | PATCH | JSON | JSON | 更新 TCP 流保护规则. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/tcp_protection_status | GET | Query | JSON | 获取保护状态. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/magic/advanced_tcp_protection/configs/tcp_protection_status | PATCH | JSON | JSON | 更新保护状态. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/managed-defense/vulnerability-discovery/repos | GET | Query | JSON | 列出仓库 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/managed-defense/vulnerability-discovery/repos | POST | JSON | JSON | 创建仓库 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/managed-defense/vulnerability-discovery/repos/{repo_id} | GET | Query | JSON | 获取仓库 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/managed-defense/vulnerability-discovery/repos/{repo_id}/scans/{scan_id}/report | GET | Query | JSON | 获取 reviewed 漏洞报告 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/managed-defense/vulnerability-discovery/scans | GET | Query | JSON | 列出扫描 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/managed-defense/vulnerability-discovery/scans | POST | JSON | JSON | 创建扫描 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/managed-defense/vulnerability-discovery/scans/{scan_id} | GET | Query | JSON | 获取扫描 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/managed-defense/vulnerability-discovery/scans/{scan_id}/report | GET | Query | JSON | 获取 reviewed 漏洞报告 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/rules/lists | GET | Query | JSON | 获取列出 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/rules/lists | POST | JSON | JSON | 创建列出 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/rules/lists/bulk_operations/{operation_id} | GET | Query | JSON | 获取批量操作状态 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/rules/lists/{list_id} | DELETE | Query | JSON | 删除列出 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/rules/lists/{list_id} | GET | Query | JSON | 获取列出 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/rules/lists/{list_id} | PUT | JSON | JSON | 更新列出 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/rules/lists/{list_id}/items | DELETE | Query | JSON | 删除列出条目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/rules/lists/{list_id}/items | GET | Query | JSON | 获取列出条目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/rules/lists/{list_id}/items | POST | JSON | JSON | 创建列出条目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/rules/lists/{list_id}/items | PUT | JSON | JSON | 更新全部列出条目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/rules/lists/{list_id}/items/{item_id} | GET | Query | JSON | 获取列出条目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/security-center/insights/{issue_id}/context | GET | Query | JSON | 获取安全中心洞察上下文 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/urlscanner/v2/bulk | POST | JSON | JSON | 批量创建 URL 扫描 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/urlscanner/v2/dom/{scan_id} | GET | Query | JSON | 获取 URL scan's DOM |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/urlscanner/v2/har/{scan_id} | GET | Query | JSON | 获取 URL scan's HAR |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/urlscanner/v2/responses/{response_id} | GET | Query | JSON | 获取原始响应 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/urlscanner/v2/result/{scan_id} | GET | Query | JSON | 获取 URL 扫描 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/urlscanner/v2/scan | POST | JSON | JSON | 创建 URL 扫描 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/urlscanner/v2/screenshots/{scan_id}.png | GET | Query | JSON | 获取截图 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/urlscanner/v2/search | GET | Query | JSON | 搜索 URL 扫描 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vuln_scanner/credential_sets | GET | Query | JSON | 列出凭证设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vuln_scanner/credential_sets | POST | JSON | JSON | 创建凭证设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vuln_scanner/credential_sets/{credential_set_id} | DELETE | Query | JSON | 删除凭证设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vuln_scanner/credential_sets/{credential_set_id} | GET | Query | JSON | 获取凭证设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vuln_scanner/credential_sets/{credential_set_id} | PATCH | JSON | JSON | 编辑凭证设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vuln_scanner/credential_sets/{credential_set_id} | PUT | JSON | JSON | 更新凭证设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vuln_scanner/credential_sets/{credential_set_id}/credentials | GET | Query | JSON | 列出凭证 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vuln_scanner/credential_sets/{credential_set_id}/credentials | POST | JSON | JSON | 创建凭证 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vuln_scanner/credential_sets/{credential_set_id}/credentials/{credential_id} | DELETE | Query | JSON | 删除凭证 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vuln_scanner/credential_sets/{credential_set_id}/credentials/{credential_id} | GET | Query | JSON | 获取凭证 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vuln_scanner/credential_sets/{credential_set_id}/credentials/{credential_id} | PATCH | JSON | JSON | 编辑凭证 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vuln_scanner/credential_sets/{credential_set_id}/credentials/{credential_id} | PUT | JSON | JSON | 更新凭证 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vuln_scanner/scans | GET | Query | JSON | 列出扫描 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vuln_scanner/scans | POST | JSON | JSON | 创建扫描 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vuln_scanner/scans/{scan_id} | GET | Query | JSON | 获取扫描 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vuln_scanner/target_environments | GET | Query | JSON | 列出目标环境 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vuln_scanner/target_environments | POST | JSON | JSON | 创建目标环境 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vuln_scanner/target_environments/{target_environment_id} | DELETE | Query | JSON | 删除目标环境 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vuln_scanner/target_environments/{target_environment_id} | GET | Query | JSON | 获取目标环境 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vuln_scanner/target_environments/{target_environment_id} | PATCH | JSON | JSON | 编辑目标环境 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vuln_scanner/target_environments/{target_environment_id} | PUT | JSON | JSON | 更新目标环境 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/bot_management | GET | Query | JSON | 获取区域机器人管理配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/bot_management | PUT | JSON | JSON | 更新区域机器人管理配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/bot_management/feedback | GET | Query | JSON | 列出区域 feedback 报告 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/bot_management/feedback | POST | JSON | JSON | 提交 feedback 报告 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/content-upload-scan/disable | POST | JSON | JSON | 禁用内容扫描用于区域. |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/content-upload-scan/enable | POST | JSON | JSON | 启用内容扫描用于区域. |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/content-upload-scan/payloads | GET | Query | JSON | 列出内容扫描自定义表达式的区域. |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/content-upload-scan/payloads | POST | JSON | JSON | 创建内容扫描自定义表达式用于区域. |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/content-upload-scan/payloads/{expression_id} | DELETE | Query | JSON | 删除内容扫描自定义表达式来自区域. |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/content-upload-scan/payloads/{expression_id} | PATCH | JSON | JSON | 更新内容扫描自定义表达式用于区域. |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/content-upload-scan/settings | GET | Query | JSON | 获取内容扫描状态用于区域. |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/content-upload-scan/settings | PUT | JSON | JSON | 更新内容扫描状态用于区域. |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/firewall/lockdowns | GET | Query | JSON | 列出区域锁定规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/firewall/lockdowns | POST | JSON | JSON | 创建区域锁定规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/firewall/lockdowns/{lock_downs_id} | DELETE | Query | JSON | 删除区域锁定规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/firewall/lockdowns/{lock_downs_id} | GET | Query | JSON | 获取区域锁定规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/firewall/lockdowns/{lock_downs_id} | PUT | JSON | JSON | 更新区域锁定规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/firewall/ua_rules | GET | Query | JSON | 列出用户智能体屏蔽规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/firewall/ua_rules | POST | JSON | JSON | 创建用户智能体屏蔽规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/firewall/ua_rules/{ua_rule_id} | DELETE | Query | JSON | 删除用户智能体屏蔽规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/firewall/ua_rules/{ua_rule_id} | GET | Query | JSON | 获取用户智能体屏蔽规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/firewall/ua_rules/{ua_rule_id} | PUT | JSON | JSON | 更新用户智能体屏蔽规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/fraud_detection/settings | GET | Query | JSON | 获取欺诈检测设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/fraud_detection/settings | PUT | JSON | JSON | 更新欺诈检测设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/leaked-credential-checks | GET | Query | JSON | 获取泄露凭证检测状态用于区域. |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/leaked-credential-checks | POST | JSON | JSON | 更新泄露凭证检测状态用于区域. |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/leaked-credential-checks/detections | GET | Query | JSON | 列出自定义检测位置的区域. |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/leaked-credential-checks/detections | POST | JSON | JSON | 创建自定义检测位置用于区域. |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/leaked-credential-checks/detections/{detection_id} | DELETE | Query | JSON | 删除自定义检测位置来自区域. |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/leaked-credential-checks/detections/{detection_id} | GET | Query | JSON | 获取自定义检测位置的区域. |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/leaked-credential-checks/detections/{detection_id} | PUT | JSON | JSON | 更新自定义检测位置的区域. |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/managed_headers | DELETE | Query | JSON | 删除托管转换 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/managed_headers | GET | Query | JSON | 列出托管转换 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/managed_headers | PATCH | JSON | JSON | 更新托管转换 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/page_shield | GET | Query | JSON | 获取 client-side 安全设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/page_shield | PUT | JSON | JSON | 更新 client-side 安全设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/page_shield/connections | GET | Query | JSON | 列出已检测连接 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/page_shield/connections/{connection_id} | GET | Query | JSON | 获取已检测连接 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/page_shield/cookies | GET | Query | JSON | 列出已检测 cookies |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/page_shield/cookies/{cookie_id} | GET | Query | JSON | 获取已检测 cookie |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/page_shield/policies | GET | Query | JSON | 列出内容安全规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/page_shield/policies | POST | JSON | JSON | 创建内容安全规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/page_shield/policies/{policy_id} | DELETE | Query | JSON | 删除内容安全规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/page_shield/policies/{policy_id} | GET | Query | JSON | 获取内容安全规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/page_shield/policies/{policy_id} | PUT | JSON | JSON | 更新内容安全规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/page_shield/scripts | GET | Query | JSON | 列出已检测脚本 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/page_shield/scripts/{script_id} | GET | Query | JSON | 获取已检测脚本 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/schema_validation/schemas | GET | Query | JSON | 列出全部 uploaded 模式 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/schema_validation/schemas | POST | JSON | JSON | 上传模式 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/schema_validation/schemas/{schema_id} | DELETE | Query | JSON | 删除模式 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/schema_validation/schemas/{schema_id} | GET | Query | JSON | 获取详情的模式 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/schema_validation/schemas/{schema_id} | PATCH | JSON | JSON | 设置模式校验状态 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/schema_validation/settings | GET | Query | JSON | 获取全局模式校验设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/schema_validation/settings | PATCH | JSON | JSON | 编辑全局模式校验设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/schema_validation/settings | PUT | JSON | JSON | 更新全局模式校验设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/schema_validation/settings/operations | GET | Query | JSON | 列出按操作模式校验设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/schema_validation/settings/operations | PATCH | JSON | JSON | 批量编辑按操作模式校验设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/schema_validation/settings/operations/{operation_id} | DELETE | Query | JSON | 删除按操作模式校验设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/schema_validation/settings/operations/{operation_id} | GET | Query | JSON | 获取按操作模式校验设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/schema_validation/settings/operations/{operation_id} | PUT | JSON | JSON | 更新按操作模式校验设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/security-center/securitytxt | DELETE | Query | JSON | 删除安全.txt |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/security-center/securitytxt | GET | Query | JSON | 获取安全.txt |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/security-center/securitytxt | PUT | JSON | JSON | 更新安全.txt |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/csam_scanner_third_party | GET | Query | JSON | 获取 CSAM 扫描器设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/csam_scanner_third_party | PATCH | JSON | JSON | 更新 CSAM 扫描器设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/google-tag-gateway/config | GET | Query | JSON | 获取 Google 标签网关配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/google-tag-gateway/config | PUT | JSON | JSON | 更新 Google 标签网关配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/smart_shield | GET | Query | JSON | 获取智能 Shield 设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/smart_shield | PATCH | JSON | JSON | 修改智能 Shield 设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/smart_shield/cache_reserve_clear | GET | Query | JSON | 获取缓存预留清除 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/smart_shield/cache_reserve_clear | POST | JSON | JSON | 启动缓存预留清除 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/smart_shield/healthchecks | GET | Query | JSON | 列出健康检查 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/smart_shield/healthchecks | POST | JSON | JSON | 创建健康检查 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/smart_shield/healthchecks/{healthcheck_id} | DELETE | Query | JSON | 删除健康检查 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/smart_shield/healthchecks/{healthcheck_id} | GET | Query | JSON | 健康检查详情 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/smart_shield/healthchecks/{healthcheck_id} | PATCH | JSON | JSON | 修改健康检查 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/smart_shield/healthchecks/{healthcheck_id} | PUT | JSON | JSON | 更新健康检查 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/url_normalization | DELETE | Query | JSON | 删除 URL 标准化设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/url_normalization | GET | Query | JSON | 获取 URL 标准化设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/url_normalization | PUT | JSON | JSON | 更新 URL 标准化设置 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/firewall/access_rules/rules | GET | Query | JSON | 列出 IP 访问规则 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/firewall/access_rules/rules | POST | JSON | JSON | 创建 IP 访问规则 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/firewall/access_rules/rules/{rule_id} | DELETE | Query | JSON | 删除 IP 访问规则 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/firewall/access_rules/rules/{rule_id} | GET | Query | JSON | 获取 IP 访问规则 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/firewall/access_rules/rules/{rule_id} | PATCH | JSON | JSON | 更新 IP 访问规则 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/rulesets | GET | Query | JSON | 列出账户或区域规则集 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/rulesets | POST | JSON | JSON | 创建账户或区域规则集 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/rulesets/phases/{ruleset_phase}/entrypoint | GET | Query | JSON | 获取账户或区域条目点规则集 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/rulesets/phases/{ruleset_phase}/entrypoint | PUT | JSON | JSON | 更新账户或区域条目点规则集 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/rulesets/phases/{ruleset_phase}/entrypoint/versions | GET | Query | JSON | 列出账户或区域条目点 ruleset's 版本 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/rulesets/phases/{ruleset_phase}/entrypoint/versions/{ruleset_version} | GET | Query | JSON | 获取账户或区域条目点规则集版本 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/rulesets/{ruleset_id} | DELETE | Query | JSON | 删除账户或区域规则集 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/rulesets/{ruleset_id} | GET | Query | JSON | 获取账户或区域规则集 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/rulesets/{ruleset_id} | PUT | JSON | JSON | 更新账户或区域规则集 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/rulesets/{ruleset_id}/rules | POST | JSON | JSON | 创建账户或区域规则集规则 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/rulesets/{ruleset_id}/rules/{rule_id} | DELETE | Query | JSON | 删除账户或区域规则集规则 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/rulesets/{ruleset_id}/rules/{rule_id} | PATCH | JSON | JSON | 更新账户或区域规则集规则 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/rulesets/{ruleset_id}/versions | GET | Query | JSON | 列出账户或区域 ruleset's 版本 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/rulesets/{ruleset_id}/versions/{ruleset_version} | DELETE | Query | JSON | 删除账户或区域规则集版本 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/rulesets/{ruleset_id}/versions/{ruleset_version} | GET | Query | JSON | 获取账户或区域规则集版本 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/security-center/insights | GET | Query | JSON | 获取安全中心洞察 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/security-center/insights/audit-log | GET | Query | JSON | 获取账户或区域审计日志 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/security-center/insights/class | GET | Query | JSON | 获取安全中心洞察数量按类别 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/security-center/insights/severity | GET | Query | JSON | 获取安全中心洞察数量按严重性 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/security-center/insights/type | GET | Query | JSON | 获取安全中心洞察数量按类型 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/security-center/insights/{issue_id}/audit-log | GET | Query | JSON | 获取议题审计日志 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/security-center/insights/{issue_id}/classification | PATCH | JSON | JSON | 更新安全中心洞察分类 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/security-center/insights/{issue_id}/dismiss | PUT | JSON | JSON | 归档安全中心洞察 |

## 网络流量分析（Radar）（150 条）

> Cloudflare Radar 公开的互联网流量统计接口：域名/ASN/国家/协议/攻击趋势等聚合数据。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.cloudflare.com/client/v4/radar/agent_readiness/summary/{dimension} | GET | Query | JSON | 获取智能体 readiness 摘要 |
| https://api.cloudflare.com/client/v4/radar/ai/bots/summary/{dimension} | GET | Query | JSON | 获取 AI 机器人 HTTP 请求分发按维度 |
| https://api.cloudflare.com/client/v4/radar/ai/bots/timeseries | GET | Query | JSON | 获取 AI 机器人 HTTP 请求时间序列 |
| https://api.cloudflare.com/client/v4/radar/ai/bots/timeseries_groups/{dimension} | GET | Query | JSON | 获取时间序列分发的 AI 机器人 HTTP 请求按维度. |
| https://api.cloudflare.com/client/v4/radar/ai/inference/summary/{dimension} | GET | Query | JSON | 获取 Workers AI inference 分发按维度 |
| https://api.cloudflare.com/client/v4/radar/ai/inference/timeseries_groups/{dimension} | GET | Query | JSON | 获取时间序列分发的 Workers AI inference 按维度. |
| https://api.cloudflare.com/client/v4/radar/ai/markdown_for_agents/summary | GET | Query | JSON | 获取 AI markdown 用于智能体 reduction 比率摘要 |
| https://api.cloudflare.com/client/v4/radar/ai/markdown_for_agents/timeseries | GET | Query | JSON | 获取 AI markdown 用于智能体 reduction 比率时间序列 |
| https://api.cloudflare.com/client/v4/radar/annotations | GET | Query | JSON | 获取最新注释 |
| https://api.cloudflare.com/client/v4/radar/annotations/outages | GET | Query | JSON | 获取最新互联网中断与异常 |
| https://api.cloudflare.com/client/v4/radar/annotations/outages/locations | GET | Query | JSON | 获取数字的中断按位置 |
| https://api.cloudflare.com/client/v4/radar/as112/summary/{dimension} | GET | Query | JSON | 获取 AS112 摘要按维度 |
| https://api.cloudflare.com/client/v4/radar/as112/timeseries | GET | Query | JSON | 获取 AS112 DNS 查询时间序列 |
| https://api.cloudflare.com/client/v4/radar/as112/timeseries_groups/{dimension} | GET | Query | JSON | 获取 AS112 时间序列分组按维度 |
| https://api.cloudflare.com/client/v4/radar/as112/top/locations | GET | Query | JSON | 获取热门位置按 AS112 DNS 查询 |
| https://api.cloudflare.com/client/v4/radar/as112/top/locations/dnssec/{dnssec} | GET | Query | JSON | 获取热门位置按 AS112 DNS 查询含 DNSSEC 支持 |
| https://api.cloudflare.com/client/v4/radar/as112/top/locations/edns/{edns} | GET | Query | JSON | 获取热门位置按 AS112 DNS 查询含 EDNS 支持 |
| https://api.cloudflare.com/client/v4/radar/as112/top/locations/ip_version/{ip_version} | GET | Query | JSON | 获取热门位置按 AS112 DNS 查询用于 IP 版本 |
| https://api.cloudflare.com/client/v4/radar/attacks/layer3/summary/{dimension} | GET | Query | JSON | 获取层 3 攻击摘要按维度 |
| https://api.cloudflare.com/client/v4/radar/attacks/layer3/timeseries | GET | Query | JSON | 获取层 3 攻击按 bytes 时间序列 |
| https://api.cloudflare.com/client/v4/radar/attacks/layer3/timeseries_groups/{dimension} | GET | Query | JSON | 获取层 3 攻击时间序列分组按维度 |
| https://api.cloudflare.com/client/v4/radar/attacks/layer3/top/attacks | GET | Query | JSON | 获取热门层 3 攻击对 (源站与目标位置) |
| https://api.cloudflare.com/client/v4/radar/attacks/layer3/top/locations/origin | GET | Query | JSON | 获取热门源站位置的层 3 攻击 |
| https://api.cloudflare.com/client/v4/radar/attacks/layer3/top/locations/target | GET | Query | JSON | 获取热门目标位置的层 3 攻击 |
| https://api.cloudflare.com/client/v4/radar/attacks/layer7/summary/{dimension} | GET | Query | JSON | 获取层 7 攻击摘要按维度 |
| https://api.cloudflare.com/client/v4/radar/attacks/layer7/timeseries | GET | Query | JSON | 获取层 7 攻击时间序列 |
| https://api.cloudflare.com/client/v4/radar/attacks/layer7/timeseries_groups/{dimension} | GET | Query | JSON | 获取层 7 攻击时间序列分组按维度 |
| https://api.cloudflare.com/client/v4/radar/attacks/layer7/top/ases/origin | GET | Query | JSON | 获取热门源站自治系统的层 7 攻击 |
| https://api.cloudflare.com/client/v4/radar/attacks/layer7/top/attacks | GET | Query | JSON | 获取热门层 7 攻击对 (源站与目标位置) |
| https://api.cloudflare.com/client/v4/radar/attacks/layer7/top/locations/origin | GET | Query | JSON | 获取热门源站位置的层 7 攻击 |
| https://api.cloudflare.com/client/v4/radar/attacks/layer7/top/locations/target | GET | Query | JSON | 获取热门目标位置的层 7 攻击 |
| https://api.cloudflare.com/client/v4/radar/bgp/hijacks/events | GET | Query | JSON | 获取 BGP hijack 事件 |
| https://api.cloudflare.com/client/v4/radar/bgp/ips/timeseries | GET | Query | JSON | 获取 announced IP 地址空间时间序列 |
| https://api.cloudflare.com/client/v4/radar/bgp/ips/top/ases | GET | Query | JSON | 获取热门自治系统按 announced IP 空间 |
| https://api.cloudflare.com/client/v4/radar/bgp/leaks/events | GET | Query | JSON | 获取 BGP 路由 leak 事件 |
| https://api.cloudflare.com/client/v4/radar/bgp/routes/ases | GET | Query | JSON | 列出自治系统来自全局路由表 |
| https://api.cloudflare.com/client/v4/radar/bgp/routes/moas | GET | Query | JSON | 获取 Multi-Origin AS (MOAS) 前缀 |
| https://api.cloudflare.com/client/v4/radar/bgp/routes/paths/{asn} | GET | Query | JSON | 获取 tier-1 路径分段用于 AS |
| https://api.cloudflare.com/client/v4/radar/bgp/routes/pfx2as | GET | Query | JSON | 获取 prefix-to-ASN 映射 |
| https://api.cloudflare.com/client/v4/radar/bgp/routes/realtime | GET | Query | JSON | 获取实时 BGP 路由用于前缀 |
| https://api.cloudflare.com/client/v4/radar/bgp/routes/stats | GET | Query | JSON | 获取 BGP 路由表统计 |
| https://api.cloudflare.com/client/v4/radar/bgp/routes/upstreams/{asn}/timeseries | GET | Query | JSON | 获取上游 composition 时间序列用于 AS |
| https://api.cloudflare.com/client/v4/radar/bgp/rpki/aspa/changes | GET | Query | JSON | 获取 ASPA 变更 over 时间 |
| https://api.cloudflare.com/client/v4/radar/bgp/rpki/aspa/snapshot | GET | Query | JSON | 获取 ASPA 对象快照 |
| https://api.cloudflare.com/client/v4/radar/bgp/rpki/aspa/timeseries | GET | Query | JSON | 获取 ASPA 统计时间序列 |
| https://api.cloudflare.com/client/v4/radar/bgp/rpki/roas/timeseries | GET | Query | JSON | 获取 RPKI ROA 部署时间序列 |
| https://api.cloudflare.com/client/v4/radar/bgp/timeseries | GET | Query | JSON | 获取 BGP 时间序列 |
| https://api.cloudflare.com/client/v4/radar/bgp/top/ases | GET | Query | JSON | 获取热门自治系统按 BGP 更新 |
| https://api.cloudflare.com/client/v4/radar/bgp/top/ases/prefixes | GET | Query | JSON | 获取热门自治系统按前缀统计 |
| https://api.cloudflare.com/client/v4/radar/bgp/top/prefixes | GET | Query | JSON | 获取热门前缀按 BGP 更新 |
| https://api.cloudflare.com/client/v4/radar/bots | GET | Query | JSON | 列出机器人 |
| https://api.cloudflare.com/client/v4/radar/bots/crawlers/summary/{dimension} | GET | Query | JSON | 获取 crawler HTTP 请求分发按维度 |
| https://api.cloudflare.com/client/v4/radar/bots/crawlers/timeseries_groups/{dimension} | GET | Query | JSON | 获取时间序列的 crawler HTTP 请求分发按维度 |
| https://api.cloudflare.com/client/v4/radar/bots/summary/{dimension} | GET | Query | JSON | 获取机器人 HTTP 请求分发按维度 |
| https://api.cloudflare.com/client/v4/radar/bots/timeseries | GET | Query | JSON | 获取机器人 HTTP 请求时间序列 |
| https://api.cloudflare.com/client/v4/radar/bots/timeseries_groups/{dimension} | GET | Query | JSON | 获取时间序列分发的机器人 HTTP 请求按维度. |
| https://api.cloudflare.com/client/v4/radar/bots/{bot_slug} | GET | Query | JSON | 获取机器人详情 |
| https://api.cloudflare.com/client/v4/radar/ct/authorities | GET | Query | JSON | 列出证书权威 |
| https://api.cloudflare.com/client/v4/radar/ct/authorities/{ca_slug} | GET | Query | JSON | 获取证书权威详情 |
| https://api.cloudflare.com/client/v4/radar/ct/logs | GET | Query | JSON | 列出证书日志 |
| https://api.cloudflare.com/client/v4/radar/ct/logs/{log_slug} | GET | Query | JSON | 获取证书日志详情 |
| https://api.cloudflare.com/client/v4/radar/ct/summary/{dimension} | GET | Query | JSON | 获取证书分发按维度 |
| https://api.cloudflare.com/client/v4/radar/ct/timeseries | GET | Query | JSON | 获取证书时间序列 |
| https://api.cloudflare.com/client/v4/radar/ct/timeseries_groups/{dimension} | GET | Query | JSON | 获取时间序列的证书分发按维度 |
| https://api.cloudflare.com/client/v4/radar/datasets | GET | Query | JSON | 列出数据集 |
| https://api.cloudflare.com/client/v4/radar/datasets/download | POST | JSON | JSON | 获取数据集下载 URL |
| https://api.cloudflare.com/client/v4/radar/datasets/{alias} | GET | Query | JSON | 获取数据集 CSV stream |
| https://api.cloudflare.com/client/v4/radar/dns/summary/{dimension} | GET | Query | JSON | 获取 DNS 摘要按维度 |
| https://api.cloudflare.com/client/v4/radar/dns/timeseries | GET | Query | JSON | 获取 DNS 查询时间序列 |
| https://api.cloudflare.com/client/v4/radar/dns/timeseries_groups/{dimension} | GET | Query | JSON | 获取 DNS 时间序列分组按维度 |
| https://api.cloudflare.com/client/v4/radar/dns/top/ases | GET | Query | JSON | 获取热门自治系统按 DNS 查询 |
| https://api.cloudflare.com/client/v4/radar/dns/top/locations | GET | Query | JSON | 获取热门位置按 DNS 查询 |
| https://api.cloudflare.com/client/v4/radar/email/routing/summary/{dimension} | GET | Query | JSON | 获取邮件路由摘要按维度 |
| https://api.cloudflare.com/client/v4/radar/email/routing/timeseries_groups/{dimension} | GET | Query | JSON | 获取邮件路由时间序列分组按维度 |
| https://api.cloudflare.com/client/v4/radar/email/security/summary/{dimension} | GET | Query | JSON | 获取邮件安全摘要按维度 |
| https://api.cloudflare.com/client/v4/radar/email/security/timeseries_groups/{dimension} | GET | Query | JSON | 获取邮件安全时间序列分组按维度 |
| https://api.cloudflare.com/client/v4/radar/email/security/top/tlds | GET | Query | JSON | 获取热门 TLDs 按邮箱消息流量 |
| https://api.cloudflare.com/client/v4/radar/email/security/top/tlds/malicious/{malicious} | GET | Query | JSON | 获取热门 TLDs 按邮箱 malicious 分类 |
| https://api.cloudflare.com/client/v4/radar/email/security/top/tlds/spam/{spam} | GET | Query | JSON | 获取热门 TLDs 按邮箱垃圾邮件分类 |
| https://api.cloudflare.com/client/v4/radar/email/security/top/tlds/spoof/{spoof} | GET | Query | JSON | 获取热门 TLDs 按邮箱 spoof 分类 |
| https://api.cloudflare.com/client/v4/radar/entities/asns | GET | Query | JSON | 列出 autonomous systems |
| https://api.cloudflare.com/client/v4/radar/entities/asns/botnet_threat_feed | GET | Query | JSON | 获取 AS rankings 按 botnet 威胁信息流活动 |
| https://api.cloudflare.com/client/v4/radar/entities/asns/ip | GET | Query | JSON | 获取 AS 详情按 IP 地址 |
| https://api.cloudflare.com/client/v4/radar/entities/asns/{asn} | GET | Query | JSON | 获取 AS 详情按 ASN |
| https://api.cloudflare.com/client/v4/radar/entities/asns/{asn}/as_set | GET | Query | JSON | 获取 IRR AS-SETs AS 是否成员的 |
| https://api.cloudflare.com/client/v4/radar/entities/asns/{asn}/rel | GET | Query | JSON | 获取 AS-level relationships 按 ASN |
| https://api.cloudflare.com/client/v4/radar/entities/ip | GET | Query | JSON | 获取 IP 地址详情 |
| https://api.cloudflare.com/client/v4/radar/entities/locations | GET | Query | JSON | 列出位置 |
| https://api.cloudflare.com/client/v4/radar/entities/locations/{location} | GET | Query | JSON | 获取位置详情 |
| https://api.cloudflare.com/client/v4/radar/geolocations | GET | Query | JSON | 列出 Geolocations |
| https://api.cloudflare.com/client/v4/radar/geolocations/{geo_id} | GET | Query | JSON | 获取 Geolocation 详情 |
| https://api.cloudflare.com/client/v4/radar/http/summary/{dimension} | GET | Query | JSON | 获取 HTTP 请求摘要按维度 |
| https://api.cloudflare.com/client/v4/radar/http/timeseries | GET | Query | JSON | 获取 HTTP 请求时间序列 |
| https://api.cloudflare.com/client/v4/radar/http/timeseries_groups/{dimension} | GET | Query | JSON | 获取 HTTP 请求时间序列分组按维度 |
| https://api.cloudflare.com/client/v4/radar/http/top/ases | GET | Query | JSON | 获取热门自治系统按 HTTP 请求 |
| https://api.cloudflare.com/client/v4/radar/http/top/ases/bot_class/{bot_class} | GET | Query | JSON | 获取热门自治系统按 HTTP 请求用于机器人类别 |
| https://api.cloudflare.com/client/v4/radar/http/top/ases/browser_family/{browser_family} | GET | Query | JSON | 获取热门自治系统按 HTTP 请求用于浏览器 family |
| https://api.cloudflare.com/client/v4/radar/http/top/ases/device_type/{device_type} | GET | Query | JSON | 获取热门自治系统按 HTTP 请求用于设备类型 |
| https://api.cloudflare.com/client/v4/radar/http/top/ases/http_protocol/{http_protocol} | GET | Query | JSON | 获取热门自治系统按 HTTP 请求用于 HTTP 协议 |
| https://api.cloudflare.com/client/v4/radar/http/top/ases/http_version/{http_version} | GET | Query | JSON | 获取热门自治系统按 HTTP 请求用于 HTTP 版本 |
| https://api.cloudflare.com/client/v4/radar/http/top/ases/ip_version/{ip_version} | GET | Query | JSON | 获取热门自治系统按 HTTP 请求用于 IP 版本 |
| https://api.cloudflare.com/client/v4/radar/http/top/ases/os/{os} | GET | Query | JSON | 获取热门自治系统按 HTTP 请求用于 OS |
| https://api.cloudflare.com/client/v4/radar/http/top/ases/tls_version/{tls_version} | GET | Query | JSON | 获取热门自治系统按 HTTP 请求用于 TLS 版本 |
| https://api.cloudflare.com/client/v4/radar/http/top/locations | GET | Query | JSON | 获取热门位置按 HTTP 请求 |
| https://api.cloudflare.com/client/v4/radar/http/top/locations/bot_class/{bot_class} | GET | Query | JSON | 获取热门位置按 HTTP 请求用于机器人类别 |
| https://api.cloudflare.com/client/v4/radar/http/top/locations/browser_family/{browser_family} | GET | Query | JSON | 获取热门位置按 HTTP 请求用于浏览器 family |
| https://api.cloudflare.com/client/v4/radar/http/top/locations/device_type/{device_type} | GET | Query | JSON | 获取热门位置按 HTTP 请求用于设备类型 |
| https://api.cloudflare.com/client/v4/radar/http/top/locations/http_protocol/{http_protocol} | GET | Query | JSON | 获取热门位置按 HTTP 请求用于 HTTP 协议 |
| https://api.cloudflare.com/client/v4/radar/http/top/locations/http_version/{http_version} | GET | Query | JSON | 获取热门位置按 HTTP 请求用于 HTTP 版本 |
| https://api.cloudflare.com/client/v4/radar/http/top/locations/ip_version/{ip_version} | GET | Query | JSON | 获取热门位置按 HTTP 请求用于 IP 版本 |
| https://api.cloudflare.com/client/v4/radar/http/top/locations/os/{os} | GET | Query | JSON | 获取热门位置按 HTTP 请求用于 OS |
| https://api.cloudflare.com/client/v4/radar/http/top/locations/tls_version/{tls_version} | GET | Query | JSON | 获取热门位置按 HTTP 请求用于 TLS 版本 |
| https://api.cloudflare.com/client/v4/radar/leaked_credential_checks/summary/{dimension} | GET | Query | JSON | 获取 HTTP 认证请求分发按维度 |
| https://api.cloudflare.com/client/v4/radar/leaked_credential_checks/timeseries_groups/{dimension} | GET | Query | JSON | 获取时间序列分发的 HTTP 认证请求按维度. |
| https://api.cloudflare.com/client/v4/radar/netflows/summary/{dimension} | GET | Query | JSON | 获取网络流量分发按维度 |
| https://api.cloudflare.com/client/v4/radar/netflows/timeseries | GET | Query | JSON | 获取网络流量时间序列 |
| https://api.cloudflare.com/client/v4/radar/netflows/timeseries_groups/{dimension} | GET | Query | JSON | 获取时间序列分发的网络流量按维度 |
| https://api.cloudflare.com/client/v4/radar/netflows/top/ases | GET | Query | JSON | 获取热门自治系统按网络流量 |
| https://api.cloudflare.com/client/v4/radar/netflows/top/locations | GET | Query | JSON | 获取热门位置按网络流量 |
| https://api.cloudflare.com/client/v4/radar/origins | GET | Query | JSON | 列出源站 |
| https://api.cloudflare.com/client/v4/radar/origins/summary/{dimension} | GET | Query | JSON | 获取源站指标分发按维度 |
| https://api.cloudflare.com/client/v4/radar/origins/timeseries | GET | Query | JSON | 获取源站指标时间序列 |
| https://api.cloudflare.com/client/v4/radar/origins/timeseries_groups/{dimension} | GET | Query | JSON | 获取源站指标时间序列分组按维度 |
| https://api.cloudflare.com/client/v4/radar/origins/{slug} | GET | Query | JSON | 获取源站详情 |
| https://api.cloudflare.com/client/v4/radar/post_quantum/origin/summary/{dimension} | GET | Query | JSON | 获取源站后量子数据摘要 |
| https://api.cloudflare.com/client/v4/radar/post_quantum/origin/timeseries_groups/{dimension} | GET | Query | JSON | 获取源站后量子数据 Over 时间 |
| https://api.cloudflare.com/client/v4/radar/post_quantum/tls/support | GET | Query | JSON | 检查后量子 TLS 支持 |
| https://api.cloudflare.com/client/v4/radar/quality/iqi/summary | GET | Query | JSON | 获取互联网质量索引 (IQI) 摘要 |
| https://api.cloudflare.com/client/v4/radar/quality/iqi/timeseries_groups | GET | Query | JSON | 获取互联网质量索引 (IQI) 时间序列 |
| https://api.cloudflare.com/client/v4/radar/quality/speed/histogram | GET | Query | JSON | 获取速度测试 histogram |
| https://api.cloudflare.com/client/v4/radar/quality/speed/summary | GET | Query | JSON | 获取速度测试摘要 |
| https://api.cloudflare.com/client/v4/radar/quality/speed/top/ases | GET | Query | JSON | 获取热门自治系统按速度测试结果 |
| https://api.cloudflare.com/client/v4/radar/quality/speed/top/locations | GET | Query | JSON | 获取热门位置按速度测试结果 |
| https://api.cloudflare.com/client/v4/radar/ranking/domain/{domain} | GET | Query | JSON | 获取域名排名详情 |
| https://api.cloudflare.com/client/v4/radar/ranking/internet_services/categories | GET | Query | JSON | 列出互联网服务类别 |
| https://api.cloudflare.com/client/v4/radar/ranking/internet_services/timeseries_groups | GET | Query | JSON | 获取互联网服务排名时间序列 |
| https://api.cloudflare.com/client/v4/radar/ranking/internet_services/top | GET | Query | JSON | 获取热门互联网服务 |
| https://api.cloudflare.com/client/v4/radar/ranking/timeseries_groups | GET | Query | JSON | 获取域名排名时间序列 |
| https://api.cloudflare.com/client/v4/radar/ranking/top | GET | Query | JSON | 获取热门或 trending 域名 |
| https://api.cloudflare.com/client/v4/radar/robots_txt/top/domain_categories | GET | Query | JSON | 获取热门域名类别按爬虫.txt 文件 parsed |
| https://api.cloudflare.com/client/v4/radar/robots_txt/top/user_agents/directive | GET | Query | JSON | 获取热门用户智能体上爬虫.txt 文件 |
| https://api.cloudflare.com/client/v4/radar/search/global | GET | Query | JSON | 搜索用于位置, 自治系统, 报告, 与 more |
| https://api.cloudflare.com/client/v4/radar/tcp_resets_timeouts/summary | GET | Query | JSON | 获取 TCP resets 与超时摘要 |
| https://api.cloudflare.com/client/v4/radar/tcp_resets_timeouts/timeseries_groups | GET | Query | JSON | 获取 TCP resets 与超时时间序列 |
| https://api.cloudflare.com/client/v4/radar/tlds | GET | Query | JSON | 列出 TLDs |
| https://api.cloudflare.com/client/v4/radar/tlds/performance/summary/{dimension} | GET | Query | JSON | 获取顶级域名性能摘要 |
| https://api.cloudflare.com/client/v4/radar/tlds/performance/timeseries_groups/{dimension} | GET | Query | JSON | 获取顶级域名性能 Over 时间 |
| https://api.cloudflare.com/client/v4/radar/tlds/{tld} | GET | Query | JSON | 获取顶级域名详情 |
| https://api.cloudflare.com/client/v4/radar/traffic_anomalies | GET | Query | JSON | 获取最新互联网流量异常 |
| https://api.cloudflare.com/client/v4/radar/traffic_anomalies/locations | GET | Query | JSON | 获取热门位置按总数流量异常 |

## DNS 与域名解析（149 条）

> DNS 记录与区域设置、DNS 防火墙、区域（Zone）管理、自定义主机名、名称服务器、域名注册与注册商沙箱。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/custom_ns | GET | Query | JSON | 列出账户自定义名称服务器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/custom_ns | POST | JSON | JSON | 添加账户自定义名称服务器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/custom_ns/{custom_ns_id} | DELETE | Query | JSON | 删除账户自定义名称服务器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dns_firewall | GET | Query | JSON | 列出 DNS 防火墙集群 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dns_firewall | POST | JSON | JSON | 创建 DNS 防火墙集群 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dns_firewall/{dns_firewall_id} | DELETE | Query | JSON | 删除 DNS 防火墙集群 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dns_firewall/{dns_firewall_id} | GET | Query | JSON | DNS 防火墙集群详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dns_firewall/{dns_firewall_id} | PATCH | JSON | JSON | 更新 DNS 防火墙集群 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dns_firewall/{dns_firewall_id}/reverse_dns | GET | Query | JSON | 显示 DNS 防火墙集群 Reverse DNS |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dns_firewall/{dns_firewall_id}/reverse_dns | PATCH | JSON | JSON | 更新 DNS 防火墙集群 Reverse DNS |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dns_records/usage | GET | Query | JSON | 获取 DNS 记录用量用于账户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dns_settings | GET | Query | JSON | 显示 DNS 设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dns_settings | PATCH | JSON | JSON | 更新 DNS 设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dns_settings/nameserver_sets | GET | Query | JSON | 列出自定义名称服务器设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dns_settings/nameserver_sets | POST | JSON | JSON | 创建自定义名称服务器设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dns_settings/nameserver_sets/{nameserver_set_id} | DELETE | Query | JSON | 删除自定义名称服务器设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dns_settings/nameserver_sets/{nameserver_set_id} | GET | Query | JSON | 获取自定义名称服务器设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dns_settings/views | GET | Query | JSON | 列出内部 DNS 查看 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dns_settings/views | POST | JSON | JSON | 创建内部 DNS 查看 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dns_settings/views/{view_id} | DELETE | Query | JSON | 删除内部 DNS 查看 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dns_settings/views/{view_id} | GET | Query | JSON | DNS 内部查看详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/dns_settings/views/{view_id} | PATCH | JSON | JSON | 更新内部 DNS 查看 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/registrar-sandbox/domain-check | POST | JSON | JSON | 检查域名可用性 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/registrar-sandbox/domain-search | GET | Query | JSON | 搜索用于可用域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/registrar-sandbox/extensions | GET | Query | JSON | 列出扩展 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/registrar-sandbox/extensions/{extension} | GET | Query | JSON | 获取扩展 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/registrar-sandbox/registrations | GET | Query | JSON | 列出注册 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/registrar-sandbox/registrations | POST | JSON | JSON | 创建注册 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/registrar-sandbox/registrations/{domain_name} | GET | Query | JSON | 获取注册 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/registrar-sandbox/registrations/{domain_name} | PATCH | JSON | JSON | 更新注册 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/registrar-sandbox/registrations/{domain_name}/registration-status | GET | Query | JSON | 获取注册状态 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/registrar-sandbox/registrations/{domain_name}/update-status | GET | Query | JSON | 获取更新状态 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/registrar/domain-check | POST | JSON | JSON | 检查域名可用性 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/registrar/domain-search | GET | Query | JSON | 搜索用于可用域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/registrar/domain-transfer-check | POST | JSON | JSON | 检查域名传输 eligibility |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/registrar/extensions | GET | Query | JSON | 列出扩展 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/registrar/extensions/{extension} | GET | Query | JSON | 获取扩展 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/registrar/registrations | GET | Query | JSON | 列出注册 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/registrar/registrations | POST | JSON | JSON | 创建注册 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/registrar/registrations/{domain_name} | GET | Query | JSON | 获取注册 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/registrar/registrations/{domain_name} | PATCH | JSON | JSON | 更新注册 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/registrar/registrations/{domain_name}/registration-status | GET | Query | JSON | 获取注册状态 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/registrar/registrations/{domain_name}/transfer-in | POST | JSON | JSON | Initiate 传输 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/registrar/registrations/{domain_name}/transfer-in-status | GET | Query | JSON | 获取传输状态 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/registrar/registrations/{domain_name}/update-status | GET | Query | JSON | 获取更新状态 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secondary_dns/acls | GET | Query | JSON | 列出 ACLs |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secondary_dns/acls | POST | JSON | JSON | 创建 ACL |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secondary_dns/acls/{acl_id} | DELETE | Query | JSON | 删除 ACL |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secondary_dns/acls/{acl_id} | GET | Query | JSON | ACL 详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secondary_dns/acls/{acl_id} | PUT | JSON | JSON | 更新 ACL |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secondary_dns/peers | GET | Query | JSON | 列出对等体 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secondary_dns/peers | POST | JSON | JSON | 创建对等体 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secondary_dns/peers/{peer_id} | DELETE | Query | JSON | 删除对等体 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secondary_dns/peers/{peer_id} | GET | Query | JSON | 对等体详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secondary_dns/peers/{peer_id} | PUT | JSON | JSON | 更新对等体 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secondary_dns/tsigs | GET | Query | JSON | 列出 TSIGs |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secondary_dns/tsigs | POST | JSON | JSON | 创建 TSIG |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secondary_dns/tsigs/{tsig_id} | DELETE | Query | JSON | 删除 TSIG |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secondary_dns/tsigs/{tsig_id} | GET | Query | JSON | TSIG 详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secondary_dns/tsigs/{tsig_id} | PUT | JSON | JSON | 更新 TSIG |
| https://api.cloudflare.com/client/v4/tenants/{tenant_tag}/custom_ns | GET | Query | JSON | 列出租户自定义名称服务器 |
| https://api.cloudflare.com/client/v4/tenants/{tenant_tag}/custom_ns | POST | JSON | JSON | 添加租户自定义名称服务器 |
| https://api.cloudflare.com/client/v4/tenants/{tenant_tag}/custom_ns/{custom_ns_id} | DELETE | Query | JSON | 删除租户自定义名称服务器 |
| https://api.cloudflare.com/client/v4/zones | GET | Query | JSON | 列出区域 |
| https://api.cloudflare.com/client/v4/zones | POST | JSON | JSON | 创建区域 |
| https://api.cloudflare.com/client/v4/zones/{zone_id} | DELETE | Query | JSON | 删除区域 |
| https://api.cloudflare.com/client/v4/zones/{zone_id} | GET | Query | JSON | 区域详情 |
| https://api.cloudflare.com/client/v4/zones/{zone_id} | PATCH | JSON | JSON | 编辑区域 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/activation_check | PUT | JSON | JSON | 重新运行激活检查 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/available_plans | GET | Query | JSON | 列出可用套餐 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/available_plans/{plan_identifier} | GET | Query | JSON | 可用套餐详情 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/available_rate_plans | GET | Query | JSON | 列出可用速率套餐 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/ct/alerting | GET | Query | JSON | 获取 CT 告警订阅 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/ct/alerting | PATCH | JSON | JSON | 更新 CT 告警订阅 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/custom_hostnames | GET | Query | JSON | 列出自定义主机名 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/custom_hostnames | POST | JSON | JSON | 创建自定义主机名 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/custom_hostnames/fallback_origin | DELETE | Query | JSON | 删除回退源站用于自定义主机名 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/custom_hostnames/fallback_origin | GET | Query | JSON | 获取回退源站用于自定义主机名 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/custom_hostnames/fallback_origin | PUT | JSON | JSON | 更新回退源站用于自定义主机名 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/custom_hostnames/quota | GET | Query | JSON | 获取自定义主机名配额 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/custom_hostnames/{custom_hostname_id} | DELETE | Query | JSON | 删除自定义主机名 (与 any issued SSL 证书) |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/custom_hostnames/{custom_hostname_id} | GET | Query | JSON | 自定义主机名详情 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/custom_hostnames/{custom_hostname_id} | PATCH | JSON | JSON | 编辑自定义主机名 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/custom_hostnames/{custom_hostname_id}/certificate_pack/{certificate_pack_id}/certificates/{certificate_id} | DELETE | Query | JSON | 删除单个证书与键用于自定义主机名 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/custom_hostnames/{custom_hostname_id}/certificate_pack/{certificate_pack_id}/certificates/{certificate_id} | PUT | JSON | JSON | 替换自定义证书与自定义键内自定义主机名 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/dns_records | GET | Query | JSON | 列出 DNS 记录 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/dns_records | POST | JSON | JSON | 创建 DNS 记录 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/dns_records/batch | POST | JSON | JSON | 批次 DNS 记录 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/dns_records/export | GET | Query | JSON | 导出 DNS 记录 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/dns_records/import | POST | JSON | JSON | 导入 DNS 记录 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/dns_records/scan/review | GET | Query | JSON | 列出 Scanned DNS 记录 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/dns_records/scan/review | POST | JSON | JSON | 审核 Scanned DNS 记录 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/dns_records/scan/trigger | POST | JSON | JSON | 触发 DNS 记录扫描 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/dns_records/usage | GET | Query | JSON | 获取 DNS 记录用量 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/dns_records/{dns_record_id} | DELETE | Query | JSON | 删除 DNS 记录 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/dns_records/{dns_record_id} | GET | Query | JSON | DNS 记录详情 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/dns_records/{dns_record_id} | PATCH | JSON | JSON | 更新 DNS 记录 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/dns_records/{dns_record_id} | PUT | JSON | JSON | Overwrite DNS 记录 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/dns_settings | GET | Query | JSON | 显示 DNS 设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/dns_settings | PATCH | JSON | JSON | 更新 DNS 设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/dnssec | DELETE | Query | JSON | 删除 DNSSEC 记录 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/dnssec | GET | Query | JSON | DNSSEC 详情 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/dnssec | PATCH | JSON | JSON | 编辑 DNSSEC 状态 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/dnssec/zsk | GET | Query | JSON | 列出 DNSSEC ZSKs |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/entitlements | GET | Query | JSON | 获取区域权益 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/environments | GET | Query | JSON | 列出区域环境 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/environments | PATCH | JSON | JSON | 部分更新区域环境 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/environments | POST | JSON | JSON | 创建区域环境 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/environments | PUT | JSON | JSON | 插入或更新区域环境 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/environments/{environment_id} | DELETE | Query | JSON | 删除区域环境 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/environments/{environment_id}/rollback | POST | JSON | JSON | 回滚区域环境 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/hold | DELETE | Query | JSON | 移除区域暂停 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/hold | GET | Query | JSON | 获取区域暂停 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/hold | PATCH | JSON | JSON | 更新区域暂停 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/hold | POST | JSON | JSON | 创建区域暂停 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/hostnames/settings/{setting_id} | GET | Query | JSON | 列出 TLS 设置用于主机名 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/hostnames/settings/{setting_id}/{hostname} | DELETE | Query | JSON | 删除 TLS 设置用于主机名 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/hostnames/settings/{setting_id}/{hostname} | GET | Query | JSON | 获取 TLS 设置用于主机名 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/hostnames/settings/{setting_id}/{hostname} | PUT | JSON | JSON | 编辑 TLS 设置用于主机名 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/observability/tracing/rules | DELETE | Query | JSON | 删除区域追踪规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/observability/tracing/rules | GET | Query | JSON | 查看区域追踪规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/observability/tracing/rules | PUT | JSON | JSON | 替换区域追踪规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/observability/tracing/settings | DELETE | Query | JSON | 重置区域追踪设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/observability/tracing/settings | GET | Query | JSON | 查看区域追踪设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/observability/tracing/settings | PATCH | JSON | JSON | 更新区域追踪设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/secondary_dns/force_axfr | POST | JSON | JSON | 强制 AXFR |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/secondary_dns/incoming | DELETE | Query | JSON | 删除辅助区域配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/secondary_dns/incoming | GET | Query | JSON | 辅助区域配置详情 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/secondary_dns/incoming | POST | JSON | JSON | 创建辅助区域配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/secondary_dns/incoming | PUT | JSON | JSON | 更新辅助区域配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/secondary_dns/outgoing | DELETE | Query | JSON | 删除主要区域配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/secondary_dns/outgoing | GET | Query | JSON | 主要区域配置详情 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/secondary_dns/outgoing | POST | JSON | JSON | 创建主要区域配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/secondary_dns/outgoing | PUT | JSON | JSON | 更新主要区域配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/secondary_dns/outgoing/disable | POST | JSON | JSON | 禁用出站区域传输 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/secondary_dns/outgoing/enable | POST | JSON | JSON | 启用出站区域传输 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/secondary_dns/outgoing/force_notify | POST | JSON | JSON | 强制 DNS 通知 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/secondary_dns/outgoing/status | GET | Query | JSON | 获取出站区域传输状态 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/nel | GET | Query | JSON | 获取 NEL 设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/nel | PATCH | JSON | JSON | 编辑 NEL 设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/transformations_allowed_origins | GET | Query | JSON | 获取图片转换允许源站设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/transformations_allowed_origins | PATCH | JSON | JSON | 变更图片转换允许源站设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/transformations_c2pa | GET | Query | JSON | 获取图片转换 C2PA 设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/transformations_c2pa | PATCH | JSON | JSON | 变更图片转换 C2PA 设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/{setting_id} | GET | Query | JSON | 获取区域设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/{setting_id} | PATCH | JSON | JSON | 编辑区域设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/subscription | GET | Query | JSON | 区域订阅详情 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/subscription | POST | JSON | JSON | 创建区域订阅 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/subscription | PUT | JSON | JSON | 更新区域订阅 |

## 存储与数据服务（143 条）

> R2 对象存储、KV 键值存储、D1 数据库、Durable Objects、Hyperdrive、Vectorize 向量库、密钥库、Web3、MoQ、Cloudflare Calls 实时媒体、Speed 测速。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/calls/apps | GET | Query | JSON | 列出应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/calls/apps | POST | JSON | JSON | 创建 SFU 应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/calls/apps/{app_id} | DELETE | Query | JSON | 删除 SFU 应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/calls/apps/{app_id} | GET | Query | JSON | 获取 SFU 应用详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/calls/apps/{app_id} | PUT | JSON | JSON | 更新 SFU 应用详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/calls/turn_keys | GET | Query | JSON | 列出 TURN 密钥 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/calls/turn_keys | POST | JSON | JSON | 创建 TURN 键 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/calls/turn_keys/{key_id} | DELETE | Query | JSON | 删除 TURN 键 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/calls/turn_keys/{key_id} | GET | Query | JSON | 获取 TURN 键详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/calls/turn_keys/{key_id} | PUT | JSON | JSON | 更新 TURN 键详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/d1/database | GET | Query | JSON | 列出 D1 数据库 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/d1/database | POST | JSON | JSON | 创建 D1 数据库 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/d1/database/{database_id} | DELETE | Query | JSON | 删除 D1 数据库 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/d1/database/{database_id} | GET | Query | JSON | 获取 D1 数据库 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/d1/database/{database_id} | PATCH | JSON | JSON | 更新 D1 数据库部分 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/d1/database/{database_id} | PUT | JSON | JSON | 更新 D1 数据库 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/d1/database/{database_id}/export | POST | JSON | JSON | 导出 D1 数据库 as SQL |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/d1/database/{database_id}/import | POST | JSON | JSON | 导入 SQL into 你的 D1 数据库 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/d1/database/{database_id}/query | POST | JSON | JSON | 查询 D1 数据库 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/d1/database/{database_id}/raw | POST | JSON | JSON | 原始 D1 数据库查询 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/d1/database/{database_id}/time_travel/bookmark | GET | Query | JSON | 获取 D1 数据库 bookmark |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/d1/database/{database_id}/time_travel/restore | POST | JSON | JSON | 还原 D1 数据库给 bookmark 或点内时间 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/event_notifications/r2/{bucket_name}/configuration | GET | Query | JSON | 列出事件通知规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/event_notifications/r2/{bucket_name}/configuration/queues/{queue_id} | DELETE | Query | JSON | 删除事件通知规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/event_notifications/r2/{bucket_name}/configuration/queues/{queue_id} | GET | Query | JSON | 获取事件通知规则用于队列 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/event_notifications/r2/{bucket_name}/configuration/queues/{queue_id} | PUT | JSON | JSON | 创建事件通知规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/hyperdrive/configs | GET | Query | JSON | 列出 Hyperdrives |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/hyperdrive/configs | POST | JSON | JSON | 创建 Hyperdrive |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/hyperdrive/configs/{hyperdrive_id} | DELETE | Query | JSON | 删除 Hyperdrive |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/hyperdrive/configs/{hyperdrive_id} | GET | Query | JSON | 获取 Hyperdrive |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/hyperdrive/configs/{hyperdrive_id} | PATCH | JSON | JSON | 更新 Hyperdrive |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/hyperdrive/configs/{hyperdrive_id} | PUT | JSON | JSON | 替换 Hyperdrive |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/hyperdrive/configs/{hyperdrive_id}/restart | POST | JSON | JSON | 重启 Hyperdrive |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/moq/relays | GET | Query | JSON | 列出中继 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/moq/relays | POST | JSON | JSON | 创建中继 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/moq/relays/{relay_id} | DELETE | Query | JSON | 删除中继 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/moq/relays/{relay_id} | GET | Query | JSON | 获取中继 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/moq/relays/{relay_id} | PUT | JSON | JSON | 更新中继 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/moq/relays/{relay_id}/tokens | GET | Query | JSON | 列出令牌 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/moq/relays/{relay_id}/tokens | POST | JSON | JSON | 创建令牌 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/moq/relays/{relay_id}/tokens/{jti} | DELETE | Query | JSON | 撤销令牌 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/buckets | GET | Query | JSON | 列出存储桶 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/buckets | POST | JSON | JSON | 创建存储桶 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/buckets/{bucket_name} | DELETE | Query | JSON | 删除存储桶 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/buckets/{bucket_name} | GET | Query | JSON | 获取存储桶 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/buckets/{bucket_name} | PATCH | JSON | JSON | 修改存储桶 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/buckets/{bucket_name}/cors | DELETE | Query | JSON | 删除存储桶 CORS 策略 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/buckets/{bucket_name}/cors | GET | Query | JSON | 获取存储桶 CORS 策略 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/buckets/{bucket_name}/cors | PUT | JSON | JSON | 设置存储桶 CORS 策略 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/buckets/{bucket_name}/domains/custom | GET | Query | JSON | 列出自定义域名的存储桶 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/buckets/{bucket_name}/domains/custom | POST | JSON | JSON | 附加自定义域名给存储桶 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/buckets/{bucket_name}/domains/custom/{domain} | DELETE | Query | JSON | 移除自定义域名来自存储桶 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/buckets/{bucket_name}/domains/custom/{domain} | GET | Query | JSON | 获取自定义域名设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/buckets/{bucket_name}/domains/custom/{domain} | PUT | JSON | JSON | 配置自定义域名设置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/buckets/{bucket_name}/domains/managed | GET | Query | JSON | 获取 r2.dev 域名的存储桶 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/buckets/{bucket_name}/domains/managed | PUT | JSON | JSON | 更新 r2.dev 域名的存储桶 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/buckets/{bucket_name}/lifecycle | GET | Query | JSON | 获取对象 Lifecycle 规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/buckets/{bucket_name}/lifecycle | PUT | JSON | JSON | 设置对象 Lifecycle 规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/buckets/{bucket_name}/lock | GET | Query | JSON | 获取存储桶锁定规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/buckets/{bucket_name}/lock | PUT | JSON | JSON | 设置存储桶锁定规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/buckets/{bucket_name}/objects | GET | Query | JSON | 列出对象 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/buckets/{bucket_name}/objects/{object_key} | DELETE | Query | JSON | 删除对象 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/buckets/{bucket_name}/objects/{object_key} | GET | Query | JSON | 获取对象 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/buckets/{bucket_name}/objects/{object_key} | PUT | JSON | JSON | 上传对象 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/buckets/{bucket_name}/sippy | DELETE | Query | JSON | 禁用 Sippy |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/buckets/{bucket_name}/sippy | GET | Query | JSON | 获取 Sippy 配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/buckets/{bucket_name}/sippy | PUT | JSON | JSON | 启用 Sippy |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/metrics | GET | Query | JSON | 获取账户级指标 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/r2/temp-access-credentials | POST | JSON | JSON | 创建临时访问凭证 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secrets_store/quota | GET | Query | JSON | 查看密钥用量 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secrets_store/stores | GET | Query | JSON | 列出账户存储 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secrets_store/stores | POST | JSON | JSON | 创建存储 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secrets_store/stores/{store_id} | DELETE | Query | JSON | 删除存储 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secrets_store/stores/{store_id} | GET | Query | JSON | 获取存储按 ID |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secrets_store/stores/{store_id}/secrets | DELETE | Query | JSON | 删除密钥 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secrets_store/stores/{store_id}/secrets | GET | Query | JSON | 列出存储密钥 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secrets_store/stores/{store_id}/secrets | POST | JSON | JSON | 创建密钥 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secrets_store/stores/{store_id}/secrets/{secret_id} | DELETE | Query | JSON | 删除密钥 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secrets_store/stores/{store_id}/secrets/{secret_id} | GET | Query | JSON | 获取密钥按 ID |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secrets_store/stores/{store_id}/secrets/{secret_id} | PATCH | JSON | JSON | 修改密钥 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/secrets_store/stores/{store_id}/secrets/{secret_id}/duplicate | POST | JSON | JSON | 复制密钥 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/slurper/jobs | GET | Query | JSON | 列出任务 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/slurper/jobs | POST | JSON | JSON | 创建任务 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/slurper/jobs/abortAll | PUT | JSON | JSON | 中止全部任务 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/slurper/jobs/{job_id} | GET | Query | JSON | 获取任务详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/slurper/jobs/{job_id}/abort | PUT | JSON | JSON | 中止任务 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/slurper/jobs/{job_id}/logs | GET | Query | JSON | 获取任务日志 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/slurper/jobs/{job_id}/pause | PUT | JSON | JSON | 暂停任务 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/slurper/jobs/{job_id}/progress | GET | Query | JSON | 获取任务 progress |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/slurper/jobs/{job_id}/resume | PUT | JSON | JSON | 恢复任务 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/slurper/source/connectivity-precheck | PUT | JSON | JSON | 检查来源连通性 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/slurper/target/connectivity-precheck | PUT | JSON | JSON | 检查目标连通性 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/storage/kv/namespaces | GET | Query | JSON | 列出命名空间 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/storage/kv/namespaces | POST | JSON | JSON | 创建命名空间 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/storage/kv/namespaces/{namespace_id} | DELETE | Query | JSON | 删除命名空间 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/storage/kv/namespaces/{namespace_id} | GET | Query | JSON | 获取命名空间 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/storage/kv/namespaces/{namespace_id} | PUT | JSON | JSON | 重命名命名空间 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/storage/kv/namespaces/{namespace_id}/bulk | PUT | JSON | JSON | Write 多个键值对 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/storage/kv/namespaces/{namespace_id}/bulk/delete | POST | JSON | JSON | 删除多个键值对 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/storage/kv/namespaces/{namespace_id}/bulk/get | POST | JSON | JSON | 获取多个键值对 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/storage/kv/namespaces/{namespace_id}/keys | GET | Query | JSON | 列出密钥内命名空间 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/storage/kv/namespaces/{namespace_id}/metadata/{key_name} | GET | Query | JSON | 获取 key's 元数据 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/storage/kv/namespaces/{namespace_id}/values/{key_name} | DELETE | Query | JSON | 删除键值 pair |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/storage/kv/namespaces/{namespace_id}/values/{key_name} | GET | Query | JSON | 获取 key's 值 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/storage/kv/namespaces/{namespace_id}/values/{key_name} | PUT | JSON | JSON | Write 键值 pair 含 optional 元数据 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vectorize/v2/indexes | GET | Query | JSON | 列出 Vectorize 索引 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vectorize/v2/indexes | POST | JSON | JSON | 创建 Vectorize 索引 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vectorize/v2/indexes/{index_name} | DELETE | Query | JSON | 删除 Vectorize 索引 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vectorize/v2/indexes/{index_name} | GET | Query | JSON | 获取 Vectorize 索引 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vectorize/v2/indexes/{index_name}/delete_by_ids | POST | JSON | JSON | 删除向量按标识符 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vectorize/v2/indexes/{index_name}/get_by_ids | POST | JSON | JSON | 获取向量按标识符 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vectorize/v2/indexes/{index_name}/info | GET | Query | JSON | 获取 Vectorize 索引信息 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vectorize/v2/indexes/{index_name}/insert | POST | JSON | JSON | Insert 向量 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vectorize/v2/indexes/{index_name}/list | GET | Query | JSON | 列出向量 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vectorize/v2/indexes/{index_name}/metadata_index/create | POST | JSON | JSON | 创建元数据索引 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vectorize/v2/indexes/{index_name}/metadata_index/delete | POST | JSON | JSON | 删除元数据索引 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vectorize/v2/indexes/{index_name}/metadata_index/list | GET | Query | JSON | 列出元数据索引 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vectorize/v2/indexes/{index_name}/query | POST | JSON | JSON | 查询向量 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/vectorize/v2/indexes/{index_name}/upsert | POST | JSON | JSON | 插入或更新向量 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/durable_objects/namespaces | GET | Query | JSON | 列出持久对象命名空间 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/workers/durable_objects/namespaces/{id}/objects | GET | Query | JSON | 列出对象内持久对象命名空间 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/speed_api/availabilities | GET | Query | JSON | 获取配额与可用性 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/speed_api/pages | GET | Query | JSON | 列出 tested webpages |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/speed_api/pages/{url}/tests | DELETE | Query | JSON | 删除全部寻呼测试 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/speed_api/pages/{url}/tests | GET | Query | JSON | 列出寻呼测试历史 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/speed_api/pages/{url}/tests | POST | JSON | JSON | 启动寻呼测试 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/speed_api/pages/{url}/tests/{test_id} | GET | Query | JSON | 获取寻呼测试结果 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/speed_api/pages/{url}/trend | GET | Query | JSON | 列出 core web vital 指标趋势 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/speed_api/schedule/{url} | DELETE | Query | JSON | 删除 scheduled 寻呼测试 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/speed_api/schedule/{url} | GET | Query | JSON | 获取寻呼测试计划 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/speed_api/schedule/{url} | POST | JSON | JSON | 创建 scheduled 寻呼测试 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/web3/hostnames | GET | Query | JSON | 列出 Web3 主机名 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/web3/hostnames | POST | JSON | JSON | 创建 Web3 主机名 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/web3/hostnames/{identifier} | DELETE | Query | JSON | 删除 Web3 主机名 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/web3/hostnames/{identifier} | GET | Query | JSON | Web3 主机名详情 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/web3/hostnames/{identifier} | PATCH | JSON | JSON | 编辑 Web3 主机名 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/web3/hostnames/{identifier}/ipfs_universal_path/content_list | GET | Query | JSON | IPFS 通用路径网关内容列出详情 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/web3/hostnames/{identifier}/ipfs_universal_path/content_list | PUT | JSON | JSON | 更新 IPFS 通用路径网关内容列出 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/web3/hostnames/{identifier}/ipfs_universal_path/content_list/entries | GET | Query | JSON | 列出 IPFS 通用路径网关内容列出条目 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/web3/hostnames/{identifier}/ipfs_universal_path/content_list/entries | POST | JSON | JSON | 创建 IPFS 通用路径网关内容列出条目 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/web3/hostnames/{identifier}/ipfs_universal_path/content_list/entries/{content_list_entry_identifier} | DELETE | Query | JSON | 删除 IPFS 通用路径网关内容列出条目 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/web3/hostnames/{identifier}/ipfs_universal_path/content_list/entries/{content_list_entry_identifier} | GET | Query | JSON | IPFS 通用路径网关内容列出条目详情 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/web3/hostnames/{identifier}/ipfs_universal_path/content_list/entries/{content_list_entry_identifier} | PUT | JSON | JSON | 编辑 IPFS 通用路径网关内容列出条目 |

## 账户与组织管理（141 条）

> 账户、组织、成员、用户、计费与订阅、资源标签、资源共享、租户、旗舰计划。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.cloudflare.com/client/v4/accounts | GET | Query | JSON | 列出账户 |
| https://api.cloudflare.com/client/v4/accounts | POST | JSON | JSON | 创建账户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id} | DELETE | Query | JSON | 删除特定账户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id} | GET | Query | JSON | 账户详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id} | PUT | JSON | JSON | 更新账户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/billable-usage | GET | Query | JSON | 获取账户 Billable 用量 (版本 1, Alpha) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/billable-usage/info | GET | Query | JSON | 获取账户 Billable 用量信息 (版本 1, Alpha) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/billable/usage | GET | Query | JSON | 获取账户用量 (版本 2, Alpha, 受限) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/billing/bad-debt | GET | Query | JSON | 获取账户不良坏账 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/billing/credits | GET | Query | JSON | 获取账户额度 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/billing/history | GET | Query | JSON | 获取账户计费历史 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/billing/profile | DELETE | Query | JSON | 删除计费配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/billing/profile | GET | Query | JSON | 获取计费配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/billing/profile | PATCH | JSON | JSON | 更新计费邮箱 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/billing/profile | POST | JSON | JSON | 创建计费配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/billing/profile | PUT | JSON | JSON | 更新计费配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/billing/profile/payment-method | POST | JSON | JSON | 创建支付意图用于计费配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/billing/unpaid-invoice | GET | Query | JSON | 获取 Unpaid 发票 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/bulk/subscriptions | POST | JSON | JSON | 创建订阅 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/client-secret | POST | JSON | JSON | 创建设置意图 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/entitlements | GET | Query | JSON | 获取账户权益 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/flagship/apps | GET | Query | JSON | 列出应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/flagship/apps | POST | JSON | JSON | 创建应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/flagship/apps/{app_id} | DELETE | Query | JSON | 删除应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/flagship/apps/{app_id} | GET | Query | JSON | 获取应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/flagship/apps/{app_id} | PUT | JSON | JSON | 更新应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/flagship/apps/{app_id}/evaluate | GET | Query | JSON | Evaluate 标志来自查询上下文 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/flagship/apps/{app_id}/flags | GET | Query | JSON | 列出标志 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/flagship/apps/{app_id}/flags | POST | JSON | JSON | 创建标志 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/flagship/apps/{app_id}/flags/{flag_key} | DELETE | Query | JSON | 删除标志 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/flagship/apps/{app_id}/flags/{flag_key} | GET | Query | JSON | 获取标志 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/flagship/apps/{app_id}/flags/{flag_key} | PUT | JSON | JSON | 更新标志 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/flagship/apps/{app_id}/flags/{flag_key}/changelog | GET | Query | JSON | 列出标志变更日志条目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/invoices | PATCH | JSON | JSON | 切换 PDF 发票 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/logs/audit | GET | Query | JSON | 获取账户审计日志 (版本 2) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/logs/audit/product_categories | GET | Query | JSON | 列出账户审计日志产品类别 (版本 2) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/logs/audit/{id}/history | GET | Query | JSON | 获取资源变更历史来自账户审计日志条目 (版本 2) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/members | GET | Query | JSON | 列出成员 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/members | POST | JSON | JSON | 添加成员 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/members/{member_id} | DELETE | Query | JSON | 移除成员 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/members/{member_id} | GET | Query | JSON | 成员详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/members/{member_id} | PUT | JSON | JSON | 更新成员 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/move | POST | JSON | JSON | 移动账户给组织 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pay-bad-debt | POST | JSON | JSON | 支付不良坏账 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/pay-invoice | POST | JSON | JSON | 支付发票 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/payment-methods | GET | Query | JSON | 列出支付方法 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/payment-methods | POST | JSON | JSON | 创建支付方法 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/payment-methods/{payment_method_id} | DELETE | Query | JSON | 删除支付方法 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/payment-methods/{payment_method_id} | GET | Query | JSON | 获取支付方法 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/payment-methods/{payment_method_id} | PUT | JSON | JSON | 更新支付方法 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/payment-methods/{payment_method_id}/set-as-default | POST | JSON | JSON | 设置默认支付方法 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/profile | GET | Query | JSON | 获取账户配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/profile | PUT | JSON | JSON | 更新账户配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/receipts/{receipt_id}/pdf | GET | Query | JSON | 获取回执 PDF |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/settings/transformations | GET | Query | JSON | 列出图片缩放配置用于账户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/shares | GET | Query | JSON | 列出账户 shares |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/shares | POST | JSON | JSON | 触发共享创建 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/shares/{share_id} | DELETE | Query | JSON | 触发共享删除 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/shares/{share_id} | GET | Query | JSON | 获取账户共享按 ID |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/shares/{share_id} | PUT | JSON | JSON | 触发共享重命名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/shares/{share_id}/recipients | GET | Query | JSON | 列出共享收件人按共享 ID |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/shares/{share_id}/recipients | POST | JSON | JSON | 触发收件人 addition 给共享 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/shares/{share_id}/recipients/{recipient_id} | DELETE | Query | JSON | 触发收件人 removal 来自共享 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/shares/{share_id}/recipients/{recipient_id} | GET | Query | JSON | 获取共享收件人按 ID |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/shares/{share_id}/resources | GET | Query | JSON | 列出共享资源按共享 ID |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/shares/{share_id}/resources | POST | JSON | JSON | 触发资源 addition 给共享 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/shares/{share_id}/resources/{share_resource_id} | DELETE | Query | JSON | 触发资源删除来自共享 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/shares/{share_id}/resources/{share_resource_id} | GET | Query | JSON | 获取共享资源按 ID |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/shares/{share_id}/resources/{share_resource_id} | PUT | JSON | JSON | 触发资源元数据更新内共享 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/subscriptions/cancel-downgrade | POST | JSON | JSON | 取消延迟降级 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/subscriptions/{subscription_identifier} | DELETE | Query | JSON | 删除订阅 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/subscriptions/{subscription_identifier} | GET | Query | JSON | 获取订阅 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/subscriptions/{subscription_identifier} | PUT | JSON | JSON | 更新订阅 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/subscriptions/{subscription_identifier}/action/append | POST | JSON | JSON | 追加订阅动作 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/subscriptions/{subscription_identifier}/cancel-reason | GET | Query | JSON | 获取取消原因 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/subscriptions/{subscription_identifier}/cancel-reason | POST | JSON | JSON | 创建取消原因 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/tags | DELETE | Query | JSON | 删除标签来自账户级资源 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/tags | GET | Query | JSON | 获取标签用于账户级资源 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/tags | PUT | JSON | JSON | 设置标签用于账户级资源 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/tags/keys | GET | Query | JSON | 列出标签密钥 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/tags/resources | GET | Query | JSON | 列出 tagged 资源 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/tags/summary | GET | Query | JSON | 列出标签键摘要 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/tags/values/{tag_key} | GET | Query | JSON | 列出标签值 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/tokens | GET | Query | JSON | 列出令牌 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/tokens | POST | JSON | JSON | 创建令牌 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/tokens/permission_groups | GET | Query | JSON | 列出权限组 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/tokens/verify | GET | Query | JSON | 校验令牌 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/tokens/{token_id} | DELETE | Query | JSON | 删除令牌 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/tokens/{token_id} | GET | Query | JSON | 令牌详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/tokens/{token_id} | PUT | JSON | JSON | 更新令牌 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/tokens/{token_id}/value | PUT | JSON | JSON | 轮换令牌 |
| https://api.cloudflare.com/client/v4/billing/address-validation | POST | JSON | JSON | 校验计费地址 |
| https://api.cloudflare.com/client/v4/billing/rate_plans/{public_key} | GET | Query | JSON | 获取速率套餐按公开键 |
| https://api.cloudflare.com/client/v4/memberships | GET | Query | JSON | 列出成员资格 |
| https://api.cloudflare.com/client/v4/memberships/{membership_id} | DELETE | Query | JSON | 删除成员资格 |
| https://api.cloudflare.com/client/v4/memberships/{membership_id} | GET | Query | JSON | 成员资格详情 |
| https://api.cloudflare.com/client/v4/memberships/{membership_id} | PUT | JSON | JSON | 更新成员资格 |
| https://api.cloudflare.com/client/v4/organizations | GET | Query | JSON | 列出组织用户访问给 |
| https://api.cloudflare.com/client/v4/organizations | POST | JSON | JSON | 创建组织 |
| https://api.cloudflare.com/client/v4/organizations/{organization_id} | DELETE | Query | JSON | 删除组织 |
| https://api.cloudflare.com/client/v4/organizations/{organization_id} | GET | Query | JSON | 获取组织 |
| https://api.cloudflare.com/client/v4/organizations/{organization_id} | PUT | JSON | JSON | 更新组织 |
| https://api.cloudflare.com/client/v4/organizations/{organization_id}/accounts | GET | Query | JSON | 列出组织账户 |
| https://api.cloudflare.com/client/v4/organizations/{organization_id}/billable/usage | GET | Query | JSON | 获取组织用量 (版本 2, Alpha, 受限) |
| https://api.cloudflare.com/client/v4/organizations/{organization_id}/logs/audit | GET | Query | JSON | 获取组织审计日志 (版本 2) |
| https://api.cloudflare.com/client/v4/organizations/{organization_id}/logs/audit/{id}/history | GET | Query | JSON | 获取资源变更历史来自组织审计日志条目 (版本 2) |
| https://api.cloudflare.com/client/v4/organizations/{organization_id}/members | GET | Query | JSON | 列出组织成员 |
| https://api.cloudflare.com/client/v4/organizations/{organization_id}/members | POST | JSON | JSON | 创建组织成员 |
| https://api.cloudflare.com/client/v4/organizations/{organization_id}/members/{member_id} | DELETE | Query | JSON | 删除组织成员 |
| https://api.cloudflare.com/client/v4/organizations/{organization_id}/members/{member_id} | GET | Query | JSON | 获取组织成员 |
| https://api.cloudflare.com/client/v4/organizations/{organization_id}/profile | GET | Query | JSON | 获取组织配置文件 |
| https://api.cloudflare.com/client/v4/organizations/{organization_id}/profile | PUT | JSON | JSON | 修改组织配置文件. |
| https://api.cloudflare.com/client/v4/tenants/{tenant_id} | GET | Query | JSON | 获取租户详情 |
| https://api.cloudflare.com/client/v4/tenants/{tenant_id}/account_types | GET | Query | JSON | 列出租户账户类型 |
| https://api.cloudflare.com/client/v4/tenants/{tenant_id}/accounts | GET | Query | JSON | 列出租户账户 |
| https://api.cloudflare.com/client/v4/tenants/{tenant_id}/entitlements | GET | Query | JSON | 列出租户权益 |
| https://api.cloudflare.com/client/v4/tenants/{tenant_id}/memberships | GET | Query | JSON | 列出租户成员资格 |
| https://api.cloudflare.com/client/v4/user | GET | Query | JSON | 用户详情 |
| https://api.cloudflare.com/client/v4/user | PATCH | JSON | JSON | 编辑用户 |
| https://api.cloudflare.com/client/v4/user/audit_logs | GET | Query | JSON | 获取用户审计日志 |
| https://api.cloudflare.com/client/v4/user/invites | GET | Query | JSON | 列出邀请 |
| https://api.cloudflare.com/client/v4/user/invites/{invite_id} | GET | Query | JSON | 邀请详情 |
| https://api.cloudflare.com/client/v4/user/invites/{invite_id} | PATCH | JSON | JSON | 响应给邀请 |
| https://api.cloudflare.com/client/v4/user/spectrum_analytics/zones/report | GET | Query | JSON | 获取区域带宽报告 |
| https://api.cloudflare.com/client/v4/user/subscriptions | GET | Query | JSON | 获取用户订阅 |
| https://api.cloudflare.com/client/v4/user/subscriptions/{identifier} | DELETE | Query | JSON | 删除用户订阅 |
| https://api.cloudflare.com/client/v4/user/subscriptions/{identifier} | PUT | JSON | JSON | 更新用户订阅 |
| https://api.cloudflare.com/client/v4/user/tenants | GET | Query | JSON | 列出用户租户 |
| https://api.cloudflare.com/client/v4/user/tokens | GET | Query | JSON | 列出令牌 |
| https://api.cloudflare.com/client/v4/user/tokens | POST | JSON | JSON | 创建令牌 |
| https://api.cloudflare.com/client/v4/user/tokens/permission_groups | GET | Query | JSON | 列出令牌权限组 |
| https://api.cloudflare.com/client/v4/user/tokens/verify | GET | Query | JSON | 校验令牌 |
| https://api.cloudflare.com/client/v4/user/tokens/{token_id} | DELETE | Query | JSON | 删除令牌 |
| https://api.cloudflare.com/client/v4/user/tokens/{token_id} | GET | Query | JSON | 令牌详情 |
| https://api.cloudflare.com/client/v4/user/tokens/{token_id} | PUT | JSON | JSON | 更新令牌 |
| https://api.cloudflare.com/client/v4/user/tokens/{token_id}/value | PUT | JSON | JSON | 轮换令牌 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/tags | DELETE | Query | JSON | 删除标签来自区域级资源 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/tags | GET | Query | JSON | 获取标签用于区域级资源 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/tags | PUT | JSON | JSON | 设置标签用于区域级资源 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/subscriptions | GET | Query | JSON | 列出订阅 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/subscriptions | POST | JSON | JSON | 创建订阅 |

## 媒体与渲染（137 条）

> Stream 视频托管、Images 图片处理、Browser Rendering 浏览器渲染、RealtimeKit 实时音视频。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/browser-rendering/accessibilityTree | POST | JSON | JSON | 获取 accessibility 树寻呼 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/browser-rendering/content | POST | JSON | JSON | 获取 HTML 内容. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/browser-rendering/crawl | POST | JSON | JSON | 爬取 websites. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/browser-rendering/crawl/{job_id} | DELETE | Query | JSON | 取消爬取任务. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/browser-rendering/crawl/{job_id} | GET | Query | JSON | 获取爬取结果. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/browser-rendering/devtools/browser | POST | JSON | JSON | 获取浏览器会话 ID. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/browser-rendering/devtools/browser/{session_id} | DELETE | Query | JSON | 关闭浏览器会话. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/browser-rendering/devtools/browser/{session_id}/json/activate/{target_id} | GET | Query | JSON | 激活浏览器目标. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/browser-rendering/devtools/browser/{session_id}/json/close/{target_id} | GET | Query | JSON | 关闭浏览器目标. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/browser-rendering/devtools/browser/{session_id}/json/list | GET | Query | JSON | 列出目标. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/browser-rendering/devtools/browser/{session_id}/json/list/{target_id} | GET | Query | JSON | 获取目标按 ID. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/browser-rendering/devtools/browser/{session_id}/json/new | PUT | JSON | JSON | Open 新浏览器 tab. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/browser-rendering/devtools/browser/{session_id}/json/protocol | GET | Query | JSON | 获取 Chrome DevTools 协议模式. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/browser-rendering/devtools/browser/{session_id}/json/version | GET | Query | JSON | 获取浏览器版本元数据. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/browser-rendering/devtools/browser/{session_id}/live_view | POST | JSON | JSON | Mint 实时查看 URL 用于浏览器会话 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/browser-rendering/devtools/session | GET | Query | JSON | 列出会话. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/browser-rendering/devtools/session/{session_id} | GET | Query | JSON | 获取会话详情. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/browser-rendering/json | POST | JSON | JSON | 获取 json. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/browser-rendering/links | POST | JSON | JSON | 获取 Links. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/browser-rendering/markdown | POST | JSON | JSON | 获取 markdown. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/browser-rendering/pdf | POST | JSON | JSON | 获取 PDF. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/browser-rendering/scrape | POST | JSON | JSON | Scrape 元素. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/browser-rendering/screenshot | POST | JSON | JSON | 获取截图. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/browser-rendering/snapshot | POST | JSON | JSON | 获取 HTML 内容与截图. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/images/v1 | POST | JSON | JSON | 上传镜像 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/images/v1/keys | GET | Query | JSON | 列出签名密钥 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/images/v1/keys/{signing_key_name} | DELETE | Query | JSON | 删除签名键 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/images/v1/keys/{signing_key_name} | PUT | JSON | JSON | 创建新签名键 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/images/v1/stats | GET | Query | JSON | 图片用量统计 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/images/v1/variants | GET | Query | JSON | 列出变体 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/images/v1/variants | POST | JSON | JSON | 创建变体 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/images/v1/variants/{variant_id} | DELETE | Query | JSON | 删除变体 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/images/v1/variants/{variant_id} | GET | Query | JSON | 变体详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/images/v1/variants/{variant_id} | PATCH | JSON | JSON | 更新变体 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/images/v1/{image_id} | DELETE | Query | JSON | 删除镜像 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/images/v1/{image_id} | GET | Query | JSON | 镜像详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/images/v1/{image_id} | PATCH | JSON | JSON | 更新镜像 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/images/v1/{image_id}/blob | GET | Query | JSON | 下载镜像 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/images/v2 | GET | Query | JSON | 列出图片 V2 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/images/v2/direct_upload | POST | JSON | JSON | 创建已认证 direct 上传 URL V2 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/apps | GET | Query | JSON | 获取全部应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/apps | POST | JSON | JSON | 创建应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/analytics/daywise | GET | Query | JSON | 获取 day-wise 会话与录制分析数据用于应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/analytics/livestreams/daywise | GET | Query | JSON | 获取 day-wise 分析数据用于你的直播 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/analytics/livestreams/overall | GET | Query | JSON | 获取 complete 分析数据用于你的直播 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/livestreams | GET | Query | JSON | 获取全部直播 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/livestreams/sessions/{livestream-session-id} | GET | Query | JSON | 获取直播会话详情使用直播会话 ID |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/livestreams/{livestream_id} | GET | Query | JSON | 获取直播详情使用直播 ID |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/livestreams/{livestream_id}/active-livestream-session | GET | Query | JSON | 获取活跃直播会话详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/meetings | GET | Query | JSON | 获取全部会议用于应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/meetings | POST | JSON | JSON | 创建会议 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/meetings/{meeting_id} | GET | Query | JSON | 获取会议用于应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/meetings/{meeting_id} | PATCH | JSON | JSON | 更新会议 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/meetings/{meeting_id} | PUT | JSON | JSON | 替换会议 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/meetings/{meeting_id}/active-livestream | GET | Query | JSON | 获取活跃直播用于会议 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/meetings/{meeting_id}/active-livestream/stop | POST | JSON | JSON | 停止 livestreaming 会议 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/meetings/{meeting_id}/active-session | GET | Query | JSON | 获取详情的活跃会话 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/meetings/{meeting_id}/active-session/kick | POST | JSON | JSON | Kick 参与者来自活跃会话 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/meetings/{meeting_id}/active-session/kick-all | POST | JSON | JSON | Kick 全部参与者 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/meetings/{meeting_id}/active-session/poll | POST | JSON | JSON | 创建轮询 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/meetings/{meeting_id}/livestreams | POST | JSON | JSON | 启动 livestreaming 会议 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/meetings/{meeting_id}/participants | GET | Query | JSON | 获取全部参与者的会议 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/meetings/{meeting_id}/participants | POST | JSON | JSON | 添加参与者 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/meetings/{meeting_id}/participants/{participant_id} | DELETE | Query | JSON | 删除参与者 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/meetings/{meeting_id}/participants/{participant_id} | GET | Query | JSON | 获取参与者详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/meetings/{meeting_id}/participants/{participant_id} | PATCH | JSON | JSON | 编辑参与者详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/meetings/{meeting_id}/participants/{participant_id}/token | POST | JSON | JSON | 刷新参与者认证令牌 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/presets | GET | Query | JSON | 获取全部预设 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/presets | POST | JSON | JSON | 创建预设 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/presets/{preset_id} | DELETE | Query | JSON | 删除预设 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/presets/{preset_id} | GET | Query | JSON | 获取详情的预设 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/presets/{preset_id} | PATCH | JSON | JSON | 更新预设 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/presets/{preset_id} | PUT | JSON | JSON | 替换预设 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/recordings | GET | Query | JSON | 获取全部 recordings 用于应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/recordings | POST | JSON | JSON | 启动录制会议 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/recordings/active-recording/{meeting_id} | GET | Query | JSON | 获取活跃录制 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/recordings/track | POST | JSON | JSON | 启动录制参与者音频追踪 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/recordings/{recording_id} | GET | Query | JSON | 获取详情的录制 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/recordings/{recording_id} | PUT | JSON | JSON | 暂停/恢复/停止录制 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/sessions | GET | Query | JSON | 获取全部会话的应用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/sessions/peer-report/{peer_id} | GET | Query | JSON | 获取详情的对等体 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/sessions/{session_id} | GET | Query | JSON | 获取详情的会话 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/sessions/{session_id}/chat | GET | Query | JSON | 获取全部聊天消息的会话 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/sessions/{session_id}/participants | GET | Query | JSON | 获取参与者列出的会话 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/sessions/{session_id}/participants/{participant_id} | GET | Query | JSON | 获取详情的参与者 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/sessions/{session_id}/summary | GET | Query | JSON | 获取摘要的 transcripts 用于会话 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/sessions/{session_id}/summary | POST | JSON | JSON | 生成摘要的 Transcripts 用于会话 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/sessions/{session_id}/transcript | GET | Query | JSON | 获取 complete transcript 用于会话 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/webhooks | GET | Query | JSON | 获取全部 webhooks 详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/webhooks | POST | JSON | JSON | 添加 webhook |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/webhooks/{webhook_id} | DELETE | Query | JSON | 删除 webhook |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/webhooks/{webhook_id} | GET | Query | JSON | 获取详情的 webhook |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/webhooks/{webhook_id} | PATCH | JSON | JSON | 编辑 webhook |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/realtime/kit/{app_id}/webhooks/{webhook_id} | PUT | JSON | JSON | 替换 webhook |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream | GET | Query | JSON | 列出视频 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream | POST | JSON | JSON | Initiate 视频上传使用 TUS |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/clip | POST | JSON | JSON | Clip 视频指定启动与 end 时间 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/copy | POST | JSON | JSON | 上传视频来自 URL |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/direct_upload | POST | JSON | JSON | 上传视频通过 direct 上传 URL |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/keys | GET | Query | JSON | 列出签名密钥 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/keys | POST | JSON | JSON | 创建签名密钥 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/keys/{identifier} | DELETE | Query | JSON | 删除签名密钥 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/live_inputs | GET | Query | JSON | 列出实时输入 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/live_inputs | POST | JSON | JSON | 创建实时输入 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/live_inputs/{live_input_identifier} | DELETE | Query | JSON | 删除实时输入 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/live_inputs/{live_input_identifier} | GET | Query | JSON | 获取实时输入 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/live_inputs/{live_input_identifier} | PUT | JSON | JSON | 更新实时输入 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/live_inputs/{live_input_identifier}/outputs | GET | Query | JSON | 列出全部输出关联含指定实时输入 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/live_inputs/{live_input_identifier}/outputs | POST | JSON | JSON | 创建新输出, connected 给实时输入 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/live_inputs/{live_input_identifier}/outputs/{output_identifier} | DELETE | Query | JSON | 删除输出 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/live_inputs/{live_input_identifier}/outputs/{output_identifier} | PUT | JSON | JSON | 更新输出 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/storage-usage | GET | Query | JSON | 存储使用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/watermarks | GET | Query | JSON | 列出水印配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/watermarks | POST | JSON | JSON | 创建水印配置文件通过基础上传 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/watermarks/{identifier} | DELETE | Query | JSON | 删除水印配置文件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/watermarks/{identifier} | GET | Query | JSON | 水印配置文件详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/webhook | DELETE | Query | JSON | 删除 webhooks |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/webhook | GET | Query | JSON | 查看 webhook |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/webhook | PUT | JSON | JSON | 创建 VOD webhooks |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/{identifier} | DELETE | Query | JSON | 删除视频 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/{identifier} | GET | Query | JSON | 获取视频详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/{identifier} | POST | JSON | JSON | 编辑视频详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/{identifier}/audio | GET | Query | JSON | 列出附加音频追踪上视频 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/{identifier}/audio/copy | POST | JSON | JSON | 添加音频追踪给视频 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/{identifier}/audio/{audio_identifier} | DELETE | Query | JSON | 删除附加音频追踪上视频 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/{identifier}/audio/{audio_identifier} | PATCH | JSON | JSON | 编辑附加音频追踪上视频 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/{identifier}/captions | GET | Query | JSON | 列出字幕或字幕 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/{identifier}/captions/{language} | DELETE | Query | JSON | 删除字幕或字幕 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/{identifier}/captions/{language} | GET | Query | JSON | 列出字幕或字幕用于已提供语言 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/{identifier}/captions/{language} | PUT | JSON | JSON | 上传字幕或字幕 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/{identifier}/captions/{language}/generate | POST | JSON | JSON | 生成字幕或字幕用于已提供语言通过 AI |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/{identifier}/captions/{language}/vtt | GET | Query | JSON | Return WebVTT 字幕用于已提供语言 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/{identifier}/downloads | DELETE | Query | JSON | 删除下载 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/{identifier}/downloads | GET | Query | JSON | 列出下载 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/{identifier}/downloads | POST | JSON | JSON | 创建下载 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/{identifier}/embed | GET | Query | JSON | 已废弃: 获取旧版 embed 代码 HTML |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/stream/{identifier}/token | POST | JSON | JSON | 创建签名 URL 令牌用于视频 |

## Cloudforce One 威胁情报（129 条）

> Cloudforce One 安全研究与威胁情报：漏洞情报、僵尸网络信息流、攻击指标（IOC）等。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/binary | POST | JSON | JSON | Posts 文件给二进制存储 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/binary/{hash} | GET | Query | JSON | 获取文件来自二进制存储 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events | GET | Query | JSON | 过滤与列出事件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/aggregate | GET | Query | JSON | 聚合事件按单个或多个列含 optional 日期过滤 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/attackers | GET | Query | JSON | 列出 attackers 跨越多个数据集 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/categories | GET | Query | JSON | 列出类别跨越多个数据集 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/categories/catalog | GET | Query | JSON | 列出类别 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/categories/create | POST | JSON | JSON | 创建新类别 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/countries | GET | Query | JSON | 获取国家信息用于全部国家 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/create | POST | JSON | JSON | 创建新事件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/create/bulk | POST | JSON | JSON | 创建批量事件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/dataset | GET | Query | JSON | 列出全部数据集内账户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/dataset/create | POST | JSON | JSON | 创建数据集 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/dataset/{dataset_id} | DELETE | Query | JSON | 删除数据集 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/dataset/{dataset_id} | GET | Query | JSON | 读取数据集 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/dataset/{dataset_id} | PATCH | JSON | JSON | 更新现有数据集 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/dataset/{dataset_id}/events/{event_id} | GET | Query | JSON | 读取事件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/dataset/{dataset_id}/indicators/tags | GET | Query | JSON | 列出 mirrored 标签用于指标数据集 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/dataset/{dataset_id}/indicators/{indicator_id} | GET | Query | JSON | 读取指标 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/dataset/{dataset_id}/targetIndustries | GET | Query | JSON | 列出全部目标行业用于特定数据集 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/event_tag/{event_id} | DELETE | Query | JSON | 移除标签来自事件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/event_tag/{event_id}/create | POST | JSON | JSON | 添加标签给事件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/graph | GET | Query | JSON | 查询 graph neighborhood 来自 R2 数据目录 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/graphql | POST | JSON | JSON | GraphQL 端点用于事件聚合 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/indicator-types | GET | Query | JSON | 列出指标类型跨越多个数据集 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/indicators | GET | Query | JSON | 列出指标跨越多个数据集 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/indicators/aggregate | GET | Query | JSON | 聚合指标按列(s) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/queries | GET | Query | JSON | 列出全部已保存事件查询 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/queries/create | POST | JSON | JSON | 创建已保存事件查询 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/queries/{query_id} | DELETE | Query | JSON | 删除已保存事件查询 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/queries/{query_id} | GET | Query | JSON | 读取已保存事件查询 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/queries/{query_id} | PATCH | JSON | JSON | 更新已保存事件查询 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/relate/{event_id} | DELETE | Query | JSON | 移除事件引用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/tags | GET | Query | JSON | 列出全部标签 (SoT) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/tags/categories | GET | Query | JSON | 列出全部标签类别 (SoT) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/tags/categories/create | POST | JSON | JSON | 创建新标签类别 (SoT) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/tags/create | POST | JSON | JSON | 创建新标签 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/tags/{tag_uuid} | DELETE | Query | JSON | 删除标签 (SoT) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/tags/{tag_uuid} | PATCH | JSON | JSON | 更新标签 (SoT) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/tags/{tag_uuid}/indicators | GET | Query | JSON | 列出指标相关给标签 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/targetIndustries | GET | Query | JSON | 列出目标行业跨越多个数据集 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/targetIndustries/catalog | GET | Query | JSON | 列出全部目标行业来自行业映射目录 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/{event_id} | PATCH | JSON | JSON | 更新事件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/{event_id}/raw/{raw_id} | GET | Query | JSON | 读取数据用于原始事件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/events/{event_id}/raw/{raw_id} | PATCH | JSON | JSON | 更新原始事件 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/requests | POST | JSON | JSON | 列出请求 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/requests/constants | GET | Query | JSON | 获取请求优先级, 状态, 与 TLP constants |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/requests/new | POST | JSON | JSON | 创建新请求. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/requests/priority/new | POST | JSON | JSON | 创建新优先级情报要求 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/requests/priority/quota | GET | Query | JSON | 获取优先级情报要求配额 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/requests/priority/{priority_id} | DELETE | Query | JSON | 删除优先级情报要求 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/requests/priority/{priority_id} | GET | Query | JSON | 获取优先级情报要求 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/requests/priority/{priority_id} | PUT | JSON | JSON | 更新优先级情报要求 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/requests/quota | GET | Query | JSON | 获取请求配额 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/requests/types | GET | Query | JSON | 获取请求类型 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/requests/{request_id} | DELETE | Query | JSON | 删除请求 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/requests/{request_id} | GET | Query | JSON | 获取请求 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/requests/{request_id} | PUT | JSON | JSON | 更新请求 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/requests/{request_id}/asset | POST | JSON | JSON | 列出请求资产 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/requests/{request_id}/asset/{asset_id} | DELETE | Query | JSON | 删除请求资产 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/requests/{request_id}/asset/{asset_id} | GET | Query | JSON | 获取请求资产 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/requests/{request_id}/asset/{asset_id} | PUT | JSON | JSON | 更新请求资产 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/requests/{request_id}/message | POST | JSON | JSON | 列出请求消息 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/requests/{request_id}/message/new | POST | JSON | JSON | 创建新请求消息 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/requests/{request_id}/message/{message_id} | DELETE | Query | JSON | 删除请求消息 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/requests/{request_id}/message/{message_id} | PUT | JSON | JSON | 更新请求消息 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/scans/config | GET | Query | JSON | 列出扫描配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/scans/config | POST | JSON | JSON | 创建新扫描配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/scans/config/{config_id} | DELETE | Query | JSON | 删除扫描配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/scans/config/{config_id} | PATCH | JSON | JSON | 更新现有扫描配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/scans/results/{config_id} | GET | Query | JSON | 获取最新扫描结果 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/articles | GET | Query | JSON | 列出威胁信号文章 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/articles | PATCH | JSON | JSON | 批量更新威胁信号文章读取状态 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/articles/{article_id} | GET | Query | JSON | 获取威胁信号文章 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/articles/{article_id} | PATCH | JSON | JSON | 更新威胁信号文章读取状态 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/articles/{article_id}/content | GET | Query | JSON | 获取威胁信号文章内容 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/articles/{article_id}/skills/{skill_id}/output | GET | Query | JSON | 获取威胁信号文章技能输出 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/articles/{article_id}/tag | POST | JSON | JSON | 生成威胁信号文章 AI 标签 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/articles/{article_id}/tags | POST | JSON | JSON | 添加标签给威胁信号文章 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/articles/{article_id}/tags/{tag_id} | DELETE | Query | JSON | 移除标签来自威胁信号文章 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/categories | GET | Query | JSON | 列出威胁信号信息流类别 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/feeds | GET | Query | JSON | 列出威胁信号信息流 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/feeds | POST | JSON | JSON | 创建威胁信号信息流 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/feeds/poll | POST | JSON | JSON | 触发威胁信号信息流轮询 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/feeds/{feed_id} | DELETE | Query | JSON | 删除威胁信号信息流 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/feeds/{feed_id} | PATCH | JSON | JSON | 更新威胁信号信息流 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/feeds/{feed_id}/raw | GET | Query | JSON | 获取威胁信号信息流 XML |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/feeds/{feed_id}/skills | GET | Query | JSON | 获取威胁信号信息流技能 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/feeds/{feed_id}/skills | PUT | JSON | JSON | 设置威胁信号信息流技能 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/indicators | GET | Query | JSON | 列出威胁信号文章指标 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/search | GET | Query | JSON | 搜索威胁信号文章使用 AI 搜索 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/skills | GET | Query | JSON | 列出威胁信号技能 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/skills | POST | JSON | JSON | 创建威胁信号技能 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/skills/{skill_id} | DELETE | Query | JSON | 删除威胁信号技能 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/skills/{skill_id} | GET | Query | JSON | 获取威胁信号技能 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/skills/{skill_id} | PATCH | JSON | JSON | 更新威胁信号技能 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/skills/{skill_id}/tag-categories | GET | Query | JSON | 获取威胁信号技能标签类别 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/cloudforce-one/v2/threat-signals/skills/{skill_id}/tag-categories | PUT | JSON | JSON | 替换威胁信号技能标签类别 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/intel/asn/{asn} | GET | Query | JSON | 获取 ASN Overview. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/intel/asn/{asn}/subnets | GET | Query | JSON | 获取 ASN 子网 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/intel/attack-surface-report/issue-types | GET | Query | JSON | 获取安全中心议题类型 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/intel/dns | GET | Query | JSON | 获取 Passive DNS 按 IP |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/intel/domain | GET | Query | JSON | 获取域名详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/intel/domain-history | GET | Query | JSON | 获取域名历史 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/intel/domain/bulk | GET | Query | JSON | 获取多个域名详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/intel/indicator-feeds | GET | Query | JSON | 获取指标信息流所属按 this 账户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/intel/indicator-feeds | POST | JSON | JSON | 创建新指标信息流 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/intel/indicator-feeds/permissions/add | PUT | JSON | JSON | 授予权限给指标信息流 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/intel/indicator-feeds/permissions/remove | PUT | JSON | JSON | 撤销权限给指标信息流 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/intel/indicator-feeds/permissions/view | GET | Query | JSON | 列出指标信息流权限 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/intel/indicator-feeds/{feed_id} | GET | Query | JSON | 获取指标信息流元数据 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/intel/indicator-feeds/{feed_id} | PUT | JSON | JSON | 更新指标信息流元数据 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/intel/indicator-feeds/{feed_id}/data | GET | Query | JSON | 获取指标信息流数据 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/intel/indicator-feeds/{feed_id}/snapshot | PUT | JSON | JSON | 更新指标信息流数据 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/intel/ip | GET | Query | JSON | 获取 IP Overview |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/intel/miscategorization | POST | JSON | JSON | 创建 Miscategorization |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/intel/sinkholes | GET | Query | JSON | 列出 sinkholes 所属按 this 账户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/intel/sinkholes | POST | JSON | JSON | 创建新黑洞用于你的账户 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/intel/sinkholes/{sinkhole_id} | DELETE | Query | JSON | 删除黑洞 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/intel/sinkholes/{sinkhole_id} | GET | Query | JSON | 获取黑洞 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/intel/sinkholes/{sinkhole_id} | PUT | JSON | JSON | 更新黑洞 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/intel/url | GET | Query | JSON | 获取 URL 情报 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/intel/whois | GET | Query | JSON | 获取 WHOIS 记录 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/intel/sinkholes/{sinkhole_id}/ingresses | POST | JSON | JSON | 创建入口规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/intel/sinkholes/{sinkhole_id}/ingresses/{ingress_id} | DELETE | Query | JSON | 删除入口规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/intel/sinkholes/{sinkhole_id}/ingresses/{ingress_id} | GET | Query | JSON | 获取入口规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/intel/sinkholes/{sinkhole_id}/ingresses/{ingress_id} | PUT | JSON | JSON | 更新入口规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/precursor | GET | Query | JSON | 获取区域 Precursor 配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/precursor | PUT | JSON | JSON | 更新区域 Precursor 配置 |

## 流量管理与可用性（105 条）

> 负载均衡器与地址池/监控器、健康检查、等候室、页面规则、缓存设置、自定义错误页。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/monitor_groups | GET | Query | JSON | 列出监控组 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/monitor_groups | POST | JSON | JSON | 创建监控组 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/monitor_groups/{monitor_group_id} | DELETE | Query | JSON | 删除监控组 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/monitor_groups/{monitor_group_id} | GET | Query | JSON | 监控组详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/monitor_groups/{monitor_group_id} | PATCH | JSON | JSON | 修改监控组 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/monitor_groups/{monitor_group_id} | PUT | JSON | JSON | 更新监控组 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/monitor_groups/{monitor_group_id}/references | GET | Query | JSON | 列出监控组引用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/monitors | GET | Query | JSON | 列出监控 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/monitors | POST | JSON | JSON | 创建监控 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/monitors/{monitor_id} | DELETE | Query | JSON | 删除监控 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/monitors/{monitor_id} | GET | Query | JSON | 监控详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/monitors/{monitor_id} | PATCH | JSON | JSON | 修改监控 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/monitors/{monitor_id} | PUT | JSON | JSON | 更新监控 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/monitors/{monitor_id}/preview | POST | JSON | JSON | 预览监控 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/monitors/{monitor_id}/references | GET | Query | JSON | 列出监控引用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/pools | GET | Query | JSON | 列出地址池 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/pools | PATCH | JSON | JSON | 修改地址池 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/pools | POST | JSON | JSON | 创建地址池 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/pools/{pool_id} | DELETE | Query | JSON | 删除地址池 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/pools/{pool_id} | GET | Query | JSON | 地址池详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/pools/{pool_id} | PATCH | JSON | JSON | 修改地址池 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/pools/{pool_id} | PUT | JSON | JSON | 更新地址池 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/pools/{pool_id}/health | GET | Query | JSON | 地址池健康详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/pools/{pool_id}/preview | POST | JSON | JSON | 预览地址池 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/pools/{pool_id}/references | GET | Query | JSON | 列出地址池引用 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/preview/{preview_id} | GET | Query | JSON | 预览结果 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/regions | GET | Query | JSON | 列出地区 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/regions/{region_id} | GET | Query | JSON | 获取地区 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/load_balancers/search | GET | Query | JSON | 搜索资源 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/cache/cache_reserve | GET | Query | JSON | 获取缓存预留设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/cache/cache_reserve | PATCH | JSON | JSON | 变更缓存预留设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/cache/cache_reserve_clear | GET | Query | JSON | 获取缓存预留清除 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/cache/cache_reserve_clear | POST | JSON | JSON | 启动缓存预留清除 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/cache/regional_tiered_cache | GET | Query | JSON | 获取区域分层缓存设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/cache/regional_tiered_cache | PATCH | JSON | JSON | 变更区域分层缓存设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/cache/tiered_cache_smart_topology_enable | DELETE | Query | JSON | 删除智能分层缓存设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/cache/tiered_cache_smart_topology_enable | GET | Query | JSON | 获取智能分层缓存设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/cache/tiered_cache_smart_topology_enable | PATCH | JSON | JSON | 修改智能分层缓存设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/cache/tiered_cache_smart_topology_enable | POST | JSON | JSON | 创建智能分层缓存设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/cache/variants | DELETE | Query | JSON | 删除变体设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/cache/variants | GET | Query | JSON | 获取变体设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/cache/variants | PATCH | JSON | JSON | 变更变体设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/environments/{environment_id}/invalidate_cache | POST | JSON | JSON | 使失效已缓存内容按环境 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/environments/{environment_id}/purge_cache | POST | JSON | JSON | 清除已缓存内容按环境 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/healthchecks | GET | Query | JSON | 列出健康检查 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/healthchecks | POST | JSON | JSON | 创建健康检查 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/healthchecks/preview | POST | JSON | JSON | 创建预览健康检查 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/healthchecks/preview/{healthcheck_id} | DELETE | Query | JSON | 删除预览健康检查 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/healthchecks/preview/{healthcheck_id} | GET | Query | JSON | 健康检查预览详情 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/healthchecks/{healthcheck_id} | DELETE | Query | JSON | 删除健康检查 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/healthchecks/{healthcheck_id} | GET | Query | JSON | 健康检查详情 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/healthchecks/{healthcheck_id} | PATCH | JSON | JSON | 修改健康检查 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/healthchecks/{healthcheck_id} | PUT | JSON | JSON | 更新健康检查 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/invalidate_cache | POST | JSON | JSON | 使失效已缓存内容 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/origin/cloud_regions | GET | Query | JSON | 列出源站云区域映射 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/origin/cloud_regions/batch | DELETE | Query | JSON | 批次删除源站云区域映射 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/origin/cloud_regions/batch | PUT | JSON | JSON | 批次创建或替换源站云区域映射 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/origin/cloud_regions/supported_regions | GET | Query | JSON | 列出支持的云厂商与地区 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/origin/cloud_regions/{origin_ip} | DELETE | Query | JSON | 删除源站云区域映射 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/origin/cloud_regions/{origin_ip} | GET | Query | JSON | 获取源站云区域映射 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/origin/cloud_regions/{origin_ip} | PUT | JSON | JSON | 创建或替换源站云区域映射 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/pagerules | GET | Query | JSON | 列出页面规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/pagerules | POST | JSON | JSON | 创建页面规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/pagerules/{pagerule_id} | DELETE | Query | JSON | 删除页面规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/pagerules/{pagerule_id} | GET | Query | JSON | 获取页面规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/pagerules/{pagerule_id} | PATCH | JSON | JSON | 编辑页面规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/pagerules/{pagerule_id} | PUT | JSON | JSON | 更新页面规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/purge_cache | POST | JSON | JSON | 清除已缓存内容 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/waiting_rooms | POST | JSON | JSON | 创建等候室 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/waiting_rooms/preview | POST | JSON | JSON | 创建自定义等候室寻呼预览 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/waiting_rooms/settings | GET | Query | JSON | 获取区域级等候室设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/waiting_rooms/settings | PATCH | JSON | JSON | 修改区域级等候室设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/waiting_rooms/settings | PUT | JSON | JSON | 更新区域级等候室设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/waiting_rooms/{waiting_room_id} | DELETE | Query | JSON | 删除等候室 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/waiting_rooms/{waiting_room_id} | GET | Query | JSON | 等候室详情 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/waiting_rooms/{waiting_room_id} | PATCH | JSON | JSON | 修改等候室 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/waiting_rooms/{waiting_room_id} | PUT | JSON | JSON | 更新等候室 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/waiting_rooms/{waiting_room_id}/events | GET | Query | JSON | 列出事件 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/waiting_rooms/{waiting_room_id}/events | POST | JSON | JSON | 创建事件 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/waiting_rooms/{waiting_room_id}/events/{event_id} | DELETE | Query | JSON | 删除事件 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/waiting_rooms/{waiting_room_id}/events/{event_id} | GET | Query | JSON | 事件详情 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/waiting_rooms/{waiting_room_id}/events/{event_id} | PATCH | JSON | JSON | 修改事件 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/waiting_rooms/{waiting_room_id}/events/{event_id} | PUT | JSON | JSON | 更新事件 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/waiting_rooms/{waiting_room_id}/events/{event_id}/details | GET | Query | JSON | 预览活跃事件详情 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/waiting_rooms/{waiting_room_id}/rules | GET | Query | JSON | 列出等候室规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/waiting_rooms/{waiting_room_id}/rules | POST | JSON | JSON | 创建等候室规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/waiting_rooms/{waiting_room_id}/rules | PUT | JSON | JSON | 替换等候室规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/waiting_rooms/{waiting_room_id}/rules/{rule_id} | DELETE | Query | JSON | 删除等候室规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/waiting_rooms/{waiting_room_id}/rules/{rule_id} | PATCH | JSON | JSON | 修改等候室规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/waiting_rooms/{waiting_room_id}/status | GET | Query | JSON | 获取等候室状态 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/custom_pages | GET | Query | JSON | 列出自定义 pages |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/custom_pages/assets | GET | Query | JSON | 列出自定义资产 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/custom_pages/assets | POST | JSON | JSON | 创建自定义资产 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/custom_pages/assets/{asset_name} | DELETE | Query | JSON | 删除自定义资产 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/custom_pages/assets/{asset_name} | GET | Query | JSON | 获取自定义资产 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/custom_pages/assets/{asset_name} | PUT | JSON | JSON | 更新自定义资产 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/custom_pages/{identifier} | GET | Query | JSON | 获取自定义寻呼 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/custom_pages/{identifier} | PUT | JSON | JSON | 更新自定义寻呼 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/load_balancers | GET | Query | JSON | 列出账户或区域负载均衡器 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/load_balancers | POST | JSON | JSON | 创建账户或区域负载均衡器 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/load_balancers/{load_balancer_id} | DELETE | Query | JSON | 删除账户或区域负载均衡器 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/load_balancers/{load_balancer_id} | GET | Query | JSON | 账户或区域负载均衡器详情 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/load_balancers/{load_balancer_id} | PATCH | JSON | JSON | 修改账户或区域负载均衡器 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/load_balancers/{load_balancer_id} | PUT | JSON | JSON | 更新账户或区域负载均衡器 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/waiting_rooms | GET | Query | JSON | 列出等候室用于账户或区域 |

## 邮件服务（100 条）

> Email Security 邮件安全网关、Email Routing 邮件路由、Email Sending 邮件发送、Email Auth 邮件认证。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/investigate | GET | Query | JSON | 搜索邮箱消息 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/investigate/bulk | GET | Query | JSON | 列出批量动作任务 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/investigate/bulk | POST | JSON | JSON | 创建批量动作任务 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/investigate/bulk/{job_id} | DELETE | Query | JSON | 删除批量动作任务 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/investigate/bulk/{job_id} | GET | Query | JSON | 获取批量动作任务详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/investigate/bulk/{job_id}/cancel | POST | JSON | JSON | 取消批量动作任务 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/investigate/bulk/{job_id}/messages | GET | Query | JSON | 列出消息用于批量动作任务 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/investigate/move | POST | JSON | JSON | 移动消息 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/investigate/preview | POST | JSON | JSON | 生成预览用于 non-detection 消息 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/investigate/release | POST | JSON | JSON | 发布消息来自隔离区 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/investigate/{investigate_id} | GET | Query | JSON | 获取消息详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/investigate/{investigate_id}/detections | GET | Query | JSON | 获取消息检测详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/investigate/{investigate_id}/move | POST | JSON | JSON | 移动消息 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/investigate/{investigate_id}/preview | GET | Query | JSON | 获取预览用于检测 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/investigate/{investigate_id}/raw | GET | Query | JSON | 获取原始邮箱内容 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/investigate/{investigate_id}/trace | GET | Query | JSON | 获取邮箱追踪 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/phishguard/reports | GET | Query | JSON | 列出 PhishGuard 报告 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/allow_policies | GET | Query | JSON | 列出邮箱允许策略 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/allow_policies | POST | JSON | JSON | 创建邮件允许策略 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/allow_policies/batch | POST | JSON | JSON | 批次允许策略操作 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/allow_policies/{policy_id} | DELETE | Query | JSON | 删除邮件允许策略 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/allow_policies/{policy_id} | GET | Query | JSON | 获取邮件允许策略 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/allow_policies/{policy_id} | PATCH | JSON | JSON | 更新邮件允许策略 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/block_senders | GET | Query | JSON | 列出已屏蔽邮箱发送者 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/block_senders | POST | JSON | JSON | 创建已屏蔽邮箱发送者 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/block_senders/batch | POST | JSON | JSON | 批次已屏蔽发送者操作 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/block_senders/{pattern_id} | DELETE | Query | JSON | 删除已屏蔽邮箱发送者 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/block_senders/{pattern_id} | GET | Query | JSON | 获取已屏蔽邮箱发送者 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/block_senders/{pattern_id} | PATCH | JSON | JSON | 更新已屏蔽邮箱发送者 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/content_policies | GET | Query | JSON | 列出内容策略 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/content_policies | POST | JSON | JSON | 创建内容策略 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/content_policies/batch | POST | JSON | JSON | 批次内容策略操作 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/content_policies/{policy_id} | DELETE | Query | JSON | 删除内容策略 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/content_policies/{policy_id} | GET | Query | JSON | 获取内容策略 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/content_policies/{policy_id} | PATCH | JSON | JSON | 更新内容策略 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/domains | GET | Query | JSON | 列出受保护邮箱域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/domains | POST | JSON | JSON | 添加新邮箱域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/domains/batch | POST | JSON | JSON | 批次域名操作 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/domains/{domain_id} | DELETE | Query | JSON | 取消保护邮箱域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/domains/{domain_id} | GET | Query | JSON | 获取邮箱域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/domains/{domain_id} | PATCH | JSON | JSON | 更新邮箱域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/domains/{domain_id} | PUT | JSON | JSON | 替换邮箱域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/impersonation_registry | GET | Query | JSON | 列出仿冒注册表条目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/impersonation_registry | POST | JSON | JSON | 创建仿冒注册表条目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/impersonation_registry/{impersonation_registry_id} | DELETE | Query | JSON | 删除仿冒注册表条目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/impersonation_registry/{impersonation_registry_id} | GET | Query | JSON | 获取仿冒注册表条目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/impersonation_registry/{impersonation_registry_id} | PATCH | JSON | JSON | 更新仿冒注册表条目 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/sending_domain_restrictions | GET | Query | JSON | 列出发送域名限制 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/sending_domain_restrictions | POST | JSON | JSON | 创建发送域名限制 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/sending_domain_restrictions/{sending_domain_restriction_id} | DELETE | Query | JSON | 删除发送域名限制 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/sending_domain_restrictions/{sending_domain_restriction_id} | GET | Query | JSON | 获取发送域名限制 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/sending_domain_restrictions/{sending_domain_restriction_id} | PATCH | JSON | JSON | 更新发送域名限制 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/trusted_domains | GET | Query | JSON | 列出受信任邮箱域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/trusted_domains | POST | JSON | JSON | 创建受信任邮箱域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/trusted_domains/batch | POST | JSON | JSON | 批次受信任域名操作 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/trusted_domains/{trusted_domain_id} | DELETE | Query | JSON | 删除受信任邮箱域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/trusted_domains/{trusted_domain_id} | GET | Query | JSON | 获取受信任邮箱域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/trusted_domains/{trusted_domain_id} | PATCH | JSON | JSON | 更新受信任邮箱域名 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/url_ignore_patterns | GET | Query | JSON | 列出 URL 忽略模式 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/url_ignore_patterns | POST | JSON | JSON | 创建 URL 忽略模式 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/url_ignore_patterns/{pattern_id} | DELETE | Query | JSON | 删除 URL 忽略模式 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/url_ignore_patterns/{pattern_id} | GET | Query | JSON | 获取 URL 忽略模式 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/settings/url_ignore_patterns/{pattern_id} | PATCH | JSON | JSON | 更新 URL 忽略模式 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email-security/submissions | GET | Query | JSON | 列出重新分类提交 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email/routing/addresses | GET | Query | JSON | 列出目的地地址 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email/routing/addresses | POST | JSON | JSON | 创建目的地地址 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email/routing/addresses/{destination_address_identifier} | DELETE | Query | JSON | 删除目的地地址 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email/routing/addresses/{destination_address_identifier} | GET | Query | JSON | 获取目的地地址 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email/routing/addresses/{destination_address_identifier} | PATCH | JSON | JSON | 更新目的地地址 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email/sending/send | POST | JSON | JSON | 发送邮箱 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email/sending/send_raw | POST | JSON | JSON | 发送原始 MIME 邮箱 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email/sending/suppressions | GET | Query | JSON | 列出账户邮件发送 suppressions |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email/sending/suppressions | POST | JSON | JSON | 创建账户邮箱发送抑制 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email/sending/suppressions/bulk | POST | JSON | JSON | 批量导入账户邮件发送 suppressions |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email/sending/suppressions/{suppression_id} | DELETE | Query | JSON | 删除账户邮箱发送抑制 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email/sending/suppressions/{suppression_id} | GET | Query | JSON | 获取账户邮箱发送抑制 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/email/sending/suppressions/{suppression_id} | PATCH | JSON | JSON | 更新账户邮箱发送抑制 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/email/auth/dmarc-reports | GET | Query | JSON | 获取 DMARC 报告状态 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/email/auth/dmarc-reports | PATCH | JSON | JSON | 配置 DMARC 报告 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/email/auth/spf/inspect | GET | Query | JSON | 检查 SPF 记录 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/email/routing | GET | Query | JSON | 获取邮件路由设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/email/routing | PATCH | JSON | JSON | 更新邮件路由设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/email/routing | PUT | JSON | JSON | 应用邮件路由设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/email/routing/dns | DELETE | Query | JSON | 禁用邮件路由 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/email/routing/dns | GET | Query | JSON | 邮件路由 - DNS 设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/email/routing/dns | PATCH | JSON | JSON | 解锁邮件路由 DNS 记录 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/email/routing/dns | POST | JSON | JSON | 启用邮件路由 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/email/routing/rules | POST | JSON | JSON | 创建路由规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/email/routing/rules/catch_all | GET | Query | JSON | 获取全域接收规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/email/routing/rules/catch_all | PUT | JSON | JSON | 更新全域接收规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/email/routing/rules/{rule_identifier} | DELETE | Query | JSON | 删除路由规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/email/routing/rules/{rule_identifier} | GET | Query | JSON | 获取路由规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/email/routing/rules/{rule_identifier} | PUT | JSON | JSON | 更新路由规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/email/sending/subdomains | GET | Query | JSON | 列出发送子域名 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/email/sending/subdomains | POST | JSON | JSON | 创建发送子域名 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/email/sending/subdomains/{subdomain_id} | DELETE | Query | JSON | 删除发送子域名 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/email/sending/subdomains/{subdomain_id} | GET | Query | JSON | 获取发送子域名 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/email/sending/subdomains/{subdomain_id} | PATCH | JSON | JSON | 更新发送子域名 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/email/sending/subdomains/{subdomain_id}/dns | GET | Query | JSON | 获取发送子域名 DNS 记录 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/email/routing/rules | GET | Query | JSON | 列出账户或区域路由规则 |

## AI 与智能服务（100 条）

> Workers AI 模型推理、AI Gateway 统一网关、AI 搜索（Vectorize/文档）、AI 安全与审计。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/billing/credit-balance | GET | Query | JSON | 获取额度均衡 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/billing/invoice-history | GET | Query | JSON | 获取发票历史 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/billing/invoice-preview | GET | Query | JSON | 获取发票预览 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/billing/spending-limit | DELETE | Query | JSON | 删除 spending 限制 (已废弃) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/billing/spending-limit | GET | Query | JSON | 获取 spending 限制 (已废弃) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/billing/topup | POST | JSON | JSON | 创建充值 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/billing/topup/config | DELETE | Query | JSON | 删除 auto 充值配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/billing/topup/config | GET | Query | JSON | 获取 auto 充值配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/billing/topup/config | POST | JSON | JSON | 设置 auto 充值配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/billing/topup/status | POST | JSON | JSON | 检查充值状态 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/billing/usage-history | GET | Query | JSON | 获取用量历史 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/custom-providers | GET | Query | JSON | 列出自定义提供方 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/custom-providers | POST | JSON | JSON | 创建自定义提供方 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/custom-providers/{id} | DELETE | Query | JSON | 删除自定义提供方 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/custom-providers/{id} | GET | Query | JSON | 获取自定义提供方 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/evaluation-types | GET | Query | JSON | 列出 evaluator 类型 (已废弃) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways | GET | Query | JSON | 列出网关 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways | POST | JSON | JSON | 创建网关 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/datasets | GET | Query | JSON | 列出数据集 (已废弃) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/datasets | POST | JSON | JSON | 创建数据集 (已废弃) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/datasets/{id} | DELETE | Query | JSON | 删除数据集 (已废弃) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/datasets/{id} | GET | Query | JSON | 获取数据集 (已废弃) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/datasets/{id} | PUT | JSON | JSON | 更新数据集 (已废弃) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/evaluations | GET | Query | JSON | 列出 evaluations (已废弃) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/evaluations | POST | JSON | JSON | 创建评估 (已废弃) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/evaluations/{id} | DELETE | Query | JSON | 删除评估 (已废弃) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/evaluations/{id} | GET | Query | JSON | 获取评估 (已废弃) |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/logs | DELETE | Query | JSON | 删除网关日志 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/logs | GET | Query | JSON | 列出网关日志 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/logs/{id} | GET | Query | JSON | 获取网关日志详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/logs/{id} | PATCH | JSON | JSON | 修改网关日志 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/logs/{id}/request | GET | Query | JSON | 获取网关日志请求 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/logs/{id}/response | GET | Query | JSON | 获取网关日志响应 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/provider_configs | GET | Query | JSON | 列出提供方密钥 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/provider_configs | POST | JSON | JSON | 存储提供方键 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/routes | GET | Query | JSON | 列出动态路由 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/routes | POST | JSON | JSON | 创建动态路由 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/routes/{id} | DELETE | Query | JSON | 删除动态路由 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/routes/{id} | GET | Query | JSON | 获取动态路由 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/routes/{id} | PATCH | JSON | JSON | 重命名动态路由 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/routes/{id}/deployments | GET | Query | JSON | 列出动态路由部署 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/routes/{id}/deployments | POST | JSON | JSON | 部署动态路由版本 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/routes/{id}/versions | GET | Query | JSON | 列出动态路由版本 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/routes/{id}/versions | POST | JSON | JSON | 创建动态路由版本 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/routes/{id}/versions/{version_id} | GET | Query | JSON | 获取动态路由版本 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{gateway_id}/url/{provider} | GET | Query | JSON | 获取网关 URL |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{id} | DELETE | Query | JSON | 删除网关 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{id} | GET | Query | JSON | 获取网关 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-gateway/gateways/{id} | PUT | JSON | JSON | 更新网关 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces | GET | Query | JSON | 列出命名空间 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces | POST | JSON | JSON | 创建命名空间 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name} | DELETE | Query | JSON | 删除命名空间 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name} | GET | Query | JSON | 获取命名空间 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name} | PUT | JSON | JSON | 更新命名空间 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name}/chat/completions | POST | JSON | JSON | Multi-Instance 聊天 Completions |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name}/instances | GET | Query | JSON | 列出 AI 搜索实例. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name}/instances | POST | JSON | JSON | 创建 AI 搜索实例 (搜索用于智能体 requires 默认命名空间). |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name}/instances/{id} | DELETE | Query | JSON | 删除 AI 搜索实例. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name}/instances/{id} | GET | Query | JSON | 获取 AI 搜索实例. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name}/instances/{id} | PUT | JSON | JSON | 更新 AI 搜索实例 (搜索用于智能体元数据 requires 默认命名空间). |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name}/instances/{id}/chat/completions | POST | JSON | JSON | 聊天 Completions |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name}/instances/{id}/items | GET | Query | JSON | 条目列出. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name}/instances/{id}/items | POST | JSON | JSON | 上传条目. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name}/instances/{id}/items | PUT | JSON | JSON | 创建或更新条目. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name}/instances/{id}/items/{item_id} | DELETE | Query | JSON | 删除条目. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name}/instances/{id}/items/{item_id} | GET | Query | JSON | 获取条目. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name}/instances/{id}/items/{item_id} | PATCH | JSON | JSON | 同步条目. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name}/instances/{id}/items/{item_id}/chunks | GET | Query | JSON | 列出条目分块. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name}/instances/{id}/items/{item_id}/download | GET | Query | JSON | 下载条目内容. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name}/instances/{id}/items/{item_id}/logs | GET | Query | JSON | 条目日志. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name}/instances/{id}/jobs | GET | Query | JSON | 列出任务 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name}/instances/{id}/jobs | POST | JSON | JSON | 创建新任务 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name}/instances/{id}/jobs/{job_id} | GET | Query | JSON | 获取任务详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name}/instances/{id}/jobs/{job_id} | PATCH | JSON | JSON | 取消 indexing 任务. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name}/instances/{id}/jobs/{job_id}/logs | GET | Query | JSON | 列出任务日志 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name}/instances/{id}/search | POST | JSON | JSON | 搜索 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name}/instances/{id}/stats | GET | Query | JSON | 获取实例统计. |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/namespaces/{name}/search | POST | JSON | JSON | Multi-Instance 搜索 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/tokens | GET | Query | JSON | 列出令牌 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/tokens | POST | JSON | JSON | 创建令牌 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/tokens/{id} | DELETE | Query | JSON | 删除令牌 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/tokens/{id} | GET | Query | JSON | 获取令牌 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai-search/tokens/{id} | PUT | JSON | JSON | 更新令牌 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai/authors/search | GET | Query | JSON | 作者搜索 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai/finetunes | GET | Query | JSON | 列出 Finetunes |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai/finetunes | POST | JSON | JSON | 创建新 Finetune |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai/finetunes/public | GET | Query | JSON | 列出公开 Finetunes |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai/finetunes/{finetune_id}/finetune-assets | POST | JSON | JSON | 上传 Finetune 资产 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai/models/schema | GET | Query | JSON | 获取 AI model's 输入与输出模式 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai/models/search | GET | Query | JSON | 模型搜索 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai/run/{model_name} | POST | JSON | JSON | 运行 Workers AI 模型 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai/tasks/search | GET | Query | JSON | 任务搜索 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai/tomarkdown | POST | JSON | JSON | 转换 uploaded 文件给 Markdown |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/ai/tomarkdown/supported | GET | Query | JSON | 列出支持的 Markdown 转换格式 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/ai-audit/robots | GET | Query | JSON | 获取爬虫.txt 规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/ai-audit/robots/bulk | POST | JSON | JSON | 批量获取爬虫.txt 规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/ai-security/custom-topics | GET | Query | JSON | 获取 AI 安全用于应用自定义主题的区域. |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/ai-security/custom-topics | PUT | JSON | JSON | 更新 AI 安全用于应用自定义主题的区域. |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/ai-security/settings | GET | Query | JSON | 获取 AI 安全用于应用状态用于区域. |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/ai-security/settings | PUT | JSON | JSON | 更新 AI 安全用于应用状态用于区域. |

## 分析与可观测性（83 条）

> 日志推送 Logpush、日志、告警 Alerting、RUM 真实用户监控、分析查询、诊断、请求追踪、审计日志。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/alerting/v3/available_alerts | GET | Query | JSON | 获取告警类型 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/alerting/v3/destinations/eligible | GET | Query | JSON | 获取投递 mechanism eligibility |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/alerting/v3/destinations/pagerduty | DELETE | Query | JSON | 删除 PagerDuty 服务 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/alerting/v3/destinations/pagerduty | GET | Query | JSON | 列出 PagerDuty 服务 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/alerting/v3/destinations/pagerduty/connect | POST | JSON | JSON | 创建 PagerDuty 集成令牌 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/alerting/v3/destinations/pagerduty/connect/{token_id} | GET | Query | JSON | 连接 PagerDuty |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/alerting/v3/destinations/webhooks | GET | Query | JSON | 列出 webhooks |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/alerting/v3/destinations/webhooks | POST | JSON | JSON | 创建 webhook |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/alerting/v3/destinations/webhooks/{webhook_id} | DELETE | Query | JSON | 删除 webhook |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/alerting/v3/destinations/webhooks/{webhook_id} | GET | Query | JSON | 获取 webhook |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/alerting/v3/destinations/webhooks/{webhook_id} | PUT | JSON | JSON | 更新 webhook |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/alerting/v3/history | GET | Query | JSON | 列出历史 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/alerting/v3/policies | GET | Query | JSON | 列出通知策略 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/alerting/v3/policies | POST | JSON | JSON | 创建通知策略 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/alerting/v3/policies/{policy_id} | DELETE | Query | JSON | 删除通知策略 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/alerting/v3/policies/{policy_id} | GET | Query | JSON | 获取通知策略 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/alerting/v3/policies/{policy_id} | PUT | JSON | JSON | 更新通知策略 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/alerting/v3/silences | GET | Query | JSON | 列出静默 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/alerting/v3/silences | POST | JSON | JSON | 创建静默 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/alerting/v3/silences | PUT | JSON | JSON | 更新静默 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/alerting/v3/silences/{silence_id} | DELETE | Query | JSON | 删除静默 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/alerting/v3/silences/{silence_id} | GET | Query | JSON | 获取静默 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/analytics/query/data-security/content-findings/top-n | POST | JSON | JSON | 热门集成按内容发现 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/analytics/query/data-security/findings/summary | POST | JSON | JSON | 数据安全发现摘要 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/analytics/query/data-security/findings/timeseries | POST | JSON | JSON | 数据安全发现 timeseries |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/analytics/query/{dataset}/summary | POST | JSON | JSON | 查询分析摘要 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/analytics/query/{dataset}/timeseries | POST | JSON | JSON | 查询分析 timeseries |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/analytics/query/{dataset}/top-n | POST | JSON | JSON | 查询分析 top-N |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/audit_logs | GET | Query | JSON | 获取账户审计日志 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/diagnostics/endpoint-healthchecks | GET | Query | JSON | 列出端点健康检查 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/diagnostics/endpoint-healthchecks | POST | JSON | JSON | 端点健康检查 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/diagnostics/endpoint-healthchecks/{id} | DELETE | Query | JSON | 删除端点健康检查 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/diagnostics/endpoint-healthchecks/{id} | GET | Query | JSON | 获取端点健康检查 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/diagnostics/endpoint-healthchecks/{id} | PUT | JSON | JSON | 更新端点健康检查 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/diagnostics/traceroute | POST | JSON | JSON | 路由追踪 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/logpush/transformers | GET | Query | JSON | 列出转换器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/logpush/transformers | POST | JSON | JSON | 创建转换器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/logpush/transformers/preview | POST | JSON | JSON | 预览转换器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/logpush/transformers/{transformer_id} | DELETE | Query | JSON | 删除转换器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/logpush/transformers/{transformer_id} | GET | Query | JSON | 获取转换器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/logpush/transformers/{transformer_id} | PUT | JSON | JSON | 更新转换器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/logpush/transformers/{transformer_id}/content | GET | Query | JSON | 获取转换器内容 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/logpush/transformers/{transformer_id}/versions | GET | Query | JSON | 列出转换器版本 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/logs/control/cmb/config | DELETE | Query | JSON | 删除 CMB 配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/logs/control/cmb/config | GET | Query | JSON | 获取 CMB 配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/logs/control/cmb/config | POST | JSON | JSON | 更新 CMB 配置 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/request-tracer/trace | POST | JSON | JSON | 请求追踪 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/rum/site_info | POST | JSON | JSON | 创建 Web 分析站点 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/rum/site_info/list | GET | Query | JSON | 列出 Web 分析站点 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/rum/site_info/{site_id} | DELETE | Query | JSON | 删除 Web 分析站点 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/rum/site_info/{site_id} | GET | Query | JSON | 获取 Web 分析站点 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/rum/site_info/{site_id} | PUT | JSON | JSON | 更新 Web 分析站点 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/rum/v2/{ruleset_id}/rule | POST | JSON | JSON | 创建 Web 分析规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/rum/v2/{ruleset_id}/rule/{rule_id} | DELETE | Query | JSON | 删除 Web 分析规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/rum/v2/{ruleset_id}/rule/{rule_id} | PUT | JSON | JSON | 更新 Web 分析规则 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/rum/v2/{ruleset_id}/rules | GET | Query | JSON | 列出规则内 Web 分析规则集 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/rum/v2/{ruleset_id}/rules | POST | JSON | JSON | 更新 Web 分析规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/logpush/edge/jobs | GET | Query | JSON | 列出即时日志任务 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/logpush/edge/jobs | POST | JSON | JSON | 创建即时日志任务 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/logs/control/retention/flag | GET | Query | JSON | 获取日志保留标志 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/logs/control/retention/flag | POST | JSON | JSON | 更新日志保留标志 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/logs/rayids/{ray_id} | GET | Query | JSON | 获取日志 RayIDs |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/logs/received | GET | Query | JSON | 获取日志已接收 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/logs/received/fields | GET | Query | JSON | 列出字段 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/logpush/datasets/{dataset_id}/fields | GET | Query | JSON | 列出字段 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/logpush/datasets/{dataset_id}/jobs | GET | Query | JSON | 列出日志推送任务用于数据集 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/logpush/jobs | GET | Query | JSON | 列出日志推送任务 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/logpush/jobs | POST | JSON | JSON | 创建日志推送任务 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/logpush/jobs/{job_id} | DELETE | Query | JSON | 删除日志推送任务 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/logpush/jobs/{job_id} | GET | Query | JSON | 获取日志推送任务详情 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/logpush/jobs/{job_id} | PUT | JSON | JSON | 更新日志推送任务 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/logpush/ownership | POST | JSON | JSON | 获取所有权挑战 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/logpush/ownership/validate | POST | JSON | JSON | 校验所有权挑战 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/logpush/validate/destination | POST | JSON | JSON | 校验目的地 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/logpush/validate/destination/exists | POST | JSON | JSON | 检查目的地 exists |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/logpush/validate/origin | POST | JSON | JSON | 校验源站 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/logs/explorer/datasets | GET | Query | JSON | 列出账户或区域数据集 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/logs/explorer/datasets | POST | JSON | JSON | 创建账户或区域数据集 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/logs/explorer/datasets/available | GET | Query | JSON | 列出可用账户或区域数据集 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/logs/explorer/datasets/{dataset_id} | DELETE | Query | JSON | 删除账户或区域数据集 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/logs/explorer/datasets/{dataset_id} | GET | Query | JSON | 获取账户或区域数据集 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/logs/explorer/datasets/{dataset_id} | PUT | JSON | JSON | 更新账户或区域数据集 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/logs/explorer/query/sql | POST | JSON | JSON | 运行日志查询 |

## SSL/TLS 与证书管理（69 条）

> SSL/TLS 设置、自定义证书、源站 CA、客户端证书、Keyless SSL、mTLS 证书、源站 TLS 客户端认证、CSR、ACM 证书包、证书颁发机构、DCV 委托。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/mtls_certificates | GET | Query | JSON | 列出 mTLS 证书 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/mtls_certificates | POST | JSON | JSON | 上传 mTLS 证书 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/mtls_certificates/{mtls_certificate_id} | DELETE | Query | JSON | 删除 mTLS 证书 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/mtls_certificates/{mtls_certificate_id} | GET | Query | JSON | 获取 mTLS 证书 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/mtls_certificates/{mtls_certificate_id}/associations | GET | Query | JSON | 列出 mTLS 证书关联 |
| https://api.cloudflare.com/client/v4/certificates | GET | Query | JSON | 列出证书 |
| https://api.cloudflare.com/client/v4/certificates | POST | JSON | JSON | 创建证书 |
| https://api.cloudflare.com/client/v4/certificates/{certificate_id} | DELETE | Query | JSON | 撤销证书 |
| https://api.cloudflare.com/client/v4/certificates/{certificate_id} | GET | Query | JSON | 获取证书 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/acm/custom_trust_store | GET | Query | JSON | 列出自定义源站信任存储详情 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/acm/custom_trust_store | POST | JSON | JSON | 上传自定义源站信任存储 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/acm/custom_trust_store/{custom_origin_trust_store_id} | DELETE | Query | JSON | 删除自定义源站信任存储 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/acm/custom_trust_store/{custom_origin_trust_store_id} | GET | Query | JSON | 自定义源站信任存储详情 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/acm/total_tls | GET | Query | JSON | 总数 TLS 设置详情 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/acm/total_tls | POST | JSON | JSON | 启用或禁用总数 TLS |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/certificate_authorities/hostname_associations | GET | Query | JSON | 列出主机名关联 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/certificate_authorities/hostname_associations | PUT | JSON | JSON | 替换主机名关联 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/client_certificates | GET | Query | JSON | 列出客户端证书 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/client_certificates | POST | JSON | JSON | 创建客户端证书 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/client_certificates/{client_certificate_id} | DELETE | Query | JSON | 撤销客户端证书 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/client_certificates/{client_certificate_id} | GET | Query | JSON | 客户端证书详情 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/client_certificates/{client_certificate_id} | PATCH | JSON | JSON | Reactivate 客户端证书 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/custom_certificates | GET | Query | JSON | 列出 SSL 配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/custom_certificates | POST | JSON | JSON | 创建 SSL 配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/custom_certificates/prioritize | PUT | JSON | JSON | Re-prioritize SSL 证书 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/custom_certificates/{custom_certificate_id} | DELETE | Query | JSON | 删除 SSL 配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/custom_certificates/{custom_certificate_id} | GET | Query | JSON | SSL 配置详情 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/custom_certificates/{custom_certificate_id} | PATCH | JSON | JSON | 编辑 SSL 配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/dcv_delegation/uuid | GET | Query | JSON | 获取 DCV 委托唯一标识符. |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/keyless_certificates | GET | Query | JSON | 列出 Keyless SSL 配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/keyless_certificates | POST | JSON | JSON | 创建 Keyless SSL 配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/keyless_certificates/{keyless_certificate_id} | DELETE | Query | JSON | 删除 Keyless SSL 配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/keyless_certificates/{keyless_certificate_id} | GET | Query | JSON | 获取 Keyless SSL 配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/keyless_certificates/{keyless_certificate_id} | PATCH | JSON | JSON | 编辑 Keyless SSL 配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/origin_tls_client_auth | GET | Query | JSON | 列出证书 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/origin_tls_client_auth | POST | JSON | JSON | 上传证书 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/origin_tls_client_auth/hostnames | PUT | JSON | JSON | 启用或禁用主机名用于客户端认证 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/origin_tls_client_auth/hostnames/certificates | GET | Query | JSON | 列出证书 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/origin_tls_client_auth/hostnames/certificates | POST | JSON | JSON | 上传主机名客户端证书 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/origin_tls_client_auth/hostnames/certificates/{certificate_id} | DELETE | Query | JSON | 删除主机名客户端证书 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/origin_tls_client_auth/hostnames/certificates/{certificate_id} | GET | Query | JSON | 获取主机名客户端证书 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/origin_tls_client_auth/hostnames/{hostname} | GET | Query | JSON | 获取主机名状态用于客户端认证 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/origin_tls_client_auth/settings | GET | Query | JSON | 获取启用设置用于区域 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/origin_tls_client_auth/settings | PUT | JSON | JSON | 设置启用用于区域 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/origin_tls_client_auth/{certificate_id} | DELETE | Query | JSON | 删除证书 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/origin_tls_client_auth/{certificate_id} | GET | Query | JSON | 获取证书详情 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/auto_origin_tls_kex | GET | Query | JSON | 获取自动源站 TLS KEX 注册状态用于指定区域 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/auto_origin_tls_kex | PATCH | JSON | JSON | 修改自动源站 TLS KEX 注册状态用于指定区域 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/origin_tls_compliance_modes | DELETE | Query | JSON | 删除源站 TLS 合规模式设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/origin_tls_compliance_modes | GET | Query | JSON | 获取源站 TLS 合规模式设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/origin_tls_compliance_modes | PATCH | JSON | JSON | 变更源站 TLS 合规模式设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/origin_tls_compliance_modes | PUT | JSON | JSON | 替换源站 TLS 合规模式设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/ssl_automatic_mode | GET | Query | JSON | 获取自动 SSL/TLS 注册状态用于指定区域 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/settings/ssl_automatic_mode | PATCH | JSON | JSON | 修改自动 SSL/TLS 注册状态用于指定区域 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/ssl/analyze | POST | JSON | JSON | 分析证书 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/ssl/certificate_packs | GET | Query | JSON | 列出证书包 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/ssl/certificate_packs/order | POST | JSON | JSON | 订购高级证书管理器证书包 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/ssl/certificate_packs/quota | GET | Query | JSON | 获取证书包配额 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/ssl/certificate_packs/{certificate_pack_id} | DELETE | Query | JSON | 删除高级证书管理器证书包 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/ssl/certificate_packs/{certificate_pack_id} | GET | Query | JSON | 获取证书包 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/ssl/certificate_packs/{certificate_pack_id} | PATCH | JSON | JSON | 重启校验或更新高级证书管理器证书包 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/ssl/universal/settings | GET | Query | JSON | 通用 SSL 设置详情 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/ssl/universal/settings | PATCH | JSON | JSON | 编辑通用 SSL 设置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/ssl/verification | GET | Query | JSON | SSL 验证详情 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/ssl/verification/{certificate_pack_id} | PATCH | JSON | JSON | 编辑 SSL 证书包校验方法 |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/custom_csrs | GET | Query | JSON | 列出自定义 CSRs |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/custom_csrs | POST | JSON | JSON | 创建自定义 CSR |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/custom_csrs/{custom_csr_id} | DELETE | Query | JSON | 删除自定义 CSR |
| https://api.cloudflare.com/client/v4/{accounts_or_zones}/{account_or_zone_id}/custom_csrs/{custom_csr_id} | GET | Query | JSON | 自定义 CSR 详情 |

## 身份与访问管理（IAM）（45 条）

> API 令牌、令牌校验、权限与角色、服务令牌、身份提供商（IdP）。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/iam/permission_groups | GET | Query | JSON | 列出账户权限组 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/iam/permission_groups/{permission_group_id} | GET | Query | JSON | 权限组详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/iam/resource_groups | GET | Query | JSON | 列出资源组 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/iam/resource_groups | POST | JSON | JSON | 创建资源组 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/iam/resource_groups/{resource_group_id} | DELETE | Query | JSON | 移除资源组 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/iam/resource_groups/{resource_group_id} | GET | Query | JSON | 资源组详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/iam/resource_groups/{resource_group_id} | PUT | JSON | JSON | 更新资源组 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/iam/user_groups | GET | Query | JSON | 列出用户组 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/iam/user_groups | POST | JSON | JSON | 创建用户组 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/iam/user_groups/{user_group_id} | DELETE | Query | JSON | 移除用户组 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/iam/user_groups/{user_group_id} | GET | Query | JSON | 用户组详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/iam/user_groups/{user_group_id} | PUT | JSON | JSON | 更新用户组 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/iam/user_groups/{user_group_id}/members | GET | Query | JSON | 列出用户组成员 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/iam/user_groups/{user_group_id}/members | POST | JSON | JSON | 添加用户组成员 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/iam/user_groups/{user_group_id}/members | PUT | JSON | JSON | 更新用户组成员 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/iam/user_groups/{user_group_id}/members/{member_id} | DELETE | Query | JSON | 移除用户组成员 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/iam/user_groups/{user_group_id}/members/{member_id} | GET | Query | JSON | 获取用户组成员 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/oauth_clients | GET | Query | JSON | 列出 OAuth 客户端 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/oauth_clients | POST | JSON | JSON | 创建 OAuth 客户端 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/oauth_clients/{oauth_client_id} | DELETE | Query | JSON | 删除 OAuth 客户端 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/oauth_clients/{oauth_client_id} | GET | Query | JSON | OAuth 客户端详情 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/oauth_clients/{oauth_client_id} | PATCH | JSON | JSON | 更新 OAuth 客户端 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/oauth_clients/{oauth_client_id}/rotate_secret | DELETE | Query | JSON | 删除 Rotated OAuth 客户端密钥 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/oauth_clients/{oauth_client_id}/rotate_secret | POST | JSON | JSON | 轮换 OAuth 客户端密钥 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/sso_connectors | GET | Query | JSON | 获取全部 SSO 连接器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/sso_connectors | POST | JSON | JSON | Initialize 新 SSO 连接器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/sso_connectors/{sso_connector_id} | DELETE | Query | JSON | 删除 SSO 连接器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/sso_connectors/{sso_connector_id} | GET | Query | JSON | 获取单个 SSO 连接器 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/sso_connectors/{sso_connector_id} | PATCH | JSON | JSON | 更新 SSO 连接器状态 |
| https://api.cloudflare.com/client/v4/accounts/{account_id}/sso_connectors/{sso_connector_id}/begin_verification | POST | JSON | JSON | Begin SSO 连接器验证 |
| https://api.cloudflare.com/client/v4/oauth/scopes | GET | Query | JSON | 列出 OAuth 作用域 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/token_validation/config | GET | Query | JSON | 列出令牌校验配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/token_validation/config | POST | JSON | JSON | 创建令牌校验配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/token_validation/config/{config_id} | DELETE | Query | JSON | 删除令牌校验配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/token_validation/config/{config_id} | GET | Query | JSON | 获取令牌校验配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/token_validation/config/{config_id} | PATCH | JSON | JSON | 编辑令牌校验配置 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/token_validation/config/{config_id}/credentials | PATCH | JSON | JSON | 编辑令牌校验凭证 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/token_validation/config/{config_id}/credentials | PUT | JSON | JSON | 替换令牌校验凭证 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/token_validation/rules | GET | Query | JSON | 列出令牌校验规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/token_validation/rules | POST | JSON | JSON | 创建令牌校验规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/token_validation/rules/bulk | PATCH | JSON | JSON | 编辑令牌校验规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/token_validation/rules/bulk | POST | JSON | JSON | 创建令牌校验规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/token_validation/rules/{rule_id} | DELETE | Query | JSON | 删除令牌校验规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/token_validation/rules/{rule_id} | GET | Query | JSON | 获取令牌校验规则 |
| https://api.cloudflare.com/client/v4/zones/{zone_id}/token_validation/rules/{rule_id} | PATCH | JSON | JSON | 编辑令牌校验规则 |

## 附：Cloudflare 相关域名与子域名清单

**A. 承载 API 接口的域名**

| 域名 | 说明 |
| --- | --- |
| `api.cloudflare.com` | REST API 主入口（`/client/v4`），本文档全部接口宿主 |
| `r2.cloudflarestorage.com` | R2 对象存储 S3 兼容 API（独立于 client/v4） |

**B. 运行时与资源存储域名（非本文档 REST 接口面）**

| 域名 | 说明 |
| --- | --- |
| `*.workers.dev` | Workers 预览与自定义域名运行时 |
| `*.pages.dev` | Cloudflare Pages 站点托管 |
| `*.r2.dev` | R2 公开存储桶访问 |
| `vhs.cloudflare.com` | Stream 视频托管播放源 |
| `*.cdn.cloudflare.net` / `customer-origin-ca.cloudflare.com` | CDN 边缘与源站证书 |
| `dash.cloudflare.com` | 管理控制台（Web UI） |
| `developers.cloudflare.com` | 官方文档站（本数据来源） |

**C. 遥测/上报/内部域名（已排除，不收录接口）**

| 域名 | 说明 |
| --- | --- |
| `cloudflareinsights.com` / `static.cloudflareinsights.com` | RUM 真实用户监控数据上报 beacon |
| `cloudflare.com` 官网营销页 | 非 API 资源 |
| 各类 `*.sentry.io` / 内部监控 | 前端错误采集，非公开接口 |

## 二、统计汇总

**按业务大类计数**：

| 业务大类 | 接口数 |
| --- | --- |
| 零信任安全（Zero Trust） | 438 |
| Workers 与 Serverless 计算 | 256 |
| Magic 网络与互联 | 234 |
| 安全防护（WAF/DDoS/机器人） | 234 |
| 网络流量分析（Radar） | 150 |
| DNS 与域名解析 | 149 |
| 存储与数据服务 | 143 |
| 账户与组织管理 | 141 |
| 媒体与渲染 | 137 |
| Cloudforce One 威胁情报 | 129 |
| 流量管理与可用性 | 105 |
| 邮件服务 | 100 |
| AI 与智能服务 | 100 |
| 分析与可观测性 | 83 |
| SSL/TLS 与证书管理 | 69 |
| 身份与访问管理（IAM） | 45 |
| **合计** | **2513** |

**按 HTTP 方法统计**：

| 方法 | 数量 |
| --- | --- |
| GET | 1199 |
| POST | 532 |
| PUT | 267 |
| DELETE | 336 |
| PATCH | 179 |

**按请求格式统计**：

| 请求格式 | 数量 |
| --- | --- |
| Query | 1535 |
| JSON | 978 |

**说明**：本文档共收录 2513 条接口（1532 条唯一路径），覆盖 16 个业务大类、123 个官方资源组。接口清单来源于 Cloudflare 官方公开文档 developers.cloudflare.com/api，用途说明为对官方英文接口名的中文整理翻译；具体请求参数、响应字段与权限要求请以官方文档为准。
