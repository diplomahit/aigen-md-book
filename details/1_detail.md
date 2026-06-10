# 第1章细纲：为什么是 Markdown？

## 标题
## 为什么是 Markdown？—— 给文字穿上"隐形西装"

难度: ★☆☆☆☆ (1/5)
预备知识: 无，任何文字编辑经验即可

## 学习目标
1. 理解 Markdown 诞生的历史背景和设计哲学
2. 掌握 Markdown 相对于 HTML 和纯文本的核心优势
3. 能说出 CommonMark 规范的意义

## 生活类比
给文章穿衣服：HTML 像定制西装——每颗纽扣都要手工缝制；纯文本像穿棉袄——保暖但没造型；Markdown 像穿优衣库——基础款但干净得体，穿 everywhere 都不违和。

## 模块地图

```
CommonMark 规范 (spec.md)
├── 0. 前言 (preamble.md) — 为什么需要规范？
├── 1. 前置（Preprocessing）—— 规范化换行和制表符
├── 2. 块级元素（Block Elements）—— 段落、标题、列表
├── 3. 行内元素（Inlines）—— 粗体、链接、代码
└── 4. 附录（Asts）—— 参考 AST 树结构
```

## 内容要点

### 1. Markdown 的诞生

- 2004年 John Gruber + Aaron Swartz
- Gruber 是 Daring Fireball 博客创始人，HTML 排版太痛苦
- "Markdown 的要点是……文本本身要能正常阅读"
- 对应规范文件：`CommonMark/spec.md` 的 Preamble 部分

### 2. Markdown vs HTML 的对抗

- HTML 的痛点：标签嵌套地狱、格式和语义绑定
- Markdown 的哲学：格式从文本中"退出来"
- 举例对比：同一个标题，HTML 6字符 vs Markdown 3字符
- 表格的对比：HTML 20行 vs Markdown 5行

### 3. Markdown vs 纯文本

- 纯文本的优势：无处不在、永不过期
- 纯文本的劣势：没有格式、没有结构
- Markdown 的甜蜜点：纯文本的兼容 + 轻量级格式

### 4. CommonMark 规范

- 2014年 CommonMark 项目成立
- 解决"各家实现不一致"的问题
- spec.md 是权威参考（当前 v0.31.0）
- 解析三件套：Tokenizer → AST → Renderer

### 5. 设计取舍

- Q: 为什么不直接用 HTML？—— 学习成本和写作效率
- Q: 为什么不扩展更多语法？—— 可读性优先
- Q: 为什么需要 CommonMark 规范？—— 互操作性

## 动手练习
1. 用纯 HTML 写一段带标题和段落的文字，再用 Markdown 重写，数一数少打了多少字符
2. 阅读 CommonMark spec.md 的 Preamble 部分，总结其设计动机
3. 思考题：如果 Markdown 只能保留3个语法特性，你会保留哪3个？

## 本章小结
本章我们讨论了 Markdown 为什么会出现。首先介绍了它的诞生背景——John Gruber 对 HTML 排版痛苦的反击，然后我们用 HTML 和纯文本做对照，找出了 Markdown 的甜蜜点：纯文本兼容 + 轻量级格式，接着我们了解了 CommonMark 规范及其解析流程（Tokenizer → AST → Renderer）。

本章我们一起学习了以下概念：

| 概念 | 解释 |
|------|------|
| John Gruber | Markdown 创建者，Daring Fireblog 创始人 |
| CommonMark | Markdown 的统一规范，解决互操作性 |
| Tokenizer → AST → Renderer | Markdown 解析的三件套 |
| 甜蜜点 | 纯文本兼容 + 轻量格式，在两者之间取平衡 |

下一章中，我们将学习 Markdown 的行内语法——那些让文字变"漂亮"的小技巧。
