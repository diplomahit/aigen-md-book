# 第11章细纲：命令行工具

## 标题
## 命令行工具 —— 终端里的"阅读器"

难度: ★★★☆☆ (3/5)
预备知识: 无

## 学习目标
1. 掌握终端中的 Markdown 预览工具（mdcat、glow、cmark）
2. 理解 Markdown 的命令行转换流程（md → html / pdf / epub）
3. 能选择适合工作流的命令行工具

## 生活类比
终端里的 Markdown 工具就像随身听——随时随地打开，不用开浏览器。glow 是"终端里的 GitHub"，mdcat 是"终端里的预览器"，cmark 是"终端里的翻译官"。

## 模块地图

```
命令行 Markdown 工具
├── 预览工具
│   ├── glow（Fuchsia 编写，终端预览）
│   ├── mdcat（Rust 编写，终端渲染）
│   └── cmark（C 编写，通用参考实现）
├── 转换工具
│   ├── pandoc（万能转换器）
│   ├── markdown-to-pdf
│   └── markdown-to-epub
└── 生成工具
    └── create-cc-skill（创建 skill）
```

## 内容要点

### 1. glow：终端里的 GitHub

- 安装：`brew install glow`
- 用法：`glow README.md`
- 特点：彩色渲染、内置预览、支持 URL

### 2. mdcat：终端渲染器

- 安装：`cargo install mdcat`
- 用法：`mdcat file.md`
- 特点：ANSI 终端渲染、支持图片和代码高亮

### 3. pandoc：万能转换器

- 安装：`apt install pandoc`
- 用法：`pandoc input.md -o output.html`
- 支持的格式：md → html / pdf / epub / docx / latex / ...

## 设计取舍

- Q: 为什么需要多个终端预览器？—— 终端兼容性差异
- Q: 为什么 pandoc 能转这么多格式？—— 中间 AST 层
- Q: 命令行 vs GUI？—— 工作流偏好

## 动手练习
1. 安装 glow，用 `glow` 预览一个 GitHub README
2. 用 pandoc 将 Markdown 转为 PDF
3. 思考题：为什么 pandoc 支持这么多格式？

## 本章小结
本章我们一起了解了命令行工具——从终端预览器到万能转换器。
