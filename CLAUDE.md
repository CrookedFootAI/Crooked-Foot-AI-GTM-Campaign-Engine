# Crooked Foot AI — GTM Campaign Engine
## Project Overview
Single-file static SPA. No build step, no package manager, no backend.
Entire app lives in `index.html`. Deployed to Netlify via GitHub Desktop push.
`logo.png` must ship alongside `index.html` — it is the favicon, nav logo, and hero image.

## Tech Stack
- **React 18.2.0** — loaded from CDN (cdnjs), not npm
- **Babel Standalone 7.23.5** — transpiles JSX in the browser at runtime
- **Inter** — Google Fonts, 400–900 weights
- **No bundler, no npm, no node_modules**

## File Structure
```
index.html   → entire app (HTML + CSS + JSX + all data)
logo.png     → brand asset (favicon + nav + hero). Never rename or move.
CLAUDE.md    → this file
```

## Code Style
- **Components:** PascalCase (`KPICard`, `SubTab`, `CampaignStudioSection`)
- **Static data:** SCREAMING_SNAKE_CASE (`COPY`, `CUSTOMERS`, `PAINS`, `PROOF_MAP`)
- **Functions/state:** camelCase (`getCopy`, `handleGenerate`, `setVertical`)
- **Colors:** always reference `S.cyan`, `S.text`, etc. — never hardcode hex in components
- **Styles:** inline React style objects only. CSS classes reserved for animations only.
- **Section markers:** use `/* ── SECTION NAME ── */` before each major block

## Brand Tokens
All colors live in `const S` (line 43). Edit there and nowhere else.
```
S.cyan       #0099CC   — primary accent
S.text       #0A0A1A   — body text
S.muted      #6B7280   — secondary text
S.surface    #F7F7F8   — card / panel backgrounds
S.border     #E5E7EB   — dividers and card borders
S.green      #059669   — positive / success states
S.amber      #D97706   — warning / medium priority
S.red        #DC2626   — negative / high priority
```

## Campaign Data
- `COPY` object holds explicit copy for 8 pre-written ICP × persona × trigger combos
- Key format: `"Vertical|Persona|Trigger"` (pipe-delimited string)
- `fallback(v, p, t)` synthesizes copy for all other combinations
- `getCopy(v, p, t)` is the single entry point — checks `COPY` first, falls back automatically

**To add a new campaign brief:**
```js
"SaaS / Technology|RevOps Director|Pipeline Visibility": {
  linkedInHeadline: "...",
  linkedInBody: "...",
  proof: "Used by: ...",
  cta: "...",
  emailSubject: "...",
  emailOpener: "...",
  landingHeadline: "...",
  landingSub: "...",
  landingCta: "...",
},
```

## Adding New Sections
1. Write `function [Name]Section()` with `id="[kebab-name]"` on the outer div
2. Add `<[Name]Section />` + `<div className="section-divider" />` in `App()`
3. Add `{ id:"[kebab-name]", label:"Label Text" }` to the `links` array in `Nav()`

## Adding New Sub-tabs to a Section
1. Add label string to the `tabs={[...]}` prop on `<SubTab />`
2. Add `{subTab==="LABEL" && (<div>...</div>)}` render block beneath existing ones

## Shared Components
| Component | Purpose |
|---|---|
| `KPICard` | Animated count-up metric card |
| `SelectorGroup` | Vertical list of toggle buttons (left panel selectors) |
| `CopyField` | Labeled text field with copy-to-clipboard button |
| `OutputCard` | Badged output container for campaign brief outputs |
| `SubTab` | Horizontal tab switcher within a section |
| `SectionHeader` | Numbered section title with accent label |

## Custom Hooks
- `useCountUp(end, duration, start)` — animates a number from 0 to `end`
- `useInView(ref, threshold)` — fires once when element enters viewport (IntersectionObserver)

## Conventions
- State is always local (`useState`). No Context, no global store.
- Copy-to-clipboard uses `navigator.clipboard.writeText()` with `textarea` fallback.
- Scroll navigation uses `element.scrollIntoView({ behavior: "smooth" })`.
- Loading states use `setTimeout` with a delay (simulated async, no real API calls).
- Git: no history yet — this is pre-first-commit. Commit both `index.html` and `logo.png`.

## Deployment
```
1. Ensure both index.html and logo.png are in the same repo folder
2. Push to GitHub via GitHub Desktop
3. Netlify auto-deploys from the connected branch
4. No build command needed — publish directory is the repo root (or subfolder)
```

## Content Engine (Section 06) — Added April 2026
Platform-native content generator: X threads, LinkedIn posts, TikTok scripts, YouTube outlines, newsletters.

**Data objects:**
- `CE_TOPICS` — 5 topic anchors (source asset themes)
- `CE_GOALS` — 5 goals: Authority, Awareness, Conversion, Engagement, Launch
- `CE_PLATFORMS` — 5 platforms: X Thread, LinkedIn, TikTok Script, YouTube Outline, Newsletter
- `CE_CONTENT` — 18 fully written variants, keyed as `"Topic|Platform|Goal"`
- `PLATFORM_META` — per-platform color, icon, label, and format hint shown in the UI
- `getContentEngine(topic, platform, goal)` — lookup with 2-level fallback (platform match → topic match → generic)

**To add a new content variant:**
```js
"28% Deal Velocity Gain|YouTube Outline|Awareness": {
  hook: "How One Enterprise Sales Team Cut Their Deal Cycle by 28%",
  body: `[0:00 – 0:30] HOOK\n...`,
  cta: "Subscribe + link in description",
  meta: "~8 min · show before/after data · chapter markers",
},
```

**Platform rules baked into PLATFORM_META:**
```
X Thread       → no hashtag spam · one idea per tweet
LinkedIn       → strong first line · short paragraphs · lesson/result framing
TikTok Script  → first 3s interrupt · script around visuals · one CTA
YouTube Outline→ show result early · chapter structure · refresh visual every 20-30s
Newsletter     → one lens · skimmable headers · opening does real work
```

**Content Engine UI structure:**
- Platform pill strip (top) — color-coded per platform, sets active platform
- Left panel — Topic Anchor + Goal selectors + Generate button
- Right panel — Hook card / Full Content card / CTA card / Format Notes card
- Bottom reminder — platform-specific rule for the active platform

## Known Constraints
- Babel standalone transpiles JSX at runtime — ~300ms cold parse on first load (acceptable for portfolio use)
- No hot reload — edit `index.html`, save, refresh browser to see changes
- All data is dummy/simulated — no real API integrations
- Nav has 6 items — if adding more sections, consider a mobile overflow or dropdown
