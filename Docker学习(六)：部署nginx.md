
### Docker学习(六)：部署nginx

我14年就开始接触了docker，因为那时候因为docker还不是很成熟，在测试环境上面使用也没有大大推出，经过这一两年docker容器也很受我们运维人员喜爱特别好的利器。
现在普遍都会在测试环境使用甚至在生产环境也用docker使用集群，之前搞过CDN云加速，环境部署也有在centos7.1最新系统环境部署。因为docker容器要求也比较高。
可以在我前面docker简介和docker安装都有提到这一点。
 
<!-- more -->
 
### 1、镜像环境说明
 
 1.1、镜像版本说明   
   操作系统:`Centos 6.5 64 位`
      docker 运行环境(CentOS Linux release 7.2.1511 (Core)  64 位 | docker)
 
 镜像版本 V1.0 软件明细: ` docker-1.10.3` 

1.2、镜像安装说明
1.2.1、镜像环境里相应软件的安装,通过脚本实现安装。在/root 下面的 install.sh1.2.2、在镜像环境中,/root/install.sh 是安装镜像环境的脚本,您可以在 Centos 
6.5 64 位系统中自行采用此脚本安装,安装后的环境跟镜像里初始化的环境一致。值 得注意的是,如果采用此脚本安装镜像环境,需要 `chmod 777 -R install.sh` 赋予 777 安装权限。1.2.3、在镜像环境中,/root 是安装环境的主目录,镜像中的环境是在此目录下安装 的。

### 2、软件目录及配置列表

这里我yum安装的所以目录是默认的：

配置目录 : 
  
```bash
 /etc/sysconfig/docker
 /etc/sysconfig/docker-storage 
 /etc/sysconfig/docker-network
```

### 3、软件操作命令汇总

```bash
这里的命令其实跟我们平常linux下面命令差不多：
service docker status  查看docker现在运行的状态
service docker start   启动docker
chkconfig docker on    设置开机自启动docker
注意：正在将请求转发到“systemctl enable docker.service”。
```

### 4、关于卸载
如何卸载镜像环境中安装的软件,可以参考如下命令完成卸载:

    yum remove docker-io
    

讲完现在基本的开始部署nginx.

 1.镜像的列出: `docker images `
 
 ![](http://7xrthw.com1.z0.glb.clouddn.com/docker-nginx1.png)
 
 2.镜像搜索: 命令：`docker search`
  这里可以搜索出nginx的镜像:
 
 ![](http://7xrthw.com1.z0.glb.clouddn.com/docker-nginx2.png)
  
 3.镜像获取从 dockerhub 上面获取 nginx 的镜像这里
 
 这里已经获取到容器，默认的就已经安装好nginx。
 
  ![](http://7xrthw.com1.z0.glb.clouddn.com/docker-nginx3.png)

 4.启动容器  
  启动 nginx 容器，这里启动命令之前也有提到过：`docker run` 
  
 ![](http://7xrthw.com1.z0.glb.clouddn.com/docker-nginx4.png)

 5.查看容器状态: `docker ps`
   ![](http://7xrthw.com1.z0.glb.clouddn.com/docker-nginx5.png)

 6.结束容器: `docker kill` 
 将 ps 列出的 container id 传入 docker kill 中即可:
 
 ![](http://7xrthw.com1.z0.glb.clouddn.com/docker-nginx6.png)

 这里有人说这里启动以后端口如何映射到本地端口。
 
 7.容器端口映射
 可以将容器的端口映射到主机上,比如 80-> 80 端口,这样可以直接被外网访问到:
 
      docker run -itd -p 80:80 nginx
      
 ![](http://7xrthw.com1.z0.glb.clouddn.com/docker-nginx7.png)


<div class="tip">
这里还有点需要注意：就是如果出现docker端口映射错误

COMMAND_FAILED: '/sbin/iptables -t nat -A DOCKER -p tcp -d 0/0 --dport 8111 -j DNAT --to-destination 172.17.0.6:8111 ! -i docker0' failed: iptables: No chain/target/match by that name.
</div>

解决方法：

```bash
pkill docker 关闭docker
iptables -t nat -F 清除防火墙规则-如果没有写可以不用。
ifconfig docker0 down  docker 网卡关闭
brctl delbr docker0
重启docker后解决
```


 8.此时访问服务器的 ip,即可看到 nginx 的页面。
 
 具体配置就不多说了。上面也说清楚，就是你要跑什么服务，配置可以用脚本docker后台运行。
 
  ![](http://7xrthw.com1.z0.glb.clouddn.com/docker-nginx8.png)


这里我写了几篇入门篇：

Docker应用: [docker应用](http://blog.yangcvo.me/2016/07/12/Docker应用/)
Docker安装篇: [Docker安装篇](http://blog.yangcvo.me/2016/07/12/Docker安装篇/)


