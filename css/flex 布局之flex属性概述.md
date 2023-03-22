## flex属性介绍

平时在项目中Flex布局的使用频率非常高，但是对于flex的几个值的具体用法和使用场景有时候不是很清楚，所以写此文来总结下。  
首先，`flex`属性其实是一种简写，是`flex-grow`，`flex-shrink`和`flex-basis`的缩写形式。 默认值为`0 1 auto`。后两个属性可选。 所以弄懂三个属性各自的含义以及作用，flex简写的含义，之后在开发中就可以随心所欲的使用flex了。

### flex-grow

`flex-grow`属性定义项目的放大比例，默认为`0`，即如果存在剩余空间，也不放大。  
如果所有项目的flex-grow属性都为1，则它们将等分剩余空间（如果有的话）。  
如果一个项目的flex-grow属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/60b71e5904584fc9b542a94a85318783~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

### flex-shrink

flex-shrink属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。  
如果所有项目的flex-shrink属性都为1，当空间不足时，都将等比例缩小。  
如果一个项目的flex-shrink属性为0，其他项目都为1，则空间不足时，前者不缩小。

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7da3ded653c94d87b1420ef56941c8e4~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

### flex-basis

flex-basis属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。  
浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。  
它可以设为跟width或height属性一样的值（比如350px），则项目将占据固定空间。

## flex缩写的等值

了解了三个属性各自的含义之后，可以看下三个属性对应的等值。

| 语法 | 等值 |
| :-: | :-: |
| flex: initial | flex: 0 1 auto |
| flex: 0 | flex: 0 1 0% |
| flex: none | flex: 0 0 auto |
| flex: 1 | flex: 1 1 0% |
| flex: auto | flex: 1 1 auto |

### flex: initial

`flex:initial`等同于设置`flex: 0 1 auto`，是flex属性的默认值。

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8c9aad6f95c94781b15f228bc01e4304~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

举例，外容器是红色，内里元素蓝色边框，比较少，会有下图效果，剩余空间仍有保留。剩余空间有，但是因为flex-grow属性是0，所以没有填补空白。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/475f1211e57e47b4b60c8f3547f50cd4~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

代码如下:

```html
<div class="container">
        <div class="item">嘿嘿</div>
        <div class="item">哈哈</div>
</div>
<style>
    .container {
        width: 200px;
        display: flex;
        border: 2px dashed crimson;
    }

    .container .item {
        border: 2px solid blue;
    }
</style>
```

如果子项内容很多，由于`flex-shrink:1`，因此，会缩小，表现效果就是文字换行，效果如下图所示。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/20e4fe10824741689c09660a88b71770~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

### 适用场景

`'initial'`表示CSS属性的初始值，通常用来还原已经设置的CSS属性。因此日常开发不会专门设置`flex:initial`声明，一般父节点设置`display：flex`，子节点不设置属性，则css默认表现为`flex:initial`。  
`flex:initial`声明适用于下图所示的布局效果。

