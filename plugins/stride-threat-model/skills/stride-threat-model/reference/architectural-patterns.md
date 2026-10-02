# Architectural Pattern Detection for AI Applications

Before generating threats for GenAI or Agentic applications, scan the description for these patterns. Each detected pattern adds a specific set of threats. Inspired by the [Cloud Security Alliance MAESTRO framework](https://cloudsecurityalliance.org/blog/2025/02/06/agentic-ai-threat-modeling-framework-maestro).

## 1. RAG / Retrieval Systems

**Detection triggers**: RAG, retrieval, vector databases, embeddings, knowledge bases, document ingestion, Pinecone, Weaviate, ChromaDB, FAISS, semantic search.

**Threats to include**:
- Vector store poisoning — malicious documents injected into knowledge base
- Embedding manipulation — adversarial content designed to surface in retrieval
- Cross-tenant data leakage (if multi-tenant RAG is implied)
- Stale/poisoned index — if incremental updates are mentioned

## 2. Multi-Agent Systems

**Detection triggers**: multiple agents, agent orchestration, agent-to-agent communication, CrewAI, AutoGen, LangGraph, swarms, hierarchical agents, agent coordination.

**Threats to include**:
- Agent impersonation — malicious agent posing as trusted agent
- Inter-agent message tampering
- Malicious agent injection into the ecosystem
- Goal conflicts and unintended emergent behaviors
- Cascading failure propagation across agent chains

## 3. Code Execution / Sandboxing

**Detection triggers**: code generation, code execution, REPL, interpreter, sandbox, Docker, containers, executing generated code, `eval`, `exec`.

**Threats to include**:
- Sandbox escape
- Container breakout (if containerized)
- Resource exhaustion via generated code (fork bombs, infinite loops)
- Filesystem/network access beyond intended scope
- Malicious dependency installation (if package installation is possible)

## 4. Tool / Plugin Ecosystems

**Detection triggers**: MCP servers, plugins, tools, function calling, external APIs, third-party integrations, tool use.

**Threats to include**:
- Malicious tool provider — tool returning poisoned data
- Tool impersonation — fake tool masquerading as legitimate
- Supply chain attacks via compromised tool packages
- Confused deputy attacks — agent tricked into misusing legitimate tools

## 5. Persistent Memory / State

**Detection triggers**: memory, conversation history, session persistence, long-term memory, memory stores, context carried across sessions.

**Threats to include**:
- Memory poisoning — past interactions corrupting future behavior
- Cross-session data leakage
- Memory extraction attacks — retrieving other users' context
- State manipulation — altering agent personality/goals over time

## 6. Fine-Tuned / Custom Models

**Detection triggers**: fine-tuning, custom training, LoRA, adapters, proprietary models.

**Threats to include**:
- Training data poisoning
- Backdoor trigger injection during fine-tuning
- Model supply chain — compromised base model or training pipeline
- Intellectual property extraction from fine-tuned model

## 7. Autonomous Operations

**Detection triggers**: autonomous, automated, scheduled, background tasks, unattended execution, long-running agents.

**Threats to include**:
- Human oversight bypass — gradually normalizing risky actions
- Rogue agent persistence beyond intended lifecycle
- Autonomous loop attacks — infinite reasoning cycles
- Time-of-attack chosen to evade monitoring windows
