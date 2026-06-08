---
name: cog-memory
description: >
  Plain-text persistent memory system with L0 progressive loading,
  three-tier storage (hot/warm/glacier), and single-source-of-truth conventions.
  Activates when the agent needs to remember, recall, search, or organize
  persistent knowledge across sessions.
---

# Cog Memory

A plain-text memory system that gives any AI agent persistent memory across sessions. Memory lives in a local folder as markdown files — observable, editable, git-trackable.

## Memory Path

Memory path is resolved in this order:

1. `$COG_HOME/memory/` — if the `COG_HOME` environment variable is set
2. `~/cog/memory/` — default fallback

All reads and writes target the resolved path regardless of what project you're currently working in. This is a global path — one memory system shared across all your projects and conversations.

If the resolved path doesn't exist, run `/cog-setup` to bootstrap it.

**Tip:** If you want to access your Cog memory from any project without installing this skill everywhere, consider asking your agent to create a global slash command (e.g. `/cog`) that reads and writes to your memory path. How that works depends on your agent — ask it to set one up for you.

## Three Tiers

| Tier | Where | Loaded | Size limit | Edit mode |
|------|-------|--------|-----------|-----------|
| **Hot** | `*/hot-memory.md` | Every conversation | <50 lines | Rewrite freely |
| **Warm** | Domain files | When topic activates | Per-file caps | File-specific |
| **Glacier** | `memory/glacier/` | On-demand (indexed) | Unlimited | Read-only |

**Hot** = your desk. Current state, top priorities. Loaded every turn.
**Warm** = your filing cabinet. Domain-specific files loaded when relevant.
**Glacier** = deep archive. Old observations, completed items. Indexed, searchable, never auto-loaded.

## L0 Headers (Progressive Context Loading)

Every memory file has a one-line L0 summary as the first line — a quick answer to "what would I find if I read this file?"

**Format:**
```
<!-- L0: summary here (max 80 chars) -->
```

### L0 → L1 → L2 Retrieval Protocol

- **L0** — Read the `<!-- L0: ... -->` header. Answer: "is this file relevant?"
- **L1** — Scan section headers (`## ...`, `### ...`). Answer: "which section is relevant?"
- **L2** — Read the full file or section.

**Decision rules:**
1. When uncertain which files are relevant, scan L0 headers across the domain directory first
2. If L0 confirms relevance but the file is >80 lines, scan section headers (L1) before full read
3. For files <80 lines or when you need full context, go directly to L2
4. Hot-memory files are always L2 — they're small by design

## Directory Structure

Domains are defined in `memory/domains.yml` — the single source of truth for all memory domains.

```
memory/
  domains.yml                      # Domain manifest
  hot-memory.md                    # Cross-domain (loaded every turn)
  link-index.md                    # Backlink index (auto-generated)
  cog-meta/                        # System self-knowledge
    self-observations.md           # What worked/didn't — append-only
    patterns.md                    # Distilled interaction rules — edit in place
    improvements.md                # Ideas, wishlists — edit in place
  personal/                        # Default domain
    hot-memory.md
    observations.md
    action-items.md
    entities.md
    calendar.md
    health.md
    habits.md
  glacier/                         # Archived data by domain
    index.md                       # Glacier catalog (auto-generated)
```

## Memory Rules

1. **Read on start**: Always read `memory/hot-memory.md` and `memory/cog-meta/patterns.md`
2. **Write immediately**: Don't wait to save something worth remembering
3. **Observations are append-only**: `- YYYY-MM-DD [tags]: <observation>` — never edit past entries
4. **Action items**: `- [ ] task | due:YYYY-MM-DD | pri:high/med/low | added:YYYY-MM-DD`
5. **Entities**: 3-line compact registry. `### Name (relationship)` / pipe-separated facts / `status: active | last: YYYY-MM-DD`
6. **Hot memory <50 lines**: Prune aggressively, detail goes in observations
7. **Single Source of Truth (SSOT)**: Each fact in ONE canonical file. Others reference via `[[link]]`.
8. **Temporal validity**: Time-bounded facts SHOULD carry an expiry marker (see below).

## Temporal Validity Markers

Facts with a natural expiry (upcoming events, temporary states, countdowns) should carry an inline marker:

```markdown
- HydroTap repair — chiller removed, reinstall in 1-2 weeks <!-- until:2026-06-20 grace:5 -->
- Leflunomide 3-month review Thu 18 Jun <!-- until:2026-06-18 grace:14 -->
- New job started at Acme <!-- from:2026-03-01 -->
```

**Marker types:**
- `<!-- until:YYYY-MM-DD -->` — expires on this date. Housekeeping archives after expiry.
- `<!-- until:YYYY-MM-DD grace:N -->` — expires N days after the `until` date (buffer for follow-up).
- `<!-- from:YYYY-MM-DD -->` — stable since this date. Never expires, documents when something became true.

**Rules:**
- Stable facts (DOB, role, relationships) need no marker
- Only mark facts that will become irrelevant after a date
- Housekeeping sweeps expired markers → moves to glacier or deletes
- Use absolute dates, never computed counts ("since Jan 27" not "Day 42")
- Grace period = buffer for the fact to still matter after the event (e.g., a medical review result may take 2 weeks to act on)

## File Edit Patterns

