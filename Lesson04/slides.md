---
theme: seriph
title: Linux 进阶教程之容器技术
class: text-center
transition: slide-left
mdc: true
overviewSnapshots: true
---

# Linux 进阶教程之容器技术

软件研发部 运维组

---
layout: quote
---

# 引言

---
layout: quote
---

## 引言

- 什么是容器技术？
- 容器技术和虚拟化技术有什么区别？
- 容器技术和 wsl 有什么区别？
- 容器技术有哪些应用场景？

---
layout: quote
---

### 什么是容器技术？

容器技术是一种轻量级的操作系统层面虚拟化技术，为软件应用及其依赖组件提供一个资源独立的运行环境。在容器化过程中，应用程序及其所有必要的依赖关系会被打包成一个可重用的镜像。镜像所使用的资源在逻辑上独立，共享系统的内存、CPU 和硬盘空间，以保证容器内部进程与外部进程相互独立。

---
layout: quote
---

### 什么是容器技术？

<div style="display: flex; align-items: center; justify-content: space-between;">
  <div style="width: 45%; max-height: 300px; display: flex; flex-direction: column; align-items: center;">
    <img src="/容器抽象结构.jpg" alt="" style="width: 100%; max-height: 300px; object-fit: contain;">
    <div style="font-style: italic; color: gray;">*图片来自网络</div>
  </div>
  <div style="width: 50%; padding-left: 20px;">
    <p>
      容器相当于从操作系统中抽象出来的一个进程，它拥有自己的文件系统、网络、进程空间等资源。所有容器基于同一个 Kernel ，不同容器中可以运行不同的操作系统,在这些不同的操作系统中，也可以运行不同的应用程序。
    </p>
    <p>
      容器是层状的，每一层都是只读的，只有最上层是可写的。这样做的好处是可以共享底层的文件系统，节省存储空间。
    </p>
  </div>
</div>

---
layout: quote
---

## 引言

- <span style="color: gray; opacity: 0.7;">什么是容器技术？</span>
- 容器技术和虚拟化技术有什么区别？
- 容器技术和 wsl 有什么区别？
- 容器技术有哪些应用场景？

---
layout: quote
---

### 容器技术和虚拟化技术有什么区别？

<div style="display: flex; flex-direction: column; justify-content: center; align-items: center; height: 100%; text-align: center;">
  <img src="/容器和虚拟机区别.png" alt="" style="height: 300px; object-fit: contain;">
  <div style="font-style: italic; color: gray;">*图片来自网络</div>
</div>

对于所有容器来说，它们都是在同一个操作系统内核上运行的，因此容器的启动速度非常快，通常只需要几秒钟。容器的资源消耗也非常低，因为它们共享操作系统内核，而不是像虚拟机那样每个都有一个操作系统。

---
layout: quote
---

## 引言

- <span style="color: gray; opacity: 0.7;">什么是容器技术？</span>
- <span style="color: gray; opacity: 0.7;">容器技术和虚拟化技术有什么区别？</span>
- 容器技术和 wsl 有什么区别？
- 容器技术有哪些应用场景？

---
layout: quote
---

### 容器技术和 wsl 有什么区别？

wsl (Windows Subsystem for Linux) ，是微软公司在 Windows 10 上推出的一个子系统，用于在 Windows 10 上运行 Linux 程序。

wsl 是在 system call 层面转义实现的，而容器技术是在进程层面实现的。wsl 是一个兼容层，而容器技术是一个进程。

而至于 wsl2 是一个完整的 Linux 内核，可以在 Windows 上运行一个完整的 Linux 系统，是属于虚拟机范畴的。而容器技术是在一个共享的 Linux 内核上运行的。

---
layout: quote
---

## 引言

- <span style="color: gray; opacity: 0.7;">什么是容器技术？</span>
- <span style="color: gray; opacity: 0.7;">容器技术和虚拟化技术有什么区别？</span>
- <span style="color: gray; opacity: 0.7;">容器技术和 wsl 有什么区别？</span>
- 容器技术有哪些应用场景？

---
layout: quote
---

### 容器技术有哪些应用场景？

- 快速部署生产环境。开发过程中，我们可以将应用程序和其依赖的库打包到一个容器中，然后在多个环境中部署这个容器。并且可以保证在不同环境中运行的容器是一致的。
- 快速部署测试环境。我们可以在容器中运行测试环境，然后在测试完成后销毁容器，以节省资源。
- 批量部署应用程序。开发完成后，我们可以编写容器编排文件，将容器快速批量部署到多个服务器上。
- 想要一个新的环境，但是不想装虚拟机。

