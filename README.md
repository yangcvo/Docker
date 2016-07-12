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
   


## Docker 简介

[Docker 中文指南简介](http://blog.yangcvo.me/2016/07/12/Docker简介/)


## docker 入门安装篇

可参考：[所有系统安装docker文章](http://docker.widuu.com/installation/mac.html)


## 其他信息

blog地址：https://blog.yangcvo.me

新浪微博：http://weibo.com/yangcvo


