# Verbs — the `gald3r` CLI surface

This page maps the `gald3r <verb>` command tree — what you type into a terminal. It is
transcribed directly from `gald3r --help` and each verb's own `--help`, not written from
memory. Every verb also answers its own `--help` (and `gald3r <verb> <subverb> --help` where a
verb has subcommands); that is always more authoritative than this page if the two ever
disagree — ask the tool, not this file.

**This page is about the CLI verb surface. It is distinct from [Commands](./commands.md)** — the
in-IDE slash-command layer (`/g-status` in Claude Code, `@g-status` in Cursor, and so on) that
most platforms also expose once you've installed that platform's overlay. A verb can exist here
with no slash-command twin, and a command can exist there with no 1:1 verb — they're
complementary catalogs, not duplicates. See [`platform-usage.md`](./platform-usage.md) for the
per-platform quick-start matrix.

## Global flags

| Flag | What it does |
|---|---|
| `--dir PATH` | Explicit project root directory — skips the `.gald3r/` walk-up entirely (the directory must already contain a `.gald3r/` directory). Highest-precedence root override; accepted by every verb. A verb's own `--root`/`--project-root` flag, where one is already defined locally on that verb, still wins over `--dir` on that same command. |

## Getting started & health

| Verb | What it does |
|---|---|
| `gald3r setup` | Turn this folder into a gald3r project — tasks, bugs, plans, and agent coordination, ready to use in one command. Safe to re-run if anything's missing or damaged. |
| `gald3r onboard` | Get a new project fully set up in one command — scaffold, pick a workflow, install a curated skill set, and link it to related projects. Safe to re-run, and works offline. |
| `gald3r doctor` | Diagnose your gald3r install — a real, honest health check that never fabricates a green result. |
| `gald3r upgrade-project` | Run the full upgrade ladder after `gald3r install update`: refreshes every installed platform overlay, takes a backup, then runs schema-migrate, `validate --fix`, a database backfill, and `doctor` — one composite verb with a baseline-vs-final regression report (`--dry-run` plans without writing; `--no-overlays` skips the overlay refresh; `--json`). |
| `gald3r identity` | Maintenance sweeps for `.gald3r/.identity` — normalize a missing lineage/user-name field using the same resolution chain a fresh `setup` uses. |
| `gald3r selftest` | Run the install self-diagnostic ("gald3r is N% functional"). |
| `gald3r sessions` | List the local gald3r chat sessions for this directory. |
| `gald3r status` | See where your project actually stands at a glance — task/bug counts, completion %, what's blocked and why, and what's waiting on you (`--min-severity N`, `--json`). |
| `gald3r --version` | Print the CLI version, the schema/rel version this binary writes, and a build fingerprint (short commit + commit time, `+dirty` when applicable). |
| `gald3r version-check` | Find out if a newer gald3r is available — run `gald3r login` first for a live answer; otherwise degrades honestly to an offline message rather than guessing. |

**Upgraded the `gald3r` binary and a project now looks stale?** `gald3r upgrade-project` runs
the full refresh sequence for you — overlay reinstall, schema migration, validation, database
backfill, and a doctor pass — and reports a before/after regression diff. `gald3r doctor`'s
overlay-currency check, and a one-line session-start prompt when a project's overlay is stamped
from an older binary version, both point you at it by name.

## Tasks, bugs, and project state

