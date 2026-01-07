---
name: internal-comms
description: 一组用于撰写各类公司内部沟通的资源，遵循我所在公司偏好的格式。当被要求撰写任何内部沟通材料时（状态汇报、领导更新、3P 更新、公司通讯、FAQ、事故复盘、项目更新等），Claude 都应使用此技能。
license: Complete terms in LICENSE.txt
---

## 何时使用此技能
用于撰写内部沟通时，在以下场景使用此技能：
- 3P 更新（Progress, Plans, Problems）
- 公司通讯/新闻简报
- FAQ 回复
- 状态汇报
- 领导更新
- 项目更新
- 事故报告/复盘

## 如何使用此技能

撰写任意内部沟通时：

1. 从需求中 **识别沟通类型**
2. 在 `examples/` 目录中 **加载对应的指南文件**：
    - `examples/3p-updates.md` - Progress/Plans/Problems 团队更新
    - `examples/company-newsletter.md` - 公司范围通讯/新闻简报
    - `examples/faq-answers.md` - 回答常见问题
    - `examples/general-comms.md` - 不明确匹配以上类型的其他内部沟通
3. 按该文件的具体说明 **执行格式、语气与信息收集要求**

如果沟通类型无法匹配现有指南，需要询问澄清或补充上下文，以确定期望的格式。

## Keywords
3P updates, company newsletter, company comms, weekly update, faqs, common questions, updates, internal comms
