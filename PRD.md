# Product Requirements Document (PRD)
## Rental Yield Calculator - Multi-Property Investment Manager

**Version:** 1.0
**Last Updated:** November 14, 2025
**Product Owner:** filouzzz
**Status:** Planning Phase

---

## 1. Executive Summary

### 1.1 Vision
Transform the existing rental yield calculator into a comprehensive, cloud-based property investment management platform that enables French/EU investors to manage multiple properties, analyze profitability, and make data-driven investment decisions - accessible from any device, online or offline.

### 1.2 Strategic Objectives
- **Primary Goal:** Deliver a working MVP within 1-2 weeks focused on core functionality
- **Learning Objective:** Understand modern full-stack architecture and AI-powered development workflows
- **Business Objective:** Build a monetizable SaaS product with potential for public distribution
- **Technical Objective:** Implement cloud-native, offline-first architecture using managed services

### 1.3 Success Metrics (MVP)
- [ ] User can create account and authenticate
- [ ] User can create/edit properties offline and sync when online
- [ ] User can upload photos for properties
- [ ] Data persists across devices
- [ ] App works as PWA (web) and Android APK
- [ ] GDPR-compliant data handling implemented

---

## 2. Target Users

### 2.1 Primary Persona
**"Marie - The Multi-Property Investor"**
- Age: 35-55
- Location: France/EU
- Profile: Real estate investor managing 2-5 rental properties
- Tech-savvy: Medium (comfortable with smartphone apps)
- Pain Points:
  - Spreadsheets are cumbersome for tracking multiple properties
  - Needs quick yield calculations on-the-go
  - Wants property photos/documents organized in one place
  - Needs offline access when visiting properties

### 2.2 Secondary Persona
**"Thomas - The First-Time Buyer"**
- Age: 28-40
- Location: France/EU
- Profile: Researching first rental property investment
- Tech-savvy: High
- Pain Points:
  - Overwhelmed by investment calculations
  - Wants to compare multiple property options
  - Needs educational guidance on rental yield interpretation

---

## 3. Core Features (MVP - Priority 1)

### 3.1 User Authentication & Account Management
**User Story:** As a user, I want to create an account so I can access my properties from any device.

**Acceptance Criteria:**
- [ ] Email/password registration
- [ ] Email verification
- [ ] Secure login
- [ ] Password reset flow
- [ ] Account deletion (GDPR right to be forgotten)

**Technical Notes:**
- Use managed authentication service (Firebase Auth / Supabase Auth)
- Store minimal user data (email, hashed password, creation date)

---

### 3.2 Two-Factor Authentication (2FA)
**User Story:** As a security-conscious user, I want 2FA to protect my investment data.

**Acceptance Criteria:**
- [ ] SMS-based 2FA (primary method)
- [ ] Authenticator app support (TOTP - Google Authenticator, Authy)
- [ ] Backup codes generation
- [ ] Optional: Email-based 2FA

**Technical Notes:**
- Most backend-as-a-service platforms include built-in 2FA
- SMS via Twilio (paid) or email-based (free alternative for MVP)

**Open Question:**
- Which 2FA methods for MVP? (Email = free, SMS = better UX but costs)

---

### 3.3 Property Management (CRUD)
**User Story:** As an investor, I want to create and manage multiple properties with all relevant financial data.

**Acceptance Criteria:**
- [ ] Create new property with fields:
  - Property name/address
  - Total project cost (€)
  - Monthly gross rent (€)
  - Loan amount (€)
  - Interest rate (TAEG %)
  - Loan duration (years)
  - **NEW:** Property type (apartment, house, commercial)
  - **NEW:** Purchase date
  - **NEW:** Property size (m²)
  - **NEW:** Number of rooms
- [ ] Edit existing property
- [ ] Delete property (with confirmation)
- [ ] List all properties in dashboard view

**Extra Fields (to be added):**
- [ ] Property taxes (taxe foncière)
- [ ] Management fees
- [ ] Insurance costs
- [ ] Maintenance reserves
- [ ] Vacancy rate (%)
- [ ] Net rental yield calculation (after all expenses)

