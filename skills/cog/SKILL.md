---
name: cog
description: >
  Plain-text persistent memory system for AI agents. Conventions for L0 progressive
  loading, three-tier storage (hot/warm/glacier), single-source-of-truth, temporal
  validity, and wiki-links. Run /cog to bootstrap or reconfigure domains.
---

# Cog

A plain-text memory system that gives any AI agent persistent memory across sessions. Memory lives in a local folder as markdown files — observable, editable, git-trackable.

**One folder, many projects.** All reads and writes target a single global memory path. Don't create separate memory in each project — that fragments your context. One brain, used everywhere.

## Memory Path

Resolved in this order:

1. `$COG_HOME/memory/` — if the `COG_HOME` environment variable is set
2. `~/cog/memory/` — default fallback

If the resolved path doesn't exist, run `/cog` to bootstrap it.

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

---

# Setup

Run `/cog` to bootstrap or reconfigure. This section only executes when the skill is invoked — not during normal conversation.

## Phase 0: Verify Environment

1. **Resolve path** — check `$COG_HOME`. If set, memory root is `$COG_HOME/memory/`. If unset, default to `~/cog/memory/`.
2. **Check existence** — does the resolved directory exist?
   - **Yes** → skip to Phase 1 (or ask "Want to add more domains?")
   - **No** → create it: `mkdir -p $resolved_path/memory`
3. **Set COG_HOME if non-default** — if the path is not `~/cog`, tell the user to add `export COG_HOME=/their/path` to their shell profile. Offer to do it for them.

## Phase 1: Discovery (Conversational)

Have a natural conversation to understand the user's domains. Ask about:

1. **Work** — "What do you do for work? Company name, role?" → becomes a `work` domain
2. **Side projects** — "Any side projects or ventures?" → each becomes a `side-project` domain
3. **Personal** — The `personal` domain is always created. Ask: "Anything specific you want to track? Health, hobbies, habits, kids?"
4. **Anything else** — "Any other areas you want persistent memory for?"

Keep it natural. 3-4 questions max. Use their answers to build the manifest.

### Domain Types

| Type | Meaning | Files |
|------|---------|-------|
| `personal` | Personal life (always one) | hot-memory, action-items, entities, observations, habits, health, calendar |
| `work` | Day job | hot-memory, action-items, entities, projects, observations |
| `side-project` | Ventures, hobbies | hot-memory, action-items, projects, observations |
| `system` | Cog internals (auto-created) | self-observations, patterns, improvements |

## Phase 2: Confirm

Before writing, show the user a summary:

```
Here's what I'll set up:

Domains:
- personal — Family, health, day-to-day
- acme — Work at Acme Corp (Designer)
- myapp — Side project

This will create:
- memory/domains.yml (domain manifest)
- Memory directories + starter files for each domain

Good to go?
```

Wait for confirmation.

## Phase 3: Generate

### 3a. Write `memory/domains.yml`

Always include `cog-meta` as a system domain automatically.

```yaml
# Cog Domain Manifest — generated by /cog
# Single source of truth for all memory domains.
# To modify: run /cog again.

domains:
  - id: personal
    path: personal
    type: personal
    label: "<from conversation>"
    triggers: [<inferred keywords>]
    files: [hot-memory, action-items, entities, observations, habits, health, calendar]

  - id: cog-meta
    path: cog-meta
    type: system
    label: "Cog self-knowledge and patterns"
    triggers: [cog, meta, memory system, patterns]
    files: [self-observations, patterns, improvements]
```

### 3b. Create Directories and Starter Files

For each domain, create `memory/{path}/` and starter files:

**hot-memory.md:**
```markdown
<!-- L0: Current state and top-of-mind for {label} -->
# {Label} — Hot Memory

<!-- Rewrite freely. Keep under 50 lines. -->
```

**observations.md:**
```markdown
<!-- L0: Timestamped observations and events -->
# {Label} — Observations

<!-- Append-only. Format: - YYYY-MM-DD [tags]: observation -->
```

**action-items.md:**
```markdown
<!-- L0: Open and completed tasks -->
# {Label} — Action Items

## Open

## Completed
```

**entities.md:**
```markdown
<!-- L0: People, places, and things -->
# {Label} — Entities

<!-- 3-line max per entry. Format: ### Name (relationship) / facts / status|last -->
```

**Other files** (calendar, health, habits, projects, etc.):
```markdown
<!-- L0: {file name} for {label} -->
# {Label} — {File Name}
```

### 3c. Create Cross-Domain Files

If they don't exist:
- `memory/hot-memory.md` — cross-domain strategic context
- `memory/link-index.md` — backlink index (auto-generated)
- `memory/glacier/index.md` — glacier catalog

## Phase 4: Summary

Output:
- Domains created
- Files generated
- Next steps: "Just talk naturally. Your memory system is ready."

## Setup Rules

1. **Never delete** — setup only creates and updates
2. **Idempotent** — running again is safe, skips existing files
3. **cog-meta is automatic** — always included, never ask about it
4. **Conversational first** — no one edits YAML manually
5. **Re-runs are additive** — "Want to add more domains or reconfigure?"
