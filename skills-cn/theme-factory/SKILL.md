---
name: theme-factory
description: 通过主题为各类产物统一样式的工具包。产物可以是幻灯片、文档、报告、HTML 落地页等。内置 10 套预设主题（颜色/字体），可应用到任意已创建的产物，也可按需即时生成新主题。
license: Complete terms in LICENSE.txt
---


# Theme Factory 技能

此技能提供一组精选的专业字体与配色主题，每个主题都包含精心挑选的调色板与字体搭配。选定主题后，可将其应用到任意产物。

## 目的

当需要为演示文稿或其他产物应用一致、专业的样式时，使用此技能。每个主题包含：
- 一套带十六进制色值的统一调色板
- 标题与正文互补的字体搭配
- 适用于不同场景与受众的明确视觉识别

## 使用说明

为幻灯片或其他产物应用样式时：

1. **展示主题合集**：展示 `theme-showcase.pdf`，让用户直观看到所有可用主题。不要修改该文件，只需展示供查看。
2. **询问选择**：询问用户希望将哪个主题应用到当前产物
3. **等待确认**：获取对所选主题的明确确认
4. **应用主题**：确认后，将该主题的颜色与字体应用到幻灯片/产物中

## 可用主题

共有 10 个主题，均在 `theme-showcase.pdf` 中展示：

1. **Ocean Depths** - Professional and calming maritime theme
2. **Sunset Boulevard** - Warm and vibrant sunset colors
3. **Forest Canopy** - Natural and grounded earth tones
4. **Modern Minimalist** - Clean and contemporary grayscale
5. **Golden Hour** - Rich and warm autumnal palette
6. **Arctic Frost** - Cool and crisp winter-inspired theme
7. **Desert Rose** - Soft and sophisticated dusty tones
8. **Tech Innovation** - Bold and modern tech aesthetic
9. **Botanical Garden** - Fresh and organic garden colors
10. **Midnight Galaxy** - Dramatic and cosmic deep tones

## 主题细节

每个主题都在 `themes/` 目录中定义，包含完整规范，例如：
- 带十六进制色值的统一调色板
- 标题与正文互补的字体搭配
- 面向不同场景与受众的独特视觉识别

## 应用流程

在用户选定主题后：
1. 从 `themes/` 目录读取对应主题文件
2. 将指定的颜色与字体一致地应用到整份产物中
3. 确保对比度与可读性
4. 跨所有页面保持主题的视觉一致性

## 自定义主题
当现有主题都不适用时，创建自定义主题。基于用户提供的输入，生成一个与上述主题风格相近的新主题，并为其命名，使名称能表达字体/配色组合所代表的意象或氛围。根据用户给出的基础描述选择合适的颜色/字体。生成后先展示主题供审阅与确认，随后按上述流程应用到产物中。
