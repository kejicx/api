# 小红书网页版 API 接口文档

> 站点：小红书 Xiaohongshu / RED — 生活方式社区（笔记 / 信息流 / 搜索 / 评论 / 直播 / 私信 IM / 看板 / AI 问答与智能体）
> 抓取时间：2026-10-05
> 抓取范围：`https://www.xiaohongshu.com/explore` → 主站工程 `xhs-pc-web`（Vite/webpack 混合）→ 从 `bundler-runtime.*.js` 解析出 **388 个异步业务 chunk 映射表**，全量下载 `resource/js/async/{id}.{hash}.js`（约 15 MB）+ 6 个主包（vendor / index / runtime）
> 提取方式：无 sourcemap。以自研 axios 请求层（`baseURL = "//edith.xiaohongshu.com"`）为锚点，按 `"/api/..."` 字面量全量正则提取路径，再回溯调用点匹配 `.get()/.post()/.put()/.delete()` 与 `method:"xxx"` 得到 HTTP 方法；共 **175 条**方法来自调用点直接证据，其余按路径语义（`create/submit/like` → POST，`get/list/detail/info` → GET）推断
> **接口总数：259 条唯一路径 / 263 条接口条目**（含同路径多方法，如 `/api/sns/v1/history/report_web` 同时支持 GET 与 POST、`/api/im/v1/users/blocked-users/` 同时支持 POST 与 DELETE）。按宿主前缀：`/api/sns/**` 169 条、`/api/im/**` 80 条、验证码与安全（`/api/captcha/**`、`/api/redcaptcha/**`、`/api/sec/**`）7 条、其它（`impaas`、`qrcode`、`store`、`p/pj`、`sns` 根路径）3 条
> 已排除：图片/视频/头像 CDN（`*.xhscdn.com`、`picasso-static`、`sns-img-*`、`sns-video-*`、`sns-avatar-*`）、静态资源与字体图标、前端构建与内网 DevOps 域名（`*.devops.xiaohongshu.com`、`npm.`、`code.`、`apihub-v2.`）、APM 埋点（`apm-fe.xiaohongshu.com/api/data`、`apm-track`）、Sentry（`fesentry`）、测试与预发环境重复项（`edith.beta.`、`edith.sit.`、`t2-test`）、创作者/商业平台独立站点（`creator.xiaohongshu.com`、`pgy.xiaohongshu.com`、`zhaoshang.xiaohongshu.com`）的接口

---

## 一、通用约定

| 项目 | 说明 |
| :--- | :--- |
| 主 API 入口 | `https://edith.xiaohongshu.com` + 路径，如 `/api/sns/web/v1/homefeed`（前端以 `//edith.xiaohongshu.com` 拼接 baseURL，跨域携带 Cookie） |
| 预发 / 测试入口 | `https://edith.beta.xiaohongshu.com`、`https://edith.sit.xiaohongshu.com`；海外品牌 `https://webapi.rednote.com` |
| 页面与分享域名 | `https://www.xiaohongshu.com`（主站）、`https://s.xiaohongshu.com` / `https://xhslink.com`（短链）、`https://so.xiaohongshu.com`（搜索落地页）、`https://agree.xiaohongshu.com`（协议与合规页） |
| 鉴权 | Cookie `web_session`（登录态）+ `a1` / `webId` / `gid`（设备指纹）；写操作需请求头 `x-s`、`x-t`、`x-s-common`、`x-b3-traceid`（由 `xhs_a_*.js` 安全 SDK 生成） |
| 风控 | 命中风控时接口返回 `code=300012`（IP at risk）并跳转 `www.xiaohongshu.com/website-login/error`；滑块/图形验证码走 `/api/captcha/v2/**` 与 `/api/redcaptcha/v2/**`，安全采集走 `/api/sec/v1/**` 与 `as.xiaohongshu.com/api/sec/v1/shield/webprofile` |
| 请求格式 | `GET`/`DELETE` 走 Query（`params`）；`POST`/`PUT`/`PATCH` 走 JSON body；文件上传先取 `/api/sns/web/upload/permit` 凭证再直传 OSS（`multipart/form-data`） |
| 返回格式 | JSON，统一信封 `{ success, code, msg, data }`；列表类 `data` 内含 `{ items, cursor, has_more }` 游标分页字段 |
| 未登录返回 | 需登录接口返回 `success=false` + `code=-100`（未登录）；游客仅可访问部分 `/explore` 首屏 |
| 路径变量 | 表中 `{boardId}`、`{sourceId}` 代表运行时拼接的变量，实际调用如 `/api/sns/web/v1/board/5f1a2b3c` |
| 流式接口 | `/api/sns/web/v1/search/dqa/stream/tokens`、`/api/sns/agentspark/web/celestial/ai/messages` 使用 SSE / 分块传输返回增量 token |

**请求方法与语义对照**：`GET`=查询、`POST`=创建/提交/动作、`PUT`=整体更新、`DELETE`=删除。同一路径出现多个方法时表示该资源支持多种操作（如 `/api/im/v1/users/blocked-users/` 的 `POST`=拉黑、`DELETE`=解除拉黑）。

## 首页与信息流（9 条）

> 推荐流、频道分区、「你」Tab 动态与详情页相关推荐

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://edith.xiaohongshu.com/api/sns/v6/relatedfeed/web | GET | Query | JSON | 获取笔记详情页相关推荐信息流 |
| https://edith.xiaohongshu.com/api/sns/web/v1/common/dual_feed | GET | Query | JSON | 获取通用双列信息流 |
| https://edith.xiaohongshu.com/api/sns/web/v1/homefeed | POST | JSON | JSON | 获取首页推荐双列信息流 |
| https://edith.xiaohongshu.com/api/sns/web/v1/homefeed/category | GET | Query | JSON | 获取首页频道（分类）列表 |
| https://edith.xiaohongshu.com/api/sns/web/v1/homefeed/initial_load | POST | JSON | JSON | 首页首屏数据初始化加载 |
| https://edith.xiaohongshu.com/api/sns/web/v1/you/connections | GET | Query | JSON | 获取「你」Tab 的关注/粉丝动态 |
| https://edith.xiaohongshu.com/api/sns/web/v1/you/likes | GET | Query | JSON | 获取「你」Tab 的点赞动态 |
| https://edith.xiaohongshu.com/api/sns/web/v1/you/mentions | GET | Query | JSON | 获取「你」Tab 的 @ 提及动态 |
| https://edith.xiaohongshu.com/api/sns/web/v1/zones | GET | Query | JSON | 获取首页分区（Zone）配置 |

