# Hermes daily-app-challenge

Vollständiges Archiv des autonomen Hermes-Cronjobs `daily-app-challenge` und des sister-jobs `daily-google-play-research`.

## Was ist das?

Ein autonomer Daily-Research-Agent, der jeden Morgen eine in 2–3 Tagen umsetzbare App-Idee findet, validiert, dokumentiert und als Repo bei GitHub anlegt — ohne menschliche Interaktion.

## Struktur

```
.
├── README.md                               # Diese Datei
├── prompts/
│   ├── daily-app-challenge-prompt.md       # Vollständiger LLM-Prompt (iOS)
│   └── daily-google-play-research-prompt.md # Vollständiger LLM-Prompt (Android)
├── skills/
│   ├── indie-app-opportunity-research/      # Skill: ASO-Nischenforschung + Mini-PRD
│   │   ├── SKILL.md                         # Vollständige Skill-Dokumentation (~60K)
│   │   └── references/cron-safe-commands.md # Sichere Shell-Patterns für Cron
│   └── competitor-app-profiling/            # Skill: Konkurrenz-Analyse
│       └── SKILL.md
└── jobs/
    └── jobs-meta.json                       # Cron-Job-Metadaten (ohne Prompt)
```

## Laufzeit-Umgebung

- **Scheduler:** Hermes Agent Cron-System
- **ASO-Tool:** RespecASO v2.9.0 (self-hosted, `192.168.41.210`)
- **GitHub:** `datasteviee/app-challenge-YYYY-MM-DD`
- **Skills:** `indie-app-opportunity-research` + `competitor-app-profiling`

## Wichtige Heuristiken

| Profil | Popularity | Difficulty | Opportunity | Interpretation |
|--------|-----------|-----------|-------------|----------------|
| **Sweet Spot** | >50 | <50 | >50 | Bestes Target |
| **Hidden Gem** | 25–40 | <25 | 30–40 | Quick Win |
| **Moderate** | >50 | 50–65 | 40–50 | Differenzierung nötig |
| **Contrarian Void** | <35 | >50 | <25 | 🚫 AVOID |

## Verifizierte Revenue Anchors

- Slopes (Curtis Herbert): ~$150K–$500K ARR
- Dark Noise (Charlie Chapman): >$5K MRR
- One Sec (Frederik Riedel): $10K → $25K MRR
- Max (Portfolio, 12+ apps): **$24K MRR**
- Danny Postma (AI Headshots): **$65K erster Monat**
- Peter Levels (levels.fyi): **$200K+ MRR** combined

## Workflow (7 Phasen)

1. **RespecASO Recherche** — Live ASO Scoring (max. 5 Keywords/Batch)
2. **Web-Verifikation** — iTunes API + Revenue-Check
3. **Community Deep Dive** — Reddit → Bluesky → Quora
4. **Competitive Feature Gap Analysis** — Battle Cards
5. **Machbarkeits-Filter** — 7-Tage-Sprint-Check (BUILD/DEFER/DROP)
6. **Mini-PRD** — Title, Subtitle, Description, USP, Tech Stack, Monetization
7. **GitHub Push** — README.md, RESEARCH.md, project.yml

## Sicherheit & Hardened Environment

- Keine `curl | python3` Pipes (HIGH risk)
- Alle `curl` → `/tmp/`, dann Python von Disk
- Kein `&` Backgrounding in Command Strings
- Kein `grep -oP` auf Play Store HTML (stattdessen `browser_snapshot`)

## License

Internes Archiv für das Portfolio von datasteviee.
