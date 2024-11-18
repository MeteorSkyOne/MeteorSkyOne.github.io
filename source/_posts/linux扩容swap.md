---
title: linux扩容swap
date: 2024-11-18 14:23:58
tags: linux
---

## 方法 1：增加现有的交换分区大小

如果系统使用的是交换分区而不是交换文件，可以按照以下步骤操作：

### 1. 检查当前swap分区

运行以下命令查看当前swap分区：

```bash
sudo swapon --show
```

### 2. 禁用当前swap分区

先临时禁用swap分区：

```bash
sudo swapoff /dev/sdX
```

将`/dev/sdX`替换为你的swap分区名称（通过`swapon --show`命令查到）。

### 3. 调整分区大小

使用分区工具（例如`fdisk`、`parted`或`gparted`）调整分区大小，增加swap分区的空间。

### 4. 格式化分区为swap

重新格式化分区为swap：

```bash
sudo mkswap /dev/sdX
```

### 5. 启用新的swap分区

重新启用swap分区：

```bash
sudo swapon /dev/sdX
```

### 6. 更新`/etc/fstab`

确保新的swap分区在系统启动时自动挂载，编辑`/etc/fstab`文件，确认对应的swap条目正确无误。

---

## 方法 2：创建新的交换文件

如果无法调整分区，可以通过增加交换文件来扩展swap空间：

### 1. 创建交换文件

使用`dd`或`fallocate`创建一个新的文件，例如`swapfile`
```bash
sudo fallocate -l 2G /swapfile
```

这里的`2G`是文件大小，可根据需要调整。如果`fallocate`不可用，也可以使用`dd`命令：

```bash
sudo dd if=/dev/zero of=/swapfile bs=1M count=2048
```

### 2. 设置正确权限

确保只有root用户能访问：

```bash
sudo chmod 600 /swapfile
```

### 3. 格式化为swap

将该文件格式化为swap：

```bash
sudo mkswap /swapfile
```

### 4. 启用交换文件

启用新的swap文件：

```bash
sudo swapon /swapfile
```

### 5. 更新`/etc/fstab`

为了在重启后自动启用，编辑`/etc/fstab`，添加以下条目：

```bash
/swapfile none swap sw 0 0
```
