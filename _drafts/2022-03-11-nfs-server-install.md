---
title:  Linux环境下NFS安装笔记
date:   2022-03-11 21:17:00 +0800
categories: [运维, Linux]
tags: [NFS]
---

# NFS服务端安装和配置

## NFS服务端安装


安装NFS客户端并挂载
CentOS
```sh
sudo yum install nfs-utils
```
Ubuntu/Debian
```sh
sudo apt update
sudo apt install -y nfs-common
```

挂载
```mkdir /data```

```echo "168.138.32.191:/data /data nfs4 _netdev 0 0" >> /etc/fstab```

```reboot```重启

NFS服务器添加共享
```echo "/data x.x.x.x(rw,no_root_squash,no_all_squash,sync)" >> /etc/exports```
重启nfs-server
```sudo systemctl restart nfs-server```
参考[官方文档](https://docs.docker.com/engine/install/debian/)安装Docker

