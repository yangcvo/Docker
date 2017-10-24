## Docker容器部署tomcat出现中文乱码

这里我先查看下实例：

```bash
[root@docker1 ~]# docker exec -it 41de9a0b6045 locale
LANG=
LC_CTYPE="POSIX"
LC_NUMERIC="POSIX"
LC_TIME="POSIX"
LC_COLLATE="POSIX"
LC_MONETARY="POSIX"
LC_MESSAGES="POSIX"
LC_PAPER="POSIX"
LC_NAME="POSIX"
LC_ADDRESS="POSIX"
LC_TELEPHONE="POSIX"
LC_MEASUREMENT="POSIX"
LC_IDENTIFICATION="POSIX"
LC_ALL=
```

docker默认为POSIX
在Dockerfile 里添加

```bash
    ENV         LANG         en_US.UTF-8
```

发现网上很多都是这个教程，那么现在给个实用的。

我这里是centos 7.1系统。

设置后，使用en_US.UTF-8
中文locale

```bash
[root@a9f82e7842c1 ~]# export LC_ALL=en_US.UTF-8
[root@a9f82e7842c1 ~]# locale
LANG=
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"
LC_ALL=en_US.UTF-8
```

上面是临时生效。永久生效写到/etc/profile

       vim /etc/profile
       export PATH USER LOGNAME MAIL HOSTNAME HISTSIZE HISTCONTROL LC_ALL
      export LC_ALL=en_US.UTF-8
      
### 查看语言是否变更

     docker exec -t 容器名 locale

就已经全部更新了。
乱码也就不会出现了。


这个是之前tomcat出现乱码的 日志截图一小部分：

```bash
20-Oct-2016 13:49:34.591 INFO [main] org.apache.catalina.startup.Catalina.start Server startup in 41115 ms
?????: 0
editSyncOldChat-?????????--???
?????: 0
editSyncOldChat-?????????--???
?????: 0
editSyncOldChat-?????????--???
?????: 0
editSyncOldChat-?????????--???
```


