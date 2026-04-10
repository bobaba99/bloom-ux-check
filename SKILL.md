---
name: bloom-ux-check
description: Generate realistic user personas from a product document, then run UI/UX flow checks against a running app using browser automation (Playwright, Chrome DevTools MCP, or similar). Combines persona-driven flow testing with heuristic evaluation, accessibility audit, cognitive walkthrough, and edge-state resilience testing. Use when asked to "test the UX", "check how real users would use this", "run persona-based UI checks", or after significant UI changes.
---

# UX Persona Check

Turn a product document into actionable user personas, then drive a live app
through each persona's motivations to find friction a single developer can't
easily spot alone. Goes beyond "does it work?" to ask "does it work *for this
person, in this state, under these conditions*?"

## Research basis

This skill combines four empirically validated UX evaluation methods. Research
shows no single method catches all usability problems (Jaspers 2009, 656 cit.),
but together they provide comprehensive coverage:

- **Personas** accelerate user identification accuracy vs raw analytics
  (Salminen et al. 2020, CHI, 53 cit.) and surface navigation and
  user-referenced findings that non-persona evaluation misses (Friess 2015)
- **Heuristic evaluation** finds ~5x more individual problems than user
  testing (Maguire & Isherwood 2018), though structured scoring is needed
  to maintain effectiveness for non-expert evaluators (Law & Hvannberg 2004)
- **Cognitive walkthrough** catches learnability and error issues that HE
  misses, with higher severity ratings (Ginting et al. 2021, avg 3 vs 2)
- **Automated accessibility testing** covers at most 50% of WCAG criteria
  (Vigo et al. 2013, 211 cit.) — manual keyboard testing is essential

See `references/research-grounding.md` for full citations and methodology
rationale for each phase.

## When to invoke

- User asks "can you check this UI from a user's perspective"
- User asks "generate personas from my PRD" or "create test personas"
- User asks to validate a flow before shipping
- After a large UI refactor where the developer wants a fresh pair of eyes
- Before a release checkpoint

## What you need

1. **A product document** — PRD, spec, README, or similar that describes target
   users, features, and UX principles
2. **A running app** — usually `localhost:3000`, `localhost:5173`, or similar
3. **Browser automation** — any of:
   - `chrome-devtools-mcp` (tools prefixed `mcp__plugin_chrome-devtools-mcp_*`)
   - Playwright MCP (`mcp__plugin_playwright_playwright__*`)
   - Any tool that can navigate, click, type, take snapshots/screenshots

If any of these are missing, **stop and ask** rather than guessing.

## Workflow Overview

