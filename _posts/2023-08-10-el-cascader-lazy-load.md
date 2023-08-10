---
layout: post
title: "el-cascader 懒加载 & 回显示"
date: 2023-05-18
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