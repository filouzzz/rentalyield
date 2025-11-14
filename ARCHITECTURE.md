# Technical Architecture Document
## Rental Yield Calculator - Property Investment Manager

**Version:** 1.0
**Last Updated:** November 14, 2025
**Status:** Architecture Design Phase

---

## 1. Architecture Overview

### 1.1 Architecture Style
**Offline-First, Cloud-Sync, Progressive Web Application**

### 1.2 High-Level Architecture Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                        CLIENT LAYER                          │
├─────────────────────────────────────────────────────────────┤
│  PWA (Web)          Android App (Capacitor)    iOS (Future) │
│  ┌──────────────┐   ┌──────────────┐          ┌──────────┐ │
│  │   Browser    │   │  WebView +   │          │ WebView  │ │
│  │  (Chrome,    │   │   Native     │          │          │ │
│  │   Firefox,   │   │   Plugins    │          │          │ │
│  │   Safari)    │   │              │          │          │ │
│  └──────────────┘   └──────────────┘          └──────────┘ │
└─────────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                    APPLICATION LAYER                         │
├─────────────────────────────────────────────────────────────┤
│  ┌────────────┐  ┌────────────┐  ┌─────────────────────┐   │
│  │   UI       │  │   State    │  │   Service Worker    │   │
│  │ Components │◄─┤ Management │  │  (Offline/Caching)  │   │
│  │  (React/   │  │  (Context/ │  │                     │   │
│  │   Vue/JS)  │  │   Redux)   │  │                     │   │
│  └────────────┘  └────────────┘  └─────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                      DATA LAYER (Client)                     │
├─────────────────────────────────────────────────────────────┤
│  ┌──────────────┐  ┌──────────────┐  ┌─────────────────┐   │
│  │  IndexedDB   │  │  Cache API   │  │   LocalStorage  │   │
│  │  (Properties,│  │  (Photos,    │  │   (Settings,    │   │
│  │   Sync       │  │   Assets)    │  │    Tokens)      │   │
│  │   Queue)     │  │              │  │                 │   │
│  └──────────────┘  └──────────────┘  └─────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                           │
                           ▼ (Sync when online)
