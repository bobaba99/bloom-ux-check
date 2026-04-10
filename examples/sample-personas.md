# Sample Personas — Scientific Poster Editor

Generated from a PRD describing a browser-based academic poster creation tool
targeting researchers, graduate students, and faculty.

---

## Persona 1: Dr. Maya Chen

**Bio:** Neuroscience postdoc at Johns Hopkins, 32. Presents at 4 conferences/year.
Has strong opinions about data visualization but limited design skills.

**Job-to-be-done:** Create a visually polished 48x36" poster for a neuroscience
conference in under 2 hours, using existing figures from R.

**Motivations:**
- Needs to look professional without hiring a designer
- Wants to reuse figures from previous publications
- Values speed — poster is due tomorrow
- Prefers clean, conservative layouts (no flashy colors)

**Frustrations / anti-patterns:**
- Would abandon the tool if it requires account creation before trying
- Gets frustrated by unclear save status (did it save? did I lose work?)
- Dislikes when drag-and-drop doesn't work as expected
- Won't read a tutorial — expects to figure it out by exploration

**Test flow:**
1. Navigate to `/` — expect a dashboard or landing page
2. Click "New Poster" or equivalent CTA
3. Enter a title: "Psilocybin-Assisted Therapy: A 12-Month Follow-Up"
4. Add an author: "Maya Chen, Johns Hopkins University"
5. Upload an image (figure from an R script)
6. Drag the image to a new position — verify it stays
7. Add a text block with methods description
8. Save — verify save confirmation appears
9. Return to dashboard — verify the poster appears with correct title

---

## Persona 2: Prof. James Okafor

**Bio:** Tenured psychology professor at University of Lagos, 55. Presents
at 1-2 conferences/year. Comfortable with PowerPoint but new to web-based
design tools.

**Job-to-be-done:** Convert an existing PowerPoint poster into the tool's
format, adding proper academic structure (abstract, methods, results, refs).

**Motivations:**
- Needs conference-compliant dimensions and formatting
- Wants to follow traditional academic poster conventions
- Values reliability over novelty — "does it print correctly?"
- Needs clear section labels and structure

**Frustrations / anti-patterns:**
- Would leave if the interface looks "too modern" or game-like
- Gets confused by implicit auto-save (prefers explicit "Save" button)
- Needs visible undo — makes frequent mistakes with mouse precision
- Institutional laptop has restricted browser (no extensions)

**Test flow:**
1. Navigate to `/` on Firefox (non-Chrome browser test)
2. Create new poster, select A0 size (conference requirement)
3. Add structured sections: Abstract, Introduction, Methods, Results
4. Enter body text in the Methods section
5. Apply bold formatting to a key phrase
6. Try keyboard navigation (Tab between fields)
7. Right-click a table cell — expect context menu (not browser default)
8. Change the poster's color palette to a conservative option
9. Verify the palette name updates in the sidebar label

---

## Persona 3: Kenji Watanabe

**Bio:** 2nd-year PhD student in computational biology, 26. Power user of
LaTeX, skeptical of WYSIWYG tools. Uses keyboard shortcuts for everything.

**Job-to-be-done:** Build a data-heavy poster with multiple tables and code
snippets, using keyboard shortcuts as much as possible.

**Motivations:**
- Wants keyboard-first workflow (minimal mouse usage)
- Needs to display tabular data with precise formatting
- Values export fidelity — "will it look the same when I print?"
- Wants to save and recall custom style presets

**Frustrations / anti-patterns:**
- Would bounce if there's no keyboard navigation for tables
- Gets frustrated by CSS-only effects that don't survive print
- Dislikes losing custom settings on page reload
- Expects Ctrl+Z to work universally

**Test flow:**
1. Navigate to editor, create a poster
2. Insert a 5x4 table
3. Navigate cells with Tab and Shift+Tab
4. Type data: "p<0.001" (special character test)
5. Apply bold to header row
6. Arrow key navigation at cell boundaries
7. Paste TSV data from clipboard
8. Save a custom style preset
9. Navigate away and back — verify preset persists
10. Resize a column by dragging the handle

---

## Persona 4: Maria Santos

**Bio:** Undergraduate research assistant, 20. First time presenting at a
conference. Has never made an academic poster before.

**Job-to-be-done:** Create her first-ever conference poster with guidance,
following her advisor's template suggestions.

**Motivations:**
- Needs hand-holding — doesn't know poster conventions
- Wants templates or examples to start from
- Needs clear feedback on what's "good" vs what needs improvement
- Values encouragement and clear progress indicators

**Frustrations / anti-patterns:**
- Would be overwhelmed by too many options on first visit
- Doesn't know what "A0" or "48x36" means — needs human-readable descriptions
- Would miss features hidden behind hover states
- Needs explicit onboarding, not "figure it out"

**Test flow:**
1. Navigate to `/` as a brand new user (clear localStorage first)
2. Verify onboarding tour or welcome screen appears
3. Follow the tour steps — verify each step highlights the correct area
4. Create a poster using a template
5. Hover over a toolbar button — verify tooltip explains what it does
6. Make a mistake (delete a section) — verify undo is available
7. Look for help/guidance — verify it exists and is findable
8. Check empty state of references section — helpful prompt or blank?
