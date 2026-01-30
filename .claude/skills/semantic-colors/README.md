# Semantic Design Tokens

### Automated Refactoring Skill for UI Codebases

---

## The Pain Points

---

### 1. Hardcoded Colors Everywhere

Colors are scattered across dozens of files as raw Tailwind utilities, hex codes, and rgba values.

```jsx
// Button.jsx
className="shadow-lg shadow-emerald-500/20 hover:shadow-emerald-500/30"

// StatusLED.jsx
style={{ backgroundColor: '#9ca3af' }}

// Mermaid.jsx
primaryColor: '#2563eb',
primaryTextColor: '#111827',
primaryBorderColor: '#2563eb',
lineColor: '#374151',
// ... 28 hardcoded hex values
```

**Impact:** Every color is a magic number. No developer knows if `#2563eb` is "brand blue" or just "some blue."

---

### 2. Dark Mode Is a Nightmare

Adding dark mode means finding every hardcoded color and adding a `dark:` variant.

```jsx
// Before: must add dark: for EVERY color
className="bg-red-50 border-red-200 text-red-700"

// With dark mode: 3 more classes per element
className="bg-red-50 dark:bg-red-900/20 border-red-200 dark:border-red-800 text-red-700 dark:text-red-300"
```

**Impact:** Class strings double in length. Miss one `dark:` override and the UI breaks in dark mode.

---

### 3. "What Color Is Error?"

Different developers pick different reds for error states.

```
File A:  text-red-500     (#ef4444)
File B:  text-red-600     (#dc2626)
File C:  text-red-700     (#b91c1c)
File D:  style={{ color: '#f87171' }}
```

**Impact:** Inconsistent visual language. Users see 4 different "error reds" across the app.

---

### 4. Redesigns Touch Every File

Changing the brand color from blue to teal means:

- Find every `blue-500`, `blue-600`, `#2563eb`, `#1d4ed8`
- Across every `.jsx`, `.tsx`, `.js`, `.ts` file
- Including inline styles, Mermaid configs, SVG fills
- Without breaking gradients or decorative effects

**Impact:** A "simple" brand color change becomes a multi-day, error-prone refactor.

---

### 5. Third-Party Components Ignore Your Theme

Libraries like Mermaid, chart components, and diagram renderers use their own hardcoded colors.

```js
// Mermaid.jsx - BEFORE
mermaid.initialize({
  themeVariables: {
    primaryColor: '#2563eb',      // Hardcoded, ignores theme
    textColor: '#111827',          // Won't change in dark mode
    background: '#ffffff',         // Always white
    // ...24 more hardcoded values
  }
});
```

**Impact:** Switch to dark mode and your diagrams stay light. Change brand color and diagrams stay blue.

---

## The Solution: Semantic Design Tokens

---

### What Are Semantic Tokens?

Instead of describing **what color** something is, describe **what it means**.

```
Hardcoded:   text-red-500         "I am red shade 500"
Semantic:    text-error           "I am an error message"

Hardcoded:   bg-green-100         "I am light green"
Semantic:    bg-success/10        "I am a success indicator at 10% opacity"

Hardcoded:   #2563eb              "I am hex blue"
Semantic:    var(--color-brand-bg) "I am the brand color"
```

---

### The Token Vocabulary

**What you say** tells the system **what you mean**, not what you look like:

| Token | Meaning | Light | Dark |
|-------|---------|-------|------|
| `text-foreground` | Primary text | `#111827` | `#ffffff` |
| `text-muted-foreground` | Secondary text | `#6b7280` | `#9ca3af` |
| `text-error` | Error message | `#dc2626` | `#f87171` |
| `text-success` | Success message | `#16a34a` | `#4ade80` |
| `bg-background` | Page background | `#ffffff` | `#1f2937` |
| `bg-card` | Card surface | `#ffffff` | `#374151` |
| `bg-error/10` | Error highlight | Light red | Dark red |

---

### One Change, Everywhere

Change the brand color in **one file** (`globals.css`):

```css
:root {
  --color-brand-bg: #0d9488;         /* Was #2563eb */
}
.dark {
  --color-brand-bg: #14b8a6;         /* Was #3b82f6 */
}
```

Every component, diagram, status indicator, and third-party library picks it up automatically. Zero file changes.

---

## How the Skill Works

---

### Per-File Workflow

For each `.tsx`, `.jsx`, `.ts`, `.js` file in the target path:

```
Read file → Find hardcoded colors → Check context → Edit immediately or skip
```

