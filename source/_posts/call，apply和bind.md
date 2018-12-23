---
title: call，apply和bind方法的区别
date: 2018-08-25 17:28:29
categories: javascript
tags: [call, apply, bind]
---

### call和apply

#### 相同点

在调用一个存在的函数时，你可以为其指定一个 `this` 对象。 `this` 指当前对象，也就是正在调用这个函数的对象。 使用 `apply`或`call`， 你可以只写一次这个方法然后在另一个对象中继承它，而不用在新对象中重复写该方法。

作用均是调用一个函数，使其具有指定的this值，并传入调用函数所用参数。

方法的第一个参数为调用此方法函数运行时使用的this值：

- 如果值为对象，则 `this` 指向这个对象；

- 如果值为原始值（数字，字符串，布尔值）， `this` 会指向该原始值的包装对象；
- 如果为null或者undefined，在非严格模式下， `this` 指向window，在严格模式下，this分别指向null和undefined。

例子：

```js
function foo(x, y) { 
  console.log(x, y, this);
}

foo.call(100, 1, 2); // 1, 2, Number(100)
foo.apply(true, [3, 4]); // 3, 4, Boolean(true) 
foo.apply(null); // undefined, undefined, window  非严格模式下
foo.apply(undefined); // undefined, undefined, window 非严格模式下

```

```js
function foo(x, y) { 
'use strict';
  console.log(x, y, this);
}

foo.apply(null); // undefined, undefined, null  严格模式下
foo.apply(undefined); // undefined, undefined, undefined  严格模式下
```

#### 不同点

传参的方式不同，call()方法接受的是若干个**参数的列表**，而apply()方法接受的是一个包含多个**参数的数组**。



### bind

`bind` 和其他两个方法作用也是一致的，只是该方法会返回一个函数。并且我们可以通过 `bind` 实现柯里化。

函数绑定要创建一个函数，可以在特定的 this 环境中 以指定参数调用另一个函数。该技巧常常和回调函数与事件处理程序一起使用，以便在将函数作为变量 传递的同时保留代码执行环境。  

一个简单的 bind()函数接受一个函数和一个环境，并返回一个在给定环境中调用给定函数的函数， 并且将所有参数原封不动传递过去。语法如下：  

```js
function bind(fn, context){
  return function() {
    return fn.apply(context, arguments);
  };
}
```

在 bind()中创建了一个闭包，闭包使用 apply()调 用传入的函数，并给 apply()传递 context 对象和参数。注意这里使用的 arguments 对象是内部函 数的，而非 bind()的。当调用返回的函数时，它会在给定环境中执行被传入的函数并给出所有参数。  

只要是将某个函数指针以值的形式进行传递，同时该函数必须在特定环境中执行，被绑定函数的效用就突显出来了。它们**主要用于事件处理程序以及 setTimeout() 和 setInterval()**。然而，被绑定函数与普通函数相比有更多的开销，它们需要更多内存，同时也因为多重函数调用稍微慢一点，所 以最好只在必要时使用。  

ECMAScript 5 为所有函数定义了一个原生的 bind()方法，进一步简单了操作。  其语法如下：

**语法：**

```javascript
fun.bind(thisArg[, arg1[, arg2[, ...]]])
```

**参数：**

thisArg：当绑定函数被调用时，该参数会作为原函数运行时的 this 指向。当使用`new` 操作符调用绑定函数时，该参数无效。

arg1, arg2, ...：当绑定函数被调用时，这些参数将置于实参之前传递给被绑定的方法。

**返回值**：

 返回由指定的**this**值和初始化参数改造的原函数拷贝。

**描述：**

bind() 函数会创建一个新函数（称为绑定函数），新函数与被调函数（绑定函数的目标函数）具有相同的函数体（在 ECMAScript 5 规范中内置的`call`属性）。当**新函数**被调用时 this 值绑定到 bind() 的第一个参数，**该参数不能被重写。**绑定函数被调用时，bind() 也接受预设的参数提供给原函数。一个绑定函数也能使用`new`操作符创建对象：这种行为就像把原函数当成构造器。提供的 this 值被忽略，同时调用时的参数被提供给模拟函数。

#### 例子

bind的功能之一是绑定函数里的this，或者说改变函数里的this指向：

