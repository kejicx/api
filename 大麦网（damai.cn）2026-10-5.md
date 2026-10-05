# 大麦网 API 接口文档

> 站点：大麦网（damai.cn）— 阿里系现场娱乐票务平台（演出 / 赛事 / 电影 / 剧本杀 / 本地生活 / 票夹）
> 抓取时间：2026-10-05
> 抓取范围：`www.damai.cn` / `m.damai.cn` / `search.damai.cn` / `detail.damai.cn` / `passport.damai.cn` 首页与移动端 `shows` 业务页 → 前端 bundle 三线（`damai/pc/1.0.55`、`damai/vue-pc/0.0.85`、`alipay-movie-client/show-h5-next/2026.09.23`，均在 `g.alicdn.com`）→ 全量下载 **96 个 URL / 101 个 JS 文件（约 7.4 MB）**
> 提取方式：无 sourcemap。大麦接口走**阿里 mtop H5 网关**，以请求配置 `requestOptions:{api:"mtop.xxx",v:"x.x"}` 中的 `api` 字面量为锚点正则提取，回溯调用点 `method:`/`type:` 还原 HTTP 方法（**16 条直接证据、290 条按 api 末段语义推断**）
> **接口总数：306 条**（唯一 mtop 接口，网关路径 `https://mtop.damai.cn/h5/{api}/{v}/`）
> 方法分布：GET 197 / POST 109　｜　业务大类：20 类
> 说明：本文档为**静态逆向前端 JS bundle** 整理所得，非大麦官方公开 API；mtop 接口调用需阿里系签名鉴权（见通用约定），仅作技术研究与接口梳理参考。

---

## 一、通用约定

| 项目 | 说明 |
| :--- | :--- |
| 主 API 入口 | `https://mtop.damai.cn/h5/{api名}/{版本号v}/`，如 `…/h5/mtop.damai.wireless.search.search/1.0/` |
| 备用 mtop 网关 | `acs.m.taobao.com` / `h5api.m.taobao.com` / `guide-acs.m.taobao.com`（阿里统一无线网关，同 `/h5/{api}/{v}/` 形态） |
| 接口命名空间 | 绝大多数为 `mtop.damai.wireless.*`（214 条），另有 `mtop.film.*`（淘票票线）、`mtop.damai.item/trade/mec/general`、`mtop.alibaba.damai.*` 等 |
| 鉴权 | 阿里 mtop 签名：Cookie `_m_h5_tk`/`_m_h5_tk_enc` + 请求参数 `jsv`/`appKey`/`t`(时间戳)/`sign`(MD5)；登录态 Cookie `cookie2`/`_tb_token_`/`unb`，大麦侧 `damai.cn` 会话 |
| 请求格式 | `GET` 走 `data` 序列化到 Query；`POST` 走 JSON body（`Content-Type: application/x-www-form-urlencoded`，data 字段为 JSON 串） |
| 返回格式 | JSON 统一信封：`{"api":"mtop.xxx","v":"1.0","ret":["SUCCESS::调用成功"],"data":{…}}`；`ret` 首元素为 `错误码::描述` |
| 常见错误码 | `SUCCESS`、`FAIL_SYS_TOKEN_EMPTY`(令牌空)、`FAIL_SYS_TOKEN_EXOIRED`(令牌过期)、`FAIL_SYS_ILLEGAL_ACCESS`(签名校验失败)、`FAIL_SYS_TRAFFIC_LIMIT`(限流)、`FAIL_BIZ_*`(业务错误) |
| 防刷 | SDK 内置 `antiCreepRequest`/`antiFloodRequest`（防爬/防刷限流），触发返回 `FAIL_SYS_*` 需重试或降级 |
| 路径变量 | 表中 `{api}` 为接口全名、`{v}` 为版本号，均为运行时固定字面量，已按实际值展开 |

**请求方法与语义对照**：`GET`=查询读取、`POST`=创建/提交/触发动作。mtop 网关对多数读写接口统一走 `POST`，此处按调用点证据与 api 末段语义标注实际方法。

## 搜索与发现（15 条）

> 关键词搜索、智能联想、分类频道、榜单推荐、发现页与播报

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://mtop.damai.cn/h5/mtop.damai.mec.aristotle.get/1.0/ | GET | Query | JSON | 内容电商-智能推荐引擎-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.discovery.detail.get/1.6/ | GET | Query | JSON | 发现-详情-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.discovery.detail.task/1.0/ | POST | JSON | JSON | 发现-详情-任务 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.discovery.locallife.detail.get/1.2/ | GET | Query | JSON | 发现-本地生活-详情-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.pay.success.recommend.get/1.0/ | GET | Query | JSON | 支付-成功-推荐-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.search.baccount.search/1.0/ | GET | Query | JSON | 搜索-商家账号-搜索 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.search.broadcast.home/1.0/ | GET | Query | JSON | 搜索-播报-首页 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.search.broadcast.list/1.0/ | GET | Query | JSON | 搜索-播报-列表 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.search.cms.category.get/2.0/ | GET | Query | JSON | 搜索-内容管理-类目-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.search.discovery.bycity.get/1.0/ | GET | Query | JSON | 搜索-发现-按城市-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.search.performance.calendar.get/1.0/ | GET | Query | JSON | 搜索-演出-日历-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.search.project.classify/1.0/ | GET | Query | JSON | 搜索-项目-分类 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.search.projectlist.byrecommend.get/1.0/ | GET | Query | JSON | 搜索-项目列表-按推荐-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.search.search/1.0/ | GET | Query | JSON | 搜索 |
| https://mtop.damai.cn/h5/mtop.item.detail.recommend.all/1.1/ | GET | Query | JSON | 项目-详情-推荐-全部 |

## 项目与演出详情（9 条）

> 项目/演出/场次详情页、票价计算、动态信息、支持项目

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://mtop.damai.cn/h5/mtop.alibaba.damai.detail.getdetail/1.2/ | GET | Query | JSON | 详情-获取详情 |
| https://mtop.damai.cn/h5/mtop.alibaba.damai.detail.getdetail.center/1.2/ | GET | Query | JSON | 详情-获取详情 |
| https://mtop.damai.cn/h5/mtop.alibaba.detail.subpage.getdetail/2.0/ | GET | Query | JSON | 详情-子页面-获取详情 |
| https://mtop.damai.cn/h5/mtop.damai.general.detail.getdetail/1.0/ | GET | Query | JSON | 通用-详情-获取详情 |
| https://mtop.damai.cn/h5/mtop.damai.item.calcTicketPrice/2.0/ | GET | Query | JSON | 项目-计算票价 |
| https://mtop.damai.cn/h5/mtop.damai.item.detail.getdetail/1.0/ | GET | Query | JSON | 项目-详情-获取详情 |
| https://mtop.damai.cn/h5/mtop.damai.item.projectdetail.projectid.get/1.1/ | GET | Query | JSON | 项目-项目详情-项目ID-获取 |
| https://mtop.damai.cn/h5/mtop.damai.item.tips.information/1.0/ | GET | Query | JSON | 项目-提示-信息 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.group.project.list/1.0/ | GET | Query | JSON | 群组-项目-列表 |

