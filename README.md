# dev-tools-hub

## 🔧 静态检查与修复记录 ✅

本次检查目标：

- `site/index.html` — HTML 静态检查（安全/可访问性/响应式）
- `.github/workflow/build-and-deploy.yml` — GitHub Actions workflow 文件位置与语法检查

发现与已做修复：

1. site/index.html
   - 问题：外部链接使用 `target="_blank"` 却未设置 `rel="noopener noreferrer"`（存在潜在的 window.opener 安全风险）。
   - 问题：缺少响应式视口元标签（`<meta name="viewport" ...>`），移动设备上可能显示不佳。
   - 处理：已为所有外部 `target="_blank"` 链接添加 `rel="noopener noreferrer"`，并加入 `<meta name="viewport" content="width=device-width, initial-scale=1">`。

2. Workflow 文件位置
   - 问题：工作流文件位于 `.github/workflow/`（单数）目录，GitHub Actions 期望目录为 `.github/workflows/`（复数），导致 Actions 无法识别该工作流。 
   - 处理：已将 `.github/workflow/` 移动到 `.github/workflows/`。

如何在本地复现检查：

- HTML 检查（推荐）：
  - 安装：`npm i -g htmlhint` 或使用 `npx htmlhint`。
  - 运行：`htmlhint site/index.html`

- YAML 检查（推荐）：
  - 安装：`pip install yamllint`。
  - 运行：`yamllint .github/workflows/build-and-deploy.yml`

提交说明与下次动作：

- 提交信息："chore: fix site/index.html (viewport + rel) and move workflow to .github/workflows; add static-checks README"
- 下步建议：若需要，可对 `site/` 使用更完备的 HTML 验证（例如 `tidy`）以及对 CI workflow 在 Ubuntu runner 上做一次测试构建。

---

如需我把这些更改拆成更小的提交、运行具体 linters，或把静态检查脚本加入 CI，请告诉我下一步要做的事项。