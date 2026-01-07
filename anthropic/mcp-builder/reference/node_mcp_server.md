# Node/TypeScript MCP 服务器实现指南

## 概览

本文档提供使用 MCP TypeScript SDK 实现 MCP 服务器的最佳实践与示例，涵盖项目结构、服务器初始化、工具注册模式、Zod 输入校验、错误处理，以及完整可运行示例。

---

## 快速参考

### 关键导入
```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StreamableHTTPServerTransport } from "@modelcontextprotocol/sdk/server/streamableHttp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import express from "express";
import { z } from "zod";
```

### 服务器初始化
```typescript
const server = new McpServer({
  name: "service-mcp-server",
  version: "1.0.0"
});
```

### 工具注册模式
```typescript
server.registerTool(
  "tool_name",
  {
    title: "工具显示名称",
    description: "工具用途说明",
    inputSchema: { param: z.string() },
    outputSchema: { result: z.string() }
  },
  async ({ param }) => {
    const output = { result: `Processed: ${param}` };
    return {
      content: [{ type: "text", text: JSON.stringify(output) }],
      structuredContent: output // 结构化数据的现代返回模式
    };
  }
);
```

---

## MCP TypeScript SDK

官方 MCP TypeScript SDK 提供：
- `McpServer`：服务器初始化
- `registerTool`：注册工具
- Zod：运行时输入校验集成
- 类型安全的工具处理器实现

**重要——仅使用现代 API：**
- 应使用：`server.registerTool()`、`server.registerResource()`、`server.registerPrompt()`
- 不应使用：已弃用的旧 API，如 `server.tool()`、`server.setRequestHandler(ListToolsRequestSchema, ...)` 或手动注册处理器
- `register*` 方法具备更好类型安全与自动 schema 处理，是推荐方式

完整细节请参阅 MCP SDK 文档。

## 服务器命名约定

Node/TypeScript MCP 服务器需遵循：
- 格式：`{service}-mcp-server`（全小写、用连字符）
- 示例：`github-mcp-server`、`jira-mcp-server`、`stripe-mcp-server`

命名要求：
- 通用（不绑定具体功能）
- 能描述集成的服务/API
- 便于从任务描述中推断
- 不含版本号或日期

## 项目结构

建议采用如下结构：

```
{service}-mcp-server/
├── package.json
├── tsconfig.json
├── README.md
├── src/
│   ├── index.ts          # 入口：McpServer 初始化
│   ├── types.ts          # 类型定义与接口
│   ├── tools/            # 工具实现（按域拆分文件）
│   ├── services/         # API 客户端与共享工具
│   ├── schemas/          # Zod 校验 schema
│   └── constants.ts      # 共享常量（API_URL、CHARACTER_LIMIT 等）
└── dist/                 # 构建产物（入口 dist/index.js）
```

## 工具实现

### 命名

工具名使用 snake_case（如 `search_users`、`create_project`、`get_channel_info`），强调动作导向与清晰度。

**避免冲突**：加入服务前缀避免不同服务器重名：
- 用 `slack_send_message` 而不是 `send_message`
- 用 `github_create_issue` 而不是 `create_issue`
- 用 `asana_list_tasks` 而不是 `list_tasks`

### 结构

通过 `registerTool` 注册工具，要求：
- 使用 Zod schema 做运行时输入校验与类型安全
- 必须显式提供 `description`（不会自动从 JSDoc 提取）
- 明确提供 `title`、`description`、`inputSchema`、`annotations`
- `inputSchema` 必须是 Zod 对象（不是 JSON schema）
- 所有参数与返回值显式标注类型

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { z } from "zod";

const server = new McpServer({
  name: "example-mcp",
  version: "1.0.0"
});

// Zod 输入校验
const UserSearchInputSchema = z.object({
  query: z.string()
    .min(2, "Query must be at least 2 characters")
    .max(200, "Query must not exceed 200 characters")
    .describe("Search string to match against names/emails"),
  limit: z.number()
    .int()
    .min(1)
    .max(100)
    .default(20)
    .describe("Maximum results to return"),
  offset: z.number()
    .int()
    .min(0)
    .default(0)
    .describe("Number of results to skip for pagination"),
  response_format: z.nativeEnum(ResponseFormat)
    .default(ResponseFormat.MARKDOWN)
    .describe("Output format: 'markdown' for human-readable or 'json' for machine-readable")
}).strict();

