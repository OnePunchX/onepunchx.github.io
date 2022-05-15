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
