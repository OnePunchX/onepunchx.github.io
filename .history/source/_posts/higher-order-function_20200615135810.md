---
title: JS高阶函数
date: 2020-06-15 10:49:49
tags: 高阶函数
---

## 可以接收另一个参数作为参数的函数，就叫做高阶函数。

### 常见高阶函数

1. Array.prototype.map,
2. Array.prototype.filter,
3. Array.prototype.reduce,

既然提到了这几个数组方法，那也就顺便说一说哈

- map 是啥，就不在这里说了，不知道就百度去。map() 方法的回调函数接受 3 个参数。 item index arraySelf 。 item 是数组的每一个元素 ，index 是该元素的下标（数组中的第几个元素），arraySelf 是调用 map 方法的这个数组，当然了，这些参数不是固定叫 item，index 啥的，这是入参，叫啥都行，位置对的上相应的位置就行。比如：

```
[1,2,3].map((one,two,three)=>{
  // 这里的one就对应上面我说的item ，two就是index，这样说就能明白吧？
  return one + two + three.length
})
```

map 有返回值，会返回一个新的数组，而类似的 forEach（）方法，就没有返回值，所以哟，用 forEach 的时候经常会搭配 Array.push() 😁 还有啊，写 jsx 的时候，map 遍历出来的玩意你得加个 key 值，通常可以是 index，比如哈，我举个栗子 🌰

```
<ul>
[1,2,3].map((item, index) => {
  return <li key = {index} value = {item} >item</li>
})
</ul>
```

这玩意写的好像有点多余。。。好像会 map 的都能知道，不会 map 看了我这也看不懂。

```
 [1,2,3].filter(item=>item > 1)

  [1,2,3].reduce((x,y)=>{
  return x + y
  })

```
