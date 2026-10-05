# www.ccw.site API 接口文档

> 站点：共创世界（ccw.site）— Scratch / 游戏 / 动画 / 漫画 / 小说创作社区
> 抓取时间：2026-10-05
> 抓取范围：首页 HTML → 4 个 JS 入口（runtime / styles / vendor / main）→ runtime 中解析出的 **64 个 chunk**（含 `vendor~Creator`、`Gandi`、`WorksDetail`、`TopicCenter` 等），累计 JS 约 22 MB
> 提取方式：sourcemap（`static-map.xiguacity.cn`）**DNS 无法解析，不可用**；改用 webpack 模块级词法分析——定位统一请求封装模块 `n05h`（导出 `.d`=POST、`.b`=GET、`.c`=PATCH），再按 `"".concat("https://host","/path")` 拼接字面量反向回溯最近一次请求调用来判定 host / method
> 已过滤：第三方埋点与统计（umeng、mob.com、qq connect 分享、sogou 站点验证、ga 等）、CDN/静态资源（`static.xiguacity.cn`、`m.ccw.site/community/images/**`、`assets.ccw.site` 图片）、`projects.scratch.mit.edu` 官方站、minilog 等三方库

---

## 一、通用约定

| 项目 | 说明 |
| :--- | :--- |
| 主 API 域 | `https://community-web.ccw.site`（约 200 个接口，占全站 85%+） |
| 认证域 | `https://sso.ccw.site` |
| Gandi 编辑器域 | `https://gandi-main.ccw.site` |
| 扩展市场域 | `https://bfs-web.ccw.site` |
| 云变量域 | `https://cloud-variable.xiguacity.cn` |
| 请求格式 | 绝大多数为 **POST + JSON body**；少量走 query string |
| 返回格式 | **JSON**，统一信封 `{ status, code, msg, body }`，`status===200` 为成功，`body` 为业务数据 |
| 鉴权 | axios 请求拦截器自动注入 `token` header（取自 sessionStorage / cookie）；`withCredentials: true` |
| 超时 | 30s（`timeout: 3e4`），GET 类接口带 10s 内存缓存（axios-cache-log 可开启 debug） |
| 错误处理 | `code !== 200` 统一弹窗；特殊码 `10824001` 提示"请刷新页面" |

**接口格式说明**：本项目几乎全部接口统一为 **POST + JSON**，包括查询类（list / page / detail / search）也是 POST，参数放 body；仅少数标记为 GET。

---

## 二、账号 / 登录 / 认证（sso.ccw.site）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://sso.ccw.site/web/auth/v3/login/by_xigua | POST | JSON | JSON | 西瓜账号密码登录（v3 协议），skipErrorHandler |
| https://sso.ccw.site/web/auth/logout | POST | JSON | JSON | 退出登录（raw response，不解包） |
| https://sso.ccw.site/web/auth/logout_by_session | POST | JSON | JSON | 按会话退出登录 |
| https://sso.ccw.site/web/auth/assistant/captcha/v2/create | POST | JSON | JSON | 创建图形验证码（助手/登录辅助） |
| https://sso.ccw.site/oauth/authorize?state=xxx | GET | Query | JSON | OAuth 授权跳转（state 透传） |
| https://community-web.ccw.site/captcha/v2/create | POST | JSON | JSON | 创建验证码（业务域） |
| https://community-web.ccw.site/captcha/v2/create_by_session | POST | JSON | JSON | 基于会话创建验证码 |
| https://community-web.ccw.site/captcha/verify | POST | JSON | JSON | 验证码校验 |
| https://community-web.ccw.site/auth/wechat/login | POST | JSON | JSON | 微信登录（待验证：方法由上下文推断） |
| https://community-web.ccw.site/thirdparty/xigua/auth | 待验证 | JSON | JSON | 第三方西瓜授权 |
| https://community-web.ccw.site/students/qq/authorize_url | POST | JSON | JSON | 获取 QQ 授权跳转链接 |
| https://community-web.ccw.site/xigua/bind | POST | JSON | JSON | 绑定西瓜账号 |
| https://community-web.ccw.site/xigua/unbind | POST | JSON | JSON | 解绑西瓜账号 |
| https://community-web.ccw.site/students/bind_phone | POST | JSON | JSON | 绑定手机号 |
| https://community-web.ccw.site/password/change | POST | JSON | JSON | 修改密码 |
| https://community-web.ccw.site/fe-dashboard/bind_phone | 待验证 | JSON | JSON | 概览面板绑定手机（待验证） |

---

