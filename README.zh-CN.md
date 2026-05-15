# md2wechat

将 Markdown 文件转换为微信公众号兼容的 HTML，通过 CSS 内联确保样式在微信编辑器中正常显示。

[English](README.md)

## 特性

- 支持 GFM 扩展：表格、围栏代码块、目录
- **代码语法高亮**，基于 Pygments（支持 500+ 语言）
- **多种样式主题**：`default`（微信绿）和 `github`（GitHub 风格），通过 `-s` 切换
- **CSS 内联**，基于 css-inline（Rust）——微信编辑器会丢弃 `<style>` 标签，只有内联样式才能生效
- 自动提取首个一级标题作为页面标题
- 响应式图片、代码块横向滚动、表格斑马条纹

## 环境要求

Python >= 3.12。使用 [uv](https://docs.astral.sh/uv/) 管理依赖。

## 安装

### 安装为全局 CLI 工具

```bash
uv tool install git+https://github.com/liuliqiu/md2wechat
# 或从本地仓库安装：
uv tool install .
```

安装后，`md2wechat` 可在任意路径下使用：

```bash
md2wechat input.md
md2wechat input.md -o output.html -s github
```

### 开发环境安装

```bash
git clone https://github.com/liuliqiu/md2wechat
cd md2wechat
uv sync
uv pip install -e .
```

## CLI 用法

```bash
md2wechat input.md                 # 默认样式 → input.html
md2wechat input.md -o output.html  # 指定输出路径
md2wechat input.md -s github       # GitHub 主题
md2wechat --help                   # 查看所有选项
```

## Claude Code & OpenCode 技能

本仓库提供技能文件 `skills/md2wechat/SKILL.md`。将其复制到本地的 skills 目录即可启用 `/md2wechat` 斜杠命令。

### Claude Code

```bash
# 项目级（仅当前项目生效）
cp -r skills/ .claude/skills/

# 或全局（所有项目生效）
cp -r skills/ ~/.claude/skills/
```

### OpenCode

```bash
# 项目级
cp -r skills/ .opencode/skills/

# 或全局
cp -r skills/ ~/.opencode/skills/
```

### 使用

安装 skill 和 `md2wechat` CLI 后：

```
/md2wechat article.md
```

也可用自然语言提问：

> 把这篇文章转成微信公众号 HTML
>
> Convert this markdown article to WeChat HTML

Claude 会根据上下文自动运行 `md2wechat` 并传入合适的参数。

## 工作原理

```
Markdown 文件
    │
    ▼
读取 UTF-8 文本 → 提取标题（首个 H1 或文件名）
    │
    ▼
markdown.markdown() 转换 → 表格 + 代码块 + 代码高亮 + 目录
    │
    ▼
嵌入 HTML 模板 → <style> 包含完整 CSS + Pygments 代码主题
    │
    ▼
css_inline.CSSInliner() → CSS 规则转为内联 style="" 属性
    │
    ▼
输出微信兼容的 HTML 文件
```

微信文章编辑器会丢弃 `<style>` 标签和外部样式表，只有内联样式才能生效，因此 CSS 内联是整个流程的核心步骤。

## 项目结构

```
├── md2wechat/
│   ├── __init__.py       # 包入口，导出 CLI
│   ├── cli.py            # CLI + 转换逻辑
│   └── styles/
│       ├── __init__.py   # 样式注册表
│       ├── default.py    # 微信绿色主题
│       └── github.py     # GitHub 风格主题
├── skills/
│   └── md2wechat/
│       └── SKILL.md      # 技能定义（Claude Code / OpenCode 通用）
├── pyproject.toml
└── uv.lock
```

## 依赖

| 包 | 说明 |
|---|------|
| [markdown](https://python-markdown.github.io/) | Markdown → HTML（tables / fenced_code / codehilite / TOC） |
| [css-inline](https://github.com/Stranger6667/css-inline) | 基于 Rust 的 CSS 内联引擎 |
| [pygments](https://pygments.org/) | 代码语法高亮（通过 codehilite 扩展集成） |