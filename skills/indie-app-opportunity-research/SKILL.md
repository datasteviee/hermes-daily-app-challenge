---
name: indie-app-opportunity-research
description: |
  Systematically find profitable, underserved App Store and Google Play Store niches using
  keyword popularity/difficulty data (RespecASO, AppTweak, etc.) or direct Play Store scraping,
  verify public revenue and success stories, then produce ranked product pitches (mini-PRDs)
  with optimized titles, subtitles, and descriptions for iOS and Android.
trigger: |
  Use this skill when the user wants to:
  1. Find app/product niches with specific popularity/difficulty profiles
  2. Run App Store or Google Play Store keyword research to validate demand vs. competition
  3. Research public earnings/revenue of indie apps in a niche
  4. Build ASO-optimized product pitches (mini-PRDs) for a keyword on iOS or Android
  5. Test a hypothesis about keyword economics (e.g. "high difficulty + low demand")
  6. Compare multiple niches and rank them by opportunity score
  7. Research exotic pet, hobby, or specialist-tracker niches where passionate communities exist but no dominant app owns the workflow
  8. Validate demand via Reddit community analysis instead of surveys
  9. Discover Android-first or cross-platform Greenfield opportunities via Play Store scraping
---

# Indie App Opportunity Research via ASO Tools

## Overview

A reproducible workflow to discover defensible indie app niches by combining:
1. **Keyword extraction** from ASO tools (RespecASO, AppTweak, Sensor Tower) or Play Store scraping
2. **Scoring & filtering** by popularity (demand) and difficulty (competition)
3. **Community validation** via Research skill (Reddit/Bluesky/Quora with URL-verified sources) or manual fallback
4. **Adversarial stress-test** via RedTeam before BUILD verdict
5. **Hypothesis-driven verdict** via Science skill (falsifiable BUILD/DROP conditions)
6. **Structured pitches** with ASO-optimized metadata (title, subtitle, description)

## Key Heuristics

| Profile | Popularity | Difficulty | Opportunity | Interpretation |
|---------|-----------|-----------|-------------|----------------|
| **Sweet Spot** | >50 | <50 | >50 | Best targets: high demand, beatable competition |
| **Hidden Gem** | 25–40 | <25 | 30–40 | Quick wins: rank in days with minimal effort |
| **Moderate** | >50 | 50–65 | 40–50 | Requires differentiation; use long-tail keywords |
| **Contrarian Void** | <35 | >50 | <25 | **AVOID**: economically unstable; usually means big-player feature overlap |
| **High Competition** | >80 | >65 | <50 | Dominated; only enter with strong differentiation or feature gap |
| **Indie Wrapper** | ~20 | ~60 | 30–45 | **Viable ONLY IF** users search for a standalone tool. AVOID when the niche is just a feature inside a dominant all-purpose app (e.g. `trail running` → AllTrails already owns it). |

### Critical distinction: Contrarian Void vs. Indie Wrapper

Both have `Pop ~20` and `Diff ~60`. The difference is **who drives the difficulty**:

| | **Contrarian Void — AVOID** | **Indie Wrapper — BUILD** |
|---|---|---|
| **Difficulty source** | Big platform feature (AllTrails → trail running) | 3–6 mediocre indie apps with no dominant player |
| **T5–T20 pattern** | Flat line (T5 ≈ T10 ≈ T20, no ranking ramp) | Staircase or ramp (T5 high, T20 much lower) |
| **Search intent** | Users want the platform's feature; app is a workaround | Users explicitly want standalone tool; no platform meets need |
| **Revenue potential** | Near-zero | $2K–$25K MRR (Max Postma / Danny Postma model) |

**Validation rule**: Search `[dominant app name] [keyword]` in the App Store. If the dominant app's page contains the keyword in *screenshots* (not just metadata), it's a **Void**. If it doesn't appear at all, it's a **Wrapper** opportunity.

---

## Workflow

### Phase 0a: Dynamic Dedup Pre-Flight (MANDATORY before any research)

The single source of truth for "already built" apps is **GitHub at runtime** — no hardcoded list in this skill, no hardcoded list in the prompt. If hardcoded tables exist anywhere in the prompts or skill examples, treat them as illustrative only.

