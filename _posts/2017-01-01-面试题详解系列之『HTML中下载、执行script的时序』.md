---
layout: post
title:  "面试题详解系列之『HTML中下载、执行script的时序』"
date:   2017-01-01 15:00:00 +0800
categories: 基础
---

> 关键词：阻塞 async defer 时序

　　一般我会以这一题开始整个面试，这个题目至少要知道一点点html渲染的知识。遇到过一点概念都没有的面试者，才第一题就已经不想再继续下去了。。。

题目：

    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
    </head>
    <body>
        <script>
            // something here
        </script>
        <script src="url here"></script>
        <div></div>
    </body>

**第一问**：这个小片段一般人都会说要把`script`放到`div`后边去，为什么？

1. `HTML`解析是流式解析，下载并解析到`script`标签（不含有`defer`、`async`属性）时会开始去下载/执行`script`中的内容
2. 下载/执行 js 时会阻塞`HTML`的解析，导致白屏时间较长
3. `script`可能要修改`div`的内容，此时还没有`div`标签对应的`DOM`节点

第一问主要是考察一点点渲染原理，js 下载和执行会阻塞文档解析。根据回答的情况，大概已经可以判断对基础知识掌握的深度。`HTML`方面的知识其实非常多，写起来能 work 是很容易，毕竟是包容性极强的。

**第二问**：如果`script`的位置不移动，有什么方法解决上边说的问题么？

我期望或者说自认为比较好的回答是：这里分为两种。

1. `script`依赖下方的`DOM`元素
    - 使用`DOMContentLoaded`事件
    - 使用`onload`事件
    - 讲讲这两者的区别
    - 使用`defer`属性
    - 讲讲`defer`的效果

2. `script`**不**依赖下方的`DOM`元素
    - 使用`async`属性
    - 讲讲`async`的效果
    - 放个不解答的问题在这，如果使用了`async`，也想依赖`DOM`元素，应该怎么搞？提示，`jQuery`玩过这招。

`defer`和`async`用`whatwg`的一张图来解释吧，有点基础的，看了马上就能理解。还有，这张图还有可以扩展问的东西。
<img src="../../../../img/sequence.png" style="margin-left: 2em">

其实`SPA`的`HTML`页面基本上都是一上来就要把`loader`和框架等东西加载进来的，都是放在`head`里边并且不延迟不异步。有时候业务代码还直接打成一个超大文件，第一次访问没有缓存，首屏体验是很差的。并且如果数据量大，`SPA`架构的交互体验确实没有页面直出好。所以`HTML`关于`script`这块的知识还是很重要的，额外还有预加载、并行下载之类的问题，可能就比较深入了，作为基础，我觉得以上的问题应该比较符合。

