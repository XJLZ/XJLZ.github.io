---
title: Python3脚本安装
date: 2021-05-20 10:01:00
tags:
- Centos7
- Python3
- ShellScript
---



#### 准备压缩包

下载网站：https://www.python.org/ftp/python 

例如：https://www.python.org/ftp/python/3.9.4/Python-3.9.4.tgz     

#### 安装依赖

```
yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gcc* make -y
```

#### SSL模块

```
#修改Setup文件,在解压文件夹下的Modules文件夹下的Setup
vim Modules/Setup
#修改结果如下：去掉最后4行注释，得到如下的结果
# Socket module helper for socket(2)
_socket socketmodule.c timemodule.c

# Socket module helper for SSL support; you must comment out the other
# socket line above, and possibly edit the SSL variable:
SSL=/usr/local/ssl
_ssl _ssl.c \
-DUSE_SSL -I$(SSL)/include -I$(SSL)/include/openssl \
-L$(SSL)/lib -lssl -lcrypto
```



#### 安装脚本

**自己修改压缩包存放目录和文件名**

```
#!/bin/bash
cd /root
echo '开始解压'
tar -zxvf Python-3.6.5.tgz
echo '进入文件夹'
cd Python-3.6.5
echo '创建安装目录'
mkdir /usr/local/python3
echo '指明安装路径'
./configure -prefix=/usr/local/python3
echo '编译安装'
make && make install
echo '为python3创建软连接'
ln -s /usr/local/python3/bin/python3 /usr/bin/python3
echo '为pip3创建软连接'
ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3
echo '验证'
python3
pip3 -V 
```

**写完脚本记得添加执行权限**

```
chmod +x xxxx.sh
0 12 * * * python3 /home/python/genshin.py
```