1. Read the file
2. Find hardcoded colors (Tailwind utilities, hex, rgba, inline styles)
3. Check if each match is inside a gradient/SVG-def context
4. If safe → apply matching rule and **edit immediately**
5. If gradient/SVG context → skip, log as `⚠ skipped`

After all files are done, re-scan to verify zero remaining violations. Print summary.

---

### Rule-Based, Not Guesswork

Every conversion maps to an explicit rule:

| Pattern | Replacement |
|---------|-------------|
| `text-red-*` | `text-error` |
| `text-green-*`, `text-emerald-*` | `text-success` |
| `text-gray-*`, `text-slate-*` | `text-muted-foreground` |
| `bg-red-*` (light shades) | `bg-error/10` |
| `bg-gray-*` | `bg-muted` |
| `shadow-emerald-*` | `shadow-success` |
| `#hex` in inline styles | `var(--color-*)` or `className` |
| `#hex` in third-party config | `cssVar('--color-*', fallback)` |

No AI guessing. No hallucinated class names. Deterministic, repeatable.

---

## Safety Guarantees

---

### Gradient Protection (Non-Negotiable)

The skill **never** touches colors inside gradient contexts:

```jsx
// PROTECTED - never modified:
background: 'linear-gradient(135deg, #667eea 0%, #764ba2 100%)'
background: 'radial-gradient(circle, rgba(34, 197, 94, 0.4), transparent)'
className="bg-gradient-to-r from-blue-500 to-purple-600"
```

```jsx
// CONVERTED - flat color, not in gradient:
backgroundColor: '#9ca3af'     -->  var(--color-icon-default)
className="shadow-emerald-500" -->  shadow-success
```

Breaking a gradient would ruin visual effects that took hours to craft.

---

### SVG Safety

```xml
<!-- PROTECTED - gradient defs never touched -->
<linearGradient id="meshGrad">
  <stop offset="0" stopColor="hsl(var(--success))" />
</linearGradient>

<!-- CONVERTED - flat fills are safe -->
<path fill="#22c55e" />  -->  <path fill="hsl(var(--success))" />
```

---

### Glass Morphism / Decorative Overlays

Decorative transparency patterns are recognized and preserved:

```jsx
// SKIP - decorative glass effect
background: 'rgba(255, 255, 255, 0.05)'
border: '1px solid rgba(255, 255, 255, 0.1)'
boxShadow: '0 25px 50px -12px rgba(0, 0, 0, 0.5)'
```

---

## Real-World Case Study: EDI Portal

---

### Scan Results

```
Files scanned:    6 (targeted components)
Violations found: 14
  - Tailwind hardcoded shadows:  2
  - Inline hex colors:           4
  - Third-party config (Mermaid): 29
  - Scoped page colors:          8

Protected (not modified):
  - Gradients:         42
  - SVG defs:           6
  - Glass morphism:    12
  - Decorative effects: 14
```

---

### Example 1: StatusLED Component

**Pain:** Loading and unknown states use raw hex `#9ca3af` that won't adapt to theme changes.

```diff
  // Loading state
- backgroundColor: '#9ca3af',
+ backgroundColor: 'var(--color-icon-default)',

  // Unknown state
- backgroundColor: '#9ca3af',
+ backgroundColor: 'var(--color-icon-default)',
```

**Safety:** Green/yellow/red LEDs use `radial-gradient()` - automatically skipped.

---

### Example 2: Login Button Shadows

**Pain:** `shadow-emerald-500/20` is a hardcoded Tailwind color. If the success color changes from emerald to teal, this shadow stays emerald.

```diff
- className="shadow-lg shadow-emerald-500/20 hover:shadow-emerald-500/30"
+ className="shadow-lg shadow-success/20 hover:shadow-success/30"
```

Now the shadow automatically matches whatever `--success` is defined as.

---

### Example 3: Mermaid Diagrams (Aggressive Mode)

**Pain:** 28 hardcoded hex values. Mermaid diagrams always render in light-mode colors, even in dark mode.

```diff
- // Module-level init with hardcoded hex
- mermaid.initialize({
-   themeVariables: {
-     primaryColor: '#2563eb',
-     textColor: '#111827',
-     background: '#ffffff',
-     // ...25 more hardcoded values
-   }
- });

+ // Runtime CSS variable resolution
+ function cssVar(name, fallback) {
+   return getComputedStyle(document.documentElement)
+     .getPropertyValue(name).trim() || fallback;
+ }
+
+ function getThemeVariables() {
+   return {
+     primaryColor: cssVar('--color-brand-bg', '#2563eb'),
+     textColor:    cssVar('--color-text-primary', '#111827'),
+     background:   cssVar('--color-bg-primary', '#ffffff'),
+     // ...25 more, all reading from CSS tokens
+   };
+ }
```

