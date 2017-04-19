---
layout: post
title: 阿里云 ECS 逻辑卷挂载数据盘
author: BaiLaobo
tags:
  - 分区
  - 阿里云
categories:
  - 技术
  - Linux
date: 2016-12-30 17:44:59
---

* content
{:toc}

## 查看磁盘信息

``` bash
    [root@ixx~]# fdisk -l
    Disk /dev/xvda: 21.5 GB, 21474836480 bytes
    255 heads, 63 sectors/track, 2610 cylinders
    Units = cylinders of 16065 * 512 = 8225280 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk identifier: 0x00078f9c
							   
    	Device Boot  Start End  Blocks   Id  System
    /dev/xvda1   *   1261120970496   83  Linux

    Disk /dev/xvdb: 107.4 GB, 107374182400 bytes
    255 heads, 63 sectors/track, 13054 cylinders
    Units = cylinders of 16065 * 512 = 8225280 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk identifier: 0x00000000
```




## 数据磁盘分区

```sh
	fdisk /dev/xvdb
	(p->n->p->1->()->()->w)

```

## 创建物理卷

```sh
	pvcreate /dev/xvdb1
```

## 创建卷组

```sh
	vgcreate vg_bjcy /dev/xvdb1
```

## 查看物理卷信息

```sh
	pvdisplay
```

## 创建逻辑卷

```sh
	# 25599（*Total PE*）的数值是由上一步查询到的物理卷信息决定的
	lvcreate -l 25599 -n lv_bjcy vg_bjcy
```

## 格式化逻辑卷

```sh
	#可先lvdisplay确认 逻辑卷路径
	mkfs.ext4  /dev/vg_bjcy/lv_bjcy
```

## 挂载逻辑卷

```sh
	mkdir /data && mount /dev/mapper/vg_bjcy-lv_bjcy /data
```

## 配置开机自动挂载

```sh
    #注意格式，最后不要直接复制(可以用vim高亮显示打开 如果格式错误 可以看出来)
	vim /etc/fstab  
	/dev/mapper/vg_bjcy-lv_bjcy /data               ext4    defaults        0 0
```

## 删除逻辑卷/逻辑卷组

```sh
	lvremove /dev/vg_bjcy/lv_bjcy
	vgremove vg_bjcy
```

