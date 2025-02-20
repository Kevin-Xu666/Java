# 微服务

[黑马程序员SpringCloud微服务开发与实战，java黑马商城项目微服务实战开发（涵盖MybatisPlus、Docker、MQ、ES、Redis高级等）_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1S142197x7/?spm_id_from=333.337.search-card.all.click&vd_source=d0a6be4ddc2dc230780bec5d7be12c7d)

## 简介

微服务是一种软件架构风格，它是以专注于单一职责的很多小型项目为基础，组合出复杂的大型项目。

单体项目把所有功能都放在一个项目中，而微服务则每个功能都拆解为一个项目：

<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250214123046751.png" alt="image-20250214123046751" style="zoom:50%;" />

## Docker

### 快速入门

以部署MySQL为例

1. 步骤

   利用传统方式部署MySQL，大概的步骤有：

   - 搜索并下载MySQL安装包
   - 上传至Linux环境
   - 编译和配置环境
   - 安装

   而使用Docker安装，仅仅需要一步即可，在命令行输入下面的命令：

   ```powershell
   docker run -d \
     --name mysql \
     -p 3306:3306 \
     -e TZ=Asia/Shanghai \
     -e MYSQL_ROOT_PASSWORD=123 \
     mysql
   ```

2. 镜像和容器

   **不同操作系统下安装包、运行环境是都不相同的**！如果是**手动安装，必须手动解决安装包不同、环境不同的、配置不同的问题**！

   而使用Docker，这些完全不用考虑。就是因为Docker会自动搜索并下载MySQL。Docker下载的不是安装包，而是**镜像。**

   镜像中不仅包含了MySQL本身，还包含了其运行所需要的环境、配置、系统级函数库。因此它在运行时就有自己独立的环境，就可以跨系统运行，也不需要手动再次配置环境了。这套独立运行的隔离环境我们称为**容器**。

   镜像仓库：Docker是去哪里搜索和下载镜像的呢？这些镜像又是谁制作的呢？Docker官方提供了一个专门管理、存储镜像的网站，并对外开放了镜像上传、下载的权利。Docker官方提供了一些基础镜像，然后各大软件公司又在基础镜像基础上，制作了自家软件的镜像，全部都存放在这个网站。像这种提供存储、管理Docker镜像的服务器，被称为DockerRegistry，可以翻译为镜像仓库。

3. Docker运行流程

   Docker命令由Docker的服务端执行。启动Docker会启动一个Docker守护进程，该进程会等待Docker命令，得到命令后去镜像仓库拉取镜像。一次拉取后，以后就不用重复拉取。

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250214130659670.png" alt="image-20250214130659670" style="zoom:50%;" />

4. 命令解读

   ```shell
   docker run -d \
     --name mysql \
     -p 3306:3306 \
     -e TZ=Asia/Shanghai \
     -e MYSQL_ROOT_PASSWORD=123 \
     mysql
   ```

   解读：

   - `docker run -d` ：创建并运行一个容器，`-d`则是让容器以后台进程运行
   - `--name mysql ` : 给容器起个名字叫`mysql`，你可以叫别的
   - `-p 3306:3306` : 设置端口映射。
     - **容器是隔离环境**，外界不可访问。但是可以**将****宿主机****端口****映射容器内到端口**，当访问宿主机指定端口时，就是在访问容器内的端口了。
     - 容器内端口往往是由容器内的进程决定，例如MySQL进程默认端口是3306，因此容器内端口一定是3306；而宿主机端口则可以任意指定，一般与容器内保持一致。
     - 格式： `-p 宿主机端口:容器内端口`，示例中就是将宿主机的3306映射到容器内的3306端口
   - `e TZ=Asia/Shanghai` : 配置容器内进程运行时的一些参数
     - 格式：`-e KEY=VALUE`，KEY和VALUE都由容器内进程决定
     - 案例中，`TZ``=Asia/Shanghai`是设置时区；`MYSQL_ROOT_PASSWORD=123`是设置MySQL默认密码
   - `mysql` : 设置**镜像**名称，Docker会根据这个名字搜索并下载镜像
     - 格式：`REPOSITORY:TAG`，例如`mysql:8.0`，其中`REPOSITORY`可以理解为镜像名，`TAG`是版本号
     - 在未指定`TAG`的情况下，默认是最新版本，也就是`mysql:latest`

### Docker基础

#### 常见命令

