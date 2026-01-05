# 输出模式 (Output Patterns)

当技能需要产生一致、高质量的输出时，请使用这些模式。

## 模板模式 (Template Pattern)

为输出格式提供模板。将严格程度与您的需求相匹配。

**对于严格要求（如 API 响应或数据格式）：**

```markdown
## 报告结构

始终使用此确切的模板结构：

# [分析标题]

## 执行摘要
[关键发现的一段概述]

## 关键发现
- 发现 1 及支持数据
- 发现 2 及支持数据
- 发现 3 及支持数据

## 建议
1. 具体的行动建议
2. 具体的行动建议
```

**对于灵活指导（当适应性有用时）：**

```markdown
## 报告结构

这是一个合理的默认格式，但请根据您的判断使用：

# [分析标题]

## 执行摘要
[概述]

## 关键发现
[根据您的发现调整部分]

## 建议
[针对具体情况进行调整]

根据具体的分析类型调整部分。
```

## 示例模式 (Examples Pattern)

对于输出质量取决于查看示例的技能，提供输入/输出对：

```markdown
## 提交消息格式

按照以下示例生成提交消息：

**示例 1：**
输入：添加了使用 JWT 令牌的用户身份验证
输出：
```
feat(auth): implement JWT-based authentication

Add login endpoint and token validation middleware
```

**示例 2：**
输入：修复了报告中日期显示不正确的错误
输出：
```
fix(reports): correct date formatting in timezone conversion

Use UTC timestamps consistently across report generation
```

遵循此风格：type(scope): brief description，然后是 detailed explanation。
```

示例比单独的描述更能帮助 Claude 清楚地理解所需的风格和细节水平。
