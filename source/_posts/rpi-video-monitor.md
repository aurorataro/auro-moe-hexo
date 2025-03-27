---
title: Motion + 树莓派 CSI 模块实现视频监控
categories:
  - Docs
tags:
  - RaspberryPi
  - Debian
date: 2021-05-20 23:59:57
index_img:
---

{% note info %}
用现有的树莓派加上 CSI 摄像模块，就可以搭建起一套廉价的视频监控系统。
{% endnote %}

# 🤔 开始前准备

## 🗒️ 设备清单

- Raspberrypi 4B+
- CSI 摄像模块
- 互联网连接

## ❗️ 注意事项

- CSI 无需买官方版本，第三方在 1688 只需 20 元左右
- CSI 不支持热插拔，移除、插入前必须将 Rpi 断电
- 如果不在外浏览摄像头或者有公网 IPV4/6 ，可以忽略内网穿透

# ▶️ 正式开始

## 📹️ 连接并启用摄像头模块

将 Rpi 断电，将 CSI 排线插进 CAMERA 接口中，并且锁死卡扣。

在系统配置中启用对摄像头的支持

这里以 [Debian-Pi-Aarch64](https://github.com/openfans-community-offical/Debian-Pi-Aarch64) 为例

``` bash
sudo vim /boot/config.txt
```

找到以下字段，将 `start_x=1` 取消注释。

``` txt
####################
## Camera Module used: start_x=1

start_x=1
```

检查摄像头连接是否成功

``` bash
sudo reboot #重启系统
vcgencmd get_camera #检查摄像头连接情况
```

如果输出

``` bash
supported=1 detected=1
```

则摄像头连接成功，默认的视频位置在 /dev/video0

## 👷 安装motion

``` bash
# 终端输入
sudo apt install motion -y
# 修改motion的默认配置文件
sudo vim /etc/default/motion
```

具体配置文件作用见下表格

| 选项 | Range/Values Default | 说明 |
| :-: |:-: | :-: |
| auto_brightness| Values: on,off  Default: off| 让motion自动调节视频的的亮度，只适用于不带有自动亮度调节的摄像机 |
| brightness | Values: 0-255  Default: 0 (disabled) | 调整摄像机的亮度 |
| saturation | Values: 0 - 255  Default: 0 (disabled) | 调整摄像机的颜色饱和度 |
| hue | Values: 0 - 255 Default: 0 (disabled) | 调整摄像机的色调 |
| contrast | Values: 0-255  Default: 0 (disabled) | 调整摄像机的对比度 |
| daemon | Values: on,off  Default: off | 以守护进程在后台运行。这个选项只能放在motion.conf，不能放在 thread config file |
| emulate_motion | Values: on, off  Default: off | 即使没有运动物体也要保存图像 |
| ffmpeg_output_movies | Values: on, off Default: off | 是否保存视频 |
| ffmpeg_bps | Values: 0 - 9999999  Default: 400000 | 视频比特率 |
| ffmpeg_variable_bitrate | Values: 0, 2 - 31  Default: 0 (disabled) | 动态比特率，如果开启这个功能ffmpeg_bps将被忽略，0为关闭，2为最好质量，31为最差质量 |
| ffmpeg_duplicate_frames | Values: on, off  Default: on | 为了达到每秒的帧数要求，会复制一下帧填充空白时间，关掉这个功能后每个帧都紧接下一个帧，看起来像快进 |
| ffmpeg_output_debug_movies | Values: on, off  Default: off | 调试模式，只看到变化的图像 |
| ffmpeg_video_codec | Values: mpeg4, msmpeg4, swf, flv, ffv1, mov, ogg, mp4, mkv, hevc  Default: mpeg4 | 视频格式 |
| framerate | Values: 2 - 100  Default: 100 (no limit) | 帧速率，每秒多少帧 |
| frequency | Values: 0 - 999999  Default: 0 (Not set) | 频率协调 Hz，（不清楚作用） |
| lightswitch | Values: 0 - 100  Default: 0 (disabled) | 忽略光照强度改变引起的变化 |
| locate_motion_mode | Values: on, off, preview  Default: off                       | 给运动物体用方框标出 |
| locate_motion_style | Values: box, redbox, cross, redcross  Default: box | 标记风格 |
| max_movie_time | Values: 0 (infinite) - 2147483647  Default: 3600 | 最大视频时间 |
| minimum_frame_time | Values: 0 - 2147483647  Default: 0 | 最小帧间隔，设置为0表示采用摄像头的帧率 |
| minimum_motion_frames | Values: 1 - 1000s  Default: 1 | 捕捉持续至少指定时间的运动帧 |
| movie_filename | Values: Max 4095 characters  Default: %v-%Y%m%d%H%M%S | 视频的文件名 |
| ffmpeg_timelapse | Values: 0-2147483647  Default: 0 (disabled) | 间隔时间，拍摄延时视频 |
| ffmpeg_timelapse_mode | Values: hourly, daily, weekly-sunday, weekly-monday, monthly, manual  Default: daily | 延时拍摄模式 |
| timelapse_filename | Values: Max 4095 characters  Default: %v-%Y%m%d-timelapse | 延时拍摄的文件名 |
| output_pictures | Values: on,off,first,best,center  Default: on | 是否保存图片和模式设置 |
| output_debug_pictures | Values: on,off  Default: off | 图片调试模式，只输出运动物体 |
| picture_filename | Values: Max 4095 characters  Default: %v-%Y%m%d%H%M%S-%q | 图片文件名 |
| picture_type | Values: jpeg,ppm  Default: jpeg | 图片类型 |
| post_capture | Values: 0 - 2147483647  Default: 0 (disabled) | 运动在持续多少帧之后才被捕捉 |
| pre_capture | Values: 0 - 100s  Default: 0 (disabled) | 输出图像包括捕捉到运动的前几秒 |
| quality | Values: 1 - 100 Default: 75 | jpg图像的质量 |
| quiet | Values: on, off  Default: off | 安静模式，检测到运动不输出哔 |
| rotate | Values: 0, 90, 180, 270  Default: 0 (not rotated) | 旋转图像角度 |
| stream_auth_method | Values: 0,1,2  Default: 0 | 网页监控身份认证方法：0-无，1-基本，2-MD5 |
| stream_authentication | Values: username:password Default: Not defined | 网页监控用户名和密码 |
| stream_limit | Values: 0 - 2147483647  Default: 0 (unlimited) | 限制帧的数量 |
| stream_localhost | Values: on, off Default: on | 是否只能本地访问网络摄像头 |
| stream_maxrate | Values: 1 - 100  Default: 1 | 限制网络摄像头帧速率 |
| stream_port | Values: 0 - 65535  Default: 0 (disabled) | 网络摄像头端口 |
| stream_quality | Values: 1 - 100 Default: 50 | 网络摄像头传输质量 |
| switchfilter | Values: on, off  Default: off | 过滤器开关，过滤器用来区分真正的运动和噪声 |
| target_dir | Values: Max 4095 characters Default: Not defined = current working directory | 视频和图片的保存路径 |
| videodevice | Values: Max 4095 characters  Default: /dev/video0 | 摄像头设备名 |
| height | Values: Device Dependent Default: 288 | 图像高度，范围跟摄像机相关 |
| width | Values: Device Dependent Default: 352 | 图像宽度，范围跟摄像机相关 |
| process_id_file | Values: Max 4095 characters  Default: Not defined | 保存PID的文件，推荐/var/run/motion.pid |
| database_busy_timeout | Values: 0 .. positive integer  Default: 0 | 数据库等待超时时间，毫秒 |

## 启动 Motion

``` bash
# 启动 Motion
sudo motion
# 修改配置文件之后，重启motion
sudo killall -SIGHUP motion
```

浏览器进入 `http://<HOST_IP>:8081`，就可以看见你摄像头的画面了。

![rpi-motion-success.webp](https://cdn.auro.moe/post/rpi-motion-success.webp)
