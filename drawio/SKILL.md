---
name: drawio
description: 生成 draw.io 图表文件（流程图、架构图、ER图、系统图等）。当用户请求创建或编辑 .drawio 文件、绘制架构图、流程图、ER图（实体关系图）、数据库设计图、系统框架图或任何需要图形化展示的场景时使用。
---

# Draw.io 图表生成技能

## 文件基本结构

Draw.io 文件使用 XML 格式，基本结构如下：

```xml
<mxfile host="localhost" agent="..." version="26.0.4">
  <diagram id="唯一ID" name="图表名称">
    <mxGraphModel dx="1346" dy="812" grid="1" gridSize="10" guides="1" tooltips="1" 
        connect="1" arrows="1" fold="1" page="1" pageScale="1" 
        pageWidth="1920" pageHeight="1080" background="#FFFFFF" math="0" shadow="0">
      <root>
        <mxCell id="0" />
        <mxCell id="1" parent="0" />
        <!-- 在此添加图形元素 -->
      </root>
    </mxGraphModel>
  </diagram>
</mxfile>
```

## 核心规则

### 1. 换行符规则
- **html=1 时**：使用 `&#xa;` 换行（不是 `\n`）
- **html=0 时**：使用 `\n` 换行

### 2. 连线规则
- 线条使用折角（orthogonalEdgeStyle），不使用直连
- 尽量不让线与内容节点相交
- 连线样式：`edgeStyle=orthogonalEdgeStyle`

#### 连线锚点规则（重要）
- **exitX/entryX** 只使用标准值：`0`（左边）、`0.5`（中间）、`1`（右边）
- **exitY/entryY** 只使用标准值：`0`（上边）、`0.5`（中间）、`1`（下边）
- **禁止使用非标准锚点值**（如 0.2, 0.3, 0.7, 0.8），这会导致斜线和显示不完整
- 连线应从字段行的**左侧(exitX=0)**或**右侧(exitX=1)**出发，保持水平
- 使用 `points` 数组定义折点，确保连线全程垂直或水平
- 布局时**预留连线通道**，避免连线穿越实体框被遮挡

#### 连线方向建议
- 主从关系(MAST→LINE)：优先使用上下连接（`exitY=1, entryY=0`）
- 同层级实体间：优先使用左右连接（`exitX=1, entryX=0`）
- 跨区域连线：使用 points 数组绕过中间实体

### 3. 节点样式规则
- 内容方框**不需要圆角**：`rounded=0`
- 字体大小：`fontSize=14`（px）
- 中文字体：`fontFamily=SimSun`（宋体）
- 英文字体：`fontFamily=Arial`
- 边框宽度：`strokeWidth=1`（px）

### 4. 配色规则
采用美观协调的配色方案，避免单一乏味。以下是推荐配色：

| 用途 | 填充色 | 边框色 | 说明 |
|------|--------|--------|------|
| 主框架 | #d5e8d4 | #82b366 | 浅绿色，用于最外层容器 |
| 访问层 | #C8E6C9 | #A5D6A7 | 绿色系 |
| 前端层 | #FFF8E1 | #FFECB3 | 暖黄色 |
| 后端层 | #B2EBF2 | #80DEEA | 青色 |
| 服务层 | #FBE9E7 | #FFAB91 | 橙色系 |
| 控制器层 | #E3F2FD | #90CAF9 | 蓝色系 |
| 数据层 | #DCEDC8 | #C5E1A5 | 浅绿色 |
| 算法层 | #EDE7F6 | #B39DDB | 紫色系 |
| 缓存层 | #FFEBEE | #EF9A9A | 红色系 |
| 文件存储 | #E0F2F1 | #B2DFDB | 蓝绿色 |
| 基础设施 | #E0F2F1 | #B2DFDB | 蓝绿色 |
| 外部系统 | #FFF3E0 | #FFCC80 | 橙黄色 |
| 说明框 | #ffe6cc | #d79b00 | 橙色，虚线边框 |
| 连接线 | - | #90A4AE | 灰蓝色 |
| 内容节点 | #FFFFFF | 随层级 | 白色填充 |

### ER 图专用配色

| 用途 | 填充色 | 边框色 | 说明 |
|------|--------|--------|------|
| 实体表头 | #E1F5FE | #0288D1 | 浅蓝色，表名区域 |
| 实体内容 | #FFFFFF | #0288D1 | 白色，字段区域 |
| 主键字段 | #FFF3E0 | #FB8C00 | 橙色高亮 |
| 外键字段 | #E8F5E9 | #43A047 | 绿色高亮 |
| 关系连线(1:1) | - | #1976D2 | 蓝色实线 |
| 关系连线(1:N) | - | #388E3C | 绿色实线 |
| 关系连线(N:M) | - | #7B1FA2 | 紫色实线 |
| 分组区域 | #FAFAFA | #BDBDBD | 浅灰色，用于模块分组 |

