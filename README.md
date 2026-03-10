# Clawdboss Upgrade

Non-destructive upgrade tool for existing [OpenClaw](https://openclaw.dev) installations. Companion to [clawdboss](https://github.com/NanoFlow-io/clawdboss).

## What It Does

If you already have a working OpenClaw setup (from clawdboss or manual install), this script merges improvements **without destroying your customizations**.

| Area | What Gets Upgraded |
|---|---|
| **Workspace files** | Injects missing sections into AGENTS.md, SOUL.md, etc. — never overwrites existing content |
| **openclaw.json** | Adds missing config keys, fixes defaults (maxConcurrent, blockStreamingCoalesce) — preserves your values |
| **Security** | Prompt injection defense, anti-loop rules, WAL Protocol, External Content Security |
| **Skills** | Offers to install missing clawdboss skills (github, humanizer, self-improving, find-skills, marketing-skills) |
| **Extensions** | Installs/updates memory-hybrid (SQLite + LanceDB two-tier memory) |
| **Specialist agents** | Patches workspace files for comms/research/security agents if they exist |
| **.env** | Adds missing env vars without overwriting existing ones |

## Key Principles

- **Never overwrites user content** — merge, don't replace
- **Always backs up first** — timestamped backup of your entire `~/.openclaw` before any changes
- **Dry-run mode** — preview exactly what would change
- **Idempotent** — safe to run as many times as you want
- **No onboarding questions** — this isn't a fresh setup, no "what's your name" prompts

## Quick Start

```bash
# Clone alongside clawdboss
git clone https://github.com/NanoFlow-io/clawdboss-upgrade.git
cd clawdboss-upgrade

# Preview changes (recommended first)
./upgrade.sh --dry-run

# Run the upgrade
./upgrade.sh
```

## Usage

```
Usage: upgrade.sh [OPTIONS]

Options:
  --dry-run       Show what would change without modifying anything
  --verbose       Show detailed diffs and operations
  --force         Skip confirmation prompts
  --clawdboss DIR Path to clawdboss repo (default: auto-detect)
  --openclaw DIR  Path to OpenClaw state dir (default: ~/.openclaw)
  --help          Show this help
```

### Examples

```bash
# Preview all changes with diffs
./upgrade.sh --dry-run --verbose

# Non-interactive upgrade (accept all defaults)
./upgrade.sh --force

# Custom paths
./upgrade.sh --clawdboss /opt/clawdboss --openclaw /home/user/.openclaw
```

## Requirements

- Existing OpenClaw installation with `~/.openclaw/openclaw.json`
- Python 3 (for JSON manipulation and markdown parsing)
- Node.js + npm (for skills and extensions)
- `git` (to clone clawdboss templates if not found locally)

The script expects the [clawdboss](https://github.com/NanoFlow-io/clawdboss) repo to be cloned alongside it (e.g., `~/clawdboss` and `~/clawdboss-upgrade`). If not found, it will attempt to clone it automatically.

## How Section Merging Works

The upgrade script uses a **section-aware merge strategy** for markdown files:

1. **Scan** the template for each section (by heading)
2. **Check** if that section (or its key content) already exists in your file
3. **Skip** sections that are already present (even if worded differently)
4. **Inject** only new sections at the end of your file

This means:
- Your custom sections are **never touched**
- Your modified versions of template sections are **preserved**
- New sections from updated templates are **added automatically**
- The script checks for content patterns, not just headers — so renamed sections still match

## What's in the Backup

Before any changes, a timestamped backup is created at `~/.openclaw/backups/upgrade-YYYYMMDD-HHMMSS/`:

```
backups/upgrade-20260310-143022/
├── openclaw.json
├── .env
├── workspace/
│   ├── AGENTS.md
│   ├── SOUL.md
│   ├── USER.md
│   ├── IDENTITY.md
│   ├── TOOLS.md
│   └── HEARTBEAT.md
├── workspace-comms/
│   └── ...
└── workspace-research/
    └── ...
```

To rollback: `cp -r ~/.openclaw/backups/upgrade-YYYYMMDD-HHMMSS/* ~/.openclaw/`

## Relationship to Clawdboss

| | clawdboss | clawdboss-upgrade |
|---|---|---|
| **Purpose** | Fresh install from scratch | Upgrade existing install |
| **Asks questions** | Yes (name, keys, personality) | No (detects everything) |
| **Creates files** | Yes (from templates) | Only if missing |
| **Overwrites** | Yes (fresh setup) | Never |
| **Backup** | Only openclaw.json | Everything |
| **Idempotent** | Not designed for re-runs | Safe to run repeatedly |

## License

MIT

## Credits

Built by the team at [NanoFlow.io](https://nanoflow.io) • Part of the Clawdboss ecosystem.
