# modelscope.cn API 接口文档

> 站点：ModelScope 魔搭社区 — 开源模型社区（模型 / 数据集 / 论文 / 创空间 / 智能体 / MCP / Notebook）
> 抓取时间：2026-10-05
> 抓取范围：`https://modelscope.cn/`（302 → `/home`）→ 单入口 umi bundle `g.alicdn.com/sail-web/maas/2.13.161/umi.js`（10.4 MB）→ 从 webpack chunk 映射表解析出 **396 个 chunk 名称，实际下载成功 395 个**（约 54 MB），覆盖 `layouts` 与全部 `p__*` 业务页
> 提取方式：无 sourcemap。定位自研请求层 `F` 类（`new F("/api").http` 与 `new F("/openapi").http` 两个客户端），从方法枚举模块 `749337` 解出 `HT=GET / a4=POST / uO=PUT / yY=DELETE / Q0=PATCH`，再按真实调用形态 `(0, X.fn)("/path", M.HT, params)` 全量正则提取
> **接口总数：432 条**（`/api/**` 409 条、`/openapi/**` 11 条、`/ds/**` 12 条）
> 已排除：阿里云 CDN 与 OSS、第三方 SDK 与站点（B站/知乎/小红书/抖音/GitHub/HuggingFace/微信QQ微博登录）、APM 埋点（`arms-retcode*`）、字体图标资源、`.wasm` 二进制

---

## 一、通用约定

| 项目 | 说明 |
| :--- | :--- |
| 主 API 入口 | `https://www.modelscope.cn/api` + 路径，如 `/api/v1/models` |
| OpenAPI 入口 | `https://www.modelscope.cn/openapi` + 路径，如 `/openapi/v1/agents` |
| 数据集平台 | `https://www.modelscope.cn/ds` + 路径，如 `/ds/v1/console/dataset/list` |
| 鉴权 | Cookie `csrf_token` / `xsrf`；拦截器自动注入 `X-Modelscope-Trace-Id`、`X-CSRF-TOKEN`（写操作）、`x-modelscope-accept-language` |
| 登录态探测 | `GET /api/v1/users/login/info` |
| 请求格式 | `GET`/`DELETE`/`PATCH` 走 `params`；`POST`/`PUT` 走 JSON body；文件上传走 `FormData`（`multipart/form-data`） |
| 返回格式 | JSON。列表类为 `{ Data, TotalCount, RequestId }`，`RequestId` 为链路追踪 ID |
| 未登录返回 | `401` |
| 路径变量 | 表中 `{id}` 代表运行时拼接的变量，实际调用如 `/v1/papers/{id}` |

**请求方法与语义对照**：`GET`=查询、`POST`=创建/提交、`PUT`=整体更新、`PATCH`=局部修改、`DELETE`=删除。同一路径出现多个方法时表示该资源支持增删改查多种操作。


## 用户（23 条）

> 账号资料、绑定关系、令牌与站内消息

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/users/authorized/check | GET | Query | JSON | 检查授权 |
| https://www.modelscope.cn/api/v1/users/authorized/url | GET | Query | JSON | 授权的链接 |
| https://www.modelscope.cn/api/v1/users/bind/email | POST | JSON | JSON | 绑定的邮箱 |
| https://www.modelscope.cn/api/v1/users/binding/check | GET | Query | JSON | 检查绑定关系 |
| https://www.modelscope.cn/api/v1/users/binding/info | GET | Query | JSON | 绑定关系信息 |
| https://www.modelscope.cn/api/v1/users/binding/revoke | POST | JSON | JSON | 绑定关系的撤销 |
| https://www.modelscope.cn/api/v1/users/homepage/highlights | DELETE | Query | JSON | 主页的亮点 |
| https://www.modelscope.cn/api/v1/users/homepage/highlights | GET | Query | JSON | 主页的亮点 |
| https://www.modelscope.cn/api/v1/users/homepage/highlights | POST | JSON | JSON | 主页的亮点 |
| https://www.modelscope.cn/api/v1/users/info | GET | Query | JSON | 信息（账号资料、绑定关系、令牌与站内消息） |
| https://www.modelscope.cn/api/v1/users/info | POST | JSON | JSON | 信息（账号资料、绑定关系、令牌与站内消息） |
| https://www.modelscope.cn/api/v1/users/info | PUT | JSON | JSON | 信息（账号资料、绑定关系、令牌与站内消息） |
| https://www.modelscope.cn/api/v1/users/login/info | GET | Query | JSON | 登录信息 |
| https://www.modelscope.cn/api/v1/users/messages | GET | Query | JSON | 消息（账号资料、绑定关系、令牌与站内消息） |
| https://www.modelscope.cn/api/v1/users/messages | PUT | JSON | JSON | 消息（账号资料、绑定关系、令牌与站内消息） |
| https://www.modelscope.cn/api/v1/users/messages/profile | POST | JSON | JSON | 消息的资料 |
| https://www.modelscope.cn/api/v1/users/messages/summary | GET | Query | JSON | 消息的摘要 |
| https://www.modelscope.cn/api/v1/users/messages/td | GET | Query | JSON | 消息的TD |
| https://www.modelscope.cn/api/v1/users/organizations | GET | Query | JSON | 组织（账号资料、绑定关系、令牌与站内消息） |
| https://www.modelscope.cn/api/v1/users/tokens | PATCH | JSON | JSON | 令牌（账号资料、绑定关系、令牌与站内消息） |
| https://www.modelscope.cn/api/v1/users/tokens | POST | JSON | JSON | 令牌（账号资料、绑定关系、令牌与站内消息） |
| https://www.modelscope.cn/api/v1/users/tokens/list | GET | Query | JSON | 令牌列表 |
| https://www.modelscope.cn/api/v1/users/verify/email | POST | JSON | JSON | 校验邮箱 |

## 登出（1 条）

> 账号登出

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/logout | POST | JSON | JSON | 创建/提交数据 |

## 模型（8 条）

> 模型仓库的检索、详情、创建、更新、删除与版本/文件管理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/models | PUT | JSON | JSON | 更新数据 |
| https://www.modelscope.cn/api/v1/models/aigc | POST | JSON | JSON | AIGC 内容（模型仓库的检索、详情、创建、更新、删除与版本/文件管理） |
| https://www.modelscope.cn/api/v1/models/aigc/tags | GET | Query | JSON | AIGC 内容的标签 |
| https://www.modelscope.cn/api/v1/models/aigc/upload/ | PUT | Form | JSON | 上传AIGC 内容 |
| https://www.modelscope.cn/api/v1/models/comments/tags | GET | Query | JSON | 评论标签 |
| https://www.modelscope.cn/api/v1/models/competitions | GET | Query | JSON | competitions（模型仓库的检索、详情、创建、更新、删除与版本/文件管理） |
| https://www.modelscope.cn/api/v1/models/fc | POST | JSON | JSON | FC（模型仓库的检索、详情、创建、更新、删除与版本/文件管理） |
| https://www.modelscope.cn/api/v1/models/list | PUT | JSON | JSON | 列表（模型仓库的检索、详情、创建、更新、删除与版本/文件管理） |

## 数据集（10 条）

> 数据集的检索、预览、下载、重试与版本管理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/datasets | GET | Query | JSON | 查询数据 |
| https://www.modelscope.cn/api/v1/datasets | POST | JSON | JSON | 创建/提交数据 |
| https://www.modelscope.cn/api/v1/datasets/batch/download | POST | JSON | JSON | 下载batch |
| https://www.modelscope.cn/api/v1/datasets/downloadSample | GET | Query | JSON | 下载（数据集的检索、预览、下载、重试与版本管理） |
| https://www.modelscope.cn/api/v1/datasets/preview | GET | Query | JSON | 预览（数据集的检索、预览、下载、重试与版本管理） |
| https://www.modelscope.cn/api/v1/datasets/preview/retry | POST | JSON | JSON | 重试评价 |
| https://www.modelscope.cn/api/v1/datasets/preview/status | GET | Query | JSON | 统计评价 |
| https://www.modelscope.cn/api/v1/datasets/preview/stop | POST | JSON | JSON | 预览热门 |
| https://www.modelscope.cn/api/v1/datasets/race | GET | Query | JSON | 竞速（数据集的检索、预览、下载、重试与版本管理） |
| https://www.modelscope.cn/api/v1/datasets/rename/repo | POST | JSON | JSON | rename的仓库 |