| Verb | What it does |
|---|---|
| `gald3r task add/list/show/update/next/ready` | Track the work that needs doing — create tasks, see what's ready to pick up, and move them through to done. |
| `gald3r task ac-check` / `ac-status` | Tick an acceptance-criterion checkbox for a task (per-criterion attestation), or check its current checked/unchecked status. |
| `gald3r task verify` | Record a reviewer verdict (`--pass` → completed, `--fail` → back to pending). |
| `gald3r task stale-claims` | List in-progress tasks whose claim has expired, classify them, and resolve simple ones automatically — complex ones get a recommendation instead of a silent auto-release. |
| `gald3r task archive` / `gald3r bug archive` | Move finished tasks/bugs out of the active list into long-term history, so `TASKS.md`/`BUGS.md` stay short and readable. |
| `gald3r task delete` / `gald3r bug delete` | Permanently remove a task/bug that was created in the wrong project or by mistake (requires `--reason`) — not for ones you simply decided not to do. |
| `gald3r task-sync-check` | Detect (and optionally repair) drift between `TASKS.md` and the `tasks/` files. |
| `gald3r bug` | Log a defect the moment you find it and keep it visible until it's actually fixed — no bug silently lost in a chat transcript (same shape as `task`). |
| `gald3r db backfill/verify/rebuild` | Keep your local task/bug database in sync with the `.gald3r/` files — import them in, check for drift, or rebuild the files from the database. `rebuild` covers tasks, bugs, and artifact kinds including adr/todo/idea. |
| `gald3r db regenerate-index` | Rebuild a slim index (`TASKS.md` / `BUGS.md` / `ADRS.md` / `TO-DO_LIST.md` / `IDEA_BOARD.md` / `DECISIONS.md` / `PRDS.md`) from `gald3r.db` (`--kind`, default all). File-walk is fallback only when that kind has zero DB rows. Also mounted as `gald3r adr regenerate-index`, `gald3r todo regenerate-index`, and `gald3r idea regenerate-index`. |
| `gald3r db query` | Run one read-only `SELECT`/`WITH` statement against `gald3r.db` (`--param NAME=VALUE` for named binding, `--limit`/`--timeout`, `--json`), or inspect its schema with `--schema` — fail-closed: mutating statements, `ATTACH`/`DETACH`, `VACUUM`, and every `PRAGMA` are rejected before the database is even opened, and the connection itself is physically read-only. This is the DB-query surface `g-rl-40` rule 2 asks for instead of brute-reading task/bug files. |
| `gald3r logs` | Ingest gald3r's own logs and platform-native chat transcripts (Claude Code, Cursor, ...) into a separate, gitignored logs database — keeps bulk log volume out of the coordination database. |
| `gald3r validate` | Validate `.gald3r/tasks|bugs` file frontmatter and WPAC inbox message files (schema, status vocabulary, folder placement); `--fix` normalizes what's safely fixable. |
| `gald3r verify` | Check whether a task actually meets its acceptance criteria before you call it done (the completion gate). |
| `gald3r layout` | Inspect and migrate the `.gald3r/` SDLC artifact layout (legacy vs. current). |
| `gald3r schema-migrate` | Migrate a project's `.gald3r/` files forward to the current schema version (dry-run by default); `--restore-missing` also restores accidentally-deleted single-file artifacts from the embedded canonical snapshot. |
| `gald3r backup` | Take a full disaster-recovery snapshot of this project — a live, non-blocking SQLite backup of `gald3r.db` plus a tarball of the `.gald3r/` markdown tree, written under `.gald3r_backups/` with a manifest of content hashes. Reverse it with `gald3r restore`. |
| `gald3r restore <artifact-path>` | Reverse a `gald3r backup` snapshot — verifies the artifact's manifest hashes and that no other process currently holds `gald3r.db` open, then (only with `--apply`) restores the database and markdown tree and re-runs `gald3r validate` automatically. |
| `gald3r pack` | Build a portable, checksummed export of `.gald3r/` for moving a project to a different machine — no database and nothing credential- or identity-shaped (`.identity`, secrets, `.gald3r/config/`, `.gald3r/vault/` unless `--include-vault`). Reverse it with `gald3r unpack`. |
| `gald3r unpack <artifact-path>` | Reverse a `gald3r pack` export — verifies every file's checksum before writing anything, then (only with `--apply`) writes the `.gald3r/` tree (create/overwrite only, never deletes) and re-runs `gald3r validate` automatically. Run `gald3r db backfill` afterward to rebuild `gald3r.db` from the unpacked markdown. |
| `gald3r inbox` | Turn quick task/bug drafts left in the inbox into fully tracked tasks and bugs. |
| `gald3r wishlist` | Turn a free-form wishlist or notes doc into real, tracked tasks (read-only against the source doc). |
| `gald3r todo` | Commit to, list, show, promote, and sync TO-DOs — a lighter-weight commitment tier than a task. |
| `gald3r idea` | Capture, list, review, and promote ideas (`IDEA_BOARD.md`). |
| `gald3r adr` | Record an architecture decision with its context, alternatives, and consequences (`ADRS.md`). |
| `gald3r feature` | Validate the project's feature hierarchy. |
| `gald3r subsystem` | Validate and regenerate the project's subsystem hierarchy. |
| `gald3r prd` | Keep a formal, sign-off-ready spec for a feature — Product Requirements Docs that stay frozen once released, with a clean revision trail when they need to change. |
| `gald3r constraint` | Declare and enforce ad-hoc, per-project rules on agent writes. |
| `gald3r decision` | Record a binding call once and stop re-litigating it — decisions are append-only, so changing your mind mints a new, chain-linked decision (`supersede`) rather than editing or deleting the old ruling. |
| `gald3r dependency-graph` | See what's blocking what — a visual map of task dependencies, the critical path, and which blocked tasks are stuck waiting. |
| `gald3r medic` | Diagnose and repair problems in your project's gald3r setup — broken links, stale indexes, drifted files — fixing what's safe to fix automatically. `medic heal --heal board-ingest-drift` WARNs on files-without-rows and fat indexes; diagnose-only even with `--apply` (no silent layout-migrate, BUG-947). |
| `gald3r triage sweep` | Walk the open backlog and classify each task/bug `already_done` / `duplicate` / `obsolete` with git/board-verified evidence. Report-only by default; `--apply` closes only the verified, uncapped findings. See [`autopilot.md`](./autopilot.md)'s "Evidence-based triage sweep" section for the citation convention and the BUG-1140 cap contract. |
| `gald3r autopilot reconcile` | Salvage-sweep every `requires-user-attention` item with a stranded implementer branch: probe with `merge-tree`, land clean branches via the existing merge machinery (item returns to awaiting-verification citing the SHA), close conflicted ones only on triage-verified already-done evidence (caps respected), and stamp the rest with a durable needs-human marker the park gate can never re-park. Report-only by default; `--apply` executes. See [`autopilot.md`](./autopilot.md)'s "Stranded-branch salvage sweep" section. |

