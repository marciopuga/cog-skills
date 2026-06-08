# Cog Skills

Agent skills for the [Cog](https://github.com/marciopuga/cog) plain-text memory system. Distributed as [SKILL.md](https://agentskills.io/specification) files via [skills.sh](https://skills.sh).

## Skills

| Skill | What it does |
|-------|-------------|
| **cog-memory** | Core conventions (L0 headers, three tiers, SSOT, temporal validity) |
| **cog-setup** | Interactive domain bootstrap |
| **cog-reflect** | Condition pipeline — 3-gate observation → pattern promotion |
| **cog-housekeeping** | Archive, prune, deterministic indexes, temporal sweep |
| **cog-evolve** | Audit architecture, auto-route threshold breaches |
| **cog-foresight** | Cross-domain strategic nudge |

## Quick Start

```bash
git clone https://github.com/marciopuga/cog ~/cog
cd ~/cog
npx skills add marciopuga/cog-skills
```

Start your agent and run `/setup` to bootstrap your domains. Works with any supported agent — `npx skills add` auto-detects the agent and installs skills into its native format.

**One folder, many projects.** `~/cog` is your agent's single brain. Don't scaffold memory inside each project — that fragments your context. One place where everything connects.

## Supported Agents

- Claude Code
- Codex (OpenAI)
- Cursor
- Windsurf
- Gemini CLI
- GitHub Copilot
- Opencode
- Cowork (Claude Desktop)

## Optional: Automated Maintenance

Schedule pipeline skills with cron. **Run housekeeping → reflect in the same session** so reflect sees freshly-pruned state:

```bash
# Weekly maintenance pulse (consolidated)
0 23 * * 0  cd ~/cog && claude -p "/housekeeping then /reflect"

# Monthly architecture audit
0  1 1 * *  cd ~/cog && claude -p "/evolve"
```

**Anti-pattern:** Running all skills every night. Weekly maintenance + monthly audit is enough.

For IDE agents (Cursor, Windsurf, Cowork), invoke skills manually when things feel stale.

## Docs

[lab.puga.com.br/cog](https://lab.puga.com.br/cog/)

## License

MIT
