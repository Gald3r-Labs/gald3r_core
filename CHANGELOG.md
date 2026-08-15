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
