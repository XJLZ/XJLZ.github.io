---
title: Nginx反向代理导致请求header头信息丢失
date: 2020-08-10 15:32:00
tags:
- Nginx
- Centos7
---

**解决方法：**

> 方法一：NGINX代理时加上请求头信息：
>
>     location /fxapi {
>         proxy_pass   http://fxApi/;
>         proxy_set_header   Host             $host:$server_port;
>         proxy_set_header   X-Real-IP        $remote_addr;
>         proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
>         proxy_set_header   Proxy-Client-IP  $remote_addr;
>     }
> 由于前端代码request的header中包含_，所以这个配置没有生效
>
>  
>
> 方法二：从根本解除nginx的限制，nginx默认request的header的那么中包含’_’时，会自动忽略掉。http部分中添加如下配置：underscores_in_headers on; （默认 underscores_in_headers 为off）

![image-20210624143610880](https://i.loli.net/2021/06/24/ecgwVKGZrAW1SPf.png)