flex布局



主轴定义 （还有一个交叉轴）

```css
flex-direction: [row、row-reverse、column、column-reverse]



flex-flow = flex-direction + flex-wrap

例子：
	flex-flow: row wrap;
```





- flex-grow
  - 按比例分配父元素（增加子元素的大小占满父元素）
- flex-shrink
  - 减小该元素的大小占满父元素
- flex-basis
  - 设置了该元素的空间大小



flex = flex-grow + flex-shrink + flex-basis

例子：

```css
.box {
    flex: 1 1 auto;
}
```





- align-item：

  - 使元素在交叉方向对齐（注意：指的是同级元素们在交叉轴方向对齐）
  - 参数（stretch、flex-start、flex-end、center）

- justify-content：

  - 使元素在主轴方向对齐
  - 参数（stretch、flex-start、flex-end、center、space-around、space-between）

  