## 选座与座位（11 条）

> 座位图、场次座位状态查询、选座锁座与销毁

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://mtop.damai.cn/h5/mtop.alibaba.distribution.seat.token.check/1.0/ | GET | Query | JSON | 分销-座位-令牌-校验 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.comment.seatview.info.get/1.0/ | GET | Query | JSON | 评论-座位图-信息-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.blindbox.confirmlock/1.0/ | POST | JSON | JSON | 订单-盲盒-确认锁座 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.remindchooseseat/1.0/ | GET | Query | JSON | 订单-提醒选座 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.seat.calcticketprice/1.0/ | GET | Query | JSON | 座位-计算票价 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.seat.distribution.dynamicinfo/1.0/ | GET | Query | JSON | 座位-分销-动态信息 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.seat.distribution.getB2B2CAreaInfo/1.0/ | GET | Query | JSON | 座位-分销-获取B2B2C区域信息 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.seat.distribution.queryperformseatstatus/1.0/ | GET | Query | JSON | 座位-分销-查询场次座位状态 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.seat.dynamicInfo/1.0/ | GET | Query | JSON | 座位-动态信息 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.seat.precheck/1.0/ | GET | Query | JSON | 座位-预校验 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.seat.queryperformseatstatus/1.0/ | GET | Query | JSON | 座位-查询场次座位状态 |

## 交易下单与订单（28 条）

> 下单构建、订单创建、发起支付、订单列表与详情、物流进度

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://mtop.damai.cn/h5/mtop.damai.item.purchaseguide/1.0/ | GET | Query | JSON | 项目-购买指南 |
| https://mtop.damai.cn/h5/mtop.damai.trade.order.adjust/1.0/ | POST | JSON | JSON | 交易-订单-调整 |
| https://mtop.damai.cn/h5/mtop.damai.trade.order.adjust.h5/1.0/ | POST | JSON | JSON | 交易-订单-调整 |
| https://mtop.damai.cn/h5/mtop.damai.trade.order.build/1.0/ | POST | JSON | JSON | 交易-订单-构建 |
| https://mtop.damai.cn/h5/mtop.damai.trade.order.build.h5/1.0/ | POST | JSON | JSON | 交易-订单-构建 |
| https://mtop.damai.cn/h5/mtop.damai.trade.order.create/1.0/ | POST | JSON | JSON | 交易-订单-创建 |
| https://mtop.damai.cn/h5/mtop.damai.trade.order.create.h5/1.0/ | POST | JSON | JSON | 交易-订单-创建 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.configure.paysuccess.get/1.0/ | GET | Query | JSON | 配置-支付完成-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.localife.paysuccess.get/1.0/ | GET | Query | JSON | 本地生活-支付完成-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.cancelorder/2.0/ | POST | JSON | JSON | 订单-取消订单 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.orderdetail/2.0/ | GET | Query | JSON | 订单-订单详情 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.orderlist/2.0/ | GET | Query | JSON | 订单-订单列表 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.orderlist.get/1.0/ | GET | Query | JSON | 订单-订单列表-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.orderpayparam/2.0/ | POST | JSON | JSON | 订单-订单支付参数 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.orderprogress/1.0/ | GET | Query | JSON | 订单-订单进度 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.paymentCompletion/1.0/ | POST | JSON | JSON | 订单-支付 完成 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.paymentCompletion4Secondary/1.0/ | POST | JSON | JSON | 订单-二次订单支付完成 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.querylogisticsdetail/2.0/ | GET | Query | JSON | 订单-查询物流详情 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.remind.common/1.0/ | GET | Query | JSON | 订单-提醒-通用 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.reminddelivery/1.0/ | POST | JSON | JSON | 订单-提醒发货 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.secondaryorderlist/1.0/ | GET | Query | JSON | 订单-二次订单列表 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.transferinfo.get/1.0/ | GET | Query | JSON | 订单-转赠信息-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.secondaryorder.orderdetail/1.0/ | GET | Query | JSON | 二次订单-订单详情 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.trade.common.order.confirm/1.0/ | POST | JSON | JSON | 交易-通用-订单-确认 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.trade.common.order.create/1.0/ | POST | JSON | JSON | 交易-通用-订单-创建 |
| https://mtop.damai.cn/h5/mtop.order.dopay/4.0/ | POST | JSON | JSON | 订单-发起支付 |
| https://mtop.damai.cn/h5/mtop.order.queryboughtlist/4.0/ | GET | Query | JSON | 订单-查询已购列表 |
| https://mtop.damai.cn/h5/mtop.purchase.guide.tourist.special/1.1/ | GET | Query | JSON | 购买-指南-游客-特殊 |

## 退款与售后（14 条）

> 退款申请、非票退款、退款保险、放弃订单

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.blindbox.abandon/1.0/ | POST | JSON | JSON | 订单-盲盒-放弃 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.refund.apply/2.0/ | POST | JSON | JSON | 订单-退款-申请 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.refund.check/1.0/ | GET | Query | JSON | 订单-退款-校验 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.refund.create/1.0/ | POST | JSON | JSON | 订单-退款-创建 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.refund.createNonTicketRefund/1.0/ | POST | JSON | JSON | 订单-退款-创建非票退款 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.refund.logistics/1.0/ | GET | Query | JSON | 订单-退款-物流 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.refund.nonTicketRender/1.0/ | POST | JSON | JSON | 订单-退款-非票渲染 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.refund.progress/1.0/ | GET | Query | JSON | 订单-退款-进度 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.refund.render/1.0/ | GET | Query | JSON | 订单-退款-渲染 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.refund.submit/1.0/ | POST | JSON | JSON | 订单-退款-提交 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.refundinsurance.auth/1.0/ | POST | JSON | JSON | 订单-退款保险-鉴权 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.refund.destory.ticket.submit/1.0/ | POST | JSON | JSON | 退款-销毁-票-提交 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.trade.common.order.refund.render/1.0/ | GET | Query | JSON | 交易-通用-订单-退款-渲染 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.waiting.order.refund.create/1.0/ | POST | JSON | JSON | 等待-订单-退款-创建 |

