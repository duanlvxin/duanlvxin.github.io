---
title: vue-vue3新特性
date: 2024-09-01 15:00:00
tags:
  - vue
  - vue3
categories:
  - vue
cover: https://pic3.zhimg.com/v2-43d3198abdc1915708e40c002697c815_1200x500.jpg
---

## 组合式 API (Composition API)
>通过组合式 API，我们可以使用导入的 API 函数来描述组件逻辑。在单文件组件中，组合式 API 通常会与 `<script setup> `搭配使用。这个 setup attribute 是一个标识，告诉 Vue 需要在编译时进行一些处理，让我们可以更简洁地使用组合式 API。比如，`<script setup>` 中的导入和顶层变量/函数都能够在模板中直接使用。(也可以使用setup函数)

文档：https://cn.vuejs.org/api/sfc-script-setup.html

```jsx
<script setup>
import { ref, onMounted } from 'vue'

// 响应式状态
const count = ref(0)

// 用来修改状态、触发更新的函数
function increment() {
  count.value++
}

// 生命周期钩子
onMounted(() => {
  console.log(`The initial count is ${count.value}.`)
})
</script>

<template>
  <button @click="increment">Count is: {{ count }}</button>
</template>
```

## ref函数和reactive函数

都是用来将数据变成响应式的。
ref一般用于基本类型数据，reactive一般用于引用类型数据
(但引用类型数据用ref也可以，只不过需要加.value)

```js
let count = ref(0)
function increase(count) {
  count.value++ // 需要用.value,在template中可以省略
}
```

```js
let list = reative([
  { id: 1, name: 'xiaoming' }
])
function add() {
  list.push({ id: new Date().getTime(), name: 'test' })
}
```

### vue3响应式原理（简单版）

通过Proxy进行属性拦截

## 父子组件通信
props,attrs,slots,emit

### props

https://cn.vuejs.org/guide/components/props.html

```ts
defineProps<{
  msg: string
}>()
```

```js
defineProps({
  msg: {
    type: String,
    required: false,
    default: '默认数据'
  },
  len: Number
})
```

### attrs

```jsx
<script setup>
import { useAttrs } from 'vue'

const attrs = useAttrs()
</script>
```

### slots（插槽）

https://cn.vuejs.org/guide/components/slots.html

```jsx
<script setup>
import { useSlots } from 'vue'

const slots = useSlots()
console.log(slots.default && slots.default())
</script>
```

打印结果示例： 
![打印结果示例](/img/vue3-slots.png)


### emit
```js
const emit = defineEmits(['change', 'delete'])
emit('change', 1)
```

## toRef函数

https://cn.vuejs.org/api/reactivity-utilities.html#toref

使用扩展运算符，这是state内部的属性不是响应式的，使用toRef可以将属性变成响应式的

## computed函数
```js
import { computed } from 'vue'
const total = computed(()=>{
  return amount * price
})
```

## watch和watchEffect函数
```js
import { watch } from 'vue'


```

## 生命周期


## 路由
vue-router v4版本

```js
import { useRouter } from 'vue-router';
const router = useRouter()
const handleClick = () => {
  router.push('/abc')
}
```

## 其他

### v-for 和 v-if 一起使用
vue2是v-for优先级高
vue3是v-if优先级高
