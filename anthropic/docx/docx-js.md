# DOCX 库教程

使用 JavaScript/TypeScript 生成 `.docx` 文件。

**重要：开始前请完整阅读本文档。** 文中涵盖关键格式规则与常见陷阱——跳读可能导致文件损坏或渲染问题。

## 安装
假设已全局安装 `docx`
如未安装：`npm install -g docx`

```javascript
const { Document, Packer, Paragraph, TextRun, Table, TableRow, TableCell, ImageRun, Media, 
        Header, Footer, AlignmentType, PageOrientation, LevelFormat, ExternalHyperlink, 
        InternalHyperlink, TableOfContents, HeadingLevel, BorderStyle, WidthType, TabStopType, 
        TabStopPosition, UnderlineType, ShadingType, VerticalAlign, SymbolRun, PageNumber,
        FootnoteReferenceRun, Footnote, PageBreak } = require('docx');

// 创建与保存
const doc = new Document({ sections: [{ children: [/* content */] }] });
Packer.toBuffer(doc).then(buffer => fs.writeFileSync("doc.docx", buffer)); // Node.js
Packer.toBlob(doc).then(blob => { /* download logic */ }); // Browser
```

## 文本与格式
```javascript
// 重要：不要用 \n 换行——每行使用独立 Paragraph
// ❌ 错误：new TextRun("Line 1\nLine 2")
// ✅ 正确：new Paragraph({ children: [new TextRun("Line 1")] }), new Paragraph({ children: [new TextRun("Line 2")] })

// 基本文本与所有格式选项
new Paragraph({
  alignment: AlignmentType.CENTER,
  spacing: { before: 200, after: 200 },
  indent: { left: 720, right: 720 },
  children: [
    new TextRun({ text: "Bold", bold: true }),
    new TextRun({ text: "Italic", italics: true }),
    new TextRun({ text: "Underlined", underline: { type: UnderlineType.DOUBLE, color: "FF0000" } }),
    new TextRun({ text: "Colored", color: "FF0000", size: 28, font: "Arial" }), // Arial default
    new TextRun({ text: "Highlighted", highlight: "yellow" }),
    new TextRun({ text: "Strikethrough", strike: true }),
    new TextRun({ text: "x2", superScript: true }),
    new TextRun({ text: "H2O", subScript: true }),
    new TextRun({ text: "SMALL CAPS", smallCaps: true }),
    new SymbolRun({ char: "2022", font: "Symbol" }), // Bullet •
    new SymbolRun({ char: "00A9", font: "Arial" })   // Copyright © - Arial for symbols
  ]
})
```

## 样式与专业排版

```javascript
const doc = new Document({
  styles: {
    default: { document: { run: { font: "Arial", size: 24 } } }, // 默认 12pt
    paragraphStyles: [
      // 文档标题样式——覆盖内置 Title 样式
      { id: "Title", name: "Title", basedOn: "Normal",
        run: { size: 56, bold: true, color: "000000", font: "Arial" },
        paragraph: { spacing: { before: 240, after: 120 }, alignment: AlignmentType.CENTER } },
      // 重要：用内置样式的精确 ID 覆盖标题样式
      { id: "Heading1", name: "Heading 1", basedOn: "Normal", next: "Normal", quickFormat: true,
        run: { size: 32, bold: true, color: "000000", font: "Arial" }, // 16pt
        paragraph: { spacing: { before: 240, after: 240 }, outlineLevel: 0 } }, // Required for TOC
      { id: "Heading2", name: "Heading 2", basedOn: "Normal", next: "Normal", quickFormat: true,
        run: { size: 28, bold: true, color: "000000", font: "Arial" }, // 14pt
        paragraph: { spacing: { before: 180, after: 180 }, outlineLevel: 1 } },
      // 自定义样式使用自定义 ID
      { id: "myStyle", name: "My Style", basedOn: "Normal",
        run: { size: 28, bold: true, color: "000000" },
        paragraph: { spacing: { after: 120 }, alignment: AlignmentType.CENTER } }
    ],
    characterStyles: [{ id: "myCharStyle", name: "My Char Style",
      run: { color: "FF0000", bold: true, underline: { type: UnderlineType.SINGLE } } }]
  },
  sections: [{
    properties: { page: { margin: { top: 1440, right: 1440, bottom: 1440, left: 1440 } } },
    children: [
      new Paragraph({ heading: HeadingLevel.TITLE, children: [new TextRun("Document Title")] }), // 使用覆盖后的 Title 样式
      new Paragraph({ heading: HeadingLevel.HEADING_1, children: [new TextRun("Heading 1")] }), // 使用覆盖后的 Heading1 样式
      new Paragraph({ style: "myStyle", children: [new TextRun("Custom paragraph style")] }),
      new Paragraph({ children: [
        new TextRun("Normal with "),
        new TextRun({ text: "custom char style", style: "myCharStyle" })
      ]})
    ]
  }]
});
```

