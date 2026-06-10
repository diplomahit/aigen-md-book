# 第4章 代码块与语法高亮 —— 给代码穿上"防护服"

## 学习目标

1. 掌握三种代码块语法（三反引号、三波浪线、缩进代码块）
2. 理解语言标识的作用和语法高亮的渲染流程
3. 能使用 GFM 的代码行号和高亮功能

## 生活类比

第2章我们学了行内代码，像给短句裹一层"保鲜膜"。代码块则像"保险箱"——厚实的容器，保护多行代码不受格式解析的干扰。语法高亮就像给保险箱贴上标签（"这是 Python 的保险箱"），让渲染器知道用什么配色方案去打开它。

## 栅栏代码块：三反引号

### 基础语法

```python
def hello():
    print("Hello, World!")

hello()
```

用三对反引号（backtick）包裹：

```markdown
```python
def hello():
    print("Hello, World!")

hello()
```
```

`python` 是**语言标识**（info string），告诉渲染器"这是一段 Python 代码"。

### 语言标识的作用

语言标识不直接产生高亮效果。Markdown 解析器的工作只是提取这个标识，存入 AST：

```
CodeBlock
├── language: "python"
├── content: "def hello():\n    print(...)\n\nhello()\n"
└── meta: ""  // 可选的额外元数据
```

实际的颜色高亮，是由**后处理工具**完成的——通常是 Prism.js、highlight.js 或 Shiki。这些工具扫描渲染后的 HTML，找到带有语言标记的代码块，然后用对应的语法高亮规则上色。

### 语言别名

大多数解析器支持语言别名：

| 全称 | 别名 |
|------|------|
| `javascript` | `js` |
| `python` | `py` |
| `typescript` | `ts` |
| `bash` | `sh` |
| `css` | `scss`（SCSS 也算 CSS） |

### 不带语言标识的代码块

如果省略语言标识：

```markdown
```
这是一个纯文本代码块
没有语法高亮
```
```

渲染后依然是一个代码块，只是没有语法高亮——代码以等宽字体显示，但颜色是默认的灰黑。

## 三波浪线：反引号的救星

当你的代码中**包含反引号**时，三反引号就失效了：

````markdown
````
// 这个代码块里有反引号
function code() { return "`"; }
````
````

渲染器会把第一个 ` ``` ` 作为开始，把第二个 ` ``` ` 作为结束，但代码中间还有一个 ` ``` `，导致代码块提前关闭——渲染错误。

**解决方案：用三对波浪线**：

````markdown
~~~
// 代码里可以有任意多个反引号
function code() { return "```"; }
~~~
````

波浪线和反引号的效果完全一样，只是"容器"不同。

### 嵌套规则

规则很简单：**容器的分隔符（反引号或波浪线的数量）必须多于内容中的最大连续数量**。

- 内容中有 1 个反引号 → 用 3 个反引号或 3 个波浪线
- 内容中有 3 个连续反引号 → 用 4 个或更多反引号

````markdown
`````
// 内容中有 ``` 嵌套
function code() {
  return "```";
}
`````
````

## 缩进代码块：古老的方案

在栅栏代码块出现之前，Markdown 使用**缩进**来标记代码块：

````markdown
    def hello():
        print("Hello, World!")

    hello()
````

行首缩进 **4 个空格**或 **1 个 Tab**，即被识别为代码块。

### 为什么不推荐？

1. **肉眼不可见**：缩进代码块在源码中不容易识别，尤其是缩进不一致时
2. **难以嵌套**：嵌套代码块需要 8 空格、12 空格……很快变得混乱
3. **编辑器冲突**：现代编辑器的自动缩进功能经常和代码块缩进冲突

### 什么时候还会遇到？

缩进代码块在 CommonMark 规范中仍然存在，所以几乎所有渲染器都支持它。但在日常使用中，**栅栏代码块几乎是唯一选择**。

## 语法高亮的渲染流程

理解 Markdown 和语法高亮的关系很重要：**Markdown 不执行高亮，它只标注语言**。

```
Markdown 源码 → Markdown 解析器 → AST（含语言标记） → HTML
                                                        ↓
                                              语法高亮库（Prism / Shiki / highlight.js）
                                                        ↓
                                              HTML + <code class="language-python">
