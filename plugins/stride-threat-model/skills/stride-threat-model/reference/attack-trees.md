# Attack Tree Generation

An attack tree enumerates the paths an adversary could take to compromise the application. The root is the attacker's high-level goal; children decompose into sub-goals and concrete techniques.

Generate an attack tree only after the threat model exists — the tree should reflect the threats already identified, expanded into multi-step paths.

## Output

Render as a Mermaid `graph TD` diagram inside a fenced ```mermaid code block. See `reference/output-formats.md` for the exact format.

## Always include these attack vectors

For **every** application, ensure the tree includes:

- Authentication bypass paths
- Data exfiltration paths
- Privilege escalation paths
- Availability / DoS paths

## For LLM / GenAI applications, add these attack vectors

1. **Prompt Injection Attacks**
   - Goal: Hijack model behavior or extract sensitive data
   - Path: Craft malicious user input → Bypass input filters → Override system instructions → Exfiltrate data or perform unauthorized actions
   - Alternative: Embed hidden instructions in documents/URLs → Model processes content → Instructions executed → Goal hijack

2. **Sensitive Data Extraction**
   - Goal: Extract training data, PII, or system prompts
   - Path: Probe model with targeted queries → Identify training data patterns → Extract sensitive information
   - Alternative: Use prompt injection to reveal system prompt → Extract API keys or credentials embedded in prompts

3. **RAG / Knowledge Base Poisoning**
   - Goal: Corrupt model responses through poisoned data
   - Path: Identify data ingestion points → Inject malicious content into knowledge base → Content gets embedded → Future queries return poisoned results
   - Alternative: Manipulate document metadata → Bias retrieval results → Influence model outputs

4. **Output Exploitation**
   - Goal: Abuse model outputs in downstream systems
   - Path: Trigger malicious code generation → Output used without sanitization → XSS/SQL injection/command injection in consuming application
   - Alternative: Generate convincing phishing content → Social engineering → Credential theft

5. **Model Supply Chain**
   - Goal: Compromise the model or its dependencies
   - Path: Target model repository/weights → Inject backdoors → Deployed model contains malicious behavior
   - Alternative: Compromise plugins/extensions → Malicious code executes in model context → Data theft or system compromise

6. **Resource Exhaustion**
   - Goal: Denial of service or cost amplification
   - Path: Craft complex prompts → Trigger expensive computations → Exhaust API quotas or compute budgets
   - Alternative: Recursive prompt loops → Model generates self-referencing content → Infinite token consumption

## For Agentic AI applications, add these attack vectors

Always include these in addition to the GenAI vectors above:

1. **Prompt Injection Chains**
   - Path: Malicious user input → Bypasses input validation → Alters agent instructions → Unauthorized actions
   - Alternative: Poisoned document → Agent processes content → Hidden instructions executed → Data exfiltration
   - Cross-layer: Poisoned RAG content → Affects foundation model output → Triggers tool misuse

2. **Tool Exploitation Paths**
   - Path: Discover available tools → Enumerate permissions → Craft malicious parameters → Execute destructive commands
   - Alternative: Chain multiple tools → Escalate through tool composition → Bypass individual tool restrictions
   - Cross-layer: Compromise tool provider → Return malicious data → Corrupt agent memory

3. **Credential & Identity Attacks**
   - Path: Identify cached credentials → Exploit confused deputy → Access resources beyond agent scope
   - Alternative: Impersonate user to agent → Agent acts on behalf of attacker → Legitimate-appearing malicious actions
   - Cross-layer: Extract credentials from memory → Use to access infrastructure directly

4. **Supply Chain Attacks**
   - Path: Target MCP server/plugin → Inject malicious tool responses → Agent trusts responses → Data theft or manipulation
   - Alternative: Poison model/prompt templates → Alter agent behavior at source
   - Cross-layer: Compromise framework dependency → Affects all agents using framework

5. **Cascading Failure Exploitation**
   - Path: Trigger error in one agent → Error propagates to dependent agents → System-wide failure
   - Alternative: Exploit error messages to leak information → Use leaked info for targeted attacks
   - Cross-layer: Infrastructure failure → Affects observability → Masks ongoing attack

6. **Human Trust Exploitation**
   - Path: Build user trust over time → Gradually expand action scope → User becomes complacent → Execute unauthorized actions
   - Alternative: Present misleading summaries → User approves without scrutiny → Harmful actions proceed
   - Cross-layer: Manipulate logs/observability → Hide true actions from human reviewers

## Pattern-specific attack tree branches

When the matching architectural pattern is detected, add the corresponding branch (see `reference/architectural-patterns.md` for detection triggers):

### RAG / Retrieval Pattern
```
Root: Compromise via RAG Pipeline
├── Poison Knowledge Base
│   ├── Inject malicious documents during ingestion
│   ├── Manipulate document metadata to bias retrieval
│   └── Cross-tenant data injection (if multi-tenant)
├── Exploit Embedding Weaknesses
│   ├── Craft adversarial content that clusters with target queries
│   └── Extract embeddings to reverse-engineer sensitive documents
└── Stale Index Exploitation
    ├── Exploit outdated cached content
    └── Race condition during index updates