┌─────────────────────────────────────────────────────────────┐
│                     API GATEWAY LAYER                        │
├─────────────────────────────────────────────────────────────┤
│                   Supabase Client SDK                        │
│         (REST API + Realtime WebSocket + Storage)            │
└─────────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                  BACKEND SERVICES (Supabase)                 │
├─────────────────────────────────────────────────────────────┤
│  ┌──────────────┐  ┌──────────────┐  ┌─────────────────┐   │
│  │ Authentication│  │  PostgreSQL  │  │  Object Storage │   │
│  │    + 2FA     │  │   Database   │  │   (S3-compat)   │   │
│  │              │  │              │  │   (Photos/Docs) │   │
│  └──────────────┘  └──────────────┘  └─────────────────┘   │
│                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌─────────────────┐   │
│  │   Realtime   │  │  Row Level   │  │   Edge          │   │
│  │  Subscriptions│  │  Security    │  │  Functions      │   │
│  │  (Sync)      │  │   (RLS)      │  │  (Serverless)   │   │
│  └──────────────┘  └──────────────┘  └─────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                  INFRASTRUCTURE LAYER                        │
├─────────────────────────────────────────────────────────────┤
│    AWS eu-west-3 (Paris) / OVH France / Azure EU            │
│    - Managed PostgreSQL (Supabase Cloud)                    │
│    - CDN for static assets                                  │
│    - Backup & disaster recovery                             │
│    - SSL/TLS certificates                                   │
└─────────────────────────────────────────────────────────────┘
```

---

## 2. Technology Stack

### 2.1 Frontend

| Component | Technology | Rationale |
|-----------|-----------|-----------|
| **UI Framework** | React + Vite (or Vue 3) | Modern, fast dev server, component reusability. Alternative: Keep vanilla JS if staying simple |
| **State Management** | React Context API / Zustand | Lightweight, no Redux boilerplate for MVP |
| **Styling** | Tailwind CSS | Utility-first, responsive, small bundle size |
| **Offline Storage** | IndexedDB (via Dexie.js) | Structured data, better than localStorage for complex objects |
| **Service Worker** | Workbox | Google library for PWA, simplifies caching strategies |
| **Photo Compression** | Browser Canvas API | Client-side, no server costs |
| **Form Validation** | Zod / Yup | Type-safe validation |
| **Date/Numbers** | date-fns, Intl.NumberFormat | Lightweight, i18n-ready |

**Bundle Size Target:** < 300KB gzipped (initial load)

---

### 2.2 Backend (Backend-as-a-Service)

| Component | Technology | Rationale |
|-----------|-----------|-----------|
| **BaaS Platform** | **Supabase** (recommended) | Open-source, PostgreSQL, EU hosting, generous free tier, all-in-one (auth + DB + storage + realtime) |
| **Database** | PostgreSQL (via Supabase) | Relational, ACID compliance, JSON support, mature |
| **Authentication** | Supabase Auth | Built-in OAuth, 2FA, JWT tokens, email verification |
| **File Storage** | Supabase Storage | S3-compatible, built-in image transformations |
| **Realtime Sync** | Supabase Realtime | WebSocket-based, auto-sync across devices |
| **Serverless Functions** | Supabase Edge Functions (Deno) | For custom business logic (photo compression server-side if needed later) |

**Alternative Considered:**
- Firebase (rejected: US-based Firestore, Google lock-in, less GDPR-friendly)
- AWS Amplify (rejected: complex, expensive for MVP)

---

### 2.3 Mobile (Cross-Platform)

| Component | Technology | Rationale |
|-----------|-----------|-----------|
| **Cross-Platform** | Capacitor 6 | Web-to-native, access to device APIs, smaller than Ionic |
| **Android** | Capacitor Android | Minimum SDK 26 (Android 8.0) |
| **iOS** | Capacitor iOS (future) | Minimum iOS 13 |
| **Native Plugins** | @capacitor/camera, @capacitor/filesystem | Photo upload, local file access |

---

### 2.4 DevOps & Tooling

| Component | Technology | Rationale |
|-----------|-----------|-----------|
| **Version Control** | Git + GitHub | Industry standard |
| **CI/CD** | GitHub Actions | Free for public repos, integrated |
| **Hosting (PWA)** | Vercel / Netlify / GitHub Pages | Free tier, auto-deploy from git |
| **Package Manager** | npm / pnpm | Fast, deterministic |
| **Build Tool** | Vite | Fast HMR, optimized builds |
| **Linting** | ESLint + Prettier | Code quality, consistency |
| **Testing** | Vitest (unit), Playwright (e2e) | Fast, modern |
| **Monitoring** | Sentry (errors), Plausible (analytics) | Privacy-friendly, GDPR-compliant |

---

## 3. Data Architecture

### 3.1 Database Schema (PostgreSQL)

```sql
-- Users table (managed by Supabase Auth)
-- auth.users (built-in, includes email, encrypted password, metadata)

-- Profiles table (extended user info)
CREATE TABLE profiles (
  id UUID PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  display_name TEXT,
  language TEXT DEFAULT 'fr',
  timezone TEXT DEFAULT 'Europe/Paris',
  -- GDPR
  data_processing_consent BOOLEAN DEFAULT FALSE,
  analytics_consent BOOLEAN DEFAULT FALSE,
  marketing_consent BOOLEAN DEFAULT FALSE
);

-- Properties table
CREATE TABLE properties (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE NOT NULL,

  -- Basic info
  name TEXT NOT NULL,
  address TEXT,
  property_type TEXT CHECK (property_type IN ('apartment', 'house', 'commercial', 'other')),
  purchase_date DATE,
  size_sqm NUMERIC(10,2), -- square meters
  num_rooms INTEGER,

  -- Financial data
  total_cost NUMERIC(12,2) NOT NULL, -- euros
  monthly_rent NUMERIC(10,2) NOT NULL,
  loan_amount NUMERIC(12,2) DEFAULT 0,
  interest_rate NUMERIC(5,2) DEFAULT 0, -- TAEG percentage
  loan_duration_years INTEGER DEFAULT 20,

  -- Extra expenses (optional)
  property_tax NUMERIC(10,2) DEFAULT 0,
  management_fee_percent NUMERIC(5,2) DEFAULT 0,
  insurance_annual NUMERIC(10,2) DEFAULT 0,
  maintenance_reserve_annual NUMERIC(10,2) DEFAULT 0,
  vacancy_rate_percent NUMERIC(5,2) DEFAULT 0,

  -- Metadata
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  deleted_at TIMESTAMP WITH TIME ZONE, -- soft delete

  -- Sync tracking
  version INTEGER DEFAULT 1, -- for conflict resolution
  last_synced_at TIMESTAMP WITH TIME ZONE
);