## Agent work

| Verb | What it does |
|---|---|
| `gald3r chat` | Built-in REPL agent loop — an ongoing conversation with your gald3r agent right in the terminal, picking up where you left off in this folder. |
| `gald3r run "<message>"` | Send gald3r a single instruction and get one response back — no session to manage, ideal for scripts and one-off asks. |
| `gald3r agent` | Define an agent's behavior — tools, prompts, guardrails — in a plain YAML spec file, then run it. |
| `gald3r go` | Claim the next eligible task, run one agent turn, self-verify. |
| `gald3r go-code TASK_ID` | Claim a specific task explicitly, run one agent turn, self-verify. Works whether your implementer provider is an API-key registry id or a CLI-subscription provider (`cursor-agent`/`claude`), which shells out to that provider's own CLI — so a Cursor- or Claude-Code-subscription-only host with no API key still works. |
| `gald3r go-bug BUG_ID` | Claim a bug explicitly, run one agent turn, self-verify, and resolve it on success — the bug-side counterpart to `go-code`. A clean exit alone is never enough to resolve a bug: it also requires independent evidence the turn actually touched the working tree. |
| `gald3r go-review TASK_ID` | Run one review-focused agent turn over a task and record the verdict — pass → completed, fail → back to pending. |
| `gald3r go-bug-review BUG_ID` | The bug-kind counterpart to `go-review`: a fresh reviewer turn (never the agent that fixed the bug) verifies the fix against its acceptance criteria, then resolves on pass or reopens on fail. |
| `gald3r go-status` | Read-only run-health check: the active loop's current state — alive, waiting, stalled, or stopped — without interrupting it. |
| `gald3r claim TASK_UUID` | Claim a task safely when more than one agent might grab it at the same moment — only one claimant wins. The lower-level primitive `autoclaim` and the `go*` verbs build on. |
| `gald3r autoclaim` | Autonomously pick, claim, and work eligible tasks off the local board. |
| `gald3r swarm` | Split a big batch of work across several agents running in parallel. |
| `gald3r xreview` | Cross-vendor review: pick a reviewer on a different AI vendor than whoever implemented the change, run your gates first, hand the reviewer a diff-only bundle, and re-dispatch the same implementer on any blocking finding — never merges, even on a clean pass. |
| `gald3r autopilot` | Drain a work queue, or run the full outer loop, unattended. `autopilot loop` runs until a stop condition, narrating live; `autopilot status`/`watch`/`events` inspect a running loop without interrupting it; `autopilot stop` asks a running loop to wind down. |
| `gald3r autopilot replay RUN_ID` | Forensic reconstruction of a past `autopilot loop` run from its durable event ledger and correlated logs alone — never a live process, never the database. Produces a per-lane timeline and flags anything the artifacts on disk can't substantiate as an invariant violation. Useful after an unattended run to understand exactly what happened, iteration by iteration. |
| `gald3r worktree` | Give each agent its own isolated git checkout to work in, so parallel agents never step on each other's uncommitted changes. |
| `gald3r housekeep` | Auto-commit safe, routine `.gald3r/` coordination changes so an autonomous run doesn't stall waiting on a human for housekeeping. |
| `gald3r msg` | Send an addressed message between a run's live agents — coordinator, a bucket, or a reviewer — without interrupting what they're doing (`send`/`list`/`approve`). |
| `gald3r handoff` | Emit and consume typed session-end handoff records — a structured, schema'd message for the next session to pick up, not hand-written prose (`emit`/`list`/`accept`/`consume`). |
| `gald3r report <text>` | Send a one-line progress update from inside an implementer/reviewer bucket straight to the run's coordinator, with no board/mailbox tool access required — it shows up in the coordinator's next brief. |
| `gald3r go-preflight` / `gald3r go-reconcile` / `gald3r go-verdicts` | Plumbing the swarm coordinators call to batch pre-flight checks, merge parallel work, and record verdicts in one call each — not typically run by hand. |
| `gald3r mission advance/checkpoint-threshold/exit-status` | Plumbing the `g-mission` state machine calls for its own gate/drain/budget progression and checkpoint timing — not typically run by hand, and does not run the mission loop itself (see `gald3r prompt get playbook.mission`). |
| `gald3r coordinator-preflight` | One read-only JSON snapshot for a fresh coordinator to consult before it dispatches — the controller housekeeping verdict, measured context, the focused task queue, and hot-inbox state, combined in a single call. Never claims work or mutates project state. |
| `gald3r sesh-goal` | Session-scoped goal register: record, remove, and list the headings you set for the current session only. Never touches `TASKS.md`, never creates a task, and lives outside project state — a lightweight way to keep an agent honest about what you actually asked for during a long session. |

