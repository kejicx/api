# 百度（baidu.com 及其子域）网页版 API 接口文档

> 站点：百度 Baidu — 中文搜索引擎与互联网产品矩阵（网页搜索 / 翻译 / 智能云 / 知道 / 百科 / 文库 / 学术 / 地图 / 账号通行证 等）
> 抓取时间：2026-10-05
> 抓取范围：`https://www.baidu.com`、`fanyi.baidu.com`、`cloud.baidu.com`、`zhidao.baidu.com`、`baike.baidu.com`、`wenku.baidu.com`、`xueshu.baidu.com`、`map.baidu.com`、`image.baidu.com`、`graph.baidu.com`、`jingyan.baidu.com`、`v.baidu.com`、`haokan.baidu.com`、`passport.baidu.com`、`top.baidu.com`、`news.baidu.com`、`anquan.baidu.com`、`yiyan.baidu.com`、`aifanfan.baidu.com`、`opendata.baidu.com`、`pan.baidu.com`、`tieba.baidu.com` 等 25 个业务子域首页 → 从 HTML 提取 **157 个 JS bundle**（约 41 MB）+ 第二批异步 chunk **47 个**（含 passport / wappass / sug / ufosdk，约 3.6 MB）+ 各页面内联 `<script>`
> 提取方式：无 sourcemap。百度为**多子域、各自独立前端工程**架构（不同于单一 baseURL），因此对每个 bundle 按其来源页面归属业务域，再以 `"/api/..."`、`"/rest/..."`、`"/sugrec"`、`"/su"`、`"/submit/..."` 等路径字面量正则提取，结合请求上下文证据（`.get()/.post()/.jsonp()`、`method:"xxx"`、`ajax`、`fetch`、`url:`）甄别真实接口，回溯调用点还原 HTTP 方法；无直接证据者按路径语义（`get/list/search/suggest`→GET，`submit/upload/create/vote/follow`→POST）推断
> **接口总数：426 条唯一接口路径（宿主+路径），含同路径多方法共 428 条接口条目**；覆盖 32 个业务子域、398 个不同 URL 路径。方法分布：GET 301、POST 127、PUT 0、DELETE 0
> 已排除：静态资源与 CDN 域名（`*.bdstatic.com`、`*.bcebos.com`、`*.bdimg.com`、`*.cdn.bcebos`、`hiphotos`、`pic.rmb`）、图片/字体/图标资源、第三方站点（`renren.com`、`weibo.com`、`qq.com`、`douyin`、`meituan`）、APM 埋点与统计（`hm.baidu.com`、`nsclick`、`sclick`、`eclick`、`fclog`、`dlswbr`、`cbjs`）、测试与内网环境（`*.bcetest.baidu.com`、`*.baidu-int.com`、`yapi.`、`cloudtest`、`intl.`）、`.swf` 二进制
> **范围限制说明**：`tieba.baidu.com`（贴吧）、`opendata.baidu.com`、`wenku.baidu.com`、`baike.baidu.com` 等子域在抓取时返回"百度安全验证"或极简跳转页，其前端 bundle 无法完整获取；本文档对这类子域仅收录可从公开页面与共享组件中提取到的接口，未做运行时抓包补全。百度产品线极多，本文档聚焦**搜索主站、翻译、智能云、知道、百科、文库、学术、地图、账号**等核心可逆向子域。

---

## 一、通用约定

| 项目 | 说明 |
| :--- | :--- |
| 主搜索入口 | `https://www.baidu.com/s`（网页搜索结果，Query 参数 `wd`/`word`、`rn`、`pn`、`ie`、`tn`、`f`、`medium` 等） |
| 搜索建议 | `https://www.baidu.com/sugrec`、`https://suggestion.baidu.com/su`、`https://www.baidu.com/sug`（关键词联想，JSONP `cb` 回调） |
| 账号 / 通行证 | `https://passport.baidu.com/v2/**`、`https://passport.baidu.com/v3/**`、`https://wappass.baidu.com/passport/login`（登录、扫码、验证码、用户信息） |
| 各子域独立入口 | 翻译 `fanyi.baidu.com`、智能云 `cloud.baidu.com`、知道 `zhidao.baidu.com`、百科 `baike.baidu.com`、文库 `wenku.baidu.com`、学术 `xueshu.baidu.com`、地图 `map.baidu.com` / `api.map.baidu.com`、识图 `graph.baidu.com`、经验 `jingyan.baidu.com`、视频 `v.baidu.com` 等，**每个子域为独立前端工程、独立接口命名空间** |
| 鉴权 | Cookie `BAIDUID`（设备标识）、`BDUSS`（登录态核心凭证）、`STOKEN`、`BAIDUID_BFESS`；智能云控制台另用 `login.bce.baidu.com` 会话；部分接口需 `t`（时间戳）+ `sign`（签名）或 `x-bce-date`/`Authorization`（BCE V1/V3 签名） |
| 请求格式 | `GET`/`DELETE` 走 Query（`params`）；`POST`/`PUT`/`PATCH` 走 JSON body；搜索建议类走 JSONP（`cb=xxx` 回调包裹）；文件/文档上传走 `multipart/form-data` |
| 返回格式 | 多为 JSON。搜索类返回 HTML 片段或 `{Result, tokens}`；智能云统一信封 `{code, message, data, requestId}`；翻译类 `{error_code, from, to, trans_result}`；列表类含分页字段（`page`/`pageSize`/`total` 或游标） |
| 未登录 / 风控 | 未登录写操作返回业务错误码或跳转 `wappass.baidu.com`；命中反爬返回"百度安全验证"（`wappass` 验证码页），需滑块 / 短信验证 |
| 路径变量 | 表中 `{id}`、`{sign}` 等代表运行时拼接变量，实际调用如 `/v2/api?method=xxx`、`/item/{docId}` |

**请求方法与语义对照**：`GET`=查询、`POST`=创建/提交/动作、`PUT`=整体更新、`DELETE`=删除。同一路径出现多个方法时表示该资源支持多种操作。百度各子域接口风格不统一（老产品多用 `.php`/`?method=` 分发，新产品多用 RESTful `/api/**`），下表按子域业务归类。

## 开放平台·统计与日志（11 条）

> 百度开放平台、联盟统计、日志上报与数据服务接口

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://aifanfan.baidu.com/leads/download | POST | JSON | JSON | 线索下载 |
| https://aifanfan.baidu.com/log/seabed | POST | JSON | JSON | 日志埋点 |
| https://api.map.baidu.com/api | GET | Query | JSON | 百度地图开放平台 Web 服务 API 统一入口（地理编码/路线规划/POI 检索等按参数分发） |
| https://api.open.baidu.com/new_hsug/data/Delete | GET | Query | JSON | 数据删除 |
| https://ext.baidu.com/rest/id-mapping/cuid | GET | Query | JSON | REST接口ID映射设备标识 |
| https://fanyi-api.baidu.com/api/trans/activity/conf | GET | Query | JSON | 接口翻译活动配置 |
| https://fclog.baidu.com/log/weirwood | POST | JSON | JSON | 日志埋点 |
| https://market.baidu.com/api | GET | Query | JSON | 百度营销平台 API 统一入口 |
| https://mbd.baidu.com/feed/api/config/index | GET | Query | JSON | 信息流接口配置首页 |
| https://secr.baidu.com/download | POST | JSON | JSON | 安全风控组件资源下载接口 |
| https://suggestion.baidu.com/su | GET | Query | JSON | 百度搜索建议（关键词联想）接口 |

## 图片·经验·视频等垂直产品（27 条）