```bash
# Step 1: Pull live built-apps list from GitHub
gh repo list datasteviee --limit 100 \
  --json name,description,createdAt,isPrivate \
  > /tmp/built-apps.json

# Step 2: Extract built apps (any of these patterns indicates a built/in-progress app)
python3 -c "
import json, re
d = json.load(open('/tmp/built-apps.json'))
patterns = [r'.*-care.*', r'.*-tracker$', r'.*-keeper$', r'app-challenge-\d{4}-\d{2}-\d{2}']
built = [r for r in d if any(re.match(p, r['name']) for p in patterns)]
print('Built apps (live from GitHub):')
for r in built:
    print(f\"  {r['name']:<40} | {(r['description'] or '')[:70]}\")
# Save for downstream phases
with open('/tmp/built-keywords.txt', 'w') as f:
    for r in built:
        # Extract root keywords from name + description
        text = (r['name'] + ' ' + (r['description'] or '')).lower()
        f.write(text + '\n')
" > /tmp/built-apps-list.txt
```

**Dedup logic in downstream phases:**
- Before recommending a keyword in Phase 1, grep `/tmp/built-keywords.txt` for the root noun (e.g. `hedgehog`, `chinchilla`, `chore`, `poem`).
- If found → mark keyword as blocked in dedup-log, skip to next-best candidate.
- If `app-challenge-$(date -u +%Y-%m-%d)` already exists with `pushedAt` within last 12h → return `[SILENT]` immediately (idempotency).

> **Why dynamic, not hardcoded**: Hardcoded "Built apps" tables drift the moment a new repo is pushed. The skill must read GitHub state, not its own memory.

---

### Phase 0b: Concept-to-Keyword Reorientation (Optional)

When a user brings an app idea with a specific keyword in mind (e.g. "Chore Roulette"), always test the exact keyword in the live ASO tool **before building a pitch**. If the score is bad (e.g. `Pop ~20`, `Insight: 🚫 Avoid`), do **not** discard the concept. Search for **semantically related keywords**:
- Broader: `chore manager` instead of `chore roulette`
- More generic: `family planner` instead of `chore rotation`
- Alternative framing: `chore chart` instead of `task randomizer`
- Compound modifiers: `household planner`, `cleaning schedule`, `family organizer`

**Real example**:
- `chore roulette` → Pop 22, Diff 41, 🚫 Avoid
- `chore chart` → Pop 47, Diff 39, 🎯 Sweet Spot
- `chore manager` → Pop 50, Diff 41, 👍 Moderate

---

### Phase 0c: Platform Decision — Native iOS vs. Cross-Platform

| Signal | Platform Decision |
|--------|------------------|
| Target audience is **exclusively** iPhone owners (niche hobbyists, paid app culture) | Native iOS (Swift + SwiftData/CloudKit) |
| Target audience includes **families with children** (mixed iOS/Android households) | Cross-platform (Flutter + Supabase) |
| App relies on **CoreML/ARKit/HealthKit** as primary moat | Native iOS |
| Keyword ASO data shows **Android downloads > iOS** | Cross-platform or Android-first |
| Monetization model is **single-device subscription** | Native iOS (simpler stack) |
| Monetization model is **family-wide license** with multi-device entitlement | Cross-platform + shared backend |

---

### Phase 0d: Google Play Store & Android ASO (Cross-Platform Expansion)

