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

其实可以这样写：
```ts
type ReplaceAll<S extends string, From extends string, To extends string> = 
  From extends '' ? S :
    S extends `${infer R}${From}${infer U}` ? `${R}${To}${ReplaceAll<U, From, To>}` : S
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

## Is Union(*)
```ts
type IsUnionImpl<T, C extends T = T> = (T extends T ? C extends T ? true : unknown : never) extends true ? false : true;
type IsUnion<T> = IsUnionImpl<T>;
```

```js
IsUnion<string>
=> IsUnionImpl<string, string>
=> (string extends string ? string extends string ? true : unknown : never) extends true ? false : true
=> (string extends string ? true : unknown) extends true ? false : true
=> (true) extends true ? false : true
=> false

IsUnion<string|number>
=> IsUnionImpl<string|number, string|number>
=> (string|number extends string|number ? string|number extends string|number ? true : unknown : never) extends true ? false : true
=> (
  (string extends string|number ? string|number extends string ? true : unknown : never) |
  (number extends string|number ? string|number extends number ? true : unknown : never)
) extends true ? false : true
=> (
  (string|number extends string ? true : unknown) |
  (string|number extends number ? true : unknown)
) extends true ? false : true
=> (
  (
    (string extends string ? true : unknown) |
    (number extends string ? true : unknown)
  ) |
  (
    (string extends number ? true : unknown) |
    (number extends number ? true : unknown)
  )
) extends true ? false : true
=> (
  (
    (true) |
    (unknown)
  ) |
  (
    (unknown) |
    (true)
  )
) extends true ? false : true
=> (true|unknown) extends true ? false : true
=> (unknown) extends true ? false : true
=> true
```

## ReplaceKeys
```ts
type ReplaceKeys<U, T, Y> =
  {[key in keyof U]: key extends T ? key extends keyof Y ? Y[key]: never: U[key]}
```

## Remove Index Signature(PropertyKey*)
// 怎么判断index signature ? - 使用PropertyKey
```ts
type RemoveIndexSignature<T> = {
  [K in keyof T as
    /* filters out all 'string' keys */
    string extends K
      ? never
      /* filters out all 'number' keys */
      : number extends K
        ? never
        /* filers out all 'symbol' keys */
        : symbol extends K
          ? never 
          : K /* all that's left are literal type keys */
  ]: T[K]
}
```

=> 简化
```ts
type RemoveIndexSignature<T, P = PropertyKey> = {
  [K in keyof T as P extends K ? never : K extends P ? K : never]: T[K]
}

// string extends K ? never : K extends string ? K : never |
// number extends K ? never : K extends number ? K : never |
// symbol extends K ? never : K extends symbol ? K : never |

// 如果K是[key in string]
// 那么就是 never | never | never => never


// 如果K是'a'
// 那么就是'a' | never | never => 'a'
```


为什么不是下面这种写法？
```ts
type RemoveIndexSignature<T, P = PropertyKey> = {
  [K in keyof T as P extends K ? never : K]: T[K]
}

// string extends K ? never : K |
// number extends K ? never : K |
// symbol extends K ? never : K

// 如果K是[key in string]
// 那么就是 K | never | never => K


// 如果K是'a'
// 那么就是'a' | never | never => 'a'
```

## Percentage Parser
```ts
TODO
```

## Drop Char
```ts
// your answer - 利用之前的ReplaceAll
type ReplaceAll<S extends string, From extends string, To extends string> = 
  From extends '' ? S :
    S extends `${infer R}${From}${infer U}` ? `${R}${To}${ReplaceAll<U, From, To>}` : S

type DropChar<S extends string, C extends string> = ReplaceAll<S, C, ''>
```

```ts
// smarter answer
type DropChar<S, C extends string> = S extends `${infer L}${C}${infer R}` ? DropChar<`${L}${R}`, C> : S;
```

## MinusOne（*）
```ts
TODO
```

## PickByType
```ts
type PickByType<T, U> = {
  [key in keyof T as T[key] extends U ? key : never] : T[key]
}
```

## StartsWith
```ts
type StartsWith<T extends string, U extends string> = 
  T extends `${U}${infer R}` ? true : false