## 数据集平台 DS（12 条）

> 数据集平台（DataSet）专用接口，供控制台与前端批量查询

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/ds/v1/console/admin/activate | POST | JSON | JSON | activate控制台/管理后台 |
| https://www.modelscope.cn/api/ds/v1/console/admin/deactivate | POST | JSON | JSON | deactivate控制台/管理后台 |
| https://www.modelscope.cn/api/ds/v1/console/admin/delete | DELETE | Query | JSON | 删除控制台/管理后台 |
| https://www.modelscope.cn/api/ds/v1/console/dataset/list | GET | Query | JSON | 控制台/数据集列表 |
| https://www.modelscope.cn/api/ds/v1/console/datasets | PUT | JSON | JSON | 控制台的数据集 |
| https://www.modelscope.cn/api/ds/v1/person/my | GET | Query | JSON | 个人的我的 |
| https://www.modelscope.cn/api/ds/v1/person/my/favorite | GET | Query | JSON | 收藏个人/我的 |
| https://www.modelscope.cn/api/ds/v2/datasets/batch | POST | JSON | JSON | 批量数据集 |
| https://www.modelscope.cn/api/ds/v2/datasets/org/query | GET | Query | JSON | 查询数据集/组织 |
| https://www.modelscope.cn/api/ds/v2/tags/batch/save | POST | JSON | JSON | 批量保存 |
| https://www.modelscope.cn/api/ds/v2/tags/delete | POST | JSON | JSON | 删除标签 |
| https://www.modelscope.cn/api/ds/v2/tags/save | POST | JSON | JSON | 标签的保存 |

## 论文（3 条）

> 论文检索、详情、引用、标签与投稿

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/papers | GET | Query | JSON | 查询数据 |
| https://www.modelscope.cn/api/v1/papers | POST | JSON | JSON | 创建/提交数据 |
| https://www.modelscope.cn/api/v1/papers/tags | GET | Query | JSON | 标签（论文检索、详情、引用、标签与投稿） |

## 工作空间（17 条）

> Studio 工作空间的创建、成员、部署与运行

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/studios | POST | JSON | JSON | 创建/提交数据 |
| https://www.modelscope.cn/api/v1/studios | PUT | JSON | JSON | 更新数据 |
| https://www.modelscope.cn/api/v1/studios/comments/tags | GET | Query | JSON | 评论标签 |
| https://www.modelscope.cn/api/v1/studios/commit/uploadFileToGit | POST | Form | JSON | 上传commit |
| https://www.modelscope.cn/api/v1/studios/domains | GET | Query | JSON | 域名（Studio 工作空间的创建、成员、部署与运行） |
| https://www.modelscope.cn/api/v1/studios/draft | POST | JSON | JSON | 草稿（Studio 工作空间的创建、成员、部署与运行） |
| https://www.modelscope.cn/api/v1/studios/draft/id | DELETE | Query | JSON | id草稿 |
| https://www.modelscope.cn/api/v1/studios/draft/id | GET | Query | JSON | id草稿 |
| https://www.modelscope.cn/api/v1/studios/draft/id | PUT | JSON | JSON | id草稿 |
| https://www.modelscope.cn/api/v1/studios/draft/latest | GET | Query | JSON | 草稿的最新 |
| https://www.modelscope.cn/api/v1/studios/file | POST | JSON | JSON | 文件（Studio 工作空间的创建、成员、部署与运行） |
| https://www.modelscope.cn/api/v1/studios/free_instance | GET | Query | JSON | 免费实例（Studio 工作空间的创建、成员、部署与运行） |
| https://www.modelscope.cn/api/v1/studios/images | GET | Query | JSON | 图片（Studio 工作空间的创建、成员、部署与运行） |
| https://www.modelscope.cn/api/v1/studios/multi_query | PUT | JSON | JSON | 查询（Studio 工作空间的创建、成员、部署与运行） |
| https://www.modelscope.cn/api/v1/studios/scenarios | GET | Query | JSON | 应用场景（Studio 工作空间的创建、成员、部署与运行） |
| https://www.modelscope.cn/api/v1/studios/sdk-version | GET | Query | JSON | SDK 版本（Studio 工作空间的创建、成员、部署与运行） |
| https://www.modelscope.cn/api/v1/studios/webembed | GET | Query | JSON | 嵌入（Studio 工作空间的创建、成员、部署与运行） |

## 智能体（1 条）

> Agent 智能体创建、配置与调试

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/agents/repo/files/parse | POST | JSON | JSON | 仓库/文件的解析 |

## MCP 服务（12 条）

> MCP 服务的注册、认领、部署与状态检测

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/mcpServers/byUrl | GET | Query | JSON | 链接解析（MCP 服务的注册、认领、部署与状态检测） |
| https://www.modelscope.cn/api/v1/mcpServers/byUrl/unClaim | GET | Query | JSON | 认领链接解析 |
| https://www.modelscope.cn/api/v1/mcpServers/checkDingTalkToken | POST | JSON | JSON | 检查（MCP 服务的注册、认领、部署与状态检测） |
| https://www.modelscope.cn/api/v1/mcpServers/claim | POST | JSON | JSON | 认领（MCP 服务的注册、认领、部署与状态检测） |
| https://www.modelscope.cn/api/v1/mcpServers/claim_status | GET | Query | JSON | 统计（MCP 服务的注册、认领、部署与状态检测） |
| https://www.modelscope.cn/api/v1/mcpServers/create | POST | JSON | JSON | 创建（MCP 服务的注册、认领、部署与状态检测） |
| https://www.modelscope.cn/api/v1/mcpServers/deploy | DELETE | Query | JSON | 部署（MCP 服务的注册、认领、部署与状态检测） |
| https://www.modelscope.cn/api/v1/mcpServers/deploy | POST | JSON | JSON | 部署（MCP 服务的注册、认领、部署与状态检测） |
| https://www.modelscope.cn/api/v1/mcpServers/deployList | GET | Query | JSON | 列表（MCP 服务的注册、认领、部署与状态检测） |
| https://www.modelscope.cn/api/v1/mcpServers/deployStatus | POST | JSON | JSON | 统计（MCP 服务的注册、认领、部署与状态检测） |
| https://www.modelscope.cn/api/v1/mcpServers/deploy_info | GET | Query | JSON | 信息（MCP 服务的注册、认领、部署与状态检测） |
| https://www.modelscope.cn/api/v1/mcpServers/detect | POST | JSON | JSON | 检测（MCP 服务的注册、认领、部署与状态检测） |

## 技能（6 条）

> Skill 技能管理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/skills | POST | JSON | JSON | 创建/提交数据 |
| https://www.modelscope.cn/api/v1/skills/claimStatus | GET | Query | JSON | 统计（Skill 技能管理） |
| https://www.modelscope.cn/api/v1/skills/github/parse | POST | JSON | JSON | github的解析 |
| https://www.modelscope.cn/api/v1/skills/github/skillInfo | POST | JSON | JSON | github信息 |
| https://www.modelscope.cn/api/v1/skills/official_repeat | POST | JSON | JSON | official_repeat（Skill 技能管理） |
| https://www.modelscope.cn/api/v1/skills/user_repeat | POST | JSON | JSON | 用户（Skill 技能管理） |

## 组织（13 条）

> Organization 组织信息与成员管理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/organizations | DELETE | Query | JSON | 删除数据 |
| https://www.modelscope.cn/api/v1/organizations | GET | Query | JSON | 查询数据 |
| https://www.modelscope.cn/api/v1/organizations | POST | JSON | JSON | 创建/提交数据 |
| https://www.modelscope.cn/api/v1/organizations | PUT | JSON | JSON | 更新数据 |
| https://www.modelscope.cn/api/v1/organizations/apply | GET | Query | JSON | 申请（Organization 组织信息与成员管理） |
| https://www.modelscope.cn/api/v1/organizations/apply | POST | JSON | JSON | 申请（Organization 组织信息与成员管理） |
| https://www.modelscope.cn/api/v1/organizations/apply | PUT | JSON | JSON | 申请（Organization 组织信息与成员管理） |
| https://www.modelscope.cn/api/v1/organizations/console/list | GET | Query | JSON | 控制台列表 |
| https://www.modelscope.cn/api/v1/organizations/console/review | PUT | JSON | JSON | 控制台的评价 |
| https://www.modelscope.cn/api/v1/organizations/permissions | DELETE | Query | JSON | 权限（Organization 组织信息与成员管理） |
| https://www.modelscope.cn/api/v1/organizations/permissions | GET | Query | JSON | 权限（Organization 组织信息与成员管理） |
| https://www.modelscope.cn/api/v1/organizations/permissions | POST | JSON | JSON | 权限（Organization 组织信息与成员管理） |
| https://www.modelscope.cn/api/v1/organizations/permissions | PUT | JSON | JSON | 权限（Organization 组织信息与成员管理） |

