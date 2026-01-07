---
name: pptx
description: "演示文稿创建、编辑与分析。当 Claude 需要处理演示文稿（.pptx 文件）时使用： (1) 从零创建新演示，(2) 修改或编辑内容，(3) 处理版式与布局，(4) 添加批注或演讲者备注，或其他任何演示相关任务"
license: Proprietary. LICENSE.txt has complete terms
---

# PPTX 创建、编辑与分析

## 概览

用户可能会要求你创建、编辑或分析 `.pptx` 文件的内容。`.pptx` 本质上是一个 ZIP 压缩包，里面包含 XML 文件与其他资源，你可以读取或编辑它们。不同任务对应不同的工具与工作流。

## 读取与分析内容

### 文本提取
如果只需要读取演示文稿的文本内容，应先将文档转换为 markdown：

```bash
# Convert document to markdown
python -m markitdown path-to-file.pptx
```

### 原始 XML 访问
以下场景需要直接访问原始 XML：批注、演讲者备注、幻灯片布局、动画、设计元素、复杂格式等。要处理这些能力，需要先解包演示文稿并读取其原始 XML 内容。

#### 解包文件
`python ooxml/scripts/unpack.py <office_file> <output_dir>`

**注意**：`unpack.py` 脚本位于项目根目录下的 `skills/pptx/ooxml/scripts/unpack.py`。如果该路径不存在，请使用 `find . -name "unpack.py"` 定位它。

#### 关键文件结构
* `ppt/presentation.xml` - 演示文稿主元数据与幻灯片引用
* `ppt/slides/slide{N}.xml` - 单页幻灯片内容（slide1.xml、slide2.xml 等）
* `ppt/notesSlides/notesSlide{N}.xml` - 每页的演讲者备注
* `ppt/comments/modernComment_*.xml` - 特定幻灯片的批注
* `ppt/slideLayouts/` - 幻灯片布局模板
* `ppt/slideMasters/` - 母版模板
* `ppt/theme/` - 主题与样式信息
* `ppt/media/` - 图片与其他媒体资源

#### 提取字体与颜色
**当用户提供了需要模仿的示例设计时**：务必先分析演示文稿的字体与颜色，方法如下：
1. **读取主题文件**：检查 `ppt/theme/theme1.xml` 中的配色（`<a:clrScheme>`）与字体（`<a:fontScheme>`）
2. **抽样查看幻灯片内容**：检查 `ppt/slides/slide1.xml` 中真实使用的字体（`<a:rPr>`）与颜色
3. **搜索模式**：用 grep 在所有 XML 中查找颜色（`<a:solidFill>`、`<a:srgbClr>`）与字体引用

## 创建新的 PowerPoint（无模板）

当从零创建新的 PowerPoint 演示文稿时，使用 **html2pptx** 工作流将 HTML 幻灯片转换为 PowerPoint，并保持精准定位。

### 设计原则

**关键**：开始制作任何演示之前，先分析内容并选择合适的设计要素：
1. **考虑主题**：这份演示讲什么？它暗示什么语气、行业或氛围？
2. **检查品牌**：若提到公司/组织，考虑其品牌色与视觉识别
3. **让配色贴合内容**：选择能反映主题的颜色
4. **先讲清思路**：写代码前先解释你的设计选择

**要求**：
- ✅ 写代码前先说明基于内容的设计思路
- ✅ 仅使用 web-safe 字体：Arial、Helvetica、Times New Roman、Georgia、Courier New、Verdana、Tahoma、Trebuchet MS、Impact
- ✅ 用字号、字重与颜色建立清晰的视觉层级
- ✅ 确保可读性：对比强、字号合适、对齐干净
- ✅ 保持一致：跨幻灯片复用模式、间距与视觉语言

#### 配色选择

**创造性地选色**：
- **不要停留在默认**：哪些颜色真正适合这个具体主题？避免“自动驾驶”式选择
- **多维度考虑**：主题、行业、氛围、能量水平、目标受众、品牌识别（如有）
- **敢于尝试**：组合可以出其不意——医疗不一定非绿，金融也不一定非藏蓝
- **构建调色板**：选择 3–5 个能协同工作的颜色（主色 + 辅助色 + 强调色）
- **保证对比**：文本必须在背景上清晰可读

