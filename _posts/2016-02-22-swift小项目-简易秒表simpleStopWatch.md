---
layout: post
title: swift小项目-简易秒表simpleStopWatch
date: 2016-02-22
categories: blog
tags: [swift]
description: 一个简易的swift秒表

---

前天在github看到一个30天一天一个swift的项目。想想自己也可以试试，于是便有了今天的swift小项目。下面是几个总结

*   使用autolayout时，在storyborad里为各个组件添加约束的时候，记住不要先写死某些长度宽度，而是要用各种约束去把宽度长度定死，最后才加上一些自己需要的长宽约束。用这种思维会清晰很多。

*   在用ctrl+拖动的方式绑定到代码里，要注意命名最好一开始就想好，不然以后改名字的时候，这些绑定要重新删除再绑定，不然会报 "setValue:forUndefinedKey:]: this class is not key value coding-compliant for the key name" 这样一种错误。

*   可以用『String(format: "%.1f", countNumber)』这种方式来将double类型格式化显示你要展示的String类型

代码可以在我的github看到

