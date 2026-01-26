# Draw.io 示例参考

## 完整的系统架构图示例

以下是一个完整的三层架构图示例：

```xml
<mxfile host="localhost" version="26.0.4">
  <diagram id="example" name="三层架构示例">
    <mxGraphModel dx="1000" dy="600" grid="1" gridSize="10" guides="1" tooltips="1" 
        connect="1" arrows="1" fold="1" page="1" pageScale="1" 
        pageWidth="1200" pageHeight="800" background="#FFFFFF" math="0" shadow="0">
      <root>
        <mxCell id="0" />
        <mxCell id="1" parent="0" />
        
        <!-- 前端层 -->
        <mxCell id="layer_ui" value="表现层 (UI)" 
            style="rounded=1;whiteSpace=wrap;html=1;strokeWidth=1;spacing=8;align=left;verticalAlign=top;fillColor=#FFF8E1;strokeColor=#FFECB3;fontSize=16;fontFamily=SimSun;fontStyle=1" 
            parent="1" vertex="1">
          <mxGeometry x="40" y="40" width="400" height="80" as="geometry" />
        </mxCell>
        <mxCell id="ui_web" value="Web界面" 
            style="shape=rectangle;rounded=0;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#FFECB3;fillColor=#FFFFFF;fontFamily=SimSun;fontSize=14;fontColor=#000000;" 
            parent="1" vertex="1">
          <mxGeometry x="80" y="70" width="100" height="30" as="geometry" />
        </mxCell>
        <mxCell id="ui_api" value="API接口" 
            style="shape=rectangle;rounded=0;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#FFECB3;fillColor=#FFFFFF;fontFamily=SimSun;fontSize=14;fontColor=#000000;" 
            parent="1" vertex="1">
          <mxGeometry x="200" y="70" width="100" height="30" as="geometry" />
        </mxCell>
        
        <!-- 业务层 -->
        <mxCell id="layer_biz" value="业务逻辑层" 
            style="rounded=1;whiteSpace=wrap;html=1;strokeWidth=1;spacing=8;align=left;verticalAlign=top;fillColor=#B2EBF2;strokeColor=#80DEEA;fontSize=16;fontFamily=SimSun;fontStyle=1" 
            parent="1" vertex="1">
          <mxGeometry x="40" y="160" width="400" height="80" as="geometry" />
        </mxCell>
        <mxCell id="biz_service" value="业务服务" 
            style="shape=rectangle;rounded=0;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#80DEEA;fillColor=#FFFFFF;fontFamily=SimSun;fontSize=14;fontColor=#000000;" 
            parent="1" vertex="1">
          <mxGeometry x="80" y="190" width="100" height="30" as="geometry" />
        </mxCell>
        <mxCell id="biz_rules" value="业务规则" 
            style="shape=rectangle;rounded=0;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#80DEEA;fillColor=#FFFFFF;fontFamily=SimSun;fontSize=14;fontColor=#000000;" 
            parent="1" vertex="1">
          <mxGeometry x="200" y="190" width="100" height="30" as="geometry" />
        </mxCell>
        
        <!-- 数据层 -->
        <mxCell id="layer_data" value="数据访问层" 
            style="rounded=1;whiteSpace=wrap;html=1;strokeWidth=1;spacing=8;align=left;verticalAlign=top;fillColor=#DCEDC8;strokeColor=#C5E1A5;fontSize=16;fontFamily=SimSun;fontStyle=1" 
            parent="1" vertex="1">
          <mxGeometry x="40" y="280" width="400" height="80" as="geometry" />
        </mxCell>
        <mxCell id="data_db" value="数据库&#xa;(MySQL)" 
            style="shape=rectangle;rounded=0;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#C5E1A5;fillColor=#FFFFFF;fontFamily=SimSun;fontSize=14;fontColor=#000000;" 
            parent="1" vertex="1">
          <mxGeometry x="80" y="310" width="100" height="30" as="geometry" />
        </mxCell>
        <mxCell id="data_cache" value="缓存&#xa;(Redis)" 
            style="shape=rectangle;rounded=0;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#EF9A9A;fillColor=#FFEBEE;fontFamily=SimSun;fontSize=14;fontColor=#000000;" 
            parent="1" vertex="1">
          <mxGeometry x="200" y="310" width="100" height="30" as="geometry" />
        </mxCell>
        
        <!-- 连线 -->
        <mxCell id="arrow1" 
            style="endArrow=block;endFill=1;rounded=0;html=1;strokeWidth=1;edgeStyle=orthogonalEdgeStyle;strokeColor=#90A4AE;" 
            parent="1" source="layer_ui" target="layer_biz" edge="1">
          <mxGeometry relative="1" as="geometry" />
        </mxCell>
        <mxCell id="arrow2" 
            style="endArrow=block;endFill=1;rounded=0;html=1;strokeWidth=1;edgeStyle=orthogonalEdgeStyle;strokeColor=#90A4AE;" 
            parent="1" source="layer_biz" target="layer_data" edge="1">
          <mxGeometry relative="1" as="geometry" />
        </mxCell>
        
      </root>
    </mxGraphModel>
  </diagram>
</mxfile>
```

