---
title: Bilibili live 自动录播与上传
categories:
  - Docs
tags:
  - Bilibili
  - Streaming
date: 2024-06-10 09:54:23
index_img:
---

从去年10月起，一直帮群友们录制直播录像。现在这套系统已经运行大半年了，还算比较稳定。  
如果你想自建，可以参考本文的部分内容。

>Docker 与直接启动服务相比比较好管理，所以本文介绍的基本都是 Docker 部署方案。

## 💭 须知

挂录播是比较烧钱的，你可能的支出有服务器成本、网盘成本、电费成本、带宽成本、维护的时间成本等等。包括但不限于 b 站会对你的 IP 进行风控，导致录制失败甚至是同局域网下哔哩哔哩加载速度变慢、无法载入视频等。

现在 b 站对于主流云服务商的多个 IP 段进行风控，可能无法用云服务器挂录播了。

## 📹️ 直播录制

现在有很多的基于 ffmpeg 的录制项目可以选择，部分甚至支持多平台。我只有哔哩哔哩的需求，所以常用的有以下两种。

### mikufans 录播姬

[项目地址](https://github.com/BililiveRecorder/BililiveRecorder)

![mikufans 录播姬](https://cdn.auro.moe/post/mikufans-record.webp)

#### 使用 Docker 部署 mikufans 录播姬

``` bash
docker run -d \
  --name bililive-recorder \
  -p 2356:2356 \
  -v /path/to/recordings:/rec \
  -e BREC_HTTP_BASIC_USER=用户名 \
  -e BREC_HTTP_BASIC_PASS=密码 \
  bililive/recorder:latest
```

使用宿主机保存录播文件的路径替换`/path/to/recordings`
如果你的容器能被外网访问，建议设置 `http-basic-auth`

容器运行后，浏览器打开录播姬运行的窗口，打开 `设置-高级设置` 进行配置，下面是我使用的一些配置，按自己需求修改。

#### mikufans 直播姬设置

| 设置 | 选项 | 备注 |
| :-: | :-: | :-: |
| 分段模式 | 根据时间分段 | 每 60 分保存为一个文件 |
| 在flv中写入直播信息 | ✔️ |保存直播信息到 `mediainfo` 中 |
| Webhook V2 |[http://192.168.1.100:2356/recordWebHook](http://192.168.1.100:2356/recordWebHook)| 这里使用的地址，用于上传插件的参数传递，稍后会提到 |
| Cookie | ******** | 建议设置小号，稍后会提到 |

其他设置默认。  
然后直接添加房间号就能开始录制了。

### blrec

[项目地址](https://github.com/acgnhiki/blrec)

![blrec](https://cdn.auro.moe/post/blrec.webp)

#### 使用 Docker 部署 blrec

``` bash
sudo docker run \
    -v /etc/blrec:/cfg -v /var/log/blrec:/log -v ~/blrec:/rec \
    -dp 2233:2233 acgnhiki/blrec \
    -c /cfg/another_settings.toml \
    --key-file path/to/key-file \
    --cert-file path/to/cert-file \
    --api-key bili2233
```

`--key-file` 和 `--cert-file` 如果你需要 https 可以设置，否则在部署时应当去除。  
配置文件、日志、录播文件请自行设置到相应的路径中。

#### blrec 设置

| 设置 | 选项 | 备注 |
| :-: | :-: | :-: |
| 时长限制 | 01:00:00 | 1 小时分割一个文件 |
| 弹幕 | 全关 |  |
| flv添加元数据 | ✔️ | 如果需要本地播放或者是剪辑需求，开启这个拖动进度条不会卡顿 |
| User Agent | Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/89.0.4389.114 Safari/537.36 | 我直接 Copy 的我电脑浏览器的配置 |
| Cookie | ******** | 建议配置小号，下面还会提到 |
| Webhook |[http://192.168.1.100:2356/recordWebHook](http://192.168.1.100:2356/recordWebHook)| 全勾。这里使用的地址，用于上传插件的参数传递，稍后会提到 |

其他设置默认。

### mikufans 录播姬与 blrec 的简单比较

|  | mikufans 录播姬 | blrec |
| :-: | :-: | :-: |
| 界面UI | 界面较为简洁，详细数据界面基本没有排版|房间列表就支持查看封面和实时下载信息，详细界面有一些图表等 |
| 格式支持 | 暂时只支持 flv 流，未来版本有 hls（咕咕咕？） | 支持 flv 和 hls 流 |
| 通知 | 不支持 | 支持主流推送渠道 |
| web 文件管理器 | 支持 | 不支持 |
| API 接口 | REST API & GraphQL API | REST API |
| 主观网络体验 | 添加房间后，经常会与弹幕服务器断开连接，导致无法及时开始录制 | 首次启动就能加载房间信息的话，录制几乎没有问题。如果上级路由设置了透明代理，可能会导致获取房间失败 |

### 为什么要设置 Cookies 以及 Cookies 的获取方法

{% note primary %}
结论
设置 Cookie 后相当于是以登录用户的身份录制直播，相比于游客录制限制更少，更稳定。
{% endnote %}

现阶段，b 站会对游客身份（无 Cookies）进行风控。具体表现在：

1. 抓取弹幕时，用户名会被打码处理`A****: 晚上好`且无法获取头像和 uid 。  

2. 无法录制原画画质。

如果你有弹幕或者录制原画的需求，请务必设置 Cookies。

{% note danger %}
使用 Cookies 的账号可能会被{% label danger @b 站风控 %}，后果可能有：被识别为机器人账号、大会员被冻结、封禁账号等。所以强烈建议使用小号的 Cookes ，切勿使用大号作死。
Cookies 十分隐私，泄露 Cookies 相当于泄露账号和密码，所以请勿传播带有自己 Cookies 的日志、文件等。
同时 Cookies 具有时效性，有一定的有效期，所以请定期获取新的 Cookies。
{% endnote %}

#### Cookies 的简单获取方法

登录账号打开`创作中心`，右键点击`查看网页源代码`

![blrec-getcookies-1](https://cdn.auro.moe/post/blrec-getcookies-1.webp)

按下`F12-开发者工具`，点击`network-网络`并刷新页面。

![blrec-getcookies-2](https://cdn.auro.moe/post/blrec-getcookies-2.webp)

点击`home`，在标头中找到 Cookie ，复制其后面一大段文本原封不动的粘贴到录播姬的配置中。（blrec 支持 cookies 的检查。）

![blrec-getcookies-3](https://cdn.auro.moe/post/blrec-getcookies-3.webp)

## ⬆️ 录像上传

在我的工作流程中，我先将录像即时投稿到哔哩哔哩，在凌晨将原文件备份至 Onedrive 并释放本地磁盘空间。

上面1小时分段一次的好处就能体现出来，在一场直播下播时，已经有大部分文件已经上传到了哔哩哔哩的投稿系统，视频审核速度更快。

### 投稿哔哩哔哩

>在这里，我使用 biliup-for-java 投稿视频。
>在录播姬设置好 webhook 地址后，主播开播、下播时，相关参数都会被传输到 biliup 中，实现自动化投稿录像。

#### biliup-for-java

[项目地址](https://github.com/mwxmmy/biliupforjava)

![blrec-biliup](https://cdn.auro.moe/post/blrec-biliup.webp)

使用 Docker 部署

``` bash
docker pull mwxmmy/biliupforjava
docker run -p 宿主机端口:80 -v 宿主机路径:/bilirecord /mnt:/mnt --name bup -d --restart always mwxmmy/biliupforjava

# 为什么要把宿主机的 /mnt 映射到容器的 /mnt ?
# 录播姬通过 webhook 传递过来的是录像文件路径，比如 /mnt/rec/123.flv ，如果不打通 /mnt 的话，biliup 会找不到录像文件导致上传失败。
```

首先在`bilibili 用户`中登录你需要上传的账号。

然后就可以添加直播间了，在编辑中可以设置一些投稿相关的参数，这里主要着重讲一下`文件后处理`这个功能，它支持的选项有：

| 选项 |备注 |
| :-: | :-: |
| 不删除 | 需要手动整理 |
| 上传完成后删除 | 不推荐，投稿失败会导致录像丢失 |
| 投稿成功后删除 | 不推荐，未过审会导致无法补档 |
| 审核通过后删除 | {% label warning @较为推荐 %} ，适合无需保留录像文件 |
| 几天后删除 | 适合临时使用 |
| 录制结束几天后移动 | 适合不投稿哔哩哔哩 |
| 上传完成后移动 | 一般推荐，但是投稿失败需要自行上传 |
| 审核通过后移动 | {% label warning @非常推荐 %}，适合投稿+保留文件 |
| 录制结束后移动(无法上传) | {% label warning @适合不投稿哔哩哔哩 %} |
| 录制结束后复制 | 复制一份到 NAS |
| 投稿成功后移动 | 转码失败或未过审需要自行补投 |
| 审核通过成功后复制 | 复制一份到 NAS |

使用`审核通过后移动`选项，可以将过审的文件移动到另一个文件夹用于 rclone 上传到网盘。未过审的依然在原路径，待人工修复后，再次触发投稿，过审后，文件继续被移动到 rclone 的文件夹中等待上传网盘。

### 上传至网盘

在上一步中，将过审后的录像文件移动到了另一个文件夹，在这里我们可以使用`rclone`或者`rsync`将该文件夹上传至远程路径。使用 crontab 在夜间创建上传任务，合理使用闲置的上传带宽。

[rclone文档](https://rclone.org/) [rsync文档](https://rsync.samba.org/documentation.html)

## ☁️ 附加服务

使用 alist 提供原文件的下载、在线播放。例如[奶粉录播站](https://r.koif.uk/)

~~我自己录的玩意儿也许在这里：[TaroREC](https://rec.tarocloud.net)~~

## 🚧 踩过的坑

摸鱼中：

上传带宽限制

容器挂载的问题

录播姬与biliup的连接问题

关闭公网访问或者是加auth
