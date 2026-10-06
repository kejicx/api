# DeepSeek（chat.deepseek.com + platform.deepseek.com）API 接口文档

> 站点：DeepSeek 深度求索 — 对话助手（chat.deepseek.com）与开发者平台（platform.deepseek.com），并含官方 OpenAI 兼容 API（api.deepseek.com）
> 抓取时间：2026-10-06
> 数据来源（混合）：
> 1. **官方公开 API**：`api-docs.deepseek.com`（Docusaurus 文档站，服务端渲染 OpenAPI）9 个接口页，对应 `https://api.deepseek.com` 网关，属官方公开、仅作整理翻译；
> 2. **前端逆向接口**：`chat.deepseek.com`（commit 44809ea4）与 `platform.deepseek.com`（commit d3e892c1）两个 webpack SPA，无公开文档。抓取首页 → 从 `main.js` 的 `.u=` 函数解析 webpack 异步 chunk 映射表 → 全量下载 chat 605 个 + platform 322 个 chunk + 主包/vendors（约 24 MB）→ 以 `/api/v0|v1`、`/auth-api/v0`、`/oauth` 路径字面量为锚点正则提取，回溯调用点 `.get()/.post()/.put()/.delete()` 与 `method:"xxx"` 还原 HTTP 方法。
> **接口总数：112 条**（chat.deepseek.com 51、platform.deepseek.com 52、api.deepseek.com 9；88 条唯一 SPA 路径 + 5 条支付动态子端点 + 1 条 WebSocket + 官方 9 条，含同路径多方法）
> 方法证据：61 条调用点直接证据、27 条按路径语义推断；已排除 CDN/OSS、文档站、埋点、测试/预发/内网域名与第三方站点

---

## 一、通用约定

| 项目 | 说明 |
| :--- | :--- |
| 对话前端入口 | `https://chat.deepseek.com` + 相对路径，如 `/api/v0/chat/completion`（SPA 同域请求，无独立网关） |
| 开发者平台入口 | `https://platform.deepseek.com` + 相对路径，如 `/api/v0/usage/by_api_key/cost` |
| 鉴权子网关 | `https://platform.deepseek.com/auth-api/v0/**`（登录/注册）与 `https://chat.deepseek.com/oauth/**`（第三方授权） |
| 官方 API 入口 | `https://api.deepseek.com` + 路径，如 `/chat/completions`（OpenAI 兼容，无 `/v1` 前缀） |
| 官方鉴权 | `Authorization: Bearer <API_KEY>`；`Content-Type: application/json` |
| 前端鉴权 | Cookie（`token` 登录态）+ 请求头 `Authorization: Bearer <jwt>`；写操作附带 `X-Requested-With` |
| 防滥用 | 对话补全前需 `POST /api/v0/chat/create_pow_challenge` 完成工作量证明（PoW） |
| 请求格式 | `GET`/`DELETE` 走 Query 参数；`POST`/`PUT`/`PATCH` 走 JSON body；文件上传走 `multipart/form-data`；`/chat/tts/` 走 WebSocket（`wss://`） |
| 返回格式 | JSON。统一信封 `{ code, msg, data }`；官方 API 遵循 OpenAI 响应结构 |
| 流式返回 | `/api/v0/chat/completion` 等以 SSE（`text/event-stream`）流式输出；`/chat/tts/` 以 WebSocket 二进制音频流输出 |
| 路径变量 | 表中 `{order_id}`、`{file_id}` 代表运行时拼接变量，如 `/api/v1/payments/{order_id}/capture` |

**请求方法与语义对照**：`GET`=查询、`POST`=创建/提交、`PUT`=整体更新、`PATCH`=局部修改、`DELETE`=删除、`WS`=WebSocket 流。同一路径出现多个方法时表示该资源支持多种操作。

## 对话与消息（9 条）

