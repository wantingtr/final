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

[TOC]



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
`<p>{ { date | formatDate 'YY-MM-DD' timeZone } }</p>`  
现在用圆括号括起来并用逗号分隔：  
`<p>{ { date | formatDate('YY-MM-DD', timeZone) } }</p>`


## 通过动态绑定HTML Class，设置商品选择情况

#### 利用`v-bind:class="{'check':item.checked}"`,将item.checked与HTML中的check状态动态绑定。  

`@click="selectProduct(item)"`
此处应为item设定一个属性`checked`，以确定商品是否被选定。  

> 利用`Vue.set( target, key, value )`向响应式对象中添加一个属性，并确保这个新属性同样是响应式的，且触发视图更新。  
它必须用于向响应式对象上添加新属性，因为 Vue 无法探测普通的新增属性 (比如 this.myObject.newProperty = 'hi')    
注意对象不能是 Vue 实例，或者 Vue 实例的根数据对象。

```
selectProduct:function(item){
  if(typeof item.checked == "undefined"){
    Vue.set(item,'checked',true); //若item没有checked这个属性，注册该属性
  }else{
    item.checked = !item.checked;
  }
```


## 全选/取消全选按钮：

#### 绑定一个点击函数，若参数为true，就全选，false就取消全选。  
`class="{'check':checkAllFlag}" @click="checkAll(true)"`

因为无论是全选还是取消全选，都要对所有商品进行遍历，**改变每一个商品的checked属性**，用`this.productList.forEach(function(item){ ... })`函数，对每一个商品进行遍历，改变`item.checked`属性值

特殊的，要注意到，当页面加载完成后，还未点击选择按钮，即此时`item.checked`属性还不存在，所以遍历时要判断是否存在`item.checked`属性。
```js
checkAll:function(flag){
  this.checkAllFlag = flag;
  var _this = this;
  this.productList.forEach(function(item){
    if(typeof item.checked == "undefined"){
      Vue.set(item,'checked',_this.checkAllFlag);
    }else{
      item.checked = _this.checkAllFlag;
    }
  })
}
```

## 实现商品金额的计算

单个商品的金额计算：`{ { item.productPrice * item.productQuantity | money('元')} }`  
总金额的计算：`{ {totalMoney | money('元')} }`

要计算totalMoney，可定义一个函数，分析其动态变化情况来源。

对于totalMoney来说，一共有三种动态变化情况：
1. 单个商品的选择与否
2. 单个商品的数量改变
3. 全选/ 取消全选

所以，对于每一种变化情况，都要对totalMoney**进行重新计算**，即在相应函数的最后，调用一次`this.calcTotalPrice();`

对于calTotalPrice函数，先对totalMoney清零，再遍历数组productList,  
`_this.totalMoney += item.productPrice*item.productQuantity;` 实现总金额的计算。

```js
calTotalPrice:function(){
  var _this = this;
  this.totalMoney = 0;
  this.productList.forEach(function(item){
    if(item.checked){
      _this.totalMoney += item.productPrice*item.productQuantity;
    }
  });
}
```

## 实现单个商品的删除
具体要求：点击商品删除按钮，跳转至确认删除界面，点击yes，删除该商品；点击no，返回至上一页。

```js
delConfirm:function(item){
  this.delFlag = true;
  this.curProduct = item;//确认当前删除的对象
},
delProduct:function(){
  var index = this.productList.indexOf(this.curProduct);//确定要删除对象的索引值
  this.productList.splice(index,1);//删除该对象
  this.delFlag = false;
}
```







******
教程地址：
<a href="https://www.imooc.com/learn/796">慕课网-利用Vue2.0实现购物车和地址选配功能</a>

相关资料参考：
- <a href="https://cn.vuejs.org/">Vue.js官方文档</a>
- <a href="https://www.jianshu.com/p/ed9e98731d96">在Vue中使用http请求-from简书</a>
