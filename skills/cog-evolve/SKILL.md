---
name: cog-evolve
description: >
  Audit memory architecture, review rule effectiveness, measure metrics,
  and propose system improvements. Run monthly to keep the system healthy.
  Invoke with /cog-evolve.
---

# Cog Evolve

Systems-level self-improvement. The architect.

**This is NOT /cog-reflect.** Reflect = "what did I learn?" Evolve = "are the rules working?" Evolve never touches memory content — it changes the rules that govern how content moves.

## Memory Path

All files under `~/cog/memory/`.

## Minimum Data Check

Before auditing, verify the system has enough history:
- If reflect has never run (no self-observations, no patterns): stop. Say "Nothing to audit yet. Run /cog-reflect a few times first to build patterns, then evolve can assess whether they're working."
- If patterns.md has < 3 entries: say "Too few patterns to evaluate effectiveness. Let the system run for a few more cycles."

Evolve audits rules — there need to be rules to audit.

## Files to Read

Continuity (read first):
- `memory/cog-meta/self-observations.md` (what's been noticed)
- `memory/cog-meta/patterns.md` (current rules)

Measure (don't edit content):
- `memory/hot-memory.md`
- Any domain satellite pattern files

## Process

### 1. Architecture Review

Evaluate structural design:
- **Tier design** — are hot/warm/glacier boundaries well-defined?
- **Consolidation pipeline** — is the flow working? Where does it stall?
- **File organization** — any files in wrong domains? Orphaned files?
- **Skill boundaries** — are housekeeping/reflect/evolve lanes clean?

### 2. Process Effectiveness Audit

Review output of recent housekeeping and reflect runs:

**Housekeeping check:**
- Did pruning priority order work?
- Are glacier thresholds (50 obs, 10 items) right?
- Is the 50-line hot-memory cap appropriate?

**Reflect check:**
- Did consolidation produce useful patterns or noise?
- Did thread detection work?
- Is reflect staying in its lane?

**Scorecard metrics:**
- Core `patterns.md`: line count / 70 (target: ≤1.0)
- Satellite pattern files: list each with line count (cap: 30)
- Entity compression ratio: total entity lines / total entries (target: ≤3.0)
- Hot-memory line counts vs 50-line cap

### 3. Rule Change Proposals

Based on findings, propose concrete rule changes:
- What problem does it solve?
- What evidence supports it?
- What's the risk?
- Rule change (apply directly) vs architecture change (propose for review)?

**Apply low-risk changes directly.** Propose architecture changes for user review.

### 4. Route Content Issues

When you spot content problems, route them:

```
→ housekeeping: entities.md at 290 lines, needs glacier pass
→ reflect: hot-memory missing link for X
→ reflect: patterns.md has stale data
```

If the same issue keeps appearing → that's a rule problem. Propose a fix.

### 5. Write Observations

Append to `memory/cog-meta/self-observations.md`:
- Format: `- YYYY-MM-DD [tag]: observation`
- Tags: bloat, staleness, redundancy, gap, architecture, opportunity, rule-drift

### 6. Debrief

Concise summary:
- *Process health* — did housekeeping/reflect follow their rules?
- *Rule changes* — applied or proposed
- *Routed issues* — content problems sent elsewhere
- *Architecture notes* — structural observations
- *Next priorities* — top 3 architecture items

Numbers over narrative.
