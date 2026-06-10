# 第8章细纲：Markdown引擎揭秘

## 标题
## Markdown引擎揭秘 —— 翻译三件套

难度: ★★★☆☆ (3/5)
预备知识: 第1章（CommonMark规范）

## 学习目标
1. 理解 Markdown 解析的完整流水线（Tokenizer → Parser → Renderer）
2. 掌握 AST（抽象语法树）的结构和意义
3. 能说出不同引擎的架构差异

## 生活类比
Markdown 引擎就像一个翻译三件套：Tokenizer 是"读题人"（把文本读成词），Parser 是"理解人"（把词组成句子结构），Renderer 是"作答人"（把结构翻译成目标语言）。不同的引擎就是不同的"翻译团队"——有的团队读题快，有的团队理解深，有的团队作答漂亮。

## 模块地图

```
Markdown 引擎架构
├── Step 1: Tokenizer（分词器）
│   ├── 块级分词：识别标题、段落、列表、代码块
│   └── 行内分词：识别粗体、斜体、链接、代码
├── Step 2: Parser（解析器）
│   ├── 块级 AST：Document → Block → Inline
│   └── 行内 AST：Inline → Text, Emphasis, Code, Link
├── Step 3: Renderer（渲染器）
│   ├── HTML Renderer：AST → HTML
│   ├── 其他 Renderer：AST → PDF / EPUB / ASCII
│   └── Rehype/Remark 生态：AST → AST 转换
└── 插件系统（Plugins）
    ├── Tokenizer 插件：添加新语法
    ├── AST 插件：转换 AST 结构
    └── Renderer 插件：自定义输出
```

## 内容要点

### 1. Tokenizer（分词器）

- 块级分词：逐行扫描，识别块类型
- 行内分词：在块内部识别行内元素
- 源码路径：marked/src/Tokenizer.js

### 2. Parser（解析器）

- 构建 AST 树
- 块级 AST 与行内 AST 的关系
- AST 的结构示例

### 3. Renderer（渲染器）

- HTML 渲染：AST → HTML 字符串
- 其他格式渲染
- Rehype/Remark 的 AST 转换链

### 4. 插件系统

- Remark 插件：`unified().use(plugin)`
- 插件类型：Tokenizer 插件、AST 插件、Renderer 插件
- 生态插件数量

## 设计取舍

- Q: 为什么 AST 是必要的？—— 解耦解析和渲染
- Q: 为什么有不同风格的引擎？—— 速度 vs 功能 vs 可维护性
- Q: 插件系统为什么重要？—— 生态扩展

## 动手练习
1. 用 marked 解析一段 Markdown，查看生成的 AST
2. 编写一个 Remark 插件，将特定的文本替换为自定义 HTML
3. 思考题：为什么 Tokenizer 不直接输出 HTML？

## 本章小结
本章我们一起了解了 Markdown 引擎的内部结构——从分词到 AST 到渲染的完整流水线。
