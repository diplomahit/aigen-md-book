# 第9章 JavaScript生态 —— 不同的翻译官

## 学习目标

1. 掌握三大 Markdown 引擎（marked、remark、markdown-it）的定位和用法
2. 理解它们的架构差异和适用场景
3. 能根据需求选择合适的引擎

## 生活类比

三位翻译官，三种风格：

**marked** 是跑步快的快递员——送得快，API 简单，适合"我只要快速把 Markdown 变成 HTML"的场景。**remark** 是学术翻译官——慢但精确，能在翻译过程中做各种加工（替换、统计、转换），适合"我要在翻译流程中插手"的场景。**markdown-it** 是全能翻译官——快、准、全，GFM 原生支持，适合"我全都要"的场景。

![三引擎类比：marked=快递，remark=学术翻译，markdown-it=全能翻译](assets/images/01-chapter9-js-engines.png)

## marked：速度优先

### 基本用法

```javascript
import { marked } from 'marked'

const html = marked.parse('# 标题\n\n这是一段**粗体**文字。')
// 输出: <h1>标题</h1>\n<p>这是一段<strong>粗体</strong>文字。</p>
```

一行代码搞定。

### 源码结构

```
marked/src/
├── Lexer.js       // 词法分析（分词）
├── Parser.js      // 语法分析（组句）
├── Tokenizer.js   // 分词器（核心）
├── Renderer.js    // 渲染器（输出 HTML）
└── defaults.js    // 默认配置
```

marked 的架构和第8章介绍的三件套完全一致：**Lexer（分词）→ Parser（解析）→ Renderer（渲染）**。

![marked 源码结构：Lexer、Parser、Tokenizer、Renderer 模块](assets/images/04-chapter9-marked-structure.png)

### Lexer.js 的核心逻辑

```javascript
// 源码路径：marked/src/Lexer.js（简化）
lex(src) {
  const tokens = []
  let token

  while (src.length) {
    // 顺序很重要：先匹配的优先
    token = this.lexHeading(src) ||
            this.lexCode(src) ||
            this.lexList(src) ||
            this.lexBlockquote(src) ||
            this.lexParagraph(src)

    if (token) {
      tokens.push(token)
    }
  }

  return tokens
}
```

注意 `||` 的匹配顺序——`lexHeading` 排在最前，说明 `#` 标题的优先级高于代码块、列表等。

### Renderer.js 的核心逻辑

```javascript
// 源码路径：marked/src/Renderer.js（简化）
render(tokens) {
  let html = ''
  for (const token of tokens) {
    switch (token.type) {
      case 'heading':
        html += this.renderHeading(token)
        break
      case 'paragraph':
        html += this.renderParagraph(token)
        break
      // ...
    }
  }
  return html
}
```

每个 `renderXxx` 方法对应一种 token 类型的 HTML 输出。

### 配置选项

```javascript
marked.setOptions({
  gfm: true,              // 启用 GFM（表格、删除线等）
  breaks: false,          // 段落内换行是否转为 <br>
  headerIds: true,        // 为标题添加 id 属性
  mangle: true,           // 混淆邮箱地址（防爬）
})
```

### 与 GitHub 的关系

marked 最初由 GitHub 的 Chris Wanstrath 创建（2011年），后来由 Tim Holman 接手维护。虽然现在 marked 和 GitHub 没有官方关系，但 GitHub 的 Markdown 渲染确实受到了 marked 的影响。

## remark：生态优先

### 基本用法

```javascript
import { unified } from 'unified'
import remarkParse from 'remark-parse'
import remarkGfm from 'remark-gfm'
import remarkRehype from 'remark-rehype'
import rehypeStringify from 'rehype-stringify'

const html = await unified()
  .use(remarkParse)       // Markdown → Remark AST
  .use(remarkGfm)         // 添加 GFM 支持
  .use(remarkRehype)      // Remark AST → Rehype AST
  .use(rehypeStringify)   // Rehype AST → HTML
  .process('# 标题')

console.log(String(html))
// 输出: <h1>标题</h1>
```

### 为什么这么复杂？

与 marked 的一行 `marked.parse()` 相比，remark 的流程看起来繁琐得多。但这正是它强大之处：

```
Markdown → remark-parse → Remark AST → remark-gfm → remark-rehype → Rehype AST → rehype-stringify → HTML
           ↑              ↑             ↑            ↑                ↑
         解析阶段        AST转换1     AST转换2     AST转换3         渲染阶段
```

