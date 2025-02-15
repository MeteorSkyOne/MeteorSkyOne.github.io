---
title: pve安装openwrt踩坑（all in one分享）
date: 2025-02-15 22:42:30
tags: openwrt
---
# 前情提要
整了一台服务器放家里，想着搞一台all in one，但是发现访问github的速度惨不忍睹，docker也得配镜像站，家里虽然有软路由，但是已经闲置好久了，那么考虑现在把他用起来

# 尝试复活闲置的软路由
说干就干，首先想到的是把原来软路由的openwrt相关的应用更新一下，毕竟上次使用还是在一年前了，但是当我放了几个新ipk进去后才发现事情有些不对劲，为什么磁盘满了？
```sh
root@OpenWrt:/tmp/pass/passwall_packages_ipk_x86_64# df -h
Filesystem                Size      Used Available Use% Mounted on
/dev/root                98.3M     96.3M         0 100% /
tmpfs                     1.9G     60.7M      1.8G   3% /tmp
/dev/sda1                15.7M      5.7M      9.7M  37% /boot
/dev/sda1                15.7M      5.7M      9.7M  37% /boot
tmpfs                   512.0K         0    512.0K   0% /dev
```
查阅资料才发现原来openwrt的固件就是这样的，磁盘大小默认就一点点，但是我这机器可是有30g的硬盘啊
```sh
sda      8:0    0 29.8G  0 disk
├─sda1   8:1    0   16M  0 part /boot
│                               /boot
└─sda2   8:2    0  104M  0 part /
```
于是想着给空间扩容，问了问gpt怎么给/扩容，照着操作了一番后reboot，不出意外的，连不上了，机器挂了😡

第二天我爸给我发消息才知道蜂鸣器都给干出来了😅

![alt text](D5D7BFEF5CAA54FA2BAC7ADE1473583C.png)

# 给pve装openwrt
## 进系统
因为这机器是直接买的成品，商家刷好openwrt寄过来的，所有我自己没装过openwrt，就想着在服务器上自己装一个，先是去官网下了个最新的24.10的固件，然后正常走pve创建虚拟机的流程，一路下一步，确定，开机，进不了系统，一气呵成。


![alt text](os.png)

查询资料发现原来openwrt的安装方法比较特殊，首先创建的时候不选择镜像文件，创建完成后上传镜像，也可以不上传，那就得ssh手动传镜像，然后ssh执行下面命令，进行磁盘挂载，其中103为虚拟机编号，openwrt.img为固件对应的路径
```sh
qm importdisk 103 openwrt.img local-lvm
```
这样在硬件上就可以看到一个未使用的磁盘，直接添加

![alt text](disk.png)

然后去启动顺序里把硬盘拉到最上面

![alt text](order.png)

这下就能进系统了，第一步就去`/etc/config/network`配一下ip，把lan的ip配成你想要的ip，然后`ifdown lan`和`ifup lan`或者`reboot`一下都可以

## 换源
下一步是换源，在`/etc/opkg/distfeeds.conf`，这里给出中科大源和清华源的地址

### 中科大源
```
src/gz openwrt_core https://mirrors.ustc.edu.cn/openwrt/releases/21.02.0/targets/rockchip/armv8/packages
src/gz openwrt_base https://mirrors.ustc.edu.cn/openwrt/releases/21.02.0/packages/aarch64_generic/base
src/gz openwrt_luci https://mirrors.ustc.edu.cn/openwrt/releases/21.02.0/packages/aarch64_generic/luci
src/gz openwrt_packages https://mirrors.ustc.edu.cn/openwrt/releases/21.02.0/packages/aarch64_generic/packages
src/gz openwrt_routing https://mirrors.ustc.edu.cn/openwrt/releases/21.02.0/packages/aarch64_generic/routing
src/gz openwrt_telephony https://mirrors.ustc.edu.cn/openwrt/releases/21.02.0/packages/aarch64_generic/telephony
```
### 清华源
```
src/gz openwrt_core https://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/21.02.0/targets/rockchip/armv8/packages
src/gz openwrt_base https://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/21.02.0/packages/aarch64_generic/base
src/gz openwrt_luci https://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/21.02.0/packages/aarch64_generic/luci
src/gz openwrt_packages https://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/21.02.0/packages/aarch64_generic/packages
src/gz openwrt_routing https://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/21.02.0/packages/aarch64_generic/routing
src/gz openwrt_telephony https://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/21.02.0/packages/aarch64_generic/telephony
```

