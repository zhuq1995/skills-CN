# MCP 服务器评测指南

## 概览

本文提供为 MCP 服务器创建全面评测的指导。评测用于检验：LLM 是否能仅使用你提供的 MCP 工具，有效回答现实且复杂的问题。

---

## 快速参考

### 评测要求
- 创建 10 个可读性良好的问题
- 问题必须只需只读、独立、非破坏性操作
- 每个问题需要多次工具调用（可能达几十次）
- 答案必须是单一且可验证的值
- 答案必须稳定（不会随时间变化）

### 输出格式
```xml
<evaluation>
   <qa_pair>
      <question>Your question here</question>
      <answer>Single verifiable answer</answer>
   </qa_pair>
</evaluation>
```

---

## 评测目的

衡量 MCP 服务器质量的标准，不在于工具实现得多完整，而在于这些实现（输入/输出 schema、docstring/描述、功能）是否能让 LLM 在无其他上下文且仅访问 MCP 服务器的情况下，回答现实且困难的问题。

## 评测概览

创建 10 个只需只读、独立、非破坏且幂等操作即可回答的问题。每个问题应当：
- 现实
- 清晰简洁
- 不含歧义
- 复杂，可能需要几十次工具调用或步骤
- 能以单一、可验证的值回答（需预先确定）

## 问题编写指南

### 核心要求

1. **问题必须独立**
   - 不依赖任何其他问题的答案
   - 不假设先前问题存在写操作

2. **问题必须仅需非破坏且幂等的工具使用**
   - 不应要求或指示通过修改状态来获得答案

3. **问题应现实、清晰、简洁且复杂**
   - 必须需要另一个 LLM 通过多次工具调用或步骤才能解答

### 复杂度与深度

4. **问题应要求深入探索**
   - 可设计多跳问题：包含多个子问题与顺序工具调用
   - 每一步应受益于前一步发现的信息

5. **问题可能需要大量分页**
   - 需要翻页获取多页结果
   - 可能需要查询偏旧数据（如 1–2 年前）以找到小众信息
   - 问题应当具有难度

6. **问题应要求深层理解**
   - 而非表层知识
   - 复杂概念可设计为需证据支持的判断题（是/否）
   - 可使用多选题，要求检索不同假设

7. **问题不得通过简单关键词搜索即可解决**
   - 不要包含目标内容的特定关键词
   - 使用同义词、相关概念或释义
   - 需要多次搜索、分析多个相关项、抽取上下文后再推导答案

### 工具能力压测

8. **问题应压测工具返回值**
   - 可能迫使工具返回大型 JSON 或列表，考验 LLM 的处理能力
   - 应要求理解多模态数据：
     - ID 与名称
     - 时间戳与日期时间（年月日、秒）
     - 文件 ID、名称、扩展名与 mimetype
     - URL、GID 等
   - 探测工具返回所有有用数据形式的能力

9. **问题应主要反映真实人类用例**
   - 即人类在 LLM 辅助下关心的信息检索任务

10. **问题可能需要几十次工具调用**
    - 挑战上下文受限的 LLM
    - 鼓励 MCP 工具控制返回信息规模

11. **可包含有歧义的问题**
    - 可能存在歧义或要求在工具选择上做难决策
    - 迫使 LLM 可能犯错或误解
    - 但务必保证仍然存在“单一可验证的答案”

### 稳定性

12. **问题设计需保证答案不变化**
    - 避免依赖“动态现状”的问题
    - 例如不要统计：
      - 帖子的反应数
      - 线程的回复数
      - 频道成员数量

13. **不要让 MCP 服务器限制你创建问题的类型**
    - 创建具有挑战性与复杂度的问题
    - 有些问题可能无法用现有工具解决
    - 问题可要求特定输出格式（如日期时间 vs epoch、JSON vs Markdown）
    - 问题可能需要几十次工具调用

## 答案编写指南

### 验证

1. **答案必须能通过“直接字符串比较”验证**
   - 若答案可有多种表达，需在问题中明确指定输出格式
   - 示例：“使用 YYYY/MM/DD”“回答 True 或 False”“仅回答 A/B/C/D”
   - 答案应为单一可验证值，例如：
     - 用户 ID、用户名、显示名、名、姓
     - 频道 ID、频道名
     - 消息 ID、字符串
     - URL、标题
     - 数值
     - 时间戳、日期时间
     - 布尔值（判断题）
     - 邮箱、手机号
     - 文件 ID、文件名、扩展名
     - 多选题答案
   - 答案不应要求复杂结构或特殊格式
   - 评测将通过“直接字符串比较”验证答案

### 可读性

