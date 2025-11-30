# Personal AI Infrastructure Implementation Guide v2
## Zane's Setup - Bazzite Linux + Obsidian + n8n + Fabric

---

## Your Stack

| Component | Location | Status |
|-----------|----------|--------|
| Fabric | Laptop (Bazzite) | ✅ Installed, using Ollama/gemma3:latest |
| Ollama + Gemma3 | Laptop | ✅ Running |
| Claude Code | Laptop | ✅ Installed |
| Obsidian | Laptop + Phone | ✅ Synced via Syncthing |
| n8n | Raspberry Pi (Docker) | ✅ Running, Gmail OAuth configured |
| Syncthing | Laptop + Pi + Phone | ✅ Configured |
| Tailscale | Laptop + Pi | ✅ Configured |
| ReMarkable | Device | ✅ Sending share links via email |

---

## Part 1: Fabric Configuration

### 1.1 Verify Fabric Installation

Fabric should already be installed. Verify:

```bash
fabric --listmodels
```

Expected output:
```
Available models:
    [1] Ollama|gemma3:latest
```

### 1.2 Fabric Contexts

Contexts are single `.md` files in `~/.config/fabric/contexts/`. They provide persistent context for your prompts.

**Create your Telos context:**

```bash
mkdir -p ~/.config/fabric/contexts
vim ~/.config/fabric/contexts/telos.md
```

Paste your condensed Telos content:

```markdown
# TELOS CONTEXT: Zane

## Identity
- 53, Denver (15 years), engaged, DINK
- BA Film/Video, Executive MBA (2024)
- 8 years wireless infrastructure at DISH/Boost
- Meditation practitioner since 1995
- r/denver moderator

## Current Mission
Career transition: Telecommunications technology SME

## Active Goals (Q1 2026)
- Secure role by end of Q1 (primary, backup plan active)
- Python fundamentals by Jan 31
- AI certification by Feb 15

## Communication Style
- Direct, solution-focused
- Systematic over random
- Contemplative but action-oriented
```

### 1.3 Using Contexts

```bash
# Use context with any pattern (note: use filename with .md extension)
echo "your input" | fabric -C telos.md -p summarize

# List available contexts
ls ~/.config/fabric/contexts/

# Example: Summarize with your context
echo "Meeting notes..." | fabric -C telos.md -p summarize -m "gemma3:latest"
```

### 1.4 Custom Patterns

Patterns live in `~/.config/fabric/patterns/`. Each pattern is a **folder** containing a `system.md` file.

**Create a custom pattern:**

```bash
mkdir -p ~/.config/fabric/patterns/telos_journal
nano ~/.config/fabric/patterns/telos_journal/system.md
```

```markdown
# IDENTITY AND PURPOSE

You process daily reflections for someone in career transition, connecting experiences to their stated goals and growth areas.

# OUTPUT SECTIONS

## TODAY'S WINS
What was accomplished toward stated goals? Be specific.

## PATTERNS NOTICED  
Behavioral patterns: preparation quality, confidence vs doubt, action vs paralysis.

## GOAL ALIGNMENT
How did activities connect to short/medium/long-term goals?

## TOMORROW'S FOCUS
One specific, actionable item.

## TELOS UPDATE
Any insight that should be added to the Telos file (or "None").

# OUTPUT INSTRUCTIONS

- Maximum 300 words total
- Be direct and specific, not generic
- Call out both wins and growth areas
- Use contemplative but action-oriented tone
- Output in clean markdown
```

**Use your custom pattern:**

```bash
echo "Today I worked on my AI infrastructure..." | fabric -C telos.md -p telos_journal
```

---

## Part 2: Obsidian Structure

### 2.1 Vault Location

Your Obsidian vault is at:
```
~/Documents/ObsidianVault/
```

### 2.2 Folder Structure

```
~/Documents/ObsidianVault/
├── Journal/
│   ├── Daily/           # Final processed journal entries
│   └── Staging/         # n8n drops ReMarkable imports here (synced via Syncthing)
├── Telos/
│   ├── telos.md         # Your main Telos file
│   └── weekly/          # Weekly synthesis outputs
├── Learning/
│   └── python/
├── Career/
│   ├── interview-prep/
│   └── job-analysis/
└── Templates/
```