```js
this.x	= 9; //全局作用域下this指向window，相当于创建了一个全局变量，变量值为9
var module = { 
  x: 81,
  getX: function() { return this.x; }
};

module.getX(); // 81  用对象.属性名方式调用，this指向该对象

var getX = module.getX; 
getX(); // 9  把对象的方法函数赋值给某一变量，调用，this就指向了全局对象

var boundGetX = getX.bind(module); 
boundGetX(); // 81 通过bind方法改变函数运行时的this指向，this在绑定module以后，再调用函数，这个this就是指向module
```

#### bind与柯里化

与函数绑定紧密相关的主题是函数柯里化（function currying），它用于创建已经设置好了一个或多 个参数的函数。函数柯里化的基本方法和函数绑定是一样的：使用一个闭包返回一个函数。两者的区别在于，当函数被调用时，返回的函数还需要设置一些传入的参数。  

函数柯里化实现将函数拆成不同子函数的功能，就是可以提供额外的参数作为对应函数的参数，然后我再去调用时，只要补充了后面剩余的参数就可以了。

柯里化函数通常由以下步骤动态创建：调用另一个函数并为它传入要柯里化的函数和必要参数。下面是创建柯里化函数的通用方式。  

```js
function curry(fn) {
  var args = Array.prototype.slice.call(arguments, 1);
  return function() {
    var innerArgs = Array.prototype.slice.call(arguments);
    var finalArgs = args.concat(innerArgs);
    return fn.apply(null, finalArgs);
    };
}
```

curry()函数的主要工作就是将被返回函数的参数进行排序。 curry()的第一个参数是要进行柯里 化的函数，其他参数是要传入的值。为了获取第一个参数之后的所有参数，在 arguments 对象上调用 了 slice()方法，并传入参数 1 表示被返回的数组包含从第二个参数开始的所有参数。然后 args 数组 包含了来自外部函数的参数。在内部函数中，创建了 innerArgs 数组用来存放所有传入的参数（又一 次用到了 slice()）。有了存放来自外部函数和内部函数的参数数组后，就可以使用 concat()方法将 它们组合为 finalArgs，然后使用 apply()将结果传递给该函数。注意这个函数并没有考虑到执行环 境，所以调用 apply()时第一个参数是 null。   

curry()函数可以按以下方式应用。  

```js
function add(num1, num2){
  return num1 + num2;
}

var curriedAdd = curry(add, 5);
alert(curriedAdd(3)); //8
```

或者

```js
var curriedAdd = curry(add, 5, 12);
alert(curriedAdd()); //17
```

在这里，柯里化的 add()函数两个参数都提供了，所以以后就无需再传递它们了。  

函数柯里化还常常作为函数绑定的一部分包含在其中，构造出更为复杂的 bind()函数。例如：  

```js
function bind(fn, context){
  var args = Array.prototype.slice.call(arguments, 2);
  return function(){
    var innerArgs = Array.prototype.slice.call(arguments);
    var finalArgs = args.concat(innerArgs);
    return fn.apply(context, finalArgs);
  };
}
```

对 curry()函数的主要更改在于传入的参数个数，以及它如何影响代码的结果。 curry()仅仅接受 一个要包裹的函数作为参数，而 bind()同时接受函数和一个 object 对象。这表示给被绑定的函数的参 数是从第三个开始而不是第二个，这就要更改 slice()的第一处调用。另一处更改是在倒数第 3 行将 object 对象传给 apply()。当使用 bind()时，它会返回绑定到给定环境的函数，并且可能它其中某些 函数参数已经被设好。  

ECMAScript 5 的 bind()方法也实现函数柯里化，只要在 this 的值之后再传入另一个参数即可。  

```js
function add(a, b, c) {
 return a + b + c;
}

var func = add.bind(undefined, 100);  //不需要改变this，就传入一个undefined或者null，但是提供额外的参数100
func(1, 2); // 103 func拿到这个函数，相当于100赋给了第一个参数a，调用的时候传入的参数1，2，就赋值给了b，c


var func2 = func.bind(undefined, 200); 
func2(10); // 310  类似的再绑定一个参数200，这时a参数绑定的是100，b参数绑定的是200，再调用传入参数10，就是参数c的值为10
```

如果你的参数非常复杂，需要做类似的拆分的话，就可以用这种方式bind方法或者说curry实现就会非常方便。

#### bind与new

