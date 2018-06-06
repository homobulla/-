---
title: js的继承
date: 2018-05-30 20:01:02
tags: [js]
categories: js
---


 ### 1. 传统形式 --过多继承了没用的属性

原型链可以用来实现共享,这种方式是利用原型让一个引用类型继承另外一个引用类型的属性和方法。
并没有隔断new 出来的各个对象

```js
Grand.prototype.lastName = 'X';

function Grand() {
    this.name = 'Y';
}

var grand = new Grand();

farther.prototype = grand;

function Father() {
    this.sex = 'man';
}

var farther = new Father();

son.prototype = farther;

function Son() {
    this.age = 12;
}
```

 ### 2. 借用构造函数 

 在子类型构造函数的内部调用超类构造函数，通过使用call()和apply()方法可以在新创建的对象上执行构造函数。
 
 - 不能继承构造函数的原型    只是部分继承
 - 每构造函数都要多走一次函数

```js

function Person(name,age,sex) {
    this.name = name;
    this.age = age;
    this.sex = sex;
}

function Student(name,age,sex,like,time) {
    Person.call(this,name,age,sex);
    this.like = like;
    this.time = time;
}

var student = new Student();
```
 ### 3. 共享原型


```js
Father.prototype.lastName = 'X';

function Father() {
    this.name = 'Y';
}

function Son() {}

Son.prototype = Father.prototype;   
```
其中，`Son.prototype = Father.prototype`指向同一个堆，以至于子类新增的属性方法都会反应到父类上。

 ### 4. 共享原型+中间层隔离

 ```js
Father.prototype.lastName = 'X';

function Father() {
    this.name = 'Y';
}

function F(){}  // 中间层隔断
F.prototype = Father.prototype;
Son.prototype = new F();
Son.prototype.constructor = Son
function Son() {}

```

或者

```js
function Father(){ 
    this.name = "father"; 
    this.colors = ["red","blue","yellow"]; 
} 

Father.prototype.sex = "男"; 

Father.prototype.say = function(){
    console.log("Oh, My God！")
} 

function Child(){ 
    Father.call(this); this.type = "child"; 
} 

Child.prototype = Object.create(Father.prototype)； //Object.create 实现类式继承
Child.prototype.constructor = Child;   // 重定向 constructor
```

最后，我们写一个函数将继承逻辑抽离出来，`F函数`即闭包好处之一私有化变量。

```js

var inherit = (function(){
    let F = function () {};
    return function (Target,Origin) {
        F.prototype = Origin.prototype;
        Target.prototype = new F();
        Target.prototype.constructor = Target;
        Target.prototype.uber = Origin.prototype;  // 超类 即实际继承自哪儿
    }
})()
```
### 5. ES6中的继承

```js
class Parent { } 
class Child extends Parent { 
    constructor(x, y, colors) {  //若无，则系统默认添加
        super(x, y); // 调用父类的constructor(x, y) 
        this.colors = colors; 
    }

    toString() { return this.colors + ' ' + super.toString(); // 调用父类的toString() 
    } 
}
```
`super`表示父类的构造函数，用来新建父类的`this`对象。
ES5的继承，实质是先创造子类的实例对象`this`，然后再将父类的方法添加到`this`上面`即Parent.apply(this)`。ES6 的继承机制完全不同，实质是先创造父类的实例对象`this`（所以必须先调用`super`方法），然后再用子类的构造函数修改`this`。


