---
layout: post
title: "关于设计稿，px与rem以及响应式网站的开发思路与流程"
subtitle: ""
date: 2018-12-14 17:00:00
author: "wantingtr"
tags:
    - 前端
---

# 关于设计稿，px与rem以及响应式网站的开发思路与流程
上周做了小型的一个响应式网站，主要负责前端部分。使用Vue+CSS+ts实现，第一次写响应式网站，加上对px，vw，rem等不太熟悉，浪费了很多时间走弯路。整理一下整体实现响应式网站的思路吧。

## 关于px，rem，vw
具体的概念就不说了，主要是**rem的优势在于使用rem为元素设定字体大小时，相对大小对比的是HTML根元素**。所以只修改根元素就能成比例地调整所有大小，并且能保证在不同宽度和尺寸的设备中能在维持布局不变的情况下对所有元素成比例缩放。  

因此，即使设计师给的设计稿是按照px为单位设计的，但是前端需要经过转换，在CSS中使用rem单位。



## 响应式网站的实现思路
响应式网站及根据不同的设备自适应响应的布局，主体可以分为两类：
1. web端与移动端的不同，即针对这类不同终端设备最好需要两份设计稿，根据web端与移动端重写两份CSS样式布局。
2. 在每一类终端上，也需要根据屏幕的不同进行大小适配，这一部分，主要利用上面所说的rem来实现。

&nbsp;

## 开发流程

### 使用rem实现自适应

首先，在网页代码(index.html)的head中，加入一行viewport元标签。
> `<meta name=”viewport” content=”width=device-width, initial-scale=1″ />`

其中：
1. width=device-width 把布局视口设置成为浏览器（屏幕）的宽度。
2. initial-scale=1 的意思是初始缩放的比例是1，使用它的时候，同时也会将布局视口的尺寸设置为缩放后的尺寸。而缩放的尺寸就是基于屏幕的宽度来的，也就起到了和width=device-width同样的效果。
3. viewport是网页默认的宽度和高度，上面这行代码的意思是，网页宽度默认等于屏幕宽度（width=device-width），原始缩放比例（initial-scale=1）为1.0，即网页初始大小占屏幕面积的100%。

在js中应用
```js
document.documentElement.style.fontSize = document.documentElement.clientWidth / 1920*100 + 'px';
```

- 1920是所给的设计稿宽度，代表1920px。
- clientWidth为浏览器实际宽度。

这里的设计稿是1920px，若有一元素在设计稿上的宽度为100px,实际设备物理宽度为960px，即刚好为设计稿的一半，所以这时该元素在设备上的实际宽度`100*（960/1920）=50px`，也就是需要设计其宽度为100rem。  

而一般浏览器会设置最小字体（例如chrome的最小字体为12px）,如果html的font-size为上述的`960/1920`,相当于为0.5px,会造成一些错误。所以,一般会将计算后的结果乘100,即在CSS中，**只需对照着设计稿，将单位为px的元素除100，即可转换成rem格式**.

### 利用media实现web端与移动端的布局自动响应

对于网站的响应式实现，可以使用bootstrap或其他的库和模板，但最简单最直观的应该还是CSS3引入的`@media查询`,可以针对不同的屏幕宽度定义不同的样式。


```css
@media screen and (max-width: 400px) {
    /*
    此处样式应放在css文件的最后
    */
}
```
注意：
- 若media与主题CSS放在同一个文件中，需要注意代码顺序。这里就又牵扯到CSS的优先级问题了。即在CSS中是越靠后的优先级越高，若将查询部分的css写在前面，即使满足查询条件，后面的css也会将前面的样式覆盖掉。
- 使用`max-width`与`min-width`时，代码顺序也不一样。若使用的是max-width，需要根据屏幕宽度从大到小的代码顺序；使用min-width时，需要根据从小到大的顺序。原因，也是上一点提到的优先级问题。
- 除第一点提到的之外，还需要注意media查询只能**改变**符合条件的元素样式，即**样式是层叠的**，若想根据不同的屏幕宽度适配独立的css，需要多份CSS文件。

### 多份设计稿，如何设定根元素font-size？

一般来说，设计师会提供两份设计稿，一份web端，一份移动端。也就是需要根据屏幕宽度动态设定html的font-size。(这次项目设计师刚开始只给了一份web端的设计稿，宽度为1920px...然后我只用media调了一下字体大小，整体布局没改，在手机上的效果..一言难尽)

这里只是很简单的查询屏幕宽度和使用一个if语句，一般认定屏幕宽度小于750px的移动端，反之为web端。  
当然有更好的适配方法，但这个肯定是最简单的。

```js
(function () {
  var designW1 = 1920; //设计稿宽，我这次的web端设计稿是1920*1080的
  var font_rate = 100; 
  var designW2 = 375;//移动端设计稿宽，一般按照iphone6的设备宽度来设计。

  if (document.documentElement.clientWidth > 750) {
    //适配
    document.getElementsByTagName("html")[0].style.fontSize = (document.documentElement.clientWidth) / designW1 * font_rate + "px";

    //监测窗口大小变化
    window.addEventListener("onorientationchange" in window ? "orientationchange" : "resize", function () {
      document.getElementsByTagName("html")[0].style.fontSize = (document.documentElement.clientWidth) / designW1 * font_rate + "px";
    }, false);
  } else {
    //适配
    document.getElementsByTagName("html")[0].style.fontSize = (document.documentElement.clientWidth) / designW2 * font_rate + "px";

    //监测窗口大小变化
    window.addEventListener("onorientationchange" in window ? "orientationchange" : "resize", function () {
      document.getElementsByTagName("html")[0].style.fontSize = (document.documentElement.clientWidth) / designW2 * font_rate + "px";
    }, false);
  }

})();
```
&nbsp;

相关连接：
- <a href='https://www.cnblogs.com/dannyxie/p/6640903.html'>一步步教你使用rem适配不同屏幕的移动设备</a>
- <a href='https://www.cnblogs.com/libaoli/p/5779629.html'>HTML5+CSS3的响应式网页设计：自动适应屏幕宽度</a>
- <a href = 'http://caibaojian.com/web-app-rem.html'>rem是如何实现自适应布局的？</a>