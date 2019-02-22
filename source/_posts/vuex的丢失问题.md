---
title: vuex丢失问题
date: 2019-02-19 10:25:30
tags: [vue]
categories: vue

---
在使用`vuex`时遇到的一个问题是一旦页面刷新，存在`vuex`的数据全部丢失。即刷新页面以前的内存就被释放了，脚本重新加载，变量重新赋值。

如何解决这个问题？

首先来说已经有很好的第三方插件帮我们做到`vuex`持久化，譬如[vuex-persistedstate](https://github.com/robinvdvleuten/vuex-persistedstate)

当然我在项目中并没有采取第三方插件，而是采用了`localStorage`这个方案。

首先我在`app.vue`里监听页面刷新：

```js
 //在页面刷新时将vuex里的信息保存到localStorage里
window.addEventListener("beforeunload", () => {
  localStorage.setItem(VERSION,JSON.stringify(this.$store.state));    
});
window.addEventListener("pagehide", () => {
  localStorage.setItem(VERSION,JSON.stringify(this.$store.state));
});
```

页面一旦刷新则将`vuex`的`state`存入本地，然后在main.js中：

```js
// 登录拦截，无法依据后端判断
// 版本号 ，是否需要用户重新登录
// 需要则修改版本号
global.VERSION = 'version4'
// local和store的混合
const reSet = (store, local) => {
    for (let i in store) {
        store[i] = store[i] ? store[i] : local[i]
    }
    return store
}
router.beforeEach((to, from, next) => {
    // 在页面加载时读取localStorage里的状态信息;

    let local = JSON.parse(localStorage.getItem(VERSION))
    if (localStorage.getItem(VERSION)) {
        store.replaceState(reSet(store.state, local))
    } else {
        localStorage.clear()
        store.commit('RESETSTATE')
    }

    store.state.uid ||
    to.path === '/' ||
    to.path === '/login' ||
    to.path == '/regist' ||
    to.path === '/forgetPass' ||
    to.path === '/loanDetails' ||
    to.path === '/lendingDetails' ||
    to.path === '/creditInfo'
        ? next()
        : router.push('/login')
})
```

在这里进入路由之前要先将`localStorage`的数据传递给`vuex`，但这里有个问题是`localStorage`内的数据并不是完全和`vuex`的数据是同步的。即我在页面A刷新后的状态会与我在其他页面的状态修改造成冲突。

于是我定义了一个`reSet`还混杂`vuex`和`localStorage`的数据，以有数据的为标准来集合。

当然这样做的话，每一次的路由跳转都要进行这一步。以后会尝试其他的方案。