> chat.deepseek.com 对话页：补全、续写、编辑、重生成、流控、反馈、防滥用挑战

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://chat.deepseek.com/api/v0/chat/completion | POST | JSON | JSON | 发起对话补全（流式返回模型回复） |
| https://chat.deepseek.com/api/v0/chat/continue | POST | JSON | JSON | 继续生成被中断的回复 |
| https://chat.deepseek.com/api/v0/chat/create_pow_challenge | POST | JSON | JSON | 创建工作量证明(PoW)防滥用挑战 |
| https://chat.deepseek.com/api/v0/chat/edit_message | POST | JSON | JSON | 编辑已发送的消息 |
| https://chat.deepseek.com/api/v0/chat/history_messages | GET | Query | JSON | 获取会话历史消息 |
| https://chat.deepseek.com/api/v0/chat/message_feedback | POST | JSON | JSON | 提交消息点赞/点踩反馈 |
| https://chat.deepseek.com/api/v0/chat/regenerate | POST | JSON | JSON | 重新生成回复 |
| https://chat.deepseek.com/api/v0/chat/resume_stream | POST | JSON | JSON | 恢复中断的输出流 |
| https://chat.deepseek.com/api/v0/chat/stop_stream | POST | JSON | JSON | 停止当前输出流 |

## 会话管理（6 条）

> chat.deepseek.com：会话的创建、删除、分页、标题与置顶

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://chat.deepseek.com/api/v0/chat_session/batch_update_pinned | POST | JSON | JSON | 批量更新会话置顶状态 |
| https://chat.deepseek.com/api/v0/chat_session/create | POST | JSON | JSON | 创建新会话 |
| https://chat.deepseek.com/api/v0/chat_session/delete | POST | JSON | JSON | 删除指定会话 |
| https://chat.deepseek.com/api/v0/chat_session/delete_all | POST | JSON | JSON | 删除全部会话 |
| https://chat.deepseek.com/api/v0/chat_session/fetch_page | GET | Query | JSON | 分页拉取会话列表 |
| https://chat.deepseek.com/api/v0/chat_session/update_title | POST | JSON | JSON | 修改会话标题 |

## 语音合成 TTS（3 条）

> chat.deepseek.com：朗读音色与实时语音合成（含 WebSocket 音频流）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://chat.deepseek.com/api/v0/chat/tts/ | WS | WebSocket | 音频流(二进制) | WebSocket 实时语音合成音频流(wss) |
| https://chat.deepseek.com/api/v0/chat/tts/voice | POST | JSON | JSON | 合成指定语音的朗读音频 |
| https://chat.deepseek.com/api/v0/chat/tts/voices | GET | Query | JSON | 获取可用音色列表 |

## 文件与附件（3 条）

> chat.deepseek.com：附件上传、列表与解析任务

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://chat.deepseek.com/api/v0/file/fetch_files | GET | Query | JSON | 获取已上传文件列表 |
| https://chat.deepseek.com/api/v0/file/fork_file_task | POST | JSON | JSON | 派生文件解析任务 |
| https://chat.deepseek.com/api/v0/file/upload_file | POST | JSON | JSON | 上传文件（multipart） |

## 联网搜索（2 条）

> chat.deepseek.com：联网检索的准备与查询

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://chat.deepseek.com/api/v0/index/prepare | GET | Query | JSON | 准备联网搜索上下文 |
| https://chat.deepseek.com/api/v0/index/query | POST | JSON | JSON | 执行联网检索查询 |

## 对话分享（5 条）

> chat.deepseek.com：对话分享的创建、读取、列表、克隆与删除

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://chat.deepseek.com/api/v0/share/content | GET | Query | JSON | 读取分享内容 |
| https://chat.deepseek.com/api/v0/share/create | POST | JSON | JSON | 创建对话分享链接 |
| https://chat.deepseek.com/api/v0/share/delete | POST | JSON | JSON | 删除分享 |
| https://chat.deepseek.com/api/v0/share/fork | POST | JSON | JSON | 克隆（Fork）他人分享的对话 |
| https://chat.deepseek.com/api/v0/share/list | GET | Query | JSON | 获取分享列表 |

## 数据导出（2 条）

