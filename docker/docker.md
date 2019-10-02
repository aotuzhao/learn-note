

### 一 简介

#### 1. Docker出现的原因？

一款产品从开发到上线，从操作系统，到运行环境，再到应用配置。作为开发+运维之间的协作我们需要关心很多东西，这也是很多互联网公司都不得不面对的问题，特别是各种版本的迭代之后，不同版本环境的兼容，对运维人员都是考验
Docker之所以发展如此迅速，也是因为它对此给出了一个标准化的解决方案。
环境配置如此麻烦，换一台机器，就要重来一次，费力费时。很多人想到，能不能从根本上解决问题，软件可以带环境安装？也就是说，安装的时候，把原始环境一模一样地复制过来。开发人员利用 Docker 可以消除协作编码时“在我的机器上可正常工作”的问题。

<img src="D:\learn-note\docker\图像.bmp" style="zoom:80%;" />

传统上认为，软件编码开发/测试结束后，所产出的成果即是程序或是能够编译执行的二进制字节码等(java为例)。而为了让这些程序可以顺利执行，开发团队也得准备完整的部署文件，让维运团队得以部署应用程式，开发需要清楚的告诉运维部署团队，用的全部配置文件+所有软件环境。不过，即便如此，仍然常常发生部署失败的状况。**Docker镜像的设计，使得Docker得以打破过去「程序即应用」的观念。透过镜像(images)将作业系统核心除外，运作应用程式所需要的系统环境，由下而上打包，达到应用程式跨平台间的无缝接轨运作。**

#### 2. Docker理念

Docker是基于Go语言实现的云开源项目。

Docker的主要目标是“Build，Ship and Run Any App,Anywhere”，也就是通过对应用组件的封装、分发、部署、运行等生命周期的管理，使用户的APP（可以是一个WEB应用或数据库应用等等）及其运行环境能够做到“**一次封装，到处运行**”。

<img src="D:\learn-note\docker\图像 (2).bmp" style="zoom: 80%;" />

**Linux 容器技术**的出现就解决了这样一个问题，而 Docker 就是在它的基础上发展过来的。将应用运行在 Docker 容器上面，而 Docker 容器在任何操作系统上都是一致的，这就实现了跨平台、跨服务器。只需要一次配置好环境，换到别的机子上就可以一键部署好，大大简化了操作。

解决了运行环境和配置问题软件容器，方便做持续集成并有助于整体发布的容器虚拟化技术。

Docker的优点：

- **1、简化程序：**
  Docker 让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的 Linux 机器上，便可以实现虚拟化。Docker改变了虚拟化的方式，使开发者可以直接将自己的成果放入Docker中进行管理。方便快捷已经是 Docker的最大优势，过去需要用数天乃至数周的 任务，在Docker容器的处理下，只需要数秒就能完成。
- **2、避免选择恐惧症：**
  如果你有选择恐惧症，还是资深患者。那么你可以使用 Docker 打包你的纠结！比如 Docker 镜像；Docker 镜像中包含了运行环境和配置，所以 Docker 可以简化部署多种应用实例工作。比如 Web 应用、后台应用、数据库应用、大数据应用比如 Hadoop 集群、消息队列等等都可以打包成一个镜像部署。
- **3、节省开支：**
  一方面，云计算时代到来，使开发者不必为了追求效果而配置高额的硬件，Docker 改变了高性能必然高价格的思维定势。Docker 与云的结合，让云空间得到更充分的利用。不仅解决了硬件管理的问题，也改变了虚拟化的方式。

#### 3. 虚拟化技术

**虚拟机**

<img src="D:\learn-note\docker\图像 (3).bmp" style="zoom: 67%;" />

虚拟机（virtual machine）就是带环境安装的一种解决方案。

它可以在一种操作系统里面运行另一种操作系统，比如在Windows 系统里面运行Linux 系统。应用程序对此毫无感知，因为虚拟机看上去跟真实系统一模一样，而对于底层系统来说，虚拟机就是一个普通文件，不需要了就删掉，对其他部分毫无影响。这类虚拟机完美的运行了另一套系统，能够使应用程序，操作系统和硬件三者之间的逻辑不变。

**容器虚拟化**

<img src="D:\learn-note\docker\图像 (4).bmp" style="zoom:67%;" />

