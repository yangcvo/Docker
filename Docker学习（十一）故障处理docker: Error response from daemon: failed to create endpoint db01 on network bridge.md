### Docker学习（十一）docker不能访问外网和报错docker/ Error response from daemon/ failed to create endpoint db01 on network bridge

问题来源：

### Docker不能访问外网


开始是因为所有容器出现不能访问外网。只能访问宿主机的IP。可以ping通。这边我监控到服务全部挂了，还好是在测试环境。

早上还是好好的、后面一个同事把宿主机防火墙刷新了。之前生产这些容器映射好的端口都是临时的。没有直接写到iptables.导致重启iptables 丢失了所有的容器的规则。访问外网都失败了。

<!-- more -->

这个原因后面写好了规则重启防火墙就可以了。

■ 访问不了外网前宿主机192.168.1.183 防火墙：


```bash
[root@docker1 files]# iptables -nvL
Chain INPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination
 6942  836K ACCEPT     all  --  *      *       0.0.0.0/0            0.0.0.0/0            state RELATED,ESTABLISHED
    0     0 ACCEPT     icmp --  *      *       0.0.0.0/0            0.0.0.0/0
    0     0 ACCEPT     all  --  lo     *       0.0.0.0/0            0.0.0.0/0
    0     0 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            state NEW tcp dpt:22
   52  3312 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            reject-with icmp-host-prohibited

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
reject-with icmp-host-prohibited

Chain OUTPUT (policy ACCEPT 857 packets, 256K bytes)
 pkts bytes target     prot opt in     out     source               destination

Chain DOCKER (1 references)
 pkts bytes target     prot opt in     out     source               destination

Chain DOCKER-ISOLATION (1 references)
 pkts bytes target     prot opt in     out     source               destination
    3   156 RETURN     all  --  *      *       0.0.0.0/0            0.0.0.0/0
```

■ 第一时间把容器端口映射规则写在iptables配置文件里面重启防火墙好了。

```
[root@docker1 files]# iptables -nvL
Chain INPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination
13578 2796K ACCEPT     all  --  *      *       0.0.0.0/0            0.0.0.0/0            state RELATED,ESTABLISHED
    0     0 ACCEPT     icmp --  *      *       0.0.0.0/0            0.0.0.0/0
    0     0 ACCEPT     all  --  lo     *       0.0.0.0/0            0.0.0.0/0
    0     0 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            state NEW tcp dpt:22
  155  9904 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            reject-with icmp-host-prohibited

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination
   25  2267 DOCKER-ISOLATION  all  --  *      *       0.0.0.0/0            0.0.0.0/0
   10  1232 DOCKER     all  --  *      docker0  0.0.0.0/0            0.0.0.0/0
   10  1232 ACCEPT     all  --  *      docker0  0.0.0.0/0            0.0.0.0/0            ctstate RELATED,ESTABLISHED
   15  1035 ACCEPT     all  --  docker0 !docker0  0.0.0.0/0            0.0.0.0/0
    0     0 ACCEPT     all  --  docker0 docker0  0.0.0.0/0            0.0.0.0/0
 1344 80600 REJECT     all  --  *      *       0.0.0.0/0            0.0.0.0/0            reject-with icmp-host-prohibited

Chain OUTPUT (policy ACCEPT 15 packets, 5460 bytes)
 pkts bytes target     prot opt in     out     source               destination

Chain DOCKER (1 references)
 pkts bytes target     prot opt in     out     source               destination
    0     0 ACCEPT     tcp  --  !docker0 docker0  0.0.0.0/0            172.17.0.2           tcp dpt:8080
    0     0 ACCEPT     tcp  --  !docker0 docker0  0.0.0.0/0            172.17.0.3           tcp dpt:8080
    0     0 ACCEPT     tcp  --  !docker0 docker0  0.0.0.0/0            172.17.0.4           tcp dpt:8080
    0     0 ACCEPT     tcp  --  !docker0 docker0  0.0.0.0/0            172.17.0.5           tcp dpt:8080
    0     0 ACCEPT     tcp  --  !docker0 docker0  0.0.0.0/0            172.17.0.6           tcp dpt:8080
    0     0 ACCEPT     tcp  --  !docker0 docker0  0.0.0.0/0            172.17.0.7           tcp dpt:8080
    0     0 ACCEPT     tcp  --  !docker0 docker0  0.0.0.0/0            172.17.0.8           tcp dpt:8080
    0     0 ACCEPT     tcp  --  !docker0 docker0  0.0.0.0/0            172.17.0.9           tcp dpt:8080
    0     0 ACCEPT     tcp  --  !docker0 docker0  0.0.0.0/0            172.17.0.10          tcp dpt:8080

Chain DOCKER-ISOLATION (1 references)
 pkts bytes target     prot opt in     out     source               destination
   25  2267 RETURN     all  --  *      *       0.0.0.0/0            0.0.0.0/0
```

已经可以访问外网了。问题是防火墙的问题。
还有就是容器的IP需要设置静态 这是前提。没有设置也有可能导致访问外网失败。



###故障处理docker/ Error response from daemon/ failed to create endpoint db01 on network bridge


出现访问不了外网还可以这样。

```
[root@docker1 ~]# docker run -itd -p 12001:8080 tomcat-account/centos /bin/bash
fd4be15583f2081b4b28aa913fee1f6b0357ce61786a782e13dc30e27edd7743
docker: Error response from daemon: failed to create endpoint sleepy_wing on network bridge: iptables failed: iptables --wait -t nat -A DOCKER -p tcp -d 0/0 --dport 12001 -j DNAT --to-destination 172.17.0.6:8080 ! -i docker0: iptables: No chain/target/match by that name.
```
这里我从新做了个容器。发现映射之前一模一样的端口就报错 网卡映射失败。

■ 报错内容：

```
docker: Error response from daemon: failed to create endpoint db01 on network bridge: COMMAND_FAILED: '/sbin/iptables -t nat -A DOCKER -p tcp -d 0/0 --dport 3306 -j DNAT --to-destination 172.17.0.2:3306 ! -i docker0' failed: iptables: No chain/target/match by that name..

```
■ 解決方法

```
# mv /var/lib/docker/network/files /tmp/docker-iptables-err
# systemctl restart docker
```
备份之前的容器的网卡信息。重启docker 

/var/lib/docker/network/files 这个主要问题是存了之前通信信息。

然后重启了docker 以后。重启之前的跑服务的容器。
会自动帮忙映射好之前设置的，防火墙规则也会临时暂时有效了。


```bash
[root@docker1 files]# docker ps -a
CONTAINER ID        IMAGE                   COMMAND             CREATED             STATUS                    PORTS                     NAMES
46979ea00dc6        tomcat-system/centos    "/bin/bash"         2 days ago          Up 2 minutes              0.0.0.0:10007->8080/tcp   evil_knuth
fb2ae4dce587        tomcat-account/centos   "/bin/bash"         2 days ago          Up 2 minutes              0.0.0.0:10001->8080/tcp   angry_goldberg
8b2ac488e454        tomcat-point/centos     "/bin/bash"         2 days ago          Up 19 minutes             0.0.0.0:10006->8080/tcp   focused_hypatia
```

在重新生成容器就不会了。


```
[root@docker1 ~]# docker run -itd -p 12001:8080 tomcat-account/centos /bin/bash
fd4be15583f2081b4b28aa913fee1f6b0357ce61786a782e13dc30e27edd7743
```

可参考链接：[docker/ Error response from daemon/ failed to create endpoint db01 on network bridge](http://qiita.com/witamo/items/9770a2a757d3f5e369a4)

