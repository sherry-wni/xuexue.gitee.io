---
title: js
date: 2020-05-29
---
### call 和 apply 和 bind

#### call

```javascript
Function.prototype.myCall = function(context = window, ...args) {
  if (typeof this !== "function") {
    throw new TypeError("is not function");
  }
  const fn = Symbol();
  context[fn] = this;
  let res = context[fn](...args);
  delete context[fn];
  return res;
};
```

#### apply

```javascript
Function.prototype.myApply = function(context = window, args = []) {
  if (typeof this !== "function") {
    throw new TypeError("is not function");
  }
  const fn = Symbol();
  context[fn] = this;
  let res = context[fn](...args);
  delete context[fn];
  return res;
};
```

#### bind

```javascript
Function.prototype.myBind = function(context = window, ...args1) {
  if (typeof this !== "function") {
    throw new TypeError("is not function");
  }
  const _this = this;
  return function F(...args2) {
    if (this instanceof F) {
      return new _this(...args1, ...args2);
    } else {
      return _this.apply(context, [...args1, ...args2]);
    }
  };
};
```

### 防抖和节流

#### 防抖

```javascript
function f(fn, wait = 50) {
  let timer;
  return function(...args) {
    if (timer) clearTimeout(timer);
    timer = setTimeout(() => {
      fn.apply(this, args);
    }, wait);
  };
}
```

#### 节流

```javascript
function f(fn, wait = 50) {
  let lastTime = 0;
  return function(...args) {
    const nowTime = +new Date();
    if (nowTime - lastTime >= 50) {
      lastTime = nowTime;
      fn.apply(this, args);
    }
  };
}
```

#### 结合版

```javascript
function fff(fn, wait = 50) {
  let lastTime = 0;
  let timer;
  return function(...args) {
    if (nowTime - lastTime >= 50) {
      clearTimeout(timer);
      timer = null;
      lastTime = nowTime;
      fn.apply(this, args);
    } else if (!timer) {
      timer = setTimeout(() => {
        fn.apply(this, args);
      }, wait);
    }
  };
}
```

### 深拷贝和浅拷贝

#### 浅拷贝

1.

```javascript
function clone(obj) {
  let obj2 = Object.create(null);
  for (const key in obj) {
    obj2[key] = obj[key];
  }
  return obj2;
}
```

2.

```javascript
let obj1 = { ...obj };
```

3.

```javascript
let obj2 = Object.assign({}, obj1);
```

#### 深拷贝

1.

```javascript
function deepClone(obj) {
  return JSON.parse(JSON.stringify(obj));
}
```

2.

```javascript
function isObject(obj) {
  const { toString } = Object.prototype;
  return (
    toString.call(obj) === "[object Object]" ||
    toString.call(obj) === "[object Array]"
  );
}

function deepClone(obj) {
  if (isObject(obj)) {
    let o = Array.isArray(obj) ? [] : {};
    for (const key in obj) {
      if (isObject(obj[key])) {
        o[key] = deepClone(obj[key]);
      } else {
        o[key] = obj[key];
      }
    }
    return o;
  } else {
    return obj;
  }
}
```

### 数组扁平化

1.

```javascript
function flat(arr, res = []) {
  for (const i of arr) {
    if (Array.isArray(i)) {
      res = res.concat(flat(i));
    } else {
      res.push(i);
    }
  }
  return res;
}
```

2.

```javascript
function flat(arr) {
  return arr.reduce((pre, cur) => {
    return Array.isArray(cur) ? [...pre, ...flat(cur)] : [...pre, cur];
  }, []);
}

function flat(arr) {
  return arr.reduce((pre, cur) => {
    return Array.isArray(cur) ? pre.concat(flat(cur)) : pre.concat(cur);
  }, []);
}
```

3.

```javascript
function flat(arr) {
  // 去掉双引号 得到一个"1,2,3,4,5,6"的字符串
  let str = JSON.stringify(arr).replace(/(\[|\])/g, "");
  return str.split(",").map((key) => key - 0);
}
```

### 模拟 map 和 filter

#### 模拟 map

1.

```javascript
// map的参数 current当前正在处理的元素，index索引,array,当前数组
// 返回一个新数组

Array.prototype.myMap = function(handler) {
  return this.reduce((target, current, index, array) => {
    target.push(handler.call(this, current, index, array));
    return target;
  }, []);
};
```

2.

```javascript
Array.prototype.myMap = function(hanler) {
  let res = [];
  // this指向调用的数组
  for (let i = 0; i < this.length; i++) {
    res.push(hanler(this[i], i, this));
  }
  return res;
};
```

#### 模拟 filter

1.