1. 命令介绍

   其中，比较常见的命令有：

   | **命令**       | **说明**                       | **文档地址**                                                 |
   | :------------- | :----------------------------- | :----------------------------------------------------------- |
   | docker pull    | 拉取镜像                       | [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) |
   | docker push    | 推送镜像到DockerRegistry       | [docker push](https://docs.docker.com/engine/reference/commandline/push/) |
   | docker images  | 查看本地镜像                   | [docker images](https://docs.docker.com/engine/reference/commandline/images/) |
   | docker rmi     | 删除本地镜像                   | [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) |
   | docker run     | 创建并运行容器（不能重复创建） | [docker run](https://docs.docker.com/engine/reference/commandline/run/) |
   | docker stop    | 停止指定容器                   | [docker stop](https://docs.docker.com/engine/reference/commandline/stop/) |
   | docker start   | 启动指定容器                   | [docker start](https://docs.docker.com/engine/reference/commandline/start/) |
   | docker restart | 重新启动容器                   | [docker restart](https://docs.docker.com/engine/reference/commandline/restart/) |
   | docker rm      | 删除指定容器                   | [docs.docker.com](https://docs.docker.com/engine/reference/commandline/rm/) |
   | docker ps      | 查看容器                       | [docker ps](https://docs.docker.com/engine/reference/commandline/ps/) |
   | docker logs    | 查看容器运行日志               | [docker logs](https://docs.docker.com/engine/reference/commandline/logs/) |
   | docker exec    | 进入容器                       | [docker exec](https://docs.docker.com/engine/reference/commandline/exec/) |
   | docker save    | 保存镜像到本地压缩文件         | [docker save](https://docs.docker.com/engine/reference/commandline/save/) |
   | docker load    | 加载本地压缩文件到镜像         | [docker load](https://docs.docker.com/engine/reference/commandline/load/) |
   | docker inspect | 查看容器详细信息               | [docker inspect](https://docs.docker.com/engine/reference/commandline/inspect/) |

   用一副图来表示这些命令的关系：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250214133409777.png" alt="image-20250214133409777" style="zoom:50%;" />

2. 命令别名

   某些命令较长，可以给命令取别名来方便使用。

   首先访问文件：

   ```shell
   vi /root/.bashrc
   ```

   看到内容如下，alias后的表达式左边的别名，右边是原命令：

   ```shell
   # .bashrc
   
   # User specific aliases and functions
   
   alias rm='rm -i'
   alias cp='cp -i'
   alias mv='mv -i'
   alias dps='docker ps --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}"'
   alias dis='docker images'
   
   # Source global definitions
   if [ -f /etc/bashrc ]; then
           . /etc/bashrc
   fi
   ```

   然后执行命令使别名生效：

   ```shell
   source /root/.bashrc
   ```

#### 数据卷

1. 设计目的

   容器是隔离环境，容器内程序的文件、配置、运行时产生的容器都在容器内部，我们要读写容器内的文件非常不方便。大家思考几个问题：

   - 如果要升级MySQL版本，需要销毁旧容器，那么数据岂不是跟着被销毁了？
   - MySQL、Nginx容器运行后，如果我要修改其中的某些配置该怎么办？
   - 我想要让Nginx代理我的静态资源怎么办？

   因此，容器提供程序的运行环境，但是**程序运行产生的数据、程序运行依赖的配置都应该与容器解耦**。

2. 数据卷介绍与挂载

   数据卷（volume）是一个虚拟目录，是**容器内目录**与**宿主机目录**之间映射的桥梁。

   以Nginx为例，我们知道Nginx中有两个关键的目录：

   - `html`：放置一些静态资源
   - `conf`：放置配置文件

   利用数据卷将两个目录与宿主机目录关联，方便我们操作：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250214144639277.png" alt="image-20250214144639277" style="zoom:50%;" />

   在上图中：

   - 我们创建了两个数据卷：`conf`、`html`
   - Nginx容器内部的`conf`目录和`html`目录分别与两个数据卷关联。
   - 而数据卷conf和html分别指向了宿主机的`/var/lib/docker/volumes/conf/_data`目录和`/var/lib/docker/volumes/html/_data`目录

   这样容器内的`conf`和`html`目录就与宿主机的`conf`和`html`目录关联起来，我们称为**挂载**。此时，我们操作宿主机的`/var/lib/docker/volumes/html/_data`就是在操作容器内的`/usr/share/nginx/html/_data`目录。

   数据卷挂载命令：

   ```shell
   docker run -v 数据卷：容器内目录
   ```

   说明：

   - `/var/lib/docker/volumes`这个目录就是默认的存放所有容器数据卷的目录，其下再根据数据卷名称创建新目录，格式为`/数据卷名/_data`。
   - 当创建容器时，如果挂载了数据卷且数据卷不存在，则会自动创建数据卷。
   - 挂载的动作必须是容器创建时完成，若容器已创建，无法再进行挂载。

3. 数据卷常见命令

   数据卷的相关命令有：

   | **命令**              | **说明**             | **文档地址**                                                 |
   | :-------------------- | :------------------- | :----------------------------------------------------------- |
   | docker volume create  | 创建数据卷           | [docker volume create](https://docs.docker.com/engine/reference/commandline/volume_create/) |
   | docker volume ls      | 查看所有数据卷       | [docs.docker.com](https://docs.docker.com/engine/reference/commandline/volume_ls/) |
   | docker volume rm      | 删除指定数据卷       | [docs.docker.com](https://docs.docker.com/engine/reference/commandline/volume_prune/) |
   | docker volume inspect | 查看某个数据卷的详情 | [docs.docker.com](https://docs.docker.com/engine/reference/commandline/volume_inspect/) |
   | docker volume prune   | 清除数据卷           | [docker volume prune](https://docs.docker.com/engine/reference/commandline/volume_prune/) |

4. 本地目录挂载

   默认的数据卷目录`/var/lib/docker/volumes`结构较深，如果我们去操作数据卷目录会不太方便。在很多情况下，我们会直接将容器目录与宿主机指定目录挂载。挂载语法与数据卷类似：

   ```shell
   # 挂载本地目录
   -v 本地目录:容器内目录
   # 挂载本地文件
   -v 本地文件:容器内文件
   ```

   **注意**：本地目录或文件必须以 `/` 或 `./`开头，如果直接以名字开头，会被识别为数据卷名而非本地目录名。

#### 自定义镜像

1. 镜像

   镜像就是包含了应用程序、程序运行的系统函数库、运行配置等文件的文件包。构建镜像的过程其实就是把上述文件打包的过程。

2. 镜像结构

   举个例子，我们要从0部署一个Java应用，大概流程是这样：

   - 准备一个linux服务（CentOS或者Ubuntu均可）
   - 安装并配置JDK
   - 上传Jar包
   - 运行jar包

   那因此，我们打包镜像也是分成这么几步：

   - 准备Linux运行环境（java项目并不需要完整的操作系统，仅仅是基础运行环境即可）
   - 安装并配置JDK
   - 拷贝jar包
   - 配置启动脚本

   上述步骤中的每一次操作其实都是在生产一些文件（系统运行环境、函数库、配置最终都是磁盘文件），所以**镜像就是一堆文件的集合**。

   但需要注意的是，镜像文件不是随意堆放的，而是按照操作的步骤分层叠加而成，每一层形成的文件都会单独打包并标记一个唯一id，称为**Layer**（**层**）。这样，如果我们构建时用到的某些层其他人已经制作过，就可以直接拷贝使用这些层，而不用重复制作。

   例如，第一步中需要的Linux运行环境，通用性就很强，所以Docker官方就制作了这样的只包含Linux运行环境的镜像。我们在制作java镜像时，就无需重复制作，直接使用Docker官方提供的CentOS或Ubuntu镜像作为基础镜像。然后再搭建其它层即可，这样逐层搭建，最终整个Java项目的镜像结构如图所示：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250214155600745.png" alt="image-20250214155600745" style="zoom:33%;" />

3. Dockerfile

   Dockerfile就是一个文本文件，其中包含一个个的指令，用指令来说明要执行什么操作来构建镜像。将来Docker可以根据Dockerfile帮我们构建镜像。

   其中的语法比较多，比较常用的有：

   | **指令**       | **说明**                                     | **示例**                     |
   | :------------- | :------------------------------------------- | :--------------------------- |
   | **FROM**       | 指定基础镜像                                 | `FROM centos:6`              |
   | **ENV**        | 设置环境变量，可在后面指令使用               | `ENV key value`              |
   | **COPY**       | 拷贝本地文件到镜像的指定目录                 | `COPY ./xx.jar /tmp/app.jar` |
   | **RUN**        | 执行Linux的shell命令，一般是安装过程的命令   | `RUN yum install gcc`        |
   | **EXPOSE**     | 指定容器运行时监听的端口，是给镜像使用者看的 | EXPOSE 8080                  |
   | **ENTRYPOINT** | 镜像中应用的启动命令，容器运行时调用         | ENTRYPOINT java -jar xx.jar  |

   例如，要基于Ubuntu镜像来构建一个Java应用：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250214160807423.png" alt="image-20250214160807423" style="zoom:50%;" /> 

4. 构建镜像

   当Dockerfile文件写好以后，就可以利用命令来构建镜像了。

   首先将文件和Dockerfile拷贝到虚拟机某一个目录下，然后在该目录下执行命令：

   ```shell
   docker build -t 镜像名 Dockerfile目录
   ```

   其中镜像名包括了repository和版本号tag

#### 网络

1. 容器网络

   容器内有个信息是IP地址，但是，容器的网络IP其实是一个虚拟的IP，其值并不固定与某一个容器绑定，如果我们在开发时写死某个IP，而在部署时很可能容器的IP会发生变化，连接会失败。

2. 网络常见命令

   | **命令**                  | **说明**                 | **文档地址**                                                 |
   | :------------------------ | :----------------------- | :----------------------------------------------------------- |
   | docker network create     | 创建一个网络             | [docker network create](https://docs.docker.com/engine/reference/commandline/network_create/) |
   | docker network ls         | 查看所有网络             | [docs.docker.com](https://docs.docker.com/engine/reference/commandline/network_ls/) |
   | docker network rm         | 删除指定网络             | [docs.docker.com](https://docs.docker.com/engine/reference/commandline/network_rm/) |
   | docker network prune      | 清除未使用的网络         | [docs.docker.com](https://docs.docker.com/engine/reference/commandline/network_prune/) |
   | docker network connect    | 使指定容器连接加入某网络 | [docs.docker.com](https://docs.docker.com/engine/reference/commandline/network_connect/) |
   | docker network disconnect | 使指定容器连接离开某网络 | [docker network disconnect](https://docs.docker.com/engine/reference/commandline/network_disconnect/) |
   | docker network inspect    | 查看网络详细信息         | [docker network inspect](https://docs.docker.com/engine/reference/commandline/network_inspect/) |

3. 自定义网络

   ```bash
   # 1.首先通过命令创建一个网络
   docker network create hmall
   
   # 2.然后查看网络
   docker network ls
   # 结果：
   NETWORK ID     NAME      DRIVER    SCOPE
   639bc44d0a87   bridge    bridge    local
   403f16ec62a2   hmall     bridge    local
   0dc0f72a0fbb   host      host      local
   cd8d3e8df47b   none      null      local
   # 其中，除了hmall以外，其它都是默认的网络
   
   # 3.让dd和mysql都加入该网络，注意，在加入网络时可以通过--alias给容器起别名
   # 这样该网络内的其它容器可以用别名互相访问！
   # 3.1.mysql容器，指定别名为db，另外每一个容器都有一个别名是容器名
   docker network connect hmall mysql --alias db
   # 3.2.db容器，也就是我们的java项目
   docker network connect hmall dd
   
   # 4.进入dd容器，尝试利用别名访问db
   # 4.1.进入容器
   docker exec -it dd bash
   # 4.2.用db别名访问
   ping db
   # 结果
   PING db (172.18.0.2) 56(84) bytes of data.
   64 bytes from mysql.hmall (172.18.0.2): icmp_seq=1 ttl=64 time=0.070 ms
   64 bytes from mysql.hmall (172.18.0.2): icmp_seq=2 ttl=64 time=0.056 ms
   # 4.3.用容器名访问
   ping mysql
   # 结果：
   PING mysql (172.18.0.2) 56(84) bytes of data.
   64 bytes from mysql.hmall (172.18.0.2): icmp_seq=1 ttl=64 time=0.044 ms
   64 bytes from mysql.hmall (172.18.0.2): icmp_seq=2 ttl=64 time=0.054 ms
   ```

   - 在自定义网络中，可以给容器起多个别名，默认的别名是容器名本身
   - 在同一个自定义网络中的容器，可以通过别名互相访问

### 项目部署

1. DockerCompose介绍

   Docker Compose可以帮助我们实现**多个相互关联的Docker容器的快速部署**。它允许用户通过一个单独的 docker-compose.yml 模板文件（YAML 格式）来定义一组相关联的应用容器。

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250214180658843.png" alt="image-20250214180658843" style="zoom:50%;" />

   

2. 基本语法

   与docker run的对比：

   | **docker run 参数** | **docker compose 指令** | **说明**   |
   | :------------------ | :---------------------- | :--------- |
   | --name              | container_name          | 容器名称   |
   | -p                  | ports                   | 端口映射   |
   | -e                  | environment             | 环境变量   |
   | -v                  | volumes                 | 数据卷配置 |
   | --network           | networks                | 网络       |

   举例来说，用docker run部署MySQL的命令如下：

   ```Bash
   docker run -d \
     --name mysql \
     -p 3306:3306 \
     -e TZ=Asia/Shanghai \
     -e MYSQL_ROOT_PASSWORD=123 \
     -v ./mysql/data:/var/lib/mysql \
     -v ./mysql/conf:/etc/mysql/conf.d \
     -v ./mysql/init:/docker-entrypoint-initdb.d \
     --network hmall
     mysql
   ```

   如果用`docker-compose.yml`文件来定义，就是这样：

   ```YAML
   version: "3.8"
   
   services:
     mysql:
       image: mysql
       container_name: mysql
       ports:
         - "3306:3306"
       environment:
         TZ: Asia/Shanghai
         MYSQL_ROOT_PASSWORD: 123
       volumes:
         - "./mysql/conf:/etc/mysql/conf.d"
         - "./mysql/data:/var/lib/mysql"
       networks:
         - new
   networks:
     new:
       name: hmall
   ```

3. 基础命令

   基本语法如下：

   ```Bash
   docker compose [OPTIONS] [COMMAND]
   ```

   其中，OPTIONS和COMMAND都是可选参数，比较常见的有：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250214183827534.png" alt="image-20250214183827534" style="zoom:40%;" />

## 微服务

### 认识微服务

1. 单体架构

   顾名思义，整个项目中所有功能模块都在一个工程中开发；项目部署时需要对所有模块一起编译、打包；项目的架构设计、开发模式都非常简单。

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250215134316091.png" alt="image-20250215134316091" style="zoom:33%;" />

   当项目规模较小时，这种模式上手快，部署、运维也都很方便，因此早期很多小型项目都采用这种模式。

   但随着项目的业务规模越来越大，团队开发人员也不断增加，单体架构就呈现出越来越多的问题：

   - **团队协作成本高**：试想一下，你们团队数十个人同时协作开发同一个项目，由于所有模块都在一个项目中，不同模块的代码之间物理边界越来越模糊。最终要把功能合并到一个分支，你绝对会陷入到解决冲突的泥潭之中。
   - **系统发布效率低**：任何模块变更都需要发布整个系统，而系统发布过程中需要多个模块之间制约较多，需要对比各种文件，任何一处出现问题都会导致发布失败，往往一次发布需要数十分钟甚至数小时。
   - **系统可用性差**：单体架构各个功能模块是作为一个服务部署，相互之间会互相影响，一些热点功能会耗尽系统资源，导致其它服务低可用。

2. 微服务

   微服务架构，首先是服务化，就是将单体架构中的功能模块从单体应用中拆分出来，独立部署为多个服务。同时要满足下面的一些特点：

   - **单一职责**：一个微服务负责一部分业务功能，并且其核心数据不依赖于其它模块。
   - **团队自治**：每个微服务都有自己独立的开发、测试、发布、运维人员，团队人员规模不超过10人（2张披萨能喂饱）
   - **服务自治**：每个微服务都独立打包部署，访问自己独立的数据库。并且要做好服务隔离，避免对其它服务产生影响

   例如，黑马商城项目，我们就可以把商品、用户、购物车、交易等模块拆分，交给不同的团队去开发，并独立部署：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250215134524047.png" alt="image-20250215134524047" style="zoom:33%;" />

3. SpringCloud

   微服务拆分以后碰到的各种问题都有对应的解决方案和微服务组件，而SpringCloud框架可以说是目前Java领域最全面的微服务组件的集合了。

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250215134605465.png" alt="image-20250215134605465" style="zoom:33%;" />

### 微服务拆分

#### 拆分原则

1. 拆分时机

   对于**大多数小型项目来说：一般是先采用单体架构**，随着用户规模扩大、业务复杂后**再逐渐拆分为**微服务架构**。这样初期成本会比较低，可以快速试错。但是，这么做的问题就在于后期做服务拆分时，可能会遇到很多代码耦合带来的问题，拆分比较困难（**前易后难）。

   对于一些大型项目，在立项之初目的就很明确，为了长远考虑，在架构设计时就直接选择微服务架构。虽然前期投入较多，但后期就少了拆分服务的烦恼（**前难后易**）。

2. 拆分目标

   - 高内聚：每个微服务的职责要尽量单一，包含的业务相互关联度高、完整度高。
   - 低耦合：每个微服务的功能要相对独立，尽量减少对其它微服务的依赖，或者依赖接口的稳定性要强。

3. 拆分方式

   - 纵向拆分：按照项目的功能模块来拆分，可以尽可能提高服务的内聚性。
   - 横向拆分：是看各个功能模块之间有没有公共的业务部分，如果有将其抽取出来作为通用服务。

4. 拆分工程结构

   一般微服务项目有两种不同的工程结构：

   （1）完全解耦：每一个微服务都创建为一个独立的工程，甚至可以使用不同的开发语言来开发，项目完全解耦。

   - 优点：服务之间耦合度低
   - 缺点：每个项目都有自己的独立仓库，管理起来比较麻烦

   （2）Maven聚合：整个项目为一个Project，然后每个微服务是其中的一个Module

   - 优点：项目代码集中，管理和运维方便（授课也方便）
   - 缺点：服务之间耦合，编译时间较长

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250215144323376.png" alt="image-20250215144323376" style="zoom:33%;" />

#### 服务调用

在拆分的时候，发现一个问题：就是购物车业务中需要查询商品信息，但商品信息查询的逻辑全部迁移到了商品微服务中，导致我们无法查询。

最终结果就是查询到的购物车数据不完整，因此要想解决这个问题，我们就必须改造其中的代码，把原本本地方法调用，改造成跨微服务的远程调用（RPC，即**R**emote **P**roduce **C**all）。

因此，现在查询购物车列表的流程变成了这样：

<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250215160811428.png" alt="image-20250215160811428" style="zoom:33%;" />

### 服务治理

#### 注册中心原理

1. 远程调用的角色

   微服务远程调用过程中，包括两个角色：

   - 服务提供者：提供接口供其它微服务访问，比如上面的`item-service`
   - 服务消费者：调用其它微服务提供的接口，比如上面的`cart-service`

   一个服务既能是服务提供者，又能是服务消费者。

2. 注册中心

   在大型微服务项目中，服务提供者的数量会非常多，为了管理这些服务就引入了**注册中心**的概念。

   注册中心：记录并监控微服务各实例状态，推送服务变更信息。

   注册中心、服务提供者、服务消费者三者间关系如下：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250215164334611.png" alt="image-20250215164334611" style="zoom:33%;" />

   流程描述：

   - 服务启动时就会注册自己的服务信息（服务名、IP、端口）到注册中心
   - 调用者可以从注册中心订阅想要的服务，获取服务对应的实例列表（1个服务可能多实例部署）
   - 调用者自己对实例列表负载均衡，挑选一个实例
   - 调用者向该实例发起远程调用

3. 总结

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250215164633436.png" alt="image-20250215164633436" style="zoom:50%;" /> 

#### Nacos注册中心

1. Nacos介绍

   目前开源的注册中心框架有很多，国内比较常见的有：

   - Eureka：Netflix公司出品，目前被集成在SpringCloud当中，一般用于Java应用
   - Nacos：Alibaba公司出品，目前被集成在SpringCloudAlibaba中，一般用于Java应用
   - Consul：HashiCorp公司出品，目前集成在SpringCloud中，不限制微服务语言

   Nacos是国内产品，中文文档比较丰富，而且同时具备**配置管理**功能，因此在国内使用较多。

   Nacos的数据会存储在MySQL数据库中。

   部署完成后，访问`http://虚拟机IP:端口/nacos/`，会得到如下界面：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250215192159099.png" alt="image-20250215192159099" style="zoom:33%;" />

   登录页的账号密码都是nacos。

2. 服务注册

   以把`item-service`注册到Nacos为例，步骤如下：

   （1）步骤1：添加依赖

   在`item-service`的`pom.xml`中添加依赖：

   ```XML
   <!--nacos 服务注册发现-->
   <dependency>
       <groupId>com.alibaba.cloud</groupId>
       <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
   </dependency>
   ```

   （2）步骤2：配置Nacos

   在`item-service`的`application.yml`中添加nacos地址配置：

   ```YAML
   spring:
     application:
       name: item-service # 服务名称
     cloud:
       nacos:
         server-addr: 192.168.150.101:8848 # nacos地址
   ```

   （3）步骤3：访问nacos控制台，可以发现服务注册成功

3. 服务发现

   服务的消费者要去nacos订阅服务，这个过程就是服务发现，步骤如下：

   （1）步骤1：添加依赖

   服务发现除了要引入nacos依赖以外，由于还需要负载均衡，因此要引入SpringCloud提供的LoadBalancer依赖。

   我们在`cart-service`中的`pom.xml`中添加下面的依赖：

   ```XML
   <!--nacos 服务注册发现-->
   <dependency>
       <groupId>com.alibaba.cloud</groupId>
       <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
   </dependency>
   ```

   可以发现，这里Nacos的依赖于服务注册时一致，这个依赖中同时包含了服务注册和发现的功能。因为任何一个微服务都可以调用别人，也可以被别人调用，即可以是调用者，也可以是提供者。

   （2）步骤2：配置Nacos

   在`cart-service`的`application.yml`中添加nacos地址配置：

   ```YAML
   spring:
     cloud:
       nacos:
         server-addr: 192.168.150.101:8848
   ```

   （3）步骤3：发现并调用服务

   服务发现需要用到一个工具，DiscoveryClient，SpringCloud已经帮我们自动装配，我们可以直接注入使用：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250215193213797.png" alt="image-20250215193213797" style="zoom:50%;" />

   通过DiscoveryClient发现服务实例列表，然后通过负载均衡算法，选择一个实例去调用：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250215193300215.png" alt="image-20250215193300215" style="zoom:33%;" />

### OpenFeign

#### 快速入门

1. 介绍

   远程调用的关键点就在于四个：

   - 请求方式
   - 请求路径
   - 请求参数
   - 返回值类型

   所以，OpenFeign就利用SpringMVC的相关注解来声明上述4个参数，然后基于动态代理帮我们生成远程调用的代码，而无需我们手动再编写。

2. 快速入门

   以cart-service中的查询我的购物车为例。因此下面的操作都是在cart-service中进行：

   （1）引入依赖

   在`cart-service`服务的pom.xml中引入`OpenFeign`的依赖和`loadBalancer`依赖：

   ```XML
     <!--openFeign-->
     <dependency>
         <groupId>org.springframework.cloud</groupId>
         <artifactId>spring-cloud-starter-openfeign</artifactId>
     </dependency>
     <!--负载均衡器-->
     <dependency>
         <groupId>org.springframework.cloud</groupId>
         <artifactId>spring-cloud-starter-loadbalancer</artifactId>
     </dependency>
   ```

   （2）启用OpenFeign

   接下来在`cart-service`的`CartApplication`启动类上添加注解，启动OpenFeign功能：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250215211622805.png" alt="image-20250215211622805" style="zoom:40%;" /> 

   （3）编写OpenFeign客户端

   在`cart-service`中，定义一个新的接口，编写Feign客户端：

   其中代码如下：

   ```Java
   package com.hmall.cart.client;
   
   import com.hmall.cart.domain.dto.ItemDTO;
   import org.springframework.cloud.openfeign.FeignClient;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.RequestParam;
   
   import java.util.List;
   
   @FeignClient("item-service")
   public interface ItemClient {
   
       @GetMapping("/items")
       List<ItemDTO> queryItemByIds(@RequestParam("ids") Collection<Long> ids);
   }
   ```

   这里只需要声明接口，无需实现方法。接口中的几个关键信息：

   - `@FeignClient("item-service")` ：声明服务名称
   - `@GetMapping` ：声明请求方式
   - `@GetMapping("/items")` ：声明请求路径
   - `@RequestParam("ids") Collection<Long> ids` ：声明请求参数
   - `List<ItemDTO>` ：返回值类型

   有了上述信息，OpenFeign就可以利用动态代理帮我们实现这个方法，并且向`http://item-service/items`发送一个`GET`请求，携带ids为请求参数，并自动将返回值处理为`List<ItemDTO>`。

   （4）使用FeignClient

   最后，在`cart-service`的`com.hmall.cart.service.impl.CartServiceImpl`中改造代码，直接调用接口`ItemClient`的方法：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250215211859724.png" alt="image-20250215211859724" style="zoom:33%;" /> 

#### 连接池

Feign底层发起http请求，依赖于其它的框架。其底层支持的http客户端实现包括：

- HttpURLConnection：默认实现，不支持连接池
- Apache HttpClient ：支持连接池
- OKHttp：支持连接池

因此通常会使用带有连接池的客户端来代替默认的HttpURLConnection。以使用OK Http为例：

（1）引入依赖

在`cart-service`的`pom.xml`中引入依赖：

```XML
<!--OK http 的依赖 -->
<dependency>
  <groupId>io.github.openfeign</groupId>
  <artifactId>feign-okhttp</artifactId>
</dependency>
```

  （2）开启连接池

在`cart-service`的`application.yml`配置文件中开启Feign的连接池功能：

```YAML
feign:
  okhttp:
    enabled: true # 开启OKHttp功能
```

重启服务，连接池就生效了。

#### 最佳实践

1. 最佳实践

   假设还有个交易微服务（`trade-service`），它也需要远程调用`item-service`中的根据id批量查询商品功能。这个需求与`cart-service`中是一样的。因此，就需要在`trade-service`中再次定义`ItemClient`接口，这就造成了重复编码。

   避免重复编码的办法就是**抽取**。这里有两种抽取思路：

   - 思路1：抽取到微服务之外的公共module
   - 思路2：每个微服务自己抽取一个module

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250215213417417.png" alt="image-20250215213417417" style="zoom:40%;" />

   方案1抽取更加简单，工程结构也比较清晰，但缺点是整个项目耦合度偏高。

   方案2抽取相对麻烦，工程结构相对更复杂，但服务之间耦合度降低。

2. 抽取Feign客户端

   在`hmall`下定义一个新的module，命名为hm-api

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250215214342522.png" alt="image-20250215214342522" style="zoom:33%;" />

   添加依赖，然后把ItemDTO和ItemClient都拷贝过来，最终结构如下：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250215214424572.png" alt="image-20250215214424572" style="zoom:33%;" />

   现在，任何微服务要调用`item-service`中的接口，只需要引入`hm-api`模块依赖即可，无需自己编写Feign客户端了。

3. 扫描包

   接下来，我们在`cart-service`的`pom.xml`中引入`hm-api`模块：

   ```XML
     <!--feign模块-->
     <dependency>
         <groupId>com.heima</groupId>
         <artifactId>hm-api</artifactId>
         <version>1.0.0</version>
     </dependency>
   ```

   删除`cart-service`中原来的ItemDTO和ItemClient，重启项目，发现报错了：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250215214609543.png" alt="image-20250215214609543" style="zoom:50%;" />

   这里因为`ItemClient`现在定义到了`com.hmall.api.client`包下，而cart-service的启动类定义在`com.hmall.cart`包下，扫描不到`ItemClient`，所以报错了。

   解决办法很简单，在cart-service的启动类上添加声明即可，两种方式：

   （1）方式1：声明扫描包

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250215214718965.png" alt="image-20250215214718965" style="zoom:43%;" /> 

   （2）方式2：声明要用的FeignClient

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250215214746841.png" alt="image-20250215214746841" style="zoom:43%;" /> 

#### 日志配置

1. Feign日志级别

   OpenFeign只会在FeignClient所在包的日志级别为**DEBUG**时，才会输出日志。而且其日志级别有4级：

   - **NONE**：不记录任何日志信息，这是默认值。
   - **BASIC**：仅记录请求的方法，URL以及响应状态码和执行时间
   - **HEADERS**：在BASIC的基础上，额外记录了请求和响应的头信息
   - **FULL**：记录所有请求和响应的明细，包括头信息、请求体、元数据。

   Feign默认的日志级别就是NONE，所以默认我们看不到请求日志。

2. 定义日志级别

   在hm-api模块下新建一个配置类，定义Feign的日志级别：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250215215119508.png" alt="image-20250215215119508" style="zoom:50%;" /> 

3. 配置

   要让日志级别生效，还需要配置这个类。有两种方式：

   - **局部**生效：在某个`FeignClient`中配置，只对当前`FeignClient`生效

   ```Java
   @FeignClient(value = "item-service", configuration = DefaultFeignConfig.class)
   ```

   - **全局**生效：在`@EnableFeignClients`中配置，针对所有`FeignClient`生效。

   ```Java
   @EnableFeignClients(defaultConfiguration = DefaultFeignConfig.class)
   ```

### 网关路由

#### 快速入门

1. 网关介绍

   网关就是网络的关口。数据在网络间传输，从一个网络传输到另一网络时就需要经过网关来做数据的路由和转发以及数据安全的校验。

   更通俗的来讲，网关就像是以前园区传达室的大爷。

   - 外面的人要想进入园区，必须经过大爷的认可，如果你是不怀好意的人，肯定被直接拦截。
   - 外面的人要传话或送信，要找大爷。大爷帮你带给目标人。

   网关可以看作是**一个独立的微服务**。前端请求不能直接访问微服务，而是要请求网关：

   - 网关可以做安全控制，也就是登录身份校验，校验通过才放行
   - 通过认证后，网关再根据请求判断应该访问哪个微服务，将请求转发过去

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250217131606276.png" alt="image-20250217131606276" style="zoom:33%;" />

   在SpringCloud当中，提供了两种网关实现方案：

   - Netflix Zuul：早期实现，目前已经淘汰
   - SpringCloudGateway：基于Spring的WebFlux技术，完全支持响应式编程，吞吐能力更强

2. 快速入门

   由于网关本身也是一个独立的微服务，因此也需要创建一个模块开发功能。大概步骤如下：

   - 创建网关微服务
   - 引入SpringCloudGateway、NacosDiscovery依赖
   - 编写启动类
   - 配置网关路由

   （1）步骤1：创建项目

   首先，我们要在hmall下创建一个新的module，命名为hm-gateway，作为网关微服务：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250217132104289.png" alt="image-20250217132104289" style="zoom:33%;" />

   （2）步骤2：引入依赖

   在`hm-gateway`模块的`pom.xml`文件中引入依赖：

   ```XML
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <parent>
           <artifactId>hmall</artifactId>
           <groupId>com.heima</groupId>
           <version>1.0.0</version>
       </parent>
       <modelVersion>4.0.0</modelVersion>
   
       <artifactId>hm-gateway</artifactId>
   
       <properties>
           <maven.compiler.source>11</maven.compiler.source>
           <maven.compiler.target>11</maven.compiler.target>
       </properties>
       <dependencies>
           <!--common-->
           <dependency>
               <groupId>com.heima</groupId>
               <artifactId>hm-common</artifactId>
               <version>1.0.0</version>
           </dependency>
           <!--网关-->
           <dependency>
               <groupId>org.springframework.cloud</groupId>
               <artifactId>spring-cloud-starter-gateway</artifactId>
           </dependency>
           <!--nacos discovery-->
           <dependency>
               <groupId>com.alibaba.cloud</groupId>
               <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
           </dependency>
           <!--负载均衡-->
           <dependency>
               <groupId>org.springframework.cloud</groupId>
               <artifactId>spring-cloud-starter-loadbalancer</artifactId>
           </dependency>
       </dependencies>
       <build>
           <finalName>${project.artifactId}</finalName>
           <plugins>
               <plugin>
                   <groupId>org.springframework.boot</groupId>
                   <artifactId>spring-boot-maven-plugin</artifactId>
               </plugin>
           </plugins>
       </build>
   </project>
   ```

   （3）步骤3：编写启动类

   在`hm-gateway`模块的`com.hmall.gateway`包下新建一个启动类：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250217132242157.png" alt="image-20250217132242157" style="zoom:33%;" />

   代码如下：

   ```Java
   package com.hmall.gateway;
   
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   
   @SpringBootApplication
   public class GatewayApplication {
       public static void main(String[] args) {
           SpringApplication.run(GatewayApplication.class, args);
       }
   }
   ```

   （4）步骤4：配置路由（**关键步骤**）

   接下来，在`hm-gateway`模块的`resources`目录新建一个`application.yaml`文件，内容如下：

   ```YAML
   server:
     port: 8080
   spring:
     application:
       name: gateway
     cloud:
       nacos:
         server-addr: 192.168.150.101:8848 # 注册中心的地址
       gateway:
         routes:
           - id: item # 路由规则id，自定义，唯一
             uri: lb://item-service # 路由的目标微服务，lb代表负载均衡，会从注册中心拉取服务列表
             predicates: # 路由断言，判断当前请求是否符合当前规则，符合则路由到目标服务
               - Path=/items/**,/search/** # 这里是以请求路径作为判断规则
           - id: cart
             uri: lb://cart-service
             predicates:
               - Path=/carts/**
           - id: user
             uri: lb://user-service
             predicates:
               - Path=/users/**,/addresses/**
           - id: trade
             uri: lb://trade-service
             predicates:
               - Path=/orders/**
           - id: pay
             uri: lb://pay-service
             predicates:
               - Path=/pay-orders/**
   ```

   （5）步骤5：测试

   启动GatewayApplication，以 http://localhost:8080 拼接微服务接口路径来测试。例如：

   http://localhost:8080/items/page?pageNo=1&pageSize=1，能正常访问

#### 路由属性

1. 网关路由属性

   网关路由对应的Java类型是RouteDefinition，其中常见的属性有：

   - id：路由唯一标识
   - uri：路由目标地址
   - predicates：路由断言，判断请求是否符合当前路由
   - filters：路由过滤器，对请求或响应做特殊处理

2. 路由断言

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250217133355227.png" alt="image-20250217133355227" style="zoom:50%;" /> 

3. 路由过滤器

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250217134256410.png" alt="image-20250217134256410" style="zoom:50%;" /> 

   路由过滤器可以配置在routes下面，也可以配置和routes同一级，字段名为`default-filters`，表示对所有路由都生效：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250217135141672.png" alt="image-20250217135141672" style="zoom:43%;" /> 

### 网关登录校验

#### 鉴权思路分析

1. 原有问题

   登录是基于JWT来实现的，校验JWT的算法复杂，而且需要用到秘钥。如果每个微服务都去做登录校验，这就存在着两大问题：

   - 每个微服务都需要知道JWT的秘钥，不安全
   - 每个微服务重复编写登录校验代码、权限校验代码，麻烦

   既然网关是所有微服务的入口，一切请求都需要先经过网关。我们完全可以把登录校验的工作放到网关去做。

2. 网关请求处理流程

   登录校验必须在请求转发到微服务之前做，否则就失去了意义。而网关的请求转发是`Gateway`内部代码实现的，要想在请求转发之前做登录校验，就必须了解`Gateway`内部工作的基本原理。

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250217145633179.png" alt="image-20250217145633179" style="zoom:50%;" />

3. 登录校验流程及其问题

   使用网关情况下的登录校验流程如下：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250217145836545.png" alt="image-20250217145836545" style="zoom:50%;" />

   这里存在几个问题：

   - 网关路由是配置的，请求转发是Gateway内部代码，我们如何在转发之前做登录校验？
   - 网关校验JWT之后，如何将用户信息传递给微服务？
   - 微服务之间也会相互调用，这种调用不经过网关，又该如何传递用户信息？

#### 自定义过滤器

1. 网关过滤器

   网关过滤器链中的过滤器有两种：

   - **`GatewayFilter`**：路由过滤器，作用范围比较灵活，可以是任意指定的路由`Route`。路由属性的路由过滤器就是Gateway组件内置的`GatewayFilter`。
   - **`GlobalFilter`**：全局过滤器，作用范围是所有路由，不可配置。

   两种过滤器的方法签名完全一致：

   ```Java
   /**
    * 处理请求并将其传递给下一个过滤器
    * @param exchange 当前请求的上下文，其中包含request、response等各种数据
    * @param chain 过滤器链，基于它向下传递请求，表示要调用过滤器链中的下一个过滤器
    * @return 根据返回值标记当前请求是否被完成或拦截，chain.filter(exchange)就放行了。
    */
   Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain);
   ```

   对于上一节提出的第一个问题，由于最终请求转发是有一个名为`NettyRoutingFilter`的过滤器来执行的，而且这个过滤器是整个过滤器链中顺序最靠后的一个。如果我们能够定义一个过滤器，在其中实现登录校验逻辑，并且将过滤器执行顺序定义到`NettyRoutingFilter`之前，这就符合我们的需求了。

2. 自定义GlobalFilter

   自定义GlobalFilter比较简单，直接实现GlobalFilter即可，而且也无法设置动态参数：

   ```Java
   @Component
   public class PrintAnyGlobalFilter implements GlobalFilter, Ordered { //实现GlobalFilter接口和控制优先级的接口
       @Override
       public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
           // 编写过滤器逻辑
           System.out.println("未登录，无法访问");
           // 放行，传递给下一个过滤器
           // return chain.filter(exchange);
   
           // 拦截
           ServerHttpResponse response = exchange.getResponse();
           response.setRawStatusCode(401);
           return response.setComplete();
       }
   
       @Override
       public int getOrder() {
           // 过滤器执行顺序，值越小，优先级越高，NettyRoutingFilter定义的是最大的int
           return 0;
       }
   }
   ```

3. 自定义GatewayFilter

   自定义`GatewayFilter`不是直接实现`GatewayFilter`，而是实现`AbstractGatewayFilterFactory`。由于使用较为繁琐，日常开发中使用较多的还是`GlobalFilter`。最简单的方式是这样的：

   ```Java
   @Component
   public class PrintAnyGatewayFilterFactory extends AbstractGatewayFilterFactory<Object> {
       @Override
       public GatewayFilter apply(Object config) {
           return new GatewayFilter() {
               @Override
               public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
                   // 获取请求
                   ServerHttpRequest request = exchange.getRequest();
                   // 编写过滤器逻辑
                   System.out.println("过滤器执行了");
                   // 放行
                   return chain.filter(exchange);
               }
           };
       }
   }
   ```

   **注意**：该类的名称一定要以`GatewayFilterFactory`为后缀！

   然后在yaml配置中这样使用：

   ```YAML
   spring:
     cloud:
       gateway:
         default-filters:
               - PrintAny # 此处直接以自定义的GatewayFilterFactory类名称前缀类声明过滤器
   ```

   另外，这种过滤器还可以支持动态配置参数，不过实现起来比较复杂，示例：

   ```Java
   @Component
   public class PrintAnyGatewayFilterFactory // 父类泛型是内部类的Config类型
                   extends AbstractGatewayFilterFactory<PrintAnyGatewayFilterFactory.Config> {
   
       @Override
       public GatewayFilter apply(Config config) {
           // OrderedGatewayFilter是GatewayFilter的子类，包含两个参数：
           // - GatewayFilter：过滤器
           // - int order值：值越小，过滤器执行优先级越高
           return new OrderedGatewayFilter(new GatewayFilter() {
               @Override
               public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
                   // 获取config值
                   String a = config.getA();
                   String b = config.getB();
                   String c = config.getC();
                   // 编写过滤器逻辑
                   System.out.println("a = " + a);
                   System.out.println("b = " + b);
                   System.out.println("c = " + c);
                   // 放行
                   return chain.filter(exchange);
               }
           }, 100);
       }
   
       // 自定义配置属性，成员变量名称很重要，下面会用到
       @Data
       static class Config{
           private String a;
           private String b;
           private String c;
       }
       // 将变量名称依次返回，顺序很重要，将来读取参数时需要按顺序获取
       @Override
       public List<String> shortcutFieldOrder() {
           return List.of("a", "b", "c");
       }
           // 返回当前配置类的类型，也就是内部的Config
       @Override
       public Class<Config> getConfigClass() {
           return Config.class;
       }
   
   }
   ```

   然后在yaml文件中使用：

   ```YAML
   spring:
     cloud:
       gateway:
         default-filters:
               - PrintAny=1,2,3 # 注意，这里多个参数以","隔开，将来会按照shortcutFieldOrder()方法返回的参数顺序依次复制
   ```

   上面这种配置方式参数必须严格按照shortcutFieldOrder()方法的返回参数名顺序来赋值。

   还有一种用法，无需按照这个顺序，就是手动指定参数名：

   ```YAML
   spring:
     cloud:
       gateway:
         default-filters:
               - name: PrintAny
                 args: # 手动指定参数名，无需按照参数顺序
                   a: 1
                   b: 2
                   c: 3
   ```

#### 实现登录校验

1. jwt工具

   登录校验需要用到JWT，而且JWT的加密需要秘钥和加密工具。这些在`hm-service`中已经有了，直接拷贝过来：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250217160139898.png" alt="image-20250217160139898" style="zoom:33%;" /> 

   具体作用如下：

   - `AuthProperties`：配置登录校验需要拦截的路径，因为不是所有的路径都需要登录才能访问
   - `JwtProperties`：定义与JWT工具有关的属性，比如秘钥文件位置
   - `SecurityConfig`：工具的自动装配
   - `JwtTool`：JWT工具，其中包含了校验和解析`token`的功能
   - `hmall.jks`：秘钥文件

   其中`AuthProperties`和`JwtProperties`所需的属性要在`application.yaml`中配置：

   ```YAML
   hm:
     jwt:
       location: classpath:hmall.jks # 秘钥地址
       alias: hmall # 秘钥别名
       password: hmall123 # 秘钥文件密码
       tokenTTL: 30m # 登录有效期
     auth:
       excludePaths: # 无需登录校验的路径
         - /search/**
         - /users/login
         - /items/**
   ```

2. 登录校验过滤器

   定义一个登录校验的过滤器：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250217160231574.png" alt="image-20250217160231574" style="zoom:33%;" /> 

   代码如下：

   ```Java
   @Component
   @RequiredArgsConstructor
   @EnableConfigurationProperties(AuthProperties.class)
   public class AuthGlobalFilter implements GlobalFilter, Ordered {
   
       private final JwtTool jwtTool;
   
       private final AuthProperties authProperties;
   
       private final AntPathMatcher antPathMatcher = new AntPathMatcher();
   
       @Override
       public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
           // 1.获取Request
           ServerHttpRequest request = exchange.getRequest();
           // 2.判断是否不需要拦截
           if(isExclude(request.getPath().toString())){
               // 无需拦截，直接放行
               return chain.filter(exchange);
           }
           // 3.获取请求头中的token
           String token = null;
           List<String> headers = request.getHeaders().get("authorization");
           if (!CollUtils.isEmpty(headers)) {
               token = headers.get(0);
           }
           // 4.校验并解析token
           Long userId = null;
           try {
               userId = jwtTool.parseToken(token);
           } catch (UnauthorizedException e) {
               // 如果无效，拦截
               ServerHttpResponse response = exchange.getResponse();
               response.setRawStatusCode(401);
               return response.setComplete();
           }
   
           // TODO 5.如果有效，传递用户信息
           System.out.println("userId = " + userId);
           // 6.放行
           return chain.filter(exchange);
       }
   
       private boolean isExclude(String antPath) {
           for (String pathPattern : authProperties.getExcludePaths()) {
               if(antPathMatcher.match(pathPattern, antPath)){
                   return true;
               }
           }
           return false;
       }
   
       @Override
       public int getOrder() {
           return 0;
       }
   }
   ```

#### 网关传递用户到微服务

1. 总体流程

   由于网关发送请求到微服务依然采用的是`Http`请求，因此我们可以将用户信息以请求头的方式传递到下游微服务。然后微服务可以从请求头中获取登录用户信息。考虑到微服务内部可能很多地方都需要用到登录用户信息，因此我们可以利用SpringMVC的拦截器来实现登录用户信息获取，并存入ThreadLocal，方便后续使用。

   流程图：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250217175928908.png" alt="image-20250217175928908" style="zoom:50%;" /> 

   因此，接下来要做的事情有：

   - 改造网关过滤器，在获取用户信息后保存到请求头，转发到下游微服务
   - 编写微服务拦截器，拦截请求获取用户信息，保存到ThreadLocal后放行

2. 保存用户到请求头

   首先修改登录校验拦截器的处理逻辑，保存用户信息到请求头中（若要使用需要先去了解一下mutate函数）：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250217180113675.png" alt="image-20250217180113675" style="zoom:50%;" /> 

3. 拦截器获取用户

   由于可能有多个微服务需要获取用户信息，若在每个微服务进行拦截，会增加重复代码。

   考虑到`hm-common`模块被其他微服务引用，可以统一在该模块中设置拦截器。

   在hm-common中已经有一个用于保存登录用户的ThreadLocal工具：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250217180426052.png" alt="image-20250217180426052" style="zoom:33%;" /> 

   接下来，只需要编写拦截器，获取用户信息并保存到`UserContext`，然后放行即可。

   在`hm-common`模块下定义一个拦截器：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250217180531112.png" alt="image-20250217180531112" style="zoom:33%;" /> 

   具体代码：

   ```Java
   package com.hmall.common.interceptor;
   
   import cn.hutool.core.util.StrUtil;
   import com.hmall.common.utils.UserContext;
   import org.springframework.web.servlet.HandlerInterceptor;
   
   import javax.servlet.http.HttpServletRequest;
   import javax.servlet.http.HttpServletResponse;
   
   public class UserInfoInterceptor implements HandlerInterceptor {
       @Override
       public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
           // 1.获取请求头中的用户信息
           String userInfo = request.getHeader("user-info");
           // 2.判断是否为空
           if (StrUtil.isNotBlank(userInfo)) {
               // 不为空，保存到ThreadLocal
                   UserContext.setUser(Long.valueOf(userInfo));
           }
           // 3.放行
           return true;
       }
   
       @Override
       public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
           // 移除用户
           UserContext.removeUser();
       }
   }
   ```

   接着在`hm-common`模块下编写`SpringMVC`的配置类，配置登录拦截器：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250217180613622.png" alt="image-20250217180613622" style="zoom:50%;" /> 

   代码：

   ```Java
   package com.hmall.common.config;
   
   import com.hmall.common.interceptors.UserInfoInterceptor;
   import org.springframework.boot.autoconfigure.condition.ConditionalOnClass;
   import org.springframework.context.annotation.Configuration;
   import org.springframework.web.servlet.DispatcherServlet;
   import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
   import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
   
   @Configuration
   @ConditionalOnClass(DispatcherServlet.class) //条件注解，判断当前项目下是否有SpringMVC的DispatcherServlet，微服务中有而网关中没有。这样当前配置只会在微服务中生效
   public class MvcConfig implements WebMvcConfigurer {
       @Override
       public void addInterceptors(InterceptorRegistry registry) {
           registry.addInterceptor(new UserInfoInterceptor());
       }
   }
   ```

   不过，需要注意的是，这个配置类默认是不会生效的，因为它所在的包是`com.hmall.common.config`，与其它微服务的扫描包不一致，无法被扫描到，因此无法生效。

   基于SpringBoot的自动装配原理，我们要将其添加到`resources`目录下的`META-INF/spring.factories`文件中：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250217180705056.png" alt="image-20250217180705056" style="zoom:33%;" /> 

   内容如下：

   ```Properties
   org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
     com.hmall.common.config.MyBatisConfig,\
     com.hmall.common.config.MvcConfig
   ```

#### 微服务间传递用户信息

1. OpenFeign传递用户

   在下单的过程中，需要调用商品服务扣减库存，调用购物车服务清理用户购物车。而清理购物车时必须知道当前登录的用户身份。但是，**订单服务调用购物车时并没有传递用户信息**，购物车服务无法知道当前用户是谁。

   由于微服务获取用户信息是通过拦截器在请求头中读取，因此要想实现微服务之间的用户信息传递，就**必须在微服务发起调用时把用户信息存入请求头**。

   微服务之间调用是基于OpenFeign来实现的，并不是我们自己发送的请求。让每一个由OpenFeign发起的请求自动携带登录用户信息呢，需要借助Feign中提供的一个拦截器接口：`feign.RequestInterceptor`

   ```Java
   public interface RequestInterceptor {
   
     /**
      * Called for every request. 
      * Add data using methods on the supplied {@link RequestTemplate}.
      */
     void apply(RequestTemplate template);
   }
   ```

   只需要实现这个接口，然后实现apply方法，利用`RequestTemplate`类来添加请求头，将用户信息保存到请求头中。这样每次OpenFeign发起请求的时候都会调用该方法，传递用户信息。

   由于`FeignClient`全部都是在`hm-api`模块，因此在`hm-api`模块的`com.hmall.api.config.DefaultFeignConfig`中编写这个拦截器：

   ```Java
   @Bean
   public RequestInterceptor userInfoRequestInterceptor(){
       return new RequestInterceptor() {
           @Override
           public void apply(RequestTemplate template) {
               // 获取登录用户
               Long userId = UserContext.getUser();
               if(userId == null) {
                   // 如果为空则直接跳过
                   return;
               }
               // 如果不为空则放入请求头中，传递给下游微服务
               template.header("user-info", userId.toString());
           }
       };
   }
   ```

   现在微服务之间通过OpenFeign调用时也会传递登录用户信息了。

2. 总结

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250217183325674.png" alt="image-20250217183325674" style="zoom:50%;" /> 

### 配置管理

#### 介绍&配置共享

1. 介绍

   配置管理的需求来源于几个问题：

   - 网关路由在配置文件中写死了，如果变更必须重启微服务
   - 某些业务配置在配置文件中写死了，每次修改都要重启服务
   - 每个微服务都有很多重复的配置，维护成本高

   这些问题都可以通过统一的**配置管理器服务**解决。而Nacos不仅仅具备注册中心功能，也具备配置管理的功能：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250217185055047.png" alt="image-20250217185055047" style="zoom:33%;" /> 

   微服务共享的配置可以统一交给Nacos保存和管理，在Nacos控制台修改配置后，Nacos会将配置变更推送给相关的微服务，并且无需重启即可生效，实现配置热更新。

   网关的路由同样是配置，因此同样可以基于这个功能实现动态路由功能，无需重启网关即可修改路由配置。

2. 配置共享

   可以把微服务共享的配置抽取到Nacos中统一管理，这样就不需要每个微服务都重复配置了。分为两步：

   - 在Nacos中添加共享配置
   - 微服务拉取配置

   （1）步骤1：添加共享配置

   以cart-service为例，看看有哪些配置是重复的，可以抽取的：jdbc、日志、swagger、OpenFeign

   在nacos控制台分别添加这些配置。

   首先是jdbc相关配置，在`配置管理`->`配置列表`中点击`+`新建一个配置：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250217190023715.png" alt="image-20250217190023715" style="zoom:50%;" /> 

   在弹出的表单中填写信息：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250217190059636.png" alt="image-20250217190059636" style="zoom:43%;" /> 

   其中详细的配置如下：

   ```YAML
   spring:
     datasource:
       url: jdbc:mysql://${hm.db.host:192.168.150.101}:${hm.db.port:3306}/${hm.db.database}?useUnicode=true&characterEncoding=UTF-8&autoReconnect=true&serverTimezone=Asia/Shanghai
       driver-class-name: com.mysql.cj.jdbc.Driver
       username: ${hm.db.un:root}
       password: ${hm.db.pw:123}
   mybatis-plus:
     configuration:
       default-enum-type-handler: com.baomidou.mybatisplus.core.handlers.MybatisEnumTypeHandler
     global-config:
       db-config:
         update-strategy: not_null
         id-type: auto
   ```

   注意这里的jdbc的相关参数并没有写死，例如：

   - `数据库ip`：通过`${hm.db.host:192.168.150.101}`配置了默认值为`192.168.150.101`，同时允许通过`${hm.db.host}`来覆盖默认值
   - `数据库端口`：通过`${hm.db.port:3306}`配置了默认值为`3306`，同时允许通过`${hm.db.port}`来覆盖默认值
   - `数据库database`：可以通过`${hm.db.database}`来设定，无默认值

   然后是统一的日志配置，命名为`shared-log.``yaml`，配置内容如下：

   ```YAML
   logging:
     level:
       com.hmall: debug
     pattern:
       dateformat: HH:mm:ss:SSS
     file:
       path: "logs/${spring.application.name}"
   ```

   然后是统一的swagger配置，命名为`shared-swagger.yaml`，配置内容如下：

   ```YAML
   knife4j:
     enable: true
     openapi:
       title: ${hm.swagger.title:黑马商城接口文档}
       description: ${hm.swagger.description:黑马商城接口文档}
       email: ${hm.swagger.email:zhanghuyi@itcast.cn}
       concat: ${hm.swagger.concat:虎哥}
       url: https://www.itcast.cn
       version: v1.0.0
       group:
         default:
           group-name: default
           api-rule: package
           api-rule-resources:
             - ${hm.swagger.package}
   ```

   注意，这里的swagger相关配置没有写死，例如：

   - `title`：接口文档标题，我们用了`${hm.swagger.title}`来代替，将来可以有用户手动指定
   - `email`：联系人邮箱，我们用了`${hm.swagger.email:``zhanghuyi@itcast.cn``}`，默认值是`zhanghuyi@itcast.cn`，同时允许用户利用`${hm.swagger.email}`来覆盖。

   

   （2）步骤2：拉取共享配置

   接下来，要在微服务拉取共享配置。将拉取到的共享配置与本地的`application.yaml`配置合并，完成项目上下文的初始化。

   不过，需要注意的是，读取Nacos配置是SpringCloud上下文（`ApplicationContext`）初始化时处理的，发生在项目的引导阶段。然后才会初始化SpringBoot上下文，去读取`application.yaml`。

   也就是说引导阶段，`application.yaml`文件尚未读取，根本不知道nacos 地址，该如何去加载nacos中的配置文件呢？

   SpringCloud在初始化上下文的时候会先读取一个名为`bootstrap.yaml`(或者`bootstrap.properties`)的文件，如果我们将nacos地址配置到`bootstrap.yaml`中，那么在项目引导阶段就可以读取nacos中的配置了。  

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250217190457238.png" alt="image-20250217190457238" style="zoom:33%;" /> 

   因此，微服务整合Nacos配置管理的步骤如下：

   1）引入依赖：

   在cart-service模块引入依赖：

   ```XML
     <!--nacos配置管理-->
     <dependency>
         <groupId>com.alibaba.cloud</groupId>
         <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
     </dependency>
     <!--读取bootstrap文件-->
     <dependency>
         <groupId>org.springframework.cloud</groupId>
         <artifactId>spring-cloud-starter-bootstrap</artifactId>
     </dependency>
   ```

   2）新建bootstrap.yaml

   在cart-service中的resources目录新建一个bootstrap.yaml文件：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250217190529859.png" alt="image-20250217190529859" style="zoom:40%;" />

   内容如下：

   ```YAML
   spring:
     application:
       name: cart-service # 服务名称
     profiles:
       active: dev
     cloud:
       nacos:
         server-addr: 192.168.150.101 # nacos地址
         config:
           file-extension: yaml # 文件后缀名
           shared-configs: # 共享配置
             - dataId: shared-jdbc.yaml # 共享mybatis配置
             - dataId: shared-log.yaml # 共享日志配置
             - dataId: shared-swagger.yaml # 共享日志配置
   ```

   3）修改application.yaml

   由于一些配置挪到了bootstrap.yaml，因此application.yaml需要修改为：

   ```YAML
   server:
     port: 8082
   feign:
     okhttp:
       enabled: true # 开启OKHttp连接池支持
   hm:
     swagger:
       title: 购物车服务接口文档
       package: com.hmall.cart.controller
     db:
       database: hm-cart
   ```

   重启服务，发现所有配置都生效了。

#### 配置热更新

有很多的业务相关参数，将来可能会根据实际情况临时调整。例如购物车业务，购物车数量有一个上限，默认是10。现在代码里购物车是写死的固定值，我们应该将其配置在配置文件中，方便后期修改。

但现在的问题是，即便写在配置文件中，修改了配置还是需要重新打包、重启服务才能生效。能不能不用重启，直接生效呢？

这就要用到Nacos的配置热更新能力了，分为两步：

- 在Nacos中添加配置
- 在微服务读取配置

（1）步骤1：添加配置

首先，在nacos中添加一个配置文件，将购物车的上限数量添加到配置中：

<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250217190705045.png" alt="image-20250217190705045" style="zoom:50%;" /> 

文件的Data-Id格式：

```Plain
[服务名]-[spring.active.profile].[后缀名]
```

文件名称由三部分组成：

- **`服务名`**：我们是购物车服务，所以是`cart-service`
- **`spring.active.profile`**：就是spring boot中的`spring.active.profile`，可以省略，则所有profile共享该配置
- **`后缀名`**：例如yaml

这里直接使用`cart-service.yaml`这个名称，则不管是dev还是local环境都可以共享该配置。

配置内容如下：

```YAML
hm:
  cart:
    maxAmount: 1 # 购物车商品数量上限
```

提交配置，在控制台能看到新添加的配置：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250217190901853.png" alt="image-20250217190901853" style="zoom:50%;" /> 



（2）步骤2：读取配置

在微服务中读取配置，实现配置热更新。

在`cart-service`中新建一个属性读取类：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250217190940619.png" alt="image-20250217190940619" style="zoom:33%;" /> 

代码如下：

```Java
package com.hmall.cart.config;

import lombok.Data;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Data
@Component
@ConfigurationProperties(prefix = "hm.cart")
public class CartProperties {
    private Integer maxAmount;
}
```

接着，在业务中使用该属性加载类：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250217191054559.png" alt="image-20250217191054559" style="zoom:33%;" /> 

无需重启服务，配置热更新就生效了。

### 微服务保护

#### 雪崩问题

1. 介绍

   以查询购物车的业务为例，假如商品服务业务并发较高，占用过多Tomcat连接。可能会导致商品服务的所有接口响应时间增加，延迟变高，甚至是长时间阻塞直至查询失败。

   此时查询购物车业务需要查询并等待商品查询结果，从而导致查询购物车列表业务的响应时间也变长，甚至也阻塞直至无法访问。而此时如果查询购物车的请求较多，可能导致购物车服务的Tomcat连接占用较多，所有接口的响应时间都会增加，整个服务性能很差， 甚至不可用。

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218115737534.png" alt="image-20250218115737534" style="zoom:33%;" />

   依次类推，整个微服务群中与购物车服务、商品服务等有调用关系的服务可能都会出现问题，最终导致整个集群不可用。

   这就是**级联失败**问题，或者叫**雪崩**问题。

2. 雪崩问题产生的原因

   - 微服务相互调用，服务提供者出现故障或阻塞。
   - 服务调用者没有做好异常处理，导致自身故障。
   - 调用链中的所有服务级联失败，导致整个集群故障。

3. 解决问题的思路

   - 尽量避免服务出现故障或阻塞。
   - 服务调用者做好远程调用异常的后备方案，避免故障扩散。

4. 常见方案

   主要有3个方案，这些方案都属于服务降级的方案。

   （1）方案1：请求限流

   请求限流，就是**限制或控制**接口访问的并发流量，避免服务因流量激增而出现故障。

   请求限流往往会有一个限流器，数量高低起伏的并发请求曲线，经过限流器就变的非常平稳。这就像是水电站的大坝，起到蓄水的作用，可以通过开关控制水流出的大小，让下游水流始终维持在一个平稳的量。

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218122316189.png" alt="image-20250218122316189" style="zoom:33%;" />

   （2）方案2：线程隔离

   为了避免某个接口故障或压力过大导致整个服务不可用，我们可以限定每个接口可以使用的资源范围，也就是将其“隔离”起来。

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218122413333.png" alt="image-20250218122413333" style="zoom:33%;" />

   如图所示，我们给查询购物车业务限定可用线程数量上限为20，这样即便查询购物车的请求因为查询商品服务而出现故障，也不会导致服务器的线程资源被耗尽，不会影响到其它接口。

   （3）方案3：服务熔断

   线程隔离虽然避免了雪崩问题，但故障服务（商品服务）依然会拖慢购物车服务（服务调用方）的接口响应速度。而且商品查询的故障依然会导致查询购物车功能出现故障，购物车业务也变的不可用了。

   所以，我们要做两件事情：

   - **编写服务降级逻辑**：就是服务调用失败后的处理逻辑，根据业务场景，可以抛出异常，也可以返回友好提示或默认数据。
   - **异常统计和熔断**：统计服务提供方的异常比例，当比例过高表明该接口会影响到其它服务，应该拒绝调用该接口，而是直接走降级逻辑。

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218122609639.png" alt="image-20250218122609639" style="zoom:33%;" />

#### Sentinel快速入门

1. 介绍和安装

   Sentinel 的使用可以分为两个部分:

   - **核心库**（Jar包）：不依赖任何框架/库，能够运行于 Java 8 及以上的版本的运行时环境，同时对 Dubbo / Spring Cloud 等框架也有较好的支持。在项目中引入依赖即可实现服务限流、隔离、熔断等功能。
   - **控制台**（Dashboard）：Dashboard 主要负责管理推送规则、监控、管理机器信息等。

   Sentinel控制台搭建：

   （1）下载jar包

   下载地址：https://github.com/alibaba/Sentinel/releases

   （2）运行

   将jar包放在任意非中文、不包含特殊字符的目录下，重命名为`sentinel-dashboard.jar`。

   然后在cmd运行如下命令启动控制台：

   ```Shell
   java -Dserver.port=8090 -Dcsp.sentinel.dashboard.server=localhost:8090 -Dproject.name=sentinel-dashboard -jar sentinel-dashboard.jar
   ```

   （3）访问

   访问[http://localhost:8090](http://localhost:8080)页面，就可以看到sentinel的控制台了：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218130307966.png" alt="image-20250218130307966" style="zoom:33%;" /> 

   需要输入账号和密码，默认都是：sentinel

   登录后，即可看到控制台，默认会监控sentinel-dashboard服务本身：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218130330363.png" alt="image-20250218130330363" style="zoom:33%;" /> 

2. 微服务整合

   假设在`cart-service`模块中整合sentinel，连接`sentinel-dashboard`控制台，步骤如下：

   （1）引入sentinel依赖

   ```XML
   <!--sentinel-->
   <dependency>
       <groupId>com.alibaba.cloud</groupId> 
       <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
   </dependency>
   ```

   （2）配置控制台

   修改application.yaml文件，添加下面内容：

   ```YAML
   spring:
     cloud: 
       sentinel:
         transport:
           dashboard: localhost:8090
   ```

   （3）访问`cart-service`的任意端点

   重启`cart-service`，然后访问查询购物车接口，sentinel的客户端就会将服务访问的信息提交到`sentinel-dashboard`控制台。并展示出统计信息：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218130727662.png" alt="image-20250218130727662" style="zoom:50%;" /> 

3. 簇点链路

   点击簇点链路菜单，会看到下面的页面：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218130821510.png" alt="image-20250218130821510" style="zoom:50%;" /> 

   所谓簇点链路，就是单机调用链路，是一次请求进入服务后经过的每一个被`Sentinel`监控的资源。默认情况下，`Sentinel`会监控`SpringMVC`的每一个`Endpoint`（接口），**可以理解为一个Controller接口**。

   因此，我们看到`/carts`这个接口路径就是其中一个簇点，我们可以对其进行限流、熔断、隔离等保护措施。

   不过，需要注意的是，我们的SpringMVC接口是按照Restful风格设计，因此购物车的查询、删除、修改等接口全部都是`/carts`路径：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218130928529.png" alt="image-20250218130928529" style="zoom:50%;" /> 

   默认情况下Sentinel会把路径作为簇点资源的名称，无法区分路径相同但请求方式不同的接口，查询、删除、修改等都被识别为一个簇点资源，这显然是不合适的。

   所以我们可以选择打开Sentinel的请求方式前缀，把`请求方式 + 请求路径`作为簇点资源名：

   首先，在`cart-service`的`application.yml`中添加下面的配置：

   ```YAML
   spring:
     cloud:
       sentinel:
         transport:
           dashboard: localhost:8090
         http-method-specify: true # 开启请求方式前缀
   ```

   然后，重启服务，通过页面访问购物车的相关接口，可以看到sentinel控制台的簇点链路发生了变化：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218131008871.png" alt="image-20250218131008871" style="zoom:50%;" /> 

#### Sentinel请求限流&线程隔离

1. 请求限流

   在簇点链路后面点击流控按钮，即可对其做限流配置：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218144033146.png" alt="image-20250218144033146" style="zoom:33%;" /> 

   在弹出的菜单中填写：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218144059590.png" alt="image-20250218144059590" style="zoom:33%;" /> 

   这样就把查询购物车列表这个簇点资源的流量限制在了每秒6个，也就是最大QPS为6。

2. 线程隔离

   限流可以降低服务器压力，尽量减少因并发流量引起的服务故障的概率，但并不能完全避免服务故障。一旦某个服务出现故障，我们必须隔离对这个服务的调用，避免发生雪崩。

   比如，查询购物车的时候需要查询商品，为了避免因商品服务出现故障导致购物车服务级联失败，我们可以把购物车业务中查询商品的部分隔离起来，限制可用的线程资源。这样，即便商品服务出现故障，最多导致查询购物车业务故障，并且可用的线程资源也被限定在一定范围，不会导致整个购物车服务崩溃。

   所以，我们要对查询商品的FeignClient接口做线程隔离。

   （1）OpenFeign整合Sentinel

   修改cart-service模块的application.yml文件，开启Feign的sentinel功能：

   ```YAML
   feign:
     sentinel:
       enabled: true # 开启feign对sentinel的支持
   ```

   配置cart-service模块的application.yml文件，修改tomcat连接：

   ```YAML
   server:
     port: 8082
     tomcat:
       threads:
         max: 50 # 允许的最大线程数
       accept-count: 50 # 最大排队等待数量
       max-connections: 100 # 允许的最大连接
   ```

   然后重启cart-service服务，可以看到查询商品的FeignClient自动变成了一个簇点资源：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218144312635.png" alt="image-20250218144312635" style="zoom:33%;" /> 

   （2）配置线程隔离

   点击查询商品的FeignClient对应的簇点资源后面的流控按钮：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218144350421.png" alt="image-20250218144350421" style="zoom:33%;" /> 

   在弹出的表单中填写：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218144413955.png" alt="image-20250218144413955" style="zoom:33%;" /> 

   这里勾选的是并发线程数限制，也就是说这个查询功能最多使用5个线程，而不是5QPS。如果查询商品的接口每秒处理2个请求，则5个线程的实际QPS在10左右，而超出的请求自然会被拒绝。

   此时如果通过页面访问购物车的其它接口，例如添加购物车、修改购物车商品数量，发现不受影响。

#### Sentinel服务熔断

1. fallback降级

   触发限流或熔断后的请求不一定要直接报错，也可以返回一些默认数据或者友好提示，用户体验会更好。

   给FeignClient编写失败后的降级逻辑有两种方式：

   - 方式一：FallbackClass，无法对远程调用的异常做处理
   - 方式二：FallbackFactory，可以对远程调用的异常做处理，一般选择这种方式。

   这里演示方式二的失败降级处理：

   （1）步骤1：在hm-api模块中给`ItemClient`定义降级处理类，实现`FallbackFactory`

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218144610389.png" alt="image-20250218144610389" style="zoom:33%;" /> 

   代码如下：

   ```Java
   package com.hmall.api.client.fallback;
   
   import com.hmall.api.client.ItemClient;
   import com.hmall.api.dto.ItemDTO;
   import com.hmall.api.dto.OrderDetailDTO;
   import com.hmall.common.exception.BizIllegalException;
   import com.hmall.common.utils.CollUtils;
   import lombok.extern.slf4j.Slf4j;
   import org.springframework.cloud.openfeign.FallbackFactory;
   
   import java.util.Collection;
   import java.util.List;
   
   @Slf4j
   public class ItemClientFallback implements FallbackFactory<ItemClient> {
       @Override
       public ItemClient create(Throwable cause) {
           return new ItemClient() {
               @Override
               public List<ItemDTO> queryItemByIds(Collection<Long> ids) {
                   log.error("远程调用ItemClient#queryItemByIds方法出现异常，参数：{}", ids, cause);
                   // 查询购物车允许失败，查询失败，返回空集合
                   return CollUtils.emptyList();
               }
   
               @Override
               public void deductStock(List<OrderDetailDTO> items) {
                   // 库存扣减业务需要触发事务回滚，查询失败，抛出异常
                   throw new BizIllegalException(cause);
               }
           };
       }
   }
   ```

   （2）步骤2：在`hm-api`模块中的`com.hmall.api.config.DefaultFeignConfig`类中将`ItemClientFallback`注册为一个`Bean`

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218144644821.png" alt="image-20250218144644821" style="zoom:33%;" /> 

   （3）步骤3：在`hm-api`模块中的`ItemClient`接口中使用`ItemClientFallbackFactory`

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218144718985.png" alt="image-20250218144718985" style="zoom:43%;" /> 

   重启后，再次测试，发现被限流的请求不再报错，走了降级逻辑：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218144746462.png" alt="image-20250218144746462" style="zoom:50%;" /> 

2. 服务熔断

   对于商品服务这种不太健康的接口，应该停止调用，直接走降级逻辑，避免影响到当前服务。也就是将商品查询接口**熔断**。当商品服务接口恢复正常后，再允许调用。这其实就是**断路器**的工作模式了。

   Sentinel中的断路器不仅可以统计某个接口的**慢请求比例**，还可以统计**异常请求比例**。当这些比例超出阈值时，就会**熔断**该接口，即拦截访问该接口的一切请求，降级处理；当该接口恢复正常时，再放行对于该接口的请求。

   断路器的工作状态切换有一个状态机来控制：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218144845463.png" alt="image-20250218144845463" style="zoom:43%;" />

   状态机包括三个状态：

   - **closed**：关闭状态，断路器放行所有请求，并开始统计异常比例、慢请求比例。超过阈值则切换到open状态
   - **open**：打开状态，服务调用被**熔断**，访问被熔断服务的请求会被拒绝，快速失败，直接走降级逻辑。Open状态持续一段时间后会进入half-open状态
   - **half-open**：半开状态，放行一次请求，根据执行结果来判断接下来的操作。 
     - 请求成功：则切换到closed状态
     - 请求失败：则切换到open状态

   可以在控制台通过点击簇点后的**`熔断`**按钮来配置熔断策略：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218144920240.png" alt="image-20250218144920240" style="zoom:33%;" /> 

   在弹出的表格中填写：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218144942464.png" alt="image-20250218144942464" style="zoom:33%;" /> 

   这种是按照慢调用比例来做熔断，上述配置的含义是：

   - RT超过200毫秒的请求调用就是慢调用
   - 统计最近1000ms内的最少5次请求，如果慢调用比例不低于0.5，则触发熔断
   - 熔断持续时长20s

### 分布式事务

#### 介绍&Seata

1. 分布式事务介绍

   在分布式系统中，如果一个业务需要多个服务合作完成，而且每一个服务都有事务，多个事务必须同时成功或失败，这样的事务就是分布式事务。其中的每个服务的事务就是一个**分支事务**，整个业务称为**全局事务**。

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218152953189.png" alt="image-20250218152953189" style="zoom:50%;" />

   如上，订单服务调用购物车服务和库存服务。当库存服务失败，订单服务和库存服务本身有感知，但购物车服务并不会感知，并不能遵循ACID的原则。

   这就是分布式事务问题，出现以下情况之一就可能产生分布式事务问题：

   - 业务跨多个服务实现
   - 业务跨多个数据源实现

   微服务需要能够解决分布式事务问题。

2. Seata

   解决分布式事务的思想非常简单：就是找一个统一的**事务协调者**，与多个分支事务通信，检测每个分支事务的执行状态，保证全局事务下的每一个分支事务同时成功或失败即可。大多数的分布式事务框架都是基于这个理论来实现的。

   在Seata的事务管理中有三个重要的角色：

   -  **TC (Transaction Coordinator) -事务协调者：**维护全局和分支事务的状态，协调全局事务提交或回滚。 
   -  **TM (Transaction Manager) -** **事务管理器：**定义全局事务的范围、开始全局事务、提交或回滚全局事务。即告诉TC全局事务的入口和结束处。 
   -  **RM (Resource Manager) -** **资源管理器：**管理分支事务，与TC交谈以注册分支事务和报告分支事务的状态，并驱动分支事务提交或回滚。 

   Seata架构：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218155536343.png" alt="image-20250218155536343" style="zoom:50%;" /> 

   其中，**TM**和**RM**可以理解为Seata的客户端部分，引入到参与事务的微服务依赖中即可。将来**TM**和**RM**就会协助微服务，实现本地分支事务与**TC**之间交互，实现事务的提交或回滚。

   而**TC**服务则是事务协调中心，是一个独立的微服务，需要单独部署。

#### TC部署&微服务集成Seata

1. 部署TC服务

   （1）准备数据库表

   Seata支持多种存储模式，但考虑到持久化的需要，一般选择基于数据库存储。执行课前资料提供的`《seata-tc.sql》`，导入数据库表：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218160755807.png" alt="image-20250218160755807" style="zoom:33%;" /> 

   （2）准备配置文件

   课前资料准备了一个seata目录，其中包含了seata运行时所需要的配置文件：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218160914459.png" alt="image-20250218160914459" style="zoom:33%;" />

   将整个seata文件夹拷贝到虚拟机的`/root`目录：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218160942643.png" alt="image-20250218160942643" style="zoom:43%;" /> 

   （3）Docker部署

   要确保nacos、mysql都在hm-net网络中。如果某个容器不在hm-net网络，可以参考下面的命令将某容器加入指定网络：

   ```Shell
   docker network connect [网络名] [容器名]
   ```

   在虚拟机的`/root`目录执行下面的命令：

   ```Shell
   docker run --name seata \
   -p 8099:8099 \
   -p 7099:7099 \
   -e SEATA_IP=192.168.150.101 \
   -v ./seata:/seata-server/resources \
   --privileged=true \
   --network hm-net \
   -d \
   seataio/seata-server:1.5.2
   ```

2. 微服务集成Seata

   参与分布式事务的每一个微服务都需要集成Seata，以`trade-service`为例。

   （1）引入依赖

   为了方便各个微服务集成seata，需要把seata配置共享到nacos，因此`trade-service`模块不仅仅要引入seata依赖，还要引入nacos依赖:

   ```XML
   <!--统一配置管理-->
     <dependency>
         <groupId>com.alibaba.cloud</groupId>
         <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
     </dependency>
     <!--读取bootstrap文件-->
     <dependency>
         <groupId>org.springframework.cloud</groupId>
         <artifactId>spring-cloud-starter-bootstrap</artifactId>
     </dependency>
     <!--seata-->
     <dependency>
         <groupId>com.alibaba.cloud</groupId>
         <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
     </dependency>
   ```

   （2）改造配置

   首先在nacos上添加一个共享的seata配置，命名为`shared-seata.yaml`：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218161210188.png" alt="image-20250218161210188" style="zoom:50%;" /> 

   内容如下：

   ```YAML
   seata:
     registry: # TC服务注册中心的配置，微服务根据这些信息去注册中心获取tc服务地址
       type: nacos # 注册中心类型 nacos
       nacos:
         server-addr: 192.168.150.101:8848 # nacos地址
         namespace: "" # namespace，默认为空
         group: DEFAULT_GROUP # 分组，默认是DEFAULT_GROUP
         application: seata-server # seata服务名称
         username: nacos
         password: nacos
     tx-service-group: hmall # 事务组名称
     service:
       vgroup-mapping: # 事务组与tc集群的映射关系
         hmall: "default"
   ```

   然后，改造`trade-service`模块，添加`bootstrap.yaml`：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218161231492.png" alt="image-20250218161231492" style="zoom:33%;" /> 

   内容如下:

   ```YAML
   spring:
     application:
       name: trade-service # 服务名称
     profiles:
       active: dev
     cloud:
       nacos:
         server-addr: 192.168.150.101 # nacos地址
         config:
           file-extension: yaml # 文件后缀名
           shared-configs: # 共享配置
             - dataId: shared-jdbc.yaml # 共享mybatis配置
             - dataId: shared-log.yaml # 共享日志配置
             - dataId: shared-swagger.yaml # 共享日志配置
             - dataId: shared-seata.yaml # 共享seata配置
   ```

   可以看到这里加载了共享的seata配置。

   然后改造application.yaml文件，内容如下：

   ```YAML
   server:
     port: 8085
   feign:
     okhttp:
       enabled: true # 开启OKHttp连接池支持
     sentinel:
       enabled: true # 开启Feign对Sentinel的整合
   hm:
     swagger:
       title: 交易服务接口文档
       package: com.hmall.trade.controller
     db:
       database: hm-trade
   ```

   参考上述办法分别改造`hm-cart`和`hm-item`两个微服务模块。

   （3）添加数据库表

   seata的客户端在解决分布式事务的时候需要记录一些中间数据，保存在数据库中。因此要先准备一个这样的表。

   将课前资料的seata-at.sql分别文件导入hm-trade、hm-cart、hm-item三个数据库中：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218161336110.png" alt="image-20250218161336110" style="zoom:43%;" /> 

   （4）测试

   找到`trade-service`模块下的`com.hmall.trade.service.impl.OrderServiceImpl`类中的`createOrder`方法，也就是下单业务方法。

   将其上的`@Transactional`注解改为Seata提供的`@GlobalTransactional`：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218161503254.png" alt="image-20250218161503254" style="zoom:33%;" /> 

   `@GlobalTransactional`注解就是在标记事务的起点，将来TM就会基于这个方法判断全局事务范围，初始化全局事务。

   重启`trade-service`、`item-service`、`cart-service`三个服务。再次测试，发现分布式事务的问题解决了。

#### XA模式

1. 介绍

   Seata支持四种不同的分布式事务解决方案：

   - **XA**
   - **TCC**
   - **AT**
   - **SAGA**

   `XA` 规范 是` X/Open` 组织定义的分布式事务处理（DTP，Distributed Transaction Processing）标准，XA 规范 描述了全局的`TM`与局部的`RM`之间的接口，几乎所有主流的数据库都对 XA 规范 提供了支持。

2. 两阶段提交

   要实现XA模式，原理要基于两阶段提交。

   正常情况：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218163831960.png" alt="image-20250218163831960" style="zoom:43%;" /> 

   异常情况：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218163905847.png" alt="image-20250218163905847" style="zoom:43%;" /> 

   一阶段：

   - 事务协调者通知每个事务参与者执行本地事务
   - 本地事务执行完成后报告事务执行状态给事务协调者，此时事务不提交，继续持有数据库锁

   二阶段：

   - 事务协调者基于一阶段的报告来判断下一步操作
   - 如果一阶段都成功，则通知所有事务参与者，提交事务
   - 如果一阶段任意一个参与者失败，则通知所有事务参与者回滚事务

3. Seata的XA模型

   Seata对原始的XA模式做了简单的封装和改造，以适应自己的事务模型，基本架构如图：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218164049465.png" alt="image-20250218164049465" style="zoom:50%;" />

   一阶段的工作：

   1. `RM`注册分支事务到`TC`
   2. `RM`执行分支业务sql但不提交
   3. `RM`报告执行状态到`TC`

   二阶段的工作：

   1.  `TC`检测各分支事务执行状态
      1. 如果都成功，通知所有RM提交事务
      2. 如果有失败，通知所有RM回滚事务 
   2. `RM`接收`TC`指令，提交或回滚事务

4. XA模式的优缺点

   优点：

   - 事务的强一致性，满足ACID原则
   - 常用数据库都支持，实现简单，并且没有代码侵入

   缺点：

   - 因为一阶段需要锁定数据库资源，等待二阶段结束才释放，性能较差
   - 依赖关系型数据库实现事务

5. 实现步骤

   首先，在配置文件中指定要采用的分布式事务模式。可以在Nacos中的共享shared-seata.yaml配置文件中设置：

   ```YAML
   seata:
     data-source-proxy-mode: XA
   ```

   其次，要利用`@GlobalTransactional`标记分布式事务的入口方法：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218164623071.png" alt="image-20250218164623071" style="zoom:40%;" /> 

#### AT模式

1. Seata的AT模型

   `AT`模式同样是分阶段提交的事务模型，不过缺弥补了`XA`模型中资源锁定周期过长的缺陷。

   基本架构如下：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218170523004.png" alt="image-20250218170523004" style="zoom:50%;" />

   阶段一`RM`的工作：

   - 注册分支事务
   - 记录undo-log（数据快照，即对原数据进行备份）
   - 执行业务sql并提交
   - 报告事务状态

   阶段二提交时`RM`的工作：

   - 删除undo-log即可

   阶段二回滚时`RM`的工作：

   - 根据undo-log恢复数据到更新前

2. AT与XA的对比

   - `XA`模式一阶段不提交事务，锁定资源；`AT`模式一阶段直接提交，不锁定资源。
   - `XA`模式依赖数据库机制实现回滚；`AT`模式利用数据快照实现数据回滚。
   - `XA`模式强一致；`AT`模式**最终一致**（在一阶段提交到二阶段最终删除log或恢复的这一小段时间里，AT模式数据不一致）

   可见，AT模式使用起来更加简单，无业务侵入，性能更好。因此企业90%的分布式事务都可以用AT模式来解决。

   **注意**：不配置默认是AT模式。

## ElasticSearch

### 初识ES

#### 认识和安装

1. ES和数据库查询

   当使用数据库模糊查询，由于模糊查询不走索引，在数据量较大的时候，查询性能很差。黑马商城的商品表中仅仅有不到9万条数据，基于数据库查询时，搜索接口的表现如图：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218202028203.png" alt="image-20250218202028203" style="zoom:43%;" /> 

   改为基于搜索引擎后，查询表现如下：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218202057475.png" alt="image-20250218202057475" style="zoom:43%;" /> 

   需要注意的是，数据库模糊查询随着表数据量的增多，查询性能的下降会非常明显，而搜索引擎的性能则不会随着数据增多而下降太多。目前仅10万不到的数据量差距就如此明显，如果数据量达到百万、千万、甚至上亿级别，这个性能差距会非常夸张。

   **其次**，功能单一

   数据库的模糊搜索功能单一，匹配条件非常苛刻，必须恰好包含用户搜索的关键字。而在搜索引擎中，用户输入出现个别错字，或者用拼音搜索、同义词搜索都能正确匹配到数据。

   综上，在面临海量数据的搜索，或者有一些复杂搜索需求的时候，推荐使用专门的搜索引擎来实现搜索功能。

2. ES认识

   Elasticsearch是由elastic公司开发的一套搜索引擎技术，它是elastic技术栈中的一部分。完整的技术栈包括：

   - Elasticsearch：用于数据存储、计算和搜索
   - Logstash/Beats：用于数据收集
   - Kibana：用于数据可视化

   整套技术栈被称为ELK，经常用来做日志收集、系统监控和状态分析等等。

   Elasticsearch不用多说，是提供核心的数据存储、搜索、分析功能的。

   然后是Kibana，Elasticsearch对外提供的是Restful风格的API，任何操作都可以通过发送http请求来完成。不过http请求的方式、路径、还有请求参数的格式都有严格的规范。这些规范我们肯定记不住，因此我们要借助于Kibana这个服务。

   Kibana是elastic公司提供的用于操作Elasticsearch的可视化控制台。它的功能非常强大，包括：

   - 对Elasticsearch数据的搜索、展示
   - 对Elasticsearch数据的统计、聚合，并形成图形化报表、图形
   - 对Elasticsearch的集群状态监控
   - 它还提供了一个开发控制台（DevTools），在其中对Elasticsearch的Restful的API接口提供了**语法提示**。

3. 安装

   （1）安装ElasticSearch

   通过下面的Docker命令即可安装单机版本的elasticsearch：

   ```Bash
   docker run -d \
     --name es \
     -e "ES_JAVA_OPTS=-Xms512m -Xmx512m" \
     -e "discovery.type=single-node" \
     -v es-data:/usr/share/elasticsearch/data \
     -v es-plugins:/usr/share/elasticsearch/plugins \
     --privileged \
     --network hm-net \
     -p 9200:9200 \
     -p 9300:9300 \
     elasticsearch:7.12.1
   ```

   安装完成后，访问9200端口，即可看到响应的Elasticsearch服务的基本信息：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218202425502.png" alt="image-20250218202425502" style="zoom:33%;" /> 

   （2）安装Kibana

   通过下面的Docker命令，即可部署Kibana：

   ```Bash
   docker run -d \
   --name kibana \
   -e ELASTICSEARCH_HOSTS=http://es:9200 \
   --network=hm-net \
   -p 5601:5601  \
   kibana:7.12.1
   ```

   安装完成后，直接访问5601端口，即可看到控制台页面：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218202509019.png" alt="image-20250218202509019" style="zoom:33%;" /> 

   选择`Explore on my own`之后，进入主页面：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218202533641.png" alt="image-20250218202533641" style="zoom:33%;" /> 

   然后选中`Dev tools`，进入开发工具页面：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250218202605617.png" alt="image-20250218202605617" style="zoom:33%;" /> 

#### 倒排索引

1. 正向索引

   例如有一张名为`tb_goods`的表：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250219124556329.png" alt="image-20250219124556329" style="zoom:33%;" /> 

   其中的`id`字段已经创建了索引，由于索引底层采用了B+树结构，因此我们根据id搜索的速度会非常快。但是其他字段例如`title`，只在叶子节点上存在。因此要根据`title`搜索的时候只能遍历树中的每一个叶子节点，判断title数据是否符合要求。

   综上，根据id精确匹配时，可以走索引，查询效率较高。而当搜索条件为模糊匹配时，由于索引无法生效，导致从索引查询退化为全表扫描，效率很差。

   因此，正向索引适合于根据索引字段的精确搜索，不适合基于部分词条的模糊匹配。

2. 倒排索引

   倒排索引中有两个非常重要的概念：

   - 文档（`Document`）：用来搜索的数据，其中的每一条数据就是一个文档。
   - 词条（`Term`）：对文档数据或用户搜索数据，利用某种算法分词，得到的具备含义的词语就是词条。例如：我是中国人，就可以分为：我、是、中国人、中国、国人这样的几个词条。

   **创建倒排索引**是对正向索引的一种特殊处理和应用，流程如下：

   - 将每一个文档的数据利用**分词算法**根据语义拆分，得到一个个词条
   - 创建表，每行数据包括词条、词条所在文档id、位置等信息
   - 因为词条唯一性，可以给**词条**创建**正向**索引

   此时形成的这张以词条为索引的表，就是倒排索引表，两者对比如下：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250219124911492.png" alt="image-20250219124911492" style="zoom:33%;" />

   倒排索引的**搜索流程**如下（以搜索"华为手机"为例），如图：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250219124935166.png" alt="image-20250219124935166" style="zoom:33%;" /> 

   流程描述：

   1）用户输入条件`"华为手机"`进行搜索。

   2）对用户输入条件**分词**，得到词条：`华为`、`手机`。

   3）拿着词条在倒排索引中查找（**由于词条有索引，查询效率很高**），即可得到包含词条的文档id：`1、2、3`。

   4）拿着文档`id`到正向索引中查找具体文档即可（由于`id`也有索引，查询效率也很高）。

   虽然要先查询倒排索引，再查询倒排索引，但是无论是**词条**、还是**文档id都建立了索引**，查询速度非常快。

3. 正向和倒排

   **正向索引**是最传统的，根据id索引的方式。但根据词条查询时，必须先逐条获取每个文档，然后判断文档中是否包含所需要的词条，是**根据文档找词条的过程**。  

   **倒排索引**则相反，是先找到用户要搜索的词条，根据词条得到保护词条的文档的id，然后根据id获取文档。是**根据词条找文档的过程**。

   正向索引：

   - 优点： 
     - 可以给多个字段创建索引
     - 根据索引字段搜索、排序速度非常快
   - 缺点： 
     - 根据非索引字段，或者索引字段中的部分词条查找时，只能全表扫描。

   倒排索引：

   - 优点： 
     - 根据词条搜索、模糊搜索时，速度非常快
   - 缺点： 
     - 只能给词条创建索引，而不是字段
     - 无法根据字段做排序

#### IK分词器

1. 介绍

   Elasticsearch的关键就是倒排索引，而倒排索引依赖于对文档内容的分词，而分词则需要高效、精准的分词算法，IK分词器就是这样一个中文分词算法。

2. 安装

   （1）方案一：在线安装

   运行一个命令即可：

   ```Shell
   docker exec -it es ./bin/elasticsearch-plugin  install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.12.1/elasticsearch-analysis-ik-7.12.1.zip
   ```

   然后重启es容器：

   ```Shell
   docker restart es
   ```

   （2）方案二：离线安装

   如果网速较差，也可以选择离线安装。

   首先，查看之前安装的Elasticsearch容器的plugins数据卷目录：

   ```Shell
   docker volume inspect es-plugins
   ```

   结果如下：

   ```JSON
   [
       {
           "CreatedAt": "2024-11-06T10:06:34+08:00",
           "Driver": "local",
           "Labels": null,
           "Mountpoint": "/var/lib/docker/volumes/es-plugins/_data",
           "Name": "es-plugins",
           "Options": null,
           "Scope": "local"
       }
   ]
   ```

   可以看到elasticsearch的插件挂载到了`/var/lib/docker/volumes/es-plugins/_data`这个目录。我们需要把IK分词器上传至这个目录。

   找到课前资料提供的ik分词器插件，课前资料提供了`7.12.1`版本的ik分词器压缩文件，你需要对其解压：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250219141322239.png" alt="image-20250219141322239" style="zoom:33%;" />

   然后上传至虚拟机的`/var/lib/docker/volumes/es-plugins/_data`。

   最后，重启es容器：

   ```Shell
   docker restart es
   ```

3. 使用IK分词器

   IK分词器包含两种模式：

   -  `ik_smart`：智能语义切分 
   -  `ik_max_word`：最细粒度切分 

   在Kibana的DevTools上来测试分词器，首先测试Elasticsearch官方提供的标准分词器：

   ```JSON
   POST /_analyze
   {
     "analyzer": "standard",
     "text": "黑马程序员学习java太棒了"
   }
   ```

   测试IK分词器：

   ```JSON
   POST /_analyze
   {
     "analyzer": "ik_smart",
     "text": "黑马程序员学习java太棒了"
   }
   ```

4. 拓展词典

   随着互联网的发展，“造词运动”也越发的频繁。出现了很多新的词语，在原有的词汇列表中并不存在。比如：“泰裤辣”，“传智播客” 等。IK分词器无法对这些词汇分词，要想正确分词，IK分词器的词库也需要不断的更新，IK分词器提供了扩展词汇的功能。

   （1）步骤1：打开IK分词器config目录

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250219141927585.png" alt="image-20250219141927585" style="zoom:33%;" />

   （2）步骤2：在IKAnalyzer.cfg.xml配置文件内容添加：

   ```XML
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
   <properties>
           <comment>IK Analyzer 扩展配置</comment>
           <!--用户可以在这里配置自己的扩展字典 *** 添加扩展词典-->
           <entry key="ext_dict">ext.dic</entry>
   </properties>
   ```

   （3）步骤3：在IK分词器的config目录新建一个 `ext.dic`，可以参考config目录下复制一个配置文件进行修改

   ```Plain
   传智播客
   泰裤辣
   ```

   （4）步骤4：重启elasticsearch

   ```Shell
   docker restart es
   
   # 查看 日志
   docker logs -f elasticsearch
   ```

   再次测试，可以发现`传智播客`和`泰裤辣`都正确分词了。

5. 总结

   分词器的作用：

   - 创建倒排索引时，对文档分词
   - 用户搜索时，对输入的内容分词

   IK分词器模式：

   - `ik_smart`：智能切分，粗粒度
   - `ik_max_word`：最细切分，细粒度

   IK分词器如何拓展词条？如何停用词条？

   - 利用config目录的`IkAnalyzer.cfg.xml`文件添加拓展词典和停用词典
   - 在词典中添加拓展词条或者停用词条

#### 基础概念

1. 文档和字段

   elasticsearch是面向**文档（Document）**存储的，可以是数据库中的一条商品数据，一个订单信息。文档数据会被序列化为`json`格式后存储在`elasticsearch`中：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250219142507585.png" alt="image-20250219142507585" style="zoom:33%;" />

   因此，原本数据库中的一行数据就是ES中的一个JSON文档；而数据库中每行数据都包含很多列，这些列就转换为JSON文档中的**字段（Field）**。

2. 索引和映射

   随着业务发展，需要在es中存储的文档也会越来越多，比如有商品的文档、用户的文档、订单文档等，所有文档都散乱存放显然非常混乱，也不方便管理。因此，要将类型相同的文档集中在一起管理，称为**索引（Index）**。

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250219142622834.png" alt="image-20250219142622834" style="zoom:33%;" />

   可以把索引当做是数据库中的表。

   数据库的表会有约束信息，用来定义表的结构、字段的名称、类型等信息。因此，索引库中就有**映射（mapping）**，是索引中文档的字段约束信息，类似表的结构约束。

3. MySQL对比ES

   | **MySQL** | **Elasticsearch** | **说明**                                                     |
   | :-------- | :---------------- | :----------------------------------------------------------- |
   | Table     | Index             | 索引(index)，就是文档的集合，类似数据库的表(table)           |
   | Row       | Document          | 文档（Document），就是一条条的数据，类似数据库中的行（Row），文档都是JSON格式 |
   | Column    | Field             | 字段（Field），就是JSON文档中的字段，类似数据库中的列（Column） |
   | Schema    | Mapping           | Mapping（映射）是索引中文档的约束，例如字段类型约束。类似数据库的表结构（Schema） |
   | SQL       | DSL               | DSL是elasticsearch提供的JSON风格的请求语句，用来操作elasticsearch，实现CRUD |

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250219142750500.png" alt="image-20250219142750500" style="zoom:50%;" />

   -  Mysql：擅长事务类型操作，可以确保数据的安全和一致性 
   -  Elasticsearch：擅长海量数据的搜索、分析、计算 

   在企业中，往往是两者结合使用：

   - 对安全性要求较高的写操作，使用mysql实现
   - 对查询性能要求较高的搜索需求，使用elasticsearch实现
   - 两者再基于某种方式，实现数据的同步，保证一致性

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250219142831579.png" alt="image-20250219142831579" style="zoom:33%;" />

### 索引库操作

#### Mapping映射属性

1. 属性

   Mapping是对索引库中文档的约束，常见的Mapping属性包括：

   - `type`：字段数据类型，常见的简单类型有： 
     - 字符串：`text`（可分词的文本）、`keyword`（精确值，例如：品牌、国家、ip地址）
     - 数值：`long`、`integer`、`short`、`byte`、`double`、`float`、
     - 布尔：`boolean`
     - 日期：`date`
     - 对象：`object`
   - `index`：是否创建索引，默认为`true`
   - `analyzer`：使用哪种分词器
   - `properties`：该字段的子字段

2. 举例

   例如下面的json文档：

   ```JSON
   {
       "age": 21,
       "weight": 52.1,
       "isMarried": false,
       "info": "黑马程序员Java讲师",
       "email": "zy@itcast.cn",
       "score": [99.1, 99.5, 98.9],
       "name": {
           "firstName": "云",
           "lastName": "赵"
       }
   }
   ```

   对应的每个字段映射（Mapping）：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250219151035085.png" alt="image-20250219151035085" style="zoom:33%;" /> 

#### 索引库CRUD

由于Elasticsearch采用的是Restful风格的API，因此其请求方式和路径相对都比较规范，而且请求参数也都采用JSON风格。

直接基于Kibana的DevTools来编写请求做测试，由于有语法提示，会非常方便。

1. 创建索引库和映射

   基本语法：

   - 请求方式：`PUT`
   - 请求路径：`/索引库名`，可以自定义
   - 请求参数：`mapping`映射

   格式：

   ```JSON
   PUT /索引库名称
   {
     "mappings": {
       "properties": {
         "字段名":{
           "type": "text",
           "analyzer": "ik_smart"
         },
         "字段名2":{
           "type": "keyword",
           "index": "false"
         },
         "字段名3":{
           "properties": {
             "子字段": {
               "type": "keyword"
             }
           }
         },
         // ...略
       }
     }
   }
   ```

   示例：

   ```JSON
   # PUT /heima
   {
     "mappings": {
       "properties": {
         "info":{
           "type": "text",
           "analyzer": "ik_smart"
         },
         "email":{
           "type": "keyword",
           "index": "false"
         },
         "name":{
           "properties": {
             "firstName": {
               "type": "keyword"
             }
           }
         }
       }
     }
   }
   ```

2. 查询索引库

   基本语法：

   -  请求方式：GET 
   -  请求路径：/索引库名 
   -  请求参数：无 

   格式：

   ```Plain
   GET /索引库名
   ```

   示例：

   ```Plain
   GET /heima
   ```

3. 删除索引库

   基本语法：

   -  请求方式：DELETE 
   -  请求路径：/索引库名 
   -  请求参数：无 

   格式：

   ```Plain
   DELETE /索引库名
   ```

   示例：

   ```Plain
   DELETE /heima
   ```

4. 修改索引库

   索引库一经建立，无法修改。

   虽然无法修改mapping中已有的字段，但是却允许添加新的字段到mapping中，因为不会对倒排索引产生影响。因此修改索引库能做的就是向索引库中添加新字段，或者更新索引库的基础属性。

   语法说明：

   ```JSON
   PUT /索引库名/_mapping
   {
     "properties": {
       "新字段名":{
         "type": "integer"
       }
     }
   }
   ```

   示例：

   ```JSON
   PUT /heima/_mapping
   {
     "properties": {
       "age":{
         "type": "integer"
       }
     }
   }
   ```

### 文档操作

Elasticsearch中的数据其实就是JSON风格的文档。操作文档自然保护`增`、`删`、`改`、`查`等几种常见操作，分别来学习。

1. 新增文档

   语法：

   ```JSON
   POST /索引库名/_doc/文档id
   {
       "字段1": "值1",
       "字段2": "值2",
       "字段3": {
           "子属性1": "值3",
           "子属性2": "值4"
       },
   }
   ```

   示例：

   ```JSON
   POST /heima/_doc/1
   {
       "info": "黑马程序员Java讲师",
       "email": "zy@itcast.cn",
       "name": {
           "firstName": "云",
           "lastName": "赵"
       }
   }
   ```

   响应：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250219152022125.png" alt="image-20250219152022125" style="zoom:43%;" /> 

2. 查询文档

   语法：

   ```JSON
   GET /{索引库名称}/_doc/{id}
   ```

   示例：

   ```JavaScript
   GET /heima/_doc/1
   ```

   查看结果：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250219152137993.png" alt="image-20250219152137993" style="zoom:43%;" /> 

3. 删除文档

   删除使用DELETE请求，同样，需要根据id进行删除：

   语法：

   ```JavaScript
   DELETE /{索引库名}/_doc/id值
   ```

   示例：

   ```JSON
   DELETE /heima/_doc/1
   ```

   结果：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250219152219926.png" alt="image-20250219152219926" style="zoom:43%;" /> 

4. 修改文档

   修改有两种方式：

   - 全量修改：直接覆盖原来的文档
   - 局部修改：修改文档中的部分字段

   （1）全量修改

   全量修改是覆盖原来的文档，其本质是两步操作：

   - 根据指定的id删除文档
   - 新增一个相同id的文档

   **注意**：如果根据id删除时，id不存在，第二步的新增也会执行，也就从修改变成了新增操作了。

   语法：

   ```JSON
   PUT /{索引库名}/_doc/文档id
   {
       "字段1": "值1",
       "字段2": "值2",
       // ... 略
   }
   ```

   示例：

   ```JSON
   PUT /heima/_doc/1
   {
       "info": "黑马程序员高级Java讲师",
       "email": "zy@itcast.cn",
       "name": {
           "firstName": "云",
           "lastName": "赵"
       }
   }
   ```

   由于`id`为`1`的文档已经被删除，所以第一次执行时，得到的反馈是`created`：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250219152401495.png" alt="image-20250219152401495" style="zoom:43%;" /> 

   所以如果执行第2次时，得到的反馈则是`updated`：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250219152431456.png" alt="image-20250219152431456" style="zoom:43%;" /> 

   （2）局部修改

   局部修改是只修改指定id匹配的文档中的部分字段。

   语法：

   ```JSON
   POST /{索引库名}/_update/文档id
   {
       "doc": {
            "字段名": "新的值",
       }
   }
   ```

   示例：

   ```JSON
   POST /heima/_update/1
   {
     "doc": {
       "email": "ZhaoYun@itcast.cn"
     }
   }
   ```

   执行结果：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250219152540947.png" alt="image-20250219152540947" style="zoom:43%;" /> 

5. 批处理

   批处理采用POST请求，基本语法如下：

   ```Java
   POST _bulk
   { "index" : { "_index" : "test", "_id" : "1" } }
   { "field1" : "value1" }
   { "delete" : { "_index" : "test", "_id" : "2" } }
   { "create" : { "_index" : "test", "_id" : "3" } }
   { "field1" : "value3" }
   { "update" : {"_id" : "1", "_index" : "test"} }
   { "doc" : {"field2" : "value2"} }
   ```

   其中：

   - `index`代表新增操作
     - `_index`：指定索引库名
     - `_id`指定要操作的文档id
     - `{ "field1" : "value1" }`：则是要新增的文档内容
   - `delete`代表删除操作
     - `_index`：指定索引库名
     - `_id`指定要操作的文档id
   - `update`代表更新操作
     - `_index`：指定索引库名
     - `_id`指定要操作的文档id
     - `{ "doc" : {"field2" : "value2"} }`：要更新的文档字段

   示例，批量新增：

   ```Java
   POST /_bulk
   {"index": {"_index":"heima", "_id": "3"}}
   {"info": "黑马程序员C++讲师", "email": "ww@itcast.cn", "name":{"firstName": "五", "lastName":"王"}}
   {"index": {"_index":"heima", "_id": "4"}}
   {"info": "黑马程序员前端讲师", "email": "zhangsan@itcast.cn", "name":{"firstName": "三", "lastName":"张"}}
   ```

   批量删除：

   ```Java
   POST /_bulk
   {"delete":{"_index":"heima", "_id": "3"}}
   {"delete":{"_index":"heima", "_id": "4"}}
   ```

6. 总结

   文档操作：

   - 创建文档：`POST /{索引库名}/_doc/文档id   { json文档 }`
   - 查询文档：`GET /{索引库名}/_doc/文档id`
   - 删除文档：`DELETE /{索引库名}/_doc/文档id`
   - 修改文档： 
     - 全量修改：`PUT /{索引库名}/_doc/文档id { json文档 }`
     - 局部修改：`POST /{索引库名}/_update/文档id { "doc": {字段}}`

### JavaRestClient

#### 安装与初始化

1. 安装

   官方文档地址：

   https://www.elastic.co/guide/en/elasticsearch/client/index.html

   由于ES目前最新版本是8.8，提供了全新版本的客户端，老版本的客户端已经被标记为过时。该项目采用的是7.12版本，因此只能使用老版本客户端：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250219163230629.png" alt="image-20250219163230629" style="zoom:33%;" />

   然后选择对应的版本，HighLevelRestClient版本：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250219163257100.png" alt="image-20250219163257100" style="zoom:43%;" />

2. 客户端初始化

   在elasticsearch提供的API中，与elasticsearch一切交互都封装在一个名为`RestHighLevelClient`的类中，必须先完成这个对象的初始化，建立与elasticsearch的连接。

   分为三步：

   （1）在要使用ES的微服务，比如`item-service`模块中引入`es`的`RestHighLevelClient`依赖：

   ```XML
   <dependency>
       <groupId>org.elasticsearch.client</groupId>
       <artifactId>elasticsearch-rest-high-level-client</artifactId>
   </dependency>
   ```

   （2）因为SpringBoot默认的ES版本是`7.17.10`，所以需要在父工程的pom文件覆盖默认的ES版本：

   ```XML
     <properties>
         <maven.compiler.source>11</maven.compiler.source>
         <maven.compiler.target>11</maven.compiler.target>
         <elasticsearch.version>7.12.1</elasticsearch.version>
     </properties>
   ```

   （3）初始化RestHighLevelClient：

   初始化的代码如下：

   ```Java
   RestHighLevelClient client = new RestHighLevelClient(RestClient.builder(
           HttpHost.create("http://192.168.150.101:9200") // 虚拟机地址加上ES端口
   ));
   ```

   这里为了单元测试方便，我们创建一个测试类`IndexTest`，然后将初始化的代码编写在`@BeforeEach`方法中：

   ```Java
   package com.hmall.item.es;
   
   import org.apache.http.HttpHost;
   import org.elasticsearch.client.RestClient;
   import org.elasticsearch.client.RestHighLevelClient;
   import org.junit.jupiter.api.AfterEach;
   import org.junit.jupiter.api.BeforeEach;
   import org.junit.jupiter.api.Test;
   
   import java.io.IOException;
   
   public class IndexTest {
   
       private RestHighLevelClient client;
   
       @BeforeEach
       void setUp() {
           this.client = new RestHighLevelClient(RestClient.builder(
                   HttpHost.create("http://192.168.150.101:9200")
           ));
       }
   
       @Test
       void testConnect() {
           System.out.println(client);
       }
   
       @AfterEach
       void tearDown() throws IOException {
           this.client.close();
       }
   }
   ```

#### 索引库操作

1. 商品Mapping映射

   搜索页面的效果如图所示：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250219164554617.png" alt="image-20250219164554617" style="zoom:33%;" /> 

   实现搜索功能需要的字段包括三大部分：

   - 搜索过滤字段：分类、品牌、价格
   - 排序字段：默认（按照更新时间降序排序）、销量、价格
   - 展示字段：商品id（用于点击后跳转）、图片地址、是否是广告推广商品、名称、价格、评价数量、销量

   对应的商品表结构如下，索引库无关字段已经划掉：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250219164618396.png" alt="image-20250219164618396" style="zoom:33%;" /> 

   结合数据库表结构，以上字段对应的mapping映射属性如下：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250219164751428.png" alt="image-20250219164751428" style="zoom:33%;" />

   因此，最终索引库文档结构应该是这样：

   ```JSON
   PUT /items
   {
     "mappings": {
       "properties": {
         "id": {
           "type": "keyword"
         },
         "name":{
           "type": "text",
           "analyzer": "ik_max_word"
         },
         "price":{
           "type": "integer"
         },
         "stock":{
           "type": "integer"
         },
         "image":{
           "type": "keyword",
           "index": false
         },
         "category":{
           "type": "keyword"
         },
         "brand":{
           "type": "keyword"
         },
         "sold":{
           "type": "integer"
         },
         "commentCount":{
           "type": "integer",
           "index": false
         },
         "isAD":{
           "type": "boolean"
         },
         "updateTime":{
           "type": "date"
         }
       }
     }
   }
   ```

2. 创建索引库

   创建索引库的API如下：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250219180512772.png" alt="image-20250219180512772" style="zoom:43%;" /> 

   代码分为三步：

   - 1）创建Request对象。
     - 因为是创建索引库的操作，因此Request是`CreateIndexRequest`。
   - 2）添加请求参数
     - 其实就是Json格式的Mapping映射参数。因为json字符串很长，这里是定义了静态字符串常量`MAPPING_TEMPLATE`，让代码看起来更加优雅。
   - 3）发送请求
     - `client.``indices``()`方法的返回值是`IndicesClient`类型，封装了所有与索引库操作有关的方法。例如创建索引、删除索引、判断索引是否存在等

   在`item-service`中的`IndexTest`测试类中，具体代码如下：

   ```Java
   @Test
   void testCreateIndex() throws IOException {
       // 1.创建Request对象
       CreateIndexRequest request = new CreateIndexRequest("items");
       // 2.准备请求参数
       request.source(MAPPING_TEMPLATE, XContentType.JSON);
       // 3.发送请求
       client.indices().create(request, RequestOptions.DEFAULT);
   }
   
   static final String MAPPING_TEMPLATE = "{\n" +
               "  \"mappings\": {\n" +
               "    \"properties\": {\n" +
               "      \"id\": {\n" +
               "        \"type\": \"keyword\"\n" +
               "      },\n" +
               "      \"name\":{\n" +
               "        \"type\": \"text\",\n" +
               "        \"analyzer\": \"ik_max_word\"\n" +
               "      },\n" +
               "      \"price\":{\n" +
               "        \"type\": \"integer\"\n" +
               "      },\n" +
               "      \"stock\":{\n" +
               "        \"type\": \"integer\"\n" +
               "      },\n" +
               "      \"image\":{\n" +
               "        \"type\": \"keyword\",\n" +
               "        \"index\": false\n" +
               "      },\n" +
               "      \"category\":{\n" +
               "        \"type\": \"keyword\"\n" +
               "      },\n" +
               "      \"brand\":{\n" +
               "        \"type\": \"keyword\"\n" +
               "      },\n" +
               "      \"sold\":{\n" +
               "        \"type\": \"integer\"\n" +
               "      },\n" +
               "      \"commentCount\":{\n" +
               "        \"type\": \"integer\"\n" +
               "      },\n" +
               "      \"isAD\":{\n" +
               "        \"type\": \"boolean\"\n" +
               "      },\n" +
               "      \"updateTime\":{\n" +
               "        \"type\": \"date\"\n" +
               "      }\n" +
               "    }\n" +
               "  }\n" +
               "}";
   ```

3. 删除索引库

   与创建索引库相比：

   - 请求方式从PUT变为DELTE
   - 请求路径不变
   - 无请求参数

   所以代码的差异，注意体现在Request对象上。流程如下：

   - 1）创建Request对象。这次是DeleteIndexRequest对象
   - 2）准备参数。这里是无参，因此省略
   - 3）发送请求。改用delete方法

   在`item-service`中的`IndexTest`测试类中，编写单元测试，实现删除索引：

   ```Java
   @Test
   void testDeleteIndex() throws IOException {
       // 1.创建Request对象
       DeleteIndexRequest request = new DeleteIndexRequest("items");
       // 2.发送请求
       client.indices().delete(request, RequestOptions.DEFAULT);
   }
   ```

4. 判断索引库是否存在

   与删除的Java代码流程是类似的，流程如下：

   - 1）创建Request对象。这次是GetIndexRequest对象
   - 2）准备参数。这里是无参，直接省略
   - 3）发送请求。改用exists方法

   ```Java
   @Test
   void testExistsIndex() throws IOException {
       // 1.创建Request对象
       GetIndexRequest request = new GetIndexRequest("items");
       // 2.发送请求
       boolean exists = client.indices().exists(request, RequestOptions.DEFAULT);
       // 3.输出
       System.err.println(exists ? "索引库已经存在！" : "索引库不存在！");
   }
   ```

5. 总结

   JavaRestClient操作elasticsearch的流程基本类似。核心是`client.indices()`方法来获取索引库的操作对象。

   索引库操作的基本步骤：

   - 初始化`RestHighLevelClient`
   - 创建XxxIndexRequest。XXX是`Create`、`Get`、`Delete`
   - 准备请求参数（ `Create`时需要，其它是无参，可以省略）
   - 发送请求。调用`RestHighLevelClient.indices().xxx()`方法，xxx是`create`、`exists`、`delete`

#### 文档操作

1. 新增文档

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250219191543462.png" alt="image-20250219191543462" style="zoom:50%;" /> 

   可以看到与索引库操作的API非常类似，同样是三步走：

   - 创建Request对象，这里是`IndexRequest`，因为添加文档就是创建倒排索引的过程
   - 准备请求参数，本例中就是Json文档
   - 发送请求

   变化的地方在于，这里直接使用`client.xxx()`的API，不再需要`client.indices()`了。

   对于item微服务，结合实际需求，导入商品数据，除了参考API模板“三步走”以外，还需要做几点准备工作：

   - 商品数据来自于数据库，我们需要先查询出来，得到`Item`对象
   - `Item`对象需要转为`ItemDoc`对象
   - `ItemDTO`需要序列化为`json`格式

   在`item-service`的`DocumentTest`测试类中，编写单元测试：

   ```Java
   @Test
   void testAddDocument() throws IOException {
       // 1.根据id查询商品数据
       Item item = itemService.getById(100002644680L);
       // 2.转换为文档类型
       ItemDoc itemDoc = BeanUtil.copyProperties(item, ItemDoc.class);
       // 3.将ItemDTO转json
       String doc = JSONUtil.toJsonStr(itemDoc);
   
       // 1.准备Request对象
       IndexRequest request = new IndexRequest("items").id(itemDoc.getId());
       // 2.准备Json文档
       request.source(doc, XContentType.JSON);
       // 3.发送请求
       client.index(request, RequestOptions.DEFAULT);
   }
   ```

2. 查询文档

   与之前的流程类似，代码大概分2步：

   - 创建Request对象
   - 准备请求参数，这里是无参，直接省略
   - 发送请求

   不过查询的目的是得到结果，解析为ItemDTO，还要再加一步对结果的解析。示例代码如下：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250219191807350.png" alt="image-20250219191807350" style="zoom:50%;" /> 

   可以看到，响应结果是一个JSON，其中文档放在一个`_source`属性中，因此解析就是拿到`_source`，反序列化为Java对象即可。

   其它代码与之前类似，流程如下：

   - 准备Request对象。这次是查询，所以是`GetRequest`
   - 发送请求，得到结果。因为是查询，这里调用`client.get()`方法
   - 解析结果，就是对JSON做反序列化

   在`item-service`的`DocumentTest`测试类中，编写单元测试：

   ```Java
   @Test
   void testGetDocumentById() throws IOException {
       // 1.准备Request对象
       GetRequest request = new GetRequest("items").id("100002644680");
       // 2.发送请求
       GetResponse response = client.get(request, RequestOptions.DEFAULT);
       // 3.获取响应结果中的source
       String json = response.getSourceAsString();
       
       ItemDoc itemDoc = JSONUtil.toBean(json, ItemDoc.class);
       System.out.println("itemDoc= " + ItemDoc);
   }
   ```

3. 删除文档

   与查询相比，仅仅是请求方式从`DELETE`变成`GET`，可以想象Java代码应该依然是2步走：

   - 1）准备Request对象，因为是删除，这次是`DeleteRequest`对象。要指定索引库名和id
   - 2）准备参数，无参，直接省略
   - 3）发送请求。因为是删除，所以是`client.delete()`方法

   在`item-service`的`DocumentTest`测试类中，编写单元测试：

   ```Java
   @Test
   void testDeleteDocument() throws IOException {
       // 1.准备Request，两个参数，第一个是索引库名，第二个是文档id
       DeleteRequest request = new DeleteRequest("item", "100002644680");
       // 2.发送请求
       client.delete(request, RequestOptions.DEFAULT);
   }
   ```

4. 修改文档

   在RestClient的API中，全量修改与新增的API完全一致，判断依据是ID：

   - 如果新增时，ID已经存在，则修改
   - 如果新增时，ID不存在，则新增

   这里主要关注局部修改的API：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250219192002158.png" alt="image-20250219192002158" style="zoom:50%;" /> 

   与之前类似，也是三步走：

   - 准备`Request`对象。这次是修改，所以是`UpdateRequest`
   - 准备参数。也就是JSON文档，里面包含要修改的字段
   - 更新文档。这里调用`client.update()`方法

   在`item-service`的`DocumentTest`测试类中，编写单元测试：

   ```Java
   @Test
   void testUpdateDocument() throws IOException {
       // 1.准备Request
       UpdateRequest request = new UpdateRequest("items", "100002644680");
       // 2.准备请求参数
       request.doc(
               "price", 58800,
               "commentCount", 1
       );
       // 3.发送请求
       client.update(request, RequestOptions.DEFAULT);
   }
   ```

5. 批量操作

   批处理与前面讲的文档的CRUD步骤基本一致：

   - 创建Request，但这次用的是`BulkRequest`
   - 准备请求参数
   - 发送请求，这次要用到`client.bulk()`方法

   `BulkRequest`本身其实并没有请求参数，其本质就是将多个普通的CRUD请求组合在一起发送。例如：

   - 批量新增文档，就是给每个文档创建一个`IndexRequest`请求，然后封装到`BulkRequest`中，一起发出。
   - 批量删除，就是创建N个`DeleteRequest`请求，然后封装到`BulkRequest`，一起发出

   因此`BulkRequest`中提供了`add`方法，用以添加其它CRUD的请求：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250219192124755.png" alt="image-20250219192124755" style="zoom:33%;" />

   可以看到，能添加的请求有：

   - `IndexRequest`，也就是新增
   - `UpdateRequest`，也就是修改
   - `DeleteRequest`，也就是删除

   因此Bulk中添加了多个`IndexRequest`，就是批量新增功能了。示例：

   ```Java
   @Test
   void testBulk() throws IOException {
       // 1.创建Request
       BulkRequest request = new BulkRequest();
       // 2.准备请求参数
       request.add(new IndexRequest("items").id("1").source("json doc1", XContentType.JSON));
       request.add(new IndexRequest("items").id("2").source("json doc2", XContentType.JSON));
       // 3.发送请求
       client.bulk(request, RequestOptions.DEFAULT);
   }
   ```

   当我们要导入商品数据时，由于商品数量达到数十万，因此不可能一次性全部导入。建议采用循环遍历方式，每次导入1000条左右的数据。

   `item-service`的`DocumentTest`测试类中，编写单元测试：

   ```Java
   @Test
   void testLoadItemDocs() throws IOException {
       // 分页查询商品数据
       int pageNo = 1;
       int size = 1000;
       while (true) {
           Page<Item> page = itemService.lambdaQuery().eq(Item::getStatus, 1).page(new Page<Item>(pageNo, size));
           // 非空校验
           List<Item> items = page.getRecords();
           if (CollUtils.isEmpty(items)) {
               return;
           }
           log.info("加载第{}页数据，共{}条", pageNo, items.size());
           // 1.创建Request
           BulkRequest request = new BulkRequest("items");
           // 2.准备参数，添加多个新增的Request
           for (Item item : items) {
               // 2.1.转换为文档类型ItemDTO
               ItemDoc itemDoc = BeanUtil.copyProperties(item, ItemDoc.class);
               // 2.2.创建新增文档的Request对象
               request.add(new IndexRequest()
                               .id(itemDoc.getId())
                               .source(JSONUtil.toJsonStr(itemDoc), XContentType.JSON));
           }
           // 3.发送请求
           client.bulk(request, RequestOptions.DEFAULT);
   
           // 翻页
           pageNo++;
       }
   }
   ```

6. 总结

   文档操作的基本步骤：

   - 初始化`RestHighLevelClient`
   - 创建XxxRequest。
     - XXX是`Index`、`Get`、`Update`、`Delete`、`Bulk`
   - 准备参数（`Index`、`Update`、`Bulk`时需要）
   - 发送请求。
     - 调用`RestHighLevelClient#.xxx()`方法，xxx是`index`、`get`、`update`、`delete`、`bulk`
   - 解析结果（`Get`时需要）

### DSL查询

#### 快速入门

1. 介绍

   Elasticsearch提供了基于JSON的DSL（[Domain Specific Language](https://www.elastic.co/guide/en/elasticsearch/reference/7.12/query-dsl.html)）语句来定义查询条件，其JavaAPI就是在组织DSL条件。

   Elasticsearch的查询可以分为两大类：

   - 叶子查询（Leaf query clauses）：一般是在特定的字段里查询特定值，属于简单查询，很少单独使用。
   - 复合查询（Compound query clauses）：以逻辑方式组合多个叶子查询或者更改叶子查询的行为方式。

2. 入门案例

   在Kibana的DevTools中学习查询的DSL语法。首先来看查询的语法结构：

   ```JSON
   GET /{索引库名}/_search
   {
     "query": {
       "查询类型": {
         // .. 查询条件
       }
     }
   }
   ```

   说明：

   - `GET /{索引库名}/_search`：其中的`_search`是固定路径，不能修改

   例如，以最简单的无条件查询为例，无条件查询的类型是：match_all，因此其查询语句如下：

   ```JSON
   GET /items/_search
   {
     "query": {
       "match_all": {
         
       }
     }
   }
   ```

   由于match_all无条件，所以条件位置不写即可。

   执行结果如下：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250220131635534.png" alt="image-20250220131635534" style="zoom:50%;" /> 

   最终的响应结果中并不会包含索引库中的所有文档，而是仅有10条。这是因为处于安全考虑，elasticsearch设置了默认的查询页数。

#### 叶子查询

1. 叶子查询类型

   叶子查询的类型也可以做进一步细分：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250220131752419.png" alt="image-20250220131752419" style="zoom:43%;" /> 

   列举一些常见的，例如：

   **全文检索查询（Full Text Queries）**：利用分词器对用户输入搜索条件先分词，得到词条，然后再利用倒排索引搜索词条。例如：

   - `match`：
   - `multi_match`

   **精确查询（Term-level queries）**：不对用户输入搜索条件分词，根据字段内容精确值匹配。但只能查找keyword、数值、日期、boolean类型的字段。例如：

   - `ids`
   - `term`
   - `range`

   **地理坐标查询：**用于搜索地理位置，搜索方式很多，例如：

   - `geo_bounding_box`：按矩形搜索
   - `geo_distance`：按点和半径搜索

2. 全文检索查询

   （1）以全文检索中的`match`为例，语法如下：

   ```JSON
   GET /{索引库名}/_search
   {
     "query": {
       "match": {
         "字段名": "搜索条件"
       }
     }
   }
   ```

   示例：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250220131910454.png" alt="image-20250220131910454" style="zoom:50%;" /> 

   （2）与`match`类似的还有`multi_match`，区别在于可以同时对多个字段搜索，而且多个字段都要满足，语法示例：

   ```JSON
   GET /{索引库名}/_search
   {
     "query": {
       "multi_match": {
         "query": "搜索条件",
         "fields": ["字段1", "字段2"]          //这里query和fields这两个字段名是写死的
       }
     }
   }
   ```

   示例：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250220132035053.png" alt="image-20250220132035053" style="zoom:50%;" /> 

3. 精确查询

   精确查询，英文是`Term-level query`，顾名思义，词条级别的查询。也就是说不会对用户输入的搜索条件再分词，而是作为一个词条，与搜索的字段内容精确值匹配。因此推荐查找`keyword`、数值、日期、`boolean`类型的字段。例如：

   - id
   - price
   - 城市
   - 地名
   - 人名

   等等，作为一个整体才有含义的字段。

   （1）以`term`查询为例，其语法如下：

   ```JSON
   GET /{索引库名}/_search
   {
     "query": {
       "term": {
         "字段名": {
           "value": "搜索条件"
         }
       }
     }
   }
   ```

   示例：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250220132225538.png" alt="image-20250220132225538" style="zoom:50%;" /> 

   在`term`查询中，当输入的搜索条件不是词语，而是短语时，由于不做分词，反而搜索不到。因此这里的`value`需要是词语。

   （2）`range`查询，语法如下：

   ```JSON
   GET /{索引库名}/_search
   {
     "query": {
       "range": {
         "字段名": {
           "gte": {最小值},
           "lte": {最大值}
         }
       }
     }
   }
   ```

   `range`是范围查询，对于范围筛选的关键字有：

   - `gte`：大于等于
   - `gt`：大于
   - `lte`：小于等于
   - `lt`：小于

   示例：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250220132418243.png" alt="image-20250220132418243" style="zoom:50%;" /> 

#### 复合查询

1. 介绍

   复合查询大致可以分为两类：

   - 第一类：基于逻辑运算组合叶子查询，实现组合条件，例如
     - bool
   - 第二类：基于某种算法修改查询时的文档相关性算分，从而改变文档排名。例如：
     - function_score
     - dis_max

2. bool查询

   bool查询，即布尔查询。就是利用逻辑运算来组合一个或多个查询子句的组合。bool查询支持的逻辑运算有：

   - must：必须匹配每个子查询，类似“与”
   - should：选择性匹配子查询，类似“或”
   - must_not：必须不匹配，**不参与算分**，类似“非”
   - filter：必须匹配，**不参与算分**

   bool查询的语法如下：

   ```JSON
   GET /items/_search
   {
     "query": {
       "bool": {
         "must": [
           {"match": {"name": "手机"}}
         ],
         "should": [
           {"term": {"brand": { "value": "vivo" }}},
           {"term": {"brand": { "value": "小米" }}}
         ],
         "must_not": [
           {"range": {"price": {"gte": 2500}}}
         ],
         "filter": [
           {"range": {"price": {"lte": 1000}}}
         ]
       }
     }
   }
   ```

   出于性能考虑，与搜索关键字无关的查询尽量采用must_not或filter逻辑运算，避免参与相关性算分。

   例如黑马商城的搜索页面：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250220132944770.png" alt="image-20250220132944770" style="zoom:50%;" /> 

   其中输入框的搜索条件肯定要参与相关性算分，可以采用match。但是价格范围过滤、品牌过滤、分类过滤等尽量采用filter，不要参与相关性算分。

   比如要搜索`手机`，但品牌必须是`华为`，价格必须是`900~1599`，那么可以这样写：

   ```JSON
   GET /items/_search
   {
     "query": {
       "bool": {
         "must": [
           {"match": {"name": "手机"}}
         ],
         "filter": [
           {"term": {"brand": { "value": "华为" }}},
           {"range": {"price": {"gte": 90000, "lt": 159900}}}
         ]
       }
     }
   }
   ```

3. 算分函数查询

   当利用match查询时，文档结果会根据与搜索词条的**关联度打分**（**_score**），返回结果时按照分值降序排列。

   要想控制相关性算分，就需要利用elasticsearch中的function score 查询了。

   **基本语法**：

   function score 查询中包含四部分内容：

   - **原始查询**条件：query部分，基于这个条件搜索文档，并且基于BM25算法给文档打分，**原始算分**（query score)
   - **过滤条件**：filter部分，符合该条件的文档才会重新算分
   - **算分函数**：符合filter条件的文档要根据这个函数做运算，得到的**函数算分**（function score），有四种函数 
     - weight：函数结果是常量
     - field_value_factor：以文档中的某个字段值作为函数结果
     - random_score：以随机数作为函数结果
     - script_score：自定义算分函数算法
   - **运算模式**：算分函数的结果、原始查询的相关性算分，两者之间的运算方式，包括： 
     - multiply：相乘
     - replace：用function score替换query score
     - 其它，例如：sum、avg、max、min

   function score的运行流程如下：

   - 1）根据**原始条件**查询搜索文档，并且计算相关性算分，称为**原始算分**（query score）
   - 2）根据**过滤条件**，过滤文档
   - 3）符合**过滤条件**的文档，基于**算分函数**运算，得到**函数算分**（function score）
   - 4）将**原始算分**（query score）和**函数算分**（function score）基于**运算模式**做运算，得到最终结果，作为相关性算分。

   因此，其中的关键点是：

   - 过滤条件：决定哪些文档的算分被修改
   - 算分函数：决定函数算分的算法
   - 运算模式：决定最终算分结果

   示例：给IPhone这个品牌的手机算分提高十倍，分析如下：

   - 过滤条件：品牌必须为IPhone
   - 算分函数：常量weight，值为10
   - 算分模式：相乘multiply

   对应代码如下：

   ```JSON
   GET /hotel/_search
   {
     "query": {
       "function_score": {
         "query": {  .... }, // 原始查询，可以是任意条件
         "functions": [ // 算分函数
           {
             "filter": { // 满足的条件，品牌必须是Iphone
               "term": {
                 "brand": "Iphone"
               }
             },
             "weight": 10 // 算分权重为2
           }
         ],
         "boost_mode": "multipy" // 加权模式，求乘积
       }
     }
   }
   ```

#### 排序&分页&高亮

1. 排序

   elasticsearch默认是根据相关度算分（`_score`）来排序，但是也支持自定义方式对搜索结果排序。不过分词字段无法排序，能参与排序字段类型有：`keyword`类型、数值类型、地理坐标类型、日期类型等。

   语法说明：

   ```JSON
   GET /indexName/_search
   {
     "query": {
       "match_all": {}
     },
     "sort": [
       {
         "排序字段": {                     //如果有多个排序字段，则按字段从前到后的顺序依次排序
           "order": "排序方式asc和desc" 
         }
       }
     ]
   }
   
   //也可以简化
   GET /indexName/_search
   {
     "query": {
       "match_all": {}
     },
     "sort": [
       {
         "排序字段": "排序方式asc和desc"
       }
     ]
   }
   ```

   示例，按照商品价格排序：

   ```JSON
   GET /items/_search
   {
     "query": {
       "match_all": {}
     },
     "sort": [
       {
         "price": {
           "order": "desc"
         }
       }
     ]
   }
   ```

2. 分页

   elasticsearch 默认情况下只返回top10的数据。而如果要查询更多数据就需要修改分页参数了。

   （1）基础分页

   elasticsearch中通过修改`from`、`size`参数来控制要返回的分页结果：

   - `from`：从第几个文档开始
   - `size`：总共查询几个文档

   类似于mysql中的`limit ?, ?`

   语法如下：

   ```JSON
   GET /items/_search
   {
     "query": {
       "match_all": {}
     },
     "from": 0, // 分页开始的位置，默认为0
     "size": 10,  // 每页文档数量，默认10
     "sort": [
       {
         "price": {
           "order": "desc"
         }
       }
     ]
   }
   ```

   （2）深度分页

   elasticsearch的数据一般会采用分片存储，也就是把一个索引中的数据分成N份，存储到不同节点上。这种存储方式比较有利于数据扩展，但给分页带来了一些麻烦。

   比如一个索引库中有100000条数据，分别存储到4个分片，每个分片25000条数据。现在每页查询10条，查询第99页。那么分页查询的条件如下：

   ```JSON
   GET /items/_search
   {
     "from": 990, // 从第990条开始查询
     "size": 10, // 每页查询10条
     "sort": [
       {
         "price": "asc"
       }
     ]
   }
   ```

   从语句来分析，要查询第990~1000名的数据。

   从实现思路来分析，肯定是将所有数据排序，找出前1000名，截取其中的990~1000的部分。但问题来了，如何才能找到所有数据中的前1000名呢？

   要知道每一片的数据都不一样，第1片上的第900~1000，在另1个节点上并不一定依然是900~1000名。所以只能在每一个分片上都找出排名前1000的数据，然后汇总到一起，重新排序，才能找出整个索引库中真正的前1000名，此时截取990~1000的数据即可。

   如图：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250220135652168.png" alt="image-20250220135652168" style="zoom:33%;" /> 

   试想一下，假如现在要查询的是第999页数据呢，是不是要找第9990~10000的数据，那岂不是需要把每个分片中的前10000名数据都查询出来，汇总在一起，在内存中排序？如果查询的分页深度更深呢，需要一次检索的数据岂不是更多？

   由此可知，当查询分页深度较大时，汇总数据过多，对内存和CPU会产生非常大的压力。

   因此elasticsearch会禁止`from+ size `超过10000的请求。

   

   针对深度分页，elasticsearch提供了两种解决方案：

   - `search after`：分页时需要排序，原理是从上一次的排序值开始，查询下一页数据。官方推荐使用的方式。
   - `scroll`：原理将排序后的文档id形成快照，保存下来，基于快照做分页。官方已经不推荐使用。

3. 高亮

   实现高亮的思路就是：

   - 用户输入搜索关键字搜索数据
   - 服务端根据搜索关键字到elasticsearch搜索，并给搜索结果中的关键字词条添加`html`标签
   - 前端提前给约定好的`html`标签添加`CSS`样式

   事实上elasticsearch已经提供了给搜索关键字加标签的语法，无需自己编码。

   基本语法如下：

   ```JSON
   GET /{索引库名}/_search
   {
     "query": {
       "match": {
         "搜索字段": "搜索关键字"
       }
     },
     "highlight": {
       "fields": {
         "高亮字段名称": {
           "pre_tags": "<em>",
           "post_tags": "</em>"
         }
       }
     }
   }
   ```

   **注意**：

   - 搜索必须有查询条件，而且是全文检索类型的查询条件，例如`match`
   - 参与高亮的字段必须是`text`类型的字段
   - 默认情况下参与高亮的字段要与搜索字段一致，除非添加：`required_field_match=false`

   示例：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250220135914506.png" alt="image-20250220135914506" style="zoom:50%;" /> 

#### 总结

查询的DSL是一个大的JSON对象，包含下列属性：

- `query`：查询条件
- `from`和`size`：分页条件
- `sort`：排序条件
- `highlight`：高亮条件

### JavaRestClient查询

#### 快速入门

1. 基本步骤

   文档的查询依然使用昨天学习的 `RestHighLevelClient`对象，查询的基本步骤如下：

   - 1）创建`request`对象，这次是搜索，所以是`SearchRequest`
   - 2）准备请求参数，也就是查询DSL对应的JSON参数
   - 3）发起请求
   - 4）解析响应，响应结果相对复杂，需要逐层解析

2. 发送请求

   以`match_all`查询为例，其DSL和JavaAPI的对比如图：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250220160501603.png" alt="image-20250220160501603" style="zoom:50%;" /> 

   代码解读：

   -  第一步，创建`SearchRequest`对象，指定索引库名 
   -  第二步，利用`request.source()`构建DSL，DSL中可以包含查询、分页、排序、高亮等 
     - `query()`：代表查询条件，利用`QueryBuilders.matchAllQuery()`构建一个`match_all`查询的DSL
   -  第三步，利用`client.search()`发送请求，得到响应 

   

   这里关键的API有两个，一个是`request.source()`，它构建的就是DSL中的完整JSON参数。其中包含了`query`、`sort`、`from`、`size`、`highlight`等所有功能：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250220160540277.png" alt="image-20250220160540277" style="zoom:33%;" />

   另一个是`QueryBuilders`，其中包含了我们学习过的各种**叶子查询**、**复合查询**等：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250220160559752.png" alt="image-20250220160559752" style="zoom:33%;" />

3. 解析响应结果

   在发送请求以后，得到了响应结果`SearchResponse`，这个类的结构与在kibana中看到的响应结果JSON结构完全一致：

   ```JSON
   {
       "took" : 0,
       "timed_out" : false,
       "hits" : {
           "total" : {
               "value" : 2,
               "relation" : "eq"
           },
           "max_score" : 1.0,
           "hits" : [
               {
                   "_index" : "heima",
                   "_type" : "_doc",
                   "_id" : "1",
                   "_score" : 1.0,
                   "_source" : {
                   "info" : "Java讲师",
                   "name" : "赵云"
                   }
               }
           ]
       }
   }
   ```

   因此，解析`SearchResponse`的代码就是在解析这个JSON结果，对比如下：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250220160708232.png" alt="image-20250220160708232" style="zoom:33%;" />

   **代码解读**：

   elasticsearch返回的结果是一个JSON字符串，结构包含：

   - `hits`：命中的结果 
     - `total`：总条数，其中的value是具体的总条数值
     - `max_score`：所有结果中得分最高的文档的相关性算分
     - `hits`：搜索结果的文档数组，其中的每个文档都是一个json对象 
       - `_source`：文档中的原始数据，也是json对象

   因此，解析响应结果，就是逐层解析JSON字符串，流程如下：

   - `SearchHits`：通过`response.getHits()`获取，就是JSON中的最外层的`hits`，代表命中的结果 
     - `SearchHits.getTotalHits().value`：获取总条数信息
     - `SearchHits.getHits()`：获取`SearchHit`数组，也就是文档数组 
       - `SearchHit.getSourceAsString()`：获取文档结果中的`_source`，也就是原始的`json`文档数据

4. 总结

   文档搜索的基本步骤是：

   1. 创建`SearchRequest`对象
   2. 准备`request.source()`，也就是DSL。
      1. `QueryBuilders`来构建查询条件
      2. 传入`request.source()` 的` query() `方法
   3. 发送请求，得到结果
   4. 解析结果（参考JSON结果，从外到内，逐层解析）

   完整代码如下：

   ```Java
   @Test
   void testMatchAll() throws IOException {
       // 1.创建Request
       SearchRequest request = new SearchRequest("items");
       // 2.组织请求参数
       request.source().query(QueryBuilders.matchAllQuery());
       // 3.发送请求
       SearchResponse response = client.search(request, RequestOptions.DEFAULT);
       // 4.解析响应
       handleResponse(response);
   }
   
   private void handleResponse(SearchResponse response) {
       SearchHits searchHits = response.getHits();
       // 1.获取总条数
       long total = searchHits.getTotalHits().value;
       System.out.println("共搜索到" + total + "条数据");
       // 2.遍历结果数组
       SearchHit[] hits = searchHits.getHits();
       for (SearchHit hit : hits) {
           // 3.得到_source，也就是原始json文档
           String source = hit.getSourceAsString();
           // 4.反序列化并打印
           ItemDoc item = JSONUtil.toBean(source, ItemDoc.class);
           System.out.println(item);
       }
   }
   ```

#### 叶子查询&复合查询

1. 叶子查询

   所有的查询条件都是由QueryBuilders来构建的，叶子查询也不例外。因此整套代码中变化的部分仅仅是query条件构造的方式，其它不动。

   （1）`match`查询：

   ```Java
   @Test
   void testMatch() throws IOException {
       // 1.创建Request
       SearchRequest request = new SearchRequest("items");
       // 2.组织请求参数
       request.source().query(QueryBuilders.matchQuery("name", "脱脂牛奶"));
       // 3.发送请求
       SearchResponse response = client.search(request, RequestOptions.DEFAULT);
       // 4.解析响应
       handleResponse(response);
   }
   ```

   （2）`multi_match`查询：

   ```Java
   @Test
   void testMultiMatch() throws IOException {
       // 1.创建Request
       SearchRequest request = new SearchRequest("items");
       // 2.组织请求参数
       request.source().query(QueryBuilders.multiMatchQuery("脱脂牛奶", "name", "category"));
       // 3.发送请求
       SearchResponse response = client.search(request, RequestOptions.DEFAULT);
       // 4.解析响应
       handleResponse(response);
   }
   ```

   （3）`range`查询：

   ```Java
   @Test
   void testRange() throws IOException {
       // 1.创建Request
       SearchRequest request = new SearchRequest("items");
       // 2.组织请求参数
       request.source().query(QueryBuilders.rangeQuery("price").gte(10000).lte(30000));
       // 3.发送请求
       SearchResponse response = client.search(request, RequestOptions.DEFAULT);
       // 4.解析响应
       handleResponse(response);
   }
   ```

   （4）`term`查询：

   ```Java
   @Test
   void testTerm() throws IOException {
       // 1.创建Request
       SearchRequest request = new SearchRequest("items");
       // 2.组织请求参数
       request.source().query(QueryBuilders.termQuery("brand", "华为"));
       // 3.发送请求
       SearchResponse response = client.search(request, RequestOptions.DEFAULT);
       // 4.解析响应
       handleResponse(response);
   }
   ```

2. 复合查询

   复合查询也是由`QueryBuilders`来构建，以`bool`查询为例，DSL和JavaAPI的对比如图：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250220160935482.png" alt="image-20250220160935482" style="zoom:50%;" />

   完整代码如下：

   ```Java
   @Test
   void testBool() throws IOException {
       // 1.创建Request
       SearchRequest request = new SearchRequest("items");
       // 2.组织请求参数
       // 2.1.准备bool查询
       BoolQueryBuilder bool = QueryBuilders.boolQuery();
       // 2.2.关键字搜索
       bool.must(QueryBuilders.matchQuery("name", "脱脂牛奶"));
       // 2.3.品牌过滤
       bool.filter(QueryBuilders.termQuery("brand", "德亚"));
       // 2.4.价格过滤
       bool.filter(QueryBuilders.rangeQuery("price").lte(30000));
       request.source().query(bool);
       // 3.发送请求
       SearchResponse response = client.search(request, RequestOptions.DEFAULT);
       // 4.解析响应
       handleResponse(response);
   }
   ```

#### 排序和分页

`requeset.source()`就是整个请求JSON参数，所以排序、分页都是基于这个来设置，其DSL和JavaAPI的对比如下：

<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250220161014429.png" alt="image-20250220161014429" style="zoom:50%;" />

完整示例代码：

```Java
@Test
void testPageAndSort() throws IOException {
    int pageNo = 1, pageSize = 5;

    // 1.创建Request
    SearchRequest request = new SearchRequest("items");
    // 2.组织请求参数
    // 2.1.搜索条件参数
    request.source().query(QueryBuilders.matchQuery("name", "脱脂牛奶"));
    // 2.2.排序参数
    request.source().sort("price", SortOrder.ASC);
    // 2.3.分页参数
    request.source().from((pageNo - 1) * pageSize).size(pageSize);
    // 3.发送请求
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);
    // 4.解析响应
    handleResponse(response);
}
```

#### 高亮

高亮查询与前面的查询有两点不同：

- 条件同样是在`request.source()`中指定，只不过高亮条件要基于`HighlightBuilder`来构造
- 高亮响应结果与搜索的文档结果不在一起，需要单独解析

首先来看高亮条件构造，其DSL和JavaAPI的对比如图：

<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250220161201289.png" alt="image-20250220161201289" style="zoom:33%;" />

示例代码如下：

```Java
@Test
void testHighlight() throws IOException {
    // 1.创建Request
    SearchRequest request = new SearchRequest("items");
    // 2.组织请求参数
    // 2.1.query条件
    request.source().query(QueryBuilders.matchQuery("name", "脱脂牛奶"));
    // 2.2.高亮条件
    request.source().highlighter(
            SearchSourceBuilder.highlight()
                    .field("name")
                    .preTags("<em>")
                    .postTags("</em>")
    );
    // 3.发送请求
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);
    // 4.解析响应
    handleResponse(response);
}
```

再来看结果解析，文档解析的部分不变，主要是高亮内容需要单独解析出来，其DSL和JavaAPI的对比如图：

<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250220161219841.png" alt="image-20250220161219841" style="zoom:33%;" />

代码解读：

- 第`3、4`步：从结果中获取`_source`。`hit.getSourceAsString()`，这部分是非高亮结果，json字符串。还需要反序列为`ItemDoc`对象
- 第`5`步：获取高亮结果。`hit.getHighlightFields()`，返回值是一个`Map`，key是高亮字段名称，值是`HighlightField`对象，代表高亮值
- 第`5.1`步：从`Map`中根据高亮字段名称，获取高亮字段值对象`HighlightField`
- 第`5.2`步：从`HighlightField`中获取`Fragments`，并且转为字符串。这部分就是真正的高亮字符串了
- 最后：用高亮的结果替换`ItemDoc`中的非高亮结果

完整代码如下：

```Java
private void handleResponse(SearchResponse response) {
    SearchHits searchHits = response.getHits();
    // 1.获取总条数
    long total = searchHits.getTotalHits().value;
    System.out.println("共搜索到" + total + "条数据");
    // 2.遍历结果数组
    SearchHit[] hits = searchHits.getHits();
    for (SearchHit hit : hits) {
        // 3.得到_source，也就是原始json文档
        String source = hit.getSourceAsString();
        // 4.反序列化
        ItemDoc item = JSONUtil.toBean(source, ItemDoc.class);
        // 5.获取高亮结果
        Map<String, HighlightField> hfs = hit.getHighlightFields();
        if (CollUtils.isNotEmpty(hfs)) {
            // 5.1.有高亮结果，获取name的高亮结果
            HighlightField hf = hfs.get("name");
            if (hf != null) {
                // 5.2.获取第一个高亮结果片段，就是商品名称的高亮值
                String hfName = hf.getFragments()[0].string();
                item.setName(hfName);
            }
        }
        System.out.println(item);
    }
}
```

### 数据聚合

#### 分类

1. 聚合

   聚合（`aggregations`）可以让我们极其方便的实现对数据的统计、分析、运算。例如：

   - 什么品牌的手机最受欢迎？
   - 这些手机的平均价格、最高价格、最低价格？
   - 这些手机每月的销售情况如何？

   实现这些统计功能的比数据库的sql要方便的多，而且查询速度非常快，可以实现近实时搜索效果。

2. 分类

   聚合常见的有三类：

   （1）桶（`Bucket`）聚合：用来对文档做分组 

   - `TermAggregation`：按照文档字段值分组，例如按照品牌值分组、按照国家分组
   - `Date Histogram`：按照日期阶梯分组，例如一周为一组，或者一月为一组

   （2）度量（`Metric`）聚合：用以计算一些值，比如：最大值、最小值、平均值等 

   - `Avg`：求平均值
   - `Max`：求最大值
   - `Min`：求最小值
   - `Stats`：同时求`max`、`min`、`avg`、`sum`等

   （3）管道（`pipeline`）聚合：其它聚合的结果为基础做进一步运算 

   **注意：**参加聚合的字段必须是keyword、日期、数值、布尔类型

#### DSL实现聚合

1. 桶聚合

   例如要统计所有商品中共有哪些商品分类，其实就是以分类（category）字段对数据分组。category值一样的放在同一组，属于`Bucket`聚合中的`Term`聚合。

   基本语法如下：

   ```JSON
   GET /items/_search
   {
     "size": 0, 
     "aggs": {
       "category_agg": {
         "terms": {
           "field": "category",
           "size": 20
         }
       }
     }
   }
   ```

   语法说明：

   - `size`：设置`size`为0，就是每页查0条，则结果中就不包含文档，只包含聚合
   - `aggs`：定义聚合
     - `category_agg`：聚合名称，自定义，但不能重复
       - `terms`：聚合的类型，按分类聚合，所以用`term`
         - `field`：参与聚合的字段名称
         - `size`：希望返回的聚合结果的最大数量

   查询结果：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250220174749310.png" alt="image-20250220174749310" style="zoom:33%;" /> 

2. 带条件聚合

   默认情况下，Bucket聚合是对索引库的所有文档做聚合，例如统计商品中所有的品牌，结果如下：

   <img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250220174835940.png" alt="image-20250220174835940" style="zoom:33%;" />

   可以看到统计出的品牌非常多。

   但真实场景下，用户会输入搜索条件，因此聚合必须是对搜索结果聚合。那么聚合必须添加限定条件。

   例如，想知道价格高于3000元的手机品牌有哪些，需要从需求中分析出搜索查询的条件和聚合的目标：

   - 搜索查询条件：
     - 价格高于3000
     - 必须是手机
   - 聚合目标：统计的是品牌，肯定是对brand字段做term聚合

   语法如下：

   ```JSON
   GET /items/_search
   {
     "query": {
       "bool": {
         "filter": [
           {
             "term": {
               "category": "手机"
             }
           },
           {
             "range": {
               "price": {
                 "gte": 300000
               }
             }
           }
         ]
       }
     }, 
     "size": 0, 
     "aggs": {
       "brand_agg": {
         "terms": {
           "field": "brand",
           "size": 20
         }
       }
     }
   }
   ```

   聚合结果如下：

   ```JSON
   {
     "took" : 2,
     "timed_out" : false,
     "hits" : {
       "total" : {
         "value" : 13,
         "relation" : "eq"
       },
       "max_score" : null,
       "hits" : [ ]
     },
     "aggregations" : {
       "brand_agg" : {
         "doc_count_error_upper_bound" : 0,
         "sum_other_doc_count" : 0,
         "buckets" : [
           {
             "key" : "华为",
             "doc_count" : 7
           },
           {
             "key" : "Apple",
             "doc_count" : 5
           },
           {
             "key" : "小米",
             "doc_count" : 1
           }
         ]
       }
     }
   }
   ```

   可以看到，结果中只剩下3个品牌了。

3. 度量聚合

   假设需要对桶内的商品做运算，获取每个品牌价格的最小值、最大值、平均值。

   这就要用到`Metric`聚合了，例如`stat`聚合，就可以同时获取`min`、`max`、`avg`等结果。

   语法如下：

   ```JSON
   GET /items/_search
   {
     "query": {
       "bool": {
         "filter": [
           {
             "term": {
               "category": "手机"
             }
           },
           {
             "range": {
               "price": {
                 "gte": 300000
               }
             }
           }
         ]
       }
     }, 
     "size": 0, 
     "aggs": {
       "brand_agg": {
         "terms": {
           "field": "brand",
           "size": 20
         },
         "aggs": {
           "stats_meric": {
             "stats": {
               "field": "price"
             }
           }
         }
       }
     }
   }
   ```

   可以看到在`brand_agg`聚合的内部，新加了一个`aggs`参数。这个聚合就是`brand_agg`的子聚合，会对`brand_agg`形成的每个桶中的文档分别统计。

   - `stats_meric`：聚合名称
   - `stats`：聚合类型，stats是`metric`聚合的一
   - `field`：聚合字段，这里选择`price`，统计价格

   由于stats是对brand_agg形成的每个品牌桶内文档分别做统计，因此每个品牌都会统计出自己的价格最小、最大、平均值。

   结果如下：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250220175147026.png" alt="image-20250220175147026" style="zoom:33%;" /> 

   还可以让聚合按照每个品牌的价格平均值排序：<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250220175219258.png" alt="image-20250220175219258" style="zoom:33%;" /> 

4. 总结

   aggs代表聚合，可以与query同级，此时query的作用是：限定聚合的的文档范围。

   聚合必须的三要素：

   - 聚合名称
   - 聚合类型
   - 聚合字段

   聚合可配置属性有：

   - size：指定聚合结果数量
   - order：指定聚合结果排序方式
   - field：指定聚合字段

#### Java客户端实现聚合

在DSL中，`aggs`聚合条件与`query`条件是同一级别，都属于查询JSON参数。因此依然是利用`request.source()`方法来设置。

不过聚合条件的要利用`AggregationBuilders`这个工具类来构造。DSL与JavaAPI的语法对比如下：

<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250220182033456.png" alt="image-20250220182033456" style="zoom:50%;" />

聚合结果与搜索文档同一级别，因此需要单独获取和解析。具体解析语法如下：

<img src="D:\Desktop\Java学习个人笔记整理\11 微服务.assets\image-20250220182106847.png" alt="image-20250220182106847" style="zoom:50%;" />

这里聚合结果的类型取决于聚合类型，比如是stats，那么聚合结果也有stats类型。

完整代码如下：

```Java
@Test
void testAgg() throws IOException {
    // 1.创建Request
    SearchRequest request = new SearchRequest("items");
    // 2.准备请求参数
    BoolQueryBuilder bool = QueryBuilders.boolQuery()
            .filter(QueryBuilders.termQuery("category", "手机"))
            .filter(QueryBuilders.rangeQuery("price").gte(300000));
    request.source().query(bool).size(0);
    // 3.聚合参数
    request.source().aggregation(
            AggregationBuilders.terms("brand_agg").field("brand").size(5)
    );
    // 4.发送请求
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);
    // 5.解析聚合结果
    Aggregations aggregations = response.getAggregations();
    // 5.1.获取品牌聚合
    Terms brandTerms = aggregations.get("brand_agg");
    // 5.2.获取聚合中的桶
    List<? extends Terms.Bucket> buckets = brandTerms.getBuckets();
    // 5.3.遍历桶内数据
    for (Terms.Bucket bucket : buckets) {
        // 5.4.获取桶内key
        String brand = bucket.getKeyAsString();
        System.out.print("brand = " + brand);
        long count = bucket.getDocCount();
        System.out.println("; count = " + count);
    }
}
```