-- Create index for faster queries
CREATE INDEX idx_properties_user_id ON properties(user_id);
CREATE INDEX idx_properties_created_at ON properties(created_at DESC);

-- Photos/Videos table
CREATE TABLE property_media (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  property_id UUID REFERENCES properties(id) ON DELETE CASCADE NOT NULL,
  user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE NOT NULL,

  media_type TEXT CHECK (media_type IN ('photo', 'video')),
  storage_path TEXT NOT NULL, -- Supabase Storage path
  storage_bucket TEXT DEFAULT 'property-media',

  -- Metadata
  file_name TEXT,
  file_size_bytes INTEGER,
  mime_type TEXT,
  width INTEGER,
  height INTEGER,

  -- Compressed versions (if applicable)
  thumbnail_path TEXT, -- 200x200
  compressed_path TEXT, -- 1024x1024

  uploaded_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  deleted_at TIMESTAMP WITH TIME ZONE
);

CREATE INDEX idx_media_property_id ON property_media(property_id);

-- Sync queue (for offline changes)
CREATE TABLE sync_queue (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE NOT NULL,

  entity_type TEXT NOT NULL, -- 'property', 'media'
  entity_id UUID NOT NULL,
  operation TEXT CHECK (operation IN ('create', 'update', 'delete')),
  payload JSONB, -- the actual data to sync

  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  synced_at TIMESTAMP WITH TIME ZONE,
  failed_at TIMESTAMP WITH TIME ZONE,
  error_message TEXT
);

CREATE INDEX idx_sync_queue_user_pending ON sync_queue(user_id, synced_at) WHERE synced_at IS NULL;
```

---

### 3.2 Row-Level Security (RLS) Policies

**Supabase RLS ensures users can only access their own data:**

```sql
-- Enable RLS on all tables
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;
ALTER TABLE properties ENABLE ROW LEVEL SECURITY;
ALTER TABLE property_media ENABLE ROW LEVEL SECURITY;
ALTER TABLE sync_queue ENABLE ROW LEVEL SECURITY;

-- Profiles: Users can only read/update their own profile
CREATE POLICY "Users can view own profile" ON profiles
  FOR SELECT USING (auth.uid() = id);

CREATE POLICY "Users can update own profile" ON profiles
  FOR UPDATE USING (auth.uid() = id);

-- Properties: Users can CRUD only their own properties
CREATE POLICY "Users can view own properties" ON properties
  FOR SELECT USING (auth.uid() = user_id);

CREATE POLICY "Users can create own properties" ON properties
  FOR INSERT WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can update own properties" ON properties
  FOR UPDATE USING (auth.uid() = user_id);

CREATE POLICY "Users can delete own properties" ON properties
  FOR DELETE USING (auth.uid() = user_id);

-- Media: Similar policies
CREATE POLICY "Users can view own media" ON property_media
  FOR SELECT USING (auth.uid() = user_id);

CREATE POLICY "Users can upload media" ON property_media
  FOR INSERT WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can delete own media" ON property_media
  FOR DELETE USING (auth.uid() = user_id);

-- Sync queue: Only for own user
CREATE POLICY "Users can access own sync queue" ON sync_queue
  FOR ALL USING (auth.uid() = user_id);
```

---

### 3.3 Client-Side Data Model (IndexedDB)

```typescript
// Using Dexie.js for IndexedDB
import Dexie, { Table } from 'dexie';

export interface Property {
  id: string; // UUID
  userId: string;
  name: string;
  address?: string;
  propertyType?: 'apartment' | 'house' | 'commercial' | 'other';
  purchaseDate?: Date;
  sizeSqm?: number;
  numRooms?: number;

  totalCost: number;
  monthlyRent: number;
  loanAmount: number;
  interestRate: number;
  loanDurationYears: number;

  propertyTax?: number;
  managementFeePercent?: number;
  insuranceAnnual?: number;
  maintenanceReserveAnnual?: number;
  vacancyRatePercent?: number;