## 合集（14 条）

> Collection 合集的创建、归集、收藏与历史

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/collections | DELETE | Query | JSON | 删除数据 |
| https://www.modelscope.cn/api/v1/collections | GET | Query | JSON | 查询数据 |
| https://www.modelscope.cn/api/v1/collections | POST | JSON | JSON | 创建/提交数据 |
| https://www.modelscope.cn/api/v1/collections | PUT | JSON | JSON | 更新数据 |
| https://www.modelscope.cn/api/v1/collections/check | GET | Query | JSON | 检查（Collection 合集的创建、归集、收藏与历史） |
| https://www.modelscope.cn/api/v1/collections/element | DELETE | Query | JSON | element（Collection 合集的创建、归集、收藏与历史） |
| https://www.modelscope.cn/api/v1/collections/element | GET | Query | JSON | element（Collection 合集的创建、归集、收藏与历史） |
| https://www.modelscope.cn/api/v1/collections/element | POST | JSON | JSON | element（Collection 合集的创建、归集、收藏与历史） |
| https://www.modelscope.cn/api/v1/collections/element | PUT | JSON | JSON | element（Collection 合集的创建、归集、收藏与历史） |
| https://www.modelscope.cn/api/v1/collections/element/check | GET | Query | JSON | 检查element |
| https://www.modelscope.cn/api/v1/collections/history | GET | Query | JSON | 历史（Collection 合集的创建、归集、收藏与历史） |
| https://www.modelscope.cn/api/v1/collections/info | GET | Query | JSON | 信息（Collection 合集的创建、归集、收藏与历史） |
| https://www.modelscope.cn/api/v1/collections/list | GET | Query | JSON | 列表（Collection 合集的创建、归集、收藏与历史） |
| https://www.modelscope.cn/api/v1/collections/org | GET | Query | JSON | 组织（Collection 合集的创建、归集、收藏与历史） |

## 排行榜（4 条）

> Leaderboard 榜单查询、投稿与审核

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/leaderboards | GET | Query | JSON | 查询数据 |
| https://www.modelscope.cn/api/v1/leaderboards | POST | JSON | JSON | 创建/提交数据 |
| https://www.modelscope.cn/api/v1/leaderboards | PUT | JSON | JSON | 更新数据 |
| https://www.modelscope.cn/api/v1/leaderboards/console | GET | Query | JSON | 控制台（Leaderboard 榜单查询、投稿与审核） |

## 赛事（6 条）

> Competition 赛事创建、投稿、评审与榜单

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/competitions | DELETE | Query | JSON | 删除数据 |
| https://www.modelscope.cn/api/v1/competitions | GET | Query | JSON | 查询数据 |
| https://www.modelscope.cn/api/v1/competitions | POST | JSON | JSON | 创建/提交数据 |
| https://www.modelscope.cn/api/v1/competitions | PUT | JSON | JSON | 更新数据 |
| https://www.modelscope.cn/api/v1/competitions/batch_query | POST | JSON | JSON | 批量（Competition 赛事创建、投稿、评审与榜单） |
| https://www.modelscope.cn/api/v1/competitions/console | GET | Query | JSON | 控制台（Competition 赛事创建、投稿、评审与榜单） |

## 勋章（12 条）

> Medal 勋章授予、校验与展示

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/medal/certificate | GET | Query | JSON | 证书（Medal 勋章授予、校验与展示） |
| https://www.modelscope.cn/api/v1/medal/console/list/all | GET | Query | JSON | 列表全部 |
| https://www.modelscope.cn/api/v1/medal/dashboard/top | GET | Query | JSON | dashboard的热门 |
| https://www.modelscope.cn/api/v1/medal/gift | GET | Query | JSON | gift（Medal 勋章授予、校验与展示） |
| https://www.modelscope.cn/api/v1/medal/gift | PUT | JSON | JSON | gift（Medal 勋章授予、校验与展示） |
| https://www.modelscope.cn/api/v1/medal/pendant | GET | Query | JSON | pendant（Medal 勋章授予、校验与展示） |
| https://www.modelscope.cn/api/v1/medal/pendant | PUT | JSON | JSON | pendant（Medal 勋章授予、校验与展示） |
| https://www.modelscope.cn/api/v1/medal/user | GET | Query | JSON | 用户（Medal 勋章授予、校验与展示） |
| https://www.modelscope.cn/api/v1/medal/user | POST | JSON | JSON | 用户（Medal 勋章授予、校验与展示） |
| https://www.modelscope.cn/api/v1/medal/user | PUT | JSON | JSON | 用户（Medal 勋章授予、校验与展示） |
| https://www.modelscope.cn/api/v1/medal/user/check | GET | Query | JSON | 检查用户 |
| https://www.modelscope.cn/api/v1/medal/user/info | GET | Query | JSON | 用户信息 |

## 品牌（3 条）

> 品牌信息查询

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/brands | GET | Query | JSON | 查询数据 |
| https://www.modelscope.cn/api/v1/brands | POST | JSON | JSON | 创建/提交数据 |
| https://www.modelscope.cn/api/v1/brands | PUT | JSON | JSON | 更新数据 |

## 画廊（12 条）

> Gallery 画廊内容管理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/gallery | DELETE | Query | JSON | 删除数据 |
| https://www.modelscope.cn/api/v1/gallery | GET | Query | JSON | 查询数据 |
| https://www.modelscope.cn/api/v1/gallery | POST | JSON | JSON | 创建/提交数据 |
| https://www.modelscope.cn/api/v1/gallery | PUT | JSON | JSON | 更新数据 |
| https://www.modelscope.cn/api/v1/gallery/check | GET | Query | JSON | 检查（Gallery 画廊内容管理） |
| https://www.modelscope.cn/api/v1/gallery/fork | PUT | JSON | JSON | Fork（Gallery 画廊内容管理） |
| https://www.modelscope.cn/api/v1/gallery/list | GET | Query | JSON | 列表（Gallery 画廊内容管理） |
| https://www.modelscope.cn/api/v1/gallery/publish | PUT | JSON | JSON | 发布（Gallery 画廊内容管理） |
| https://www.modelscope.cn/api/v1/gallery/square/files | GET | Query | JSON | square的文件 |
| https://www.modelscope.cn/api/v1/gallery/square/files/download | POST | JSON | JSON | 下载square/文件 |
| https://www.modelscope.cn/api/v1/gallery/square/files/upload | POST | Form | JSON | 上传square/文件 |
| https://www.modelscope.cn/api/v1/gallery/tree | GET | Query | JSON | tree（Gallery 画廊内容管理） |

## Notebook（24 条）