// 类型定义
type UserSearchInput = z.infer<typeof UserSearchInputSchema>;

server.registerTool(
  "example_search_users",
  {
    title: "搜索示例用户",
    description: `按姓名、邮箱或团队搜索 Example 系统中的用户。

此工具支持对平台内所有用户档案进行搜索，支持部分匹配与多种过滤方式。不创建或修改用户，仅搜索现有用户。

参数：
  - query (string)：匹配姓名/邮箱的搜索字符串
  - limit (number)：返回最大结果数，范围 1–100（默认 20）
  - offset (number)：分页偏移量（默认 0）
  - response_format ('markdown' | 'json')：输出格式（默认 'markdown'）

返回（JSON 格式时的结构化数据）：
  {
    "total": number,           // 总匹配数
    "count": number,           // 本次返回数量
    "offset": number,          // 当前分页偏移
    "users": [
      {
        "id": string,          // 用户 ID，如 "U123456789"
        "name": string,        // 全名，如 "John Doe"
        "email": string,       // 邮箱
        "team": string,        // 团队名（可选）
        "active": boolean      // 是否激活
      }
    ],
    "has_more": boolean,       // 是否还有更多结果
    "next_offset": number      // 下一页偏移量（当 has_more 为 true）
  }

示例：
  - 使用场景：“找所有市场团队成员” -> params: query="team:marketing"
  - 使用场景：“搜索 John 的账号” -> params: query="john"
  - 不适用场景：需要创建用户（请改用 example_create_user）

错误处理：
  - 若请求过多（429）返回 "Error: Rate limit exceeded"
  - 若搜索为空，返回 "No users found matching '<query>'"`,
    inputSchema: UserSearchInputSchema,
    annotations: {
      readOnlyHint: true,
      destructiveHint: false,
      idempotentHint: true,
      openWorldHint: true
    }
  },
  async (params: UserSearchInput) => {
    try {
      // Zod 已处理输入校验
      // 使用校验后的参数进行 API 请求
      const data = await makeApiRequest<any>(
        "users/search",
        "GET",
        undefined,
        {
          q: params.query,
          limit: params.limit,
          offset: params.offset
        }
      );

      const users = data.users || [];
      const total = data.total || 0;

      if (!users.length) {
        return {
          content: [{
            type: "text",
            text: `No users found matching '${params.query}'`
          }]
        };
      }

      // 结构化输出
      const output = {
        total,
        count: users.length,
        offset: params.offset,
        users: users.map((user: any) => ({
          id: user.id,
          name: user.name,
          email: user.email,
          ...(user.team ? { team: user.team } : {}),
          active: user.active ?? true
        })),
        has_more: total > params.offset + users.length,
        ...(total > params.offset + users.length ? {
          next_offset: params.offset + users.length
        } : {})
      };

      // 根据请求格式生成文本内容
      let textContent: string;
      if (params.response_format === ResponseFormat.MARKDOWN) {
        const lines = [`# User Search Results: '${params.query}'`, "",
          `Found ${total} users (showing ${users.length})`, ""];
        for (const user of users) {
          lines.push(`## ${user.name} (${user.id})`);
          lines.push(`- **Email**: ${user.email}`);
          if (user.team) lines.push(`- **Team**: ${user.team}`);
          lines.push("");
        }
        textContent = lines.join("\n");
      } else {
        textContent = JSON.stringify(output, null, 2);
      }

      return {
        content: [{ type: "text", text: textContent }],
        structuredContent: output // 结构化数据返回
      };
    } catch (error) {
      return {
        content: [{
          type: "text",
          text: handleApiError(error)
        }]
      };
    }
  }
);
```

## Zod 输入校验

Zod 提供运行时类型校验：

```typescript
import { z } from "zod";

// 带校验的基础 schema
const CreateUserSchema = z.object({
  name: z.string()
    .min(1, "Name is required")
    .max(100, "Name must not exceed 100 characters"),
  email: z.string()
    .email("Invalid email format"),
  age: z.number()
    .int("Age must be a whole number")
    .min(0, "Age cannot be negative")
    .max(150, "Age cannot be greater than 150")
}).strict();  // 用 .strict() 禁止额外字段

