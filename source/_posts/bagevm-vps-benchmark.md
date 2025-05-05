---
title: 自用 BageVM VPS 测评报告
categories:
  - Docs
tags:
  - VPS
  - BageVM
  - Benchmark
date: 2025-04-03 15:50:48
index_img:
---

Bage 是我找了很久之后，无意中发现的一家 VPS 厂商。他家的机器性能和网络都还可以，价格也很亲民。

# 机器配置

| 型号 | Salt Lake City - TINY |
|:-:|:-:|
| CPU | 1 * AMD Ryzen 9 9950X |
| 内存 | 1GB DDR5 |
| 硬盘 | 20GB SSD |
| 流量 | 4T@1000Mbps ( 双向计费 ) |
| IP | 1 \* IPv4 & 1 \* IPv6 |
| 位置 | 盐湖城，美国 |
| 价格 | $2.59 USD / 每月 |
| 购买链接 | [with AFF](https://www.bagevm.com/aff.php?aff=121) |

# 系统信息

```text
 CPU 型号            : AMD Ryzen 9 9950X 16-Core Processor @4291.932 MHz
 CPU 数量            : 1 Virtual CPU(s)
 CPU 缓存            : L1: 128 KB / L2: 512 KB / L3: 16 MB
 AES-NI              : ✔️ Enabled
 VM-x/AMD-V/Hyper-V  : ✔️ Enabled
 内存                : 390.33 MB / 964.53 MB
 气球驱动            : ✔️ Enabled
 内核页合并          : ❌ Undetected
 虚拟内存 Swap       : 269.86 MB / 2.00 GB
 硬盘空间            : 13.65 GB / 19.60 GB
 启动盘路径          : /dev/vda1
 系统                : debian 11.11 [x86_64] 
 内核                : 5.10.0-34-amd64
 系统在线时间        : 32 days, 04 hours, 25 minutes
 时区                : CST
 负载                : 0.32 / 0.24 / 0.20
 虚拟化架构          : KVM
 NAT类型             : Port Restricted Cone
 TCP加速方式         : bbr
 IPV4 ASN            : AS63150 BAGE
 IPV4 Location       : Salt Lake City / Utah / United States
 IPV4 Active IPs     : 220/256 (subnet /24)
 IPV6 ASN            : AS63150 BAGE
 IPV6 Location       : Salt Lake City / Utah / United States
 IPv6 子网掩码       : /64
```

# 硬件 Benchmark

```text
--------------------------------CPU测试-通过sysbench测试--------------------------------
1 线程测试(单核)得分:   6154.34
--------------------------------内存测试-通过sysbench测试---------------------------------
单线程顺序写速度: 35201.67 MB/s(36.91K IOPS, 5s)
单线程顺序读速度: 84583.00 MB/s(88.69K IOPS, 5s)
-----------------------------------硬盘测试-通过fio测试-----------------------------------
测试路径      块大小   读测试(IOPS)            写测试(IOPS)            总和(IOPS)
/root         4k       209.75 MB/s(52.4k)      210.31 MB/s(52.6k)      420.06 MB/s(105.0k)     
/root         64k      472.37 MB/s(7380)       474.85 MB/s(7419)       947.22 MB/s(14.8k)      
/root         512k     746.12 MB/s(1457)       785.76 MB/s(1534)       1.53 GB/s(2991)         
/root         1m       837.63 MB/s(817)        893.41 MB/s(872)        1.73 GB/s(1689)    
```

Bage 的硬件性能给的是非常不错，十分适合建站。

# 流媒体解锁

```text
Apple                     YES (Region: USA) [Native]
BingSearch                YES (Region: US)
Claude                    YES [Native]
Dazn                      Banned
Disney+                   YES (Region: US) [Via DNS]
Gemini                    YES (Region: USA) [Native]
GoogleSearch              YES
Google Play Store         YES (Region: US) [Native]
IQiYi                     YES (Region: US) [Native]
Instagram Licensed Audio  YES [Native]
KOCOWA                    YES [Native]
MetaAI                    YES (Region: US) [Native]
Netflix                   YES (Region: US) [Via DNS]
Netflix CDN               US
OneTrust                  YES (Region: US UTAH) [Via DNS]
ChatGPT                   YES (Region: US) [Native]
Paramount+                YES [Native]
Amazon Prime Video        YES (Region: US) [Native]
Reddit                    YES
SonyLiv                   YES (Region: US) [Native]
Sora                      YES (Region: US)
Spotify Registration      NO
Steam Store               YES (Community Available) (Region: US)
TVBAnywhere+              YES (Region: US) [Native]
TikTok                    YES (Region: US) [Native]
Viu.com                   YES [Native]
Wikipedia Editability     YES
YouTube Region            YES (Region: US) [Native]
YouTube CDN               LAX
```

# 网络测试

## 国内三网回程

| 运营商 | 测试IP | 线路名称 | 级别 |
| - | - | - | - |
| 北京电信 | 219.141.140.10 | 电信163 | [普通线路] |
| 北京联通 | 202.106.195.68 | 联通4837 | [普通线路] |
| 北京移动 | 221.179.155.161 | 移动CMI | [普通线路] |
| 上海电信 | 202.96.209.133 | 电信163 | [普通线路] |
| 上海联通 | 210.22.97.1 | 联通4837 | [普通线路] |
| 上海移动 | 211.136.112.200 | 移动CMI | [普通线路] |
| 广州电信 | 58.60.188.222 | 电信163 | [普通线路] |
| 广州联通 | 210.21.196.6 | 联通4837 | [普通线路] |
| 广州移动 | 120.196.165.24 | 移动CMI | [普通线路] |
| 成都电信 | 61.139.2.69 | 电信163 | [普通线路] |
| 成都联通 | 119.6.6.6 | 联通4837 | [普通线路] |
| 成都移动 | 211.137.96.205 | 移动CMI | [普通线路] |

## 节点测速

| 位置 | 上传速度 | 下载速度 | 延迟 | 丢包率 |
| - | - | - | - | - |
| Speedtest.net | 988.81 Mbps | 970.18 Mbps | 0.55 ms | 0.0% |
| 洛杉矶 | 1046.44 Mbps | 997.26 Mbps | 14.04 ms | 0.0% |
| 联通Beijing | 258.65 Mbps | 963.63 Mbps | 163.29 ms | Not available. |  
| 联通上海5G | 729.29 Mbps | 533.41 Mbps | 192.43 ms | 0.0% |
| 电信浙江 | 211.34 Mbps | 1010.35 Mbps | 153.76 ms | Not available. |  
| 电信Zhenjiang5G | 200.70 Mbps | 997.41 Mbps | 188.27 ms | Not available. |  
| 移动Suzhou | 392.97 Mbps | 0.28 Mbps | 306.36 ms | 0.0% |

## Speedtest 测速

<div align="center">
  <img src="https://www.speedtest.net/result/c/aa8962a4-72a4-472b-9496-26673a0eb36e.png" width="50%" alt="Speedtest">
</div>
