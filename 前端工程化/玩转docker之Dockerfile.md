## 前言

当我们对 `Docker` 和常用命令有了简单的认识之后。

期间也使用了 `Dockerfile` 构建了一个 `Vue` 的 `dev` 镜像，在一起学习了 `Docker` 的基础知识之后，也就到需要实际运用 `Docker` 阶段了。

在项目正式发布之前，我们需要借助 `Dockerfile` 来构建各种适配的镜像，然后再将镜像推送到制品库，再通过其他手段发布，例如使用 `k8s` 来发布。

**现在我们可以试着如何在实际项目中使用 `Dockerfile`**

## Dockerfile

```dockerfile
# dockerfile

FROM node:16-alpine3.15

WORKDIR /home/app/

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 8080

ENTRYPOINT ["npm", "run"]

CMD ["serve"]
```

上述脚本时一个简单的关于 `Dockerfile` 的例子，接下来对每一行命令进行描述下，看下这些命令都是干嘛的：

1.  **FROM**

为这次构建流程指定基础镜像，同时必须是**第一条指令**，因为后续所有的操作都必须基于基础镜像之上，每一行运行系统的命令都必须是基础镜像中所含有的，所以当基础镜像中不包含 `Node` 环境的话，后续的命令也无法正常使用 `Node` 相关的 `API`，除非你在后续调用 `Node API` 之前已经安装过。

同时镜像名称的规则之前也提过，为 `<imageName>:<tag>`，当未填写 `tag` 的时候，默认为需要拉取式最新的 `tag` 版本。

2.  **WORKDIR**

此命令是设置当前工作目录，如果目录不存在，则会直接创建，设置完毕之后，所有操作的路径都将处于当前指定路径之下。

3.  **COPY**

顾名思义，这是一个复制的命令，但是 `COPY` 只能复制宿主机本地的文件。

4.  **ADD**

与 `COPY` 的功能类似但 `ADD` 的功能更加强大，具备跨服务器复制内容的能力，类似于 `Linux` 命令中的 `scp`，如果将来源换成 `url` 的话，那么又会变成 `wegt` 的命令，整体可操作性更高，往后可以自己去尝试一下。

> 此外在执行 <**源文件**\> 为 tar 压缩文件的话，压缩格式为 `gzip`, `bzip2` 以及 `xz` 的情况下，会自动复制并解压到 <**目标路径**\>，由于解压可能会导致缓存失效，所以仅仅是复制文件的需求，建议使用 `COPY`。

5.  **EXPOSE**

声明当前容器要访问的网络端口，但是仅仅是声明要暴露的端口，启动的时候并不会直接能访问到，需要在启动容器的时候添加 `-p` 参数来挂载宿主端口。

6.  **RUN**

**在构建镜像过程中执行的命令**，需要执行的命令格式如下：

```bash
1. RUN <command>
2. RUN ["executable", "param"] 
```

第一种格式是直接执行 `shell` 命令，第二种则是启动文件（或者全局命令模式，例如 `npm`） + 传入参数的格式，如果 `shell` 命令不太容易满足功能的时候，可以使用第二种，直接写好处理的脚本，加传入参数即可。

7.  **CMD**

**在容器启动时候执行的命令**，执行命令的语法如下：

```objectivec
1. CMD command param
2. CMD ["executable", "param"]
```

启动命令与 `RUN` 也是类似的，分别是直接执行 `shell` 与执行可执行命令或文件。

**RUN & CMD**

这两个都是执行命令，但是 `RUN` 是**构建镜像时**就运行的命令，`CMD` 是**容器启动时**执行的命令，在构建时并不运行，所以在编写 `Docker File` 的时候不要将两者搞混。

此外 `Docker` 容器默认会把容器内部第一个进程，也就是 **pid=1** 的程序作为 `Docker` 容器是否正在运行的依据，所以为了 **Docker** 容器常驻，`CMD` 执行的 `Node` 服务（_其他服务也是一样_）的时候一定要前台启动，否则使用后台启动的话，容器启动后执行完命令就会自动退出。

> 在上一章提到过，`Docker Image` 作为文件联合系统（`Union FS`），由一层层 `layer` 堆叠组成，`Docker File` 进行的每一次指令操作会多添加一层 `layer`，所以在一些频繁的操作，例如 `RUN`、`ADD` 执行多个命令的时候，尽可能的合并命令，减少 `layer` 层级，这样可以避免镜像臃肿同时减少构建时间。但这个也是相对而言，如果多个命令需要处理的功能关系不大，为了后期维护方便还是建议分开执行，在**可维护性与效率**这两者关系平衡好就行。

8.  **ENTRYPOINT**

`ENTRYPOINT` 是指定镜像的默认入口命令，该入口命令会在启动容器时作为根命令执行，所有其他传入值作为该命令的参数，单独使用时与 `CMD` 脚本使用方式一致，执行命令脚本如下:

```css
ENTRYPOINT ["executable", "param1", "param2"]
```