> chat.deepseek.com：全量数据导出与导出历史下载

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://chat.deepseek.com/api/v0/download_export_history | GET | Query | JSON | 下载历史导出记录 |
| https://chat.deepseek.com/api/v0/export_all | GET | Query | JSON | 导出全部对话数据 |

## 用户与账号（14 条）

> chat/platform：账号资料、设置、验证码、会话注销与鉴权票据

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://chat.deepseek.com/api/v0/auth/ticket | POST | JSON | JSON | 获取鉴权票据（ticket） |
| https://chat.deepseek.com/api/v0/users | POST | JSON | JSON | 用户资源（读取/更新账号信息） |
| https://chat.deepseek.com/api/v0/users/create_email_verification_code | POST | JSON | JSON | 创建邮箱验证码 |
| https://platform.deepseek.com/api/v0/users/create_email_verification_code | POST | JSON | JSON | 创建邮箱验证码 |
| https://chat.deepseek.com/api/v0/users/create_guest_challenge | POST | JSON | JSON | 创建游客访问挑战 |
| https://chat.deepseek.com/api/v0/users/create_sms_verification_code | POST | JSON | JSON | 创建短信验证码 |
| https://platform.deepseek.com/api/v0/users/create_sms_verification_code | POST | JSON | JSON | 创建短信验证码 |
| https://chat.deepseek.com/api/v0/users/get_birthday | GET | Query | JSON | 获取生日信息 |
| https://platform.deepseek.com/api/v0/users/get_user_summary | GET | Query | JSON | 获取用户概要信息 |
| https://chat.deepseek.com/api/v0/users/logout_all_sessions | POST | JSON | JSON | 注销全部登录会话 |
| https://platform.deepseek.com/api/v0/users/set_alert_bound | POST | JSON | JSON | 设置余额告警阈值 |
| https://chat.deepseek.com/api/v0/users/set_birthday | POST | JSON | JSON | 设置生日信息 |
| https://chat.deepseek.com/api/v0/users/settings | GET | Query | JSON | 获取用户个人设置 |
| https://chat.deepseek.com/api/v0/users/update_settings | POST | JSON | JSON | 更新用户个人设置 |

## API Key 管理（2 条）

> platform.deepseek.com：开发者 API Key 的查询与增删改

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://platform.deepseek.com/api/v0/users/edit_api_keys | POST | JSON | JSON | 创建/编辑/删除 API Key |
| https://platform.deepseek.com/api/v0/users/get_api_keys | GET | Query | JSON | 获取 API Key 列表 |

## 用量与计费（5 条）

> platform.deepseek.com：按 Key 的用量/消费、导出与费用估算

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://platform.deepseek.com/api/v0/pricing/estimate_tax | GET | Query | JSON | 估算税费 |
| https://platform.deepseek.com/api/v0/pricing/estimate_token | POST | JSON | JSON | 估算 Token 消耗与费用 |
| https://platform.deepseek.com/api/v0/usage/by_api_key/amount | GET | Query | JSON | 按 API Key 查询用量额度 |
| https://platform.deepseek.com/api/v0/usage/by_api_key/cost | GET | Query | JSON | 按 API Key 查询消费金额 |
| https://platform.deepseek.com/api/v0/usage/export | GET | Query | JSON | 导出用量明细 |

## 充值与支付（7 条）

> platform.deepseek.com：支付订单、捕获、回执、账单地址与对公汇款

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://platform.deepseek.com/api/v0/company/remit | POST | JSON | JSON | 企业对公转账汇款 |
| https://platform.deepseek.com/api/v1/payments | POST | JSON | JSON | 创建支付订单 |
| https://platform.deepseek.com/api/v1/payments/{order_id}/billing_address | GET | Query | JSON | 获取账单地址 |
| https://platform.deepseek.com/api/v1/payments/{order_id}/capture | POST | JSON | JSON | 确认/捕获支付 |
| https://platform.deepseek.com/api/v1/payments/{order_id}/order_detail | POST | JSON | JSON | 查询订单详情 |
| https://platform.deepseek.com/api/v1/payments/{order_id}/receipt | POST | JSON | JSON | 生成支付回执 |
| https://platform.deepseek.com/api/v1/payments/{order_id}/track_antom_submit | POST | JSON | JSON | 上报 Antom 支付提交埋点 |

