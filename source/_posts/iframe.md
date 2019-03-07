---
date: 2019-02-22 14:30:30
title: iframe的使用
tag: js
---

[iframe](https://juejin.im/entry/589d8c532f301e0069bfa595)

关于`iframe`最常听到的是“安全性问题”、“性能问题”，而我在之前也很少使用`iframe`,虽说其功能很强大。

<!--more-->

使用的契机是源于一个微信端的项目。其中有两个报表页面不仅后端返回数据复杂嵌套很深，而且页面内容更是臃肿。而这两个报表同样会在公司app里使用。于是便将两个报表单独抽离出去作为单个页面。

app引用h5页面很简单，一个`webView`即可。而在微信端的话，我只想到了使用`iframe`来嵌套报表。

而在代码层面，遇到最大的问题是兼容性问题。也就是在安卓设备整个`iframe`覆盖整个屏幕高度了，但在苹果设备则显示不全。

```html
  <iframe
        id="daiqian"
        :src="iframeUrl"
        frameborder="0"
        :scrolling="scroll"
        style="width:100vw!important;height:auto!important;"
      ></iframe>

#iframe {
  overflow: scroll;
  -webkit-overflow-scrolling: touch;
  min-width: 100%;
  width: 100%;
  width: 1px;
  -webkit-overflow-scrolling: touch;
  overflow: auto;
}

```

首先在`created`期间判断设备类型：

```js
 let u = navigator.userAgent;
    this.isAndroid = u.indexOf("Android") > -1 || u.indexOf("Adr") > -1; //android终端
    this.isiOS = !!u.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/); //ios终端
```

然后在`mounted`里获取`iframe`元素进行样式调整：

```js
 let _this = this;
    let daiqian = document.querySelector("#daiqian");

    var w = document.documentElement.clientWidth || document.body.clientWidth;
    var h = document.documentElement.clientHeight || document.body.clientHeight;
    console.log(h);
    if (_this.isiOS) {
      this.scroll = "no";
       // 如果是苹果设备，则属性scrolling = 'no'
    }
    // 监控iframe 加载事件，这里是为了防止iframe还未加载完成就对iframe父级页面进行操作。
    if (daiqian.attachEvent) {
        // 兼容IE9以下
      daiqian.attachEvent("onload", function() {
        if (this.isAndroid) {
          daiqian.style.height = `${h}px`;
           // 安卓设备必须显示声明具体屏幕高度。
        }
        setTimeout(() => {
          _this.$Indicator.close();
        }, 1500);
      });
    } else {
      daiqian.onload = function() {
        if (_this.isAndroid) {
          daiqian.style.height = `${h}px`;
           // 安卓设备必须显示声明具体屏幕高度。
            
        }
        setTimeout(() => {
          _this.$Indicator.close();
        }, 1500);
      };
    }
```

总的来说`iframe`很强大，但对其的了解只限于知识点的片面了解。

