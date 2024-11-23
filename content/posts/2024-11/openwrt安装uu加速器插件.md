+++
title = 'openwrt安装uu加速器插件'
date = '2024-11-22T19:32:16+08:00'
draft = false
categories = ["教程"]
tags = ["uu加速器", "openwrt"]
author ='Yuu'
+++

openwrt版本：21.02.7

link: [openwrt](https://archive.openwrt.org/releases/21.02.7/targets/x86/64/)

建议21.x版本或者18.x

## 设置ip

```shell
ifconfig # 查看ip信息
```
设置静态ip地址
```shell
vi /etc/config/network
```
设置好后进入openwrt后台。
设置网关和dns保证联网。

## 安装uu加速器插件
```shell
wget http://uu.gdl.netease.com/uuplugin-script/20231117102400/install.sh
```
```shell
/bin/sh install.sh openwrt $(uname -m)
```
正常会输出sn信息。
如果没有就输入下面的指令。
```shell
/bin/sh install.sh openwrt mipseb
```

```shell
opkg update
opkg install kmod-tun
```
查看kmod-tun
```shell
ls /dev/net/tun
```
输出/dev/net/tun
## 结束
手机上下载uu加速器主机版，设置网关和dns指向openwrt的ip地址。

绑定路由器，加速游戏。

