# Linux Nginx

## Nginx简介

Nginx(engine x)是一个高性能的HTTP（解决C10k的问题）和反向代理服务器，也是一个IMAP/POP/SMTP服务器

**Nginx的web优势：**

+ 高并发
  + 可同时支持过万级流量
  + IO多路复用
  + 异步

## Nginx YUM安装

> Linux yum安装官网路径：http://nginx.org/en/linux_packages.html#RHEL

### 1.配置本地yum

1. 挂载磁盘
2. 配置yum文件

### 2.安装 yum-utils

```
sudo yum install yum-utils
```

### 3.编辑文件`/etc/yum.repos.d/nginx.repo`

```bash
[nginx-stable]
name=nginx stable repo												# 指定仓库名
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/		# 指定仓库路径
gpgcheck=1															# 是否需要开启签名校验
enabled=1															# 是否启用仓库
gpgkey=https://nginx.org/keys/nginx_signing.key						# 校验密钥URL
module_hotfixes=true												# 启用模块热修复，以便在安装或更新软件包时忽略模块流冲突

[nginx-mainline]
name=nginx mainline repo
baseurl=http://nginx.org/packages/mainline/centos/$releasever/$basearch/
gpgcheck=1
enabled=0
gpgkey=https://nginx.org/keys/nginx_signing.key
module_hotfixes=true
```

### 4.启用最新版yum

```
sudo yum-config-manager --enable nginx-mainline
```

### 5.安装Nginx

```
sudo yum install nginx
```

### 6.启动Nginx

```bash
[root@localhost ~]# systemctl start nginx
[root@localhost ~]# systemctl enable nginx
Created symlink /etc/systemd/system/multi-user.target.wants/nginx.service → /usr/lib/systemd/system/nginx.service.
```

### 7.防火墙放行

```bash
[root@localhost ~]# firewall-cmd --permanent --add-service=http
success
[root@localhost ~]# firewall-cmd --reload
success
```

## Nginx配置文件

### 1.查看Nginx配置文件

```bash
[root@localhost ~]# rpm -ql nginx
/etc/logrotate.d/nginx			# 日志轮转文件
/etc/nginx/nginx.conf			# 主配置文件		
/etc/nginx/conf.d				# 子配置文件
/etc/nginx/conf.d/default.conf	# 默认网站配置文件
/etc/nginx/fastcgi_params
/etc/nginx/mime.types			# 文件关联程序
/etc/nginx/modules				# nginx扩展，第三方模块
/var/log/nginx						# nginx日志文件夹
```

## Nginx编译参数