## 流程图示例

```xml
<mxfile host="localhost" version="26.0.4">
  <diagram id="flow" name="简单流程图">
    <mxGraphModel dx="800" dy="600" grid="1" gridSize="10" guides="1" tooltips="1" 
        connect="1" arrows="1" fold="1" page="1" pageScale="1" 
        pageWidth="1000" pageHeight="600" background="#FFFFFF" math="0" shadow="0">
      <root>
        <mxCell id="0" />
        <mxCell id="1" parent="0" />
        
        <!-- 开始 -->
        <mxCell id="start" value="开始" 
            style="ellipse;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#81C784;fillColor=#E8F5E9;fontFamily=SimSun;fontSize=14;fontColor=#000000;" 
            parent="1" vertex="1">
          <mxGeometry x="100" y="40" width="80" height="40" as="geometry" />
        </mxCell>
        
        <!-- 处理步骤 -->
        <mxCell id="step1" value="步骤1：输入数据" 
            style="shape=rectangle;rounded=0;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#90CAF9;fillColor=#E3F2FD;fontFamily=SimSun;fontSize=14;fontColor=#000000;" 
            parent="1" vertex="1">
          <mxGeometry x="70" y="120" width="140" height="40" as="geometry" />
        </mxCell>
        
        <!-- 判断 -->
        <mxCell id="decision" value="数据有效？" 
            style="rhombus;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#FFCC80;fillColor=#FFF3E0;fontFamily=SimSun;fontSize=14;fontColor=#000000;" 
            parent="1" vertex="1">
          <mxGeometry x="80" y="200" width="120" height="60" as="geometry" />
        </mxCell>
        
        <!-- 处理步骤 -->
        <mxCell id="step2" value="步骤2：处理数据" 
            style="shape=rectangle;rounded=0;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#90CAF9;fillColor=#E3F2FD;fontFamily=SimSun;fontSize=14;fontColor=#000000;" 
            parent="1" vertex="1">
          <mxGeometry x="70" y="300" width="140" height="40" as="geometry" />
        </mxCell>
        
        <!-- 错误处理 -->
        <mxCell id="error" value="显示错误信息" 
            style="shape=rectangle;rounded=0;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#EF9A9A;fillColor=#FFEBEE;fontFamily=SimSun;fontSize=14;fontColor=#000000;" 
            parent="1" vertex="1">
          <mxGeometry x="260" y="210" width="120" height="40" as="geometry" />
        </mxCell>
        
        <!-- 结束 -->
        <mxCell id="end" value="结束" 
            style="ellipse;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#81C784;fillColor=#E8F5E9;fontFamily=SimSun;fontSize=14;fontColor=#000000;" 
            parent="1" vertex="1">
          <mxGeometry x="100" y="380" width="80" height="40" as="geometry" />
        </mxCell>
        
        <!-- 连线 -->
        <mxCell id="a1" 
            style="endArrow=block;endFill=1;rounded=0;html=1;strokeWidth=1;edgeStyle=orthogonalEdgeStyle;strokeColor=#90A4AE;" 
            parent="1" source="start" target="step1" edge="1">
          <mxGeometry relative="1" as="geometry" />
        </mxCell>
        <mxCell id="a2" 
            style="endArrow=block;endFill=1;rounded=0;html=1;strokeWidth=1;edgeStyle=orthogonalEdgeStyle;strokeColor=#90A4AE;" 
            parent="1" source="step1" target="decision" edge="1">
          <mxGeometry relative="1" as="geometry" />
        </mxCell>
        <mxCell id="a3" value="是" 
            style="endArrow=block;endFill=1;rounded=0;html=1;strokeWidth=1;edgeStyle=orthogonalEdgeStyle;strokeColor=#90A4AE;" 
            parent="1" source="decision" target="step2" edge="1">
          <mxGeometry relative="1" as="geometry" />
        </mxCell>
        <mxCell id="a4" value="否" 
            style="endArrow=block;endFill=1;rounded=0;html=1;strokeWidth=1;edgeStyle=orthogonalEdgeStyle;strokeColor=#90A4AE;" 
            parent="1" source="decision" target="error" edge="1">
          <mxGeometry relative="1" as="geometry" />
        </mxCell>
        <mxCell id="a5" 
            style="endArrow=block;endFill=1;rounded=0;html=1;strokeWidth=1;edgeStyle=orthogonalEdgeStyle;strokeColor=#90A4AE;" 
            parent="1" source="step2" target="end" edge="1">
          <mxGeometry relative="1" as="geometry" />
        </mxCell>
        <mxCell id="a6" 
            style="endArrow=block;endFill=1;rounded=0;html=1;strokeWidth=1;edgeStyle=orthogonalEdgeStyle;strokeColor=#90A4AE;" 
            parent="1" source="error" target="step1" edge="1">
          <mxGeometry relative="1" as="geometry">
            <Array as="points">
              <mxPoint x="320" y="90" />
              <mxPoint x="140" y="90" />
            </Array>
          </mxGeometry>
        </mxCell>
        
      </root>
    </mxGraphModel>
  </diagram>
</mxfile>
```