> Notebook 笔记本的创建、运行与分享

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/notebooks | GET | Query | JSON | 查询数据 |
| https://www.modelscope.cn/api/v1/notebooks | POST | JSON | JSON | 创建/提交数据 |
| https://www.modelscope.cn/api/v1/notebooks/activelog | POST | JSON | JSON | 运行日志（Notebook 笔记本的创建、运行与分享） |
| https://www.modelscope.cn/api/v1/notebooks/console/blacklist | DELETE | Query | JSON | 控制台的黑名单 |
| https://www.modelscope.cn/api/v1/notebooks/console/blacklist | POST | JSON | JSON | 控制台的黑名单 |
| https://www.modelscope.cn/api/v1/notebooks/console/blacklist/running | GET | Query | JSON | 运行控制台/黑名单 |
| https://www.modelscope.cn/api/v1/notebooks/credential/check | GET | Query | JSON | 检查凭证 |
| https://www.modelscope.cn/api/v1/notebooks/image | GET | Query | JSON | 图片（Notebook 笔记本的创建、运行与分享） |
| https://www.modelscope.cn/api/v1/notebooks/image | PUT | JSON | JSON | 图片（Notebook 笔记本的创建、运行与分享） |
| https://www.modelscope.cn/api/v1/notebooks/image/recommend | GET | Query | JSON | 图片的推荐 |
| https://www.modelscope.cn/api/v1/notebooks/jupyter/get | GET | Query | JSON | 获取jupyter |
| https://www.modelscope.cn/api/v1/notebooks/paid/get | GET | Query | JSON | 获取paid |
| https://www.modelscope.cn/api/v1/notebooks/paid/list | GET | Query | JSON | paid列表 |
| https://www.modelscope.cn/api/v1/notebooks/paid/open | POST | JSON | JSON | paid的开放 |
| https://www.modelscope.cn/api/v1/notebooks/paid/open/check | GET | Query | JSON | 检查paid/开放 |
| https://www.modelscope.cn/api/v1/notebooks/paid/start | PUT | JSON | JSON | 启动paid |
| https://www.modelscope.cn/api/v1/notebooks/paid/stop | PUT | JSON | JSON | 停止paid |
| https://www.modelscope.cn/api/v1/notebooks/paid/token | GET | Query | JSON | paid的令牌 |
| https://www.modelscope.cn/api/v1/notebooks/paid/url | GET | Query | JSON | paid的链接 |
| https://www.modelscope.cn/api/v1/notebooks/snapshot | GET | Query | JSON | 快照（Notebook 笔记本的创建、运行与分享） |
| https://www.modelscope.cn/api/v1/notebooks/snapshot | POST | JSON | JSON | 快照（Notebook 笔记本的创建、运行与分享） |
| https://www.modelscope.cn/api/v1/notebooks/specs | GET | Query | JSON | 规格（Notebook 笔记本的创建、运行与分享） |
| https://www.modelscope.cn/api/v1/notebooks/stop | PUT | JSON | JSON | 停止（Notebook 笔记本的创建、运行与分享） |
| https://www.modelscope.cn/api/v1/notebooks/token | GET | Query | JSON | 令牌（Notebook 笔记本的创建、运行与分享） |

## AIGC（10 条）

> AIGC 图片生成、上传、标注与工作流编排

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/aigc/picture | DELETE | Query | JSON | 图片（AIGC 图片生成、上传、标注与工作流编排） |
| https://www.modelscope.cn/api/v1/aigc/picture | GET | Query | JSON | 图片（AIGC 图片生成、上传、标注与工作流编排） |
| https://www.modelscope.cn/api/v1/aigc/picture | POST | JSON | JSON | 图片（AIGC 图片生成、上传、标注与工作流编排） |
| https://www.modelscope.cn/api/v1/aigc/picture | PUT | JSON | JSON | 图片（AIGC 图片生成、上传、标注与工作流编排） |
| https://www.modelscope.cn/api/v1/aigc/picture/labels | GET | Query | JSON | 图片的标签 |
| https://www.modelscope.cn/api/v1/aigc/picture/list | POST | JSON | JSON | 图片列表 |
| https://www.modelscope.cn/api/v1/aigc/picture/update | PUT | JSON | JSON | 更新图片 |
| https://www.modelscope.cn/api/v1/aigc/picture/upload | POST | Form | JSON | 上传图片 |
| https://www.modelscope.cn/api/v1/aigc/workflow/add | POST | JSON | JSON | 添加workflow |
| https://www.modelscope.cn/api/v1/aigc/workflow/edit | POST | JSON | JSON | 编辑workflow |

## 工作流（8 条）

> Workflow 工作流的编排、标记与评论

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/workflow | DELETE | Query | JSON | 删除数据 |
| https://www.modelscope.cn/api/v1/workflow | GET | Query | JSON | 查询数据 |
| https://www.modelscope.cn/api/v1/workflow | PUT | JSON | JSON | 更新数据 |
| https://www.modelscope.cn/api/v1/workflow/comments | POST | JSON | JSON | 评论（Workflow 工作流的编排、标记与评论） |
| https://www.modelscope.cn/api/v1/workflow/comments/list | GET | Query | JSON | comments列表 |
| https://www.modelscope.cn/api/v1/workflow/comments/summary | GET | Query | JSON | 评论摘要 |
| https://www.modelscope.cn/api/v1/workflow/mark | POST | JSON | JSON | 标记（Workflow 工作流的编排、标记与评论） |
| https://www.modelscope.cn/api/v1/workflow/user | GET | Query | JSON | 用户（Workflow 工作流的编排、标记与评论） |

## 文件资源（5 条）

> 文件资源（RM）上传、下载、代理与配额

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/rm/downloadUrl | POST | JSON | JSON | 下载（文件资源（RM）上传、下载、代理与配额） |
| https://www.modelscope.cn/api/v1/rm/fc | POST | JSON | JSON | FC（文件资源（RM）上传、下载、代理与配额） |
| https://www.modelscope.cn/api/v1/rm/resource/proxy | GET | Query | JSON | resource的代理 |
| https://www.modelscope.cn/api/v1/rm/uploadSts | POST | Form | JSON | 上传（文件资源（RM）上传、下载、代理与配额） |
| https://www.modelscope.cn/api/v1/rm/uploadUrl | POST | Form | JSON | 上传（文件资源（RM）上传、下载、代理与配额） |

## 训练（4 条）

> 训练任务的创建与状态管理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/train/paid/url | GET | Query | JSON | paid的链接 |
| https://www.modelscope.cn/api/v1/train/projects | GET | Query | JSON | 项目（训练任务的创建与状态管理） |
| https://www.modelscope.cn/api/v1/train/projects | POST | JSON | JSON | 项目（训练任务的创建与状态管理） |
| https://www.modelscope.cn/api/v1/train/projects | PUT | JSON | JSON | 项目（训练任务的创建与状态管理） |

## 部署（5 条）

> 模型部署服务与推荐配置

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/deploy | POST | JSON | JSON | 创建/提交数据 |
| https://www.modelscope.cn/api/v1/deploy/publicList | GET | Query | JSON | 列表（模型部署服务与推荐配置） |
| https://www.modelscope.cn/api/v1/deploy/recommendConfigs | GET | Query | JSON | 配置（模型部署服务与推荐配置） |
| https://www.modelscope.cn/api/v1/deploy/servicesByModel | GET | Query | JSON | 按模型的服务（模型部署服务与推荐配置） |
| https://www.modelscope.cn/api/v1/deploy/templateFile | GET | Query | JSON | 模板文件（模型部署服务与推荐配置） |

## 主题（6 条）

> 创空间主题模板管理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/themes | DELETE | Query | JSON | 删除数据 |
| https://www.modelscope.cn/api/v1/themes | GET | Query | JSON | 查询数据 |
| https://www.modelscope.cn/api/v1/themes | POST | JSON | JSON | 创建/提交数据 |
| https://www.modelscope.cn/api/v1/themes | PUT | JSON | JSON | 更新数据 |
| https://www.modelscope.cn/api/v1/themes/console | GET | Query | JSON | 控制台（创空间主题模板管理） |
| https://www.modelscope.cn/api/v1/themes/token | PUT | JSON | JSON | 令牌（创空间主题模板管理） |

## 活动（4 条）

> 运营活动管理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/activities | GET | Query | JSON | 查询数据 |
| https://www.modelscope.cn/api/v1/activities | POST | JSON | JSON | 创建/提交数据 |
| https://www.modelscope.cn/api/v1/activities | PUT | JSON | JSON | 更新数据 |
| https://www.modelscope.cn/api/v1/activities/console | GET | Query | JSON | 控制台（运营活动管理） |

## 文章（6 条）

> 资讯文章发布与审核

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/articles | GET | Query | JSON | 查询数据 |
| https://www.modelscope.cn/api/v1/articles | POST | JSON | JSON | 创建/提交数据 |
| https://www.modelscope.cn/api/v1/articles | PUT | JSON | JSON | 更新数据 |
| https://www.modelscope.cn/api/v1/articles/batch | POST | JSON | JSON | 批量（资讯文章发布与审核） |
| https://www.modelscope.cn/api/v1/articles/brand | POST | JSON | JSON | 品牌（资讯文章发布与审核） |
| https://www.modelscope.cn/api/v1/articles/console | GET | Query | JSON | 控制台（资讯文章发布与审核） |

