# Hermes Daily App Challenge — Prompt Archive

> This repository contains the **full agent prompts** and **skill snapshots** for the autonomous daily app research cronjobs.
> **Current version:** Vault-based (since 2026-05-16). Previously: GitHub-repo-based.

---

## Architecture

| Job | Cronjob ID | Schedule | Target | Storage |
|-----|-----------|----------|--------|---------|
| **iOS Research** | `3393cbb19211` | `30 5 * * *` | App Store / iTunes | Obsidian Vault |
| **Android Research** | `b343f62dc98b` | `0 6 * * *` | Google Play Store | Obsidian Vault |

**Vault path:** `Brain/Projects/AppChallenges/YYYY-W##/`

---

## Files

```
.
├── README.md                                          # This file
├── project.yml                                        # xcodegen-style manifest
├── jobs/
│   └── jobs-meta.json                                 # Job metadata + IDs
├── prompts/
│   ├── daily-app-challenge-vault-prompt.md            # iOS prompt — vault-based
│   └── daily-google-play-research-vault-prompt.md     # Android prompt — vault-based
└── skills/
    ├── indie-app-opportunity-research/
    │   ├── SKILL.md                                   # Workflow + heuristics (~440 lines)
    │   └── references/
    │       ├── cron-safe-commands.md                  # Safe shell patterns for hardened cron
    │       ├── example-pitches.md                     # 10 validated Mini-PRD templates
    │       └── revenue-anchors.md                     # Verified MRR benchmarks
    └── competitor-app-profiling/
        └── SKILL.md                                   # Light-touch profiling + PrivateInvestigator escalation
```

---

## Workflow (per cron run)

| Phase | Action | Tooling |
|---|---|---|
| **0** | Vault sync (`git pull --rebase`) + dynamic dedup via `find Brain/Projects/AppChallenges/` | Bash |
| **1** | RespecASO keyword research (max 5/batch) | RespecASO v2.9 + fallback |
| **2** | iTunes Search API + revenue verification | curl + Python |
| **3** | Community deep dive | **`Research` skill** (Standard, 4 agents, URL-verified) → manual Reddit/Bluesky/Quora fallback |
| **4** | Competitive feature gap analysis | iTunes API + Play Store; escalate to **`PrivateInvestigator`** on red flags |
| **5** | Feasibility filter + adversarial stress-test | **`RedTeam`** (32 parallel agents) + **`Science`** (testable hypothesis) |
| **6** | Mini-PRD as Obsidian note (frontmatter + body) | Templates in `references/example-pitches.md` |
| **7** | Atomic vault write + commit + push | git |

---

## Migration History

### 2026-05-16 (later): Skill Diet + PAI Integration + Dedup Hardening

- **Dynamic dedup**: Vault-prompts read `find Brain/Projects/AppChallenges/` at runtime instead of carrying hardcoded "Bereits dokumentierte Apps" blacklist tables that drift.
- **Skill diet**: `indie-app-opportunity-research/SKILL.md` trimmed from 960 → ~440 lines. Examples + revenue anchors moved to `references/` to reduce LLM attention-drift on long cron runs.
- **PAI skill integration**: Phase 3 calls `Research` skill (Standard Mode, 4 verified agents) with manual fallback. Phase 5 calls `RedTeam` + `Science` before BUILD verdict.
- **`competitor-app-profiling` refactored**: Light-touch skill that escalates to `PrivateInvestigator` for deep entity research on suspicious competitors.
- **Legacy prompts deleted**: The `*-prompt.md` files were removed — `jobs-meta.json` points only at the vault variants, keeping the legacy files around invited drift.

### 2026-05-16 (earlier): GitHub → Vault Migration

**Problem:** Scattered `app-challenge-YYYY-MM-DD` repos under `datasteviee/` created repo sprawl, no discoverability, no unified search.

**Solution:** Centralize all research in the Obsidian Vault at `/root/vault/Brain/Projects/AppChallenges/`.

**Changes:**
- Both cronjobs now use `workdir: /root/vault`
- Output: Markdown PRDs in `YYYY-W##/` folders (not GitHub repos)
- Git workflow: atomic `pull --rebase` → write → commit → push to `steviee/vault.git`
- README.md index auto-updated per run

---

## Vault Conventions

- **Week folders:** `YYYY-W##/` (ISO calendar week)
- **iOS PRD:** `YYYY-MM-DD-{keyword-slug}.md`
- **Android PRD:** `YYYY-MM-DD-{keyword-slug}.android.md`
- **Frontmatter schema:** `date`, `keyword`, `platform`, `aso_pop`, `aso_diff`, `aso_opp`, `aso_insight`, `verdict`, `revenue_anchor`
- **Rotation categories:**
  - Day 1: Exotic Pet (skip built)
  - Day 2: AI-Speech/Writing
  - Day 3: Hobby/Enthusiast
  - Day 4: Family/Household
  - Day 5: Micro-Education
  - Day 6: Free choice
  - Day 7: Deep-dive top performer

---

## Hardened Environment Notes

- No `curl | python3` pipes (HIGH risk — tirith blocks)
- All `curl` → `/tmp/`, then Python from disk
- No `&` backgrounding in command strings
- No `grep -oP` on Play Store HTML (use `browser_snapshot`)

Full list: `skills/indie-app-opportunity-research/references/cron-safe-commands.md`.

---

## License

MIT — These prompts are public reference material. The actual cronjobs run on a private Hermes Agent instance.
