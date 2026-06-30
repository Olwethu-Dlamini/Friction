# Friction — Guided Walkthrough Design

**Date:** 2026-06-30
**Status:** Approved (Approach A + Settings replay)
**Scope:** Add an interactive in-app guided walkthrough to the existing single-file PWA (`index.html`). No other features, polish, or refactors.

## Goal

Teach a first-time user the 4-gate intervention flow *before* a real urge hits, by narrating them through the real gate screens in a safe "tour mode." The tour auto-runs once after onboarding and can be replayed from Settings.

## Approach (chosen: A)

A small step-driven overlay tour engine reuses the app's **actual** gate screens. A global `tourMode` flag makes the gates non-trapping. A coach-mark panel narrates each step.

Rejected alternatives:
- **B — Cloned demo screens:** doubles markup; drifts out of sync with real gates.
- **C — Tour library (Shepherd/Intro.js):** external dependency; breaks single-file offline-first PWA.

## Components

### 1. Tour data — `TOUR_STEPS`
An ordered array. Each step:
```js
{ screen: 'screen-gate1', target: '#time-slider', title: '...', body: '...' }
```
`target` may be `null` (panel shown without a highlight ring).

The six steps:
| # | screen | target | message theme |
|---|--------|--------|---------------|
| 1 | screen-home   | `.hero-button`      | This is your emergency brake — tap when an urge hits. |
| 2 | screen-gate1  | `#time-slider`      | Reality Check: quantify the time an urge really costs. |
| 3 | screen-gate2  | `#stroop-options`   | Sensory Override: tap the COLOR, not the word; buttons shuffle. |
| 4 | screen-gate3  | `#matrix-grid`      | Decay Matrix: memorize the pattern — it fades, like the urge. |
| 5 | screen-gate4  | `#hold-area`        | Physical Grounding: breathe with the circle, hold your thumb. |
| 6 | screen-home   | `null`              | That's the loop. No streaks, no shame. You're ready. |

### 2. Tour controller
- State: `tourMode` (bool), `tourStepIndex` (int).
- `startTour()` — sets `tourMode = true`, `tourStepIndex = 0`, renders overlay, shows step 0.
- `tourNext()` / `tourBack()` — clamp index, re-render. Past last step → `endTour()`.
- `endTour(completed)` — `tourMode = false`, hide overlay, restore nav, `goToScreen('screen-home')`, persist `tour_completed = true` in the vault. Called by both finishing and Skip.
- `renderTourStep()` — `goToScreen(step.screen)` (guarded by tourMode), then position the coach-mark and highlight ring on `step.target`.

### 3. Overlay DOM
A single fixed-position layer (`#tour-overlay`, `z-index` above bottom-nav, below nothing else):
- **Backdrop:** dim, semi-transparent, blocks interaction with the app behind.
- **Highlight ring:** a positioned box drawn around the target element's bounding rect (via `getBoundingClientRect()`), using outline/box-shadow cutout. Recomputed each step; hidden when `target` is null.
- **Coach-mark panel:** bottom sheet containing — step counter "Step N of 6", progress dots, title, body, and controls: `Skip` (left), `Back` + `Next` (right). `Back` hidden/disabled on step 1; `Next` reads "Finish" on the last step.

### 4. Tour-mode safety guards
Added as early-return / branch checks inside existing functions so gates don't trap a touring user:
- `startBrownNoise()` — no-op while `tourMode` (tour is not a real intervention).
- `checkStroop()` — while `tourMode`, wrong answers do **not** reset the round and do not surface the escape hatch; the round simply re-renders. Navigation is driven by the overlay's Next, not by completing 5 rounds.
- `startMatrixRound()` / `checkMatrixCell()` — while `tourMode`, the grid does **not** escalate size and a wrong cell does **not** surface the escape hatch.
- `initGrounding()` — while `tourMode`, the hold timer does **not** call `completeIntervention()` (no auto-advance, no ledger write); the breathing animation still plays for demonstration.
- `completeIntervention()` — never reached during a tour (overlay drives navigation), so no ledger entry is written.

The guiding rule: **in tour mode the overlay's Next is the only thing that advances screens.** Gate interactions remain visible and lightly tappable for feel, but cannot block, reset, or complete the flow.

### 5. Entry points
- **Auto after onboarding:** `completeOnboarding()` — after hiding the onboarding layer, check the vault `tour_completed` flag; if absent/false, call `startTour()`.
- **Settings replay:** a new settings item "Replay walkthrough" with a button/row that calls `startTour()` directly (does not depend on the flag).

### 6. Persistence
- New vault key `tour_completed` (boolean), stored via existing `saveToVault` / `getFromVault`. Set true on `endTour()` (whether finished or skipped).
- Cleared by the existing `clearAllData()` (it clears the whole store), so a reset user sees the tour again — acceptable and consistent.

## Data flow
```
onboarding "I Understand"
  -> completeOnboarding(): set onboarded; if !tour_completed -> startTour()
startTour -> tourMode=true, index=0 -> renderTourStep()
renderTourStep -> goToScreen(step.screen) -> position ring + panel
Next/Back -> renderTourStep() ; Next past end -> endTour(true)
Skip -> endTour(false)
endTour -> tourMode=false, persist tour_completed=true, hide overlay, goToScreen home
Settings "Replay walkthrough" -> startTour()
```

## Error handling / edge cases
- **Target not found / off-screen:** if `getBoundingClientRect()` yields a zero/out-of-view rect, hide the ring and just show the panel (panel never depends on the ring).
- **Resize / orientation change:** re-position ring + panel on `resize` while `tourMode` is active; remove the listener on `endTour`.
- **goToScreen side effects:** entering a gate normally runs `initStroop`/`initMatrix`/`initGrounding`. These still run (so visuals appear) but are neutralized by the tour-mode guards above.
- **Bottom nav:** existing `goToScreen` hides the nav on gate screens. The tour overlay sits above the app regardless; on step 6 (home) the nav reappears normally after `endTour`.
- **Double-start:** `startTour()` is idempotent — if already in tour mode, reset to index 0 rather than stacking overlays.

## Accessibility
- Overlay panel uses `role="dialog"` `aria-modal="true"`; title via `aria-labelledby`.
- Controls are real `<button>`s, keyboard-focusable; `Next`/`Back`/`Skip` reachable by Tab; `Esc` triggers Skip.
- Move focus to the panel on each step; `aria-live` on the body text so step changes are announced.
- Respect `prefers-reduced-motion` for ring/panel transitions (reuse existing CSS conventions).

## Testing (manual, hackathon-appropriate)
1. Fresh state (clear data) → reload → onboarding → "I Understand" → tour auto-starts at step 1.
2. Next through all 6 steps; confirm each lands on the right screen with ring on the right element.
3. On gate 2/3, tap wrong answers → confirm no reset, no escape hatch, no advance.
4. On gate 4, hold → confirm breathing animates but no success/ledger entry.
5. Skip mid-tour → returns home, nav restored, no ledger entry.
6. Reload → tour does NOT auto-run again (`tour_completed` set).
7. Settings → "Replay walkthrough" → tour runs again.
8. Confirm a real `Create Friction` run still resets/escalates/completes normally (guards only fire in tour mode).
9. Rotate device mid-step → ring/panel re-position.

## Out of scope
Visual polish, new gates, bug fixes unrelated to the tour, copy rewrites beyond the 6 step messages.

## Files touched
- `index.html` only (markup for overlay + Settings item, CSS for overlay, JS for tour engine and guards).
