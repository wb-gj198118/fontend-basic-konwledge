## 简介

`mac`抓包神器`charles`.`charles`的使用和介绍笔者不再多说，网上教程很多。今天主要介绍的是使用`charles`在`mac`上怎么抓取PC端、移动端的`http`和`https`包。

别急着划走，结尾有惊喜。

## PC端抓取http包

`charles`还是比较智能的，`charles`运行时会在本地建立一个代理服务，默认地址：127.0.0.1:8888，当我们启动`charles`的时候会自动给我们的浏览器设置代理，浏览器的请求我们就可以在`charles`面板看到了，但是此时只能捕获到`http`的请求，`https`的请求需要我们进一步配置。

如果启动`charles`后并没有捕获到请求，请检查你的浏览器是否`使用了代理软件`，这里是个大坑，一定要注意。

## PC端抓取https包

首先我们需要安装根证书。找到`Help -> SSL Proxying -> Install Charles Root Certificate`。

![WX20220318-175423.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/029dbaa5a7824b53ac2a714a50751932~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

证书安装完后默认不是信任的，所以我们还需要将其改为始终信任。

![WX20220318-175533.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c203a82b2ae741ce80a85296fb66c15c~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

你以为这样就可以了吗，不是的，还得将我们需要抓取的`https`域名进行配置。配置有两种方法，第一种手动配置。找到`Proxy -> SSL Proxying Settings`。在`SSL Proxying`栏目下添加想要抓取的域名。

![WX20220318-180016.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0a459ba33f0b4a20b60adacd94276fa5~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

第二种，直接选择开启，我们主需要在首页`Structure`栏目下选择想要捕获`https`的域名，然后右键选择`Enable SSL Proxying`开启即可。使用这种方式会自动在`SSL Proxying`栏目下添加该域名。

![WX20220318-180246.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c3f86e6c33974dcf84345dcd60e4a903~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

这样，使用`charles`在`mac`上既能捕获到`http`的包还能捕获到`https`的包啦。

## 移动端抓取http包

手机端抓取`http`包需要开启`charles`代理。找到`Proxy -> Proxy Settings`。把`Support HTTP/2`和`Enable transparent HTTP proxying`勾选中保存。

![WX20220318-181212.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0cdae947edc2446abdb31714646f726f~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

然后查看本机`内网ip`，找到`Help -> Local IP Address`即可看到。

![WX20220318-181553.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6e1cab583ae249cbaaa52588850988d2~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

我们需要保证电脑和手机连接在同一内网，也就是在同一个路由器上，然后在手机上手动配置代理即可。服务器就是我们电脑的`局域网ip`，端口是我们设置的，如果没有改动默认是`8888`。

![IMG_0448.jpeg](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/910092ee25614edbb9cde2cff1dfccf0~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

然后我们在移动端访问`http`链接的请求都能被`charles`正常捕获啦。

注意，首次在手机端获取请求的时候 `charles` 会弹出手机请求连接的确认菜单，点击 **Allow** 即可。

## 移动端抓取https包

移动端抓取https包，我们还需要在手机端下载证书并安装。找到`Help -> SSL Proxying -> Install Charles Root Certificate on a Mobile Device or Remote Browser`。就会看到如下提示。

![WX20220318-190725.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/91643f8a407a4f5c9649aa26852b604f~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

笔者是按顺序讲的，这里默认我们的mac已经安装好了根证书，并且手机也配置好了代理。

我们在手机浏览器输入`chls.pro/ssl`，把证书下载下来，然后安装即可。苹果手机在`系统设置 -> 通用 -> 描述文件里面`。笔者这里安装了`fiddler`和`charles`的证书，你们只需要保证安装了`charles`的证书即可。

![IMG_0449.jpeg](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7068944086d64d279522c077f831aecf~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

注意，这里我们也是需要把需要捕获的`https`域名配置在`Proxy -> SSL Proxying Settings`的`SSL Proxying`栏目下。

安卓手机配置方法类似，笔者就不再赘述了。

## 惊喜

`charles`是收费的，允许免费使用30天。笔者这里有个注册码，可以供小伙伴们使用。

-   Registered Name: `https://zhile.io`
-   License Key: `48891cf209c6d32bf4`

该码来源于网络，如有侵权联系删除即可。

## 扩展

### safari调试ios网页

有时我们想用`ios真机`来调试我们的网站，就可以使用这种方法啦。只需要简单的三步。

第一步，打开Mac上Safari的开发者模式，流程是【Safari】->【偏好设置】->【高级】->【在菜单栏中显示“开发”菜单】勾选；

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0cb36859b7f44847aa17e841aefed4f6~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

第二步，打开iphone手机的开发者模式，流程是：【设置】->【Safari】->【高级】->开启【Web检查器】；

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dcf7e58ebc1647e2a8dc0050ab574262~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

第三步，用数据线将iphone手机和mac连接起来，在电脑的safari中按照流程执行：【开发】->【手机名称】->【正在调试的网站】；

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ccaed994aa8046c4aba4726e34136301~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

选完需要调试的网站后，mac上就会出现控制台了，我们就能在控制台进行我们想要的操作啦。