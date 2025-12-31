# Transformational Epicenter - API

> *The backbone of the digital ecosystem*

---

## Overview

The API powers all Transformational Epicenter applications - mobile app, marketing site, and admin dashboard. Built with security, scalability, and HIPAA compliance as foundational requirements.

**Stack**: NestJS, TypeScript, PostgreSQL, Redis, Prisma

---

## Getting Started

```bash
# Install dependencies
pnpm install

# Set up database
docker-compose up -d postgres redis
pnpm prisma:migrate

# Seed development data
pnpm prisma:seed

# Start development server
pnpm dev

# API available at http://localhost:3001
```

---

## Project Structure

```
/api
├── /src
│   ├── /modules              # Feature modules
│   │   ├── /auth             # Authentication & authorization
│   │   ├── /users            # User management
│   │   ├── /medical          # Medical records (PHI)
│   │   ├── /journey          # Guest journey management
│   │   ├── /content          # Content delivery
│   │   ├── /scheduling       # Appointments & schedules
│   │   ├── /community        # Groups & messaging
│   │   ├── /notifications    # Push, email, SMS
│   │   └── /analytics        # Outcomes & metrics
│   │
│   ├── /common               # Shared code
│   │   ├── /decorators       # Custom decorators
│   │   ├── /guards           # Auth guards
│   │   ├── /interceptors     # Request interceptors
│   │   ├── /filters          # Exception filters
│   │   ├── /pipes            # Validation pipes
│   │   └── /utils            # Utility functions
│   │
│   ├── /config               # Configuration
│   │   ├── database.config.ts
│   │   ├── auth.config.ts
│   │   ├── redis.config.ts
│   │   └── app.config.ts
│   │
│   ├── app.module.ts         # Root module
│   └── main.ts               # Application entry
│
├── /prisma
│   ├── schema.prisma         # Database schema
│   ├── /migrations           # Database migrations
│   └── seed.ts               # Seed data
│
├── /test
│   ├── /e2e                  # End-to-end tests
│   └── /unit                 # Unit tests
│
└── /docker
    ├── Dockerfile
    └── docker-compose.yml
```

---

## Modules

### Auth Module (`/auth`)

Authentication and authorization using Auth0.

**Endpoints**:
| Method | Path | Description |
|--------|------|-------------|
| POST | `/auth/login` | Authenticate user |
| POST | `/auth/refresh` | Refresh token |
| POST | `/auth/logout` | Logout user |
| GET | `/auth/me` | Get current user |

### Users Module (`/users`)

User profile and account management.

**Endpoints**:
| Method | Path | Description |
|--------|------|-------------|
| GET | `/users/:id` | Get user profile |
| PATCH | `/users/:id` | Update profile |
| GET | `/users/:id/preferences` | Get preferences |
| PATCH | `/users/:id/preferences` | Update preferences |

### Medical Module (`/medical`) - PHI

Medical records and health data. HIPAA-compliant handling.

**Endpoints**:
| Method | Path | Description |
|--------|------|-------------|
| GET | `/medical/:userId` | Get medical record |
| POST | `/medical/:userId/intake` | Submit intake |
| POST | `/medical/:userId/screening` | Add screening |
| GET | `/medical/:userId/vitals` | Get vitals history |

**Security**:
- All endpoints require medical_read or medical_write permissions
- All access is audit logged
- Data encrypted at rest

### Journey Module (`/journey`)

Guest journey management and progression.

**Endpoints**:
| Method | Path | Description |
|--------|------|-------------|
| GET | `/journey/:userId` | Get current journey |
| POST | `/journey` | Create journey |
| PATCH | `/journey/:id/stage` | Update stage |
| GET | `/journey/:id/milestones` | Get milestones |

### Content Module (`/content`)

Educational content, practices, and resources.

**Endpoints**:
| Method | Path | Description |
|--------|------|-------------|
| GET | `/content` | List content |
| GET | `/content/:id` | Get content item |
| GET | `/content/practices` | Get practices |
| GET | `/content/resources` | Get resources |

### Scheduling Module (`/scheduling`)

Appointments and schedule management.

**Endpoints**:
| Method | Path | Description |
|--------|------|-------------|
| GET | `/schedule/:date` | Get daily schedule |
| GET | `/appointments` | List appointments |
| POST | `/appointments` | Create appointment |
| DELETE | `/appointments/:id` | Cancel appointment |

### Community Module (`/community`)

Groups, messaging, and events.

**Endpoints**:
| Method | Path | Description |
|--------|------|-------------|
| GET | `/groups` | List groups |
| GET | `/groups/:id/messages` | Get messages |
| POST | `/groups/:id/messages` | Send message |
| GET | `/events` | List events |

### Notifications Module (`/notifications`)

Push, email, and SMS notifications.

**Endpoints**:
| Method | Path | Description |
|--------|------|-------------|
| POST | `/notifications/push` | Send push |
| POST | `/notifications/email` | Send email |
| GET | `/notifications` | Get user notifications |

