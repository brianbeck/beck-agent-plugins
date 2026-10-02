# Mitigation Suggestions

Produce mitigations only for threats already identified in the threat model. Tailor each mitigation to the *specific* scenario — not generic boilerplate. Output as a markdown table (see `reference/output-formats.md`).

## For LLM / Generative AI threats

Draw from these mitigation categories (OWASP Top 10 for LLM Applications 2025):

### 1. Prompt Injection Defense
- Input validation and sanitization for all user inputs
- Prompt/response filtering to detect injection attempts
- Separate system instructions from user content with clear delimiters
- Instruction hierarchy or privileged prompts

### 2. Sensitive Data Protection
- PII detection and redaction in LLM inputs and outputs
- Differential privacy techniques for fine-tuning
- Sanitize training data to remove sensitive information
- Output filtering to prevent data leakage

### 3. Supply Chain Security
- Verify model integrity with checksums and signatures
- Use models only from trusted sources with security audits
- Vulnerability scanning for ML dependencies
- Maintain an ML bill of materials (ML-BOM)

### 4. Data & Model Integrity
- Validate and sanitize all data used for RAG and fine-tuning
- Provenance tracking for training data
- Anomaly detection to identify poisoned data
- Regularly evaluate model outputs for drift or degradation

### 5. Output Validation
- Never trust LLM output — validate and sanitize before use
- Output encoding appropriate to the context (HTML, SQL, etc.)
- Allowlists for permitted actions/commands
- Content security policies for generated content

### 6. Access Control & Rate Limiting
- Per-user and per-session rate limits
- Token/cost budgets to prevent unbounded consumption
- Tiered access based on user trust level
- Monitor for anomalous usage patterns

### 7. System Prompt Protection
- Avoid storing secrets or sensitive logic in system prompts
- Prompt leakage detection
- Indirect references for sensitive configuration
- Regularly audit prompts for information exposure risks

### 8. RAG & Embedding Security
- Access controls on vector databases
- Validate retrieved content before injection into prompts
- Tenant isolation for multi-tenant RAG systems
- Monitor for embedding manipulation attempts

## For Agentic AI threats

Apply these additionally for agentic apps (OWASP Top 10 for Agentic Applications):

### 1. Input Validation & Prompt Security
- Sanitize all inputs before processing by the agent
- Prompt injection detection and filtering (direct and indirect)
- Validate and sanitize content from external sources (documents, emails, web pages)
- Content security scanning for ingested documents
- Input source verification and provenance tracking

### 2. Least Privilege & Access Control
- Minimize tool/function permissions to only what's necessary for each task
- Scoped, short-lived credentials instead of long-lived tokens
- Per-action authorization checks
- Different permission levels based on input source trust
- Capability-based security for tool access

### 3. Sandboxing & Isolation
- Execute generated code in isolated containers with resource limits (CPU, memory, time)
- Restrict file system access to specific directories with read/write controls
- Limit network access to allowlisted destinations only
- Process isolation for multi-tenant environments
- seccomp/AppArmor profiles to restrict system calls

### 4. Audit Logging & Observability
- Log all agent actions, decisions, and tool invocations with timestamps
- Immutable audit trails for forensic analysis
- Monitor for anomalous agent behavior patterns
- Track agent decision chains for explainability
- Real-time alerting for high-risk actions

### 5. Human Oversight & Approval Workflows
- Require human approval for high-risk actions (financial, destructive, external communication)
- Confirmation prompts for irreversible operations
- Clear visibility into agent decision-making rationale
- Human-in-the-loop review for sensitive data access
- Enable action rollback where possible

### 6. Memory & Context Protection
- Encrypt persistent agent memory at rest and in transit
- Validate and sanitize retrieved context before use
- Memory segmentation between users/sessions/tenants
- Memory retention limits and automatic expiration
- Scan memory contents for sensitive data leakage

### 7. Agent Authentication & Integrity
- Cryptographic signing for inter-agent communication
- Verify tool provider authenticity (MCP server certificates, package signatures)
- Mutual TLS for agent-to-agent communication
- Agent identity attestation in multi-agent systems

### 8. Circuit Breakers & Resilience
- Rate limits on agent loops and reasoning cycles
- Maximum recursion depth for agent-to-agent calls
- Timeout enforcement for long-running operations
- Graceful degradation when downstream services fail
- Kill switches for runaway autonomous behavior

## Quality bar for mitigations

- Each mitigation must reference the specific threat it addresses, not generic principles.
- Prefer concrete controls ("rotate OAuth client secrets every 90 days via Vault") over abstract ones ("manage secrets carefully").
- For each high-risk threat (DREAD ≥ 7), suggest layered controls — preventive + detective + responsive.
- Call out implementation gotchas where relevant ("PII redaction must run before logging, not after").
