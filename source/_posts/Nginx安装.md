---
title: Nginx 安装
date: 2025-07-30 15:32:00
tags:
- Nginx
- Centos
---

# Centos7

#### 安装依赖

```bash
1 gcc 安装
yum install -y gcc gcc-c++
2 PCRE pcre-devel 安装
yum install -y pcre pcre-devel
3 zlib 安装
yum install -y zlib zlib-devel
4 OpenSSL 安装
yum install -y openssl openssl-devel
```

#### 脚本

```bash
#!/bin/bash
#安装目录
data="usr/local"
#压缩包目录
default="home/mirror/nginx"
#安装依赖
#1 gcc 安装
yum install -y gcc gcc-c++
#2 PCRE pcre-devel 安装
yum install -y pcre pcre-devel
#3 zlib 安装
yum install -y zlib zlib-devel
#4 OpenSSL 安装
yum install -y openssl openssl-devel
## 解压
tar -xvf ./nginx-1.19.1.tar.gz -C /${data}/

mv /${data}/nginx-1.19.1 /${data}/nginx

##进入nginx目录
cd /${data}/nginx
## 配置
./configure --prefix=/usr/local/nginx

# make
make
make install
rm -rf logs
mkdir logs
chmod 700 logs
# cd到刚才配置的安装目录/usr/loca/nginx/
cd /usr/local/nginx
./sbin/nginx -t

#在文件的最后一行加入文件引入
cd conf
sed -i '$d' nginx.conf
echo 'include /'${default}'/default.conf;' >> nginx.conf
echo '}' >> nginx.conf

firewall-cmd --zone=public --add-port=80/tcp --permanent

systemctl restart firewalld.service
```

default.conf

```conf
upstream  /backApi {
    server 127.0.0.1:9100;
}
server {
    listen       80 default_server;
    server_name  www.xxxx.com;

    index index.html;
    location / {
        alias   /root;
        index  index.html index.htm;
    }
    location /api {
        rewrite ^/api/(.*)$ /$1 break;
        proxy_pass   http://backApi;
        proxy_set_header   Host             $host:$server_port;
        proxy_set_header   X-Real-IP        $remote_addr;
        proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
        proxy_set_header   Proxy-Client-IP  $remote_addr;
    }

}

server {
    listen  8888
    server_name  www.xxxx.com;

    location /wqe/ {
        alais /home/xxxx
    }

}
```

#### 目录浏览配置

```
autoindex_localtime on; 
默认为off，显示的文件时间为GMT时间。
改为on后，显示的文件时间为文件的服务器时间。
autoindex_exact_size off;    
默认为on，显示出文件的确切大小，单位是bytes。
改为off后，显示出文件的大概大小，单位是kB或者MB或者GB。
charset utf-8,gbk;
解决中文乱码问题。
```

# CentOS Stream release 10 (Coughlan)

```shell
sudo vi /etc/yum.repos.d/nginx.repo

[nginx-stable]
name=nginx stable repo
baseurl=https://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key
module_hotfixes=true

# 刷新 DNF 缓存
sudo dnf clean all
sudo dnf makecache
# 卸载旧的系统内置 Nginx 包
sudo dnf remove nginx nginx-core -y
# 安装
sudo dnf install nginx -y
```
