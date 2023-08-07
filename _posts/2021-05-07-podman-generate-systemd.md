---
title:  Podman重启自动运行容器
date:   2021-05-07 23:19:00 +0800
categories: [运维, 容器]
tags: [Podman]
image:
  path: /images/posts/podman.png
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: Podman重启自动运行容器
---

# 背景
因为Podman不使用Daemon守护进程，所以podman run命令没有--restart=always参数来重启容器。Podman的容器自启动需要配合Systemd来实现。

# 方案
我们可以使用podman generate systemd命令轻松的生成systemd元文件。

```sh
# 创建systemd元文件
# --restart-policy=always自动重启
# -t 1 停止超时时间为1秒
# --name 在创建的systemd元文件中，用容器name启动、停止容器
# -f 在当前目录创建{container,pod}-{ID,name}.service格式的元文件，不加此参数，创建内容只在控制台显示。
podman generate systemd --restart-policy=always -t 1 --name -f caddy

# 把文件复制到/etc/systemd/system/目录
cp container-caddy.service /etc/systemd/system/

# 设置开机自启动
systemctl enable container-caddy

# 如果容器当前运行，先停止掉
podman stop caddy

# 启动服务
systemctl start container-caddy

# 查看状态
systemctl status container-caddy
```
至此，容器已经可以利用Systemd守护进程并自动启动了。

# 参考文档
http://docs.podman.io/en/latest/markdown/podman-generate-systemd.1.html