## 发票管理（5 条）

> platform.deepseek.com：开票申请、作废、历史、可开额度与抬头联想

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://platform.deepseek.com/api/v0/fapiao/apply | POST | JSON | JSON | 申请开具发票 |
| https://platform.deepseek.com/api/v0/fapiao/available_amount | GET | Query | JSON | 查询可开票金额 |
| https://platform.deepseek.com/api/v0/fapiao/company_hint | GET | Query | JSON | 获取发票抬头联想 |
| https://platform.deepseek.com/api/v0/fapiao/history | GET | Query | JSON | 获取开票历史 |
| https://platform.deepseek.com/api/v0/fapiao/void | POST | JSON | JSON | 作废发票 |

## 退款（3 条）

> platform.deepseek.com：退款申请、历史与可退额度

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://platform.deepseek.com/api/v0/refund/apply | POST | JSON | JSON | 提交退款申请 |
| https://platform.deepseek.com/api/v0/refund/available_amount | GET | Query | JSON | 查询可退款金额 |
| https://platform.deepseek.com/api/v0/refund/history | GET | Query | JSON | 获取退款历史 |

## 企业与实名认证（8 条）

> platform.deepseek.com：企业/个人实名认证与子账号

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://platform.deepseek.com/api/v0/company/business_verification | GET | Query | JSON | 获取企业认证信息 |
| https://platform.deepseek.com/api/v0/company/create_business_sub_account | POST | JSON | JSON | 创建企业子账号 |
| https://platform.deepseek.com/api/v0/company/get_business_sub_account | GET | Query | JSON | 获取企业子账号 |
| https://platform.deepseek.com/api/v0/company/sync_business_verification | POST | JSON | JSON | 同步企业认证状态 |
| https://platform.deepseek.com/api/v0/company/verify | POST | JSON | JSON | 提交企业认证 |
| https://platform.deepseek.com/api/v1/identity_verify | POST | JSON | JSON | 提交个人实名认证 |
| https://platform.deepseek.com/api/v1/my_business_verification | GET | Query | JSON | 查询本人企业认证状态 |
| https://platform.deepseek.com/api/v1/my_identity_verification | GET | Query | JSON | 查询本人实名认证状态 |

## 客户端配置与埋点（5 条）

> chat/platform：客户端配置、配置上报、链路埋点与微信 JS-SDK 签名

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://chat.deepseek.com/api/v0/client/settings | GET | Query | JSON | 获取客户端配置 |
| https://platform.deepseek.com/api/v0/client/settings | GET | Query | JSON | 获取客户端配置 |
| https://chat.deepseek.com/api/v0/client/settings/report | POST | JSON | JSON | 上报客户端配置 |
| https://chat.deepseek.com/api/v0/client/span | POST | JSON | JSON | 上报链路埋点(span) |
| https://chat.deepseek.com/api/v0/client/wechat_js_sdk_signature | GET | Query | JSON | 获取微信 JS-SDK 签名 |

## 登录与第三方授权（24 条）

