---
title: "如何在 Ubuntu 上安装 QQ"
description: "手动安装 deepin 商店版 TIM 的过程记录"
authors: ["xuchunqiu"]
date: 2019-02-25T08:24:22Z
toc: true
draft: false
---
# Update

深度的 [维基页面](https://wiki.deepin.org/) 现在已经列出了直接使用 `apt` 命令安装 [QQ轻聊版](https://wiki.deepin.org/wiki/QQ%E8%BD%BB%E8%81%8A%E7%89%88)、[QQ 标准版](https://wiki.deepin.org/wiki/QQ) 的方法。

## 1. 安装 Deepin wine 环境
```bash
# 下载
git clone https://gitee.com/wszqkzqk/deepin-wine-for-ubuntu.git
# 执行安装脚本
sudo sh ./deepin-wine-for-ubuntu/install.sh
```
## 2. 安装 Deepin 版 TIM 安装包
```bash
# 下载
wget http://packages.deepin.com/deepin/pool/non-free/d/deepin.com.qq.office/deepin.com.qq.office_2.0.0deepin4_i386.deb
# 解包安装
sudo dpkg -i ./Downloads/deepin.com.qq.office_2.0.0deepin4_i386.deb
# 在 Ubuntu 的 Lauchpad 应已出现 TIM 的图标，单击启动、登录，应该就可以正常使用了
```
## 3. 可选：解决在高分屏 GUI 过小的问题
使用如下命令调出 wine 的配置页面：
```bash
WINEPREFIX=~/.deepinwine/Deepin-TIM/ deepin-wine winecfg
```
在 Graphics 选项卡中往大调整 Screen resolution 的值，调好后重新运行 TIM 即可看到效果。