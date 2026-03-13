# Dusk UI — Final Plan

> All decisions locked. This is the source of truth.
> Date: March 13, 2026

---

## Identity

| Key | Value |
|---|---|
| **Name** | Dusk UI |
| **URL** | `https://dusk-ui.vercel.app` |
| **Registry base** | `https://dusk-ui.vercel.app/r/{name}.json` |
| **GitHub** | `https://github.com/<your-username>/dusk-ui` |
| **Model** | Fork of Pure UI — incremental rebrand + evolution |
| **Distribution** | shadcn CLI copy-paste (`registry.json`) |

---

## Locked Stack

| Layer | Choice | Notes |
|---|---|---|
| Framework | Next.js 16, App Router, Turbopack | Inherited from Pure UI |
| React | 19.x | Concurrent, Actions, compiler |
| Primitives | Base UI 1.0.0 | Stable release |
| Styling | Tailwind CSS v4 | `@import "tailwindcss"`, no config file |
| Variant util | `tailwind-variants` (`tv()`) | Slots + compound variants |
| Animation (components) | CSS `data-starting-style` / `data-ending-style` | Zero JS, Base UI lifecycle |
| Animation (demos) | Motion v12 (`motion/react`) | Doc site + blocks only |
| Linter + Formatter | Biome 2.x | Replaces ESLint + Prettier |
| TypeScript | 5.9, strict | |
| Package manager | pnpm | |
| Node | 22.x enforced | `engines` field in `package.json` |
| Color system | OKLCH | Perceptually uniform |
| Color scheme | Monochrome (for now) | No brand accent color yet |
| Icon set | `@tabler/icons-react` | Keep Pure UI's choice |
| Heading font | Chillax | Keep Pure UI's choice |
| Code font | Noto Mono | Keep Pure UI's choice |
| Docs framework | `next-mdx-remote` | Keep Pure UI's approach, full control |
| Analytics | Umami | Replace OpenPanel + Wandry |
| Toast state | Zustand imperative | `toast.add(...)` outside React tree |
| Deployment | Vercel | No Docker, no VPS |
| React compiler | `babel-plugin-react-compiler` | Auto-memoization |

---

## Phase 1 — Fork & Rebrand

> Goal: Zero traces of Pure UI, Krishna, or MusKRI. Clean build on Vercel.

### 1.1 — Fork & Clone

```bash
# Fork MusKRI/pure-ui on GitHub
# Rename the fork to "dusk-ui"
git clone https://github.com/<your-username>/dusk-ui
cd dusk-ui
```

---

### 1.2 — Delete Infrastructure Files

Pure UI deploys via Docker on a VPS. Dusk UI deploys to Vercel. Delete everything Docker-related:

```bash
rm Dockerfile docker-compose.yml proxy.ts .env.example
rm -rf .github
```

| Deleted | Reason |
|---|---|
| `Dockerfile` | Docker VPS deploy — not needed on Vercel |
| `docker-compose.yml` | Same |
| `proxy.ts` | Local reverse proxy for the Docker setup |
| `.github/workflows/deploy.yml` | SSH deploy to Krishna's VPS |
| `.github/` | Whole folder — no CI needed initially |
| `.env.example` | Krishna's OpenPanel analytics key slot |

---

### 1.3 — Rename Registry Folder

```bash
mv src/registry/pure-ui src/registry/dusk-ui
```

This single rename cascades into every import in the codebase. Fix all broken imports in the next step.

---

### 1.4 — Global Find-and-Replace

Run these across the **entire repo** (editor global search or CLI `sed`):

| Find | Replace |
|---|---|
| `@/registry/pure-ui/` | `@/registry/dusk-ui/` |
| `"pure-ui"` | `"dusk-ui"` |
| `"Pure UI"` | `"Dusk UI"` |
| `PureUI` | `DuskUI` |
| `pureUI` | `duskUI` |
| `pure-ui` (non-path kebab) | `dusk-ui` |
| `pure.kam-ui.com` | `dusk-ui.vercel.app` |
| `MusKRI` | `<your-github-username>` |
| `Krishna` | `Mohamed` |
| `Built by Krishna` | `Built by Mohamed` |

After running: do a repo-wide search for `pure` and `Pure` to catch any stragglers.

---

### 1.5 — File-by-File Manual Edits

#### `package.json`
```json
{
  "name": "dusk-ui"
}
```

---

#### `src/app/layout.tsx`
```tsx
export const metadata: Metadata = {
  title: "Dusk UI",
  description: "A modern React UI component library built on Base UI.",
};
```

