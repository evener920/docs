# 我的知识库 Wiki（MkDocs Material）

一个**直接拖 Markdown 就能上线**的漂亮 Wiki：自动分类、标签、搜索、深色模式，
推送到 GitHub 后自动部署到 GitHub Pages。

## 目录结构

```
wiki/
├── mkdocs.yml              # 站点配置（一般不用改）
├── requirements.txt        # Python 依赖
├── .github/workflows/
│   └── deploy.yml          # 推送即自动部署到 GitHub Pages
└── docs/                   # ← 你的所有内容都放这里
    ├── .pages              # 侧边栏排序（根目录）
    ├── index.md            # 首页
    ├── tags.md             # 标签页（自动生成，勿手改内容）
    ├── research-notes/     # 分类①：研究笔记
    │   ├── .pages          #   设中文标题
    │   └── rag-publishing.md
    ├── reading-notes/      # 分类②：读书笔记
    │   ├── .pages
    │   └── tang-history.md
    └── project-docs/       # 分类③：项目文档
        ├── .pages
        └── journal-founding.md
```

## 一、日常用法：拖 md 即生效

1. **加分类**：在 `docs/` 下新建文件夹（建议英文目录名），里面放一个 `.pages` 写中文标题：
   ```yaml
   title: 我的分类
   ```
2. **加文章**：把 `.md` 丢进对应文件夹，头部加标题和标签：
   ```markdown
   ---
   title: 文档标题
   tags: [标签A, 标签B]
   ---

   正文……
   ```
3. **改顺序/折叠**：编辑各层 `.pages` 的 `arrange` 列表。

> 不用改 `mkdocs.yml`，侧边栏和标签页会自动更新。

## 二、本地预览（可选）

```bash
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
mkdocs serve        # 打开 http://localhost:8000
```

## 三、部署到 GitHub Pages

### 1. 在 GitHub 新建仓库（网页或命令行）

```bash
# 没有 gh 的话先装：brew install gh，再 gh auth login
cd wiki
git init
git add -A
git commit -m "init wiki"
git branch -M master
git remote add origin https://github.com/evener920/wikis.git
git push -u origin master        # 若仓库已存在且内容要覆盖，用 git push -f origin master
```

### 2. 开启 Pages

仓库 → **Settings → Pages → Build and deployment → Source 选 "GitHub Actions"**。
（不用选 branch，我们的 workflow 会自己部署。）

### 3. 之后每次更新

```bash
git add -A && git commit -m "更新" && git push
```

GitHub Actions 会自动构建并发布，几分钟后访问：
`https://<你的用户名>.github.io/<仓库名>/`

## 四、个性化（改 mkdocs.yml）

- `site_name` / `site_url` / `repo_url`：改成你自己的
- `theme.palette`：`primary` / `accent` 换主题色（如 `blue` `green` `red`）
- `features`：开关各种功能（代码复制、搜索联想等）
- 更多主题：https://squidfunk.github.io/mkdocs-material/

## 常见问题

- **标签不显示？** 确认每篇 md 头部有 `tags:` 且 `mkdocs.yml` 里启用了 `tags` 插件。
- **中文目录名乱码？** 用英目录名 + `.pages` 的 `title:` 设中文显示名（已示范）。
- **部署失败？** 检查仓库 Settings → Actions 权限，以及 Pages Source 是否选了 GitHub Actions。
