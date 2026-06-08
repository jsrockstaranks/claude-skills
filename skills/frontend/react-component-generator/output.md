# Example Output

> Built a `Badge` component with 5 variants, 3 sizes, optional icon, and controlled/uncontrolled
> dismiss. Assumption: "controlled" means `visible` prop controls render; uncontrolled uses internal
> state. Tailwind + `cn()` helper used for styling.

---

## Badge.tsx

```tsx
// ─── Badge.tsx ──────────────────────────────────────────────────────────────
import * as React from 'react';
import { cn } from '@/lib/utils';
import { X } from 'lucide-react';

export interface BadgeProps extends React.HTMLAttributes<HTMLSpanElement> {
  /** Visual style of the badge */
  variant?: 'default' | 'success' | 'warning' | 'error' | 'info';
  /** Size of the badge */
  size?: 'sm' | 'md' | 'lg';
  /** Optional leading icon rendered before the label */
  icon?: React.ReactNode;
  /** When provided, renders a dismiss (X) button */
  onDismiss?: () => void;
  /** Controlled visibility. Omit to use internal uncontrolled state */
  visible?: boolean;
  /** Default visibility for uncontrolled mode */
  defaultVisible?: boolean;
  /** Additional class names */
  className?: string;
}

const variantStyles = {
  default: 'bg-gray-100 text-gray-700 border-gray-200',
  success: 'bg-green-50  text-green-700 border-green-200',
  warning: 'bg-yellow-50 text-yellow-700 border-yellow-200',
  error:   'bg-red-50    text-red-700   border-red-200',
  info:    'bg-blue-50   text-blue-700  border-blue-200',
} as const;

const sizeStyles = {
  sm: 'text-xs px-2 py-0.5 gap-1',
  md: 'text-sm px-2.5 py-1 gap-1.5',
  lg: 'text-base px-3 py-1.5 gap-2',
} as const;

const dismissSizeStyles = {
  sm: 'w-3 h-3',
  md: 'w-3.5 h-3.5',
  lg: 'w-4 h-4',
} as const;

function useControllable(
  controlledValue: boolean | undefined,
  defaultValue: boolean,
  onChange?: () => void,
) {
  const [internal, setInternal] = React.useState(defaultValue);
  const isControlled = controlledValue !== undefined;
  const value = isControlled ? controlledValue : internal;
  const hide = React.useCallback(() => {
    if (!isControlled) setInternal(false);
    onChange?.();
  }, [isControlled, onChange]);
  return [value, hide] as const;
}

const Badge = React.forwardRef<HTMLSpanElement, BadgeProps>(
  (
    {
      variant = 'default',
      size = 'md',
      icon,
      onDismiss,
      visible: controlledVisible,
      defaultVisible = true,
      className,
      children,
      ...rest
    },
    ref,
  ) => {
    const [isVisible, dismiss] = useControllable(controlledVisible, defaultVisible, onDismiss);

    if (!isVisible) return null;

    return (
      <span
        ref={ref}
        role="status"
        className={cn(
          'inline-flex items-center border rounded-full font-medium select-none',
          variantStyles[variant],
          sizeStyles[size],
          className,
        )}
        {...rest}
      >
        {icon && (
          <span className="shrink-0" aria-hidden="true">
            {icon}
          </span>
        )}
        {children}
        {onDismiss && (
          <button
            type="button"
            aria-label="Dismiss"
            onClick={dismiss}
            className={cn(
              'shrink-0 rounded-full opacity-60 hover:opacity-100 focus:outline-none',
              'focus-visible:ring-2 focus-visible:ring-current transition-opacity',
            )}
          >
            <X className={dismissSizeStyles[size]} />
          </button>
        )}
      </span>
    );
  },
);
Badge.displayName = 'Badge';

export { Badge };
```

---

## Badge.stories.tsx

