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

