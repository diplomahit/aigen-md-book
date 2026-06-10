# Markdown 完全指南 —— 从语法到生产级文档的轻量之道

## 篇一：Markdown 的直觉（基础篇）

### 第1章 为什么是 Markdown？
- 学习目标：理解 Markdown 的核心价值
- 生活类比：记事本 vs HTML vs Markdown
- 源码/规范对应：CommonMark 规范 v0.31.0
- 内容要点：Markdown 诞生背景（John Gruber, 2004），HTML 的痛点，纯文本的优势

### 第2章 行内语法全解
- 学习目标：掌握所有行内格式化
- 生活类比：给文字戴首饰（粗体、斜体、删除线、代码标记）
- 规范对应：CommonMark 行内元素定义
- 内容要点：加粗、斜体、加粗斜体、删除线、行内代码、链接、引用

### 第3章 块级元素全解
- 学习目标：掌握段落、标题、列表、引用、分割线
- 生活类比：报纸排版（标题、段落、侧栏）
- 规范对应：CommonMark 块级元素定义
- 内容要点：6级标题、有序/无序列表、嵌套列表、引用块、水平分割线

## 篇二：Markdown 的进阶（提升篇）

### 第4章 代码块与语法高亮
- 学习目标：理解 fence 代码块的三种形式和语言标识
- 生活类比：给代码做"防护服"
- 规范对应：CommonMark Fenced code blocks, GFM spec
- 内容要点：三反引号、三波浪线、语言标注、行号、行高亮

### 第5章 表格的艺术
- 学习目标：掌握 GFM 表格语法和排版技巧
- 生活类比：给数据建"网格"
- 规范对应：GFM table spec
- 内容要点：基础表格、对齐方式、复杂表格、表格陷阱

### 第6章 图片与嵌入
- 学习目标：掌握图片语法、alt 文本、图片链接
- 生活类比：给文章贴"照片墙"
- 规范对应：CommonLink Extended, GFM
- 内容要点：图片语法、相对/绝对路径、图片链接、标题和alt

### 第7章 脚注与自定义属性
- 学习目标：掌握脚注和 HTML 混写技巧
- 生活类比：给文章加"批注本"
- 规范对应：CommonMark footnotes, GFM custom attributes
- 内容要点：脚注语法、脚注位置、HTML 标签混写、自定义属性

## 篇三：Markdown 的生态（工具篇）

### 第8章 Markdown 引擎揭秘
- 学习目标：理解 parse → AST → render 流程
- 生活类比：翻译三件套（读题→理解→作答）
- 源码对应：marked 源码、remark/rehype 源码
- 内容要点：解析器（Tokenizer）、AST（Abstract Syntax Tree）、渲染器

### 第9章 JavaScript 生态
- 学习目标：掌握 marked、remark、markdown-it
- 生活类比：不同的翻译官
- 源码对应：marked/src/Tokenizer.js, remark/lib
- 内容要点：marked（快速）、markdown-it（插件化）、remark（AST 驱动）

### 第10章 Python 生态
- 学习目标：掌握 markdown、mistletoe、pymdownx
- 生活类比：Python 世界的"翻译官"们
- 源码对应：python-markdown 的 blockparser.py
- 内容要点：markdown（标准库式）、mistletoe（速度最快）、pymdownx（扩展丰富）

### 第11章 命令行工具
- 学习目标：掌握 mdcat、glow、cmark
- 生活类比：终端里的"阅读器"
- 内容要点：终端预览、HTML 转换、PDF 导出

## 篇四：Markdown 在生产环境（实战篇）

### 第12章 从 Markdown 到博客
- 学习目标：掌握 MDX 和静态站点生成
- 生活类比：从食材到成品菜
- 内容要点：MDX（Markdown + JSX）、Hugo、Jekyll、VitePress

### 第13章 从 Markdown 到文档站点
- 学习目标：掌握 MkDocs、GitBook、Docusaurus
- 内容要点：配置、主题、插件、部署

### 第14章 Markdown 的极限
- 学习目标：理解 Markdown 的边界和扩展方案
- 生活类比：知道刀能切什么、不能切什么
- 内容要点：LaTeX 数学公式（MathJax/KaTeX）、Mermaid 图表、PlantUML、HTML 兜底

### 第15章 编写生产级 Markdown
- 学习目标：制定团队 Markdown 规范
- 内容要点：Linter（markdownlint）、CI/CD 集成、一致性检查、命名规范、目录结构
