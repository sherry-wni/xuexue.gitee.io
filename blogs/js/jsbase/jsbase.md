---
title: js 数据类型的认识以及判断
date: 2020-05-05
tag:
  - tag1
categories:
  - js
---
在`ES5`的时候，我们认知的数据类型确实是 6种：Number、String、Boolean、undefined、object、Null。
`ES6` 中新增了一种 Symbol 。这种类型的对象永不相等，即始创建的时候传入相同的值，可以解决属性名冲突的问题，做为标记。
谷歌67版本中还出现了一种 bigInt。是指安全存储、操作大整数。

::: tip 介绍

1.js的数据类型有8种如下
```
Number,String,Boolean,Null,undefined,object,symbol,bigInt。
```
2.js的基本类型
```
String,Number,boolean,null,undefined
```
3.js的引用类型
```
 object,array,function
```
:::

## 1.typeof
typeof 可以判断基础数据类型加function,并且返回一个数据类型的一个字符串。

##### number
```script
typeof 18 === 'number';
typeof NaN === 'number'; 
typeof Infinity === 'number';
typeof Number('1') === 'number'; 
typeof 42n === 'bigint';
```
##### String
```script
typeof '' === 'string';
typeof (typeof 1) === 'string'; 
typeof '1' === 'string';
```
##### Boolean
```script
typeof false === 'boolean';
typeof Boolean(1) === 'boolean'; 
typeof !!(1) === 'boolean';
```
##### Symbols
```script 
typeof Symbol() === 'symbol'
typeof Symbol('foo') === 'symbol'
typeof Symbol.iterator === 'symbol
```
##### Undefined
```script 
typeof undefined === 'undefined';
typeof declaredButUndefinedletiable === 'undefined';
typeof undeclaredletiable === 'undefined';
```
##### Object
```script 
typeof {a: 1} === 'object';
typeof [1, 2, 4] === 'object';
typeof new Date() === 'object';
typeof /regex/ === 'object';
```
##### Functions
```script 
typeof function() {} === 'function';
typeof class C {} === 'function';
typeof Math.sin === 'function';
```
## 2.instanceof
判断是否是某个类或者某个构造函数的实例
```script
 function Person(){};
 let a = new Person();
 console.log(a instanceof Person) //true
```
## 3.Object.prototype.toString()
可以判断任何数据类型。
原理：子类型在内部借用了object的tostring()方法。
```script
    let str = 'hello';
    console.log(Object.prototype.toString.call(str));//[object String]
    let bool = true;
    console.log(Object.prototype.toString.call(bool))//[object Boolean]
    let num = 123;
    console.log(Object.prototype.toString.call(num));//[object Number]
    let nul = null;
    console.log(Object.prototype.toString.call(nul));//[object Null]
    let und = undefined;
    console.log(Object.prototype.toString.call(und));//[object Undefined]
    let oDate = new Date();
    console.log(Object.prototype.toString.call(oDate));//[object Date]
    let json = {};
    console.log(Object.prototype.toString.call(json));//[object Object]
    let arr = [];
    console.log(Object.prototype.toString.call(arr));//[object Array]
    let reg = /a/;
    console.log(Object.prototype.toString.call(reg));//[object RegExp]
    let fun = function(){};
    console.log(Object.prototype.toString.call(fun));//[object Function]
    let error = new Error();
    console.log(Object.prototype.toString.call(error));//[object Error]
```