## Understanding what happened

| Verb | What it does |
|---|---|
| `gald3r context` | See exactly how much of your context window an agent is actually using, measured from its session transcript — never a guess. Reports "unmeasured" honestly instead of fabricating a number when it can't tell. |
| `gald3r errors` / `gald3r trace` | Aggregate error/failure/warning trace records across every task. |
| `gald3r crash-stats` | See which of your Commands, Rules, Agents, Skills, and Hooks are actually firing during real sessions — not just installed, but used. |
| `gald3r parity-audit` | Compare the live gald3r CLI's verb tree against the legacy gald3r-agent reference verb set — a migration-completeness check, not a day-to-day command. |
| `gald3r muninn` | Query the local codebase knowledge graph — what calls this function, what depends on that file, what breaks if you change it — without grepping by hand. |
| `gald3r search` | Find text anywhere in the project, including files your normal search tool silently skips because they're gitignored. |
| `gald3r sync` | Maintainer-build-only (`gald3r_maintainer` tag; a release binary carries a hidden refusal stub): redeploy the neutral commands/rules/hooks/agents/skills set from `neutral_source/` to this source checkout's own IDE folders after editing them, replacing the untracked manual-copy workaround (BUG-538/BUG-541). Dry-run drift summary by default; `--apply` writes. |
| `gald3r lint` | Catch problems the moment they happen: syntax errors right after a file is written, missing component tags, or a shell command that looks dangerous before it runs. |
| `gald3r secrets` | Inspect the `.gald3rsecret` sensitivity-tier policy for this repo — which paths are list-only or no-cloud, and why. Reports the policy only; never prints file content. |
| `gald3r env` | Start and check the services your project needs to run (databases, local servers, test accounts) with one command instead of juggling several. |
| `gald3r connectivity-benchmark` | Measure how much slower coordination gets when offline vs. online, instead of guessing. Read-only and safe to run repeatedly. |

