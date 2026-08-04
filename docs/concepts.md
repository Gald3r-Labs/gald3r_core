# Core Concepts

A handful of ideas that make the rest of gald3r_core make sense. This page is written for
someone *using* `gald3r`, not building it — see the root [`GALD3R.md`](../../GALD3R.md) and
[`AGENTS.md`](../../AGENTS.md) if you need the full internal contract (they're written for
agents/contributors working inside a gald3r-native repo, and go considerably deeper than a
product user needs).

## `.gald3r/` — your project's control-plane directory

Running `gald3r setup` in a project creates a `.gald3r/` directory holding everything gald3r
tracks about that project: tasks, bugs, plans, constraints, subsystem notes, and
cross-project links. It's plain files (mostly Markdown + a SQLite DB), so it's readable,
diffable, and safe to commit to your own repo if you want that history tracked.

Two things worth knowing about how it's kept consistent:

- **SQLite (`.gald3r/gald3r.db`) is the source of truth** for task/bug state.
  `.gald3r/TASKS.md`, `.gald3r/BUGS.md`, and the individual task/bug files under
  `.gald3r/tasks/` and `.gald3r/bugs/` are a **generated, portable cache** — a DB update
  triggers a rewrite of the corresponding Markdown, not the other way around.
- Prefer the CLI verbs (`gald3r task add`, `gald3r task update`, `gald3r bug ...`) over
  hand-editing the Markdown files. `gald3r db backfill` imports existing files into the DB
  if you ever need to reconcile the two; `gald3r db verify` reports drift.

## Tasks and bugs

The two units of trackable work:

- **Tasks** — `gald3r task add "Title" -d "Description" --type feature --priority medium`.
  Move through a small status lifecycle (`pending` → `in-progress` → `awaiting-verification`
  → `completed`, plus `failed`/`paused`/`cancelled`). `gald3r task list`, `gald3r task next`,
  and `gald3r task ready` are how you find what to work on next.
- **Bugs** — `gald3r bug` — the same idea, scoped to defects rather than planned work.

Both render a compact visual priority-bar chart on `list` (see the quickstart's example
output) rather than a plain table — that's intentional, not a formatting accident.

## CRASH — Commands, Rules, Agents, Skills, Hooks

CRASH is gald3r's component model for turning instructions into things that actually run,
rather than markdown an LLM might or might not follow:

| Component | What it is |
|---|---|
| **Commands** | Named, invocable actions (`gald3r <verb>`, or `/g-*` / `@g-*` inside an IDE integration) |
| **Rules** | Always-apply behavioral constraints, loaded automatically every session |
| **Agents** | Specialized personas scoped to a narrower job |
| **Skills** | On-demand instruction packages loaded when a command or the agent's own judgment calls for them |
| **Hooks** | Lifecycle scripts (session-start, pre-tool-call, agent-complete, ...) — the one CRASH component type that is **executing code**, not injected instructions |

`gald3r crash-stats` shows live activation counts for the current session if you want to see
this in practice.

## Sessions: `chat` vs `run`

- `gald3r chat` — a persistent REPL that resumes the current directory's latest session.
  Good for an ongoing back-and-forth.
- `gald3r run "<message>"` — one-shot: send a message, get a response, done. Add
  `--resume <session_id>` to continue a specific prior session instead of starting fresh.
  `--backend dev-echo` swaps in a deterministic, offline, no-provider-required session —
  useful for confirming the CLI works before configuring a real provider (see the
  quickstart).

## Providers

`gald3r` talks to models through a `providers.yaml` config (`gald3r init-providers`
generates a starter one). It supports local OpenAI-compatible backends (Ollama, LM Studio,
vLLM, llama.cpp) as well as cloud providers, configured per-provider with model lists and
roles (`coding`, `general`, `reasoning`, `vision`, ...). Nothing about gald3r requires a
specific cloud vendor. See [`providers.md`](./providers.md) for the full guide — annotated
`providers.yaml` anatomy, a worked recipe per local backend, OpenRouter/cloud setup, and
real provider-error troubleshooting.

## `world_tree` (optional, online-only)

Several verbs (`gald3r valk`, `gald3r login`, `gald3r version-check`, cross-project
`workspace`/WPAC coordination) talk to an optional hosted coordination service called
`world_tree`. None of this is required for local, single-project use — `gald3r doctor`
reports `world_tree ok` / connectivity status but treats it as informational, and
`gald3r auth` being unset is a `WARN`, not a failure.

## Where the deeper reference lives

Each idea above has its own full page in this doc set:

- [`crash.md`](./crash.md) — CRASH, with real component examples from this repo
- [`task-bug-workflow.md`](./task-bug-workflow.md) — the full task/bug lifecycle
- [`autopilot.md`](./autopilot.md) — `go`/`go-code`/`go-review`/`autoclaim`/`swarm`/`autopilot`
- [`coordination.md`](./coordination.md) — Valkyrie (live) and WPAC (file-based) multi-project
  coordination, and `world_tree`
- [`memory.md`](./memory.md) — scoped memory records and the vault
- [`providers.md`](./providers.md) — `providers.yaml` anatomy, local backend recipes
  (Ollama/LM Studio/vLLM/llama.cpp), cloud providers, and provider troubleshooting

If you outgrow this whole doc set — e.g. you're building ON gald3r_core itself, not just
using the finished CLI — the root [`GALD3R.md`](../../GALD3R.md) is the canonical framework
reference (CRASH internals, the `.gald3r/` authority model, Valkyrie vs. WPAC coordination,
the parity/build model), and [`AGENTS.md`](../../AGENTS.md) is the full agent-behavior
contract. Both are written for people/agents working inside a gald3r-native project day to
day, so expect more depth (and more internal jargon) than this page.
