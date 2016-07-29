---
title: JS中的this
categories: JavaScript
tags: [JavaScript, this, 前端开发]
description: 简单介绍一下this
toc: true
date: 2016-05-28 22:00:13
---
<!--more-->
## 一些总结
- 和C++、Java类似，this指向当前正在调用该方法的对象本身；
- this是执行环境的一个重要属性，其指向函数赖以执行的环境**对象**（注意是一个对象，而且是一个**普通对象**，而不是一个执行环境）；
- this的值在进入上下文的时候才会被确定；
- this的值**直接受**函数调用方式的影响，其是由激活上下文代码的调用者来提供的，指向调用时所在函数所绑定的对象。

## 一个例子
```JavaScript
var obj = {  
    context: "object",
    method: function () {
        var context = "function";
        console.log(this.context); 
        /*this指向当前正在调用该方法的对象本身*/
    }
}
obj.method();
//结果为object，而不是function
```
