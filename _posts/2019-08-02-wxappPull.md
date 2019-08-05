---
layout: post
title: "小程序实现单个卡片左滑显示按钮，并防止上下滑动干扰"
#subtitle: ""
date: 2019-08-02 18:00:00
author: "wantingtr"
tags:
    - 前端
    - 微信小程序
---


实现类似ios端微信的左滑显示置顶、删除按钮的功能，首先需将按钮部分设为绝对定位，并且right设为负值溢出屏幕。利用小程序事件处理的api，分别读取触摸开始，触摸移动时，触摸结束的X/Y坐标，根据差值来改变整个卡片的位置。

这里有一个细节，为了**防止按钮的点击干扰到卡片主部分的滑动**，需要将整个卡片分为两部分。

```js
<View style={`${positionStyle[index]}`}>
    <View 
        onTouchStart={this.moveTaskStart}
        onTouchMove={this.moveTask}
        onTouchEnd={this.moveTaskEnd}
    >
        这是主要的卡片部分
    </View>

    <View style="right: -260px;">这是滑动后出现的按钮部分</View>
</View>
```


这里使用的是Taro框架，方法与原生事件api一样，在开始滑动时分别存入X,Y坐标，在手指移动过程中计算移动的距离，当X的移动距离大于整个按钮部分的一班，展开按钮。

核心功能的实现，只需要分析X坐标即可，但因为**用户可能在上下滑动过程中x坐标也会偏移造成按钮的展开**，所以需要**在move与end两部分都计算垂直坐标的改变与水平坐标改变形成的角度**，当角度过大，视为上下滑动，按钮不展开。
完整代码如下：
```js
moveTaskStart(e) {
    if (e.touches.length == 1) {
        //触摸屏上只有一个触摸点
        this.setState({
            // 开始触摸屏幕的X坐标
            startX: e.touches[0].clientX,
            startY: e.touches[0].clientY
        });
    }
}

moveTask(e) {
    if (e.touches.length == 1) {
        let moveX = e.touches[0].clientX;
        let moveY = e.touches[0].clientY;
        // 移动距离
        let disX = this.state.startX - moveX;
        let disY = this.state.startY - moveY;
        let angle = disY > 0 ? disY / disX : -disY / disX;
        let btnWidth = this.state.btnWidth;
        let txtStyle = "";
        if (disX == 0 || disX < 30) {
            //右滑动
            txtStyle = "left:0px";
        } else if(angle > 0.5) {
            return;
        } else if (disX > 30 && angle < 0.5) {
            txtStyle = `left: -${disX}px`;
            if (disX >= btnWidth) {
                //距离最大值
                txtStyle = `left: -${btnWidth}px`;
            }
        }
        let index = e.currentTarget.dataset.index;
        let list = this.state.positionStyle;
        this.setState({
            positionStyle: list
        });
    }
}

moveTaskEnd(e) {
    let txtStyle;
    if (e.changedTouches.length == 1) {
        let endX = e.changedTouches[0].clientX;
        let moveY = e.changedTouches[0].clientY;
        //移动距离
        let disX = this.state.startX - endX;
        let disY = this.state.startY - moveY;
        //移动角度
        let angle = disY > 0 ? disY / disX : -disY / disX;
        let btnWidth = this.state.btnWidth;
        //如果距离大于按钮宽度的一半，并且移动角度较小，显示按钮
        if(disX > (btnWidth / 2) && angle < 0.5) {
            txtStyle = `left:-${btnWidth}px`
        }else {
            txtStyle = "left:0px"
        }
        let index = e.currentTarget.dataset.index;
        let list = this.state.positionStyle;
        list[index] = txtStyle;
        this.setState({
            positionStyle: list
        });
    }
}
```
    