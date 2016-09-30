---
title: linux
date: 2016-09-26 10:56:32
tags:
---
### linux

### /
可以把Linux系统看作由四部分构成：内核、用户界面、文件结构和实用工具
目录结构如下

## root

 启动Linux时使用的一些核心文件。如操作系统内核、引导程序Grub等。

## home
 存储普通用户的个人文件

  * ftp --- 用户所有服务

  * httpd

  * samba

  * user

## bin

 系统启动时需要的执行文件（二进制）

## sbin

 可执行程序的目录，但大多存放涉及系统管理的命令。只有root权限才能执行

## proc

 虚拟，存在linux内核镜像；保存所有内核参数以及系统配置信息
* 1 --- 进程编号

## usr

 用户目录，存放用户级的文件
   * bin --- 几乎所有用户所用命令，另外存在与/bin，/usr/local/bin
   * sbin --- 系统管理员命令，与用户相关，例如，大部分服务器程序
   * include ---  存放C/C++头文件的目录
   * lib --- 固定的程序数据
   * local --- 本地安装软件保存位置
   * man --- 手工生成的目录
   * info --- 信息文档
   * doc --- 不同包文档信息
   * tmp
   * X11R6 ---  该目录用于保存运行X-Window所需的所有文件。该目录中还包含用于运行GUI要的配置文件和二进制文件。
   * X386　--- 功能同X11R6，X11 发行版5 的系统文件

## boot

 引导加载器所需文件，系统所需图片保存于此

## lib

 根文件系统目录下程序和核心模块的公共库
* modules
    可加载模块，系统崩溃后重启所需模块

## dev

 设备文件目录

## etc

 配置文件
* skel --- home目录建立，该目录初始化
* sysconfig --- 网络，时间，键盘等配置目录


## var

  * file
  * lib --- 该目录下的文件在系统运行时，会改变
  * local --- 安装在/usr/local的程序数据，变化的
  * lock --- 文件使用特定外设或文件，为其上锁，其他文件暂时不能访问
  * log --- 记录日志
  * run --- 系统运行合法信息
  * spool --- 打印机、邮件、代理服务器等假脱机目录
  * tmp
  * catman --- 缓存目录


## mnt

 临时用于挂载文件系统的地方。一般情况下这个目录是空的，而在我们将要挂载分区时在这个目录下建立目录，再将我们将要访问的设备挂载在这个目录上，这样我们就可访问文件了。


## tmp

 临时文件目录，系统启动后的临时文件存放在/var/tmp


## lost+found

 在文件系统修复时恢复的文件


> /

根目录，一般根目录下只存放目录，不要存放文件，/etc、/bin、/dev、/lib、/sbin应该和根目录放置在一个分区中

> /bin:/usr/bin

可执行二进制文件的目录，如常用的命令ls、tar、mv、cat等。

> /boot

放置linux系统启动时用到的一些文件。/boot/vmlinuz为linux的内核文件，以及/boot/gurb。建议单独分区，分区大小100M即可

> /dev


存放linux系统下的设备文件，访问该目录下某个文件，相当于访问某个设备，常用的是挂载光驱mount /dev/cdrom /mnt。

> /etc

系统配置文件存放的目录，不建议在此目录下存放可执行文件，重要的配置文件有/etc/inittab、/etc/fstab、/etc/init.d、/etc/X11、/etc/sysconfig、/etc/xinetd.d修改配置文件之前记得备份。

    注：/etc/X11存放与x windows有关的设置。

> /home

系统默认的用户家目录，新增用户账号时，用户的家目录都存放在此目录下，~表示当前用户的家目录，~test表示用户test的家目录。建议单独分区，并设置较大的磁盘空间，方便用户存放数据

> /lib:/usr/lib:/usr/local/lib

系统使用的函数库的目录，程序在执行过程中，需要调用一些额外的参数时需要函数库的协助，比较重要的目录为/lib/modules。

> /lost+fount

系统异常产生错误时，会将一些遗失的片段放置于此目录下，通常这个目录会自动出现在装置目录下。如加载硬盘于/disk 中，此目录下就会自动产生目录/disk/lost+found

> /mnt:/media

光盘默认挂载点，通常光盘挂载于/mnt/cdrom下，也不一定，可以选择任意位置进行挂载。

> /opt

