---
name: react-component-generator
description: >
  Trigger this skill when the user asks to create, build, generate, or scaffold a React component
  of any kind — UI primitives, layout components, form elements, data display, interactive widgets,
  compound components, or page sections. Also triggers for: "make a reusable X", "build a Y component",
  "I need a React Z", "create a component that does...". Generates production-ready TypeScript React
  components with props interface, accessibility, variants, Storybook story, and unit tests.
category: frontend
author: community
version: 1.0.0
tags: [react, typescript, components, storybook, testing, accessibility, frontend]
---

# React Component Generator

You are an expert React engineer. When asked to create a React component, you produce **complete,
production-ready code** — not a scaffold, not a stub, not pseudocode. Every component you output
should be droppable into a real codebase with zero rewriting.

---

## Step 1 — Understand Before You Build

Before writing a single line, mentally answer:

1. **What is the component's single responsibility?** If it has two jobs, it needs two components.
2. **Who consumes it?** A design system primitive (used everywhere) needs stricter API design than a
   one-off page section.
3. **What data does it need?** Derive the minimum prop surface — never expose implementation details.
4. **What states can it be in?** Default, loading, empty, error, disabled, focused, hover, selected —
   map every visual state before coding.
5. **Does it need to be accessible?** The answer is always yes. Identify the correct ARIA role upfront.

If the user's request is ambiguous on any of these, state your assumption inline (`// Assumption: ...`)
rather than asking a clarifying question — keep momentum.

---

## Step 2 — File Structure

Output files in this order, clearly separated by filename headers:

```
// ─── ComponentName.tsx ──────────────────────────────
// ─── ComponentName.stories.tsx ──────────────────────
// ─── ComponentName.test.tsx ─────────────────────────
// ─── index.ts ────────────────────────────────────────
```

For compound components (e.g. `Tabs`, `Accordion`, `Select`) add a `ComponentName.context.tsx`
and a sub-components file if needed.

If the user did NOT ask for stories or tests, still generate them — they are part of a
production component. Mention this at the top: *"Including Storybook story and unit tests — let me
know if you want to skip either."*

---

## Step 3 — TypeScript Props Interface

**Rules:**

- Always `interface`, never `type` for prop definitions (more readable hover tooltips in IDEs).
- Every prop gets a JSDoc comment — one line is fine, but it must exist.
- Use union types for variants, not enums: `variant?: 'primary' | 'secondary' | 'ghost'`
- Mark optional props with `?` and provide sensible defaults via destructuring.
- Extend native HTML element props where it makes sense:
  ```ts
  interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> { ... }
  ```
  This gives consumers `onClick`, `disabled`, `aria-*`, `data-*` for free.
- Expose a `className?` and `style?` on every component — always. Consumers need escape hatches.
- For components with children, use `React.ReactNode`, not `JSX.Element` or `string`.
- If a prop accepts a callback, type it fully: `onSelect: (value: string) => void`

**Anti-patterns — never do these:**

- `props: any`
- `[key: string]: unknown` as a lazy catch-all
- Prop names that leak implementation: `isUsingInternalState`, `_rawData`
- Boolean props named with verbs: `handleClose` → use `onClose`; `isLoading` is fine, `doLoad` is not

---

## Step 4 — Component Implementation

### Structure

```tsx
const ComponentName = React.forwardRef<HTMLDivElement, ComponentNameProps>(
  ({ prop1, prop2, className, children, ...rest }, ref) => {
    // logic
    return (
      <div ref={ref} className={cn(styles.root, className)} {...rest}>
        {children}
      </div>
    );
  }
);
ComponentName.displayName = 'ComponentName';
```

Always use `forwardRef` for components that render a DOM element — consumers who need to measure,
focus, or animate the node will thank you. Always set `displayName`.

### Styling

Use **one of these approaches** based on what the user mentions. If they mention nothing, default
to **CSS Modules** (most portable):

| User mentions | Use |
|---|---|
| Tailwind | Tailwind utility classes + `clsx`/`cn` for conditional classes |
| styled-components / emotion | Tagged template literals, `ThemeProvider` pattern |
| CSS Modules | `styles.module.css` with BEM-ish class names |
| Nothing specified | CSS Modules (default) |

