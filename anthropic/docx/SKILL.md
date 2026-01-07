---
name: docx
description: "用于创建、编辑与分析文档的完整能力，支持修订(Tracked Changes)、批注、格式保留与文本抽取。当 Claude 需要处理专业文档（.docx 文件）以完成：(1) 创建新文档，(2) 修改/编辑内容，(3) 处理修订，(4) 添加批注，或其他文档相关任务时使用。"
license: Proprietary. LICENSE.txt has complete terms
---

# DOCX 创建、编辑与分析

## 概览

用户可能会要求你创建、编辑或分析某个 `.docx` 文件的内容。`.docx` 本质上是一个 ZIP 压缩包，内部包含 XML 文件与其他资源，你可以读取或编辑这些内容。针对不同任务，需要使用不同工具与工作流。

## 工作流决策树

### 读取/分析内容
使用下方“文本抽取”或“原始 XML 访问”

### 创建新文档
使用“创建新的 Word 文档”工作流

### 编辑已有文档
- **你自己的文档 + 简单修改**
  使用“基础 OOXML 编辑”工作流

- **他人的文档**
  使用 **“修订审阅工作流（Redlining workflow）”**（推荐默认）

- **法律/学术/商务/政府类文档**
  使用 **“修订审阅工作流（Redlining workflow）”**（必选）

## 读取与分析内容

### 文本抽取
如果你只需要阅读文档的文本内容，应使用 `pandoc` 将文档转换为 Markdown。Pandoc 对保留文档结构支持很好，并且可展示修订：

```bash
# Convert document to markdown with tracked changes
pandoc --track-changes=all path-to-file.docx -o output.md
# Options: --track-changes=accept/reject/all
```

### 原始 XML 访问
以下场景需要原始 XML 访问：批注、复杂格式、文档结构、嵌入媒体、元数据。遇到这些需求，需要先解包文档并读取其原始 XML。

#### 解包文件
`python ooxml/scripts/unpack.py <office_file> <output_directory>`

#### 关键文件结构
* `word/document.xml` - 主文档内容
* `word/comments.xml` - 在 `document.xml` 中引用的批注
* `word/media/` - 嵌入的图片与媒体文件
* 修订使用 `<w:ins>`（插入）与 `<w:del>`（删除）标签

## 创建新的 Word 文档

从零创建新的 Word 文档时，使用 **docx-js**，它允许你用 JavaScript/TypeScript 创建 Word 文档。

### Workflow
1. **强制 - 阅读完整文件**：完整阅读 [`docx-js.md`](docx-js.md)（约 500 行），从头到尾全部读取。**阅读该文件时绝不要设置范围限制。** 在开始创建文档前，先掌握详细语法、关键格式规则与最佳实践。
2. 使用 Document、Paragraph、TextRun 组件创建一个 JavaScript/TypeScript 文件（可假设依赖已安装；若没有，参考下方“依赖”）
3. 使用 `Packer.toBuffer()` 导出为 `.docx`

## 编辑已有的 Word 文档

编辑已有 Word 文档时，使用 **Document library**（用于操作 OOXML 的 Python 库）。该库会自动处理基础设施与文件结构，并提供文档操作方法。复杂场景下，也可以通过该库直接访问底层 DOM。

### Workflow
1. **强制 - 阅读完整文件**：完整阅读 [`ooxml.md`](ooxml.md)（约 600 行），从头到尾全部读取。**阅读该文件时绝不要设置范围限制。** 该文件包含 Document library 的 API 以及直接编辑文档文件所需的 XML 模式。
2. 解包文档：`python ooxml/scripts/unpack.py <office_file> <output_directory>`
3. 编写并运行一个使用 Document library 的 Python 脚本（见 `ooxml.md` 的 “Document Library” 小节）
4. 打包最终文档：`python ooxml/scripts/pack.py <input_directory> <office_file>`

Document library 既提供常见操作的高层方法，也提供复杂场景下的直接 DOM 访问能力。

## 文档审阅的修订工作流（Redlining workflow）

该工作流允许你在真正改 OOXML 前，先用 Markdown 规划完整的修订（Tracked Changes）。**关键**：若要实现“完整修订”，必须系统性地实现所有变更，不能漏项。

**分批策略**：将相关变更按 3-10 条为一批分组。这样既能保持效率，也便于调试。每批完成后先测试，再进入下一批。

**原则：最小且精准的编辑**
实现修订时，只标记真正发生变化的文本。重复未改文本会让审阅更困难且显得不专业。替换应拆分为：[未变文本] + [删除] + [插入] + [未变文本]。未变文本要保留原 run 的 RSID：从原文中抽取对应 `<w:r>` 元素并复用。

示例：把句子里的 “30 days” 改成 “60 days”：
```python
# BAD - Replaces entire sentence
'<w:del><w:r><w:delText>The term is 30 days.</w:delText></w:r></w:del><w:ins><w:r><w:t>The term is 60 days.</w:t></w:r></w:ins>'

# GOOD - Only marks what changed, preserves original <w:r> for unchanged text
'<w:r w:rsidR="00AB12CD"><w:t>The term is </w:t></w:r><w:del><w:r><w:delText>30</w:delText></w:r></w:del><w:ins><w:r><w:t>60</w:t></w:r></w:ins><w:r w:rsidR="00AB12CD"><w:t> days.</w:t></w:r>'
```

