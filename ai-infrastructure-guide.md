WIP WARNING: THIS IS A WORKING COPY AND BEING UPDATED TO ACTUALLY WORK

# Personal AI Infrastructure Implementation Guide
## Zane's Setup - Bazzite Linux + Obsidian + n8n

---

## Your Stack

| Component | Status | Purpose |
|-----------|--------|---------|
| Fabric | ✅ Installed | Pattern-based AI workflows |
| Ollama + Gemma3 | ✅ Running | Local LLM (default for Fabric) |
| Claude Code | ✅ Installed | Complex reasoning tasks |
| Obsidian | ✅ Installed + synced to phone | Knowledge base & journal |
| n8n | ✅ On Raspberry Pi | Automation workflows |
| ReMarkable | ✅ Owned | Handwritten capture → email |

---

## Part 1: Directory Structure

### 1.1 Fabric Patterns Location

Default location (use this):
```
~/.config/fabric/patterns/
```

Your custom patterns go here alongside the built-in ones. When you run `fabric --updatepatterns`, only the official patterns update—your custom ones are preserved as long as they have unique names.

### 1.2 Obsidian Vault Structure

Create this structure in your Obsidian vault:
```
~/Obsidian/  (or wherever your vault is)
├── Journal/
│   ├── Daily/           # Processed daily entries
│   └── Staging/         # n8n drops ReMarkable notes here for editing
├── Telos/
│   ├── telos.md         # Your main Telos file
│   └── weekly/          # Weekly synthesis outputs
├── Learning/
│   └── python/
├── Career/
│   ├── interview-prep/
│   └── job-analysis/
└── Templates/
    └── daily-journal.md
```

### 1.3 Set Environment Variables

Add to `~/.bashrc` or `~/.zshrc`:
```bash
# Your Obsidian vault path
export OBSIDIAN_VAULT="$HOME/Obsidian"  # Adjust to your actual path

# Fabric defaults to local Ollama
export DEFAULT_MODEL="ollama/gemma3"

# Staging directory for n8n outputs
export JOURNAL_STAGING="$OBSIDIAN_VAULT/Journal/Staging"
```

---

## Part 2: Telos Integration with Fabric

### 2.1 Create Telos Context

Fabric contexts live in `~/.config/fabric/contexts/`. Create yours:

```bash
mkdir -p ~/.config/fabric/contexts/telos
```

Create the context file:
```bash
nano ~/.config/fabric/contexts/telos/telos.md
```

Paste a condensed version of your Telos for AI consumption:

```markdown
# TELOS CONTEXT: Zane

## Identity
- 53, Denver (15 years), engaged, DINK
- BA Film/Video, Executive MBA (2024)
- 8 years wireless infrastructure at DISH/Boost
- Meditation practitioner since 1995 
- r/denver moderator

## Current Mission
Career transition: IC → Director track

## Active Goals (Q1 2026)
- Secure role by end of Q1 (
- Python fundamentals by Jan 31 (pandas, data analysis)
- AI certification by Feb 15
- Pay off debt by July 2026

## Key Strengths
- Technology ↔ business bridge (not a formal engineer)
- $1.4B vendor program, $30M annual savings
- CBRS, 5G, spectrum expertise
- Community leadership, constructive dialogue

## Known Challenges
- Inconsistent meeting prep (when prepared = great, when not = messy)
- Filler words ("um") in presentations
- Imposter syndrome re: technical depth
- Discomfort with business networking

## Communication Style
- Direct, solution-focused
- Systematic over random
- Contemplative but action-oriented
- Values depth over surface

## Insecurities to Watch
1. "How technical is technical enough?" anxiety
2. Post-presentation self-doubt
3. Missing leadership confidence had prior role
```

### 2.2 Using Telos Context

```bash
# Any Fabric pattern with your context
echo "your input" | fabric -C telos.md -p pattern_name

# Example: Summarize with your context
echo "Meeting notes..." | fabric -C telos.md -p summarize -m "gemma3:latest"
```

---

## Part 3: Custom Patterns

All patterns go in `~/.config/fabric/patterns/`. Each pattern is a folder with a `system.md` file inside.

### 3.1 Pattern: telos_journal

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
- Reference actual goals when relevant
- Output in clean markdown
```

### 3.2 Pattern: interview_prep

```bash
mkdir -p ~/.config/fabric/patterns/interview_prep
nano ~/.config/fabric/patterns/interview_prep/system.md
```

```markdown
# IDENTITY AND PURPOSE

