# CSS之盒模型--css学习笔记

## 1\. 盒子模型

在 CSS 中，所有的元素都被一个个的“盒子（box）”包围着，理解这些“盒子”的基本原理，是我们使用 CSS 实现准确布局、处理元素排列的关键。

### **块级（block）盒子**:

-   盒子会在内联的方向上扩展并占据父容器在该方向上的所有可用空间，在绝大数情况下意味着盒子会和父容器一样宽
-   每个盒子都会换行
-   [`width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/width "https://developer.mozilla.org/zh-CN/docs/Web/CSS/width") 和 [`height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/height "https://developer.mozilla.org/zh-CN/docs/Web/CSS/height") 属性可以发挥作用
-   内边距（padding）, 外边距（margin）和 边框（border）会将其他元素从当前盒子周围“推开”

**内联盒子（Inline box）**:

-   盒子不会产生换行。
-   [`width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/width "https://developer.mozilla.org/zh-CN/docs/Web/CSS/width") 和 [`height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/height "https://developer.mozilla.org/zh-CN/docs/Web/CSS/height") 属性将不起作用。
-   垂直方向的内边距、外边距以及边框会被应用但是不会把其他处于 `inline` 状态的盒子推开。
-   水平方向的内边距、外边距以及边框会被应用且会把其他处于 `inline` 状态的盒子推开。

用做链接的 `<a>` 元素、 `<span>`、 `<em>` 以及 `<strong>` 都是默认处于 `inline` 状态的。

我们通过对盒子[`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display "https://developer.mozilla.org/zh-CN/docs/Web/CSS/display") 属性的设置，比如 `inline` 或者 `block` ，来控制盒子的外部显示类型。

### [补充：内部和外部显示类型](https://developer.mozilla.org/zh-CN/docs/Learn/CSS/Building_blocks/The_box_model#%E8%A1%A5%E5%85%85%EF%BC%9A%E5%86%85%E9%83%A8%E5%92%8C%E5%A4%96%E9%83%A8%E6%98%BE%E7%A4%BA%E7%B1%BB%E5%9E%8B "https://developer.mozilla.org/zh-CN/docs/Learn/CSS/Building_blocks/The_box_model#%E8%A1%A5%E5%85%85%EF%BC%9A%E5%86%85%E9%83%A8%E5%92%8C%E5%A4%96%E9%83%A8%E6%98%BE%E7%A4%BA%E7%B1%BB%E5%9E%8B"),`display` 属性可以改变盒子的外部显示类型是块级还是内联，这将会改变它与布局中的其他元素的显示方式。

## **CSS 盒模型**

完整的 CSS 盒模型应用于块级盒子，内联盒子只使用盒模型中定义的部分内容。模型定义了盒的每个部分 —— margin, border, padding, and content —— 合在一起就可以创建我们在页面上看到的内容。为了增加一些额外的复杂性，有一个标准的和替代（IE）的盒模型。

### [盒模型的各个部分](https://developer.mozilla.org/zh-CN/docs/Learn/CSS/Building_blocks/The_box_model#%E7%9B%92%E6%A8%A1%E5%9E%8B%E7%9A%84%E5%90%84%E4%B8%AA%E9%83%A8%E5%88%86 "https://developer.mozilla.org/zh-CN/docs/Learn/CSS/Building_blocks/The_box_model#%E7%9B%92%E6%A8%A1%E5%9E%8B%E7%9A%84%E5%90%84%E4%B8%AA%E9%83%A8%E5%88%86")

CSS 中组成一个块级盒子需要：

-   **Content box**: 这个区域是用来显示内容，大小可以通过设置 [`width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/width "https://developer.mozilla.org/zh-CN/docs/Web/CSS/width") 和 [`height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/height "https://developer.mozilla.org/zh-CN/docs/Web/CSS/height").
-   **Padding box**: 包围在内容区域外部的空白区域；大小通过 [`padding`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/padding "https://developer.mozilla.org/zh-CN/docs/Web/CSS/padding") 相关属性设置。
-   **Border box**: 边框盒包裹内容和内边距。大小通过 [`border`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border "https://developer.mozilla.org/zh-CN/docs/Web/CSS/border") 相关属性设置。
-   **Margin box**: 这是最外面的区域，是盒子和其他元素之间的空白区域。大小通过 [`margin`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin "https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin") 相关属性设置。

### [标准盒模型](https://developer.mozilla.org/zh-CN/docs/Learn/CSS/Building_blocks/The_box_model#%E6%A0%87%E5%87%86%E7%9B%92%E6%A8%A1%E5%9E%8B "https://developer.mozilla.org/zh-CN/docs/Learn/CSS/Building_blocks/The_box_model#%E6%A0%87%E5%87%86%E7%9B%92%E6%A8%A1%E5%9E%8B")

在标准模型中，如果你给盒设置 `width` 和 `height`，实际设置的是 _content box_。padding 和 border 再加上设置的宽高一起决定整个盒子的大小。

**备注：**  margin 不计入实际大小 —— 当然，它会影响盒子在页面所占空间，但是影响的是盒子外部空间。盒子的范围到边框为止 —— 不会延伸到 margin。

### [替代（IE）盒模型](https://developer.mozilla.org/zh-CN/docs/Learn/CSS/Building_blocks/The_box_model#%E6%9B%BF%E4%BB%A3%EF%BC%88ie%EF%BC%89%E7%9B%92%E6%A8%A1%E5%9E%8B "https://developer.mozilla.org/zh-CN/docs/Learn/CSS/Building_blocks/The_box_model#%E6%9B%BF%E4%BB%A3%EF%BC%88ie%EF%BC%89%E7%9B%92%E6%A8%A1%E5%9E%8B")

你可能会认为盒子的大小还要加上边框和内边距，这样很麻烦，而且你的想法是对的 ! 因为这个原因，css 还有一个替代盒模型。使用这个模型，所有宽度都是可见宽度

默认浏览器会使用标准模型。如果需要使用替代模型，您可以通过为其设置 `box-sizing: border-box` 来实现。这样就可以告诉浏览器使用 `border-box` 来定义区域，从而设定您想要的大小。

```css
.box {
  box-sizing: border-box;
}

```

参考： [盒模型](https://developer.mozilla.org/zh-CN/docs/Learn/CSS/Building_blocks/The_box_model#%E4%BB%80%E4%B9%88%E6%98%AF_css_%E7%9B%92%E6%A8%A1%E5%9E%8B%EF%BC%9F