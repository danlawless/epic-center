# Transformational Epicenter - Shared

> *Common code powering the ecosystem*

---

## Overview

Shared types, utilities, and constants used across all Transformational Epicenter applications.

---

## Structure

```
/shared
├── /types              # TypeScript type definitions
│   ├── user.ts
│   ├── journey.ts
│   ├── medical.ts
│   ├── content.ts
│   ├── community.ts
│   └── index.ts
│
├── /utils              # Shared utilities
│   ├── date.ts
│   ├── validation.ts
│   ├── formatting.ts
│   └── index.ts
│
├── /constants          # Shared constants
│   ├── roles.ts
│   ├── stages.ts
│   ├── status.ts
│   └── index.ts
│
└── /validation         # Zod schemas
    ├── user.schema.ts
    ├── medical.schema.ts
    └── index.ts
```

---

## Types

### User Types

```typescript
// types/user.ts
export interface User {
  id: string;
  email: string;
  role: Role;
  status: UserStatus;
  profile: Profile;
  createdAt: Date;
  updatedAt: Date;
}

export interface Profile {
  firstName: string;
  lastName: string;
  phone?: string;
  dateOfBirth?: Date;
  address?: Address;
  emergencyContact?: EmergencyContact;
  avatarUrl?: string;
}

export type Role =
  | 'guest'
  | 'facilitator'
  | 'medical_staff'
  | 'admin'
  | 'super_admin';

export type UserStatus =
  | 'active'
  | 'inactive'
  | 'pending'
  | 'suspended';
```

### Journey Types

```typescript
// types/journey.ts
export interface Journey {
  id: string;
  userId: string;
  programId: string;
  locationId: string;
  status: JourneyStatus;
  startDate: Date;
  endDate: Date;
  stages: Stage[];
  createdAt: Date;
}

export type JourneyStatus =
  | 'inquiry'
  | 'applied'
  | 'approved'
  | 'preparing'
  | 'active'
  | 'integrating'
  | 'completed';

export type JourneyStage =
  | 'pre_care'
  | 'on_site'
  | 'integration';
```

### Medical Types

```typescript
// types/medical.ts
export interface MedicalRecord {
  id: string;
  userId: string;
  status: MedicalStatus;
  intakeDate?: Date;
  clearanceDate?: Date;
  allergies?: string[];
  medications?: Medication[];
  conditions?: string[];
  screenings: Screening[];
}

export type MedicalStatus =
  | 'pending'
  | 'in_review'
  | 'cleared'
  | 'requires_action'
  | 'declined';
```

---

## Constants

### Roles

```typescript
// constants/roles.ts
export const ROLES = {
  GUEST: 'guest',
  FACILITATOR: 'facilitator',
  MEDICAL_STAFF: 'medical_staff',
  ADMIN: 'admin',
  SUPER_ADMIN: 'super_admin',
} as const;

export const ROLE_PERMISSIONS = {
  guest: ['read:own', 'update:own'],
  facilitator: ['read:assigned', 'update:assigned'],
  medical_staff: ['read:medical', 'update:medical'],
  admin: ['read:all', 'update:all'],
  super_admin: ['*'],
};
```

### Journey Stages

```typescript
// constants/stages.ts
export const JOURNEY_STAGES = {
  PRE_CARE: 'pre_care',
  ON_SITE: 'on_site',
  INTEGRATION: 'integration',
} as const;

export const STAGE_ORDER = ['pre_care', 'on_site', 'integration'];
```

---

## Utilities

### Date Utilities

```typescript
// utils/date.ts
export function formatDate(date: Date, format: string): string;
export function daysBetween(start: Date, end: Date): number;
export function addDays(date: Date, days: number): Date;
export function isWithinRange(date: Date, start: Date, end: Date): boolean;
```

### Validation Utilities

```typescript
// utils/validation.ts
export function isValidEmail(email: string): boolean;
export function isValidPhone(phone: string): boolean;
export function sanitizeInput(input: string): string;
```

### Formatting Utilities

```typescript
// utils/formatting.ts
export function formatName(first: string, last: string): string;
export function formatCurrency(amount: number, currency: string): string;
export function formatPhoneNumber(phone: string): string;
```

---

## Validation Schemas

Using Zod for runtime validation:

```typescript
// validation/user.schema.ts
import { z } from 'zod';

export const CreateUserSchema = z.object({
  email: z.string().email(),
  firstName: z.string().min(1).max(100),
  lastName: z.string().min(1).max(100),
  phone: z.string().optional(),
});

export const UpdateProfileSchema = z.object({
  firstName: z.string().min(1).max(100).optional(),
  lastName: z.string().min(1).max(100).optional(),
  phone: z.string().optional(),
  dateOfBirth: z.date().optional(),
});

export type CreateUserInput = z.infer<typeof CreateUserSchema>;
export type UpdateProfileInput = z.infer<typeof UpdateProfileSchema>;
```

---

## Usage

### In Applications

```typescript
// Import types
import type { User, Journey, MedicalRecord } from '@epicenter/shared';

// Import constants
import { ROLES, JOURNEY_STAGES } from '@epicenter/shared';

// Import utilities
import { formatDate, isValidEmail } from '@epicenter/shared';

// Import validation schemas
import { CreateUserSchema } from '@epicenter/shared';
```

---

**Version**: 1.0.0
**Last Updated**: December 2024
