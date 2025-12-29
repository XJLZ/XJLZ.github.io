---
title: Centos7安装FFmpeg
date: 2022-04-02 09:32:00
tags:
- ffmpeg
- Centos7
---



### 下载源码

http://www.ffmpeg.org/releases/

选择对应版本的压缩包，例如：ffmpeg-5.0.tar.gz      

### 安装步骤

1. 上传到服务器

2. 安装编译环境

   ```
   sudo yum -y install make automake gcc gcc-c++ cc kernel-devel glibc-devel
    
   sudo yum -y install libxml2 libxml2-devel libxslt libxslt-devel
   
   sudo yum -y install yasm
   ```

   

3. 解压

   ```
   tar zxvf ffmpeg-4.4.tar.gz
   ```

4. 编译安装

   ```
   sudo ./configure --enable-shared --prefix=/usr/local/ffmpeg
   sudo make && make install
   ```

5. 行环境变量配置

   ```
   sudo vim /etc/ld.so.conf
   添加安装的路径: 
   /usr/local/ffmpeg/bin
   更新
   ldconfig
   ```

6. 建立软连接，变为全局,对应自己路径

   ```
   sudo ln -s /usr/local/ffmpeg/bin/ffmpeg /usr/local/bin
   ```

7. 验证

   ```
   ffmpeg -version
   ```

   

ps:

若无法安装到/usr/local/ffmpeg,可以安装到当前登录用户名(user01)下的文件空间下，例如：/home/user01/