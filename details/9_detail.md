# 第9章细纲：JavaScript生态

## 标题
## JavaScript生态 —— 不同的翻译官

难度: ★★★☆☆ (3/5)
预备知识: 第8章（引擎架构）

## 学习目标
1. 掌握三大 Markdown 引擎（marked、remark、markdown-it）的定位和用法
2. 理解它们的架构差异和适用场景
3. 能根据需求选择合适的引擎

## 生活类比
三大引擎就像三位翻译官：**marked** 是跑步快的快递员——送得快但不一定能把复杂内容翻译得完美；**remark** 是学术翻译官——慢但精确，还能在翻译过程中做各种加工；**markdown-it** 是全能翻译官——快、准、全，但学习曲线陡峭。

## 模块地图

```
JavaScript Markdown 引擎
├── marked（速度优先）
│   ├── 架构：Tokenizer + Renderer + Lexer
│   ├── 特点：速度快、API简单、GitHub 同宗
│   ├── 源码路径：marked/src/
│   └── 适用场景：快速渲染
├── remark（生态优先）
│   ├── 架构：unified + plugins + AST
│   ├── 特点：AST 驱动、插件丰富、可链式转换
│   ├── 源码路径：remark/lib/
│   └── 适用场景：文档站、静态生成器
└── markdown-it（插件优先）
    ├── 架构：Parser + Highlighter + Renderer
    ├── 特点：GFM 原生支持、插件丰富、速度快
    ├── 源码路径：lib/
    └── 适用场景：博客、文档站
```

## 内容要点

### 1. marked

- 安装：`npm install marked`
- 基本用法：`marked.parse(text)`
- 源码结构：`src/Lexer.js`、`src/Parser.js`、`src/Tokenizer.js`、`src/Renderer.js`
- 配置选项
- 与 GitHub 的关系

### 2. remark

- 安装：`npm install remark remark-gfm remark-rehype rehype-stringify`
- 基本用法：`unified().use(remarkParse).use(remarkGfm).use(remarkRehype).use(rehypeStringify).processSync(text)`
- 源码结构：`remark/lib/`
- 插件生态
- 适用场景：MDX、VitePress、Docusaurus

### 3. markdown-it

- 安装：`npm install markdown-it`
- 基本用法：`md.render(text)`
- 源码结构：`lib/`
- GFM 原生支持
- 插件系统和 highlight.js 集成
- 适用场景：VuePress、Hexo

### 4. 三引擎对比

| 维度 | marked | remark | markdown-it |
|------|--------|--------|-------------|
| 速度 | ★★★★★ | ★★☆☆☆ | ★★★★☆ |
| 规范覆盖率 | ★★★☆☆ | ★★★★★ | ★★★★☆ |
| 插件生态 | ★★★☆☆ | ★★★★★ | ★★★★☆ |
| 学习曲线 | ★★★★★（简单） | ★★☆☆☆（复杂） | ★★★☆☆（中等） |
| 体积 | 小 | 大（unified链） | 小 |

## 设计取舍

- Q: 为什么没有"最好"的引擎？—— 场景不同
- Q: 为什么 remark 这么复杂？—— 为了可扩展性
- Q: 为什么 markdown-it 选择自定义规则而非 AST？—— 性能和简洁

## 动手练习
1. 用三个引擎分别解析同一段 Markdown，对比速度和输出
2. 为 marked 编写一个自定义渲染器，输出 JSON 而非 HTML
3. 思考题：为什么 VitePress 选择 remark 而非 marked？

## 本章小结
本章我们一起对比了 JavaScript 三大 Markdown 引擎——marked、remark、markdown-it。
