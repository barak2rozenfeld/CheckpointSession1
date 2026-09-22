# Session 2 — Interactive Design (mirror of Session 1)

> How to build `session2/index.html` so it feels like a sibling of Session 1's `index.html`, one interactive, self-contained HTML deck, same visual language, adapted to **English / LTR** and a **customer (CISO)** audience.

---

## 1. Same skeleton as Session 1

Reuse the exact scaffolding from the root `index.html`:

- **Single self-contained file:** one `<style>` block + one `<script>` block, no build step, no dependencies except Google Fonts. Deployable to Vercel like Session 1.
- **Fixed stage, scaled to fit:** a `1280×720` `.stage` scaled to any viewport.

```
(function(){const st=document.getElementById('stage');
 function fit(){const s=Math.min(innerWidth/1280,innerHeight/720);
  st.style.transform=`translate(-50%,-50%) scale(${s})`;}
 addEventListener('resize',fit);fit();})();
```

- **Slides:** each `<section class="slide">` is absolutely positioned; `.active` fades/slides it in.
- **Navigation:** `go(i)` is the single source of truth. Keyboard (arrows, space, Home/End), prev/next buttons, clickable dots, a progress bar, and a `N / total` counter. Reuse Session 1's `go()` verbatim.
- **Story rail:** auto-generated from a chapter list `CH` and each slide's `data-ch`. Rebrand chapters for the customer story (see file 02).

## 2. Chrome (per slide)

Same components as Session 1, top to bottom:

- `.topbar` -> `.brand` (Check Point logo + title) + `.tags` (2-3 colored pills).
- `.rail` (story progress).
- `.body` (the content; starts with `.eyebrow`, then the big `.title` with an `.hl` highlight span).
- `.thread` at the bottom (a badge + a one-line punchline).
- `.src.box` block for data slides (label always "מקורות והנחות" per the presentation-style rule; sources separated by ` · `, then `<b>הנחות:</b>` then the assumptions).

### Sourcing rule (mandatory, every field)

Every slide that shows a number, claim, or comparison **must carry both**, in the same `.src.box`:
1. **At least one source** (datasheet, report, or official page), and
2. **At least one assumption** (the estimate, formula, normalization, or basis of calculation behind the number).

- No number without a source. No number without a stated assumption.
- Format: `sources separated by ' · '`, then `<b>הנחות:</b>` (or "Assumptions:" if localized), then the assumptions.
- Hero / pure-narrative slides with no data carry no block.
- After editing the block, verify the `.body` still fits: `scrollHeight === clientHeight`.
- `.tristripe` + `.footer` (Check Point Software Technologies · Confidential | Barak Rozenfeld · Senior PM Assignment 2026).

## 3. Design tokens (reuse Session 1 `:root`)

- Colors: `--primary:#e40c5b` (Check Point pink), `--purple`, `--dark`, plus competitor colors `--ft` (Fortinet), `--pa` (Palo Alto), `--cs` (Cisco).
- Fonts: `Heebo` (body) + `Barlow Semi Condensed` (numbers, `.num`).
- Helpers: `.en` and `.num` isolate LTR runs; `.hl` for the highlight in titles.

## 4. What changes for Session 2 (English / LTR)

- `<html lang="en" dir="ltr">` (Session 1 is `he` / `rtl`). This flips paddings, `inset-inline`, rail underline, packet animation direction, etc. Audit every `inset-inline-*` and any hard-coded left/right.
- Text is English; the `.en` / `.num` LTR-isolation helpers are mostly unneeded but harmless.
- Keep the `.src.box` label in Hebrew per the style rule, or localize to "Sources & assumptions" (decision below in Open items). The rule was written for Session 1; confirm with Barak for a customer-facing English deck.
- Packet / flow animations move left-to-right (internet -> org) instead of Session 1's right-to-left.

## 5. Interactive components to build (reuse Session 1 patterns)

Session 1 already implements these; port and re-skin them:

| Component | Session 1 source | Session 2 use |
|---|---|---|
| Segmented toggle + live throughput bar | `setCfg()` / `#cfgseg` (Firewall -> NGFW -> TP -> TP+TLS) | Slide 3 dilemma: "turn on TLS -> watch throughput drop" |
| Packet animation along a wire | `spawn()` Web Animations API | Slide 3 / 7: traffic hitting the gateway |
| Chassis before/after + gauges | `.box9800` / `.gauges2` (slide 8 of S1) | Slide 7: empty slot -> DPU card, 10 -> 40 gauge |
| Bar/positioning chart | `CMP1` / `CMP2`, plane/quadrant | Slide 9 proof: interactive TCO chart |
| Donuts | `[data-donut]` JSON | market/share visuals if needed |
| Roadmap timeline | (new, simple) | Slide 8 twist: today -> post-quantum -> on-box AI |

## 6. Code hygiene (from the workspace rules)

- One `<script>` block; every per-slide IIFE that touches a slide element **must** start with a guard: `if(!el) return;`. An uncaught error in one IIFE kills every IIFE after it.
- Delete IIFEs for any removed slide; never leave an orphan.
- Verify with a screenshot (not the a11y snapshot), navigate via `go(N)` (0-indexed) through CDP.
- Cache-bust every preview: `index.html?v=<unique>`.
- The truth is on disk: verify with `Select-String` if Read/Grep look stale.

## Open items

- [ ] Localize `.src.box` label to English, or keep Hebrew per the style rule? (customer-facing English deck argues for English)
- [ ] Confirm chapter names for the LTR story rail.
- [ ] Confirm logo asset path (`checkpoint-logo.PNG`) is reused.
