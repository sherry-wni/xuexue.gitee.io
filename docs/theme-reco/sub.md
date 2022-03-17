---
title: 发布订阅
date: 2020-05-29
tags:
  - tag1
categories:
  - js
---
### 发布订阅模式
```
   由于一直搬砖，对于前端设计模式以及很多原理性的东西十分欠缺，导致很多时候不会从全局去思考问题，
   这次花点时间学习困扰我很久的问题 ——发布和订阅。在网上找到了一个讲的很不错的博客学习，做一个实践。
   博客地址：https://www.cnblogs.com/crazycode2/p/11846277.html
```
```javascript
class Watcher {
  constructor() {
    this.subs = [];
  }
  on(eventName, fn) {
    if (this.subs[eventName]) {
      this.subs[eventName].push(fn);
    } else {
      this.subs[eventName] = [fn];
    }
  }
  emit(eventName, ...args) {
    if (Array.isArray(this.subs[eventName])) {
      this.subs[eventName].forEach((fn) => {
        fn.apply(this, args);
      });
    }
  }
  unbind(eventName, fn) {
    if (fn) {
      this.subs[eventName] = this.subs[eventName].filter(
        (e) => e !== fn && e.orgin !== fn
      );
    } else {
      delete this.subs[eventName];
    }
  }
  once(eventName, fn) {
    let only = (...args) => {
      fn.apply(this, args);
      this.unbind(eventName, fn);
    };
    only.orgin = fn;
    this.on(eventName, only);
  }
}
```