### 2.3 Syncthing Configuration

Syncthing syncs the `Journal/Staging/` folder between your Pi and laptop.

**Laptop (gesar):**
- Folder Path: `~/Documents/ObsidianVault/Journal/Staging`
- Shared with: server

**Pi (dorje):**
- Folder Path: `/home/zane/Documents/ObsidianVault/Journal/Staging`
- Shared with: laptop

**Important:** When setting up Syncthing shares, create the folder on the **source device first** (where files originate), then share to the receiving device.

---

## Part 3: n8n Automation (ReMarkable → Obsidian)

### 3.1 Overview

This workflow automatically captures notes from your ReMarkable tablet and saves them to Obsidian for review.

**Flow:**
```
ReMarkable "Share" → Email with link → Gmail Trigger → 
Extract URL → Fetch via Jina AI → Parse content → 
Write to Pi → Syncthing → Laptop Obsidian
```

### 3.2 n8n Docker Configuration

Located on your Raspberry Pi. Docker compose includes:

```yaml
n8n:
  image: docker.n8n.io/n8nio/n8n
  container_name: n8n
  environment:
    - N8N_SECURE_COOKIE=true
    - N8N_PROTOCOL=https
    - N8N_SSL_KEY=/certs/rpi-tailscale.key
    - N8N_SSL_CERT=/certs/rpi-tailscale.crt
    - N8N_PORT=5678
    - N8N_HOST=0.0.0.0
    - N8N_WEBHOOK_BASE_URL=tailscale.server.address:5678/
    - WEBHOOK_URL=https://tailscale.server.address:5678/
  ports:
    - "5678:5678"
  volumes:
    - n8n_data:/home/node/.n8n
    - /home/zane/rpi-tailscale.key:/certs/rpi-tailscale.key:ro
    - /home/zane/rpi-tailscale.crt:/certs/rpi-tailscale.crt:ro
    - /home/zane/Documents/ObsidianVault:/home/zane/Documents/ObsidianVault
  restart: unless-stopped
```

**Key points:**
- Webhook URLs use Tailscale hostname for OAuth callbacks
- Obsidian vault mounted for file writing
- SSL certs for HTTPS

### 3.3 Gmail OAuth Setup

1. **Google Cloud Console:**
   - Create project, enable Gmail API
   - Create OAuth 2.0 credentials (Web application)
   - Add redirect URI: `https://tailscale.server.address:5678/rest/oauth2-credential/callback`

2. **n8n Credentials:**
   - Add Gmail OAuth2 credential with Client ID and Secret
   - Access n8n via Tailscale URL to complete OAuth flow

### 3.4 Workflow Nodes

**Node 1: Gmail Trigger**
- Credential: Your Gmail OAuth2
- Poll Times: Every Hour (or as desired)
- Event: Message Received
- Filters - Sender: `my@remarkable.com`
- Filters - subject:"Your file is ready"

**Node 2: Gmail - Get Message**
- Operation: Get Message  
- Message ID: `{{ $json.id }}`
- Simplify: On

**Node 3: Code - Extract URL**
```javascript
const item = $input.first().json;
let emailText = item.text || item.html || item.snippet || '';

const urlMatch = emailText.match(/https:\/\/share\.remarkable\.com\/l\/([^\s\]"<\?]+)/);

if (!urlMatch) {
  return [{
    json: {
      error: 'No ReMarkable share URL found'
    }
  }];
}

const shareId = urlMatch[1];
const today = new Date().toISOString().split('T')[0];

return [{
  json: {
    shareUrl: urlMatch[0],
    shareId: shareId,
    filename: `${today}-remarkable-draft.md`
  }
}];
```

**Node 4: HTTP Request - Fetch via Jina AI**
- Method: GET
- URL: `https://r.jina.ai/{{ $json.shareUrl }}`
- Response Format: Text
- Put Output in Field: `data`

**Note on Jina AI privacy:** Your content passes through Jina's servers for JavaScript rendering. For personal journal notes this is a reasonable trade-off since content is already on ReMarkable's servers. For more privacy, self-host Playwright/Puppeteer.

