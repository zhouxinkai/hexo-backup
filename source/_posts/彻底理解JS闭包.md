---
title: 彻底理解JS闭包
categories: JavaScript
tags: [JavaScript, 闭包, 前端开发, Scope chain]
description: 闭包并不是JS所独有的，在计算机科学中其是一个普遍的概念，在Python中也有闭包的概念，但闭包在Python应用不是很广泛，JS可谓是把闭包发扬光大，普照你我众猿。
toc: true
date: 2016-05-29 13:10:28
---
闭包并不是JS所独有的，在计算机科学中其是一个普遍的概念，在Python中也有闭包的概念，但闭包在Python应用不是很广泛，JS可谓是把闭包发扬光大，普照你我众猿。
<!--more-->

## 闭包现象
闭包是指有权访问另一个函数作用域中的变量的函数，创建闭包的常见方式，就是在一个函数内部创建另一个函数。
```JavaScript
function outer(){
    var context = "outer";
    return function inner(){
        console.log(context);
    }
}
var fn = outer();
//在创建inner函数时，发生了闭包，当outer被返回后，
//在inner函数中仍然可以访问外部函数outer中的变量，
//我们称内部函数inner闭包了外部函数outer的context变量。

fn();
//输出outer
```

## 原理
**Hint：**先认真看上一篇文章<深刻理解JS的作用域链>，理解了作用域链再来看闭包都是分分钟的事。

根据作用域链的创建规则，当执行`var fn = outer();`语句时，会创建一个outer函数对应的变量对象。
然后返回了一个函数inner，inner函数在**创建的时候**（注意还没执行），会事先创建一条作用域链，然后将作用域链的引用赋给inner函数的`内部属性[[Scope]]`，
**重要的是**上面创建的这条作用域链中的首元结点指向了outer函数对应的变量对象。

**总结：闭包从字面意思理解，即为封闭包含，
因为内部函数在被创建时，其作用域链对外部函数对应的变量对象存在一个引用，
而JS采用引用计数的方法进行内存管理，
所以当外部函数被执行完毕后，其对应的变量对象不会被回收，
这样就发生了闭包，在外部函数执行完毕后，我们在内部函数中仍然可以访问外部函数作用域中的变量。**

## 闭包的应用
- 模仿块级作用域
- 定义函数的public接口，public方法在JS中被称为特权方法

## 一点思考
越看JS越觉得JS是一门奇葩语言，你说我搞个继承还要通过原型去实现，定义类的public接口还要通过闭包去实现，直接下面这样不好吗，清楚明了？
```C++
class CSubClass : public CBaseClass
{
public:
    CSubClass():CBaseClass(){};
    ~CSubClass(void){};
    void PublicMethod(){};

protected:
    virtual BOOL handle_event (HELEMENT he, BEHAVIOR_EVENT_PARAMS& params); 
private:
    std::wstring m_wstrTargetId;
};
```
JS硬要搞的这么奇葩，更多的时候我们应该把精力放在如何设计类上面，包括如何从实际生活中抽象出一个类，这个类要隐藏什么信息，该把什么暴露出来，而且类的接口应该展现出一致的抽象层次；这个类和其他类应该是什么样的关系，按照耦合关系的强弱，分为依赖、关联、聚合、组合、实现、继承；等等。说了这么多，我只有一个感觉JS的命太好了，这个当年10天之内被设计出来的语言，谁也没有想到现如今统一了浏览器，只能怪Java的Applet不给力，然而语言只不过是工具，技术是相通的，重在应用，更多的时候我们不会一个东西，不是对语言本身不了解，更多的时候是对业务不了解，不知道自己要解决什么问题。

