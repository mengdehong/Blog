+++
title = 'Hugo'
date = "2025-10-17T09:47:45+08:00"
draft = false
tags = ["Hugo"]
categories = ["工程技术"]
summary = "一份精炼的 Hugo 博客日常维护指南，覆盖从创建文章到部署上线的核心命令与关键知识点。"
+++

本文档旨在提供维护 Hugo 博客所需的核心知识，用于快速回顾。

## 1. 核心理念：工厂 vs 成品

- **项目文件夹**: 这是**工厂**，存放所有原材料（Markdown, 配置文件）。所有工作在此进行。
- **`public/` 目录**: 这是**商店**，存放最终生成的 HTML 成品网站。此目录内容会被 `hugo` 命令反复覆盖，切勿手动修改。

---

## 2. 日常写作流程

#### a. 创建新文章
在博客根目录运行：
```bash
hugo new content posts/article-name.md
```
hugo根据 `archetypes/default.md` 模板创建新文章。

#### b. 修改 Front Matter (文章元数据)
打开新创建的 `.md` 文件，编辑顶部的 `+++` 区域。
```toml
+++
title = "文章标题"
date = "2024-10-26T15:00:00+08:00"
draft = false   #  <-- 发布前必须改为 false
categories = ["分类一", "分类二"]
tags = ["标签一", "标签二"]
summary = "文章摘要，会显示在列表页"
+++
```

#### c. 添加图片
1.  将图片（如 `my-image.png`）放入项目根目录的 `static/images/` 文件夹下。
2.  在 Markdown 中使用**根路径**引用：
    ```markdown
    ![图片描述](/images/my-image.png)
    ```

#### d. 本地实时预览
```bash
hugo server -D
```
- `-D` 参数会构建草稿文章。
- 在浏览器打开 `http://localhost:1313` 查看效果。

---

## 3. 网站配置与定制

- **主配置文件**: `hugo.toml` 是网站的“控制中心”。
- **常见修改**:
  - `baseURL`: 部署上线时，必须修改为最终域名。
  - `title`, `[params].author`: 修改网站标题和作者名。
  - `[[menu.main]]`: 管理顶部导航菜单。
  - `[[params.socialIcons]]`: 配置社交媒体链接。
- **深入定制**: 查看 [PaperMod 主题官方文档](https://adityatelange.github.io/hugo-PaperMod/) 探索更多功能。

---

## 4. 维护与更新

#### a. 版本控制 (Git)
每次完成修改后，提交工作作为备份。
```bash
git add .
git commit -m "清晰的修改说明 (例如: 发表新文章《...》)"
```

#### b. 更新 Hugo 程序
从 [Hugo Releases](https://github.com/gohugoio/hugo/releases) 下载最新的 `hugo_extended_..._linux-amd64.deb` 包并安装。

#### c. 更新 PaperMod 主题
```bash
git submodule update --remote --merge
```

---

## 5. 上线部署

#### a. 生成最终网站
```bash
hugo
```
此命令会将最终成品生成到 `public/` 目录。

#### b. 推荐部署平台 (自动化)
- **GitHub Pages**: 免费，经典的静态托管方案。

---

## 常用命令速查

| 命令                               | 作用                          |
| :--------------------------------- | :---------------------------- |
| `hugo new content posts/file.md`   | 创建一篇新文章                |
| `hugo server -D`                   | **启动本地预览 (含草稿)**     |
| `hugo`                             | 生成最终网站到 `public/` 目录 |
| `git add . && git commit -m "..."` | 保存修改记录                  |
| `git submodule update --remote`    | 更新主题                      |