## docker 部署Go项目

###  环境准备

> 1. 安装docker服务
> 2. 安装go环境, 并且 version > 1.11, 开启 go mod 模式

### 拉取项目

> 示例项目: yys-scrm

```go
$ git clone ssh://git@gitlabj01.vdongchina.com:2022/framework/yys-scrm.git
```

### 编译项目

#### 切换到项目目录

>  Note: 项目目录修改为自己的

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





