# 第11章 命令行工具 —— 终端里的"阅读器"

## 学习目标

1. 掌握终端中的 Markdown 预览工具（glow、mdcat、cmark）
2. 理解 Markdown 的命令行转换流程（md → html / pdf / epub）
3. 能选择适合工作流的命令行工具

## 生活类比

终端里的 Markdown 工具就像随身听——随时随地打开，不用开浏览器。glow 是"终端里的 GitHub"，mdcat 是"终端里的预览器"，pandoc 是"万能翻译官"。

![终端 Markdown 工具类比：glow=终端 GitHub，mdcat=预览器，pandoc=万能翻译官](assets/images/03-chapter11-terminal-tools.png)

## glow：终端里的 GitHub

### 安装与使用

```bash
# 安装（macOS）
brew install glow

# 安装（Linux，下载二进制文件）
curl -fsSL https://github.com/charmbracelet/glow/releases/latest/download/glow_$(uname | awk '{print tolower($0)}')_$(uname -m).tar.gz | tar xz

# 预览 Markdown 文件
glow README.md

# 预览 URL 上的 Markdown
glow https://raw.githubusercontent.com/charmbracelet/glow/main/README.md

# 交互模式（逐页浏览）
glow --style dark
```

### 特点

glow 由 Charm（`charm.sh`）团队开发，是他们众多终端工具之一（还有 bubbletea、lip Gloss 等）。它的核心特点是**彩色渲染**：

- 标题有颜色区分（H1 红色、H2 蓝色……）
- 代码块保持等宽字体，语法高亮
- 链接显示为下划线
- 表格用边框线分隔

### 工作原理

glow 在内部使用 **cmark**（CommonMark 的 C 参考实现）解析 Markdown，然后用 **lip Gloss**（Charm 的 Go 终端 UI 库）渲染彩色输出：

```
Markdown 源码 → cmark（解析为 AST） → lip Gloss（终端 ANSI 渲染） → 彩色终端输出
```

![glow 渲染流程：cmark 解析 Markdown 到 AST，lip Gloss 渲染为彩色终端输出](assets/images/04-chapter11-glow-workflow.png)

## mdcat：终端渲染器

### 安装与使用

```bash
# 安装（Rust）
cargo install mdcat

# 预览文件
mdcat file.md

# 管道输入
cat README.md | mdcat

# 与 git diff 结合
git diff | mdcat
```

### 特点

mdcat 由 Axel Erlund 开发，是 Rust 生态的终端渲染器：

- 支持 ANSI 彩色输出
- 支持图片渲染（在支持图片的终端中）
- 支持代码语法高亮
- 支持管道输入（`cat file.md | mdcat`）

### 与 glow 的对比

| 维度 | glow | mdcat |
|------|------|-------|
| 语言 | Go | Rust |
| 彩色输出 | 是 | 是 |
| 图片渲染 | 否 | 是（特定终端） |
| 代码高亮 | 是 | 是（基于 pygments/external） |
| 包管理 | brew | cargo |
| 生态 | Charm 全家桶 | Rust 生态 |

![glow vs mdcat 对比：五种维度横向对比终端 Markdown 渲染器](assets/images/05-chapter11-glow-vs-mdcat.png)

## cmark：通用参考实现

### 安装与使用

```bash
# 安装
apt install cmark
brew install cmark

# 转换为 HTML
cmark README.md -o README.html

# 转换为 CommonMark AST 表示
cmark README.md --to commonmark

# 转换为 GitHub Flavored Markdown
cmark README.md --to gfm
```

### 特点

cmark 是 **CommonMark 规范的 C 参考实现**——它不追求功能多，而是追求**规范的正确性**。

如果你在开发 Markdown 引擎或需要验证某个 Markdown 文件是否符合规范，cmark 是最可靠的工具。

## pandoc：万能转换器

### 安装与使用

```bash
# 安装（Linux）
apt install pandoc

# 安装（macOS）
brew install pandoc

# Markdown → HTML
pandoc input.md -o output.html

# Markdown → PDF
pandoc input.md -o output.pdf --pdf-engine=xelatex

# Markdown → EPUB
pandoc input.md -o output.epub

# Markdown → Word (docx)
pandoc input.md -o output.docx

# Markdown → LaTeX
pandoc input.md -o output.tex

# Markdown → RTF
pandoc input.md -o output.rtf
```

### 支持的格式

pandoc 支持**超过 50 种格式**之间的转换：

| 输入 | 输出 | 示例 |
|------|------|------|
| Markdown | HTML | `pandoc in.md -o out.html` |
| Markdown | PDF | `pandoc in.md -o out.pdf` |
| Markdown | EPUB | `pandoc in.md -o out.epub` |
| Markdown | docx | `pandoc in.md -o out.docx` |
| Markdown | LaTeX | `pandoc in.md -o out.tex` |
| docx | Markdown | `pandoc doc.docx -o doc.md` |
| HTML | Markdown | `pandoc doc.html -o doc.md` |

