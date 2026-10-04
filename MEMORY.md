# Portfolio — Decision Record

Single-page capability portfolio for **Muthanandham Gnanamuthuraj**, DevOps Engineer.
Everything ships as one file: `index.html` (~1,790 lines, ~100 KB, no build step, no
dependencies beyond two Google Fonts).

This document records *why* the page is the way it is — the brief, the decisions, the
bugs found along the way, and the measured constraints that future edits must respect.
If you change something here and it looks wrong, the reason is probably below.

---

## 1. The brief

Not a résumé. Not an employment timeline. A **product landing page for a capability** —
it argues what the person can build, not where they have worked.

> **From code to cloud. Built to run.**

The governing idea: *the medium is the credential*. The visitor does not read that the
author understands distributed systems — they scroll through one, watch it assemble,
watch it break, and watch it heal. A DevOps portfolio that loads slowly or breaks on a
phone argues against its own author, so page craft is part of the argument.

Source material: the author's own résumé. **Every tool, domain and claim on the page is
real.** No invented metrics, no fabricated project names, no numbers that cannot be
substantiated.

---

## 2. Architecture decisions

### ADR-001 — Portfolio as product page, not résumé
**Context.** Most DevOps portfolios are a skills grid plus an employment list. They read
as a CV in CSS and are indistinguishable from one another.
**Decision.** Structure the page as a product launch: a problem, a set of capabilities, a
demonstration, a proof of judgment, an invitation.
**Consequence.** No experience section, no employer timeline, no dates. Domain context is
carried by Act 08 ("Where it ran") instead.
**Known cost.** A visitor cannot tell seniority or availability from the page. This was
raised explicitly and accepted. If it ever needs closing, one line in Act 08 or the
footer does it without reintroducing a timeline.

### ADR-002 — One signature visual, transformed; never per-section animation
**Context.** Unrelated effects per section read as a template with decoration bolted on.
**Decision.** A single fixed full-viewport canvas behind all content, driven by one
scroll value `0 → 1`. Sections are *keyframes of one system*, not separate scenes.
**Chosen direction.** Hybrid: an artifact travels code → cloud (Acts 1–4), then the
topology grows around it (Acts 7–9), with a thin operator HUD as persistent chrome.
Rejected: pure "living infrastructure" (no narrative arc), pure "precision product"
restraint (forgettable), pure "cinematic launch" (least technical proof).

### ADR-003 — Monochrome base, semantic-only colour
**Context.** Candidate palettes were electric cyan, terminal amber, signal green and
monochrome. Green was rejected for a structural reason: green is already the *healthy*
state, so using it as the brand accent means the recovery moment in Act 05 lands as a
shrug. Cyan was the safe pick.
**Decision.** The interface has **no colour of its own**. Colour appears only when the
*system* has something to say.

```
grey   → structure, chrome, everything built
white  → attention, the human voice
green  → healthy
amber  → degraded
red    → incident
```

**Consequence.** The red in Act 04 (pipeline halt) and Act 05 (incident) is the first
saturated colour on ~20 screens of scroll, and it genuinely jolts. This is the whole
payoff of the palette choice — do not spend red anywhere else.
**Known cost.** Without hue, highlight states are harder. See ADR-013.

### ADR-004 — One HTML file, Canvas 2D, zero framework
**Decision.** Inline CSS and JS. Canvas 2D for the node system (not WebGL). Native
`position: sticky` for pinned sections. `IntersectionObserver` for reveals. Google Fonts
is the only external request.
**Rationale.** Portability, and the performance budget is itself part of the argument.

### ADR-005 — Nine acts
`00 Boot · 01 Hero · 02 The Gap · 03 Capabilities · 04 Delivery · 05 Incident ·
06 How I Operate · 07 Toolkit · 08 Where It Ran · 09 Resolution`

Mapped to what a visitor asks, in order: who → why → what → how → how under pressure →
principles → tools → context → contact. Each act is deep-linkable by id.

### ADR-006 — Capabilities are verbs, not skill nouns
Six statements, each a thing made true about a system:
*I make infrastructure reproducible · I make releases boring · I make systems observable ·
I make security part of the pipeline · I make things scale and stay up · I find out why it
broke.*
Each answers a failure mode named in Act 02 — which is why those two acts are adjacent.
"I make releases boring" is the line doing brand work.

### ADR-007 — Pinned sequences, never scroll hijacking
Act 04 uses a `520vh` track with a `100svh` sticky child. Scroll position drives stage
index and log reveal. Native scrolling is preserved throughout; the pin always releases.

### ADR-008 — The incident act is the differentiator
Six clicks: alert → `kubectl describe` → `OOMKilled` → correlate to the deploy →
roll back → fix in code, add the alarm, update the runbook.
**Why it exists.** Anyone can claim they ship. Root-cause work under time pressure is the
other half of the job and is invisible on every other portfolio. The final step (fix,
detect, document) is the point of the entire act — an incident is not over when the graph
goes green.

