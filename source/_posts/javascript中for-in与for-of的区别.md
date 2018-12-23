---
title: javascript中for...in与for...of的区别
date: 2018-08-24 09:59:43
categories: javascript
tags: [for...in,for...of,Object.keys()]
---

## for...in与for...of的区别

### 主要区别

1. 推荐在循环对象属性的时候，使用`for...in`,在遍历数组的时候的时候使用`for...of`。
2. `for...in`循环出的是key，`for...of`循环出的是value
3. 注意，`for...of`是ES6新引入的特性。修复了ES5引入的`for...in`的不足
4. `for...of`不能循环普通的对象，需要通过和`Object.keys()`搭配使用

### 解释

假设我们要遍历一个数组的value`let aArray = ['a',123,{a:'1',b:'2'}]`

使用`for...in`循环：

```
for(let index in aArray){
    console.log(`${aArray[index]}`);
}
```

使用`for...of`循环：

```
for(var value of aArray){
    console.log(value);
}
```

咋一看好像好像只是写法不一样而已，那为什么说`for...of`修复了`for...in`的缺陷和不足。
假设我们往数组添加一个属性name:
`aArray.name = 'demo'`,再分别查看上面写的两个循环：

```
for(let index in aArray){
    console.log(`${aArray[index]}`); //Notice!!aArray.name也被循环出来了
}
for(var value of aArray){
    console.log(value);
}
```

所以说，作用于数组的`for-in`循环除了遍历数组元素以外,还会遍历自定义属性。

`for...of`循环不会循环对象的key，只会循环出数组的value，因此`for...of`不能循环遍历**普通对象**,对普通对象的属性遍历推荐使用`for...in`。

如果实在想用`for...of`来遍历普通对象的属性的话，可以通过和`Object.keys()`搭配使用，先获取对象的所有key的数组，然后遍历：

```
var student={
    name:'wujunchuan',
    age:22,
    locate:{
    country:'china',
    city:'xiamen',
    school:'XMUT'
    }
}
for(var key of Object.keys(student)){
    //使用Object.keys()方法获取对象key的数组
    console.log(key+": "+student[key]);
}
```

参考链接：https://segmentfault.com/q/1010000006658882

## for....in遍历和Object.key()遍历key值的区别

### 主要区别

for...in遍历对象属性时，会访问原型链上的可枚举属性，一般配合着hasOwnProperty()方法使用。

而**Object.keys()** 方法会返回一个由给定对象的**自身可枚举属性**组成的数组。数组中属性名的排列顺序和使用 [`for...in`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for...in) 循环遍历该对象时返回的顺序一致。

### 解释

**一.for in**

1.使用 for in 循环遍历对象的属性时，**原型链上的所有可枚举属性**都将被访问：

```
Object.prototype.say="cgl";   // 修改Object.prototype  
    var person ={ age: 18 };
    for (var key in person) {
        console.log(key, person[key]);//这里用person.key得不到对象key的值，用person[key] 或者 eval("person."+key);
    }   //结果： age 18 
                say cgl
```

2.只遍历对象自身的属性，而不遍历继承于原型链上的属性，使用hasOwnProperty 方法过滤一下。  

```
Object.prototype.say="cgl";
    var person ={
        age: 18
    };
    for (var key in person) {
        if(person.hasOwnProperty(key)){
            console.log(key, eval("person."+key));
        }
    }  //结果：age 18 
```



**二.Object.keys()** 

**Object.keys()** 方法会返回一个由给定对象的**自身可枚举属性**组成的数组，数组中属性名的排列顺序和使用 [`for...in`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for...in) 循环遍历该对象时返回的顺序一致 （两者的主要区别是 一个 for-in 循环还会枚举其原型链上的属性）。返回值是这个**对象的所有可枚举属性组成的字符串数组**。

 

```
Object.prototype.say="cgl";
var person ={ age: 18};
console.log(Object.keys(person));//结果  ["age"] 
```

 **小技巧：object对象没有length属性，可以通过Object.keys(person).length，来获取person的长度了。**

参考链接：https://www.cnblogs.com/chenguangliang/p/6678564.html





