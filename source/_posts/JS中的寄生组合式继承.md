---
title: JS中的寄生组合式继承
categories: JavaScript
tags: [JavaScript, Prototype, 前端开发]
description: 寄生组合式继承是JS中最完美的继承方式
toc: true
date: 2016-05-27 22:10:46
---
寄生组合式继承，集寄生式继承和组合式继承的优点于一身，是JS中最完美的继承方式。
<!--more-->
## 原型式继承
- 实现方式
```JavaScript
SubType.prototype = new SuperType();
//SubType是子类，SuperType是超类
//将超类的一个实例赋给子类构造函数的原型
```
- 存在问题
子类对象将共享所有继承的属性和方法，一个子类实例更改一个继承的属性，这将会影响到所有的子类实例

## 组合继承
- 实现方式
```JavaScript
function SubType(){
    SuperType.call(this);
    /*在子类构造函数的内部调用超类的构造函数，
    这样就使的子类的每个实例都具有自己的属性，
    解决了原型式继承带来的问题*/
}
SubType.prototype = new SuperType();
```
- 存在问题
组合继承最大的问题就是在创建子类实例时，无论什么情况下，
都会调用**两次**超类的构造函数，这将会导致效率低下。
而且子类的原型对象和其实例会中有重复的属性，
解决方法是：不必为了指定子类的原型而调用超类的构造函数，
我们所需的不过就是超类原型对象的一个副本而已。

## 寄生组合式继承
- 实现方式
```JavaScript
function SubType(){
    SuperType.call(this);
    // 这和组合继承保持一样
}
function Extend(subType, superType){
    function FTemp(){};
    FTemp.prototype = superType.prototype;
    var temp = new FTemp();
    //创建一个临时中间对象
    temp.constructor = subType;
    //当为对象实例添加一个属性时，这个属性会屏蔽原型对象中的同名属性，
    //也就是说，添加的属性只会阻止我们访问原型中的那个属性，但不会修改那个属性
    subType.prototype = temp;
}
Extend(SubType, SuperType);
```
- 开发人员普遍认为寄生组合式继承是最理想的继承方式。

## 一个寄生组合式继承的完整例子
```JavaScript
function SuperType(name){
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
}
SuperType.prototype.sayName = function(){
    console.log(this.name);
}

function SubType(name, age){
    // 继承属性
    SuperType.call(this, name);
    this.age = age;
}
//继承方法
SubType.prototype = new SuperType();
SubType.prototype.constructor = SubType;
SubType.prototype.sayAge = function(){
    console.log(this.age);
}

var instance1 = new SubType('bruce', 23);
instance1.colors.push('black');
console.log(instance1.colors);
instance1.sayAge()
instance1.sayName();

var instance2 = new SubType('mike', 28);
console.log(instance2.colors);
instance1.sayAge()
instance1.sayName();

/*
以上是组合式继承。
组合继承最大的问题就是在创建子类实例时，无论什么情况下，
都会调用**两次**超类的构造函数，这将会导致效率低下。
而且子类的原型对象和其实例会中有重复的属性，
解决方法是：不必为了指定子类的原型而调用超类的构造函数，
我们所需的不过就是超类原型对象的一个副本而已。
修改方法如下：
*/
function Extend(subType, superType){
    function FTemp(){};
    FTemp.prototype = superType.prototype;
    var temp = new FTemp();
    //创建一个临时中间对象
    temp.constructor = subType;
    subType.prototype = temp;
}
Extend(SubType, SuperType);
SubType.prototype.sayAge = function(){
    console.log(this.age);
}
var instance3 = new SubType('cater', 25);
console.log(instance3.colors);
instance3.sayAge()
instance3.sayName();
```

## 参考资料
[JavaScript高级程序设计](https://book.douban.com/subject/10546125/)