You prepare interview responses for a senior telecom professional. You help craft compelling narratives using the STAR format that demonstrate expertise without overclaiming.

# CONTEXT (when -C telos is used, this enhances it)

Candidate has 8 years at DISH/Boost, bridges technology and business without being a formal engineer. Targeting $165-180K Director-track roles.

# OUTPUT SECTIONS

## STORY OPTIONS
3 specific experiences that answer this question. For each:
- Situation (1 sentence)
- Task (1 sentence)  
- Action (2-3 sentences with specifics)
- Result (quantified if possible)

## TECHNICAL POSITIONING
How to demonstrate expertise authentically (not overclaiming).

## LIKELY FOLLOW-UPS
Questions they might ask next. Prepare for these.

## CONFIDENCE ANCHOR
One specific past achievement that proves capability for this.

# OUTPUT INSTRUCTIONS

- Each story should be speakable in under 90 seconds
- Include specific numbers where available
- Balance confidence with authenticity
- Prepare for "how technical are you really?" probes
```

### 3.3 Pattern: analyze_job

```bash
mkdir -p ~/.config/fabric/patterns/analyze_job
nano ~/.config/fabric/patterns/analyze_job/system.md
```

```markdown
# IDENTITY AND PURPOSE

You analyze job postings for fit with a specific candidate. You identify alignment, gaps, and positioning strategy.

# CANDIDATE PROFILE

- 8 years wireless infrastructure (DISH/Boost)
- Program/Product management
- $1.4B vendor program, $30M savings
- CBRS, 5G, spectrum expertise
- Executive MBA, non-traditional background
- Target: $165-180K, Director track

# OUTPUT SECTIONS

## FIT SCORE
Rate 1-10 with one-sentence rationale.

## STRONG ALIGNMENT
Where experience directly matches requirements.

## GAPS TO ADDRESS
Requirements needing explanation or positioning.

## POSITIONING STRATEGY
How to frame experience for this specific role.

## QUESTIONS TO ASK THEM
Strategic questions demonstrating insight.

## RED FLAGS
Any concerns about role, company, or fit.

# OUTPUT INSTRUCTIONS

- Be realistic about gaps
- Identify transferable skills for technical requirements
- Note salary range if disclosed
- Maximum 400 words
```

### 3.4 Pattern: weekly_synthesis

```bash
mkdir -p ~/.config/fabric/patterns/weekly_synthesis
nano ~/.config/fabric/patterns/weekly_synthesis/system.md
```

```markdown
# IDENTITY AND PURPOSE

You synthesize a week of journal entries into actionable insights for someone tracking professional development.

# OUTPUT SECTIONS

## WEEK SUMMARY
2-3 sentences capturing the week.

## GOAL PROGRESS
Status on each active short-term goal. Use: ✅ On track | ⚠️ Behind | 🔄 Blocked

## BEHAVIORAL PATTERNS
What worked vs. what didn't. Note preparation/outcome correlations.

## KEY DECISIONS
Decisions made this week and their reasoning.

## NEXT WEEK PRIORITIES
Maximum 3 specific items.

## TELOS UPDATES
Specific additions or changes to make to Telos file.

# OUTPUT INSTRUCTIONS

- Ruthlessly honest about progress vs. intentions
- Identify action/paralysis oscillations
- Maximum 400 words
- Output in clean markdown suitable for Obsidian
```

### 3.5 Pattern: remarkable_to_journal

For processing ReMarkable notes that come through n8n:

```bash
mkdir -p ~/.config/fabric/patterns/remarkable_to_journal
nano ~/.config/fabric/patterns/remarkable_to_journal/system.md
```

```markdown
# IDENTITY AND PURPOSE

You process handwritten notes (converted to text) from a ReMarkable tablet into structured journal entries. The input may be rough, abbreviated, or have OCR errors.

# YOUR TASK

1. Clean up OCR artifacts and expand abbreviations
2. Preserve the original meaning and voice
3. Structure into readable journal format
4. Flag anything unclear for human review

# OUTPUT FORMAT

## Date: [extract if present, otherwise "DATE_NEEDED"]

### Raw Thoughts
[Cleaned version of the handwritten content]

### Key Points
- [Extracted main points as bullets]

### Needs Review
[Any unclear text marked with ???]

---
*Processed from ReMarkable. Review before finalizing.*

