# Docker 是什么？

1. Docker 本身并不是容器，它是创建容器的工具，是应用容器引擎。

## 虚拟机工作流程

1. 虚拟机是利用 Hypervisor 虚拟化 CPU、内存、IO 等设备;
2. 在虚拟出来的机器上面安装操作系统。

## Docker 工作流程

1. Docker 是利用 Linux 内核本身支持的容器方式(进程，共享操作系统内核)，实现了资源和环境的隔离，简单来说，Docker 就是利用 Namespace 实现了系统环境的隔离，利用了 cgroup 实现了资源的限制，利用镜像实例实现跟环境的隔离。
2. docker 容器共享操作系统内核
3. docker 是无状态的，每次重启都是一个新的容器。
4. docker 容器内的 Linux 发行版不需要跟 docker 主机上运行的 Linux 发行版匹配。但是，Linux 容器要求 dockers 主机运行 Linux 内核。例如 Linux 容器不能直接在 window docker 主机上运行。window 容器也是如此。

<img src='https://pic1.zhimg.com/80/v2-e30ef11428ac454d101f2b7d3ffc067e_720w.jpg?source=1940ef5c'>
<img src='https://pic3.zhimg.com/80/v2-c2a31e2008835b2974170ad1dbac0d42_720w.jpg?source=1940ef5c'>

# Docker 基础

## 存储驱动

1. linux: AUFS（最原始也是最老的）、Overlay2（可能是未来的最佳选择）、Device Mapper、Btrfs 和 ZFS。

2. windows: Windows Filter
   > 每个 Docker 主机只能选择一种存储驱动，而不能为每个容器选择不同的存储驱动。

### 修改存储类型

在 Linux 上，读者可以通过修改 /etc/docker/daemon.json 文件来修改存储引擎配置，修改完成之后需要重启 Docker 才能够生效。

```json
{ "storage-driver": "overlay2" },
```

> 1. 注意：如果读者修改了正在运行 Docker 主机的存储引擎类型，则现有的镜像和容器在重启之后将不可用，这是因为每种存储驱动在主机上存储镜像层的位置是不同的（通常在 /var/lib/docker/ <storage-driver>/... 目录下）。
> 2. 修改了存储驱动的类型，Docker 就无法找到原有的镜像和容器了。切换到原来的存储驱动，之前的镜像和容器就可以继续使用了。
> 3. 如果希望在切换存储引擎之后还能够继续使用之前的镜像和容器，需要将镜像保存为 Docker 格式，上传到某个镜像仓库，修改本地 Docker 存储引擎并重启，之后从镜像仓库将镜像拉取到本地，最后重启容器。

## 镜像

1. 镜像由多个层组成，每层叠加之后，从外部看来就如一个独立的对象。镜像内部是一个精简的操作系统（OS），同时还包含应用运行所必须的文件和依赖包。
2. 镜像中还不包含内核——容器都是共享所在 Docker 主机的内核。所以有时会说容器仅包含必要的操作系统（通常只有操作系统文件和文件系统对象）。

### 镜像和容器

一旦容器从镜像启动后，二者之间就变成了互相依赖的关系，并且在镜像上启动的容器全部停止之前，镜像是无法被删除的。尝试删除镜像而不停止或销毁使用它的容器，会导致出错。

### 镜像仓库服务

一个镜像仓库服务包含多个镜像仓库（Image Repository）。其中一个镜像仓库中可以包含多个镜像。

### 镜像命名和标签

1. 只需要给出镜像的名字和标签，就能在官方仓库中定位一个镜像（采用“:”分隔）。
2. 从非官方仓库拉取镜像也是类似的，读者只需要在仓库名称面前加上 Docker Hub 的用户名或者组织名称。

```sh
docker image pull microsoft/powershell:nanoserver
```

### 镜像和分层

所有的 Docker 镜像都起始于一个基础镜像层，当进行修改或增加新的内容时，就会在当前镜像层之上，创建新的镜像层。