## 三、用户资料 / 社交关系

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-web.ccw.site/students/profile | POST | JSON | JSON | 获取/更新用户资料 |
| https://community-web.ccw.site/students/stats | POST | JSON | JSON | 用户统计数据（获赞/粉丝/作品数等） |
| https://community-web.ccw.site/students/update_member_achieve | POST | JSON | JSON | 更新会员成就 |
| https://community-web.ccw.site/user-card/detail | POST | JSON | JSON | 用户名片详情 |
| https://community-web.ccw.site/user_label/list | POST | JSON | JSON | 用户标签列表 |
| https://community-web.ccw.site/user/device/save | POST | JSON | JSON | 保存用户设备信息 |
| https://community-web.ccw.site/user_package/status | POST | JSON | JSON | 用户套餐/权益状态 |
| https://community-web.ccw.site/user_package/user_product | POST | JSON | JSON | 用户已购产品列表 |
| https://community-web.ccw.site/self/detail | POST | JSON | JSON | 当前登录用户详情 |
| https://community-web.ccw.site/profile/verify | 待验证 | JSON | JSON | 身份认证（前端路由 `/profile/verify`，接口待验证） |
| https://community-web.ccw.site/v2/student | 待验证 | JSON | JSON | 学生信息 v2（待验证） |
| https://community-web.ccw.site/student/follower/page | POST | JSON | JSON | 粉丝分页列表 |
| https://community-web.ccw.site/student/following/page | POST | JSON | JSON | 关注分页列表 |
| https://community-web.ccw.site/study-community/following/follow | POST | JSON | JSON | 关注用户 |
| https://community-web.ccw.site/study-community/following/batch_follow | POST | JSON | JSON | 批量关注 |
| https://community-web.ccw.site/extensions/following/status | POST | JSON | JSON | 关注状态查询（扩展站） |
| https://community-web.ccw.site/locked_user/detail | POST | JSON | JSON | 被封禁（锁定）用户详情 |
| https://community-web.ccw.site/muted_user/detail | POST | JSON | JSON | 被静音用户详情 |
| https://community-web.ccw.site/es_student/search | POST | JSON | JSON | ES 搜索用户 |
| https://community-web.ccw.site/student/block_record/create | POST | JSON | JSON | 创建拉黑记录 |
| https://community-web.ccw.site/student/block_record/delete | POST | JSON | JSON | 删除拉黑记录 |
| https://community-web.ccw.site/student/block_record/detail | POST | JSON | JSON | 拉黑记录详情 |
| https://community-web.ccw.site/student/block_record/list | POST | JSON | JSON | 拉黑记录列表 |
| https://community-web.ccw.site/student/block_record/status | POST | JSON | JSON | 拉黑状态查询 |
| https://community-web.ccw.site/approval/list | POST | JSON | JSON | 审批列表（认证/申诉） |
| https://community-web.ccw.site/approval/update | POST | JSON | JSON | 更新审批状态 |
| https://community-web.ccw.site/reputation_score_log/page | POST | JSON | JSON | 声望积分变动日志 |
| https://community-web.ccw.site/reputation/rules | 待验证 | JSON | JSON | 声望规则（前端路由 `/reputation/rules`，接口待验证） |

---