## Providers & configuration

| Verb | What it does |
|---|---|
| `gald3r init-providers` | Get set up to use local LLMs (Ollama, LM Studio, vLLM, llama.cpp) — auto-detects what's installed and writes the config for you. Full guide: [`providers.md`](./providers.md). |
| `gald3r providers` | List, add/remove/edit providers and models in `providers.yaml` without hand-editing YAML, validate the whole file, and store API keys securely in your OS's keyring. |
| `gald3r local-model` | Find out which local LLM your hardware can actually run well, and get the exact command to serve it. |
| `gald3r config` | Show or change your gald3r configuration — default provider/model, personal preferences, and platform install defaults (`get`/`set`/`show`). |
| `gald3r profile` | See or change your personal display preferences for this gald3r install (voice/tone and similar settings). |
| `gald3r user-profile` | Manage your global and per-project gald3r identity/preferences profile. |
| `gald3r pers` | Give your agent a distinct voice and style — switch between personality packs, or customize your own. |
| `gald3r skill` | Record and compile demonstrated workflows into real gald3r skills. |
| `gald3r skill-pack` | Add optional bundles of skills to your agent — browse, install, remove, or customize a skill pack. |
| `gald3r skills-lock` | Verify your installed skills haven't been tampered with, using a signed checksum lockfile. |
| `gald3r plugin` | Add new capabilities to gald3r from the community — install, update, and enable third-party skills without hand-editing your own project files. |
| `gald3r marketplace` | Curated one-click skill installers with provenance, an in-Throne skill builder that produces a valid CRASH skill shape, and a malware-review pipeline (static checks + prompt-injection screen) with a per-skill change log — every add/edit/update is scanned and logged, and a rejection always explains why. See [`marketplace.md`](./marketplace.md). |
| `gald3r policy` | Enforce your org's own rules automatically — block or allow actions against a shared policy (Team/Org tier; a no-op on free/retail installs). |
| `gald3r prompt` | Judgment/prompt-asset library (role briefs, rubrics, playbooks, voice). `prompt list` shows known assets; `prompt get PROMPT_ID` prints one rendered, and `--out FILE` writes it to a file instead of stdout — useful when an asset is large enough that a terminal or tool-output cap would otherwise silently truncate it. Shipped gald3r-internal assets are refused on this surface except a narrow agent-retrievable allowlist; assets from an installed plugin's own prompt directory remain fully gettable. |
| `gald3r codename` | Manage the internal codename/nickname registry for this project — map internal codenames to plain names, legacy forms, and definitions, and `resolve` fuzzy description-shaped queries against it. Renaming a codename here never touches code or folders. |
| `gald3r project-type` | Switch between preset workflows (solo dev, team, enterprise, ...) that tune how gald3r behaves in this project. |
| `gald3r pricing` | Look up what an LLM model costs per token, right from the terminal. |

## Coordination — your projects and agents, talking to each other

Most AI tooling stops at one assistant in one repo. These verbs are what make gald3r *multiplayer*:
projects that answer each other's questions, agents you can message mid-run, and shared state
across your machines and your team.

