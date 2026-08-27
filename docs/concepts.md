# Core Concepts

A handful of ideas that make the rest of gald3r_core make sense. This page is written for
someone *using* `gald3r`, not building it — see the root [`GALD3R.md`](../../GALD3R.md) and
[`AGENTS.md`](../../AGENTS.md) if you need the full internal contract (they're written for
agents/contributors working inside a gald3r-native repo, and go considerably deeper than a
product user needs).

## `.gald3r/` — your project's control-plane directory

Running `gald3r setup` in a project creates a `.gald3r/` directory holding everything gald3r
tracks about that project: tasks, bugs, plans, constraints, subsystem notes, and
cross-project links. It's plain files (mostly Markdown + a SQLite database), so it's readable,
diffable, and safe to commit to your own repo if you want that history tracked.

A fresh project gets the current **v2 layout**: `.gald3r/.identity`, `PROJECT.md`,
`CONSTRAINTS.md`, and `config/` live directly under `.gald3r/`, while the per-project SDLC
artifacts — `TASKS.md`/`tasks/`, `BUGS.md`/`bugs/`, `PLAN.md`, `FEATURES.md`, `SUBSYSTEMS.md`,
`PRDS.md`, `IDEA_BOARD.md`, `TO-DO_LIST.md`, `ADRS.md`, `DECISIONS.md`, and the rest — nest
under `.gald3r/project/`. Run `gald3r layout show` any time to print the resolved path for
every artifact; `gald3r layout migrate` moves an older, flat-layout project onto v2.

Two things worth knowing about how it's kept consistent:

- **SQLite (`.gald3r/gald3r.db`) is the source of truth** for task/bug status transitions,
  creation, and acceptance criteria. The corresponding Markdown (`TASKS.md`, `BUGS.md`, and the
  individual task/bug files) is a **generated, portable cache** — a database write commits
  first, then the Markdown is regenerated to match, not the other way around. Most other
  documents (`PLAN.md`, `SUBSYSTEMS.md`, and similar) are still file-first today, with the
  database mirroring them; the split is deliberate and moving toward full database authority
  over time, one document class at a time.
- Prefer the CLI verbs (`gald3r task add`, `gald3r task update`, `gald3r bug ...`) over
  hand-editing the Markdown files. `gald3r db backfill` imports existing files into the database
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

## CRASH — Commands, Rules, Agents, Skills, Hooks (plus Verbs: CRASHV)

CRASH is gald3r's component model for turning instructions into things that actually run,
rather than markdown an LLM might or might not follow. Counting the compiled CLI verb layer
alongside it, the full set is sometimes called **CRASHV**:

| Component | What it is |
|---|---|
| **Commands** | The in-IDE slash-command layer (`/g-*` in Claude Code, `@g-*` in Cursor and the rest) — thin dispatchers onto a skill. See [Commands](./commands.md). |
| **Rules** | Always-apply behavioral constraints, loaded automatically every session. |
| **Agents** | Specialized personas scoped to a narrower job — invoke one directly when you specifically want that framing. See [Agents](./agents.md). |
| **Skills** | On-demand instruction packages loaded when a command or the agent's own judgment calls for them. See [Skills](./skills.md). |
| **Hooks** | Lifecycle scripts (session-start, pre-tool-call, agent-complete, ...) — the one CRASH component type that is **executing code**, not injected instructions. |
| **Verbs** | The compiled `gald3r <verb>` CLI surface itself — what you type in a terminal, distinct from the in-IDE command layer above. See [Verbs](./verbs.md). |

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
`world_tree`. None of this is required for local, single-project use — `gald3r workspace
probe` reports `world_tree` connectivity on demand, and `gald3r workspace token-status`
reports whether a session token is stored; neither being unset blocks anything outside the
features that actually need them.

## Where the deeper reference lives

Each idea above has its own full page in this doc set:

- [`commands.md`](./commands.md) / [`skills.md`](./skills.md) / [`agents.md`](./agents.md) /
  [`verbs.md`](./verbs.md) — the four CRASHV component catalogs, narrative layer plus links to
  the exhaustive generated inventories
- [`crash.md`](./crash.md) — CRASH end to end, with real component examples from this repo
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