Also update the `ToastProvider` import path (already handled by global replace in 1.4).

---

#### `src/app/(home)/page.tsx`
```tsx
<RollingText
  text="Dusk"
  speed={0.05}
  duration={3}
  className="text-5xl uppercase sm:text-7xl lg:text-8xl font-semibold"
/>
<RollingText
  text="UI"
  speed={0.05}
  duration={3}
  className="text-5xl uppercase sm:text-7xl lg:text-8xl font-semibold"
/>

<p className="mx-auto max-w-md text-lg text-foreground/80 font-light">
  A modern React component library built on Base UI.
  Beautiful animations. Zero compromise.
</p>
```

---

#### `src/core/components/composed/logo.tsx`
```tsx
// Rename function only — keep SVG for now, replace in Phase 2
export function DuskUILogo(props: SVGProps<SVGSVGElement>) {
  // SVG body unchanged until Phase 2
}
```

---

#### `src/core/components/composed/header/github-button.tsx`
```tsx
// Update fetch URL and Link href:
const data = await fetch(
  "https://api.github.com/repos/<your-username>/dusk-ui",
  { next: { revalidate: 3600 } }
);

<Link
  href="https://github.com/<your-username>/dusk-ui"
  target="_blank"
  rel="noreferrer"
/>
```

---

#### `components.json`
```json
{
  "registries": {
    "@duskui": "https://dusk-ui.vercel.app/r/{name}.json"
  }
}
```

---

#### `src/registry/dusk-ui/registry.ts`
```ts
const DUSK_UI_STYLE = {
  type: "registry:style",
  dependencies: ["lucide-react", "tailwind-variants"],
  devDependencies: ["tw-animate-css"],
  registryDependencies: ["classes"],
  cssVars: {},
  files: [],
};

export const registry = {
  name: "Dusk UI",
  homepage: "https://dusk-ui.vercel.app",
  items: z.array(registryItemSchema).parse([
    { name: "index", ...DUSK_UI_STYLE },
    ...duskUIComponents,
    ...duskUILib,
    ...duskUICompExamples,
    ...duskUIBlocks,
  ]),
} satisfies Registry;
```

---

#### `src/core/events/` — Replace Analytics

Remove `@openpanel/nextjs` and `@wandry/analytics-sdk` from `package.json`.

Install Umami:
```bash
pnpm remove @openpanel/nextjs @wandry/analytics-sdk
```

Replace `src/core/events/openpanel.tsx` with `src/core/events/umami.tsx`:

```tsx
// src/core/events/umami.tsx
export function Analytics() {
  if (process.env.NODE_ENV !== "production") return null;

  return (
    <script
      defer
      src={process.env.NEXT_PUBLIC_UMAMI_SCRIPT_URL}
      data-website-id={process.env.NEXT_PUBLIC_UMAMI_WEBSITE_ID}
    />
  );
}
```

Update `src/app/layout.tsx` to import from the new path:
```tsx
import { Analytics } from "@/core/events/umami";
```

Add to `.env.local` (create this file, do not commit):
```
NEXT_PUBLIC_UMAMI_SCRIPT_URL=https://analytics.umami.is/script.js
NEXT_PUBLIC_UMAMI_WEBSITE_ID=<your-umami-website-id>
```

Add a new `.env.example`:
```
NEXT_PUBLIC_UMAMI_SCRIPT_URL=
NEXT_PUBLIC_UMAMI_WEBSITE_ID=
```

---

#### `src/content/*.mdx` — Installation Commands

Bulk replace across all MDX files:
```
https://pure.kam-ui.com/r/  →  https://dusk-ui.vercel.app/r/
```

---

#### `README.md`

Rewrite entirely:
```md
# Dusk UI

A modern React component library built on Base UI, Tailwind CSS v4, and Motion.
Copy-paste components. No black boxes.

## Getting Started

Visit [dusk-ui.vercel.app](https://dusk-ui.vercel.app)

## Tech Stack

- Next.js 16 + React 19
- Base UI 1.0
- Tailwind CSS v4
- tailwind-variants
- Motion v12
- Biome

## License

MIT
```

---

### 1.6 — Reinstall & Verify

```bash
pnpm install
pnpm build     # must pass with zero errors
pnpm dev       # visual verification
```

**Visual verification checklist:**
- [ ] Browser tab title: "Dusk UI"
- [ ] Home page rolling text: "Dusk" + "UI"
- [ ] Home page tagline is yours
- [ ] Header logo/name shows "Dusk UI"
- [ ] GitHub button links to your repo
- [ ] No "Pure UI", "Krishna", or "MusKRI" visible anywhere in the UI
- [ ] No console errors or warnings
- [ ] `pnpm build` exits 0

