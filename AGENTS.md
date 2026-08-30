# AGENTS.md — instructions for any AI agent working in this repo

This is a personal training/nutrition log, maintained by talking to an AI agent
(Claude, Google Jules, or others) instead of a dedicated app. Multiple different
agents may work on this repo at different times — **always assume you are not
the only one.** The rules below exist because specific mistakes have already
happened; each one names the failure it prevents.

`README.md`, `NUTRITION.md`, `ROUTINE.md`, and `PROGRESS.md` explain *why* the
project works the way it does. This file is the procedural checklist for *how*
to make an edit without breaking something — read it before touching any file,
every session, even if you've edited this repo before.

## 0. Git workflow & branching (MANDATORY BEFORE & AFTER EDITS)

This repository is maintained across multiple environments and agents (e.g., Claude Code directly on GitHub, Antigravity or local agents in a local clone). To prevent split-brain states and lost data:

### 0a. Branch policy: Always `main`, never branch
- **Never create or switch to feature branches.** All edits belong directly on `main`.
- Ensure you are on `main`:
  ```bash
  git checkout main
  ```

### 0b. Before you read or write anything (Pre-flight sync)
- **Pull latest changes from GitHub immediately:**
  ```bash
  git pull origin main
  ```
  Another agent (e.g., Claude Code) may have committed since your session started or since your context was built. Never assume local files are current without pulling first.
- **Read the exact section you're about to edit, fresh**, not from memory of an earlier turn or cached context.

## 1. Repeated items are normal — don't "fix" them

If the same food or product appears on two different days in `meals/`, that is
the user eating the same thing twice, not a duplicate log entry. **Never merge,
delete, or re-date an entry because it matches an earlier day** — only remove
or change a log entry if the user explicitly says it was logged wrong. When in
doubt whether something is a real repeat vs. an actual mistake, ask; don't decide
silently either way.

## 2. Dates: Mountain Time, no exceptions

