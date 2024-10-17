# 运动手表不完全使用体验：Apple vs Garmin


作为一个有轻度运动习惯（骑车、跑步、游泳、力量、HIIT，每周3～5次）的肥仔，我先后使用了三年多 *Apple Watch Series 4（GPS）*、一年半 *Garmin Enduro 2*。本文不会对比具体的硬件型号+软件版本（有这方面需要请直接去看 [DC Rainmaker](https://www.dcrainmaker.com)），而是希望根据我有限的体验对比两家做产品的思路差异。

定这个标题的时候我有意避开了「智能手表」这个称呼，因为 Garmin 在跟手机的联动上只能说差强人意（当前没有 Android 设备所以以下表述中手机默认为 iPhone），具体原因后面会写到。

## 放弃 Apple Watch 的契机

我的 *S4* 是 2019 年从香港买完激活过 ECG（v1）才背回内地，用了三年多电池寿命下降到 80%，我不得不在公司留一块充电器才有足够的安全感。*S4* 从满电到自动关机大概能支撑 5～6 小时的骑行记录（关机前运动记录会自动结束，但要等重新充电开机后才能发送到手机），2023 年 9 月发布的 [iOS 17 带来了可以跟 Apple Watch 联动的骑行码表视图](https://support.apple.com/zh-cn/guide/watch/apd4cbc876c7/10.0/watchos/10.0)，是否手表没电后手机可以接管 GPS 记录我不得而知，因为 2023 年 3 月我在天猫的「Garmin佳明海外旗舰店」购入了 *Enduro 2*。 正如前面提到的 *S4* 电池寿命告急，有次早上起床来不及补电就去爬山，结果没等下山手表就电量耗尽了，回家后越想越气连夜下单换表。

## 为什么不是 *Apple Watch Ultra*？

Garmin 手表有很多细分产品线(铁三、跑步、潜水、高尔夫、航海……)去它 [官网](https://www.garmin.com.cn/products/wearables/?cat=watches) 可以看到详尽的分类，我选择 *Enduro 2* 就是因为续航最长。[*Apple Watch Ultra* 宣称有 12 小时的运动记录时长](https://www.apple.com.cn/watch/battery)，而作为当时 Garmin 续航最强的表 [*Enduro 2* 的运动记录时长是 68 小时](https://www.garmin.com.cn/products/wearables/enduro-2/#specsTab)。

## 为什么不是高驰、Amazfit 这些国产表？

因为有用 Strava 汇总运动记录、[running_page](https://github.com/yihong0618/running_page) 集中 [展示](https://biking.xuchunqiu.com) 的习惯，这一点上作为国际大牌 Garmin 无疑是方便的。另一个原因是买自行车码表的时候还在上学预算有限选择了 Wahoo，现在有了积蓄正好来试试 Garmin 的手表。

## 锁区现状

**相信没人会喜欢花相同的钱买到被阉割的商品，但遗憾的是两家厂商的手表都存在锁区/阉割现象。**

正如前面提到的 Apple Watch 的 ECG 功能在早年因为医疗审批问题是无法直接开通的，比较有趣的是当时有人 [在口岸将手机连上香港运营商信号后，不用过境就能开通 ECG](https://v2ex.com/t/710861)。更多 Apple 锁区的技术细节可以参考《[iOS 如何按地区限制功能：浅析 MobileGestalt 与 Eligibility](https://sspai.com/prime/story/ios-feature-region-lock)》。2024 年的今天国行 Apple Watch 已经可以无痛开通 ECG 了，但随 WatchOS 11 推出的 [睡眠呼吸暂停功能又陷入了和 ECG 一样的轮回](https://www.v2ex.com/t/1075937)。至于运动数据同步，手机上安装 Strava 后关联苹果健康即可。

Garmin 这边的情况稍有不同，[中国区账号只能关联（也就是上传运动数据到）国内的第三方运动 App；国际区账号则可以关联 Strava。我买的外版手表则从软件上阉割了国内的两家付款码程序和公交卡](https://dailysync.vyzt.dev/docs/%E6%88%91%E5%BA%94%E8%AF%A5%E4%BD%BF%E7%94%A8%E5%9B%BD%E9%99%85%E5%8C%BA%E8%BF%98%E6%98%AF%E4%B8%AD%E5%9B%BD%E5%8C%BA)。国际区账号可以直接注册没有任何验证，手表付款很鸡肋可能在单独带蜂窝版（仅 Apple，Garmin 没有这种型号）出门时才会用到。地图方面 Garmin ~~有点坏~~有点太合规了，以某些方式对中国大陆的用户（包括我）隐藏了等高线地图（TopoActivate China ML），目前我手表上原厂跟中国大陆有关的地图是「CN China NT 2023.20-NI」、「CN RDA China NT 2023.20-NI ALL」、「China DEM v1.00」。

## 喜欢 Garmin 的点

1. **Garmin 的按键操作逻辑完备，它甚至允许用户完全关闭触摸功能（充分考虑了登山带手套、水上/水下运动容易误触的场景）**。实际上只要不看地图我也是关闭了触摸在用，一方面是 Garmin 的触摸操作不够精准不如用按键快速，另一方面也有利于省电。Apple Watch 不支持仅通过按键操作。
2. **全天候的心率变异性（[HRV]^(Heart Rate Variability)）检测**。Apple Watch 的 HRV 检测频率非常低，从苹果健康里检查原始数据就能看到它一天只测量最多十几次。大概是处理器性能弱 + 电池大才给了 Garmin 这样的底气。也正因全天候的检测使得 Garmin 可以在此基础上通过 Firstbeat 的算法得到身体电量、压力指数。
3. **表盘信息量丰富，尽管在美观程度上不如 Apple Watch**。Garmin 可以通过手机上的 ConnectIQ 应用程序向手表安装应用/表盘，我用了第三方表盘 [HIPERION](https://apps.garmin.com/apps/034dc8d6-c695-4cbc-9193-ff2375e1e4e0) 同时查看日出日落、心率、压力分数、步数、步行距离、呼吸频率、血氧含量、身体电量。
4. **[ANT]^(Adaptive Network Topology)+ 连接能力**。ANT+ 是骑行配件普遍支持的互联协议，与更广为人知的蓝牙相比 ANT+ 支持多播，我的使用场景是同时在码表和手表上连接同一个踏频传感器，这样就有两份骑行记录互为备份。
5. **支持心率广播，可以替代心率带**。我手机上至今还留着两个应用程序 [HeartCast](https://apps.apple.com/us/app/heartcast-heart-rate-monitor/id1499771124)、[ECHO](https://apps.apple.com/us/app/echo-watch-heart-rate-monitor/id1494679144)，它们的功能是获取 Apple Watch 的心率通过蓝牙发送到码表，这样骑行记录里就会包含心率数据了，这是我在没买心率带时候的权宜之计。实际可能途中我打开手机拍两张照片这个 app 的后台就被系统杀掉了，稳定性堪忧。所以很快我就买了心率带来用。心率带也不是完美的，穿上的时候把握不好松紧会勒，脱下的时候及时清洗避免生锈，导致我每次骑车都要花更多时间做好心理建设。当发现 Garmin 支持心率广播后立刻就不再使用心率带了。有些健身房的跑步机也支持接收心率广播展示你的实时心率。
6. **运动记录界面自由地定义栏位和数据**。这个可以说 Garmin 就是按照码表的思路来布置的，从一～八栏（视表盘尺寸可能有差异）统统给你，每个栏位几十种数据可以选择。
7. **提供网页版控制台**。苹果健康里的数据只支持在移动设备上查看，而 Garmin 的主程序 Garmin Connect 同时也有网页版 https://connect.garmin.com。从功能上来说网页版是手机版的超集，提供了比手机版更全面的数据视图、训练指导。
8. **在记录运动的同时导航**。这在户外运动的时候是蛮常见的需求，根据提前规划的路线进行 [TBT]^(Turn By Turn) 导航并在偏离路线时重新算路。我甚至可以武断地讲所有以运动为卖点的手表都会实现这个功能（只是某些厂商可能不支持开放且通用的 [[GPX](https://www.topografix.com/gpx.asp)]^(Global Positioning XML) 轨迹格式；某些厂商的重新算路功能需要连接手机使用）。但 [截至 2024 年在 Apple Watch 上第一方的地图与运动仍是两个独立的 app](https://discussionschinese.apple.com/thread/254872040?sortBy=rank)，第三方的 [WorkOutDoors](https://apps.apple.com/us/app/workoutdoors/id1241909999) 售价 7.99 USD 支持 GPX 导入并在运动界面显示地图和回到/偏离路线提醒，遗憾的是它尚未实现 TBT 导航。
9. **离线地图**。这是一个可能会有争议的点因为离线意味着会过时，但至少在没有信号的地方会有安全感。[WatchOS 10 加入了离线地图功能](https://support.apple.com/zh-sg/guide/watch/apd0111bf788/10.0/watchos/10.0)，所以两者的差异只是地图服务提供商。

## Garmin 没有做好的点
1. **孱弱的处理器性能**，这影响了很多细节，例如：
    1. Garmin 的界面操作起来不跟手，不如 Apple Watch 丝滑。
    2. Garmin 在拖动、缩放地图时会有明显的重新绘制等待（根据网上的视频最顶级的旗舰机型也有此问题）。
    3. 在做力量训练时我会尝试以微小幅度晃动手腕期望 Garmin 亮屏，但它就是检测不到。
    4. Garmin 导入 GPX 会有一个「计算路线」的过程，这至少涉及 TBT 的计算，还可能包含使用「China DEM v1.00」生成坡度信息。以一段 143 KM 的新路线为例，载入路线花了 14 s，计算路线花了 32 s。
2. **糟糕的手机互联**，例如：
    1. Apple Watch 在手表上截图是会自动进入手机相册的，而 Garmin 需要你用 USB 线连接电脑查看截图。
    2. iOS 中被设置为定时摘要的 app 通知会按与手机相同的逻辑推送，而 Garmin 则会立刻通知你。并且 Garmin 弹通知的时候开头数秒只显示 app 名称，要盯着手表发呆后才见到通知内容。
    3. Garmin 秒针经常慢几秒。在 Garmin 的文档中声称「[当设备与 Garmin connect mobile 配对同步后，设备可通过 app 同步获取手机时间](https://support.garmin.com/zh-CN/?faq=uXR3bWcLnq6465i9tYA5iA)」，但即使我通过手表上的「同步时间」功能手动触发看起来也还是不准。按前面引用的文档 Garmin 也会自动通过 GPS 卫星授时，这个准不准我没有仔细测。
3. **落后的 OTA 和软件生态**，包括：
    1. Apple Watch 长周期的系统更新和新功能下放真的是独一份，Garmin 三个月都不见得能更新一次系统，更遑论新功能下放了。这背后一方面是因为产品线丰富开发者力不从心，但很难不怀疑还有为促进新型号销量的商业考量。
    2. Garmin 的软件/表盘商店虽然也允许第三方开发者的存在，实际普遍都没什么亮点。不像 Apple Watch 因为高性能处理器和全面的 SDK 可以让一些第三方出品的运动或健康监测 app 比系统自带的更好用。
4. **不应该存在的限制**（以下数字可能因机型而异），包括：
    1. 闹钟最多定 10 个时间，即使所有闹钟都是关闭状态也不允许你添加第 11 个。
    2. 从 ConnectIQ 安装的应用/表盘/数据字段合计最多 33 个。我目前还剩 59.60 MB 空间可以再安装 6 个，有趣的是如果把手表连接到电脑会看到储存空间还有足足 9 GB。
    3. 在运动途中无法重新加载 GPX。这个需求的场景是在路途中间改变行程，用手机重新规划了另一条路线想走，但手表上只允许在地图上重新选择终点后使用机内导航提供的路线。

