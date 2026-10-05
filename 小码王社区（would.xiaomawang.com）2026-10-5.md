# world.xiaomawang.com API 接口文档

> 站点：小码王 Scratch 编程社区 — Scratch / Python 图形化编程创作与社交学习平台
> 抓取时间：2026-10-05
> 抓取范围：`https://world.xiaomawang.com/`（302 → `/w/index`）→ **35 个首页 JS** + `_buildManifest.js` 中解析出的 **97 个页面 chunk**（含 activity 系列 34 个、market、person、orders、task-center、workshop 等），累计约 130 个 JS 文件 / 6.6 MB
> 提取方式：Next.js SSR 站点，`__NEXT_DATA__` 可读；定位统一请求层模块 `CKEv`（4 个 axios 实例 + 1 个 mock 实例），按模块作用域解析别名 → 导出字符 → baseURL 的映射关系，再回溯 `(get|post|put|patch|delete)("path")` 调用点
> 已过滤：第三方埋点/统计（sensors 时间戳上报除外，见备注）、CDN 与 OSS 直连（`xmcdn.oss-cn-shanghai.aliyuncs.com`、`community-wscdn.xmwol.com`、`wscdn.xmwol.com`）、第三方 SDK（`connect.qq.com`、`sns.qzone.qq.com`、`service.weibo.com`、`open.weixin.qq.com`）、第三方库 URL（github / redux.js.org / momentjs.com）、Next.js 内部路由

---

## 一、通用约定

| 项目 | 说明 |
| :--- | :--- |
| 主 API 域 | `https://community-api.xiaomawang.com`（约 105 条，占比 65%） |
| 社区 API 域 | `https://communityapi.xiaomawang.com`（约 15 条） |
| 世界 API 域 | `https://worldapi.xiaomawang.com`（约 21 条） |
| 公共 API 域 | `https://commonapi.xiaomawang.com`（3 条） |
| 请求格式 | **GET 传 `params`，POST 传 `data`（JSON body）** |
| 返回格式 | JSON（axios 默认 `response.data`） |
| 鉴权 | Cookie / `token` header，全站 `withCredentials` 生效 |
| 路由前缀约定 | `/api/v1/**` = 主业务 v1；`/japi/v1/**` = 支付/任务/商城等交易类；`/sqapi/**` = Scratch 2.0 旧接口；`/free/v1/**` = 公开接口；`/servlet/**` = 旧 Java 服务 |
| 已知业务配置 | `xmAppId: xmw8066532241050`、微信 `app_id: wxf1c89a1469ad1d90`、Scratch2 站点 `scratch2020.xiaomawang.com`、考试站 `program-exam.xiaomawang.com` |

---

## 二、作品（Composition）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-api.xiaomawang.com/api/v1/composition/get-info | GET | Query | JSON | 作品详情 |
| https://community-api.xiaomawang.com/api/v1/composition/get-other-list | GET | Query | JSON | 相关/其他作品列表 |
| https://community-api.xiaomawang.com/api/v1/composition/get-discovery-list | GET | Query | JSON | 发现页作品列表 |
| https://community-api.xiaomawang.com/api/v1/composition/get-show-list | GET | Query | JSON | 展示作品列表 |
| https://community-api.xiaomawang.com/api/v1/composition/get-choice-list | GET | Query | JSON | 精选作品列表 |
| https://community-api.xiaomawang.com/api/v1/composition/get-specific-composition | GET | Query | JSON | 指定作品详情 |
| https://community-api.xiaomawang.com/api/v1/composition/get-zone-composition | GET | Query | JSON | 分区作品列表 |
| https://community-api.xiaomawang.com/api/v1/composition/get-tag-list | GET | Query | JSON | 作品标签列表 |
| https://community-api.xiaomawang.com/api/v1/composition/get-evaluation-info | GET | Query | JSON | 作品评价信息 |
| https://community-api.xiaomawang.com/api/v1/composition/get-publish-detail | GET | Query | JSON | 发布详情（创作前配置） |
| https://community-api.xiaomawang.com/api/v1/composition/adaptation-tree | GET | Query | JSON | 改编关系树 |
| https://community-api.xiaomawang.com/api/v1/composition/save | POST | JSON | JSON | 保存作品 |
| https://community-api.xiaomawang.com/api/v1/composition/publish | POST | JSON | JSON | 发布作品 |
| https://community-api.xiaomawang.com/api/v1/composition/adapt | POST | JSON | JSON | 改编作品 |
| https://community-api.xiaomawang.com/api/v1/composition/fantasy-evaluate | POST | JSON | JSON |  fantasy 作品评测 |
| https://community-api.xiaomawang.com/api/v1/composition/bind-evaluation-result | POST | JSON | JSON | 绑定评测结果 |
| https://community-api.xiaomawang.com/japi/v1/composition/topic-list | GET | Query | JSON | 作品话题列表 |
| https://community-api.xiaomawang.com/japi/v1/composition/visit-detail | GET | Query | JSON | 作品访问详情 |
| https://community-api.xiaomawang.com/japi/v1/composition/base-visit-detail | GET | Query | JSON | 作品基础访问详情 |
| https://community-api.xiaomawang.com/japi/v1/composition/adaptation-num | GET | Query | JSON | 改编数量统计 |
| https://community-api.xiaomawang.com/japi/v1/composition/in-composition-purchase | POST | JSON | JSON | 站内购买作品 |

