---
layout: post
title: "commander.js 参数取反"
date: 2021-10-19
categories: [commander.js]
tags: [node, js]
---

commander.js 用于 Node.js 的命令行解析和任务处理

没什么特别要讲的，精炼强大，此处记录一下选项的基本使用。特别强调一下取反处理。

```ts
const { Command } = require("commander");
const program = new Command();

program
    .option("-a, --no-add", "add a file")
    .option("-u, --update <fileName>", "update a file")
    .parse(process.argv);

console.log(program.add, program.update);

// node cmd -u update
// 输出: true update

// node cmd --no-add -u update
// 输出: false update
```