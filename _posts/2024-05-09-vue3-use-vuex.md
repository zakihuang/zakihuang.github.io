---
layout: post
title: "如何优雅的在 vue3 中使用 vuex"
date: 2024-05-09
categories: [vue3, vuex]
tags: [vue, vuex]
---

### 背景
> vuex 也许是被 vue3.0 抛弃了，开始推 pinia 了，pinia 太过灵活，灵活就很容易失控，对于一个习惯了 vuex 严谨函数式设计思维的同学，
vue3 对 vuex 的支持真是无法忍受，如何优雅的在 vue3 中 使用 vuex 呢？hooks 你值得拥有。


### 1：把 vuex 封装成一个 hooks, 命名为 useVuex.js
```ts
import { computed } from "vue";
import { createNamespacedHelpers, useStore } from "vuex";

const useFunctionHandle = (moduleName, mapper, mapFn) => {
  let mapperFn = mapFn;
  if (typeof moduleName === "string" && moduleName.length > 0) {
    mapperFn = createNamespacedHelpers(moduleName)[mapFn];
  } else {
    mapper = moduleName;
  }

  const store = useStore();
  const tempFns = mapperFn(mapper);
  const storeFns = {};
  Object.keys(tempFns).forEach((keyFn) => {
    storeFns[keyFn] = tempFns[keyFn].bind({ $store: store });
  });
  return storeFns;
};

const useFunctionState = (moduleName, mapper, mapFn) => {
  let mapperFn = mapFn;
  if (typeof moduleName === "string" && moduleName.length > 0) {
    mapperFn = createNamespacedHelpers(moduleName)[mapFn];
  } else {
    mapper = moduleName;
  }

  const store = useStore();
  const tempFns = mapperFn(mapper);
  const storeState = {};

  Object.keys(tempFns).forEach((keyFn) => {
    storeState[keyFn] = computed(() => tempFns[keyFn].call({ $store: store }));
  });
  return storeState;
};

export const useState = (moduleName, mapper) => {
  return useFunctionState(moduleName, mapper, "mapState");
};

export const useGetters = (moduleName, mapper) => {
  return useFunctionState(moduleName, mapper, "mapGetters");
};

// 使用 Composition API 创建类似于 mapActions 的函数
export const useActions = (moduleName, mapper) => {
  return useFunctionHandle(moduleName, mapper, "mapActions");
};

// 使用 Composition API 创建类似于 mapMutations 的函数
export const useMutations = (moduleName, mapper) => {
  return useFunctionHandle(moduleName, mapper, "mapMutations");
};
```

### 1：引用操作 vuex, 所有使用方式无限趋近于之前的 vuex 版本，真香，有没有？
```ts
import {
  useState,
  useGetters,
  useActions,
  useMutations,
} from "useVuex.js";

// 直接使用 State
const storeState = useState("demo", ["demoList"]);
// 换名使用 State
const { listStateNewName } = useState("demo", { listStateNewName: "demoList" });

// 直接使用 Getters
const { getList } = useGetters("demo", ["getList"]);

// 换名使用 Getters
const { listNewName } = useGetters("demo", { listNewName: "getList" });

// 直接使用 Actions
const { addAction } = useActions("demo", ["addAction"]);
addAction({ a: 9 });

// 重命令 Actions
const { addAc } = useActions("demo", { addAc: "addAction" });
addAc({ a: 99 });
addAc({ a: 922 });

// 直接使用 Mutations
const { addMutation, removeMutaion } = useMutations("demo", [
  "addMutation",
  "removeMutaion",
]);

addMutation({ b: 666 });
removeMutaion({ id: "008" });

// 重命名 Mutations
const { add, remove } = useMutations("demo", {
  add: "addMutation",
  remove: "removeMutaion",
});
```