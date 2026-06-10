# 第7章细纲：脚注与自定义属性

## 标题
## 脚注与自定义属性 —— 给文章加"批注本"

难度: ★★★☆☆ (3/5)
预备知识: 第2章行内语法（链接和引用语法）

## 学习目标
1. 掌握 GFM 脚注语法
2. 理解 HTML 标签在 Markdown 中的混写技巧
3. 能使用自定义属性为元素添加数据

## 生活类比
脚注就像书的页脚——正文中用一个上标数字（"见[1]"），读者翻到页脚才能看到完整解释。自定义属性就像给元素贴"便签"——在 HTML 元素上附加额外信息，Markdown 本身不关心，但 JavaScript 可以读取它。

## 模块地图

```
GFM 扩展语法
├── 脚注（Footnotes）
│   ├── 正文引用：[^1]
│   └── 脚注定义：[^1]: 脚注文字
├── HTML 混写
│   ├── 块级 HTML 标签：<div> <section> <details>
│   └── 行内 HTML 标签：<del> <sub> <sup>
├── 自定义属性（GFM）
│   └── {#id .class key="value"}
└── YAML Front Matter
    └── ---\ntitle: ...\n---
```

## 内容要点

### 1. 脚注

- 正文引用：`[^label]`
- 脚注定义：`[^label]: 脚注内容`
- 脚注的位置：通常在文档末尾
- 脚注中的链接和代码

### 2. HTML 混写

- 块级 HTML：`<details>`（折叠面板）、`<div>`（自定义容器）
- 行内 HTML：`<del>`（删除线）、`<sub>`（下标）、`<sup>`（上标）
- 为什么需要 HTML 兜底

### 3. 自定义属性

- 语法：`{#id .class key="value"}`
- 应用于下一个元素
- 用于 JavaScript 交互和 CSS 样式

### 4. YAML Front Matter

- 语法：`---\nkey: value\n---`
- 适用于 Hugo、Jekyll、VitePress 等
- 常见字段：title、date、tags、layout

## 设计取舍

- Q: 为什么脚注不在 CommonMark 核心？—— 语法歧义和实现差异
- Q: 为什么允许 HTML 混写？—— 复杂场景的兜底
- Q: 自定义属性只影响下一个元素？—— 简洁性优先

## 动手练习
1. 写一个包含脚注的段落，尝试在脚注中放一个链接
2. 尝试用 `<sup>` 和 `<sub>` 写化学公式
3. 思考题：为什么 YAML Front Matter 需要三条 `---` 包裹？

## 本章小结
本章我们一起学习了脚注、HTML 混写、自定义属性和 YAML Front Matter——Markdown 的扩展工具箱。
