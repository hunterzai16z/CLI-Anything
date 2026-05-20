---
name: cli-anything
description: >
  Use this skill whenever the user asks about CLI-Anything, generating a CLI harness for a GUI
  application or software repository, making software agent-native, building a CLI with Click,
  using the cli-hub package manager, or integrating new software with Claude Code / Codex / Pi /
  OpenClaw / OpenCode / Cursor. Triggers include: "/cli-anything", "cli-hub install", "build a CLI
  for <software>", "make <app> agent-ready", "HARNESS.md", "agent harness", "cli-anything-gimp",
  "cli-anything-blender", or any mention of the HKUDS/CLI-Anything GitHub repo. Also trigger when
  the user wants to contribute a new CLI harness, run /cli-anything:refine, or understand the
  7-phase build pipeline.
---

# CLI-Anything Skill

CLI-Anything makes **any software agent-native** by generating a complete Python Click CLI with
REPL mode, JSON output, session state, and a full test suite — in one command. Agents (Claude
Code, Codex, Pi, OpenClaw, Cursor, OpenCode, and others) can then drive GIMP, Blender,
LibreOffice, Obsidian, FreeCAD, and 60+ other apps through the generated CLI.

---

## Install the plugin (Claude Code — recommended)

```bash
# Step 1: Add the marketplace
/plugin marketplace add HKUDS/CLI-Anything

# Step 2: Install the plugin
/plugin install cli-anything

# Step 3: Build a CLI for any software
/cli-anything ./gimp            # from a local path
/cli-anything https://github.com/blender/blender   # or a GitHub URL
```

If `/cli-anything` isn't recognized after install:
```bash
/reload-plugins
/help cli-anything          # verify it loaded
# Legacy form: /cli-anything:cli-anything ./gimp
```

---

## Install CLIs without building (CLI-Hub package manager)

Pre-built CLIs are published to PyPI. Browse and install instantly:

```bash
pip install cli-anything-hub    # install the hub
cli-hub list                    # browse all available CLIs
cli-hub search image            # search by keyword
cli-hub search "3d modeling"
cli-hub install gimp            # install a CLI
cli-hub info gimp               # show details
cli-hub update gimp             # update to latest
cli-hub uninstall gimp          # remove
```

Web hub: https://hkuds.github.io/CLI-Anything/

Install via `npx skills`:
```bash
npx skills add HKUDS/CLI-Anything --skill <skill-name> -g -y
```

---

## Install on other agents

| Agent       | Command / Method |
|-------------|-----------------|
| **Pi**      | `bash .pi-extension/cli-anything/install.sh` (from repo root) |
| **OpenClaw**| Uses `cli-anything` plugin; same `/cli-anything` commands |
| **OpenCode**| Copy `opencode-commands/` to your workspace; use `/cli-anything` |
| **Codex CLI**| `codex plugin marketplace add HKUDS/CLI-Anything && codex plugin install cli-anything` |
| **Cursor / Copilot CLI** | Manual install; copy `cli-anything-plugin/` to plugin dir |

---

## The 7-phase build pipeline

When you run `/cli-anything ./gimp`, the agent executes all 7 phases automatically:

| Phase | What happens |
|-------|-------------|
| 1 🔍 **Analyze** | Scans source code, maps GUI actions to APIs, identifies state model |
| 2 📐 **Design** | Architects command groups, output formats, REPL interaction |
| 3 🔨 **Implement** | Builds Click CLI with REPL, `--json` output, undo/redo |
| 4 📋 **Plan Tests** | Creates `TEST.md` with unit + E2E test plans |
| 5 🧪 **Write Tests** | Implements comprehensive pytest test suite |
| 6 📝 **Document** | Updates `TEST.md` with results, generates `SKILL.md` |
| 7 📦 **Publish** | Creates `setup.py`, installs entry point to PATH |

Output structure:
```
<software>/
└── agent-harness/
    ├── <SOFTWARE>.md          # full capability reference
    ├── setup.py               # pip-installable package
    └── cli_anything/
        └── <software>/
            ├── README.md
            ├── __init__.py
            ├── __main__.py
            ├── <software>_cli.py   # Click entrypoint
            ├── core/               # command implementations
            ├── utils/              # helpers
            └── tests/              # unit + E2E tests
```

---

## Refine an existing CLI

After the initial build, iteratively expand coverage:

```bash
# Broad gap analysis across all capabilities
/cli-anything:refine ./gimp

# Focused — target a specific area
/cli-anything:refine ./gimp "batch image processing and filters"
/cli-anything:refine ./blender "rigging and animation curves"
```

Each refine run is incremental and non-destructive. Run it multiple times.

---

## Validate / test a CLI

```bash
# Run the built-in test suite
/cli-anything:test ./gimp

# Validate a harness matches HARNESS.md spec
/cli-anything:validate ./gimp
```

---

## Using a generated CLI

All generated CLIs share the same interface pattern:

```bash
# REPL mode (default — no subcommand)
cli-anything-gimp

# One-shot commands
cli-anything-gimp file open --path /path/to/image.jpg
cli-anything-gimp image resize --width 800 --height 600
cli-anything-gimp file export --format PNG --path /output/image.png

# JSON output (for agents)
cli-anything-gimp --json file open --path /path/to/image.jpg

# Help
cli-anything-gimp --help
cli-anything-gimp image --help
```

---

## Available pre-built CLIs (60+ apps)

**Creative:** gimp, inkscape, krita, blender, freecad, cloudcompare, musescore, audacity, kdenlive, shotcut, obs-studio, videocaptioner, comfyui

**Productivity:** libreoffice, obsidian, zotero, drawio, mermaid, mubu, mailchimp, n8n, dify-workflow

**Development:** lldb, renderdoc, nsight-graphics, nslogger, unrealinsights, wiremock, chromadb, pm2, ollama, exa, novita

**Geospatial / Science:** qgis, unimol-tools

**Browser / Web:** browser, safari, iterm2

**Games:** slay-the-spire-ii, godot

**Finance / Admin:** firefly-iii, cloudanalyzer, adguardhome

**Misc:** zoom, seaclip, openscreen, sbox, intelwatch, anygen

---

## Contribute a new CLI harness

1. Read `cli-anything-plugin/HARNESS.md` — the authoritative build spec
2. Build your harness following the 7-phase pipeline
3. Add your CLI to `registry.json` with metadata
4. Open a PR — merged CLIs appear instantly on CLI-Hub

Contribution guide: `CONTRIBUTING.md`

---

## HARNESS.md — the build spec

`cli-anything-plugin/HARNESS.md` is the single source of truth for how harnesses are built. Key rules it enforces:

- **Stateful REPL:** every CLI starts in REPL mode by default; commands maintain session state
- **JSON output:** `--json` flag required on every command group; returns machine-parseable dicts
- **Undo/redo:** wrap destructive operations so agents can recover from mistakes
- **Phases are contiguous:** never skip; each phase produces artifacts the next phase depends on
- **Test coverage:** unit tests for every command, at least one E2E test per command group
- **`setup.py` + entry point:** the CLI must be pip-installable as `cli-anything-<name>`

---

## When NOT to use this skill

- The user wants to use an existing CLI tool (like `git`, `ffmpeg`) — just use it directly
- The user wants a simple Python script with no REPL or agent interface
- The user is asking about MCP tools or REST APIs — those are different patterns
