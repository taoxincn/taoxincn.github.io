---
title:  为Podman配置加速器
date:   2021-09-11 22:19:00 +0800
categories: [运维, 容器]
tags: [Podman]
image:
  path: /images/posts/podman.png
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: 为Podman配置加速器
---

在使用Podman拉取DockerHub的镜像时，由于网络原因，经常会拉取很慢或拉取失败，好在国内有科研机构和互联网厂商提供了DockerHub加速器，配置方法如下：

1. 使用vi或者其他文本修改工具修改配置文件`/etc/containers/registries.conf`，修改前最好备份一下
```bash
cp /etc/containers/registries.conf /etc/containers/registries.conf.bak
vi /etc/containers/registries.conf
```

2. 删除或者用#注释掉`registries.conf`文件的全部内容
3. 把下面内容粘贴进去，保存文件
```
unqualified-search-registries = ["docker.io"]
[[registry]]
location = "docker.io"
[[registry.mirror]]
location = "docker.mirrors.ustc.edu.cn"
```
同时国内可用的公共加速器还有
> 网易加速器 `hub-mirror.c.163.com`
> 百度加速器 `mirror.baidubce.com`

可以[点此查看](https://github.com/docker-practice/docker-registry-cn-mirror-test/actions)各加速器的可用情况。

这时候pull镜像，速度就快很多了。

![Desktop View](https://podman.io/assets/images/podman-logo-dark-9ddc71368309ef9bd7163aa5ad850398.png)