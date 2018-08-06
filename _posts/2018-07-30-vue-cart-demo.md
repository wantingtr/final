---
layout: post
title: "利用Vue.js2.0实现购物车和地址选配功能"
#subtitle: "慕课网实战教程实践"
date: 2018-07-30 20:00:00
author: "wantingtr"
tags:
    - 前端
    - Vue.js
---


## vue中的`$http`请求服务.

#### 通过调用http服务，对.json文件发送http请求，通过遍历数组数据完成页面渲染


引入vue-resource.js，即可以使用全局的 Vue.http 或者在 Vue 实例中的 `this.$http` 调用 http 服务。

使用方法：
```js
// 全局 Vue 对象
Vue.http.get('/someUrl', [options]).then(successCallback, errorCallback);
Vue.http.post('/someUrl', [body], [options]).then(successCallback, errorCallback);

// Vue 实例
this.$http.get('/someUrl', [options]).then(successCallback, errorCallback);
this.$http.post('/someUrl', [body], [options]).then(successCallback, errorCallback);
```
部分源代码：
```
cartView: function () {
    	// var _this = this;
    this.$http.get('data/cartData.json').then(res=>{
      //then()方法是异步执行就是当.then()前的方法执行完后再执行then()内部的程序.避免了数据没获取到等的问题
  	  //此处使用了箭头函数，使得内部的this与外部this保持一致，可以不用上面声明的_this了
    this.productList = res.data.result.list;
  	  // this.totalMoney = res.data.result.totalMoney;
})
```

> 其中
  then()方法是**异步执行**。  
  就是**当.then()前的方法执行完后再执行then()内部的程序**，避免了数据没获取到等的问题。
  语法：  
  promise.then(onCompleted, onRejected);  
  Promise 为方法对象.  
  onCompleted必需，为承诺成功完成时要运行的**正确处理程序函数。**  
  onRejected可选，为承诺被拒绝时要运行的**错误处理程序函数。**



## 创建过滤器格式化价格

#### 创建过滤器，通过管道符号改变数据内容  { { item.productPrice | formatMoney } }
起初，根据教程创建了局部过滤器和全局过滤器，运行时会发现chrome一直报错
```
[Vue warn]: Failed to resolve filter: formatMoney

(found in <Anonymous>)
```
经过不断的尝试和修改，最终发现,出现这种错误应该是因为filter的代码顺序问题。  
应该**在创建 Vue 实例之前全局定义过滤器**

```js
Vue.filter("money",function(value,type){
  return ("￥" + value.toFixed(2) + type);
})
```

其中，Vue 2.0 在过滤器参数格式处
> 现在过滤器参数形式可以更好地与 js 函数调用方式一致，因此不用再用空格分隔参数：
 `<p>{ { date | formatDate 'YY-MM-DD' timeZone } }</p>``
现在用圆括号括起来并用逗号分隔：
`<p>{ { date | formatDate('YY-MM-DD', timeZone) } }</p>``




教程地址：
<a href="https://www.imooc.com/learn/796">慕课网-利用Vue2.0实现购物车和地址选配功能</a>

部分代码参考：
<a href="https://www.jianshu.com/p/ed9e98731d96">在Vue中使用http请求-from简书</a>
