---
name: mcp-builder
description: 用于创建高质量 MCP（Model Context Protocol）服务器的指南，通过良好设计的工具让 LLM 与外部服务交互。适用于构建用于集成外部 API 或服务的 MCP 服务器，无论是 Python（FastMCP）还是 Node/TypeScript（MCP SDK）。
license: Complete terms in LICENSE.txt
---

# MCP 服务器开发指南

## 概览

创建 MCP（Model Context Protocol）服务器，通过良好设计的工具让 LLM 与外部服务交互。MCP 服务器的质量取决于它在多大程度上帮助 LLM 完成真实世界任务。

---

# 流程

## 高层工作流

创建高质量 MCP 服务器通常包含四个主要阶段：

### 阶段 1：深度调研与规划

#### 1.1 理解现代 MCP 设计

**API 覆盖 vs. 工作流工具：**
在“尽量覆盖 API 端点”和“提供专门的工作流工具”之间取得平衡。工作流工具对特定任务可能更方便，而全面覆盖能让智能体自由组合操作。不同客户端的表现不同——有些客户端适合通过代码执行来组合基础工具，有些则更适合高层工作流。拿不准时，优先做全面 API 覆盖。

**工具命名与可发现性：**
清晰、描述性强的工具名称能帮助智能体快速找到合适工具。使用一致的前缀（如 `github_create_issue`、`github_list_repos`）并采用动作导向命名。

**上下文管理：**
智能体更受益于简洁的工具描述，以及对结果进行过滤/分页的能力。设计工具时应返回聚焦且相关的数据。有些客户端支持代码执行，可帮助智能体高效过滤与处理数据。

**可执行的错误信息：**
错误信息应提供明确建议与下一步，引导智能体走向可行的解决方案。

#### 1.2 学习 MCP 协议文档

**查阅 MCP 规范：**

先从站点地图定位相关页面：`https://modelcontextprotocol.io/sitemap.xml`

然后通过在 URL 后追加 `.md` 获取 markdown 格式页面（例如 `https://modelcontextprotocol.io/specification/draft.md`）。

建议重点阅读：
- 规范概览与架构
- 传输机制（streamable HTTP、stdio）
- 工具、资源与 prompt 的定义

#### 1.3 学习框架文档

**推荐技术栈：**
- **语言**：TypeScript（SDK 支持质量高，在许多执行环境中兼容性好，例如 MCPB；同时 AI 模型也更擅长生成 TypeScript，且其使用广泛、静态类型与 lint 工具完善）
- **传输**：远程服务器优先使用 streamable HTTP，并使用无状态 JSON（更易扩展与维护，相比有状态会话与流式响应更简单）；本地服务器使用 stdio。

**加载框架文档：**

- **MCP 最佳实践**：[查看最佳实践](./reference/mcp_best_practices.md) - 核心指南

**TypeScript（推荐）：**
- **TypeScript SDK**：用 WebFetch 加载 `https://raw.githubusercontent.com/modelcontextprotocol/typescript-sdk/main/README.md`
- [TypeScript 指南](./reference/node_mcp_server.md) - TypeScript 模式与示例

**Python：**
- **Python SDK**：用 WebFetch 加载 `https://raw.githubusercontent.com/modelcontextprotocol/python-sdk/main/README.md`
- [Python 指南](./reference/python_mcp_server.md) - Python 模式与示例

#### 1.4 规划实现方案

**理解 API：**
阅读服务的 API 文档，识别关键端点、鉴权要求与数据模型。必要时使用 Web 搜索与 WebFetch。

**工具选择：**
优先保证全面的 API 覆盖。列出要实现的端点，从最常用的操作开始。

---

### 阶段 2：实现

#### 2.1 搭建项目结构

项目初始化请参考对应语言指南：
- [TypeScript 指南](./reference/node_mcp_server.md) - 项目结构、package.json、tsconfig.json
- [Python 指南](./reference/python_mcp_server.md) - 模块组织与依赖

#### 2.2 实现核心基础设施

创建共享工具与基础设施：
- 带鉴权的 API 客户端
- 错误处理辅助函数
- 响应格式化（JSON/Markdown）
- 分页支持

#### 2.3 实现工具

对每个工具：

**输入 Schema：**
- Use Zod (TypeScript) or Pydantic (Python)
- Include constraints and clear descriptions
- Add examples in field descriptions

