# 为 Model Context Protocol 做贡献

感谢您有兴趣为 Model Context Protocol 规范、schema 或文档做出贡献！
本文档概述了如何为本项目做出贡献。

另请参阅 [MCP 通信指南](https://modelcontextprotocol.io/community/communication)，其中说明了讨论如何进行以及在哪里进行。

## 一般先决条件

处理规范需要以下软件：

- Node.js 24 或更高版本
- TypeScript
- TypeScript JSON Schema（用于生成 JSON schema）
- [Mintlify](https://mintlify.com/)（可选，用于文档）
- nvm（可选，用于管理 Node 版本）

### 开始

1. [复刻仓库](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/fork-a-repo)

2. 克隆您的复刻：

   ```bash
   git clone https://github.com/YOUR-USERNAME/modelcontextprotocol.git
   cd modelcontextprotocol
   ```

3. 安装依赖：

   ```bash
   nvm install  # 安装正确的 Node 版本
   npm install  # 安装依赖
   ```

4. 创建新分支：

   ```bash
   git checkout -b feature/your-feature-name
   ```

## Schema 变更

Schema 变更放在 `schema/draft/schema.ts` 中。要验证您的更改，请运行：

```bash
npm run check:schema:ts
```

`schema/draft/schema.json` 和 `docs/specification/draft/schema.mdx` 是从 `schema/draft/schema.ts` 生成的；请勿直接编辑它们。要生成它们，请运行：

```bash
npm run generate:schema
```

### 解决生成文件中的合并冲突

如果您的分支在生成文件（`schema/*/schema.json`、`docs/specification/*/schema.mdx`、`docs/seps/*.mdx`）中与 `main` 冲突，请勿手动解决。合并 `main`，解决源文件（例如 `schema/draft/schema.ts`）中的任何冲突，然后重新生成并提交：

```bash
git merge main
npm run generate
git add .
git commit
```

这些文件在 `.gitattributes` 中标记为 `-merge`，因此 git 会保留您分支的副本并将其标记为冲突，而不是插入冲突标记。

## 文档变更

文档以 MDX 格式编写，位于 [`docs`](./docs) 目录中。

您可以通过运行以下命令在本地预览文档变更：

```bash
npm run serve:docs
```

并使用以下命令进行 lint 检查：

```bash
npm run check:docs
npm run format
```

> [!NOTE]
> 您可以一次性运行 `npm run prep`
> 来执行所有 schema/文档变更。

## 博客变更

博客使用 [Hugo](https://gohugo.io/installation/) 构建，位于 [`blog`](./blog) 目录中。

要在本地预览博客变更：

```bash
npm run serve:blog
```

### 文档指南

贡献文档时：

- 保持内容清晰、简洁且技术准确
- 遵循现有文件结构和命名约定
- 在适当的地方包含代码示例
- 使用正确的 MDX 格式和组件
- 测试所有链接和代码示例
  - 您可以运行 `npm run check:docs:links` 来检查损坏的内部链接。
- 使用适当的标题：教程中使用"何时使用"、"步骤"和"提示"
- 将新页面放在适当的章节（概念、教程等）
- 添加新页面时更新 `docs.json`
- 遵循现有的文件命名约定（`kebab-case.mdx`）
- 在 MDX 文件中包含正确的前置元数据

## 规范提案指南

规范变更遵循 [SEP 流程](https://modelcontextprotocol.io/community/sep-guidelines)。
在起草提案之前，请先查看 [MCP 设计原则](https://modelcontextprotocol.io/community/design-principles)
—— 与这些原则一致的提案能更快通过审查。

最简单的总结：探索问题空间并验证其他人是否也遇到相同问题，
构建一个展示解决方案的原型，然后根据原型给你的经验撰写 SEP。

## 提交变更

1. 将您的更改推送到您的复刻
2. 向主仓库提交 pull request
3. 遵循 pull request 模板
4. 等待审查

## AI 辅助贡献

> [!IMPORTANT]
>
> 如果您使用**任何类型的 AI 辅助**来贡献 Model Context Protocol，
> 必须在 pull request 或 issue 中披露。

我们欢迎并鼓励使用 AI 工具来帮助改进 Model Context Protocol。许多有价值的贡献
都通过 AI 辅助在代码生成、问题检测和功能定义方面得到了增强。

话虽如此，如果您在使用任何类型的 AI 辅助（例如 Claude Code、ChatGPT 等代理）
为 Model Context Protocol 做贡献，**必须在 pull request 或 issue 中披露**，
同时说明使用 AI 辅助的程度（例如，文档注释 vs. 代码生成）。

如果您的 PR 回复或评论是由 AI 生成的，也请披露这一点。

作为例外，无关紧要的空格或拼写修复不需要披露，只要更改限于
代码的小部分或简短短语。

披露示例：

> 此 PR 主要由 Claude Code 编写。

或更详细的披露：

> 我咨询了 ChatGPT 来理解代码库，但解决方案
> 完全由我自己手动编写。

未能披露这一点首先是对 pull request 另一端的人类操作者的不尊重，
而且还会使确定对贡献应施加多少审查变得困难。

在理想世界中，AI 辅助会产生与任何人类同等或更高质量的工作。但这不是我们
今天生活的世界，在大多数没有人类监督或专业知识参与的情况下，它生成的代码
或变更无法合理维护或演进。

### 我们的期望

提交 AI 辅助的贡献时，请确保包含：

- **明确披露 AI 使用** - 您透明地说明 AI 的使用情况以及使用程度
- **人类理解** - 您个人理解这些更改的作用
- **明确理由** - 您可以解释为什么需要此更改以及它如何符合 Model Context Protocol 的目标
- **具体证据** - 包含展示改进的测试用例、场景或示例
- **您自己的分析** - 分享您对端到端体验的看法

### 我们会关闭的情况

我们保留关闭那些似乎未遵循披露政策的提交的权利。

## 许可证

通过贡献，您同意您的代码或规范贡献将
根据 Apache License 2.0 授权。文档贡献（不包括
规范）根据 CC-BY 4.0 授权。详情请参阅 [LICENSE](LICENSE) 文件。

## 安全

请查看我们的[安全政策](SECURITY.md)来报告安全 issues。