2. **答案一般应优先人类可读**
   - 如姓名、日期时间、文件名、消息字符串、URL、是/否、True/False、A/B/C/D 等
   - 而非不透明的 ID（尽管 ID 也可接受）
   - 大多数答案应为人类可读

### 稳定性

3. **答案必须稳定/平稳**
   - 关注已结束的内容（已结束对话、已上线项目、已回答问题）
   - 基于“闭合”的概念设计问题，使答案恒定
   - 可指定固定时间窗口以隔离非平稳答案
   - 依赖不太可能变化的上下文
   - 示例：若要找论文名字，描述需足够具体，避免与后续论文混淆

4. **答案必须清晰且不含歧义**
   - 问题设计需保证单一且明确的答案
   - 答案可通过 MCP 工具推导得到

### 多样性

5. **答案须具多样性**
   - 在多模态与多格式下保持“单一可验证值”
   - 用户维度：用户 ID、用户名、显示名、名、姓、邮箱、手机号
   - 频道维度：频道 ID、频道名、频道主题
   - 消息维度：消息 ID、消息字符串、时间戳、年月日

6. **答案不得为复杂结构**
   - 不为值列表
   - 不为复杂对象
   - 不为 ID 或字符串列表
   - 不为自然语言文本
   - 除非能通过直接字符串比较直观验证、且现实中可重复生成
   - 且不太可能出现不同排序或格式导致不一致

## 评测流程

### 步骤 1：文档检查

阅读目标 API 文档以了解：
- 可用端点与功能
- 若存在歧义，从网络获取补充信息
- 尽可能并行进行此步骤
- 确保各子代理仅检查本地文件或网络文档

### 步骤 2：工具检查

列出 MCP 服务器可用工具：
- 直接检查 MCP 服务器
- 理解输入/输出 schema、docstring 与描述
- 此阶段不调用工具

### 步骤 3：形成理解

重复步骤 1 与 2 直到具备充分理解：
- 迭代多次
- 思考要创建何种任务
- 精炼你的理解
- 任意阶段都不应阅读服务器实现代码
- 依据直觉与理解创建合理、现实但非常有挑战的任务

### 步骤 4：只读内容检查

理解 API 与工具后，使用 MCP 工具：
- 仅以只读、非破坏性操作检查内容
- 目标：识别具体内容（用户、频道、消息、项目、任务等）以创建现实问题
- 不调用任何修改状态的工具
- 不阅读服务器实现代码
- 并行进行本步骤，子代理独立探索
- 子代理仅执行只读、非破坏、幂等操作
- 注意：某些工具可能返回大量数据，导致上下文耗尽
- 进行增量、小规模、针对性工具调用
- 所有调用均应使用 `limit` 参数限制结果（<10）
- 使用分页

### 步骤 5：生成任务

内容检查后，创建 10 个可读性良好的问题：
- LLM 应能仅用 MCP 服务器回答
- 遵循上述问题与答案指南

## 输出格式

Each QA pair consists of a question and an answer. The output should be an XML file with this structure:

```xml
<evaluation>
   <qa_pair>
      <question>Find the project created in Q2 2024 with the highest number of completed tasks. What is the project name?</question>
      <answer>Website Redesign</answer>
   </qa_pair>
   <qa_pair>
      <question>Search for issues labeled as "bug" that were closed in March 2024. Which user closed the most issues? Provide their username.</question>
      <answer>sarah_dev</answer>
   </qa_pair>
   <qa_pair>
      <question>Look for pull requests that modified files in the /api directory and were merged between January 1 and January 31, 2024. How many different contributors worked on these PRs?</question>
      <answer>7</answer>
   </qa_pair>
   <qa_pair>
      <question>Find the repository with the most stars that was created before 2023. What is the repository name?</question>
      <answer>data-pipeline</answer>
   </qa_pair>
</evaluation>
```

## 评测示例

### 优秀问题示例

**Example 1: Multi-hop question requiring deep exploration (GitHub MCP)**
```xml
<qa_pair>
   <question>Find the repository that was archived in Q3 2023 and had previously been the most forked project in the organization. What was the primary programming language used in that repository?</question>
   <answer>Python</answer>
</qa_pair>
```

此问题的优点：
- 需要多次搜索找到被归档仓库
- 需识别归档前 fork 数最多的仓库
- 需查看仓库详情以确定语言
- 答案简单且可验证
- 基于历史闭合数据，不会变化

**Example 2: Requires understanding context without keyword matching (Project Management MCP)**
```xml
<qa_pair>
   <question>Locate the initiative focused on improving customer onboarding that was completed in late 2023. The project lead created a retrospective document after completion. What was the lead's role title at that time?</question>
   <answer>Product Manager</answer>
</qa_pair>
```

