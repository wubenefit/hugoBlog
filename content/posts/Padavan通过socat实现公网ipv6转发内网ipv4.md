---
title: "Padavan通过socat实现公网ipv6转发内网ipv4"
date: 2023-02-26T13:50:19+08:00
draft: true
tags: 
    - padavan
    - 路由器
categories: [路由器]
keywords: [路由器]
---

> 本文作者：Seeker
> 原文地址：http://www.yizu.org/archives/786/

#### 在没有公网IP的情况下，我们会采用内网穿透的方式访问内网，比如frp，但是这种方式需要一台公网服务器，而且速度受到这台服务器带宽的限制，体验可能很一般。但是如果我们有公网ipv6地址，就可直接访问家里设备了。如果你的路由器有IPv6地址，而内网设备因为种种原因没有IPv6，但需要公网访问，可以使用socat来进行转发

#### 这里介绍Padavan下的配置，openwrt系统类似。首先通过socat -h命令看一下你的路由器有没有安装socat,如果没有安装则先安装socat

> opkg update
> opkg install socat

##### 在“高级设置”-“参数设置”-“脚本”找到“在防火墙规则启动后执行:”，输入以下脚本

##### 设置防火墙，开放3322端口
> ip6tables -A INPUT -p tcp --dport 3322 -j ACCEPT\
> ip6tables -A OUTPUT -p tcp --dport 3322 -j ACCEPT\
> ip6tables -A INPUT -p udp --dport 3322 -j ACCEPT\
> ip6tables -A OUTPUT -p udp --dport 3322 -j ACCEPT
##### 设置socat
> nohup socat TCP6-LISTEN:3322,reuseaddr,fork TCP4:192.168.1.2:22 &\
> nohup socat UDP6-LISTEN:3322,reuseaddr,fork UDP4:192.168.1.2:22 &

###### 应用设置后即可。这段代码可以实现通过访问“[路由器ipv6地址]:3322”,达到公网访问“192.168.1.2:22”的目的