## 票夹与凭证（27 条）

> 电子票夹、转赠、撤回、二维码/人脸核验、数字藏品与电子纪念品

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet.comment.get/1.0/ | GET | Query | JSON | 票夹-评论-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet.face.unbinding/1.0/ | POST | JSON | JSON | 票夹-人脸-解绑 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet.gift.get/1.0/ | GET | Query | JSON | 票夹-礼品-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet.gift.receive/1.0/ | GET | Query | JSON | 票夹-礼品-领取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet.gift.valid/1.0/ | GET | Query | JSON | 票夹-礼品-有效 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet2.extension.exchangesite.list/1.0/ | GET | Query | JSON | 票夹-扩展-兑换点-列表 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet2.extension.list/1.0/ | GET | Query | JSON | 票夹-扩展-列表 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet2.extension.notice.query/1.0/ | GET | Query | JSON | 票夹-扩展-公告-查询 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet2.forgot.card.get/1.0/ | GET | Query | JSON | 票夹-找回-卡片-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet2.nft.prepareIssue/1.0/ | GET | Query | JSON | 票夹-数字藏品-出票准备 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet2.perform.detail.get/1.0/ | GET | Query | JSON | 票夹-场次-详情-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet2.perform.detail.get.bind/1.0/ | POST | JSON | JSON | 票夹-场次-详情-获取-绑定 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet2.perform.detail.qrcode/1.0/ | GET | Query | JSON | 票夹-场次-详情-二维码 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet2.perform.my.detail.get/1.0/ | GET | Query | JSON | 票夹-场次-我的-详情-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet2.performs.get/1.0/ | GET | Query | JSON | 票夹-场次-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet2.performs.history.get/1.0/ | GET | Query | JSON | 票夹-场次-历史-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet2.performs.watched/1.0/ | GET | Query | JSON | 票夹-场次-已观看 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet2.souvenir.detail.get/1.0/ | GET | Query | JSON | 票夹-电子纪念品-详情-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet2.transfer.accept/1.0/ | POST | JSON | JSON | 票夹-转赠-接受 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet2.transfer.accept.query/1.0/ | POST | JSON | JSON | 票夹-转赠-接受-查询 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet2.transfer.cancel/1.0/ | POST | JSON | JSON | 票夹-转赠-取消 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet2.transfer.grant/1.0/ | POST | JSON | JSON | 票夹-转赠-发放 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet2.transfer.query/1.0/ | GET | Query | JSON | 票夹-转赠-查询 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet2.wechat.notify/1.0/ | GET | Query | JSON | 微信·票夹-微信-通知 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet2.withdraw.order.detail/1.0/ | GET | Query | JSON | 票夹-撤回-订单-详情 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet2.withdraw.perform.detail/1.0/ | GET | Query | JSON | 票夹-撤回-场次-详情 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ticklet2.withdraw.user.confirm/1.0/ | POST | JSON | JSON | 票夹-撤回-用户-确认 |

## 发票（5 条）

> 发票申请、查询、提交、重发与提醒

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://mtop.damai.cn/h5/mtop.damai.wireless.invoice.applyInvoice/2.0/ | GET | Query | JSON | 发票-申请发票 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.invoice.queryInvoiceInfo/2.0/ | GET | Query | JSON | 发票-查询发票信息 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.invoice.submitInvoiceApplication/2.0/ | GET | Query | JSON | 发票-提交发票申请 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.remindInvoice/1.0/ | GET | Query | JSON | 订单-提醒开具发票 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.resendInvoice/1.0/ | GET | Query | JSON | 订单-重发发票 |

## 评论与互动（38 条）

> 评论发布/点赞/删除/举报、内容信息流、话题主题、想看收藏、投票

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://mtop.damai.cn/h5/mtop.damai.content.item.video.live.get/1.0/ | GET | Query | JSON | 内容-项目-视频-直播-获取 |
| https://mtop.damai.cn/h5/mtop.damai.mec.popup.report/1.0/ | POST | JSON | JSON | 内容电商-弹窗-上报 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.comment.batch.praise/1.0/ | POST | JSON | JSON | 评论-批量-点赞 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.comment.content.report/1.0/ | POST | JSON | JSON | 评论-内容-上报 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.comment.content.reportreason/1.0/ | GET | Query | JSON | 评论-内容-举报原因 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.comment.delete/1.0/ | POST | JSON | JSON | 评论-删除 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.comment.head.get/1.0/ | GET | Query | JSON | 评论-头部-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.comment.list.get/3.3/ | GET | Query | JSON | 评论-列表-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.comment.module.get/1.1/ | GET | Query | JSON | 评论-模块-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.comment.praise/1.0/ | POST | JSON | JSON | 评论-点赞 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.comment.publish/2.1/ | POST | JSON | JSON | 评论-发布 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.comment.publisher.render/1.0/ | GET | Query | JSON | 评论-发布者-渲染 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.comment.query.theme/1.1/ | GET | Query | JSON | 评论-查询-主题 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.comment.render/1.2/ | GET | Query | JSON | 评论-渲染 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.comment.success.render/1.0/ | GET | Query | JSON | 评论-成功-渲染 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.content.hotspot.get/1.0/ | GET | Query | JSON | 内容-热点-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.content.hotspot.pop/1.0/ | GET | Query | JSON | 内容-热点-弹窗 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.content.hotspot.user.get/1.0/ | GET | Query | JSON | 内容-热点-用户-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.discovery.detail.wanna.getrecommendinfo/1.1/ | GET | Query | JSON | 发现-详情-想看-获取推荐信息 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.discovery.publish.check/1.1/ | GET | Query | JSON | 发现-发布-校验 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.discovery.theme.project.get/1.0/ | GET | Query | JSON | 发现-主题-项目-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.locallife.comment.batch.praise/1.0/ | POST | JSON | JSON | 本地生活-评论-批量-点赞 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.locallife.comment.delete/1.0/ | POST | JSON | JSON | 本地生活-评论-删除 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.locallife.comment.list.get/1.1/ | GET | Query | JSON | 本地生活-评论-列表-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.locallife.comment.module.get/1.0/ | GET | Query | JSON | 本地生活-评论-模块-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.locallife.comment.praise/1.0/ | POST | JSON | JSON | 本地生活-评论-点赞 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.locallife.comment.publish/1.0/ | POST | JSON | JSON | 本地生活-评论-发布 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.locallife.comment.success.render/1.0/ | GET | Query | JSON | 本地生活-评论-成功-渲染 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.share.popup/1.0/ | GET | Query | JSON | 分享-弹窗 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.themenew.recommend/1.0/ | GET | Query | JSON | 新主题-推荐 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.topic.search/1.0/ | GET | Query | JSON | 话题-搜索 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.feedback.add/1.0/ | POST | JSON | JSON | 用户-反馈-添加 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.feedback.bizIdentifiers/1.0/ | POST | JSON | JSON | 用户-反馈-业务标识 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.feedback.replayDetail/1.0/ | POST | JSON | JSON | 用户-反馈-回放 详情 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.feedback.replayList/1.0/ | POST | JSON | JSON | 用户-反馈-回放 列表 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.my.content.get/1.0/ | POST | JSON | JSON | 用户-我的-内容-获取 |
| https://mtop.damai.cn/h5/mtop.item.detail.content.feed/1.0/ | GET | Query | JSON | 项目-详情-内容-信息流 |
| https://mtop.damai.cn/h5/mtop.purchase.guide.city.feed/1.0/ | GET | Query | JSON | 购买-指南-城市-信息流 |

