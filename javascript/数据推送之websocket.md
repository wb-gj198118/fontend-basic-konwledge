## websoket

在了解了`eventSource`是服务器给浏览器单向推送数据的模式，如果在需要客户端给服务器发送消息时`eventSource`显然不适合，这个时候我们就需要强大的数据推送功能`websocket`。websocket是一种在单个TCP连接上进行**全双工通信的协议**，与`eventSource`最大不同就是浏览器和服务器建立连接后，双方可以**互相发送消息**。

![789F3720-7980-4d72-B55C-CC5E7213B843.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b348efaac8ec4aa9922bac7d3bc9723e~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

-   优点
    1.  WebSocket支持自定义发送的数据类型。
    2.  WebSocket客户端和服务器端可以互相发送消息
-   缺点
    1.  消息连接后如果长时间没有消息通信，连接便会自动断开，需要发送心跳包，并且异常断开后需要重新重新连接。

## websocket 实战应用

1.  创建websocket实例

```js
var websocketUrl=url;
var ws = new WebSocket(websocketUrl);
```

2.  websocket建立后打开的方法

```js
ws.onopen = function () {
    console.log('ws连接成功', websocketUrl);
};
```

3.websocket接收到服务器端的响应方法

```js
ws.onmessage = function (event) {
    try {
        if (event.data && typeof event.data === 'string') {
            let data = JSON.parse(JSON.parse(event.data));
            /*
            *业务逻辑代码块
            */
        }
    } catch (error) {
        console.log('ws消息异常', error);
    }
};
```

4.websocket发送消息的方法

```js
ws.send(JSON.stringify(messageObj));
```

5.websocket关闭的方法

```js
ws.close()
```

6.websocket关闭的回调

```js
ws.onclose = function () {
    console.log('ws连接断开', websocketUrl);
};
```

7.websocket异常的回调

```js
websocket.onerror = function (error) {
    console.log('ws连接异常', error);
};
```

## 封装

针对以上的应用步骤，将其封装成了一个公用的类。并增加了以下几个功能，使用时只需要实例化这个类，传入websocketUrl和消息回调函数，连接后首次发送的客户端数据便可，大家如果发现有错误之处，还请指正。

-   定时发送心跳包，防止断线
-   异常断开后增加重连机制
-   断线后消息发送失败，将消息暂存在数组中，在重新连接后将之前发送失败的消息二次发送

```js
class initWebSocket {
    //构造函数
    constructor(url, messagesFn, openData) {
        //ws地址
        this.wsUrl = url;
        //ws初始化发送的消息
        this.openData = openData;
        //接收消息的回调
        this.messagesFn = messagesFn;
        //重连开关
        this.reconnectSwitch = true;
        //重连定时器
        this.reconnectTimer = null;
        //心跳定时器
        this.keepAliveTimer = null;
        //心跳消息
        this.keepaliveData = JSON.stringify({ "keepalive": "live" });
        //前次发送失败的消息
        this.tempSendMessage = [];
        //ws对象
        this.websock = null;
        //初始化ws
        this.initWs()
    }

    //初始化ws
    initWs() {
        let that = this;

        this.websock = new WebSocket(that.wsUrl);
        this.websock.onopen = this.onopen.bind(that);
        this.websock.onmessage = this.onmessage.bind(that);
        this.websock.onclose = (e) => { that.reconnectWs() };
        this.websock.onerror = (e) => { that.reconnectWs() };
    }

    //ws打开
    onopen() {
        let that = this;

        //关闭旧的定时器
        that.clearWsInterval();

        //发送初始化消息
        if (that.openData) {
            //that.openData包含客户端唯一信息，以便后续服务推送消息时给哪个客户端推送
            this.send(that.openData);
        }

        //发送前一次发送失败的消息
        if (this.tempSendMessage && that.tempSendMessage.length > 0) {
            let time = setTimeout(() => {
                that.tempSendMessage.forEach(item => {
                    if (typeof item === 'string') {
                        that.send(item);
                    }
                    else {
                        that.send(JSON.stringify(item));
                    }
                })
                that.tempSendMessage = [];
                clearTimeout(time)
            }, 1000)
        }

        //开启保活机制
        this.keepalive();

    }

    //ws接收到消息
    onmessage(message) {
        if (message && message.data) {
            let res = JSON.parse(message.data);
            if (res.data) {
                //调用页面的消息回调方法
                this.messagesFn(res);
            }
        }
    }

    //ws发送消息
    send(agentData) {
        try {
            this.websock.send(agentData)
        } catch (error) {
            this.tempSendMessage.push(agentData);
            this.reconnect();
        }
    }

    //主动关闭
    close() {
        this.reconnectSwitch = false;
        this.websock.close();
    }
    
    // 重连
    reconnectWs() {
        if (this.reconnectSwitch) {
            this.reconnect();
        }
    }

    //保活心跳
    keepalive() {
        let that = this;
        this.keepAliveTimer = setInterval(() => {
            that.websock.send(that.keepaliveData);
        }, 5000)
    }

    //重连机制
    reconnect() {
        let that = this;
        this.clearWsInterval();

        this.reconnectTimer = setInterval(() => {
            that.initWs()
        }, 3000);
    }

    //清除保活和重连的定时器
    clearWsInterval() {
        let that = this;
        this.reconnectTimer && clearInterval(that.reconnectTimer);
        this.keepAliveTimer && clearInterval(that.keepAliveTimer);
    }
}

export default initWebSocket
```

## 待优化

-   重新连接次数限制
-   保活心跳和重连定时器间隔没有作为配置项

## 总结

对于数据实时性要求较高，客户端需要主动向服务器请求数据的，服务器也需要主动给客户端推送数据的场景，比如**系统预警，网页聊天，视频会议，在线教育，...等,**`websocket`相比于`eventSource` 更合适。

## 相关连接

-   [WebSocket\_MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket "https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket")
-   [数据推送解决方案简介：短轮询，长轮询，eventSource，websocket](https://juejin.cn/post/7036311944864727071 "https://juejin.cn/post/7036311944864727071")
-   [eventSource实战应用](https://juejin.cn/post/7036313010142609421 "https://juejin.cn/post/7036313010142609421")