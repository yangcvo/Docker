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
 

