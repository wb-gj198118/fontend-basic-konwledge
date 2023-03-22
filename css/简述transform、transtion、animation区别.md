# 易混淆的几个概念

| 属性 | 含义 |
| --- | --- |
| animation 动画 | 搭配@keyframes实现动画效果 |
| transition 过渡 | 用于css属性的过渡 |
| transform 转换 | 主要用于平移，缩放，倾斜，旋转等效果 |
| translate 平移 | translate只是trasform的一个属性值 |

# transform 转换

## 基本使用

transform有以下属性值：

-   translate()
-   rotate()
-   skew()
-   scale()

除此之外，我们还可以使用transform-origin属性，指定转换的中心点。

-   transform-origin: 50% 50% //默认值，可以是百分比
-   transform-origin: 100px 100px //具体值
-   transform-origin: left top //关键字

我们来实际体验一下：

```css
//html
<div class="wrapper">
    <div class="box"></div>
</div>

//css
.wrapper {
    width: 300px;
    height: 300px;
    border: 1px solid red;
}
.box {
    width: 100px;
    height: 100px;
    background: green;
}
```

效果如下：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fcf83bcf46834be99963df2593ad4fc7~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

#### 1\. translate() 平移

我们想要 .box 向下和向右分别移动100px，如何实现？

```css
.box {
    transform: translate(100px, 100px)
}
```

效果如下：

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1924bbe9d3a24611b1bbed6f7cfa4df2~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

#### 2\. rotate() 旋转

我们想要 .box 顺时针旋转 60度，如何实现呢？

```css
.box {
    tansform: rotate(60deg)
}
```

效果如下：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/48d8b64ae70743b7af244fe620f308dd~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

#### 3\. skew() 倾斜拉伸

包含两个参数值，分别表示X轴和Y轴倾斜的角度，如果第二个参数为空，则默认为0，参数为负表示向相反方向倾斜。

```css
.box {
    transform: skew(30deg);
}

```

效果如下：

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2fa7e4ceefa64012af8f4bbd9a5593e8~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

#### 4\. scale() 缩放

我们想要 .box 放大一倍，如何实现？

```css
.box {
    transform: scale(2)
}
```

我们发现变成了如下效果：

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e1740ed7ea8749bfa0b464342981a4cf~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

我们发现这个时候 .box 超出红框了，为什么呢？其实是transform-origin默认为50% 50% 导致的，也就是说默认情况下，是以中心点向四周放大，所以自然而然就超出了，如果我们指定以左上角为中心放大，不就实现我们想要的效果啦。

代码如下：

```css
.box {
    transform: scale(2)
    transform-origin: left top;
}
```

