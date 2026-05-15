---
name: competitor-app-profiling
description: >
  Profile a rival indie app developer's full portfolio, business model, and estimated performance.
  Uses iTunes Search API, App Store ratings, Play Store install ranges. For DEEP entity background
  (legal entity history, founder LinkedIn, alternate domains, prior projects), delegates to the
  PAI PrivateInvestigator skill. Trigger when the user asks "who is behind X", "what apps does Y have",
  "how many downloads does Z have", or any competitive intel request.
tags: [competitive-intelligence, app-store, market-research, aso, reverse-engineering]
---

# Competitor App Developer Profiling

Two-mode skill: **light-touch app-store-only profiling** (this file) for cron-fast iterations, and **deep entity research** (delegate to `PrivateInvestigator`) when the developer is suspicious, anonymized, or worth understanding deeply.

## Mode Selection

| Question | Use this skill (light) | Delegate to PrivateInvestigator (deep) |
|---|---|---|
| "How many apps does this dev have on iOS?" | ✅ | ❌ (overkill) |
| "Avg rating + review count across portfolio?" | ✅ | ❌ |
| "Estimated downloads from review count?" | ✅ | ❌ |
| "Who is the legal entity behind this app?" | partial (sellerName only) | ✅ |
| "Is this a mass-wrapper studio or a real solo indie?" | infer from portfolio velocity | ✅ |
| "Founder LinkedIn, prior startups, accelerator history?" | ❌ | ✅ |
| "Alternate domains, registered email, whois history?" | ❌ | ✅ |
| "Is this developer monetizing via shady patterns?" | ❌ | ✅ |

**Default for cron jobs**: stay light. Only escalate to PrivateInvestigator when a Phase 4 competitor in `indie-app-opportunity-research` looks like a Top-3 threat AND a portfolio scan reveals red flags (40+ apps from same entity, generic AI-wrapper naming, etc.).

## Trigger Conditions
- User asks about an app developer, domain, or specific app by name
- User wants "how many downloads", "revenue", "what else they have"
- Competitive research on App Store / Play Store rivals
- Evaluating whether a niche is saturated based on existing players

---

## Light-Touch Workflow (this skill)

### 1. Domain & Legal Identity (quick)
```bash
whois DOMAIN
```
- Registrar, registration date, privacy proxy vs. real registrant, DNS nameservers
- If privacy-proxied AND the dev is a Phase 4 threat → **escalate to PrivateInvestigator**

### 2. Website Recon
```bash
curl -sL DOMAIN | grep -i -E "(app|store|apple|itunes|id[0-9]|bundle|developer)"
```
- Extract app names, store links, developer IDs
- Look for structured data (`application/ld+json`)

### 3. iTunes Search API (Primary Source)
```bash
# by developer name
curl -s -o /tmp/itunes_dev.json "https://itunes.apple.com/search?term=DEV_NAME&entity=software&limit=200"
python3 -c "import json; d=json.load(open('/tmp/itunes_dev.json')); [print(f'{r[\"trackName\"]} | {r[\"sellerName\"]} | id:{r[\"trackId\"]}') for r in d.get('results',[])]"

# by artist/developer ID
curl -s -o /tmp/itunes_artist.json "https://itunes.apple.com/lookup?id=ARTIST_ID&entity=software&limit=200"

# competitor discovery by keyword
curl -s -o /tmp/itunes_kw.json "https://itunes.apple.com/search?term=KEYWORD&entity=software&limit=10"
```

Key fields:
- `trackName`, `trackId`, `primaryGenreName`
- `releaseDate` (portfolio velocity)
- `formattedPrice`, `price` (monetization model)
- `userRatingCount`, `averageUserRating`
- `sellerName`, `artistName` (legal entity)

### 4. Niche Competitive Position Check
After profiling one developer, scan the niche to see ranking:
```bash
curl -s -o /tmp/niche.json "https://itunes.apple.com/search?term=KEYWORD&entity=software&limit=15"
python3 -c "import json; d=json.load(open('/tmp/niche.json')); [print(f'{i+1}. {r.get(\"trackName\",\"\")[:40]:<40} | {r.get(\"sellerName\",\"\")[:25]:<25} | ⭐{r.get(\"averageUserRating\",0)} ({r.get(\"userRatingCount\",0)})') for i,r in enumerate(d.get('results',[]))]"
```

### 5. Download Estimation Heuristic

Apple publishes **only** `userRatingCount`, not downloads:

