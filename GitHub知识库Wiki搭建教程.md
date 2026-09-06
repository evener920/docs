# 零基础搭建漂亮的知识库 Wiki（拖 md 即上线）

> 一份从 0 到 1 的实战教程。你只需要会写 Markdown，剩下的分类、标签、搜索、深色模式、自动部署，全帮你配好。
> 本教程基于真实踩坑写成（GitHub Pages 部署环节的 5 个大坑都附了避坑法），照做即可一次跑通。

---

## 一、这套方案能给你什么

| 你的需求 | 本方案的解法 |
|---------|------------|
| 文件都是 `.md` | 直接把 md 丢进文件夹，无需转换 |
| 要**分类** | 用 `docs/` 下的**文件夹**当分类（中文标题用 `.pages` 配置） |
| 要**标签** | 每篇 md 头部写 `tags: [...]` → 自动聚合出标签页 |
| 要**漂亮** | Material 主题：浅/深色一键切换、全文搜索、代码复制、折叠目录 |
| 要**直接拖 md 就上线** | 用 `awesome-pages` 插件，新增文件不用改任何配置，侧边栏自动同步 |
| 不想装环境运维 | push 到 GitHub 后，**Actions 自动构建发布到 Pages** |

**技术栈**：`MkDocs` + `Material for MkDocs` 主题 + `awesome-pages` 插件 + `tags` 插件 + `GitHub Actions` 自动部署。

---

## 二、目录结构（脚手架）

```
wiki/
├── mkdocs.yml                  # 站点总配置（主题/插件/功能都在这里）
├── requirements.txt            # Python 依赖清单
├── .gitignore
├── README.md
├── .github/
│   └── workflows/
│       └── deploy.yml          # 自动部署流水线（push 即上线）
└── docs/                       # ← 你的内容都放这里
    ├── .pages                  # 根目录导航顺序（分类排序）
    ├── index.md                # 首页
    ├── tags.md                 # 标签页（自动聚合，留空即可）
    ├── research-notes/         # 分类1：研究笔记
    │   ├── .pages              #    分类中文标题
    │   └── rag-publishing.md   #    一篇带标签的文章
    ├── reading-notes/          # 分类2：读书笔记
    │   ├── .pages
    │   └── tang-history.md
    ├── project-docs/           # 分类3：项目文档
    │   ├── .pages
    │   └── journal-founding.md
    └── 诗词/                    # 分类4：诗词
        ├── .pages
        └── index.md
```

> **命名铁律**：分类文件夹用**英文或拼音**目录名（如 `research-notes`），中文显示名写在里面的 `.pages`（`title: 研究笔记`）。这样 URL 不会乱码、便于分享。

---

## 三、核心配置 `mkdocs.yml`

这是整站的心脏。复制下面这份即可用（已按真实环境校验）：

```yaml
# ============================================================
# MkDocs Material Wiki 配置
# 拖入 .md 即可，无需手动维护导航（awesome-pages 自动生成）
# ============================================================

site_name: 我的知识库 Wiki
site_description: 基于 Markdown 的分类 / 标签知识库，部署于 GitHub Pages
site_url: https://evener920.github.io/wikis/      # ← 改成你的：https://<用户名>.github.io/<仓库名>/

# 仓库信息（用于右上角编辑/查看源码按钮）
repo_url: https://github.com/evener920/wikis     # ← 改成你的仓库
repo_name: wikis
edit_uri: edit/main/docs/                         # ← 默认分支是 main 就写 main；master 就写 master

theme:
  name: material
  language: zh
  palette:
    # 浅色（默认）
    - scheme: default
      primary: indigo
      accent: indigo
      toggle:
        icon: material/weather-night
        name: 切换到深色
    # 深色
    - scheme: slate
      primary: indigo
      accent: indigo
      toggle:
        icon: material/weather-sunny
        name: 切换到浅色
  features:
    - navigation.instant      # 瞬时跳转
    - navigation.tracking     # 滚动高亮当前章节
    - navigation.top          # 返回顶部
    - content.code.copy       # 代码块复制按钮
    - search.suggest          # 搜索联想
    - search.highlight        # 搜索结果高亮

# 插件：搜索 + 标签 + 自动导航
plugins:
  - search
  - tags:                     # 标签页：每篇 md 头部写 tags: [...] 即可
      tags_file: tags.md
  - awesome-pages            # 按文件夹结构自动生成侧边导航（拖 md 即生效）

# Markdown 增强
markdown_extensions:
  - admonition
  - pymdownx.details
  - pymdownx.superfences
  - pymdownx.tabbed:
      alternate_style: true
  - pymdownx.highlight:
      anchor_linenums: true
  - pymdownx.inlinehilite
  - pymdownx.snippets
  - attr_list
  - md_in_html
  - toc:
      permalink: true

extra:
  generator: false
```

