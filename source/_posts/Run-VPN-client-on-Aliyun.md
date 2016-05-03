title: Run VPN client on Aliyun
date: 2016-03-04 18:50:07
tags:
description: 如果您正在使用阿里云服务器，你可能会无法安装node-sass之类的东西，本文将告诉您一种方法

---

# 在阿里云上通过VPN翻墙安装软件

> 首先你要有个VPN（PPTP），如果没有VPN就不必往下看了

## 各种IP要区分清楚，

* 阿里云服务器IP － 就是你的阿里云服务器IP，一般有两个，这里指那个外网IP；
* 阿里云服务器路由IP － 指默认路由对应的IP，可以通过route命令查到；
* 你自己的外网IP － 一般来说是你的路由器的外网IP，可以在登录阿里云的瞬间看到提示的那个就是了。
* VPN远端IP － 成功连接VPN，会为本地ppp设备分配一个本地VPN IP，同时分配一个远端VPN IP，远端VPN IP，就是你用来翻墙的路由。

## 登录阿里云


```Bash
ssh root@ppmessage.com
```

一般会提示：
Last login: Fri Mar  4 18:31:30 2016 from 123.116.51.166

您可能想这是上一次，你可以再登录一遍，试试, ^_^

123.116.51.166 就是你自己的外网IP.

# 查看路由

```Bash
route -n
```

输出类似：

```Bash
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         123.57.143.247  0.0.0.0         UG    0      0        0 eth1
10.0.0.0        10.172.191.247  255.0.0.0       UG    0      0        0 eth0
10.172.184.0    0.0.0.0         255.255.248.0   U     0      0        0 eth0
100.64.0.0      10.172.191.247  255.192.0.0     UG    0      0        0 eth0
106.185.41.129  123.57.143.247  255.255.255.255 UGH   0      0        0 eth1
123.57.140.0    0.0.0.0         255.255.252.0   U     0      0        0 eth1
123.116.51.166  123.57.143.247  255.255.255.255 UGH   0      0        0 eth1
172.16.0.0      10.172.191.247  255.240.0.0     UG    0      0        0 eth0
192.168.0.0     10.172.191.247  255.255.0.0     UG    0      0        0 eth0
```

Destination 0.0.0.0 对应的 Gateway 就是阿里云服务器路由IP，123.57.143.247

## 第一次配置路由

因为你与阿里云之间的网络连接不能断，所以在翻墙之前先为自己配置一条路由，并且删除默认路由

```Bash
route add -host 你自己的外网IP gw 阿里云服务器路由IP
route del -net 0.0.0.0 gw 阿里云服务器路由IP
```

## 建立VPN连接

```
pptpsetup --create vpnname --server vpn_server --username vpn_username --password vpn_password --encrypt --start
```

如果成功会提示形如这样的结果
> local  IP address 10.85.92.24
> remote IP address 10.85.92.1

其实就是为你本地准备了一个IP，并且远端跟你相连的IP也告诉你了。

## 第二次改路由

```Bash
route add default gw 10.85.92.1
```

其中10.85.92.1就是VPN服务器为你分配的远端IP，那么你加入这个路由的目的就是让所有没有特殊指定的IP请求走这个远端IP。

## 开始干活把

你是nodejs大侠还是ruby大神就可以开始了。

## 第三次改路由

用完之后再把路由改回来

```Bash
route del -net 0.0.0.0 gw VPN远端IP or route del default gw VPN远端IP
route add default gw 阿里云服务器路由IP
```

## 删除VPN

```Bash
pptpsetup --delete vpnname
```

## 值得注意的事情

如果路由没配好，把自己和阿里云的连接断掉了，只有登录阿里云的控制台重启了，我也重启了好几遍呢.

