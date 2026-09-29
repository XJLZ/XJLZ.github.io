```yaml
title: s3-client
date: 2026-09-10 11:32:00
tags:
- minio
- aws s3
- rclone
```

# MC安装

[官网]: https://dl.min.io/client/mc/release/

## Mac

```bash
brew install minio/stable/mc
```

MinIO Client (`mc`) 是 MinIO 官方提供的命令行工具，其语法和 UNIX 命令行保持一致（如 `ls`, `cp`, `cat` 等）。它不仅支持 MinIO，还兼容 AWS S3、Google Cloud Storage 等主流对象存储服务。

### 一、 基础配置管理 (`alias`)

`alias` 用于管理管理与 MinIO 服务端的连接凭证。

- **添加/更新服务端连接：**
  
  ```bash
  mc alias set <ALIAS> <ENDPOINT> <ACCESS_KEY> <SECRET_KEY>
  # 示例：
  mc alias set minio91 http://127.0.0.1:9000 admin minioadmin
  ```

- **查看配置的别名列表：**
  
  ```bash
  mc alias list
  ```

- **删除指定的别名：**
  
  ```bash
  mc alias remove <ALIAS>
  ```

### 二、 存储桶 (Bucket) 操作

- **创建存储桶：**

```bash
mc mb <ALIAS>/<BUCKET>
# 示例：
mc mb minio91/mybucket
```

- **删除存储桶（只允许删除空桶）：**

```bash
mc rb <ALIAS>/<BUCKET>
```

- **强制删除存储桶（包含桶内所有数据）：**

```bash
mc rb --force <ALIAS>/<BUCKET>
```

### 三、 对象与文件管理

- **查看目录/列表 (`ls`)：**

```bash
# 列出桶内根目录
mc ls <ALIAS>/<BUCKET>

# 递归列出目录下所有对象
mc ls --recursive <ALIAS>/<BUCKET>/<PREFIX>/
```

- **查看文件或目录占用空间 (`du`)：**

```bash
mc du <ALIAS>/<BUCKET>/<PREFIX>/
mc du --json minio91/resource/latex/
```

- **读取/查看文件内容 (`cat` / `head`)：**

```bash
mc cat <ALIAS>/<BUCKET>/<FILE_PATH>
```

- **删除文件/目录 (`rm`)：**

```bash
# 删除单文件
mc rm <ALIAS>/<BUCKET>/path/to/file.png

# 递归删除整个目录
mc rm --recursive --force <ALIAS>/<BUCKET>/path/to/dir/
```

### 四、 数据传输与同步

- **上传/下载/拷贝文件 (`cp`)：**

```bash
# 本地上传到 MinIO
mc cp local-file.txt minio91/mybucket/

# 从 MinIO 下载到本地
mc cp minio91/mybucket/remote-file.txt ./

# 递归复制目录
mc cp --recursive ./local-folder/ minio91/mybucket/folder/
```

- **数据增量同步 (`mirror`)：** `mirror` 会比对源路径与目标路径，仅同步差异部分，常用于备份和迁移。

```bash
# 将本地文件夹同步到 MinIO 桶
mc mirror ./local-dir minio91/mybucket/backup/

# 桶对桶全量增量迁移（加上 --overwrite 强制覆盖）
mc mirror --overwrite minio91/bucketA minio91/bucketB
```

### 五、 权限与预签名链接

- **生成临时外链下载地址 (`share`)：** 生成一个有时效性的 HTTP URL，无需认证即可下载文件：

```bash
# 生成有效期为 7 天的下载链接（默认 7 天，可修改如 24h）
mc share download --expire 168h minio91/mybucket/photo.jpg
mc share download --expire 1h minio91/resource/test.svg
```

- **设置存储桶策略 (`anonymous`)：** 控制桶或前缀是否允许匿名（公开）访问。

```bash
# 设置桶为公开只读
mc anonymous set download minio91/mybucket

# 设置桶为私有 (默认)
mc anonymous set private minio91/mybucket

# 查看当前策略
mc anonymous get minio91/mybucket
```

### 六、 通用高效技巧

- **管道结合 `jq` 处理 JSON：** 绝大多数 `mc` 命令加上 `--json` 标志后均可输出 JSON 数据，方便脚本处理。

```bash
mc ls --json minio91/mybucket/ | jq '.key'
```

- **管道直接上传：**

```bash
  echo "Hello World" | mc pipe minio91/mybucket/test.txt
```

## 同步

```bash
 mc mirror --overwrite --retry \
  minio91/resource/latex/6 \
  zos/szjc-uat/latex/6
```

### 排除某些文件/目录

例如排除 `.tmp` 文件：

