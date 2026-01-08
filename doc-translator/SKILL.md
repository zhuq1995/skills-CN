---
name: doc-translator
description: 将非中文的skill文档翻译为中文，并创建到新目录。当用户需要翻译技能文档、创建中文版本或本地化文档时使用。
---

# 文档翻译器 (Doc Translator)

将非中文的skill文档翻译为中文，保持专业术语和格式。

## 使用场景

- 翻译英文或其他语言的skill文档
- 为现有技能创建中文版本
- 本地化项目文档

## 工作流程

### 步骤1: 扫描源目录

首先发现目录中的所有文档文件：

```bash
Glob "**/*" in [源目录路径]
```

常见文件模式：
- `SKILL.md` - 技能主文档
- `examples.md` - 示例文档
- `reference.md` - 参考文档
- 其他 `.md` 文件

### 步骤2: 读取所有文件

并行读取所有发现的文档：

```bash
Read [每个文件路径]
```

### 步骤3: 创建目标目录

```bash
mkdir [目标目录路径]
```

命名规范：
- 英文原文 → `xxx-zh` 或 `xxx-cn`
- 中文 → `xxx-en`

### 步骤4: 翻译并写入文件

**翻译规则**：

| 内容类型 | 处理方式 |
|---------|----------|
| 标题 | 翻译为中文 |
| 正文段落 | 翻译为中文 |
| YAML frontmatter | 翻译 description，保留 name |
| 表格 | 翻译表头和单元格内容 |
| 代码块 | **保持不变** |
| 专业术语 | **保留原文**（如 Manus、token、cache） |
| 文件路径 | **保持原文** |
| 命令/工具名 | **保留原文**（如 Read、Write、Edit） |
| 变量名/标识符 | **保持原文** |

**专业术语保留清单**：
- AI/ML相关：AI、Agent、LLM、Model、Prompt、Token、Context
- 技术概念：API、Cache、KV-cache、JSON、Markdown
- 产品名称：Manus、Claude、Meta
- 代码相关：function、class、interface、hook、props

### 步骤5: 验证输出

确认所有文件已正确创建：
- 文件数量与原文一致
- 文件名对应正确
- 翻译质量完整

## 示例

**用户请求**：
```
翻译 skills/planning-with-files 目录到中文
```

**执行步骤**：

1. 发现文件：`SKILL.md`、`examples.md`、`reference.md`
2. 读取所有文件内容
3. 创建目标目录：`skills/planning-with-files-zh`
4. 翻译并写入三个文件
5. 验证输出

**结果**：
```
skills/
├── planning-with-files/
│   ├── SKILL.md
│   ├── examples.md
│   └── reference.md
└── planning-with-files-zh/
    ├── SKILL.md      (中文翻译)
    ├── examples.md   (中文翻译)
    └── reference.md  (中文翻译)
```

## 注意事项

- 保持原文的标题层级结构
- 代码块和示例中的注释**不要翻译**
- 链接和引用保持原文路径
- 翻译后检查是否有遗漏的英文段落
