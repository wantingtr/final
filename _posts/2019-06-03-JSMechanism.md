---
layout: post
title: "JavaScript的执行机制——同步与异步、事件循环、微任务与宏任务"
#subtitle: ""
date: 2019-06-03 14:00:00
author: "wantingtr"
tags:
    - 前端
    - js
---


部分参考：[这一次，彻底弄懂 JavaScript 执行机制](https://juejin.im/post/59e85eebf265da430d571f89)

# JS执行机制

* **javascript是一门单线程语言**
* **Event Loop是javascript的执行机制**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190603130856831.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dhbnRpbmd0cg==,size_16,color_FFFFFF,t_70)


## 同步任务
- 按照执行顺序一件事一件事的执行，只有**前一个任务执行完毕后才能执行后一个任务。**
```js
console.log(1)
while(true) {
 
}
console.log(2)
//只输出1
```
解析：
while是一个同步任务，会在while语句中无限循环

## 异步任务
- JS引擎按顺序执行到异步任务后，先把这个任务**挂起**，先执行后面的代码，等到**时机成熟，再回来执行异步任务的回调函数。**

### 为什么要异步？
- 对于一些耗时比较长且与JS无关的操作（比如延时setTimeout，setInterval以及IO操作，ajax等），JS**无需白白等待，可以先把这个操作挂起，等获得响应之后在回来执行**这个任务的回调函数。提高效率。

### 常见的异步任务/模式

#### setTimeout

> setTimeout是**经过指定时间后，把要执行的任务(本例中为task())加入到Event Queue中**。
即只有当时间到了，定时器才会把函数体扔到异步队列中，异步队列再等待事件循环执行

```js
console.log(1)
setTimeout(function () {
    console.log(2)
}, 0)
console.log(3)
//运行结果，1,3,2
```
解析：
setTimeout是一个异步任务
console.log是同步任务

在JS运行机制中，从上往下运行，但运行时异步任务运行时会被挂起，等同步任务执行完毕之后，才会执行。

```js
for (var i = 0; i < 4; i++) {
    setTimeout(function () {
        console.log(i)
    },1000);
}
//输出 四个4（4,4,4,4）
```
解析：
setTimeout是异步事件，当进入for循环时，会先将其挂起，并且有时间（1000）限制，就算for循环执行完之后异步事件也不会立即放入到任务队列里，而是等到时间到了才会执行setTimeout语句，此时i已经加成4了。

for循环执行完了才会执行异步队列，但是此时异步队列并没有东西。
只有当时间到了，定时器会把函数体扔到异步队列中，异步队列再等待事件循环执行

#### setInterval
setInterval会按照指定时间循环执行


* 对于`setInterval(fn,ms)`来说，不是每过ms秒会执行一次fn，而是**每过ms秒，会有fn进入Event Queue**。
* 一旦setInterval的回调函数fn执行时间超过了延迟时间ms，那么就完全看不出来有时间间隔了。

### Promise
见es6笔记

### generator

### async/await

### ajax
> js的单线程机制意味着所有任务需要排队，前一个任务结束，才会执行后一个任务。如果前一个任务耗时很长，后一个任务就不得不一直等着。于是就有一个概念，任务队列。如果排队是因为计算量大，CPU忙不过来，倒也算了。但是很多时候CPU是闲着的，因为IO设备（输入输出设备）很慢（**比如Ajax操作从网络读取数据**），不得不等着结果出来，再往下执行。

> JavaScript语言的设计者意识到，这时主线程完全可以**不管IO设备，挂起处于等待中的任务，先运行排在后面的任务**。等到IO设备返回了结果，再回过头，把挂起的任务继续执行下去。

"回调函数"（callback）就是那些**会被主线程挂起来的代码**。异步任务必须指定回调函数，当**主线程开始执行异步任务，就是执行对应的回调函数**。


## js如何实现异步？-> eventLoop事件循环

* 同步和异步任务分别进入不同的执行"场所"，**同步的进入主线程**，**异步的进入Event Table并注册函数。**
* 当指定的事情完成时，Event Table会将这个函数移入Event Queue。
* 主线程内的任务执行完毕为空，会去Event Queue读取对应的函数，进入主线程执行。
* 上述过程会不断重复，也就是常说的Event Loop(事件循环)。

### 微任务与宏任务

除了广义的同步任务和异步任务，还可更细的将任务分为微任务与宏任务

* macro-task (宏任务)：
    * 包括**整体代码script，`setTimeout`，`setInterval`,`setImmediate`,`I/O`,`
UI rendering`**
* micro-task (微任务):
    - **`Promise`,`process.nextTick`,`MutationObserver`**

不同类型的任务会进入其对应的Event Queue，比如`setTimeout`和`setInterval`会进入相同的Event Queue。


![在这里插入图片描述](https://img-blog.csdnimg.cn/20190603130841553.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dhbnRpbmd0cg==,size_16,color_FFFFFF,t_70)

事件循环的顺序，决定js代码的执行顺序。
**进入整体代码(宏任务)后，开始第一次循环。接着执行所有的微任务。然后再次从宏任务开始，找到其中一个任务队列执行完毕，再执行所有的微任务。**

### 延伸到vue

* Vue 在修改数据后，视图不会立刻更新，而是**等同一事件循环中的所有数据变化完成之后，再统一进行视图更新。也用到了nexttick**

> 它会把这些数据的修改全部 push 到一个队列里，然后内部调用 一次 nextTick 去更新视图，所以<u>数据到 DOM 视图的变化是需要在下一个 tick</u> 才能完成。

### 例子

#### 1
```js
setTimeout(function() { 
    console.log('setTimeout'); //下一个事件循环的宏任务
}) 

new Promise(function(resolve) { 
    console.log('promise');  //立即执行
}).then(function() { 
    console.log('then');  //此次事件循环的微任务
}) 

console.log('console'); //立即执行
```
执行结果：
`promise->console->then->setTimeout`


* 这段代码作为宏任务，进入主线程。
* 先遇到setTimeout，将其回调函数注册后分发到宏任务Event Queue。
* 遇到了Promise，new Promise立即执行，then函数分发到微任务Event Queue。
* 遇到console.log()，立即执行。
* 整体代码script作为第一个宏任务执行结束，开始执行此次事件循环的微任务即then函数
* 第一轮事件循环结束，开始第二轮循环，从宏任务Event Queue开始。执行setTimeout函数

#### 2
```js
console.log('1');

setTimeout(function() {
    console.log('2');
    process.nextTick(function() {
        console.log('3');
    })
    new Promise(function(resolve) {
        console.log('4');
        resolve();
    }).then(function() {
        console.log('5')
    })
})
process.nextTick(function() {
    console.log('6');
})
new Promise(function(resolve) {
    console.log('7');
    resolve();
}).then(function() {
    console.log('8')
})

setTimeout(function() {
    console.log('9');
    process.nextTick(function() {
        console.log('10');
    })
    new Promise(function(resolve) {
        console.log('11');
        resolve();
    }).then(function() {
        console.log('12')
    })
})

```

执行结果
`1，7，6，8，2，4，3，5，9，11，10，12`