> 百度图片、识图、经验、视频、爱采购、汉语、对话搜索等垂直产品接口

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://anquan.baidu.com/activity/prize | GET | Query | JSON | 百度安全中心活动奖品领取接口 |
| https://anquan.baidu.com/haoma/common | GET | Query | JSON | 号码服务（haoma）常用查询接口 |
| https://anquan.baidu.com/haoma/search | GET | Query | JSON | 号码服务（haoma）号码搜索接口 |
| https://b2b.baidu.com/s | GET | Query | JSON | 百度爱采购商品搜索接口 |
| https://chat.baidu.com/search | GET | Query | JSON | 百度 AI 对话搜索接口 |
| https://f3.baidu.com/index.php/feedback/zx/bdis | POST | JSON | JSON | 反馈在线设备 |
| https://graph.baidu.com/s | GET | Query | JSON | 百度识图（图像搜索）结果接口 |
| https://graph.baidu.com/upload | POST | multipart | JSON | 百度识图图片上传接口 |
| https://hanyu.baidu.com/s | GET | Query | JSON | 百度汉语字词查询接口 |
| https://hku.baidu.com/h5/share/detail | POST | JSON | JSON | 分享详情 |
| https://hku.baidu.com/h5/share/s/ | POST | JSON | JSON | 高校活动 H5 分享短链接口 |
| https://image.baidu.com/search/index | GET | Query | JSON | 搜索首页 |
| https://image.baidu.com/sugrec | GET | Query | JSON | 搜索建议 |
| https://jingyan.baidu.com/edit/content | GET | Query | JSON | 编辑内容 |
| https://jingyan.baidu.com/search | GET | Query | JSON | 百度经验内容搜索接口 |
| https://jingyan.baidu.com/submit/author | POST | JSON | JSON | 百度经验提交作者关注/认证接口 |
| https://jingyan.baidu.com/submit/comment | POST | JSON | JSON | 百度经验提交评论接口 |
| https://jingyan.baidu.com/submit/exp | POST | JSON | JSON | 百度经验提交经验（发布文章）接口 |
| https://jingyan.baidu.com/submit/follow | POST | JSON | JSON | 百度经验提交关注操作接口 |
| https://jingyan.baidu.com/submit/notice | POST | JSON | JSON | 百度经验提交通知已读接口 |
| https://jingyan.baidu.com/submit/user | POST | JSON | JSON | 百度经验提交用户信息接口 |
| https://jingyan.baidu.com/submit/vote | POST | JSON | JSON | 百度经验提交投票（有用/收藏）接口 |
| https://jingyan.baidu.com/user/submit/favor | POST | JSON | JSON | 百度经验用户提交收藏接口 |
| https://m.baidu.com/s | GET | Query | JSON | 百度搜索（移动端）结果接口 |
| https://pan.baidu.com/api/analytics | GET | Query | JSON | 百度网盘埋点数据统计接口 |
| https://v.baidu.com/follow/works | POST | JSON | JSON | 百度视频关注作品列表接口 |
| https://v.baidu.com/uc/follow/ | GET | Query | JSON | 百度视频用户关注接口 |

## 百度百科（20 条）

> 百度百科词条、编辑、搜索、关系与多媒体接口

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://baike.baidu.com/api/searchui/clearhistory | GET | Query | JSON | 接口搜索UI清空历史 |
| https://baike.baidu.com/api/searchui/deletehistory | GET | Query | JSON | 接口搜索UI删除历史 |
| https://baike.baidu.com/api/searchui/gethistory | GET | Query | JSON | 接口搜索UI获取历史 |
| https://baike.baidu.com/api/searchui/suggest | GET | Query | JSON | 接口搜索UI联想 |
| https://baike.baidu.com/api/usercenter/battle | GET | Query | JSON | 接口用户中心答题 |
| https://baike.baidu.com/api/usercenter/login | GET | Query | JSON | 接口用户中心登录 |
| https://baike.baidu.com/api/wikiad/getcapybaras | GET | Query | JSON | 接口百科广告获取广告位 |
| https://baike.baidu.com/api/wikihome/totalnum | GET | Query | JSON | 接口百科首页总数 |
| https://baike.baidu.com/api/wikihome/userlemma | GET | Query | JSON | 接口百科首页用户词条 |
| https://baike.baidu.com/api/wikimessage/readmsgstatus | GET | Query | JSON | 接口百科消息读取消息状态 |
| https://baike.baidu.com/editor/check/preeditcheck | GET | Query | JSON | 编辑器检查编辑前检查 |
| https://baike.baidu.com/event/api/challenge/getshowpage | POST | JSON | JSON | 活动接口挑战赛获取活动页 |
| https://baike.baidu.com/event/api/challenge/getsigninshowpage | GET | Query | JSON | 活动接口挑战赛获取签到页 |
| https://baike.baidu.com/event/api/challenge/takereward | POST | JSON | JSON | 活动接口挑战赛领取奖励 |
| https://baike.baidu.com/event/api/challenge/updatestatus | POST | JSON | JSON | 活动接口挑战赛更新状态 |
| https://baike.baidu.com/event/api/challenge/updatetaketime | POST | JSON | JSON | 活动接口挑战赛更新领取时间 |
| https://baike.baidu.com/search | GET | Query | JSON | 百度百科词条搜索接口 |
| https://baike.baidu.com/search/word | GET | Query | JSON | 搜索词语 |
| https://baike.baidu.com/ucenter/task | GET | Query | JSON | 用户中心任务 |
| https://baike.baidu.com/ucenter/task/lemma | GET | Query | JSON | 用户中心任务词条 |

## 智能云·门户内容与运营（43 条）

> 百度智能云官网门户、资讯、产品文档、解决方案与内容管理接口

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://cloud.baidu.com/api | GET | Query | JSON | 百度智能云控制台 API 统一入口 |
| https://cloud.baidu.com/api/bcc/image/list/forCreateInstance | GET | Query | JSON | 接口云服务器BCC图片列表 |
| https://cloud.baidu.com/api/cloud-live/msg | GET | Query | JSON | 接口消息 |
| https://cloud.baidu.com/api/cloud-live/uc_bind_pass | POST | JSON | JSON | 智能云直播账号与百度通行证绑定接口 |
| https://cloud.baidu.com/api/cloud-server/clear-preview | POST | JSON | JSON | 智能云服务器产品预览数据清除接口 |
| https://cloud.baidu.com/api/cms/data | GET | Query | JSON | 接口内容管理数据 |
| https://cloud.baidu.com/api/cms/path | GET | Query | JSON | 接口内容管理 |
| https://cloud.baidu.com/api/cms/tpl | GET | Query | JSON | 接口内容管理 |
| https://cloud.baidu.com/api/common/ability/auth-code | GET | Query | JSON | 接口公共授权码 |
| https://cloud.baidu.com/api/common/ability/auth-code/verify | POST | JSON | JSON | 接口公共授权码校验 |
| https://cloud.baidu.com/api/cveList | GET | Query | JSON | 接口漏洞列表 |
| https://cloud.baidu.com/api/feed/similar_product | GET | Query | JSON | 接口信息流相似产品 |
| https://cloud.baidu.com/api/gaiadb/package/config | GET | Query | JSON | 接口向量数据库套餐配置 |
| https://cloud.baidu.com/api/global/mirror/list | GET | Query | JSON | 接口镜像列表 |
| https://cloud.baidu.com/api/live/comment | POST | JSON | JSON | 接口直播评论 |
| https://cloud.baidu.com/api/live/current | GET | Query | JSON | 接口直播当前用户 |
| https://cloud.baidu.com/api/live/delete_comment | POST | JSON | JSON | 接口直播 |
| https://cloud.baidu.com/api/news | GET | Query | JSON | 接口新闻 |
| https://cloud.baidu.com/api/news_list | GET | Query | JSON | 接口新闻列表 |
| https://cloud.baidu.com/api/portal/leads | GET | Query | JSON | 接口门户线索 |
| https://cloud.baidu.com/api/portal/leads | POST | JSON | JSON | 接口门户线索 |
| https://cloud.baidu.com/api/portal/partner/activity_invite | POST | JSON | JSON | 接口门户合作伙伴 |
| https://cloud.baidu.com/api/portal/sub-navigation | GET | Query | JSON | 接口门户子导航 |
| https://cloud.baidu.com/api/portalsearch | GET | Query | JSON | 接口门户搜索 |
| https://cloud.baidu.com/api/rank/news_ticker | GET | Query | JSON | 接口排行 |
| https://cloud.baidu.com/api/rds/package/config | GET | Query | JSON | 接口云数据库套餐配置 |
| https://cloud.baidu.com/api/scs/package/config | GET | Query | JSON | 接口存储套餐配置 |
| https://cloud.baidu.com/api/search/top | GET | Query | JSON | 接口搜索顶部 |
| https://cloud.baidu.com/api/search/type | GET | Query | JSON | 接口搜索 |
| https://cloud.baidu.com/api/securityBulletins | GET | Query | JSON | 接口安全公告 |
| https://cloud.baidu.com/api/suggest | GET | Query | JSON | 接口联想 |
| https://cloud.baidu.com/api/tms/query/detail | GET | Query | JSON | 接口查询详情 |
| https://cloud.baidu.com/api/tms/query/list | GET | Query | JSON | 接口查询列表 |
| https://cloud.baidu.com/api/tms/query/time | GET | Query | JSON | 接口查询 |
| https://cloud.baidu.com/api/tms/transaction/goods/query-list | GET | Query | JSON | 智能云交易系统商品查询列表接口 |
| https://cloud.baidu.com/api/tms/transaction/v2/goods/list | GET | Query | JSON | 接口v2列表 |
| https://cloud.baidu.com/api/video-center/dislikes | GET | Query | JSON | 接口视频中心点踩数 |
| https://cloud.baidu.com/api/video-center/likes | GET | Query | JSON | 接口视频中心点赞数 |
| https://cloud.baidu.com/api/video-center/list | GET | Query | JSON | 接口视频中心列表 |
| https://cloud.baidu.com/api/video-center/preCourseCheck | GET | Query | JSON | 接口视频中心课程预检 |
| https://cloud.baidu.com/api/video-center/type | GET | Query | JSON | 接口视频中心 |
| https://cloud.baidu.com/api/video-center/user-info | GET | Query | JSON | 接口视频中心 |
| https://cloud.baidu.com/api/video-center/video | GET | Query | JSON | 接口视频中心视频 |
| https://cloud.baidu.com/api/video-center/views | GET | Query | JSON | 接口视频中心播放量 |

