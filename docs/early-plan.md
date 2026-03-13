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
- `AGENTS.md` (37KB) — LLM-targeted coding guide (all patterns adopted, see §8)
- Biome for lint + format, Turborepo + Bun
- `render` prop throughout (Base UI), not `asChild` (Radix UI)

**Spell UI** — uses Motion v12 for animation, pnpm, both Base UI and Radix UI (we use Base UI only)  
**Selia** — unique layout primitives (`Stack`, `Heading`, `Text`, `Chip`)  
**Pure UI / Kamui** — copy-paste, polished Base UI animations

---

## 2. Confirmed Technology Stack

| Decision | Choice | Status |
|---|---|---|
| **Base primitive** | `@base-ui/react` | ✅ Confirmed |
| **Styling** | Tailwind CSS v4 | ✅ Confirmed |
| **Animation** | `motion/react` (Motion v12) | ✅ Confirmed |
| **Icon library** | Hugeicons (`@hugeicons/react`) | ✅ Confirmed |
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
| **Development method** | Spec-driven + Agent Skills + rules | ✅ Confirmed |
| **Fonts** | Geist Sans, Geist Mono, Geist Pixel | ✅ Confirmed |
| **Project bootstrap** | `shadcn init` preset | ✅ Confirmed |

### Why Biome for Everything

Oxfmt is not production-ready as of March 2026. Biome handles both linting (~300+ rules) and formatting in a single Rust-based tool. One config file (`biome.json`), one command (`biome check --write .`), no conflicts between two tools. Same setup used by coss ui.

---

## 3. Confirmed: Next.js (App Router)

**Primary reasons:**
- Fumadocs is Next.js App Router-native — using it with any other framework requires major workarounds
- shadcn `init` presets and `components.json` are Next.js-flavored
- `next-themes` is zero-config with Next.js
- Vercel deployment is zero-config
- `"use client"` discipline from coss ui's AGENTS.md maps directly to App Router conventions
- Server Components keep docs pages static with no client bundle overhead

**Important note on Tailwind v4 + next-themes:**  
Tailwind v4 uses CSS-native `@variant dark` by default. `next-themes` toggles a `.dark` class on `<html>`. These two must be explicitly aligned. In `globals.css`, configure:

```css
@variant dark (&:where(.dark, .dark *));
```

This tells Tailwind v4 to apply dark styles when any ancestor has `.dark`, which matches what `next-themes` sets.

---

## 4. Confirmed: Bootstrap Command

```bash
pnpm dlx shadcn@latest init --preset aH32 --base base --template next
```

**What this automatically sets up:**
- Next.js (latest) app with App Router
- Base UI as the primitive library
- Radix UI (also installed by the preset — we will not use it directly)
- `components.json` configured with `"base-nova"` style (Base UI + Nova spacing/radius system)
- Any future `shadcn add [component]` will install using Base UI + Nova

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

