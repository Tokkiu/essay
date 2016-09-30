---
title: tools
date: 2016-09-12 16:29:35
tags:
---
## terminal
### heroku
安装heroku首先需要ruby和go环境
安装命令
```
wget -O- https://toolbelt.heroku.com/install-ubuntu.sh | sh

```
安装好以后查看是否安装成功
```
heroku --version
```
输出如下：

    heroku-toolbelt/3.43.12 (x86_64-linux-gnu) ruby/2.3.1
    heroku-cli/5.2.41-7b040f4 (linux-amd64) go1.6.2
    You have no installed plugins.
---------------------------------

Heroku 是一个商业的Rails主机托管解决方案，提供的是“无需准备的部署服务”，因为操作和扩展都是自动的，无需任何系统管理。虽然相较于其它服务商而言Heroku目前的价格较高。

为适应不断变化的需求，Heroku可以在网格中其它地方启动新的完全独立的dyno，或者关闭那些闲置的dyno。Dyno的启动时间还不到2秒，这足以证明Heroku的平台空前的强大。为了满足更高需求而启动新的dyno实例时，Heroku特制的路由系统可以在把新的请求“冻结”。四个dyno的计算性能相当于传统环境中的一台服务器的计算性能。

Heroku 的网格本身建于一个强大的云计算环境中，这样它可以根据需要的dyno数量方便地进行扩展或者缩减。网格上层是一个成熟的高并发路由网络，它承担了把请求分派至dyno的工作。还有一些额外的元件，比如HTTP cache和memory cache，它们分别用来减少对dyno和数据库的访问。


### pxe
> pxe(perboot execute environment，预启动执行环境)


是由英特尔开发，工作于client/server的工作模式，支持工作站通过网络从远端服务器下载映像，并由此通过网络启动操作系统。
在执行过程中，终端要求服务器分配ip，再用TFTP或MTFTP协议下载一个启动软件包到本机内存中执行，这个安装包会完成终端基本软件配置，并引导预先安装在服务器中的终端操作系统。
比较直接的表现是，在网络环境下，pxe可以省去硬盘，该技术的pc相比有盘pc要快3倍以上。
但使用pxe的pc也不是传统意义上的 terminal终端，因为使用了pxe的pc并不消耗服务器的cpu、ram等资源，故服务器的硬件要求极低。
