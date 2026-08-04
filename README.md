# gald3r

**gald3r** is a single-binary CLI coding-agent platform -- a task/bug tracker, a
multi-provider agent runtime (local models via Ollama/LM Studio/vLLM, or cloud providers),
and the CRASH (Commands, Rules, Agents, Skills, Hooks) component system that turns agent
guidance into behavior that actually executes. One signed executable, no IDE required.

Version **4.0.0-beta.3** (v4.0.0-beta.3).

## Install

Download the signed binary from this release, verify it, put it on your PATH.

**Windows**

```powershell
(Get-FileHash .\gald3r.exe -Algorithm SHA256).Hash.ToLower()
Get-Content .\gald3r.exe.sha256          # must match
# move gald3r.exe onto a PATH directory, then open a NEW terminal (running
# processes keep the old PATH)
gald3r --version
```

**macOS / Linux**

```bash
sha256sum -c gald3r.sha256
chmod +x gald3r
mv gald3r ~/.local/bin/gald3r            # or anywhere on PATH
gald3r --version
```

No Python, no `uv`, no source checkout required -- this is the compiled binary.

## First run

```powershell
cd your-project
gald3r setup       # creates .gald3r/ -- tasks, bugs, plans, agent coordination
gald3r doctor      # confirm the install is healthy
gald3r status      # see what's tracked
```

## Documentation

| | |
|---|---|
| [Quickstart](docs/quickstart.md) | Install, first run, first useful command |
| [Concepts](docs/concepts.md) | CRASH, `.gald3r/`, tasks and bugs, sessions |
| [CLI reference](docs/cli-reference.md) | Every verb, grouped by what you're trying to do |
| [Changelog](CHANGELOG.md) | What shipped, and when |

## License

[Fair Source License 1.1 (FSL-1.1-Apache)](LICENSE) -- (c) 2025-2026 Gald3r Labs LLC. Free
for internal use, education, research, and professional services; each release converts to
the Apache License 2.0 on its second anniversary.

---

Built from [Gald3r-Labs/gald3r_core_dev](https://github.com/Gald3r-Labs/gald3r_core_dev) at
`v4.0.0-beta.3`.
