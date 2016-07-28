---
title: 深刻理解JS的作用域链
categories: JavaScript
tags: [JavaScript, Scope chain, 前端开发, 闭包]
description: 作用域链的概念对理解闭包至关重要
toc: true
date: 2016-05-28 23:47:42
---
作用域链的概念对理解闭包至关重要
<!--more-->
## 先来一个例子

```JavaScript
var scope = "global";
function CheckScope(){
    var scope = "local";
    return scope;
}
CheckScope();
//结果为local
```
1.当代码进入Global Execution Context后，会创建Global VO
![](http://7xtj85.com1.z0.glb.clouddn.com/global%20EC.png)
2.当代码执行到`CheckScope();`语句的时候，进入CheckScope Execution Context；根据上一篇文章<执行环境>的介绍，这里会创建CheckScope VO，并设置CheckScope Execution Context的`[[Scope]]属性`
![](http://7xtj85.com1.z0.glb.clouddn.com/local_ECS.png)

## 作用域链的创建规则
### 当定义一个函数时
在函数内部会创建一个`[[Scope]]属性`，这个属性指向一条作用域链。
也就是说在定义函数时，会**事先创建**一条作用域链。
这从chrome中可以看出来，如下图所示，`<function scope>即为我说的[[Scope]]属性`
![](http://7xtj85.com1.z0.glb.clouddn.com/test.png)
而这条事先就创建好的作用域链的创建规则也是很重要的，有以下几点：
- JS中只有**两种类型**的作用域：全局作用域、函数作用域，所以在作用域链上的对象，**只可能**是window对象或者函数执行环境所对应的变量对象，**但是with语句是一个例外**，其可以临时在作用域链的前端临时增加一个**普通对象**。

```JavaScript
var o = {
    bruceZhou: 'bruceZhou',
    fn: function(){
        console.log(fn)
        console.log(bruceZhou);
    }
}
o.fn();
//ERROR报错
//因为在执行匿名函数时，其作用域链为匿名函数所对应的变量对象--->window对象
//所以会找不到fn和bruceZhou的定义

//最重要的是其作用域链不包括对象o，对象o只负责保存fn
//在JS中只有全局作用域、函数作用域，并没有对象作用域这一说
//如果要在fn内部访问对象o，可以引用this或者使用with语句
```

- 一个函数被定义时，在确定其`[[Scope]]属性`时，JS解释器执行如下的规则：从函数内部向外遍历，每当**碰到一个`function {...}`时**，就将其对应的变量对象添加至作用域链中去，如此下去，直到window对象，然后将作用域链的引用赋给`[[Scope]]属性`。

### 当调用这个函数时
解释器会先创建一个新的变量对象，
然后将这个变量对象的添加至上面那个作用域链的栈顶，
此后将函数内部的`[[Scope]]属性`直接赋值给执行环境的`[[Scope]]属性`。

### 当函数执行完之后
1.对应的函数执行环境肯定会被销毁
2.函数内部属性[[Scope]]指向的作用链的栈顶肯定会被pop，以解除对该执行函数所对应的变量对象的引用，
3.**但该执行函数所对应的变量对象却不一定会被销毁，**
因为可能还有其他东西引用着这个变量对象，这时就会发生闭包现象。

## 作用域链的数据结构
作用域即变量对象，**作用域链是一个由变量对象组成的带头结点的单向链表，其主要作用就是用来进行变量查找。而[[Scope]]属性是一个指向这个链表头结点的指针。**

### 带头结点的单链表
1.结点的存储结构（用C语言来表示）
```C
typedef struct _tNODE{
    VariableObject *pVO;
    //指向一个变量对象，即指向一个作用域
    struct _tNODE *next;
    //指向下一个结点
}tNODE, *tPNODE;
```

2.猜想
我猜想作用域链的数据大概如下，是一个链栈，只是为了说明问题，不保证准确性，当然我也是不会为它负责的。
![](http://7xtj85.com1.z0.glb.clouddn.com/scope%20chainpng.png)

