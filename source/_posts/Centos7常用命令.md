---
title: Centos7常用命令
date: 2020-08-10 15:32:00
tags:
- Centos7
---

#### 开端口

```
【单个】
firewall-cmd --zone=public --add-port=2181/tcp --permanent
【范围】
firewall-cmd --zone=public --add-port=30000-30999/tcp --permanent
【删除】
firewall-cmd --zone=public --remove-port=8080/tcp --permanent
【刷新配置】
firewall-cmd --reload
#找出公共区域的所有设置
firewall-cmd --zone=public --list-all
firewall-cmd --list-all
```

####  重启防火墙：

```
systemctl restart firewalld.service
systemctl stop firewalld.service
```

####  开机启动配置

```
/etc/rc.local
可加入命令，如：nohup java -jar text.jar
```

#### shell脚本执行报错（ /bin/bash^M: 坏的解释器: 没有那个文件或目录）

```
sed -i "s/\r//" install.sh
```

#### 查看系统版本

```
cat /proc/version
cat /etc/redhat-release
```

#### 查看文件/文件夹大小

```
指定目录的总大小，可以使用 du -sh 目录名称，du -sh test/ 或 du -h test/
当前目录大小 du -sh 或  du -h
文件大小 du -h index.html
```

#### 查看系统时间

- 查看系统时间的命令： date

#### 查看硬件时间

- 查看硬件时间的命令：  hwclock

#### 时间服务器上的时间同步的方法

```
安装ntpdate工具
# yum -y install ntp ntpdate

设置系统时间与网络时间同步
# ntpdate cn.pool.ntp.org

将系统时间写入硬件时间
# hwclock --systohc
```

#### 安装net-tools

```
yum install -y net-tools.x86_64 
```

#### 安装lrzsz

```
yum -y install lrzsz
```