## 关注与订阅（14 条）

> 关注关系、订阅提醒、会员关系

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://mtop.damai.cn/h5/mtop.damai.wireless.dailyremindapi.relationdailyremind/1.0/ | POST | JSON | JSON | 每日提醒接口-关联每日提醒 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.follow.relation.book.cancel/1.0/ | POST | JSON | JSON | 关注-关系-预约-取消 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.follow.relation.follow.list/1.3/ | GET | Query | JSON | 关注-关系-关注-列表 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.follow.relation.target.get/1.2/ | GET | Query | JSON | 关注-关系-目标-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.follow.relation.update/1.2/ | POST | JSON | JSON | 关注-关系-更新 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.follow.relation.update.batch/1.1/ | POST | JSON | JSON | 关注-关系-更新-批量 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.follow.relation.update.v2/1.2/ | POST | JSON | JSON | 关注-关系-更新 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.locallife.message.template.subscribe/1.0/ | POST | JSON | JSON | 本地生活-消息-模板-订阅 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.search.account.relationship.list/1.0/ | GET | Query | JSON | 搜索-账号-关系-列表 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.template.subscribe/1.0/ | POST | JSON | JSON | 模板-订阅 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.theme.follow.relation.update/1.0/ | POST | JSON | JSON | 主题-关注-关系-更新 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.tpp.follow.relation.target.get/1.0/ | GET | Query | JSON | tpp-关注-关系-目标-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.tpp.follow.relation.update/1.0/ | POST | JSON | JSON | tpp-关注-关系-更新 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.tpp.follow.relation.update.batch/1.0/ | POST | JSON | JSON | tpp-关注-关系-更新-批量 |

## 用户中心与账号授权（36 条）

> 用户资料、收货地址、实名认证、登录会话、第三方授权绑定、隐私与反馈

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://mtop.damai.cn/h5/mtop.damai.mxm.user.accesstoken.getbytbs/1.0/ | GET | Query | JSON | 麦座消息-用户-访问令牌-通过淘宝获取 |
| https://mtop.damai.cn/h5/mtop.damai.mxm.user.token.get/1.0/ | GET | Query | JSON | 麦座消息-用户-令牌-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.alipay.open.api.oauthcode/1.0/ | POST | JSON | JSON | 支付宝·支付宝-开通-OAuth授权码 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.baccount.getbindinfo/1.0/ | GET | Query | JSON | 商家账号-获取绑定信息 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.baccount.submitbind/1.0/ | POST | JSON | JSON | 商家账号-提交绑定 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.dailyremindapi.querydailyremindbyuserid/1.0/ | GET | Query | JSON | 每日提醒接口-按用户ID查询每日提醒 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.damai.release.account/1.0/ | GET | Query | JSON | 发布-账号 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.open.api.oauth.redirect/1.0/ | GET | Query | JSON | 开通-OAuth授权-跳转 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.queryselfmachineaddress/1.0/ | POST | JSON | JSON | 订单-查询自助机地址 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.script.store.certificationinfo.get/1.0/ | GET | Query | JSON | 剧本-门店-认证信息-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.canVerifyCustomers.list/1.0/ | GET | Query | JSON | 用户-可核验客户-列表 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.certification.queryStatus/1.0/ | POST | JSON | JSON | 用户-认证-查询状态 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.certification.submit/2.0/ | POST | JSON | JSON | 用户-认证-提交 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.customer.add/2.0/ | POST | JSON | JSON | 用户-客户-添加 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.customer.addcheck/1.0/ | POST | JSON | JSON | 用户-客户-添加核验 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.customer.countries/1.0/ | POST | JSON | JSON | 用户-客户-国家 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.customer.remove/1.0/ | POST | JSON | JSON | 用户-客户-移除 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.customer.typelist/1.0/ | GET | Query | JSON | 用户-客户-类型列表 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.customer.update/2.0/ | POST | JSON | JSON | 用户-客户-更新 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.customerlist.get/2.0/ | GET | Query | JSON | 用户-客户列表-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.islogin/1.0/ | GET | Query | JSON | 用户-是否登录 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.permission.getlist/1.0/ | GET | Query | JSON | 用户-权限-获取列表 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.permission.setlist/1.0/ | POST | JSON | JSON | 用户-权限-设置列表 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.phone.allowable.query/1.0/ | GET | Query | JSON | 用户-手机-可允许-查询 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.service.token.validate/1.0/ | POST | JSON | JSON | 用户-服务-令牌-验证 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.session.transform/1.0/ | POST | JSON | JSON | 用户-会话-转换 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.shippingaddress.add/1.0/ | POST | JSON | JSON | 用户-收货地址-添加 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.shippingaddress.del/1.0/ | POST | JSON | JSON | 用户-收货地址-删除 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.shippingaddress.getUserAddressList/1.0/ | GET | Query | JSON | 用户-收货地址-获取用户地址列表 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.shippingaddress.modify/1.0/ | POST | JSON | JSON | 用户-收货地址-修改 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.shippingaddress.setDefaultAddress/1.0/ | POST | JSON | JSON | 用户-收货地址-设置默认地址 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.user.third.session.get/1.0/ | GET | Query | JSON | 用户-第三方-会话-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.venue.photo.user/1.0/ | GET | Query | JSON | 场馆-照片-用户 |
| https://mtop.damai.cn/h5/mtop.idle.user.simple/1.0/ | GET | Query | JSON | 闲鱼·闲鱼-用户-简要 |
| https://mtop.damai.cn/h5/mtop.taobao.authorize.hasqwenbinding/1.0/ | POST | JSON | JSON | 淘宝·授权-是否绑定通义千问 |
| https://mtop.damai.cn/h5/mtop.user.getUserSimple/1.0/ | GET | Query | JSON | 用户-获取用户简要信息 |

