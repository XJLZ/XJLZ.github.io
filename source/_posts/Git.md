---
title: git
date: 2026-07-10 11:32:00
tags:
- git
---

#### 安装

[官网]: https://git-scm.com/

 

#### 命令

```
# 配置git信息
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com

# 设置 HTTP 代理
git config --global http.proxy http://127.0.0.1:7897

# 设置 HTTPS 代理（注意：地址写法相同，协议头写 http 即可）
git config --global https.proxy http://127.0.0.1:7897

# 对GitHub代理
git config --global http.https://github.com.proxy http://127.0.0.1:7897

# 取消全局 HTTP 代理
git config --global --unset http.proxy
git config --global --unset https.proxy

# 或者取消特定域名的代理
git config --global --unset http.https://github.com.proxy

# 拉取代码
git pull

#查看分支
git branch
git branch -r
git branch -a

# 切换分支
git checkout -b dev origin/dev
```

