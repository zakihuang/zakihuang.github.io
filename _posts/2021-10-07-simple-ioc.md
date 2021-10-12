---
layout: post
title: "typescript ioc"
date: 2021-10-07
categories: [typescript]
tags: [ioc]
---

```ts
type mapType = { [k: string]: any };

type container = (name: string) => any;

function Container(provider: mapType) {
  const cache: mapType = {};

  const container = function (name: string) {
    if (!cache[name]) {
      cache[name] = provider[name](container);
    }

    return cache[name];
  };

  return container;
}

class UserCache {
  desc: string = '缓存类被依赖哦';
}

class UserService {
  desc: string = '我是用户服务类';
  constructor(public cache: UserCache) {
    console.log(1111, this);
  }
}


const container = Container({
  "cache": async () => new UserCache(),
  "user-service": async (c: container) => new UserService(await c("cache"))
});

// and a simple test:

container("user-service").then((service: UserService) => {
  console.log(service.cache);

  container("user-service").then((same_service: object) => {
    console.log(service === same_service); // true
  });
});
```

