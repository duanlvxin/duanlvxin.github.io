---
title: typescript类和接口
date: 2025-05-10 16:26:25
tags:
 - typescript
categories:
 - typescript
cover: /svg/typescript.svg
bg_size: auto
---

## 类和继承

### 访问修饰符
- 示例
```ts
class Piece {
  protected position: Position
  constructor(
    private readonly color: Color,
    file: File,
    rank: Rank
  ) {
    this.position = new Position(x, y)
  }
}
```

- ts类中的属性和方法支持三个访问修饰符：
```
public 任何地方可以访问
private 只能当前类的实例访问
protected 当前类和字类的实例可访问
```

- 使用`private readonly color: Color`
自动把参数值赋给this => this.color = color
(position也是，不过如果不显示的写，position类型是Position | undefined)

### abstract
使用abstract声明的类只能继承，不能实例化

### 总结
- 类使用class关键字声明，扩展类用extends关键字
- 类可以是具体的，也可以是抽象的（abstract），抽象类可以有抽象属性和抽象方法
  （抽象属性和抽象方法表示子类一定要实现，非抽象方法在抽象类中就要实现，否则会报错）
- 方法的可见性可以是private,protected,public(默认)。
  方法分为实例方法和静态方法(static)
- 类可以有实例属性，可见性可以是private,protected,public(默认)。
  实例属性可以在构造函数的参数中声明，也可通过属性初始化语句声明
- 声明实例属性时可以使用readonly把属性标记为只读

## super

```ts
// 1.显式调用, 使用super只能访问父类的方法，不能访问父类的属性
super.take()

// 2.在子类的constructor中调用：
super()
```

## 以this作为返回类型

## 接口

interface

### 定义
- 与类型别名相似，接口是一种命名类型的方式。

  ```ts
  type Sushi = {
    calories: number,
    salty: boolean,
    tasty: boolean
  }

  interface Sushi {
    calories: number,
    salty: boolean,
    tasty: boolean
  }
  ```

- 把类型组合在一起：
```ts
type Food = {
  calories: number,
  tasty: boolean
}

type Sushi = Food & {
  salty: boolean
}

type Cake = Food & {
  sweet: boolean
}
```

  ```ts
  interface Food {
    calories: number,
    tasty: boolean
  }

  interface Sushi extends Food {
    salty: boolean
  }

  interface Cake extends Food {
    sweet: boolean
  }
  ```

- 接口和类型别名的区别？
```
1.类型别名更为通用，右边可以是任何类型；而在接口中，右边必须是结构
2.扩展接口时，ts会检查扩展的接口是否可赋值给被扩展的接口；类型会重载，不会报错
3.同一作用域的多个同名接口将自动合并，同一作用域中的多个同名类型别名将报错
```

### 声明合并

```ts
interface User {
  name: string
}

interface User {
  age: string
}

let a: User = {
  name: 'zhangsan',
  age: 18
}
```

注意：
- 两个接口不能有冲突，如果一个接口中某个属性的类型是T，另一个接口中该属性的类型是U，会报错
- 如果接口中声明了泛型，那么两个接口要使用完全相同的方式声明泛型（名称不一样也是不行的）

### 实现

- 接口可以声明实例属性，但是不能带有可见性修饰符，也不能用static关键字。readonly可以用

```ts
interface Animal {
  readonly name: string
  eat(food: string): void
  sleep(hours: number): void
}

class Cat implements Animal {
  name = 'doubao'
  eat(food: string) {
    console.log('Ate some ', food, '. Mmm！')
  }
  sleep(hours: number) {
    console.log('Slep for ', hours, ' hours')
  }
}
```

### 实现接口还是扩展抽象类

- 接口更通用、更轻量；抽象类更具体，功能更丰富
- 如果多个类共用同一个实现，使用抽象类；如果需要轻量表示“这个类型是T型”，使用接口

## 类是结构化类型