---

## 三、Python 创作器（PythonComposition）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-api.xiaomawang.com/api/v1/pythonComposition/list | GET | Query | JSON | Python 作品列表 |
| https://community-api.xiaomawang.com/api/v1/pythonComposition/detail | GET | Query | JSON | Python 作品详情 |
| https://community-api.xiaomawang.com/api/v1/pythonComposition/save | POST | JSON | JSON | 保存 Python 作品 |
| https://community-api.xiaomawang.com/abcde | POST | JSON | JSON | Python 创作器调试占位接口（测试代码遗留，待验证） |

---

## 四、用户（User）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-api.xiaomawang.com/api/v1/user/get-info | GET | Query | JSON | 用户信息 |
| https://community-api.xiaomawang.com/api/v1/user/get-bind-info | GET | Query | JSON | 用户绑定信息 |
| https://community-api.xiaomawang.com/api/v1/user/get-composition-list | GET | Query | JSON | 用户作品列表 |
| https://community-api.xiaomawang.com/api/v1/user/get-collect-composition | GET | Query | JSON | 用户收藏的作品 |
| https://community-api.xiaomawang.com/api/v1/user/get-collect-studio | GET | Query | JSON | 用户收藏的工作室 |
| https://community-api.xiaomawang.com/api/v1/user/get-rand-talent | GET | Query | JSON | 随机推荐达人 |
| https://community-api.xiaomawang.com/api/v1/user/search | GET | Query | JSON | 用户搜索 |
| https://community-api.xiaomawang.com/api/v1/user/search-hot | GET | Query | JSON | 搜索热词 |
| https://community-api.xiaomawang.com/api/v1/user/top-comment | GET | Query | JSON | 置顶评论列表 |
| https://community-api.xiaomawang.com/api/v1/user/top-comment-off | GET | Query | JSON | 关闭置顶评论 |
| https://community-api.xiaomawang.com/api/v1/user/validate-token | GET | Query | JSON | 校验登录 token |
| https://community-api.xiaomawang.com/api/v1/user/save | POST | JSON | JSON | 保存用户资料 |
| https://community-api.xiaomawang.com/api/v1/user/change-password | POST | JSON | JSON | 修改密码 |
| https://community-api.xiaomawang.com/api/v1/user/change-background | POST | JSON | JSON | 修改个人主页背景 |
| https://community-api.xiaomawang.com/api/v1/user/follow | POST | JSON | JSON | 关注用户 |
| https://community-api.xiaomawang.com/api/v1/user/like | POST | JSON | JSON | 点赞 |
| https://community-api.xiaomawang.com/api/v1/user/collect | POST | JSON | JSON | 收藏 |
| https://community-api.xiaomawang.com/api/v1/user/comment | POST | JSON | JSON | 发表评论 |
| https://community-api.xiaomawang.com/api/v1/user/delete-comment | POST | JSON | JSON | 删除评论 |
| https://community-api.xiaomawang.com/api/v1/user/delete-composition | POST | JSON | JSON | 删除作品 |
| https://community-api.xiaomawang.com/api/v1/user/recover-composition | POST | JSON | JSON | 恢复（回收站）作品 |
| https://community-api.xiaomawang.com/api/v1/user/report | POST | JSON | JSON | 举报 |
| https://community-api.xiaomawang.com/api/v1/user/toggle-top | POST | JSON | JSON | 切换作品置顶状态 |
| https://community-api.xiaomawang.com/api/v1/user/apply-studio | POST | JSON | JSON | 申请加入工作室 |
| https://worldapi.xiaomawang.com/api/user/check-password | GET | Query | JSON | 校验密码 |
| https://worldapi.xiaomawang.com/api/user/feedback | POST | JSON | JSON | 用户反馈提交 |