# OUTPUT INSTRUCTIONS

- Preserve authentic voice, don't over-polish
- Mark unclear sections rather than guessing
- Keep structure simple for easy editing
- This is a DRAFT for human review, not final output
```

---

## Part 4: n8n Automation (ReMarkable → Obsidian)

### 4.1 The Flow

```
ReMarkable "Send to Email" 
    → Gmail receives PDF/text
    → n8n Gmail Trigger
    → Extract text from attachment
    → Save to Obsidian staging folder
    → (Optional) Send notification
    → You edit in Obsidian
    → Move to Journal/Daily when ready
```

### 4.2 n8n Workflow Setup

On your Raspberry Pi n8n instance, create this workflow:

**Node 1: Gmail Trigger**
- Trigger: On new email
- Filter: From `my@remarkable.com` (or whatever ReMarkable uses)
- Or filter by subject containing "ReMarkable" or your notebook name

**Node 2: Add a "Gmail" node**
- Add a new node: Gmail (not Gmail Trigger)
- Operation: Get Message
- Message ID: {{ $json.id }} (from the trigger)
- Add Options → Download Attachments: true

**Node 3: Extract Attachment**
- If PDF: Use n8n's PDF Extract node or call an external tool
- Input Binary Field = attachment_0

**Node 4: Format Output**
```javascript
// In a Code node
const today = new Date().toISOString().split('T')[0];
const emailBody = $input.first().json.text || '';
const subject = $input.first().json.subject || 'ReMarkable Note';

const output = `---
date: ${today}
source: remarkable
status: draft
---

# ReMarkable Import: ${subject}

## Raw Content

${emailBody}

---
*Imported via n8n. Edit and move to Journal/Daily when ready.*
`;

return [{ json: { content: output, filename: `${today}-remarkable-draft.md` } }];
```

**Node 4: Write to File**
- Use n8n's "Write Binary File" or SSH/SFTP to your laptop
- Path: `/path/to/Obsidian/Journal/Staging/`
- Filename: From previous node

**Alternative: Node 4 using Obsidian Local REST API**
If you install the "Local REST API" Obsidian plugin:
```
POST http://your-laptop:27123/vault/Journal/Staging/{filename}
```

### 4.3 Optional: Process with Fabric via n8n

Add a step to run Fabric on the content:

**Node: SSH Command**
```bash
echo "CONTENT_HERE" | fabric -p remarkable_to_journal > /path/to/output.md
```

Or use n8n's Execute Command node if n8n runs on the same machine as Fabric.

### 4.4 Notification

Add a final node to notify you:
- Pushover, Telegram, or email
- Message: "ReMarkable note ready for review in Obsidian staging"

---

## Part 5: Daily Workflow Integration

### 5.1 Shell Aliases

Add to `~/.bashrc`:

```bash
# Obsidian vault location
export OBSIDIAN_VAULT="$HOME/Obsidian"

# Quick Fabric with Telos context
alias ft='fabric -C telos'

# Journal workflow
alias journal='fabric -C telos -p telos_journal'
alias weekly='fabric -C telos -p weekly_synthesis'

# Job search
alias jobfit='fabric -C telos -p analyze_job'
alias interviewprep='fabric -C telos -p interview_prep'

# Process ReMarkable draft (if you want to re-run Fabric on staging files)
remarkable_process() {
    local file="$1"
    cat "$OBSIDIAN_VAULT/Journal/Staging/$file" | fabric -C telos -p remarkable_to_journal
}

# Save journal to Obsidian
journal_save() {
    local date_stamp=$(date +'%Y-%m-%d')
    local output="$OBSIDIAN_VAULT/Journal/Daily/${date_stamp}.md"
    fabric -C telos -p telos_journal -o "$output"
    echo "Saved to $output"
}

# Weekly synthesis from this week's journals
weekly_save() {
    local week=$(date +'%Y-W%W')
    cat "$OBSIDIAN_VAULT/Journal/Daily/"*.md | tail -7 | \
        fabric -C telos -p weekly_synthesis -o "$OBSIDIAN_VAULT/Telos/weekly/${week}.md"
    echo "Weekly synthesis saved"
}
```

### 5.2 Using Claude Code for Complex Tasks

When Gemma3 isn't enough, pipe to Claude Code:

```bash
# Generate something with Fabric, then enhance with Claude Code
fabric -p summarize < long_document.txt > summary.txt
claude "Review and improve this summary for executive audience" < summary.txt

