---
name: xlsx
description: "全面的电子表格创建、编辑与分析能力，支持公式、格式、数据分析与可视化。当 Claude 需要处理表格（.xlsx、.xlsm、.csv、.tsv 等）时使用： (1) 创建带公式与格式的新表格，(2) 读取或分析数据，(3) 在保留公式的前提下修改现有表格，(4) 在表格中进行数据分析与可视化，或 (5) 重新计算公式"
license: Proprietary. LICENSE.txt has complete terms
---

# 输出要求

## 所有 Excel 文件

### 公式错误必须为零
- 交付的每个 Excel 模型必须做到 0 个公式错误（#REF!、#DIV/0!、#VALUE!、#N/A、#NAME?）

### 保留既有模板（更新模板时）
- 修改文件前，先研究并严格匹配既有格式、风格与约定
- 对于已有明确模式的文件，绝不要强行套用标准化格式
- 既有模板的约定永远优先于本指南

## 财务模型

### 颜色编码标准
除非用户或既有模板另有说明

#### 行业标准颜色约定
- **蓝色文本（RGB: 0,0,255）**：硬编码输入、以及用户用于情景分析会修改的数字
- **黑色文本（RGB: 0,0,0）**：所有公式与计算结果
- **绿色文本（RGB: 0,128,0）**：同一工作簿内跨工作表引用的链接
- **红色文本（RGB: 255,0,0）**：指向其他文件的外部链接
- **黄色背景（RGB: 255,255,0）**：需要关注的关键假设或需要更新的单元格

### 数字格式标准

#### 必须遵循的格式规则
- **年份**：用文本字符串格式（例如 `"2024"`，而不是 `"2,024"`）
- **货币**：使用 `$#,##0` 格式；表头必须标明单位（例如 `"Revenue ($mm)"`）
- **零值**：通过数字格式把所有 0 显示为 `"-"`，百分比也同样适用（例如 `"$#,##0;($#,##0);-"`）
- **百分比**：默认使用 `0.0%`（保留 1 位小数）
- **倍数**：估值倍数用 `0.0x` 格式（EV/EBITDA、P/E）
- **负数**：使用括号 `(123)`，不要用负号 `-123`

### 公式构建规则

#### 假设项放置
- 将所有假设（增长率、利润率、倍数等）放在单独的假设单元格中
- 在公式中使用单元格引用，避免硬编码常量
- 示例：用 `=B5*(1+$B$6)`，不要用 `=B5*1.05`

#### 防止公式错误
- 核对所有单元格引用是否正确
- 检查区间是否存在 off-by-one 错误
- 确保所有预测期的公式一致
- 用边界情况测试（0、负数等）
- 确认没有非预期的循环引用

#### 硬编码的来源标注要求
- 在单元格批注中，或（若处于表格末尾）写在旁边单元格中。格式：`"Source: [System/Document], [Date], [Specific Reference], [URL if applicable]"`
- 示例：
  - "Source: Company 10-K, FY2024, Page 45, Revenue Note, [SEC EDGAR URL]"
  - "Source: Company 10-Q, Q2 2025, Exhibit 99.1, [SEC EDGAR URL]"
  - "Source: Bloomberg Terminal, 8/15/2025, AAPL US Equity"
  - "Source: FactSet, 8/20/2025, Consensus Estimates Screen"

# XLSX 创建、编辑与分析

## 概览

用户可能会要求你创建、编辑或分析一个 `.xlsx` 文件的内容。针对不同任务，可以选择不同的工具与工作流。

## 重要要求

**重新计算公式需要 LibreOffice**：可以假设已安装 LibreOffice，用于通过 `recalc.py` 脚本重新计算公式值。脚本会在首次运行时自动完成 LibreOffice 配置。

## 读取与分析数据

### 使用 pandas 做数据分析
进行数据分析、可视化与基础操作时，使用 **pandas**，它提供强大的数据处理能力：

```python
import pandas as pd

# Read Excel
df = pd.read_excel('file.xlsx')  # Default: first sheet
all_sheets = pd.read_excel('file.xlsx', sheet_name=None)  # All sheets as dict

# Analyze
df.head()      # Preview data
df.info()      # Column info
df.describe()  # Statistics

# Write Excel
df.to_excel('output.xlsx', index=False)
```

## Excel 文件工作流

## 关键：使用公式，不要硬编码数值

**始终使用 Excel 公式，而不是在 Python 里算出结果再写死到单元格。** 这样表格才能保持动态、可更新。

### ❌ 错误示例：硬编码计算值
```python
# Bad: Calculating in Python and hardcoding result
total = df['Sales'].sum()
sheet['B10'] = total  # Hardcodes 5000

# Bad: Computing growth rate in Python
growth = (df.iloc[-1]['Revenue'] - df.iloc[0]['Revenue']) / df.iloc[0]['Revenue']
sheet['C5'] = growth  # Hardcodes 0.15

# Bad: Python calculation for average
avg = sum(values) / len(values)
sheet['D20'] = avg  # Hardcodes 42.5
```

### ✅ 正确示例：使用 Excel 公式
```python
# Good: Let Excel calculate the sum
sheet['B10'] = '=SUM(B2:B9)'

# Good: Growth rate as Excel formula
sheet['C5'] = '=(C4-C2)/C2'

# Good: Average using Excel function
sheet['D20'] = '=AVERAGE(D2:D19)'
```

这适用于所有计算：汇总、百分比、比率、差值等。源数据变化后，表格应能自行重新计算。

## 通用工作流
1. **选择工具**：数据处理用 pandas，公式/格式用 openpyxl
2. **创建/加载**：创建新工作簿或加载现有文件
3. **修改**：新增/编辑数据、公式与格式
4. **保存**：写入文件
5. **重新计算公式（只要使用了公式就必须做）**：使用 `recalc.py` 脚本
   ```bash
   python recalc.py output.xlsx
   ```