## 智能云·生态合作与开发者（32 条）

> 百度智能云生态伙伴、开发者中心、ISV 与合作计划接口

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://cloud.baidu.com/api/accelerator/course | GET | Query | JSON | 接口课程 |
| https://cloud.baidu.com/api/accelerator/nums | GET | Query | JSON | 接口数量 |
| https://cloud.baidu.com/api/accelerator/status | POST | JSON | JSON | 接口状态 |
| https://cloud.baidu.com/api/bce_developer | GET | Query | JSON | 百度智能云开发者中心（BCE Developer）接口 |
| https://cloud.baidu.com/api/case_center/scene_industry_data | GET | Query | JSON | 接口案例中心 |
| https://cloud.baidu.com/api/case_center/search | GET | Query | JSON | 接口案例中心搜索 |
| https://cloud.baidu.com/api/doc/best_practice/list | GET | Query | JSON | 接口文档最佳实践列表 |
| https://cloud.baidu.com/api/doc/best_practice/type | GET | Query | JSON | 接口文档最佳实践 |
| https://cloud.baidu.com/api/doc/operation_info | GET | Query | JSON | 接口文档运营信息 |
| https://cloud.baidu.com/api/doc/search | GET | Query | JSON | 接口文档搜索 |
| https://cloud.baidu.com/api/partner-video/tag | GET | Query | JSON | 接口标签 |
| https://cloud.baidu.com/api/partner/course | GET | Query | JSON | 接口合作伙伴课程 |
| https://cloud.baidu.com/api/partner/data/download_record | GET | Query | JSON | 接口合作伙伴数据 |
| https://cloud.baidu.com/api/partner/exam | GET | Query | JSON | 接口合作伙伴考试 |
| https://cloud.baidu.com/api/partner/exam/apply | POST | JSON | JSON | 接口合作伙伴考试 |
| https://cloud.baidu.com/api/partner/exam/qualify | POST | JSON | JSON | 接口合作伙伴考试资质 |
| https://cloud.baidu.com/api/partner/exam/result | GET | Query | JSON | 接口合作伙伴考试 |
| https://cloud.baidu.com/api/partner/invitation/count | POST | JSON | JSON | 接口合作伙伴邀请数量 |
| https://cloud.baidu.com/api/partner/invitation/info | GET | Query | JSON | 接口合作伙伴邀请信息 |
| https://cloud.baidu.com/api/partner/invitation/update_inviter_info | POST | JSON | JSON | 接口合作伙伴邀请 |
| https://cloud.baidu.com/api/partner/invitation/user_info | GET | Query | JSON | 接口合作伙伴邀请 |
| https://cloud.baidu.com/api/partner/portal/product/categories | GET | Query | JSON | 接口合作伙伴门户产品分类 |
| https://cloud.baidu.com/api/partner/portal/product/compatibility | GET | Query | JSON | 接口合作伙伴门户产品 |
| https://cloud.baidu.com/api/partner/portal/query | GET | Query | JSON | 接口合作伙伴门户查询 |
| https://cloud.baidu.com/api/partner/portal/video/qualify | POST | JSON | JSON | 接口合作伙伴门户视频资质 |
| https://cloud.baidu.com/api/practice | GET | Query | JSON | 接口练习 |
| https://cloud.baidu.com/api/practice/category | GET | Query | JSON | 接口练习分类 |
| https://cloud.baidu.com/api/practice/search | GET | Query | JSON | 接口练习搜索 |
| https://cloud.baidu.com/api/summit/live/status | POST | JSON | JSON | 接口峰会直播状态 |
| https://cloud.baidu.com/api/survey_summit/apply | POST | JSON | JSON | 接口问卷提交 |
| https://cloud.baidu.com/api/survey_summit/precondition | GET | Query | JSON | 接口问卷提交 |
| https://cloud.baidu.com/api/survey_summit/search_company | GET | Query | JSON | 接口问卷提交 |

## 智能云·账号与基础服务（8 条）

> 百度智能云账号、实名、权限、消息与基础服务接口

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://cloud.baidu.com/api/account/loginName | GET | Query | JSON | 接口账号登录名 |
| https://cloud.baidu.com/api/account/real_name/type | GET | Query | JSON | 接口账号实名 |
| https://cloud.baidu.com/api/account/session | GET | Query | JSON | 接口账号会话 |
| https://cloud.baidu.com/api/account/status | POST | JSON | JSON | 接口账号状态 |
| https://cloud.baidu.com/api/account/v2/displayName | GET | Query | JSON | 接口账号v2显示名称 |
| https://cloud.baidu.com/api/iam/sts/role/activate | POST | JSON | JSON | 接口身份管理临时授权角色激活 |
| https://cloud.baidu.com/upload/image | POST | multipart | JSON | 上传图片 |
| https://cloud.baidu.com/user/current | GET | Query | JSON | 用户当前用户 |

## 智能云·价格计算器与产品（41 条）

> 百度智能云产品价格计算器、产品配置、规格与询价接口

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://cloud.baidu.com/api/bcc/instance/flavorSpec | GET | Query | JSON | 接口云服务器BCC实例 |
| https://cloud.baidu.com/api/calculator/addCalculatorList | GET | Query | JSON | 接口价格计算器 |
| https://cloud.baidu.com/api/calculator/bcc/instance/price | GET | Query | JSON | 接口价格计算器云服务器BCC实例价格 |
| https://cloud.baidu.com/api/calculator/bcc/instance/priceV2 | GET | Query | JSON | 接口价格计算器云服务器BCC实例 |
| https://cloud.baidu.com/api/calculator/bccFlavor | GET | Query | JSON | 接口价格计算器 |
| https://cloud.baidu.com/api/calculator/bec/flavor | GET | Query | JSON | 接口价格计算器边缘云规格 |
| https://cloud.baidu.com/api/calculator/bec/price | GET | Query | JSON | 接口价格计算器边缘云价格 |
| https://cloud.baidu.com/api/calculator/bes/module/listWithSlot | GET | Query | JSON | 接口价格计算器ES模块 |
| https://cloud.baidu.com/api/calculator/bmr/flavor | GET | Query | JSON | 接口价格计算器 MapReduce规格 |
| https://cloud.baidu.com/api/calculator/bos/flavor | GET | Query | JSON | 接口价格计算器对象存储规格 |
| https://cloud.baidu.com/api/calculator/dedicated/capacity | GET | Query | JSON | 接口价格计算器 |
| https://cloud.baidu.com/api/calculator/dedicated/price | GET | Query | JSON | 接口价格计算器价格 |
| https://cloud.baidu.com/api/calculator/dedicated/resource | GET | Query | JSON | 接口价格计算器资源 |
| https://cloud.baidu.com/api/calculator/delCalculatorList | GET | Query | JSON | 接口价格计算器 |
| https://cloud.baidu.com/api/calculator/getCalculatorList | GET | Query | JSON | 接口价格计算器 |
| https://cloud.baidu.com/api/calculator/getPrice/batch | GET | Query | JSON | 接口价格计算器批量 |
| https://cloud.baidu.com/api/calculator/gscp/price | GET | Query | JSON | 接口价格计算器价格 |
| https://cloud.baidu.com/api/calculator/logstash/module/listWithSlot | GET | Query | JSON | 接口价格计算器Logstash模块 |
| https://cloud.baidu.com/api/calculator/ls/flavor | GET | Query | JSON | 接口价格计算器Logstash规格 |
| https://cloud.baidu.com/api/calculator/ls/price | GET | Query | JSON | 接口价格计算器Logstash价格 |
| https://cloud.baidu.com/api/calculator/palo/module/listWithSlot | GET | Query | JSON | 接口价格计算器Palo数据库模块 |
| https://cloud.baidu.com/api/calculator/qianfan/order/price | GET | Query | JSON | 接口价格计算器千帆大模型订单价格 |
| https://cloud.baidu.com/api/calculator/snapshot/price | GET | Query | JSON | 接口价格计算器快照价格 |
| https://cloud.baidu.com/api/calculator/updateCalculatorList | GET | Query | JSON | 接口价格计算器 |
| https://cloud.baidu.com/api/merge_purchase/auth_code | GET | Query | JSON | 接口组合购买 |
| https://cloud.baidu.com/api/merge_purchase/auth_code/verify | POST | JSON | JSON | 接口组合购买校验 |
| https://cloud.baidu.com/api/merge_purchase/orders | GET | Query | JSON | 接口组合购买订单 |
| https://cloud.baidu.com/api/merge_purchase/orders/mobile | GET | Query | JSON | 接口组合购买订单 |
| https://cloud.baidu.com/api/merge_purchase/prices | GET | Query | JSON | 接口组合购买价格 |
| https://cloud.baidu.com/api/merge_purchase/vpc_list | GET | Query | JSON | 接口组合购买 |
| https://cloud.baidu.com/api/palo/portal/price-calculator/flavor/v1/decoupled | GET | Query | JSON | 接口Palo数据库门户规格v1 |
| https://cloud.baidu.com/api/palo/portal/price-calculator/flavor/v1/integrated | GET | Query | JSON | 接口Palo数据库门户规格v1 |
| https://cloud.baidu.com/api/price | GET | Query | JSON | 接口价格 |
| https://cloud.baidu.com/api/price/activity_detail | GET | Query | JSON | 接口价格 |
| https://cloud.baidu.com/api/price/activity_list | GET | Query | JSON | 接口价格 |
| https://cloud.baidu.com/api/price/config | GET | Query | JSON | 接口价格配置 |
| https://cloud.baidu.com/api/price/config/cache | GET | Query | JSON | 接口价格配置 |
| https://cloud.baidu.com/api/price/merge-product | GET | Query | JSON | 接口价格 |
| https://cloud.baidu.com/api/region/list | GET | Query | JSON | 接口地域列表 |
| https://cloud.baidu.com/api/region/region_list | GET | Query | JSON | 接口地域 |
| https://cloud.baidu.com/api/res/group | GET | Query | JSON | 接口资源分组 |

