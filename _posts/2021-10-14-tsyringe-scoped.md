---
layout: post
title: "tsyringe 里使用实例共享"
date: 2021-10-19
categories: [tsyringe]
tags: [ioc, scoped]
---

tsyringe 里使用 scoped 共享实例

## Api: scoped
```ts
import 'reflect-metadata'
import { container, Lifecycle, scoped, injectable } from 'tsyringe'

@scoped(Lifecycle.ResolutionScoped)
@injectable()
class Bar {
  constructor() { }
  x = 1;
}

@injectable()
class Foo {
  constructor(public myBar: Bar) { }
}

@injectable()
class FooBar {
  constructor(public bar: Bar, public foo: Foo) { }
}

const fooBar = container.resolve(FooBar);
fooBar.bar.x = 2;

console.log(fooBar.bar.x === fooBar.foo.myBar.x)
// 输出: true
```

以上 Bar 类尽管被多次引用，但其实只共享了一个实例