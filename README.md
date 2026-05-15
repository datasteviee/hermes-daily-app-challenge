# Hermes Daily App Challenge — Prompt Archive

> This repository contains the **full agent prompts** and **skill snapshots** for the autonomous daily app research cronjobs.  
> **Current version:** Vault-based (since 2026-05-16). Previously: GitHub-repo-based (legacy).

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
│   ├── daily-app-challenge-vault-prompt.md            # Current iOS prompt (vault-based)
│   ├── daily-google-play-research-vault-prompt.md     # Current Android prompt (vault-based)
│   ├── daily-app-challenge-prompt.md                  # Legacy prompt (GitHub-repo-based)
│   └── daily-google-play-research-prompt.md           # Legacy prompt (GitHub-repo-based)
└── skills/
    ├── indie-app-opportunity-research/
    │   └── SKILL.md                                   # Full skill snapshot
    └── competitor-app-profiling/
        └── SKILL.md                                   # Full skill snapshot
```

---

## Migration History

### 2026-05-16: GitHub → Vault Migration

**Problem:** Scattered `app-challenge-YYYY-MM-DD` repos under `datasteviee/` created repo sprawl, no discoverability, no unified search.

**Solution:** Centralize all research in the Obsidian Vault at `/root/vault/Brain/Projects/AppChallenges/`.

**Changes:**
- Both cronjobs now use `workdir: /root/vault`
- Output: Markdown PRDs in `YYYY-W##/` folders (not GitHub repos)
- Git workflow: atomic `pull --rebase` → write → commit → push to `steviee/vault.git`
- README.md index auto-updated per run
- Legacy prompts archived in `prompts/*-prompt.md`

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

## License

MIT — These prompts are public reference material. The actual cronjobs run on a private Hermes Agent instance.
