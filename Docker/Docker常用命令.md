## 一、安装Docker

```shell
#1、yum包更新到最新
yum update
#2、安装需要的软件,yum-install提供yum-config-manager功能,另外两个是devicemapper驱动依赖的
yum install -y yum-utils device-mapper-persistent-data lvm2
#3、设置yum源
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
#4、安装docker
yum install -y docker-ce
#5、查看docker版本，验证是否安装成功
docker -v
```

## 二、配置Docker

默认从http://hub.docker.com/下载docker镜像，太慢，可以替换为

- USTC：中科大镜像加速器
- 阿里云
- 网易云
- 腾讯云

Docker Hub官网：http://www.hub.docker.com

## 三、常用命令

### Docker命令

```shell
#启动docker
systemctl start docker
#查看docker启动状态
systemctl status docker
#关闭docker
systemctl stop docker
#重启docker
systemctl restart docker
#开机启动docker
systemctl enable docker
```

### Image命令

```shell
#查看镜像
docker images
docker image -q #查看所有的id
#搜索镜像
docker search redis
#拉取镜像(不加版本号,tag默认最新)
docker pull redis:5.0
#删除镜像(镜像ID)
docker rmi image_id
#删除所有的镜像
docker rmi `docker image -q`
```

### Container命令

```shell
#查看容器
docker ps #查看正在运行的容器
docker ps -a #查看所有的容器
#创建并启动容器
#  -i 容器一直运行
#  -t 分配一个终端 -it 交互式容器
#  -d 后台运行 -id 守护式容器
#  --name 容器取名字
docker run -it --name=centos7 centos:7 /bin/bash
docker start centos7 #前提是容器已经创建好了
#进入容器
docker exec -it c_mysql /bin/bash
#停止容器(容器名称)
docker stop centos7
#删除容器
docker rm centos7
#查看容器信息
docker inspect centos7
#查看容器进程信息
docker top centos7
```

## 四、数据卷

```shell
#挂载数据卷
# -v 宿主机目录:容器目录
docker run -it --name=c1 -v /root/data:/root/data_container centos:7 /bin/bash
```

### 数据卷容器

```shell
#使用 -v 参数,设置数据卷
docker run -it --name=c3 -v /volume centos:7 /bin/bash
#使用 --volumes-from 参数,设置数据卷
docker run -it --name=c1 --volumes-from c3 centos:7 /bin/bash
docker run -it --name=c2 --volumes-from c3 centos:7 /bin/bash
```

## 五、Docker镜像原理

操作系统组成部分

- 进程调度子系统
- 进程通信子系统
- 内存管理子系统
- 设备管理子系统
- <font color=red>文件管理子系统</font>
- 网络通信子系统
- 作业控制子系统

Linux文件系统由bootfs和rootfs两部分组成

- bootfs：包含bootloader(引导加载程序)和kernel(内核)
- rootfs：root文件系统，包含的就是典型Linux系统中的/dev、/proc、/bin、/etc等标准目录和文件
- 不同的Linux发行版本，bootfs基本一样，而rootfs不同，如ubuntu，centos等

Docker镜像是由特殊的文件系统叠加而成

- 最底层是bootfs，并使用宿主机的bootfs
- 第二层是rootfs，称为base image
- 然后再往上可以叠加其他的镜像文件
- 统一文件系统(Union File System)技术能够将不同的层整合成一个文件系统，为这些层提供一个统一的视角，这样就隐藏了多层的存在
- 一个镜像可以放在另一个镜像的上面。位于下面的镜像称为父镜像，最底部的镜像称为基础镜像
- 当从一个镜像启动容器时，Docker会在最顶层加载一个可读写文件系统作为容器

## 六、Dockerfile关键字

| 关键字     | 作用           | 备注                                                |
| ---------- | -------------- | --------------------------------------------------- |
| FROM       | 指定父镜像     | 指定dockerfile基于哪个image构建                     |
| MAINTAINER | 作者信息       | 用来标明这个dockerfile是谁写的                      |
| LABEL      | 标签           | 用来标明dockerfile的标签                            |
| RUN        | 执行命令       | 执行一段命令，默认是/bin/sh                         |
| CMD        | 容器启动命令   | 提供启动容器时候的命令 和ENTRYPOINT配合使用         |
| ENTRYPOINT | 入口           | 一般在制作一些执行就关闭的容器中使用                |
| COPY       | 复制文件       | build的时候复制文件到image中                        |
| ADD        | 添加文件       | build的时候添加文件到image中，可来源与远程服务      |
| ENV        | 环境变量       | 指定build的环境变量，通过-e覆盖                     |
| VOLUME     | 可挂载的数据卷 | 指定image的哪些目录可以在启动的时候挂载到文件系统中 |
| EXPOSE     | 暴露端口       | 定义容器运行的时候监听的端口，通过-p来绑定暴露端口  |

