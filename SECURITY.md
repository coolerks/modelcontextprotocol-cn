# 安全政策

感谢您帮助保护 Model Context Protocol 及其生态系统的安全。

## 报告安全 Issues

如果您在此仓库中发现安全漏洞，请通过
[GitHub 安全公告流程](https://docs.github.com/en/code-security/security-advisories/guidance-on-reporting-and-writing-information-about-vulnerabilities/privately-reporting-a-security-vulnerability)
为该仓库报告。

请**不要**通过公开的 GitHub issues、讨论或 pull requests 报告安全漏洞。

## 需要包含的内容

为帮助我们快速分类和响应，请包含：

- 漏洞描述
- 重现步骤
- 潜在影响
- 任何建议的修复（可选）

## 预期行为与信任模型

本节记录了 MCP 中有意为之的设计选择，这些行为**不**被视为安全漏洞。理解这些行为有助于
开发者构建准确的威胁模型，使安全研究人员能够专注于
真正的漏洞，并明确 MCP 中所有实现者的信任边界。

### 信任模型

MCP 旨在使 AI 应用能够与外部工具、数据源和服务进行交互。该协议在以下信任假设下运行：

**MCP clients 信任它们连接的 MCP servers。** 当用户或应用
将 MCP client 配置为连接到 server 时，client 信任该 server 能提供
tools、resources 和 prompts。这种信任关系的安全性
取决于用户或管理员对 server 的正确选择和配置。

**本地 MCP servers 像您安装的任何其他软件一样被信任。** 当您运行
本地 MCP server 时，您对其信任的访问级别与您系统上的任何其他
应用或软件包相同。就像您在安装前会评估库或工具的
可信度一样，您也应该在运行 MCP server 前进行评估。

**MCP servers 信任它们所运行的执行环境。** Servers 可以访问其执行上下文中可用的
资源。这是有意设计的，因为 servers 需要访问
本地文件、数据库、API 或其他资源以提供其预期功能。

**用户和管理员负责 server 的选择。** MCP clients 应
提供关于 server 能力的清晰信息，但连接和使用 server 的
决定权在于用户或管理员。某些 clients 可能会根据配置自动连接
到某些 servers；用户应检查这些设置。

### 不属于漏洞的行为

以下行为是 MCP 的有意功能，**不**属于安全漏洞报告的范围：

#### STDIO 传输的命令执行

使用 STDIO 传输的 MCP clients 通过执行命令来启动 MCP servers。这种
命令执行是预期功能，而非漏洞：

- Clients 执行配置的命令以启动 server 进程
- Server 进程以与 client 相同的权限运行
- 配置中指定的命令参数会传递给 server

**这是预期行为。** 用户配置要运行哪些 servers，然后 client
执行这些配置。关于通过 STDIO 传输配置进行"任意命令执行"的报告，
无论是在 MCP client 应用还是 SDK 中，都不是漏洞。进程生成是 STDIO 传输机制的核心功能。

#### Server 能力与副作用

MCP servers 提供的能力可能会对系统或外部服务产生显著影响。
这些能力是功能，而非漏洞：

**文件系统访问：** 像 reference filesystem server 这样的 server 会故意
在其配置范围内读取、写入和列出文件。文件系统 server 的
目的是为 AI 应用提供文件访问。

**Git 和版本控制：** 提供 git 功能的 servers 可以执行 git 命令，
这可能包括重置提交或强制推送等操作。如果您
授予 AI agent 对 git 命令的不受限制的访问权限，它可以执行任何 git
操作——这不是 server 的漏洞。

**数据库操作：** Servers 可以根据其预期目的执行查询、修改数据或管理数据库 schema。

**网络和 API 访问：** Servers 可以发出 HTTP 请求、调用外部 API 或
与远程服务交互。

**系统命令：** 某些 servers 被设计为执行系统命令或脚本。

**这是预期行为。** 执行其文档中所述功能的 servers 是按预期工作的。
关于"server X 可以执行操作 Y"的报告，当 Y 是 server 的预期目的时，不构成漏洞。
这些能力的适当保护措施和权限由部署 server 的用户或管理员负责。

#### Resource 访问模式

MCP resources 向 clients 暴露数据。Servers 可能提供包含文件内容、
数据库查询结果、API 响应或系统信息的 resources。

**这是预期行为。** Resources 旨在为 AI 应用提供上下文和数据。
可访问数据的范围由 server 的实现和配置决定。

#### LLM 驱动的 Tool 调用

当 AI 应用使用 MCP 时，语言模型根据用户请求和可用的 tool 描述来决定调用哪些 tools。
这意味着：

- LLM 可能以用户未明确请求的方式调用 tools
- Tool 调用取决于 LLM 如何解释用户的意图
- 多个 tools 可能按顺序被调用

**这是预期行为。** LLM 驱动的 tool 选择是 AI 应用如何使用 MCP 的基础。
关于"LLM 调用了意外的 tool"的报告不是 MCP 漏洞，因为它们涉及的是 LLM 行为和应用程序级别的控制。

### 开发者和运营者责任

MCP 的安全模型对开发者和运营者施加了特定责任：

**Server 开发者负责：**

- 在其 servers 中实现适当的访问控制
- 记录其 servers 所需的能力和权限
- 在执行敏感操作前验证来自 clients 的输入
- 在 server 设计中遵循最小权限原则

**Client 开发者负责：**

- 向用户提供关于 server 能力的清晰信息
- 在连接到 servers 前实现适当的同意机制
- 在适当时向用户显示 tool 调用和 resource 访问
- 在可行的情况下对 server 执行进行沙箱隔离

**运营者和用户负责：**

- 仅连接到受信任的 MCP servers
- 在部署前审查 server 配置
- 了解他们启用的 servers 的能力
- 为其环境配置适当的访问限制

有关构建和部署安全的 MCP 实现的更多指导，请参阅
[安全最佳实践](https://modelcontextprotocol.io/specification/draft/basic/security_best_practices)文档。

### 仍在范围内的问题

以下类别在由 MCP 规范或官方 SDK 实现中的缺陷引起时，**属于**安全漏洞：

- **协议级漏洞**：MCP 规范中的缺陷，无论实现如何都能启用攻击
- **身份验证/授权绕过**：在未获得适当授权的情况下访问 resources 或调用 tools 的方式
- **实现漏洞**：特定 SDK 实现中的错误（缓冲区溢出、注入缺陷等）
- **沙箱逃逸**：突破协议或 SDK 中明确定义的预期隔离边界
- **会话劫持**：未经授权访问其他用户的会话
- **令牌窃取或泄露**：暴露访问令牌的漏洞
- **跨租户访问**：在多租户部署中访问属于其他用户的资源

此列表并非详尽无遗。

### 报告指南

在评估是否报告潜在安全问题时：

1. **首先查看本文档。** 如果该行为被列为预期行为，则不是漏洞。
2. **考虑信任模型。** 如果问题需要攻击者已经拥有信任模型假定他们拥有的访问权限，则可能不是漏洞。
3. **关注意外访问。** 漏洞通常涉及访问资源或执行在既定信任边界内不应可能的操作。
4. **提供上下文。** 如果您认为找到了真正的漏洞，请解释它如何违反了预期的安全边界。