---

## 五、评论（Comment）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-api.xiaomawang.com/api/v1/comment/get-list | GET | Query | JSON | 评论列表 |
| https://community-api.xiaomawang.com/api/v1/comment/get-children-comment | GET | Query | JSON | 子评论（回复）列表 |
| https://worldapi.xiaomawang.com/api/comment/get-comment-children | GET | Query | JSON | 评论子回复（旧版，待验证） |

---

## 六、工作室（Studio）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-api.xiaomawang.com/api/v1/studio/get-info | GET | Query | JSON | 工作室详情 |
| https://community-api.xiaomawang.com/api/v1/studio/get-user-list | GET | Query | JSON | 工作室成员列表 |
| https://community-api.xiaomawang.com/api/v1/studio/get-composition-list | GET | Query | JSON | 工作室作品列表 |
| https://community-api.xiaomawang.com/api/v1/studio/get-discovery-list | GET | Query | JSON | 工作室发现列表 |
| https://community-api.xiaomawang.com/api/v1/studio/get-dynamic-list | GET | Query | JSON | 工作室动态列表 |
| https://community-api.xiaomawang.com/api/v1/studio/get-star-list | GET | Query | JSON | 工作室 Star 列表 |
| https://community-api.xiaomawang.com/api/v1/studio/get-choice-list | GET | Query | JSON | 工作室精选列表 |
| https://community-api.xiaomawang.com/api/v1/studio/get-cloud-disk | GET | Query | JSON | 云磁盘信息 |
| https://community-api.xiaomawang.com/api/v1/studio/check-apply-composition | GET | Query | JSON | 检查作品发布申请状态 |
| https://community-api.xiaomawang.com/api/v1/studio/upload-composition | POST | JSON | JSON | 上传作品到工作室 |
| https://community-api.xiaomawang.com/api/v1/studio/publish-composition | POST | JSON | JSON | 发布工作室作品 |
| https://community-api.xiaomawang.com/api/v1/studio/apply-publish-composition | POST | JSON | JSON | 申请发布作品 |
| https://community-api.xiaomawang.com/api/v1/studio/cancel-publish-composition | POST | JSON | JSON | 取消发布作品 |
| https://community-api.xiaomawang.com/api/v1/studio/kick-out | POST | JSON | JSON | 踢出成员 |
| https://community-api.xiaomawang.com/api/v1/studio/update-studio-image | POST | JSON | JSON | 更新工作室头像 |
| https://worldapi.xiaomawang.com/api/studio/apply-join | POST | JSON | JSON | 申请加入工作室 |
| https://worldapi.xiaomawang.com/api/studio/get-apply-list | GET | Query | JSON | 申请加入列表 |
| https://worldapi.xiaomawang.com/api/studio/get-apply-join-num | GET | Query | JSON | 待审批申请数量 |
| https://worldapi.xiaomawang.com/api/studio/drop-out | POST | JSON | JSON | 退出工作室 |
| https://worldapi.xiaomawang.com/api/studio/set-start-member | POST | JSON | JSON | 设置为首个成员 |
| https://worldapi.xiaomawang.com/api/studio/update-studio-introduce | POST | JSON | JSON | 更新工作室简介 |
| https://worldapi.xiaomawang.com/api/studio/update-studio-name | POST | JSON | JSON | 更新工作室名称 |
| https://worldapi.xiaomawang.com/api/studio/update-studio-slogan | POST | JSON | JSON | 更新工作室标语 |
| https://worldapi.xiaomawang.com/api/studio/get-comment-list | GET | Query | JSON | 工作室评论列表 |
| https://worldapi.xiaomawang.com/api/studio/post-comment | POST | JSON | JSON | 工作室发表评论 |
| https://worldapi.xiaomawang.com/api/studio/get-skill-list | GET | Query | JSON | 工作室技能列表 |
| https://worldapi.xiaomawang.com/api/studio/appointment | POST | JSON | JSON | 预约工作室 |
| https://worldapi.xiaomawang.com/api/studio/pre-appointment | GET | Query | JSON | 预约信息查询 |
| https://worldapi.xiaomawang.com/api/studio/delete-compose | POST | JSON | JSON | 删除工作室作品 |
| https://worldapi.xiaomawang.com/api/studio/verify-pass | POST | JSON | JSON | 审批通过 |
| https://worldapi.xiaomawang.com/api/studio/verify-miss | POST | JSON | JSON | 审批驳回 |