# Linter + Formatter
pnpm add -D @biomejs/biome
pnpm biome init
```

---

## 5. Project Structure (Confirmed + Enhanced)

Based on Next.js App Router official project structure conventions ([reference](https://nextjs.org/docs/app/getting-started/project-structure)), the recommended structure is:

```
your-ui-library/
├── app/                            # Next.js App Router (routing only, no logic here)
│   ├── (docs)/                     # Route group: docs site (URL omits "docs")
│   │   ├── layout.tsx              # Docs layout (sidebar, TOC)
│   │   └── [[...slug]]/
│   │       └── page.tsx            # Dynamic MDX page route
│   ├── (home)/                     # Route group: marketing/home
│   │   └── page.tsx                # Landing page
│   ├── layout.tsx                  # Root layout: ThemeProvider + Geist fonts + metadata
│   ├── not-found.tsx
│   └── globals.css                 # Tailwind v4 @import, CSS custom properties, dark variant
├── components/                     # Shared docs-site components (not distributable)
│   ├── ui/                         # Distributable UI components (copied into user projects)
│   ├── docs/                       # Docs-site-specific components (nav, header, TOC, etc.)
│   └── theme-toggle.tsx
├── registry/                       # shadcn registry source
│   ├── registry-ui.ts              # Component definitions + metadata + dependencies
│   ├── registry-styles.ts          # CSS variable themes (light/dark design tokens)
│   ├── registry-hooks.ts           # Custom hook registry entries
│   ├── registry-lib.ts             # Utility function registry entries
│   └── default/
│       └── ui/                     # Actual component .tsx source files
├── content/                        # Fumadocs MDX content
│   └── docs/                       # One .mdx per component + handbook pages
├── lib/
│   ├── utils.ts                    # cn() utility (clsx + tailwind-merge)
│   └── source.ts                   # Fumadocs content source config
├── hooks/                          # Shared hooks (e.g. useMediaQuery, useDebounce)
├── public/
│   └── r/                          # Built registry JSON served by Vercel
├── registry.json                   # Built registry output (generated, do not edit manually)
├── components.json                 # shadcn config (primitive: base-ui, style: nova)
├── biome.json                      # Biome lint + format config
├── next.config.ts                  # with Fumadocs MDX plugin
├── postcss.config.mjs              # @tailwindcss/postcss
├── tsconfig.json                   # strict: true, path alias @/*
├── AGENTS.md                       # LLM coding guide (to be written, modeled after coss ui)
└── package.json
```

### Key Structure Decisions (from Next.js docs)

- **Route groups `(group)`** — Separate `(docs)` and `(home)` without polluting URLs. Each has its own layout.
- **`app/` is routing-only** — All logic, components, hooks, and utils live outside `app/` in top-level folders.
- **`components/ui/`** — Distributable component files, separate from registry source.
- **`content/docs/`** — Fumadocs MDX source files, resolved via `lib/source.ts`.
- **`public/r/`** — Built registry served as static JSON from Vercel.

---

## 6. Icon Library: Hugeicons

*Docs: [hugeicons.com/docs/integrations/react/quick-start](https://hugeicons.com/docs/integrations/react/quick-start)*

### Why Hugeicons
- 4,600+ free stroke-rounded icons in the free tier, 46,000+ across 10 styles in Pro
- Pixel-perfect on a consistent 24x24 grid
- Fully tree-shakeable — only imported icons land in the bundle
- Full TypeScript support
- Supports `altIcon` prop for state-toggle animations (e.g. Sun ↔ Moon)
- Has an MCP server for AI-assisted icon discovery

### ⚠️ Deprecated Package — Do Not Use

```bash
# ❌ DEPRECATED — do not install
npm install hugeicons-react
```

The old `hugeicons-react` package is no longer maintained. Always use the new split packages.

### Correct Installation

```bash
pnpm add @hugeicons/react @hugeicons/core-free-icons
```

Two packages are always required:
- `@hugeicons/react` — the React wrapper (`HugeiconsIcon` component)
- `@hugeicons/core-free-icons` — the free icon assets (4,600+ stroke-rounded icons)

### Usage Pattern

```tsx
import { HugeiconsIcon } from '@hugeicons/react';
import { SearchIcon, HomeIcon } from '@hugeicons/core-free-icons';

// Basic usage
<HugeiconsIcon icon={SearchIcon} />

// With explicit size and color
<HugeiconsIcon icon={HomeIcon} size={20} color="currentColor" strokeWidth={1.5} />

// State-toggle with altIcon (e.g. theme toggle)
import { SunIcon, MoonIcon } from '@hugeicons/core-free-icons';
<HugeiconsIcon icon={SunIcon} altIcon={MoonIcon} showAlt={isDark} />
```

### Icon Sizing Rules (Tailwind Integration)

Unlike `lucide-react` (which uses `className="size-4"`), Hugeicons uses a **`size` prop** on the `HugeiconsIcon` wrapper, not Tailwind classes on the SVG directly.

```tsx
// ✅ Correct — use size prop
<HugeiconsIcon icon={SearchIcon} size={16} color="currentColor" />
<HugeiconsIcon icon={SearchIcon} size={20} color="currentColor" />

