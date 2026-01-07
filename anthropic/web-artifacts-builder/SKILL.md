---
name: web-artifacts-builder
description: 使用现代前端技术（React、Tailwind CSS、shadcn/ui）创建复杂、多组件 claude.ai HTML artifact 的工具套件。适用于需要状态管理、路由或 shadcn/ui 组件的复杂产物；不适用于简单的单文件 HTML/JSX 产物。
license: Complete terms in LICENSE.txt
---

# Web Artifacts Builder

要构建强大的前端 claude.ai artifact，按以下步骤执行：
1. 使用 `scripts/init-artifact.sh` 初始化前端仓库
2. 通过编辑生成的代码开发 artifact
3. 使用 `scripts/bundle-artifact.sh` 将全部代码打包为单个 HTML 文件
4. 向用户展示 artifact
5. （可选）测试 artifact

**Stack**: React 18 + TypeScript + Vite + Parcel (bundling) + Tailwind CSS + shadcn/ui

## 设计与样式指南

非常重要：为避免常见的“AI 流水线审美”，请避免过度居中布局、紫色渐变、统一的圆角，以及 Inter 字体。

## 快速开始

### 第一步：初始化项目

运行初始化脚本创建新的 React 项目：
```bash
bash scripts/init-artifact.sh <project-name>
cd <project-name>
```

脚本会创建一个已完整配置的项目，包含：
- ✅ React + TypeScript (via Vite)
- ✅ Tailwind CSS 3.4.1 with shadcn/ui theming system
- ✅ Path aliases (`@/`) configured
- ✅ 40+ shadcn/ui components pre-installed
- ✅ All Radix UI dependencies included
- ✅ Parcel configured for bundling (via .parcelrc)
- ✅ Node 18+ compatibility (auto-detects and pins Vite version)

### 第二步：开发 artifact

通过编辑生成的文件来构建 artifact。具体做法见下方 **Common Development Tasks**（常见开发任务）部分的指引。

### 第三步：打包为单个 HTML 文件

将 React 应用打包为单个 HTML artifact：
```bash
bash scripts/bundle-artifact.sh
```

会生成 `bundle.html`——一个自包含的 artifact，内联了所有 JavaScript、CSS 与依赖。该文件可直接在 Claude 对话中作为 artifact 分享。

**要求**：项目根目录必须存在 `index.html`。

**脚本做了什么**：
- 安装打包依赖（parcel、@parcel/config-default、parcel-resolver-tspaths、html-inline）
- 创建支持路径别名的 `.parcelrc` 配置
- 使用 Parcel 构建（不生成 source map）
- 使用 html-inline 将所有资源内联到单个 HTML

### 第四步：向用户分享 artifact

最后，在对话中把打包后的 HTML 文件分享给用户，以便其作为 artifact 查看。

### 第五步：测试/可视化 artifact（可选）

注意：这是完全可选的步骤，仅在必要或用户要求时执行。

测试/可视化 artifact 时，可使用可用工具（包括其他技能或内置工具，如 Playwright 或 Puppeteer）。一般不要在一开始就测试，因为这会增加从需求到可见成品之间的延迟。若用户要求或出现问题，再在展示 artifact 之后进行测试。

## Reference

- **shadcn/ui components**: https://ui.shadcn.com/docs/components
