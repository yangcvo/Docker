### Docker容器时间同步修改docker时区

前几天遇到这样一个业务场景，数据库运行在Docker 中，docker 的市区是utc 所以就跟北京时间相差8个小时。但是又不能重新运行一个容器，只能保证数据库运行状态，并把宿主机的时区复制给docker 容器。很苦恼.
首先我先把宿主机的时区改成啦CST 北京时间。然后把宿主机的时区复制给docker 容器。命令如下:

<!-- more -->

    docker cp /etc/localtime:【容器ID或者NAME】/etc/localtime

当然也可以进入容器进行修改时区（不过我的容器修改的时候总是报`/etc/localtime` 文件只读，不让修改。所以就用了上面的方法），命令如下:
首先添加所有的时区 
然后再修改时区

```
apk add tzdata 
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime 
echo "Asia/Shanghai" > /etc/timezone
```

当然，在容器内改，也很麻烦，每次启动新的容器那么就要修改，所以在dockerfile 中修改更好啦。
命令如下

```
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
RUN     /bin/echo -e "ZONE="Asia/Shanghai"\nUTC=false\nRTC=false" > /etc/sysconfig/clock

```

##### 查看时间是否正确
 
     docker exec -t 容器ID date


## 问题：

问题1： Docker中mysql时间相差八小时，java log的时间是不对的，尝试过在DockerFile中添加:

    RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai  /etc/localtime
    RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

参考：[Docker 运行的容器时间不对，怎么解决!](http://dockone.io/question/362)

    ENV TZ=Asia/Shanghai
    RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime &&   echo $TZ > /etc/timezone

参考：[Docker容器时间同步问题](http://dockone.io/question/505)

修改完成以后发现还是有问题。

```
[root@fb2ae4dce587 logs]# hwclock
hwclock: Cannot access the Hardware Clock via any known method.
hwclock: Use the --debug option to see the details of our search for an access method.
```

我参考这篇文章做了修改。

[调整时间](http://linux.it.net.cn/e/distro/Debian/2015/1010/17757.html)

* 把时区设置加入到Dockerfile中

这里我修改：cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

```
# Ubuntu

RUN echo "Asia/shanghai" > /etc/timezone;

# CentOS

RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

