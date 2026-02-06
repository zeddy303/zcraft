# CLAUDE.md

## Project Overview

This is **zcraft** — a documentation repository for a personal AI infrastructure that connects handwritten note-taking, automation, pattern-based AI workflows, and knowledge management.

This repo contains implementation guides, not application code. There is no build system, test suite, or runtime.

## Repository Structure

```
.
├── README.md                    # Brief overview and inspiration links
├── PAI-workflow-v2.md           # Current implementation guide (v2, complete)
├── ai-infrastructure-guide.md   # Original guide (v1, WIP/outdated)
└── CLAUDE.md                    # This file
```

## Key Files

- **PAI-workflow-v2.md** — The primary, up-to-date guide. Covers Fabric configuration, Obsidian vault structure, n8n automation (ReMarkable to Obsidian pipeline), daily workflows, custom Fabric patterns, shell aliases, and maintenance.
- **ai-infrastructure-guide.md** — Earlier draft (marked WIP). Contains some additional patterns (`remarkable_to_journal`) and more detailed n8n workflow JSON not in v2. May have outdated paths/commands.
- **README.md** — Project summary with links to upstream inspirations (Fabric, Telos, Personal_AI_Infrastructure by Daniel Miessler).

## Infrastructure Stack Documented

| Component | Purpose |
|-----------|---------|
| Fabric | Pattern-based CLI AI tool for structured prompts |
| Ollama + Gemma3 | Local LLM (default model for Fabric) |
| Claude Code | Complex reasoning tasks |
| Obsidian | Knowledge base and journal (synced to phone via Syncthing) |
| n8n | Workflow automation (runs on Raspberry Pi via Docker) |
| ReMarkable | Handwritten note capture, shared via email |
| Syncthing | File sync between Pi, laptop, and phone |
| Tailscale | VPN connecting devices |

## Data Flow

```
ReMarkable (handwritten notes)
  → Email share to Gmail
  → n8n on Raspberry Pi (Gmail trigger → Jina AI fetch → format)
  → Syncthing to laptop
  → Obsidian vault (Journal/Staging/)
  → Manual review + Fabric patterns
  → Journal/Daily/
```

## Key Paths Referenced in Guides

- Obsidian vault: `~/Documents/ObsidianVault/`
- Fabric contexts: `~/.config/fabric/contexts/`
- Fabric patterns: `~/.config/fabric/patterns/`
- Journal staging: `~/Documents/ObsidianVault/Journal/Staging/`

## Custom Fabric Patterns

- `telos_journal` — Process daily reflections, connect to goals
- `interview_prep` — STAR format interview responses
- `analyze_job` — Job posting fit analysis
- `weekly_synthesis` — Weekly journal synthesis
- `remarkable_to_journal` — Clean up ReMarkable OCR text (v1 only)

## Writing Conventions

- All documentation is Markdown
- Code blocks use language-specific fencing (```bash, ```yaml, ```javascript, etc.)
- Guides are organized into numbered Parts with subsections (e.g., Part 1: Fabric Configuration, 1.1 Verify Installation)
- Inline Fabric patterns are shown with the full `system.md` content for copy-paste setup
- Shell aliases and commands use `$OBSIDIAN_VAULT` environment variable for paths