```bash
--prefix= 指向安装目录
--sbin-path 指向（执行）程序文件（nginx）
--conf-path= 指向配置文件（nginx.conf）
--error-log-path= 指向错误日志目录
--pid-path= 指向pid文件（nginx.pid）
--lock-path= 指向lock文件（nginx.lock）（安装文件锁定，防止安装文件被别人利用，或自己误操作。）
--user= 指定程序运行时的非特权用户
--group= 指定程序运行时的非特权用户组
--builddir= 指向编译目录
--with-rtsig_module 启用rtsig模块支持（实时信号）
--with-select_module 启用select模块支持（一种轮询模式,不推荐在高载环境下使用）禁用：--without-select_module
--with-poll_module 启用poll模块支持（功能与select相同，与select特性相同，为一种轮询模式,不推荐在高载环境下使用）
--with-file-aio 启用file aio支持（一种APL文件传输格式）
--with-ipv6 启用ipv6支持
--with-http_ssl_module 启用ngx_http_ssl_module支持（使支持https请求，需已安装openssl）
--with-http_realip_module 启用ngx_http_realip_module支持（这个模块允许从请求标头更改客户端的IP地址值，默认为关）
--with-http_addition_module 启用ngx_http_addition_module支持（作为一个输出过滤器，支持不完全缓冲，分部分响应请求）
--with-http_xslt_module 启用ngx_http_xslt_module支持（过滤转换XML请求）
--with-http_image_filter_module 启用ngx_http_image_filter_module支持（传输JPEG/GIF/PNG 图片的一个过滤器）（默认为不启用。gd库要用到）
--with-http_geoip_module 启用ngx_http_geoip_module支持（该模块创建基于与MaxMind GeoIP二进制文件相配的客户端IP地址的ngx_http_geoip_module变量）
--with-http_sub_module 启用ngx_http_sub_module支持（允许用一些其他文本替换nginx响应中的一些文本）
--with-http_dav_module 启用ngx_http_dav_module支持（增加PUT,DELETE,MKCOL：创建集合,COPY和MOVE方法）默认情况下为关闭，需编译开启
--with-http_flv_module 启用ngx_http_flv_module支持（提供寻求内存使用基于时间的偏移量文件）
--with-http_gzip_static_module 启用ngx_http_gzip_static_module支持（在线实时压缩输出数据流）
--with-http_random_index_module 启用ngx_http_random_index_module支持（从目录中随机挑选一个目录索引）
--with-http_secure_link_module 启用ngx_http_secure_link_module支持（计算和检查要求所需的安全链接网址）
--with-http_degradation_module  启用ngx_http_degradation_module支持（允许在内存不足的情况下返回204或444码）
--with-http_stub_status_module 启用ngx_http_stub_status_module支持（获取nginx自上次启动以来的工作状态）
--without-http_charset_module 禁用ngx_http_charset_module支持（重新编码web页面，但只能是一个方向--服务器端到客户端，并且只有一个字节的编码可以被重新编码）
--without-http_gzip_module 禁用ngx_http_gzip_module支持（该模块同-with-http_gzip_static_module功能一样）
--without-http_ssi_module 禁用ngx_http_ssi_module支持（该模块提供了一个在输入端处理处理服务器包含文件（SSI）的过滤器，目前支持SSI命令的列表是不完整的）
--without-http_userid_module 禁用ngx_http_userid_module支持（该模块用来处理用来确定客户端后续请求的cookies）
--without-http_access_module 禁用ngx_http_access_module支持（该模块提供了一个简单的基于主机的访问控制。允许/拒绝基于ip地址）
--without-http_auth_basic_module禁用ngx_http_auth_basic_module（该模块是可以使用用户名和密码基于http基本认证方法来保护你的站点或其部分内容）
--without-http_autoindex_module 禁用disable ngx_http_autoindex_module支持（该模块用于自动生成目录列表，只在ngx_http_index_module模块未找到索引文件时发出请求。）
--without-http_geo_module 禁用ngx_http_geo_module支持（创建一些变量，其值依赖于客户端的IP地址）
--without-http_map_module 禁用ngx_http_map_module支持（使用任意的键/值对设置配置变量）
--without-http_split_clients_module 禁用ngx_http_split_clients_module支持（该模块用来基于某些条件划分用户。条件如：ip地址、报头、cookies等等）
--without-http_referer_module 禁用disable ngx_http_referer_module支持（该模块用来过滤请求，拒绝报头中Referer值不正确的请求）
--without-http_rewrite_module 禁用ngx_http_rewrite_module支持（该模块允许使用正则表达式改变URI，并且根据变量来转向以及选择配置。如果在server级别设置该选项，那么他们将在 location之前生效。如果在location还有更进一步的重写规则，location部分的规则依然会被执行。如果这个URI重写是因为location部分的规则造成的，那么 location部分会再次被执行作为新的URI。 这个循环会执行10次，然后Nginx会返回一个500错误。）
--without-http_proxy_module 禁用ngx_http_proxy_module支持（有关代理服务器）
--without-http_fastcgi_module 禁用ngx_http_fastcgi_module支持（该模块允许Nginx 与FastCGI 进程交互，并通过传递参数来控制FastCGI 进程工作。 ）FastCGI一个常驻型的公共网关接口。
--without-http_uwsgi_module 禁用ngx_http_uwsgi_module支持（该模块用来医用uwsgi协议，uWSGI服务器相关）
--without-http_scgi_module 禁用ngx_http_scgi_module支持（该模块用来启用SCGI协议支持，SCGI协议是CGI协议的替代。它是一种应用程序与HTTP服务接口标准。它有些像FastCGI但他的设计 更容易实现。）
--without-http_memcached_module 禁用ngx_http_memcached_module支持（该模块用来提供简单的缓存，以提高系统效率）
-without-http_limit_zone_module 禁用ngx_http_limit_zone_module支持（该模块可以针对条件，进行会话的并发连接数控制）
--without-http_limit_req_module 禁用ngx_http_limit_req_module支持（该模块允许你对于一个地址进行请求数量的限制用一个给定的session或一个特定的事件）
--without-http_empty_gif_module 禁用ngx_http_empty_gif_module支持（该模块在内存中常驻了一个1*1的透明GIF图像，可以被非常快速的调用）
--without-http_browser_module 禁用ngx_http_browser_module支持（该模块用来创建依赖于请求报头的值。如果浏览器为modern ，则$modern_browser等于modern_browser_value指令分配的值；如 果浏览器为old，则$ancient_browser等于 ancient_browser_value指令分配的值；如果浏览器为 MSIE中的任意版本，则 $msie等于1）
--without-http_upstream_ip_hash_module 禁用ngx_http_upstream_ip_hash_module支持（该模块用于简单的负载均衡）
--with-http_perl_module 启用ngx_http_perl_module支持（该模块使nginx可以直接使用perl或通过ssi调用perl）
--with-perl_modules_path= 设定perl模块路径
--with-perl= 设定perl库文件路径
--http-log-path= 设定access log路径
--http-client-body-temp-path= 设定http客户端请求临时文件路径
--http-proxy-temp-path= 设定http代理临时文件路径
--http-fastcgi-temp-path= 设定http fastcgi临时文件路径
--http-uwsgi-temp-path= 设定http uwsgi临时文件路径
--http-scgi-temp-path= 设定http scgi临时文件路径
-without-http 禁用http server功能
--without-http-cache 禁用http cache功能
--with-mail 启用POP3/IMAP4/SMTP代理模块支持
--with-mail_ssl_module 启用ngx_mail_ssl_module支持
--without-mail_pop3_module 禁用pop3协议（POP3即邮局协议的第3个版本,它是规定个人计算机如何连接到互联网上的邮件服务器进行收发邮件的协议。是因特网电子邮件的第一个离线协议标 准,POP3协议允许用户从服务器上把邮件存储到本地主机上,同时根据客户端的操作删除或保存在邮件服务器上的邮件。POP3协议是TCP/IP协议族中的一员，主要用于 支持使用客户端远程管理在服务器上的电子邮件）
--without-mail_imap_module 禁用imap协议（一种邮件获取协议。它的主要作用是邮件客户端可以通过这种协议从邮件服务器上获取邮件的信息，下载邮件等。IMAP协议运行在TCP/IP协议之上， 使用的端口是143。它与POP3协议的主要区别是用户可以不用把所有的邮件全部下载，可以通过客户端直接对服务器上的邮件进行操作。）
--without-mail_smtp_module 禁用smtp协议（SMTP即简单邮件传输协议,它是一组用于由源地址到目的地址传送邮件的规则，由它来控制信件的中转方式。SMTP协议属于TCP/IP协议族，它帮助每台计算机在发送或中转信件时找到下一个目的地。）
--with-google_perftools_module 启用ngx_google_perftools_module支持（调试用，剖析程序性能瓶颈）
--with-cpp_test_module 启用ngx_cpp_test_module支持
--add-module= 启用外部模块支持
--with-cc= 指向C编译器路径
--with-cpp= 指向C预处理路径
--with-cc-opt= 设置C编译器参数（PCRE库，需要指定–with-cc-opt=”-I /usr/local/include”，如果使用select()函数则需要同时增加文件描述符数量，可以通过–with-cc- opt=”-D FD_SETSIZE=2048”指定。）
--with-ld-opt= 设置连接文件参数。（PCRE库，需要指定–with-ld-opt=”-L /usr/local/lib”。）
--with-cpu-opt= 指定编译的CPU，可用的值为: pentium, pentiumpro, pentium3, pentium4, athlon, opteron, amd64, sparc32, sparc64, ppc64
--without-pcre 禁用pcre库
--with-pcre 启用pcre库
--with-pcre= 指向pcre库文件目录
--with-pcre-opt= 在编译时为pcre库设置附加参数
--with-md5= 指向md5库文件目录（消息摘要算法第五版，用以提供消息的完整性保护）
--with-md5-opt= 在编译时为md5库设置附加参数
--with-md5-asm 使用md5汇编源
--with-sha1= 指向sha1库目录（数字签名算法，主要用于数字签名）
--with-sha1-opt= 在编译时为sha1库设置附加参数
--with-sha1-asm 使用sha1汇编源
--with-zlib= 指向zlib库目录
--with-zlib-opt= 在编译时为zlib设置附加参数
--with-zlib-asm= 为指定的CPU使用zlib汇编源进行优化，CPU类型为pentium, pentiumpro
--with-libatomic 为原子内存的更新操作的实现提供一个架构
--with-libatomic= 指向libatomic_ops安装目录
--with-openssl= 指向openssl安装目录
--with-openssl-opt 在编译时为openssl设置附加参数
--with-debug 启用debug日志
```

