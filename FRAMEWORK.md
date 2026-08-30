# Autonomous Multi-Agent AI Fitness & Nutrition Coach: Framework Specification

## 1. System Overview & Architecture

### 1.1 Philosophy: Conversational Simplicity with Engineering Rigor
Traditional fitness applications force athletes into rigid database forms, barcode lookups, and static workout templates. This system replaces dedicated mobile apps with an **autonomous, version-controlled conversational framework** operated by LLM agents (Claude, Jules, Antigravity, etc.).

- **Frictionless Input:** The athlete logs meals, workouts, scale readings, and fatigue signals purely in plain natural language or by uploading photos (food plates, nutrition labels, cardio monitor summaries).
- **Evidence-Based Backend:** The AI acts as a sports scientist and dietitian, translating casual qualitative inputs into quantitative metrics: macro tracking, volume landmarks (Dr. Mike Israetel / Renaissance Periodization), estimated 1-rep maxes (Epley with fatigue discounting), and dynamic calorie calibration.
- **Git as the Single Source of Truth:** Every log entry, PR update, and routine adjustment is committed to a Git repository on `main`. Markdown files provide an auditable, transparent history that can never be locked behind a proprietary app paywall.
- **Instant Client-Side Visualization:** A single-file, zero-dependency `dashboard.html` mirrored to GitHub Pages provides an interactive web dashboard (daily macro runway, volume audit bars, moving-average weight charts, and 1RM leaderboards).

```
┌─────────────────────────────────────────────────────────────┐
│                       ATHLETE / USER                        │
│  "Had 3 eggs & toast" · [Photo of label] · "Leg press 495x15"│
└──────────────────────────────┬──────────────────────────────┘
                               │ Chat / Images
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                    AI COACHING AGENT                        │
│   (Claude Code / Jules / Antigravity / Any LLM Interface)   │
├─────────────────────────────────────────────────────────────┤
│ 1. Pre-flight git pull origin main (Multi-agent sync)       │
│ 2. Timezone validation (Mountain Time / Athlete TZ)         │
│ 3. Quantitative estimation (USDA, nutrition labels, Epley)  │
│ 4. Scientific audit (RP Volume landmarks, RIR defaults)     │
│ 5. Multi-file atomic update & complete dashboard.html sync  │
│ 6. Post-logging proportional coaching feedback              │
│ 7. Post-flight git commit & push origin main                │
└──────────────────────────────┬──────────────────────────────┘
                               │ Commits & Pushes
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                      GITHUB REPOSITORY                      │
├──────────────────────────────┬──────────────────────────────┤
│ Markdown Core Files:         │ Single-Page Web Dashboard:   │
│ - GOALS.md                   │ - dashboard.html             │
│ - NUTRITION.md               │   (Auto-deployed via         │
│ - ROUTINE.md                 │    GitHub Pages)             │
│ - PROGRESS.md                │                              │
│ - meals/YYYY-MM.md           │                              │
│ - workouts/YYYY-MM.md        │                              │
│ - AGENTS.md                  │                              │
└──────────────────────────────┴──────────────────────────────┘
```

---

## 2. Multi-Agent Concurrency & Operational Rules

Because multiple distinct AI agents may maintain the repository across different devices and sessions, strict operational guardrails are enforced in `AGENTS.md`:

### 2.1 Git Synchronization Protocol
- **Always `main`, Never Branches:** To prevent split-brain states and untracked merge conflicts, all agent work is performed directly on `main`.
- **Pre-Flight Pull:** Every session **must** run `git pull origin main` before reading files or writing code. Never assume cached context is current.
- **Post-Flight Push:** Every session **must** stage modified files, create a descriptive commit message (`git commit -m "Log 8/29 dinner: ..."`), and push immediately before ending the turn. If rejected due to remote updates, the agent runs `git pull --rebase origin main` and resolves cleanly.