```

### 常见的高亮库

| 库 | 特点 | 适用场景 |
|-----|------|----------|
| **Shiki** | 基于 TextMate 语法，VS Code 同款，色彩精确 | 现代 Web 站点 |
| **Prism.js** | 轻量、插件丰富、主题多 | 轻量级站点 |
| **highlight.js** | 自动检测语言、免配置 | 快速集成 |

### 以 Shiki 为例的解析流程

```javascript
// 伪代码：Shiki 的高亮流程
// 源码路径：shiki/dist/index.js（示意）
async function highlight(code, language) {
  // ① 加载对应语言的语法（如 Python.tmLanguage.json）
  const grammar = await loadGrammar(language)
  // ② 将代码逐行分词
  const tokens = tokenize(code, grammar)
  // ③ 为每个 token 分配颜色（基于主题）
  const coloredTokens = applyTheme(tokens, theme)
  // ④ 生成带 class 的 HTML
  return generateHTML(coloredTokens)
}
```

这段代码的每一步都有明确的职责：

1. **加载语法**：每种语言对应一个 TextMate 语法文件（`.tmLanguage.json`），定义了什么字符序列匹配什么 token
2. **分词**：把代码字符串切分成 token（关键字、字符串、注释、标识符……）
3. **着色**：根据用户选择的主题（如 `vscode-dark`），给每个 token 分配 CSS class
4. **生成 HTML**：输出 `<span class="tok-keyword">def</span>` 这样的 HTML

## 代码行号与行高亮

### 代码行号

GitHub 默认在代码块中**自动显示行号**——这是 GitHub 平台的扩展功能，不是 Markdown 规范的一部分。

```markdown
```python
def hello():  # 第1行
    print("hello")  # 第2行

hello()  # 第3行
```
```

在 GitHub 渲染时，行号会显示在代码块的左侧，方便引用特定行（"看第3行"）。

### 行高亮（Line Highlighting）

GFM 扩展了**行高亮**语法，通过 `meta` 字符串指定：

````markdown
```python {3,5-7}
def greet(name):
    greeting = f"Hello, {name}!"  # 第3行，高亮
    print(greeting)  # 第4行，不高亮
    return greeting  # 第5行，高亮
    # 第6-7行也会高亮
```
````

`{3,5-7}` 是 meta 字符串，告诉高亮库高亮第 3 行和第 5-7 行。

**注意**：这需要渲染器（如 VitePress、Docusaurus）和高亮库（如 Shiki）都支持该扩展。

## 设计取舍

**为什么语法高亮不集成在 Markdown 规范中？**

因为高亮是**展示层**的事，而 Markdown 规范关注的是**语义层**。同样的 Python 代码，在深色主题下应该用浅色文字，在浅色主题下应该用深色文字——主题变化不应该改变 Markdown 源码。把高亮交给生态库，让 Markdown 保持语言无关性。

**三反引号 vs 三波浪线？**

反引号更常见（键盘上直接有），所以是默认选择。波浪线是"逃生舱口"——当反引号不够用时启用。

**为什么缩进代码块不推荐？**

可读性差。缩进代码块在源码中"隐身"，不像栅栏代码块有明显的开始和结束标记。在团队协作中，这会导致大量的格式混淆。

## 动手练习

1. 用三种不同的代码块语法（三反引号、三波浪线、4空格缩进）写同一个代码片段，比较渲染结果
2. 尝试用不存在的语言标识（如 ````xyz`），看看渲染器如何处理
3. 思考题：为什么 Markdown 不直接支持行号？（提示：行号是展示层的概念，和语义无关）

## 本章小结

这一章中我们学习了 Markdown 的代码块语法。首先介绍了栅栏代码块的两种形式（三反引号、三波浪线），解释了语言标识的作用和语法高亮的完整渲染流程（Markdown → AST → 高亮库 → HTML），然后了解了缩进代码块为何不推荐使用，最后介绍了 GFM 的行号和行高亮扩展。

本章我们一起学习了以下概念：

| 概念 | 解释 |
|------|------|
| 栅栏代码块 | 三反引号或三波浪号包裹的多行代码 |
| 语言标识 | 代码块后的语言名，如 `python`，供高亮库使用 |
| 缩进代码块 | 4空格缩进，已不推荐 |
| Shiki | 基于 TextMate 语法的现代高亮库，VS Code 同款 |
| 行高亮 | `{3,5-7}` 语法，GFM 扩展 |

下一章中，我们将学习 Markdown 的表格语法——如何在纯文本中构建结构化的数据。
