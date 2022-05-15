---
title: 前端路由
date: 2020-06-11 18:05:10
tags:
---

## 什么是前端路由？

路由的概念来源于服务端，在服务端中路由描述的是 URL 与处理函数之间的映射关系。

在 Web 前端单页应用 SPA(Single Page Application)中，路由描述的是 URL 与 UI 之间的映射关系，这种映射是单向的，即 URL 变化引起 UI 更新（无需刷新页面）。

## 如何实现前端路由？

要实现前端路由，需要解决两个核心问题：

1.如何改变 URL 却不引起页面刷新？ 2.如何检测 URL 变化了？

### hash 实现

hash 是 URL 中 hash (#) 及后面的那部分，常用作锚点在页面内进行导航，改变 URL 中的 hash 部分不会引起页面刷新
通过 hashchange 事件监听 URL 的变化，改变 URL 的方式只有这几种：通过浏览器前进后退改变 URL、通过标签改变 URL、通过 window.location 改变 URL，这几种情况改变 URL 都会触发 hashchange 事件

### history 实现

history 提供了 pushState 和 replaceState 两个方法，这两个方法改变 URL 的 path 部分不会引起页面刷新
history 提供类似 hashchange 事件的 popstate 事件，但 popstate 事件有些不同：通过浏览器前进后退改变 URL 时会触发 popstate 事件，通过 pushState/replaceState 或标签改变 URL 不会触发 popstate 事件。好在我们可以拦截 pushState/replaceState 的调用和标签的点击事件来检测 URL 变化，所以监听 URL 变化可以实现，只是没有 hashchange 那么方便。

## hash 实现（简略版）

```

<body>
  <ul>
ref="">    <!-- 定义路由 -->
    <li><a href="#/home">home</a></li>
    <li><a href="#/about">about</a></li>

ref="">    <!-- 渲染路由对应的 UI -->
    <div id="routeView"></div>
  </ul>
</body>




// 页面加载完不会触发 hashchange，这里主动触发一次 hashchange 事件
window.addEventListener('DOMContentLoaded', onLoad)
// 监听路由变化
window.addEventListener('hashchange', onHashChange)

// 路由视图
var routerView = null

function onLoad () {
  routerView = document.querySelector('#routeView')
  onHashChange()
}

// 路由变化时，根据路由渲染对应 UI
function onHashChange () {
  switch (location.hash) {
    case '#/home':
      routerView.innerHTML = 'Home'
      return
    case '#/about':
      routerView.innerHTML = 'About'
      return
    default:
      return
  }
}
```
