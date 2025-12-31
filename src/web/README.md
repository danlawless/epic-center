# Transformational Epicenter - Web (Marketing Site)

> *The public face of transformation*

---

## Overview

The marketing website serves as the public entry point for Transformational Epicenter, providing information about our programs, approach, and facilitating guest applications.

**Stack**: Next.js 14, React 18, TypeScript, Tailwind CSS

---

## Getting Started

```bash
# Install dependencies
pnpm install

# Start development server
pnpm dev

# Build for production
pnpm build

# Start production server
pnpm start
```

---

## Project Structure

```
/web
├── /app                    # Next.js App Router
│   ├── layout.tsx          # Root layout
│   ├── page.tsx            # Homepage
│   ├── /about              # About pages
│   ├── /programs           # Program pages
│   ├── /experience         # The experience
│   ├── /science            # Science & safety
│   ├── /apply              # Application flow
│   ├── /blog               # Blog/resources
│   └── /api                # API routes
│
├── /components             # React components
│   ├── /ui                 # Base UI components
│   ├── /layout             # Layout components
│   ├── /forms              # Form components
│   └── /sections           # Page sections
│
├── /lib                    # Utilities
│   ├── /api                # API client
│   ├── /hooks              # Custom hooks
│   └── /utils              # Helper functions
│
├── /public                 # Static assets
│   ├── /images             # Images
│   ├── /fonts              # Fonts
│   └── /icons              # Icons
│
└── /styles                 # Global styles
    └── globals.css         # Tailwind + custom
```

---

## Pages

### Core Pages

| Route | Purpose |
|-------|---------|
| `/` | Homepage - Vision and entry point |
| `/about` | Our story, team, values |
| `/programs` | Program overview and details |
| `/programs/[slug]` | Individual program pages |
| `/experience` | What to expect |
| `/science` | Medical approach, safety |
| `/apply` | Application flow |
| `/contact` | Contact information |

### Content Pages

| Route | Purpose |
|-------|---------|
| `/blog` | Articles and resources |
| `/blog/[slug]` | Individual articles |
| `/faq` | Frequently asked questions |
| `/privacy` | Privacy policy |
| `/terms` | Terms of service |

---

## Key Features

### Application Flow

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Inquiry   │────▶│   Medical   │────▶│   Review    │
│    Form     │     │   Intake    │     │  & Accept   │
└─────────────┘     └─────────────┘     └─────────────┘
```

### Content Management

- Headless CMS integration (Contentful/Sanity)
- Dynamic page generation
- SEO optimization
- Blog/article management

### Performance

- Static generation where possible
- Image optimization
- Font optimization
- Lazy loading
- Core Web Vitals optimization

---

## Environment Variables

```bash
# .env.local

# API
NEXT_PUBLIC_API_URL=http://localhost:3001

# CMS
CONTENTFUL_SPACE_ID=
CONTENTFUL_ACCESS_TOKEN=
CONTENTFUL_PREVIEW_TOKEN=

# Analytics
NEXT_PUBLIC_GA_ID=
NEXT_PUBLIC_GTM_ID=

# Forms
NEXT_PUBLIC_FORM_ENDPOINT=

# Feature flags
NEXT_PUBLIC_ENABLE_APPLICATIONS=true
```

---

## Development

### Commands

```bash
# Development
pnpm dev                 # Start dev server
pnpm lint               # Run linting
pnpm type-check         # TypeScript check

# Testing
pnpm test               # Run tests
pnpm test:watch         # Watch mode

# Production
pnpm build              # Build for production
pnpm start              # Start production server
pnpm analyze            # Bundle analysis
```

### Code Quality

- ESLint + Prettier for formatting
- Husky for pre-commit hooks
- TypeScript strict mode
- Lighthouse CI for performance

---

## Deployment

### Vercel (Recommended)

```bash
# Deploy to Vercel
vercel

# Deploy to production
vercel --prod
```

### Self-Hosted

```bash
# Build
pnpm build

# Start with PM2
pm2 start npm --name "epicenter-web" -- start
```

---

## Component Library

See [Design System](../../design/DESIGN_SYSTEM.md) for component specifications.

### Base Components

- Button
- Input
- Select
- Textarea
- Card
- Modal
- Navigation

### Section Components

- Hero
- Features
- Testimonials
- Programs
- Team
- FAQ
- CTA

---

**Version**: 1.0.0
**Last Updated**: December 2024
