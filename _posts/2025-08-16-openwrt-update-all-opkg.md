---
title:  OpenWrt更新所有软件
date:   2025-08-16 09:00:00 +0800
categories: [运维]
tags: [OpenWrt]
---

#### 更新软件列表
`opkg update`

#### 更新所有 LUCI 插件
`opkg list-upgradable | grep luci- | cut -f 1 -d ' ' | xargs opkg upgrade`

#### 如果要更新所有软件，包括 OpenWRT 内核、固件等
`opkg list-upgradable | cut -f 1 -d ' ' | xargs opkg upgrade`