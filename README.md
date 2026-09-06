# Cog Skills

Agent skills for [Cog](https://github.com/marciopuga/cog) — one memory, not one per tool. A structured, plain-text memory layer shared across all your AI agents and projects. Distributed as [SKILL.md](https://agentskills.io/specification) files via [skills.sh](https://skills.sh).

## Skills

| Skill | What it does |
|-------|-------------|
| **cog** | Core memory system — conventions, setup, and domain bootstrap (required) |
| **cog-reflect** | Condition pipeline — 3-gate observation → pattern promotion, scenario retrospectives |
| **cog-housekeeping** | Archive, prune, deterministic indexes, temporal sweep |
| **cog-foresight** | Cross-domain strategic nudge, flags scenario candidates |
| **cog-history** | Deep memory search — piece together a narrative across files |
| **cog-scenario** | Decision simulation — branch a decision into 2-3 modeled paths |

## Quick Start

```bash
git clone https://github.com/marciopuga/cog ~/cog
cd ~/cog
npx skills add marciopuga/cog-skills
```

Start your agent and run `/cog` to bootstrap your domains. Works with any supported agent — `npx skills add` auto-detects the agent and installs skills into its native format.

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
0 23 * * 0  cd "${COG_HOME:-$HOME/cog}" && claude -p "/cog-housekeeping then /cog-reflect"
```

(In the [cog repo](https://github.com/marciopuga/cog) itself, the vendored skills use unprefixed names: `/housekeeping then /reflect`.)

**Anti-pattern:** Scheduling every skill. One weekly pulse is enough — foresight, scenario, and history run when you ask.

For IDE agents (Cursor, Windsurf, Cowork), invoke skills manually when things feel stale.

## Docs

[lab.puga.com.br/cog](https://lab.puga.com.br/cog/)

## License

MIT
