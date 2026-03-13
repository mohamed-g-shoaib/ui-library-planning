# UI Library — Early Planning Document

> **Session date:** March 13, 2026  
> **Status:** Pre-implementation — planning phase  
> **Author:** Mohamed Gamal ([@mohamed-g-shoaib](https://github.com/mohamed-g-shoaib))

---

## 1. Context & Inspiration

This library was designed after a deep study of four production UI libraries:

| Library | Repo | Live Docs |
|---|---|---|
| **coss ui** | [cosscom/coss](https://github.com/cosscom/coss) | [coss.com/ui/docs](https://coss.com/ui/docs) |
| **Pure UI / Kamui** | [MusKRI/pure-ui](https://github.com/MusKRI/pure-ui) | [pure.kam-ui.com/docs](https://pure.kam-ui.com/docs) |
| **Selia** | [nauvalazhar/selia](https://github.com/nauvalazhar/selia) | [selia.earth/docs](https://selia.earth/docs/introduction) |
| **Spell UI** | [xxtomm/spell-ui](https://github.com/xxtomm/spell-ui) | [spell.sh/docs](https://spell.sh/docs/introduction) |

### Key Learnings

**coss ui** is the primary architectural reference:
- 51 confirmed components, `registry-particles.ts` for pre-built compositions
- `AGENTS.md` (37KB) — LLM-targeted coding guide (all patterns adopted, see §9)
- Biome for lint + format, Turborepo + Bun
- `render` prop throughout (Base UI), not `asChild` (Radix UI)

**Spell UI** — uses Motion v12 for animation, pnpm, both Base UI and Radix UI (we use Base UI only)  
**Selia** — unique layout primitives (`Stack`, `Heading`, `Text`, `Chip`), oklch token system  
**Pure UI / Kamui** — copy-paste, polished Base UI animations

---

## 2. Confirmed Technology Stack

| Decision | Choice | Status |
|---|---|---|
| **Base primitive** | `@base-ui/react` | ✅ Confirmed |
| **Styling** | Tailwind CSS v4 | ✅ Confirmed |
| **Animation** | `motion/react` (Motion v12) | ✅ Confirmed |
| **Icon library** | Hugeicons (`@hugeicons/react`) | ✅ Confirmed |
| **Form library** | `react-hook-form` | ✅ Confirmed |
| **Package manager** | pnpm | ✅ Confirmed |
| **Linter** | Biome | ✅ Confirmed |
| **Formatter** | Biome | ✅ Confirmed |
| **Monorepo** | No | ✅ Confirmed |
| **Docs framework** | Fumadocs | ✅ Confirmed |
| **Registry format** | shadcn registry (`shadcn build`) | ✅ Confirmed |
| **License** | MIT | ✅ Confirmed |
| **3D / Canvas** | No | ✅ Confirmed |
| **Framework** | Next.js (App Router) | ✅ Confirmed |
| **Deployment** | Vercel | ✅ Confirmed |
| **Themes** | `next-themes` (Light + Dark) | ✅ Confirmed |
| **RTL support** | Yes — shadcn native RTL (logical classes) | ✅ Confirmed |
| **Rich text / MDX in components** | No — React children only | ✅ Confirmed |
| **Development method** | Spec-driven + Agent Skills + rules | ✅ Confirmed |
| **Fonts** | Geist Sans, Geist Mono, Geist Pixel | ✅ Confirmed |
| **Project bootstrap** | `shadcn init` preset | ✅ Confirmed |
| **Target audience** | Public open-source, enterprise-scalable | ✅ Confirmed |
| **Component strategy** | Inspect reference libs → combine best → own version | ✅ Confirmed |
| **Library name** | **Dusk** (`dusk-ui.vercel.app`) | ✅ Confirmed |
| **Brand color** | **Orange** — `oklch(0.68 0.18 55)` primary | ✅ Confirmed |

---

## 3. Library Name — Dusk ✅

**Chosen name: Dusk**  
Deployment URL: `dusk-ui.vercel.app`  
Rationale: Evokes the warmth of orange light at the edge of day — matches the orange brand color perfectly. One syllable, easy to say, impossible to confuse with any existing UI library. Not AI-sounding, not generic.

---

## 4. Component Build Strategy

### Philosophy
Components are **not invented from scratch**. The process is:
1. **Inspect** — study how the same component is built in coss ui, Selia, Pure UI, and Spell UI
2. **Compare** — identify what each does best (API design, animation, accessibility, visual quality)
3. **Synthesize** — build our own version that combines the best of all four
4. **Own** — the result is fully ours: readable, typed, documented, accessible

### Build Order

**Tier 1 — Atoms (start here):**  
Button, Badge, Input, Textarea, Checkbox, Radio, Switch, Separator, Avatar, Spinner, Skeleton, Label, Kbd

**Tier 2 — Molecules (once atoms are stable):**  
Select, Combobox, Dialog, Alert Dialog, Popover, Tooltip, Drawer, Toast, Tabs, Accordion, Collapsible, Slider, Number Field, Progress, Meter

**Tier 3 — Organisms (requires molecules):**  
Form, Field, Table, Navigation Menu, Menubar, Toolbar, Pagination, Command, Scroll Area, Preview Card

### Two-Tier Deliverable System

| Tier | Name | Description | Examples |
|---|---|---|---|
| **1** | **Primitives** | Single-purpose, composable base components | Button, Input, Dialog, Select |
| **2** | **Particles** | Pre-built compositions of primitives | Login form, Data table, Settings page, Sidebar nav |

---

## 5. Form Library: react-hook-form ✅

**Confirmed:** `react-hook-form` + Zod for the Particles tier only.  
Primitive components (`Input`, `Checkbox`, etc.) remain fully decoupled — no form library dependency.

```bash
pnpm add react-hook-form @hookform/resolvers zod
```

---

## 6. Rich Text / Markdown in Components

**Decision: No.** Components accept React children only. No `remark`, `rehype`, or `react-markdown` dependencies.

---

## 7. RTL Support ✅

**Confirmed:** shadcn native RTL. Write physical classes in source (`left-*`, `pl-*`). CLI transforms at install time. Add `--rtl` flag to bootstrap.

```css
@variant dark (&:where(.dark, .dark *));
```

Known bug: `tw-animate-css` logical slides — pass `dir` prop to portal elements directly when RTL active.

---

## 8. Target Audience & Scale

**Confirmed:** Public open-source, enterprise-scalable from day one. Strict semver, WCAG 2.1 AA, tree-shakeable, full TypeScript strict mode, `CHANGELOG.md` from first release.

---

## 9. Color Palette — Orange ✅

**Confirmed:** Orange primary. Selia's `oklch()` token architecture adopted with hue shifted to ~55.

| Aspect | Selia (reference) | Dusk (ours) |
|---|---|---|
| Color space | `oklch()` | `oklch()` |
| Primary hue | 262 (blue-indigo) | **55 (orange)** |
| Neutral base | Blue-tinted gray | Warm-tinted gray |
| Component sub-tokens | ✅ | ✅ adopted |
| Separate dark token set | ✅ | ✅ |
| Semantic states | ✅ | ✅ |

---

## 10. Confirmed: Next.js (App Router)

Fumadocs, shadcn presets, next-themes, and Vercel are all Next.js App Router-native. Zero-config deployment.

```css
/* globals.css — Tailwind v4 + next-themes dark mode alignment */
@variant dark (&:where(.dark, .dark *));
```

---

## 11. Bootstrap Command

```bash
pnpm dlx shadcn@latest init --preset aH32 --base base --template next --rtl
```

**Manual additions after bootstrap:**
```bash
pnpm add motion
pnpm add next-themes
pnpm add fumadocs-core fumadocs-mdx fumadocs-ui
pnpm add @hugeicons/react @hugeicons/core-free-icons
pnpm add react-hook-form @hookform/resolvers zod
pnpm add -D @biomejs/biome
pnpm biome init
```

---

## 12. Project Structure

```
dusk-ui/
├── app/
│   ├── (docs)/
│   │   ├── layout.tsx
│   │   └── [[...slug]]/
│   │       └── page.tsx
│   ├── (home)/
│   │   └── page.tsx
│   ├── layout.tsx
│   ├── not-found.tsx
│   └── globals.css
├── components/
│   ├── ui/                         # Distributable UI components
│   ├── docs/                       # Docs-site-specific components
│   └── theme-toggle.tsx
├── registry/
│   ├── registry-ui.ts
│   ├── registry-styles.ts
│   ├── registry-hooks.ts
│   ├── registry-lib.ts
│   └── default/ui/
├── content/docs/
├── lib/
│   ├── utils.ts
│   └── source.ts
├── hooks/
├── public/r/
├── registry.json
├── components.json
├── biome.json
├── next.config.ts
├── postcss.config.mjs
├── tsconfig.json
├── AGENTS.md
└── package.json
```

**Key rules:** `app/` is routing-only. All logic, hooks, utils, and components live outside `app/`. `(docs)` and `(home)` route groups have separate layouts. `public/r/` serves the built registry.

---

## 13. Icon Library: Hugeicons

```bash
pnpm add @hugeicons/react @hugeicons/core-free-icons
# ❌ NEVER: hugeicons-react (deprecated)
```

```tsx
import { HugeiconsIcon } from '@hugeicons/react';
import { SearchIcon } from '@hugeicons/core-free-icons';

<HugeiconsIcon icon={SearchIcon} size={20} color="currentColor" strokeWidth={1.5} />
```

Sizes: `16` (sm), `20` (default), `24` (grid), `32` (lg). Always `color="currentColor"`.

---

## 14. Base UI: Composition & Customization Rules

```tsx
// ✅ render prop
<Menu.Trigger render={<MyButton size="md" />}>Open</Menu.Trigger>

// ✅ State-dependent rendering
<Switch.Thumb render={(props, state) => <span {...props}>{state.checked ? <On /> : <Off />}</span>} />

// ✅ eventDetails
<Tooltip.Root onOpenChange={(open, { reason, cancel }) => { if (reason === 'trigger-press') cancel(); }} />

// ✅ Prevent Base UI handler
<NumberField.Input onPaste={(e) => { e.preventBaseUIHandler(); }} />
```

---

## 15. Base UI: Full Component Index (v1.2.0)

| Component | Docs | Component | Docs |
|---|---|---|---|
| Accordion | [↗](https://base-ui.com/react/components/accordion) | Progress | [↗](https://base-ui.com/react/components/progress) |
| Alert Dialog | [↗](https://base-ui.com/react/components/alert-dialog) | Radio | [↗](https://base-ui.com/react/components/radio) |
| Autocomplete | [↗](https://base-ui.com/react/components/autocomplete) | Scroll Area | [↗](https://base-ui.com/react/components/scroll-area) |
| Avatar | [↗](https://base-ui.com/react/components/avatar) | Select | [↗](https://base-ui.com/react/components/select) |
| Button | [↗](https://base-ui.com/react/components/button) | Separator | [↗](https://base-ui.com/react/components/separator) |
| Checkbox | [↗](https://base-ui.com/react/components/checkbox) | Slider | [↗](https://base-ui.com/react/components/slider) |
| Checkbox Group | [↗](https://base-ui.com/react/components/checkbox-group) | Switch | [↗](https://base-ui.com/react/components/switch) |
| Collapsible | [↗](https://base-ui.com/react/components/collapsible) | Tabs | [↗](https://base-ui.com/react/components/tabs) |
| Combobox | [↗](https://base-ui.com/react/components/combobox) | Toast | [↗](https://base-ui.com/react/components/toast) |
| Context Menu | [↗](https://base-ui.com/react/components/context-menu) | Toggle | [↗](https://base-ui.com/react/components/toggle) |
| Dialog | [↗](https://base-ui.com/react/components/dialog) | Toggle Group | [↗](https://base-ui.com/react/components/toggle-group) |
| Drawer | [↗](https://base-ui.com/react/components/drawer) | Toolbar | [↗](https://base-ui.com/react/components/toolbar) |
| Field | [↗](https://base-ui.com/react/components/field) | Tooltip | [↗](https://base-ui.com/react/components/tooltip) |
| Fieldset | [↗](https://base-ui.com/react/components/fieldset) | Navigation Menu | [↗](https://base-ui.com/react/components/navigation-menu) |
| Form | [↗](https://base-ui.com/react/components/form) | Number Field | [↗](https://base-ui.com/react/components/number-field) |
| Input | [↗](https://base-ui.com/react/components/input) | Menubar | [↗](https://base-ui.com/react/components/menubar) |
| Menu | [↗](https://base-ui.com/react/components/menu) | Meter | [↗](https://base-ui.com/react/components/meter) |
| Popover | [↗](https://base-ui.com/react/components/popover) | Preview Card | [↗](https://base-ui.com/react/components/preview-card) |

**Utilities:** `CspProvider`, `DirectionProvider`, `mergeProps`, `useRender`

> **Rule:** Before building any component, verify it exists above.

---

## 16. AGENTS.md Rules

### File & Naming
- Components in `components/ui/` — one file per component
- Named exports only — no default exports
- Colocate types with components

### `"use client"` Rules
- Add only for: hooks, state, event handlers, browser APIs
- Never add for stateless components

### Import Rules
- Icons: `@hugeicons/core-free-icons` only — never `hugeicons-react`
- Icon wrapper: `HugeiconsIcon` from `@hugeicons/react`
- Never `import * as React`
- Base UI: named imports from `@base-ui/react`
- Utilities: `@/lib/utils`
- Forms: Particles tier only — never in primitives

### Styling Rules
- Semantic tokens only — never raw Tailwind colors (`text-gray-500` ❌)
- Never `border-border` — it is the default
- `in-[[data-slot=button]:hover]:` over `group`/`group-hover:`
- `!` suffix for Tailwind important overrides

### RTL Rules
- Physical classes in source. Never `ps-*`/`pe-*` manually.
- Icons needing RTL flip: `rtl:rotate-180`
- Portal elements (`Dialog`, `Popover`, `Drawer`, `Toast`) always get `dir` prop

### Build Workflow
```bash
pnpm biome check --write .
pnpm shadcn build
```

---

## 17. Design Token System

```css
:root {
  /* Surfaces */
  --background; --foreground;
  --border; --muted; --muted-foreground; --accent;

  /* Brand */
  --primary; --primary-foreground; --primary-border;
  --secondary; --secondary-foreground; --secondary-border;

  /* Semantic states */
  --danger  --danger-foreground  --danger-border
  --warning --warning-foreground --warning-border
  --success --success-foreground --success-border
  --info    --info-foreground    --info-border

  /* Component sub-tokens */
  --card  --card-border  --card-footer  --card-shadow
  --popover  --popover-border  --popover-shadow  --popover-accent
  --dialog  --dialog-border  --dialog-footer
  --input  --input-border  --input-accent-border  --input-shadow
  --tabs  --tabs-border  --tabs-accent
  --toast  --toast-border

  /* Radius */
  --radius: 0.75rem;
  --radius-xs: 5px;
  --radius-sm: calc(var(--radius) - 0.25rem);
  --radius-md: var(--radius);
  --radius-lg: calc(var(--radius) + 0.25rem);
  --radius-xl: calc(var(--radius) + 0.5rem);

  /* Typography */
  --font-sans; --font-mono; --font-display;

  /* Shadows */
  --shadow; --shadow-card; --shadow-input; --shadow-popover;
}
```

---

## 18. Cross-Library Synthesis: What We Steal

This section captures the final analysis from studying coss ui's source code and Pure UI's architecture. These patterns are **implementation rules**, not inspiration.

### From coss ui

**`useRender` + `mergeProps` — Polymorphism without `asChild`**  
Every interactive component accepts a `render` prop via Base UI's `useRender`. Callers can pass `render={<a />}` or `render={<NextLink />}` without any extra wrapper.

**`data-slot` — Structural Styling API**  
Every compound component sub-part gets `data-slot="[part-name]"`. This lets parent components target children from outside:
```tsx
// Parent styles child without arbitrary class names:
"[&_[data-slot=toast-icon]]:text-danger"
```
Apply `data-slot` to every compound component part.

**Touch Target Safety**  
Buttons get invisible 44px minimum tap area on touch devices via `pointer-coarse:`:
```tsx
"pointer-coarse:after:absolute pointer-coarse:after:size-full pointer-coarse:after:min-h-11 pointer-coarse:after:min-w-11"
```

**`color-mix()` Dark Mode Tokens**  
Dark backgrounds computed via:
```css
--background: color-mix(in srgb, var(--color-neutral-950) 96%, var(--color-white));
```
No hardcoded hex in dark mode — computed blends.

**Inset Shadow Press Feedback**  
```tsx
"not-disabled:inset-shadow-[0_1px_--theme(--color-white/16%)]"  // rest
"[:active]:inset-shadow-[0_1px_--theme(--color-black/8%)]"      // pressed
```
Physical press feel without JS.

**Pure CSS Toast Animation**  
Toast stack behavior via `data-` attributes and CSS transforms only — no JS position tracking:
```tsx
"data-starting-style:transform-[translateY(calc(-100%-var(--toast-inset)))]"
"data-ending-style:opacity-0"
"[transition:transform_.5s_cubic-bezier(.22,1,.36,1),opacity_.5s]"
```

**Additional Components from coss ui to Adopt**  

| Component | Why |
|---|---|
| `command.tsx` | ⌘K command palette — power-user essential |
| `autocomplete.tsx` | Full async combobox with filtering |
| `number-field.tsx` | Accessible increment/decrement via Base UI |
| `empty.tsx` | Empty state — critical for dashboards |
| `input-group.tsx` | Prefix/suffix icons, addon layouts |
| `toolbar.tsx` | Keyboard-navigable Base UI toolbar |
| `sidebar.tsx` | Full layout system with mobile Sheet fallback |
| `kbd.tsx` | Keyboard shortcut display primitive |
| `meter.tsx` | Distinct from progress — known-range value |
| `preview-card.tsx` | Link hover preview |
| `frame.tsx` | Layout framing primitive |

### From Pure UI

**Typed Event Bus (`core/events`)**  
Pure UI has a `core/events` architecture enabling cross-component communication without Provider drilling. Toast and Dialog become imperatively triggerable from anywhere:
```tsx
// Instead of: <ToastProvider><App /></ToastProvider>
// Users can just: toast.show({ message: 'Saved!' })
```

**Animation Layer Rule (The Three-Layer Principle)**  
- **CSS `data-starting-style` / `data-ending-style`** → Enter/exit transitions (Base UI built-in, zero JS)
- **Tailwind transitions** → Micro-interactions (hover, focus, active)
- **`motion/react`** → Physics-based only: spring animations, drag-to-dismiss, `<motion.div layout>` reflows

Never use `motion/react` for simple enter/exit — CSS handles it. `motion/react` is reserved for what CSS cannot do.

---

## 19. Next Steps (Ordered)

- [ ] Bootstrap: `pnpm dlx shadcn@latest init --preset aH32 --base base --template next --rtl`
- [ ] Add manual dependencies (§11)
- [ ] Configure `biome.json`
- [ ] Configure Tailwind v4 dark mode in `globals.css`
- [ ] Set up `next-themes` `ThemeProvider` + `DirectionProvider` in `app/layout.tsx`
- [ ] Set up Geist fonts
- [ ] Set up Fumadocs: `lib/source.ts`, `next.config.ts`, `(docs)` route group
- [ ] Define full CSS token system in `globals.css` (§17) with orange oklch primary
- [ ] Write `AGENTS.md`
- [ ] Build first component: **Button** (establishes all patterns)
- [ ] Registry workflow scripts
- [ ] First doc page: `content/docs/button.mdx`