### ADR-009 — Tools are answers to questions
Act 07 is not a badge wall. Selecting a capability lights only its tools; hovering a tool
gives its actual job plus a reverse lookup of which capabilities it serves. 60 tools
indexed, all from the résumé.

### ADR-010 — Deliberate exclusions
- **No invented metrics.** Nothing quantified that cannot be substantiated.
- **No employment timeline.** See ADR-001.
- **No résumé download.** Removed on request; the PDF was deleted from the folder.
- **No analytics.** Would be checked twice and then ignored.
- **No blog.** An unmaintained blog reads worse than no blog.

### ADR-011 — Command palette: added, then removed
A `⌘K` palette was built (navigation, `whoami`, `deploy`, `status`, copy-contact) and
later removed in full — markup, ~55 lines of CSS, a 137-line JS module, the HUD button,
the hero CTA and the footer entry. The hero's second CTA became **"get in touch →"**.
The toast helper lived inside that module and was extracted as a standalone function;
the motion toggle still uses it. `body.locked` and `.btn .k` were removed as dead rules.
*If it is ever reinstated, note it also needs a focus trap — the original had none.*

### ADR-012 — Canvas legibility: three mechanisms, not one dial
This went through three rounds. The final design uses layered protection so the
constellation can stay bright **and** the text stay clean:

1. **Per-act opacity.** Each `<section class="act">` carries its own `data-canvas`
   value, applied to `--canvas-op` by the scroll handler.
   `hero 1 · gap 0.72 · capabilities 0.46 · delivery 0.48 · incident 0.44 ·
   operate 0.44 · toolkit 0.42 · domains 0.5 · contact 0.92`
   Mobile multiplies by `0.55`.
2. **A left-weighted scrim** (`.scrim`) between canvas and content — 98% opaque over the
   text column, clearing to transparent at 100% so the constellation breathes on the right.
3. **A text halo** — `text-shadow: 0 0 16px rgba(8,9,10,.96)` on body copy that sits
   further right than the scrim protects. This is what allows Act 02 to run at 0.72.

**Do not fix a legibility problem by lowering opacity alone.** That was tried; it made
the animation invisible and had to be undone.

### ADR-013 — Highlight by inverting, never by dimming others
**Context.** First attempt lit matching tools with a white outline — too small a step
from grey-on-dark. Second attempt added `opacity: .24` to non-matching tools, which made
them unreadable. Feedback: *"normally everything should be visible."*
**Decision.** Lit tools **invert** — solid white fill, near-black text. Nothing is ever
dimmed out of legibility. Base tool colour was also raised from `--ink-ghost` (1.85:1,
fails WCAG) to `--ink-dim` (6.07:1, passes). A clicked tool is filled *and* ringed so it
stays distinct from merely matched.
**Principle.** The toolkit is content, not chrome. Emphasis is added to the selection,
never subtracted from everything else.

### ADR-014 — Progressive enhancement for reveals
`.rv` elements are **visible by default**. An inline script at the top of `<body>` adds
`class="js"`, which opts into the hidden-then-revealed state. A 2.6s timer force-reveals
anything in viewport as a safety net. Same pattern guards the boot overlay
(`display:none` until `body.js`).
**Why.** Before this, a JS failure rendered a completely blank page.

### ADR-015 — Time-based canvas easing
Node settling uses `1 - Math.exp(-dt / 190)` with `dt` clamped to 80ms, not a fixed
per-frame lerp. Identical settling speed at 30, 60 or 120 fps. The first layout snaps
into place so nodes never fly in from the origin.

### ADR-016 — Name as a two-line wordmark
`MUTHANANDHAM` (12 chars) / `GNANAMUTHURAJ.` (13 chars) stack as two near-equal lines —
a far better composition than the previous lopsided `MUTHANANDHAM` / `G.`
The HUD collapses the surname to `G` below 820px so it does not crowd the status readout.

### ADR-017 — Eyebrow is a two-beat line
```
01   DEVOPS ENGINEER — I BUILD THE PART NOBODY SEES     (desktop)
01   I BUILD THE PART NOBODY SEES                        (≤640px)
```
The job title stays for scanners; the human half is what makes someone scroll. Rejected:
stack lists (`AWS · KUBERNETES · IOT`), which made Act 01 the only off-pattern label on a
page where every other act has a short one.

### ADR-018 — Title written for the search result, not the tab
```html
<title>Muthanandham Gnanamuthuraj — I build the part nobody sees</title>
```
A tab fits ~176px; the full name alone measures 170px, so **everything after it is cut
regardless of what it says**. Nobody finds a personal portfolio by searching "DevOps
Engineer", so the keyword does no work there. The trailing half is written to be
*clickable in a SERP* (all 57 chars show in Google). `og:title` keeps the tagline.

### ADR-019 — Favicon sized for 16px
A rounded dark tile with a solid white dot; the outer ring only resolves from 32px up.
The previous icon used a `1.5` stroke on a `32` viewBox — **0.75px at tab size**, below a
device pixel, so it rendered as an empty square. Measured at true 16×16: lit pixels went
from **16/256 to 32/256**, with a pure-white centre.

---

