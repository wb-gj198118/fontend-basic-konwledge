**在 CSS 中，一些属性的修改可能会导致浏览器触发回流，而另一些属性的修改则不会触发回流，以下是一些不会触发回流的操作：**

# 1. transform  
使用 transform 属性进行 2D 或 3D 转换，例如平移、旋转、缩放等。
# 2. opacity
使用 opacity 属性改变元素的透明度。
# 3. visibility
使用 visibility 属性控制元素的可见性。
# 4. background-color、color
修改元素的背景色、文字颜色等属性。
# 5. box-shadow
使用 box-shadow 属性添加元素阴影效果。
# 6. text-shadow
使用 text-shadow 属性为文字添加阴影效果。
# 7. outline
使用 outline 属性添加元素轮廓线效果。
# 8. border-radius
使用 border-radius 属性添加元素圆角效果。
# 9. transition
使用 transition 属性实现元素的平滑过渡效果。

***需要注意的是，虽然这些属性的修改不会触发回流，但是它们的修改仍然可能引起重绘（repaint）操作，因此应该尽可能减少它们的修改次数，以提高页面性能。***