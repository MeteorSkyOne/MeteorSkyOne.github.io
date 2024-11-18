---
title: docker启动nacos

date: 2024-11-18 14:19:13
tags: 
  - nacos
  - docker
---

```bash
docker run --name nacos-quick -e MODE=standalone -p 8848:8848 -p 7848:7848  -p 9848:9848 -p 9849:9849 -d nacos/nacos-server:2.0.2
```

注意除了8848还要映射7849 9848 9849这3个端口