![适合flex:initial声明的布局轮廓图示意](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6aa23f4247fd4829a61d4e962977322f~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

上图所示的布局效果常见于按钮、标题、小图标等小部件的排版布局，因为这些小部件的宽度都不会很宽，水平位置的控制多使用`justify-content`和`margin-left:auto/margin-right:auto`实现。

除了上图所示的布局效果外，`flex:initial`声明还适用于一侧内容宽度固定，另外一侧内容宽度任意的两栏自适应布局场景，布局轮廓如图下图所示（点点点表示文本内容）。

![适合flex:initial声明的两栏自适应布局轮廓图示意](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0bd0455a368b448f935ebd63060008a0~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

此时，无需任何其他Flex布局相关的CSS设置，只需要容器元素设置`display:flex`即可。

又或者只是想要父节点下的所有子节点全部都并排排列，不去使用`float`属性。

如下图:

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d2843b4b7ae74987bedf4404b26cafad~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

总结下就是那些希望元素尺寸收缩，同时元素内容万一较多又能自动换行的场景可以不做任何`flex`属性设置。

### flex:0和flex:none

这里介绍一下区别和使用场景。  
`flex:0`等同于设置`flex: 0 1 0%`。  
`flex:none`等同于设置`flex: 0 0 auto`。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/21ffd1cc0cf34ada8084073ffa44fb18~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

`flex:0`时代码如下

```html
<div class="container">
        <div class="item">嘿嘿</div>
        <div class="item">哈哈</div>
</div>
<style>
    .container {
        width: 200px;
        display: flex;
        border: 2px dashed crimson;
    }

    .container .item {
        border: 2px solid blue;
        flex: 0;
    }
</style>
```

`flex:none`只是把css中的`flex`属性设置为`none`，不再展示代码。  
效果如下：

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d9c917a907974908836a9d6011e64fce~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/611ed3853cb34e0fbe1fc344fb3d06e6~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

对比看来可以看到flex-0时候会表现为最小内容宽度，会将高度撑高（当前没有设置高度，如果设置高度文字会超过设置的高度，如下图）flex-none时候会表现为最大内容宽度，字数过多时候会超过容器宽度。

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a3e7dc1c30274b1c83cc639f17f144ef~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

### 适用场景 flex-0

由于应用了`flex:0`的元素表现为最小内容宽度，因此，适合使用`flex:0`的场景并不多。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f33fb01a9d284f11a3a9f35b61be34a2~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

其中上图左侧部分的矩形表示一个图像，图像下方会有文字内容不定的描述信息，此时，左侧内容就适合设置`flex:0`，这样，无论文字的内容如何设置，左侧内容的宽度都是图像的宽度。

### 适用场景 flex-none

`flex-none`比`flex-0`的适用场景多，如内容文字固定不换行，宽度为内容宽度就适用该属性。  
没设置flex-none代码如下：

```html
 <div class="aa">
        <img src="a.png">
        <p>右侧按钮没有设置flex-none</p>
        <button>按钮</button>
    </div>
<style>
    .aa {
            width: 300px;
            border: 1px solid #000;
            display: flex;
        }

    .aa img {
            width: 100px;
            height: 100px;
        }
        
    .aa buttton {
            height: 50px;
            align-self: center;
        }
</style>
```

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c9b4789e40d44ce985391c5324b026a3~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

设置了flex-none效果如下：

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cadd8f71334f4aa28dfae3d3e141790f~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

### flex:1和flex:auto

`flex:1`等同于设置`flex: 1 1 0%`。  
`flex:auto`等同于设置`flex: 1 1 auto`。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b038e375229440f79e4e4c74e6629f1f~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

通过代码表现看看两者区别：

`flex:1`时代码如下

```html
<div class="container">
       <div class="item">嘿嘿嘿嘿嘿嘿嘿嘿嘿嘿嘿嘿嘿嘿嘿嘿嘿</div>
       <div class="item">哈哈</div>
       <div class="item">呵呵</div>
</div>
<style>
     .container {
            width: 200px;
            display: flex;
            border: 2px dashed crimson;
     }

    .container .item {
        border: 2px solid blue;
        flex:1;
    }
</style>
```

表现为：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b44d0baaaa64474e90249f746c58fccb~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

`flex-auto`时表现为：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/22e1b98c51094452ac326eb7a8cc8296~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

上面两个图体现了`flex:1`和`flex:auto`的区别，虽然都是充分分配容器的尺寸，但是`flex:1`的尺寸表现更为内敛（优先牺牲自己的尺寸），`flex:auto`的尺寸表现则更为霸道（优先扩展自己的尺寸）。

##### 适合使用flex:1的场景

当希望元素充分利用剩余空间，同时不会侵占其他元素应有的宽度的时候，适合使用`flex:1`，这样的场景在Flex布局中非常的多。

例如所有的等分列表，或者等比例列表都适合使用`flex:1`或者其他flex数值，适合的布局效果轮廓如下图所示。

![flex:1适合用在固定比例的列表中](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b0c9819a154340cf8f3667027b5d4c49~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

##### 适合使用flex:auto的场景

当希望元素充分利用剩余空间，但是各自的尺寸按照各自内容进行分配的时候，适合使用`flex:auto`。例如导航栏。整体设置为200px，内部设置flex:auto,会自动按照内容比例进行分配宽度。

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/26b2f715aa754522908d88ebbea932c6~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

## 总结

最后总结一下：

-   `flex:initial`表示默认的flex状态，无需设置，适合小控件元素的分布布局，或者某一项内容动态变化的布局；
-   `flex:0`适用场景较少，适合设置在替换元素的父元素上；
-   `flex:none`适用于不换行的内容固定或者较少的小控件元素上，如按钮。
-   `flex:1`适合等分布局；
-   `flex:auto`适合基于内容动态适配的布局；