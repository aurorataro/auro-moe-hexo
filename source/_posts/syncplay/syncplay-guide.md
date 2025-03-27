---
title: Syncplay 使用教程 & 资源发布
categories:
  - [SyncPlay]
tags:
  - SyncPlay
date: 2024-02-05 20:00:44
category_bar: true
index_img:
---

<!-- markdownlint-disable MD034 -->

# 🌴 部署一起观看所需环境

{% note light %}
📁  
文件名：syncplay-mpv.zip  
大小：65.7 MB  
SHA-1：68cdaef93ef39becd4f9f8a0b97e7b9dfc273fdc
{% endnote %}

{% btn https://share.tarocloud.net/share/XCBtAFvv, 📁文件下载, Tarocloud 盐湖城节点 %}
{% btn https://loafcraft.lanzouo.com/iMOew2q6qyxi, ☁️蓝奏云备用, 大陆服务器 %}

解压后，我们得到以下文件

``` Text
播放器和一起看
├── mpv.net
│   └── mpvnet.exe
└── syncplay
    └── Syncplay.exe
```

打开 `Syncplay.exe`

![Syncplay 向导界面](https://cdn.auro.moe/post/Snipaste_2024-02-06_03-56-33.webp)

请在 `连接设置` 中填写：

- 服务器地址：`v.tarocloud.net:8999`
- 服务器密码：`114514`
- 用户名：`可选填`
- 默认房间：`qianqiu`

在 `播放器设置` 中选择刚刚 `mpv.net` 文件夹下的 `mpvnet.exe`。

![fileloca2exe](https://cdn.auro.moe/post/fileloca2exe.gif)

现在就可以点击 `保存设置并运行Syncplay` 了。

# 📽️ 开始一起看

此时弹出两个窗口，分别是 ``Syncplay 控制台`` 和 ``播放器 mpv.net``。

{% note warning %}
**接下来观影的一段时间内，请勿关闭任何一个窗口**
{% endnote %}

![sync-play](https://cdn.auro.moe/post/Snipaste_2024-02-06_04-28-23.webp)

左上角点击 `文件`-`打开媒体文件`，选择事先准备好的电影文件，就大功告成了。如果你已经准备好，请点击右下 `准备好了` 按钮。稍等一下大家，电影马上开始。

![sync-play & player](https://cdn.auro.moe/post/Snipaste_2024-02-07_01-44-53.webp)

# 🔊 更换音轨、字幕

本片支持切换英配、国配以及多种字幕。  
调整位置在播放器底栏

![audio tracks & subtitles](https://cdn.auro.moe/post/Snipaste_2024-02-07_02-24-32_ps.webp)

# ❓ 无法运行Syncplay？

请尝试安装 c++ 运行环境：
https://www.microsoft.com/zh-cn/download/details.aspx?id=53587
