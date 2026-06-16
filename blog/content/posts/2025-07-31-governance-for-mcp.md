---
title: "行稳致远：MCP 的新治理模型"
date: "2025-07-31T00:00:00Z"
description: "介绍 MCP 的正式治理模型：规范增强提案 (SEP)、维护者结构以及社区驱动的协议演进流程。"
author:
  - David Soria Parra（首席维护者）
tags:
  - 公告
  - 治理
  - 社区
---

自 2024 年 11 月开源发布以来，Model Context Protocol 项目的增长速度超出了我们的想象。这是一个美妙的问题，但增长也伴随着成长的烦恼。我们现有的流程在小团队中运作良好，但现在已开始显现其局限性。

今天，我们迈出了重要一步，以确保 MCP 能够继续增长和蓬勃发展。我们引入了一个正式的治理模型，旨在为开发过程带来清晰度，同时保留使 MCP 成功的协作式开源精神。

## 规范增强提案 (SEP)

我们引入的首要重大变化之一是[规范增强提案](https://modelcontextprotocol.io/community/sep-guidelines) (SEP)。这将是为任何人提议 MCP 变更的主要机制。SEP 的灵感来自其他项目，如 [Python PEPs](https://peps.python.org/) 或 [Rust RFCs](https://github.com/rust-lang/rfcs)。我们的目标是使向 Model Context Protocol 提出变更建议的过程尽可能简单：

1. 遵循 [SEP 指南](https://modelcontextprotocol.io/community/sep-guidelines)，以 [GitHub issue](https://github.com/modelcontextprotocol/modelcontextprotocol/issues) 形式提交提案，开启讨论。
2. 我们的维护者和核心维护者定期审查提案，并将 SEP 标记为待审查和赞助。您也可以在 [Discord](https://discord.gg/6CSzBmMkjX) 或 [GitHub](https://github.com/modelcontextprotocol/modelcontextprotocol) 上联系和协作。请参阅 [`MAINTAINERS.md`](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/MAINTAINERS.md) 获取当前活跃维护者及其关注领域的列表。
3. 与赞助者和 MCP 社区合作，将您的提案推进到草案、审查和实施阶段。

SEP 为协议演进提供了清晰、有记录的路径，确保每个重大变更都经过社区的充分审查。

## 领导角色

新模型还确立了三种类型的领导角色，确保专注的所有权和广泛的社区代表性：

- **Maintainers（维护者）** 管理特定组件，如 SDK、文档和各个仓库。
- **Core Maintainers（核心维护者）** 指导项目的总体方向和 MCP 规范的演进。
- **Lead Maintainers（首席维护者）** 担任最终决策者，确保项目的长期健康。

所有维护者组成 **MCP 指导组**。为确保对收到的提案进行结构化和及时的审查，我们的核心和首席维护者将每两周召开一次会议，审查已提交的 [SEP](#规范增强提案-sep)。会议记录和决策将始终公开。例如[2025 年 7 月 23 日核心维护者会议记录](https://github.com/modelcontextprotocol/modelcontextprotocol/issues/1061)。

## 参与其中

我们需要您的帮助来构建 MCP 的未来，欢迎每个人加入。无论您是经验丰富的开源老手，还是刚开始探索的好奇心者，我们的社区都有您的位置。

我们的许多维护者都是从一个小小的贡献开始的——有时只是修正一个拼写错误或提出一个有深度的问题。每段旅程都始于某个起点，我们很高兴能帮助您迈出第一步。

- **新贡献者**：不确定从哪里开始？从帮助文档、修复 bug 或构建示例开始。每项贡献都很重要，我们在这里支持您。查看标记了 [`good first issue`](https://github.com/modelcontextprotocol/modelcontextprotocol/issues?q=is%3Aissue%20state%3Aopen%20label%3A%22good%20first%20issue%22) 的 issues - 它们非常适合入门，您会发现友善的面孔随时准备提供帮助。
- **SDK 开发者**：有偏爱的编程语言吗？随着 MCP 的发展，我们需要您的专业知识来构建和维护协议的 SDK。您的工作可以让全新的社区使用 MCP。
- **文档作者**：清晰、全面的[文档](https://modelcontextprotocol.io/introduction)是优秀项目与卓越项目之间的分水岭。如果您喜欢解释事物或制作指南，您的贡献将帮助他人取得成功。
- **未来的维护者**：我们相信从内部培养团队。成为维护者的道路始于持续、高质量的贡献和对项目成功的承诺。想象一下您指导新贡献者并塑造 MCP 的未来。

无论您的背景或经验如何，您都属于这里。加入我们的 [Discord](https://discord.gg/6CSzBmMkjX) 与其他贡献者交流、提问并寻找指导。无论您是在修正拼写错误还是提议对协议进行重大变更，您的声音都受到重视，您的努力都会带来改变。

有关所有详细信息，请参阅我们的完整[治理文档](https://modelcontextprotocol.io/community/governance)。

## 感谢

没有围绕 MCP 凝聚起来的非凡社区，这一切都不可能实现。从相信愿景的早期采用者，到构建 MCP clients 和 servers 的开发者，再到贡献时间和专业知识的维护者。每项贡献对于 Model Context Protocol 取得今天的成功都至关重要。

你们帮助我们识别问题、改进文档、构建 SDK、创建引人注目的示例，并推动了平台集成的可能性边界。你们的反馈、bug 报告、功能请求和代码贡献已将 MCP 塑造成了一个远优于我们独自构建的成果。

在我们开启正式治理的新篇章之际，我们比以往任何时候都更致力于培育使 MCP 蓬勃发展的开放、包容的社区。感谢您成为这段旅程的一部分——我们迫不及待地想看到我们接下来一起构建什么。
