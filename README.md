# Cog Skills

Agent skills for the [Cog](https://github.com/marciopuga/cog) plain-text memory system. Install individually via [skills.sh](https://skills.sh).

## Skills

| Skill | What it does | Install |
|-------|-------------|---------|
| **cog-memory** | Core memory conventions (L0 headers, three tiers, SSOT) | `npx skills add marciopuga/cog-skills --skill cog-memory` |
| **cog-setup** | Interactive domain bootstrap | `npx skills add marciopuga/cog-skills --skill cog-setup` |
| **cog-reflect** | Mine interactions, consolidate patterns | `npx skills add marciopuga/cog-skills --skill cog-reflect` |
| **cog-housekeeping** | Archive, prune, rebuild indexes | `npx skills add marciopuga/cog-skills --skill cog-housekeeping` |
| **cog-evolve** | Audit architecture, propose improvements | `npx skills add marciopuga/cog-skills --skill cog-evolve` |
| **cog-foresight** | Cross-domain strategic nudge | `npx skills add marciopuga/cog-skills --skill cog-foresight` |

## Quick Start

```bash
# 1. Clone the memory folder
git clone https://github.com/marciopuga/cog ~/cog

# 2. Install the core memory skill (works in any agent)
npx skills add marciopuga/cog-skills --skill cog-memory

# 3. Bootstrap your domains
npx skills add marciopuga/cog-skills --skill cog-setup
```

## Supported Agents

These skills use the [SKILL.md](https://agentskills.io/specification) standard and work with:

- Claude Code
- Codex (OpenAI)
- Cursor
- Windsurf
- Gemini CLI
- GitHub Copilot
- Opencode
- Cowork (Claude Desktop)

## Optional: Automated Maintenance

Install pipeline skills and run them on a schedule:

```bash
npx skills add marciopuga/cog-skills --skill cog-reflect
npx skills add marciopuga/cog-skills --skill cog-housekeeping
```

Schedule with cron (using any CLI agent's headless mode):

```bash
# Claude Code
0 23 * * 0  claude -p "/cog-housekeeping"
0  0 * * 0  claude -p "/cog-reflect"

# Codex
0 23 * * 0  codex exec "/cog-housekeeping"
0  0 * * 0  codex exec "/cog-reflect"
```

For IDE agents (Cursor, Windsurf, Cowork), invoke skills manually when things feel stale.

## Docs

[cog.puga.com.br](https://cog.puga.com.br)

## License

MIT
