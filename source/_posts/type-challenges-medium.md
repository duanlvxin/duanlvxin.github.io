---
title: type-challenges-medium
date: 2025-05-13 21:25:15
tags:
 - typescript
categories:
 - typescript
cover: /svg/typescript.svg
bg_size: auto
---

## Get Return Type
```ts
type MyReturnType<T> = T extends (...args: any[]) => (infer R) ? R : never
```

## Omit (使用as*）
```ts
type MyOmit<T, K extends keyof T> = {[P in keyof T as P extends K ? never: P] :T[P]}
```

## Reanonly (使用交集&)
```ts
type MyReadonly2<T, K extends keyof T =  keyof T> = {
  readonly [P in K]: T[P]
} & {
  [P in keyof T as P extends K ? never : P]: T[P]
}
```

```ts
type MyReadonly2<T, K extends keyof T =  keyof T> = {
  readonly [P in K]: T[P]
} & MyOmit<T, K>
```

## Deep Reanonly
```ts
type DeepReadonly<T> = T extends {} ? (T extends (...args: []) => any ? T : {
  readonly [key in keyof T]: DeepReadonly<T[key]>
}) : T
```

```ts
// 高赞答案
type DeepReadonly<T> = keyof T extends never
  ? T
  : { readonly [k in keyof T]: DeepReadonly<T[k]> };
```

## Tuple to Union
```ts
type TupleToUnion<T> = T extends (infer U)[] ? U : never
```

```ts
// 另一种解法
type TupleToUnion<T extends unknown[]> = T[number]
```

## Chainable Options
```ts
// 正解
type Chainable<R = object> = {
  option<K extends  string, V>(
    key: K extends keyof R ? never : K,
    value: V
  ): Chainable<Omit<R, K> & Record<K, V>>
  get(): R
}
```

```ts
// 部分正确，无法处理option传入多次相同key值提示错误的情况（仅记录思路）
interface Chainable {
  option<K extends keyof any, V>(key: K, value: V): this & {[key in K]: V}
  get(): {[key in keyof this as key extends 'option' | 'get' ? never : key]: this[key]}
}
```

## Last of Array
```ts
type Last<T extends any[]> = T extends [...any[], infer U] ? U : never
```

## Pop
```ts
type Pop<T extends any[]> = T extends [] ? [] : T extends [...infer U, any] ? U : never
```

## Promise.all
```ts
declare function PromiseAll<T extends any[]>(values: readonly [...T]):
  Promise<{ [K in keyof T]: Awaited<T[K]> }>;
```

```ts
// 部分正确解
declare function PromiseAll<T extends any[]>(values: readonly [...T]):
  Promise<{ [K in keyof T]: T[K] extends Promise<infer R> ? R : T[K] }>;
```

## Type Lookup（条件类型* - |关系）
```ts
type LookUp<U, T> = U extends {type: T} ? U : never;
```

## Trim Left
```ts
type space = ' ' | '\n' | '\t'
type TrimLeft<S extends string> = S extends `${space}${infer R}` ? TrimLeft<R> : S
```

## Trim
```ts
type Trim<S extends string> = S extends `${space}${infer R}` ? Trim<R> : S extends `${infer U}${space}` ? Trim<U> : S
```

同理，trim right
```ts
type space = ' ' | '\n' | '\t'
type TrimRight<S extends string> = S extends `${infer R}${space}` ? TrimRight<R> : S
```

## Capitalize(内置的Uppercase)
```ts
type MyCapitalize<S extends string> = S extends `${infer R}${infer Rest}` ? `${Uppercase<R>}${Rest}` : S
```

## Replace
```ts
type Replace<S extends string, From extends string, To extends string> =
  From extends '' ? S :
    S extends `${infer P}${From}${infer R}` ? `${P}${To}${R}` : S
```

## Replace all
```ts
type ReplaceAll<S extends string, From extends string, To extends string> = 
  S extends '' ? '' : 
    From extends '' ? S :
      S extends `${infer R}${From}${infer U}` ? U extends `${infer M}${From}${infer N}` ?  `${R}${To}${ReplaceAll<U, From, To>}`: `${R}${To}${U}` : S
```