## AI智能助手（18 条）

> 先锋 AI、智能对话与历史、智能推荐引擎

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ai.chat.history.query/1.1/ | GET | Query | JSON | AI-对话-历史-查询 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ai.chat.history.querybysessionidandtraceid/1.0/ | GET | Query | JSON | AI-对话-历史-按会话ID与追踪ID查询 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ai.pageConfig/1.0/ | GET | Query | JSON | AI-页面配置 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ai.search/1.0/ | GET | Query | JSON | AI-搜索 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.comment.ai.praise/1.0/ | POST | JSON | JSON | 评论-AI-点赞 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.comment.ai.query/1.0/ | GET | Query | JSON | 评论-AI-查询 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.pioneer.ai.artist.hot/1.0/ | GET | Query | JSON | 先锋-AI-艺人-热门 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.pioneer.ai.feedback/1.0/ | POST | JSON | JSON | 先锋-AI-反馈 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.pioneer.ai.input/1.0/ | POST | JSON | JSON | 先锋-AI-输入 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.pioneer.ai.preset.word/1.0/ | GET | Query | JSON | 先锋-AI-预设-词 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.pioneer.ai.remind.up.seatpic/1.0/ | GET | Query | JSON | 先锋-AI-提醒-上-座位图 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.pioneer.ai.render.v2/1.0/ | GET | Query | JSON | 先锋-AI-渲染 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.pioneer.ai.report.chat/1.0/ | POST | JSON | JSON | 先锋-AI-上报-对话 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.pioneer.ai.seat.view.get/1.0/ | GET | Query | JSON | 先锋-AI-座位-查看-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.pioneer.ai.suggest.get/1.0/ | GET | Query | JSON | 先锋-AI-搜索建议-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.pioneer.health.detail/1.0/ | GET | Query | JSON | 先锋-健康-详情 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.pioneer.health.get/1.0/ | GET | Query | JSON | 先锋-健康-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.pioneer.health.open/1.0/ | POST | JSON | JSON | 先锋-健康-开通 |

## 优惠券与营销活动（11 条）

> 优惠券领取与查询、权益、盲盒、抽奖、拼单、团购券、活动

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://mtop.damai.cn/h5/mtop.damai.groupcoupon.detail.getdetail/1.0/ | GET | Query | JSON | 团购券-详情-获取详情 |
| https://mtop.damai.cn/h5/mtop.damai.groupcoupon.supportproject.getsupportprojects/1.0/ | GET | Query | JSON | 团购券-支持项目-获取支持项目 |
| https://mtop.damai.cn/h5/mtop.damai.mtopfcodeapi.bindCouponRealNameInfo/1.0/ | POST | JSON | JSON | 兑换码接口-绑定优惠券实名信息 |
| https://mtop.damai.cn/h5/mtop.damai.spliceorder.create/1.0/ | POST | JSON | JSON | 拼单-创建 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.activity.vote/2.0/ | POST | JSON | JSON | 活动-投票 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.channel.page.prize.draw/1.0/ | POST | JSON | JSON | 渠道-页面-奖品-抽取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.mkt.coupon.applyCoupon4User/1.0/ | GET | Query | JSON | 营销-优惠券-为用户领取优惠券 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.mkt.coupon.queryCouponActsOfItem/1.0/ | GET | Query | JSON | 营销-优惠券-查询项目优惠券活动 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.mkt.privilege.verifyandsigncode/1.0/ | POST | JSON | JSON | 营销-特权-核验并签名码 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.order.blindbox.queryblindboxorderdetail/1.0/ | POST | JSON | JSON | 订单-盲盒-查询盲盒订单详情 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.spliceorder.cancel/1.0/ | POST | JSON | JSON | 拼单-取消 |

## 城市与场馆（10 条）

> 城市列表、行政区划、场馆、地区、景点、IP 定位

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://mtop.damai.cn/h5/mtop.damai.maitix.division.getdisivionchildrenspecial/1.0/ | GET | Query | JSON | 麦座-行政区划-获取特殊行政区划子级 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.area.groupcity/1.0/ | GET | Query | JSON | 区域-城市群 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.cities.location.parse/1.0/ | GET | Query | JSON | 城市-位置-解析 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.cities.query/1.0/ | GET | Query | JSON | 城市-查询 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.ipLocation.query/1.0/ | GET | Query | JSON | IP 位置-查询 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.other.ipLocation/1.1/ | GET | Query | JSON | 其它-IP 位置 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.project.getb2b2careainfo/1.3/ | GET | Query | JSON | 项目-获取B2B2C区域信息 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.venue.info/2.0/ | GET | Query | JSON | 场馆-信息 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.venue.more.info/1.0/ | GET | Query | JSON | 场馆-更多-信息 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.venue.photo.official/1.0/ | GET | Query | JSON | 场馆-照片-官方 |

## 消息通知（1 条）

> 站内消息、公告、通知、每日提醒

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://mtop.damai.cn/h5/mtop.damai.item.notice.query/1.0/ | GET | Query | JSON | 项目-公告-查询 |

## 媒体与图片（2 条）

> 视频、直播、图片上传

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://mtop.damai.cn/h5/mtop.damai.artiste.home.pic/1.0/ | GET | Query | JSON | 艺人-首页-图片 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.video.upload.sign/1.0/ | POST | JSON | JSON | 视频-上传-签到 |

## 配置与页面渲染（2 条）

> 页面配置、模板模块、动态渲染、弹窗与扩展

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://mtop.damai.cn/h5/mtop.damai.mec.popup.get/1.0/ | GET | Query | JSON | 内容电商-弹窗-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.configure.msite.list/1.0/ | GET | Query | JSON | 配置-移动站-列表 |

## 本地生活与周边（6 条）

> 本地生活、附近门店、剧本杀、粉丝周边、出行旅游、追剧卡

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://mtop.damai.cn/h5/mtop.damai.fandommerch.detail.getdetail/1.0/ | GET | Query | JSON | 粉丝周边-详情-获取详情 |
| https://mtop.damai.cn/h5/mtop.damai.item.detail.scriptkill.getdetail/1.0/ | GET | Query | JSON | 项目-详情-剧本杀-获取详情 |
| https://mtop.damai.cn/h5/mtop.damai.travel.ticket.detail.getdetail/1.0/ | GET | Query | JSON | 出行-票-详情-获取详情 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.map.scenicspot.nearby/1.0/ | GET | Query | JSON | 地图-景点-附近 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.map.scenicspot.rendering/1.0/ | GET | Query | JSON | 地图-景点-渲染 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.script.store.detail/1.0/ | GET | Query | JSON | 剧本-门店-详情 |