---

### 3.4 Offline-First Architecture
**User Story:** As a user visiting properties in areas with poor signal, I want to create/edit properties offline and have them sync automatically when I'm back online.

**Acceptance Criteria:**
- [ ] All property data stored locally (IndexedDB)
- [ ] Create/edit operations work without internet
- [ ] Queue offline changes
- [ ] Auto-sync when connection restored
- [ ] Conflict resolution (last-write-wins for MVP)
- [ ] Visual indicator of sync status

**Technical Notes:**
- Service Worker for offline capability
- IndexedDB for local storage
- Sync queue with retry logic
- Background Sync API (where supported)

---

### 3.5 Photo & Video Management
**User Story:** As a property manager, I want to attach photos and short videos to each property for documentation and reference.

**Acceptance Criteria:**
- [ ] Upload multiple photos per property
- [ ] Upload short video clips (30 seconds max for MVP)
- [ ] View photos in gallery view
- [ ] Delete photos/videos
- [ ] Photos accessible offline (cached)

**Photo Compression (Open Question - see Section 8):**
- Auto-compress on upload?
- Client-side vs server-side compression?
- Keep original + compressed versions?

**Technical Notes:**
- Cloud storage: Supabase Storage / Firebase Storage / AWS S3
- Max file size limits (5MB per photo for MVP)
- Supported formats: JPG, PNG, WebP for photos; MP4 for video

---

### 3.6 Cross-Device Data Sync
**User Story:** As a user with multiple devices, I want my property data to sync automatically across my phone, tablet, and computer.

**Acceptance Criteria:**
- [ ] Data syncs in real-time (or near real-time)
- [ ] Login on new device shows all existing properties
- [ ] Changes on one device appear on others within 5 seconds
- [ ] Handles concurrent edits gracefully

**Technical Notes:**
- Real-time sync via WebSockets or long polling
- Supabase Realtime / Firebase Firestore listeners

---

### 3.7 GDPR Compliance
**User Story:** As an EU user, I want transparency and control over my personal data.

**Acceptance Criteria:**
- [ ] Privacy Policy page (clear, simple French)
- [ ] Terms of Service
- [ ] Cookie consent banner (if using analytics/ads)
- [ ] Data export functionality (download all user data as JSON)
- [ ] Account deletion with full data removal
- [ ] Clear consent for data processing
- [ ] Data retention policy documented

**Technical Notes:**
- Use EU-based data centers (AWS eu-west-3 Paris, OVH France)
- Encrypt data in transit (HTTPS) and at rest
- Minimal data collection by default

---

## 4. Nice-to-Have Features (Priority 2 - Post-MVP)

### 4.1 Advanced Calculations
- [ ] Net rental yield (after all expenses)
- [ ] Cash flow projection (monthly/annual)
- [ ] ROI calculation
- [ ] Break-even analysis
- [ ] Tax implications calculator

### 4.2 Data Visualization
- [ ] Charts showing payment breakdown over loan duration
- [ ] Portfolio overview (all properties aggregated)
- [ ] Yield comparison across properties
- [ ] Historical performance tracking

### 4.3 Sharing & Collaboration
- [ ] Share property details via link
- [ ] Export property report as PDF
- [ ] Print-friendly view
- [ ] Invite co-owners (multi-user access)

### 4.4 Notifications & Alerts
- [ ] Rent payment reminders
- [ ] Loan payment due alerts
- [ ] Property tax deadlines
- [ ] Yield drop warnings

### 4.5 Multi-language Support
- [ ] English translation
- [ ] Spanish translation
- [ ] Auto-detect user language

### 4.6 iOS Version
- [ ] Build iOS app using Capacitor
- [ ] Publish to Apple App Store
- **Note:** Requires macOS for building, Apple Developer account ($99/year)

---

## 5. Monetization Strategy

### 5.1 Revenue Models (Open Questions - see Section 8)

**Option A: Freemium SaaS**
- Free tier: 2 properties max
- Pro tier (€4.99/month): Unlimited properties, advanced analytics, priority support
- Premium tier (€9.99/month): All features + API access

