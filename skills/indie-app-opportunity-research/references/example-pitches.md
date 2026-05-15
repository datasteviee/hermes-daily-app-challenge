# Mini-PRD Examples — Validated Pitches

> Extracted from SKILL.md to reduce context load. These are illustrative templates from real research sessions, not authoritative — always verify ASO data live before reusing.

Each example documents:
- **Discovery context**: date, ASO scores, cross-platform check
- **Mini-PRD**: TITLE, SUBTITLE, DESC structure
- **Tech stack + monetization**
- **Revenue anchor + reasoning**

---

## Example 0 — Cross-Platform Family App (`chore chart`, `chore manager`)

```
TITLE:    Chore Chart — Family Task Planner
SUBTITLE: Smart household routine for families

DESC:
Chore Chart makes family life easier. Create rotating weekly schedules,
assign age-appropriate tasks, and keep everyone on track with smart
reminders — on iPhone, iPad, or Android.

• Rotating weekly schedules — automatically fair
  — chore chart, chore manager, family planner
• Age-appropriate tasks (3–6, 7–12, 13+ years)
  — kids chores, chore list, family organizer
• 🪙 Chore Coins: In-app currency for completed tasks
  — chore tracker, rewards app, kids motivation
• 🖼️ Beweisfotos: Kids take photos, parents approve
  — chore verification, family accountability
• 🎁 Family Rewards: Exchange coins for screen time, outings, allowance
  — reward system, family economy, chore allowance
• Widget & calendar sync (iCal / Google Calendar)
  — household routine, cleaning schedule
• Works offline — your family data stays private
  — offline family app, private chore tracker

#familyplanner #chorechart #kidschores #familylife #household
```

**Cross-platform rationale**: Keyword `chore manager` with Pop 50 and family-keyword clustering mandates Android + iOS. Children use hand-me-down Android tablets while parents own iPhones. RevenueCat family-wide entitlement (`family_id` as `customer_id`) plus Supabase self-hosted backend ensures shared state across ecosystems.

**Gamification pattern — Chore Coins economy**:
| Task | Base Coins | Schwierigkeit | Streak ×7 | Streak ×30 |
|------|-----------|---------------|-----------|------------|
| Tisch decken | 2 🪙 | Easy | ×1.5 | ×2.0 |
| Müll raus | 3 🪙 | Easy | ×1.5 | ×2.0 |
| Staubsaugen | 5 🪙 | Medium | ×1.5 | ×2.0 |
| Bad putzen | 8 🪙 | Hard | ×1.5 | ×2.0 |
| Küche aufräumen | 10 🪙 | Hard | ×1.5 | ×2.0 |

**Monetization**: 7-day trial → Read-Only after expiry → $4.99/mo or $39.99/yr or $79.99 lifetime.

**Revenue anchor**: Family organizer apps (Cozi, Sweepy) show $1–2M lifetime gross at 14–20M downloads with freemium models. Indie implementations with narrower scope and Premium Tool billing typically land $150–$600 MRR in the first 6 months.

---

## Example 1 — AI Resignation Letter (`resignation letter maker`)

```
TITLE:    QuitCraft AI — Resignation Letter Maker
SUBTITLE: Write professional two weeks notice letters

DESC:
Quitting your job? Don't stress about the wording. QuitCraft AI
generates polished, professional resignation letters tailored to your
situation — graceful exits guaranteed.

• AI-generated two weeks notice in under 60 seconds
   — resignation letter templates, quit job letter, professional notice
• Custom tone: formal, grateful, or concise
   — email resignation generator, farewell letter to team
• Review & edit before sending via email or PDF
   — notice letter generator, leave job email builder
• History archive: retain all letters for records
   — resignation letter archive, work exit documents

#resignationletter #quittingjob #2weeksnotice #careertools
```

**Revenue anchor**: A builder on Hacker News hit $1,200 MRR after 6 weeks running a combined resignation + cover letter micro-app.

---

## Example 2 — AI Wedding Vows (`wedding vows writer`)