## 智能云·域名与备案（7 条）

> 百度智能云域名管理、WHOIS、ICP 备案与解析接口

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://cloud.baidu.com/api/bcd/open/domain/detail | GET | Query | JSON | 接口域名域名详情 |
| https://cloud.baidu.com/api/bcd/open/notice/query | GET | Query | JSON | 接口域名通知查询 |
| https://cloud.baidu.com/api/bcd/open/portal/agent-demand/anonymous-verification | POST | JSON | JSON | 接口域名门户需求 |
| https://cloud.baidu.com/api/bcd/open/portal/anonymous/agent-consult | POST | JSON | JSON | 接口域名门户匿名 |
| https://cloud.baidu.com/api/bcd/open/portal/recommend | GET | Query | JSON | 接口域名门户推荐 |
| https://cloud.baidu.com/api/bcd/open/resource/package/sale | GET | Query | JSON | 接口域名资源套餐 |
| https://cloud.baidu.com/api/bcd/whois/suffix_recommend | GET | Query | JSON | 接口域名域名查询 |

## 智能云·营销运营与活动（31 条）

> 百度智能云营销活动、优惠券、报名、抽奖与运营位接口

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://cloud.baidu.com/api/bla/portal/activities | GET | Query | JSON | 接口区块链存证门户 |
| https://cloud.baidu.com/api/bla/portal/navigation | GET | Query | JSON | 接口区块链存证门户导航 |
| https://cloud.baidu.com/api/bla/portal/nft/banner | GET | Query | JSON | 接口区块链存证门户数字藏品横幅 |
| https://cloud.baidu.com/api/bla/portal/nft/qualifications | GET | Query | JSON | 接口区块链存证门户数字藏品 |
| https://cloud.baidu.com/api/bla/portal/qualifications | GET | Query | JSON | 接口区块链存证门户 |
| https://cloud.baidu.com/api/bla/portal/recommendation | GET | Query | JSON | 接口区块链存证门户推荐 |
| https://cloud.baidu.com/api/bla/portal/section | GET | Query | JSON | 接口区块链存证门户板块 |
| https://cloud.baidu.com/api/bla/qualification/industries | GET | Query | JSON | 接口区块链存证资质 |
| https://cloud.baidu.com/api/bla/qualification/query | GET | Query | JSON | 接口区块链存证资质查询 |
| https://cloud.baidu.com/api/bla/requirements/anonymous | POST | JSON | JSON | 接口区块链存证匿名 |
| https://cloud.baidu.com/api/bla/requirements/verify | POST | JSON | JSON | 接口区块链存证校验 |
| https://cloud.baidu.com/api/coupon/avail | GET | Query | JSON | 接口优惠券 |
| https://cloud.baidu.com/api/crs/demand/anonymous | POST | JSON | JSON | 接口需求匿名 |
| https://cloud.baidu.com/api/crs/static/data | GET | Query | JSON | 接口数据 |
| https://cloud.baidu.com/api/crs/verification/sms | POST | JSON | JSON | 接口验证短信 |
| https://cloud.baidu.com/api/sme/clue/behavior/save | POST | JSON | JSON | 接口中小企业线索行为保存 |
| https://cloud.baidu.com/api/sme/coupon | GET | Query | JSON | 接口中小企业优惠券 |
| https://cloud.baidu.com/api/sme/coupon/issue-coupon | POST | JSON | JSON | 接口中小企业优惠券 |
| https://cloud.baidu.com/api/sme/coupon/promotion | GET | Query | JSON | 接口中小企业优惠券 |
| https://cloud.baidu.com/api/sme/user/activity/info | GET | Query | JSON | 接口中小企业用户活动信息 |
| https://cloud.baidu.com/api/sme/user/activity/isNewUser | GET | Query | JSON | 接口中小企业用户活动新用户判断 |
| https://cloud.baidu.com/api/survey/need_login/apply | POST | JSON | JSON | 接口问卷 |
| https://cloud.baidu.com/api/survey/need_login/precondition | GET | Query | JSON | 接口问卷 |
| https://cloud.baidu.com/api/survey/no_need_login/apply | POST | JSON | JSON | 接口问卷 |
| https://cloud.baidu.com/api/survey/status | POST | JSON | JSON | 接口问卷状态 |
| https://cloud.baidu.com/api/survey/upload | POST | multipart | JSON | 接口问卷上传 |
| https://cloud.baidu.com/api/wx_signature | POST | JSON | JSON | 接口微信签名 |
| https://cloud.baidu.com/api/yunying/coupon/apply | POST | JSON | JSON | 接口运营优惠券 |
| https://cloud.baidu.com/api/yunying/coupon/precondition | GET | Query | JSON | 接口运营优惠券 |
| https://cloud.baidu.com/api/yunying/discount/login/precondition | POST | JSON | JSON | 接口运营折扣登录 |
| https://cloud.baidu.com/api/yunying/order/data/save-order-config-data | POST | JSON | JSON | 接口运营订单数据 |

## 智能云·千帆大模型（9 条）

> 百度智能云千帆大模型平台（Qianfan）模型、数据集、推理与训练接口

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://cloud.baidu.com/api/portal/partner/activity/qianfan_invocation_rank | GET | Query | JSON | 接口门户合作伙伴活动 |
| https://cloud.baidu.com/api/qianfan/agree_protocol | POST | JSON | JSON | 接口千帆大模型 |
| https://cloud.baidu.com/api/qianfan/carrier/api | POST | JSON | JSON | 接口千帆大模型运营商接口 |
| https://cloud.baidu.com/api/qianfan/charge/overview/chargeInfo | GET | Query | JSON | 接口千帆大模型计费费用信息 |
| https://cloud.baidu.com/api/qianfan/chat | GET | Query | JSON | 接口千帆大模型对话 |
| https://cloud.baidu.com/api/qianfan/check_protocol | POST | JSON | JSON | 接口千帆大模型 |
| https://cloud.baidu.com/api/qianfan/completions | POST | JSON | JSON | 接口千帆大模型模型推理 |
| https://cloud.baidu.com/api/qianfan/completions/count | POST | JSON | JSON | 接口千帆大模型模型推理数量 |
| https://cloud.baidu.com/api/qianfan/models | GET | Query | JSON | 接口千帆大模型模型列表 |

## 百度翻译（25 条）