### 修订（Tracked Changes）工作流

1. **获取 Markdown 表示**：将文档转换为 Markdown，并保留修订：
   ```bash
   pandoc --track-changes=all path-to-file.docx -o current.md
   ```

2. **识别并分组变更**：审阅文档并识别所有需要的变更，将其组织成逻辑清晰的批次：

   **定位方法**（用于在 XML 中找到变更位置）：
   - Section/heading numbers (e.g., "Section 3.2", "Article IV")
   - Paragraph identifiers if numbered
   - Grep patterns with unique surrounding text
   - Document structure (e.g., "first paragraph", "signature block")
   - **不要使用 Markdown 行号** - 它们无法映射到 XML 结构

   **批次组织方式**（每批分组 3-10 条相关变更）：
   - By section: "Batch 1: Section 2 amendments", "Batch 2: Section 5 updates"
   - By type: "Batch 1: Date corrections", "Batch 2: Party name changes"
   - By complexity: Start with simple text replacements, then tackle complex structural changes
   - Sequential: "Batch 1: Pages 1-3", "Batch 2: Pages 4-6"

3. **阅读文档并解包**：
   - **强制 - 阅读完整文件**：完整阅读 [`ooxml.md`](ooxml.md)（约 600 行），从头到尾全部读取。**阅读该文件时绝不要设置范围限制。** 重点关注 “Document Library” 与 “Tracked Change Patterns” 两节。
   - **解包文档**：`python ooxml/scripts/unpack.py <file.docx> <dir>`
   - **记录建议的 RSID**：解包脚本会建议一个用于修订的 RSID。复制该 RSID，供第 4b 步使用。

4. **分批实现变更**：按章节、类型或位置接近度对变更分组，并在一个脚本中批量实现。这样做的好处：
   - Makes debugging easier (smaller batch = easier to isolate errors)
   - Allows incremental progress
   - Maintains efficiency (batch size of 3-10 changes works well)

   **建议的分组方式：**
   - By document section (e.g., "Section 3 changes", "Definitions", "Termination clause")
   - By change type (e.g., "Date changes", "Party name updates", "Legal term replacements")
   - By proximity (e.g., "Changes on pages 1-3", "Changes in first half of document")

   对每一批相关变更：

   **a. Map text to XML**: Grep for text in `word/document.xml` to verify how text is split across `<w:r>` elements.

   **b. Create and run script**: Use `get_node` to find nodes, implement changes, then `doc.save()`. See **"Document Library"** section in ooxml.md for patterns.

   **注意**：在编写脚本前务必立即 grep `word/document.xml`，以获取最新行号并核对文本内容。每次运行脚本后行号都会变化。

5. **打包文档**：所有批次完成后，将解包目录重新打包为 `.docx`：
   ```bash
   python ooxml/scripts/pack.py unpacked reviewed-document.docx
   ```

6. **最终验证**：对完整文档进行全面检查：
   - Convert final document to markdown:
     ```bash
     pandoc --track-changes=all reviewed-document.docx -o verification.md
     ```
   - 验证所有变更都已正确应用：
     ```bash
     grep "original phrase" verification.md  # Should NOT find it
     grep "replacement phrase" verification.md  # Should find it
     ```
   - 检查是否引入了任何非预期变更


## 将文档转换为图片

要对 Word 文档做可视化分析，可用两步将其转换为图片：

1. **Convert DOCX to PDF**:
   ```bash
   soffice --headless --convert-to pdf document.docx
   ```

2. **Convert PDF pages to JPEG images**:
   ```bash
   pdftoppm -jpeg -r 150 document.pdf page
   ```
   这会生成类似 `page-1.jpg`、`page-2.jpg` 等文件。

参数说明：
- `-r 150`: 分辨率设为 150 DPI（按质量/体积平衡进行调整）
- `-jpeg`: 输出 JPEG 格式（如需 PNG 可用 `-png`）
- `-f N`: 起始页（例如 `-f 2` 从第 2 页开始）
- `-l N`: 结束页（例如 `-l 5` 到第 5 页结束）
- `page`: 输出文件名前缀

Example for specific range:
```bash
pdftoppm -jpeg -r 150 -f 2 -l 5 document.pdf page  # 仅转换第 2-5 页
```

## 代码风格指南
**重要**：生成 DOCX 操作相关代码时：
- 代码要简洁
- 避免冗长变量名与重复操作
- 避免不必要的 `print` 输出

## 依赖

所需依赖（若不可用则安装）：

- **pandoc**: `sudo apt-get install pandoc`（用于文本抽取）
- **docx**: `npm install -g docx`（用于创建新文档）
- **LibreOffice**: `sudo apt-get install libreoffice`（用于转 PDF）
- **Poppler**: `sudo apt-get install poppler-utils`（用于 `pdftoppm` 将 PDF 转图片）
- **defusedxml**: `pip install defusedxml`（用于安全的 XML 解析）