换好后更新一下所有可以更新的
```sh
opkg update
opkg list-upgradable | cut -f 1 -d ' ' | xargs -r opkg upgrade
```
## 扩容
然后进行空间扩容，有了上文惨痛的经历，不敢瞎跟着gpt搞了，去网上搜了一下，首先装好以下的ipk
```sh
opkg install fdisk
opkg install block-mount
opkg install e2fsprogs
reboot
```
然后查看目前的分区情况
```sh
root@OpenWrt:~# fdisk -l
Disk /dev/sda: 10.12 GiB, 10867441664 bytes, 21225472 sectors
Disk model: QEMU HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: A21FA13B-34B6-35B4-69BB-92E6F5A90E00

Device       Start      End  Sectors  Size Type
/dev/sda1      512    33279    32768   16M Linux filesystem
/dev/sda2    33280   246271   212992  104M Linux filesystem
/dev/sda128     34      511      478  239K BIOS boot

Partition table entries are not in disk order.
```
然后`fdisk /dev/sda`进去操作，这里fdisk的操作就补细说了，先p查看目前情况，然后n新建一个分区，选择头和尾，w写入，操作完后输入下列命令进行ext4的转换
```sh
mkfs.ext4 /dev/sda3
```
这样一个新的分区就建好了
```sh
root@OpenWrt:~# fdisk -l
Disk /dev/sda: 10.12 GiB, 10867441664 bytes, 21225472 sectors
Disk model: QEMU HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: A21FA13B-34B6-35B4-69BB-92E6F5A90E00

Device       Start      End  Sectors  Size Type
/dev/sda1      512    33279    32768   16M Linux filesystem
/dev/sda2    33280   246271   212992  104M Linux filesystem
/dev/sda3   247808 21223423 20975616   10G Linux filesystem
/dev/sda128     34      511      478  239K BIOS boot

Partition table entries are not in disk order.
```
然后需要进webui，找到`System-Mount Points`,添加一个挂载点，选择刚创建的分区，勾选启用，保存并应用

![alt text](mount-point.png)

然后执行上述的命令
```sh
mkdir -p /tmp/introot
mkdir -p /tmp/extroot
mount --bind / /tmp/introot
mount /dev/sda1 /tmp/extroot
tar -C /tmp/introot -cvf - . | tar -C /tmp/extroot -xf -
umount /tmp/introot
umount /tmp/extroot
reboot
```
重启后就会发现成功扩容分区了
```sh
root@OpenWrt:~# df -h
Filesystem                Size      Used Available Use% Mounted on
/dev/root                98.3M     21.9M     74.4M  23% /rom
tmpfs                     1.9G      2.2M      1.9G   0% /tmp
/dev/sda3                 9.7G    237.9M      9.0G   3% /
/dev/sda1                16.0M      6.1M      9.8M  38% /boot
/dev/sda1                16.0M      6.1M      9.8M  38% /boot
tmpfs                   512.0K         0    512.0K   0% /dev
```
## 装插件
这时候就可以装自己想装的插件了，首先先装一个`openclash`和`passwall`，这里有一个坑点是`passwall`的依赖截至发文时最高只支持到`openwrt 23.05`，但我是24.10的，没办法，再重装一个吧😭

重装完23.05后成功安装了passwall

## 新增网卡
下一个任务是给openwrt的虚拟机再添加一个网卡，我家目前的网络拓扑图如下

![alt text](topo.png)

可以看到我是打算把服务器创建的虚拟机和家庭设备的网段分离的，防止在路由器设备目录里看到一堆东西，但是openwrt我是打算给家里设备和虚拟机两用的，现在我只给他加了192.168.31.80这个ip，我还需要再给他加一个192.168.10.0/24网段的ip，让他做vm的旁路由，在pve中添加网络设备后在webui中`Network-Interfaces`添加一下新加的网卡

![alt text](add.png)

vm中ping openwrt的ip，发现不通，研究了一番才知道openwrt默认有防火墙，得配置一下  
进`Network-Firewall-Zone Settings`把新网卡也加进zone list

![alt text](zone-setting.png)

这样就可以ping通了这样差不多就大公告成了，把想要科学上网的机器的网关和dns改成openwrt的ip就行了，这样就可以愉快的进行网络冲浪了~