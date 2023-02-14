## 外边距重叠

外边距重叠一般有两种情况

-   一种是父子盒子外边距重叠（平时一般会给父盒子写padding，所以比较少见）；
-   一种兄弟盒子外边距重叠（常见），实际开发中容易出现在列表渲染的时候；

```css
  <div className="wrap">
    {state.map((item, index) => {
      return (
        <div key={index} className="box">
          {item.name}
        </div>
      )
    })}
  </div>
  ​
  .wrap {
    height: 300px;
    width: 300px;
    background-color: skyblue;
    margin: 0 auto;
  }
  .box {
    height: 100px;
    width: 100px;
    background-color: pink;
    margin: 20px;
  }
  
```

父子外边距重叠

-   父级使用`overflow:hidden`或者父级使用`padding`解决

兄弟外边距重叠

-   兄弟两个盒子的`margin`都设为需要间隔的`px`，也就是如果上下两个兄弟如果需要`10px`的间隙，就给两个盒子都设置`10px`的`margin`值
-   给两个盒子外面套一层`div`，并给这个`div`设为**BFC**，如`overflow:hidden`，原理是**BFC**特性：属于不同**BFC**的两个元素不会发生边距重叠

## 文字和图片对齐

文字和图片默认是以`baseline`基线对齐，也就是与x字母底部对齐（如果只考虑小写字母，则只需要给图片`vertical-align:middle`就可以了，但是开发常常考虑的是文字和数字，所以这种情况忽略），但是文字/大写字母/数字对齐方式有所不同，开发中只需要管文字/大写字母/数字对齐就行了，如下：

```ini
<div className="wrap">
  <img
    src="https://p3-passport.byteimg.com/img/user-avatar/7827dfb31e84e8f3d5bbcf1a2688b50d~100x100.awebp"
    alt=""
  />
 {/* 文字外面是否包一层span不影响 */}
  xx小写字母
</div>
​
.wrap {
  width: 300px;
  text-align: center;
  margin: 20px auto;
  font-size: 14px;
}
.wrap img {
  width: 20px;
}
```

