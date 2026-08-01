# CLI Reference

This table is transcribed directly from `gald3r --help` (run against this repo's own
source, 2026-07-26, `gald3r 0.2.0 build 03badb80c277`) — not written from memory or from
reading source code. Every verb also answers its own `--help`; that is always more
authoritative than this page if the two ever disagree; ask the tool, not this file.

## Getting started & health

| Verb | What it does |
|---|---|
| `gald3r setup` | Scaffold (or repair) a fresh `.gald3r/` project in `--root` (default: cwd) |
| `gald3r init` | Alias for `setup` — identical flags and behavior (verified: same `--help` text) |
| `gald3r onboard` | One-command onboarding into the linked gald3r ecosystem: scaffold → project-type pick → curated skill set → identity → parent/sibling linking |
| `gald3r doctor` | Diagnose the local install (python/build fingerprint/torch/providers/env/identity/daemon/world_tree/auth) |
| `gald3r selftest` | Run the install self-diagnostic ("gald3r is N% functional") |
| `gald3r sessions` | Read-only listing of local chat sessions for the current directory |
| `gald3r status` | Compiled project status report -- task/bug counts, completion %, awaiting-verification `release_hold` groups, dependency-blocked tasks, active milestone, WPAC topology gate (`--min-severity N`, `--json`) |
| `gald3r --version` | Print version + build fingerprint |

## Tasks, bugs, and project state

| Verb | What it does |
|---|---|
| `gald3r task add/list/show/update/next/ready` | Manage tasks (create, browse, claim, complete) |
| `gald3r task ac-check` | Tick an acceptance-criterion checkbox for a task (per-criterion attestation) |
| `gald3r task verify` | Record a reviewer verdict (`--pass` → completed, `--fail` → back to pending) |
| `gald3r task regenerate-index` | Rebuild `TASKS.md` from the on-disk task files |
| `gald3r task-sync-check` | Validate `TASKS.md` against the `tasks/` files |
| `gald3r bug` | Report and list bugs (same shape as `task`) |
| `gald3r db backfill/verify/rebuild` | SQLite state-layer maintenance — import files into the DB, check for drift, or regenerate files from the DB |
| `gald3r validate` | Validate `.gald3r/tasks|bugs` frontmatter (schema, status vocabulary, folder placement); `--fix` normalizes what's safely fixable |
| `gald3r verify` | Deterministic verification ladder for a task (the completion gate) |
| `gald3r schema-migrate` | Migrate a project's `.gald3r/` files forward to the current schema version (dry-run by default) |
| `gald3r inbox` | Absorb queued task/bug drafts from `tasks/inbox/` + `bugs/inbox/` into tracked state |
| `gald3r wishlist` | Mine a human-prose wishlist/intent doc into tasks (read-only against the source doc) |
| `gald3r idea` | Capture, list, review, and promote ideas (`IDEA_BOARD.md`) |
| `gald3r feature` / `gald3r subsystem` | Feature and subsystem data operations |
| `gald3r prd` | Manage PRDs (add/list/show/revise) |
| `gald3r dependency-graph` | Render the task dependency graph (Mermaid + analysis) from `gald3r.db` |
| `gald3r medic` | Self-heal and curation verbs for a project's `.gald3r/` tree |

## Agent work