```
TITLE:    Wedding Vows AI — Write Your Perfect Vows
SUBTITLE: AI speech writer for love promises

DESC:
Staring at a blank page before your wedding? Our AI Wedding Vows
Writer helps you craft heartfelt, personalized vows in minutes. Just
answer 5 questions about your love story and get a unique speech
ready for the big day.

• Answer 5 prompts — the AI builds a one-of-a-kind vow
   — wedding vows generator, love promise writer, ceremony speech
• Tone selector: romantic, funny, poetic, or tear-jerker
   — funny vows generator, romantic wedding vows AI
• Export as PDF or email to your officiant
   — officiant speech maker, vow text download
• Companion: best man & maid of honor speeches
   — best man speech AI, maid of honor speech generator

#weddingvows #aispeechwriter #weddingidea #vowsgenerator
```

**Revenue anchor**: A Reddit builder reported $450 first month with a $6.99 one-time pricing tier for ceremonial AI generation.

---

## Example 3 — AI Tattoo Generator (`ai tattoo generator`)

```
TITLE:    InkMind AI — Tattoo Design Generator
SUBTITLE: Create custom tattoo art with AI

DESC:
Describe your dream tattoo. Pick a style — minimal, Japanese,
geometric, fine line. InkMind AI generates custom tattoo designs you
can bring directly to your artist. No generic clipart: every design
is unique to your idea.

• 15+ tattoo styles (fine line, traditional, dotwork, etc.)
   — ai tattoo artist, custom tattoo generator, fine line tattoo AI
• Export high-resolution PNG + sleeve-ready layouts
   — sleeve tattoo ideas, tattoo mockup creator
• Tattoo placement guide: arm, leg, back, chest
   — placement generator, tattoo stencil download
• Save designs to "my boards" for the studio visit
   — tattoo board maker, studio-ready tattoo export

#tattoodesign #aitattoo #customtattoo #tattooideas #tattooart
```

**Revenue anchor**: "Marc" (r/SaaS) made $1,800 in the first month with a simple prompting layer over DALL-E.

---

## Example 4 — AI Meal Planner (`ai meal planner`)

```
TITLE:    MealMind AI — Weekly Meal Planner
SUBTITLE: AI-powered diet plans and grocery lists

DESC:
Stop scrolling for recipes. MealMind AI creates a personalized
weekly meal plan based on your diet goals, allergies, and calorie
targets — then auto-generates your shopping list.

• Tell us your diet, allergies, and calorie target
   — meal planner, diet plan generator, ai recipe maker
• Weekly plan with grocery list & batch-prep tips
   — grocery list planner, healthy meal prep assistant
• Swap meals on the fly; AI recalculates macros
   — macro tracker, calorie meal swap
• Family mode: multi-person plans & shopping lists
   — family meal planner, household diet app

#mealplanning #aihealth #dietplan #healthymeals #smartgrocery
```

**Revenue anchor**: Meal prep / fitness AI apps typically land in the $2K–$8K MRR range within 60–90 days.

---

## Example 5 — AI Event Speech Writer (`best man speech writer`)

```
TITLE:    SpeechCraft AI — Event Speech Writer
SUBTITLE: Write best man, maid of honor & toasts

DESC:
Need to give a toast but can't find the words? SpeechCraft AI
writes funny, touching, and appropriate speeches for weddings,
birthdays, graduations, and retirements.

• Weddings: best man, maid of honor, father of the bride
   — wedding speech writer, best man speech, maid of honor toast
• Milestone events: birthdays, graduations, anniversaries
   — birthday toast writer, graduation speech AI
• Tone control: hilarious, heartfelt, or roast-light
   — roast writer, funny toast generator
• Export PDF or text-to-speech rehearsal mode
   — rehearsal prompter, speech teleprompter

#weddingspeech #bestman #toastwriter #eventplanning
```

**Revenue anchor**: A Reddit post cited $350 during the first launch week for a wedding-speech micro-app.

---

## Example 6 — Exotic Pet Care Tracker (`hedgehog care`)