---
layout: quote
---

# Linux容器原理

---
layout: quote
---

## Linux容器原理

- 容器的工作原理 —— Linux Namespace
- 容器的工作原理 —— Linux Cgroups
- 容器的工作原理 —— Linux UnionFS

---
layout: quote
---

### 容器的工作原理 —— Linux Namespace

Linux Namespace 是 Linux 内核提供的一种机制，用于隔离进程的资源。Linux 内核提供了 7 种 Namespace，分别是：

- `UTS`：隔离主机名和域名
- `IPC`：隔离进程间通信
- `PID`：隔离进程 ID
- `NET`：隔离网络设备、网络栈、端口等
- `MNT`：隔离文件系统挂载点
- `USER`：隔离用户和用户组
- `CGROUP`：隔离 cgroup 根目录

---
layout: quote
---

### 容器的工作原理 —— Linux Namespace

我们可以使用 `unshare` 命令创建一个新的 Namespace：

```bash
sudo unshare --uts --ipc --pid --net --mount --user --cgroup /bin/bash
```

然后我们可以在这个新的 Namespace 中执行命令，查看不同 Namespace 的信息：

```bash
hostname
ipcs
ps aux
ip a
mount
id
ls /sys/fs/cgroup
```

可以看到，在 Namespace 里面这些命令的输出与在宿主机中的完全不影响。

---
layout: quote
---

## Linux容器原理

- <span style="color: gray; opacity: 0.7;">容器的工作原理 —— Linux Namespace</span>
- 容器的工作原理 —— Linux Cgroups
- 容器的工作原理 —— Linux UnionFS

---
layout: quote
---

### 容器的工作原理 —— Linux Cgroups

Linux Cgroups 是 Linux 内核提供的一种机制，用于限制进程的资源。Linux 内核提供了 10 种 Cgroups，分别是：

- `cpu`：限制 CPU 使用率
- `memory`：限制内存使用量
- `blkio`：限制磁盘 IO
- `cpuset`：限制 CPU 核心
- `devices`：限制设备访问
- `freezer`：暂停和恢复进程
- `hugetlb`：限制大页内存
- `net_cls`：限制网络流量
- `net_prio`：限制网络优先级
- `pids`：限制进程数量

---
layout: quote
---

### 容器的工作原理 —— Linux Cgroups

我们可以使用 `cgcreate` 命令创建一个新的 Cgroup：

```bash
cgcreate -g cpu,memory:my-cgroup
```

然后我们可以使用 `cgset` 命令设置 Cgroup 的限制：

```bash
cgset -r cpu.cfs_quota_us=10000 my-cgroup
cgset -r memory.limit_in_bytes=100M my-cgroup
```

然后我们可以使用 `cgexec` 命令运行一个进程：

```bash
cgexec -g cpu,memory:my-cgroup /bin/bash
```

然后我们可以在这个进程中查看限制的效果：

```bash
stress --cpu 4 --io 2 --vm 2 --vm-bytes 128M --timeout 10s
```

---
layout: quote
---

## Linux容器原理

- <span style="color: gray; opacity: 0.7;">容器的工作原理 —— Linux Namespace</span>
- <span style="color: gray; opacity: 0.7;">容器的工作原理 —— Linux Cgroups</span>
- 容器的工作原理 —— Linux UnionFS

---
layout: quote
---

### 容器的工作原理 —— Linux UnionFS

Linux UnionFS 是 Linux 内核提供的一种机制，用于将多个文件系统合并为一个文件系统。Linux 内核提供了 4 种 UnionFS，分别是：

- `aufs`：Advanced UnionFS
- `overlay`：OverlayFS
- `overlay2`：OverlayFS 2
- `zfs`：Z File System

---
layout: quote
---

# 常见的容器技术

---
layout: quote
---

## 常见的容器技术

- Docker / Podman
- LXC

---
layout: quote
---

### Docker / Podman

Docker 是一个开源的容器引擎，可以让开发者打包应用程序和其依赖到一个容器中，然后在不同的环境中运行这个容器。Docker 使用了 Linux Namespace、Linux Cgroups 和 Linux UnionFS 等技术。

Podman 是一个与 Docker 兼容的容器引擎，但是 Podman 不需要 root 权限，可以在用户空间运行。

---
layout: quote
---

## 常见的容器技术

- <span style="color: gray; opacity: 0.7;">Docker / Podman</span>
- LXC

---
layout: quote
---

### LXC

