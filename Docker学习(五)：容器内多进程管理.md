### Docker学习(五)：容器内多进程管理

## 目录
1、Supervisor介绍及原理
2、Supervisor配置与实例
3、参考文档

从Docker的设计初衷来看，它并不推崇在一个容器中运行多个进程，但在一些实际的场景，比如小规模应用、老业务容器化等，都可能需要在一个容器中，同时运行多个程序。

<!-- more -->

在非容器的环境下，同时运行多个进程是非常简单的，系统初始化的时候，都会启动一个init进程，其余的进程都由它来管理。但在容器环境下就不一样了，因为它并没有init进程，所以大多数情况下，我们启动一个Docker容器，只让它运行一个前台程序，并且保证它不会退出，当容器检查到内部没有运行的前台进程之后，自己就会自动退出。


换句话说启动Docker容器时不能运行后台程序，诸如在CentOS里面，service xxx start，这种后台启动进程的方式都不可用，因为没init进程。

那么有办法解决这个问题吗？

目前主要有两个工具，一个是Supervisor，另一个是Monit。本篇先来介绍Supervisor，Monit会在之后的文章中再做详解。


一、Supervisor介绍及原理

Supervisor是一个进程管理工具，是客户端/服务端结构，通过它可以监控和控制其他的进程，同时它自身提供了一个WebUI，对其管理的应用进程，可以在WebUI进行start,stop,restart操作。由Supervisor管理的进程，都是它的子进程。

在Linux系统启动之后，第一个启动的用户态进程是/sbin/init ，它的PID是1，其余用户态的进程都是init进程的子进程。Supervisor在Docker容器里面充当的就类似init进程的角色，其它的应用进程都是Supervisor进程的子进程。通过这种方法就可以实现在一个容器中启动运行多个应用。



~Docker中的Supervisor

编辑Dockerfile

```bash
vim Dockerfile
FROM ubuntu:14.04
MAINTAINER debugo@sina.com
RUN apt-get update
RUN apt-get install -y openssh-server supervisor
RUN mkdir -p /var/run/sshd
RUN mkdir -p /var/log/supervisor
COPY supervisord.conf /etc/supervisor/supervisord.conf
EXPOSE 22 80
CMD ["/usr/bin/supervisord"]
```
其中，在当前目录新建一个supervisord.conf,由于是CMD执行，supervisord不用以daemon的形式启动。

```bash
[supervisord]
nodaemon=true
[program:sshd]
command=/usr/sbin/sshd -D
autorestart=unexpected
autostart=true
```

下面构建这个image

```
docker build -t supervisor-test .
Successfully built a97936f2da4a
```

运行测试：

```bash
docker run -it supervisor-test
2015-01-04 06:41:12,010 CRIT Supervisor running as root (no user in config file)
2015-01-04 06:41:12,016 INFO supervisord started with pid 1
2015-01-04 06:41:13,019 INFO spawned: 'sshd' with pid 8
2015-01-04 06:41:14,021 INFO success: sshd entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)
2015-01-04 06:41:55,268 WARN received SIGINT indicating exit request
2015-01-04 06:41:55,269 INFO waiting for sshd to die
2015-01-04 06:41:55,270 INFO stopped: sshd (exit status 0)
```


