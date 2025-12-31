# Data Architecture

> *The foundation of guest safety and transformation tracking*

---

## Overview

The data architecture for Transformational Epicenter prioritizes three core concerns:
1. **Security & Compliance** - HIPAA/GDPR compliant medical data handling
2. **Guest Journey Continuity** - Complete view of the transformation arc
3. **Operational Efficiency** - Support for staff and clinical workflows

---

## Data Classification

### Tier 1: Protected Health Information (PHI)
**Highest sensitivity - Maximum protection required**

- Medical intake forms
- Health screenings and results
- Cardiac monitoring data
- Blood work and lab results
- Medication history
- Treatment notes
- Progress assessments

**Security Requirements**:
- AES-256 encryption at rest
- TLS 1.3 in transit
- Row-level security
- Comprehensive audit logging
- Role-based access control
- Data masking in non-production

### Tier 2: Personally Identifiable Information (PII)
**High sensitivity - Strong protection required**

- Full name and demographics
- Contact information
- Emergency contacts
- Payment information
- Identity documents

**Security Requirements**:
- Encryption at rest
- Access logging
- Minimal exposure principle
- Anonymization for analytics

### Tier 3: Operational Data
**Standard sensitivity - Normal protection**

- Schedules and bookings
- Content and resources
- Staff assignments
- General communications

### Tier 4: Analytics Data
**Low sensitivity - Aggregated and anonymized**

- Engagement metrics
- Outcome measurements
- Operational KPIs
- Anonymized research data

---

## Core Data Models

### User Domain

```
┌─────────────────────────────────────────────────────────────────────┐
│                           USER DOMAIN                                │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  ┌───────────────┐       ┌───────────────┐       ┌───────────────┐  │
│  │     User      │       │    Profile    │       │  Preferences  │  │
│  ├───────────────┤       ├───────────────┤       ├───────────────┤  │
│  │ id            │──────▶│ userId        │──────▶│ userId        │  │
│  │ email         │       │ firstName     │       │ notifications │  │
│  │ authProviderId│       │ lastName      │       │ timezone      │  │
│  │ role          │       │ dateOfBirth   │       │ language      │  │
│  │ status        │       │ phone         │       │ accessibility │  │
│  │ createdAt     │       │ address       │       │ privacy       │  │
│  │ updatedAt     │       │ emergencyContact│     │               │  │
│  └───────────────┘       │ avatarUrl     │       └───────────────┘  │
│                          └───────────────┘                           │
│                                                                       │
└─────────────────────────────────────────────────────────────────────┘
```

### Medical Domain

```
┌─────────────────────────────────────────────────────────────────────┐
│                         MEDICAL DOMAIN                               │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  ┌───────────────┐       ┌───────────────┐       ┌───────────────┐  │
│  │ MedicalRecord │       │   Screening   │       │    Vitals     │  │
│  ├───────────────┤       ├───────────────┤       ├───────────────┤  │
│  │ id            │       │ id            │       │ id            │  │
│  │ guestId       │──────▶│ recordId      │       │ recordId      │  │
│  │ intakeDate    │       │ type          │       │ timestamp     │  │
│  │ primaryPhysician│     │ conductedBy   │       │ heartRate     │  │
│  │ allergies     │       │ date          │       │ bloodPressure │  │
│  │ medications   │       │ results       │       │ temperature   │  │
│  │ conditions    │       │ clearance     │       │ oxygenSat     │  │
│  │ status        │       │ notes         │       │ source        │  │
│  └───────────────┘       └───────────────┘       └───────────────┘  │
│         │                                                             │
│         │                ┌───────────────┐       ┌───────────────┐  │
│         │                │  LabResults   │       │TreatmentNotes │  │
│         │                ├───────────────┤       ├───────────────┤  │
│         └───────────────▶│ id            │       │ id            │  │
│                          │ recordId      │       │ recordId      │  │
│                          │ type          │       │ journeyId     │  │
│                          │ date          │       │ authorId      │  │
│                          │ results       │       │ date          │  │
│                          │ referenceRange│       │ noteType      │  │
│                          │ interpretation│       │ content       │  │
│                          └───────────────┘       │ visibility    │  │
│                                                  └───────────────┘  │
└─────────────────────────────────────────────────────────────────────┘
```

### Journey Domain