### 2.2 Date & Timezone Integrity
- **Athlete Timezone Primacy:** All timestamps and daily entries are pegged strictly to the athlete's local timezone (Mountain Time, `America/Denver`).
- **No Conversational Inference:** Never assume the agent's UTC runtime date or infer dates from ambiguous terms like "last night" or "this morning." Always inspect the actual Mountain Time date (`TZ=America/Denver date`) unless the user explicitly commands a retroactive log (e.g., "log this for yesterday").

### 2.3 Idempotency & Repeat Logging
- If an identical meal or workout item appears multiple times on the same or consecutive days, it is **never** treated as a duplicate or merged. It represents repeated consumption or training.

### 2.4 Complete Dashboard Synchronization (100% Rule)
`dashboard.html` mirrors the markdown files. Partial syncs corrupt dashboard consistency. Every commit touching any source file must synchronize:
1. `NUTRITION_LOG` JS object with individual meal line items.
2. `TODAY` date variable.
3. Workout Log callout HTML blocks.
4. Estimated 1RMs table & routine cards.
5. Stat grid counters (Workout Entries, Meal Entries, Lift PRs, Weigh-ins).
6. Sync header timestamp.

---

## 3. Scientific Methodology & Training Principles

### 3.1 Hypertrophy Volume Landmarks (Dr. Mike Israetel / Renaissance Periodization)
The training engine is organized around evidence-based weekly set volume for each discrete muscle group:
- **Direct Sets Only:** Volume is evaluated strictly by direct working sets taken within 0–3 RIR. Synergist muscles do not receive fractional credit (e.g., bench press counts toward Chest, but gives 0 fractional sets to Triceps; vertical rows count toward Back, giving 0 fractional sets to Biceps). This maintains clarity and avoids artificial volume inflation.
- **Volume Landmarks:**
  - **MEV (Minimum Effective Volume):** The lowest weekly direct volume to stimulate measurable hypertrophy (typically 6–10 sets/week). Days 1–3 in the training split must hit MEV across all tracked muscle groups.
  - **MAV (Maximum Adaptive Volume / "Optimal"):** The volume range yielding optimal hypertrophy (typically 10–20 sets/week). Day 4 acts as an additive volume booster to elevate muscle groups into MAV.
  - **MRV (Maximum Recoverable Volume):** The volume ceiling beyond which recovery fails and systemic fatigue degrades adaptations.
- **Evaluation Window:** Monday through Sunday calendar week (hard reset each Monday), avoiding rolling-window distortions.

### 3.2 Load Prescription & Fatigue Modeling
1. **Epley Formula:**
   $$\text{e1RM} = \text{weight} \times \left(1 + \frac{\text{reps}}{30}\right)$$
   - Validated for sets of 1–10 reps.
   - For 11–15 reps, accuracy degrades; the result is marked as **Medium confidence** or **Directional**.
   - For >15 reps, it serves as a rough trend indicator only.
2. **Effort Baseline (0 RIR Default):**
   - For serious, high-intensity athletes who regularly train to concentric failure, the default assumption for unstated set effort is **0 RIR (near failure)**. Assuming higher reserve (e.g., 2 RIR) artificially inflates e1RM and leads to dangerous, unachievable working weight suggestions.
   - Set failures (e.g., "failed on rep 10") calculate e1RM using the completed reps ($N-1 = 9$).
3. **90% Training Max Haircut for Suggested Working Weights:**
   - Single-set formulas assume fresh conditions. Real training involves 3–4 consecutive sets superset within a 60-minute session.
   - Suggested weights calculate from a **Training Max** equal to $90\%$ of e1RM:
     $$\text{Suggested Weight} = \frac{0.90 \times \text{e1RM}}{1 + \frac{\text{reps}}{30}}$$
   - Rounded to practical gym increments (2.5 lb or 5 lb).