**Option B: Ads-Based (Free for all users)**
- Display ads in free version
- One-time payment (€9.99) to remove ads permanently

**Option C: Hybrid**
- Free tier with ads + limited properties
- Paid tier removes ads + unlocks features

### 5.2 Ad Implementation (If chosen)
**Acceptance Criteria:**
- [ ] Ads from trusted networks only (Google AdMob, AdSense)
- [ ] Non-intrusive placement (bottom banner, between property list items)
- [ ] No redirect to unsafe URLs
- [ ] Compliant with GDPR (consent for personalized ads)

**Technical Notes:**
- Web (PWA): Google AdSense
- Android: Google AdMob SDK
- Frequency capping (max 1 ad per screen)

---

## 6. Technical Requirements

### 6.1 Platform Support (Priority Order)
1. **PWA (Web App)** - Priority 1
   - Responsive design (mobile, tablet, desktop)
   - Installable on home screen
   - Offline-capable
   - Works on all modern browsers

2. **Android App** - Priority 2
   - Native APK via Capacitor
   - Google Play Store distribution (eventually)
   - Supports Android 8.0+ (API level 26+)

3. **iOS App** - Nice-to-have (Priority 3)
   - Native app via Capacitor
   - App Store distribution
   - Supports iOS 13+

### 6.2 Performance Requirements
- [ ] Initial page load: < 3 seconds (3G connection)
- [ ] Time to interactive: < 5 seconds
- [ ] Offline mode activation: < 1 second
- [ ] Sync completion: < 10 seconds for typical dataset

### 6.3 Security Requirements
- [ ] HTTPS everywhere
- [ ] Encrypted data storage (cloud)
- [ ] Secure authentication (OAuth 2.0 / OpenID Connect)
- [ ] Rate limiting on API endpoints
- [ ] Input validation and sanitization (prevent XSS, SQL injection)
- [ ] Regular security audits (automated tools)

### 6.4 Accessibility Requirements
- [ ] WCAG 2.1 Level AA compliance
- [ ] Keyboard navigation support
- [ ] Screen reader compatible
- [ ] Sufficient color contrast
- [ ] Responsive text sizing

---

## 7. User Experience Requirements

### 7.1 Onboarding Flow
1. Landing page with value proposition
2. Sign up (email + password)
3. Email verification
4. Welcome screen with quick tutorial
5. "Add your first property" prompt
6. Dashboard with sample data (optional)

### 7.2 Key User Flows

**Flow 1: Add First Property**
```
Dashboard → "Add Property" button →
Form with fields →
Upload photos (optional) →
Save →
View property detail with calculated yield
```

**Flow 2: Edit Property Offline**
```
Open app offline →
Select property →
Edit fields →
Save (queued locally) →
"Syncing..." indicator appears when online →
"Synced ✓" confirmation
```

**Flow 3: View Across Devices**
```
Login on Device A →
Add property →
Login on Device B →
See same property automatically
```

### 7.3 UI/UX Principles
- **Simplicity:** Clean, uncluttered interface
- **French-first:** All text in French (English optional)
- **Mobile-first:** Design for phone screens primarily
- **Forgiving:** Undo actions, confirmation dialogs for destructive operations
- **Helpful:** Tooltips explaining financial terms (e.g., "TAEG = Taux Annuel Effectif Global")

---

## 8. Open Questions & Decisions Needed

### 8.1 Monetization
**Question:** Freemium SaaS vs Ads vs Hybrid model?
**Impact:** Architecture (payment gateway integration), user acquisition strategy
**Decision needed by:** Before MVP development starts
**Recommendation:** Start with free + ads for MVP, add premium tier later (faster to market)

---

### 8.2 Photo Compression Strategy
**Question:** Client-side (device) vs Server-side compression?
**Options:**
- A) Client-side: Browser compresses before upload (Canvas API)
  - Pros: Free, reduces upload time, saves bandwidth
  - Cons: Drains phone battery, slower on old devices
- B) Server-side: Upload original, server compresses
  - Pros: Consistent quality, no device impact
  - Cons: Requires compute resources (costs money), slower initial upload