**专业字体组合：**
- **Arial（标题）+ Arial（正文）** —— 通用支持、干净专业
- **Times New Roman（标题）+ Arial（正文）** —— 经典衬线标题 + 现代无衬线正文
- **Georgia（标题）+ Verdana（正文）** —— 屏幕友好、对比优雅

**样式原则：**
- **覆盖内置样式**：用精确 ID（如 "Heading1"、"Heading2"、"Heading3"）覆盖 Word 标题样式
- **HeadingLevel 常量**：`HeadingLevel.HEADING_1` 使用 "Heading1"，`HeadingLevel.HEADING_2` 使用 "Heading2"
- **设置 outlineLevel**：H1 用 0，H2 用 1，以确保 TOC 工作
- **优先自定义样式** 而非内联格式以保持一致性
- **设置默认字体**：`styles.default.document.run.font`，推荐 Arial
- **建立视觉层级**：通过字号区分（标题 > 头部 > 正文）
- **合理留白**：用段前/段后间距
- **谨慎用色**：标题与头部优先黑色与灰阶
- **统一页边距**（1440 = 1 英寸）


## 列表（务必使用“真正的列表”——不要用 Unicode 项符号）
```javascript
// 项符号——务必用 numbering 配置，不要用 unicode
// 关键：使用 LevelFormat.BULLET 常量，而非字符串 "bullet"
const doc = new Document({
  numbering: {
    config: [
      { reference: "bullet-list",
        levels: [{ level: 0, format: LevelFormat.BULLET, text: "•", alignment: AlignmentType.LEFT,
          style: { paragraph: { indent: { left: 720, hanging: 360 } } } }] },
      { reference: "first-numbered-list",
        levels: [{ level: 0, format: LevelFormat.DECIMAL, text: "%1.", alignment: AlignmentType.LEFT,
          style: { paragraph: { indent: { left: 720, hanging: 360 } } } }] },
      { reference: "second-numbered-list", // 引用不同 → 从 1 重新编号
        levels: [{ level: 0, format: LevelFormat.DECIMAL, text: "%1.", alignment: AlignmentType.LEFT,
          style: { paragraph: { indent: { left: 720, hanging: 360 } } } }] }
    ]
  },
  sections: [{
    children: [
      // Bullet list items
      new Paragraph({ numbering: { reference: "bullet-list", level: 0 },
        children: [new TextRun("First bullet point")] }),
      new Paragraph({ numbering: { reference: "bullet-list", level: 0 },
        children: [new TextRun("Second bullet point")] }),
      // Numbered list items
      new Paragraph({ numbering: { reference: "first-numbered-list", level: 0 },
        children: [new TextRun("First numbered item")] }),
      new Paragraph({ numbering: { reference: "first-numbered-list", level: 0 },
        children: [new TextRun("Second numbered item")] }),
      // ⚠️ 关键：不同引用 → 独立列表，重新从 1 开始
      // 相同引用 → 延续之前的编号
      new Paragraph({ numbering: { reference: "second-numbered-list", level: 0 },
        children: [new TextRun("Starts at 1 again (because different reference)")] })
    ]
  }]
});

// ⚠️ 关键编号规则：每个 reference 创建一个独立编号列表
// - 相同 reference = 延续编号（1,2,3... 然后 4,5,6...）
// - 不同 reference = 从 1 重新开始（1,2,3... 然后 1,2,3...）
// 为每个独立编号段落使用唯一 reference！

// ⚠️ 关键：不要用 unicode 项符号——这是“伪列表”
// new TextRun("• Item")           // 错误
// new SymbolRun({ char: "2022" }) // 错误
// ✅ 使用 numbering 配置 + LevelFormat.BULLET 才是“真正的 Word 列表”
```

