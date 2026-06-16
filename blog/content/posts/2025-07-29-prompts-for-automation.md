---
title: "MCP Prompts：构建工作流自动化"
date: "2025-08-04T18:00:00+01:00"
publishDate: "2025-08-04T18:00:00+01:00"
draft: false
description: 通过膳食计划示例，展示如何利用 MCP prompts 和 resource templates 构建工作流自动化的实用指南。
author:
  - Inna Harper（核心维护者）
tags:
  - 自动化
  - mcp
  - prompts
  - 教程
---

[MCP (Model Context Protocol)](https://modelcontextprotocol.io/specification/2025-06-18) prompts 通过结合 AI 能力与结构化数据访问来实现工作流自动化。本文通过一个实际示例，演示如何使用 MCP 的 [prompts](https://modelcontextprotocol.io/specification/2025-06-18/server/prompts) 和 [resource templates](https://modelcontextprotocol.io/specification/2025-06-18/server/resources#resource-templates) 构建自动化。

本指南展示了 MCP prompts 如何自动化重复性工作流。无论您是对 MCP 生态系统感兴趣，还是只想利用 AI 进行工作流自动化，您都将通过一个具体的膳食计划示例学习如何构建实用的自动化。无需 MCP 经验——我们会在深入实现之前介绍基础知识。

## 问题：耗时的重复性任务

每个人都有一些消耗生产时间的重复性任务。常见的例子包括应用代码审查反馈、生成周报、更新文档或创建样板代码。这些任务并不复杂——它们遵循可预测的模式——但繁琐且耗时。[MCP prompts](https://modelcontextprotocol.io/specification/2025-06-18/server/prompts) 旨在帮助自动化这类工作。

MCP prompts 提供的不仅仅是命令快捷方式。它们是构建工作流自动化的原语，结合了脚本的灵活性和现代 AI 系统的智能。本文探讨如何使用 MCP 的 prompt 系统、resource templates 和模块化 servers 来构建自动化。我将通过我构建的膳食计划自动化来演示这些概念，但这些模式广泛适用于任何结构化、重复性的工作流。

## 示例：自动化每周膳食计划

我需要解决一个反复出现的问题：按菜系规划每周膳食以高效管理食材。手动过程包括选择菜系、选择菜品、列出食材、购物和组织食谱——这些重复步骤每周都要花费大量时间。

所以我决定使用 MCP！通过自动化这些步骤，我能够将整个工作流程简化为选择菜系并接收完整的膳食计划和购物清单。（任何支持 MCP prompts 的 client 应该都能工作！）

1. **选择 prompt**

   <img
   src="/posts/images/prompts-list.png"
   alt="MCP prompts 列表，显示可用的自动化命令"
   />

2. **从下拉菜单中选择菜系**
   <img
     src="/posts/images/prompts-suggestions.png"
     alt="用户输入时显示菜系建议的下拉菜单"
   />
3. **完成！**
   系统会生成膳食计划、购物清单，甚至打印购物清单和食谱。

<img
    src="/posts/images/prompts-final-result.png"
    alt="最终生成的膳食计划和购物清单输出"
  />

本文主要关注 Recipe Server 及其 prompts 和 resources。您可以[在此处找到打印 server 的示例](https://github.com/ihrpr/mcp-server-tiny-print)（它与特定的热敏打印机型号配合使用，但您可以轻松地将其替换为 email、Notion 或任何其他输出方式）。独立 server 的美妙之处在于您可以混合搭配不同的能力。

## 核心组件

让我们深入了解使此自动化成为可能的三个组件：[prompts](https://modelcontextprotocol.io/specification/2025-06-18/server/prompts)、[resources](https://modelcontextprotocol.io/specification/2025-06-18/server/resources) 和 [completions](https://modelcontextprotocol.io/specification/2025-06-18/server/utilities/completion)。我将展示每个组件的概念原理，然后我们一起实现它们。

### 1. Resource Templates

在 MCP 中，[静态 resources](https://modelcontextprotocol.io/specification/2025-06-18/server/resources#resource-types) 使用唯一 URI 表示特定内容——例如 `file://recipes/italian.md` 或 `file://recipes/mexican.md`。这种方法虽然直接，但扩展性不佳。如果您有 20 种菜系的食谱，就需要定义 20 个独立的 resources，每个都有自己的 URI 和元数据。

[Resource templates](https://modelcontextprotocol.io/specification/2025-06-18/server/resources#resource-templates) 通过带参数的 URI 模式解决了这个问题，将静态 resource 定义转换为动态内容提供者。

例如，像 `file://recipes/{cuisine}.md` 这样的 template 可能代表以下 resources 集合：

- `file://recipes/italian.md` 返回意大利菜谱
- `file://recipes/mexican.md` 返回墨西哥菜谱

这种模式超越了简单的过滤。您可以为以下场景创建 templates：

- 分层数据：`file://docs/{category}/{topic}`
- Git 仓库内容：`git://repo/{branch}/path/{file}`
- Web 资源：`https://api.example.com/users/{userId}/data`
- 查询参数：`https://example.com/{collection}?type={filter}`

有关 URI 方案和 resource templates 的更多详细信息，请参阅 [MCP Resource 规范](https://modelcontextprotocol.io/specification/2025-06-18/server/resources#resource-templates)。

### 2. Completions

没有人能记住精确的参数值。是 "italian" 还是 "Italian" 还是 "it"？[Completions](https://modelcontextprotocol.io/specification/2025-06-18/server/utilities/completion) 通过在用户输入时提供建议来弥补这一差距，创建了一个直观而非限制性的界面。

不同的 MCP clients 以不同的方式呈现 completions：

- VS Code 显示可筛选的下拉菜单
- 命令行工具可能使用模糊匹配
- Web 界面可能提供丰富的预览

但底层数据来自您的 server，在所有 clients 中保持一致性。

### 3. Prompts：随上下文演变的命令

[Prompts](https://modelcontextprotocol.io/specification/2025-06-18/server/prompts) 是自动化的入口点。它们定义了哪些命令可用，可以从简单的文本指令到丰富的上下文感知操作。

让我们看看 prompts 如何演变以处理日益复杂的使用场景：

**基本 prompt：静态指令**

```
"Create a meal plan for a week"
```

这有效，但过于通用。AI 将基于一般知识创建膳食计划。

**添加参数：动态定制**

```
"Create a meal plan for a week using {cuisine} cuisine"
```

现在用户可以指定意大利菜、墨西哥菜或任何其他菜系。Prompt 会根据用户输入进行调整，但仍然依赖于 AI 关于这些菜系的一般知识。

**包含 resources：您的数据**

Prompts 可以包含 resources 来添加超出简单文本指令的上下文数据。当您需要 AI 使用您的特定上下文而不是一般知识时，这至关重要。

在我的膳食计划示例中，我不想要通用食谱——我希望 AI 使用**我**收藏的、经过验证且我知道自己喜好的食谱。复杂 prompts 通过将 prompt 文本与嵌入的 resources 捆绑在一起使这成为可能。

工作原理如下：

1. **用户选择一个 prompt** 并带参数（例如，"plan-meals" 且 cuisine="italian"）
2. **Server 返回** 指令文本和 resource 引用
3. **Client 决定如何处理 resources** - 应用程序可能选择使用 embeddings 或关键词搜索来选择数据子集，或将原始数据直接传递给模型
4. **AI 接收到上下文** 并生成响应

在我的示例中，VS Code 将整个 resource 附加到 prompt，这对这个用例效果很好。在规划意大利菜周时，AI 可以访问我所有的意大利食谱，确保它只推荐我确实有食谱的菜品。

与简单 prompts 的关键区别：不是问"规划意大利餐"并得到通用建议，而是 AI 使用您实际的食谱收藏、饮食偏好和约束来工作。

<img
    src="/posts/images/prompts-rendered-prompt.png"
    alt="VS Code 显示渲染后的 prompt 及附加的食谱 resources"
  />

我们一直在使用的食谱 resources 是**嵌入 resources**，它们包含来自 server 的内联内容。根据 [MCP 规范](https://modelcontextprotocol.io/specification/2025-06-18/server/prompts#data-types)，prompts 还可以包含其他数据类型。

这使得超越我们基于文本的食谱的高级用例成为可能，比如带截图的审阅 prompts 或语音转录服务。

## 构建 Recipe Server

让我们实现一个完整的 MCP server，将我们讨论的所有概念结合起来。我们将从 server 设置开始，然后实现每个能力。

### 先决条件

在深入代码之前，请确保您已准备好：

1. **Node.js**（v18 或更高版本）和 npm 已安装
2. **MCP SDK** 已安装：
   ```bash
   npm install @modelcontextprotocol/sdk
   ```
3. **支持 prompt 和 resource 的 MCP 兼容 client**，例如带有 MCP 扩展的 VS Code

在本教程中，我将使用 TypeScript SDK，但 MCP 也支持 Python 和其他语言。

### Server 设置和能力

首先，让我们创建 MCP server：

```typescript
const server = new McpServer({
  name: "favorite-recipes",
  version: "1.0.0",
});

async function main() {
  const transport = new StdioServerTransport();
  await server.connect(transport);
}

main().catch((error) => {
  console.error("Server error:", error);
  process.exit(1);
});
```

### 实现 Resources

接下来，让我们注册一个带有 completions 的 resource template。

```typescript
server.registerResource(
  "recipes",
  new ResourceTemplate("file://recipes/{cuisine}", {
    list: undefined,
    complete: {
      cuisine: (value) => {
        return CUISINES.filter((cuisine) => cuisine.startsWith(value));
      },
    },
  }),
  {
    title: "Cuisine-Specific Recipes",
    description: "Traditional recipes organized by cuisine",
  },
  async (uri, variables, _extra) => {
    const cuisine = variables.cuisine as string;

    if (!CUISINES.includes(cuisine)) {
      throw new Error(`Unknown cuisine: ${cuisine}`);
    }

    const content = formatRecipesAsMarkdown(cuisine);
    return {
      contents: [
        {
          uri: uri.href,
          mimeType: "text/markdown",
          text: content,
        },
      ],
    };
  },
);
```

### 实现 Prompts

最后，让我们注册 prompt，它也带有 completions：

```typescript
server.registerPrompt(
  "weekly-meal-planner",
  {
    title: "Weekly Meal Planner",
    description:
      "Create a weekly meal plan and grocery shopping list from cuisine-specific recipes",
    argsSchema: {
      cuisine: completable(z.string(), (value) => {
        return CUISINES.filter((cuisine) => cuisine.startsWith(value));
      }),
    },
  },
  async ({ cuisine }) => {
    const resourceUri = `file://recipes/${cuisine}`;
    const recipeContent = formatRecipesAsMarkdown(cuisine);

    return {
      title: `Weekly Meal Planner - ${cuisine} Cuisine`,
      description: `Weekly meal planner for ${cuisine} cuisine`,
      messages: [
        {
          role: "user",
          content: {
            type: "text",
            text: `Plan cooking for the week. I've attached the recipes from ${cuisine} cuisine.

Please create:
1. A 7-day meal plan using these recipes
2. An optimized grocery shopping list that minimizes waste by reusing ingredients across multiple recipes
3. Daily meal schedule with specific dishes for breakfast, lunch, and dinner
4. Preparation tips to make the week more efficient
5. Print Shopping list

Focus on ingredient overlap between recipes to reduce food waste.`,
          },
        },
        {
          role: "user",
          content: {
            type: "resource",
            resource: {
              uri: resourceUri,
              mimeType: "text/markdown",
              text: recipeContent,
            },
          },
        },
      ],
    };
  },
);
```

## 自己运行

[Recipe server 的完整代码可在此处获取](https://github.com/ihrpr/mcp-server-fav-recipes)。

按照 VS Code 的[文档设置 server](https://code.visualstudio.com/docs/copilot/chat/mcp-servers)。在 VS Code 中设置好 server 后，您可以查看其状态、调试正在发生的事情，并快速迭代您的自动化。

在 VS Code 中设置 server 后，在聊天中输入 "/" 并选择 prompt。

<img
    src="/posts/images/prompts-list.png"
    alt="MCP prompts 列表，显示可用的自动化命令"
 />

## 扩展您的自动化

MCP prompts 开启了令人兴奋的自动化可能性：

- **Prompt 链**：按顺序执行多个 prompts（规划膳食 → 生成购物清单 → 下单购买）
- **动态 Prompts**：根据可用资源或季节进行调整
- **跨 Server 工作流**：协调多个 MCP servers 实现复杂自动化
- **外部触发器**：通过 webhooks 或计划任务激活 prompts

在膳食计划中展示的模式适用于许多领域：

- 了解您代码库的文档生成
- 可访问您数据源的报告创建
- 理解您项目结构的开发工作流
- Customer support automations with full context

**Key takeaways:**

- MCP prompts can include dynamic resources, giving AI full context for tasks
- Resource templates enable scalable content serving without duplication
- Modular server architecture lets you mix and match capabilities

## Wrapping Up

This meal planning automation started as a simple desire to avoid rewriting shopping lists every week. It evolved into a complete system that handles meal planning, shopping lists, and recipe printing with just a few clicks.

MCP prompts provide practical tools to automate repetitive tasks. The modular architecture means you can start small—perhaps just automating one part of your workflow—and expand as needed. Whether you're automating documentation, reports, or meal planning, the patterns remain the same: identify repetitive tasks, build focused automations, and let the system handle the tedious parts.
