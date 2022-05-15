---
title: 组件卸载生命周期遇见的坑
date: 2020-10-13 19:55:27
categories: 前端
tags: 组件卸载 componentWillUnmount
---

### 前言：

刚刚在敲码时候遇见了个离谱的问题，排查发现与组件卸载相关。
刚好写个博，一来趁热记录一下刚刚走过的坑，二来总结学习一下相关的问题其余的坑。

### 问题描述：

在页面挂载了一个 pubsub 监听程序，，啊，突然发现 pubsub 使用方法的博客还没写，留了个坑
问题是触发该 pubsub 的时候，本该执行一次的函数被执行了多次，并且发现每次热更新就会多执行一次函数

### 问题分析

经过排查啊，页面组件是只加载一次的，所以就一定是 pubsub 的监听被触发了多次或者生成了多个监听程序

### 上代码实战分析

```
 PubSub.subscribe('电子签名', (keyword, data) => {
      const { functionalModuleId, caseName, caseObject, okFn, ...rest } = data;

      const eSignAuth = getEsignAuthorities().find(
        item => `${item.id}` === `${functionalModuleId}`,
      ); // 获取电子签名权限信息
      //  判断电子签名是否开启
      const newDisabled = !(
        eSignAuth &&
        (eSignAuth.electronicSignatureIdentifier === 1 ||
          eSignAuth.electronicSignatureIdentifier === 3)
      );
      if (!functionalModuleId || newDisabled) {
        okFn({});
        return;
      }
      this.setState({
        isModalShow: true,
        functionalModuleId,
        caseName,
        caseObject,
        okFn,
        ...rest,
      });
    });

```

原来上面这段代码写在 componentDidMount 的生命周期里。后来放进类构造函数里，用 componentWillUnmount 处理
代码如下

```

 componentDidMount() {
   this.esignPubsub;
 }

 componentWillUnmount() {
   PubSub.unsubscribe(this.esignPubsub);
 }

```

这里其实还涉及一些其他的点，比如
热更新 websocket 推送过去的，只更新变化的 bundle。
浏览器刷新全量更新除缓存外所有文件。

所以每次保存代码热更新，，都没有清空上一次程序的线程，会导致多次运行同一个程序
类似的还有：

websocket 的运行等

#### 这个组件卸载的生命周期啊，就是这个 componentWillUnmount 生命周期还要注意定时器的清楚。这已经是老生常谈了哈哈哈

### 结语

解决这个问题还是收获挺多的。挺开心。以前害怕遇见问题解决不了，现在希望能遇见更多的问题，从中学到更多的遗漏的知识点。
心态的改变，也印证了自我的成长。继续前行吧
