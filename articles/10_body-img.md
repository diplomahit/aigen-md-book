# 第10章 Python生态 —— 翻译官们的不同活法

## 学习目标

1. 掌握三大 Python Markdown 引擎（markdown、mistletoe、pymdownx）的用法
2. 理解它们与 JS 引擎的架构差异
3. 能根据 Python 项目需求选择合适的引擎

## 生活类比

Python 世界的三位翻译官：**markdown** 是标准公务员——按规矩办事，稳定可靠但不花哨；**mistletoe** 是速度狂人——用 C 扩展加速，追求极致性能；**pymdownx** 是扩展狂魔——装了十几个插件包，什么都能干。

![Python 三引擎类比：markdown=标准公务员，mistletoe=速度狂人，pymdownx=扩展狂魔](assets/images/01-chapter10-python-engines.png)

## markdown：标准公务员

### 基本用法

```python
import markdown

text = """
# 标题

这是一段**粗体**文字。

- 列表项1
- 列表项2
"""

html = markdown.markdown(text)
print(html)
# 输出: <h1>标题</h1>\n<p>这是一段<strong>粗体</strong>文字。</p>\n<ul>\n<li>列表项1</li>\n<li>列表项2</li>\n</ul>
```

一行代码，和 JS 的 `marked.parse()` 一样简单。

### 源码结构

```
markdown/
├── __init__.py           # 入口
├── blockparser.py        # 块级解析器
├── inlineparser.py       # 行内解析器
├── postprocessors.py     # 后处理
├── unithelper.py         # 工具函数
└── extensions/           # 扩展包
    ├── codehilite.py     # 代码高亮
    ├── meta.py           # 元数据（YAML Front Matter）
    ├── tables.py         # 表格
    └── fenced_code.py    # 栅栏代码块
```

![Python markdown 源码结构：blockparser、inlineparser、postprocessors、extensions 扩展目录](assets/images/04-chapter10-markdown-structure.png)

### blockparser.py 的核心逻辑

```python
# 源码路径：markdown/blockparser.py（简化）
class BlockParser:
    def parse(self, source):
        document = Element('div')
        self.parser_state = ParseState()

        for line in source.split('\n'):
            for matcher in self.matchers:
                m = matcher.match(line)
                if m:
                    block = matcher.run(m, document)
                    if block:
                        document.append(block)
                    break

        return document
```

这和 JS 引擎的 `for line in lines` 遍历模式一致——逐行扫描，匹配规则，生成元素。

### 扩展机制

markdown 的扩展机制是其最大优势：

```python
import markdown

md = markdown.Markdown(extensions=[
    'markdown.extensions.extra',   # 扩展语法（表格、脚注等）
    'markdown.extensions.codehilite',  # 代码高亮
    'markdown.extensions.tables',  # 表格
    'markdown.extensions.meta',    # 元数据
])

html = md.convert(text)
```

### 与 Django/Flask 的集成

```python
# Django 模板过滤器
# templatetags/markdown_extras.py
from markdown import markdown as md
from django import template

register = template.Library()

@register.filter(is_safe=True)
def markdown(value):
    return md(str(value))
```

在 Django 模板中直接使用：

```html
<!-- templates/article.html -->
{{ article.content|markdown }}
```

## mistletoe：速度狂人

### 基本用法

```python
import mistletoe

text = """
# 标题

这是**粗体**文字。
"""

html = mistletoe.markdown(text)
print(html)
```

和 `markdown` 引擎用法一致，但速度更快。

### 为什么快？

mistletoe 用 **C 扩展**加速核心解析逻辑：

```python
# 伪代码：mistletoe 的 C 加速
# 源码路径：mistletoe/cmodule.c（C 扩展）
// 核心分词器用 C 编写，Python 通过 ctypes 调用
static PyObject* fast_tokenize(PyObject* self, PyObject* args) {
    const char* src;
    PyArg_ParseTuple(args, "s", &src);
    // C 层面的逐字符扫描和匹配
    // 比 Python 层面的正则匹配快 5-10 倍
}
```

性能对比（解析 1000 次同一篇 5000 字文档）：

| 引擎 | 时间 | 相对速度 |
|------|------|----------|
| mistletoe | ~150ms | 1x（最快） |
| markdown | ~600ms | 0.25x |
| marked (JS) | ~200ms | 0.75x |

![mistletoe 架构：SpanToken 和 BlockToken 直接输出 HTML，无独立 AST 层](assets/images/05-chapter10-mistletoe-architecture.png)

### 架构差异

mistletoe 的架构和 JS 引擎有相似之处，但更简化：

```
mistletoe: Markdown → SpanToken/BlockToken → HTML
```

它没有独立的 AST 层——Token 既是分词结果也是中间表示。这比 remark 的 AST 链简单得多，但比 markdown 的"直接输出 HTML"多了一步 Token 组织。

```python
# 源码路径：mistletoe/span.py（简化）
class SpanToken:
    delimiter = None
    children = []
    prepend = []

    @classmethod
    def parse_pattern(cls, pattern, match, inner=True):
        # 行内分词：匹配行内模式（粗体、链接、代码...）
        # 递归解析内层内容
        pass
```

