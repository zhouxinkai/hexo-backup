---
title: 用单例模式来采集GA数据
toc: true
date: 2016-05-19 17:49:29
categories: [C++, 设计模式]
tags: [C++, 设计模式, HTTP]
description: [单例模式在项目中的运用]
---
单例模式在项目中的运用
<!--more-->
## GA简介 ##

GA是Google Analytics的简称，GA提供了非常强大的数据采集功能，我在这个博客中就引入了GA，你在这里看了我的那一篇文章，点了什么东西，我都能看到，如下图所示。
![这个博客的GA统计数据](http://www.53zi.com/ga_blog.png)
而且最重要的是，它是免费的，在网页中你只需要嵌入一小段JS脚本即可，但在C++中这样不行，但本质上来讲采集GA数据的过程是一个HTTP GET请求(可以从Chrome的开发者工具的Network面板中看到，也可以用WireShark抓包得到)。
所以在C++只要按照相应的格式发送HTTP GET请求就行了。
![发送GA事件](http://www.53zi.com/ga_request_true.png)
![GA响应](http://www.53zi.com/ga_response.png)
附：[GA开发者手册](https://developers.google.com/analytics/devguides/collection/analyticsjs/events)

## C++发送GA事件 ##

在项目中只用GA来追踪用户点击事件，简单来说一共有三个字段：
- category: 对应一张UI
- action: 对应上面这张UI中的一个button
- label: 可以是版本号或语言等等

由于项目中发送GA事件的频率很高，在很多地方都会用到，所以第一时间想到采用**单例模式(Singleton)**,Singleton有两个特点：
- 实例化控制：保证一个类只有一个实例
- 全局访问：提供了一个全局访问点

![单例模式结构图](http://www.53zi.com/singleton.png)

很显然单例模式用在这里很合适，核心code如下：
```C++
class GAEvent
{
public:
    ~GAEvent(void);
    static GAEvent * GetInstance()
    /*此方法是获得本类实例的唯一全局访问点
    注意到它是一个静态方法*/
    {
        if (NULL == m_instance)
        {
            boost::lock_guard<boost::mutex> lock(m_InstanceMutex);
            if (NULL == m_instance)
            /*考虑到多线程时可能会创建多个实例
            所以采用了双重锁定来避免这个问题*/
            {
                m_instance  = new GAEvent();
            }
        }
        return m_instance;
    }
private:
    GAEvent(void);
    /*构造方法为private，这就堵死了外界利用
    new创建此类实例的可能*/
}
GAEvent *gaEvent = GAEvent::GetInstance();
```

###  懒汉式与饿汉式 ###
- 懒汉式：在类被加载时就将自己实例化。
```C++
class SingletonStatic  
{  
private:  
    static const SingletonStatic* m_instance;  
    SingletonStatic(){}  
public:  
    static SingletonStatic* getInstance()  
    {  
        return m_instance;  
    }  
};  
  
//静态变量是在main函数调用之前就完成初始化的
const SingletonStatic* SingletonStatic::m_instance = new SingletonStatic; 
```
- 饿汉式:上面的方式就是饿汉式单例类，即要在第一次引用时，才会将自己实例化。

##  其它 ##
- 在项目中调用nmap相关的方法时也用到了单例模式的地方,UI上大部分的数据都来源于nmap
- 改进：应用解释器模式
   **解释器模式**的应用场景：如果一种**特定类型的问题发生的频率足够高**，那么就值得将该问题的各个实例表述为一个简单语言中的句子。这样就可以构建一个解释器，该解释器通过解释这些句子来解决该问题。
  上面说到GA事件有三个字段：category、action、label，由于我把一个button的GA事件放到了HTML标签的id属性中，所以指定一个固定的格式很重要，而且这个问题的频率很高，最后我指定了如下格式：feedback_bad &lt;label&gt;too slow&lt;/label&gt;
  在上述string中，category为feedback，action为bad，label为too slow