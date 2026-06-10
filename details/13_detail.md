# 第13章细纲：从Markdown到文档站点

## 标题
## 从 Markdown 到文档站点 —— 从单篇到文库

难度: ★★★☆☆ (3/5)
预备知识: 第12章（博客生成）

## 学习目标
1. 掌握 MkDocs、GitBook、Docusaurus 三大文档站工具
2. 理解文档站与博客的区别
3. 能选择适合项目的文档工具

## 生活类比
博客像日记本——按时间倒序排列，个人化强。文档站像图书馆——按主题分类，结构化强。MkDocs 是社区图书馆（简单、免费），GitBook 是商业书店（精美但收费），Docusaurus 是大学图书馆（功能全、可定制）。

## 模块地图

```
Markdown → 文档站
├── MkDocs（Python 编写）
│   ├── mkdocs.yml 配置
│   ├── 目录即结构
│   └── 构建：mkdocs build
├── GitBook（商业产品）
│   ├── GitBook 编辑器
│   ├── 团队协作
│   └── 托管服务
└── Docusaurus（React 编写）
    ├── docusaurus.config.js
    ├── 版本管理
    └── 构建：npm run build
```

## 内容要点

### 1. MkDocs

- 安装：`pip install mkdocs mkdocs-material`
- 配置：mkdocs.yml
- 目录结构
- 主题：Material for MkDocs
- 插件系统

### 2. GitBook

- GitBook 编辑器
- 团队协作
- 托管服务

### 3. Docusaurus

- 初始化：`npx create-docusaurus`
- 配置：docusaurus.config.js
- 版本管理
- 插件和主题

### 4. 文档站 vs 博客

| 维度 | 文档站 | 博客 |
|------|--------|------|
| 结构 | 分类导向 | 时间导向 |
| 内容 | 静态为主 | 动态更新 |
| 导航 | 侧边栏 | 按时间 |
| SEO | 功能性强 | 简单 |

## 设计取舍

- Q: 为什么文档站用侧边栏？—— 导航效率
- Q: MkDocs vs Docusaurus？—— Python vs React
- Q: 为什么要版本管理？—— API 文档的长期维护

## 动手练习
1. 用 MkDocs + Material 搭建一个文档站点
2. 用 Docusaurus 初始化一个项目，添加版本管理
3. 思考题：为什么文档站不需要分页功能？

## 本章小结
本章我们一起了解了文档站工具——MkDocs、GitBook、Docusaurus。
