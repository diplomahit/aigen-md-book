# 第8章 Markdown引擎揭秘 —— 翻译三件套

## 学习目标

1. 理解 Markdown 解析的完整流水线（Tokenizer → Parser → Renderer）
2. 掌握 AST（抽象语法树）的结构和意义
3. 能说出不同引擎的架构差异

## 生活类比

Markdown 引擎就像一个翻译三件套。想象你要把一封中文信翻译成英文：

**Tokenizer 是"读题人"**——他读信，把句子拆成"这行是标题"、"这行是段落"、"这行是列表"。他不做翻译，只做分类。

**Parser 是"理解人"**——他把读题人分好的词组合成句子结构。标题是什么标题，段落是什么段落，它们之间是什么关系。

**Renderer 是"作答人"**——他把理解人整理好的结构翻译成目标语言（HTML、PDF、EPUB……）。

不同的引擎就是不同的"翻译团队"——有的团队读题快（marked），有的团队理解深（remark），有的团队作答漂亮（Shiki）。但他们的三件套结构是一样的。

![引擎类比：Tokenizer=读题人，Parser=理解人，Renderer=作答人](assets/images/01-chapter8-engine-pipeline.png)

## Tokenizer：读题人

Tokenizer 的职责是**把原始文本切割成"块"（blocks）和"词"（tokens）**。

### 块级分词

块级分词逐行扫描 Markdown 源码，识别每一行属于什么类型的块：