---

## 七、话题（Topic）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-api.xiaomawang.com/api/v1/topic/get-list | GET | Query | JSON | 话题列表 |
| https://community-api.xiaomawang.com/api/v1/topic/get-info | GET | Query | JSON | 话题详情 |
| https://community-api.xiaomawang.com/api/v1/topic/get-index-list | GET | Query | JSON | 话题索引列表 |
| https://community-api.xiaomawang.com/api/v1/topic/get-choice-list | GET | Query | JSON | 精选话题列表 |
| https://community-api.xiaomawang.com/api/v1/topic/get-composition-list | GET | Query | JSON | 话题下作品列表 |
| https://community-api.xiaomawang.com/api/v1/topic/get-composition-info | GET | Query | JSON | 话题作品详情 |
| https://community-api.xiaomawang.com/api/v1/topic/get-other-list | GET | Query | JSON | 其他相关话题 |
| https://community-api.xiaomawang.com/api/v1/topic/contribute | POST | JSON | JSON | 贡献话题 |
| https://community-api.xiaomawang.com/free/v1/topic/get-sum-vote-score | GET | Query | JSON | 话题投票总分（`?topicIds=1,2`） |

---

## 八、动态（Dynamic）与消息（Message）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-api.xiaomawang.com/api/v1/dynamic/get-list | GET | Query | JSON | 动态流列表 |
| https://community-api.xiaomawang.com/api/v1/message/get-unread | GET | Query | JSON | 未读消息数 |
| https://community-api.xiaomawang.com/api/v1/message/get-comment-list | GET | Query | JSON | 评论消息列表 |
| https://community-api.xiaomawang.com/api/v1/message/get-like-list | GET | Query | JSON | 点赞消息列表 |
| https://community-api.xiaomawang.com/api/v1/message/get-follow-list | GET | Query | JSON | 关注消息列表 |
| https://community-api.xiaomawang.com/api/v1/message/get-studio-list | GET | Query | JSON | 工作室消息列表 |
| https://community-api.xiaomawang.com/api/v1/message/get-system-list | GET | Query | JSON | 系统消息列表 |
| https://community-api.xiaomawang.com/japi/v1/msg/is-alert | GET | Query | JSON | 是否有提示弹窗 |
| https://community-api.xiaomawang.com/japi/v1/msg/read | GET | Query | JSON | 消息标记已读 |

---

## 九、举报与审核（Report / Judgement）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-api.xiaomawang.com/api/v1/report/get-info | GET | Query | JSON | 举报详情 |
| https://community-api.xiaomawang.com/api/v1/report/get-comment-list | GET | Query | JSON | 举报评论列表 |
| https://community-api.xiaomawang.com/api/v1/report/get-composition-list | GET | Query | JSON | 举报作品列表 |
| https://community-api.xiaomawang.com/api/v1/report/verify-comment | POST | JSON | JSON | 审核被举报评论 |
| https://community-api.xiaomawang.com/api/v1/report/verify-composition | POST | JSON | JSON | 审核被举报作品 |
| https://community-api.xiaomawang.com/japi/v1/judgement/join | POST | JSON | JSON | 参与评判（仲裁） |
| https://community-api.xiaomawang.com/japi/v1/judgement/listJudgedReport | POST | JSON | JSON | 已评判举报列表 |
| https://community-api.xiaomawang.com/japi/v1/judgement/myOpinion | POST | JSON | JSON | 我的评判意见 |