## Nginx基本配置

### 主配置文件

```bash
[root@localhost nginx]# vim /etc/nginx/nginx.conf
# 核心驱动模块
user  nginx;												# 服务对应用户
worker_processes  auto;										# 启动进程数

error_log  /var/log/nginx/error.log notice;					# 错误文件存放处，以notice格式写入
pid        /var/run/nginx.pid;								# 程序的PID值存放处

# 事件驱动模块
events {
    worker_connections  1024;								# 最大连接数1024
}

# http模块
http {
    include       /etc/nginx/mime.types;					# 包含关联程序文件
    default_type  application/octet-stream;
    		# 应用程序流，默认字节流处理方式

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '		# 日志格式定义
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;									# 访问日志文件，以main格式写入

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}

```

### 默认网站配置文件

```bash
server {
    listen       80;				# 监听端口号
    server_name  localhost;			# 网站域名

    #access_log  /var/log/nginx/host.access.log  main;		# 如果这里注释，那么所有网站日志都会放在 /var/log/nginx/access.log 中

    location / {
        root   /usr/share/nginx/html;		# 站点根目录
        index  index.html index.htm;		# 匹配we
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}
```

## 实战1：Nginx搭建虚拟主机-基于端口

### 1、创建站点子配置文件

```bash
[root@localhost /]# vim /etc/nginx/conf.d/tangzai.conf
server{
        listen 8080;					# 修改监听端口为8080
        server_name tangzai.io;			# 修改域名
    location / {
            root /tangzai;				# 网站根目录
            index index.html;			# 索引文件匹配
    }
}
```