  createdAt: Date;
  updatedAt: Date;
  deletedAt?: Date;

  // Sync metadata
  version: number;
  lastSyncedAt?: Date;
  isDirty: boolean; // true if changed locally but not synced
}

export interface Media {
  id: string;
  propertyId: string;
  userId: string;
  mediaType: 'photo' | 'video';
  storagePath: string;
  fileName?: string;
  fileSizeBytes?: number;
  mimeType?: string;
  uploadedAt: Date;
  deletedAt?: Date;

  // Local-only
  localBlobUrl?: string; // for offline viewing
  uploadStatus: 'pending' | 'uploading' | 'uploaded' | 'failed';
}

export interface SyncQueueItem {
  id: string;
  userId: string;
  entityType: 'property' | 'media';
  entityId: string;
  operation: 'create' | 'update' | 'delete';
  payload: any;
  createdAt: Date;
  syncedAt?: Date;
  failedAt?: Date;
  errorMessage?: string;
}

class AppDatabase extends Dexie {
  properties!: Table<Property>;
  media!: Table<Media>;
  syncQueue!: Table<SyncQueueItem>;

  constructor() {
    super('RentalYieldDB');
    this.version(1).stores({
      properties: 'id, userId, createdAt, isDirty',
      media: 'id, propertyId, userId, uploadedAt',
      syncQueue: 'id, userId, syncedAt, entityType'
    });
  }
}

export const db = new AppDatabase();
```

---

## 4. Offline-First Architecture

### 4.1 Sync Strategy

**Principle:** "Local-first, cloud-sync"
- All data writes go to IndexedDB first (instant UI response)
- Background sync pushes changes to Supabase when online
- Conflicts resolved using "last-write-wins" for MVP (can upgrade to CRDTs later)

### 4.2 Sync Flow

```
┌──────────────────────────────────────────────────────┐
│  User Action (Create/Update/Delete Property)         │
└────────────┬─────────────────────────────────────────┘
             │
             ▼
┌──────────────────────────────────────────────────────┐
│  1. Write to IndexedDB immediately                   │
│     - Update local data                              │
│     - Mark as "dirty" (isDirty = true)               │
│     - Add to sync_queue                              │
└────────────┬─────────────────────────────────────────┘
             │
             ▼
┌──────────────────────────────────────────────────────┐
│  2. UI updates instantly (optimistic)                │
└────────────┬─────────────────────────────────────────┘
             │
             ▼
       ┌─────────────┐
       │  Online?    │
       └─────┬───────┘
             │
        ┌────┴────┐
        │         │
       NO        YES
        │         │
        ▼         ▼
   ┌────────┐  ┌──────────────────────────────────┐
   │  Wait  │  │ 3. Background Sync Worker runs   │
   │  for   │  │    - Process sync_queue items    │
   │ online │  │    - Send to Supabase API        │
   └────────┘  └────────┬─────────────────────────┘
                        │
                        ▼
                ┌───────────────┐
                │  Success?     │
                └───────┬───────┘
                        │
                   ┌────┴────┐
                   │         │
                  YES       NO
                   │         │
                   ▼         ▼
        ┌──────────────┐  ┌──────────────────┐
        │ 4. Mark      │  │ Retry (3x)       │
        │   synced     │  │ Exponential      │
        │   Clear      │  │ backoff          │
        │   isDirty    │  └──────────────────┘
        └──────────────┘           │
                                   ▼
                            ┌──────────────────┐
                            │ Still fails?     │
                            │ Mark as failed,  │
                            │ notify user      │
                            └──────────────────┘