> 百度翻译（在线翻译 / 客户端 / 文档翻译）通用翻译、词库、历史与分享接口

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://fanyi.baidu.com/Pc/baikeEvaluation | GET | Query | JSON | PC端百科评价 |
| https://fanyi.baidu.com/ai/history/copilot | POST | JSON | JSON | 百度翻译 AI 助手对话历史接口 |
| https://fanyi.baidu.com/ai/history/del | POST | JSON | JSON | 历史删除 |
| https://fanyi.baidu.com/api/trans/activity/conf | GET | Query | JSON | 接口翻译活动配置 |
| https://fanyi.baidu.com/client/download/redirect | POST | JSON | JSON | 客户端下载重定向 |
| https://fanyi.baidu.com/editor/edit | GET | Query | JSON | 编辑器编辑 |
| https://fanyi.baidu.com/editor/edit/ | GET | Query | JSON | 编辑器编辑 |
| https://fanyi.baidu.com/editor/read/ | GET | Query | JSON | 百度翻译文档译文编辑器读取接口 |
| https://fanyi.baidu.com/export | POST | JSON | JSON | 百度翻译译文导出接口 |
| https://fanyi.baidu.com/export/download | POST | JSON | JSON | 导出下载 |
| https://fanyi.baidu.com/file/chunk | GET | Query | JSON | 文件分片 |
| https://fanyi.baidu.com/get | GET | Query | JSON | 百度翻译数据获取接口 |
| https://fanyi.baidu.com/log/alert | POST | JSON | JSON | 日志告警 |
| https://fanyi.baidu.com/log/sentence/update | POST | JSON | JSON | 日志句子更新 |
| https://fanyi.baidu.com/login | POST | JSON | JSON | 百度翻译登录状态校验接口 |
| https://fanyi.baidu.com/mock/docPro/export/ | POST | JSON | JSON | 百度翻译文档专业版导出接口 |
| https://fanyi.baidu.com/pc/collection/modify | POST | JSON | JSON | PC端收藏修改 |
| https://fanyi.baidu.com/pc/config | GET | Query | JSON | PC端配置 |
| https://fanyi.baidu.com/pc/vip-intro | GET | Query | JSON | PC端会员介绍 |
| https://fanyi.baidu.com/privatize/web/auth/getInfo | POST | JSON | JSON | 私有化Web鉴权 |
| https://fanyi.baidu.com/privatize/web/auth/user/logout | POST | JSON | JSON | 私有化Web鉴权用户登出 |
| https://fanyi.baidu.com/project-manage/detail/ | GET | Query | JSON | 项目管理详情 |
| https://fanyi.baidu.com/sentence/get | GET | Query | JSON | 句子获取 |
| https://fanyi.baidu.com/sentence/update | POST | JSON | JSON | 句子更新 |
| https://fanyi.baidu.com/share/ | POST | JSON | JSON | 百度翻译结果分享接口 |

## 百度翻译·人工翻译(AIT)（34 条）

> 百度翻译人工翻译（AIT）下单、报价、订单、译员与交付流程接口

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://fanyi.baidu.com/ait/activity/info | GET | Query | JSON | 人工翻译活动信息 |
| https://fanyi.baidu.com/ait/agent/project/create | POST | JSON | JSON | 人工翻译智能体项目创建 |
| https://fanyi.baidu.com/ait/agent/project/delete | POST | JSON | JSON | 人工翻译智能体项目删除 |
| https://fanyi.baidu.com/ait/agent/project/export/download | POST | JSON | JSON | 人工翻译智能体项目导出下载 |
| https://fanyi.baidu.com/ait/agent/project/export/start | POST | JSON | JSON | 人工翻译智能体项目导出开始 |
| https://fanyi.baidu.com/ait/agent/project/export/status | POST | JSON | JSON | 人工翻译智能体项目导出状态 |
| https://fanyi.baidu.com/ait/agent/project/file/delete | POST | JSON | JSON | 人工翻译智能体项目文件删除 |
| https://fanyi.baidu.com/ait/agent/project/list | GET | Query | JSON | 人工翻译智能体项目列表 |
| https://fanyi.baidu.com/ait/catalog/get | GET | Query | JSON | 人工翻译目录获取 |
| https://fanyi.baidu.com/ait/config/cms/list | GET | Query | JSON | 人工翻译配置内容管理列表 |
| https://fanyi.baidu.com/ait/cooperation/conf/get | GET | Query | JSON | 人工翻译协作配置获取 |
| https://fanyi.baidu.com/ait/cooperation/task/get | GET | Query | JSON | 人工翻译协作任务获取 |
| https://fanyi.baidu.com/ait/cooperation/task/list | GET | Query | JSON | 人工翻译协作任务列表 |
| https://fanyi.baidu.com/ait/cooperation/task/submit | POST | JSON | JSON | 人工翻译协作任务提交 |
| https://fanyi.baidu.com/ait/cooperation/task/view | GET | Query | JSON | 人工翻译协作任务 |
| https://fanyi.baidu.com/ait/expert/companyUser/delete | POST | JSON | JSON | 人工翻译专家企业用户删除 |
| https://fanyi.baidu.com/ait/expert/companyUser/list | GET | Query | JSON | 人工翻译专家企业用户列表 |
| https://fanyi.baidu.com/ait/expert/coupon/list | GET | Query | JSON | 人工翻译专家优惠券列表 |
| https://fanyi.baidu.com/ait/expert/order/assign | POST | JSON | JSON | 人工翻译专家订单派单 |
| https://fanyi.baidu.com/ait/expert/order/close | POST | JSON | JSON | 人工翻译专家订单关闭 |
| https://fanyi.baidu.com/ait/expert/order/confirm | POST | JSON | JSON | 人工翻译专家订单确认 |
| https://fanyi.baidu.com/ait/expert/order/delete | POST | JSON | JSON | 人工翻译专家订单删除 |
| https://fanyi.baidu.com/ait/expert/order/deliver | POST | JSON | JSON | 人工翻译专家订单交付 |
| https://fanyi.baidu.com/ait/expert/order/list | GET | Query | JSON | 人工翻译专家订单列表 |
| https://fanyi.baidu.com/ait/expert/order/platform/getInfo | GET | Query | JSON | 人工翻译专家订单平台 |
| https://fanyi.baidu.com/ait/expert/task/getAdjustment | GET | Query | JSON | 人工翻译专家任务获取调整 |
| https://fanyi.baidu.com/ait/expert/task/getRating | GET | Query | JSON | 人工翻译专家任务获取评分 |
| https://fanyi.baidu.com/ait/expert/task/getReturnDetail | GET | Query | JSON | 人工翻译专家任务获取退回详情 |
| https://fanyi.baidu.com/ait/expert/task/purchaseWordCount | GET | Query | JSON | 人工翻译专家任务购买字数 |
| https://fanyi.baidu.com/ait/guest/document/create | POST | JSON | JSON | 人工翻译访客文档创建 |
| https://fanyi.baidu.com/ait/history/list | GET | Query | JSON | 人工翻译历史列表 |
| https://fanyi.baidu.com/ait/knowledge/term/get | GET | Query | JSON | 人工翻译知识库术语获取 |
| https://fanyi.baidu.com/ait/media/pe/list | GET | Query | JSON | 人工翻译媒体译员列表 |
| https://fanyi.baidu.com/ait/picture/get | GET | Query | JSON | 人工翻译图片获取 |

## 百度翻译·译文编辑(MTPE)（24 条）

> 百度翻译文档译文编辑器（MTPE）段落、术语、校对与导出接口

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://fanyi.baidu.com/mtpe/config/getList | GET | Query | JSON | 译文编辑器配置 |
| https://fanyi.baidu.com/mtpe/document/create | POST | JSON | JSON | 译文编辑器文档创建 |
| https://fanyi.baidu.com/mtpe/export/getDetail | POST | JSON | JSON | 译文编辑器导出 |
| https://fanyi.baidu.com/mtpe/export/start | POST | JSON | JSON | 译文编辑器导出开始 |
| https://fanyi.baidu.com/mtpe/log/sentence/update | POST | JSON | JSON | 译文编辑器日志句子更新 |
| https://fanyi.baidu.com/mtpe/v2/activity/invite/masterPage | GET | Query | JSON | 译文编辑器v2活动邀请主页 |
| https://fanyi.baidu.com/mtpe/v2/company/franchisee/bind | POST | JSON | JSON | 译文编辑器v2加盟商绑定 |
| https://fanyi.baidu.com/mtpe/v2/company/franchisee/list | GET | Query | JSON | 译文编辑器v2加盟商列表 |
| https://fanyi.baidu.com/mtpe/v2/company/franchisee/report | POST | JSON | JSON | 译文编辑器v2加盟商上报 |
| https://fanyi.baidu.com/mtpe/v2/company/member/list | GET | Query | JSON | 译文编辑器v2成员列表 |
| https://fanyi.baidu.com/mtpe/v2/company/sso/get | GET | Query | JSON | 译文编辑器v2单点登录获取 |
| https://fanyi.baidu.com/mtpe/v2/config/cms | GET | Query | JSON | 译文编辑器v2配置内容管理 |
| https://fanyi.baidu.com/mtpe/v2/corpus/item/info | GET | Query | JSON | 译文编辑器v2语料库条目信息 |
| https://fanyi.baidu.com/mtpe/v2/export/delete | POST | JSON | JSON | 译文编辑器v2导出删除 |
| https://fanyi.baidu.com/mtpe/v2/export/getList | POST | JSON | JSON | 译文编辑器v2导出 |
| https://fanyi.baidu.com/mtpe/v2/fanyipro/contactForm/create | POST | JSON | JSON | 译文编辑器v2联系表单创建 |
| https://fanyi.baidu.com/mtpe/v2/intelterm/feedback | POST | JSON | JSON | 译文编辑器v2智能术语反馈 |
| https://fanyi.baidu.com/mtpe/v2/member/config | GET | Query | JSON | 译文编辑器v2成员配置 |
| https://fanyi.baidu.com/mtpe/v2/project/get | GET | Query | JSON | 译文编辑器v2项目获取 |
| https://fanyi.baidu.com/mtpe/v2/project/list | GET | Query | JSON | 译文编辑器v2项目列表 |
| https://fanyi.baidu.com/mtpe/v2/promotionSelect/history | GET | Query | JSON | 译文编辑器v2促销选择历史 |
| https://fanyi.baidu.com/mtpe/v2/promotionSelect/info | GET | Query | JSON | 译文编辑器v2促销选择信息 |
| https://fanyi.baidu.com/mtpe/v2/sentence/search | GET | Query | JSON | 译文编辑器v2句子搜索 |
| https://fanyi.baidu.com/mtpe/v2/toolbox/download | POST | JSON | JSON | 译文编辑器v2工具箱下载 |

