
# Docker搭建JAVA Tomcat运行环境

#### 前言

Docker在提供一种应用程序的自动化部署解决方案，在 Linux 系统上迅速创建一个容器（轻量级虚拟机）并部署和运行应用程序，并通过配置文件可以轻松实现应用程序的自动化安装、部署和升级，非常方便。

因为使用了容器，所以可以很方便的把生产环境和开发环境分开，互不影响，这是 docker 最普遍的一个玩法。更多的玩法还有大规模 web 应用、数据库部署、持续部署、集群、测试环境、面向服务的云计算、虚拟桌面 VDI 等等。


#### docker部署tomcat

第一： 可以隔离每个tomcat服务单独跑。 这样资源可以很好的利用。
第二： 后期用jenkins结合docker部署tomcat。 非常方便。
第三： tomcat重点服务扩容迁移也很方便。只需要生成镜像在其他的虚拟机上面跑就行。


#### 部署tomcat环境

这里是使用KVM 虚拟的4G内存的centos 7.1 x64位系统 ，也可以使用ubuntu-13.10以上

#### 安装docker 

这个也写过文档，多种linux系统安装docker .这里不详细讲解。


这里安装的Tomcat继承了之前JDK7的Docker镜像，因为运行Tomcat需要依赖JDK。

#### 大概步骤：

* 上传Tomcat/8.0.32到宿主机
* 编写Dockerfile构建镜像
* 编写supervisor配置文件
* build和run

```bash
# 方式一：可以通过ssh上传指定版本的tomcat（这里选择第一种）
# 1. 上传tomcat7到宿主机
# 2. 将tomcat7都解压到指定的目录下（和Dockerfile文件同目录）

# 方式二：从官网或者镜像网站下载Tomcat/8.0.32

# 方法三： 从官网下载好scp到docker tomcat容器指定目录
```

Dockerfile文件


```bash
############################################
# version : centos/Tomcat/8.0.32
# desc : 当前版本安装的Tomcat/8.0.32
############################################
# 设置继承自我们创建的 jdk8 镜像
FROM yangc/jdk8

# 下面是一些创建者的基本信息
MAINTAINER birdben (yangcvo@gmail.com)

# 设置环境变量，所有操作都是非交互式的
ENV DEBIAN_FRONTEND noninteractive

# 添加 supervisord 的配置文件，并复制配置文件到对应目录下面。（supervisord.conf文件和Dockerfile文件在同一路径）
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# 设置 tomcat 的环境变量，若读者有其他的环境变量需要设置，也可以在这里添加。
ENV CATALINA_HOME /srv/tomcat/tomcat

# 复制 apache-tomcat-8.0.32 文件到镜像中（apache-tomcat-8.0.32 文件夹要和Dockerfile文件在同一路径）
ADD apache-tomcat-8.0.32 /srv/tomcat/tomcat

# 容器需要开放Tomcat 8080端口
EXPOSE 8080

# 容器需要开放SSH 22端口
EXPOSE 22

# 执行supervisord来同时执行多个命令，使用 supervisord 的可执行路径启动服务。
CMD ["/usr/bin/supervisord"]
```

Dockerfile源文件链接：[dockerfile-tomcat](https://github.com/yangcvo/Docker/blob/master/tomcat8.0/Dockerfile)




#### supervisor配置文件内容

```bash
# 配置文件包含目录和进程
# 第一段 supervsord 配置软件本身，使用 nodaemon 参数来运行。
# 第二段包含要控制的 2 个服务。每一段包含一个服务的目录和启动这个服务的命令。

[supervisord]
nodaemon=true

[program:sshd]
command=/usr/sbin/sshd -D

[program:tomcat]
command=/bin/bash -c "exec ${CATALINA_HOME}/bin/catalina.sh run"
```

运行：
脚本写好了，需要转换成镜像：

```bash
# 构建镜像
docker build -t="yangc/tomcat8" .

-t： 为构建的镜像制定一个标签，便于记忆/索引等

. ： 指定Dockerfile文件在当前目录下

网速不太好，会等待很长时间。很多操作可能需要科学上网，逼得我只能一直挂着VPN，方能畅通无阻。

构建镜像完成之后，看看运行效果：


# 执行已经构件好的镜像

docker run -p 9999:22 -p 10001:8080 -t -i yangc/tomcat8


#这里也可以导出某个容器。不是按脚本去跑的。这里自己配置好的容器。导出

docker export 容器name  > tomcat8.tar

#导入

cat tomcat8.tar | docker import - yangc/tomcat8

```
在运行命令中，还得需要显式指定 -p 22 -p 8080:8080，否则在Docker 0.8.1版本中不会主动映射到宿主机上。据悉在Docker 0.4.8版本时，就不担心这个问题。 或者，您要有好的方式，不妨告知于我，谢谢。

在Dockerfile中，若没有使用ENTRYPOINT/CMD指令，若运行多个命令，可以这样做：


    docker run -d -p 22 -p 8080 yangc/tomcat8 /bin/sh -c "service tomcat start && /usr/sbin/sshd -D"


浏览器访问:

     http://虚拟机IP:10001/