// ❌ Do not try to size with Tailwind className on HugeiconsIcon
<HugeiconsIcon icon={SearchIcon} className="size-4" /> // won't work as expected
```

**Standard size values:**
- `size={16}` — small (equivalent to `size-4`)
- `size={20}` — default UI size (equivalent to `size-5`)
- `size={24}` — default Hugeicons grid size
- `size={32}` — large / feature icons

**Always use `color="currentColor"`** so icons inherit text color and respond to dark mode automatically.

### Accessibility Rules for Hugeicons

- Decorative icons: always add `aria-hidden="true"` on the wrapper element or its parent
- Semantic icons (e.g. alert severity): do NOT hide from screen readers
- Icon-only buttons: `aria-label` on the `<button>`, `aria-hidden="true"` on the icon

```tsx
// ✅ Decorative icon in a button with text
<button>
  <span aria-hidden="true">
    <HugeiconsIcon icon={PlusIcon} size={16} color="currentColor" />
  </span>
  Add item
</button>

// ✅ Icon-only button
<button aria-label="Close">
  <HugeiconsIcon icon={XmarkIcon} size={16} color="currentColor" aria-hidden="true" />
</button>
```

### Import Rules (AGENTS.md)

- Always named imports from `@hugeicons/core-free-icons`
- Never import the entire package (`import * from '@hugeicons/core-free-icons'`)
- Always pair with the `HugeiconsIcon` wrapper from `@hugeicons/react`
- Do not use the deprecated `hugeicons-react` package under any circumstances

---

## 7. Base UI: Composition & Customization Rules

*Source: [base-ui.com/react/handbook/composition](https://base-ui.com/react/handbook/composition) and [base-ui.com/react/handbook/customization](https://base-ui.com/react/handbook/customization)*

### The `render` Prop (Not `asChild`)

Base UI uses a `render` prop — not Radix UI's `asChild`. This is the single most important API difference to internalize.

```tsx
// ✅ Base UI: render prop
<Menu.Trigger render={<MyButton size="md" />}>
  Open menu
</Menu.Trigger>

// ❌ Radix UI: asChild (do not use)
<Menu.Trigger asChild>
  <MyButton size="md">Open menu</MyButton>
</Menu.Trigger>
```

The custom component passed to `render` **must** forward its `ref` and spread all received props onto its underlying DOM node.

### Changing the Rendered Element

```tsx
// Render a Menu.Item as an anchor link
<Menu.Item render={<a href="/about" />}>
  About
</Menu.Item>
```

### Render Function (State-Dependent Rendering)

```tsx
<Switch.Thumb
  render={(props, state) =>
    <span {...props}>
      {state.checked ? <CheckedIcon /> : <UncheckedIcon />}
    </span>
  }
/>
```

### Base UI Event System

```tsx
onOpenChange: (open: boolean, eventDetails: BaseUIChangeEventDetails) => void
```

The `eventDetails` object exposes:
- `reason` — why the change occurred (e.g. `'trigger-press'`, `'escape-key'`)
- `event` — the native DOM event
- `cancel()` — prevent internal state update
- `allowPropagation()` — allow DOM event to propagate

### Preventing Base UI from Handling React Events

```tsx
<NumberField.Input
  onPaste={(event) => {
    event.preventBaseUIHandler();
  }}
