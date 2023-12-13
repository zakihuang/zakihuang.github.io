---
layout: post
title: "向 npm 命令 传递参数"
date: 2023-12-13
categories: [npm]
tags: [npm, params]
---

#### 背景
> npm 打包命令需要根据客户不同，调整加载相应配置

### 具体做法
修改 package.json 文件的 scripts 里的具体脚本

 ```ts
 // package.json
{
    scripts: {
        "prod": "egg-scripts start --env=\"${npm_config_env}\"  --port \"${npm_config_port}\"",
    }
}
```

运行如下命令，----env 对应的参数会通过 \"${npm_config_env}\" 传入 npm 命令

> npm run build:prod ----env=customer.a.prod ----port=9000

最终，package.json 会被替换后执行

 ```ts
 // package.json
{
    scripts: {
        "prod": "egg-scripts start --env=customer.a.prod  --port 9000",
    }
}
```