---
title: typescript类型进阶
date: 2024-04-10 22:24:25
tags:
 - typescript
categories:
 - typescript
cover: /svg/typescript.svg
bg_size: auto
---

## 类型之间的关系

### 子类型和超类型
> 如果B是A的子类型，那么在需要A的地方都可以使用B
> 如果B是A的超类型，那么在需要B的地方都可以使用A

1、所有类型都是any的子类型, 所有类型也都是unknown的子类型
2、nerver是所有类型的子类型

### 型变 
规定：
`A<:B`,指A类型是B类型的子类型，或是同种类型
`A>:B`,指A类型是B类型的超类型，或是同种类型

型变有4种：
1. 不变：只能是T
2. 协变：可以是`<:T`
3. 逆变： 可以是`>:T`
4. 双变： 可以是`<:T` 或者 `>:T`

#### 结构和数组型变
对于对象，如果想保证A对象可赋值给B对象，那么A的每个属性都`<:`B对象的对应属性。(也就是协变)

#### 函数型变
如果函数A的参数数量小于函数B的参数数量，而且满足以下条件，那么函数A是函数B的子类型
1、函数A的this类型未指定，或者函数A的this类型`>:`函数B的this类型（逆变）
2、函数A的各个参数类型`>:`函数B的各个参数类型（逆变）《= 见下面例子
3、函数A的返回类型`<:`函数B的返回类型（协变）《= 见下面例子

```js
class Animal {}
class Bird extends Animal {
  shirp()
}
class Crow extends Bird {
  caw()
}
```

```js
function shrip(bird: Bird): Bird {
  bird.shirp();
  return bird;
}
```

下面再定义一个函数，函数参数是一个函数
```js
function clone(f:(bird: Bird)=>Bird): viod {}
```

1、型变返回值（<:）
```js
function birdToCrow(d: Bird): Crow {}
clone(birdToCrow); // OK

function birdToAnimal(d: Bird): Animal {}
clone(birdToAnimal); // error
```

```js
function clone(f:(bird: Bird)=>Bird): viod {
  let parent = new Bird();
  let babyBird = f(parent);
  babyBird.shirp(); // 如果f返回的是Animal,这里就会报错
}
```

2、 型变参数（>:）
```js
function animalToBird(a: Animal): Bird {}
clone(animalToBird); // ok

function crowToBird(c: Crow): Bird {}
clone(crowToBird); // error
```

// crowToBird可能是这样的实现，传给clone的话参数是Bird类型，使用Bird类型进行.caw就会报错
```js
function crowToBird(c: Crow): Bird {
  c.caw();
  return new Bird();
}
```

3、型变this（>:）
```js
function parentThis(b: Bird): Bird {}

function childThis(b: Bird): Bird {
  this.test();
}
```

### 可赋值性

A类型可赋值给B类型：
```
1. A <: B
2. A是any
```

对于枚举
```
1. A是枚举B的成员
2. B至少有一个成员是number类型，且A是数字
```

### 类型拓宽

#### let,var,null,undefined
使用let,var会类型拓宽，从字面量放大到包含该字面量的基类型。
const不会。
如果不想让typescript进行类型拓宽，需要显示注解类型

```ts
let a : 'x' = 'x'; // x

const b = 'x'; // x
let c = b; // string

const d:'x' = 'x'; // x
let e = d; // x
```

null和undefined会被拓宽为any,但是从作用域出来之后就不拓宽了
```js
function x() {
  let a = null; // any
  a = 3; // any
  a = 'b'; //any
  return a; // any
}
x() // string
```

#### const类型断言

as const能禁止类型拓宽
```ts
let c = { x: 3 } as const; // {readonly x:3}
```

as const不仅能阻止拓宽类型，还能递归把成员设为readonly
```ts
let d = [1, {x: 2}] // (number | {x: number})[]
let e = [1, {x: 2}] as const // readonly [1, {readonly x : 2}]
```

#### 多余属性检查
ts做的多余属性检查，无需关注

#### 细化

```ts
type UserTextEvent = { value: string, target: HTMLInputElement };
type UserMouseEvent = { value: [number, number], target: HTMLElement };
type UserEvent = UserTextEvent | UserMouseEvent;

function handler(event: UserEvent) {
  if (typeof event.value === 'string') {
    event.value; // string
    event.target; // HTMLInputElement | HTMLElement => ts不深入推导
    return;
  }

  event.value; // [number, number]
  event.target; // HTMLInputElement | HTMLElement => ts不深入推导
  return;
}
```

```ts
type UserTextEvent = { type: 'TextEvent', value: string, target: HTMLInputElement }
type UserMouseEvent = { type: 'MouseEvent', value: [number, number], target: HTMLElement }
type UserEvent = UserTextEvent | UserMouseEvent
function handler(event: UserEvent) {
  if (event.type === 'TextEvent') {
    event.value // string
    event.target // HTMLInputElement
    return
  }

  event.value // [number, number]
  event.target // HTMLElement
  return
}
```

## 对象类型进阶
### 对象类型的类型运算符
#### "键入"运算符

```ts
type ApiResponse = {
  user: {
    userId: string,
    friendList: {
      count: number,
      friends: {
        firstName: string,
        lastName: string
      }[]
    }
  }
}

// 使用方括号表示法键入，不能用点号表示法
type FriendList = ApiResponse['user']['friendList']
// 因为是数组，所以用number; 如果是元组，用具体的0，1，...
type Firend = FriendList['friends'][number]
```

#### keyof运算符

> keyof运算符获取对象所有键值的类型，合并为一个字符串字面量类型

