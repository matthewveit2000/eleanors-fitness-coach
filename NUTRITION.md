# Nutrition — Eleanor

_Last updated: 2026-08-29_

## Why this exists
Training provides the hypertrophic stimulus; nutrition determines whether the body builds muscle and sheds fat. Without consistent energy balance and adequate protein, recovery and body composition improvements stall.

## Targets (Starting Point — see Calibration below)

| | Target | Basis |
|---|---|---|
| Calories | **~TBD kcal/day** | Baseline maintenance (TDEE) minus modest ~10–15% deficit for recomposition |
| Protein | **~TBD g/day** | ~0.8–1.0g per pound of bodyweight (research-backed floor for muscle retention & growth in a deficit) |
| Fat | **~TBD g/day** | ~25–30% of daily calories for hormonal health and vitamin absorption |
| Carbs | **~TBD g/day** | Fills remaining calorie budget to fuel high-intensity lifting and cardio recovery |

**Calories are the primary lever for body composition.** Hit the protein target consistently to preserve lean mass, while staying within the daily calorie runway.

## Why a moderate deficit, not an aggressive cut
Aggressive deficits (>20–25%) sharply elevate cortisol, impair sleep, degrade training strength, and risk lean muscle loss. A modest 10–15% deficit combined with high protein and progressive overload allows steady fat loss while preserving or building lean muscle tissue.

## Calibration protocol (Empirical Feedback Loop)
Initial targets are mathematical estimates. Real weight-trend data over time determines actual adjustments:
1. Weigh in regularly under identical conditions (morning, post-void, pre-food/water) — logged in `PROGRESS.md`.
2. After 2–3 weeks across complete logging days, check the trend:
   - **Losing ~0.5–1% of bodyweight/week:** On track; maintain current intake.
   - **Weight flat / not moving:** Drop calories by ~100–150 kcal/day.
   - **Losing faster than ~1%/week:** High muscle-loss risk; add ~100–150 kcal/day back.
3. Re-adjust every 2–3 weeks rather than reacting to daily scale fluctuations (which are driven by sodium, water, and glycogen).
4. Training performance is an early indicator: if strength or gym stamina drops off noticeably, evaluate whether calorie intake is too low before waiting for the scheduled check-in.

## Logging — Flexible & Conversational
No tedious barcode scanning or manual food logging apps required. Simply describe what was consumed in chat or send a photo:
- **Describe it in chat:** ("had 2 eggs, avocado toast, and an iced latte") — the agent estimates calories, protein, fat, and carbs and logs it.
- **Send a photo:** Of the meal or the nutrition label. If a label is present, it is recorded directly as exact (`exact, from label`).
- **Restaurant / Brand lookups:** Whenever a restaurant or branded item is mentioned, the agent checks official nutrition sources before falling back to USDA estimates.
- **Itemized meal entries:** Multi-item meals are logged line-by-line with individual macros (never blended into a single opaque lump sum) so each food's nutritional cost remains transparent.
- **Running Daily Totals:** Every logged item automatically updates the running daily total against the targets.
- **Timezone:** Entries are logged under current Mountain Time (`TZ=America/Denver date`) unless explicitly requested otherwise.
- **Explicit Assumptions:** Every logging reply will clearly state any assumptions made (e.g. assumed brands, portion weights, cooking oils/butter) so corrections can be made frictionlessly.

## Preferred Staples
Default items and go-to products assumed when logging unless a description or photo specifies otherwise:
- _[e.g. Preferred protein powder / ready-to-drink shake]_
- _[e.g. Preferred milk / plant milk]_
- _[e.g. Preferred bread / snacks]_

## Standing Habits (Not logged in daily food totals)
- _[Document standing daily habits here, e.g. daily multivitamins, creatine monohydrate 5g, morning hydration/electrolytes]_
