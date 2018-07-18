---
title:  关于es6中的遍历器
date: 2018-07-17 19:33:05
tags: [js]
categories: js

---

### 遍历器
> 一种接口，为各种不同的数据结构提供统一的访问机制。任何数据只要部署了`Iterator`接口，就可以完成遍历操作。<b>即被认为是可遍历的数据</b>

#### iterator的作用
- 为各种数据结构，提供一个统一的、简便的访问接口
- 使得数据结构的成员能够按某种次序排列
- ES6 创造了一种新的遍历命令`for...of`循环，Iterator 接口主要供`for...of`消费

#### Iterator 的遍历过程
1. 创建一个指针对象，指向当前数据结构的起始位置。也就是说，遍历器对象本质上，就是一个指针对象。
2. 第一次调用指针对象的next方法，可以将指针指向数据结构的第一个成员。
3. 第二次调用指针对象的next方法，指针就指向数据结构的第二个成员。
4. 不断调用指针对象的next方法，直到它指向数据结构的结束位置。
<!-- more -->
#### 可迭代对象规则

1. 主对象/类应该存储一些数据。
2. 主对象/类必须有全局的“众所周知的”`symbol`，即`symbol.iterator`作为它的属性，然后按照从规则`#3`到`#6`的每条规则来实现一个特有的方法。
3. `symbol.iterator` 方法必须返回另一个对象 —— 一个“迭代器(iterator)”对象。
4. “迭代器(iterator)”对象必须有一个名为 next 的方法。
5. `next`方法应该可以访问存储在规则`#1`的数据。
6. 如果我们调用 iteratorObj.next() ，应该返回存储在规则 #1 中的数据，如果想返回更多的值使用格式`{value:<stored data>, done: false}`，如果不想返回其他更多的值则使用格式`{done: true}`。

#### 模拟next()

```js
let it = makeIterator(['a', 'b']);

it.next() // { value: "a", done: false }
it.next() // { value: "b", done: false }
it.next() // { value: undefined, done: true }

function makeIterator(array) {
  let nextIndex = 0;
  return {
    next: function() {
      return nextIndex < array.length ?
        {value: array[nextIndex++], done: false} :
        {value: undefined, done: true};
    }
  };
}
```
#### 默认的iterator接口
>ES6 规定，默认的 `Iterator` 接口部署在数据结构的`Symbol.iterator`属性，或者说，一个数据结构<b>只要具有`Symbol.iterator`属性</b>，就可以认为是“可遍历的”（iterable）。Symbol.iterator属性本身是<b>一个函数</b>，就是当前数据结构默认的遍历器生成函数。执行这个函数，就会返回一个遍历器。至于属性名`Symbol.iterator`，它是一个表达式，返回`Symbol`对象的`iterator`属性，这是一个预定义好的、类型为 `Symbol` 的特殊值，所以要放在方括号内.

```js
const obj = {
  [Symbol.iterator] : function () {
    return {
      next: function () {
        return {
          value: 1,
          done: true
        };
      }
    };
  }
};

var a = obj[Symbol.iterator]();
a.next() // {value: 1, done: true}

// 数组原生部署了Symbol.iterator属性
let arr = ['a', 'b', 'c'];
let iter = arr[Symbol.iterator]();

iter.next() // { value: 'a', done: false }
iter.next() // { value: 'b', done: false }
iter.next() // { value: 'c', done: false }
iter.next() // { value: undefined, done: true }
```
#### 与其他遍历语法的比较

最原始的`for`循环
```js
for (var index = 0; index < myArray.length; index++) {
  console.log(myArray[index]);
}
```

数组内置的`forEach`
```js
arr.forEach(i =>{
    console.log(i)
})
```

`for...in` 为遍历对象而设计

`for...of`循环相比上面几种做法，有一些显著的优点。
- 有着同`for...in`一样的简洁语法，但是没有`for...in`那些缺点。
- 不同于`forEach`方法，它可以与`break`、`continue`和`return`配合使用。
- 提供了遍历所有数据结构的统一操作接口。