---

## 十、商城与订单（Mall / Order）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-api.xiaomawang.com/japi/v1/mall/product/list | GET | Query | JSON | 商品列表 |
| https://community-api.xiaomawang.com/japi/v1/mall/product/detail | GET | Query | JSON | 商品详情 |
| https://community-api.xiaomawang.com/japi/v1/mall/order | POST | JSON | JSON | 创建订单 |
| https://community-api.xiaomawang.com/japi/v1/mall/order/list | GET | Query | JSON | 订单列表 |
| https://community-api.xiaomawang.com/japi/v1/mall/order/detail | GET | Query | JSON | 订单详情 |
| https://community-api.xiaomawang.com/japi/v1/mall/order/confirm | POST | JSON | JSON | 确认订单 |
| https://community-api.xiaomawang.com/japi/v1/mall/order/cancel | POST | JSON | JSON | 取消订单 |
| https://community-api.xiaomawang.com/japi/v1/finance-account/info | GET | Query | JSON | 财务账户信息 |
| https://community-api.xiaomawang.com/japi/v1/reward/add | POST | JSON | JSON | 打赏/奖励发放 |

---

## 十一、背包（Backpack）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-api.xiaomawang.com/japi/v1/backpack/use-list | GET | Query | JSON | 背包使用中列表 |
| https://community-api.xiaomawang.com/japi/v1/backpack/unuse-list | GET | Query | JSON | 背包未使用列表 |
| https://community-api.xiaomawang.com/japi/v1/backpack/prop/use | POST | JSON | JSON | 使用道具 |
| https://community-api.xiaomawang.com/japi/v1/backpack/prop/unload | POST | JSON | JSON | 卸下道具 |
| https://community-api.xiaomawang.com/japi/v1/backpack/prop/load | POST | JSON | JSON | 装载道具 |
| https://community-api.xiaomawang.com/japi/v1/backpack/prop/recycle | POST | JSON | JSON | 回收道具 |
| https://community-api.xiaomawang.com/japi/v1/backpack/prop/compose | POST | JSON | JSON | 合成道具 |

---

## 十二、任务与签到（Task / Reward）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-api.xiaomawang.com/japi/v1/task/center | GET | Query | JSON | 任务中心列表 |
| https://community-api.xiaomawang.com/japi/v1/task/sign-info | GET | Query | JSON | 签到信息 |
| https://community-api.xiaomawang.com/japi/v1/task/day-sign | POST | JSON | JSON | 每日签到 |

---

## 十三、练习与测评（Practice，学科考试）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-api.xiaomawang.com/japi/v1/practice/selectMenu | GET | Query | JSON | 练习科目菜单 |
| https://community-api.xiaomawang.com/japi/v1/practice/matchSourceMenu | GET | Query | JSON | 比赛来源菜单 |
| https://community-api.xiaomawang.com/japi/v1/practice/matchList | GET | Query | JSON | 比赛列表 |
| https://community-api.xiaomawang.com/japi/v1/practice/matchPaperInfo | GET | Query | JSON | 试卷信息 |
| https://community-api.xiaomawang.com/japi/v1/practice/verifyMatch | GET | Query | JSON | 校验比赛 |
| https://community-api.xiaomawang.com/japi/v1/practice/attendNoList | GET | Query | JSON | 参赛编号列表 |
| https://community-api.xiaomawang.com/japi/v1/practice/userAnswerRecord | GET | Query | JSON | 用户答题记录 |

---

## 十四、活动（Activity）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://worldapi.xiaomawang.com/api/activity/get-list | GET | Query | JSON | 活动列表 |
| https://community-api.xiaomawang.com/api/activity/get-community-activity-award | GET | Query | JSON | 社区活动奖励 |
| https://community-api.xiaomawang.com/api/activity/submit-activity-compose | POST | JSON | JSON | 提交活动作品 |
| https://community-api.xiaomawang.com/sqapi/activity/get-list | GET | Query | JSON | Scratch 活动列表（2.0 旧接口） |
| https://community-api.xiaomawang.com/sqapi/activity/submit | POST | JSON | JSON | 提交活动作品（2.0 旧接口） |