### 为什么 pandoc 能转这么多格式？

pandoc 的核心秘密是 **通用 AST**：

```
输入格式 → [解析器] → 通用 AST → [渲染器] → 输出格式
```

无论输入是 Markdown、docx、HTML 还是 LaTeX，都被解析为同一个"通用 AST"。然后从这个通用 AST 可以输出到任意格式。

这就好比一个通用插座——任何国家的插头都能插进去，然后转换成你需要的电压输出。

```
Markdown  ─┐
HTML      ─┼─→ 解析器 → 通用 AST → 渲染器 → docx
docx      ─┘                   ↑
LaTeX ────┘                    │
                               └→ 输出格式：HTML / PDF / EPUB / ...
```

![pandoc 通用转换器架构：多种输入格式经通用 AST 输出到多种格式](assets/images/06-chapter11-pandoc-architecture.png)

### pandoc  vs 其他转换工具

| 工具 | pandoc | markdown-to-pdf | markdown-to-epub |
|------|--------|-----------------|------------------|
| 支持格式 | 50+ | 仅 PDF | 仅 EPUB |
| 输入格式 | Markdown/docx/HTML/LaTeX | 仅 Markdown | 仅 Markdown |
| 配置灵活性 | 高（模板、CSS） | 低 | 中 |
| 适用场景 | 通用转换 | 快速 PDF | 电子书 |

## 工作流：命令行工具的选择

### 日常预览

| 工具 | 命令 | 适用场景 |
|------|------|----------|
| glow | `glow file.md` | 日常快速预览 |
| mdcat | `cat file.md \| mdcat` | 管道中输入 |
| cmark | `cmark file.md` | 规范验证 |

### 格式转换

| 工具 | 命令 | 输出格式 |
|------|------|----------|
| pandoc | `pandoc in.md -o out.html` | HTML/PDF/EPUB/docx/LaTeX... |
| glow | `glow file.md --to html` | HTML |

### 最佳实践

1. **日常预览用 glow**——彩色渲染，终端中阅读体验好
2. **格式转换用 pandoc**——万能转换，支持 50+ 格式
3. **规范验证用 cmark**——CommonMark 参考实现
4. **管道集成用 mdcat**——支持 `cat file \| mdcat`

![命令行工作流选择指南：根据需求选择 glow、mdcat、cmark 或 pandoc](assets/images/07-chapter11-workflow-guide.png)

## 设计取舍

**为什么需要多个终端预览器？**

因为终端兼容性差异。不同终端模拟器（xterm、gnome-terminal、Windows Terminal）支持的 ANSI 颜色和图形协议不同。glow 用 Go 开发，跨平台但功能有限；mdcat 用 Rust 开发，更灵活但依赖 Rust 工具链。

**为什么 pandoc 支持这么多格式？**

因为它的通用 AST 架构。一旦有了统一的中间表示，输出格式只是"换个渲染器"的事。这个架构和 remark → rehype 的链式转换异曲同工——中间表示决定了可扩展性。

**命令行 vs GUI？**

命令行工具的优势是**可组合性**——`cat file.md | glow | wc -l` 可以把 Markdown 预览和字数统计组合成一条命令。GUI 工具的优势是**可视化**——拖拽、实时预览、图形化配置。两者不冲突，适合不同场景。

![设计取舍：为什么需要多个终端预览器、为什么 pandoc 支持多格式、命令行 vs GUI](assets/images/08-chapter11-design-tradeoffs.png)

## 动手练习

1. 安装 glow，用 `glow` 预览一个 GitHub README
2. 用 pandoc 将 Markdown 转为 PDF（需要安装 LaTeX）
3. 思考题：为什么 pandoc 支持这么多格式？（提示：从通用 AST 架构角度思考）

## 本章小结

这一章中我们了解了 Markdown 的命令行工具生态。首先介绍了终端预览器（glow 的彩色渲染、mdcat 的管道支持、cmark 的规范验证），然后深入讲解了 pandoc 的万能转换能力和通用 AST 架构，最后给出了工作流选择指南。

本章我们一起学习了以下概念：

| 概念 | 解释 |
|------|------|
| glow | Charm 开发的终端 Markdown 预览器，彩色渲染 |
| mdcat | Rust 开发的终端渲染器，支持管道和 ANSI |
| cmark | CommonMark 规范的 C 参考实现，规范验证 |
| pandoc | 万能转换器，通用 AST 架构支持 50+ 格式 |
| 通用 AST | 中间表示，让跨格式转换成为可能 |

![本章知识回顾：glow 终端预览、mdcat 管道支持、cmark 规范验证、pandoc 万能转换](assets/images/09-chapter11-summary.png)

第三篇（生态篇）到此结束。下一章开始第四篇——Markdown 在生产环境中的实战应用。
