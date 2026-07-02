# Friction

> **Addiction is a frictionless slide. Friction is the handrail.**

Friction is a zero-barrier, offline-first **Progressive Web App** that acts as an emergency brake for compulsive urges. Instead of counting "days clean," it deliberately re-introduces *resistance* between impulse and action — the exact thing that modern habit-forming apps are engineered to remove.

Built for the **Youth xAI Hackathon**.

🔗 **Live app:** https://olwethu-dlamini.github.io/Friction/

---

## The Problem

Everything on your phone is designed to be *frictionless*: one-tap buying, autoplay, infinite scroll. That same frictionlessness is what makes compulsive behaviour so hard to interrupt — there is almost nothing standing between the impulse and the act.

Traditional recovery apps respond with **gamified streak counters**. But behavioural psychology exposes a dark side to streaks: when a user breaks a 47-day streak, the shame is intense. This **shame spiral** often triggers a *heavier* relapse and causes the user to abandon the app entirely. The tool meant to help becomes a source of trauma.

## The Solution

Friction throws that model out. It doesn't track the addiction — it **interrupts the impulse**.

When an urge hits, the user taps a single **"Create Friction"** button and enters an adaptive **4-Gate intervention pipeline**. Each gate buys time and forces cognitive focus, letting the wave of craving crest and break. There are **no streaks and no scores** — only a **Resilience Ledger** that counts *interventions survived*, rewarding effort over perfection.

Everything runs **client-side**: no accounts, no servers, no cloud sync. Your data never leaves your device.

---

## Features

- **⛔ One-tap emergency brake** — a single *Create Friction* button on the home screen starts an intervention the instant an urge hits.
- **🚪 Adaptive 4-Gate pipeline** — escalating cognitive and physical tasks that add resistance and let the craving pass.
- **🪂 Automated Escape Hatch** — if a user is too panicked to complete a gate, the app bypasses the hard tasks and guides them straight to breathing.
- **📓 Resilience Ledger** — tracks *interventions survived* instead of days clean. No streaks to break, no shame to spiral into.
- **💬 Future Self Vault** — after each intervention, users can leave a note for the next urge; a past note is surfaced on the home screen when it's needed most.
- **🎧 Brown-noise ambient sound** — an optional calming soundscape synthesised live with the Web Audio API (no audio files to download).
- **📳 Haptic feedback** — subtle vibration on key interactions (where supported).
- **🧭 Guided walkthrough** — an in-app tour teaches the whole flow *before* a real urge hits, and can be replayed any time from Settings.
- **📴 Fully offline & installable** — a real PWA with a service worker and manifest; works with no connection and installs to the home screen.

---

## The 4-Gate Intervention

When *Create Friction* is tapped, the user moves through four gates. Each one is designed around a specific behavioural principle.

| # | Gate | What happens | Why it works |
|---|------|--------------|--------------|
| **1** | **The Reality Check** | A tactile slider quantifies the *time theft* — how many hours the urge will really cost (the act, the recovery, the regret). | Reframes a "5 minutes of relief" impulse as a concrete trade against hours of the user's day. |
| **2** | **Sensory Override** | A Stroop-effect game: tap the **colour** of the word, not the word itself. Button positions **reshuffle every round** (5 rounds). | Shatters muscle memory and forces deliberate cognitive focus, pulling attention off the craving. |
| **3** | **The Decay Matrix** | A memory grid: memorise a pattern, then reproduce it as it fades. Escalates over 3 rounds. | The fading blocks mirror the urge itself — a felt demonstration that cravings decay if you hold on. |
| **4** | **Physical Grounding** | Press and hold your thumb on the screen and breathe with an animated circle for 3 full cycles. | Grounds the body, slows breathing, and physically anchors the user until the wave has broken. |

If the cognitive load is too high at gate 2 or 3, the **Escape Hatch** appears and offers a direct *Skip to Breathing* path to gate 4 — the goal is always to survive the moment, never to "win" the game.

On completion, the user reaches a calm **"The wave has broken"** screen, records an optional note to their future self, and the intervention is added to the Resilience Ledger.
