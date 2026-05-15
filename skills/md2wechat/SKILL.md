---
name: md2wechat
description: Convert Markdown to WeChat-compatible HTML with inline CSS.
---

当用户要求将 Markdown 转为微信公众号 HTML 时，运行 `md2wechat` CLI：

```bash
md2wechat <input> -o <output> -s <style>
```

- 默认样式 `default`（微信绿），用户明确要 GitHub 风格时才用 `github`
- 输出路径默认 `<input stem>.html`
- 转换完成后告知用户输出路径