## 节点模板

### 容器/层级节点
```xml
<mxCell id="layer_id" value="层级名称" 
    style="rounded=1;whiteSpace=wrap;html=1;strokeWidth=1;spacing=8;align=left;verticalAlign=top;fillColor=#B2EBF2;strokeColor=#80DEEA;fontSize=16;fontFamily=SimSun;fontStyle=1" 
    parent="1" vertex="1">
  <mxGeometry x="60" y="166" width="1160" height="70" as="geometry" />
</mxCell>
```

### 内容方框节点
```xml
<mxCell id="node_id" value="节点名称" 
    style="shape=rectangle;rounded=0;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#FFECB3;fillColor=#FFFFFF;fontFamily=SimSun;fontSize=14;fontColor=#000000;" 
    parent="layer_id" vertex="1">
  <mxGeometry x="80" y="33" width="120" height="30" as="geometry" />
</mxCell>
```

### 多行内容节点（使用 html=1）
```xml
<mxCell id="multi_line" value="第一行&#xa;第二行&#xa;第三行" 
    style="shape=rectangle;rounded=0;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#B39DDB;fillColor=#FFFFFF;fontFamily=SimSun;fontSize=12;fontColor=#000000;" 
    parent="1" vertex="1">
  <mxGeometry x="20" y="39" width="140" height="40" as="geometry" />
</mxCell>
```

### 折角连线
```xml
<mxCell id="arrow_id" 
    style="endArrow=block;endFill=1;rounded=0;html=1;strokeWidth=1;edgeStyle=orthogonalEdgeStyle;strokeColor=#90A4AE;" 
    parent="1" source="source_id" target="target_id" edge="1">
  <mxGeometry relative="1" as="geometry" />
</mxCell>
```

### 虚线连线
```xml
<mxCell id="dashed_arrow" 
    style="endArrow=block;endFill=1;rounded=0;html=1;strokeWidth=1;edgeStyle=orthogonalEdgeStyle;strokeColor=#90A4AE;dashed=1;" 
    parent="1" source="source_id" target="target_id" edge="1">
  <mxGeometry relative="1" as="geometry" />
</mxCell>
```

### 带拐点的连线
```xml
<mxCell id="waypoint_arrow" 
    style="endArrow=block;endFill=1;rounded=0;html=1;strokeWidth=1;edgeStyle=orthogonalEdgeStyle;strokeColor=#90A4AE;dashed=1;" 
    parent="1" source="source_id" target="target_id" edge="1">
  <mxGeometry relative="1" as="geometry">
    <Array as="points">
      <mxPoint x="460" y="480" />
      <mxPoint x="1030" y="480" />
    </Array>
  </mxGeometry>
</mxCell>
```

### 分组节点（Group）
```xml
<mxCell id="group_id" value="" style="group" parent="1" vertex="1" connectable="0">
  <mxGeometry x="60" y="166" width="1160" height="80" as="geometry" />
</mxCell>
<!-- 子节点将 parent 设为 group_id -->
```

### 说明框
```xml
<mxCell id="note" value="&lt;b&gt;说明：&lt;/b&gt;&lt;br&gt;1) 第一点说明；&lt;br&gt;2) 第二点说明；" 
    style="shape=rectangle;rounded=0;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#d79b00;fillColor=#ffe6cc;fontFamily=SimSun;fontSize=12;dashed=1;align=left;" 
    parent="1" vertex="1">
  <mxGeometry x="40" y="708" width="300" height="100" as="geometry" />
</mxCell>
```

---

## ER 图专用模板

### ER 图设计原则
1. **边先于顶点声明**：在 XML 中先声明 edge（关系线），再声明 vertex（实体），确保连线渲染在实体下方
2. **使用 swimlane 形状**：实体表使用 `shape=swimlane` 实现表头+内容的分层效果
3. **字段分行显示**：使用 `&#xa;` 换行或子节点方式展示字段
4. **关系标注清晰**：在连线上标注关系类型（1:1, 1:N, N:M）和关系名称
5. **分组按模块**：大型 ER 图按业务模块分组，使用不同颜色区分

