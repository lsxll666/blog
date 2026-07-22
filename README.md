# blog

个人博客 — Hugo + GitHub Pages 静态站点。

## 技术栈
- [Hugo](https://gohugo.io/) 静态站点生成器（主题：ananke，git submodule）
- GitHub Pages 托管，合并到 `main` 分支后由 GitHub Actions 自动部署

## 写作与发布流程
1. 新建分支，在 `content/posts/` 下添加 Markdown 文章
2. frontmatter 必须包含四个字段：`title` / `date` / `tags` / `author`
3. 提交 PR，合并到 `main` 后自动构建并发布

## License
内容采用 [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/deed.zh-hans) 协议。
