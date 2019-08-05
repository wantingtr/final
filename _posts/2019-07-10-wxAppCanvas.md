---
layout: post
title: "利用Canvas实现小程序分享海报的生成与本地保存"
#subtitle: ""
date: 2019-07-10 18:00:00
author: "wantingtr"
tags:
    - 前端
    - 微信小程序
---

虽然说这是个很常见的需求了，但真正实现起来才发现其中的坑是如此之多。尤其是微信小程序对于Canvas的支持非常之不友好。

## 实现过程
分享海报通常在小程序里是以弹窗的形式呈现，并需要利用canvas绘制图片并保存至本地，由用户自行分享到朋友圈。刚开始我是直接利用canvas渲染到小程序页面中，而因为这个需求需要从云端获取弹窗的背景图，这一部分延迟比较长，所以还是采用原生HTML渲染 + canvas 的形式, 将canvas设为absolute定位并使其位于页面之外。如果是没有这部分的请求延迟，可以直接渲染Canvas，工作量也会降低一些。

### 在组件中渲染canvas
因为是在弹窗组件中渲染canvas，如果使用原始语法绘制，会发现渲染失败，并报错canvas为空。这是因为在自定义组件下，对于`createCanvasContext`这个API，一定要加上第二个参数this，表示在这个自定义组件下查找拥有 canvas-id 的 canvas ，如果省略则不在任何自定义组件内查找。
但对于Taro框架来说：**this指向的是Taro页面或组件实例。**，如果要获取到**Taro的页面和组件所对应 小程序原生页面和组件实例**，需要使用`this.$scope`。

`const ctx = Taro.createCanvasContext('share-photo', this.$scope);`


### 使用`getImageInfo`获取canvas的图片资源
微信目前不支持在canvas中直接获取远程图片资源并渲染，所以需要通过`getImageInfo`这个api先获取图片并加载到用户本地之后再调用渲染。
```js
Taro.getImageInfo({
    src: bgImg
}).then((res) => {
    console.log(res.path);
    this.setState({
        bgImage: res.path,
    })
})

//...
ctx.drawImage(bgImage, 0, 0, 345 * scale, 486 * scale);
```
虽然说可以成功渲染，但**延迟非常严重**，可能是因为网速问题，也可能是因为图片大小问题。在开发者工具中的延迟已经有将近4秒，就算是小程序页面HTML渲染,在用户保存图片的时候仍然会出现绘制未成功的情况，所以我这里选择在上一个页面先提前将canvas要用到的图片资源获取完毕，这样延迟的情况基本可以忽略。

### 根据机型适配尺寸
因为使用的是Taro框架，开发过程中Taro会自动将px转化为rpx，但canvas里的定位还是绝对的。需要前端手动获取一下当前用户的屏幕大小并计算出比例。在canvas中的所有定位和尺寸部分中都需要乘这个比例。

```js
const ret = Taro.getSystemInfoSync();
const windowWith = ret.windowWidth;
const scale = windowWith / 375.0;

// ...

ctx.drawImage(require('@assets/images/card.png'), 15 * scale, 288 * scale, 315 * scale, 178 * scale);
```

### 将canvas保存成图片
```js
Taro.canvasToTempFilePath({
    canvasId: 'share-photo',
    success: (res) => {
        if (res.tempFilePath) {
            Taro.saveImageToPhotosAlbum({
                filePath: res.tempFilePath
            }).then(() => {
                Taro.showToast({
                    title: '保存成功',
                    duration: 3000
                });
            }, (err) => {
                Taro.showModal({
                    title: '温馨提示',
                    content: '保存失败了哦，请重新生成再试试哈',
                    showCancel: false,
                    confirmText: '我知道了'
                })
            });
        } else {
            Taro.hideLoading();
            Taro.showModal({
                title: '温馨提示',
                content: '保存失败了哦，请重新生成再试试哈',
                showCancel: false,
                confirmText: '我知道了'
            })
        }
    },
    complete: () => {
        Taro.hideLoading();
    }
}, this.$scope);
```

### 其他一些坑

#### canvas在安卓机上的绘制问题
同样的一个canvas在开发者工具以及苹果机上是不会有任何问题的，但对于一些圆角矩形或比较复杂的绘制路线，有可能在安卓机上出现一些奇奇怪怪的bug。这个的解决方法只有让设计单独切个图出来了。

#### canvas的层级问题
在微信小程序中，canvas是原生组件，默认是出于最顶层的，若出现页面遮挡问题，需要用` cover-view`和` cover-image`解决。