**Play Store Scraping Setup:**
```bash
npm install google-play-scraper
# Note: npm package often returns incomplete metadata (no ratings/installs).
# Fallback: direct browser scraping of `https://play.google.com/store/search?q=KW&c=apps`
```

**Android Competitive Heuristics (Install-Range Proxy):**

| Market Signal | Top-1 Installs | Top-3 Pattern | Verdict |
|---|---|---|---|
| **Offene Nische** | <10K or non-care apps (games/sounds) | Top-3 have <10K reviews, weak descriptions, stale updates (>6 mo) | 🟢 **BUILD** — First-mover |
| **Hidden Gem** | 10K–50K | Top-10 contains ≥3 apps with <1K reviews | 🟡 **BUILD** — Long-tail entry |
| **Moderater Wettbewerb** | 50K–500K | Top-3 have 10K–100K reviews, active updates | 🟠 **DIFFERENTIATE** |
| **Dominierter Markt** | >1M | Top-1 has >1M reviews, active dev, Editor's Choice | 🔴 **AVOID** |

**Android ASO Rules:**
- Title: 50 chars max (vs. 30 on iOS) → use long-tail keywords aggressively
- Short description: 80 chars
- Full description: 4,000 chars → front-load keywords in first 167 chars
- Android users accept ad-supported & freemium more readily than iOS users

### Game-Trope Keyword Masking (critical heuristic)

When a care or hobby keyword doubles as a popular game theme (`ant`, `mushroom`, `chinchilla`, `dino`, `dragon`, `shark`, `bee`, `tarantula`), Play Store search results will be flooded with games before any real utility appears. A casual scan may conclude "the niche is saturated" when **not a single care/tracker app exists**.

**Validation rule**: Scroll the first 30 results, tag each entry by category. If >70% are games/entertainment and ≤1 care utility exists, classify as **Non-Relevant Dominance** = 🟢 BUILD NOW.

| Keyword / Search | Game Results? | Actual Care Utility? | Verdict |
|---|---|---|---|
| `ant keeping` | 100% games (Top-20) | 0 dedicated apps | 🟢 GREENFIELD |
| `chinchilla care` | 90% games/sounds | 0 dedicated apps | 🟢 GREENFIELD |
| `tarantula care` | ~60% games | 1 strong all-rounder + 3 weak trackers | 🟡 DIFFERENTIATE |
| `leopard gecko` | ~70% games | 0 dedicated care utility on iOS | 🟢 GREENFIELD |
| `mushroom identification` | ~20% games | `Picture Mushroom` (1M+ DL) | 🔴 DOMINATED |

---

### Phase 0e: No-ASO-Tool Emergency Workflow

When the ASO tool (RespecASO, AppTweak) is **unavailable**, run discovery using only browser snapshots and public APIs.

**Step 1 — Browser scrape Play Store search results**
```
URL: https://play.google.com/store/search?q=KEYWORD&c=apps
Actions:
  1. browser_navigate to URL
  2. browser_snapshot to extract Top-10 list
  3. Note per result: App name, Developer, Rating, Review count, Install badge
  4. If a dedicated app exists (<3 relevant results), click into detail page
```

**Step 2 — Extract competitive signals from snapshot**

| Signal | What to look for |
|---|---|
| **Install badge** | "50+", "1K+", "10K+" — primary Android competition proxy |
| **Review count** | <100 = weak; >10K = established |
| **Last update** | >6 months stale = abandoned; <3 months = active dev |
| **Non-relevant results** | If Top-10 dominated by games for a care keyword → **strong greenfield signal** |

**Step 3 — Safe iTunes Search API cross-check (avoid `curl | python3` pipe)**
```bash
curl -s -o /tmp/ios_check.json "https://itunes.apple.com/search?term=KEYWORD&entity=software&limit=10&country=us"
python3 -c "import json; d=json.load(open('/tmp/ios_check.json')); [print(f'{i+1}. {r[\"trackName\"][:40]:<40} | ⭐{r.get(\"averageUserRating\",0)} ({r.get(\"userRatingCount\",0)})') for i,r in enumerate(d.get('results',[]))]"
```

**Step 4 — Community demand heuristic**

If Reddit search is blocked by CAPTCHA, rely on domain knowledge of subreddit size + pain-point frequency:
- `r/SUBREDDIT` size >20K with daily "Is this normal?" posts → **High demand**.
- Absence of app recommendations in threads → **Greenfield**.

**Pitfall — Terminal scraping timeouts**: Play Store HTML is large (1MB+) and rendered client-side. `curl` returns shell HTML only. In headless/cron environments, prefer `browser_navigate` + `browser_snapshot`.

---

### Phase 1: Tool Setup & Keyword Harvesting

1. Connect to ASO tool (`http://192.168.41.210/` for RespecASO)
2. Run batches of niche keyword hypotheses (max 5 keywords per batch — RespecASO crashes at >5)
3. **Read scores from the live tool UI only** — never quote popularity/difficulty/opportunity from memory or training. Live indices shift weekly.
4. Let the tool compute popularity, overall difficulty, T5/T10/T20 difficulty, and opportunity scores
5. Review the `Insight` column: 💎 Hidden Gem | 🎯 Sweet Spot | ✅ Good Target | 👍 Moderate | 🚫 Avoid | 🔍 Low Volume | ⚔️ High Competition

