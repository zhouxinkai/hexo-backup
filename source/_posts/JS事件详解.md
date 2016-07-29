---
title: JS事件详解
categories: JavaScript
tags: [JavaScript, 前端开发， 事件]
description: 事件作为JS的一个重要组成部分，搞定它的意义毋庸置疑。
toc: true
date: 2016-06-04 16:11:35
---
事件作为JS的一个重要组成部分，搞定它的意义毋庸置疑。
<!--more-->
## 事件流的三个阶段
> Netscape(网景)和IE曾经为了取得浏览器的控制权，网景主张捕获方式，微软主张冒泡方式。后来 W3C 采用折中的方式，制定了统一的标准——先捕获再冒泡，这个过程具体如下：

1. 捕获阶段
即在事件到达预定目标之前捕获它，低版本的IE不支持捕获阶段
2. 目标阶段
事件到达预定目标，其被看成是冒泡阶段的一部分
3. 冒泡阶段
事件被冒泡至window对象，所有现代浏览器都支持事件冒泡

**注：**大多数浏览器都是从`window对象`开始捕获，然后冒泡至`window对象`；
建议通常都使用事件冒泡，只有在特殊情况下才使用事件捕获。

## 绑定事件和解绑事件
### W3C
- 绑定事件： `target.addEventListener(eventType, listener, bIsUseCapture)`
eventType: 事件类型，比如`click`
listener: 事件触发时执行的函数（事件处理程序或事件侦听器）
bIsUseCapture： 表示是否在捕获阶段调用事件处理函数，如果为false（默认为false），表示在冒泡阶段调用listener
- 解绑事件: `target.removeEventListener(eventType, listener, bIsUseCapture)`
参数意义同上

### IE
- 绑定事件： `target.attachEvent(listenerName, listener)`
listenerName: 事件处理程序的名字，以"on"开头，比如`onclick`
listener: 同W3C
- 解绑事件： `target.detachEvent(listenerName, listener)`
参数意义同上

### 不同
1. 事件处理程序的作用域
W3C对应的作用域是事件所依附的目标元素，即`this`指向当前元素
而IE对应的作用域是全局作用域，即`this`指向window对象
2. 多个事件处理程序的触发顺序
W3C按照添加它们的前后顺序触发，即先添加的先触发
而IE恰好相反，先添加的后触发
3. 事件处理程序的阶段
W3C即可以添加到冒泡阶段，也可以添加捕获阶段
而IE只能添加到冒泡阶段

### 相同
对于绑定事件和解绑事件，二者都要求解绑时的参数要与绑定时的参数**相同**，这意味着添加的匿名事件处理程序将没法被移除，因为所有声明的匿名函数都是一个新函数，即使二者代码完全一样。

### 原生JS实现事件绑定函数
其接口是`target.addEventListener(eventType, listener)`
核心需求就是可以对某一个事件名称绑定多个事件响应函数，大致实现思路就是创建一个类，其中有两个函数，一个 `bind`一个`trigger`，分别实现绑定事件和触发事件，然后当触发这个事件名称时，依次按绑定顺序触发相应的响应函数。具体做法是在bind和trigger函数外层作用域创建一个`字典对象`，用于存储注册的事件及响应函数列表，bind时，如果字典没有则创建一个，key是事件名称，value是数组，里面放着当前注册的响应函数，如果字段中有，那么就直接push到数组即可。trigger时调出来依次触发事件响应函数即可。
其中key和value的关系其实是`C#中的委托`，C#中的委托是一个二级指针，指向一个函数指针数组（当然这些函数必须具有相同的参数列表和返回值，不一样还瞎掺和啥），这个数组中的每一个元素都是一个函数，当调用委托时，数组中的函数将被依次唤起。
下面是一个简单的例子
```JavaScript
//有待完善
function attachEvent(eventType, listener){
    var objDelegate;
    var that = this;

    function bind(eventType, listener){
        if(objDelegate[eventType] instanceof Array){
            objDelegate[eventType].push(listener);
        }else{
            objDelegate[eventType] = [];
            objDelegate[eventType].push(listener);
        }
        that.objDelegate = objDelegate;
    }

    function trigger(){
        var args = [];
        for(var i=0; i<arguments.length; i++){
            args.push[arguments[i]];
        }
        var listeners = that.objDelegate[eventType];
        for (var index in listeners){
            listeners[index].apply(that, args);
            //listeners.shift();
        }
    }
}
```