**Node 5: Code - Format Content**
```javascript
const rawText = $input.first().json.data;

// Extract the main content
let content = rawText;

// Remove header junk
content = content.replace(/^Title:.*?===============\n\n/s, '');
content = content.replace(/\[Skip to main content\].*?\n\n/g, '');
content = content.replace(/Beta\n\nCopy link Log in\n\n\* \* \*\n\n/g, '');

// Remove footer junk
content = content.replace(/\* \* \*\n\n!\[Image.*$/s, '');
content = content.replace(/Tell us what you think.*$/s, '');

// Clean up
content = content.trim();

// Get filename from previous node
const filename = $('Code in JavaScript1').first().json.filename;
const today = new Date().toISOString().split('T')[0];

// Format as Obsidian markdown
const obsidianContent = `---
date: ${today}
source: remarkable
status: draft
---

# ReMarkable Import

${content}

---
*Imported via n8n. Edit and move to Journal/Daily when ready.*
`;

return [{
  json: {
    content: obsidianContent,
    filename: filename
  }
}];
```

**Node 6: Convert to File**
- Operation: Convert to Text File
- Text Input Field: `content`
- Put Output File in Field: `data`
- Options → File Name: `{{ $json.filename }}`

**Node 7: Read/Write Files from Disk**
- Operation: Write File to Disk
- File Path and Name: `/home/zane/Documents/ObsidianVault/Journal/Staging/{{ $('Code in JavaScript1').item.json.filename }}`
- Input Binary Field: `data`

### 3.5 Workflow Summary

```
Gmail Trigger
    ↓
Gmail (Get Message)
    ↓
Code (Extract URL) 
    ↓
HTTP Request (Jina AI)
    ↓
Code (Format Content)
    ↓
Convert to File
    ↓
Write to Disk
    ↓
[Syncthing syncs to laptop]
    ↓
[File appears in Obsidian Staging folder]
```

---

## Part 4: Daily Workflow

### 4.1 ReMarkable to Obsidian Flow

1. **Write on ReMarkable** - Take notes, journal entries, ideas
2. **Share via email** - Use ReMarkable's "Share" feature, select "Create link and send via email"
3. **n8n processes automatically** - Extracts OCR'd text, formats as markdown
4. **File appears in Obsidian** - Check `Journal/Staging/` folder
5. **Review and edit** - Clean up any OCR errors, add context
6. **Move to Daily** - When ready, move to `Journal/Daily/`

### 4.2 Shell Aliases

Add to `~/.bashrc` or `~/.zshrc`:

```bash
# Obsidian vault location
export OBSIDIAN_VAULT="$HOME/Documents/ObsidianVault"

# Fabric with Telos context
alias ft='fabric -C telos.md'

# Quick patterns
alias journal='fabric -C telos.md -p telos_journal'
alias jobfit='fabric -C telos.md -p analyze_job'
alias prep='fabric -C telos.md -p interview_prep'

# Check staging folder
alias staging='ls -la $OBSIDIAN_VAULT/Journal/Staging/'

# Open staging in Obsidian (if using obsidian CLI)
alias ostaging='obsidian $OBSIDIAN_VAULT/Journal/Staging/'
```

### 4.3 Morning Routine

```bash
# Check for new ReMarkable imports
staging

# Review any drafts, edit in Obsidian, move to Journal/Daily when done

# Quick goal review
cat $OBSIDIAN_VAULT/Telos/telos.md | fabric -p extract_recommendations
```

### 4.4 Evening Routine

```bash
# Journal entry (type or pipe from file)
echo "Today I worked on..." | journal

# Or save directly to Obsidian
echo "Today I worked on..." | fabric -C telos.md -p telos_journal -o "$OBSIDIAN_VAULT/Journal/Daily/$(date +%Y-%m-%d).md"
```

---

## Part 5: Additional Patterns

### 5.1 Interview Prep Pattern

```bash
mkdir -p ~/.config/fabric/patterns/interview_prep
nano ~/.config/fabric/patterns/interview_prep/system.md
```