## 笔记与互动（18 条）

> 笔记详情、点赞收藏、不感兴趣、浏览历史、分享口令与指标上报

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://edith.xiaohongshu.com/api/sns/h5/v1/note_info | GET | Query | JSON | 获取 H5 分享页笔记信息 |
| https://edith.xiaohongshu.com/api/sns/v1/history/report_web | GET | Query | JSON | 上报 Web 端浏览历史 |
| https://edith.xiaohongshu.com/api/sns/v1/history/report_web | POST | JSON | JSON | 上报 Web 端浏览历史 |
| https://edith.xiaohongshu.com/api/sns/v1/note/user/page/search/web | GET | Query | JSON | 在用户主页内搜索其发布的笔记 |
| https://edith.xiaohongshu.com/api/sns/web/share/code | POST | JSON | JSON | 生成笔记分享口令 |
| https://edith.xiaohongshu.com/api/sns/web/v1/feed | POST | JSON | JSON | 获取笔记详情（正文、图片、视频、互动数） |
| https://edith.xiaohongshu.com/api/sns/web/v1/get_liked_num | GET | Query | JSON | 获取笔记点赞数 |
| https://edith.xiaohongshu.com/api/sns/web/v1/note/collect | POST | JSON | JSON | 收藏笔记 |
| https://edith.xiaohongshu.com/api/sns/web/v1/note/dislike | POST | JSON | JSON | 标记笔记「不感兴趣」 |
| https://edith.xiaohongshu.com/api/sns/web/v1/note/like | POST | JSON | JSON | 点赞笔记 |
| https://edith.xiaohongshu.com/api/sns/web/v1/note/like/page | GET | Query | JSON | 分页获取点赞记录 |
| https://edith.xiaohongshu.com/api/sns/web/v1/note/metrics_report | POST | JSON | JSON | 上报笔记曝光/阅读时长指标 |
| https://edith.xiaohongshu.com/api/sns/web/v1/note/move | POST | JSON | JSON | 将笔记移动到其他看板 |
| https://edith.xiaohongshu.com/api/sns/web/v1/note/uncollect | POST | JSON | JSON | 取消收藏笔记 |
| https://edith.xiaohongshu.com/api/sns/web/v2/note/browsing_history/clear | POST | JSON | JSON | 清空浏览历史 |
| https://edith.xiaohongshu.com/api/sns/web/v2/note/browsing_history/count | GET | Query | JSON | 获取浏览历史条数 |
| https://edith.xiaohongshu.com/api/sns/web/v2/note/browsing_history/delete | POST | JSON | JSON | 删除指定浏览历史记录 |
| https://edith.xiaohongshu.com/api/sns/web/v2/note/browsing_history/delete_except | POST | JSON | JSON | 删除除保留项外的浏览历史 |

## 评论（7 条）

> 发表评论、回复、点赞点踩与父子评论分页

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://edith.xiaohongshu.com/api/sns/web/v1/comment/delete | POST | JSON | JSON | 删除评论 |
| https://edith.xiaohongshu.com/api/sns/web/v1/comment/dislike | POST | JSON | JSON | 点踩评论 |
| https://edith.xiaohongshu.com/api/sns/web/v1/comment/like | POST | JSON | JSON | 点赞评论 |
| https://edith.xiaohongshu.com/api/sns/web/v1/comment/post | POST | JSON | JSON | 发表评论/回复 |
| https://edith.xiaohongshu.com/api/sns/web/v2/comment/pag | GET | Query | JSON | 分页获取评论（旧版别名） |
| https://edith.xiaohongshu.com/api/sns/web/v2/comment/page | GET | Query | JSON | 分页获取笔记评论列表 |
| https://edith.xiaohongshu.com/api/sns/web/v2/comment/sub/page | GET | Query | JSON | 分页获取评论下的子评论 |

## 搜索（12 条）

> 关键词搜索、用户搜索、推荐词、热榜、筛选、OneBox 与搜索历史同步

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://edith.xiaohongshu.com/api/sns/web/search/history/sync | POST | JSON | JSON | 同步云端搜索历史 |
| https://edith.xiaohongshu.com/api/sns/web/search/send/ai | POST | JSON | JSON | 向 AI 搜索发起提问 |
| https://edith.xiaohongshu.com/api/sns/web/search/signal | POST | JSON | JSON | 上报搜索行为信号 |
| https://edith.xiaohongshu.com/api/sns/web/v1/search/filter | GET | Query | JSON | 获取搜索结果筛选项（排序/类型/时间） |
| https://edith.xiaohongshu.com/api/sns/web/v1/search/notes | POST | JSON | JSON | 搜索笔记关键词结果 |
| https://edith.xiaohongshu.com/api/sns/web/v1/search/onebox | POST | JSON | JSON | 获取搜索 OneBox 聚合卡片 |
| https://edith.xiaohongshu.com/api/sns/web/v1/search/pc/websearch | POST | JSON | JSON | PC 端站内网页搜索 |
| https://edith.xiaohongshu.com/api/sns/web/v1/search/recommend | GET | Query | JSON | 获取搜索框推荐词 |
| https://edith.xiaohongshu.com/api/sns/web/v1/search/trending/query | GET | Query | JSON | 获取搜索热榜（大家都在搜） |
| https://edith.xiaohongshu.com/api/sns/web/v1/search/usersearch | POST | JSON | JSON | 搜索用户 |
| https://edith.xiaohongshu.com/api/sns/web/v1/worldcup/search/onebox | POST | JSON | JSON | 获取世界杯赛事搜索 OneBox |
| https://edith.xiaohongshu.com/api/sns/web/v2/search/notes | POST | JSON | JSON | 搜索笔记关键词结果（v2） |

## AI 问答（DQA）（9 条）