```ts
type ApiResponseKeys = keyof ApiResponse // 'user'
type UserKeys = keyof ApiResponse['user'] // 'userId' | 'friendList'
```

#### "键入"运算符和keyof运算符结合

```ts
function get<O extends object, K extends keyof O>(o: O, k: K): O[K] {
  return o[k]
}
```

### Record类型
> Record类型类型用于描述有映射关系的对象(比常规的索引签名更强大)

```ts
type Weekday = 'Mon' | 'Tue' | 'Wed' | 'Thu' | 'Fri'
type Day = Weekday | 'Sat' | 'Sun'
let nextDay: Record<Weekday, Day> = {
  Mon: 'Tue',
  Tue: 'Wed',
  Wed: 'Thu',
  Thu: 'Fri',
  Fri: 'Sat'
}
```

#### 映射类型
```ts
let nextDay: {[K in Weekday]: Day} = {
  Mon: 'Tue',
  Tue: 'Wed',
  Wed: 'Thu',
  Thu: 'Fri',
  Fri: 'Sat'
}
```

ts内置的Record类型是使用映射类型实现的
```ts
type Record<K extends keyof any, T> = {
  [P in K]: T;
};
```

示例：
```ts
type Account = {
  id: number,
  isEmployee: boolean,
  notes: string[]
}

// 所有字段可选 ————  ？
type OptionalAccount = {
  [K in keyof Account]?: Account[K]
}

// 所有字段可为null ————  +(基本不用)
type NullableAccount = {
  [K in keyof Account]: Account[K] | null
}

// 所有字段只读 ————  readonly
type ReanonlyAccount = {
  reanonly [K in keyof Account]: Account[K]
}

// 所有字段可写 ———— -
type Account2 = {
  -reanonly [K in keyof ReanonlyAccount]: Account[K]
}

// 所有字段必须 ———— -
type Account3 = {
  [K in keyof OptionalAccount]-?: Account[K]
}
```

内置的映射类型
```ts
Record<Keys, Values>
Partial<Object>
Required<Object>
Reanonly<Object>

// Pick
Pick<Object, Keys>
type Pick<T, K extends keyof T> = {
  [P in K]: T[P];
};
```

### 伴生对象模式
> 把类型和对象配对在一起，称为伴生对象模式

## 函数类型进阶

### 改善元组的类型推导

```ts
function tuper<T extends unknown[]>(...args: T): T {
  return args
}
let a = tuper(1, true)
```

### 用户定义的类型防护措施
```ts
function isString(a: unknown): boolean {
  return typeof a === 'string'
}

// 如果需要明确a传入string时，返回的是true
function isString(a: unknown): a is string {
  return typeof a === 'string'
}
```

## 条件类型

```ts
type IsString<T> = T extends string ? true : false
```

### 条件分配

> 分配率

```ts
// 取出T中那些不在U中的类型
type without<T,U> = T extends U ? never : T

type A = without<boolean | number | string, boolean>
       = without<boolean, boolean>
        | without<number, boolean>
        | without<string, boolean>
       = never | number | string
       = number | string
```


### infer关键字
```ts
type ElementType<T> = T extends unknown[] ? T[number] : T
type A = ElementType<number[]> // number

// 使用infer改写
type ElementType2<T> = T extends (infer U)[] ? U : T
```

### 内置的条件类型

```ts
// 从T的类型中排除在U的类型
Exclude<T, U>
type Exclude<T, U> = T extends U ? never : T;

// 计算T中可赋值给U的类型
Extract<T, U>
type Extract<T, U> = T extends U ? T : never;

// 从T中排除null和undefined
NonNullable<T>
type NonNullable<T> = T extends null | undefined ? never : T;

// 计算函数的返回类型，不适用泛型和重载的函数
ReturnType<F>
type ReturnType<T extends (...args: any) => any> = T extends (...args: any) => infer R ? R : any;

// 计算类构造方法的实例类型
InstanceType<C>
type InstanceType<T extends abstract new (...args: any) => any> = T extends abstract new (...args: any) => infer R ? R : any;
```

## 断言(`少用`)

### 类型断言(as)

> 只能断言一个类型是自身的超类型或子类型，不能断定number是string

```ts
function formatInput(input: string) {

}
function getInput(): string | number {
  return 'a'
}
let input = getInput()
formatInput(input as string)
```

### 非空断言(!) 

> 用于断言类型不是null或undefined

```ts
element.parentNode!.removeChild(element)
```

### 明确赋值断言(!)

> 用于断言变量已经明确赋值

```ts
// 改写为let userId!: string 进行明确赋值断言
let userId: string
type GlobalCache = {
  get(key: string): any;
  [key: string]: any; // 索引签名，允许通过字符串键访问属性
};
let globalCache: GlobalCache = {
  get(key: string) {
    return this[key];
  }
};
fetchUser()
// 这里会报错
userId.toUpperCase()
function fetchUser() {
  userId = globalCache.get('userId')
}
```

## 模拟名义类型（略）

```ts
type CompanyId = string & { readonly brand: unique symbol}
type UserId = string & { readonly brand: unique symbol}
type id = CompanyId | UserId

function queryForUser(id: UserId) {

}

function CompanyId(id: string) {
  return id as CompanyId
}
function UserId(id: string) {
  return id as UserId
}

let companyId = CompanyId('8AKSDLEP')
let userId = UserId('dwerwerwer')

queryForUser(companyId)
queryForUser(userId)
```

## 安全地扩展原型
使用interface
```ts
define global {
  interface Array<T> {
    zip<U>(list: U[]): [T, U][]
  }
}
```