---
layout: post
title: 鸟哥的Linux私房菜 基础学习篇 笔记
category: notes
description: 这本书个人并不是太喜欢，觉得太啰嗦，扯皮的话说得太多。广度还可以，深度不够，作为Linux入门还凑合。
---

* 目录
{:toc}

#1. 第一部分 1-5章 Linux规划与安装
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

若根目录"/"在partition1，"/home"目录挂载在partition2，则"/home"目录下所有文件都在partition2。

重要目录：
/boot : 放在整个硬盘最前面，100MB左右（小于1024个柱面）。有些老主板的BIOS无法识别大硬盘，导致开机无法启动。
/ : 根目录
/usr：系统可执行文件  
/var：系统数据  
/home：用户数据  
swap：交换分区  

###1.1.4 文件系统类型
在给Linux系统分区时，可以选择如下文件系统类型

* ext2/ext3: Linux适用的文件系统类型。
* physical volume (LVM): 可用来弹性调整文件系统大小的类型。
* software RAID: 用软件仿真磁盘阵列。
* swap: 内存交换空间类型。如果用此类型就不需要指定挂载点。
* vfat: 同时被Linux和Windows支持的类型。

##1.2 安装启动相关
Windows的环境中最好将Linux的根目录与swap取消挂载，否则Windows的资源管理器会提示你格式化这两个分区。

Linux不要随便删除，因为grub会读取Linux根目录下的/boot/目录内容。如果删除了，Windows也无法启动。

即使有多块硬盘，也只有一个MBR，BIOS会读取第一个可开机设备的MBR。

Linux默认提供6个终端界面tty1~tty6，还有一个图形界面。
CTRL+ALT+F1~F6: 文字界面登录tty1~tty6终端。 
CTRL+ALT+F7: 图形界面桌面。

##1.3 执行Linux命令
Linux命令的基本格式为：

    command [-options] para1 para2
    command: 命令或者可执行文件
    [Tab]键：在输入命令时作“命令补全”，在输入para时，作“文件补齐”
    [Ctrl]+c: 中断当前命令执行
    [Ctrl]+d: 作EOF，代表键盘输入结束
    
    
命令太长时，使用反斜杠"\"+[Enter]换行。

	$ uname -r
	# 查看Linux内核版本
	$ lsb_release -a
	# 查看distribution详细信息

##1.4 命令查询
man是manual的简称，可以查询Linux的系统相关信息，类似的还有info命令。man可查寻的内容包括：

* 1: 命令或可执行文件
* 2: 系统内核可调用的函数于工具
* 3: 一些常用的函数与函数库，大部分为C的函数库(libc)
* 4: 设备文件的说明，通常在/dev下的文件
* 5: 设备文件或者是某些文件的格式
* 6: 游戏
* 7: 惯例于协议等，如Linux文件系统、网络协议、ASCII说明
* 8: 系统管理员可用的管理命令
* 9: 根kernel有关的文件

这些项会在查询结果的后面用相应数字加以区分。同时可用`man [number] item`的方式查询某个类型的项。

    man -f item     # 查找所有类型中与item有关的文件
    man -k item     # 在系统说明文件中搜索字符串item

在man page常用的按键

space       向下翻一页  
Page Down   向下翻一页  
Page Up     向上翻一页  
Home        去第一页  
End         去最后一页  
/string     向下查询string  
?string     向上查询string  
n,N         n表示继续向下查询，N表示继续向上查询，不管是用的/或?  
q           退出此man page
 
#2. 第6、7章 Linux文件与目录

##2.1 文件与目录权限

Linux包含用户和用户组的概念，一个用户可以属于多个用户组。

* 系统所有用户账号信息默认在文件/etc/passwd
* 个人密码记录在文件/etc/shadow
* 所有组名记录在文件/etc/group

执行ls命令查看文件权限

	$ ls -l
	total 132										
	-rwxrw-r--	4	root	root	32142	Sep 8 18:26	install.log
	[文件权限][连接数][所有者][所属用户组][大小(B)][最后修改时间][文件名]
	...

文件权限的第一个字母表示文件类型：

* [d] 目录
* [-] 文件：按内容分为纯文本文件，二进制文件和数据格式文件。cat命令只能输出纯文本文件的内容。
* [l] 链接文件
* [b] 块设备，随机读取，比如硬盘/dev/sda
* [c] 串行端口设备，一次性读取，比如键盘、鼠标。
* [s] 套接字sockets，/var/run/下。
* [p] FIFO管道。

其余9个字母分成三组，分别表示[所有者、所属用户组、所有人]拥有的权限。  
r-读，w-写，x-执行。对于目录和文件，两者的意义略有不同。

