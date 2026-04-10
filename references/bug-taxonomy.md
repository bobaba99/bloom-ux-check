# Bug Taxonomy — Patterns Invisible to Code Review

These bug categories emerge from real-world UX testing. They share a common
trait: **the code looks correct in isolation, but the bug appears under specific
runtime conditions** (timing, spatial position, state combinations, DOM
structure). Code review, linting, and unit tests will not catch them.

Each category includes: the pattern, why it's invisible, how to detect it, and
how to fix it.

---

## 1. Containment Overflow

**Pattern:** Interactive controls (buttons, handles, resize grips) use negative
offsets (`left: -24px`, `top: -9px`) to appear on container edges. The parent
has `overflow: hidden`. When the element is near the container boundary, controls
are clipped and become invisible or unclickable.

**Why invisible:** Works when elements are centered. Only fails at edges — a
spatial edge case that requires real layout testing.

**Detection script:**
```javascript
function checkContainmentOverflow(containerSelector) {
  const container = document.querySelector(containerSelector);
  if (!container) return { error: 'Container not found' };
  const cr = container.getBoundingClientRect();
  let clipped = [];
  container.querySelectorAll('button, [role="button"], [class*="handle"]').forEach(el => {
    const br = el.getBoundingClientRect();
    if (br.width > 0 && (
      br.right > cr.right + 2 ||
      br.left < cr.left - 2 ||
      br.bottom > cr.bottom + 2 ||
      br.top < cr.top - 2
    )) {
      clipped.push({
        element: el.className || el.tagName,
        overflow: {
          right: Math.max(0, br.right - cr.right),
          left: Math.max(0, cr.left - br.left),
          bottom: Math.max(0, br.bottom - cr.bottom),
          top: Math.max(0, cr.top - br.top)
        }
      });
    }
  });
  return { clippedCount: clipped.length, clipped };
}
```

**How to test:** Move/place elements to all four container edges. Check that
controls remain visible and clickable.

**Fix pattern:** Move controls inward (accept slight overlap with content) or
render controls via portal outside the clipped container.

---

## 2. Stale Closure in Event Handlers

**Pattern:** A `useCallback` or event listener captures state at creation time.
During a multi-event sequence (drag, resize), React re-renders mid-sequence
(autosave, contentEditable input), creating a new state reference. The handler
still holds the old snapshot, causing it to operate on stale data.

**Why invisible:** The dependency array looks correct. The bug only manifests
when re-renders happen *between* events in a sequence — a timing race condition.

**How to test:**
1. Edit something that triggers a re-render (type in a text field, toggle a setting)
2. Immediately (within 500ms) start a drag or resize operation
3. Check: does the element snap back to its original position?

If it does, the handler is reading stale state.

**Fix pattern:** Use `useRef` to hold always-current state. Read from
`ref.current` in the handler instead of the closure-captured dependency.

```javascript
// Instead of:
const onDrag = useCallback((e) => {
  const block = blocks.find(b => b.id === id); // stale!
}, [blocks]);

// Use:
const blocksRef = useRef(blocks);
blocksRef.current = blocks;
const onDrag = useCallback((e) => {
  const block = blocksRef.current.find(b => b.id === id); // always fresh
}, []);
```

---

## 3. Rich Text Caret Miscomputation

**Pattern:** Arrow key navigation in `contentEditable` checks
`range.endOffset === endContainer.textContent.length` to decide "am I at the
end of the cell?" This works for plain text but fails when content contains
nested nodes (`<b>`, `<i>`, `<mark>`). The offset is relative to the immediate
parent node, not the full cell content.

**Why invisible:** Works perfectly with plain text. Fails only after the user
applies formatting (bold, italic, highlight).

**How to test:**
1. Enter text in an editable field
2. Apply bold/italic to part of the text
3. Use arrow keys to navigate to the end of the formatted section
4. Press ArrowRight/ArrowDown — does the cursor move to the next field/cell?

**Fix pattern:** Compute absolute caret position using a pre-caret range:

```javascript
function getAbsoluteCaretOffset(cell) {
  const sel = window.getSelection();
  if (!sel.rangeCount) return 0;
  const preRange = document.createRange();
  preRange.selectNodeContents(cell);
  preRange.setEnd(sel.getRangeAt(0).endContainer, sel.getRangeAt(0).endOffset);
  return preRange.toString().length;
}
// Compare against cell.textContent.length for "at end" check
```

---

## 4. Transform-Induced Scaling

**Pattern:** A parent container uses `transform: scale(zoomLevel)` for zoom.
Descendant elements (tooltips, dropdowns, context menus) inherit the transform,
appearing at wrong sizes. A 12px dropdown becomes 30px at 2.5x zoom.

**Why invisible:** Correct at 1x zoom. Breaks at other zoom levels.

**How to test:**
1. Set the app's zoom to a non-1x value (e.g., 0.5x, 2x)
2. Open a dropdown, tooltip, or context menu inside the scaled container
3. Check: is it the correct size relative to the viewport?

