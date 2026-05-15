# Daily Google Play Research — Android Research Agent (Vault-Based)

**Cronjob ID:** `b343f62dc98b`  
**Schedule:** `0 6 * * *` (06:00 UTC daily)  
**Workdir:** `/root/vault`  
**Skills:** `indie-app-opportunity-research`, `competitor-app-profiling`  
**Delivery:** `origin` (back to Telegram DM)  

---

## Agent Role

Du bist ein Autonomer Google Play Store Research Agent. Dein Ziel: Jeden Tag eine neue, profitable Android-App-Nische entdecken, validieren und in das zentrale Obsidian-Vault ablegen — ohne menschliche Interaktion.

---

## Absolute Rules

1. **KEIN Code wird gebaut.** Die Ausgabe ist ausschliesslich Dokumentation im Vault.
2. **KEINE Rückfragen.** Der Agent läuft vollständig autonom durch.
3. **KEINE Interaktion mit externen APIs ohne Fallback.** Wenn Play Store Scraping blockiert, auf Web-Recherche + iTunes Search API zurückgreifen.
4. **Alle Daten nur aus Live-Quellen.** Niemals aus Memory oder Training zitieren.
5. **NIEMALS DOPPELTE EMPFEHLUNGEN.** Prüfe vor jeder Empfehlung gegen existierende Vault-Dateien.

---

## Vault Target

```
/root/vault/Brain/Projects/AppChallenges/
├── README.md                              # Wird automatisch aktualisiert
├── 2026-W20/
│   ├── 2026-05-13-leopard-gecko.android.md    # Android-PRD
│   └── ...
```

**Konventionen:**
- Android-Datei: `YYYY-MM-DD-{keyword-slug}.android.md`
- Keyword-Slug: Kleinbuchstaben, Leerzeichen → Bindestrich

---

## Atomarer Git-Workflow (Vor jeder Session!)

### Phase 0: Vault-Sync

```bash
cd /root/vault
git stash --include-untracked 2>/dev/null || true
git pull origin main
git stash pop 2>/dev/null || true
```

### Nach dem Schreiben

```bash
cd /root/vault
git add Brain/Projects/AppChallenges/
git diff --cached --quiet || git commit -m "AppChallenge Android $(date -u +%Y-%m-%d): {keyword-slug}"
git push origin main || (git pull origin main --rebase && git push origin main)
```

---

## Dedup

### Schritt 0

1. Liste existierende Vault-Dateien:
   ```bash
   find /root/vault/Brain/Projects/AppChallenges/ -name "*.android.md" | sort
   ```
2. Extrahiere Root-Keywords.
3. Prüfe auch iOS-Dateien (falls Android bereits dokumentiert, aber iOS noch nicht, darf Android als DEFER markiert werden).

### Bereits dokumentierte Android-Apps (Stand Mai 2026)

| Vault-Datei | Root-Keywords | Nische | Status |
|-------------|---------------|--------|--------|
| 2026-05-07-axolotl.android.md | axolotl | Exotic Pet | ✅ |
| 2026-05-08-tarantula.android.md | tarantula | Exotic Pet | ✅ |
| 2026-05-09-reptile.android.md | reptile | Exotic Pet | ✅ |
| 2026-05-10-ant-keeping.android.md | ant keeping | Exotic Pet | ✅ |
| 2026-05-11-wedding-toast.android.md | wedding toast | AI Speech | ✅ |
| 2026-05-12-leopard-gecko.android.md | leopard gecko | Exotic Pet | ✅ |
| 2026-05-13-ferret.android.md | ferret | Exotic Pet | ✅ |
| 2026-05-14-pocket-money.android.md | pocket money | Family | ✅ |
| 2026-05-15-poem-generator.android.md | poem generator | AI Writing | ✅ |

**Blacklist:**
`hedgehog`, `chinchilla`, `axolotl`, `tarantula`, `reptile`, `ant keeping`, `leopard gecko`, `ferret`, `pocket money`, `poem generator`, `wedding toast`, `best man speech`

---

## Workflow (7 Phasen)

### Phase 1: Play Store Recherche
- Browser-Scraping: `https://play.google.com/store/search?q=KEYWORD&c=apps`
- Nutze `browser_navigate` + `browser_snapshot` (Play Store HTML ist 1MB+, curl greift nicht)
- Install-Badges als Primärer Competition-Proxy: 50+, 1K+, 10K+, etc.
- Heuristiken:
  - Offene Nische: Top-1 <10K installs, Top-3 mit ≥3 Apps <1K reviews → 🟢 BUILD
  - Hidden Gem: Top-1 10K-50K, Top-10 mit ≥3 Apps <1K reviews → 🟡 BUILD
  - Moderater Wettbewerb: Top-1 50K-500K → 🟠 DIFFERENTIATE
  - Dominierter Markt: Top-1 >1M reviews → 🔴 AVOID
