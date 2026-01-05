**重要：必须按顺序完成以下步骤。不要跳过直接写代码。**

如需填写 PDF 表单，首先检查该 PDF 是否包含可填写的表单字段。请在本文件目录中运行脚本：
`python scripts/check_fillable_fields <file.pdf>`，根据结果进入“可填写字段”或“不可填写字段”并严格遵循对应说明。

# 可填写字段
如果 PDF 具有可填写的表单字段：
- 在本文件目录运行脚本：`python scripts/extract_form_field_info.py <input.pdf> <field_info.json>`。它会生成一个字段列表的 JSON 文件，格式如下：
```
[
  {
    "field_id": (字段唯一 ID),
    "page": (页码，1 起),
    "rect": ([left, bottom, right, top]，PDF 坐标的边界框，y=0 为页面底部),
    "type": ("text" | "checkbox" | "radio_group" | "choice"),
  },
  // 复选框包含 "checked_value" 与 "unchecked_value" 属性：
  {
    "field_id": (字段唯一 ID),
    "page": (页码，1 起),
    "type": "checkbox",
    "checked_value": (设置为该值可勾选复选框),
    "unchecked_value": (设置为该值可取消勾选),
  },
  // 单选组包含 "radio_options"，列出所有可选项：
  {
    "field_id": (字段唯一 ID),
    "page": (页码，1 起),
    "type": "radio_group",
    "radio_options": [
      {
        "value": (设置为该值选中该单选项),
        "rect": (该单选项按钮的边界框)
      },
      // 其他单选项
    ]
  },
  // 多选下拉字段包含 "choice_options"：
  {
    "field_id": (字段唯一 ID),
    "page": (页码，1 起),
    "type": "choice",
    "choice_options": [
      {
        "value": (设置为该值选择该选项),
        "text": (该选项的显示文本)
      },
      // 其他选项
    ],
  }
]
```
- 将 PDF 转为 PNG（每页一张图片）：
`python scripts/convert_pdf_to_images.py <file.pdf> <output_directory>`
随后分析图像以确定每个字段的用途（注意将 PDF 边界框坐标转换到图像坐标）。
- 创建 `field_values.json`，为每个字段填写的值，格式如下：
```
[
  {
    "field_id": "last_name", // 必须匹配 `extract_form_field_info.py` 的 field_id
    "description": "用户的姓氏",
    "page": 1, // 必须匹配 field_info.json 中的 page 值
    "value": "Simpson"
  },
  {
    "field_id": "Checkbox12",
    "description": "若用户年满 18 岁则勾选",
    "page": 1,
    "value": "/On" // 若为复选框，使用其 checked_value 勾选；若为单选组，使用 radio_options 中某个 value。
  }
  // 更多字段
]
```
- 在本文件目录运行 `fill_fillable_fields.py` 以生成已填写的 PDF：
`python scripts/fill_fillable_fields.py <input pdf> <field_values.json> <output pdf>`
该脚本会验证你提供的字段 ID 与值的有效性；若打印错误信息，请修正后再试。

# 不可填写字段
若 PDF 不含可填写字段，你需要通过视觉分析确定数据应添加的位置，并创建文本注释。严格按以下步骤执行。为确保表单准确完成，所有步骤都必须执行。各步骤详述如下。
- 将 PDF 转为 PNG 图像并确定字段边界框
- 创建包含字段信息与验证图像（显示边界框）的 JSON 文件
- 验证边界框
- 使用边界框向表单填入内容

## 步骤 1：视觉分析（必做）
- 将 PDF 转为 PNG：在本文件目录运行
`python scripts/convert_pdf_to_images.py <file.pdf> <output_directory>`
脚本会为 PDF 的每一页生成 PNG 图像。
- 仔细检查每张 PNG，识别所有字段及需输入数据的区域。对于用户需输入文本的字段，需同时确定字段标签的边界框与文本输入区域的边界框。二者边界框必须不相交；文本输入框仅包含应输入数据的区域。通常该区域在标签的右侧、上方或下方。输入边界框需具有足够的高度与宽度以容纳文本。

