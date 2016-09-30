---
title: http
date: 2016-09-23 11:20:26
tags:
---
## http理解

### 长连接
### WebSocket

> header

WebSocket的连接请求头很小，只有2字节。相比http request的头很长，但可能只包含一个很小的值，性能提升很多。

> handshaking

由浏览器发起创建连接，然后服务器做出回应，这个过程称为握手。
只要一次握手，2者之间就可以建立持久的连接，允许数据进行双向传输。

> Socket

WebSocket是一种在tcp连接上进行全双工通讯的协议，与http的唯一联系是使用http 的101状态码进行协议切换，使用tcp的80端口。

> html5

html5中定义的WebSocket


### http request
### 双工
> half-duplex

允许2台设备之间的数据传输，但同一时间只允许一个设备进行传输。

> full-duplex

允许2台设备同一时间进行双向数据传输。

> TDD

Time-Division Duplexing
用时间分隔多任务来分隔发送的和接收的信号。用半双工的传输来模拟全双工。
时分双工在非对称网络上（上传及下载速度不平衡）有明显优势，可动态调整对应带宽。

> FDD

Frequency-Division Duplexing
用频率分隔技术来分隔发送及接受的信号。
上传及下载的区块之间用“频率偏移（frequency offset）”来分隔。
在上传下载相近时，更有效率。

### http管线化
http pipelining
将多个http请求整批提交的技术，而在发送过程中不需要等待服务器响应。
管线化机制通过永久连接完成（persistent connection），并且只有GET和HEAD等请求可以被管线化，非幂等的方法如POST不会被管线化。

### http持久连接

http1.0中会在现有协议中加一个参数

    Connection:Keep-Alive

同时服务端也返回这样一个参数
在http1.1中，默认所以连接都是持久连接
持久连接即使用同一个TCP连接来发送和接收多个http请求/应答，不会打开新的连接。

## 协议科普

### tftp
> travial file transfer protocel,简单文件传输协议。

是tcp/ip中一个用来在客户机与服务器之间进行简单文件传输的协议，提供不复杂，开销不大的文件传输服务，端口号69
基于udp协议实现。
一个tftp包中会包含以下几段：

    | local medium | internet | Datagram | TFTP |

tftp的优势
1.可用于UDP环境
当需要将程序或者文件同时向许多机器下载时，就需要使用到TFTP协议。
2.
TFTP所占的内存较小。

与FTP相比，
TFTP多用于局域网以及远程UNIX计算机中，常见FTP多用于互联网中，需要客户端验证，
FTP与服务器通信用TCP，而TFTP与服务器通信用UDP、
TFTP只支持文件传输，不支持交互。
### MTFTP
是多点的TFTP服务，应用在windows无盘工作站的服务端。