### 2、创建文件夹于网站

```bash
[root@localhost /]# mkdir /tangzai
[root@localhost /]# echo "tangzai.github.io" > index.html
```

### 3、防火墙放行端口

```bash
[root@localhost nginx]# firewall-cmd --permanent --add-port=8080/tcp
success
[root@localhost nginx]# firewall-cmd --reload
success
```

### 4、SELINUX关联上下文

```bash
[root@localhost /]# chcon -v -t httpd_sys_content_t tangzai/index.html
正在更改'tangzai/index.html' 的安全环境
[root@localhost /]# ls -Z tangzai/index.html
unconfined_u:object_r:httpd_sys_content_t:s0 tangzai/index.html
```

## Nginx 日志

### Nginx 访问日志

日志文件路径：`/var/log/ngix/access.log`

Nginx访问日志格式设置在Nginx主配置文件中

```bash
http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

	# /var/log/nginx/access.log 文件会以下面格式记录
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}
```

+ `remote_addr`：客户端IP地址
+ `remote_user`：客户端用户名
+ `time_locat`：访问时间
+ `request`：HTTP 请求方法
+ `ststus`：HTTP 状态码
+ `body_bytes_sent`：HTTP响应内容长度
+ `http_referer`：客户端来源URL
+ `http_user_agent`：客户端User-Agent
+ `http_x_forwarded_for`：客户端代理地址记录（记录客户端经过哪些代理服务器）

日志文件示例：

```bash
[root@localhost /]# tail /var/log/nginx/access.log
192.168.231.1 - - [16/Jun/2023:07:49:52 -0400] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/114.0" "-"
192.168.231.1 - - [16/Jun/2023:07:50:06 -0400] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/114.0" "-"
```

### Nginx错误日志

Nginx错误文件路径：`/var/log/nginx/error.log`

此错误是由于SELINUX没有做上下文关联配置，Nginx用户没有对该文件的访问权限导致

```bash
[root@localhost /]# tail /var/log/nginx/error.log
2023/06/16 09:05:28 [emerg] 6879#6879: unexpected "}" in /etc/nginx/conf.d/tangzai.conf:12
2023/06/16 09:06:35 [error] 6947#6947: *1 open() "/tangzai/abc.html" failed (2: No such file or directory), client: 192.168.231.1, server: tangzai.io, request: "GET /abc.html HTTP/1.1", host: "192.168.231.133:8080"
```

### 日志缓存

==该功能默认不打开，也不建议打开==

Nginx服务器的日志文件写入，是会遵循以下循环：

1. 打开日志文件
2. 写入日志信息
3. 关闭日志文件

不停的循环此操作会耗费大量的IO资源，所以希望将日志信息先写入缓存，后续在一起写在日志文件，达到一次打开，多次写入

```bash
[root@localhost /]# vim /etc/nginx/conf.d/tangzai.conf
server{
        open_log_file_cache max=1000 inactive=20s min_uses=3 valid=1m;
        listen 8080;
        server_name tangzai.io;
location / {
        root /tangzai;
        index index.html;
        }

error_page 404 /404.html;
location = /404.html {
        root /tangzai;
        }
}

```

+ `open_log_file_cache`：定义日志缓存
+ `max` 1000：最大缓存数量为1000
+ `inactive=20s min_uses=3`  ：20s内使用该文件小于3次则直接关闭文件
+ `valid=1m`：检查周期为1分钟

总结：缓存数量为1000，如果20s内缓存文件的使用次数小于3次，则直接关闭文件1分钟。这1分钟内的日志信息都写在缓存

### 日志分析

#### 1、查看日志文件行数

```bash
# error.log 文件的一共有1005行
[root@localhost log]# cat error.log | wc -l
1005
```

#### 2、`awk`命令过滤

#### 格式：

```bash
awk 'pattern { action }' file
```

#### `awk`命令的匹配

awk 命令使用`$num`来做匹配，表示匹配第num列的记录，其中0表示匹配所有列

```bash
# 输出文件中第一行的所有内容信息
[root@localhost log]# head -1 error.log |awk '{print $0}'
[Fri Nov 30 09:51:19 2018] [error] [client 157.55.39.0] File does not exist: /var/www/cas-hm.pbh.gov.br/robots.txt

# 输出文件中第一行的第4列信息
[root@localhost log]# head -1 error.log |awk '{print $4}'
09:51:19
```

#### 查看`error.log`文件2022年的错误记录条数

```bash
# $5 表示年份列
[root@localhost log]# awk '$5 = 2022' error.log | wc -l
1006
```

#### 查看2022年12月的访问数量

+ 使用`~`触发正则表达式
+ 在`//`里面填写正则表达式
+ `{action}`匹配后执行动作