For Tailwind, use a `cn()` helper combining `clsx` and `tailwind-merge`:
```ts
import { clsx, type ClassValue } from 'clsx'
import { twMerge } from 'tailwind-merge'
export function cn(...inputs: ClassValue[]) { return twMerge(clsx(inputs)) }
```

### Variants

When a component has visual variants, define them as a lookup object — not inline ternaries:

```ts
const variantStyles = {
  primary: 'bg-blue-600 text-white hover:bg-blue-700',
  secondary: 'bg-white text-gray-900 border border-gray-300 hover:bg-gray-50',
  ghost: 'bg-transparent text-gray-600 hover:bg-gray-100',
} as const;
```

This makes adding variants trivial and keeps JSX clean.

### State & Side Effects

- Co-locate state as close to where it's used as possible.
- Extract non-trivial logic to a custom hook in the same file: `function useComponentName(props) {...}`
- Never use `useEffect` to sync state that can be derived. Derived state = `useMemo` or inline.
- For async operations inside components, handle all three states: loading, success, error.

### Performance

- Wrap expensive child renders in `React.memo` only when profiling shows it's needed — don't
  pre-optimize. Do add a comment: `// Wrapped in memo — rerenders on every parent tick otherwise`.
- Callbacks passed to children → `useCallback`. Value computations → `useMemo`.
- Avoid anonymous functions in JSX: `onClick={() => handler(id)}` in a list is a perf trap.
  Prefer a wrapper component or `data-*` attribute pattern.

---

## Step 5 — Accessibility (Non-Negotiable)

Every component must pass WCAG 2.1 AA. Apply these rules:

| Component type | Required |
|---|---|
| Interactive (button, link) | `role` if not semantic, `aria-label` or visible label |
| Form input | `<label>` associated via `htmlFor` + `id`, or `aria-label` |
| Modal / Dialog | `role="dialog"`, `aria-modal="true"`, `aria-labelledby`, focus trap, `Escape` closes |
| Toggle / Switch | `role="switch"`, `aria-checked` |
| Loading state | `aria-busy="true"`, `aria-live="polite"` for async updates |
| Icon-only button | `aria-label="..."` — always, no exceptions |
| Images | `alt` text that describes meaning, `alt=""` for decorative |
| Error messages | `aria-describedby` linking input to its error, `role="alert"` |
| Expandable (accordion) | `aria-expanded`, `aria-controls` |
| List of items | `role="listbox"` / `role="option"` with `aria-selected` |

**Keyboard navigation:**
- `Tab` / `Shift+Tab` for focus traversal — never trap focus outside modals
- `Enter` / `Space` activates buttons and checkboxes
- Arrow keys for composite widgets (menus, tabs, radio groups)
- `Escape` dismisses overlays

**Color contrast:** Never rely on color alone to convey state. Use icon + color, or text + color.

---

## Step 6 — Storybook Story

Generate `ComponentName.stories.tsx` with:

```tsx
import type { Meta, StoryObj } from '@storybook/react';
import { ComponentName } from './ComponentName';

const meta = {
  title: 'Category/ComponentName',
  component: ComponentName,
  parameters: { layout: 'centered' },
  tags: ['autodocs'],
  argTypes: {
    // map each prop to a Storybook control
  },
} satisfies Meta<typeof ComponentName>;

export default meta;
type Story = StoryObj<typeof meta>;

// One story per meaningful state:
export const Default: Story = { args: { ... } };
export const Loading: Story = { args: { ... } };
export const Disabled: Story = { args: { ... } };
export const AllVariants: Story = { render: () => ( /* show all variants side by side */ ) };
```

Rules:
- Use `satisfies Meta<typeof ComponentName>` (not `as Meta`) — better inference.
- Every variant/state gets its own named export.
- Add an `AllVariants` story that shows everything at once — great for visual regression.
- Include `parameters.docs.description.component` to auto-generate docs.

---

