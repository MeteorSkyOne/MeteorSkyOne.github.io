---
title: vps测试脚本合集
date: 2025-02-16 14:22:07
tags:
---
# 前置条件
```sh
apt update -y && apt install -y curl wget sudo
```

# CPU性能与磁盘读写测试
```sh
curl -sL yabs.sh | bash -s -- -i -5
```
sample output
```
fio Disk Speed Tests (Mixed R/W 50/50) (Partition /dev/vda1):
---------------------------------
Block Size | 4k            (IOPS) | 64k           (IOPS)
  ------   | ---            ----  | ----           ---- 
Read       | 62.54 MB/s   (15.6k) | 1.07 GB/s    (16.7k)
Write      | 62.65 MB/s   (15.6k) | 1.07 GB/s    (16.8k)
Total      | 125.19 MB/s  (31.2k) | 2.14 GB/s    (33.5k)
           |                      |                     
Block Size | 512k          (IOPS) | 1m            (IOPS)
  ------   | ---            ----  | ----           ---- 
Read       | 1.02 GB/s     (2.0k) | 1.01 GB/s      (991)
Write      | 1.08 GB/s     (2.1k) | 1.08 GB/s     (1.0k)
Total      | 2.10 GB/s     (4.1k) | 2.09 GB/s     (2.0k)

Geekbench 5 Benchmark Test:
---------------------------------
Test            | Value                         
                |                               
Single Core     | 1402                          
Multi Core      | 1463                          
Full Test       | https://browser.geekbench.com/v5/cpu/23347251
```

# OpenAI解锁状态查看
```sh
bash <(curl -Ls https://cdn.jsdelivr.net/gh/missuo/OpenAI-Checker/openai.sh)
```
sample output
```
OpenAI Access Checker. Made by Vincent
https://github.com/missuo/OpenAI-Checker
-------------------------------------
[IPv4]
Your IPv4: censored - DMIT
Your IP supports access to OpenAI. Region: US
-------------------------------------
[IPv6]
Your IPv6: censored - DMIT
Your IP supports access to OpenAI. Region: US
-------------------------------------
```

# 流媒体解锁测试

```sh
bash <(curl -L -s check.unlock.media)
```
sample output
```
============[ Multination ]============
 Dazn:                                  Yes (Region: US)
 Disney+:                               Yes (Region: US)
 Netflix:                               Yes (Region: US)
 YouTube Premium:                       Yes (Region: US)
 Amazon Prime Video:                    Yes (Region: US)
 TVBAnywhere+:                          Yes
 Spotify Registration:                  Yes (Region: US)
 OneTrust Region:                       US [California]
 iQyi Oversea Region:                   US
 Bing Region:                           US
 Apple Region:                          US
 YouTube CDN:                           [FCIXUS] in [San Jose, CA]
 Netflix Preferred CDN:                 San Jose, CA
 ChatGPT:                               Yes
 Google Gemini:                         Yes (Region: USA)
 Claude:                                Yes
 Wikipedia Editability:                 No
 Google Play Store:                     United States 
 Google Search CAPTCHA Free:            Yes
 Steam Currency:                        USD
 ---Forum---
 Reddit:                                Yes
=======================================
```

# tiktok解锁状态
```sh
wget -qO- https://github.com/yeahwu/check/raw/main/check.sh | bash
```
sample output
```
 Netflix              : Yes (Region: US)
 YouTube Premium      : Yes (Region: US)
 BiliBili China       : No
 TikTok               : Yes (Region: US-TTP2)
 iQIYI International  : Yes (Region: US)
 ChatGPT              : Yes (Region: US)
```
# 三网回程延迟测试
```sh
wget -qO- git.io/besttrace | bash
```
sample output
```
----------------------------------------------------------------------
北京移动
NextTrace v1.3.5 2024-10-14T11:17:47Z 4ae9d8e
[NextTrace API] preferred API IP - 104.26.13.151 - 90.83ms - Misaka.LAX
IP Geo Data Provider: LeoMoeAPI
traceroute to 221.179.155.161, 30 hops max, 52 bytes payload
1   38.59.232.1     AS41378                   英国 英格兰 威尔士登  as41378.net 
                                              0.29 ms / 0.36 ms / 1.16 ms
2   *
3   59.43.182.182   *        [CN2-BackBone]   中国 上海   chinatelecom.cn  电信
                                              122.59 ms / 122.52 ms / 122.54 ms
4   59.43.39.101    *        [CN2-BackBone]   中国 广东 广州  chinatelecom.cn  电信
                                              123.54 ms / 122.94 ms / 122.93 ms
5   59.43.159.17    *        [CN2-BackBone]   中国 上海   chinatelecom.cn  电信
                                              131.11 ms / 128.82 ms / 134.40 ms
6   59.43.80.141    *        [CN2-BackBone]   中国 上海   chinatelecom.cn  电信
                                              175.00 ms / * ms / * ms
7   *
8   *
9   *
10  *
11  221.183.46.178  AS9808   [CMNET]          中国 北京   chinamobileltd.com  移动
                                              155.81 ms / 155.86 ms / 155.79 ms
12  *
13  221.179.155.161 AS56048  [CMNET]          中国 北京   bj.10086.cn  移动
                                              158.63 ms / 158.71 ms / 158.65 ms
----------------------------------------------------------------------
```