## 评论（5 条）

> 评论查询、标记与开放

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/comments | GET | Query | JSON | 查询数据 |
| https://www.modelscope.cn/api/v1/comments/open | POST | JSON | JSON | 开放（评论查询、标记与开放） |
| https://www.modelscope.cn/api/v1/comments/user/mark | POST | JSON | JSON | 标记用户 |
| https://www.modelscope.cn/api/v1/comments/user/marked | GET | Query | JSON | 标记用户 |
| https://www.modelscope.cn/api/v1/comments/user/owned | GET | Query | JSON | 用户的我拥有的 |

## 横幅（4 条）

> 首页与运营位横幅管理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/banners | GET | Query | JSON | 查询数据 |
| https://www.modelscope.cn/api/v1/banners | POST | JSON | JSON | 创建/提交数据 |
| https://www.modelscope.cn/api/v1/banners | PUT | JSON | JSON | 更新数据 |
| https://www.modelscope.cn/api/v1/banners/console | GET | Query | JSON | 控制台（首页与运营位横幅管理） |

## 标签（4 条）

> 标签管理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/tags | GET | Query | JSON | 查询数据 |
| https://www.modelscope.cn/api/v2/tags | GET | Query | JSON | 查询数据 |
| https://www.modelscope.cn/api/v2/tags/details/list | GET | Query | JSON | 详情列表 |
| https://www.modelscope.cn/api/v2/tags/org/list | GET | Query | JSON | 组织列表 |

## 主页（2 条）

> 个人主页数据

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/profile | GET | Query | JSON | 查询数据 |
| https://www.modelscope.cn/api/v1/profile | POST | JSON | JSON | 创建/提交数据 |

## 智能体工具（2 条）

> 智能体绑定的工具管理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/agent-tools | POST | JSON | JSON | 创建/提交数据 |
| https://www.modelscope.cn/api/v1/agent-tools | PUT | JSON | JSON | 更新数据 |

## 工具箱（2 条）

> 工具箱管理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/toolboxes | GET | Query | JSON | 查询数据 |
| https://www.modelscope.cn/api/v1/toolboxes | POST | JSON | JSON | 创建/提交数据 |

## Pivot 评测（8 条）

> Pivot 评测任务的创建、查询与结果管理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/pivotEval/evalTaskDatasets | GET | Query | JSON | 设置（Pivot 评测任务的创建、查询与结果管理） |
| https://www.modelscope.cn/api/v1/pivotEval/predictResult | GET | Query | JSON | 预测结果（Pivot 评测任务的创建、查询与结果管理） |
| https://www.modelscope.cn/api/v1/pivotEval/taskInfo | GET | Query | JSON | 信息（Pivot 评测任务的创建、查询与结果管理） |
| https://www.modelscope.cn/api/v1/pivotEval/taskList | GET | Query | JSON | 列表（Pivot 评测任务的创建、查询与结果管理） |
| https://www.modelscope.cn/api/v1/pivotEval/taskLog | GET | Query | JSON | 日志（Pivot 评测任务的创建、查询与结果管理） |
| https://www.modelscope.cn/api/v1/pivotEval/taskProgress | GET | Query | JSON | 任务进度（Pivot 评测任务的创建、查询与结果管理） |
| https://www.modelscope.cn/api/v1/pivotEval/taskReport | GET | Query | JSON | 举报（Pivot 评测任务的创建、查询与结果管理） |
| https://www.modelscope.cn/api/v1/pivotEval/updateStatus | POST | JSON | JSON | 更新（Pivot 评测任务的创建、查询与结果管理） |

## Dolphin（50 条）

> Dolphin 内部数据服务：聚合检索、数据集索引与后台查询

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/dolphin/admin/query | DELETE | Query | JSON | 查询管理后台 |
| https://www.modelscope.cn/api/v1/dolphin/admin/query | GET | Query | JSON | 查询管理后台 |
| https://www.modelscope.cn/api/v1/dolphin/admin/query | POST | JSON | JSON | 查询管理后台 |
| https://www.modelscope.cn/api/v1/dolphin/admin/query/blacklist | DELETE | Query | JSON | 查询黑名单 |
| https://www.modelscope.cn/api/v1/dolphin/admin/query/blacklist | GET | Query | JSON | 查询黑名单 |
| https://www.modelscope.cn/api/v1/dolphin/admin/query/blacklist | POST | JSON | JSON | 查询黑名单 |
| https://www.modelscope.cn/api/v1/dolphin/agents | PUT | JSON | JSON | 智能体（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |
| https://www.modelscope.cn/api/v1/dolphin/agg | PUT | JSON | JSON | 聚合（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |
| https://www.modelscope.cn/api/v1/dolphin/agg/homepage | GET | Query | JSON | 聚合的主页 |
| https://www.modelscope.cn/api/v1/dolphin/agg/query | DELETE | Query | JSON | 查询聚合 |
| https://www.modelscope.cn/api/v1/dolphin/agg/query | PUT | JSON | JSON | 查询聚合 |
| https://www.modelscope.cn/api/v1/dolphin/agg/suggestv2 | POST | JSON | JSON | 聚合的搜索建议 |
| https://www.modelscope.cn/api/v1/dolphin/aigcAgg | POST | JSON | JSON | AIGC 内容（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |
| https://www.modelscope.cn/api/v1/dolphin/aigcAgg/query | DELETE | Query | JSON | 查询AIGC 内容 |
| https://www.modelscope.cn/api/v1/dolphin/aigcPictures | PUT | JSON | JSON | AIGC 内容（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |
| https://www.modelscope.cn/api/v1/dolphin/aigcRecommends | PUT | JSON | JSON | AIGC 内容（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |
| https://www.modelscope.cn/api/v1/dolphin/aigcSimilars | POST | JSON | JSON | AIGC 内容（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |
| https://www.modelscope.cn/api/v1/dolphin/aigcSimliarAgg | POST | JSON | JSON | AIGC 内容（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |
| https://www.modelscope.cn/api/v1/dolphin/articles | POST | JSON | JSON | articles（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |
| https://www.modelscope.cn/api/v1/dolphin/brands | PUT | JSON | JSON | 品牌（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |
| https://www.modelscope.cn/api/v1/dolphin/collections | PUT | JSON | JSON | 收藏（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |
| https://www.modelscope.cn/api/v1/dolphin/comments | PUT | JSON | JSON | 评论（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |
| https://www.modelscope.cn/api/v1/dolphin/dataset/query | DELETE | Query | JSON | 查询数据集 |
| https://www.modelscope.cn/api/v1/dolphin/dataset/query | PUT | JSON | JSON | 查询数据集 |
| https://www.modelscope.cn/api/v1/dolphin/dataset/suggestv2 | POST | JSON | JSON | 数据集的搜索建议 |
| https://www.modelscope.cn/api/v1/dolphin/datasets | GET | Query | JSON | 数据集（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |
| https://www.modelscope.cn/api/v1/dolphin/discussions | POST | JSON | JSON | 讨论（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |
| https://www.modelscope.cn/api/v1/dolphin/documents | POST | JSON | JSON | 文档（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |
| https://www.modelscope.cn/api/v1/dolphin/gallerys | PUT | JSON | JSON | 画廊（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |
| https://www.modelscope.cn/api/v1/dolphin/headlines | PUT | JSON | JSON | 头条（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |
| https://www.modelscope.cn/api/v1/dolphin/mcpServers | PUT | JSON | JSON | MCP 服务（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |
| https://www.modelscope.cn/api/v1/dolphin/model/query | DELETE | Query | JSON | 查询模型 |
| https://www.modelscope.cn/api/v1/dolphin/model/query | PUT | JSON | JSON | 查询模型 |
| https://www.modelscope.cn/api/v1/dolphin/model/suggestv2 | POST | JSON | JSON | 模型的搜索建议 |
| https://www.modelscope.cn/api/v1/dolphin/modelSimilars | POST | JSON | JSON | 模型（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |
| https://www.modelscope.cn/api/v1/dolphin/models | PUT | JSON | JSON | 模型（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |
| https://www.modelscope.cn/api/v1/dolphin/models/relatedRecommend | PUT | JSON | JSON | 模型的相关推荐 |
| https://www.modelscope.cn/api/v1/dolphin/organizations | PUT | JSON | JSON | 组织（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |
| https://www.modelscope.cn/api/v1/dolphin/paper/query | DELETE | Query | JSON | 查询论文 |
| https://www.modelscope.cn/api/v1/dolphin/paper/query | PUT | JSON | JSON | 查询论文 |
| https://www.modelscope.cn/api/v1/dolphin/papers | PUT | JSON | JSON | 论文（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |
| https://www.modelscope.cn/api/v1/dolphin/profile | GET | Query | JSON | 资料（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |
| https://www.modelscope.cn/api/v1/dolphin/profile | PUT | JSON | JSON | 资料（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |
| https://www.modelscope.cn/api/v1/dolphin/races | POST | JSON | JSON | 竞速（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |
| https://www.modelscope.cn/api/v1/dolphin/skills | PUT | JSON | JSON | 技能（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |
| https://www.modelscope.cn/api/v1/dolphin/studio/query | DELETE | Query | JSON | 查询创空间 |
| https://www.modelscope.cn/api/v1/dolphin/studio/query | PUT | JSON | JSON | 查询创空间 |
| https://www.modelscope.cn/api/v1/dolphin/studio/suggestv2 | POST | JSON | JSON | 创空间的搜索建议 |
| https://www.modelscope.cn/api/v1/dolphin/studios | PUT | JSON | JSON | 创空间（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |
| https://www.modelscope.cn/api/v1/dolphin/users | PUT | JSON | JSON | 用户（Dolphin 内部数据服务：聚合检索、数据集索引与后台查询） |