```

### 4.3 Conflict Resolution (MVP)

**Strategy:** Last-Write-Wins (LWW)
- Each entity has `version` and `updatedAt` timestamp
- When syncing, compare local vs server `updatedAt`
- If server is newer: overwrite local copy
- If local is newer: push to server
- If equal: no-op

**Future Enhancement:** Operational Transformation (OT) or CRDTs for true multi-device concurrent editing

---

## 5. Security Architecture

### 5.1 Authentication Flow

```
┌─────────────────────────────────────────────────┐
│  User enters email + password                   │
└────────────┬────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────┐
│  Supabase Auth: Verify credentials              │
│  - Check hashed password (bcrypt)               │
│  - Rate limit: 5 attempts per 15 min            │
└────────────┬────────────────────────────────────┘
             │
             ▼
       ┌─────────────┐
       │  2FA        │
       │  Enabled?   │
       └─────┬───────┘
             │
        ┌────┴────┐
        │         │
       NO        YES
        │         │
        ▼         ▼
   ┌────────┐  ┌──────────────────────────┐
   │ Issue  │  │ Send 2FA code (SMS/email)│
   │ JWT    │  │ User enters code         │
   │ tokens │  │ Verify code              │
   └────────┘  └────────┬─────────────────┘
                        │
                        ▼
                   ┌────────┐
                   │ Issue  │
                   │ JWT    │
                   └────────┘
                        │
                        ▼
        ┌────────────────────────────────────┐
        │  Tokens stored in:                 │
        │  - Access token: Memory (15 min)   │
        │  - Refresh token: httpOnly cookie  │
        └────────────────────────────────────┘
```

### 5.2 API Security

| Layer | Mechanism | Implementation |
|-------|-----------|----------------|
| **Transport** | HTTPS/TLS 1.3 | Enforced, no HTTP allowed |
| **Authentication** | JWT tokens | Signed with HS256, short-lived (15 min) |
| **Authorization** | Row-Level Security | Supabase RLS policies (user can only access own data) |
| **Rate Limiting** | Per-user limits | Supabase built-in: 100 req/min per user |
| **Input Validation** | Schema validation | Zod schemas on client + PostgreSQL constraints |
| **XSS Prevention** | Content Security Policy | CSP headers, no inline scripts |
| **CSRF Prevention** | SameSite cookies | Refresh tokens use SameSite=Strict |
| **SQL Injection** | Parameterized queries | Supabase client uses prepared statements |

### 5.3 Data Encryption

| Data Type | At Rest | In Transit |
|-----------|---------|------------|
| Database (PostgreSQL) | AES-256 (Supabase managed) | TLS 1.3 |
| File Storage (photos) | AES-256 (S3-compatible) | TLS 1.3 |
| JWT tokens | N/A (signed, not encrypted) | TLS 1.3 |
| IndexedDB (local) | No encryption (browser sandbox) | N/A (local) |

**Future Enhancement:** Add client-side encryption for sensitive data (E2EE)

---

## 6. Performance Optimization

### 6.1 Frontend Performance

| Optimization | Technique | Target |
|--------------|-----------|--------|
| **Bundle Size** | Code splitting, tree shaking | < 300KB gzipped |
| **Initial Load** | Lazy loading routes | < 3s on 3G |
| **Images** | WebP format, lazy loading | 80%+ smaller than JPEG |
| **Fonts** | System fonts, subset if custom | No font flash |
| **Caching** | Service Worker, Cache-First strategy | Instant repeat visits |
| **Rendering** | Virtual scrolling for long lists | 60fps scroll |

### 6.2 Backend Performance

| Optimization | Technique | Target |
|--------------|-----------|--------|
| **Database Queries** | Indexes on foreign keys, WHERE clauses | < 50ms avg |
| **API Latency** | Edge Functions (Deno Deploy) near users | < 100ms p95 |
| **Realtime** | WebSocket connection pooling | 1000+ concurrent users |
| **File Upload** | Direct upload to storage (presigned URLs) | No server bottleneck |
| **CDN** | CloudFlare / Fastly for static assets | < 10ms global |

### 6.3 Offline Performance

- IndexedDB queries: < 10ms for typical dataset (100 properties)
- Service Worker cache hits: < 5ms
- Photo compression: < 2s per 5MB photo (client-side)

---

## 7. Scalability Considerations

### 7.1 MVP Scale (Free Tier Limits)

**Supabase Free Tier:**
- Database: 500MB (sufficient for ~5,000 properties with metadata)
- Storage: 1GB (sufficient for ~200 photos at 5MB each compressed)
- Bandwidth: 2GB/month
- Concurrent connections: 60

**Estimated Capacity:**
- Users: 100-200 active users
- Properties: 5,000 total
- Photos: 10,000 total (compressed)

### 7.2 Growth Path (Paid Tiers)

**When to upgrade:**
- \> 100 active users → Supabase Pro ($25/month)
  - 8GB database
  - 100GB storage
  - 50GB bandwidth
  - Daily backups

- \> 1,000 active users → Supabase Team ($599/month)
  - Dedicated resources
  - 24/7 support
  - Custom limits

**Alternative (Self-hosting):**
- Supabase is open-source, can migrate to:
  - AWS (RDS PostgreSQL + S3 + Lambda)
  - OVH Cloud (Managed PostgreSQL + Object Storage)
  - Cost: ~$50-100/month for 1,000 users

### 7.3 Database Scaling Strategy

1. **Vertical:** Increase database size (500MB → 8GB → unlimited)
2. **Archival:** Move old data to cheaper cold storage after 2 years
3. **Partitioning:** Partition `properties` table by `user_id` (Postgres native)
4. **Read Replicas:** Add read replicas for analytics queries (Supabase Pro+)

---

## 8. Monitoring & Observability

### 8.1 Metrics to Track

| Metric | Tool | Threshold |
|--------|------|-----------|
| **Error Rate** | Sentry | < 0.1% of requests |
| **API Latency** | Supabase Dashboard | p95 < 200ms |
| **Page Load Time** | Vercel Analytics | p75 < 3s |
| **Offline Sync Success** | Custom events → Plausible | > 95% success rate |
| **User Engagement** | Plausible Analytics | DAU/MAU ratio |
| **Storage Usage** | Supabase Dashboard | Track toward limits |

### 8.2 Logging Strategy

**Client-Side:**
- Errors: Sentry (exceptions, unhandled promises)
- Analytics: Plausible (privacy-friendly, no cookies)
- Sync events: Custom logging to Supabase (for debugging)

**Server-Side:**
- Supabase logs: Auth events, API errors
- Edge Functions: Console logs (Deno Deploy dashboard)

### 8.3 Alerting

**Critical Alerts:**
- Database > 80% capacity → Email notification
- Error rate > 1% for 5 minutes → Slack alert
- Supabase service downtime → Status page check

---

## 9. Deployment Architecture

### 9.1 Environments

| Environment | Purpose | URL | Branch |
|-------------|---------|-----|--------|
| **Local Dev** | Development | localhost:5173 | feature/* |
| **Staging** | Testing, QA | staging.rentalyield.app | develop |
| **Production** | Live users | rentalyield.app / www.rentalyield.app | main |

### 9.2 CI/CD Pipeline (GitHub Actions)

```yaml
# .github/workflows/deploy.yml
name: Deploy