> chat/platform：账密/短信/游客登录、注册、Apple/微信 OAuth 与令牌换取

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://platform.deepseek.com/auth-api/v0/dsh/auth_approve | POST | JSON | JSON | DSH 场景授权确认 |
| https://platform.deepseek.com/auth-api/v0/users | POST | JSON | JSON | 账号资源（注册/登录相关） |
| https://platform.deepseek.com/auth-api/v0/users/create_guest_challenge | POST | JSON | JSON | 创建游客登录挑战 |
| https://platform.deepseek.com/auth-api/v0/users/get_all_invoice | GET | Query | JSON | 获取全部发票信息 |
| https://platform.deepseek.com/auth-api/v0/users/login | POST | JSON | JSON | 账号密码登录 |
| https://platform.deepseek.com/auth-api/v0/users/login_by_mobile_sms | POST | JSON | JSON | 手机短信验证码登录 |
| https://platform.deepseek.com/auth-api/v0/users/logout_all_sessions | POST | JSON | JSON | 注销全部登录会话 |
| https://platform.deepseek.com/auth-api/v0/users/register | POST | JSON | JSON | 邮箱注册 |
| https://platform.deepseek.com/auth-api/v0/users/register_by_mobile | POST | JSON | JSON | 手机号注册 |
| https://chat.deepseek.com/oauth/apple/login | POST | JSON | JSON | Apple 账号登录 |
| https://platform.deepseek.com/oauth/apple/login | POST | JSON | JSON | Apple 账号登录 |
| https://chat.deepseek.com/oauth/apple/mobile_verification | POST | JSON | JSON | Apple 登录手机验证 |
| https://platform.deepseek.com/oauth/apple/mobile_verification | POST | JSON | JSON | Apple 登录手机验证 |
| https://platform.deepseek.com/oauth/callback | POST | JSON | JSON | 通用 OAuth 回调 |
| https://chat.deepseek.com/oauth/get_token | GET | Query | JSON | 用授权码换取访问令牌 |
| https://platform.deepseek.com/oauth/get_token | GET | Query | JSON | 用授权码换取访问令牌 |
| https://chat.deepseek.com/oauth/wechat/bind | POST | JSON | JSON | 绑定微信 |
| https://platform.deepseek.com/oauth/wechat/bind | POST | JSON | JSON | 绑定微信 |
| https://chat.deepseek.com/oauth/wechat/callback | POST | JSON | JSON | 微信 OAuth 回调 |
| https://platform.deepseek.com/oauth/wechat/callback | POST | JSON | JSON | 微信 OAuth 回调 |
| https://chat.deepseek.com/oauth/wechat/mobile_verification | POST | JSON | JSON | 微信登录手机验证 |
| https://platform.deepseek.com/oauth/wechat/mobile_verification | POST | JSON | JSON | 微信登录手机验证 |
| https://chat.deepseek.com/oauth/wechat/unbind | DELETE | Query | JSON | 解绑微信 |
| https://platform.deepseek.com/oauth/wechat/unbind | DELETE | Query | JSON | 解绑微信 |

## 官方 API（api.deepseek.com）（9 条）

> DeepSeek 官方公开 OpenAI 兼容 API（来源 api-docs.deepseek.com）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://api.deepseek.com/chat/completions | POST | JSON | JSON | Chat Completions：对话式补全 |
| https://api.deepseek.com/completions | POST | JSON | JSON | Completions：文本补全 |
| https://api.deepseek.com/files | GET | Query | JSON | Files：列出文件 |
| https://api.deepseek.com/files | POST | JSON | JSON | Files：上传文件（multipart） |
| https://api.deepseek.com/files/{file_id} | GET | Query | JSON | Files：检索单个文件 |
| https://api.deepseek.com/files/{file_id} | DELETE | Query | JSON | Files：删除文件 |
| https://api.deepseek.com/models | GET | Query | JSON | Models：列出可用模型 |
| https://api.deepseek.com/responses | POST | JSON | JSON | Responses：响应式生成 |
| https://api.deepseek.com/user/balance | GET | Query | JSON | Balance：查询账户余额 |
---

## 二、域名清单

> 从 chat/platform 全部前端 bundle（930 个 JS）中提取到的 `*.deepseek.com` 域名共 18 个，按用途归类。仅 A 类承载接口，B/C 类不收录接口。

### A. 承载 API 接口的域名（3 个）

| 域名 | 角色 | 说明 |
| :--- | :--- | :--- |
| `api.deepseek.com` | 官方 API 网关 | OpenAI 兼容对外接口，返回 401 `Authentication Fails` 确认存在，鉴权 `Bearer <API_KEY>` |
| `chat.deepseek.com` | 对话助手前端 | SPA 同域请求 `/api/v0/**`、`/oauth/**`，含 SSE 流式与 WebSocket TTS |
| `platform.deepseek.com` | 开发者平台前端 | SPA 同域请求 `/api/v0|v1/**`、`/auth-api/v0/**`，含计费/发票/实名 |