**Result:** Diagrams now respect dark mode, brand color changes, and any future theme.

---

### Example 4: Status Page (Scoped Tokens)

**Pain:** Inline hex colors on a fixed dark gradient page. Can't use global theme vars (they'd break on light theme).

**Solution:** Scoped CSS custom properties with fallbacks:

```diff
- color: '#e4e4e7',
+ color: 'var(--status-text, #e4e4e7)',

- color: '#fff',
+ color: 'var(--status-text-bright, #fff)',

- color: '#a1a1aa',
+ color: 'var(--status-text-muted, #a1a1aa)',

- color: '#71717a',
+ color: 'var(--status-text-subtle, #71717a)',
```

**Result:** Zero visual change today. But if the page is ever themed, just define `--status-text` in CSS.

---

## Before & After Summary

| Metric | Before | After |
|--------|--------|-------|
| Hardcoded hex colors in components | 35 | 0 |
| Hardcoded Tailwind color utilities | 4 | 0 |
| Theme-aware Mermaid diagrams | No | Yes |
| Dark mode support (diagrams) | Broken | Automatic |
| Brand color change effort | Multi-file | 1 file |
| Gradient/SVG breakage | Risk | Zero (protected) |

---

## Integration into Development Workflow

---

### As a CLI Skill (Today)

```bash
# Run against a specific directory
/semantic-colors edi-portal/

# Run against entire project (default)
/semantic-colors
```

---

### As a CI Lint Rule (Recommended)

```yaml
# .github/workflows/lint.yml
- name: Check semantic tokens
  run: npx eslint --rule 'no-hardcoded-styles: error' .
```

Enforce on every PR:
- No new `text-red-*`, `bg-green-*`, `#hex`, or `rgba()` in component files
- Gradients and SVG defs are excluded
- Auto-fixable in most cases

---

### Token Lifecycle

```
1. Define tokens    -->  globals.css (:root / .dark)
2. Consume tokens   -->  Components use text-error, bg-muted, var(--)
3. Enforce tokens   -->  Lint rule blocks hardcoded colors
4. Audit tokens     -->  /semantic-colors finds and fixes drift
5. Update tokens    -->  Change one CSS file, propagates everywhere
```

---

## Key Takeaways

1. **Semantic tokens describe intent, not appearance** - `text-error` not `text-red-500`

2. **One source of truth** - Change colors in `globals.css`, everything follows

3. **Dark mode for free** - Semantic tokens resolve to the correct value per theme

4. **Gradients are sacred** - The skill never breaks visual effects

5. **Deterministic** - Rule-based, not AI-guessing. Same input = same output

6. **Incremental** - Run on one file or the whole codebase

---

## Appendix: Full Token Reference

### Text Tokens
| Token | Usage |
|-------|-------|
| `text-foreground` | Primary content text |
| `text-muted-foreground` | Secondary / helper text |
| `text-primary` | Brand-colored text |
| `text-primary-foreground` | Text on brand background |
| `text-success` | Success messages |
| `text-warning` | Warning messages |
| `text-error` | Error messages |
| `text-info` | Informational text |

### Background Tokens
| Token | Usage |
|-------|-------|
| `bg-background` | Page background |
| `bg-card` | Card / panel surface |
| `bg-muted` | Subtle background |
| `bg-primary` | Brand background |
| `bg-success` | Success indicator |
| `bg-warning` | Warning indicator |
| `bg-error` | Error indicator |
| `bg-info` | Info indicator |

### CSS Custom Properties (Inline Styles)
| Property | Usage |
|----------|-------|
| `var(--color-bg-primary)` | Page / component background |
| `var(--color-text-primary)` | Primary text color |
| `var(--color-text-muted)` | Muted text color |
| `var(--color-brand-bg)` | Brand / primary color |
| `var(--color-icon-default)` | Default icon color |
| `var(--color-border-default)` | Default border color |
| `var(--color-error-text)` | Error state text |
| `var(--color-success-text)` | Success state text |

### Foreground Pairing Rule
| Background | Must Pair With |
|------------|---------------|
| `bg-primary` | `text-primary-foreground` |
| `bg-success` | `text-success-foreground` |
| `bg-error` | `text-error-foreground` |
| `bg-warning` | `text-warning-foreground` |

### Status Mapping
| Status | Token |
|--------|-------|
| DRAFT | `muted` |
| PENDING, SUBMITTED | `warning` |
| CONFIRMED, SUCCESS | `success` |
| REJECTED, ERROR | `error` |
| INFO, SENT | `info` |
