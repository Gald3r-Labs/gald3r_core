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
