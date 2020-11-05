## Docker前言

### 简介

Docker 是一个开源的应用容器引擎，基于 [Go 语言](https://www.runoob.com/go/go-tutorial.html)   并遵从 Apache2.0 协议开源。

Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。

容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）,更重要的是容器性能开销极低。

### 应用场景

- Web 应用的自动化打包和发布。

- 自动化测试和持续集成、发布。
- 在服务型环境中部署和调整数据库或其他的后台应用。
- 从头编译或者扩展现有的 OpenShift 或 Cloud Foundry 平台来搭建自己的 PaaS 环境。

### 优点

Docker 是一个用于开发，交付和运行应用程序的开放平台。Docker 使您能够将应用程序与基础架构分开，从而可以快速交付软件。借助  Docker，您可以与管理应用程序相同的方式来管理基础架构。通过利用 Docker  的方法来快速交付，测试和部署代码，您可以大大减少编写代码和在生产环境中运行代码之间的延迟。

**1. 快速，一致地交付您的应用程序**

Docker 允许开发人员使用您提供的应用程序或服务的本地容器在标准化环境中工作，从而简化了开发的生命周期。

容器非常适合持续集成和持续交付（CI / CD）工作流程，请考虑以下示例方案：

- 您的开发人员在本地编写代码，并使用 Docker 容器与同事共享他们的工作。
- 他们使用 Docker 将其应用程序推送到测试环境中，并执行自动或手动测试。
- 当开发人员发现错误时，他们可以在开发环境中对其进行修复，然后将其重新部署到测试环境中，以进行测试和验证。
- 测试完成后，将修补程序推送给生产环境，就像将更新的镜像推送到生产环境一样简单。

**2. 响应式部署和扩展**

Docker 是基于容器的平台，允许高度可移植的工作负载。Docker 容器可以在开发人员的本机上，数据中心的物理或虚拟机上，云服务上或混合环境中运行。

Docker 的可移植性和轻量级的特性，还可以使您轻松地完成动态管理的工作负担，并根据业务需求指示，实时扩展或拆除应用程序和服务。

**3. 在同一硬件上运行更多工作负载**

Docker 轻巧快速。它为基于虚拟机管理程序的虚拟机提供了可行、经济、高效的替代方案，因此您可以利用更多的计算能力来实现业务目标。Docker 非常适合于高密度环境以及中小型部署，而您可以用更少的资源做更多的事情。

### CentOS7.0 安装Docker

**1. 卸载旧版本**

较旧的 Docker 版本称为 docker 或 docker-engine 。如果已安装这些程序，请卸载它们以及相关的依赖项。

```go
[root@iZj6c0p2g1civqsvqacl2zZ /]# sudo yum remove docker docker-client docker-client-latest docker-common  docker-latest docker-latest-logrotate docker-logrotate docker-engine
Loaded plugins: fastestmirror
No Match for argument: docker
No Match for argument: docker-client
No Match for argument: docker-client-latest
No Match for argument: docker-common
No Match for argument: docker-latest
No Match for argument: docker-latest-logrotate
No Match for argument: docker-logrotate
No Match for argument: docker-engine
No Packages marked for removal
```

**2. 安装依赖软件包**

yum-utils 提供了 yum-config-manager  ，并且 device mapper 存储驱动程序需要 device-mapper-persistent-data 和 lvm2。

```go
[root@iZj6c0p2g1civqsvqacl2zZ /]# sudo yum install -y yum-utils device-mapper-persistent-data lvm2
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
Resolving Dependencies
--> Running transaction check
Dependencies Resolved
...
```

**3. 更新yum源**

> 使用官方源地址（比较慢）

```go
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

> 可以选择国内的一些源地址:
>
> 阿里云

```go
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

> 清华大学

```go
sudo yum-config-manager --add-repo https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/centos/docker-ce.repo
```

**4. 安装 Docker Engine-Community 和 containerd** 

```go
[root@iZj6c0p2g1civqsvqacl2zZ ~]# sudo yum -y install docker-ce docker-ce-cli containerd.io
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
Resolving Dependencies
--> Running transaction check
---> Package containerd.io.x86_64 0:1.3.7-3.1.el7 will be installed
--> Processing Dependency: container-selinux >= 2:2.74 for package: containerd.io-1.3.7-3.1.el7.x86_64
---> Package docker-ce.x86_64 3:19.03.13-3.el7 will be installed
--> Processing Dependency: libcgroup for package: 3:docker-ce-19.03.13-3.el7.x86_64
...
```

### 运行docker

> 启动docker

```go
$ sudo systemctl start docker
```

> 通过运行 hello-world 映像来验证是否正确安装了 Docker Engine-Community 。

```go
sudo docker run hello-world
```



## Docker 部署Go项目

###  环境准备

> 1. 安装docker并开启服务
> 2. 安装go环境, 并且 version > 1.11, 开启 go mod 模式

### 拉取项目

> 示例项目: yys-scrm

```go
$ git clone ssh://git@gitlabj01.vdongchina.com:2022/framework/yys-scrm.git
```

### 编译项目

#### 切换到项目目录

>  note: 项目目录修改为自己的

```go
[root@localhost yys-scrm]# cd /usr/local/src/golang/myproject/yys-scrm/
[root@localhost yys-scrm]# ls
application  config  go.mod  go.sum  main.go  README.md  runtime
```

#### 编译go项目

```go
[root@localhost yys-scrm]# go build main.go 
[root@localhost yys-scrm]# ls
application  config  go.mod  go.sum  main  main.go  README.md  runtime
```

### 制作镜像

#### 项目中创建Dockerfile

```go
[root@localhost yys-scrm]# touch Dockerfile
[root@localhost yys-scrm]# ls
application  config  Dockerfile go.mod  go.sum  main  main.go  README.md  runtime
```

> 编辑DockerFile文件的内容如下：
>
> ```go
> FROM golang:latest #定制的镜像都是基于 FROM 的镜像，这里是 golang
> WORKDIR $GOPATH/src # 指定工作目录
> COPY main $GOPATH/src/main # 复制指令，从上下文目录中复制文件或者目录到容器里指定路径。
> COPY config $GOPATH/src/config
> COPY runtime $GOPATH/src/runtime
> ENV RATGO_RUNMODE dev # 设置环境变量，定义了环境变量，那么在后续的指令中，就可以使用这个环境变量。
> EXPOSE 8080 # 自定义容器外漏端口, 可与项目配置的服务端口一致
> ENTRYPOINT ["./main"] # 类似于 CMD 指令，但其不会被 docker run 的命令行参数指定的指令所覆盖，而且这些命令行参数会被当作参数送给 ENTRYPOINT 指令指定的程序。
> ```

#### 根据Dockerfile内容创建镜像

> 使用 docker build 命令创建镜像

```go
[root@localhost yys-scrm]# docker build -t go-yys-scrm:v1.0 .
Sending build context to Docker daemon  15.76MB
Step 1/8 : FROM golang:latest
 ---> 05c8f6d2538a
Step 2/8 : WORKDIR $GOPATH/src
 ---> Using cache
 ---> 9e7118d6867c
Step 3/8 : COPY main $GOPATH/src/main
 ---> Using cache
 ---> c8bf03ac6866
Step 4/8 : COPY config $GOPATH/src/config
 ---> Using cache
 ---> d26c3072b18a
Step 5/8 : COPY runtime $GOPATH/src/runtime
 ---> 9c0aa1f3e79a
Step 6/8 : ENV RATGO_RUNMODE dev
 ---> Running in ce1f6dc42916
Removing intermediate container ce1f6dc42916
 ---> 435769d57891
Step 7/8 : EXPOSE 8080
 ---> Running in 8e94b198dcb0
Removing intermediate container 8e94b198dcb0
 ---> 7cedd76e6e4d
Step 8/8 : ENTRYPOINT ["./main"]
 ---> Running in ba66127dcf9a
Removing intermediate container ba66127dcf9a
 ---> 547fafa4a334
Successfully built 547fafa4a334
Successfully tagged go-yys-scrm:v1.0
```

####  查看镜像

> go-yys-scrm  v1.0创建成功

```go
[root@localhost yys-scrm]# docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
go-yys-scrm           v1.0                547fafa4a334        5 seconds ago       855MB
go                    latest              eb1eddc8f53f        13 minutes ago      855MB
go-yys-scrm           latest              1e9997361b7c        6 hours ago         855MB
xltdocker             latest              78aefdacdf59        3 days ago          845MB
httpd                 latest              417af7dc28bc        4 weeks ago         138MB
httpd                 test                417af7dc28bc        4 weeks ago         138MB
golang                latest              05c8f6d2538a        4 weeks ago         839MB
centos                centos7             7e6257c9f8d8        2 months ago        203MB
centos                latest              0d120b6ccaa8        2 months ago        215MB
portainer/portainer   latest              62771b0b9b09        2 months ago        79.1MB
training/webapp       latest              6fae60ef3446        5 years ago         349MB
```

#### 根据刚创建的镜像运行容器

> 运行并查看容器

```go
[root@localhost yys-scrm]# docker run -d -p 8080:8080 go-yys-scrm:v1.0
be1aa52593fb5a1a53b631703ad6e855ca436817894d7b2ead7b731e75a1d3be
[root@localhost yys-scrm]# docker ps 
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
be1aa52593fb        go-yys-scrm:v1.0    "./main"            6 seconds ago       Up 5 seconds        0.0.0.0:8080->8080/tcp   elastic_saha
```

#### 外部访问

```go
http://ip:8080
```

### 推送镜像

#### 推送至仓库 harbor

> docker设置

```go
[root@localhost yys-scrm]# echo '{ "insecure-registries":["http://harbor.moliyan.net:85"] }' > /etc/docker/daemon.json
```

> 登录 harbor 镜像仓库

```go
[root@localhost yys-scrm]# docker login http://harbor.moliyan.net:85
Username: admin
Password: Data123456.
Login Succeeded
```

> 在项目中标记镜像

```go
[root@localhost yys-scrm]# docker tag go-yys-scrm:v1.0 harbor.moliyan.net:85/go-test/go-yys-scrm:v1.0
```

> 推送镜像到当前项目

```go
[root@localhost yys-scrm]# docker push harbor.moliyan.net:85/go-test/go-yys-scrm:v1.0
The push refers to repository [harbor.moliyan.net:85/go-test/go-yys-scrm]
68b0b8aa24eb: Pushed 
2bd903edf932: Pushed 
95ff2db45213: Pushed 
1ba6a3ca6c41: Pushed
d2588c27d938: Pushed
6b6e43b44148: Pushed 
17bdf5e22660: Pushed 
d37096232ed8: Pushed 
6add0d2b5482: Pushed 
4ef54afed780: Pushed 
v1.0: digest: sha256:54879a6edee05b238d007d3979f38c7a50ad0294757b9f38e2da4829e37461c0 size: 2421
```
