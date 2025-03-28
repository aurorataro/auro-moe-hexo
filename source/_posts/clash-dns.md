---
title: 【转载】Clash 的 DNS 请求逻辑
categories:
  - [Network,Proxy]
tags:
  - Clash
  - Proxy
  - DNS 
date: 2021-07-25 00:41:54
author: Sukka
index_img:
---

{% note info %}
本文节选转载自 [浅谈在代理环境中的 DNS 解析行为 by Sukka](https://blog.skk.moe/post/what-happend-to-dns-in-proxy/) ，请支持原作者。
{% endnote %}

## DNS

1. 当访问一个域名时， nameserver 与 fallback 列表内的所有服务器并发请求，得到域名对应的 IP 地址。
2. clash 将选取 nameserver 列表内，解析最快的结果。
3. 若解析结果中，IP 地址属于 国外，那么 clash 将选择 fallback 列表内，解析最快的结果。因此，我在 nameserver 和 fallback 内都放置了无污染、解析速度较快的国内 DNS 服务器，以达到最快的解析速度。但是 fallback 列表内服务器会用在解析境外网站，为了结果绝对无污染，我仅保留了支持 DoT/DoH 的两个服务器。

## clash DNS 配置注意事项

1. 如果您为了确保 DNS 解析结果无污染，请仅保留列表内以 tls:// 或 https:// 开头的 DNS 服务器，但是通常对于国内域名没有必要。

2. 如果您不在乎可能解析到污染的结果，更加追求速度。请将 nameserver 列表的服务器插入至 fallback 列表内，并移除重复项。

- 关于 DNS over HTTPS (DoH) 和 DNS over TLS (DoT) 的选择：

对于两项技术双方各执一词，而且会无休止的争论，各有利弊。各位请根据具体需求自行选择，但是配置文件内默认启用 DoT，因为目前国内没有封锁或管制。

DoH: 以 https:// 开头的 DNS 服务器。拥有更好的伪装性，且几乎不可能被运营商或网络管理封锁，但查询效率和安全性可能略低。

DoT: 以 tls:// 开头的 DNS 服务器。拥有更高的安全性和查询效率，但端口有可能被管制或封锁。

若要了解更多关于 DoH/DoT 相关技术，请自行查阅规范文档。