**`site_url` 必须带末尾斜杠和子路径**：部署在 `https://用户名.github.io/仓库名/` 这种「项目页」时，漏掉 `/仓库名/` 会导致 CSS/JS 路径错误、页面裸奔。

---

## 四、分类：用 `.pages` 控制顺序与中文名

每个分类文件夹里放一个 `.pages`，只需写中文标题：

```yaml
# docs/research-notes/.pages
title: 研究笔记
```

根目录 `docs/.pages` 控制**侧边栏的整体顺序**，`arrange:` 里的每一项都必须真实存在，否则构建直接报错：

```yaml
# docs/.pages
arrange:
  - index.md
  - research-notes
  - reading-notes
  - project-docs
  - 诗词
  - tags.md
collapse: false
```

> ⚠️ **致命坑**（见第八节坑2）：`arrange` 里列了 `诗词`，但 `docs/诗词/` 文件夹不存在 → 整站构建失败、站点停更。增删分类一定要同步这个文件。

---

## 五、标签：每篇 md 头部加 `tags`

```markdown
---
title: RAG 知识库赋能专业出版
tags: [RAG, 出版, 论文, 知识库]     # ← 这些标签会被自动汇总到「标签总览」
---

正文写在这里……

本文头部的标签会被自动汇总到 [标签总览](../../tags.md) 页面，
点进去能看到所有带该标签的文章。
```

构建后自动生成 `tags/` 页，按标签聚合全部文章。

---

## 六、本地预览（先看效果）

```bash
cd wiki
python3 -m venv .venv && . .venv/bin/activate
pip install -r requirements.txt
mkdocs serve -a 127.0.0.1:8000
```

浏览器打开 `http://127.0.0.1:8000`。

> ⚠️ **预览两个坑**（见第八节坑5）：① 项目页模式下站点挂在 `http://127.0.0.1:8000/wikis/`，根路径会 302 跳转过去，直接看根像「没效果」；② `serve` 会**缓存 `mkdocs.yml`**，改了核心配置要**杀掉旧进程重启**才生效。

---

## 七、部署到 GitHub Pages（完整实操）

### 7.1 建仓库
在 GitHub 网页新建仓库（如 `wikis`，公开）。本项目页地址即 `https://<用户名>.github.io/wikis/`。

### 7.2 准备 PAT（个人访问令牌）
> **真实环境提示**：如果你的网络封了 SSH 22 端口（表现为 `Connection closed by 198.18.1.0 port 22`），就**改走 HTTPS + PAT**，不要死磕 SSH。

1. 打开 https://github.com/settings/tokens （选 **Classic token**）
2. 勾选范围 **`repo`**（展开后整组勾上，**务必包含 `workflow`** —— 因为首次推送包含 workflow 文件，缺这个权限会被拒）
3. 设过期时间（7 天足够，推完可删）
4. 复制 `ghp_...` 开头的 token

### 7.3 本地提交并推送
```bash
cd wiki
git init
git add -A
git commit -m "init wiki"
git branch -M main                 # 默认分支用 main（或你仓库的实际默认分支）
git remote add origin https://github.com/<用户名>/wikis.git
git push -f origin main            # 首次用 -f 覆盖远程旧结构
```
推送时若弹用户名 → 填 GitHub 用户名；密码 → **粘贴 PAT**（屏幕不显示，正常）。

### 7.4 自动部署 workflow（`.github/workflows/deploy.yml`）

本项目用 `mkdocs gh-deploy` 把构建产物推到 `gh-pages` 分支，再由 Pages 从该分支发布：

