## Docker 优势

老规矩，在温习学习 `Docker` 的使用玩法之前，我们先来看下为什么要学习 `Docker`？

**第一优势**：**跨平台**，由于 `Docker` 的镜像能够提供除了系统内核之外完成的运行环境，所以能在任何系统中都能提供**一致的运行环境**，这样就不需要考虑不同系统中间兼容性的问题，也就不存在虚拟机在各系统中间的配置不同的情况。

**第二优势**：就是借助于跨平台的特性，`Docker` 可以将很多配置复杂的服务端中间件打包成基础镜像提供给开发使用。这样无疑能够大大降低配置成本，开发只需要知道常规的 `Docker` 相关的命令或者直接运行提供的容器编排脚本就可以搭建出需要使用的服务端环境。同时公共的镜像仓库上已经有很多这种基础镜像，例如 `Mysql`、`Redis`、`Node` 等等，按需提取即可。

**第三优势**：这点对于运维同学比较好操作，`Docker` 能提供快速迁移以及配合 `k8s`，能够快速的伸缩副本，减少运维的工作成本与负担。

`Docker` 也是借助上述优势将非常迅速的发展自身生态，特别是近两年熟练使用 `Docker` 也已经频繁的出现在高级前端的 **JD** 中了。

> 虽然说了是跨平台，但结合自身的体验来看，`Docker` 在 `windows 11` 中的兼容性做的并不好（如果有熟悉如果在 `Windows` 使用 `Docker` 的同学可以联系我沟通一下），此外由于文件内存处理格式的问题，在数据持久化中，直接使用 `volumes` 对大量文件读取性能有影响，这一点在之前字节的 `Dev Better` 沙龙中的前端工程化中也聊过（**all in docker** 方案），有兴趣的同学也可以联系我沟通。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3121e1e6acf24b928945e26502263800~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

#### Docker 基本知识

接下来在正式使用 `Docker` 之前，先来掌握两个必备的概念：镜像（`Image`）与容器（`Container`）。

**何为镜像**？

如果喜欢折腾的同学应该自己装过 `Windows` 系统，我们在制作启动盘的时候，会将 `Windows` 某个版本的 `ISO` 镜像下载下来再制作系统盘，同理 `Docker` 的镜像也可以这么理解，它是包含了一个完整功能的最小系统。

`Docker Image` 作为文件联合系统（`Union FS`），它由一层层 `layer` 堆叠组成，这意味着我们可以基于基础镜像来构建各个业务的或者更高级的中间件镜像。同时借助镜像堆叠的功能，在拉取新的镜像时，避免拉取本地已存在的基础镜像，节约资源且提高效率。

**何为容器**？

由上述安装 `Windows` 的例子来看，容器可以看成已经成功装机的电脑，可以运行某些命令、执行程序等操作。

一般只需要制作一个启动盘就可以安装多台电脑，`Docker Image` 构建完毕之后，与启动盘一样可以直接基于 `Image` 启动多个不同的容器，而且他们之间是相互隔离的。

> 简单的概念就如上所说，能够大概了解基本的特性即可满足常规使用，更加细节的原理性的问题则需要各位同学自己去探索。

## Docker 命令的学习

再了解完 `Docker` 的一些基本概念后，接下来我们继续学习日常开发中常用的 `Docker` 命令。

首先可以使用以下命令获取 `image` 相关的命令信息：

```shell
$ docker image help
```

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/738a7de2dae84b00983d655fcc330c94~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

#### 查看镜像

```shell
$ docker image
$ docker image ls
$ docker image ls -a 
$ docker image ls -q
```

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d7d16b8affa5471ea3c9da2d36be90c9~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

如上图所示，除了最后一条命令只展现 `Image ID` 之外，前面三条都是一样的效果，所以随便输入就行。

#### 拉取镜像

```arduino
$ docker image pull 【imageName:version】
```

镜像的命名规则为 `Name:version` 的模板，一般在 `CI/CD` 的过程中，是按照 version 来判断是否需要拉取新的镜像，所以很少会同版本覆盖构建上传

