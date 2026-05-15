# md2wechat

[中文](README.zh-CN.md)

Convert Markdown files to WeChat Official Account (微信公众号) compatible HTML, with CSS inlining so styles survive WeChat's editor.

## Features

- GFM extensions: tables, fenced code blocks, TOC
- **Syntax highlighting** via Pygments (500+ languages)
- **Style themes**: `default` (WeChat green) and `github` — switch with `-s`
- **CSS inlining** via css-inline (Rust) — WeChat strips `<style>` tags, only inline styles work
- Auto-extracts first H1 as page title
- Responsive images, code block horizontal scroll, zebra-striped tables

## Requirements

Python >= 3.12. Uses [uv](https://docs.astral.sh/uv/) for dependency management.

## Installation

### Install as a global CLI tool

```bash
uv tool install git+https://github.com/liuliqiu/md2wechat
# or from a local clone:
uv tool install .
```

After install, `md2wechat` is available anywhere:

```bash
md2wechat input.md
md2wechat input.md -o output.html -s github
```

### Install for development

```bash
git clone https://github.com/liuliqiu/md2wechat
cd md2wechat
uv sync
uv pip install -e .
```

## CLI usage

```bash
md2wechat input.md                 # default style → input.html
md2wechat input.md -o output.html  # custom output path
md2wechat input.md -s github       # GitHub theme
md2wechat --help                   # list all options
```

## Claude Code & OpenCode skill

This repo includes a skill file at `skills/md2wechat/SKILL.md`. Copy it into your local skills directory to enable `/md2wechat` as a slash command.

### Claude Code

```bash
# Project-level (only this project)
cp -r skills/ .claude/skills/

# Or global (all projects)
cp -r skills/ ~/.claude/skills/
```

### OpenCode

```bash
# Project-level
cp -r skills/ .opencode/skills/

# Or global
cp -r skills/ ~/.opencode/skills/
```

### Usage

Once the skill is in place and `md2wechat` CLI is installed, invoke with:

```
/md2wechat article.md
```

Or ask in natural language:

> 把这篇文章转成微信公众号 HTML
>
> Convert this markdown article to WeChat HTML

Claude will run `md2wechat` with the right arguments based on context.

## How it works

```
Markdown file
    │
    ▼
Read UTF-8 text → Extract title (first H1 or filename)
    │
    ▼
markdown.markdown() → tables, fenced_code, codehilite, TOC
    │
    ▼
Embed HTML template → <style> with full CSS + Pygments theme
    │
    ▼
css_inline.CSSInliner() → CSS rules → inline style="" attributes
    │
    ▼
WeChat-compatible HTML file
```

WeChat's article editor strips `<style>` tags and external stylesheets — only inline styles survive, so CSS inlining is the critical step.

## Project structure

```
├── md2wechat/
│   ├── __init__.py       # package entry, re-exports CLI
│   ├── cli.py            # CLI + conversion logic
│   └── styles/
│       ├── __init__.py   # style registry
│       ├── default.py    # WeChat green theme
│       └── github.py     # GitHub theme
├── skills/
│   └── md2wechat/
│       └── SKILL.md      # skill definition (Claude Code / OpenCode)
├── pyproject.toml
└── uv.lock
```

## Dependencies

| Package | Role |
|---------|------|
| [markdown](https://python-markdown.github.io/) | Markdown → HTML (tables, fenced_code, codehilite, toc) |
| [css-inline](https://github.com/Stranger6667/css-inline) | Rust-powered CSS inlining |
| [pygments](https://pygments.org/) | Syntax highlighting (via codehilite) |
