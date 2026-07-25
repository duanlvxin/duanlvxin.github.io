---
title: promise和generator
date: 2025-12-21 15:29:41
categories:
  - js
tags:
  - js
cover: /img/cover/frontend.png
top_img: /img/default-top.jpg
---

## 要点
- promise的状态，参数
  1.pending，fulfilled, rejected
  2.传入executor函数，该函数立即执行，参数为resolve，reject函数。
- promise的状态的默认值，状态流转
  1.默认状态为pending，成功-value，失败-reason
  2.只能从pending流转到fulfilled或rejected
- promise的返回值
  1.then，接受onFulfilled，onRejected函数
  2.then的时候，
    1).如果promise状态为fulfilled，调用onFulfilled函数，参数为promise的value
    2).如果promise状态为rejected，调用onRejected函数，参数为promise的reason
    3).如果promise状态为pending，将onFulfilled，onRejected函数加入到promise的回调队列中

## 实现

### 简易版
```js
const PENDDING = 'pending';
const FULFILLED = 'fulfilled';
const REJECTED = 'rejected';

class Promise {
  constructor(executor) {
    this.state = PENDDING;
    this.value = undefined;
    this.reason = undefined;
    this.onFulfilledCallbacks = [];
    this.onRejectedCallbacks = [];

    const resolve = (value) => {
      if(this.state === PENDING) {
        queueMicrotask(() => {
          if(this.state === PENDING) {
            this.state = FULFILLED;
            this.value = value;
            this.onFulfilledCallbacks.forEach(fn => fn(this.value));
          }
        })
      }
    }

    const reject = (reason) => {
      if(this.state === PENDING) {
        queueMicrotask(() => {
          if(this.state === PENDING) {
            this.state = REJECTED;
            this.reason = reason;
            this.onRejectedCallbacks.forEach(fn => fn(this.reason));
          }
        })
      }
    }

    try {
      executor(resolve, reject);
    } catch (error) {
      reject(error);
    }
  }

  then(onFulfilled, onRejected) {
    onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : value => value;
    onRejected = typeof onRejected === 'function' ? onRejected : reason => { throw reason };

    if(this.state === FULFILLED) {
      onFulfilled(this.value);
    }
    if(this.state === REJECTED) {
      onRejected(this.reason);
    }
    if(this.state === PENDING) {
      this.onFulfilledCallbacks.push(onFulfilled);
      this.onRejectedCallbacks.push(onRejected);
    }
  }
}
```

### 完整版 - 主要是then函数
```js

function execFunctionWithCatchError(execFunc, value, resolve, reject) {
  try {
    const result = execFunc(value);
    resolve(result);
  } catch (error) {
    reject(error);
  }
}

class Promise {
  ...

  then(onFulfilled, onRejected) {
    onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : value => value;
    onRejected = typeof onRejected === 'function' ? onRejected : reason => { throw reason };

    return new Promise((resolve, reject) => {
      if(this.state === FULFILLED) {
        execFunctionWithCatchError(onFulfilled, this.value, resolve, reject);
      }
      if(this.state === REJECTED) {
        execFunctionWithCatchError(onRejected, this.reason, resolve, reject);
      }
      if(this.state === PENDING) {
        if(onFulfilled) {
          this.onFulfilledCallbacks.push(() => execFunctionWithCatchError(onFulfilled, this.value, resolve, reject));
        }
        if(onRejected) {
          this.onRejectedCallbacks.push(() => execFunctionWithCatchError(onRejected, this.reason, resolve, reject));
        }
      }
    })
  }

  catch(onRejected) {
    return this.then(undefined, onRejected);
  }

  finally(onFinally) {
    return this.then(onFinally, onFinally);
  }

  static resolve(value) {
    return new Promise((resolve, reject) => {
      resolve(value);
    })
  }

  static reject(reason) {
    return new Promise((resolve, reject) => {
      reject(reason);
    })
  }

  // 所有的都fulfilled，才fulfilled 《- resolve
  // 任意一个rejected，就rejected 《- reject
  static all(promises) {
    return new Promise((resolve, reject) => {
      const result = []
      promises.forEach(promise => {
        promise.then((value) => {
          result.push(value)
          if(result.length === promises.length) {
            resolve(result)
          }
        }, err => {
          reject(err)
        })
      })
    })
  }

  // 所有的状态都确定了，就算成功
  static allSettled(promises) {
    return new Promise((resolve, reject) => {
      const results = []
      promises.forEach(promise => {
        promise.then(value => {
          results.push({
            status: FULFILLED,
            value
          })
          if(results.length === promise.length) {
            resolve(results)
          }
        }, err => {
          results.push({
            status: REJECTED,
            value: err
          }),
          if(results.length === promise.length) {
            resolve(results)
          }
        })
      })
    })
  }

  // 任意一个fulfilled，就fulfilled
  // 任意一个rejected，就rejected
  static race(promises) {
    return new Promise((resolve, reject) => {
      promises.forEach(promise => {
        promise.then(value => {
          resolve(value)
        }, err => {
          reject(err)
        })
      })
    })
  }

  // 只要有一个成功，就算成功
  static any(promises) {
    return new Promise((resolve, reject) => {
      const reasons = []
      promises.forEach(promise => {
        promise.then(value => {
          resolve(value)
        }, err => {
          reasons.push(err)
          if(reasons.length === promises.length) {
            reject(reasons)
          }
        })
      })
    })
  }
}
```

## 取消任务
promise是一经执行无法取消，如何进行中断？

## generator