Linux 发展出了另一种虚拟化技术：Linux 容器（Linux Containers，缩写为 LXC）。
Linux 容器不是模拟一个完整的操作系统，而是对进程进行隔离。有了容器，就可以将软件运行所需的所有资源打包到一个隔离的容器中。容器与虚拟机不同，不需要捆绑一整套操作系统，只需要软件工作所需的库资源和设置。系统因此而变得高效轻量并保证部署在任何环境中的软件都能始终如一地运行。

> 容器虚拟化是将应用及应用所需依赖打包为一个容器，多个容器共享宿主机内核。不包括宿主机系统，而虚拟机技术，需要将宿主机的硬件资源进行虚拟，并虚拟操作系统。
>
> Docker是内核级虚拟化，其不像传统的虚拟化技术一样需要额外的Hypervisor支持，所以在一台物理机上可以运行很多个容器实例，可大大提升物理服务器的CPU和内存的利用率。

Kernel 就是连接操作系统(OS)和硬件(Hardware)的一个中间组件。



### 二 安装

#### 1. Docker 架构

<img src="D:\learn-note\docker\微信截图_20190918143423.png" style="zoom:50%;" />



| **Images**（镜像）     | 用于创建容器的只读模板                                       |
| ---------------------- | ------------------------------------------------------------ |
| **Container （容器）** | 容器是独立运行的一个或一组应用，**容器是镜像创建的运行实例**。 |
| **Registry(仓库)**     | 用来保存镜像，类似GitHub                                     |
| Docker Machine         | Docker Machine是一个简化Docker安装的命令行工具，通过一个简单的命令行即可在相应的平台上安装Docker，比如VirtualBox。 |
| Client                 | Docker 客户端通过命令行或者其他工具使用 Docker API与 Docker 的守护进程通信。 |
| Host                   | 一个物理或者虚拟的机器用于执行 Docker 守护进程和容器。       |

Docker三要素：镜像、容器、仓库

镜像是创建容器的模板。容器是镜像的运行实例。

容器可以被启动、开始、停止、删除。每个容器都是相互隔离的、保证安全的平台。可以把容器看做是一个简易版的 Linux 环境（包括root用户权限、进程空间、用户空间和网络空间等）和运行在其中的应用程序。器的定义和镜像几乎一模一样，也是一堆层的统一视角，唯一区别在于容器的最上面那一层是可读可写的。

仓库（Repository）是集中存放镜像文件的场所。仓库（Repository）是集中存放镜像文件的场所。
仓库(Repository)和仓库注册服务器（Registry）是有区别的。仓库注册服务器上往往存放着多个仓库，每个仓库中又包含了多个镜像，每个镜像有不同的标签（tag）。

仓库分为公开仓库（Public）和私有仓库（Private）两种形式。最大的公开仓库是 Docker Hub，
存放了数量庞大的镜像供用户下载。国内的公开仓库包括阿里云 、网易云 等。

> Docker 本身是一个容器运行载体或称之为管理引擎。我们把应用程序和配置依赖打包好形成一个可交付的运行环境，这个打包好的运行环境就似乎 image镜像文件。只有通过这个镜像文件才能生成 Docker 容器。image 文件可以看作是容器的模板。Docker 根据 image 文件生成容器的实例。同一个 image 文件，可以生成多个同时运行的容器实例。
>
> *  image 文件生成的容器实例，本身也是一个文件，称为镜像文件。
> *  一个容器运行一种服务，当我们需要的时候，就可以通过docker客户端创建一个对应的运行实例，也就是我们的容器
> * 至于仓储，就是放了一堆镜像的地方，我们可以把镜像发布到仓储中，需要的时候从仓储中拉下来就可以了。

#### 2. 安装

安装：https://www.runoob.com/docker/centos-docker-install.html

```shell
移除旧的版本
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine
                  
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
sudo yum makecache fast
sudo yum -y install docker-ce
sudo systemctl start docker
 docker run hello-world
 
 删除
 $ sudo yum remove docker-ce
$ sudo rm -rf /var/lib/docker
```



配置镜像加速：

```json
{
  "registry-mirrors": ["http://hub-mirror.c.163.com","http://hub-mirror.c.163.com","https://registry.docker-cn.com"]
}
```

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\1568801210318.png" alt="1568801210318" style="zoom:50%;" />

#### 3. 底层原理

Docker是怎么工作的？

Docker是一个Client-Server结构的系统，Docker守护进程运行在主机上， 然后通过Socket连接从客户端访问，守护进程从客户端接受命令并管理运行在主机上的容器。 容器，是一个运行时环境，就是我们前面说到的集装箱。