## 四、作品（Creation）核心

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-web.ccw.site/creation/page | POST | JSON | JSON | 作品列表分页（query 带 perPage/page） |
| https://community-web.ccw.site/creation/detail | POST | JSON | JSON | 作品详情 |
| https://community-web.ccw.site/creation/create | POST | JSON | JSON | 创建作品 |
| https://community-web.ccw.site/creation/update | POST | JSON | JSON | 更新作品 |
| https://community-web.ccw.site/creation/delete | POST | JSON | JSON | 删除作品 |
| https://community-web.ccw.site/creation/submit | POST | JSON | JSON | 提交作品（发布） |
| https://community-web.ccw.site/creation/import | POST | JSON | JSON | 导入作品 |
| https://community-web.ccw.site/creation/recommend | POST | JSON | JSON | 推荐作品 |
| https://community-web.ccw.site/creation/hot/page | POST | JSON | JSON | 热门作品分页 |
| https://community-web.ccw.site/creation/excellent/list | POST | JSON | JSON | 优秀作品列表 |
| https://community-web.ccw.site/creation/rank/page | POST | JSON | JSON | 作品排行榜分页 |
| https://community-web.ccw.site/creation/potential/list | POST | JSON | JSON | 潜力作品列表 |
| https://community-web.ccw.site/creation/remixed/page | POST | JSON | JSON | 改编（remix）作品分页 |
| https://community-web.ccw.site/creation/own/page | POST | JSON | JSON | 我的作品分页 |
| https://community-web.ccw.site/creation/page_by_student | POST | JSON | JSON | 按用户筛选作品分页 |
| https://community-web.ccw.site/creation/search/page | POST | JSON | JSON | 作品搜索分页 |
| https://community-web.ccw.site/creation/student/detail | POST | JSON | JSON | 作品作者详情 |
| https://community-web.ccw.site/creation/introduction/detail | POST | JSON | JSON | 作品简介详情 |
| https://community-web.ccw.site/creation/loading/tips | POST | JSON | JSON | 作品加载提示文案 |
| https://community-web.ccw.site/creation/recommend/subject/page | POST | JSON | JSON | 专题推荐作品分页 |
| https://community-web.ccw.site/creation/tag/list | POST | JSON | JSON | 作品标签列表 |
| https://community-web.ccw.site/creation/hash_tag/batch_mark | POST | JSON | JSON | 批量标记作品话题标签 |
| https://community-web.ccw.site/creation_release/page | POST | JSON | JSON | 作品发布记录分页 |
| https://community-web.ccw.site/creation_release/code_profiling/detail | POST | JSON | JSON | 代码性能分析详情 |
| https://community-web.ccw.site/creation_activity_stats/list | POST | JSON | JSON | 作品活动统计列表 |
| https://community-web.ccw.site/creation_attribute/detail | POST | JSON | JSON | 作品属性详情 |
| https://community-web.ccw.site/creation_attribute/upsert | POST | JSON | JSON | 作品属性写入/更新 |
| https://community-web.ccw.site/creation_screenshot/create | POST | JSON | JSON | 创建作品截图记录 |
| https://community-web.ccw.site/creation_screenshot/page | POST | JSON | JSON | 作品截图分页 |
| https://community-web.ccw.site/great_creation/update | POST | JSON | JSON | 更新"好作品"标记 |
| https://community-web.ccw.site/creation_peer/join | POST | JSON | JSON | 加入创作协作（peer） |
| https://community-web.ccw.site/project/v | POST | JSON | JSON | 项目信息 v 版（编辑器加载） |
| https://community-web.ccw.site/scratch_project/detail | POST | JSON | JSON | Scratch 项目详情 |
| https://community-web.ccw.site/version/project | 待验证 | JSON | JSON | 项目版本信息（待验证） |
| https://community-web.ccw.site/creation/like/detail | POST | JSON | JSON | 作品点赞详情 |
| https://community-web.ccw.site/creation_stats/like | POST | JSON | JSON | 上报作品点赞 |
| https://community-web.ccw.site/creation_stats/view | POST | JSON | JSON | 上报作品浏览 |
| https://community-web.ccw.site/creation_stats/support | POST | JSON | JSON | 上报作品点赞/助力 |
| https://community-web.ccw.site/creation_donated_record/ranking_list | POST | JSON | JSON | 作品打赏排行榜 |
| https://community-web.ccw.site/typical_project/page | POST | JSON | JSON | 典型项目分页 |
| https://community-web.ccw.site/typical_project/detail | POST | JSON | JSON | 典型项目详情 |
| https://community-web.ccw.site/typical_project/student/status/update | POST | JSON | JSON | 更新典型项目学习状态 |
| https://community-web.ccw.site/typical_project/student/unread_count | POST | JSON | JSON | 典型项目未读数 |

---

## 五、协作 / 团队

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-web.ccw.site/creation/team_member/list | POST | JSON | JSON | 协作成员列表 |
| https://community-web.ccw.site/creation/team_member/join | POST | JSON | JSON | 加入协作成员 |
| https://community-web.ccw.site/creation/team_member/remove | POST | JSON | JSON | 移除协作成员 |
| https://community-web.ccw.site/creation/team_member/invite_token/fetch | POST | JSON | JSON | 获取邀请 Token |
| https://community-web.ccw.site/creation/team_member/invite_token/refresh | POST | JSON | JSON | 刷新邀请 Token |
| https://community-web.ccw.site/creation/team_member/observer_invite_token/fetch | POST | JSON | JSON | 获取观察者邀请 Token |
| https://community-web.ccw.site/creation/team_member/produce_ticket | POST | JSON | JSON | 生成协作票据 |
| https://community-web.ccw.site/creation/teamwork/disable | POST | JSON | JSON | 关闭协作 |
| https://community-web.ccw.site/creation/transfer_teamwork | POST | JSON | JSON | 转让协作 |
| https://community-web.ccw.site/creation/teamwork_log/page | POST | JSON | JSON | 协作日志分页 |
| https://community-web.ccw.site/historical_team_member/page | POST | JSON | JSON | 历史成员分页 |
| https://community-web.ccw.site/team/list | POST | JSON | JSON | 团队列表 |

---

