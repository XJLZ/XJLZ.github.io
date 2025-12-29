---
title: Docker-Compose
date: 2021-01-18 15:32:00
tags:
- Centos7
- Docker
---

#### 1.下载

https://github.com/docker/compose

#### 2.安装

```shell
sudo chmod +x /usr/local/bin/docker-compose
```
#### 3.常用命令 （当前目录默认在docker-compose.yml目录下）

```
首次构建和启动命令
docker-compose up --build -d
启动
docker-compose up
后台启动
docker-compose up -d
查看容器状态
docker-compose ps
查看日志
docker-compose logs 
实时查看日志（持续输出）（类似 tail -f）
docker-compose logs -f --tail 100 
# 将日志输出到文件
docker-compose logs  > app.log
# 不带颜色
docker-compose logs --no-color > full_logs.txt
停止
docker-compose stop
重启
docker-compose restart 
停止与清理
docker-compose down
```


#### 4.编写docker-compose.yml

```
version: '3'

services:
  docker-test:
    image: docker-test:latest # 指定基础镜像
    container_name: docker-testr # 容器名称
    volumes:
      - "/docker/log:/log" #日志挂载
    command:
      - "--mysqlurl=192.168.33.10"
      - "--username=root"
      - "--password=root"
    ports:
      - 8001:8001 # 端口映射
  mysql:
    image: mysql:5.7.24
    container_name: mysql
    environment:
      MYSQL_ROOT_PASSWORD: 123456
    ports:
      - 3306:3306
    volumes:
      - /febs/mysql/data:/var/lib/mysql #挂载 MySQL数据
  redis:
    image: redis:4.0.14
    container_name: redis
    command: redis-server /usr/local/etc/redis/redis.conf --appendonly yes
    volumes:
      - /febs/redis/data:/data #挂载 Redis数据
      - /febs/redis/conf/redis.conf:/usr/local/etc/redis/redis.conf #挂载 Redis配置
    ports:
      - 6379:6379
```

**挂载/映射--->   宿主机:docker镜像**

#### 5.启动镜像

在`docker-compose.yml`目录下运行`docker-compose up -d` 后台启动服务

#### 6.停止运行

在`docker-compose.yml`目录下运行`docker-compose stop`