给主机额外安装软件所摆放的目录。如：FC4使用的Fedora 社群开发软件，如果想要自行安装新的KDE 桌面软件，可以将该软件安装在该目录下。以前的 Linux 系统中，习惯放置在 /usr/local 目录下

> /proc

此目录的数据都在内存中，如系统核心，外部设备，网络状态，由于数据都存放于内存中，所以不占用磁盘空间，比较重要的目录有/proc/cpuinfo、/proc/interrupts、/proc/dma、/proc/ioports、/proc/net/ 等

> /root\

系统管理员root的家目录，系统第一个启动的分区为/，所以最好将/root和/放置在一个分区下。

> /sbin:/usr/sbin:/usr/local/sbin

放置系统管理员使用的可执行命令，如fdisk、shutdown、mount等。与/bin不同的是，这几个目录是给系统管理员root使用的命令，一般用户只能"查看"而不能设置和使用。

> /tmp

一般用户或正在执行的程序临时存放文件的目录,任何人都可以访问,重要数据不可放置在此目录下

> /srv

服务启动之后需要访问的数据目录，如www服务需要访问的网页数据存放在/srv/www内

> /usr

应用程序存放目录，/usr/bin存放应用程序，/usr/share存放共享数据，/usr/lib存放不能直接运行的，却是许多程序运行所必需的一些函数库文件。/usr/local:存放软件升级包。/usr/share/doc:系统说明文件存放目录。/usr/share/man: 程序说明文件存放目录，使用 man ls时会查询/usr/share/man/man1/ls.1.gz的内容建议单独分区，设置较大的磁盘空间

> /var

放置系统执行过程中经常变化的文件，如随时更改的日志文件/var/log，/var/log/message：所有的登录文件存放目录，/var/spool/mail：邮件存放的目录，/var/run:程序或服务启动后，其PID存放在该目录下。建议单独分区，设置较大的磁盘空间


### grep

    # grep root /etc/passwd
    # cat /etc/passwd | grep root

使用方法
[root@www ~]# grep [-acinv] [--color=auto] '搜寻字符串' filename
选项与参数：
* -a ：将 binary 文件以 text 文件的方式搜寻数据
* -c ：计算找到 '搜寻字符串' 的次数
* -i ：忽略大小写的不同，所以大小写视为相同
* -n ：顺便输出行号
* -v ：反向选择，亦即显示出没有 '搜寻字符串' 内容的那一行！
* --color=auto ：可以将找到的关键词部分加上颜色的显示喔！


    # dmesg | grep -n -A3 -B2 --color=auto 'eth'  dmesg核心信息，以grep找出eth,所在行的前两行与后三行显示
    # grep ‘energywise’ *                         在当前目录带'energywise'
    # grep -r ‘energywise’ *                      在当前目录及其子目录下带'energywise'
    # grep -l -r ‘energywise’ *                   在当前目录及其子目录下带'energywise'，但只显示匹配的文件
    # grep -n 't[ae]st' regular_express.txt       共通的 't?st' 存在
    # grep -n '[^g]oo' regular_express.txt        不想要 oo 前面有 g
    # grep -n '[^a-z]oo' regular_express.txt      oo 前面不想要有小写字节
    # grep -n '[0-9]' regular_express.txt         取得有数字的那一行
    # grep -n '^the' regular_express.txt          the 只在行首列出
    # grep -n '^[^a-zA-Z]' regular_express.txt    不想要开头是英文字母
    # grep -n '\.$' regular_express.txt           行尾结束为小数点 (.)
    # grep -n '^$' regular_express.txt            找出空白行
    # grep -n 'g..d' regular_express.txt          g??d
    # grep -n 'ooo*' regular_express.txt          至少两个 o 以上的字串
    # grep -n 'g.*g' regular_express.txt          g 开头与 g 结尾
    # grep -n '[0-9][0-9]*' regular_express.txt   任意数字
    # grep -n 'o\{2\}' regular_express.txt        两个 o 的字串
    # grep -n 'go\{2,5\}g' regular_express.txt    g 后面接 2 到 5 个 o
    # grep -n 'go\{2,\}g' regular_express.txt     2 个 o 以上的 goooo....g


### alias
alias[别名]=[指令名称]

* alias cp='cp -i'
* alias l.='ls -d .* --color=tty'
* alias ll='ls -l --color=tty'
* alias ls='ls --color=tty'
* alias mv='mv -i'
* alias rm='rm -i'
* alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'

##