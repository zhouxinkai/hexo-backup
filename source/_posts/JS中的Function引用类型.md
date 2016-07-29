---
title: JS中的Function类型
categories: JavaScript
tags: [JavaScript, 函数, 前端开发]
description: 在JS中一切都是对象
toc: true
date: 2016-05-25 19:09:56
---
在JS中一切都是对象
<!--more-->
**函数是对象，函数名是指针：**在JS中函数实际上是对象，每个函数都是Function类型的实例,而函数名其实就是一个**变量**

## 函数声明与函数表达式
```JavaScript
//函数声明
function sum(num1, num2){
    return num1 + num2;
}
//函数表达式
var f = function(num1, num2){
    return num1 + num2;
}
```
对于函数声明，JS会通过一个**声明提升**的过程，将函数名和函数引用添加到执行环境中。
而对于函数表达式则必须等到解释器执行到所在代码行时，才会真正的被解释执行。

## 作为值的函数
因为JS中的**函数名本身就是变量**，所以函数也可以作为值来使用。
即不仅可以像传递参数一样把一个函数传递给另一个函数，而且可以将一个函数作为另一个函数的结果返回

## 函数的内部属性
- arguments对象
其主要用途是传递参数，但其还有一个名叫**callee(被调用者)**的属性，指向拥有这个arguments对象的函数

- this
见文章：JS中的this

- caller
指向调用当前函数的函数，和this不一样

## 函数属性和方法
- length
其表示函数希望接收到的命名参数的个数
- prototype
原型，在此不做过多介绍

- apply()和call()
这两个方法的用途都是在**特定的作用域**中调用函数，实际上等于设置函数体内的**this值**。
二者的作用相同，区别**仅仅在于**接收参数的方式不同，二者的第一个参数都是要指定的作用域，
但第二个参数，apply是数组，而call必须逐个列出
```JavaScript
function sum(num1, num2){
    return num1 + num2;
}
var obj = {};
sum.apply(obj, [10, 10]);
sum.call(obj, 10, 10);
```

- bind()
其会创建一个函数实例，实例的this会被绑定到传给bind函数的值
```JavaScript
function sayColor(){
    console.log(this.color);
}
var obj = {color:'blue'};
var objectSayColor = sayColor.bind(obj);
objectSayColor();
//输出blue
```

## 参考资料
[JavaScript高级程序设计](https://book.douban.com/subject/10546125/)
