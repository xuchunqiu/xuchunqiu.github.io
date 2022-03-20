# 如何在 Ubuntu 上安装深度截图

# Update

深度截图目前已有 [官方介绍](https://www.deepin.org/zh/original/deepin-screenshot/)、[使用说明](https://wiki.deepin.org/wiki/%E6%B7%B1%E5%BA%A6%E6%88%AA%E5%9B%BE)，但依然没有提供从深度商店外安装深度截图的教程。

appimage 格式深度截图的下载地址已经失效，但依然可以从互联网档案馆上 [下载（35.7 MB）](https://web.archive.org/web/20201120043221/https://github-production-release-asset-2e65be.s3.amazonaws.com/110259696/6110e818-0909-11e8-92a8-e734dc188fe0?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20201120%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20201120T043221Z&X-Amz-Expires=300&X-Amz-Signature=134866f6be04ad00a4eaf1a2aa00604d5fbfd4fe5f9597a94a2a92da3d758053&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=110259696&response-content-disposition=attachment%3B%20filename%3DDeepin_Screenshot_4.0.10.4-x86_64.AppImage&response-content-type=application%2Foctet-stream)。

## 1. 下载深度截图

从以下地址下载第三方打包的深度截图：

~~https://github.com/ZetaoYang/deepin-app-package/releases~~

## 2. 运行
首先添加执行权限
```bash
chmod +x ./Deepin_Screenshot-x86_64.AppImage
```
使用方法：
- GUI： 文件管理器双击
- CLI：`~/Downloads/Deepin_Screenshot_4.0.10.4-x86_64.AppImage`

## 3. 让深度截图在 Ubuntu 启动台显示
### ~~3.1 AppImageLauncher 法~~
AppImageLauncher 顾名思义是一个可以快速把 appimage 软件加入启动台的工具。只需要从 https://github.com/TheAssassin/AppImageLauncher/releases 下载的合适版本，然后执行以下命令安装

```bash
sudo dpkg -i ~/Downloads/appimagelauncher_1.0.3-travis508.git20190223.0c6d768_amd64.deb
```

理论上再次双击之前的 Deepin_Screenshot_4.0.10.4-x86_64.AppImage 就可以初始化 AppImageLauncher，进而可以把 Deepin_Screenshot_4.0.10.4-x86_64.AppImage 显示在 LaunchPad。不幸的是我双击后遇到闪退。

### 3.2 手动法

此方法适用于所有只能命令行启动的程序，只需要在 /usr/share/applications/ 下添加一个配置文件就能让程序显示在 Ubuntu 启动台上。但在开始之前，为了美观还需要一个合适的图标，我是从 http://appstore.deepin.com/data/v1/workaround/metadata/deepin-screenshot/meta/icons/deepin-screenshot.svg 下载图标，然后用在线工具转为PNG格式备用。

按照 [文件系统层次结构标准](https://zh.wikipedia.org/wiki/%E6%96%87%E4%BB%B6%E7%B3%BB%E7%BB%9F%E5%B1%82%E6%AC%A1%E7%BB%93%E6%9E%84%E6%A0%87%E5%87%86) 为深度截图在 `/opt` 下创建目录：

```bash
sudo mkdir /opt/deepinscreenshot
```

然后把 appimage 程序和 PNG 格式的图标复制进去：

```bash
sudo cp ~/Downloads/Deepin_Screenshot_4.0.10.4-x86_64.AppImage /opt/deepinscreenshot/
sudo cp ~/Downloads/deepin-screenshot-24px.png /opt/deepinscreenshot/
```

~~注入灵魂~~ 注册应用程序到启动台：
```bash
sudo vim /usr/share/applications/biubiubiu.desktop
```

{{< admonition type=info title="biubiubiu.desktop 的内容示例" open=true >}}
```
[Desktop Entry]
Name=biubiubiu
Name[zh_CN]=深度截图
Exec=/opt/deepinscreenshot/Deepin_Screenshot_4.0.10.4-x86_64.AppImage
Icon=/opt/deepinscreenshot/deepin-screenshot-24px.png
Terminal=false
X-MultipleArgs=false
Type=Application
Encoding=UTF-8
Categories=Application;
StartupNotify=false
```
{{< /admonition >}}

然后就可以在 LaunchPad 看到图标了，鼠标拖拽即可固定到 dock 栏。
