---
layout: post
title: "git submodule 原理"
date: 2023-11-30
categories: [git]
tags: [git]
---

#### 背景：
> 当前有部分业务使用了 jQuery 技术栈，另外，由多个小业务系统组成一个大的系统矩阵
小业务系统有一些业务无关的 模块 或 是组件需要抽象到一起，以便复用；由于一些历史原因，无法使用 npm
这时 git submodule 就成一个不错的解。

#### 流程：
> 
submodule 涉及到两个仓库类型：<br />
submodule<br />
子模块，比如需要使用的第三方库<br />
superproject<br />
主仓库，自己的工程，依赖子模块代码

在 superproject 中更新 submodule “指针”的操作如下：

![submodule 更新示意图](/assets/images/git-submodule-diagram.png "submodule 更新示意图")

#### 原理：
> 
上图，很好的说明了 git submodule 的原理 <br />
借用网络上的一个解释：<br />
A submodule is nothing but a clone of a git repo within another repo with some extra meta data. 
=> 子模块不过是一个放置于另一个仓库的 git copy。<br />

有了这个原理的认知:

Git submodule 本质上是两个独立的仓库，各自可以独立地像普通的 repo 一样操作。 同时 superproject 有一个“指针”，记录了它使用的子模块的 commit revision 。

无论是 superproject 还是 submodule ，都像普通的 repo 一样进行 branch, add, push, diff 等等的操作， 只是最后在通过 git submodule 命令再更新下新“指针”位置即可。

