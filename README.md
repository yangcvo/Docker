# Docker

## Open source application container engine, Linux internship virtualization

1. Docker is not all powerful, the design of the beginning of the KVM is not a substitute for a virtual method, a simple summary of several points:
2. Docker is based on 64bit Linux, can not be used in the linux/Windows/unix 32bit environment
3. LXC is based on CGroup and other kernel Linux function, so the guest Linux system can only be base container
4. Isolation compared to KVM or the like of the virtualization program is somewhat lacking, all the container common part of the operation of the library
5. Network management is relatively simple, mainly based on namespace isolation
6. CPU's CGroup and cpuset provide the CPU function compared to KVM and other virtualization programs are difficult to measure (so dotcloud is mainly by memory charges)
7. Docker management of disk is relatively limited
8. Container with the process of the user to stop the process of destruction, log container and other user data is not collected
   
* [Docker（一）：简介入门](https://github.com/yangcvo/Docker/blob/master/README.md)
* [Docker学习(二)：安装篇](https://github.com/yangcvo/Docker/blob/master/Docker%E5%AD%A6%E4%B9%A0(%E4%BA%8C)%EF%BC%9A%E5%AE%89%E8%A3%85%E7%AF%87.md)
* [Docker学习(三)：容器应用](https://github.com/yangcvo/Docker/blob/master/Docker%E5%AD%A6%E4%B9%A0(%E4%B8%89)%EF%BC%9A%E5%AE%B9%E5%99%A8%E5%BA%94%E7%94%A8.md)
* [Docker学习(四)虚拟容器ssh登录问题解决方法](https://github.com/yangcvo/Docker/blob/master/%20Docker%E8%99%9A%E6%8B%9F%E5%AE%B9%E5%99%A8ssh%E7%99%BB%E5%BD%95%E9%97%AE%E9%A2%98%E8%A7%A3%E5%86%B3%E6%96%B9%E6%B3%95.md)
* [Docker学习(五)：容器内多进程管理](https://github.com/yangcvo/Docker/blob/master/Docker%E5%AD%A6%E4%B9%A0(%E4%BA%94)%EF%BC%9A%E5%AE%B9%E5%99%A8%E5%86%85%E5%A4%9A%E8%BF%9B%E7%A8%8B%E7%AE%A1%E7%90%86.md)
* [Docker学习(六)：部署nginx](https://github.com/yangcvo/Docker/blob/master/Docker%E5%AD%A6%E4%B9%A0(%E5%85%AD)%EF%BC%9A%E9%83%A8%E7%BD%B2nginx.md)
* [Docker学习(七)：Docker命令总结学习积累经验](https://github.com/yangcvo/Docker/blob/master/Docker%E5%AD%A6%E4%B9%A0(%E4%B8%83)%EF%BC%9ADocker%E5%91%BD%E4%BB%A4%E6%80%BB%E7%BB%93%E5%AD%A6%E4%B9%A0%E7%A7%AF%E7%B4%AF%E7%BB%8F%E9%AA%8C.md)
* [Docker学习笔记(九)如何将docker镜像上传到docker.hub仓库](https://github.com/yangcvo/Docker/blob/master/%20Docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0(%E4%B9%9D)%E5%A6%82%E4%BD%95%E5%B0%86docker%E9%95%9C%E5%83%8F%E4%B8%8A%E4%BC%A0%E5%88%B0docker.hub%E4%BB%93%E5%BA%93.md)
* [Docker学习(十)：部署jdk](https://github.com/yangcvo/Docker/blob/master/Docker%E5%AD%A6%E4%B9%A0(%E5%8D%81)%EF%BC%9A%E9%83%A8%E7%BD%B2jdk.md)
* [Docker学习（十一）故障处理docker: Error response from daemon: failed to create endpoint db01 on network ](https://github.com/yangcvo/Docker/blob/master/Docker%E5%AD%A6%E4%B9%A0%EF%BC%88%E5%8D%81%E4%B8%80%EF%BC%89%E6%95%85%E9%9A%9C%E5%A4%84%E7%90%86docker:%20Error%20response%20from%20daemon:%20failed%20to%20create%20endpoint%20db01%20on%20network%20bridge.md)
* [Docker学习(十二) SSH远程登陆docker容器](https://github.com/yangcvo/Docker/blob/master/Docker%E5%AD%A6%E4%B9%A0(%E5%8D%81%E4%BA%8C)%20SSH%E8%BF%9C%E7%A8%8B%E7%99%BB%E9%99%86docker%E5%AE%B9%E5%99%A8.md)
* [Docker容器启动过程](https://github.com/yangcvo/Docker/blob/master/Docker%E5%AE%B9%E5%99%A8%E5%90%AF%E5%8A%A8%E8%BF%87%E7%A8%8B.md)
* [Docker容器部署tomcat出现中文乱码](https://github.com/yangcvo/Docker/blob/master/Docker%E5%AE%B9%E5%99%A8%E9%83%A8%E7%BD%B2tomcat%E5%87%BA%E7%8E%B0%E4%B8%AD%E6%96%87%E4%B9%B1%E7%A0%81.md)

## Docker 简介

Docker入门简介的个人理解：
在大型的互联网已大部分在使用docker容器，主要是因为对单个应用的确提供了非常方便。迁移环境扩容部署只需要几分钟就可以做到上百台上千台。

理解分几点：

```bash
1.Docker虚拟化对比：跟传统的虚拟化对比 XEN或KVM区别。

因为XEN和KVM这些传统方式则是在硬件的基础上，虚拟出自己的系统，在系统上班部署相关的APP应用。
而容器docker是在这些虚拟机或者操作系统层面实现虚拟化，直接复用本地主机的操作系统，实现快速部署相同的应用。
这个在云加速云领域用的比较多。

2.传统虚拟化方案，和Docker虚拟化方案对比：

传统虚拟化：都是基于管理软件，去创建多一级别的虚拟机。
Docker虚拟化：直接把应用当一个容器，不需要调用本机系统。

3.Docker虚拟化有三个概念：分别镜像，容器，仓库。
（1）镜像：docker镜像其实就是模板，跟我们ISO镜像很相似，刻录了一套应用部署到其他机器上面运行。
（2）容器：使用镜像常用应用或者系统，比如：tomcat，nginx,Apache等等应用就是称之为容器。
（3）仓位：仓位是放镜像的地方。分公开的仓库，和私有的仓库。
```
## Docker特点

## 跟传统VM比较具有如下优点：

```
1）操作启动快
运行的性能可以获得极大的提升，管理操作（启动，停止，开启，关闭，重启等等）都是以秒或者毫秒计算为单位。
2)轻量级虚拟化
就是好比拥有足够的”操作系统“仅需要添加减少镜像即可，在一台服务器可以部署100~1000个容器，但是传统的虚拟化10到20台就不错了。
3）开源免费的。低成本。
```
## 跟传统KVM-vm比较缺点：

在我接触中，刚刚开始维护自动化运维云加速就有使用到docker，去单独跑一个应用，用Python脚本写的。
现在对比目前知道的人比较少，用在每个行业也比较少。
相关资料也比较欠缺。
GO语言还不是特别成熟
上面是我对Docker个人的理解，下面我也贴出我学习Docker的文档。
这里开始学习docker 是看docker官网文章写的特别详细。

下面是我官网一些介绍：

## Docker是什么

Docker是一种容器技术，它可以将应用和环境等进行打包，形成一个独立的，类似于iOS的APP形式的“应用”，这个应用可以直接被分发到任意一个支持Docker的环境中，通过简单的命令即可启动运行。Docker是一种最流行的容器化实现方案。和虚拟化技术类似，它极大的方便了应用服务的部署；又与虚拟化技术不同，它以一种更轻量的方式实现了应用服务的打包。使用Docker可以让每个应用彼此相互隔离，在同一台机器上同时运行多个应用，不过他们彼此之间共享同一个操作系统。Docker的优势在于，它可以在更细的粒度上进行资源的管理，也比虚拟化技术更加节约资源。

Docker 提供了一个可以运行你的应用程序的封套(envelope)，或者说容器。它原本是 dotCloud 启动的一个业余项目，并在前些时候开源了。它吸引了大量的关注和讨论，导致 dotCloud 把它重命名到 Docker Inc。它最初是用 Go 语言编写的，它就相当于是加在 LXC（LinuX Containers，linux 容器）上的管道，允许开发者在更高层次的概念上工作。

Docker 扩展了 Linux 容器（Linux Containers），或着说 LXC，通过一个高层次的 API 为进程单独提供了一个轻量级的虚拟环境。Docker 利用了 LXC， cgroups 和 Linux 自己的内核。和传统的虚拟机不同的是，一个 Docker 容器并不包含一个单独的操作系统，而是基于已有的基础设施中操作系统提供的功能来运行的。

## Docker能做什么？

Docker可以解决虚拟机能够解决的问题，同时也能够解决虚拟机由于资源要求过高而无法解决的问题。Docker能处理的事情包括：

```

隔离应用依赖
创建应用镜像并进行复制
创建容易分发的即启即用的应用
允许实例简单、快速地扩展
测试应用并随后销毁它们 Docker背后的想法是创建软件程序可移植的轻量容器，让其可以在任何安装了Docker的机器上运行，而不用关心底层操作系统，就像野心勃勃的造船者们成功创建了集装箱而不需要考虑装在哪种船舶上一样。

```

## 基本概念

* 开始试验Docker之前，我们先来了解一下Docker的几个基本概念：

```
1. 虚拟化技术依赖物理CPU和内存，是硬件级别的；而docker构建在操作系统上，利用操作系统的containerization技术，所以docker甚至可以在虚拟机上运行。
2. 虚拟化系统一般都是指操作系统镜像，比较复杂，称为“系统”；而docker开源而且轻量，称为“容器”，单个容器适合部署少量应用，比如部署一个redis、一个memcached。
3. 传统的虚拟化技术使用快照来保存状态；而docker在保存状态上不仅更为轻便和低成本，而且引入了类似源代码管理机制，将容器的快照历史版本一一记录，切换成本很低。
4. 传统的虚拟化技术在构建系统的时候较为复杂，需要大量的人力；而docker可以通过Dockfile来构建整个容器，重启和构建速度很快。更重要的是Dockfile可以手动编写，这样应用程序开发人员可以通过发布 Dockfile来指导系统环境和依赖，这样对于持续交付十分有利。
5. Dockerfile可以基于已经构建好的容器镜像，创建新容器。Dockerfile可以通过社区分享和下载，有利于该技术的推广。
6. Docker 会像一个可移植的容器引擎那样工作。它把应用程序及所有程序的依赖环境打包到一个虚拟容器中，这个虚拟容器可以运行在任何一种 Linux 服务器上。这大大地提高了程序运行的灵活性和可移植性，无论需不需要许可、是在公共云还是私密云、是不是裸机环境等等。
7. Docker也是一个云计算平台，它利用Linux的LXC、AUFU、Go语言、cgroup实现了资源的独立，可以很轻松的实现文件、资源、网络等隔离，其最终的目标是实现类似PaaS平台的应用隔离。
```

## Docker 由下面这些组成

```bash
Docker 服务器守护程序（server daemon），用于管理所有的容器。
Docker 命令行客户端，用于控制服务器守护程序。
Docker 镜像：查找和浏览 docker 容器镜像。
```

* 镜像：我们可以理解为一个预配置的系统光盘，这个光盘插入电脑后就可以启动一个操作系统。当然由于是光盘，所以你无法修改它或者保存数据，每次重启都是一个原样全新的系统。Docker里面镜像基本上和这个差不多。

* 容器：同样一个镜像，我们可以同时启动运行多个，运行期间的产生的这个实例就是容器。把容器内的操作和启动它的镜像进行合并，就可以产生一个新的镜像。

开始Docker基于LXC技术实现，依赖于Linux内核，所以Docker目前只能在Linux以原生方式运行。目前主要的Linux发行版在他们的软件仓库中内置了Docker：

```bash
Ubuntu:
Ubuntu Trusty 14.04 (LTS)
Ubuntu Precise 12.04 (LTS)
Ubuntu Saucy 13.10

Mac:
Mac OS X 大于等于 10.8 "Snow Leopard" 才可以安装 Docker Toolbox。

CentOS:
CentOS 7
```

## 容器

现在这里都说容器了。你可以从镜像中创建容器，这等同于从快照中创建虚拟机，不过更轻量。应用是由容器运行的。

容器与虚拟机一样，是隔离的（有一点要注意，我稍后会讨论到）。它们也拥有一个唯一ID和唯一的供人阅读的名字。容器对外公开服务是必要的，因此Docker允许公开容器的特定端口。

## 这里画了图举了个例子：

容器是设计来运行一个应用的，而非一台机器。你可能会把容器当虚拟机用，但如我们所见，你将失去很多的灵活性，因为Docker提供了用于分离应用与数据的工具，使得你可以快捷地更新运行中的代码/系统，而不影响数据。

## 数据卷

数据卷让你可以不受容器生命周期影响进行数据持久化。它们表现为容器内的空间，但实际保存在容器之外，从而允许你在不影响数据的情况下销毁、重建、修改、丢弃容器。Docker允许你定义应用部分和数据部分，并提供工具让你可以将它们分开。使用Docker时必须做出的最大思维变化之一就是：容器应该是短暂和一次性的。

卷是针对容器的，你可以使用同一个镜像创建多个容器并定义不同的卷。卷保存在运行Docker的宿主文件系统上，你可以指定卷存放的目录，或让Docker保存在默认位置。保存在其他类型文件系统上的都不是一个卷，稍后再具体说。

### 阅读卷的文章：docker数据卷

参考资料

[深入理解Docker Volume](http://dockone.io/article/128)
[Docker学习——镜像导出](http://www.sxt.cn/u/756/blog/5339)
[Dockerfile Reference]( https://docs.docker.com/reference/builder/)

关于Dockerfile http://blog.tankywoo.com/docker/2014/05/08/docker-2-dockerfile.html 

### Docker总结学习积累经验：

现在对Docker这个运行的原理以及命令都已经非常清楚，因为自己整理了很多Docker基本命令，已经需要注意都刻意标注：

谈谈学习Docker心得体会：

14年就开始接触，后面docker只是知道是容器类似vm虚拟机一样，就是在虚拟机里面跑单独隔离的服务，这是那时候的单面了解，后面学KVM用的感觉还不错，后面VM就慢慢的不再使用，因为针对KVM也深入去学习了。开始学习docker，openstack服务器云平台。


在前面学习6篇文章，是我本人自己总结的学习经验。


下面整理centos 系统操作命令：

```bash
yum -y remove docker         先卸载之前的包
yum install docker-io        安装

service docker start         启动服务
chkconfig docker on          开机启动

docker pull centos           网上下载centos镜像。
docker version               docker查看版本
docker -help                 docker查看帮助命令

docker images centos        docker查看centos镜像
docker run -i -t centos:latest /bin/bash   启动一个新的容器。后面latest是版本名称

docker ps 列出容器
docker ps -l 列出前台在运行的镜像容器
docker ps -a 查看所有在运行和没有运行的镜像容器
docker logs 显示容器的标准输出
docker stop 停止正在运行的容器
docker restart 容器终止然后重新启动它。
docker run -i -t 启动运行容器。
docker run -i -t -d  让容器后台运行程序。
docker attach sad_shaw 进入到指定容器。**非常方便**
docker exec -it eafd9111ada6  /bin/bash  # 进入容器
docker export sad_shaw  > /root/centos.tar  镜像导出到服务器指定目录。没有指定绝对目录默认当前目录。
docker export sad_shaw  > centos.tar sad_shaw 镜像到处-到处为centos.tar
cat centos.tar | docker import - test/centos    这里是把centos.tar导入到新的镜像命名为/test/centos
docker rm  sad_shaw               删除容器sad_shaw
docker rm -f sad_shaw             删除运行中的容器sad_shaw
docker rm $(docker ps -a -q)      清理所有处于停止的容器
docker rmi <image id>             删除images，通过image的id来指定删除谁
docker rmi $(docker images -q)    删除全部image
docker rmi $(docker images | grep "^<none>" | awk "{print $3}")
```

想要删除untagged images，也就是那些id为<None>的image的话可以用。


**Docker下载centos虚拟机**

    docker pull docker.io/jdeathe/centos-ssh

**启动虚拟机**
     
 docker run -i -t docker.io/jdeathe/centos-ssh  /bin/bash
 


## 其他信息

blog地址：https://blog.yancy.cc




