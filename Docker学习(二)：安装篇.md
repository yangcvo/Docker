# Docker学习(二)：安装篇


CentOS 系列安装Docker

以下版本的CentOS 支持 Docker ：

CentOS 7 (64-bit)
CentOS 6.5 (64-bit) or later

该指南可能会适用于其它的 EL6/EL7 的 Linux 发行版，譬如 Scientific Linux 。但是我们没有做过任何测试。

请注意，由于 Docker 的局限性，Docker 只能运行在64位的系统中。

这些操作系统下可以直接通过命令安装Docker，老一些操作系统Docker官方也提供了安装方案。下面的实验基于CentOS 7进行。关于其他版本操作系统上Docker的安装，请参考官方文档

#### 内核支持

目前的CentOS项目，仅发行版本中的内核支持Docker。如果你打算在非发行版本的内核上运行 Docker，内核的改动可能会导致出错。

Docker 运行在 CentOS-6.5 或更高的版本的 CentOS 上，需要内核版本是 2.6.32-431 或者更高版本 ，因为这是允许它运行的指定内核补丁版本。

#### 安装 Docker - CentOS-6.5

在 CentOS-6.5 中，Docker 包含在 Extra Packages for Enterprise Linux (EPEL) 提供的镜像源中，该组织致力于为 RHEL 发行版创建和维护更多可用的软件包。

首先，你需要安装 EPEL 镜像源，请查看 EPEL installation instructions.

在 CentOS-6 中，一个系统自带的可执行的应用程序与 docker 包名字发生冲突，所以我们重新命名 docker 的RPM包名字为 docker-io 。

#### CentOS-6 中 安装 docker-io 之前需要先卸载 docker 包。

```bash
$ sudo yum -y remove docker
下一步，安装 docker-io 包来为我们的主机安装Docker。

$ sudo yum install docker-io
安装 - CentOS-7

Docker 软件包已经包含在默认的 CentOS-Extras 软件源里，安装命令如下：

使用yum从软件仓库安装Docker：

sudo yum install docker 
FirewallD

CentOS-7中介绍了firewalld，firewall的底层是使用iptables进行数据过滤，建立在iptables之上，这可能会与Docker产生冲突。

当firewalld 启动或者重启的时候，将会从 iptables 中移除 DOCKER 的规则，从而影响了 Docker 的正常工作。

当你使用的是 Systemd 的时候， firewalld 会在 Docker 之前启动，但是如果你在 Docker 启动之后再启动 或者重启 firewalld ，你就需要重启 Docker 进程了。

手动安装最新版本的 Docker

当你使用推荐方法来安装 Docker 的时候，上述的 Docker 包可能不是最新发行版本。 如果你想安装最新版本，你可以直接安装二进制包

当你使用二进制安装时，你可能想将 Docker 集成到 Systemd 的系统服务中。为了实现至一点，你需要从github中下载 service and 

socket两个文件，然后安装到 /etc/systemd/system 中。

首先启动Docker的守护进程：

sudo service docker start 
如果想要Docker在系统启动时运行，执行：

chkconfig docker on 
Docker在CentOS上好像和防火墙有冲突，应用防火墙规则后可能导致Docker无法联网，重启Docker可以解决。
```


这里不重启的话，会提示无法连接：


```bash
root@docker opt]# docker pull centos
Using default tag: latest
Trying to pull repository docker.io/library/centos ...
Pulling repository docker.io/library/centos
Tag latest not found in repository docker.io/library/centos
Tag latest not found in repository docker.io/library/centos
Ubuntu 系列安装 Docker
```
系统要求

Docker 支持以下版本的Ubuntu操作系统：

* Ubuntu Xenial 16.04 (LTS)
* Ubuntu Wily 15.10
* Ubuntu Trusty 14.04 (LTS)
* Ubuntu Precise 12.04 (LTS)
预安装

Docker 目前只能安装在 64 位平台上，并且要求内核版本不低于 3.10，实际上内核

越新越好，过低的内核版本容易造成功能的不稳定。

用户可以通过如下命令检查自己的内核版本详细信。