### 共享镜像层

多个镜像之间可以并且确实会共享镜像层。这样可以有效节省空间并提升性能。

### 根据摘要拉取镜像

#### 为什么要根据摘要拉取镜像？

1. 标签是可变的
2. 原镜像被覆盖后，没办法区分镜像的版本

#### 摘要（散列值）

基于内容（内容的变更一定会导致散列值的改变）

### 镜像散列值（摘要）

## 容器

## 仓库

# Docker 引擎

Docker 引擎是用来运行和管理容器的核心软件。通常人们会简单地将其代指为 Docker 或 Docker 平台。

Docker 引擎由如下主要的组件构成：Docker 客户端（Docker Client）、Docker 守护进程（Docker daemon）、containerd 以及 runc。它们共同负责容器的创建和运行。

## 第一版

1. Docker daemon 是单一的二进制文件，包含诸如 Docker 客户端、Docker API、容器运行时、镜像构建等。

2. LXC 提供了对诸如命名空间（Namespace）和控制组（CGroup）等基础工具的操作能力，它们是基于 Linux 内核的容器虚拟化技术。

## 第二版（小而专的工具可以组装为大型工具）

1. runc 实质上是一个轻量级的、针对 Libcontainer 进行了包装的命令行交互工具（Libcontainer 取代了早期 Docker 架构中的 LXC）。
   > 有时也将 runc 所在的那一层称为“OCI 层”
2. containerd 最初被设计为轻量级的小型工具，仅用于容器的生命周期管理。然而，随着时间的推移，它被赋予了更多的功能，比如镜像管理。
3. Docker daemon 拆解后主要负责 API 和其他特性

## 容器启动过程

1. 当使用 Docker 命令行工具执行如上命令时，Docker 客户端会将其转换为合适的 API 格式，并发送到正确的 API 端点。
2. API 是在 daemon 中实现的。一旦 daemon 接收到创建新容器的命令，它就会向 containerd 发出调用。
   > 1. daemon 已经不再包含任何创建容器的代码了！
   > 2. daemon 使用一种 CRUD 风格的 API，通过 gRPC 与 containerd 进行通信。
   > 3. daemon 的主要功能包括镜像管理、镜像构建、REST API、身份验证、安全、核心网络以及编排。
3. containerd 将 Docker 镜像转换为 OCI bundle，并让 runc 基于此创建一个新的容器。
   > 虽然名叫 containerd，但是它并不负责创建容器，而是指挥 runc 去做。
4. runc 与操作系统内核接口进行通信，基于所有必要的工具（Namespace、CGroup 等）来创建容器。容器进程作为 runc 的子进程启动，启动完毕后，runc 将会退出。一旦容器进程的父进程 runc 退出，相关联的 containerd-shim 进程就会成为容器的父进程。
   <img src='http://c.biancheng.net/uploads/allimg/190416/4-1Z4161413112O.gif'/>

## 该模型的显著优势

容器运行时与 Docker daemon 解耦,对 Docker daemon 的维护和升级工作不会影响到运行中的容器。

在旧模型中，所有容器运行时的逻辑都在 daemon 中实现，启动和停止 daemon 会导致宿主机上所有运行中的容器被杀掉。

这在生产环境中是一个大问题——想一想新版 Docker 的发布频次吧！每次 daemon 的升级都会杀掉宿主机上所有的容器，这太糟了！

## shim

shim 是实现无 daemon 的容器不可或缺的工具。（用于将运行中的容器与 daemon 解耦，以便进行 daemon 升级等操作）
作为容器的父进程，shim 的部分职责如下。

1. 保持所有 STDIN 和 STDOUT 流是开启状态，从而当 daemon 重启的时候，容器不会因为管道（pipe）的关闭而终止。
2. 将容器的退出状态反馈给 daemon。

## 应用容器化

当成功将应用代码构建到了 Docker 镜像当中，然后以容器的方式启动该镜像，这个过程叫作“应用容器化”