```

// infer R 可以换成string
```ts
type StartsWith<T extends string, U extends string> = 
  T extends `${U}${string}` ? true : false
```

## EndsWith
```ts
type EndsWith<T extends string, U extends string> =
  T extends `${infer R}${U}` ? true : false
```

## PartialByKeys
```ts
// your answer
type PartialByKeys<T, K extends keyof T = keyof T> = {
  [key in keyof T as key extends K ? never : key] : T[key]
} & {
  [key in keyof T as key extends K ? key : never]? : T[key]
}
```

```ts
type IntersectionToObj<T> = {
  [K in keyof T]: T[K]
}

type PartialByKeys<T, K extends keyof T = keyof T> = IntersectionToObj<{
  [key in keyof T as key extends K ? never : key] : T[key]
} & {
  [key in keyof T as key extends K ? key : never]? : T[key]
}>
```

## RequiredByKeys
```ts
type IntersectionToObj<T> = {
  [K in keyof T]: T[K]
}
type RequiredByKeys<T, K extends keyof T = keyof T> = IntersectionToObj<{
  [key in keyof T as key extends K ? never : key] : T[key]
} & {
  [key in keyof T as key extends K ? key : never]-? : T[key]
}>
```

// 或者使用Omit
```ts
type RequiredByKeys<
  T, 
  K extends keyof T = keyof T,
  O = Omit<T, K> & { [P in K]-?: T[P] }
> = { [P in keyof O]: O[P] }
```

## Mutable
实现一个通用的类型 Mutable<T>，使类型 T 的全部属性可变（非只读）。

```ts
type Mutable<T extends object> = {
  -readonly [key in keyof T]: T[key]
}
```

## OmitByType
从T中，把值不能赋值给U的那些属性排除
```ts
type OmitByType<T, U> = {
  [key in keyof T as T[key] extends U ? never : key] : T[key]
}
```

## ObjectEntries
// 实现类型版本的Object.entries
```ts
interface Model {
  name: string;
  age: number;
  locations: string[] | null;
}
type modelEntries = ObjectEntries<Model> // ['name', string] | ['age', number] | ['locations', string[] | null];
```

```ts
type ObjectEntries<T, K extends keyof T = keyof T> = 
  K extends PropertyKey ? [K, T[K]]: never

// 这个用例Expect<Equal<ObjectEntries<Partial<Model>>, ModelEntries>>会通不过
// 感觉是用例本身有问题，
// 应该和['name', string | undefined] | ['age', number | undefined] | ['locations', string[] | null | undefined]相等
```

// 另一种解法
```ts
// 数组转联合类型用 [number] 作为下标
// ['1', '2']['number'] // '1' | '2'
// 对象则是用 [keyof T] 作为下标

type ObjectToUnion<T> = T[keyof T]
type ObjectEntries<T> = {
  [K in keyof T]-?: [K, T[K]]
}[keyof T]
```

## Shift
```ts
type Shift<T extends any[]> = T extends [infer R, ...infer Rest] ? Rest : []
```

## TupleToNestedObject
例子：
```ts
type a = TupleToNestedObject<['a'], string> // {a: string}
type b = TupleToNestedObject<['a', 'b'], number> // {a: {b: number}}
type c = TupleToNestedObject<[], boolean> // boolean. if the tuple is empty, just return the U type
```

```ts
// your answer
type TupleToNestedObject<T extends string[], U> =
   T extends [] ? U:
    T extends [infer R extends string] ? { [key in R] : U }: 
      T extends [infer R extends string, ...infer Rest extends string[]] ? { [key in R] : TupleToNestedObject<Rest, U>} : U
```

```ts
// better answer
type TupleToNestedObject<T, U> = T extends [infer F,...infer R]?
  {
    [K in F&string]:TupleToNestedObject<R,U>
  }
  :U