可能出现的表单结构示例：

【标签在框内】
```
┌────────────────────────┐
│ Name:                  │
└────────────────────────┘
```
输入区域应位于 “Name” 标签的右侧并延伸至该框右边缘。

【标签在横线之前】
```
Email: _______________________
```
输入区域应在横线上方，并包含横线的完整宽度。

【标签在横线下方】
```
_________________________
Name
```
输入区域应位于横线上方，并包含横线的完整宽度（常用于签名与日期字段）。

【标签在横线上方】
```
Please enter any special requests:
________________________________________________
```
输入区域应自标签底部延伸至横线，并包含横线的完整宽度。

【复选框】
```
Are you a US citizen? Yes □  No □
```
对于复选框：
- 寻找小方框（□）——这是真正需要定位的复选框，可能位于标签文本左侧或右侧。
- 区分标签文本（“Yes”“No”）与可点击的小方框。
- 输入边界框应仅覆盖小方框，不包含标签文本。

### 步骤 2：创建 fields.json 与验证图像（必做）
- 创建 `fields.json`，记录字段信息与边界框，格式如下：
```
{
  "pages": [
    {
      "page_number": 1,
      "image_width": (第 1 页图像宽度，像素),
      "image_height": (第 1 页图像高度，像素),
    },
    {
      "page_number": 2,
      "image_width": (第 2 页图像宽度，像素),
      "image_height": (第 2 页图像高度，像素),
    }
    // 更多页
  ],
  "form_fields": [
    // 文本字段示例
    {
      "page_number": 1,
      "description": "在此输入用户的姓氏",
      // 边界框为 [left, top, right, bottom]；标签与输入框的边界框不可重叠
      "field_label": "Last name",
      "label_bounding_box": [30, 125, 95, 142],
      "entry_bounding_box": [100, 125, 280, 142],
      "entry_text": {
        "text": "Johnson", // 作为注释添加到 entry_bounding_box 位置
        "font_size": 14, // 可选，默认 14
        "font_color": "000000", // 可选，RRGGBB 格式，默认 000000（黑色）
      }
    },
    // 复选框示例：输入边界框目标是“小方框”，不是文本
    {
      "page_number": 2,
      "description": "若用户年满 18 岁则勾选",
      "entry_bounding_box": [140, 525, 155, 540],  // 覆盖复选方框的小区域
      "field_label": "Yes",
      "label_bounding_box": [100, 525, 132, 540],  // 覆盖 “Yes” 文本的区域
      // 使用 "X" 勾选复选框
      "entry_text": {
        "text": "X",
      }
    }
    // 更多字段
  ]
}
```

为每页生成验证图像，在本文件目录运行：
`python scripts/create_validation_image.py <page_number> <path_to_fields.json> <input_image_path> <output_image_path>`

验证图像中，红色矩形表示输入区域，蓝色矩形覆盖标签文本。

### 步骤 3：验证边界框（必做）
#### 自动交叉检测
- 使用 `check_bounding_boxes.py` 检查 `fields.json`，确保边界框不相交且输入框高度足够（在本文件目录运行）：
`python scripts/check_bounding_boxes.py <JSON file>`

如有错误，请重新分析相关字段、调整边界框并迭代，直到无错误。注意：标签（蓝色）边界框应包含标签文本；输入（红色）边界框不应包含文本。

#### 手动图像检查
**重要：未经人工检查验证图像，不得继续执行**
- 红色矩形仅覆盖输入区域
- 红色矩形不得包含任何文本
- 蓝色矩形应包含标签文本
- 对于复选框：
  - 红色矩形必须居中于复选方框
  - 蓝色矩形应覆盖复选框的文本标签

- 若任何矩形看起来不正确，请修正 `fields.json`、重新生成验证图像并再次验证。重复该过程直至边界框完全准确。

### 步骤 4：向 PDF 添加注释
在本文件目录运行脚本，依据 `fields.json` 创建填写完成的 PDF：
`python scripts/fill_pdf_form_with_annotations.py <input_pdf_path> <path_to_fields.json> <output_pdf_path>`
