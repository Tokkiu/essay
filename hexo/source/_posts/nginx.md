---
title: nginx
date: 2016-07-28 11:26:45
tags:
---
#  try to get the point

## how it works?
> 基本路由映射文件

## orders
关掉所有nginx进程
``` bash
/usr/sbin$ killall nginx
```

启动nginx服务
``` bash
/usr/sbin$ nginx
```

查看当前的nginx进程
```bash
/usr/sbin# ps -aux|grep nginx
```
## errors
作为一个新手，也不例外遇到了很多神奇的bug－

> failed to open stream:Permission denied in

导致这种情况发生有2种可能
第一种，目标文件的路径不对或者文件不存在
这个是比较误导的情况，首先应该检查这种情况是否出现，只需更正路径即可
第二种，就是字面意思权限问题
这段代码所在文件没有权限做你想做的事情，无论读或者写，要做的就是给你的代码文件或当前目录更高的权限：
```bash
chmod 777 file
```
777的意思是一个权限代码，可读可写可创建，一般都够了。