此问题的优点：
- 未使用具体项目名（“专注改进用户上手的项目”）
- 需在特定时间范围内查找已完成项目
- 需识别项目负责人及其角色
- 需从复盘文档理解上下文
- 答案人类可读且稳定
- 基于已完成工作（不会变化）

**Example 3: Complex aggregation requiring multiple steps (Issue Tracker MCP)**
```xml
<qa_pair>
   <question>Among all bugs reported in January 2024 that were marked as critical priority, which assignee resolved the highest percentage of their assigned bugs within 48 hours? Provide the assignee's username.</question>
   <answer>alex_eng</answer>
</qa_pair>
```

此问题的优点：
- 需按日期、优先级与状态过滤缺陷
- 需按指派人分组并计算解决率
- 需理解时间戳以判定 48 小时窗口
- 测试分页（可能有很多缺陷）
- 答案为单一用户名
- 基于特定时间范围的历史数据

**Example 4: Requires synthesis across multiple data types (CRM MCP)**
```xml
<qa_pair>
   <question>Find the account that upgraded from the Starter to Enterprise plan in Q4 2023 and had the highest annual contract value. What industry does this account operate in?</question>
   <answer>Healthcare</answer>
</qa_pair>
```

此问题的优点：
- 需理解订阅等级变更
- 需在特定时间范围内识别升级事件
- 需比较合同金额
- 需获取账户行业信息
- 答案简单且可验证
- 基于已完成的历史交易

### 不佳问题示例

**Example 1: Answer changes over time**
```xml
<qa_pair>
   <question>How many open issues are currently assigned to the engineering team?</question>
   <answer>47</answer>
</qa_pair>
```

此问题的不足：
- 随着问题创建、关闭或重指派，答案会变化
- 不基于稳定数据
- 依赖动态“现状”

**Example 2: Too easy with keyword search**
```xml
<qa_pair>
   <question>Find the pull request with title "Add authentication feature" and tell me who created it.</question>
   <answer>developer123</answer>
</qa_pair>
```

此问题的不足：
- 用准确标题的关键词搜索即可解决
- 不需要深入探索或理解
- 无需综合或分析

**Example 3: Ambiguous answer format**
```xml
<qa_pair>
   <question>List all the repositories that have Python as their primary language.</question>
   <answer>repo1, repo2, repo3, data-pipeline, ml-tools</answer>
</qa_pair>
```

此问题的不足：
- 答案是列表，返回顺序可能不同
- 难以用直接字符串比较验证
- LLM 可能以不同格式返回（JSON 数组、逗号分隔、换行分隔）
- 更好的问题是要求具体聚合（计数）或极值（最多星标）

## 验证流程

After creating evaluations:

1. **Examine the XML file** to understand the schema
2. **Load each task instruction** and in parallel using the MCP server and tools, identify the correct answer by attempting to solve the task YOURSELF
3. **Flag any operations** that require WRITE or DESTRUCTIVE operations
4. **Accumulate all CORRECT answers** and replace any incorrect answers in the document
5. **Remove any `<qa_pair>`** that require WRITE or DESTRUCTIVE operations

请并行解题以避免上下文耗尽，最后汇总答案并统一修改文件。

## 创建高质量评测的建议

1. **Think Hard and Plan Ahead** before generating tasks
2. **Parallelize Where Opportunity Arises** to speed up the process and manage context
3. **Focus on Realistic Use Cases** that humans would actually want to accomplish
4. **Create Challenging Questions** that test the limits of the MCP server's capabilities
5. **Ensure Stability** by using historical data and closed concepts
6. **Verify Answers** by solving the questions yourself using the MCP server tools
7. **Iterate and Refine** based on what you learn during the process

---

# 运行评测

After creating your evaluation file, you can use the provided evaluation harness to test your MCP server.

## 环境准备

1. **Install Dependencies**

   ```bash
   pip install -r scripts/requirements.txt
   ```

   Or install manually:
   ```bash
   pip install anthropic mcp
   ```

2. **Set API Key**

   ```bash
   export ANTHROPIC_API_KEY=your_api_key_here
   ```

## 评测文件格式

Evaluation files use XML format with `<qa_pair>` elements:

```xml
<evaluation>
   <qa_pair>
      <question>Find the project created in Q2 2024 with the highest number of completed tasks. What is the project name?</question>
      <answer>Website Redesign</answer>
   </qa_pair>
   <qa_pair>
      <question>Search for issues labeled as "bug" that were closed in March 2024. Which user closed the most issues? Provide their username.</question>
      <answer>sarah_dev</answer>
   </qa_pair>
</evaluation>
```

## 运行方式

The evaluation script (`scripts/evaluation.py`) supports three transport types:

**重要：**
- **stdio 传输**：评测脚本会自动启动并管理 MCP 服务器进程，请勿手动运行服务器。
- **sse/http 传输**：需先单独启动 MCP 服务器，再运行评测脚本，脚本会连接到指定 URL 的已运行服务器。