### 适用场景

- **高频解析**：每秒需要解析成千上万条 Markdown（如聊天系统）
- **流式解析**：mistletoe 支持流式处理
- **资源受限**：内存有限的嵌入式场景

## pymdownx：扩展狂魔

### 基本概念

pymdownx 不是一个独立的 Markdown 引擎——它是 **markdown 引擎的扩展层**：

```python
import markdown
from pymdownx import superfences, highlight, tabs, snipscope

md = markdown.Markdown(extensions=[
    'pymdownx.superfences',   # 嵌套代码块
    'pymdownx.highlight',     # 代码高亮（基于 pygments）
    'pymdownx.tabbed',        # 标签页
    'pymdownx.snippets',      # 代码片段插入
    'pymdownx.emoji',         # Emoji 支持
    'pymdownx.caret',         # 上标
    'pymdownx.superfences',   # 嵌套代码块
    'pymdownx.details',       # 折叠面板
])

html = md.convert(text)
```

![pymdownx 扩展生态：围绕 markdown 核心围绕 20+ 扩展包](assets/images/06-chapter10-pymdownx-extensions.png)

### 扩展列表

pymdownx 包含**超过 20 个扩展包**：

| 扩展 | 功能 |
|------|------|
| `superfences` | 嵌套代码块（代码块中嵌套代码块） |
| `highlight` | Pygments 代码高亮 |
| `tabbed` | 标签页 |
| `snippets` | 代码片段插入（类似 include） |
| `emoji` | Emoji 支持 |
| `details` | 折叠面板 |
| `superfenced` | 高级栅栏代码块 |
| `inlinehilite` | 行内代码高亮 |
| `critic` | 修订标记（接受/拒绝） |
| `attr_list` | 自定义属性 |

### 适用场景

| 场景 | 原因 |
|------|------|
| Sphinx 文档 | Sphinx 默认使用 markdown+pymdownx |
| MkDocs | MkDocs 的默认 Markdown 引擎 |
| 技术博客 | 丰富的扩展覆盖大多数需求 |

## Python vs JavaScript 引擎对比

### 架构对比

| 维度 | Python (markdown) | JavaScript (marked) |
|------|-------------------|---------------------|
| 解析方式 | 正则匹配 + 状态机 | 正则匹配 + 状态机 |
| AST | 有（Element 树） | 有（内部 AST） |
| 扩展机制 | extensions/ 目录 | 插件链 / 配置 |
| 输出格式 | 仅 HTML | HTML（可自定义 Renderer） |
| GFM 支持 | 需要扩展 | marked 配置 / mistletoe 原生 |

### 核心差异

**Python 生态缺少统一的 AST 框架**：

JS 生态有 remark（unified + AST 链），Python 没有对应的统一框架。这是因为：

1. **Python 的 Markdown 使用场景更集中**——主要是博客、文档站，不需要 JS 生态那样广泛的 AST 转换需求
2. **Python 的扩展方式是"插件包"而非"插件链"**——pymdownx 是一个包包含 20+ 扩展，而不是 20 个独立的 `.use()` 调用
3. **Python 生态更重视"开箱即用"**——安装一个包就搞定，不像 JS 需要手动组装 unified 链

![Python vs JS 引擎架构对比：Python 用扩展包和目录结构，JS 用插件链](assets/images/07-chapter10-python-vs-js.png)

![性能对比：mistletoe 最快 150ms，marked 200ms，Python markdown 600ms](assets/images/08-chapter10-performance-comparison.png)

![Python 生态哲学：使用场景集中、插件包方式、开箱即用](assets/images/09-chapter10-python-philosophy.png)

## 动手练习

1. 用 `markdown` 引擎解析同一段 Markdown，对比带/不带 GFM 扩展的输出差异
2. benchmark 三个引擎（markdown、mistletoe、pymdownx）解析同一份长文档的速度
3. 思考题：为什么 Python 生态缺少统一的 AST 框架？（提示：从语言哲学和生态成熟度角度思考）

## 本章小结

这一章中我们了解了 Python 生态的三大 Markdown 引擎。首先介绍了 markdown 的标准公务员风格（稳定可靠、扩展成熟、Django/Flask 集成），然后分析了 mistletoe 的速度狂人路线（C 扩展加速、性能最优），接着讨论了 pymdownx 的扩展狂魔策略（基于 markdown 的 20+ 扩展层），最后对比了 Python 和 JS 生态的核心差异。

本章我们一起学习了以下概念：

| 概念 | 解释 |
|------|------|
| markdown | Python 官方推荐引擎，扩展机制成熟 |
| mistletoe | C 扩展加速，速度最快 |
| pymdownx | markdown 的扩展层，20+ 扩展包 |
| 扩展 vs 插件链 | Python 用扩展包，JS 用插件链 |
| 无统一 AST | Python 生态缺乏 remark 式的 AST 框架 |

![本章知识回顾：markdown 标准公务员、mistletoe 速度狂人、pymdownx 扩展狂魔、Python vs JS 架构对比](assets/images/10-chapter10-summary.png)

下一章我们将进入命令行工具——终端里的 Markdown 阅读器、转换器和生成器。