# 三网回程测试
```sh
curl https://raw.githubusercontent.com/zhucaidan/mtr_trace/main/mtr_trace.sh|bash
```
sample output
```
——————————————————————————————

目标:北京电信[219.141.136.12]   回程线路:电信CN2 GIA

目标:北京联通[202.106.50.1]     回程线路:电信CN2 GIA

目标:北京移动[221.179.155.161]  回程线路:电信CN2 GIA

目标:上海电信[202.96.209.133]   回程线路:电信CN2 GIA

目标:上海联通[210.22.97.1]      回程线路:电信CN2 GIA

目标:上海移动[211.136.112.200]  回程线路:电信CN2 GIA

目标:深圳电信[58.60.188.222]    回程线路:电信CN2 GIA

目标:深圳联通[210.21.196.6]     回程线路:电信CN2 GIA

目标:深圳移动[120.196.165.24]   回程线路:电信CN2 GIA


——————————————————————————————
```

# 三网测速
```sh
bash <(curl -Lso- https://git.io/superspeed_uxh)
```
sample output
```
——————————————————————————————————————————————————————————
 SuperSpeed 全面测速修复版. By UXH & ernisn & oooldking
 节点更新: 2022/11/28 | 脚本更新: 2022/11/28
 Github: https://github.com/uxh/superspeed
——————————————————————————————————————————————————————————
  测速类型:    0. 取消测速    1. 三网测速    2. 详细测速
               3. 电信节点    4. 联通节点    5. 移动节点
  请输入数字选择测速类型: 1
——————————————————————————————————————————————————————————
ID    测速服务器信息       上传/Mbps   下载/Mbps   延迟/ms
24447 联通|上海５Ｇ　　　　　　↑ 663.68    ↓ 588.06    135.42  
——————————————————————————————————————————————————————————
  测试完成, 本次测速耗时: 29 秒
  当前时间: 2025-02-16 14:42:55
  # 三网测速中为避免节点数不均及测试过久，每部分未使用所
  # 有节点，如果需要使用全部节点，可分别选择三网节点检测
```

# 测速命令
```sh
wget -qO- bench.sh | bash
```
sample output
```
-------------------- A Bench.sh Script By Teddysun -------------------
 Version            : v2024-11-11
 Usage              : wget -qO- bench.sh | bash
----------------------------------------------------------------------
 CPU Model          : Intel Xeon E3-12xx v2 (Ivy Bridge, IBRS)
 CPU Cores          : 1 @ 2999.998 MHz
 CPU Cache          : 16384 KB
 AES-NI             : ✓ Enabled
 VM-x/AMD-V         : ✓ Enabled
 Total Disk         : 9.3 GB (2.7 GB Used)
 Total Mem          : 724.4 MB (255.2 MB Used)
 System uptime      : 48 days, 1 hour 46 min
 Load average       : 0.13, 0.15, 0.15
 OS                 : Debian GNU/Linux 11
 Arch               : x86_64 (64 Bit)
 Kernel             : 5.10.0-9-amd64
 TCP CC             : bbr
 Virtualization     : KVM
 IPv4/IPv6          : ✓ Online / ✗ Offline
 Organization       : AS41378 Kirino LLC
 Location           : San Jose / US
 Region             : California
----------------------------------------------------------------------
 I/O Speed(1st run) : 261 MB/s
 I/O Speed(2nd run) : 310 MB/s
 I/O Speed(3rd run) : 446 MB/s
 I/O Speed(average) : 339.0 MB/s
----------------------------------------------------------------------
 Node Name        Upload Speed      Download Speed      Latency     
----------------------------------------------------------------------
 Finished in        : 11 sec
 Timestamp          : 2025-02-16 06:44:28 UTC
----------------------------------------------------------------------
```