> 深度问答（Deep Question Answer）的发起、流式生成、引用来源与历史会话

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://edith.xiaohongshu.com/api/sns/web/v1/celestial/lt | POST | JSON | JSON | AI「Celestial」长任务会话 |
| https://edith.xiaohongshu.com/api/sns/web/v1/dqa/complex/source | POST | JSON | JSON | 获取复杂问答的引用来源列表 |
| https://edith.xiaohongshu.com/api/sns/web/v1/dqa/history/detail | GET | Query | JSON | 获取历史问答会话详情 |
| https://edith.xiaohongshu.com/api/sns/web/v1/dqa/history/list | GET | Query | JSON | 获取历史问答会话列表 |
| https://edith.xiaohongshu.com/api/sns/web/v1/dqa/instant | POST | JSON | JSON | 发起即时深度问答（Deep Question Answer） |
| https://edith.xiaohongshu.com/api/sns/web/v1/dqa/recommend/query | GET | Query | JSON | 获取问答推荐问题 |
| https://edith.xiaohongshu.com/api/sns/web/v1/search/dqa/detail | GET | Query | JSON | 获取搜索结果中的问答详情 |
| https://edith.xiaohongshu.com/api/sns/web/v1/search/dqa/onebox/detail | GET | Query | JSON | 获取问答 OneBox 详情 |
| https://edith.xiaohongshu.com/api/sns/web/v1/search/dqa/stream/tokens | POST | JSON | JSON | 拉取问答流式生成 token |

## AI 智能体（AgentSpark）（12 条）

> 智能体会话、消息回复、澄清问题、POI/交通/行程规划与文件读取

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://edith.xiaohongshu.com/api/sns/agentspark/poi/detail | POST | JSON | JSON | 获取智能体地图 POI 详情 |
| https://edith.xiaohongshu.com/api/sns/agentspark/web/celestial/ai/messages | GET | Query | JSON | 获取智能体 AI 对话消息流 |
| https://edith.xiaohongshu.com/api/sns/agentspark/web/conversation/detail | GET | Query | JSON | 获取智能体会话详情 |
| https://edith.xiaohongshu.com/api/sns/agentspark/web/file_path/list_all | POST | JSON | JSON | 列出会话生成的全部文件路径 |
| https://edith.xiaohongshu.com/api/sns/agentspark/web/file_read | POST | JSON | JSON | 读取会话生成的文件内容 |
| https://edith.xiaohongshu.com/api/sns/agentspark/web/message/reply | GET | Query | JSON | 获取智能体消息回复内容 |
| https://edith.xiaohongshu.com/api/sns/agentspark/web/note/detail | POST | JSON | JSON | 获取智能体引用的笔记详情 |
| https://edith.xiaohongshu.com/api/sns/agentspark/web/poi/transport | POST | JSON | JSON | 查询 POI 周边交通信息 |
| https://edith.xiaohongshu.com/api/sns/agentspark/web/question/select | POST | JSON | JSON | 提交智能体澄清问题的选项 |
| https://edith.xiaohongshu.com/api/sns/agentspark/web/share/file_read | POST | JSON | JSON | 通过分享链接读取文件内容 |
| https://edith.xiaohongshu.com/api/sns/agentspark/web/v1/dqa/complex/source | GET | Query | JSON | 获取智能体复杂问答引用来源 |
| https://edith.xiaohongshu.com/api/sns/agentspark/web/v1/travel/route_plan | POST | JSON | JSON | 生成旅行路线规划 |

## 用户与关系（11 条）

> 本人/他人资料、关注取关、用户笔记列表、悬浮名片与亲密好友

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://edith.xiaohongshu.com/api/sns/web/v1/intimacy/intimacy_list | GET | Query | JSON | 获取亲密好友（互关）列表 |
| https://edith.xiaohongshu.com/api/sns/web/v1/intimacy/intimacy_list/search | GET | Query | JSON | 搜索亲密好友 |
| https://edith.xiaohongshu.com/api/sns/web/v1/user/follow | POST | JSON | JSON | 关注用户 |
| https://edith.xiaohongshu.com/api/sns/web/v1/user/hover_card | GET | Query | JSON | 获取用户悬浮名片 |
| https://edith.xiaohongshu.com/api/sns/web/v1/user/info | POST | JSON | JSON | 批量获取用户信息 |
| https://edith.xiaohongshu.com/api/sns/web/v1/user/otherinfo | GET | Query | JSON | 获取他人主页信息 |
| https://edith.xiaohongshu.com/api/sns/web/v1/user/selfinfo | GET | Query | JSON | 获取本人账号信息 |
| https://edith.xiaohongshu.com/api/sns/web/v1/user/unfollow | POST | JSON | JSON | 取消关注 |
| https://edith.xiaohongshu.com/api/sns/web/v1/user_posted | GET | Query | JSON | 获取用户发布的笔记列表 |
| https://edith.xiaohongshu.com/api/sns/web/v2/user/me | GET | Query | JSON | 获取当前登录用户资料 |
| https://edith.xiaohongshu.com/api/sns/web/v2/user_posted | GET | Query | JSON | 获取用户发布的笔记列表（v2） |

## 看板与收藏（6 条）

> 看板（专辑）的创建、详情、内嵌笔记与收藏笔记分页

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://edith.xiaohongshu.com/api/sns/web/v1/board | POST | JSON | JSON | 创建/更新看板（专辑） |
| https://edith.xiaohongshu.com/api/sns/web/v1/board | PUT | JSON | JSON | 创建/更新看板（专辑） |
| https://edith.xiaohongshu.com/api/sns/web/v1/board/note | GET | Query | JSON | 获取看板内的笔记列表 |
| https://edith.xiaohongshu.com/api/sns/web/v1/board/user | GET | Query | JSON | 获取用户创建的看板列表 |
| https://edith.xiaohongshu.com/api/sns/web/v1/board/{boardId} | GET | Query | JSON | 获取指定看板详情 |
| https://edith.xiaohongshu.com/api/sns/web/v2/note/collect/page | GET | Query | JSON | 分页获取收藏的笔记 |

## 消息盒子与通知（10 条）

