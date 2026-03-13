# Dusk UI — Early Plan v2

> Based on deep analysis of Pure UI ([MusKRI/pure-ui](https://github.com/MusKRI/pure-ui))  
> Approach: **Fork & Rebrand** — not greenfield  
> Status: Draft — awaiting discussion before final plan  
> Date: March 13, 2026

---

## Approach

Dusk UI starts as a fork of Pure UI. The codebase is production-quality, the architecture is already correct, and the animation system is exactly what we want. Instead of rebuilding from scratch, we:

1. Fork the repo
2. Run a full rebrand (Phase 1) — remove all traces of Pure UI, Krishna, and MusKRI
3. Incrementally evolve the library into Dusk UI's own identity

This saves weeks of scaffolding work and lets us focus immediately on design and component additions.

---

## What Changed from v1 Plan

The previous early plan described a **greenfield build** from scratch. That is now obsolete.

| Old Approach | New Approach |
|---|---|
| Create new Next.js app | Fork Pure UI repo |
| Set up Tailwind v4 from scratch | Already configured |
| Build animation system | Already built — adopt it |
| Set up shadcn registry pipeline | Already working |
| 27 components to build | 27 components already exist — evolve them |
| Weeks of scaffolding | Phase 1 = ~1 day of renaming |

---

## Phase 1 — Fork & Rebrand

This phase has one goal: **zero traces of Pure UI remain**. The app must build cleanly and look like Dusk UI from the first commit.

### Step 1 — Fork

```bash
# Fork MusKRI/pure-ui to your GitHub account
# Clone locally
git clone https://github.com/<your-username>/dusk-ui
cd dusk-ui
```

---

### Step 2 — Delete Infrastructure Files

These files are Pure UI's deployment infrastructure (Docker VPS deploy). Dusk UI will deploy to Vercel — none of these are needed.

| File/Folder | Reason to Delete |
|---|---|
| `Dockerfile` | Pure UI deploys via Docker on a VPS |
| `docker-compose.yml` | Same |
| `proxy.ts` | Local proxy for the VPS Docker setup |
| `.github/workflows/deploy.yml` | SSH + `docker compose up` to Krishna's server |
| `.github/` | Delete the entire folder — no workflows needed initially |
| `.env.example` | Contains `NEXT_PUBLIC_OPENPANEL_CLIENT_ID` (Krishna's analytics slot) |

```bash
rm Dockerfile docker-compose.yml proxy.ts .env.example
rm -rf .github
```

---

### Step 3 — Rename the Registry Folder

The entire component library lives under `src/registry/pure-ui/`. Rename it:

```bash
mv src/registry/pure-ui src/registry/dusk-ui
```

This renames the folder but breaks all imports. Fix with a global find-and-replace in the next step.

---

### Step 4 — Global Find-and-Replace (String Level)

Run these replacements across the **entire repo** (use your editor's global search, or `sed`/`grep`):

| Find | Replace | Scope |
|---|---|---|
| `@/registry/pure-ui/` | `@/registry/dusk-ui/` | All `.tsx` `.ts` `.mdx` files |
| `"pure-ui"` | `"dusk-ui"` | All files |
| `"Pure UI"` | `"Dusk UI"` | All files |
| `PureUI` | `DuskUI` | All files |
| `pureUI` | `duskUI` | All files |
| `pure-ui` (kebab, non-path) | `dusk-ui` | All files |
| `pure.kam-ui.com` | `dusk-ui.com` (or placeholder) | All files |
| `MusKRI` | `<your-github-username>` | All files |
| `Krishna` | `<your-name>` | All files |
| `Built by Krishna` | `Built by Mohamed` | `layout.tsx` |

> ⚠️ After running these, do a repo-wide search for `pure` and `Pure` to catch any stragglers.

---

### Step 5 — File-by-File Updates

Some files need manual edits beyond string replacement:

#### `package.json`
```json
{
  "name": "dusk-ui",
  ...
}
```
Also remove any Docker-related scripts if present.

---

#### `src/app/layout.tsx`
```tsx
export const metadata: Metadata = {
  title: "Dusk UI",
  description: "<your tagline>",
};
```

---

#### `src/app/(home)/page.tsx`
```tsx
// Change the two RollingText components:
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

// Change the tagline <p>:
<p className="mx-auto max-w-md text-lg text-foreground/80 font-light">
  <your tagline here>
</p>
```

---

#### `src/core/components/composed/logo.tsx`
```tsx
// Rename the function:
export function DuskUILogo(props: SVGProps<SVGSVGElement>) {
  // The SVG itself can stay for now — replace with Dusk UI logo in Phase 2
}
```

---

#### `src/core/components/composed/header/github-button.tsx`
```tsx
// Update both the fetch URL and the Link href:
const data = await fetch("https://api.github.com/repos/<your-username>/dusk-ui", {
  next: { revalidate: 3600 },
});

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
    "@duskui": "https://dusk-ui.com/r/{name}.json"
  }
}
```

---

#### `src/registry/dusk-ui/registry.ts` (after folder rename)
```ts
export const registry = {
  name: "Dusk UI",
  homepage: "https://dusk-ui.com",
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

#### `src/core/events/openpanel.tsx` — Analytics

**Option A (Recommended for now):** Strip analytics entirely to avoid Krishna's key being active:
```tsx
// Replace entire file contents with:
export const Analytics = () => null;
```
Then remove `@openpanel/nextjs` and `@wandry/analytics-sdk` from `package.json` and reinstall.

**Option B:** Register your own OpenPanel project at https://openpanel.dev and replace `NEXT_PUBLIC_OPENPANEL_CLIENT_ID` with your key.

---

#### `src/content/*.mdx` — Docs Installation Commands

Every MDX file contains installation commands like:
```bash
npx shadcn add https://pure.kam-ui.com/r/button.json
```
Bulk replace across all content files:
```
https://pure.kam-ui.com/r/  →  https://dusk-ui.com/r/
```

---

#### `README.md`
Rewrite from scratch — new name, description, install instructions.

---

### Step 6 — Verify Build

```bash
pnpm install        # reinstall after package.json changes
pnpm build          # should complete with no errors
pnpm dev            # visually verify: name shows "Dusk UI" everywhere
```

Do a final visual pass on:
- [ ] Browser tab title says "Dusk UI"
- [ ] Header logo/name correct
- [ ] Home page rolling text says "Dusk" + "UI"
- [ ] Home page tagline is yours
- [ ] GitHub button links to your repo
- [ ] No console errors
- [ ] No "Pure UI" or "Krishna" or "MusKRI" visible anywhere
- [ ] `pnpm build` passes cleanly

---

### Phase 1 Checklist (Ordered)

```
[ ] 1.  Fork MusKRI/pure-ui → your GitHub account, rename to dusk-ui
[ ] 2.  Clone locally
[ ] 3.  Delete: Dockerfile, docker-compose.yml, proxy.ts, .env.example
[ ] 4.  Delete: .github/ (entire folder)
[ ] 5.  mv src/registry/pure-ui → src/registry/dusk-ui
[ ] 6.  Global find-replace all brand strings (see table in Step 4)
[ ] 7.  Repo-wide search for "pure" / "Pure" — catch stragglers
[ ] 8.  package.json: name → "dusk-ui", remove Docker scripts
[ ] 9.  src/app/layout.tsx: title + description
[ ] 10. src/app/(home)/page.tsx: RollingText + tagline
[ ] 11. src/core/components/composed/logo.tsx: rename PureUILogo → DuskUILogo
[ ] 12. github-button.tsx: repo URL → your repo
[ ] 13. components.json: registry URL → dusk-ui.com
[ ] 14. src/registry/dusk-ui/registry.ts: name, homepage, import names
[ ] 15. src/core/events/openpanel.tsx: strip or replace with your analytics
[ ] 16. src/content/*.mdx: bulk replace installation command URLs
[ ] 17. README.md: rewrite
[ ] 18. pnpm install
[ ] 19. pnpm build — must pass cleanly
[ ] 20. pnpm dev — visual verification pass
```

---

## Phase 2 — Visual Identity (After Phase 1 is clean)

Once the rebrand is done and the build is clean, the next layer is Dusk UI's own visual identity. These are **not blockers** for Phase 1 — they come after.

| Task | Details |
|---|---|
| Logo | Design a Dusk UI SVG logo, replace the grid SVG in `logo.tsx` |
| Favicon | Replace `src/app/favicon.ico` |
| Brand color | Decide: monochrome (like Pure UI) or a distinct color (violet, amber, etc.) |
| Dark mode tokens | Finalize `.dark {}` OKLCH values in `globals.css` |
| Typography | Decide on heading font — Pure UI uses Chillax; keep or replace |
| Tagline | Finalize the hero tagline on `(home)/page.tsx` |
| og:image / SEO | Add `opengraph-image.tsx` to the app route |

---

## Phase 3 — Incremental Component Evolution

After the visual identity is set, we begin diverging from Pure UI component by component. Not all 27 need to change — only those where Dusk UI has a different design opinion.

Priority order (most visible → least visible):

1. `Button` — primary brand touchpoint; finalize `variant`/`size`/`radius` values
2. `Dialog` / `Sheet` — most complex; verify all 11 animation presets work correctly after rename
3. `Accordion` — verify `swiss` variant intact after rename
4. `Tabs` — verify sliding indicator and `ResizeObserver` height animation
5. `Toast` — verify Zustand store works, update Zustand actions if needed
6. `Input` / `InputGroup` — adjust ring/border tokens to match Dusk UI palette
7. `Card` — adjust shadow/border tokens
8. Everything else — incremental polish

---

## Phase 4 — New Components

Components Pure UI does not have that Dusk UI will add. **To be prioritized together after Phase 3 is stable.**

Candidates:
- `Alert` / `AlertDialog`
- `Progress` / `Meter`
- `Skeleton`
- `Breadcrumb`
- `Pagination`
- `Table`
- `NavigationMenu`
- `DatePicker` (uses `react-day-picker` — already a dep)
- `Slider`
- `Command` / `CommandPalette`

---

## Open Questions (Discuss Before Final Plan)

These do not block Phase 1. They need answers before we write the final plan.

| # | Question | Options |
|---|---|---|
| Q1 | **Docs framework** | A: Keep `next-mdx-remote` (Pure UI way — full control) · B: Migrate to Fumadocs (batteries-included) |
| Q2 | **Icon set** | A: Keep `@tabler/icons-react` (already in Pure UI) · B: `lucide-react` · C: `@hugeicons/react` |
| Q3 | **Typography / heading font** | A: Keep Chillax (Pure UI) · B: Replace with a font that fits "Dusk" identity |
| Q4 | **Brand color** | A: Monochrome/neutral (timeless, like Pure UI) · B: Distinct color (violet, amber, slate-blue, etc.) |
| Q5 | **Toast API** | A: Keep Zustand imperative `toast.add(...)` · B: React context provider |
| Q6 | **Analytics** | A: OpenPanel (register own account) · B: Vercel Analytics · C: Strip entirely for now |
| Q7 | **`radius` variant on Button** | A: Keep as first-class `tv()` axis · B: Remove, leave to `className` |
| Q8 | **Domain / URL** | What is the final domain? (needed for `components.json` registry URL and MDX install commands) |

---

## What Is Already Decided

These are **locked** — not up for discussion:

- Fork Pure UI as the starting point (not greenfield)
- Base UI 1.0 as primitive layer
- `tailwind-variants` (`tv()`) — not CVA
- CSS `data-starting-style` / `data-ending-style` for all component animations
- Motion v12 for doc site demos only — never inside registry components
- OKLCH color system
- shadcn CLI `registry.json` distribution
- Biome for linting and formatting
- React 19 + TypeScript 5.9 strict
- The full `cssAnimationPresets` + `cssTransitionPresets` system (15 presets, 25 easings)
- `data-slot` on every compound sub-part
- `build-registry.ts` → `shadcn build` → `registry.json` pipeline
- Named exports only (no default exports for components)
- pnpm + Node 22.x
- Vercel for deployment (not Docker VPS)
