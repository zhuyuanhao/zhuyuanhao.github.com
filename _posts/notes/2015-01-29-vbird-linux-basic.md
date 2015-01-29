---
layout: post
title: 鸟哥的Linux私房菜 基础学习篇 笔记
category: notes
description: 这本书个人并不是太喜欢，觉得太啰嗦，扯皮的话说得太多。广度还可以，深度不够，作为Linux入门还凑合。
---

* 目录
{:toc}

#1. 第一部分 Linux规划与安装
一些概念

* 当使用M，G之类的单位时，如果指硬盘、文件、内存这种容量单位，按1024计算。如果指频率、带宽这种速度单位，按1000计算。
* 操作系统包含内核和系统调用两部分，对下直接操作硬件，对上为应用程序提供访问操作系统的接口。
* 操作系统内核的功能：1. 系统调用接口。2.程序管理。3.内存管理。4.文件系统管理。5.设备驱动。
* Linux distribution = Kernel + Softwares + Tools
* Linux两大系统：使用RPM方式安装软件的Red Hat, Fedora, SuSE等；使用dpkg方式安装软件的Debian，Ubuntu, B2D等。
* Linux系统中所有硬件设备都当作一个文件。IDE硬盘对应文件名/dev/hd[a-d]，SATA/USB硬盘对应文件名/dev/sd[a-p]。

##1.1 磁盘分区
磁盘每个扇区512bytes，第一个扇区记录了主引导分区（MBR，Master Boot Record），446bytes和分区表（partition table），64bytes。

###1.1.1 磁盘主引导分区
开机过程：

1. BIOS启动第一个程序，识别第一个可开机设备。（硬件信息从CMOS中读取）
2. MBR在设备第一个扇区，包含一个最基本的引导加载程序。
3. 引导加载程序（Boot loader）读取内核文件。
4. 内核文件开始操作系统。

引导加载程序提供的功能：

* 提供菜单，用于开机多重引导
* 载入内核文件
* 将引导加载任务转交其他loader，用于多重引导功能

引导加载程序不仅可以安装在MBR，还能安装在每个分区的引导扇区。每个loader只认识自己系统分区内的开机内核文件和其他loader。

Linux的loader可以手动设置菜单，但Windows装机的时候会覆盖MBR以及自己分区的启动扇区。所以装双系统时必须先装Windows再装Linux。

###1.1.2 磁盘分区表
柱面（cylinder）为文件系统的最小单位，也是分区的最小单位。

磁盘分区表包含四组记录区，每组记录区记录了该区段的起始和结束柱面号。分区可以是主（Primary）分区或者扩展（Extended）分区类型。其中扩展分区最多只能有一个。

扩展分区记录了分区范围，同时扩展分区本身只是额外的用来记录分区的扇区。扩展分区本身无法格式化，由扩展分区分出来的分区被称为逻辑分区（logical partition）。

设备文件名前4个/dev/hda[1-4]保留给主分区或扩展分区，逻辑分区的设备文件名从5开始。

###1.1.3 目录结构
Linux的文件系统是目录数结构，根目录为"/"目录。对每个磁盘分区，需要使用一个目录当成进入点，使该磁盘内的数据都放置在该目录下。将磁盘分区与目录相关联的过程叫挂载，这个目录叫挂载点。

若根目录“/”在partition1，“/home”目录挂载在partition2，则“/home”目录下所有文件都在partition2。

/usr：系统可执行文件  
/var：系统数据  
/home：用户数据  
swap：交换分区  


