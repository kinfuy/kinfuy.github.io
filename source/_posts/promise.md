---
title: ES6异步解决方案Promise
theme: default
tag: javascript
time: 2020.6.14
---

## ES6 异步解决方案 Promise

## Parmise

Promise 是异步编程的一种解决方案，比传统的解决方案——回调函数和事件——更合理和更强大。

**Promise 对象有以下两个特点。**

（1）对象的状态不受外界影响。Promise 对象代表一个异步操作，有三种状态：Pending（进行中）、Resolved（已完成，又称 Fulfilled）和 Rejected（已失败）。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。这也是 Promise 这个名字的由来，它的英语意思就是“承诺”，表示其他手段无法改变。

（2）一旦状态改变，就不会再变，任何时候都可以得到这个结果。Promise 对象的状态改变，只有两种可能：从 Pending 变为 Resolved 和从 Pending 变为 Rejected。只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果。就算改变已经发生了，你再对 Promise 对象添加回调函数，也会立即得到这个结果。这与事件（Event）完全不同，事件的特点是，如果你错过了它，再去监听，是得不到结果的。

有了 Promise 对象，就可以将异步操作以同步操作的流程表达出来，避免了层层嵌套的回调函数
（**回调地狱**）。此外，Promise 对象提供统一的接口，使得控制异步操作更加容易。

Promise 也有一些缺点。首先，无法取消 Promise，一旦新建它就会立即执行，无法中途取消。其次，如果不设置回调函数，Promise 内部抛出的错误，不会反应到外部。第三，当处于 Pending 状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

```js
var promise = new Promise(function(resolve, reject) {
  // ... some code

  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});
```

ES6 规定，Promise 对象是一个构造函数，用来生成 Promise 实例。Promise 构造函数接受一个函数作为参数，该函数的两个参数分别是 resolve 和 reject。它们是两个函数，由 JavaScript 引擎提供，不用自己部署。

**resolve 函数**的作用是，将 Promise 对象的状态从“**未完成**”变为“**成功**”（即从 Pending 变为 Resolved），在异步操作成功时调用，并将异步操作的结果，作为参数传递出去；**reject 函数**的作用是，将 Promise 对象的状态从“**未完成**”变为“**失败**”（即从 Pending 变为 Rejected），在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。

Promise 实例生成以后，可以用 then 方法分别指定 Resolved 状态和 Reject 状态的回调函数。

```js
promise.then(
  function (value) {
    // success
  },
  function (error) {
    // failure
  }
);
```

then 方法可以接受两个回调函数作为参数。第一个回调函数是 Promise 对象的状态变为 Resolved 时调用，第二个回调函数是 Promise 对象的状态变为 Reject 时调用。其中，第二个函数是可选的，不一定要提供。这两个函数都接受 Promise 对象传出的值作为参数。

```js
function timeout(ms) {
  return new Promise((resolve, reject) => {
    setTimeout(resolve, ms, 'done');
  });
}

timeout(100).then((value) => {
  console.log(value);
});
```

Promise 新建后就会立即执行。

```js
let promise = new Promise(function (resolve, reject) {
  console.log('Promise');
  resolve();
});

promise.then(function () {
  console.log('Resolved.');
});

console.log('Hi!');

// Promise
// Hi!
// Resolved
```

### Promise 简单封装 Ajax

```js
let Ajax = function (url) {
  let promise = new Promise(function (resolve, reject) {
    let client = new XMLHttpRequest();
    client.open('GET', url);
    client.onreadystatechange = handleFunction;
    client.responseType = 'json';
    client.setRequestHeader('Accept', 'application/json');
    client.send();

    function handleFunction() {
      if (this.readyState !== 4) {
        return;
      }
      if (this.status === 200) {
        resolve(this.response);
      } else {
        reject(new Error(this.statusText));
      }
    }
  });

  return promise;
};

Ajax(url).then(
  function (res) {
    console.log('Contents: ' + res);
  },
  function (error) {
    console.error('出错了', error);
  }
);
```

如果调用 resolve 函数和 reject 函数时带有参数，那么它们的参数会被传递给回调函数。reject 函数的参数通常是 Error 对象的实例，表示抛出的错误；resolve 函数的参数除了正常的值以外，还可能是另一个 Promise 实例，表示异步操作的结果有可能是一个值，也有可能是另一个异步操作，比如像下面这样。

```js
var p1 = new Promise(function (resolve, reject) {
  // ...
});

var p2 = new Promise(function (resolve, reject) {
  // ...
  resolve(p1);
});
```

上面代码中，p1 和 p2 都是 Promise 的实例，但是 p2 的 resolve 方法将 p1 作为参数，即一个异步操作的结果是返回另一个异步操作。

**注意**:这时 p1 的状态就会传递给 p2，也就是说，p1 的状态决定了 p2 的状态。如果 p1 的状态是 Pending，那么 p2 的回调函数就会等待 p1 的状态改变；如果 p1 的状态已经是 Resolved 或者 Rejected，那么 p2 的回调函数将会立刻执行。

