# gald3r

> **Release home for the gald3r binary.** Builds here are produced by `gald3r release stage` from
> the development repo and published as signed GitHub Releases. Everything in this repo is pipeline
> output — please open issues rather than PRs against generated files.
>
> **Pre-1.0 / early access.** The current builds are prereleases on the road to v4.0; expect rapid
> iteration. The framework/templates live in [`Gald3r-Labs/gald3r`](https://github.com/Gald3r-Labs/gald3r).

**gald3r** is a single-binary CLI coding-agent platform: a file-first task/bug tracker, a
multi-provider agent runtime (local models via Ollama/LM Studio/vLLM, or cloud providers), and
the **CRASH** component system (**C**ommands, **R**ules, **A**gents, **S**kills, **H**ooks) that
turns agent guidance into *enforced, executing behavior* in your project — not just markdown an
LLM might choose to follow.

It is comparable to tools like Claude Code, Cursor's agent, or OpenCode — but standalone: **one
binary, no required IDE** — and it also *coordinates* those tools: install gald3r into a project
and Cursor, Claude Code, Copilot, and 30+ other AI platforms share one `.gald3r/` brain (tasks,
bugs, plans, constraints) with per-platform CRASH overlays.

## Install (Windows)

1. Download **`gald3r.exe`** and **`gald3r.exe.sha256`** from the
   [latest release](../../releases/latest).
2. **Verify** (recommended — every release is Authenticode-signed by *Gald3r Labs LLC* via Azure
   Artifact Signing, timestamp countersigned):
   ```powershell
   # Signature
   (Get-AuthenticodeSignature .\gald3r.exe).Status        # -> Valid
   # Hash
   (Get-FileHash .\gald3r.exe -Algorithm SHA256).Hash.ToLower()
   Get-Content .\gald3r.exe.sha256                        # -> must match
   ```
3. Place it on your PATH, e.g.:
   ```powershell
   New-Item -ItemType Directory -Force "$env:USERPROFILE\.local\bin" | Out-Null
   Move-Item .\gald3r.exe "$env:USERPROFILE\.local\bin\gald3r.exe"
   # Add %USERPROFILE%\.local\bin to your user PATH if it isn't already
   ```
4. **Fully restart your IDE/terminal** after PATH changes — already-running processes inherit
   the old environment and will not see `gald3r` until relaunched.

macOS (arm64) and Linux binaries build in CI. macOS signing/notarization is in progress — those
platforms are not part of this staging release yet.

## First run

```powershell
gald3r --version          # gald3r 0.2.0 (build ...)
gald3r setup              # scaffold .gald3r/ + CRASH overlays into your project
gald3r status             # project status: tasks, bugs, pipeline state
gald3r task ready         # what's runnable right now
gald3r chat               # talk to the agent runtime
```

## Documentation

| Doc | Covers |
|---|---|
| [docs/quickstart.md](docs/quickstart.md) | Install → first run → first useful command |
| [docs/concepts.md](docs/concepts.md) | CRASH, `.gald3r/`, tasks/bugs, sessions — the ideas everything else builds on |
| [docs/cli-reference.md](docs/cli-reference.md) | Every top-level `gald3r` verb, grouped by intent |
| [CHANGELOG.md](CHANGELOG.md) | Everything in the current staged build |

> Note: the docs are promoted from the development tree; the quickstart's install section
> predates packaged releases and leads with from-source setup — on this staging repo, prefer
> the binary install above.

## License

[Fair Source License 1.1, Apache 2.0 future grant (FSL-1.1-Apache)](LICENSE) —
Copyright (c) 2025–2026 Gald3r Labs LLC. Free for internal use, education, research, and
professional services; competing commercial use is restricted until each release's Apache-2.0
conversion date.
