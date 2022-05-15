---
title: dom0-dom2
date: 2020-06-15 18:38:20
tags: dom0 dom2
categories: 前端
---

## dom0

或者叫 0 级 dom ，如下

```
<input id="btn" type="button" onclick="console.log('You clicked the button!');" value="Click" />

//  或者js写法
document.getElementById('btn').onclick = function() {
            console.log('Ｉ　am ｐｒｏｃｅｓｓｅｄ by dom0!');
        };
```

### 这种写法，后面的代码会覆盖前面的，也就是说如果多次输出，也只会输出最后一条。

## dom2

或者叫 2 级 dom，继续如下呗

```
   <input id='input' type="text" onclick="alert('haha')" />
    <script>
        document.getElementById('input').onclick = function() {
            alert('fff')
        }; // 目标阶段
        document.getElementById('input').addEventListener('click', function() {
            alert('mmm')
        }, true)  // 捕获阶段
        document.getElementById('input').addEventListener('click', function() {
            alert('ooooo')
        }, true)  // 捕获阶段
        document.getElementById('input').addEventListener('click', function() {
            alert('pppp')
        }, false)  // 冒泡阶段

        // 兼容低版本IE的写法
        document.getElementById('input').attachEvent("onclick", function() {
            alert('pppp')
        });
    </script>

    // mmm ooooo fff pppp
```

dom2 把事情分为 3 步 ———— 事件捕获阶段、处于目标阶段和事件冒泡阶段

```
addEventListener('click',function(){},true/false)

// 第一个参数，表明是啥触发事件
// 第二个参数，事件触发以后的回调函数
// 第三个参数，true或者false ，true是第二个参数那个回调函数在捕获阶段调用 ，false在冒泡阶段 ，默认是false
// 2015年底，扩展了第三个参数

el.addEventListener(type, listener, {
    capture: false, // useCapture
    once: false,    // 是否设置单次监听
    passive: false  // 是否让阻止默认行为preventDefault()失效

    //  由于 touchmove 事件对象的 cancelable 属性为 true，也就是说它的默认行为可以被监听器通过 preventDefault() 方法阻止。那它的默认行为是什么呢，通常来说就是滚动当前页面（还可能是缩放页面），如果它的默认行为被阻止了，页面就必须静止不动。但浏览器无法预先知道一个监听器会不会调用 preventDefault()，它能做的只有等监听器执行完后再去执行默认行为，而监听器执行是要耗时的，有些甚至耗时很明显，这样就会导致页面卡顿。即便监听器是个空函数，也会产生一定的卡顿，毕竟空函数的执行也会耗时。当设置了passive为true，则会忽略代码中的preventDefault(), 因此页面会变得更流畅

})


```

window => document => body => div 捕获

div => body => document => window 冒泡

### 这种写法，不会覆盖，会依次输出所有项

顺便提一嘴 dom3 ，增加了更多的事件类型，比如：
       DOM3 级事件在 DOM2 级事件的基础上添加了更多的事件类型，全部类型如下：

UI 事件，当用户与页面上的元素交互时触发，如：load、scroll

焦点事件，当元素获得或失去焦点时触发，如：blur、focus

鼠标事件，当用户通过鼠标在页面执行操作时触发如：dbclick、mouseup

滚轮事件，当使用鼠标滚轮或类似设备时触发，如：mousewheel

文本事件，当在文档中输入文本时触发，如：textInput

键盘事件，当用户通过键盘在页面上执行操作时触发，如：keydown、keypress

合成事件，当为 IME（输入法编辑器）输入字符时触发，如：compositionstart

变动事件，当底层 DOM 结构发生变化时触发，如：DOMsubtreeModified

同时 DOM3 级事件也允许使用者自定义一些事件。

## 总结

区别：dom0 会覆盖，dom2 不会覆盖。