| Verb | What it does |
|---|---|
| `gald3r chat` | Persistent chat REPL (resumes this directory's latest session) |
| `gald3r run "<message>"` | One-shot agent message (`--backend dev-echo` for an offline, no-provider smoke test; `--model provider:model` to pick a target; `--resume <id>` to continue a session) |
| `gald3r agent` | Author and run declarative agent specs |
| `gald3r go` | Claim the next pending task, run one agent iteration, mark awaiting-verification |
| `gald3r go-code T123` | Implement a specific task, then mark awaiting-verification |
| `gald3r go-review T123` | Review a task's acceptance criteria; mark completed (pass) or pending (fail) |
| `gald3r go-status` | Whether an active autopilot loop is alive, idle-waiting, or stalled |
| `gald3r autoclaim` | Autonomously claim and work pending tasks off the local board (`--online` for the world_tree board) |
| `gald3r swarm` | Launch and manage multi-agent swarm runs |
| `gald3r autopilot` | Manage the autopilot task queue (enqueue/status/run/loop) |
| `gald3r worktree` | Manage agent worktrees (create/report/remove/cleanup + lifecycle) |
| `gald3r claim` | Claim a task UUID against world_tree's atomic collision arbiter |
| `gald3r housekeep` | Safe controller `.gald3r/` housekeeping classifier |

## Providers & configuration

| Verb | What it does |
|---|---|
| `gald3r init-providers` | Write a starter `providers.yaml`, auto-discovering local Ollama/LM Studio/vLLM/llama.cpp models |
| `gald3r local-model` | Local-model hardware assessment + serve-command helpers |
| `gald3r config` | Inspect or modify agent configuration |
| `gald3r profile` / `gald3r user-profile` | User profile and preferences (global + per-project) |
| `gald3r pers` | Manage personality overlay packs (list/pick/add/del/save) |
| `gald3r skill-pack` | Manage optional skill packs (add/del/list/save) |
| `gald3r skills-lock` | Read/write/verify the skills anti-tamper lockfile |
| `gald3r plugin` | Manage external git-cloned `SKILL.md` plugins |
| `gald3r policy` | Org policy-as-code guardrail (Team/Org tier only; empty bundle otherwise) |
| `gald3r prompt` | Judgment/prompt-asset library (role briefs, rubrics, playbooks, voice) |
| `gald3r project-type` | Manage gald3r Workflow Profiles (list/use/copy/edit/validate/resolve) |
| `gald3r pricing` | Read-only model pricing lookups |

## Diagnostics

| Verb | What it does |
|---|---|
| `gald3r context` | Report this agent's own live context usage, measured from its session transcript (never estimated) |
| `gald3r errors` / `gald3r trace` | Summarize agent errors/failures from trace logs |
| `gald3r crash-stats` | CRASH activation stats (Commands/Rules/Agents/Skills/Hooks) |
| `gald3r muninn` | Query the local codebase knowledge graph: symbol search, caller/callee chains, import-impact analysis |
| `gald3r search` | Gitignore-agnostic content search (sees `.gald3r/` the same as any other directory) |
| `gald3r lint` | Syntax lint verbs (delta-lint gate, subsystem-tag check, dangerous-command guard) |
| `gald3r env` | Bring up and inspect the agent's run-this-project environment |
| `gald3r connectivity-benchmark` | Measure online-vs-offline verb latency |

## Coordination (Valkyrie / WPAC / world_tree)

| Verb | What it does |
|---|---|
| `gald3r valk` | Manage the resident Valkyrie world_tree connector |
| `gald3r workspace` | Workspace-coordination verbs (WPAC inbox; touch-set preflight; member marker governance) |
| `gald3r connect` | Connect a provider account via world_tree device-code flow (reports an honest "not yet supported" today — `gald3r login --token` works) |
| `gald3r login` / `gald3r logout` | Authenticate against / clear a world_tree session |
| `gald3r version-check` | Check world_tree for a newer gald3r version (JWT-gated; offline-safe fallback) |

## Release & distribution (mostly relevant if you're shipping *your own* gald3r-based tool)

| Verb | What it does |
|---|---|
| `gald3r ship` | Ship a release: bump version, promote CHANGELOG, tag, update badge |
| `gald3r push-gate` | Pre-push gate: regular (never blocks) vs. release (CHANGELOG/version discipline) |
| `gald3r release` | Publish a built payload to a remote, or emit a `release.published` event to world_tree |
| `gald3r tier` | Product-tier onboarding — scaffold release profiles + annotate subsystems |
| `gald3r template` | Emit embedded CI/release scaffolding templates |
| `gald3r install` | Download and install a signed gald3r product (Agent CLI binary, Throne desktop app) from its public GitHub Releases |
| `gald3r platform` | Cross-IDE freshness loop: doc-scan proposals + `PLATFORM_STATUS.md` regeneration |
| `gald3r throne` | Launch the installed Gald3r Throne desktop app |
| `gald3r acp` | Launch the gald3r-agent ACP (Agent Client Protocol) adapter server over stdio |
| `gald3r parity-audit` | Diff the product CLI's wired verbs against `gald3r-agent` (internal go/no-go tool) |
| `gald3r broadcast` | Show cached Gald3r Labs vendor broadcast alerts (release notices, advisories) |

## Other

| Verb | What it does |
|---|---|
| `gald3r vault` | File-first knowledge vault (ingest, list, reindex, lint, search, export/import) |
| `gald3r tel` | Terminal Event Layer: pattern-to-action rules over agent/CLI output |
| `gald3r ui-test` | OS-level UI-test verbs |
| `gald3r learn` | Continual-learning loop — nightly session-summary fact extraction |
| `gald3r compress-memory` | Compress the non-gald3r regions of memory files, preserving managed ranges |
| `gald3r mcp` | Native MCP (Model Context Protocol) stdio server surface |

---

Not every verb above is exercised in the [`quickstart`](./quickstart.md) — this page's job
is to be a map of what exists, not a tutorial for each one. `gald3r <verb> --help` (and
`gald3r <verb> <subverb> --help` where a verb has subcommands) is the authoritative usage
text for anything you want to actually run.
