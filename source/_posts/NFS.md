---
title: NFS挂载
date: 2026-07-31 11:32:00
tags:
- NFS
- Linux
- Mac
---

# 服务端

```bash
sudo yum install -y nfs-utils
```

编辑 `/etc/exports`，增加：

```
/data/tusd 192.168.31.200(rw,sync,no_subtree_check)
/data/tusd 192.168.31.201(rw,sync,no_subtree_check)
/data/tusd 192.168.30.117(rw,sync,no_subtree_check,insecure)

# 允许整个网段访问
/data/tusd 192.168.30.0/24(ro,sync,no_subtree_check,insecure)
```

权限设置

```
sudo chown -R tusd:tusd /data/tusd
sudo chmod 0755 /data/tusd
```

然后执行：

```shell
sudo systemctl enable --now nfs-server
sudo exportfs -rav
sudo exportfs -v
```

# 客户端

## Linux

先安装客户端：

```
sudo yum install -y nfs-utils
```

确保本地目录存在：

```
sudo mkdir -p /data/tusd
```

临时挂载测试：

```
sudo mount -t nfs 192.168.31.91:/data/tusd /data/tusd
```

验证能读到 tusd 写入的目录：

```bash
df -hT /data/tusd
mount | grep /data/tusd
ls -la /data/tusd
```

如果 Jar 服务运行用户需要读取文件，还要验证该用户能读：

```
sudo -u <Jar服务运行用户> ls -la /data/tusd
```

### 自动挂载

确认临时挂载成功后，在 30.117 的 `/etc/fstab` 增加：

```
192.168.31.199:/data/tusd /data/tusd nfs defaults,_netdev 0 0
```

验证：

```sh
sudo umount /data/tusd
sudo mount -a
df -hT /data/tusd
```
## Mac

```sh
# NFS服务的需开启
sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.30.117" service name="nfs" accept'

sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.30.117" service name="mountd" accept'

sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.30.117" service name="rpc-bind" accept'
```



```sh
sudo mount -t nfs \
  -o vers=3,resvport,rw \
  192.168.31.199:/data/tusd \
  /Users/xiao/Desktop/data/tusd
  
sudo mount -t nfs -o vers=3,resvport,rw  192.168.31.91:/data/tusd /Users/xiao/Desktop/data/tusd
```

### 自动挂载

临时挂载验证通过后，再考虑写入 Mac 的 `/etc/fstab`：

```
192.168.31.199:/data/tusd /Users/xiao/resource-tusd nfs rw,vers=3,resvport 0 0
```

然后测试：

```sh
sudo umount /Users/xiao/resource-tusd
sudo mount -a
mount | grep resource-tusd
```