## 任务（3 条）

> 任务系统

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/tasks | GET | Query | JSON | 查询数据 |
| https://www.modelscope.cn/api/v1/tasks/popular | POST | JSON | JSON | 热门（任务系统） |
| https://www.modelscope.cn/api/v1/tasks/popular/list | GET | Query | JSON | 热门列表 |

## 举报（1 条）

> 举报受理与处理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/reports | POST | JSON | JSON | 创建/提交数据 |

## OpenAPI 开放接口（11 条）

> 面向外部开发者的开放能力接口，含智能体 Provider 配置与远端模型拉取

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/openapi/v1/agent_providers/custom | DELETE | Query | JSON | agent_providers的自定义 |
| https://www.modelscope.cn/openapi/v1/agent_providers/custom | POST | JSON | JSON | agent_providers的自定义 |
| https://www.modelscope.cn/openapi/v1/agent_providers/custom | PUT | JSON | JSON | agent_providers的自定义 |
| https://www.modelscope.cn/openapi/v1/agent_providers/custom/list | POST | JSON | JSON | agent_providers/自定义列表 |
| https://www.modelscope.cn/openapi/v1/agent_providers/custom/sort | POST | JSON | JSON | 排序agent_providers/自定义 |
| https://www.modelscope.cn/openapi/v1/agent_providers/model | DELETE | Query | JSON | agent_providers的模型 |
| https://www.modelscope.cn/openapi/v1/agent_providers/model | POST | JSON | JSON | agent_providers的模型 |
| https://www.modelscope.cn/openapi/v1/agent_providers/model | PUT | JSON | JSON | agent_providers的模型 |
| https://www.modelscope.cn/openapi/v1/agent_providers/model/list | POST | JSON | JSON | agent_providers/模型列表 |
| https://www.modelscope.cn/openapi/v1/agent_providers/model/remote_models | GET | Query | JSON | agent_providers/模型的远端模型 |
| https://www.modelscope.cn/openapi/v1/agents | POST | JSON | JSON | 智能体（面向外部开发者的开放能力接口，含智能体 Provider 配置与远端模型拉取） |

## 其他（1 条）

> 其他未归类接口

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/fbi/report | GET | Query | JSON | 举报（其他未归类接口） |

## AMD 加速（1 条）

> AMD 硬件加速状态检查

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/amd/status/check | GET | Query | JSON | 统计检查 |

## Hub 应用（5 条）

> 应用中心（Hub）应用的安装与状态

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/hub_apps | DELETE | Query | JSON | 删除数据 |
| https://www.modelscope.cn/api/v1/hub_apps | GET | Query | JSON | 查询数据 |
| https://www.modelscope.cn/api/v1/hub_apps | PATCH | JSON | JSON | 修改数据 |
| https://www.modelscope.cn/api/v1/hub_apps | POST | JSON | JSON | 创建/提交数据 |
| https://www.modelscope.cn/api/v1/hub_apps/endpoints/validate | POST | JSON | JSON | endpoints的校验 |

## MCP（1 条）

> MCP 通用配置与状态

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/mcp/extract | POST | JSON | JSON | 抽取（MCP 通用配置与状态） |

## Magicubes（1 条）

> Magicubes 组件/魔方的查询与管理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/magicubes/balance | GET | Query | JSON | 余额（Magicubes 组件/魔方的查询与管理） |

## Muse（4 条）

> Muse 创作工具的推理与模板接口

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/muse/image/getImageDownloadUrl | GET | Query | JSON | 下载图片 |
| https://www.modelscope.cn/api/v1/muse/predict/unauth/defaultTemplateV2 | GET | Query | JSON | predict/unauth的默认 |
| https://www.modelscope.cn/api/v1/muse/shop/workflow/getInfo | GET | Query | JSON | shop/workflow信息 |
| https://www.modelscope.cn/api/v1/muse/tool/generateImageCaption | GET | Query | JSON | tool的生成图像描述 |

## Muse 商业（1 条）

> Muse 商业化能力的接口

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/museBusiness/redirect_link | POST | JSON | JSON | 跳转链接（Muse 商业化能力的接口） |

## OAuth（11 条）

> 第三方 OAuth 应用的注册、授权与回调管理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/oauth/app | DELETE | Query | JSON | 应用（第三方 OAuth 应用的注册、授权与回调管理） |
| https://www.modelscope.cn/api/v1/oauth/app | GET | Query | JSON | 应用（第三方 OAuth 应用的注册、授权与回调管理） |
| https://www.modelscope.cn/api/v1/oauth/app | POST | JSON | JSON | 应用（第三方 OAuth 应用的注册、授权与回调管理） |
| https://www.modelscope.cn/api/v1/oauth/app | PUT | JSON | JSON | 应用（第三方 OAuth 应用的注册、授权与回调管理） |
| https://www.modelscope.cn/api/v1/oauth/app/refreshSecret | POST | JSON | JSON | 刷新应用 |
| https://www.modelscope.cn/api/v1/oauth/apps | GET | Query | JSON | 应用（第三方 OAuth 应用的注册、授权与回调管理） |
| https://www.modelscope.cn/api/v1/oauth/authorize | GET | Query | JSON | 授权（第三方 OAuth 应用的注册、授权与回调管理） |
| https://www.modelscope.cn/api/v1/oauth/authorize | POST | JSON | JSON | 授权（第三方 OAuth 应用的注册、授权与回调管理） |
| https://www.modelscope.cn/api/v1/oauth/cancel | POST | JSON | JSON | 取消（第三方 OAuth 应用的注册、授权与回调管理） |
| https://www.modelscope.cn/api/v1/oauth/user/apps | GET | Query | JSON | 用户的应用 |
| https://www.modelscope.cn/api/v1/oauth/user/apps/revoke | POST | JSON | JSON | 用户/应用的撤销 |

## 动态流（1 条）

> 首页动态流与信息流数据

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/feeds | GET | Query | JSON | 查询数据 |

## 控制台 Console/admin（24 条）

