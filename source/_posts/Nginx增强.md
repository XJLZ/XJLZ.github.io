---
title: Nginx 增强
date: 2026-07-27 13:32:00
tags:
- Nginx
- Linux
---

# log

```bash
# 自定义安装的nginx
/data/soft/nginx/logs/*.log {
    # 切割周期
    daily
    # 保留数量
    rotate 30
    # 日志不存在时不报错
    missingok
    # 空日志不切割
    notifempty
    # gzip压缩历史日志
    compress
    # 延迟一次压缩
    delaycompress
    # 使用日期作为后缀
    dateext
    # 日期格式
    dateformat -%Y%m%d
    # 脚本只执行一次
    sharedscripts

    postrotate
        if [ -f /data/soft/nginx/logs/nginx.pid ]; then
            kill -USR1 "$(cat /data/soft/nginx/logs/nginx.pid)"
        fi
    endscript
}

# yum/dnf安装的nginx
/var/log/nginx/*.log {
    daily
    rotate 30
    missingok
    notifempty
    compress
    delaycompress
    dateext
    dateformat -%Y%m%d
    sharedscripts

    postrotate
        if [ -f /run/nginx.pid ]; then
            kill -USR1 "$(cat /run/nginx.pid)"
        fi
    endscript
}
```

然后检查配置：

```bash
logrotate -d /etc/logrotate.d/nginx
```

手动验证一次切割：

```bash
logrotate -f /etc/logrotate.d/nginx
```

整个过程不需要重启 Nginx，也不会中断正常请求。

日志切割的完整过程是：

```bash
access.log
    ↓ logrotate 重命名
access.log-20260727
    ↓ 向 Nginx 发送 USR1
Nginx 创建并写入新的 access.log
```

# gzip

```nginx
gzip on; # 开启Gzip压缩
gzip_min_length 1k; # 仅压缩大于1KB的响应（避免小数据压缩反而增加体积）
gzip_http_version 1.1; # 仅对HTTP 1.1及以上协议启用（避免老协议兼容性问题）
gzip_comp_level 6; # 压缩级别（1-9，6为兼顾压缩率与性能的最优值：1级最快但压缩率低，9级反之）
gzip_types
    text/plain
    text/css
    application/json
    application/javascript
    application/xml
    application/xml+rss
    text/javascript
    image/svg+xml; # 需压缩的MIME类型（重点包含教材接口的XML/JSON格式）
gzip_vary on; # 向客户端返回Vary: Accept-Encoding头，告知客户端支持压缩（CDN场景必备）
gzip_proxied any; # 对代理请求也启用压缩（若Nginx反向代理后端服务，必须配置此项）
```


