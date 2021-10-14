---
layout: post
title: "es6 decorator"
date: 2021-10-13
categories: [javascript]
tags: [es6, javascript, decorator]
---

装饰器:装饰器是一种特殊类型的声明，它能够被附加到类声明，方法，属性或参数上，可以修改类的行为。

通俗的讲装饰器就是一个方法，可以注入到类、方法、属性参数上来扩展类、属性、方法、参数的功能。

常见的装饰器有：类装饰器、属性装饰器、方法装饰器、参数装饰器

装饰器的写法：普通装饰器（无法传参）、装饰器工厂（可传参）

装饰器是过去几年中js最大的成就之一，已是Es7的标准特性之一

> 针对类的修饰，只有一个参数 target 指类本身
> 针对静态 属性 方法 方法的参数来讲：第一个参数 target 是指 类本身(也既构造函数本身，注意这里讲的是'构造函数'，因为 javascript 没有类机制，所以用的构造函数模拟实现)
> 针对实例 属性 方法 方法的参数来讲：第一个参数 target 是指 类的 prototype

请注意，这是非常重要的区别，以下代码演示这些区别

### 修饰类
```ts
function decoClass(target: any) {
  // target 就是当前类
  target.prototype.apiUrl = '动态扩展的属性';
  target.prototype.run = function () {
    console.log('我是一个run方法');
  };
}

@decoClass
class TestClass {}

const instance: any = new TestClass();

console.log(instance.apiUrl);

instance.run();
```

### 修饰属性 [静态 | 实例]
属性装饰器表达式会在运行时当作函数被调用，传入下列2个参数：

1、对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。

2、成员的名字。
```ts
function logProperty(params: any) {
  return function (target: any, attr: any) {
    console.log(target);
    console.log(attr);
    target[attr] = params;
  };
}

class staticDemo {
  @logProperty('http://loaderman.com')
  // 区别在这里，看这里，看这里
  public url: any | undefined;

  getData() {
    console.log(this.url);
  }
}
const staticDemoTest = new staticDemo();
staticDemoTest.getData();


class instanceDemo {
  @logProperty('http://loaderman.com')
  // 区别在这里，看这里，看这里
  static url: any | undefined;

  getData() {
    console.log(instanceDemo.url);
  }
}
const instanceDemoTest = new instanceDemo();
instanceDemoTest.getData();
```

### 修饰方法 [静态 | 实例]
它会被应用到方法的 属性描述符上，可以用来监视，修改或者替换方法定义。

方法装饰会在运行时传入下列3个参数：

1、对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。

2、成员的名字。

3、成员的属性描述符。
```ts
function decoMethod(params: any) {
  return function (target: any, methodName: string, desc: any) {
    console.log(target);
    console.log(methodName);
    console.log(desc);
    target.url = params;
    target.run = function () {
      console.log('run');
    };
  };
}

// 实例方法修饰
class instanceMehodClass {
  // 区别在这里，看这里看这里
  public url: string | undefined;
  @decoMethod('http://www.loaderman,com')
  getData() {
    console.log(this.url);
  }
}

const http: any = new instanceMehodClass();
http.getData();

// 静态方法修饰
class staticMethodClass {
  static url: string | undefined;
  @decoMethod('http://www.loaderman,com')
  static getData() {
    console.log(staticMethodClass.url);
  }
}

staticMethodClass.getData();
```

### 修饰方法的参数 [静态 | 实例]
参数装饰器表达式会在运行时当作函数被调用，可以使用参数装饰器为类的原型增加一些元素数据 ，传入下列3个参数：

1、对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。

2、方法的名字。

3、参数在函数参数列表中的索引。
```ts
function logParams(params: any) {
  return function (target: any, methodName: any, paramsIndex: any) {
    console.log(params);
    console.log(target);
    console.log(methodName);
    console.log(paramsIndex);
    target.apiUrl = params;
  };

}

class paramsClass {
  public url: any | undefined;
  getData(@logParams('xxxxx') uuid: any) {
    console.log(uuid);
  }
}


const http: any = new paramsClass();
http.getData(123456);
console.log(http.apiUrl);
```



### 装饰器执行顺序
属性》方法》方法参数》类

如果有多个同样的装饰器，它会先执行后面的, 至于工厂装饰器，按洋葱模型执行
```ts
function logClass1(params: string) {
  return function (target: any) {
    console.log('类装饰器1',target, params);
  };
}

function logClass2(params: string) {
  return function (target: any) {
    console.log('类装饰器2',target, params);
  };
}

function logAttribute1(params?: string) {
  return function (target: any, attrName: any) {
    console.log('属性装饰器1',target, attrName, params);
  };
}

function logAttribute2(params?: string) {
  return function (target: any, attrName: any) {
    console.log('属性装饰器2',target, attrName, params);
  };
}

function logMethod1(params?: string) {
  return function (target: any, attrName: any, desc: any) {
    console.log('方法装饰器1', target, attrName, desc, params);
  };
}
function logMethod2(params?: string) {
  return function (target: any, attrName: any, desc: any) {
    console.log('方法装饰器2' ,target, attrName, desc, params);
  };
}

function logParams1(params?: string) {
  return function (target: any, attrName: any, desc: any) {
    console.log('方法参数装饰器1' ,target, attrName, desc, params);
  };
}

function logParams2(params?: string) {
  return function (target: any, attrName: any, desc: any) {
    console.log('方法参数装饰器2' ,target, attrName, desc, params);
  };
}

@logClass1('http://www.loaderman.com/api')
@logClass2('xxxx')
class HttpClient {
  @logAttribute1()
  @logAttribute2()
  public apiUrl: string | undefined;
  @logMethod1()
  @logMethod2()
  getData() {
    return true;
  }
  setData(@logParams1() attr1: any, @logParams2() attr2: any) {
    console.log(attr1, attr2);
  }
}

const http: any = new HttpClient();
http.getData();
```