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
| **Component naming** | TBD (see §3) | 🔲 Pending |
| **Brand color** | TBD — Blue or Orange (see §13) | 🔲 Pending |

### Why Biome for Everything

Oxfmt is not production-ready as of March 2026. Biome handles both linting (~300+ rules) and formatting in a single Rust-based tool. One config file (`biome.json`), one command (`biome check --write .`), no conflicts between two tools. Same setup used by coss ui.

---

## 3. Library Name — TBD 🔲

The library does not have a name yet. Below are naming criteria and a working shortlist.

### Criteria
- Not an existing Vercel deployment (`.vercel.app` subdomain or custom domain)
- Not an existing npm package or GitHub org
- Easily remembered — 1–2 syllables preferred
- Has personality — feels crafted, not AI-generated
- Not generic or overused (no "ui", "kit", "craft", "design" suffixes)
- Works as a domain: `[name].com` or `[name].dev` ideally available
- Can stand alone — no need to explain what it is from the name

### Candidate Names

Each name is provided with a rationale and a vibe check:

| Name | Vibe | Notes |
|---|---|---|
| **Flint** | Sharp, reliable, minimal | Primitive material → sparks things to life. Short, memorable, no obvious clashes. |
| **Hew** | Handcrafted, deliberate | "To hew" = shape carefully. 3 letters, highly unique, unusual for a UI lib. |
| **Luma** | Bright, refined, accessible | Light/clarity metaphor. Slightly more common but still clean. |
| **Prim** | Playful yet precise | Short for "primitive" — directly references the Base UI foundation. Self-aware naming. |
| **Croft** | Grounded, artisanal | A croft = a small crafted plot. Handmade feel. Uncommon in the ecosystem. |
| **Fern** | Organic, calm, distinctive | Natural, no tech clichés, memorable, pairs well with a green accent if desired. |
| **Keel** | Structural, foundational | A keel is the spine of a ship. Strong metaphor for a primitive component foundation. |
| **Opal** | Iridescent, premium | Gem name — implies multiple color palettes/themes. Works well with blue or orange. |
| **Sable** | Dark, sophisticated | Strong dark-mode-first identity. A bit niche. |
| **Pave** | Constructive, systematic | "To pave the way" — building blocks metaphor. Distinct from anything current. |

> **Note:** None of these were verified against Vercel/npm/GitHub exhaustively at the time of writing. Final choice requires availability check across: Vercel subdomain, npm (`@[name]/react` or `[name]-ui`), GitHub org/repo, and domain registrar.

### Decision Required
**Mohamed must choose or propose a name before the bootstrap step.** The name affects:
- The npm package scope: `@[name]/react` or `[name]-ui`
- The Vercel deployment URL: `[name].vercel.app`
- The GitHub repo name and org (if creating one)
- The `components.json` `name` field
- The docs site URL and SEO

---

## 4. Component Build Strategy

### Philosophy
Components are **not invented from scratch**. The process is:
1. **Inspect** — study how the same component is built in coss ui, Selia, Pure UI, and Spell UI
2. **Compare** — identify what each does best (API design, animation, accessibility, visual quality)
3. **Synthesize** — build our own version that combines the best of all four
4. **Own** — the result is fully ours: readable, typed, documented, accessible

This is the same spirit as coss ui's copy-paste model: you understand every line because you participated in crafting it.

### Build Order
Start with atomic/primitive components and build upward:

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

Particles come later, after primitives are stable and battle-tested. No particle can depend on an unstable primitive.

---

## 5. Form Library: react-hook-form ✅

**Confirmed:** `react-hook-form` is the form state management library.

### Rationale
- Industry standard — highest adoption in the React ecosystem
- Used by shadcn/ui, coss ui, and most production Next.js apps
- Uncontrolled by default (minimal re-renders)
- Excellent TypeScript + Zod integration via `@hookform/resolvers`
- Pairs naturally with Base UI's `Field`, `Fieldset`, and `Form` primitives

### Standard Stack
```bash
pnpm add react-hook-form @hookform/resolvers zod
```