## 登录与账号安全（2 条）

> 百度通行证登录、渠道、验证码与安全校验接口

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://passport.baidu.com/v3/login/getChannel | POST | JSON | JSON | v3登录获取登录渠道 |
| https://wappass.baidu.com/passport/login | POST | JSON | JSON | 通行证登录 |

## 百度地图（1 条）

> 百度地图 Web / 开放平台接口（本域可提取部分）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://map.baidu.com/su | GET | Query | JSON | 百度地图搜索建议（联想词）接口 |

## 百度文库与学术AI（24 条）

> 百度文库文档检索、下载、会员与学术 AI（ScholarAI）辅助写作接口

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://wenku.baidu.com/data/gettips | GET | Query | JSON | 数据获取提示 |
| https://wenku.baidu.com/scholarai/aiedit/aigccheck | GET | Query | JSON | 学术AIAIGC检测 |
| https://wenku.baidu.com/scholarai/aiedit/aigcrewrite | GET | Query | JSON | 学术AIAI改写 |
| https://wenku.baidu.com/scholarai/aiedit/contentfix | GET | Query | JSON | 学术AI内容修正 |
| https://wenku.baidu.com/scholarai/aiedit/innovationcheck | GET | Query | JSON | 学术AI创新性检测 |
| https://wenku.baidu.com/scholarai/assistant/getassociatedquestion | GET | Query | JSON | 学术AI助手获取关联问题 |
| https://wenku.baidu.com/scholarai/assistant/getuserinfo | GET | Query | JSON | 学术AI助手 |
| https://wenku.baidu.com/scholarai/assistant/papersubmit | GET | Query | JSON | 学术AI助手论文提交 |
| https://wenku.baidu.com/scholarai/assistant/stream | GET | Query | JSON | 学术AI助手流式生成 |
| https://wenku.baidu.com/scholarai/assistant/topic2outline | GET | Query | JSON | 学术AI助手生成大纲 |
| https://wenku.baidu.com/scholarai/doc/createdoc | GET | Query | JSON | 学术AI文档创建文档 |
| https://wenku.baidu.com/scholarai/doc/exportdoc | GET | Query | JSON | 学术AI文档导出文档 |
| https://wenku.baidu.com/scholarai/doc/fanyidoc | GET | Query | JSON | 学术AI文档文档翻译 |
| https://wenku.baidu.com/scholarai/doc/fanyipage | GET | Query | JSON | 学术AI文档页面翻译 |
| https://wenku.baidu.com/scholarai/doc/filesubmit | GET | Query | JSON | 学术AI文档文件提交 |
| https://wenku.baidu.com/scholarai/doc/getuploadbostoken | GET | multipart | JSON | 学术AI文档获取上传凭证 |
| https://wenku.baidu.com/scholarai/doc/ocr | GET | Query | JSON | 学术AI文档OCR识别 |
| https://wenku.baidu.com/scholarai/paper/detail/info | GET | Query | JSON | 学术AI论文详情信息 |
| https://wenku.baidu.com/scholarai/paper/getpapergraph | GET | Query | JSON | 学术AI论文获取论文图谱 |
| https://wenku.baidu.com/search | GET | Query | JSON | 百度文库文档搜索接口 |
| https://wenku.baidu.com/search/api/search | GET | Query | JSON | 搜索接口搜索 |
| https://wenku.baidu.com/search/api/userinfo | GET | Query | JSON | 搜索接口用户信息 |
| https://wenku.baidu.com/submit/addtips | POST | JSON | JSON | 提交新增提示 |
| https://wenku.baidu.com/usercenter/paper/search | GET | Query | JSON | 用户中心论文搜索 |

## 网页搜索与首页（36 条）

> 百度搜索主站首页、搜索结果、联想、通知、状态与全局配置接口

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.baidu.com/Security/Get | GET | Query | JSON | 安全获取 |
| https://www.baidu.com/TTS/Create | GET | Query | JSON | 语音合成创建 |
| https://www.baidu.com/api | GET | Query | JSON | 百度主站 API 统一入口 |
| https://www.baidu.com/api/account/bdVid | GET | Query | JSON | 接口账号百度VID |
| https://www.baidu.com/api/account/clickId | GET | Query | JSON | 接口账号点击ID |
| https://www.baidu.com/api/account/displayName | GET | Query | JSON | 接口账号显示名称 |
| https://www.baidu.com/api/account/judge_user_old_or_new | GET | Query | JSON | 接口账号新老用户判断 |
| https://www.baidu.com/api/account/qualify/info | POST | JSON | JSON | 接口账号资质信息 |
| https://www.baidu.com/api/account/status | POST | JSON | JSON | 接口账号状态 |
| https://www.baidu.com/api/account/v2/displayName | GET | Query | JSON | 接口账号v2显示名称 |
| https://www.baidu.com/api/health | GET | Query | JSON | 接口健康检查 |
| https://www.baidu.com/api/portal/leads | POST | JSON | JSON | 接口门户线索 |
| https://www.baidu.com/api/portal/track/behavior | POST | JSON | JSON | 接口门户追踪行为 |
| https://www.baidu.com/api/services/Accessibility | GET | Query | JSON | 接口无障碍 |
| https://www.baidu.com/api/unit/dialogue/query | GET | Query | JSON | 接口单元对话查询 |
| https://www.baidu.com/api/unit/questions | GET | Query | JSON | 接口单元问题 |
| https://www.baidu.com/data | GET | Query | JSON | 百度首页数据接口 |
| https://www.baidu.com/edit/content | GET | Query | JSON | 编辑内容 |
| https://www.baidu.com/feedcmp/inner1/list/wisevideo | GET | Query | JSON | 信息流组件内部列表视频信息流 |
| https://www.baidu.com/haokan/ui-web/relation/query | POST | JSON | JSON | 好看查询 |
| https://www.baidu.com/index.php/feedback/zx/getData | POST | JSON | JSON | 反馈在线 |
| https://www.baidu.com/notice/getinitdata | GET | Query | JSON | 百度首页通知初始化数据接口 |
| https://www.baidu.com/notice/getsystemnoticecount | GET | Query | JSON | 百度首页系统通知数量接口 |
| https://www.baidu.com/notice/setpopinfo | POST | JSON | JSON | 百度首页弹窗信息设置接口 |
| https://www.baidu.com/pae/common/api/feedback | GET | Query | JSON | 平台公共接口反馈 |
| https://www.baidu.com/pctts/report/report_audio_land_page | POST | JSON | JSON | PC语音上报语音落地页上报 |
| https://www.baidu.com/s | GET | Query | JSON | 百度搜索主接口（网页搜索结果页） |
| https://www.baidu.com/search | GET | Query | JSON | 百度搜索接口 |
| https://www.baidu.com/status/ | GET | Query | JSON | 百度用户登录状态查询接口 |
| https://www.baidu.com/submit/follow | POST | JSON | JSON | 提交关注 |
| https://www.baidu.com/submit/notice | POST | JSON | JSON | 提交通知 |
| https://www.baidu.com/suggest | GET | Query | JSON | 百度搜索建议（关键词联想）接口 |
| https://www.baidu.com/sugrec | GET | Query | JSON | 搜索建议 |
| https://www.baidu.com/user/nuc/message | GET | Query | JSON | 用户通知中心消息 |
| https://www.baidu.com/user/nucpage/message | GET | Query | JSON | 用户通知页消息 |
| https://www.baidu.com/utask/ajax/task | POST | JSON | JSON | 用户任务异步请求任务 |

## 百度学术（10 条）