**示例调色板**（用于启发创意——可选其一、微调或自创）：

1. **Classic Blue**: Deep navy (#1C2833), slate gray (#2E4053), silver (#AAB7B8), off-white (#F4F6F6)
2. **Teal & Coral**: Teal (#5EA8A7), deep teal (#277884), coral (#FE4447), white (#FFFFFF)
3. **Bold Red**: Red (#C0392B), bright red (#E74C3C), orange (#F39C12), yellow (#F1C40F), green (#2ECC71)
4. **Warm Blush**: Mauve (#A49393), blush (#EED6D3), rose (#E8B4B8), cream (#FAF7F2)
5. **Burgundy Luxury**: Burgundy (#5D1D2E), crimson (#951233), rust (#C15937), gold (#997929)
6. **Deep Purple & Emerald**: Purple (#B165FB), dark blue (#181B24), emerald (#40695B), white (#FFFFFF)
7. **Cream & Forest Green**: Cream (#FFE1C7), forest green (#40695B), white (#FCFCFC)
8. **Pink & Purple**: Pink (#F8275B), coral (#FF574A), rose (#FF737D), purple (#3D2F68)
9. **Lime & Plum**: Lime (#C5DE82), plum (#7C3A5F), coral (#FD8C6E), blue-gray (#98ACB5)
10. **Black & Gold**: Gold (#BF9A4A), black (#000000), cream (#F4F6F6)
11. **Sage & Terracotta**: Sage (#87A96B), terracotta (#E07A5F), cream (#F4F1DE), charcoal (#2C2C2C)
12. **Charcoal & Red**: Charcoal (#292929), red (#E33737), light gray (#CCCBCB)
13. **Vibrant Orange**: Orange (#F96D00), light gray (#F2F2F2), charcoal (#222831)
14. **Forest Green**: Black (#191A19), green (#4E9F3D), dark green (#1E5128), white (#FFFFFF)
15. **Retro Rainbow**: Purple (#722880), pink (#D72D51), orange (#EB5C18), amber (#F08800), gold (#DEB600)
16. **Vintage Earthy**: Mustard (#E3B448), sage (#CBD18F), forest green (#3A6B35), cream (#F4F1DE)
17. **Coastal Rose**: Old rose (#AD7670), beaver (#B49886), eggshell (#F3ECDC), ash gray (#BFD5BE)
18. **Orange & Turquoise**: Light orange (#FC993E), grayish turquoise (#667C6F), white (#FCFCFC)

#### 视觉细节选项

**Geometric Patterns**:
- Diagonal section dividers instead of horizontal
- Asymmetric column widths (30/70, 40/60, 25/75)
- Rotated text headers at 90° or 270°
- Circular/hexagonal frames for images
- Triangular accent shapes in corners
- Overlapping shapes for depth

**Border & Frame Treatments**:
- Thick single-color borders (10-20pt) on one side only
- Double-line borders with contrasting colors
- Corner brackets instead of full frames
- L-shaped borders (top+left or bottom+right)
- Underline accents beneath headers (3-5pt thick)

**Typography Treatments**:
- Extreme size contrast (72pt headlines vs 11pt body)
- All-caps headers with wide letter spacing
- Numbered sections in oversized display type
- Monospace (Courier New) for data/stats/technical content
- Condensed fonts (Arial Narrow) for dense information
- Outlined text for emphasis

**Chart & Data Styling**:
- Monochrome charts with single accent color for key data
- Horizontal bar charts instead of vertical
- Dot plots instead of bar charts
- Minimal gridlines or none at all
- Data labels directly on elements (no legends)
- Oversized numbers for key metrics

**Layout Innovations**:
- Full-bleed images with text overlays
- Sidebar column (20-30% width) for navigation/context
- Modular grid systems (3×3, 4×4 blocks)
- Z-pattern or F-pattern content flow
- Floating text boxes over colored shapes
- Magazine-style multi-column layouts

**Background Treatments**:
- Solid color blocks occupying 40-60% of slide
- Gradient fills (vertical or diagonal only)
- Split backgrounds (two colors, diagonal or vertical)
- Edge-to-edge color bands
- Negative space as a design element

### 布局建议
**制作含图表或表格的幻灯片时：**
- **双栏布局（优先）**：标题横跨全宽，下方两栏：一栏放文字/要点，另一栏放主体内容。更平衡，也更利于图表/表格可读性。用 flexbox 设置不等宽（如 40%/60%）以优化空间分配。
- **整页布局**：让主体内容（图表/表格）占满整页，最大化冲击力与可读性
- **绝不要纵向堆叠**：不要把图表/表格放在单栏文字下方，这会导致可读性差与布局问题

### 工作流
1. **必做：完整阅读文件**：从头到尾通读 [`html2pptx.md`](html2pptx.md)。**阅读该文件时绝不要设置范围限制。** 在开始创建演示之前，先掌握详细语法、关键格式规则与最佳实践。
2. 为每页幻灯片创建一个 HTML 文件，并设置正确尺寸（例如 16:9 用 720pt × 405pt）
   - 所有文本内容使用 `<p>`、`<h1>`-`<h6>`、`<ul>`、`<ol>`
   - 图表/表格将要添加的区域用 `class="placeholder"` 标记（用灰底渲染便于识别）
   - **关键**：先用 Sharp 将渐变与图标栅格化为 PNG，再在 HTML 中引用
   - **布局**：包含图表/表格/图片的页面，使用整页布局或双栏布局以提升可读性
3. 使用 [`html2pptx.js`](scripts/html2pptx.js) 库编写并运行 JavaScript，把 HTML 幻灯片转换为 PowerPoint 并保存
   - 使用 `html2pptx()` 处理每个 HTML 文件
   - 使用 PptxGenJS API 将图表与表格添加到占位符区域
   - 使用 `pptx.writeFile()` 保存演示文稿
4. **视觉校验**：生成缩略图并检查布局问题
   - 生成缩略图网格：`python scripts/thumbnail.py output.pptx workspace/thumbnails --cols 4`
   - 仔细检查缩略图是否存在：
     - **文字裁切**：文字被标题条、图形或页面边缘切掉
     - **文字重叠**：文字与其他文字或图形发生重叠
     - **位置问题**：内容离边界或其他元素过近
     - **对比不足**：文字与背景对比不够导致可读性差
   - 若发现问题，调整 HTML 的边距/间距/配色并重新生成演示文稿
   - 重复直到所有页面视觉正确

## 编辑现有 PowerPoint 演示文稿

要编辑现有演示文稿的幻灯片，需要直接处理 Office Open XML（OOXML）格式：解包 `.pptx`、编辑 XML 内容、再重新打包。

### 工作流
1. **必做：完整阅读文件**：从头到尾通读 [`ooxml.md`](ooxml.md)（约 500 行）。**阅读该文件时绝不要设置范围限制。** 在开始任何编辑前，先理解 OOXML 结构与编辑工作流。
2. 解包演示文稿：`python ooxml/scripts/unpack.py <office_file> <output_dir>`
3. 编辑 XML 文件（主要是 `ppt/slides/slide{N}.xml` 及相关文件）
4. **关键**：每次编辑后立即校验并修复所有校验错误再继续：`python ooxml/scripts/validate.py <dir> --original <file>`
5. 打包生成最终文件：`python ooxml/scripts/pack.py <input_directory> <office_file>`

## 创建新的 PowerPoint（使用模板）

当需要创建遵循既有模板设计的演示文稿时，通常需要先复制/重排模板幻灯片，然后再替换占位符内容。

### 工作流
1. **提取模板文本并生成缩略图网格**：
   * 提取文本：`python -m markitdown template.pptx > template-content.md`
   * Read `template-content.md`: 通读整个文件以理解模板演示的内容。**阅读该文件时绝不要设置范围限制。**
   * 生成缩略图网格：`python scripts/thumbnail.py template.pptx`
   * 详见 [创建缩略图网格](#创建缩略图网格)

2. **分析模板并将清单保存到文件**：
   * **视觉分析**：查看缩略图网格，理解幻灯片布局、设计模式与视觉结构
   * 创建并保存模板清单文件 `template-inventory.md`，内容包含：
     ```markdown
     # Template Inventory Analysis
     **Total Slides: [count]**
     **重要：幻灯片索引从 0 开始（第 1 页为 0，最后一页为 count-1）**

     ## [Category Name]
     - Slide 0: [Layout code if available] - Description/purpose
     - Slide 1: [Layout code] - Description/purpose
     - Slide 2: [Layout code] - Description/purpose
     [... EVERY slide must be listed individually with its index ...]
     ```
   * **使用缩略图网格**：通过视觉缩略图识别：
     - 布局模式（标题页、内容布局、分隔页等）
     - 图片占位符的位置与数量
     - 不同页面组之间的一致性
     - 视觉层级与结构
   * 下一步选择合适模板时必须使用该清单文件

3. **基于模板清单创建演示大纲**：
   * 回顾第 2 步得到的可用模板。
   * 为第 1 页选择一个引言或封面模板（通常在模板前几页）。
   * 其余页面选择更稳妥的纯文本布局。
   * **关键：版式结构必须匹配真实内容**：
     - Single-column layouts: Use for unified narrative or single topic
     - Two-column layouts: Use ONLY when you have exactly 2 distinct items/concepts
     - Three-column layouts: Use ONLY when you have exactly 3 distinct items/concepts
     - Image + text layouts: Use ONLY when you have actual images to insert
     - Quote layouts: Use ONLY for actual quotes from people (with attribution), never for emphasis
     - Never use layouts with more placeholders than you have content
     - If you have 2 items, don't force them into a 3-column layout
     - If you have 4+ items, consider breaking into multiple slides or using a list format
   * 选择布局前先数清楚真实内容块的数量
   * 确认所选布局的每个占位符都能被有意义的内容填满
   * 对每个内容段落选择一个“最合适”的布局方案
   * 保存 `outline.md`：同时包含内容与模板映射关系
   * Example template mapping:
      ```
      # Template slides to use (0-based indexing)
      # 警告：确认索引在范围内！例如模板有 73 页，其索引范围为 0-72
      # Mapping: slide numbers from outline -> template slide indices
      template_mapping = [
          0,   # Use slide 0 (Title/Cover)
          34,  # Use slide 34 (B1: Title and body)
          34,  # Use slide 34 again (duplicate for second B1)
          50,  # Use slide 50 (E1: Quote)
          54,  # Use slide 54 (F2: Closing + Text)
      ]
      ```

4. **使用 `rearrange.py` 复制、重排并删除幻灯片**：
   * Use the `scripts/rearrange.py` script to create a new presentation with slides in the desired order:
     ```bash
     python scripts/rearrange.py template.pptx working.pptx 0,34,34,50,52
     ```
   * 脚本会自动处理重复幻灯片的复制、未使用幻灯片的删除与顺序调整
   * 幻灯片索引从 0 开始（第 1 页为 0、第 2 页为 1，以此类推）
   * 同一索引可重复出现，以复制该页幻灯片

5. **使用 `inventory.py` 提取全部文本**：
   * **运行清单提取**：
     ```bash
     python scripts/inventory.py working.pptx text-inventory.json
     ```
   * **Read text-inventory.json**：通读整个文件以理解所有形状及其属性。**阅读该文件时绝不要设置范围限制。**

   * inventory JSON 结构：
      ```json
        {
          "slide-0": {
            "shape-0": {
              "placeholder_type": "TITLE",  // or null for non-placeholders
              "left": 1.5,                  // position in inches
              "top": 2.0,
              "width": 7.5,
              "height": 1.2,
              "paragraphs": [
                {
                  "text": "Paragraph text",
                  // Optional properties (only included when non-default):
                  "bullet": true,           // explicit bullet detected
                  "level": 0,               // only included when bullet is true
                  "alignment": "CENTER",    // CENTER, RIGHT (not LEFT)
                  "space_before": 10.0,     // space before paragraph in points
                  "space_after": 6.0,       // space after paragraph in points
                  "line_spacing": 22.4,     // line spacing in points
                  "font_name": "Arial",     // from first run
                  "font_size": 14.0,        // in points
                  "bold": true,
                  "italic": false,
                  "underline": false,
                  "color": "FF0000"         // RGB color
                }
              ]
            }
          }
        }
      ```

   * 关键特性：
     - **Slides**：命名为 `"slide-0"`、`"slide-1"` 等
     - **Shapes**：按视觉位置（从上到下、从左到右）排序，命名为 `"shape-0"`、`"shape-1"` 等
     - **Placeholder types**：TITLE、CENTER_TITLE、SUBTITLE、BODY、OBJECT 或 null
     - **Default font size**：从布局占位符中提取的 `default_font_size`（单位：pt，若可用）
     - **过滤页码**：占位符类型为 SLIDE_NUMBER 的形状会被自动排除
     - **项目符号**：当 `bullet: true` 时，总会包含 `level`（即使为 0）
     - **间距**：`space_before`、`space_after`、`line_spacing`（单位：pt，仅在设置时出现）
     - **颜色**：RGB 用 `color`（例如 `"FF0000"`），主题色用 `theme_color`（例如 `"DARK_1"`）
     - **属性精简**：输出中只包含非默认值

6. **生成替换文本并保存为 JSON**
  基于上一步的文本清单：
  - **关键**：先确认清单里实际有哪些形状，只能引用确实存在的形状
  - **校验**：`replace.py` 会校验替换 JSON 中引用的形状是否都存在于清单中
    - 引用不存在的形状会报错，并列出可用形状
    - 引用不存在的幻灯片会报错提示该幻灯片不存在
    - 所有校验错误会一次性输出后退出
  - **重要**：`replace.py` 内部会使用 `inventory.py` 识别所有文本形状
  - **自动清空**：除非为形状提供 `"paragraphs"`，否则清单中的所有文本形状都会被清空
  - 对需要填充内容的形状添加 `"paragraphs"` 字段（不是 `"replacement_paragraphs"`）
  - 替换 JSON 中未提供 `"paragraphs"` 的形状会被自动清空
  - 含项目符号的段落会被自动左对齐。当 `"bullet": true` 时不要设置 `alignment`
  - 为占位符文本生成合适的替换内容
  - 根据形状尺寸决定合适的内容长度
  - **关键**：必须包含原始清单中的段落属性，不要只提供文本
  - **重要**：当 `bullet: true` 时，文本里不要写项目符号字符（•、-、*），它们会自动添加
  - **必备格式规则**：
    - 标题通常需要 `"bold": true`
    - 列表项需要 `"bullet": true, "level": 0`（当 bullet 为 true 时 level 必填）
    - 保留所有对齐属性（例如居中文本的 `"alignment": "CENTER"`）
    - 当字体属性不同于默认值时要包含它们（例如 `"font_size": 14.0`、`"font_name": "Lora"`）
    - 颜色：RGB 用 `"color": "FF0000"`；主题色用 `"theme_color": "DARK_1"`
    - 替换脚本需要的是**格式正确的 paragraphs**，而不是纯字符串
    - **重叠形状**：优先选择 `default_font_size` 更大或 `placeholder_type` 更合适的形状
  - 将带替换内容的更新清单保存为 `replacement-text.json`
  - **警告**：不同模板布局的形状数量不同，创建替换前务必先检查实际清单

   正确格式的 `paragraphs` 字段示例：
   ```json
   "paragraphs": [
     {
       "text": "New presentation title text",
       "alignment": "CENTER",
       "bold": true
     },
     {
       "text": "Section Header",
       "bold": true
     },
     {
       "text": "First bullet point without bullet symbol",
       "bullet": true,
       "level": 0
     },
     {
       "text": "Red colored text",
       "color": "FF0000"
     },
     {
       "text": "Theme colored text",
       "theme_color": "DARK_1"
     },
     {
       "text": "Regular paragraph text without special formatting"
     }
   ]
   ```

   **替换 JSON 中未列出的形状会被自动清空**：
   ```json
   {
     "slide-0": {
       "shape-0": {
         "paragraphs": [...] // This shape gets new text
       }
       // shape-1 and shape-2 from inventory will be cleared automatically
     }
   }
   ```

   **演示文稿常见格式模式**：
   - 标题页：加粗文本，有时居中
   - 页内小节标题：加粗文本
   - 项目符号列表：每一项都需要 `"bullet": true, "level": 0`
   - 正文：通常不需要特殊属性
   - 引用：可能需要特殊对齐或字体属性

7. **Apply replacements using the `replace.py` script**
   ```bash
   python scripts/replace.py working.pptx replacement-text.json output.pptx
   ```

   脚本会：
   - 先用 `inventory.py` 中的函数提取所有文本形状的清单
   - 校验替换 JSON 中引用的形状是否都存在于清单中
   - 清空清单识别到的所有形状文本
   - 仅对替换 JSON 中定义了 `"paragraphs"` 的形状写入新文本
   - 通过 JSON 中的段落属性保留格式
   - 自动处理项目符号、对齐、字体属性与颜色
   - 保存更新后的演示文稿

   校验错误示例：
   ```
   ERROR: Invalid shapes in replacement JSON:
     - Shape 'shape-99' not found on 'slide-0'. Available shapes: shape-0, shape-1, shape-4
     - Slide 'slide-999' not found in inventory
   ```

   ```
   ERROR: Replacement text made overflow worse in these shapes:
     - slide-0/shape-2: overflow worsened by 1.25" (was 0.00", now 1.25")
   ```

## 创建缩略图网格

要生成用于快速分析与参考的 PowerPoint 幻灯片缩略图网格：

```bash
python scripts/thumbnail.py template.pptx [output_prefix]
```

**特性**：
- 输出：`thumbnails.jpg`（大文件会输出 `thumbnails-1.jpg`、`thumbnails-2.jpg` 等）
- 默认：5 列，每张网格最多 30 页（5×6）
- 自定义前缀：`python scripts/thumbnail.py template.pptx my-grid`
  - 注意：如果希望输出到特定目录，前缀需要带路径（例如 `workspace/my-grid`）
- 调整列数：`--cols 4`（范围：3–6，会影响每张网格包含的页数）
- 网格容量：3 列=12 页/网格、4 列=20、5 列=30、6 列=42
- 幻灯片索引从 0 开始（Slide 0、Slide 1 等）

**使用场景**：
- 模板分析：快速理解布局与设计模式
- 内容审阅：对整份演示做视觉总览
- 导航参考：通过视觉外观定位特定页面
- 质量检查：确认所有页面格式正确

**示例**：
```bash
# Basic usage
python scripts/thumbnail.py presentation.pptx

# Combine options: custom name, columns
python scripts/thumbnail.py template.pptx analysis --cols 4
```

## 将幻灯片转换为图片

要对 PowerPoint 幻灯片做视觉分析，可按两步将其转换为图片：

1. **将 PPTX 转为 PDF**：
   ```bash
   soffice --headless --convert-to pdf template.pptx
   ```

2. **将 PDF 页面转为 JPEG 图片**：
   ```bash
   pdftoppm -jpeg -r 150 template.pdf slide
   ```
   会生成 `slide-1.jpg`、`slide-2.jpg` 等文件。

参数：
- `-r 150`：设置分辨率为 150 DPI（按清晰度/体积权衡调整）
- `-jpeg`：输出 JPEG（如需 PNG 用 `-png`）
- `-f N`：从第 N 页开始（例如 `-f 2` 从第 2 页开始）
- `-l N`：到第 N 页结束（例如 `-l 5` 到第 5 页为止）
- `slide`：输出文件名前缀

指定范围示例：
```bash
pdftoppm -jpeg -r 150 -f 2 -l 5 template.pdf slide  # Converts only pages 2-5
```

## 代码风格指南
**重要**：生成用于 PPTX 操作的代码时：
- 写简洁代码
- 避免冗长变量名与重复操作
- 避免不必要的 print

## Dependencies

需要的依赖（通常已安装）：

- **markitdown**：`pip install "markitdown[pptx]"`（从演示文稿中提取文本）
- **pptxgenjs**：`npm install -g pptxgenjs`（通过 html2pptx 创建演示）
- **playwright**：`npm install -g playwright`（用于 html2pptx 的 HTML 渲染）
- **react-icons**：`npm install -g react-icons react react-dom`（图标库）
- **sharp**：`npm install -g sharp`（SVG 栅格化与图像处理）
- **LibreOffice**：`sudo apt-get install libreoffice`（用于转换为 PDF）
- **Poppler**：`sudo apt-get install poppler-utils`（提供 pdftoppm，将 PDF 转为图片）
- **defusedxml**：`pip install defusedxml`（安全解析 XML）
