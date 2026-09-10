# gald3r

**gald3r** is a single-binary CLI coding-agent platform -- a task/bug tracker, a
multi-provider agent runtime (local models via Ollama/LM Studio/vLLM, or cloud providers),
and the CRASH (Commands, Rules, Agents, Skills, Hooks) component system that turns agent
guidance into behavior that actually executes. One signed executable, no IDE required.

Version **5.0.56** (v5.0.56).

<!-- BEGIN: gald3r-downloads -->
## Downloads

**gald3r 5.0.56 — choose one recommended download for your computer.**
Core includes the command-line engine and companion programs, including Longship.
Throne is the desktop control center; IDE is the editor. Suite installers let you choose applications.

| Platform / processor | Recommended download | Applications and installation |
|---|---|---|
| Windows — Intel/AMD 64-bit (x86_64) | [Download suite installer (.msi)](https://github.com/Gald3r-Labs/gald3r_core/releases/download/v5.0.56/gald3r-windows-x86_64.msi) | Signed. Choose Core, Throne and/or IDE, then choose the destination. |
| Linux — Intel/AMD 64-bit (x86_64) | [Download suite installer bundle (.tar.gz)](https://github.com/Gald3r-Labs/gald3r_core/releases/download/v5.0.56/gald3r-suite-linux-x86_64.tar.gz) | Includes Core, Throne and IDE. Extract it, read its README, then run `sh install.sh` from the extracted suite directory. |
| macOS — Apple silicon (ARM64, M-series) | [Download suite installer (.pkg)](https://github.com/Gald3r-Labs/gald3r_core/releases/download/v5.0.56/gald3r-suite-macos-arm64.pkg) | Signed and notarized, including the desktop apps. Choose Core, Throne and/or IDE. |
| macOS — Intel (x86_64) | [Download Core installer (.pkg)](https://github.com/Gald3r-Labs/gald3r_core/releases/download/v5.0.56/gald3r-macos-x86_64.pkg) | Signed and notarized. Core and companions only; Throne and IDE are not supplied for Intel Macs. |

[Download SHA-256 checksums](https://github.com/Gald3r-Labs/gald3r_core/releases/download/v5.0.56/SHA256SUMS.txt) to verify your download.

### Before installing

- **Linux:** the suite uses a shell-script installer, not a `.deb` or `.rpm` package. It offers application and destination options. Desktop apps require WebKit2GTK 4.1; the installer does not install system dependencies or run `sudo`. Linux ARM64 is not supplied in this release.
- **Mac:** the Apple-silicon suite installs desktop apps in `/Applications` and commands in `/usr/local/bin`. These locations are fixed for this release. Deselecting an application does not uninstall an existing copy.
- **Updates:** use the suite download to update desktop applications. `gald3r install update` updates Core and companions, not the entire suite.
- **Not a USB workspace:** these downloads do not provide the planned Portable Gald3r environment carrying settings, projects, AI models and dependencies on a removable drive.
- All shipped applications use the same release version. GitHub's automatically added “Source code” ZIP/tar.gz files are not installers.

<details>
<summary>Advanced: Core-only installers and standalone binary archives</summary>

These alternatives are for manual or Core-only installations. Most users should choose a recommended installer above.
A **binary archive** is a compressed application bundle, not a self-contained portable workspace.

| Platform | Core and companions only | Separate desktop applications |
|---|---|---|
| Windows x86_64 | [Core binary archive (.zip)](https://github.com/Gald3r-Labs/gald3r_core/releases/download/v5.0.56/gald3r-windows-x86_64.zip) — no Throne or IDE | Use the suite installer and select the application(s) you want. |
| Linux x86_64 | [Core archive with install.sh (.tar.gz)](https://github.com/Gald3r-Labs/gald3r_core/releases/download/v5.0.56/gald3r-linux-x86_64.tar.gz) | [Throne binary archive](https://github.com/Gald3r-Labs/gald3r_core/releases/download/v5.0.56/gald3r_throne-linux-amd64.tar.gz) · [IDE binary archive](https://github.com/Gald3r-Labs/gald3r_core/releases/download/v5.0.56/gald3r_ide-linux-amd64.tar.gz) |
| macOS Apple silicon | [Core-only installer (.pkg)](https://github.com/Gald3r-Labs/gald3r_core/releases/download/v5.0.56/gald3r-macos-arm64.pkg) · [Core binary archive (.tar.gz)](https://github.com/Gald3r-Labs/gald3r_core/releases/download/v5.0.56/gald3r-macos-arm64.tar.gz) | [Throne unsigned archive](https://github.com/Gald3r-Labs/gald3r_core/releases/download/v5.0.56/gald3r_throne-macos-arm64-unsigned.tar.gz) · [IDE unsigned archive](https://github.com/Gald3r-Labs/gald3r_core/releases/download/v5.0.56/gald3r_ide-macos-arm64-unsigned.tar.gz) |
| macOS Intel | [Core-only installer (.pkg)](https://github.com/Gald3r-Labs/gald3r_core/releases/download/v5.0.56/gald3r-macos-x86_64.pkg) · [Core binary archive (.tar.gz)](https://github.com/Gald3r-Labs/gald3r_core/releases/download/v5.0.56/gald3r-macos-x86_64.tar.gz) | Not supplied for Intel Macs. |

The two Mac Core archives contain the command-line programs and installation/uninstallation scripts, compiled separately for Apple silicon and Intel. They do not contain Throne or IDE.
**Only the separate Mac desktop archives are unsigned.** The recommended Apple-silicon suite contains signed desktop apps and is notarized; use that installer instead of these unsigned alternatives.

</details>
<!-- END: gald3r-downloads -->

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
| [CLI reference](docs/verbs.md) | Every verb, grouped by what you're trying to do |
| [Changelog](CHANGELOG.md) | What shipped, and when |

## License

[Fair Source License 1.1 (FSL-1.1-Apache)](LICENSE) -- (c) 2025-2026 Gald3r Labs LLC. Free
for internal use, education, research, and professional services; each release converts to
the Apache License 2.0 on its second anniversary.

---

© Gald3r Labs LLC. Release `v5.0.56`.
