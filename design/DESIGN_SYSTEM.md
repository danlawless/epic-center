# Transformational Epicenter - Design System

> *Visual language for transformation*

---

## Design Philosophy

The Transformational Epicenter design system embodies our core values:

1. **Calm Authority** - Medical credibility with warmth
2. **Natural Luxury** - Organic materials, refined details
3. **Spacious Clarity** - Breathing room, clear hierarchy
4. **Grounded Warmth** - Earth tones, natural textures
5. **Sacred Modern** - Ancient wisdom, contemporary expression

---

## Brand Essence

### Visual Metaphor
The nervous system - patterns of connection, flow, and coherence

### Mood
- Grounded yet elevated
- Clinical yet warm
- Luxurious yet accessible
- Ancient yet modern
- Precise yet flowing

### Keywords
Transformation | Sanctuary | Precision | Nature | Healing | Coherence

---

## Color Palette

### Primary Colors

```css
:root {
  /* Primary - Deep Forest */
  --primary-50: #f0f7f7;
  --primary-100: #d9ebea;
  --primary-200: #b3d7d5;
  --primary-300: #80bbb8;
  --primary-400: #4d9f9a;
  --primary-500: #2d7a76;
  --primary-600: #1a5a57;
  --primary-700: #1a4a47;
  --primary-800: #1a3a3a;  /* Main brand color */
  --primary-900: #152c2c;

  /* Secondary - Warm Gold */
  --secondary-50: #faf8f1;
  --secondary-100: #f2edd9;
  --secondary-200: #e5dbb3;
  --secondary-300: #d4c488;
  --secondary-400: #c4a962;  /* Main accent */
  --secondary-500: #b39347;
  --secondary-600: #9a7a3a;
  --secondary-700: #7d6131;
  --secondary-800: #674f2d;
  --secondary-900: #574329;
}
```

### Neutral Colors

```css
:root {
  /* Warm Neutrals */
  --neutral-50: #fafaf8;
  --neutral-100: #f5f5f2;
  --neutral-200: #e8e8e3;
  --neutral-300: #d4d4cd;
  --neutral-400: #a8a89e;
  --neutral-500: #7a7a70;
  --neutral-600: #5c5c54;
  --neutral-700: #454540;
  --neutral-800: #2e2e2a;
  --neutral-900: #1a1a18;
}
```

### Semantic Colors

```css
:root {
  /* Success */
  --success-50: #f0fdf4;
  --success-500: #22c55e;
  --success-700: #15803d;

  /* Warning */
  --warning-50: #fffbeb;
  --warning-500: #f59e0b;
  --warning-700: #b45309;

  /* Error */
  --error-50: #fef2f2;
  --error-500: #ef4444;
  --error-700: #b91c1c;

  /* Info */
  --info-50: #eff6ff;
  --info-500: #3b82f6;
  --info-700: #1d4ed8;
}
```

### Color Usage

| Color | Use Case |
|-------|----------|
| Primary-800 | Headers, primary CTAs, key elements |
| Primary-600 | Hover states, secondary emphasis |
| Secondary-400 | Accents, highlights, important elements |
| Neutral-50 | Page backgrounds |
| Neutral-100 | Card backgrounds |
| Neutral-700 | Body text |
| Neutral-900 | Headings |

---

## Typography

### Font Families

```css
:root {
  /* Primary - Headings */
  --font-heading: 'Cormorant Garamond', Georgia, serif;

  /* Secondary - Body */
  --font-body: 'Inter', system-ui, sans-serif;

  /* Accent - Labels */
  --font-accent: 'Montserrat', sans-serif;
}
```

### Type Scale

```css
:root {
  /* Font Sizes */
  --text-xs: 0.75rem;     /* 12px */
  --text-sm: 0.875rem;    /* 14px */
  --text-base: 1rem;      /* 16px */
  --text-lg: 1.125rem;    /* 18px */
  --text-xl: 1.25rem;     /* 20px */
  --text-2xl: 1.5rem;     /* 24px */
  --text-3xl: 1.875rem;   /* 30px */
  --text-4xl: 2.25rem;    /* 36px */
  --text-5xl: 3rem;       /* 48px */
  --text-6xl: 3.75rem;    /* 60px */

  /* Line Heights */
  --leading-tight: 1.25;
  --leading-snug: 1.375;
  --leading-normal: 1.5;
  --leading-relaxed: 1.625;
  --leading-loose: 2;
}
```

### Typography Styles

| Style | Font | Size | Weight | Line Height |
|-------|------|------|--------|-------------|
| H1 | Cormorant Garamond | 48-60px | 400 | 1.25 |
| H2 | Cormorant Garamond | 36-48px | 400 | 1.25 |
| H3 | Cormorant Garamond | 24-30px | 500 | 1.375 |
| H4 | Inter | 20-24px | 600 | 1.375 |
| Body | Inter | 16-18px | 400 | 1.5 |
| Small | Inter | 14px | 400 | 1.5 |
| Caption | Montserrat | 12px | 500 | 1.5 |
| Button | Montserrat | 14-16px | 500 | 1 |

---

## Spacing

### Spacing Scale

```css
:root {
  --space-0: 0;
  --space-1: 0.25rem;   /* 4px */
  --space-2: 0.5rem;    /* 8px */
  --space-3: 0.75rem;   /* 12px */
  --space-4: 1rem;      /* 16px */
  --space-5: 1.25rem;   /* 20px */
  --space-6: 1.5rem;    /* 24px */
  --space-8: 2rem;      /* 32px */
  --space-10: 2.5rem;   /* 40px */
  --space-12: 3rem;     /* 48px */
  --space-16: 4rem;     /* 64px */
  --space-20: 5rem;     /* 80px */
  --space-24: 6rem;     /* 96px */
}
```

