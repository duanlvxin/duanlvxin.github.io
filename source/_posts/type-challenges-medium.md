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

## Reanonly2 (使用交集&)
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

## Append Argument
```ts
type AppendArgument<Fn extends (...args: any[]) => any, A> =
  Fn extends (...args: infer R) => infer T ? (...args: [...R, A]) => T : never
```

## Permutation(条件分配 & 条件类型)
```ts
type Permutation<T, K = T> =
  [T] extends [never]
    ? []
    : K extends K
      ? [K, ...Permutation<Exclude<T, K>>]
      : never
```

(关于`[T] extends [never]`的分析：https://github.com/type-challenges/type-challenges/issues/614)
```ts
// 这里的结果是never, 因为IsNever<never> 中的 never 实际上是一个空的联合类型，一项都没有，所以 T extends ... 过程实际上被整体跳过了，所以最后的结果就是 never。
// ts 中对于 A | B extends C 的计算会转为A extends C | B extends C, 且会跳过never
type IsNever<T> = T extends never ? true : false
type Result = IsNever<never>
```

## Length of string
```ts
type StringToArray<S extends string> = S extends `${infer first}${infer Rest}` ? [first, ...StringToArray<Rest>] : [] 
type LengthOfString<S extends string> = StringToArray<S>["length"]
```

## Flatten
```ts
type Flatten<T extends any[]> = 
  T extends [infer First, ...infer Rest] 
    ? First extends any[] ? [...Flatten<First>, ...Flatten<Rest>] : [First, ...Flatten<Rest>]
    : []
```

## Append to object
```ts
type AppendToObject<T, U extends string | number | symbol, V> = 
  {[key in (keyof T) | U]: key extends U ? V : key extends keyof T ? T[key] : never}
```

```ts
// 更简洁的写法
type AppendToObject<T, U extends string | number | symbol, V> = 
  {[key in (keyof T) | U]: key extends keyof T ? T[key] : V}
```

## Absolute
```ts
// your answer
type Replace<S extends string, From extends string, To extends string> =
  From extends '' ? S :
    S extends `${infer P}${From}${infer R}` ? `${P}${To}${R}` : S

type Absolute<T extends number | string | bigint> = Replace<`${T}`, '-', ''>
```

```ts
// 优解
type Absolute<T extends number | string | bigint> = 
  `${T}` extends `-${infer R}` ? `${R}` : `${T}`
```

使用模板字符串将数组转换为字符串时，编译器会自动进行转换
```ts
type NumToStr<
  T extends number | bigint
> = `${T}`

type A = NumToStr<123> // "123"
type B = NumToStr<1_2345> // "12345"
type C = NumToStr<0xF123> // "61731"
type D = NumToStr<0o123> // 83
type E = NumToStr<1e5> // "100000"
type F = NumToStr<123n> // 123
```

## String to Union
```ts
type StringToUnion<T extends string> = 
  T extends `${infer R}${infer Rest}` ? R | StringToUnion<Rest> : never
```

## Merge
```ts
type Merge<F, S> = {
  [key in (keyof F | keyof S)]: key extends keyof S ? S[key] : key extends keyof F ? F[key] : never
}
```

## KebabCase
```ts
// your answer
type IsUpperCase<S extends string> = 
  S extends Uppercase<S> & Lowercase<S> ? false : S extends Uppercase<S> ? true : false

type KebabCase<S, First = true> = 
  S extends `${infer R}${infer Rest}`
    ? `${[IsUpperCase<R>, First] extends [true, false] ? '-' : ''}${IsUpperCase<R> extends true ? Lowercase<R> : R}${KebabCase<Rest, false>}` :
    ''
```

```ts
// 优解
type KebabCase<S extends string> = S extends `${infer S1}${infer S2}`
  ? S2 extends Uncapitalize<S2>
  ? `${Uncapitalize<S1>}${KebabCase<S2>}`
  : `${Uncapitalize<S1>}-${KebabCase<S2>}`
  : S;
```

## Diff
```ts
// your answer
type Diff<O, O1> = {
  [key in (keyof O1 | keyof O ) as key extends (keyof O1 & keyof O) ? never : key ]: key extends keyof O1 ? O1[key] : key extends keyof O ? O[key]: never
}
```

```ts
// better answer
type Diff<O, O1> = Omit<O & O1, keyof (O | O1)>
```

```ts
// better answer
type Diff<O, O1> = {
  [K in Exclude<keyof (O & O1), keyof(O | O1)>]: (O & O1)[K]
}
```

## Any of
```ts
// your answer
type isTrue<T> = T extends 0 | '' | false | [] | {[key: string]: never} | undefined | null ? false : true
type AnyOf<T extends readonly any[]> = 
  T extends [infer R, ...infer Rest] 
    ? isTrue<R> extends true ? true: AnyOf<Rest>
    : false
```

```ts
// better answer
type AnyOf<T extends any[]> = T[number] extends 0 | '' | false | [] | {[key: string]: never} | undefined | null
? false : true;
```

## IsNever
```ts
type IsNever<T> = [T] extends [never] ? true : false
```