```
┌─────────────────────────────────────────────────────────────────────┐
│                         JOURNEY DOMAIN                               │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  ┌───────────────┐       ┌───────────────┐       ┌───────────────┐  │
│  │    Journey    │       │     Stage     │       │   Milestone   │  │
│  ├───────────────┤       ├───────────────┤       ├───────────────┤  │
│  │ id            │──────▶│ id            │──────▶│ id            │  │
│  │ guestId       │       │ journeyId     │       │ stageId       │  │
│  │ programId     │       │ type          │       │ type          │  │
│  │ locationId    │       │ status        │       │ title         │  │
│  │ startDate     │       │ startDate     │       │ completedAt   │  │
│  │ endDate       │       │ endDate       │       │ data          │  │
│  │ status        │       │ facilitatorId │       └───────────────┘  │
│  │ assignedTeam  │       │ notes         │                          │
│  └───────────────┘       └───────────────┘                          │
│         │                                                             │
│         │                ┌───────────────┐       ┌───────────────┐  │
│         │                │    Program    │       │   Intention   │  │
│         │                ├───────────────┤       ├───────────────┤  │
│         └───────────────▶│ id            │       │ id            │  │
│                          │ name          │       │ journeyId     │  │
│                          │ duration      │       │ content       │  │
│                          │ description   │       │ createdAt     │  │
│                          │ phases        │       │ category      │  │
│                          │ inclusions    │       │ reflection    │  │
│                          └───────────────┘       └───────────────┘  │
└─────────────────────────────────────────────────────────────────────┘
```

### Scheduling Domain

```
┌─────────────────────────────────────────────────────────────────────┐
│                       SCHEDULING DOMAIN                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  ┌───────────────┐       ┌───────────────┐       ┌───────────────┐  │
│  │  Appointment  │       │   Schedule    │       │ Availability  │  │
│  ├───────────────┤       ├───────────────┤       ├───────────────┤  │
│  │ id            │       │ id            │       │ id            │  │
│  │ guestId       │       │ date          │       │ resourceId    │  │
│  │ resourceId    │       │ locationId    │       │ resourceType  │  │
│  │ resourceType  │       │ entries[]     │       │ dayOfWeek     │  │
│  │ startTime     │       │               │       │ startTime     │  │
│  │ endTime       │       │               │       │ endTime       │  │
│  │ type          │       │               │       │ exceptions[]  │  │
│  │ status        │       │               │       │               │  │
│  │ notes         │       │               │       │               │  │
│  └───────────────┘       └───────────────┘       └───────────────┘  │
│                                                                       │
│  Resource Types: room, equipment, staff, treatment                   │
└─────────────────────────────────────────────────────────────────────┘
```

### Content Domain

```
┌─────────────────────────────────────────────────────────────────────┐
│                        CONTENT DOMAIN                                │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  ┌───────────────┐       ┌───────────────┐       ┌───────────────┐  │
│  │    Content    │       │   Practice    │       │   Resource    │  │
│  ├───────────────┤       ├───────────────┤       ├───────────────┤  │
│  │ id            │       │ id            │       │ id            │  │
│  │ title         │       │ title         │       │ title         │  │
│  │ type          │       │ category      │       │ type          │  │
│  │ category      │       │ duration      │       │ url           │  │
│  │ body          │       │ instructions  │       │ fileType      │  │
│  │ mediaUrl      │       │ audioUrl      │       │ size          │  │
│  │ stage         │       │ videoUrl      │       │ stage         │  │
│  │ visibility    │       │ stage         │       │ visibility    │  │
│  │ tags[]        │       │ frequency     │       │               │  │
│  └───────────────┘       └───────────────┘       └───────────────┘  │
│                                                                       │
│  Content Types: article, video, audio, guide                         │
│  Practice Categories: breathwork, meditation, movement, journaling  │
└─────────────────────────────────────────────────────────────────────┘
```

### Community Domain