```

### Multi-Agent Pattern
```
Root: Compromise Agent Ecosystem
├── Agent Impersonation
│   ├── Spoof agent identity credentials
│   ├── Replay captured agent messages
│   └── Register malicious agent with trusted identity
├── Inter-Agent Message Attacks
│   ├── Tamper with message content in transit
│   ├── Inject malicious messages into communication channel
│   └── Exploit message ordering/timing vulnerabilities
└── Cascading Compromise
    ├── Compromise one agent → use trust to attack others
    ├── Exploit shared memory/state between agents
    └── Trigger emergent malicious behavior through agent interactions
```

### Code Execution Pattern
```
Root: Escape Execution Sandbox
├── Container/Sandbox Breakout
│   ├── Exploit kernel vulnerabilities from within container
│   ├── Abuse mounted volumes or network access
│   └── Resource exhaustion to destabilize host
├── Malicious Code Generation
│   ├── Prompt injection to generate backdoored code
│   ├── Dependency confusion via generated package installs
│   └── Obfuscated payload in generated code
└── Execution Environment Manipulation
    ├── Modify environment variables
    ├── Hijack imported modules
    └── Persist malicious code across sessions
```

### Tool / MCP Pattern
```
Root: Compromise Tool Ecosystem
├── Malicious Tool Provider
│   ├── Compromise MCP server to return poisoned responses
│   ├── Impersonate legitimate tool provider
│   └── Supply chain attack on tool package
├── Tool Abuse Chains
│   ├── Chain multiple tools to bypass individual restrictions
│   ├── Use read tool to discover secrets → use write tool to exfiltrate
│   └── Exploit tool parameter injection
└── Confused Deputy via Tools
    ├── Trick agent into misusing legitimate tools
    ├── Exploit implicit trust in tool responses
    └── Manipulate tool selection logic
```

### Persistent Memory Pattern
```
Root: Exploit Agent Memory
├── Memory Poisoning
│   ├── Inject malicious content into long-term memory
│   ├── Corrupt memory to alter agent personality/goals
│   └── Cross-session attack via persistent context
├── Memory Extraction
│   ├── Query agent to reveal stored memories
│   ├── Extract other users' context from shared memory
│   └── Side-channel memory inference
└── Memory Manipulation
    ├── Delete safety-related memories
    ├── Insert false memories to establish trust
    └── Manipulate memory timestamps/ordering
```

### Autonomous Operations Pattern
```
Root: Exploit Autonomous Operations
├── Human Oversight Bypass
│   ├── Gradually normalize risky actions to reduce scrutiny
│   ├── Present misleading action summaries
│   └── Time attacks during low-monitoring periods
├── Rogue Agent Persistence
│   ├── Establish persistence beyond intended lifecycle
│   ├── Create hidden scheduled tasks
│   └── Resist shutdown commands
└── Autonomous Loop Attacks
    ├── Trigger infinite reasoning cycles
    ├── Exhaust resources through recursive operations
    └── Create self-reinforcing malicious behaviors
```

## Cross-component attack chains (Agentic only)

Include at least 2 attack paths that span multiple architectural components, showing how compromise in one area enables attacks on others. Examples to draw from:

- RAG poisoning → Agent goal hijack → Tool misuse → Data exfiltration
- Tool provider compromise → Memory poisoning → Future session hijacking
- Multi-agent manipulation → Cascading failures → Observability blind spot → Persistent access