## 常用形状样式

### 矩形（处理步骤）
```
style="shape=rectangle;rounded=0;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#90CAF9;fillColor=#E3F2FD;fontFamily=SimSun;fontSize=14;fontColor=#000000;"
```

### 椭圆（开始/结束）
```
style="ellipse;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#81C784;fillColor=#E8F5E9;fontFamily=SimSun;fontSize=14;fontColor=#000000;"
```

### 菱形（判断/决策）
```
style="rhombus;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#FFCC80;fillColor=#FFF3E0;fontFamily=SimSun;fontSize=14;fontColor=#000000;"
```

### 圆角矩形（容器/层级）
```
style="rounded=1;arcSize=4;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#82b366;fillColor=#d5e8d4;fontFamily=SimSun;fontSize=16;fontStyle=1;"
```

### 平行四边形（输入/输出）
```
style="shape=parallelogram;perimeter=parallelogramPerimeter;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#90CAF9;fillColor=#E3F2FD;fontFamily=SimSun;fontSize=14;"
```

## 多行文本示例

使用 `&#xa;` 进行换行（html=1 时）：

```xml
<mxCell id="multiline" value="第一行内容&#xa;第二行内容&#xa;第三行内容" 
    style="shape=rectangle;rounded=0;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#90CAF9;fillColor=#E3F2FD;fontFamily=SimSun;fontSize=14;fontColor=#000000;" 
    parent="1" vertex="1">
  <mxGeometry x="40" y="40" width="150" height="60" as="geometry" />
</mxCell>
```

