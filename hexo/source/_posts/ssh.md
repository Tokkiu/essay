---
title: ssh
date: 2016-08-10 09:52:24
tags:
---

#ssh

## 基本使用

## 我用ssh做了什么

### 往docker里放文件
以前不知道还可以这样，每次都很笨的强行复制粘贴，今天宋姐告我，可以用ssh登陆到自己的本地主机，然后把本地主机的文件放上来。
> scp

```
scp ary@192.168.53.156:/home/ary/xx.js /home/root/xx.js
```
如果是传一个文件夹的话
```
scp -f ary@192.168.53.156:/home/ary/xx /home/root/
```
