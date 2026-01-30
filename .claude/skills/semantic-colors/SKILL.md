# Semantic Design Tokens Refactor

Scan files, replace hardcoded colors with semantic tokens, edit files directly.

## Usage

```
/semantic-design [path]
```

## DO NOT TOUCH (Non-Negotiable)

- **Gradients:** `from-*`, `via-*`, `to-*`, `bg-gradient-*`, `linear-gradient()`, `radial-gradient()`, `conic-gradient()`
- **SVG defs:** `<linearGradient>`, `<radialGradient>`, `<stop>`, `url(#...)`, anything inside `<defs>`
- **Glass morphism:** `rgba(255,255,255,0.0x)` overlays on gradient backgrounds
- **Decorative shadows:** `boxShadow` glow effects tied to gradient LED/orb effects
- **Token source files:** `globals.css`, `tailwind.config.*`, `tokens.ts`
- **Excluded dirs:** `node_modules`, `.next`, `lib/generated`, `prisma/`

If a color is inside a gradient/SVG-def context, **skip** and log `⚠ skipped (gradient context)`.

## Rules

### Color Replacements

| Hardcoded | Semantic |
|-----------|----------|
| `text-red-*` | `text-error` |
| `text-green-*`, `text-emerald-*` | `text-success` |
| `text-yellow-*`, `text-amber-*` | `text-warning` |
| `text-blue-*` | `text-info` |
| `text-gray-*`, `text-slate-*`, `text-zinc-*` | `text-muted-foreground` |
| `text-white` (on colored bg) | `text-primary-foreground` |
| `text-black` | `text-foreground` |
| `bg-red-*` | `bg-error` or `bg-error/10` (light shades) |
| `bg-green-*`, `bg-emerald-*` | `bg-success` or `bg-success/10` |
| `bg-yellow-*`, `bg-amber-*` | `bg-warning` or `bg-warning/10` |
| `bg-blue-*` | `bg-info` or `bg-info/10` |
| `bg-gray-*` | `bg-muted` |
| `bg-white` | `bg-background` or `bg-card` |
| `bg-black/50` (overlay) | `bg-overlay` |
| `border-gray-*` | `border-border` |
| `border-red-*` | `border-error/20` |
| `border-green-*` | `border-success/20` |
| `border-blue-*` | `border-info/20` |
| `border-yellow-*` | `border-warning/20` |
| `shadow-emerald-*`, `shadow-green-*` | `shadow-success` |
| `shadow-red-*` | `shadow-error` |
| `shadow-blue-*` | `shadow-info` |
| `ring-red-*` | `ring-error` |
| `ring-green-*` | `ring-success` |

### Foreground Pairing

| Background | Foreground |
|------------|------------|
| `bg-primary` | `text-primary-foreground` |
| `bg-success` | `text-success-foreground` |
| `bg-error` | `text-error-foreground` |
| `bg-warning` | `text-warning-foreground` |
| `bg-info` | `text-info-foreground` |

### Dark Mode Override Removal

Collapse light+dark pairs into one semantic token:

```diff
- className="text-green-600 dark:text-green-400"
+ className="text-success"

- className="bg-red-100 dark:bg-red-900/20 border-red-200 dark:border-red-800"
+ className="bg-error/10 border-error/20"
```

### Inline Style Replacements

| Hardcoded | Semantic |
|-----------|----------|
| `style={{ color: '#111827' }}` | `className="text-foreground"` |
| `style={{ color: '#6b7280' }}` | `className="text-muted-foreground"` |
| `style={{ backgroundColor: '#ffffff' }}` | `var(--color-bg-primary)` |
| `style={{ backgroundColor: '#9ca3af' }}` | `var(--color-icon-default)` |
| `style={{ backgroundColor: '#fee2e2' }}` | `className="bg-error/10"` |
| `rgba(255,255,255,N)` on gradient | Keep as-is (gradient context) |

### Third-Party Config Pattern (Mermaid, charts, diagrams)

When a library requires hex values at initialization, use runtime CSS variable resolution with safe fallbacks:

```js
function cssVar(name, fallback) {
  if (typeof window === 'undefined') return fallback;
  return getComputedStyle(document.documentElement)
    .getPropertyValue(name).trim() || fallback;
}

// Usage: cssVar('--color-brand-bg', '#2563eb')
```

Initialize in `useEffect` (not module level) so CSS variables are available and theme changes are picked up.

### Scoped Token Pattern (fixed-context pages)

When a page has a fixed dark gradient background that doesn't change with theme, use `var()` with inline fallback:

```js
// Global theme vars would break (light text vars on dark bg)
// Instead: scoped custom property + fallback preserves appearance
color: 'var(--status-text-muted, #a1a1aa)'
```

### Typography Replacements (Aggressive Mode Only)

Only apply if custom typography utility classes are defined in the project.

| Hardcoded | Semantic |
|-----------|----------|
| `text-3xl font-bold` | `text-heading-xl` |
| `text-2xl font-semibold` | `text-heading-lg` |
| `text-lg font-medium` | `text-heading-md` |
| `text-base` | `text-body` |
| `text-sm` (muted context) | `text-caption` |
| `uppercase tracking-wide` | `text-label` |

### Status → Semantic Mapping

| Status | Semantic |
|--------|----------|
| `DRAFT` | `muted` |
| `PENDING`, `SUBMITTED` | `warning` |
| `CONFIRMED`, `SUCCESS`, `COMPLETED`, `PAID` | `success` |
| `REJECTED`, `ERROR`, `CANCELLED` | `error` |
| `INFO`, `SENT`, `RECEIVED`, `INVOICED` | `info` |

### Available Tokens Reference

**Text:** `text-foreground`, `text-muted-foreground`, `text-primary`, `text-primary-foreground`, `text-success`, `text-warning`, `text-error`, `text-info`

**Background:** `bg-background`, `bg-card`, `bg-muted`, `bg-primary`, `bg-success`, `bg-warning`, `bg-error`, `bg-info`, `bg-overlay`

**Border:** `border-border`, `border-input`, `border-primary`, `border-success`, `border-error`, `border-warning`, `border-info`

**Shadow/Ring:** `shadow-success`, `shadow-error`, `shadow-info`, `ring-success`, `ring-error`, `ring-info`

**Typography:** `text-heading-xl`, `text-heading-lg`, `text-heading-md`, `text-body`, `text-body-sm`, `text-caption`, `text-label`

**CSS Custom Properties (for inline styles):** `var(--color-bg-primary)`, `var(--color-text-primary)`, `var(--color-text-muted)`, `var(--color-brand-bg)`, `var(--color-icon-default)`, `var(--color-border-default)`, `var(--color-error-text)`, `var(--color-success-text)`

## Behavior

For each `.tsx`, `.jsx`, `.ts`, `.js` file in the target path (skip excluded dirs):

1. Read the file
2. Find hardcoded colors — check if inside gradient/SVG-def context
3. If safe → apply matching rule and edit immediately
4. If gradient/SVG context → skip, log as `⚠ skipped`

After all files are done, re-scan to verify zero remaining violations. Print summary.

## Summary Format

```
Semantic refactor: N files changed, M replacements
- file.tsx: brief description of changes
⚠ file.tsx:42 — skipped (gradient context)
⚠ file.tsx:88 — skipped (SVG defs)
```