### Spacing Usage

| Space | Use Case |
|-------|----------|
| 4px | Inline elements, icons |
| 8px | Compact internal spacing |
| 16px | Standard internal spacing |
| 24px | Section internal spacing |
| 32px | Between elements |
| 48px | Section separation |
| 64px+ | Major section separation |

---

## Layout

### Container Widths

```css
:root {
  --container-xs: 320px;
  --container-sm: 640px;
  --container-md: 768px;
  --container-lg: 1024px;
  --container-xl: 1280px;
  --container-2xl: 1440px;
}
```

### Grid System

```css
.grid {
  display: grid;
  gap: var(--space-6);
}

.grid-cols-2 { grid-template-columns: repeat(2, 1fr); }
.grid-cols-3 { grid-template-columns: repeat(3, 1fr); }
.grid-cols-4 { grid-template-columns: repeat(4, 1fr); }
```

### Breakpoints

| Breakpoint | Width | Target |
|------------|-------|--------|
| sm | 640px | Large phones |
| md | 768px | Tablets |
| lg | 1024px | Small laptops |
| xl | 1280px | Desktops |
| 2xl | 1440px | Large desktops |

---

## Components

### Buttons

```css
.btn {
  font-family: var(--font-accent);
  font-weight: 500;
  border-radius: 4px;
  transition: all 0.2s ease;
}

.btn-primary {
  background: var(--primary-800);
  color: white;
}

.btn-secondary {
  background: transparent;
  border: 1px solid var(--primary-800);
  color: var(--primary-800);
}

.btn-accent {
  background: var(--secondary-400);
  color: var(--primary-900);
}

/* Sizes */
.btn-sm { padding: 8px 16px; font-size: 14px; }
.btn-md { padding: 12px 24px; font-size: 16px; }
.btn-lg { padding: 16px 32px; font-size: 18px; }
```

### Cards

```css
.card {
  background: var(--neutral-100);
  border-radius: 8px;
  padding: var(--space-6);
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
}

.card-elevated {
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.card-bordered {
  border: 1px solid var(--neutral-200);
  box-shadow: none;
}
```

### Form Elements

```css
.input {
  font-family: var(--font-body);
  padding: 12px 16px;
  border: 1px solid var(--neutral-300);
  border-radius: 4px;
  background: white;
}

.input:focus {
  border-color: var(--primary-600);
  outline: none;
  box-shadow: 0 0 0 3px rgba(26, 58, 58, 0.1);
}

.label {
  font-family: var(--font-accent);
  font-size: 14px;
  font-weight: 500;
  color: var(--neutral-700);
}
```

---

## Effects

### Shadows

```css
:root {
  --shadow-sm: 0 1px 2px rgba(0, 0, 0, 0.05);
  --shadow-md: 0 4px 6px rgba(0, 0, 0, 0.1);
  --shadow-lg: 0 10px 15px rgba(0, 0, 0, 0.1);
  --shadow-xl: 0 20px 25px rgba(0, 0, 0, 0.1);
}
```

### Border Radius

```css
:root {
  --radius-sm: 2px;
  --radius-md: 4px;
  --radius-lg: 8px;
  --radius-xl: 12px;
  --radius-2xl: 16px;
  --radius-full: 9999px;
}
```

### Transitions

```css
:root {
  --transition-fast: 150ms ease;
  --transition-normal: 200ms ease;
  --transition-slow: 300ms ease;
}
```

---

## Iconography

### Icon Style

- Line style (not filled)
- 1.5-2px stroke weight
- Rounded caps and joins
- Consistent 24x24 grid

### Icon Library

Primary: Lucide Icons (recommended)
- Clean, consistent line style
- Comprehensive set
- MIT licensed

---

## Imagery

### Photography Style

- Natural light preferred
- Warm, soft color grading
- Nature and organic settings
- Authentic, unposed moments
- Architectural details
- Texture and material focus

### Image Treatment

- Subtle warm color overlay
- Soft shadows
- Generous white space around
- Consistent aspect ratios

---

## Motion

### Animation Principles

1. **Purposeful** - Every animation serves function
2. **Subtle** - Not distracting
3. **Quick** - Respect user's time
4. **Natural** - Organic feeling

### Timing

| Type | Duration | Use |
|------|----------|-----|
| Micro | 100-200ms | Hover, focus |
| Short | 200-300ms | Transitions |
| Medium | 300-400ms | Larger reveals |
| Long | 400-600ms | Page transitions |

### Easing

```css
:root {
  --ease-out: cubic-bezier(0.16, 1, 0.3, 1);
  --ease-in-out: cubic-bezier(0.65, 0, 0.35, 1);
}
```

---

## Accessibility

### Requirements

- WCAG 2.1 AA compliance
- Color contrast 4.5:1 minimum
- Focus visible states
- Keyboard navigation
- Screen reader support
- Reduced motion support

### Focus States

```css
*:focus-visible {
  outline: 2px solid var(--primary-600);
  outline-offset: 2px;
}
```

---

## Mobile Considerations

### Touch Targets

- Minimum 44x44px touch targets
- Adequate spacing between targets
- Clear tap feedback

### Mobile Typography

- Slightly larger body text (17-18px)
- Increased line height
- Generous padding

---

## Implementation

### Design Tokens

See `/design/tokens/` for:
- `colors.json`
- `typography.json`
- `spacing.json`
- `shadows.json`

### Component Specs

See `/design/components/` for detailed component specifications.

### Brand Assets

See `/design/brand/` for logos, icons, and brand guidelines.

---

**Version**: 1.0.0
**Last Updated**: December 2024