### 1. 本地 STDIO 服务器

For locally-run MCP servers (script launches the server automatically):

```bash
python scripts/evaluation.py \
  -t stdio \
  -c python \
  -a my_mcp_server.py \
  evaluation.xml
```

With environment variables:
```bash
python scripts/evaluation.py \
  -t stdio \
  -c python \
  -a my_mcp_server.py \
  -e API_KEY=abc123 \
  -e DEBUG=true \
  evaluation.xml
```

### 2. Server-Sent Events（SSE）

For SSE-based MCP servers (you must start the server first):

```bash
python scripts/evaluation.py \
  -t sse \
  -u https://example.com/mcp \
  -H "Authorization: Bearer token123" \
  -H "X-Custom-Header: value" \
  evaluation.xml
```

### 3. HTTP（Streamable HTTP）

For HTTP-based MCP servers (you must start the server first):

```bash
python scripts/evaluation.py \
  -t http \
  -u https://example.com/mcp \
  -H "Authorization: Bearer token123" \
  evaluation.xml
```

## 命令行选项

```
usage: evaluation.py [-h] [-t {stdio,sse,http}] [-m MODEL] [-c COMMAND]
                     [-a ARGS [ARGS ...]] [-e ENV [ENV ...]] [-u URL]
                     [-H HEADERS [HEADERS ...]] [-o OUTPUT]
                     eval_file

positional arguments:
  eval_file             Path to evaluation XML file

optional arguments:
  -h, --help            Show help message
  -t, --transport       Transport type: stdio, sse, or http (default: stdio)
  -m, --model           Claude model to use (default: claude-3-7-sonnet-20250219)
  -o, --output          Output file for report (default: print to stdout)

stdio options:
  -c, --command         Command to run MCP server (e.g., python, node)
  -a, --args            Arguments for the command (e.g., server.py)
  -e, --env             Environment variables in KEY=VALUE format

sse/http options:
  -u, --url             MCP server URL
  -H, --header          HTTP headers in 'Key: Value' format
```

## 输出报告

The evaluation script generates a detailed report including:

- **Summary Statistics**:
  - Accuracy (correct/total)
  - Average task duration
  - Average tool calls per task
  - Total tool calls

- **Per-Task Results**:
  - Prompt and expected response
  - Actual response from the agent
  - Whether the answer was correct (✅/❌)
  - Duration and tool call details
  - Agent's summary of its approach
  - Agent's feedback on the tools

### 保存报告到文件

```bash
python scripts/evaluation.py \
  -t stdio \
  -c python \
  -a my_server.py \
  -o evaluation_report.md \
  evaluation.xml
```

## 完整示例工作流

Here's a complete example of creating and running an evaluation:

1. **Create your evaluation file** (`my_evaluation.xml`):

```xml
<evaluation>
   <qa_pair>
      <question>Find the user who created the most issues in January 2024. What is their username?</question>
      <answer>alice_developer</answer>
   </qa_pair>
   <qa_pair>
      <question>Among all pull requests merged in Q1 2024, which repository had the highest number? Provide the repository name.</question>
      <answer>backend-api</answer>
   </qa_pair>
   <qa_pair>
      <question>Find the project that was completed in December 2023 and had the longest duration from start to finish. How many days did it take?</question>
      <answer>127</answer>
   </qa_pair>
</evaluation>
```

2. **Install dependencies**:

```bash
pip install -r scripts/requirements.txt
export ANTHROPIC_API_KEY=your_api_key
```

3. **Run evaluation**:

```bash
python scripts/evaluation.py \
  -t stdio \
  -c python \
  -a github_mcp_server.py \
  -e GITHUB_TOKEN=ghp_xxx \
  -o github_eval_report.md \
  my_evaluation.xml
```

4. **Review the report** in `github_eval_report.md` to:
   - See which questions passed/failed
   - Read the agent's feedback on your tools
   - Identify areas for improvement
   - Iterate on your MCP server design

## 故障排查

### 连接错误

If you get connection errors:
- **STDIO**: Verify the command and arguments are correct
- **SSE/HTTP**: Check the URL is accessible and headers are correct
- Ensure any required API keys are set in environment variables or headers

### 准确率低

If many evaluations fail:
- Review the agent's feedback for each task
- Check if tool descriptions are clear and comprehensive
- Verify input parameters are well-documented
- Consider whether tools return too much or too little data
- Ensure error messages are actionable

### 超时问题

If tasks are timing out:
- Use a more capable model (e.g., `claude-3-7-sonnet-20250219`)
- Check if tools are returning too much data
- Verify pagination is working correctly
- Consider simplifying complex questions