```tsx
// ─── Badge.stories.tsx ──────────────────────────────────────────────────────
import type { Meta, StoryObj } from '@storybook/react';
import { CheckCircle2 } from 'lucide-react';
import { Badge } from './Badge';

const meta = {
  title: 'UI/Badge',
  component: Badge,
  parameters: {
    layout: 'centered',
    docs: {
      description: {
        component:
          'A compact label for status, category, or count. Supports 5 variants, 3 sizes, optional icon, and dismissal.',
      },
    },
  },
  tags: ['autodocs'],
  argTypes: {
    variant: { control: 'select', options: ['default', 'success', 'warning', 'error', 'info'] },
    size: { control: 'radio', options: ['sm', 'md', 'lg'] },
  },
} satisfies Meta<typeof Badge>;

export default meta;
type Story = StoryObj<typeof meta>;

export const Default: Story = {
  args: { children: 'Badge' },
};

export const Success: Story = {
  args: { variant: 'success', children: 'Published' },
};

export const WithIcon: Story = {
  args: {
    variant: 'success',
    icon: <CheckCircle2 className="w-3.5 h-3.5" />,
    children: 'Verified',
  },
};

export const Dismissible: Story = {
  args: {
    variant: 'info',
    children: 'New feature',
    onDismiss: () => console.log('dismissed'),
  },
};

export const AllVariants: Story = {
  render: () => (
    <div className="flex flex-wrap gap-2">
      {(['default', 'success', 'warning', 'error', 'info'] as const).map((v) => (
        <Badge key={v} variant={v}>{v}</Badge>
      ))}
    </div>
  ),
};

export const AllSizes: Story = {
  render: () => (
    <div className="flex items-center gap-2">
      <Badge size="sm">Small</Badge>
      <Badge size="md">Medium</Badge>
      <Badge size="lg">Large</Badge>
    </div>
  ),
};
```

---

## Badge.test.tsx

```tsx
// ─── Badge.test.tsx ─────────────────────────────────────────────────────────
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { describe, it, expect, vi } from 'vitest';
import { Badge } from './Badge';

describe('Badge', () => {
  it('renders children', () => {
    render(<Badge>Published</Badge>);
    expect(screen.getByText('Published')).toBeInTheDocument();
  });

  it('is not visible when defaultVisible is false', () => {
    render(<Badge defaultVisible={false}>Hidden</Badge>);
    expect(screen.queryByText('Hidden')).not.toBeInTheDocument();
  });

  it('renders dismiss button when onDismiss is provided', () => {
    render(<Badge onDismiss={vi.fn()}>Label</Badge>);
    expect(screen.getByRole('button', { name: /dismiss/i })).toBeInTheDocument();
  });

  it('calls onDismiss and hides badge when dismiss clicked (uncontrolled)', async () => {
    const user = userEvent.setup();
    const onDismiss = vi.fn();
    render(<Badge onDismiss={onDismiss}>Label</Badge>);
    await user.click(screen.getByRole('button', { name: /dismiss/i }));
    expect(onDismiss).toHaveBeenCalledOnce();
    expect(screen.queryByText('Label')).not.toBeInTheDocument();
  });

  it('respects controlled visible=false', () => {
    render(<Badge visible={false}>Label</Badge>);
    expect(screen.queryByText('Label')).not.toBeInTheDocument();
  });

  it('applies custom className', () => {
    render(<Badge className="custom-class">Label</Badge>);
    expect(screen.getByRole('status')).toHaveClass('custom-class');
  });

  it('renders icon slot', () => {
    render(<Badge icon={<span data-testid="icon" />}>Label</Badge>);
    expect(screen.getByTestId('icon')).toBeInTheDocument();
  });

  it('has accessible role', () => {
    render(<Badge>Status</Badge>);
    expect(screen.getByRole('status')).toBeInTheDocument();
  });
});
```

---

## index.ts

```ts
// ─── index.ts ────────────────────────────────────────────────────────────────
export { Badge } from './Badge';
export type { BadgeProps } from './Badge';
```

---

## Usage

```tsx
import { Badge } from '@/components/ui/Badge';

// Basic
<Badge variant="success">Published</Badge>

// With icon
<Badge variant="info" icon={<InfoIcon />}>New</Badge>

// Dismissible (uncontrolled)
<Badge variant="warning" onDismiss={() => console.log('gone')}>
  Beta
</Badge>

// Dismissible (controlled)
const [show, setShow] = useState(true);
<Badge visible={show} onDismiss={() => setShow(false)}>
  Trial ending
</Badge>
```

---

## Props

| Prop | Type | Default | Description |
|---|---|---|---|
| `variant` | `'default' \| 'success' \| 'warning' \| 'error' \| 'info'` | `'default'` | Visual color style |
| `size` | `'sm' \| 'md' \| 'lg'` | `'md'` | Badge size |
| `icon` | `ReactNode` | — | Leading icon rendered before label |
| `onDismiss` | `() => void` | — | Shows dismiss button; called on click |
| `visible` | `boolean` | — | Controlled visibility |
| `defaultVisible` | `boolean` | `true` | Default visibility (uncontrolled) |
| `className` | `string` | — | Additional CSS classes |
| `children` | `ReactNode` | — | Badge label content |
