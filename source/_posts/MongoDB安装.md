---
title: CentOS 7 安装MongoDB
date: 2020-08-10 15:32:00
tags:
- mongo
- CentOS 
---

1. ## 🛠️ CentOS 7.9 安装 MongoDB (使用 Yum 仓库)

   

   

   ### 步骤一：创建 MongoDB 仓库文件

   

   首先，您需要为 MongoDB 创建一个 `.repo` 文件，以便 `yum` 知道从哪里下载安装包。

   使用 `sudo vim` 或 `sudo nano` 创建并编辑 `/etc/yum.repos.d/mongodb.repo` 文件：

   Bash

   ```
   sudo vim /etc/yum.repos.d/mongodb.repo
   ```

   将以下内容粘贴到文件中（这里以 **MongoDB 7.0** 为例）：

   ```
   [mongodb-org-7.0]
   name=MongoDB Repository
   baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/7.0/x86_64/
   gpgcheck=1
   enabled=1
   gpgkey=https://www.mongodb.org/static/pgp/server-7.0.asc
   ```

   > **注意：**
   >
   > - `$releasever` 会自动替换为您的 CentOS 版本（7）。
   > - 如果您需要安装其他版本（如 6.0 或 5.0），请将配置文件中的 `7.0` 替换为相应的版本号。

   保存并退出文件。

   

   ### 步骤二：安装 MongoDB

   

   使用 `yum` 命令安装 MongoDB 软件包。`mongodb-org` 包会自动安装以下组件：

   - `mongodb-org-server` (MongoDB 服务器)
   - `mongodb-org-shell` (Mongo Shell)
   - `mongodb-org-mongos` (分片路由器)
   - `mongodb-org-tools` (导入/导出工具等)

   Bash

   ```
   sudo yum install -y mongodb-org
   ```

   

   ### 步骤三：启动 MongoDB 服务

   

   使用 `systemctl` 命令启动 MongoDB 服务：

   Bash

   ```
   # 启动 MongoDB 服务
   sudo systemctl start mongod
   
   # 设置 MongoDB 开机自启动
   sudo systemctl enable mongod
   
   # 检查服务状态
   sudo systemctl status mongod
   ```

   如果服务状态显示为 `active (running)`，则表示启动成功。

   ------

   

   ## ⚙️ 验证与基本配置

   ### 步骤四：验证安装与连接

   

   1. **连接到 Mongo Shell：**

      Bash

      ```
      mongosh
      ```

   2. **查看版本：** 连接成功后，在 Shell 中运行以下命令：

      JavaScript

      ```
      db.version()
      ```

      如果返回版本号，则说明安装成功。输入 `exit` 退出 Shell。

   

   ### 步骤五：配置远程访问 (CentOS 防火墙)

   

   默认情况下，MongoDB 只监听 `127.0.0.1` (本地回环)，并且 CentOS 的防火墙也会阻止外部连接。

   

   #### 1. 配置监听 IP

   

   请修改 MongoDB 配置文件 `/etc/mongod.conf`：

   Bash

   ```
   sudo vi /etc/mongod.conf
   ```

   找到 `net:` 部分，修改或添加 `bindIp` 行，以监听所有 IP：

   YAML

   ```
   # 找到这一行
   # bindIp: 127.0.0.1
   # 修改为:
   bindIp: 192.168.31.xxx
   ```

   保存并退出后，**重启 MongoDB 服务**：

   Bash

   ```
   sudo systemctl restart mongod
   ```

   

   #### 2. 开放防火墙端口

   

   使用 `firewalld` 命令开放 MongoDB 的默认端口 `27017`：

   Bash

   ```
   # 允许 TCP 端口 27017
   sudo firewall-cmd --zone=public --add-port=27017/tcp --permanent
   
   # 重新加载防火墙配置使设置生效
   sudo firewall-cmd --reload
   ```

   现在，您的 MongoDB 实例应该可以从外部网络通过服务器的 IP 地址进行连接了。