### 实体表（Swimlane 样式，推荐）
```xml
<mxCell id="entity_user" value="用户表 (USER)" 
    style="swimlane;fontStyle=1;align=center;verticalAlign=top;childLayout=stackLayout;horizontal=1;startSize=30;horizontalStack=0;resizeParent=1;resizeParentMax=0;resizeLast=0;collapsible=0;marginBottom=0;fillColor=#E1F5FE;strokeColor=#0288D1;fontFamily=SimSun;fontSize=14;fontColor=#01579B;" 
    parent="1" vertex="1">
  <mxGeometry x="40" y="40" width="200" height="150" as="geometry" />
</mxCell>
<!-- 字段行 -->
<mxCell id="user_pk" value="🔑 user_id (PK)" 
    style="text;strokeColor=#FB8C00;fillColor=#FFF3E0;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
    parent="entity_user" vertex="1">
  <mxGeometry y="30" width="200" height="24" as="geometry" />
</mxCell>
<mxCell id="user_name" value="username VARCHAR(50)" 
    style="text;strokeColor=#0288D1;fillColor=#FFFFFF;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
    parent="entity_user" vertex="1">
  <mxGeometry y="54" width="200" height="24" as="geometry" />
</mxCell>
<mxCell id="user_email" value="email VARCHAR(100)" 
    style="text;strokeColor=#0288D1;fillColor=#FFFFFF;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
    parent="entity_user" vertex="1">
  <mxGeometry y="78" width="200" height="24" as="geometry" />
</mxCell>
```

### 实体表（简化矩形样式）
```xml
<mxCell id="entity_simple" value="&lt;b&gt;订单表 (ORDER)&lt;/b&gt;&#xa;───────────&#xa;🔑 order_id (PK)&#xa;🔗 user_id (FK)&#xa;order_date DATE&#xa;total_amount DECIMAL" 
    style="shape=rectangle;rounded=0;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#0288D1;fillColor=#E1F5FE;fontFamily=SimSun;fontSize=12;fontColor=#000000;align=left;verticalAlign=top;spacingLeft=8;spacingTop=8;" 
    parent="1" vertex="1">
  <mxGeometry x="40" y="40" width="200" height="120" as="geometry" />
</mxCell>
```

### 外键字段行
```xml
<mxCell id="order_fk_user" value="🔗 user_id (FK)" 
    style="text;strokeColor=#43A047;fillColor=#E8F5E9;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
    parent="entity_order" vertex="1">
  <mxGeometry y="54" width="200" height="24" as="geometry" />
</mxCell>
```

### 一对一关系连线 (1:1)
```xml
<mxCell id="rel_1to1" value="1:1" 
    style="edgeStyle=orthogonalEdgeStyle;rounded=0;html=1;strokeWidth=2;strokeColor=#1976D2;endArrow=none;endFill=0;startArrow=none;startFill=0;fontFamily=SimSun;fontSize=12;fontColor=#1976D2;labelBackgroundColor=#FFFFFF;" 
    parent="1" source="entity_user" target="entity_profile" edge="1">
  <mxGeometry relative="1" as="geometry" />
</mxCell>
```

### 一对多关系连线 (1:N) - Crow's Foot 风格
```xml
<mxCell id="rel_1toN" value="1:N" 
    style="edgeStyle=orthogonalEdgeStyle;rounded=0;html=1;strokeWidth=2;strokeColor=#388E3C;endArrow=ERmany;endFill=0;startArrow=ERone;startFill=0;fontFamily=SimSun;fontSize=12;fontColor=#388E3C;labelBackgroundColor=#FFFFFF;" 
    parent="1" source="entity_user" target="entity_order" edge="1">
  <mxGeometry relative="1" as="geometry" />
</mxCell>
```

### 多对多关系连线 (N:M)
```xml
<mxCell id="rel_NtoM" value="N:M" 
    style="edgeStyle=orthogonalEdgeStyle;rounded=0;html=1;strokeWidth=2;strokeColor=#7B1FA2;endArrow=ERmany;endFill=0;startArrow=ERmany;startFill=0;fontFamily=SimSun;fontSize=12;fontColor=#7B1FA2;labelBackgroundColor=#FFFFFF;" 
    parent="1" source="entity_student" target="entity_course" edge="1">
  <mxGeometry relative="1" as="geometry" />
</mxCell>
```

### 带关系名称的连线
```xml
<mxCell id="rel_named" value="下单&#xa;places" 
    style="edgeStyle=orthogonalEdgeStyle;rounded=0;html=1;strokeWidth=2;strokeColor=#388E3C;endArrow=ERmany;endFill=0;startArrow=ERone;startFill=0;fontFamily=SimSun;fontSize=11;fontColor=#388E3C;labelBackgroundColor=#FFFFFF;verticalAlign=middle;" 
    parent="1" source="entity_user" target="entity_order" edge="1">
  <mxGeometry relative="1" as="geometry" />
</mxCell>
```