<img src="D:\learn-note\docker\图像 (5).bmp" style="zoom:50%;" />

为什么Docker比VM快？

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\1568801736356.png" alt="1568801736356" style="zoom:50%;" />

(1)docker有着比虚拟机更少的抽象层。由亍docker不需要Hypervisor实现硬件资源虚拟化,运行在docker容器上的程序直接使用的都是实际物理机的硬件资源。因此在CPU、内存利用率上docker将会在效率上有明显优势。

(2)docker利用的是宿主机的内核,而不需要Guest OS。因此,当新建一个容器时,docker不需要和虚拟机一样重新加载一个操作系统内核。仍而避免引寻、加载操作系统内核返个比较费时费资源的过程,当新建一个虚拟机时,虚拟机软件需要加载Guest OS,返个新建过程是分钟级别的。而docker由于直接利用宿主机的操作系统,则省略了返个过程,因此新建一个docker容器只需要几秒钟。

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\1568801776015.png" alt="1568801776015" style="zoom:50%;" />



### 三 Docker命令

#### 1. 帮助命令

```shell
docker version  查看docker版本信息
docker info 查看docker信息
docker --help 查看帮助命令
```

#### 2. 镜像命令

```shell
docker images 列出本地镜像
	-a :列出本地的所有镜像，含中间影响层
	-q :只列出镜像的ID
	--digests:显示镜像的摘要信息
	--no-trunc:显示完整的镜像信息
	
docker search 镜像名字
	--no-trunc :显示完整镜像描述
	--filter=stars=70：列出收藏数不小于指定值的镜像(-s 被替换)
	--filter=is-automated=true:只列出automated build 类型的镜像（--automated 被替换）
docker pull 镜像名字
	下载镜像，默认tag=latest
	镜像名字[:TAG]
docker rmi 
		rmi 镜像ID
		docker rmi -f $(docker images -qa）删除全部镜像
```

#### 3. 容器命令

```shell
# 新建启动容器
docker run [options] image [命令][参数] 
	【options】 常用的
		-i 以交互模式运行容器，通常与 -t 联合使用
		-t 为容器重新分配一个伪终端
		--name='容器的名字' 为容器指定一个名字
		-d :后台运行容器，并返回容器ID，即启动守护式容器
		-P:随机端口映射
		-p:指定端口映射，【ip:hostPort:containerPort】【ip::containerPort】

#使用镜像centos:latest以交互模式启动一个容器,在容器内执行/bin/bash命令。
docker run -it centos /bin/bash 			

# 列出正在运行的容器
docker ps [options]
	-a 列出当前所有正在运行的容器+历史运行过的容器
	-l 列出最近创建的容器
	-n 显示最近n个创建的容器
	-q 静默模式，只显示容器编号
	--no-trunc 不截断输出

# 退出容器
exit 容器停止退出
ctrl+P+Q 

# 启动容器
docker start 容器ID 或 容器名
docker stop 容器id/容器名
docker restart 容器名/容器id
docker kill 容器id/容器名 强制停止容器

# 删除容器
docker rm 容器id
docker rm -f $(docker ps -a -q)

```

容器高级命令

```shell
#以守护方式启动进程
docker run -d 容器名
#查看容器日志
docker logs -tf -tail 数字  容器id 
#查看容器内部细节
docker inspect 容器id
#进入正在运行的容器，进行命令交互
docker attach 容器id
#从外部与正在运行的容器交互，命令结果直接输出在宿主机上
docker exec -it 容器id 命令
#从容器内拷贝文件到主机上
docker cp 容器id:容器内路径 目的主机路径

```

<img src="D:\learn-note\docker\图像6.bmp"  />