/>
```

---

## 8. Base UI: Full Component Index

*Source: [base-ui.com/llms.txt](https://base-ui.com/llms.txt) — ground truth for all component availability checks.*

**Latest stable release:** v1.2.0 (Feb 12, 2026)

### Available Components (37 total)

| Component | Docs |
|---|---|
| Accordion | [accordion.md](https://base-ui.com/react/components/accordion.md) |
| Alert Dialog | [alert-dialog.md](https://base-ui.com/react/components/alert-dialog.md) |
| Autocomplete | [autocomplete.md](https://base-ui.com/react/components/autocomplete.md) |
| Avatar | [avatar.md](https://base-ui.com/react/components/avatar.md) |
| Button | [button.md](https://base-ui.com/react/components/button.md) |
| Checkbox | [checkbox.md](https://base-ui.com/react/components/checkbox.md) |
| Checkbox Group | [checkbox-group.md](https://base-ui.com/react/components/checkbox-group.md) |
| Collapsible | [collapsible.md](https://base-ui.com/react/components/collapsible.md) |
| Combobox | [combobox.md](https://base-ui.com/react/components/combobox.md) |
| Context Menu | [context-menu.md](https://base-ui.com/react/components/context-menu.md) |
| Dialog | [dialog.md](https://base-ui.com/react/components/dialog.md) |
| Drawer | [drawer.md](https://base-ui.com/react/components/drawer.md) |
| Field | [field.md](https://base-ui.com/react/components/field.md) |
| Fieldset | [fieldset.md](https://base-ui.com/react/components/fieldset.md) |
| Form | [form.md](https://base-ui.com/react/components/form.md) |
| Input | [input.md](https://base-ui.com/react/components/input.md) |
| Menu | [menu.md](https://base-ui.com/react/components/menu.md) |
| Menubar | [menubar.md](https://base-ui.com/react/components/menubar.md) |
| Meter | [meter.md](https://base-ui.com/react/components/meter.md) |
| Navigation Menu | [navigation-menu.md](https://base-ui.com/react/components/navigation-menu.md) |
| Number Field | [number-field.md](https://base-ui.com/react/components/number-field.md) |
| Popover | [popover.md](https://base-ui.com/react/components/popover.md) |
| Preview Card | [preview-card.md](https://base-ui.com/react/components/preview-card.md) |
| Progress | [progress.md](https://base-ui.com/react/components/progress.md) |
| Radio | [radio.md](https://base-ui.com/react/components/radio.md) |
| Scroll Area | [scroll-area.md](https://base-ui.com/react/components/scroll-area.md) |
| Select | [select.md](https://base-ui.com/react/components/select.md) |
| Separator | [separator.md](https://base-ui.com/react/components/separator.md) |
| Slider | [slider.md](https://base-ui.com/react/components/slider.md) |
| Switch | [switch.md](https://base-ui.com/react/components/switch.md) |
| Tabs | [tabs.md](https://base-ui.com/react/components/tabs.md) |
| Toast | [toast.md](https://base-ui.com/react/components/toast.md) |
| Toggle | [toggle.md](https://base-ui.com/react/components/toggle.md) |
| Toggle Group | [toggle-group.md](https://base-ui.com/react/components/toggle-group.md) |
| Toolbar | [toolbar.md](https://base-ui.com/react/components/toolbar.md) |
| Tooltip | [tooltip.md](https://base-ui.com/react/components/tooltip.md) |

### Available Utilities

| Utility | Docs |
|---|---|
| CSP Provider | [csp-provider.md](https://base-ui.com/react/utils/csp-provider.md) |
| Direction Provider | [direction-provider.md](https://base-ui.com/react/utils/direction-provider.md) |
| `mergeProps` | [merge-props.md](https://base-ui.com/react/utils/merge-props.md) |
| `useRender` | [use-render.md](https://base-ui.com/react/utils/use-render.md) |

> **Rule:** Before building any component, verify it exists in the table above. If a primitive is not listed, it does not exist in Base UI and must be built from scratch or sourced differently.

### Handbook References

- [Styling](https://base-ui.com/react/handbook/styling.md)
- [Animation](https://base-ui.com/react/handbook/animation.md)
- [Composition](https://base-ui.com/react/handbook/composition.md)
- [Customization](https://base-ui.com/react/handbook/customization.md)
- [Forms](https://base-ui.com/react/handbook/forms.md)
- [TypeScript](https://base-ui.com/react/handbook/typescript.md)

---

## 9. AGENTS.md Rules (To Be Written)

An `AGENTS.md` file must be created at the repo root. It will be modeled after coss ui's guide.

### File & Naming
- All distributable components live in `components/ui/`
- One file per component; named after the component: `button.tsx`, `dialog.tsx`
- Named exports only — no default exports for components
- Colocate types with components

### `"use client"` Rules
- Add **only** when using React hooks, state, event handlers that call `setState`, or browser APIs
- Never add for stateless components
- Never add as a default — it must be justified

### Import Rules
- Icons: named imports from `@hugeicons/core-free-icons` only — never `import *`
- Icon wrapper: always `HugeiconsIcon` from `@hugeicons/react`
- Never use the deprecated `hugeicons-react` package
- React hooks: named imports — never `import * as React`
- Base UI: named imports from `@base-ui/react/{component}`
- Utilities: always from `@/lib/utils`

### Composition Rules (Base UI)
- Always use `render` prop — never `asChild`
- Custom components passed to `render` must forward `ref` and spread all props
- Use render function `(props, state) => JSX` for state-dependent rendering
- Use `onOpenChange`/`onValueChange` signatures (not standard DOM events)
- Use `eventDetails.cancel()` as an alternative to full controlled mode
- Use `event.preventBaseUIHandler()` to prevent Base UI from handling React events

### Icon Rules
- Always use `size` prop on `HugeiconsIcon` — do not size via Tailwind className
- Always use `color="currentColor"` for dark mode compatibility
- Decorative icons: wrap in `<span aria-hidden="true">` or pass `aria-hidden` directly
- Icon-only buttons: `aria-label` on the button, icon is decorative
- Semantic icons (alerts): do NOT hide from screen readers

### Styling Rules
- Use semantic color tokens only — never raw Tailwind colors
- Never write `border-border` — it is the default
- Use `in-[[data-slot=button]:hover]:` pattern instead of `group`/`group-hover:`
- Use `!` suffix for Tailwind important overrides
- Dark mode via `@variant dark (&:where(.dark, .dark *))` in globals.css

### Accessibility Rules
- Every interactive component must have correct ARIA roles, states, properties
- Full keyboard navigation: Tab, Arrow keys, Escape, Enter/Space
- `aria-label` for icon-only interactive elements
- Alert icons are semantic — do NOT hide from screen readers
- Always specify `type` on every `<Input>` (even `type="text"`)
- Prefer `aria-label` over `sr-only` text

### Component API Rules
- Expose `className` and `style` on every component
- Prefer explicit over clever: `variant="destructive"` over `danger={true}`
- Compound components + render props for complex components
- React Context within compound components — no prop drilling
- No runtime dependencies beyond React and Base UI

### Build Workflow
```bash
pnpm biome check --write .          # format + lint fix
pnpm shadcn build                   # generate registry.json + public/r/
```

---

## 10. Design Token System

All colors, spacing, radius, and shadow values will use CSS custom properties.

```css
/* Colors */
--color-background
--color-foreground
--color-primary
--color-primary-foreground
--color-secondary
--color-secondary-foreground
--color-muted
--color-muted-foreground
--color-destructive
--color-destructive-foreground
--color-border
--color-ring