每一环都是**可选的**——你可以插入任意多的 AST 转换步骤：

```javascript
.use(wordcount)           // 统计字数
.use(sidebar)             // 生成侧边栏目录
.use(toc)                 // 生成目录
.use(remarkImages)        // 图片优化
.use(remarkMath)          // 数学公式
```

![remark 插件链：从 Markdown 通过 remark-parse 到 AST，经多个插件转换到 Rehype AST，最终输出 HTML](assets/images/14-chapter9-remark-chain.png)

### 源码结构

```
remark/lib/
├── index.js              // unified 入口
├── preprocess/           // 预处理
├── parse/                // 解析（remark-parse）
├── stringify/            // 序列化（remark-stringify）
└── util/                 // 工具函数
```

remark 的核心设计哲学是 **"一切皆 AST"**——Markdown 解析成 AST，AST 转换成 AST，AST 序列化回 Markdown/HTML。

### 适用场景

| 场景 | 引擎 | 原因 |
|------|------|------|
| VitePress | remark | 需要在解析和渲染之间做 AST 转换（MDX、数学公式、Mermaid） |
| Docusaurus | remark | 需要丰富的插件生态 |
| MDX | remark | MDX 本质上是 remark 的 AST 扩展 |
| 快速预览 | marked | 只需要快速渲染，不需要复杂转换 |

## markdown-it：插件优先

### 基本用法

```javascript
import MarkdownIt from 'markdown-it'

const md = new MarkdownIt({
  html: true,       // 允许 HTML 标签
  linkify: true,    // 自动链接 URL
  typographer: true, // 排版优化
  highlight: function (str, lang) {
    // 集成 highlight.js
    return highlight(code, lang)
  }
})

const html = md.render('# Markdown\n\nHello, **world**!')
// 输出: <h1>Markdown</h1>\n<p>Hello, <strong>world</strong>!</p>
```

### 源码结构

```
markdown-it/lib/
├── index.js                // 入口
├── parser_block.js         // 块级解析器
├── parser_inline.js        // 行内解析器
├── renderer.js             // 渲染器
├── rules_block/            // 块级规则（标题、段落、列表...）
├── rules_inline/           // 行内规则（粗体、斜体、链接...）
├── helpers.js              // 工具函数
└── rule.js                 // 规则基类
```

markdown-it 与 marked 的最大区别：**它没有 AST**。markdown-it 的解析器直接输出 HTML，不经过 AST 中间层。

![markdown-it 源码结构：parser_block、parser_inline、renderer、rules_block、rules_inline](assets/images/06-chapter9-markdown-it-structure.png)

### 为什么没有 AST？

```
marked:       Markdown → Token → AST → HTML
remark:       Markdown → AST → AST → AST → HTML
markdown-it:  Markdown → HTML
```

markdown-it 选择**跳过 AST**，因为它的架构设计是**规则驱动**而非**树驱动**：

```javascript
// 源码路径：lib/parser_block.js（简化）
// 每个块级元素都是一个"规则"
const rules = {
  heading: function(state, start) {
    // 检查是否以 # 开头
    // 解析标题内容
    // 直接生成 HTML 输出
  },
  paragraph: function(state, start) {
    // 检查是否是段落
    // 解析段落内容
    // 直接生成 HTML 输出
  }
  // ...
}
```

这种设计的优势是**速度快**——没有 AST 的构建和遍历开销。劣势是**难以做 AST 级别的转换**——比如你想在渲染前修改整个文档的结构（生成目录、统计字数），在没有 AST 的情况下会困难得多。

![三引擎架构对比：marked有AST、remark链式AST、markdown-it无AST](assets/images/07-chapter9-architecture-comparison.png)

### 插件系统

markdown-it 有**超过 200 个插件**，包括：

- `markdown-it-gfm` — GFM 扩展
- `markdown-it-footnote` — 脚注
- `markdown-it-math` — 数学公式
- `markdown-it-mermaid` — Mermaid 流程图
- `markdown-it-anchor` — 标题锚链接

```javascript
const md = new MarkdownIt()
  .use(anchor)        // 标题锚链接
  .use(footnote)      // 脚注
  .use(math)          // 数学公式
  .use(mermaid)       // Mermaid
```

### 适用场景