---

## Database Schema

Using Prisma ORM with PostgreSQL.

### Core Models

```prisma
model User {
  id            String    @id @default(uuid())
  email         String    @unique
  authProviderId String   @unique
  role          Role      @default(GUEST)
  status        Status    @default(ACTIVE)
  profile       Profile?
  medicalRecord MedicalRecord?
  journeys      Journey[]
  createdAt     DateTime  @default(now())
  updatedAt     DateTime  @updatedAt
}

model MedicalRecord {
  id              String    @id @default(uuid())
  userId          String    @unique
  user            User      @relation(fields: [userId], references: [id])
  // Encrypted fields
  allergiesEnc    Bytes?
  medicationsEnc  Bytes?
  conditionsEnc   Bytes?
  // Metadata
  status          MedicalStatus
  clearanceDate   DateTime?
  screenings      Screening[]
  vitals          Vitals[]
  createdAt       DateTime  @default(now())
  updatedAt       DateTime  @updatedAt
}

model Journey {
  id          String        @id @default(uuid())
  userId      String
  user        User          @relation(fields: [userId], references: [id])
  programId   String
  program     Program       @relation(fields: [programId], references: [id])
  locationId  String
  status      JourneyStatus
  startDate   DateTime
  endDate     DateTime
  stages      Stage[]
  createdAt   DateTime      @default(now())
  updatedAt   DateTime      @updatedAt
}
```

---

## Security

### Authentication

Using Auth0 for authentication:

```typescript
// JWT validation
@UseGuards(JwtAuthGuard)
@Get('protected')
async protectedRoute(@CurrentUser() user: User) {
  return user;
}
```

### Authorization

Role-based access control:

```typescript
// Role guard
@Roles(Role.MEDICAL_STAFF)
@UseGuards(RolesGuard)
@Get('medical/:userId')
async getMedicalRecord(@Param('userId') userId: string) {
  return this.medicalService.getRecord(userId);
}
```

### PHI Handling

```typescript
// Encrypted field handling
@Injectable()
export class MedicalService {
  async createRecord(data: CreateMedicalDto) {
    const encrypted = await this.encryptPHI(data);
    await this.auditLog.log('medical_create', userId);
    return this.prisma.medicalRecord.create({ data: encrypted });
  }
}
```

### Audit Logging

```typescript
// All PHI access logged
@Injectable()
export class AuditInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler) {
    const request = context.switchToHttp().getRequest();
    this.auditService.log({
      userId: request.user.id,
      action: request.method,
      resource: request.path,
      timestamp: new Date(),
    });
    return next.handle();
  }
}
```

---

## Environment Variables

```bash
# .env

# Application
NODE_ENV=development
PORT=3001

# Database
DATABASE_URL=postgresql://user:pass@localhost:5432/epicenter

# Redis
REDIS_URL=redis://localhost:6379

# Auth0
AUTH0_DOMAIN=your-domain.auth0.com
AUTH0_AUDIENCE=https://api.epicenter.com
AUTH0_CLIENT_ID=
AUTH0_CLIENT_SECRET=

# Encryption
ENCRYPTION_KEY=your-32-byte-key

# AWS
AWS_REGION=us-east-1
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_S3_BUCKET=

# External Services
SENDGRID_API_KEY=
TWILIO_ACCOUNT_SID=
TWILIO_AUTH_TOKEN=
STRIPE_SECRET_KEY=
```

---

## Development

### Commands

```bash
# Development
pnpm dev                    # Start with hot reload
pnpm debug                  # Start with debugger

# Database
pnpm prisma:migrate         # Run migrations
pnpm prisma:generate        # Generate client
pnpm prisma:studio          # Open Prisma Studio
pnpm prisma:seed            # Seed database

# Testing
pnpm test                   # Run unit tests
pnpm test:e2e               # Run e2e tests
pnpm test:cov               # Coverage report

# Build
pnpm build                  # Build for production
pnpm start:prod             # Start production
```

### Local Services

```yaml
# docker-compose.yml
services:
  postgres:
    image: postgres:15
    ports:
      - "5432:5432"
    environment:
      POSTGRES_DB: epicenter
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres

  redis:
    image: redis:7
    ports:
      - "6379:6379"
```

---

## API Documentation

OpenAPI/Swagger documentation available at `/api/docs` in development.

```typescript
// main.ts
const config = new DocumentBuilder()
  .setTitle('Transformational Epicenter API')
  .setVersion('1.0')
  .addBearerAuth()
  .build();

const document = SwaggerModule.createDocument(app, config);
SwaggerModule.setup('api/docs', app, document);
```

---

## Deployment

### Docker

```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN pnpm install --frozen-lockfile
COPY . .
RUN pnpm build
CMD ["node", "dist/main.js"]
```

### AWS ECS

See [Infrastructure documentation](../../docs/architecture/INFRASTRUCTURE.md) for deployment details.

---

**Version**: 1.0.0
**Last Updated**: December 2024