```

## Reverse
```ts
type Reverse<T extends any[]> = T extends [infer R, ...infer Rest] ? [...Reverse<Rest>, R] : []
```

## FlipArgument
```ts
// your answer 错误解答 => 有办法在这基础上改正吗
type Reverse<T extends any[]> = T extends [infer R, ...infer Rest] ? [...Reverse<Rest>, R] : []
type FlipArguments<T extends (...args: any[]) => any> = 
  T extends (...args: infer A) => infer R
    ? (...args: { [key in keyof Reverse<A>]: (Reverse<A>)[key]}) => R
    : never
```

```ts
// 正确解答
type Reverse<T extends any[]> = T extends [infer R, ...infer Rest] ? [...Reverse<Rest>, R] : []
type FlipArguments<T extends (...args: any[]) => any> = 
  T extends (...args: infer A) => infer R
    ? (...args: Reverse<A>) => R
    : never
```

## FlattenDepth
```ts
// MinusOne是之前的的MinusOne
type FlattenDepth<T extends any[], num extends number = 1> = 
num extends 0 ? T :
  T extends [infer First, ...infer Rest] 
    ? First extends any[] ? [...FlattenDepth<First, MinusOne<num>>, ...FlattenDepth<Rest, num>] : [First, ...FlattenDepth<Rest, num>]
    : []
```

## BEM style string(`T[number]`和条件分配*)
```ts
type BEM<B extends string, E extends string[],M extends string[]> = 
  `${B}${E extends [] ? '' : `__${E[number]}`}${M extends [] ? '' : `--${M[number]}`}`
```

解析：
```ts
可以通过下标来将数组或者对象转成联合类型
// 数组
T[number]
// 对象  
Object[keyof T]

特殊的，当字符串中通过这种方式申明时，会自动生成新的联合类型，例如这题下面的写法，
type BEM<B extends string, E extends string[], M extends string[]> = `${B}__${E[number]}--${M[number]}`
```

## InorderTraversal
```ts
type InorderTraversal<T extends TreeNode | null> = 
  T extends null ? [] : 
    [
      ...InorderTraversal<T extends TreeNode ? T['left'] : null>,
      T extends TreeNode? T['val'] : null,
      ...InorderTraversal<T extends TreeNode ? T['right']: null>
    ]
```

```ts
type InorderTraversal<T extends TreeNode | null, NT extends TreeNode = NonNullable<T>> =
  T extends null
    ? []
    : [...InorderTraversal<NT['left']>, NT['val'], ...InorderTraversal<NT['right']>]
```

**解析**
为什么需要判断NT?
因为条件分配，T['left']可能是null

## Flip
```ts
// 1.使用as 2.使用``
type Flip<T extends Record<any, any>> = {
  [P in keyof T as `${T[P]}`]: P
}
```

```ts
// 如果只是把下标变成值的话：
type Flip<T extends object> = {
  [key in T[keyof T] & PropertyKey]: key
}
```

## Fibonacci Sequence
```ts
// Helper type to get the length of a tuple
type Length<T extends any[]> = T['length'];

// Fibonacci implementation - index的长度相当于下标
type Fibonacci<
  T extends number,
  Index extends any[] = [1],
  Prev extends any[] = [],
  Current extends any[] = [1]
> = Length<Index> extends T
  ? Length<Current>
  : Fibonacci<T, [...Index, 1], Current, [...Prev, ...Current]>;
```

## AllCombinations
```ts
TODO
```

## Greater Than（*）
```ts
// your answer -> 大数会报错
type GreaterThan<T extends number, U extends number, A extends any[] = [], B extends any[] = []> = 
  A['length'] extends T 
    ? false
    : B['length'] extends U 
      ? true 
      : GreaterThan<T, U, [...A, 1], [...B, 1]>;
```

解法一：利用MinusOne => 不停的减一，直到T和U相等
```ts
type ParseInt<T> = T extends `${infer X extends number}` ? X :  never

