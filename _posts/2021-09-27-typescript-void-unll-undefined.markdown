---
layout: post
title:  "void unll undefined"
date:   2021-09-27
#last_modified_at: 2021-09-27
categories: [typescript]
tags: [void, unll, undefined]
---
#### 空值：
> JavaScript 没有空值（Void）的概念，在 TypeScript 中，可以用 void 表示没有任何返回值的函数：

```ts
function test(): void {
    alert('My name is Tom');
}
```

声明一个 void 类型的变量没有什么用，因为你只能将它赋值为 undefined 和 null（只在 --strictNullChecks 未指定时）：

```ts
let unusable: void = undefined;
```

#### Null 和 Undefined

在 TypeScript 中，可以使用 null 和 undefined 来定义这两个原始数据类型：

```ts
let u: undefined = undefined;
let n: null = null;
```

与 void 的区别是，undefined 和 null 是所有类型的子类型。也就是说 undefined 类型的变量，可以赋值给 number 类型的变量：

```ts
// 这样不会报错
let num: number = undefined;
let str: string = null;

// 这样也不会报错
let u: undefined;
let n: null;
let num: number = u;
let str: string = n;
```

而 void 类型的变量不能赋值给 number 类型的变量：
```ts
let u: void;
let num: number = u;

// Type 'void' is not assignable to type 'number'.
```

> 注意：tsconfig.json 里的 "strictNullChecks": false 时，以上代码才能通过

另外可参考 阮老师的 关于 js 中的 null 和 undefined 的解释 [undefined与null的区别](http://www.ruanyifeng.com/blog/2014/03/undefined-vs-null.html){:target="_blank"}