6. **验证并修复错误**：
   - 脚本会返回包含错误详情的 JSON
   - 如果 `status` 为 `errors_found`，查看 `error_summary` 中的错误类型与位置
   - 修复后再次重新计算
   - 常见需要修复的错误：
     - `#REF!`: Invalid cell references
     - `#DIV/0!`: Division by zero
     - `#VALUE!`: Wrong data type in formula
     - `#NAME?`: Unrecognized formula name

### 创建新的 Excel 文件

```python
# Using openpyxl for formulas and formatting
from openpyxl import Workbook
from openpyxl.styles import Font, PatternFill, Alignment

wb = Workbook()
sheet = wb.active

# Add data
sheet['A1'] = 'Hello'
sheet['B1'] = 'World'
sheet.append(['Row', 'of', 'data'])

# Add formula
sheet['B2'] = '=SUM(A1:A10)'

# Formatting
sheet['A1'].font = Font(bold=True, color='FF0000')
sheet['A1'].fill = PatternFill('solid', start_color='FFFF00')
sheet['A1'].alignment = Alignment(horizontal='center')

# Column width
sheet.column_dimensions['A'].width = 20

wb.save('output.xlsx')
```

### 编辑现有 Excel 文件

```python
# Using openpyxl to preserve formulas and formatting
from openpyxl import load_workbook

# Load existing file
wb = load_workbook('existing.xlsx')
sheet = wb.active  # or wb['SheetName'] for specific sheet

# Working with multiple sheets
for sheet_name in wb.sheetnames:
    sheet = wb[sheet_name]
    print(f"Sheet: {sheet_name}")

# Modify cells
sheet['A1'] = 'New Value'
sheet.insert_rows(2)  # Insert row at position 2
sheet.delete_cols(3)  # Delete column 3

# Add new sheet
new_sheet = wb.create_sheet('NewSheet')
new_sheet['A1'] = 'Data'

wb.save('modified.xlsx')
```

## 重新计算公式

openpyxl 创建或修改的 Excel 文件会把公式保存为字符串，但不会填充计算结果。使用提供的 `recalc.py` 脚本重新计算公式：

```bash
python recalc.py <excel_file> [timeout_seconds]
```

示例：
```bash
python recalc.py output.xlsx 30
```

脚本会：
- 首次运行时自动配置 LibreOffice 宏
- 重新计算所有工作表中的全部公式
- 扫描所有单元格中的 Excel 错误（#REF!、#DIV/0! 等）
- 返回包含错误位置与数量的详细 JSON
- 同时支持 Linux 与 macOS

## 公式验证清单

用于快速确认公式是否正确的检查项：

### 必要校验
- [ ] **抽查 2–3 个引用**：在搭建完整模型前先确认引用能拉到正确数值
- [ ] **列映射**：确认 Excel 列号映射正确（例如第 64 列是 BL，而不是 BK）
- [ ] **行偏移**：Excel 行号从 1 开始（DataFrame 第 5 行对应 Excel 第 6 行）

### 常见坑点
- [ ] **NaN 处理**：用 `pd.notna()` 检查空值
- [ ] **靠右列**：FY 数据常在第 50 列之后
- [ ] **多处匹配**：要搜索所有出现位置，而不是只取第一个
- [ ] **除零**：公式里使用 `/` 之前先检查分母（避免 #DIV/0!）
- [ ] **引用错误**：确认所有引用都指向预期单元格（避免 #REF!）
- [ ] **跨表引用**：跨工作表引用使用正确格式（`Sheet1!A1`）

### 公式测试策略
- [ ] **从小处开始**：先在 2–3 个单元格上验证公式，再批量应用
- [ ] **核对依赖**：确认公式引用的所有单元格都存在
- [ ] **覆盖边界情况**：包含 0、负数与极大值

### 解读 recalc.py 输出
脚本会返回包含错误详情的 JSON：
```json
{
  "status": "success",           // or "errors_found"
  "total_errors": 0,              // Total error count
  "total_formulas": 42,           // Number of formulas in file
  "error_summary": {              // Only present if errors found
    "#REF!": {
      "count": 2,
      "locations": ["Sheet1!B5", "Sheet1!C10"]
    }
  }
}
```

## 最佳实践

### 库选择
- **pandas**：适合数据分析、批量操作与简单导出
- **openpyxl**：适合复杂格式、公式与 Excel 特性操作

### 使用 openpyxl
- 单元格索引从 1 开始（`row=1, column=1` 对应 A1）
- 读取计算值可用 `data_only=True`：`load_workbook('file.xlsx', data_only=True)`
- **警告**：以 `data_only=True` 打开并保存会把公式替换为数值，且不可逆
- 大文件：读取用 `read_only=True`，写入用 `write_only=True`
- openpyxl 会保留公式但不计算，需要用 `recalc.py` 更新结果

### 使用 pandas
- 指定 dtype 避免类型推断问题：`pd.read_excel('file.xlsx', dtype={'id': str})`
- 大文件按列读取：`pd.read_excel('file.xlsx', usecols=['A', 'C', 'E'])`
- 正确处理日期：`pd.read_excel('file.xlsx', parse_dates=['date_column'])`

## 代码风格指南
**重要**：生成用于 Excel 操作的 Python 代码时：
- 写最小化、简洁的代码，避免无意义的注释
- 避免冗长变量名与重复操作
- 避免不必要的 print

**对于 Excel 文件本身**：
- 对复杂公式或关键假设所在单元格添加批注
- 对硬编码数值记录数据来源
- 对关键计算与模型区块添加说明