```javascript
// 伪代码：块级分词的核心逻辑
// 源码路径：marked/src/Tokenizer.js（示意）
function blockTokenize(src) {
  const tokens = []
  let i = 0

  while (i < src.length) {
    const line = src[i]

    // 遇到 # 开头？→ 标题
    if (line.match(/^#{1,6}\s/)) {
      tokens.push({ type: 'heading', depth: countHash(line), text: stripHash(line) })
      i += 1
      continue
    }

    // 遇到 ---？→ 水平分割线
    if (line.match(/^(-{3,}|_{3,}|\*{3,})$/)) {
      tokens.push({ type: 'hr' })
      i += 1
      continue
    }

    // 遇到 - 或 * 或 + 开头？→ 列表
    if (line.match(/^[\s]*[-*+]\s/)) {
      tokens.push(...tokenizeList(src, i))
      // 列表可能跨多行，i 跳过多行
      continue
    }

    // 遇到 > 开头？→ 引用
    if (line.match(/^\s*>/)) {
      tokens.push(...tokenizeBlockquote(src, i))
      continue
    }

    // 遇到 ```？→ 栅栏代码块
    if (line.match(/^```/)) {
      tokens.push(...tokenizeFencedCode(src, i))
      continue
    }

    // 否则 → 段落
    tokens.push(...tokenizeParagraph(src, i))
    // 段落可能跨多行
    i += countParagraphLines(src, i)
  }

  return tokens
}
```

这段代码的核心逻辑非常直白：**看到什么模式，就标记为什么类型**。它不做翻译，只做分类。

![块级分词：文本行通过模式匹配识别为标题、分割线、列表、代码块、段落等 token](assets/images/03-chapter8-block-tokenizer.png)

### 行内分词

块级分词完成后，行内分词在**每个块内部**识别行内元素：

```javascript
// 伪代码：行内分词的核心逻辑
function inlineTokenize(text) {
  const tokens = []
  let remaining = text

  while (remaining.length > 0) {
    // 遇到 ** ？→ 粗体
    if (remaining.match(/\*\*/)) {
      tokens.push({ type: 'emphasis', delimiter: '**' })
      // ...
    }

    // 遇到 ` ？→ 代码
    if (remaining.match(/`/)) {
      tokens.push({ type: 'code', content: extractBacktickContent(remaining) })
      // ...
    }

    // 遇到 [ ？→ 链接
    if (remaining.match(/\[/)) {
      tokens.push({ type: 'link', ...extractLink(remaining) })
      // ...
    }

    // 普通文字
    tokens.push({ type: 'text', text: extractText(remaining) })
  }

  return tokens
}
```

行内分词的核心挑战是**匹配闭合**——`**粗体**` 中两个 `**` 如何配对？这涉及到嵌套、转义、奇偶计数等规则，是 Markdown 解析中最复杂的部分。

![行内分词：在块内部匹配粗体、代码、链接等行内元素，挑战在于闭合匹配](assets/images/04-chapter8-inline-tokenizer.png)

## Parser：理解人

Parser 的职责是**把 Tokenizer 分好的"词"组合成结构化的 AST（抽象语法树）**。

### AST 的结构

```
AST（抽象语法树）
└── root
    ├── heading [depth=2, text="欢迎使用"]
    ├── paragraph
    │   ├── text "这是 "
    │   ├── code "一行代码"
    │   ├── text " 和 "
    │   └── link
    │       ├── text "一个链接"
    │       └── url "https://example.com"
    └── paragraph
        ├── text "第二段文字。"
```

每一层节点都有明确的类型和内容：

| 节点类型 | 属性 | 说明 |
|----------|------|------|
| `root` | `children: []` | 树的根节点 |
| `heading` | `depth: 2` | 二级标题 |
| `paragraph` | `children: []` | 段落 |
| `text` | `value: "文字"` | 纯文本 |
| `code` | `value: "代码"`, `lang: "python"` | 代码块 |
| `link` | `url: "..."` | 链接 |

![AST 抽象语法树结构：根节点分支到标题、段落及子节点](assets/images/05-chapter8-ast-structure.png)

### 块级 AST 与行内 AST 的关系

Markdown 的 AST 是**双层结构**的：

```
Document（块级树）
├── Heading [depth=2]
│   └── children: [Inline 节点]  ← 行内 AST 嵌入在块级节点中
├── Paragraph
│   └── children: [Inline 节点]
└── CodeBlock
    └── value: "代码内容"  ← 代码块不需要行内 AST
```

块级 AST 管理"大结构"（标题、段落、列表），行内 AST 管理"小结构"（粗体、链接、代码）。代码块是例外——它不需要行内解析，内容原样保留。

![双层 AST：块级树管理大结构，行内树嵌入在块级节点中，代码块是例外](assets/images/12-chapter8-double-ast.png)

## Renderer：作答人

Renderer 的职责是**遍历 AST，逐个节点输出目标格式**。

### HTML 渲染器

```javascript
// 伪代码：HTML 渲染器的核心逻辑
// 源码路径：marked/src/Renderer.js（示意）
function render(ast) {
  let html = ''

  for (const node of ast.children) {
    switch (node.type) {
      case 'heading':
        html += `<h${node.depth}>${renderInline(node.children)}</h${node.depth}>`
        break
      case 'paragraph':
        html += `<p>${renderInline(node.children)}</p>`
        break
      case 'link':
        html += `<a href="${node.url}">${renderInline(node.children)}</a>`
        break
      case 'text':
        html += escapeHtml(node.value)
        break
      case 'code':
        html += `<code>${escapeHtml(node.value)}</code>`
        break
    }
  }

  return html
}

function renderInline(children) {
  let html = ''
  for (const child of children) {
    // 递归处理行内节点
    // 粗体、斜体、代码、链接...
  }
  return html
}
```

渲染器的核心逻辑：**遍历 AST → 匹配类型 → 输出对应的 HTML 标签**。

![渲染器遍历 AST：匹配节点类型输出对应 HTML 标签](assets/images/07-chapter8-renderer.png)

### 其他格式渲染

同样的 AST，不同的 Renderer 可以输出不同的格式：

| Renderer | 输入 | 输出 |
|----------|------|------|
| HTML Renderer | AST | `.html` |
| PDF Renderer | AST | `.pdf` |
| EPUB Renderer | AST | `.epub` |
| ASCII Renderer | AST | `.txt`（终端预览） |

这就是为什么同一个 Markdown 文件可以用在 GitHub（HTML）、Jupyter（HTML）、Hugo（HTML）里——它们共享**同一份 AST**，只是换了不同的 Renderer。

### Remark 和 Rehype 的 AST 转换链

remark 生态的特别之处在于：**AST 是中间格式，可以链式转换**。

```
Markdown → [remark-parse] → Remark AST → [remark-rehype] → Rehype AST → [rehype-stringify] → HTML
```

每一步都是 AST → AST 的转换：

```
remark-parse: Markdown → Remark AST
remark-rehype: Remark AST → Rehype AST
rehype-stringify: Rehype AST → HTML
```

这种链式架构允许在中间插入**任意数量的转换步骤**：

```
Markdown → remark-parse → Remark AST → [插件1] → [插件2] → [插件3] → rehype-stringify → HTML
```

每个插件只做一件事：插件1可能把 `::warning::` 变成 HTML 警告框，插件2可能把图片路径替换为 CDN URL，插件3可能统计字数。

![AST 链式转换：Markdown 通过 remark-parse 到 Remark AST，经插件链到 Rehype AST，最终输出 HTML](assets/images/08-chapter8-plugin-system.png)

## 插件系统

### Remark 插件

```javascript
import { unified } from 'unified'
import remarkParse from 'remark-parse'
import remarkRehype from 'remark-rehype'
import rehypeStringify from 'rehype-stringify'

const processor = unified()
  .use(remarkParse)       // Markdown → Remark AST
  .use(myPlugin)          // 自定义转换
  .use(remarkRehype)      // Remark AST → Rehype AST
  .use(rehypeStringify)   // Rehype AST → HTML

const result = processor.processSync('# 标题')
```

`.use(plugin)` 是统一 API，任何符合规范的插件都可以接入。

### 插件类型

| 类型 | 作用时机 | 示例 |
|------|----------|------|
| Tokenizer 插件 | 解析阶段，修改分词规则 | 添加自定义语法 |
| AST 插件 | AST 阶段，修改树结构 | 替换特定内容 |
| Renderer 插件 | 渲染阶段，修改输出 | 自定义 HTML 模板 |

![三种插件类型：Tokenizer 插件修改分词规则、AST 插件修改树结构、Renderer 插件修改输出](assets/images/09-chapter8-plugin-types.png)

### 生态插件数量

remark 生态目前有**超过 300 个插件**，涵盖：

- 表格支持（`remark-gfm`）
- 脚注支持（`remark-footnotes`）
- 目录生成（`remark-toc`）
- 字数统计（`remark-wordcount`）
- 图片优化（`remark-remark-images`）
- 数学公式（`remark-math`）
- 流程图（`remark-mermaid`）

## 设计取舍

**为什么 AST 是必要的？**

如果没有 AST，Tokenizer 必须直接输出 HTML——这意味着"解析"和"渲染"耦合在一起。AST 解耦了两者：你可以用同一份 AST 输出 HTML、PDF、EPUB，而不需要为每种格式重写解析逻辑。

**为什么有不同风格的引擎？**

因为不同场景的需求不同：

- **速度优先**：marked 追求最快解析，牺牲了部分规范覆盖率
- **功能优先**：remark 追求最大可扩展性，牺牲了速度
- **规范优先**：cmark 追求完全符合 CommonMark 规范，牺牲了扩展性

**插件系统为什么重要？**

Markdown 的核心语法是固定的（标题、段落、列表……），但**实际需求是无限的**（脚注、表格、数学公式、流程图……）。插件系统让核心保持简洁，同时通过生态扩展覆盖所有需求。

![设计取舍：为什么需要 AST、为什么有不同引擎、为什么插件系统重要](assets/images/10-chapter8-engine-design.png)

## 动手练习

1. 用 `marked` 解析一段 Markdown，查看生成的 AST（`marked.lexer(text)`）
2. 编写一个 Remark 插件，将特定的文本（如 `::warning::`）替换为自定义 HTML 警告框
3. 思考题：为什么 Tokenizer 不直接输出 HTML？（提示：从可测试性和可维护性角度思考）

## 本章小结

这一章中我们深入 Markdown 引擎的内部，了解了完整的解析流水线。首先介绍了 Tokenizer 的两个层次（块级分词和行内分词），然后讲解了 Parser 如何构建双层 AST 结构（块级树 + 行内树），接着介绍了 Renderer 如何遍历 AST 输出不同格式，最后讨论了 remark 的链式 AST 转换和插件系统。

本章我们一起学习了以下概念：

| 概念 | 解释 |
|------|------|
| Tokenizer | 将 Markdown 文本切割成 token（词） |
| AST | 抽象语法树，结构化的中间表示 |
| Renderer | 遍历 AST，输出目标格式（HTML/PDF/EPUB） |
| 双层 AST | 块级树 + 行内树，代码块是例外 |
| 链式转换 | Remark → Rehype → HTML，可插入任意插件 |

![本章知识回顾：Tokenizer、AST、Renderer、双层AST、链式转换](assets/images/11-chapter8-engine-overview.png)

下一章我们将进入 JavaScript 生态——marked、remark、markdown-it 三大引擎的详细对比。
