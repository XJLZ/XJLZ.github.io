---
title: MyCat安装使用
date: 2021-05-28 11:32:00
tags:
- Centos7
- MySql
- MyCat
---

#### 1.下载安装

下载目录：http://dl.mycat.org.cn/

##### 上传 解压

```
tar -zxvf Mycat-server-1.6.7.4-release-20200105164103-linux.tar.gz 
```

##### 重要的三个配置文件

```
cd mycat/conf
    schema.xml		# 定义逻辑库,表,分片节点等内容
    rule.xml		# 定义分片规则
    server.xml		# mycat服务配置
```

#### 2.修改配置文件

##### 修改server.xml

*<!-- 配置 MyCat 用户名 密码 -->*

```
 <user name="mycat" defaultAccount="true">
     <property name="password">123456</property>
     <property name="schemas">TESTDB</property>
     <property name="defaultSchema">TESTDB</property>
 </user>
```

![image-20210528110254129](https://i.loli.net/2021/05/28/XKktJQwYia36GCF.png)

##### 修改schema.xml

```
<?xml version="1.0"?>
<!DOCTYPE mycat:schema SYSTEM "schema.dtd">
<mycat:schema xmlns:mycat="http://io.mycat/">
		<!-- 相当与一个虚拟数据库,这个库中的表格映射了test库中的表 -->
        <schema name="TESTDB" checkSQLschema="true" sqlMaxLimit="100" dataNode="dn1">
        </schema>
        <!-- 数据节点 host150 机器内的 mycat 数据库 -->
        <dataNode name="dn1" dataHost="host150" database="mycat" />
        <dataHost name="host150" maxCon="1000" minCon="10" balance="2"
                          writeType="0" dbType="mysql" dbDriver="jdbc" switchType="1"  slaveThreshold="100">
                <!-- 心跳 默认就好 -->
                <heartbeat>select user()</heartbeat>
                <!-- 读写分离 -->
                <writeHost host="hostM1" url="jdbc:mysql://192.168.186.150:3306" user="root" password="root">
                         <readHost host="hostS1" url="jdbc:mysql://192.168.186.151:3306" user="root" password="root"></readHost>
                </writeHost>
        </dataHost>
</mycat:schema>
```

![image-20210528110517024](https://i.loli.net/2021/05/28/DJvq1XdKa6mpUo4.png)

```
读写分离 一般设置为 1 或者 3，这里为了看清效果 设置为 2
balance="2"
负载均衡类型，目前的取值有3种：
1. balance="0", 不开启读写分离机制，所有读操作都发送到当前可用的 writehost 上。 
2. balance="1"，全部的 readhost 与 standby wtirehost 参与select语句的负载均衡，简单的说，当双主双从模式(M1->S1，M2->S2，并且M1与M2互为主备)，正常情况下，M2,S1,S2 都参与 select 语句的负载均衡。 
3. balance="2"，所以读操作都随机的在 writehost、readhost 上分发。
4. balance="3"，所有读请求随机的分发到 writehost 对应的 readhost 执行，writehost不负担读压力，注意 balance=3 只有1.4及其以后版本有，1.3没有。
```



#### 3.启动&登录

```
bin/mycat start
```

```
mysql -umycat -p123456 -P 8066 -h 192.168.186.150
```

![image-20210528112139346](https://i.loli.net/2021/05/28/umHdGDIxKP38aqX.png)

#### 4.搭建读写分离

##### 一主一从

###### 1.主机配置

* vim /etc/mt.cnf 

```
[mysqld]
# 主服务器唯一 ID
server_id=1
# 启用二进制日志
log-bin=mysql-bin
# 设置 logbin 格式
binlog-format=STATEMENT
# 设置不要复制的数据库
binlog-ignore-db=mysql
binlog-ignore-db=infomation_schema
# 设置需要复制的数据库
binlog-do-db=mycat
```

##### *<!-- binlog_format 三种格式-->*

```
# master写入执行的SQL语句到binlog中，从库读取这些SQL语句并执行，
# 这种基于SQL语句的复制方式是MySQL最早支持的复制方式。
# 缺点 执行某些函数时可能造成主从不一致的情况 如 update ... time=now()
binlog_format=statement

# 可以将master的binlog_format配置成同时使用基于statement和row两者的组合格式，
# 它记录日志取决于修改的类型，选择合适的格式来记录该修改。
# 默认情况下使用statement格式记录日志，特定情况下转换成基于row格式记录。
# 缺点 识别不了特定的名称[系统变量] 如 @@host name
binlog_format=mixed
 
# MySQL5.7.7版本之后，把binlog_format的默认值修改为了row，
# master将修改表的event写入binlog中，并且master将该binlog发送给slave，
# slave重放binlog中的event。基于row格式复制时最安全的复制，slave需要的行锁更少。
# 复制过程中建议使用row格式，其他格式可能会造成主从数据不一致的情况。
# 缺点 执行 100 万次更新,会记录一百万次
binlog_format=row
```

###### 2.从机配置

* vim /etc/mt.cnf 

```
[mysqld]
# 从机服务器唯一 ID
server_id=2
# 启用中继日志
relay-log=mysql-relay
```

**主从机都关闭防火墙或者开启3306端口 ，重启 mysql 服务**

[防火墙]: Centos7常用命令.md

##### 主机上创建账户并授权 slave 

```
1.登录数据库
mysql -uroot -proot
2.创建账户并授权 slave 
GRANT REPLICATION SLAVE ON *.* TO 'slave'@'%' IDENTIFIED BY 'sasa';
3.刷新权限
FLUSH PRIVILEGES;
4.查看主机状态
show master status;
```

+---------------------------+-------------+-----------------------+--------------------------------------+----------------------------+
| File                           | Position | Binlog_Do_DB | Binlog_Ignore_DB               | Executed_Gtid_Set |
+---------------------------+-------------+-----------------------+--------------------------------------+----------------------------+
| mysql-bin.000008 |     3703 | mycat                 | mysql,infomation_schema |                                  |
+---------------------------+-------------+-----------------------+--------------------------------------+----------------------------+

ps: **此后，主机不要做任何操作**

##### 从机配置

```
1.登录数据库
mysql -uroot -proot
2.重置
mysql> stop slave;  
mysql> reset master;
3.配置
CHANGE MASTER TO MASTER_HOST='192.168.186.150',
MASTER_USER='slave',
MASTER_PASSWORD='sasa',
MASTER_LOG_FILE='mysql-bin.000008',MASTER_LOG_POS=3703;
4.从机执行复制功能
start slave;
5.查看从机服务状态
show slave status\G;
```

![image-20210528130555085](https://i.loli.net/2021/05/28/yMWzaxAohXFscLS.png)

![image-20210528131440355](https://i.loli.net/2021/05/28/XOS6Cm8Ict5NDvk.png)

遇到的问题： https://blog.csdn.net/leshami/article/details/43854505

#### 验证

进入主机mysql

插入一条数据

```
insert into user values(8,@@hostname);
```

查看主从机数据

主：

![image-20210528140816254](https://i.loli.net/2021/05/28/jpmbc9KJuny8fAx.png)

从：

![image-20210528140843508](https://i.loli.net/2021/05/28/njtefbCBFz5g3l6.png)



主机登录mycat

```
 mysql -umycat -p123456 -P 8066 -h 192.168.186.150
 use TESTDB;
 select * from user where id = 8;
```

![image-20210528141021129](https://i.loli.net/2021/05/28/1ew5CDNQMpcOLsU.png)