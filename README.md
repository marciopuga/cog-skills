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
npx skills add marciopuga/cog-skills --skill cog-evolve
```

Schedule with cron (using any CLI agent's headless mode). **Run housekeeping → reflect in the same session** so reflect sees freshly-pruned state:

```bash
# Claude Code — weekly maintenance pulse (consolidated)
0 23 * * 0  claude -p "/cog-housekeeping then /cog-reflect"

# Monthly architecture audit
0  1 1 * *  claude -p "/cog-evolve"

# Codex — weekly maintenance pulse
0 23 * * 0  codex exec "/cog-housekeeping then /cog-reflect"
```

**Anti-pattern:** Running all skills every night. This generates reports nobody reads and logs the same issues repeatedly. Weekly maintenance + monthly audit is enough.

For IDE agents (Cursor, Windsurf, Cowork), invoke skills manually when things feel stale.

## Docs

[lab.puga.com.br/cog](https://lab.puga.com.br/cog/)

## License

MIT