| Verb | What it does |
|---|---|
| `gald3r valk` | Let your projects and agents talk to each other — and to you — while they work. Ask another project a question and get a grounded, cited answer from its real context; message a running swarm without interrupting it; keep state in sync across machines and teammates. |
| `gald3r workspace` | Coordinate work across multiple related projects — read incoming requests from linked repos, check what's safe to touch before a multi-repo change, and keep member repos in the expected shape. |
| `gald3r connect` | Link a provider account with a device code, like signing into a streaming app on a TV (this is the world_tree device-code sign-in itself — for a third-party AI provider, use `gald3r providers add`/`set-key` instead). See [`providers.md`](./providers.md) for real, current output. |
| `gald3r login` / `gald3r logout` | Sign in or out of your gald3r account so team and multi-device features work — `login` opens a device-code flow by default (visit a URL, enter a short code); pass `--token` for CI/power-user use. |
| `gald3r auth reconcile` | World_tree identity maintenance — supersede this project's locally-generated user id with world_tree's official one, once you're properly signed in. |
| `gald3r billing checkout/portal` | Open a hosted Stripe Checkout or Customer Portal page for your gald3r plan — world_tree is the merchant of record, so no card details ever pass through the CLI. |

## Release & distribution (relevant if you're shipping *your own* gald3r-based tool)

