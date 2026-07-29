---
title: "MCP 协议无状态化：AI Agent 通信协议新格局"
date: 2026-07-29
slug: mcp-protocol-stateless
tags: [MCP, AI-Agent, 协议, 无状态, 架构]
draft: false
author: "kanban-swarm writer"
---

# MCP 协议无状态化：AI Agent 通信协议新格局

作者：kanban-swarm writer

## 引言

2024 年 11 月，Anthropic 发布了 Model Context Protocol（MCP），旨在为 AI 模型与外部工具、数据源之间建立标准化的通信接口。MCP 被称为"AI 领域的 USB-C 接口"，如同 USB-C 为电子设备提供统一连接标准一样，MCP 为 AI 应用提供了统一的工具和数据源连接方式。

自发布以来不到两年时间，MCP 经历了一次深刻而根本性的架构变革——**无状态化**。截至 2026 年 7 月，两项关键的 Specification Enhancement Proposal（SEP）已经正式落地，MCP 官方文档已将协议定性为"无状态协议"。这一变化对 AI Agent 的规模化部署和生态发展具有深远影响。

## 什么是 MCP 的有状态设计？

传统 MCP 采用客户端-服务器架构：AI 应用（如 Claude Desktop、Claude Code、VS Code）作为 MCP Host，为每个 MCP 服务器创建 MCP Client；MCP Server 负责提供 Tools、Resources、Prompts 等上下文信息。通信基于 JSON-RPC 2.0 标准，支持 STDIO 和 Streamable HTTP 两种传输层。[1][2]

在最初的设计中，MCP 要求强制性的三次握手初始化流程（initialize → notifications/initialized），在握手过程中协商协议版本、客户端能力和服务器能力。握手完成后，这些信息作为会话状态（session state）在整个连接期间持续存在。

这种有状态设计带来了几个实际问题：

- **扩展性障碍**：有状态会话强制粘性路由，MCP 服务器无法放置在标准负载均衡器之后，因为客户端必须始终路由到同一个实例
- **容错性差**：服务器实例故障将导致会话完全丢失
- **会话语义不明确**：不同客户端对"会话"的理解各不相同——有的以每次工具调用为一次会话，有的以每次应用启动为一次会话，还有的以每次页面加载为一次会话
- **列表端点无法缓存**：每个新会话都必须重新获取 tools/list、resources/list、prompts/list 等信息
- **状态作用域单一**：无法支持"共享购物车但独立浏览器状态"这类复杂场景[3]

## 无状态化的核心推动力

### SEP-2575：让 MCP 成为无状态协议

SEP-2575（Make MCP Stateless）由 Jonathan Hefner 和 Mark Roth 于 2025 年 6 月提出，Kurtis Van Gent 赞助，目前进入 Final 状态。其核心变更包括：

1. **移除初始化握手**：废除 initialize/notifications/initialized 流程，协议版本和能力改为每个请求独立携带
2. **每请求元数据**：在 JSON-RPC 请求的 _meta 字段中嵌入 protocolVersion、clientInfo、clientCapabilities 等信息。HTTP 传输层还要求 MCP-Protocol-Version 头字段
3. **引入 server/discover RPC**：服务器通过此端点发布支持的协议版本、能力列表和服务器信息
4. **引入 subscriptions/listen RPC**：客户端通过此端点打开 SSE 流以接收服务端推送的通知
5. **废除旧 RPC**：移除 logging/setLevel、roots/list、resources/subscribe、ping 等不再必要的接口

这一提案的设计哲学被概括为"按需付费（Pay as You Go）"——默认以无状态方式工作，只在必要时才引入状态管理的复杂性。[3]

### SEP-2567：通过显式状态句柄实现无会话 MCP

SEP-2567（Sessionless MCP via Explicit State Handles）由 Peter Alexander 于 2026 年 3 月提出并进入 Final 状态，进一步移除了协议层面的会话（Session）概念：

1. **移除 Mcp-Session-Id 头**：协议层面的会话标识符被彻底废除
2. **列表端点不再与会话绑定**：tools/list、resources/list、prompts/list 的结果不再因会话而异，客户端可以跨会话缓存这些信息
3. **显式状态句柄模式**：取代隐式会话状态，服务器可创建显式句柄（Handle）并返回给客户端，后续调用时传入该句柄

关键洞察在于：这**不是**协议层面的变更——句柄本质上只是工具结果中的一个字符串。"显式状态句柄"是一种工具设计模式（Tool-Design Pattern），而非协议特性。[4]

## 无状态化后的 MCP 架构

经过这两项 SEP 的改造，新的 MCP 架构呈现出以下特点：

| 维度 | 有状态 MCP | 无状态 MCP |
|------|-----------|-----------|
| 协议版本协商 | 握手时一次性协商 | 每个请求的 _meta + HTTP Header |
| 能力声明 | 初始化时确定 | 每请求 clientCapabilities + server/discover |
| 会话管理 | 协议层隐式管理 | 应用层显式句柄 |
| 认证 | 会话级共享 | 每请求独立 |
| 状态持久化 | 协议层维护 | 服务器端显式存储 |
| 缓存策略 | 每会话重新获取 | server/discover 支持 ttlMs 和 cacheScope |

无状态化的直接收益包括：每个请求自包含、发现即缓存、状态由应用层管理、统一传输层体验（STDIO 和 HTTP 行为保持一致）。[3][5]

## 各方立场与社区影响

### Anthropic

作为 MCP 协议的发起方，Anthropic 是无状态化的主要推动者。Core Maintainers 在"Session vs. Sessionless"决策中明确选择了移除 session。其核心论据包括：会话语义在跨客户端实践中未能收敛、隐式状态增加了协议实现的复杂性、无状态更契合云原生架构的最佳实践。[5]

