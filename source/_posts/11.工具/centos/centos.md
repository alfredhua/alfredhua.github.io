---
title: centos下常见操作
date: 2023-09-04
keywords:  centos下常见操作
description:  常见操作,vim,无线连接，挂在硬盘
top: false
tags:
  -  centos
categories:
  -  centos
---
##  连接无线网络
> sudo wpa_supplicant -B -i wlp4s0 -c <(wpa_passphrase "[SSID]" "[PIN]") 
> sudo dhclient wlp4s0


## 安装vim

> yum install vim

## 开启公钥

> ssh-keygen -t rsa
> vim  /etc/ssh/sshd_config
> 修改PubkeyAuthentication yes
> .ssh/authorized_keys 中增加公钥

## 笔记本关闭盖子，不关机

> vim  /etc/systemd/logind.conf
> 修改 HandleLidSwitch=ignore
> systemctl restart systemd-logind



## 挂载硬盘

> 显示当前所有的硬盘，fdisk -l
> 找到要挂载的硬盘。 如:磁盘 /dev/sdb：500.1 GB,
> fdisk /dev/sdb
> 根据提示，依次输入"n"，"p" "1"，两次回车，"wq"，分区就开始了，很快就会完成。
> 格式化硬盘： mkfs.ext4 /dev/sdb
> mkdir /mnt
> mount /dev/sdb /mnt
> 设置开机自动挂载: vi /etc/fstab 
> 在vi中输入i进入INERT模式，将光标移至文件结尾处并回车，将下面的内容复制/粘贴，然后按Esc键，输入":wq"(不含双引号)保存并退出
> /dev/sdb    /data    ext4    defaults    0 0

## centos下挂在 extfat格式文件
1.yum install -y http://li.nux.ro/download/nux/dextop/el7/x86_64/nux-dextop-release-0-5.el7.nux.noarch.rpm
2.yum install exfat-utils fuse-exfat

mount -t extfat /dev/sdc /data

## 修改阿里源
> cd /etc/yum.repos.d
> wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
> yum makecache

## 安装nginx

>  yum -y install epel-release
>  yum install nginx

## 安装
> yum install wget

> yum install unzip


## 关闭防火墙

> systemctl stop firewalld.service


## 自定义服务

>  目录：/usr/lib/systemd/system
>
>  增加配置文件:tomcate.service
>  [Unit]
>  Description=java tomcat project
>  After=tomcat.service
>
>  [Service]
>  Type=forking
>  User=users
>  Group=users
>  PIDFile=/usr/local/tomcat/tomcat.pid
>  ExecStart=/usr/local/tomcat/bin/startup.sh
>  ExecReload=
>  ExecStop=/usr/local/tomcat/bin/shutdown.sh
>  PrivateTmp=true
>
>  [Install]
>  WantedBy=multi-user.target

```
systemctl start  tomcat.service
```

