# Quickstart

## 1. Install

Download `gald3r` (`gald3r.exe` on Windows) plus its `.sha256` sidecar from the **v5.0.0-beta.1**
release, verify it, and put it on your PATH.

**Windows**

```powershell
(Get-FileHash .\gald3r.exe -Algorithm SHA256).Hash.ToLower()
Get-Content .\gald3r.exe.sha256          # must match
# move gald3r.exe onto a PATH directory, then open a NEW terminal
gald3r --version                          # -> gald3r 5.0.0-beta.1 ...
```

**macOS / Linux**

```bash
sha256sum -c gald3r.sha256
chmod +x gald3r && mv gald3r ~/.local/bin/gald3r
gald3r --version
```

No Python, no `uv`, no source checkout required -- this is the compiled binary.

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
