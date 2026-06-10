# 第13章 从 Markdown 到文档站点 —— 从单篇到文库

## 学习目标

1. 掌握 MkDocs、Docusaurus 两大文档站工具
2. 理解文档站与博客的结构差异
3. 能选择适合项目的文档工具

## 生活类比

博客像日记本——按时间倒序排列，个人化强。文档站像图书馆——按主题分类，结构化强。MkDocs 是社区图书馆（简单、免费、材料主题精美）；Docusaurus 是大学图书馆（功能全、可定制、React 生态）。

## 文档站 vs 博客

在开始之前，先明确文档站和博客的核心差异：

| 维度 | 文档站 | 博客 |
|------|--------|------|
| 导航 | 侧边栏（分类导向） | 按时间（倒序列表） |
| 内容 | 静态为主，长期有效 | 动态更新，有时效性 |
| 搜索 | 全站搜索 | 简单搜索 |
| 版本管理 | 有（多版本文档） | 无 |
| SEO | 功能性强 | 简单 |
| 示例 | Python 文档、React 文档 | 技术博客、个人日志 |

### 为什么文档站用侧边栏？

因为读者搜索的是"功能 A 怎么用"，而不是"最新发布了什么"。侧边栏按功能分类，让读者能快速定位到需要的文档。

## MkDocs：Python 编写的文档站

### 安装与初始化

```bash
# 安装 MkDocs 和 Material 主题
pip install mkdocs mkdocs-material

# 初始化项目
mkdocs new my-docs
cd my-docs
```

### 目录结构

```
my-docs/
├── docs/
│   ├── index.md           # 首页
│   ├── getting-started.md # 入门指南
│   ├── api-reference.md   # API 参考
│   └── extras/
│       └── diagram.md     # 补充材料
├── mkdocs.yml             # 配置文件
└── site/                  # 构建输出（自动创建）
```

核心原则：**`docs/` 目录下的每个 `.md` 文件就是一个页面**。文件名自动映射为 URL：

```
docs/index.md        → /
docs/getting-started.md  → /getting-started/
docs/api-reference.md    → /api-reference/
```

### 配置文件

```yaml
# mkdocs.yml
site_name: 我的文档
theme:
  name: material

nav:
  - 首页: index.md
  - 入门指南:
    - 安装: getting-started.md
    - 配置: getting-started/configuration.md
  - API 参考: api-reference.md
  - 补充材料: extras/diagram.md
```

`nav` 定义了侧边栏的结构——这是一个**树形导航**，支持多级嵌套。

### 构建与预览

```bash
# 本地预览（热更新）
mkdocs serve

# 构建静态站
mkdocs build
```

`mkdocs serve` 启动一个本地服务器，修改 Markdown 文件后自动刷新页面。

### Material 主题的特性

Material for MkDocs 是 MkDocs 最流行的主题：

| 特性 | 说明 |
|------|------|
| 深色模式 | 自动跟随系统或手动切换 |
| 搜索 | 全站全文搜索 |
| 锚链接 | 每个标题自动生成锚链接 |
| 复制按钮 | 代码块自带复制按钮 |
| 多语言 | 支持 i18n |
| 插件 | 丰富的插件生态 |

## Docusaurus：React 编写的文档站

### 初始化

```bash
# 初始化项目
npx create-docusaurus@latest my-docs classic

cd my-docs

# 本地预览
npm run start

# 构建
npm run build
```

### 目录结构

```
my-docs/
├── docs/
│   ├── index.md              # 首页
│   ├── intro.md              # 入门指南
│   └── versioned_docs/
│       └── version-1.0/
│           └── api.md        # v1.0 的 API 文档
├── src/
│   ├── pages/                # 自定义 React 页面
│   └── components/           # 自定义 React 组件
├── versioned_docs/           # 版本化文档
├── versions.json             # 版本列表
├── docusaurus.config.js      # 配置文件
└── sidebars.js               # 侧边栏配置
```

### 配置文件

```javascript
// docusaurus.config.js
export default {
  title: '我的文档',
  tagline: '一个基于 Docusaurus 的文档站',
  favicon: 'img/favicon.ico',
  url: 'https://mydocs.io',
  baseUrl: '/',
  themeConfig: {
    navbar: {
      title: '我的文档',
      items: [
        { to: '/docs/intro', label: '文档', position: 'left' },
        { to: '/blog', label: '博客', position: 'left' },
      ],
    },
    sidebar: {
      autoCollapseCategories: true,
    },
  },
  presets: [
    [
      'classic',
      {
        docs: {
          sidebarPath: './sidebars.js',
          routeBasePath: 'docs',
        },
        blog: {
          routeBasePath: 'blog',
        },
      },
    ],
  ],
}
```

