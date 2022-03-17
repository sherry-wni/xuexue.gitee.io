---
title: 简易版vue
date: 2020-05-29
tags:
  - tag2
categories:
  - vue
---
## VUE

### 手写简易版 vue

https://segmentfault.com/a/1190000019884386

#### html

```html
<style>
  #app {
    border: 1px solid red;
    margin: 10px;
    padding: 20px;
  }
</style>
<body>
  <div id="app">
    <input type="text" v-modal="name" />
    <div class="outer">
      <span>{{name}}</span>
      <p><span v-html="name"></span></p>
    </div>
    <button @click="reset">重置</button>
  </div>
</body>
<script src="./wvue.js"></script>
<script>
  //  阶段一
  const data = {
    el: "#app",
    data: {
      name: "米粒",
    },
    methods: {
      reset() {
        this.name = "";
      },
    },
  };
  const app = new Wvue(data);
</script>
```

#### js

```javascript
class Wvue {
  constructor(option) {
    this.$option = option;
    this.$data = option.data;
    this.$methods = option.methods;
    this.observer(this.$data);
    new Compile(option.el, this);
  }
  observer(obj) {
    if (!obj || typeof obj !== "object") {
      return;
    }
    console.log("observer");
    Object.keys(obj).forEach((key) => {
      this.defineProperty(obj, key, obj[key]);
      this.proxyObj(key);
    });
  }
  defineProperty(obj, key, val) {
    this.observer(val);
    //---------------- 新增为每一个变量都创建管理watcher的Dep实例
    const dep = new Dep();
    Object.defineProperty(obj, key, {
      get() {
        console.log("defineProperty获取");
        // 每次访问name 都会创建一个watcher，并加入到Dep中
        Dep.target !== null && dep.addDep(Dep.target);
        return val;
      },
      set(newVal) {
        console.log("defineProperty更新了", newVal);
        val = newVal;
        dep.notify();
      },
    });
  }

  proxyObj(key) {
    Object.defineProperty(this, key, {
      get() {
        console.log("proxyObj获取");
        return this.$data[key];
      },
      set(newVal) {
        console.log("proxyObj更新", newVal);
        this.$data[key] = newVal;
      },
    });
  }
}
// -----------新增Watcher类 用于根据通知触发绑定的回调函数
class Watcher {
  constructor(vm, key, cb) {
    this.$vm = vm;
    this.$key = key;
    this.$cb = cb;
    // 用一个全局变量来指代当前watch
    Dep.target = this;
    console.log("Watcher-------");
    // 实际是访问了this.name，触发了当前变量的get，
    // 当前变量的get会收集当前Dep.target指向的watcher,即当前watcher
    this.$vm[this.$key];
    Dep.target = null;
  }
  update() {
    // 执行
    this.$cb.call(this.$vm, this.$vm[this.$key]);
  }
}
// -----------新增Dep类 用于收集watcher
class Dep {
  constructor() {
    this.dep = [];
  }
  addDep(dep) {
    console.log("addDep");
    this.dep.push(dep);
  }
  notify() {
    // 通知所有的watcher执行更新
    this.dep.forEach((watcher) => {
      watcher.update();
    });
  }
}
```