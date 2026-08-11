# CLI Reference

This table is transcribed directly from `gald3r --help` (run against this repo's own
source, 2026-08-01, `gald3r 4.0.0 build a7242bba63ea`) — not written from memory or from
reading source code. Every verb also answers its own `--help`; that is always more
authoritative than this page if the two ever disagree; ask the tool, not this file.

**Driving these verbs from inside an AI IDE instead of a terminal?** Most of them also exist
as an in-IDE slash command (`/g-status` in Claude Code, `@g-status` in Cursor, and so on) once
you've installed that platform's overlay — see [`platform-usage.md`](./platform-usage.md) for
the command/skill/rule/hook/agent tour and the per-platform quick-start matrix, or go straight
to the generated [Commands reference](./reference/crash/commands.md) for a usage card per
`/g-`/`@g-` command (purpose, arguments, example invocation, related commands).

<!-- Go-era update (verified against gald3r_cli/internal/commands/root.go,
     2026-08-08): the Go rewrite added `--dir` as a new persistent/global
     flag, documented below. -->

## Global flags

| Flag | What it does |
|---|---|
| `--dir PATH` | Explicit project root directory — skips the `.gald3r/` walk-up entirely (the directory must already contain a `.gald3r/` directory). Highest-precedence root override; accepted by every verb. A verb's own `--root`/`--project-root` flag, where one is already defined locally on that verb, still wins over `--dir` on that same command. |

## Getting started & health

