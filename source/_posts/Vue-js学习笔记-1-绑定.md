---
title: Vue.js学习笔记 (1):绑定
categories: JavaScript
tags: [JavaScript, Vue, 前端开发]
description: 本文的关键词是绑定
toc: true
date: 2016-07-28 13:26:43
---
<!--more-->
### 初始化

初始化vue时，我们一般指定三个选项：
- `el`:element，表示MVVM中的View，是一个CSS选择器，是用户实际看到的DOM元素
- `data`:表示MVVM中的Model，是一个原生的JavaScript对象
- `methods`:View可以触发的函数

还有其他的一些选项，可以在[Vue.js的API文档](http://vuejs.org.cn/api/)中查看。

Vue.js 提供的核心是 MVVM 中的 VM，也就是 `ViewModel`，其负责`连接` View 和 Model，保证视图和数据的一致性，因此在一些文档中经常会使用 `vm` 这个变量名。

### 例1
```JavaScript
<div id="demo">
    <p>{{message}}</p>
    <input v-model="message">
</div>

var data = {
    message: 'Hello Vue.js!'
}

var vm = new Vue({
    el: '#demo',
    data: data
})
```
在创建vm对象的过程中，其实 Vue.js `已经建立了DOM元素和数据data之间的连接`,
即把二者`绑定`在了一起，这表示对 data.message 的任何改动，都会触发 DOM的更新。 
同样当用户在输入栏里打字的时候，数据会被同步回 data.message 当中去,
这也说明在MVVM中，`Model和View是双向绑定的`，即若修改了View，则Model会立即改变，同样的若修改了Model，则View也会立即改变。

### 例2
```JavaScript
<a v-bind:href="url">test</a>
```
这里的href是v-bind指令的参数，它告诉v-bind指令将元素的href属性跟表达式url的值`绑定`在一起

### 参考资料
[官方文档](http://vuejs.org.cn/guide/)
[Vue.js 中文入门](http://www.html-js.com/article/column/99)