## 艺人与其它业务组件（2 条）

> 艺人主页、分销、内容电商、麦座、艺术等垂直组件

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://mtop.damai.cn/h5/mtop.damai.wireless.channel.artiste/1.0/ | GET | Query | JSON | 渠道-艺人 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.guide.shorturl.get/1.0/ | GET | Query | JSON | 指南-短链接-获取 |

## 淘票票(film)业务线（50 条）

> 淘票票订单、任务、抽奖、拼单、地区、想看、签到、签名、口令分享、颁奖等

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://mtop.damai.cn/h5/mtop.film.MtopLuckyDrawAPI.batchLotteryDraw/9.3/ | POST | JSON | JSON | 淘票票·film-幸运抽奖接口-批量抽奖 |
| https://mtop.damai.cn/h5/mtop.film.MtopLuckyDrawAPI.getBatchQualificationResult/9.0/ | POST | JSON | JSON | 淘票票·film-幸运抽奖接口-获取批量资格结果 |
| https://mtop.damai.cn/h5/mtop.film.MtopLuckyDrawAPI.newLotteryDraw/9.0/ | POST | JSON | JSON | 淘票票·film-幸运抽奖接口-新抽奖 |
| https://mtop.damai.cn/h5/mtop.film.MtopMindAPI.modifyUserPrivacy/8.6/ | POST | JSON | JSON | 淘票票·film-想看接口-修改用户隐私 |
| https://mtop.damai.cn/h5/mtop.film.MtopOrderAPI.closeUnpayOrder/5.0/ | POST | JSON | JSON | 淘票票·film-订单接口-关闭未支付订单 |
| https://mtop.damai.cn/h5/mtop.film.MtopOrderAPI.delHistoryOrder/9.0/ | POST | JSON | JSON | 淘票票·film-订单接口-删除历史订单 |
| https://mtop.damai.cn/h5/mtop.film.MtopOrderAPI.getBizOrders/5.0/ | GET | Query | JSON | 淘票票·film-订单接口-获取业务订单 |
| https://mtop.damai.cn/h5/mtop.film.MtopOrderAPI.getSaleOrderDetail/7.8/ | GET | Query | JSON | 淘票票·film-订单接口-获取销售订单详情 |
| https://mtop.damai.cn/h5/mtop.film.MtopRegionAPI.getNewAllRegion/1.0/ | GET | Query | JSON | 淘票票·film-地区接口-获取全部新区域 |
| https://mtop.damai.cn/h5/mtop.film.MtopRegionAPI.getRegion/4.0/ | GET | Query | JSON | 淘票票·film-地区接口-获取地区 |
| https://mtop.damai.cn/h5/mtop.film.MtopRegionAPI.getRegionByCode/1.0/ | GET | Query | JSON | 淘票票·film-地区接口-按编码获取区域 |
| https://mtop.damai.cn/h5/mtop.film.MtopSaleAPI.closeUnpaySaleOrder/7.8/ | POST | JSON | JSON | 淘票票·film-销售接口-关闭未支付销售订单 |
| https://mtop.damai.cn/h5/mtop.film.MtopSignAPI.damaiSignIn/1.0/ | GET | Query | JSON | 淘票票·film-签到接口-大麦签到 |
| https://mtop.damai.cn/h5/mtop.film.MtopSignatureAPI.getWeixinSignature/1.0/ | GET | Query | JSON | 淘票票·film-签名接口-获取微信签名 |
| https://mtop.damai.cn/h5/mtop.film.MtopSpliceOrderAPI.getFinishUsers/1.0/ | GET | Query | JSON | 淘票票·film-拼单接口-获取完成用户 |
| https://mtop.damai.cn/h5/mtop.film.MtopSpliceOrderAPI.getSpliceOrderInviteDetail/1.0/ | GET | Query | JSON | 淘票票·film-拼单接口-获取拼单邀请详情 |
| https://mtop.damai.cn/h5/mtop.film.MtopSpliceOrderAPI.listSpliceOrder/1.0/ | GET | Query | JSON | 淘票票·film-拼单接口-拼单列表 |
| https://mtop.damai.cn/h5/mtop.film.MtopSpliceOrderAPI.openSpliceOrder/1.0/ | POST | JSON | JSON | 淘票票·film-拼单接口-开通拼单 |
| https://mtop.damai.cn/h5/mtop.film.MtopTaskAPI.assistTask/1.0/ | POST | JSON | JSON | 淘票票·film-任务接口-协助任务 |
| https://mtop.damai.cn/h5/mtop.film.MtopTaskAPI.completeTask/1.0/ | POST | JSON | JSON | 淘票票·film-任务接口-完成任务 |
| https://mtop.damai.cn/h5/mtop.film.MtopTaskAPI.queryTaskBanner/1.0/ | GET | Query | JSON | 淘票票·film-任务接口-查询任务横幅 |
| https://mtop.damai.cn/h5/mtop.film.MtopTaskAPI.queryUserTaskByBizId/1.0/ | GET | Query | JSON | 淘票票·film-任务接口-按业务ID查询用户任务 |
| https://mtop.damai.cn/h5/mtop.film.MtopTaskAPI.queryUserTaskModule/9.4/ | GET | Query | JSON | 淘票票·film-任务接口-查询用户任务模块 |
| https://mtop.damai.cn/h5/mtop.film.MtopTaskAPI.reportTask/1.0/ | POST | JSON | JSON | 淘票票·film-任务接口-上报任务 |
| https://mtop.damai.cn/h5/mtop.film.MtopUserAPI.getUserSession/8.2/ | GET | Query | JSON | 淘票票·film-用户接口-获取用户会话 |
| https://mtop.damai.cn/h5/mtop.film.MtopWatchwordShareAPI.generateWatchword/1.0/ | GET | Query | JSON | 淘票票·film-口令分享接口-生成口令 |
| https://mtop.damai.cn/h5/mtop.film.life.aristotle.get/3.0/ | GET | Query | JSON | 淘票票·film-生活-智能推荐引擎-获取 |
| https://mtop.damai.cn/h5/mtop.film.mtopmindapi.changeshowwantstatus/7.7/ | POST | JSON | JSON | 淘票票·film-想看接口-变更演出想看状态 |
| https://mtop.damai.cn/h5/mtop.film.mtopuserapi.getUserProfilesByPhone/1.0/ | GET | Query | JSON | 淘票票·film-用户接口-按手机号获取用户资料 |
| https://mtop.damai.cn/h5/mtop.film.mtopuserapi.getminiuserprofile/9.0/ | GET | Query | JSON | 淘票票·film-用户接口-获取迷你用户资料 |
| https://mtop.damai.cn/h5/mtop.film.notify.subscribe.query/1.0/ | GET | Query | JSON | 淘票票·film-通知-订阅-查询 |
| https://mtop.damai.cn/h5/mtop.film.order.item.confirmitemorder/1.0/ | POST | JSON | JSON | 淘票票·film-订单-项目-确认商品订单 |
| https://mtop.damai.cn/h5/mtop.film.order.item.create/1.0/ | POST | JSON | JSON | 淘票票·film-订单-项目-创建 |
| https://mtop.damai.cn/h5/mtop.film.order.item.paperticketcustom.render/1.0/ | GET | Query | JSON | 淘票票·film-订单-项目-纸质票自定义-渲染 |
| https://mtop.damai.cn/h5/mtop.film.order.item.paperticketcustomtext.submit/1.0/ | POST | JSON | JSON | 淘票票·film-订单-项目-纸质票自定义文本-提交 |
| https://mtop.damai.cn/h5/mtop.film.order.item.unionitemorderdetail.dramacard.marketing/1.0/ | GET | Query | JSON | 淘票票·film-订单-项目-联合项目订单详情-追剧卡-营销 |
| https://mtop.damai.cn/h5/mtop.film.order.item.unionitemorderdetail.get/1.0/ | GET | Query | JSON | 淘票票·film-订单-项目-联合项目订单详情-获取 |
| https://mtop.damai.cn/h5/mtop.film.order.item.unionitemorders.get/1.0/ | GET | Query | JSON | 淘票票·film-订单-项目-联合项目订单-获取 |
| https://mtop.damai.cn/h5/mtop.film.pfusercenter.collect.detail.get/1.0/ | GET | Query | JSON | 淘票票·film-用户中心-收藏-详情-获取 |
| https://mtop.damai.cn/h5/mtop.film.pfusercenter.collect.logindetail.get/1.0/ | GET | Query | JSON | 淘票票·film-用户中心-收藏-登录详情-获取 |
| https://mtop.damai.cn/h5/mtop.film.pfusercenter.collect.statistics.get/1.0/ | GET | Query | JSON | 淘票票·film-用户中心-收藏-统计-获取 |
| https://mtop.damai.cn/h5/mtop.film.pfusercenter.guide.static.page.get.damai/1.0/ | GET | Query | JSON | 淘票票·film-用户中心-指南-静态-页面-获取 |
| https://mtop.damai.cn/h5/mtop.film.pfusercenter.profit.queryUserPerformDefinedTicketTips/1.0/ | POST | JSON | JSON | 淘票票·film-用户中心-权益-查询用户场次自定义票提示 |
| https://mtop.damai.cn/h5/mtop.film.pfusercenter.profit.subscription.create/1.0/ | POST | JSON | JSON | 淘票票·film-用户中心-权益-订阅-创建 |
| https://mtop.damai.cn/h5/mtop.film.pfusercenter.profit.subscription.delete/1.0/ | POST | JSON | JSON | 淘票票·film-用户中心-权益-订阅-删除 |
| https://mtop.damai.cn/h5/mtop.film.pfusercenter.profit.userDefinedTicketTips/1.0/ | POST | JSON | JSON | 淘票票·film-用户中心-权益-用户自定义票提示 |
| https://mtop.damai.cn/h5/mtop.film.pfusercenter.relation.queryMemberRelation/1.0/ | POST | JSON | JSON | 淘票票·film-用户中心-关系-查询会员关系 |
| https://mtop.damai.cn/h5/mtop.film.tfoscar.agendaAPI.queryAgendaScheduleIds/2.0/ | GET | Query | JSON | 淘票票·film-淘票票颁奖-日程-查询日程场次ID |
| https://mtop.damai.cn/h5/mtop.film.user.appToken.get/1.0/ | GET | Query | JSON | 淘票票·film-用户-应用 令牌-获取 |
| https://mtop.damai.cn/h5/mtop.film.user.token.get/1.0/ | GET | Query | JSON | 淘票票·film-用户-令牌-获取 |

