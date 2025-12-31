# Transformational Epicenter - Source Code

> *The digital ecosystem powering transformation*

---

## Overview

This directory contains all source code for the Transformational Epicenter digital platform.

```
/src
├── /web          # Next.js marketing website
├── /mobile       # React Native guest app
├── /api          # NestJS backend services
├── /shared       # Shared types, utilities, and components
└── /admin        # Next.js admin dashboard
```

---

## Quick Start

### Prerequisites

- Node.js 20+
- pnpm 8+
- Docker (for local development)
- iOS development: macOS + Xcode
- Android development: Android Studio

### Development Setup

```bash
# Clone and install
git clone <repository>
cd epic-center
pnpm install

# Start all services in development
pnpm dev

# Or start individual services
pnpm --filter @epicenter/web dev
pnpm --filter @epicenter/api dev
pnpm --filter @epicenter/mobile start
pnpm --filter @epicenter/admin dev
```

### Environment Setup

```bash
# Copy example environment files
cp .env.example .env.local

# Required environment variables (see each package for specifics)
DATABASE_URL=
REDIS_URL=
AUTH0_DOMAIN=
AUTH0_CLIENT_ID=
# ... see .env.example for full list
```

---

## Project Structure

### `/web` - Marketing Website

**Stack**: Next.js 14, React 18, TypeScript, Tailwind CSS

**Purpose**: Public-facing website for brand, information, and applications

```
/web
├── /app              # Next.js app router pages
├── /components       # React components
├── /lib              # Utilities and helpers
├── /public           # Static assets
└── /styles           # Global styles
```

### `/mobile` - Guest Mobile App

**Stack**: React Native, Expo, TypeScript

**Purpose**: Primary guest interface for all journey phases

```
/mobile
├── /app              # Expo Router screens
├── /components       # React Native components
├── /hooks            # Custom hooks
├── /services         # API services
├── /stores           # State management
└── /utils            # Utilities
```

### `/api` - Backend Services

**Stack**: NestJS, TypeScript, PostgreSQL, Redis

**Purpose**: API services powering all applications

```
/api
├── /src
│   ├── /modules      # Feature modules
│   │   ├── /auth     # Authentication
│   │   ├── /users    # User management
│   │   ├── /medical  # Medical records
│   │   ├── /journey  # Guest journeys
│   │   ├── /content  # Content delivery
│   │   └── /...      # Other modules
│   ├── /common       # Shared utilities
│   └── /config       # Configuration
├── /prisma           # Database schema
└── /test             # Tests
```

### `/shared` - Shared Code

**Purpose**: Types, utilities, and components shared across packages

```
/shared
├── /types            # TypeScript type definitions
├── /utils            # Shared utilities
├── /constants        # Shared constants
└── /validation       # Validation schemas
```

### `/admin` - Admin Dashboard

**Stack**: Next.js 14, React 18, TypeScript, Tailwind CSS

**Purpose**: Staff and admin interface for operations

```
/admin
├── /app              # Next.js app router pages
├── /components       # React components
├── /lib              # Utilities
└── /features         # Feature modules
```

---

## Development Guidelines

### Code Style

- **TypeScript**: Strict mode enabled
- **Formatting**: Prettier with project config
- **Linting**: ESLint with project config
- **Commits**: Conventional commits

### Testing

```bash
# Run all tests
pnpm test

# Run specific package tests
pnpm --filter @epicenter/api test
pnpm --filter @epicenter/web test

# Run with coverage
pnpm test:coverage
```

### Building

```bash
# Build all packages
pnpm build

# Build specific package
pnpm --filter @epicenter/api build
```

---

## Security

### HIPAA Compliance

- All PHI encrypted at rest and in transit
- Audit logging for all PHI access
- Role-based access control
- Secure authentication via Auth0

### Best Practices

- No secrets in code
- Input validation on all endpoints
- Output encoding for XSS prevention
- Parameterized queries for SQL injection prevention
- Regular dependency audits

---

## Deployment

### Environments

| Environment | URL | Branch |
|-------------|-----|--------|
| Development | localhost | feature/* |
| Staging | staging.epicenter.com | develop |
| Production | app.epicenter.com | main |

### CI/CD

- GitHub Actions for CI
- Automated testing on PR
- Staging deploy on merge to develop
- Production deploy on merge to main

---

## Related Documentation

- [System Architecture](../docs/architecture/SYSTEM_OVERVIEW.md)
- [Data Architecture](../docs/architecture/DATA_ARCHITECTURE.md)
- [Security & Compliance](../docs/architecture/SECURITY_COMPLIANCE.md)
- [Design System](../design/DESIGN_SYSTEM.md)

---

**Version**: 1.0.0
**Last Updated**: December 2024
