---
title: type-challenges-easy
date: 2025-05-12 22:07:55
tags:
 - typescript
categories:
 - typescript
cover: /svg/typescript.svg
bg_size: auto
---

## 题库代码仓库地址
[type-challenges](https://github.com/type-challenges/type-challenges)

## Pick
```ts
type MyPick<T, U extends keyof T> = {
  [key in U]: T[U]
}
```

## Reanonly
```ts
type MyReadonly<T> = {
  readonly [key in keyof T]: T[key]
}
```

## Tuple to Object
```ts
type TupleToObject<T extends readonly any[]> = {
  [key in T[number]]: key
}
```

## First of Array
```ts
type First<T extends any[]> = T extends [] ? never : T[0]
// 注意这里只能用中括号，不能用点
type First<T extends any[]> = T['length'] extends 0 ? never : T[0]
type First<T extends any[]> = T extends [infer A, ...infer rest] ? A : never
```

## length of Tuple
```ts
type Length<T extends readonly any[]> = T['length']
```

## Exclude
```ts
type MyExclude<T, U> = T extends U ? never : T
```

## Awaited
```ts
type MyAwaited<T> = T extends PromiseLike<infer U> ?  (U extends PromiseLike<any> ? MyAwaited<U> : U ) : never
```

```ts
// 高赞答案
type MyAwaited<T extends PromiseLike<any>> = T extends PromiseLike<infer U>
  ? U extends PromiseLike<any>
    ? MyAwaited<U>
    : U
  : never;
```

## If
```ts
type If<C extends boolean, T, F> = C extends true ? T : F
```

```ts
type Case1 = IF<boolean, "a", 2> // 类型是 "a" | 2, 为什么？
```

## Concat
```ts
type Concat<T extends any[] | readonly any[], U extends any[] | readonly any[]> = [...T, ...U]
```

```ts
// 高赞答案：因为readonly unknown[]是unkonw[]的子类型
type Tuple = readonly unknown[];
type Concat<T extends Tuple, U extends Tuple> = [...T, ...U];
```

## Includes
```ts
type Includes<T extends readonly any[], U> = 
T extends [infer A,...infer B] ? (Equal<U,A> extends true ? true : Includes<B, U>) : false
```

```ts
// 高赞回答
type IsEqual<T, U> =
	(<G>() => G extends T ? 1 : 2) extends
	(<G>() => G extends U ? 1 : 2)
		? true
		: false;

type Includes<Value extends any[], Item> =
	IsEqual<Value[0], Item> extends true
		? true
		: Value extends [Value[0], ...infer rest]
			? Includes<rest, Item>
			: false;
```

## Push
```ts
type Push<T extends any[], U> = [...T, U]
```

## Unshift
```ts
type Unshift<T extends any[], U> = [U, ...T]
```

## Parameters(*)
```ts
type MyParameters<T extends (...args: any[]) => any> = T extends (...any: infer S) => any ? S : any 
```

## 总结
- keyof
- key in U
- T[number]
- infer => 未知量，按顺序
- T[length]
- [...T, ...U]
