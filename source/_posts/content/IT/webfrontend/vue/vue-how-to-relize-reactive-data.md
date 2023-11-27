---
author: "Medo"
title: "Vue How to implement Reactivity"
date: "2022-09-16"
summary: "How to relize Reactive Data at Vue2 and Vue3, what improvment have been made."
tags: 
  - Five minutes to learn Vue
  - Five minutes to learn anything
categories:
  - IT Tech
  - WebFrontend
  - Vue
  - Five minutes to learn Vue
series:
  - Five minutes to learn Vue
cover: https://jsd.cdn.zzko.cn/gh/ZarkMedo/photoBed@master/img/vue_reactivitity.png
---

## vue-how-to-implement-reactive-data
### How to Use In Code
#### vue2 always use the data() method to return a object
```vue
<script>
 // when change message the view will be updated
export default {
  data() {
    return {
      message: 0
    }
  },
    methods: {
        increment() {
        this.message++
        }
    }
}
</script>
```
#### vue3 use reactive() method to return a reactive object
##### Method1: ref() method
```vue
<script setup>
import { ref } from 'vue'
// demo 
const count = ref(0)
// when change count the view will be updated

increment() {
    count.value++
}
</script>
```
##### Method2: reactive() method
```vue
<script setup>
import { reactive } from 'vue'
// demo
const state = reactive({
  count: 0
})
// when change state.count the view will be updated
increment() {
    state.count++
}
</script>
```

#### Difference between them
<!-- create a table compare them -->
|                       | Vue 2 `data`                                     | Vue 3 `ref`                                        | Vue 3 `reactive`                                  |
| --------------------- | ------------------------------------------------ | -------------------------------------------------- | ------------------------------------------------- |
| Import                | N/A                                              | `import { ref } from 'vue'`                        | `import { reactive } from 'vue'`                  |
| Purpose               | Create reactive data properties for a component  | Create a single reactive primitive value or object | Create a reactive object with multiple properties |
| Returns               | Plain object                                     | Ref object (with `.value` property)                | Reactive proxy                                    |
| Manual update         | Manually assign new values to properties         | Access and modify `.value` property                | Access and modify properties directly             |
| Deep reactivity       | No                                               | Yes                                                | Yes                                               |
| Array reactivity      | No                                               | Yes                                                | Yes                                               |
| Template usage        | Through component instance (`this.propertyName`) | Through `.value` property (`refName.value`)        | Direct usage (`reactiveObject.propertyName`)      |
| Composition API usage | Not directly compatible                          | Compatible                                         | Compatible                                        |

#### Sumary
vue2 use data() method to return a plain object, just inconvinient to use, you need to manually assign new values to properties, and it's not support array reactivity. vue3 use ref() method to return a ref object, it's more convenient to use, you can access and modify `.value` property, and it's support array reactivity. vue3 use reactive() method to return a reactive proxy, it's more convenient to use, you can access and modify properties directly, and it's support array reactivity.


### What's the Benefit of Vue3's Reactivity
#### Enhanced easy of use
- No need to manually assign new values to properties
```vue
<script setup>
import { ref } from 'vue';

const message = ref('Hello, world!');

console.log(message.value); // Output: Hello, world!

message.value = 'Goodbye, world!';
console.log(message.value); // Output: Goodbye, world!
</script>
```

#### More powerful
- Vue 3's reactivity system is more powerful compared to Vue 2. It introduces deep reactivity, allowing automatic tracking of changes in nested objects and arrays.
```vue
<script setup>
import { reactive } from 'vue';

const ShoppingCart = reactive({
  items: ['Apple', 'Banana', 'Orange'],
  addItem(item) {
    this.items.push(item);
  }
});

// Add an item to the shopping cart
ShoppingCart.addItem('Mango');

console.log(ShoppingCart.items); // Output: ['Apple', 'Banana', 'Orange', 'Mango']
</script>
```
#### More flexible
- You can defined a reactive object not only in the setup() method, just use it like a utility function.
##### Example
- get the data from localStorage, when the data changed, the localStorage will be updated.

