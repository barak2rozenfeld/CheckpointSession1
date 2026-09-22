# Session 2 — Build Brief (kickoff for a new chat)

> Hand this to a fresh session. Goal: build the **Session 2** interactive deck. Read the three companion files first, then build.

---

## Objective

Build `session2/index.html`: a single, self-contained, interactive HTML deck for **Session 2** of Barak Rozenfeld's Check Point Senior PM assignment. It must feel like a sibling of Session 1 (`../index.html`).

## Context (read once)

- **Assignment:** Senior PM, Hardware Platforms, Security Gateway Appliances @ Check Point. Full context in `../research/00-context.md`.
- **Session 1** already exists: `../index.html` (Hebrew, internal Exec audience, competitive analysis + feature + business case + ask). The feature there is a **BlueField-3 DPU card** for the 9700/9800 (×4 TP+TLS in the same 1RU). Do not modify Session 1.
- **Session 2** (this deck) is the customer-facing counterpart.

## Audience, language, time

- **Audience:** Fortune 500 CISO (enterprise prospect, cross-industry).
- **Language:** English, LTR.
- **Time:** ~15 min main deck (10 slides) + appendix "ready if asked" slides.

## Requirements (research/00-context.md, line 44)

Session 2 must cover, in this order:
1. **9000 overview**
2. **differentiators**
3. **feature as benefit** (the DPU card, framed as the proof of the "future-proof" differentiator)
4. **CTA** (a 30-day POC / design-partner program, not "buy today", the card is a roadmap proposal)

Plus **the twist**: Capacity-on-Demand ("buy once, keeps growing"), the customer-facing translation of Session 1's "flexibility axis" (programmable DPU vs fixed ASIC).

## Companion files (read before building)

- `01-interactive-design.md` — how to build it like Session 1 (stage, `go(i)`, chrome, tokens, LTR changes, components to reuse, code hygiene).
- `02-presentation-plan.md` — the plan: requirements check, narrative arc, 10-slide main deck, A1-A7 appendix, timing.
- `03-slide-content.md` — per-slide English copy, punchlines, and source/assumption blocks.
- `../research/02-competitive-analysis.md` — pull real numbers (CyberRatings, TCO, per-1RU TP+TLS).
- `../research/01-market-overview.md` — market/threat context (encrypted-traffic stat).

## Hard constraints (do not violate)

- **No em-dash (—)** anywhere in slide text. Use a period, comma, or colon.
- **No "הבא:" / "next:" teaser** in the thread; end on the punchline.
- **Every data field needs BOTH a source and an assumption** in a single `.src.box` block. No number without a source; no number without a stated assumption. Hero / narrative slides carry no block.
- **Code hygiene:** one `<script>` block; every per-slide IIFE starts with `if(!el) return;`; no orphan IIFEs; verify by **screenshot** (not a11y snapshot); navigate via `go(N)` (0-indexed); **cache-bust** every preview with `?v=<unique>`.
- **The truth is on disk:** if Read/Grep look stale, verify with `Select-String`.

## Build approach

1. Copy the Session 1 scaffolding (stage fit, `go()`, dots, keyboard, rail, tags, tristripe, footer). Set `<html lang="en" dir="ltr">` and audit every `inset-inline-*` / left-right for LTR.
2. Build the 10 main slides per `03-slide-content.md`. Reuse Session 1 components (throughput toggle, packet animation, chassis before/after + gauges, comparison chart).
3. Build the A1-A7 appendix slides (reachable in one click, not counted in the 15 min).
4. Fill real numbers from `research/`; keep every data slide's source+assumption block accurate.
5. Verify: load `session2/index.html?v=<n>`, step through all slides via `go(N)`, screenshot each, confirm `.body` `scrollHeight === clientHeight` on data slides.

## Resolve first (open items)

- [ ] Localize the `.src.box` label to English ("Sources & assumptions") or keep Hebrew? (English deck argues for English.)
- [ ] Lock numbers: encrypted-traffic %, CyberRatings block rate, TCO figures, per-1RU TP+TLS.
- [ ] Confirm the 3 differentiators (draft: prevention / unified platform / future-proof).
- [ ] Confirm CTA framing (30-day POC + design-partner).

## Definition of done

- 10 main slides + A1-A7 appendix, English/LTR, in `session2/index.html`.
- Every data slide carries a source + an assumption; no em-dashes; punchline endings.
- Deck runs standalone (double-click), scales to viewport, keyboard + dots navigation work.
- All main slides screenshot-verified with content fitting the body.
