# RV Insurance Landing Page

A high-performance, accessible lead generation landing page for RV insurance services in Israel.

**Live Site:** [rv-ins.co.il](https://rv-ins.co.il)

## Overview

A modern, mobile-first landing page built with React 19 and TypeScript. Features Hebrew RTL layout, lazy-loaded sections for optimal performance, and serverless form submission via EmailJS.

## Tech Stack

| Category | Technology |
|----------|------------|
| Framework | React 19 |
| Language | TypeScript (strict mode) |
| Build Tool | Vite 7 |
| Styling | Tailwind CSS v4 |
| Animations | Framer Motion |
| Forms | React Hook Form + EmailJS |
| Hosting | Netlify |

## Features

- **Performance Optimized** - Lazy loading for below-the-fold sections, code splitting for vendor chunks
- **Fully Accessible** - Accessibility menu with grayscale, high contrast, large text, and reduced motion options
- **RTL Support** - Native Hebrew right-to-left layout
- **Responsive Design** - Mobile-first approach with fluid typography
- **SEO Ready** - Semantic HTML, meta tags, and structured data
- **Security Hardened** - CSP headers, input validation, and secure form handling

## Architecture Highlights

```
App
├─ AccessibilityMenu (global)
├─ Navbar (sticky with scroll behavior)
└─ Suspense
    ├─ Hero (eager load)
    └─ Lazy sections: ProblemSolution, Credibility,
       Services, Process, FAQ, Contact, Footer
```

## Performance

- Lighthouse Performance: 90+
- First Contentful Paint: < 1.5s
- Cumulative Layout Shift: < 0.1

---

**Note:** This repository contains documentation only. The source code is maintained in a private repository.
