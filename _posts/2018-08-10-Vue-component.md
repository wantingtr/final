---
layout: post
title: "关于Vue的组件,生命周期与路由"
date: 2018-08-10 13:00:00
author: "wantingtr"
tags:
    - 前端
    - Vue.js
---

&nbsp;
## 父组件向子组件传值：
`v-bind:content="father-components-data"`  
**子组件利用props：[...] 接收每一个父组件传过来的值**

## 子组件向父组件传值：
`this.$emit()`  
**通过事件触发向上一层触发事件，父组件监听此事件，获取子组件带出的数据内容**

&nbsp;

以一个todolist为例：

```js
var todoItem = {
  props:['content','index'],
  //子组件利用props：[...] 接收每一个父组件传过来的值
  template:"<li @click='deleteItem'>{ {content} }</li>",
  methods:{
    deleteItem:function(){
      this.$emit("delete",this.index)
      //子组件向父组件传值: this.$emit()
      //通过事件触发向上一层触发事件，父组件监听此事件，获取子组件带出的数据内容
    }
  }
}
var app = new Vue({
  el:'#app',
  data:{
    list:[],
    inputValue:""
  },
  components:{
    todoItem: todoItem
  },
  methods:{
    buttonClick:function(){
      this.list.push(this.inputValue);
      this.inputValue = '';
    },
    deleteItem: function(index){
      this.list.splice(index,1);
    }
  }
})

```

```html
<div id='app'>
  <input type="text" v-model="inputValue">
  <button @click="buttonClick" value="">提交</button>
  <ul>
    <todo-item :content="item"
               :index="index"
               v-for="(item,index) in list"
               @delete="deleteItem">
    </todo-item>
    <!--
      :content="item"和:index="index"为父组件向子组件传值，这些值都要通过props接收
      @delete为父组件通过父组件中的deleteItem函数，监听子组件中额delete事件。
    -->
  </ul>
</div>
```


******
Vue.js的生命周期函数

![lifeCycle](/img/post/vue/lifecycle.png)

&nbsp;

**生命周期函数：Vue实例在某一时间点会自动执行的函数**

&nbsp;
## 路由

路由就是根据网址的不同，返回不同的内容给用户

> vue-router是Vue.js官方的路由插件，它和vue.js是深度集成的，适合用于构建单页面应用。  
**vue的单页面应用是基于路由和组件的**，**路由用于设定访问路径，并将路径和组件映射起来。**  
传统的页面应用，是用一些超链接来实现页面切换和跳转的。  
在vue-router单页面应用中，则是**路径之间的切换**，也就是**组件的切换。**

在一个vue工程项目文件夹中:
- `.vue `是一个单文件组件.
- 路由的配置都放在router这个文件夹下`index.js`里
- APP是整个应用的根组件
- `<router-view/>`这个标签显示的内容就是当前路由地址对应的路由内容(即`index.js`里对应的配置项)