```bash
# 使用正则匹配出2022年12月的所有记录并计数
[root@localhost log]# grep -oE "[0-9]{2}/Dec/2022" access.log |head
01/Dec/2022
01/Dec/2022
01/Dec/2022
01/Dec/2022
01/Dec/2022
01/Dec/2022
01/Dec/2022
01/Dec/2022
01/Dec/2022
01/Dec/2022
[root@localhost log]# grep -oE "[0-9]{2}/Dec/2022" access.log | wc -l
19493
```

#### 查看2022年12月26日的访问量

其中，正则表达式中，第一个匹配必须对应，如下面，$2 是月份列，则必须先匹配月份，且严格区分大小写

```bash
[root@localhost log]# grep -oE "26/Dec/2022" access.log | wc -l
168
```

#### 查看访问次数最多的前10个IP地址

```bash
# $1 表示IP地址，uniq去重并计数，sort排序
[root@localhost log]# awk '{print $1}' access.log |uniq -c |sort -nr |head
  14200 201.54.214.79
   3016 201.54.214.79
   2924 201.54.214.79
   2662 20.195.192.160
   2582 201.54.214.79
   2171 201.54.208.61
   2110 201.54.214.79
   2002 20.195.192.160
   1727 20.195.192.160
   1686 201.54.214.79

```

#### 统计访问次数最多的前100个IP地址

```bash
[root@localhost log]# awk '{print $1}' access.log |uniq -c |sort -nr |head -100
  14200 201.54.214.79
   3016 201.54.214.79
   2924 201.54.214.79
   2662 20.195.192.160
   2582 201.54.214.79
   2171 201.54.208.61
   2110 201.54.214.79
   2002 20.195.192.160
```

#### 统计2022年12月20日访问次数最多的页面

```bash
# uniq只能删除相同的相邻信息，所以需要先使用sort做排序
[root@localhost log]# grep -E '20/Dec/2022' access.log |awk '{print $7}' |sort |uniq -c |sort -nr |head
     83 /
     76 /cas/login
     50 /cas/images/logo2.png
     34 /cas/js/libs/jqueryui/1.8.5/jquery-ui.min.js
     34 /cas/js/libs/jquery/1.4.2/jquery.min.js
     34 /cas/js/jquery.cookie.js
     34 /cas/js/jquery-1.11.1.min.js
     34 /cas/js/cas.js
     34 /cas/css/cas.css
     20 /cas/login?service=https%3A%2F%2Fbhisshomologa.pbh.gov.br%2FcontroleAcesso%2Fj_acegi_cas_security_check

     
# 统计访问最多的页面
[root@localhost log]# awk '{print $7}' access.log |sort |uniq -c | sort -nr |head
 380724 /cas/login?service=https%3A%2F%2Fbhisshomologa.pbh.gov.br%2Fnfse%2Fj_acegi_cas_security_check
 266846 /
  74163 /cas/login
  59105 /cas/images/logo2.png
  42877 /cas/css/cas.css
  42813 /cas/js/cas.js
  42792 /cas/js/libs/jqueryui/1.8.5/jquery-ui.min.js
  42788 /cas/js/libs/jquery/1.4.2/jquery.min.js
  42787 /cas/js/jquery.cookie.js
  42776 /cas/js/jquery-1.11.1.min.js

```

#### 统计2022年12月的URL访问内容总大小

```bash
# grep匹配出2022年12月的所有记录，awk列出需要的信息，sort排序，uniq去重，sort再排序，awk列出URL并且计算出访问量
[root@localhost log]# grep -E '[0-9]{2}/Dec/2022' access.log |awk '{print $7,$10}' |sort |uniq -c |sort -nr |awk '{print $2, $1*$3}' |head
/ 6645408
/cas/css/cas.css 1257432
/cas/js/libs/jquery/1.4.2/jquery.min.js 20904064
/cas/js/libs/jqueryui/1.8.5/jquery-ui.min.js 42184511
/cas/login 1367184
/cas/login 1276360
/cas/images/logo2.png 177525
/cas/js/jquery.cookie.js 1084004
/cas/js/cas.js 688758
/cas/js/jquery-1.11.1.min.js 15441984

```

#### 统计2022年12月的各个状态码数量

```bash
# grep 正则匹配出2022年12月的状态码，awk列出需要信息，sort排序一遍uniq计数，uniq计数，sort再排序
[root@localhost log]# grep -E '[0-9]{2}/Dec/2022' access.log |awk '{print $9}' |sort |uniq -c |sort -nr |head
  17760 200
   1184 304
    289 301
    137 302
    123 404
```

#### 统计2022年12月每个IP地址访问状态码为404的数量

```bash
[root@localhost log]# grep -E '[0-9]{2}/Dec/2022' access.log | awk '$9==404 {print $1,$9}' |sort |uniq -c |sort -nr |head
     11 35.193.35.20 404
     11 34.94.186.34 404
     10 66.249.64.123 404
      6 66.249.66.87 404
      6 66.249.64.125 404
      5 40.77.167.98 404
      4 66.249.66.86 404
      4 66.249.66.85 404
      4 66.249.64.121 404
      4 152.255.97.18 404
```