### 3.3 The Gym Comparability Rule
Pin-loaded machines, cable pulleys, and leverage arms vary wildly across gyms (e.g., a 200 lb stack on a 2:1 pulley feels like 100 lb; cam profiles alter resistance curves).
- **Rule:** When an athlete logs an e1RM that is >15–20% higher or lower than a confirmed baseline without a noted gym change:
  - Do **not** overwrite the confirmed baseline or assume rapid strength changes.
  - Record the data point but flag it with warning markers (⚠️).
  - Track gym/machine variants as distinct items in `PROGRESS.md` until verified.
  - Protect the suggested weight in `ROUTINE.md` from unsafe inflation.

### 3.4 Logging Coverage & Gap Taxonomy
To preserve analytical correctness without requiring obsessive 365-day tracking:
- **`complete` (Default):** All food and training logged. Included in all averages, moving averages, and calorie-balance math.
- **`partial`:** Day logged, but with a known gap (e.g., uncounted dinner, social event). Totals act as a **floor**, not a true total. Excluded from average intake and calorie reconciliation.
- **`untracked`:** Planned vacation or travel. Excluded completely. **Never treated as zero calories or zero training.**
- **Post-Gap Caution Window:** Travel disrupts sodium, water, and glycogen. Any weigh-in within ~3 days of returning from an untracked stretch is flagged as **Not a Trend Point**. Require 3–5 days of normal routine before recalibrating nutrition targets.

---

## 4. Dynamic Nutrition Calibration Engine

### 4.1 Starting Baseline
- **Maintenance (TDEE):** Calculated via Mifflin-St Jeor formula or calibrated with wearable metabolic expenditure data (WHOOP, Apple Watch, Garmin).
- **Target Deficit/Surplus:**
  - Moderate Recomp / Cut: Mild 8–15% deficit (~200–350 kcal/day below maintenance) to maximize muscle retention while shedding fat.
  - Protein: 0.8–1.0g per pound of total bodyweight (sufficient for maximal muscle protein synthesis in a deficit).
  - Fat: ≥0.3–0.4g per pound of bodyweight for endocrine/hormonal support.
  - Carbohydrates: Remaining calorie budget dedicated to glycogen replenishment and training performance.

### 4.2 The 2–3 Week Empirical Calibration Loop
Population formulas are merely starting hypotheses. Real scale trends govern adjustments:
1. Athlete tracks weigh-ins under identical conditions (morning, post-void, pre-food/water).
2. Every 2–3 weeks across complete logging windows:
   - Weight loss between 0.5%–1.0% BW/week: **On target. Maintain current calories.**
   - Weight flat / stalled: **Reduce intake by 100–150 kcal/day.**
   - Weight dropping >1.0% BW/week (muscle loss risk): **Add 100–150 kcal/day back.**
3. Calibration changes are logged chronologically in `PROGRESS.md` with explicit scientific rationale.

### 4.3 Conversational Food Logging & Source Tagging
Every logged food item must cite its provenance:
- `(exact, from label)`: Directly read from a package nutrition panel.
- `(official <brand> nutrition info, exact)`: Retrieved from restaurant or manufacturer published PDFs/databases.
- `(estimate — <one-line reasoning>)`: Derived from USDA reference food databases or visual portion estimates.
- **Assumption Transparency:** Agents must state all assumed product variants, serving sizes, and cooking fats in their final chat reply so the user can easily correct deviations.

---

## 5. Proportional Coaching Feedback Rubric

AI coaching responses after an entry must be **concise, actionable, and proportionate to the size of the log** (never generic cheerleader fluff):

| Log Type | Proportionality | Key Coaching Dimensions | Example Response Focus |
|---|---|---|---|
| **Snack / Shake** | 1–2 punchy sentences | Immediate pacing & macro runway | Remaining protein/calorie balance heading into the next meal. |
| **Full Meal (Lunch/Dinner)** | Short paragraph or bullets | Macro balance & evening strategy | Calorie runway left for dinner; adjustments for dietary fiber or carbs. |
| **Workout Session** | Comprehensive breakdown | Historical comparisons, PRs, volume audit | Compare to previous 1RMs; acknowledge PRs; advise on post-workout recovery fueling. |
| **Weigh-In / Weekly Review** | Thorough analytical review | Trend analysis & calibration check | Rate of weight change; post-travel caution checks; Mon–Sun RP volume audit. |