## 其它与工具（7 条）

> 短链接、统计、健康、通用工具接口

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://mtop.damai.cn/h5/mtop.damai.blind.box.query/1.0/ | GET | Query | JSON | 盲盒-盒子-查询 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.badge.mark/1.0/ | POST | JSON | JSON | 徽章-标记 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.behavelist/1.0/ | GET | Query | JSON | 行为列表 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.channel.page.tasks.get/1.0/ | GET | Query | JSON | 渠道-页面-任务-获取 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.damai.release.check/1.0/ | GET | Query | JSON | 发布-校验 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.getfootprinthistory/1.0/ | GET | Query | JSON | 获取足迹历史 |
| https://mtop.damai.cn/h5/mtop.damai.wireless.my.getfootsummaryrecord/1.0/ | GET | Query | JSON | 我的-获取足迹汇总记录 |
---

## 附：大麦网所属域名与子域名清单

> 从 306 个 JS bundle 中提取到 200 个大麦/阿里系域名，按用途分组如下。

### A. 承载 API 接口（mtop 无线网关，路径 `/h5/{api}/{v}/`）

| 域名 | 说明 |
| :--- | :--- |
| `mtop.damai.cn` | 大麦主 mtop 网关（本文档接口入口，已验证返回标准 mtop 信封） |
| `acs.m.taobao.com` | 阿里统一无线 API 网关（App/H5 通用） |
| `h5api.m.taobao.com` | H5 mtop 网关 |
| `guide-acs.m.taobao.com` | 导购链路 mtop 网关 |
| `acs.m.tmall.com` / `h5api.m.tmall.com` | 天猫侧 mtop 网关（部分电商组件复用） |

### B. 承载页面与业务服务（前端站点 / 业务子域）

