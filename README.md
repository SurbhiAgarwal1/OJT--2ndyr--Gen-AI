# 🚀 Agent-to-Agent Protocol Implementation (MCP / A2A)

[![Protocol Version](https://img.shields.io/badge/Protocol-MCP%20v1.0.0-blue.svg)](https://modelcontextprotocol.io)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.0+-3178C6.svg?logo=typescript&logoColor=white)](https://typescriptlang.org)
[![Python](https://img.shields.io/badge/Python-3.11+-3776AB.svg?logo=python&logoColor=white)](https://python.org)
[![Official SDK](https://img.shields.io/badge/SDK-%40modelcontextprotocol%2Fsdk-orange.svg)](https://www.npmjs.com/package/@modelcontextprotocol/sdk)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

A production-grade, enterprise reference implementation of Anthropic's **Model Context Protocol (MCP)** and Google's **Agent-to-Agent (A2A)** protocol guidelines. Built to solve the core reliability, scalability, and interoperability challenges in autonomous multi-agent ecosystems.

---

## 👥 Project Identity & Metadata

- **Project Title**: Agent-to-Agent Protocol Implementation (MCP / A2A)
- **Authors**: Surbhi Agarwal & Triveni Reddy
- **Domain**: GenAI · AI/ML Infrastructure — Agent Interoperability
- **Theme**: Agent Interoperability & Protocol Engineering
- **Core Track**: AI/LLM Engineering, Protocol Design, Distributed Systems
- **Primary Stack**: TypeScript / Python · Official MCP SDK (`@modelcontextprotocol/sdk`)
- **Primary References**: Anthropic Model Context Protocol Spec · Google A2A Protocol Docs
- **Target Audience**: AI infrastructure teams building reusable, standardized tool integrations for agent ecosystems
- **Deliverables**: Published MCP Server, Compatible MCP Client (Claude Desktop / Code), Written Architecture Spec, Recorded Demo Video
- **Version**: `1.0.0` (September 2026)

---

## 📌 Problem Statement & Mission

Modern AI agent ecosystems suffer from fragmented, non-standardized integration code ("glue code") when connecting Large Language Models (LLMs) to tools, APIs, and external datasets. 

The **Agent-to-Agent Protocol Platform** solves this by establishing a standardized, production-grade **MCP / A2A Protocol Implementation**. It eliminates custom glue code by providing dynamic tool discovery, multi-transport communication, fine-grained authentication scoping, live server push updates, and multi-server session aggregation.

---

## 🏗️ System Architecture

### 1. System Topology Diagram

```mermaid
graph TD
    subgraph ClientLayer["Client Layer (MCP Clients)"]
        ClaudeClient["Claude Desktop / CLI MCP Client"]
        WebPlayground["Web Playground & Live Protocol Inspector"]
    end

    subgraph TransportLayer["Swappable Transport Layer"]
        StdioTrans["stdio Transport Adapter (Local IPC)"]
        SSETrans["HTTP / SSE Transport Adapter (Networked Push)"]
    end

    subgraph SecurityLayer["Security & Policy Scoping"]
        JWTAuth["Per-Client JWT Authentication"]
        ScopeFilter["Permission & Tool Scope Filter Engine"]
    end

    subgraph RouterEngine["MCP Core Aggregator Router"]
        MultiAggregator["Multi-Server Session Aggregator"]
        VersionNegotiator["Schema Negotiation & Versioning Engine"]
        MetricsExporter["Prometheus Metrics & Latency Monitor"]
    end

    subgraph ServerLayer["Target MCP Tool Servers"]
        NotesServer["Notes & Knowledgebase Server"]
        SearchServer["Semantic Search & RAG Tool"]
        AuditLedger["Immutable Protocol Audit Ledger"]
    end

    %% Flow Connections
    ClaudeClient --> StdioTrans
    WebPlayground --> SSETrans

    StdioTrans --> JWTAuth
    SSETrans --> JWTAuth

    JWTAuth --> ScopeFilter
    ScopeFilter --> VersionNegotiator
    VersionNegotiator --> MultiAggregator
    MultiAggregator --> MetricsExporter

    MultiAggregator --> NotesServer
    MultiAggregator --> SearchServer
    MultiAggregator --> AuditLedger
```

---

### 2. Protocol Interaction & Sequence Diagram

```mermaid
sequenceDiagram
    autonumber
    actor User as Developer / User
    participant Client as MCP Client (Claude API)
    participant Aggregator as Multi-Server Aggregator Proxy
    participant Auth as Auth & Scope Engine
    participant Server as Target MCP Server
    participant SSE as Real-time Push Engine

    User->>Client: Input Natural Language Query
    Client->>Aggregator: Protocol Handshake & Capability Discovery
    Aggregator->>Auth: Validate Client Credentials & Resolve Scopes
    Auth-->>Aggregator: Authorized Tool Manifest
    Aggregator-->>Client: Combined Tool & Resource Schema
    
    User->>Client: Prompt Execution
    Client->>Aggregator: Invoke Tool Request (JSON-RPC)
    Aggregator->>Server: Forward RPC Call over Selected Transport (stdio / SSE)
    Server->>Server: Process Tool Logic & Validate Schemas
    Server-->>Aggregator: Return Execution Payload
    Aggregator-->>Client: Relay Output to LLM Context
    Client-->>User: Synthesize Final Response

    opt Server-Initiated Push Update
        Server->>SSE: Resource Modified Notification
        SSE-->>Client: Streaming Update via SSE Push Event
        Client-->>User: Live Context Refreshed
    end
```

---

## 🧰 Production Feature Matrix

| Feature Tier | Feature Name | Description | Status |
| :--- | :--- | :--- | :---: |
| **Must-Have Core** | **MCP Server** | Exposes tools, resources, and schemas via official `@modelcontextprotocol/sdk` | ✅ Ready |
| **Must-Have Core** | **MCP Client** | Dynamic capability discovery, session management, and LLM tool execution | ✅ Ready |
| **Advanced** | **Dual Transport Support** | Swappable `stdio` (local process IPC) and `HTTP/SSE` (streaming push) | ✅ Ready |
| **Advanced** | **Resource Subscriptions** | Server-initiated push updates for real-time data state updates | ✅ Ready |
| **Advanced** | **Auth & Scope Filter** | Tiered per-client JWT authentication restricting available tools | ✅ Ready |
| **Advanced** | **Multi-Server Aggregation** | Single client orchestrating and aggregating multiple MCP tool servers | ✅ Ready |
| **Advanced** | **Schema Negotiation** | Backward-compatible version negotiation and schema fallback | ✅ Ready |
| **Good-to-Have** | **Web Playground UI** | Interactive browser inspector with live message tracing & latency charts | ✅ Ready |
| **Good-to-Have** | **Chaos Testing Suite** | Adversarial payload injection, network drop recovery & concurrency tests | ✅ Ready |

---

## 🎯 Success Criteria & Verification

- **Zero Manual Configuration**: Server is automatically discovered and consumed by standard MCP clients (Claude Desktop / Code) with zero custom setup.
- **100% Callable Tools**: 100% of exposed tools pass schema validation and end-to-end invocation tests.
- **Resilience**: Graceful recovery from dropped SSE connections, invalid client tokens, and concurrent RPC invocations.

---

## 📁 Repository Structure

```text
Agent-to-Agent-MCP-/
├── mcp-notes-project/
│   ├── client/                  # MCP Client Implementation (TypeScript)
│   ├── server/                  # MCP Server Implementation (TypeScript / Express)
│   ├── PRD.md                   # Product Requirements Document
│   ├── TRD.md                   # Technical Requirements Document
│   ├── Project_Overview.md      # High-Level Architecture Overview
│   └── README.md                # Component Guide               
└── README.md                    # Master Project Documentation
```

---

## ⚡ Quick Start & Setup

### Prerequisites
- **Node.js**: `v20.0.0` or higher
- **npm**: `v9.0.0` or higher
- **Anthropic API Key**: Export `ANTHROPIC_API_KEY` in environment

### 1. Installation
```bash
git clone https://github.com/SurbhiAgarwal1/Agent-to-Agent-MCP-.git
cd Agent-to-Agent-MCP-/mcp-notes-project
npm install
```

### 2. Run MCP Reference Server
```bash
cd server
npm run build
npm start
```

### 3. Run MCP Client
```bash
# In a new terminal tab
cd client
npm run build
npm start
```

---

## 📄 License & Authorship

Distributed under the **MIT License**. See `LICENSE` for details.

- **Maintained by**: **Surbhi Agarwal** & **Triveni Reddy**
- **Date**: September 2026
