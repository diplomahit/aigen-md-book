# 第12章 从 Markdown 到博客 —— 从食材到成品菜

## 学习目标

1. 理解 MDX 的概念和使用场景
2. 掌握静态站点生成器（Hugo、VitePress）的基本工作流
3. 了解从 Markdown 到可发布博客的完整流水线

## 生活类比

Markdown 是食材，静态站点生成器是厨房，HTML 是成品菜。你只负责准备食材（写 Markdown），厨房负责洗切炒（解析、主题渲染、构建），最后端出来的菜（HTML 站点）可以直接上桌（部署）。

这个流水线的好处是：**你只需要关注食材的质量，不用管厨房的装修**。

## MDX：Markdown + React

### 什么是 MDX？

MDX 是 **Markdown + JSX** 的混合语法。你可以在 Markdown 中直接嵌入 React 组件：

```mdx
# 我的博客

这是一段普通 Markdown 文字。

<Counter initial={0} />

更多 Markdown 内容。
```

其中 `<Counter>` 是一个 React 组件。

### 与普通 Markdown 的差异

| 特性 | Markdown | MDX |
|------|----------|-----|
| 标题、段落、列表 | 支持 | 支持 |
| 代码块 | 支持 | 支持 |
| 嵌入 React 组件 | 不支持 | 支持 |
| 导入/导出 | 不支持 | 支持 `import Component from './Component'` |
| 动态数据 | 不支持 | 支持 |

### 适用场景

- 博客中需要交互式内容（图表、代码编辑器、动画）
- 文档站中需要可交互的示例
- 产品页面中需要自定义组件

### 不适用场景

- 纯文字博客（MDX 增加了构建复杂度）
- 需要在非 JS 环境中渲染的文档
- 追求极致简单的写作体验

## Hugo：Go 编写的静态站生成器

### 项目初始化

```bash
# 创建项目
hugo new site myblog
cd myblog

# 安装主题
git init
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke

# 创建文章
hugo new posts/first-post.md
```

### 目录结构

```
myblog/
├── content/
│   ├── posts/
│   │   ├── first-post.md
│   │   └── second-post.md
│   └── _index.md
├── themes/
│   └── ananke/
├── config.toml        # 配置文件
└── layouts/            # 自定义模板（可选）
```

核心原则：**目录即路由**——`content/posts/first-post.md` 映射到 URL `/posts/first-post/`。

### 文章Front Matter

```yaml
---
title: "第一篇文章"
date: 2026-06-08
draft: false
tags:
  - markdown
  - 博客
---

正文从这里开始。
```

`draft: true` 的文章不会构建到输出目录——这是 Hugo 的草稿机制。

### 主题系统

Hugo 的主题本质上是 **Go 模板 + CSS + JavaScript**：

```
themes/ananke/
├── layouts/
│   ├── _default/
│   │   ├── baseof.html     # 基础布局（所有页面继承）
│   │   ├── list.html       # 列表页（文章列表）
│   │   └── single.html     # 单页（文章页面）
│   └── partials/           # 可复用组件
│       ├── header.html
│       └── footer.html
├── static/                 # 静态资源
└── assets/                 # CSS/JS 源文件
```

### 构建

```bash
# 本地预览
hugo server -D   # -D 显示草稿文章

# 构建静态站
hugo             # 输出到 public/ 目录
```

构建后，`public/` 目录就是完整的静态网站——所有 Markdown 都转换为了 HTML。

## VitePress：Vue 驱动的文档+博客

### 初始化

```bash
# 初始化项目
npm init vite@latest my-docs -- --template vue

cd my-docs
npm install
npx vitepress init

# 创建文章
mkdir docs
echo '# 欢迎\n\n这是我的 VitePress 站点。' > docs/index.md
echo '## 文章\n\nHello Markdown!' > docs/guide.md
```

### 配置文件

```javascript
// .vitepress/config.js
import { defineConfig } from 'vitepress'

export default defineConfig({
  title: '我的文档站',
  description: '一个基于 VitePress 的文档站点',
  
  themeConfig: {
    nav: [
      { text: '首页', link: '/' },
      { text: '指南', link: '/guide' },
    ],
    sidebar: [
      {
        text: '指南',
        items: [
          { text: '开始', link: '/' },
          { text: '文章', link: '/guide' },
        ],
      },
    ],
  },
})
```