> 赞和收藏、新增关注、评论与@ 等系统通知的读取、置顶、免打扰与删除

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://edith.xiaohongshu.com/api/sns/v2/message/config | GET | Query | JSON | 获取消息盒子通知配置 |
| https://edith.xiaohongshu.com/api/sns/v2/message/delete | DELETE | Query | JSON | 删除站内消息 |
| https://edith.xiaohongshu.com/api/sns/v2/message/message_box | GET | Query | JSON | 获取消息盒子列表（赞和收藏/新增关注/评论@） |
| https://edith.xiaohongshu.com/api/sns/v6/message/delete_box | GET | Query | JSON | 清空整个消息盒子 |
| https://edith.xiaohongshu.com/api/sns/v6/message/mute | POST | JSON | JSON | 设置消息盒子免打扰 |
| https://edith.xiaohongshu.com/api/sns/v6/message/sticky_top | POST | JSON | JSON | 置顶消息盒子 |
| https://edith.xiaohongshu.com/api/sns/v6/message/web/delete_msg | POST | JSON | JSON | 删除指定站内消息 |
| https://edith.xiaohongshu.com/api/sns/v6/message/web/detect | GET | Query | JSON | 消息通道可用性探测 |
| https://edith.xiaohongshu.com/api/sns/web/unread_count | GET | Query | JSON | 获取未读消息总数 |
| https://edith.xiaohongshu.com/api/sns/web/v1/message/read | POST | JSON | JSON | 标记站内消息已读 |

## IM 私信与会话（Web）（42 条）

> 会话列表、历史消息、已读回执、撤回、在线状态、表情/GIF/语音转文字

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://edith.xiaohongshu.com/api/im/web/chat/get_unread | GET | Query | JSON | 获取指定会话未读数 |
| https://edith.xiaohongshu.com/api/im/web/chats/group | GET | Query | JSON | 获取群会话列表 |
| https://edith.xiaohongshu.com/api/im/web/chats/group/info | GET | Query | JSON | 获取群会话详情 |
| https://edith.xiaohongshu.com/api/im/web/emoji/config | GET | Query | JSON | 获取 IM 表情面板配置 |
| https://edith.xiaohongshu.com/api/im/web/group/query_online_status | GET | Query | JSON | 查询群成员在线状态 |
| https://edith.xiaohongshu.com/api/im/web/messages/history | GET | Query | JSON | 拉取会话历史消息 |
| https://edith.xiaohongshu.com/api/im/web/messages/revoke | POST | JSON | JSON | 撤回已发送消息 |
| https://edith.xiaohongshu.com/api/im/web/panel/search/gif | GET | Query | JSON | 在表情面板内搜索 GIF |
| https://edith.xiaohongshu.com/api/im/web/private/query_online_status | GET | Query | JSON | 查询单聊对方在线状态 |
| https://edith.xiaohongshu.com/api/im/web/red/group/messages/history | GET | Query | JSON | 拉取红包群历史消息 |
| https://edith.xiaohongshu.com/api/im/web/red/group/personal_page_group_show_info | GET | Query | JSON | 获取个人主页展示的红包群信息 |
| https://edith.xiaohongshu.com/api/im/web/search/gif | GET | Query | JSON | 搜索 GIF 动图 |
| https://edith.xiaohongshu.com/api/im/web/short_link/send_message | POST | JSON | JSON | 通过短链向用户发送消息 |
| https://edith.xiaohongshu.com/api/im/web/smiles | GET | Query | JSON | 获取表情包列表 |
| https://edith.xiaohongshu.com/api/im/web/smiles/add/third_party | POST | JSON | JSON | 添加第三方表情包 |
| https://edith.xiaohongshu.com/api/im/web/smiles/hot | GET | Query | JSON | 获取热门表情包 |
| https://edith.xiaohongshu.com/api/im/web/smiles/hot_smiles | GET | Query | JSON | 获取热门表情 |
| https://edith.xiaohongshu.com/api/im/web/speak/create_auth_check | POST | JSON | JSON | 发言创建权限校验 |
| https://edith.xiaohongshu.com/api/im/web/users/filterUser/stranger | POST | JSON | JSON | 过滤出非陌生人用户 |
| https://edith.xiaohongshu.com/api/im/web/users/following/all | GET | Query | JSON | 获取全部关注用户（用于发起会话） |
| https://edith.xiaohongshu.com/api/im/web/v1/chats/record/overview | GET | Query | JSON | 获取聊天记录总览 |
| https://edith.xiaohongshu.com/api/im/web/v1/chats/record/search | GET | Query | JSON | 按关键词搜索聊天记录 |
| https://edith.xiaohongshu.com/api/im/web/v1/chats/remove/group | POST | JSON | JSON | 从会话列表移除群聊 |
| https://edith.xiaohongshu.com/api/im/web/v1/group/alter/public | POST | JSON | JSON | 修改群是否公开 |
| https://edith.xiaohongshu.com/api/im/web/v1/group/personal_page/subject | POST | JSON | JSON | 设置个人主页展示的群话题 |
| https://edith.xiaohongshu.com/api/im/web/v1/group/remove_all_message | POST | JSON | JSON | 清空群内全部消息 |
| https://edith.xiaohongshu.com/api/im/web/v1/group/stick_top/messages | GET | Query | JSON | 获取群置顶消息 |
| https://edith.xiaohongshu.com/api/im/web/v1/plus/config | GET | Query | JSON | 获取 IM Plus 增值功能配置 |
| https://edith.xiaohongshu.com/api/im/web/v1/smiles/add | POST | JSON | JSON | 添加表情包 |
| https://edith.xiaohongshu.com/api/im/web/v1/smiles/delete | POST | JSON | JSON | 删除表情包 |
| https://edith.xiaohongshu.com/api/im/web/v1/smiles/file_id | GET | Query | JSON | 获取表情文件 ID（上传凭证） |
| https://edith.xiaohongshu.com/api/im/web/v1/users/mutual/follow | GET | Query | JSON | 获取互关好友列表 |
| https://edith.xiaohongshu.com/api/im/web/v2/group/members | GET | Query | JSON | 获取群成员列表 |
| https://edith.xiaohongshu.com/api/im/web/v2/messages/ack | POST | JSON | JSON | 发送消息送达回执 |
| https://edith.xiaohongshu.com/api/im/web/v2/messages/location/list | GET | Query | JSON | 获取消息定位（跳转）列表 |
| https://edith.xiaohongshu.com/api/im/web/v2/messages/location/read | POST | JSON | JSON | 标记消息定位已读 |
| https://edith.xiaohongshu.com/api/im/web/v2/messages/offline | GET | Query | JSON | 拉取离线未同步消息 |
| https://edith.xiaohongshu.com/api/im/web/v2/messages/read | POST | JSON | JSON | 标记会话消息已读 |
| https://edith.xiaohongshu.com/api/im/web/v2/messages/total_unread | GET | Query | JSON | 获取全部会话总未读数 |
| https://edith.xiaohongshu.com/api/im/web/v3/chats | GET | Query | JSON | 获取会话列表（v3） |
| https://edith.xiaohongshu.com/api/im/web/v3/chats/info | GET | Query | JSON | 获取会话详情（v3） |
| https://edith.xiaohongshu.com/api/im/web/voice_convert | POST | JSON | JSON | 语音消息转文字 |