> 百度学术论文检索、期刊、学者、引用与文献服务接口

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://xueshu.baidu.com/Security/Get | GET | Query | JSON | 安全获取 |
| https://xueshu.baidu.com/TTS/Create | GET | Query | JSON | 语音合成创建 |
| https://xueshu.baidu.com/api/services/Accessibility | GET | Query | JSON | 接口无障碍 |
| https://xueshu.baidu.com/batch-client-query/status | POST | JSON | JSON | 批量客户端查询状态 |
| https://xueshu.baidu.com/client | GET | Query | JSON | 百度学术客户端配置接口 |
| https://xueshu.baidu.com/data | GET | Query | JSON | 百度学术数据接口 |
| https://xueshu.baidu.com/status/ | GET | Query | JSON | 百度学术状态查询接口 |
| https://xueshu.baidu.com/v1/instance/price | GET | Query | JSON | v1实例价格 |
| https://xueshu.baidu.com/v1/token/create | POST | JSON | JSON | v1令牌创建 |
| https://xueshu.baidu.com/v3/live/session | GET | Query | JSON | v3直播会话 |

## 百度知道（36 条）

> 百度知道提问、回答、采纳、搜索、用户与任务相关接口

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://zhidao.baidu.com/api/comment | GET | Query | JSON | 接口评论 |
| https://zhidao.baidu.com/api/getOldUserLottery | GET | Query | JSON | 接口老用户抽奖 |
| https://zhidao.baidu.com/api/getReplyActInfo | GET | Query | JSON | 接口回复活动信息 |
| https://zhidao.baidu.com/api/gettag | POST | JSON | JSON | 接口获取标签 |
| https://zhidao.baidu.com/api/gettasksigninfo | GET | Query | JSON | 接口任务签到信息 |
| https://zhidao.baidu.com/api/gettinyurl | GET | Query | JSON | 接口获取短链 |
| https://zhidao.baidu.com/api/httpscheck | GET | Query | JSON | 百度知道 HTTPS 可用性检查接口 |
| https://zhidao.baidu.com/api/loginInfo | GET | Query | JSON | 接口登录信息 |
| https://zhidao.baidu.com/api/sendphonevcode | POST | JSON | JSON | 接口发送手机验证码 |
| https://zhidao.baidu.com/api/vcode | POST | JSON | JSON | 接口验证码 |
| https://zhidao.baidu.com/daily/search | GET | Query | JSON | 百度知道每日热搜/签到搜索接口 |
| https://zhidao.baidu.com/data | GET | Query | JSON | 百度知道数据接口 |
| https://zhidao.baidu.com/ihome | GET | Query | JSON | 个人首页 |
| https://zhidao.baidu.com/ihome/api/getvideoquestion | GET | Query | JSON | 个人首页接口获取视频提问 |
| https://zhidao.baidu.com/ihome/api/homepage | GET | Query | JSON | 个人首页接口首页 |
| https://zhidao.baidu.com/ihome/api/myanswer | GET | Query | JSON | 个人首页接口我的回答 |
| https://zhidao.baidu.com/ihome/api/myask | GET | Query | JSON | 个人首页接口我的提问 |
| https://zhidao.baidu.com/ihome/api/push | POST | JSON | JSON | 个人首页接口推送 |
| https://zhidao.baidu.com/ihome/api/signInfo | GET | Query | JSON | 个人首页接口签到信息 |
| https://zhidao.baidu.com/ihome/homepage/myitem | GET | Query | JSON | 个人首页首页我的条目 |
| https://zhidao.baidu.com/ihome/homepage/recommendquestion | GET | Query | JSON | 个人首页首页推荐问题 |
| https://zhidao.baidu.com/ihome/homepage/videoquestion | GET | Query | JSON | 个人首页首页视频提问 |
| https://zhidao.baidu.com/ihome/myitem/ucenter | GET | Query | JSON | 个人首页我的条目用户中心 |
| https://zhidao.baidu.com/ihome/set/profile | POST | JSON | JSON | 个人首页设置资料 |
| https://zhidao.baidu.com/list | GET | Query | JSON | 百度知道问题列表接口 |
| https://zhidao.baidu.com/search | GET | Query | JSON | 百度知道问答搜索接口 |
| https://zhidao.baidu.com/shop/lottery | GET | Query | JSON | 商城抽奖 |
| https://zhidao.baidu.com/submit | GET | Query | JSON | 百度知道提问/回答提交接口 |
| https://zhidao.baidu.com/submit/ | POST | JSON | JSON | 百度知道提交接口（带子路径） |
| https://zhidao.baidu.com/submit/ajax | POST | JSON | JSON | 提交异步请求 |
| https://zhidao.baidu.com/submit/ajax/ | GET | Query | JSON | 提交异步请求 |
| https://zhidao.baidu.com/submit/ajax/ | POST | JSON | JSON | 提交异步请求 |
| https://zhidao.baidu.com/submit/user | POST | JSON | JSON | 提交用户 |
| https://zhidao.baidu.com/task/api/getmytasklist | GET | Query | JSON | 任务接口获取任务列表 |
| https://zhidao.baidu.com/task/api/mytask | GET | Query | JSON | 任务接口我的任务 |
| https://zhidao.baidu.com/task/submit/getreward | POST | JSON | JSON | 任务提交获取奖励 |
| https://zhidao.baidu.com/task/submit/opentask | POST | JSON | JSON | 任务提交开启任务 |

## 全局朗读与无障碍组件（5 条）

> 百度全站通用朗读 / 高亮无障碍组件配置接口（多子域共享）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://www.baidu.com/data/settings | GET | Query | JSON | 数据设置 |
| https://www.baidu.com/highlight/enable | GET | Query | JSON | 高亮启用 |
| https://www.baidu.com/highlight/rate | GET | Query | JSON | 高亮比率 |
| https://www.baidu.com/voice/enable | GET | Query | JSON | 语音朗读启用 |
| https://www.baidu.com/voice/rate | GET | Query | JSON | 语音朗读比率 |
---

## 附：baidu.com 所属域名与子域名清单

抓取过程中在 JS/HTML 中共出现 **512 个百度系相关域名**（含 `baidu.com`/`baidu-int.com`/`bdstatic.com`/`bdimg.com`/`bcebos.com`/`baidustatic.com`/`baidu-img.cn` 等主域及其子域），其中出现次数 ≥3 的有 226 个。以下按"是否承载接口请求"分类。

### A. 承载接口请求的域名（业务 API 宿主，32 个）

| 域名 | 接口数 | 角色 |
| :--- | :--- | :--- |
| `cloud.baidu.com` | 171 | 百度智能云官网与控制台（价格计算器、千帆、生态、营销、备案、门户） |
| `fanyi.baidu.com` | 83 | 百度翻译 Web 端（在线翻译 / AIT 人工翻译 / MTPE 译文编辑） |
| `www.baidu.com` | 41 | 百度搜索主站（首页、结果、联想、通知、无障碍组件宿主） |
| `zhidao.baidu.com` | 36 | 百度知道（提问 / 回答 / 搜索 / 用户） |
| `wenku.baidu.com` | 24 | 百度文库（文档检索 / 下载 / 会员） |
| `baike.baidu.com` | 20 | 百度百科（词条 / 编辑 / 搜索） |
| `xueshu.baidu.com` | 10 | 百度学术（论文 / 期刊 / 学者 / ScholarAI） |
| `jingyan.baidu.com` | 10 | 百度经验（发布 / 评论 / 投票 / 收藏） |
| `anquan.baidu.com` | 3 | 百度安全中心（号码服务 / 活动奖品） |
| `image.baidu.com` | 2 | 百度图片（检索 / 联想） |
| `graph.baidu.com` | 2 | 百度识图（图像搜索 / 上传） |
| `v.baidu.com` | 2 | 百度视频（关注 / 作品） |
| `aifanfan.baidu.com` | 2 | 爱番番（线索 / 埋点） |
| `hku.baidu.com` | 2 | 高校活动 H5（分享） |
| `map.baidu.com` | 1 | 百度地图 Web 端（搜索建议、地图服务） |
| `api.map.baidu.com` | 1 | 百度地图开放平台 Web 服务 API 入口 |
| `passport.baidu.com` | 1 | 百度通行证（登录 / 渠道 / 用户信息） |
| `wappass.baidu.com` | 1 | 百度通行证安全验证（登录 / 验证码） |
| `suggestion.baidu.com` | 1 | 百度搜索建议独立域名（联想词） |
| `api.open.baidu.com` | 1 | 百度开放平台（HSUG 数据服务） |
| `fanyi-api.baidu.com` | 1 | 百度翻译开放 API（机器翻译 / 活动配置） |
| `market.baidu.com` | 1 | 百度营销平台 API 入口 |
| `mbd.baidu.com` | 1 | 百度信息流（feed 配置） |
| `chat.baidu.com` | 1 | 百度 AI 对话搜索 |
| `b2b.baidu.com` | 1 | 百度爱采购（商品搜索） |
| `hanyu.baidu.com` | 1 | 百度汉语（字词查询） |
| `m.baidu.com` | 1 | 百度搜索移动端（结果） |
| `f3.baidu.com` | 1 | 百度反馈服务（feedback） |
| `fclog.baidu.com` | 1 | 前端日志上报（埋点） |
| `ext.baidu.com` | 1 | 外部 ID 映射（设备标识） |
| `secr.baidu.com` | 1 | 安全风控组件资源 |
| `opendata.baidu.com` | 0 | 百度开放数据（本域接口受限未展开） |

