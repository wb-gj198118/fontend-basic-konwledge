## Socket是什么？

-   TPC/IP协议是传输层协议，主要解决数据如何在网络中传输；
-   Socket是对TCP/IP协议的封装和应用(程序员层面上)；
-   而HTTP是应用层协议，主要解决如何包装数据。

**TCP/IP和HTTP协议**的关系是：“我们在传输数据时，可以只使用(传输层)TCP/IP协议，但是那样的话，如果没有应用层，便无法识别数据内容。如果想要使传输的数据有意义，则必须使用到应用层协议。应用层协议有很多，比如HTTP、FTP、TELNET等，也可以自己定义应用层协议。WEB使用HTTP协议作应用层协议，以封装HTTP文本信息，然后使用TCP/IP做传输层协议将它发到网络上。”

Socket是什么呢，实际上socket是对TCP/IP协议的封装，Socket本身并不是协议，而是一个调用接口(API)。通过Socket，我们才能使用TCP/IP协议。  
**Socket跟TCP/IP协议关系**是：“TCP/IP只是一个协议栈，就像操作系统的运行机制一样，必须要具体实现，同时还要提供对外的操作接口。这个就像操作系统会提供标准的编程接口，比如win32编程接口一样，TCP/IP也要提供可供程序员做网络开发所用的接口，这就是Socket编程接口。”

![在TCP/IP网络中HTTP的位置](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/97e9a6cc686b4ed08f18905eef0a293a~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

从上图中可以看到，HTTP是基于传输层的TCP协议的，而Socket API也是，所以只是从使用上说，可以认为Socket和HTTP类似（但一个是成文的互联网协议，一个是一直沿用的一种编程概念），是对于传输层协议的另一种直接使用，因为按照设计，网络对用户的接口都应该在应用层。

## WebSocket协议的来源：

WebSocket属于WHATWG发布的Web Application的一部分（即HTML5）的产物。大约在08年的时候，WG的工程师在讨论网络环境中需要一种全双工的连接形式，刚开始一直叫做「TCPConnection」，并讨论了这种协议需要支持的功能，大致已经和我们今天看到的WebSocket差不多了。他们认为基于现有的HTTP之上的一些技术（如长轮询、Comet）并满足不了这种需求，有必要定义一个全新的协议。  
WebSocket协议的开篇就说，本协议的目的是**为了解决基于浏览器的程序需要拉取资源时必须发起多个HTTP请求和长时间的轮训的问题……而创建的**。

## WebSocket和Socket的区别？

两者没什么关系，就像雷锋跟雷锋塔一样。  
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ede270cb66de436ba56f231dc80dc4ed~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

## 为什么要使用Websocket？

WebSocket的目的就是解决网络传输中的双向通信的问题，HTTP1.1默认使用持久连接（persistent connection），在一个TCP连接上也可以传输多个Request/Response消息对，但是HTTP的基本模型还是一个Request对应一个Response。这在双向通信（客户端要向服务器传送数据，同时服务器也需要实时的向客户端传送信息，一个聊天系统就是典型的双向通信）时一般会使用这样几种解决方案：

-   轮询（polling），轮询就会造成对网络和通信双方的资源的浪费，且非实时。
-   长轮询，客户端发送一个超时时间很长的Request，服务器hold住这个连接，在有新数据到达时返回Response，相比#1，占用的网络带宽少了，其他类似。
-   长连接，其实有些人对长连接的概念是模糊不清的，我这里讲的其实是HTTP的长连接（1）。如果你使用Socket来建立TCP的长连接（2），那么，这个长连接（2）跟我们这里要讨论的WebSocket是一样的，实际上TCP长连接就是WebSocket的基础，但是如果是HTTP的长连接，本质上还是Request/Response消息对，仍然会造成资源的浪费、实时性不强等问题。

## Websocket协议内容：

WebSocket的目的是取代HTTP在双向通信场景下的使用，而且它的实现方式有些也是基于HTTP的（WS的默认端口是80和443）。现有的网络环境（客户端、服务器、网络中间人、代理等）对HTTP都有很好的支持，所以这样做可以充分利用现有的HTTP的基础设施，有点向下兼容的意味。 WS协议有两部分组成：握手和数据传输。

### 握手

使用Http进行实现。由客户端使用http的方式发起握手请求，服务端接请求后，将当前正在使用的连接（TCP）的协议，由http协议切换为websocket协议。

握手请求头会带有Upgrade参数用于升级协议类型：

**Upgrade**：upgrade是HTTP1.1中用于定义转换协议的header域。它表示，如果服务器支持的话，客户端希望使用现有的「网络层」已经建立好的这个「连接（此处是TCP连接）」，切换到另外一个「应用层」（此处是WebSocket）协议。

> Upgrade扩展：Upgrade是HTTP中用来进行协议升级的头域，在扩展的协议内容中，客户端发起的协议转换的方式更多，同时服务器也可以选择不接受客户端的协议升级请求；服务端也可以发起协议升级。

请求uri格式：

```javascript
  ws-URI = "ws:" "//" host [ ":" port ] path [ "?" query ]
  wss-URI = "wss:" "//" host [ ":" port ] path [ "?" query ]

  host = <host, defined in [RFC3986], Section 3.2.2>
  port = <port, defined in [RFC3986], Section 3.2.3>
  path = <path-abempty, defined in [RFC3986], Section 3.3>
  query = <query, defined in [RFC3986], Section 3.4>
```

### 数据传输

服务端接收握手请求后，回复response消息，一旦这个握手回复发出去，服务端就认为此WebSocket连接已经建立成功，处于OPEN状态。它就可以开始发送数据了。  

WebSocket中所有**发送的数据使用帧的形式发送**。客户端发送的数据帧都要经过掩码处理，服务端发送的所有数据帧都不能经过掩码处理。否则对方需要发送关闭帧。

一个帧包含一个帧类型的标识码，一个负载长度，和负载。负载包括扩展内容和应用内容。

## WebSocket和HTTP的对比

### 相同点

-   都是基于TCP的应用层协议。
-   都使用Request/Response模型进行连接的建立。
-   在连接的建立过程中对错误的处理方式相同，在这个阶段WS可能返回和HTTP相同的返回码。
-   都可以在网络中传输数据。

### 不同点

-   WS使用HTTP来建立连接，但是定义了一系列新的header域，这些域在HTTP中并不会使用。
-   WS的连接不能通过中间人来转发，它必须是一个直接连接。
-   WS连接建立之后，通信双方都可以在任何时刻向另一方发送数据。
-   WS连接建立之后，数据的传输使用帧来传递，不再需要Request消息。
-   WS的数据帧有序。

> 参考：[www.jianshu.com/p/59b5594ff…](https://www.jianshu.com/p/59b5594ffbb0 "https://www.jianshu.com/p/59b5594ffbb0")