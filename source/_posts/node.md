---
title: node
date: 2020-10-20 14:25:56
categories: 前端
tags: node
---

序言
准备学习 node
多学几个框架

注：node 官网有对 package.json 的指南

### koa

基于 Node.js 平台的下一代 web 开发框架

```
const Koa = require('koa');
const app = new Koa();

// x-response-time

app.use(async (ctx, next) => {
  const start = Date.now();
  await next();
  const ms = Date.now() - start;
  ctx.set('X-Response-Time', `${ms}ms`);
});

// logger

app.use(async (ctx, next) => {
  const start = Date.now();
  await next();
  const ms = Date.now() - start;
  console.log(`${ctx.method} ${ctx.url} - ${ms}`);
});

// response

app.use(async ctx => {
  ctx.body = 'Hello World';
});

app.listen(3000);

```
