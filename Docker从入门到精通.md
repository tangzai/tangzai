# 初识Docker

## Docker安装

### 1. 卸载旧版

```
yum remove docker \
    docker-client \
    docker-client-latest \
    docker-common \
    docker-latest \
    docker-latest-logrotate \
    docker-logrotate \
    docker-engine
```



### 2. 配置Docker的yum库

安装yum工具

```
yum install -y yum-utils
```

安装成功后，配置docke的yum源

```
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```



### 3. 安装Docker

```
yum install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
或者
yum install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin --allowerasing
```



### 验证是否安装成功

```
[root@localhost ~]# docker -v
Docker version 24.0.6, build ed223bc
```

## Docker 架构

Docker主要分为三部分：

+ 软件仓库：镜像文件的下载点（应用商店）
+ 镜像：镜像文件
+ 容器：镜像文件生成的虚拟机

![image-20231009153548513](Docker%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A.assets/image-20231009153548513.png)

## 配置镜像加速

镜像加速能让docker下载镜像文件的时间大大缩小

在首页的产品中，找到阿里云的**容器镜像服务**：

![img](Docker%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A.assets/16968370640604.png)

点击后进入控制台：

![img](Docker%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A.assets/16968370640591.png)

首次可能需要选择立刻开通，然后进入控制台。

## 5.3.配置镜像加速

找到**镜像工具**下的**镜像****加速器**：

![img](Docker%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A.assets/16968370640592.png)

页面向下滚动，即可找到配置的文档说明：

![img](Docker%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A.assets/16968370640603.png)

```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://pqjrtg29.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```



# Docker 服务相关命令

## Docker 服务命令

```
# 启动Docker
systemctl start docker

# 停止Docker
systemctl stop docker

# 重启
systemctl restart docker

# 设置开机自启
systemctl enable docker

# 执行docker ps命令，如果不报错，说明安装启动成功
docker ps
```

## Docker 镜像命令

+ 查看本地所有镜像

```
[root@localhost ~]# docker images
```

+ 搜索要使用的镜像

```
[root@localhost ~]# docker search 镜像名称
```

+ 下载镜像

```
[root@localhost ~]# docker pull 镜像名称
[root@localhost ~]# docker pull 镜像名称:版本号	# 指定版本号
```

+ 删除镜像

```
[root@localhost ~]# docker rmi <IMAGE ID>
```

关于镜像的搜索和版本信息的查看都可以docker官网上查看：`https://hub.docker.com/search`

+ 打包镜像

```shell
# docker save -o /ouput/path <IMAGE ID>
[root@localhost ~]# docker save -o nginx.tar nginx
```

+ 解压镜像

```shell
# docker load -i /input/path <IMAGE ID>
[root@localhost ~]# docker load -i nginx.tar
```

## Docker 容器相关命令

+ 查看容器

```
[root@localhost ~]# docker ps		# 查看当前正在运行的容器
[root@localhost ~]# docker ps -a		# 查看所有容器
```

+ 创建并启动容器

```
docker run 参数
```

- **-i:** 以交互模式运行容器，通常与 -t 同时使用；
- **-t:** 为容器重新分配一个伪输入终端，通常与 -i 同时使用；
- **-p:** 指定端口映射，格式为：**主机(宿主)端口:容器端口**
- **-d:** 后台运行容器，并返回容器ID；
- **--name="nginx-lb":** 为容器指定一个名称；
- **--dns 8.8.8.8:** 指定容器使用的DNS服务器，默认和宿主一致；

在运行的时候遇到了一个问题，环境红帽8

![image-20231009160624448](Docker%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A.assets/image-20231009160624448.png)

解决办法：安装依赖

```shell
[root@localhost ~]# sudo yum install -y libseccomp-devel
```

+ 以交互式的形式启动docker，退出则自动关闭容器，不会保持在后台运行

```shell
[root@localhost ~]# docker run -it --name=c1 centos:7 /bin/bash
[root@d52c4bb6cdb6 /]#
```

+ 守护进程的方式启动容器，退出不关闭容器，保持在后台运行

```shell
[root@localhost ~]# docker run -id --name=c2 centos:7 /bin/bash
4c4c5596231efbccccb237c1d4308183f01294a9457ae45daf28e834656c7081
```

+ **进入容器**

```shell
# docker exec -it <镜像ID | 镜像名> /bin/bash
[root@localhost ~]# docker exec -it c2 /bin/bash
```

+ 停止容器

```shell
# docker stop <镜像ID | 镜像名>
[root@localhost ~]# docker stop c2
```

+ 启动容器

```shell
# docker start <镜像ID | 镜像名>
[root@localhost ~]# docker start c2
```

+ 删除容器

