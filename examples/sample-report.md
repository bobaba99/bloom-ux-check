# UX Audit Report — Poster Editor (Pre-Launch)

**Date:** 2026-04-10
**App:** localhost:5173 (poster editor)
**Personas tested:** 4
**Total findings:** 7 (1 critical, 3 moderate, 3 low)

---

## Executive Summary

4 personas, 9 phases of evaluation. 82% of persona flow steps passed on first
run. 1 critical accessibility finding (keyboard trap in modal), 3 moderate
friction points, 3 low-priority polish items. Heuristic sweep scored 34/40
overall (H3: User Control scored lowest at 2/4).

---

## Heuristic Scores

| # | Heuristic | Score | Notes |
|---|-----------|-------|-------|
| 1 | Visibility of system status | 4 | Save pill, loading spinners present |
| 2 | Match between system and real world | 3 | "A0" label without dimensions in mm |
| 3 | User control and freedom | 2 | No undo after block deletion |
| 4 | Consistency and standards | 4 | Consistent button styles, nav patterns |
| 5 | Error prevention | 3 | Delete has confirm, but no unsaved changes warning |
| 6 | Recognition rather than recall | 4 | All options visible, labeled fields |
| 7 | Flexibility and efficiency | 3 | Keyboard nav works but no shortcuts listed |
| 8 | Aesthetic and minimalist design | 4 | Clean layout, clear hierarchy |
| 9 | Error recovery | 3 | Errors are specific but no retry option on network fail |
| 10 | Help and documentation | 4 | Onboarding tour, contextual tooltips |

**Total: 34/40**

---

## Accessibility Findings

| Check | Result | Severity |
|-------|--------|----------|
| Images without alt text | 0 | PASS |
| Unlabeled form inputs | 0 | PASS |
| Empty buttons | 1 (close button on modal) | MODERATE |
| Focus order | Logical | PASS |
| Keyboard-only: full flow | Tab trap in color picker modal | CRITICAL |
| Focus visibility | Custom outline present | PASS |
| Modal Escape key | Works | PASS |

**CRITICAL: Keyboard trap in color picker.** When the color picker modal opens,
focus enters but cannot exit via Tab or Escape. Keyboard-only users are stuck.

---

## Cognitive Walkthrough Findings

| Screen | Decision Point | Issue |
|--------|---------------|-------|
| Dashboard (empty) | "What do I click?" | PASS — clear "New Poster" CTA |
| Editor (first visit) | "Where do I start?" | PASS — tour activates |
| Editor (add content) | "How do I add a table?" | PASS — Insert tab visible |
| Editor (save) | "Did it save?" | PASS — "Saved" pill visible |
| Editor (delete block) | "Can I undo?" | FAIL — no undo, no warning |
| References section | "How do I add refs?" | MODERATE — empty state has no prompt |

---

## Edge State Results

| State | Result |
|-------|--------|
| Empty dashboard (no posters) | PASS — "Create your first poster" message |
| Empty references section | FAIL — blank space, no guidance |
| Very long title (200 chars) | PASS — text wraps, no overflow |
| Special chars in text (<, >, &) | PASS — rendered correctly |
| Browser refresh mid-edit | PASS — autosave restores state |
| Browser back from editor | MODERATE — returns to dashboard but no "unsaved?" |
| Two tabs editing same poster | FAIL — last save wins silently |
| Slow 3G network | PASS — loading spinner appears |

---

## Bug Taxonomy Hits

| Category | Finding | Severity |
|----------|---------|----------|
| Containment overflow | None found | - |
| Stale closure | None found (image drag works after edit) | - |
| Z-index stacking | Tour overlay + color picker conflict | LOW |
| State persistence | Style presets survive navigation | PASS |
| Layout collision | 5th block insertion finds open slot | PASS |

---

## Per-Persona Results

| Persona | Steps | Pass | Fail | Skip |
|---------|-------|------|------|------|
| Dr. Maya Chen | 9 | 8 | 1 | 0 |
| Prof. James Okafor | 9 | 7 | 2 | 0 |
| Kenji Watanabe | 10 | 9 | 1 | 0 |
| Maria Santos | 8 | 6 | 1 | 1 |

**Maya — Step 8 FAIL:** Save confirmation appears but dismisses in 1.5s.
Maya's test assumed she'd see it, but if she looked away during the save, she
wouldn't know it succeeded. Recommend: persistent "Saved" pill (not toast).

**Okafor — Step 7 FAIL:** Right-click context menu works in Chrome but shows
browser default in Firefox. Recommend: `event.preventDefault()` on
`contextmenu` event.

**Okafor — Step 9 FAIL:** Palette label in sidebar shows "Classic Academic"
after switching to "Psychology/Neuro". Sync lag — derived label not updating.

**Kenji — Step 9 FAIL:** Style preset survives page navigation but NOT browser
refresh (localStorage write fires on unmount, not on change). Recommend: write
to localStorage on every preset save, not just on component cleanup.

**Maria — Step 6 FAIL:** No undo after deleting a section. Ctrl+Z has no
effect. Recommend: implement undo stack or at minimum a "Restore last deleted
block" option.

**Maria — Step 8 SKIP:** References section empty state has no guidance text.
Tested but feature is not in current scope per PRD.

---

## Concrete Next Actions

### Code fixes
1. **CRITICAL** — Fix keyboard trap in color picker modal (add Tab containment
   + Escape to close)
2. **MODERATE** — Add `aria-label="Close"` to modal close button
3. **MODERATE** — Write to localStorage on preset save, not on unmount
4. **MODERATE** — Add `event.preventDefault()` to `contextmenu` handler for
   Firefox compatibility

### Product doc clarifications
5. Should references empty state show a prompt? (Not in PRD scope but Maria
   persona expects it)
6. Multi-tab editing behavior — should it warn or merge?

### Persona updates
7. Add a persona for a screen reader user (full accessibility flow)
8. Okafor's test should include a Firefox-specific step explicitly

---

*Report generated by bloom-ux-check skill. Total agent execution: ~22 minutes.*
