---
title: "如何在 Ubuntu 上安装配置 Rime 输入法"
description: "Rime 输入法的安装是一个颇为让人头疼的问题"
authors: ["xuchunqiu"]
date: 2019-05-24T01:04:47Z
toc: true
draft: false
---

Ubuntu 上比较流行又好装的中文输入法应该就只有 [搜狗输入法 for Linux](https://pinyin.sogou.com/linux/) 一款了，但如果你追求简洁纯粹，那 [Rime 输入法](https://rime.im) 或许是你的菜。

Rime 是一款由 [佛振](https://github.com/lotem) 主导开发的自由软件，支持在 Windows、macOS 和 Linux 操作系统上运行。它的优点是干净纯粹支持高度的客制化，缺点是较差的联想功能（自己慢慢养词库或者导入其它输入法的词库）和比较复杂的配置。本文将专注于介绍 Rime 在安装阶段的配置过程。

## 1. 基本安装

在 [Rime 下载页面的 Linux 章节](https://rime.im/download/#Linux) 中给出了基于 IBus 和 Fcitx 两种输入法框架的安装指导，我这里是选择 IBus 框架：

```bash
sudo apt-get install ibus-rime
# 可选：安装双拼
sudo apt-get install librime-data-double-pinyin 
```

## 2. 切换到 IBus

从启动台打开「输入法」或在终端中使用命令开启：

```bash
im-config
```
然后选择 `ibus`

## 3. 添加 Rime 到 IBus

在终端中使用以下命令开启 IBus 设置：

```bash
ibus-setup
```
在输入法中添加 `Chinese - Rime`。如果看不到这一项，需要在系统设置的语言中添加中文并拖动到最顶，然后登出系统以使新的语言设置生效。

## 4. 切换 IBus engine 到 Rime

使用如下命令切换到 Rime：

```bash
ibus engine rime
```

{{< admonition type=tip title="其它有用的命令" open=true >}}
```bash
# 列出所有可用引擎
ibus list-engine

# 查看当前使用引擎
ibus engine
```
{{< /admonition >}}

## 5. 可选：在 Rime 中启用小鹤双拼

修改配置文件 `~/.config/ibus/rime/default.yaml` ，依照现有布局加入条目：

```yaml
- schema：double_pinyin_flypy
```
然后在输入时就能通过 `ctrl` + `/`` 呼出方案选单。

{{< admonition type=tip title="" open=true >}}
更多配置教程请查阅 [Rime 帮助文档](https://rime.im/docs/)
{{< /admonition >}}