与 Node 一样，`Docker` 也提供了公共的仓库服务 [Docker Hub](https://hub.docker.com/search?q=&type=image "https://hub.docker.com/search?q=&type=image")：

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e8b1b006e43f423db36c6876a768fcff~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

我们日常所需要的基础镜像都可以从公共仓库中获取:

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b882b5fb33894ccc8ec9c347e760501c~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

为了后面的测试方便，我们可以先拉取一个 `Node` 的稳定版本 `node:16-alpine3.15` 镜像到本地

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9fd2c189fb1c40ce881285f88f2b8ff7~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

> 如果大家观察仔细的话，在拉取镜像的过程中，图中圈出来的内容在拉取中会带有 `layer` 标识。

一般来说名称带 `Alpine` 或 `BusyBox` 的镜像都非常小，在构建业务镜像可以优先考虑，比如刚刚拉取的 `node:16-alpine3.15` 镜像包在 `100M` 左右，假设有特殊需求，需要 `Linux` 某些特殊功能如 `ssh` 的话，有能力话也可以基于基础镜像来构建，嫌麻烦的话则可以直接选择已有的 `Centos` 或者其他 `Linux` 镜像。

#### 构建镜像

接下来我们将一起来构建一个简单的镜像，首先我们需要在 `demo` 工程的根目录下创建一个 `Dockerfile`，如下图所示：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1f773e450cfb43558124b01f5dd47f9e~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

```dockerfile
# dockerfile

FROM node:16-alpine3.15

RUN mkdir -p /home/app/

WORKDIR /home/app/

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 8080

ENTRYPOINT ["npm", "run"]

CMD ["serve"]
```

接下来项目根路径下直接运行下述命令构建镜像：

```shell
$ docker build -f ./Dockerfile -t test:0.0.1 .
```

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3cde021b3fd949309053a285a5f70b75~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

由于之前我们已经预先将 `node:16-alpine3.15` 基础包拉取下来了，所以在上图的构建过程中可以发现，并没有额外再拉取新的 `layer`。

再次使用 `docker images` 可以看到本地多了一个 `test/0.0.1` 的镜像：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/221b6fb3303d4f61affe0bb3434cefa8~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

#### 运行容器

```shell
$ docker run -d -p 8080:8080 test:0.0.1
```

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9f154d0257f3418f89a06fd29ee170a4~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

如果与图上保持一致，那么代表已经完成容器的启动，浏览器 [http://localhost:8080/](http://localhost:8080/ "http://localhost:8080/") 可以看到如下节目则代表启动成功。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/937aeafae2de433981b84a67db441976~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

为了验证之前的容器概念，我们可以再次启动一个暴露端口为 **3000** 的容器：

```shell
$ docker run -d -p 3000:8080 test:0.0.1
```

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cd588888651f49099446ded49d3f0b59~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

> 基于这个特性，我们可以在开发工程的时候规范一下开发规则，使用运行时注入环境变量的方式来区分 **dev**、**test**、**prod**，可以达到一次构建多环境部署的效果，避免构建中出现其他依赖问题导致生产事故。

> `Demo` 工程采用的是 vue-cli 创建的基础工程，如果想使用其他的前端工程，需要调整端口与启动命令。

#### 查看容器

```ruby
$ docker ps      查看当前运行中的容器
$ docker ps -a   查看所有容器，包括已停止的容器
$ docker stop [容器ID/Name] 停止选定的容器
```

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6ba0d0f36c684e7b8bce97a10f343843~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

#### 删除容器跟镜像

```shell
$ docker rmi [镜像ID]   删除选定镜像
$ docker rm [容器ID/Name] 删除选定的容器
```

> 注意：运行中的镜像是无法删除的，存在容器的时候也是无法删除镜像的。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c1a3bbb3be3c4555b23d24d0b68b9dbf~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

#### 进入容器

```shell
$ docker exec -it [容器ID] /bin/sh
```

如果上述命令不行的话，最后的地址可以换成 `/bin/bash`，进入路径与构建镜像有关系，示例使用的 `alpine` 基础镜像，所示使用上述命令。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/04b66a9759fa40e588901fe7e1300576~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

接下来我们来修改容器中的内容看看是否生效：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9e59bf821a6f4d489d4f76cf81875bc9~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0d7e6c867d894ca89f8ce071b02dd952~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

修改完毕，再次查看页面就能看到内容已经被修改完毕，最后可以使用 `exit` 退出当前容器。

#### 查看容器日志

```css
$ docker logs [容器ID]
```

使用 `logs` 命令，可以看到容器中运行的日志信息，下图就是我们 `Vue` 项目启动的日志以及我们修改之后重启的日志信息：

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/35196fbc0b9643098200c80d4224eae8~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

以上是一些常见的 `Docker` 命令，大家可以尝试使用下，更多的命令可以查看官网，后面介绍 `Docker` 的一些高阶玩法。
