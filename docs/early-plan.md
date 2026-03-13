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

### Key Learnings From the Study

**coss ui** is the most architecturally mature reference:
- Turborepo + Bun monorepo with `apps/ui/`, `apps/origin/` (legacy), `packages/ui/`
- Uses `@base-ui/react ^1.3.0` + Tailwind v4 + Fumadocs for docs
- Biome for linting/formatting (not ESLint/Prettier)
- 51 confirmed components in `registry-ui.ts`, plus a large `registry-particles.ts` (Particles layer)
- `AGENTS.md` (37KB) — a detailed LLM-targeted coding guide covering: file naming, accessibility rules, Tailwind patterns, Base UI API differences (`render` prop vs `asChild`), size mappings (shadcn → coss), build workflow
- Sidebar is the most complex component (8 peer dependencies)
- Calendar is the only component NOT using Base UI (uses `react-day-picker`)

**Pure UI / Kamui** — copy-paste model, Base UI + polished animations, shadcn registry format  
**Selia** — opinionated by default, React Router v7, unique layout primitives (`Stack`, `Heading`, `Text`, `Chip`)  
**Spell UI** — uses BOTH Base UI AND Radix UI, Motion v12 animations, Three.js for 3D effects, pnpm

---

## 2. Confirmed Technology Preferences