type RemoveLeadingZeros<T extends string> = T extends '0' ? T : (
  T extends `${0}${infer Rest}` ? RemoveLeadingZeros<Rest> : T
)

type InnerMinusOne<T extends string> = T extends `${infer X extends number}${infer Y}` ? (
  X extends 0 ? `9${InnerMinusOne<Y>}` : `${[-1, 0, 1, 2, 3, 4, 5, 6, 7, 8][X]}${Y}`
) : ''

type Reverse<T extends string> = T extends `${infer X}${infer Y}` ? `${Reverse<Y>}${X}` : ''

type MinusOne<T extends number> = ParseInt<RemoveLeadingZeros<Reverse<InnerMinusOne<Reverse<`${T}`>>>>>

type InnerGreaterThan<T extends number, U extends number> = T extends U ? true : (
  T extends 0 ? false : InnerGreaterThan<MinusOne<T>, U>
)

type GreaterThan<T extends number, U extends number> = T extends U ? false : (
  U extends 0 ? true : InnerGreaterThan<T, U>
)
```

解法二：利用字符比较
```ts
// 把数字转为字符数组
type SplitString<S extends string> = S extends `${infer D}${infer Rest}`
  ? [D, ...SplitString<Rest>]
  : [];

// 比较两个数字字符大小
type DigitGreaterThan<A extends string, B extends string> = 
  A extends '0' ? (B extends '0' ? false : false) :
  A extends '1' ? (B extends '0' ? true : B extends '1' ? false : false) :
  A extends '2' ? (B extends '0' | '1' ? true : B extends '2' ? false : false) :
  A extends '3' ? (B extends '0' | '1' | '2' ? true : B extends '3' ? false : false) :
  A extends '4' ? (B extends '0' | '1' | '2' | '3' ? true : B extends '4' ? false : false) :
  A extends '5' ? (B extends '0' | '1' | '2' | '3' | '4' ? true : B extends '5' ? false : false) :
  A extends '6' ? (B extends '0' | '1' | '2' | '3' | '4' | '5' ? true : B extends '6' ? false : false) :
  A extends '7' ? (B extends '0' | '1' | '2' | '3' | '4' | '5' | '6' ? true : B extends '7' ? false : false) :
  A extends '8' ? (B extends '0' | '1' | '2' | '3' | '4' | '5' | '6' | '7' ? true : B extends '8' ? false : false) :
  A extends '9' ? (B extends '0' | '1' | '2' | '3' | '4' | '5' | '6' | '7' | '8' ? true : B extends '9' ? false : false) :
  never;

// 1.如果T的长度大于U的长度，返回true
// 2.如果T的长度小于U的长度，返回false
// 3.如果T的长度等于U的长度，比较T和U的每一位，如果T的每一位都大于U的每一位，返回true，否则返回false
type CompareDigitArrays<
  A extends string[],
  B extends string[]
> = 
  A extends [infer ADigit extends string, ...infer ARest extends string[]]
    ? B extends [infer BDigit extends string,...infer BRest extends string[]]
      ? DigitGreaterThan<ADigit, BDigit> extends true
        ? true // A高位大于B高位
        : DigitGreaterThan<ADigit, BDigit> extends false // A高位等于B高位
         ? CompareDigitArrays<ARest, BRest>
         : false // A高位小于B高位
      : false
      : true // A is longer than B
    : false // B is longer than A

type GreaterThan<A extends number, B extends number> = CompareDigitArrays<
  SplitString<`${A}`>,
  SplitString<`${B}`>
>;
```

## Zip
```ts
// your answer
type Zip<T extends any[], U extends any[]> =
  T extends [infer R,...infer Rest]? U extends [infer R1,...infer Rest1]? [[R,R1],...Zip<Rest, Rest1>] : [] : []
