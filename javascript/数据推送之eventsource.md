
## eventSource简介

`eventSourc`又称`SSE(Server-Sent-Events)`,一种服务器端到客户端(浏览器)的单项消息推送方案，它和轮询一样，也是基于http请求来完成的。但是和轮询不一样的是它是服务器与客户端建立连接后，服务器主动的向客户端进行数据推送。 ![7B2B7B77-B8BA-4d28-9B3B-5EEC8AE92382.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1f12bc6085e146bb828e3f8b38440075~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

-   优点
    1.  SSE是一个轻量级协议，相对简单。
    2.  SSE默认支持断线重连。
    3.  SSE支持自定义发送的数据类型。
    4.  SSE不支持CORS，参数url就是服务器网址，必须与当前网页的网址在同一个网域（domain），而且协议和端口都必须相同。
-   缺点
    1.  只适用高级浏览器，IE浏览器暂不支持
    2.  连接建立后，只能服务器给客户端推送数据，客户端无法给服务器端推送数据

## eventSource 实战应用

1.  创建eventSource实例

```js
var eventSourceUrl=url;
var eventSource = new EventSource(eventSourceUrl);
```

2.  eventSource建立后打开的方法

```js
eventSource.onopen = function () {
    console.log('EventSource连接成功', eventSourceUrl);
};
```

3.eventSource接收到服务器端的响应方法

```js
eventSource.onmessage = function (event) {
    try {
        if (event.data && typeof event.data === 'string') {
            let data = JSON.parse(JSON.parse(event.data));
            /*
            *业务逻辑代码块
            */
        }
    } catch (error) {
        console.log('EventSource结束消息异常', error);
    }
};
```

4.eventSource关闭的方法

```js
eventSource.close()
```

5.eventSource关闭的回调

```js
eventSource.onclose = function () {
    console.log('EventSource连接断开', eventSourceUrl);
};
```

6.eventSource异常的回调

```js
eventSource.onerror = function (error) {
    console.log('EventSource连接异常', error);
};
```

## 封装

针对以上的应用步骤，将其封装成了一个公用的类，使用时只需要实例化这个类，传入eventSourceUrl和对于的消息回调函数便可，大家如果发现有错误之处，还请指正。

```js
class EventSource {
    //构造函数
    constructor(url, onmessage) {
        this.eventSourceUrl = url;
        this.onmessage = onmessage;
        this.eventSource = null;
        this.initEventSource(url);
    }

    //初始化eventSource
    initEventSource(url) {
    
        //兼容判断
        if ('EventSource' in window) {

            let that = this;

            //实例化EventSource
            this.eventSource = new EventSource(that.eventSourceUrl);

            //EventSource打开
            this.eventSource.onopen = function () {
                console.log('EventSource连接成功', that.eventSourceUrl);
            };

            //EventSource接收到新消息
            this.eventSource.onmessage = function (event) {
                try {
                    if (event.data && typeof event.data === 'string') {
                        let data = JSON.parse(JSON.parse(event.data));
                        
                        //业务逻辑回调
                        if (typeof that.onmessage === 'function') {
                            that.onmessage(data);
                        }
                    }
                } catch (error) {
                    console.log('EventSource初始化异常', error);
                }
            };

            //EventSource关闭
            this.eventSource.onclose = function () {
                console.log('EventSource连接断开', that.eventSourceUrl);
            };

            //EventSource错误
            this.eventSource.onerror = function (error) { // 监听错误
                console.log('EventSource连接错误', error);
                this.close();
            }
        }
        else {
            throw new Error('浏览器不支持EventSource对象')
        }
    }

    //关闭eventSource
    close() {
        this.eventSource.close();
        this.eventSourceUrl = '';
        this.eventSource = null;
        this.onmessage = null;
    }
}

export default EventSource
```

## 总结

对于数据实时性要求较高，客户端不需要主动向服务器请求数据的场景下，比如**可视化大屏展示，广告推送，新闻推荐**...,`eventSource【SSE】` 不失为一种合适的解决方案。

## 相关连接

-   [EventSource\_MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/EventSource "https://developer.mozilla.org/zh-CN/docs/Web/API/EventSource")