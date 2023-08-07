---
title:  运营商不下发ipv6前缀的地区，通过OpenWrt热插拔特性，自动下发前缀
date:   2022-06-11 22:17:00 +0800
categories: [网络]
tags: [OpenWrt, IPv6]
---

运营商不下发IPv6前缀的地区，通过openwrt热插拔特性，可自动下发前缀，亲测有效。

```bash
vim /etc/hotplug.d/iface/99-ipv6

#!/bin/sh
[ "$ACTION" = ifup ] || exit 0
[ "$INTERFACE" = wan_6 ] || exit 0
uci set network.globals.ula_prefix="$(ip -6 route show | grep default | sed -e 's/^.*from //g' | sed 's/ via.*$//g')"
uci commit network
/sbin/ifup lan
```

也可以在`系统`-`启动项`-`本地启动脚本`的`exit 0`之前加入以下代码
```bash
cat > /etc/hotplug.d/iface/99-ipv6 <<EOF
#!/bin/sh
[ "\$ACTION" = ifup ] || exit 0
[ "\$INTERFACE" = wan_6 ] || exit 0
uci set network.globals.ula_prefix="\$(ip -6 route show | grep default | sed 's/^.*from //g' | sed 's/ via.*$//g')"
uci commit network
/sbin/ifup lan
EOF
```