---
layout: post
title: "util.js promisify and callbackfy"
date: 2021-10-13
categories: [node]
tags: [promisify, callbackfy]
---

util.js 里的 promisify 函数可以把常规的，最后一个参数为回调函数的函数转化为 promise

```ts
const util = require('util');

const fn = (param, callback) => {
  callback('err', param)
};

var fnTest = util.promisify(fn)

(async function () {

  const x = await ffnTest(1)
  
  console.log(x);

})();
```

另一个功能是可以使用 util.promisify.custom 符号重写 util.promisify 返回值。

```ts
const fn = (param, callback) => {
  callback('err', param)
};

fn[util.promisify.custom] = (e, r) => {
  return Promise.resolve('这是自定义返回');
};

(async function () {

  const x = await f(1)
  console.log(x);

})();

```

另外一个叫 callbackfy 的函数正好相反，可以把 promise 轩化为回调函数

```ts
async function ff() {
  return 999
}

const fff = util.callbackify(ff);

fff((err, ret) => {
  console.log(err, ret)
})
```
