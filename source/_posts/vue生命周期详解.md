---
title: vue生命周期详解
date: 2018-07-20 21:12:04
categories: vue
tags: vue生命周期
---

生命周期图中名词解释：

昨天写demo时发现好像对vue的生命周期不太理解，今天再来学习了一下，看了一些别人写的博客，有的涉及源码的文章其实，没太看懂，但是还是先把看的相关资料整合如下，方便以后回顾查阅。

## 生命周期图

vue官网给的生命周期图如下：

![img](https://user-gold-cdn.xitu.io/2018/5/21/163833416472aa86?imageslim) 

注：该图来自 [vue生命周期解析](https://juejin.im/post/5b02de32f265da0b9265e655)

## 生命周期图中名词解释

| new vue                                | 创建vue实例                                                  |
| -------------------------------------- | ------------------------------------------------------------ |
| init events & lifecycle                | 开始初始化。Init Events 方法初始化事件的相关属性：on、off、emit ； Init Lifecycle方法主要是为vm对象添加了$parent、$root、$children、$refs等属性，以及一些其它的生命周期相关的标识 |
| beforeCreate                           | 组件刚被创建，组建属性计算之前，如data属性等                 |
| init injections & reactivity           | 通过依赖注入导入依赖项                                       |
| created                                | 组件实例创建完成，属性已绑定，此时DOM还未生成                |
| el属性                                 | 检查vue配置，即new Vue{}里面的el项是否存在，有就继续检查template项。没有则等到手动绑定调用vm.$mount() |
| template                               | 检查配置中的template项，如果没有template进行填充被绑定区域，则被绑定区域的el对象的outerHTML（即整个#app DOM对象，包括<div id=”app” >和</div>标签）都作为被填充对象替换掉填充区域 |
| beforeMount                            | 模板编译、挂载之前                                           |
| create vm.$el and replace “el” with it | 编译，并替换了被绑定元素                                     |
| mounted                                | 编译、挂载                                                   |
| Before update                          | 组件更新之前                                                 |
| updated                                | 组件更新之后                                                 |
| destroy                                | 当vm.$destroy()被调用，开始拆卸组件和监听器，生命周期终结    |

来自：  [vue生命周期、钩子理解](https://blog.csdn.net/Coder_Chang/article/details/78758537)

Vue2.0的生命周期钩子一共有10个，结合官方文档作出了下表：

| 生命周期钩子  | 详细                                                         |
| ------------- | ------------------------------------------------------------ |
| beforeCreate  | 在实例初始化之后，数据观测(data observer) 和 event/watcher 事件配置之前被调用。 |
| created       | 实例已经创建完成之后被调用。在这一步，实例已完成以下的配置：数据观测(data observer)，属性和方法的运算， watch/event 事件回调。然而，挂载阶段还没开始，$el 属性目前不可见。 |
| beforeMount   | 在挂载开始之前被调用：相关的 render 函数首次被调用。         |
| mounted       | el 被新创建的 `vm.$el` 替换，并挂载到实例上去之后调用该钩子。如果 root 实例挂载了一个文档内元素，当 mounted 被调用时` vm.$el `也在文档内。 |
| beforeUpdate  | 数据更新时调用，发生在虚拟 DOM 重新渲染和打补丁之前。你可以在这个钩子中进一步地更改状态，这不会触发附加的重渲染过程。 |
| updated       | 由于数据更改导致的虚拟 DOM 重新渲染和打补丁，在这之后会调用该钩子。当这个钩子被调用时，组件 DOM 已经更新，所以你现在可以执行依赖于 DOM 的操作。 |
| activated     | keep-alive 组件激活时调用。                                  |
| deactivated   | keep-alive 组件停用时调用。                                  |
| beforeDestroy | 实例销毁之前调用。在这一步，实例仍然完全可用。               |
| destroyed     | Vue 实例销毁后调用。调用后，Vue 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。 |

（除了beforeCreate和created钩子之外，其他钩子均在服务器端渲染期间不被调用。）

来自[Vue.js2.0生命周期的研究与理解](https://blog.csdn.net/qq_25186543/article/details/79470184)

### callHook

生命周期其实就是在源码的某个时间点执行了callHook方法来调用vm.$options的生命周期钩子方法 

~~~
// vue/src/core/instance/lifecycle.js
export function callHook (vm: Component, hook: string) {
  const handlers = vm.$options[hook] // 获取Vue选项中的生命周期钩子函数
  if (handlers) {
    for (let i = 0, j = handlers.length; i < j; i++) {
      try {
        handlers[i].call(vm) // 执行生命周期函数
      } catch (e) {
        handleError(e, vm, `${hook} hook`)
      }
    }
  }
  if (vm._hasHookEvent) {
    vm.$emit('hook:' + hook)
  }
  popTarget()
}
~~~

例如触发created钩子的方法：callHook(vm, 'created') 

### new Vue()

### 创建Vue对象，通过构造函数的形式创建

~~~
// vue/src/core/instance/index.js
function Vue (options) {
  if (process.env.NODE_ENV !== 'production' &&
    !(this instanceof Vue)
  ) { //判断是否是通过new关键字创建Vue对象
    warn('Vue is a constructor and should be called with the `new` keyword')
  }
  this._init(options) // 初始化各个功能
~~~

### this._init()

~~~
// vue/src/core/instance/init.js
 Vue.prototype._init = function (options?: Object) {
    /* istanbul ignore else */
    if (process.env.NODE_ENV !== 'production') {
      initProxy(vm)
    } else {
      vm._renderProxy = vm
    }
    // expose real self
    vm._self = vm
    initLifecycle(vm) //初始化生命周期
    initEvents(vm) //初始化事件
    initRender(vm) //初始化渲染
    callHook(vm, 'beforeCreate') //触发beforeCreate钩子
    initInjections(vm) // resolve injections before data/props 初始化Inject
    initState(vm) //初始化数据
    initProvide(vm) // resolve provide after data/props 初始化Provide
    callHook(vm, 'created') //触发created钩子

    if (vm.$options.el) { //判断是否有el选项,如果有则执行$mount方法
      vm.$mount(vm.$options.el)
    }
  }
~~~

整个初始化过程中大致做的事情：

- initLifecycle(vm) //初始化生命周期
- initEvents(vm)  //初始化事件
- initRender(vm)  //初始化渲染
- callHook(vm, 'beforeCreate') //触发beforeCreate钩子
- initInjections(vm) // resolve injections before data/props 初始化Inject
- initState(vm) //初始化数据
- initProvide(vm) // resolve provide after data/props 初始化Provide
- callHook(vm, 'created') //触发created钩子

### **Init Events && Lifecycle**

初始化事件和生命周期发生在创建Vue对象的过程中并在触发beforeCreate钩子之前，此过程还包含了初始化渲染。

-  Init Events 方法初始化事件的相关属性：on、off、emit ；
- Init Lifecycle方法主要是为vm对象添加了`$parent`、`$root`、`$children`、`$refs`等属性，以及一些其它的生命周期相关的标识 ;
- initRender(vm)，主要是给vm添加虚拟dom、solt等相关属性和方法 。

###  **beforeCreate()**

该钩子函数在实例初始化之后，数据观测 (data observer) 和 event/watcher 事件配置之前被调用。

beforeCreate执行时：data和el均未初始化，值为undefined 。**就是实例初始化，Vue会将所有的data数据和methods中的函数添加到创建的vue对象上面。** 

### **Init injections && reactivity** 

initInjections(vm) && initProvide(vm)  两个方法配套使用，以允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在起上下游关系成立的时间里始终生效，直白点说就是用于将父组件_provided中定义的值，通过inject注入到子组件。provide 和 inject 绑定并不是可响应的。这是刻意为之的。然而，如果你传入了一个可监听的对象，那么其对象的属性还是可响应的。

initState(vm)  这部分主要是对数据进行操作，props、methods、data、computed、watch 

### **created()**

在实例创建完成后被立即调用。在这一步，实例已完成以下的配置：数据观测 (data observer)，属性和方法的运算，watch/event 事件回调。然而，挂载阶段还没开始，$el 属性目前不可见 。

created执行时：Vue 实例观察的数据对象data已经配置好，已经可以得到app.message的值，但Vue 实例使用的根 DOM 元素el还未初始化。在这个方法中我们可以做一些**数据初始化工作，最常见的就是发送ajax请求来对已经构建完毕的vue对象的静态属性进行一些初始化** 

### 判断是否有’el‘选项

~~~
// vue/src/core/instance/init.js
if (vm.$options.el) {
  vm.$mount(vm.$options.el)
}
~~~

该部分主要是判断是否有‘el’选项，若有则进行“判断是否有template选项”操作，否则进行“vm.$mount(el) is called”操作 。

### Compile template into render && Compile el's outerHTML as template

这部分主要是template进行解析编译。先判断是否有render函数，如果有的话不作处理直接执行mount.call(this, el, hydrating)。如果没有render函数，则获取template，template可以是#id、模版字符串、dom元素，如果没有template，则获取el以及其子内容作为模版

###  beforeMount()

在挂载开始之前被调用：相关的 render 函数首次被调用。该钩子在服务器端渲染期间不被调用。 

beforeMount执行时：data和el均已经初始化，但从{{message}}等现象可以看出此时el并没有渲染进数据，el的值为“虚拟”的元素节点 。 这个过程就是根据编译以后产生的虚拟dom结构来构造一个真实的dom结构， 注意虚拟dom只表示数据结构，真实的dom可以直接添加到html文档结构中来渲染一个效果。 在这期间，Vue还要做一件非常重要的事情就是**通过Vue自己的方式来标识每一个真实的dom节点**，我们在vue里一般都是通过`$els`或者`$refs`来操作对应的dom元素，但是这期间正是标注所有元素的过程，很有可能会出一些意想不到的错误。 

#### mountComponent

在mountComponent()方法中调用了beforeMount钩子，然后在updateComponent中调用了vm._render()，最后返回vm对象

mountComponent在Vue的$mount方法中使用：

### mounted() 

el被新创建的`vm.$el`替换，并挂载到实例上去之后调用该钩子。如果root实例挂载了一个文档内元素，当mounted被调用时`vm.$el`也在文档内。

.mounted执行时：此时el已经渲染完成并挂载到实例上 。在这里我们就可以完成一些对于真实dom的操作，不论是直接访问dom的属性内容或者事件的绑定，都可以在这里放心大胆的做了，一般情况下我们不需要直接操作dom，Vue也不推荐这么做，但是这里你需要知道的是如果你有这种需求，完全可以在这里完成，**挂载的过程就是将生成的真实DOM对象append到挂载点下面**，就是appendChild的过程，这个时候的虚拟dom结构跟生成出来的real dom结构一模一样，我们以后要操作的就是这个虚拟dom结构，就是前面根据模版生成出来的dom数据结构。 

#### mountComponent

判断如果vm.$vnode == null，则设置vm._isMounted = true并调用mounted钩子函数 

### beforeUpdate()

数据更新时调用，发生在虚拟DOM打补丁之前。这里适合在更新之前访问现有的DOM，比如手动移除已添加的事件监听器。 

beforeUpdate和updated触发时，el中的数据都已经渲染完成，但只有updated钩子被调用时候，组件dom才被更新。

 **beforeUpdate这个方法在整个生命周期之中也只被调用一次**，在做什么呢，想一想上面所做的事情，上面是根据模版构造出来一个真实的dom对象，到现在他并没有跟我们的vue实例对象扯上关系，怎么通过我们的vue实例来影响到真实的dom对象呢，怎么把我们的数据绑定的真实dom当中了，以后我们只需要操作数据就可以影响到dom结构的渲染呢？**在这个方法里，Vue同样依据前面的规则根据vue实例提供的数据在模版中的位置来重新生成一个虚拟dom数据结构**，没错，你没看错，**我们要生成两个虚拟dom，前一个dom只是dom结构，并没有绑定我们vue的属性数据，这一个dom是绑定了我们vue实例数据的dom结构，在这个dom生成的过程中，vue根据自己的语法规则，对比如指令，表达式之类的东西进行替换，生成一个新的虚拟dom结构**，接下来我们要做什么，我想大家应该所料不错，没错，就是进行比较两个虚拟dom之间存在的差异。 

#### mountComponent

监测数据发生了变化，并且已经dom被挂载了之后，调用beforeUpdate钩子 

#### vm._update

调用beforeUpdate钩子之后对虚拟dom重新渲染和补丁 

### updated()

由于数据更改导致的虚拟DOM重新渲染和打补丁，在这之后会调用该钩子。 当这个钩子被调用时，组件DOM已经更新，所以你现在可以执行依赖于DOM的操作。然而在大多数情况下，你应该避免在此期间更改状态。如果要相应状态改变，通常最好使用计算属性或watcher取而代之。

 队列执行完Watcher数组的update方法后调用了updated钩子，然后再是调用mounted钩子 .

**update这个方法在我们整个生命周期之内会反复调用**，你会发现，**每一次对vue对象的变更都会触发update方法**，**他就是在反复的生成一个virtual dom**，生成的新dom不断跟之前的dom结构进行比对，也就是**differ算法**，前面说过，**Vue在beforeMount的时候会对每一个dom节点进行标记，就是为了更加快速准确的定位dom节点。**

用一种更加清楚明了的总结方式就是代码：

// 1. 构建虚拟DOM
var tree = el('div', {'id': 'container'}, [
el('h1', {style: 'color: blue'}, ['simple virtal dom']),
el('p', ['Hello, virtual-dom']),
el('ul', [el('li')])
])
// 2. 通过虚拟DOM构建真正的DOM
var root = tree.render()
document.body.appendChild(root)
// 3. 生成新的虚拟DOM
var newTree = el('div', {'id': 'container'}, [
el('h1', {style: 'color: red'}, ['simple virtal dom']),
el('p', ['Hello, virtual-dom']),
el('ul', [el('li'), el('li')])
])
// 4. 比较两棵虚拟DOM树的不同
var patches = diff(tree, newTree)
// 5. 在真正的DOM元素上应用变更
patch(root, patches)

### beforeDestory()

实例销毁之前调用。在这一步，实例仍然完全可用。该钩子在服务器端渲染期间不被调用。 

vm.$destory() is called时被调用 .

### destroyed()

Vue实例销毁后调用。调用后，Vue实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。 

这部分销毁Vue实例，将各种配置清空和移除。移除Watchers、child、events。 

.对Vue实例进行销毁，调用app.$destroy()方法即可将其销毁 ,发现实例依然存在，但是此时变化已经发生在了其他地方，根据官方文档描述：Vue 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。 如果你在子组件也加入destroyed钩子，发现该钩子也会被触发，这也证明了子实例也会被一起销毁。**这里的销毁并不指代'抹去'，而是表示'解绑'**。

主要参考： [vue生命周期解析](https://juejin.im/post/5b02de32f265da0b9265e655)   [VUE生命周期理解](https://zhuanlan.zhihu.com/p/26291416)    [Vue.js2.0生命周期的研究与理解](https://blog.csdn.net/qq_25186543/article/details/79470184)

## **补充说说虚拟dom为什么很快**

- JavaScript执行速度很快
- Dom操作很慢

Chrome刚出来的时候，在Chrome里跑Javascript非常快，给了其它浏览器很大压力。而现在经过几轮你追我赶，各主流浏览器的Javascript执行速度都很快了。但是遗憾的是浏览器厂商只对Javascript引擎进行也极限优化，但是对html文档结构的构建速度和css样式的渲染并没有多少进步，就是说dom引擎和css引擎的发展速度远远更不上js引擎解析js脚本的速度，因为js很多时候都是为了操作dom节点和修饰dom，由于html文档结构重新排布的速度远远没有js执行速度快，这在很多时候就造成了难以理解的逻辑错误，称为难以言说的痛。于是在这种情况下，虚拟dom应用而生了，虚拟dom说白了就是一个js结构的对象，他内部的数据组织方式是按照html文档结构组成的树状结构，我们通过每一次数据的修改来重新构造一个js对象，根据前后对象之间的比对就可以发现具体那个节点发生了变化，然后在真实的dom结构中迅速的定位到元素，进行修改，最小化真实dom的重排和重绘。如果你通过原声js，每次修改dom结构或者内容，你必须从头到尾进行dom节点遍历，造成很大的时间和资源的浪费，这是一点，还有一点就是每一次dom操作导致的结构重新排布，所以最好的dom操作方式是一次性把要修改的东西全部做完，进行一次性行更新。而虚拟dom做到了这一点，尽量少操作dom，尽量最小化dom操作，尽量一次性操作dom，尽量不去查询dom。

主要参考：  [VUE生命周期理解](https://zhuanlan.zhihu.com/p/26291416)

## **面试时生命周期全过程描述**

在谈到Vue的生命周期的时候，我们首先需要创建一个实例，也就是在 new Vue ( ) 的对象过程当中，首先执行了init（init是vue组件里面默认去执行的），在init的过程当中首先调用了beforeCreate，然后在injections（注射）和reactivity（反应性）的时候，它会再去调用created。所以在init的时候，事件已经调用了，我们在beforeCreate的时候千万不要去修改data里面赋值的数据，最早也要放在created里面去做（添加一些行为）。

当created完成之后，它会去判断instance（实例）里面是否含有“el”option（选项），如果没有的话，它会调用vm.$mount(el)这个方法，然后执行下一步；如果有的话，直接执行下一步。紧接着会判断是否含有“template”这个选项，如果有的话，它会把template解析成一个render function ，这是一个template编译的过程，结果是解析成了render函数：

```
render (h) {
  return h('div', {}, this.text)
}
```

解释一下，render函数里面的传参h就是Vue里面的createElement方法，return返回一个createElement方法，其中要传3个参数，第一个参数就是创建的div标签；第二个参数传了一个对象，对象里面可以是我们组件上面的props，或者是事件之类的东西；第三个参数就是div标签里面的内容，这里我们指向了data里面的text。

使用render函数的结果和我们之前使用template解析出来的结果是一样的。**render函数是发生在beforeMount和mounted之间的**，这也从侧面说明了，在beforeMount的时候，$el还只是我们在HTML里面写的节点，然后到mounted的时候，它就把渲染出来的内容挂载到了DOM节点上。这中间的过程其实是执行了render function的内容。

在使用.vue文件开发的过程当中，我们在里面写了template模板，在经过了vue-loader的处理之后，就变成了render function，最终放到了vue-loader解析过的文件里面。这样做有什么好处呢？原因是由于在解析template变成render function的过程，是一个非常耗时的过程，vue-loader帮我们处理了这些内容之后，当我们在页面上执行vue代码的时候，效率会变得更高。

beforeMount在有了render function的时候才会执行，当执行完render function之后，就会调用mounted这个钩子，在mounted挂载完毕之后，这个实例就算是走完流程了。

后续的钩子函数执行的过程都是需要外部的触发才会执行。比如说有数据的变化，会调用beforeUpdate，然后经过Virtual DOM，最后updated更新完毕。当组件被销毁的时候，它会调用beforeDestory，以及destoryed。

这就是vue实例从新建到销毁的一个完整流程，以及在这个过程中它会触发哪些生命周期的钩子函数。那说到这儿，可能很多童鞋会问，钩子函数是什么意思？

钩子函数，其实和回调是一个概念，当系统执行到某处时，检查是否有hook，有则回调。说的更直白一点，每个组件都有属性，方法和事件。所有的生命周期都归于事件，在某个时刻自动执行。 

参考：[如何解释vue的生命周期才能令面试官满意？](https://segmentfault.com/a/1190000014376915)

## 参考资料汇总

 [vue生命周期解析](https://juejin.im/post/5b02de32f265da0b9265e655)  

[VUE生命周期理解](https://zhuanlan.zhihu.com/p/26291416)

[如何解释vue的生命周期才能令面试官满意？](https://segmentfault.com/a/1190000014376915)

  [vue生命周期、钩子理解](https://blog.csdn.net/Coder_Chang/article/details/78758537)

[Vue.js2.0生命周期的研究与理解](https://blog.csdn.net/qq_25186543/article/details/79470184)

 

 

 