---
title: Vue.js学习笔记 (2):指令
categories: JavaScript
tags: [JavaScript, Vue, 前端开发]
description: 
toc: true
date: 2016-07-28 14:02:51
---
<!--more-->
### 绑定表达式
在 Vue.js 中，一段绑定表达式由一个简单的 JavaScript 表达式和可选的一个或多个过滤器构成。
其可以用在`{% raw %}{{  }}{% endraw %}`、`{% raw %}{{{  }}}{% endraw %}`内，也可以作为指令的值。

### 指令
指令都带有`前缀v-`，以指示它们是Vue.js提供的特殊属性。
指令的值被限定为绑定表达式，其的作用是把表达式的值和DOM元素`绑定`起来。

#### 参数
有些指令可以在其名称后面带一个“参数” (Argument)，中间放一个`冒号`隔开。
```JavaScript
<a v-bind:href="url"></a>
```

#### 修饰符
修饰符 (Modifiers) 是以`半角句号 . `开始的特殊后缀，用于表示指令应当以特殊方式绑定。
```JavaScript
<input v-model="newTodo" v-on:keyup.enter="addTodo">
```

#### 常用指令
- v-on：监听DOM事件，事件类型可以作为它的参数
- v-bind：绑定HTML属性
- v-text：等同于{% raw %}{{}}{% endraw %}
- v-componet：自定义组件
- v-for：循环

### 参考资料
[官方文档](http://vuejs.org.cn/guide/)
[Vue.js 中文入门](http://www.html-js.com/article/column/99)
