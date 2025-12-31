# Infrastructure Architecture

> *Reliable, secure, and scalable foundations*

---

## Infrastructure Philosophy

Our infrastructure must embody the same principles as our healing work:
1. **Reliability** - Always available when guests need us
2. **Security** - Protecting the sacred trust of health data
3. **Scalability** - Ready to grow to 30+ locations
4. **Simplicity** - Complex enough to serve, simple enough to maintain

---

## Cloud Architecture (AWS)

### Overview

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              AWS CLOUD                                        │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │                            VPC (10.0.0.0/16)                            │  │
│  │  ┌─────────────────────────────────────────────────────────────────┐  │  │
│  │  │                      PUBLIC SUBNETS                               │  │  │
│  │  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐        │  │  │
│  │  │  │   ALB    │  │ NAT GW   │  │ NAT GW   │  │ Bastion  │        │  │  │
│  │  │  │          │  │  (AZ-A)  │  │  (AZ-B)  │  │  Host    │        │  │  │
│  │  │  └──────────┘  └──────────┘  └──────────┘  └──────────┘        │  │  │
│  │  │          10.0.1.0/24          10.0.2.0/24                        │  │  │
│  │  └─────────────────────────────────────────────────────────────────┘  │  │
│  │                                                                         │  │
│  │  ┌─────────────────────────────────────────────────────────────────┐  │  │
│  │  │                     PRIVATE SUBNETS (APP)                         │  │  │
│  │  │  ┌──────────────────────────────────────────────────────────┐   │  │  │
│  │  │  │                    ECS CLUSTER                             │   │  │  │
│  │  │  │  ┌────────┐  ┌────────┐  ┌────────┐  ┌────────┐          │   │  │  │
│  │  │  │  │  API   │  │ Worker │  │ Admin  │  │ Cron   │          │   │  │  │
│  │  │  │  │Service │  │Service │  │Service │  │Service │          │   │  │  │
│  │  │  │  └────────┘  └────────┘  └────────┘  └────────┘          │   │  │  │
│  │  │  └──────────────────────────────────────────────────────────┘   │  │  │
│  │  │          10.0.10.0/24         10.0.11.0/24                       │  │  │
│  │  └─────────────────────────────────────────────────────────────────┘  │  │
│  │                                                                         │  │
│  │  ┌─────────────────────────────────────────────────────────────────┐  │  │
│  │  │                    PRIVATE SUBNETS (DATA)                         │  │  │
│  │  │  ┌──────────┐  ┌──────────┐  ┌──────────┐                       │  │  │
│  │  │  │   RDS    │  │ Elasti-  │  │   S3     │                       │  │  │
│  │  │  │ Postgres │  │  Cache   │  │ Endpoint │                       │  │  │
│  │  │  │(Primary +│  │ (Redis)  │  │          │                       │  │  │
│  │  │  │ Replica) │  │          │  │          │                       │  │  │
│  │  │  └──────────┘  └──────────┘  └──────────┘                       │  │  │
│  │  │          10.0.20.0/24         10.0.21.0/24                       │  │  │
│  │  └─────────────────────────────────────────────────────────────────┘  │  │
│  └───────────────────────────────────────────────────────────────────────┘  │
│                                                                               │
│  External Services:                                                          │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐      │
│  │CloudFront│  │   S3     │  │ Secrets  │  │   KMS    │  │   SES    │      │
│  │  (CDN)   │  │(Storage) │  │ Manager  │  │  (Keys)  │  │ (Email)  │      │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘  └──────────┘      │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Compute Infrastructure

### ECS (Elastic Container Service)

**Why ECS over EKS:**
- Simpler operational overhead
- Better AWS integration
- Sufficient for our scale
- Lower cost
- Easier HIPAA compliance

**Cluster Configuration:**
```yaml
cluster:
  name: epicenter-production
  capacity_providers:
    - FARGATE
    - FARGATE_SPOT  # For non-critical workloads

services:
  api:
    task_definition: epicenter-api
    desired_count: 2
    min_count: 2
    max_count: 10
    cpu: 512
    memory: 1024
    capacity_provider: FARGATE

  worker:
    task_definition: epicenter-worker
    desired_count: 1
    min_count: 1
    max_count: 5
    cpu: 256
    memory: 512
    capacity_provider: FARGATE_SPOT

  admin:
    task_definition: epicenter-admin
    desired_count: 1
    cpu: 256
    memory: 512
    capacity_provider: FARGATE
```

