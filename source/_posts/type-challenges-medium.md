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
