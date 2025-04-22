  ---
layout: post
title: MCP with Stateless Endpoint
date: 2025-03-20
tags: [multi-agents, mcp]
categories: [multi-agents]
---

https://github.com/modelcontextprotocol/specification/discussions/112
https://x.com/opentools_/status/1902374510743187464

MCP现在支持无状态架构，这到底意味着什么？

## **MCP 的核心目标**

首先说明MCP是什么，其实最简单的，可以把MCP ~=Function calling，当使用mcp协议实现了某个应用程序的server之后，对于模型来说，就可以用function calling类似的方式来调用应用程序。从而标准化任何应用程序的接入。

### Agent与Tool的概念

那么我们可以进一步认为：

- Agent = Model + Tool + Loop
- Tool = Code + Doc

我们又想到 

RESTAPI = Code + Doc 

难道MCP==RESTAP吗？并不是，MCP目前是bidirectional以及long live http connection的，因此RESTAPI目前与mcp tool并不相同

### 早期 MCP 设计的局限

在 MCP 的早期版本中，协议依赖于双向长连接（如 HTTP Server-Sent Events），确保客户端和服务器可以相互调用。

- **优势**：允许构建层级代理（hierarchical agents），客户端既可以调用服务器，也可以被服务器调用，实现更复杂的上下文管理。
- **问题**：
    - 资源消耗高，保持长连接的成本较大。
    - 许多 MCP 客户端（包括主流云服务商）并未真正支持此机制。

而mcp在支持了无状态之后，就可以做到

 RESTAPI ~= Tool

这样就可以通过标准的RESTAPI快速适配MCP，无需维护长连接