## 3. Defects found and fixed

| # | Defect | Impact | Fix |
|---|---|---|---|
| 1 | `.rv` hidden by default | **Blank page** if JS failed | Progressive enhancement (ADR-014) |
| 2 | Fixed per-frame lerp | Frame-rate dependent settling | Time-based easing (ADR-015) |
| 3 | Mobile pinned pipeline 923px inside an 812px `overflow:hidden` pin | Console silently clipped | Compressed sub-1040px treatment → 592px |
| 4 | All closing-act CSS targeted `#close`; the section id is `contact` | **Entire act unstyled** — headline at browser-default 24px instead of 172px, `.dim` inline and white | Selectors corrected to `#contact` |
| 5 | `.close-in { max-width: 26ch }` | `ch` resolved against the 16px parent, not the 172px headline → statement clipped to a ~230px column | Removed; `.say` carries the constraint |
| 6 | Narrow-viewport block placed mid-stylesheet | `.contact`'s later base rule won the cascade; third grid column collapsed to `0px` | Moved to the end of the cascade |
| 7 | Pipeline log scroll assumed a 22px line height | Drift from actual rendering | Measured from `offsetHeight` |
| 8 | Incident step 4 showed `alert=1` while its own log said "alert cleared" | Self-contradictory state | Status became `mitigated · open`, alert cleared |
| 9 | `.lit` persisted after clicking a tool | Selected tool *and* ten unrelated lit chips at once | Click clears the capability filter |
| 10 | Favicon invisible at 16px | Empty square in the tab | ADR-019 |
| 11 | `h1` 291px inside a 280px box at 320px | Glyphs bled into the gutter | Clamp floor lowered |

**Verified at 320 / 375 / 390 / 768 / 1440:** zero elements overflow their own box, zero
outside the viewport, no console errors.

---

## 4. Measured constraints — respect these when editing

| Thing | Measurement |
|---|---|
| Tab title budget | ~176px ≈ 26 chars at 12px system-ui |
| Full name in a tab | 170px — fits with 6px to spare |
| `GNANAMUTHURAJ.` width | **8.178px per font-px** — this line governs `h1` sizing, not `MUTHANANDHAM` |
| `h1` clamp | `clamp(28px, 10.4vw, 152px)` → 1225px in a 1281px column at 1440 |
| Contrast on `#08090A` | `--ink-ghost` **1.85:1** ✗ · `--ink-faint` **2.89:1** ✗ · `--ink-dim` **6.07:1** ✓ |
| Pipeline pin track | `520vh` desktop, `420vh` ≤1039px |
| Page length | ~20 viewport heights at 1440×900 |
| OG card | 1200×630 (1.91:1) |

> **Open accessibility defect.** `--ink-ghost` and `--ink-faint` still fail WCAG AA for
> normal text and are used widely for the HUD, act numbers and machine-voice lines. Only
> the toolkit was lifted (ADR-013). `--ink-dim` and above are fine.

---

## 5. A note on tooling, for whoever works on this next

Computed styles read immediately after a class change return the *pre-transition* value,
and a browser pane that is hidden or backgrounded freezes `requestAnimationFrame` —
which stalls the scroll handler and serves stale style data. Several hours were lost
chasing a "CSS bug" in the toolkit that did not exist. **When the DOM and
`getComputedStyle` disagree, trust a screenshot.**

---

## 6. Not done yet

| Item | State |
|---|---|
| **OG image** | Designed and rendered as 1200×630; two variants built (name-led, statement-led). **Not chosen, not wired.** Generator deleted during cleanup — rebuild from the spec below. |
| **JSON-LD** | `Person` schema drafted (name, jobTitle, email, url, sameAs, alumniOf, knowsAbout). Not added. |
| **Deployment** | Not hosted. The highest-leverage remaining item: ship it via GitHub Actions and inject the commit SHA into the footer, so the page is *evidence* that the pipeline in Act 04 is real rather than a claim. |
| **Evidence artifacts** | Every capability is still an assertion. Two or three real artifacts — a Terraform module, a Jenkinsfile, a Deployment manifest — in a small inline code viewer would be the strongest remaining content addition. |
| **Contrast pass** | See §4. |

**OG card spec (for rebuilding):** 1200×630, `#08090A` ground, deterministic constellation
at ~78% width behind a left-weighted scrim, `DEVOPS ENGINEER` eyebrow in JetBrains Mono
at 15px with 3.4px tracking, name in Space Grotesk 700 at ~104px, promise at 600/44px
(white + `#5A5F65`), footer rule with a green probe dot and
`6 capabilities · 60 tools · all probes healthy`. Render at 2× and downsample.
Both existing cards read "MUTHANANDHAM G." and must be regenerated with the full name.

---

## 7. Files

```
index.html             the entire site
MEMORY.md              this file
.claude/launch.json    dev-server config for the preview pane (tooling, not shipped)
```

Preview and generator artifacts (`palette-preview.html`, `og-generator.html`,
`favicon-preview.html`, `og-variant-*.png`) were removed after their decisions were
recorded here.