```
TITLE:    Hedgehog Care — Health & Diet Tracker
SUBTITLE: AI vet scanner & daily care log

DESC:
The only app built exclusively for hedgehog owners. Log meals, weights,
symptoms, and vet visits. Scan your hedgehog for common skin issues
using on-device AI. Set temperature/humidity reminders. Track hibernation
cycles. Join thousands of hedgehog parents keeping their spiky friends
healthy.

• Weight & meal tracker with trend graphs
   — hedgehog diet, pet health log, exotic pet tracker
• AI skin condition scanner (works offline)
   — hedgehog vet, pet symptom checker, skin condition AI
• Temperature & humidity alerts for habitat
   — pet habitat monitor, small pet care, cage conditions
• Vet visit log with photo attachments
   — pet vet tracker, health photo diary, animal medical log

#hedgehogcare #exoticpet #smallpet #pethealth #hedgehog
```

**Revenue anchor**: Reddit communities (r/hedgehogs: 80K+ members; r/Hedgehog: 12K+) drive daily health/diet questions. No dedicated hedgehog care app existed in the App Store at discovery time. CoreML-based scanning and offline content create a defensible moat without server costs. Indie developers targeting exotic pet niches report $2K–$8K/mo by Month 6–12 with Premium Tool monetization.

---

## Example 7 — Exotic Pet Care Tracker: Android Greenfield (`chinchilla care`)

```
TITLE:    Chinchilla Care — Health, Diet & Cage Tracker
SHORT DESC: Daily care log for chinchilla owners

FULL DESC:
The only app built exclusively for chinchilla owners. Log daily dust
baths, pellet meals, hay intake, and weight trends. Set temperature &
humidity alerts for their sensitive habitat. Track vet visits with
photo attachments. Get seasonal care reminders.

• Weight & diet tracker with trend graphs
  — chinchilla diet, exotic pet health, small pet tracker
• Dust bath schedule & fur condition log
  — chinchilla bath tracker, fur care log, pet grooming
• Temperature & humidity alerts (chinchillas overheat at 26°C+)
  — habitat monitor, cage conditions, pet environment
• Vet visit log with photo attachments & medication tracker
  — pet medical diary, exotic vet records, chinchilla health
• Breeding & pedigree management
  — chinchilla breeding, pedigree tracker, pet genealogy
• Widget for quick daily logging without opening the app
  — pet care widget, daily tracker widget, small pet

No account required. Your chinchilla data stays on your device.
Optional cloud sync via Google Drive backup.

#chinchilla #exoticpet #smallpet #petcare #chinchillalove
#pettracker #chinchillalife #rodentcare #exoticpetcare
```

**Android discovery context (2026-05-03)**:
- Play Store search `chinchilla care` returns **zero dedicated care apps**
- Top result: `My Chinchilla` (Tamagotchi-Spiel, 10K+ DL) — a game, not a utility
- Remaining results: sound-effect apps, unrelated "care" apps
- iOS cross-check: `Chinchilla Care — Photo Log` (0 reviews), `ChinchillaTrack` (0 reviews) — also empty
- Reddit: r/chinchillas has 70K+ members with daily health/diet questions and no app recommendations
- **Verdict**: True Greenfield / Erst-Entdecker-Chance on both Android and iOS

**Tech recommendation**: Flutter + Supabase (cross-platform simultaneously). Native Android (Kotlin + Room) is acceptable for a fast MVP, but iOS should follow within 30 days.

**Monetization**: Freemium (1 chinchilla, 30-day history) → Pro unlock $3.99/mo or $29.99 lifetime.

---

## Example 8 — Exotic Pet Care Tracker: iOS Hidden Gem (`leopard gecko`)