```yaml
name: 部署 Wiki 到 GitHub Pages

on:
  push:
    branches:
      - main          # 默认分支是 main，push 到 main 即自动部署
  workflow_dispatch:   # 允许在 GitHub 网页手动触发

permissions:
  contents: write     # mkdocs gh-deploy 需要写权限来推送 gh-pages 分支

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: 检出代码
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: 配置 Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.x"

      - name: 安装依赖
        run: pip install -r requirements.txt

      - name: 构建并部署到 gh-pages
        run: mkdocs gh-deploy --force --verbose
```

> **两种 Pages 模式二选一，别混**：
> - 本教程用 **「Deploy from a branch」** 模式 + `gh-deploy` → 源设为 `gh-pages` 分支。
> - 若你选 **「GitHub Actions」** 源，则 `deploy.yml` 必须改成 build→upload-pages-artifact→deploy-pages 的官方写法（`gh-deploy` 与它冲突，会导致站点不更新）。

### 7.5 设置 Pages 源
仓库 **Settings → Pages → Source** 选择：
- **Deploy from a branch** → Branch 选 `gh-pages`，目录 `/ (root)`
- 保存后等 1–2 分钟，刷新 `https://<用户名>.github.io/wikis/` 即可看到站点。

---

## 八、踩坑实录（血泪 5 坑，照抄可避）

| # | 现象 | 根因 | 解法 |
|---|------|------|------|
| **坑1** | push 后站点不更新、Actions 没动静 | workflow 监听 `main`，但仓库默认分支是 `master`（或反过来） | `deploy.yml` 的 `branches` 和 `edit_uri` 必须与**仓库真实默认分支**一致。先 `git branch -r` 或 GitHub 网页确认默认分支 |
| **坑2** | 加了分类后整站构建失败、页面停更 | `docs/.pages` 的 `arrange` 列了不存在的文件夹 | `arrange` 每项必须真实存在；新分类先建好文件夹（含 `.pages`），再列进来 |
| **坑3** | `Connection closed by 198.18.1.0 port 22` | 网络把 `github.com` 解析到 `198.18.1.0` 并**封了 22 端口**，SSH 走不通 | 改用 **HTTPS + PAT**（`git remote set-url origin https://github.com/...`），443 端口是通的 |
| **坑4** | 部署跑成功，但线上还是旧页面 | Pages 源选错 / 没切到 `gh-pages` | 用 `gh-deploy` 方案就把 Pages 源设成 `gh-pages`；用 Actions 源就别用 `gh-deploy` |
| **坑5** | 本地预览「没效果」、页面裸奔无样式 | ① 项目页下 serve 挂在 `/仓库名/` 子路径；② serve 缓存了旧 `mkdocs.yml` | 访问 `http://127.0.0.1:8000/仓库名/`；改了核心配置**杀掉旧 serve 进程重启** |

**额外注意（PAT 权限）**：首次推送包含 `.github/workflows/` 文件时，PAT 必须带 `workflow` 作用域，否则报 `refusing to allow ... to create or update workflow`。

---

## 九、日常使用三步法

以后加内容，只需 3 步，全程不用碰配置：

1. **放文件**：把 `.md` 丢进 `docs/对应分类/` 文件夹（新建分类就建 `docs/新分类/` + 一个 `.pages` 写 `title:`）。
2. **加标签**：md 头部写 `tags: [标签A, 标签B]`。
3. **推上线**：`git add -A && git commit -m "更新" && git push origin main` → Actions 自动部署，约 1 分钟上线。

一篇标准文章模板：
```markdown
---
title: 文档标题
tags: [标签A, 标签B]
---

# 正文

写你的内容……
```

---

## 十、速查命令

```bash
# 本地预览
mkdocs serve -a 127.0.0.1:8000

# 本地构建校验（不要用 --strict，tags_file 弃用警告会误判失败）
mkdocs build

# 提交并上线
git add -A && git commit -m "更新" && git push origin main

# 远程改 HTTPS（SSH 不通时）
git remote set-url origin https://github.com/<用户名>/<仓库名>.git
```

---

## 附录：依赖 `requirements.txt`

```
mkdocs>=1.6
mkdocs-material>=9.5
mkdocs-awesome-pages-plugin>=2.9
```

---

> **这份教程本身也可以变成你 Wiki 里的一页**：把它存成 `docs/wiki-tutorial.md`，下次 push 就会出现在你的网站上。祝搭建顺利 🐘