```bash
mc mirror --overwrite --retry \
  --exclude "*.tmp" \
  minio91/resource/mathml \
  zos/ctyun-szjc-pro-data/mathml
```

排除某个目录：

```bash
mc mirror --overwrite --retry \
  --exclude "cache/*" \
  minio91/resource/mathml \
  zos/ctyun-szjc-pro-data/mathml
```

### 只传某种文件

例如只传 `.xml`：

```bash
mc mirror --overwrite --retry \
  --include "*.xml" \
  minio91/resource/mathml \
  zos/ctyun-szjc-pro-data/mathml
```

### 多个过滤规则

例如只传 XML，但排除某个子目录：

```bash
mc mirror --overwrite --retry \
  --include "*.xml" \
  --exclude "test/*" \
  minio91/resource/mathml \
  zos/ctyun-szjc-pro-data/mathml
```

### 只传某个子目录

如果你只想传 `mathml` 下的某个目录，最简单是直接指定 prefix：

```bash
mc mirror \
  minio91/resource/mathml/2025 \
  zos/ctyun-szjc-pro-data/mathml/2025
```

# rclone

## Mac

```bash
 brew install rclone
```

### 配置

执行：

```bash
rclone config
```

交互选择：

这里 **provider 选 `Other`**，因为 ZOS 是 S3 兼容存储

```bash
n) New remote
name> minio91
Storage> s3
provider> Minio
env_auth> false
access_key_id> 你的MinIO Access Key
secret_access_key> 你的MinIO Secret Key
region> 
endpoint> http://你的MinIO地址:9000
location_constraint>
acl> private
```

配置完成后测试：

```bash
rclone lsd minio91:
```

再测试 bucket：

```bash
rclone lsf minio91:resource
```

### 1. 列出当前目录下文件/文件夹及大小

```bash
rclone size minio91:resource/mathml
```

输出汇总大小和文件数，但**不是逐文件列表**。

### 2. 逐个列出文件，显示大小

```bash
rclone ls minio91:resource/mathml
```

输出格式：

```bash
123456  9/a.svg
789012  9/b.svg
```

第一列就是字节数。

### 3. 人类可读的大小（KB / MB / GB）

```bash
rclone lsl minio91:resource/mathml
```

例如：

```bash
2026-09-17 10:20:00       120 KiB  9/a.svg
2026-09-17 10:20:01       3.2 MiB  9/b.svg
```

### 4. 只看当前路径下的文件夹汇总大小

```bash
rclone size minio91:resource/mathml/9
```

### 5. 按目录层级统计大小

```bash
rclone size minio91:resource/mathml --fast-list
```

如果想看到类似：

```bash
9       1.2 GB
10      800 MB
11      2.3 GB
```

可以用：

```bash
rclone lsd minio91:resource/mathml
```

但 `lsd` 主要列目录，不显示目录大小。

## 同步

```bash
rclone copy \
  minio91:resource/latex/4/86978e670e63eed5828b9b3e1a8c286b.svg \
  zos:ctyun-szjc-pro-data/latex/4/ \
  --s3-acl public-read \
  --progress \
  --transfers 32 \
  --checkers 16 \
  --retries 10 \
  --low-level-retries 20 \
  --stats 5s
```

```bash
rclone copy \
  minio91:resource/mathml \
  zos:ctyun-szjc-pro-data/mathml \
  --include "*.svg" \
  --s3-acl public-read \
  --progress \
  --transfers 32 \
  --checkers 16 \
  --retries 10 \
  --low-level-retries 20 \
  --stats 5s
```

#### 逐项解释

| 参数                               | 含义                                                        |
| -------------------------------- | --------------------------------------------------------- |
| `rclone copy`                    | 复制文件，**不会删除目标端多余文件**                                      |
| `minio91:resource/mathml`        | 源端：remote=`minio91`，bucket=`resource`，路径=`mathml`         |
| `zos:ctyun-szjc-pro-data/mathml` | 目标端：remote=`zos`，bucket=`ctyun-szjc-pro-data`，路径=`mathml` |
| `--include "*.svg"`              | 只复制 `.svg` 文件，其他后缀跳过                                      |
| `--s3-acl public-read`           | 上传时请求设置对象 ACL 为 `public-read`                             |
| `--progress`                     | 显示实时传输进度                                                  |
| `--transfers 32`                 | 同时传输最多 **32 个文件**                                         |
| `--checkers 16`                  | 同时进行最多 **16 个检查/对比任务**                                    |
| `--retries 10`                   | 失败任务最多重试 10 次                                             |
| `--low-level-retries 20`         | 底层 HTTP/API 请求失败最多重试 20 次                                 |
| `--stats 5s`                     | 每 5 秒刷新一次统计信息                                             |