LXC (Linux Containers) 是一个开源的容器引擎，可以让开发者打包应用程序和其依赖到一个容器中，然后在不同的环境中运行这个容器。LXC 使用了 Linux Namespace、Linux Cgroups 和 Linux UnionFS 等技术。

---
layout: quote
---

# Docker 详细介绍

---
layout: quote
---

## Docker 详细介绍

- Docker 安装
- Docker 镜像：构建、拉取、发布
- Docker 容器：创建、管理、删除
- Dockerfile 编写与容器化应用
- Docker 常用命令
- Docker Compose 编写与容器编排

---
layout: quote
---

### Docker 安装

前面讲了好多有关容器技术的理论知识，接下来我们选择最常用的一款容器软件来实际操作一下吧。~~

打开我们之前安装的 Ubuntu 虚拟机，按下 `Ctrl + Alt + T` 打开终端，输入以下命令：

```bash
sudo apt update
sudo apt install docker.io
```

然后输入 `docker --version` 查看 docker 版本，如果有输出则说明安装成功。

---
layout: quote
---

### Docker 安装

Docker 镜像拉取网站在国内几乎无法访问，国内的镜像源也因为各种原因无法使用。

一种解决方法就是获取国际联网能力，这里就不多介绍。

还有一种解决方法就是配置第三方加速镜像源。

---
layout: quote
---

### Docker 安装

打开终端，输入以下命令：

```bash
sudo mkdir -p /etc/docker
vim /etc/docker/daemon.json
```

输入别人在网络上公开的镜像源地址，例如：

```json
{
  "registry-mirrors": ["https://docker.insmtr.cn"]
}
```

配置完成后，重启 docker 服务：

```bash
sudo systemctl daemon-reload
sudo systemctl restart docker
```

---
layout: quote
---

## Docker 详细介绍

- <span style="color: gray; opacity: 0.7;">Docker 安装</span>
- Docker 镜像：构建、拉取、发布
- Docker 容器：创建、管理、删除
- Dockerfile 编写与容器化应用
- Docker 常用命令
- Docker Compose 编写与容器编排

---
layout: quote
---

### Docker 镜像：构建、拉取、发布 & 容器：创建、管理、删除

配置好镜像源后，我们可以尝试拉取一个镜像试试：

```bash
docker pull ubuntu:20.04
```

拉取好镜像后，我们可以尝试运行一个容器：

```bash
docker run -it --name my-ubuntu ubuntu:20.04 /bin/bash
```

于是我们就进入了一个 ubuntu 容器中，可以在里面执行命令。你会发现，现在我们进入了一个与宿主机完全隔离的环境，并且极其精简。按下 `Ctrl + D` 可以退出容器。

---
layout: quote
---

### Docker 镜像：构建、拉取、发布 & 容器：创建、管理、删除

我们可以尝试在容器中安装一些软件，比如 vim：

```bash
docker exec -it --name my-ubuntu ubuntu:20.04
apt update
apt install vim
```

按下 `Ctrl + D` 退出容器，然后我们可以查看容器的变化：

```bash
docker diff my-ubuntu
```

我们也可以导出这个容器为一个镜像：

```bash
docker commit my-ubuntu my-ubuntu-vim
```

然后使用 `docker images` 查看镜像列表，可以看到我们刚刚导出的镜像。

---
layout: quote
---

### Docker 镜像：构建、拉取、发布 & 容器：创建、管理、删除

我们可以尝试删除容器，然后再用我们刚刚导出的镜像再创建一个新容器：

```bash
docker rm my-ubuntu
docker run -it --name my-ubuntu-vim my-ubuntu-vim
```

我们尝试在容器中执行 `vim` 命令，发现我们刚刚安装的 vim 已经存在。

按下 `Ctrl + D` 退出容器，然后我们也可以把这个镜像发布到 docker hub 上：

```bash
docker login
docker tag my-ubuntu-vim docker-id/my-ubuntu-vim
docker push docker-id/my-ubuntu-vim
```

---
layout: quote
---

### Docker 镜像：构建、拉取、发布 & 容器：创建、管理、删除

如果我们想要让容器可以直接读取宿主机的文件或与其它容器交换文件，可以使用 `-v` 参数挂载一个本地目录：

```bash
docker run -it --rm --name my-ubuntu-vim -v ./myFiles:/data my-ubuntu-vim
```

如果不想直接将目录挂载到容器中而只是想要让容器间共享文件，可以使用 `-v` 参数挂载一个存储卷：

```bash
docker run -it --rm --name my-ubuntu-vim -v my-volume:/data my-ubuntu-vim
```

