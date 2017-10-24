
# Docker学习(十二) SSH远程登陆docker容器


### 环境：

```bash
CentOS release 6.8 (Final)
```

### 任务：

```bash
实现宿主机使用ssh登陆docker容器。方便后期使用Jenkins访问docker.
```

## 1、用户密码认证方式登陆


//第一步：运行容器，指定IP，这里的示例容器开启的SSH服务，后面拿它测试

//d第二步：进入容器。编辑ssh_config配置文件。

```bash
[root@docker1 ~]# docker run -itd -p 192.168.1.183:9999:22 yangc/centos6.8  /bin/bash
a3026d8da25d40214c470fc6d12638476c72774d70127a73069498c1397aac62
[root@docker1 ~]# docker exec -it a3026d8da25d /bin/bash
[root@a3026d8da25d ~]# vim /etc/ssh/ssh_config
```

编辑ssh_config配置文件。

```bash
sshd_config 需要关注三个地方，未修改之前是这样：
PermitRootLogin without-password
#AuthorizedKeysFile	%h/.ssh/authorized_keys
#PasswordAuthentication yes
说明：
#PermitRootLogin yes #允许root用户以任何认证方式登录（用户名密码认证和公钥认证）
#PermitRootLogin without-password #只允许root用公钥认证方式登录
#PermitRootLogin no #不允许root用户以任何认证方式登录

这里先修改两处：
PermitRootLogin without-password 改为 PermitRootLogin yes
#PasswordAuthentication yes 改为 PasswordAuthentication yes
```

编辑完重启服务。

```bash
[root@a3026d8da25d ~]# service sshd restart
Stopping sshd:                                             [  OK  ]
Starting sshd:                                             [  OK  ]
```

**测试连接报错3个问题**

* 1.这个是没有启动容器的sshd服务。

Docker容器使用静态独立的外部IP（便于集群组建）

```bash
[root@docker1 ~]# ssh root@192.168.1.183 -p9999
ssh: connect to host 192.168.1.183 port 9999: Connection refused
```
这个是没有启动容器的sshd服务。


* 2.测试连接，在Docker宿主机上SSH到第一个容器

ssh连接报错：Permission denied (publickey,gssapi-keyex,gssapi-with-mic).

```bash
[root@docker1 ~]# ssh root@192.168.1.183 -p9999
Permission denied (publickey,gssapi-keyex,gssapi-with-mic).
```
报了这个错，原因是修改了/etc/ssh/sshd_config 中的"PasswordAuthentication"参数值为"no",修改回"yes"，重启sshd服务即可。


*  3.修改root密码报错。

    /usr/share/cracklib/pw_dict.pwd: No such file or directory

```bash
[root@a3026d8da25d ~]# passwd root
Changing password for user root.
New password:
/usr/share/cracklib/pw_dict.pwd: No such file or directory
PWOpen: No such file or directory
```

这是因为缺少某些lib库。解决办法：

    yum reinstall -y cracklib-dicts



在重新试下：

```bash
[root@a3026d8da25d ~]# passwd root
Changing password for user root.
New password:
Retype new password:
passwd: all authentication tokens updated successfully.
```

 ifconfig #获得docker的内网地址（inet addr）：172.17.0.2

两种连接方式：直接连接docker 内网地址，或者直接连接映射宿主机的地址加端口。

 因为做了端口映射，所以可以直接从映射的端口登陆，只需要知道和docker的22端口映射的宿主机端口和宿主机的ip（如果和docker的22做端口映射时候采用默认IP方式，则默认宿主机的所有IP都和docker的22端口映射，这样localhost和子网IP均可等登陆）

```bash
[root@docker1 ~]# ssh root@172.17.0.13
The authenticity of host '172.17.0.13 (172.17.0.13)' can't be established.
RSA key fingerprint is 8d:98:c5:71:11:b9:5f:47:f5:f6:25:7f:9c:53:91:9c.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '172.17.0.13' (RSA) to the list of known hosts.
root@172.17.0.13's password:
[root@a3026d8da25d ~]#

[root@docker1 ~]# ssh root@192.168.1.183 -p9999
root@192.168.1.183's password:
Last login: Wed Oct 12 07:46:59 2016 from 172.17.0.1
[root@a3026d8da25d ~]#
```

##  2、公钥认证方式登陆



前面第一步份不变。
只需要第二步做下修改：进入容器。编辑ssh_config配置文件。

```bash
把第一步中提到的需要注意的三个地方做以下修改：
PermitRootLogin without-password
#AuthorizedKeysFile	%h/.ssh/authorized_keys改为

AuthorizedKeysFile	%h/.ssh/authorized_keys

#PasswordAuthentication yes改为PasswordAuthentication yes

（如果服务器不在本地，千万不能PasswordAuthentication yes->no，
万一当前的ssh链接中断，万一RAS认证没弄好，密码验证又禁止了。
可以理解为公钥认证优先于用户密码认证，但是万一公钥认证失败，用用户密码认证以防万一）
```
 
```bash
 [root@docker1 ~]#  ssh-keygen -t rsa
#一直回车，生成宿主机的密钥
[root@docker1 ~]# ssh-copy-id root@172.17.0.13
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
root@172.17.0.13's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'root@172.17.0.13'"
and check to make sure that only the key(s) you wanted were added.
```
测试登陆

```bash
[root@docker1 ~]# ssh root@192.168.1.183 -p9999
Last login: Wed Oct 12 07:57:47 2016 from 192.168.1.183
[root@a3026d8da25d ~]#
```

********可以替换上面的通过scp方法把公钥传送到docker*********
   #或者直接把宿主机的id_rsa.pub内容复制到docker的/root/.ssh/authorized_keys


课外资料：

[ubuntu环境ssh远程登录docker](http://www.cnblogs.com/hslzju/p/5839913.html)
[外部访问容器](http://www.kancloud.cn/thinkphp/docker_practice/30928)