## 六、评论 / 互动

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-web.ccw.site/comment/reply | POST | JSON | JSON | 发表评论/回复 |
| https://community-web.ccw.site/comment/like_record/create | POST | JSON | JSON | 创建评论点赞 |
| https://community-web.ccw.site/comment/like_record/delete | POST | JSON | JSON | 取消评论点赞 |
| https://community-web.ccw.site/study-community/comment/delete | POST | JSON | JSON | 删除评论 |
| https://community-web.ccw.site/study-community/comment/allow_update_weight | POST | JSON | JSON | 允许更新评论权重 |
| https://community-web.ccw.site/featured_comment/page | POST | JSON | JSON | 精选评论分页 |
| https://community-web.ccw.site/featured_comment/component/list | POST | JSON | JSON | 精选评论组件列表 |
| https://community-web.ccw.site/emoji/all | POST | JSON | JSON | 全部表情 |
| https://community-web.ccw.site/emoji/category/list | POST | JSON | JSON | 表情分类列表 |
| https://community-web.ccw.site/emoji/page | POST | JSON | JSON | 表情分页 |
| https://community-web.ccw.site/report_log/create | POST | JSON | JSON | 举报日志上报 |
| https://community-web.ccw.site/notify_message/show | POST | JSON | JSON | 通知消息展示/弹窗 |

---

## 七、收藏 / 点赞 / 分享

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-web.ccw.site/creation_favorite/page | POST | JSON | JSON | 作品收藏分页 |
| https://community-web.ccw.site/creation_favorite/detail | POST | JSON | JSON | 收藏详情 |
| https://community-web.ccw.site/creation_favorite/favorite | POST | JSON | JSON | 收藏作品 |
| https://community-web.ccw.site/creation_favorite/unfavorite | POST | JSON | JSON | 取消收藏作品 |
| https://community-web.ccw.site/post/favorite/personal | POST | JSON | JSON | 个人帖子收藏分页 |
| https://community-web.ccw.site/hash_tag_favorite/detail | POST | JSON | JSON | 话题收藏详情 |
| https://community-web.ccw.site/hash_tag_favorite/favorite | POST | JSON | JSON | 收藏话题 |
| https://community-web.ccw.site/hash_tag_favorite/unfavorite | POST | JSON | JSON | 取消收藏话题 |
| https://community-web.ccw.site/short_url/create | POST | JSON | JSON | 创建短链接 |
| https://community-web.ccw.site/short_url/decompress | POST | JSON | JSON | 短链接解压还原 |
| https://community-web.ccw.site/api/v1/short_code/encode | POST | JSON | JSON | 短码编码 |
| https://community-web.ccw.site/api/v1/short_code/decode | POST | JSON | JSON | 短码解码 |
| https://community-web.ccw.site/share_code/create | POST | JSON | JSON | 创建分享码 |
| https://community-web.ccw.site/share_code/detail | POST | JSON | JSON | 分享码详情 |
| https://community-web.ccw.site/share_code/apply | POST | JSON | JSON | 应用分享码 |
| https://community-web.ccw.site/share_recommend_creation/page | POST | JSON | JSON | 分享推荐作品分页 |
| https://community-web.ccw.site/creation/like/detail | POST | JSON | JSON | 作品点赞明细 |

---

## 八、话题 / 标签（Hash Tag）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-web.ccw.site/hash_tag/detail | POST | JSON | JSON | 话题详情 |
| https://community-web.ccw.site/hash_tag/update | POST | JSON | JSON | 更新话题 |
| https://community-web.ccw.site/hash_tag/view | POST | JSON | JSON | 话题浏览上报 |
| https://community-web.ccw.site/hash_tag/search/v2 | POST | JSON | JSON | 话题搜索 v2 |
| https://community-web.ccw.site/hash_tag/recommend/list | POST | JSON | JSON | 推荐话题列表 |
| https://community-web.ccw.site/hash_tag/personal | POST | JSON | JSON | 个人话题列表 |
| https://community-web.ccw.site/hash_tag/managed/list | POST | JSON | JSON | 话题管理列表 |
| https://community-web.ccw.site/hash_tag_creation/page | POST | JSON | JSON | 话题作品分页 |
| https://community-web.ccw.site/hash_tag_creation/page_by_hash_tag | POST | JSON | JSON | 按话题查作品分页 |
| https://community-web.ccw.site/hash_tag_creation/mine | POST | JSON | JSON | 我的话题作品 |
| https://community-web.ccw.site/hash_tag_creation/list_relation | POST | JSON | JSON | 话题关联关系列表 |
| https://community-web.ccw.site/hash_tag_creation/audit | POST | JSON | JSON | 话题作品审核 |
| https://community-web.ccw.site/hash_tag_creation/batch_audit | POST | JSON | JSON | 话题作品批量审核 |
| https://community-web.ccw.site/tag/co/new | 待验证 | JSON | JSON | 新版标签（learn.ccw.site 域，待验证） |
| https://community-web.ccw.site/topic/id/{id} | 待验证 | JSON | JSON | 话题中心详情（前端路由拼接，接口待验证） |

