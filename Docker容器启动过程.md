### Docker容器启动过程

下面让我们看看一个Docker容器它启动过程中，背后到底做了什么？


    docker run -i -t ubuntu /bin/bash

输入上面这行命令，启动一个ubuntu容器时，到底发生了什么？
 
大致过程可以用下图描述：

![](http://7xrthw.com1.z0.glb.clouddn.com/Docker.qidon.png)

首先系统要有一个docker daemon的后台进程在运行，当刚才这行命令敲下时，发生了如下动作：

1. docker client(即：docker终端命令行)会调用docker daemon请求启动一个容器，
2. docker daemon会向host os(即：linux)请求创建容器
3. linux会创建一个空的容器（可以简单理解为：一个未安装操作系统的裸机，只有虚拟出来的CPU、内存等硬件资源）
4. docker daemon请检查本机是否存在docker镜像文件（可以简单理解为操作系统安装光盘），如果有，则加载到容器中（即：光盘插入裸机，准备安装操作系统）
5. 将镜像文件加载到容器中（即：裸机上安装好了操作系统，不再是裸机状态）
 
最后，我们就得到了一个ubuntu的虚拟机，然后就可以进行各种操作了。
 
如果在第4步检查本机镜像文件时，发现文件不存在，则会到默认的docker镜像注册机构（即：docker hub网站）去联网下载，下载回来后，再进行装载到容器的动作，即下图所示：

![](http://7xrthw.com1.z0.glb.clouddn.com/Docker.qidong.png)

另外官网有一张图也很形象的描述了这个过程：


![](http://7xrthw.com1.z0.glb.clouddn.com/Docker.qidong2.png)


参考文章：
https://www.gitbook.com/book/joshhu/docker_theory_install/details  
https://docs.docker.com/engine/introduction/understanding-docker/ 