### Auto Scaling

```yaml
scaling:
  api:
    target_tracking:
      - metric: CPUUtilization
        target: 70
      - metric: RequestCountPerTarget
        target: 1000
    step_scaling:
      - adjustment: +2
        lower_bound: 80  # CPU %
      - adjustment: -1
        upper_bound: 30  # CPU %

  worker:
    target_tracking:
      - metric: ApproximateNumberOfMessagesVisible
        target: 100  # SQS queue depth
```

---

## Database Infrastructure

### RDS PostgreSQL

**Configuration:**
```yaml
rds:
  engine: postgres
  version: "15"
  instance_class: db.r6g.large  # Production
  multi_az: true
  storage:
    type: gp3
    size: 100  # GB
    iops: 3000
    throughput: 125  # MB/s

  backup:
    retention: 30  # days
    window: "03:00-04:00"
    copy_to_region: us-west-2

  maintenance:
    window: "Sun:04:00-Sun:05:00"

  encryption:
    enabled: true
    kms_key: alias/epicenter-rds

  parameters:
    log_statement: "all"  # HIPAA requirement
    log_min_duration_statement: 1000  # Log slow queries
    ssl: 1
```

**Read Replicas:**
```yaml
replicas:
  - region: same
    purpose: read_scaling
    instance_class: db.r6g.medium

  - region: us-west-2
    purpose: disaster_recovery
    instance_class: db.r6g.medium
```

### ElastiCache (Redis)

**Configuration:**
```yaml
elasticache:
  engine: redis
  version: "7.0"
  node_type: cache.r6g.medium
  num_cache_clusters: 2  # Primary + replica
  automatic_failover: true

  encryption:
    at_rest: true
    in_transit: true

  parameters:
    maxmemory-policy: volatile-lru
    notify-keyspace-events: Ex  # For pub/sub
```

---

## Storage Infrastructure

### S3 Buckets

```yaml
buckets:
  # Medical documents (HIPAA)
  epicenter-medical-documents:
    encryption: aws:kms
    versioning: enabled
    lifecycle:
      - transition_to_ia: 90 days
      - transition_to_glacier: 365 days
    access: private
    cors: disabled

  # User uploads
  epicenter-user-uploads:
    encryption: aws:kms
    versioning: enabled
    lifecycle:
      - expiration: 90 days  # Temporary uploads
    access: private
    cors: enabled

  # Static assets
  epicenter-static-assets:
    encryption: AES256
    versioning: disabled
    access: public (via CloudFront)
    cors: enabled

  # Backups
  epicenter-backups:
    encryption: aws:kms
    versioning: enabled
    lifecycle:
      - transition_to_glacier: 30 days
      - expiration: 2555 days  # 7 years
    access: private
    replication: us-west-2
```

---

## Networking

### VPC Design

```yaml
vpc:
  cidr: 10.0.0.0/16
  enable_dns_hostnames: true
  enable_dns_support: true

  subnets:
    public:
      - cidr: 10.0.1.0/24
        az: us-east-1a
      - cidr: 10.0.2.0/24
        az: us-east-1b

    private_app:
      - cidr: 10.0.10.0/24
        az: us-east-1a
      - cidr: 10.0.11.0/24
        az: us-east-1b

    private_data:
      - cidr: 10.0.20.0/24
        az: us-east-1a
      - cidr: 10.0.21.0/24
        az: us-east-1b
```

### Security Groups

```yaml
security_groups:
  alb:
    ingress:
      - port: 443
        source: 0.0.0.0/0
      - port: 80
        source: 0.0.0.0/0  # Redirect to HTTPS

  api:
    ingress:
      - port: 8080
        source: sg-alb
    egress:
      - port: 443
        destination: 0.0.0.0/0  # External APIs
      - port: 5432
        destination: sg-rds
      - port: 6379
        destination: sg-redis

  rds:
    ingress:
      - port: 5432
        source: sg-api

  redis:
    ingress:
      - port: 6379
        source: sg-api
```

