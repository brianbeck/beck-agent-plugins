# OWASP Top 10 for Agentic Applications (ASI) → STRIDE Mapping

Apply this **only** when the application type is `Agentic AI application`. Agentic apps get BOTH LLM (LLM01-LLM10) AND ASI (ASI01-ASI10) tags on every applicable threat.

## SPOOFING (Traditional + Agentic)
- Traditional: Identity spoofing, credential theft
- **ASI07 (Insecure Inter-Agent Communication)**: Spoofed agent identities, fake agents joining multi-agent systems
- **ASI04 (Agentic Supply Chain Vulnerabilities)**: Malicious MCP servers or tool providers impersonating legitimate ones
- Fake tool responses injected into agent context

## TAMPERING (Traditional + Agentic)
- Traditional: Data modification, code injection
- **ASI06 (Memory and Context Poisoning)**: RAG poisoning, manipulated agent memory/state, cross-session contamination
- **ASI01 (Agent Goal Hijack)**: Prompt injection via poisoned documents, emails, or user inputs that alter agent objectives
- **ASI07**: Message tampering in inter-agent communication channels

## REPUDIATION (Traditional + Agentic)
- Traditional: Denial of actions, log manipulation
- **ASI09 (Human-Agent Trust Exploitation)**: Agent actions that circumvent audit trails by exploiting user over-trust
- Untraceable autonomous agent decisions due to insufficient logging
- Gaps in agent decision audit logs making forensics impossible

## INFORMATION DISCLOSURE (Traditional + Agentic)
- Traditional: Data leaks, unauthorized access
- **ASI06**: Context window leakage exposing sensitive data from previous sessions, cross-tenant data exposure
- **ASI01**: Prompt injection attacks leading to data exfiltration via crafted outputs
- Sensitive credentials or data exposed through agent tool call logs or persistent memory

## DENIAL OF SERVICE (Traditional + Agentic)
- Traditional: Resource exhaustion, service disruption
- **ASI08 (Cascading Failures)**: Error propagation across agent chains causing system-wide outages
- Agent loop attacks where malicious input causes infinite reasoning cycles
- Resource exhaustion through repeated expensive tool invocations or runaway code execution

## ELEVATION OF PRIVILEGE (Traditional + Agentic)
- Traditional: Privilege escalation, unauthorized access
- **ASI02 (Tool Misuse and Exploitation)**: Over-privileged tools executing destructive commands, unvalidated tool inputs
- **ASI03 (Identity and Privilege Abuse)**: Cached credential misuse, confused deputy attacks, cross-agent delegation abuse
- **ASI05 (Unexpected Code Execution)**: Unsafe eval/exec of generated code, shell injection, sandbox escape
- **ASI10 (Rogue Agents)**: Agents persisting beyond intended lifecycle, impersonating other agents or users

## Cross-layer threat analysis

For each threat, consider how it could cascade across system boundaries:
- How could a data poisoning attack (in retrieval/RAG) affect agent decision-making and tool usage?
- How could a compromised tool provider affect the foundation model's outputs or agent memory?
- How could an agent ecosystem attack (multi-agent manipulation) lead to infrastructure compromise?
- How could observability gaps hide attack progression across components?

**Include at least 2-3 threats that explicitly describe cross-component attack chains** where applicable. Example chains:
- RAG poisoning → Agent goal hijack → Tool misuse → Data exfiltration
- Tool provider compromise → Memory poisoning → Future session hijacking
- Multi-agent manipulation → Cascading failures → Observability blind spot → Persistent access

## Critical Agentic risks to evaluate

1. **ASI01 – Agent Goal Hijack**: How could adversarial inputs (documents, emails, web content) redirect the agent's objectives?
2. **ASI02 – Tool Misuse**: Are tools properly scoped with least privilege? Can the agent execute dangerous commands?
3. **ASI03 – Identity/Privilege Abuse**: Can the agent abuse delegated permissions or cached credentials?
4. **ASI04 – Supply Chain**: Are external tool providers (MCP servers, plugins) trusted, verified, and integrity-checked?
5. **ASI05 – Code Execution**: Does the agent have code execution capabilities? Are they properly sandboxed?
6. **ASI06 – Memory Poisoning**: Can persistent memory be poisoned to affect future sessions or other users?
7. **ASI07 – Inter-Agent Communication**: In multi-agent systems, can agents be spoofed or messages tampered with?
8. **ASI08 – Cascading Failures**: How do errors propagate through agent chains? Are there circuit breakers?
9. **ASI09 – Human-Agent Trust**: Can the agent exploit user over-trust to perform harmful actions without scrutiny?
10. **ASI10 – Rogue Agents**: Can the agent persist beyond its intended lifecycle, impersonate users, or resist shutdown?

## Improvement suggestions for Agentic apps

When suggesting what additional info would sharpen a future iteration, specifically look for:

- Agent framework/orchestration details (e.g., LangChain, CrewAI, AutoGen, custom) if not specified
- RAG pipeline architecture if retrieval is mentioned but vector store details are missing
- Sandbox/isolation mechanisms if code execution is mentioned
- Inter-agent communication protocols if multi-agent is mentioned but coordination method is unclear
- Memory persistence mechanisms if long-running agents are implied
- Tool validation and output sanitization approaches
- Circuit breaker and rate limiting implementations for agent loops
- Audit logging coverage for agent decisions and tool invocations
- Human escalation paths and approval workflows