### 模块分组框
```xml
<mxCell id="group_user_module" value="用户模块" 
    style="rounded=1;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#BDBDBD;fillColor=#FAFAFA;dashed=1;dashPattern=8 8;fontSize=14;fontFamily=SimSun;fontStyle=1;fontColor=#616161;align=left;verticalAlign=top;spacingLeft=10;spacingTop=5;" 
    parent="1" vertex="1">
  <mxGeometry x="20" y="20" width="450" height="300" as="geometry" />
</mxCell>
```

### ER 图箭头样式参考

| 箭头类型 | startArrow/endArrow | 说明 |
|----------|---------------------|------|
| 无箭头 | none | 普通连线 |
| 一端 (1) | ERone | Crow's Foot 一端 |
| 多端 (N) | ERmany | Crow's Foot 多端（鸦爪） |
| 零或一 | ERzeroToOne | 可选一端 |
| 一或多 | ERoneToMany | 强制多端 |
| 零或多 | ERzeroToMany | 可选多端 |
| 仅一 | ERmandOne | 强制一端 |

---

## 常用 Style 属性参考

| 属性 | 说明 | 示例值 |
|------|------|--------|
| rounded | 圆角 | 0=无圆角, 1=圆角 |
| arcSize | 圆角大小 | 4 |
| whiteSpace | 文字换行 | wrap |
| html | 启用HTML | 1 |
| strokeWidth | 边框宽度 | 1 |
| strokeColor | 边框颜色 | #90CAF9 |
| fillColor | 填充颜色 | #FFFFFF |
| fontFamily | 字体 | SimSun / Arial |
| fontSize | 字号 | 14 |
| fontColor | 字体颜色 | #000000 |
| fontStyle | 字体样式 | 1=粗体, 2=斜体, 4=下划线 |
| align | 水平对齐 | left / center / right |
| verticalAlign | 垂直对齐 | top / middle / bottom |
| spacing | 内边距 | 8 |
| dashed | 虚线 | 1 |
| edgeStyle | 连线样式 | orthogonalEdgeStyle |
| endArrow | 箭头样式 | block / classic / open |
| endFill | 箭头填充 | 1 |

## 图表类型配色方案

### 系统架构图
```
主框架: #d5e8d4 / #82b366
各层级: 使用上方配色表中的不同颜色区分
连线: #90A4AE
```

### 流程图
```
开始/结束: #E8F5E9 / #81C784
处理步骤: #E3F2FD / #90CAF9
判断: #FFF3E0 / #FFCC80
连线: #90A4AE
```

### ER 图
```
实体表头: #E1F5FE / #0288D1 (蓝色系)
实体内容: #FFFFFF / #0288D1
主键字段: #FFF3E0 / #FB8C00 (橙色高亮)
外键字段: #E8F5E9 / #43A047 (绿色高亮)
普通字段: #FFFFFF / #0288D1
关系线(1:1): #1976D2 (蓝色)
关系线(1:N): #388E3C (绿色)
关系线(N:M): #7B1FA2 (紫色)
模块分组: #FAFAFA / #BDBDBD (灰色虚线)
```

## 最佳实践

### 通用最佳实践

1. **布局规划**：先规划好各层级的位置和大小，再填充内容节点
2. **ID 命名**：使用有意义的 ID，如 `layer_frontend`、`node_auth`
3. **层级关系**：使用 `parent` 属性建立正确的层级关系
4. **间距一致**：保持节点间距一致，通常使用 10 的倍数
5. **颜色协调**：同一层级使用相同配色，不同层级使用不同配色

### ER 图最佳实践

1. **边先声明**：在 XML 中先声明所有 edge，再声明 vertex，确保连线在实体下方渲染
2. **模块化分组**：大型数据库按业务域分组（用户、订单、库存等），每组使用虚线框
3. **统一命名**：实体 ID 使用 `entity_表名`，关系 ID 使用 `rel_源表_目标表`
4. **字段标注**：主键用 🔑 标记，外键用 🔗 标记，便于识别
5. **关系类型颜色区分**：1:1 用蓝色，1:N 用绿色，N:M 用紫色
6. **使用 Crow's Foot 符号**：标准化关系表示法，`ERone` 和 `ERmany`
7. **表宽一致**：同一区域的实体表保持相同宽度（如 200px）
8. **字段行高统一**：每个字段行高保持一致（如 24px）
9. **避免交叉**：合理布局减少连线交叉，必要时使用拐点
10. **保持图表更新**：数据库结构变更时同步更新 ER 图