```
┌─────────────────────────────────────────────────────────────────────┐
│                       COMMUNITY DOMAIN                               │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  ┌───────────────┐       ┌───────────────┐       ┌───────────────┐  │
│  │     Group     │       │    Message    │       │     Event     │  │
│  ├───────────────┤       ├───────────────┤       ├───────────────┤  │
│  │ id            │       │ id            │       │ id            │  │
│  │ name          │       │ groupId       │       │ title         │  │
│  │ type          │       │ senderId      │       │ description   │  │
│  │ description   │       │ content       │       │ startTime     │  │
│  │ members[]     │       │ attachments[] │       │ endTime       │  │
│  │ moderators[]  │       │ createdAt     │       │ type          │  │
│  │ visibility    │       │ readBy[]      │       │ hostId        │  │
│  │ cohortId      │       │               │       │ attendees[]   │  │
│  └───────────────┘       └───────────────┘       │ meetingUrl    │  │
│                                                  └───────────────┘  │
│                                                                       │
│  Group Types: cohort, alumni, interest, support                      │
│  Event Types: workshop, circle, q&a, celebration                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Entity Relationship Diagram

```
                                    ┌─────────────┐
                                    │    User     │
                                    └──────┬──────┘
                                           │
                    ┌──────────────────────┼──────────────────────┐
                    │                      │                      │
             ┌──────▼──────┐        ┌──────▼──────┐       ┌──────▼──────┐
             │   Profile   │        │MedicalRecord│       │   Journey   │
             └─────────────┘        └──────┬──────┘       └──────┬──────┘
                                           │                      │
                              ┌────────────┼────────────┐         │
                              │            │            │         │
                       ┌──────▼──┐  ┌──────▼──┐  ┌──────▼──┐     │
                       │Screening│  │LabResult│  │ Vitals  │      │
                       └─────────┘  └─────────┘  └─────────┘      │
                                                                   │
                              ┌────────────────────────────────────┘
                              │
               ┌──────────────┼──────────────┬──────────────┐
               │              │              │              │
        ┌──────▼──────┐ ┌─────▼─────┐ ┌──────▼──────┐ ┌─────▼─────┐
        │    Stage    │ │ Intention │ │TreatmentNote│ │Appointment│
        └──────┬──────┘ └───────────┘ └─────────────┘ └───────────┘
               │
        ┌──────▼──────┐
        │  Milestone  │
        └─────────────┘
```

---

## Database Schema Patterns

### Multi-Tenancy (Location-Based)

```sql
-- Every major table includes location context
CREATE TABLE journeys (
    id UUID PRIMARY KEY,
    location_id UUID NOT NULL REFERENCES locations(id),
    guest_id UUID NOT NULL REFERENCES users(id),
    -- ... other fields

    -- Row-level security for multi-tenancy
    CONSTRAINT location_access CHECK (location_id IS NOT NULL)
);

-- Enable RLS
ALTER TABLE journeys ENABLE ROW LEVEL SECURITY;

-- Policy for location-scoped access
CREATE POLICY location_isolation ON journeys
    USING (location_id = current_setting('app.current_location')::UUID);
```

### Audit Logging

```sql
-- Audit trail for all PHI access
CREATE TABLE audit_logs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    timestamp TIMESTAMPTZ DEFAULT NOW(),
    user_id UUID REFERENCES users(id),
    action VARCHAR(50) NOT NULL,
    resource_type VARCHAR(50) NOT NULL,
    resource_id UUID NOT NULL,
    ip_address INET,
    user_agent TEXT,
    changes JSONB,

    -- Immutable audit log
    CONSTRAINT no_updates CHECK (TRUE)
);

-- Prevent updates/deletes on audit log
CREATE RULE audit_no_update AS ON UPDATE TO audit_logs DO INSTEAD NOTHING;
CREATE RULE audit_no_delete AS ON DELETE TO audit_logs DO INSTEAD NOTHING;
```

### Soft Deletes

```sql
-- All PHI uses soft delete for compliance
CREATE TABLE medical_records (
    id UUID PRIMARY KEY,
    -- ... fields ...
    deleted_at TIMESTAMPTZ,
    deleted_by UUID REFERENCES users(id),

    -- Soft delete index
    CONSTRAINT active_records CHECK (deleted_at IS NULL OR deleted_at > '2020-01-01')
);

CREATE INDEX idx_active_medical_records
    ON medical_records(guest_id)
    WHERE deleted_at IS NULL;
