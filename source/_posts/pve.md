---
title: PVE配置nat与端口转发
date: 2025-01-28 12:35:09
tags: pve
---
# 建立NAT

参考视频：https://www.youtube.com/watch?v=ITYMeRE455g
### 1.建立新网络设备vmbr1
![1738039422136](assets/1738039422136.png)
### 2.修改pve网络设置
```
auto vmbr1
iface vmbr1 inet static
        address 192.168.10.1/24
        netmask 255.255.255.0
        bridge-ports none
        bridge-stp off
        bridge-fd 0
#NAT SETTINGS
post-up echo 1 > /proc/sys/net/ipv4/ip_forward
post-up iptables -t nat -A POSTROUTING -s '192.168.10.0/24' -o vmbr0 -j MASQUERADE
post-down iptables -t nat -D POSTROUTING -s '192.168.10.0/24' -o vmbr0 -j MASQUERADE
```
```sh
ifdown vmbr1 && ifup vmbr1
iptables -t nat -A POSTROUTING -s '192.168.10.0/24' -o vmbr0 -j MASQUERADE
iptables -t nat -D POSTROUTING -s '192.168.10.0/24' -o vmbr0 -j MASQUERADE
iptables-save
```
### 3.修改vm网络设备为vmbr1
![1738039601521](assets/1738039601521.png)
### 4.修改vm网络设置
### 5.重启vm

# 端口转发
```sh
iptables -t nat -A PREROUTING -p tcp --dport 25001 -i vmbr0 -j DNAT --to-destination 192.168.10.100:22
```