对文件：r-可读；w-可修改内容但不能删除；x-可执行  
对目录：r-可查看目录下文件（目录）列表但不能查看文件（目录）权限等信息；w-可修改目录，包括新建、删除或重命名文件和目录；x-可进入该目录成为工作目录并执行命令。

注意：   
root用户不受用户权限限制。  
在本目录执行文件，且本目录不在$PATH中时，由于命令执行需要变量支持，所以需要严格指定该文件，如`./run.sh`

cp命令复制文件时，会复制命令执行者的权限。  
修改权限的命令chgrp,chown,chmod。

	chgrp [-R] group file/dir  
	# group必须在/etc/group中存在
	
	chown [-R] user file/dir  
	# user必须在/etc/passwd中存在，可使用user.group或user:group同时改变所有者和用户组，还可以单使用.group改变所属用户组。

	chmod [-R] 777 file/dir
	# r:4 w:2 x:1
	chmod [-R] u+r,g-w,o=rx,a-x file/dir
	# u:user g:group o:other a:all
	# +:增加 -:除去 =:设置

[连接数]表示有多少文件名连接到此节点（i-node）。

[文件名]单一文件或目录名长度限制在255个字符，包含目录的完整文件名限制在4096个字符。  
以点号(.)开头的文件时隐藏文件。命名文件时，避免一些特殊字符`* ? < > ; & ! [ ] | \ ' " ( ) { } + - `

##2.2 Linux目录结构

Linux有FHS标准(Filesystem Hierarchy Standard)。规定了一些基本的文件结构，包括是否可分享（被其他主机挂载），是否可变（经常变动）。

根目录下包含与开机、还原、系统修复等操作有关的文件，所以建议根目录分区越小越好。  
由于开机时，只有根目录会被挂载，其他分区是开机完成后挂载，所以与开机有关的目录必须和根目录在同一个分区，包括：  

* /etc: 配置文件，重要的包括/etc/inittab,/etc/init.d/,/etc/modprobe.conf,/etc/X11/,/etc/fstab,/etc/sysconfig/；
* /bin: 开机执行文件，但用户维护模式下还能被使用的命令；
* /dev: 所需要的设备文件，/dev/null,/dev/zero,/dev/tty,/dev/lp\*,/dev/sd\*,/dev/hd\*；
* /lib: /bin和/sbin中执行文件所需要的函数库与内核所需的模块(lib/modules/)；
* /sbin: 系统开机执行文件。

其他目录：

* /boot: 开机时用到的文件，/boot/grub/等。
* /home: 所有用户的主文件夹。
* /media: 可删除设备,/media/floppy, /media/cdrom等。
* /mnt: 挂载的额外设备。
* /opt: 第三方软件目录。
* /root: root用户的主文件夹，最好和根目录在同一分区中。
* /srv: 网络服务启动后取数据的目录。
* /tmp: 临时文件存放目录，任何人都可以访问。
* /lost+found: 文件系统发生错误时，存放一些丢失片段的目录。通常位于分区的顶层。
* /proc: 内存映射的虚拟文件目录
* /sys: 内存映射的与内核有关文件的虚拟文件目录

比较复杂的目录：

* /usr: UNIX software resource。FHS定义可分享不可变动的目录。所有系统默认的软件建议放在此目录，此目录一般很大。包含
	* /usr/X11R6: X Windows目录
	* /usr/bin/: 用户可执行命令文件
	* /usr/include/: C/C++头文件
	* /usr/lib(64)/: 各种应用软件的函数库、目标文件，一些不常用的特殊命令。
	* /usr/local/: 非系统默认的软件。该目录下也具有./bin, ./etc, ./include, ./lib子目录。
	* /usr/sbin/: 非开机运行的系统命令。比如网络服务器服务命令。
	* /usr/share/: 共享文件。./man在线帮助文件，./doc软件说明文件，./zoneinfo与时区有关的文件。
	* /usr/src/: 源码。内核源码在/usr/src/linux/。
* /var: 系统运行时变动的文件，运行时容量逐步增长。
	* /var/cache/: 应用程序暂存文件
	* /var/lib/: 程序运行时的数据文件存放位置
	* /var/lock/: 设备或文件的锁
	* /var/log/: 登录文件。/var/log/messages, /var/log/wtmp（登录者信息）
	* /var/mail/: 个人电子邮箱目录，常与/var/spool/mail/互为链接。
	* /var/run/: PID放置目录
	* /var/spool/: 等待其他程序使用的队列数据。如/var/spool/mail/, /var/spool/mqueue/, /var/spool/cron/等。