```

### Encryption

```sql
-- Encrypted PHI fields using pgcrypto
CREATE TABLE medical_records (
    id UUID PRIMARY KEY,
    guest_id UUID NOT NULL,

    -- Encrypted with application-level key
    allergies_encrypted BYTEA,
    medications_encrypted BYTEA,
    conditions_encrypted BYTEA,

    -- Search-optimized hashes for lookup
    allergies_hash VARCHAR(64),

    -- Metadata (not encrypted)
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);
```

---

## Data Flows

### Guest Onboarding Flow

```
┌─────────┐     ┌─────────┐     ┌─────────┐     ┌─────────┐
│  User   │     │ Profile │     │ Medical │     │ Journey │
│ Created │────▶│ Created │────▶│ Intake  │────▶│ Created │
└─────────┘     └─────────┘     └─────────┘     └─────────┘
     │               │               │               │
     │               │               │               │
     ▼               ▼               ▼               ▼
┌─────────┐     ┌─────────┐     ┌─────────┐     ┌─────────┐
│ Auth0   │     │   PG    │     │ PG+Enc  │     │   PG    │
│ Record  │     │ Record  │     │ Record  │     │ Record  │
└─────────┘     └─────────┘     └─────────┘     └─────────┘
```

### Medical Data Flow

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│    Guest     │     │   API        │     │   Database   │
│  Mobile App  │────▶│  Service     │────▶│  (Encrypted) │
└──────────────┘     └──────────────┘     └──────────────┘
       │                    │                     │
       │                    │                     │
       ▼                    ▼                     ▼
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Local Cache  │     │  Audit Log   │     │  Backup      │
│ (Encrypted)  │     │              │     │  (Encrypted) │
└──────────────┘     └──────────────┘     └──────────────┘
```

---

## Data Retention

| Data Category | Retention Period | Deletion Method |
|--------------|------------------|-----------------|
| Medical Records | 7 years post-treatment | Secure deletion |
| User Accounts | Until deletion request | Anonymization |
| Audit Logs | 7 years | Archive to cold storage |
| Analytics | 3 years | Aggregation |
| Session Data | 30 days | Automatic purge |
| Temporary Files | 24 hours | Automatic purge |

---

## Data Access Patterns

### By Role

| Role | PHI Access | PII Access | Operations | Analytics |
|------|------------|------------|------------|-----------|
| Guest | Own only | Own only | Own only | None |
| Facilitator | Assigned guests | Assigned | Location | None |
| Medical Staff | Full | Full | Location | Limited |
| Admin | Full | Full | Full | Full |
| System | Audit only | None | Full | Full |

### API Access Patterns

```typescript
// Example: Guest medical record access
interface MedicalRecordAccess {
  // Can only access own records
  getMyRecords(): MedicalRecord[];

  // Can submit new intake data
  submitIntake(data: IntakeData): void;

  // Can view approved summaries
  getApprovedSummary(): MedicalSummary;
}

// Example: Medical staff access
interface MedicalStaffAccess {
  // Can access all guest records
  getGuestRecords(guestId: string): MedicalRecord[];

  // Can add treatment notes
  addTreatmentNote(guestId: string, note: TreatmentNote): void;

  // Can update medical status
  updateMedicalStatus(guestId: string, status: MedicalStatus): void;
}
```

---

## Backup & Recovery

### Backup Strategy

```
┌─────────────────────────────────────────────────────────────┐
│                     Backup Architecture                       │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  Continuous      ┌──────────────────┐                        │
│  Replication ───▶│  RDS Read Replica │ (Real-time sync)      │
│                  └──────────────────┘                        │
│                                                               │
│  Hourly          ┌──────────────────┐                        │
│  Snapshots ─────▶│  S3 (Same Region) │ (Point-in-time)       │
│                  └──────────────────┘                        │
│                                                               │
│  Daily           ┌──────────────────┐                        │
│  Backups ───────▶│  S3 (Cross Region)│ (Disaster recovery)   │
│                  └──────────────────┘                        │
│                                                               │
│  Weekly          ┌──────────────────┐                        │
│  Archives ──────▶│  Glacier         │ (Long-term retention)  │
│                  └──────────────────┘                        │
└─────────────────────────────────────────────────────────────┘
```

### Recovery Procedures

1. **Point-in-Time Recovery**: Restore from continuous backups (< 1 hour)
2. **Same-Region Recovery**: Restore from hourly snapshots (< 4 hours)
3. **Cross-Region Recovery**: Restore from daily backups (< 24 hours)
4. **Archive Recovery**: Restore from weekly archives (< 48 hours)

---

**Version**: 1.0.0
**Last Updated**: December 2024
