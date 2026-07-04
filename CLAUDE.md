# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Single-file interactive web page (`sorpresa-para-ti.html`, Spanish) — a "surprise" gift experience for a romantic partner. No build tooling, no package manager, no dependencies beyond a Google Fonts CDN link. Open the file directly in a browser to run it.

## Commands

- Run: open `sorpresa-para-ti.html` in any browser (double-click, or `start sorpresa-para-ti.html` on Windows).
- No build, lint, or test tooling exists in this repo.

## Architecture

Everything lives in one HTML file: `<style>` block, then markup, then a single `<script>` block at the bottom. No modules, no frameworks.

**Flow model**: the page is a linear 5-step wizard. Each step is a `<section class="paso" id="p1">`..`id="p5"`; only the section with class `activo` is displayed (`display:flex`), others `display:none`. Progress dots (`#dots`) mirror the current step. Navigation between steps goes through the single `irA(n)` function in the script, which toggles `.activo` and updates dots — there is no routing/history, no state beyond `let actual`.

**Step responsibilities** (each wired independently in the script, no shared component abstraction):
1. `p1` — clickable envelope (`#sobre`) that animates open via CSS class toggle, then auto-advances via `setTimeout`.
2. `p2` — 3 flip cards (CSS 3D `rotateY` flip via `.girada` class); advancing to `p3` is gated on all 3 cards being flipped (`giradas` counter).
3. `p3` — "¿me quieres?" Sí/No prompt where the No button (`#btnNo`) evades the pointer (`huir()` repositions it randomly within `#zona` on hover/touch/click), cycling through an escalating array of taunt phrases (`frases`).
4. `p4` — HTML canvas scratch-card (`#lienzo`). Reveal is implemented by drawing an opaque gradient over a prize `<div>`, then erasing with `globalCompositeOperation='destination-out'` as the user drags; `chequear()` samples pixel alpha data to detect when >45% is scratched, then removes the canvas.
5. `p5` — final screen with static "coupon" list and a heart-explosion button.

**Shared effects**: `explosion(x,y,cant)` spawns short-lived `.boom` divs animated outward via CSS custom properties (`--dx`/`--dy`) set inline per-particle, used at multiple trigger points (envelope open, "Sí" click, scratch reveal, final button). Ambient floating background hearts are generated once on load into `#amb` with randomized CSS animation timing per span.

**Editing content**: text/copy for each step (letter messages, prize text, coupons, taunt phrases) lives inline in the HTML/JS for that step's section — no data file or i18n layer to update separately.
