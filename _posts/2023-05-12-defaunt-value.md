---
layout: post
title: "关于vue项目中前后端接口默认值问题"
date: 2023-05-09
categories: [vue]
tags: [vue, api, default value]
---

javascript 中有 string, number, boolean, undefined, null 这 5 种基本类型 和 引用类型 object

其中 null 表示一个值被定义了，定义为“空值”；而 undefined 表示根本不存在定义（缺少值）。

所以设置一个值为 null 是合理的，如 obj.value = null;

但设置一个值是 undefined 是不合理的，undefined 典型用法是，<a href="http://www.ruanyifeng.com/blog/2014/03/undefined-vs-null.html">请参阅</a>

由此引发 "前后端接口定义的默认值问题"

```ts
    // java 对应的数据类型的默认值（空值）对应都是 null
    string  => null
    number  => null
    boolean => null
    date    => null
    object  => null
    array   => null
```

如果后端直接抛给前端这些个 null，经过表单编辑后的数据如下

```ts
    server  =>  string  => null => el-input  => ''     => server
    server  =>  number  => null => el-input  => ''     => server
    server  =>  boolean => null => el-switch => false  => server
    server  =>  date    => null => el-date   => 时间戳  => server
    server  =>  object  => null => {}        => {}     => server
    server  =>  array   => null => []        => []     => server
```

再加载页面，后端把数据传给前端

```ts
  server  =>  string  => ''     => el-input  => ''     =>  server
  server  =>  number  => ''     => el-input  => ''     =>  server
  server  =>  boolean => false  => el-switch => false  =>  server
  server  =>  date    => 时间戳  => el-date   => 时间戳  =>  server
  server  =>  object  => null   => {}        => {}     =>  server
  server  =>  array   => null   => []        => []     =>  server
```

这里有争议点在于，字符串 string, 数字 number, 和布尔 boolean 类型

用户一旦经过下列交互，null(空值)即被改变

字符 null => 'abc' => ''    [交互导致]

数字 null => '123' => ''    [交互导致]

布尔 null => false => false [组件行为]

所以，关于 这 三个类型，一旦后端 api 赋予 null 表达业务逻辑，如：表示空值
前端同学在保存数据时就要小心处理，如 


字符 null => 'abc' => ''     [交互导致]  => 转换回 null

数字 null => '123' => ''     [交互导致]  => 转换回 null

布尔 null => false => false  [组件行为]  => 转换回 null

其中 布尔 的处理比较特殊，即使用户没交互，组件初始化时就把它改变了，所以需要根据情况特殊处理

另外，关于 objec 和 array 建议定义为空对象{} 和 空数组[], 避免不必要的错误
```ts
data() {
  return {
    obj: {
        string  : null
        number  : null
        boolean : null
        date    : null
        object  : {}
        array   : []
    }
  }
}
```
