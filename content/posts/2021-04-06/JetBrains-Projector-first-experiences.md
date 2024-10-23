---
title: "JetBrains Projector 使用初体验"
description: ""
authors: ["xuchunqiu"]
date: 2021-07-06T10:22:06Z
toc: true
draft: false
---

Projector 是 JetBrains 新推出的远程开发解决方案。实现上是在远程主机上部署 IDE 服务端然后在本地浏览器中或本地客户端中访问，本文记录了安装步骤和使用体验。

## 0x00 可能有用的链接

1. 官方博客介绍：[使用 Projector 远程访问 IDE](https://blog.jetbrains.com/zh-hans/blog/2021/03/25/projector-ide/)
2. 官方文档：[Projector documentation](https://jetbrains.github.io/projector-client/mkdocs/latest/)
3. 安装方式 A（命令行）：[JetBrains/projector-installer](https://github.com/JetBrains/projector-installer)
4. 安装方式 B（docker）：[JetBrains/projector-docker](https://github.com/JetBrains/projector-docker)
5. Win/macOS/Linux 客户端下载：[releases on JetBrains/projector-client](https://github.com/JetBrains/projector-client/releases/tag/launcher-v1.0.1)

## 0x01 安装步骤

本文是采用上面的安装方式 A 在运行 Ubuntu 18.04 的远程主机上运行，具体步骤直接参考链接 3。命令抄录如下：

```bash
# install python3
user@machine:~# sudo apt install python3 python3-pip -y

# check pip3 version
user@machine:~# pip3 --version
# if pip3 < 19
user@machine:~# python3 -m pip install -U pip

# install other packages
user@machine:~# sudo apt install libxext6 libxrender1 libxtst6 libfreetype6 libxi6 less -y

# install Jetrains Projector
user@machine:~# ip3 install projector-installer --user

# use Projector to install IDE
user@machine:~# projector install
Checking for updates ... done.
Installing IDE in quick mode; for full customization you can rerun this command with "--expert" argument or edit this config later via "projector config edit" command.
           1. Idea_Community
           2. Idea_Ultimate
           3. PyCharm_Community
           4. PyCharm_Professional
           5. CLion
           6. GoLand
           7. DataGrip
           8. PhpStorm
           9. WebStorm
          10. RubyMine
          11. Rider
Choose IDE type or 0 to exit: [0-11]: 4
Do you want to select from Projector-tested IDE only? [y/N]y
           1. PyCharm Professional 2019.3.4
           2. PyCharm Professional 2020.2
           3. PyCharm Professional 2020.3.3
Choose IDE number to install or 0 to exit: [0-3]: 3
Installing PyCharm Professional 2020.3.3
Downloading pycharm-professional-2020.3.3.tar.gz
[##------------------------------------------------]    4%  00:04:15
Unpacking pycharm-professional-2020.3.3.tar.gz
[##################################################]  100%          
Configuration name: PyCharm
Checking for updates ... done.
To access IDE, open in browser 
        http://localhost:9999/
        http://127.0.0.1:9999/
        http://10.106.200.224:9999/

To see Projector logs in realtime run
        tail -f "/root/.projector/configs/PyCharm/projector.log"

Exit IDE or press Ctrl+C to stop Projector.

# start IDE
user@machine:~# projector run
```
其中我遇到的坑主要是需要升级 pip3（>= 19）和 Python（>= 3.6），解决方案见下一章。

## 0x02 故障排除

### 更新完 pip3 后得到 `SyntaxError: invalid syntax` 

首先按照链接 3 的教程更新 pip3 到最新版本：

```bash
# 检查现有 pip3 版本
pip3 --version

# 如果版本小于 19 就使用如下命令更新到最新版本
python3 -m pip install -U pip
```

再接着执行任何 `pip3 install` 都会得到如下错误：

```bash
sys.stderr.write(f"ERROR: {exc}")
                                   ^
SyntaxError: invalid syntax
```

这个问题出现的原因是远程主机 Python = 3.5，但 pip 默认更新到最新的 pip 21.0 仅支持 Python 3.6+[^1]。我的选择是将 pip 更新到最后支持 Python 3.5 的那一版：

```bash
curl -fsSL -o- https://bootstrap.pypa.io/pip/3.5/get-pip.py | python3.5
```

这就为接下来的问题埋下伏笔。现在回想此时正确的做法应是直接更新 Python。

### 尝试用 pip3 安装 Projector 时遇到 `Error`

在按照链接 3 的教程安装完一系列的依赖后，就可以正式安装 Projector：

```bash
pip3 install projector-installer --user

ERROR: Could not find a version that satisfies the requirement projector-installer
ERROR: No matching distribution found for projector-installer
```

如果遇到以上错误，说明需要升级 Python 版本[^2]。教程见[^3]，摘录如下：

```bash
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt-get update
sudo apt-get install python3.8
sudo apt install python3.8-distutils

# 更新软链接
sudo rm /usr/bin/python3
sudo ln -s python3.8 /usr/bin/python3
```

### `projector` 命令无效

这个问题在链接 3 的 FAQ 部分已经进行了说明[^4]。是出在 `projector` 的安装路径 `~/.local/bin` 没有加入到环境变量 `$PATH` 中，直接的 workaround 是使用完整路径启动 `projector`：

```bash
~/.local/bin/projector
```

不过一劳永逸的方法将添加环境变量 `$PATH` 的命令写入到 `~/.profile`：

```bash
echo "export PATH=${PATH}:~/.local/bin" >> ~/.profile
. ~/.profile
```

### `projector run` 尝试启动 IDE 后很快出现报错日志

在初次使用 `projector install` 完成 IDE 安装后会自动启动 IDE，使用完毕后使用 `Ctrl` + `c` 退出。之后想再次启动已经安装好的 IDE 就需要使用 `projector run` 命令，可能遇到在输出完 IDE 的访问链接后立刻打印报错日志的情况：

```bash
Checking for updates ... done.
To access IDE, open in browser 
        http://localhost:9999/
        http://127.0.0.1:9999/
        http://10.106.200.224:9999/

To see Projector logs in realtime run
        tail -f "/root/.projector/configs/PyCharm/projector.log"

Exit IDE or press Ctrl+C to stop Projector.
--------------------- Projector log session start. Installer version: 1.1.1 Run config: PyCharm - 2021-04-07 08:02:35.367158
OpenJDK 64-Bit Server VM warning: Option UseConcMarkSweepGC was deprecated in version 9.0 and will likely be removed in a future release.
[DEBUG] :: IdeState :: Starting attempts to attach IJ injector agent
[DEBUG] :: IdeState :: Starting attempts to initialize IDEA: fix AA and disable smooth scrolling (at start)
[INFO] :: ProjectorServer :: ProjectorServer is starting on host 0.0.0.0/0.0.0.0 and port 9999
[DEBUG] :: IdeState :: Starting attempts to Getting IDE colors
[INFO] :: ProjectorServer :: WebSocket SSL is disabled
[INFO] :: ProjectorServer :: Server started on host 0.0.0.0/0.0.0.0 and port 9999
[DEBUG] :: ProjectorServer :: Daemon thread starts
[DEBUG] :: IdeState :: Starting attempts to search for editors
IDE has not been initialized yet
[DEBUG] :: ProjectorServer :: Daemon thread finishes
```

这个问题可能是之前的 IDE 没有成功退出导致的[^5]（虽然 log 看起来并非如此），杀掉旧进程就可以了：

```bash
root@machine:~# ps aux | grep -i pycharm
root       966 36.5  0.2 8513048 286408 pts/0  Sl   07:38   9:51 /root/.projector/apps/pycharm-2020.3.3/jbr/bin/java -classpath /r
root      2102  0.0  0.0  13216  1064 pts/0    S+   08:05   0:00 grep --color=auto -i pycharm
root@machine:~# kill 966
root@machine:~# ps aux | grep -i pycharm
root      2113  0.0  0.0  13216  1044 pts/0    S+   08:06   0:00 grep --color=auto -i pycharm
root@machine:~# projector run
```

## 0x03 存在的问题

此处仅列举我在实际体验中觉得很糟心的地方，其它问题可以在链接 2 的 Known problems[^6] 部分查看。

### 快捷键冲突和剪贴板共享

Projector 的服务端是安装在远程 Linux 环境中，然后通过本地浏览器或下载链接 5 的客户端使用（这里需要提一句的是 JetBrains 的 IDE 插件市场中也上架了 `Projector`[^7]，但这个是把本机 IDE 当服务端来用的）。

当通过浏览器访问 Projector 时，IDE 的快捷键会与浏览器本身的快捷键冲突，例如选择代码块的快捷键 `Ctrl` + `w`[^8]在 Chromium/Firefox/Safari 中是关闭当前标签页。所以为了使用快捷键还有复制粘贴功能，最好还是下载独立的 Projector 客户端。

### 启用自签证书的 HTTPS

本文初次写成于 2021 年 3 月，但当 4 月我在另一台机器上部署完 Projector 后发现即使用 Projector 客户端去连也不能将其中的文本复制粘贴到其它程序中。在 JetBrains 的 bug 跟踪系统 YouTrack 上有人[^9]解释是新版本的服务端后只有在 localhost 部署或启用 HTTPS 后才能跨程序复制粘贴，同时给出了对于 [不完整剪贴板同步实现过程的官方文档](https://jetbrains.github.io/projector-client/mkdocs/latest/ij_user_guide/accessing/#incomplete-clipboard-synchronization)。

就个人+内网使用来说，自签 HTTPS 证书是比较简单的方式。此处给出自签证书的启用方法，至于使用可信任证书颁发机构给域名颁发的证书，可以参考链接 3 自述文件的 FAQ 部分[^10]。

在命令行中交互式启动自签 HTTPS 证书：

```bash
# 所有可用命令
projector config (--help)

# 列出所有配置的名称
projector config list

# 展示选定配置的详情
projector config show
# 这里的信息要比 ~/.projector/configs/PyCharm/config.ini 更详细，
# 也有一个布尔的 "Projector uses secure config (https/wss)= 0"

# 交互式修改配置
projector config edit
# 只需给 Use secure connection? 一个 y 的回答
```

修改完毕后再 `projector run` 就能看到终端中提示要从 `~/.projector/ssl/ca.crt` 安装证书，有意思的是这个证书下载下来是 0KB，无法安装。我最后是通过直接在 Chrome 访问 `https://<projector-server-address>:<projector-port>` -> 导出证书 -> 文件管理器安装。

[^1]: https://stackoverflow.com/questions/65869296/installing-pip-is-not-working-in-python-3-6

[^2]: https://youtrack.jetbrains.com/issue/PRJ-302

[^3]: https://stackoverflow.com/questions/58591889/how-i-can-upgrade-my-ubuntu-python3-7-to-python3-8-latest-version

[^4]: https://github.com/JetBrains/projector-installer#projector-command-is-unavailable

[^5]: https://stackoverflow.com/questions/61236544/clion-pycharm-phpstorm-intellij-wont-start-says-ide-has-not-been-initialized/61237140#61237140

[^6]: https://jetbrains.github.io/projector-client/mkdocs/latest/ij_user_guide/jetbrains/#known-problems

[^7]: https://plugins.jetbrains.com/plugin/16015-projector

[^8]: https://www.jetbrains.com/help/idea/working-with-source-code.html#editor_code_selection

[^9]: https://youtrack.jetbrains.com/issue/PRJ-426#focus=Comments-27-4816576.0-0

[^10]: https://github.com/JetBrains/projector-installer#secure-connection