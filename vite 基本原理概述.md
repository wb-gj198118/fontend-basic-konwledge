# 核心原理

当声明一个 script标签类型为 module 时,如
```
<script type="module" src="/src/main.js"></script>
```

当浏览器解析资源时，会往当前域名发起一个GET请求main.js文件
```
// main.js
import { createApp } from 'vue'
import App from './App.vue'
createApp(App).mount('#app')
```

请求到了main.js文件，会检测到内部含有import引入的包，又会import 引用发起HTTP请求获取模块的内容文件，如App.vue、vue文件

Vite其核心原理:
利用浏览器现在已经支持ES6的import碰见import就会发送一个HTTP请求去加载文件，
Vite启动一个 koa 服务器拦截这些请求，
并在后端进行相应的处理将项目中使用的文件通过简单的分解与整合，
然后再以ESM格式返回返回给浏览器。
Vite整个过程中没有对文件进行打包编译，做到了真正的按需加载，所以其运行速度比原始的webpack开发编译速度快出许多！