```

```ts
// 另一种解法（利用GreaterThan)
type ArrayWithLength<T extends number, U extends any[] = []> = U['length'] extends T ? U : ArrayWithLength<T, [true, ...U]>;
type GreaterThan<T extends number, U extends number> = ArrayWithLength<U> extends [...ArrayWithLength<T>, ...infer _] ? false : true;
type Zip<T extends any[], U extends any[]> = GreaterThan<T['length'], U['length']> extends false ? {
  [key in keyof T]: key extends keyof U ? [T[key], U[key]] : never
} : {
  [key in keyof U]: key extends keyof T ? [T[key], U[key]] : never
}
```

## IsTuple
```ts
type IsTuple<T> = 
  [T] extends [never] ? false :
    T extends [] ? true:
      T extends readonly [any, ...any] ? true : false
```

```ts
// better answer => tuple的length是固定的
type IsTuple<T> = 
  [T] extends [never] ? false :
    T extends readonly any[]
      ? number extends T['length'] ? false : true
      : false
```

## Chunk
```ts
// 取出前N个
type PopN<T extends any[], N, Count extends any[] = []> = 
  Count['length'] extends N ? [] : T extends [infer R, ...infer Rest] ? [R, ...PopN<Rest,N,[...Count,1]>] : []

// 去除前N个后的剩余部分
type RestN<T extends any[], N, Count extends any[] = []> =
 Count['length'] extends N ? T : T extends [infer R, ...infer Rest] ? RestN<Rest, N, [...Count, 1]> : []

// 从T里取出0...N的数组
// chunk剩余部分
type Chunk<T extends any[], N extends number> = 
  T extends [] ? [] : [PopN<T, N>, ...Chunk<RestN<T, N>, N>]
```

## Fill
```ts
type Fill<
  T extends unknown[],
  N,
  Start extends number = 0,
  End extends number = T['length']
> = 
  End extends Start
    ? T
    : T extends [infer R, ...infer Rest]
      ? Start extends 0
        ? [N, ...Fill<Rest, N, 0, MinusOne<End>>]
        : [R, ...Fill<Rest, N, MinusOne<Start>, MinusOne<End>]
      : []
```

```ts
// 利用辅助数组和标志位
// Count['length']视为当前指针
// 当Count['length']一直小于start时，也就是标志位为false,不进行替换，之后增加Count['length'](指针右移)
// 当Count['length']等于start时，也就是标志位为true, 进行替换，之后增加Count['length']（指针右移）,
// 同时，之后的都要进行替换，直到Count['length']等于end
type Fill<
  T extends unknown[],
  N,
  Start extends number = 0,
  End extends number = T['length'],
  Count extends any[] = [],
  Flag extends boolean = Count['length'] extends Start ? true : false
> = Count['length'] extends End
  ? T
  : T extends [infer R, ...infer U]
    ? Flag extends false
      ? [R, ...Fill<U, N, Start, End, [...Count, 0]>]
      : [N, ...Fill<U, N, Start, End, [...Count, 0], Flag>]
    : T
```

## Without
```ts
// your answer
type Without<T, U> = 
  T extends [infer R, ...infer Rest]
    ? U extends any[]
      ? R extends U[number] ? Without<Rest, U> : [R,...Without<Rest, U>]
      : R extends U ? Without<Rest, U> : [R,...Without<Rest, U>]
    : T
```

```ts
// better answer
type ToUnion<T> = T extends any[] ? T[number] : T
type Without<T, U> = 
  T extends [infer R, ...infer F]
    ? R extends ToUnion<U>
      ? Without<F, U>
      : [R, ...Without<F, U>]
    : T
```

## Trunc
```ts
type Trunc<T extends number | string> = 
  `${T}` extends `${infer R}.${any}`
    ? R extends '' | '+' | '-' ? `${R}0` : `${R}`
    : `${T}`
```

## IndexOf
```ts
type IndexOf<T extends any[], U, Count extends any[] = []> =
  T extends [infer R,...infer Rest]
   ? Equal<R, U> extends true
    ? Count['length']
     : IndexOf<Rest, U, [...Count, 1]>
    : -1
