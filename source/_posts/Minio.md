

---
title: Minio
date: 2024-01-11 12:00:00
tags:
- Centos7
- Minio
---



#### 环境

**Centos7**

#### 安装Minio

```
1.下载
	wget https://dl.min.io/server/minio/release/linux-amd64/minio
	或
	curl https://dl.min.io/server/minio/release/linux-amd64/minio > minio
2.添加执行权限
	chmod +x minio
3.创建存储目录
	mkdir /home/minio/data
4.防火墙开启端口
	firewall-cmd --zone=public --add-port=9999/tcp --permanent
	firewall-cmd --reload
5.运行（指定端口）---默认端口是9000
	nohup ./minio server --address 本机IP:9999 /home/minio/data/ > /home/minio/minio.log 2>&1 &
6.默认端口启动
	nohup ./minio server /home/minio/data/ > /home/minio/minio.log 2>&1 &
7.访问web管理界面
	IP:9999
	默认账号密码都是 minioadmin
```

#### 新版本minio

1. 本体下载

```
wget https://dl.minio.org.cn/server/minio/release/linux-amd64/minio
```

2.  下载配置到 /etc/systemd/system/

```
wget https://raw.githubusercontent.com/minio/minio-service/master/linux-systemd/minio.service
	删除文件里的 User/Group
```

   

3. vim /etc/default/minio

```
# 指定数据存储目录(注意这个目录要存在)
MINIO_VOLUMES="/home/user01/minio/data"

# 指定监听端口(也可以监听指定网卡，如 127.0.0.1:9199)
MINIO_OPTS="--address :9000  --console-address :9001"

# 账号
MINIO_ROOT_USER="admin"

# 密码
MINIO_ROOT_PASSWORD="miniopwd"


# Access key 建议与MINIO_ROOT_USER相同
MINIO_ACCESS_KEY="admin"

# Secret key 建议与MINIO_ROOT_PASSWORD相同
MINIO_SECRET_KEY="miniopwd"

# 区域值，这是完全自己写的，比如你愿意的话写“abcd”也行，但标准格式是“国家-区域-编号”，
# 如“中国-华北-1号”就可写成“cn-north-1”，又比如“美国-西部-2号”可写成“us-west-1”
MINIO_REGION="cn-hanghzou-1"

# 域名 没域名就写 IP:端口 端口为MINIO_OPTS里指定的
#MINIO_DOMAIN=192.168.31.91:9001
```

### 启动

```
systemctl daemon-reload
systemctl start minio
systemctl status minio
systemctl stop minio

```



#### 安装MC

```
1.下载
	wget https://dl.min.io/client/mc/release/linux-amd64/mc
	或
	curl https://dl.min.io/client/mc/release/linux-amd64/mc > mc
2.添加执行权限
	chmod +x mc
```

#### 给MC添加minio

```
./mc config host add minio http://本机IP:9999 minioadmin minioadmin
```

![image-20210430113608186](https://i.loli.net/2021/04/30/rBFEzbXKsPgUjy7.png)

#### 使用MC操作minio

MinIO Client完全指南：http://docs.minio.org.cn/docs/master/minio-client-complete-guide