---

### 1.7 — Deploy to Vercel

1. Push to GitHub
2. Import repo in Vercel dashboard
3. Add env vars: `NEXT_PUBLIC_UMAMI_SCRIPT_URL` + `NEXT_PUBLIC_UMAMI_WEBSITE_ID`
4. Deploy — site live at `dusk-ui.vercel.app`

---

### Phase 1 Master Checklist

```
[ ] 1.  Fork MusKRI/pure-ui → rename fork to dusk-ui on GitHub
[ ] 2.  Clone locally
[ ] 3.  rm Dockerfile docker-compose.yml proxy.ts .env.example
[ ] 4.  rm -rf .github
[ ] 5.  mv src/registry/pure-ui → src/registry/dusk-ui
[ ] 6.  Global find-replace all brand strings (see 1.4 table)
[ ] 7.  Repo-wide search for "pure"/"Pure" — catch stragglers
[ ] 8.  package.json: name → "dusk-ui"
[ ] 9.  src/app/layout.tsx: title + description
[ ] 10. src/app/(home)/page.tsx: RollingText text + tagline
[ ] 11. logo.tsx: PureUILogo → DuskUILogo
[ ] 12. github-button.tsx: URLs → your repo
[ ] 13. components.json: registry URL → dusk-ui.vercel.app
[ ] 14. src/registry/dusk-ui/registry.ts: name, homepage, import names
[ ] 15. pnpm remove @openpanel/nextjs @wandry/analytics-sdk
[ ] 16. Create src/core/events/umami.tsx
[ ] 17. Update layout.tsx analytics import
[ ] 18. Create .env.example with Umami keys
[ ] 19. src/content/*.mdx: bulk replace install command URLs
[ ] 20. README.md: rewrite
[ ] 21. pnpm install
[ ] 22. pnpm build — must pass
[ ] 23. pnpm dev — visual verification pass
[ ] 24. Push to GitHub
[ ] 25. Deploy to Vercel, add env vars
```

---

## Phase 2 — Visual Identity

> Runs after Phase 1 is deployed and clean. Not a blocker.

| Task | File | Notes |
|---|---|---|
| Logo | `src/core/components/composed/logo.tsx` | Design Dusk UI SVG mark |
| Favicon | `src/app/favicon.ico` | Match new logo |
| og:image | `src/app/opengraph-image.tsx` | Add social share image |
| Dark mode tokens | `src/styles/globals.css` | Finalize `.dark {}` OKLCH values |
| Brand color decision | `src/styles/globals.css` | Currently monochrome; revisit later |
| Tagline refinement | `src/app/(home)/page.tsx` | Polish the hero copy |

---

## Phase 3 — Component Evolution

> Diverging from Pure UI's design decisions, component by component. Build order = highest visibility first.

| Priority | Component | What to Evolve |
|---|---|---|
| 1 | `Button` | Finalize variant/size/radius values for Dusk UI palette |
| 2 | `Dialog` / `Sheet` | Verify all 11 animation presets survive the rename intact |
| 3 | `Accordion` | Verify `swiss` variant CSS `has()` logic intact |
| 4 | `Tabs` | Verify sliding indicator + `ResizeObserver` height animation |
| 5 | `Toast` | Verify Zustand store; update position defaults if needed |
| 6 | `Input` / `InputGroup` | Adjust ring/focus tokens to Dusk UI palette |
| 7 | `Card` | Adjust shadow/border tokens |
| 8 | All others | Incremental polish as needed |

---

## Phase 4 — New Components

> Pure UI does not have these. Add after Phase 3 is stable. Prioritize together.

| Component | Base UI Primitive | Notes |
|---|---|---|
| `Alert` | — | Semantic feedback banner |
| `AlertDialog` | `@base-ui/react/alert-dialog` | Destructive confirmation |
| `Progress` | `@base-ui/react/progress` | Linear progress bar |
| `Meter` | `@base-ui/react/meter` | Value gauge |
| `Skeleton` | — | Loading placeholder |
| `Breadcrumb` | — | Nav trail |
| `Pagination` | — | Page navigation |
| `Table` | — | Data table (styled HTML table) |
| `NavigationMenu` | `@base-ui/react/navigation-menu` | Top-level nav with dropdowns |
| `DatePicker` | `react-day-picker` (already a dep) | Full calendar picker |
| `Slider` | `@base-ui/react/slider` | Range input |
| `Command` | — | Command palette (cmdk-style) |

