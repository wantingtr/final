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
---资料来自  <a href="https://www.cnblogs.com/langzianan/p/8403332.html">前端阿楠的博客</a>、<a href="https://baijiahao.baidu.com/s?id=1584661822398072586&wfr=spider&for=pc">百家号</a>、<a href="https://www.tslang.cn/">TypeScript官网</a>

## JavaScript 和 TypeScript 的概要介绍

### JavaScript
JavaScript 是一种轻量级的解释性脚本语言，可嵌入到 HTML 页面中，在浏览器端执行，能够实现浏览器端丰富的交互功能，为用户带来流畅多样的用户体验。

**JavaScript 是基于对象和事件驱动的，无需特定的语言环境，只需在支持的浏览器上就能运行。**

JavaScript 的特点：

- JavaScript 是一种脚本编写语言，无需编译，只要嵌入 HTML 代码中，就能由浏览器逐行加载解释执行。
- JavaScript 是一种基于对象的语言，可以创建对象同时使用现有对象。但是 Javascript 并不支持其它面向对象语言所具有的继承和重载功能。
- JavaScript 的语法简单，使用的变量为弱类型。
- JavaScript 语言较为安全，仅在浏览器端执行，不会访问本地硬盘数据。
- JavaScript 语言具有动态性。JavaScript 是事件驱动的，只根据用户的操作做出相应的反应处理。
- JavaScript 只依赖于浏览器，与操作系统的因素无关。因此 JavaScript 是一种跨平台的语言。
- JavaScript 兼容性较好，能够与其他技术（如 XML，REST API 等）一起使用。

***

### TypeScript
TypeScript 是 Microsoft 开发和维护的一种面向对象的编程语言。它是 JavaScript 的超集，包含了 JavaScript 的所有元素，可以载入 JavaScript 代码运行，并扩展了 JavaScript 的语法。

>TypeScript是JavaScript类型的超集，它可以编译成纯JavaScript,可以在任何浏览器、任何计算机和任何操作系统上运行，并且是开源的。

TypeScript 的特点：

1. 始于JavaScript，归于JavaScript  
TypeScript从今天数以百万计的JavaScript开发者所熟悉的语法和语义开始。使用现有的JavaScript代码，包括流行的JavaScript库，并从JavaScript代码中调用TypeScript代码。
TypeScript可以编译出纯净、 简洁的JavaScript代码，并且可以运行在任何浏览器上、Node.js环境中和任何支持ECMAScript 3（或更高版本）的JavaScript引擎中。

2. 强大的工具构建大型应用程序  
类型允许JavaScript开发者在开发JavaScript应用程序时使用高效的开发工具和常用操作比如静态检查和代码重构。
类型是可选的，类型推断让一些类型的注释使你的代码的静态验证有很大的不同。类型让你定义软件组件之间的接口和洞察现有JavaScript库的行为。

3. 基于先进的 JavaScript  
TypeScript提供最新的和不断发展的JavaScript特性，包括那些来自2015年的ECMAScript和未来的提案中的特性，比如异步功能和Decorators，以帮助建立健壮的组件。
这些特性为高可信应用程序开发时是可用的，但是会被编译成简洁的ECMAScript3（或更新版本）的JavaScript。

## JavaScript 和 TypeScript 的主要差异

TypeScript 可以使用 JavaScript 中的所有代码和编码概念，TypeScript 是为了使 JavaScript 的开发变得更加容易而创建的。例如，TypeScript 使用类型和接口等概念来描述正在使用的数据，这使开发人员能够快速检测错误并调试应用程序

- TypeScript 从核心语言方面和类概念的模塑方面对 JavaScript 对象模型进行扩展。
- JavaScript 代码可以在无需任何修改的情况下与 TypeScript 一同工作，同时可以使用编译器将 TypeScript 代码转换为 JavaScript。
- TypeScript 通过类型注解提供编译时的静态类型检查。
- TypeScript 中的数据要求带有明确的类型，JavaScript不要求。
- TypeScript 为函数提供了缺省参数值。
- TypeScript 引入了 JavaScript 中没有的“类”概念。
- TypeScript 中引入了模块的概念，可以把声明、数据、函数和类封装在模块中。


## TypeScript 的优势
下面列举 TypeScript 相比于 JavaScript 的显著优势：

1. 简单易用  
对JavaScript有效的对TypeScript同样有效，因此可以通过简单地将.js的文件扩展名更改为.ts来使用TypeScript编译器和TypeScript感知工具。  
TypeScript具备absolutelytyped.org收集类型定义，提供数百个JavaScript库(包括jQuery，Angular和Bootstrap)的定义并描述这些库的导出接口。由于TypeScript可以在任何平台上定位JavaScript虚拟机，并且不仅提供编译器，还提供一组支持智能编码的语言服务。

2. 静态输入  
静态类型化是一种功能，可以在开发人员编写脚本时检测错误。查找并修复错误是当今开发团队的迫切需求。有了这项功能，就会允许开发人员编写更健壮的代码并对其进行维护，以便使得代码质量更好、更清晰。

3. 大型的开发项目  
有时为了改进开发项目，需要对代码库进行小的增量更改。这些小小的变化可能会产生严重的、意想不到的后果，因此有必要撤销这些变化。使用TypeScript工具来进行重构更变的容易、快捷。

4. 更好的协作  
当发开大型项目时，会有许多开发人员，此时乱码和错误的机也会增加。类型安全是一种在编码期间检测错误的功能，而不是在编译项目时检测错误。这为开发团队创建了一个更高效的编码和调试过程。

5. 更强的生产力  
干净的 ECMAScript 6 代码，自动完成和动态输入等因素有助于提高开发人员的工作效率。这些功能也有助于编译器创建优化的代码。



## JavaScript 的优势
相比于 TypeScript，JavaScript 也有一些明显优势。

1. 人气  
  JavaScript 的开发者社区仍然是巨大而活跃的，在社区中可以很方便地找到大量成熟的开发项目和可用资源。  

2. 学习曲线  
  由于 JavaScript 语言发展的较早，也较为成熟，所以仍有一大批开发人员坚持使用他们熟悉的脚本语言 JavaScript，而不是学习 TypeScript。

3. 本地浏览器支持  
  TypeScript 代码需要被编译（输出 JavaScript 代码），这是 TypeScript 代码执行时的一个额外的步骤。

4. 不需要注释  
  为了充分利用 TypeScript 特性，开发人员需要不断注释他们的代码，这可能会使项目效率降低。

5. 灵活性  
  有些开发人员更喜欢 JavaScript 的灵活性。



## 如何抉择
TypeScript 正在成为开发大型编码项目的有力工具。因为其面向对象编程语言的结构保持了代码的清洁、一致和简单的调试。因此在应对**大型开发项目时，使用 TypeScript 更加合适**。如果**有一个相对较小的编码项目，似乎没有必要使用 TypeScript，只需使用灵活的 JavaScript 即可**。