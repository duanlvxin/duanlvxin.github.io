---
title: typescript函数
date: 2025-05-10 13:58:25
tags:
 - typescript
categories:
 - typescript
cover: /svg/typescript.svg
bg_size: auto
---

## 声明和调用函数
1. ts函数中通常显示注解函数参数，返回参数能推导出来，可以不写
2. ts支持五种声明函数的方法：
```ts
// 1. 具名函数
function greet1(name: string) {
  return 'hello ' + name
}

// 2. 函数表达式
let greet2 = function(name: string) {
  return 'hello ' + name
}

// 3. 箭头函数表达式
let greet3 = (name: string) => {
  return 'hello ' + name
}

// 4. 箭头函数表达式简写形式
let greet4 = (name: string) => 'hello ' + name

// 5. 构造函数方法（危险，一般不用）
let greet5 = new Function('name', 'return "hello " + name')
```

### 可选和默认参数

- 可选参数用?, 默认参数用=
- 可选参数必须放在末尾，默认参数不用

```ts
function log(message:string, userId?: string = 'Not signed in') {
  // xxx
}

// 可以简写为下面的形式, ts会根据默认参数进行推导userId的类型
function log(message:string, userId = 'Not signed in') {
  // xxx
}
```

### 剩余参数

- arguments不安全（里面每一项都是any类型）
- 一个函数最多只能由一个剩余参数，且必须在参数列表的最后面

```ts
function sumVariadic(...numbers: number[]) {
  return numbers.reduce((a, b) => a + b, 0)
}
```

```ts
interface Console {
  log(message?:any, ...optionalParams: any[]): void
}
```

### call，apply，bind

```ts
add(10, 20)
add.apply(null, [10, 20])
add.call(null, 10, 20)
add.bind(null, 10, 20)()
```

### 注解this的类型
- this是.左侧的对象
- 如果函数使用this，在函数的第一个参数中声明this的类型。this不是常规的参数，而是保留字，
是函数签名的一部分。生成为js后，第一个this参数是没有的。

```ts
function fancyDate(this: Date) {
  return `${this.getDate()}/${this.getMonth()}/${this.getFullYear()}`
}
```

### 生成器和迭代器

#### 概念

```js
function *generator(): IterableIterator<number> {
  let a = 0
  let b = 1
  while(true) {
    yield a
    const total = a + b
    a = b
    b = total
  }
}
```

- 可迭代对象是有Symbol.iterator属性的对象，而且该属性的值是一个函数，返回一个迭代器
- 迭代器是定义有next方法的对象，该方法返回一个具有value和done属性的对象
- generator是生成器，调用这个函数得到的值是一个可迭代对象, 也是迭代器，成为可迭代的迭代器。
因为该值既有Symbol.iterator, 也有next方法


可迭代对象：`IterableIterator<number>`
```js
let numbers = {
  *[Symbol.iterator]() {
    for(let i = 0; i <= 10; i++) {
      yield i
    }
  }
}
```
#### 使用迭代器

```js
// 使用for-of迭代一个迭代器
for (let a of numbers) {

}

// 展开一个迭代器
let allNumbers = [...numbers]

// 解构一个迭代器
let [one, two, ...rest] = numbers
```

### 调用签名

```js
function sum(a: number, b: number): number {
  return a + b
}

// 调用签名如下：
type Sum = (a: number, b: number) => number
```

- 函数的调用签名只包含类型层面的代码，只有类型没有值
- 调用签名没法表示默认值（因为默认值是值，不是类型）
- 调用签名没有函数的定义体，无法推导出返回类型，所以必须显示注解


使用函数调用签名重写log函数：
```ts
function log(message:string, userId?: string = 'Not signed in') {
  // xxx
}
```
=>
```ts
type Log = (message: string, userId?: string) => void
let log: Log = function(message, userId = 'Not signed in') {
  // xxx
}
```

### 上下文类型推导

1.示例一：
看下面的例子，message和userId不用显示注解类型，因为ts会进行上下文推导
```ts
type Log = (message: string, userId?: string) => void
let log: Log = function(message, userId = 'Not signed in') {
  // xxx
}
```

2.示例二：

```ts
function times(f: (index: number) => void, n: number) {
  for(let i=0;i<n;i++) {
    f(i)
  }
}

// 调用times
// 1. 在行内声明，无需显式注解
times(n => console.log(n), 3)
// 2. 非行内声明，需要显式注解，因为ts无法进行推导
function f(n: number) {
  console.log(n)
}
times(f, 3)
```