刚刚探讨的bind方法都是直接调用函数的，如果不用bind方法，直接调用，this都指向全局对象，但是如果用new的话就会又特殊些。

```js
function foo() {
this.b = 100; 
return this.a; //这里如果直接调用foo，里面的this指向全局对象，返回全局对象a属性
}
var func = foo.bind({a:1}); //用bind方法传入一个对象参数

func(); // 1  直接调用，this就会指向bind传入的对象参数

new func(); // {b : 100} //如果用new的话，return的不是对象，就会把this作为返回值，并且this会被初始化为一个空对象，这个空对象的继承自foo.prototype，所以这里用new调用，即使用bind绑定了对象，但是this仍然会指向，那个新创建的对象。意思就是用new这种方式调用，bind对于this层面的作用会被忽略掉
```

通过bind方法创建的函数是没有prototype属性的。

#### 配合 `setTimeout`使用

在默认情况下，使用 [`window.setTimeout()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/setTimeout) 时，`this `关键字会指向 [`window`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window) （或全局）对象。当使用类的方法时，需要 `this` 引用类的实例，你可能需要显式地把 `this` 绑定到回调函数以便继续使用实例。

```js
function LateBloomer() {
  this.petalCount = Math.ceil(Math.random() * 12) + 1;
}

// Declare bloom after a delay of 1 second
LateBloomer.prototype.bloom = function() {
  window.setTimeout(this.declare.bind(this), 1000);
};

LateBloomer.prototype.declare = function() {
  console.log('I am a beautiful flower with ' +
    this.petalCount + ' petals!');
};

var flower = new LateBloomer();
flower.bloom();  // 一秒钟后, 调用'declare'方法
```

#### polyfill

```javascript
if (!Function.prototype.bind) {
  Function.prototype.bind = function(oThis) { //以上面的例子为例，这里的oThis为传入的第一个对象参数{a:1}
    if (typeof this !== 'function') { //以上面的例子为例，这里的this指向foo
      // closest thing possible to the ECMAScript 5
      // internal IsCallable function
      throw new TypeError('Function.prototype.bind - what is trying to be bound is not callable');
    }

    var aArgs   = Array.prototype.slice.call(arguments, 1),
        fToBind = this, ////以上面的例子为例，这里的this指向仍然为foo
        fNOP    = function() {},
        fBound  = function() {
          // this instanceof fNOP === true时,说明返回的fBound被当做new的构造函数调用
          return fToBind.apply(this instanceof fNOP
                 ? this
                 : oThis,
//这里的this是bind之后函数func里面的this，作为一般函数调用，this就指向全局对象window，其instanceof fNOP就会返回false，如果是false，this就变为oThis，就指向了{a:1}这个对象，然后用数组方法cancat进行剩余参数和之前参数的完整拼接
//new函数调用，this会指向继承自foo.prototype的那个新创建的对象。其instanceof fNOP就会返回true，如果是true，this就指向的那个对象，相当于忽略bind作用
                 // 获取调用时(fBound)的传参.bind 返回的函数入参往往是这么传递的
                 aArgs.concat(Array.prototype.slice.call(arguments)));
        };

    // 维护原型关系
    if (this.prototype) {
      // Function.prototype doesn't have a prototype property
      fNOP.prototype = this.prototype; 
    }
    // 下行的代码使fBound.prototype是fNOP的实例,因此
    // 返回的fBound若作为new的构造函数,new生成的新对象作为this传入fBound,新对象的__proto__就是fNOP的实例
    fBound.prototype = new fNOP();

    return fBound;//这里的fBound就是最后的返回值,以上面的例子为例,相当于func，里面的过程相当于fBound.prototype=Object.create(fNOP.prototype)
  };
}
```

#### 模拟实现bind

```js
Function.prototype.myBind = function (context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error')
  }
  var _this = this
  var args = [...arguments].slice(1)
  // 返回一个函数
  return function F() {
    // 因为返回了一个函数，我们可以 new F()，所以需要判断
    if (this instanceof F) {
      return new _this(...args, ...arguments)
    }
    return _this.apply(context, [...args, ...arguments])
  }
}
```

参考链接：

[interviewmap](https://yuchengkai.cn/docs/zh/frontend/#call-apply-bind-%E5%8C%BA%E5%88%AB)

[call-mdn](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/call)

[apply-mdn](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)

[bind-mdn](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)

js高级程序设计

