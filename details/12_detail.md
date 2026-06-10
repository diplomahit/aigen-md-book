# 第12章细纲：从Markdown到博客

## 标题
## 从 Markdown 到博客 —— 从食材到成品菜

难度: ★★★☆☆ (3/5)
预备知识: 无

## 学习目标
1. 理解 MDX 的概念和使用场景
2. 掌握静态站点生成器（Hugo、VitePress）的基本工作流
3. 了解从 Markdown 到可发布博客的完整流水线

## 生活类比
Markdown 是食材，静态站点生成器是厨房，HTML 是成品菜。你只负责准备食材（写 Markdown），厨房负责洗切炒（解析、主题渲染、构建），最后端出来的菜（HTML 站点）可以直接上桌（部署）。

## 模块地图

```
Markdown → 博客流水线
├── MDX（Markdown + JSX）
│   └── Markdown 中嵌入 React 组件
├── Hugo（Go 编写，静态站生成器）
│   ├── 目录即路由
│   ├── 主题系统
│   └── 构建：hugo → public/
├── VitePress（Vue 编写，文档+博客）
│   ├── .vitepress/config.js
│   ├── 主题配置
│   └── 构建：vite build → .vitepress/dist
└── 部署
    ├── GitHub Pages
    ├── Vercel
    └── Netlify
```

## 内容要点

### 1. MDX

- 语法：Markdown 中嵌入 JSX
- 适用场景：交互式博客内容
- 与纯 Markdown 的差异

### 2. Hugo

- 安装与项目创建
- 目录结构
- 主题系统
- 构建命令

### 3. VitePress

- 安装与初始化
- 配置文件
- 主题定制
- 构建与部署

### 4. 部署

- GitHub Pages（免费）
- Vercel/Netlify（CI/CD 自动部署）

## 设计取舍

- Q: 为什么用静态站点而非 CMS？—— 速度与安全性
- Q: MDX vs 纯 Markdown？—— 交互性 vs 简洁性
- Q: Hugo vs VitePress？—— Go 速度 vs Vue 生态

## 动手练习
1. 用 Hugo 初始化一个博客项目，写一篇 Markdown 文章并发布
2. 用 VitePress 搭建一个文档+博客站点
3. 思考题：为什么静态站点生成器比 CMS 更快？

## 本章小结
本章我们一起了解了从 Markdown 到博客的完整流水线。