**B-1 大麦自有站点（28 个）**：`www.damai.cn`（PC 首页）、`m.damai.cn`（移动端，重定向壳 `/damai/home`）、`search.damai.cn`（搜索）、`detail.damai.cn`（项目详情）、`t.damai.cn`（评价/社区）、`tickets.damai.cn`（票务）、`passport.damai.cn` / `ipassport.damai.cn`（登录鉴权）、`pages.damai.cn`、`mobile.damai.cn`、`help.damai.cn`、`faas.damai.cn`、`perico.damai.cn`、`intercms.damai.cn`（内容管理）、`sentiment.damai.cn`（舆情）、`ticklet.damai.cn`（票夹）、`mx-venue.damai.cn`（场馆）、`mx-partner-admin.damai.cn`（商家后台）、`mec-comment.damai.cn`（评论）、`comment-cdn.damai.cn`、`item-cdn.damai.cn`、`assets.damai.cn`、`pimg.damai.cn`、`usercenter-bgimg.damai.cn`、`finance-upload.damai.cn` 等：

代表（按引用频次）：`ipassport.damai.cn`、`m.damai.cn`、`t.damai.cn`、`search.damai.cn`、`pimg.damai.cn`、`intercms.damai.cn`、`perico.damai.cn`、`assets.damai.cn`、`comment-cdn.damai.cn`、`sentiment.damai.cn`、`mx-partner-admin.damai.cn`、`mec-comment.damai.cn`、`finance-upload.damai.cn`、`ticklet.damai.cn`、`usercenter-bgimg.damai.cn`、`item-cdn.damai.cn`、`mx-venue.damai.cn`、`tickets.damai.cn`、`detail.damai.cn`、`p.damai.cn`

**B-2 阿里生态跳转/资源/CDN（97 个）**：CDN 静态资源 `g.alicdn.com` / `gw.alicdn.com` / `img.alicdn.com` / `assets.alicdn.com` / `aeis.alicdn.com` / `liangcang-material.alicdn.com`（前端 bundle 与图片均在此）；业务跳转 `market.m.taobao.com`（营销活动页）、`h5.m.taobao.com`、`new.m.taobao.com`、`main.m.taobao.com`、`login.taobao.com`（登录）、`dianying.taobao.com`（电影/淘票票）、`huodong.taobao.com`（活动）、`tpp-act.taobao.com`（淘票票活动）、`fourier.taobao.com`、`jump.taobao.com`（跳转）、`item.taobao.com`、`cart.taobao.com`、`trade.taobao.com`、`refund.m.taobao.com`、`einvoice.taobao.com`（发票）、`wuliu.taobao.com`（物流）等：

代表（按引用频次）：`gw.alicdn.com`、`g.alicdn.com`、`img.alicdn.com`、`market.m.taobao.com`、`dianying.taobao.com`、`havanalogin.taobao.com`、`jump.taobao.com`、`tpp-act.taobao.com`、`huodong.taobao.com`、`fourier.taobao.com`、`liangcang-material.alicdn.com`、`h5.m.taobao.com`、`pages-fast.m.taobao.com`、`m.intl.taobao.com`、`item.taobao.com`、`assets.alicdn.com`、`aeis.alicdn.com`、`detail.i56.taobao.com`

### C. 测试 / 预发 / 监控 / 内网 / 品牌店铺（已排除，不收录接口）

- 预发与测试环境：`*.wapa.damai.cn`、`*.waptest.taobao.com`、`pre-*`（pre-t/pre-tickets/pre-e/pre-faas/pre-market1~5/pre-pages-fast/pre-tpp-act）等灰度域名
- 阿里监控/数据/广告：`subway.simba.taobao.com`（广告）、`sycm.taobao.com`（生意参谋）、`holmes.taobao.com`、`healthcenter.taobao.com`、`insight.tmall.com`、`databank.tmall.com`、`strategy.tmall.com`、`bigsale.tmall.com`、`crm/ecrm/alicrm`、`onetalk`、`translate/sourcing/profile/post/biz.data/cashier.alibaba.com`（国际站）、`*.aliyun.com`（控制台/通义/千问等）
- 品牌旗舰店与第三方店铺：`adidas/anta/bosideng/decathlon.tmall.com`、`shop数字.taobao.com`、`xiangqing.wangpu.taobao.com` 等（非大麦接口）
- 合计排除 70 个域名

---

## 二、统计汇总

| 统计维度 | 数值 |
| :--- | :--- |
| 接口总数（唯一 mtop 接口） | 306 |
| 业务大类 | 20 |
| GET / POST | 197 / 109 |
| 请求格式 Query / JSON | 197 / 109 |
| 方法证据来源（直接 / 语义推断） | 16 / 290 |
| 主版本号 1.0 占比 | 247 / 306 |
| 提取文件数（JS bundle） | 101 |
| 发现域名数（大麦/阿里系） | 200 |

**按接口命名空间分布**：

| 命名空间 | 接口数 |
| :--- | :--- |
| `mtop.damai.wireless` | 214 |
| `mtop.film（淘票票）` | 50 |
| `mtop.damai.*（其它）` | 16 |
| `其它命名空间` | 9 |
| `mtop.damai.item` | 7 |
| `mtop.damai.trade` | 6 |
| `mtop.alibaba.*` | 4 |

**按业务大类分布**：

| 业务大类 | 接口数 |
| :--- | :--- |
| 淘票票(film)业务线 | 50 |
| 评论与互动 | 38 |
| 用户中心与账号授权 | 36 |
| 交易下单与订单 | 28 |
| 票夹与凭证 | 27 |
| AI智能助手 | 18 |
| 搜索与发现 | 15 |
| 关注与订阅 | 14 |
| 退款与售后 | 14 |
| 选座与座位 | 11 |
| 优惠券与营销活动 | 11 |
| 城市与场馆 | 10 |
| 项目与演出详情 | 9 |
| 其它与工具 | 7 |
| 本地生活与周边 | 6 |
| 发票 | 5 |
| 媒体与图片 | 2 |
| 配置与页面渲染 | 2 |
| 艺人与其它业务组件 | 2 |
| 消息通知 | 1 |

**版本号分布（Top）**：`1.0`×247、`2.0`×19、`1.1`×11、`1.2`×7、`9.0`×4、`4.0`×3、`1.3`×2、`5.0`×2、`7.8`×2、`3.3`×1

> **免责声明**：本文档基于对大麦网（damai.cn）公开可访问的前端 JavaScript 代码做静态分析整理而成，接口清单反映的是前端代码中出现的 mtop 调用，不代表大麦官方对外承诺的公开 API，亦未包含仅在服务端或已登录/风控态下才暴露的接口。mtop 接口需阿里系签名鉴权方可调用，本文档仅作技术研究与接口体系梳理参考，请勿用于任何未授权的商业抓取或违反大麦用户协议的行为。大麦为阿里系多产品线聚合（含淘票票 film 线），部分接口实际由淘宝/天猫网关承载。
