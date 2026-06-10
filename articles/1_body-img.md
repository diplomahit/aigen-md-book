# 第1章 为什么是 Markdown？—— 给文字穿上"隐形西装"

## 学习目标

1. 理解 Markdown 诞生的历史背景和设计哲学
2. 掌握 Markdown 相对于 HTML 和纯文本的核心优势
3. 能说出 CommonMark 规范的意义

## 生活类比

想象一下，你要写一篇文章发表在网站上。你有三个选择：

**纯文本**就像穿棉袄——保暖，到哪都能穿，但就是没造型。**HTML**像定制西装——精致但每颗纽扣都要手工缝，换个场合可能就不合身了。**Markdown**像穿优衣库的基础款——干净、得体、去哪都不违和，而且便宜。

![纯文本、HTML、Markdown 三种写作方式的对比：纯文本简单无格式、HTML 标签繁多、Markdown 简洁得体](assets/images/01-concept-three-choices.png)

这就是 Markdown 的定位。它不是要你取代 HTML，也不是要回到纯文本时代，而是在两者之间找到一条中间道路。

## Markdown 的诞生

2004年，John Gruber 发布了第一个 Markdown 语法定义。

Gruber 是谁？他是著名博客 [Daring Fireblog](https://daringfireblog) 的创始人。他的痛点非常真实：

> 我想发明一种标记语言，它的源代码看起来就像一篇干净的信件，渲染后却拥有漂亮的、正确的 HTML 输出。

你看，"源代码看起来就像一篇干净的信件"——这就是 Markdown 的核心哲学。

我们来看看 CommonMark 规范（`spec.md`）的 Preamble 部分：

```
# CommonMark Spec
# version 0.31.0
#
# This is a reference manual for the CommonMark spec,
# which defines a precise specification of the markup syntax
# and parsing rules for the exchange of structured text via the Internet.
# It is also useful as a guide for other implementations.
```

这一段前言告诉我们三件事：

1. **它是参考手册**——不是某个人的"最佳实践"，而是社区共识
2. **精确定义**——每一行语法规则都有明确的边界，不是"大概这么写就行"
3. **交换格式**——目的是跨平台、跨工具互通

### 为什么需要规范？

在 CommonMark 出现之前，Markdown 是一场"各玩各的"的混乱局面：

- 有人用 `**` 表示粗体，有人用 `_` 表示粗体
- 有人列表缩进两个空格，有人缩进四个
- 有人把 `~~` 变成删除线，有人当普通字符

同一份 `.md` 文件，在不同的渲染器里可能长得不一样。这就像你写好的一封信，寄到 A 地址是横版，寄到 B 地址变成竖版——谁看了都头疼。

![CommonMark 规范文档结构：Preamble、Preliminaries、Block Elements、Inlines 四个核心章节](assets/images/02-commonmark-spec.png)

CommonMark 的出现，就是给这个"各玩各的"的局面画上句号。从此，一份 Markdown 文档在 GitHub、VS Code、 Hugo、Jupyter 里的样子，应该是一致的。

## Markdown vs HTML：一场不对等的战斗

我们直接上代码对比。

假设你想写一段文字：一个二级标题、一段正文、一行代码和一个链接。

**用 HTML 怎么写？**

```html
<!-- HTML版本 —— 标签嵌套地狱 -->
<h2>欢迎使用</h2>       <!-- 1. 开始标签 -->
<p>                    <!-- 2. 开始标签 -->
  这是 <code>一行代码</code> 和 <a href="https://example.com">一个链接</a>。 <!-- 3. 行内标签嵌套 -->
</p>                   <!-- 4. 闭合标签 -->
                      <!-- 5. 空行（可选但推荐）-->
```

![Markdown 与 HTML 的直观对比：HTML 标签密密麻麻、Markdown 简洁清晰](assets/images/03-markdown-vs-html.png)

数一下：6 个标签、1 个自闭合标签（如果 `br` 也算的话），大约 **4 个字符的信息量**（"欢迎使用"）对应 **60+ 个字符的标记符号**。

**用 Markdown 怎么写？**

```markdown
## 欢迎使用

这是 `一行代码` 和 [一个链接](https://example.com)。
```

同样表达同样的意思，只需要 **20 个字符的标记符号**。

这就是 Markdown 的"压缩率"优势——它把 HTML 的"仪式感"（开始、闭合、层级）压缩成了"直觉"（# 表示标题，** 表示粗体）。

## Markdown vs 纯文本：甜蜜点的寻找

那为什么不直接用纯文本？

纯文本确实是最安全的格式。你的 `.txt` 文件在 1970 年能用，2070 年照样能用。但纯文本有一个致命缺陷：**没有格式**。

你想表达一段代码？纯文本只能靠缩进：

```
    这段代码是缩进表示的
    但缩进几个空格？2个？4个？无限制？
    不同工具理解不同
```

你想表达一个引用？

```
    这是引用？
    还是只是段文字开头有空格？
    读者不知道
```

Markdown 的甜蜜点就在于此：**保留纯文本的可读性，同时增加轻量级的格式标识**。

一份 Markdown 文件，即使用不支持 Markdown 的编辑器打开，你也能读懂大概——粗体是 `**文字**`，链接是 `[文字](URL)`，标题是 `# 文字`。这些语法标记在纯文本中不会破坏阅读。

这就是 John Gruber 说的"源代码看起来就像一篇干净的信件"——即使没有渲染器，你的 Markdown 文件本身也是一篇可读的文档。

## CommonMark 规范揭秘

CommonMark 规范当前版本是 **0.31.0**，它的文件结构是这样的：

```
spec.md（整个规范的主体）
├── 0. Preamble —— 前言和设计动机
├── 1. Preliminaries —— 前置处理（换行、制表符）
├── 2. Block Elements —— 块级元素（标题、段落、列表...）
├── 3. Inlines —— 行内元素（粗体、链接、代码...）
└── 4. ASTs —— 参考 AST 树结构
```

解析 Markdown 的过程，就像翻译一本书：

```
Markdown 源码 → [Tokenizer 分词] → [Parser 组句] → AST（抽象语法树）→ [Renderer 翻译] → HTML
```

![Markdown 解析流程：源码经分词器（Tokenizer）→ 抽象语法树（AST）→ 渲染器（Renderer）→ HTML](assets/images/04-tokenizer-ast-renderer.png)

### Tokenizer（分词器）

Tokenizer 把原始文本切割成"块"（blocks）和"行内元素"（inlines）。它做的事情很朴素：

```javascript
// 伪代码：Tokenizer 的核心逻辑
// 源码路径：marked/src/Tokenizer.js（示意）
function tokenize(src) {
  // 逐行扫描
  while (src.length > 0) {
    // 遇到 # 开头？→ 标题块
    // 遇到 - 开头？→ 列表项
    // 遇到 > 开头？→ 引用块
    // 遇到空行？→ 段落结束
    // 否则 → 段落内容
  }
}
```

这段代码的逻辑很直白：它就像一个识字的人读文章，看到"这行有#"就标记为标题，看到"这行有>"就标记为引用。

### Parser（解析器）

Parser 把 Tokenizer 分好的"词"组合成句子——也就是 **AST（抽象语法树）**。

AST 是什么？你可以把它想象成文章的"骨架"：

```
Document
├── Heading (level=2, text="欢迎使用")
├── Paragraph
│   └── Inline
│       ├── Text("这是 ")
│       ├── Code("一行代码")
│       ├── Text(" 和 ")
│       └── Link(text="一个链接", url="https://example.com")
```

每一行 Markdown 被翻译成了结构化的节点树。渲染器只需要遍历这棵树，逐个节点输出 HTML 即可。

### Renderer（渲染器）

Renderer 是最灵活的一环——同样的 AST，不同的 Renderer 可以输出不同的格式：

| Renderer | 输出 | 适用场景 |
|----------|------|----------|
| HTML Renderer | `.html` | 网页、博客 |
| PDF Renderer | `.pdf` | 论文、报告 |
| EPUB Renderer | `.epub` | 电子书 |
| ASCII Renderer | `.txt` | 终端预览 |

![同一个 AST 可被多种渲染器输出为 HTML、PDF、EPUB、TXT 等不同格式](assets/images/05-multi-renderer.png)

这就是为什么同样的 Markdown 源码，可以用在 GitHub（HTML）、Jupyter（HTML）、Hugo（HTML）、MkDocs（HTML）里——因为它们共享的是同一个 AST，只是换了不同的 Renderer。

## 设计取舍

**为什么不直接用 HTML？**

HTML 的学习曲线陡峭。你要理解标签的层级、属性、嵌套规则，还要记一堆闭合标签。对于"只是想写点文字"的用户来说，HTML 就像要考驾驶证才能骑自行车。

Markdown 把门槛降到了"会打字就行"。

**为什么不用更多扩展语法？**

CommonMark 选择做减法。它只定义了最基本的语法——标题、段落、列表、链接、粗体、斜体、代码、引用。没有表格（由 GFM 扩展）、没有数学公式（由 KaTeX 扩展）、没有流程图（由 Mermaid 扩展）。

这种"少即是多"的设计哲学，恰恰是 Markdown 能够普及的原因。语法越少，互操作性越强，工具链越简单。

**为什么需要 CommonMark 规范？**

在规范出现之前，同一个 Markdown 文件在不同的渲染器里可能"长得不一样"。比如一段代码：

```
This is a paragraph
with two lines.
```

没有规范约束时，有的渲染器把它当成两个段落（中间空行），有的当成一个段落（保留换行）。规范出现后，所有渲染器必须按照同一套规则处理——这就叫**互操作性**。

![设计取舍三角：HTML 功能强但学习曲线陡，纯文本简单但无格式，Markdown 在两者间找到甜蜜点](assets/images/06-design-tradeoffs.png)

## 动手练习

1. 用纯 HTML 写一段带标题和段落的文字，再用 Markdown 重写，数一数少打了多少字符
2. 阅读 CommonMark spec.md 的 Preamble 部分，总结其设计动机
3. 思考题：如果 Markdown 只能保留3个语法特性，你会保留哪3个？

## 本章小结

这一章中我们学习了 Markdown 为什么会出现。首先介绍了它的诞生背景——John Gruber 对 HTML 排版痛苦的反击，然后我们用 HTML 和纯文本做对照，找出了 Markdown 的甜蜜点：纯文本兼容 + 轻量级格式，接着我们了解了 CommonMark 规范及其解析流程（Tokenizer → AST → Renderer），最后讨论了为什么选择"少即是多"的设计哲学。

本章我们一起学习了以下概念：

| 概念 | 解释 |
|------|------|
| John Gruber | Markdown 创建者，Daring Fireblog 创始人 |
| CommonMark | Markdown 的统一规范，解决互操作性 |
| Tokenizer → AST → Renderer | Markdown 解析的三件套 |
| 甜蜜点 | 纯文本兼容 + 轻量格式，在两者之间取平衡 |

下一章中，我们将学习 Markdown 的行内语法——那些让文字变"漂亮"的小技巧。