---

## Analytics Setup (Umami)

Umami is open-source, self-hostable, and GDPR-compliant. Two deployment options:

**Option A — Umami Cloud (easiest):**
1. Create account at https://umami.is
2. Add a new website → get `Website ID` and script URL
3. Add to Vercel env vars

**Option B — Self-hosted:**
1. Deploy Umami on Railway / Supabase / PlanetScale
2. Use your own domain for the script URL

Either way, the `Analytics` component in `src/core/events/umami.tsx` is the same — only the env vars differ.

---

## Registry Distribution

Users install Dusk UI components via:

```bash
# Install a single component
npx shadcn add https://dusk-ui.vercel.app/r/button.json

# Install the full style (tokens + base)
npx shadcn add https://dusk-ui.vercel.app/r/index.json
```

The registry is built automatically before every `next build` via `prebuild` in `package.json`:

```
src/registry/dusk-ui/ui/<component>/index.tsx
        ↓  pnpm build:dusk-ui  (runs build-registry.ts)
src/registry/dusk-ui/__index__.tsx  (AUTO-GENERATED — never edit)
        ↓  pnpm registry:build  (runs shadcn build)
registry.json  (public CLI manifest at repo root)
```

---

## Component Animation API (Reference)

Every animated component exposes these three props:

```tsx
animationPreset?: CSSAnimationPreset   // default: 'fade'
transitionPreset?: CSSTransitionPreset // default: 'outQuad'
reduceMotion?: boolean                 // default: false
```

### Animation Presets (15)

| Preset | What moves |
|---|---|
| `none` | No animation |
| `fade` | opacity + height |
| `scale` | scale + opacity + height (origin-top) |
| `slide` | translateY + opacity |
| `topSlide` | slides down from top |
| `bottomSlide` | slides up from bottom |
| `leftSlide` | slides in from left |
| `rightSlide` | slides in from right |
| `topFlip` | rotateX from top + perspective |
| `bottomFlip` | rotateX from bottom + perspective |
| `leftFlip` | rotateY from left + perspective |
| `rightFlip` | rotateY from right + perspective |
| `wipe` | clip-path curtain reveal |
| `perspective` | rotateX(90deg) + perspective(1000px) |
| `perspectiveBlur` | perspective + blur(9px) |

### Transition Presets (25 named easings)

`inExpo` · `outExpo` · `inOutExpo` · `anticipate` · `quickOut` · `overshootOut` · `swiftOut` · `snappyOut` · `in` · `out` · `inOut` · `outIn` · `inQuad` · `outQuad` · `inOutQuad` · `inCubic` · `outCubic` · `inOutCubic` · `inQuart` · `outQuart` · `inOutQuart` · `inQuint` · `outQuint` · `inOutQuint` · `inCirc` · `outCirc` · `inOutCirc` · `inOutBase`

---

## File Locations (Final)

| What | Where |
|---|---|
| Distributable components | `src/registry/dusk-ui/ui/{component}/index.tsx` |
| Registry manifest (hand-maintained) | `src/registry/dusk-ui/registry.ts` |
| Auto-generated barrel | `src/registry/dusk-ui/__index__.tsx` |
| Public CLI manifest | `registry.json` (repo root) |
| Doc-site components | `src/core/components/` |
| Utilities bundled with components | `src/registry/dusk-ui/lib/classes.ts` |
| App-level utilities | `src/lib/classes.ts` |
| Design tokens | `src/styles/globals.css` |
| Partial CSS | `src/styles/partials/` |
| MDX docs | `src/content/{component}.mdx` |
| Build script | `src/scripts/build-registry.ts` |
| Analytics | `src/core/events/umami.tsx` |
| Hooks (doc site) | `src/core/hooks/` |
| Providers | `src/core/providers/` |

---

## Agent Rules Summary

For any AI agent or contributor working on this codebase:

- Read `spec/agent-context.md` before writing any code
- Never edit `src/registry/dusk-ui/__index__.tsx` — it is auto-generated
- Never use `motion/react` inside `src/registry/dusk-ui/ui/*` — CSS animations only
- Never use CVA — use `tailwind-variants` (`tv()`)
- Never use `export default` for components — named exports only
- Never use Tailwind palette colors (`text-gray-500`) — semantic tokens only
- Always add `data-slot="[part-name]"` to every compound sub-part
- Always define `cssAnimationPresets` and `cssTransitionPresets` as static objects outside the component function
- Always add `will-change` + `transform-gpu` on GPU-animated elements
- Always support `reduceMotion` prop on animated components
