
# Docker学习笔记(九)如何将docker镜像上传到docker.hub仓库


第一步:先注册个docker账号吧.反正总要用到的.

[点击这里注册账号](https://hub.docker.com/)

注册一个账号，例如yangc 我user ,并创建仓库.

![](http://7xrthw.com1.z0.glb.clouddn.com/docker.hub1.png)

### 提交/保存镜像

创建好的镜像，可以保存到索引仓库中，便于下次使用（当然，我们直接共享Dockerfile，是最简单的事情，:)) ），但毕竟镜像可以做到开箱即用。
 

记住`username，password，email `.后面在命令行验证登陆的时候需要用到，再下来就是创建仓库了，本文假定你的英语还凑合可以看得懂英文：`create --->  create repository` ,取个名字，这里我们最终创建的仓库名称：rual/ljw ，这个rual 是我的帐号，ljw是其中一个仓库名。
 

1.构建镜像

```bash
docker build -t="yangc/tomcat8" .

-t： 为构建的镜像制定一个标签，便于记忆/索引等

. ： 指定Dockerfile文件在当前目录下


网速不太好，会等待很长时间。很多操作可能需要科学上网，逼得我只能一直挂着VPN，方能畅通无阻。

上面已经构建OK的话，可省略此步。


#这里也可以导出某个容器。不是按脚本去跑的。这里自己配置好的容器。导出

docker export 容器name  > tomcat8.tar

#导入

cat tomcat8.tar | docker import - yangc/tomcat8
```

2.docker hub 帐号在本地验证登陆

```bash
[root@docker ~]# docker login
Username: yangc
Password:
Email: yangcvo@gmail.com
WARNING: login credentials saved in /root/.docker/config.json
Login Succeeded
```

3 docker push 镜像到docker hub 的仓库

```bash
docker push

提交到Docker索引仓库

docker push yangc/tomcat8

上传OK的话，可以得到类似地址：https://index.docker.io/u/yangc/tomcat8/

4. 如何使用镜像

docker pull yangc/tomcat8

```
剩下的步骤，就很简单了。不需要一一介绍。



