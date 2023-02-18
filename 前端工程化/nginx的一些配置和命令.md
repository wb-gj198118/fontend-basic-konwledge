在开发项目时，想要自己部署时，这就需要了解一些常用服务器的一些配置，或者命令等，这里主要以nginx来记录

## 常用命令及操作

### 安装Nginx 服务器

-   CentOS 环境
    
    `$ sudo yum install epel-release && yum install nginx`
    
-   Ubuntu环境
    
    `$ sudo dnf install nginx`
    

当然，在不同的场景及环境下，安装的命令大体都是类似的操作，接下来就拿一个举例子

### 检查版本

-   `nginx -v`

`[root@VM_0_3_centos umi-nest]# nginx -v nginx version: nginx/1.18.0 [root@VM_0_3_centos umi-nest]#` 

其中，这个就是咱们的版本 `nginx version: nginx/1.18.0`

### 检查配置语法是否合法

-   `nginx -t`

`[root@VM_0_3_centos umi-nest]# nginx -t nginx: [emerg] invalid number of arguments in "root" directive in /www/server/panel/vhost/nginx/umi_nest.conf:6 nginx: configuration file /www/server/nginx/conf/nginx.conf test failed`

上文，说明我们的配置是有问题的不是吗 **failed** 至于为什么错误，当然是咱们的配置是有点问题，其实咱们只需要了解基本的配置就像

### 启动Nginx服务

-   `service nginx start`
    
    `[root@VM_0_3_centos umi-nest]# service nginx start Starting nginx... nginx (pid 7691 7690 6001) already running.`
    

说明咱们的`nginx` 是正在启动

### 重启Nginx服务

-   `service nginx restart`
    
    `[root@VM_0_3_centos umi-nest]# service nginx restart Stoping nginx...  done Starting nginx...  done`
    

### 查看Nginx服务状态

-   `service nginx status`
    
    `[root@VM_0_3_centos umi-nest]# service nginx status nginx (pid 10352 10351 10350) already running.`
    

### 重新加载Nginx服务

-   `service nginx reload`
    
    `[root@VM_0_3_centos umi-nest]# service nginx reload Reload service nginx...  done`
    

### 停止Nginx服务

-   `service nginx stop`

### 命令示例

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/6/15/172b85e473deed9e~tplv-t2oaga2asx-zoom-in-crop-mark:4536:0:0:0.image)

### 总结

如若权限不够的话，请\*\*sudo \*\*,例如 `sudo nginx -s reload`

-   | nginx -s |  |  |
    | --- | --- | --- |
    |  | reload | 重新加载配置 |
    |  | reopen | 重启 |
    |  | stop | 停止 |
    |  | quit | 退出 |
    
-   查看进程的命令 `ps -ef |grep nginx`
    
    `[root@VM_0_3_centos umi-nest]# ps -ef |grep nginx root     10350     1  0 22:17 ?        00:00:00 nginx: master process /www/server/nginx/sbin/nginx -c /www/server/nginx/conf/nginx.conf www      10773 10350  0 22:20 ?        00:00:00 nginx: worker process www      10774 10350  0 22:20 ?        00:00:00 nginx: cache manager process root     12631 24792  0 22:32 pts/0    00:00:00 grep --color=auto nginx`
    

## 负载均衡

我们可以通过通过简单的配置实现 小小的负载均衡，我们可以举个例子

`upstream tomcats{     server 192.168.25.148:8080 weight=2;     server 192.168.25.148:8081; }  server {     listen       80;     server_name  tomcat.test.com;     location / {         proxy_pass   http://tomcats;         index  index.html index.htm;     } }`

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/6/15/172b881069557f6c~tplv-t2oaga2asx-zoom-in-crop-mark:4536:0:0:0.image)

只需要在upstream的server后面添加一个weight即可代表权重。权重越高，分配请求的数量就越多。默认权重是1。也就是当请求过来的时候，会有很多的实例来 均衡

## 关联阅读

-   \[10 Most Used Nginx Commands Every Linux User Must Know\](10 Most Used Nginx Commands Every Linux User Must Know)
    
-   [Flutter & Koa2 实战全面升级，试问谁不孤独？（小万字长文）](https://juejin.cn/post/6844904094625169415#heading-52 "https://juejin.cn/post/6844904094625169415#heading-52")
    
    其中有牵扯到 `pm2` 等等，
    

## 写在最后

-   关于文中的图片素材有的来自三方网站