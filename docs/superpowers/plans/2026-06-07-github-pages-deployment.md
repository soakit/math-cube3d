# GitHub Pages 自动部署 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 配置 GitHub Actions 自动构建，当 main 分支变更时，将静态文件提取并精简部署到 GitHub Pages。

**Architecture:** 使用 GitHub Pages 官方推荐的免分支 Actions 直接部署模式。在 Ubuntu 环境中拉取代码，把静态网页的核心资源文件（`index.html`、`css/` 和 `js/`）复制到临时构建目录 `dist/`，打包上传为 GitHub Pages 部署包，最终自动发布。

**Tech Stack:** GitHub Actions, Git, GitHub Pages APIs

---

### Task 1: 创建 GitHub Actions 工作流配置

**Files:**
- Create: `.github/workflows/deploy.yml`

- [ ] **Step 1: 创建配置文件 `.github/workflows/deploy.yml`**

新建 `.github/workflows/deploy.yml` 文件并写入以下完整的工作流定义：
```yaml
name: Deploy static content to Pages

on:
  push:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Pages
        uses: actions/configure-pages@v5

      - name: Prepare build artifact
        run: |
          mkdir -p dist/css dist/js
          cp index.html dist/
          cp -r css/* dist/css/
          cp -r js/* dist/js/

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: './dist'

      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

- [ ] **Step 2: 验证配置文件是否存在且内容正确**

运行：
`Get-Content .github/workflows/deploy.yml` (PowerShell) 或者在本地编辑器中查看，确认没有乱码或断行。

- [ ] **Step 3: 提交配置变更到 Git 暂存区并进行 commit**

运行：
```bash
git add .github/workflows/deploy.yml
git commit -m "feat: add GitHub Actions workflow for static site deployment to GitHub Pages"
```

---

### Task 2: 推送分支并执行 GitHub Pages 验证

**Files:**
- None (仅进行远程仓库推送与测试验证)

- [ ] **Step 1: 推送 main 分支到远程仓库**

运行：
`git push origin main`

- [ ] **Step 2: 检查 Actions 执行状态**

在浏览器中登录 GitHub，进入 `soakit/math-cube3d` 仓库的 `Actions` 栏，查看 "Deploy static content to Pages" 工作流是否成功运行。

- [ ] **Step 3: 验证部署后的静态网页**

在仓库的 `Settings` -> `Pages` 中，找到自动生成的站点 URL，访问页面并确认：
- 页面能正常打开并加载 CSS 和 JS。
- 3D 正方体折叠演示功能正常。
- 闯关练习正常工作。