```bash
$ uname -a
Linux Host 3.16.0-43-generic #58~14.04.1-Ubuntu SMP Mon Jun 22 1
0:21:20 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
或者

$ cat /proc/version
Linux version 3.16.0-43-generic (buildd@brownie) (gcc version 4.
8.2 (Ubuntu 4.8.2-19ubuntu1) ) #58~14.04.1-Ubuntu SMP Mon Jun 22
10:21:20 UTC 2015
Docker 目前支持的最低 Ubuntu 版本为 12.04 LTS，但实际上从稳定性上考虑，推
```
荐至少使用 14.04 LTS 版本。

更新APT镜像源

首先需要安装 apt-transport-https 包支持 https 协议的源。

```bash
$ sudo apt-get install apt-transport-https ca-certificates
添加源的 gpg 密钥。

$ sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net
:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
获取当前操作系统的代号。

$ lsb_release -c
Codename: trusty
一般的，12.04 (LTS) 代号为 precise，14.04 (LTS) 代号为 trusty，15.04 代号为

vivid，15.10 代号为 wily，16.04 代号为Xenial 。这里获取到代号为 trusty。

接下来就可以添加 Docker 的官方 apt 软件源了。通过下面命令创建

/etc/apt/sources.list.d/docker.list 文件，并写入源的地址内容。非

trusty 版本的系统注意修改为自己对应的代号。

$ sudo cat <<EOF > /etc/apt/sources.list.d/docker.list
deb https://apt.dockerproject.org/repo ubuntu-trusty main
EOF
```
添加成功后，更新 apt 软件包缓存。

$ sudo apt-get update
分版本的预安装任务

```bash
高于 12.04 LTS的版本
Ubuntu Xenial 16.04 (LTS)
Ubuntu Wily 15.10
Ubuntu Trusty 14.04 (LTS)
1. 为了让 Docker 使用 aufs 存储，推荐安装 linux-image-extra 软件包。
$ sudo apt-get install -y linux-image-extra-$(uname -r)
1. 在 Ubuntu 14.04 或者 12.04上安装Docker，需要安装 apparmor （apparmor 是Linux内核的一个安全模块，新版本的Ubuntu已经被整合到内核）:
$ sudo apt-get install apparmor
12.04 LTS版本
```
如果使用 12.04 LTS 版本，首先要更新系统内核和安装可能需要的软件包，包括

* linux-image-generic-lts-trusty （必备）
* linux-headers-generic-lts-trusty （必备）
* xserver-xorg-lts-trusty （带图形界面时必备）
* libgl1-mesa-glx-lts-trusty（带图形界面时必备） 安装命令(根据环境和要求不同，选择安装上述软件包)，如：
$ sudo apt-get install linux-image-generic-lts-trusty
当然，12.04 LTS 还要根据需要安装 linux-image-extra 和 apparmor 软件

包。

注：Ubuntu 发行版中，LTS （Long-Term-Support）意味着更稳定的功能和更长期

（目前为 5 年）的升级支持，生产环境中尽量使用 LTS 版本。

安装 Docker

在成功添加源之后，就可以安装最新版本的 Docker 了，软件包名称为 dockerengine。

$ sudo apt-get install -y docker-engine
如果系统中存在旧版本的 Docker （lxc-docker），会提示是否先删除，选择是即

可。

其他可选配置
参见 docker官方配置文档
基本信息查看
这里我是用centos 7.1 安装做学习的
docker version：查看docker的版本号，包括客户端、服务端、依赖的Go等

```
[root@docker opt]# docker version
Client:
 Version:         1.10.3
 API version:     1.22
 Package version: docker-common-1.10.3-44.el7.centos.x86_64
 Go version:      go1.4.2
 Git commit:      9419b24-unsupported
 Built:           Fri Jun 24 12:09:49 2016
 OS/Arch:         linux/amd64

Server:
 Version:         1.10.3
 API version:     1.22
 Package version: docker-common-1.10.3-44.el7.centos.x86_64
 Go version:      go1.4.2
 Git commit:      9419b24-unsupported
 Built:           Fri Jun 24 12:09:49 2016
 OS/Arch:         linux/amd64    
Docker 帮助说明

[root@docker opt]# docker -help
Usage: docker [OPTIONS] COMMAND [arg...]
       docker daemon [ --help | ... ]
       docker [ --help | -v | --version ]

A self-sufficient runtime for containers.

Options:

  --config=~/.docker              Location of client config files
  -D, --debug                     Enable debug mode
  -H, --host=[]                   Daemon socket(s) to connect to
  -h, --help                      Print usage
  -l, --log-level=info            Set the logging level
  --tls                           Use TLS; implied by --tlsverify
  --tlscacert=~/.docker/ca.pem    Trust certs signed only by this CA
  --tlscert=~/.docker/cert.pem    Path to TLS certificate file
  --tlskey=~/.docker/key.pem      Path to TLS key file
  --tlsverify                     Use TLS and verify the remote
  -v, --version                   Print version information and quit
```
### Docker仓库

