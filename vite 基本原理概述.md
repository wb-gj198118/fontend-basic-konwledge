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


# 优缺点总结:
## 优点：

快速的冷启动: 采用No Bundle和esbuild预构建，速度远快于Webpack
高效的热更新：基于ESM实现，同时利用HTTP头来加速整个页面的重新加载，增加缓存策略
真正的按需加载: 基于浏览器ESM的支持，实现真正的按需加载

## 缺点: 

生态：目前Vite的生态不如Webapck，不过我觉得生态也只是时间上的问题。
生产环境由于esbuild对css和代码分割不友好使用Rollup进行打包

Vite.js虽然才在构建打包场景兴起，但在很多场景下基本都会优于现有的解决方案。如果有生态、想要丰富的loader、plugins的要求可以考虑成熟的Webpack。在其余情况下，Vite.js不失为一个打包构建工具的好选择
