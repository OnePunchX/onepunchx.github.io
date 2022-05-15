---
title: event.stopPropagation()和event.preventDefault()
date: 2020-06-17 11:32:33
tags: 阻止事件冒泡 阻止默认事件
---

### 硬核整活。就 3 个玩意儿：

1. event.stopPropagation() 阻止冒泡

2. event.preventDefault() 阻止默认事件

3. return false 同时阻止冒泡和默认事件

### 阻止事件冒泡，能咋滴？

el.addEventListener(”click“,function(){console.log('xxx')},false) 接受第二个参数是个回调函数吧？函数里有操作吧，有干活吧？
这个阻止冒泡，就阻止这个回调函数干活。说的是冒泡奥，捕获不阻止的奥。

### 阻止默认事件，能咋滴？

首先你得知道啥是默认事件，你知不知道？
不知道我告诉你呗，反正也是我百度的。职业复制粘贴工程师，给你复制一下：
就是浏览器通过 HTML 标签或 DOM 元素提供的一些功能性的默认行为。
比如在 a 标签 href 属性上的跳转，右键呼出的菜单，当你在一个 form 表单里点击提交按钮时网页会产生提交行为并刷新网页，当你网页上滚动鼠标滚轮时，网页的滚动条会动。
阻止默认事件就能阻止类似这些事

### 同时阻止，能咋滴？

不能咋滴，你 return false 🐂🍺

## 总结

就这两行玩意，不总结了奥。