## 实战2：个性化404

### 1、修改主机配置文件

```bash
[root@localhost /]# cat /etc/nginx/conf.d/tangzai.conf
server{
        listen 8080;
        server_name tangzai.io;
location / {
        root /tangzai;
        index index.html;
        }

# 404 文件重新定义
error_page 404 /404.html;
location = /404.html {
        root /tangzai;	# 定义404文件所在路径
        }
}

```

### 2、创建404文件

```bash
[root@localhost /]#echo "No This file" > /tangzai/404.html
```

### 3、客户端测试

<img src="Linux%E4%B8%AD%E9%97%B4%E4%BB%B6.assets/image-20230616211004832.png" alt="image-20230616211004832" style="zoom:50%;" />

## Nginx Web模块

### 链接状态模块

NGINX 连接状态模块（ngx_http_stub_status_module）提供了对基本状态信息的访问。此模块默认不会构建，应使用 `--with-http_stub_status_module` 配置参数启用它。例如，可以在 NGINX 配置文件中添加以下内容来启用此模块：

```
location = /basic_status {
    stub_status;
}
```

#### 配置演示

```bash
[root@localhost log]# vim /etc/nginx/conf.d/default.conf
# 添加下面内容
location /nginx_status {
        stub_status;
        allow all;
    }
[root@localhost log]# vim /etc/nginx/conf.d/default.conf
```

<img src="Linux%E4%B8%AD%E9%97%B4%E4%BB%B6.assets/image-20230617155329656.png" alt="image-20230617155329656" style="zoom:50%;" />

### 随机主页模块

nginx的随机主页模块是`http_random_index_module`。它可以在编译时通过指定编译选项`--with-http_random_index_module`来配置。这个模块的作用是在多个网页中随机选择一个做为主页，==隐藏文件不会称为随机主页==

#### 配置演示

```bash
# 修改配置文件
[root@localhost log]# vim /etc/nginx/conf.d/default.conf
 location / {
        # root   /usr/share/nginx/html;
        # index  index.html index.htm;
        root /app/index;
        random_index on;
    }

# 创建所需文件
[root@localhost log]# mkdir /app/index -p
[root@localhost log]# cd /app/index/
[root@localhost index]# touch {red.html,green.html,bule.html,.yellow.html}
[root@localhost index]# vim red.html
[root@localhost index]# vim green.html
[root@localhost index]# echo "bulu.html is this" >> bule.html
[root@localhost index]# echo "yellow.html is this" >> .yellow.html

# 重启服务并关闭SELINUX
[root@localhost index]# systemctl restart nginx
[root@localhost index]# setenforce 0

```

### 替换模块

Nginx中的`ngx_http_sub_module`模块是一个过滤器，它修改网站响应内容中的字符串。这个模块已经内置在nginx中，通常用于网页内容有问题，需要紧急处理的时候

#### 配置演示

```bash
[root@localhost index]# vim /etc/nginx/conf.d/default.conf
# 添加以下内容
	sub_filter nginx 'AAA';
	# 关闭只替换一次的操作，默认开启
    sub_filter_once off;

[root@localhost index]# systemctl restart nginx

```

## Nginx访问限制

限制IP地址在一定时间内的访问此时，可用于有效的放置DOS攻击

#### 配置演示

```bash
[root@localhost index]# vim /etc/nginx/nginx.conf
# 在http块下添加下面内容
http {
    limit_req_zone $binary_remote_addr zone=req_zone:10m rate=1r/s;

[root@localhost index]# vim /etc/nginx/conf.d/default.conf
# 在server块下添加下面内容
server {
    limit_req zone=req_zone;
    
# 下载压力测试工具
yum install -y httpd-tools

# 发起测试
[root@localhost index]# ab -n 100 -c 10 http://192.168.231.133/
This is ApacheBench, Version 2.3 <$Revision: 1843412 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 192.168.231.133 (be patient).....done


Server Software:        nginx/1.25.1
Server Hostname:        192.168.231.133
Server Port:            80

Document Path:          /
Document Length:        613 bytes

Concurrency Level:      10
Time taken for tests:   0.160 seconds
# 发起100次请求，失败99次
Complete requests:      100
Failed requests:        99
   (Connect: 0, Receive: 0, Length: 99, Exceptions: 0)
Non-2xx responses:      99
Total transferred:      64689 bytes
HTML transferred:       49618 bytes
Requests per second:    626.10 [#/sec] (mean)
Time per request:       15.972 [ms] (mean)
Time per request:       1.597 [ms] (mean, across all concurrent requests)
Transfer rate:          395.52 [Kbytes/sec] received
```

## Nginx访问控制

### 基于IP

可在Nginx子配置文件中设置黑白名单以限制IP地址的访问

#### 配置演示

```bash
[root@localhost index]# vim /etc/nginx/conf.d/default.conf
server {
    deny 192.168.231.1;
    allow all;
[root@localhost index]# systemctl restart nginx
```

