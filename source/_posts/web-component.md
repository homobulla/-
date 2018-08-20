
---
title: Web Component
date: 2018-08-20 16:01:02
tags: [js]
categories: js

---


### Web Compontent

> Web Compontent 是一套创建可重用的定制元素的技术,旨在为了 Web 组件模型统一化

由以下四种技术组成：

-   Custom elements（自定义元素）：一组 JavaScript API，允许您定义 custom elements 及其行为，然后可以在您的用户界面中按照需要使用它们。
-   Shadow DOM（影子 DOM）：一组 JavaScript API，用于将封装的“影子”DOM 树附加到元素（与主文档 DOM 分开呈现）并控制其关联的功能。通过这种方式，您可以保持元素的功能私有，这样它们就可以被脚本化和样式化，而不用担心与文档的其他部分发生冲突。
-   HTML templates（HTML 模板）：<template> 和 <slot> 元素使您可以编写不在呈现页面中显示的标记模板。然后它们可以作为自定义元素结构的基础被多次重用。
-   HTML Imports（HTML 导入）：一旦定义了自定义组件，最简单的重用它的方法就是使其定义细节保存在一个单独的文件中，然后使用导入机制将其导入到想要实际使用它的页面中。HTML 导入就是这样一种机制，尽管存在争议 — Mozilla 根本不同意这种方法，并打算在将来实现更合适的。

即模板插槽、影子节点、自定义元素、Imports。

实现[web component](https://developer.mozilla.org/zh-CN/docs/Web/Web_Components)的基本方法通常如下所示：

1. 使用 ECMAScript 2015 类语法创建一个类，来指定 web 组件的功能.
2. 使用 CustomElementRegistry.define()方法注册您的新自定义元素 ，并向其传递要定义的元素名称、指定元素功能的类以及可选的，其所继承自的元素。
3. 如果需要的话，使用 Element.attachShadow()方法将一个 shadow DOM 附加到自定义元素上。使用通常的 DOM 方法向 shadow DOM 中添加子元素、事件监听器等等。
4. 如果需要的话，使用<template> 和 <slot>方法定义一个 HTML 模板。再次使用常规 DOM 方法克隆模板并将其附加到您的 shadow DOM 中。
5. 在页面任何您喜欢的位置使用自定义元素，就像使用常规 HTML 元素那样。

<!--more-->

#### 自定义标签

即自定义新的 HTML 元素，声明其行为和样式，从自定义的程度分两类：

-   自定义标签元素（Autonomous custom elements）：完全独立于原始 HTML 元素标签的新标签元素，其所有行为需要开发者定义；
-   自定义内置元素（Customized built-in）：基于 HTML 原始元素标签的自定义元素，以便于使用原始元素的特性，开发者只需要定义拓展行为；

##### 创建自定义标签元素

为了创建一个自定义标签元素，我们需要继承 HTMLELement 类:
查看[demo](https://github.com/homobulla/-js-/blob/master/Web%20Component/Autonomous-custom-elements.html)
```js
class GoTop extends HTMLElement {
    constructor() {
        super()
    }
}
customElements.define('go-top', GoTop)
```

##### 创建自定义内置元素

即在某些基础内置元素的扩展。

#### Shadow DOM

即完全封装 DOM 和 style，将组件完全隔离出来，解决方案就是`attachShadow()`方法。
要创建一个影子 DOM，很简单，使用`attachShadow()`方法即可，而需要注意的是所有影子 DOM 必须和一个文档中存在的元素（HTML 内置元素或自定义元素）绑定，才能使用：

```js
var frag = document.createElement('div')
var shadowRoot = frag.attachShadow({ mode: 'open' }) // open 指定为开放的封装模式。
shadowRoot.innerHTML = '<p>Shadow DOM Content</p>'
```

#### HTML template 模板

HTML 模板是支持度最高的特性，其允许开发者定义一个直接被复制时才会使用的 html 模板。
查看[demo](https://github.com/homobulla/-js-/blob/master/Web%20Component/template.html)
```html
<template id="custom-template>
     <h1>HTML Templates are rad</h1>
</template>
```

在 js 中使用：

```js
const template = document.getElementById('custom-template')
const templateContent = template.content
const container = document.getElementById('container')
const templateInstance = templateContent.cloneNode(true)
container.appendChild(templateInstance)
```

参考资料：

-   [2018 来谈谈 Web Component](https://juejin.im/post/5b780a98e51d4538980bf5cf?utm_source=gold_browser_extension)
