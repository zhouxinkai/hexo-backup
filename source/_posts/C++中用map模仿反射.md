---
title: C++中用map模仿反射
toc: true
date: 2016-05-19 11:24:49
categories: [C++, 设计模式]
tags: [C++, 设计模式]
description: [反射反射，程序员的快乐]
---
反射反射，程序员的快乐
<!--more-->
## 前言 ##
从去年7月份进入公司来，一直负责一款局域网设备扫描检测客户端的前端开发工作，技术解决方案是采用轻量级的HTML渲染引擎HTMLayout，并在其的基础上用C++开发了一套类似jQuery的前端框架，用来支持DOM操作，主要功能是将服务器的返回数据和Nmap的扫描结果展现出来，并做好Google Analytics数据采集。
这个库是公司一个前辈开发的，前阵子刚好有时间，周末抽了两天研究了一下，真的是写的太好了，尤其是那个创建不同CHlWindow对象的map，从来没有看过这么好的code啊，我们在学校写的都是垃圾啊
**注：**HTMLayout是一个免费的开源界面库，以DLL方式运行，并提供一些API，其相当于一个轻量级的HTML渲染引擎，可以高效快速的解析和渲染HTML文档。

## UML类图 ## 
这个库的核心思想是采用**策略模式**，首先我们有很多张UI（比如Eula、Homepage等等），从概念上看这些UI具有相同的操作，比如Open、Close、Reload、EventHandle等等，不同的是这些操作对于不同的UI实现起来是不一样的。然后对这些UI抽象出一个**接口**，在窗口管理类CWindowMgrImpl中通过这个接口可以很方便的操作这些UI。
**策略模式**
- 概念：策略模式是一种定义一系列算法的模式，所有这些算法完成的都是相同的工作，只是实现不同，比如打折、返利都是一种收费方式。
- 应用场景：策略模式就是用来封装算法的，但在实践我们发现可以用它来封装几乎**任何类型的规则**，只要在分析过程中听到需要在不同时间运用不同的业务规则，就可以考虑用策略模式来处理这种变化。

这个库的UML类图如下： 

![UML类图][1]

## 核心代码 ##

- 问题的关键是如何在窗口管理类CWindowMgrImpl中创建不同的UI对象（比如HomepageUI对象），用case条件语句来判断似乎不是一个很好的办法，C#中有**反射**的概念，在这里我们借助map来模仿反射，关键代码如下：
```C++
typedef TmCommon::wstring WindowType;

class CWindowMgrImpl
{
public:
    std::map<WindowType, std::shared_ptr<IHlWindowCtrller>> m_mapWndCtrl;
}
template <typename T>
static std::shared_ptr<IHlWindowCtrller> CreateHlWindow
(const TmCommon::wstring& wstrId)
{
    return std::shared_ptr<IHlWindowCtrller>(std::make_shared<T>(wstrId));
    /*std::shared_ptr称为智能指针，其采用引用计数，是防止内存泄露的神器。
     *std::make_shared用于创建一个对象。
     *所以上面这个表达式的意思是创建一个对象，并返回一个指向这个对象的智能指针。
     */
}

typedef std::shared_ptr<IHlWindowCtrller> (*FP_CreateHlWindow)
(const TmCommon::wstring& wstrId);
/*定义一个函数指针*/
typedef std::map<WindowType, FP_CreateHlWindow> HlWindowCreatorMap;

class CHlWindowFactory
{
friend class CWindowMgrImpl;
private:
    static HlWindowCreatorMap s_mapCreator;
public:
    CHlWindowFactory(const WindowType& type, FP_CreateHlWindow)
    {
        s_mapCreator[type] = fp;
    }
};

#define ADD_HLWINDOW_CREATOR(windowType)\
    CHlWindowFactory temp##windowType(L#windowType, CreateHlWindow<windowType>)

bool CWindowMgrImpl::OpenHlWindow
(const WindowType& type, const WindowId& id, void* pParam)
{
    boost::lock_guard<boost::mutex> lock(m_mutex);
    // already exist this window id 
    if (m_mapWndCtrl.find(id) != m_mapWndCtrl.end())
    {  
        ::ShowWindow(m_mapWndCtrl[id]->GetHWnd(), SW_SHOWNORMAL);
        SetForegroundWindow(m_mapWndCtrl[id]->GetHWnd());   
        return true;
    }

    auto itr = CHlWindowFactory::s_mapCreator.find(type);
    if (itr == CHlWindowFactory::s_mapCreator.end())
    {
        // the type does not exist
        return false;
    }

    std::shared_ptr<IHlWindowCtrller> pCtrler = itr->second(id);
    /*创建这个UI对象.*/
    pCtrler->SetWindowMgr(this);
    HWND hWnd = pCtrler->Open(pParam);
    if (NULL != hWnd)
    {
        m_mapWndCtrl[id] = pCtrler;
    }

    return true;
}

/*1.这样在HomepageUI中就可以用上面这个宏，比如ADD_HLWINDOW_CREATOR(HomepageUI);
 *2.调用mgr.OpenHlWindow(L"HomepageUI", L"HomepageUI");
 *  mgr是CWindowMgrImpl的实例
 *  就可以成功创建并打开一个窗口了.*/
```


[1]:http://www.53zi.com/image/jpg/TmHtmlayout_UML.png