## IM 群组管理（19 条）

> 建群、群资料与设置、成员、入群审批与群广场

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://edith.xiaohongshu.com/api/im/chats/group/remark | POST | JSON | JSON | 修改群聊备注名 |
| https://edith.xiaohongshu.com/api/im/group/management | POST | JSON | JSON | 群管理操作（禁言/移除/转让等） |
| https://edith.xiaohongshu.com/api/im/v1/group/create | POST | JSON | JSON | 创建群聊 |
| https://edith.xiaohongshu.com/api/im/v1/group/create/config | GET | Query | JSON | 获取建群可选配置 |
| https://edith.xiaohongshu.com/api/im/v1/group/get_approve_join_group_infos | POST | JSON | JSON | 获取入群审批申请列表 |
| https://edith.xiaohongshu.com/api/im/v1/group/get_group_basic_info | GET | Query | JSON | 获取群基础信息 |
| https://edith.xiaohongshu.com/api/im/v1/group/message/sender | GET | Query | JSON | 获取群内消息发送者列表 |
| https://edith.xiaohongshu.com/api/im/v1/group/notification/settings | POST | JSON | JSON | 设置群消息通知偏好 |
| https://edith.xiaohongshu.com/api/im/v1/group/settings | GET | Query | JSON | 获取群设置 |
| https://edith.xiaohongshu.com/api/im/v1/group/square/config | GET | Query | JSON | 获取群广场配置 |
| https://edith.xiaohongshu.com/api/im/v1/group/square/list | GET | Query | JSON | 获取群广场推荐群列表 |
| https://edith.xiaohongshu.com/api/im/v1/group/square/search | GET | Query | JSON | 搜索群广场中的群 |
| https://edith.xiaohongshu.com/api/im/v1/group/update_join_group_approval | POST | JSON | JSON | 更新入群审批开关与规则 |
| https://edith.xiaohongshu.com/api/im/v1/group/user/following | GET | Query | JSON | 获取可邀请的关注人列表 |
| https://edith.xiaohongshu.com/api/im/v2/group/create | POST | JSON | JSON | 创建群聊（v2） |
| https://edith.xiaohongshu.com/api/im/v2/group/get_group_jump_page_info | GET | Query | JSON | 获取群跳转页信息（v2） |
| https://edith.xiaohongshu.com/api/im/v2/group/user | GET | Query | JSON | 获取群内用户信息 |
| https://edith.xiaohongshu.com/api/im/v2/group_mini_info | GET | Query | JSON | 获取群简要信息 |
| https://edith.xiaohongshu.com/api/im/v2/update_group_info | POST | JSON | JSON | 更新群资料（名称/头像/简介） |

## IM 红包群与红包表情（14 条）

> 红包群的二维码、邀请码、加群退群、公告审批与红包表情资源

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://edith.xiaohongshu.com/api/im/red/group/add | POST | JSON | JSON | 向红包群添加成员 |
| https://edith.xiaohongshu.com/api/im/red/group/approveJoinGroup | POST | JSON | JSON | 审批单条入群申请 |
| https://edith.xiaohongshu.com/api/im/red/group/approveJoinGroupAll | POST | JSON | JSON | 批量通过入群申请 |
| https://edith.xiaohongshu.com/api/im/red/group/approveJoinGroupHistory | POST | JSON | JSON | 获取入群审批历史记录 |
| https://edith.xiaohongshu.com/api/im/red/group/gen_invite_emoji_code | GET | Query | JSON | 生成表情邀请码 |
| https://edith.xiaohongshu.com/api/im/red/group/get_group_jump_page_info | POST | JSON | JSON | 获取红包群跳转页信息 |
| https://edith.xiaohongshu.com/api/im/red/group/get_invite_emoji_code_info | POST | JSON | JSON | 解析表情邀请码信息 |
| https://edith.xiaohongshu.com/api/im/red/group/join_group | POST | JSON | JSON | 通过邀请码加入红包群 |
| https://edith.xiaohongshu.com/api/im/red/group/qr_code | GET | Query | JSON | 获取红包群二维码 |
| https://edith.xiaohongshu.com/api/im/red/group/remove | POST | JSON | JSON | 退出红包群或移除成员 |
| https://edith.xiaohongshu.com/api/im/red/group/updateAnnouncement | POST | JSON | JSON | 更新红包群公告 |
| https://edith.xiaohongshu.com/api/im/red/group/username | POST | JSON | JSON | 设置红包群内昵称 |
| https://edith.xiaohongshu.com/api/im/redmoji/detail | GET | Query | JSON | 获取红包表情详情 |
| https://edith.xiaohongshu.com/api/im/redmoji/version | GET | Query | JSON | 获取红包表情资源版本 |

## IM 其它（中台与关系）（9 条）

> IM PaaS 网关、黑名单/禁言名单、置顶消息与最近聊天

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://edith.xiaohongshu.com/api/im/messages/updateTop | POST | JSON | JSON | 置顶/取消置顶会话消息 |
| https://edith.xiaohongshu.com/api/im/v1/messages/stranger/read | POST | JSON | JSON | 标记陌生人消息已读 |
| https://edith.xiaohongshu.com/api/im/v1/users/blocked-users/ | DELETE | Query | JSON | 黑名单管理（POST 拉黑 / DELETE 解除） |
| https://edith.xiaohongshu.com/api/im/v1/users/blocked-users/ | POST | JSON | JSON | 黑名单管理（POST 拉黑 / DELETE 解除） |
| https://edith.xiaohongshu.com/api/im/v1/users/muted-users/ | DELETE | Query | JSON | 禁言名单管理（POST 禁言 / DELETE 解除） |
| https://edith.xiaohongshu.com/api/im/v1/users/muted-users/ | POST | JSON | JSON | 禁言名单管理（POST 禁言 / DELETE 解除） |
| https://edith.xiaohongshu.com/api/impaas | GET | Query | JSON | IM PaaS 即时通讯中台网关入口 |
| https://edith.xiaohongshu.com/api/sns/v1/im/delete_recent_chat | POST | JSON | JSON | 删除最近聊天会话 |
| https://edith.xiaohongshu.com/api/sns/v1/im/web/get_recent_chats | GET | Query | JSON | 获取最近聊天会话列表 |