使用 HTML 标签进行换行：

```xml
<mxCell id="html_multiline" value="&lt;b&gt;标题&lt;/b&gt;&lt;br&gt;第一行&lt;br&gt;第二行" 
    style="shape=rectangle;rounded=0;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#90CAF9;fillColor=#E3F2FD;fontFamily=SimSun;fontSize=14;fontColor=#000000;" 
    parent="1" vertex="1">
  <mxGeometry x="40" y="40" width="150" height="60" as="geometry" />
</mxCell>
```

---

## ER 图（实体关系图）示例

### 完整的电商系统 ER 图示例

以下是一个包含用户、订单、产品的电商系统 ER 图，使用 Swimlane 样式和 Crow's Foot 关系符号：

```xml
<mxfile host="localhost" version="26.0.4">
  <diagram id="er_ecommerce" name="电商系统ER图">
    <mxGraphModel dx="1200" dy="800" grid="1" gridSize="10" guides="1" tooltips="1" 
        connect="1" arrows="1" fold="1" page="1" pageScale="1" 
        pageWidth="1400" pageHeight="900" background="#FFFFFF" math="0" shadow="0">
      <root>
        <mxCell id="0" />
        <mxCell id="1" parent="0" />
        
        <!-- ========== 关系连线（先声明，渲染在实体下方）========== -->
        
        <!-- 用户 1:N 订单 -->
        <mxCell id="rel_user_order" value="1:N&#xa;下单" 
            style="edgeStyle=orthogonalEdgeStyle;rounded=0;html=1;strokeWidth=2;strokeColor=#388E3C;endArrow=ERmany;endFill=0;startArrow=ERone;startFill=0;fontFamily=SimSun;fontSize=11;fontColor=#388E3C;labelBackgroundColor=#FFFFFF;exitX=1;exitY=0.5;exitDx=0;exitDy=0;entryX=0;entryY=0.5;entryDx=0;entryDy=0;" 
            parent="1" source="user_email" target="order_fk_user" edge="1">
          <mxGeometry relative="1" as="geometry" />
        </mxCell>
        
        <!-- 订单 N:1 产品（通过订单明细） -->
        <mxCell id="rel_order_detail" value="1:N&#xa;包含" 
            style="edgeStyle=orthogonalEdgeStyle;rounded=0;html=1;strokeWidth=2;strokeColor=#388E3C;endArrow=ERmany;endFill=0;startArrow=ERone;startFill=0;fontFamily=SimSun;fontSize=11;fontColor=#388E3C;labelBackgroundColor=#FFFFFF;exitX=1;exitY=0.5;exitDx=0;exitDy=0;entryX=0;entryY=0.5;entryDx=0;entryDy=0;" 
            parent="1" source="order_status" target="detail_fk_order" edge="1">
          <mxGeometry relative="1" as="geometry" />
        </mxCell>
        
        <!-- 订单明细 N:1 产品 -->
        <mxCell id="rel_detail_product" value="N:1&#xa;关联" 
            style="edgeStyle=orthogonalEdgeStyle;rounded=0;html=1;strokeWidth=2;strokeColor=#1976D2;endArrow=ERone;endFill=0;startArrow=ERmany;startFill=0;fontFamily=SimSun;fontSize=11;fontColor=#1976D2;labelBackgroundColor=#FFFFFF;exitX=1;exitY=0.5;exitDx=0;exitDy=0;entryX=0;entryY=0.5;entryDx=0;entryDy=0;" 
            parent="1" source="detail_fk_product" target="product_name" edge="1">
          <mxGeometry relative="1" as="geometry" />
        </mxCell>
        
        <!-- ========== 模块分组框 ========== -->
        
        <!-- 用户模块 -->
        <mxCell id="group_user" value="用户模块" 
            style="rounded=1;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#BDBDBD;fillColor=#FAFAFA;dashed=1;dashPattern=8 8;fontSize=14;fontFamily=SimSun;fontStyle=1;fontColor=#616161;align=left;verticalAlign=top;spacingLeft=10;spacingTop=5;" 
            parent="1" vertex="1">
          <mxGeometry x="20" y="20" width="240" height="220" as="geometry" />
        </mxCell>
        
        <!-- 订单模块 -->
        <mxCell id="group_order" value="订单模块" 
            style="rounded=1;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#BDBDBD;fillColor=#FAFAFA;dashed=1;dashPattern=8 8;fontSize=14;fontFamily=SimSun;fontStyle=1;fontColor=#616161;align=left;verticalAlign=top;spacingLeft=10;spacingTop=5;" 
            parent="1" vertex="1">
          <mxGeometry x="300" y="20" width="480" height="220" as="geometry" />
        </mxCell>
        
        <!-- 产品模块 -->
        <mxCell id="group_product" value="产品模块" 
            style="rounded=1;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#BDBDBD;fillColor=#FAFAFA;dashed=1;dashPattern=8 8;fontSize=14;fontFamily=SimSun;fontStyle=1;fontColor=#616161;align=left;verticalAlign=top;spacingLeft=10;spacingTop=5;" 
            parent="1" vertex="1">
          <mxGeometry x="820" y="20" width="240" height="220" as="geometry" />
        </mxCell>
        
        <!-- ========== 用户表 (USER) ========== -->
        <mxCell id="entity_user" value="用户表 (USER)" 
            style="swimlane;fontStyle=1;align=center;verticalAlign=top;childLayout=stackLayout;horizontal=1;startSize=30;horizontalStack=0;resizeParent=1;resizeParentMax=0;resizeLast=0;collapsible=0;marginBottom=0;fillColor=#E1F5FE;strokeColor=#0288D1;fontFamily=SimSun;fontSize=14;fontColor=#01579B;" 
            parent="1" vertex="1">
          <mxGeometry x="40" y="50" width="200" height="174" as="geometry" />
        </mxCell>
        <mxCell id="user_pk" value="🔑 user_id INT (PK)" 
            style="text;strokeColor=#FB8C00;fillColor=#FFF3E0;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
            parent="entity_user" vertex="1">
          <mxGeometry y="30" width="200" height="24" as="geometry" />
        </mxCell>
        <mxCell id="user_name" value="username VARCHAR(50)" 
            style="text;strokeColor=#0288D1;fillColor=#FFFFFF;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
            parent="entity_user" vertex="1">
          <mxGeometry y="54" width="200" height="24" as="geometry" />
        </mxCell>
        <mxCell id="user_password" value="password VARCHAR(255)" 
            style="text;strokeColor=#0288D1;fillColor=#FFFFFF;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
            parent="entity_user" vertex="1">
          <mxGeometry y="78" width="200" height="24" as="geometry" />
        </mxCell>
        <mxCell id="user_email" value="email VARCHAR(100)" 
            style="text;strokeColor=#0288D1;fillColor=#FFFFFF;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
            parent="entity_user" vertex="1">
          <mxGeometry y="102" width="200" height="24" as="geometry" />
        </mxCell>
        <mxCell id="user_phone" value="phone VARCHAR(20)" 
            style="text;strokeColor=#0288D1;fillColor=#FFFFFF;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
            parent="entity_user" vertex="1">
          <mxGeometry y="126" width="200" height="24" as="geometry" />
        </mxCell>
        <mxCell id="user_created" value="created_at DATETIME" 
            style="text;strokeColor=#0288D1;fillColor=#FFFFFF;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
            parent="entity_user" vertex="1">
          <mxGeometry y="150" width="200" height="24" as="geometry" />
        </mxCell>
        
        <!-- ========== 订单表 (ORDER) ========== -->
        <mxCell id="entity_order" value="订单表 (ORDER)" 
            style="swimlane;fontStyle=1;align=center;verticalAlign=top;childLayout=stackLayout;horizontal=1;startSize=30;horizontalStack=0;resizeParent=1;resizeParentMax=0;resizeLast=0;collapsible=0;marginBottom=0;fillColor=#E1F5FE;strokeColor=#0288D1;fontFamily=SimSun;fontSize=14;fontColor=#01579B;" 
            parent="1" vertex="1">
          <mxGeometry x="320" y="50" width="200" height="174" as="geometry" />
        </mxCell>
        <mxCell id="order_pk" value="🔑 order_id INT (PK)" 
            style="text;strokeColor=#FB8C00;fillColor=#FFF3E0;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
            parent="entity_order" vertex="1">
          <mxGeometry y="30" width="200" height="24" as="geometry" />
        </mxCell>
        <mxCell id="order_fk_user" value="🔗 user_id INT (FK)" 
            style="text;strokeColor=#43A047;fillColor=#E8F5E9;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
            parent="entity_order" vertex="1">
          <mxGeometry y="54" width="200" height="24" as="geometry" />
        </mxCell>
        <mxCell id="order_date" value="order_date DATETIME" 
            style="text;strokeColor=#0288D1;fillColor=#FFFFFF;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
            parent="entity_order" vertex="1">
          <mxGeometry y="78" width="200" height="24" as="geometry" />
        </mxCell>
        <mxCell id="order_total" value="total_amount DECIMAL" 
            style="text;strokeColor=#0288D1;fillColor=#FFFFFF;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
            parent="entity_order" vertex="1">
          <mxGeometry y="102" width="200" height="24" as="geometry" />
        </mxCell>
        <mxCell id="order_status" value="status VARCHAR(20)" 
            style="text;strokeColor=#0288D1;fillColor=#FFFFFF;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
            parent="entity_order" vertex="1">
          <mxGeometry y="126" width="200" height="24" as="geometry" />
        </mxCell>
        <mxCell id="order_remark" value="remark VARCHAR(500)" 
            style="text;strokeColor=#0288D1;fillColor=#FFFFFF;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
            parent="entity_order" vertex="1">
          <mxGeometry y="150" width="200" height="24" as="geometry" />
        </mxCell>
        
        <!-- ========== 订单明细表 (ORDER_DETAIL) ========== -->
        <mxCell id="entity_detail" value="订单明细 (ORDER_DETAIL)" 
            style="swimlane;fontStyle=1;align=center;verticalAlign=top;childLayout=stackLayout;horizontal=1;startSize=30;horizontalStack=0;resizeParent=1;resizeParentMax=0;resizeLast=0;collapsible=0;marginBottom=0;fillColor=#E1F5FE;strokeColor=#0288D1;fontFamily=SimSun;fontSize=14;fontColor=#01579B;" 
            parent="1" vertex="1">
          <mxGeometry x="560" y="50" width="200" height="150" as="geometry" />
        </mxCell>
        <mxCell id="detail_pk" value="🔑 detail_id INT (PK)" 
            style="text;strokeColor=#FB8C00;fillColor=#FFF3E0;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
            parent="entity_detail" vertex="1">
          <mxGeometry y="30" width="200" height="24" as="geometry" />
        </mxCell>
        <mxCell id="detail_fk_order" value="🔗 order_id INT (FK)" 
            style="text;strokeColor=#43A047;fillColor=#E8F5E9;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
            parent="entity_detail" vertex="1">
          <mxGeometry y="54" width="200" height="24" as="geometry" />
        </mxCell>
        <mxCell id="detail_fk_product" value="🔗 product_id INT (FK)" 
            style="text;strokeColor=#43A047;fillColor=#E8F5E9;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
            parent="entity_detail" vertex="1">
          <mxGeometry y="78" width="200" height="24" as="geometry" />
        </mxCell>
        <mxCell id="detail_qty" value="quantity INT" 
            style="text;strokeColor=#0288D1;fillColor=#FFFFFF;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
            parent="entity_detail" vertex="1">
          <mxGeometry y="102" width="200" height="24" as="geometry" />
        </mxCell>
        <mxCell id="detail_price" value="unit_price DECIMAL" 
            style="text;strokeColor=#0288D1;fillColor=#FFFFFF;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
            parent="entity_detail" vertex="1">
          <mxGeometry y="126" width="200" height="24" as="geometry" />
        </mxCell>
        
        <!-- ========== 产品表 (PRODUCT) ========== -->
        <mxCell id="entity_product" value="产品表 (PRODUCT)" 
            style="swimlane;fontStyle=1;align=center;verticalAlign=top;childLayout=stackLayout;horizontal=1;startSize=30;horizontalStack=0;resizeParent=1;resizeParentMax=0;resizeLast=0;collapsible=0;marginBottom=0;fillColor=#E1F5FE;strokeColor=#0288D1;fontFamily=SimSun;fontSize=14;fontColor=#01579B;" 
            parent="1" vertex="1">
          <mxGeometry x="840" y="50" width="200" height="174" as="geometry" />
        </mxCell>
        <mxCell id="product_pk" value="🔑 product_id INT (PK)" 
            style="text;strokeColor=#FB8C00;fillColor=#FFF3E0;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
            parent="entity_product" vertex="1">
          <mxGeometry y="30" width="200" height="24" as="geometry" />
        </mxCell>
        <mxCell id="product_name" value="name VARCHAR(100)" 
            style="text;strokeColor=#0288D1;fillColor=#FFFFFF;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
            parent="entity_product" vertex="1">
          <mxGeometry y="54" width="200" height="24" as="geometry" />
        </mxCell>
        <mxCell id="product_desc" value="description TEXT" 
            style="text;strokeColor=#0288D1;fillColor=#FFFFFF;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
            parent="entity_product" vertex="1">
          <mxGeometry y="78" width="200" height="24" as="geometry" />
        </mxCell>
        <mxCell id="product_price" value="price DECIMAL(10,2)" 
            style="text;strokeColor=#0288D1;fillColor=#FFFFFF;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
            parent="entity_product" vertex="1">
          <mxGeometry y="102" width="200" height="24" as="geometry" />
        </mxCell>
        <mxCell id="product_stock" value="stock INT" 
            style="text;strokeColor=#0288D1;fillColor=#FFFFFF;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
            parent="entity_product" vertex="1">
          <mxGeometry y="126" width="200" height="24" as="geometry" />
        </mxCell>
        <mxCell id="product_status" value="status TINYINT" 
            style="text;strokeColor=#0288D1;fillColor=#FFFFFF;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;" 
            parent="entity_product" vertex="1">
          <mxGeometry y="150" width="200" height="24" as="geometry" />
        </mxCell>
        
        <!-- ========== 图例说明 ========== -->
        <mxCell id="legend" value="&lt;b&gt;图例说明：&lt;/b&gt;&lt;br&gt;🔑 主键 (PK)&lt;br&gt;🔗 外键 (FK)&lt;br&gt;━━ 1:N 一对多关系 (绿色)&lt;br&gt;━━ N:1 多对一关系 (蓝色)" 
            style="shape=rectangle;rounded=0;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#d79b00;fillColor=#ffe6cc;fontFamily=SimSun;fontSize=12;dashed=1;align=left;spacingLeft=8;spacingTop=4;" 
            parent="1" vertex="1">
          <mxGeometry x="20" y="260" width="200" height="100" as="geometry" />
        </mxCell>
        
      </root>
    </mxGraphModel>
  </diagram>
</mxfile>
```