```shell
attach    Attach to a running container                 # 当前 shell 下 attach 连接指定运行镜像
build     Build an image from a Dockerfile              # 通过 Dockerfile 定制镜像
commit    Create a new image from a container changes   # 提交当前容器为新的镜像
cp        Copy files/folders from the containers filesystem to the host path   #从容器中拷贝指定文件或者目录到宿主机中
create    Create a new container                        # 创建一个新的容器，同 run，但不启动容器
diff      Inspect changes on a container's filesystem   # 查看 docker 容器变化
events    Get real time events from the server          # 从 docker 服务获取容器实时事件
exec      Run a command in an existing container        # 在已存在的容器上运行命令
export    Stream the contents of a container as a tar archive   # 导出容器的内容流作为一个 tar 归档文件[对应 import ]
history   Show the history of an image                  # 展示一个镜像形成历史
images    List images                                   # 列出系统当前镜像
import    Create a new filesystem image from the contents of a tarball # 从tar包中的内容创建一个新的文件系统映像[对应export]
info      Display system-wide information               # 显示系统相关信息
inspect   Return low-level information on a container   # 查看容器详细信息
kill      Kill a running container                      # kill 指定 docker 容器
load      Load an image from a tar archive              # 从一个 tar 包中加载一个镜像[对应 save]
login     Register or Login to the docker registry server    # 注册或者登陆一个 docker 源服务器
logout    Log out from a Docker registry server          # 从当前 Docker registry 退出
logs      Fetch the logs of a container                 # 输出当前容器日志信息
port      Lookup the public-facing port which is NAT-ed to PRIVATE_PORT    # 查看映射端口对应的容器内部源端口
pause     Pause all processes within a container        # 暂停容器
ps        List containers                               # 列出容器列表
pull      Pull an image or a repository from the docker registry server   # 从docker镜像源服务器拉取指定镜像或者库镜像
push      Push an image or a repository to the docker registry server    # 推送指定镜像或者库镜像至docker源服务器
restart   Restart a running container                   # 重启运行的容器
rm        Remove one or more containers                 # 移除一个或者多个容器
rmi       Remove one or more images             # 移除一个或多个镜像[无容器使用该镜像才可删除，否则需删除相关容器才可继续或 -f 强制删除]
run       Run a command in a new container              # 创建一个新的容器并运行一个命令
save      Save an image to a tar archive                # 保存一个镜像为一个 tar 包[对应 load]
search    Search for an image on the Docker Hub         # 在 docker hub 中搜索镜像
start     Start a stopped containers                    # 启动容器
stop      Stop a running containers                     # 停止容器
tag       Tag an image into a repository                # 给源中镜像打标签
top       Lookup the running processes of a container   # 查看容器中运行的进程信息
unpause   Unpause a paused container                    # 取消暂停容器
version   Show the docker version information           # 查看 docker 版本号
wait      Block until a container stops, then print its exit code   # 截取容器停止时的退出状态值
 
```



### 四 镜像解析

#### 1. 简介

Docker镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境的软件，它包括运行某个软件所需的所有内容，包括代码、运行时、库、环境变量、配置文件。

#### 2. UnionFS (联合文件系统)

UnionFS(联合文件系统)是一种**分层、轻量级并且高性能**的文件系统，它**支持对文件系统的修改作为一次提交来一层层的叠加**，同时可以将不同目录挂载到同一虚拟文件系统。UnionFS是Docker镜像的基础。镜像可以通过分层进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。

特性： 一次同时加载多个文件系统，但从外面只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录。

#### 3. Docker镜像加载原理

Docker的镜像实际上是由一层层的文件系统组成，这些文件系统就是联合文件系统。

![](D:\learn-note\docker\图像7.bmp)

bootfs：boot file system 主要包含bootloader和kernel，bootloader主要是加载kernel，Linux刚启动时会加载bootfs文件系统，在Docker镜像的底层是bootfs。这一层与我们典型的Linux、Unix系统一样，包含boot加载器和内核。当boot加载完成之后，整个内核就都在内存中了，此时内存的使用权已由bootfs交给内核，系统会卸载bootfs。

rootfs：root file system 在bootfs之上，包含的是典型Linux系统中的 /dev/proc./bin./etc等标准目录和文件。rootfs就是各种不同的操作系统发行版，比如Ubuntu，Centos，等

对于一个精简的OS，rootfs可以很小，只需要包含基本的命令、工具和程序就可以了。因为底层直接使用Host的Kernel，自己只需要提供rootfs就行了。由此可见对于不同的Linux发行版，bootfs基本是一致的，rootfs会有差别，因此不同的发行版可以共用bootfs。

Docker镜像是分层的。分层最大的好处就是共享资源。不同的镜像都从相同的base镜像构建而来，那么宿主机只需在磁盘上保存一份base镜像，同时内存中也只需加载一份base镜像，就可以为所有容器服务了。而且镜像的每一层都可以被共享。

Docker镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部。这一层通常被称为容器层，容器层之下都叫镜像层。

#### 4. commit

```shell
docker commit 提交一个正在运行的容器副本，作为新的镜像
docker commit -m="描述信息" -a="作者" 容器ID 命名空间/镜像名称:标签
docker commit -m="my tomcat" -a="zxz" werw24sf zhaoxue/tomcat2:1.1
```