```markdown
# IDENTITY AND PURPOSE

You prepare interview responses for a senior telecom professional. You help craft compelling narratives using the STAR format.

# CONTEXT

Candidate has 8 years at DISH/Boost, bridges technology and business without being a formal engineer. Targeting $165-180K Director-track roles.

# OUTPUT SECTIONS

## STORY OPTIONS
3 specific experiences that answer this question using STAR format.

## TECHNICAL POSITIONING
How to demonstrate expertise authentically.

## LIKELY FOLLOW-UPS
Questions they might ask next.

## CONFIDENCE ANCHOR
One specific past achievement that proves capability.

# OUTPUT INSTRUCTIONS

- Each story speakable in under 90 seconds
- Include specific numbers where available
- Balance confidence with authenticity
```

### 5.2 Job Analysis Pattern

```bash
mkdir -p ~/.config/fabric/patterns/analyze_job
nano ~/.config/fabric/patterns/analyze_job/system.md
```

```markdown
# IDENTITY AND PURPOSE

You analyze job postings for fit with a specific candidate profile.

# CANDIDATE PROFILE

- 8 years wireless infrastructure (DISH/Boost)
- Program/Product management
- $1.4B vendor program, $30M savings
- CBRS, 5G, spectrum expertise
- Executive MBA, non-traditional background
- Target: $165-180K, Director track

# OUTPUT SECTIONS

## FIT SCORE
Rate 1-10 with rationale.

## STRONG ALIGNMENT
Where experience directly matches.

## GAPS TO ADDRESS
Requirements needing positioning.

## POSITIONING STRATEGY
How to frame experience for this role.

## RED FLAGS
Any concerns about role or company.

# OUTPUT INSTRUCTIONS

- Be realistic about gaps
- Identify transferable skills
- Maximum 400 words
```

### 5.3 Weekly Synthesis Pattern

```bash
mkdir -p ~/.config/fabric/patterns/weekly_synthesis
nano ~/.config/fabric/patterns/weekly_synthesis/system.md
```

```markdown
# IDENTITY AND PURPOSE

You synthesize a week of journal entries into actionable insights.

# OUTPUT SECTIONS

## WEEK SUMMARY
2-3 sentences capturing the week.

## GOAL PROGRESS
Status on each active goal. Use: ✅ On track | ⚠️ Behind | 🔄 Blocked

## BEHAVIORAL PATTERNS
What worked vs. didn't.

## NEXT WEEK PRIORITIES
Maximum 3 specific items.

## TELOS UPDATES
Specific additions or changes to make.

# OUTPUT INSTRUCTIONS

- Ruthlessly honest about progress
- Maximum 400 words
- Output in clean markdown
```

---

## Part 6: Maintenance

### Weekly
- Review and clear `Journal/Staging/` folder
- Run weekly synthesis pattern
- Update Telos file with new insights

### Monthly
- Review and refine custom patterns
- Update Fabric: `go install github.com/danielmiessler/fabric/cmd/fabric@latest`
- Check n8n workflow is running smoothly
- Review Syncthing sync status

### Troubleshooting

**Fabric model not found:**
```bash
fabric --listmodels  # Check available models
ollama list          # Verify Ollama has the model
```

**n8n OAuth issues:**
- Access n8n via Tailscale URL (not local IP)
- Verify redirect URI in Google Cloud matches exactly

**Syncthing not syncing:**
- Create folder on source device first
- Check both devices show "Up to Date"
- Verify folder paths match expected locations

**ReMarkable emails not processing:**
- Check Gmail trigger filter (sender: `my@remarkable.com`)
- Verify "Your link is ready" emails (not "Document from my reMarkable")
- Test Jina AI URL manually if needed

---

## Quick Reference

```bash
# Fabric with context
fabric -C telos.md -p pattern_name

# List patterns
fabric -l

# List contexts
ls ~/.config/fabric/contexts/

# Check Obsidian staging
ls ~/Documents/ObsidianVault/Journal/Staging/

# n8n access (via Tailscale)
https://rpi-tailscale.tailc273b5.ts.net:5678
```

---

*This infrastructure grows with you. Start simple, add complexity as needed.*
