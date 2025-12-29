---
title: Centos7安装elasticsearch
date: 2020-11-19 09:32:00
tags:
- elasticsearch
- Centos7
---

#### 下载安装包

官网地址  https://www.elastic.co/cn/downloads/past-releases#elasticsearch

选择自己要下载的版本，这里以v7.3.2为例

#### 安装&脚本

​	es是要用非root用户启动

```
#!/bin/bash
data="/usr/local"
# 设置用户和用户组
user="es"
group="elasticsearch"
password="123456"
groupadd ${group}

useradd ${user}

passwd ${user} ${password}

usermod -G ${group} ${user}

tar -xvf elasticsearch-7.3.2-linux-x86_64.tar.gz

sudo mv elasticsearch-7.3.2 ${data}/es

sudo chown -R ${user}:${group} ${data}/es

echo "vm.max_map_count=262144" >> /etc/sysctl.conf

sysctl -p 
# 修改每个进程最大同时打开文件数和最大线程个数
echo "* hard nofile 65536" >> /etc/security/limits.conf
echo "* soft nofile 65536" >> /etc/security/limits.conf
echo "* soft nproc 4096" >> /etc/security/limits.conf
echo "* hard nproc 4096" >> /etc/security/limits.conf

```

#### 安装插件

如：ik分词插件

下载安装包 https://github.com/medcl/elasticsearch-analysis-ik/releases

解压到刚刚安装的es目录 如： /usr/local/es/plugins

#### 配置elasticsearch.yml（无密码）

进入安装目录的config目录，编辑vim elasticsearch.yml

##### 集群配置：

```
cluster.name: cluster-es

node.name: node-1

network.host: 192.168.186.140

node.master: true
node.data: true
http.port: 9200

http.cors.enabled: true
http.cors.allow-origin: "*"
http.cors.allow-methods: OPTIONS, HEAD, GET, POST, PUT, DELETE
http.cors.allow-headers: "X-Requested-With, Content-Type, Content-Length, X-User"


cluster.initial_master_nodes: ["node-1","node-2","node-3"]

discovery.seed_hosts: ["192.168.186.140:9300", "192.168.186.151:9300", "192.168.186.152:9300"]
xpack.security.enabled: true
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.verification_mode: certificate
xpack.security.transport.ssl.keystore.path: elastic-certificates.p12
xpack.security.transport.ssl.truststore.path: elastic-certificates.p12
```



##### 单节点配置：

主要修改：

```
node.name: node-1
network.host: 192.168.186.140
http.port: 9200
cluster.initial_master_nodes: ["node-1"]
http.cors.enabled: true
http.cors.allow-origin: "*"
http.cors.allow-headers: "X-Requested-With, Content-Type, Content-Length, X-User"
```



#### 集群配置elasticsearch.yml（SSL认证/证书）

**生成证书--->分发证书到节点---->修改配置文件----->elasticsearch各节点为xpack.security.transport添加密码**

##### 生成证书(密码1)

```
bin/elasticsearch-certutil  ca   
bin/elasticsearch-certutil  cert --ca elastic-stack-ca.p12
```

##### 分发证书到节点

```
scp elastic-certificates.p12 es@192.168.186.151:/home/es/elasticsearch-7.3.2/config/
scp elastic-certificates.p12 es@192.168.186.152:/home/es/elasticsearch-7.3.2/config/
```

##### 修改配置文件

编辑vim elasticsearch.yml，添加如下配置：

```
xpack.security.enabled: true
xpack.security.transport.ssl.enabled: truexpack.security.enabled: true
xpack.security.transport.ssl.verification_mode: certificate
xpack.security.transport.ssl.keystore.path: elastic-certificates.p12
xpack.security.transport.ssl.truststore.path: elastic-certificates.p12
```

##### 各节点为xpack.security.transport添加密码(密码为创建证书时设置的密码)

```
bin/elasticsearch-keystore add xpack.security.transport.ssl.keystore.secure_password
bin/elasticsearch-keystore add xpack.security.transport.ssl.truststore.secure_password
```

##### 非root用户重启elasticsearch服务

##### 设置密码（需要es服务正在运行中）

```
bin/elasticsearch-setup-passwords interactive  
```

![image-20201119102153473](https://i.loli.net/2020/11/19/kONVlUxq5BFfyvC.png)



#### 修改kibana配置文件（有密码）

**1.修改kibana.yml配置文件，添加以下配置**

```
#进入kibana安装目录
cd /xxxx/kibana-7.2.0-linux-x86_64/config

#修改配置文件
vim kibana.yml

#添加配置
elasticsearch.username: "elastic"
elasticsearch.password: "xxx"
```

**2.非root用户重启kibana服务**

**3.登录kibana**



#### 常用命令

##### 1.查询索引文档数量

```
curl --noproxy '*' --user 'user:passwd' -X GET 'http://ip:9200/_cat/count/index_name?v'

epoch      timestamp count
1614838001 06:06:41  122090


curl --noproxy '*' --user 'user:passwd' -X GET 'http://ip:9200/_cat/indices?v
```

##### 2.查看es状态

```
curl --noproxy '*' --user 'user:passwd' -X GET 'http://ip:9200/_cluster/health?pretty'

{
  "cluster_name" : "fx",
  "status" : "yellow",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 22,
  "active_shards" : 22,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 11,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 66.66666666666666
}
```

##### 3.删除索引下的数据

```
curl --noproxy '*' --user 'user:passwd' -X POST 'http:ip//:9200/cpsl_library_v1.12/_delete_by_query?pretty' -d '{"query": {"match_all": {}}}'

curl --noproxy '*' --user 'elastic:v2m$yYlvN!' -X POST  'http://ip:9200/cpsl_library_v1.12/_delete_by_query?refresh&pretty' -H 'Content-Type: application/json' -d '{"query": {"match_all": {}}}'
```