| Verb | What it does |
|---|---|
| `gald3r ship` | Ship a release: bump version, promote CHANGELOG, tag, update badge. |
| `gald3r push-gate` | Catch a missing CHANGELOG entry or version bump before a release push goes out, not after. |
| `gald3r release` | Push a built release out to a remote destination, and let your team know it went out. |
| `gald3r template` | Get ready-made CI/release config files (GitHub Actions, release-please, ...) written into your project instead of writing them by hand. |
| `gald3r shutdown` | Safely stop persistent gald3r MCP, Valkyrie, and autopilot processes before an in-place binary upgrade — especially on Windows, where a running process can lock the binary (`--dry-run` previews without signalling anything; `--force` kills stragglers left after the graceful `--wait` window). See [Install → Stop the local fleet](./install.md#stop-the-local-fleet-before-replacing-files). |
| `gald3r install update` | Download, verify, and install the latest signed gald3r_core release, replacing whatever `gald3r` currently resolves to on `PATH`. |
| `gald3r install throne` | Download, verify, and stage the signed Gald3r Throne desktop app from its own GitHub Releases. |
| `gald3r platform` | Keep gald3r's support for Cursor, Claude Code, Copilot, and every other AI IDE up to date as their own docs change. |
| `gald3r throne` | Open Gald3r Throne, the visual desktop companion to this CLI. |
| `gald3r acp` | Compatibility ACP stdio adapter, for editors like Zed that drive an agent over the Agent Client Protocol. |
| `gald3r broadcast` | Show cached Gald3r Labs vendor broadcast alerts (release notices, advisories). |

## Other

| Verb | What it does |
|---|---|
| `gald3r vault` | Build a searchable knowledge base from the docs, repos, and web pages you feed it — plain markdown files you own, not locked away in a database. |
| `gald3r memory` | Scoped memory records (user/project/workspace/team/company/client) — small, individually-addressable facts, not a session-summary document — plus chat-native recall over them and your vault notes together. |
| `gald3r wiki` | Compile your captured sessions into a browsable project wiki (`wiki compile`) and list what it holds (`wiki catalog`) — runs automatically at session end on installed platforms, and each new session starts with the catalog in context when real articles exist. |
| `gald3r tel` | Watch your terminal output for patterns and react automatically — alert, log, or trigger a follow-up command. |
| `gald3r telemetry` | Opt in (or out) of local-only usage telemetry — a count of crashes, your gald3r version, and OS/architecture; off by default, nothing ever leaves your machine. |
| `gald3r ui-test` | Drive real mouse/keyboard/screenshot steps against a native desktop app to verify a UI change actually works, not just that the code compiles. |
| `gald3r browser` | Drive a real headless Chrome/Chromium browser — open a page, run a scripted click/type/extract session, capture a screenshot, or hand a task to the model-driven browser agent loop (`open`/`run`/`screenshot`/`agent`). |
| `gald3r vision eval` | Ask a multimodal model to judge an image (file path, URL, or base64) against a plain-language question, and get back a structured pass/fail/confidence/reasoning verdict. |
| `gald3r voice` | Push-to-talk voice for your gald3r chat/run loop — local ASR/TTS, always shown as on-screen text too, never an audio-only path (`chat`/`activation`/`status`). |
| `gald3r learn` | Have gald3r learn from your sessions — extracts durable facts from recent chats so it remembers them next time, instead of relearning the same thing every session. |
| `gald3r compress-memory` | Shrink your `AGENTS.md`/`CLAUDE.md` memory files to save context space — leaves gald3r's own managed sections untouched. |
| `gald3r mcp` | Expose gald3r's prompt/judgment library, chat-native memory recall, and a typed record-mutation tool per `.gald3r` record class (task/bug/adr/todo/idea/decision/prd/feature/subsystem/release/constraint/document) to any MCP-compatible client over stdio. See [MCP tools reference](./mcp-tools.md) for the full list. |
| `gald3r hearth` | Run a small local background service so other tools can check your project's status without shelling out to the CLI each time. |
| `gald3r scheduler` | Reads `.gald3r/config/HEARTBEAT.md` on a cron clock, runs watchdogs, and enqueues agent routines onto the wakeup queue — the clock behind Throne's settings GUI, distinct from `gald3r hearth` (`start`/`stop`/`status`/`tick`/`list`). |
| `gald3r gfix` | Build, redact, and file a diagnostic field report against this repo's own issue tracker — gathers evidence only from gald3r's own surfaces (version, OS shape, terminal state), never your repo contents or project paths, and requires an explicit confirm step before anything is actually filed. |
| `gald3r gfix-harvest pull` | Pull this project's own classified inbound issues back from world_tree (the reports `gald3r gfix` files) and store them locally. |
| `gald3r harvest-issues` | Classify inbound GitHub issues as defect / enhancement / hostile / non-work (dry-run only — no GitHub writes, no board writes). |
| `gald3r harvest-absorb` | Turn already-classified issue results into `.gald3r/` bug/task inbox drafts — writes drafts only; never claims work and never starts an agent run. |
| `gald3r completion` | Generate the shell autocompletion script for your shell. |

## Attempt budgets, once you're running unattended

Autonomous runs (`autopilot`, `swarm`, `go-go`-style loops) cap how many times the same item is
re-attempted, and the cap applies only to **implementation** attempts — a task or bug that keeps
failing review gets a bounded number of re-implement passes before it's handed back to you as
`requires-user-attention`, rather than looping forever. **Review turns are free**: reviewing an
item doesn't consume any of its implementation budget, so a slow reviewer queue never starves
the implementer side, and a fresh review can always be requested without "spending" anything.

## Update flow and the Windows dual-exe

`gald3r install update` downloads the latest signed release for your OS, verifies its checksum,
and replaces whatever `gald3r` currently resolves to on `PATH` — `--dry-run` prints the plan
without touching anything network-side; `--release TAG` pins a specific version instead of
latest; `--require-verification` fails closed if the download's integrity can't be checked. On
Windows this is a **safe, fail-closed transaction across both shipped binaries**: `gald3r.exe`
(console subsystem — the one you invoke from a terminal or script; always prints and returns a
real exit code) and `gald3rw.exe` (GUI subsystem — used for spawns from a process that must
never flash a console window, such as an IDE hook or the Throne desktop app). If either binary
is locked by a running process, the update aborts with a clear message naming the locked file
and rolls back any partial replace rather than leaving you with a mismatched pair; `gald3r
shutdown` (above) stops the common lock-holders first. `gald3r doctor` reports when the two
binaries disagree on `--version`. Always invoke `gald3r`, not `gald3rw`, from a terminal or
script.

---

Not every verb above is exercised in the [quickstart](./quickstart.md) — this page's job is to
be a map of what exists, not a tutorial for each one. `gald3r <verb> --help` (and `gald3r <verb>
<subverb> --help` where a verb has subcommands) is the authoritative usage text for anything you
want to actually run.