---

## 九、通知 / 消息 / 动态流

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-web.ccw.site/notification/page | POST | JSON | JSON | 通知分页列表 |
| https://community-web.ccw.site/notification/stats/v2 | POST | JSON | JSON | 通知统计 v2（未读数） |
| https://community-web.ccw.site/notification/mark_read | POST | JSON | JSON | 标记通知已读 |
| https://community-web.ccw.site/notification/creation_share | POST | JSON | JSON | 作品分享通知 |
| https://community-web.ccw.site/notification/friend_invite | POST | JSON | JSON | 好友邀请通知 |
| https://community-web.ccw.site/notification/user_recommend | POST | JSON | JSON | 用户推荐通知 |
| https://community-web.ccw.site/feed/list | POST | JSON | JSON | 动态流列表 |
| https://community-web.ccw.site/feed/creation_share | POST | JSON | JSON | 作品分享动态 |
| https://community-web.ccw.site/feed/friend_invite | POST | JSON | JSON | 好友邀请动态 |
| https://community-web.ccw.site/feed/user_recommend | POST | JSON | JSON | 用户推荐动态 |
| https://community-web.ccw.site/spread_feed/list | POST | JSON | JSON | 传播动态流列表 |
| https://community-web.ccw.site/spread_feed/list/v2 | POST | JSON | JSON | 传播动态流 v2 |
| https://community-web.ccw.site/study-community/spread_feed/unread/count | POST | JSON | JSON | 传播动态未读数 |
| https://community-web.ccw.site/study-community/feed/delete | POST | JSON | JSON | 删除动态 |
| https://community-web.ccw.site/mobile/notice | 待验证 | JSON | JSON | 移动端通知（MobileNotice 模块，接口待验证） |
| https://community-web.ccw.site/recommend_creator/list | POST | JSON | JSON | 推荐创作者列表 |

---

## 十、帖子 / 文章（Post & Learn）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-web.ccw.site/post/page | POST | JSON | JSON | 帖子分页列表 |
| https://community-web.ccw.site/post/detail | POST | JSON | JSON | 帖子详情 |
| https://community-web.ccw.site/post/search | POST | JSON | JSON | 帖子搜索 |
| https://community-web.ccw.site/post/favorite/personal | POST | JSON | JSON | 个人收藏帖子分页 |

> 说明：`learn.ccw.site`（社区文章/教程）与 `m.ccw.site/concise`、`/article` 在前端仅以**页面链接**形式出现（`https://learn.ccw.site/article/{uuid}`、`https://m.ccw.site/concise/{uuid}`），其数据接口由 `post/*` 系列承载，未发现独立 learn 域 API。

---

## 十一、搜索

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-web.ccw.site/search/all | POST | JSON | JSON | 全站聚合搜索 |
| https://community-web.ccw.site/search/hot_words | POST | JSON | JSON | 搜索热词 |
| https://community-web.ccw.site/es_student/search | POST | JSON | JSON | ES 用户搜索 |

---

## 十二、话题中心 / 活动 / 分类

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-web.ccw.site/subject_area/list | POST | JSON | JSON | 学科领域列表 |
| https://community-web.ccw.site/subject_area/page | POST | JSON | JSON | 学科领域分页 |
| https://community-web.ccw.site/subject_area/page_by_channel | POST | JSON | JSON | 按频道查学科领域分页 |
| https://community-web.ccw.site/activities/detail | POST | JSON | JSON | 活动详情 |
| https://community-web.ccw.site/season/detail | POST | JSON | JSON | 赛季详情 |
| https://community-web.ccw.site/event | POST | JSON | JSON | 事件上报（skipErrorHandler） |
| https://community-web.ccw.site/campaign_resource/detail | POST | JSON | JSON | 活动资源详情 |
| https://community-web.ccw.site/creation_recommend_position/list | POST | JSON | JSON | 推荐位列表 |
| https://community-web.ccw.site/creation_recommend_position/create | POST | JSON | JSON | 创建推荐位 |
| https://community-web.ccw.site/creation_recommend_position/update | POST | JSON | JSON | 更新推荐位 |
| https://community-web.ccw.site/creation_recommend_position/delete | POST | JSON | JSON | 删除推荐位 |
| https://community-web.ccw.site/creation_recommend_position/reorder | POST | JSON | JSON | 推荐位排序 |
| https://community-web.ccw.site/leaflets/item/list | POST | JSON | JSON | 折页/专题条目列表（skipErrorHandler） |
| https://community-web.ccw.site/config/detail | POST | JSON | JSON | 站点配置详情 |
| https://community-web.ccw.site/ccw-main/status | POST | JSON | JSON | 服务状态探测 |

---