当与 `CMD` 命令混合使用时，`CMD` 的功能就不再是直接运行脚本，而是将 `CMD` 的内容作为参数传给 `ENTRYPOINT`，所以在上述示例的 dockerfole 中大家会发现两种混合的写法：`ENTRYPOINT` 的参数是 `NPM RUN`，而 `CMD` 的参数是 `serve`。

当出现多次 `ENTRYPOINT` 脚本时，最后一条会覆盖上述所有的脚本，**即只有最后一条才会真实生效**，同时可以在启动容器的时候使用 `--entrypoint` 参数来覆盖。

> 虽然 `ENTRYPOINT` 与 `CMD` 两者都是有可以当作容器启动命令来看待，但是还是有不少的区别，这块的内容展开说说会有比较多的篇幅，最开始我们对 Docker 的学习还是先从熟悉命令与使用方式入手，比较深入或者难懂的内容后期会放在**加餐**里面进行详细的拓展。

## dockerignore

除了 `Dockerfile` 之外，正常使用中我们还可能接触到 `.dockerignore` 文件，一般存放在 `docker` 构建上下文的根目录（也就是项目根目录），用来排除不需要上传到 `docker` 服务端的文件或目录，它存在的价值有：

-   构建镜像时能避免不需要的大文件上传到服务端，从而拖慢构建的速度、网络带宽的消耗；
-   可以避免构建镜像时将一些敏感文件及其他不需要的文件打包到镜像中，从而提高镜像的安全性；

`.dockerignore` 的使用规则基本上与 `.gitignore` 保持一致就不过多讲解了，常规项目可以直接使用如下规则：

```bash
# compiled output
/dist
/node_modules

# Logs
logs
*.log
npm-debug.log*
pnpm-debug.log*
yarn-debug.log*
yarn-error.log*
lerna-debug.log*

# OS
.DS_Store

# Tests
/coverage
/.nyc_output

# IDEs and editors
/.idea
.project
.classpath
.c9/
*.launch
.settings/
*.sublime-workspace

# IDE - VSCode
.vscode/*
!.vscode/settings.json
!.vscode/tasks.json
!.vscode/launch.json
!.vscode/extensions.json

# cache
.yalc
yalc.lock
```

添加以上配置之后，后续执行 `COPY` 与 `ADD` 处理本地文件移动的时候就会根据以上规则忽略对应的文件了。

## 静态业务部署

市面上大部分的前端同学都是开发常规的 `Web` 项目，假设我们需要部署一个 `Web` 项目的话，也有不少的选择（例如 `oss + cdn` 模式），这里我们选择使用 `Docker` 来部署一个静态服务。

在上一章节，我们构建了一个 `Docker` 镜像来启动开发环境的 `Vue` 项目，真实项目中当然不会有 `dev-server` 这种来访问静态资源，所以我们需要一个像 `Nginx` 的静态服务器来帮助访问静态资源，但是我们既然使用了 `Node` 环境如果要使用 `Nginx` 的话，就需要切换基础镜像或者安装一个 `Nginx`，所以本示例采用 `anywhere` 库来作为静态服务。

> `anywhere` 本身就是前台启动服务，如果想使用 `Nginx` 来作为静态服务器的话，记得添加 `daemon off` 参数来强制 `Nginx` 进程前台启动。

那么之前的脚本就要从启动 `dev-server` 换成 `build` 脚本：

```diff
FROM node:16-alpine3.15

RUN mkdir -p /home/app/

WORKDIR /home/app/

RUN npm i anywhere -g

COPY package*.json ./

RUN npm install

COPY . .

RUN npm run build

EXPOSE 8080

CMD anywhere -p 8080 -d ./dist -s
```

接下来使用以下脚本即可打包出一个前端静态资源以及配置了静态服务的镜像

```shell
$ docker build -f ./Dockerfile -t test:0.0.5 .
```

使用一下脚本即可启动此镜像：

```shell
$ docker run -d -p 80:8080 test:0.0.5
```

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/210fe709faec47c997634de3a223f13e~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

启动完脚本之后可以直接访问 [http://localhost/](http://localhost/ "http://localhost/") 访问到我们的生产服务了。

> 注意再上一步启动容器的时候，我们将宿主机的映射端口改为了 **80** 端口，所以再访问项目地址就不再需要带端口号了，如果映射失败有可能是电脑用户权限没办法开启 **80** 端口。

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/913f4d45501a4671a612ae1505632685~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

## QA

#### 为什么要先复制 `package.json` 而不是跟项目一起复制进来构建

文章中也反复提及了 `Docker` 是文件联合系统的架构，在 `Dockerfile` 里面每一行命令都会生成一层 `Layer`，如果有相同的 `Layer` 的话，`Docker` 会直接复用已有，所以当这一次 `package.json` 无变化时，就不默认使用之前的 `Layer`，并不会重新拉去依赖。

而如果是跟着项目一起复制进来，由于大概率存在开发文件的差异性，必然会重新更新依赖。所以我们也是借助 `Docker` 的这一特性来做了层缓存操作来提高**构建效率**。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/018e445ca9ee4692a2fe3f509b7e5af5~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

如上图所示，在二次构建的时候，合理利用了这一规则，使用缓存跳过了依赖安装的过程。