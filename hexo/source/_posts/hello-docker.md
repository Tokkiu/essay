---
title: 'hello,docker'
date: 2016-07-22 16:39:53
tags:
---

> 2016-07-19

来新智云实习啦，想着做大数据，结果上来就是搭docker－php，略悲伤的故事，，
养成写东西的好习惯，一些初始的也要记下来，以后少踩坑。
> 2016-07-27

如果真的是异步的话，无论是新开进程还是类似node那样用事件池，其实都是等于发送一个start信号，然后继续走，至于要异步跑的东西是不管的，popen感觉在执行这个脚本以后还是会等一段时间才结束，讲道理应该是立马结束，至于邮件有没有发出来是不管的，而用fsockopen的话，貌似是只能异步请求http的，没有见到执行本地脚本的例子，还要研究。

> 2016-08-01

docker 的端口映射机制还是比较重要的，一台服务器上跑一个docker没什么意思，跑数百数千个才是发挥docker的魅力，这时候，端口管理就很重要了，而且最好有工具协助管理，把端口和容器名，相互使用的容器联系显示出来，会方便很多。

** 下面是些什么鬼？ **

## docker

### init
不太清楚了，主要是这个
``` bash
$ sudo apt-get install docker-engine
```

More info: [官方指导](https://docs.docker.com/engine/installation/linux/ubuntulinux/)        

### pull image
docker官方提供了一个带版本控制功能的社区,[docker-hub](https://www.docker.com/products/docker-hub),
一般都从这上面拉
``` bash
sudo docker pull whalesay
```
一般从docker－hub上拉的都没什么问题，如果是自己的dockerfile来本地build的话，可能会出现各种各样的问题，比如某个工具的后面没加：latest tag的问题，比如网络有墙，下载太慢的问题，会返回各种不为0的non－code，耐心点，一般都没问题。

### run container

用命令查看本地镜像
``` bash
sudo docker images
```
镜像可以有名字和标签，通过这些都可以定位到单一镜像，如果有时候拉下来，发现docker抽风，这些都是none的话，就要靠每个镜像唯一的id来run了
``` bash
sudo docker run [镜像ID]
 ```
 参数：
``` bash
 [-d] 后台运行
 [-p] 容器端口映射，[outport:inport],即把内部的某一端口映射到外部
 [-rm]当退出后，所做的更改不会保留在当前的文件系统中
```
以bash交互方式运行
``` bash
sudo docker-compose run --rm 镜像ID bash
```
查看当前的容器：
``` bash
  sudo docker ps
```
所给出的数据里包括镜像ID和containerID

查看当前运行的容器：
``` bash
sudo docker top [containerID]
 ```
 会给出包括进程pid等数据
 在容器跑起来以后，还可以通过命令以bash方式进行交互：
 ```bash
 sudo docker exec -it [conrainerID] bash
 ```
### 关闭容器
停止运行
```bash
sudo docker stop containerID
```
关闭
```bash
sudo docker kill containerID
```
全部关闭
```bash
sudo docker kill $(sudo docker ps -a)
```
删除容器
```bash
sudo docker rm $(sudo docker ps -a -q)
```

### 查看容器内部IP
``` bash
sudo docker inspect --format '{{.NetworkSettings.IPAddress}}' 镜像ID

sudo docker inspect 镜像ID （容器信息）
```

### 编写Dockerfile
> FROM

最重要的dockerfile命令，必须是首个命令
定义了使用哪个基础镜像启动构建流程
```bash
FROM ubuntu
```

> ADD

添加文件
```bash
ADD file dir/
```
> CMD

在容器构建后被调用

> ENTRYPOINT

配置一个容器使之可执行化?
可以和cmd配合使用
```bash
ENTRYPOINT echo
CMD "hello"
```
> RUN

接受命令作为参数并创建镜像
是dockerfile的核心命令
在容器内的目录进行操作
```bash
RUN cp file dir/ \
    && cp file dir/ \
    && rm -f file
```
```bash
RUN apt-get install --yes
```

> ENV

设置环境变量
ENV SERVER_WORKS 4
> EXPOSE

用于指定端口
```bash
EXPOSE 80
```
> USER

用于设置容器的UID
```bash
USER 751
```
> VOLUME

可以让容器访问宿主机上的目录

> WORKDIR

设置cmd指令的运行目录
```bash
WORKDIR ~/
```
## linux一些常用操作
### cat
查看文件
``` bash
cat file.file
```
新建文件
``` bash
cat > newfile.file < <EOF
>balabalabala（为新建的文件内容）
>EOF(这里输入EOF后结束)

```
删除文件
``` bash
rm -f file.file  （删除文件）
rm -rf dir （删除文件夹）
```

### find
``` bash
find -name "*.php"
```
寻找后缀为php的所有文件，反之亦然

### proxy
公司有统一的代理，只要访问公司给的IP就好，所以把shadowsocks配好后，每次开机启动就好。
``` bash
cd /etc
sslocal -c shadowsocks.json startc
```

由于chrome不像firefox那样可以配代理，要这样启动
``` bash
google-chrome --proxy-server="socks5.//localhost:10801"（10801端口为自己本地配置）
```