## 表格
```javascript
// Complete table with margins, borders, headers, and bullet points
const tableBorder = { style: BorderStyle.SINGLE, size: 1, color: "CCCCCC" };
const cellBorders = { top: tableBorder, bottom: tableBorder, left: tableBorder, right: tableBorder };

new Table({
  columnWidths: [4680, 4680], // ⚠️ 关键：表级设置列宽，单位 DXA（1/20 pt）
  margins: { top: 100, bottom: 100, left: 180, right: 180 }, // Set once for all cells
  rows: [
    new TableRow({
      tableHeader: true,
      children: [
        new TableCell({
          borders: cellBorders,
          width: { size: 4680, type: WidthType.DXA }, // ALSO set width on each cell
          // ⚠️ 关键：务必使用 ShadingType.CLEAR 防止 Word 出现黑底
          shading: { fill: "D5E8F0", type: ShadingType.CLEAR }, 
          verticalAlign: VerticalAlign.CENTER,
          children: [new Paragraph({ 
            alignment: AlignmentType.CENTER,
            children: [new TextRun({ text: "Header", bold: true, size: 22 })]
          })]
        }),
        new TableCell({
          borders: cellBorders,
          width: { size: 4680, type: WidthType.DXA }, // ALSO set width on each cell
          shading: { fill: "D5E8F0", type: ShadingType.CLEAR },
          children: [new Paragraph({ 
            alignment: AlignmentType.CENTER,
            children: [new TextRun({ text: "Bullet Points", bold: true, size: 22 })]
          })]
        })
      ]
    }),
    new TableRow({
      children: [
        new TableCell({
          borders: cellBorders,
          width: { size: 4680, type: WidthType.DXA }, // ALSO set width on each cell
          children: [new Paragraph({ children: [new TextRun("Regular data")] })]
        }),
        new TableCell({
          borders: cellBorders,
          width: { size: 4680, type: WidthType.DXA }, // ALSO set width on each cell
          children: [
            new Paragraph({ 
              numbering: { reference: "bullet-list", level: 0 },
              children: [new TextRun("First bullet point")] 
            }),
            new Paragraph({ 
              numbering: { reference: "bullet-list", level: 0 },
              children: [new TextRun("Second bullet point")] 
            })
          ]
        })
      ]
    })
  ]
})
```

**重要：表格宽度与边框**
- 同时使用 `columnWidths` 数组与每个单元格的 `width: { size, type: WidthType.DXA }`
- DXA 单位：1440 = 1 英寸；美式信纸（1" 边距）可用宽度 = 9360 DXA
- 边框应用到 `TableCell`，不要应用到 `Table`

**预设列宽（信纸 1" 边距，总宽 9360 DXA）：**
- **2 列：** `columnWidths: [4680, 4680]`
- **3 列：** `columnWidths: [3120, 3120, 3120]`

## 链接与导航
```javascript
// TOC（需要标题）——关键：只用 HeadingLevel，不要给标题段落加自定义样式
// ❌ 错误：new Paragraph({ heading: HeadingLevel.HEADING_1, style: "customHeader", children: [new TextRun("Title")] })
// ✅ 正确：new Paragraph({ heading: HeadingLevel.HEADING_1, children: [new TextRun("Title")] })
new TableOfContents("Table of Contents", { hyperlink: true, headingStyleRange: "1-3" }),

// 外部链接
new Paragraph({
  children: [new ExternalHyperlink({
    children: [new TextRun({ text: "Google", style: "Hyperlink" })],
    link: "https://www.google.com"
  })]
}),

// 内部链接与书签
new Paragraph({
  children: [new InternalHyperlink({
    children: [new TextRun({ text: "Go to Section", style: "Hyperlink" })],
    anchor: "section1"
  })]
}),
new Paragraph({
  children: [new TextRun("Section Content")],
  bookmark: { id: "section1", name: "section1" }
}),
```

## 图像与媒体
```javascript
// 基本图像（含大小与定位）
// 关键：务必指定 'type' 参数——ImageRun 必填
new Paragraph({
  alignment: AlignmentType.CENTER,
  children: [new ImageRun({
    type: "png", // 新要求：必须指定图像类型（png, jpg, jpeg, gif, bmp, svg）
    data: fs.readFileSync("image.png"),
    transformation: { width: 200, height: 150, rotation: 0 }, // 旋转单位：度
    altText: { title: "Logo", description: "Company logo", name: "Name" } // 重要：三个字段均必填
  })]
})
```

