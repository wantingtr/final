---
layout: post
title: "Typecript和JavaScript对比"
#subtitle: "关于typescript和JavaScript的小对比"
date: 2018-04-08 21:30:00
author: "wantingtr"
tags:
    - 前端
    - JavaScript
    - TypeScript
---

# Typescript 和 Javascript之间的区别

\------部分资料来自  <a href="https://www.cnblogs.com/langzianan/p/8403332.html">前端阿楠的博客</a>、<a href="https://baijiahao.baidu.com/s?id=1584661822398072586&wfr=spider&for=pc">IT168</a>、<a href="https://www.tslang.cn/">TypeScript官网</a>、《Learning TypeScript》

因为是初学小白，较多资料来自网络和书籍，并且进行了一些整合和修改。如有错误，请多指正。

## JavaScript 和 TypeScript 的概要介绍

### JavaScript

JavaScript 是一种轻量级的解释性脚本语言，可嵌入到 HTML 页面中，在浏览器端执行，能够实现浏览器端丰富的交互功能，为用户带来流畅多样的用户体验。

**JavaScript 是基于对象和事件驱动的，无需特定的语言环境，只需在支持的浏览器上就能运行。**

* * *

### TypeScript

目前的JavaScript标准(ES5)是在许多年前设计出来的，面对如今大规模JavaScript应用的复杂性时，它缺少了许多必要的特性，而新一代的JS标准(ES6)，旨在解决上述可维护性问题，但它还没有完全实现，且现在我们使用的浏览器与之也不是完全兼容的。为了解决此类问题，微软花了两年时间开发出了TypeScript。

> 我们为需要构建和维护大型JavaScript程序的团队设计了TypeScript,以满足他们的需求。typescript可帮助他们在软件组建之间定义接口，并且帮助理解现存JS的行为。同时，使用TS的团队可以将代码构建成动态加载的模块，以减少命名冲突的问题。TS可选的类型系统使开发者们可以使用一些高效的开发工具和最佳实践：静态检查，基于符号的导航，代码补全和代码重构。              ---TypeScript语言特性1.0


> TypeScript是JavaScript类型的超集，它可以编译成纯JavaScript,可以在任何浏览器、任何计算机和任何操作系统上运行，并且是开源的。

TypeScript 的特点：

1.  始于JavaScript，归于JavaScript  
    TypeScript是JavaScript 的超集，包含了 JavaScript 的所有元素，可以载入 JavaScript 代码运行，并扩展了 JavaScript 的语法。TypeScript可以编译出纯净、 简洁的JavaScript代码，并且可以运行在任何浏览器上、Node.js环境中和任何支持ECMAScript 3（或更高版本）的JavaScript引擎中。

2.  强大的工具构建大型应用程序  
    类型允许JavaScript开发者在开发JavaScript应用程序时使用高效的开发工具和常用操作比如静态检查和代码重构。
    类型是可选的，类型推断让一些类型的注释使你的代码的静态验证有很大的不同。类型让你定义软件组件之间的接口和洞察现有JavaScript库的行为。

## TypeScript初接触

在最开始接触到TypeScript的时候，根据官方说明文档，在atom里面安装了TypeScript的插件，创建一个`.ts`后缀的文件，并在命令行运行`tsc filename.ts`，输出结果为一个`filename.js`文件，它包含了和输入文件中相同的JavsScript代码，并且我们可以运行这个**使用TypeScript写的JavaScript应用**。除此之外，观察到相对于ES5标准的JavaScript，TypeScript添加了类属性，并且为了兼容ES3和ES5，会被编译为JS中的函数。  

## TypeScript 的优势

下面列举 TypeScript 相比于 JavaScript 的显著优势：

1.  简单易用
    TypeScript可直接编译成JS文件，并且与现存的JS代码有很高的兼容性，这意味着任何合法的JS程序都是合法的TS程序。

2.  静态输入  
    可选的静态类型声明可约束函数，变量，属性等程序实体，这样编译器和相应的开发工具就可以在开发过程中提供更好的正确性验证和帮助性能。这种强类型特性能让程序员对自己和其他开发团队人员在代码中表达他的意图。

3.  大型的开发项目  
    有时为了改进开发项目，需要对代码库进行小的增量更改。这些小小的变化可能会产生严重的、意想不到的后果，因此有必要撤销这些变化。使用TypeScript工具来进行重构更变的容易、快捷。

## 如何抉择

TypeScript 正在成为开发大型编码项目的有力工具。因为其面向对象编程语言的结构保持了代码的清洁、一致和简单的调试。因此在应对**大型开发项目时，使用 TypeScript 更加合适**。如果**有一个相对较小的编码项目，似乎没有必要使用 TypeScript，只需使用灵活的 JavaScript 即可**。
