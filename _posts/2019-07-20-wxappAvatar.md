---
layout: post
title: "利用flex布局实现仿微信群的多头像九宫格自适应布局"
#subtitle: ""
date: 2019-07-20 17:00:00
author: "wantingtr"
tags:
    - 前端
    - 微信小程序
---




# 利用flex布局实现微信群的多头像自适应布
微信群聊的头像是多个成员的组合，例如下图。
![avatar](/img/post/avatar.jpg)
可以看到根据单个小头像的大小可以分为三类，分别可以看做是整个大头像的98%，47%和31%。所以可以根据单个数组的长度，分别匹配不同的样式。而为了模拟后台返回的多头像接口，需要额外做一个判断，即最多只能显示9张头像。

```html
<div id="app">
    <div class="avatar" v-for="(avatarItem,index) in avatarList">
        <image v-for="(item,index) in avatarItem" :src="item" :class="computedAvatar(avatarItem)" v-if="index < 9"></image>
    </div>
</div>
```

```js
//计算头像布局
computedAvatar(avatarList) {
    if(avatarList.length > 4) {
        return "avatarItem--3"
    }else if(avatarList.length > 1) {
        return "avatarItem--2"
    }else {
        return "avatarItem--1"
    }
}
```
要做到多排的自适应布局，最简单的就是利用flex布局。最基本的是居中，即`justify-content: center;`和`align-content: center;`。
除此之外，当单轴线排不下后，需换行。flex的换行方式有两种。分别是`wrap`和`wrap-reverse`。这里用`wrap-reverse`。
![](https://img-blog.csdnimg.cn/20190720165645332.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dhbnRpbmd0cg==,size_16,color_FFFFFF,t_70)

```css
.avatar {
     width: 60px;
     height: 60px;
     overflow: hidden;
     display: flex;
     justify-content: center;
     align-items: center;
     align-content: center;
     flex-wrap: wrap-reverse;
     border: #000 1px solid;
     margin: 30px;
 }
```

```css
 .avatarImg {
     width: 60px;
     height: 60px;
 }

 .avatarItem--1 {
     width: 98%;
     height: 98%;
 }

 .avatarItem--2 {
     width: 47%;
     height: 47%;
     margin: 1%;
 }

 .avatarItem--3 {
     width: 32%;
     height: 30%;
     margin: 1%;
 }
```