```bash
Docker使用类似git的方式管理镜像。通过基本的镜像可以定制创建出来不同种应用的Docker镜像。Docker Hub是Docker官方提供的镜像中心。在这里可以很方便地找到各类应用、环境的镜像。

由于Docker使用联合文件系统，所以镜像就像是夹心饼干一样一层层构成，相同底层的镜像可以共享。所以Docker还是相当节约磁盘空间的。要使用一个镜像，需要先从远程的镜像注册中心拉取，这点非常类似git。

现在，我们来验证 Docker 是否正常工作。第一步，我们需要下载最新的 centos 镜像。 

$ docker pull centos 
我们很容易就能从Docker Hub镜像注册中心下载一个最新版本的centos镜像到本地。国内网络可能会稍慢，DAOCLOUD提供了Docker Hub的国内加速服务，可以尝试配置使用。

下一步，我们运行下边的命令来查看镜像，确认镜像是否存在：

$ sudo docker images centos
这将会输出如下的信息：

 [root@docker opt]# docker images centos
 REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
 docker.io/centos    latest              05188b417f30        10 days ago         196.8 MB
运行一个容器

使用Docker最关键的一步就是从镜像创建容器。有两种方式可以创建一个容器：使用docker create命令创建容器，或者使用docker run命令运行一个新容器。

两个命令并没有太大差别，只是前者创建后并不会立即启动容器。

以centos为例，我们启动一个新容器，并将centos的Shell作为入口：

运行简单的脚本来测试镜像：

 $ sudo docker run -i -t centos /bin/bash
这时候我们成功创建了一个centos的容器，并将当前终端连接为这个centos的bash shell。这时候就可以愉快地使用centos的相关命令了~可以快速体验一下。

参数 -i表示这是一个交互容器，会把当前标准输入重定向到容器的标准输入中，而不是终止程序运行。-t指为这个容器分配一个终端。

如果正常运行，你将会获得一个简单的 bash 提示，输入 exit 来退出 或者按Ctrl+D可以退出这个容器了。

在容器运行期间，我们可以通过docker ps命令看到所有当前正在运行的容器。添加-a参数可以看到所有创建的容器：

$ docker ps -a 
这将会输出如下的信息：

[root@docker opt]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                            PORTS               NAMES
28e833d4a45f        centos              "/bin/bash"         2 minutes ago       Exited (130) About a minute ago                       sad_shaw
这里显示的一些东西:

* CONTAINER ID: 容器的ID
* IMAGE: 容器所使用的镜像
* COMMAND: 建立容器时候使用的命令
* CREATED: 创建时间
* STATUS: 当前状态
* PORTS: 端口映射(默认为无)
* NAMES: 容器的名字
输出信息详解：

每个容器都有一个唯一的ID标识，通过ID可以对这个容器进行管理和操作。在创建容器时，我们可以通过–name参数指定一个容器名称，如果没有指定系统将会分配一个，就像这里的“sad_shaw”（什么鬼）。

当我们按Ctrl+D退出容器时，命令执行完了，所以容器也就退出了。要重新启动这个容器，可以使用docker start命令：

$ docker start -i sad_shaw
同样，-i参数表示需要交互式支持。
```
注意：每次执行docker run命令都会创建新的容器，建议一次创建后，使用docker start/stop来启动和停用容器。

卸载 Docker

```
卸载软件包

$ sudo apt-get purge docker-engine
卸载依赖包

sudo apt-get autoremove --purge docker-engine
如有必要，执行以下命令，删除全部镜像、容器、数据卷和其他docker相关用户信息

$ rm -rf /var/lib/docker

```

