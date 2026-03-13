# Dusk UI — Final Plan

> **Status:** All decisions locked. This is the single source of truth.  
> **Author:** Mohamed Gamal ([@mohamed-g-shoaib](https://github.com/mohamed-g-shoaib))  
> **Last updated:** March 13, 2026

---

## 1. Locked Stack

| Concern | Decision |
|---|---|
| **Library name** | **Dusk** |
| **Deployment URL** | `dusk-ui.vercel.app` |
| **Framework** | Next.js (App Router, latest) |
| **Base primitive** | `@base-ui/react` |
| **Styling** | Tailwind CSS v4 |
| **Animation** | CSS-first (Base UI `data-starting-style`); `motion/react` for physics/gestures only |
| **Linter + Formatter** | Biome (one tool, one config) |
| **Package manager** | pnpm |
| **Docs framework** | Fumadocs |
| **Registry** | shadcn format (`shadcn build`) |
| **Icons** | `@hugeicons/react` + `@hugeicons/core-free-icons` |
| **Forms** | `react-hook-form` + Zod (Particles tier only) |
| **Themes** | `next-themes` — Light + Dark |
| **Dark mode selector** | `.dark` class via `next-themes`; `@variant dark (&:where(.dark, .dark *))` in CSS |
| **Font** | Geist Sans (UI), Geist Mono (code), Geist Pixel (display headings only) |
| **Color space** | `oklch()` throughout |
| **Brand color** | Orange — `oklch(0.68 0.18 55)` as `--primary` |
| **RTL** | shadcn native RTL — physical classes in source, CLI transforms at install |
| **Rich text in components** | No — `ReactNode` children only |
| **3D / Canvas** | No |
| **Monorepo** | No |
| **License** | MIT |
| **Target audience** | Public open-source, enterprise-scalable |
| **Deployment** | Vercel |

---

## 2. Design Token Spec

Based on Selia's token architecture with hue shifted to orange. Uses `oklch()` throughout.

```css
:root {
  /* === SURFACES === */
  --background: oklch(1 0 0);
  --foreground: oklch(0.145 0 0);
  --border: oklch(0.88 0.005 80);
  --muted: oklch(0.96 0.005 80);
  --muted-foreground: oklch(0.52 0.01 80);
  --accent: oklch(0.94 0.01 55);
  --accent-foreground: oklch(0.25 0.02 55);

  /* === BRAND — Orange primary === */
  --primary: oklch(0.68 0.18 55);
  --primary-foreground: oklch(1 0 0);
  --primary-border: oklch(0.62 0.19 55);
  --secondary: oklch(0.94 0.01 55);
  --secondary-foreground: oklch(0.3 0.05 55);
  --secondary-border: oklch(0.88 0.02 55);

  /* === SEMANTIC STATES === */
  --danger: oklch(0.577 0.245 27.325);
  --danger-foreground: oklch(0.98 0.01 27);
  --danger-border: oklch(0.52 0.22 27);

  --warning: oklch(0.75 0.18 70);
  --warning-foreground: oklch(0.25 0.05 70);
  --warning-border: oklch(0.70 0.17 70);

  --success: oklch(0.65 0.175 145);
  --success-foreground: oklch(0.98 0.01 145);
  --success-border: oklch(0.59 0.16 145);

  --info: oklch(0.60 0.19 240);
  --info-foreground: oklch(0.98 0.01 240);
  --info-border: oklch(0.54 0.18 240);

  /* === COMPONENT SUB-TOKENS === */
  --card: oklch(0.99 0 0);
  --card-border: oklch(0.90 0.005 80);
  --card-footer: oklch(0.96 0.005 80);
  --card-shadow: 0 1px 3px oklch(0 0 0 / 0.06);

  --popover: oklch(1 0 0);
  --popover-border: oklch(0.88 0.005 80);
  --popover-shadow: 0 4px 12px oklch(0 0 0 / 0.10);
  --popover-accent: oklch(0.95 0.008 55);

  --dialog: oklch(1 0 0);
  --dialog-border: oklch(0.88 0.005 80);
  --dialog-footer: oklch(0.97 0.003 80);

  --input: oklch(1 0 0);
  --input-border: oklch(0.82 0.008 80);
  --input-accent-border: oklch(0.68 0.18 55);
  --input-shadow: 0 1px 2px oklch(0 0 0 / 0.04);

  --tabs: oklch(0.96 0.005 80);
  --tabs-border: oklch(0.90 0.005 80);
  --tabs-accent: oklch(1 0 0);

  --toast: oklch(1 0 0);
  --toast-border: oklch(0.88 0.005 80);

  /* === RADIUS === */
  --radius: 0.75rem;
  --radius-xs: 5px;
  --radius-sm: calc(var(--radius) - 0.25rem);
  --radius-md: var(--radius);
  --radius-lg: calc(var(--radius) + 0.25rem);
  --radius-xl: calc(var(--radius) + 0.5rem);
  --radius-2xl: calc(var(--radius) + 1rem);
  --radius-full: 9999px;

  /* === TYPOGRAPHY === */
  --font-sans: 'Geist', sans-serif;
  --font-mono: 'Geist Mono', monospace;
  --font-display: 'Geist Pixel', monospace;

  /* === SHADOWS === */
  --shadow: 0 1px 2px oklch(0 0 0 / 0.05);
  --shadow-card: 0 1px 3px oklch(0 0 0 / 0.06);
  --shadow-input: 0 1px 2px oklch(0 0 0 / 0.04);
  --shadow-popover: 0 4px 12px oklch(0 0 0 / 0.10);

  /* === SIDEBAR SUB-SYSTEM === */
  --sidebar: oklch(0.97 0.003 80);
  --sidebar-border: oklch(0.90 0.005 80);
  --sidebar-foreground: oklch(0.3 0.01 80);
  --sidebar-accent: oklch(0.93 0.01 55);
  --sidebar-accent-foreground: oklch(0.3 0.05 55);
  --sidebar-ring: oklch(0.68 0.18 55);
  --sidebar-primary: oklch(0.68 0.18 55);

  /* === CHART COLORS === */
  --chart-1: oklch(0.68 0.18 55);
  --chart-2: oklch(0.60 0.19 240);
  --chart-3: oklch(0.65 0.175 145);
  --chart-4: oklch(0.75 0.18 70);
  --chart-5: oklch(0.577 0.245 27);
}

.dark {
  --background: color-mix(in srgb, oklch(0.145 0 0) 96%, white);
  --foreground: oklch(0.96 0 0);
  --border: oklch(0.28 0.005 80);
  --muted: oklch(0.22 0.005 80);
  --muted-foreground: oklch(0.62 0.01 80);
  --accent: oklch(0.28 0.015 55);
  --accent-foreground: oklch(0.90 0.02 55);

  --primary: oklch(0.72 0.17 55);
  --primary-foreground: oklch(0.10 0.01 55);
  --primary-border: oklch(0.65 0.18 55);
  --secondary: oklch(0.26 0.015 55);
  --secondary-foreground: oklch(0.88 0.02 55);
  --secondary-border: oklch(0.32 0.02 55);

  --danger: oklch(0.65 0.22 27);
  --danger-foreground: oklch(0.10 0.01 27);
  --danger-border: oklch(0.58 0.20 27);

  --warning: oklch(0.80 0.16 70);
  --warning-foreground: oklch(0.15 0.03 70);
  --warning-border: oklch(0.73 0.15 70);

  --success: oklch(0.70 0.16 145);
  --success-foreground: oklch(0.10 0.02 145);
  --success-border: oklch(0.63 0.15 145);

  --info: oklch(0.65 0.17 240);
  --info-foreground: oklch(0.10 0.01 240);
  --info-border: oklch(0.58 0.16 240);

  --card: color-mix(in srgb, oklch(0.145 0 0) 94%, white);
  --card-border: oklch(0.28 0.005 80);
  --card-footer: oklch(0.20 0.005 80);
  --card-shadow: 0 1px 3px oklch(0 0 0 / 0.25);

  --popover: color-mix(in srgb, oklch(0.145 0 0) 94%, white);
  --popover-border: oklch(0.30 0.005 80);
  --popover-shadow: 0 4px 16px oklch(0 0 0 / 0.40);
  --popover-accent: oklch(0.26 0.015 55);

  --dialog: color-mix(in srgb, oklch(0.145 0 0) 94%, white);
  --dialog-border: oklch(0.28 0.005 80);
  --dialog-footer: oklch(0.20 0.005 80);

  --input: oklch(0.20 0.005 80);
  --input-border: oklch(0.32 0.008 80);
  --input-accent-border: oklch(0.72 0.17 55);
  --input-shadow: 0 1px 2px oklch(0 0 0 / 0.20);

  --tabs: oklch(0.22 0.005 80);
  --tabs-border: oklch(0.28 0.005 80);
  --tabs-accent: oklch(0.18 0.005 80);

  --toast: color-mix(in srgb, oklch(0.145 0 0) 94%, white);
  --toast-border: oklch(0.28 0.005 80);

  --sidebar: oklch(0.18 0.005 80);
  --sidebar-border: oklch(0.26 0.005 80);
  --sidebar-foreground: oklch(0.80 0.01 80);
  --sidebar-accent: oklch(0.26 0.015 55);
  --sidebar-accent-foreground: oklch(0.88 0.02 55);
  --sidebar-ring: oklch(0.72 0.17 55);
  --sidebar-primary: oklch(0.72 0.17 55);
}

/* === ROOT ISOLATION === */
.root { isolation: isolate; }   /* Base UI portal z-index */
body { position: relative; }    /* iOS Safari 26+ backdrop */
```

---

## 3. Component Roadmap

### Tier 1 — Atoms (build first)

| Component | Base UI Primitive | Status |
|---|---|---|
| Button | `Button` | 🔲 |
| Badge | none (styled span) | 🔲 |
| Input | `Input` | 🔲 |
| Textarea | `Input` (multiline) | 🔲 |
| Checkbox | `Checkbox` | 🔲 |
| Radio | `Radio` | 🔲 |
| Switch | `Switch` | 🔲 |
| Separator | `Separator` | 🔲 |
| Avatar | `Avatar` | 🔲 |
| Spinner | none (animated SVG) | 🔲 |
| Skeleton | none (animated div) | 🔲 |
| Label | `Field.Label` | 🔲 |
| Kbd | none (styled `kbd`) | 🔲 |
| Chip | none (styled span) | 🔲 |

### Tier 2 — Molecules

| Component | Base UI Primitive | Status |
|---|---|---|
| Select | `Select` | 🔲 |
| Combobox | `Combobox` | 🔲 |
| Autocomplete | `Autocomplete` | 🔲 |
| Dialog | `Dialog` | 🔲 |
| Alert Dialog | `AlertDialog` | 🔲 |
| Drawer | `Drawer` | 🔲 |
| Popover | `Popover` | 🔲 |
| Tooltip | `Tooltip` | 🔲 |
| Toast | `Toast` | 🔲 |
| Tabs | `Tabs` | 🔲 |
| Accordion | `Accordion` | 🔲 |
| Collapsible | `Collapsible` | 🔲 |
| Slider | `Slider` | 🔲 |
| Number Field | `NumberField` | 🔲 |
| Progress | `Progress` | 🔲 |
| Meter | `Meter` | 🔲 |
| Toggle | `Toggle` | 🔲 |
| Toggle Group | `ToggleGroup` | 🔲 |

### Tier 3 — Organisms

| Component | Base UI Primitive | Status |
|---|---|---|
| Field | `Field` | 🔲 |
| Fieldset | `Fieldset` | 🔲 |
| Form | `Form` + react-hook-form | 🔲 |
| Command | `Combobox` (adapted) | 🔲 |
| Menu | `Menu` | 🔲 |
| Menubar | `Menubar` | 🔲 |
| Context Menu | `ContextMenu` | 🔲 |
| Navigation Menu | `NavigationMenu` | 🔲 |
| Toolbar | `Toolbar` | 🔲 |
| Table | none (HTML table) | 🔲 |
| Scroll Area | `ScrollArea` | 🔲 |
| Pagination | none (composed) | 🔲 |
| Preview Card | `PreviewCard` | 🔲 |
| Breadcrumb | none (composed) | 🔲 |
| Alert | none (styled div) | 🔲 |
| Card | none (styled div) | 🔲 |

### Tier 4 — Layout Primitives (Selia-inspired)

| Component | Description | Status |
|---|---|---|
| Stack | Flexbox layout primitive with gap/direction | 🔲 |
| Heading | Typography primitive with semantic levels | 🔲 |
| Text | Inline typography primitive | 🔲 |
| Divider | Enhanced separator with label support | 🔲 |
| Icon Box | Icon container with background/size variants | 🔲 |
| Item | Generic list item primitive | 🔲 |

### Tier 5 — Complex Layout

| Component | Description | Status |
|---|---|---|
| Sidebar | Full layout system with mobile Sheet fallback | 🔲 |
| Empty | Empty state with icon, title, description, action | 🔲 |
| Input Group | Prefix/suffix icons, addons | 🔲 |
| Frame | Layout framing primitive | 🔲 |

---

## 4. Architecture

```
dusk-ui/
├── app/
│   ├── (docs)/
│   │   ├── layout.tsx              # Fumadocs sidebar layout
│   │   └── [[...slug]]/
│   │       └── page.tsx
│   ├── (home)/
│   │   └── page.tsx                # Landing page
│   ├── layout.tsx                  # Root: fonts, ThemeProvider, DirectionProvider
│   ├── not-found.tsx
│   └── globals.css                 # All design tokens + Tailwind base
├── components/
│   ├── ui/                         # All distributable components
│   │   └── button.tsx
│   └── docs/                       # Docs-site-only components
├── registry/
│   ├── registry-ui.ts
│   ├── registry-styles.ts
│   ├── registry-hooks.ts
│   ├── registry-lib.ts
│   └── default/ui/
├── content/docs/                   # MDX component documentation
├── lib/
│   ├── utils.ts                    # cn() and helpers
│   └── source.ts                   # Fumadocs source config
├── hooks/                          # Shared custom hooks
├── public/r/                       # Built registry served as static JSON
├── registry.json                   # Generated by shadcn build
├── components.json                 # shadcn config (base-nova, rtl: true)
├── biome.json
├── next.config.ts
├── postcss.config.mjs
├── tsconfig.json
├── AGENTS.md                       # LLM-targeted coding rules
├── CHANGELOG.md
├── CONTRIBUTING.md
└── package.json
```

**Rules:**
- `app/` is routing-only. Logic, hooks, utils, components all live outside.
- `(docs)` and `(home)` are route groups — separate layouts, no URL prefix.
- `components/ui/` holds only distributable components — no docs-site-specific code.
- `public/r/` is the static registry endpoint for `shadcn add` to consume.

---

## 5. The Cross-Library Steal List

Implementation rules synthesized from all four reference libraries:

### From Selia
- `oklch()` color space for all tokens
- Component-specific sub-tokens (`--card-*`, `--popover-*`, `--input-*`, etc.)
- Completely separate dark mode token set — no shortcuts
- Compact 14px base type scale
- Layout primitives: `Stack`, `Heading`, `Text`, `Divider`, `Icon Box`, `Item`, `Chip`
- Ring/focus system via `data-[focused]` + CSS `outline` via `--ring` token

### From coss ui
- `useRender` + `mergeProps` on every interactive component for polymorphism
- `data-slot` attribute on every compound sub-part for external styling API
- `pointer-coarse:after:min-h-11 min-w-11` touch target safety on buttons
- `color-mix()` for dark mode computed surfaces
- `--info`, `--success`, `--warning`, `--danger` full semantic set with foreground + border
- Inset shadow press feedback on buttons (tactile feel without JS)
- `--chart-1..5` palette tokens
- `--sidebar-*` sub-system (7 tokens)
- `--code`, `--code-foreground`, `--code-highlight` tokens
- Pure CSS toast stacking via `data-starting-style`/`data-ending-style`
- `isolation: isolate` on root, `position: relative` on body
- Mobile active opacity: `a:active, button:active { opacity: 72% on mobile }`

### From Pure UI
- Typed event bus (`core/events`) for imperative Toast/Dialog triggering
- Animation three-layer principle:
  1. CSS `data-starting-style`/`data-ending-style` → enter/exit (Base UI, zero JS)
  2. Tailwind transitions → micro-interactions
  3. `motion/react` → physics only (spring, drag, layout reflow)

### From Spell UI
- `motion/react` for physics-based animations where CSS is insufficient
- Visual text effects as optional decorative Particle components (not core)

---

## 6. AGENTS.md Rules (Dusk)

> This section is the authoritative coding contract. Every component must follow these rules without exception.

### File & Naming
- Distributable components: `components/ui/{component}.tsx`
- One file per component, one responsibility
- Named exports only — `export { Button }`, never `export default`
- Colocate types with component: `export interface ButtonProps {}`

### `"use client"`
- Add **only** when using: React hooks, `useState`/`useEffect`, event handlers that call state setters, browser APIs
- Never add for stateless/display components
- Never as a default — must be justified per file

### Imports
```tsx
// ✅ Correct icon imports
import { HugeiconsIcon } from '@hugeicons/react';
import { SearchIcon, SunIcon } from '@hugeicons/core-free-icons';
// ❌ Never
import { SearchIcon } from 'hugeicons-react'; // deprecated

// ✅ React hooks
import { useState, useCallback } from 'react';
// ❌ Never
import * as React from 'react';

// ✅ Base UI
import * as Dialog from '@base-ui/react/dialog';

// ✅ Utilities
import { cn } from '@/lib/utils';

// ✅ Forms (Particles tier only)
import { useForm } from 'react-hook-form';
import { z } from 'zod';
```

### Base UI Composition
```tsx
// ✅ Polymorphism via render prop
<Button.Root render={<a href="/" />}>Home</Button.Root>

// ✅ State-dependent rendering
<Switch.Thumb
  render={(props, state) => (
    <span {...props}>{state.checked ? <CheckIcon /> : <XIcon />}</span>
  )}
/>

// ✅ eventDetails for cancel without full controlled mode
<Tooltip.Root
  onOpenChange={(open, { reason, cancel }) => {
    if (reason === 'trigger-press') cancel();
  }}
/>

// ✅ Prevent Base UI from handling
<NumberField.Input onPaste={(e) => { e.preventBaseUIHandler(); }} />

// ❌ Never asChild
<Menu.Trigger asChild><Button>Open</Button></Menu.Trigger>
```

### Styling
```tsx
// ✅ Semantic tokens
<p className="text-muted-foreground" />
// ❌ Raw colors
<p className="text-gray-500" />

// ✅ data-slot on every compound sub-part
<div data-slot="dialog-footer" className="..." />

// ✅ Parent-aware hover via data-slot
"in-[[data-slot=button]:hover]:opacity-80"
// ❌ Avoid group/group-hover when data-slot works
"group-hover:opacity-80"

// ✅ Important override
"h-auto!"

// ✅ Touch targets on buttons
"relative pointer-coarse:after:absolute pointer-coarse:after:inset-0 pointer-coarse:after:min-h-11 pointer-coarse:after:min-w-11"

// ✅ Press feedback
"not-disabled:inset-shadow-[0_1px_--theme(--color-white/16%)]"
"[:active]:inset-shadow-[0_1px_--theme(--color-black/8%)]"
```

### Animation
```tsx
// ✅ Enter/exit — CSS only via Base UI attributes
"data-[starting-style]:opacity-0 data-[starting-style]:-translate-y-1"
"data-[ending-style]:opacity-0 data-[ending-style]:-translate-y-1"
"transition-[opacity,transform] duration-150 ease-out"

// ✅ Physics/gesture — motion/react
import { motion } from 'motion/react';
<motion.div drag="x" onDragEnd={handleDismiss} />

// ❌ Never motion/react for simple show/hide
<AnimatePresence><motion.div initial={{ opacity: 0 }} animate={{ opacity: 1 }} /></AnimatePresence>
```

### Icons
```tsx
// ✅ Size via prop only
<HugeiconsIcon icon={SearchIcon} size={20} color="currentColor" strokeWidth={1.5} />
// ❌ Never size via className
<HugeiconsIcon icon={SearchIcon} className="w-5 h-5" />

// ✅ Decorative
<HugeiconsIcon icon={StarIcon} size={16} color="currentColor" aria-hidden="true" />
// ✅ Semantic (icon-only button)
<button aria-label="Search"><HugeiconsIcon icon={SearchIcon} size={20} color="currentColor" /></button>
// ✅ Alert icon — never aria-hidden
<HugeiconsIcon icon={AlertIcon} size={20} color="currentColor" />
```

### RTL
- Write physical classes (`left-*`, `pl-*`, `right-*`, `pr-*`) — CLI transforms at install
- Never write `ps-*`/`pe-*` in source
- RTL-flipped icons: add `rtl:rotate-180`
- Portal elements (`Dialog`, `Popover`, `Drawer`, `Toast`): always pass `dir` prop when RTL

### Static Data
```tsx
// ✅ Outside component
const SIZES = ['sm', 'md', 'lg'] as const;
function Button() { ... }

// ❌ Never inside
function Button() {
  const SIZES = ['sm', 'md', 'lg'];
}
```

### Build Workflow
```bash
pnpm biome check --write .    # lint + format
pnpm shadcn build             # generate registry.json + public/r/
```

---

## 7. Bootstrap Sequence

Run these commands **in order** in a clean directory:

```bash
# 1. Bootstrap
pnpm dlx shadcn@latest init --preset aH32 --base base --template next --rtl

# 2. Animation
pnpm add motion

# 3. Themes
pnpm add next-themes

# 4. Docs
pnpm add fumadocs-core fumadocs-mdx fumadocs-ui

# 5. Icons
pnpm add @hugeicons/react @hugeicons/core-free-icons

# 6. Forms
pnpm add react-hook-form @hookform/resolvers zod

# 7. Dev tooling
pnpm add -D @biomejs/biome
pnpm biome init
```

Then manually configure:
1. `globals.css` — full token system (§2) + `@variant dark` + `isolation` + `body position`
2. `app/layout.tsx` — Geist fonts + `ThemeProvider` from `next-themes` + `DirectionProvider` from Base UI
3. `next.config.ts` — Fumadocs MDX plugin
4. `lib/source.ts` — Fumadocs source config
5. `biome.json` — lint + format rules
6. `components.json` — verify `"base-nova"` + `"rtl": true`

---

## 8. First Component: Button

The Button establishes **every pattern** used by every future component. It must be built first and reviewed carefully before any other work begins.

Button implements:
- `useRender` + `mergeProps` (polymorphism)
- `data-slot="button"` (structural styling API)
- CVA variants (`variant`, `size`)
- Touch target safety (`pointer-coarse:after:`)
- Inset shadow press feedback
- Full Hugeicons icon support (leading + trailing)
- `aria-label` for icon-only variant
- Reduced-motion awareness
- All semantic token usage

Once Button is approved, all other components follow the same skeleton.
