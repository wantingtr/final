---
layout: post
title: "关于Vue的组件传值与生命周期"
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

![lifeCycle](/img/post/vue/lifeCycle.png)

&nbsp;

**生命周期函数：Vue实例在某一时间点会自动执行的函数**