```

## Join
```ts
// your answer
type Shift<T extends any[]> = T extends [infer R, ...any[]] ? R : never
type Join<T extends string[], U extends string | number = ','> =
  T['length'] extends 0 ? '' :
    T['length'] extends 1 ? Shift<T> :
      T extends [infer R extends string, ...infer Rest extends any[]] ? `${R}${U}${Join<Rest, U>}` : ''
```

```ts
// better answer
type Join<T extends any[], U extends string | number = ','> =
  T extends [infer R,...infer Rest]
    ? Rest['length'] extends 0
      ? `${R & string}`
      : `${R & string}${U}${Join<Rest, U>}`
    : ''

type Join<T extends any[], U extends string | number=','> =
  T extends [infer F, ...infer R]
    ? R['length'] extends 0
      ? `${F & string}`
      : `${F & string}${U}${Join<R, U>}`
    : ''
```

## LastIndexOf
```ts
// your answer
// 取出前N个后剩余的部分
type RestN<T extends any[], N extends number = 1, Count extends any[] = []> =
 Count['length'] extends N ? T : T extends [infer R, ...infer Rest] ? RestN<Rest, N, [...Count, 1]> : []

type LastIndexOf<T extends any[], U, Count extends any[] = RestN<T>> =
  T extends [...infer Rest, infer R]
   ? Equal<R, U> extends true
     ? Count['length']
      : LastIndexOf<Rest, U, RestN<Count>>
    : -1
```

```ts
// better answer
type LastIndexOf<T extends any[], U> = 
  T extends [...infer I, infer L]
    ? L extends U
      ? I['length']
      : LastIndexOf<I, U>
    : -1;
```

## Unique
```ts
type In<T extends any[], N> =
  T extends [infer R, ...infer Rest]
    ? Equal<R,N> extends true ? true : In<Rest, N>
    : false

type Unique<T extends any[]> = 
  T extends [...infer Rest, infer R]
    ? In<Rest, R> extends true ? Unique<Rest> : [...Unique<Rest>, R]
    : T
```

## MapTypes（精确*）
```ts
// your answer -- 当R是联合类型的时候有问题
type MapTypes<T, R extends {mapFrom: any, mapTo: any}> = {
  [key in keyof T]: T[key] extends R['mapFrom'] ? R['mapTo'] : T[key]
}
```

```ts
// correct answer
type MapTypes<T, R extends {mapFrom: any, mapTo: any}> = {
  [key in keyof T]: T[key] extends R['mapFrom']
    ? R extends { mapFrom: T[key] }
      ? R['mapTo']
      : never
    : T[key]
}
```

## ConstructTuple
```ts
type ConstructTuple<L extends number, Count extends any[] = []> = 
  L extends 0 ? [] :
    Count['length'] extends L ? Count : ConstructTuple<L, [...Count, unknown]>
```

关于大数的解法：https://github.com/type-challenges/type-challenges/issues/14153（可以了解了解）

## NumberRange
```ts
type ToUnion<T extends any[]> = T[number]
type NumberRangeArr<L extends number, H extends number, Count extends any[] = []> = 
  H extends L ? [...Count, H] : NumberRangeArr<L, MinusOne<H>, [...Count, H]>
type NumberRange<L extends number, H extends number> = ToUnion<NumberRangeArr<L,H>>
```

```ts
// better answer
// 根据 Flag 来判断是否开始插入 Res，如果不可以就说明还没到开始的点，继续计数，一旦开始插入，最终的结果就是在 H 的位置返回 Res
type NumberRange<L, H, Count extends any[] = [], Res extends any[] = [] , Flag extends boolean = Count['length'] extends L ? true : false> = 
  Flag extends true
    ? Count['length'] extends H
      ? [...Res, Count['length']][number]
      : NumberRange<L, H, [...Count, ''], [...Res,  Count['length']], Flag>
    : NumberRange<L, H, [...Count, '']>