```javascript
// map的参数 current当前正在处理的元素，index索引,array,当前数组
// 返回过滤后的数组
Array.prototype.reduceToFilter = function(handler) {
  return this.reduce((target, current, index, array) => {
    // 如果满足传入的函数
    if (handler.call(this, current, index, array)) {
      target.push(current);
    }
    return target;
  }, []);
};
```

2.

```javascript
Array.prototype.myFilter = function(hanler) {
  let res = [];
  for (let i = 0; i < this.length; i++) {
    if (hanler(this[i], i, this)) {
      res.push(this[i]);
    }
  }
  return res;
};
```



### 模拟 fill

1.

```javascript
//array.fill(value, start, end)
//value 必需。填充的值。start 可选。开始填充位置。end 可选。停止填充位置 (默认为 array.length)
Array.prototype.myFill = function(target, start = 0, end = arr.length) {
  for (let i = start; i < end; i++) {
    this[i] = target;
  }
};
```

### 模拟实现 Array.find()、Array.findIndex()

#### 模拟 find

1.

```javascript
// find() 方法返回数组中满足提供的测试函数的第一个元素的值。 否则返回 undefined
Array.prototype.myfind = function(fn, start = 0, end = this.length) {
  for (let i = start; i < end; i++) {
    if (fn.call(this, this[i], i, this)) {
      return this[i];
    }
  }
};
```

#### 模拟 findIndex

```javascript
// findIndex() 方法返回数组中满足提供的测试函数的第一个元素的索引。 否则返回 -1
Array.prototype.myFindIndex = function(fn, start = 0, end = this.length) {
  for (let i = start; i < end; i++) {
    if (fn.call(this, this[i], i, this)) {
      return i;
    }
  }
  return -1;
};
```

### 单例模式

```typescript
class Person {
  static instance: null | Person;
  private name: string;
  private age: string;
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  static createPerson(name, age) {
    if (!Person.instance) {
      Person.instance = new Person(name, age);
    }
    return Person.instance;
  }
}
```
### 数组乱序

1.

```javascript
function random(arr) {
  let len = arr.length;
  let current = len - 1;
  while (current > -1) {
    let randomNum = Math.floor(Math.random() * len);
    [arr[current], arr[randomNum]] = [arr[randomNum], arr[current]];
    current--;
  }
  return arr;
}
```

2.

```javascript
function random(arr) {
  return arr.sort(() => 0.5 - Math.random());
}
```

### 函数柯里化

```javascript
function add(...args) {
  let res = [...args];
  let adder = (...args2) => {
    res.push(...args2);
    return adder;
  };
  adder.toString = function() {
    return res.reduce((pre, current) => pre + current, 0);
  };
  return adder;
}
```

### 异步循环打印

```javascript
`打印10个10`;
for (var i = 0; i < 10; i++) {
  setTimeout(() => {
    console.log(i);
  }, 0);
}
```

1.利用 let 限制作用域

2.利用闭包保存变量

```javascript
(function(j) {
  setTimeout(() => {
    console.log(j);
  }, 0);
})(i);
```

3.利用定时器的第三个参数

```javascript
`第三个参数会被当成第一个函数的参数`;
for (var i = 0; i < 10; i++) {
  setTimeout(
    (j) => {
      console.log(j);
    },
    0,
    i
  );
}
```

### 实现继承

#### 原型链继承

```javascript
`缺点 1.创建子类实例时，无法向父类构造函数传参 2.共享父类引用类型对象`;
function Father() {}
Father.prototype.sayHi = function() {
  return `说 Hi`;
};

function Son(age) {
  this.age = age;
}
Son.prototype = new Father();
const son = new Son(15);
```

#### 构造函数继承

```javascript
`缺点 1.无法继承父类的原型`;
function Father(name) {
  this.name = name;
}
Father.prototype.sayHi = function() {
  return `说 Hi`;
};

function Son(name, age) {
  Father.call(this, name);
  this.age = age;
}

const son = new Son("zs", 15);
```

#### 组合继承

```javascript
`缺点 1.调用两次构造函数，浪费内存`;
function Father(name) {
  this.name = name;
}
Father.prototype.sayHi = function() {
  return `${this.name} 说 Hi`;
};

function Son(name, age) {
  Father.call(this, name);
  this.age = age;
}
Son.prototype = new Father();
const son = new Son("zs", 15);
```

#### 寄生式继承

```javascript
function Father(name) {
  this.name = name;
}
Father.prototype.sayHi = function() {
  return `${this.name} 说 Hi`;
};

function Son(name, age) {
  Father.call(this, name);
  this.age = age;
}

Son.prototype = Object.create(Father.prototype, {
  constructor: {
    value: Son,
  },
});
const son = new Son("zs", 15);
```

#### Class 继承