on:
  push:
    branches: [main, develop]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - Checkout code
      - Install dependencies (npm ci)
      - Run linter (npm run lint)
      - Run tests (npm run test)
      - Build (npm run build)
      - Deploy to Vercel (production if main, staging if develop)
      - Build Android APK (if main branch)
      - Create GitHub Release (if tagged)
```

### 9.3 Hosting

**PWA (Web App):**
- **Vercel** (recommended): Auto-deploy from Git, edge network, free tier
- Alternative: Netlify, GitHub Pages

**Android APK:**
- **GitHub Releases** (current method)
- **Google Play Store** (future): Requires signing keys, $25 fee

**Backend:**
- **Supabase Cloud** (EU region): Managed, auto-scaling

---

## 10. GDPR Compliance Architecture

### 10.1 Data Minimization

**Principle:** Collect only necessary data

| Data | Purpose | Retention |
|------|---------|-----------|
| Email | Authentication, communication | Until account deletion |
| Password (hashed) | Authentication | Until account deletion |
| Property data | Core feature | Until user deletes |
| Photos | User-uploaded content | Until user deletes |
| IP address (logs) | Security, abuse prevention | 7 days |
| Analytics (anonymized) | Product improvement | 90 days |

### 10.2 User Rights Implementation

| Right | Implementation |
|-------|----------------|
| **Access** | "Download My Data" button → JSON export |
| **Rectification** | Edit profile, edit properties (UI already supports) |
| **Erasure** | "Delete Account" → Hard delete from DB + storage |
| **Portability** | JSON export includes all data |
| **Restrict Processing** | Disable analytics consent (toggle in settings) |
| **Object** | Opt-out of marketing emails (unsubscribe link) |

### 10.3 Consent Management

```typescript
// Consent tracking in profiles table
interface Consent {
  data_processing_consent: boolean; // Required for account
  analytics_consent: boolean; // Optional (Plausible)
  marketing_consent: boolean; // Optional (email newsletters)
}