### Phase 2: Data Compaction

1. Export the keyword table into structured form
2. Compute distance from target profile: `sqrt((pop - target_pop)^2 + (diff - target_diff)^2)`
3. Filter by range or distance threshold
4. Flag keywords with **T5 difficulty >> T20 difficulty** — indicates a ranking "ramp"

### Phase 3: Community Deep Dive (use Research skill)

**Preferred path — invoke Research skill in Standard mode** (4 parallel verified agents, ~30–60s):

```
Skill: Research
Mode: Standard
Query: "Community demand validation for {KEYWORD} app niche. Find:
  1. Reddit subreddit size + 10 verbatim pain-point quotes from last 12 months
  2. Bluesky posts mentioning {KEYWORD} app frustration
  3. Quora top answers comparing {KEYWORD} apps (>50 upvotes only)
  4. Tag each finding as: Pain Point / Workaround / Feature Wish / Competitor Mention
  5. Return URL-verified sources only — hallucinated links are a hard failure"
```

The Research skill returns confidence-tagged findings ([HIGH] / [MED] / [LOW]) with every URL verified before delivery. Use this output directly in the RESEARCH.md community section.

**Fallback path — manual chain when Research is unavailable**:

**Step A: Reddit** (highest signal-to-noise)
- `site:reddit.com/r/SUBREDDIT "how do you" KEYWORD`
- `site:reddit.com/r/SUBREDDIT "is there an app" KEYWORD`
- `site:reddit.com/r/SUBREDDIT "frustrated" KEYWORD`
- `site:reddit.com/r/SUBREDDIT "wish" OR "feature request" KEYWORD`
- Collect 10–15 verbatim quotes. Tag each as: Pain Point / Workaround / Feature Wish / Competitor Mention
- CAPTCHA fallback: `redditsearch.io` or `subredditstats.com`

**Step B: Bluesky** (no auth needed)
```bash
curl -s -o /tmp/bsky.json "https://api.bsky.app/xrpc/app.bsky.feed.searchPosts?q=KEYWORD+app&limit=30"
python3 -c "import json; d=json.load(open('/tmp/bsky.json')); [print(f\"{p['author']['handle']}: {p['record']['text'][:200]}\") for p in d.get('posts',[])]"
```

**Step C: Quora** (fallback when <10 useful signals)
- `browser_navigate` to `https://www.quora.com/search?q=best+KEYWORD+app`
- Focus on answers with >50 upvotes

**Output structure (RESEARCH.md community section):**

```markdown
## Community Deep Dive: {KEYWORD}

### Pain Points
| # | Pain Point | Source (URL, verified) | Frequency |
|---|------------|----------------------|-----------|

### Workarounds
| # | Workaround | Drawback |
|---|------------|----------|

### Feature Requests (verbatim wishlist)
| # | Request | Source | Priority |
|---|---------|--------|----------|

### Competitor Gaps
| # | App Mentioned | What Users Like | What Users Hate |
|---|---------------|-----------------|-----------------|

### Emotional Language (for ASO copy)
- Direct verbatim quotes from users
```

---

### Phase 4: Competitive Feature Gap Analysis

Battle card per competitor (from Phase 3 community data + Phase 1 ASO data):

| App | Core Features | Missing Features (from Phase 3) | My Potential USP |
|-----|--------------|---------------------------------|-------------------|
| {Competitor A} | basic log, photos | widget, AI scan, alerts | ✅ All three built-in |
| {Competitor B} | tracker for all pets | species-specific info | ✅ Species-only deep care |

For deep entity profiling of suspicious competitors (mass-wrapper studios, anonymous dev shops), invoke the **PrivateInvestigator skill** — it pulls together domain history, alternate apps from the same legal entity, LinkedIn footprint, and prior project history. See `../competitor-app-profiling/SKILL.md` for the iTunes/Play Store specifics this skill still owns.

---

### Phase 5: Machbarkeits-Filter — Adversarial Stress-Test

