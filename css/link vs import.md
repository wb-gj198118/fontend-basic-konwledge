# 1.  标签属性

> link：是html定义的标签，不存在样式的兼容性问题 @import：是CSS2.1才被提出来的语法，低版本的IE不兼容

# 2.  加载范畴

> link：可以加载普通的CSS样式以外，还可以加载rss、rel等其他连接的属性 @import：CSS定义的语法，只能加载CSS样式文件

# 3.  样式权重

> link 比 @import 的权重更高，重复引用，link样式会覆盖@import的样式

# 4.  样式修改

> JS可以通过操作DOM，插入、替换link标签改变样式 但是不能操作CSS文件，故不能操作@import样式

# 5.  加载顺序

> link：加载页面的同时可以加载link标签引入的样式，非阻塞 @import：等待加载完页面，再加载@import样式，阻塞