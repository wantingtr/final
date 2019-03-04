---
layout: post
title: "JavaScript中的原型与原型链全解析"
#subtitle: ""
date: 2019-02-25 23:00:00
author: "wantingtr"
tags:
    - 前端
    - JavaScript
---

## prototype

只要创建一个函数，就会自动的为这个函数创建一个prototype属性，这个属性是一个**指针**，**指向函数的原型对象**。  
在默认情况下，所有原型对象都会自动获得一个constructor属性（是一个指针），指向原来的函数。

![img](/img/post/JS/JS-prototype.jpg)

### 原型对象有什么好处？
#### 让所有的对象实例共享属性和方法
```js
function person(name) {
    this.sayName = new Function("alert(this.name)")
}

var person1 = new person('david');
var person2 = new preson('leslie');

console.log(person1.sayName == person2.sayName) //false
```
我们在创建对象时，希望所有的对象实例应该共享构造函数中的属性和方法，但是**使用构造函数声明对象，不同实例上的同名函数是不相等的**，所以需要通过原型模式来解决。

```js
function person() {

}

person.prototype.name = 'jackson'
person.prototype.sayName = function() {
    alert(this.name)
}

var person1 = new person()
var person2 = new person()

console.log(person1.sayName == person2.sayName) //true
```
使用原型对象，所有的对象实例会共享原型对象中的实例和方法


### __proto__
上面说的prototype是每个函数都有的属性，而**__proto__则是每个对象实例都有的属性**，有时也叫也叫`[[Prototype]]`。  
**在创建对象实例时，自动创建__proto__属性指向原型对象**

![img](/img/post/JS/JS-prototype1.jpg)

上图中的两个实例，都包含上述所说的__proto__，并指向原型对象

上面说到，不同的实例是共享所有原型对象中的属性和函数的，那如果该实例**重写了原型对象中的属性时，就会将其屏蔽（屏蔽而不是覆盖！），即这个属性是这个实例所独有的。**。  

1. 那解析器是怎么解析的呢？  
- 当代码读取对象的某个属性或者对象时，**先在实例中搜索**，若找到，直接返回；若没找到，则**再次搜索实例的__proto__指向的原型对象**，若找到，返回。  
这也是多个对象实例共享原型中的属性和方法的基本原理。  

> 使用`hasOwnProperty()`可以检测一个属性是存在在实例中，还是存在在原型中。当其存在在实例中，返回true，反之返回false。


## JS中创建对象的三种方式
之所以将其分为三种不同的方式，也是由于其__proto__指向的对象不同。

### 字面量方式

```js
var obj1 = { name: 'obj1';}
var obj11 = new Object( {name: "obj1";} )
```
对象的原型链指向Object
![img](/img/post/JS/object.jpg)

### 使用显式构造函数（构造器方式）

```js
var M = function(name) {
    this.name = name
}
var obj2 = new M();
```
![img](/img/post/JS/constructor.jpg)


### 使用Object.create

```js
var P = { name: 'obj3' }
var obj3 = Objecr.create(P)
```

![img](/img/post/JS/objectCreate.jpg)

## 原型链
利用原型让子对象继承父对象的属性和方法，这样层层递进，就形成了原型链。
![img](/img/post/JS/chain.jpg)

示例代码：
```js
function superType() {
    this.property = true //创建父构造函数，包含的实例属性会被继承到子原型对象中
}

superType.prototype.getSuperValue = function () {
    return this.property //父原型对象中的方法，不会被继承到子原型对象中，而是共享
}

function subType() {
    this.subproperty = false //创建子构造函数
}

subType prototype = new superType() //继承 !important

subType.prototype.getSubValue = function() {
    return this.subproperty
}

var instance = new subType() //实例
console.log(instance.getSuperValue()) //true
```

在前面说过读取属性时，先搜索实例对象，再网上搜索实例原型。  
在通过原型链实现继承的情况下，仍然按照上述方法进行搜索：
1. 搜索实例
2. 搜索子原型对象`subType.prototype`
3. 搜索父原型对象`superType.prototype`。

## 加上JS默认对象Object
![img](/img/post/JS/JSchain.jpg)
JS中的所有对象都是Object的实例，因此默认原型对象都会包含__proto__指向Object.prototype。

## 确定原型和实例之间的关系

### instanceof
```js
instace instanceof Object  //true
instace instanceof superType //true
instace instanceof subType //true
``` 

### isPrototypeOf()
```js
Object.prototype.isPrototypeOf(instance) //true
superType.prototype.isPrototypeOf(instance) //true
subType.prototype.isPrototypeOf(instance) //true
```

参考资料：
- 《JavaScript高级程序设计》
- <a href="https://www.cnblogs.com/shuiyi/p/5305435.html">三张图搞懂JavaScript的原型对象与原型链</a>