# blog.7hihi.com

基于 Jekyll 4.2 的个人博客，使用 Jekyll-Paper 主题（纸质书籍排版风格）。

## 环境要求

- Ruby >= 2.6
- bundler ~> 2.4.22（Ruby 2.6 兼容版）

> 注：Ruby 2.6 无法安装 bundler 2.6+，需显式指定 2.4.22。rouge 也锁定为 3.30.0。

## 本地运行

```bash
gem install bundler -v '2.4.22'
bundle _2.4.22_ install
bundle _2.4.22_ exec jekyll serve --host 0.0.0.0 --port 4000
```

访问 http://localhost:4000

## 写新文章

在 `_posts/` 下新建文件，命名格式：`YYYY-MM-DD-title.md`

头部示例：

```yaml
---
layout: post
title: "文章标题"
date: 2025-01-01 10:00:00 +0800
categories: [分类名]
tags: [标签1, 标签2]
---
```

## 统计

当前集成 [Webfunny](https://webmonitor.hang-xin.cn) 前端监控。代码在 `_includes/analytics.html`，每页自动加载。需修改跟踪 ID 时直接编辑该文件。

## 部署

构建目标目录为 `./docs`，提交后由 GitHub Pages 自动发布。

```bash
bundle _2.4.22_ exec jekyll build
```

## 配置速查

| 配置项 | 说明 |
|--------|------|
| `_config.yml` | 站点标题、域名、分页、插件等 |
| `_data/menus.yml` | 顶部导航栏 |
| `destination: ./docs` | 构建输出目录 |
| `paginate: 25` | 首页文章分页数 |
| `mathjax: true` | 数学公式支持 |
| `mermaid: true` | 图表支持 |

## 许可

MIT