### Form Component Pattern
The `Form` particle will wrap Base UI's form primitives with `react-hook-form` context:
```tsx
// Users of the particle-tier Form component:
<Form onSubmit={handleSubmit}>
  <Field.Root name="email">
    <Field.Label>Email</Field.Label>
    <Input type="email" {...register('email')} />
    <Field.Error />
  </Field.Root>
  <Button type="submit">Submit</Button>
</Form>
```

At the primitive tier, `Input`, `Field`, `Checkbox`, etc. are **not** coupled to react-hook-form — they are plain components. The form integration lives in the Particles tier only.

---

## 6. Rich Text / Markdown in Components

**Decision: No.** Components accept React children only. No component will internally parse or render Markdown/MDX.

- If a component needs rich formatting, it accepts `ReactNode` children
- MDX rendering is handled by Fumadocs at the docs level, not by UI components
- No `remark`, `rehype`, or `react-markdown` dependencies in the component layer

This keeps the component bundle zero-dependency beyond React and Base UI.

---

## 7. RTL Support ✅

**Confirmed:** Full RTL support, using the **shadcn native RTL approach** — no manual `ps`/`pe` noise.

*Source: [ui.shadcn.com/docs/rtl](https://ui.shadcn.com/docs/rtl) and [ui.shadcn.com/docs/rtl/next](https://ui.shadcn.com/docs/rtl/next)*

### How It Works

When `rtl: true` is set in `components.json`, the shadcn CLI **automatically transforms** physical positioning classes to logical equivalents at install time:
- `left-*` / `right-*` → `start-*` / `end-*`
- `pl-*` / `pr-*` → `ps-*` / `pe-*`
- Directional animation classes (e.g. `slide-in-from-right` → `slide-in-from-end`)
- Supported icons are auto-flipped with `rtl:rotate-180`

This means **we write components using physical classes** (`left-*`, `pl-*`). The CLI handles the logical transformation when users install via `shadcn add`. No manual `ps`/`pe` in source code.

> ⚠️ Only available for `base-nova` and `radix-nova` styles (our preset is `base-nova`) — confirmed compatible.

### Bootstrap with RTL
Add `--rtl` to the init command:
```bash
pnpm dlx shadcn@latest init --preset aH32 --base base --template next --rtl
```
This sets `"rtl": true` in `components.json` automatically.

### DirectionProvider in Root Layout
```tsx
// app/layout.tsx
import { DirectionProvider } from '@/components/ui/direction';

export default function RootLayout({ children }) {
  return (
    <html lang="en" dir="ltr">
      <body>
        <DirectionProvider direction="ltr">
          {children}
        </DirectionProvider>
      </body>
    </html>
  );
}
```

For Arabic/RTL usage, change `lang="ar"` and `direction="rtl"`. Noto fonts are recommended for Arabic/Hebrew RTL text alongside Geist.

### Known Issue
`tw-animate-css` logical slide utilities have a bug. Workaround: pass `dir` prop directly to portal elements (Dialog, Popover, etc.) when using RTL.

### AGENTS.md RTL Rules
- Write all source components using physical classes (`left-*`, `right-*`, `pl-*`, `pr-*`)
- **Never** write `ps-*`/`pe-*` manually in source — the CLI handles the transformation
- Icons that need flipping in RTL: add `rtl:rotate-180` class
- Always pass `dir` prop to portal elements when RTL is active (Dialog, Popover, Drawer, Toast)

---

## 8. Target Audience & Scale

**Confirmed:** Public open-source library, designed to be enterprise-scalable from day one.

### What This Means in Practice

| Concern | Decision |
|---|---|
| **API stability** | Semver strictly — no breaking changes in minor/patch releases |
| **TypeScript** | Strict mode always; all public props fully typed and documented |
| **Accessibility** | WCAG 2.1 AA minimum — non-negotiable, not a future enhancement |
| **Theming** | CSS custom properties only — works in any environment, no JS required at runtime |
| **Bundle size** | Tree-shakeable exports; each component importable independently |
| **Versioning** | `CHANGELOG.md` maintained from the first release |
| **Contributing** | `CONTRIBUTING.md` written before the first public announcement |
| **Documentation** | Every component fully documented before being marked stable |
| **Deprecation policy** | Deprecated APIs announced with migration guides, removed only in major versions |

### Enterprise-Readiness Checklist (to address over time)
- [ ] Keyboard navigation on every interactive component
- [ ] ARIA authoring practices compliance
- [ ] High-contrast mode testing
- [ ] Reduced-motion support (`@media (prefers-reduced-motion)`)
- [ ] Print stylesheet compatibility for relevant components
- [ ] CSP compatibility (Base UI's `CspProvider` — already in §8)
- [ ] RTL out of the box (§7)

---

## 9. Color Palette — TBD 🔲

No final palette decided. Brand color is either **Blue** or **Orange**. Analysis below.

### Selia's Token System (Reference)

*Source: [nauvalazhar/selia — selia.css](https://github.com/nauvalazhar/selia/blob/master/app/selia.css)*

Selia uses `oklch()` color space throughout — the correct modern approach for perceptually uniform color. Key observations:

- **Primary** is a medium blue: `oklch(0.5784 0.2057 262.95)` (hue 262 = blue-indigo)
- **Neutral base** is a blue-tinted gray (hue ~248) — not pure gray, giving it a cool, slightly tech feel
- **Component-specific tokens** for each surface type: `--card`, `--card-border`, `--card-footer`, `--card-shadow`, `--popover`, `--popover-shadow`, `--dialog`, `--tabs`, `--track`, etc.
- **Semantic states** are complete: `--danger`, `--warning`, `--success`, `--info` — all with `*-foreground` and `*-border` variants
- **Dark mode** is a completely separate token set (not just `opacity` or `invert`) — all values carefully re-tuned for dark
- **Typography scale** is compact: base is `0.875rem` (14px), not 16px — a deliberate product-UI choice

### coss ui's Approach (Reference)

*Source: [coss.com/ui/docs/styling](https://coss.com/ui/docs/styling)*

- Uses **opaque borders** that mix with shadows to create visual depth — different from Selia's flat-border approach
- Extends shadcn's token set with `--info`, `--info-foreground`, `--success`, `--success-foreground`, `--warning`, `--warning-foreground`
- Requires `isolation: isolate` on the root wrapper for portal z-index correctness with Base UI
- Requires `position: relative` on `body` for iOS Safari 26+ backdrop compatibility

### What We Will Adopt

Regardless of brand color, the following are confirmed for the token system:

- ✅ `oklch()` color space for all color tokens (like Selia)
- ✅ Component-specific sub-tokens (like Selia): `--card-*`, `--popover-*`, `--dialog-*`, `--tabs-*`, `--input-*`
- ✅ Full semantic state set: `--danger`, `--warning`, `--success`, `--info` + foreground + border variants (like both)
- ✅ Completely separate dark mode token set — no shortcuts
- ✅ `isolation: isolate` on root wrapper (coss ui requirement for Base UI portals)
- ✅ `position: relative` on `body` for iOS Safari 26+ (coss ui)
- ✅ Compact type scale (base 14px like Selia) for product/app UI feel

### Blue vs. Orange — The Decision

Both are strong choices for a developer-facing UI library. The table below helps frame the decision:

| | **Blue** | **Orange** |
|---|---|---|
| **Personality** | Trustworthy, technical, precise | Energetic, bold, memorable |
| **Ecosystem fit** | Dominant — many UI libs use blue | Rare — almost no major UI lib uses orange as primary |
| **Dark mode look** | Excellent — blue reads well on dark surfaces | Excellent — warm on dark creates striking contrast |
| **Selia primary** | `oklch(0.5784 0.2057 262.95)` — blue-indigo | N/A |
| **Orange equivalent** | N/A | ~`oklch(0.68 0.18 55)` (amber-orange) |
| **Differentiation** | Lower — blends with Selia, coss ui, shadcn | **Higher** — stands out in the ecosystem |
| **Enterprise perception** | Standard, expected | Unconventional but confident |

> **Recommendation:** If the goal is maximum differentiation and personality, **Orange** is the bolder choice. Blue is safer and more familiar. Both work well with a neutral gray base. This decision can be deferred until the first theming implementation — the token system works identically regardless of which hue is chosen for `--primary`.

---

## 10. Confirmed: Next.js (App Router)

**Primary reasons:**
- Fumadocs is Next.js App Router-native
- shadcn `init` presets and `components.json` are Next.js-flavored
- `next-themes` is zero-config with Next.js
- Vercel deployment is zero-config
- Server Components keep docs pages static with no client bundle overhead

**Important note on Tailwind v4 + next-themes:**
```css
/* globals.css */
@variant dark (&:where(.dark, .dark *));
```

---

## 11. Confirmed: Bootstrap Command

```bash
pnpm dlx shadcn@latest init --preset aH32 --base base --template next --rtl
```

**What this automatically sets up:**
- Next.js (latest) app with App Router
- Base UI as the primitive library
- Radix UI (also installed by the preset — we will not use it directly)
- `components.json` configured with `"base-nova"` + `"rtl": true`

**What must be added manually after bootstrap:**

```bash
# Animation
pnpm add motion

# Themes
pnpm add next-themes

# Docs framework
pnpm add fumadocs-core fumadocs-mdx fumadocs-ui

# Icon library (two packages required)
pnpm add @hugeicons/react @hugeicons/core-free-icons

# Form library
pnpm add react-hook-form @hookform/resolvers zod

# Linter + Formatter
pnpm add -D @biomejs/biome
pnpm biome init
```

---

## 12. Project Structure (Confirmed + Enhanced)

```
[library-name]/
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

**Key rules:** `app/` is routing-only. All logic lives outside. `(docs)` and `(home)` route groups have separate layouts. `public/r/` serves the built registry as static JSON.

---

## 13. Icon Library: Hugeicons

*Docs: [hugeicons.com/docs/integrations/react/quick-start](https://hugeicons.com/docs/integrations/react/quick-start)*

### Correct Installation

```bash
pnpm add @hugeicons/react @hugeicons/core-free-icons
# ❌ NEVER: npm install hugeicons-react  (deprecated)
```

### Usage Pattern

```tsx
import { HugeiconsIcon } from '@hugeicons/react';
import { SearchIcon, SunIcon, MoonIcon } from '@hugeicons/core-free-icons';

<HugeiconsIcon icon={SearchIcon} size={20} color="currentColor" strokeWidth={1.5} />
<HugeiconsIcon icon={SunIcon} altIcon={MoonIcon} showAlt={isDark} size={20} color="currentColor" />
```

**Sizing:** Always via `size` prop (not Tailwind `className`). Standard values: `16` (sm), `20` (default), `24` (grid), `32` (lg). Always `color="currentColor"`.

**A11y:** Decorative icons → `aria-hidden="true"`. Icon-only buttons → `aria-label` on `<button>`. Alert/severity icons → never hidden.

---

## 14. Base UI: Composition & Customization Rules

*Source: [base-ui.com/react/handbook/composition](https://base-ui.com/react/handbook/composition)*

### The `render` Prop (Not `asChild`)

```tsx
// ✅ Base UI
<Menu.Trigger render={<MyButton size="md" />}>Open menu</Menu.Trigger>

// ❌ Radix UI pattern — never use
<Menu.Trigger asChild><MyButton>Open menu</MyButton></Menu.Trigger>
```

### Render Function for State-Dependent Output

```tsx
<Switch.Thumb
  render={(props, state) =>
    <span {...props}>{state.checked ? <On /> : <Off />}</span>
  }
/>
```

### Event System

```tsx
<Tooltip.Root
  onOpenChange={(open, { reason, cancel }) => {
    if (reason === 'trigger-press') cancel();
  }}
/>
```

`eventDetails`: `reason`, `event`, `cancel()`, `allowPropagation()`. Use `cancel()` instead of full controlled mode for simple cases.

### Prevent Base UI Handling

```tsx
<NumberField.Input onPaste={(e) => { e.preventBaseUIHandler(); }} />
```

---

## 15. Base UI: Full Component Index

*Source: [base-ui.com/llms.txt](https://base-ui.com/llms.txt) — v1.2.0 (Feb 12, 2026)*

| Component | Docs | Component | Docs |
|---|---|---|---|
| Accordion | [↗](https://base-ui.com/react/components/accordion.md) | Progress | [↗](https://base-ui.com/react/components/progress.md) |
| Alert Dialog | [↗](https://base-ui.com/react/components/alert-dialog.md) | Radio | [↗](https://base-ui.com/react/components/radio.md) |
| Autocomplete | [↗](https://base-ui.com/react/components/autocomplete.md) | Scroll Area | [↗](https://base-ui.com/react/components/scroll-area.md) |
| Avatar | [↗](https://base-ui.com/react/components/avatar.md) | Select | [↗](https://base-ui.com/react/components/select.md) |
| Button | [↗](https://base-ui.com/react/components/button.md) | Separator | [↗](https://base-ui.com/react/components/separator.md) |
| Checkbox | [↗](https://base-ui.com/react/components/checkbox.md) | Slider | [↗](https://base-ui.com/react/components/slider.md) |
| Checkbox Group | [↗](https://base-ui.com/react/components/checkbox-group.md) | Switch | [↗](https://base-ui.com/react/components/switch.md) |
| Collapsible | [↗](https://base-ui.com/react/components/collapsible.md) | Tabs | [↗](https://base-ui.com/react/components/tabs.md) |
| Combobox | [↗](https://base-ui.com/react/components/combobox.md) | Toast | [↗](https://base-ui.com/react/components/toast.md) |
| Context Menu | [↗](https://base-ui.com/react/components/context-menu.md) | Toggle | [↗](https://base-ui.com/react/components/toggle.md) |
| Dialog | [↗](https://base-ui.com/react/components/dialog.md) | Toggle Group | [↗](https://base-ui.com/react/components/toggle-group.md) |
| Drawer | [↗](https://base-ui.com/react/components/drawer.md) | Toolbar | [↗](https://base-ui.com/react/components/toolbar.md) |
| Field | [↗](https://base-ui.com/react/components/field.md) | Tooltip | [↗](https://base-ui.com/react/components/tooltip.md) |
| Fieldset | [↗](https://base-ui.com/react/components/fieldset.md) | Navigation Menu | [↗](https://base-ui.com/react/components/navigation-menu.md) |
| Form | [↗](https://base-ui.com/react/components/form.md) | Number Field | [↗](https://base-ui.com/react/components/number-field.md) |
| Input | [↗](https://base-ui.com/react/components/input.md) | Menubar | [↗](https://base-ui.com/react/components/menubar.md) |
| Menu | [↗](https://base-ui.com/react/components/menu.md) | Meter | [↗](https://base-ui.com/react/components/meter.md) |
| Popover | [↗](https://base-ui.com/react/components/popover.md) | Preview Card | [↗](https://base-ui.com/react/components/preview-card.md) |

**Utilities:** `CspProvider`, `DirectionProvider`, `mergeProps`, `useRender`

> **Rule:** Before building any component, verify it exists above. If not listed, it does not exist in Base UI — build from scratch or source differently.

---

## 16. AGENTS.md Rules (To Be Written)

### File & Naming
- All distributable components live in `components/ui/`
- One file per component: `button.tsx`, `dialog.tsx`
- Named exports only — no default exports
- Colocate types with components

### `"use client"` Rules
- Add only for: hooks, state, event handlers calling `setState`, browser APIs
- Never add for stateless components
- Never as a default — must be justified

### Import Rules
- Icons: named imports from `@hugeicons/core-free-icons` only
- Icon wrapper: `HugeiconsIcon` from `@hugeicons/react`
- Never use `hugeicons-react` (deprecated)
- React hooks: named imports — never `import * as React`
- Base UI: named imports from `@base-ui/react/{component}`
- Utilities: from `@/lib/utils`
- Forms: `react-hook-form` + `zod` in Particles tier only — never in primitive components

### RTL Rules
- Write physical classes in source (`left-*`, `pl-*`, `right-*`, `pr-*`)
- Never write `ps-*`/`pe-*` manually — the CLI transforms at install time
- Icons needing flip: `rtl:rotate-180` class
- Always pass `dir` prop to portal elements (Dialog, Popover, Drawer, Toast) when RTL active

### Composition Rules (Base UI)
- Always `render` prop — never `asChild`
- Custom components for `render` must forward `ref` and spread all props
- `(props, state) => JSX` for state-dependent rendering
- `eventDetails.cancel()` over full controlled mode for simple cases
- `event.preventBaseUIHandler()` to block Base UI from a React event

### Icon Rules
- `size` prop only — never size via Tailwind `className`
- Always `color="currentColor"`
- Decorative: `aria-hidden="true"`. Icon-only button: `aria-label` on button. Alert icons: never hidden.

### Styling Rules
- Semantic tokens only — never raw Tailwind colors (`text-gray-500` ❌)
- Never `border-border` — it is the default
- `in-[[data-slot=button]:hover]:` over `group`/`group-hover:`
- `!` suffix for Tailwind important overrides
- Dark mode via `@variant dark (&:where(.dark, .dark *))`

### Static Data
- Define static arrays/objects outside the component function, never inside

### Build Workflow
```bash
pnpm biome check --write .   # lint + format
pnpm shadcn build            # registry.json + public/r/
```

---

## 17. Design Token System

Based on analysis of Selia and coss ui. Uses `oklch()` throughout.

```css
:root {
  /* Base surfaces */
  --background
  --foreground
  --border
  --muted
  --muted-foreground
  --accent          /* hover/active surface */

  /* Brand */
  --primary
  --primary-foreground
  --primary-border
  --secondary
  --secondary-foreground
  --secondary-border

  /* Semantic states */
  --danger  --danger-foreground  --danger-border
  --warning --warning-foreground --warning-border
  --success --success-foreground --success-border
  --info    --info-foreground    --info-border

  /* Component-specific sub-tokens (from Selia pattern) */
  --card  --card-border  --card-footer  --card-shadow
  --popover  --popover-border  --popover-shadow  --popover-accent
  --dialog  --dialog-border  --dialog-footer
  --input  --input-border  --input-accent-border  --input-shadow
  --tabs  --tabs-border  --tabs-accent
  --toast  --toast-border

  /* Radius (relative to --radius base) */
  --radius: 0.75rem;
  --radius-xs: 5px;
  --radius-sm: calc(var(--radius) - 0.25rem);
  --radius-md: var(--radius);
  --radius-lg: calc(var(--radius) + 0.25rem);
  --radius-xl: calc(var(--radius) + 0.5rem);

  /* Typography */
  --font-sans      /* Geist Sans */
  --font-mono      /* Geist Mono */
  --font-display   /* Geist Pixel — display/headings only */

  /* Shadows */
  --shadow
  --shadow-card
  --shadow-input
  --shadow-popover
}
```

**Root isolation (required for Base UI portals):**
```css
.root { isolation: isolate; }
body { position: relative; } /* iOS Safari 26+ */
```

---

## 18. Next Steps (Ordered)

- [ ] **Decide library name** — choose from shortlist in §3 or propose new; verify availability
- [ ] **Decide brand color** — Blue or Orange (see §9)
- [ ] Run bootstrap: `pnpm dlx shadcn@latest init --preset aH32 --base base --template next --rtl`
- [ ] Add manual dependencies: `motion`, `next-themes`, `fumadocs-*`, `@hugeicons/*`, `react-hook-form`, `@hookform/resolvers`, `zod`, `@biomejs/biome`
- [ ] Configure `biome.json`
- [ ] Configure Tailwind v4 dark mode in `globals.css`
- [ ] Set up `next-themes` `ThemeProvider` + `DirectionProvider` in `app/layout.tsx`
- [ ] Set up Geist fonts
- [ ] Set up Fumadocs: `lib/source.ts`, `next.config.ts`, `(docs)` route group
- [ ] Define full CSS token system in `globals.css` (§17)
- [ ] Write `AGENTS.md`
- [ ] Build first component: **Button** (establishes all patterns)
- [ ] Registry workflow scripts
- [ ] First doc page: `content/docs/button.mdx`
