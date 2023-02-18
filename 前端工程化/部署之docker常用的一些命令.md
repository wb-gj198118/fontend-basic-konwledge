# 🧑‍🏫Docker 简单命令.

> Docker常用的命令.

**对于Docker的工作原理、网络以及分层等稍后梳理之后会进行详细分析.**

## 🏷️一、 Docker 基本命令.

下面是`Docker`的基本命令，除了`Docker`的基本命令还有`Docker`隔离相关的命令.

#### 1.1 基本命令.

| 命令 | 说明 |
| --- | --- |
| docker info | 检查当前容器的安装情况（包括镜像数、容器书、多少个物理机节点） |
| docker version | 查看当前安装的Docker的版本. |

#### 1.2 Docker容器生命周期相关命令.

| 命令 | 说明 |
| --- | --- |
| docker run -d -p x:x --name xxx id. | 用某一个镜像在后台运行一个容器 |
| docker create --name xxx nginx:latest | 创建一个新的容器，但是不启动 |
| docker start\\stop\\restart | 启动\\停止\\停止一个容器. |
| docker kill 容器id. | 终止一个运行中的容器，kill 不管是否同意，直接强制终止 |
| docker rm -vf 容器id | 删除一个或者多个容器 |
| docker exec -it id bash | 进入容器内部. |

#### 1.3 Docker容器相关命令.

| 命令 | 说明 |
| --- | --- |
| docker ps -a | grep xxxx | 显示某一个组件XXX的容器列表. |
| docker inspect id. | 获取容器或者镜像的元数据. |
| docker top id | 查看容器中运行的进程信息 |
| docker stats id | 实时显示容器资源的使用统计 |
| docker events | 从服务器获取实时事件 |
| docker logs id | 查看容器内的标准日志输出 |
| docker port id | 列出指定容器的端口映射 |
| docker cp ./t.txt id:/root/ | 将宿主机当前目录下的t.txt复制到id容器中的root目录下 |
| docker diff id | 列出该容器自从创建来，容器内部文件的变化. |
| docker commit -m "comment" -a "authon" 容器id repository:tag | 将容器转换为一个镜像 |
| docker update --memory=16G | 修改容器运行中的配置，即使生效无需配置. |

#### 1.4 Docker 本地镜像管理命令.

| 命令 | 说明 |
| --- | --- |
| docker images | 列出本地宿主机上的所有镜像 |
| docker history id | 查看指定镜像的分层结构以及创建历史 |
| docker image inspect id | 查看镜像的元数据信息 |
| docker rmi id | 根据镜像id删除镜像 |
| docker tag image-name:tag | 给指定镜像增加tag. |
| docekr build -t tag . | 根据当前目目录下的Dockerfile来构建一个标签为tag的镜像. |
| docker export -o xxx.tar id | 将镜像打包成文件 |
| docker import xxx.tar name | 从归档文件中创建镜像. |
| docker save -o xxx.tat id | 将指定镜像保存为归档文件 |
| docker load --input xxx.tar | 用于将docker svae 生成的归档文件还原成镜像. |

#### 1.5 Docker 镜像仓库命令.

| 命令 | 说明 |
| --- | --- |
| docker loging -u xxx -p xxx | 登录一个docker镜像仓库，如果未指定镜像仓库地址，则默认为Docker Hub镜像仓库 |
| docker logout | 退出登录的镜像仓库 |
| docker pull nginx | 从默认的Docker hub 上拉取nginx的镜像. |
| docker push image\_name | 将本地镜像上传到镜像仓库，前提是Docker必须进行Login. |
| docker search xxx | 从默认的Docker Hub中搜索指定的镜像. |

#### 1.6 下面这张图显示了完整的Docker命令关系.

