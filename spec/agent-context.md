# Dusk UI — Agent Context

> This file is the **LLM-targeted cheat sheet** for autonomous component generation.  
> Read this before writing any code. Obey every rule. No exceptions.
> Last updated: March 13, 2026

---

## Identity

- **Library:** Dusk UI
- **URL:** `dusk-ui.vercel.app`
- **Stack:** Next.js App Router + Base UI + Tailwind v4 + Biome + pnpm
- **Registry:** shadcn format
- **Docs:** Fumadocs

---

## Hard Rules

### Never Do
- `asChild` — Base UI uses `render` prop
- `import * as React` — use named imports
- `import { x } from 'hugeicons-react'` — deprecated; use `@hugeicons/core-free-icons`
- `export default function Component` — named exports only
- `text-gray-500`, `bg-zinc-800`, etc. — semantic tokens only
- `border-border` — it is already the default
- `ps-*`/`pe-*` in source — write physical classes; CLI handles RTL transform
- `motion/react` for simple enter/exit — use CSS `data-starting-style`/`data-ending-style`
- `group`/`group-hover:` when `in-[[data-slot=x]:hover]:` works
- `useForm`, `useController`, `zodResolver` in primitive components — forms live in Particles only
- `"use client"` on stateless components

### Always Do
- `data-slot="[part-name]"` on every compound sub-part
- `render` prop via `useRender` + `mergeProps` on every interactive component
- `color="currentColor"` on every `HugeiconsIcon`
- `size={20}` (or 16/24/32) via prop — never size icons via className
- `aria-hidden="true"` on decorative icons
- `aria-label` on icon-only interactive elements
- `pointer-coarse:after:min-h-11 min-w-11` on buttons
- `dir` prop on portal elements when RTL is active
- Named exports: `export { Button, type ButtonProps }`
- Colocate types: `export interface ButtonProps {}`
- Static arrays/objects outside component function

---

## Import Map

```tsx
// Base UI
import * as DialogPrimitive from '@base-ui/react/dialog';
import { mergeProps, useRender } from '@base-ui/react/utils';

// Icons
import { HugeiconsIcon } from '@hugeicons/react';
import { SearchIcon, XIcon } from '@hugeicons/core-free-icons';

// Styling
import { cn } from '@/lib/utils';
import { cva, type VariantProps } from 'class-variance-authority';

// Animation (only for physics/gestures)
import { motion, AnimatePresence } from 'motion/react';

// Forms (Particles tier only)
import { useForm, Controller } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';
```

---

## Component Skeleton

Every primitive follows this exact skeleton:

```tsx
'use client'; // only if hooks/state/browser API are used

import { HugeiconsIcon } from '@hugeicons/react';
import { SomeIcon } from '@hugeicons/core-free-icons';
import { mergeProps, useRender } from '@base-ui/react/utils';
import * as Primitive from '@base-ui/react/{component}';
import { cva, type VariantProps } from 'class-variance-authority';
import { cn } from '@/lib/utils';

// Static data outside component
const componentVariants = cva(
  'base-classes',
  {
    variants: {
      variant: { default: '...', destructive: '...' },
      size: { sm: '...', md: '...', lg: '...' },
    },
    defaultVariants: { variant: 'default', size: 'md' },
  }
);

export interface ComponentProps
  extends React.ComponentPropsWithoutRef<'element'>,
    VariantProps<typeof componentVariants> {
  // explicit additional props
}

function Component({ variant, size, className, ...props }: ComponentProps) {
  return (
    <Primitive.Root
      data-slot="component"
      className={cn(componentVariants({ variant, size }), className)}
      {...props}
    />
  );
}

export { Component, type ComponentProps };
```

---

## Animation Rules

```
CSS (data-starting-style / data-ending-style)
  └─ Enter/exit transitions — ALWAYS use this first

Tailwind transitions (transition-*, duration-*, ease-*)
  └─ Micro-interactions — hover, focus, active, color changes

motion/react
  └─ Physics only:
       - Spring animations
       - Drag to dismiss
       - layout reflow (<motion.div layout>)
       - Stagger children
```

