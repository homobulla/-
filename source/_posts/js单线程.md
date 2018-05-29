---
title: js单线程
tags: [js]
categories: js

---

事件循环的出现是因为javascript单线程的缘故。

js为什么被设计成单线程呢？
我们知道，js作为一门前台脚本语言，是为了用户交互和操作dom等。在这种应用场景下，js必然不能进行多线程的‘自相矛盾’的操作。比如有两个线程，一个要修改dom的内容，一个却要隐藏这个dom。这种操作交互上的冲突制约了js只能是单线程。

单线程的意思是需要排队。每一个事件每一段代码都需要在排队，等待执行。任务有两类，同步和异步。
同步任务会老老实实排队，但是异步任务尤其是某些耗时长的任务，主线程会将其抛出到一个名叫‘’任务队列的任务中，这个任务队列会被挂载一边。一旦某一异步有了执行结果，会在任务队列里扔一个‘事件’。
当主线程中的同步任务执行完毕了，主线程会搜寻任务队列中的事件进行执行。

以上的一系列过程会被主线程不断重复。所以才有了`Event Loop`。

如果js的执行仅仅是这样的话，无疑会很简单，但可惜并不是。

<! --more -->

当我们学习ES6的`promise`时，会遇到很多关于先后输出的问题。

Js 中，有两类任务队列：宏任务队列（macro tasks）和微任务队列（micro tasks）。宏任务队列可以有多个，微任务队列只有一个。那么什么任务，会分到哪个队列呢？

宏任务：script（全局任务）,`setTimeout`, `setInterval`, `setImmediate`, `I/O`, `UI` `rendering`.
微任务：`process.nextTick`, `Promise`, `Object.observer`, `MutationObserver`.
我们上面讲到，当stack空的时候，就会从任务队列中，取任务来执行。共分3步：

取一个宏任务来执行。执行完毕后，下一步。
取一个微任务来执行，执行完毕后，再取一个微任务来执行。直到微任务队列为空，执行下一步。
更新UI渲染。
Event Loop 会无限循环执行上面3步，这就是Event Loop的主要控制逻辑。 #
事件循环的顺序，决定JS代码的执行顺序。进入整体代码(宏任务)后，开始第一次循环。接着执行所有的微任务。然后再次从宏任务开始，找到其中一个任务队列执行完毕，再执行所有的微任务。听起来有点绕，我们用文章最开始的一段代码说明：
```js

setTimeout(function(){

console.log('setTimeout');

})

newPromise(function(resolve){

console.log('promise');

}).then(function(){

console.log('then');

})

console.log('console');

```
这段代码作为宏任务，进入主线程。

先遇到`setTimeout`，那么将其回调函数注册后分发到宏任务`Event Queue`。(注册过程与上同，下文不再描述)

接下来遇到了`Promise`，`new Promise`立即执行，`then`函数分发到微任务`Event Queue`。

遇到`console.log()`，立即执行。

好啦，整体代码script作为第一个宏任务执行结束，看看有哪些微任务？我们发现了then在微任务`Event Queue`里面，执行。

ok，第一轮事件循环结束了，我们开始第二轮循环，当然要从宏任务`Event Queue`开始。我们发现了宏任务Event Queue中`setTimeout`对应的回调函数，立即执行。

结束。