## 十三、任务 / 成就 / 打卡 / 声望

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-web.ccw.site/task/mine | POST | JSON | JSON | 我的任务列表 |
| https://community-web.ccw.site/task/award | POST | JSON | JSON | 领取任务奖励（原路径 `//task/award`，双斜杠待验证） |
| https://community-web.ccw.site/check_in_record/detail | POST | JSON | JSON | 打卡记录详情 |
| https://community-web.ccw.site/check_in_record/insert | POST | JSON | JSON | 写入打卡记录 |
| https://gandi-main.ccw.site/achievements | GET | Query | JSON | 成就列表 |
| https://gandi-main.ccw.site/achievement-records | GET | Query | JSON | 成就记录查询 |
| https://gandi-main.ccw.site/achievement-records | POST | JSON | JSON | 写入成就记录 |
| https://gandi-main.ccw.site/achievement-records/process | PATCH | JSON | JSON | 处理成就记录 |
| https://community-web.ccw.site/reputation_score_log/page | POST | JSON | JSON | 声望积分日志 |

---

## 十四、Gandi 排行榜 / 编辑器

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://gandi-main.ccw.site/creation/leaderboards | GET | Query | JSON | 作品排行榜 |
| https://gandi-main.ccw.site/creation/leaderboards/{id} | GET | Query | JSON | 排行榜详情 |
| https://gandi-main.ccw.site/creation/leaderboards/{id} | POST | JSON | JSON | 提交排行榜成绩 |
| https://community-web.ccw.site/gandi/project/{id} | 待验证 | JSON | JSON | Gandi 项目数据（前端路由拼接，接口待验证） |
| https://community-web.ccw.site/workspace/my | 待验证 | JSON | JSON | 我的工作区（前端路由 `/workspace/my`，接口待验证） |
| https://community-web.ccw.site/modeck/guide | 待验证 | JSON | JSON | Modeck 引导（前端路由 `/modeck/guide`，接口待验证） |

---

## 十五、扩展市场（bfs-web.ccw.site）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://bfs-web.ccw.site/extensions | GET | Query | JSON | 扩展列表 |
| https://bfs-web.ccw.site/extensions/ | GET | Query | JSON | 扩展列表（带尾斜杠变体） |
| https://bfs-web.ccw.site/extensions/submit | POST | JSON | JSON | 提交扩展 |
| https://bfs-web.ccw.site/users/{id} | GET | Query | JSON | 扩展作者信息 |
| https://community-web.ccw.site/extensions/creations/{id} | GET | Query | JSON | 扩展关联作品 |
| https://community-web.ccw.site/extensions/users/{id} | GET | Query | JSON | 扩展用户信息 |
| https://community-web.ccw.site/extensions/creation-favorite/detail | POST | JSON | JSON | 扩展作品收藏详情 |
| https://community-web.ccw.site/extensions/creation-like/detail | POST | JSON | JSON | 扩展作品点赞详情 |
| https://community-web.ccw.site/extensions/creation-donated-records/ranking | POST | JSON | JSON | 扩展作品打赏排行 |
| https://community-web.ccw.site/extensions/following/status | POST | JSON | JSON | 扩展关注状态 |

---

## 十六、素材 / 资产 / 云存储（Asset & Pack）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-web.ccw.site/asset/list | POST | JSON | JSON | 素材列表 |
| https://community-web.ccw.site/asset/update | POST | JSON | JSON | 更新素材 |
| https://community-web.ccw.site/asset/move | POST | JSON | JSON | 移动素材 |
| https://community-web.ccw.site/asset/batch/create | POST | JSON | JSON | 批量创建素材 |
| https://community-web.ccw.site/asset/batch/delete | POST | JSON | JSON | 批量删除素材 |
| https://community-web.ccw.site/asset_pack/page | POST | JSON | JSON | 素材包分页（perPage=100, status=PRIVATE） |
| https://community-web.ccw.site/asset_pack/detail | POST | JSON | JSON | 素材包详情（入参 oid） |
| https://community-web.ccw.site/asset_pack/create | POST | JSON | JSON | 创建素材包 |
| https://community-web.ccw.site/asset_pack/update | POST | JSON | JSON | 更新素材包 |
| https://community-web.ccw.site/asset_pack/delete | POST | JSON | JSON | 删除素材包 |
| https://community-web.ccw.site/asset_pack/get_or_create_default_pack | POST | JSON | JSON | 获取或创建默认素材包 |
| https://community-web.ccw.site/cloud_asset/search | POST | JSON | JSON | 云素材搜索（perPage=100&page=1） |
| https://community-web.ccw.site/cloud_asset/batch_upload | POST | JSON | JSON | 云素材批量上传 |
| https://community-web.ccw.site/student/scratch/asset/list | POST | JSON | JSON | 学生 Scratch 素材列表 |
| https://community-web.ccw.site/study-main/scratch/asset/use | POST | JSON | JSON | 使用 Scratch 素材（上报） |
| {assetHost}/internalapi/asset/{assetId}.{fmt}/get/ | GET | Query | 二进制 | 素材读取（assetHost 运行时注入） |
| {assetHost}/internalapi/asset/{assetId} | POST | 二进制 | JSON | 素材写入/更新 |

