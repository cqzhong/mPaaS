###  Promise

#### 0、使用

```javascript
function f1(resolve, reject) {
  // 异步代码...
}
var p1 = new Promise(f1);
p1.then(f2);
```
`f1`的异步操作执行完成，就会执行`f2`。

####  1、Promise既是对象又是构造函数

所有异步任务都返回一个Promise实例

```javascript
// 传统写法
step1(function (value1) {
  step2(value1, function(value2) {
    step3(value2, function(value3) {
      step4(value3, function(value4) {
        // ...
      });
    });
  });
});

// Promise 的写法
(new Promise(step1))
  .then(step2)
  .then(step3)
  .then(step4);
```

#### 2、Promise对象的状态

> - 异步操作未完成（pending）
> - 异步操作成功（fulfilled）（已定型）
> - 异步操作失败（rejected）（已定型）

Promise 的最终结果只有两种。

> - 异步操作成功，Promise 实例传回一个值（value），状态变为`fulfilled`。
> - 异步操作失败，Promise 实例抛出一个错误（error），状态变为`rejected`。

#### 3、Promise构造函数

构造函数函数原型

```
var promise = new Promise(function (resolve, reject) {
  // ...

  if (/* 异步操作成功 */){
    resolve(value);
  } else { /* 异步操作失败 */
    reject(new Error());
  }
});
```

#### 4、Promise.prototype.then()

```javascript
var p1 = new Promise(function (resolve, reject) {
	console.log("fun 1");
	resolve('成功');
  });
  var p2 = new Promise(function (resolve, reject) {
	  console.log("fun 2");
	reject('失败');
  });
  p1.then(console.log, console.error);
  p2.then(console.log, console.error);

//result
fun 1
fun 2
成功
失败

function f(){
	return new Promise(function(sunccess,fail){
		console.log("in promise");
		sunccess('arg')
	})
}
f().then(function(arg){
	console.log('over');
})
console.log("out promise");
//result
in promise
out promise
over
```
Promise构造函数接收一个函数作为参数，该函数的两个参数是resolve和reject，他们是两个函数，他们的参数会传递回调函数 （eg. 上述代码中的arg）
##### Promise对象报错具有传递性

最后一个`then`方法，回调函数是`console.log`和`console.error`，用法上有一点重要的区别。`console.log`只显示`step3`的返回值，而`console.error`可以显示`p1`、`step1`、`step2`、`step3`之中任意一个发生的错误。举例来说，如果`step1`的状态变为`rejected`，那么`step2`和`step3`都不会执行了（因为它们是`resolved`的回调函数）。Promise 开始寻找，接下来第一个为`rejected`的回调函数，在上面代码中是`console.error`。这就是说，Promise 对象的报错具有**传递性**。

#### 5、微任务

Promise 的回调函数属于异步任务，会在同步任务之后执行。

```JavaScript
new Promise(function (resolve, reject) {
  resolve(1);
}).then(console.log);

console.log(2);
// 2
// 1
```

但是，Promise 的回调函数不是正常的异步任务，而是微任务（microtask）。它们的区别在于，正常任务追加到下一轮事件循环，微任务追加到本轮事件循环。这意味着，微任务的执行时间一定早于正常任务。

```javascript
setTimeout(function() {
  console.log(1);
}, 0);//正常的异步任务

new Promise(function (resolve, reject) {
  resolve(2);
}).then(console.log);//Promise的回调函数

console.log(3);//同步任务
// 3
// 2
// 1
```