# ⚛️ react-component-generator

> Generates complete, production-ready React components with TypeScript, accessibility, Storybook stories, and unit tests — in one shot.

## What It Does

Drop this skill into Claude and it transforms a plain description like *"a dismissible notification badge"* into:

- `ComponentName.tsx` — Full TypeScript component with `forwardRef`, variants, all visual states
- `ComponentName.stories.tsx` — Storybook stories for every state (Storybook 7+)
- `ComponentName.test.tsx` — Vitest + React Testing Library test suite
- `index.ts` — Barrel export with type export

## When to Use

| Trigger phrase | Example |
|---|---|
| Create/build/make a component | "Create a reusable Tag component with color variants" |
| Generate a UI element | "Build a skeleton loader for a card grid" |
| Scaffold a widget | "I need a multi-select dropdown with search" |
| Needs a form element | "Make a controlled file upload input" |
| Compound component | "Build a Tabs component with keyboard navigation" |

## Output Quality

Every generated component:
- ✅ Full TypeScript with JSDoc-commented props interface
- ✅ Extends native HTML attributes (consumers get `className`, `style`, `aria-*` for free)
- ✅ Controlled + uncontrolled pattern support
- ✅ WCAG 2.1 AA accessibility (correct roles, keyboard nav, focus management)
- ✅ All visual states handled: default, loading, disabled, error, empty
- ✅ Storybook story per state + AllVariants story
- ✅ Unit tests covering render, interaction, accessibility, and edge cases
- ✅ `forwardRef` + `displayName` set

## Styling Adapts to Your Stack

The skill detects what you mention and adapts:

| You mention | Generated styles |
|---|---|
| Tailwind | Utility classes + `cn()` helper |
| styled-components / emotion | Tagged template literals |
| CSS Modules | `*.module.css` (default if nothing specified) |

## Usage

```bash
# Copy SKILL.md content into:
# - Claude.ai → Project Instructions
# - Anthropic API → system prompt parameter
# - Any Claude integration that supports system prompts
```

Then prompt Claude naturally:

```
Build a Toast notification component with variants:
success, error, warning, info. Should auto-dismiss after
5 seconds with a visible progress bar. Use Tailwind.
```

## Example Output Structure

```
Toast.tsx
Toast.stories.tsx
Toast.test.tsx
index.ts
```

See [`example/`](./example/) for a full sample input → output.

## Requirements

| Tool | Version |
|---|---|
| React | 18+ |
| TypeScript | 5+ |
| Storybook | 7+ (CSF3 format) |
| Vitest | 1+ |
| @testing-library/react | 14+ |

## Customization Tips

Add to your prompt to guide output:

- `"No tests needed"` — skips test file
- `"No Storybook"` — skips stories file
- `"Use Radix UI primitives"` — builds on accessible headless components
- `"Match this design system: [paste tokens]"` — uses your exact spacing/color tokens
- `"shadcn/ui style"` — adapts to shadcn conventions
