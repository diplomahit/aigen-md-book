# 第15章 编写生产级 Markdown —— 团队规范与工程化

## 学习目标

1. 掌握生产级 Markdown 的核心规范
2. 了解 Markdown Linter 的使用
3. 能将 Markdown 集成到 CI/CD 流程中

## 生活类比

生产级 Markdown 就像餐厅的后厨规范——每个厨师（作者）写的菜（文章）都必须遵循同样的标准：食材命名（文件命名）、刀工大小（缩进格式）、摆盘规则（标题层级）、出餐检查（Lint 和 CI）。这样不管是哪个厨师做的菜，顾客（读者）都能吃到一致的品质。

![生产级 Markdown 类比：后厨规范=团队标准，厨师=作者，出餐检查=Lint 和 CI](assets/images/03-chapter15-kitchen-standards.png)

## 团队 Markdown 规范

### 文件命名

```yaml
# 推荐：kebab-case
getting-started.md
api-reference.md
troubleshooting.md

# 不推荐：空格或大驼峰
getting started.md    # 空格在 URL 中编码为 %20
GettingStarted.md    # 不直观，不易排序
```

**规则**：使用小写 kebab-case（短横线分隔），无空格，无大写字母。

### 标题层级

```yaml
# 推荐：严格遵循 H1 → H2 → H3 层级
# H1：页面标题（每篇文章只有一个）
# H2：章节
# H3：子节
# H4：小节（必要时）

# 不推荐：跳级
# H1
# H3  ← 跳过 H2
# H1  ← 另一个 H1
```

**规则**：每篇文章一个 H1，H 层级不得跳级。

### 图片规范

```yaml
# 推荐：
- 图片路径使用相对路径
- 所有图片都有 alt 文本
- 图片命名使用 kebab-case
- 图片放在 assets/ 目录下

# 不推荐：
- 绝对路径引用图片
- alt 文本缺失
- 中文命名图片（如 截图.png）
- 图片散落在各处
```

### 链接规范

```yaml
# 推荐：
- 使用相对链接（站内链接）
- 外部链接使用完整 URL
- 定期检查死链

# 不推荐：
- 使用站根路径（如 /docs/getting-started）
- 手动维护链接列表
- 长链接不折叠
```

### 代码块规范

```yaml
# 推荐：
- 所有代码块标注语言
- 代码示例可运行（测试通过）
- 代码行不超过 80 字符

# 不推荐：
- 不带语言标识的代码块
- 截图代替代码
- 伪代码不说明
```

## Markdown Linter：markdownlint

### 安装

```bash
# 全局安装
npm install -g markdownlint-cli

# 或在项目中
npm install markdownlint-cli --save-dev
```

### 配置

```json
// .markdownlint.json
{
  "MD013": { "line_length": 120 },
  "MD024": false,
  "MD033": false,
  "default": true
}
```

### 常见规则

| 规则 | 检查项 | 说明 |
|------|--------|------|
| MD001 | 标题层级 | 确保标题层级正确 |
| MD003 | 标题风格 | 确保标题风格一致 |
| MD007 | 列表缩进 | 列表缩进 2 空格 |
| MD013 | 行长度 | 每行不超过指定字符数 |
| MD024 | 重复标题 | 标题不重复 |
| MD025 | 单 H1 | 每个文件一个 H1 |
| MD026 | 尾部分隔符 | 标题后不能有分隔符 |
| MD029 | 有序列表序号 | 有序列表序号连续 |
| MD033 | 内联 HTML | 禁止内联 HTML（可选） |
| MD041 | 首行 H1 | 首行必须是 H1 |

### 使用

```bash
# 检查所有 Markdown 文件
markdownlint "*.md"

# 检查特定文件
markdownlint docs/README.md

# 自动修复（部分规则）
markdownlint --fix "*.md"

# 输出 JSON 格式（CI 友好）
markdownlint --json "*.md"
```

![Markdown Linter 工作流：文件通过 Lint 检查，绿色通过，红色不通过](assets/images/04-chapter15-linter-workflow.png)

## CI/CD 集成

### GitHub Actions

```yaml
# .github/workflows/lint-markdown.yml
name: Lint Markdown

on:
  pull_request:
    branches: [main]
    paths:
      - '**.md'

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
      - run: npm ci
      - run: npx markdownlint-cli2 "**/*.md"
```

### 检查流程

```
开发者提交 PR
  → CI 触发
  → markdownlint 检查所有 .md 文件
  → 有错误？→ PR 被拒绝，提示修复
  → 通过？→ PR 合并
```

### 完整的 CI/CD 流水线

```yaml
# .github/workflows/deploy-docs.yml
name: Deploy Docs

on:
  push:
    branches: [main]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
      - run: npm ci
      - run: npx markdownlint-cli2 "**/*.md"

  build:
    needs: lint
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
      - run: npm ci
      - run: npm run build
      - uses: actions/upload-pages-artifact@v3
        with:
          path: .vitepress/dist

  deploy:
    needs: build
    runs-on: ubuntu-latest
    permissions:
      pages: write
      id-token: write
    steps:
      - uses: actions/deploy-pages@v4
```

