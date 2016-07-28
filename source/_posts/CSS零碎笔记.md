---
title: CSS零碎笔记
categories: CSS
tags: [CSS, 前端开发]
description: CSS的知识总的来说比较杂，这里记录一下工作用到的东西，方便以后查看，将持续更新...
toc: true
date: 2016-06-03 10:36:28
---
CSS的知识总的来说比较杂，这里记录一下工作用到的东西，方便以后查看，将持续更新...
<!--more-->
### 四个伪类选择符
 - E:link，设置超链接a在**未被访问前**的样式。 
 - E:visited，设置超链接a在其链接地址**已被访问过**时的样式。 
 - E:hover，设置鼠标**悬停在**元素上时的样式。 
 - E:active，设置元素在被用户激活（在鼠标点击与释放之间发生的事件）时的样式。 
这四个伪类的前后顺序在某些情况下是很重要的。

### 盒模型
![](http://7xtj85.com1.z0.glb.clouddn.com/%E7%9B%92%E6%A8%A1%E5%9E%8B.png)
 - Content(内容)： 盒子的内容，显示文本和图像，CSS内定义的`width`和`height`属性，只是设置内容区域的宽度和高度。。
 - Margin(外边距)： 外边距是透明的，其决定着不同盒子之间的距离，而两个元素之间间距到底为多少，取两者中**最大的**margin。
 - **实际高度** = 元素的内容高度 + `padding-top` + `padding-bottom` + `border-top` + `border-bottom`，margin是不算在内的。
元素的实际宽度计算方法同上。

### CSS布局模型
 - 流动模型：Flow，是默认的网页布局模式。
块状元素独占一行，宽度为100%；内联元素在所处的包含元素内从左到右显示。
 - 浮动模型：Float，不让块级元素独占一行。
 - 层模型：Layer，CSS定义了一组定位（position）属性来支持层布局模型。

### position定位属性
 - 默认值`position：static`
**没有定位**，元素出现在正常的流中（忽略 top, bottom, left, right 或者 z-index 声明）。
 - 绝对定位`position:absolute`
相对于其**最接近**的一个**具有定位属性**的父包含块进行定位。如果不存在这样的包含块，则相对于`body`元素。
 - 相对定位`position：relative`
相对于元素**本身正常位置**进行定位，其偏移前后，父包含块内其他元素的位置保持不变。（正常位置即按static方式确定的位置）
 - 固定定位`position：fixed`
位置固定不变，其是绝对定位的特殊情况，其相对于浏览器窗口定位，它的位置不会随浏览器窗口的滚动条滚动而变化。

### 隐性改变display类型
有一个有趣的现象就是当为元素（不论之前是什么类型元素，display:none 除外）设置以下 3 个句之一：
1. position : absolute 
2. float : left 或 float:right 
3. overflow:hidden/scroll/auto
简单来说，只要html代码中出现以上三句之一，元素的display显示类型就会自动变为以 display:inline-block（内联块状元素）的方式显示，当然就可以设置元素的 width 和 height 了，且默认宽度不占满父元素。
另外说到包裹性，在这里面的鼻祖应该算是diaplay:inline-block;其他的像overflow，position:absolute,float这几个产生包裹性的原因，本质应该都是因为他们使Dom元素具有了类似inline-block的性质，所以，凡是设置了像display:inline-block或者overflow:hidden/scroll/auto或者position:absolute或者float:left/right这几个属性时，他们`具有的包裹性都可以清除他们子元素浮动造成的影响`。

### 居中
- 水平居中：定宽+左右margin为auto
- 垂直居中：height和line-height相等
- text-align：center，设置文本水平居中

### 参考资料
[CSS参考手册](http://7xtj85.com1.z0.glb.clouddn.com/CSS%E5%8F%82%E8%80%83%E6%89%8B%E5%86%8C.chm)