// 枚举
enum ResponseFormat {
  MARKDOWN = "markdown",
  JSON = "json"
}

const SearchSchema = z.object({
  response_format: z.nativeEnum(ResponseFormat)
    .default(ResponseFormat.MARKDOWN)
    .describe("Output format")
});

// 带默认值的可选字段
const PaginationSchema = z.object({
  limit: z.number()
    .int()
    .min(1)
    .max(100)
    .default(20)
    .describe("Maximum results to return"),
  offset: z.number()
    .int()
    .min(0)
    .default(0)
    .describe("Number of results to skip")
});
```

## 响应格式选项

支持多种输出格式以提升灵活性：

```typescript
enum ResponseFormat {
  MARKDOWN = "markdown",
  JSON = "json"
}

const inputSchema = z.object({
  query: z.string(),
  response_format: z.nativeEnum(ResponseFormat)
    .default(ResponseFormat.MARKDOWN)
    .describe("Output format: 'markdown' for human-readable or 'json' for machine-readable")
});
```

**Markdown 格式：**
- 使用标题、列表与格式增强清晰度
- 时间戳转换为人类可读格式
- 展示名后加括号显示 ID
- 省略冗长元数据
- 逻辑分组相关信息

**JSON 格式：**
- 返回完整结构化数据，便于程序处理
- 包含所有可用字段与元数据
- 使用一致的字段名与类型

## 分页实现

用于列资源的工具：

```typescript
const ListSchema = z.object({
  limit: z.number().int().min(1).max(100).default(20),
  offset: z.number().int().min(0).default(0)
});

async function listItems(params: z.infer<typeof ListSchema>) {
  const data = await apiRequest(params.limit, params.offset);

  const response = {
    total: data.total,
    count: data.items.length,
    offset: params.offset,
    items: data.items,
    has_more: data.total > params.offset + data.items.length,
    next_offset: data.total > params.offset + data.items.length
      ? params.offset + data.items.length
      : undefined
  };

  return JSON.stringify(response, null, 2);
}
```

## 字符限制与截断

增加 `CHARACTER_LIMIT` 常量以防响应过大：

```typescript
// constants.ts 顶层
export const CHARACTER_LIMIT = 25000;  // 最大响应字符数

async function searchTool(params: SearchInput) {
  let result = generateResponse(data);

  // 超限则截断
  if (result.length > CHARACTER_LIMIT) {
    const truncatedData = data.slice(0, Math.max(1, data.length / 2));
    response.data = truncatedData;
    response.truncated = true;
    response.truncation_message =
      `Response truncated from ${data.length} to ${truncatedData.length} items. ` +
      `Use 'offset' parameter or add filters to see more results.`;
    result = JSON.stringify(response, null, 2);
  }

  return result;
}
```

## 错误处理

提供清晰、可执行的错误信息：

```typescript
import axios, { AxiosError } from "axios";

function handleApiError(error: unknown): string {
  if (error instanceof AxiosError) {
    if (error.response) {
      switch (error.response.status) {
        case 404:
          return "Error: Resource not found. Please check the ID is correct.";
        case 403:
          return "Error: Permission denied. You don't have access to this resource.";
        case 429:
          return "Error: Rate limit exceeded. Please wait before making more requests.";
        default:
          return `Error: API request failed with status ${error.response.status}`;
      }
    } else if (error.code === "ECONNABORTED") {
      return "Error: Request timed out. Please try again.";
    }
  }
  return `Error: Unexpected error occurred: ${error instanceof Error ? error.message : String(error)}`;
}
```

## 共享工具

将通用功能抽取为可复用函数：

```typescript
// 共享 API 请求函数
async function makeApiRequest<T>(
  endpoint: string,
  method: "GET" | "POST" | "PUT" | "DELETE" = "GET",
  data?: any,
  params?: any
): Promise<T> {
  try {
    const response = await axios({
      method,
      url: `${API_BASE_URL}/${endpoint}`,
      data,
      params,
      timeout: 30000,
      headers: {
        "Content-Type": "application/json",
        "Accept": "application/json"
      }
    });
    return response.data;
  } catch (error) {
    throw error;
  }
}
```

## Async/Await 最佳实践

网络与 I/O 操作务必使用 async/await：

```typescript
// 好：异步网络请求
async function fetchData(resourceId: string): Promise<ResourceData> {
  const response = await axios.get(`${API_URL}/resource/${resourceId}`);
  return response.data;
}