| Verb | What it does |
|---|---|
| `gald3r setup` | Turn this folder into a gald3r project — tasks, bugs, plans, and agent coordination, ready to use in one command. Safe to re-run if anything's missing or damaged. |
| `gald3r init` | Alias for `setup` — identical flags and behavior (verified: same `--help` text) |
| `gald3r onboard` | Get a new project fully set up in one command — scaffold, pick a workflow, install a curated skill set, and link it to related projects. Safe to re-run, and works offline. |
| `gald3r doctor` | Find out why gald3r isn't working right — one command checks your Python setup, providers, credentials, and background services, and tells you exactly what's broken. |
| `gald3r upgrade-project` | Bring an existing project fully current after you've upgraded the `gald3r` binary (`gald3r install update`): refreshes every installed platform overlay, takes a pre-flight backup, then runs schema-migrate, `validate --fix`, a database backfill, and `doctor` — one composite verb with a baseline-vs-final regression report, replacing the old hand-run, step-by-step upgrade sequence (`--dry-run` plans the whole ladder without writing anything; `--no-overlays` skips the overlay-refresh step; `--json`) |
| `gald3r identity normalize` | Backfill a missing `gald3r_lineage_epoch`/canonical `user_name` into an existing `.gald3r/.identity` file, using the same identity-resolution chain `gald3r setup` uses for brand-new projects. Dry-run by default; `--apply` writes, `--force` also overwrites an existing non-empty value that differs from the canonical one (T606) |
| `gald3r selftest` | Run the install self-diagnostic ("gald3r is N% functional") |
| `gald3r sessions` | Read-only listing of local chat sessions for the current directory |
| `gald3r status` | See where your project actually stands at a glance — task/bug counts, completion %, what's blocked and why, and what's waiting on you (`--min-severity N`, `--json`) |
| `gald3r --version` | Print the CLI version, the schema/rel version this binary writes, and a VCS build fingerprint (short commit + commit time, `+dirty` when applicable) when the binary was built with VCS stamping. <!-- Corrected during Phase H1 verification, 2026-08-08: an earlier draft of this row, written from gald3r_cli/internal/commands/root.go's doc comment alone, claimed --version was "currently a plain version-string stub" with no build fingerprint. That comment describes only root.go's OWN contribution (cobra's bare Version field) -- it misses that gald3r_cli/internal/commands/doctor.go's RegisterDoctor() calls root.SetVersionTemplate(versionIdentityBlock()), which overrides that plain output with the schema/rel + build-fingerprint block described here (verified directly against the compiled binary's real `--version` output, not just source comments: `gald3r version 0.0.1-dev` / `  schema/rel version: 3.0.0` / `  build: <sha> (<time>) [+dirty]`). What is still genuinely absent versus the Python era: a `--json` machine-readable payload and the BUG-510 build-drift-check comparison against an expected fingerprint -- neither is implemented, so do not claim those two specifically. --> |

**Upgraded the `gald3r` binary and a project now looks stale?** Previously this meant re-running,
by hand, in order: `platform install <name> --force` for every installed IDE overlay, `schema-migrate
--apply`, `validate --fix`, `db backfill`, `db verify`, then `doctor` — with no single command tying
the steps together and no report telling you whether anything actually regressed along the way. That
hand-run sequence is retired: `gald3r upgrade-project` runs all of it and diffs the before/after state
for you. `gald3r doctor`'s `overlay_currency` check, and a one-line session-start prompt when a
project's overlay is stamped from an older binary version, both point you at `upgrade-project` by name.

## Tasks, bugs, and project state

| Verb | What it does |
|---|---|
| `gald3r task add/list/show/update/next/ready` | Track the work that needs doing — create tasks, see what's ready to pick up, and move them through to done. |
| `gald3r task ac-check` | Tick an acceptance-criterion checkbox for a task (per-criterion attestation) |
| `gald3r task verify` | Record a reviewer verdict (`--pass` → completed, `--fail` → back to pending) |
| `gald3r task regenerate-index` | Rebuild `TASKS.md` from the on-disk task files |
| `gald3r task archive` / `gald3r bug archive` | Move finished tasks/bugs out of the active list into long-term history, so `TASKS.md`/`BUGS.md` stay short and readable |
| `gald3r task delete` / `gald3r bug delete` | Permanently remove a task/bug that was created in the wrong project or by mistake — not for ones you simply decided not to do |
| `gald3r task-sync-check` | Validate `TASKS.md` against the `tasks/` files |
| `gald3r bug` | Log a defect the moment you find it and keep it visible until it's actually fixed — no bug silently lost in a chat transcript (same shape as `task`) |
| `gald3r db backfill/verify/rebuild` | Keep your local task/bug database in sync with the `.gald3r/` files — import them in, check for drift, or rebuild the files from the database |
| `gald3r db render-history` | Render `status_history`/`op_log` to append-only, git-tracked `.gald3r/history/*.ndjson` files, so a rollback + `db backfill` can reconstruct history that has no file of its own (T559) |
| `gald3r logs ingest/status/prune` | Ingest gald3r's own logs and platform-native chat transcripts (Claude Code, Cursor, ...) into a separate, gitignored `.gald3r/logs.db` — keeps bulk log volume out of the coordination database, with automatic retention/rotation (T559) |
| `gald3r validate` | Validate `.gald3r/tasks|bugs` frontmatter (schema, status vocabulary, folder placement); `--fix` normalizes what's safely fixable |
| `gald3r verify` | Check whether a task actually meets its acceptance criteria before you call it done (the completion gate) |
| `gald3r schema-migrate` | Migrate a project's `.gald3r/` files forward to the current schema version (dry-run by default) |
| `gald3r inbox` | Turn quick task/bug drafts left in the inbox into fully tracked tasks and bugs |
| `gald3r wishlist` | Turn a free-form wishlist or notes doc into real, tracked tasks (read-only against the source doc) |
| `gald3r idea` | Capture, list, review, and promote ideas (`IDEA_BOARD.md`) |
| `gald3r feature` | Catch broken feature-hierarchy links before they drift into `FEATURES.md` |
| `gald3r subsystem` | Catch drift between your subsystem specs and `SUBSYSTEMS.md`'s index |
| `gald3r prd` | Keep a formal, sign-off-ready spec for a feature — Product Requirements Docs (PRDs) that stay frozen once released, with a clean revision trail when they need to change |
| `gald3r constraint add/list/show/check` | Declare and enforce ad-hoc, per-project rules on agent writes — record a rule in `CONSTRAINTS.md`'s Constraint Index (`add`), see what's currently in force (`list`/`show`), and check whether writing to a given path would violate one before you write it (`check`) |
| `gald3r decision add/list/show/supersede` | Record a binding call once and stop re-litigating it — `DECISIONS.md` is append-only, so changing your mind mints a new, chain-linked decision (`supersede`) rather than editing or deleting the old ruling |
| `gald3r dependency-graph` | See what's blocking what — a visual map of task dependencies, the critical path, and which blocked tasks are stuck waiting |
| `gald3r medic` | Diagnose and repair problems in your project's gald3r setup — broken links, stale indexes, drifted files — fixing what's safe to fix automatically |

## Agent work

| Verb | What it does |
|---|---|
| `gald3r chat` | Have an ongoing conversation with your gald3r agent right in the terminal — it picks back up where you left off in this folder, so context carries across sessions. |
| `gald3r run "<message>"` | Send gald3r a single instruction and get one response back — no session to manage, ideal for scripts and one-off asks (`--backend dev-echo` for an offline, no-provider smoke test; `--model provider:model` to pick a target; `--resume <id>` to continue a session) |
| `gald3r agent` | Define an agent's behavior — tools, prompts, guardrails — in a plain YAML spec file, then run it |
| `gald3r go` | Have gald3r pick up the next task on your queue and work it, one step at a time, then hand it back to you for review |
| `gald3r go-code T123` | Implement a specific task, then mark awaiting-verification. Works whether your implementer provider is an API-key registry id (`anthropic`/`openai`/...) or a CLI-subscription provider (`cursor-agent`/`claude`) — the latter shells out to that provider's own CLI (BUG-839), so a Cursor- or Claude-Code-subscription-only host with no API key still works |
| `gald3r go-bug BUG-42` | Claim a bug, implement a fix, self-verify it, and resolve it on success — the bug-side counterpart to `go-code`. Same CLI-subscription-or-registry provider flexibility as `go-code` above (BUG-839); a CLI-vocab turn has no captured response text to scan for a verdict line, so it requires BOTH a clean exit AND independent evidence the turn actually touched the working tree (an uncommitted change or a new commit) before treating it as a pass — a clean exit alone is never enough to resolve a bug |
| `gald3r go-review T123` | Review a task's acceptance criteria; mark completed (pass) or pending (fail) |
| `gald3r go-status` | Check on a running autopilot loop without interrupting it — is it still working, waiting, or stuck |
| `gald3r autoclaim` | Work through several pending tasks in a row without stopping to ask which one's next (`--online` extends this across your whole team's shared board) |
| `gald3r swarm` | Split a big batch of work across several agents running in parallel |
| `gald3r autopilot` | Turn gald3r loose on your task queue — it implements, reviews, and repeats on its own until the backlog is clear or you call it off. |
| `gald3r worktree` | Give each agent its own isolated git checkout to work in, so parallel agents never step on each other's uncommitted changes |
| `gald3r claim` | Claim a task safely when multiple agents might grab the same one at once — only one of them wins |
| `gald3r housekeep` | Auto-commit safe, routine `.gald3r/` coordination changes so an autonomous run doesn't stall waiting on a human |
| `gald3r go-preflight` / `gald3r go-reconcile` / `gald3r go-verdicts` | Internal plumbing the `go`/swarm coordinators call to batch pre-flight checks, merge parallel work, and record verdicts in one call each — not typically run by hand |
| `gald3r coordinator-preflight` | One read-only JSON snapshot for a fresh coordinator to consult before it dispatches — the controller housekeeping verdict, measured context, the focused DB queue, and hot-inbox state, combined in a single call. Never claims work or mutates project state (`--min-value`/`--min-severity` tune the focused queue floors, `--session-id` scopes the context reading) |

## Providers & configuration

| Verb | What it does |
|---|---|
| `gald3r init-providers` | Get set up to use local LLMs (Ollama, LM Studio, vLLM, llama.cpp) — auto-detects what's installed and writes the config for you. Full guide: [`providers.md`](./providers.md#gald3r-init-providers) |
| `gald3r providers` | List, add/remove/edit providers and models in `providers.yaml` without hand-editing YAML (round-trip safe), validate the whole file, and store API keys securely in your OS's keyring. Full guide: [`providers.md`](./providers.md#managing-providersyaml-from-the-cli-t610) |
| `gald3r providers set-key` | Store a provider's API key in your OS keyring instead of a plaintext file. Full guide: [`providers.md`](./providers.md#gald3r-providers-set-key-keys-keyring-vs-plaintext) |
| `gald3r local-model` | Find out which local LLM your hardware can actually run well, and get the exact command to serve it. Full guide: [`providers.md`](./providers.md#gald3r-local-model-assess) |
| `gald3r config` | Reserved for future agent configuration — not yet implemented |
| `gald3r profile` | See or change your personal display preferences for this gald3r install (voice/tone and similar settings) |
| `gald3r user-profile` | Manage your gald3r account settings across every project on this machine, with per-project overrides where you need them |
| `gald3r pers` | Give your agent a distinct voice and style — switch between personality packs, or customize your own |
| `gald3r skill-pack` | Install ready-made bundles of skills your agents can use, without writing them from scratch |
| `gald3r skills-lock` | Verify your installed skills haven't been tampered with, using a signed checksum lockfile |
| `gald3r plugin` | Add new capabilities to gald3r from the community — install, update, and enable third-party skills without hand-editing your own project files. |
| `gald3r policy` | Enforce your org's own rules automatically (Team/Org tier only; a no-op on free/retail installs) |
| `gald3r prompt` | Judgment/prompt-asset library (role briefs, rubrics, playbooks, voice) |
| `gald3r project-type` | Switch between preset workflows (solo dev, team, enterprise, ...) that tune how gald3r behaves in this project |
| `gald3r pricing` | Look up what an LLM model costs per token, right from the terminal |

## Diagnostics

| Verb | What it does |
|---|---|
| `gald3r context` | See exactly how much of your context window an agent is actually using, measured from its session transcript (never a guess) |
| `gald3r errors` / `gald3r trace` | Summarize agent errors/failures from trace logs |
| `gald3r crash-stats` | See which of your Commands, Rules, Agents, Skills, and Hooks are actually firing during real sessions — not just installed, but used |
| `gald3r muninn` | Ask questions about your codebase's structure — what calls this function, what breaks if I change that file — without grepping by hand |
| `gald3r search` | Find text anywhere in the project, including files your normal search tool silently skips because they're gitignored |
| `gald3r lint` | Catch problems the moment they happen: syntax errors right after a file is written, missing component tags, or a dangerous-looking shell command |
| `gald3r secrets check/test` | See what an optional `.gald3rsecret` file protects — every declared rule, its tier, and how many files it touches (`check`), or which tier one specific path resolves to (`test`). Reports the policy only; never prints file content |
| `gald3r env` | Start and check the services your project needs to run (databases, local servers, test accounts) with one command |
| `gald3r connectivity-benchmark` | Measure how much slower coordination gets when offline vs. online, instead of guessing |

## Coordination — your projects and agents, talking to each other

Most AI tooling stops at one assistant in one repo. These verbs are what make gald3r *multiplayer*:
projects that answer each other's questions, agents you can message mid-run, and shared state
across your machines and your team.

| Verb | What it does |
|---|---|
| `gald3r valk` | Let your projects and agents talk to each other — and to you — while they work. Ask another project a question and get a grounded, cited answer from its real context; message a running swarm without interrupting it; keep state in sync across machines and teammates. |
| `gald3r workspace` | Coordinate work across multiple related projects — read incoming requests from linked repos, check what's safe to touch before a multi-repo change, and keep member repos in the expected shape. |
| `gald3r connect` | Link a provider account with a device code, like signing into a streaming app on a TV. See [`providers.md`](./providers.md#gald3r-connect) for a real, current capture of what this returns. |
| `gald3r login` / `gald3r logout` | Sign in or out of your gald3r account so team and multi-device features work. |
| `gald3r version-check` | Find out if a newer gald3r is available — works offline, tells you the truth either way. |

## Release & distribution (mostly relevant if you're shipping *your own* gald3r-based tool)

| Verb | What it does |
|---|---|
| `gald3r ship` | Ship a release: bump version, promote CHANGELOG, tag, update badge |
| `gald3r push-gate` | Catch a missing CHANGELOG entry or version bump before a release push goes out, not after |
| `gald3r release` | Push a built release out to a remote destination, and let your team know it went out |
| `gald3r template` | Get ready-made CI/release config files (GitHub Actions, release-please, ...) written into your project instead of writing them by hand |
| `gald3r shutdown` | Safely stop persistent gald3r MCP, Valkyrie, and autopilot processes before an in-place binary upgrade — especially on Windows, where a running MCP server can lock `gald3r.exe` (`--dry-run` previews without signalling anything; `--force` kills stragglers left after the graceful `--wait` window). See [`install.md`](./install.md#stop-the-local-fleet-before-replacing-files) for the full before-an-upgrade sequence |
| `gald3r install update` | Get the latest signed `gald3r_core` release, replacing whatever `gald3r` currently resolves to on PATH (`install agent` is a deprecated alias) |
| `gald3r install throne` | Get the signed Throne desktop app from its public GitHub Releases |
| `gald3r platform` | Keep gald3r's support for Cursor, Claude Code, Copilot, and every other AI IDE up to date as their own docs change |
| `gald3r throne` | Open Gald3r Throne, the visual desktop companion to this CLI |
| `gald3r acp` | Let ACP-compatible editors (like Zed) drive gald3r as an embedded agent over stdio |
| `gald3r parity-audit` | Check this CLI for missing commands compared to the legacy `gald3r-agent` binary (internal go/no-go tool) |
| `gald3r broadcast` | Show cached Gald3r Labs vendor broadcast alerts (release notices, advisories) |

## Other

| Verb | What it does |
|---|---|
| `gald3r vault` | Build a searchable knowledge base from the docs, repos, and web pages you feed it — plain markdown files you own, not locked away in a database. |
| `gald3r memory` | Small, scoped memory records (user/project/workspace/team/company/client) plus chat-native recall over them and your vault notes together (T557: `add`/`list`/`show`/`supersede`/`migrate-learned-facts`/`recall`) |
| `gald3r tel` | Watch your terminal output for patterns and react automatically — alert, log, or trigger a follow-up command |
| `gald3r telemetry` | Opt in (or out) of local-only usage telemetry — a count of crashes, your gald3r version, and OS/architecture; off by default, nothing ever leaves your machine |
| `gald3r ui-test` | Drive real mouse/keyboard/screenshot steps against a native desktop app to verify a UI change actually works |
| `gald3r learn` | Have gald3r learn from your sessions — extracts durable facts from recent chats so it remembers them next time |
| `gald3r compress-memory` | Shrink your AGENTS.md/CLAUDE.md memory files to save context space, without touching gald3r's own managed sections |
| `gald3r mcp` | Expose gald3r's prompt/judgment library to any MCP-compatible client (Claude Desktop, Cursor, and others) over stdio |
| `gald3r hearth` | Run a small local background service so other tools (dashboards, IDE extensions) can check your project's status without shelling out to the CLI each time |
| `gald3r serve` | Host gald3r over HTTP for multiple isolated tenants/users to connect to at once |
| `gald3r sync` | For gald3r's own contributors: redeploy this repo's hooks/commands/rules/agents/skills into its local IDE copies. Dry-run by default; `--apply` writes, `--prune` (also dry-run-first, opt-in) safely removes only overlay orphans it can prove it generated |

---

Not every verb above is exercised in the [`quickstart`](./quickstart.md) — this page's job
is to be a map of what exists, not a tutorial for each one. `gald3r <verb> --help` (and
`gald3r <verb> <subverb> --help` where a verb has subcommands) is the authoritative usage
text for anything you want to actually run.
