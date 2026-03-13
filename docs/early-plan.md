# Dusk UI — Early Plan v2

> Based on deep analysis of Pure UI ([MusKRI/pure-ui](https://github.com/MusKRI/pure-ui))  
> Status: **Draft — awaiting discussion with Mohamed before final plan**  
> Date: March 13, 2026

---

## What Changed from v1

The previous early plan was built around coss UI patterns (Fumadocs, CVA, Hugeicons, `useRender`). This version pivots to **Pure UI as the primary reference** because:

1. Pure UI has the best Motion/React integration in the ecosystem — clean separation between CSS animations (components) and Motion (demos)
2. Its animation preset system (`cssAnimationPresets` + `cssTransitionPresets`) is the most composable enter/exit pattern we've seen
3. It uses `tailwind-variants` (slots support) instead of CVA — better for compound components
4. The Tabs sliding indicator and ResizeObserver height animation solve real problems elegantly without JS libs
5. The shadcn CLI registry distribution model is proven and already built out in Pure UI

---

## Project Name & Identity

- **Name:** Dusk UI
- **Tagline:** TBD — to discuss
- **Model:** Copy-paste, not installed as a black box (same as shadcn/ui, Pure UI, coss UI)
- **Distribution:** `npx shadcn add https://dusk-ui.com/registry/<component>`
- **Target:** React 19 + Tailwind CSS v4 + Base UI apps

---

## Stack Decisions

| Decision | Choice | Rationale |
|---|---|---|
| Framework | Next.js 16 (App Router, Turbopack) | Same as Pure UI; mature App Router, fast builds |
| React | 19.x | Concurrent features, Actions, compiler |
| Base UI | 1.0.0 | Stable; all primitives we need are available |
| Styling | Tailwind CSS v4 | `@import "tailwindcss"`, no config file, `@theme inline` bridge |
| Variant util | `tailwind-variants` (`tv()`) | Slots support; better TypeScript inference than CVA |
| Animation (components) | CSS `data-starting-style` / `data-ending-style` | Zero JS, Base UI lifecycle hooks, native `@starting-style` |
| Animation (demos) | Motion v12 (`motion/react`) | `AnimatePresence`, layout, springs — doc site only |
| Linter + Formatter | Biome 2.x | Single tool, fast |
| TypeScript | 5.9, strict | Full type safety |
| Package manager | pnpm | |
| Node | 22.x | Enforced via `engines` |
| Color system | OKLCH | Perceptually uniform, great dark mode |
| Icon set | TBD (to discuss — @tabler or @hugeicons or lucide) | |
| Docs framework | TBD (to discuss — Fumadocs vs next-mdx-remote like Pure UI) | |
| State (Toast) | Zustand | Lightweight, no provider needed |
| React compiler | `babel-plugin-react-compiler` | Auto-memoization |

---

## Folder Structure (Proposed)

Directly modeled on Pure UI's structure with minor naming updates:

```
dusk-ui/
│
├── src/
│   ├── app/
│   │   ├── layout.tsx              ← Root layout (fonts, providers)
│   │   ├── (home)/                 ← Landing page
│   │   └── (app)/
│   │       ├── layout.tsx          ← App shell
│   │       ├── docs/               ← Docs routes
│   │       └── blocks/             ← Block demo routes
│   │
│   ├── core/                       ← Doc-site infrastructure (NOT distributed)
│   │   ├── components/
│   │   │   ├── composed/           ← Header, Sidebar, MobileMenu, ThemeToggle
│   │   │   └── mdx/                ← MDX component registry + renderers
│   │   ├── hooks/                  ← useActiveSection, etc.
│   │   ├── providers/              ← ThemeProvider, ToastProvider
│   │   ├── events/                 ← Analytics helpers
│   │   └── icons/                  ← Icon wrappers
│   │
│   ├── registry/
│   │   └── dusk-ui/
│   │       ├── ui/                 ← The components (one folder per component)
│   │       ├── lib/                ← cn() bundled with components
│   │       ├── examples/           ← Per-component demo files
│   │       ├── blocks/             ← Full section/page blocks
│   │       ├── registry.ts         ← Hand-maintained component manifest
│   │       └── __index__.tsx       ← AUTO-GENERATED barrel (never edit)
│   │
│   ├── lib/
│   │   └── classes.ts              ← cn() (clsx + tailwind-merge)
│   │
│   ├── scripts/
│   │   └── build-registry.ts       ← Generates __index__.tsx
│   │
│   ├── styles/
│   │   ├── globals.css             ← Design tokens + Tailwind v4 setup
│   │   └── partials/               ← Partial CSS (animations, typography, etc.)
│   │
│   ├── content/                    ← MDX documentation
│   └── types/                      ← Global TypeScript ambient declarations
│
├── registry.json                   ← shadcn CLI public manifest (auto-generated)
├── components.json                 ← shadcn CLI config
├── biome.json
├── next.config.ts
├── tsconfig.json
└── package.json
```

---

## Component Roadmap (Phase 1 — Foundation)

These 27 components mirror Pure UI's initial set, grouped by build order:

### Tier 1 — Primitives (no deps, build first)

| Component | Base UI Primitive | Key Design Decisions |
|---|---|---|
| `Button` | `@base-ui/react/button` | `variant`, `size`, `radius` as 3 separate `tv()` axes |
| `Badge` | — (styled `<span>`) | `tv()` variants |
| `Spinner` | — (SVG) | Size prop mapped to px values |
| `Separator` | `@base-ui/react/separator` | Horizontal/vertical via `orientation` |
| `Label` | — (styled `<label>`) | Pairs with form inputs |
| `Kbd` | — (styled `<kbd>`) | Keyboard shortcut display |
| `Avatar` | `@base-ui/react/avatar` | Image + fallback |

### Tier 2 — Form Inputs

| Component | Base UI Primitive | Key Design Decisions |
|---|---|---|
| `Input` | native `<input>` | Ring/error states, slot-based |
| `Textarea` | native `<textarea>` | Optional auto-resize |
| `InputGroup` | — | `tv()` slots: `root`, `addon`, `input` |
| `InputOTP` | `input-otp` pkg | Segmented OTP |
| `Checkbox` | `@base-ui/react/checkbox` | CSS-animated checkmark |
| `RadioGroup` | `@base-ui/react/radio-group` | CSS-animated indicator |
| `Switch` | `@base-ui/react/switch` | Animated thumb |
| `NumberField` | `@base-ui/react/number-field` | Stepper buttons |
| `Select` | `@base-ui/react/select` | Animated popup |
| `Combobox` | `@base-ui/react/combobox` | Filterable dropdown |

### Tier 3 — Layout & Display

| Component | Base UI Primitive | Key Design Decisions |
|---|---|---|
| `Card` | — | `tv()` slots: `root`, `header`, `body`, `footer`, `image` |
| `ScrollArea` | `@base-ui/react/scroll-area` | Custom scrollbar styling |
| `ButtonGroup` | — | Shared border collapse |

### Tier 4 — Animated Overlays (CSS presets required)

| Component | Base UI Primitive | Animation Presets |
|---|---|---|
| `Dialog` | `@base-ui/react/dialog` | 11 presets · nested dialog CSS var · wipe/flip/slide |
| `Sheet` | `@base-ui/react/dialog` | rightSlide / leftSlide / topSlide / bottomSlide |
| `Popover` | `@base-ui/react/popover` | fade / scale |
| `Tooltip` | `@base-ui/react/tooltip` | fade / scale |
| `Toast` | Zustand + custom | slide + Zustand queue |

### Tier 5 — Animated Disclosure

| Component | Base UI Primitive | Animation Presets |
|---|---|---|
| `Accordion` | `@base-ui/react/accordion` | 6 presets · `default/card/swiss` variants · 25 easings |
| `Collapsible` | `@base-ui/react/collapsible` | CSS height animation |
| `Tabs` | `@base-ui/react/tabs` | Sliding indicator · ResizeObserver height · `segmented/underline/card` |

---

## Design Token System (Proposed)

All in OKLCH. Tailwind v4 `@theme inline` bridges tokens to utility classes.

```css
/* src/styles/globals.css */
@import "tailwindcss";
@import "tw-animate-css";
@import "./partials/index.css";

@custom-variant dark (&:where(.dark, .dark *));

:root {
  /* Radius */
  --radius: 0.625rem;

  /* Core surfaces */
  --background:            oklch(1 0 0);
  --foreground:            oklch(0.21 0.006 285.885);
  --border:                oklch(0 0 0 / 10%);
  --input:                 oklch(0 0 0 / 10%);
  --ring:                  oklch(0.705 0.015 286.067);

  /* Brand */
  --primary:               oklch(0.274 0.006 286.033);
  --primary-foreground:    oklch(0.985 0 0);
  --secondary:             oklch(0 0 0 / 4%);
  --secondary-foreground:  oklch(0.21 0.006 285.885);

  /* Surfaces */
  --card:                  oklch(1 0 0);
  --card-foreground:       oklch(0.21 0.006 285.885);
  --popover:               oklch(1 0 0);
  --popover-foreground:    oklch(0.21 0.006 285.885);
  --muted:                 oklch(0 0 0 / 4%);
  --muted-foreground:      oklch(0.442 0.017 285.786);
  --accent:                oklch(0 0 0 / 4%);
  --accent-foreground:     oklch(0.21 0.006 285.885);

  /* Semantic states */
  --destructive:           oklch(0.637 0.237 25.331);
  --destructive-foreground:oklch(0.505 0.213 27.518);
  --success:               oklch(0.696 0.17 162.48);
  --success-foreground:    oklch(0.508 0.118 165.612);
  --warning:               oklch(0.769 0.188 70.08);
  --warning-foreground:    oklch(0.555 0.163 48.998);
  --info:                  oklch(0.623 0.214 259.815);
  --info-foreground:       oklch(0.488 0.243 264.376);

  /* Sidebar */
  --sidebar:               oklch(0.985 0 0);
  /* ...full sidebar palette */
}

.dark {
  /* Inverted token values — TBD after brand direction is set */
}

@theme inline {
  /* Bridge CSS vars → Tailwind utilities */
  --color-background:   var(--background);
  --color-foreground:   var(--foreground);
  --color-primary:      var(--primary);
  /* ...etc */
  --radius-sm:   calc(var(--radius) - 4px);
  --radius-md:   calc(var(--radius) - 2px);
  --radius-lg:   var(--radius);
  --radius-xl:   calc(var(--radius) + 4px);
  --radius-2xl:  calc(var(--radius) + 8px);
}
```

---

## Animation System Plan

### cssAnimationPresets to implement (full set)

| Preset | What animates |
|---|---|
| `none` | No animation |
| `fade` | opacity + height |
| `scale` | scale + opacity + height (origin-top) |
| `slide` | translateY + opacity + height |
| `topSlide` | translateY negative (slides down from top) |
| `bottomSlide` | translateY positive (slides up from bottom) |
| `leftSlide` | translateX negative |
| `rightSlide` | translateX positive |
| `topFlip` | rotateX from top with perspective |
| `bottomFlip` | rotateX from bottom with perspective |
| `leftFlip` | rotateY from left with perspective |
| `rightFlip` | rotateY from right with perspective |
| `wipe` | `clip-path: inset()` curtain reveal |
| `perspective` | rotateX(90deg) + perspective(1000px) |
| `perspectiveBlur` | perspective + blur(9px) |

### cssTransitionPresets to implement (25 named easings)

inExpo · outExpo · inOutExpo · anticipate · quickOut · overshootOut · swiftOut · snappyOut · in · out · inOut · outIn · inQuad · outQuad · inOutQuad · inCubic · outCubic · inOutCubic · inQuart · outQuart · inOutQuart · inQuint · outQuint · inOutQuint · inCirc · outCirc · inOutCirc · inOutBase

---

## Open Questions (for discussion)

These are the key decisions we need to make together before writing the final plan:

### Q1 — Docs Framework
**Option A:** `next-mdx-remote` (Pure UI approach) — full control, custom MDX components, no framework lock-in  
**Option B:** Fumadocs — batteries-included, search, sidebar config, less custom work  
→ **Recommendation:** Discuss tradeoffs; Pure UI's MDX system is well-architected and highly customizable

### Q2 — Icon Set
**Option A:** `@tabler/icons-react` (Pure UI uses this) — 5000+ icons, consistent stroke style, free  
**Option B:** `@hugeicons/react` (coss UI approach) — unique style, paid tier for full set  
**Option C:** `lucide-react` — most common in ecosystem  
→ **To decide:** Does Dusk UI have a preferred icon style/brand?

### Q3 — Typography / Display Font
Pure UI uses `Chillax` (geometric sans) for headings + `Noto Mono` for code.  
→ **To decide:** What fonts define Dusk UI's visual identity?

### Q4 — Component API — `ButtonGroup` vs `Button` composition
**Option A:** `<ButtonGroup>` wrapping `<Button>` elements (Pure UI approach)  
**Option B:** `<Button>` with `groupFirst`/`groupMiddle`/`groupLast` props  
→ Pure UI's approach is cleaner and more composable

### Q5 — Toast Architecture
Pure UI uses **Zustand** for the toast queue — a store outside React tree, imperatively called via `toast.add(...)`.  
→ Should Dusk UI match this (no provider needed) or use a React context provider?

### Q6 — Additional Component Tiers
Pure UI has 27 components. Should Phase 2 of Dusk UI add:  
- Data components (`Table`, `DataGrid`)  
- Navigation (`NavigationMenu`, `Breadcrumb`, `Pagination`)  
- Feedback (`Progress`, `Skeleton`, `Alert`)  
- Form (`DatePicker`, `Slider`, `ColorPicker`)  
→ **To prioritize together**

### Q7 — Brand Color
Pure UI is monochrome (near-black primary). Should Dusk UI have:  
**Option A:** A distinct brand color (blue, violet, etc.)  
**Option B:** Neutral/monochrome like Pure UI (timeless, works everywhere)  
→ **Mohamed's call**

### Q8 — `radius` variant on Button
Pure UI exposes `radius` as a first-class `tv()` variant axis.  
→ Keep this? It's useful but adds API surface area.

---

## What's Definitely Decided (Not Up for Discussion)

- Base UI 1.0 as primitive layer
- `tailwind-variants` (not CVA)
- CSS `data-starting-style` / `data-ending-style` for all component animations
- OKLCH color system
- shadcn CLI distribution via `registry.json`
- Biome for linting/formatting
- React 19 + TypeScript 5.9 strict
- The full `cssAnimationPresets` + `cssTransitionPresets` system
- `data-slot` on every sub-part
- The `build-registry.ts` → `shadcn build` pipeline
- Named exports only, no default exports
- `pnpm` + Node 22.x
