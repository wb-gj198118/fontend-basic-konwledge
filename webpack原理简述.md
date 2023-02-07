
# 核心流程解析
  Webpack 最核心的功能是将各种类型的资源，包括图片、css、js等，转译、组合、拼接、生成 JS 格式的 bundler 文件。官网首页的动画很形象地表达了这一点：
  ![image](https://user-images.githubusercontent.com/29973853/217200822-355826f2-385d-458d-9c7d-75dd4cb13041.png)
  这个过程核心完成了 内容转换 + 资源合并 两种功能，实现上包含三个阶段：

## 初始化阶段：

初始化参数：从配置文件、 配置对象、Shell 参数中读取，与默认配置结合得出最终的参数
创建编译器对象：用上一步得到的参数创建 Compiler 对象
初始化编译环境：包括注入内置插件、注册各种模块工厂、初始化 RuleSet 集合、加载配置的插件等
开始编译：执行 compiler 对象的 run 方法
确定入口：根据配置中的 entry 找出所有的入口文件，调用 compilition.addEntry 将入口文件转换为 dependence 对象

## 构建阶段：

编译模块(make)：根据 entry 对应的 dependence 创建 module 对象，调用 loader 将模块转译为标准 JS 内容，调用 JS 解释器将内容转换为 AST 对象，从中找出该模块依赖的模块，再 递归 本步骤直到所有入口依赖的文件都经过了本步骤的处理
完成模块编译：上一步递归处理所有能触达到的模块后，得到了每个模块被翻译后的内容以及它们之间的 依赖关系图

## 生成阶段：

输出资源(seal)：根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 Chunk，再把每个 Chunk 转换成一个单独的文件加入到输出列表，这步是可以修改输出内容的最后机会
写入文件系统(emitAssets)：在确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统

单次构建过程自上而下按顺序执行，在此之前，对上述提及的各类技术名词不太熟悉的同学，可以先看看简介：

Entry：编译入口，webpack 编译的起点
Compiler：编译管理器，webpack 启动后会创建 compiler 对象，该对象一直存活知道结束退出
Compilation：单次编辑过程的管理器，比如 watch = true 时，运行过程中只有一个 compiler 但每次文件变更触发重新编译时，都会创建一个新的 compilation 对象
Dependence：依赖对象，webpack 基于该类型记录模块间依赖关系
Module：webpack 内部所有资源都会以“module”对象形式存在，所有关于资源的操作、转译、合并都是以 “module” 为基本单位进行的
Chunk：编译完成准备输出时，webpack 会将 module 按特定的规则组织成一个一个的 chunk，这些 chunk 某种程度上跟最终输出一一对应
Loader：资源内容转换器，其实就是实现从内容 A 转换 B 的转换器
Plugin：webpack构建过程中，会在特定的时机广播对应的事件，插件监听这些事件，在特定时间点介入编译过程

# 一句话简介构建过程就是：
1. webpack在启动后自动从配置文件中的entry开始
2. 再从配置文件中module开始递归解析Entry依赖的所有module，每找到一个module，就会根据配置的loader去找出对应的转换规则，对module进行转换后，再解析出当前Module依赖的Module
3. 再将这些模块以entry为单位进行分组，一个entry和其依赖的的Module，被分到一个组里也就是一个chunk
4. 最后webpack会把所有的chunk转换为文件输出，在整个事件流中，webpack会在适当的时机里执行plugin里面的逻辑