### 基于用户

```bash
# 安装需要的工具
yum install -y httpd-tools

# 创建用户
[root@localhost index]# htpasswd -cm /etc/nginx/conf.d/passwd user10
New password:
Re-type new password:
Adding password for user user10
[root@localhost index]# htpasswd -m /etc/nginx/conf.d/passwd user20
New password:
Re-type new password:
Adding password for user user20

[root@localhost index]# vim /etc/nginx/conf.d/default.conf
# 在server块下添加以下内容
server {
     auth_basic "Hellow auth";
     auth_basic_user_file /etc/nginx/conf.d/passwd;

[root@localhost index]# systemctl restart nginx
```

**客户端测试**

<img src="Linux%E4%B8%AD%E9%97%B4%E4%BB%B6.assets/image-20230617172723500.png" alt="image-20230617172723500" style="zoom:50%;" />

## Nginx安全配置

### 目录遍历漏洞防御

在Nginx网站配置文件中添加下面内容

```bash
[root@localhost sec]# vim /etc/nginx/conf.d/default.conf
location / {
    autoindex off;
}
[root@localhost sec]# systemctl restart nginx
```

### 版本泄露

在没有配置之前，比如我们访问一个不存在的漏洞会看到nginx服务的版本号

<img src="Linux%E4%B8%AD%E9%97%B4%E4%BB%B6.assets/image-20230617231837899.png" alt="image-20230617231837899" style="zoom:50%;" />

在nginx主配置文件添加以下内容

```bash
[root@localhost sec]# systemctl restart nginx
http {
    ...
    server_tokens off;
    ...
}

```

再次测试

<img src="Linux%E4%B8%AD%E9%97%B4%E4%BB%B6.assets/image-20230617231947365.png" alt="image-20230617231947365" style="zoom:50%;" />

### Nginx 错误文件日志定位

查看Nginx服务的主配置文件：`/etc/nginx/nginx.conf`

```bash
user  nginx;
worker_processes  auto;
# 一般在第三行
error_log  /var/log/nginx/error.log notice;
```

### Nginx 当前网站根目录定位

Nginx所有网站的配置文件都在子配置文件夹中：`/etc/nginx/conf.d/`，通常文件名就是网站的域名，默认网站是：`default.conf`，通过查看默认网站的配置文件可以看到网站根目录的所在位置

```bash
[root@localhost nginx]# ls /etc/nginx/conf.d/
default.conf  default.conf.bak  passwd  tangzai.conf

[root@localhost nginx]# vim /etc/nginx/conf.d/default.conf
server{
...
    location / {
        root   /usr/share/nginx/html;		# 网站根目录（默认）路径
        index  index.html index.htm;		# 网站自动匹配文件	
        autoindex off;
    }
...
}
```

### Nginx 最大连接数限制

修改主配置文件

```bash
[root@localhost nginx]# vim /etc/nginx/nginx.conf
events {
    worker_connections  1024;
}

```

### 修改 403 和 404 错误页面为网站根目录下的 error4.html

修改站点配置文件

```bash
[root@localhost nginx]# vim /etc/nginx/conf.d/default.conf
	# 50x 的错误配置是default.conf自带的，直接将遮4行拷贝改就行
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

	# 添加以下配置
    error_page   403 404  /error4.html;
    location = /error4.html {
        root   /usr/share/nginx/html;
    }
```

创建所需文件

```bash
[root@localhost nginx]# cd /usr/share/nginx/html
[root@localhost html]# echo "error4.html is this" > error4.html
```



# Linux MySQL

## 1. RhEL8-Linux MySQL安装

> Mysql官方下载网站：https://dev.mysql.com/downloads/mysql/

### 1.1 检查依赖

```bash
[root@localhost opt]# rpm -qa |grep libaio
libaio-0.3.110-12.el8.x86_64
[root@localhost opt]# rpm -qa |grep net-tools
net-tools-2.0-0.51.20160912git.el8.x86_64
```

### 1.2 将下列文件粘贴到Linux

```bash
[root@localhost db]# ls
mysql-community-client-8.0.33-1.el8.x86_64.rpm          
mysql-community-common-8.0.33-1.el8.x86_64.rpm          
mysql-community-libs-8.0.33-1.el8.x86_64.rpm
mysql-community-client-plugins-8.0.33-1.el8.x86_64.rpm  
mysql-community-icu-data-files-8.0.33-1.el8.x86_64.rpm  
mysql-community-server-8.0.33-1.el8.x86_64.rpm
```

### 1.3 按下面顺序安装

```bash
[root@localhost db]# rpm -ivh mysql-community-common-8.0.33-1.el8.x86_64.rpm
[root@localhost db]# rpm -ivh mysql-community-client-plugins-8.0.33-1.el8.x86_64.rpm
[root@localhost db]# rpm -ivh mysql-community-libs-8.0.33-1.el8.x86_64.rpm
[root@localhost db]# rpm -ivh mysql-community-client-8.0.33-1.el8.x86_64.rpm
[root@localhost db]# rpm -ivh mysql-community-icu-data-files-8.0.33-1.el8.x86_64.rpm
[root@localhost db]# rpm -ivh mysql-community-server-8.0.33-1.el8.x86_64.rpm
```

