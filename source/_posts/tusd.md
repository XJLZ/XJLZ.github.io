---
title: tusd
date: 2026-07-31 11:32:00
tags:
- tusd
- Linux
---

# 部署

## 下载

**1-download.sh**

```sh
#!/usr/bin/env bash

# 下载二进制tusd
case "$(uname -m)" in
  x86_64) TUSD_RELEASE_ARCH=amd64 ;;
  aarch64|arm64) TUSD_RELEASE_ARCH=arm64 ;;
  *) echo "当前服务器架构不在部署文档支持范围内"; exit 1 ;;
esac

mkdir -p /home/user01/tusd
cd /home/user01/tusd

curl -x http://192.168.31.199:7890 --fail --location --remote-name \
  "https://github.com/tus/tusd/releases/download/v2.10.0/tusd_linux_${TUSD_RELEASE_ARCH}.tar.gz"
curl -x http://192.168.31.199:7890 --fail --location --remote-name \
  "https://github.com/tus/tusd/releases/download/v2.10.0/tusd_linux_${TUSD_RELEASE_ARCH}.tar.gz.sha256"

sha256sum --check "tusd_linux_${TUSD_RELEASE_ARCH}.tar.gz.sha256"
tar -xzf "tusd_linux_${TUSD_RELEASE_ARCH}.tar.gz"
"tusd_linux_${TUSD_RELEASE_ARCH}/tusd" -version
```

## 安装

**2-install.sh**

```sh
#!/usr/bin/env bash

# 安装二进制
case "$(uname -m)" in
  x86_64) TUSD_RELEASE_ARCH=amd64 ;;
  aarch64|arm64) TUSD_RELEASE_ARCH=arm64 ;;
  *) echo "当前服务器架构不在部署文档支持范围内"; exit 1 ;;
esac

cd /home/user01/tusd
sudo install -d -m 0755 /opt/tusd/releases/2.10.0
sudo install -m 0755 \
  "tusd_linux_${TUSD_RELEASE_ARCH}/tusd" \
  /opt/tusd/releases/2.10.0/tusd
sudo ln -sfn /opt/tusd/releases/2.10.0 /opt/tusd/current
/opt/tusd/current/tusd -version
```

## 创建用户

**3-user.sh**

```sh
#!/usr/bin/env bash

# 创建用户和存储目录
if ! id tusd >/dev/null 2>&1; then
  TUSD_NOLOGIN_PATH="$(command -v nologin)"
  test -n "$TUSD_NOLOGIN_PATH"
  sudo useradd --system \
    --home-dir /home/tusd \
    --shell "$TUSD_NOLOGIN_PATH" \
    tusd
fi

# 用户工作目录
sudo install -d -o tusd -g tusd -m 0750 /home/tusd
# 数据存储目录
sudo install -d -o tusd -g tusd -m 0750 /data/tusd


# 如果两步都成功，说明 tusd 用户对该目录有完整的读写删除权限。
sudo -u tusd touch /data/tusd/.tusd-write-test
sudo -u tusd unlink /data/tusd/.tusd-write-test
```

## 配置文件

**4-env.sh**

```sh
#!/usr/bin/env bash

# 配置运行参数
sudo install -d -o root -g root -m 0750 /etc/tusd

sudo touch /etc/tusd/tusd.env

# 覆盖写入
# 2GB 2147483648
# 同时满足 本地 localhost、内网 IP 192.168.x.x 以及 *.coursegate.cn 级联子域名
sudo tee /etc/tusd/tusd.env > /dev/null << 'EOF'
TUSD_MAX_SIZE=2147483648
TUSD_CORS_ALLOW_ORIGIN='^(https?://localhost(:[0-9]+)?|https?://192\.168\.[0-9]+\.[0-9]+(:[0-9]+)?|https?://([a-zA-Z0-9-]+\.)*coursegate\.cn(:[0-9]+)?)$'
EOF

sudo chown root:root /etc/tusd/tusd.env
sudo chmod 0600 /etc/tusd/tusd.env
```

## 创建 systemd 服务

**5-service.sh**

```sh
#!/usr/bin/env bash

# 创建 systemd 服务

sudo touch /etc/systemd/system/tusd.service

sudo tee /etc/systemd/system/tusd.service > /dev/null << 'EOF'
[Unit]
Description=Resource Centre tusd Server
After=network-online.target remote-fs.target
Wants=network-online.target
RequiresMountsFor=/data/tusd

[Service]
Type=simple
User=tusd
Group=tusd
WorkingDirectory=/home/tusd
EnvironmentFile=/etc/tusd/tusd.env
ExecStart=/opt/tusd/current/tusd \
  -host=127.0.0.1 \
  -port=1080 \
  -upload-dir=/data/tusd \
  -base-path=/files/ \
  -behind-proxy \
  -disable-download \
  -max-size=${TUSD_MAX_SIZE} \
  -hooks-http=http://127.0.0.1:18080/resource/internal/tusd/hooks-proxy \
  -hooks-enabled-events=pre-create,post-create,post-receive,post-finish,pre-terminate,post-terminate \
  -hooks-http-timeout=15s \
  -hooks-http-retry=3 \
  -hooks-http-backoff=1s \
  -cors-allow-origin=${TUSD_CORS_ALLOW_ORIGIN} \
  -cors-allow-headers=header-token,X-Upload-Token
Restart=on-failure
RestartSec=5s
TimeoutStopSec=30s
LimitNOFILE=1048576
NoNewPrivileges=true
PrivateTmp=true
PrivateDevices=true
ProtectSystem=strict
ProtectHome=false
ProtectKernelTunables=true
ProtectKernelModules=true
ProtectControlGroups=true
RestrictSUIDSGID=true
ReadWritePaths=/data/tusd

[Install]
WantedBy=multi-user.target
EOF
```