```

## Combination (*)

题目：给定字符串数组，进行全排列和组合
```ts
// expected to be `"foo" | "bar" | "baz" | "foo bar" | "foo bar baz" | "foo baz" | "foo baz bar" | "bar foo" | "bar foo baz" | "bar baz" | "bar baz foo" | "baz foo" | "baz foo bar" | "baz bar" | "baz bar foo"`
type Keys = Combination<['foo', 'bar', 'baz']>
```

```ts
type Combination<T extends string[], All = T[number], Item = All>
= Item extends string ? 
    `${Item}` | `${Item} ${Combination<[], Exclude<All, Item>>}`
    : never
```

// 这样写有问题（仅记录思路）, ['a', 'b', 'c'] => 会缺少a在中间的情况
```ts
type Combination<T extends string[]> = 
  T extends [infer R,...infer Rest extends string[]]
   ? R | `${R & string} ${Combination<Rest>}` | Combination<Rest> | `${Combination<Rest>} ${R & string}`
    : never
```

## Sequence
```ts
// your answer
type Subsequence<T extends any[]> = 
  T extends [infer R, ...infer Rest]
    ? [R] | Subsequence<Rest> | [R, ...Subsequence<Rest>]
    : []
```

```ts
// better answer
type Subsequence<T extends any[]> = 
  T extends [infer R, ...infer Rest]
    ? Subsequence<Rest> | [R, ...Subsequence<Rest>]
    : []
```

## Check Repeated Chars
```ts
// your answer
type StringToArray<S extends string> = S extends `${infer first}${infer Rest}` ? [first, ...StringToArray<Rest>] : [] 

type In<T extends any[], N> =
  T extends [infer R, ...infer Rest]
    ? Equal<R,N> extends true ? true : In<Rest, N>
    : false

type CheckRepeatedChars<T extends string> = 
  T extends `${infer R}${infer Rest}`
    ? In<StringToArray<Rest>, R> extends true ? true : CheckRepeatedChars<Rest>
    : false
```

```ts
// better answer
type CheckRepeatedChars<T extends string> =
  T extends `${infer R}${infer Rest}`
   ? Rest extends `${string}${R}${string}`? true : CheckRepeatedChars<Rest>
    : false
```

## FirstUniqueCharInde
- 解法一

```ts
// your answer - 一种复杂的解法
type StringToArray<S extends string> = S extends `${infer first}${infer Rest}` ? [first, ...StringToArray<Rest>] : [] 

type IndexOf<T extends any[], U, Count extends any[] = []> =
  T extends [infer R,...infer Rest]
   ? Equal<R, U> extends true
    ? Count['length']
     : IndexOf<Rest, U, [...Count, 1]>
    : -1

type LastIndexOf<T extends any[], U> = 
  T extends [...infer I, infer L]
    ? L extends U
      ? I['length']
      : LastIndexOf<I, U>
    : -1;


// 取一个字符，如果lastIndexof和IndexOf相同，返回
type FirstUniqueCharIndex<T extends string, Count extends any[] = [], Sub extends string = T> = 
  Sub extends `${infer R}${infer Rest}`
    ? LastIndexOf<StringToArray<T>, R> extends IndexOf<StringToArray<T>, R>
      ? Count['length']
      : FirstUniqueCharIndex<T, [...Count, 1], Rest>
    : -1
```

- 解法二

```ts
// 如果字符是空，返回-1
// 否则，取一个字符，如果前半段或者后半段都没有和他重复的，返回；否则，继续
type FirstUniqueCharIndex<T extends string, Count extends any[] = [], Pre extends string=''> =
  // 当前匹配字符是R
  T extends `${Pre}${infer R}${infer Rest}`
    // 判断后半段有没有重复的
    ? Rest extends `${string}${R}${string}`
      // 如果有，递归
      ? FirstUniqueCharIndex<T, [...Count, 1], `${Pre}${R}`>
      // 否则，看前半段有没有重复的
      : Pre extends `${string}${R}${string}`
        // 如果有，那么递归
        ? FirstUniqueCharIndex<T, [...Count,1], `${Pre}${R}`>
        // 否则，返回计数数组的长度
        : Count['length']
    : -1
