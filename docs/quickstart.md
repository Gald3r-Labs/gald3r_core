# Quickstart

## 1. Install

Choose the recommended installer for your processor from the [v5.0.57 release](https://github.com/Gald3r-Labs/gald3r_core/releases/tag/v5.0.57), and verify your download against its `SHA256SUMS.txt`.

| Platform | Recommended installer |
|---|---|
| Windows x86_64 | `gald3r-windows-x86_64.msi` — choose Core, Throne and/or IDE |
| Linux x86_64 | `gald3r-suite-linux-x86_64.tar.gz` — extract, read the included README and run `sh install.sh` from the extracted suite directory |
| Mac Apple silicon | `gald3r-suite-macos-arm64.pkg` — signed and notarized; choose Core, Throne and/or IDE |
| Mac Intel | `gald3r-macos-x86_64.pkg` — Core and companions only |

Linux desktop apps require WebKit2GTK 4.1; the shell installer does not install system dependencies. Linux ARM64 and Mac Intel desktop apps are not supplied in this release.
Standalone binary archives are advanced alternatives, not a self-contained USB workspace.
No Python, `uv`, or source checkout is required to install the compiled programs.

If you selected Core, open a new terminal and run `gald3r --version` to verify 5.0.57.

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
[`verbs.md`](./verbs.md) for the full task/bug verb surface.

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
- [CLI reference](./verbs.md) -- the full verb catalog
- `gald3r <verb> --help` -- always the authoritative source for any single verb's flags