# Or use Claude Code directly for complex reasoning
claude "Analyze this job posting against my background and create a positioning strategy" < job_posting.txt
```

### 5.3 Daily Routine

**Morning:**
1. Check `Journal/Staging/` for any ReMarkable imports
2. Edit and move to `Journal/Daily/`
3. Quick goal review: `cat $OBSIDIAN_VAULT/Telos/telos.md | fabric -p extract_recommendations`

**Evening:**
1. Brain dump on ReMarkable or directly:
   ```bash
   echo "Today I worked on..." | journal
   ```
2. Or type into staging file and process:
   ```bash
   cat $OBSIDIAN_VAULT/Journal/Staging/notes.md | journal_save
   ```

**Sunday:**
```bash
weekly_save
```

---

## Part 6: Obsidian Templates

### 6.1 Daily Journal Template

Create `Templates/daily-journal.md`:

```markdown
---
date: {{date}}
type: journal
status: complete
---

# {{date:YYYY-MM-DD}} - {{date:dddd}}

## Morning Intention


## What Happened


## Reflection


## Tomorrow's Focus


---
*Tags:* #journal
```

### 6.2 Fabric Output Template

For when you pipe Fabric output into Obsidian:

```markdown
---
date: {{date}}
source: fabric
pattern: 
---

# Fabric Output - {{date:YYYY-MM-DD}}

[OUTPUT GOES HERE]

---
*Generated via Fabric pattern: PATTERN_NAME*
```

---

## Part 7: Full n8n Workflow JSON

Here's an importable n8n workflow structure (adjust credentials):

```json
{
  "name": "ReMarkable to Obsidian",
  "nodes": [
    {
      "name": "Gmail Trigger",
      "type": "n8n-nodes-base.gmailTrigger",
      "position": [250, 300],
      "parameters": {
        "filters": {
          "sender": "my@remarkable.com"
        }
      }
    },
    {
      "name": "Extract Text",
      "type": "n8n-nodes-base.code",
      "position": [450, 300],
      "parameters": {
        "jsCode": "// Extract and format\nconst date = new Date().toISOString().split('T')[0];\nconst text = $input.first().json.text || $input.first().json.snippet;\nconst subject = $input.first().json.subject;\n\nreturn [{\n  json: {\n    content: `---\\ndate: ${date}\\nsource: remarkable\\nstatus: draft\\n---\\n\\n# ${subject}\\n\\n${text}\\n\\n---\\n*Review and edit before moving to Daily*`,\n    filename: `${date}-remarkable.md`\n  }\n}];"
      }
    },
    {
      "name": "Write to Staging",
      "type": "n8n-nodes-base.ssh",
      "position": [650, 300],
      "parameters": {
        "command": "echo '{{$json.content}}' > /home/zane/Obsidian/Journal/Staging/{{$json.filename}}"
      }
    },
    {
      "name": "Notify",
      "type": "n8n-nodes-base.telegram",
      "position": [850, 300],
      "parameters": {
        "text": "📝 ReMarkable note ready in Obsidian staging"
      }
    }
  ]
}
```

---

## Quick Reference

### Pattern Commands
```bash
fabric -p telos_journal          # Process journal entry
fabric -p interview_prep         # Prep for interview question
fabric -p analyze_job            # Analyze job posting
fabric -p weekly_synthesis       # Weekly review
fabric -p remarkable_to_journal  # Process ReMarkable text
```

### With Telos Context
```bash
fabric -C telos -p [pattern]     # Add your context to any pattern
```

### Save to Obsidian
```bash
fabric -p [pattern] -o "$OBSIDIAN_VAULT/path/file.md"
```

### Check Available Patterns
```bash
fabric -l                        # List all patterns
ls ~/.config/fabric/patterns/    # See pattern folders
```

---

## Checklist

- [ ] Verify Fabric default model is Gemma3: `fabric --listmodels`
- [ ] Create Obsidian folder structure
- [ ] Create `~/.config/fabric/contexts/telos/context.md`
- [ ] Create custom patterns in `~/.config/fabric/patterns/`
- [ ] Add shell aliases to `.bashrc`
- [ ] Set up n8n workflow on Raspberry Pi
- [ ] Test: `echo "Test entry" | fabric -C telos -p telos_journal`
- [ ] Configure ReMarkable to email notes to monitored address
