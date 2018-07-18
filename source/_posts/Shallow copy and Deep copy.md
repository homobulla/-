---
title: 深浅拷贝
tags: [js]
categories: js

---

### Shallow copy and Deep copy

#### 如何定义深拷贝、浅拷贝

两者的分界线是什么？
假设我们 B 拷贝了 A，当 A 发生改变时，B 随着改变的话就是浅拷贝，倘若 B 岿然不动则意味着做了深度拷贝。
深复制和浅复制最根本的区别在于是否是真正获取了一个对象的复制实体，而不是引用。

```js
let a = 1;
let b = a;
a = 2;
console.log(b); // 1
let arr1 = [1, 2];
let arr2 = arr1;
arr1.pop();
console.log(arr2); // [1]
```

从上面我们可以看出，深浅拷贝只是针对引用数据类型。因为对于内存占用很大的对象而言，在复制时重新开辟内存的代价很大，于是就直接把指针复制了过来(直接引用)。
浅拷贝仅仅是在栈内存中保存的引用地址+1。深拷贝就是在堆中重新开辟内存空间。
<!--more-->
#### 深拷贝实现

##### Array 的拷贝

最简单的当然是 for 循环了。
ES6 新增了`Array.from`和扩展运算符可以达到深拷贝，即不会发生对象的引用。

```js
let arr = [1, 2, 3, undefined, function a() {}];
let arr2 = Array.from(arr);
let arr3 = [...arr];
arr.pop();
console.log(arr, arr2, arr3); // [ 1, 2, 3, undefined ] [ 1, 2, 3, undefined, [Function: a] ] [ 1, 2, 3, undefined, [Function: a] ]
```

#####  Object 的拷贝

对象比较复杂，要考虑嵌套数组，对象，`undefined`,`null`等情况。
所以我们需要用递归开循环对象，且对数组和对象函数分别判断。

```js
var obj = {
  arr: [1, 2, 3, { a: '111', b: '2222' }, 45],
  c: '333333',
  d: function() {},
  e: undefined,
  f: null
};

function deepCopy(o) {
  if (o instanceof Array) {
    var n = [];
    for (var i = 0; i < o.length; ++i) {
      n[i] = deepCopy(o[i]);
    }
    return n;
  } else if (o instanceof Function) {
    var n = new Function('return ' + o.toString())();
    return n;
  } else if (o instanceof Object) {
    var n = {};
    for (var i in o) {
      n[i] = deepCopy(o[i]);
    }
    return n;
  } else {
    return o;
  }
}

var obj2 = deepCopy(obj);
console.log(obj2 === obj, obj2); //false { arr: [ 1, 2, 3, { a: '111', b: '2222' }, 45 ],c: '333333',d: [Function],e: undefined,f: null }
```

当然，`deepCopy`函数依旧无法将原型链上的属性复制以及未考虑循环引用的问题。当然大多数情况下我们可以用序列化来进行最简单的深拷贝。

```js
var obj2 = JSON.parse(JSON.stringify(obj));
console.log(obj2); //  { arr: [ 1, 2, 3, { a: '111', b: '2222' }, 45 ], c: '333333',f: null }
```

从结果可以看出来，序列化有几个缺点：

- 无法复制函数
- 无法复制 undefined
- 无法解决循环引用

#### 一些第三方库的实现

- lodash —— _.clone() / _.cloneDeep()
- jQuery —— $.clone() / $.extend()
