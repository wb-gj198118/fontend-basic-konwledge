## 前言

WebSocket 是 Web 应用程序的传输协议，它提供了双向的，按序到达的数据流。

他是一个 Html5 协议，WebSocket 的连接是持久的，他通过在客户端和服务器之间保持双工连接，服务器的更新可以被及时推送给客户端，而不需要客户端以一定时间间隔去轮询。

1.  建立在 TCP 协议之上，服务器端的实现比较容易。
2.  与 HTTP 协议有着良好的兼容性。默认端口也是80和443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。
3.  数据格式比较轻量，性能开销小，通信高效。
4.  可以发送文本，也可以发送二进制数据。
5.  没有同源限制，客户端可以与任意服务器通信。
6.  协议标识符是ws（如果加密，则为wss），服务器网址就是 URL。

## Api 的简单实例

WebSocket 是 Html5 所提供的 Api，因此只是简单的使用的话是非常简单的

#### 前端

```kotlin
new WebSocket('ws://localhost:3000/ws/chat');
this.ws.onmessage = this.onMessage;
this.ws.onerror = this.onError;
this.ws.onclose = this.onClose;
this.ws.onopen = () => this.send({type: 'text', meg: 'Hello 大家好~'});
this.ws.close()
```

前端大致是这样的

#### 后端

index

```javascript
// koa
import Koa from 'koa';
import logger from 'koa-logger';
import bodyParser from 'koa-bodyparser';
import cors from 'koa2-cors';
import ws from 'ws';
import webSocket from './webSocket';
import corsConfig from './corsConfig';
import routes from './router';

const WebSocketServer = ws.Server;
const app = new Koa();
const isProduction = process.env.NODE_ENV !== 'development';

// 日志
!isProduction ? app.use(logger()) : '';

// CORS 跨域配置
app.use(cors(corsConfig));

// koa-bodyparser
app.use(bodyParser());

// add router middleware:
app.use(routes());

// 在端口3000监听:
const server = app.listen(3000);
console.log('koa 服务启动成功!');
app.wss = webSocket(server, WebSocketServer);
console.log('app http 服务启动成功! port: 3000...');
```

webSocket

```javascript
// webSocket.js
import url from 'url';

var messageIndex = 0;

// 广播当前 webSocket 连接用户
function broadcastUsers(wss, user) {
  ...
}

// 从 cookie 中获取 user
function cookiesUser(req) {
  ...
}

// 创建消息
function createMessage(type, user, data) {
  ...
}

// 连接后回调
function onConnect() {
  ...
}

// 监听客户端消息
function onMessage(message) {
  ...
}

// 客户端关闭 webSocket
function onClose() {
  ...
}

// webSocket 连接异常
function onError(err) {
  console.log('[WebSocket] error: ' + err);
};

// 创建 webSocket 服务器
function createWebSocketServer(server, WebSocketServer) {
  //创建WebSocketServer:
  const wss = new WebSocketServer({
    server: server,
  });
  // 聊天功能：在接收到信息后，将消息广播发送到 webSocket 所有绑定的 client
  wss.broadcast = function broadcast(data) {
    wss.clients.forEach(function each(client) {
      console.log(client.readyState);
      if (client.readyState === 1) {
        client.send(data);
      }
    });
  };
  wss.on('connection', function(client, req) {
    let location = url.parse(req.url, true);
    client.on('message', onMessage);
    client.on('close', onClose);
    client.on('error', onError);
    // 如果接收到 webSocket 请求，但不是 chat 聊天模块的，关闭改请求
    if (location.pathname !== '/ws/chat') {
      client.close(4000, 'Invalid URL');
    }
    client.user = cookiesUser(req);
    client.wss = wss;
    // 连接成功后的 自定义回调
    onConnect.apply(client);
  });
  console.log('webSocket 服务器启动成功!');
  return wss;
}

export default createWebSocketServer;
```

后端的实现这里采用了一个 koa 作为服务器进行案例，但本文的重点不在于后端对 webSocket 这一块的实现。