| userRatingCount | Estimated iOS Downloads |
|-----------------|------------------------|
| 0               | 0–50 (likely abandoned) |
| 1–10            | 50–500 |
| 10–50           | 500–5,000 |
| 50–200          | 2,000–15,000 |
| 200–1,000       | 10,000–80,000 |
| 1,000–5,000     | 50,000–400,000 |
| 5,000+          | 250,000+ |

**Assumptions baked in:** 1 review per 20–100 downloads. Free apps with intrusive review prompts skew low (1:20); premium with silent happy users skew high (1:100+).

**Always disclose**: *"Apple gives no real download numbers — this is a heuristic estimate based on review-to-download ratios observed in the industry."*

### 6. Play Store Cross-Reference
```bash
curl -sL -o /tmp/play.html "https://play.google.com/store/apps/dev?id=DEV_ID"
grep -o 'href="/store/apps/details?id=[^"]*"' /tmp/play.html
```
- Same bundle IDs on Android?
- Google sometimes shows install ranges (1K+, 10K+) — search for ` installs` in page text

### 7. Business Model Inference (light)

From the app list, infer:
- **Portfolio strategy**: Spray-and-pray (40+ AI identifier apps) vs. concentrated (1–3 deep products)
- **Monetization**: Free+IAP, subscription, paid, ad-supported
- **Tech stack**: Camera/vision (AI identifier apps), health sensors, MLKit
- **Niche density**: How many apps in same category from same dev (indicates keyword farming)

### 8. Output Template

```markdown
## [Developer/Studio Name] — Profile

| | |
|---|---|
| **Legal Entity** | [sellerName from iTunes] |
| **Domain** | [domain] |
| **Registered** | [whois creation date] |
| **Registrar** | [GoDaddy/Namecheap/etc] |
| **Privacy proxy** | [Yes/No] |
| **Total Apps** | [count] |
| **Platforms** | iOS [count] / Android [count] |

### Portfolio Summary
| App | Category | Released | ⭐ Ratings | Avg Rating | Est. Downloads |
|-----|----------|----------|------------|------------|----------------|

### Top Apps by Estimated Traction
1. ...

### Business Model
- **Strategy**: [spray-and-pray / concentrated / utility farming]
- **Monetization**: [subscription / IAP / ads / paid]
- **Release Velocity**: [apps per month]
- **Pattern**: [what unifies the apps?]

### Competitive Assessment
- [Threat level: low/medium/high]
- [Why]
- [Escalate to PrivateInvestigator? Yes/No + reason]
```

---

## Deep-Mode Escalation (delegate to PrivateInvestigator)

When the light-touch profile reveals one of these red flags, hand off to the `PrivateInvestigator` skill:

| Red flag | Why escalate |
|---|---|
| 40+ apps under one `sellerName` | Mass-wrapper studio; need to understand business model deeply before competing |
| `sellerName` is a generic LLC + privacy-proxied domain | Anonymized operator; OSINT needed |
| Top-3 competitor in target niche with active updates + funded look | Worth knowing who they are before going head-to-head |
| App description copies competitor wording verbatim | Possible IP/reskinning concern; understand legal exposure |
| Reviews mention "scam", "fake", "doesn't work" repeatedly | Validate business pattern before assuming the niche is monetizable |

**Handoff prompt:**
```
Skill: PrivateInvestigator
Target: {sellerName / legal entity} + {domain} + {linked apps}
Goal: Identify founder(s), prior projects, accelerator/funding history, alternate
  domains, and any pattern of behavior (mass-wrapper farming, abandoned portfolios,
  prior shutdowns). Return structured profile + risk assessment.
```

---

## Pitfalls
- iTunes Search API fuzzy-matches by default — filter `sellerName` or `artistName` to dedupe
- App Store web frontend often returns "An Error Occurred" — always use the API
- Play Store structure changes frequently — use `grep` defensively, or `browser_snapshot`
- `userRatingCount` is lifetime, not monthly — a 5-year-old app with 50 ratings is worse than a 3-month-old with 50
- Developers rebrand (e.g. `Artmvstd SIA` vs `Maksim Artemov`) — search both
- Google Play `dev?id=` differs from iTunes `artistId` — NOT the same number
- Privacy-proxied whois tells you nothing about the person — escalate to PrivateInvestigator if it matters

## Variations
- **Single-app focus**: Skip step 2, go straight to `itunes.apple.com/lookup?id=APP_ID`
- **Niche saturation scan**: Search iTunes by genre/keyword, group by developer, count apps per dev
- **Revenue estimation**: subscription price × estimated active users (downloads × 5–15% retention) — very rough
