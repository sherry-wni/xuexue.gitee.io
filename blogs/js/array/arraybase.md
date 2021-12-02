---
title: 数组方法重写
date: 2019-10-2
tags:
  - tag1
categories:
  - js
---
## deepClone
```javascript  
 const obj = {
  name :"小野",
  age:18,
  career:'学生',
  info:{
   field:['12','34','56'],
   framwork:['aa','bb','cc'],
   student:[
    {name:'小红',age:18},
    {name:'小张',age:11}
   ],
   hobby:['w','x']
  }
 }
Array.prototype.deepClone = function(origin,target){
  var tar = target || {}
  for(var k in origin){
   if(origin.hasOwnProperty(k)){
     if(typeof origin[k] === 'object' && origin[k] !== null){
      tar[k] =  Object.prototype.toString.call(origin[k]) === '[object Object]' ? {} :[];
       deepClone(origin[k],tar[k]);
      }else{
        tar[k] = origin[k];
      }
   }
  }
  return tar;
}
```
## forEach
```javascript
 const arr = [
  {name:'小红',age:18},
  {name:'小明',age:25},
 ];
 Array.prototype.myForeach = function(cb){
   var _arr = this;
   var _len = _arr.length;
   var _arg2 = arguments[1] || window;
   for(var i = 0 ; i < _len;i++){
    cb.apply(_arg2,[_arr[i],i,_arr]);
   }
 }
```
## map
```javascript
const arr = [
  {name:'小红',age:18},
  {name:'小明',age:25},
] 
Array.prototype.myMap = function(cb){
 var _arr = this;
 var _len = _arr.length;
 var _arg2 = arguments[1] || window;
 var _res;
 var _item;
 var newArr;
 for(var i = 0 ; i < _len; i++){
  _item = deepClone(_arr[i]);
  _res = cb.apply(_arg2,[_item,i,_arr]);
  _res && newArr.push(_item);
 }
 return newArr;
}
```
## filter
```javascript 
Array.prototype.myFilter = function(cb){
 var _arr = this;
 var _len = _arr.length;
 var _arg2 = arguments[1] || window;
 var item;
 var newArr = [];
 for(var i = 0 ; i < _len;i++){
  item = deepClone(_arr[i]);
  cb.apply(_arg2,[item,i,_arr]) ? newArr.push(item) : ''; 
 }
 return newArr
}
```
## every
```javascript 
Array.prototype.myEvery = function(cb){
var _arr = this;
var _len = _arr.length;
var _arg2 = arguments[1] || window;
var _flag = true;
for(var i = 0; i < _len; i++){
 if(!cb.apply(_arg2,[_arr[i],i,_arr])){
   _flag = false;
   break;
  }
 }
 return _flag;
}
```
## some
```javascript
Array.prototype.mySome = function(cb){
 var _arr = this;
 var _len = _arr.length;
 var _arg2 = arguments[1] || window;
 var _flag = false;
 for(var i = 0; i < _len; i++){
   if(cb.apply(_arg2,[_arr[i],i,_arr])){
   _flag = true;
   break;
 }
 return _flag;
 }
}
```
## reduce
```javascript
Array.prototype.myReduce = function(cb,initialValue){
 var _arr = this;
 var _len = _arr.length;
 var _arg3 = arguments[2] || window;
 var _item;
 for(var i = _len; i > 0;i--){
  _item = deepClone(_arr[i])
  initialValue = cb.apply(_arg3,[initialValue,_item,i,_arr]);
 }
 return initialValue
}
```