---

## 十七、云项目 / 云变量（Cloud Database）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-web.ccw.site/study-cloud-database/cloud_project/save | POST | JSON | JSON | 保存云项目 |
| https://community-web.ccw.site/study-cloud-database/cloud_project/detail | POST | JSON | JSON | 云项目详情 |
| https://community-web.ccw.site/study-community/cloud_variable/create | POST | JSON | JSON | 创建云变量 |
| https://cloud-variable.xiguacity.cn/cloud_variable/list | POST | JSON | JSON | 云变量列表 |
| https://cloud-variable.xiguacity.cn/internal/v1/project | POST | JSON | JSON | 云项目数据 v1 内部接口 |
| https://community-web-cloud-database.ccw.site/{projectId} | GET/PATCH | JSON | JSON | 云数据库直连读写（projectHost 运行时注入） |

---

## 十八、交易 / 打赏 / 智能合约

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-web.ccw.site/currency/account/personal | POST | JSON | JSON | 个人虚拟币账户 |
| https://community-web.ccw.site/study-trade/trade/donate | POST | JSON | JSON | 作品打赏下单 |
| https://community-web.ccw.site/study-community/creation_donated_record/detail | POST | JSON | JSON | 打赏记录详情 |
| https://community-web.ccw.site/order/create | POST | JSON | JSON | 创建订单 |
| https://community-web.ccw.site/order/list | POST | JSON | JSON | 订单列表 |
| https://community-web.ccw.site/product/get | POST | JSON | JSON | 商品详情 |
| https://community-web.ccw.site/product/fe/query | POST | JSON | JSON | 商品前端查询 |
| https://community-web.ccw.site/smart_contract/create | POST | JSON | JSON | 创建智能合约 |
| https://community-web.ccw.site/smart_contract/list | POST | JSON | JSON | 智能合约列表 |
| https://community-web.ccw.site/smart_contract/detail | POST | JSON | JSON | 智能合约详情 |
| https://community-web.ccw.site/smart_contract/type/list | POST | JSON | JSON | 合约类型列表 |
| https://community-web.ccw.site/smart_contract/execute | POST | JSON | JSON | 执行智能合约 |
| https://community-web.ccw.site/smart_contract/invest | POST | JSON | JSON | 智能合约投资 |
| https://community-web.ccw.site/smart_contract/account | POST | JSON | JSON | 合约账户信息 |
| https://community-web.ccw.site/smart_contract/earnings/page | POST | JSON | JSON | 合约收益分页 |
| https://community-web.ccw.site/creation/smart_contract/split_rule/create | POST | JSON | JSON | 创建分成规则 |
| https://community-web.ccw.site/creation/smart_contract/split_rule/detail | POST | JSON | JSON | 分成规则详情 |

---

## 十九、收货地址

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-web.ccw.site/address/list | POST | JSON | JSON | 地址列表 |
| https://community-web.ccw.site/address/add | POST | JSON | JSON | 新增地址 |
| https://community-web.ccw.site/address/update | POST | JSON | JSON | 更新地址 |
| https://community-web.ccw.site/address/delete | POST | JSON | JSON | 删除地址 |

---

## 二十、B 站 / QQ 分享接入

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-web.ccw.site/bilibili/token/fetch | POST | JSON | JSON | 获取 B 站 access_token |
| https://community-web.ccw.site/bilibili/token/refresh | POST | JSON | JSON | 刷新 B 站 token |
| https://community-web.ccw.site/bilibili/video/init | POST | JSON | JSON | 初始化投稿（获得 oid） |
| https://community-web.ccw.site/bilibili/video/create | POST | JSON | JSON | 创建投稿 |
| https://community-web.ccw.site/bilibili/video/complete | POST | JSON | JSON | 完成投稿 |
| https://community-web.ccw.site/bilibili/video/type/list | POST | JSON | JSON | 投稿分区类型列表 |
| https://community-web.ccw.site/bilibili/cover/upload | POST | Form | JSON | 上传投稿封面 |

---

