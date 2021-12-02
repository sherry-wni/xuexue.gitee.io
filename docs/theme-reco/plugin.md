---
title: 事件循环 (event loop)
date: 2020-05-28
tag:
  - tag1
categories:
  - js
---
::: tip 
 1.认识同步，异步。

 同步：主线程上排队执行的任务，只有前面的任务执行完成，才能执行后面的任务。

 异步：是指不进入主线程，而是进入任务队列的任务，只有任务队列通知主线程，某个任务可以执行了，该任务才会进入主线程执行，

 2.了解进程。

 打开浏览器之后，我们更关心的是渲染进程，渲染进程也是浏览器的核心进程。
 GUI线程: 负责渲染页面，解析html、css;构建DOM树和渲染树。

 js引擎线程:js引擎线程负责解析和执行js程序，而chrome的V8引擎就是跑在js引擎线程上的,js引擎线程与gui线程相斥，当浏览器执行js程序的时候，GUI渲染线程会保存在一个队列当中，当js程序执行完成，才会接着执行，如果js的执行时间过长，会影响页面渲染的不连贯性。

 定时触发线程:定时器触发线程是在定时任务完成之后会通知事件触发线程往队列里添加事件。

 事件触发器:满足触发条件的事件放在任务队列，一些异步的事件会放到异步队列中。
 异步HTTP请求线程:经过ajax请求的，在请求完成时如果有回调函数就通知事件触发线程添加到任务队列中。
:::

### Event Loop 机制

以前 js 是在浏览器环境中运行，由于 chrome 对 v8 做了开源；所以 js 有机会在服务端运行；浏览器和 node 都是 js 的运行环境，它们相当于是一个宿主，宿主能提供一个能力能帮助 js 实现 Event Loop

####  Event Loop 是什么

Event Loop 是一个程序结构，用于等待和发送信息的事件。 简单说就是在程序中设置 2 个线程，一个负责程序本身的运行，称为“主线程”；另一个负责主线程和其他进程（主要是各种 I/O 操作）的通信 被称为“Event Loop 线程”（也可以翻译为消息线层）


##### 异步实现
1.宏观：浏览器多线程（从宏观来看是多线程实现了异步）

2.微观：Event Loop，事件循环（Event Loop 翻译是事件循环，是实现异步的一种机制）

```script
console.log(1)
setTimeout(function() {
  console.log(2)
}, 0)
Promise.resolve().then(function() {
  console.log(3)
})
console.log(4) // 1 4 3 2
```
#### 宏任务
:::tip
script：script 整体代码

setImmediate：node 的一个方法

setTimeout 和 setInterval

requestAnimationFrame

I/O

UI rendering
:::
#### 微任务
:::tip
Object.observe:监听对象变化的一个方法

MutationObserver:可以监听 Dom 结构变化的一个 api

postMessgae:window 对象通信的一个方法

Promise.then catch finally
:::
```script
async function async1() {
  console.log('async1 start')
  await async2()
  console.log('async1 end')
}

async function async2() {
  return Promise.resolve().then(_ => {
    console.log('async2 promise')
  })
}

console.log('start')
setTimeout(function() {
  console.log('setTimeout')
}, 0)

async1()

new Promise(function(resolve) {
  console.log('promise1')
  resolve()
}).then(function() {
  console.log('promise2')
})

/*
       start
       async1 start
       promise1
       async2 promise
       promise2
       async1 end
       setTimeout
   */
```