// 差：链式 Promise
function fetchData(resourceId: string): Promise<ResourceData> {
  return axios.get(`${API_URL}/resource/${resourceId}`)
    .then(response => response.data);  // 可读性与维护性较差
}
```

## TypeScript 最佳实践

1. 启用严格模式（tsconfig.json）
2. 定义清晰的接口
3. 避免 `any`，使用确切类型或 `unknown`
4. 使用 Zod 做运行时校验
5. 编写类型守卫做复杂类型检查
6. 全面错误处理，正确鉴别错误类型
7. 空值安全：使用可选链（`?.`）与空值合并（`??`）

```typescript
// 好：Zod + 接口保证类型安全
interface UserResponse {
  id: string;
  name: string;
  email: string;
  team?: string;
  active: boolean;
}

const UserSchema = z.object({
  id: z.string(),
  name: z.string(),
  email: z.string().email(),
  team: z.string().optional(),
  active: z.boolean()
});

type User = z.infer<typeof UserSchema>;

async function getUser(id: string): Promise<User> {
  const data = await apiCall(`/users/${id}`);
  return UserSchema.parse(data);  // 运行时校验
}

// 差：使用 any
async function getUser(id: string): Promise<any> {
  return await apiCall(`/users/${id}`);  // 无类型安全
}
```

## 包配置

### package.json

```json
{
  "name": "{service}-mcp-server",
  "version": "1.0.0",
  "description": "MCP server for {Service} API integration",
  "type": "module",
  "main": "dist/index.js",
  "scripts": {
    "start": "node dist/index.js",
    "dev": "tsx watch src/index.ts",
    "build": "tsc",
    "clean": "rm -rf dist"
  },
  "engines": {
    "node": ">=18"
  },
  "dependencies": {
    "@modelcontextprotocol/sdk": "^1.6.1",
    "axios": "^1.7.9",
    "zod": "^3.23.8"
  },
  "devDependencies": {
    "@types/node": "^22.10.0",
    "tsx": "^4.19.2",
    "typescript": "^5.7.2"
  }
}
```

### tsconfig.json

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "Node16",
    "moduleResolution": "Node16",
    "lib": ["ES2022"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "allowSyntheticDefaultImports": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

## 完整示例

```typescript
#!/usr/bin/env node
/**
 * MCP Server for Example Service.
 *
 * 提供操作 Example API 的工具，包括用户搜索、项目管理与数据导出等。
 */

import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";
import axios, { AxiosError } from "axios";

// 常量
const API_BASE_URL = "https://api.example.com/v1";
const CHARACTER_LIMIT = 25000;

// 枚举
enum ResponseFormat {
  MARKDOWN = "markdown",
  JSON = "json"
}

// Zod schemas
const UserSearchInputSchema = z.object({
  query: z.string()
    .min(2, "Query must be at least 2 characters")
    .max(200, "Query must not exceed 200 characters")
    .describe("Search string to match against names/emails"),
  limit: z.number()
    .int()
    .min(1)
    .max(100)
    .default(20)
    .describe("Maximum results to return"),
  offset: z.number()
    .int()
    .min(0)
    .default(0)
    .describe("Number of results to skip for pagination"),
  response_format: z.nativeEnum(ResponseFormat)
    .default(ResponseFormat.MARKDOWN)
    .describe("Output format: 'markdown' for human-readable or 'json' for machine-readable")
}).strict();

type UserSearchInput = z.infer<typeof UserSearchInputSchema>;

// 共享工具函数
async function makeApiRequest<T>(
  endpoint: string,
  method: "GET" | "POST" | "PUT" | "DELETE" = "GET",
  data?: any,
  params?: any
): Promise<T> {
  try {
    const response = await axios({
      method,
      url: `${API_BASE_URL}/${endpoint}`,
      data,
      params,
      timeout: 30000,
      headers: {
        "Content-Type": "application/json",
        "Accept": "application/json"
      }
    });
    return response.data;
  } catch (error) {
    throw error;
  }
}

