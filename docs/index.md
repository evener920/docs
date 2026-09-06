---
title: 首页
---

# 欢迎来到我的知识库 :material-book-open-page-variant:

这是一个用 **MkDocs Material** 搭建的 Wiki，所有内容都是 Markdown 文件。

## 它能做什么

- **分类**：用文件夹自动分类，侧边栏实时同步
- **标签**：每篇文档头部写 `tags: [...]`，自动汇总成标签页
- **搜索**：内置全文搜索，开箱即用
- **深色模式**：右上角一键切换
- **自动部署**：推送到 GitHub 后，自动发布到 Pages

## 怎么用（三步）

1. 在 `docs/` 下建文件夹 = 一个分类（建议英文目录名，用 `.pages` 设中文标题）
2. 把 `.md` 文件丢进对应文件夹，头部加上标题和标签：

   ```markdown
   ---
   title: 文档标题
   tags: [标签A, 标签B]
   ---

   正文写在这里……
   ```

3. `git push` —— GitHub Actions 会自动构建并上线

> 就这么简单，不需要改任何配置文件。

## 快捷入口

- 浏览 [标签总览](tags.md)
- 进入 [研究笔记](research-notes/rag-publishing.md)
- 进入 [读书笔记](reading-notes/tang-history.md)
- 进入 [项目文档](project-docs/journal-founding.md)
