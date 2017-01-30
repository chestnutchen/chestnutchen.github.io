---
layout: post
title:  "面试题详解系列之『实现一个insertAfter』"
date:   2017-01-27 17:00:00  +0800
categories: 基础
---

> 关键词：Conner Case, 数据结构基本思路

　　来看这个屡试不爽的小型编程题，随便一搜或者面多几次，应该很容易遇到的。只是我很奇怪，面了挺多社招的同学，好像总体真的没有实习和校招的答得好，有时候遇到问上一篇的问题会把`insertAfter`也说出来的。原生接口不知道，提示了也写不出来，那真的很遗憾。倒有部分人主要是对原生接口不熟导致思路真的很奇怪。不过你是在不熟原生接口的情况下写出来的话，我反倒觉得达到了考察的结果。

题目不是算法，强行归类应该算数据结构，考背接口没意思，所以如果原生接口你不熟，我会提供

- Node.insertBefore
- Node.appendChild

其他属性或者接口可以自行yy，如果不是很清楚的话。但是代码和程序总是会有一个或一类最优解的，yy也要有道理。对于只懂`jQuery`的，我会考虑换成实现一个`.after()`的方法，同样我提供

- before
- append

这个题目并不难，Conner Case 只有一个，能一口气答出来的人却不多。我觉得这是一个习惯，解决问题时，多从底层或源头全方位考虑一下会比较好。

首先，这是考察`DOM`树增删改查的增，很明显的数据结构入门题目。一般不要修改原生类型，所以尽量不要直接在`Node.prototype`上直接挂。那么实现一个工具函数，我的思路如下

1. 审题，`insertAfter`是插入到当前节点后边，即插入一个同父节点的兄弟节点，参数需要一个新节点`newNode`，一个当前节点`curNode`，均为必传
1. 溯源，`DOM`树是一棵有部分特定结构的多叉树，树插入同级别节点需要通过`parent`指针来操作
2. 分析，多叉树的后代节点一般是一个容器，或者我们直接当成数组，可以分为三种情况
    - 在头部插入（特殊）
    - 在中间插入（大多数情况）
    - 在尾部插入（特殊）

    <br>
    由于我们已经有一个当前节点在父节点的后代容器中，不会出现第一种情况，那么剩下的就是处理第二、三种。

3. 细节，一般先考虑特殊情况`尾部`插入，再考虑`中间`
    - 不熟悉接口的思路
        1. 使用迭代的方式在后代容器中找到当前节点，判断是否是容器的最后一个节点，如果是，使用我提示的`appendChild`
        2. 如果不是，继续迭代找到下一个节点，使用我提示的`insertBefore`
    - 熟悉接口的思路
        1. 使用`lastChild`判断当前节点是不是父节点最后一个后代，是的话使用`appendChild`（考虑到可读性较好，不然`insertBefore`第二个参数传`null`也可以完成）
        2. 否则，使用`insertBefore`插入到下个节点`curNode.nextSibling`的前边

如果你知道`Element.insertAdjacentXxx`系列方法，可能会中奖，目测会再问问为什么这个题目里边不用这个，看起来好像更方便嘛。

是不是理清思路觉得简单得不行？不熟悉接口的话你随意yy个接口名字是可以接受的，属性不知道用循环去判断也是可以接受的。完整的代码：

```js
/**
 * 插入节点到指定节点的后边
 *
 * @param {Node} newNode 新节点
 * @param {Node} curNode 当前指定节点
 */
function insertAfter(newNode, curNode) {
    if (!newNode || !curNode) {
        // insertBefore 因为是在 parentNode 上调用的，不太相同，这里需要有 curNode
        throw new Error('参数有误');
    }

    if (!curNode.parent) {
        // 顶级节点不应该支持
        throw new Error('当前节点不支持该方法');
    }

    let {parent} = curNode;
    if (parent.lastChild === curNode) {
        parent.appendChild(newNode);
    }
    else {
        parent.insertBefore(newNode, curNode.nextSibling);
    }
}
```