---

## Load Balancing & CDN

### Application Load Balancer

```yaml
alb:
  name: epicenter-api
  scheme: internet-facing
  type: application

  listeners:
    - port: 443
      protocol: HTTPS
      certificate: arn:aws:acm:...
      default_action: forward to api-target-group

    - port: 80
      protocol: HTTP
      default_action: redirect to HTTPS

  target_groups:
    api:
      port: 8080
      protocol: HTTP
      health_check:
        path: /health
        interval: 30
        timeout: 5
        healthy_threshold: 2
        unhealthy_threshold: 3

  access_logs:
    enabled: true
    bucket: epicenter-logs
```

### CloudFront CDN

```yaml
cloudfront:
  distributions:
    # Static assets and web app
    web:
      origins:
        - s3://epicenter-static-assets
        - alb://epicenter-api
      behaviors:
        - path: /static/*
          origin: s3
          cache: 86400  # 1 day
        - path: /api/*
          origin: alb
          cache: 0
        - path: /*
          origin: s3
          cache: 3600  # 1 hour
      ssl:
        certificate: arn:aws:acm:us-east-1:...
        minimum_protocol: TLSv1.2

    # Media delivery
    media:
      origins:
        - s3://epicenter-user-uploads
      behaviors:
        - path: /*
          signed_urls: true
          cache: 86400
```

---

## CI/CD Pipeline

### GitHub Actions Workflow

```yaml
# .github/workflows/deploy.yml
name: Deploy

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run tests
        run: npm test
      - name: Run security scan
        run: npm audit

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Build Docker image
        run: docker build -t epicenter-api .
      - name: Push to ECR
        run: |
          aws ecr get-login-password | docker login --username AWS --password-stdin $ECR_URL
          docker push $ECR_URL/epicenter-api:$GITHUB_SHA

  deploy-staging:
    needs: build
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    environment: staging
    steps:
      - name: Deploy to ECS
        run: |
          aws ecs update-service --cluster epicenter-staging --service api --force-new-deployment

  deploy-production:
    needs: deploy-staging
    runs-on: ubuntu-latest
    environment: production
    steps:
      - name: Deploy to ECS
        run: |
          aws ecs update-service --cluster epicenter-production --service api --force-new-deployment
```

### Infrastructure as Code (Terraform)

```
/infrastructure/terraform
├── environments/
│   ├── production/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── terraform.tfvars
│   └── staging/
│       ├── main.tf
│       ├── variables.tf
│       └── terraform.tfvars
├── modules/
│   ├── vpc/
│   ├── ecs/
│   ├── rds/
│   ├── redis/
│   ├── s3/
│   └── monitoring/
└── shared/
    └── backend.tf
```

---

## Monitoring & Observability

### Monitoring Stack

```
┌─────────────────────────────────────────────────────────────────────┐
│                       OBSERVABILITY STACK                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  ┌─────────────────────────────────────────────────────────────────┐│
│  │                        DATADOG                                   ││
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐        ││
│  │  │ Metrics  │  │   Logs   │  │  Traces  │  │   APM    │        ││
│  │  └──────────┘  └──────────┘  └──────────┘  └──────────┘        ││
│  └─────────────────────────────────────────────────────────────────┘│
│                                                                       │
│  ┌─────────────────────────────────────────────────────────────────┐│
│  │                        SENTRY                                    ││
│  │  ┌──────────────────────────────────────────────────────────┐   ││
│  │  │             Error Tracking & Performance                  │   ││
│  │  └──────────────────────────────────────────────────────────┘   ││
│  └─────────────────────────────────────────────────────────────────┘│
│                                                                       │
│  ┌─────────────────────────────────────────────────────────────────┐│
│  │                      PAGERDUTY                                   ││
│  │  ┌──────────────────────────────────────────────────────────┐   ││
│  │  │                    Alerting & On-Call                     │   ││
│  │  └──────────────────────────────────────────────────────────┘   ││
│  └─────────────────────────────────────────────────────────────────┘│
│                                                                       │
└─────────────────────────────────────────────────────────────────────┘
```

