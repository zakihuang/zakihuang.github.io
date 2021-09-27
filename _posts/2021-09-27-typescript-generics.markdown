---
layout: post
title:  "generics"
date:   2021-09-27
last_modified_at: 2021-09-27
categories: [typescript]
tags: [generics]
---
#### 官方定义：
> 泛型（Generics）是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性

官方定义略显生硬，对于没接触过泛型的同学很难一下子就 get 到要点，以下例子通过是否使用泛型，试图解释什么是泛型

#### 不使用泛型
```ts

function createArray(length: number, value: string): Array<string> {
  let result: string[] = [];
  for (let i = 0; i < length; i++) {
      result[i] = value;
  }
  return result;
}

console.log( createArray(3, 'string') ); // ['string', 'string', 'string']

```

#### 使用泛型
```ts

function createArray<T>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}

console.log( createArray<string>(3, 'string') ); // ['string', 'string', 'string']

```

可以看到两段代码的功能是相同的，不同点在于 `不使用泛型` 时，createArray函数 的 参数 value 是指定为 string 类型的，返回值同样被指定为 string[] 数组，试想
如果参数和返回值 只有在使用时才可能确定，这种定义就显得力不从心了，泛型的出现正是为了弥补这种遗憾，换句话说就是定义时不指定具体类型，在使用时才指定。可以看到
函数定义时引入了 `<T>` 符号，放在函数名后面，表示：此函数定义引了了类型 T, 至于 T 是什么类型，当前不确定，调用函数时才能确定，如何确定？调用时在把 `<T>` 尖括号里的 T 换
成具体类型即可, 类似于函数参数，这里是类型参数。

#### 泛型约束
在函数内部使用泛型变量的时候，由于事先不知道它是哪种类型，所以不能随意的操作它的属性或方法：
```ts

function loggingIdentity<T>(arg: T): T {
    console.log(arg.length);
    return arg;
}

// index.ts(2,19): error TS2339: Property 'length' does not exist on type 'T'.

```

上例中，泛型 `T` 不一定包含属性 `length`，所以编译的时候报错了。

这时，我们可以对泛型进行约束，只允许这个函数传入那些包含 `length` 属性的变量。这就是泛型约束：

```ts
 
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);
    return arg;
}

```

上例中，我们使用了 `extends` 约束了泛型 `T` 必须符合接口 `Lengthwise` 的形状，也就是必须包含 `length` 属性。

此时如果调用 `loggingIdentity` 的时候，传入的 `arg` 不包含 `length`，那么在编译阶段就会报错了：

```ts
 
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);
    return arg;
}

loggingIdentity(7);

// index.ts(10,17): error TS2345: Argument of type '7' is not assignable to parameter of type 'Lengthwise'.

```

多个类型参数之间也可以互相约束：
```ts
 
function copyFields<T extends U, U>(target: T, source: U): T {
    for (let id in source) {
        target[id] = (<T>source)[id];
    }
    return target;
}

let x = { a: 1, b: 2, c: 3, d: 4 };

copyFields(x, { b: 10, d: 20 });

```

上例中，我们使用了两个类型参数，其中要求 T 继承 U，这样就保证了 U 上不会出现 T 中不存在的字段。
#### 泛型接口

可以使用接口的方式来定义一个函数需要符合的形状：

```ts

interface SearchFunc {
  (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
    return source.search(subString) !== -1;
}

```

也可以使用含有泛型的接口来定义函数的形状：

```ts

interface CreateArrayFunc {
    <T>(length: number, value: T): Array<T>;
}

let createArray: CreateArrayFunc;
createArray = function<T>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}

createArray(3, 'x'); // ['x', 'x', 'x']

```

进一步，我们可以把泛型参数提前到接口名上：

```ts

interface CreateArrayFunc<T> {
    (length: number, value: T): Array<T>;
}

let createArray: CreateArrayFunc<string>;
createArray = function<T>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}

createArray(3, 'x'); // ['x', 'x', 'x']

```

注意，此时在使用泛型接口的时候，需要定义泛型的类型。
#### 泛型类
与泛型接口类似，泛型也可以用于类的类型定义中：

```ts

class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();

```
#### 泛型参数的默认类型
在 TypeScript 2.3 以后，我们可以为泛型中的类型参数指定默认类型。当使用泛型时没有在代码中直接指定类型参数，从实际值参数中也无法推测出时，这个默认类型就会起作用。

```ts

function createArray<T = string>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}

```