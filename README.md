# 🦞 Clawdboss Upgrade

**Non-destructive upgrade tool for existing [OpenClaw](https://openclaw.dev) installations.**

Already have a working OpenClaw setup? This script merges Clawdboss improvements into your existing install — without destroying your customizations. Companion to [Clawdboss](https://github.com/NanoFlow-io/clawdboss).

## What Gets Upgraded

| Area | What Changes |
|---|---|
| **Workspace files** | Injects missing sections into AGENTS.md, SOUL.md, etc. — never overwrites existing content |
| **openclaw.json** | Adds missing config keys, fixes defaults (maxConcurrent, blockStreamingCoalesce) — preserves your values |
| **Security** | Prompt injection defense, anti-loop rules, WAL Protocol, External Content Security |
| **Secret migration** | Detects plaintext API keys in openclaw.json and offers to move them to `.env` with `${VAR}` references |
| **Skills** | Offers to install missing Clawdboss skills (GitHub, Humanizer, Self-Improving, Find Skills, Marketing Skills) |
| **Extensions** | Installs/updates memory-hybrid (SQLite + LanceDB two-tier memory) |
| **Specialist agents** | Patches workspace files for comms/research/security agents if they exist |
| **Telegram** | Offers to add Telegram as a messaging channel (non-destructive — won't add if not wanted) |
| **.env** | Adds missing env vars without overwriting existing ones |

## Key Principles

- 🔒 **Never overwrites user content** — merge, don't replace
- 💾 **Always backs up first** — timestamped backup of your entire `~/.openclaw` before any changes
- 👀 **Dry-run mode** — preview exactly what would change
- 🔄 **Idempotent** — safe to run as many times as you want
- 🚫 **No onboarding questions** — this isn't a fresh setup, no "what's your name" prompts

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

The script expects the [Clawdboss](https://github.com/NanoFlow-io/clawdboss) repo to be cloned alongside it (e.g., `~/clawdboss` and `~/clawdboss-upgrade`). If not found, it will attempt to clone it automatically.

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

## Ecosystem Tools & Skills

Clawdboss Upgrade can install these optional tools and skills if they're missing from your setup:

| Tool | Purpose | Link |
|------|---------|------|
| **OCTAVE** | Token compression for multi-agent handoffs (3-20x reduction) | [GitHub](https://github.com/elevanaltd/octave-mcp) · [PyPI](https://pypi.org/project/octave-mcp/) |
| **Graphthulhu** | Knowledge graph memory with Obsidian/Logseq backends | [GitHub](https://github.com/skridlevsky/graphthulhu) |
| **ApiTap** | Intercepts web traffic to teach agents how APIs work | [GitHub](https://github.com/n1byn1kt/apitap) · [npm](https://www.npmjs.com/package/@apitap/core) |
| **Scrapling** | Anti-bot web scraping with adaptive selectors | [GitHub](https://github.com/D4Vinci/Scrapling) · [PyPI](https://pypi.org/project/scrapling/) |
| **GitHub** | Issues, PRs, CI/CD via the `gh` CLI | [CLI](https://cli.github.com) · [ClawHub](https://clawhub.ai) |
| **Playwright MCP** | Full browser automation (navigate, click, fill, screenshot) | [ClawHub](https://clawhub.ai/Spiceman161/playwright-mcp) |
| **Humanizer** | Detects and removes AI writing patterns (24 patterns, 500+ terms) | [ClawHub](https://clawhub.ai/biostartechnology/humanizer) |
| **Self-Improving Agent** | Captures errors and corrections for continuous learning | [ClawHub](https://clawhub.ai/pskoett/self-improving-agent) |
| **Find Skills** | Discover and install new capabilities from ClawHub on-the-fly | [ClawHub](https://clawhub.ai) |
| **Marketing Skills** | 15+ reference skills for copywriting, CRO, SEO, email, paid ads | [ClawHub](https://clawhub.ai/jchopard69/marketing-skills) |
| **Healthcheck** | Host security audits: firewall, SSH, system updates, exposure | Built into OpenClaw |
| **Clawmetry** | Real-time observability dashboard (token costs, sessions, flow) | [GitHub](https://github.com/vivekchand/clawmetry) · [Website](https://clawmetry.com) |
| **ClawSec** | File integrity monitoring, security advisory feed, malicious skill detection | [GitHub](https://github.com/prompt-security/clawsec) · [Website](https://prompt.security/clawsec) |

## Clawdboss vs Clawdboss Upgrade

| | Clawdboss | Clawdboss Upgrade |
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

Built by [NanoFlow.io](https://nanoflow.io) • Part of the [Clawdboss](https://github.com/NanoFlow-io/clawdboss) ecosystem