- 如果常规的对象定义了同样的属性或方法，也与类兼容
- 检查一个结构是否可赋值给一个类时，如果类中有private或protected字段，而且结构不是类或其子类的实例，
  那么结构就不可赋值给类

```ts
class Zebra {
  trot() {

  }
}
class Poole {
  trot() {

  }
}
function ambleAround(animal: Zebra) {
  animal.trot()
}
ambleAround(new Zebra())
ambleAround(new Poole())
```

```ts
class A { private x = 1}
class B extends A {}
function f(a: A) {}

f(new A)
f(new B)
f({ x: 1 }) // error 
```

## 类既声明值也声明类型

`typeof Person`

```ts
class Person {
  constructor(public name: string, public age: number) {}
  greet() {
    console.log(`Hello, my name is ${this.name} and I am ${this.age} years old.`);
  }
}

const PersonConstructor: typeof Person = Person;
const anotherPerson = new PersonConstructor("Bob", 25);
anotherPerson.greet(); // 输出: Hello, my name is Bob and I am 25 years old.
```

## 多态

```ts
class MyMap<K,V> {
  // 1.constructor上不能声明泛型，应该在类声明中声明泛型
  constructor(initKey: K, initVal: V) {

  }
  get(k: K): V {

  }
  set(k: K, value: V): void {

  }
  // 2.实例方法可以访问类声明中的泛型，并且可以自定义泛型
  merge<K1, V1>(map: MyMap<K1, V1>): MyMap<K | K1, V | V1> {
    
  }
  // 3.静态方法不能方位类的泛型，这里的K,V是静态方法自己声明的
  static of<K, V>(k: K, v: V): MyMap<K, V> {

  }
}
```

## 混入

混入其实就是一个函数，只不过这个函数接受一个类构造方法，返回一个类构造方法。

## 装饰器

装饰器可以用来装饰类、类方法、属性和方法参数。实际上就算是在装饰目标上调用函数。


## 模拟final类

final：把类标记为不可扩展，把方法标记为不可覆盖

在ts中可以用private来模拟实现final类
(构造函数前用private,不能继承也不能new; 如果构造函数前是protected,那么可以继承，不能new)
```ts
class Person {
  private constructor(private name: string) {

  }
  static create(name: string) {
    return new Person(name)
  }
}
```

## 设计模式

### 工厂模式

鞋子工厂:
```ts
type Shoe = {
  purpose: string
}

class BalletFlat implements Shoe {
  purpose = 'dancing'
}

class Boot implements Shoe {
  purpose = 'woodcutting'
}

class Sneaker implements Shoe {
  purpose = 'walking'
}
```

```ts
let shoeFactory = {
  create(type: 'ballet' | 'boot' | 'sneaker'): Shoe {
    switch(type) {
      case 'ballet':
        return new BalletFlat()
      case 'boot':
        return new Boot()
      case 'sneaker':
        return new Sneaker()
    }
  }
}
```

=> 优化

```ts
type ShoeFactory = {
  create(type: 'balletFlat'): BalletFlat
  create(type: 'boot'): Boot
  create(type: 'sneaker'): Sneaker
}
let shoeFactory: ShoeFactory = {
  create(type: 'balletFlat' | 'boot' | 'sneaker'): Shoe {
    switch(type) {
      case 'balletFlat':
        return new BalletFlat()
      case 'boot':
        return new Boot()
      case 'sneaker':
        return new Sneaker()
    }
  }
}
```

### 建造者模式

```ts
new RequestBuilder()
  .setURL('/users')
  .setMethod('get')
  .setData({ 'firstname': 'Anna' })
  .send()
```

```ts
class RequestBuilder {
  private data: object | null = null
  private method: 'get' | 'post' | null = null
  private url: string | null = null

  setURL(url: string): this {
    this.url = url
    return this
  }

  setMethod(method: 'get' | 'post'): this {
    this.method = method
    return this
  }

  setData(data: object): this {
    this.data = data
    return this
  }
}
```