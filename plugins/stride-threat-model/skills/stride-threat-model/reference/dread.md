# DREAD Risk Assessment

DREAD scores each threat from the model on five dimensions (1-10) and computes a risk score as the mean.

- **1-3**: Low
- **4-6**: Medium
- **7-10**: High

## The five dimensions

- **Damage Potential** — How bad is the impact if exploited?
- **Reproducibility** — How reliably can the attack be executed?
- **Exploitability** — How much effort, skill, or access is needed?
- **Affected Users** — What proportion of users / scope is hit?
- **Discoverability** — How easily can an attacker discover the vulnerability?

**Risk Score** = (D + R + E + A + Di) / 5, rounded to 2 decimal places.

## Output format

See `reference/output-formats.md` for the markdown table format.

## Scoring considerations for LLM / GenAI applications

### Damage Potential
- Prompt injection can lead to complete system compromise or data exfiltration
- Training data poisoning affects all users and persists across model updates
- Sensitive data disclosure may include PII, credentials, or proprietary information
- Malicious output generation can cause reputational damage and legal liability

### Reproducibility
- Prompt injection success varies with model, temperature, and context
- Some attacks require specific phrasing or multi-turn conversations
- RAG poisoning may need time for embeddings to propagate
- Model behavior can be non-deterministic, making exact reproduction difficult

### Exploitability
- Prompt injection requires minimal technical skill
- Supply chain attacks on models/plugins require access to trusted sources
- Training data poisoning may need access to fine-tuning pipelines
- Some attacks are as simple as crafting malicious user input

### Affected Users
- Model-level vulnerabilities affect all users of that model instance
- RAG/knowledge base poisoning impacts everyone querying that data
- Per-user context isolation failures can expose cross-user data
- Shared prompt templates multiply the blast radius

### Discoverability
- Basic prompt injection is easily discoverable through experimentation
- System prompt extraction is often trivial with creative prompting
- Training data can sometimes be extracted through careful probing
- Model capabilities and limitations are often documented or enumerable

## Scoring considerations for Agentic AI applications

### Damage Potential
- Consider cascading effects across agent chains and downstream systems
- Memory/context poisoning can affect all future sessions and users
- Credential exfiltration may grant persistent access to multiple systems
- Autonomous actions may cause irreversible damage before detection
- Multi-agent compromises can have exponential blast radius
- Infrastructure-level attacks (sandbox escape, container breakout) enable lateral movement

### Reproducibility
- Agent behaviors may be probabilistic and context-dependent
- Prompt injection success depends on specific input formatting and model state
- Memory poisoning effects may manifest inconsistently across sessions
- Multi-step attacks through agent chains may be harder to reproduce exactly
- RAG-based attacks depend on embedding similarity and retrieval ranking
- Tool exploitation may require specific parameter combinations

### Exploitability
- Prompt injection attacks require low technical skill but specific knowledge of agent behavior
- Tool misuse often exploits configuration gaps rather than code vulnerabilities
- Supply chain attacks (malicious MCP servers) require access to trusted repositories
- Context poisoning may require patience but little technical sophistication
- Code execution exploits vary: simple prompt tricks vs. advanced sandbox escapes
- Multi-agent attacks may require understanding of agent communication protocols

### Affected Users
- Multi-agent systems can amplify impact across all connected agents and their users
- Shared memory/RAG databases affect all users of that knowledge base
- Credential abuse may affect all systems accessible with those credentials
- Agent-to-agent communication attacks can cascade across entire agent ecosystems
- Foundation model compromises affect every application using that model
- Infrastructure attacks may impact all tenants in shared environments

### Discoverability
- Prompt injection vulnerabilities are easily discoverable through normal interaction
- Agent decision logs may reveal exploitable patterns and tool permissions
- Tool permissions and capabilities are often documented or easily enumerated
- Memory contents may leak through carefully crafted queries
- Framework vulnerabilities are often published in CVE databases
- Agent behavior patterns can be profiled through systematic probing

## Pattern-specific score adjustments (Agentic)

When the matching architectural pattern is present, nudge scores accordingly:

### RAG / Retrieval Systems
- Damage Potential: **+1-2** if RAG serves multiple users (poisoning affects everyone)
- Reproducibility: **-1** if retrieval is non-deterministic (harder to reproduce exactly)
- Affected Users: **+2-3** if shared knowledge base across users/tenants
- Discoverability: **+1** if document upload is user-accessible (easy to test poisoning)

### Multi-Agent Systems
- Damage Potential: **+2-3** for cascading/amplification effects across agent chains
- Reproducibility: **-1 to -2** for complex multi-agent interactions
- Affected Users: **+2** for ecosystem-wide attacks affecting all connected agents
- Discoverability: **+1** if agent communication patterns are observable

### Code Execution Environments
- Damage Potential: **+3** if sandbox escape is possible (full system compromise)
- Exploitability: variable based on sandbox strength (**-2** for strong isolation, **+2** for weak)
- Affected Users: **+2-3** if shared infrastructure (affects co-tenants)
- Discoverability: **-1** for sophisticated sandbox escapes (requires expertise)

### Tool / MCP Ecosystems
- Damage Potential: **+1-2** based on tool permissions (higher for write/execute tools)
- Reproducibility: **+1** if tool behavior is deterministic
- Exploitability: **+2** if tools accept unvalidated parameters
- Discoverability: **+2** if tool list and capabilities are enumerable

### Persistent Memory Systems
- Damage Potential: **+2** if memory affects future sessions (persistent compromise)
- Reproducibility: **-1** if memory retrieval is similarity-based (less predictable)
- Affected Users: **+2-3** if memory is shared across users
- Discoverability: **+1** if memory contents can be queried directly

### Autonomous Operations
- Damage Potential: **+2-3** if actions are irreversible before human review
- Exploitability: **+1** if human oversight is minimal or can be bypassed
- Discoverability: **-1** if attack occurs during low-monitoring periods

## Cross-layer threat scoring

For threats that span multiple architectural components, consider cumulative risk:
- If a threat enables follow-on attacks, score Damage Potential for the full attack chain.
- If exploitation requires multiple steps across components, adjust Reproducibility accordingly.
- If compromise propagates to shared infrastructure, increase Affected Users significantly.