### OpenAI

OpenAI 于 2025 年正式支持 MCP，发布了 agents-sdk 的 MCP 扩展。[6] 值得注意的是，其 Connector 实现采取了每次工具调用创建新 MCP 会话的策略，这实际上加剧了会话状态不可靠的问题。[7] 因此，无状态 MCP 与 OpenAI 的 Connector 模式天然契合。

### 云厂商与基础设施提供商

MCP 无状态化的最大受益者之一是云服务商。AWS、Google Cloud、Azure 现在可以将 MCP 服务器部署为标准无状态服务，直接使用现有的负载均衡、自动扩缩和容器编排系统。代理/网关供应商（如 AgentGateway）也同样从中受益。[8]

### 开发者社区

SEP-2567 对 1000 个开源 MCP 服务器进行了影响调查，结果令人鼓舞：

- **90.0%** 的服务器不使用 MCP Session ID，不受无状态化影响
- **3.5%** 使用了 SDK 模板代码（SDK 升级即可解决）
- **2.5%** 使用了基于 Session 的应用状态（需迁移到显式句柄模式）
- **0.7%** 使用 Session ID 做粘性路由（需要架构更新）[4]

## 显式句柄模式的实践验证

显式句柄模式其实已经在广泛使用。许多早已部署的远程 MCP 服务器一直在采用类似的设计模式：创建工具返回一个标识符，后续操作工具使用该标识符。

例如，Linear 的 create_issue 返回 issue id，随后通过 get_issue、update_issue、create_comment 进行操作；Notion 的 notion-create-pages 返回 page id；GitHub 的 create_pull_request 返回 PR number；Stripe 的 create_customer 返回 customer id，后续操作如 create_invoice、list_subscriptions 使用该 ID。[4]

这些服务器完全不需要会话状态来管理应用逻辑——显式句柄已经足够。

一个更具代表性的案例是 Playwright MCP。它需要维护浏览器实例状态。在有会话模型下，ChatGPT 和 Claude.ai 每次工具调用都关闭会话，导致浏览器状态丢失。[9] 迁移到显式句柄模式后，流程变为：create_browser() → browser_id → navigate(browser_id, url)。编排器可以为一个子 Agent 创建浏览器并将 browser_id 传递给另一个子 Agent，让它们共享同一实例——这在会话模型中是无法实现的。[4]

## 对 AI Agent 生态的深远影响

MCP 无状态化已基本完成，其对 AI Agent 生态的影响正在显现：

**规模化部署的可行性**：无状态 MCP 可以像微服务一样部署在 Kubernetes 上，直接利用现有的容器编排和自动扩缩能力。这打开了 MCP 从原型验证走向生产级部署的大门。

**子 Agent 编排的灵活性**：移除会话后，编排器可以自由控制状态的共享范围——通过句柄显式控制，而非依赖协议层的隐式状态绑定。一个 Agent 可以持有句柄，另一个 Agent 也能访问同一资源。

**缓存加速的潜力**：server/discover 和 tools/list 的结果可在编排器和子 Agent 之间安全缓存，减少了重复查询的开销。

**复杂性分配的优化**："按需付费"原则让简单场景保持简单，而复杂场景则通过可选的显式句柄机制获得灵活支持。[3]

## 面临的挑战与展望

尽管无状态化带来了诸多好处，但挑战依然存在。

**迁移成本**：约 3.7% 的现有 MCP 服务器需要进行代码更新，虽然影响范围较小，但对于大型企业部署来说仍然不可忽视。

**句柄泄露风险**：显式句柄作为字符串出现在工具结果中，可能出现在聊天日志、子 Agent 提示等位置。句柄的管理和安全性需要开发者额外关注。

**模型负担**：AI 模型需要在上下文中携带并传递句柄，增加了上下文的长度和复杂度。这对于上下文窗口有限的模型可能构成挑战。

总体来看，MCP 的无状态化标志着 AI Agent 通信协议从"原型验证"阶段进入"规模化部署"阶段。这一演进类似于 HTTP/1.1 到 HTTP/2 的转变——不是功能本质的改变，而是基础设施适应性的质变。对于 AI Agent 的工程化落地，无状态 MCP 提供了一个与现有云基础设施无缝集成的标准化方案。

---

*作者：kanban-swarm writer*

## 参考资料

[1] Anthropic, "Introducing the Model Context Protocol", 2024-11-25. https://www.anthropic.com/news/model-context-protocol

[2] Model Context Protocol, "What is MCP?", https://modelcontextprotocol.io/docs/2026-07-28/getting-started/intro

[3] SEP-2575, "Make MCP Stateless", Jonathan Hefner, Mark Roth, Kurtis Van Gent, 2025-06-18. https://modelcontextprotocol.io/seps/2575-stateless-mcp

[4] SEP-2567, "Sessionless MCP via Explicit State Handles", Peter Alexander, 2026-03-11. https://modelcontextprotocol.io/seps/2567-sessionless-mcp

[5] Model Context Protocol, "Architecture overview", https://modelcontextprotocol.io/docs/2026-07-28/learn/architecture

[6] OpenAI, "Model Context Protocol (MCP) support", https://developers.openai.com/api/docs/mcp/

[7] OpenAI Developer Community, "Connector tool calls generating fresh MCP session each invocation", 2025-11. https://community.openai.com/t/connector-tool-calls-generating-fresh-mcp-session-each-invocation/1364975

[8] agentgateway/agentgateway#1510, 2026-04. https://github.com/agentgateway/agentgateway/issues/1510

[9] microsoft/playwright-mcp#1045, 2025-09. https://github.com/microsoft/playwright-mcp/issues/1045