/* Radius */
--radius-sm
--radius-md
--radius-lg
--radius-xl

/* Typography */
--font-sans     /* Geist Sans */
--font-mono     /* Geist Mono */
--font-display  /* Geist Pixel — display/headings only */
```

Light/dark values set inside `@variant dark { ... }` in `globals.css`, toggled by `next-themes` via `.dark` on `<html>`.

---

## 11. Next Steps (Ordered)

- [ ] Run bootstrap: `pnpm dlx shadcn@latest init --preset aH32 --base base --template next`
- [ ] Add manual dependencies: `motion`, `next-themes`, `fumadocs-core`, `fumadocs-mdx`, `fumadocs-ui`, `@hugeicons/react`, `@hugeicons/core-free-icons`, `@biomejs/biome`
- [ ] Configure `biome.json`
- [ ] Configure Tailwind v4 dark mode: `@variant dark (&:where(.dark, .dark *))` in `globals.css`
- [ ] Set up `next-themes` `ThemeProvider` in `app/layout.tsx`
- [ ] Set up Geist fonts
- [ ] Set up Fumadocs: `lib/source.ts`, `next.config.ts` MDX plugin, `(docs)` route group
- [ ] Define CSS design token system in `globals.css`
- [ ] Write `AGENTS.md` at repo root
- [ ] Build first component: **Button**
- [ ] Set up registry workflow scripts
- [ ] Write first doc page: `content/docs/button.mdx`
