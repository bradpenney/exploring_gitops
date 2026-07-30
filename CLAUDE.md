# CLAUDE.md

This file provides guidance to Claude Code when working with this repository.

**Note:** this file didn't exist before 2026-07-30 — it was created to document one specific cross-site standard (below) while doing SEO work here. It is not a full editorial-standards doc like the other `exploring_*` sites have; persona, tone, and quality-checklist rules for this site haven't been formally written down yet. Don't assume the absence of a rule here means "no rule" — check with Brad before inventing one.

## Site Structure

- `docs/day_one/` — GitOps paradigm for developers/new team members. Published.
- `docs/essentials/` — FluxCD for platform engineers setting it up. One article live (`deploying_the_edge_stack.md`, a step in the bradpenney.io `cluster-to-internet` pathway); more planned (Installing Flux, Bootstrapping, GitRepositories and Kustomizations) — see commented-out block in `mkdocs.yaml`.
- `docs/efficiency/`, `docs/mastery/` — planned, all draft, excluded from the build (`exclude.glob: efficiency/*`, `mastery/*` in `mkdocs.yaml`).
- Tier model matches the other `exploring_*` sites: Day One → Essentials → Efficiency → Mastery.

## Publishing

- Do NOT run git operations — the user handles all git and deploys.
- `poetry run mkdocs build --strict` IS allowed for testing/verification (updated 2026-07-30) — use it to confirm changes build cleanly. `mkdocs serve` is allowed too for a genuine need, but only on a non-default port (3000 is almost always occupied) and only as a short-lived test, never left running.
- New Essentials drafts must be added to the `exclude.glob` list individually (no blanket `essentials/*` glob — see the comment above it in `mkdocs.yaml`).
- Build check before considering an article publish-ready: `poetry run mkdocs build --strict` (per `README.md`) — but the user runs this, not Claude.

**Every tier needs its own overview page (mandatory, cross-site standard, added 2026-07-30):** before a tier's first article goes live, that tier must have a published `overview.md` linked at the top of its nav section — the tier's real SEO landing page, not just a homepage card linking straight to one article. **Status here:** Day One has one. Essentials was live (1 article) with none — fixed 2026-07-30, `essentials/overview.md` written and wired in, homepage link repointed at it.