## 直播（32 条）

> 直播广场、进出房间、弹幕互动、礼物打赏、钱包充值、付费直播与直播预约

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://edith.xiaohongshu.com/api/sns/red/live/app/gift/v1/worldcup/web/host_team_change | POST | JSON | JSON | 切换主播支持队伍 |
| https://edith.xiaohongshu.com/api/sns/red/live/app/gift/v1/worldcup/web/host_team_info | GET | Query | JSON | 获取主播支持队伍信息 |
| https://edith.xiaohongshu.com/api/sns/red/live/v1/web/room_user/viewer_heart | POST | JSON | JSON | 观众发送「比心」互动 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/comment/v1/user_violation | GET | Query | JSON | 查询用户直播违规（禁言）状态 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/feed/category | GET | Query | JSON | 获取直播频道分类 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/feed/v1/squarefeed | GET | Query | JSON | 获取直播广场信息流 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/gift/v1/batch_send_gift | POST | JSON | JSON | 批量发送礼物 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/gift/v1/batch_send_gift_finish | POST | JSON | JSON | 批量送礼完成回执 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/gift/v1/gift_panel | GET | Query | JSON | 获取礼物面板配置 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/gift/v1/give/gift | POST | JSON | JSON | 赠送单个礼物 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/gift/v1/give/gift/finish | POST | JSON | JSON | 赠送礼物完成回执 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/group_live/entrance | GET | Query | JSON | 获取群直播入口信息 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/paid_live/preview_report | POST | JSON | JSON | 上报付费直播试看行为 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/paid_live/purchase | POST | JSON | JSON | 购买付费直播观看权 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/paid_live/query_price_info | POST | JSON | JSON | 查询付费直播价格信息 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/pay/v1/charge_panel | GET | Query | JSON | 获取充值面板 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/pay/v1/prepare_charge_transaction | POST | JSON | JSON | 创建充值交易订单 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/pay/v1/wallet/coins/balance | GET | Query | JSON | 查询钱包虚拟币余额 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/resource_by_id | GET | Query | JSON | 按 ID 获取直播资源 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/room/share | POST | JSON | JSON | 生成直播间分享链接 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/share | GET | Query | JSON | 获取直播间分享信息 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/v1/center/room/join/room | POST | JSON | JSON | 加入直播间 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/v1/line/mic_relation | POST | JSON | JSON | 查询连麦关系状态 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/v1/room/aggregate_business_info | GET | Query | JSON | 获取直播间聚合业务信息 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/v1/room/current_room_info | GET | Query | JSON | 获取当前直播间信息 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/v1/room/join_business_base_info | GET | Query | JSON | 获取进入直播的基础业务信息 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/v1/room/join_comment_info | GET | Query | JSON | 获取直播间评论入口信息 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/v1/trailer/cancel_subscribe | POST | JSON | JSON | 取消直播预约 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/v1/trailer/subscribe | POST | JSON | JSON | 预约直播开播提醒 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/{sourceId}/user_card | GET | Query | JSON | 获取直播间内用户名片 |
| https://edith.xiaohongshu.com/api/sns/v1/live/web/activity/world/cup26/incidents | GET | Query | JSON | 获取 2026 世界杯直播互动事件流 |
| https://edith.xiaohongshu.com/api/sns/v1/live/web/interaction/send_comment | POST | JSON | JSON | 发送直播弹幕评论 |

## 世界杯活动（16 条）

> 赛程日历、积分榜、淘汰赛对阵、球员数据、直播预约与活动主视觉

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://edith.xiaohongshu.com/api/sns/red/live/web/v1/activity_platform/world_cup_calendar | GET | Query | JSON | 获取活动平台世界杯日历 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/v1/activity_platform/world_cup_live_info | POST | JSON | JSON | 获取世界杯赛事直播信息 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/v1/activity_platform/world_cup_match_lineup | POST | JSON | JSON | 获取世界杯比赛首发阵容 |
| https://edith.xiaohongshu.com/api/sns/red/live/web/v1/activity_platform/world_cup_query_player_single_base | POST | JSON | JSON | 查询球员单场基础数据 |
| https://edith.xiaohongshu.com/api/sns/web/worldcup | GET | Query | JSON | 获取世界杯活动主页数据 |
| https://edith.xiaohongshu.com/api/sns/web/worldcup/access_check | POST | JSON | JSON | 世界杯活动准入资格校验 |
| https://edith.xiaohongshu.com/api/sns/web/worldcup/calendar_info | GET | Query | JSON | 获取世界杯赛程日历 |
| https://edith.xiaohongshu.com/api/sns/web/worldcup/dots/display_period | GET | Query | JSON | 获取活动小红点展示周期 |
| https://edith.xiaohongshu.com/api/sns/web/worldcup/live/cancel | POST | JSON | JSON | 取消赛事直播预约 |
| https://edith.xiaohongshu.com/api/sns/web/worldcup/live/subscribe | POST | JSON | JSON | 预约世界杯赛事直播 |
| https://edith.xiaohongshu.com/api/sns/web/worldcup/live_bar | GET | Query | JSON | 获取直播悬浮条数据 |
| https://edith.xiaohongshu.com/api/sns/web/worldcup/main_kv | GET | Query | JSON | 获取活动主视觉 KV 配置 |
| https://edith.xiaohongshu.com/api/sns/web/worldcup/nockout_bracket | POST | JSON | JSON | 获取淘汰赛对阵图 |
| https://edith.xiaohongshu.com/api/sns/web/worldcup/note/seo | POST | JSON | JSON | 获取世界杯笔记 SEO 元信息 |
| https://edith.xiaohongshu.com/api/sns/web/worldcup/rank/player_stats | GET | Query | JSON | 获取球员数据排行榜 |
| https://edith.xiaohongshu.com/api/sns/web/worldcup/standings | GET | Query | JSON | 获取小组赛积分榜 |

