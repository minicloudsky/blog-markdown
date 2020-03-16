---
title: docker学习笔记
date: 2019-05-15 08:33:42
tags:
- docker
categories: docker
---

### 什么是docker
docker包括一个命令行程序、一个后台守护进程、以及一组远程服务。它解决了常见的软件问题，并建华路安装、运行、发布和删除软件。这一切能够实现是使用一项Unix技术，称为容器。
### 容器不是虚拟化
在没有docker时代，使用硬件虚拟化(虚拟机),以提供隔离，虚拟机提供虚拟硬件，可安装一个os和其他程序，需要很长时间创建，资源开销打，因为除了要执行你需要的软件，还需要运行整个操作系统的副本。
docker容器不使用硬件虚拟化，运行在docker容器中的程序接口和逐渐的Linux内核直接打交道，容器中运行的程序与计算机操作系统直接没有额外的中间层，没有资源被冗余软件的运行或者虚拟硬件模拟而浪费掉，docker不是虚拟化技术，相反，它可以帮助使用已经内置到操作系统中的容器技术。

<!-- more -->

### 隔离容器中运行软件
docker使用已经成为linux一部分的linux命名空间和cgroups，docker不提供容器技术，但它使得容器更易于使用要了解系统中在容器什么样子，让我们先建立一条基线，如下为计算机系统体系结构上运行的基本容器示例。
![](instance.png)

运行docker可以认为是在用户控件运行两个程序，首先是docker守护进程，如果正确安装，该进程应该始终处于运行状态。另一个为docker CLI，它是与用户交互的docker程序，如果要启动，停止，运行或安装软件，你可以用docker CLI执行相应命令。
![](three.png)

### docker容器隔离
docker构建容器隔离包括8个方面，具体如下:  

* `PID命令空间` ----- 进程标识符和能力
* `UTS命名空间` ----- 主机名和域名
* `MNT命名空间` ----- 文件系统访问和结构
* `IPC命名空间` ----- 通过共享内存的进程间通信
* `NET命名空间` ----- 网络访问和结构 
* `chroot()`   ----- 控制文件系统根目录的位置
* `cgroups`    ----- 资源保护  

Linux命名空间和`cgroups`管理着运行时容器，docker采用另一套技术，就像运输集装箱一样为文件提供容器。  
### 分发容器
docker容器可以看成物理运输的集装箱，这是你存储、运行应用程序及其所有依赖的盒子。docker可以执行、复制、轻松地分发容器。docker通过一种打包和分发软件，完成传统容器的封装。这个用来充当容器分发角色的组件被称为`镜像`。  
Docker镜像，是一个容器中运行程序的所有文件的捆绑快照。你可以从镜像中创造尽可能多的容器，但是你这样做时候，从相同的镜像启动的容器不共享文件系统的更改。当你用docker分发软件，其实就是分发这些镜像，并在接收的机器上创建容器镜像在docker生态系统中是可交付的基本单位。



------

### docker下安装常用工具命令总结

查看docker磁盘占用

`docker system df`

查看虚悬镜像

`docker image ls  -f dangling=true`

删除虚悬镜像

`docker image  prune`

根据仓库名列出镜像

`docker images ls Ubuntu`

列出镜像和标签

`docker images ls Ubuntu:18.04`

容器重命名 

docker rename name1 name2

## Dockerfile

from 指定基础镜像

以一个镜像为基础，在其上进行定制。必须是第一条命令

除了选择现有镜像为基础镜像外，Docker 还存在一个特殊的镜像，名为scratch。这个镜像是虚拟的概念，并不实际存在，它表示一个空白的镜像。

`FROM scratch`

如果你以scratch为基础镜像的话，意味着你不以任何镜像为基础，接下来所写的指令将作为镜像第一层开始存在

### RUN执行命令

run命令是指用来执行命令行命令的，有两种格式：

1. shell格式，run  <命令>  就像直接在命令行中输入的命令一样

   `RUN echo  '<h1>Hello ,Docker!</h1>'    > /usr/share/nginx/html/index.html`

   dockerfile写法

   ```
FROM debian:stretchRUNbuildDeps='gcc libc6-dev make wget' \    
   
   ```

&& apt-get update \    

&& apt-get install -y $buildDeps \    

&& wget -O redis.tar.gz "http://download.redis.io/releases/redis-5.0.3.tar.gz" \    

