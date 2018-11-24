---
layout: post
title: "JS的变量 原型与原型链 作用域 闭包"
subtitle: " "
date: 2018-11-16 20:00:00
author: "wantingtr"
tags:
    - 前端
    - JavaScript
---

## 数据形式
### 张量(Tensor)
1. 不可变
2. 一组数值形成一维或多维数组
```js
// 2x3 Tensor
const shape = [2, 3]; // 2 行, 3 列
//shape属性来定义这一组数值如何组成张量
const a = tf.tensor([1.0, 2.0, 3.0, 10.0, 20.0, 30.0], shape);
//tf.tensor为构造函数
a.print(); // 打印张量值
// 输出:    [[1 , 2 , 3 ],
//          [10, 20, 30]]

// shape也可以用下面的方式实现:
const b = tf.tensor([[1.0, 2.0, 3.0], [10.0, 20.0, 30.0]]);
b.print();
// 输出:    [[1 , 2 , 3 ],
//          [10, 20, 30]]
```
除此之外，还有
`tf.scalar（零维）`,`tf.tensor1d（一维）`,`tf.tensor2d（二维）`,`tf.tensor3d（三维）`,`tf.tensor4d（四维）`,`tf.ones（值全是1）`,`tf.zeros（值全是0）`


## 变量(Variable)
变量是**通过张量进行初始化得到的。**
且变量的值是可变的，可以通过assign方法，分配一个新的张量来改变变量的值  
```js
const initialValues = tf.zeros([5]);
const biases = tf.variable(initialValues); // 初始化biases变量
biases.print(); // 输出: [0, 0, 0, 0, 0]

const updatedValues = tf.tensor1d([0, 1, 0, 1, 0]);
biases.assign(updatedValues); // 通过assign方法改变 biases的值
biases.print(); // 输出: [0, 1, 0, 1, 0]
```
所以，变量由张量生成，且张量不可变而变量可变。