| File | Edit mode |
|------|-----------|
| `hot-memory.md` | Rewrite freely |
| `observations.md` | Append only |
| `action-items.md` | Append new, check off done |
| `entities.md` | Edit in place (3-line max per entry) |
| `calendar.md` | Edit in place |
| `health.md` | Current State: rewrite / History: append |
| `habits.md` | Current State: rewrite / Patterns: append |
| Thread files | Current State: rewrite / Timeline: append |
| `cog-meta/patterns.md` | Edit in place (distill from observations) |
| `link-index.md` | Auto-generated — do not edit |
| `glacier/*` | Read-only |

## Wiki-Links

Cross-reference files using `[[domain/filename]]` or `[[domain/filename#Section]]`.

- Path relative to `memory/`, no `.md` extension
- **Write-time linking**: When editing ANY file, add `[[links]]` to related files
- **Write-time back-linking**: When adding A→B, check if B benefits from pointing back to A
- Follow links when the linked topic is relevant — don't chase every link mechanically

## SSOT (Single Source of Truth)

Each fact lives in exactly ONE canonical file:

- People → `entities.md`
- Tasks → `action-items.md`
- Health → `health.md`
- Events → `calendar.md`
- Current state → `hot-memory.md` (pointers only, not source facts)
- Raw events → `observations.md`

When the same fact appears in two files: keep it in the canonical file, replace the duplicate with a `[[link]]`.

## Threads (Zettelkasten Layer)

Threads are read-optimized synthesis files for topics that appear across 3+ observations over 2+ weeks. One file per topic, consistent spine:

1. **Current State** — what's true now (rewrite freely)
2. **Timeline** — dated entries, append-only, full detail preserved
3. **Insights** — learnings, patterns, what's different this time

**Rules:**
- One file forever — threads grow long, don't split
- Texture is the value — keep full detail, quotes, dates
- Fragments never move — threads reference them via wiki-links

## Memory Retrieval Protocol

When responding to any query:

1. **Identify domain** — match query to a domain
2. **L0 scan** — scan `<!-- L0:` headers across the domain to find relevant files
3. **Select by query type:**
   - Tasks → `action-items.md` + `calendar.md`
   - Person → `entities.md`
   - Overview → `hot-memory.md` + `action-items.md`
   - Cross-reference → check `link-index.md`
4. **L1 before L2** — for files >80 lines, scan headers first
5. **SSOT check on write** — before writing, verify fact doesn't already exist elsewhere

## Consolidation

Memory flows upward through the tiers:

```
observations (raw events, append-only)
    ↓ cluster 3+ on same theme
patterns (distilled rules, edit in place)
    ↓ most urgent/active
hot-memory (current state, rewrite freely)
```

Each layer up is smaller and more distilled. Nothing is deleted — abstracted and relocated.

## Glacier Archival

When files exceed limits, old data moves to `memory/glacier/{domain}/`:

- `observations.md` >50 entries → `glacier/{domain}/observations-{tag}.md`
- `action-items.md` >10 completed → `glacier/{domain}/action-items-done.md`
- Inactive entities (6+ months) → `glacier/{domain}/entities-inactive.md`

All glacier files have YAML frontmatter:
```yaml
---
type: observations
domain: personal
tags: [health, habits]
date_range: 2024-01 to 2024-06
entries: 47
summary: Health and habit observations from early 2024
---
```

## Domain Registry

`memory/domains.yml` is the single source of truth:

```yaml
domains:
  - id: personal
    path: personal
    type: personal
    label: "Family, health, calendar, day-to-day"
    triggers: [family, health, kids, calendar]
    files: [hot-memory, action-items, entities, observations, habits, health, calendar]
```

Domain types: `personal` (always one), `work`, `side-project`, `system` (cog-meta, auto-created).

## Patterns

Distilled rules from 3+ observations on the same theme. Timeless, actionable, no examples or dates.

- **Core** (`cog-meta/patterns.md`): universal rules, ≤70 lines. Loaded every turn.
- **Satellite** (`{domain}/patterns.md`): domain-specific, soft cap 30 lines. Loaded when domain activates.

## Scheduling: Consolidated Pulses

When automating memory maintenance (cron, scheduled tasks, or manual batch runs), **run skills in the same session** rather than as separate isolated invocations.

### Why

Separate runs re-read all context from scratch and can't see what the prior skill modified. Hand-off files between runs drift and add complexity. Running housekeeping → reflect in one session means reflect sees what housekeeping just cleaned — no handoff needed.

### Recommended Groupings

| Pulse | Skills (in order) | Cadence | Rationale |
|-------|-------------------|---------|-----------|
| **Maintenance** | housekeeping → reflect | Weekly | Reflect sees cleaned state; promotions land in freshly-pruned files |
| **Architecture** | evolve (standalone) | Monthly | Audits the rules that housekeeping/reflect follow |
| **Strategic** | foresight (standalone) | Weekly | Read-only scan, writes one nudge file |

### Anti-Pattern: Nightly Everything

Running all skills every night is theatrical — it generates reports nobody reads and logs the same issues repeatedly without resolving them. Better cadence:
- **Weekly**: housekeeping + reflect (consolidated)
- **Monthly**: evolve (audit + auto-route)
- **Weekly or on-demand**: foresight

### Hand-Off Principle

Within a consolidated pulse, phases share context naturally (same conversation). Between pulses (e.g., evolve reading reflect's output), the contract is through FILES — `patterns.md`, `action-items.md`, `self-observations.md`. No separate state files needed.
