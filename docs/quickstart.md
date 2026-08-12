# Quickstart

## 1. Install

Download the platform bundle (or installer) for your OS from the **v5.0.0-beta.16** release, verify it
against `SHA256SUMS.txt`, and install it.

**Windows** -- easiest: run `gald3r-windows-x86_64.msi`. Manual install: extract
`gald3r-windows-x86_64.zip`, put it on your PATH.

```powershell
Expand-Archive gald3r-windows-x86_64.zip
Get-FileHash .\gald3r-windows-x86_64\gald3r.exe -Algorithm SHA256
Get-Content .\gald3r-windows-x86_64\SHA256SUMS      # must match
gald3r --version                          # -> gald3r 5.0.0-beta.16 ...
```

**macOS** -- easiest: run `gald3r-macos-arm64.pkg` (Apple Silicon) or `gald3r-macos-x86_64.pkg`
(Intel). Manual install: extract the matching `.tar.gz` and run its bundled `install.sh`.

**Linux**

```bash
tar xzf gald3r-linux-x86_64.tar.gz
cd gald3r-*-linux-x86_64
sha256sum -c SHA256SUMS                   # must match
./install.sh
gald3r --version
```

No Python, no `uv`, no source checkout required -- these are the compiled binaries.

## 2. First run -- confirm the install is healthy

```
gald3r doctor
```

`WARN` lines are informational (optional providers/telemetry), not failures.

## 3. Scaffold a project

From inside the project directory you want gald3r-enabled:

```
gald3r setup --dry-run   # see what would be created, writes nothing
gald3r setup             # actually create it
```

Nothing outside `.gald3r/` is touched.

## 4. A zero-configuration smoke test

Before wiring up any AI provider, confirm the agent-run plumbing works end to end with no
network call and no API key:

```
gald3r run "hello gald3r" --backend dev-echo
```

## 5. Track real work: tasks

```
gald3r task add "Write the quickstart doc" -d "Draft it" --type docs --priority medium
gald3r task list
gald3r task next
```

`gald3r task update <id> --status in-progress` claims a task. See
[`cli-reference.md`](./cli-reference.md) for the full task/bug verb surface.

## 6. Optional: a real agent run with a provider

```
gald3r init-providers --dry-run   # preview without writing
gald3r init-providers             # write providers.yaml
gald3r run "explain what this project does"
```

The generated starter defaults to a local Ollama endpoint -- no cloud account or API key
required if you already have Ollama running locally. Edit `providers.yaml` to point at
OpenAI/Anthropic/etc. instead.

## 7. Where next

- [Concepts](./concepts.md) -- CRASH, `.gald3r/`, and the DB-vs-markdown state model
- [CLI reference](./cli-reference.md) -- the full verb catalog
- `gald3r <verb> --help` -- always the authoritative source for any single verb's flags
