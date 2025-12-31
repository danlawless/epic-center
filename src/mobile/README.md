# Transformational Epicenter - Mobile App

> *The guest's companion through transformation*

---

## Overview

The mobile app is the primary touchpoint for guests throughout their transformation journey - from pre-arrival preparation through lifelong integration and community connection.

**Stack**: React Native, Expo, TypeScript

---

## Getting Started

```bash
# Install dependencies
pnpm install

# Start Expo development server
pnpm start

# Run on iOS simulator
pnpm ios

# Run on Android emulator
pnpm android

# Run on physical device
# Scan QR code from Expo Go app
```

---

## Project Structure

```
/mobile
├── /app                    # Expo Router (file-based routing)
│   ├── _layout.tsx         # Root layout
│   ├── (auth)              # Auth screens
│   │   ├── login.tsx
│   │   └── register.tsx
│   ├── (tabs)              # Main tab navigation
│   │   ├── home.tsx
│   │   ├── journey.tsx
│   │   ├── community.tsx
│   │   └── profile.tsx
│   ├── onboarding/         # Onboarding flow
│   ├── precare/            # Pre-care screens
│   ├── onsite/             # On-site screens
│   └── integration/        # Integration screens
│
├── /components             # React Native components
│   ├── /ui                 # Base UI components
│   ├── /forms              # Form components
│   ├── /cards              # Card components
│   └── /navigation         # Navigation components
│
├── /hooks                  # Custom hooks
│   ├── useAuth.ts
│   ├── useJourney.ts
│   └── usePractices.ts
│
├── /services               # API and external services
│   ├── api.ts              # API client
│   ├── auth.ts             # Auth service
│   └── storage.ts          # Secure storage
│
├── /stores                 # State management (Zustand)
│   ├── auth.ts
│   ├── journey.ts
│   └── practices.ts
│
├── /utils                  # Utility functions
│   ├── date.ts
│   ├── validation.ts
│   └── formatting.ts
│
├── /assets                 # Static assets
│   ├── /images
│   ├── /fonts
│   └── /icons
│
└── /constants              # App constants
    ├── colors.ts
    ├── spacing.ts
    └── config.ts
```

---

## App Sections

### Authentication

- Login
- Registration
- Password reset
- Biometric authentication

### Onboarding

- Welcome flow
- Account creation
- Medical intake wizard
- Document upload
- Consent signing

### Pre-Care (Journey Stage: Preparation)

- Preparation dashboard
- Daily practices
- Educational content
- Checklist progress
- Countdown to arrival
- Facilitator messaging

### On-Site (Journey Stage: Retreat)

- Daily schedule
- Current activity
- Service booking (massage, bio-optimization)
- Meal information
- Facilitator connection
- Emergency contact

### Integration (Journey Stage: Post-Retreat)

- Integration dashboard
- Practice recommendations
- Journal/reflection
- Progress tracking
- Coaching scheduling
- Community access

### Community

- Cohort groups
- Discussion forums
- Direct messaging
- Events calendar
- Resource library

### Profile

- Personal information
- Health data summary
- Journey timeline
- Preferences
- Settings
- Support access

---

## Key Features

### Offline Support

```typescript
// Critical data cached for offline access
const offlineData = {
  schedule: true,        // Today's schedule
  practices: true,       // Active practices
  content: true,         // Downloaded content
  messages: 'queue',     // Queue for sync
};
```

### Push Notifications

```typescript
// Notification categories
enum NotificationType {
  PRACTICE_REMINDER = 'practice_reminder',
  SCHEDULE_UPDATE = 'schedule_update',
  MESSAGE_RECEIVED = 'message_received',
  COMMUNITY_UPDATE = 'community_update',
  IMPORTANT_ALERT = 'important_alert',
}
```

### Secure Storage

```typescript
// Encrypted storage for sensitive data
import * as SecureStore from 'expo-secure-store';

// PHI stored securely
await SecureStore.setItemAsync('medical_summary', encryptedData);
```

---

## Environment Variables

```bash
# .env

# API
API_URL=http://localhost:3001
WEBSOCKET_URL=ws://localhost:3001

# Auth
AUTH0_DOMAIN=
AUTH0_CLIENT_ID=

# Push Notifications
EXPO_PUSH_TOKEN=

# Analytics (if used)
MIXPANEL_TOKEN=

# Feature Flags
ENABLE_OFFLINE_MODE=true
ENABLE_BIOMETRIC_AUTH=true
```

---

## Development

### Commands

```bash
# Development
pnpm start              # Start Expo dev server
pnpm ios                # Run iOS simulator
pnpm android            # Run Android emulator

# Testing
pnpm test               # Run tests
pnpm test:watch         # Watch mode
pnpm test:coverage      # Coverage report

# Code Quality
pnpm lint               # Run linting
pnpm type-check         # TypeScript check

# Build
pnpm build:ios          # Build iOS
pnpm build:android      # Build Android
```

### Testing on Device

1. Install Expo Go on device
2. Run `pnpm start`
3. Scan QR code with device camera (iOS) or Expo Go (Android)

---

## State Management

Using Zustand for lightweight, performant state:

```typescript
// stores/journey.ts
import { create } from 'zustand';

interface JourneyState {
  currentStage: JourneyStage;
  practices: Practice[];
  setStage: (stage: JourneyStage) => void;
}

export const useJourneyStore = create<JourneyState>((set) => ({
  currentStage: 'preparation',
  practices: [],
  setStage: (stage) => set({ currentStage: stage }),
}));
```

---

## Navigation

Using Expo Router (file-based routing):

```
/app
├── (auth)/             # Auth group (no tabs)
├── (tabs)/             # Main tab group
│   ├── _layout.tsx     # Tab navigator setup
│   ├── home.tsx        # Home tab
│   ├── journey.tsx     # Journey tab
│   ├── community.tsx   # Community tab
│   └── profile.tsx     # Profile tab
└── [...]               # Other routes
```

---

## Design System

See [Design System](../../design/DESIGN_SYSTEM.md) for full specifications.

### Theme

```typescript
// constants/theme.ts
export const theme = {
  colors: {
    primary: '#1A3A3A',
    secondary: '#C4A962',
    background: '#FAFAF8',
    surface: '#FFFFFF',
    text: '#1A1A1A',
    textSecondary: '#6B7280',
  },
  spacing: {
    xs: 4,
    sm: 8,
    md: 16,
    lg: 24,
    xl: 32,
  },
  borderRadius: {
    sm: 4,
    md: 8,
    lg: 16,
  },
};
```

---

## Deployment

### App Store (iOS)

```bash
# Build for App Store
eas build --platform ios --profile production

# Submit to App Store
eas submit --platform ios
```

### Google Play (Android)

```bash
# Build for Play Store
eas build --platform android --profile production

# Submit to Play Store
eas submit --platform android
```

---

## Security Considerations

- Biometric authentication
- Secure token storage
- Certificate pinning
- Data encryption at rest
- PHI handling compliance
- Session management

---

**Version**: 1.0.0
**Last Updated**: December 2024
