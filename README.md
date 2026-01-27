<div align="center">
  <h1>Insurance Landing Page</h1>
  <h3>Portfolio Project Showcase</h3>
  <p>A high-performance, accessible lead generation landing page template for insurance services. Built with React 19, featuring Hebrew RTL support and modern web technologies.</p>

  <br />

  <strong>DEMO - Portfolio Showcase Only</strong>

</div>

---

> **Note:** This repository serves as a **technical showcase** for a portfolio project. This README documents the architectural decisions, tech stack, and features implemented.

---

<div align="center">

### Tech Stack

[![React](https://img.shields.io/badge/React-19-61DAFB?style=flat-square&logo=react)](https://react.dev/)
[![TypeScript](https://img.shields.io/badge/TypeScript-Strict-3178C6?style=flat-square&logo=typescript)](https://www.typescriptlang.org/)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind%20CSS-v4-06B6D4?style=flat-square&logo=tailwindcss)](https://tailwindcss.com/)
[![Vite](https://img.shields.io/badge/Vite-7-646CFF?style=flat-square&logo=vite)](https://vitejs.dev/)
[![Framer Motion](https://img.shields.io/badge/Framer_Motion-Animation-0055FF?style=flat-square&logo=framer)](https://www.framer.com/motion/)

</div>

---

## Key Features

This project was built with a focus on performance, accessibility, and conversion optimization.

- **Performance Optimized** - Lazy loading for below-the-fold sections, vendor chunk splitting (React & Framer Motion), and optimized asset delivery.
- **Mobile-First Design** - Fully responsive layouts with fluid typography and touch-optimized interactions.
- **Native RTL Support** - Built from the ground up for Hebrew right-to-left layout with proper text alignment and UI mirroring.
- **Comprehensive Accessibility** - A dedicated A11y menu offering:
    - Grayscale & High Contrast modes
    - Text resizing & Link highlighting
    - Reduced motion preferences
    - Full screen reader support
- **Demo Mode Forms** - Form submissions are simulated for portfolio demonstration.
- **Security Hardened** - Content Security Policy headers, input validation with Israeli phone format support.
- **Conversion Focused** - Strategic CTA placement, trust indicators, and social proof elements.

## Architecture & Technical Decisions

### 1. Adoption of Tailwind CSS v4
Implemented the latest Tailwind v4 features leveraging the new engine:
- **Zero-config approach:** Configuration handled via native CSS variables and `@theme` directives in `index.css`.
- **Performance:** Utilizing the `@tailwindcss/vite` plugin for instant HMR and smaller bundle sizes.

### 2. Smart Component Loading Strategy
```
App
├─ DemoBadge (floating indicator)
├─ AccessibilityMenu (global, always available)
├─ Navbar (eager, sticky with intelligent scroll hide/show)
└─ Suspense
    ├─ Hero (eager load - critical for LCP)
    └─ Lazy sections: ProblemSolution, Credibility,
       Services, Process, FAQ, Contact, Footer
```

### 3. Centralized Content Management
To allow for easy updates without touching the codebase:
- All textual content, image paths, and data are managed in a strict TypeScript structure (`src/data/content.ts`).
- Components consume this data, making the UI purely presentational and reusable.

### 4. Custom Hooks & Utilities
Instead of relying on heavy third-party libraries:
- **`useEmailForm`**: Custom hook for form handling with demo mode simulation.
- **`useScrollSpy`**: For updating navigation state based on viewport position.
- **Validation Utils**: Israeli phone number (9-10 digits) and email validation patterns.

## Design System

The design reflects a professional, trustworthy brand:

- **Palette:**
  - **Primary Blue** (`#4A90D9`) - Trust & professionalism
  - **Gold Accent** (`#E8A849`) - CTAs & highlights
  - **Clean backgrounds** - Modern, uncluttered feel

- **Typography:** Self-hosted Hebrew fonts optimized for legibility and performance.

- **Animation:** Premium easing curves (`cubic-bezier(0.43, 0.13, 0.23, 0.96)`) with configurable durations respecting `prefers-reduced-motion`.

## Performance Metrics

| Metric | Score |
|--------|-------|
| Lighthouse Performance | 90+ |
| First Contentful Paint | < 1.5s |
| Cumulative Layout Shift | < 0.1 |

---

## Developed By

**Sagi Menahem**

I am a final-year Computer Science student (B.Sc.) passionate about building polished, high-performance web experiences.

[![GitHub](https://img.shields.io/badge/GitHub-sagiia-181717?style=flat-square&logo=github)](https://github.com/sagiia)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=flat-square&logo=linkedin)](https://www.linkedin.com/in/sagi-menachem/)

---

<div align="center">
  <small>Code by Sagi Menahem. This is a portfolio demonstration.</small>
</div>