Standard checks (fast path):
- UI in ≤3 Tagen baubar? (→ nur 3–5 Screens)
- Backend nötig? (→ bevorzuge Offline/CoreML)
- Einzel-Feature oder Ökosystem? (→ nur Einzel-Feature)
- RevenueCat-Aufwand sinnvoll? (→ erwartet MRR >$150 nach 3 Monaten?)
- Braucht es spezialisierte Daten?

**Then: invoke RedTeam skill before committing to BUILD**:

```
Skill: RedTeam
Workflow: ParallelAnalysis
Input: "Build pitch for {APP_NAME} in {KEYWORD} niche.
  Core features: {features from Phase 3}.
  USP: {USP from Phase 4}.
  Revenue model: {monetization}.
  Stress-test this idea: 24 atomic claims, 32-agent parallel attack, return
  severity-ranked weaknesses with remediation paths."
```

If RedTeam surfaces severity-CRITICAL findings without remediation, downgrade to 🟡 DEFER.

**Then: structure verdict with Science skill** — turn "BUILD" into a falsifiable hypothesis:

```
Skill: Science
Input: "Frame the BUILD decision for {APP_NAME} as a testable hypothesis.
  Return: H1 (the bet), H0 (null), preconditions for BUILD, kill-criteria for DROP.
  Format as: 'BUILD if [A] and [B], DROP if [C] or [D]'."
```

Verdict: 🟢 BUILD / 🟡 DEFER / 🔴 DROP. Only 🟢 proceeds to Phase 6.

---

### Phase 6: Pitch Construction (Mini-PRD)

```
TITLE:    {Primary Keyword} — {Core Value Proposition}
SUBTITLE: {Secondary keyword phrase that clarifies differentiation}

DESC:
{Hook / why this exists — first 167 chars visible without "more" tap}

• {Feature 1 (keyword-rich bullet, adresses Phase 3 pain point)}
• {Feature 2 (keyword-rich bullet, adresses Phase 3 pain point)}
• {Feature 3 (keyword-rich bullet, adresses Phase 3 pain point)}
• {Feature 4 (keyword-rich bullet, adresses Phase 3 pain point)}

#{hashtag block for algorithmic reach}

Keywords: {comma-separated keyword list for metadata/backend}
```

**Title rules**: Lead with primary keyword, max 30 chars visible (iOS) / 50 chars (Android), include a benefit or qualifier.
**Subtitle rules**: 30 chars (iOS) / 80 chars short-desc (Android), expand primary keyword into long-tail phrase.
**Description rules**: Front-load keywords in first 167 chars, bullet list for skimming, end with hashtag block.

For full Mini-PRD templates across 10 validated niches (chore chart, resignation letter, wedding vows, tattoo, meal planner, speech writer, poem generator, hedgehog care, chinchilla care, leopard gecko), see `references/example-pitches.md`.

### Phase 7: Synthesis & Ranking

Present findings in a ranked table:
- Keyword, Popularity, Difficulty, Opportunity, Insight
- Public revenue evidence (Verified Exact / Verified Range / Estimated / Unverifiable)
- MRR potential range based on comparable apps (see `references/revenue-anchors.md`)
- Verdict: 🟢 BUILD / 🟡 DEFER / 🔴 DROP
- RedTeam severity summary
- Science hypothesis with kill-criteria

---

## Pitfalls

- **Contrarian void fallacy**: `Pop < 35` + `Diff > 50` is usually a trap. High difficulty without matching demand means big players cover the niche as a *feature* (e.g. AllTrails does "trail running"), not as a standalone app.
- **Identical T5/T10/T20 — context matters**: If all three are nearly equal (±3 points), there is no ranking ramp. **When Difficulty > 50**, strong avoid signal. **When Difficulty < 40**, a flat line means *even Top 5 is easy* — keyword still viable (e.g. `pocket money` T5=T10=T20=32, Diff 32, Opp 54 → 🟢 BUILD). Always pair ramp analysis with the absolute Difficulty score.
- **Low volume × Low difficulty ≠ Hidden Gem**: If both popularity and difficulty are <15, the keyword is essentially worthless — no one searches for it.
- **Keyword cannibalization**: Apps that over-optimize for the same keyword in title/subtitle/description simultaneously raise the difficulty for everyone. Long-tail entry is safer.
- **Hallucinated keyword scores**: Never quote popularity, difficulty, or opportunity from memory, training data, or prior sessions. Always read the live ASO tool output. If the tool is unreachable, state the data gap explicitly and do not fabricate numbers.
- **Self-reporting drift**: Never rely on hardcoded "already built" tables — they go stale immediately. Use Phase 0a dynamic dedup.

