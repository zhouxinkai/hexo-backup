---
title: JavaScript中的函数形参
toc: true
date: 2016-05-25 09:50:39
categories: JavaScript
tags: [JavaScript, 函数, 前端开发]
description: JavaScript中的函数总体来说与大多数其他语言有所不同
---
JavaScript中的函数总体来说与大多数其他语言有所不同
下面将从返回值、参数传递两个方面进行说明
<!--more-->
## 基本语法
```JavaScript
function functionName(arg0, arg1, ..., argN){
    statements
}
/*以下是一个例子*/
function sayHi(name, message){
    alert('Hello ' + name + ',' + message);
}
/*invoke*/
sayHi('bruce zhou', 'how are you today?');
```

## 返回值
- 在定义时不必指定返回值，因为JS中的函数可以在任何时候返回任何值
- 未指定返回值的函数（没有return语句或return语句没有带返回值），返回undefined

## 参数传递
**在JS中，因为函数参数是用一个数组arguments传递的，而其他语言是用形参传递的，所以:**
- 没有函数声明的概念，命名的形参只提供便利，并不是必需的
比如你定义的函数只接受两个参数，但在调用的时候，可以传递一个、三个甚至不传递参数
- 可以向函数传递任意数量的参数，并且可以通过arguments对象来访问这些参数
- arguments和形参
 - 在函数内部，二者可以一起使用
 - 二者可以互换使用
 - arguments的值和对应形参的值保持**同步**
 **注：**但是二者的内存空间是独立的，只是他们的值会同步
- 没有传递值的形参将自动被赋予undefined 
- 参数传递是**值传递**，不能通过引用传递参数