&& mkdir -p /usr/src/redis \    

&& tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1 \    

&& make -C /usr/src/redis \    

&& make -C /usr/src/redis install \    

&& rm -rf /var/lib/apt/lists/* \    

&& rm redis.tar.gz \    && rm -r /usr/src/redis \    

&& apt-get purge -y --auto-remove $buildDeps  
   ```bash
    写dockerfile时候，要注意不是写shell脚本，而是在定义每一层该如何构建。
   dockerfile支持shell的行尾换行方式，以及行首# 进行注释 ，后添加了清理工作的命令，删除了为了编译构建所需要的软件，清理了所有下载、展开的文件，并且还清理了apt缓存文件。这是很重要的一步，我们之前说过，镜像是多层存储，每一层的东西并不会在下一层被删除，会一直跟随着镜像。因此镜像构建时，一定要确保每一层只添加真正需要添加的东西，任何无关的东西都应该清理掉。

   ### 镜像构建

   `docker build 【选项】<上下文路径/url/->`

   例如构建nginx

   `docker build -t nginx:v3  .`

   ### 镜像构建上下文

   Docker 在运行时分为 Docker 引擎（也就是服务端守护进程）和客户端工具。Docker 的引擎提供了一组 REST API，被称为Docker Remote API，而如docker命令这样的客户端工具，则是通过这组 API 与 Docker 引擎交互，从而完成各种功能。因此，虽然表面上我们好像是在本机执行各种docker功能，但实际上，一切都是使用的远程调用形式在服务端（Docker 引擎）完成。也因为这种 C/S 设计，让我们操作远程服务器的 Docker 引擎变得轻而易举。当我们进行镜像构建的时候，并非所有定制都会通过RUN指令完成，经常会需要将一些本地文件复制进镜像，比如通过COPY指令、ADD指令等。而dockerbuild命令构建镜像，其实并非在本地构建，而是在服务端，也就是 Docker 引擎中构建的。那么在这种客户端/服务端的架构中，如何才能让服务端获得本地文件呢？

   这就引入了上下文的概念。当构建的时候，用户会指定构建镜像上下文的路径，docker build命令得知这个路径后，会将路径下的所有内容打包，然后上传给 Docker 引擎。这样 Docker 引擎收到这个上下文包后，展开就会获得构建镜像所需的一切文件。如果在Dockerfile中这么写：

   `COPY./package.json /app/`

   这并不是要复制执行docker build命令所在的目录下的package.json，也不是复制Dockerfile所在目录下的package.json，而是复制上下文（context）目录下的package.json。因此，COPY这类指令中的源文件的路径都是相对路径。这也是初学者经常会问的为什么COPY ../package.json /app或者COPY /opt/xxxx /app无法工作的原因，因为这些路径已经超出了上下文的范围，Docker 引擎无法获得这些位置的文件。如果真的需要那些文件，应该将它们复制到上下文目录中去。现在就可以理解刚才的命令docker build -t nginx:v3 .中的这个.，实际上是在指定上下文的目录，docker build命令会将该目录下的内容打包交给Docker 引擎以帮助构建镜像。

   一般来说，应该会将Dockerfile置于一个空目录下，或者项目根目录下。如果该目录下没有所需文件，那么应该把所需文件复制一份过来。如果目录下有些东西确实不希望构建时传给 Docker 引擎，那么可以用.gitignore一样的语法写一个.dockerignore，该文件是用于剔除不需要作为上下文传递给 Docker 引擎的。

   ### 其他 docker build 用法

   直接用git repo进行构建

   `docker build https://github.com/twang2218/gitlab-ce-zh.git#:11.1`

   该命令指定了构建所需的git repo，并且指定默认的master分支，构建目录为/11.1/,然后docker就会自己去git clone该项目，切换分支，并且进入指定目录后开始构建。

   用给定的tar 压缩包构建

   `dokcer build http://server/context.tar.gz`

   自动解压缩作为上下文，开始构建

   从标准输入中读取dockerfile进行构建

   `docker build - < Dockerfile`

   或

   `cat Dockerfile | docker build -`

   如果标准输入传入的是文本文件，则将其视为Dockerfile，并开始构建。这种形式由于直接从标准输入中读取 Dockerfile 的内容，它没有上下文，因此不可以像其他方法那样可以将本地文件COPY进镜像之类的事情。

   从标准输入中读取上下文压缩包进行构建

    `docker build - < context.tar.gz`

   如果发现标准输入的文件格式是gzip、bzip2以及xz的话，将会使其为上下文压缩包，直接将其展开，将里面视为上下文，并开始构建。

   ### Dockerfile 指令详解

   COPY 复制文件

   `ADD` 指令和 `COPY` 的格式和性质基本一致。但是在 `COPY` 基础上增加了一些功能。

   比如 `<源路径>` 可以是一个 `URL`，这种情况下，Docker 引擎会试图去下载这个链接的文件放到 `<目标路径>` 去。下载后的文件权限自动设置为 `600`，如果这并不是想要的权限，那么还需要增加额外的一层 `RUN` 进行权限调整，另外，如果下载的是个压缩包，需要解压缩，也一样还需要额外的一层 `RUN` 指令进行解压缩。所以不如直接使用 `RUN` 指令，然后使用 `wget` 或者 `curl` 工具下载，处理权限、解压缩、然后清理无用文件更合理。因此，这个功能其实并不实用，而且不推荐使用。

   如果 `<源路径>` 为一个 `tar` 压缩文件的话，压缩格式为 `gzip`, `bzip2` 以及 `xz` 的情况下，`ADD` 指令将会自动解压缩这个压缩文件到 `<目标路径>` 去。

   最适合使用 `ADD` 的场合，就是所提及的需要自动解压缩的场合。

   在使用该指令的时候还可以加上 `--chown=<user>:<group>` 选项来改变文件的所属用户及所属组。
   
   ```dockerfile
   ADD --chown=55:mygroup files* /mydir/
   ADD --chown=bin files* /mydir/
   ADD --chown=1 files* /mydir/
   ADD --chown=10:11 files* /mydir/
   ```

   ### CMD 容器启动命令

   `CMD` 指令的格式和 `RUN` 相似，也是两种格式：

   - `shell` 格式：`CMD <命令>`

- `exec` 格式：`CMD ["可执行文件", "参数1", "参数2"...]`
   - 参数列表格式：`CMD ["参数1", "参数2"...]`。在指定了 `ENTRYPOINT` 指令后，用 `CMD` 指定具体的参数。

   之前介绍容器的时候曾经说过，Docker 不是虚拟机，容器就是进程。既然是进程，那么在启动容器的时候，需要指定所运行的程序及参数。`CMD` 指令就是用于指定默认的容器主进程的启动命令的。

   在运行时可以指定新的命令来替代镜像设置中的这个默认命令，比如，`ubuntu` 镜像默认的 `CMD` 是 `/bin/bash`，如果我们直接 `docker run -it ubuntu` 的话，会直接进入 `bash`。我们也可以在运行时指定运行别的命令，如 `docker run -it ubuntu cat /etc/os-release`。这就是用 `cat /etc/os-release` 命令替换了默认的 `/bin/bash` 命令了，输出了系统版本信息。

   在指令格式上，一般推荐使用 `exec` 格式，这类格式在解析时会被解析为 JSON 数组，因此一定要使用双引号 `"`，而不要使用单引号。

   ### ENTRYPOINT 入口点

   `ENTRYPOINT` 的格式和 `RUN` 指令格式一样，分为 `exec` 格式和 `shell` 格式。

   `ENTRYPOINT` 的目的和 `CMD` 一样，都是在指定容器启动程序及参数。`ENTRYPOINT` 在运行时也可以替代，不过比 `CMD` 要略显繁琐，需要通过 `docker run` 的参数 `--entrypoint` 来指定。

   当指定了 `ENTRYPOINT` 后，`CMD` 的含义就发生了改变，不再是直接的运行其命令，而是将 `CMD` 的内容作为参数传给 `ENTRYPOINT` 指令，换句话说实际执行时，将变为：
   
   ```bash
   <ENTRYPOINT> "<CMD>"
   ```
当利用 docker run 来创建容器时，Docker 在后台运行的标准操作包括：
检查本地是否存在指定的镜像，不存在就从公有仓库下载
利用镜像创建并启动一个容器
分配一个文件系统，并在只读的镜像层外面挂载一层可读写层
从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去
从地址池配置一个 ip 地址给容器
执行用户指定的应用程序
执行完毕后容器被终止


#### mysql

##### 拉取mysql镜像

`docker pull mysql`

启动一个镜像，并将mysql映射到服务器3306端口

```bash
docker  run  -it --name  mysql -p 3306:3306   -e MYSQL_ROOT_PASSWORD=root   -v   /home/containermaps/mysql/log/:/var/log/mysql  -v /home/containermaps/mysql/conf.d/:/etc/mysql     -v   /home/containermaps/mysql/data:/var/lib/mysql -d mysql:5.7
```

默认用户为root

--name为容器名

-p指定端口

MYSQL_ROOT_PASSWORD 指定密码

进入mysql

`docker exec -it mysql5.7-2  bash`

`mysql     -uroot  -p`

#### nginx服务器

`docker pull nginx`

运行nginx

`docker run --name  nginx-test -p 80:80 -d nginx`

将nginx映射到本地目录

首先在/usr/local下新建目录

```bash
mkdir -p /usr/local/docker-nginx/www /usr/local/docker-nginx/logs /usr/local/docker-nginx/conf

将docker中nginx配置文件复制到到服务器

docker cp 63185c51103a:/etc/nginx/nginx.conf /usr/local/docker-nginx/conf/

关闭上一个容器，重新启动一个nginx容器

docker run -d -p 80:80 --name nginx-server -v /usr/local/docker-nginx/www:/usr/share/nginx/html -v /usr/local/docker-nginx/conf/nginx.conf:/etc/nginx/nginx.conf -v /usr/local/docker-nginx/logs:/var/log/nginx nginx
```

由此配置好以后，修改/usr/local/docker-nginx目录下的conf中的nginx.conf，就可以完成对nginx的配置，修改www目录下的html文件即可更新nginx部署的网页

#### redis数据库

```bash
docker pull redis
docker run --restart=always -d -p 6379:6379 -v /usr/local/docker-redis/data/:/data --name redis redis redis-server --appendonly yes --requirepass "redis"
```



##### 命令说明：

`-p 6388:6379 : 将容器的 6379 端口映射到主机的 6379 端口`
`-v /usr/local/docker-redis/data/ : 将主机中 /usr/local/docker-redis/data/ 目录下的 redis 挂载到容器的 /data`
`redis-server --appendonly yes : 在容器执行 redis-server 启动命令，并打开 redis 持久化配置`

连接redis

`docker exec -ti 5f4c4cf5a5f5 redis-cli a "redis passwd"`

[https://luoliangdsga.github.io/2018/04/26/%E4%BD%BF%E7%94%A8Docker%E9%83%A8%E7%BD%B2Redis/](https://luoliangdsga.github.io/2018/04/26/使用Docker部署Redis/)

#### mongodb

`docker pull mongo`

```bash
docker run -p 27017:27017 -v /usr/local/docker-mongodb/data:/data/db -d mongo
```

进入mongo

`docker exec -it mongo mongo admin`

### elasticsearch

```bash
docker pull docker.elastic.co/elasticsearch/elasticsearch:7.5.2
docker run -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:7.5.2
```

### kibana

```bash
docker pull docker.elastic.co/kibana/kibana:7.6.0

docker run --link YOUR_ELASTICSEARCH_CONTAINER_NAME_OR_ID:elasticsearch -p 5601:5601 {docker-repo}:{version}
```

### docker 容器中软件安装

- 使用国内镜像

```
mv /etc/apt/sources.list /etc/apt/sources.list.bak
echo "deb http://mirrors.163.com/debian/ jessie main non-free contrib" >> /etc/apt/sources.list
echo "deb http://mirrors.163.com/debian/ jessie-proposed-updates main non-free contrib" >>/etc/apt/sources.list
echo "deb-src http://mirrors.163.com/debian/ jessie main non-free contrib" >>/etc/apt/sources.list
echo "deb-src http://mirrors.163.com/debian/ jessie-proposed-updates main non-free contrib" >>/etc/apt/sources.list
```

- 更新 `apt-get` 指令

```
apt-get update
```

- 安装 `yum` 命令

```
apt-get install vim
```

- 添加 `ls` 命令

```
vim ~/.bashrc
```

- 在最后一行添加

```
alias ll='ls $LS_OPTIONS -l'
```

- 使之生效

```
source ~/.bashrc
```