```js
var p1 = new Promise(function (resolve, reject) {
  setTimeout(() => reject(new Error('fail')), 3000);
});

var p2 = new Promise(function (resolve, reject) {
  setTimeout(() => resolve(p1), 1000);
});

p2.then((result) => console.log(result)).catch((error) => console.log(error));
// Error: fail
```

上面代码中，p1 是一个 Promise，3 秒之后变为 rejected。p2 的状态在 1 秒之后改变，resolve 方法返回的是 p1。此时，由于 p2 返回的是另一个 Promise，所以后面的 then 语句都变成针对后者（p1）。又过了 2 秒，p1 变为 rejected，导致触发 catch 方法指定的回调函数。

**Promise.prototype.then()**

当 promise 状态改变为成功的时候调用。

then 方法返回的是一个新的 Promise 实例（注意，不是原来那个 Promise 实例）。因此可以采用链式写法，即 then 方法后面再调用另一个 then 方法。

**Promise.prototype.catch()**

当 promise 状态改变为失败的时候调用，

**注意**：连续调用 then 的情况下，它们之中任何一个抛出的错误，都会被最后一个 catch 捕获。
一般来说，不要在 then 方法里面定义 Reject 状态的回调函数（即 then 的第二个参数），总是使用 catch 方法。

**Promise.all()** **有一个失败就失败，全部成功才成功**

Promise.all 参数，如果不是 Promise 实例，就会先调用下面讲到的 Promise.resolve 方法，将参数转为 Promise 实例，再进一步处理。

Promise.all 方法用于将多个 Promise 实例，包装成一个新的 Promise 实例。

**Promise.resolve()**

有时需要将现有对象转为 Promise 对象，Promise.resolve 方法就起到这个作用。

```
let jsPromise = Promise.resolve($.ajax('/whatever.json'));
```

**注意**：

（1）如果参数是 Promise 实例，那么 Promise.resolve 将不做任何修改、原封不动地返回这个实例。

（2）参数是一个 thenable 对象，thenable 对象指的是具有 then 方法的对象。Promise.resolve 方法会将这个对象转为 Promise 对象，然后就立即执行 thenable 对象的 then 方法

（3）参数不是具有 then 方法的对象，或根本就不是对象，如果参数是一个原始值，或者是一个不具有 then 方法的对象，则 Promise.resolve 方法返回一个新的 Promise 对象，状态为 Resolved。

（4）不带有任何参数，Promise.resolve 方法允许调用时不带参数，直接返回一个 Resolved 状态的 Promise 对象。所以，如果希望得到一个 Promise 对象，比较方便的方法就是直接调用 Promise.resolve 方法。

**Promise.reject()**

Promise.reject(reason)方法也会返回一个新的 Promise 实例，该实例的状态为 rejected。它的参数用法与 Promise.resolve 方法完全一致。

```
let p = Promise.all([p1, p2, p3]);
```

p 的状态由 p1、p2、p3 决定，分成两种情况。

（1）只有 p1、p2、p3 的状态都变成成功，p 的状态才会变成成功，此时 p1、p2、p3 的返回值组成一个数组，传递给 p 的回调函数。

（2）只要 p1、p2、p3 之中有一个被失败，p 的状态就变成失败，此时第一个被 reject 的实例的返回值，会传递给 p 的回调函数。

```
var promises = [url1, url2, url3, url4, url5, url6].map(function (url) {
  return Ajax(url);
});

Promise.all(promises).then(function (posts) {
  // ...
}).catch(function(reason){
  // ...
});
```

上面代码中，promises 是包含 6 个 Promise 实例的数组，只有这 6 个实例的状态都变成成功，或者其中有一个变为失败，才会调用 Promise.all 方法后面的回调函数。

**Promise.race()** **有一个成功就成功有一个失败就失败**

Promise.race 方法同样是将多个 Promise 实例，包装成一个新的 Promise 实例。

Promise.race()参数，如果不是 Promise 实例，就会先调用下面讲到的 Promise.resolve 方法，将参数转为 Promise 实例，再进一步处理。

```
let p = Promise.race([p1, p2, p3]);
```

上面代码中，只要 p1、p2、p3 之中有一个实例率先改变状态，p 的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给 p 的回调函数。

**done()**

不管以 then 方法或 catch 方法结尾，要是最后一个方法抛出错误，都有可能无法捕捉到（因为 Promise 内部的错误不会冒泡到全局）。因此，我们可以提供一个 done 方法，总是处于回调链的尾端，保证抛出任何可能出现的错误。

**finally()**

finally 方法用于指定不管 Promise 对象最后状态如何，都会执行的操作。它与 done 方法的最大区别，它接受一个普通的回调函数作为参数，该函数不管怎样都必须执行。

**Promise.try()**

Promise.try 就是模拟 try 代码块，就像 promise.catch 模拟的是 catch 代码块。
