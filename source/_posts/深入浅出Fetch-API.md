---
title: 深入浅出Fetch API
categories: JavaScript
tags: [JavaScript, Fetch]
description: Fetch作为Ajax的替代者，接口简洁、功能强大
toc: true
date: 2017-01-11 10:44:39
---
之前写了一个小项目，踩了一些Fetch的坑，记录在此，方便以后查看
<!--more-->

## 完整示例
```JavaScript
const url = URL.format({
  pathname: '/api/add',
});
/* URL是url模块，可以很方便的处理url，提供了两个方法: format、parse
 * format: 将对象变成字符串，parse: 将字符串变成对象
 * 相似的一个模块叫querystring，专门处理查询字符串，也提供了两个方法: stringify、parse
**/
const headers = new Headers({
  `Content-Type`: "application/json"
});
const request = new Request(url, {
  method: 'POST',
  mode: 'cors',
  // 该属性决定了是否可以跨域，但成功与否还要看服务器的支持
  credentials: 'include',
  // 该属性决定了请求是可以否带cookie
  headers,
  body: JSON.stringify(body),
  // body不能是一个对象
});
const response = await fetch(request);
const body = await response.json();
```
Fetch引入了`3个接口: Headers, Request, Response`,它们直接对应HTTP中的相应概念，下面将依次介绍。

## Headers
### 常用方法
Headers接口是一个简单的键值对，Request接口中的headers参数就可以直接用一个对象来代替，但它和普通对象的不同在于它提供了以下一些方法,且大多方法的返回值是可以被`for of`迭代的，使得操作Header更为方便 ![Headers接口的方法](http://7xtj85.com1.z0.glb.clouddn.com/Fetch-Headers%E6%8E%A5%E5%8F%A3.png)

### guard属性
在Headers对象中有一个`guard` 属性，来指定其是否可以被改变，其有以下5种取值。
- `none`：默认值
- `request`：Request.headers 对象只读
- `request-no-cors`：在 no-cors 模式下，Request.headers 对象只读
- `response`：Response.headers 对象只读
- `immutable`：通常在 ServiceWorkers 中使用，所有 Header 对象都为只读

## Request
其有`两个参数`，第一个参数是`URL(字符串)`，其他参数都通过第二个参数(对象)传递，下面将详细说明第二个参数中的相应字段。
### mode
mode参数用来决定是否允许跨域请求，以及哪些response属性可读。可选的mode值为 `same-origin`、`no-cors`（默认）以及 `cors`。
- `same-origin`: 请求遵守同源策略
- `cors`: 请求遵守CORS协议，并只有有限的一些 Header 被暴露给 Response 对象，但是 body 是可读的,虽然允许跨域，但成功与否，还要看服务器支持，在服务器上可以设置`Access-Control-Allow-Origin`头，允许所有的话，可以设置为`*`。
- `no-cors`:  该模式允许来自 CDN 的脚本、其他域的图片和其他一些跨域资源，但是首先有个前提条件，就是请求的 method 只能是HEAD、GET 或 POST,另外JS不能访问 Response 对象中的任何属性。

### credentials
该属性与XHR中的`withCredentials`相同，决定了是否可以跨域访问cookie，但是有三个值：`omit`(默认), `same-origin`, `include`.
比如你要跨域访问一个服务器，并要携带cookie，那就要把这个属性设置为`include`。

[官方文档关于Request的介绍很详细](https://fetch.spec.whatwg.org/#request-class)

## Response
Response 对象通常在 fetch() 的回调中获得，也可以通过 JS 构造，不过这通常只在 ServiceWorkers 中使用。
其常用属性有`status`, `statusText`, `headers`, `type`，`body`，下面分别介绍。
`status`, `statusText`, `headers`和HTTP保持一致。

### type
`type`属性的值可能有: `basic`, `cors`, `default`, `opaque`。
- `basic`：同域的响应，除 Set-Cookie 和 Set-Cookie2 之外的所有 Header 可用
- `cors`：Response 从一个合法的跨域请求获得，某些 Header 和 body 可读
- `error`：网络错误。Response 对象的 status 属性为 0，headers 属性为空并且不可写。当 Response 对象从 Response.error() 中得到时，就是这种类型
- `opaque`：在 `no-cors` 模式下请求了跨域资源。依靠服务端来做限制

### body
`body`的类型只能是以下几种的一个
- ArrayBuffer
- ArrayBufferView
- Blod/File
- string
- URLSearchParams
- FormData

`注意并没有对象，这意味着如果要传一个对象的话，必须先把它stringify,然后把Content-Type设置为application/json`

Fetch还针对以上类型提供了对应的方法，将body从Request或Response中取出来，这些方法都返回一个使用实际内容 resolve 的 Promise 对象。
- arrayBuffer()
- blod()
- json()
- text()
- formData()

#### body只能被读取一次
非常重要的一点是，Request 和 Response 的 body 只能被读取一次！它们有一个属性叫 `bodyUsed`，读取一次之后设置为 true，之后就不能再被读取了。
这样设计的目的是为了之后兼容基于流的 API，我们的目的是当数据到达时就进行相应的处理，这样就使得 JavaScript 可以处理大文件例如视频，并且可以支持实时压缩和编辑。
那么，如何让 body 能被多次读取呢？API 为这两个对象提供了一个 `clone()` 方法。调用这个方法可以得到一个克隆对象，对象中包含全新的 body。不过要记得，clone() 必须要在使用 body 之前调用，也就是先 clone() 再读使用。

## serviceWorker
在 Fetch 规范中对 API 进行了定义，它结合 ServiceWorkers，尝试做到如下优化：改善离线体验,保持可扩展性。
serviceWorker作为一个新的API，可以拦截HTTP请求，通过编程的方式去控制HTTP缓存，还可以做到提前加载，加快首屏渲染速度。

## 参考资料
[fetch API 简介](http://bubkoo.com/2015/05/08/introduction-to-fetch/)
[官方文档](https://fetch.spec.whatwg.org/#request-class)
[fetch polyfill](https://github.com/github/fetch)

