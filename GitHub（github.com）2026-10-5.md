# GitHub REST API 接口文档

> **站点**：GitHub 开发者平台 REST API（`https://api.github.com`）  
> **抓取时间**：2026-10-05  
> **数据来源**：GitHub 官方维护的 OpenAPI 规范仓库 [`github/rest-api-description`](https://github.com/github/rest-api-description) 中 dotcom 版 `descriptions/api.github.com/api.github.com.json`（规范版本 1.1.4，对应 `X-GitHub-Api-Version: 2022-11-28`）  
> **提取方式**：直接解析官方 OpenAPI spec 的 `paths` 节点，逐操作读取 `method`、`tags`、`summary`（官方用途描述）、`deprecated`、`requestBody.content` 类型，比逆向前端 JS 更权威、完整、准确；用途说明由官方英文 `summary` 机器翻译为中文  
> **接口总数**：1232 条操作条目，覆盖 816 条唯一路径，分属 44 个官方分类（tags）  
> **方法分布**：GET 644 / POST 193 / PUT 134 / DELETE 188 / PATCH 73；其中 38 条已标注 `deprecated`（用途前加【已废弃】）  
> **已排除**：GraphQL API（`/graphql`，非 REST）、企业 Server 私有版差异接口、Webhook 事件 payload（非 HTTP 调用接口）、`docs.github.com` 文档站

## 一、通用约定

| 项目 | 说明 |
| --- | --- |
| 主 API 入口 | `https://api.github.com`（绝大多数接口统一挂载于此，路径变量以 `{}` 表示，如 `{owner}`、`{repo}`、`{issue_number}`） |
| 鉴权方式 | 请求头 `Authorization: Bearer <TOKEN>`，TOKEN 可为 Personal Access Token（classic / fine-grained）、OAuth App 用户令牌、GitHub App 安装令牌或用户访问令牌；部分公开仓库接口允许匿名访问 |
| 内容协商 | `Accept: application/vnd.github+json`（建议显式声明媒体类型以获得稳定响应结构） |
| API 版本 | `X-GitHub-Api-Version: 2022-11-28`（当前 GA 版本，规范固定该基线） |
| 速率限制 | 匿名按 IP 60 次/小时；认证用户/PAT 5000 次/小时；GitHub App 安装 5000 次/小时（并按分钟限 5000）；GraphQL 单独按积分计算。响应头 `X-RateLimit-Limit/Remaining/Reset` 反馈额度 |
| 请求格式 | GET/DELETE 走 Query 与路径参数；POST/PUT/PATCH 走 `application/json` 请求体；Release 资产上传等特殊接口走 `multipart`（`application/octet-stream`） |
| 返回格式 | 统一 JSON；`204 No Content` 表示操作成功但无响应体；列表接口返回数组并支持游标/分页 |
| 分页 | 传统 `?page=&per_page=`（≤100）配合响应头 `Link`；部分接口改用游标分页；搜索类上限 1000 条 |
| 条件请求 | 支持 `ETag`/`Last-Modified` 与 `If-None-Match`，命中返回 `304 Not Modified` 且不消耗速率额度 |
| 错误信封 | `{ "message": string, "documentation_url": string, "status"?: string }`；常见 401/403/404/422/451；二级验证失败返回 403 并附 `X-GitHub-OTP` 提示 |
| 幂等/写操作 | 写接口多为 RESTful 语义（POST 创建、PUT 整体替换、PATCH 局部更新、DELETE 删除），部分支持 `If-Match`/条件更新 |

**请求方法语义对照**：`GET` 读取资源；`POST` 创建资源或触发操作；`PUT` 替换/设置资源（含权限、启用等幂等设置）；`PATCH` 局部修改字段；`DELETE` 删除或撤销资源。

## 仓库 Repositories（203 条）

> 官方分类标签 `repos`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/orgs/{org}/repos | GET | Query | JSON | 列出组织仓库 |
| https://api.github.com/orgs/{org}/repos | POST | JSON | JSON | 创建组织仓库 |
| https://api.github.com/orgs/{org}/rulesets | GET | Query | JSON | 获取全部组织仓库规则集 |
| https://api.github.com/orgs/{org}/rulesets | POST | JSON | JSON | 创建组织仓库规则集 |
| https://api.github.com/orgs/{org}/rulesets/rule-suites | GET | Query | JSON | 列出组织规则套件 |
| https://api.github.com/orgs/{org}/rulesets/rule-suites/{rule_suite_id} | GET | Query | JSON | 获取组织规则套件 |
| https://api.github.com/orgs/{org}/rulesets/{ruleset_id} | DELETE | Query | JSON | 删除组织仓库规则集 |
| https://api.github.com/orgs/{org}/rulesets/{ruleset_id} | GET | Query | JSON | 获取组织仓库规则集 |
| https://api.github.com/orgs/{org}/rulesets/{ruleset_id} | PUT | JSON | JSON | 更新组织仓库规则集 |
| https://api.github.com/repos/{owner}/{repo} | DELETE | Query | JSON | 删除仓库 |
| https://api.github.com/repos/{owner}/{repo} | GET | Query | JSON | 获取仓库 |
| https://api.github.com/repos/{owner}/{repo} | PATCH | JSON | JSON | 更新仓库 |
| https://api.github.com/repos/{owner}/{repo}/activity | GET | Query | JSON | 列出仓库活动 |
| https://api.github.com/repos/{owner}/{repo}/attestations | POST | JSON | JSON | 创建证明 |
| https://api.github.com/repos/{owner}/{repo}/attestations/{subject_digest} | GET | Query | JSON | 列出证明 |
| https://api.github.com/repos/{owner}/{repo}/autolinks | GET | Query | JSON | 获取全部自动链接的仓库 |
| https://api.github.com/repos/{owner}/{repo}/autolinks | POST | JSON | JSON | 创建自动链接引用用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/autolinks/{autolink_id} | DELETE | Query | JSON | 删除自动链接引用来自仓库 |
| https://api.github.com/repos/{owner}/{repo}/autolinks/{autolink_id} | GET | Query | JSON | 获取自动链接引用的仓库 |
| https://api.github.com/repos/{owner}/{repo}/automated-security-fixes | DELETE | Query | JSON | 禁用 Dependabot 安全更新 |
| https://api.github.com/repos/{owner}/{repo}/automated-security-fixes | GET | Query | JSON | 检查是否 Dependabot 安全更新是否已启用用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/automated-security-fixes | PUT | Query | JSON | 启用 Dependabot 安全更新 |
| https://api.github.com/repos/{owner}/{repo}/branches | GET | Query | JSON | 列出分支 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch} | GET | Query | JSON | 获取分支 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection | DELETE | Query | JSON | 删除分支保护 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection | GET | Query | JSON | 获取分支保护 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection | PUT | JSON | JSON | 更新分支保护 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/enforce_admins | DELETE | Query | JSON | 删除管理员分支保护 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/enforce_admins | GET | Query | JSON | 获取管理员分支保护 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/enforce_admins | POST | Query | JSON | 设置管理员分支保护 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/required_pull_request_reviews | DELETE | Query | JSON | 删除拉取请求审核保护 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/required_pull_request_reviews | GET | Query | JSON | 获取拉取请求审核保护 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/required_pull_request_reviews | PATCH | JSON | JSON | 更新拉取请求审核保护 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/required_signatures | DELETE | Query | JSON | 删除提交签名保护 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/required_signatures | GET | Query | JSON | 获取提交签名保护 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/required_signatures | POST | Query | JSON | 创建提交签名保护 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/required_status_checks | DELETE | Query | JSON | 移除状态检查保护 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/required_status_checks | GET | Query | JSON | 获取状态检查保护 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/required_status_checks | PATCH | JSON | JSON | 更新状态检查保护 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/required_status_checks/contexts | DELETE | JSON | JSON | 移除状态检查上下文 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/required_status_checks/contexts | GET | Query | JSON | 获取全部状态检查上下文 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/required_status_checks/contexts | POST | JSON | JSON | 添加状态检查上下文 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/required_status_checks/contexts | PUT | JSON | JSON | 设置状态检查上下文 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/restrictions | DELETE | Query | JSON | 删除访问限制 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/restrictions | GET | Query | JSON | 获取访问限制 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/restrictions/apps | DELETE | JSON | JSON | 移除应用访问限制 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/restrictions/apps | GET | Query | JSON | 获取应用含访问给受保护分支 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/restrictions/apps | POST | JSON | JSON | 添加应用访问限制 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/restrictions/apps | PUT | JSON | JSON | 设置应用访问限制 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/restrictions/teams | DELETE | JSON | JSON | 移除团队访问限制 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/restrictions/teams | GET | Query | JSON | 获取团队含访问给受保护分支 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/restrictions/teams | POST | JSON | JSON | 添加团队访问限制 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/restrictions/teams | PUT | JSON | JSON | 设置团队访问限制 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/restrictions/users | DELETE | JSON | JSON | 移除用户访问限制 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/restrictions/users | GET | Query | JSON | 获取用户含访问给受保护分支 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/restrictions/users | POST | JSON | JSON | 添加用户访问限制 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/protection/restrictions/users | PUT | JSON | JSON | 设置用户访问限制 |
| https://api.github.com/repos/{owner}/{repo}/branches/{branch}/rename | POST | JSON | JSON | 重命名分支 |
| https://api.github.com/repos/{owner}/{repo}/codeowners/errors | GET | Query | JSON | 列出 CODEOWNERS 文件错误 |
| https://api.github.com/repos/{owner}/{repo}/collaborators | GET | Query | JSON | 列出仓库协作者 |
| https://api.github.com/repos/{owner}/{repo}/collaborators/{username} | DELETE | Query | JSON | 移除仓库协作者 |
| https://api.github.com/repos/{owner}/{repo}/collaborators/{username} | GET | Query | JSON | 检查是否用户是否仓库协作者 |
| https://api.github.com/repos/{owner}/{repo}/collaborators/{username} | PUT | JSON | JSON | 添加仓库协作者 |
| https://api.github.com/repos/{owner}/{repo}/collaborators/{username}/permission | GET | Query | JSON | 获取仓库权限用于用户 |
| https://api.github.com/repos/{owner}/{repo}/comments | GET | Query | JSON | 列出提交评论用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/comments/{comment_id} | DELETE | Query | JSON | 删除提交评论 |
| https://api.github.com/repos/{owner}/{repo}/comments/{comment_id} | GET | Query | JSON | 获取提交评论 |
| https://api.github.com/repos/{owner}/{repo}/comments/{comment_id} | PATCH | JSON | JSON | 更新提交评论 |
| https://api.github.com/repos/{owner}/{repo}/commits | GET | Query | JSON | 列出提交 |
| https://api.github.com/repos/{owner}/{repo}/commits/{commit_sha}/branches-where-head | GET | Query | JSON | 列出分支用于头部提交 |
| https://api.github.com/repos/{owner}/{repo}/commits/{commit_sha}/comments | GET | Query | JSON | 列出提交评论 |
| https://api.github.com/repos/{owner}/{repo}/commits/{commit_sha}/comments | POST | JSON | JSON | 创建提交评论 |
| https://api.github.com/repos/{owner}/{repo}/commits/{commit_sha}/pulls | GET | Query | JSON | 列出拉取请求关联含提交 |
| https://api.github.com/repos/{owner}/{repo}/commits/{ref} | GET | Query | JSON | 获取提交 |
| https://api.github.com/repos/{owner}/{repo}/commits/{ref}/status | GET | Query | JSON | 获取合并状态用于特定引用 |
| https://api.github.com/repos/{owner}/{repo}/commits/{ref}/statuses | GET | Query | JSON | 列出提交状态用于引用 |
| https://api.github.com/repos/{owner}/{repo}/community/profile | GET | Query | JSON | 获取社区简介指标 |
| https://api.github.com/repos/{owner}/{repo}/compare/{basehead} | GET | Query | JSON | 比较两个提交 |
| https://api.github.com/repos/{owner}/{repo}/contents/{path} | DELETE | JSON | JSON | 删除文件 |
| https://api.github.com/repos/{owner}/{repo}/contents/{path} | GET | JSON | JSON | 获取仓库内容 |
| https://api.github.com/repos/{owner}/{repo}/contents/{path} | PUT | JSON | JSON | 创建或更新文件内容 |
| https://api.github.com/repos/{owner}/{repo}/contributors | GET | Query | JSON | 列出仓库贡献者 |
| https://api.github.com/repos/{owner}/{repo}/deployments | GET | Query | JSON | 列出部署 |
| https://api.github.com/repos/{owner}/{repo}/deployments | POST | JSON | JSON | 创建部署 |
| https://api.github.com/repos/{owner}/{repo}/deployments/{deployment_id} | DELETE | Query | JSON | 删除部署 |
| https://api.github.com/repos/{owner}/{repo}/deployments/{deployment_id} | GET | Query | JSON | 获取部署 |
| https://api.github.com/repos/{owner}/{repo}/deployments/{deployment_id}/statuses | GET | Query | JSON | 列出部署状态 |
| https://api.github.com/repos/{owner}/{repo}/deployments/{deployment_id}/statuses | POST | JSON | JSON | 创建部署状态 |
| https://api.github.com/repos/{owner}/{repo}/deployments/{deployment_id}/statuses/{status_id} | GET | Query | JSON | 获取部署状态 |
| https://api.github.com/repos/{owner}/{repo}/dispatches | POST | JSON | JSON | 创建仓库调度事件 |
| https://api.github.com/repos/{owner}/{repo}/environments | GET | Query | JSON | 列出环境 |
| https://api.github.com/repos/{owner}/{repo}/environments/{environment_name} | DELETE | Query | JSON | 删除环境 |
| https://api.github.com/repos/{owner}/{repo}/environments/{environment_name} | GET | Query | JSON | 获取环境 |
| https://api.github.com/repos/{owner}/{repo}/environments/{environment_name} | PUT | JSON | JSON | 创建或更新环境 |
| https://api.github.com/repos/{owner}/{repo}/environments/{environment_name}/deployment-branch-policies | GET | Query | JSON | 列出部署分支策略 |
| https://api.github.com/repos/{owner}/{repo}/environments/{environment_name}/deployment-branch-policies | POST | JSON | JSON | 创建部署分支策略 |
| https://api.github.com/repos/{owner}/{repo}/environments/{environment_name}/deployment-branch-policies/{branch_policy_id} | DELETE | Query | JSON | 删除部署分支策略 |
| https://api.github.com/repos/{owner}/{repo}/environments/{environment_name}/deployment-branch-policies/{branch_policy_id} | GET | Query | JSON | 获取部署分支策略 |
| https://api.github.com/repos/{owner}/{repo}/environments/{environment_name}/deployment-branch-policies/{branch_policy_id} | PUT | JSON | JSON | 更新部署分支策略 |
| https://api.github.com/repos/{owner}/{repo}/environments/{environment_name}/deployment_protection_rules | GET | Query | JSON | 获取全部部署保护规则用于环境 |
| https://api.github.com/repos/{owner}/{repo}/environments/{environment_name}/deployment_protection_rules | POST | JSON | JSON | 创建自定义部署保护规则上环境 |
| https://api.github.com/repos/{owner}/{repo}/environments/{environment_name}/deployment_protection_rules/apps | GET | Query | JSON | 列出自定义部署规则集成可用用于环境 |
| https://api.github.com/repos/{owner}/{repo}/environments/{environment_name}/deployment_protection_rules/{protection_rule_id} | DELETE | Query | JSON | 禁用自定义保护规则用于环境 |
| https://api.github.com/repos/{owner}/{repo}/environments/{environment_name}/deployment_protection_rules/{protection_rule_id} | GET | Query | JSON | 获取自定义部署保护规则 |
| https://api.github.com/repos/{owner}/{repo}/forks | GET | Query | JSON | 列出 forks |
| https://api.github.com/repos/{owner}/{repo}/forks | POST | JSON | JSON | 创建 fork |
| https://api.github.com/repos/{owner}/{repo}/hash-algorithm | GET | Query | JSON | 获取哈希算法用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/hooks | GET | Query | JSON | 列出仓库 webhooks |
| https://api.github.com/repos/{owner}/{repo}/hooks | POST | JSON | JSON | 创建仓库 webhook |
| https://api.github.com/repos/{owner}/{repo}/hooks/{hook_id} | DELETE | Query | JSON | 删除仓库 webhook |
| https://api.github.com/repos/{owner}/{repo}/hooks/{hook_id} | GET | Query | JSON | 获取仓库 webhook |
| https://api.github.com/repos/{owner}/{repo}/hooks/{hook_id} | PATCH | JSON | JSON | 更新仓库 webhook |
| https://api.github.com/repos/{owner}/{repo}/hooks/{hook_id}/config | GET | Query | JSON | 获取 webhook 配置用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/hooks/{hook_id}/config | PATCH | JSON | JSON | 更新 webhook 配置用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/hooks/{hook_id}/deliveries | GET | Query | JSON | 列出投递用于仓库 webhook |
| https://api.github.com/repos/{owner}/{repo}/hooks/{hook_id}/deliveries/{delivery_id} | GET | Query | JSON | 获取投递用于仓库 webhook |
| https://api.github.com/repos/{owner}/{repo}/hooks/{hook_id}/deliveries/{delivery_id}/attempts | POST | Query | JSON | 重新投递投递用于仓库 webhook |
| https://api.github.com/repos/{owner}/{repo}/hooks/{hook_id}/pings | POST | Query | JSON | Ping 仓库 webhook |
| https://api.github.com/repos/{owner}/{repo}/hooks/{hook_id}/tests | POST | Query | JSON | 测试推送仓库 webhook |
| https://api.github.com/repos/{owner}/{repo}/immutable-releases | DELETE | Query | JSON | 禁用不可变发行版 |
| https://api.github.com/repos/{owner}/{repo}/immutable-releases | GET | Query | JSON | 检查是否不可变发行版是否已启用用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/immutable-releases | PUT | Query | JSON | 启用不可变发行版 |
| https://api.github.com/repos/{owner}/{repo}/invitations | GET | Query | JSON | 列出仓库邀请 |
| https://api.github.com/repos/{owner}/{repo}/invitations/{invitation_id} | DELETE | Query | JSON | 删除仓库邀请 |
| https://api.github.com/repos/{owner}/{repo}/invitations/{invitation_id} | PATCH | JSON | JSON | 更新仓库邀请 |
| https://api.github.com/repos/{owner}/{repo}/issue-types | GET | Query | JSON | 列出议题类型用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/keys | GET | Query | JSON | 列出部署密钥 |
| https://api.github.com/repos/{owner}/{repo}/keys | POST | JSON | JSON | 创建部署密钥 |
| https://api.github.com/repos/{owner}/{repo}/keys/{key_id} | DELETE | Query | JSON | 删除部署密钥 |
| https://api.github.com/repos/{owner}/{repo}/keys/{key_id} | GET | Query | JSON | 获取部署密钥 |
| https://api.github.com/repos/{owner}/{repo}/languages | GET | Query | JSON | 列出仓库语言 |
| https://api.github.com/repos/{owner}/{repo}/merge-upstream | POST | JSON | JSON | 同步 fork 分支含上游仓库 |
| https://api.github.com/repos/{owner}/{repo}/merges | POST | JSON | JSON | 合并分支 |
| https://api.github.com/repos/{owner}/{repo}/pages | DELETE | Query | JSON | 删除 GitHub Pages 站点 |
| https://api.github.com/repos/{owner}/{repo}/pages | GET | Query | JSON | 获取 GitHub Pages 站点 |
| https://api.github.com/repos/{owner}/{repo}/pages | POST | JSON | JSON | 创建 GitHub Pages 站点 |
| https://api.github.com/repos/{owner}/{repo}/pages | PUT | JSON | JSON | 更新信息关于 GitHub Pages 站点 |
| https://api.github.com/repos/{owner}/{repo}/pages/builds | GET | Query | JSON | 列出 GitHub Pages 构建 |
| https://api.github.com/repos/{owner}/{repo}/pages/builds | POST | Query | JSON | 请求 GitHub Pages 构建 |
| https://api.github.com/repos/{owner}/{repo}/pages/builds/latest | GET | Query | JSON | 获取最新 Pages 构建 |
| https://api.github.com/repos/{owner}/{repo}/pages/builds/{build_id} | GET | Query | JSON | 获取 GitHub Pages 构建 |
| https://api.github.com/repos/{owner}/{repo}/pages/deployments | POST | JSON | JSON | 创建 GitHub Pages 部署 |
| https://api.github.com/repos/{owner}/{repo}/pages/deployments/{pages_deployment_id} | GET | Query | JSON | 获取状态的 GitHub Pages 部署 |
| https://api.github.com/repos/{owner}/{repo}/pages/deployments/{pages_deployment_id}/cancel | POST | Query | JSON | 取消 GitHub Pages 部署 |
| https://api.github.com/repos/{owner}/{repo}/pages/health | GET | Query | JSON | 获取 DNS 健康检查用于 GitHub Pages |
| https://api.github.com/repos/{owner}/{repo}/private-vulnerability-reporting | DELETE | Query | JSON | 禁用私有漏洞报告用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/private-vulnerability-reporting | GET | Query | JSON | 检查是否私有漏洞报告是否已启用用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/private-vulnerability-reporting | PUT | Query | JSON | 启用私有漏洞报告用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/properties/values | GET | Query | JSON | 获取全部自定义属性值用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/properties/values | PATCH | JSON | JSON | 创建或更新自定义属性值用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/readme | GET | Query | JSON | 获取仓库 README |
| https://api.github.com/repos/{owner}/{repo}/readme/{dir} | GET | Query | JSON | 获取仓库 README 用于目录 |
| https://api.github.com/repos/{owner}/{repo}/releases | GET | Query | JSON | 列出发行版 |
| https://api.github.com/repos/{owner}/{repo}/releases | POST | JSON | JSON | 创建发行版 |
| https://api.github.com/repos/{owner}/{repo}/releases/assets/{asset_id} | DELETE | Query | JSON | 删除发行版资产 |
| https://api.github.com/repos/{owner}/{repo}/releases/assets/{asset_id} | GET | Query | JSON | 获取发行版资产 |
| https://api.github.com/repos/{owner}/{repo}/releases/assets/{asset_id} | PATCH | JSON | JSON | 更新发行版资产 |
| https://api.github.com/repos/{owner}/{repo}/releases/generate-notes | POST | JSON | JSON | 生成发行版备注内容用于发行版 |
| https://api.github.com/repos/{owner}/{repo}/releases/latest | GET | Query | JSON | 获取最新发行版 |
| https://api.github.com/repos/{owner}/{repo}/releases/tags/{tag} | GET | Query | JSON | 获取发行版按标签名称 |
| https://api.github.com/repos/{owner}/{repo}/releases/{release_id} | DELETE | Query | JSON | 删除发行版 |
| https://api.github.com/repos/{owner}/{repo}/releases/{release_id} | GET | Query | JSON | 获取发行版 |
| https://api.github.com/repos/{owner}/{repo}/releases/{release_id} | PATCH | JSON | JSON | 更新发行版 |
| https://api.github.com/repos/{owner}/{repo}/releases/{release_id}/assets | GET | Query | JSON | 列出发行版资产 |
| https://api.github.com/repos/{owner}/{repo}/releases/{release_id}/assets | POST | multipart | JSON | 上传发行版资产 |
| https://api.github.com/repos/{owner}/{repo}/rules/branches/{branch} | GET | Query | JSON | 获取规则用于分支 |
| https://api.github.com/repos/{owner}/{repo}/rulesets | GET | Query | JSON | 获取全部仓库规则集 |
| https://api.github.com/repos/{owner}/{repo}/rulesets | POST | JSON | JSON | 创建仓库规则集 |
| https://api.github.com/repos/{owner}/{repo}/rulesets/rule-suites | GET | Query | JSON | 列出仓库规则套件 |
| https://api.github.com/repos/{owner}/{repo}/rulesets/rule-suites/{rule_suite_id} | GET | Query | JSON | 获取仓库规则套件 |
| https://api.github.com/repos/{owner}/{repo}/rulesets/{ruleset_id} | DELETE | Query | JSON | 删除仓库规则集 |
| https://api.github.com/repos/{owner}/{repo}/rulesets/{ruleset_id} | GET | Query | JSON | 获取仓库规则集 |
| https://api.github.com/repos/{owner}/{repo}/rulesets/{ruleset_id} | PUT | JSON | JSON | 更新仓库规则集 |
| https://api.github.com/repos/{owner}/{repo}/rulesets/{ruleset_id}/history | GET | Query | JSON | 获取仓库规则集历史 |
| https://api.github.com/repos/{owner}/{repo}/rulesets/{ruleset_id}/history/{version_id} | GET | Query | JSON | 获取仓库规则集版本 |
| https://api.github.com/repos/{owner}/{repo}/stats/code_frequency | GET | Query | JSON | 获取每周提交活动 |
| https://api.github.com/repos/{owner}/{repo}/stats/commit_activity | GET | Query | JSON | 获取最近年的提交活动 |
| https://api.github.com/repos/{owner}/{repo}/stats/contributors | GET | Query | JSON | 获取全部贡献者提交活动 |
| https://api.github.com/repos/{owner}/{repo}/stats/participation | GET | Query | JSON | 获取每周提交统计 |
| https://api.github.com/repos/{owner}/{repo}/stats/punch_card | GET | Query | JSON | 获取每小时提交统计用于每个当日 |
| https://api.github.com/repos/{owner}/{repo}/statuses/{sha} | POST | JSON | JSON | 创建提交状态 |
| https://api.github.com/repos/{owner}/{repo}/tags | GET | Query | JSON | 列出仓库标签 |
| https://api.github.com/repos/{owner}/{repo}/tarball/{ref} | GET | Query | JSON | 下载仓库归档 (tar) |
| https://api.github.com/repos/{owner}/{repo}/teams | GET | Query | JSON | 列出仓库团队 |
| https://api.github.com/repos/{owner}/{repo}/topics | GET | Query | JSON | 获取全部仓库主题 |
| https://api.github.com/repos/{owner}/{repo}/topics | PUT | JSON | JSON | 替换全部仓库主题 |
| https://api.github.com/repos/{owner}/{repo}/traffic/clones | GET | Query | JSON | 获取仓库克隆 |
| https://api.github.com/repos/{owner}/{repo}/traffic/popular/paths | GET | Query | JSON | 获取热门推荐路径 |
| https://api.github.com/repos/{owner}/{repo}/traffic/popular/referrers | GET | Query | JSON | 获取热门推荐源 |
| https://api.github.com/repos/{owner}/{repo}/traffic/views | GET | Query | JSON | 获取页面查看 |
| https://api.github.com/repos/{owner}/{repo}/transfer | POST | JSON | JSON | 转移仓库 |
| https://api.github.com/repos/{owner}/{repo}/vulnerability-alerts | DELETE | Query | JSON | 禁用漏洞告警 |
| https://api.github.com/repos/{owner}/{repo}/vulnerability-alerts | GET | Query | JSON | 检查是否漏洞告警是否已启用用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/vulnerability-alerts | PUT | Query | JSON | 启用漏洞告警 |
| https://api.github.com/repos/{owner}/{repo}/zipball/{ref} | GET | Query | JSON | 下载仓库归档 (zip) |
| https://api.github.com/repos/{template_owner}/{template_repo}/generate | POST | JSON | JSON | 创建仓库使用模板 |
| https://api.github.com/repositories | GET | Query | JSON | 列出公开仓库 |
| https://api.github.com/user/repos | GET | Query | JSON | 列出仓库用于已认证用户 |
| https://api.github.com/user/repos | POST | JSON | JSON | 创建仓库用于已认证用户 |
| https://api.github.com/user/repository_invitations | GET | Query | JSON | 列出仓库邀请用于已认证用户 |
| https://api.github.com/user/repository_invitations/{invitation_id} | DELETE | Query | JSON | 拒绝仓库邀请 |
| https://api.github.com/user/repository_invitations/{invitation_id} | PATCH | Query | JSON | 接受仓库邀请 |
| https://api.github.com/users/{username}/repos | GET | Query | JSON | 列出仓库用于用户 |

## 拉取请求 Pull Requests（35 条）

> 官方分类标签 `pulls`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/repos/{owner}/{repo}/pulls | GET | Query | JSON | 列出拉取请求 |
| https://api.github.com/repos/{owner}/{repo}/pulls | POST | JSON | JSON | 创建拉取请求 |
| https://api.github.com/repos/{owner}/{repo}/pulls/comments | GET | Query | JSON | 列出审核评论内仓库 |
| https://api.github.com/repos/{owner}/{repo}/pulls/comments/{comment_id} | DELETE | Query | JSON | 删除审核评论用于拉取请求 |
| https://api.github.com/repos/{owner}/{repo}/pulls/comments/{comment_id} | GET | Query | JSON | 获取审核评论用于拉取请求 |
| https://api.github.com/repos/{owner}/{repo}/pulls/comments/{comment_id} | PATCH | JSON | JSON | 更新审核评论用于拉取请求 |
| https://api.github.com/repos/{owner}/{repo}/pulls/{pull_number} | GET | Query | JSON | 获取拉取请求 |
| https://api.github.com/repos/{owner}/{repo}/pulls/{pull_number} | PATCH | JSON | JSON | 更新拉取请求 |
| https://api.github.com/repos/{owner}/{repo}/pulls/{pull_number}/comments | GET | Query | JSON | 列出审核评论上拉取请求 |
| https://api.github.com/repos/{owner}/{repo}/pulls/{pull_number}/comments | POST | JSON | JSON | 创建审核评论用于拉取请求 |
| https://api.github.com/repos/{owner}/{repo}/pulls/{pull_number}/comments/{comment_id}/replies | POST | JSON | JSON | 创建回复用于审核评论 |
| https://api.github.com/repos/{owner}/{repo}/pulls/{pull_number}/commits | GET | Query | JSON | 列出提交上拉取请求 |
| https://api.github.com/repos/{owner}/{repo}/pulls/{pull_number}/files | GET | Query | JSON | 列出拉取请求文件 |
| https://api.github.com/repos/{owner}/{repo}/pulls/{pull_number}/merge | GET | Query | JSON | 检查是否拉取请求已已合并 |
| https://api.github.com/repos/{owner}/{repo}/pulls/{pull_number}/merge | PUT | JSON | JSON | 合并拉取请求 |
| https://api.github.com/repos/{owner}/{repo}/pulls/{pull_number}/merge-async | PUT | JSON | JSON | 合并拉取请求异步 |
| https://api.github.com/repos/{owner}/{repo}/pulls/{pull_number}/merge-async/{uuid} | GET | Query | JSON | 获取结果的异步合并 |
| https://api.github.com/repos/{owner}/{repo}/pulls/{pull_number}/requested_reviewers | DELETE | JSON | JSON | 移除已请求审核人来自拉取请求 |
| https://api.github.com/repos/{owner}/{repo}/pulls/{pull_number}/requested_reviewers | GET | Query | JSON | 获取全部已请求审核人用于拉取请求 |
| https://api.github.com/repos/{owner}/{repo}/pulls/{pull_number}/requested_reviewers | POST | JSON | JSON | 请求审核人用于拉取请求 |
| https://api.github.com/repos/{owner}/{repo}/pulls/{pull_number}/requested_reviewers/rerequest | POST | JSON | JSON | 重新请求审核人用于拉取请求 |
| https://api.github.com/repos/{owner}/{repo}/pulls/{pull_number}/reviews | GET | Query | JSON | 列出审核用于拉取请求 |
| https://api.github.com/repos/{owner}/{repo}/pulls/{pull_number}/reviews | POST | JSON | JSON | 创建审核用于拉取请求 |
| https://api.github.com/repos/{owner}/{repo}/pulls/{pull_number}/reviews/{review_id} | DELETE | Query | JSON | 删除待处理审核用于拉取请求 |
| https://api.github.com/repos/{owner}/{repo}/pulls/{pull_number}/reviews/{review_id} | GET | Query | JSON | 获取审核用于拉取请求 |
| https://api.github.com/repos/{owner}/{repo}/pulls/{pull_number}/reviews/{review_id} | PUT | JSON | JSON | 更新审核用于拉取请求 |
| https://api.github.com/repos/{owner}/{repo}/pulls/{pull_number}/reviews/{review_id}/comments | GET | Query | JSON | 列出评论用于拉取请求审核 |
| https://api.github.com/repos/{owner}/{repo}/pulls/{pull_number}/reviews/{review_id}/dismissals | PUT | JSON | JSON | 解除审核用于拉取请求 |
| https://api.github.com/repos/{owner}/{repo}/pulls/{pull_number}/reviews/{review_id}/events | POST | JSON | JSON | 提交审核用于拉取请求 |
| https://api.github.com/repos/{owner}/{repo}/pulls/{pull_number}/update-branch | PUT | JSON | JSON | 更新拉取请求分支 |
| https://api.github.com/repos/{owner}/{repo}/stacks | GET | Query | JSON | 列出拉取请求技术栈 |
| https://api.github.com/repos/{owner}/{repo}/stacks | POST | JSON | JSON | 创建拉取请求技术栈 |
| https://api.github.com/repos/{owner}/{repo}/stacks/{stack_number} | GET | Query | JSON | 获取拉取请求技术栈 |
| https://api.github.com/repos/{owner}/{repo}/stacks/{stack_number}/add | POST | JSON | JSON | 添加拉取请求给拉取请求技术栈 |
| https://api.github.com/repos/{owner}/{repo}/stacks/{stack_number}/unstack | POST | Query | JSON | 移除拉取请求来自拉取请求技术栈 |

## 议题 Issues（61 条）

> 官方分类标签 `issues`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/issues | GET | Query | JSON | 列出议题已指派给已认证用户 |
| https://api.github.com/orgs/{org}/issues | GET | Query | JSON | 列出组织议题已指派给已认证用户 |
| https://api.github.com/repos/{owner}/{repo}/assignees | GET | Query | JSON | 列出指派人 |
| https://api.github.com/repos/{owner}/{repo}/assignees/{assignee} | GET | Query | JSON | 检查是否用户可以已指派 |
| https://api.github.com/repos/{owner}/{repo}/issues | GET | Query | JSON | 列出仓库议题 |
| https://api.github.com/repos/{owner}/{repo}/issues | POST | JSON | JSON | 创建议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/comments | GET | Query | JSON | 列出议题评论用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/issues/comments/{comment_id} | DELETE | Query | JSON | 删除议题评论 |
| https://api.github.com/repos/{owner}/{repo}/issues/comments/{comment_id} | GET | Query | JSON | 获取议题评论 |
| https://api.github.com/repos/{owner}/{repo}/issues/comments/{comment_id} | PATCH | JSON | JSON | 更新议题评论 |
| https://api.github.com/repos/{owner}/{repo}/issues/comments/{comment_id}/pin | DELETE | Query | JSON | 取消置顶议题评论 |
| https://api.github.com/repos/{owner}/{repo}/issues/comments/{comment_id}/pin | PUT | Query | JSON | 置顶议题评论 |
| https://api.github.com/repos/{owner}/{repo}/issues/events | GET | Query | JSON | 列出议题事件用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/issues/events/{event_id} | GET | Query | JSON | 获取议题事件 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number} | GET | Query | JSON | 获取议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number} | PATCH | JSON | JSON | 更新议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/assignees | DELETE | JSON | JSON | 移除指派人来自议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/assignees | POST | JSON | JSON | 添加指派人给议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/assignees/{assignee} | GET | Query | JSON | 检查是否用户可以已指派给议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/comments | GET | Query | JSON | 列出议题评论 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/comments | POST | JSON | JSON | 创建议题评论 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/dependencies/blocked_by | GET | Query | JSON | 列出依赖议题是否已屏蔽按 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/dependencies/blocked_by | POST | JSON | JSON | 添加依赖议题是否已屏蔽按 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/dependencies/blocked_by/{issue_id} | DELETE | Query | JSON | 移除依赖议题是否已屏蔽按 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/dependencies/blocking | GET | Query | JSON | 列出依赖议题是否屏蔽 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/events | GET | Query | JSON | 列出议题事件 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/issue-field-values | GET | Query | JSON | 列出议题字段值用于议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/issue-field-values | POST | JSON | JSON | 添加议题字段值给议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/issue-field-values | PUT | JSON | JSON | 设置议题字段值用于议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/issue-field-values/{issue_field_id} | DELETE | Query | JSON | 删除议题字段值来自议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/labels | DELETE | Query | JSON | 移除全部标签来自议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/labels | GET | Query | JSON | 列出标签用于议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/labels | POST | JSON | JSON | 添加标签给议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/labels | PUT | JSON | JSON | 设置标签用于议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/labels/{name} | DELETE | Query | JSON | 移除标签来自议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/lock | DELETE | Query | JSON | 解锁议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/lock | PUT | JSON | JSON | 锁定议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/parent | GET | Query | JSON | 获取父议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/relates_to | GET | Query | JSON | 列出议题相关给议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/relates_to | POST | JSON | JSON | 添加相关议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/relates_to/{issue_id} | DELETE | Query | JSON | 移除相关议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/sub_issue | DELETE | JSON | JSON | 移除子议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/sub_issues | GET | Query | JSON | 列出子议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/sub_issues | POST | JSON | JSON | 添加子议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/sub_issues/priority | PATCH | JSON | JSON | 重新排序子议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/suggestions | GET | Query | JSON | 列出议题建议 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/suggestions/{suggestion_id}/approve | POST | Query | JSON | 批准议题建议 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/suggestions/{suggestion_id}/dismiss | POST | Query | JSON | 解除议题建议 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/timeline | GET | Query | JSON | 列出时间线事件用于议题 |
| https://api.github.com/repos/{owner}/{repo}/labels | GET | Query | JSON | 列出标签用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/labels | POST | JSON | JSON | 创建标签 |
| https://api.github.com/repos/{owner}/{repo}/labels/{name} | DELETE | Query | JSON | 删除标签 |
| https://api.github.com/repos/{owner}/{repo}/labels/{name} | GET | Query | JSON | 获取标签 |
| https://api.github.com/repos/{owner}/{repo}/labels/{name} | PATCH | JSON | JSON | 更新标签 |
| https://api.github.com/repos/{owner}/{repo}/milestones | GET | Query | JSON | 列出里程碑 |
| https://api.github.com/repos/{owner}/{repo}/milestones | POST | JSON | JSON | 创建里程碑 |
| https://api.github.com/repos/{owner}/{repo}/milestones/{milestone_number} | DELETE | Query | JSON | 删除里程碑 |
| https://api.github.com/repos/{owner}/{repo}/milestones/{milestone_number} | GET | Query | JSON | 获取里程碑 |
| https://api.github.com/repos/{owner}/{repo}/milestones/{milestone_number} | PATCH | JSON | JSON | 更新里程碑 |
| https://api.github.com/repos/{owner}/{repo}/milestones/{milestone_number}/labels | GET | Query | JSON | 列出标签用于议题内里程碑 |
| https://api.github.com/user/issues | GET | Query | JSON | 列出用户账户议题已指派给已认证用户 |

## 表情回应 Reactions（15 条）

> 官方分类标签 `reactions`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/repos/{owner}/{repo}/comments/{comment_id}/reactions | GET | Query | JSON | 列出回应用于提交评论 |
| https://api.github.com/repos/{owner}/{repo}/comments/{comment_id}/reactions | POST | JSON | JSON | 创建回应用于提交评论 |
| https://api.github.com/repos/{owner}/{repo}/comments/{comment_id}/reactions/{reaction_id} | DELETE | Query | JSON | 删除提交评论回应 |
| https://api.github.com/repos/{owner}/{repo}/issues/comments/{comment_id}/reactions | GET | Query | JSON | 列出回应用于议题评论 |
| https://api.github.com/repos/{owner}/{repo}/issues/comments/{comment_id}/reactions | POST | JSON | JSON | 创建回应用于议题评论 |
| https://api.github.com/repos/{owner}/{repo}/issues/comments/{comment_id}/reactions/{reaction_id} | DELETE | Query | JSON | 删除议题评论回应 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/reactions | GET | Query | JSON | 列出回应用于议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/reactions | POST | JSON | JSON | 创建回应用于议题 |
| https://api.github.com/repos/{owner}/{repo}/issues/{issue_number}/reactions/{reaction_id} | DELETE | Query | JSON | 删除议题回应 |
| https://api.github.com/repos/{owner}/{repo}/pulls/comments/{comment_id}/reactions | GET | Query | JSON | 列出回应用于拉取请求审核评论 |
| https://api.github.com/repos/{owner}/{repo}/pulls/comments/{comment_id}/reactions | POST | JSON | JSON | 创建回应用于拉取请求审核评论 |
| https://api.github.com/repos/{owner}/{repo}/pulls/comments/{comment_id}/reactions/{reaction_id} | DELETE | Query | JSON | 删除拉取请求评论回应 |
| https://api.github.com/repos/{owner}/{repo}/releases/{release_id}/reactions | GET | Query | JSON | 列出回应用于发行版 |
| https://api.github.com/repos/{owner}/{repo}/releases/{release_id}/reactions | POST | JSON | JSON | 创建回应用于发行版 |
| https://api.github.com/repos/{owner}/{repo}/releases/{release_id}/reactions/{reaction_id} | DELETE | Query | JSON | 删除发行版回应 |

## Git 数据 Git Data（13 条）

> 官方分类标签 `git`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/repos/{owner}/{repo}/git/blobs | POST | JSON | JSON | 创建数据块 |
| https://api.github.com/repos/{owner}/{repo}/git/blobs/{file_sha} | GET | Query | JSON | 获取数据块 |
| https://api.github.com/repos/{owner}/{repo}/git/commits | POST | JSON | JSON | 创建提交 |
| https://api.github.com/repos/{owner}/{repo}/git/commits/{commit_sha} | GET | Query | JSON | 获取提交对象 |
| https://api.github.com/repos/{owner}/{repo}/git/matching-refs/{ref} | GET | Query | JSON | 列出匹配引用 |
| https://api.github.com/repos/{owner}/{repo}/git/ref/{ref} | GET | Query | JSON | 获取引用 |
| https://api.github.com/repos/{owner}/{repo}/git/refs | POST | JSON | JSON | 创建引用 |
| https://api.github.com/repos/{owner}/{repo}/git/refs/{ref} | DELETE | Query | JSON | 删除引用 |
| https://api.github.com/repos/{owner}/{repo}/git/refs/{ref} | PATCH | JSON | JSON | 更新引用 |
| https://api.github.com/repos/{owner}/{repo}/git/tags | POST | JSON | JSON | 创建标签对象 |
| https://api.github.com/repos/{owner}/{repo}/git/tags/{tag_sha} | GET | Query | JSON | 获取标签 |
| https://api.github.com/repos/{owner}/{repo}/git/trees | POST | JSON | JSON | 创建树 |
| https://api.github.com/repos/{owner}/{repo}/git/trees/{tree_sha} | GET | Query | JSON | 获取树 |

## 检查 Checks（12 条）

> 官方分类标签 `checks`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/repos/{owner}/{repo}/check-runs | POST | JSON | JSON | 创建检查运行 |
| https://api.github.com/repos/{owner}/{repo}/check-runs/{check_run_id} | GET | Query | JSON | 获取检查运行 |
| https://api.github.com/repos/{owner}/{repo}/check-runs/{check_run_id} | PATCH | JSON | JSON | 更新检查运行 |
| https://api.github.com/repos/{owner}/{repo}/check-runs/{check_run_id}/annotations | GET | Query | JSON | 列出检查运行注释 |
| https://api.github.com/repos/{owner}/{repo}/check-runs/{check_run_id}/rerequest | POST | Query | JSON | 重新请求检查运行 |
| https://api.github.com/repos/{owner}/{repo}/check-suites | POST | JSON | JSON | 创建检查套件 |
| https://api.github.com/repos/{owner}/{repo}/check-suites/preferences | PATCH | JSON | JSON | 更新仓库偏好用于检查套件 |
| https://api.github.com/repos/{owner}/{repo}/check-suites/{check_suite_id} | GET | Query | JSON | 获取检查套件 |
| https://api.github.com/repos/{owner}/{repo}/check-suites/{check_suite_id}/check-runs | GET | Query | JSON | 列出检查运行内检查套件 |
| https://api.github.com/repos/{owner}/{repo}/check-suites/{check_suite_id}/rerequest | POST | Query | JSON | 重新请求检查套件 |
| https://api.github.com/repos/{owner}/{repo}/commits/{ref}/check-runs | GET | Query | JSON | 列出检查运行用于 Git 引用 |
| https://api.github.com/repos/{owner}/{repo}/commits/{ref}/check-suites | GET | Query | JSON | 列出检查套件用于 Git 引用 |

## 团队 Teams（32 条）

> 官方分类标签 `teams`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/orgs/{org}/teams | GET | Query | JSON | 列出团队 |
| https://api.github.com/orgs/{org}/teams | POST | JSON | JSON | 创建团队 |
| https://api.github.com/orgs/{org}/teams/{team_slug} | DELETE | Query | JSON | 删除团队 |
| https://api.github.com/orgs/{org}/teams/{team_slug} | GET | Query | JSON | 获取团队按名称 |
| https://api.github.com/orgs/{org}/teams/{team_slug} | PATCH | JSON | JSON | 更新团队 |
| https://api.github.com/orgs/{org}/teams/{team_slug}/invitations | GET | Query | JSON | 列出待处理团队邀请 |
| https://api.github.com/orgs/{org}/teams/{team_slug}/members | GET | Query | JSON | 列出团队成员 |
| https://api.github.com/orgs/{org}/teams/{team_slug}/memberships/{username} | DELETE | Query | JSON | 移除团队成员资格用于用户 |
| https://api.github.com/orgs/{org}/teams/{team_slug}/memberships/{username} | GET | Query | JSON | 获取团队成员资格用于用户 |
| https://api.github.com/orgs/{org}/teams/{team_slug}/memberships/{username} | PUT | JSON | JSON | 添加或更新团队成员资格用于用户 |
| https://api.github.com/orgs/{org}/teams/{team_slug}/repos | GET | Query | JSON | 列出团队仓库 |
| https://api.github.com/orgs/{org}/teams/{team_slug}/repos/{owner}/{repo} | DELETE | Query | JSON | 移除仓库来自团队 |
| https://api.github.com/orgs/{org}/teams/{team_slug}/repos/{owner}/{repo} | GET | Query | JSON | 检查团队权限用于仓库 |
| https://api.github.com/orgs/{org}/teams/{team_slug}/repos/{owner}/{repo} | PUT | JSON | JSON | 添加或更新团队仓库权限 |
| https://api.github.com/orgs/{org}/teams/{team_slug}/teams | GET | Query | JSON | 列出子团队 |
| https://api.github.com/teams/{team_id} | DELETE | Query | JSON | 【已废弃】删除团队 (旧版) |
| https://api.github.com/teams/{team_id} | GET | Query | JSON | 【已废弃】获取团队 (旧版) |
| https://api.github.com/teams/{team_id} | PATCH | JSON | JSON | 【已废弃】更新团队 (旧版) |
| https://api.github.com/teams/{team_id}/invitations | GET | Query | JSON | 【已废弃】列出待处理团队邀请 (旧版) |
| https://api.github.com/teams/{team_id}/members | GET | Query | JSON | 【已废弃】列出团队成员 (旧版) |
| https://api.github.com/teams/{team_id}/members/{username} | DELETE | Query | JSON | 【已废弃】移除团队成员 (旧版) |
| https://api.github.com/teams/{team_id}/members/{username} | GET | Query | JSON | 【已废弃】获取团队成员 (旧版) |
| https://api.github.com/teams/{team_id}/members/{username} | PUT | Query | JSON | 【已废弃】添加团队成员 (旧版) |
| https://api.github.com/teams/{team_id}/memberships/{username} | DELETE | Query | JSON | 【已废弃】移除团队成员资格用于用户 (旧版) |
| https://api.github.com/teams/{team_id}/memberships/{username} | GET | Query | JSON | 【已废弃】获取团队成员资格用于用户 (旧版) |
| https://api.github.com/teams/{team_id}/memberships/{username} | PUT | JSON | JSON | 【已废弃】添加或更新团队成员资格用于用户 (旧版) |
| https://api.github.com/teams/{team_id}/repos | GET | Query | JSON | 【已废弃】列出团队仓库 (旧版) |
| https://api.github.com/teams/{team_id}/repos/{owner}/{repo} | DELETE | Query | JSON | 【已废弃】移除仓库来自团队 (旧版) |
| https://api.github.com/teams/{team_id}/repos/{owner}/{repo} | GET | Query | JSON | 【已废弃】检查团队权限用于仓库 (旧版) |
| https://api.github.com/teams/{team_id}/repos/{owner}/{repo} | PUT | JSON | JSON | 【已废弃】添加或更新团队仓库权限 (旧版) |
| https://api.github.com/teams/{team_id}/teams | GET | Query | JSON | 【已废弃】列出子团队 (旧版) |
| https://api.github.com/user/teams | GET | Query | JSON | 列出团队用于已认证用户 |

## 组织 Organizations（116 条）

> 官方分类标签 `orgs`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/organizations | GET | Query | JSON | 列出组织 |
| https://api.github.com/orgs/{org} | DELETE | Query | JSON | 删除组织 |
| https://api.github.com/orgs/{org} | GET | Query | JSON | 获取组织 |
| https://api.github.com/orgs/{org} | PATCH | JSON | JSON | 更新组织 |
| https://api.github.com/orgs/{org}/artifacts/metadata/deployment-record | POST | JSON | JSON | 创建构件部署记录 |
| https://api.github.com/orgs/{org}/artifacts/metadata/deployment-record/cluster/{cluster} | POST | JSON | JSON | 设置集群部署记录 |
| https://api.github.com/orgs/{org}/artifacts/metadata/deployment-record/cluster/{cluster}/jobs | POST | JSON | JSON | 创建集群部署记录任务 |
| https://api.github.com/orgs/{org}/artifacts/metadata/deployment-record/cluster/{cluster}/jobs/{job_id} | GET | Query | JSON | 获取集群部署记录任务状态 |
| https://api.github.com/orgs/{org}/artifacts/metadata/storage-record | POST | JSON | JSON | 创建构件元数据存储记录 |
| https://api.github.com/orgs/{org}/artifacts/{subject_digest}/metadata/deployment-records | GET | Query | JSON | 列出构件部署记录 |
| https://api.github.com/orgs/{org}/artifacts/{subject_digest}/metadata/storage-records | GET | Query | JSON | 列出构件存储记录 |
| https://api.github.com/orgs/{org}/attestations/bulk-list | POST | JSON | JSON | 列出证明按批量主题摘要 |
| https://api.github.com/orgs/{org}/attestations/delete-request | POST | JSON | JSON | 删除证明内批量 |
| https://api.github.com/orgs/{org}/attestations/digest/{subject_digest} | DELETE | Query | JSON | 删除证明按主题摘要 |
| https://api.github.com/orgs/{org}/attestations/repositories | GET | Query | JSON | 列出证明仓库 |
| https://api.github.com/orgs/{org}/attestations/{attestation_id} | DELETE | Query | JSON | 删除证明按 ID |
| https://api.github.com/orgs/{org}/attestations/{subject_digest} | GET | Query | JSON | 列出证明 |
| https://api.github.com/orgs/{org}/blocks | GET | Query | JSON | 列出用户已屏蔽按组织 |
| https://api.github.com/orgs/{org}/blocks/{username} | DELETE | Query | JSON | 解除屏蔽用户来自组织 |
| https://api.github.com/orgs/{org}/blocks/{username} | GET | Query | JSON | 检查是否用户是否已屏蔽按组织 |
| https://api.github.com/orgs/{org}/blocks/{username} | PUT | Query | JSON | 屏蔽用户来自组织 |
| https://api.github.com/orgs/{org}/failed_invitations | GET | Query | JSON | 列出失败组织邀请 |
| https://api.github.com/orgs/{org}/hooks | GET | Query | JSON | 列出组织 webhooks |
| https://api.github.com/orgs/{org}/hooks | POST | JSON | JSON | 创建组织 webhook |
| https://api.github.com/orgs/{org}/hooks/{hook_id} | DELETE | Query | JSON | 删除组织 webhook |
| https://api.github.com/orgs/{org}/hooks/{hook_id} | GET | Query | JSON | 获取组织 webhook |
| https://api.github.com/orgs/{org}/hooks/{hook_id} | PATCH | JSON | JSON | 更新组织 webhook |
| https://api.github.com/orgs/{org}/hooks/{hook_id}/config | GET | Query | JSON | 获取 webhook 配置用于组织 |
| https://api.github.com/orgs/{org}/hooks/{hook_id}/config | PATCH | JSON | JSON | 更新 webhook 配置用于组织 |
| https://api.github.com/orgs/{org}/hooks/{hook_id}/deliveries | GET | Query | JSON | 列出投递用于组织 webhook |
| https://api.github.com/orgs/{org}/hooks/{hook_id}/deliveries/{delivery_id} | GET | Query | JSON | 获取 webhook 投递用于组织 webhook |
| https://api.github.com/orgs/{org}/hooks/{hook_id}/deliveries/{delivery_id}/attempts | POST | Query | JSON | 重新投递投递用于组织 webhook |
| https://api.github.com/orgs/{org}/hooks/{hook_id}/pings | POST | Query | JSON | Ping 组织 webhook |
| https://api.github.com/orgs/{org}/insights/api/route-stats/{actor_type}/{actor_id} | GET | Query | JSON | 获取路由统计按执行者 |
| https://api.github.com/orgs/{org}/insights/api/subject-stats | GET | Query | JSON | 获取主题统计 |
| https://api.github.com/orgs/{org}/insights/api/summary-stats | GET | Query | JSON | 获取摘要统计 |
| https://api.github.com/orgs/{org}/insights/api/summary-stats/users/{user_id} | GET | Query | JSON | 获取摘要统计按用户 |
| https://api.github.com/orgs/{org}/insights/api/summary-stats/{actor_type}/{actor_id} | GET | Query | JSON | 获取摘要统计按执行者 |
| https://api.github.com/orgs/{org}/insights/api/time-stats | GET | Query | JSON | 获取时间统计 |
| https://api.github.com/orgs/{org}/insights/api/time-stats/users/{user_id} | GET | Query | JSON | 获取时间统计按用户 |
| https://api.github.com/orgs/{org}/insights/api/time-stats/{actor_type}/{actor_id} | GET | Query | JSON | 获取时间统计按执行者 |
| https://api.github.com/orgs/{org}/insights/api/user-stats/{user_id} | GET | Query | JSON | 获取用户统计 |
| https://api.github.com/orgs/{org}/installations | GET | Query | JSON | 列出应用安装用于组织 |
| https://api.github.com/orgs/{org}/invitations | GET | Query | JSON | 列出待处理组织邀请 |
| https://api.github.com/orgs/{org}/invitations | POST | JSON | JSON | 创建组织邀请 |
| https://api.github.com/orgs/{org}/invitations/{invitation_id} | DELETE | Query | JSON | 取消组织邀请 |
| https://api.github.com/orgs/{org}/invitations/{invitation_id}/teams | GET | Query | JSON | 列出组织邀请团队 |
| https://api.github.com/orgs/{org}/issue-fields | GET | Query | JSON | 列出议题字段用于组织 |
| https://api.github.com/orgs/{org}/issue-fields | POST | JSON | JSON | 创建议题字段用于组织 |
| https://api.github.com/orgs/{org}/issue-fields/{issue_field_id} | DELETE | Query | JSON | 删除议题字段用于组织 |
| https://api.github.com/orgs/{org}/issue-fields/{issue_field_id} | PATCH | JSON | JSON | 更新议题字段用于组织 |
| https://api.github.com/orgs/{org}/issue-types | GET | Query | JSON | 列出议题类型用于组织 |
| https://api.github.com/orgs/{org}/issue-types | POST | JSON | JSON | 创建议题类型用于组织 |
| https://api.github.com/orgs/{org}/issue-types/{issue_type_id} | DELETE | Query | JSON | 删除议题类型用于组织 |
| https://api.github.com/orgs/{org}/issue-types/{issue_type_id} | PUT | JSON | JSON | 更新议题类型用于组织 |
| https://api.github.com/orgs/{org}/members | GET | Query | JSON | 列出组织成员 |
| https://api.github.com/orgs/{org}/members/{username} | DELETE | Query | JSON | 移除组织成员 |
| https://api.github.com/orgs/{org}/members/{username} | GET | Query | JSON | 检查组织成员资格用于用户 |
| https://api.github.com/orgs/{org}/memberships/{username} | DELETE | Query | JSON | 移除组织成员资格用于用户 |
| https://api.github.com/orgs/{org}/memberships/{username} | GET | Query | JSON | 获取组织成员资格用于用户 |
| https://api.github.com/orgs/{org}/memberships/{username} | PUT | JSON | JSON | 设置组织成员资格用于用户 |
| https://api.github.com/orgs/{org}/organization-roles | GET | Query | JSON | 获取全部组织角色用于组织 |
| https://api.github.com/orgs/{org}/organization-roles/teams/{team_slug} | DELETE | Query | JSON | 移除全部组织角色用于团队 |
| https://api.github.com/orgs/{org}/organization-roles/teams/{team_slug}/{role_id} | DELETE | Query | JSON | 移除组织角色来自团队 |
| https://api.github.com/orgs/{org}/organization-roles/teams/{team_slug}/{role_id} | PUT | Query | JSON | 指派组织角色给团队 |
| https://api.github.com/orgs/{org}/organization-roles/users/{username} | DELETE | Query | JSON | 移除全部组织角色用于用户 |
| https://api.github.com/orgs/{org}/organization-roles/users/{username}/{role_id} | DELETE | Query | JSON | 移除组织角色来自用户 |
| https://api.github.com/orgs/{org}/organization-roles/users/{username}/{role_id} | PUT | Query | JSON | 指派组织角色给用户 |
| https://api.github.com/orgs/{org}/organization-roles/{role_id} | GET | Query | JSON | 获取组织角色 |
| https://api.github.com/orgs/{org}/organization-roles/{role_id}/teams | GET | Query | JSON | 列出团队是否已指派给组织角色 |
| https://api.github.com/orgs/{org}/organization-roles/{role_id}/users | GET | Query | JSON | 列出用户是否已指派给组织角色 |
| https://api.github.com/orgs/{org}/outside_collaborators | GET | Query | JSON | 列出外部协作者用于组织 |
| https://api.github.com/orgs/{org}/outside_collaborators/{username} | DELETE | Query | JSON | 移除外部协作者来自组织 |
| https://api.github.com/orgs/{org}/outside_collaborators/{username} | PUT | JSON | JSON | 转换组织成员给外部协作者 |
| https://api.github.com/orgs/{org}/personal-access-token-requests | GET | Query | JSON | 列出请求给访问组织资源含细粒度个人访问令牌 |
| https://api.github.com/orgs/{org}/personal-access-token-requests | POST | JSON | JSON | 审核请求给访问组织资源含细粒度个人访问令牌 |
| https://api.github.com/orgs/{org}/personal-access-token-requests/{pat_request_id} | POST | JSON | JSON | 审核请求给访问组织资源含细粒度个人访问令牌 |
| https://api.github.com/orgs/{org}/personal-access-token-requests/{pat_request_id}/repositories | GET | Query | JSON | 列出仓库已请求给已访问按细粒度个人访问令牌 |
| https://api.github.com/orgs/{org}/personal-access-tokens | GET | Query | JSON | 列出细粒度个人访问令牌含访问给组织资源 |
| https://api.github.com/orgs/{org}/personal-access-tokens | POST | JSON | JSON | 更新访问给组织资源通过细粒度个人访问令牌 |
| https://api.github.com/orgs/{org}/personal-access-tokens/{pat_id} | POST | JSON | JSON | 更新访问细粒度个人访问令牌给组织资源 |
| https://api.github.com/orgs/{org}/personal-access-tokens/{pat_id}/repositories | GET | Query | JSON | 列出仓库细粒度个人访问令牌访问给 |
| https://api.github.com/orgs/{org}/properties/installations | GET | Query | JSON | 获取已注册应用安装用于外部自定义属性 |
| https://api.github.com/orgs/{org}/properties/installations | POST | JSON | JSON | 注册应用安装用于外部自定义属性 |
| https://api.github.com/orgs/{org}/properties/installations/schema | GET | Query | JSON | 获取全部外部自定义属性用于 GitHub 应用安装内组织 |
| https://api.github.com/orgs/{org}/properties/installations/values | PATCH | JSON | JSON | 创建或更新外部自定义属性值用于组织仓库 |
| https://api.github.com/orgs/{org}/properties/installations/values/{property_name} | DELETE | Query | JSON | 移除全部外部自定义属性值用于属性跨越全部组织仓库 |
| https://api.github.com/orgs/{org}/properties/installations/values/{property_name} | PATCH | JSON | JSON | 创建或更新外部自定义属性值用于属性跨越组织仓库 |
| https://api.github.com/orgs/{org}/properties/schema | GET | Query | JSON | 获取全部自定义属性用于组织 |
| https://api.github.com/orgs/{org}/properties/schema | PATCH | JSON | JSON | 创建或更新自定义属性用于组织 |
| https://api.github.com/orgs/{org}/properties/schema/{custom_property_name} | DELETE | Query | JSON | 移除自定义属性用于组织 |
| https://api.github.com/orgs/{org}/properties/schema/{custom_property_name} | GET | Query | JSON | 获取自定义属性用于组织 |
| https://api.github.com/orgs/{org}/properties/schema/{custom_property_name} | PUT | JSON | JSON | 创建或更新自定义属性用于组织 |
| https://api.github.com/orgs/{org}/properties/values | GET | Query | JSON | 列出自定义属性值用于组织仓库 |
| https://api.github.com/orgs/{org}/properties/values | PATCH | JSON | JSON | 创建或更新自定义属性值用于组织仓库 |
| https://api.github.com/orgs/{org}/public_members | GET | Query | JSON | 列出公开组织成员 |
| https://api.github.com/orgs/{org}/public_members/{username} | DELETE | Query | JSON | 移除公开组织成员资格用于已认证用户 |
| https://api.github.com/orgs/{org}/public_members/{username} | GET | Query | JSON | 检查公开组织成员资格用于用户 |
| https://api.github.com/orgs/{org}/public_members/{username} | PUT | Query | JSON | 设置公开组织成员资格用于已认证用户 |
| https://api.github.com/orgs/{org}/rulesets/{ruleset_id}/history | GET | Query | JSON | 获取组织规则集历史 |
| https://api.github.com/orgs/{org}/rulesets/{ruleset_id}/history/{version_id} | GET | Query | JSON | 获取组织规则集版本 |
| https://api.github.com/orgs/{org}/security-managers | GET | Query | JSON | 【已废弃】列出安全管理器团队 |
| https://api.github.com/orgs/{org}/security-managers/teams/{team_slug} | DELETE | Query | JSON | 【已废弃】移除安全管理器团队 |
| https://api.github.com/orgs/{org}/security-managers/teams/{team_slug} | PUT | Query | JSON | 【已废弃】添加安全管理器团队 |
| https://api.github.com/orgs/{org}/settings/immutable-releases | GET | Query | JSON | 获取不可变发行版设置用于组织 |
| https://api.github.com/orgs/{org}/settings/immutable-releases | PUT | JSON | JSON | 设置不可变发行版设置用于组织 |
| https://api.github.com/orgs/{org}/settings/immutable-releases/repositories | GET | Query | JSON | 列出已选仓库用于不可变发行版执行 |
| https://api.github.com/orgs/{org}/settings/immutable-releases/repositories | PUT | JSON | JSON | 设置已选仓库用于不可变发行版执行 |
| https://api.github.com/orgs/{org}/settings/immutable-releases/repositories/{repository_id} | DELETE | Query | JSON | 禁用已选仓库用于不可变发行版内组织 |
| https://api.github.com/orgs/{org}/settings/immutable-releases/repositories/{repository_id} | PUT | Query | JSON | 启用已选仓库用于不可变发行版内组织 |
| https://api.github.com/orgs/{org}/{security_product}/{enablement} | POST | JSON | JSON | 【已废弃】启用或禁用安全功能用于组织 |
| https://api.github.com/user/memberships/orgs | GET | Query | JSON | 列出组织成员资格用于已认证用户 |
| https://api.github.com/user/memberships/orgs/{org} | GET | Query | JSON | 获取组织成员资格用于已认证用户 |
| https://api.github.com/user/memberships/orgs/{org} | PATCH | JSON | JSON | 更新组织成员资格用于已认证用户 |
| https://api.github.com/user/orgs | GET | Query | JSON | 列出组织用于已认证用户 |
| https://api.github.com/users/{username}/orgs | GET | Query | JSON | 列出组织用于用户 |

## 用户 Users（47 条）

> 官方分类标签 `users`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/user | GET | Query | JSON | 获取已认证用户 |
| https://api.github.com/user | PATCH | JSON | JSON | 更新已认证用户 |
| https://api.github.com/user/blocks | GET | Query | JSON | 列出用户已屏蔽按已认证用户 |
| https://api.github.com/user/blocks/{username} | DELETE | Query | JSON | 解除屏蔽用户 |
| https://api.github.com/user/blocks/{username} | GET | Query | JSON | 检查是否用户是否已屏蔽按已认证用户 |
| https://api.github.com/user/blocks/{username} | PUT | Query | JSON | 屏蔽用户 |
| https://api.github.com/user/email/visibility | PATCH | JSON | JSON | 设置主要邮箱可见性用于已认证用户 |
| https://api.github.com/user/emails | DELETE | JSON | JSON | 删除邮箱地址用于已认证用户 |
| https://api.github.com/user/emails | GET | Query | JSON | 列出邮箱地址用于已认证用户 |
| https://api.github.com/user/emails | POST | JSON | JSON | 添加邮箱地址用于已认证用户 |
| https://api.github.com/user/followers | GET | Query | JSON | 列出粉丝的已认证用户 |
| https://api.github.com/user/following | GET | Query | JSON | 列出人们已认证用户关注 |
| https://api.github.com/user/following/{username} | DELETE | Query | JSON | 取消关注用户 |
| https://api.github.com/user/following/{username} | GET | Query | JSON | 检查是否个人是否已关注按已认证用户 |
| https://api.github.com/user/following/{username} | PUT | Query | JSON | 关注用户 |
| https://api.github.com/user/gpg_keys | GET | Query | JSON | 列出 GPG 密钥用于已认证用户 |
| https://api.github.com/user/gpg_keys | POST | JSON | JSON | 创建 GPG 密钥用于已认证用户 |
| https://api.github.com/user/gpg_keys/{gpg_key_id} | DELETE | Query | JSON | 删除 GPG 密钥用于已认证用户 |
| https://api.github.com/user/gpg_keys/{gpg_key_id} | GET | Query | JSON | 获取 GPG 密钥用于已认证用户 |
| https://api.github.com/user/keys | GET | Query | JSON | 列出公开 SSH 密钥用于已认证用户 |
| https://api.github.com/user/keys | POST | JSON | JSON | 创建公开 SSH 密钥用于已认证用户 |
| https://api.github.com/user/keys/{key_id} | DELETE | Query | JSON | 删除公开 SSH 密钥用于已认证用户 |
| https://api.github.com/user/keys/{key_id} | GET | Query | JSON | 获取公开 SSH 密钥用于已认证用户 |
| https://api.github.com/user/public_emails | GET | Query | JSON | 列出公开邮箱地址用于已认证用户 |
| https://api.github.com/user/social_accounts | DELETE | JSON | JSON | 删除社交账户用于已认证用户 |
| https://api.github.com/user/social_accounts | GET | Query | JSON | 列出社交账户用于已认证用户 |
| https://api.github.com/user/social_accounts | POST | JSON | JSON | 添加社交账户用于已认证用户 |
| https://api.github.com/user/ssh_signing_keys | GET | Query | JSON | 列出 SSH 签名密钥用于已认证用户 |
| https://api.github.com/user/ssh_signing_keys | POST | JSON | JSON | 创建 SSH 签名密钥用于已认证用户 |
| https://api.github.com/user/ssh_signing_keys/{ssh_signing_key_id} | DELETE | Query | JSON | 删除 SSH 签名密钥用于已认证用户 |
| https://api.github.com/user/ssh_signing_keys/{ssh_signing_key_id} | GET | Query | JSON | 获取 SSH 签名密钥用于已认证用户 |
| https://api.github.com/user/{account_id} | GET | Query | JSON | 获取用户使用其 ID |
| https://api.github.com/users | GET | Query | JSON | 列出用户 |
| https://api.github.com/users/{username} | GET | Query | JSON | 获取用户 |
| https://api.github.com/users/{username}/attestations/bulk-list | POST | JSON | JSON | 列出证明按批量主题摘要 |
| https://api.github.com/users/{username}/attestations/delete-request | POST | JSON | JSON | 删除证明内批量 |
| https://api.github.com/users/{username}/attestations/digest/{subject_digest} | DELETE | Query | JSON | 删除证明按主题摘要 |
| https://api.github.com/users/{username}/attestations/{attestation_id} | DELETE | Query | JSON | 删除证明按 ID |
| https://api.github.com/users/{username}/attestations/{subject_digest} | GET | Query | JSON | 列出证明 |
| https://api.github.com/users/{username}/followers | GET | Query | JSON | 列出粉丝的用户 |
| https://api.github.com/users/{username}/following | GET | Query | JSON | 列出人们用户关注 |
| https://api.github.com/users/{username}/following/{target_user} | GET | Query | JSON | 检查是否用户关注另一个用户 |
| https://api.github.com/users/{username}/gpg_keys | GET | Query | JSON | 列出 GPG 密钥用于用户 |
| https://api.github.com/users/{username}/hovercard | GET | Query | JSON | 获取上下文信息用于用户 |
| https://api.github.com/users/{username}/keys | GET | Query | JSON | 列出公开密钥用于用户 |
| https://api.github.com/users/{username}/social_accounts | GET | Query | JSON | 列出社交账户用于用户 |
| https://api.github.com/users/{username}/ssh_signing_keys | GET | Query | JSON | 列出 SSH 签名密钥用于用户 |

## GitHub Apps 应用（37 条）

> 官方分类标签 `apps`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/app | GET | Query | JSON | 获取已认证应用 |
| https://api.github.com/app-manifests/{code}/conversions | POST | Query | JSON | 创建 GitHub 应用来自 manifest |
| https://api.github.com/app/hook/config | GET | Query | JSON | 获取 webhook 配置用于应用 |
| https://api.github.com/app/hook/config | PATCH | JSON | JSON | 更新 webhook 配置用于应用 |
| https://api.github.com/app/hook/deliveries | GET | Query | JSON | 列出投递用于应用 webhook |
| https://api.github.com/app/hook/deliveries/{delivery_id} | GET | Query | JSON | 获取投递用于应用 webhook |
| https://api.github.com/app/hook/deliveries/{delivery_id}/attempts | POST | Query | JSON | 重新投递投递用于应用 webhook |
| https://api.github.com/app/installation-requests | GET | Query | JSON | 列出安装请求用于已认证应用 |
| https://api.github.com/app/installations | GET | Query | JSON | 列出安装用于已认证应用 |
| https://api.github.com/app/installations/{installation_id} | DELETE | Query | JSON | 删除安装用于已认证应用 |
| https://api.github.com/app/installations/{installation_id} | GET | Query | JSON | 获取安装用于已认证应用 |
| https://api.github.com/app/installations/{installation_id}/access_tokens | POST | JSON | JSON | 创建安装访问令牌用于应用 |
| https://api.github.com/app/installations/{installation_id}/suspended | DELETE | Query | JSON | 取消暂停应用安装 |
| https://api.github.com/app/installations/{installation_id}/suspended | PUT | Query | JSON | 暂停应用安装 |
| https://api.github.com/applications/{client_id}/grant | DELETE | JSON | JSON | 删除应用授权 |
| https://api.github.com/applications/{client_id}/token | DELETE | JSON | JSON | 删除应用令牌 |
| https://api.github.com/applications/{client_id}/token | PATCH | JSON | JSON | 重置令牌 |
| https://api.github.com/applications/{client_id}/token | POST | JSON | JSON | 检查令牌 |
| https://api.github.com/applications/{client_id}/token/scoped | POST | JSON | JSON | 创建受限范围访问令牌 |
| https://api.github.com/apps/{app_slug} | GET | Query | JSON | 获取应用 |
| https://api.github.com/installation/repositories | GET | Query | JSON | 列出仓库可访问给应用安装 |
| https://api.github.com/installation/token | DELETE | Query | JSON | 撤销安装访问令牌 |
| https://api.github.com/marketplace_listing/accounts/{account_id} | GET | Query | JSON | 获取订阅套餐用于账户 |
| https://api.github.com/marketplace_listing/plans | GET | Query | JSON | 列出套餐 |
| https://api.github.com/marketplace_listing/plans/{plan_id}/accounts | GET | Query | JSON | 列出账户用于套餐 |
| https://api.github.com/marketplace_listing/stubbed/accounts/{account_id} | GET | Query | JSON | 获取订阅套餐用于账户 (占位) |
| https://api.github.com/marketplace_listing/stubbed/plans | GET | Query | JSON | 列出套餐 (占位) |
| https://api.github.com/marketplace_listing/stubbed/plans/{plan_id}/accounts | GET | Query | JSON | 列出账户用于套餐 (占位) |
| https://api.github.com/orgs/{org}/installation | GET | Query | JSON | 获取组织安装用于已认证应用 |
| https://api.github.com/repos/{owner}/{repo}/installation | GET | Query | JSON | 获取仓库安装用于已认证应用 |
| https://api.github.com/user/installations | GET | Query | JSON | 列出应用安装可访问给用户访问令牌 |
| https://api.github.com/user/installations/{installation_id}/repositories | GET | Query | JSON | 列出仓库可访问给用户访问令牌 |
| https://api.github.com/user/installations/{installation_id}/repositories/{repository_id} | DELETE | Query | JSON | 移除仓库来自应用安装 |
| https://api.github.com/user/installations/{installation_id}/repositories/{repository_id} | PUT | Query | JSON | 添加仓库给应用安装 |
| https://api.github.com/user/marketplace_purchases | GET | Query | JSON | 列出订阅用于已认证用户 |
| https://api.github.com/user/marketplace_purchases/stubbed | GET | Query | JSON | 列出订阅用于已认证用户 (占位) |
| https://api.github.com/users/{username}/installation | GET | Query | JSON | 获取用户安装用于已认证应用 |

## 活动与通知 Activity（34 条）

> 官方分类标签 `activity`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/events | GET | Query | JSON | 列出公开事件 |
| https://api.github.com/feeds | GET | Query | JSON | 获取信息流 |
| https://api.github.com/networks/{owner}/{repo}/events | GET | Query | JSON | 列出公开事件用于网络的仓库 |
| https://api.github.com/notifications | GET | Query | JSON | 列出通知用于已认证用户 |
| https://api.github.com/notifications | PUT | JSON | JSON | 标记通知作为已读 |
| https://api.github.com/notifications/threads/{thread_id} | DELETE | Query | JSON | 标记讨论线作为完成 |
| https://api.github.com/notifications/threads/{thread_id} | GET | Query | JSON | 获取讨论线 |
| https://api.github.com/notifications/threads/{thread_id} | PATCH | Query | JSON | 标记讨论线作为已读 |
| https://api.github.com/notifications/threads/{thread_id}/subscription | DELETE | Query | JSON | 删除讨论线订阅 |
| https://api.github.com/notifications/threads/{thread_id}/subscription | GET | Query | JSON | 获取讨论线订阅用于已认证用户 |
| https://api.github.com/notifications/threads/{thread_id}/subscription | PUT | JSON | JSON | 设置讨论线订阅 |
| https://api.github.com/orgs/{org}/events | GET | Query | JSON | 列出公开组织事件 |
| https://api.github.com/repos/{owner}/{repo}/events | GET | Query | JSON | 列出仓库事件 |
| https://api.github.com/repos/{owner}/{repo}/notifications | GET | Query | JSON | 列出仓库通知用于已认证用户 |
| https://api.github.com/repos/{owner}/{repo}/notifications | PUT | JSON | JSON | 标记仓库通知作为已读 |
| https://api.github.com/repos/{owner}/{repo}/stargazers | GET | Query | JSON | 列出星标者 |
| https://api.github.com/repos/{owner}/{repo}/stargazers/count | GET | Query | JSON | 获取星标者统计 |
| https://api.github.com/repos/{owner}/{repo}/stargazers/history | GET | Query | JSON | 获取仓库加星标历史 |
| https://api.github.com/repos/{owner}/{repo}/subscribers | GET | Query | JSON | 列出关注者 |
| https://api.github.com/repos/{owner}/{repo}/subscription | DELETE | Query | JSON | 删除仓库订阅 |
| https://api.github.com/repos/{owner}/{repo}/subscription | GET | Query | JSON | 获取仓库订阅 |
| https://api.github.com/repos/{owner}/{repo}/subscription | PUT | JSON | JSON | 设置仓库订阅 |
| https://api.github.com/user/starred | GET | Query | JSON | 列出仓库已加星标按已认证用户 |
| https://api.github.com/user/starred/{owner}/{repo} | DELETE | Query | JSON | 取消加星标仓库用于已认证用户 |
| https://api.github.com/user/starred/{owner}/{repo} | GET | Query | JSON | 检查是否仓库是否已加星标按已认证用户 |
| https://api.github.com/user/starred/{owner}/{repo} | PUT | Query | JSON | 加星标仓库用于已认证用户 |
| https://api.github.com/user/subscriptions | GET | Query | JSON | 列出仓库已观看按已认证用户 |
| https://api.github.com/users/{username}/events | GET | Query | JSON | 列出事件用于已认证用户 |
| https://api.github.com/users/{username}/events/orgs/{org} | GET | Query | JSON | 列出组织事件用于已认证用户 |
| https://api.github.com/users/{username}/events/public | GET | Query | JSON | 列出公开事件用于用户 |
| https://api.github.com/users/{username}/received_events | GET | Query | JSON | 列出事件已接收按已认证用户 |
| https://api.github.com/users/{username}/received_events/public | GET | Query | JSON | 列出公开事件已接收按用户 |
| https://api.github.com/users/{username}/starred | GET | Query | JSON | 列出仓库已加星标按用户 |
| https://api.github.com/users/{username}/subscriptions | GET | Query | JSON | 列出仓库已观看按用户 |

## Gist 代码片段（20 条）

> 官方分类标签 `gists`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/gists | GET | Query | JSON | 列出 gists 用于已认证用户 |
| https://api.github.com/gists | POST | JSON | JSON | 创建 gist |
| https://api.github.com/gists/public | GET | Query | JSON | 列出公开 gists |
| https://api.github.com/gists/starred | GET | Query | JSON | 列出已加星标 gists |
| https://api.github.com/gists/{gist_id} | DELETE | Query | JSON | 删除 gist |
| https://api.github.com/gists/{gist_id} | GET | Query | JSON | 获取 gist |
| https://api.github.com/gists/{gist_id} | PATCH | JSON | JSON | 更新 gist |
| https://api.github.com/gists/{gist_id}/comments | GET | Query | JSON | 列出 gist 评论 |
| https://api.github.com/gists/{gist_id}/comments | POST | JSON | JSON | 创建 gist 评论 |
| https://api.github.com/gists/{gist_id}/comments/{comment_id} | DELETE | Query | JSON | 删除 gist 评论 |
| https://api.github.com/gists/{gist_id}/comments/{comment_id} | GET | Query | JSON | 获取 gist 评论 |
| https://api.github.com/gists/{gist_id}/comments/{comment_id} | PATCH | JSON | JSON | 更新 gist 评论 |
| https://api.github.com/gists/{gist_id}/commits | GET | Query | JSON | 列出 gist 提交 |
| https://api.github.com/gists/{gist_id}/forks | GET | Query | JSON | 列出 gist forks |
| https://api.github.com/gists/{gist_id}/forks | POST | Query | JSON | Fork gist |
| https://api.github.com/gists/{gist_id}/star | DELETE | Query | JSON | 取消加星标 gist |
| https://api.github.com/gists/{gist_id}/star | GET | Query | JSON | 检查是否 gist 是否已加星标 |
| https://api.github.com/gists/{gist_id}/star | PUT | Query | JSON | 加星标 gist |
| https://api.github.com/gists/{gist_id}/{sha} | GET | Query | JSON | 获取 gist 修订 |
| https://api.github.com/users/{username}/gists | GET | Query | JSON | 列出 gists 用于用户 |

## Packages 软件包（27 条）

> 官方分类标签 `packages`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/orgs/{org}/docker/conflicts | GET | Query | JSON | 获取列出的冲突软件包期间 Docker 迁移用于组织 |
| https://api.github.com/orgs/{org}/packages | GET | Query | JSON | 列出软件包用于组织 |
| https://api.github.com/orgs/{org}/packages/{package_type}/{package_name} | DELETE | Query | JSON | 删除软件包用于组织 |
| https://api.github.com/orgs/{org}/packages/{package_type}/{package_name} | GET | Query | JSON | 获取软件包用于组织 |
| https://api.github.com/orgs/{org}/packages/{package_type}/{package_name}/restore | POST | Query | JSON | 还原软件包用于组织 |
| https://api.github.com/orgs/{org}/packages/{package_type}/{package_name}/versions | GET | Query | JSON | 列出软件包版本用于软件包所属按组织 |
| https://api.github.com/orgs/{org}/packages/{package_type}/{package_name}/versions/{package_version_id} | DELETE | Query | JSON | 删除软件包版本用于组织 |
| https://api.github.com/orgs/{org}/packages/{package_type}/{package_name}/versions/{package_version_id} | GET | Query | JSON | 获取软件包版本用于组织 |
| https://api.github.com/orgs/{org}/packages/{package_type}/{package_name}/versions/{package_version_id}/restore | POST | Query | JSON | 还原软件包版本用于组织 |
| https://api.github.com/user/docker/conflicts | GET | Query | JSON | 获取列出的冲突软件包期间 Docker 迁移用于已认证用户 |
| https://api.github.com/user/packages | GET | Query | JSON | 列出软件包用于已认证用户的命名空间 |
| https://api.github.com/user/packages/{package_type}/{package_name} | DELETE | Query | JSON | 删除软件包用于已认证用户 |
| https://api.github.com/user/packages/{package_type}/{package_name} | GET | Query | JSON | 获取软件包用于已认证用户 |
| https://api.github.com/user/packages/{package_type}/{package_name}/restore | POST | Query | JSON | 还原软件包用于已认证用户 |
| https://api.github.com/user/packages/{package_type}/{package_name}/versions | GET | Query | JSON | 列出软件包版本用于软件包所属按已认证用户 |
| https://api.github.com/user/packages/{package_type}/{package_name}/versions/{package_version_id} | DELETE | Query | JSON | 删除软件包版本用于已认证用户 |
| https://api.github.com/user/packages/{package_type}/{package_name}/versions/{package_version_id} | GET | Query | JSON | 获取软件包版本用于已认证用户 |
| https://api.github.com/user/packages/{package_type}/{package_name}/versions/{package_version_id}/restore | POST | Query | JSON | 还原软件包版本用于已认证用户 |
| https://api.github.com/users/{username}/docker/conflicts | GET | Query | JSON | 获取列出的冲突软件包期间 Docker 迁移用于用户 |
| https://api.github.com/users/{username}/packages | GET | Query | JSON | 列出软件包用于用户 |
| https://api.github.com/users/{username}/packages/{package_type}/{package_name} | DELETE | Query | JSON | 删除软件包用于用户 |
| https://api.github.com/users/{username}/packages/{package_type}/{package_name} | GET | Query | JSON | 获取软件包用于用户 |
| https://api.github.com/users/{username}/packages/{package_type}/{package_name}/restore | POST | Query | JSON | 还原软件包用于用户 |
| https://api.github.com/users/{username}/packages/{package_type}/{package_name}/versions | GET | Query | JSON | 列出软件包版本用于软件包所属按用户 |
| https://api.github.com/users/{username}/packages/{package_type}/{package_name}/versions/{package_version_id} | DELETE | Query | JSON | 删除软件包版本用于用户 |
| https://api.github.com/users/{username}/packages/{package_type}/{package_name}/versions/{package_version_id} | GET | Query | JSON | 获取软件包版本用于用户 |
| https://api.github.com/users/{username}/packages/{package_type}/{package_name}/versions/{package_version_id}/restore | POST | Query | JSON | 还原软件包版本用于用户 |

## Projects 项目（26 条）

> 官方分类标签 `projects`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/orgs/{org}/projectsV2 | GET | Query | JSON | 列出项目用于组织 |
| https://api.github.com/orgs/{org}/projectsV2/{project_number} | GET | Query | JSON | 获取项目用于组织 |
| https://api.github.com/orgs/{org}/projectsV2/{project_number}/drafts | POST | JSON | JSON | 创建草稿条目用于组织所属项目 |
| https://api.github.com/orgs/{org}/projectsV2/{project_number}/fields | GET | Query | JSON | 列出项目字段用于组织 |
| https://api.github.com/orgs/{org}/projectsV2/{project_number}/fields | POST | JSON | JSON | 添加字段给组织所属项目. |
| https://api.github.com/orgs/{org}/projectsV2/{project_number}/fields/{field_id} | GET | Query | JSON | 获取项目字段用于组织 |
| https://api.github.com/orgs/{org}/projectsV2/{project_number}/items | GET | Query | JSON | 列出条目用于组织所属项目 |
| https://api.github.com/orgs/{org}/projectsV2/{project_number}/items | POST | JSON | JSON | 添加条目给组织所属项目 |
| https://api.github.com/orgs/{org}/projectsV2/{project_number}/items/{item_id} | DELETE | Query | JSON | 删除项目条目用于组织 |
| https://api.github.com/orgs/{org}/projectsV2/{project_number}/items/{item_id} | GET | Query | JSON | 获取条目用于组织所属项目 |
| https://api.github.com/orgs/{org}/projectsV2/{project_number}/items/{item_id} | PATCH | JSON | JSON | 更新项目条目用于组织 |
| https://api.github.com/orgs/{org}/projectsV2/{project_number}/views | POST | JSON | JSON | 创建查看用于组织所属项目 |
| https://api.github.com/orgs/{org}/projectsV2/{project_number}/views/{view_number}/items | GET | Query | JSON | 列出条目用于组织项目查看 |
| https://api.github.com/user/{user_id}/projectsV2/{project_number}/drafts | POST | JSON | JSON | 创建草稿条目用于用户所属项目 |
| https://api.github.com/users/{user_id}/projectsV2/{project_number}/views | POST | JSON | JSON | 创建查看用于用户所属项目 |
| https://api.github.com/users/{username}/projectsV2 | GET | Query | JSON | 列出项目用于用户 |
| https://api.github.com/users/{username}/projectsV2/{project_number} | GET | Query | JSON | 获取项目用于用户 |
| https://api.github.com/users/{username}/projectsV2/{project_number}/fields | GET | Query | JSON | 列出项目字段用于用户 |
| https://api.github.com/users/{username}/projectsV2/{project_number}/fields | POST | JSON | JSON | 添加字段给用户所属项目 |
| https://api.github.com/users/{username}/projectsV2/{project_number}/fields/{field_id} | GET | Query | JSON | 获取项目字段用于用户 |
| https://api.github.com/users/{username}/projectsV2/{project_number}/items | GET | Query | JSON | 列出条目用于用户所属项目 |
| https://api.github.com/users/{username}/projectsV2/{project_number}/items | POST | JSON | JSON | 添加条目给用户所属项目 |
| https://api.github.com/users/{username}/projectsV2/{project_number}/items/{item_id} | DELETE | Query | JSON | 删除项目条目用于用户 |
| https://api.github.com/users/{username}/projectsV2/{project_number}/items/{item_id} | GET | Query | JSON | 获取条目用于用户所属项目 |
| https://api.github.com/users/{username}/projectsV2/{project_number}/items/{item_id} | PATCH | JSON | JSON | 更新项目条目用于用户 |
| https://api.github.com/users/{username}/projectsV2/{project_number}/views/{view_number}/items | GET | Query | JSON | 列出条目用于用户项目查看 |

## Codespaces 开发环境（48 条）

> 官方分类标签 `codespaces`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/orgs/{org}/codespaces | GET | Query | JSON | 列出 codespaces 用于组织 |
| https://api.github.com/orgs/{org}/codespaces/access | PUT | JSON | JSON | 【已废弃】管理访问控制用于组织 codespaces |
| https://api.github.com/orgs/{org}/codespaces/access/selected_users | DELETE | JSON | JSON | 【已废弃】移除用户来自 Codespaces 访问用于组织 |
| https://api.github.com/orgs/{org}/codespaces/access/selected_users | POST | JSON | JSON | 【已废弃】添加用户给 Codespaces 访问用于组织 |
| https://api.github.com/orgs/{org}/codespaces/secrets | GET | Query | JSON | 列出组织密钥 |
| https://api.github.com/orgs/{org}/codespaces/secrets/public-key | GET | Query | JSON | 获取组织公开密钥 |
| https://api.github.com/orgs/{org}/codespaces/secrets/{secret_name} | DELETE | Query | JSON | 删除组织密钥 |
| https://api.github.com/orgs/{org}/codespaces/secrets/{secret_name} | GET | Query | JSON | 获取组织密钥 |
| https://api.github.com/orgs/{org}/codespaces/secrets/{secret_name} | PUT | JSON | JSON | 创建或更新组织密钥 |
| https://api.github.com/orgs/{org}/codespaces/secrets/{secret_name}/repositories | GET | Query | JSON | 列出已选仓库用于组织密钥 |
| https://api.github.com/orgs/{org}/codespaces/secrets/{secret_name}/repositories | PUT | JSON | JSON | 设置已选仓库用于组织密钥 |
| https://api.github.com/orgs/{org}/codespaces/secrets/{secret_name}/repositories/{repository_id} | DELETE | Query | JSON | 移除已选仓库来自组织密钥 |
| https://api.github.com/orgs/{org}/codespaces/secrets/{secret_name}/repositories/{repository_id} | PUT | Query | JSON | 添加已选仓库给组织密钥 |
| https://api.github.com/orgs/{org}/members/{username}/codespaces | GET | Query | JSON | 列出 codespaces 用于用户内组织 |
| https://api.github.com/orgs/{org}/members/{username}/codespaces/{codespace_name} | DELETE | Query | JSON | 删除 codespace 来自组织 |
| https://api.github.com/orgs/{org}/members/{username}/codespaces/{codespace_name}/stop | POST | Query | JSON | 停止 codespace 用于组织用户 |
| https://api.github.com/repos/{owner}/{repo}/codespaces | GET | Query | JSON | 列出 codespaces 内仓库用于已认证用户 |
| https://api.github.com/repos/{owner}/{repo}/codespaces | POST | JSON | JSON | 创建 codespace 内仓库 |
| https://api.github.com/repos/{owner}/{repo}/codespaces/devcontainers | GET | Query | JSON | 列出 devcontainer 配置内仓库用于已认证用户 |
| https://api.github.com/repos/{owner}/{repo}/codespaces/machines | GET | Query | JSON | 列出可用机器类型用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/codespaces/new | GET | Query | JSON | 获取默认属性用于 codespace |
| https://api.github.com/repos/{owner}/{repo}/codespaces/permissions_check | GET | Query | JSON | 检查是否权限已定义按 devcontainer 已已接受按已认证用户 |
| https://api.github.com/repos/{owner}/{repo}/codespaces/secrets | GET | Query | JSON | 列出仓库密钥 |
| https://api.github.com/repos/{owner}/{repo}/codespaces/secrets/public-key | GET | Query | JSON | 获取仓库公开密钥 |
| https://api.github.com/repos/{owner}/{repo}/codespaces/secrets/{secret_name} | DELETE | Query | JSON | 删除仓库密钥 |
| https://api.github.com/repos/{owner}/{repo}/codespaces/secrets/{secret_name} | GET | Query | JSON | 获取仓库密钥 |
| https://api.github.com/repos/{owner}/{repo}/codespaces/secrets/{secret_name} | PUT | JSON | JSON | 创建或更新仓库密钥 |
| https://api.github.com/repos/{owner}/{repo}/pulls/{pull_number}/codespaces | POST | JSON | JSON | 创建 codespace 来自拉取请求 |
| https://api.github.com/user/codespaces | GET | Query | JSON | 列出 codespaces 用于已认证用户 |
| https://api.github.com/user/codespaces | POST | JSON | JSON | 创建 codespace 用于已认证用户 |
| https://api.github.com/user/codespaces/secrets | GET | Query | JSON | 列出密钥用于已认证用户 |
| https://api.github.com/user/codespaces/secrets/public-key | GET | Query | JSON | 获取公开密钥用于已认证用户 |
| https://api.github.com/user/codespaces/secrets/{secret_name} | DELETE | Query | JSON | 删除密钥用于已认证用户 |
| https://api.github.com/user/codespaces/secrets/{secret_name} | GET | Query | JSON | 获取密钥用于已认证用户 |
| https://api.github.com/user/codespaces/secrets/{secret_name} | PUT | JSON | JSON | 创建或更新密钥用于已认证用户 |
| https://api.github.com/user/codespaces/secrets/{secret_name}/repositories | GET | Query | JSON | 列出已选仓库用于用户密钥 |
| https://api.github.com/user/codespaces/secrets/{secret_name}/repositories | PUT | JSON | JSON | 设置已选仓库用于用户密钥 |
| https://api.github.com/user/codespaces/secrets/{secret_name}/repositories/{repository_id} | DELETE | Query | JSON | 移除已选仓库来自用户密钥 |
| https://api.github.com/user/codespaces/secrets/{secret_name}/repositories/{repository_id} | PUT | Query | JSON | 添加已选仓库给用户密钥 |
| https://api.github.com/user/codespaces/{codespace_name} | DELETE | Query | JSON | 删除 codespace 用于已认证用户 |
| https://api.github.com/user/codespaces/{codespace_name} | GET | Query | JSON | 获取 codespace 用于已认证用户 |
| https://api.github.com/user/codespaces/{codespace_name} | PATCH | JSON | JSON | 更新 codespace 用于已认证用户 |
| https://api.github.com/user/codespaces/{codespace_name}/exports | POST | Query | JSON | 导出 codespace 用于已认证用户 |
| https://api.github.com/user/codespaces/{codespace_name}/exports/{export_id} | GET | Query | JSON | 获取详情关于 codespace 导出 |
| https://api.github.com/user/codespaces/{codespace_name}/machines | GET | Query | JSON | 列出机器类型用于 codespace |
| https://api.github.com/user/codespaces/{codespace_name}/publish | POST | JSON | JSON | 创建仓库来自未发布 codespace |
| https://api.github.com/user/codespaces/{codespace_name}/start | POST | Query | JSON | 启动 codespace 用于已认证用户 |
| https://api.github.com/user/codespaces/{codespace_name}/stop | POST | Query | JSON | 停止 codespace 用于已认证用户 |

## GitHub Actions 工作流（200 条）

> 官方分类标签 `actions`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/enterprises/{enterprise}/actions/cache/retention-limit | GET | Query | JSON | 获取 GitHub Actions 缓存保留限制用于企业 |
| https://api.github.com/enterprises/{enterprise}/actions/cache/retention-limit | PUT | JSON | JSON | 设置 GitHub Actions 缓存保留限制用于企业 |
| https://api.github.com/enterprises/{enterprise}/actions/cache/storage-limit | GET | Query | JSON | 获取 GitHub Actions 缓存存储限制用于企业 |
| https://api.github.com/enterprises/{enterprise}/actions/cache/storage-limit | PUT | JSON | JSON | 设置 GitHub Actions 缓存存储限制用于企业 |
| https://api.github.com/organizations/{org}/actions/cache/retention-limit | GET | Query | JSON | 获取 GitHub Actions 缓存保留限制用于组织 |
| https://api.github.com/organizations/{org}/actions/cache/retention-limit | PUT | JSON | JSON | 设置 GitHub Actions 缓存保留限制用于组织 |
| https://api.github.com/organizations/{org}/actions/cache/storage-limit | GET | Query | JSON | 获取 GitHub Actions 缓存存储限制用于组织 |
| https://api.github.com/organizations/{org}/actions/cache/storage-limit | PUT | JSON | JSON | 设置 GitHub Actions 缓存存储限制用于组织 |
| https://api.github.com/orgs/{org}/actions/cache/usage | GET | Query | JSON | 获取 GitHub Actions 缓存用量用于组织 |
| https://api.github.com/orgs/{org}/actions/cache/usage-by-repository | GET | Query | JSON | 列出仓库含 GitHub Actions 缓存用量用于组织 |
| https://api.github.com/orgs/{org}/actions/hosted-runners | GET | Query | JSON | 列出 GitHub 托管运行器用于组织 |
| https://api.github.com/orgs/{org}/actions/hosted-runners | POST | JSON | JSON | 创建 GitHub 托管运行器用于组织 |
| https://api.github.com/orgs/{org}/actions/hosted-runners/images/custom | GET | Query | JSON | 列出自定义图片用于组织 |
| https://api.github.com/orgs/{org}/actions/hosted-runners/images/custom/{image_definition_id} | DELETE | Query | JSON | 删除自定义图片来自组织 |
| https://api.github.com/orgs/{org}/actions/hosted-runners/images/custom/{image_definition_id} | GET | Query | JSON | 获取自定义图片定义用于 GitHub Actions 托管运行器 |
| https://api.github.com/orgs/{org}/actions/hosted-runners/images/custom/{image_definition_id}/versions | GET | Query | JSON | 列出图片版本的自定义图片用于组织 |
| https://api.github.com/orgs/{org}/actions/hosted-runners/images/custom/{image_definition_id}/versions/{version} | DELETE | Query | JSON | 删除图片版本的自定义图片来自组织 |
| https://api.github.com/orgs/{org}/actions/hosted-runners/images/custom/{image_definition_id}/versions/{version} | GET | Query | JSON | 获取图片版本的自定义图片用于 GitHub Actions 托管运行器 |
| https://api.github.com/orgs/{org}/actions/hosted-runners/images/github-owned | GET | Query | JSON | 获取 GitHub 所属图片用于 GitHub 托管运行器内组织 |
| https://api.github.com/orgs/{org}/actions/hosted-runners/images/partner | GET | Query | JSON | 获取合作伙伴图片用于 GitHub 托管运行器内组织 |
| https://api.github.com/orgs/{org}/actions/hosted-runners/limits | GET | Query | JSON | 获取限制上 GitHub 托管运行器用于组织 |
| https://api.github.com/orgs/{org}/actions/hosted-runners/machine-sizes | GET | Query | JSON | 获取 GitHub 托管运行器机器规范用于组织 |
| https://api.github.com/orgs/{org}/actions/hosted-runners/platforms | GET | Query | JSON | 获取平台用于 GitHub 托管运行器内组织 |
| https://api.github.com/orgs/{org}/actions/hosted-runners/{hosted_runner_id} | DELETE | Query | JSON | 删除 GitHub 托管运行器用于组织 |
| https://api.github.com/orgs/{org}/actions/hosted-runners/{hosted_runner_id} | GET | Query | JSON | 获取 GitHub 托管运行器用于组织 |
| https://api.github.com/orgs/{org}/actions/hosted-runners/{hosted_runner_id} | PATCH | JSON | JSON | 更新 GitHub 托管运行器用于组织 |
| https://api.github.com/orgs/{org}/actions/permissions | GET | Query | JSON | 获取 GitHub Actions 权限用于组织 |
| https://api.github.com/orgs/{org}/actions/permissions | PUT | JSON | JSON | 设置 GitHub Actions 权限用于组织 |
| https://api.github.com/orgs/{org}/actions/permissions/artifact-and-log-retention | GET | Query | JSON | 获取构件与日志保留设置用于组织 |
| https://api.github.com/orgs/{org}/actions/permissions/artifact-and-log-retention | PUT | JSON | JSON | 设置构件与日志保留设置用于组织 |
| https://api.github.com/orgs/{org}/actions/permissions/fork-pr-contributor-approval | GET | Query | JSON | 获取 fork PR 贡献者审批权限用于组织 |
| https://api.github.com/orgs/{org}/actions/permissions/fork-pr-contributor-approval | PUT | JSON | JSON | 设置 fork PR 贡献者审批权限用于组织 |
| https://api.github.com/orgs/{org}/actions/permissions/fork-pr-workflows-private-repos | GET | Query | JSON | 获取私有仓库 fork PR 工作流设置用于组织 |
| https://api.github.com/orgs/{org}/actions/permissions/fork-pr-workflows-private-repos | PUT | JSON | JSON | 设置私有仓库 fork PR 工作流设置用于组织 |
| https://api.github.com/orgs/{org}/actions/permissions/repositories | GET | Query | JSON | 列出已选仓库已启用用于 GitHub Actions 内组织 |
| https://api.github.com/orgs/{org}/actions/permissions/repositories | PUT | JSON | JSON | 设置已选仓库已启用用于 GitHub Actions 内组织 |
| https://api.github.com/orgs/{org}/actions/permissions/repositories/{repository_id} | DELETE | Query | JSON | 禁用已选仓库用于 GitHub Actions 内组织 |
| https://api.github.com/orgs/{org}/actions/permissions/repositories/{repository_id} | PUT | Query | JSON | 启用已选仓库用于 GitHub Actions 内组织 |
| https://api.github.com/orgs/{org}/actions/permissions/selected-actions | GET | Query | JSON | 获取允许 actions 与可复用工作流用于组织 |
| https://api.github.com/orgs/{org}/actions/permissions/selected-actions | PUT | JSON | JSON | 设置允许 actions 与可复用工作流用于组织 |
| https://api.github.com/orgs/{org}/actions/permissions/self-hosted-runners | GET | Query | JSON | 获取自托管运行器设置用于组织 |
| https://api.github.com/orgs/{org}/actions/permissions/self-hosted-runners | PUT | JSON | JSON | 设置自托管运行器设置用于组织 |
| https://api.github.com/orgs/{org}/actions/permissions/self-hosted-runners/repositories | GET | Query | JSON | 列出仓库允许给使用自托管运行器内组织 |
| https://api.github.com/orgs/{org}/actions/permissions/self-hosted-runners/repositories | PUT | JSON | JSON | 设置仓库允许给使用自托管运行器内组织 |
| https://api.github.com/orgs/{org}/actions/permissions/self-hosted-runners/repositories/{repository_id} | DELETE | Query | JSON | 移除仓库来自列出的仓库允许给使用自托管运行器内组织 |
| https://api.github.com/orgs/{org}/actions/permissions/self-hosted-runners/repositories/{repository_id} | PUT | Query | JSON | 添加仓库给列出的仓库允许给使用自托管运行器内组织 |
| https://api.github.com/orgs/{org}/actions/permissions/workflow | GET | Query | JSON | 获取默认工作流权限用于组织 |
| https://api.github.com/orgs/{org}/actions/permissions/workflow | PUT | JSON | JSON | 设置默认工作流权限用于组织 |
| https://api.github.com/orgs/{org}/actions/policies | GET | Query | JSON | 列出组织 Actions 策略 |
| https://api.github.com/orgs/{org}/actions/policies | POST | JSON | JSON | 创建组织 Actions 策略 |
| https://api.github.com/orgs/{org}/actions/policies/{policy_id} | DELETE | Query | JSON | 删除组织 Actions 策略 |
| https://api.github.com/orgs/{org}/actions/policies/{policy_id} | GET | Query | JSON | 获取组织 Actions 策略 |
| https://api.github.com/orgs/{org}/actions/policies/{policy_id} | PUT | JSON | JSON | 更新组织 Actions 策略 |
| https://api.github.com/orgs/{org}/actions/runner-groups | GET | Query | JSON | 列出自托管运行器分组用于组织 |
| https://api.github.com/orgs/{org}/actions/runner-groups | POST | JSON | JSON | 创建自托管运行器分组用于组织 |
| https://api.github.com/orgs/{org}/actions/runner-groups/{runner_group_id} | DELETE | Query | JSON | 删除自托管运行器分组来自组织 |
| https://api.github.com/orgs/{org}/actions/runner-groups/{runner_group_id} | GET | Query | JSON | 获取自托管运行器分组用于组织 |
| https://api.github.com/orgs/{org}/actions/runner-groups/{runner_group_id} | PATCH | JSON | JSON | 更新自托管运行器分组用于组织 |
| https://api.github.com/orgs/{org}/actions/runner-groups/{runner_group_id}/hosted-runners | GET | Query | JSON | 列出 GitHub 托管运行器内分组用于组织 |
| https://api.github.com/orgs/{org}/actions/runner-groups/{runner_group_id}/repositories | GET | Query | JSON | 列出仓库访问给自托管运行器分组内组织 |
| https://api.github.com/orgs/{org}/actions/runner-groups/{runner_group_id}/repositories | PUT | JSON | JSON | 设置仓库访问用于自托管运行器分组内组织 |
| https://api.github.com/orgs/{org}/actions/runner-groups/{runner_group_id}/repositories/{repository_id} | DELETE | Query | JSON | 移除仓库访问给自托管运行器分组内组织 |
| https://api.github.com/orgs/{org}/actions/runner-groups/{runner_group_id}/repositories/{repository_id} | PUT | Query | JSON | 添加仓库访问给自托管运行器分组内组织 |
| https://api.github.com/orgs/{org}/actions/runner-groups/{runner_group_id}/runners | GET | Query | JSON | 列出自托管运行器内分组用于组织 |
| https://api.github.com/orgs/{org}/actions/runner-groups/{runner_group_id}/runners | PUT | JSON | JSON | 设置自托管运行器内分组用于组织 |
| https://api.github.com/orgs/{org}/actions/runner-groups/{runner_group_id}/runners/{runner_id} | DELETE | Query | JSON | 移除自托管运行器来自分组用于组织 |
| https://api.github.com/orgs/{org}/actions/runner-groups/{runner_group_id}/runners/{runner_id} | PUT | Query | JSON | 添加自托管运行器给分组用于组织 |
| https://api.github.com/orgs/{org}/actions/runners | GET | Query | JSON | 列出自托管运行器用于组织 |
| https://api.github.com/orgs/{org}/actions/runners/deprecations/{version} | GET | Query | JSON | 获取运行器版本生命周期终止计划用于组织 |
| https://api.github.com/orgs/{org}/actions/runners/downloads | GET | Query | JSON | 列出运行器应用用于组织 |
| https://api.github.com/orgs/{org}/actions/runners/generate-jitconfig | POST | JSON | JSON | 创建配置用于即时运行器用于组织 |
| https://api.github.com/orgs/{org}/actions/runners/registration-token | POST | Query | JSON | 创建注册令牌用于组织 |
| https://api.github.com/orgs/{org}/actions/runners/remove-token | POST | Query | JSON | 创建移除令牌用于组织 |
| https://api.github.com/orgs/{org}/actions/runners/{runner_id} | DELETE | Query | JSON | 删除自托管运行器来自组织 |
| https://api.github.com/orgs/{org}/actions/runners/{runner_id} | GET | Query | JSON | 获取自托管运行器用于组织 |
| https://api.github.com/orgs/{org}/actions/runners/{runner_id}/labels | DELETE | Query | JSON | 移除全部自定义标签来自自托管运行器用于组织 |
| https://api.github.com/orgs/{org}/actions/runners/{runner_id}/labels | GET | Query | JSON | 列出标签用于自托管运行器用于组织 |
| https://api.github.com/orgs/{org}/actions/runners/{runner_id}/labels | POST | JSON | JSON | 添加自定义标签给自托管运行器用于组织 |
| https://api.github.com/orgs/{org}/actions/runners/{runner_id}/labels | PUT | JSON | JSON | 设置自定义标签用于自托管运行器用于组织 |
| https://api.github.com/orgs/{org}/actions/runners/{runner_id}/labels/{name} | DELETE | Query | JSON | 移除自定义标签来自自托管运行器用于组织 |
| https://api.github.com/orgs/{org}/actions/secrets | GET | Query | JSON | 列出组织密钥 |
| https://api.github.com/orgs/{org}/actions/secrets/public-key | GET | Query | JSON | 获取组织公开密钥 |
| https://api.github.com/orgs/{org}/actions/secrets/{secret_name} | DELETE | Query | JSON | 删除组织密钥 |
| https://api.github.com/orgs/{org}/actions/secrets/{secret_name} | GET | Query | JSON | 获取组织密钥 |
| https://api.github.com/orgs/{org}/actions/secrets/{secret_name} | PUT | JSON | JSON | 创建或更新组织密钥 |
| https://api.github.com/orgs/{org}/actions/secrets/{secret_name}/repositories | GET | Query | JSON | 列出已选仓库用于组织密钥 |
| https://api.github.com/orgs/{org}/actions/secrets/{secret_name}/repositories | PUT | JSON | JSON | 设置已选仓库用于组织密钥 |
| https://api.github.com/orgs/{org}/actions/secrets/{secret_name}/repositories/{repository_id} | DELETE | Query | JSON | 移除已选仓库来自组织密钥 |
| https://api.github.com/orgs/{org}/actions/secrets/{secret_name}/repositories/{repository_id} | PUT | Query | JSON | 添加已选仓库给组织密钥 |
| https://api.github.com/orgs/{org}/actions/variables | GET | Query | JSON | 列出组织变量 |
| https://api.github.com/orgs/{org}/actions/variables | POST | JSON | JSON | 创建组织变量 |
| https://api.github.com/orgs/{org}/actions/variables/{name} | DELETE | Query | JSON | 删除组织变量 |
| https://api.github.com/orgs/{org}/actions/variables/{name} | GET | Query | JSON | 获取组织变量 |
| https://api.github.com/orgs/{org}/actions/variables/{name} | PATCH | JSON | JSON | 更新组织变量 |
| https://api.github.com/orgs/{org}/actions/variables/{name}/repositories | GET | Query | JSON | 列出已选仓库用于组织变量 |
| https://api.github.com/orgs/{org}/actions/variables/{name}/repositories | PUT | JSON | JSON | 设置已选仓库用于组织变量 |
| https://api.github.com/orgs/{org}/actions/variables/{name}/repositories/{repository_id} | DELETE | Query | JSON | 移除已选仓库来自组织变量 |
| https://api.github.com/orgs/{org}/actions/variables/{name}/repositories/{repository_id} | PUT | Query | JSON | 添加已选仓库给组织变量 |
| https://api.github.com/repos/{owner}/{repo}/actions/artifacts | GET | Query | JSON | 列出构件用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/artifacts/{artifact_id} | DELETE | Query | JSON | 删除构件 |
| https://api.github.com/repos/{owner}/{repo}/actions/artifacts/{artifact_id} | GET | Query | JSON | 获取构件 |
| https://api.github.com/repos/{owner}/{repo}/actions/artifacts/{artifact_id}/{archive_format} | GET | Query | JSON | 下载构件 |
| https://api.github.com/repos/{owner}/{repo}/actions/cache/retention-limit | GET | Query | JSON | 获取 GitHub Actions 缓存保留限制用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/cache/retention-limit | PUT | JSON | JSON | 设置 GitHub Actions 缓存保留限制用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/cache/storage-limit | GET | Query | JSON | 获取 GitHub Actions 缓存存储限制用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/cache/storage-limit | PUT | JSON | JSON | 设置 GitHub Actions 缓存存储限制用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/cache/usage | GET | Query | JSON | 获取 GitHub Actions 缓存用量用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/caches | DELETE | Query | JSON | 删除 GitHub Actions 缓存用于仓库 (使用缓存密钥) |
| https://api.github.com/repos/{owner}/{repo}/actions/caches | GET | Query | JSON | 列出 GitHub Actions 缓存用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/caches/{cache_id} | DELETE | Query | JSON | 删除 GitHub Actions 缓存用于仓库 (使用缓存 ID) |
| https://api.github.com/repos/{owner}/{repo}/actions/concurrency_groups | GET | Query | JSON | 列出并发分组用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/concurrency_groups/{concurrency_group_name} | GET | Query | JSON | 获取并发分组用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/jobs/{job_id} | GET | Query | JSON | 获取任务用于工作流运行 |
| https://api.github.com/repos/{owner}/{repo}/actions/jobs/{job_id}/logs | GET | Query | JSON | 下载任务日志用于工作流运行 |
| https://api.github.com/repos/{owner}/{repo}/actions/jobs/{job_id}/rerun | POST | JSON | JSON | 重新运行任务来自工作流运行 |
| https://api.github.com/repos/{owner}/{repo}/actions/jobs/{job_id}/steps/{step_number}/logs | GET | Query | JSON | 下载步骤日志用于工作流运行任务 |
| https://api.github.com/repos/{owner}/{repo}/actions/oidc/customization/sub | GET | Query | JSON | 获取自定义模板用于 OIDC 主题声明用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/oidc/customization/sub | PUT | JSON | JSON | 设置自定义模板用于 OIDC 主题声明用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/organization-secrets | GET | Query | JSON | 列出仓库组织密钥 |
| https://api.github.com/repos/{owner}/{repo}/actions/organization-variables | GET | Query | JSON | 列出仓库组织变量 |
| https://api.github.com/repos/{owner}/{repo}/actions/permissions | GET | Query | JSON | 获取 GitHub Actions 权限用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/permissions | PUT | JSON | JSON | 设置 GitHub Actions 权限用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/permissions/access | GET | Query | JSON | 获取级别的访问用于工作流外部的仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/permissions/access | PUT | JSON | JSON | 设置级别的访问用于工作流外部的仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/permissions/artifact-and-log-retention | GET | Query | JSON | 获取构件与日志保留设置用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/permissions/artifact-and-log-retention | PUT | JSON | JSON | 设置构件与日志保留设置用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/permissions/fork-pr-contributor-approval | GET | Query | JSON | 获取 fork PR 贡献者审批权限用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/permissions/fork-pr-contributor-approval | PUT | JSON | JSON | 设置 fork PR 贡献者审批权限用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/permissions/fork-pr-workflows-private-repos | GET | Query | JSON | 获取私有仓库 fork PR 工作流设置用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/permissions/fork-pr-workflows-private-repos | PUT | JSON | JSON | 设置私有仓库 fork PR 工作流设置用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/permissions/selected-actions | GET | Query | JSON | 获取允许 actions 与可复用工作流用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/permissions/selected-actions | PUT | JSON | JSON | 设置允许 actions 与可复用工作流用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/permissions/workflow | GET | Query | JSON | 获取默认工作流权限用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/permissions/workflow | PUT | JSON | JSON | 设置默认工作流权限用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/policies | GET | Query | JSON | 列出仓库 Actions 策略 |
| https://api.github.com/repos/{owner}/{repo}/actions/policies | POST | JSON | JSON | 创建仓库 Actions 策略 |
| https://api.github.com/repos/{owner}/{repo}/actions/policies/{policy_id} | DELETE | Query | JSON | 删除仓库 Actions 策略 |
| https://api.github.com/repos/{owner}/{repo}/actions/policies/{policy_id} | GET | Query | JSON | 获取仓库 Actions 策略 |
| https://api.github.com/repos/{owner}/{repo}/actions/policies/{policy_id} | PUT | JSON | JSON | 更新仓库 Actions 策略 |
| https://api.github.com/repos/{owner}/{repo}/actions/runners | GET | Query | JSON | 列出自托管运行器用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/runners/deprecations/{version} | GET | Query | JSON | 获取运行器版本生命周期终止计划用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/runners/downloads | GET | Query | JSON | 列出运行器应用用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/runners/generate-jitconfig | POST | JSON | JSON | 创建配置用于即时运行器用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/runners/registration-token | POST | Query | JSON | 创建注册令牌用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/runners/remove-token | POST | Query | JSON | 创建移除令牌用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/runners/{runner_id} | DELETE | Query | JSON | 删除自托管运行器来自仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/runners/{runner_id} | GET | Query | JSON | 获取自托管运行器用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/runners/{runner_id}/labels | DELETE | Query | JSON | 移除全部自定义标签来自自托管运行器用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/runners/{runner_id}/labels | GET | Query | JSON | 列出标签用于自托管运行器用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/runners/{runner_id}/labels | POST | JSON | JSON | 添加自定义标签给自托管运行器用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/runners/{runner_id}/labels | PUT | JSON | JSON | 设置自定义标签用于自托管运行器用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/runners/{runner_id}/labels/{name} | DELETE | Query | JSON | 移除自定义标签来自自托管运行器用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/runs | GET | Query | JSON | 列出工作流运行用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/actions/runs/{run_id} | DELETE | Query | JSON | 删除工作流运行 |
| https://api.github.com/repos/{owner}/{repo}/actions/runs/{run_id} | GET | Query | JSON | 获取工作流运行 |
| https://api.github.com/repos/{owner}/{repo}/actions/runs/{run_id}/approvals | GET | Query | JSON | 获取审核历史用于工作流运行 |
| https://api.github.com/repos/{owner}/{repo}/actions/runs/{run_id}/approve | POST | Query | JSON | 批准工作流运行用于 fork 拉取请求 |
| https://api.github.com/repos/{owner}/{repo}/actions/runs/{run_id}/artifacts | GET | Query | JSON | 列出工作流运行构件 |
| https://api.github.com/repos/{owner}/{repo}/actions/runs/{run_id}/attempts/{attempt_number} | GET | Query | JSON | 获取工作流运行尝试 |
| https://api.github.com/repos/{owner}/{repo}/actions/runs/{run_id}/attempts/{attempt_number}/jobs | GET | Query | JSON | 列出任务用于工作流运行尝试 |
| https://api.github.com/repos/{owner}/{repo}/actions/runs/{run_id}/attempts/{attempt_number}/logs | GET | Query | JSON | 下载工作流运行尝试日志 |
| https://api.github.com/repos/{owner}/{repo}/actions/runs/{run_id}/cancel | POST | Query | JSON | 取消工作流运行 |
| https://api.github.com/repos/{owner}/{repo}/actions/runs/{run_id}/concurrency_groups | GET | Query | JSON | 列出并发分组用于工作流运行 |
| https://api.github.com/repos/{owner}/{repo}/actions/runs/{run_id}/deployment_protection_rule | POST | JSON | JSON | 审核自定义部署保护规则用于工作流运行 |
| https://api.github.com/repos/{owner}/{repo}/actions/runs/{run_id}/force-cancel | POST | Query | JSON | 强制取消工作流运行 |
| https://api.github.com/repos/{owner}/{repo}/actions/runs/{run_id}/jobs | GET | Query | JSON | 列出任务用于工作流运行 |
| https://api.github.com/repos/{owner}/{repo}/actions/runs/{run_id}/logs | DELETE | Query | JSON | 删除工作流运行日志 |
| https://api.github.com/repos/{owner}/{repo}/actions/runs/{run_id}/logs | GET | Query | JSON | 下载工作流运行日志 |
| https://api.github.com/repos/{owner}/{repo}/actions/runs/{run_id}/pending_deployments | GET | Query | JSON | 获取待处理部署用于工作流运行 |
| https://api.github.com/repos/{owner}/{repo}/actions/runs/{run_id}/pending_deployments | POST | JSON | JSON | 审核待处理部署用于工作流运行 |
| https://api.github.com/repos/{owner}/{repo}/actions/runs/{run_id}/rerun | POST | JSON | JSON | 重新运行工作流 |
| https://api.github.com/repos/{owner}/{repo}/actions/runs/{run_id}/rerun-failed-jobs | POST | JSON | JSON | 重新运行失败任务来自工作流运行 |
| https://api.github.com/repos/{owner}/{repo}/actions/runs/{run_id}/timing | GET | Query | JSON | 获取工作流运行用量 |
| https://api.github.com/repos/{owner}/{repo}/actions/secrets | GET | Query | JSON | 列出仓库密钥 |
| https://api.github.com/repos/{owner}/{repo}/actions/secrets/public-key | GET | Query | JSON | 获取仓库公开密钥 |
| https://api.github.com/repos/{owner}/{repo}/actions/secrets/{secret_name} | DELETE | Query | JSON | 删除仓库密钥 |
| https://api.github.com/repos/{owner}/{repo}/actions/secrets/{secret_name} | GET | Query | JSON | 获取仓库密钥 |
| https://api.github.com/repos/{owner}/{repo}/actions/secrets/{secret_name} | PUT | JSON | JSON | 创建或更新仓库密钥 |
| https://api.github.com/repos/{owner}/{repo}/actions/variables | GET | Query | JSON | 列出仓库变量 |
| https://api.github.com/repos/{owner}/{repo}/actions/variables | POST | JSON | JSON | 创建仓库变量 |
| https://api.github.com/repos/{owner}/{repo}/actions/variables/{name} | DELETE | Query | JSON | 删除仓库变量 |
| https://api.github.com/repos/{owner}/{repo}/actions/variables/{name} | GET | Query | JSON | 获取仓库变量 |
| https://api.github.com/repos/{owner}/{repo}/actions/variables/{name} | PATCH | JSON | JSON | 更新仓库变量 |
| https://api.github.com/repos/{owner}/{repo}/actions/workflows | GET | Query | JSON | 列出仓库工作流 |
| https://api.github.com/repos/{owner}/{repo}/actions/workflows/{workflow_id} | GET | Query | JSON | 获取工作流 |
| https://api.github.com/repos/{owner}/{repo}/actions/workflows/{workflow_id}/disable | PUT | Query | JSON | 禁用工作流 |
| https://api.github.com/repos/{owner}/{repo}/actions/workflows/{workflow_id}/dispatches | POST | JSON | JSON | 创建工作流调度事件 |
| https://api.github.com/repos/{owner}/{repo}/actions/workflows/{workflow_id}/enable | PUT | Query | JSON | 启用工作流 |
| https://api.github.com/repos/{owner}/{repo}/actions/workflows/{workflow_id}/runs | GET | Query | JSON | 列出工作流运行用于工作流 |
| https://api.github.com/repos/{owner}/{repo}/actions/workflows/{workflow_id}/timing | GET | Query | JSON | 获取工作流用量 |
| https://api.github.com/repos/{owner}/{repo}/environments/{environment_name}/secrets | GET | Query | JSON | 列出环境密钥 |
| https://api.github.com/repos/{owner}/{repo}/environments/{environment_name}/secrets/public-key | GET | Query | JSON | 获取环境公开密钥 |
| https://api.github.com/repos/{owner}/{repo}/environments/{environment_name}/secrets/{secret_name} | DELETE | Query | JSON | 删除环境密钥 |
| https://api.github.com/repos/{owner}/{repo}/environments/{environment_name}/secrets/{secret_name} | GET | Query | JSON | 获取环境密钥 |
| https://api.github.com/repos/{owner}/{repo}/environments/{environment_name}/secrets/{secret_name} | PUT | JSON | JSON | 创建或更新环境密钥 |
| https://api.github.com/repos/{owner}/{repo}/environments/{environment_name}/variables | GET | Query | JSON | 列出环境变量 |
| https://api.github.com/repos/{owner}/{repo}/environments/{environment_name}/variables | POST | JSON | JSON | 创建环境变量 |
| https://api.github.com/repos/{owner}/{repo}/environments/{environment_name}/variables/{name} | DELETE | Query | JSON | 删除环境变量 |
| https://api.github.com/repos/{owner}/{repo}/environments/{environment_name}/variables/{name} | GET | Query | JSON | 获取环境变量 |
| https://api.github.com/repos/{owner}/{repo}/environments/{environment_name}/variables/{name} | PATCH | JSON | JSON | 更新环境变量 |

## Dependabot（25 条）

> 官方分类标签 `dependabot`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/enterprises/{enterprise}/dependabot/alerts | GET | Query | JSON | 列出 Dependabot 告警用于企业 |
| https://api.github.com/enterprises/{enterprise}/dependabot/repository-access | GET | Query | JSON | 列出仓库 Dependabot 可以访问内企业 |
| https://api.github.com/enterprises/{enterprise}/dependabot/repository-access | PATCH | JSON | JSON | 更新 Dependabot 的仓库访问列出用于企业 |
| https://api.github.com/enterprises/{enterprise}/dependabot/repository-access/default-level | PUT | JSON | JSON | 设置默认仓库访问级别用于 Dependabot 内企业 |
| https://api.github.com/orgs/{org}/dependabot/alerts | GET | Query | JSON | 列出 Dependabot 告警用于组织 |
| https://api.github.com/orgs/{org}/dependabot/repository-access | GET | Query | JSON | 列出仓库 Dependabot 可以访问内组织 |
| https://api.github.com/orgs/{org}/dependabot/repository-access | PATCH | JSON | JSON | 更新 Dependabot 的仓库访问列出用于组织 |
| https://api.github.com/orgs/{org}/dependabot/repository-access/default-level | PUT | JSON | JSON | 设置默认仓库访问级别用于 Dependabot |
| https://api.github.com/orgs/{org}/dependabot/secrets | GET | Query | JSON | 列出组织密钥 |
| https://api.github.com/orgs/{org}/dependabot/secrets/public-key | GET | Query | JSON | 获取组织公开密钥 |
| https://api.github.com/orgs/{org}/dependabot/secrets/{secret_name} | DELETE | Query | JSON | 删除组织密钥 |
| https://api.github.com/orgs/{org}/dependabot/secrets/{secret_name} | GET | Query | JSON | 获取组织密钥 |
| https://api.github.com/orgs/{org}/dependabot/secrets/{secret_name} | PUT | JSON | JSON | 创建或更新组织密钥 |
| https://api.github.com/orgs/{org}/dependabot/secrets/{secret_name}/repositories | GET | Query | JSON | 列出已选仓库用于组织密钥 |
| https://api.github.com/orgs/{org}/dependabot/secrets/{secret_name}/repositories | PUT | JSON | JSON | 设置已选仓库用于组织密钥 |
| https://api.github.com/orgs/{org}/dependabot/secrets/{secret_name}/repositories/{repository_id} | DELETE | Query | JSON | 移除已选仓库来自组织密钥 |
| https://api.github.com/orgs/{org}/dependabot/secrets/{secret_name}/repositories/{repository_id} | PUT | Query | JSON | 添加已选仓库给组织密钥 |
| https://api.github.com/repos/{owner}/{repo}/dependabot/alerts | GET | Query | JSON | 列出 Dependabot 告警用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/dependabot/alerts/{alert_number} | GET | Query | JSON | 获取 Dependabot 告警 |
| https://api.github.com/repos/{owner}/{repo}/dependabot/alerts/{alert_number} | PATCH | JSON | JSON | 更新 Dependabot 告警 |
| https://api.github.com/repos/{owner}/{repo}/dependabot/secrets | GET | Query | JSON | 列出仓库密钥 |
| https://api.github.com/repos/{owner}/{repo}/dependabot/secrets/public-key | GET | Query | JSON | 获取仓库公开密钥 |
| https://api.github.com/repos/{owner}/{repo}/dependabot/secrets/{secret_name} | DELETE | Query | JSON | 删除仓库密钥 |
| https://api.github.com/repos/{owner}/{repo}/dependabot/secrets/{secret_name} | GET | Query | JSON | 获取仓库密钥 |
| https://api.github.com/repos/{owner}/{repo}/dependabot/secrets/{secret_name} | PUT | JSON | JSON | 创建或更新仓库密钥 |

## 代码扫描 Code Scanning（25 条）

> 官方分类标签 `code-scanning`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/orgs/{org}/code-scanning/ai-scan | GET | Query | JSON | 获取 AI 扫描设置用于组织 |
| https://api.github.com/orgs/{org}/code-scanning/ai-scan | PATCH | JSON | JSON | 更新 AI 扫描设置用于组织 |
| https://api.github.com/orgs/{org}/code-scanning/alerts | GET | Query | JSON | 列出代码扫描告警用于组织 |
| https://api.github.com/repos/{owner}/{repo}/code-scanning/ai-scan | GET | Query | JSON | 获取 AI 扫描启用用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/code-scanning/ai-scan | PATCH | JSON | JSON | 更新 AI 扫描启用用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/code-scanning/alerts | GET | Query | JSON | 列出代码扫描告警用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/code-scanning/alerts/{alert_number} | GET | Query | JSON | 获取代码扫描告警 |
| https://api.github.com/repos/{owner}/{repo}/code-scanning/alerts/{alert_number} | PATCH | JSON | JSON | 更新代码扫描告警 |
| https://api.github.com/repos/{owner}/{repo}/code-scanning/alerts/{alert_number}/autofix | GET | Query | JSON | 获取状态的自动修复用于代码扫描告警 |
| https://api.github.com/repos/{owner}/{repo}/code-scanning/alerts/{alert_number}/autofix | POST | Query | JSON | 创建自动修复用于代码扫描告警 |
| https://api.github.com/repos/{owner}/{repo}/code-scanning/alerts/{alert_number}/autofix/commits | POST | JSON | JSON | 提交自动修复用于代码扫描告警 |
| https://api.github.com/repos/{owner}/{repo}/code-scanning/alerts/{alert_number}/instances | GET | Query | JSON | 列出实例的代码扫描告警 |
| https://api.github.com/repos/{owner}/{repo}/code-scanning/analyses | GET | Query | JSON | 列出代码扫描分析用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/code-scanning/analyses/{analysis_id} | DELETE | Query | JSON | 删除代码扫描分析来自仓库 |
| https://api.github.com/repos/{owner}/{repo}/code-scanning/analyses/{analysis_id} | GET | Query | JSON | 获取代码扫描分析用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/code-scanning/codeql/databases | GET | Query | JSON | 列出 CodeQL 数据库用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/code-scanning/codeql/databases/{language} | DELETE | Query | JSON | 删除 CodeQL 数据库 |
| https://api.github.com/repos/{owner}/{repo}/code-scanning/codeql/databases/{language} | GET | Query | JSON | 获取 CodeQL 数据库用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/code-scanning/codeql/variant-analyses | POST | JSON | JSON | 创建 CodeQL 变体分析 |
| https://api.github.com/repos/{owner}/{repo}/code-scanning/codeql/variant-analyses/{codeql_variant_analysis_id} | GET | Query | JSON | 获取摘要的 CodeQL 变体分析 |
| https://api.github.com/repos/{owner}/{repo}/code-scanning/codeql/variant-analyses/{codeql_variant_analysis_id}/repos/{repo_owner}/{repo_name} | GET | Query | JSON | 获取分析状态的仓库内 CodeQL 变体分析 |
| https://api.github.com/repos/{owner}/{repo}/code-scanning/default-setup | GET | Query | JSON | 获取代码扫描默认设置配置 |
| https://api.github.com/repos/{owner}/{repo}/code-scanning/default-setup | PATCH | JSON | JSON | 更新代码扫描默认设置配置 |
| https://api.github.com/repos/{owner}/{repo}/code-scanning/sarifs | POST | JSON | JSON | 上传分析作为 SARIF 数据 |
| https://api.github.com/repos/{owner}/{repo}/code-scanning/sarifs/{sarif_id} | GET | Query | JSON | 获取信息关于 SARIF 上传 |

## 代码安全 Code Security（20 条）

> 官方分类标签 `code-security`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/enterprises/{enterprise}/code-security/configurations | GET | Query | JSON | 获取代码安全配置用于企业 |
| https://api.github.com/enterprises/{enterprise}/code-security/configurations | POST | JSON | JSON | 创建代码安全配置用于企业 |
| https://api.github.com/enterprises/{enterprise}/code-security/configurations/defaults | GET | Query | JSON | 获取默认代码安全配置用于企业 |
| https://api.github.com/enterprises/{enterprise}/code-security/configurations/{configuration_id} | DELETE | Query | JSON | 删除代码安全配置用于企业 |
| https://api.github.com/enterprises/{enterprise}/code-security/configurations/{configuration_id} | GET | Query | JSON | 获取代码安全配置的企业 |
| https://api.github.com/enterprises/{enterprise}/code-security/configurations/{configuration_id} | PATCH | JSON | JSON | 更新自定义代码安全配置用于企业 |
| https://api.github.com/enterprises/{enterprise}/code-security/configurations/{configuration_id}/attach | POST | JSON | JSON | 附加企业配置给仓库 |
| https://api.github.com/enterprises/{enterprise}/code-security/configurations/{configuration_id}/defaults | PUT | JSON | JSON | 设置代码安全配置作为默认用于企业 |
| https://api.github.com/enterprises/{enterprise}/code-security/configurations/{configuration_id}/repositories | GET | Query | JSON | 获取仓库关联含企业代码安全配置 |
| https://api.github.com/orgs/{org}/code-security/configurations | GET | Query | JSON | 获取代码安全配置用于组织 |
| https://api.github.com/orgs/{org}/code-security/configurations | POST | JSON | JSON | 创建代码安全配置 |
| https://api.github.com/orgs/{org}/code-security/configurations/defaults | GET | Query | JSON | 获取默认代码安全配置 |
| https://api.github.com/orgs/{org}/code-security/configurations/detach | DELETE | JSON | JSON | 分离配置来自仓库 |
| https://api.github.com/orgs/{org}/code-security/configurations/{configuration_id} | DELETE | Query | JSON | 删除代码安全配置 |
| https://api.github.com/orgs/{org}/code-security/configurations/{configuration_id} | GET | Query | JSON | 获取代码安全配置 |
| https://api.github.com/orgs/{org}/code-security/configurations/{configuration_id} | PATCH | JSON | JSON | 更新代码安全配置 |
| https://api.github.com/orgs/{org}/code-security/configurations/{configuration_id}/attach | POST | JSON | JSON | 附加配置给仓库 |
| https://api.github.com/orgs/{org}/code-security/configurations/{configuration_id}/defaults | PUT | JSON | JSON | 设置代码安全配置作为默认用于组织 |
| https://api.github.com/orgs/{org}/code-security/configurations/{configuration_id}/repositories | GET | Query | JSON | 获取仓库关联含代码安全配置 |
| https://api.github.com/repos/{owner}/{repo}/code-security-configuration | GET | Query | JSON | 获取代码安全配置关联含仓库 |

## 密钥扫描 Secret Scanning（17 条）

> 官方分类标签 `secret-scanning`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/orgs/{org}/secret-scanning/alerts | GET | Query | JSON | 列出密钥扫描告警用于组织 |
| https://api.github.com/orgs/{org}/secret-scanning/custom-patterns | DELETE | JSON | JSON | 批量删除组织自定义模式 |
| https://api.github.com/orgs/{org}/secret-scanning/custom-patterns | GET | Query | JSON | 列出组织自定义模式 |
| https://api.github.com/orgs/{org}/secret-scanning/custom-patterns | POST | JSON | JSON | 批量创建组织自定义模式 |
| https://api.github.com/orgs/{org}/secret-scanning/custom-patterns/{pattern_id} | PATCH | JSON | JSON | 更新组织自定义模式 |
| https://api.github.com/orgs/{org}/secret-scanning/pattern-configurations | GET | Query | JSON | 列出组织模式配置 |
| https://api.github.com/orgs/{org}/secret-scanning/pattern-configurations | PATCH | JSON | JSON | 更新组织模式配置 |
| https://api.github.com/repos/{owner}/{repo}/secret-scanning/alerts | GET | Query | JSON | 列出密钥扫描告警用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/secret-scanning/alerts/{alert_number} | GET | Query | JSON | 获取密钥扫描告警 |
| https://api.github.com/repos/{owner}/{repo}/secret-scanning/alerts/{alert_number} | PATCH | JSON | JSON | 更新密钥扫描告警 |
| https://api.github.com/repos/{owner}/{repo}/secret-scanning/alerts/{alert_number}/locations | GET | Query | JSON | 列出位置用于密钥扫描告警 |
| https://api.github.com/repos/{owner}/{repo}/secret-scanning/custom-patterns | DELETE | JSON | JSON | 批量删除仓库自定义模式 |
| https://api.github.com/repos/{owner}/{repo}/secret-scanning/custom-patterns | GET | Query | JSON | 列出仓库自定义模式 |
| https://api.github.com/repos/{owner}/{repo}/secret-scanning/custom-patterns | POST | JSON | JSON | 批量创建仓库自定义模式 |
| https://api.github.com/repos/{owner}/{repo}/secret-scanning/custom-patterns/{pattern_id} | PATCH | JSON | JSON | 更新仓库自定义模式 |
| https://api.github.com/repos/{owner}/{repo}/secret-scanning/push-protection-bypasses | POST | JSON | JSON | 创建推送保护绕过 |
| https://api.github.com/repos/{owner}/{repo}/secret-scanning/scan-history | GET | Query | JSON | 获取密钥扫描扫描历史用于仓库 |

## 安全公告 Security Advisories（10 条）

> 官方分类标签 `security-advisories`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/advisories | GET | Query | JSON | 列出全局安全公告 |
| https://api.github.com/advisories/{ghsa_id} | GET | Query | JSON | 获取全局安全公告 |
| https://api.github.com/orgs/{org}/security-advisories | GET | Query | JSON | 列出仓库安全公告用于组织 |
| https://api.github.com/repos/{owner}/{repo}/security-advisories | GET | Query | JSON | 列出仓库安全公告 |
| https://api.github.com/repos/{owner}/{repo}/security-advisories | POST | JSON | JSON | 创建仓库安全公告 |
| https://api.github.com/repos/{owner}/{repo}/security-advisories/reports | POST | JSON | JSON | 私有报告安全漏洞 |
| https://api.github.com/repos/{owner}/{repo}/security-advisories/{ghsa_id} | GET | Query | JSON | 获取仓库安全公告 |
| https://api.github.com/repos/{owner}/{repo}/security-advisories/{ghsa_id} | PATCH | JSON | JSON | 更新仓库安全公告 |
| https://api.github.com/repos/{owner}/{repo}/security-advisories/{ghsa_id}/cve | POST | Query | JSON | 请求 CVE 用于仓库安全公告 |
| https://api.github.com/repos/{owner}/{repo}/security-advisories/{ghsa_id}/forks | POST | Query | JSON | 创建临时私有 fork |

## OIDC（8 条）

> 官方分类标签 `oidc`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/enterprises/{enterprise}/actions/oidc/customization/properties/repo | GET | Query | JSON | 列出 OIDC 自定义属性包含用于企业 |
| https://api.github.com/enterprises/{enterprise}/actions/oidc/customization/properties/repo | POST | JSON | JSON | 创建 OIDC 自定义属性包含用于企业 |
| https://api.github.com/enterprises/{enterprise}/actions/oidc/customization/properties/repo/{custom_property_name} | DELETE | Query | JSON | 删除 OIDC 自定义属性包含用于企业 |
| https://api.github.com/orgs/{org}/actions/oidc/customization/properties/repo | GET | Query | JSON | 列出 OIDC 自定义属性包含用于组织 |
| https://api.github.com/orgs/{org}/actions/oidc/customization/properties/repo | POST | JSON | JSON | 创建 OIDC 自定义属性包含用于组织 |
| https://api.github.com/orgs/{org}/actions/oidc/customization/properties/repo/{custom_property_name} | DELETE | Query | JSON | 删除 OIDC 自定义属性包含用于组织 |
| https://api.github.com/orgs/{org}/actions/oidc/customization/sub | GET | Query | JSON | 获取自定义模板用于 OIDC 主题声明用于组织 |
| https://api.github.com/orgs/{org}/actions/oidc/customization/sub | PUT | JSON | JSON | 设置自定义模板用于 OIDC 主题声明用于组织 |

## 互动限制 Interactions（16 条）

> 官方分类标签 `interactions`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/orgs/{org}/interaction-limits | DELETE | Query | JSON | 移除互动限制用于组织 |
| https://api.github.com/orgs/{org}/interaction-limits | GET | Query | JSON | 获取互动限制用于组织 |
| https://api.github.com/orgs/{org}/interaction-limits | PUT | JSON | JSON | 设置互动限制用于组织 |
| https://api.github.com/orgs/{org}/interaction-limits/pulls/creation-cap | GET | Query | JSON | 获取拉取请求创建上限用于组织 |
| https://api.github.com/orgs/{org}/interaction-limits/pulls/creation-cap | PATCH | JSON | JSON | 更新拉取请求创建上限用于组织 |
| https://api.github.com/repos/{owner}/{repo}/interaction-limits | DELETE | Query | JSON | 移除互动限制用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/interaction-limits | GET | Query | JSON | 获取互动限制用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/interaction-limits | PUT | JSON | JSON | 设置互动限制用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/interaction-limits/pulls/bypass-list | DELETE | JSON | JSON | 移除用户来自拉取请求创建上限绕过列出用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/interaction-limits/pulls/bypass-list | GET | Query | JSON | 获取拉取请求创建上限绕过列出用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/interaction-limits/pulls/bypass-list | PUT | JSON | JSON | 添加用户给拉取请求创建上限绕过列出用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/interaction-limits/pulls/creation-cap | GET | Query | JSON | 获取拉取请求创建上限用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/interaction-limits/pulls/creation-cap | PATCH | JSON | JSON | 更新拉取请求创建上限用于仓库 |
| https://api.github.com/user/interaction-limits | DELETE | Query | JSON | 移除互动限制来自你的公开仓库 |
| https://api.github.com/user/interaction-limits | GET | Query | JSON | 获取互动限制用于你的公开仓库 |
| https://api.github.com/user/interaction-limits | PUT | JSON | JSON | 设置互动限制用于你的公开仓库 |

## 数据迁移 Migrations（22 条）

> 官方分类标签 `migrations`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/orgs/{org}/migrations | GET | Query | JSON | 列出组织迁移 |
| https://api.github.com/orgs/{org}/migrations | POST | JSON | JSON | 启动组织迁移 |
| https://api.github.com/orgs/{org}/migrations/{migration_id} | GET | Query | JSON | 获取组织迁移状态 |
| https://api.github.com/orgs/{org}/migrations/{migration_id}/archive | DELETE | Query | JSON | 删除组织迁移归档 |
| https://api.github.com/orgs/{org}/migrations/{migration_id}/archive | GET | Query | JSON | 下载组织迁移归档 |
| https://api.github.com/orgs/{org}/migrations/{migration_id}/repos/{repo_name}/lock | DELETE | Query | JSON | 解锁组织仓库 |
| https://api.github.com/orgs/{org}/migrations/{migration_id}/repositories | GET | Query | JSON | 列出仓库内组织迁移 |
| https://api.github.com/repos/{owner}/{repo}/import | DELETE | Query | JSON | 【已废弃】取消导入 |
| https://api.github.com/repos/{owner}/{repo}/import | GET | Query | JSON | 【已废弃】获取导入状态 |
| https://api.github.com/repos/{owner}/{repo}/import | PATCH | JSON | JSON | 【已废弃】更新导入 |
| https://api.github.com/repos/{owner}/{repo}/import | PUT | JSON | JSON | 【已废弃】启动导入 |
| https://api.github.com/repos/{owner}/{repo}/import/authors | GET | Query | JSON | 【已废弃】获取提交作者 |
| https://api.github.com/repos/{owner}/{repo}/import/authors/{author_id} | PATCH | JSON | JSON | 【已废弃】映射提交作者 |
| https://api.github.com/repos/{owner}/{repo}/import/large_files | GET | Query | JSON | 【已废弃】获取大型文件 |
| https://api.github.com/repos/{owner}/{repo}/import/lfs | PATCH | JSON | JSON | 【已废弃】更新 Git LFS 偏好 |
| https://api.github.com/user/migrations | GET | Query | JSON | 列出用户迁移 |
| https://api.github.com/user/migrations | POST | JSON | JSON | 启动用户迁移 |
| https://api.github.com/user/migrations/{migration_id} | GET | Query | JSON | 获取用户迁移状态 |
| https://api.github.com/user/migrations/{migration_id}/archive | DELETE | Query | JSON | 删除用户迁移归档 |
| https://api.github.com/user/migrations/{migration_id}/archive | GET | Query | JSON | 下载用户迁移归档 |
| https://api.github.com/user/migrations/{migration_id}/repos/{repo_name}/lock | DELETE | Query | JSON | 解锁用户仓库 |
| https://api.github.com/user/migrations/{migration_id}/repositories | GET | Query | JSON | 列出仓库用于用户迁移 |

## 搜索 Search（7 条）

> 官方分类标签 `search`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/search/code | GET | Query | JSON | 搜索代码 |
| https://api.github.com/search/commits | GET | Query | JSON | 搜索提交 |
| https://api.github.com/search/issues | GET | Query | JSON | 搜索议题与拉取请求 |
| https://api.github.com/search/labels | GET | Query | JSON | 搜索标签 |
| https://api.github.com/search/repositories | GET | Query | JSON | 搜索仓库 |
| https://api.github.com/search/topics | GET | Query | JSON | 搜索主题 |
| https://api.github.com/search/users | GET | Query | JSON | 搜索用户 |

## Copilot（31 条）

> 官方分类标签 `copilot`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/enterprises/{enterprise}/copilot/metrics/reports/enterprise-1-day | GET | Query | JSON | 获取 Copilot 企业用量指标用于特定当日 |
| https://api.github.com/enterprises/{enterprise}/copilot/metrics/reports/enterprise-28-day/latest | GET | Query | JSON | 获取 Copilot 企业用量指标 |
| https://api.github.com/enterprises/{enterprise}/copilot/metrics/reports/repos-1-day | GET | Query | JSON | 获取 Copilot 企业仓库报告用于特定当日 |
| https://api.github.com/enterprises/{enterprise}/copilot/metrics/reports/user-teams-1-day | GET | Query | JSON | 获取 Copilot 企业用户团队报告用于特定当日 |
| https://api.github.com/enterprises/{enterprise}/copilot/metrics/reports/users-1-day | GET | Query | JSON | 获取 Copilot 用户用量指标用于特定当日 |
| https://api.github.com/enterprises/{enterprise}/copilot/metrics/reports/users-28-day/latest | GET | Query | JSON | 获取 Copilot 用户用量指标 |
| https://api.github.com/enterprises/{enterprise}/copilot/policies/coding_agent | PUT | JSON | JSON | 设置编码智能体策略用于企业 |
| https://api.github.com/enterprises/{enterprise}/copilot/policies/coding_agent/organizations | DELETE | JSON | JSON | 移除组织来自企业编码智能体策略 |
| https://api.github.com/enterprises/{enterprise}/copilot/policies/coding_agent/organizations | POST | JSON | JSON | 添加组织给企业编码智能体策略 |
| https://api.github.com/orgs/{org}/copilot/billing | GET | Query | JSON | 获取 Copilot 席位信息与设置用于组织 |
| https://api.github.com/orgs/{org}/copilot/billing/seats | GET | Query | JSON | 列出全部 Copilot 席位作业用于组织 |
| https://api.github.com/orgs/{org}/copilot/billing/selected_teams | DELETE | JSON | JSON | 移除团队来自 Copilot 订阅用于组织 |
| https://api.github.com/orgs/{org}/copilot/billing/selected_teams | POST | JSON | JSON | 添加团队给 Copilot 订阅用于组织 |
| https://api.github.com/orgs/{org}/copilot/billing/selected_users | DELETE | JSON | JSON | 移除用户来自 Copilot 订阅用于组织 |
| https://api.github.com/orgs/{org}/copilot/billing/selected_users | POST | JSON | JSON | 添加用户给 Copilot 订阅用于组织 |
| https://api.github.com/orgs/{org}/copilot/coding-agent/permissions | GET | Query | JSON | 获取 Copilot 云智能体权限用于组织 |
| https://api.github.com/orgs/{org}/copilot/coding-agent/permissions | PUT | JSON | JSON | 设置 Copilot 云智能体权限用于组织 |
| https://api.github.com/orgs/{org}/copilot/coding-agent/permissions/repositories | GET | Query | JSON | 列出仓库已启用用于 Copilot 云智能体内组织 |
| https://api.github.com/orgs/{org}/copilot/coding-agent/permissions/repositories | PUT | JSON | JSON | 设置已选仓库用于 Copilot 云智能体内组织 |
| https://api.github.com/orgs/{org}/copilot/coding-agent/permissions/repositories/{repository_id} | DELETE | Query | JSON | 禁用仓库用于 Copilot 云智能体内组织 |
| https://api.github.com/orgs/{org}/copilot/coding-agent/permissions/repositories/{repository_id} | PUT | Query | JSON | 启用仓库用于 Copilot 云智能体内组织 |
| https://api.github.com/orgs/{org}/copilot/content_exclusion | GET | Query | JSON | 获取 Copilot 内容排除规则用于组织 |
| https://api.github.com/orgs/{org}/copilot/content_exclusion | PUT | JSON | JSON | 设置 Copilot 内容排除规则用于组织 |
| https://api.github.com/orgs/{org}/copilot/metrics/reports/organization-1-day | GET | Query | JSON | 获取 Copilot 组织用量指标用于特定当日 |
| https://api.github.com/orgs/{org}/copilot/metrics/reports/organization-28-day/latest | GET | Query | JSON | 获取 Copilot 组织用量指标 |
| https://api.github.com/orgs/{org}/copilot/metrics/reports/repos-1-day | GET | Query | JSON | 获取 Copilot 组织仓库报告用于特定当日 |
| https://api.github.com/orgs/{org}/copilot/metrics/reports/user-teams-1-day | GET | Query | JSON | 获取 Copilot 组织用户团队报告用于特定当日 |
| https://api.github.com/orgs/{org}/copilot/metrics/reports/users-1-day | GET | Query | JSON | 获取 Copilot 组织用户用量指标用于特定当日 |
| https://api.github.com/orgs/{org}/copilot/metrics/reports/users-28-day/latest | GET | Query | JSON | 获取 Copilot 组织用户用量指标 |
| https://api.github.com/orgs/{org}/members/{username}/copilot | GET | Query | JSON | 获取 Copilot 席位作业详情用于用户 |
| https://api.github.com/repos/{owner}/{repo}/copilot/cloud-agent/configuration | GET | Query | JSON | 获取 Copilot 云智能体配置用于仓库 |

## Copilot Spaces（28 条）

> 官方分类标签 `copilot-spaces`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/orgs/{org}/copilot-spaces | GET | Query | JSON | 列出组织 Copilot 空间 |
| https://api.github.com/orgs/{org}/copilot-spaces | POST | JSON | JSON | 创建组织 Copilot 空间 |
| https://api.github.com/orgs/{org}/copilot-spaces/{space_number} | DELETE | Query | JSON | 删除组织 Copilot 空间 |
| https://api.github.com/orgs/{org}/copilot-spaces/{space_number} | GET | Query | JSON | 获取组织 Copilot 空间 |
| https://api.github.com/orgs/{org}/copilot-spaces/{space_number} | PUT | JSON | JSON | 设置组织 Copilot 空间 |
| https://api.github.com/orgs/{org}/copilot-spaces/{space_number}/collaborators | GET | Query | JSON | 列出协作者用于组织 Copilot 空间 |
| https://api.github.com/orgs/{org}/copilot-spaces/{space_number}/collaborators | POST | JSON | JSON | 添加协作者给组织 Copilot 空间 |
| https://api.github.com/orgs/{org}/copilot-spaces/{space_number}/collaborators/{actor_type}/{actor_identifier} | DELETE | Query | JSON | 移除协作者来自组织 Copilot 空间 |
| https://api.github.com/orgs/{org}/copilot-spaces/{space_number}/collaborators/{actor_type}/{actor_identifier} | PUT | JSON | JSON | 设置协作者角色用于组织 Copilot 空间 |
| https://api.github.com/orgs/{org}/copilot-spaces/{space_number}/resources | GET | Query | JSON | 列出资源用于组织 Copilot 空间 |
| https://api.github.com/orgs/{org}/copilot-spaces/{space_number}/resources | POST | JSON | JSON | 创建资源用于组织 Copilot 空间 |
| https://api.github.com/orgs/{org}/copilot-spaces/{space_number}/resources/{space_resource_id} | DELETE | Query | JSON | 删除资源来自组织 Copilot 空间 |
| https://api.github.com/orgs/{org}/copilot-spaces/{space_number}/resources/{space_resource_id} | GET | Query | JSON | 获取资源用于组织 Copilot 空间 |
| https://api.github.com/orgs/{org}/copilot-spaces/{space_number}/resources/{space_resource_id} | PUT | JSON | JSON | 设置资源用于组织 Copilot 空间 |
| https://api.github.com/users/{username}/copilot-spaces | GET | Query | JSON | 列出 Copilot 空间用于用户 |
| https://api.github.com/users/{username}/copilot-spaces | POST | JSON | JSON | 创建 Copilot 空间用于用户 |
| https://api.github.com/users/{username}/copilot-spaces/{space_number} | DELETE | Query | JSON | 删除 Copilot 空间用于用户 |
| https://api.github.com/users/{username}/copilot-spaces/{space_number} | GET | Query | JSON | 获取 Copilot 空间用于用户 |
| https://api.github.com/users/{username}/copilot-spaces/{space_number} | PUT | JSON | JSON | 设置 Copilot 空间用于用户 |
| https://api.github.com/users/{username}/copilot-spaces/{space_number}/collaborators | GET | Query | JSON | 列出协作者用于 Copilot 空间用于用户 |
| https://api.github.com/users/{username}/copilot-spaces/{space_number}/collaborators | POST | JSON | JSON | 添加协作者给 Copilot 空间用于用户 |
| https://api.github.com/users/{username}/copilot-spaces/{space_number}/collaborators/{actor_type}/{actor_identifier} | DELETE | Query | JSON | 移除协作者来自 Copilot 空间用于用户 |
| https://api.github.com/users/{username}/copilot-spaces/{space_number}/collaborators/{actor_type}/{actor_identifier} | PUT | JSON | JSON | 设置协作者角色用于 Copilot 空间用于用户 |
| https://api.github.com/users/{username}/copilot-spaces/{space_number}/resources | GET | Query | JSON | 列出资源用于 Copilot 空间用于用户 |
| https://api.github.com/users/{username}/copilot-spaces/{space_number}/resources | POST | JSON | JSON | 创建资源用于 Copilot 空间用于用户 |
| https://api.github.com/users/{username}/copilot-spaces/{space_number}/resources/{space_resource_id} | DELETE | Query | JSON | 删除资源来自 Copilot 空间用于用户 |
| https://api.github.com/users/{username}/copilot-spaces/{space_number}/resources/{space_resource_id} | GET | Query | JSON | 获取资源用于 Copilot 空间用于用户 |
| https://api.github.com/users/{username}/copilot-spaces/{space_number}/resources/{space_resource_id} | PUT | JSON | JSON | 设置资源用于 Copilot 空间用于用户 |

## Agents 智能体（30 条）

> 官方分类标签 `agents`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/orgs/{org}/agents/secrets | GET | Query | JSON | 列出组织密钥 |
| https://api.github.com/orgs/{org}/agents/secrets/public-key | GET | Query | JSON | 获取组织公开密钥 |
| https://api.github.com/orgs/{org}/agents/secrets/{secret_name} | DELETE | Query | JSON | 删除组织密钥 |
| https://api.github.com/orgs/{org}/agents/secrets/{secret_name} | GET | Query | JSON | 获取组织密钥 |
| https://api.github.com/orgs/{org}/agents/secrets/{secret_name} | PUT | JSON | JSON | 创建或更新组织密钥 |
| https://api.github.com/orgs/{org}/agents/secrets/{secret_name}/repositories | GET | Query | JSON | 列出已选仓库用于组织密钥 |
| https://api.github.com/orgs/{org}/agents/secrets/{secret_name}/repositories | PUT | JSON | JSON | 设置已选仓库用于组织密钥 |
| https://api.github.com/orgs/{org}/agents/secrets/{secret_name}/repositories/{repository_id} | DELETE | Query | JSON | 移除已选仓库来自组织密钥 |
| https://api.github.com/orgs/{org}/agents/secrets/{secret_name}/repositories/{repository_id} | PUT | Query | JSON | 添加已选仓库给组织密钥 |
| https://api.github.com/orgs/{org}/agents/variables | GET | Query | JSON | 列出组织变量 |
| https://api.github.com/orgs/{org}/agents/variables | POST | JSON | JSON | 创建组织变量 |
| https://api.github.com/orgs/{org}/agents/variables/{name} | DELETE | Query | JSON | 删除组织变量 |
| https://api.github.com/orgs/{org}/agents/variables/{name} | GET | Query | JSON | 获取组织变量 |
| https://api.github.com/orgs/{org}/agents/variables/{name} | PATCH | JSON | JSON | 更新组织变量 |
| https://api.github.com/orgs/{org}/agents/variables/{name}/repositories | GET | Query | JSON | 列出已选仓库用于组织变量 |
| https://api.github.com/orgs/{org}/agents/variables/{name}/repositories | PUT | JSON | JSON | 设置已选仓库用于组织变量 |
| https://api.github.com/orgs/{org}/agents/variables/{name}/repositories/{repository_id} | DELETE | Query | JSON | 移除已选仓库来自组织变量 |
| https://api.github.com/orgs/{org}/agents/variables/{name}/repositories/{repository_id} | PUT | Query | JSON | 添加已选仓库给组织变量 |
| https://api.github.com/repos/{owner}/{repo}/agents/organization-secrets | GET | Query | JSON | 列出仓库组织密钥 |
| https://api.github.com/repos/{owner}/{repo}/agents/organization-variables | GET | Query | JSON | 列出仓库组织变量 |
| https://api.github.com/repos/{owner}/{repo}/agents/secrets | GET | Query | JSON | 列出仓库密钥 |
| https://api.github.com/repos/{owner}/{repo}/agents/secrets/public-key | GET | Query | JSON | 获取仓库公开密钥 |
| https://api.github.com/repos/{owner}/{repo}/agents/secrets/{secret_name} | DELETE | Query | JSON | 删除仓库密钥 |
| https://api.github.com/repos/{owner}/{repo}/agents/secrets/{secret_name} | GET | Query | JSON | 获取仓库密钥 |
| https://api.github.com/repos/{owner}/{repo}/agents/secrets/{secret_name} | PUT | JSON | JSON | 创建或更新仓库密钥 |
| https://api.github.com/repos/{owner}/{repo}/agents/variables | GET | Query | JSON | 列出仓库变量 |
| https://api.github.com/repos/{owner}/{repo}/agents/variables | POST | JSON | JSON | 创建仓库变量 |
| https://api.github.com/repos/{owner}/{repo}/agents/variables/{name} | DELETE | Query | JSON | 删除仓库变量 |
| https://api.github.com/repos/{owner}/{repo}/agents/variables/{name} | GET | Query | JSON | 获取仓库变量 |
| https://api.github.com/repos/{owner}/{repo}/agents/variables/{name} | PATCH | JSON | JSON | 更新仓库变量 |

## 智能体任务 Agent Tasks（5 条）

> 官方分类标签 `agent-tasks`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/agents/repos/{owner}/{repo}/tasks | GET | Query | JSON | 列出任务用于仓库 |
| https://api.github.com/agents/repos/{owner}/{repo}/tasks | POST | JSON | JSON | 启动任务 |
| https://api.github.com/agents/repos/{owner}/{repo}/tasks/{task_id} | GET | Query | JSON | 获取任务按仓库 |
| https://api.github.com/agents/tasks | GET | Query | JSON | 列出任务 |
| https://api.github.com/agents/tasks/{task_id} | GET | Query | JSON | 获取任务按 ID |

## 托管计算 Hosted Compute（6 条）

> 官方分类标签 `hosted-compute`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/orgs/{org}/settings/network-configurations | GET | Query | JSON | 列出托管计算网络配置用于组织 |
| https://api.github.com/orgs/{org}/settings/network-configurations | POST | JSON | JSON | 创建托管计算网络配置用于组织 |
| https://api.github.com/orgs/{org}/settings/network-configurations/{network_configuration_id} | DELETE | Query | JSON | 删除托管计算网络配置来自组织 |
| https://api.github.com/orgs/{org}/settings/network-configurations/{network_configuration_id} | GET | Query | JSON | 获取托管计算网络配置用于组织 |
| https://api.github.com/orgs/{org}/settings/network-configurations/{network_configuration_id} | PATCH | JSON | JSON | 更新托管计算网络配置用于组织 |
| https://api.github.com/orgs/{org}/settings/network-settings/{network_settings_id} | GET | Query | JSON | 获取托管计算网络设置资源用于组织 |

## 私有注册表 Private Registries（6 条）

> 官方分类标签 `private-registries`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/orgs/{org}/private-registries | GET | Query | JSON | 列出私有注册表用于组织 |
| https://api.github.com/orgs/{org}/private-registries | POST | JSON | JSON | 创建私有注册表用于组织 |
| https://api.github.com/orgs/{org}/private-registries/public-key | GET | Query | JSON | 获取私有注册表公开密钥用于组织 |
| https://api.github.com/orgs/{org}/private-registries/{secret_name} | DELETE | Query | JSON | 删除私有注册表用于组织 |
| https://api.github.com/orgs/{org}/private-registries/{secret_name} | GET | Query | JSON | 获取私有注册表用于组织 |
| https://api.github.com/orgs/{org}/private-registries/{secret_name} | PATCH | JSON | JSON | 更新私有注册表用于组织 |

## Classroom 课堂（6 条）

> 官方分类标签 `classroom`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/assignments/{assignment_id} | GET | Query | JSON | 【已废弃】已关闭 - 获取作业 |
| https://api.github.com/assignments/{assignment_id}/accepted_assignments | GET | Query | JSON | 【已废弃】已关闭 - 列出已接受作业用于作业 |
| https://api.github.com/assignments/{assignment_id}/grades | GET | Query | JSON | 【已废弃】已关闭 - 获取作业成绩 |
| https://api.github.com/classrooms | GET | Query | JSON | 【已废弃】已关闭 - 列出课堂 |
| https://api.github.com/classrooms/{classroom_id} | GET | Query | JSON | 【已废弃】已关闭 - 获取课堂 |
| https://api.github.com/classrooms/{classroom_id}/assignments | GET | Query | JSON | 【已废弃】已关闭 - 列出作业用于课堂 |

## 计费 Billing（13 条）

> 官方分类标签 `billing`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/organizations/{org}/settings/billing/ai_credit/usage | GET | Query | JSON | 获取计费 AI 额度用量报告用于组织 |
| https://api.github.com/organizations/{org}/settings/billing/budgets | GET | Query | JSON | 获取全部预算用于组织 |
| https://api.github.com/organizations/{org}/settings/billing/budgets | POST | JSON | JSON | 创建预算用于组织 |
| https://api.github.com/organizations/{org}/settings/billing/budgets/{budget_id} | DELETE | Query | JSON | 删除预算用于组织 |
| https://api.github.com/organizations/{org}/settings/billing/budgets/{budget_id} | GET | Query | JSON | 获取预算按 ID 用于组织 |
| https://api.github.com/organizations/{org}/settings/billing/budgets/{budget_id} | PATCH | JSON | JSON | 更新预算用于组织 |
| https://api.github.com/organizations/{org}/settings/billing/premium_request/usage | GET | Query | JSON | 获取计费高级请求用量报告用于组织 |
| https://api.github.com/organizations/{org}/settings/billing/usage | GET | Query | JSON | 获取计费用量报告用于组织 |
| https://api.github.com/organizations/{org}/settings/billing/usage/summary | GET | Query | JSON | 获取计费用量摘要用于组织 |
| https://api.github.com/users/{username}/settings/billing/ai_credit/usage | GET | Query | JSON | 获取计费 AI 额度用量报告用于用户 |
| https://api.github.com/users/{username}/settings/billing/premium_request/usage | GET | Query | JSON | 获取计费高级请求用量报告用于用户 |
| https://api.github.com/users/{username}/settings/billing/usage | GET | Query | JSON | 获取计费用量报告用于用户 |
| https://api.github.com/users/{username}/settings/billing/usage/summary | GET | Query | JSON | 获取计费用量摘要用于用户 |

## 营销活动 Campaigns（5 条）

> 官方分类标签 `campaigns`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/orgs/{org}/campaigns | GET | Query | JSON | 列出营销活动用于组织 |
| https://api.github.com/orgs/{org}/campaigns | POST | JSON | JSON | 创建营销活动用于组织 |
| https://api.github.com/orgs/{org}/campaigns/{campaign_number} | DELETE | Query | JSON | 删除营销活动用于组织 |
| https://api.github.com/orgs/{org}/campaigns/{campaign_number} | GET | Query | JSON | 获取营销活动用于组织 |
| https://api.github.com/orgs/{org}/campaigns/{campaign_number} | PATCH | JSON | JSON | 更新营销活动 |

## 依赖图 Dependency Graph（5 条）

> 官方分类标签 `dependency-graph`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/repos/{owner}/{repo}/dependency-graph/compare/{basehead} | GET | Query | JSON | 获取差异的依赖之间提交 |
| https://api.github.com/repos/{owner}/{repo}/dependency-graph/sbom | GET | Query | JSON | 【已废弃】导出软件计费的材料 (SBOM) 用于仓库. |
| https://api.github.com/repos/{owner}/{repo}/dependency-graph/sbom/fetch-report/{sbom_uuid} | GET | Query | JSON | 获取软件计费的材料 (SBOM) 用于仓库. |
| https://api.github.com/repos/{owner}/{repo}/dependency-graph/sbom/generate-report | GET | Query | JSON | 请求生成的软件计费的材料 (SBOM) 用于仓库. |
| https://api.github.com/repos/{owner}/{repo}/dependency-graph/snapshots | POST | JSON | JSON | 创建快照的依赖用于仓库 |

## 代码质量 Code Quality（4 条）

> 官方分类标签 `code-quality`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/repos/{owner}/{repo}/code-quality/findings | GET | Query | JSON | 列出代码质量发现用于仓库 |
| https://api.github.com/repos/{owner}/{repo}/code-quality/findings/{finding_number} | GET | Query | JSON | 获取代码质量发现 |
| https://api.github.com/repos/{owner}/{repo}/code-quality/setup | GET | Query | JSON | 获取代码质量设置配置 |
| https://api.github.com/repos/{owner}/{repo}/code-quality/setup | PATCH | JSON | JSON | 更新代码质量设置配置 |

## 许可证 Licenses（3 条）

> 官方分类标签 `licenses`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/licenses | GET | Query | JSON | 获取全部常用使用许可证 |
| https://api.github.com/licenses/{license} | GET | Query | JSON | 获取许可证 |
| https://api.github.com/repos/{owner}/{repo}/license | GET | Query | JSON | 获取许可证用于仓库 |

## 行为准则 Codes of Conduct（2 条）

> 官方分类标签 `codes-of-conduct`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/codes_of_conduct | GET | Query | JSON | 获取全部代码的行为准则 |
| https://api.github.com/codes_of_conduct/{key} | GET | Query | JSON | 获取代码的行为准则 |

## GitIgnore 模板（2 条）

> 官方分类标签 `gitignore`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/gitignore/templates | GET | Query | JSON | 获取全部 gitignore 模板 |
| https://api.github.com/gitignore/templates/{name} | GET | Query | JSON | 获取 gitignore 模板 |

## Markdown 渲染（2 条）

> 官方分类标签 `markdown`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/markdown | POST | JSON | JSON | 渲染 Markdown 文档 |
| https://api.github.com/markdown/raw | POST | JSON | JSON | 渲染 Markdown 文档内原始模式 |

## 凭证 Credentials（1 条）

> 官方分类标签 `credentials`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/credentials/revoke | POST | JSON | JSON | 撤销列出的凭证 |

## 表情符号 Emojis（1 条）

> 官方分类标签 `emojis`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/emojis | GET | Query | JSON | 获取表情 |

## 速率限制 Rate Limit（1 条）

> 官方分类标签 `rate-limit`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/rate_limit | GET | Query | JSON | 获取速率限制状态用于已认证用户 |

## 元信息 Meta（5 条）

> 官方分类标签 `meta`，以下按路径排序，路径均为 `https://api.github.com` 下的完整绝对 URL。

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| --- | --- | --- | --- | --- |
| https://api.github.com/ | GET | Query | JSON | GitHub API 根 |
| https://api.github.com/meta | GET | Query | JSON | 获取 GitHub 元信息 |
| https://api.github.com/octocat | GET | Query | JSON | 获取 Octocat |
| https://api.github.com/versions | GET | Query | JSON | 获取全部 API 版本 |
| https://api.github.com/zen | GET | Query | JSON | 获取 Zen 的 GitHub |
## 附：GitHub 相关域名与子域名清单

GitHub 采用「单一 API 网关」架构——所有 REST 接口统一挂载在 `api.github.com`，与百度/小红书的分散子域不同。以下按用途分组：

**A. 承载 API 接口**

| 域名 | 说明 |
| --- | --- |
| `api.github.com` | REST API 主网关，本文档全部 1232 条接口挂载于此 |
| `uploads.github.com` | Release 资产、LFS 等大文件上传专用主机 |
| `api.githubapp.com` | GitHub App 相关的 Webhook/事件签名域名 |
| `githubapp.com` | OAuth 应用回调与授权域名 |

**B. Web 站点与资源存储（非 API，已排除）**

| 域名 | 说明 |
| --- | --- |
| `github.com` | 主站（HTML 页面、仓库浏览、下载） |
| `codeload.github.com` | 仓库归档 tar.gz/zipball 下载 |
| `raw.githubusercontent.com` | 仓库原始文件（Raw）访问 |
| `objects.githubusercontent.com` | Release 资产与文件对象存储 CDN |
| `gist.github.com` | Gist 代码片段站点（数据仍走 api.github.com/gists） |
| `avatars.githubusercontent.com` | 用户/组织头像 CDN |
| `camo.githubusercontent.com` | README 外部图片代理缓存 |
| `ghcr.io` | GitHub Container Registry 容器镜像仓库 |
| `pages.github.com` / `*.github.io` | GitHub Pages 静态站点托管 |
| `marketplace.github.com` | GitHub Marketplace 应用市场 |
| `copilot-proxy.githubusercontent.com` | Copilot 代码补全代理端点 |
| `ssh.github.com` / `git.ssh.github.com` | SSH 克隆与访问 |

**C. 监控、文档与内网（已排除）**

| 域名 | 说明 |
| --- | --- |
| `docs.github.com` | 官方文档站（含 REST 参考，非接口） |
| `githubstatus.com` | 服务状态页（独立 Statuspage） |
| `desktop.github.com` / `cli.github.com` | Desktop/CLI 产品页 |
| `sentry.github.com` / `collector.github.com` | 遥测与错误上报（已排除） |
| `education.github.com` | GitHub Education 教育版站点 |
| `securitylab.github.com` | GitHub Security Lab 研究站点 |
| `about.github.com` / `resources.github.com` | 品牌与营销站点 |

## 二、统计汇总

- **操作条目总数**：1232
- **唯一 API 路径**：816（同一路径可对应多个 HTTP 方法）
- **官方分类（tags）**：44 个
- **已废弃接口**：38 条（用途说明前标注【已废弃】）
- **鉴权要求**：绝大多数接口需 `Authorization` 头；少数公开接口（如 emoji、gitignore、licenses、meta/root）可匿名访问

**按 HTTP 方法统计**

| 方法 | 数量 |
| --- | --- |
| GET | 644 |
| POST | 193 |
| PUT | 134 |
| PATCH | 73 |
| DELETE | 188 |

**按请求格式统计**

| 请求格式 | 数量 | 说明 |
| --- | --- | --- |
| Query | 882 | GET/DELETE 等无请求体，参数走 Query 与路径变量 |
| JSON | 349 | POST/PUT/PATCH 携带 `application/json` 请求体（含 text/plain、text/x-markdown 等特殊文本体） |
| multipart | 1 | Release 资产上传等 `application/octet-stream` 二进制体 |

**按业务分类统计（官方 tag，降序）**

| 分类 | tag | 接口数 |
| --- | --- | --- |
| 仓库 Repositories | `repos` | 203 |
| GitHub Actions 工作流 | `actions` | 200 |
| 组织 Organizations | `orgs` | 116 |
| 议题 Issues | `issues` | 61 |
| Codespaces 开发环境 | `codespaces` | 48 |
| 用户 Users | `users` | 47 |
| GitHub Apps 应用 | `apps` | 37 |
| 拉取请求 Pull Requests | `pulls` | 35 |
| 活动与通知 Activity | `activity` | 34 |
| 团队 Teams | `teams` | 32 |
| Copilot | `copilot` | 31 |
| Agents 智能体 | `agents` | 30 |
| Copilot Spaces | `copilot-spaces` | 28 |
| Packages 软件包 | `packages` | 27 |
| Projects 项目 | `projects` | 26 |
| Dependabot | `dependabot` | 25 |
| 代码扫描 Code Scanning | `code-scanning` | 25 |
| 数据迁移 Migrations | `migrations` | 22 |
| 代码安全 Code Security | `code-security` | 20 |
| Gist 代码片段 | `gists` | 20 |
| 密钥扫描 Secret Scanning | `secret-scanning` | 17 |
| 互动限制 Interactions | `interactions` | 16 |
| 表情回应 Reactions | `reactions` | 15 |
| 计费 Billing | `billing` | 13 |
| Git 数据 Git Data | `git` | 13 |
| 检查 Checks | `checks` | 12 |
| 安全公告 Security Advisories | `security-advisories` | 10 |
| OIDC | `oidc` | 8 |
| 搜索 Search | `search` | 7 |
| Classroom 课堂 | `classroom` | 6 |
| 私有注册表 Private Registries | `private-registries` | 6 |
| 托管计算 Hosted Compute | `hosted-compute` | 6 |
| 元信息 Meta | `meta` | 5 |
| 智能体任务 Agent Tasks | `agent-tasks` | 5 |
| 营销活动 Campaigns | `campaigns` | 5 |
| 依赖图 Dependency Graph | `dependency-graph` | 5 |
| 代码质量 Code Quality | `code-quality` | 4 |
| 许可证 Licenses | `licenses` | 3 |
| 行为准则 Codes of Conduct | `codes-of-conduct` | 2 |
| GitIgnore 模板 | `gitignore` | 2 |
| Markdown 渲染 | `markdown` | 2 |
| 凭证 Credentials | `credentials` | 1 |
| 表情符号 Emojis | `emojis` | 1 |
| 速率限制 Rate Limit | `rate-limit` | 1 |

**按路径首段资源前缀统计（Top 20）**

| 资源前缀 | 接口数 |
| --- | --- |
| `/repos` | 532 |
| `/orgs` | 386 |
| `/user` | 94 |
| `/users` | 65 |
| `/enterprises` | 29 |
| `/gists` | 19 |
| `/teams` | 16 |
| `/organizations` | 14 |
| `/app` | 13 |
| `/notifications` | 8 |
| `/search` | 7 |
| `/marketplace_listing` | 6 |
| `/agents` | 5 |
| `/applications` | 5 |
| `/assignments` | 3 |
| `/classrooms` | 3 |
| `/advisories` | 2 |
| `/codes_of_conduct` | 2 |
| `/gitignore` | 2 |
| `/installation` | 2 |

> **免责与范围说明**：本文档基于 GitHub 官方公开的 OpenAPI 规范自动整理，接口清单、HTTP 方法、请求体类型均来自规范原文，用途说明由官方英文 `summary` 翻译而来，可能存在个别术语翻译不够地道，但不影响接口定位。GitHub 持续迭代 API，实际调用请以 [`docs.github.com/rest`](https://docs.github.com/rest) 最新文档与 `X-GitHub-Api-Version` 基线为准；已废弃接口随时可能移除。GraphQL API（`POST /graphql`）为独立体系，不在本 REST 文档范围内。