### 五 Docker容器数据卷

#### 1. 数据卷简介

类似Redis中的RDB和AOF 文件，对容器数据进行持久化。

Docker容器产生的数据，如果不通过docker commit 生成新的镜像，使得数据作为镜像的一部分保存下来，那么当容器删除之后，数据自然也就没了。docker容器数据卷就是为了能保存数据在Docker容器中。

卷就是目录或文件，存在于一个或多个容器中，由Docker挂载到容器，但不属于联合文件系统，因为能绕过UnionFs提供一些用于持续存储或共享数据的特性：

特点：

- 数据卷可在容器之间共享或重用数据
- 卷中的更改可直接生效
- 数据卷中的更改不会包含在镜像的更新中
- 数据卷的生命周期一直持续到没有容器使用它为止



#### 2. 数据卷的使用

数据卷在容器添加有两种方式

- 直接命令添加
- DockerFile添加

##### 命令添加



```shell
# docker volume 
docker vokume 
	create 创建一个数据卷
	inspect 显示一个或多个卷的详细信息
	ls 列出所有数据卷
	prune 移除所有未使用的本地数据卷
	rm 移除一个或多个数据卷
# -v 绑定一个数据卷
docker run -it -v /宿主机目录:/容器内目录 镜像名

# 查看是否挂载成功 Volumes/VolumesRW
docker inspect 容器id

```

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\1569208957084.png" alt="1569208957084" style="zoom:50%;" />

容器和宿主机之间共享数据；容器退出后，主机修改后数据同步；

```shell
# 加入权限 ro意思是只读
docker run -it -v /宿主机绝对路径目录:/容器内目录:ro 镜像名
```

##### DockerFile 添加

```shell
VOLUME["/dataVolumeContainer","/dataVolumeContainer2","/dataVolumeContainer3"]
```

说明：

出于可移植和分享的考虑，用-v 主机目录:容器目录这种方法不能够直接在Dockerfile中实现。
由于宿主机目录是依赖于特定宿主机的，并不能够保证在所有的宿主机上都存在这样的特定目录。



> Docker挂载主机目录Docker访问出现cannot open directory .: Permission denied
>
> 解决办法：在挂载目录后多加一个--privileged=true参数即可

#### 3. 数据卷容器

命名的容器挂载数据卷，其它容器通过挂载这个（父容器）实现数据共享，挂载数据卷的容器，成为数据卷容器。

容器之间配置信息的传递，数据卷的生命周期一直持续到没有容器使用它为止。



### 六 DockerFile

#### 1. DockerFile基础

DockerFile是用来构建Docker镜像的构建文件，是由一系列命令和参数构成的脚本。、

编写dockerfile---> docker build --> docker run

Docker File 基础知识

- 每条保留字指令都必须为大写字母且后面要跟随至少一个参数
- 指令按照从上到下顺序执行
- #表示注释
- 每条指令都会创建一个新的镜像层，并对镜像进行提交

Docker执行DockerFile的大致流程：

1. docker从基础镜像运行一个容器
2. 执行一条指令并对容器做出修改
3. 执行类似docker commit 的操作提交一个新的镜像层
4. docker基于提交的镜像运行一个新容器
5. 执行dockerfile中的下一条指令直到所有指令都执行完成

**DockerFile、Docker镜像、Docker容器的关系：**

DockerFile 是原材料，Docker镜像是交付成品，Docker容器则是运行的实例

DockerFile面向开发，Docker镜像成为交付标准，Docker容器则涉及部署与运维，三者缺一不可，合力成为Docker体系的基石。

![](D:\learn-note\docker\图像8.bmp)

**DockerFile**：定义了进程需要的一切东西。DockerFile涉及的内容包括执行代码或者是文件、环境变量、依赖包、运行时环境、服务进程和内核进程等；

**Docker镜像**：在Dockerfile定义一个文件之后，docker build时会产生一个Docker镜像，当运行Docker镜像时，会真正开始提供服务；

**Docker容器**：容器是直接提供服务

#### 2. DockerFile结构解析

```shell
# centos 7 dockerfile
FROM scratch
ADD centos-7-x86_64-docker.tar.xz /

LABEL org.label-schema.schema-version="1.0" \
    org.label-schema.name="CentOS Base Image" \
    org.label-schema.vendor="CentOS" \
    org.label-schema.license="GPLv2" \
    org.label-schema.build-date="20190801"

CMD ["/bin/bash"]
```

