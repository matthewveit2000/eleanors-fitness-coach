# Eleanor's Fitness Coach

Personal training log and coaching notes for Eleanor. Training mix: weightlifting, running/cardio, and bodyweight/conditioning. Primary goals: hypertrophy, body recomposition, general athleticism and health.

**AI agents working in this repo (Claude, Google Jules, Antigravity, or others): read [`AGENTS.md`](AGENTS.md) first.** More than one agent maintains this repo, sometimes in the same day — it's the procedural checklist (dashboard sync, gym-comparability rule for PRs, date/format conventions) that keeps them from silently contradicting each other or corrupting the log. For deep architectural details and sports science methodology, see [`FRAMEWORK.md`](FRAMEWORK.md).

## Structure

- `FRAMEWORK.md` — master system architecture, scientific foundations (RP volume landmarks, Epley math, calibration loops), and onboarding protocol
- `GOALS.md` — current goals, athlete profile, and target metrics, updated as they evolve
- `ROUTINE.md` — the current training plan/split and volume landmarks
- `NUTRITION.md` — calorie/macro targets and the calibration protocol
- `PROGRESS.md` — bodyweight, measurements, and lift/run PRs over time
- `workouts/` — one log file per month, individual workout entries go here
- `meals/` — one log file per month, meals logged from a chat description or photo
- `dashboard.html` — tabbed HTML dashboard (Overview/Goals/Routine/Nutrition/Progress) summarizing everything above; published as an artifact and regenerated **every single time** any source file changes (also available on [GitHub Pages](https://matthewveit2000.github.io/eleanors-fitness-coach/dashboard.html)) — including every individual meal or workout log entry, not just edits to `GOALS.md`/`ROUTINE.md`/`NUTRITION.md`/`PROGRESS.md`

## Workflow

Workouts get logged by telling the agent what happened in a session; it records the entry in the appropriate `workouts/YYYY-MM.md` file and updates `PROGRESS.md` when a PR or notable measurement comes up. Meals get logged the same way — describe what you ate or send a photo, and the agent estimates macros and records it in `meals/YYYY-MM.md`. See `AGENTS.md` for the exact format, post-logging coaching feedback rules (§10), and PR-verification rules this depends on.

**Dashboard republishing is not optional and not batched.** `dashboard.html` mirrors the markdown source files, so *every* edit to `GOALS.md`, `ROUTINE.md`, `NUTRITION.md`, `PROGRESS.md`, `workouts/YYYY-MM.md`, or `meals/YYYY-MM.md` — including a single logged meal or a single logged set — requires updating `dashboard.html` to match **completely**, not partially (see `AGENTS.md` → §5 for the full sync checklist; a partial sync is worse than none). `dashboard.html` is also mirrored to [GitHub Pages](https://matthewveit2000.github.io/eleanors-fitness-coach/dashboard.html), which updates automatically on push — any agent that can commit to `main` keeps that copy current just by committing correctly. A Claude session additionally has to call the `Artifact` tool to republish to the same artifact link (same URL, `force: true` if a conflict is reported), in that same turn, since `git push` alone doesn't update that separate live link — never say "dashboard's synced/updated/pushed" unless the `Artifact` tool was actually called that turn. Agents without Artifact access (e.g. Jules) aren't expected to do this step, but should still leave `dashboard.html` itself fully correct so the next Claude session can catch the artifact up without also having to fix the underlying data.

## Logging coverage and gaps

Not every day gets logged — vacations, travel, events, forgotten meals. Days are classified so that a gap costs the analysis **coverage** without costing it **correctness**. Every day is one of:

| Status | Meaning | How analysis must treat it |
|---|---|---|
| **complete** _(default)_ | Everything eaten/trained that day was logged | Counts normally in every average, total, and trend |
| **partial** | Logged, but with a known unlogged gap (an event, a skipped meal) | Totals are a **floor, not a total**. Excluded from intake averages and from all calorie-balance math. Still counts as a training day if a session was logged |
| **untracked** | Deliberately not logged at all (vacation, travel) | Excluded entirely. **Never treated as a zero-calorie or zero-training day** |

Statuses live inline in `meals/YYYY-MM.md` / `workouts/YYYY-MM.md` and in `dashboard.html`'s `DAY_STATUS` object. Anything absent from `DAY_STATUS` is complete.

### Rules that follow from the labels

1. **Never average a partial or untracked day into intake.** Report averages as _"X kcal/day across N complete days"_ and always name N — an average whose denominator is unstated is how a gap silently becomes a fake deficit.
2. **Calorie-balance reconciliation (intake vs. weight change) requires a contiguous run of complete days.** If the window contains a partial or untracked day, the reconciliation is invalid — say so and narrow the window instead of running it anyway. Weight change across untracked days cannot be attributed to logged intake in either direction.
3. **A weigh-in within ~3 days of returning from an untracked stretch is not a trend point.** Travel food, sodium, and disrupted routine inflate it with water weight. Record it, annotate it, and wait for a same-conditions weigh-in after ~3–5 days of normal eating before reading the trend or recalibrating targets.
4. **A week containing an untracked stretch is an off-week, not a volume miss.** Report that Mon–Sun audit as "off-week (vacation)" rather than flagging every muscle group as under-MEV — the prescription wasn't missed, it wasn't in effect.
5. **Don't re-baseline off the first session back.** Performance after a layoff is often down slightly from rhythm and fatigue rather than lost strength. The "PR only moves up" rule already protects the e1RMs; the same restraint applies to suggested weights — leave them where they are and let the second or third session back set the direction.
6. **State coverage whenever it's degraded.** Any analysis touching a window with gaps says so up front, rather than presenting a clean-looking number built on a partial denominator.

**Date handling for logs:** every entry gets logged under the date the message describing it was received. **Determine that date by checking the actual current date/time in Mountain Time (the user's timezone) — e.g. `TZ=America/Denver date` — and log under that date automatically, without confirming with the user.** (This replaces an earlier version of this policy that required confirming every date; as of 2026-07-30 the user asked to stop being asked and have dates just looked up in Mountain Time instead.) Never infer a date from conversational phrasing ("last night," "this morning," "today") when it conflicts with the actual Mountain Time date. Log something under a different date only when the user explicitly asks for it (e.g. "log this for yesterday") — otherwise always use today's actual Mountain Time date.