```
TITLE:    Leopard Gecko — Care, Morphs & Health
SUBTITLE: Daily care log for leopard gecko owners

DESC:
The only app built exclusively for leopard gecko owners. Log meals,
weights, shed cycles, and habitat temperatures. Identify your gecko's
morph with the built-in genetics guide. Set calcium-dust and UVB
reminders. Track vet visits with photo attachments. Join thousands of
leo owners keeping their pets healthy.

• Weight & feeding tracker with calcium-dust logging
  — leopard gecko diet, reptile health log, exotic pet tracker
• Shed cycle countdown & photo timeline
  — reptile shed tracker, skin cycle monitor, pet molt log
• Morph encyclopedia with 100+ morphs & genetics calculator
  — leopard gecko morphs, reptile genetics, morph calculator
• Temperature & humidity habitat alerts
  — gecko habitat monitor, reptile enclosure, pet environment
• Vet visit log with photo attachments & medication tracker
  — reptile vet records, pet medical diary, leopard gecko health
• Widget for quick daily logging without opening the app
  — reptile care widget, daily tracker widget, pet log

No account required. Your gecko data stays on your device.
Optional iCloud backup.

#leopardgecko #reptilecare #exoticpet #gecko #petcare
#pettracker #reptile #geckolove #lizardcare #exoticpetcare
```

**Discovery context (2026-05-12)**:
- RespecASO v2.9.0: `leopard gecko` → Pop 27 / Diff 20 / Opp 30 (💎 Hidden Gem)
- Ranking ramp confirmed: T5=27, T20=22 — long-tail entry possible
- iTunes Search API: Top results are virtual pet games + generic reptile trackers. Zero dedicated care utility.
- Play Store cross-check: Same pattern — games dominate, no dedicated care app.
- Reddit: r/leopardgeckos ≈180K members, daily Q&A. No app recommendations in threads.
- **Verdict**: 🟢 BUILD — True Hidden Gem on iOS. Root keyword outperforms long-tail.

**Tech stack**: SwiftUI + SwiftData + CloudKit + RevenueCat. Single-device subscription model. No backend needed.

**Monetization**: 7-day trial → Read-Only after expiry → $4.99/mo, $29.99/yr, $79.99 lifetime.

**Revenue anchor**: Comparable exotic pet trackers (hedgehog, chinchilla) estimate $2.5K–$8K MRR after 6–12 months.

---

## Example 9 — AI Poem Generator (`poem generator`, `ai poem generator`)

```
TITLE:    PoemCraft AI — Poem Maker
SUBTITLE: Write love & funny poems fast

DESC:
Stuck on the perfect poem? PoemCraft AI turns your idea into a beautiful,
personalized poem in seconds. Choose your mood — romantic, funny, sad, or
inspiring — pick a style like haiku or sonnet, and let AI craft the words
for birthdays, weddings, anniversaries, apologies, and more.

• AI poem generator — from idea to finished poem in seconds
   — poem generator, ai poem generator, poetry writer, poem maker
• Mood picker: romantic, funny, sad, inspiring, rhyming
   — love poem generator, funny poem maker, sad poetry, rhyming poems
• Occasion picker: birthday, wedding, anniversary, apology, farewell
   — birthday poem, wedding vows poem, anniversary poem, sorry poem
• Style selector: haiku, sonnet, free verse, limerick, acrostic
   — haiku generator, sonnet writer, limerick maker, acrostic poem
• Save, share & copy — export to Notes, Messages, or social
   — share poems, copy poem text, poem export
• Works offline — generate poems anywhere, anytime
   — offline poem writer, no internet poem app

#poemgenerator #aipoetry #lovepoems #poetrywriter #aipoems
#birthdaypoem #weddingpoem #poetryapp #creativewriting #sonnet
```

**Revenue anchor**: AI text-generation micro-apps typically land $1.2K–$4K MRR within 60–90 days when the primary keyword has Popularity >60 and Difficulty <35.

**Competitive dynamic**: "Strong Traditional vs. Weak AI Incumbent" pattern. When a category has a dominant traditional tool that does NOT offer AI generation, and weak AI-native apps exist with <200 reviews, the opportunity is a premium AI wrapper with better UX. Traditional app's audience is not the AI wrapper's audience — they are complementary.