- C) Progressive: Keep both original + compressed
  - Pros: Flexibility, lossless backup
  - Cons: 2x storage costs

**Impact:** Storage costs, user experience, technical complexity
**Decision needed by:** Before photo upload feature development
**Recommendation:** Client-side compression for MVP (free, good enough quality)

---

### 8.3 Video Storage Approach
**Question:** Allow video uploads in MVP or defer to v2?
**Considerations:**
- Videos consume significant storage (30-sec clip = 10-50MB)
- Increases cloud storage costs
- Compression more complex than photos
- Usage may be low compared to photos

**Impact:** Storage costs, development time
**Decision needed by:** During MVP scope finalization
**Recommendation:** Defer videos to v2, focus on photos for MVP

---

### 8.4 Data Privacy Level
**Question:** Balanced vs Standard data collection?
**Options:**
- **Balanced:** Essential analytics (page views, feature usage), require consent, anonymized data
  - Tools: Privacy-focused analytics (Plausible, Fathom)
  - Ads: Contextual only (no tracking)
- **Standard:** Google Analytics, personalized ads, user behavior tracking
  - Tools: Google Analytics, AdMob with personalization
  - Ads: Targeted, higher revenue potential

**Impact:** Revenue potential (targeted ads pay 2-3x more), user trust, GDPR compliance complexity
**Decision needed by:** Before analytics/ads implementation
**Recommendation:** Balanced for MVP (easier GDPR compliance, builds user trust)

---

### 8.5 Backend Service Selection
**Question:** Which managed backend service?
**Options:**
| Service | Pros | Cons | GDPR | Free Tier |
|---------|------|------|------|-----------|
| **Supabase** | Open-source, postgres, generous free tier, EU hosting | Smaller ecosystem | ✅ EU servers | 500MB DB, 1GB storage |
| **Firebase** | Mature, excellent docs, large community | Google lock-in | ⚠️ US-based (Firestore) | 1GB storage, 10GB transfer |
| **AWS Amplify** | Powerful, scalable, AWS ecosystem | Complex, expensive | ✅ EU regions available | Limited free tier |
| **Azure** | Good for enterprises, EU regions | Microsoft ecosystem | ✅ EU regions | Limited free tier |
| **OVH Cloud** | EU-based (France), GDPR-native | Smaller ecosystem, less documentation | ✅ EU-based | Varies |

**Impact:** Development velocity, costs, vendor lock-in, GDPR compliance ease
**Decision needed by:** Before MVP development starts
**Recommendation:** **Supabase** for MVP (open-source, GDPR-friendly, generous free tier, EU hosting, all features needed: Auth + 2FA + Storage + Realtime + PostgreSQL)

---

### 8.6 Ad Network Selection (If ads chosen)
**Question:** Which ad network for trusted, safe ads?
**Options:**
- **Google AdSense/AdMob:** Industry standard, reliable, good fill rates
  - Safe: Yes (Google vets advertisers)
  - GDPR: Requires consent management
  - Revenue: Medium to high
- **Ezoic:** AI-optimized ads, higher revenue potential
  - Safe: Yes
  - GDPR: Built-in consent tools
  - Revenue: Higher than AdSense
  - Requirement: Minimum traffic (10k visits/month)
- **Ethical/Privacy-focused:**
  - EthicalAds (developer-focused, no tracking)
  - Carbon Ads (design/dev audience, curated)
  - Revenue: Lower but privacy-friendly

**Impact:** Revenue, user experience, GDPR compliance effort
**Decision needed by:** Before monetization implementation
**Recommendation:** Google AdMob for MVP (proven, safe, easy to implement)

---

## 9. Success Criteria (Definition of Done)

### 9.1 MVP Launch Criteria
- [ ] All Priority 1 features implemented and tested
- [ ] PWA installable on mobile devices
- [ ] Android APK builds successfully
- [ ] User can complete full flow: signup → add property → view yield → access from another device
- [ ] Offline mode works (create property offline, syncs when online)
- [ ] Privacy policy and terms published
- [ ] GDPR consent mechanisms in place
- [ ] No critical bugs (P0/P1 severity)
- [ ] Performance meets requirements (load time, sync time)