### B. 静态资源与站点域名（6 个，不承载业务接口）

| 域名 | 用途 |
| :--- | :--- |
| `fe-static.deepseek.com` | 前端静态资源 CDN（chat/platform 的 main.js 与全部异步 chunk） |
| `cdn.deepseek.com` | 图片、服务配置、政策协议等静态资源 |
| `static.deepseek.com` | FAQ 等静态页面 |
| `www.deepseek.com` | 官网主站（harness 条款/隐私） |
| `download.deepseek.com` | 客户端下载 |
| `applink.deepseek.com` | 分享唤起链接（chat/share） |

### C. 文档、监控与测试/内网域名（9 个，已排除）

| 域名 | 用途 |
| :--- | :--- |
| `api-docs.deepseek.com` | 官方 API 文档站（Docusaurus，本文档官方部分来源） |
| `status.deepseek.com` | 服务状态监控页 |
| `gitlab.deepseek.com` | 内部代码托管 |
| `chat-dev.deepseek.com` / `chat-test.deepseek.com` | 对话环境 开发/测试 |
| `platform-dev.deepseek.com` / `platform-test.deepseek.com` | 平台环境 开发/测试 |
| `hif-test.deepseek.com` / `hif-leim.deepseek.com` / `hif-dliq.deepseek.com` | 内部实验/灰度（`/query`） |

---

## 三、统计汇总

| 维度 | 数值 |
| :--- | :--- |
| 接口条目总数 | 112 |
| 唯一「域名+路径」 | 110 |
| 业务分类数 | 17 |
| 承载接口域名 | 3（chat / platform / api） |

**按域名**：chat.deepseek.com 51 条、platform.deepseek.com 52 条、api.deepseek.com 9 条

**按方法**：GET 37、POST 71、DELETE 3、WS 1

**按请求格式**：Query 40、JSON 71、WebSocket 1

**按业务分类**：

| 分类 | 条数 |
| :--- | :--- |
| 登录与第三方授权 | 24 |
| 用户与账号 | 14 |
| 对话与消息 | 9 |
| 官方 API（api.deepseek.com） | 9 |
| 企业与实名认证 | 8 |
| 充值与支付 | 7 |
| 会话管理 | 6 |
| 客户端配置与埋点 | 5 |
| 发票管理 | 5 |
| 用量与计费 | 5 |
| 对话分享 | 5 |
| 语音合成 TTS | 3 |
| 文件与附件 | 3 |
| 退款 | 3 |
| 数据导出 | 2 |
| 联网搜索 | 2 |
| API Key 管理 | 2 |

---

## 四、范围与免责说明

- **官方 API 部分**（api.deepseek.com，9 条）来自 `api-docs.deepseek.com` 公开文档，权威且完整，仅作整理翻译。
- **前端逆向部分**（chat / platform，103 条）基于前端 JS 静态分析，未包含仅服务端或登录后/风控态才暴露的接口；`/api/v0/chat/tts/` 为 WebSocket 音频流端点。
- **方法证据**：61 条来自调用点 `.get()/.post()/.put()/.delete()` 直接匹配，27 条按路径末段语义推断（create/verify→POST、get/list→GET、delete/logout→DELETE），可能存在个别偏差。
- **动态拼接端点**：`/api/v1/payments/{order_id}/*` 由 `.concat()` 还原，`{order_id}` 为运行时变量；裸 `/v0/users/*` 为 auth 客户端相对路径，已归并至 `/auth-api/v0/*`，不重复计数。
- 前端接口需登录态 Cookie 与 Bearer JWT 方可调用，仅作技术研究与接口梳理参考，请遵守 DeepSeek 服务条款与 robots 协议，勿用于批量抓取或滥用。
