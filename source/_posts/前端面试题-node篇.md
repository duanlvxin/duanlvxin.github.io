---
title: 前端面试题-node篇
date: 2024-09-01 12:25:00
tags:
  - 面试
  - node
categories:
  - 面试
cover: /img/mianshi.jpg
---

## 如何理解node中模块的概念

模块就是通过 *函数作用域*，把变量和函数，进行隔离

```js
function moduleA(){
  var a = {
    foo: '1',
    bar: '2',
    baz: '3'
  }
  return a
}

moduleA().foo
```


### 模块化
```js
var module = {
  exports: {

  }
}

function resolve(module, exports) {
  var bar = 'hello'
  var baz = 'world'
  module.exports = {
    bar, baz
  }

  // exports.bar = bar // 可以
  // exports = {bar, baz} // 无效
}

resolve(module, module.exports);
```

=> 多个模块

```js
var module = {
  './src/A.js': {
    exports: {

    }
  },
  './src/B.js': {
    exports: {

    }
  }
}

function require(id) {
  return module[id].exports
}

(function(module, exports, require){
  // ./src/A.js文件内容
  var foo = require('./src/B.js')
  var bar = '1'
  var baz = '2'
  module.exports = {
    foo, bar, baz
  }
})(module['./src/A.js'], module['./src/A.js'].exports, require)
```

## 简述require的模块加载机制

核心模块，包模块(node_modules), 相对路径，绝对路径
缓存 (`cache[module]`)

### 实现一个简单的requrie
```js
const { readFileSync } = require('fs')
const path = require('path')
const { Script } = require('vm')

function my_require(filename) {
  // 读取文件
  // 需要区分核心模块，包模块(node_modules), 相对路径，绝对路径，这里只考虑相对路径和绝对路径
  const fileContent = readFileSync(path.resolve(__dirname, filename))

  // 使用函数包裹原始代码
  const wrapper = `function(required, module, exports){
    ${fileContent}
  }`

  let module = {
    exports: {
      
    }
  }
    // 运行原始代码
  const script = new Script(wrapper)
  const func = script.runInThisContext()

  func(my_require, module, module.exports)

  return module.exports;
}

global.my_require = my_require
```

### 为什么浏览器端不能使用require
require的模块加载机制，是通过读取文件，以及script上下文执行实现的，浏览器端不能读

### 扩展：import/export的实现？

## 如何理解node的事件循环模型
![node的事件循环模型](/img/shijianxunhuan.png)

下面的代码的输出顺序？
```js
async function async1() {
  console.log('async1 started');
  await async2();
  console.log('async end');
}
async function async2() {
  console.log('async2');
}
console.log('script start');
setTimeout(function () {
  console.log('setTimeout0');
  setTimeout(()=>{
    console.log('setTimeout1')
  }, 0)
  setImmediate(()=>{
    console.log('setImmediate')
  })
}, 0);
async1();
process.nextTick(()=>{
  console.log('nextTick');
})
new Promise(function (resolve) {
  console.log('promise1');
  resolve();
  console.log('promise2')
}).then(function () {
  console.log('promise.then');
});
console.log('script end');
```

输出顺序：
```js
// script start
// async1 started
// async2
// promise1
// promise2
// script end
// nextTick
// async end
// promise.then
// setTimeout0
// setImmediate
// setTimeout1
```


## 如何描述异步I/O的流程

### 什么是阻塞和非阻塞

系统在接受输入的时候，再到输出的过程中，能不能接受别的输入
比如说请求文件上传，loading过程中用户还能执行别的操作，那这个上传过程就是非阻塞的。

### 如何实现非阻塞？

#### 方案一：多线程
线程上下文切换，线程同步（加锁）

#### 方案二：单线程 + 异步I/O
- I/O不能阻塞CPU的执行
- 不要带来锁的问题
- 性能要OK

## 简述V8的垃圾回收机制以及关键点

### 为什么会产生垃圾

### 垃圾回收，是如何实现的？

分代式垃圾回收机制

- Major 主垃圾回收器 / 新生代 -> scavenge算法 ：对等分成两份，一半是active区域，一般是空闲区域；当active区域快满了，把活着的放到空闲区域，然后清除整个active区域。这次操作之后active区域和空闲区域的作用就反转了，原先的active区域就变成了空闲区，原先的空闲区就变成了active区。（多次操作的变量会升级到老生代）
- Minio 副垃圾回收器 / 老生代 -> 标记清除 : 从根节点触发，所有的能够索引到的，都不是垃圾，其他的，就是垃圾

## 内存泄漏

常见导致内存泄漏的行为：
1.全局变量
2.函数闭包
3.事件监听

检测方法：
1.heapdunp
2.chrome devtools 快照

## 进程和线程
### 简述对于node的多进程架构的理解
- Mater - Worker的一个双向通信协议
- fork

cluster?

### 如何创建子进程，以及子进程crash之后如何重启

#### 创建子进程的方式？
spawn fork exec execFile

- spawn(最基础的)
```js
// 在父进程中
const cp = spawn('node', 'child.js', {
  cwd: path.resolve(process.cwd(), './worker')
  stdio: [process.stdio, process.stdout, process.stderr]
})

// 在子进程中
process.stdout.write('sum' + sum);
```

- fork

- exec

- execFile

#### 如何重启
- 一般在生成环境中，使用pm2进行进程守护，父进程监听子进程的退出事件，监听到之后重启
- 进行健康检查，定时启动一个进程，查询目标进程的健康状态

## 简述koa中间件原理
https://github.com/koajs/compose/blob/master/index.js

就是实现下面的compose函数，next的时候调用下一个函数，最后往回执行
```js
const middleware = []
middleware.push((ctx, next)=>{
  console.log(1)
  next()
  console.log(2)
})
middleware.push((ctx, next)=>{
  console.log(3)
  next()
  console.log(4)
})
middleware.push((ctx, next)=>{
  console.log(5)
  next()
  console.log(6)
})

const fn = compose(middleware)
fn() // 1 3 5 6 4 2
```

核心原理：
```js
function compose(middleware) {
  return function(context, next) {
    return dispatch(0)

    function dispatch(i) {
      if(i > middleware.length) return
      let fn = middleware[i]
      if(i === middleware.length) fn = next || (()=>{})
      
      const result = fn(context, dispatch.bind(null, i + 1))
      return result
    }
  }
}
```