```shell
# tomcat8 dockerfile 省略
FROM openjdk:8-jdk

ENV CATALINA_HOME /usr/local/tomcat
ENV PATH $CATALINA_HOME/bin:$PATH
RUN mkdir -p "$CATALINA_HOME"
WORKDIR $CATALINA_HOME

RUN xxxxxxxxx
RUN xxxxxxxxx

EXPOSE 8080
CMD ["catalina.sh", "run"]
```



**FROM**:基础镜像，当前镜像是基于哪个镜像的,scratch是base镜像，类似Object类

**MAINTAINER**: 镜像维护者的姓名和邮箱地址，xxnamexx '<'xxx@sss.com' >'

**RUN**:容器构建时需要运行的命令

**EXPOSE**:当前容器暴露的端口

**WORKDIR**:指定在创建容器后，终端登录默认进入的工作目录

**ENV**:用来在构建镜像过程中设置环境变量，设置的变量可以通过  $xxx 进行引用

**ADD**:将宿主机的文件拷贝到容器，add命令会自动处理压缩文件和url

**COPY**:类似ADD，拷贝文件到宿主机下，COPY ["src","dest"]

**VOLUME**:容器数据卷，用于共享数据

**CMD**:指定一个容器启动时要运行的命令

> CMD 指令的格式和 RUN 相似，也是两种格式
>
> - shell 格式  :  CMD <命令>
> - exec 格式：CMD ["可执行文件"，"参数1","参数2"...]
> - 参数列表格式：CMD["参数1"，“参数2”。。。。]，在指定了ENTRYPOINT 指令之后，用来指定参数
> - DockerFile中可以有多个CMD指令，但只有最后一个生效，CMD会被docker run 之后的参数替换

**ENTRYPOINT**:指定一个容器启动时要运行的命令，和CMD一样，都是在容器启动时执行部分命令

**ONBUILD**:当构建一个被继承的DockerFile时运行命令，父镜像在被子继承后父镜像的ONBIILD被触发



![](D:\learn-note\docker\图像 (6).bmp)



#### 3. Dockerfile 补充

Docker Hub中99%的镜像都是通过base镜像安装和配置需要的软件构建出来的

<img src="D:\learn-note\docker\图像9.bmp" style="zoom:80%;" />

常用镜像安装略

###  七 Docker镜像发布

<img src="D:\learn-note\docker\图像 (7).bmp" style="zoom:80%;" />

镜像生成：通过Dockerfile构建镜像或者通过已有镜像的容器commit

推送镜像：详见阿里云操作手册

```shell
1. 登录阿里云Docker Registry
$ sudo docker login --username=3097594492@qq.com registry.cn-beijing.aliyuncs.com
用于登录的用户名为阿里云账号全名，密码为开通服务时设置的密码。

您可以在产品控制台首页修改登录密码。

2. 从Registry中拉取镜像
$ sudo docker pull registry.cn-beijing.aliyuncs.com/zxzhao/mycentos:[镜像版本号]
3. 将镜像推送到Registry
$ sudo docker login --username=3097594492@qq.com registry.cn-beijing.aliyuncs.com
$ sudo docker tag [ImageId] registry.cn-beijing.aliyuncs.com/zxzhao/mycentos:[镜像版本号]
$ sudo docker push registry.cn-beijing.aliyuncs.com/zxzhao/mycentos:[镜像版本号]
请根据实际镜像信息替换示例中的[ImageId]和[镜像版本号]参数。

4. 选择合适的镜像仓库地址
从ECS推送镜像时，可以选择使用镜像仓库内网地址。推送速度将得到提升并且将不会损耗您的公网流量。

如果您使用的机器位于VPC网络，请使用 registry-vpc.cn-beijing.aliyuncs.com 作为Registry的域名登录，并作为镜像命名空间前缀。
5. 示例
使用"docker tag"命令重命名镜像，并将它通过专有网络地址推送至Registry。

$ sudo docker images
REPOSITORY                                                         TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
registry.aliyuncs.com/acs/agent                                    0.7-dfb6816         37bb9c63c8b2        7 days ago          37.89 MB
$ sudo docker tag 37bb9c63c8b2 registry-vpc.cn-beijing.aliyuncs.com/acs/agent:0.7-dfb6816
使用"docker images"命令找到镜像，将该镜像名称中的域名部分变更为Registry专有网络地址。

$ sudo docker push registry-vpc.cn-beijing.aliyuncs.com/acs/agent:0.7-dfb6816
```