### 9.2 Public Launch Criteria (Post-MVP)
- [ ] Google Play Store listing approved
- [ ] Monetization active (ads or payment gateway)
- [ ] Analytics tracking implemented
- [ ] Customer support channel defined (email, chat)
- [ ] At least 10 beta testers completed full user journey
- [ ] SEO optimized (meta tags, sitemap, structured data)
- [ ] Social media presence (optional)

---

## 10. Out of Scope (Explicitly NOT in MVP)

- ❌ Property marketplace (buying/selling properties)
- ❌ Mortgage broker integration
- ❌ Automated property valuation (AVM)
- ❌ Rental income tracking (actual vs projected)
- ❌ Tenant management
- ❌ Maintenance request system
- ❌ Integration with accounting software
- ❌ AI-powered investment recommendations
- ❌ White-label solution for real estate agencies

---

## 11. Risks & Mitigation

### 11.1 Technical Risks
| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| Offline sync conflicts cause data loss | Medium | High | Implement conflict resolution, auto-backup before sync |
| Photo storage costs exceed budget | Medium | Medium | Client-side compression, storage limits per user |
| Authentication service downtime | Low | High | Choose reliable provider (99.9% SLA), implement graceful degradation |
| GDPR non-compliance penalties | Low | Critical | Legal review, use GDPR-compliant tools, minimize data collection |

### 11.2 Business Risks
| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| Low user adoption | Medium | High | Beta test with target users, iterate on feedback |
| Ad revenue insufficient | Medium | Medium | Plan freemium alternative, keep costs low |
| Competitor launches similar product | Low | Medium | Speed to market (MVP in 1-2 weeks), unique UX |

---

## 12. Timeline & Milestones

### Week 1: Foundation
- [ ] Day 1-2: Technical architecture setup (Supabase, repo structure, CI/CD)
- [ ] Day 3-4: Authentication + 2FA implementation
- [ ] Day 5-7: Property CRUD + offline storage

### Week 2: MVP Completion
- [ ] Day 8-10: Photo upload + cloud sync
- [ ] Day 11-12: PWA configuration + Android build
- [ ] Day 13-14: Testing, bug fixes, GDPR compliance, deploy

### Post-MVP (Week 3+)
- [ ] Beta testing with 5-10 users
- [ ] Iterate based on feedback
- [ ] Implement monetization
- [ ] Google Play Store submission
- [ ] Public launch

---

## 13. Stakeholders & Roles

| Role | Name | Responsibilities |
|------|------|------------------|
| Product Owner | filouzzz | Vision, prioritization, decisions |
| Developer | Claude Code (AI) + filouzzz | Implementation, architecture |
| Designer | filouzzz | UI/UX decisions, design approval |
| QA Tester | filouzzz | Manual testing, bug reporting |
| Legal/GDPR | TBD | Privacy policy review (optional consultation) |

---

## Appendix A: References
- Original calculator: https://imavenir.com/investissement-locatif-cle-en-main/calculette-rentabilite-locative/
- GitHub repository: https://github.com/filouzzz/rentalyield
- Current release: https://github.com/filouzzz/rentalyield/releases/tag/v1.0.0

## Appendix B: Glossary
- **PWA:** Progressive Web App (web app that works like native app)
- **CRUD:** Create, Read, Update, Delete (basic data operations)
- **2FA:** Two-Factor Authentication
- **GDPR:** General Data Protection Regulation (EU privacy law)
- **MVP:** Minimum Viable Product (simplest version with core value)
- **TAEG:** Taux Annuel Effectif Global (APR in French)
- **SaaS:** Software as a Service (subscription-based software)
- **IndexedDB:** Browser database for offline storage
- **Service Worker:** Browser background script enabling offline functionality

---

**Document Status:** Draft - Ready for Review
**Next Steps:**
1. Review and approve PRD
2. Create Technical Architecture document
3. Break down into Product Backlog with story points
4. Begin Sprint 1 (Week 1 foundation)
