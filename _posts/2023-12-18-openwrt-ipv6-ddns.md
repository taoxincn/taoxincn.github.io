---
title:  通过OpenWrt热拔插实现内网设备IPv6自动配置DDNS
date:   2023-12-18 12:39:00 +0800
categories: [网络]
tags: [OpenWrt, IPv6]
---

家中的软路由经常会跑一些自己常用的服务，比如Alist、Syncthing、Jellyfin等，由于不在家中的时候也有访问这些应用的需求，就需要把这些服务对外发布出去，方案首先考虑的就是DDNS了，但现在家庭宽带IPv4已经很难申请到公网IP了，所以IPv4就此作罢。

不过目前运营商已经基本都开了IPv6网络，而且基本都下发了IPv6前缀，所以每一台设备现在都可以配置至少一个IPv6公网地址。那么能搞定IPv6的DDNS也都不错，毕竟现在IPv6已经很普及了，手机4G、5G网络都默认开启了IPv6，只要配置好防火墙策略，把服务端口放出去就行了。

从OpenWrt的仓库里看了一下，有几款DDNS工具，但是都不太符合我的心意，于是自己动手写了一个shell脚本。主要是利用了OpenWrt的热拔插属性，`/etc/hotplug.d`目录下面有个neigh文件夹，应该是和arp相关的，看了下OpenWrt源码，放置在这个文件夹下的脚本，果然可以在arp添加和老化后执行，而且附带了IP地址和MAC地址的参数，那我就可以把MAC地址和域名关联起来实现DDNS了。

实现的代码如下，把这段代码随意命名，我这里命名为`80-arp-ipv6-ddns`，然后放置于`/etc/hotplug.d/neigh`目录下面，然后在OpenWrt管理界面的`系统-启动项-本地脚本`中重启`dnsmasq`服务。

下面直接上`80-arp-ipv6-ddns`脚本的内容，有一些参数需要改成你自己的实际情况，比如CF的相关参数，祝你折腾愉快😄

```shell
#!/bin/sh

CF_TOKEN='xxxxxxxxxxxxxxxxxxxxxxxx' #改成你自己的CF API Token

case $MACADDR in
'52:54:00:d8:04:54') #改成你自己的设备或者虚拟机的MAC地址
    CF_ZONEID='xxxxxxxxxxxxx' #改成你自己的CF区域ID
    NAME='home.taox.in' #你自己的域名
    PROXIED=true
    ;;
'a0:36:9f:50:ae:e9')
    CF_ZONEID='xxxxxxxxxxxxx'
    NAME='speed.taox.in'
    PROXIED=true
    ;;
esac

[ $NAME ] || exit 0

#判断IP地址是否公网IPv6地址
funIsIPV6() {
    PD='0::\/60' #如果56位前缀，改成'00::\/56'，一般运营商只下发56或60位前缀。
    IPV6_PD=$(ip -6 route show | grep -E "default from [0-9a-f:]*$PD" | sed -r "s/^default from ([0-9a-f:]*)$PD.*/\1/")

    if [[ $IPADDR =~ ^$IPV6_PD[0-9a-f:]*$ ]]; then
        echo 'YES'
    else
        echo 'NO'
    fi
}

#更新或创建DNS
#参数： $1:域名
funAddDNS() {
    RID=$(echo $records | sed -r "s/.*\"id\":\"([0-9a-f]*)\",\"zone_id\":\"[0-9a-f]*\",\"zone_name\":\"[^\"]*\",\"name\":\"$1\".*/\1/")
    IPV6=$(echo $records | sed -r "s/.*\"name\":\"$1\",\"type\":\"AAAA\",\"content\":\"([0-9a-f:]*)\".*/\1/")
    #如果有匹配的AAAA记录
    if [ ${#IPV6} -lt 40 ]; then
        #如果已经存在的记录和本次添加记录ip地址相同，则跳过。
        [ $IPV6 != $IPADDR ] || return
        #更新DNS记录
        funUpdateDNS $RID $1
    #如果没有匹配的AAAA记录，则创建
    else
        funCreateDNS $1
    fi

}

#更新DNS的http请求
#参数： $1:记录ID $2:域名
funUpdateDNS() {
    curl -s --request PUT \
        --url "https://api.cloudflare.com/client/v4/zones/$CF_ZONEID/dns_records/$1" \
        --header "Content-Type: application/json" \
        --header "Authorization: Bearer $CF_TOKEN" \
        --data "{\"content\":\"$IPADDR\",\"name\":\"$2\",\"type\":\"AAAA\",\"proxied\":$PROXIED}"
}

#创建DNS的http请求
#参数： $1:域名
funCreateDNS() {
    curl -s --request POST \
        --url "https://api.cloudflare.com/client/v4/zones/$CF_ZONEID/dns_records" \
        --header "Content-Type: application/json" \
        --header "Authorization: Bearer $CF_TOKEN" \
        --data "{\"content\":\"$IPADDR\",\"name\":\"$1\",\"type\":\"AAAA\",\"proxied\":$PROXIED}"
}

records=$(curl -s --request GET \
    --url https://api.cloudflare.com/client/v4/zones/$CF_ZONEID/dns_records \
    --header 'Content-Type: application/json' \
    --header "Authorization: Bearer $CF_TOKEN")

case $ACTION in
'add')
    [ $(funIsIPV6) = 'YES' ] || exit 0
    funAddDNS $NAME
    ;;
'remove')
    ;;
esac

```