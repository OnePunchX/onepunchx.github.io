---
title: 一些实用的操作符
date: 2020-09-01 16:45:20
tags: 可选链
categories: 前端
---

### ?. 可选链

通过连接的对象的引用或函数可能是 undefined 或 null 时，可选链操作符提供了一种方法来简化被连接对象的值访问。
react 项目中用的时候需要在 package.json 文件中加入这个依赖
"devDependencies": {
"@babel/plugin-proposal-optional-chaining": "7.2.0",
}
使用 vscode 会报错，要在 vscode 的 setting 中配置：
"javascript.validate.enable": false,

```
//  不用可选链
let nestedProp = obj.first && obj.first.second;
//  用可选链
let nestedProp = obj.first?.second;

let result = someInterface.customMethod?.();

let arrayItem = arr?.[42]
```

### ?? 空值合并操作符

第一次见的时候，觉得不就是 || 这种操作么， 比如 false || 'zhujun'  
结果当然不是那么简单
让我来 show you something

|| 这种叫做 逻辑或操作符，左边成立用左边，左边 false 用右边 可是如果左边有值为 0 的时候，就不对了 ，举个 🌰

```
const wanghao = { IQ:0 , cm:150 };
const value = wanghao.IQ || 100
console.log(value)  //  100

//  这就不对了，它的IQ明明是0 。。。。哈哈
//  假值（0,'',NaN,null,undefined） 对于空字符串和数字0，就只用用逻辑操作符 || 会有问题的

const newValue = wanghao.IQ ?? 'wanghao is really smart'
console.log(newValue)  // 0  这就对了，，哈哈😄
```

用了?? 保证没有 null 或者 undefined ~

tips：null || undefined ?? "foo"; // 抛出 SyntaxError

空值合并操作符针对 undefined 与 null 这两个值，可选链式操作符（?.） 也是如此。在这访问属性可能为 undefined 与 null 的对象时，可选链式操作符非常有用。
