# beck-agent-plugins

Plugins containing [Agent Skills](https://agentskills.io) that work in both Claude Code and OpenAI Codex.
Each plugin lives under `plugins/<name>/` and is listed in both marketplace files at the repo root.

## Install

Claude Code:

    /plugin marketplace add brianbeck/beck-agent-plugins
    /plugin install stride-threat-model@beck-agent-plugins

Codex:

    codex plugin marketplace add brianbeck/beck-agent-plugins

then install from the Plugins directory.

## Plugins

- `stride-threat-model` — STRIDE threat models, attack trees, DREAD scoring, mitigations, Gherkin security tests.

## Adding a plugin

1. Create `plugins/<name>/` with `plugin.json`, `.claude-plugin/plugin.json`, `.codex-plugin/plugin.json`, and `skills/<skill>/SKILL.md`.
2. Add an entry to `.claude-plugin/marketplace.json` and `.agents/plugins/marketplace.json`.
3. Run `claude plugin validate ./plugins/<name> --strict`.