## 事件代理/委托
> 如果你单击了某个button，JS认为单击事件不仅仅发生在button上，换句话说，在单击button的同时，你也单击了button的父元素，甚至也单击了整个页面。

JS的事件委托靠事件冒泡来实现
### 优点
- 内存占用少： 因为每个事件处理函数都是对象，都会占用内存
- DOM访问次数少： 加快页面的交互就绪时间，因为不管什么时候，只要是访问DOM树中的一个元素，浏览器都会搜索整个DOM树，从中查找匹配的元素
- 动态绑定事件： 可以实现当新增子对象时无需再次对其绑定事件，对于动态内容部分尤为合适

### 缺点
事件代理的应用应该仅限于上述需求下，如果把所有事件都用代理就可能会出现事件误判，即本不该绑定事件的元素被绑上了事件。

### 原生JS实现事件委托
> 首先说一下事件对象event的常用属性
> 1.target（在IE中为srcElement）属性： 指向事件的实际目标元素
> 2.currentTarget（IE中没有）属性： 事件处理函数的作用域
> 3.type属性： 被触发的事件的类型

```JavaScript
/*简单的事件委托，思路如下
 *1.事件处理程序是绑定在父元素上的
 *2.事件对象event的target属性（在IE中为srcElement属性）
 *  是指向实际目标元素的
 *3.当实际目标元素等于给定元素时，才调用真实的事件处理函数 
**/
function delegateEvent(selector, eventType, listener){
    var delegator = this;
    if (delegator.addEventListener){
        delegator.addEventListener(eventType, listenerFunc, false);
    } else if(delegator.attachEvent){
        delegator.attachEvent("on"+eventType, listenerFunc);
    } else {
        delegator["on"+eventType] = listenerFunc;
    }

    // 封装实际的事件处理函数
    function listenerFunc(event){
        var event = event || window.event;
        var target = event.target || event.srcElement;

        if(target.matchSelector(selector)){
            if(listener){
                listener.call(target, event);
            }
        }
    }

    // 判断实际目标元素是不是事先给定的元素
    function matchSelector(selector){
        var targetElement = this;
        
        if (selector.charAt(0) === "#"){
            return targetElement.id = selector.slice(1);
        }

        if (selector.charAt(0) === "."){
            return (" " + targetElement.className + " ").indexOf(
                " " + selector.slice(1) + " ") != -1;
        }

       return targetElement.tagName.toLowerCase() === selector.toLowerCase();
    }

}

// 调用
var outerDiv = document.getElementById("outerDiv");
outerDiv.delegateEvent("#innerDiv", "click", function(event){
    console.log(event.target.id);
})
```

## 模拟事件
事件通常由用户操作或通过浏览器的某些功能来触发，但JS也能在任意时刻手动的触发特定事件。
这被称为事件模拟，也被称为事件派发或事件广播，在`测试Web应用程序`时，模拟触发事件是一种极其有用的技术。
一般来说模拟触发一个事件要经过以下3步：
1. 创建一个事件对象event
2. 对其进行初始化
3. 调用`dispatchEvent`（IE下为fireEvent方法）触发这个事件

在这里就不详细介绍了，留一个印象即可。

## 其他
那么接下来就是熟悉各种类型的事件，比如click、文本改变事件等等各种事件。
少年，踩坑去吧，看好你...


