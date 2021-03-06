---
title: JS中的零碎知识
categories: JavaScript
tags: [JavaScript, 前端开发]
description: 记录一下JavaScript中的零碎小知识
toc: true
date: 2016-05-25 10:59:48
---
将持续更新
<!--more-->
1. 变量命名除了数字、字母、下划线外，新加入美元符号$
2. 变量类型为松散类型，即可以把任意类型的数据赋给一个变量，同一个变量可以反复赋值，而且可以是不同类型的数据，这种变量本身类型不固定的语言称为**动态语言**，比如Python，JS
3. `var关键字`：定义一个局部变量，定义变量时省略var关键字，将定义一个全局变量
4. 5种**基本类型**：`Undefined、Null、Boolean、Number、String`
5种**引用类型**:`Object、Array、Date、RegExp、Function`
 - Undefined：未定义变量或定义了但是未初始化
 - Null：**空对象指针**
 - Number：IEEE754格式表示，说明JS中的数字均是浮点数
 - String：同C一样，字符串是不可变的
 - Object：是所有实例的基础
5. JS中的操作符与其他语言不同的是，它们能够适用于很多值，例如String、Number、Boolean，甚至对象，**当一个操作符应用于不同类型的值时**,会先进行**类型转换**，再进行计算,**因为一个操作符两边操作数的类型要保持一致**,这和C一样
6. 逻辑与、逻辑或：在有一个操作数不是布尔值的情况下，**不一定返回布尔值**
7. 加法操作符：数值加法、字符串拼接，当有一个操作数是字符串时，即为字符串拼接
8. `==`和`===`:前者会进行强制类型转换，而后者不会进行类型转换
switch语句在比较值时使用的是全等操作符
9. JS中没有块级作用域
10. `with语句`提供了一种读取对象属性的快捷方式，但是在with语句中**并不能**给这个对象创建一个新的属性，
也就是说使用with设定的变量对象，是**只读的**，不可写，因为变量对象是用来解析标识符的。
并且大量使用with语句会导致性能下降，同时也会给代码调试造成困难。
```JavaScript
function buildUrl(){
    var qs = '?bebug=true';
    with(location){
        var url = href + qs;
    }

    return url;
    /*在with语句中定义的变量url，并没有成为location的一个属性，
    而是成为了函数执行环境的一部分*/
}
```
11. 解除引用
```JavaScript
var example = new Object();
example = null;
/* 一旦数据不再有用，最好通过将其值设置为null来释放其引用，这称为：解除引用
 * 解除一个值的引用并不意味着**立刻回收**该值所占用的内存，其真正的作用是
 * 让值脱离执行环境，以便垃圾收集器下次运行时将其回收.
 */
```
12. 基本包装类型
为了便于操作基本类型值，JS还提供了3个特殊的引用类型:**Boolean、Number、String**，称为基本包装类型。
实际上，**每当读取**一个基本类型值的时候，**后台就会自动创建**一个对应的基本包装类型的对象，从而让我们可以调用一些方法来操作这些数据。
而自动创建的基本包装类型的对象，则只存在于**一行代码的执行瞬间。**
```JavaScript
var s1 = "some text";
var s2 = s1.substring(2);
/*上面这句话(var s2 = s1.substring(2);)，后台会自动完成以下操作
 *var s1 = new String("some text");
 *var s2 = s1.substring(2);
 *s1 = null;
 *经过此番处理，基本的字符串值就变得跟对象一样了
 */
```
13. 作用域链和原型链
- 作用域链用来查找对象
- 原型链用来查找对象的属性
