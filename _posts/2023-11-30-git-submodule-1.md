---
layout: post
title: "git submodule 使用"
date: 2023-12-12
categories: [git]
tags: [git, submodule]
---

### 1. 添加子模块

> 进入主项目目录，使用以下命令创建一个子模块

```ts
git submodule add [子模块远程仓库地址]
// git submodule add https://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@github.com/zakihuang/git-sub-module.git 
```

此时项目仓库中会多出两个文件

+ .gitmodules：保存子模块的相关信息
+ git-sub-module：子模块代码
+ 这时候可以通过git status查看当前状态

![status 更新示意图](/assets/images/submodule_status.png#pic_left "submodule status")

### 2. 查看子模块版本信息
```ts
git submodule  
```

![submodule](/assets/images/submodule.png#pic_left "submodule")

然后git commit，push，成功后在主项目的远程仓库里可以看到子模块文件夹，并带上其仓库的版本号

### 3. 使用带有子模块的项目
后续使用者clone主项目后，子模块默认是空的，需要我们在项目根目录下执行如下命令完成子模块的下载

```ts
git submodule init
git submodule update
// 或
git submodule update --init --recursive
```
还有一种更简单的办法，在clone主项目的时候带参数，这样会递归地将项目中所有子模块的代码拉取。

```ts
git clone --recurse-submodules [主项目url]
```

对于已经clone过存在的项目，用git pull 命令同样如此

```ts
git pull --recurse-submodules
```

拉取子模块代码后，子模块默认显示在临时分支上，如果要进行开发，记得进入子模块切换到指定分支，如果子模块太多且分支一样，可以执行以下命令批量切换。

```ts
git submodule foreach git checkout xxx
```
如果不小心在临时分支上做了修改，要记得合并到主分支，否则修改可能会丢失。

### 4. 更新子模块

子模块是不断在维护更新的，在我们自己的项目里，一般不会去改动子模块。
第一种情况，子模块远程有更新，如果想保持本地子项目与远程仓库版本一致，就需要用以下命令更新子模块至远程仓库的最新版。


```ts
git submodule update --remote
// 注意：命令 git submodule update 是更新主项目内子模块到最新版本，即与主项目远程仓库记录的子模块版本一致
```

当然也可以进入到子模块目录下，用传统的git pull获取最新代码。如果主项目的子项目特别多，此时可以用以下命令执行

```ts
git submodule foreach git pull
```
第二种情况，如果本地主项目的子模块有更新，按以下步骤
+ a. 进入到子项目中对更新的代码进行一次提交操作，并推送到远程
+ b. 再回退到主项目中，对主项目进行一次提交，推送
> 注意：不管主项目是否有更新，只要子项目修改过代码并提交过，主项目就需要再提交一次，因为子项目的修改是会同步到主项目中的，这样才能保证开发的一致性
 
### 5. 删除子模块
+ a. 执行 git submodule deinit testsub，自动在 .git/config 中删除了子模块相关信息。这个命令如果添加上参数 --force，则子模块工作区内即使有本地的修改，也会被移除。
+ b. 执行 git rm testsub，移除了 testsub 文件夹，并自动在 .gitmodules 中相关信息。
> 此时，主项目中关于子模块的信息基本已经删除（可能.git/modules 目录下还有残余），但是不影响，继续commit，push，完成对子模块的删除。
 