- ❌ Bad example
```vue
import { ref, watchEffect, computed } from "vue";

let title = ref("");
let todos = ref(JSON.parse(localStorage.getItem('todos')||'[]'));
watchEffect(()=>{
    localStorage.setItem('todos',JSON.stringify(todos.value))
})
function addTodo() {
  todos.value.push({
    title: title.value,
    done: false,
  });
```
- ✔ Good example
```javascript
// useStorage.js, a utility function use to store data in localStorage
import { ref, watchEffect, computed } from "vue";
export function useStorage(name, value=[]){
    let data = ref(JSON.parse(localStorage.getItem(name)|| value))
    watchEffect(()=>{
        localStorage.setItem(name,JSON.stringify(data.value))
    })
    return data
}
```
#### More efficient
- Vue 2's reactivity system is based on `Object.defineProperty()`, which is not very efficient. Vue 3's reactivity system is built on `ES6 proxies`, which optimize performance and efficiency. The fine-grained reactivity updates allow Vue 3 to optimize `rendering and reduce unnecessary re-renders`.
<!-- table to compare vue2, vue3 ref -->
|                | Vue 2 `data`              | Vue 3 `ref`                                         | Vue 3 `reactive` |
| -------------- | ------------------------- | --------------------------------------------------- | ---------------- |
| Implementation | `Object.defineProperty()` | primitive type: `Value Settr`, Object Type: `Proxy` | `Proxy`          |
| Performance    | Poor                      | Good                                                | Good             |
| Scene          | Vue2                      | Vue3 primitive value                                | Vue3 object      |

### How to Realize Vue3's Reactivity
#### Object.defineProperty() Vue2
- [Mdn-defineProperty](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)
##### Example
```javascript
var data = { count: 0 };

Object.keys(data).forEach(function(key) {
  Object.defineProperty(data, key, {
    get: function() {
      console.log('Getter called');
      return data['_' + key];
    },
    set: function(value) {
      console.log('Setter called');
      data['_' + key] = value;
    }
  });
});

console.log(data.count); // Getter called, 0
data.count = 1; // Setter called
console.log(data.count); // Getter called, 1
```
##### Advantage
1. Compatible with all browsers
2. Strong Extendability
##### Disadvantage
1. Can't support Array Reactivitly, you must use the special method to update the array(eg: push, pop...)
2. Can't watch delete the property, you must use the special method to delete the property(eg: delete)
3. more performance consumption
#### Value Setter (Vue3 ref)
##### Example
```javascript
function createRef(value) {
  let _value = value;
  const handlers = {
    get() {
      // 在访问时返回值
      return _value;
    },
    set(newValue) {
      // 在修改时更新值，并触发响应式更新
      _value = newValue;
      // 触发更新逻辑，例如重新渲染视图
      // 这里省略具体的更新逻辑
    }
  };
  return new Proxy({}, handlers);
}

// 创建一个 Ref 对象
const count = createRef(0);

console.log(count); // 输出: {}

console.log(count.value); // 输出: 0

count.value = 1; // 修改 Ref 的值

console.log(count.value); // 输出: 1
```
##### Advantage
1. Just hijack the set method, so it's more efficient than Object.defineProperty()
2. Support for Primitive Value
##### Disadvantage
1. You need to use the .value property to access the value
#### Proxy (Vue3 reactive)
- [Mdn-Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)

##### Example
```javascript
// 创建 reactive 函数
function reactive(obj) {
  // 使用 Proxy 对象包装传入的对象
  return new Proxy(obj, {
    get(target, key) {
      // 在 get 拦截器中进行依赖追踪
      track(target, key);
      return target[key];
    },
    set(target, key, value) {
      // 在 set 拦截器中进行更新操作
      target[key] = value;
      trigger(target, key);
      return true;
    },
  });
}

// 创建依赖追踪函数
function track(target, key) {
  // 在这里建立响应式函数与属性的关联
  // 例如，将当前正在运行的响应式函数与属性建立关联
}

// 创建更新操作函数
function trigger(target, key) {
  // 在这里触发相关的更新操作
  // 例如，重新渲染组件或触发依赖的回调函数
}

// 创建一个普通的 JavaScript 对象
const obj = {
  name: 'Alice',
  age: 25,
};

// 将普通对象转换为响应式对象
const reactiveObj = reactive(obj);

// 访问响应式对象的属性
console.log(reactiveObj.name); // 输出: "Alice"

// 修改响应式对象的属性
reactiveObj.age = 26;

// 输出修改后的属性值
console.log(reactiveObj.age); // 输出: 26
```
##### Advantage
1. Support for Object and Array Reactivity
2. more efficient than Object.defineProperty()
##### Disadvantage
1. Can't support all browsers, you need to use polyfill to support all browsers
2. Can't support Primitive Value

#### Summary
- If you want to support all browsers, you must use Vue2, else you can use Vue3, it's more efficient and more powerful. If the reactive data is primitive value, you can use ref() method, else you can use reactive() method.
![](https://jsd.cdn.zzko.cn/gh/ZarkMedo/photoBed@master/img/Browser_proxy_compatibility.png)