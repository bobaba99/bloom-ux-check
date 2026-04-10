# Heuristic Evaluation — Detailed Scoring Guide

Based on Jakob Nielsen's 10 Usability Heuristics with actionable browser-testable
criteria for each.

## Scoring Scale

| Score | Meaning | Action |
|-------|---------|--------|
| 0 | Not applicable | Skip |
| 1 | Critical violation — users will fail or abandon | Must fix before launch |
| 2 | Minor violation — users can work around it | Should fix |
| 3 | Mostly passes with small gaps | Nice to fix |
| 4 | Exemplary implementation | Document as a pattern |

## H1: Visibility of System Status

**What to check:**
- After clicking "Save" — is there a confirmation (toast, pill, checkmark)?
- During long operations — is there a spinner, progress bar, or skeleton?
- After form submission — does the user know it succeeded or failed?
- File upload — is there progress indication?
- Navigation — does the active page/tab highlight?

**How to test:**
```javascript
// Check for loading indicators
const spinners = document.querySelectorAll('[class*="spinner"], [class*="loading"], [role="progressbar"]');
const skeletons = document.querySelectorAll('[class*="skeleton"], [class*="placeholder"]');
```

**Score 1 if:** User clicks a button and nothing visually changes for >1 second.
**Score 4 if:** Every state transition has immediate visual feedback.

## H2: Match Between System and Real World

**What to check:**
- Are labels in the user's language, not developer jargon?
- Do icons match real-world metaphors (floppy disk for save is dated but understood)?
- Is date format appropriate for the locale?
- Are technical terms explained on first use?

**How to test:**
- Read every visible label on the page aloud — would a non-technical user understand?
- Check for developer-facing terms: "null", "undefined", "404", "error code", "payload"

**Score 1 if:** Critical actions use jargon the target user won't know.
**Score 4 if:** All terminology matches the user's domain vocabulary.

## H3: User Control and Freedom

**What to check:**
- Can users undo the last action?
- Is there a "Cancel" on every form/dialog?
- Can users go back from any screen?
- Are destructive actions (delete, clear) reversible or at least confirmed?
- Can users close modals by clicking outside or pressing Escape?

**How to test:**
```javascript
// Check for undo capability
const undoButtons = document.querySelectorAll('[aria-label*="undo"], button:has(> [class*="undo"])');
// Check that modals have close mechanisms
const modals = document.querySelectorAll('[role="dialog"]');
modals.forEach(m => {
  const hasClose = m.querySelector('button[aria-label*="close"], button:has(> [class*="close"])');
  const hasCancel = m.querySelector('button:has(> :is(:contains("Cancel"), :contains("cancel")))');
});
```

**Score 1 if:** Destructive actions have no confirmation and no undo.
**Score 4 if:** Every action is reversible or clearly warned.

## H4: Consistency and Standards

**What to check:**
- Do similar elements look and behave the same across pages?
- Is the same action called the same thing everywhere? ("Delete" vs "Remove" vs "Trash")
- Are button styles consistent (primary, secondary, destructive)?
- Does navigation work the same way on every page?
- Are form patterns consistent (labels above or beside, validation inline or summary)?

**How to test:**
- Navigate 3+ different pages, screenshot each, compare visual patterns
- Search for the same action (e.g., "delete") across the app — is the UI consistent?

**Score 1 if:** Same action has different names or behaviors across pages.
**Score 4 if:** Consistent design language throughout.

## H5: Error Prevention

**What to check:**
- Submit a form with empty required fields — is the submit button disabled, or does
  the form validate before submission?
- Enter invalid data (wrong format email, negative number) — is it caught before submit?
- Click "Delete" — is there a confirmation dialog?
- Can users accidentally navigate away from unsaved changes?

**How to test:**
```javascript
// Check for required field validation
const required = document.querySelectorAll('[required], [aria-required="true"]');
// Check for type-specific validation
const emails = document.querySelectorAll('input[type="email"]');
const numbers = document.querySelectorAll('input[type="number"]');
```

**Score 1 if:** Users can trigger destructive or invalid actions with no guard.
**Score 4 if:** Invalid states are prevented, not just detected after the fact.

## H6: Recognition Rather Than Recall

**What to check:**
- Are navigation options always visible (not hidden behind a hamburger on desktop)?
- Are form fields labeled (not just placeholder text that disappears on focus)?
- Are recently used items accessible?
- Are instructions visible during the task, not only shown once?

**How to test:**
```javascript
// Check for placeholder-only labels (anti-pattern)
const placeholderOnly = [...document.querySelectorAll('input[placeholder]')].filter(
  i => !i.labels?.length && !i.closest('label') && !i.getAttribute('aria-label')
);
```

**Score 1 if:** Users must memorize instructions or options to complete tasks.
**Score 4 if:** All options and instructions are visible in context.

## H7: Flexibility and Efficiency of Use

**What to check:**
- Are there keyboard shortcuts for frequent actions?
- Can power users skip onboarding/tutorials?
- Are there bulk actions for repetitive tasks?
- Can users customize the interface (layout, preferences)?
- Is there a search/filter for large lists?

**Score 1 if:** Repetitive tasks have no shortcuts and no bulk operations.
**Score 4 if:** Both novice and expert paths exist for key workflows.

## H8: Aesthetic and Minimalist Design

**What to check:**
- Count primary CTAs (buttons) per screen — more than 2 is a red flag
- Is there visual hierarchy (clear heading, subheading, body)?
- Are decorative elements competing with functional ones?
- Is information density appropriate (not too sparse, not overwhelming)?
- Is whitespace used effectively?

**How to test:**
```javascript
// Count primary-style buttons per viewport
const primaryBtns = document.querySelectorAll(
  'button[class*="primary"], button[class*="cta"], [role="button"][class*="primary"]'
);
// Check information density
const textNodes = document.querySelectorAll('p, span, label, h1, h2, h3, h4, h5, h6');
```

**Score 1 if:** Screen is cluttered with competing elements and no visual hierarchy.
**Score 4 if:** Clean layout with clear hierarchy and purposeful whitespace.

## H9: Help Users Recognize, Diagnose, and Recover from Errors

**What to check:**
- Submit invalid data — is the error message specific? ("Email must contain @" not "Invalid input")
- Does the error message say what to fix?
- Is the error displayed near the field that caused it?
- After an error, is the user's valid input preserved?
- Network errors — is there a retry option?

**How to test:**
- Submit empty/invalid forms and screenshot the error states
- Check error message specificity and placement

**Score 1 if:** Errors show generic messages ("Something went wrong") with no guidance.
**Score 4 if:** Every error is specific, contextual, and actionable.

## H10: Help and Documentation

**What to check:**
- Is there onboarding for first-time users?
- Are tooltips available for complex features?
- Is help contextual (appears where needed, not just a FAQ page)?
- Can users search for help?
- Is the help concise and task-focused?

**Score 1 if:** Complex features have no guidance and no documentation.
**Score 4 if:** Contextual help appears at decision points without overwhelming.
