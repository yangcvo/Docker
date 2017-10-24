# Docker学习(三)：容器应用

启动容器

启动容器有两种方式，一种是基于镜像新建一个容器并启动，另外一个是将在终止

状态（stopped）的容器重新启动。

因为 Docker 的容器实在太轻量级了，很多时候用户都是随时删除和新创建容器。

docker 客户端非常简单 。Docker 的每一项操作都是通过命令行来实现的，而每一条命令行都可以使用一系列的标识（flags）和参数。

# Usage:  [sudo] docker [flags] [command] [arguments] ..
# Example:
$ docker run -i -t centos /bin/bash
这个命令不仅返回了您使用的 Docker 客户端和进程的版本信息，还返回了 GO 语言的版本信息( Docker的编程语言 )。

一个交互式的容器

所需要的命令主要为 docker run 。

例如，下面的命令输出一个 “Hello World”，之后终止容器。

```
[root@docker ~]# docker run -i -t docker.io/centos  /bin/echo 'Hello world'
 Hello world
这跟在本地直接执行/bin/echo 'hello world'几乎感觉不出任何区别。

让我们尝试再次运行 docker run，这次我们指定一个新的命令来运行我们的容器。

$ sudo docker run -t -i centos  /bin/bash

[root@docker opt]# sudo docker run -t -i centos  /bin/bash
[root@04a1e7967683 /]#
我们继续指定了 docker run 命令，并启动了 centos 镜像。但是我们添加了两个新的标识(参数flags)： -t 和 -i 。-t表示在新容器内指定一个伪终端或终端，-i表示允许我们对容器内的 (STDIN) 进行交互。

我们在容器内还指定了一个新的命令： /bin/bash 。这将在容器内启动 bash shell 所以当容器（container）启动之后，我们会获取到一个命令提示符：

 root@04a1e7967683 /]#
我们尝试在容器内运行一些命令：

[root@04a1e7967683 /]# pwd
/
[root@04a1e7967683 /]# ls
anaconda-post.log  bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```
你可以看到我们运行 pwd 来显示当前目录，这时候显示的是我们的根目录。我们还列出了根目录的文件列表，通过目录列表我们看出来这是一个典型的 Linux 文件系统。

你可以在容器内随便的玩耍，你可以使用 exit 命令或者使用 CTRL-D 来退出容器。

与我们之前的容器一样，一旦你的 Bash shell 退出之后，你的容器就停止了。

当利用 docker run 来创建容器时，Docker 在后台运行的标准操作包括：

* 检查本地是否存在指定的镜像，不存在就从公有仓库下载
* 利用镜像创建并启动一个容器
* 分配一个文件系统，并在只读的镜像层外面挂载一层可读写层
* 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去
* 从地址池配置一个 ip 地址给容器
* 执行用户指定的应用程序
* 执行完毕后容器被终止
启动已终止容器

可以利用 docker start 命令，直接将一个已经终止的容器启动运行。

容器的核心为所执行的应用程序，所需要的资源都是应用程序运行所必需的。除此

之外，并没有其它的资源。可以在伪终端中利用 ps 或 top 来查看进程信息。

[root@149f2055cb10 /]# ps
  PID TTY          TIME CMD
    1 ?        00:00:00 bash
   15 ?        00:00:00 ps
可见，容器中仅运行了指定的 bash 应用。这种特点使得 Docker 对资源的利用率

极高，是货真价实的轻量级虚拟化。

后台(background)运行

更多的时候，需要让 Docker在后台运行而不是直接把执行命令的结果输出在当前

宿主机下。此时，可以通过添加-d参数来实现。

下面举两个例子来说明一下。

如果不使用-d 参数运行容器。

root@docker ~]# docker run -i -t docker.io/centos  /bin/sh -c "while true; do echo hello world; sleep 1 ; done"
hello world
hello world
hello world
hello world
hello world
hello world
hello world
hello world
容器会把输出的结果(STDOUT)打印到宿主机上面

这里我是用脚本让他一直输出出来，那么如果使用了 -d参数去运行容器。

这里我也举了个例子。

[root@docker ~]# docker run -d -i -t docker.io/centos  /bin/sh -c "while true; do echo hello world; sleep 1 ; done"
2581176d1d864860b754293f9c4606698d5fcb290bb1de2800afd5a8cf441f2e
此时容器会在后台运行并不会把输出的结果(STDOUT)打印到宿主机上面(输出结果

可以用docker logs 查看)。