我们可以使用 `docker volume ls` 查看存储卷列表，使用 `docker volume rm` 删除存储卷。

```bash
docker volume ls
docker volume rm my-volume
```

---
layout: quote
---

### Docker 镜像：构建、拉取、发布 & 容器：创建、管理、删除

如果我们想要让容器可以直接读取宿主机的网络，可以使用 `--network host` 参数：

```bash
docker run -it --rm --network host alpine
```

`--network` 参数表示容器的网络选项，可选值有 `bridge`、`none`、`container:其它容器名` 等。

---
layout: quote
---

### Docker 镜像：构建、拉取、发布 & 容器：创建、管理、删除

在 `--network host` 模式下，容器直接共享了宿主机的网络，在容器内启动的服务可以直接被外界发现并访问。但是在默认的 `bridge` 模式下，容器是一个独立的网络环境，需要通过端口映射才能访问。

我们可以使用 `-p` 参数指定端口映射：

```bash
docker run -it --rm -p 8080:80 nginx
```

---
layout: quote
---

## Docker 详细介绍

- <span style="color: gray; opacity: 0.7;">Docker 安装</span>
- <span style="color: gray; opacity: 0.7;">Docker 镜像：构建、拉取、发布</span>
- <span style="color: gray; opacity: 0.7;">Docker 容器：创建、管理、删除</span>
- Dockerfile 编写与容器化应用
- Docker 常用命令
- Docker Compose 编写与容器编排

---
layout: quote
---

### Dockerfile 编写与容器化应用

以上的过程，我们可以用一个更加优雅的方式来实现，那就是使用 Dockerfile。

Dockerfile 是一个文本文件，用来描述如何构建一个镜像。我们可以在 Dockerfile 中指定基础镜像、安装软件、配置环境等。

比如上面的例子中，我们可以创建一个 my-ubuntu-vim.dockerfile：

```dockerfile
FROM ubuntu:20.04
RUN apt-get update && apt-get install -y vim
```

然后使用 `docker build` 命令构建镜像：

```bash
docker build -t my-ubuntu-vim -f my-ubuntu-vim.dockerfile .
```

最后，我们运行一个容器验证一下：

```bash
docker run -it --rm my-ubuntu-vim
```

---
layout: quote
---

### Dockerfile 编写与容器化应用

Dockerfile 语法非常简单，但是功能非常强大。以下是一些常用的指令：

- `FROM`：指定基础镜像
- `RUN`：在镜像中执行命令
- `COPY`：从宿主机复制文件到镜像中
- `ADD`：从宿主机复制文件到镜像中，功能更高级，支持直接从 url 下载和自动解压 tar / tar.gz 文件
- `CMD`：指定容器启动时执行的命令，即默认命令
- `ENTRYPOINT`：指定容器启动时执行的命令，即默认命令
- `WORKDIR`：指定容器中的工作目录
- `EXPOSE`：指定容器监听的端口
- `VOLUME`：指定容器中的挂载点
- `ENV`：设置环境变量
- `ARG`：设置构建时的参数
- `USER`：指定容器中的用户

---
layout: quote
---

### Dockerfile 编写与容器化应用

其中，CMD 和 ENTRYPOINT 的区别在于，CMD 可以被 docker run 后面的命令覆盖，而 ENTRYPOINT 不可以除非 docker run 时显式指定了 `--entrypoint` 。命令行参数会被追加到 ENTRYPOINT 后面。并且，如果同时指定了 CMD 和 ENTRYPOINT，CMD 会被当作 ENTRYPOINT 的默认参数。

举个例子，这里有一个 Dockerfile：

```dockerfile
FROM ubuntu:20.04
CMD ["world", "docker"]
ENTRYPOINT ["echo", "hello"]
```

然后我们构建并运行：

```bash
docker build -t hello-docker .
docker run hello-docker abc
```

于是你就会看到输出 `hello abc`。

---
layout: quote
---

### Dockerfile 编写与容器化应用

举个复杂一点的例子，这是一个 code-server 的 Dockerfile：

```dockerfile
FROM ubuntu:20.04
ENV DEBIAN_FRONTEND=noninteractive
ADD local.tar.gz /root/
RUN apt-get update && \
    apt-get install -y curl build-essential gdb python3 && \
    curl -fsSL https://code-server.dev/install.sh | sh && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
CMD ["code-server"]
```

这个 Dockerfile 会在 ubuntu 20.04 的基础上安装 curl、build-essential、gdb、python3，并且下载安装 code-server。最后，启动 code-server。

```bash
docker build -t code-server .
docker run -it -p 8080:8080 code-server
```

