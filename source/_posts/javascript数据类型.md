---
title: javascript数据类型
date: 2018-08-26 20:53:35
categories: javascript
tags: 数据类型
---

javascript中有6中简单数据类型（也称为基本数据类型）：Undefied，Null，Boolean，Number，String，Symbol（ES6新增）和一种复杂数据类型——Object。

其中 JS 的数字类型是浮点类型的，没有整型。并且浮点类型基于 IEEE 754标准实现，在使用中会遇到某些 [Bug](https://yuchengkai.cn/docs/zh/frontend/##%E4%B8%BA%E4%BB%80%E4%B9%88-01--02--03)。`NaN` 也属于 `number` 类型，并且 `NaN` 不等于自身。

对于基本类型来说，如果使用字面量的方式，那么这个变量只是个字面量，只有在必要的时候才会转换为对应的类型

```js
let a = 111 // 这只是字面量，不是 number 类型
a.toString() // 使用时候才会转换为对象类型
```

对象（Object）是引用类型，在使用过程中会遇到浅拷贝和深拷贝的问题。

```js
let a = { name: 'FE' }
let b = a
b.name = 'EF'
console.log(a.name) // EF
```

## 类型转换

### 转Boolean

在条件判断时，除了 `undefined`， `null`， `false`， `NaN`， `''`， `0`， `-0`，其他所有值都转为 `true`，包括所有对象。

### 转Number

Number()函数的转换规则如下：

- 如果是 Boolean 值， true 和 false 将分别被转换为 1 和 0。  
- 如果是数字值，只是简单的传入和返回。  
- 如果是 null 值，返回 0。  
- 如果是 undefined，返回 NaN。  
- 如果是字符串，遵循下列规则：  
  - 如果字符串中只包含数字（包括前面带正号或负号的情况），则将其转换为十进制数值，即"1" 会变成 1， "123"会变成 123，而"011"会变成 11（注意：前导的零被忽略了）；  
  - 如果字符串中包含有效的浮点格式，如"1.1"，则将其转换为对应的浮点数值（同样，也会忽 略前导零）；  
  - 如果字符串中包含有效的十六进制格式，例如"0xf"，则将其转换为相同大小的十进制整 数值；  
  - 如果字符串是空的（不包含任何字符），则将其转换为 0；  
  - 如果字符串中包含除上述格式之外的字符，则将其转换为 NaN。  
- 如果是对象，则调用对象的 valueOf()方法，然后依照前面的规则转换返回的值。如果转换 的结果是 NaN，则调用对象的 toString()方法，然后再次依照前面的规则转换返回的字符 串值。  

### 对象转基本类型

对象在转换基本类型时，首先会调用 `valueOf` 然后调用 `toString`。并且这两个方法你是可以重写的。

```js
let a = {
    valueOf() {
    	return 0
    }
}
```

当然你也可以重写 `Symbol.toPrimitive` ，该方法在转基本类型时调用优先级最高。

```js
let a = {
  valueOf() {
    return 0;
  },
  toString() {
    return '1';
  },
  [Symbol.toPrimitive]() {
    return 2;
  }
}
1 + a // => 3
'1' + a // => '12'
```

#### valueOf()

`valueOf()` 方法返回指定对象的原始值。如果对象没有原始值，则`valueOf`将返回对象本身。avaScript的许多内置对象都重写了该函数，以实现更适合自身的功能需要。因此，不同类型对象的valueOf()方法的返回值和返回值类型均可能不同。 

| **对象** | **返回值**                                               |
| -------- | -------------------------------------------------------- |
| Array    | 返回数组对象本身。                                       |
| Boolean  | 布尔值。                                                 |
| Date     | 存储的时间是从 1970 年 1 月 1 日午夜开始计的毫秒数 UTC。 |
| Function | 函数本身。                                               |
| Number   | 数字值。                                                 |
| Object   | 对象本身。这是默认情况。                                 |
| String   | 字符串值。                                               |
|          | Math 和 Error 对象没有 valueOf 方法                      |

#### toString()

`toString()` 方法返回一个表示该对象的字符串。

每个对象都有一个`toString()`方法，当该对象被表示为一个文本值时，或者一个对象以预期的字符串方式引用时自动调用。默认情况下，`toString()`方法被每个`Object`对象继承。如果此方法在自定义对象中未被覆盖，`toString()` 返回 "[object *type*]"，其中`type`是对象的类型。以下代码说明了这一点：

```js
var o = new Object();
o.toString(); // returns [object Object]
```

例子：

```js
[1,2].toString(); //"1,2"

(function A(){}).toString();//"function A(){}"
```

## 操作符

### 布尔操作符

#### 逻辑非

逻辑非（！）  操作符首先会将它的操作数转换为一个布尔值，然后再 对其求反。  

#### 逻辑与

逻辑与（&&）  操作可以应用于任何类型的操作数，而不仅仅是布尔值。在有一个操作数不是布尔值的情况 下，逻辑与操作就不一定返回布尔值；此时，它遵循下列规则：  

- 如果第一个操作数是对象，则返回第二个操作数；  
- 如果第二个操作数是对象，则只有在第一个操作数的求值结果为 true 的情况下才会返回该 对象；  
- 如果两个操作数都是对象，则返回第二个操作数；  
- 如果有一个操作数是 null，则返回 null；  
- 如果有一个操作数是 NaN，则返回 NaN；  
- 如果有一个操作数是 undefined，则返回 undefined  

逻辑与操作属于短路操作，即如果第一个操作数能够决定结果，那么就不会再对第二个操作数求值。对于逻辑与操作而言，如果第一个操作数是 false，则无论第二个操作数是什么值，结果都不再可能是 true 了。  

逻辑或



#### 逻辑或

 逻辑或操作符由两个竖线符号（||）表示  ，与逻辑与操作相似，如果有一个操作数不是布尔值，逻辑或也不一定返回布尔值；此时，它遵循下 列规则：  

- 如果第一个操作数是对象，则返回第一个操作数  
- 如果第一个操作数的求值结果为 false，则返回第二个操作数；  
- 如果两个操作数都是对象，则返回第一个操作数；  
- 如果两个操作数都是 null，则返回 null；  
- 如果两个操作数都是 NaN，则返回 NaN；  
- 如果两个操作数都是 undefined，则返回 undefined  

与逻辑与操作符相似，逻辑或操作符也是短路操作符。也就是说，如果第一个操作数的求值结果为 true，就不会对第二个操作数求值了。  

### 一元操作符  

#### 递增和递减操作符  

后置递增和递减与前置递增和递减有一个非常重要的区别，即递增和递减操作是在包含它们的语句被求 值之后才执行的。  这个区别在某些情况下不是什么问题，例如：  

```
var age = 29;
age++;
```

把递增操作符放在变量后面并不会改变语句的结果，因为递增是这条语句的唯一操作。但是，当语 句中还包含其他操作时，上述区别就会非常明显了。请看下面的例子：  

```
var num1 = 2;
var num2 = 20;
var num3 = num1-- + num2; // 等于 22
var num4 = num1 + num2; // 等于 21
```

所有这 4 个操作符对任何值都适用，也就是它们不仅适用于整数，还可以用于字符串、布尔值、浮 点数值和对象。在应用于不同的值时，递增和递减操作符遵循下列规则。  

- 在应用于一个包含有效数字字符的字符串时，先将其转换为数字值，再执行加减 1 的操作。字 符串变量变成数值变量。  
- 在应用于一个不包含有效数字字符的字符串时，将变量的值设置为 NaN（第 4 章将详细讨论）。 字符串变量变成数值变量。  
- 在应用于布尔值 false 时，先将其转换为 0 再执行加减 1 的操作。布尔值变量变成数值变量。  
- 在应用于布尔值 true 时，先将其转换为 1 再执行加减 1 的操作。布尔值变量变成数值变量。  
- 在应用于浮点数值时，执行加减 1 的操作。  
- 在应用于对象时，先调用对象的 valueOf()方法（第 5 章将详细讨论）以取得一个可供操作的 值。然后对该值应用前述规则。如果结果是 NaN，则在调用 toString()方法后再应用前述规 则。对象变量变成数值变量。  

#### 一元加和减操作符  

在对非数值应用一元加操作符时，该操作符会像 Number()转型函数一样对这个值执行转换。  

在将一元减操作符应用于数值时，该值会变成负数（如上面的例子所示）。而当应用于非数值时， 一元减操作符遵循与一元加操作符相同的规则，最后再将得到的数值转换为负数。

### 加性操作符  

加法操作符（+）的用法如下所示：  

如果两个操作符都是数值，执行常规的加法计算，然后根据下列规则返回结果  ：

- 如果有一个操作数是 NaN，则结果是 NaN；  
- 如果是 Infinity 加 Infinity，则结果是 Infinity；  
- 如果是-Infinity 加-Infinity，则结果是-Infinity；  
- 如果是 Infinity 加-Infinity，则结果是 NaN；  
- 如果是+0 加+0，则结果是+0；  
- 如果是-0 加-0，则结果是-0；  
- 如果是+0 加-0，则结果是+0。  

如果有一个操作数是字符串，那么就要应用如下规则：  

- 如果两个操作数都是字符串，则将第二个操作数与第一个操作数拼接起来；  

- 如果只有一个操作数是字符串，则将另一个操作数转换为字符串，然后再将两个字符串拼接起来。  

如果有一个操作数是对象、数值或布尔值，则调用它们的 toString()方法取得相应的字符串值， 然后再应用前面关于字符串的规则。对于 undefined 和 null，则分别调用 String()函数并取得字符 串"undefined"和"null"。  

只有当加法运算时，其中一方是字符串类型，就会把另一个也转为字符串类型。其他运算只要其中一方是数字，那么另一方就转为数字。

```js
1 + '1' // '11'
2 * '2' // 4
[1, 2] + [2, 1] // '1,22,1'
// [1, 2].toString() -> '1,2'
// [2, 1].toString() -> '2,1'
// '1,2' + '2,1' = '1,22,1'
```

对于加号需要注意这个表达式 `'a' + + 'b'`

```js
'a' + + 'b' // -> "aNaN"
// 因为 + 'b' -> NaN
// 你也许在一些代码中看到过 + '1' -> 1
```

例子：

```js
5 – “4” //1
5 + “4” //54
+!{}[true] //1
+[1] //1
+[1, 2] //NaN
7 – “a” //NaN
7 / 0 //Infinity

5 + “4” //54
5 + null //5
4 == “4.00” //true
4 === “4.00” //false
null == undefined //true
0 == false //true
0 == null //true
null == false //false
undefined == 0 //false
```

### 乘性操作符  

ECMAScript 定义了 3 个乘性操作符：乘法、除法和求模。  如果参与乘性计算的某 个操作数不是数值，后台会先使用 Number()转型函数将其转换为数值。也就是说，空字符串将被当作 0，布尔值 true 将被当作 1。  

### 关系操作符  

小于（<）、大于（>）、小于等于（<=）和大于等于（>=）这几个关系操作符用于对两个值进行比 较，比较的规则与我们在数学课上所学的一样。这几个操作符都返回一个布尔值  。

与 ECMAScript 中的其他操作符一样，当关系操作符的操作数使用了非数值时，也要进行数据转换 或完成某些奇怪的操作。以下就是相应的规则。  

- 如果两个操作数都是数值，则执行数值比较。  
- 如果两个操作数都是字符串，则比较两个字符串对应的字符编码值。  
- 如果一个操作数是数值，则将另一个操作数转换为一个数值，然后执行数值比较。
- 如果一个操作数是对象，则调用这个对象的 valueOf()方法，用得到的结果按照前面的规则执 行比较。如果对象没有 valueOf()方法，则调用 toString()方法，并用得到的结果根据前面 的规则执行比较。  
- 如果一个操作数是布尔值，则先将其转换为数值，然后再执行比较。   

总的来说：

1. 如果是对象，就通过 `toPrimitive` 转换对象
2. 如果是字符串，就通过 `unicode` 字符索引来比较

### 相等操作符  

ECMAScript 的解决方案就是提供两组操作符： 相等和不相等——先转换再比较， 全等和不 全等——仅比较而不转换  。

#### 相等和不相等  

ECMAScript 中的相等操作符由两个等于号（==）表示，如果两个操作数相等，则返回 true。而不 相等操作符由叹号后跟等于号（!=）表示，如果两个操作数不相等，则返回 true。这两个操作符都会 先转换操作数（通常称为强制转型），然后再比较它们的相等性。  

在转换不同的数据类型时，相等和不相等操作符遵循下列基本规则：  

- 如果有一个操作数是布尔值，则在比较相等性之前先将其转换为数值——false 转换为 0，而 true 转换为 1；  
- 如果一个操作数是字符串，另一个操作数是数值，在比较相等性之前先将字符串转换为数值；  
- 如果一个操作数是对象，另一个操作数不是，则调用对象的 valueOf()方法，用得到的基本类 型值按照前面的规则进行比较；  
- null 和 undefined 是相等的。  
- 要比较相等性之前，不能将 null 和 undefined 转换成其他任何值。  
- 如果有一个操作数是 NaN，则相等操作符返回 false，而不相等操作符返回 true。重要提示： 即使两个操作数都是 NaN，相等操作符也返回 false；因为按照规则， NaN 不等于 NaN。  
- 如果两个操作数都是对象，则比较它们是不是同一个对象。如果两个操作数都指向同一个对象， 则相等操作符返回 true；否则，返回 false。  

```
1.23” == 1.23  //true
0 == false  //true
null == undefined //true
new Object() == new Object()  //false
[1, 2] == [1, 2] //false
```

Javascript中对象的比较是用引用比较，而不是用值去比较，所以，这里的[1, 2] == [1, 2] //false 因为他们两个不是完全相同的两个对象，我们定义一个对象x，然后让x和x做比较，只有这样才会是true，同样是两个空对象，他们之间也不会相等。

#### 全等和不全等  

除了在比较之前不转换操作数之外，全等和不全等操作符与相等和不相等操作符没有什么区别。 全 等操作符由 3 个等于号（===）表示，它只在两个操作数未经转换就相等的情况下返回 true 。 

例子：

```
1 == ‘1’ //true
1 === ‘1’ //false
1 + ‘2’ === ‘1’ + 2  //true
1 + ‘2’ === ‘1’ + new Number(2) //true
1 + true === false + 2 //true
1 + null == undefined  + 1 //false
a’ - ‘b’ == ‘b’ - ‘a’//false
```



**总结：**

1. 对于a === b：

- 类型不同，返回false

- 类型相同
  - NaN ≠ NaN
  - new Object ≠ new Object
  - null === null
  - undefined === undefined

2. 对于a == b：

- 类型相同，同===
- 类型不同，尝试类型转换和比较:
  - null == undefined 相等
  - number == string 转number     `1 == “1.0" // true`
  - boolean == ?  转number     `  1 == true  // true`
  - object == number | string 尝试对象转为基本类型 ` new String('hi') == ‘hi’ // true`
  - 其它：false

这里来解析一道题目 `[] == ![] // -> true` ，下面是这个表达式为何为 `true` 的步骤

```js
// [] 转成 true，然后取反变成 false
[] == false
// 根据第 8 条得出
[] == ToNumber(false)
[] == 0
// 根据第 10 条得出
ToPrimitive([]) == 0
// [].toString() -> ''
'' == 0
// 根据第 6 条得出
0 == 0 // -> true
```

## 包装对象

为了便于操作基本类型值， ECMAScript 还提供了 3 个特殊的引用类型： Boolean、 Number 和 String。  

实际上，每当读取一个基本类型值的时候，后台就会创建一个对应的基本包装类型的对象，从而让我们 能够调用一些方法来操作这些数据。来看下面的例子。  

```
var s1 = "some text";
var s2 = s1.substring(2);
```

这个例子中的变量 s1 包含一个字符串，字符串当然是基本类型值。而下一行调用了 s1 的 substring()方法，并将返回的结果保存在了 s2 中。我们知道，基本类型值不是对象，因而从逻辑上 讲它们不应该有方法（尽管如我们所愿，它们确实有方法）。其实，为了让我们实现这种直观的操作， 后台已经自动完成了一系列的处理。当第二行代码访问 s1 时，访问过程处于一种读取模式，也就是要 从内存中读取这个字符串的值。而在读取模式中访问字符串时，后台都会自动完成下列处理。  

(1) 创建 String 类型的一个实例； (2) 在实例上调用指定的方法； (3) 销毁这个实例  

可以将以上三个步骤想象成是执行了下列 ECMAScript 代码  

```
var s1 = new String("some text");
var s2 = s1.substring(2);
s1 = null;
```

经过此番处理，基本的字符串值就变得跟对象一样了。而且，上面这三个步骤也分别适用于 Boolean 和 Number 类型对应的布尔值和数字值。  

引用类型与基本包装类型的主要区别就是对象的生存期。使用 new 操作符创建的引用类型的实例， 在执行流离开当前作用域之前都一直保存在内存中。而自动创建的基本包装类型的对象，则只存在于一 行代码的执行瞬间，然后立即被销毁。这意味着我们不能在运行时为基本类型值添加属性和方法。  

来看 下面的例子：  

```
var s1 = "some text";
s1.color = "red";
alert(s1.color); //undefined
```

在此，第二行代码试图为字符串 s1 添加一个 color 属性。但是，当第三行代码再次访问 s1 时， 其 color 属性不见了。问题的原因就是第二行创建的 String 对象在执行第三行代码时已经被销毁了。 第三行代码又创建自己的 String 对象，而该对象没有 color 属性。  

当然，可以显式地调用 Boolean、 Number 和 String 来创建基本包装类型的对象。不过，应该在 绝对必要的情况下再这样做，因为这种做法很容易让人分不清自己是在处理基本类型还是引用类型的 值。对基本包装类型的实例调用 typeof 会返回"object"，而且所有基本包装类型的对象都会被转换 为布尔值 true。  

```js
var a = “string”;
alert(a.length); //6
a.t = 3;
alert(a.t);//undefined
```

在javascript中，当尝试将基本类型以对象的方式去使用的时候，比如访问length属性，或者增加属性，javascript会智能的将基本类型转换为对应的包装类型对象，相当于new Sring()，返回的值和字符串值是一样的，当你完成这个访问后，这个临时对象会被销毁掉，所以你之后再输入a.t值为undefined。

## 类型检测

javascript中用于类型检测的方式主要包括：

typeof和instanceof操作符，Object.prototype.toString方法，constructor属性。

### typeof

`typeof` 对于基本类型，除了 `null` 都可以显示正确的类型

```js
typeof 1 // 'number'
typeof '1' // 'string'
typeof undefined // 'undefined'
typeof true // 'boolean'
typeof Symbol() // 'symbol'
typeof b // b 没有声明，但是还会显示 undefined
typeof` 对于对象，除了函数都会显示 `object
typeof [] // 'object'
typeof {} // 'object'
typeof console.log // 'function'
typeof(new Object() ) === “object”
typeof([1， 2]) === “object”
typeof(NaN) === “number”
typeof(typeof(‘string’))  //'string'
```

对于 `null` 来说，虽然它是基本类型，但是会显示 `object`，这是一个存在很久了的 Bug

```js
typeof null // 'object'
```

PS：为什么会出现这种情况呢？因为在 JS 的最初版本中，使用的是 32 位系统，为了性能考虑使用低位存储了变量的类型信息，`000` 开头代表是对象，然而 `null` 表示为全零，所以将它错误的判断为 `object`。虽然现在的内部类型判断代码已经改变了，但是对于这个 Bug 却是一直流传下来。

从逻辑角度来看， null 值表示一个空对象指针，而这也正是使用 typeof 操作符检测 null 值时会返回"object"的原因。

如果定义的变量准备在将来用于保存对象，那么最好将该变量初始化为 null 而不是其他值。这样 一来，只要直接检查 null 值就可以知道相应的变量是否已经保存了一个对象的引用，如下面的例子所示：  

```
if (car != null){
// 对 car 对象执行某些操作
}
```

实际上， undefined 值是派生自 null 值的，因此 ECMA-262 规定对它们的相等性测试要返回 true：  

```
alert(null == undefined); //true
```

如果我们想获得一个变量的正确类型，可以通过 `Object.prototype.toString.call(xx)`。这样我们就可以获得类似 `[Object Type]` 的字符串。

```js
let a
// 我们也可以这样判断 undefined
a === undefined
// 但是 undefined 不是保留字，能够在低版本浏览器被赋值
let undefined = 1
// 这样判断就会出错
// 所以可以用下面的方式来判断，并且代码量更少
// 因为 void 后面随便跟上一个组成表达式
// 返回就是 undefined
a === void 0
```

### instanceof

`instanceof` **运算符**用来测试一个对象在其原型链中是否存在一个构造函数的 `prototype` 属性。

语法为：

```j&#39;s
object instanceof constructor
```

用来检测 `constructor.prototype `是否存在于参数 `object` 的原型链上。

但是**不同window或iframe间的对象类型检测不能使用instanceof！**

在浏览器中，我们的脚本可能需要在多个窗口之间进行交互。多个窗口意味着多个全局环境，不同的全局环境拥有不同的全局对象，从而拥有不同的内置类型构造函数。这可能会引发一些问题。比如，表达式 `[] instanceof window.frames[0].Array` 会返回`false`，因为 `Array.prototype !== window.frames[0].Array.prototype`，并且数组从前者继承。

起初，你会认为这样并没有意义，但是当你在你的脚本中开始处理多个frame或多个window以及通过函数将对象从一个窗口传到另一个窗口时，这就是一个有效而强大的话题。比如，实际上你可以通过使用 `Array.isArray(myObj)` 或者`Object.prototype.toString.call(myObj) === "[object Array]"`来安全的检测传过来的对象是否是一个数组。

```js
[1, 2] instanceof Array === true
new Object() instanceof Array === false
null instanceof Object //false
[null] instanceof Object //true
```



```js
function Person() {}
function Student() {}
Student.prototype = new Person()
Student.prototype.constructor = student
var bosn = new Student()
bosn instanceof Studeng //true
var one = new Person()
one instanceof Person //true
one instanceof Student //false
bosn instanceof Person //true
```

### Object.prototype.toString

在任何值上调用 Object 原生的 toString()方法，都会返回一个[object NativeConstructorName]格式的字符串。每个类在内部都有一个[[Class]]属 性，这个属性中就指定了上述字符串中的构造函数名。举个例子吧。  

 ```js
var value = [1, 2];
alert(Object.prototype.toString.call(value)); //"[object Array]"
 ```

由于原生数组的构造函数名与全局作用域无关，因此使用 toString()就能保证返回一致的值。

```js
Object.prototype.toString.apply([]);// === “[object Array]”;
Object.prototype.toString.apply(function(){}); //=== “[object Function]”;
Object.prototype.toString.apply(null); //=== “[object Null]”
Object.prototype.toString.apply(undefined); //=== “[object Undefined]”
IE6/7/8 Object.prototype.toString.apply(null) 返回”[object Object]”
Object.prototype.toString.apply(new Date); // [object Date]
Object.prototype.toString.call(Math); // [object Math]

Object.prototype.toString.apply([]).slice(8, -1);//"Array"
```



```js
{}.toString.apply(new String(‘str’)); //“[object String]”
{}.toString.apply(‘str’); //“[object String]”
test”.substring(0 ,1) //'t'
```

### constructor

返回创建实例对象的 [`Object`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object) 构造函数的引用。注意，此属性的值是对函数本身的引用，而不是一个包含函数名称的字符串。对原始类型来说，如`1`，`true`和`"test"`，该值只可读。

所有对象都会从它的原型上继承一个 `constructor` 属性：

```
var o = {};
o.constructor === Object; // true

var o = new Object;
o.constructor === Object; // true

var a = [];
a.constructor === Array; // true

var a = new Array;
a.constructor === Array // true

var n = new Number(3);
n.constructor === Number; // true
```

### 小结

typeof：适合基本类型及function检测，遇到null失效。

instanceof：适合自定义对象，也可以用来检测原生对象，在不同iframe和window间检测时失效。

[[Class]]：通过{}.toString拿到，适合内置对象和基元类型，遇到null和undefined失效(IE678等返回[object
Object])。

## 运算符优先级

运算符的优先级决定了表达式中运算执行的先后顺序，优先级高的运算符最先被执行。

### 结合性

结合性决定了拥有相同优先级的运算符的执行顺序。考虑下面这个表达式：

```html
a OP b OP c
```

左结合（左到右）相当于把左边的子表达式加上小括号`(a OP b) OP c`，右关联（右到左）相当于`a OP (b OP c)`。赋值运算符是右关联的，所以你可以这么写：

```html
a = b = 5;
```

结果 `a` 和 `b` 的值都会成为5。这是因为赋值运算符的返回结果就是赋值运算符右边的那个值，具体过程是：`b`被赋值为5，然后`a`也被赋值为 `b=5` 的返回值，也就是5。

### 汇总表

下面的表将所有运算符按照优先级的不同从高到低排列。

| 优先级                                                       | 运算类型                                                     | 关联性        | 运算符      |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------- | ----------- |
| 20                                                           | [`圆括号`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Grouping) | n/a           | `( … )`     |
| 19                                                           | [`成员访问`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Property_Accessors#%E7%82%B9%E7%AC%A6%E5%8F%B7%E8%A1%A8%E7%A4%BA%E6%B3%95) | 从左到右      | `… . …`     |
| [`需计算的成员访问`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Property_Accessors#%E6%8B%AC%E5%8F%B7%E8%A1%A8%E7%A4%BA%E6%B3%95) | 从左到右                                                     | `… [ … ]`     |             |
| [`new`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new) (带参数列表) | n/a                                                          | `new … ( … )` |             |
| [函数调用](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Functions) | 从左到右                                                     | `… ( … )`     |             |
| 18                                                           | [new](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new) (无参数列表) | 从右到左      | `new …`     |
| 17                                                           | [后置递增](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Increment)(运算符在后) | n/a           | `… ++`      |
| [后置递减](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Decrement)(运算符在后) | `… --`                                                       |               |             |
| 16                                                           | [逻辑非](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Logical_Operators#Logical_NOT) | 从右到左      | `! …`       |
| [按位非](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Bitwise_NOT) | `~ …`                                                        |               |             |
| [一元加法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Unary_plus) | `+ …`                                                        |               |             |
| [一元减法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Unary_negation) | `- …`                                                        |               |             |
| [前置递增](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Increment) | `++ …`                                                       |               |             |
| [前置递减](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Decrement) | `-- …`                                                       |               |             |
| [typeof](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/typeof) | `typeof …`                                                   |               |             |
| [void](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/void) | `void …`                                                     |               |             |
| [delete](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/delete) | `delete …`                                                   |               |             |
| [await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await) | `await …`                                                    |               |             |
| 15                                                           | [幂](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Exponentiation) | 从右到左      | `… ** …`    |
| 14                                                           | [乘法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Multiplication) | 从左到右      | `… * …`     |
| [除法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Division) | `… / …`                                                      |               |             |
| [取模](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Remainder) | `… % …`                                                      |               |             |
| 13                                                           | [加法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Addition) | 从左到右      | `… + …`     |
| [减法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Subtraction) | `… - …`                                                      |               |             |
| 12                                                           | [按位左移](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators) | 从左到右      | `… << …`    |
| [按位右移](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators) | `… >> …`                                                     |               |             |
| [无符号右移](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators) | `… >>> …`                                                    |               |             |
| 11                                                           | [小于](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Comparison_Operators#Less_than_operator) | 从左到右      | `… < …`     |
| [小于等于](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Comparison_Operators#Less_than__or_equal_operator) | `… <= …`                                                     |               |             |
| [大于](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comparison_Operators#Greater_than_operator) | `… > …`                                                      |               |             |
| [大于等于](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Comparison_Operators#Greater_than_or_equal_operator) | `… >= …`                                                     |               |             |
| [in](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/in) | `… in …`                                                     |               |             |
| [instanceof](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/instanceof) | `… instanceof …`                                             |               |             |
| 10                                                           | [等号](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Comparison_Operators#Equality) | 从左到右      | `… == …`    |
| [非等号](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Comparison_Operators#Inequality) | `… != …`                                                     |               |             |
| [全等号](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Comparison_Operators#Identity) | `… === …`                                                    |               |             |
| [非全等号](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Comparison_Operators#Nonidentity) | `… !== …`                                                    |               |             |
| 9                                                            | [按位与](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Bitwise_AND) | 从左到右      | `… & …`     |
| 8                                                            | [按位异或](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Bitwise_XOR) | 从左到右      | `… ^ …`     |
| 7                                                            | [按位或](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Bitwise_OR) | 从左到右      | `… | …`     |
| 6                                                            | [逻辑与](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Logical_Operators#Logical_AND) | 从左到右      | `… && …`    |
| 5                                                            | [逻辑或](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Logical_Operators#Logical_OR) | 从左到右      | `… || …`    |
| 4                                                            | [条件运算符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Conditional_Operator) | 从右到左      | `… ? … : …` |
| 3                                                            | [赋值](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Assignment_Operators) | 从右到左      | `… = …`     |
| `… += …`                                                     |                                                              |               |             |
| `… -= …`                                                     |                                                              |               |             |
| `… *= …`                                                     |                                                              |               |             |
| `… /= …`                                                     |                                                              |               |             |
| `… %= …`                                                     |                                                              |               |             |
| `… <<= …`                                                    |                                                              |               |             |
| `… >>= …`                                                    |                                                              |               |             |
| `… >>>= …`                                                   |                                                              |               |             |
| `… &= …`                                                     |                                                              |               |             |
| `… ^= …`                                                     |                                                              |               |             |
| `… |= …`                                                     |                                                              |               |             |
| 2                                                            | [yield](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/yield) | 从右到左      | `yield …`   |
| [yield*](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/yield*) | `yield* …`                                                   |               |             |
| 1                                                            | [展开运算符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Spread_operator) | n/a           | `...` …     |
| 0                                                            | [逗号](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Comma_Operator) | 从左到右      | `… , …`     |

参考资料：

JS高级程序设计

[interviewmap](https://yuchengkai.cn/docs/zh/frontend/#%E5%86%85%E7%BD%AE%E7%B1%BB%E5%9E%8B)

[javascript深入浅出](https://www.imooc.com/learn/277)

[运算符优先级-mdn](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Operator_Precedence)