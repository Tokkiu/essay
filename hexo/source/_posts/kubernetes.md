---
title: kubernetes tips
date: 2016-09-02 13:49:33
tags:
---
## kubernetes
### 总体架构
kubernetes集群由2类节点组成：Master和Node。
在Master上运行etcd、API Server、Controller Manager和Scheduler四个组件，后三个负责对集群中所以资源进行管控和调度。
在每个node上运行kubelet、Proxy、Docker Deamon三个组件，负责对节点上的pod的生命周期管理，以实现服务代理。
在所有节点上都可以运行kubectl命令，提供了kubernetes的集群管理工具集。
etcd是高可用的key/value存储系统，用于持久化存储集群中的所有资源对象。
API Server提供了操作etcd的封装接口API，以REST方式提供服务。
可知，kubernetes的各组件功能如下：
> API Server

提供资源对象的唯一操作入口

> Controller Manager

集群内部的管理控制中心，主要是实现集群的故障检测和恢复的自动化工作

> Scheduler

集群调度器，负责pod在节点中的调度分配

> kubelet

负责本node节点上的pod的全生命周期管理

> Proxy

实现Service的代理及软件模式的负载均衡器

## start basic
### to know
在kubernetes中，node,pod,rs,rs这些概念都可以被看作是一种资源对象，通过kubernetes提供的kubectl工具或api调用进行操作，并保存在etcd中。
### node
Node是kubernetes集群中相对于Master而言的工作主机，可以是一台物理主机，也可以是一台虚拟机。
在每个node上运行用于管理pod的服务-kubelet，并且能够被Master管理，
在node上运行的服务包括kubelet，kube-proxy，docker daemon,
查看node信息：
```
kubectl describe node <node_name>
```
> node 管理

node通常是物理机、虚拟机或云数据提供商提供的资源，说kubernetes创建了一个node，是说node通常是物理机、虚拟机或云数据提供商提供的资源，说kubernetes在内部创建了一个node对象，创建后对其进行一系列健康检查，“Ready”状态后即可用

> node controller

node controller是kubernetes Master的一个组件，用于管理node对象，有2个功能：集群范围内的node信息同步，以及单个node的生命周期管理。
node信息同步可以通过kube-controller-manager的启动参数--node-sync-period设置同步的时间周期

> node 自注册

kubelet启动参数：
*  --apiservers     (地址)
*  --kubeconfig     (登陆apiserver的证书)
*  --cloud_provider   (云服务商地址，用于获取自身的metadata)
*  --register-node   (设置为true时表示自动注册到apiserver)

### pod
pod包含多个紧密相关的容器，一个pod可以被一个容器化的环境看作应用层的"logical host",
一个pod中的多个容器通常是紧耦合的，pod在node上被创建、启动或销毁。

> 为何封装一层pod？

Docker容器间通信受Docker网络机制的限制，在Docker的世界中，一个容器需要link方式才能访问另一个容器提供的服务，大量容器间的link很麻烦，pod将多个容器组合在一个虚拟的主机内，可以实现容器间仅需要通过localhost就能互相通信。

> 一个pod中的容器共享一组资源。如下

* PID命名空间-Pod中的不同应用程序可以看到其他应用程序的进程ID
* 网络命名空间-Pod中的多个容器能够访问同一个IP和端口范围
* IPC命名空间-Pod中的多个容器能够使用SystemV IPC或POSIX消息队列进行通信
* UTS命名空间-Pod的多个容器共享一个主机名
* Volumes(共享存储卷):Pod中的多个容器可以访问在Pod级别的Volumes

> pod 细节

对pod的定义通过json或yaml的配置文件来完成，在spec中包含了对container的定义，
一个pod的生命周期是通过Replication Controller来管理的，包括：通过模板定义，然后分配到一个node上运行，在pod所含容器运行结束后pod也结束。
pod有4种状态：
* Pending-pod已提交到Master，但其需要的image还不能用，或者Master还在进行调度
* Running-ok
* Succeeded-pod运行结束，未被重启
* Failed-通常是某个容器有问题，被关掉了，或者pod结束，但有容器是以失败状态结束的。

kubernetes为pod设计了一套独特的网络配置，包括：为每一个pod分配一个ip地址，使用pod名作为容器间通信的主机名等

### Label
Label以key/value的键值对形式附加到各种对象上，Label定义这些对象的可识别属性，用来进行管理和选择。
在为对象定义好label后，其他对象就可以使用Label Selector来定义其作用的对象了。

> 2种Label Selector：

* 基于等式的 (Equality-based)
  eg: name=redis-slave
* 基于集合的 (Set-based)
  eg: name in (redis-master,redis-slave)

在使用时可以将多个Label进行组合来选择
在Replication ControllerheService的定义中需要在spec.selector中指定Label来确定相关pod

### Replication Controller
rc用于定义pod副本的数量，在Master内，Controller Manager进程通过RC的定义来完成pod的创建、监控、启停等操作。
通过RC的定义，kubernetes总是保证集群中运行着用户期望的副本数量(Replica)。
通过对RC的使用，kubernetes实现了应用集群的高可用性，并大大减少了传统运维的压力
在运行时，可以通过修改RC的副本数量，来实现pod的动态缩放(Scaling)。
```
kubectl scale rc redis-slave --replicas=3
```
但是，删除rc不会删除其pod，需要指定replica为0，然后更新该rc。
客户端工具kubectl提供了stop和delete命令来一次性删除RC和RC控制的全部pod。