### 版本管理

Docusaurus 最强大的功能是**版本管理**——为不同版本的软件维护不同的文档：

````bash
# 创建版本
docusaurus docs:version 1.0

# 版本目录结构
docs/                 # 最新版本文档
versioned_docs/
  version-1.0/        # v1.0 的文档
  version-2.0/        # v2.0 的文档
versions.json         # 版本列表
````

用户访问 `mydocs.io/docs/` 时看到最新版本文档，访问 `mydocs.io/docs/1.0/` 时看到 v1.0 的文档。

### 与博客的集成

Docusaurus 同时支持文档和博客：

```javascript
// docusaurus.config.js
presets: [
  ['classic', {
    docs: { /* 文档配置 */ },
    blog: { /* 博客配置 */ },  // 同时启用博客
  }],
]
```

这意味着一个 Docusaurus 项目可以同时是文档站+博客，共享同一个基础设施。

### 自定义 React 页面

```javascript
// src/pages/custom.js
import Layout from '@theme/Layout'

export default function CustomPage() {
  return (
    <Layout title="自定义页面">
      <h1>这是一个自定义 React 页面</h1>
      <p>不是 Markdown，是纯 JSX。</p>
    </Layout>
  )
}
```

这允许你在 Markdown 之外，用 React 构建自定义页面（如首页、404 页面、 landing page）。

## MkDocs vs Docusaurus

### 功能对比

| 维度 | MkDocs | Docusaurus |
|------|--------|------------|
| 语言 | Python | Node.js (React) |
| 安装 | `pip install mkdocs` | `npm install` |
| 配置 | YAML（mkdocs.yml） | JavaScript（docusaurus.config.js） |
| 主题 | 丰富（Material 等） | 有限（默认 + 自定义） |
| 版本管理 | 需要插件 | 内置 |
| 博客 | 需要插件 | 内置 |
| 自定义页面 | 有限 | React 组件 |
| 构建速度 | 快 | 中等 |
| 学习曲线 | 低 | 中 |

### 选择指南

| 你的场景 | 推荐 | 原因 |
|----------|------|------|
| Python 项目文档 | **MkDocs** | Python 生态，安装简单 |
| API 文档 | **MkDocs** | Material 主题自带 API 文档样式 |
| React 项目文档 | **Docusaurus** | React 生态，与项目技术栈一致 |
| 多版本文档 | **Docusaurus** | 内置版本管理 |
| 快速搭建 | **MkDocs** | 安装即用，配置最少 |
| 需要自定义页面 | **Docusaurus** | React 组件，灵活性高 |

## 文档站的核心设计原则

### 1. 导航效率

文档站的第一原则是**让读者最快找到需要的信息**。侧边栏 + 搜索是最佳组合：

```
侧边栏（分类导航） + 搜索（关键词定位）
```

### 2. 一致性

所有文档页面应共享相同的：

- 主题（颜色、字体、间距）
- 导航结构（侧边栏、页脚）
- 写作风格（术语、语气）
- 代码示例格式

### 3. 可发现性

- **目录自动生成**——从 Markdown 的标题生成 TOC
- **交叉链接**——文档之间互相引用
- **搜索**——全文搜索是文档站的标配
- **面包屑**——显示当前页面在文档树中的位置

## 动手练习

1. 用 MkDocs + Material 搭建一个文档站点，配置侧边栏和搜索
2. 用 Docusaurus 初始化一个项目，添加版本管理
3. 思考题：为什么文档站不需要分页功能？（提示：文档是按需查找，不是按时间浏览）

## 本章小结

这一章中我们对比了 MkDocs 和 Docusaurus 两大文档站工具。首先介绍了 MkDocs 的 YAML 配置方式和 Material 主题的丰富功能，然后讲解了 Docusaurus 的版本管理、React 集成和博客功能，最后给出了选择指南。

本章我们一起学习了以下概念：

| 概念 | 解释 |
|------|------|
| MkDocs | Python 编写的文档站生成器，YAML 配置，Material 主题 |
| Docusaurus | React 编写的文档站生成器，内置版本管理 |
| 版本管理 | 为不同软件版本维护不同文档 |
| 文档站 vs 博客 | 分类导航 vs 时间导航 |
| 导航效率 | 侧边栏 + 搜索是文档站的核心 |

下一章我们将进入 Markdown 的极限扩展——数学公式、Mermaid 图表、PlantUML 等。
