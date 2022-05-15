---
title: 读《你不知道的javascript》
date: 2020-07-30 18:18:09
tags: javascript
categories: 前端
---

## 上卷

### 作用域

作用域是能够储存变量当中的值，并且能在之后对这个 值进行访问或修改。

#### 理解作用域

- 引擎

从头到尾负责整个 javascript 程序的变异及执行过程

- 编译器

引擎的好朋友之一，负责语法分析及代码生成等脏活累活

- 作用域

引擎的另一个好朋友，负责手机并且维护由所有生命的标识符（变量）组成的一系列查询，并实施一套非常严格的规则，确定当前执行的代码对撰写标识符的访问权限。

举个栗子 🌰

```
function foo(a) { console.log( a ); // 2
}
foo( 2 );
```

引擎:我说作用域，我需要为 foo 进行 RHS 引用。你见过它吗?
作用域:别说，我还真见过，编译器那小子刚刚声明了它。它是一个函数，给你。
引擎:哥们太够意思了!好吧，我来执行一下 foo。
引擎:作用域，还有个事儿。我需要为 a 进行 LHS 引用，这个你见过吗?
作用域:这个也见过，编译器最近把它声名为 foo 的一个形式参数了，拿去吧。
引擎:大恩不言谢，你总是这么棒。现在我要把 2 赋值给 a。
引擎:哥们，不好意思又来打扰你。我要为 console 进行 RHS 引用，你见过它吗?
作用域:咱俩谁跟谁啊，再说我就是干这个。这个我也有，console 是个内置对象。 给你。
引擎:么么哒。我得看看这里面是不是有 log(..)。太好了，找到了，是一个函数。
引擎:哥们，能帮我再找一下对 a 的 RHS 引用吗?虽然我记得它，但想再确认一次。
作用域:放心吧，这个变量没有变动过，拿走，不谢。
引擎:真棒。我来把 a 的值，也就是 2，传递进 log(..)。
......

```
var a = 2
```

1. 首先，var a 在其作用域中声明新变量。这会在最开始的阶段，也就是代码执行前进行。
2. 接下来，a = 2 会查询(LHS 查询)变量 a 并对其进行赋值。

块级作用域
try catch 语句里面 catch 部分是块级作用域
let 可以在任意作用域中，通常是函数内部。可以当做块级变量～但是没有声明提升
const 也一样

### 编译器

包括变量和函数在内的所有声明都会在任何代码被执行前首先 被处理。

当你看到 var a = 2; 时，可能会认为这是一个声明。但 JavaScript 实际上会将其看成两个 声明:var a;和 a = 2;。
第一个定义声明是在编译阶段进行的。第二个赋值声明会被留在 原地等待执行阶段。

声明变量提升

#### 函数优先

```
foo(); // 1
var foo;
function foo() {
   console.log( 1 );
}
foo = function() {
   console.log( 2 );
};
// 会输出 1 而不是 2 !这个代码片段会被引擎理解为如下形式:
function foo() {
  console.log( 1 );
}
foo(); // 1
foo = function() {
  console.log( 2 );
};

```

**小结：**
我们习惯将 var a = 2;看作一个声明，而实际上 JavaScript 引擎并不这么认为。它将 var a
和 a = 2 当作两个单独的声明，第一个是编译阶段的任务，而第二个则是执行阶段的任务。
这意味着无论作用域中的声明出现在什么地方，都将在代码本身被执行前首先进行处理。 可以将这个过程形象地想象成所有的声明(变量和函数)都会被“移动”到各自作用域的 最顶端，这个过程被称为提升。

### this

为什么用 this。 简洁 ，复用性好

我觉得关于 this 真的没什么好总结的，就一句话就完事：
this 实际上是在函数被调用时发生的绑定，它指向什么完全取决于函数在哪里被调用。

### 属性描述符

说实在的 这玩意我在红宝书里看过的

```
var myObject = { a:2
};
     Object.getOwnPropertyDescriptor( myObject, "a" );
     // {
// value: 2,
// writable: true,
// enumerable: true,
// configurable: true // }


var myObject = {};
     Object.defineProperty( myObject, "a", {
         value: 2,
writable: true, configurable: true, enumerable: true
     } );
     myObject.a; // 2

//  禁止一个对象添加新属性并且保留已有属性
        Object.preventExtensions( myObject );
     myObject.b = 3;
     myObject.b; // undefined

```

Object.seal(..) 会创建一个“密封”的对象，这个方法实际上会在一个现有对象上调用 Object.preventExtensions(..) 并把所有现有属性标记为 configurable:false

Object.freeze(..) 会创建一个冻结对象，这个方法实际上会在一个现有对象上调用 Object.seal(..) 并把所有“数据访问”属性标记为 writable:false，这样就无法修改它们 的值

for in 遍历对象中所有可枚举属性
for of 遍历对象{key:value}中的 value

构造函数 原型啥的不写了

## 中卷

类型
值
原生函数
强制类型转换
语法
。。。又是 300 多页
我挑感兴趣的看吧

鸭子类型： 当一个东西长得像鸭子，叫声也像鸭子，那么它就是个鸭子

从 JavaScript 主程序(或另一个 Worker)中，可以这样实例化一个 Worker: var w1 = new Worker( "http://some.url.1/mycoolworker.js" );
这个 URL 应该指向一个 JavaScript 文件的位置(而不是一个 HTML 页面!)，这个文件将 被加载到一个 Worker 中。然后浏览器启动一个独立的线程，让这个文件在这个线程中作 为独立的程序运行。

Worker 之间以及它们和主程序之间，不会共享任何作用域或资源，那会把所有多线程编程 的噩梦带到前端领域，而是通过一个基本的事件消息机制相互联系。

Worker w1 对象是一个事件侦听者和触发者，可以通过订阅它来获得这个 Worker 发出的事 件以及发送事件给这个 Worker。
以下是如何侦听事件(其实就是固定的 "message" 事件):
w1.addEventListener( "message", function(evt){
// evt.data
} );
也可以发送 "message" 事件给这个 Worker:
w1.postMessage( "something cool to say" );
在这个 Worker 内部，收发消息是完全对称的: // "mycoolworker.js"
addEventListener( "message", function(evt){
// evt.data
} );
postMessage( "a really cool reply" );
注意，专用 Worker 和创建它的程序之间是一对一的关系。也就是说，"message" 事件 没有任何歧义需要消除，因为我们确定它只能来自这个一对一的关系:它要么来自这个 Worker，要么来自主页面。

### 小结

也读过周爱民写的 js 基本原理，总的来说对 js 有了一些认知了。太深刻的，复杂的我就不细研究了。就这。
