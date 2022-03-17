---
title: 性能指标和优化目标
date: 2020-05-29
tags:
  - tag1
categories:
  - 性能优化
---
# 性能指标和优化目标
```
项目优化是一个前端工程师都会面临到的问题，这篇博客主要分享性能优化的前端工具。
```
## NetWork 瀑布图

- 蓝色的线DOMContentLoaded： DOM加载完成的时间
- `红色的线：站点其它资源加载完成的时间`
- DNS Lookup：dns寻址时间

- inital connection: TCP连接时间

- TTFB: 你的请求发出到你的请求回来总时长(决定了你的网站响应时间)



## Lighthouse

- First Contentful Paint  首次渲染
- Speed index 速度指数(谷歌官方给定的时间是小于4S )
- FPS 查看



# RAIL测量模型

谷歌提出的前端性能测量模型，通过它可以知道我们的性能测试目标，RAIL是如下字母的缩写

- Response 响应  处理事件应在`50ms`内完成 `这个响应不是指代网络请求的响应,而是指用户交互的响应`

- Animation 动画

  - 动画是否流畅 FPS  ≈ 60
  - 每10ms产生一帧

- Idle 空闲 尽可能增加空闲时间，不能让你的主线程一直处于忙碌的状态(忙碌的状态就会很卡)

  - 通过DevTool 性能面板查看

    ![Lighthouse@2x](/Users/feiwu/workspace/字节跳动/前端性能优化/screen/Lighthouse@2x.png)

- Load 在`5S`内完成加载并可以交互

# 性能测量工具

- Chrome DevTools 
- Lighthouse 网站整体质量评估
- WebPagesTest 多测试地点、全面性能报告



# 使用WebPageTest评估Web网站性能

## https://webpagetest.org

![WX20220304-141724@2x](/Users/feiwu/workspace/字节跳动/前端性能优化/screen/WX20220304-141724@2x.png)



## 测试结果

![image-20220304141940227](/Users/feiwu/Library/Application Support/typora-user-images/image-20220304141940227.png)

 如下是我们比较关心的指标

- First Byte：表示我们发出去的第一个请求 响应的时间是多少
- Start Render：开始首屏渲染
- Speed index:  速度指数



# 使用 Lighthouse 分析前端性能

Lighthouse 最新版的提供了 6 个性能指标：**FCP**、**SI**、**LCP**、**TTI**、**TBT** 和 **CLS**；权重分别是 15%，15%，25%，15%，25% 和 5%。Lighthouse 会根据权重计算得到一个分数值。 

通过全局npm安装的方式使用

```shell
npm i lighthouse -g

#使用也很简单
lighthouse 你要测试的网站
ighthouse https://www.baidu.com/ --locale=zh-CN --preset=desktop --disable-network-throttling=true --disable-storage-reset=true
# 测试完成后，会在terminal中告诉你报告生成的地址
```

### 执行的结果

![WX20220304-143852@2x](/Users/feiwu/workspace/字节跳动/前端性能优化/screen/WX20220304-143852@2x.png)

- **Performance** 性能总指标

- **Accessibility**  可访问指标

- **Best Practices**

- **SEO**

- **PWA**

  

### lighthouse 优化建议

在 lighthouse 的优化建议中，我们可以看到和 JavaScript 资源加载相关的建议有：

- **Reduce unused JavaScript**
- **Minify JavaScript**
- **Remove duplicate modules in JavaScript bundles**

资源加载的优化通常有几个思路：

- 合理的加载顺序/策略（延迟加载/预先加载）
- 压缩优化资源的体积
- 代码分割 & 公共提取 & 按需加载

![WX20220304-143907@2x](/Users/feiwu/workspace/字节跳动/前端性能优化/screen/WX20220304-143907@2x.png)



**内容呈现相关**

- **FCP**  **首次内容绘制**。
  - 它统计的是**从进入页面到首次有 DOM 内容绘制所用的时间**。这里的 DOM 内容指的是文本、图片、非空的 `canvas` 或者 SVG。
- **LCP** **最大内容绘制**
  -  它统计的是**从页面开始加载到视窗内最大内容绘制的所需时间**，这里的内容指文本、图片、视频、非空的 canvas 或者 SVG 等。

**用户交互相关**

- TTI **页面可交互的时间**
  - 页面开始绘制内容，即 **FCP** 指标开始之后
  - 用户的交互可以及时响应：
  - 页面中大部分可见的元素已经注册了对应的监听事件（通常在 DOMContentLoaded 事件之后）
  - 在 **TTI** 之后持续 5 秒的时间内无**长任务**执行（没有超过 50 ms 的执行任务 & 没有超过 2 个 GET 请求）

- TBT **阻塞总时间**

  - 测量的是 **FCP** 与 **TTI** 之间的时间间隔。这个指标反映了用户的交互是否能及时响应。 如果主线程执行了长任务会导致用户的输入无法及时响应。当主线执行的任务所需的时长超过 50ms，我们就认为这是一个长任务（[long task](https://link.zhihu.com/?target=https%3A//web.dev/custom-metrics/%23long-tasks-api)）。假设在主线程上执行了一系列的任务，每个长任务的阻塞时间等于执行时间减去 50 ms，最后可以统计得到一个总的阻塞时间。

    ![TBT](/Users/feiwu/workspace/字节跳动/前端性能优化/screen/TBT.jpeg)