# 第10章细纲：Python生态

## 标题
## Python生态 —— 翻译官们的不同活法

难度: ★★★☆☆ (3/5)
预备知识: 第8章（引擎架构）、第9章（JS引擎对比）

## 学习目标
1. 掌握三大 Python Markdown 引擎（markdown、mistletoe、pymdownx）的用法
2. 理解它们与 JS 引擎的架构差异
3. 能根据 Python 项目需求选择合适的引擎

## 生活类比
Python 世界的三位翻译官：**markdown** 是标准公务员——按规矩办事，稳定可靠但不花哨；**mistletoe** 是速度狂人——用 C 扩展加速，追求极致性能；**pymdownx** 是扩展狂魔——装了十几个插件包，什么都能干。

## 模块地图

```
Python Markdown 引擎
├── markdown（标准库式）
│   ├── 架构：Parser → BlockLexer → InlineLexer → HTML
│   ├── 特点：PyPI 官方推荐、扩展机制成熟
│   ├── 源码路径：markdown/blockparser.py
│   └── 适用场景：Django/Flask 集成
├── mistletoe（速度优先）
│   ├── 架构：Parser → Token → HTML（AST 极简化）
│   ├── 特点：C 扩展加速、速度最快
│   ├── 源码路径：mistletoe/
│   └── 适用场景：高性能场景
└── pymdownx（扩展优先）
    ├── 架构：基于 markdown 的扩展层
    ├── 特点：GFM + 大量自定义扩展
    ├── 适用场景：Sphinx、文档站
```

## 内容要点

### 1. markdown（Python 版）

- 安装：`pip install markdown`
- 基本用法：`markdown.markdown(text)`
- 源码结构：blockparser.py, inline.py, extensions/
- 扩展机制：extensions 系统
- 与 Django/Flask 的集成

### 2. mistletoe

- 安装：`pip install mistletoe`
- 基本用法：`mistletoe.markdown(text)`
- 特点：C 扩展、速度最快
- 源码结构：span.py, block.py, renderer.py
- 适用场景：高频解析

### 3. pymdownx

- 安装：`pip install pymdown-extensions`
- 基于 markdown 引擎的扩展层
- 扩展列表：超级表格、代码高亮、脚注、徽章等
- 适用场景：Sphinx 文档

### 4. Python vs JavaScript 引擎对比

| 维度 | markdown (Python) | mistletoe | pymdownx |
|------|-------------------|-----------|----------|
| 速度 | ★★★☆☆ | ★★★★★ | ★★★☆☆ |
| 功能 | ★★★★☆ | ★★★☆☆ | ★★★★★ |
| 易用性 | ★★★★★ | ★★★★☆ | ★★★☆☆ |
| 生态 | ★★★★☆ | ★★☆☆☆ | ★★★★☆ |

## 设计取舍

- Q: 为什么 Python 没有像 remark 一样的 AST 框架？—— 生态差异
- Q: 为什么 mistletoe 用 C 扩展？—— 追求速度
- Q: 为什么 pymdownx 依赖 markdown 引擎？—— 复用+扩展

## 动手练习
1. 用 markdown 引擎解析同一段 Markdown，对比带/不带 GFM 扩展的输出
2. benchmark 三个引擎解析同一份长文档的速度
3. 思考题：为什么 Python 生态缺少统一的 AST 框架？

## 本章小结
本章我们一起了解了 Python 生态的三大 Markdown 引擎——markdown、mistletoe、pymdownx。
