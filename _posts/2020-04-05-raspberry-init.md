---
layout: post
title: 树莓派系列--初始化
categories: raspberry
description: 树莓派系列--初始化
keywords: raspberry
---

# init
3号终于回到武汉，接下来就是一周的隔离。想到即将到来的无聊感，决定搞个树莓派玩玩，顺便重温下linux的知识和操作。写个博客也作为自己的笔记记录下。

- 主板：树莓派4B，外加一套散热
- 摄像头：树莓派3代、4代专用，500万像素
- 硬盘底座：奥瑞科，旧电脑拆下来的两块硬盘，搞个底座接入
- 闪迪TF卡：白卡，因为要作为系统内存卡，红卡还是差了些
- 读卡器

# 初始化配置
## 镜像制作
- 镜像：[官方镜像](https://www.raspberrypi.org/downloads/raspbian/)
- 写入工具：[balenaEtcher-Portable-1.5.80](https://www.balena.io/etcher/)
- ssh工具：termius，免费版够用了
- 桌面访问工具：VNC viewer
  
过程很简单，略过

## 系统配置
镜像制作完成后，在tf卡中新建空白文件"ssh"，以开启ssh权限

主板接网线、硬盘（可不接）、摄像头，插入TF卡，上电！顺利的话主板红灯常亮、黄灯闪烁后灭，说明系统稳定。

在路由器中看下树莓派IP，就可以ssh到板子了，初始用户名：pi，密码：raspberry

进入配置：`sudo raspi-config`，依次进行一下配置：
- 改密码：1
- 改时区：4 Localization -> I2 timezone
- 开启VNC(图形界面，后续可通过VNC Viewer进入桌面)：5 Interfacing Options -> P3
- 开启camera: 5 Interfacing Options -> P1
- 开启WiFi，并填入ssid和密码: 2 Network Options -> N2
- 修改图形界面分辨率：7 advanced option -> A5 resolution:除了第一个都能选，我选的1280*720

退出config，直接重启。

## 修改源并更新
在这之前可以修改下源，`sudo vi /etc/apt/sources.list`，注释掉官方源，添加中科大源（以下适用于buster版本）：
```
# 官方源 
# deb http://raspbian.raspberrypi.org/raspbian/ buster main contrib non-free rpi
# Uncomment line below then 'apt-get update' to enable 'apt-get source'
#deb-src http://raspbian.raspberrypi.org/raspbian/ buster main contrib non-free rpi

# 中科大 
deb http://mirrors.ustc.edu.cn/raspbian/raspbian/ buster main contrib non-free rpi
deb-src http://mirrors.ustc.edu.cn/raspbian/raspbian/ buster main contrib non-free rpi
```
再修改下系统源：`sudo vi /etc/apt/sources.list.d/raspi.list`:
```
# 官方源
# deb http://archive.raspberrypi.org/debian/ buster main
# Uncomment line below then 'apt-get update' to enable 'apt-get source'
#deb-src http://archive.raspberrypi.org/debian/ buster main

# 中科大
deb http://mirrors.ustc.edu.cn/archive.raspberrypi.org/debian/ buster main ui
```
本人习惯用vim，也可以使用nano，更简单些。

接下来更新
```
# 更新软件源列表
sudo apt-get update
# 更新软件版本
sudo apt-get upgrade
sudo apt-get dist-upgrade
# 更新系统内核
sudo rpi-update
```

## 安装常用软件
- vim

```
sudo apt-get install vim
```
配置: `sudo vim /etc/vim/vimrc`

```
# 修改tab为4个空格，末尾添加
set ts=4
set expandtab
set autoindent
```

## 时间同步
上述更新的时候发现会报错：
```
E: Release file for http://raspbian.raspberrypi.org/raspbian/dists/buster/InRelease is not valid yet (invalid for another 63d 18h 32min 43s). Updates for this repository will not be applied.
E: Release file for http://archive.raspberrypi.org/debian/dists/buster/InRelease is not valid yet (invalid for another 63d 18h 31min 15s). Updates for this repository will not be applied.
```
date竟然发现时间不对，通过传统的ntp也无法同步时间，最后用[htpdate](https://github.com/iridium77/htpdate)成功同步
```
sudo apt install htpdate
sudo htpdate -t -s neu.edu.cn
date
```

接下来就可以正式玩起来啦



