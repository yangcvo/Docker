## Docker学习(十三) SSH远程登陆docker容器


#### Docker虚拟容器ssh登录问题解决方法


虽然CentOS 6/7 的容器启动以后都没有安装sshd，我单独通过yum安装了服务并且启动，非常奇怪的是，其他客户端尝试连接都被reset连接:

    ssh root@192.168.1.3
    Read from socket failed: Connection reset by peer

使用debug方式连接容器:

    ssh -vvv 192.168.1.3
显示如下:

```bash
OpenSSH_6.0p1 Debian-4, OpenSSL 1.0.1e 11 Feb 2013
debug1: Reading configuration data /etc/ssh/ssh_config
debug1: /etc/ssh/ssh_config line 19: Applying options for *
debug2: ssh_connect: needpriv 0
debug1: Connecting to 192.168.1.3 [192.168.1.3] port 22.
debug1: Connection established.
debug1: permanently_set_uid: 0/0
debug1: identity file /root/.ssh/id_rsa type -1
debug1: identity file /root/.ssh/id_rsa-cert type -1
debug1: identity file /root/.ssh/id_dsa type -1
debug1: identity file /root/.ssh/id_dsa-cert type -1
debug1: identity file /root/.ssh/id_ecdsa type -1
debug1: identity file /root/.ssh/id_ecdsa-cert type -1
debug1: Remote protocol version 2.0, remote software version OpenSSH_5.3
debug1: match: OpenSSH_5.3 pat OpenSSH_5*
debug1: Enabling compatibility mode for protocol 2.0
debug1: Local version string SSH-2.0-OpenSSH_6.0p1 Debian-4
debug2: fd 3 setting O_NONBLOCK
debug3: load_hostkeys: loading entries for host "192.168.1.3" from file "/root/.ssh/known_hosts"
debug3: load_hostkeys: loaded 0 keys
debug1: SSH2_MSG_KEXINIT sent
Connection closed by 192.168.1.3
```
这个问题通常是由于服务器端的权限问题导致，例如 .ssh 目录属性没有设置成 700 等。
由于默认没有安装 `rsyslog` ，所以我单独安装了` rsyslog `以后，再重启了一次 `sshd `服务，然后使用客户端` ssh -vvv SERVERIP 则可以看到 /var/log/secure `日志中显示:


    Sep  8 10:36:39 e7674eb07c3d sshd[225]: fatal: chroot("/var/empty/sshd"): Operation not permitted

这个问题在 [connect via ssh to jhipster docker container on CentOS 7 ](http://stackoverflow.com/questions/25428669/connect-via-ssh-to-jhipster-docker-container-on-centos-7)提供了解决方法：


对于 centos 7 采用的是比较陈旧的版本（当前0.11.1-22.el7），所以是:

```bash
sed 's/UsePrivilegeSeparation yes/UsePrivilegeSeparation no/' -i /etc/ssh/sshd_config
/usr/sbin/sshd
```

不过，我发现这样的情况下，在 centos 7 容器中显示的ssh进程比较奇怪:

```bash
root       265     1  0 11:10 ?        00:00:00 /usr/sbin/sshd
root       267   265  0 11:10 ?        00:00:00 sshd: root@notty
```
客户端长时间等待，然后出现报错 `PTY allocation request failed on channel 0 `测试过密钥认证和密码认证都这个问题。
对于 centos 6 采用是比较新的版本，需要修改 `/etc/ssh/sshd_config` 的配置，取消掉 `UsePAM`

```bash
sed 's/UsePAM yes/UsePAM no/' -i /etc/ssh/sshd_config
```
已验证，上述方法用于 docker 1.2 版本可以解决centos 6容器的ssh登录问题