**CSS enter/exit pattern:**
```tsx
// On the animated element:
"data-[starting-style]:opacity-0 data-[starting-style]:scale-95"
"data-[ending-style]:opacity-0 data-[ending-style]:scale-95"
"transition-[opacity,transform] duration-150 ease-out"
```

---

## Token Reference

### Surfaces
`--background` `--foreground` `--border` `--muted` `--muted-foreground` `--accent` `--accent-foreground`

### Brand
`--primary` `--primary-foreground` `--primary-border`  
`--secondary` `--secondary-foreground` `--secondary-border`

### Semantic States
`--danger` `--danger-foreground` `--danger-border`  
`--warning` `--warning-foreground` `--warning-border`  
`--success` `--success-foreground` `--success-border`  
`--info` `--info-foreground` `--info-border`

### Component Sub-tokens
`--card` `--card-border` `--card-footer` `--card-shadow`  
`--popover` `--popover-border` `--popover-shadow` `--popover-accent`  
`--dialog` `--dialog-border` `--dialog-footer`  
`--input` `--input-border` `--input-accent-border` `--input-shadow`  
`--tabs` `--tabs-border` `--tabs-accent`  
`--toast` `--toast-border`  
`--sidebar` `--sidebar-border` `--sidebar-foreground` `--sidebar-accent` etc.

### Radius
`--radius-xs` `--radius-sm` `--radius-md` `--radius-lg` `--radius-xl` `--radius-2xl` `--radius-full`

### Typography
`--font-sans` (Geist Sans) `--font-mono` (Geist Mono) `--font-display` (Geist Pixel — display only)

---

## Base UI Component Availability (v1.2.0)

Before building, verify the component exists here. If not listed — it does not exist in Base UI.

Accordion, Alert Dialog, Autocomplete, Avatar, Button, Checkbox, Checkbox Group, Collapsible, Combobox, Context Menu, Dialog, Drawer, Field, Fieldset, Form, Input, Menu, Menubar, Meter, Navigation Menu, Number Field, Popover, Preview Card, Progress, Radio, Scroll Area, Select, Separator, Slider, Switch, Tabs, Toast, Toggle, Toggle Group, Toolbar, Tooltip

Utilities: `mergeProps`, `useRender`, `CspProvider`, `DirectionProvider`

Full docs index: https://base-ui.com/llms.txt

---

## Icon Sizes

| Size | Value | When |
|---|---|---|
| `sm` | `16` | Inline, tight spaces |
| `default` | `20` | Most UI elements |
| `grid` | `24` | Cards, empty states |
| `lg` | `32` | Feature icons, hero |

---

## File Locations

| What | Where |
|---|---|
| Distributable components | `components/ui/{component}.tsx` |
| Docs-site components | `components/docs/` |
| Utilities | `lib/utils.ts` |
| Fumadocs source | `lib/source.ts` |
| Custom hooks | `hooks/` |
| Registry source | `registry/default/ui/` |
| MDX docs | `content/docs/{component}.mdx` |
| Design tokens | `app/globals.css` |
| Coding rules | `AGENTS.md` |

---

## Reference Libraries (source of truth for patterns)

| Library | Repo | What to study |
|---|---|---|
| coss ui | [cosscom/coss](https://github.com/cosscom/coss) | `useRender`, `data-slot`, token system, AGENTS.md |
| Selia | [nauvalazhar/selia](https://github.com/nauvalazhar/selia) | oklch tokens, layout primitives, ring system |
| Pure UI | [MusKRI/pure-ui](https://github.com/MusKRI/pure-ui) | Animation patterns, event bus |
| Spell UI | [xxtomm/spell-ui](https://github.com/xxtomm/spell-ui) | motion/react physics patterns |