// Consent banner UI flow
1. First visit → Show banner
2. User clicks "Accept All" → All true
3. User clicks "Reject Non-Essential" → Only data_processing = true
4. User clicks "Customize" → Granular toggles
5. Store in profiles table + localStorage
```

---

## 11. Migration Path (v1.0 → v2.0)

### 11.1 Current State (v1.0 - Single HTML file)
- Vanilla JS calculator
- No backend
- No user accounts
- Local-only data

### 11.2 Migration Strategy

**Option A: Clean Break**
- Build v2.0 as separate codebase
- Provide import tool (user can manually enter properties)
- Retire v1.0

**Option B: Progressive Migration**
1. Add backend to existing app (keep UI)
2. Add auth (optional at first)
3. Gradual feature additions
4. Eventually rebrand/redesign

**Recommendation:** Option A (cleaner, faster for MVP timeline)

### 11.3 Data Migration Tool

```typescript
// v1.0 used localStorage, can import to v2.0
function importFromV1() {
  const v1Data = localStorage.getItem('rentalyield_v1_properties');
  if (v1Data) {
    const properties = JSON.parse(v1Data);
    // Map v1 schema to v2 schema
    // Bulk insert to Supabase
  }
}
```

---

## 12. Disaster Recovery

### 12.1 Backup Strategy

**Supabase Managed Backups:**
- Free tier: No automated backups (manual exports only)
- Pro tier ($25/month): Daily backups, 7-day retention
- Team tier: Point-in-time recovery

**Recommended for MVP:**
- Weekly manual backup: `pg_dump` → S3/OVH storage
- Critical data: Photos stored redundantly (Supabase Storage has replication)

### 12.2 Recovery Plan

| Scenario | Recovery Time Objective (RTO) | Recovery Procedure |
|----------|-------------------------------|-------------------|
| Database corruption | 1 hour | Restore from latest backup |
| Supabase outage | N/A (wait for service) | Show offline mode, queue syncs |
| Accidental deletion | 24 hours | Restore from backup |
| Complete data loss | 1 week | Rebuild from backups + user support |

---

## Appendix A: Technology Alternatives Considered

| Decision | Chosen | Alternatives Considered | Reason for Choice |
|----------|--------|------------------------|-------------------|
| **Backend** | Supabase | Firebase, AWS Amplify, Parse | Open-source, EU hosting, PostgreSQL, all-in-one |
| **Frontend Framework** | React/Vue (or vanilla) | Angular, Svelte, Solid | Ecosystem size, hiring pool, learning curve |
| **State Management** | Context API / Zustand | Redux, MobX, Recoil | Simpler for MVP, less boilerplate |
| **Styling** | Tailwind CSS | Bootstrap, Material UI, Styled Components | Utility-first, customizable, small size |
| **Database** | PostgreSQL | MongoDB, MySQL, Firestore | Relational model fits use case, mature, ACID |
| **Mobile** | Capacitor | React Native, Flutter, Ionic | Reuses web code, simpler than RN |
| **Auth** | Supabase Auth | Auth0, Clerk, custom JWT | Included with Supabase, no extra cost |

---

## Appendix B: API Endpoints (Supabase Auto-Generated)

```
# Authentication
POST   /auth/v1/signup
POST   /auth/v1/token (login)
POST   /auth/v1/logout
POST   /auth/v1/recover (password reset)
POST   /auth/v1/user (update profile)

# Properties (REST API)
GET    /rest/v1/properties
POST   /rest/v1/properties
PATCH  /rest/v1/properties?id=eq.{id}
DELETE /rest/v1/properties?id=eq.{id}

# Media
GET    /rest/v1/property_media?property_id=eq.{id}
POST   /rest/v1/property_media
DELETE /rest/v1/property_media?id=eq.{id}

# Storage (Photos)
POST   /storage/v1/object/property-media/{path}
GET    /storage/v1/object/public/property-media/{path}
DELETE /storage/v1/object/property-media/{path}

# Realtime (WebSocket)
WS     /realtime/v1/websocket (subscribe to table changes)
```

---

**Document Status:** Draft - Ready for Review
**Next Steps:**
1. Review and approve architecture
2. Set up Supabase project
3. Initialize frontend codebase with chosen stack
4. Begin Sprint 1 development
