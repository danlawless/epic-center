# Integration Architecture

> *Connecting systems to serve the complete guest journey*

---

## Integration Philosophy

Every integration must answer one question: **Does this serve the guest's transformation?**

We integrate with external systems when they:
1. Enhance guest safety (medical monitoring)
2. Improve guest experience (seamless booking)
3. Enable better care (data sharing with providers)
4. Support operations (staff efficiency)

---

## Integration Landscape

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                          INTEGRATION LANDSCAPE                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                               │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                         MEDICAL SYSTEMS                               │    │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐             │    │
│  │  │   EMR    │  │ Cardiac  │  │   Lab    │  │ Wearable │             │    │
│  │  │  System  │  │ Monitor  │  │ Systems  │  │ Devices  │             │    │
│  │  └──────────┘  └──────────┘  └──────────┘  └──────────┘             │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                               │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                       COMMUNICATION SYSTEMS                           │    │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐             │    │
│  │  │  Email   │  │   SMS    │  │  Video   │  │   Push   │             │    │
│  │  │ (SendGrid│  │ (Twilio) │  │  (Zoom)  │  │  (FCM/   │             │    │
│  │  │   /SES)  │  │          │  │          │  │   APNs)  │             │    │
│  │  └──────────┘  └──────────┘  └──────────┘  └──────────┘             │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                               │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                        BUSINESS SYSTEMS                               │    │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐             │    │
│  │  │ Payment  │  │   CRM    │  │ Calendar │  │ Analytics│             │    │
│  │  │ (Stripe) │  │(Hubspot) │  │ (Google) │  │(Mixpanel)│             │    │
│  │  └──────────┘  └──────────┘  └──────────┘  └──────────┘             │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                               │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                        CONTENT SYSTEMS                                │    │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐                           │    │
│  │  │   CMS    │  │  Media   │  │   CDN    │                           │    │
│  │  │(Contentful│ │(Cloudinary│ │(CloudFront│                          │    │
│  │  │  /Sanity)│  │  /Mux)   │  │          │                           │    │
│  │  └──────────┘  └──────────┘  └──────────┘                           │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                               │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Medical Integrations

### Electronic Medical Records (EMR)

**Purpose**: Centralized medical record keeping and clinical workflows

**Recommended Options**:
- **Jane App**: Designed for wellness clinics, good UX
- **Practice Better**: Integrative health focus
- **Cerbo**: Functional medicine oriented
- **Custom Build**: Full control, higher investment

**Integration Pattern**:
```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  Our Platform   │────▶│   Integration   │────▶│   EMR System    │
│                 │     │   Gateway       │     │                 │
│  - Guest App    │     │   (HL7 FHIR)    │     │  - Records      │
│  - Admin        │     │                 │     │  - Notes        │
└─────────────────┘     └─────────────────┘     └─────────────────┘
```

**Data Exchange**:
| Direction | Data | Frequency |
|-----------|------|-----------|
| Outbound | Intake forms, consent | Real-time |
| Outbound | Appointment updates | Real-time |
| Inbound | Medical history | Initial sync |
| Inbound | Treatment notes | As created |

### Cardiac Monitoring

**Purpose**: Continuous heart monitoring during plant medicine sessions

**Requirements**:
- Real-time ECG/EKG monitoring
- Heart rate variability (HRV)
- Arrhythmia detection
- Alarm integration
- Data export for records

**Integration Pattern**:
```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  Monitoring     │────▶│   Gateway       │────▶│  Alert System   │
│  Device         │     │   (BLE/WiFi)    │     │                 │
│                 │     │                 │     │  - Staff notify │
│  - ECG data     │     │  - Processing   │     │  - Escalation   │
│  - Vitals       │     │  - Thresholds   │     │  - Logging      │
└─────────────────┘     └─────────────────┘     └─────────────────┘
```

**Alert Thresholds**:
| Metric | Warning | Critical |
|--------|---------|----------|
| Heart Rate | <50 or >100 | <40 or >120 |
| QTc Interval | >450ms | >500ms |
| BP Systolic | <90 or >150 | <80 or >180 |

### Wearable Devices (Optional)

**Purpose**: Continuous wellness tracking before, during, and after retreat

**Supported Devices**:
- Apple Watch (HealthKit)
- Oura Ring (API)
- Whoop (API)
- Garmin (API)

**Data Collected**:
- Sleep quality and stages
- HRV trends
- Activity levels
- Recovery scores

---

## Communication Integrations

### Email (SendGrid / AWS SES)

**Purpose**: Transactional and marketing email

**Use Cases**:
| Type | Example | Priority |
|------|---------|----------|
| Transactional | Booking confirmation | High |
| Journey | Pre-care reminders | Medium |
| Community | Event notifications | Medium |
| Marketing | Newsletter | Low |

**Implementation**:
```typescript
interface EmailConfig {
  provider: 'sendgrid' | 'ses';
  templates: {
    booking_confirmation: string;
    medical_reminder: string;
    journey_update: string;
    community_digest: string;
  };
  tracking: {
    opens: boolean;
    clicks: boolean;
  };
}
```

### SMS (Twilio)

**Purpose**: Critical notifications and 2FA

**Use Cases**:
- Two-factor authentication
- Appointment reminders (24hr, 1hr)
- Emergency notifications
- Time-sensitive updates

**Considerations**:
- International support (Mexico, US, EU)
- HIPAA-compliant messaging
- Opt-in/opt-out management

### Video Conferencing (Zoom / Daily.co)

**Purpose**: Telemedicine, coaching, community events

**Integration Features**:
- Scheduled meeting creation
- In-app joining (SDK)
- Recording and transcription
- HIPAA-compliant option

**Use Cases**:
| Context | Platform | Recording |
|---------|----------|-----------|
| Medical consult | Zoom (HIPAA) | Yes, stored securely |
| Coaching session | Zoom | Optional |
| Community circle | Daily.co | No |
| Staff meeting | Zoom | Optional |

### Push Notifications (FCM / APNs)

**Purpose**: Real-time mobile engagement

**Notification Categories**:
```typescript
enum NotificationType {
  // High Priority
  MEDICAL_ALERT = 'medical_alert',
  SCHEDULE_CHANGE = 'schedule_change',
  MESSAGE_RECEIVED = 'message_received',

  // Medium Priority
  PRACTICE_REMINDER = 'practice_reminder',
  COMMUNITY_UPDATE = 'community_update',
  CONTENT_NEW = 'content_new',

  // Low Priority
  ENGAGEMENT = 'engagement',
  MARKETING = 'marketing',
}
```

---

## Business Integrations

### Payment Processing (Stripe)

**Purpose**: Secure payment handling for deposits, programs, and add-ons

**Features Required**:
- International payments (multi-currency)
- Subscription/recurring billing
- Payment plans
- Secure card storage
- Refund handling

**Integration Pattern**:
```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  Guest          │────▶│   Our API       │────▶│   Stripe        │
│  Checkout       │     │                 │     │                 │
│                 │     │  - Payment      │     │  - Processing   │
│  - Card input   │◀────│    Intent       │◀────│  - Webhooks     │
│  - Confirmation │     │  - Webhook      │     │  - Disputes     │
└─────────────────┘     └─────────────────┘     └─────────────────┘
```

**Webhook Events**:
| Event | Action |
|-------|--------|
| payment_intent.succeeded | Confirm booking |
| payment_intent.failed | Notify guest |
| invoice.paid | Update subscription |
| charge.refunded | Update records |

### CRM (HubSpot / Custom)

**Purpose**: Lead management, guest relationship tracking

**Integration Points**:
- Website form submissions
- Application tracking
- Communication history
- Follow-up automation

### Calendar (Google Calendar / Cal.com)

**Purpose**: Staff scheduling, availability management

**Features**:
- Bi-directional sync
- Availability windows
- Booking integration
- Conflict detection

---

## Content Integrations

### Content Management System (Contentful / Sanity)

**Purpose**: Manage educational content, practices, and resources

**Content Types**:
```typescript
interface ContentModel {
  articles: {
    title: string;
    body: RichText;
    category: string;
    stage: JourneyStage;
    media: Asset[];
  };
  practices: {
    title: string;
    instructions: RichText;
    duration: number;
    audioUrl: string;
    videoUrl: string;
  };
  programs: {
    name: string;
    duration: number;
    description: RichText;
    inclusions: string[];
  };
}
```

### Media Management (Cloudinary / Mux)

**Purpose**: Optimized image and video delivery

**Cloudinary (Images)**:
- Automatic optimization
- Responsive images
- Transformation API
- CDN delivery

**Mux (Video)**:
- Adaptive streaming
- Player SDK
- Analytics
- Secure playback

---

## Integration Patterns

### API Gateway Pattern

```
┌─────────────────────────────────────────────────────────────────────┐
│                          API GATEWAY                                 │
│                     (Kong / AWS API Gateway)                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐                  │
│  │    Auth     │  │    Rate     │  │   Logging   │                  │
│  │  Validation │  │  Limiting   │  │             │                  │
│  └─────────────┘  └─────────────┘  └─────────────┘                  │
│                                                                       │
│  ┌─────────────────────────────────────────────────────────────────┐│
│  │                      ROUTE MAPPING                               ││
│  │  /medical/*   → Medical Service                                  ││
│  │  /booking/*   → Booking Service                                  ││
│  │  /content/*   → Content Service                                  ││
│  │  /community/* → Community Service                                ││
│  └─────────────────────────────────────────────────────────────────┘│
│                                                                       │
└─────────────────────────────────────────────────────────────────────┘
```

### Event-Driven Integration

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  Source         │────▶│   Event Bus     │────▶│  Consumers      │
│  Service        │     │  (Redis/SQS)    │     │                 │
│                 │     │                 │     │  - Notification │
│  - Booking      │     │  - Routing      │     │  - Analytics    │
│  - Medical      │     │  - Retry        │     │  - Sync         │
│  - Journey      │     │  - DLQ          │     │  - Audit        │
└─────────────────┘     └─────────────────┘     └─────────────────┘
```

**Event Examples**:
```typescript
interface DomainEvent {
  type: string;
  timestamp: Date;
  source: string;
  data: object;
}

// Examples
type Events =
  | { type: 'booking.created'; data: { bookingId: string; guestId: string } }
  | { type: 'medical.cleared'; data: { guestId: string; clearanceId: string } }
  | { type: 'journey.stage_changed'; data: { journeyId: string; stage: string } }
  | { type: 'payment.completed'; data: { paymentId: string; amount: number } };
```

### Webhook Pattern

```typescript
interface WebhookConfig {
  endpoint: string;
  events: string[];
  secret: string;
  retryPolicy: {
    maxRetries: number;
    backoff: 'linear' | 'exponential';
  };
}

// Webhook handler
async function handleWebhook(req: Request): Promise<Response> {
  // 1. Verify signature
  const isValid = verifySignature(req, secret);
  if (!isValid) {
    return new Response('Invalid signature', { status: 401 });
  }

  // 2. Parse event
  const event = parseEvent(req.body);

  // 3. Process asynchronously
  await queue.add('process_webhook', event);

  // 4. Acknowledge immediately
  return new Response('OK', { status: 200 });
}
```

---

## Integration Security

### Authentication Methods

| Integration | Auth Method | Key Storage |
|-------------|-------------|-------------|
| Stripe | API Key | Secrets Manager |
| Twilio | API Key + Secret | Secrets Manager |
| SendGrid | API Key | Secrets Manager |
| EMR | OAuth 2.0 | Token storage |
| Zoom | OAuth 2.0 + JWT | Secrets Manager |

### Secrets Management

```
┌─────────────────────────────────────────────────────────────────────┐
│                     SECRETS MANAGEMENT                               │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  ┌─────────────────┐         ┌─────────────────┐                    │
│  │   AWS Secrets   │◀───────▶│   Application   │                    │
│  │     Manager     │         │                 │                    │
│  └─────────────────┘         └─────────────────┘                    │
│           │                                                           │
│           │  Rotation                                                 │
│           ▼                                                           │
│  ┌─────────────────┐                                                 │
│  │  Lambda Function│                                                 │
│  │  (Auto Rotate)  │                                                 │
│  └─────────────────┘                                                 │
│                                                                       │
│  Secrets:                                                             │
│  - stripe_api_key                                                    │
│  - twilio_auth_token                                                 │
│  - sendgrid_api_key                                                  │
│  - database_password                                                 │
│  - jwt_signing_key                                                   │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Error Handling & Resilience

### Circuit Breaker Pattern

```typescript
class CircuitBreaker {
  private state: 'closed' | 'open' | 'half-open' = 'closed';
  private failureCount = 0;
  private lastFailure: Date | null = null;

  async call<T>(fn: () => Promise<T>): Promise<T> {
    if (this.state === 'open') {
      if (this.shouldRetry()) {
        this.state = 'half-open';
      } else {
        throw new CircuitOpenError();
      }
    }

    try {
      const result = await fn();
      this.onSuccess();
      return result;
    } catch (error) {
      this.onFailure();
      throw error;
    }
  }
}
```

### Retry Strategy

```typescript
const retryConfig = {
  maxRetries: 3,
  baseDelay: 1000, // 1 second
  maxDelay: 30000, // 30 seconds
  backoff: 'exponential',
  retryOn: [
    'ECONNRESET',
    'ETIMEDOUT',
    'ECONNREFUSED',
    '502',
    '503',
    '504',
  ],
};
```

---

## Monitoring & Alerting

### Integration Health Dashboard

| Integration | Health Check | Alert Threshold |
|-------------|--------------|-----------------|
| Stripe | Payment test | 3 failures |
| Twilio | SMS test | 3 failures |
| EMR | API ping | 5 failures |
| Video | Room test | 2 failures |

### Key Metrics

- Request success rate
- Response time (p50, p95, p99)
- Error rates by type
- Queue depths
- Webhook delivery rate

---

**Version**: 1.0.0
**Last Updated**: December 2024