### 1.4 检查是否安装成功

```bash
[root@localhost db]# mysql --version
mysql  Ver 8.0.33 for Linux on x86_64 (MySQL Community Server - GPL)
```

### 1.5 服务初始化

为了保证数据库目录文件的所有者为MySQL登录用户，如果你是以root身份运行MySQL服务，需要执行下面命令初始化

```bash
[root@localhost db]# mysqld --initialize --user=mysql
```

说明：--initialize 选项默认以“安全”模式来初始化，则会为root用户生成一个**密码并将密码标记为过期**，登录后你需要设置一个新密码，生成**临时密码**会往日志中记录

**查看密码**

```bash
[root@localhost db]# cat /var/log/mysqld.log
2023-06-17T11:58:24.973935Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.0.33) initializing of server in progress as process 7929
2023-06-17T11:58:25.038327Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2023-06-17T11:58:27.608010Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2023-06-17T11:58:35.010637Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: a.O+euqpd8/8
```

### 1.6启动服务

```bash
[root@localhost db]# systemctl start mysqld.service
[root@localhost db]# systemctl enable mysqld@
Created symlink /etc/systemd/system/multi-user.target.wants/mysqld@.service → /usr/lib/systemd/system/mysqld@.service.
```

### 1.7 MySQL 首次登录

登录密码是日志文件中的密码，要使用数据库之前必须先修改密码

```bash
[root@localhost db]# mysql -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.33

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.

# 修改密码并且重新登录
mysql> alter user 'root'@'localhost' identified by 'root';
Query OK, 0 rows affected (0.02 sec)

mysql> exit
```

### 1.8 相关文件

```bash
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
配置文件：/etc/my.cnf
```

## 2. MySQL忘记密码

### 2.1 修改配置文件

在`[mysqld]`添加下面内容

```bash
[root@localhost local]# vim /etc/my.cnf
[mysqld]
skip-grant-tables

# 重启服务
[root@localhost local]# systemctl restart mysqld
```

直接输入MySQL即可登录服务器

```bash
[root@localhost local]# mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.33 MySQL Community Server - GPL

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```

### 2.2 刷新权限修改密码

```bash
mysql> flush privileges;
Query OK, 0 rows affected (0.01 sec)

mysql> alter user 'root'@'localhost' identified by 'Aa123456';
Query OK, 0 rows affected (0.02 sec)
```

### 2.3 恢复配置文件并重启服务器

```bash
[root@localhost local]# vim /etc/my.cnf
[mysqld]
# skip-grant-tables

# 重启服务
[root@localhost local]# systemctl restart mysqld
```

## 3. Linux 安全配置

### 3.1 查看日志文件位置

日志文件的位置的确定以配置文件为准

Linux：

```bash
log-error=/var/log/mysqld.log
配置文件：/etc/my.cnf
```

Windows：

```bash
[mysqld]
datadir=E:/phpstudy/phpstudy/phpstudy_pro/Extensions/MySQL5.7.26/data/
配置文件：my.ini
```

### 3.2 将数据库设置为只允许本地访问

```bash
# 修改配置文件，加入以下内容
bind-address=127.0.0.1
```

### 3.3 禁止Mysql对文件操作

此配置可有效防御DNSLog攻击和防止hack通过SQL漏洞对服务器挂马

```bash
当secure_file_priv的值为null ，表示限制mysqld 不允许导入|导出
当secure_file_priv的值为/tmp/ ，表示限制mysqld 的导入|导出只能发生在/tmp/目录下
当secure_file_priv的值没有具体值时，表示不对mysqld 的导入|导出做限制
```

答案：

```bash
secure_fire_priv=null
```

### 3.4 查看Mysql密码长度要求

你在MySQL命令行客户端中使用`SHOW VARIABLES LIKE 'validate_password%';`命令来查看当前MySQL服务器的密码验证策略。这将显示一系列变量，其中`validate_password_length`变量表示密码的最小长度要求。

```
mysql> show variables like 'validata_password%';
+--------------------------------------+-------+
| Variable_name                        | Value |
+--------------------------------------+-------+
| validate_password_length             | 8     |
| ...                                  | ...   |
+--------------------------------------+-------+
```

### 3.5 查看登录次数限制和登录延迟

```bash
mysql> show variables like 'max_connect_errors';
+--------------------+-------+
| Variable_name      | Value |
+--------------------+-------+
| max_connect_errors | 100   |
+--------------------+-------+
1 row in set (0.01 sec)


mysql> show variables like 'delayed_insert_timeout';
+------------------------+-------+
| Variable_name          | Value |
+------------------------+-------+
| delayed_insert_timeout | 300   |
+------------------------+-------+
1 row in set (0.01 sec)

```