Log every entry under the actual current date **in Mountain Time**
(`TZ=America/Denver date`), never the date/timezone of your own runtime, and
never inferred from phrasing like "last night" or "this morning." Only use a
different date if the user explicitly asks for one (e.g. "log this for
yesterday"). See `README.md` → Workflow for the full policy.

## 3. Meal entries — required format

Every food line needs: description, kcal, protein, fat, carbs, and a source
tag in parentheses:
- `(exact, from label)` / `(official <brand> nutrition info, exact)` — read
  directly off a label or official source
- `(estimate — <one-line reasoning>)` — no label available; say what the
  estimate is based on

After every new item, recompute and append `**Daily total so far: ...**`;
switch to `**Daily total: ...**` (no "so far") only once you're sure the day
is over — check the current Mountain Time before closing a day out, since a
later entry can still land on that same date.

See `NUTRITION.md` for targets, staple-product defaults, and portion
conventions (e.g. banana peel discounted at USDA's 36% refuse figure).

### 3a. Past meal history context & explicit assumptions

- **Check previous meal logs for unstated context:** When a user logs a food or beverage with incomplete product details (e.g., "milk", "fairlife shake", "burrito", "chicken bites"), **do not** guess generic USDA items or pick an arbitrary brand SKU. Check `NUTRITION.md` ("Preferred Staples") and recent entries in `meals/` (especially the current month) to see what exact product, brand, or serving basis the user normally consumes, and use that established baseline.
- **Clearly state all assumptions in the response:** In the final response to the user after logging, **always explicitly state any assumptions made** (e.g., which brand/product variant was assumed from past history, estimated portion/volume, cooking oils/butter, etc.). This ensures full transparency and allows the user to immediately catch and correct anything that differed.


## 4. Workout entries — required format

- Heading format: `## YYYY-MM-DD — <Session Type>` — always include the
  session type/day label (e.g. `Gym (Day 2)`, `Zone 2 Run (Cardio)`), never a
  bare date. If a gym session's exercise pairing matches one of the Day 1–5
  templates in `ROUTINE.md`, name it (e.g. `Day 4 — Extra Volume`); if it's a
  genuine mix, say so explicitly rather than leaving the type blank.
- RIR default is **0** (near-failure) unless the message says otherwise — see
  `PROGRESS.md`'s RIR history for why. A "failed Nth rep" set uses the reps
  *actually completed* (N−1) in the Epley formula, not N.
- e1RM formula: `weight × (1 + reps/30)` (Epley). Reliable to ~12 reps; past
  that, treat the result as directional and downgrade confidence accordingly
  (see existing entries in `PROGRESS.md` for the tone/format to match).

### 4a. The gym-comparability rule — mandatory, no exceptions

This user regularly switches between gyms/machines, and a pin-loaded machine
at 300lb can mean something completely different from one gym to the next.
**Before recording any new e1RM as validating or replacing an existing PR:**

1. Compare it to the *most recently confirmed* number for that exact
   exercise in `PROGRESS.md` (not just the single highest number on file —
   read the full history in that row).
2. If the new number is dramatically higher or lower (as a rule of thumb,
   more than ~15–20%) than a baseline that's been **confirmed more than
   once**, and the session didn't note a gym/machine change, **do not**
   assert the new number invalidates the old one. Instead:
   - Record the new data point as logged (never discard real performance
     data).
   - Add a flag, matching the tone of existing flagged entries in
     `PROGRESS.md` (search for "⚠️" in that file for examples) — name the
     specific prior baseline being contradicted and by how much, and say
     what would confirm or contradict it (typically: a follow-up session at
     a known gym).
   - Carry the same flag into the matching `ROUTINE.md` suggested-weight
     line(s) — **every** occurrence of that exercise (an exercise can appear
     on more than one day, see §5).
3. **Never delete, soften, or silently overwrite an existing caveat** written
   by a previous session — if new evidence changes the picture, add to the
   history, don't erase it.

This exists because `ROUTINE.md`'s suggested weights are numbers the user
actually loads onto a machine at the gym next session — an unverified inflated
number is a real injury/frustration risk, not just a data-quality nit.

## 5. Every markdown edit requires a *complete* dashboard sync

`dashboard.html` mirrors `GOALS.md` / `ROUTINE.md` / `NUTRITION.md` /
`PROGRESS.md` / `meals/*.md` / `workouts/*.md`. A **partial** sync is worse
than no sync — it makes the dashboard internally contradict itself, which is
harder to spot than a stale-but-consistent dashboard. On every commit that
touches any source file, update **all** of the following in `dashboard.html`:

- [ ] `NUTRITION_LOG` JS object — add/edit the day's item array
- [ ] `TODAY` — the current Mountain Time date
- [ ] Workout Log section — one new HTML block per session, in the same
      `<div class="callout info">...</div>` format as every existing entry
      (do not introduce a different structure like a bare `<ul><li>` list —
      grep the file for `class="callout info"` and match it exactly)
- [ ] Estimated 1RMs table — every exercise row whose `PROGRESS.md` line
      changed, including confidence label and any ⚠️ flag
- [ ] **Every** Routine-tab card that mentions the changed exercise — some
      exercises appear on more than one day (e.g. Leg Extension is on both
      Day 2 and Day 4). Grep the exercise name in `dashboard.html` after
      editing and confirm every occurrence changed, not just the first one
      you found.
- [ ] "What's Logged So Far" stat tiles — Workout Entries and Meal Entries
      counts (count real `## ` session headings in `workouts/*.md`,
      excluding the format-template header and untracked/vacation
      placeholders; count `NUTRITION_LOG` array items for meals)
- [ ] Header `sync-note` "last updated" date

**Before ending your turn**, grep the file(s) you just edited for the
exercise/date/value you changed and confirm every match was updated — not
just the first one you found.

## 6. Repo hygiene

- Never commit tool/runtime artifacts: `server.log`, `*.pyc`,
  `__pycache__/`, `node_modules/`, editor swap files, etc. `.gitignore`
  covers the common cases — extend it rather than committing around it. If
  you ran a local server or script to preview `dashboard.html`, clean up
  before committing.
- A commit should only touch files that are actually part of the logged
  change.

## 7. Coverage / gaps

See `README.md` → "Logging coverage and gaps" for the partial/untracked-day
rules (a day can be `partial`, `untracked`, or default `complete`) — get this
right before computing any average, total, or trend across multiple days.

## 8. Commit messages & post-flight push (MANDATORY AFTER EDITS)

- **Commit message format:** Describe what was logged/changed in the first line (e.g. `Log 8/22 dinner: pasta, chicken bites, sauce`), not a generic message. If you touched `PROGRESS.md`/`ROUTINE.md` because of a PR or a flagged anomaly, say so.
- **Stage and commit only relevant files:**
  ```bash
  git add <modified-files>
  git commit -m "<descriptive message>"
  ```
- **Push immediately before ending turn:**
  Every local agent must push to `origin main` before concluding the turn:
  ```bash
  git push origin main
  ```
- **If push is rejected (concurrent remote commit):**
  Another agent (e.g., Claude Code) pushed to GitHub while you were working. Run:
  ```bash
  git pull --rebase origin main
  git push origin main
  ```
  Never force push (`--force`). If there is a merge conflict during rebase, resolve it cleanly (e.g., keep both meals or workout entries if both are valid) and complete the rebase and push.

## 9. Always provide the Dashboard link

Whenever you log anything (meals, workouts, weigh-ins, measurements, routine updates, etc.) or touch any source files, **always provide the direct link to the GitHub Pages dashboard in your final response**:

🔗 **Dashboard:** [https://matthewveit2000.github.io/eleanors-fitness-coach/dashboard.html](https://matthewveit2000.github.io/eleanors-fitness-coach/dashboard.html)

## 10. Post-logging coaching feedback (concise, proportional & actionable)

After logging any entry (meal, workout, weigh-in, etc.), provide a short, high-value coaching response. **Keep it concise, actionable, and proportionate to the size of the log** — never generic cheerleader fluff, and never mechanically forcing comments on things that don't warrant them. Use agent judgment to highlight what is actually useful for the user right now.

### 10a. Scale feedback proportionally to the log
- **Small log (e.g., protein shake, piece of fruit, single snack):**
  - Keep it very brief (1–2 punchy sentences).
  - Focus on immediate pacing (e.g., how it contributes to protein/calorie targets so far, or what remains for upcoming meals).
- **Medium log (e.g., a full meal like lunch or dinner):**
  - Slightly more detailed (a short paragraph or bulleted takeaways).
  - Address current macro balance and give forward-looking guidance for the rest of the day (e.g., calorie budget remaining, what macro to prioritize at dinner or evening snacks, hydration/activity balance).
- **Large log (e.g., full workout session, weigh-in + measurements, major multi-meal day):**
  - More thorough coaching breakdown.
  - Highlight trends, compare to prior sessions or baselines (e.g., PRs, volume progression, weight stability/fluctuations), reflect on execution across the past few days, and suggest recovery or fueling strategies for the rest of the day.

### 10b. Core coaching dimensions (use judgment, don't force all of them)
Select only the most relevant dimensions for the situation:
- **Trends & historical comparisons:** Connect new data points to recent history (e.g., "Third time matching 175lb on cable rows," "Weigh-in continues the flat ~165lb trend," "Protein is pacing ahead of schedule today").
- **Encouragement & reflection:** Acknowledge genuine consistency, progress, or solid execution (e.g., clean recovery after a high-calorie event or travel) with a grounded, coach-like tone rather than hollow cheerleading.
- **Actionable advice for the rest of the day:** Provide practical next steps (e.g., "You have ~850 kcal and 45g protein left — aim for a lean protein dinner with plenty of fiber and carbs," or "Heavy leg session completed; prioritize post-workout hydration and keep evening carbs high to replenish glycogen").
- **Agent judgment over robotic checklists:** Do not output a rigid template for every log. Tailor the feedback to what genuinely matters based on current time of day, remaining budget, and recent training.

### 10c. Eleanor's Specific Coaching Persona & Nurse Shift Protocol
- **Tone:** Encouraging & Grounded. Supportive, positive, recognizing real-world fatigue, zero hollow cheerleading.
- **Feedback Length:** **Punchy 1–2 sentences** (e.g. *"Breakfast logged: 380 kcal, 36g protein. You have 1,620 kcal and 94g protein left for your day!"*).
- **The Nurse Shift Rule (6 ON / 8 OFF):** Eleanor works 12-hour hospital shifts (6 days in a row on, 8 days off). On shift days, 10,000–15,000 floor steps count as active cardio. Never nag or guilt her about missing workouts on shift days. Volume is loaded into her 8 days off.
- **Zero Food-Prep Protocol:** Eleanor does not food prep. When logging or providing suggestions, always champion zero-prep convenience staples (Fairlife shakes, lactose-free milk, hard-boiled eggs, rotisserie chicken, Greek yogurt) rather than complex recipe meal prep.

