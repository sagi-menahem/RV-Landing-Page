# AI Development Guidelines

> **Source of Truth** for all development on projects derived from this template.
> Read this document completely before making any changes.

---

## Table of Contents

1. [Project Architecture](#1-project-architecture)
2. [Design System & UI](#2-design-system--ui)
3. [Content Management Rules](#3-content-management-rules)
4. [Component Standards](#4-component-standards)
5. [File Structure Reference](#5-file-structure-reference)
6. [Common Tasks Cheatsheet](#6-common-tasks-cheatsheet)

---

## 1. Project Architecture

### 1.1 The Astro + React Hybrid Model

This project uses **Astro** as the primary framework with **React** for interactive components ("islands").

```
┌─────────────────────────────────────────────────────────────────┐
│                     ASTRO (Static Shell)                        │
│  - Pages (src/pages/*.astro)                                    │
│  - Layouts (src/layouts/*.astro)                                │
│  - Data fetching from Content Collections                       │
│  - SEO, meta tags, HTML structure                               │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                   REACT ISLANDS (Interactive)                   │
│  - Components with client:load directive                        │
│  - State management (useState, useCallback)                     │
│  - Event handlers (clicks, forms, keyboard)                     │
│  - Animations (Framer Motion)                                   │
└─────────────────────────────────────────────────────────────────┘
```

#### When to Use Astro Components (.astro)
- Static content that doesn't need interactivity
- Layout wrappers and page structures
- SEO-critical content (rendered at build time)
- Data fetching from content collections

#### When to Use React Components (.tsx)
- User interactions (clicks, hovers, keyboard events)
- Form handling and validation
- State-dependent UI (modals, accordions, filters)
- Animations requiring JavaScript

#### The `client:` Directive

React components **must** have a client directive to hydrate:

```astro
<!-- Hydrate immediately when page loads -->
<Navbar client:load />

<!-- Hydrate when component enters viewport -->
<ContactForm client:visible />

<!-- Hydrate on idle (low priority) -->
<LogoMarquee client:idle />
```

**Rule:** Use `client:load` for above-the-fold interactive elements. Use `client:visible` or `client:idle` for below-the-fold components.

### 1.2 Data Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        DATA SOURCES                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  src/content/              │  CMS-editable content              │
│  ├── settings/index.json   │  Site-wide settings (title, nav)   │
│  ├── homepage/index.json   │  Homepage section content          │
│  ├── posts/*.md            │  Blog posts (Markdown)             │
│  ├── projects/*.json       │  Portfolio items                   │
│  ├── faqs/*.json           │  FAQ entries                       │
│  └── logos/*.json          │  Client/partner logos              │
│                                                                 │
│  src/consts.ts             │  Hardcoded UI text & defaults      │
│  ├── SITE_CONFIG           │  Language, direction (LTR/RTL)     │
│  ├── UI_LABELS             │  Button text, form labels, etc.    │
│  └── DEFAULTS              │  Fallbacks if CMS empty            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

#### Content Collections (src/content/)

Managed by **Keystatic CMS** at `/admin`. Schema defined in `src/content/config.ts`.

- **Type: 'content'** - Markdown files with frontmatter (posts)
- **Type: 'data'** - JSON files (settings, projects, FAQs)

To fetch content in Astro pages:

```typescript
import { getEntry, getCollection } from 'astro:content';

// Single entry (singleton)
const settings = await getEntry('settings', 'index');

// Multiple entries (collection)
const posts = await getCollection('posts', ({ data }) => !data.draft);
```

#### Constants (src/consts.ts)

For UI text that doesn't need CMS editing:

```typescript
import { UI_LABELS, DEFAULTS, SITE_CONFIG } from '../consts';

// Access labels
const buttonText = UI_LABELS.form.submitButton;

// Check RTL mode
const isRTL = SITE_CONFIG.direction === 'rtl';
```

---

## 2. Design System & UI

### 2.1 Theme Colors (CSS Variables)

All colors are defined as CSS custom properties in `src/styles/global.css`:

```css
@theme {
  /* Primary - Main brand color */
  --color-primary: #2563eb;
  --color-primary-dark: #1d4ed8;
  --color-primary-light: #dbeafe;

  /* Secondary - Accent color */
  --color-secondary: #8b5cf6;
  --color-secondary-dark: #7c3aed;
  --color-secondary-light: #ede9fe;

  /* Background */
  --color-background: #ffffff;
  --color-background-alt: #f9fafb;

  /* Surface (cards, elevated elements) */
  --color-surface: #ffffff;
  --color-surface-elevated: #f3f4f6;

  /* Text */
  --color-foreground: #111827;
  --color-muted: #6b7280;

  /* Border */
  --color-border: #e5e7eb;
  --color-border-hover: #d1d5db;

  /* Status */
  --color-success: #10b981;
  --color-error: #ef4444;
  --color-warning: #f59e0b;
}
```

#### How to Change Theme Colors

1. Open `src/styles/global.css`
2. Modify values in the `@theme` block
3. Use semantic color names in Tailwind classes:

```html
<!-- Use semantic names, NOT hex values -->
<div class="bg-primary text-white">Correct</div>
<div class="bg-primary text-white">Incorrect - Avoid hardcoded colors</div>
```

### 2.2 Component Utilities

Pre-defined component classes in `src/styles/global.css`:

| Class | Usage |
|-------|-------|
| `.btn-primary` | Primary CTA buttons |
| `.btn-secondary` | Secondary/ghost buttons |
| `.card` | Card containers with hover effects |
| `.input` | Form input fields |

```html
<button class="btn-primary">Get Started</button>
<a href="#" class="btn-secondary">Learn More</a>
<div class="card p-6">Card content</div>
<input class="input" placeholder="Email" />
```

### 2.3 Image Requirements (CRITICAL for CLS)

**STRICT RULE:** All `<img>` elements MUST have explicit `width` and `height` attributes to prevent Cumulative Layout Shift (CLS).

```tsx
// CORRECT - Explicit dimensions
<img
  src={project.image}
  alt={project.alt}
  width={400}
  height={300}
  className="w-full h-full object-cover"
  loading="lazy"
/>

// INCORRECT - Missing dimensions (causes CLS)
<img
  src={project.image}
  alt={project.alt}
  className="w-full h-auto"
/>
```

For responsive images with fixed aspect ratio, use Tailwind's aspect utilities:

```html
<div class="aspect-4/3 overflow-hidden">
  <img src="..." alt="..." width={400} height={300} class="w-full h-full object-cover" />
</div>
```

### 2.4 Animation System

Animations are centralized in `src/lib/animations.ts`. Import and use predefined variants:

```tsx
import { EASING, DURATION, fadeInUp, staggerContainer } from '../lib/animations';

<motion.div
  variants={fadeInUp}
  initial="hidden"
  animate="visible"
>
```

**Available animations:**
- `fadeIn`, `fadeInUp`, `fadeInDown`, `fadeInLeft`, `fadeInRight`
- `scaleUp`
- `staggerContainer`, `staggerContainerFast`, `staggerContainerSlow`
- `staggerItem`

**Do NOT** create inline animation objects. Always use or extend the centralized variants.

---

## 3. Content Management Rules

### 3.1 No Hardcoded Strings in Components (STRICT)

**RULE:** All user-facing text MUST be extracted to `src/consts.ts` or content collections.

```tsx
// WRONG - Hardcoded string
<button aria-label="Close modal">

// CORRECT - From consts.ts
import { UI_LABELS } from '../consts';
<button aria-label={UI_LABELS.lightbox.close}>
```

### 3.2 UI_LABELS Structure

The `UI_LABELS` object in `src/consts.ts` is organized by feature:

```typescript
export const UI_LABELS = {
  form: {
    nameLabel: 'Name',
    emailLabel: 'Email',
    submitButton: 'Send Message',
    // ...
  },
  validation: {
    nameRequired: 'Name must be at least 2 characters',
    // ...
  },
  accessibility: {
    title: 'Accessibility',
    buttonLabel: 'Accessibility options',
    // ...
  },
  nav: {
    toggleMenu: 'Toggle menu',
    // ...
  },
  lightbox: {
    close: 'Close lightbox',
    previous: 'Previous image',
    next: 'Next image',
  },
  gallery: {
    noResults: 'No projects found in this category.',
  },
  // ...
};
```

### 3.3 Extending UI_LABELS

When adding a new component with UI text:

1. Add a new section to `UI_LABELS` in `src/consts.ts`:

```typescript
export const UI_LABELS = {
  // ... existing sections

  // New section for your component
  testimonials: {
    title: 'What Our Clients Say',
    readMore: 'Read full testimonial',
    emptyState: 'No testimonials yet.',
  },
};
```

2. Import and use in your component:

```tsx
import { UI_LABELS } from '../consts';

const { testimonials: labels } = UI_LABELS;

<h2>{labels.title}</h2>
<button>{labels.readMore}</button>
```

### 3.4 DEFAULTS Object

Fallback values when CMS content is empty:

```typescript
export const DEFAULTS = {
  siteTitle: 'Astro Landing',
  siteDescription: 'A modern landing page template',
  featuresTitle: 'Features',
  // ...
};
```

Use with nullish coalescing in pages:

```typescript
const {
  heroHeadline = DEFAULTS.siteTitle,
  heroSubheadline = DEFAULTS.siteDescription,
} = homepage;
```

---

## 4. Component Standards

### 4.1 Creating New Sections

Every new section MUST follow this structure:

```astro
<!-- In src/pages/index.astro -->
{
  shouldShowSection && (
    <section id="section-id" class="bg-background py-24">
      <div class="container mx-auto px-4">
        <AnimatedSection animation="fadeInUp" className="mx-auto max-w-2xl text-center mb-12" client:load>
          <h2 class="text-3xl font-bold tracking-tight text-foreground md:text-4xl">
            {sectionTitle}
          </h2>
          <p class="mt-4 text-lg text-muted">{sectionSubtitle}</p>
        </AnimatedSection>

        <div class="mx-auto max-w-6xl">
          <!-- Section content -->
        </div>
      </div>
    </section>
  )
}
```

**Checklist for new sections:**
- [ ] Wrapped in `<section>` with unique `id` attribute
- [ ] Has conditional rendering check (`shouldShowSection && (...)`)
- [ ] Uses `container mx-auto px-4` for content width
- [ ] Title/subtitle wrapped in `AnimatedSection`
- [ ] Background alternates: `bg-background` or `bg-background-alt`
- [ ] Consistent vertical padding: `py-24`

### 4.2 Astro vs React Decision Tree

```
                    Does it need interactivity?
                              │
                    ┌─────────┴─────────┐
                    │                   │
                   YES                  NO
                    │                   │
                    ▼                   ▼
            Use React (.tsx)    Use Astro (.astro)
            with client:*       or inline in page
                    │
                    ▼
        ┌───────────────────────┐
        │ What kind of          │
        │ interactivity?        │
        └───────────────────────┘
                    │
    ┌───────────────┼───────────────┐
    │               │               │
    ▼               ▼               ▼
Form/Input     Animation      State/Toggle
    │               │               │
    ▼               ▼               ▼
ContactForm   AnimatedSection   FAQAccordion
(client:load) (client:load)     (client:load)
```

### 4.3 React Component Template

```tsx
/**
 * ComponentName Component
 *
 * Brief description of what it does.
 */

import { useState, useCallback } from 'react';
import { motion, AnimatePresence } from 'framer-motion';
import { SomeIcon } from 'lucide-react';
import { UI_LABELS, SITE_CONFIG } from '../consts';
import { cn } from '../lib/utils';
import { EASING, DURATION } from '../lib/animations';

// Destructure labels at top level
const { sectionName: labels } = UI_LABELS;

// TypeScript interface for props
interface ComponentNameProps {
  /** Description of prop */
  items: Array<{ id: string; title: string }>;
  /** Optional prop with default */
  showTitle?: boolean;
}

export function ComponentName({
  items,
  showTitle = true,
}: ComponentNameProps) {
  const isRTL = SITE_CONFIG.direction === 'rtl';
  const [activeIndex, setActiveIndex] = useState<number | null>(null);

  const handleClick = useCallback((index: number) => {
    setActiveIndex(index);
  }, []);

  return (
    <div className="w-full">
      {/* Component JSX */}
    </div>
  );
}

export default ComponentName;
```

### 4.4 Semantic HTML Requirements

- **Page structure:** `<header>`, `<main>`, `<footer>` at page level
- **Sections:** Use `<section>` with descriptive `id` attributes
- **Navigation:** Use `<nav>` element
- **Articles/Posts:** Use `<article>` element
- **Time:** Use `<time datetime="...">` for dates
- **Headings:** Maintain hierarchy (h1 > h2 > h3), one `<h1>` per page

### 4.5 RTL Support

The template supports Right-to-Left languages. Key points:

1. Set direction in `src/consts.ts`:
```typescript
export const SITE_CONFIG = {
  direction: 'rtl', // or 'ltr'
  language: 'he',   // Language code
  locale: 'he-IL',  // For date formatting
};
```

2. Use logical properties in CSS/Tailwind:
```html
<!-- Use logical properties (RTL-aware) -->
<div class="ps-4 pe-2 ms-auto me-0">

<!-- NOT physical properties -->
<div class="pl-4 pr-2 ml-auto mr-0">
```

3. Check RTL in components:
```tsx
const isRTL = SITE_CONFIG.direction === 'rtl';
```

---

## 5. File Structure Reference

```
src/
├── components/           # React interactive components
│   ├── Navbar.tsx       # Main navigation
│   ├── ContactForm.tsx  # Form with validation
│   ├── FAQAccordion.tsx # Expandable FAQ
│   ├── Lightbox.tsx     # Image viewer modal
│   ├── ProjectsGallery.tsx  # Filterable gallery
│   ├── LogoMarquee.tsx  # Scrolling logos
│   ├── AnimatedSection.tsx  # Animation wrappers
│   ├── AccessibilityBtn.tsx # A11y menu
│   └── DynamicIcon.tsx  # Lucide icon loader
│
├── content/             # Keystatic CMS content
│   ├── config.ts        # Collection schemas
│   ├── settings/        # Site-wide settings
│   ├── homepage/        # Homepage content
│   ├── posts/           # Blog posts (Markdown)
│   ├── projects/        # Portfolio items
│   ├── faqs/            # FAQ entries
│   └── logos/           # Client logos
│
├── layouts/
│   └── Layout.astro     # Base HTML template
│
├── lib/
│   ├── animations.ts    # Framer Motion variants
│   └── utils.ts         # Utility functions (cn, etc.)
│
├── pages/
│   ├── index.astro      # Homepage
│   └── posts/[slug].astro  # Blog post template
│
├── styles/
│   └── global.css       # Theme & component styles
│
└── consts.ts            # UI labels & defaults
```

---

## 6. Common Tasks Cheatsheet

### Change Brand Colors
1. Edit `src/styles/global.css` → `@theme` block
2. Modify `--color-primary`, `--color-secondary`, etc.

### Add New Navigation Link
1. Go to `/admin` → Settings
2. Add to "Nav Links" array

### Add New FAQ Entry
1. Go to `/admin` → FAQs
2. Create new entry with question/answer

### Add New Section to Homepage
1. Define content in `src/content/homepage/index.json`
2. Add schema to `src/content/config.ts` (if new fields)
3. Fetch data in `src/pages/index.astro`
4. Add UI labels to `src/consts.ts`
5. Create section HTML with proper structure

### Create New Interactive Component
1. Create `src/components/NewComponent.tsx`
2. Add labels to `src/consts.ts`
3. Import in page with `client:load` directive

### Change Site Language/Direction
1. Edit `SITE_CONFIG` in `src/consts.ts`
2. Update all `UI_LABELS` text
3. Test RTL layout if switching direction

### Deploy to Production
1. Update `PUBLIC_SITE_URL` env variable
2. Update sitemap URL in `public/robots.txt`
3. Run `npm run build` to verify no errors
4. Deploy via Vercel or preferred platform

---

## Quick Reference Card

| Task | Location |
|------|----------|
| Change colors | `src/styles/global.css` |
| Edit UI text | `src/consts.ts` → `UI_LABELS` |
| Add CMS content | `/admin` (Keystatic) |
| Define new content types | `src/content/config.ts` |
| Create animations | `src/lib/animations.ts` |
| Page structure | `src/pages/index.astro` |
| Component utilities | `src/styles/global.css` → `@layer components` |

---

*Last Updated: December 2024*
*Template Version: 1.0.0*