效果如下： ![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4ce0398b7a0f47f496b9f6d48e13a2df~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

相信通过上面的介绍，我们已经知道了transform的基本使用

## transform的副作用

#### 1\. z-index

即使用transform样式的元素可以使用z-index，从而可以覆盖普通流元素和低级别的定位元素。

例如：我们有两个盒子：

```css
//html
<div class="box1"></div>
<div class="box2"></div>

// css
.box1 {
    width: 200px;
    height: 100px;
    background: green;
}
.box2 {
    width: 300px;
    height: 100px;
    background: red;
}
```

效果如下：

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3bb1d041277b467fbc239f103814f5ba~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

然后我们设置box2的margin-top: -50px;

```css
.box2 {
    margin-top: -50px;
}
```

按照正常流，如果有重叠部分，后面的元素会覆盖前面的元素，

效果如下：

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/81ec68671cf943768a8c704c23845478~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

这时，如何让box1位于box2的上面呢？这时我们可能会想到定位，因为定位可以设置z-index，没错，定位肯定可以实现，这里我们用另外一种方法：transform

```css
.box1 {
    transform: scale(1)
}
```

效果如下：

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ee9b6cc2780441529ff319f36eb6b34b~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

相信，通过上面这个例子，我们深刻体会到，transform可以使元素使用z-index。

#### 2\. fixed

固定定位fixed使元素相对于视窗进行定位，不随着页面滚动条的滚动而滚动。但是，如果在固定定位元素的父级设置transform不为none，则会将固定定位降级为绝对定位absolute

```css
//html
<div class="wrapper">
    <div class="box"></div>
</div>

//css
.box {
    position: fixed;
    width: 100px;
    height: 100px;
    background: red;
}
```

上面由于.box元素设置了fixed，所以页面滚动的时候，.box元素并不会动，但是我们如果把它的父元素.wrapper添加transform之后呢？

```css
.wrapper {
    transform: scale(1)
}
```

这个时候，我们就发现，页面滚动的时候，.box也会滚动，原因就是它的父元素使用了transform属性，导致.box的position降级为absolute.

#### 3\. overflow

默认情况下，如果子元素position是absolute，父元素的position是static（即默认没有定位）时，父元素设置overflow:hidden是无效的，这时，如果我们给父元素添加transform属性之后，overflow:hidden就可以生效。

我们来看一个例子：

```css
//html
<div class="wrapper">
    <div class="box"></div>
</div>

//css
.wrapper {
    border: 1px solid #aaa;
    height: 50px;
    width: 200px;
    overflow: hidden;
}
.box {
    position: absolute;
    width: 100px;
    height: 100px;
    background: green;
}
```

效果如下： ![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/379516b99103478db7d60bfc2a43c3a1~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

然后，我们给父元素添加transforms属性：

```css
.wrapper {
    transform: scale(1)
}
```

效果如下：

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2786fdaf705f4fb9961af81f06c1cf2c~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

## transform两大面试题

#### 1\. transform 是否会脱离文档流？

transform不会脱离文档流，原位置保持不变，只是对节点本身进行旋转，拉伸等变形或者位置移动。

#### 2\. transform 是否会引起重排或者重绘制？

transform 不会引起重绘和重排，原因是采用了GPU硬件加速。

具体可以参考：

[blog.csdn.net/weixin\_3392…](https://blog.csdn.net/weixin_33922670/article/details/89108346 "https://blog.csdn.net/weixin_33922670/article/details/89108346") [www.cnblogs.com/Kuro-P/p/86…](https://www.cnblogs.com/Kuro-P/p/8676771.html "https://www.cnblogs.com/Kuro-P/p/8676771.html")

# transition 过渡

## 基本使用

transition 是一个复合属性，包括：

-   transition-property: 过渡属性
-   transition-duration：过渡时间
-   transition-timing-function: 过渡函数（默认为ease）
-   transition-delay：过渡延迟时间

transition 复合书写的时候，顺序即上面的顺序：

```css
transition: width 2s ease 5s; //即5s后执行width属性的过渡动画，动画执行时间2s
```

我们来看一个例子：

```css
//html
<div></div>

//css
div {
	width:100px;
	height:100px;
	background:red;
	transition:width 2s;
	-webkit-transition:width 2s; /* Safari */
}
```

点击查看：[动画效果](https://www.runoob.com/try/try.php?filename=trycss3_transition "https://www.runoob.com/try/try.php?filename=trycss3_transition")

## 应用场景

transition 通常和 hover 等事件配合使用，由某一个事件触发。

我们来看一个例子：hover的时候宽度边大

```css
div {
    width: 200px;
}
div:hover {
    width: 400px;
}
```

上面是最简单的写法，如果我们想在宽度变化的过程中加一个动画，这时就可以使用transition

```css
div {
    width: 200px;
    transition: width 1s; 
}
div:hover {
    width: 400px;
}
```

transition通常也可以和transform搭配使用，表示为transform转换的过程加个过渡动画，直接transform略显生硬。

我们来实现一个效果：

描述：一个小方块，鼠标hover的时候，宽高放大一倍。

这时，我们直接想到的可能是hover的时候，添加transform:scale(2)即可，

```css
div {
    display: inline-block;
    width: 100px;
    height: 100px;
    background: green;
}
div:hover {
    transform: scale(2);
}
```

这时，我们发现鼠标hover以后，放大的效果非常生硬，如何加一个过渡呢？transition即可

```css
div {
    display: inline-block;
    width: 100px;
    height: 100px;
    background: green;
    transition: transform 1s;
}
div:hover {
    transform: scale(2);
}
```

# animation 动画

## 基本使用

通常是通过@keyframes定义动画，然后通过animation引入动画

animation也是一个复合属性，包含：

-   animation-name: 动画名称，由@keyframes定义
-   animation-duration：动画持续时间
-   animation-timing-function：动画函数，默认是ease
-   animation-delay：动画延迟时间
-   animation-iteration-count: 动画执行次数（值为：n或者infinite）
-   animation-direction: 动画是否发转播放（值为：normal,alternate）

合起来写法顺序即：

```css
animation: name duration timing-function delay iteration-count direction;
```

我们来用animation 实现一个宽度变大的动画：

```css
@keyframes expand {
    from {
        width: 100px;
    }
    to {
        width: 200px;
    }
}

div {
    animation: expand 1s;
}
```

这个时候，可能大家会有个疑问？animation和transition的区别在哪里呢？

通过上面的例子，我们最直接感受到的就是：动画触发的条件不同，上面我们提到的transition需要通过hover等事件来触发，而animation则进入页面会理解播放，类似gif动图。

接下来，我们就具体看看animation和transition有哪些不同点

## animation与transition的不同点

不同点：

1.  触发条件不同：transition需要通过hover等事件来触发，animation则是立即播放。
2.  循环：animation可以通过animation-iteration-count设置播放次数，而transition只能播放一次。
3.  精确性：animation可以设置每一帧的样式和属性，transition无法控制到这么细
4.  与js的交互：animation和js交互不是很紧密，transition和js结合更强大，可以搭配实现我们想要的效果。

所以，在实际开发过程中，

1.  我们想要更灵活定制动画的时候，可以考虑用animation，
2.  如果是简单的from，to效果，用transition，
3.  如果想要与js搭配实现动画，用transition