## 检查并启动服务

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now tusd.service
sudo systemctl status tusd.service
sudo journalctl -u tusd.service -n 200 --no-pager
curl --fail http://127.0.0.1:1080/metrics
```

## Nginx配置

**生成Hook密钥**

```bash
openssl rand -base64 48
```

**新建tusd.conf**

```nginx
map $http_origin $resource_upload_allow_origin {
    default "";
    "~^https?://localhost(:[0-9]+)?$" $http_origin;
    "~^https?://192\.168\.[0-9]+\.[0-9]+(:[0-9]+)?$" $http_origin;
    "~^https?://([a-zA-Z0-9-]+\.)*coursegate\.cn(:[0-9]+)?$" $http_origin;
}

upstream resource_centre_backend {
    server <resource-centre-server-A-IP>:11093;
    server <resource-centre-server-B-IP>:11093;
    server 192.168.30.117:11093;
    keepalive 32;
}

upstream resource_centre_tusd {
    server 127.0.0.1:1080;
    keepalive 16;
}

server {
    listen 9527;
    server_name 192.168.31.199;

    location = /resource-upload-auth {
        internal;
        proxy_pass http://resource_centre_backend/resource/uploads/authorize;
        proxy_pass_request_body off;
        proxy_set_header Content-Length "";
        proxy_set_header X-Original-Method $request_method;
        proxy_set_header X-Original-URI $request_uri;
        proxy_set_header X-Upload-Token $http_x_upload_token;
        proxy_set_header header-token $http_header_token;
    }

    location /files/ {
        if ($request_method = OPTIONS) {
          add_header Access-Control-Allow-Origin $resource_upload_allow_origin always;
          add_header Access-Control-Allow-Methods "POST, HEAD, PATCH, DELETE, OPTIONS" always;
          add_header Access-Control-Allow-Headers "Tus-Resumable, Upload-Length, Upload-Offset, Upload-Metadata, Upload-Defer-Length, Upload-Concat, Content-Type, header-token, X-Upload-Token" always;
          add_header Access-Control-Max-Age 86400 always;
          add_header Vary Origin always;
          add_header Content-Length 0;
          return 204;
        }
        auth_request /resource-upload-auth;

        proxy_pass http://resource_centre_tusd;
        proxy_http_version 1.1;
        proxy_request_buffering off;
        proxy_buffering off;
        client_max_body_size 0;
        proxy_read_timeout 3600s;
        proxy_send_timeout 3600s;

        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-Host $host;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Upload-Token $http_x_upload_token;
        proxy_set_header header-token $http_header_token;
    }
}

server {
    listen 127.0.0.1:18080;
    server_name _;

    location = /resource/internal/tusd/hooks-proxy {
        proxy_pass http://resource_centre_backend/resource/internal/tusd/hooks;
        proxy_set_header Content-Type application/json;
        proxy_set_header X-Tusd-Hook-Secret "<Hook密钥>";
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

**注意，若nginx（31.199）和tusd（31.91）不在同一服务器，做如下改动**

```sh
-host=192.168.31.91

-hooks-http=http://192.168.31.199:18080/resource/internal/tusd/hooks-proxy \
```

**Nginx 同步修改**

```nginx
upstream resource_centre_tusd {
    server 192.168.31.91:1080;
    keepalive 16;
}
```

Hook 代理不能继续只监听回环地址：

```nginx
server {
    listen 192.168.31.199:18080;
    server_name _;

    location = /resource/internal/tusd/hooks-proxy {
        allow 192.168.31.91;
        deny all;

        proxy_pass http://resource_centre_backend/resource/internal/tusd/hooks;
        proxy_set_header Content-Type application/json;
        proxy_set_header X-Tusd-Hook-Secret "<Hook密钥>";
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

这里必须同时保留：

```
allow 192.168.31.91;
deny all;
```

不能把 `18080` 开放给整个网络。

**自定义路径**

```nginx
location /resource-center/tus/ {

      proxy_pass http://192.168.31.91:1080/files/;

      # 增加这一行：将响应头 Location 中的 /files/ 替换为 /resource-center/tus/
      proxy_redirect http://$host/files/ http://$host/resource-center/tus/;

      proxy_http_version 1.1;
      proxy_request_buffering off;
      proxy_buffering off;
      client_max_body_size 0;
      proxy_read_timeout 3600s;
      proxy_send_timeout 3600s;

      proxy_set_header Host $host;
      proxy_set_header X-Forwarded-Host $host;
      proxy_set_header X-Forwarded-Proto $scheme;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Upload-Token $http_x_upload_token;
      proxy_set_header header-token $http_header_token;
  }
```

# 升级和回滚

升级时把新版本安装到新的 `/opt/tusd/releases/<版本>` 目录。完成官方 SHA-256 校验并执行 `<新版本tusd路径> -version` 后，再切换软链接并重启：

```bash
sudo ln -sfn /opt/tusd/releases/<已校验的新版本> /opt/tusd/current
sudo systemctl restart tusd.service
sudo systemctl status tusd.service
```

回滚到本文档版本：

```bash
sudo ln -sfn /opt/tusd/releases/2.10.0 /opt/tusd/current
sudo systemctl restart tusd.service
sudo systemctl status resource-centre-tusd.service
```