### 简化版 ER 图（矩形样式）

适合快速绘制小型数据库设计：

```xml
<mxfile host="localhost" version="26.0.4">
  <diagram id="er_simple" name="简化ER图">
    <mxGraphModel dx="800" dy="600" grid="1" gridSize="10" guides="1" tooltips="1" 
        connect="1" arrows="1" fold="1" page="1" pageScale="1" 
        pageWidth="1000" pageHeight="600" background="#FFFFFF" math="0" shadow="0">
      <root>
        <mxCell id="0" />
        <mxCell id="1" parent="0" />
        
        <!-- 关系连线先声明 -->
        <mxCell id="rel1" value="1:N" 
            style="edgeStyle=orthogonalEdgeStyle;rounded=0;html=1;strokeWidth=2;strokeColor=#388E3C;endArrow=ERmany;endFill=0;startArrow=ERone;startFill=0;fontFamily=SimSun;fontSize=11;fontColor=#388E3C;labelBackgroundColor=#FFFFFF;" 
            parent="1" source="tbl_user" target="tbl_order" edge="1">
          <mxGeometry relative="1" as="geometry" />
        </mxCell>
        
        <!-- 用户表 -->
        <mxCell id="tbl_user" value="&lt;b&gt;用户表 USER&lt;/b&gt;&#xa;─────────────&#xa;🔑 user_id INT&#xa;   username VARCHAR(50)&#xa;   email VARCHAR(100)&#xa;   created_at DATETIME" 
            style="shape=rectangle;rounded=0;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#0288D1;fillColor=#E1F5FE;fontFamily=SimSun;fontSize=12;fontColor=#000000;align=left;verticalAlign=top;spacingLeft=8;spacingTop=8;" 
            parent="1" vertex="1">
          <mxGeometry x="40" y="80" width="180" height="120" as="geometry" />
        </mxCell>
        
        <!-- 订单表 -->
        <mxCell id="tbl_order" value="&lt;b&gt;订单表 ORDER&lt;/b&gt;&#xa;─────────────&#xa;🔑 order_id INT&#xa;🔗 user_id INT&#xa;   order_date DATE&#xa;   total_amount DECIMAL" 
            style="shape=rectangle;rounded=0;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#0288D1;fillColor=#E1F5FE;fontFamily=SimSun;fontSize=12;fontColor=#000000;align=left;verticalAlign=top;spacingLeft=8;spacingTop=8;" 
            parent="1" vertex="1">
          <mxGeometry x="320" y="80" width="180" height="120" as="geometry" />
        </mxCell>
        
      </root>
    </mxGraphModel>
  </diagram>
</mxfile>
```