### 函数类型重载

#### 两种写法
```ts
// 简写
type Log = (message: string, userId?: string) => void

// 完整
type Log = {
  (message: string, userId?: string): void
}
```

函数重载只能用完整写法，因为type不能重复定义，示例：
```ts
type Reserve = {
  (from: Date, to: Date, desination: string): void
  (from: Date, desination: string): void
}
```

```ts
// 这样写会报错
let reserve: Reserve = (from, to, destination) => {
  // xxx
}

// 需要声明组合后的调用签名，因为ts无法自动推导
// 注意：类型和数量都需要匹配！！！
let reserve: Reserve = (from: Date, to: Date | string, destination?: string | null): void => {
  
}
```

通常函数库里会这样写:
```ts
declare module add {
  export function addStr(a: string, b: string): string
  export function addNum(a: number, b: number): number
}
add.addNum(1, 2)
add.addStr('a', 'b')
```

#### 重载的签名需要具体一些

```ts
type CreateElement = {
  (tag: 'a'): HTMLAnchorElement
  (tag: 'canvas'): HTMLCanvasElement
  (tag: 'table'): HTMLTableElement
  (tag: string): HTMLElement
}
```

## 多态

```ts
type Add = {
  (a: string, b: string): string
  (a: number, b: number): number
}
```

=> 使用泛型

```ts
type Add = {
  <T>(a: T, b: T): T
}
```

### 什么时候绑定泛型

1. 写法一
T在`调用签名`中声明，ts将在调用Filter类型的函数时为T绑定具体类型

```ts
type Filter = {
  <T>(array: T[], f: (item: T) => boolean): T[]
}

let filter: Filter = (array, f) => {
  return array.filter(item => f(item))
}
```

2. 写法二
把T的作用域限定在`类型别名`Filter中，ts则要求在使用Filter时显示绑定类型

```ts
type Filter<T> = {
  (array: T[], f: (item: T) => boolean): T[]
}

type NumberFilter = Filter<number> 
type StringFilter = Filter<string> 
let numberFilter: NumberFilter = function(array, f) {
  return array.filter(item => f(item))
}
let stringFilter: NumberFilter = function(array, f) {
  return array.filter(item => f(item))
}
```

### 可以在哪声明泛型

只要是ts支持的声明调用签名的方法，都有办法在签名中加入泛型

### 泛型推导

```ts
function mymap<T, U>(array: T[], f: (item: T) => U): U[] {
  let result = []
  for(let i = 0; i < array.length; i++) {
    result[i] = f(array[i])
  }
  return result
}

// 鼠标悬浮到mymap上，能看到T和U分别对应的string和boolean
const map = mymap(['1', '2', '3'], (item) => Boolean(item))
```

- 也可以显式注解泛型，不过显式注解泛型时，所有的泛型都必须注解
- ts会检查推导出来的每个泛型是否可赋值给显式绑定的泛型，如果不可赋值，将报错

```ts
mymap<string, boolean>(['1', '2', '3'], (item) => Boolean(item))
// 会报错
mymap<string, number>(['1', '2', '3'], (item) => Boolean(item))
// 正确
mymap<string, number | boolean>(['1', '2', '3'], (item) => Boolean(item))
```

### 泛型别名

- 在类型别名中只有这一个地方可以声明泛型：紧随类型别名的名称之后，赋值运算符(=)之前
```ts
type MyEvent<T> = {
  target: T,
  type: string
}
```

- 使用MyEvent这样的泛型时，必须显式绑定类型参数
- 泛型别名也可以在函数的签名中使用，Ts为T绑定类型时，还会自动为MyEvent绑定

```ts
function triggerEvent<T>(event: MyEvent<T>): void {

}

triggerEvent({
  target: document.querySelector('#mybutton'),
  type: 'mouseover'
})

// 推导出来T是Element | null
```

### 受限的多态

使用extends


```ts
type TreeNode = {
  value: string
}
function mapNode<T extends TreeNode>(node: T, f: (value: string) => string) {
  return {
    ...node,
    value: f(node.value)
  }
}
```

### 泛型默认类型

```ts
type MyEvent<Type extends string, Target extends HTMLElement = HTMLElement> = {
  type: Type,
  target: Target
}
```