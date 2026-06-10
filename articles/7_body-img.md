# 第7章 脚注与自定义属性 —— 给文章加"批注本"

## 学习目标

1. 掌握 GFM 脚注语法
2. 理解 HTML 标签在 Markdown 中的混写技巧
3. 能使用自定义属性为元素添加数据
4. 了解 YAML Front Matter 的用途

## 生活类比

脚注就像书的页脚——正文中用一个上标数字（"见¹"），读者翻到页面底部才能看到完整解释。自定义属性就像给元素贴"便签"——在 HTML 元素上附加额外信息，Markdown 本身不关心，但 CSS 和 JavaScript 可以读取它。

![脚注=页脚批注，自定义属性=元素便签](assets/images/01-chapter7-html-tags.png)

## 脚注：正文之外的注释

### 基础语法

脚注由两部分组成：**正文引用**和**脚注定义**。

```markdown
Markdown 是一种轻量级标记语言[^1]。

[^1]: 轻量级标记语言：不需要写 HTML 标签，用简单的符号表示格式。
```

渲染后：正文中出现一个上标链接"[¹]"，点击后跳转到页面底部的脚注区域。

### 结构解析

脚注语法分为两个位置：

```markdown
<!-- 位置1：正文中的引用 -->
Markdown 是一种轻量级标记语言[^1]。

<!-- 位置2：文档底部的定义 -->
[^1]: 轻量级标记语言：不需要写HTML标签，用简单的符号表示格式。
```

两个位置可以**相距很远**——脚注定义可以放在文档的最末尾，不影响正文的阅读流。

![脚注结构：正文引用与文档底部定义通过标识符关联](assets/images/03-chapter7-footnote-structure.png)

### 脚注中的复杂内容

脚注不限于一句话，可以包含多个段落、列表、代码：