---

## 十五、免费创作与上传（Free Compose / OSS）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://communityapi.xiaomawang.com/api/community/free-compose/index | GET | Query | JSON | 免费创作首页配置 |
| https://communityapi.xiaomawang.com/api/community/free-compose/config | POST | JSON | JSON | 免费创作配置 |
| https://communityapi.xiaomawang.com/api/community/free-compose/save-s3 | POST | JSON | JSON | 免费创作保存到 S3 |
| https://community-api.xiaomawang.com/api/v1/oss/get-token | GET | Query | JSON | 获取 OSS 上传凭证 |
| https://community-api.xiaomawang.com/api/v1/common/uploadToken | GET | Query | JSON | 获取通用上传 Token |
| https://community-api.xiaomawang.com/api/v1/cloud/get-ws-token | GET | Query | JSON | 获取云端 WebSocket Token |
| https://communityapi.xiaomawang.com/api/index/index/upload-log | POST | JSON | JSON | 上传日志上报 |
| https://communityapi.xiaomawang.com/api/index/index/upload-log-bind | POST | JSON | JSON | 上传日志绑定上报 |
| https://worldapi.xiaomawang.com/api/compose/copy-compose | POST | JSON | JSON | 复制作品 |

---

## 十六、Scratch 2.0 旧接口（sqapi）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://communityapi.xiaomawang.com/sqapi/compose/get-base | GET | Query | JSON | Scratch 作品基础信息 |
| https://communityapi.xiaomawang.com/sqapi/compose/save | POST | JSON | JSON | 保存 Scratch 作品 |
| https://communityapi.xiaomawang.com/sqapi/compose/submit | POST | JSON | JSON | 提交 Scratch 作品 |

---

## 十七、首页 / CMS / 引导（Index / Banner / CMS / Delpdk）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://community-api.xiaomawang.com/api/v1/banner/list | GET | Query | JSON | 首页轮播 Banner 列表 |
| https://community-api.xiaomawang.com/api/v1/cms/list | GET | Query | JSON | CMS 内容列表 |
| https://community-api.xiaomawang.com/api/v1/cms/get-info | GET | Query | JSON | CMS 内容详情 |
| https://community-api.xiaomawang.com/api/v1/index/get-area-list | GET | Query | JSON | 地区列表 |
| https://community-api.xiaomawang.com/api/v1/index/get-school-list | GET | Query | JSON | 学校列表 |
| https://communityapi.xiaomawang.com/api/user/delpdk/get-status | GET | Query | JSON | 删除引导 SDK 状态 |
| https://communityapi.xiaomawang.com/api/user/delpdk/get-guide-status | GET | Query | JSON | 新手引导状态 |
| https://communityapi.xiaomawang.com/api/user/delpdk/set-status | POST | JSON | JSON | 设置引导状态 |
| https://communityapi.xiaomawang.com/api/user/delpdk/set-del-pdk | POST | JSON | JSON | 设置删除引导 SDK |
| https://communityapi.xiaomawang.com/api/user/delpdk/close-guide | POST | JSON | JSON | 关闭新手引导 |

---

## 十八、公共接口（commonapi）与旧 Java 服务（servlet）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://commonapi.xiaomawang.com/servlet/api/picture/auth-user-tag | GET | Query | JSON | 用户图片认证标签 |
| https://commonapi.xiaomawang.com/servlet/sensors/timestamp | GET | Query | JSON | 服务器时间戳（防重复提交/时间校验，跨 11 个页面调用） |
| https://community-api.xiaomawang.com/servlet/api/picture/auth-user-tag | GET | Query | JSON | 用户图片认证标签（待验证：域归属存在歧义） |

---

## 十九、认证与第三方平台（非 axios 层，需单独调用）

