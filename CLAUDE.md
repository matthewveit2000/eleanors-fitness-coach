See [`AGENTS.md`](AGENTS.md) — the procedural checklist for logging format, the
dashboard-sync requirements, and the gym-comparability rule for PRs applies to
every agent maintaining this repo, Claude included. Read it before making any
edit.

Claude-specific addition: this repo's `dashboard.html` is published as a
Claude Artifact. `AGENTS.md` §5 covers keeping `dashboard.html`'s *content*
correct; `README.md` → Workflow covers the additional step only Claude can
do — calling the `Artifact` tool to republish it to the same URL in the same
turn a source file changes.