````markdown
[^2]: 这是一个多段落的脚注。

    第二段文字。

    - 脚注中的列表项1
    - 脚注中的列表项2

    ````python
    # 脚注中的代码
    print("hello")
    ````

    [引用链接](https://example.com)
````

### 命名脚注

脚注的标识符不限于数字，可以是任意文本：

```markdown
正文引用[^ref]。

[^ref]: 这是命名脚注的示例。
```

命名脚注在长文档中更有意义——`[^sec1]` 比 `[^1]` 更容易追踪。

![命名脚注 vs 数字脚注：[^ref] 比 [^1] 更易追踪](assets/images/04-chapter7-named-footnotes.png)

### 脚注的位置

脚注定义通常放在**文档末尾**。但 Markdown 解析器不会自动将脚注移到页面底部——它只是在渲染时识别脚注内容，具体"放在哪里"取决于渲染器。

在 GitHub、VitePress 等现代渲染器中，脚注会自动渲染为：
1. 正文中的上标链接
2. 页面底部的脚注列表
3. 点击脚注可回跳正文

## HTML 混写：Markdown 的兜底工具箱

### 为什么允许 HTML？

Markdown 的设计哲学是"简单场景用 Markdown，复杂场景用 HTML"。当 Markdown 的轻量语法无法满足需求时，**直接使用 HTML 标签**是最简单的解决方案。

### 行内 HTML 标签

最常用的行内 HTML 标签：

````markdown
<!-- 删除线 -->
<del>已删除的文字</del>

<!-- 下标 -->
H<sub>2</sub>O

<!-- 上标 -->
E=mc<sup>2</sup>

<!-- 强调 -->
<strong>加粗</strong>
<em>斜体</em>
````

渲染为：

- `<del>已删除的文字</del>` → 已删除的文字（删除线）
- `<sub>` → H₂O
- `<sup>` → E=mc²

### 块级 HTML 标签

块级标签用于更大的结构：

````markdown
<!-- 折叠面板 -->
<details>
<summary>点击查看详细内容</summary>

这是折叠后显示的内容。

- 列表项1
- 列表项2
</details>

<!-- 水平分割 -->
<div style="border-top: 1px solid #ccc; margin: 2em 0;"></div>
````

渲染为：

<details>
<summary>点击查看详细内容</summary>

这是折叠后显示的内容。

- 列表项1
- 列表项2
</details>

![HTML 混写示例：del 删除线、sub 下标、sup 上标、details 折叠面板](assets/images/05-chapter7-html-mixing.png)

### 为什么 `<details>` 值得单独提？

`<details>` 和 `<summary>` 是**唯一的原生折叠面板**——不需要 JavaScript，浏览器原生支持。在 Markdown 中几乎是唯一需要混写 HTML 的常见场景。

## 自定义属性（GFM）

### 语法

GFM 扩展了自定义属性语法，花括号包裹：

````markdown
这是一个带有自定义属性的段落。{#my-id .my-class data-value="42"}
````

属性应用于**紧随其后的元素**。

### 属性类型

花括号中可以包含三种属性：

```markdown
{#id .class key="value"}
 │    │      │
 │    │      └─ 数据属性（data-key="value"）
 │    └──────── CSS 类名
 └───────────── ID
```

![自定义属性语法：ID、CSS 类名、数据属性三种类型](assets/images/06-chapter7-custom-attributes.png)

### 工作原理

自定义属性最终输出为 HTML 属性：

```markdown
这是一段文字。{#para-1 .highlight data-note="重要"}
```

渲染为：

```html
<p id="para-1" class="highlight" data-note="重要">这是一段文字。</p>
```

### 适用场景

| 场景 | 示例 |
|------|------|
| CSS 样式 | `{#my-section .custom-style}` |
| JavaScript 交互 | `{#counter data-count="42"}` |
| 语义标识 | `{#warning .alert}` |

### 局限

- 只影响**紧随其后的元素**——不能跨元素
- 不是 CommonMark 核心——部分渲染器不支持

## YAML Front Matter

### 语法

YAML Front Matter 是许多静态站点生成器（Hugo、Jekyll、VitePress）支持的元数据格式：

````markdown
---
title: "Markdown 完全指南"
date: 2026-06-08
tags:
  - markdown
  - 教程
---

正文从这里开始。
````

三行 `---` 包裹 YAML 内容，必须在文档的**最开头**。

![YAML Front Matter 结构：三行分隔符包裹元数据块](assets/images/07-chapter7-yaml-frontmatter.png)

### 常见字段

| 字段 | 说明 |
|------|------|
| `title` | 页面标题 |
| `date` | 发布日期 |
| `tags` | 标签列表 |
| `description` | 页面描述（SEO） |
| `layout` | 布局模板 |
| `cover` | 封面图片 |

### 为什么用 YAML？

YAML 是 Markdown 生态的事实标准元数据格式：

1. **可读性好**：`key: value` 结构清晰
2. **支持嵌套**：`tags: [a, b, c]`
3. **生态广泛**：Hugo、Jekyll、VitePress、Docusaurus 都支持

### 不是 Markdown 的一部分

YAML Front Matter **不是 Markdown 规范的一部分**——它是静态站点生成器的扩展。如果你的渲染器不支持 Front Matter（如 GitHub 直接渲染），Front Matter 会被当作普通文本渲染出来（通常是一条横线）。

## 设计取舍

**为什么脚注不在 CommonMark 核心？**

脚注的语法歧义和实现差异太大。不同渲染器对脚注的定位（放在文档末尾还是页面底部）、样式（上标数字还是方括号链接）理解不同。CommonMark 选择不纳入——让 GFM 扩展处理。

**为什么允许 HTML 混写？**

因为 Markdown 的"简单"是有边界的。当需要 `<details>` 折叠面板、`<sub>` 下标、`<sup>` 上标时，硬要用 Markdown 语法去模拟（如 `~~删除线~~` 不是所有渲染器都支持），只会增加复杂度。HTML 是兜底方案——不常用，但关键时刻救命。

**自定义属性为什么只影响下一个元素？**

简洁性。如果自定义属性可以影响任意元素（"选择器"方式），那就要引入选择器语法（CSS 的选择器），这会大大增加 Markdown 规范的复杂度。"只影响下一个元素"是一个简单而有效的约定。

![设计取舍：为什么脚注不在核心、为什么允许HTML、为什么自定义属性只影响下一个元素](assets/images/08-chapter7-design-tradeoffs.png)

## 动手练习

1. 写一个包含脚注的段落，尝试在脚注中放一个链接
2. 尝试用 `<sup>` 和 `<sub>` 写化学公式（如 H₂SO₄、CaCO₃）
3. 思考题：为什么 YAML Front Matter 需要三条 `---` 包裹？（提示：一条 `---` 是水平分割线，两条 `---` 可能引起歧义）

## 本章小结

这一章中我们学习了 Markdown 的扩展工具箱。首先介绍了 GFM 脚注语法（正文引用 `[^label]` + 脚注定义 `[^label]: 内容`），然后讲解了 HTML 混写技巧（行内 `<del>` `<sub>` `<sup>`、块级 `<details>`），接着介绍了自定义属性语法 `{#id .class key="value"}` 和 YAML Front Matter 的用途。

本章我们一起学习了以下概念：

| 概念 | 解释 |
|------|------|
| 脚注 | `[^label]` 引用 + `[^label]: 内容` 定义 |
| HTML 混写 | 复杂场景用 HTML 标签兜底 |
| 自定义属性 | `{#id .class}` 为元素附加数据 |
| YAML Front Matter | `---\ntitle: ...\n---` 文档元数据 |

![本章知识回顾：脚注、HTML混写、自定义属性、YAML Front Matter](assets/images/09-chapter7-summary.png)

进阶篇到此结束。下一章开始第三篇——Markdown 的生态：引擎揭秘。我们将深入 Markdown 的"黑盒"，看看一个 Markdown 文件是如何被解析、转换、渲染的。
