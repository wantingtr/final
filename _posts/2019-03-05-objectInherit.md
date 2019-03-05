---
layout: post
title: "JavaScript中几种对象继承的方式对比"
#subtitle: ""
date: 2019-02-25 23:00:00
author: "wantingtr"
tags:
    - 前端
    - JavaScript
---

## 继承方式

### 1. 借助构造函数实现继承

```js
function parent() {
    this.name = 'parent'
}
parent.prototype.say = function () {}
function child() {
    parent.call(this) //或apply,改变函数运行的上下文（this）
    //将父构造函数上的this指向子构造函数的实例上
    this.type = 'child'
}
console.log(new chiid().say())   //error！！
```
缺点：
- 父级的原型对象并没有被子对象继承
- 只实现部分继承（只能继承构造函数上的属性，不能继承原型对象的属性）


### 2. 借助原型链实现继承（原型式继承）

```js
//借助原型链实现继承
function parent() {
    this.name = 'name'
    this.play = [1, 2, 3]
}
function child() {
    this.type = 'child'
}
child.prototype = new parent() //!important

new child().__proto__ === child2.prototype //true

var obj1 = new child()
var obj2 = new child()

obj1.play.push(4)

obj1.play //[1,2,3,4]
obj2.play //[1,2,3,4]  !error

obj1.__proto__ === obj2.__proto__ //true
```
缺点：
- 所有实例公用原型链中的原型对象


### 3. 组合方式继承

```js
//组合方式继承
function parent() {
    this.name = 'name'
    this.play = [1, 2, 3]
}
function child() {
    parent.call(this) //每次新建实例时都会执行一次构造函数
    this.type = 'child'
}
child.prototype = new parent()

obj1.play.push(4)

obj1.play //[1,2,3,4]
obj2.play //[1,2,3]  
```
解决上面原型链继承共享原型对象的问题

缺点：
- 每次新建实例时都会执行一次构造函数（性能不高）


### 4. 组合方式优化

```js
function parent() {
    this.name = 'name'
    this.play = [1, 2, 3]
}
function child() {
    parent.call(this)//获取父级构造函数内的属性
    this.type = 'child'
}
child.prototype = parent.prototype //继获取父级原型对象中的属性
//子类和父类的prototype是同一个，里面包含的constructor也是同一个，都指向父类

var obj = new child()

console.log(obj.constructor) //parent  ！error
```
解决每次都new实例都会执行一次构造函数的问题

缺点：
- 无法判断实例是new哪个对象，构造函数永远都是父类的对象


### 5. 寄生组合式继承（最优）

```js
//寄生组合式继承
function parent() {
    this.name = 'name'
    this.play = [1, 2, 3]
}
function child() {
    parent.call(this)
    this.type = 'child'
}
child.prototype = Object.create(parent.prototype)//创建中间空对象，隔离子类和父类的prototype，可以修改子类的constructor

child.prototype.constructor = child //修改子类的constructor

var obj = new child()

obj instance of child //true
obj instance of parent //true

obj.constructor = child //true
```