```javascript
class Father {
  constructor(name) {
    this.name = name;
  }
  sayHi() {
    console.log(`${this.name} Say Hi`);
  }
}

// 会自动继承父类的原型
class Son extends Father {
  constructor(name, age) {
    //使用super调用父类构造函数
    super(name);
    this.age = age;
  }
}

const person = new Son("zs", 15);
person.sayHi();
```

### 插入大量 DOM

```javascript
let container = document.querySelector(".container");

const sum = 1000;
const num = 20;
const loop = Math.floor(sum / num);
let count = 0;

function render() {
  const frag = document.createDocumentFragment();
  for (let i = 0; i < num; i++) {
    const div = document.createElement("div");
    div.innerHTML = `${Math.floor(count * Math.random())}`;
    frag.appendChild(div);
  }
  container.appendChild(frag);
  if (++count !== loop) {
    looper();
  }
}
function looper() {
  requestAnimationFrame(render);
}
looper();
```

#### Compile

```javascript
class Compile {
  constructor(el, vm) {
    this.$vm = vm;
    // $el挂载的就是需要处理的DOM
    this.$el = document.querySelector(el);
    // 将真实的DOM元素拷贝一份作为文档片段，之后进行分析
    const fragment = this.node2Fragment(this.$el);
    // 解析文档片段
    this.compileNode(fragment);
    // 将文档片段加入到真实的DOM中去
    this.$el.appendChild(fragment);
  }
  // https://developer.mozilla.org/zh-CN/search?q=querySelector
  // https://developer.mozilla.org/zh-CN/docs/Web/API/Node node对象
  node2Fragment(el) {
    // 创建空白文档片段
    const fragment = document.createDocumentFragment();
    let child;
    //  appendChild会把原来的child给移动到新的文档中，当el.firstChild为空时，
    // while也会结束 a = undefined  => 返回 undefined
    while ((child = el.firstChild)) {
      fragment.appendChild(child);
    }
    return fragment;
  }
  // 通过迭代循环来找出{{}}中的内容，v-xxx与@xxx的内容，并且单独处理
  compileNode(node) {
    const nodes = node.childNodes;
    // 类数组的循环
    Array.from(nodes).forEach((node) => {
      if (this.isElement(node)) {
        this.compileElement(node);
      } else if (this.isInterpolation(node)) {
        this.compileText(node);
      }
      node.childNodes.length > 0 && this.compileNode(node);
    });
  }
  // https://developer.mozilla.org/zh-CN/docs/Web/API/Node  Node.nodeType
  isElement(node) {
    return node.nodeType === 1;
  }
  // 校验是否是文本节点 并且是大括号中的内容
  isInterpolation(node) {
    return node.nodeType === 3 && /\{\{(.*)\}\}/.test(node.textContent);
  }
  compileText(node) {
    const reg = /\{\{(.*?)\}\}/g;
    const string = node.textContent.match(reg);
    // 取出大括号中的内容，并且处理
    // RegExp.$1是RegExp的一个属性,指的是与正则表达式匹配的第一个 子匹配(以括号为标志)字符串
    // 以此类推，RegExp.$2，RegExp.$3，..RegExp.$99总共可以有99个匹配
    this.text(node, RegExp.$1);
  }
  compileElement(node) {
    const nodeAttrs = node.attributes;
    Array.from(nodeAttrs).forEach((arr) => {
      if (arr.name.indexOf("v-") > -1) {
        this[`${arr.name.substring(2)}`](node, arr.value);
      }
      if (arr.name.indexOf("@") > -1) {
        // console.log(node, arr.value)
        this.eventHandle(node, arr.name.substring(1), arr.value);
      }
    });
  }
  // 因为是大括号里面的内容，所以沿用之前的逻辑，都加上watcher
  text(node, key) {
    new Watcher(this.$vm, key, () => {
      node.textContent = this.$vm[key];
    });
    // 第一次初始化界面， 不然如果不进行赋值操作，
    // 就不会触发watcher里面的回调函数
    node.textContent = this.$vm[key];
  }
  html(node, key) {
    new Watcher(this.$vm, key, () => {
      node.innerHTML = this.$vm[key];
    });
    node.innerHTML = this.$vm[key];
  }
  // 对@xxx事件的处理
  eventHandle(node, eventName, methodName) {
    node.addEventListener(eventName, () => {
      this.$vm.$methods[methodName].call(this.$vm);
    });
  }
  // v-modal的处理 不仅仅当赋值的时候回触发watcher，并且为input添加事件
  // input中的值去修改this.$data.$xxx的值，实现双向绑定
  modal(node, key) {
    console.log(node.value);
    new Watcher(this.$vm, key, () => {
      node.value = this.$vm[key];
    });
    node.value = this.$vm[key];
    node.addEventListener("input", (e) => {
      this.$vm[key] = e.target.value;
    });
  }
}
```
