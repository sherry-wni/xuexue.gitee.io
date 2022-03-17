---
title: js 进阶
date: 2019-09-21
tags:
  - tag1
categories:
  - js
---

### async 实现 promise.all

```javascript
用async实现promise.All;
async function asyncAlls(jobs) {
  try {
    let results = jobs.map(async (job) => await job);
    let res = [];
    for (const result of results) {
      res.push(await result);
    }
    return res;
  } catch (error) {
    throw new Error(error);
  }
}
```
### 模仿 promise

#### TypeScript 版

```typescript
type PromiseState = "pending" | "resolved" | "rejected";

interface PromiseResolve<T> {
  (value: T): void;
}
interface PromiseReject<T> {
  (value: T): void;
}

interface MyPromiseMethod<T> {
  then: (onFulfilled: any, onRejected: any) => void;
  all: (promises: MyPromise<T>) => any;
  race: (promises: MyPromise<T>[]) => any;
  reject: (reason: any) => any;
  resolve: (value: any) => any;
  catch: (error: any) => any;
  finally: (fn: any) => any;
}

class MyPromise<T> implements MyPromiseMethod<T> {
  state: PromiseState;
  value: T;
  reason: T;
  successCb: PromiseResolve<T>[];
  failCb: any[];
  constructor(exec) {
    this.state = "pending";
    this.value = null;
    this.reason = null;
    this.successCb = [];
    this.failCb = [];
    const resolve: PromiseResolve<T> = (value: T) => {
      if (this.state === "pending") {
        this.value = value;
        this.state = "resolved";
        this.successCb.forEach((fn) => {
          fn(value);
        });
      }
    };
    const reject: PromiseReject<T> = (reason: T) => {
      if (this.state === "pending") {
        this.reason = reason;
        this.state = "rejected";
        this.failCb.forEach((fn) => {
          fn(reason);
        });
      }
    };
    try {
      exec(resolve, reject);
    } catch (e) {
      reject(e);
    }
  }
  then(onFulfilled, onRejected) {
    onFulfilled =
      typeof onFulfilled === "function" ? onFulfilled : (value) => value;
    onRejected =
      typeof onRejected === "function" ? onRejected : (reason) => reason;
    if (this.state === "pending") {
      this.successCb.push(onFulfilled);
      this.failCb.push(onRejected);
    }
    if (this.state === "resolved") {
      onFulfilled(this.value);
    }
    if (this.state === "rejected") {
      onFulfilled(this.reason);
    }
  }
  all(promise) {
    let count = 0;
    let res = [];
    return new MyPromise((resolve, reject) => {
      for (let i = 0; i < promise.length; i++) {
        (promise[i] as MyPromise<T>).then(
          (value) => {
            res.push(value);
            count++;
            if (count === promise.length) {
              resolve(res);
            }
          },
          (e) => {
            reject(e);
          }
        );
      }
    });
  }
  race(promise) {
    return new MyPromise((resolve, reject) => {
      for (let i = 0; i < promise.length; i++) {
        (promise[i] as MyPromise<T>).then(
          (value) => {
            resolve(value);
          },
          (e) => {
            reject(e);
            return;
          }
        );
      }
    });
  }
  reject(reason) {
    return new MyPromise((resolve, reject) => {
      reject(reason);
    });
  }
  resolve(value) {
    return new MyPromise((resolve, reject) => {
      resolve(value);
    });
  }
  catch(onRejected) {
    return this.then(null, onRejected);
  }
  finally(fn) {
    this.then(
      (value) => {
        fn();
        return value;
      },
      (err) => {
        fn();
        return err;
      }
    );
  }
}
function testPromise(value) {
  return new MyPromise((resolve, reject) => {
    setTimeout(() => {
      if (value > 5) {
        resolve("大于5");
      } else {
        reject("小于等于5");
      }
    }, 50);
  });
}
function testPromise2(value) {
  return new MyPromise((resolve, reject) => {
    setTimeout(() => {
      if (value > 5) {
        resolve("大于5");
      } else {
        reject("小于等于5");
      }
    }, 50);
  });
}
MyPromise.prototype.all([testPromise2(11), testPromise(10)]).then(
  (value) => {
    console.log(value);
  },
  (e) => {
    console.log(e);
  }
);
```

#### JavaScript 版本

```javascript
```

### 模拟 AJAX

```javascript
function ajax(config: AjaxConfig) {
  return new Promise((resolve, reject) => {
    const { data = null, url, method = "Get" } = config;
    const request = new XMLHttpRequest();
    request.open(method, url, true);
    request.onreadystatechange = function() {
      if (request.readyState === 4 && request.status === 200) {
        resolve(request.responseText);
      } else {
        reject(request.statusText);
      }
    };
    request.send(JSON.stringify(data));
  });
}
```

### 模拟 new 的过程

1.

```javascript
function myNew(fn, ...args) {
  //创建一个新对象，将新对象的__proto__ 只想构造函数的原型
  let obj = Object.create(fn.prototype);
  let res = fn.apply(obj, args);
  return res instanceof Object ? res : obj;
}
```

### 模拟实现 Object.create 方法

1.

```javascript
// 用于创建一个新对象,被创建的对象继承另一个对象(o)的原型
function create(obj) {
  function F() {}
  F.prototype = obj;
  return new F();
}
```

### 模拟实现 instanceof 的功能

1.

```javascript
function myInstanceof(obj, F) {
  while (obj.__proto__) {
    if (obj.__proto__ === F.prototype) {
      return true;
    }
    obj = obj.__proto__;
  }
  return false;
}
```

### 使用 setTimeout 实现 setInterval 方法

1.

```javascript
function recursive(fn, wait = 4000) {
  fn();
  setTimeout(function() {
    recursive(fn, wait); //递归，每隔4秒调用一次recursive()
  }, wait);
}
```

### 实现 JSONP

```javascript
//https://sp0.baidu.com/su?wd=Java&cb=cb';
function jsonP(url) {
  let script = document.createElement("script");
  script.scr = url;
  document.body.appendChild(script);
}
function cb(value) {
  console.log(value);
}
```

### promise 实现 sleep 函数
```javascript
function sleep(wait = 50) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve();
    }, wait);
  });
}
```