```shell
# docker rm <镜像ID | 镜像名>
[root@localhost ~]# docker rm c2
```

+ 查看容器日志

```
[root@localhost ~]# docker logs -f nginx
```



# Docker 容器数据卷

## 数据卷的概念及作用

+ 数据卷是在宿主机上的一个目录
+ 当容器和数据卷目录绑定后，对方的修改会立即同步
+ 一个数据卷可以被多个容器同时挂载
+ 数据卷的挂载必须要在容器启动时挂载，容器启动后无法再重新挂载

数据卷是宿主机和容器的一个桥梁，用于做文件的交互

![image-20231009223809081](Docker%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A.assets/image-20231009223809081.png)

## 数据卷命令

| 命令                  | 说明                     |
| --------------------- | ------------------------ |
| docker volume create  | 创建数据卷               |
| docker volume ls      | 查看所有数据卷           |
| dockers volume rm     | 删除指定数据卷           |
| docker volume inspect | 查看某个数据卷的相信信息 |
| docker volume prune   | 清除数据卷               |

## 配置数据卷

+ 创建启动容器时，使用 -v 参数 设置数据卷

```shell
[root@localhost ~]# docker run  ... -v 宿主机目录:容器内目录
[root@localhost ~]# docker run -p 18080:80 -it -v html:/usr/share/nginx/html nginx
```

+ 查看数据集列表

```html
[root@localhost ~]# docker volume ls
DRIVER    VOLUME NAME
local     html
```

+ 查看数据集详细信息

```shell
[root@localhost ~]# docker volume inspect html
[
    {
        "CreatedAt": "2023-10-10T02:18:49-04:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/html/_data",
        "Name": "html",
        "Options": null,
        "Scope": "local"
    }
]
```

## 本地目录挂载

为了更好的管理数据，docker提供了一种本地目录挂载的方式来存储必要的docker文件，以此能更好的管理docker

```shell
[root@localhost ~]# docker run  ... -v 宿主机绝对路径:容器内目录
```

以本地目录挂载的形式创建mysql容器

```shell
docker run -d \
  --name mysql \
  -p 3306:3306 \
  -e TZ=Asia/Shanghai \
  -e MYSQL_ROOT_PASSWORD=123 \
  -v /root/mysql/data:/var/lib/mysql \
  -v /root/mysql/init:/docker-entrypoint-initdb.d \
  -v /root/mysql/conf:/etc/mysql/conf.d \
  mysql
```

# Dockerfile 语法

## 镜像结构

![image-20231010145310671](Docker%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A.assets/image-20231010145310671.png)

docker镜像文件是由多个文件构成的，这些文件在docker中被称为**层**，由上图可看出，ubantu是最底层，java环境为第二层，以此类推。

## 镜像命令

![image-20231010145528484](Docker%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A.assets/image-20231010145528484.png)

## Dockerfile 文件示例

```shell
# 指定基础镜像
FROM ubuntu:16.04
# 配置环境变量，JDK的安装目录，容器内时区
ENV JAVA_DIR=/usr/local
# 拷贝jdk和java项目的包
COPY ./jdk8.tar.gz $JAVA_DIR/
COPY ./docker-demo.jar /tmp/app.jar
# 安装JDK
RUN cd $JAVA_DIR \ && tar -xf ./jdk8.tar.gz \
&& mv ./jdk1.8.0_144 ./java8
# 配置环境变量
ENV JAVA_HOME=$JAVA_DIR/java8
ENV PATH=$PATH:$JAVA_HOME/bin
# 入口，java项目的启动命令
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

## Dockerfile文件案例

**Dockerfile文件**

```
# 指定基础镜像
FROM openjdk:11.0-jre-buster
# 拷贝jar包（项目包）
COPY docker-demo.jar /app.jar
# 入口 java项目的启动命令
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

编写好Dockerfile文件之后，在**Dockerfile文件所在文件夹**中构建镜像

```
docker build -t myImage:1.0 .
```

+ `-t`：指定镜像名
+ `.`：指定Dockerfile所在目录，如果就在当前目录，则指定为`.`

# 容器网络互连

默认情况下，所有容器都说以bridge方式连接到Docker的一个虚拟网桥上，这个网桥会自动给其他容器分配IP地址，达到默认互通

![image-20231010204857756](Docker%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A.assets/image-20231010204857756.png)

但是为了更方便的管理，我们也可以使用自定义网络，再创建一个虚拟网桥

![image-20231010204946214](Docker%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A.assets/image-20231010204946214.png)

在创建容器的时候，也可以直接使用`--network`指定要连接的网桥

```
docker run -p 18080:80 -it -v html:/usr/share/nginx/html --network 网络mi nginx
```