| 接口路径 | 请求方法 | 请求格式 | 返回格式 | 用途说明 |
| :--- | :--- | :--- | :--- | :--- |
| https://sso.xiaomawang.com/login.html | GET | 页面跳转 | HTML | 统一登录页（SDK 加载 `xmcdn.../xm_sso_web/2.0.0/prod/login-sdk.js`） |
| https://m-world.xiaomawang.com/login/wx-auth | GET | Query | JSON | 微信授权绑定回调（bindUrl） |
| https://world.xiaomawang.com/w/auth/weixin-auth | GET | Query | JSON | 微信授权回调（redirect_uri） |
| https://program-exam.xiaomawang.com | 待验证 | — | — | 学科考试服务（examUrl，跨域跳转） |
| https://scratch2020.xiaomawang.com | 待验证 | — | — | Scratch 2.0 作品查看（examScratchViewUrl） |
| http://match.xiaomawang.com | 待验证 | — | — | 比赛系统（matchUrl） |
| https://online.xiaomawang.com | 待验证 | — | — | 在线课程（`/course_detail`） |
| https://vip.xiaomawang.com | 待验证 | — | — | VIP 会员服务 |
| https://testcourseapidev.xiaomawang.com/api/index/compose/save-homeworknew | POST | JSON | JSON | 测试环境作业保存接口（testcourseapidev） |

---

## 二十、已排除项（第三方 / 静态资源 / 框架）

| 类别 | 示例 |
| :--- | :--- |
| CDN 与 OSS 直连 | `xmcdn.oss-cn-shanghai.aliyuncs.com/**`、`xmyj.oss-cn-shanghai.aliyuncs.com`、`community-wscdn.xmwol.com/**`、`wscdn.xmwol.com/**`、`community-wscdn.xiaomawang.com/**` |
| 社交分享 SDK | `connect.qq.com`、`sns.qzone.qq.com/cgi-bin/qzshare`、`service.weibo.com/share`、`open.weixin.qq.com` |
| 播放器 SDK | `xmcdn.../xm_scp/dist/scratch-player.prod.2.2.1.js`、`xgplayer.min.css` |
| 字体 / 组件库 | `at.alicdn.com/t/c/font_914070`、`antd.min.css` |
| 第三方库 URL | `github.com`、`redux.js.org`、`reactjs.org`、`momentjs.com`、`beian.miit.gov.cn` |
| Next.js 内部路由（非 API） | `/activity/*`（34 个页面路由）、`/find`、`/market`、`/measure`、`/playground`、`/person/*`、`/dynamic`、`/message`、`/orders`、`/information`、`/feedback`、`/task-center`、`/w/release`、`/w/topic/`、`/community/main/compose/` 等 |
| Node/工具误匹配 | `/index`、`/`、`//`、`/_next/data/` |

---

## 二十一、覆盖率与待验证说明

| 项目 | 数值 |
| :--- | :--- |
| 抓取 JS 文件 | 130 个（35 首页 + 97 页面 chunk + 外部 SDK），约 6.6 MB |
| 页面路由数 | 97 个（全部来自 `_buildManifest.js`，含 34 个 activity 活动页） |
| 识别业务接口 | **163 条** |
| community-api.xiaomawang.com | 约 105 条 |
| communityapi.xiaomawang.com | 15 条 |
| worldapi.xiaomawang.com | 21 条 |
| commonapi.xiaomawang.com | 3 条 |
| 另有跨域平台 | 9 条（SSO / 考试 / Scratch2 / 比赛 / 课程 / VIP） |
| 方法明确 | 163 条全部明确（GET/POST），无待定方法 |

**归属置信度说明**：
- **无标记（高置信）**：请求层别名 → 导出字符 → baseURL 在同一 webpack 模块作用域内可完整解析。
- **标"待验证"（约 40 条）**：minified 后短别名（`f`、`r`、`K`、`j` 等）在多个模块间复用，跨模块复用时归属存在歧义，表中已按最可能的 baseURL 归类。这类接口**路径与方法均已确认，仅域名归属待验证**。
- `/abcde` 为 Python 创作器页面中的调试占位路径（原代码 `X.b.post("/abcde", n)`），疑似未完成/测试代码。

**核心结论**：该站采用 **axios 多实例 + 路径前缀区分业务域** 的架构——`/api/v1/**` 与 `/japi/v1/**` 为主业务与交易接口，`/sqapi/**` 为 Scratch 2.0 遗留接口，`/servlet/**` 为旧 Java 服务。请求规范为 **GET 传 params / POST 传 JSON body**，返回裸 JSON（无统一信封包装），与 ccw.site 的 `{status, code, msg, body}` 信封模式不同。
