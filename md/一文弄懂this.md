# 一文弄懂this

在JavaScript中，this的指向问题一直是很多人迷惑的地方，而各种前端面试中关于this的问题也是五花八门，令人头大。

>与其他语言相比，**函数的 `this` 关键字**在 JavaScript 中的表现略有不同，此外，在[严格模式](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Strict_mode)和非严格模式之间也会有一些差别。
>
>在绝大多数情况下，函数的调用方式决定了 `this` 的值（运行时绑定）。`this` 不能在执行期间被赋值，并且在每次函数被调用时 `this` 的值也可能会不同。ES5 引入了 [bind](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind) 方法来设置函数的 `this` 值，而不用考虑函数如何被调用的。ES2015 引入了[箭头函数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Arrow_functions)，箭头函数不提供自身的 this 绑定（`this` 的值将保持为闭合词法上下文的值）。

## 上下文环境

在大多数情况下，this值取决于其执行环境。在全局环境中执行时，this指向全局对象；在函数内部执行时，则取决于函数被调用的方式。

```js
console.log(this); // 此时输出的为window对象

function f1 () {
    console.log(this);
}
f1(); // 此时输出的为window对象

function f2(){
  "use strict"; // 这里是严格模式
  console.log(this);
}
f2(); // 此时输出的为undefined
window.f2(); // 此时输出的为window对象
```

通过上面的例子可以发现，在全局环境和普通函数中，this一般都指向全局对象window，但是在严格模式下有一些例外，这是因为在严格模式下，如果进入执行环境时没有设置 `this` 的值，`this` 会保持为 `undefined`。 `f2` 是被直接调用的，而不是作为对象的属性或方法调用的（如 `window.f2()`），所以其会输出为`undefine`。

## call、apply、bind

> `call()` 方法使用一个指定的 `this` 值和单独给出的一个或多个参数来调用一个函数。
>
> `apply()` 方法调用一个具有给定`this`值的函数，以及以一个数组（或[类数组对象](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Indexed_collections#working_with_array-like_objects)）的形式提供的参数。
>
> call()方法的作用和 apply() 方法类似，区别就是`call()`方法接受的是**参数列表**，而`apply()`方法接受的是**一个参数数组**。

`call`和`apply`方法类似，我们直接放在一起解析。在日常的开发过程中，简单情况下使用 `call` 或者`apply` 方法来指定某个函数的上下文this。当然，`call`和`apply`的使用方法不止这些，我们还可以使用他们进行构造函数的调用、数组查询等，在此不展开讲解，这里主要讲其关于指定上下文this的功能。

```js
"use strict";
var a = 'window a';
function f1 () {
    console.log(this.a);
}
f1(); // window a
var o = {
    a: 'o a'
}
f1.call(); // window a
f1.apply(); // window a
// 在非严格模式下，call和apply不传参则默认绑定到全局对象，严格模式下this指向undefined，此时打印this.a则会发生报错

f1.call(o); // o a
f1.apply(o); // o a
// 由于这里函数没有传参，所以未体现出call和apply的差别
```



> `bind()` 方法创建一个新的函数，在 `bind()` 被调用时，这个新函数的 `this` 被指定为 `bind()` 的第一个参数，而其余参数将作为新函数的参数，供调用时使用。

```js
var a = 'window';
function fn () {
    console.log(this.a);
}
var o = {
    a: 'o a'
}
var f1 = fn.bind(o);
fn(); // window
f1(); // o a
```

## 构造函数

当一个函数用作构造函数时，它的`this`被绑定到正在构造的新对象。

```js
function P (a) {
    this.a = 1;
}
var x = new P();
console.log(x.a); // 1
```

