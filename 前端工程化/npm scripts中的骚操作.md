## 简介

作为前端开发，`npm script` 基本上每天都会用到，但是呢，很多同学可能都是只停留在`run`的层面，并没有去深入了解。今天就来介绍一些不一样的`npm script`操作。希望能对小伙伴们有所帮助。

## 什么是 npm script

`npm script`其实我们每天都在使用，就是我们`package.json`文件`scripts`属性里面的语句。比如我们常用的`npm run dev、npm run serve、npm start`等等都是`npm script`。

## 运行单个命令

首先我们在`package.json`文件新建一个`test script`，内容是输出`hello randy`

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0ed48e88086d421f9da6fa881032839c~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

然后我们运行`npm run test`，控制台可以看到

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bda915e8e3da4cf88c310e610e4e69d9~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

至此我们的第一个`npm script`就运行成功啦。

## 运行多个命令

在实际开发过程中可能会碰到多个命令一起启动的情况。比如我们前后端一起启动。

运行多个命令就会涉及到串行和并行问题了，下面笔者分开讲解。

### 串行

串行实现方式也比较简单，只需要用 `&&` 符号把多条 `npm script` 按先后顺序串起来即可。

```js
"scripts": {
  "frotend": "echo \"前端服务启动啦\"",
  "server": "echo \"后端服务启动啦\"",
  "all1": "npm run frotend && npm run server",
},
```

运行结果如下

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8e72c73e860e4a25ba9f0c7944668e66~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

需要注意的是，串行执行的时候如果前序命令失败，后续全部命令都会终止。

### 并行

有时为了提高效率，我们希望多个命令并行执行，只需要用 `&` 符号把多条 `npm script` 按先后顺序串起来即可。

```js
"scripts": {
  "frotend": "echo \"前端服务启动啦\"",
  "server": "echo \"后端服务启动啦\"",
  "all2": "npm run frotend & npm run server",
},
```

运行结果如下

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4c986c88ee084de9b3482b954858b611~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