## 安全与验证码（8 条）

> 滑块/图形验证码配置与行为上报、风控脚本采集、登录安全码

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://edith.xiaohongshu.com/api/captcha/v2/web/getconfig | GET | Query | JSON | 获取图形/滑块验证码初始化配置 |
| https://edith.xiaohongshu.com/api/captcha/v2/web/log | POST | JSON | JSON | 上报验证码交互行为日志 |
| https://edith.xiaohongshu.com/api/redcaptcha | GET | Query | JSON | 红书验证码服务根入口 |
| https://edith.xiaohongshu.com/api/redcaptcha/v2/getconfig | GET | Query | JSON | 获取新版验证码渲染配置 |
| https://edith.xiaohongshu.com/api/redcaptcha/v2/web/log | POST | JSON | JSON | 上报新版验证码行为日志 |
| https://edith.xiaohongshu.com/api/sec/v1/sbtsource | GET | Query | JSON | 获取安全埋点（sbt）脚本源 |
| https://edith.xiaohongshu.com/api/sec/v1/scripting | POST | JSON | JSON | 上报安全脚本采集的风控数据 |
| https://edith.xiaohongshu.com/api/sns/web/v2/login/security_code | POST | JSON | JSON | 获取登录二次校验安全码 |

## 登录与认证（11 条）

> 短信验证码登录、扫码登录、第三方登录、会话激活与退出

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://edith.xiaohongshu.com/api/qrcode/userinfo | GET | Query | JSON | 扫码后获取待授权用户信息 |
| https://edith.xiaohongshu.com/api/sns/web/v1/login/activate | POST | JSON | JSON | 激活并校验登录会话（风控拦截跳转此接口） |
| https://edith.xiaohongshu.com/api/sns/web/v1/login/check_code | GET | Query | JSON | 校验短信验证码是否正确 |
| https://edith.xiaohongshu.com/api/sns/web/v1/login/code | POST | JSON | JSON | 使用手机验证码登录 |
| https://edith.xiaohongshu.com/api/sns/web/v1/login/logout | GET | Query | JSON | 退出登录 |
| https://edith.xiaohongshu.com/api/sns/web/v1/login/qrcode/create | POST | JSON | JSON | 创建扫码登录二维码 |
| https://edith.xiaohongshu.com/api/sns/web/v1/login/qrcode/status | GET | Query | JSON | 轮询二维码扫码状态 |
| https://edith.xiaohongshu.com/api/sns/web/v1/login/send_code | GET | Query | JSON | 发送短信验证码 |
| https://edith.xiaohongshu.com/api/sns/web/v1/login/social | POST | JSON | JSON | 第三方社交账号（微信/QQ 等）授权登录 |
| https://edith.xiaohongshu.com/api/sns/web/v2/login/code | POST | JSON | JSON | 使用手机验证码登录（v2） |
| https://edith.xiaohongshu.com/api/sns/web/v2/login/send_code | GET | Query | JSON | 发送短信验证码（v2） |

## 配置、上报与工具（18 条）

> 全局/系统配置、灰度实验、NPS 与举报、上传凭证、性能与网络质量上报

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://edith.xiaohongshu.com/api/p/pj | POST | JSON | JSON | 前端性能与异常探针数据上报 |
| https://edith.xiaohongshu.com/api/sns/ | GET | Query | JSON | SNS API 根路径（占位） |
| https://edith.xiaohongshu.com/api/sns/web | GET | Query | JSON | Web API 根路径（健康检查/占位） |
| https://edith.xiaohongshu.com/api/sns/web/ares/config | POST | JSON | JSON | 获取 Ares 实验/灰度分流配置 |
| https://edith.xiaohongshu.com/api/sns/web/ares/submit | POST | JSON | JSON | 提交 Ares 用户反馈问卷 |
| https://edith.xiaohongshu.com/api/sns/web/global/config | GET | Query | JSON | 获取站点全局配置（功能开关、文案） |
| https://edith.xiaohongshu.com/api/sns/web/nio/feed | POST | JSON | JSON | 上报 NIO 网络质量探测数据 |
| https://edith.xiaohongshu.com/api/sns/web/nio/init | POST | JSON | JSON | 初始化 NIO 网络性能监控 |
| https://edith.xiaohongshu.com/api/sns/web/racing_report | POST | JSON | JSON | 上报接口竞态/耗时数据 |
| https://edith.xiaohongshu.com/api/sns/web/report/list | POST | JSON | JSON | 获取举报理由选项列表 |
| https://edith.xiaohongshu.com/api/sns/web/report/submit | POST | JSON | JSON | 提交举报 |
| https://edith.xiaohongshu.com/api/sns/web/upload/permit | GET | Query | JSON | 申请文件上传凭证（OSS 直传） |
| https://edith.xiaohongshu.com/api/sns/web/v1/config | POST | JSON | JSON | 获取客户端运行配置 |
| https://edith.xiaohongshu.com/api/sns/web/v1/nps | POST | JSON | JSON | 提交 NPS 满意度评分 |
| https://edith.xiaohongshu.com/api/sns/web/v1/resource_load | GET | Query | JSON | 获取静态资源加载策略 |
| https://edith.xiaohongshu.com/api/sns/web/v1/system/config | GET | Query | JSON | 获取系统级配置 |
| https://edith.xiaohongshu.com/api/sns/web/v2/widgets | POST | JSON | JSON | 获取页面挂件（Widget）配置 |
| https://edith.xiaohongshu.com/api/store/jpd/main | GET | Query | JSON | 获取 JPD 商城主页数据 |

---

## 附：小红书所属域名与子域名清单

### A. 承载接口与业务页面