## ER 图样式汇总

### 实体表头样式（Swimlane）
```
style="swimlane;fontStyle=1;align=center;verticalAlign=top;childLayout=stackLayout;horizontal=1;startSize=30;horizontalStack=0;resizeParent=1;resizeParentMax=0;resizeLast=0;collapsible=0;marginBottom=0;fillColor=#E1F5FE;strokeColor=#0288D1;fontFamily=SimSun;fontSize=14;fontColor=#01579B;"
```

### 主键字段行样式
```
style="text;strokeColor=#FB8C00;fillColor=#FFF3E0;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;"
```

### 外键字段行样式
```
style="text;strokeColor=#43A047;fillColor=#E8F5E9;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;"
```

### 普通字段行样式
```
style="text;strokeColor=#0288D1;fillColor=#FFFFFF;align=left;verticalAlign=middle;spacingLeft=4;spacingRight=4;overflow=hidden;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;rotatable=0;whiteSpace=wrap;html=1;fontFamily=SimSun;fontSize=12;"
```

### 一对多关系连线 (1:N)
```
style="edgeStyle=orthogonalEdgeStyle;rounded=0;html=1;strokeWidth=2;strokeColor=#388E3C;endArrow=ERmany;endFill=0;startArrow=ERone;startFill=0;fontFamily=SimSun;fontSize=11;fontColor=#388E3C;labelBackgroundColor=#FFFFFF;"
```

### 模块分组框样式
```
style="rounded=1;whiteSpace=wrap;html=1;strokeWidth=1;strokeColor=#BDBDBD;fillColor=#FAFAFA;dashed=1;dashPattern=8 8;fontSize=14;fontFamily=SimSun;fontStyle=1;fontColor=#616161;align=left;verticalAlign=top;spacingLeft=10;spacingTop=5;"
```