## 分页符
```javascript
// 手动分页符
new Paragraph({ children: [new PageBreak()] }),

// 段前分页
new Paragraph({
  pageBreakBefore: true,
  children: [new TextRun("This starts on a new page")]
})

// ⚠️ 关键：不要单独使用 PageBreak——会生成 Word 无法打开的无效 XML
// ❌ 错误：new PageBreak() 
// ✅ 正确：new Paragraph({ children: [new PageBreak()] })
```

## 页眉/页脚与页面设置
```javascript
const doc = new Document({
  sections: [{
    properties: {
      page: {
        margin: { top: 1440, right: 1440, bottom: 1440, left: 1440 }, // 1440 = 1 英寸
        size: { orientation: PageOrientation.LANDSCAPE },
        pageNumbers: { start: 1, formatType: "decimal" } // "upperRoman", "lowerRoman", "upperLetter", "lowerLetter"
      }
    },
    headers: {
      default: new Header({ children: [new Paragraph({ 
        alignment: AlignmentType.RIGHT,
        children: [new TextRun("Header Text")]
      })] })
    },
    footers: {
      default: new Footer({ children: [new Paragraph({ 
        alignment: AlignmentType.CENTER,
        children: [new TextRun("Page "), new TextRun({ children: [PageNumber.CURRENT] }), new TextRun(" of "), new TextRun({ children: [PageNumber.TOTAL_PAGES] })]
      })] })
    },
    children: [/* content */]
  }]
});
```

## 制表位
```javascript
new Paragraph({
  tabStops: [
    { type: TabStopType.LEFT, position: TabStopPosition.MAX / 4 },
    { type: TabStopType.CENTER, position: TabStopPosition.MAX / 2 },
    { type: TabStopType.RIGHT, position: TabStopPosition.MAX * 3 / 4 }
  ],
  children: [new TextRun("Left\tCenter\tRight")]
})
```

## 常量与速查
- **Underlines:** `SINGLE`, `DOUBLE`, `WAVY`, `DASH`
- **Borders:** `SINGLE`, `DOUBLE`, `DASHED`, `DOTTED`  
- **Numbering:** `DECIMAL` (1,2,3), `UPPER_ROMAN` (I,II,III), `LOWER_LETTER` (a,b,c)
- **Tabs:** `LEFT`, `CENTER`, `RIGHT`, `DECIMAL`
- **Symbols:** `"2022"` (•), `"00A9"` (©), `"00AE"` (®), `"2122"` (™), `"00B0"` (°), `"F070"` (✓), `"F0FC"` (✗)

## 关键问题与常见错误
- **关键：PageBreak 必须在 Paragraph 内**——单独使用会生成 Word 无法打开的无效 XML
- **表格底纹务必使用 ShadingType.CLEAR**——不要用 SOLID（会出现黑底）
- 测量单位为 DXA（1440 = 1 英寸）｜每个单元格至少需 1 个 Paragraph｜TOC 仅接受 HeadingLevel 样式
- **统一使用自定义样式** + Arial 字体以获得专业外观与正确层级
- **设置默认字体**：`styles.default.document.run.font`（建议 Arial）
- **表格需列宽数组 + 单元格宽度** 保证兼容性
- **不要用 unicode 作为项符号**——使用 `LevelFormat.BULLET` 常量（不是字符串 "bullet"）
- **不要用 \n 换行**——每行独立 Paragraph
- **Paragraph 子元素必须是 TextRun**——不要直接在 Paragraph 上设置 text
- **图像**：ImageRun 必须指定 `type`（png/jpg/jpeg/gif/bmp/svg）
- **项符号**：必须用 `LevelFormat.BULLET` 常量，并包含 `text: "•"`
- **编号**：每个 numbering reference 都是独立列表；相同 → 延续，不同 → 从 1 开始；为每段编号用唯一 reference
- **目录**：使用 TableOfContents 时，标题必须只用 HeadingLevel，不要给标题段落加自定义样式
- **表格**：设置 `columnWidths` + 单元格宽度，边框应用于单元格而非表格
- **在表级设置表格边距** 以统一内边距（避免逐单元格重复）
