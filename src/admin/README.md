# Transformational Epicenter - Admin Dashboard

> *The operations center for transformation*

---

## Overview

The admin dashboard provides staff and administrators with tools to manage guests, medical records, scheduling, content, and operations.

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

# Dashboard available at http://localhost:3002
```

---

## Project Structure

```
/admin
├── /app                    # Next.js App Router
│   ├── layout.tsx          # Root layout
│   ├── page.tsx            # Dashboard home
│   ├── /guests             # Guest management
│   ├── /medical            # Medical records
│   ├── /schedule           # Scheduling
│   ├── /content            # Content management
│   ├── /community          # Community moderation
│   ├── /staff              # Staff management
│   ├── /analytics          # Analytics & reports
│   └── /settings           # System settings
│
├── /components             # React components
│   ├── /ui                 # Base UI components
│   ├── /data               # Data display components
│   ├── /forms              # Form components
│   └── /layout             # Layout components
│
├── /features               # Feature modules
│   ├── /guests             # Guest feature
│   ├── /medical            # Medical feature
│   └── /...                # Other features
│
├── /lib                    # Utilities
│   ├── /api                # API client
│   └── /hooks              # Custom hooks
│
└── /styles                 # Styles
```

---

## Modules

### Guest Management

**Features**:
- Guest list with filtering
- Guest profile view
- Journey tracking
- Document management
- Communication history

**Routes**:
| Route | Description |
|-------|-------------|
| `/guests` | Guest list |
| `/guests/[id]` | Guest detail |
| `/guests/[id]/journey` | Journey view |
| `/guests/[id]/documents` | Documents |

### Medical Records

**Features**:
- Medical record access (authorized users)
- Screening management
- Clearance workflow
- Vitals monitoring
- Lab results

**Routes**:
| Route | Description |
|-------|-------------|
| `/medical` | Medical overview |
| `/medical/[guestId]` | Guest medical record |
| `/medical/screenings` | Screening queue |
| `/medical/clearances` | Clearance queue |

### Scheduling

**Features**:
- Daily schedule view
- Resource management
- Appointment booking
- Staff assignments
- Availability management

**Routes**:
| Route | Description |
|-------|-------------|
| `/schedule` | Schedule overview |
| `/schedule/day/[date]` | Daily view |
| `/schedule/resources` | Resource management |
| `/schedule/staff` | Staff schedules |

### Content Management

**Features**:
- Content library
- Practice management
- Resource management
- Content scheduling

**Routes**:
| Route | Description |
|-------|-------------|
| `/content` | Content library |
| `/content/practices` | Practices |
| `/content/resources` | Resources |

### Community

**Features**:
- Group management
- Message moderation
- Event management
- Member management

### Staff Management

**Features**:
- Staff directory
- Role management
- Schedule management
- Performance tracking

### Analytics

**Features**:
- Guest outcomes
- Operational metrics
- Financial reports
- Custom reports

---

## Role-Based Access

| Role | Access |
|------|--------|
| Facilitator | Assigned guests, schedule, community |
| Medical Staff | + Medical records, screenings |
| Admin | + All guests, staff, content |
| Super Admin | + Settings, roles, full access |

---

## Key Components

### Data Tables

```tsx
<DataTable
  columns={guestColumns}
  data={guests}
  pagination
  filtering
  sorting
  actions={['view', 'edit']}
/>
```

### Forms

```tsx
<Form schema={guestSchema} onSubmit={handleSubmit}>
  <FormField name="firstName" label="First Name" />
  <FormField name="lastName" label="Last Name" />
  <FormField name="email" type="email" label="Email" />
  <FormSubmit>Save</FormSubmit>
</Form>
```

### Metrics Cards

```tsx
<MetricCard
  title="Active Guests"
  value={24}
  change={+3}
  trend="up"
/>
```

---

## Real-Time Updates

WebSocket connection for live updates:

```typescript
// Live schedule updates
useScheduleSubscription((update) => {
  queryClient.invalidateQueries(['schedule', date]);
});

// Live guest status
useGuestSubscription(guestId, (update) => {
  setGuestData(update);
});
```

---

## Environment Variables

```bash
# .env.local

# API
NEXT_PUBLIC_API_URL=http://localhost:3001

# Auth
AUTH0_DOMAIN=
AUTH0_CLIENT_ID=
AUTH0_CLIENT_SECRET=
AUTH0_AUDIENCE=

# Feature Flags
NEXT_PUBLIC_ENABLE_MEDICAL=true
NEXT_PUBLIC_ENABLE_ANALYTICS=true
```

---

## Development

### Commands

```bash
pnpm dev          # Development server
pnpm build        # Production build
pnpm lint         # Linting
pnpm test         # Tests
```

---

## Security

- All routes protected by authentication
- Role-based access control
- HIPAA compliance for medical routes
- Audit logging for all actions
- Session timeout

---

**Version**: 1.0.0
**Last Updated**: December 2024
