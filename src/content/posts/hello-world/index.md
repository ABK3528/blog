---
title: 开博第一篇：把博客搬到 Astro + Cloudflare，全程不碰 R2
published: 2026-09-30
description: "换框架记录：零对象存储、零数据库、图片在构建期自动转 WebP/AVIF，本地 Markdown 写作 + git push 自动部署。"
image: "./cover.png"
tags: ["Blog", "Cloudflare", "Astro"]
category: 折腾
draft: false
---

之前那套 CMS 的后台上传要靠对象存储，这次索性换成纯静态方案：文章是 Markdown，图片跟文章放在同一个 git 仓里，构建的时候自动生成多尺寸的 WebP。

## 本地图片：构建期自动优化

直接把图片放在文章目录旁边，用相对路径引用：

![本地图片会被构建期处理](./demo-local.png)

构建产物里会变成带哈希的静态资源，同时生成 `srcset` 多尺寸，浏览器按屏幕宽度自己挑 —— 不需要任何对象存储，也不消耗运行时图片变换额度。

## 外链图片：原样引用

```md
![远程图片](https://example.com/pic.jpg)
```

远程图片不走本地优化管线，直接由对方服务器提供。

## 代码块

```python
from pathlib import Path

def posts(root: str = "src/content/posts"):
    for p in sorted(Path(root).glob("*/index.md")):
        print(p.parent.name, "->", p.stat().st_size, "bytes")
```

## 提示块

:::note
写作流程就是本地写 Markdown，然后 `git push`，Cloudflare 构建完自动上线。
:::

:::tip
图片建议长边压到 1600px 以内再进仓，仓里别堆原图。
:::

## 部署

| 环节 | 用的东西 | 花销 |
| --- | --- | --- |
| 托管 | Cloudflare Workers 静态资源 | 免费额度内 |
| 图片 | 跟文章同仓，构建期优化 | 0 |
| 搜索 | Pagefind（构建期生成索引） | 0 |
| 数据库 | 不需要 | 0 |
