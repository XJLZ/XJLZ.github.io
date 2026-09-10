```yaml
title: minio-mc
date: 2026-09-10 11:32:00
tags:
- minio
```

# 安装

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