### Service
在kubernetes里，每个pod被创建时都会被分配一个单独的ip，而一个service可以看做是一组提供相同功能的pod的对外访问接口，service作用于哪些pod是通过label来定义的。
定义一个service后，spec.selector表示该service将包含所有符合条件的pod，
在pod正常启动后，系统将会根据service的定义创建出与pod对应的endpoint对象，以建立起service与后端pod的对应关系。
随着pod的创建，销毁，endpoint对象也将被更新。
endpoint对象主要由pod的ip地址和容器所需监听的端口号组成
> ip地址

pod的ip地址是Docker Daemon根据docker0网桥的ip地址段进行分配的，但service的clusterIP地址是kubernetes系统中的虚拟ip地址，由系统动态分配。
service的cluster ip相对与pod的ip地址来说比较稳定，在销毁前都不会变。

> 外部访问Service

由于service在clusterIP Range池中分配的ip只能在内部访问，需要给这个service提供公共ip
2种type定义对位服务：
* NodePort
  spec.type=nodePort
  spec.ports.nodePort=80
  这样，在每一个启动了该service下的pod的node上，都会打开一个80端口
* LoadBalancer
  如果云服务器支持外接负载均衡器，可以通过spec.type=LoadBalancer来定义service，同时需要指定负载均衡器的ip地址(clusterIP)
  当有多个端口需要暴露时，可以通过端口命名来避免endpoint重复。

### Volume
Volume是pod中能够被多个容器访问的共享目录，当容器终止或重启时，Volume中的数据也不会丢失。
kubernetes支持多种类型的volume，并且一个pod可以同时使用多个volume。
类型如下：
* EmptyDir
  在pod被分配到node时创建，初始内容为空，pod中所以容器都能读写，当pod移除后，这些数据也会被永久删除
* hostPath
  在pod上挂载宿主机上的文件或目录
  > 用处

  容器应用程序生成的日志文件需永久保存
  需要访问宿主机上docker引擎内部数据结构的容器应用

* gcePersistentDisk
  表示使用谷歌计算引擎(Google Compute Engine,GCE)上永久磁盘(Persistent Disk,PD)上的文件，内容会被永久保存。
  当pod被删除时，PD只是被卸载，但不会被删除，使用前要先创建一个PD
* awsElasticBlockStore
  使用Amazon提供的Amazon web Services(AWS)的EBS Volume，并可以挂载到pod中
* nfs
  使用NFS(网络文件系统)提供的共享目录挂载，需要系统中有一个运行中的NFS系统
* iscsi
* glusterfs
* rbd
* gitRepo
* secret
* persistentVolumeClaim

### Namespace
使不同的分组在共享使用整个集群的资源时还能被分别管理
### Annotation
是用户定义的附加信息，便于外部工具查找

## 宏观的看看
### 发展
> 由来

kubernetes是一个全新的基于容器技术的分布式架构领先方案，但实际上它是google几十年来大规模应用容器技术的经验积累。
**Borg** 的一个开源版本。kubernetes提供的解决方案和强大的自动化机制，使得系统后期的运维难度和运维成本大幅度降低。
kubernetes没有语言限制，通过标准的TCP通信协议进行交互。
这是一个完备的分布式系统支撑平台，具有完备的集群管理能力，包括多层次的安全防护和准入机制，多租户应用支撑能力，透明的服务注册和服务发现机制，内建智能负载均衡器，强大的故障发现和自我修复能力、服务滚动升级和在线扩容能力、可扩展的资源自动调度机制，以及多粒度的资源配额管理能力。
同时还提供了完善的管理工具，包括开发，部署，测试，运维监控。
所以是一个一站式的完备的分布式系统开发和支撑平台。

> 核心

service是分布式集群机构的核心，service的服务进程目前都基于Socket通信方式对位提供服务，如Redis,Memacache,MySQL,Web Server,或者是实现了某一个具体业务的一个特定TCP Server服务。
一个服务一旦创建就不再变化，只要通过这个固定的虚拟ip就可以访问到。
每一个pod里运行着一个称为pause的容器，其他称为业务容器，这些容器共享pause容器的网络栈和volume挂载卷，彼此之间通信和数据交换更为高效。
只有那些提供服务的一组pod才被映射一个服务。

> 服务扩容和系统升级

服务扩容涉及资源分配(选择哪个节点进行扩容)、实例部署和启动，在kubernetes中，只需要为扩容的service关联的pod创建一个ReplicationController，则该service的扩容以至于后来的service升级等问题得到解决。
一个rc的定义文件包括：pod的定义、pod需要运行的副本数量(Eplicas)、要监控的目标pod的label。
service升级也通过修改rc来自动完成。

> 微服务

微服务架构的核心是将一个巨大的单体应用分解为很多小的互相连接的微服务，一个微服务后面可能有多个实例副本在支撑，副本的数量可能会随着系统的负荷变化而进行调整，内嵌的负载均衡起了很大作用。
每个微服务独立开发、升级、扩展，系统具有很高的稳定性和快速迭代进化能力。
kubernetes架构具备超强的横向扩容能力。

## 使用
### 起一个本地集群
首先cd进本地的kubernetes文件夹
然后执行
```
hack/local-up-cluster.sh    
```
接着新开一个terminal，然后设置如下
```
export KUBERNETES_PROVIDER=local
cluster/kubectl.sh config set-cluster local --server=http://127.0.0.1:8080 --insecure-skip-tls-verify=true
cluster/kubectl.sh config set-context local --cluster=local
cluster/kubectl.sh config use-context local
```
这样就可以开始用kubectl来管理查看这个集群了。