### 主题定制

VitePress 内置了三种主题：

| 主题 | 适用场景 | 特点 |
|------|----------|------|
| Default | 通用 | 简洁、响应式 |
| Team | 团队介绍 | 卡片布局 |
| Home | 首页 | Hero + Features |

### 构建与部署

```bash
# 本地预览
npx vitepress dev

# 构建
npx vitepress build   # 输出到 .vitepress/dist

# 部署到 GitHub Pages
npx vitepress build
git push -f dist origingh-pages
```

## 部署：让站点上线

### GitHub Pages

```yaml
# .github/workflows/deploy.yml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
      - run: npm ci
      - run: npm run build
      - uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist  # Hugo: ./public
```

**优点**：免费、与代码仓库集成、自动部署
**缺点**：自定义域名需要额外配置、CDN 速度有限

### Vercel / Netlify

```yaml
# Vercel 配置（vercel.json，可选）
{
  "buildCommand": "hugo",
  "outputDirectory": "public",
  "framework": "static"
}
```

**Vercel/Netlify** 的优势：

1. **零配置部署**——连接 GitHub 仓库，自动识别 Hugo/VitePress 项目
2. **自动 HTTPS**——每次推送自动构建和部署
3. **全球 CDN**——比 GitHub Pages 快
4. **自定义域名**——免费支持

### 部署流程总结

```
你写 Markdown → Git Push → CI/CD 触发
  → Hugo/VitePress 构建
  → 上传到 CDN
  → 站点更新
```

整个流程大约 1-3 分钟，你不需要手动操作任何构建命令。

## 设计取舍

**为什么用静态站点而非 CMS？**

CMS（如 WordPress）需要在服务器上运行数据库和 PHP，增加了维护成本和攻击面。静态站点生成器的好处是：

- **速度快**——HTML 是预构建的，服务器只返回静态文件
- **安全**——没有数据库，没有注入攻击
- **便宜**——GitHub Pages 免费，Vercel 免费额度足够
- **版本控制**——Markdown 文件可以直接 Git 管理

**MDX vs 纯 Markdown？**

MDX 增加了交互能力，但牺牲了**简洁性**和**工具兼容性**。不是所有 Markdown 工具都支持 MDX（如 `glow`、`mdcat` 不解析 JSX）。选择 MDX 意味着你承诺使用支持它的工具链。

**Hugo vs VitePress？**

- Hugo 用 Go 编写，构建速度极快（数万篇文章秒级构建），但主题需要 Go 模板知识
- VitePress 用 Vue 编写，主题可以用 Vue 组件定制，但构建速度较慢

对于小中型站点（< 1000 篇文章），两者差异不大。对于大型站点（> 10000 篇文章），Hugo 的速度优势明显。

## 动手练习

1. 用 Hugo 初始化一个博客项目，写一篇 Markdown 文章并发布到 GitHub Pages
2. 用 VitePress 搭建一个文档+博客站点，自定义导航栏
3. 思考题：为什么静态站点生成器比 CMS 更快？（提示：从预构建和 CDN 角度思考）

## 本章小结

这一章中我们了解了从 Markdown 到博客的完整流水线。首先介绍了 MDX（Markdown + JSX，适合交互式内容），然后分别讲解了 Hugo（Go 编写、速度快、目录即路由）和 VitePress（Vue 编写、主题灵活、生态丰富）的工作流，最后讨论了 GitHub Pages、Vercel/Netlify 三种部署方案。

本章我们一起学习了以下概念：

| 概念 | 解释 |
|------|------|
| MDX | Markdown + JSX，可在 Markdown 中嵌入 React 组件 |
| Hugo | Go 编写的静态站生成器，目录即路由，构建速度快 |
| VitePress | Vue 驱动的文档+博客生成器，主题可定制 |
| 静态部署 | 预构建 HTML，上传 CDN，零服务器运维 |
| CI/CD | Git Push 自动构建和部署 |

下一篇（实战篇最后一篇）我们将学习：从 Markdown 到文档站点、Markdown 的极限扩展、以及如何制定生产级 Markdown 规范。
