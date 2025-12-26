# dev-tools-hub

## 🔍 CI 静态检查（htmlhint / yamllint）

本仓库已添加一个专门的静态检查 workflow：`.github/workflows/static-checks.yml`，会在 PR 和 push 到 `main` 时运行：

- HTML 校验（`htmlhint`）：检查 `site/**/*.html`。可本地运行：
  - `npx --yes htmlhint "site/**/*.html"`
- YAML 校验（`yamllint`）：检查 `.github/workflows/*.yml`（以及其它需要检查的 YAML）。可本地运行：
  - `python -m pip install --upgrade pip yamllint`
  - `yamllint -c .yamllint .github/workflows/*.yml`

配置文件：
- `.htmlhintrc` — HTMLHint 规则
- `.yamllint` — yamllint 配置

如果你希望我把这些检查作为必须通过的 status check（阻止合并），我可以继续配置分支保护规则或把 workflow 的触发条件细化。
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

- 注意：在 CI 运行中发现 `libpolly-dev` 在默认 Ubuntu 仓库不可用，直接安装该包会失败。已改为使用 `apt.llvm.org` 的 `llvm.sh` 安装脚本（与 `tools/cppinsights` 保持一致），该脚本会安装包含 Polly/ISL 的适配版 LLVM；如有必要，我也可以改为下载并解压官方 clang+llvm 二进制包以保证构建一致性。

---

如需我把这些更改拆成更小的提交、运行具体 linters，或把静态检查脚本加入 CI，请告诉我下一步要做的事项。