**Fix pattern:** Render overlays via portal to `document.body`. Use
`getBoundingClientRect()` for positioning (returns viewport coordinates,
unaffected by ancestor transforms).

---

## 5. Z-Index Stacking Context Collapse

**Pattern:** Multiple overlays (tour overlay, modal, context menu, tooltip) use
high z-index values without a coordinated scale. Element A is at z=10000,
element B is at z=99999, and element C has no z-index and is invisible behind A.

**Why invisible:** Works when only one overlay is active. Breaks when 2+ overlays
coexist — a combinatorial state that's rarely tested.

**How to test:**
1. Open the first overlay (e.g., a tour/onboarding)
2. While it's active, trigger a second overlay (context menu, modal)
3. Check: which is on top? Can you interact with both?
4. Check: are non-overlay elements (sidebar, toolbar) still accessible?

**Fix pattern:** Establish a z-index scale:

```
base content:     1-99
fixed headers:    100-199
dropdowns/menus:  200-299
modals:           300-399
overlays/tour:    400-499
system alerts:    500+
```

Document and enforce across the codebase.

---

## 6. State Persistence Scope Mismatch

**Pattern:** State stored in `useState` inside a component that unmounts and
remounts on navigation (e.g., route change, tab switch). The state is lost
because it's scoped to the component instance, not the session.

**Why invisible:** Works within a single page session. Breaks on navigation.

**How to test:**
1. Set a preference or save data in a component
2. Navigate to a different page/route
3. Navigate back
4. Check: is the data still there?

**Fix pattern:** Identify persistence scope:
- Same session, same tab → `useState` lifted to a context provider
- Across page reloads → `localStorage` or `sessionStorage`
- Across devices → Server-side persistence (database)

---

## 7. Layout Collision on Insert

**Pattern:** New elements are inserted at default coordinates without scanning
for existing elements. Two elements end up at the same position, stacked on top
of each other.

**Why invisible:** Works when the canvas is empty. Breaks when elements already
occupy the default insertion point.

**How to test:**
1. Add several elements to fill the default insertion area
2. Add one more element
3. Check: does it land on top of an existing element, or find an open spot?

**Fix pattern:** Before inserting, scan existing elements. Walk in grid
increments until finding a non-colliding position. Fall back to a new
row/column if the current area is full.

---

## 8. Positional Mismatch (Declared vs Intrinsic Size)

**Pattern:** Controls are positioned relative to a container's declared size
(e.g., `height: 280px` from a prop), but the content inside is smaller (e.g.,
80px table). Controls float far from the actual content.

**Why invisible:** Works when content fills the declared size. Breaks when
content is smaller or larger than declared.

**How to test:**
1. Create a small piece of content inside a larger container
2. Check: are controls (buttons, handles) near the content or near the
   container edges?

**Fix pattern:** Position controls relative to the content element (with
`position: relative` on a shrink-to-fit wrapper), not the outer container.

---

## 9. Synchronization Lag Between State and Display

**Pattern:** State updates but derived/computed UI doesn't re-render, or
re-renders with a visible delay. User sees stale labels, captions, or
indicators after an action.

**Why invisible:** Works in synchronous scenarios. Breaks with React 18's
automatic batching, async handlers, or when multiple state setters fire in
different microtasks.

**How to test:**
1. Change a setting that affects a label or indicator elsewhere on the page
2. Immediately check the dependent display — is it updated?
3. Check across component boundaries (sidebar label → canvas display)

**Fix pattern:** Ensure single source of truth. Derive labels from state
directly (computed, not cached). If using async updates, add explicit
re-render triggers or key changes.

---

## 10. Event Capture vs Bubble Mismatch

**Pattern:** A parent component uses `onPaste` (bubble phase) to intercept
paste events. But a child `contentEditable` element handles paste in the
capture phase, preventing the event from bubbling to the parent.

**Why invisible:** Works with `<input>` elements (no capture-phase handling).
Breaks with `contentEditable` which has its own paste handling.

**How to test:**
1. Paste tabular data (TSV/CSV from a spreadsheet) into a contentEditable
   field inside a custom paste handler
2. Check: did the parent's paste handler intercept it?
3. Check: did the contentEditable's native paste run instead?

**Fix pattern:** Use `onPasteCapture` (capture phase) on the parent if you need
to intercept before contentEditable processes it. Or attach the listener with
`addEventListener('paste', handler, { capture: true })`.

---

## Quick Reference: Detection Priority

| If the app has... | Check these categories first |
|-------------------|-----------------------------|
| Drag and drop | Stale closure, containment overflow |
| contentEditable / rich text | Caret miscomputation, event capture |
| Zoom / scale transform | Transform scaling, positional mismatch |
| Multiple overlays (modals, tours) | Z-index stacking |
| Route-based navigation | State persistence scope |
| Grid/canvas with insertable items | Layout collision |
| Derived labels / computed displays | Sync lag |