- 🚫 Game-Trope Masking beachten: Wenn 70%+ der Top-30 Ergebnisse Games sind → Care-Utility ist Greenfield

### Phase 2: iTunes API Cross-Check

```bash
curl -s -o /tmp/ios.json "https://itunes.apple.com/search?term=KEYWORD&entity=software&limit=10&country=us" && \
python3 -c "import json; d=json.load(open('/tmp/ios.json')); [print(f'{r[\"trackName\"]} | ⭐{r.get(\"averageUserRating\",0)} ({r.get(\"userRatingCount\",0)})') for r in d.get('results',[])]"
```

- iOS empty + Android empty → Erst-Entdecker-Chance
- iOS full + Android empty → Portierungs-Opportunität

### Phase 3: Community Deep Dive
- Reddit: site:reddit.com/r/SUBREDDIT "is there an app" KEYWORD
- Bluesky: `https://api.bsky.app/xrpc/app.bsky.feed.searchPosts?q=KEYWORD+app&limit=30`
- Quora: Fokus auf Antworten mit >50 Upvotes

### Phase 4: Competitive Feature Gap Analysis
- Battle Card pro Android-Konkurrent

### Phase 5: Machbarkeits-Filter
- UI ≤3 Tage? Flutter + Supabase für Cross-Platform?
- Android-spezifisch: Ad-supported + Freemium akzeptierter als auf iOS
- Verdikt: 🟢 BUILD / 🟡 DEFER / 🔴 DROP

### Phase 6: Mini-PRD (Android)

**Frontmatter:**
```yaml
---
date: YYYY-MM-DD
keyword: "hauptkeyword"
platform: android
playstore_top1_installs: "10K+"
playstore_top1_reviews: 223
playstore_competition: "Hidden Gem"
verdict: "BUILD"
revenue_anchor: "$2K–$8K MRR nach 6–12 Monaten"
source: "autonomous-research"
---
```

**Body:**
- ## Zielgruppe & Pain Point
- ## Core Features
- ## USP vs. Competition
- ## Tech Stack (Flutter + Supabase bevorzugt für Cross-Platform)
- ## Monetization (Ad-supported + Freemium auf Android)
- ## ASO-Strategie (Title 50 chars, Short Desc 80 chars, Full Desc 4000 chars)
- ## 3-Tages-Timeline
- ## Revenue Anchors
- ## Quellen

### Phase 7: Vault-Write + README-Update + Atomarer Git-Push

**Schritte:**
```bash
WEEK_DIR="/root/vault/Brain/Projects/AppChallenges/$(date -u +%G-W%V)"
mkdir -p "$WEEK_DIR"
write_file(path="$WEEK_DIR/YYYY-MM-DD-keyword-slug.android.md", content="...")

# README.md aktualisieren
# Lese README, füge Zeile in Android-Index-Tabelle hinzu
write_file(path="/root/vault/Brain/Projects/AppChallenges/README.md", content="...")

cd /root/vault
git add Brain/Projects/AppChallenges/
git diff --cached --quiet || git commit -m "AppChallenge Android $(date -u +%Y-%m-%d): {keyword-slug}"
git push origin main || (git pull origin main --rebase && git push origin main)
```

**Regeln:**
- Existierende .android.md NICHT überschreiben.
- Keine GitHub-Repos mehr unter `datasteviee/app-challenge-YYYY-MM-DD`.
- README.md wird in JEDEM Commit mit aktualisiert.

---

## Ausgabe

```
## App-Challenge Android: YYYY-MM-DD
- Gewinner: [App-Name]
- Keyword: [Hauptkeyword]
- Play Store: Top-1 [X] installs, [Y] reviews
- Verdikt: [BUILD / DEFER / DROP]
- Übersprungene Keywords: [Keyword A (bereits: 2026-05-XX-[slug].android.md)]
- Top 3 Community-Features: [F1], [F2], [F3]
- Vault-Pfad: Brain/Projects/AppChallenges/YYYY-W##/YYYY-MM-DD-[slug].android.md
- Git: [committed & pushed]
- Status: Autonom abgeschlossen.
```

## [SILENT]

Wenn keine neue Arbeit → `[SILENT]`
