---
title: 个人 Linux 知识库
categories:
  - Linux
tags:
  - Note
  - Docker
  - Git
  - GitHub
date: 2023-11-30 03:24:39
index_img:
---

{% note info %}
这篇笔记旨在收集一些个人可能需要去反复 Google 的命令，以及经常会踩的坑。已与多年前发布的一篇笔记合并。
{% endnote %}

# ✔ 解决方案

## 🌐 网络带宽限制

### 1. 限制网络接口带宽

[Wondershaper](https://github.com/magnific0/wondershaper)

```bash
wondershaper -a <ADAPTER> -u <RATE> -d <RATE>
# 对 <ADAPTER> 进行带宽限制，上行为 <RATE> Kbps ，下行为 <RATE> Kbps

ip addr show
# 查看所有网络接口名称

wondershaper -a <ADAPTER> -c
# 清除 <ADAPTER> 上的限速设置

wondershaper -a eth0 -u 100 -d 200
# 限制 eth0 的带宽为：上行 100 Kbps，下行 200 Kbps。
```

使用包管理器安装的 wondershaper 版本较老，不适用于上述指令。需要在 Github 仓库自行 clone 。安装方法详见 Github Repo。

### 2. 限制进程带宽

[Trickle](https://github.com/mariusae/trickle)

```bash
apt install trickle
# Debian/Ubuntu 安装 Trickle

trickle -s -u <RATE> -d <RATE> <command>
# 以独立模式运行 Trickle，受到带宽限制的进程是 <command>，上行 <RATE> KB/s，下行 <RATE> KB/s

trickle -s -u 512 -d 2048 firefox
# 以上行 512 KB/s，下行 2048 KB/s 的带宽运行 Firefox
```

## 🐋 Docker Hub 拉取容器

因不可抗力因素，中国大陆网络从 [Docker Hub 官方镜像](https://hub.docker.com/) 拉取容器较为困难 ( 间歇中断 )。

此时可以使用其他服务商提供的镜像，此处列举部分：

- ~~百度云：`https://mirror.baidubce.com`~~
- ~~网易云：`https://hub-mirror.c.163.com`~~
- ~~Docker Proxy：`https://dockerproxy.com`~~
- ~~阿里云：[登录控制台自行获取](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)，每人独有地址。~~
- 轩辕：`https://docker.1ms.run` `https://docker.xuanyuan.me`

以下服务商的镜像服务可能不可用，自行甄别：

- 谷歌云容器镜像：`http://mirror.gcr.io` ( 可能不可用 )
- 中科大镜像：`https://docker.mirrors.ustc.edu.cn` ( 仅供内部使用 )

Cloudflare Workers 搭建 Docker Hub 镜像：

[https://github.com/ciiiii/cloudflare-docker-proxy](https://github.com/ciiiii/cloudflare-docker-proxy)

### 🐋 修改 Docker 镜像源方法

修改文件 `/etc/docker/daemon.json`，粘贴以下内容：

```bash
{
  "registry-mirrors": [
    "https://mirror.baidubce.com",
    "https://hub-mirror.c.163.com",
    "https://dockerproxy.com"
  ]
}
```

重载服务

```bash
sudo systemctl daemon-reload
sudo systemctl restart docker
```

---

# ⌨️ 常用命令

## 😻 Git 、Github 相关

```bash
git config --global user.name "user"
git config --global user.email user@example.com
# Git 设置用户名、邮箱

ssh-keygen -t ed25519 -C "user@example.com"
# 生成 SSH 密钥，并将公钥文件粘贴进 https://github.com/settings/keys
ssh -T git@github.com
# 如返回 'successfully authenticated' 等字样，则 SSH 密钥添加成功。
```

使用 `git clone git@github.com:user/repo.git` 方式拉取仓库时，`git push` 将不会要求身份认证，使用 Https 拉取仓库时则会每次要求输入密码。

## 🌐 Openwrt 单 LAN 口设置旁路由

### 接口

1. 设置 DHCP 忽略 LAN
2. 禁用 IPV6 分配长度 （如果不需要）
3. 设置网关为主路由
4. 使用 Openclash 也许可以关闭桥接。也可以在 Clash 里面指定 br-lan (?)，比较玄学，还在研究中。

### 防火墙

1. 关闭 SYN-flood 防御
2. 打开 LAN-WAN 的 IP 动态伪装
3. 添加自定义防火墙规则

```bash
iptables -t nat -I POSTROUTING -o eth0 -j MASQUERADE
# 部分品牌路由器做主路由时，无需此规则，如果出现了异常的加载速度就使用这个。
```

## 🐋 Docker.service 启动时意外退出 error code 1”

`sudo dockerd --debug` 找报错点。

## 🍥 Debian 12 (bookworm) 静态 IP

1. 打开 `systemd-networkd` 的目录：

    ``` bash
    cd /etc/systemd/network/
    ```

2. 找到对应的网络接口配置文件（通常是类似 99-default.link 或 00-wired.network 等），如果没有，可以创建一个新的配置文件：

    ``` bash
    sudo nano /etc/systemd/network/20-wired.network
    ```

3. 在文件中添加以下内容（将 `enp3s0` 替换为你实际的网络接口名，IP 地址、网关和 DNS 也根据你的网络设置来修改）：

    ``` ini
    [Network]
    Address=192.168.1.100/24          # 设定静态 IP 地址和子网掩码
    Gateway=192.168.1.1               # 设置默认网关
    DNS=8.8.8.8                       # 设置 DNS 服务器（可选）
    DNS=8.8.4.4                       # 可设置多个 DNS

    [DHCP]
    UseDHCP=false                     # 禁用 DHCP
    ```

4. 保存退出文件。

5. 重新启动网络服务

    ``` bash
    sudo systemctl restart systemd-networkd
    ```

## ✂️ FFmpeg 指令

### 1. 指定开始结束时间无损裁剪**视频**

```bash
ffmpeg  -i input.mp4 -ss 00:01:00 -to 00:02:00 -c copy output1.mp4
```

### 2. 指定开始结束时间无损裁剪**音频**

```bash
ffmpeg  -i source.mp3  -vn -acodec copy -ss 00:03:21.36 -t 00:00:41 output.mp3
```

推荐使用小丸工具箱。

## 📄 设置 Swap 空间 ( Swap 文件 )

创建一个 1 GB 的 Swap 文件

``` bash
sudo fallocate -l 1G /swapfile
# 或者
sudo dd if=/dev/zero of=/swapfile bs=1M count=1024 status=progress
```

设置 `600` 权限，只有 root 能读取

``` bash
chmod 600 /swapfile
```

格式化文件为 Swap，并启用

``` bash
sudo mkswap /swapfile
sudo swapon /swapfile
```

`free m` 检查是否生效

``` bash
aurora@debian12:~$ free -h
               total        used        free      shared  buff/cache   available
Mem:           960Mi       274Mi       224Mi       2.6Mi       618Mi       686Mi
Swap:          1.0Gi          0B       1.0Gi
```

## Debian 安装 Nodejs

官网推荐使用 `nvm` 安装

``` bash
# 安装 nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash

# 刷新终端(?)
\. "$HOME/.nvm/nvm.sh"

# 列出远程 Nodejs 版本
nvm ls-remote

# 安装 v22.15.1 (lts)
nvm install v22.15.1
```