打开浏览器，访问 `http://localhost:8080` ，你就可以看到 code-server 的界面了。

---
layout: quote
---

## Docker 详细介绍

- <span style="color: gray; opacity: 0.7;">Docker 安装</span>
- <span style="color: gray; opacity: 0.7;">Docker 镜像：构建、拉取、发布</span>
- <span style="color: gray; opacity: 0.7;">Docker 容器：创建、管理、删除</span>
- <span style="color: gray; opacity: 0.7;">Dockerfile 编写与容器化应用</span>
- Docker 常用命令
- Docker Compose 编写与容器编排

---
layout: quote
---

### Docker 常用命令

现在我们已经学会了如何构建镜像、运行容器、编写 Dockerfile，接下来我们来学习一下 Docker 的常用命令。

- `docker images`：查看镜像列表
- `docker ps`：查看容器列表
- `docker run`：运行一个容器
- `docker exec`：在容器中执行命令
- `docker stop`：停止一个容器
- `docker start`：启动一个容器
- `docker rm`：删除一个容器
- `docker rmi`：删除一个镜像
- `docker build`：构建一个镜像
- `docker commit`：导出一个容器为镜像
- `docker login`：登录 docker hub
- `docker tag`：给镜像打标签

---
layout: quote
---

### Docker 常用命令

现在我们已经学会了如何构建镜像、运行容器、编写 Dockerfile，接下来我们来学习一下 Docker 的常用命令。

- `docker push`：发布一个镜像
- `docker pull`：拉取一个镜像
- `docker diff`：查看容器的变化
- `docker logs`：查看容器的日志
- `docker cp`：从容器中复制文件到宿主机
- `docker inspect`：查看容器的详细信息

---
layout: quote
---

### Docker 常用命令

命令有些多，我们把他们放在一个例子里面，这样你就可以一次性学会了（：

```bash
docker pull ubuntu:20.04
docker images
docker rmi ubuntu:20.04
docker images
docker pull busybox
docker run -id --name my-busybox busybox
docker ps -a
docker inspect my-busybox
docker logs -f my-busybox
docker exec -it my-busybox /bin/sh
docker stop my-busybox
docker ps -a
docker rm my-busybox
docker ps -a
```

---
layout: quote
---

## Docker 详细介绍

- <span style="color: gray; opacity: 0.7;">Docker 安装</span>
- <span style="color: gray; opacity: 0.7;">Docker 镜像：构建、拉取、发布</span>
- <span style="color: gray; opacity: 0.7;">Docker 容器：创建、管理、删除</span>
- <span style="color: gray; opacity: 0.7;">Dockerfile 编写与容器化应用</span>
- <span style="color: gray; opacity: 0.7;">Docker 常用命令</span>
- Docker Compose 编写与容器编排

---
layout: quote
---

### Docker Compose 编写与容器编排

Docker Compose 是一个用来定义和一键运行多容器 Docker 应用的工具。

Docker Compose 的配置文件是一个 YAML 文件，我们可以在这个文件中定义多个服务，每个服务可以包含多个容器。

---
layout: quote
---

### Docker Compose 编写与容器编排

举个例子，这是一个使用 Docker Compose 配置的 WordPress 服务：

```yaml
version: '3.3'
services:
  db:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: somewordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
  wordpress:
    image: wordpress:latest
    ports:
      - "8000:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
```

---
layout: quote
---

### Docker Compose 编写与容器编排

我们可以使用 `docker-compose up` 命令一键启动这个服务：

```bash
docker-compose up
```

然后我们就可以访问 `http://localhost:8000` 来访问 WordPress 了。

---
layout: quote
---

# 容器编排与 Kubernetes

---
layout: quote
---

## 容器编排与 Kubernetes

- Kubernetes 简介

---
layout: quote
---

### Kubernetes 简介

Kubernetes 是一个开源的容器编排引擎，用于自动化部署、扩展和管理容器化应用程序。Kubernetes 基于 Google 内部的 Borg 系统，是 CNCF 的一个重要项目。

Kubernetes 提供了一种容器编排的解决方案，可以让用户在多个节点上运行容器，并且可以自动扩展、负载均衡、服务发现等。

Pod 是 Kubernetes 的最小调度单位，Pod 中可以包含一个或多个容器。

---
layout: quote
---

## 参考资料

- [容器 (虚拟化)](https://zh.wikipedia.org/wiki/%E5%AE%B9%E5%99%A8_(%E8%99%9A%E6%8B%9F%E5%8C%96)/)

---
layout: end
---

Thanks!
