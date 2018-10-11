---
title: JS小实践之项目官网开发
categories: JavaScript
tags: [JavaScript, 前端开发]
description: 俗话说光说不练假把式，知道和会差别是很大的，技术只有经过项目的考验，踩过无数的坑之后，方可说熟练运用。
toc: true
date: 2016-06-03 16:50:27
---
俗话说光说不练假把式，知道和会差别是很大的，技术只有经过项目的考验，踩过无数的坑之后，方可说熟练运用。正好最近项目要做个网站，作为我们产品的官网，项目组就我一个开发，还有一个architecture，人家自然看不上去写网站，所以就交给我了，恰好我很乐意。
<!--more-->
### 技术选型
由于领导不想在这个网站上花费太多时间，他们的重点都在桌面，所以我就选了**Bootstrap+原生JS+Jinjia2模板+Python Flask**。
- Bootstrap不必说，中规中矩的一个CSS框架
- 由于网站也没什么太多交互，原生JS就能直接搞定
- Jinjia2模板，好用易学，用它写HTML很方便，可以将页脚和页眉都写进父模板中去
- Flask，作为Python的一个轻量级Web框架，在[Github上排名](https://github.com/showcases/web-application-frameworks)很不错，用在这里绰绰有余

### 踩到的坑
#### CORS跨域
XHR（XMLHttpRequest）作为负责Ajax（Asynchronous JavaScript and XML，异步JS和XML）运行的核心对象，最早由微软在IE5中引入，用于通过JS从服务器取得XML数据，在此之后，各大浏览器都实现了相同的特性，使XHR成为了Web的一个标准。
同源策略是对XHR的一个主要约束，它为通信设置了**同域、同端口、同协议**的限制，默认情况下当使用XHR访问上述限制之外的资源，浏览器会直接驳回跨域请求，并引发错误，触发error事件，此时请求不会到达服务器。
W3C定义了CORS的实现标准：当发送请求时，在HTTP请求头中附加一个额外的**Origin字段：告诉服务器，我这个请求是从哪里来的**，然后服务器根据这个头部来判断要不要接受这个请求，如果同意，就在**Access-Control-Allow-Origin响应头部中，回发相同的Origin源信息**（如果是公共资源，可以回发`*`），然后浏览器会检查这个响应头部，如果没有这个头部或者有这个头部但源信息不匹配，浏览器就会驳回请求，并触发error事件。
各大浏览器也根据W3C的这个标准，实现了CORS，但限制很多，比如请求和响应都不包含cookie，**不能自定义请求头部**，坑就在这里，这次我要发送表单到项目组的另一个server，这显然不同源，最重要的是那个server要使用4个自定义的头部，所以即使跨域也不行，我最后的解决方案是把我自己的server作为中介，请求先到我的server，再到项目组的server，响应也经过我的server再到达浏览器。

#### 生成XML数据
因为提交给项目组server的数据格式是XML，这个在很早之前他们就定好的，所以我这边要把表单数据转成XML格式，这个还好，是个小坑

#### 发送GA事件
本来想在每个能点的地方都加上click事件，后来想了想，直接把click事件加在document对象上，利用事件冒泡，再利用自定义的ga_info属性就可以了

#### 如何判断是否点击了整个button
![](http://www.53zi.com/%E6%8C%89%E9%92%AE.png)
在这个案例中我只有两个这样的button，但我觉得这是一个普遍现象，是在每个button都加上click事件，还是在两个button的父元素上只加**一个事件**，我的看法是后者更好，理由如下：首先每个函数都是对象，都会占用内存，内存中的对象越多，性能就越差；其次，必须事先指定所有事件处理函数而导致的DOM访问次数，会延迟整个页面的交互就绪时间。
第二种方法虽好，但这里有一个坑，就是如果直接在父元素的click事件处理函数里判断button的id属性，会导致一个问题，就是当我点击button里的图片或文字时，会没有反应，原因是click事件一直被传播到了button里的图片或文字，然后再往上冒泡，但此时`event.target`明显不是整个button，判断id属性自然会出错。
所以必须判断`event.target`是不是button的子元素，方法如下
```JavaScript
function isParent (obj,parentObj){
    while (obj != undefined && obj != null && obj.tagName.toUpperCase() != 'BODY'){
        if (obj == parentObj){
            return true;
        }
        obj = obj.parentNode;
        //这里用到了DOM的parentNode属性，指向父元素
    }
    return false;
}
```
如果`event.target`为button的子元素，我们就认为整个button被点击了，然后再处理

#### deploy
部署采用nginx+flask，部署在AWS上，在这个小项目中，前端很简单，后端就更加不用提了。
[访问网址，有兴趣的话，可以下载我们的产品跑一跑，这也是我一直在负责的产品，如果能帮到你，万分荣幸。](http://devicesecurity.trendmicro.com/)

### 展望
项目虽小，但还算可以，权当练手，本来还想加点效果的，但领导一直强调不要在网站花费太多的时间，比如表单输入时，没有内容时submit按钮为灰，有内容时才可以提交，这可以用文本输入事件来实现，技术有的时候是相通的，在我们的桌面产品当时实现类似的功能时，也是采用文本输入事件，在没有别人提醒时，我开始还开了两个线程去判断文字有没有改变，被他们笑了好几天，囧。。。