这是一个**三阶段流水线**：

```
lint（检查） → build（构建） → deploy（部署）
  串行           串行
```

每个阶段有明确的职责：lint 确保代码质量，build 生成静态站，deploy 发布上线。任何一个阶段失败，后续步骤都不会执行。

![CI/CD 流水线：lint 检查 → build 构建 → deploy 部署三阶段串行](assets/images/05-chapter15-cicd-pipeline.png)

## 生产级 Markdown 规范清单

以下是团队 Markdown 规范的完整清单，可直接作为团队的 CONTRIBUTING.md：

### 必选规范

1. **文件命名**：kebab-case，无空格
2. **标题层级**：每篇文章一个 H1，H1 → H2 → H3，不跳级
3. **代码块**：所有代码块标注语言，不超过 80 字符
4. **图片**：使用相对路径，所有图片有 alt 文本
5. **链接**：站内使用相对链接，外部使用完整 URL
6. **Lint**：使用 markdownlint，配置在 CI 中强制执行

### 可选规范

1. **Front Matter**：所有文章使用 YAML Front Matter
2. **章节长度**：每个章节不超过 1000 字
3. **语言风格**：使用"你"称呼读者，不缩写
4. **日期格式**：YYYY-MM-DD
5. **引用规范**：引用块使用 `>`，不嵌套超过 3 层

### 建议规范

1. **表格**：每列不超过 10 行
2. **链接文字**：使用有意义的文字，不使用"点击这里"
3. **代码示例**：确保可运行，定期更新
4. **图片格式**：WebP / SVG 优先，PNG 其次，JPEG 用于照片
5. **目录**：超过 5 个章节的文章自动生成 TOC

![生产级 Markdown 规范清单：必选可选建议三类规则](assets/images/06-chapter15-checklist.png)

## 设计取舍

**为什么要有 Markdown 规范？**

因为 Markdown 的灵活性是一把双刃剑——同样的内容可以用不同的格式写出来，导致文档风格不统一、可读性不一致。规范不是限制创作自由，而是让团队协作更高效。

**为什么用 Lint 而不是人工审查？**

人工审查 Markdown 格式容易遗漏（比如漏掉一个缺 alt 的图片），且疲劳后会下降。Lint 是**自动的、一致的、零成本的**——它只检查格式，不检查内容质量。

**规范越多越好吗？**

不是。规范的**复杂度应该与团队规模成正比**：

| 团队规模 | 规范数量 |
|----------|----------|
| 1-2 人 | 5 条核心规范 |
| 3-10 人 | 10 条核心 + 5 条可选 |
| 10+ 人 | 全部规范 + Lint 强制执行 |

规范的核心原则是：**越少越好，但不可少**。

![设计取舍：为什么要有规范、为什么用 Lint、规范不是越多越好](assets/images/07-chapter15-design-tradeoffs.png)

## 动手练习

1. 在项目中安装 markdownlint，运行 `markdownlint-cli2 "**/*.md"` 检查你的文档
2. 在 GitHub Actions 中配置 markdownlint 检查，提交一个违反规范的 Markdown 文件观察 CI 反应
3. 思考题：为什么规范要有"必选"和"可选"之分？

## 本章小结

这一章中我们学习了生产级 Markdown 的规范化和工程化。首先介绍了文件命名、标题层级、图片、链接、代码块五大核心规范，然后讲解了 markdownlint 的安装配置和常见规则，接着设计了完整的 CI/CD 流水线（lint → build → deploy），最后给出了完整的团队 Markdown 规范清单。

本章我们一起学习了以下概念：

| 概念 | 解释 |
|------|------|
| 文件命名 | kebab-case，无空格，无大写字母 |
| markdownlint | Markdown Linter，自动检查格式规范 |
| CI/CD 集成 | 在 GitHub Actions 中自动化检查 |
| 规范清单 | 必选 + 可选 + 建议，与团队规模匹配 |
| 设计规范 | 越少越好，但不可少 |

![本章知识回顾：后厨规范、Lint 工作流、CI/CD 流水线、规范清单](assets/images/08-chapter15-summary.png)

## 全书总结

这本《Markdown 完全指南》从直觉到进阶，从生态到实战，系统地讲解了 Markdown 的方方面面：

- **篇一（第1-3章）**：Markdown 的基础语法——行内元素和块级元素
- **篇二（第4-7章）**：Markdown 的进阶语法——代码块、表格、图片、脚注
- **篇三（第8-11章）**：Markdown 的生态——引擎架构、JS/Python 工具链、命令行工具
- **篇四（第12-15章）**：Markdown 的实战——博客、文档站、极限扩展、生产规范

感谢阅读。愿你写出的每一篇 Markdown，都能被优雅地渲染。