| 场景 | 引擎 | 原因 |
|------|------|------|
| VuePress | markdown-it | 速度快，GFM 原生支持 |
| Hexo | markdown-it | 插件丰富，集成简单 |
| 博客/文档站 | markdown-it | 性能和功能平衡 |

## 三引擎对比

### 功能对比

| 维度 | marked | remark | markdown-it |
|------|--------|--------|-------------|
| 速度 | ★★★★★ | ★★☆☆☆ | ★★★★☆ |
| 规范覆盖率 | ★★★☆☆ | ★★★★★ | ★★★★☆ |
| 插件生态 | ★★★☆☆ | ★★★★★ | ★★★★☆ |
| 学习曲线 | ★★★★★（简单） | ★★☆☆☆（复杂） | ★★★☆☆（中等） |
| 体积 | ~30KB | ~100KB（含unified） | ~60KB |
| AST | 有（内部） | 有（公开） | 无 |
| GFM | 需要配置 | 需要插件 | 原生支持 |

![三引擎功能对比表：速度、规范覆盖率、插件生态、学习曲线、体积、AST、GFM支持](assets/images/08-chapter9-feature-comparison.png)

### 速度对比

```
解析 1000 篇技术文档：
marked:      ~200ms   （最快）
markdown-it: ~350ms   （次快）
remark:      ~800ms   （最慢，因为 AST 转换）
```

### 选择指南

| 你的需求 | 推荐引擎 |
|----------|----------|
| 快速渲染，不需要复杂功能 | **marked** |
| 需要在渲染流程中做 AST 转换 | **remark** |
| 博客/文档站，需要 GFM 和插件 | **markdown-it** |
| MDX 支持 | **remark**（MDX 基于 remark） |
| 静态站点生成器（VitePress/Docusaurus） | **remark** |
| 静态站点生成器（VuePress/Hexo） | **markdown-it** |

![三引擎选择指南决策树：根据速度和功能需求选择 marked、remark 或 markdown-it](assets/images/09-chapter9-selection-guide.png)

### 设计取舍

**为什么没有"最好"的引擎？**

因为**速度、功能、可扩展性**三者不可兼得。marked 追求速度，牺牲了规范覆盖率和可扩展性；remark 追求可扩展性，牺牲了速度；markdown-it 追求平衡，但在极端的任一方面都不突出。

**为什么 remark 这么复杂？**

因为它的目标不是"快速把 Markdown 变成 HTML"，而是"提供一套可扩展的 Markdown 处理框架"。每一个 `.use()` 调用都是一次 AST 转换——这种灵活性是有代价的。

**为什么 markdown-it 选择自定义规则而非 AST？**

性能和简洁。没有 AST 意味着更少的内存占用和更快的执行速度。对于大多数博客和文档站场景，"Markdown → HTML"直接转换足够了。如果需要 AST 级别的转换，开发者可以选择 remark。

![设计取舍：为什么没有最好引擎、为什么remark复杂、为什么markdown-it跳过AST](assets/images/10-chapter9-design-tradeoffs.png)

## 动手练习

1. 用三个引擎分别解析同一段 Markdown（包含标题、段落、代码块、列表），对比速度和 HTML 输出
2. 为 marked 编写一个自定义渲染器，输出 JSON 而非 HTML（重写 `Renderer.render` 方法）
3. 思考题：为什么 VitePress 选择 remark 而非 marked？（提示：考虑 MDX 支持和 AST 转换需求）

## 本章小结

这一章中我们对比了 JavaScript 生态中的三大 Markdown 引擎。首先介绍了 marked 的快速解析架构（Lexer → Parser → Renderer），然后讲解了 remark 的链式 AST 转换和插件生态，接着分析了 markdown-it 的规则驱动架构和无 AST 设计，最后给出了选择指南。

本章我们一起学习了以下概念：

| 概念 | 解释 |
|------|------|
| marked | 速度优先，Lexer → Parser → Renderer，API 简单 |
| remark | 生态优先，unified + 插件链，AST 驱动 |
| markdown-it | 插件优先，规则驱动，无 AST，GFM 原生支持 |
| 无 AST | markdown-it 直接输出 HTML，跳过 AST 中间层 |
| 链式转换 | remark 的 `.use()` 链，可插入任意 AST 转换 |

![本章知识回顾：marked速度优先、remark生态优先、markdown-it插件优先、架构对比、选择指南](assets/images/11-chapter9-summary.png)

下一章我们将进入 Python 生态——markdown、mistletoe、pymdownx 三大引擎的详细对比。
