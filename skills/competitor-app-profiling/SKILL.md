---
name: competitor-app-profiling
description: >
  Reverse-engineer a rival indie app developer's full portfolio, business model, and estimated performance. 
  Uses domain whois, website scraping, iTunes Search API, App Store ratings, and Play Store heuristics.
  Trigger when the user asks "who is behind X", "what apps does Y have", "how many downloads does Z have",
  or any competitive intel request against an app, developer name, or domain.
tags: [competitive-intelligence, app-store, market-research, aso, reverse-engineering, downloads-estimation]
---

# Competitor App Developer Profiling

Systematically profile an indie app developer (or studio) using only public APIs and data. No private tools required.

## Trigger Conditions

- User asks about an app developer, domain, or specific app by name/descriptor
- User wants to know "how many downloads", "revenue", "what else they have"
- Competitive research on App Store / Play Store rivals
- Evaluating whether a niche is saturated based on existing players

## Workflow

### 1. Domain & Legal Identity
```bash
whois DOMAIN
```
- Registrar (GoDaddy, Namecheap, etc.)
- Registration date / expiry (indicates how established)
- Privacy proxy vs. real registrant data
- DNS nameservers

### 2. Website Recon
```bash
curl -sL DOMAIN | grep -i -E "(app|store|apple|itunes|id[0-9]|bundle|developer)"
```
- Extract app names, store links, developer IDs
- Look for structured data (`application/ld+json`)
- Note: many indie devs use Astro/static sites — inspect the JSON-LD

### 3. iTunes Search API (Primary Source)
```bash
# by developer name
curl -s "https://itunes.apple.com/search?term=DEV_NAME&entity=software&limit=200" | python3 -c "import json,sys; d=json.load(stdin); [print(f'{r[\"trackName\"]} | {r[\"sellerName\"]} | id:{r[\"trackId\"]}') for r in d.get('results',[])]"

# by artist/developer ID (if known from step 2)
curl -s "https://itunes.apple.com/lookup?id=ARTIST_ID&entity=software&limit=200" | python3 -c "..."

# by related keyword (competitor discovery in the niche)
curl -s "https://itunes.apple.com/search?term=KEYWORD&entity=software&limit=10" | python3 -c "..."
```

Key fields to extract per app:
- `trackName`, `trackId`, `primaryGenreName`
- `releaseDate` (portfolio velocity)
- `formattedPrice`, `price` (monetization model)
- `userRatingCount`, `averageUserRating`
- `sellerName`, `artistName` (legal entity behind the app)

### 4b. Niche Competitive Position Check (Optional but powerful)
After profiling one developer, search the same niche by keyword to see how they rank against *all* competitors:
```bash
curl -s "https://itunes.apple.com/search?term=KEYWORD&entity=software&limit=15" | \
  python3 -c "import json,sys; d=json.load(sys.stdin); [print(f'{i+1}. {r.get(\"trackName\",\"\")[:40]:<40} | {r.get(\"sellerName\",\"\")[:25]:<25} | ⭐{r.get(\"averageUserRating\",0)} ({r.get(\"userRatingCount\",0)})') for i,r in enumerate(d.get('results',[]))]"
```
This reveals whether the developer is a Top-3 player in their claimed niche or irrelevant (common with spray-and-pray portfolios).

Apple publishes **only** `userRatingCount`, not downloads. Use this heuristic ratio:

| userRatingCount | Estimated iOS Downloads |
|-----------------|------------------------|
| 0               | 0–50 (likely abandoned) |
| 1–10            | 50–500 |
| 10–50           | 500–5,000 |
| 50–200          | 2,000–15,000 |
| 200–1,000       | 10,000–80,000 |
| 1,000–5,000     | 50,000–400,000 |
| 5,000+          | 250,000+ |

**Assumptions baked in:** 1 review per 20–100 downloads (varies wildly by app quality, paywall timing, and prompt aggressiveness). Free apps with intrusive review prompts skew low (1:20). Premium/subscription apps with silent happy users skew high (1:100+).

Always disclose: *"Apple gives no real download numbers — this is a heuristic estimate based on review-to-download ratios observed in the industry."*

### 5. Play Store Cross-Reference
```bash
curl -sL "https://play.google.com/store/apps/dev?id=DEV_ID" | grep -o 'href="/store/apps/details?id=[^"]*"'
```
- Check if same bundle IDs exist on Android
- Google sometimes shows install ranges (1K+, 10K+, etc.) — watch for ` installs` in page text
- Use `grep -oP '(?<= )[0-9]+[KM]?\+?(?= installs)'` to extract ranges

### 6. Business Model Inference

From the app list, infer:
- **Portfolio strategy**: Spray-and-pray (40+ AI identifier apps) vs. concentrated (1–3 deep products)
- **Monetization**: Free+IAP, subscription, paid, ad-supported
- **Tech stack**: Camera/vision (AI identifier apps), health sensors, MLKit
- **Niche density**: How many apps in same category from same dev (indicates keyword farming)

### 7. Output Template

```markdown
## [Developer/Studio Name] — Profile

| | |
|---|---|
| **Legal Entity** | [sellerName from iTunes] |
| **Domain** | [domain] |
| **Registered** | [whois creation date] |
| **Registrar** | [GoDaddy/Namecheap/etc] |
| **Privacy** | [Yes/No — Domains By Proxy?] |
| **Total Apps** | [count] |
| **Platforms** | iOS [count] / Android [count] |

### Portfolio Summary
| App | Category | Released | ⭐ Ratings | Avg Rating | Est. Downloads |
|-----|----------|----------|------------|------------|----------------|
| ... | ... | ... | ... | ... | ... |

### Top Apps by Estimated Traction
1. ...
2. ...
3. ...

### Business Model
- **Strategy**: [spray-and-pray / concentrated / utility farming]
- **Monetization**: [subscription / IAP / ads / paid]
- **Release Velocity**: [apps per month]
- **Pattern**: [what unifies the apps? e.g., "all AI camera scanners"]

### Competitive Assessment
- [Threat level: low/medium/high]
- [Why: they have more apps but lower quality per app, etc.]
```

## Pitfalls

- iTunes Search API fuzzy-matches by default — filter `sellerName` or `artistName` to dedupe
- App Store web frontend often returns "An Error Occurred" for dev pages — always use the API
- Play Store page structure changes frequently, scraping is brittle — use `grep` defensively
- `userRatingCount` is lifetime, not monthly — a 5-year-old app with 50 ratings is worse than a 3-month-old with 50
- Some developers rebrand (`Artmvstd SIA` vs `Maksim Artemov`) — search both
- Google Play `dev?id=` IDs differ from iTunes `artistId` — they are NOT the same number
- Privacy-proxied whois tells you nothing about the person, only that they paid for privacy

## Variations

- **Single-app focus**: Skip step 2, go straight to `itunes.apple.com/lookup?id=APP_ID`
- **Niche saturation scan**: Search iTunes by genre/keyword, group by developer, count apps per dev
- **Revenue estimation**: If you know subscription price × estimated active users (downloads × 5–15% retention). Very rough.
