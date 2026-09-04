# Changelog

Release notes for the **gald3r** binary. Written for people using gald3r, not for people building
it — engineering-level history lives in the development repository and is not mirrored here.

Format follows [Keep a Changelog](https://keepachangelog.com).

## Versioning

The binary shares a version line with the [gald3r framework](https://github.com/Gald3r-Labs/gald3r).
A given major.minor release means the same thing in both places:

| Repository | Ships |
|---|---|
| `Gald3r-Labs/gald3r` | the framework and templates — CRASH components, project scaffolding, platform overlays |
| `Gald3r-Labs/gald3r_core` (this repo) | the compiled `gald3r` binary that runs them |

**gald3r 4.0** is the first release where the framework ships with a compiled binary. Framework
v3.x was template-only and installed per-IDE; 4.0 unifies both halves under one version, so
"gald3r 4.0" names a complete, matched pair.

Prereleases are tagged `vX.Y.Z-beta.N` and marked as prereleases on GitHub; only full releases are
recommended for general use.

---

## [5.0.0-beta.51] - 2026-09-04

_No public release notes were provided for v5.0.0-beta.51._

## [5.0.0-beta.50] - 2026-09-03

Further notes for the next cut go here. Hook first, then topic headings
and bullets. Leave this placeholder until that copy is ready.

## [5.0.0-beta.49] - 2026-09-02

_No public release notes were provided for v5.0.0-beta.49._

## [5.0.0-beta.48] - 2026-09-02

Your project agents can now talk, and you can put all of them in one room.

### Talk to your project agents

- The per-project chat answers through a model when one is available: it reads the project first (search, board, status) and asks a local runtime such as Ollama or LM Studio to answer over that context. Name a provider in the project config to choose one; nothing paid is ever picked for you. With no model reachable you still get the grounded structured answer, clearly marked as such, and every answer shows which model or fallback produced it.
- The Council Room convenes every project agent in the workspace at once: pick who joins, mute or remove anyone, ask one question, confirm how many agents it will reach, and watch the answers land in one transcript attributed to each project. Open it from the Workspace page.

## [5.0.0-beta.47] - 2026-09-02

Throne starts showing you the whole workspace, and the engine stops accepting work nobody can verify.

### Throne: see the workspace, not just the project

- A Workspace graph opens the Workspace page: every project in your workspace as a node with its name and project type, a lit indicator when that project's Valkyrie connector is running, and a badge when its project agent is fitted out. Projects the manifest names but cannot resolve are listed separately instead of silently dropped.
- The Momentum board now lives inside the Project page, so opening a project shows its totals, overview tiles and tasks by status without a detour.
- The coordinators view gains an Organization scope beside the per-project one, and tells you plainly when you do not have access to an organization's presence rather than showing an empty room.
- When world_tree is unreachable, the coordinator graph still shows a Valkyrie connector that is running on this machine.

### Throne: decisions, history and settings

- An Approvals queue on the Mission Control page lists actions agents are waiting on; approve with an optional note or reject with a required reason, backed by a durable ledger.
- Chat history is real: sessions come from a ledger with no cap and page back to the oldest one. Recording sessions from the assistant is the next step.
- The scheduler settings page shows whether the local scheduler is running, its process id and last tick, and lets you change the cadence of the two default inbox jobs.
- The AI Assistant provider picker only offers providers the app can actually chat through, and says so when saved credentials exist but none are usable.
- Every gated page uses the same upgrade affordance: required tier, reason, and a button to billing.

### Engine: acceptance criteria are part of coding now

- Claiming a task without acceptance criteria warns you; finishing it is refused until criteria exist, and one command can add them and complete in the same call.
- A review PASS on a task with no criteria is recorded as a FAIL with the reason "No acceptance criteria to test", so the item goes back to the implementer with the fix spelled out.
- `gald3r task claim` is a verb.
- Two project-agent jobs run every 15 minutes by default: the agent inbox poll and a free, local read of the WPAC file inbox. No host scheduler, no paid calls.

### Fixes

- Human-action requests sent through the ask verb now always reach the sibling project's inbox, even when the server answered.
- The WPAC inbox stops re-listing closed messages whose files were deleted.
- Throne's WebView polls carry the native session token through a host-side request, ending the false "session expired" bounce after login, and the frontend typecheck passes again.

## [5.0.0-beta.46] - 2026-09-01

Your project's agent can now address another project's agent on the event
wire — delivery still waits on a server catalog deploy.

### Ask another project

- Same-machine asks still write the local mailbox, as before.
- `gald3r project-agent ask --to-project <id> …` queues a remote question and
  flushes the outbox immediately.
- The resident Valkyrie inbox loop now drains that same outbox on every tick,
  so answers are not stranded on disk.
- You need a signed-in session. Team coordination (or the Redis coordination
  add-on) is required; solo and harvest keys are refused.
- Production `api.gald3r.ai` does not yet list the new event types in its
  catalog (checked against the live OpenAPI enum). Source has them; the
  running API does not. Until that deploy, a remote publish will be rejected.

### Board collisions, not folder sync

- Incoming field-level collisions are kept as a visible conflict log.
  Nothing last-write-wins. Status reports a pending-conflict count.
- This is not instant sync of `.gald3r/` folders, task files, or the local
  database. Server board writers stay gated.
- Do not treat this cut as a multi-user coordination product. The
  verification ladder is still the wedge.

### Left out on purpose

- No `task claim` alias, Longship chat-join, entitlement-flag seed, or
  error-intake work in this binary.
- No Postgres ingest for mailbox or conflict rows.

## [5.0.0-beta.45] - 2026-08-31

Your repo can answer you now. Talk to it from the terminal or a slash
command — same brain as Throne — instead of opening a second editor
window on the board.

### Talk to the project

- `gald3r project-agent status`, `ask`, and `chat` — or `@g-project-agent`
- Same engine Throne and the resident connector already use
- Set `GALD3R_AGENT_FITOUT` to pick the domain fit-out (terminal and Throne)
- Agents offer Valkyrie when you need a live question to another project,
  not only when you name it

### Worktrees that clean up

- Windows no longer dies on the first Permission denied from Defender,
  Cursor, or the search indexer
- Cleanup retries the lock, names leftovers that still will not delete,
  and reclaims folders git already unregistered

### Autopilot you can actually steer

- `--gauntlet` on every `@g-go*` command (off by default; same knobs as
  the autopilot loop)
- `-1` turns a numeric cutoff off — no more fake `999999999`
- Dispatch briefs can be a file path instead of a 4–5 KB paste
- A graceful stop finishes in-flight work; only `--now` abandons it
- Merged work is verified, not re-implemented from a stale bug text

### Faster lookups, safer vault

- `gald3r search` is about 10× faster on a large tree (seconds, not
  minutes) — agents stop falling back to blind grep
- Vault ingest commits the vault repo after a write, so research is not
  sitting uncommitted on disk

### New commands

- `/g-cm` — CARVER matrix for what to do next
- `/g-gilf` — senior-expert answers, no 101-level padding

## [5.0.0-beta.44] - 2026-08-28

This beta closes a serious board-integrity defect class, removes the biggest
throughput bottleneck in autonomous runs, and cleans internal-only content out
of the installed payload.

**Your completed work stays completed.** A multi-day defect class that could
silently revert already-resolved tasks and bugs during autonomous merges is
closed structurally: agent worktrees no longer see board records at all, commit
safety fails closed whenever a merge or conflicted state is in progress, claim
cleanup can no longer overwrite a resolution that landed in the meantime, and
the verdict-recovery safety nets were rebuilt to survive the new isolation.
Completing a task now also requires its acceptance criteria to be ticked at
write time — the evidence gate fires when the status changes, not hours later.

**Autonomous runs keep moving.** Finish-gate checks across pending branches now
run in parallel instead of one at a time, so a single slow check no longer
stalls everything queued behind it. Each agent lane is watched individually by
silence rather than a shared deadline — one dead lane frees its slot while
healthy siblings keep working, and results are credited the moment each lane
reports instead of waiting for the whole batch. Repeatedly-failing merge
attempts are remembered and skipped instead of retried from scratch every run.

**Discipline your agents inherit.** New shipped protocols raise the floor for
every agent driving the system: a three-verdict verification contract
(VERIFIED / NOT VERIFIED / INCONCLUSIVE) for proving a fix actually works, a
flake-handling protocol with a shared known-flaky-tests index that stops
re-diagnosing the same failure, a standing merge-conflict-resolution brief with
hard guardrails, and a reviewer flow that gathers evidence in parallel before
the judgment pass — measured at a fraction of the previous review cost.

**Cleaner installs.** Maintainer-only content that had leaked into installed
projects — internal release-pipeline references that could misdirect your
agents — is scrubbed from the shipped payload, a release gate now blocks it
from ever shipping again, and upgrading overwrites the affected files in
existing installs. Bug routing is now explicit: defects in your own project go
on your board; suspected defects in gald3r itself go upstream via `gald3r
gfix`. New projects also receive `COMMANDMENTS.md`, an owner-editable home for
the guiding principles above any single rule (shipped intentionally blank).

**Also in this build.** A backlog Momentum Board inside Throne (project page,
landing module, and fleet tiles from live data), an update alert when a newer
release is available, and a code-navigation tool (`gald3r graph
explore|impact|callers` plus an MCP equivalent) that returns a symbol's
declarations, callers, and blast radius in one call — byte-identical to what
your editor reads.

Full technical detail is in CHANGELOG.md.

## [5.0.0-beta.43] - 2026-08-27

This beta makes the autopilot genuinely self-healing and hardens the paths users
actually hit.

**Autopilot that finishes what it starts.** The engine now merges completed work
itself with an automatic conflict-resolution ladder, salvages interrupted work
before anything is lost (dirty worktrees are preserved automatically and stranded
branches are garbage-collected), unblocks dependent items the moment their
blockers complete, and runs an honesty gate at startup that refuses to launch on
unverified board state. Agent lanes now report liveness continuously, and review
verdicts apply end-to-end without manual intervention — including a
severity-based cap that routes high-impact closures through explicit
confirmation.

**Safer economics.** Metered agent spawns are classified before launch and
refused without explicit opt-in; the authenticated account identity is shown
before anything spends. Context-window telemetry for agent lanes now reads the
vendor's own transcript, so burn gates measure instead of guessing.

**Memory that compounds.** Sessions now compile into a searchable project wiki
automatically at session end, and each new session starts with that knowledge
injected — bounded, and only when there is real content.

**Quality of life.** Legacy provider configuration files migrate automatically
with timestamped backups instead of being refused. `gald3r db query` offers
fail-closed, read-only SQL over the project database. Release cuts now stamp
every shipped item with its release and publish backlog momentum stats. The
validator repairs previously hard-erroring status values. Numerous 3D
entertainment-stage improvements landed: large static geometry batching,
per-prop interaction hit-boxes (72% fewer interactive objects), character
pose-rendering hardening, and verified demo + broadcast modes.

Full technical detail is in CHANGELOG.md.

## [5.0.0-beta.28] - 2026-08-16

**The resilience build.** The loop now survives its own worst moments — and
leaves a complete forensic trail when anything goes wrong.

- **One stuck task can never crash a run again.** The crash class where a
  requeued work item could take down the whole loop mid-run is fixed at three
  layers: the root ordering bug, graceful refusal instead of failure, and a
  recovery net around the scheduler itself.
- **Budget exhaustion finishes what it started.** When the work budget runs
  out, in-flight items complete, merge, and get reviewed before the run ends —
  nothing is abandoned half-done, and never-started items report as exactly
  that (one summary line, not a wall of false failures).
- **Blocked work stays blocked.** The work picker now honors every dependency
  mechanism (blocked-by, unmet dependencies, gate markers) through one shared
  rule — a blocked item can no longer burn a full agent turn.
- **Forensic replay.** New: `gald3r autopilot replay RUN_ID` reconstructs any
  past run entirely from its durable records — per-lane timelines, what showed
  life on which channel, and flags for anything the records cannot
  substantiate. Debugging a run is now one command, not archaeology.
- **Large playbooks can't be silently truncated.** Oversized prompt assets are
  delivered as file references (`--out` on `prompt get`, automatic in the MCP
  tool) so an agent always reads the whole document.
- **Issue triage foundation.** A deterministic classifier for inbound GitHub
  issues with injection screening built in: hostile content quarantines,
  feature requests route to a human, issue text is always data — never
  instructions.

To verify a download: each release asset ships with a SHA-256 checksum file;
compare with `certutil -hashfile <asset> SHA256` (Windows) or
`shasum -a 256 <asset>` (macOS/Linux).

## [5.0.0-beta.27] - 2026-08-16

**The supervision build.** This release is about one promise: when the autopilot
loop is running, it is either shipping work or telling you exactly why not — never
silently spinning.

- **Reviews now finish everything.** The review phase can verify bug fixes as well
  as tasks, so a queue of completed-but-unverified bug work drains instead of
  accumulating. A new `gald3r go-bug-review` verb gives any bug a fresh-eyes
  verdict turn on demand.
- **Fresh work wins.** The work picker now remembers, across runs, which items have
  repeatedly produced no real change, and ranks fresh actionable work above them.
  Nothing is ever dropped — repeat offenders just stop crowding out your queue.
- **Safety gates always arm.** When a provider exposes no cost or context
  telemetry, the loop's burn ceiling, context stop, and time gates no longer sit
  inert: they arm on computed proxies (wall-clock, iteration count, output volume)
  and say so plainly in a once-per-run banner. A run that keeps iterating without
  completing anything now trips a no-progress breaker instead of running for hours.
- **Silent workers get reclaimed.** A worker that stops producing output is
  detected, cleanly killed (including its child processes), and its lane is
  immediately refilled with new work — one stalled worker no longer starves the
  rest of the run.
- **Briefs trust your board.** Agent briefs now state explicitly that the task's
  own recorded notes and history outrank any repository document that contradicts
  them — an agent can no longer refuse work because it found a stale page.
- **Consistent health reporting.** `gald3r doctor` and the running loop now agree
  on whether a project participates in workspace coordination, using one shared
  detection rule.

To verify a download: each release asset ships with a SHA-256 checksum file;
compare with `certutil -hashfile <asset> SHA256` (Windows) or
`shasum -a 256 <asset>` (macOS/Linux).

## [5.0.0-beta.26] - 2026-08-15

**The throughput build.** beta.26 makes the loop spend its budget on work an agent can
actually finish — and hand everything else to you, once, instead of retrying it forever.

- **The loop stops re-attempting items a human has to unblock.** A failed review whose
  remaining acceptance criteria are human-gated parks the item in your attention queue
  after one attempt. Any item caps at three implement attempts per run, then parks with a
  named reason. No more burning full model turns writing "no new changes" essays.
- **The picker skips un-implementable work**: items whose remaining criteria are
  human-gated are never offered to an implementer, and an item that merges with no
  changes is not re-offered in the same run.
- **Parallelism holds at your `--lanes` setting** — a worker that dies at spawn frees its
  slot for the next item immediately.
- **Windows reliability**: the worktree marker write now rides through antivirus/indexer
  file locks instead of killing the worker, and projects that keep `.gald3r/` out of git
  no longer see refused staging messages every cycle.
- Per-iteration summaries report real review PASS/FAIL counts.

**Recommended before your first beta.26 run:** `gald3r backlog-curate` (or the
`@g-backlog-curate` command) to sweep stale and human-gated items off the active floor —
then the loop's whole budget goes to real, landable work.

**Verify a download:** check `SHA256SUMS` and run `gald3r --version` — expect
`5.0.0-beta.26`.

## [5.0.0-beta.25] - 2026-08-15

**The stability build.** beta.25 consolidates two weeks of rapid field-driven fixes into
one hardened release: the loop tells the truth about its own state, upgrades are safe, and
budgets finally mean what they say.

- **Budget you can explain in one sentence: it is the number of tasks and bugs the loop
  will attempt to handle.** Reviews never consume budget — they are part of the workflow,
  not new work. A task that implements, fails review, and retries costs two attempts, and
  the end-of-run summary reports exactly that.
- **Completed work gets finalized first.** When items are waiting for review, the loop
  dispatches reviewers before starting anything new — and if the coordinator under-reports
  the review queue, a deterministic fallback spawns the reviewers anyway.
- **The board never lies anymore.** A failed or empty merge rolls the item back and
  requeues it instead of leaving a false "done"; claims release themselves; killed workers
  leave logs, ledger events, and released claims behind; end-of-run summaries report real
  PASS/FAIL counts and a truthful final state.
- **Safe upgrades.** `gald3r install update` detects wrapper scripts and already-damaged
  files on PATH, updates the real install instead of overwriting a shim with binary bytes,
  and warns when your shell would still resolve an old copy. `gald3r doctor` flags stale
  binaries and version mismatches.
- **MCP servers launch reliably.** Generated MCP configs now point at the exact installed
  binary instead of whatever a PATH search happens to find.
- **No more Python-era instructions.** Every guide, skill, and playbook now uses the plain
  `gald3r` command — nothing asks you for a `uv` environment.
- Agent spawns that could hang silently (a known Windows launcher edge) are refused loudly
  before they start, and finished runs kill their entire process tree — no leaked workers
  burning quota overnight.

**Verify a download:** check the published `SHA256SUMS` file against your binary, and run
`gald3r --version` — it should print `5.0.0-beta.25`.

## [5.0.0-beta.24] - 2026-08-15

_(Nothing staged yet. Add short, business-level, user-facing notes here before the next cut.)_

## [5.0.0-beta.23] - 2026-08-14

Autopilot now runs implementers in parallel by default. Use `--no-code-swarm` if you want the old one-at-a-time coding path. Sibling count defaults to 5 (recommended 3–8; higher values are allowed and warn). `--budget` is an attempt cap; `0` means unlimited.

Vault-first research is on: agents search your local knowledgebase before the internet, and memory recall treats ingested research notes as first-class results.

A local scheduler can wake HEARTBEAT rows without keeping Throne open. Doctor `--apply` upgrades a legacy WPAC inbox to the thin index plus per-message files.

## [5.0.0-beta.22] - 2026-08-14

BUGS.md is now a slim table of links, the same shape as TASKS.md. Filing or
rebuilding bugs no longer reprints every bug as a multi-paragraph dump in the
index. Already-corrupted projects: run `gald3r bug regenerate-index --apply`.
The full description still lives in each `bugs/bugNNN_*.md` file.

## [5.0.0-beta.21] - 2026-08-13

_No public release notes have been written yet for the next tag. Replace this line with
business-level notes before cutting._

## [5.0.0-beta.20] - 2026-08-13

Autopilot (`gald3r autopilot loop` / `@g-go-go`) now runs multiple implementers at once by
default. Use `--no-code-swarm` when you need one-at-a-time (debugging, or a host that cannot
run several coding agents together). Merge-back stays serialized so parallel worktrees do not
wedge the git lock.

## [5.0.0-beta.19] - 2026-08-13

Field fixes from the beta.17 Windows/Cursor run, for testers who already installed beta.18.

**Conductor vs parallel work.** `@g-go-go` / `autopilot loop` stays sequential (that honesty shipped in beta.18). This cut makes the sequential path survivable: the liveness heartbeat can see bucket output, silent buckets are killed after **30 minutes** by default (was 5), and a still-running bucket writes a live log so a kill is diagnosable.

**go-bug honesty.** If the agent’s last word is `VERDICT: FAIL`, the bug stays open. A docs-only commit no longer marks it Resolved. Empty bug specs are filled from the markdown file (and the database if needed); a Critical bug with only a title is not dispatched. Briefs inside an isolated worktree say so, and tell the agent to commit on that branch.

**One loop per repo.** Starting a second `autopilot loop` while one is still alive is refused. `autopilot stop --now` also tears down leftover loop processes recorded on sibling run markers. On Windows, INFO progress lines no longer show up as `NativeCommandError` crash spam.

**Graceful stop during a sequential bucket.** `autopilot stop` (without `--now`) skips any bucket that has not started yet and gives the one already running three minutes, then kills it. You do not wait out a 25-minute coding turn. `--now` still stops immediately.

**Stranded claims after an upgrade.** Starting a new loop releases leftover `go-bug` in-progress claims from a previous binary (the ones that blocked redispatch after upgrade). Bugs a human marked in-progress are not touched.

**Database MCP in the brief.** If Oracle or another MCP is configured (for example port 6002), the implementer brief says so. Agents should use it instead of claiming they need a DBA.

**Install update vs leftover processes.** If another `gald3r` process still has the old binary open, update still lands via rename-aside, then lists those PIDs and tells you to run `gald3r shutdown --force` so the next `gald3r` is the new build.

**Still not this cut.** GitHub filing polish beyond the beta.18 gfix hollow/`gh` warning (reporter identity ranking). 16A review-phase items (L8–L11, L4, L7) remain unconfirmed until a review phase is actually run.

## [5.0.0-beta.18] - 2026-08-13

Autopilot loop docs now match the binary: `@g-go-go` runs one implementer at a time. Parallel
coding is `@g-go --swarm`, not a hidden loop flag.

Field reports (`gald3r gfix`) warn in the draft itself when GitHub CLI cannot auto-file, fill
watchdog evidence from the last captured kill when you type the class by hand, and refuse to
auto-file an empty watchdog report unless you pass `--force`.

## [5.0.0-beta.17] - 2026-08-13

This build is the field follow-up to 5.0.0-beta.16. Autopilot now treats a finished
no-commit turn as real work instead of an idle failure, so a correct "nothing to
commit" triage no longer trips the capacity breaker and kills a run that still has
queued work. Claims held by buckets that die at run-end are released instead of
occupying implementer slots forever.

On a Cursor host, review without an explicit provider prefers the Cursor agent over
a missing local model. A transport error is a run/provider problem, not a mass
FAIL of every task under review. A complete implementation is not marked failed
because teardown hit a brief unauthorized blip. Review verdicts still count when
the JSON file write is blocked, as long as the reviewer stated a verdict in text.

`constraint add --rule-file` works without a dummy positional argument; update and
delete can repair a bad row. `gfix` warns at draft time if `gh` is missing or not
logged in.

Primary-checkout git applies wait on an index lock and retry instead of deleting
the lockfile.

## [5.0.0-beta.16] - 2026-08-12

**Spawned agents now receive their complete instructions — every time, at any size.**
This release closes the last known gap in the orchestration pipeline: agent briefs are
now delivered whole, never truncated.

- **Complete-brief delivery.** Large agent briefs (coordinator, implementer, and
  reviewer) are written to a file inside the project's own log directory, and the
  spawned agent receives a short pointer to read it — instead of squeezing the brief
  through the operating system's command-line length limits. Briefs of any size now
  arrive intact on every OS, and each delivered brief is kept on disk so you can see
  exactly what an agent was told.
- **Windows launcher-shim hardening.** Some agent CLIs on Windows launch through shell
  wrapper scripts that can mangle multi-line text of any length. gald3r now detects
  those wrappers and automatically switches to file delivery — even for small briefs.
- **Multi-line input everywhere.** Every free-text option across the CLI (task and bug
  descriptions, notes, memory bodies, and more) now accepts `--<flag>-file PATH` and
  `-` for stdin, so multi-line content never has to survive shell quoting on the
  command line.
- **Truthful self-diagnostics.** `gald3r selftest` now checks hook wiring for every
  installed platform overlay (not just Claude), understands the opt-in git-hooks
  setup instead of reporting a false failure, and verifies active project constraints.
  `gald3r doctor` gains a PATH check that flags stale or shadowed `gald3r` binaries —
  including version mismatches between what's installed and what your shell actually
  runs.
- **Local AI: Unsloth Studio documented.** The local-AI guide now covers Unsloth
  Studio setup (keyed endpoint, port 8888) alongside Ollama, LM Studio, vLLM, and
  llama.cpp.

**Verify a download:** check the published `SHA256SUMS` file against your binary, and
run `gald3r --version` — it should print `5.0.0-beta.16`.

## [5.0.0-beta.15] - 2026-08-12

This release delivers the first real autonomous throughput on every supported
configuration — and gives users a one-command way to report anything that still goes
wrong.

- **Worker agents actually work now.** The agent CLI is invoked correctly for
  non-interactive use (the missing print-mode and trust flags explain both the earlier
  silent hangs and the "completed but changed nothing" runs), a turn that produces zero
  output is honestly classified as a failure instead of a completion, and every worker's
  own output is captured into its per-task log.
- **Stale workspaces refresh themselves.** A previously-attempted task's working copy is
  automatically recreated against the current code before dispatch, so finished work can
  always merge; a new `--stale-base-action` flag keeps the choice in your hands.
- **Interrupting a run is clean.** Stopping releases the claims of exactly the workers it
  terminated — no leftover locks, no manual cleanup.
- **NEW: `gald3r gfix` — one-command bug reporting.** When something fails, gald3r can
  draft a complete report from its own logs, automatically scrub personal information
  (usernames, hostnames, paths, secrets — and it shows you what it removed), let you edit
  the draft, and only after your explicit confirmation open a prefilled GitHub issue.
  Duplicate reports are detected and pointed at the existing issue. You control the offer
  level (off / critical-only / all), asked exactly once.

To verify a download: checksums ship alongside every asset on the release page; Windows
binaries are Authenticode-signed, macOS binaries notarized.

## [5.0.0-beta.13] - 2026-08-12

This release restores the autonomous pipeline's full operating knowledge and makes its
safety checks real.

- **The autopilot coordinator got its complete instructions back.** The condensed
  internal briefing that made autonomous coordinators unreliable at delegating work has
  been replaced with the full, field-proven version — restored from the era when
  multi-hour unattended runs worked, carried inside the binary, and adapted to the
  current engine. Coordinators now receive complete work-item detail up front (acceptance
  criteria, descriptions, notes), so they act immediately instead of burning time and
  tokens re-reading the project.
- **Pipeline guidance restored across the board.** The built-in playbooks for the go
  command family were audited line-by-line against their proven predecessors; dozens of
  operating rules that had been lost to over-condensation are back, and every statement
  about what the binary enforces was verified against the actual code — several stale
  claims were corrected to say honestly what is and isn't automated yet.
- **`gald3r go-preflight` now performs the workspace safety checks it describes**:
  cross-project inbox conflict detection, and a clean-status check across workspace
  member repositories, both with clear remediation output when they fire.
- **Verified before shipping**: full test pass across the affected engine, plus a real
  end-to-end autonomous loop run (live coordinator, live worker agents, real tasks
  completed) on this exact build.

To verify a download: checksums ship alongside every asset on the release page; Windows
binaries are Authenticode-signed, macOS binaries notarized.

## [5.0.0-beta.12] - 2026-08-11

_No public release notes have been written yet for the next tag. Replace this line with
business-level notes before running `gald3r release stage --apply`._

## [5.0.0-beta.11] - 2026-08-11

_No public release notes have been written yet for the next tag. Replace this line with
business-level notes before running `gald3r release stage --apply`._

## [5.0.0-beta.10] - 2026-08-11

_No public release notes have been written yet for the next tag. Replace this line with
business-level notes before running `gald3r release stage --apply`._

## [5.0.0-beta.9] - 2026-08-11

_No public release notes have been written yet for the next tag. Replace this line with
business-level notes before running `gald3r release stage --apply`._

## [5.0.0-beta.8] - 2026-08-10

_No public release notes have been written yet for the next tag. Replace this line with
business-level notes before running `gald3r release stage --apply`._

## [5.0.0-beta.7] - 2026-08-10

_No public release notes have been written yet for the next tag. Replace this line with
business-level notes before running `gald3r release stage --apply`._

## [5.0.0-beta.6] - 2026-08-10

_No public release notes have been written yet for the next tag. Replace this line with
business-level notes before running `gald3r release stage --apply`._

## [5.0.0-beta.5] - 2026-08-10

_No public release notes have been written yet for the next tag. Replace this line with
business-level notes before running `gald3r release stage --apply`._

## [5.0.0-beta.4] - 2026-08-09

_No public release notes have been written yet for the next tag. Replace this line with
business-level notes before running `gald3r release stage --apply`._

## [5.0.0-beta.3] - 2026-08-09

_No public release notes have been written yet for the next tag. Replace this line with
business-level notes before running `gald3r release stage --apply`._

## [5.0.0-beta.2] - 2026-08-09

_No public release notes have been written yet for the next tag. Replace this line with
business-level notes before running `gald3r release stage --apply`._

## [5.0.0-beta.1] - 2026-08-09

_No public release notes have been written yet for the next tag. Replace this line with
business-level notes before running `gald3r release stage --apply`._

## [4.0.0-beta.3] - 2026-08-05

_No public release notes have been written yet for the next tag. Replace this line with
business-level notes before running `gald3r release stage --apply`._

## [Unreleased] — v4.0.0

Targeting all three platforms — Windows, macOS, and Linux — in one release.

### Added
- **Signed binaries on every platform.** Windows builds are Authenticode-signed as *Gald3r Labs
  LLC* with a timestamp countersignature; macOS builds are signed with an Apple Developer ID and
  notarized. Every artifact ships with a SHA-256 sidecar so a download can be verified before it is
  run.
- **Windows installer.** An MSI alongside the standalone binary, for people who would rather not
  manage a PATH entry by hand.
- **winget support.** `winget install gald3r`, with upgrades handled the normal way.

### Changed
- **Faster startup.** Cold start dropped from roughly four seconds to about one, so single commands
  feel immediate rather than sluggish.

Full notes will be finalized when 4.0.0 ships.

---

## [4.0.0-beta.1] — 2026-08-01

First public build: a signed, self-contained Windows binary — no installer, no runtime, no Python
required.

### What gald3r is
A single-binary coding-agent platform with three parts that work together:
- **A project brain.** `gald3r setup` creates a `.gald3r/` store — tasks, bugs, plans, constraints,
  decisions — that every AI tool on your machine reads and writes. Cursor, Claude Code, Copilot and
  30+ others coordinate through the same state instead of each keeping private notes.
- **An agent runtime.** `gald3r chat` and `gald3r run` drive local models (Ollama, LM Studio, vLLM)
  or cloud providers, with a default-deny network gate around sandboxed runs.
- **CRASH components.** Commands, Rules, Agents, Skills, and Hooks that turn agent guidance into
  behavior the tooling actually enforces — not documentation an assistant may or may not follow.

### Added
- Authenticode-signed Windows binary with SHA-256 sidecar and timestamp countersignature.
- Autopilot (`gald3r autopilot loop`): rolling implement-then-review cycles in which reviewer
  agents are always independent and never verify their own work.
- Project state in SQLite with human-readable Markdown alongside it, so the data stays portable and
  reviewable in version control.

### Known limitations
- **Windows only in this build.** macOS and Linux binaries build in CI and ship together at 4.0.0.
- **Prerelease.** Some commands are thin, and a few depend on optional network services. Expect
  rapid iteration between now and 4.0.0.
- The bundled documentation's install section still leads with a from-source setup; prefer the
  binary install in the README.

### Verify your download
```powershell
(Get-AuthenticodeSignature .\gald3r.exe).Status          # -> Valid
(Get-FileHash .\gald3r.exe -Algorithm SHA256).Hash.ToLower()
Get-Content .\gald3r.exe.sha256                          # -> must match
```