> 管理后台数据操作

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/console/admin/aigc/tags | DELETE | Query | JSON | 管理后台/AIGC 内容的标签 |
| https://www.modelscope.cn/api/v1/console/admin/aigc/tags | POST | JSON | JSON | 管理后台/AIGC 内容的标签 |
| https://www.modelscope.cn/api/v1/console/admin/aigc/tags | PUT | JSON | JSON | 管理后台/AIGC 内容的标签 |
| https://www.modelscope.cn/api/v1/console/admin/aigc/tags/list | GET | Query | JSON | 管理后台/AIGC 内容列表 |
| https://www.modelscope.cn/api/v1/console/admin/brands | DELETE | Query | JSON | 管理后台的品牌 |
| https://www.modelscope.cn/api/v1/console/admin/brands | GET | Query | JSON | 管理后台的品牌 |
| https://www.modelscope.cn/api/v1/console/admin/brands | POST | JSON | JSON | 管理后台的品牌 |
| https://www.modelscope.cn/api/v1/console/admin/brands | PUT | JSON | JSON | 管理后台的品牌 |
| https://www.modelscope.cn/api/v1/console/admin/mcpServer/claims | GET | Query | JSON | 认领管理后台/MCP 服务 |
| https://www.modelscope.cn/api/v1/console/admin/mcpServer/claims | POST | JSON | JSON | 认领管理后台/MCP 服务 |
| https://www.modelscope.cn/api/v1/console/admin/mcpServer/create | POST | JSON | JSON | 创建管理后台/MCP 服务 |
| https://www.modelscope.cn/api/v1/console/admin/mcpServer/move_down | PUT | JSON | JSON | 移动管理后台/MCP 服务 |
| https://www.modelscope.cn/api/v1/console/admin/mcpServer/move_up | PUT | JSON | JSON | 移动管理后台/MCP 服务 |
| https://www.modelscope.cn/api/v1/console/admin/models | POST | JSON | JSON | 管理后台的模型 |
| https://www.modelscope.cn/api/v1/console/admin/models/certification | GET | Query | JSON | 管理后台/模型的认证 |
| https://www.modelscope.cn/api/v1/console/admin/models/popular | POST | JSON | JSON | 管理后台/模型的热门 |
| https://www.modelscope.cn/api/v1/console/admin/models/popular/list | GET | Query | JSON | 管理后台/模型列表 |
| https://www.modelscope.cn/api/v1/console/admin/papers/claims | GET | Query | JSON | 认领管理后台/论文 |
| https://www.modelscope.cn/api/v1/console/admin/papers/claims | POST | JSON | JSON | 认领管理后台/论文 |
| https://www.modelscope.cn/api/v1/console/admin/studios | POST | JSON | JSON | 管理后台的创空间 |
| https://www.modelscope.cn/api/v1/console/admin/studios/listResource | GET | Query | JSON | 管理后台/创空间列表 |
| https://www.modelscope.cn/api/v1/console/admin/studios/resourceServices | GET | Query | JSON | 管理后台/创空间的资源 |
| https://www.modelscope.cn/api/v1/console/admin/users | DELETE | Query | JSON | 管理后台的用户 |
| https://www.modelscope.cn/api/v1/console/admin/users | GET | Query | JSON | 管理后台的用户 |

## 控制台 Console/comments（1 条）

> 评论内容审核与管理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/console/comments/open | POST | JSON | JSON | 评论开放 |

## 控制台 Console/config（7 条）

> 平台配置项管理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/console/config | GET | Query | JSON | 配置（平台配置项管理） |
| https://www.modelscope.cn/api/v1/console/config | POST | JSON | JSON | 配置（平台配置项管理） |
| https://www.modelscope.cn/api/v1/console/config/list | GET | Query | JSON | config列表 |
| https://www.modelscope.cn/api/v1/console/config/list/business | GET | Query | JSON | businessconfig/列表 |
| https://www.modelscope.cn/api/v1/console/config/list/business/event_type | GET | Query | JSON | 列表事件类型 |
| https://www.modelscope.cn/api/v1/console/config/list/business/global-config | GET | Query | JSON | 配置config/列表 |
| https://www.modelscope.cn/api/v1/console/config/list/business/homeIntroduce | GET | Query | JSON | 列表首页介绍 |

## 控制台 Console/datasetCommentTags（1 条）

> 数据集评论标签管理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/console/datasetCommentTags | POST | JSON | JSON | 设置（数据集评论标签管理） |

## 控制台 Console/datasets（1 条）

> 数据集后台管理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/console/datasets/comments | GET | Query | JSON | 评论数据集 |

## 控制台 Console/message（2 条）

> 站内消息后台管理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/console/message/task/list | GET | Query | JSON | 消息/task列表 |
| https://www.modelscope.cn/api/v1/console/message/task/send | POST | JSON | JSON | 消息/task的发送 |

## 控制台 Console/modelCommentTags（1 条）

> 模型评论标签管理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/console/modelCommentTags | POST | JSON | JSON | 评论（模型评论标签管理） |

## 控制台 Console/models（1 条）

> 模型仓库后台管理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/console/models/comments | POST | JSON | JSON | 评论模型 |

## 控制台 Console/operation（7 条）

> 运营数据统计与操作

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/console/operation/event | POST | JSON | JSON | operation的事件 |
| https://www.modelscope.cn/api/v1/console/operation/event/list | GET | Query | JSON | operation/事件列表 |
| https://www.modelscope.cn/api/v1/console/operation/event/search | GET | Query | JSON | 搜索operation/事件 |
| https://www.modelscope.cn/api/v1/console/operation/event/stage | DELETE | Query | JSON | operation/事件的标签 |
| https://www.modelscope.cn/api/v1/console/operation/event/stage | GET | Query | JSON | operation/事件的标签 |
| https://www.modelscope.cn/api/v1/console/operation/event/stage | POST | JSON | JSON | operation/事件的标签 |
| https://www.modelscope.cn/api/v1/console/operation/event/stage | PUT | JSON | JSON | operation/事件的标签 |

## 控制台 Console/personal（15 条）

> 个人中心后台管理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/console/personal/agents | POST | JSON | JSON | 个人的智能体 |
| https://www.modelscope.cn/api/v1/console/personal/agg | POST | JSON | JSON | 个人的聚合 |
| https://www.modelscope.cn/api/v1/console/personal/articles | POST | JSON | JSON | articles个人 |
| https://www.modelscope.cn/api/v1/console/personal/brands | POST | JSON | JSON | 个人的品牌 |
| https://www.modelscope.cn/api/v1/console/personal/collections | POST | JSON | JSON | 收藏个人 |
| https://www.modelscope.cn/api/v1/console/personal/gallery | POST | JSON | JSON | 个人的画廊 |
| https://www.modelscope.cn/api/v1/console/personal/mcpServers | POST | JSON | JSON | 个人的MCP 服务 |
| https://www.modelscope.cn/api/v1/console/personal/models | POST | JSON | JSON | 个人的模型 |
| https://www.modelscope.cn/api/v1/console/personal/papers | POST | JSON | JSON | 个人的论文 |
| https://www.modelscope.cn/api/v1/console/personal/pictures | POST | JSON | JSON | 个人的图片 |
| https://www.modelscope.cn/api/v1/console/personal/pictures | PUT | JSON | JSON | 个人的图片 |
| https://www.modelscope.cn/api/v1/console/personal/pictures/label/top | PUT | JSON | JSON | 个人/图片的热门 |
| https://www.modelscope.cn/api/v1/console/personal/pictures/label/untop | PUT | JSON | JSON | 个人/图片的热门 |
| https://www.modelscope.cn/api/v1/console/personal/skills | POST | JSON | JSON | 个人的技能 |
| https://www.modelscope.cn/api/v1/console/personal/studios | POST | JSON | JSON | 个人的创空间 |

## 控制台 Console/studioCommentTags（1 条）

> 工作空间评论标签管理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/console/studioCommentTags | POST | JSON | JSON | 评论（工作空间评论标签管理） |

## 控制台 Console/studios（1 条）

> 工作空间后台管理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/console/studios/comments  | POST | JSON | JSON | 评论创空间 |

## 控制台 Console/trend（3 条）

> 趋势数据后台管理

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/console/trend | GET | Query | JSON | 趋势（趋势数据后台管理） |
| https://www.modelscope.cn/api/v1/console/trend | POST | JSON | JSON | 趋势（趋势数据后台管理） |
| https://www.modelscope.cn/api/v1/console/trend/reset | GET | Query | JSON | 设置趋势 |

## 控制台 Console/user（1 条）

> 用户管理后台

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/console/user/group | GET | Query | JSON | 用户的分组 |

## 推理（1 条）

> 在线推理服务的模板与配额查询

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/inference/rate-limit | GET | Query | JSON | 限流（在线推理服务的模板与配额查询） |

## 文档（1 条）

