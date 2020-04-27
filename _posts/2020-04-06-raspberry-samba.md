---
layout: post
title: 树莓派系列--samba配置
categories: raspberry
description: 树莓派系列--samba配置
keywords: raspberry
---

对于了解linux的小伙伴来说samba就不需要多做介绍了，直接上配置过程。

# 安装
```
sudo apt-get update
sudo apt-get install samba samba-common-bin
```
# 创建共享目录
```
mkdir /home/pi/share
sudo chmod 777 /home/pi/share
```
# 配置
```
sudo vim /etc/samba/smb.conf

# 注释掉[homes]及下面选项内容，目的是隐藏home下文件内容
#在最后添加配置内容
[share]
   path = /home/pi/share
   valid users = pi
   browseable = yes
   public = yes
   writable = yes
   guest ok = no
   read only = no
  
# 重启
sudo /etc/init.d/samba restart
```

# 添加共享用户
```
# 设置共享用户，并重复设置密码
sudo smbpasswd -a pi
```

# 访问
windows:网络和共享中心<br>
Linux：文件夹的路径中输入`smb:\\[ip]`


---------------
# \[参考\]

[Samba服务的配置总结](https://www.cnblogs.com/kevingrace/p/8662088.html)<br>
[树莓派简单配置samba，共享文件](https://blog.csdn.net/byforson/article/details/90900640)