### Key Metrics

```yaml
dashboards:
  infrastructure:
    - CPU utilization (ECS)
    - Memory utilization (ECS)
    - Database connections (RDS)
    - Cache hit rate (Redis)
    - Request latency (ALB)

  application:
    - Request rate
    - Error rate (4xx, 5xx)
    - Response time (p50, p95, p99)
    - Active users
    - API endpoint performance

  business:
    - Booking conversion
    - User engagement
    - Content consumption
    - Community activity

alerts:
  critical:
    - API error rate > 5%
    - Database CPU > 90%
    - Response time p95 > 2s
    - Health check failures

  warning:
    - API error rate > 1%
    - Database CPU > 70%
    - Response time p95 > 1s
    - Cache hit rate < 80%
```

---

## Disaster Recovery

### Backup Strategy

```yaml
backups:
  database:
    automated:
      frequency: daily
      retention: 30 days
    snapshots:
      frequency: hourly
      retention: 7 days
    cross_region:
      destination: us-west-2
      frequency: daily

  files:
    s3_versioning: enabled
    cross_region_replication:
      destination: us-west-2
      filter: all objects

  secrets:
    aws_secrets_manager:
      backup: included in AWS backup
```

### Recovery Procedures

| Scenario | RTO | RPO | Procedure |
|----------|-----|-----|-----------|
| Service failure | 5 min | 0 | Auto-healing, ECS redeploy |
| Database failure | 15 min | 5 min | Promote replica |
| AZ failure | 15 min | 5 min | ALB failover |
| Region failure | 4 hours | 1 hour | DR site activation |

### DR Runbook

```markdown
## Region Failure Recovery

1. **Assess situation** (5 min)
   - Confirm primary region is down
   - Notify team via PagerDuty

2. **Activate DR site** (30 min)
   - Update Route 53 to DR region
   - Promote RDS read replica
   - Scale up ECS services

3. **Verify functionality** (30 min)
   - Run health checks
   - Verify data integrity
   - Test critical flows

4. **Communicate** (ongoing)
   - Update status page
   - Notify affected users
   - Document timeline

5. **Plan recovery** (when ready)
   - Assess primary region status
   - Plan data sync strategy
   - Schedule failback
```

---

## Cost Optimization

### Estimated Monthly Costs

| Service | Configuration | Est. Cost |
|---------|--------------|-----------|
| ECS Fargate | 2 API + 1 Worker + 1 Admin | $150 |
| RDS PostgreSQL | db.r6g.large Multi-AZ | $400 |
| ElastiCache Redis | cache.r6g.medium x2 | $200 |
| S3 | 100GB + requests | $50 |
| CloudFront | 1TB transfer | $100 |
| ALB | Standard usage | $50 |
| NAT Gateway | 2 AZs | $100 |
| Secrets Manager | 20 secrets | $20 |
| DataDog | Standard tier | $200 |
| **Total** | | **~$1,270/mo** |

### Cost Optimization Strategies

1. **Reserved Instances** - 1-year commit for RDS (40% savings)
2. **Spot Instances** - Worker services on Fargate Spot
3. **Right-sizing** - Regular review of instance utilization
4. **S3 Lifecycle** - Automatic tiering to cheaper storage
5. **CDN Caching** - Reduce origin requests

---

## Environments

### Environment Matrix

| Environment | Purpose | Infrastructure | Data |
|-------------|---------|----------------|------|
| Development | Local dev | Docker Compose | Fixtures |
| Staging | Testing | Reduced AWS | Anonymized |
| Production | Live | Full AWS | Real |

### Development Environment

```yaml
# docker-compose.yml
services:
  api:
    build: ./src/api
    ports:
      - "8080:8080"
    environment:
      - DATABASE_URL=postgres://postgres:password@db:5432/epicenter
      - REDIS_URL=redis://redis:6379

  db:
    image: postgres:15
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7

  localstack:
    image: localstack/localstack
    # S3, SES, SQS emulation
```

---

**Version**: 1.0.0
**Last Updated**: December 2024