**Tech stack**: iOS 16.0+, SwiftUI, SwiftData/CloudKit, RevenueCat. No backend needed if using on-device `NaturalLanguage` + template/prompt logic. For GPT-based generation, a lightweight serverless endpoint keeps costs near-zero at low scale.

**Monetization**: 7-day trial → Read-Only after expiry → $4.99/mo, $29.99/yr, $79.99 lifetime.

---

## Exotic Pet Care Trackers — Repeatable Indie Pattern

This class of niche is a **particularly strong Indie Wrapper** because of a structural gap: large communities of passionate pet owners exist on Reddit, TikTok, and Instagram, but no incumbent app owns their workflow.

| Keyword | Popularity | Difficulty | Insight | Reddit/Community Signal |
|---------|-----------|-----------|---------|------------------------|
| `hedgehog` | 90 | 42 | ✅ Good Target | 80K+ members, daily health/diet posts |
| `axolotl` | 89 | 48 | ✅ Good Target | 200K+ r/axolotl, viral pet content |
| `chinchilla` | 76 | 24 | 🎯 Sweet Spot | Passionate but underserved |
| `tarantula` | 59 | 25 | 🎯 Sweet Spot | Reptile community, extreme loyalty; 1 strong all-rounder but zero deep-specialist trackers |
| `reptile care` | 45 | 25 | 🎯 Sweet Spot | Broad niche, fragmented tools |
| `leopard gecko` | 27 | 20 | 💎 Hidden Gem | 180K+ r/leopardgeckos, daily Q&A; no dedicated care app |
| `ferret` | 69 | 31 | 🎯 Sweet Spot | 47K+ r/ferrets, daily care/feeding Q&A; no dedicated ferret-only tracker |

> **Note**: For the live "already built" list, see the dynamic Pre-Flight Step 0 in SKILL.md — `gh repo list datasteviee` is the source of truth, not this table.

### Root vs. Long-tail Pattern (confirmed multi-species)

Always test the **root noun** (species name) alongside the `X care` long-tail:

| Keyword | Popularity | Difficulty | Verdict |
|---------|-----------|-----------|---------|
| `chinchilla` | 76 | 24 | ✅ Root wins |
| `chinchilla care` | 23 | 10 | Long-tail underperforms (🔍 Low Volume) |
| `leopard gecko` | 27 | 20 | ✅ Root wins |
| `leopard gecko care` | 18 | 15 | Long-tail underperforms |
| `axolotl` | 89 | 48 | Root wins |
| `axolotl care` | ~15 | ~10 | Long-tail underperforms |

**Action**: In every exotic pet batch, include the root noun. If the root scores well and the long-tail is Low Volume, build the pitch around the root keyword but keep the word "Care" in the subtitle to capture both search intents.

### Why these work as a portfolio
1. **Zero incumbent**: No dominant app for any single species.
2. **High owner attachment**: Exotic pets are expensive; owners spend on care.
3. **Content moat**: Care knowledge is specialized and scattered. Building a verified care database provides defensibility.
4. **Zero server costs** if built as a privacy-first, CoreML + SwiftData + CloudKit app.
5. **Portfolio repeatability**: The same architecture maps directly across species.

### Monetization model that fits
- 7-day full-featured trial → Read-Only after trial (user sees data but cannot add new entries)
- Monthly $4.99 / Yearly $29.99 / Lifetime $79.99
- **Premium Tool** model, not Freemium. No "free tier" that limits entries.
- The first paid step is psychologically linked to the user's goal (their pet's health), increasing conversion.

### Validation without surveys
Instead of building a landing page and waiting for signups, search the relevant subreddit. If you see daily posts asking "Is this normal?", "What should I feed?", or "Vet recommendations?", you have validated demand.

### Reddit → product pipeline
1. Spend 2–3 hours reading the "new" and "top" of the last year in the relevant subreddit.
2. Compile the top 10 pain points into a Markdown file — this becomes your content and feature list.
3. Train a CoreML image classifier on publicly-shared Reddit photos for common health conditions.
4. Build the app, launch with In-App Events targeting the subreddit community.