如果读者对这一块感兴趣的话，可以去观摩鄙人的简单案例实现：[Github](https://github.com/DesBisous/vue-ts "https://github.com/DesBisous/vue-ts")

## WebSocket 封装

对于 WebSocket 开发中，可以按照自行的需求进行处理封装，下面就来对这块做一个简单的封装，由于本人参考项目中使用的 TypeScript 进行开发，所以下面展示的将都是 TS 编写的

**interfaces/ws.ts**

```TypeScript
export interface WSResult {
	code: number;
	message: string;
	errorCode?: number | string;
	httpMsg?: string;
	hide?: boolean;
	[x: string]: any;
}

export interface WebSocketMegFunc {
	(res: WSResult): void;
}

export interface WebSocketFunc {
	(ws: WebSocketExp): void;
}

export interface WebSocketExp extends WebSocket {

}
```

**webSocket.ts**

```TypeScript
/**
 * 收到服务器数据后的回调函数
 */
function onMessage(
	this: WebSocketExp,
	ev: MessageEvent,
	onMessageCallback: WebSocketMegFunc
): void {
	// 这里可以做一些通用措施，然后再传给 onMessageCallback
	if (onMessageCallback) {
		try {
			const res = JSON.parse(ev.data).data;
			onMessageCallback(res);
		} catch (error) {
			handleThrottleMsg(`[WS JSON ERROR](${this.url})`);
			onMessageCallback(null);
		}
	}
}

/**
 * 报错时的回调函数
 */
function onError(this: WebSocketExp, ev: Event): void {
	console.error(`[WS ERROR](${this.url}) 异常`);
	this.close();
}

/**
 * 连接关闭后的回调函数
 */
function onClose(this: WebSocketExp, ev: CloseEvent): void {
	console.warn(`[WS CLOSED](${this.url}) ${ev.code}: ${ev.reason}`);
}

/**
 * 连接成功后的回调函数
 */
function onOpen(this: WebSocketExp, ev: Event): void {
	console.log(`ws: ${this.url} connection succeeded ~`);
}

export default function wsConstructor(url: string, onMessageCallback: WebSocketMegFunc): WebSocket {
	const ws: WebSocketExp = new WebSocket(url);
	ws.onmessage = function (this: WebSocket, ev: MessageEvent): void {
		onMessage.call(this, ev, onMessageCallback);
	};
	ws.onerror = onError;
	ws.onclose = onClose;
	ws.onopen = onOpen;
	return ws;
}
```

上述的 websocket.js 文件就是对 WebSocket 的简单分装，在使用上可以如下使用：

**api/ws.ts**

```javascript
import wsConstructor from '~/lib/webSocket';
import { WebSocketMegFunc } from '~/interfaces/ws';
import { getWsUrl } from '~/data/ws'; // 该方法仅仅是对 url 拼装一下

const ws = {
	getCountry: (onMessageCallback: WebSocketMegFunc): WebSocket =>
	wsConstructor(getWsUrl('/ws/get_country/'), onMessageCallback),
};

export default ws;
```

**views/index.tsx**

```javascript
import { ws } from '~/api';
import { WSResult } from '~/interfaces/ws';

const onCMapWsMessage = (res: WSResult): void => {
    // 各种处理
    ...
};

ws.getCountry(onCMapWsMessage);
```

## 心跳机制

想必看到这里，一般的前端可能就实现了业务需求了，但往往很多需求还能够往下深挖，或者说能做得更好，下面所展示是对于 WebSocket 的一个心跳机制，如果能够知道这一个优化项，其实我认为比一般的前端优秀了。

#### 心跳机制是什么？为什么需要？

心跳机制其实只要看词就能大概了解，就是类似一个轮询的机制，必要时向对方询问情况的一种操作。

Websocket 是前后端交互的长连接，前后端也都可能因为一些情况导致连接失效并且相互之间没有了反应。因此为了保证连接的可持续性和稳定性，Websocket 心跳机制就应运而生。

在使用原生 Websocket 的时候，如果设备网络断开，不会立刻触发 Websocket 的任何事件，前端也就无法得知当前连接是否已经断开。这个时候如果调用 `Websocket.send` 方法，浏览器才会发现链接断开了，便会立刻或者一定时间后（不同浏览器或者浏览器版本可能表现不同）触发 onclose 函数。

后端 Websocket 服务也可能出现异常，造成连接断开，这时前端也并没有收到断开通知，因此需要前端定时发送心跳消息 ping，后端收到 ping 类型的消息，立马返回消息，告知前端连接正常。如果一定时间没收到消息，就说明连接不正常，前端便会执行重连。

为了解决以上两个问题，以前端作为主动方，定时发送 ping 消息，用于检测网络和前后端连接问题。一旦发现异常，前端持续执行重连逻辑，直到重连成功。

接下来对上面分装过的代码片段添加心跳机制

#### 心跳实现

对于上面封装后的 WebSocket 构造工厂，是每次调用 `wsConstructor` 之后都会返回当前的 ws 对象给当前调用的页面保存，并且心跳机制的代码必须也需要放到封装的 webSocket.ts 中，供所有调用 `wsConstructor` 提供服务，那由此会产生一个问题：

当一个页面有多个 ws 长连接的存在，或者说后台和不同页面也存在着多个 ws 长连接的存在，那当某个长连接中断了，或者所有长连接中断了，如果做到针对相应的 ws 长连接进行心跳重连，并且恢复上一个 ws 长连接的**使用者不会拿错别的 ws 对象进行响应操作呢？**

可能这里会有人想到，那肯定需要一个**唯一标志**，告诉使用者，我响应的是 aWs 长连接，他响应的是 bWs 长连接，而它的 cWs 长连接断开了，进行了重连新生成了 cWs 对象并告知它替换上一个旧的已断开的长连接对象来使用。没错，肯定需要一个唯一标示，就像我们的身份证，**那接下来还有一个问题，使用者拿着手上的长连接身份证去找相对应的 ws 对象，那要去哪里找呢？**

就像人们拿着身份证和别人说，我是谁谁谁，别人一看，哦，知道你是谁，这个别人依据的是什么？第一当然是你手上的身份证，第二那就是国家电脑资料库中有你身份证号的资料，这里我采用 Vue3 对待 proxy 数据和源数据的响应式封装后的一个保存方法，那就是使用了一个大 Map 集合进行存储，使用者可凭借 ID 去集合中才找响应的 ws 对象进行操作，并会按照必要更新 Map 资料库的资料。

说到这里，肯定心中莫名还是感到哪里不对经，没错，**还有一个问题，那就是当某个 ws 长连接迟迟没有响应，并且触发了心跳确认了是连接已断开需要重连的时候，当前的代码如何判断我应该重连的地址是谁？**，可能有人就会说，那个谁谁谁 ws 连接断了，需要重连那它(ws)当然知道接下来要对那个服务器 Api 发起重连了，不就是自己之前连过的 xxx/xxx 么，没错理是这么个理，但编写者不告诉程序从哪里获取相关信息，它是不知道的，不要使用人类的理所当然去充当代码的大脑，编写代码需要使用人类的思维 + 计算机的思维并驾齐驱才能更好的编写逻辑，**为了解决此问题，每次 WebSockek 构造工厂创建一个 ws 对象的时候，都会对当前使用者传递过来的一些信息进行记录和保存，做法就是给 ws 额外添加属性。**

**interfaces/ws.ts**

```diff
export interface WSResult {
	code: number;
	message: string;
	errorCode?: number | string;
	httpMsg?: string;
	hide?: boolean;
	[x: string]: any;
}

export interface WebSocketMegFunc {
	(res: WSResult): void;
}

export interface WebSocketFunc {
	(ws: WebSocketExp): void;
}

export interface WebSocketExp extends WebSocket {
+   onMessageCallback?: WebSocketMegFunc; // 保存使用者的回调方法
+	wsKey?: string; // 记录当前使用者基于 ws 的 Id
+	timeout?: any; // 记录当前 ws 心跳定时器
+	serverTimeout?: any; // 记录当前 ws 心跳发出等待后端响应的等待定时器
+	debounceTimeout?: any; // 记录重连发出的定时器，用于重连机制的一个节流操作
}
```

**webSocket.ts**

```diff
+ import { WebSocketMegFunc, WebSocketExp } from '~/interfaces/ws';

+ const timeout = 1000 * 60 * 4; // 4分钟，后端推送间隔为两分钟
+ const reconnectTimeout = 1000 * 60 * 2; // 尝试重连间隔为两分钟
+ export const wsMap = new Map(); // ws 对象的集合库

/**
 * 初始化 Props
 */
+ function initProps(ws: WebSocketExp, onMessageCallback: WebSocketMegFunc, wsKey: string): void {
+	 ws.onMessageCallback = onMessageCallback; // 记录使用者的回调函数
+	 ws.wsKey = wsKey; // 新 ws 集成旧 ws 的 Id
+	 ws.timeout = null; // 声明心跳定时器
+	 ws.serverTimeout = null; // 声明心跳反馈定时器
+	 // 这里保存上一个 ws 的重连状态，配合 reconnect 的节流功能，保存不会频繁发送无意义的心跳
+	 ws.debounceTimeout = wsMap.get(wsKey) ? wsMap.get(wsKey).debounceTimeout : null;
+	 wsMap.set(wsKey, ws); // 保存或者更新 ws 在资料库 map 的数据
+ }

/**
 * 清除定时器
 */
+ function clearTimeout(ws: WebSocketExp): void {
+ 	 ws.timeout && window.clearTimeout(ws.timeout);
+	 ws.serverTimeout && window.clearTimeout(ws.serverTimeout);
+	 ws.debounceTimeout && window.clearTimeout(ws.debounceTimeout);
+ }

/**
 * 重置心跳检查
 */
+ function reset(ws: WebSocketExp): void {
+ 	 clearTimeout(ws);
+ 	 start(ws);
+ }

/**
* 开始心跳检查
*/
+ function start(ws: WebSocketExp): void {
+	 ws.timeout = setTimeout(function () {
+		 ws.send(JSON.stringify({ type: 1000, data: 'HeartBeat' })); // 和后端约定，心跳发送标识为: HeartBeat
+		 ws.serverTimeout = setTimeout(function () {
+			 // 如何发起了心跳检查，timeout 时间后还是没有返回，那就直接关闭
+			 ws.close();
+		 }, timeout);
+	 }, timeout);
+ }

/**
 * 重连操作
 */
+ function reconnect(ws: WebSocketExp): void {
+	 // 节流
+	 clearTimeout(ws); // 如果发起重连，则关闭心跳等定时器
+	 const callNow = !ws.debounceTimeout;
+	 ws.debounceTimeout = setTimeout(() => {
+		 ws.debounceTimeout = null;
+		 reconnect(ws); // 已进行过重连的，下一次必须经过 reconnectTimeout 之后才能再次发起重连
+	 }, reconnectTimeout);
+	 if (callNow) {
+		 console.warn(`[WS RECONNECT](${ws.url})`);
+		 wsConstructor(ws.url, ws.onMessageCallback, ws.wsKey);
+	 }
+ }

/**
 * 收到服务器数据后的回调函数
 */
function onMessage(
	this: WebSocketExp,
	ev: MessageEvent,
	onMessageCallback: WebSocketMegFunc
): void {
+   // 重置心跳
+	reset(this);
	// 这里可以做一些通用措施，然后再传给 onMessageCallback
	if (onMessageCallback) {
		try {
			const res = JSON.parse(ev.data).data;
+			if (res.data.text === 'alive') return; // 与后端约定，心跳反馈标识为: alive，得到 alive 标识链接正常，什么也不做
			onMessageCallback(res);
		} catch (error) {
			console.error(`[WS JSON ERROR](${this.url})`);
			onMessageCallback(null);
		}
	}
}

/**
 * 报错时的回调函数
 */
function onError(this: WebSocketExp, ev: Event): void {
	console.error(`[WS ERROR](${this.url}) 异常`);
	this.close();
}

/**
 * 连接关闭后的回调函数
 */
function onClose(this: WebSocketExp, ev: CloseEvent): void {
	console.warn(`[WS CLOSED](${this.url}) ${ev.code}: ${ev.reason}`);
+	// 前端手动关闭的就不用重连了
+	if (ev.code !== 1000) {
+		reconnect(this);
+	} else {
+		// web主动关闭就不再重连了
+		// 清除所有定时器
+		clearTimeout(this);
+		// 删除了 wsMap 对应存储的 ws 实例
+		wsMap.delete(this.wsKey);
+	}
}

/**
 * 连接成功后的回调函数
 */
function onOpen(this: WebSocketExp, ev: Event): void {
	console.log(`ws: ${this.url} connection succeeded ~`);
+	// 开启心跳
+	start(this);
}

export default function wsConstructor(
    url: string, 
    onMessageCallback: WebSocketMegFunc,
    wsKey: string // 这是使用者对相应的 ws 给予身份证 Id
): WebSocket {
	const ws: WebSocketExp = new WebSocket(url);
	ws.onmessage = function (this: WebSocket, ev: MessageEvent): void {
		onMessage.call(this, ev, onMessageCallback);
	};
	ws.onerror = onError;
	ws.onclose = onClose;
	ws.onopen = onOpen;
+	// 初始化 Props，记录使用者构建 ws 所提供的信息保存在 ws 对象自身中
+	initProps(ws, onMessageCallback, wsKey);
	return ws;
}
```

使用上的变更：

**api/ws.ts**

```diff
import wsConstructor from '~/lib/webSocket';
import { WebSocketMegFunc } from '~/interfaces/ws';
import { getWsUrl } from '~/data/ws'; // 该方法仅仅是对 url 拼装一下

const ws = {
	getCountry: (
	    onMessageCallback: WebSocketMegFunc,
+	    wsKey: string
	): WebSocket =>
		wsConstructor(
		    getWsUrl('/ws/get_country/'), 
		    onMessageCallback,
+		    wsKey: string
		),
};

export default ws;
```

**views/index.tsx**

```diff
import { ws } from '~/api';
import { WSResult } from '~/interfaces/ws';

+ // ws
+ const cMapWs = 'cMapWs';
+ const cDetailsWs = 'cDetailsWs';

+ useEffect(() => {
+	 return (): void => {
+        // 离开页面关闭各自的 ws 长连接
+		 wsMap.get(cMapWs) && wsMap.get(cMapWs).close();
+		 wsMap.get(cDetailsWs) && wsMap.get(cDetailsWs).close();
+	 };
+ }, []);

const onCMapWsMessage = (res: WSResult): void => {
    // 各种处理
    ...
};

const onCDetailsWsMessage = (res: WSResult): void => {
    // 各种处理
    ...
};

ws.getCountry(
    onCMapWsMessage,
+   cMapWs
);

+ // 比如有多个
ws.getCountryDetails(
    onCDetailsWsMessage, 
+   cDetailsWs
);
```

## 总结

到此就完成了 WebSocket 的封装和心跳机制，相信只要能够理解上述代码中其中的含义和逻辑，各开发者结合自身的业务需求，就可以封装符合自己的 WebSocket 了。

对于 WebSocket 的使用，其实大多数的开发者，一开始可能都会嫌麻烦或者各种外界原因，直接采用了轮询的机制来处理，当然这种方式也是可以的，所有的方法都是按照实际场景出发，但条件允许的情况下，使用 WebSocket 岂不是更优雅一些？

另一方面，使用 WebSocket 一般来说，过一下 MDN 官方文档，可能也就可以实现了，但要知道的是，出去面试就会发现，面试官问道 WebSocket 的时候，难道他会问你怎么用吗？更多的是面试官会问：“WebSocket 由于各种原因和后端失去的连接没有了反应，如何处理？” 如果面试者能够知道心跳，并且讲出这个解决方案，那是否能成为亮点呢？

所有的功能其实都能够进行深挖和优化，变成一个具备亮点的功能，针对增删改查也不例外。