其实除了使用`&& 和 &`，我们还有更优雅的方式，可以使用[npm-run-all](https://github.com/mysticatea/npm-run-all "https://github.com/mysticatea/npm-run-all")或[concurrently](https://github.com/open-cli-tools/concurrently "https://github.com/open-cli-tools/concurrently")。

### 更好的方式1 npm-run-all

首先我们来安装

```shell
npm i npm-run-all -D
```

然后添加我们的`npm-run-all`命令

```js
"scripts": {
  "frotend": "echo \"前端服务启动啦\"",
  "server": "echo \"后端服务启动啦\"",
  "all3": "npm-run-all frotend server",
},
```

运行结果如下

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c6216ccdc0974c2481def46fa686d551~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

上面是串行执行的，当然我们也可以并行执行，只需要加上`--parallel`

```js
"scripts": {
  "frotend": "echo \"前端服务启动啦\"",
  "server": "echo \"后端服务启动啦\"",
  "all4": "npm-run-all --parallel frotend server",
},
```

运行结果如下

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fc50131ed0824134b061ca779a578e74~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

[npm-run-all](https://github.com/mysticatea/npm-run-all "https://github.com/mysticatea/npm-run-all")还有很多用法，这里只是一个入门，感兴趣的同学可以查看文档自行学习。

### 更好的方式2 concurrently

首先我们来安装

```shell
npm i concurrently -D
```

然后添加我们的`concurrently`命令

```js
"scripts": {
  "frotend": "echo \"前端服务启动啦\"",
  "server": "echo \"后端服务启动啦\"",
  "all5": "concurrently \"npm run frotend\" \"npm run server\""
},
```

运行结果如下

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/aa70e6e4b2af4d36b88a245e294017a2~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

[concurrently](https://github.com/open-cli-tools/concurrently "https://github.com/open-cli-tools/concurrently")还有很多用法，这里只是一个入门，感兴趣的同学可以查看文档自行学习。

## 给 npm script 传递参数

`eslint`我们都知道，是前端开发代码检测的一个重要工具。我们经常配置两条`script`，一条用来检测错误，一条用来进行简单错误的修复。

```js
"lint": "eslint js/*.js",
"lint:fix": "eslint js/*.js --fix"
```

笔者`js`文件夹下有这样一个`js`文件，它是空格问题。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/71fe241ce1384374bc9d28da820d71af~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

我们运行`npm run lint`，可以看到如下错误

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7c5f393bf3284296be5a4337e9004524~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

空格这种简单问题`eslint`是可以自动修复的，我们运行`npm run lint:fix`

可以看到，空格问题被自动修复了

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/670af472b7ea47f29892c6e5c58c8911~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

其实学习完`npm script` 传递参数后，我们一条命令就可以搞定了。

> `npm script`使用`--`传递参数。

我们在`package.json`里面只需要配置一条命令

```js
"lint": "eslint js/*.js",
```

需要修复的时候我们通过传递参数的方式进行处理。

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/05a94e35518b4bfa97ffc902b7a1e33a~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

可以看到，空格问题也被自动修复了，其实它运行的就是`eslint js/*.js --fix`

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/670af472b7ea47f29892c6e5c58c8911~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

通过参数的方法可以大大减少我们`script`的数量，让`script`灵活度更高，特别是某些需要传递各种参数的`script`。

## npm script 日志

在运行 `npm script` 出现问题时你需要有能力去调试它，我们可以通过看日志来分析问题。日志级别是可以通过参数控制的。

### 默认日志输出级别

即不加任何日志控制参数得到的输出，可能是你最常用的，能看到执行的命令、命令执行的结果。

比如笔者上面的例子

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2c51b58713ec47268aaab4598af6d82c~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

### 显示更少日志

如果需要显示更少日志，我们只需要在运行`script`的时候传递`--loglevel silent`或者 `--silent`或者更简单的 `-s` 来控制。

比如笔者上面的例子

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b5fd8baf32a44233af35a72502d4f61a~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

就只会输出结果，非常简单。

### 显示更多日志

如果需要显示更多日志，我们只需要在运行`script`的时候传递`--loglevel verbose`或者 `--verbose`或者更简单的 `-d` 来控制。

比如笔者上面的例子

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8bc7e914ef954bcfb9186fbd41536775~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

可以看到，输出了非常多的信息。

## npm script 钩子

为了方便开发者自定义，`npm script` 的设计者为命令的执行增加了类似生命周期的机制，具体来说就是 `pre` 和 `post` 钩子脚本。这种特性在某些操作前需要做检查、某些操作后需要做清理的情况下非常有用。

举例来说，运行 `npm run test` 的时候，分 3 个阶段：

1.  检查 scripts 对象中是否存在 pretest 命令，如果有，先执行该命令；
2.  检查是否有 test 命令，有的话运行 test 命令，没有的话报错；
3.  检查是否存在 posttest 命令，如果有，执行 posttest 命令；

还是上面代码检查的例子，我们创建三条命令

```js
"lint": "eslint js/*.js",
"prelint": "eslint js/*.js --fix",
"postlint": "echo \"eslint执行完毕\""
```

我们在每次`lint`前，自动进行代码修复，执行完`lint`后输出`eslint`执行完毕。我们来看下效果

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fbb1e933feba418ca2a5f582db8036f0~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

我们只执行了`npm run lint`，但是它执行了`npm run prelint、npm run lint、npm run postlint`三条命令。

## npm script 变量

`npm script` 可以使用预定义变量和自定义变量。

### 预定义变量

预定义变量其实就是解析整个`package.json`文件获取各个字段和值。

通过运行 `npm run env` 就能拿到完整的变量列表，这个列表非常长，这里我使用 `npm run env | grep npm_package | sort` 拿到部分排序后的预定义变量：

笔者的文件如下

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9e2fd0b382e4486da032abfd54cd5330~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

变量的使用方法遵循 `shell` 里面的语法，直接在 `npm script` 给想要引用的变量前面加上 `$` 符号即可。

```js
"var1": "echo $npm_package_author_name"
```

执行`npm run var1`，运行结果如下

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3956acb3ab65431e8845ab60f2061689~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

### 自定义变量

除了预定义变量外，我们还可以在 `package.json` 中添加自定义变量，并且在 `npm script` 中使用这些变量。

自定义变量需要配置在`package.json`里面`config`字段里。

```js
{
  "config": { "say": "hello randy" },
}
```

使用跟预定义变量一样，在变量前面加上 `$` 符号即可。

```js
"var2": "echo $npm_package_config_say"
```

执行`npm run var2`，运行结果如下

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6b15996ed3344bce899e0ca0a56d49e3~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

> 注意 `Linux、Mac` 下直接可以用 `$npm_package_name`，而 `Windows` 下必须使用 `%npm_package_name%`。

## npm script 跨平台兼容

前面说到`Linux、Mac`和`Windows`引用变量的方式是不同的。

可能有部分同学处理过 `npm script` 跨平台兼容的问题，比如粗暴的为两种平台各写一份 `npm script`，像下面这样：

```js
{
  "name": "test",
  "scripts": {
    "bash-script": "echo Hello $npm_package_name",
    "win-script": "echo Hello %npm_package_name%"
  }
}
```

那有没有什么更好的办法来实现呢？

### cross-var

[cross-var](https://github.com/elijahmanor/cross-var "https://github.com/elijahmanor/cross-var")就是用来解决跨平台变量使用问题的。

首先我们安装 `cross-var`

```shell
npm i cross-var -D
```

然后修改我们的命令

```js
"var3": "cross-var echo %npm_package_config_say%",
"var4": "cross-var echo $npm_package_config_say"
```

我们来看下运行结果

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4c9ee85b1868486ca4807c4f000c81a7~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

笔者是`mac`，可以看到，使用`windows`的方式引用变量也是能正常运行的。这就是`cross-var`的魅力。

有写小伙伴可能会说啦，变量平时使用的不多，环境变量倒使用的不少，那有什么跨平台使用环境变量的方式吗？

### cross-env

[cross-env](https://github.com/kentcdodds/cross-env "https://github.com/kentcdodds/cross-env")是用来解决跨平台环境变量问题的。

首先我们安装 `cross-env`

```shell
npm i cross-env -D
```

然后我们在设置环境变量的时候，加上`cross-env`就可以了。

```js
"env1": "cross-env NODE_ENV=staging"
```

这样环境变量也可以跨平台使用了。

## npm script 命令列表

对于一个新项目，如果想要知道它有哪些`script`我们一般都会找到`package.json`文件，然后查看它`scripts`字段。其实还有更简单的方法，直接在项目目录下执行`npm run` 就能列出 `scripts` 对象中定义的所有命令。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4eb7bf49ba32434d918099aa3184f074~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

这样是不是很爽呢。

## 运行 npm run xxx 的时候发生了什么

`npm run xxx`的时候，首先会去项目的`package.json`文件里找 `scripts` 里找对应的`xxx`，然后执行 `xxx` 的命令，例如启动`vue`项目 `npm run serve`的时候，实际上就是执行了`vue-cli-service serve` 这条命令。

为什么不直接执行`vue-cli-service serve`而要执行`npm run serve` 呢？

因为 直接执行`vue-cli-service serve`，会报错，因为操作系统中没有存在`vue-cli-service`这一条指令。

那既然`vue-cli-service`这条指令不存在操作系统中，为什么执行`npm run serve`的时候，也就是相当于执行了`vue-cli-service serve` ，为什么这样它就能成功，而且不报指令不存在的错误呢？

这就需要了解`npm run xxx`是怎么查找命令的啦。

1.  运行 `npm run xxx`的时候，`npm` 会先在当前目录的 `node_modules/.bin` 查找要执行的程序，如果找到则运行。
    
2.  没有找到则从全局的 `node_modules/.bin` 中查找，`npm i -g xxx`就是安装到到全局目录。
    
3.  如果全局目录还是没找到，那么就从 `path` 环境变量中查找有没有其他同名的可执行程序。如果再没有找到就会报错了。
    

因为我们在安装依赖的时候，是通过`npm i xxx` 来执行的，例如 `npm i @vue/cli-service`，`npm` 在安装这个依赖的时候，就会在`node_modules/.bin/` 目录中创建好`vue-cli-service` 为名的几个可执行文件了。

所以当使用 `npm run serve` 执行 `vue-cli-service serve` 时，`npm` 会到 `./node_modules/.bin` 中找到 `vue-cli-service` 文件作为脚本来执行，则相当于执行了 `./node_modules/.bin/vue-cli-service serve`。

有时我们可以全局安装，例如`npm i -g @vue/cli-service`，这样他会在全局的`node_modules/.bin`中创建可以执行程序，所以我们在电脑的任何目录下都能运行对应的命令。

好了，感谢小伙伴们的耐心观看，关于`npm script`今天就讲到这里。

![IMG_0387.jpeg](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c061a39effa94c26a5c1be1493e6ad76~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

## 后记

感谢小伙伴们的耐心观看，本文为笔者个人学习笔记，如有谬误，还请告知，万分感谢！如果本文对你有所帮助，还请点个关注点个赞~，您的支持是笔者不断更新的动力！