> 文档内容获取

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/document | GET | Query | JSON | 查询数据 |

## 智能体 ID（1 条）

> 智能体 ID 绑定与解析

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/agent_ids | POST | JSON | JSON | 创建/提交数据 |

## 目录（1 条）

> 目录（Catalogue）浏览接口

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/catalogues | GET | Query | JSON | 查询数据 |

## 菜单（1 条）

> 导航菜单数据

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/menu | GET | Query | JSON | 查询数据 |

## 认证（7 条）

> 账号登录、注册、改密、验证码与绑定校验

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/auth/check | POST | JSON | JSON | 检查（账号登录、注册、改密、验证码与绑定校验） |
| https://www.modelscope.cn/api/v1/auth/login/generate/oauth2/url | GET | Query | JSON | 登录/生成的链接 |
| https://www.modelscope.cn/api/v1/auth/login/user/create | POST | JSON | JSON | 创建登录/用户 |
| https://www.modelscope.cn/api/v1/auth/loginRegister | POST | JSON | JSON | 登录注册（账号登录、注册、改密、验证码与绑定校验） |
| https://www.modelscope.cn/api/v1/auth/resetPassword | POST | JSON | JSON | 重置密码（账号登录、注册、改密、验证码与绑定校验） |
| https://www.modelscope.cn/api/v1/auth/sendEmail | POST | JSON | JSON | 发送邮件（账号登录、注册、改密、验证码与绑定校验） |
| https://www.modelscope.cn/api/v1/auth/syncInviteCode | POST | JSON | JSON | 同步邀请码（账号登录、注册、改密、验证码与绑定校验） |

## 讨论（1 条）

> 讨论区数据

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/discussions/datasetsComment/comments/tags | GET | Query | JSON | 评论标签 |

## 许可证（1 条）

> 开源许可证查询

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/licenses | GET | Query | JSON | 查询数据 |

## 趋势（1 条）

> 趋势分析与统计接口

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/trend | GET | Query | JSON | 查询数据 |

## 邀请码（3 条）

> 邀请码生成与校验

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/invitationCodes | POST | JSON | JSON | 创建/提交数据 |
| https://www.modelscope.cn/api/v1/invitationCodes/latest | GET | Query | JSON | 最新（邀请码生成与校验） |
| https://www.modelscope.cn/api/v1/invitationCodes/refresh | POST | JSON | JSON | 刷新（邀请码生成与校验） |

## 配额（1 条）

> 资源配额查询与调整

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.modelscope.cn/api/v1/quota/status | GET | Query | JSON | 统计（资源配额查询与调整） |

---

## 附：modelscope.cn 所属域名与子域名清单

抓取中共出现 **10 个 modelscope 系域名**。以下按"是否承载接口请求"分类。

### A. 承载接口请求的域名（业务 API 宿主）

| 域名 | 角色 | 说明 |
| :--- | :--- | :--- |
| `www.modelscope.cn` | **主 API 宿主** | 全部 432 条接口均以此为宿主，通过 `/api`、`/openapi`、`/ds` 三个路径前缀区分 |
| `modelscope.cn` | 主站 / 跳转入口 | 302 → `www.modelscope.cn/home`；前端路由与规范域名 |

**关键结论**：ModelScope 前端采用**同源相对路径**请求，`umi.js` 中仅存在 `new F("/api").http` 与 `new F("/openapi").http` 两个客户端实例，**未配置任何跨域 baseURL**。因此全部业务接口统一落在 `https://www.modelscope.cn` 单一主机上。

实测验证（未带 Cookie）：

| 探测路径 | 状态码 | 说明 |
| :--- | :--- | :--- |
| `/api/v1/papers` | 200 | 公开可访问 |
| `/openapi/v1/agents` | 200 | 公开可访问 |
| `/api/v1/medal/console/list/all` | 401 | 需登录 |
| `/api/v1/brands` | 400 | 参数校验 |
| `/api/v1/datasets` | 500 | 缺必填参数 |

### B. 资源 / 存储类子域名（非 API，已排除）

| 域名 | 用途 | 出现次数 |
| :--- | :--- | :--- |
| `resouces.modelscope.cn` | 图片/视频资源 CDN（官方拼写为 `resouces`，少一个 c） | 254 |
| `resources.modelscope.cn` | 同上（另一写法） | 49 |
| `resources.modelscope.ai` | 资源 CDN 镜像节点 | 4 |
| `www.modelscope.ai` | `.ai` 镜像站 | 2 |

### C. 环境与后台子域名（代码中硬编码，非生产调用）

| 域名 | 用途 | 出现处数 |
| :--- | :--- | :--- |
| `pre.modelscope.cn` | 预发布环境 | 57 |
| `test.modelscope.cn` | 测试环境 | 19 |
| `dev.modelscope.cn` | 开发环境 | 9 |
| `admin.modelscope.cn` | 管理后台 | 5 |
| `test-admin.modelscope.cn` | 测试管理后台 | 5 |

这些域名多出现在错误页、环境判断与白名单常量中；抓包层面的接口调用全部指向 `www.modelscope.cn`，故未拆为独立接口表。

### D. 短链与白名单域名

| 域名 | 用途 |
| :--- | :--- |
| `s5k.cn` | 短链服务；出现在合法来源白名单常量 `["https://s5k.cn","https://modelscope.cn","https://*.ms.show","https://*.ms.fun","https://*.modelscope.cn"]` 中，非 API 宿主 |

### E. 已排除的第三方域名（摘要）

| 类别 | 域名示例 |
| :--- | :--- |
| 阿里云 CDN / OSS | `img.alicdn.com`(828)、`g.alicdn.com`、`gw.alicdn.com`、`alidocs.oss-cn-zhangjiakou.aliyuncs.com`、`modelscope-docs-dev.oss-cn-hangzhou.aliyuncs.com` |
| APM 埋点 | `arms-retcode.aliyuncs.com`、`arms-retcode-sg.aliyuncs.com`、`arms-retcode-daily.alibaba.net`、`retcode-us-west-1.arms.aliyuncs.com` |
| 第三方内容站 | `player.bilibili.com`、`www.bilibili.com`、`github.com`、`www.zhihu.com`、`www.xiaohongshu.com`、`www.douyin.com`、`x.com`、`huggingface.co`、`discord.gg`、`docs.langflow.org`、`mcp.amap.com` |
| 第三方登录 | `connect.qq.com`、`open.weixin.qq.com`、`service.weibo.com` |
| 政府 / 备案 | `beian.miit.gov.cn`、`beian.mps.gov.cn`、`zzlz.gsxt.gov.cn`、`ceba.wangsu.com` |
| 文档与工具 | `developer.aliyun.com`、`help.aliyun.com`、`free.aliyun.com`、`docs.qq.com`、`www.yuque.com`、`reactjs.org`、`www.w3.org` |

---

## 覆盖率统计

| 项目 | 数值 |
| :--- | :--- |
| 抓取 JS 文件 | 396 个（1 个 umi 主包 10.4 MB + 395 个 chunk 约 54 MB） |
| chunk 命名规律 | `p__<模块>__<页面>__index`（如 `p__mcp__ServersDetail__Tools__index`），可枚举站点全部功能模块 |
| 识别接口 | **432 条** |
| `/api/**` | 409 条 |
| `/openapi/**` | 11 条 |
| `/ds/**` | 12 条 |
| 方法精度 | GET / POST / PUT / DELETE / PATCH 全部由枚举模块 `749337` 精确解出，**无推测** |
| 顶级业务域 | 约 40 个（models、datasets、papers、studios、agents、mcpServers、skills、organizations、collections、leaderboards、competitions、medal、brands、gallery、notebooks、aigc、workflow、rm、train、deploy、themes、activities、articles、comments、banners、tags、dolphin、console/* 等） |
| 待验证项 | `/api/fbi/report`（1 条，路径宽泛，疑为内部 BI 报表接口） |

**核心结论**：ModelScope 采用**单一主机 + 三前缀**（`/api`、`/openapi`、`/ds`）的 API 架构，全部业务接口同源部署于 `www.modelscope.cn`，不存在跨域子域调用。鉴权走 Cookie `csrf_token` + `X-CSRF-TOKEN` 请求头，链路追踪通过 `X-Modelscope-Trace-Id` 与返回体 `RequestId` 双通道。