注： 容器是否会长久运行，是和docker run指定的命令有关，和 -d 参数无关。
使用` -d` 参数启动后会返回一个唯一的 `id`，也可以通过 docker ps命令来查看 容器信息

[root@docker ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
2581176d1d86        docker.io/centos    "/bin/sh -c 'while tr"   2 minutes ago       Up 2 minutes                            fervent_wescoff
要获取容器的输出信息，可以通过 docker logs 命令。

[root@docker ~]# docker logs [container ID or NAMES]
hello world
hello world
hello world
.....

[root@docker ~]# docker logs 2581176d1d86
hello world
hello world
hello world
.....
终止容器

现在我们已经可以创建我们自己的容器了，让我们处理正在运行的进程容器并停止它。我们使用 docker stop 命令来停止容器 。

    $ sudo docker stop sad_shaw
    sad_shaw
此外，当Docker容器中指定的应用终结时，容器也自动终止。 例如对于上一章节

中只启动了一个终端的容器，用户通过 exit 命令或 Ctrl+d 来退出终端时，

所创建的容器立刻终止。

终止状态的容器可以用 docker ps -a 命令看到。例如:

```bash
[root@docker ~]# docker ps -a
CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS                           PORTS               NAMES
2581176d1d86        docker.io/centos        "/bin/sh -c 'while tr"   8 minutes ago       Up 8 minutes                                         fervent_wescoff
6decd2bc924e        docker.io/centos        "/bin/sh -c 'while tr"   19 minutes ago      Up 19 minutes                                        pensive_jepsen
6b747a38d1ab        docker.io/centos        "/bin/sh -c 'while tr"   21 minutes ago      Up 21 minutes                                        jovial_swirles
149f2055cb10        docker.io/centos        "/bin/bash"              48 minutes ago      Exited (0) 22 minutes ago                            focused_hawking
ea9cb48d5aea        docker.io/centos        "/bin/echo 'Hello wor"   48 minutes ago      Exited (0) 48 minutes ago                            condescending_feynman
c90d7129c34e        docker.io/centos        "/bin/bash 'Hello wor"   49 minutes ago      Exited (127) 49 minutes ago                          pedantic_kirch
8ac4d548112e        docker.io/centos        "/bin/bash"              About an hour ago   Exited (0) 49 minutes ago                            adoring_swartz
a258ef1ef83b        centos                  "/bin/bash"              About an hour ago   Exited (127) About an hour ago                       determined_s

处于终止状态的容器，可以通过 docker start 命令来重新启动。

[root@docker ~]# docker stop 6b747a38d1ab
6b747a38d1ab
[root@docker ~]# docker stop 6decd2bc924e
6decd2bc924e
```
此外， docker restart 命令会将一个运行态的容器终止，然后再重新启动它。

docker stop 命令会通知 Docker 停止正在运行的容器。如果它成功了，它将返回刚刚停止的容器名称。

让我们通过 docker ps 命令来检查它是否还工作。

###进入容器

在使用 -d参数时，容器启动后会进入后台。 某些时候需要进入容器进行操作，
有很多种方法，包括使用 docker attach 命令或 nsenter 工具等。

attach 命令

docker attach 是Docker自带的命令。下面示例如何使用该命令。

```
[root@docker ~]# docker run -idt centos
bc9fc22c17a3c41e51ebe30a10d9f1554d51f468651fae6b41764fc8f6435357
[root@docker ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
bc9fc22c17a3        centos              "/bin/bash"              10 seconds ago      Up 9 seconds                            cranky_poitras
2581176d1d86        docker.io/centos    "/bin/sh -c 'while tr"   19 minutes ago      Up 18 minutes                           fervent_wescoff
28e833d4a45f        centos              "/bin/bash"              4 weeks ago         Up 4 weeks                              sad_shaw
[root@docker ~]# docker attach sad_shaw
[root@28e833d4a45f /]#
```
但是使用 attach命令有时候并不方便。当多个窗口同时 attach到同一个容器的

时候，所有窗口都会同步显示。当某个窗口因命令阻塞时,其他窗口也无法执行操作

了。

#### 导出和导入容器

导出容器

如果要导出本地某个容器，可以使用 docker export 命令。

```bash
[root@docker ~]#  docker ps -a
CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS                           PORTS               NAMES
28e833d4a45f        centos                  "/bin/bash"              4 weeks ago         Up 2 seconds                                         sad_shaw
[root@docker ~]# docker export sad_shaw  > centos.tar
[root@docker ~]# ll
总用量 199584
-rw-------. 1 root root       947 7月  12 13:54 anaconda-ks.cfg
-rw-r--r--. 1 root root 204362240 8月  11 04:48 centos.tar
-rw-r--r--. 1 root root       291 8月  10 04:18 syslog.sh
```
这样将导出容器快照到本地文件。

#### 导入容器快照

可以使用 docker import 从容器快照文件中再导入为镜像，例如：

```bash
[root@docker ~]# cat centos.tar | docker import - test/centos
sha256:952fc37f4a2dc8604cbc3d624369fec8a6b0e2d9a09af294461944f34ddf7f04
[root@docker ~]# docker images
REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
test/centos                latest              952fc37f4a2d        11 seconds ago      196.8 MB
docker.io/bitnami/apache   latest              240bf1b03f01        4 weeks ago         283.1 MB
docker.io/centos           latest              05188b417f30        5 weeks ago         196.8 MB
```

这里你可以看到REPOSITORY 下面test的镜像文件大小都一样的。

此外，也可以通过指定 URL 或者某个目录来导入，例如

$sudo docker import http://example.com/exampleimage.tgz example/
imagerepo
注：用户既可以使用 docker load 来导入镜像存储文件到本地镜像库，也可以

使用 docker import 来导入一个容器快照到本地镜像库。这两者的区别在于容

器快照文件将丢弃所有的历史记录和元数据信息（即仅保存容器当时的快照状

态），而镜像存储文件将保存完整记录，体积也要大。此外，从容器快照文件导入

时可以重新指定标签等元数据信息。

#### 删除容器

可以使用 docker rm 来删除一个处于终止状态的容器。 例如:

[root@docker ~]# docker rm naughty_boyd
naughty_boyd
如果要删除一个运行中的容器，可以添加 -f参数。Docker 会发送 SIGKILL

信号给容器。

```bash
root@docker ~]# docker ps -l
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
6b747a38d1ab        docker.io/centos    "/bin/sh -c 'while tr"   36 hours ago        Exited (137) 36 hours ago                       jovial_swirles
[root@docker ~]# docker
docker                docker-current        docker-storage-setup
[root@docker ~]# docker rm -f 6b747a38d1ab
6b747a38d1ab
清理所有处于终止状态的容器
```

用 docker ps -a 命令可以查看所有已经创建的包括终止状态的容器，如果数量
太多要一个个删除可能会很麻烦，用 docker rm $(docker ps -a -q)可以全

部清理掉。

例子：

```bash
[root@docker ~]# docker rm $(docker ps -a -q)
149f2055cb10
ea9cb48d5aea
c90d7129c34e
8ac4d548112e
a258ef1ef83b
10942e7856ab
e30fe573c06c
712498a4a633
04a1e7967683
28e833d4a45f
```
注意：这个命令其实会试图删除所有的包括还在运行中的容器，不过就像上面提过 的 docker rm 默认并不会删除运行中的容器。
新手经常会有疑问的是关于Docker打包的粒度，比如MySQL要不要放在镜像中？最佳实践是根据应用的规模和可预见的扩展性来确定Docker打包的粒度。例如某小型项目管理系统使用LAMP环境，由于团队规模和使用人数并不会有太大的变化（可预计的团队规模范围是几人到几千人），数据库也不会承受无法承载的记录数（生命周期内可能一个表最多会有数十万条记录），并且客户最关心的是快速部署使用。那么这时候把MySQL作为依赖放在镜像里是一种不错的选择。当然如果你在为一个互联网产品打包，那最好就是把MySQL独立出来，因为MySQL很可能会单独做优化做集群等。

使用公有云构建发布运行Docker也是个不错的选择。DaoCloud提供了从构建到发布到运行的全生命周期服务。特别适合像微擎这种微信公众平台、或者中小型企业CRM系统。上线周期更短，比使用IAAS、PAAS的云服务更具有优势。

小结

有关Dockerfile的进阶阅读：

http://www.docker.io/learn/dockerfile/level2/
http://www.docker.io/learn/dockerfile/level2/

