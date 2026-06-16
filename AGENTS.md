# Model Context Protocol (MCP)

本仓库包含 MCP 规范、文档和博客。

## 文档结构

- `docs/` - Mintlify 站点（`npm run serve:docs`）
  - `docs/docs/` - 指南和教程
  - `docs/specification/` - MCP 规范（更正式，版本化）
- `blog/` - Hugo 博客（`npm run serve:blog`）

### 文档指南

- 在创建流程图和图表以可视化协议时，尽可能使用 mermaid 图表。
- 在编写表格时，确保列标题和列与空白对齐。
- 在推送或创建 PR 之前，确保 `npm run prep` 没有警告和错误。

## 规范版本管理

规范使用**基于日期的版本管理**（YYYY-MM-DD），而非语义化版本：

- `schema/[YYYY-MM-DD]/` 和 `docs/specification/[YYYY-MM-DD]/` - 已发布版本
- `schema/draft/` 和 `docs/specification/draft/` - 进行中的工作

## Schema 生成

TypeScript 文件是协议 schema 的**唯一事实来源**：

- 编辑：`schema/[version]/schema.ts`
- 生成 JSON + 文档：`npm run generate:schema`
- 这将创建 `schema/[version]/schema.json` 和 `docs/specification/[version]/schema.mdx` 中的 Schema 参考文档

编辑 schema 文件后始终重新生成。

## Schema 示例

JSON 示例位于 `schema/[version]/examples/[TypeName]/`：

- 目录名 = schema 类型（例如 `Tool/`、`Resource/`）
- 文件根据其目录类型进行验证：`Tool/example-name.json` → Tool schema
- 通过 `@includeCode` JSDoc 标签在 `schema.ts` 中引用

## 代理技能

添加新技能时，还需在 `docs/.mintlify/skills/<name>` 处创建一个指向 `../../../plugins/<plugin-name>/skills/<name>` 的目录符号链接，以便 Mintlify 的 `.well-known/agent-skills/` 和 MCP server 自动扫描能够发现它。

## 常用命令

```bash
# 开发服务器
npm run serve:docs       # 本地 Mintlify 文档服务器
npm run serve:blog       # 本地 Hugo 博客服务器

# 生成（编辑源文件后运行）
npm run generate         # 生成全部（schema + SEP）
npm run generate:schema  # 从 TypeScript 生成 JSON schema + MDX
npm run generate:seps    # 生成 SEP 文档

# 格式化
npm run format           # 格式化全部（文档 + schema）
npm run format:docs      # 格式化 Markdown/MDX 文件
npm run format:schema    # 格式化 schema TypeScript 文件

# 检查
npm run check            # 运行所有检查
npm run check:schema     # 检查 schema（TS、JSON、示例、MDX）
npm run check:docs       # 检查文档（格式、评论、链接）
npm run check:seps       # 检查 SEP 文档

# 工作流
npm run prep             # 提交前的完整准备（检查、生成、格式化）
```

## 创建 Issue

禁止空白 Issue。`gh issue create` 和 API 会绕过模板选择器，因此通过 CLI 或 API 提交时，你**必须**使用 `.github/ISSUE_TEMPLATE/` 中的某个表单并填写其必填字段。

在提交前，检查 `.github/ISSUE_TEMPLATE/config.yml` — 某些类别会完全重定向到此仓库之外：

- **SEP** 是向 `seps/` 添加文件的拉取请求，而不是 Issue
- **SDK 错误** 属于各个 SDK 仓库
- **Claude MCP 行为** 属于 `anthropics/claude-ai-mcp`

## 提交指南

- 不要在提交信息中包含模型名称或细节（例如 "Claude"、"Opus"）