| 域名 | 角色 |
| :--- | :--- |
| `edith.xiaohongshu.com` | 主站 Web API 网关，本文档全部 `/api/**` 接口的宿主 |
| `edith.beta.xiaohongshu.com` | 预发环境 API 网关 |
| `edith.sit.xiaohongshu.com` | 集成测试环境 API 网关 |
| `www.xiaohongshu.com` | 主站页面（/explore、/user/profile、/worldcup 等）与登录风控跳转页 |
| `www.beta.xiaohongshu.com` | 预发主站 |
| `www.sit.xiaohongshu.com` | 测试主站 |
| `s.xiaohongshu.com` | 分享短链跳转服务 |
| `so.xiaohongshu.com` | 搜索落地页与 SEO 聚合页 |
| `agree.xiaohongshu.com` | 用户协议、隐私政策与合规声明页 |
| `creator.xiaohongshu.com` | 创作者服务平台（独立站点，接口不在本文档范围） |
| `pgy.xiaohongshu.com` | 蒲公英博主合作平台（独立站点） |
| `zhaoshang.xiaohongshu.com` | 电商招商平台（独立站点） |
| `pro.xiaohongshu.com` | 专业号 / 企业号平台（独立站点） |
| `redlive.xiaohongshu.com` | 直播业务域名 |
| `live-room.xiaohongshu.com` | 直播间页面与推拉流入口 |
| `pages.xiaohongshu.com` | 活动与专题静态页 |
| `e.xiaohongshu.com` | 营销投放短链 |
| `c.xiaohongshu.com` | 内容分享短链 |
| `t.xiaohongshu.com` | t2.xiaohongshu.com / t2-test.xiaohongshu.com：测试与灰度入口 |
| `lng.xiaohongshu.com` | 地理位置与逆地址解析服务 |
| `fse.xiaohongshu.com` | 前端安全引擎（风控特征计算） |
| `as.xiaohongshu.com` | 安全 SDK 域名，`/api/sec/v1/shield/webprofile` 采集浏览器指纹 |
| `webapi.rednote.com` | 海外版 REDNOTE Web API（`www.rednote.com` 为海外主站） |
| `ci.xiaohongshu.com` | 图片处理（裁剪/压缩）服务 |
| `dc.xhscdn.com` | 下载与内容分发服务 |
| `job.xiaohongshu.com` | 招聘官网（独立站点） |
| `oa.xiaohongshu.com` | 内部办公系统（非公开） |

### B. 资源与 CDN 存储（无业务接口）

| 域名 | 角色 |
| :--- | :--- |
| `picasso-static.xiaohongshu.com` | 前端静态资源主 CDN（JS/CSS/图标，出现 78 次，为引用最高域名） |
| `fe-static.xhscdn.com` | 前端静态资源备用 CDN |
| `fe-platform.xhscdn.com` | 平台侧前端资源（活动素材、图片模板，61 次） |
| `fe-video-qc.xhscdn.com` | 视频转码与播放资源（36 次） |
| `sns-img-qc / -hw / -bd / -qn.xhscdn.com` | 笔记图片存储（七牛 / 华为云 / 百度云 / 七牛南京多机房） |
| `sns-video-qc / -hw / -bd / -qn.xhscdn.com` | 笔记视频存储（多机房） |
| `sns-avatar-qc.xhscdn.com` | 用户头像存储 |
| `sns-webpic-qc.xhscdn.com` | Web 端图片缩略图 |
| `growth-img.xhscdn.com` | 增长与运营活动图片 |
| `xhs-doc.xhscdn.com` | 文档与富文本附件存储（`xhs-doc-sit` 为测试环境） |
| `cdn.xiaohongshu.com` | 通用 CDN 入口 |
| `apppush-sh5.xiaohongshu.com / apppush-rws.xiaohongshu.com` | 消息推送与长连接（WebSocket）服务 |
| `spltest.xiaohongshu.com` | 实验分流测试域名 |

### C. 监控、埋点与内部环境（已排除）

| 域名 | 角色 |
| :--- | :--- |
| `apm-fe.xiaohongshu.com` | 前端性能监控，`/api/data` 上报 PV/UV、耗时与错误（已排除） |
| `apm-track.xiaohongshu.com` | 埋点事件上报（`apm-track-test` 为测试） |
| `fesentry.xiaohongshu.com` | 前端异常采集（Sentry） |
| `spider-tracker.xiaohongshu.com` | 反爬行为追踪 |
| `logan.devops.xiaohongshu.com` | 日志采集服务 |
| `npm.devops.xiaohongshu.com` | 内部 npm 私有源 |
| `code.devops.xiaohongshu.com` | 内部代码托管 |
| `apihub-v2.devops.xiaohongshu.com` | 内部接口文档平台 |
| `dragon.devops.xiaohongshu.com` | 内部发布平台 |
| `fse.devops.xiaohongshu.com` | 内部前端安全服务 |
| `serverless.int.sit.xiaohongshu.com` | 内部 Serverless 测试环境 |
| `redrive.sit.xiaohongshu.com` | 网盘 / 文件服务测试环境 |
| `local / test / sit.xiaohongshu.com` | 本地与测试环境域名 |

---

## 二、统计汇总

| 分类 | 接口数 | 分类 | 接口数 |
| :--- | :--- | :--- | :--- |
| 首页与信息流 | 9 | 笔记与互动 | 18 |
| 评论 | 7 | 搜索 | 12 |
| AI 问答（DQA） | 9 | AI 智能体（AgentSpark） | 12 |
| 用户与关系 | 11 | 看板与收藏 | 6 |
| 消息盒子与通知 | 10 | IM 私信与会话（Web） | 42 |
| IM 群组管理 | 19 | IM 红包群与红包表情 | 14 |
| IM 其它（中台与关系） | 9 | 直播 | 32 |
| 世界杯活动 | 16 | 安全与验证码 | 8 |
| 登录与认证 | 11 | 配置、上报与工具 | 18 |
| **合计** | **263** |  |  |

**按 API 前缀（唯一路径数）**：`/api/sns/web/**` 106、`/api/im/web/**` 42、`/api/sns/red/**` 34（几乎全部为直播 `/live/web/**`）、`/api/im/v1|v2/**` 20、`/api/sns/v1|v2|v6/**` 15、`/api/im/red/**` 14、`/api/sns/agentspark/**` 12、验证码 5、`/api/im` 根级 4、`/api/sec/**` 2、其它 3（`impaas`、`qrcode/userinfo`、`store/jpd/main`、`p/pj`、`sns` 根）。合计 259。

> 说明：小红书 Web 端未开放接口文档，本文档由前端 bundle 静态逆向得到，覆盖 `xhs-pc-web` 工程全部已加载代码路径中的 `/api/**` 字面量。未出现在 bundle 中的服务端内部接口、App 端专属接口（`/api/sns/v1/*` 的 App 版本）不在统计范围内。