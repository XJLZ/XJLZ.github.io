---
title: Grafana+Loki日志系统
date: 2026-07-21 12:00:00
tags:
- Grafana
- Loki
---

# Grafana

## 目录结构

![](https://cdn.jsdelivr.net/gh/XJLZ/md-bed/blog/1784776767141.png)

## docker-compose.yml

```yaml
services:

  loki:
    image: grafana/loki:3.6.11
    container_name: loki
    environment:
      - TZ=Asia/Shanghai
    restart: always
    ports:
      - "3100:3100"
    command:
      - -config.file=/etc/loki/local-config.yaml
    volumes:
      - ./loki-config.yml:/etc/loki/local-config.yaml
      - ./loki/data:/loki

  grafana:
    image: grafana/grafana:11.6.16
    container_name: grafana
    restart: always
    ports:
      - "3000:3000"
    environment:
      - TZ=Asia/Shanghai
      - GF_SECURITY_ADMIN_PASSWORD=admin123
    volumes:
      - ./grafana/data:/var/lib/grafana
```

## loki-config.yml

```yaml
auth_enabled: false

server:
  http_listen_port: 3100

common:
  instance_addr: 127.0.0.1
  path_prefix: /loki

  storage:
    filesystem:
      chunks_directory: /loki/chunks
      rules_directory: /loki/rules

  replication_factor: 1

  ring:
    kvstore:
      store: inmemory

schema_config:
  configs:
    - from: 2024-01-01
      store: tsdb
      object_store: filesystem
      schema: v13
      index:
        prefix: index_
        period: 24h

storage_config:
  filesystem:
    directory: /loki/chunks

ruler:
  storage:
    type: local
    local:
      directory: /loki/rules

  rule_path: /loki/rules-temp

  ring:
    kvstore:
      store: inmemory

  alertmanager_url: http://localhost:9093
```

## 开启端口

```bash
# 
sudo firewall-cmd --zone=public --add-port=3100/tcp --permanent
sudo firewall-cmd --reload
```

# Loki

**部署在需要收集日志的应用服务器上**

## 目录结构

![](https://cdn.jsdelivr.net/gh/XJLZ/md-bed/blog/1784776838525.png)

## docker-compose.yml

```yaml
services:
  promtail:
    image: grafana/promtail:3.6.11
    container_name: promtail
    environment:
      - TZ=Asia/Shanghai
    restart: always
    command:
      - -config.file=/etc/promtail/config.yml
    volumes:
      - ./promtail-config.yml:/etc/promtail/config.yml:ro
      - /home/user01/program/backend/course/logs:/data/logs:ro
      - ./positions:/tmp
```

## promtail-config.yml

```yaml
server:
  http_listen_port: 9080

positions:
  filename: /tmp/positions.yaml
# Grafana 服务
clients:
  - url: http://192.168.31.199:3100/loki/api/v1/push

scrape_configs:
  - job_name: springboot

    pipeline_stages:
      - multiline:
          firstline: '^\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2}\.\d{3}'

      - regex:
          expression: '^(?P<time>\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2}\.\d{3})'

      - timestamp:
          source: time
          format: '2006-01-02 15:04:05.000'
          location: Asia/Shanghai

    static_configs:
      - targets:
          - localhost
        labels:
          job: springboot
          app: course-service
          host: server01
          __path__: /data/logs/*.log
```

# Java Spring Boot 日志

## logback-spring.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <!-- 日志存放路径 -->
    <property name="log.path" value="./logs/"/>
    <!-- 日志输出格式 -->
    <property name="log.colorPattern"
              value="%d{yyyy-MM-dd HH:mm:ss} | %highlight(%-5level) | %boldYellow([%method,%thread,%line]) | %boldGreen(%logger) | %boldMagenta([%X{user_id:--},%X{real_name:--},%X{trace_id:--}]) | %msg%n"/>
    <property name="log.pattern"
              value="%d{yyyy-MM-dd HH:mm:ss.SSS} | %-5level | [%method,%thread,%line] | %logger{36} | [%X{user_id:--},%X{real_name:--},%X{trace_id:--}] | %msg%n"/>
    <!--控制台输出-->
    <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>${log.colorPattern}</pattern>
        </encoder>
    </appender>
    <springProperty scope="context" name="app_name" source="spring.application.name"/>

    <!-- 系统日志输出 -->
    <appender name="file_info" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${log.path}/info.log</file>
        <!-- 循环政策：基于时间创建日志文件 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 日志文件名格式 -->
            <fileNamePattern>${log.path}/info.%d{yyyy-MM-dd}.log</fileNamePattern>
            <!-- 日志最大的历史 180天 -->
            <maxHistory>180</maxHistory>
        </rollingPolicy>
        <encoder>
            <pattern>${log.pattern}</pattern>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <!-- 过滤的级别 -->
            <level>INFO</level>
            <!-- 匹配时的操作：接收（记录） -->
            <onMatch>ACCEPT</onMatch>
            <!-- 不匹配时的操作：拒绝（不记录） -->
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>

    <appender name="file_error" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${log.path}/error.log</file>
        <!-- 循环政策：基于时间创建日志文件 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 日志文件名格式 -->
            <fileNamePattern>${log.path}/error.%d{yyyy-MM-dd}.log</fileNamePattern>
            <!-- 日志最大的历史 180天 -->
            <maxHistory>180</maxHistory>
        </rollingPolicy>
        <encoder>
            <pattern>${log.pattern}</pattern>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <!-- 过滤的级别 -->
            <level>ERROR</level>
            <!-- 匹配时的操作：接收（记录） -->
            <onMatch>ACCEPT</onMatch>
            <!-- 不匹配时的操作：拒绝（不记录） -->
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>

    <!-- 用户访问日志输出  -->
    <appender name="sys-user" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${log.path}/user.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 按天回滚 daily -->
            <fileNamePattern>${log.path}/user.%d{yyyy-MM-dd}.log</fileNamePattern>
            <!-- 日志最大的历史 180天 -->
            <maxHistory>180</maxHistory>
        </rollingPolicy>
        <encoder>
            <pattern>${log.pattern}</pattern>
        </encoder>
    </appender>

    <!-- 系统模块日志级别控制  -->
    <logger name="com.cspm" level="info"/>
    <!-- Spring日志级别控制  -->
    <logger name="org.springframework" level="warn"/>

    <root level="info">
        <appender-ref ref="console"/>
    </root>

    <!--系统操作日志-->
    <root level="info">
        <appender-ref ref="file_info"/>
        <appender-ref ref="file_error"/>
        <!--<appender-ref ref="logstash"/>-->
    </root>

    <!--系统用户操作日志-->
    <logger name="sys-user" level="info">
        <appender-ref ref="sys-user"/>
    </logger>
</configuration>
```

# 配置Grafana

1. 点击左上角图标，进入 http://192.168.31.199:3000/connections/datasources

![](https://cdn.jsdelivr.net/gh/XJLZ/md-bed/blog/1784778872131.png)

2. 点击右上角 Add new data source

![](https://cdn.jsdelivr.net/gh/XJLZ/md-bed/blog/1784778905564.png)

3. 搜索loki ，点击进入配置

4. Connection 输入： http://loki:3100

5. 在最后在最下面点击按钮：Save & test

6. 点击左上角菜单，进入 Logs，Data source选择loki

7. 导入dashboard 

https://grafana.com/grafana/dashboards/?dataSource=loki&panelType=logs

https://grafana.com/grafana/dashboards/13639-logs-app/

![](https://cdn.jsdelivr.net/gh/XJLZ/md-bed/blog/1787218718405.png)

![](https://cdn.jsdelivr.net/gh/XJLZ/md-bed/blog/1787218583064.png)

13639

![](https://cdn.jsdelivr.net/gh/XJLZ/md-bed/blog/1787218677089.png)

![](https://cdn.jsdelivr.net/gh/XJLZ/md-bed/blog/1787218694913.png)