---

## Autonomous Cron-Job Execution Mode

When this skill is invoked by a scheduled cron job (no user present, no clarifications possible), follow these additional rules.

### Pre-Flight order (strict sequence)
1. **Phase 0a dynamic dedup** — `gh repo list datasteviee` → `/tmp/built-apps.json`
2. **Today's repo idempotency check**: If `app-challenge-$(date -u +%Y-%m-%d)` exists and `pushedAt` is within 12h → return `[SILENT]` immediately.
3. Only after both checks pass: proceed to ASO/Research/Phase-1 work.

### Browser Automation Resilience (RespecASO v2.9+)

| Failure | Recovery |
|---|---|
| `Could not locate element` after click | Re-navigate to `http://192.168.41.210/` for fresh `browser_snapshot` |
| Page redirects to YouTube/CAPTCHA | Re-navigate to dashboard. If twice: fallback to Phase 0e |
| `Searching…` hangs >30s | Reload page, retry with smaller batch (max 5 keywords) |
| `Skipped N keyword(s)` | Already searched today — read from Search History table |

**Rule of three**: If browser automation fails three times on the same action, abandon browser path and fall back to **Phase 0e** (curl + iTunes API + Play Store browser scraping).

### `[SILENT]` Delivery Protocol

- **No new work performed** (repo exists, ASO tool down + no fallback, all keywords blacklisted) → respond with exactly `[SILENT]` and nothing else.
- **Work performed** → produce standard report block (Winner, Keyword, Verdict, Repo URL, Status).
- **Never** combine `[SILENT]` with content. Never add explanations after `[SILENT]`.

### Timezone Awareness
Cron is configured for **UTC** (`date -u`). The daily challenge date is `date -u +%Y-%m-%d`. Not local time.

### Emergency Abort Checklist
Abort and return `[SILENT]` if:
1. Today's repo already exists, pushed within last 12 hours.
2. ASO tool unreachable AND iTunes API returns errors after 3 retries.
3. All candidate keywords for the day's rotation are blocked (via Phase 0a).
4. `gh auth status` fails.

Log abort reasons to `/tmp/app-challenge-abort-$(date -u +%Y-%m-%d).log`.

---

## Hardened Environment Security Workarounds

| Blocked Pattern | Why Blocked | Safe Replacement |
|---|---|---|
| `curl ... \| python3` | HIGH risk: shellcode injection | `curl -o /tmp/file.json && python3 -c "... open('/tmp/file.json') ..."` |
| `cat file \| python3` | HIGH risk: pipe to interpreter | `python3 -c "... open('/tmp/file.json') ..."` |
| `command1 & command2` | `&` backgrounding rejected | `command1 && command2` or separate `terminal` calls |
| `grep -oP '(?<=...)'` | PCRE lookbehind/lookahead unavailable | Use `browser_snapshot` for Play Store extraction |
| `.app` TLD in curl URLs | Lookalike TLD detection | Use `browser_navigate` to bsky.app directly |

**Key rule for cron jobs**: Every `curl` writes to `/tmp/` first. Every `python3` reads from disk. No pipes between network tools and interpreters.

---

## References

- **`references/example-pitches.md`** — 10 validated Mini-PRD templates (chore chart, resignation letter, wedding vows, tattoo, meal planner, speech writer, poem generator, hedgehog care, chinchilla care, leopard gecko) + exotic pet portfolio pattern + Root vs. Long-tail rule
- **`references/revenue-anchors.md`** — Verified public revenue benchmarks for sizing MRR potential
- **`references/cron-safe-commands.md`** — Full blocked-pattern list and safe replacements
- **`../competitor-app-profiling/SKILL.md`** — Reverse-engineering competitor developers via iTunes Search API and Play Store
- **PAI skills used by this workflow**: `Research` (Phase 3 community deep dive), `RedTeam` (Phase 5 adversarial stress-test), `Science` (Phase 5 hypothesis-driven verdict), `PrivateInvestigator` (Phase 4 deep entity profiling when needed)