**输出 Schema：**
- Define `outputSchema` where possible for structured data
- Use `structuredContent` in tool responses (TypeScript SDK feature)
- Helps clients understand and process tool outputs

**工具说明：**
- Concise summary of functionality
- Parameter descriptions
- Return type schema

**实现要点：**
- Async/await for I/O operations
- Proper error handling with actionable messages
- Support pagination where applicable
- Return both text content and structured data when using modern SDKs

**注解（Annotations）：**
- `readOnlyHint`: true/false
- `destructiveHint`: true/false
- `idempotentHint`: true/false
- `openWorldHint`: true/false

---

### 阶段 3：评审与测试

#### 3.1 代码质量

检查要点：
- No duplicated code (DRY principle)
- Consistent error handling
- Full type coverage
- Clear tool descriptions

#### 3.2 构建与测试

**TypeScript:**
- Run `npm run build` to verify compilation
- Test with MCP Inspector: `npx @modelcontextprotocol/inspector`

**Python:**
- Verify syntax: `python -m py_compile your_server.py`
- Test with MCP Inspector

更详细的测试方法与质量清单见对应语言指南。

---

### 阶段 4：创建评测（Evaluations）

实现 MCP 服务器后，创建全面的评测用例来验证其有效性。

**完整评测指南请加载 [Evaluation Guide](./reference/evaluation.md)。**

#### 4.1 理解评测目的

用评测来验证 LLM 是否能有效使用你的 MCP 服务器回答真实且复杂的问题。

#### 4.2 创建 10 个评测问题

按评测指南中的流程创建高质量评测：

1. **Tool Inspection**: List available tools and understand their capabilities
2. **Content Exploration**: Use READ-ONLY operations to explore available data
3. **Question Generation**: Create 10 complex, realistic questions
4. **Answer Verification**: Solve each question yourself to verify answers

#### 4.3 评测要求

确保每个问题满足：
- **Independent**: Not dependent on other questions
- **Read-only**: Only non-destructive operations required
- **Complex**: Requiring multiple tool calls and deep exploration
- **Realistic**: Based on real use cases humans would care about
- **Verifiable**: Single, clear answer that can be verified by string comparison
- **Stable**: Answer won't change over time

#### 4.4 输出格式

创建如下结构的 XML 文件：

```xml
<evaluation>
  <qa_pair>
    <question>Find discussions about AI model launches with animal codenames. One model needed a specific safety designation that uses the format ASL-X. What number X was being determined for the model named after a spotted wild cat?</question>
    <answer>3</answer>
  </qa_pair>
<!-- More qa_pairs... -->
</evaluation>
```

---

# 参考文件

## 文档库

开发过程中按需加载以下资源：

### 核心 MCP 文档（优先加载）
- **MCP 协议**：先看 `https://modelcontextprotocol.io/sitemap.xml`，再获取具体页面（追加 `.md` 后缀）
- [MCP 最佳实践](./reference/mcp_best_practices.md) - 通用 MCP 指南，包括：
  - Server and tool naming conventions
  - Response format guidelines (JSON vs Markdown)
  - Pagination best practices
  - Transport selection (streamable HTTP vs stdio)
  - Security and error handling standards

### SDK 文档（阶段 1/2 加载）
- **Python SDK**: Fetch from `https://raw.githubusercontent.com/modelcontextprotocol/python-sdk/main/README.md`
- **TypeScript SDK**: Fetch from `https://raw.githubusercontent.com/modelcontextprotocol/typescript-sdk/main/README.md`

### 语言实现指南（阶段 2 加载）
- [Python 实现指南](./reference/python_mcp_server.md) - 完整的 Python/FastMCP 指南，包括：
  - Server initialization patterns
  - Pydantic model examples
  - Tool registration with `@mcp.tool`
  - Complete working examples
  - Quality checklist

- [TypeScript 实现指南](./reference/node_mcp_server.md) - 完整的 TypeScript 指南，包括：
  - Project structure
  - Zod schema patterns
  - Tool registration with `server.registerTool`
  - Complete working examples
  - Quality checklist

### 评测指南（阶段 4 加载）
- [Evaluation Guide](./reference/evaluation.md) - 完整的评测创建指南，包括：
  - Question creation guidelines
  - Answer verification strategies
  - XML format specifications
  - Example questions and answers
  - Running an evaluation with the provided scripts