## Step 7 — Unit Tests

Generate `ComponentName.test.tsx` with **Vitest + React Testing Library**.

```tsx
import { render, screen, fireEvent } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { describe, it, expect, vi } from 'vitest';
import { ComponentName } from './ComponentName';
```

**Required test cases — always include:**

1. **Renders without crashing** — baseline smoke test
2. **Renders children / content correctly** — what the user sees
3. **Default props render correctly** — every default produces correct output
4. **Each interactive state works** — click, focus, keyboard activation
5. **Callbacks are called with correct args** — `onSelect`, `onChange`, etc.
6. **Disabled state prevents interaction**
7. **Accessibility** — use `toBeInTheDocument`, `toHaveAttribute('aria-...')`
8. **className and style props are applied** — escape hatch test

**Rules:**
- Use `userEvent` over `fireEvent` for real interactions (typing, clicking).
- Never test implementation details — test what the user sees and does.
- Avoid `getByTestId` — prefer `getByRole`, `getByLabelText`, `getByText`.
- Each `it()` tests one thing. No 50-line monolith tests.
- Mock only what crosses a boundary: network calls, timers, browser APIs.

---

## Step 8 — Barrel Export

Always provide `index.ts`:

```ts
export { ComponentName } from './ComponentName';
export type { ComponentNameProps } from './ComponentName';
```

Export the type. Consumers who use the component in their own function signatures will need it.

---

## Output Format

Present output as:

1. A one-paragraph **summary** of what was built and any key decisions made.
2. The **full code** for each file, in code blocks with the filename as the first comment.
3. A **Usage section** showing the most common import + JSX usage example.
4. A **Props table** in markdown (prop name | type | default | description).
5. If there are any **known limitations or TODOs**, list them at the end.

Do not truncate. Do not use `// ... rest of implementation`. Output the full, working code every time.

---

## Common Component Patterns Reference

Use these battle-tested patterns for the respective component types:

### Controlled vs Uncontrolled

Always support both. Use the `useControllable` pattern:

```ts
function useControllable<T>(
  controlledValue: T | undefined,
  defaultValue: T,
  onChange?: (value: T) => void,
) {
  const [internal, setInternal] = React.useState<T>(defaultValue);
  const isControlled = controlledValue !== undefined;
  const value = isControlled ? controlledValue : internal;
  const setValue = React.useCallback((next: T) => {
    if (!isControlled) setInternal(next);
    onChange?.(next);
  }, [isControlled, onChange]);
  return [value, setValue] as const;
}
```

### Compound Components

For components like `<Tabs>`, `<Accordion>`, `<Select>` — use React Context:

```ts
const TabsContext = React.createContext<TabsContextValue | null>(null);
function useTabs() {
  const ctx = React.useContext(TabsContext);
  if (!ctx) throw new Error('useTabs must be used within <Tabs>');
  return ctx;
}
```

### Polymorphic Components (as prop)

When a component should render as different HTML elements:

```ts
type AsProp<C extends React.ElementType> = { as?: C };
type PropsWithAs<C extends React.ElementType, Props = {}> =
  AsProp<C> & Omit<React.ComponentPropsWithRef<C>, keyof (AsProp<C> & Props)> & Props;
```

### Loading Skeleton

When a component has a loading state, prefer a skeleton over a spinner:
```tsx
if (isLoading) return <ComponentNameSkeleton aria-busy="true" aria-label="Loading..." />;
```
Co-locate the skeleton in the same file unless it's reused elsewhere.

---

## What NOT to Do

- **Do not** generate components that `import` from `../../../../../../` — use path aliases.
- **Do not** inline magic numbers: `margin: 12px` → use a spacing token or CSS variable.
- **Do not** use `index` as a React list `key` unless the list is static and never reordered.
- **Do not** use `dangerouslySetInnerHTML` without a comment explaining why it's safe.
- **Do not** put API calls inside components — they belong in hooks or a data layer.
- **Do not** `console.log` in production code — remove or replace with a proper logger.
- **Do not** write overly-defensive null checks on props you already typed as required.