| Phase | Purpose | Output |
|-------|---------|--------|
| 1. Read | Extract who, what, why from the product doc | Scoped test plan |
| 2. Personas | Generate 3-6 realistic users | `docs/personas/<product>-personas.md` |
| 3. Heuristic sweep | Structural quality check (Nielsen's 10) | Scored checklist |
| 4. Accessibility audit | WCAG 2.1 AA automated + keyboard checks | Violation list |
| 5. Cognitive walkthrough | First-time user perspective per screen | Decision-point flags |
| 6. Edge states | Empty, extreme, loading, error, persistence | State resilience map |
| 7. Bug taxonomy scan | Pattern-based checks for invisible bugs | Categorized findings |
| 8. Persona flows | Execute each persona's motivated flow | Per-persona pass/fail |
| 9. Report | Synthesize all findings | Actionable report |

---

### Phase 1 — Read the Product Document

Read the document in full. Extract:

- **Who is the target user?** Look for "Overview", "Target Users", "Audience"
- **What are they trying to do?** Feature list + problem statement
- **What friction does the doc explicitly avoid?** "Zero friction", "no signup",
  "constraint as feature" — these become test assertions
- **What's in scope vs deferred?** Don't test against unshipped features

### Phase 2 — Generate Personas

Write 3-6 personas to `docs/personas/<product>-personas.md`. Each **must** have:

- **Name + 1-line bio** — realistic and specific, not archetypal
- **Job-to-be-done** — one concrete sentence
- **Motivations** — 3-5 bullet points
- **Frustrations / anti-patterns** — what would make them bounce
- **Concrete test flow** — numbered steps using browser automation

**Diversity checklist** — cover range across:

- Technical comfort (novice to expert)
- Screen size / device (mobile, laptop, large desktop)
- Time pressure (minutes vs hours)
- Input modality (mouse, keyboard-only, touch, assistive tech)
- Institutional constraints (locked-down laptop, no-install, slow network)
- Domain variety (if the app crosses fields)
- Visual preferences (conservative vs bold)

**Red flags in generated personas:**

- Generic names like "Power User" or "Casual User" — use real names + backgrounds
- Test flows that just click every button — redo as goal-motivated flows
- Frustrations copy-pasted between personas — differentiate

### Phase 3 — Heuristic Sweep (Nielsen's 10)

Before running persona flows, evaluate structural quality. For each heuristic,
navigate 2-3 key screens and score 0-4:

| # | Heuristic | What to check |
|---|-----------|---------------|
| 1 | Visibility of system status | Loading states, save confirmations, progress indicators |
| 2 | Match between system and real world | Labels, icons, terminology familiar to target users |
| 3 | User control and freedom | Undo, back, cancel — start a destructive action, can you bail? |
| 4 | Consistency and standards | Same action = same pattern everywhere (buttons, forms, nav) |
| 5 | Error prevention | Guards on dangerous actions, disabled buttons for invalid states |
| 6 | Recognition rather than recall | Options visible, not hidden; labeled fields, not just placeholders |
| 7 | Flexibility and efficiency | Keyboard shortcuts, bulk actions, skippable wizards |
| 8 | Aesthetic and minimalist design | Visual clutter, competing CTAs (>2 primary per screen = red flag) |
| 9 | Error recovery | Specific error messages that say *what to fix*, not just "invalid" |
| 10 | Help and documentation | Onboarding, tooltips, contextual help |

**Scoring:** 0=N/A, 1=critical violation, 2=minor violation, 3=mostly passes,
4=exemplary. Any score <=2 is a finding.

See `references/heuristic-checklist.md` for detailed evaluation criteria.

### Phase 4 — Accessibility Audit

Run automated checks via `page.evaluate()`:

```javascript
const a11y = {};
// Images without alt text
const imgs = document.querySelectorAll('img');
a11y.missingAlt = [...imgs].filter(i => !i.alt && !i.getAttribute('aria-label')).length;
// Form inputs without labels
const inputs = document.querySelectorAll('input, select, textarea');
a11y.unlabeled = [...inputs].filter(i => !i.labels?.length && !i.getAttribute('aria-label')).length;
// Buttons without accessible names
const btns = document.querySelectorAll('button');
a11y.emptyButtons = [...btns].filter(b => !b.textContent.trim() && !b.getAttribute('aria-label')).length;
// Focus visibility — check if focus-visible styles exist
a11y.focusStyles = !!document.querySelector(':focus-visible');
```

**Keyboard-only walkthrough:**

- Tab through the full page — is focus order logical?
- Can every interactive element be reached?
- Is focus *visible* (not just browser default removed via `outline: none`)?
- Can modals be closed with Escape?
- Do custom dropdowns work with arrow keys?

If a persona specifies assistive tech or institutional constraints, run their
entire flow keyboard-only.

### Phase 5 — Cognitive Walkthrough

For each screen personas will encounter, answer 4 questions at every decision
point:

1. **Will the user know what to do?** — Is the next action obvious?
2. **Will they notice the correct action?** — Is it prominent? Competing elements?
3. **Will they understand the feedback?** — Does the system confirm what happened?
4. **Can they recover from the wrong choice?** — Back button, undo, clear error?

Any "no" or "maybe" is a finding. This catches issues invisible to developers
who already know the interface.

### Phase 6 — Edge State Testing

Test the app in states developers rarely encounter in their own workflows:

**Empty states:**
- Fresh account, zero data — helpful message or blank space?
- List/table with no items — guidance or void?
- Search with no results — does it suggest alternatives?

**Data extremes:**
- Very long text (200+ chars in name fields, multi-paragraph in single-line)
- Special characters: `<script>`, emoji, RTL text, CJK characters
- Boundary numbers: 0, -1, 999999, decimals
- Large datasets: 100+ items in a list

**Loading & transition states:**
- Throttle to Slow 3G — are there loading indicators?
- Double-click submit rapidly — does it double-submit?
- Navigate away during save — is data lost?

**State persistence:**
- Refresh (F5) — does state restore or reset?
- Browser back/forward — expected navigation or broken?
- Deep link: copy URL, new tab — same view?
- Multi-tab: edit in one, what happens in the other?
- Close and reopen — session preserved?

### Phase 7 — Bug Taxonomy Scan

These bug categories are **invisible in code review** and require live browser
testing. Check each pattern that applies to the app:

| Category | What to test | How to detect |
|----------|-------------|---------------|
| **Containment overflow** | Interactive controls near container edges | Place/move elements to boundaries, check for clipping |
| **Stale closure state** | Drag/resize after a re-render trigger | Edit content, then immediately drag — does it snap back? |
| **Rich text caret bugs** | Arrow/Tab nav in formatted text | Apply bold/italic, then arrow-key navigate at boundaries |
| **Transform scaling** | Dropdowns/tooltips inside scaled containers | Zoom in, open a dropdown — is it the right size? |
| **Z-index stacking** | Overlays competing (tour + modal + context menu) | Open multiple overlays simultaneously |
| **State persistence scope** | Data survives navigation | Save settings, navigate away, return — still there? |
| **Layout collision** | New elements spawning on existing ones | Add multiple items rapidly — do they stack? |
| **Sync lag** | Derived UI after state updates | Change a setting, verify all dependent displays update |
| **Positional mismatch** | Controls anchored to wrong reference | Resize content (add rows, wrap text) — do controls follow? |
| **Event capture vs bubble** | Paste interception in contentEditable | Paste TSV into contentEditable inside a custom paste handler — does it intercept? |

See `references/bug-taxonomy.md` for detailed detection scripts and fix patterns.

### Phase 8 — Run Persona Flows

For each persona, execute their test flow:

1. Navigate to entry URL
2. Take snapshot for accessibility tree (use snapshot *before* screenshot)
3. Walk through the flow step-by-step:
   - Click by element reference from snapshot
   - Type into fields
   - Assert visibility via snapshot text search
   - Assert no console errors
   - Screenshot at key checkpoints
4. Record each step as PASS, FAIL, or SKIP (feature not shipped)

**Conventions:**
- Snapshot before screenshot — snapshots give reliable targets
- Don't drive impossible flows — if a feature isn't shipped, mark SKIP
- Console warnings = info, console errors = potential fail
- If a persona specifies reduced motion, emulate `prefers-reduced-motion`

### Phase 9 — Report

Write a single report to `docs/ux-audit/<date>/report.md` with:

1. **Executive summary** — X personas, Y findings, Z critical
2. **Heuristic scores** — 10-item table with scores and worst violations
3. **Accessibility findings** — automated + keyboard audit results
4. **Per-persona pass/fail** — table with step-level detail for failures only
5. **Edge state results** — which states are broken
6. **Bug taxonomy hits** — categorized by pattern, not by persona
7. **Screenshots** of worst failures
8. **Concrete next actions** grouped as:
   - Code fix (specific, actionable)
   - Product doc clarification
   - Persona update (gaps found)

The report should be terse and actionable. Describe outcomes, not clicks.

## Regression Checklist Template

After the first audit, generate a project-specific regression checklist as
JavaScript assertions that can be re-run on every subsequent audit. Template:

```javascript
// Template — adapt selectors and thresholds to your project
const results = {};

// Layout collision: key elements must not overlap
const elA = document.querySelector('[data-component="header"]');
const elB = document.querySelector('[data-component="content"]');
if (elA && elB) {
  const rA = elA.getBoundingClientRect();
  const rB = elB.getBoundingClientRect();
  results.headerContentOverlap = rB.top < rA.bottom;
}

// Containment: interactive controls must not be clipped
const container = document.querySelector('[data-main-container]');
const cr = container?.getBoundingClientRect();
let clipped = 0;
document.querySelectorAll('button, [role="button"]').forEach(b => {
  const br = b.getBoundingClientRect();
  if (br.width > 0 && cr && (br.right > cr.right + 5 || br.left < cr.left - 5)) clipped++;
});
results.clippedControls = clipped;

// Accessibility: no empty buttons or unlabeled inputs
results.emptyButtons = document.querySelectorAll('button:empty:not([aria-label])').length;
results.unlabeledInputs = [...document.querySelectorAll('input:not([aria-label])')].filter(
  i => !i.labels?.length
).length;
```

Save this as `docs/ux-audit/<date>/regression.js` and re-run every audit.
**Every check must pass.** Any failure is CRITICAL.

## Constraints

- **Read-only.** Never alter production data, push code, or change app state
  beyond what the persona flow requires.
- **No credential guessing.** If a flow hits an auth wall, that IS a finding.
- **Don't re-run passing tests.** If a persona passes, don't loop.
- **Cap at ~30 minutes.** This is a quality check, not a full test harness.
- **Agent-agnostic.** Use whichever browser automation tools are available.
  Prefer snapshot/accessibility-tree tools over screenshot-only when possible.

## Example invocations

- "Run bloom-ux-check on this repo"
- "Create personas from PRD.md and verify the editor is usable for each"
- "My landing page is live on localhost:3000 — check it with realistic users"
- "Run a pre-launch UX audit against the staging deployment"

## What good looks like

- 4 personas, each with a 5-8 step test flow
- Heuristic sweep with 0-2 scores explained
- Accessibility: 0 critical violations, keyboard flow documented
- 80% of persona steps pass on first run
- 2-5 concrete friction points surfaced (not generic)
- A screenshot of the worst-looking moment
- A suggested code fix or product doc update for each finding
- Total developer time to read the report: < 10 minutes