| Decision | Choice | Notes |
|---|---|---|
| **Base primitive** | Base UI (`@base-ui/react`) | Headless, accessible, modern |
| **Styling** | Tailwind CSS v4 | Build-time only, no runtime CSS-in-JS |
| **Animation** | `motion/react` (Motion v12) | Named import, not `framer-motion` |
| **Package manager** | pnpm | |
| **Linter** | Oxlint | Rust-based, extremely fast |
| **Formatter** | dprint (via Oxfmt) | See pushback note §4.1 |
| **Monorepo** | No | Single Next.js or Vite app |
| **Docs framework** | Fumadocs | MDX-based, used by coss ui |
| **Registry format** | shadcn registry (`shadcn build`) | Industry standard for copy-paste libs |
| **License** | MIT | |
| **3D / Canvas** | No | Intentionally excluded |
| **Framework** | TBD — Next.js or Vite | See decision §3 |
| **Deployment** | Vercel | |
| **Themes** | `next-themes` | Light + Dark |
| **Development method** | Spec-driven + Agent Skills + rules | |
| **Fonts** | Geist Sans, Geist Mono, Geist Pixel | From [vercel.com/font](https://vercel.com/font) |
| **Project bootstrap** | `shadcn init` preset | e.g. `pnpm dlx shadcn@latest init --preset aH32 --base base --template next` |

---

## 3. Open Decision: Next.js vs Vite

This is the most impactful unresolved decision. Both are valid but serve different strengths.

### Arguments for Next.js

- **Fumadocs is Next.js-native.** Fumadocs is the chosen docs framework and it is built specifically for Next.js App Router. Using it with Vite would require significant workaround or a different docs solution.
- **coss ui uses Next.js** (`next@16.0.9`). You studied it extensively and its patterns (e.g. `"use client"` placement, App Router conventions) will transfer directly.
- **Vercel deployment** is zero-config for Next.js. Fumadocs + Vercel + Next.js is a proven, documented stack.
- **shadcn `init` presets** are Next.js-flavored — the bootstrap command you specified targets Next.js.
- **Server Components** allow you to keep demo pages static without client bundles, which improves docs site performance.

### Arguments for Vite

- **Faster dev server.** Vite's HMR is faster than Next.js's for pure component development in isolation.
- **Framework-agnostic components.** If you later want to distribute components that work in non-Next.js projects, Vite-built components are more portable.
- **Simpler mental model.** No App Router, no Server/Client component boundary rules to manage.

### Recommendation (open for discussion)

**Use Next.js.** The Fumadocs dependency alone makes this the correct choice — it is not realistically portable to Vite without replacing the docs framework. Every other piece of the chosen stack (shadcn preset, Vercel, next-themes, the `"use client"` discipline from coss ui's AGENTS.md) aligns with Next.js. Vite would make sense if docs were a separate repo, but since you want a single app, Next.js is the answer.

> **Action needed from Mohamed:** Confirm Next.js or override with reasoning.

---

## 4. Pushback & Discussion Notes

### 4.1 — Oxfmt Does Not Exist (Yet)

⚠️ **Oxfmt is not a released, production-ready formatter as of March 2026.** The Oxc project (which created Oxlint) is working on a formatter, but it is not stable. Using it in production tooling would introduce risk.

**Recommendation:** Use **Biome** as your formatter alongside Oxlint for linting. This is a well-established split:
- Oxlint handles linting (it is 50–100x faster than ESLint for lint rules)
- Biome handles formatting (it is the formatter used by coss ui)

Alternatively, use **Biome for both linting and formatting** (as coss ui does) and skip Oxlint entirely. Biome's linter now covers ~300+ rules and is production-ready.

> **Action needed from Mohamed:** Decide between (A) Oxlint + Biome, or (B) Biome for everything, or (C) wait for Oxfmt to stabilize.

### 4.2 — `motion/react` is the Correct Import (Confirmed)

You specified `motion/react` — this is correct. In Motion v11+, the package was renamed from `framer-motion` to `motion`, and the React-specific import is `motion/react`. Spell UI uses `motion@^12.23.12`. This aligns with your preference. No issue here.

### 4.3 — `next-themes` + Tailwind v4 Requires Care

Tailwind v4 moved from a `class`-based dark mode to a CSS-native approach using `@variant dark { ... }`. `next-themes` by default toggles a `dark` class on `<html>`. You will need to configure Tailwind v4 to use `selector` strategy (i.e. `.dark` class) to be compatible with `next-themes`. This is a known integration point that requires explicit configuration and should be part of the setup spec.

### 4.4 — No Monorepo is Fine for Now

A single app is the right call at this stage. The decision can be revisited once the component count grows past ~60 and you want to publish to npm. At that point, a minimal Turborepo setup (identical to coss ui's structure) would make sense. The registry format and `packages/ui/` sync pattern from coss ui provides a clear migration path.

### 4.5 — Geist Pixel is Experimental

Geist Pixel is part of Vercel's Geist font family but is a display/experimental typeface. It should be used sparingly (headings, branding) and not as body text. Geist Sans for UI, Geist Mono for code blocks, Geist Pixel for decorative/display use only.

---

## 5. Proposed Architecture

```
your-ui-library/
├── app/                        # Next.js App Router
│   ├── (docs)/                 # Fumadocs documentation routes
│   └── layout.tsx              # Root layout with ThemeProvider + Geist fonts
├── components/
│   └── ui/                     # The actual UI components (copied into projects)
├── registry/
│   ├── registry-ui.ts          # Component registry definitions
│   ├── registry-styles.ts      # CSS variable themes (light/dark tokens)
│   └── default/
│       └── ui/                 # Component source files
├── content/
│   └── docs/                   # MDX documentation pages
├── lib/
│   └── utils.ts                # cn() and other utilities
├── hooks/                      # Shared hooks (e.g. useMediaQuery)
├── public/
│   └── r/                      # Built registry JSON (served by Vercel)
├── registry.json               # Built registry output
├── components.json             # shadcn config
├── oxlint.json                 # Oxlint config
├── biome.json                  # Biome formatter config (if chosen)
├── next.config.ts
├── tailwind.config.ts          # (Tailwind v4 uses CSS @import, may not be needed)
├── postcss.config.mjs
├── tsconfig.json
└── package.json
```

---

## 6. Bootstrap Command

```bash
pnpm dlx shadcn@latest init --preset aH32 --base base --template next
```

Post-init setup steps:
1. Install Base UI: `pnpm add @base-ui/react`
2. Install Motion: `pnpm add motion`
3. Install Fumadocs: `pnpm add fumadocs-core fumadocs-mdx`
4. Install next-themes: `pnpm add next-themes`
5. Install Oxlint: `pnpm add -D oxlint`
6. Configure Tailwind v4 dark mode selector strategy
7. Add Geist font family (available via `next/font/local` or `geist` npm package)
8. Configure `components.json` for shadcn registry

---

## 7. Next Steps

- [ ] Confirm: Next.js or Vite?
- [ ] Confirm: Oxlint + Biome, or Biome-only, or wait for Oxfmt?
- [ ] Run bootstrap command and verify scaffolded output
- [ ] Set up Fumadocs with first documentation page
- [ ] Define design token system (CSS custom properties: `--ui-color-*`, `--ui-spacing-*`, `--ui-radius-*`)
- [ ] Write `AGENTS.md` (modeled after coss ui's guide) for autonomous component creation
- [ ] Build first component: `Button` (simplest, establishes all patterns)
- [ ] Define component API conventions (props interface, variants, `className` exposure)
- [ ] Write registry workflow scripts

---

## 8. Reference: Key Patterns From coss ui (To Adopt)

These patterns from coss ui's `AGENTS.md` are recommended for adoption verbatim:

- **`render` prop instead of `asChild`** — Base UI's pattern, not Radix
- **Semantic color tokens only** — never raw Tailwind colors in components
- **`in-[[data-slot=button]:hover]:` over `group`/`group-hover:`** — parent-aware hover
- **`aria-label` over `sr-only` text** — for icon-only interactive elements
- **`type` attribute required on every `Input`** — even `type="text"`
- **`GroupSeparator` always required between controls in `Group`**
- **Static data defined outside the component function** — performance
- **`"use client"` only when actually needed** — never by default
- **Named imports from `lucide-react`** — never `import *`