![2.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bd4dfb24b0084b0996534629de269976~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

解决方案：

-   使用flex布局
-   ```css
    display: flex;
    align-items: center;
    ```
    

## flex两列居中布局

> flex默认值是0 1 auto，也就是flex-grow:0 , flex-shrink:1,flex-basis:auto的简写
> 
> flex:1是flex:1 1 0%的简写，也就是flex-grow:1 , flex-shrink:1,flex-basis:0%（忽略自身宽度）的简写
> 
> flex: auto是flex:1 1 auto的简写，也就是flex-grow:1 , flex-shrink:1,flex-basis:auto（元素自身宽度）的简写
> 
> 完全等分布局使用flex:1 ，根据内容宽度动态分配宽度则使用flex:auto

整个页面两列布局如果不想`margin:0 auto`去居中，可以用`flex`实现

外层大盒子使用flex，左右两个字盒子使用`flex: 1 1 auto`(不熟悉可以去看一下张旭鑫或者阮一峰的博客)，或者`flex: auto`并不给宽度，在孙盒子里给定宽度就能实现两列居中，主要原理是，用了`flex-grow`为`1`和`flex-basis`为`auto`，在没有宽度的时候，宽度为子元素的content的宽度，然后因为左右两个flex-grow都为1，均分剩下的空间，就实现了居中效果。

## 文本换行左侧靠齐

用flex的时候，又需要换行的时候，容易遇到这种情况

```ini
import './App.css'
function App() {
  const img =
    'https://p3-passport.byteimg.com/img/user-avatar/7827dfb31e84e8f3d5bbcf1a2688b50d~100x100.awebp'
  const state = [img, img, img, img, img, img, img, img]
  return (
    <div className="wrap">
      {state.map((item, index) => {
        return <img src={item} alt="" key={index} />
      })}
    </div>
  )
}
​
export default App
​
.wrap {
  display: flex;
  width: 300px;
  background-color: skyblue;
  flex-wrap: wrap;
  justify-content: space-between;
}
.wrap img {
  height: 40px;
  width: 40px;
  margin: 20px;
}
```

张旭鑫文章里有多种解决方案，[传送门](https://www.zhangxinxu.com/wordpress/2019/08/css-flex-last-align/ "https://www.zhangxinxu.com/wordpress/2019/08/css-flex-last-align/")，这里写一种常用的：也就是下面的grid布局

## grid多行多列布局

> grid和flex一个主要的区别就是 grid是二维布局，flex是一维布局
> 
> grid-template-columns/grid-template-rows设置行列元素和其大小
> 
> gap/columns-gap/rows-gap设置行列的间距

下面是一个`grid`布局的`demo`：

```javascript
function App() {
  const img =
    'https://p3-passport.byteimg.com/img/user-avatar/7827dfb31e84e8f3d5bbcf1a2688b50d~100x100.awebp'
  const state = [img, img, img, img, img, img, img]
  return (
    <div className="wrap">
      {state.map((item, index) => {
        return <img src={item} />
      })}
    </div>
  )
}
```

```css
.wrap {
  width: 300px;
  display: grid;
  grid-template-columns: repeat(3, 60px);/*具体的数字或者auto-fill*/
  row-gap: 20px;
  justify-content: space-between;
  background-color: pink;
}
.wrap img {
  width: 60px;
  height: 60px;
}
```

## 响应式布局

> 缺点：使用媒体布局后，会写三套css，会比较冗余，而且有时候修改一个端可能忘记或影响到另外一个端
> 
> 优点：开发速度方面快很多，因为只是css多写了几套，js和配置等都是一样的

设计师会出三套UI，该网站使用媒体布局来完成响应式的

```arduino
@media screen and (min-width: 1200px){
  /* pc样式 */
}  
@media screen and (min-width: 780px) and (max-width: 1199px){
  /* pad样式 */
}
@media screen and (max-width: 779px){
  /* h5样式 */ 
}
```

## 粘性布局

> 需要考虑一个问题：到达顶部就悬浮，如果屏幕较小，内容又多超过屏幕可视区，则以底部为准，如[weibo.com](weibo.com "weibo.com")右侧效果

普通粘性布局（注意父元素不能**overflow:hidden**否则会失效）：

```css
{
  position: sticky;
  top: 0;
}
```

动态计算高度是否超过屏幕高度来布局，给个伪代码：

```javascript
useEffect(() => {
  // 用js获取盒子的高度，如果高度小于window.innerHeight的高度，则sticky，否则用了sticky后，还需要设置top:盒子高度-window.innerHeight
  if (fixRef.current.clientHeight < window.innerHeight) {
  } else {
   window.innerHeight - fixRef.current.clientHeight
  }
}, [])
```

## 图片的显示

> 考虑问题：
> 
> 1.  图片居中裁剪
> 2.  图片高斯模糊和饱和度调节
> 3.  图片大小压缩
> 4.  图片懒加载
> 5.  背景全屏铺满
> 6.  类似朋友圈图片

**居中裁剪:** `object-fit: cover`，我们只需要关注`contain`和`cover`这两个属性，一般`cover`较多

**高斯模糊和饱和度：**

```css
/*直接作用于图片*/
.wrap img {
  filter: saturate(100%) blur(0px);/*饱和度默认值100% 高斯模糊默认0*/
}
/*作用于元素背景模糊*/
backdrop-filter:saturate(100%) blur(100px);
```

**图片大小压缩：**

0.  通过请求的图片，后端或运维会让图片经过阿里云处理，如果支持webp则会使用webp格式和对应压缩，可以在图片的相应头字段content-type看到真实的格式
1.  放本地的图片，对于小图片，会采用base64，减少http请求，大图片不会用base64是因为体积会变为原来的4/3，所以只有小图片合适；一些大图片和UI沟通，并可用[tinify.cn/](https://tinify.cn/ "https://tinify.cn/")来压缩图片

**图片懒加载**：一般组件库里会自带有方法，原理是用`intersectionObserver`去实现，能极大减少渲染图片压力

**背景图片铺满盒子：**

```css
background-image: url('xxx.png');
background-position: 50%;/*或者center*/
background-repeat: no-repeat;
background-size: cover;
```

**类似朋友圈图片展示功能：**

```css
.fa {
    background-color: skyblue;
    width: fit-content;
}

.son {
    height: 100%;
    width: 100%;
    object-fit: cover;
    max-height: 400px;
    max-width: 400px;
    min-height: 200px;
    min-width: 200px;
}
```

## 全屏滚动布局

```css
scroll-snap-type: y mandatory; /*设置父级*/
scroll-snap-align: center; /*子级设置*/
```

## css module一些用法

> 说一下用css module的感想：
> 
> -   会感觉每次多写个styles不太方便
> -   需要`CSS Modules`插件去提示css
> -   没有必要用less module/scss module，写less/scss我们往往喜欢嵌套，但是不嵌套对性能其实更好，因为浏览器是从右边往左匹配，嵌套后说明会多出匹配的开销
> -   想尝试原子化css

修改组件库样式，只修改当前组件样式：

```css
<div className={styles.wrap}>
  <Button type="primary" className={styles.abtn}>
    A按钮
  </Button>
</div>
​
//global只能写在要改的样式前
.wrap :global .ant-btn-primary {
  background-color: pink;
}
```

使用短横线：

```css
<div className={styles['wrap-line']}></div>
```

使用多个类：

```css
<div className={`${styles.wrap1} ${styles['wrap-line']}`}></div>
```

## 文字溢出

```scss
/* 单行 */
overflow: hidden;            // 溢出隐藏
text-overflow: ellipsis;     // 溢出用省略号显示
white-space: nowrap;         // 规定段落中的文本不进行换行
​
/* 多行 */
overflow: hidden;            // 溢出隐藏
text-overflow: ellipsis;     // 溢出用省略号显示
display:-webkit-box;         // 作为弹性伸缩盒子模型显示。
-webkit-box-orient:vertical; // 设置伸缩盒子的子元素排列方式：从上到下垂直排列
-webkit-line-clamp:3;        // 显示的行数
```