---

## 6. Repository Blueprint & File Roles

| File Path | Functional Purpose |
|---|---|
| `README.md` | High-level system overview, gap taxonomy, and timezone conventions. |
| `AGENTS.md` | Mandatory operational checklist for AI agents (git sync, formatting, dashboard sync, feedback rules). |
| `CLAUDE.md` | Lightweight instructions for Claude CLI sessions and Claude Artifact republishing. |
| `GOALS.md` | Athlete profile, aesthetic/performance goals, philosophy, injury guardrails, target metrics. |
| `NUTRITION.md` | Macro targets, starting TDEE logic, calibration protocol, preferred staples, and logging conventions. |
| `ROUTINE.md` | Training split, RP volume landmark tables, superset pairings, exercise selection, and e1RM load formulas. |
| `PROGRESS.md` | Chronological weigh-in log, PR tables, estimated 1RMs leaderboard, and nutrition calibration log. |
| `meals/YYYY-MM.md` | Monthly itemized food logs with running daily totals and source tags. |
| `workouts/YYYY-MM.md` | Monthly workout logs with set/rep/weight breakdowns, RIR, and coach feedback notes. |
| `dashboard.html` | Client-side reactive dashboard rendering today's runway, weekly audits, routines, and progress charts. |
| `FRAMEWORK.md` | This document: the master architecture and methodology specification. |

---

## 7. New Athlete Onboarding Guide (e.g., Eleanor)

To instantiate this framework for a new athlete, execute the following protocol:

### Step 1: Athlete Intake Profile
Collect the following baseline metrics:
- **Demographics:** Name, Age, Height, Current Bodyweight, Estimated Body Fat %.
- **Experience Level:** Years of lifting/training experience (determines rate of adaptation and recomposition potential).
- **Primary Goals:** Hypertrophy, body recomposition, fat loss, athletic conditioning, race/competition prep.
- **Biomechanical Guardrails:** Existing joint pains, disc issues, or movement contraindications (e.g., avoiding heavy spinal compression).
- **Equipment & Schedule:** Access to commercial gyms, free weights, or home setups; available training days per week (3–5 days).

### Step 2: Establish Nutrition Targets
1. Calculate baseline BMR and TDEE using Mifflin-St Jeor:
   $$\text{BMR} = 10 \times \text{weight(kg)} + 6.25 \times \text{height(cm)} - 5 \times \text{age} + s$$
   ($s = +5$ for males, $-161$ for females).
2. Set target deficit (e.g., 10–15% below TDEE for recomposition).
3. Set protein to 0.8–1.0g/lb of bodyweight.
4. Set dietary fats to 25–30% of total calories.
5. Set carbohydrates to fill the remaining energy budget.
6. Populate `NUTRITION.md` and `dashboard.html` `TARGETS` object.

### Step 3: Configure Training Split
1. Select compound and machine exercises aligned with the athlete's goals and joint tolerances.
2. Build an antagonist superset split (e.g., Chest ↔ Back, Quads ↔ Abs/Hams, Delts ↔ Arms) to minimize workout duration.
3. Verify that weekly direct sets hit **MEV** across Days 1–3 and **MAV** on Day 4 in `ROUTINE.md`.
4. Initialize suggested weights to `—` (populated automatically upon first workout log).

### Step 4: Reset & Launch
1. Ensure all previous athlete meal and workout files are cleared from `meals/` and `workouts/`.
2. Clear `PROGRESS.md` weigh-in and 1RM tables.
3. Reset `dashboard.html` state variables (`NUTRITION_LOG = {}`, `WEIGH_INS = []`, `DAY_STATUS = {}`, counters to 0).
4. Initialize the new Git remote repository and verify GitHub Pages deployment.
