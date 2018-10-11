---
title: 理解JS中的原型
categories: JavaScript
tags: [JavaScript, Prototype, 前端开发]
description: 动态语言和静态语言有很大的不同，比如在C++中定义类时，并不分配内存，而在动态语言中定义类时，却会分配内存。
toc: true
date: 2016-05-26 14:55:10
---
<!--more-->
动态语言和静态语言有很大的不同，比如在C++中定义类时，并不分配内存，而在动态语言中定义类时，却会分配内存。
- 比如在JS中**定义**了一个函数时，将会为该函数创建一个**prototype**属性，这个属性指向该函数的原型对象；JS中万物皆对象，一个对象要么是函数的实例，要么是原型的实例。
- 比如在Python中定义了一个类时，将会创建一个**类型对象**（类其实是能够创建出类实例的对象，类本身也是实例，而且是metaclass元类的实例）；Python中所有的东西都是对象，其要么是类的实例，要么是**metaclass元类**的实例。

原型对象中的属性被**所有实例所共享**，这类似于C++中的静态成员，静态成员属于类本身，而不是属于对象，但是被类的所有实例所共有。

## 创建一个空函数
```JavaScript
function Person() {};
```
像这样创建一个空函数，js解析为以下三步：
1. 创建一个Object对象（有constructor属性及[[Prototype]]属性）;
2. 创建一个函数（有name、prototype属性），再通过prototype属性引用刚才创建的对象;
3. 创建变量Person，同时把函数的引用赋值给变量Person
![如图可以表示为](http://www.53zi.com/prototype.png)

## 实例化一个对象
我们用上面这个Person函数去实例化一个对象时，js解析又是怎样呢？比如：
```JavaScript
var angela = new Person();
```
实例化出来的对象，js解析也分为下面三步：
1. 新建一个对象并赋值给变量angela：var angela = {};
2. 把这个对象的[[Prototype]]属性指向函数Person的原型对象：angela.[[Prototype]] = Person.prototype
3. 调用函数Person，同时把this指向刚创建的对象angela，对这个对象进行初始化：Person.apply(angela,arguments)
![如图可表示为](http://www.53zi.com/prototype1.png)
**总结：**构造函数、原型和实例之间的关系，每个构造函数包含一个指向原型对象的指针prototype，原型对象都包含一个指向构造函数的指针constructor，而实例都包含一个指向原型对象的内部指针\__proto__（有的地方称为[[prototype]]）。

## 重写prototype对象
在上面两个例子的基础上，再进行如下操作
```JavaScript
Person.prototype = {
    name: "bruce",
    age: 23
};
```
- 上面使用的语法将会**完全重写默认的prototype对象**，其会直接导致Person的prototype对象里面没有了constructor属性，constructor属性只能从Person.prototype.__proto__.constructor继承过来(Person.prototype的原型对象为Object原型对象)，即constructor将指向**Object构造函数**
- 把原型对象修改为另外一个对象就等于，切断了构造函数Person和**最初的原型对象**之间的联系。
但是实例angela和最初的原型对象之间的联系不变。
**注：**实例中的\__proto__指针仅指向原型，而不指向构造函数。

## 搜索一个属性
- 每当代码读取某个对象的属性时，首先会在对象实例本身中**搜索**，如果在实例中找到了该属性，则返回该属性。如果没有找到，则继续在**\__proto__**指向的原型对象中**搜索**，如果找到了该属性，则返回该属性。如此继续下去。
也就是说当为对象实例添加了一个属性时，这个属性就会**屏蔽**原型对象中保存的**同名属性**。
- `hasOwnProperty`方法可以检测一个属性是存在于实例中，还是存在于原型中，只有给定属性存在于实例中，才会返回true。

## prototye属性
- JS中万物皆对象,通常来说，javascript中的对象就是一个指向原型对象的指针和一个自身的属性列表。
prototype属性本质上它就是一个普通的指针,其之所以特别，是因为javascript时**读取属性时的遍历机制决定的**。
只有**构造函数才具有prototype属性**，且只有以下对象才是构造函数：
5种基本引用类型（Object、Array、Date、RegExp、Function）、3种包装类型（Boolean、Number、String）、Function的实例
javascript创建对象时采用了写时复制的理念，当调用构造函数创建一个实例后，该实例内部将包含一个指针（内部属性,下图中暂时称为inobj），指向构造函数的原型对象。
![调用构造函数创建一个实例](http://www.53zi.com/1593745-54254e96f4a43db7.jpg)
- 而普通对象是没有prototype属性的，在chrome上对象有一个\__proto__属性指向对象的原型，但是
在其他浏览器上这个属性对外完全不可见，要取得普通对象的原型对象，可以调用Object.getPrototypeOf(instance),便可取得实例instance的原型。

**总结：**JS中的每个对象都包含一个指向其原型对象的指针，这个指针在构造函数是**prototype属性**，可以直接访问；在普通对象中是\__proto__属性，不可以直接访问。

## 原型链
- 让一个函数的原型对象等于另外一个类型的实例，而这个实例的原型对象又等于另外一个类型的实例，如此层层递进，就构成了`实例与原型之间`的**一条链条**，这就是所谓原型链的概念。
若A继承自B，B又继承自C，则这条原型链为：`A的实例->A的原型对象->B的原型对象->C的原型对象->Object的原型对象->null`
- 所有引用类型的prototype指针默认都指向Object的原型对象，所有函数的默认原型对象的\__proto\__指针都指向Object的原型对象。
- 原型链是JS实现继承的主要方法。
- 如果实例A的\__proto__指针指向类型B的**原型对象**，可以直接指向，也可以间接指向（即A->C->B），则**称A是B的实例**。
- instanceof操作符
```
A instanceof B
//如果类型B的原型对象出现在实例A的原型链中，则返回true
```
- 作用域链和原型链
作用域链用来查找对象
原型链用来查找对象的属性

## 参考资料
1. [公司一位大牛关于prototype的总结](http://www.jianshu.com/p/c8f29b62fec8)
2. [理解js中的原型继承](http://fungwan.me/2015/01/05/js%E5%AD%A6%E4%B9%A0%E4%B9%8B%E5%8E%9F%E5%9E%8B%E7%BB%A7%E6%89%BF/)
