> **注意：** 本仓库包含 Anthropic 为 Claude 实现的技能 (Skills)。关于 Agent Skills 标准的更多信息，请参阅 [agentskills.io](http://agentskills.io)。

# 技能 (Skills)
技能是指 Claude 动态加载以提高特定任务表现的指令、脚本和资源的文件夹。技能教导 Claude 如何以可重复的方式完成特定任务，无论是使用贵公司的品牌指南创建文档、使用贵组织的特定工作流程分析数据，还是自动化个人任务。

欲了解更多信息，请查看：
- [什么是技能？](https://support.claude.com/en/articles/12512176-what-are-skills)
- [在 Claude 中使用技能](https://support.claude.com/en/articles/12512180-using-skills-in-claude)
- [如何创建自定义技能](https://support.claude.com/en/articles/12512198-creating-custom-skills)
- [使用 Agent Skills 装备 Agent 应对现实世界](https://anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)

# 关于本仓库

本仓库包含演示 Claude 技能系统可能性的技能。这些技能范围从创意应用（艺术、音乐、设计）到技术任务（测试 Web 应用程序、MCP 服务器生成）再到企业工作流程（通信、品牌推广等）。

每个技能都包含在自己的文件夹中，带有一个包含 Claude 使用的指令和元数据的 `SKILL.md` 文件。浏览这些技能以获取自己技能的灵感，或了解不同的模式和方法。

本仓库中的许多技能都是开源的 (Apache 2.0)。我们还在 [`skills/docx`](./skills/docx)、[`skills/pdf`](./skills/pdf)、[`skills/pptx`](./skills/pptx) 和 [`skills/xlsx`](./skills/xlsx) 子文件夹中包含了支持 [Claude 文档功能](https://www.anthropic.com/news/create-files) 的文档创建和编辑技能。这些是源码可用的 (source-available)，而不是开源的，但我们希望与开发人员分享这些，作为在生产 AI 应用程序中积极使用的更复杂技能的参考。

## 免责声明

**提供这些技能仅用于演示和教育目的。** 虽然 Claude 可能提供其中某些功能，但您从 Claude 收到的实现和行为可能与这些技能中显示的不同。这些技能旨在说明模式和可能性。在依赖它们执行关键任务之前，请务必在您自己的环境中彻底测试技能。

# 技能集
- [./skills](./skills)：创意与设计、开发与技术、企业与通信以及文档技能的技能示例
- [./spec](./spec)：Agent Skills 规范
- [./template](./template)：技能模板

# 在 Claude Code、Claude.ai 和 API 中试用

## Claude Code
您可以通过在 Claude Code 中运行以下命令，将此仓库注册为 Claude Code 插件市场：
```
/plugin marketplace add anthropics/skills
```

然后，要安装特定的技能集：
1. 选择 `Browse and install plugins` (浏览并安装插件)
2. 选择 `anthropic-agent-skills`
3. 选择 `document-skills` 或 `example-skills`
4. 选择 `Install now` (立即安装)

或者，通过以下方式直接安装任一插件：
```
/plugin install document-skills@anthropic-agent-skills
/plugin install example-skills@anthropic-agent-skills
```

安装插件后，您只需提及即可使用该技能。例如，如果您从市场安装了 `document-skills` 插件，您可以要求 Claude Code 做如下事情：“使用 PDF 技能从 `path/to/some-file.pdf` 中提取表单字段”

## Claude.ai

所有这些示例技能都已向 Claude.ai 的付费计划提供。

要使用本仓库中的任何技能或上传自定义技能，请按照 [在 Claude 中使用技能](https://support.claude.com/en/articles/12512180-using-skills-in-claude#h_a4222fa77b) 中的说明进行操作。

## Claude API

您可以通过 Claude API 使用 Anthropic 的预构建技能并上传自定义技能。有关更多信息，请参阅 [Skills API 快速入门](https://docs.claude.com/en/api/skills-guide#creating-a-skill)。

# 创建基本技能

创建技能很简单 - 只需一个包含 `SKILL.md` 文件的文件夹，其中包含 YAML frontmatter 和指令。您可以使用本仓库中的 **template-skill** 作为起点：

```markdown
---
name: my-skill-name
description: A clear description of what this skill does and when to use it
---

# My Skill Name

[Add your instructions here that Claude will follow when this skill is active]

## Examples
- Example usage 1
- Example usage 2

## Guidelines
- Guideline 1
- Guideline 2
```

Frontmatter 只需要两个字段：
- `name` - 技能的唯一标识符（小写，用连字符代替空格）
- `description` - 对技能功能及使用时机的完整描述

下方的 markdown 内容包含 Claude 将遵循的指令、示例和指南。有关更多详细信息，请参阅 [如何创建自定义技能](https://support.claude.com/en/articles/12512198-creating-custom-skills)。

# 合作伙伴技能

技能是教导 Claude 如何更好地使用特定软件的好方法。当我们看到来自合作伙伴的精彩示例技能时，我们可能会在这里重点介绍其中一些：

- **Notion** - [Claude 的 Notion 技能](https://www.notion.so/notiondevs/Notion-Skills-for-Claude-28da4445d27180c7af1df7d8615723d0)