function handleApiError(error: unknown): string {
  if (error instanceof AxiosError) {
    if (error.response) {
      switch (error.response.status) {
        case 404:
          return "Error: Resource not found. Please check the ID is correct.";
        case 403:
          return "Error: Permission denied. You don't have access to this resource.";
        case 429:
          return "Error: Rate limit exceeded. Please wait before making more requests.";
        default:
          return `Error: API request failed with status ${error.response.status}`;
      }
    } else if (error.code === "ECONNABORTED") {
      return "Error: Request timed out. Please try again.";
    }
  }
  return `Error: Unexpected error occurred: ${error instanceof Error ? error.message : String(error)}`;
}

// 创建 MCP 服务器实例
const server = new McpServer({
  name: "example-mcp",
  version: "1.0.0"
});

// 注册工具
server.registerTool(
  "example_search_users",
  {
    title: "搜索示例用户",
    description: `[见上方完整描述]`,
    inputSchema: UserSearchInputSchema,
    annotations: {
      readOnlyHint: true,
      destructiveHint: false,
      idempotentHint: true,
      openWorldHint: true
    }
  },
  async (params: UserSearchInput) => {
    // 按上方示例实现
  }
);

// 主函数
// 本地（stdio）
async function runStdio() {
  if (!process.env.EXAMPLE_API_KEY) {
    console.error("ERROR: EXAMPLE_API_KEY environment variable is required");
    process.exit(1);
  }

  const transport = new StdioServerTransport();
  await server.connect(transport);
  console.error("MCP server running via stdio");
}

// 远程（streamable HTTP）
async function runHTTP() {
  if (!process.env.EXAMPLE_API_KEY) {
    console.error("ERROR: EXAMPLE_API_KEY environment variable is required");
    process.exit(1);
  }

  const app = express();
  app.use(express.json());

  app.post('/mcp', async (req, res) => {
    const transport = new StreamableHTTPServerTransport({
      sessionIdGenerator: undefined,
      enableJsonResponse: true
    });
    res.on('close', () => transport.close());
    await server.connect(transport);
    await transport.handleRequest(req, res, req.body);
  });

  const port = parseInt(process.env.PORT || '3000');
  app.listen(port, () => {
    console.error(`MCP server running on http://localhost:${port}/mcp`);
  });
}

// 按环境选择传输
const transport = process.env.TRANSPORT || 'stdio';
if (transport === 'http') {
  runHTTP().catch(error => {
    console.error("Server error:", error);
    process.exit(1);
  });
} else {
  runStdio().catch(error => {
    console.error("Server error:", error);
    process.exit(1);
  });
}
```

---

## 高级 MCP 特性

### 资源注册

以 URI 为基础暴露数据以提升访问效率：

```typescript
import { ResourceTemplate } from "@modelcontextprotocol/sdk/types.js";

// 使用 URI 模板注册资源
server.registerResource(
  {
    uri: "file://documents/{name}",
    name: "Document Resource",
    description: "按名称访问文档",
    mimeType: "text/plain"
  },
  async (uri: string) => {
    // 从 URI 中解析参数
    const match = uri.match(/^file:\/\/documents\/(.+)$/);
    if (!match) {
      throw new Error("Invalid URI format");
    }

    const documentName = match[1];
    const content = await loadDocument(documentName);

    return {
      contents: [{
        uri,
        mimeType: "text/plain",
        text: content
      }]
    };
  }
);

// 动态列出可用资源
server.registerResourceList(async () => {
  const documents = await getAvailableDocuments();
  return {
    resources: documents.map(doc => ({
      uri: `file://documents/${doc.name}`,
      name: doc.name,
      mimeType: "text/plain",
      description: doc.description
    }))
  };
});
```

**何时用资源 vs 工具：**
- 资源：用于基于 URI 的简单数据访问
- 工具：用于需要校验与业务逻辑的复杂操作
- 资源：数据相对静态或模板化
- 工具：操作有副作用或复杂工作流

### 传输选项

TypeScript SDK 支持两类主要传输：

#### Streamable HTTP（远程服务器推荐）

```typescript
import { StreamableHTTPServerTransport } from "@modelcontextprotocol/sdk/server/streamableHttp.js";
import express from "express";

const app = express();
app.use(express.json());

