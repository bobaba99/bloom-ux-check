# ux-persona-check

A research-grounded UX evaluation skill for AI coding agents. Generates
realistic user personas from product documents, then drives a live app through
each persona's workflow using browser automation — combining persona flows,
heuristic evaluation (Nielsen's 10), cognitive walkthrough, accessibility
audit, and edge-state resilience testing.

## What it does

1. **Reads your PRD** (or README, spec, product doc) to understand target users
2. **Generates 3-6 realistic personas** with concrete test flows
3. **Runs a heuristic sweep** (Nielsen's 10 usability heuristics, scored 0-4)
4. **Audits accessibility** (automated WCAG checks + keyboard-only walkthrough)
5. **Cognitive walkthrough** (first-time user perspective at each decision point)
6. **Tests edge states** (empty, extreme data, loading, persistence, multi-tab)
7. **Scans for invisible bug patterns** (stale closures, overflow clipping, z-index conflicts, etc.)
8. **Executes persona flows** via browser automation
9. **Produces an actionable report** with findings, screenshots, and fix suggestions

## Why this skill exists

Most UX bugs are invisible in code review. Stale closure state, overflow
clipping at boundaries, caret miscomputation in rich text, z-index stacking
conflicts — these only surface when you drive the app through realistic
scenarios in a real browser. This skill automates that process using
empirically validated methods.

Research shows no single usability evaluation method catches all problems
(Jaspers 2009). This skill combines four complementary approaches to maximize
coverage. See [references/research-grounding.md](references/research-grounding.md)
for the full evidence base.

## Compatibility

Works with any AI coding agent that has browser automation tools:

| Agent | Browser Tool | Status |
|-------|-------------|--------|
| **Claude Code** | chrome-devtools-mcp, Playwright MCP | Tested |
| **Cursor** | Any browser MCP server | Compatible |
| **Codex** | Playwright MCP, browser tools | Compatible |
| **OpenCode** | MCP browser servers | Compatible |
| **Ollama + open-weight models** | Via MCP bridge | Compatible |
| **Any MCP-capable agent** | Any browser automation | Compatible |

The skill references generic browser actions (navigate, click, type, snapshot,
screenshot) and adapts to whichever tools are available.

## Installation

### Claude Code

```bash
# Option 1: Clone to your skills directory
git clone https://github.com/YOUR_USERNAME/ux-persona-check.git ~/.claude/skills/ux-persona-check

# Option 2: Add as a project skill
git clone https://github.com/YOUR_USERNAME/ux-persona-check.git .claude/skills/ux-persona-check
```

The skill will be automatically discovered. Invoke with:
```
Run ux-persona-check on this repo
```

### Cursor

```bash
# Clone to your Cursor skills directory
git clone https://github.com/YOUR_USERNAME/ux-persona-check.git ~/.cursor/skills/ux-persona-check
```

Or add the SKILL.md content to your project's `.cursorrules` file.

### Codex / OpenCode / Other MCP-capable agents

```bash
# Clone anywhere and point your agent config to the SKILL.md
git clone https://github.com/YOUR_USERNAME/ux-persona-check.git ~/skills/ux-persona-check
```

Then reference the SKILL.md path in your agent's skill/instruction configuration.

### Ollama / Local models

```bash
git clone https://github.com/YOUR_USERNAME/ux-persona-check.git ~/skills/ux-persona-check
```

Include the SKILL.md content in your system prompt or instruction file.
Requires an MCP bridge or direct Playwright/Puppeteer integration for browser
automation.

## Requirements

- A running web application (localhost or deployed)
- Browser automation capability (any of):
  - [chrome-devtools-mcp](https://github.com/nicholasgriffintn/chrome-devtools-mcp)
  - [Playwright MCP](https://github.com/microsoft/playwright-mcp)
  - Any tool that can navigate, click, type, and take snapshots/screenshots
- A product document (PRD, README, spec) describing target users and features

## File structure

```
ux-persona-check/
  SKILL.md                              # Main skill (agent instructions)
  references/
    heuristic-checklist.md              # Nielsen's 10 detailed scoring guide
    bug-taxonomy.md                     # 10 invisible bug patterns + detection scripts
    research-grounding.md               # Empirical evidence for each phase
  examples/
    sample-personas.md                  # Example persona output
    sample-report.md                    # Example audit report
  README.md                            # This file
  LICENSE                              # MIT
```

## Example invocations

```
Run ux-persona-check on this repo
Create personas from PRD.md and verify the editor is usable for each
My landing page is live on localhost:3000 — check it with realistic users
Run a pre-launch UX audit against the staging deployment
```

## What good output looks like

- 4 personas with 5-8 step test flows each
- Heuristic scores (0-4) for all 10 heuristics with specific violations
- Accessibility: 0 critical violations, keyboard flow documented
- 80% of persona steps pass on first run
- 2-5 concrete, non-generic friction points
- Screenshots of worst failures
- Actionable fix suggestions (code changes, not vague advice)
- Developer reads the report in < 10 minutes

## Research basis

This skill is grounded in 20+ peer-reviewed papers spanning CHI, IEEE, and
medical informatics. Key findings:

- Personas improve user identification accuracy over analytics alone
  (Salminen et al. 2020, CHI, n=34)
- Heuristic evaluation finds 5x more problems than user testing
  (Maguire & Isherwood 2018, n=32)
- Cognitive walkthrough catches higher-severity issues (avg 3/4) than
  HE (avg 2/4), especially for learnability (Ginting et al. 2021)
- Automated a11y tools cover at most 50% of WCAG criteria
  (Vigo et al. 2013, 211 citations)
- No single method catches everything — combining methods is essential
  (Jaspers 2009, 656 citations)

Full citations: [references/research-grounding.md](references/research-grounding.md)

## Contributing

PRs welcome. If you've used this skill and found bugs it missed, please
open an issue describing the bug category — it may warrant a new entry in
the [bug taxonomy](references/bug-taxonomy.md).

## License

MIT