```

- 解法三(*)

```ts
type FirstUniqueCharIndex<
  T extends string,
  U extends string[] = []
> = T extends `${infer F}${infer R}`
  // 判断F 在不在 U中存在相同的
  ? F extends U[number]
    // 如果在就把F添加进去，此时也相当于索引+1了
    ? FirstUniqueCharIndex<R, [...U, F]>
    // 如果不在，继续判断F在不在R中存在
    : R extends `${string}${F}${string}`
      ? FirstUniqueCharIndex<R, [...U, F]>
      // 双重判断后都不在，就可以返回索引了
      : U['length']
  : -1
```

## ParseUrlParams
```ts
TODO 和Percentage Parser类似？
```

## GetMiddleElement
```ts
type GetMiddleElement<T extends any[]> = 
  T extends [infer R]
    ? [R]
    : T extends [infer R, infer Q]
      ? [R, Q]
      : T extends [any,...infer Rest, any]
        ? GetMiddleElement<Rest>
        : []

// 精简写法
type GetMiddleElement<T extends any[]> = 
  T['length'] extends 0 | 1 | 2?
    T:
    T extends [any,...infer M,any]?
      GetMiddleElement<M>:never
```


```ts
(GreaterThan是之前的题目)
// 使用计数数组，表示指针从前往后移动的步数, 当前指针指向元素R
// 如果是奇数数组，当计数数组长度和Rest length相同，返回[R]
// 如果是偶数数组，当计数数组长度大于Rest length时，返回[R前一个, R]
// 如何判断奇偶？数字末尾是1，3，5，7，9 就是奇数
type IsOdd<N extends number> = `${N}` extends `${string}${1 | 3 | 5 | 7 | 9}` ? true : false
type GetMiddleElement<T extends any[], After extends any[] = T, Count extends any[] = [], Pre extends any= ''> = 
  After extends [infer R, ...infer Rest]
    ? IsOdd<T['length']> extends true
      ? Rest['length'] extends Count['length'] ? [R] : GetMiddleElement<T, Rest, [...Count, 1]>
      : GreaterThan<Count['length'], Rest['length']> extends true ? [Pre, R] : GetMiddleElement<T, Rest, [...Count, 1], R>
    : []
```

## Appear only once

和FirstUniqueCharIndex逻辑类似

```ts
// 判断某个元素是否在数组中
type In<T extends any[], N> =
  T extends [infer R, ...infer Rest]
    ? Equal<R,N> extends true ? true : In<Rest, N>
    : false

// 某个元素，如果前半数组和后半数组中都没有，放入；否则，不放入；使用Result统计结果
type FindEles<T extends any[], Pre extends any[] = [], Result extends any[] = []> = 
  // 当前匹配元素R
  T extends [...Pre, infer R, ...infer Rest]
    // 判断后半数组中有没有
    ? In<Rest, R> extends false
      // 后半数组中没有, 判断前半数组中有没有
      ? In<Pre, R> extends false
        // 前半数组也没有，放入，递归
        ? FindEles<T, [...Pre, R], [...Result, R]>
        // 前半数组有，不放入，递归
        : FindEles<T, [...Pre, R], Result>
      // 后半数组中有，不放入，递归
      : FindEles<T, [...Pre, R], Result>
    : Result
```

如果不考虑`[1,2,number,number]`这种情况：
U用来保存结果，O用来存储计算过的数字
```ts
type FindEles<T extends any[], U extends any[] = [], O extends any[] = []> 
= T extends [infer F, ...infer Rest]
  ? F extends [...Rest, ...O][number]
    ? FindEles<Rest, U, [...O, F]>
    : FindEles<Rest, [...U, F], [...O, F]>
  : U
```
