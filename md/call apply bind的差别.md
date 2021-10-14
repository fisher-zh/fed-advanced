# `call` `apply` `bind` 的差别与使用
> `call()` 方法使用一个指定的 this 值和单独给出的一个或多个参数来调用一个函数。`call()`作用与 `apply()` 方法类似，只有一个区别，就是 `call()` 方法接受的是一个参数列表，而 `apply()` 方法接受的是一个包含多个参数的数组。

> `bind()` 方法创建一个新的函数，在 `bind()` 被调用时，这个新函数的 `this` 被指定为 `bind()` 的第一个参数，而其余参数将作为新函数的参数，供调用时使用。

## `call()` `apply()`

### `call` 常规方法
```js
function print (age) {
  console.log(this.name, age || 0);
}
var obj = {
  name: '张三'
}
print.call(obj); // 张三 0
print.call(obj, 12); // 张三 12
```
### `call` 方法实现继承
```js
function Person(name, age) {
  this.name = name;
  this.age = age;
}

function Boy(name, age) {
  Person.call(this, name, age);
  this.sex = '男';
}

function Girl(name, age) {
  Person.call(this, name, age);
  this.sex = '女';
}

var a = new Boy('a', 5);
var b = new Girl('b', 40);
```
### `apply` 方法操作数组
```js
/**
 * 查找数组中的最大值
 * @param {array} array 目标数组
 * @return {number} 最大值
 */
function arrayMax (array) {
  // 使用allay将Math.max挂载在null上执行数组，因为apply是传入一个数组参数的的
  return Math.max.apply(null, array);
}

/**
 * 查找数组中的最小值
 * @param {array} array 目标数组
 * @return {number} 最小值
 */
function arrayMin (array) {
  // 使用allay将Math.max挂载在null上执行数组，因为apply是传入一个数组参数的的
  return Math.min.apply(null, array);
}

/**
 * 合并数组
 * @param {array} array1 目标数组
 * @param {array} array2 需要合并的数组
 * @return {array} array1
 */
function arrayConcat (array1, array2) {
  // 使用allay将Math.max挂载在null上执行数组，因为apply是传入一个数组参数的的
  Array.prototype.push.apply(array1, array2);
  return array1
}
```
## `bind()`
### `bind` 常规方法绑定
```js
function print (age) {
  console.log(this.name, age || 0);
}
this.name = '李四';
var obj = {
  name: '张三'
}
print(); // 默认this指向window
print.bind(obj)(12);
```
### `bind` 函数设置默认参数
```js
function add (arg1, arg2) {
  return arg1 + arg2
}
var add2 = add.bind(null, 1);
add2(2);

// 这里实现的功能类似函数柯里化的一个功能
function curryAdd (arg1) {
  return function (arg2) {
    return arg1 + arg2
  }
}
var add1 = curryAdd(1);
add1(2);
```

## 总结
`call()` `apply()` 方法做的事情基本一致，只是传参有些差异，我们可以根据不同的场景来选择使用哪个方法，比如上述的代码中对数组排序等几个方法就使用了 `apply` 参数为数组的特点。 `bind()` 方法与上述两个方法的不同点在于其只是创建一个函数，而不是立即执行函数，同时，`bind()` 生成的函数还可以继续接收参数。
