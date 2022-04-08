# 远程开发工具 JetBrains Gateway 初体验


在去年写完 [Projector 的体验](https://xuchunqiu.me/jetbrains-projector-first-experiences/) 之后，我就已经没有再使用过它了，原因如下：

- 必须安装客户端才能使用快捷键
- 服务端非本机部署时，必须在客户端主机上安装自签证书才能使用复制粘贴功能
- 因为网络传输不畅而不时遇到画质压缩，就只能等它连接稳定后再继续操作，使用体验不够连贯

所以之后我又用回了通过 SSH 来部署代码和调用远程 Python 解释器的方法。去年年底，JetBrains 在其博客上发布了《[隆重推出 JetBrains IDE 的远程开发](https://blog.jetbrains.com/zh-hans/blog/2021/11/29/introducing-remote-development-for-jetbrains-ides/)》一文介绍了 [Gateway](https://www.jetbrains.com/zh-cn/remote-development/gateway/) 这一全新的远程开发工具。最近我终于有时间来安装体验一下 Gateway，本文就是对这一过程的简单回顾。

## 0x00 前言

在开始之前必须强调的是，与 Projector 可以部署社区版 IDE 不同，要想通过 Gateway 连接到远程 IDE 则必须有对应的 IDE 订阅。我是通过学生 edu 邮箱获得的 JetBrains 全家桶订阅。

更多关于 Gateway 的常见问题可以参阅官方博文：《[深入了解 JetBrains Gateway](https://blog.jetbrains.com/zh-hans/blog/2021/12/03/dive-into-jetbrains-gateway/#%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98%E8%A7%A3%E7%AD%94)》

## 0x01 下载与安装

支持的操作系统：

- 服务端：Linux（[未来会加入 Windows 和 macOS 的支持](https://blog.jetbrains.com/zh-hans/blog/2021/12/03/dive-into-jetbrains-gateway/#:~:text=%E6%88%91%E4%BB%AC%E5%B0%86%E5%9C%A8%E6%9C%AA%E6%9D%A5%E6%B7%BB%E5%8A%A0%E5%AF%B9%20Windows%20%E5%92%8C%20macOS%20%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%9A%84%E6%94%AF%E6%8C%81)）
- 客户端：Linux、Windows、macOS（intel）、macOS（Apple Silicon）

与 Projector 不同，Gateway 服务端的安装完全通过客户端完成：[下载地址](https://www.jetbrains.com/zh-cn/remote-development/gateway/)

目前下载到的应该都 [抢先体验计划]^(Early Access Programs) 的版本，截止本文写就时最新的版本是 `2022.1 Beta (221.5080.102)`，安装好后的主界面如下；

{{< figure src="https://raw.githubusercontent.com/xuchunqiu/personal-blog-resources/main/posts/2022-03-08/2022-03-08-001.png" title="" >}}

共有 SSH、JebBrains Space 和 共享链接 三种启动 Gateway 客户端的方式。区别如下：

- SSH：只要提供远程 Linux 主机的 SSH 连接信息即可，也是本文使用的方式
- JetBrains Space：团队开发协作平台，有提供 [基础的免费方案](https://www.jetbrains.com/space/buy/)
- Code With Me 链接 / Gateway 链接：前者是用于结对编程的 IDE 插件，同样有 [基础的免费方案](https://www.jetbrains.com/zh-cn/code-with-me/buy/#personal)；后者的分享链接暂时还没有暴露出来，只有在连接到已有环境失败时才会显示出来

接下来就选择 SSH，提供远程 Linux 主机的连接信息：

{{< figure src="https://raw.githubusercontent.com/xuchunqiu/personal-blog-resources/main/posts/2022-03-08/2022-03-08-002.png" title="" >}}

成功后就可以选择需要在远程主机上安装哪个 IDE 服务端（在 Gateway 中被称为 Backend）。当前有 IDEA、CLion、GoLand、PhpStorm、PyCharm、Rider、RubyMine 和 WebStorm 可选（EAP 的 Gateway 看到的 IDE 也都是 [EAP]^(Early Access Programs) 或 [RC]^(Release Candidate) 版），但都不提供社区版的选项：

{{< figure src="https://raw.githubusercontent.com/xuchunqiu/personal-blog-resources/main/posts/2022-03-08/2022-03-08-003.png" title="" >}}

默认是直接从互联网下载 IDE Backend 安装到远程主机的 `~/. cache/JetBrains/RemoteDev/dist`，也可以手动上传安装包和指定安装路径：

{{< figure src="https://raw.githubusercontent.com/xuchunqiu/personal-blog-resources/main/posts/2022-03-08/2022-03-08-004.png" title="" >}}

等待下载和安装过程完成，就会在本机自动打开 Gateway Client 了，与正常的 IDE 稍有区别的是多了一些连接状态的显示。整体上 Gateway 服务端的安装实在是比 Projector 省心太多。

{{< figure src="https://raw.githubusercontent.com/xuchunqiu/personal-blog-resources/main/posts/2022-03-08/2022-03-08-005.png" title="" >}}

## 0x02 Gateway 的使用感受

### 优点

1. 比起在 `Settings`-`Tools`-`SSH Configurations` 添加 SSH 配置的远程开发方式，Gateway 不需要手动同步本地和远程代码（因为它的代码只保存在远程）、配置远程解释器。
2. 比起 Projector 或是 VNC 方案，Gateway 画面永远不会糊。
3. 剪贴板共享功能开箱即用。
4. 支持将远程主机端口映射到本地。这个主要是因为我的远程主机是一个 Docker 环境，在创建好后就不能添加端口映射了，但 Gateway Client 的 `Settings`-`Tools`- `Port Forwarding` 里可以很方便地添加端口映射：
   {{< figure src="https://raw.githubusercontent.com/xuchunqiu/personal-blog-resources/main/posts/2022-03-08/2022-03-08-006.png" title="" >}}

### 缺点

1. 有时需要复制两遍。当在 Gateway Client 中打开的文本内容先复制再粘贴时，有机率粘上的是之前一次复制的内容，不过只要 Gateway Client 再复制一次就没问题了。
2. 没有考虑非英文输入的情况。这篇博文就是通过 Gateway 完成的，在使用拼音输入中文的过程中，我注意到当打字速度过快时就会有前后「吃字」的问题。这可能是因为 Gateway 对于输入的每个字母都是立即传输的，但当汉字上屏后光标位置的瞬间跳动没有被正确处理。
3. 上传文件功能缺失。拖动本地文件到 Gateway Client 的文件树上并不会有任何反应，如果这时能自动通过 SFTP 协议上传就好了。

## 0x03 补充：强制结束 IDE Backend 的方法

因为目前还处在 EAP 阶段，稳定度还不算太高，有时会遇到 IDE Backend 旧进程卡死新进程启动不起来的窘境，目前 Gateway 也没有提供强制重启之类的功能，只能手动去 kill 相关进程。在 Gateway 的 [最近 SSH 项目]^(Recent SSH Projects) 页面有一个终端图标，点进去执行以下命令：
```bash
user@machine:~# ps aux | grep -i pycharm | awk '{print $2}'
738
743
861
876
1138
2880

# 把以上所有进程号都杀掉
user@machine:~# kill 738

```
