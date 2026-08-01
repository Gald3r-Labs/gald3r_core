# Quickstart

Every command below was actually run against this repo's own source (not guessed from
reading code) while writing this page. Output is trimmed for length but not altered in
substance. If your output looks different, the CLI has likely changed since — check
`gald3r <verb> --help`, which is always authoritative.

## 0. What you're installing, honestly

There is **no packaged binary release yet** (see [`README.md`](./README.md)'s "Honest
status" section) and **no PyPI package**. The only path that works today is running
`gald3r` from a source checkout via [`uv`](https://docs.astral.sh/uv/). That's what this
page documents. When a tagged release ships, prefer downloading the platform binary from
this repo's GitHub Releases instead — this page will be updated to say so explicitly.

## 1. Prerequisites

- Python 3.11+
- [`uv`](https://docs.astral.sh/uv/) — this project uses `uv`, not bare `pip`/`venv`
  (`uv venv` / `uv sync` / `uv run`)
- Git

## 2. Install (from source)

```powershell
git clone https://github.com/Gald3r-Labs/gald3r_core_dev.git
cd gald3r_core_dev
uv sync
```

`uv sync` creates `.venv/` and installs `gald3r_core` itself (editable) plus its dev
dependency group. This also produces a real installed console-script entry point at
`.venv\Scripts\gald3r.exe` (Windows) / `.venv/bin/gald3r` (macOS/Linux) — confirmed by
running it directly from an unrelated directory:

```
> .venv\Scripts\gald3r.exe --version
gald3r 0.2.0 (build 03badb80c277)
```

**To use `gald3r` against a project of your own** (not the gald3r_core checkout itself),
either:
- add `<repo>\.venv\Scripts` (Windows) / `<repo>/.venv/bin` (POSIX) to your `PATH`, or
- invoke the binary by its full path from inside your own project directory, or
- run everything through `uv run gald3r ...` from inside the `gald3r_core_dev` checkout
  while your target project is elsewhere and pass `--root <path>` to the verbs that accept
  it (`setup` does; most others operate on the current directory, so PATH registration is
  the more practical option day to day).

If you've just edited `PATH`, **fully restart your terminal/IDE** — a process that was
already running when you edited `PATH` will not see the change (same caveat the root
[`README.md`](../../README.md) documents for the compiled-binary case).

## 3. First run — confirm the install is healthy

```
gald3r doctor
```

Real output from a fresh checkout:

```
gald3r doctor -- <your project dir>
  [OK  ] python       Python 3.13.7 (...\.venv\Scripts\python.exe)
  [OK  ] gald3r_core  gald3r_core 0.2.0
  [OK  ] build_fingerprint build 03badb80c277 | binary: ... | neutral_source: ... (641 files)
  [WARN] torch        torch not installed (optional -- only needed for local inference)
  [OK  ] gald3r_home  C:\Users\you\.gald3r_template_core
  [OK  ] providers    ...\providers.yaml: 2 provider(s) configured
  [OK  ] env          set: GALD3R_WORLD_TREE_URL
  [OK  ] identity     project_id=... (your-project-name)
  [OK  ] daemon       no connector lock (Valkyrie not running)
  [OK  ] tel          no .gald3r/tel/ configured yet (optional, T83 epic)
  [OK  ] world_tree   ok
  [WARN] auth         no stored session token (https://api.gald3r.ai)
  [OK  ] channels     no channels.yaml found (channels are optional)

All checks passed (warnings/skips are informational, not blocking).
```

`WARN` lines are informational, not failures — `torch` and cloud `auth` are both optional
for local/offline use.

## 4. Scaffold a project

From inside the project directory you want gald3r-enabled (`cd` there first):

```
gald3r setup --dry-run   # see what would be created, writes nothing
gald3r setup             # actually create it
```

`setup --dry-run` lists exactly what it's about to do (36 items on a fresh project as of
this writing: `.gald3r/` plus its `tasks/`, `bugs/`, `config/`, `subsystems/`, `features/`,
`releases/`, `linking/`, `experiments/`, `specifications_collection/`, `tracking/`, `prds/`
subdirectories, and starter files like `TASKS.md`, `BUGS.md`, `PROJECT.md`,
`CONSTRAINTS.md`). Nothing outside `.gald3r/` is touched.

## 5. First useful command — a zero-configuration smoke test

Before wiring up any AI provider, you can confirm the agent-run plumbing works end to end
with **no network call and no API key**:

```
gald3r run "hello gald3r" --backend dev-echo
```

```
[echo #0] r3dlag olleh
```

`--backend dev-echo` is a deterministic, offline stand-in session — useful for confirming
the CLI, not for real agent work. It's the fastest way to prove the install works before
touching any provider configuration.

## 6. Track real work: tasks

```
gald3r task add "Write the quickstart doc" -d "Draft it" --type docs --priority medium
gald3r task list
```

```
Created task 1: Write the quickstart doc
```

```
1 task(s) by value (if done):
  9-10 (crit) │                                         0
  7-8 (high)  │                                         0
  5-6 (med)   │████████████████████████████████████████ 1
  1-4 (low)   │                                         0
    9-10 release/demo-critical (the moat)   ·   7-8 major user-facing feature
    5-6 useful improvement / user docs      ·   1-4 minor polish / busywork
──────────────────────────────────────────────────────────
[📋] T1      pending                  Write the quickstart doc
```

`gald3r task next` shows the single next task to work; `gald3r task update T1 --status
in-progress` claims it. See [`cli-reference.md`](./cli-reference.md) for the full task/bug
verb surface.

## 7. Optional: a real agent run with a provider

`gald3r run` without `--backend dev-echo` talks to a real provider. Generate a starter
config first:

```
gald3r init-providers --dry-run   # preview without writing
gald3r init-providers             # write ~/.gald3r_template_core/providers.yaml (or wherever GALD3R_HOME resolves)
```

The generated starter defaults to a **local Ollama** endpoint (`http://localhost:11434/v1`,
model `qwen3:4b`) — no cloud account or API key required if you already have Ollama running
locally. Edit `providers.yaml` to point at OpenAI/Anthropic/etc. instead if you'd rather use
a cloud provider (`api_key` fields accept `$ENV_VAR`-style references). Once configured:

```
gald3r run "explain what this project does"
```

This step was **not** exercised end-to-end while writing this doc (it requires either a
running local model server or a real cloud API key, neither of which is available in this
sandboxed drafting pass) — everything above it in this quickstart was.

## 8. Where next

- [`concepts.md`](./concepts.md) — CRASH, `.gald3r/`, and the DB-vs-markdown state model
- [`cli-reference.md`](./cli-reference.md) — the full verb catalog
- `gald3r <verb> --help` — always the authoritative source for any single verb's flags