## 二十一、第三方能力（AI / 翻译 / 语音 / 短信）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-web.ccw.site/study-main/external/mt/translate | POST | JSON | JSON | 机器翻译 |
| https://community-web.ccw.site/study-main/external/speech/tts | POST | JSON | JSON | 文字转语音 |
| https://community-web.ccw.site/study-main/external/speech/asr | POST | JSON | JSON | 语音转文字 |
| https://community-web.ccw.site/study-main/external/chat_bot/chat | POST | JSON | JSON | AI 聊天机器人 |
| https://community-web.ccw.site/study-main/external/face/detect | POST | JSON | JSON | 人脸检测 |
| https://community-web.ccw.site/study-main/external/sms/send | POST | JSON | JSON | 发送短信验证码 |
| https://community-web.ccw.site/study-main/external/ip/resolve | POST | JSON | JSON | IP 地址解析 |
| https://community-web.ccw.site/study-main/external/weather/forecast15days | POST | JSON | JSON | 15 天天气预报 |
| https://community-web.ccw.site/study-main/external/weather/aqiforecast5days | POST | JSON | JSON | 空气质量 5 天预报 |
| https://community-web.ccw.site/study-main/schools/list | POST | JSON | JSON | 学校列表 |
| https://community-web.ccw.site/study-main/districts/list | POST | JSON | JSON | 行政区划列表 |
| https://community-web.ccw.site/study-main/activities/creation_works/verify | POST | JSON | JSON | 课堂活动作品校验 |
| https://community-web.ccw.site/wechat/js_config | POST | JSON | JSON | 微信 JS-SDK 签名配置 |
| https://community-web.ccw.site/yu_federation/identity_assertion/create | POST | JSON | JSON | 宇盟身份断言创建 |
| https://community-web.ccw.site/yu_federation/publish_assertion/create | POST | JSON | JSON | 宇盟发布断言创建 |
| https://community-web.ccw.site/base/dateTime | POST | JSON | JSON | 服务端当前时间（防时间戳偏移） |
| https://community-web.ccw.site/study-community/member/detail | POST | JSON | JSON | 成员详情 |

---

## 二十二、已排除项（第三方 / 静态资源）

以下为抓取中出现但**不属于 ccw.site 业务接口**

| 类别 | 示例 |
| :--- | :--- |
| 埋点统计 | `www.umeng.com`、`www.mob.com`、`res.wx.qq.com/connect/zh_CN/htmledition/js/wxLogin.js`、`sogou_site_verification` |
| 社交分享 SDK | `connect.qq.com/widget/shareqq`、`sns.qzone.qq.com/cgi-bin/qzshare`、`wiki.connect.qq.com/qq` |
| CDN / 静态资源 | `static.xiguacity.cn/**`、`m.ccw.site/community/images/**`、`m.ccw.site/gutenberg/**`、`assets.ccw.site/**`、`bilivideo.ccw.site` |
| Scratch 官方 | `projects.scratch.mit.edu?token=`、`assets.scratch.mit.edu` |
| OSS 直连 | `zhishi.oss-cn-beijing.aliyuncs.com`、`xiguahw.oss-cn-beijing.aliyuncs.com` |
| 第三方 npm CDN | `cdn.jsdelivr.net`、`cdn.jsdmirror.com`、`cdn.tldraw.com` |
| 前端路由（非 API） | `/profile/verify`、`/reputation/rules`、`/topic/id/`、`/workspace/my`、`/modeck/guide`、`/classes/`、`/projects/`、`/gandi/project/`、`/educators/classes/`、`/assets/**`、`/post/:slug` 等 |
| 系统路径（Node 误匹配） | `/proc/self/*`、`/dev/null`、`/dev/shm/*`、`/home/web_user`、`/size/w` |

---

## 二十三、覆盖率与待验证说明

| 项目 | 数值 |
| :--- | :--- |
| 抓取 JS 文件 | 68 个（4 入口 + 64 chunk），约 22 MB |
| 接口总数 | **231 条**（已去重、按 host+path 归一） |
| 其中 community-web.ccw.site | 约 200 条 |
| sso / gandi-main / bfs-web / cloud-variable | 6 / 5 / 5 / 2 条 |
| 明确方法（GET/POST/PATCH） | 约 218 条 |
| 方法待验证 | 约 13 条（前端路由拼接或动态变量注入，已在表中标注） |
| sourcemap | **不可用**，`static-map.xiguacity.cn` DNS 无法解析（已重试 3 次 + 公共 DNS 兜底均失败） |

**标"待验证"的项**原因分三类：① 该字符串为前端路由而非 API（如 `/profile/verify`）；② 路径由变量动态拼接，无法静态还原（如 `/topic/id/`、`/gandi/project/`）；③ 请求实例为运行时创建且方法由调用方二次封装（如 `/auth/wechat/login`）。

**核心结论**：全站采用**统一 POST + JSON 信封**的后端协议（`{status, code, msg, body}`），业务查询接口绝大多数也是 POST，仅 Gandi 排行榜、扩展列表、云数据库直连等少数为 GET。
