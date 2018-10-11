---
title: 理解JS的执行环境
categories: JavaScript
tags: [JavaScript, 前端开发]
description: 执行环境（Execution context，EC）或执行上下文，是JS中一个极为重要的概念
toc: true
date: 2016-05-25 15:15:36
---
执行环境（Execution context，EC）或执行上下文，是JS中一个极为重要的概念
<!--more-->
## EC的组成
当JavaScript代码执行的时候，会进入不同的执行上下文，这些执行上下文会构成了一个**执行上下文栈**（Execution context stack，ECS）。
![EC的组成](http://www.53zi.com/Execution%20Context.png)
- 变量对象（Variable object，VO）: 变量对象即包含变量的对象，除了我们无法访问它外，和普通对象没什么区别。
- `[[Scope]]`属性:作用域即变量对象，**作用域链是一个由变量对象组成的带头结点的单向链表，其主要作用就是用来进行变量查找。而[[Scope]]属性是一个指向这个链表头节点的指针。**
- this: 指向一个环境对象，注意是一个对象，而且是一个**普通对象**，而不是一个执行环境。

## 产生EC的两个阶段
当一段JS代码执行的时候，JS解释器会通过两个阶段去产生一个EC
1. 创建阶段（当函数被调用，但是开始执行函数内部代码之前）
 1. 创建变量对象VO
 2. 设置`[[Scope]]`属性的值
 3. 设置this的值
2. 激活/代码执行阶段
**初始化变量对象**，即设置变量的值、函数的引用，然后解释/执行代码。

## 创建变量对象VO
1. 根据函数的参数，创建并初始化arguments object
2. 扫描函数内部代码，查找函数声明（function declaration）
 - 对于所有找到的函数声明，将函数名和函数引用存入VO中
 - 如果VO中已经有同名函数、同名变量，那么就进行`覆盖`
```JavaScript
//函数声明
function f(){}
```
3. 扫描函数内部代码，查找变量声明（Variable declaration）
 - 对于所有找到的变量声明，将变量名存入VO中，并初始化为undefined
 - 如果变量名跟已经声明的形参或函数相同，则`什么也不做`
```JavaScript
//变量声明，必须通过var关键字声明
var example = 'example'
```
**注：
1.**步骤2和3也称为声明提升（`declaration hoisting`）
2.当函数和变量同名时，函数的优先级要高一点
3.没有使用var声明的变量（这种变量是全局的声明方式，只是给Global添加了一个属性，并不在VO中）
4.函数表达式（与函数声明相对）不包含在VO之中


## 执行环境的分类
- 全局执行环境
在浏览器中，其指`window对象`，是JS代码开始运行时的默认环境。
全局执行环境的变量对象**始终都是**作用域链中的最后一个对象。
- 函数执行环境
当某个函数被调用时，会**先创建**一个执行环境及相应的作用域链。**然后**使用arguments和其他命名参数的值来**初始化**执行环境的变量对象。

注：上面的分类也说明了JS中只有**两种作用域**（作用域即变量对象）：全局作用域、函数作用域，并没有块级作用域，更没有对象作用域(见最后的例子，但是with语句是一个例外，其可以临时在作用域链的前端增加一个普通对象)

## this
见文章：JS中的this

## 参考资料
[JavaScript的执行上下文, 五星级好文](http://www.cnblogs.com/wilber2013/p/4909430.html)
[JavaScript中的this](http://www.cnblogs.com/wilber2013/p/4909505.html)
[理解JavaScript中的作用域链](http://www.cnblogs.com/wilber2013/p/4909459.html)
[Scope chain](http://dmitrysoshnikov.com/ecmascript/chapter-4-scope-chain/)
[JavaScript高级程序设计](https://book.douban.com/subject/10546125/)
