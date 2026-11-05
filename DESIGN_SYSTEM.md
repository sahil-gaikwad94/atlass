# Design System — Atlas // OS

**Status:** Draft v0.1
**Based on:** the React/Tailwind prototype (dark "precision instrument" look: deep navy-black, cyan + lime + orange accents, mono labels, glass dock). Where the prototype is too small or too low-contrast for real use, this doc says what to change.

---

## 1. Principles

1. **Calm depth.** Dark, quiet surfaces; colour is used to signal meaning, not decoration.
2. **Reading first.** Long-form lessons must be comfortable for 30 minutes on a phone.
3. **Interactive things look interactive.** Tappable terms, symbols, and sliders are visibly different from prose.
4. **Motion explains.** Animate to show cause and effect (fan-out opening, data moving), never just to impress.
5. **Accessible by default.**

## 2. Colour tokens

| Token | Value (prototype) | Use |
|---|---|---|
| `--bg` | `#040508` / `#07080D` | App and phone-frame background |
| `--surface-1` | `#0C0E16` | Cards, modules |
| `--surface-2` | `#111625` | Raised/active cards, headers |
| `--surface-inset` | `#000000` @ 50–70% | Code, formula wells |
| `--border` | `#FFFFFF` @ 10–15% | Default borders |
| `--text` | `#F1F5F9` | Primary text |
| `--text-muted` | `#FFFFFF` @ 65–70% | Secondary text |
| `--text-faint` | `#FFFFFF` @ 50% | Meta (check contrast, see §3) |
| `--accent` (cyan) | `#00F0FF` | Primary actions, active state, links, math "T" |
| `--accent-2` (lime) | `#D4FF00` | Highlights, Bridge callouts, progress, "Agent" |
| `--danger` (orange) | `#FF5500` | Principal-level, chaos, stalls, warnings |
| `--success` | `#10B981` | Completed, healthy |

Gradient signature: `cyan → lime → orange` (logo ring, Atlas orb, progress bar).

**Light theme** *(P2)*: define the same tokens as CSS variables so themes are swappable; do not hardcode hex in components.

```css
:root {
  --bg:#07080D; --surface-1:#0C0E16; --surface-2:#111625;
  --text:#F1F5F9; --text-muted:rgb(255 255 255 / .68);
  --accent:#00F0FF; --accent-2:#D4FF00; --danger:#FF5500; --success:#10B981;
}
```

## 3. Typography

| Role | Prototype | **Recommended** |
|---|---|---|
| Lesson prose | 13px | **16px**, line-height 1.6–1.7, max ~65 characters per line on tablets |
| UI text | 11–12px | 14px |
| Meta / mono labels | 9–10px | **min 11px**; use uppercase sparingly |
| Headings | 16–18px extrabold | 20–24px, tighter tracking |
| Code | 11px mono | 13px mono, horizontal scroll with fade edges |

- Sans: a clean UI font (system or Inter-class). Mono: JetBrains Mono / system mono for labels and code.
- Support Dynamic Type / user font scaling; layouts must not break at 200%.
- Equations: render with KaTeX/MathML; every equation has a text alternative.

**Contrast:** text must meet WCAG 2.2 AA (4.5:1 body, 3:1 large/UI). Verify `--text-faint` and coloured text on `--surface-*`; raise opacity or lighten if it fails.

## 4. Layout and spacing

- 4px base grid; common steps 4, 8, 12, 16, 24.
- Radii: cards 24–28px, inner wells 16px, chips 8–12px, dock 30px.
- Screen padding 16px; respect safe areas (notch, home indicator).
- Content width: single column on phones; two-pane (outline + reader) on tablet/desktop.
- Floating dock overlaps content: add bottom padding ≥ dock height + safe area.

## 5. Components

| Component | Notes |
|---|---|
| **Dock (4+1)** | 5 items; center Atlas orb raised; active tab uses `--accent`; 44px+ touch targets; label always visible |
| **Atlas Orb** | Gradient ring; states: idle, working (pulse), needs-confirmation (orange dot), done (lime dot) |
| **Course header card** | Title, counts (modules, lessons, labs), progress bar, cross-course credit |
| **Phase label** | Mono, lime, uppercase |
| **Module card** | Status icon (done / active / upcoming), title, meta; expandable lesson list |
| **Lesson row** | Number, title, type tags, status; active row highlighted |
| **Fan-out chip `⑂`** | Inline, cyan, bordered; tap to open drawer; visible affordance in prose |
| **Fan-out drawer** | Left cyan border, collapse control, breadcrumb, "merge back" action |
| **Bridge callout** | Lime tint, bordered; labelled "From your background" |
| **Math Decoder** | Formula with tappable symbols → detail panel (plain English / code / live value); sliders |
| **Simulator frame** | Title, play/pause/reset, seed, speed; text summary for screen readers |
| **X-Ray stop card** | Stop number, file/symbol, commit link, short explanation |
| **Ghost-slot editor** | Read-only code dimmed; editable regions highlighted; token toolbar; Run tests button |
| **Hint Ladder** | 5 steps with cost shown; confirm before H4/H5 |
| **Claim badge** | Small pill: Verified / Model-written / Disputed; tap for sources |
| **Agent Trace row** | Tool icon, summary, duration, cost; expandable |
| **Cost meter** | Spend vs. budget for the current task |
| **Toast / confirm sheet** | Used for T3 actions (publish/share) |

## 6. Motion

- Library: Framer Motion (as in the prototype).
- Durations: 150–250ms for UI; 300–400ms for drawers; springs for the orb.
- Fan-out: height + opacity expand from the tapped term; collapse returns scroll position.
- Respect `prefers-reduced-motion`: replace slides with fades, stop looping pings.
- Avoid infinite animations (the prototype's pinging dots) except for genuine "live" states.

## 7. Content patterns

- **Lesson structure:** hook → Bridge → narrative → interactive → X-Ray/paper → lab → Teach-Back.
- **Section headings** use mono eyebrow + short title; keep paragraphs ≤ 5 lines on phone.
- **Callout types:** Bridge (lime), Pitfall (orange), Production note (cyan), Source note (neutral).
- **Code in prose:** inline code uses `--surface-inset` background; never rely on colour alone to convey meaning.

## 8. Accessibility checklist

- Touch targets ≥ 44×44px.
- Focus visible for keyboard/switch users.
- Screen-reader labels for icon-only buttons; simulators expose a text description and state changes via live regions.
- No information by colour alone (status icons + text).
- Captions/transcripts for audio and video.
- Test with VoiceOver and TalkBack on real devices.

## 9. Performance budget

- First lesson render: < 2 s on a mid-range phone over 4G.
- Lazy-load simulators, Pyodide, and heavy libraries; prefetch when a lab is next.
- Avoid blur-heavy effects on low-end devices (the dock's backdrop blur → fallback to solid surface).
- Keep the initial JS bundle small; code-split by tab.

## 10. Implementation notes

- Tailwind with design tokens mapped to CSS variables (`tailwind.config` reads the variables).
- Component library: shadcn-style primitives + custom blocks; Storybook for block catalogue.
- Visual regression tests for core blocks (Chromatic/Playwright screenshots).
- Icon set: Lucide (as in the prototype).

## 11. Open questions

- Light theme at launch or later?
- Custom type (brand font) vs. system fonts for speed?
- Tablet layout: two-pane reader in v1?