app.post('/mcp', async (req, res) => {
  // 每次请求创建新传输（无状态，避免请求 ID 冲突）
  const transport = new StreamableHTTPServerTransport({
    sessionIdGenerator: undefined,
    enableJsonResponse: true
  });

  res.on('close', () => transport.close());

  await server.connect(transport);
  await transport.handleRequest(req, res, req.body);
});

app.listen(3000);
```

#### stdio（用于本地集成）

```typescript
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

const transport = new StdioServerTransport();
await server.connect(transport);
```

**选择传输：**
- Streamable HTTP：Web 服务、远程访问、多客户端
- stdio：命令行工具、本地开发、子进程集成

### 通知支持

当服务器能力变化时通知客户端：

```typescript
// 工具列表变更通知
server.notification({
  method: "notifications/tools/list_changed"
});

// 资源变更通知
server.notification({
  method: "notifications/resources/list_changed"
});
```

谨慎使用通知——仅在服务器能力确实发生变化时。

---

## 代码最佳实践

### 可组合性与复用

务必优先可组合性与代码复用：

1. 抽取通用功能：
   - 为跨工具使用的操作创建可复用助手函数
   - 构建共享 API 客户端替代复制粘贴
   - 将错误处理逻辑集中在工具函数中
   - 将业务逻辑抽取为可组合函数
   - 抽取共享 Markdown/JSON 字段选择与格式化逻辑

2. 避免重复：
   - 不要在不同工具间复制相似代码
   - 若逻辑出现两次，请抽取为函数
   - 分页、过滤、字段选择、格式化等通用操作应共享
   - 认证/鉴权逻辑集中管理

## 构建与运行

运行前请先构建 TypeScript 代码：

```bash
# 构建
npm run build

# 运行
npm start

# 开发（自动重载）
npm run dev
```

务必确保 `npm run build` 成功完成后再视为实现完成。

## 质量检查清单

在完成 Node/TypeScript MCP 服务器实现前，请确认：

### 策略设计
- [ ] 工具支持完整工作流，而不仅是 API 包装
- [ ] 工具命名符合自然任务划分
- [ ] 响应格式优化上下文占用
- [ ] 适当位置使用人类可读标识
- [ ] 错误信息能有效引导使用

### 实现质量
- [ ] 聚焦实现：最重要且高价值的工具已实现
- [ ] 全部工具使用 `registerTool` 完整注册
- [ ] 每个工具包含 `title`、`description`、`inputSchema`、`annotations`
- [ ] 注解设置正确（readOnlyHint、destructiveHint、idempotentHint、openWorldHint）
- [ ] 全部工具使用 Zod 并 `.strict()` 强约束
- [ ] Zod schema 具备合适的约束与报错信息
- [ ] 工具说明包含显式输入/输出类型
- [ ] 说明包含返回值示例与完整 schema 文档
- [ ] 错误消息清晰、可执行且具教育性

### TypeScript 质量
- [ ] 所有数据结构均有接口定义
- [ ] tsconfig.json 启用严格模式
- [ ] 不使用 `any`，使用确切类型或 `unknown`
- [ ] 所有 async 函数具显式 `Promise<T>` 返回类型
- [ ] 错误处理具类型守卫（如 `axios.isAxiosError`、`z.ZodError`）

### 高级特性（如适用）
- [ ] 资源注册到适当数据端点
- [ ] 配置合适传输（stdio 或 streamable HTTP）
- [ ] 实现动态能力通知
- [ ] SDK 接口保持类型安全

### 项目配置
- [ ] package.json 依赖完整
- [ ] 构建脚本在 dist/ 生成可运行 JS
- [ ] 主入口配置为 dist/index.js
- [ ] 名称遵循 `{service}-mcp-server`
- [ ] tsconfig.json 严格模式

### 代码质量
- [ ] 分页正确实现（如适用）
- [ ] 大响应检查 CHARACTER_LIMIT 并友好截断
- [ ] 为可能很大的结果集提供过滤选项
- [ ] 网络操作妥善处理超时与连接错误
- [ ] 抽取通用功能为可复用函数
- [ ] 相似操作的返回类型保持一致

### 测试与构建
- [ ] `npm run build` 无错误完成
- [ ] 生成 dist/index.js 且可执行
- [ ] 服务器可运行：`node dist/index.js --help`
- [ ] 所有导入解析正确
- [ ] 示例工具调用按预期工作