**关键结论**：与小红书（单一 `edith.xiaohongshu.com` baseURL）不同，百度是**去中心化的多子域架构**——每个产品线（搜索、翻译、智能云、知道、百科、文库、学术、地图、账号）都是**独立前端工程 + 独立接口命名空间**，不存在统一 API 网关。因此接口宿主高度分散：`cloud.baidu.com`（智能云）172 条、`fanyi.baidu.com`（翻译）83 条、`www.baidu.com`（主站）41 条、`zhidao.baidu.com`（知道）34 条，长尾分布在 20+ 个子域。

### B. 资源 / 存储类子域名（静态 CDN，非 API，已排除）

- `*.bdstatic.com`（bce.bdstatic.com、pss.bdstatic.com、ms.bdstatic.com、fex.bdstatic.com、b.bdstatic.com、now.bdstatic.com、code.bdstatic.com、zz.bdstatic.com、ss.bdstatic.com、dss0-3.bdstatic.com、gss0.bdstatic.com、mbdp02.bdstatic.com、exp-new.bdstatic.com、avatar.bdstatic.com、hk.bdstatic.com、vd2.bdstatic.com、nd-static.bdstatic.com、ss0/ss1.bdstatic.com、static.tieba.baidu.com）
- `*.bdimg.com`（webmap0/webmap1.bdimg.com、wkstatic.bdimg.com、baikebcs.bdimg.com、iknow-zhidao.bdimg.com、himg.bdimg.com、mapsv0/mapsv1.bdimg.com、su.bdimg.com、ss.bdimg.com、ecmb.bdimg.com、bkssl.bdimg.com、passport.bdimg.com、edu-wenku.bdimg.com、bdimg.share.baidu.com、mpics.bdstatic.com）
- `*.cdn.bcebos.com` / `*.bj.bcebos.com`（psstatic.cdn.bcebos.com、fanyiapp.cdn.bcebos.com、fyb-2.cdn.bcebos.com、emoji.cdn.bcebos.com、iknow-pic.cdn.bcebos.com、fyb-pc-static.cdn.bcebos.com、ppui-static-pc.cdn.bcebos.com、fanyi-cdn.cdn.bcebos.com、news-bos.cdn.bcebos.com、search-operate.cdn.bcebos.com、iknow-base.cdn.bcebos.com、contentcms-bj.cdn.bcebos.com、aisearch.cdn.bcebos.com、ndstatic.cdn.bcebos.com、exp-picture.cdn.bcebos.com、edu-public.cdn.bcebos.com、iknow-base.bj.bcebos.com、bce-cdn.bj.bcebos.com、mms-graph.cdn.bcebos.com、staticsns.cdn.bcebos.com、fe-prod.cdn.bcebos.com、fanyitest.cdn.bcebos.com、baozhang/wyw-pic.cdn.bcebos.com）
- `*.hiphotos.baidu.com`（a-h.hiphotos、hiphotos，图片对象存储）、`pic.rmb.bdstatic.com`、`cdn01/cdn00.baidu-img.cn`、`baidustatic.com`/`cpro.baidustatic.com`（联盟广告静态）

### C. 统计 / 埋点 / 测试与内网域名（已排除）

- `hm.baidu.com`（百度统计站点统计）、`nsclick.baidu.com`/`sclick.baidu.com`/`eclick.baidu.com`（点击与转化追踪）、`fclog.baidu.com`/`log.news.baidu.com`（日志上报）、`cbjs.baidu.com`（cj 联盟脚本）、`dlswbr.baidu.com`（下载组件）、`sestat.baidu.com`、`kstj.baidu.com`、`wkctj.baidu.com`、`s.share.baidu.com`/`bdimg.share.baidu.com`（分享组件）
- `*.bcetest.baidu.com`（qasandbox.bcetest、login.bcetest、cloudtest.baidu.com、bcetest.baidu.com）、`*.baidu-int.com`（intl.cloudtest.baidu-int.com、yapi.baidu-int.com、intl.cloud.baidu-int.com）、`2fwww.baidu.com`/`2fm.baidu.com`（内网代理前缀）、`bjyz-mco-*`/`szzj-qilin-*`（机房内网域名）、`fanyitest.cdn.bcebos.com`（测试环境）
- 第三方站点：`renren.com`、`weibo.com`、`connect.qq.com`、`*.douyin`、`*.meituan`、`*.xiaodutv`（分享与登录外链，非百度接口）

### D. 其他百度系业务子域（本次抓取未覆盖或无独立 Web API）

`tieba.baidu.com`（贴吧，抓取时被安全验证拦截）、`pan.baidu.com`（网盘，仅取到 analytics）、`news.baidu.com`（新闻）、`top.baidu.com`（热搜榜）、`haokan.baidu.com`（好看视频）、`music.baidu.com`（音乐）、`yiyan.baidu.com`（文心一言早期入口）、`baijiahao.baidu.com`（百家号）、`map.` 系列（`sp0-sp3.baidu.com`、`f7.baidu.com`、`gsp0.baidu.com` 为搜索结果静态节点）、`bce.baidu.com`/`console.bce.baidu.com`/`login.bce.baidu.com`（智能云控制台与登录，接口经 `cloud.baidu.com` 代理）、`qianfanmarket.baidu.com`/`appbuilder.cloud.baidu.com`（千帆市场与 AppBuilder）、`developer.baidu.com`/`open.baidu.com`（开放平台文档站）、`help.baidu.com`/`cas.baidu.com`/`anquan.baidu.com`（客服与安全）等，或为纯静态/跳转页，或被反爬拦截，未纳入接口表。

---

## 二、统计汇总

| 分类 | 接口数 |
| :--- | :--- |
| 开放平台·统计与日志 | 11 |
| 图片·经验·视频等垂直产品 | 27 |
| 百度百科 | 20 |
| 智能云·门户内容与运营 | 43 |
| 智能云·生态合作与开发者 | 32 |
| 智能云·账号与基础服务 | 8 |
| 智能云·价格计算器与产品 | 41 |
| 智能云·域名与备案 | 7 |
| 智能云·营销运营与活动 | 31 |
| 智能云·千帆大模型 | 9 |
| 百度翻译 | 25 |
| 百度翻译·人工翻译(AIT) | 34 |
| 百度翻译·译文编辑(MTPE) | 24 |
| 登录与账号安全 | 2 |
| 百度地图 | 1 |
| 百度文库与学术AI | 24 |
| 网页搜索与首页 | 36 |
| 百度学术 | 10 |
| 百度知道 | 36 |
| 全局朗读与无障碍组件 | 5 |
| **合计（唯一宿主+路径）** | **426** |

### 按维度统计

| 项目 | 数值 |
| :--- | :--- |
| 唯一接口（宿主 + 路径） | **426 条** |
| 接口表格条目（含同路径多方法） | 428 条 |
| 不同 URL 路径（跨子域去重） | 398 条 |
| 承载接口的业务子域 | 32 个 |
| 抓取 JS bundle | 157 个主包（约 41 MB）+ 47 个异步 chunk（约 3.6 MB）+ 各页内联脚本，共 204 个文件 |
| 方法分布 | GET 301、POST 127、PUT 0、DELETE 0 |
| 方法证据精度 | 约六成由调用点 `.get()/.post()`/`method:"xxx"` 直接证据还原，其余按路径语义推断 |

### 接口数 Top 子域

| 子域 | 接口数 |
| :--- | :--- |
| `cloud.baidu.com` | 171 |
| `fanyi.baidu.com` | 83 |
| `www.baidu.com` | 41 |
| `zhidao.baidu.com` | 36 |
| `wenku.baidu.com` | 24 |
| `baike.baidu.com` | 20 |
| `jingyan.baidu.com` | 10 |
| `xueshu.baidu.com` | 10 |
| `anquan.baidu.com` | 3 |
| `aifanfan.baidu.com` | 2 |
| `graph.baidu.com` | 2 |
| `hku.baidu.com` | 2 |

---

> **免责声明**：本文档为基于前端 JS 静态逆向整理的接口清单，用途为技术研究与文档归档，不代表对任一接口发起真实调用或用于自动化批量抓取。百度各子域接口随版本迭代频繁变动，路径、参数、鉴权方式可能已调整；部分方法为语义推断，实际以线上为准。抓取受反爬（"百度安全验证"）限制，贴吧、开放数据等子域未能完整覆盖。
