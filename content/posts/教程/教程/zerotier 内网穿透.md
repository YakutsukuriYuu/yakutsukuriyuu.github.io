---
title: zerotier 内网穿透
categories:
  - 教程
tags:
  - 教程
date: 2024-08-01
updated: 2024-08-01
published: true
---
# zerotier 内网穿透
![1722464466551.png](http://121.4.252.215:8888/i/2024/08/01/66aab8d4d4916.png)
## 重要的是openwrt的设置
![1722464927707.png](http://121.4.252.215:8888/i/2024/08/01/66aabaa1dbd21.png)
## 设置虚拟网口
![1722465264655.png](http://121.4.252.215:8888/i/2024/08/01/66aabbf713859.png)
![1722465938652.png](http://121.4.252.215:8888/i/2024/08/01/66aabe94c09d2.png)
## 防火墙设置
![1722465059868.png](http://121.4.252.215:8888/i/2024/08/01/66aabb273b482.png)
![1722465117188.png](http://121.4.252.215:8888/i/2024/08/01/66aabb5fafc2f.png)
规则添加
```sh
iptables -I FORWARD -i ztppi3vz6d-j ACCEPT
iptables -I FORWARD -o ztppi3vz6d-j ACCEPT
iptables -t nat -I POSTROUTING -o ztppi3vz6d-j MASQUERADE
```
ztppi3vz6d是自己添加的网口名字

# zerotier 设置
![1722465441566.png](http://121.4.252.215:8888/i/2024/08/01/66aabca325eef.png)
第一个主路由的ip
我的是192.168.10.1/24
第二个是软路由在zerotier组网的ip地址
在成员那里看

成员要勾选授权，并且openwrt要选择桥接
点edit
![1722465621371.png](http://121.4.252.215:8888/i/2024/08/01/66aabd73a9513.png)
![1722465649175.png](http://121.4.252.215:8888/i/2024/08/01/66aabd7495a41.png)
这样就可以了 其他设备通过加入网络就可以和openwrt组网