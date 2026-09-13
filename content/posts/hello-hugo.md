---
title: "你好，Hugo"
date: 2026-09-13T00:30:00+08:00
tags: ["Hugo", "GitHub Pages"]
author: "李佳悦"
---

这是本站的第一篇文章，用来把「写作 → 提 PR → 合并 → 自动部署」这条链路走通。

## 这个站点是怎么搭起来的

- **静态站点生成**：Hugo（扩展版），构建速度极快，单页渲染毫秒级
- **托管**：GitHub Pages，源码放在 `main` 分支
- **自动化**：每次合并到 `main`，GitHub Actions 自动拉代码、装 Hugo、构建 `public/`、发布到 Pages
- **写作**：文章放在 `content/posts/`，Markdown 格式，frontmatter 四个字段

## 发布流程

```bash
git checkout -b post/<slug>
# 写 content/posts/<slug>.md
git add . && git commit -m "post: <slug>"
git push -u origin post/<slug>
# 开 PR → 合并到 main → 自动部署
```

## 文章头信息

```yaml
---
title: "文章标题"
date: 2026-09-13T00:30:00+08:00
tags: ["Hugo", "GitHub Pages"]
author: "李佳悦"
---
```

四个字段，不多不少。剩下的交给 Markdown 就行了。
