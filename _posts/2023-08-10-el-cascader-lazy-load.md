---
layout: post
title: "el-cascader 懒加载 & 回显示"
date: 2023-08-14
categories: [api]
tags: [api, default value]
---

el-cascader 的懒加载很容易，配置下 lazyload 回调函数就成了
但懒加载有点儿猫腻，瞅了一眼源码，发现人家已经实现了，而且很好用
保存以下文件到 index.vue，跑起来。。。
```
<template>
  <div class="dashboard-editor-container">
    <el-cascader
      :props="cascaderProps"
      clearable
      style="width: 100%"
      v-model="activeUserCode"
    >
    </el-cascader>
  </div>
</template>
<script>
let serverData = {
  root: [
    {
      value: "a",
      label: "a节点",
      leaf: false,
    },
    {
      value: "c",
      label: "c节点",
      leaf: false,
    },
  ],
  a: [
    {
      value: "b",
      label: "a的子节点b",
      leaf: false,
    },
    {
      value: "x",
      label: "a的子节点x",
      leaf: true,
    },
  ],
  b: [
    {
      value: "d",
      label: "d节点",
      leaf: true,
    },
  ],
};

export default {
  name: "lazySelector",
  data() {
    return {
      activeUserCode: ["a", "b", "d"],
      cascaderProps: {
        multiple: false,
        value: "value",
        label: "label",
        children: "children",
        lazy: true,
        lazyLoad(node, resolve) {
          console.log(node.value);
          if (node.root) {
            setTimeout(resolve, 0, serverData.root);
          } else {
            setTimeout(resolve, 0, serverData[node.value]);
          }
        },
      },
    };
  },
};
</script>
```

一切完美了，but 有瑕疵，我们的 value 一般情况是通过接口异步获取的, 所以难免延迟赋值
这样就没法驱动 el-cascader 动态反显（组件非常偷懒的去只加载第一层数据），所以的，我
们的反显出 bug 了，失效了，解决办法有两个

### 第一种

```ts
// 添加一个 ready 属性给  el-cascader 的 v-if='ready'
// 让我们的 value 赋值之后再让 el-cascader 显示
```
### 第二种
```ts
// 添加一个 options 给 el-cascader
//  el-cascader options="options"
// 初始值设置为

options = [0];

// 异步加载 value 赋值之后调整下 options 的值 

options = [];
// 别问我为啥可以，问就是源码里这么写的骚操作，正好被我们利用
```

推荐使用第二种，需要我们手动的二次封装下这个组件，要不然这谁看得懂，受得了