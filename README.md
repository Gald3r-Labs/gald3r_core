# gald3r

**gald3r** is a single-binary CLI coding-agent platform -- a task/bug tracker, a
multi-provider agent runtime (local models via Ollama/LM Studio/vLLM, or cloud providers),
and the CRASH (Commands, Rules, Agents, Skills, Hooks) component system that turns agent
guidance into behavior that actually executes. One signed executable, no IDE required.

Version **5.0.0-beta.23** (v5.0.0-beta.23).

## Install

Download the platform bundle (or installer) for your OS from this release, verify it against
`SHA256SUMS.txt`, then install it.

**Windows**

Easiest: download `gald3r-windows-x86_64.msi` and run it -- installs every binary and wires up
your PATH.

Prefer a manual install? Download `gald3r-windows-x86_64.zip` instead:

```powershell
Expand-Archive gald3r-windows-x86_64.zip
Get-FileHash .\gald3r-windows-x86_64\gald3r.exe -Algorithm SHA256
Get-Content .\gald3r-windows-x86_64\SHA256SUMS      # must match
# move the extracted folder onto a PATH directory, then open a NEW terminal
# (running processes keep the old PATH)
gald3r --version
```

**macOS**

Easiest: download `gald3r-macos-arm64.pkg` (Apple Silicon) or `gald3r-macos-x86_64.pkg` (Intel)
and run it -- signed and notarized, installs to `/usr/local/bin`.

Prefer the tarball? Download `gald3r-macos-arm64.tar.gz` / `gald3r-macos-x86_64.tar.gz`:

```bash
tar xzf gald3r-macos-arm64.tar.gz    # or the -x86_64 tarball on Intel
cd gald3r-*-macos-*
shasum -a 256 -c SHA256SUMS          # must match
./install.sh                          # ~/.local/bin, or /usr/local/bin with sudo
gald3r --version
```

**Linux**

```bash
tar xzf gald3r-linux-x86_64.tar.gz
cd gald3r-*-linux-x86_64
sha256sum -c SHA256SUMS              # must match
./install.sh                          # ~/.local/bin, or /usr/local/bin with sudo
gald3r --version
```

`install.sh`/`uninstall.sh` are bundled inside the macOS/Linux tarballs and install (or remove)
every binary shipped in that archive. No Python, no `uv`, no source checkout required -- these
are the compiled binaries.

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

© Gald3r Labs LLC. Release `v5.0.0-beta.23`.