![image-20210914200645229](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9783a04f352a4d8d8e0b869abe0f91ce~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

## 🏷️ 二、Docker高级命令.

#### 2.1 与容器运行模式相关的命令.

> 就是 docker run 命令附带的参数详解.

| 参数 | 说明 |
| --- | --- |
| \-a,--attach=\[\] | 是否绑定到标准输入、输出、和错误. |
| \-d,--detach=true|false | 是否在后台运行，默认为false. |
| \--detach-keys="" | 从attach模式退出的快捷键，默认是Ctrl+P |
| \--entrypoint="" | 镜像存在入口命令时覆盖新的命令. |
| \--expost=\[\] | 暴露出来的镜像端口 |
| \--group-add=\[\] | 运行容器的用户组 |
| \-i | 保持标准输入打开 |
| \--ipc="" | 容器IP命名空间. |
| \--isolation="default" | 容器使用的隔离机制. |
| \--log-driver="" | 指定容器的日志驱动类型. |
| \--log-opy=\[\] | 传递给日志驱动的选项. |
| \--net="bridge" | 指定容器的网络模式. |
| \--net-alias=\[\] | 容器在网络中的别名 |
| \--volume=\[=\[HOST-DIR\]:\]CONTAINER-DIR:\[:OPTIONS\]\] | 挂在宿主机上的数据卷到容器内 |
| \--volume-driver="" | 挂在数据卷的驱动类型. |
| \--volumes-from=\[\] | 从其他容器挂在数据卷 |

#### 2.2 与容器环境配置相关的命令.

| 参数 | 说明 |
| --- | --- |
| \--add-host=\[\] | 在容器内添加一个宿主机名到IP地址的映射关系，修改HOST文件 |
| \--device=\[\] | 映射物理机上的设备到容器 |
| \--dns-search=\[\] | 指定容器DNS搜索域. |
| \--dns-opt=\[\] | 自定义的DNS选项 |
| \--dns=\[\] | 自定义的DNS服务器 |
| \-e,-env=\[\] | 指定容器内的环境变量. |
| \-env-file=\[\] | 从文件中读取环境变量到容器内 |
| \-h,--hostname="" | 指定容器中的主机名. |
| \--ip="" | 指定容器内ip4的地址 |
| \--ip6="" | 指定容器内ip6的地址 |
| \--link=\[id\] | 连接到其他容器，不需要其他容器暴露端口. |
| \--name=="" | 指定容器的别名 |

#### 2.3 Docker限制CPU使用.

| 参数 | 说明 |
| --- | --- |
| \--cpu-shares=0 | 允许容器使用CPU资源的相对权重，默认一个容器能用满一个CPU，用于设置多个容器竞争CPU时，各个容器相对能分配到CPU时间占比. |
| \--cpu-period=0 | 限制容器在CFS调度下CPU占用的时间片，用于绝对设置容器能使用的CPU时间. |
| \--cpu-quota=0 | 限制容器在CFS调度器下的CPU配额，用于绝对设置容器能使用CPU的时间 |
| \--cpuset-cpus="" | 限制容器能使用那些cpu核数， |
| \--cpuset-mems="" | NUMA架构下使用那些核心内存. |

#### 2.4 Docker 容器内存资源限制.

| 参数 | 说明 |
| --- | --- |
| \--kernel-memory="" | 限制容器使用内核的内存大小 |
| \-m,--memory="" | 限制容器内应用使用内存的大小 |
| \--memory-reservation="" | 当系统中内存过小时，容器会被强制限制内存到给定值默认情况下等于内存限制值 |
| \--memory-swap="LIMIT" | 限制容器使用内存和交换分区的大小 |
| \--oom-kill-disable=true | 内存耗尽时是否终止容器 |
| \--oom-score-adj="" | 调整容器的内存耗尽参数 |
| \--memory-swappiness="0-100" | 调整容器的内存交换分区参数 |

#### 2.5 Docker 容器磁盘IO控制.

| 参数 | 说明 |
| --- | --- |
| \--device-read-bps | 限制此设备上的读速率 |
| \--device-read-iops | 通过每秒读IO次数限制指定设备的读速率 |
| \--device-write-bps | 限制此设备的写速率 |
| \--device-write-iops | 通过每秒写IO次数限制指定设备的写速率 |
| \--blkio-weight | 容器默认磁盘IO的加权值， |
| \--blkio-weight-device | 针对特定设备的IO加权控制. |

\*\*后续更新Docker容器安全相关的命令. \*\*