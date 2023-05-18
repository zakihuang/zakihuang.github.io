---
layout: post
title: "前后端开发规约"
date: 2023-05-18
categories: [api]
tags: [api, default value]
---

前期的麻烦是减少后期更多的沟通成本，减少前后端返工情况。


### 对齐需求
开发前，前后端需要对齐一遍需求及对应接口列表，交互方式

### 文档
接口文档一定要落在线的，禁忌：

- **禁口头通知、描述等，容易遗忘**

- **禁聊天文本发送，查找记录麻烦**

- **禁私自修改文档，一定要通知对应人员**

- **禁没有注释，要明确接口字段含义，防止歧义**

### 接口规范
关于接口输入输出一些规范讨论，后端可以看看阿里巴巴 Java开发手册.pdf

### URL参数长度
通过GET请求传参数长度限制，不同浏览器对长度限制不一样，最小超过 2048 字节

### 数据类型要一致
不要前端枚举获取的Number类型，提交之后，获取输出的变成String类型，不同表的数据类型请尽量保持一致，不然影响前端判断

### 字段名尽量保持一致

输入输出字段名尽量保持一致，比如保存接口的参数字段名和查询详情的参数字段名完全不一样

- 不利于前端理解，假如多个后端开发对接一个前端，一个业务字段每个接口key都不一样，前端理解成本高，需要转换理解
- 不利于前端开发，查询和保存都要相互转换对应字段名
> 因为字段名被占用、关键字等原因就是要不一样，那就是尽量减少这种情况


### 字段名风格
可以统一成lowerCamelCase风格，方便理解和使用，比如error-message这字段名无法被前端当变量名使用的

### 唯一索引
确保列表数组有唯一索引，比如 ID, Code 等，前端遍历需要确保唯一性

### 时间格式
前后端的时间格式统一为 timestamp，统一为 GMT，减少前后端格式沟通


### 空值字段


前端输入 接口参数 一般对象属性值是 undefined 和 null值，前端会过滤不传，如果需要传字段请让前端传空字符 ''

字段属性是undefined在 JSON 当中会被过滤，在数组中会被替换成null，所以后端post请求不会接收到值是undefined字段，get请求统一前端过滤处理





```ts
JSON.stringify({ key: undefined })
// '{}'

JSON.stringify({ x: [10, undefined] });
// '{"x":[10,null]}'
```

后端输出 接口数据 确保接口返回的字段不会缺失，一般值为空：基础类型可以设置成 '' 或 null，引用类型可以设置成 [] 或 {} ，如果字段缺失：

- 无法确定是缺少字段还是没有数据
- 集合或数组时需要做繁琐的空值判断

```ts
// 后端返回 null 或 不返回
const result = {
  "status": 200,
  "data": null
}

// 前端
if(status === 200 && data && data.list){ /* todo */ }
```

> 前端觉得怎么能把代码稳定性交给和后端约定的规范？这其实没有限制前端去加空值判断，好的规范是一种双重保障，减少双方失误导致的问题

### 数据列表或集合

数据列表相关的接口返回，如果为空，则返回空数组[]或空集合{}，**注意是任何深度的数组和集合的数据都要**

### 分页数据

- 用户输入参数的小于 1，则前端返回第一页参数给后端
- 后端发现用户输入的参数大于总页数，直接返回最后一页

```ts
const response = {
  "status": 200,
  "data": {
    "list": [],
    "total": 0,
    "pageSize": 10
  }
}
```

### 请求方式

默认提交方式: POST / GET 方法 application/x-www-form-urlencoded，<form>

```js
// POST
Content-Type: application/x-www-form-urlencoded

foo=bar&baz=The+first+line.%0D%0AThe+second+line.%0D%0A

// GET 放到URL后
?foo=bar&baz=The%20first%20line.%0AThe%20second%20line.

```
文件上传常用提交方式: POST 方法 multipart/form-data

```js
// POST
Content-Type: multipart/form-data; boundary=---------------------------314911788813839

-----------------------------314911788813839
Content-Disposition: form-data; name="foo"

bar
-----------------------------314911788813839
Content-Disposition: form-data; name="baz"

The first line.
The second line.

-----------------------------314911788813839--

```

常用的请求JSON数据方式: POST / GET 方法 application/json

```js
// POST
Content-Type: application/json

{"foo": "bar"}

// GET
?foo=bar
```