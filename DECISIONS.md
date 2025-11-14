# Decision Log & Open Questions
## Rental Yield Calculator - Property Investment Manager

**Version:** 1.0
**Last Updated:** November 14, 2025
**Purpose:** Track key architectural decisions and unresolved questions requiring stakeholder input

---

## 1. Decision Records (ADR - Architecture Decision Records)

### ADR-001: Backend-as-a-Service Platform Selection
**Date:** 2025-11-14
**Status:** ✅ DECIDED
**Decision Maker:** filouzzz + Claude Code

**Context:**
Need a backend platform for authentication, database, file storage, and realtime sync. Options: Supabase, Firebase, AWS Amplify, custom backend.

**Decision:**
**Use Supabase** for MVP and beyond

**Rationale:**
- ✅ Open-source (can self-host later if needed)
- ✅ EU-based hosting (GDPR-friendly)
- ✅ PostgreSQL (relational DB, better for structured property data than NoSQL)
- ✅ All features in one: Auth + DB + Storage + Realtime
- ✅ Generous free tier (500MB DB, 1GB storage)
- ✅ Row-Level Security (RLS) for data isolation
- ✅ No vendor lock-in (standard PostgreSQL)

**Alternatives Considered:**
- Firebase: Rejected due to US-based Firestore, Google ecosystem lock-in
- AWS Amplify: Rejected due to complexity and cost
- Custom backend: Rejected due to time constraints for MVP (would take 2-3 weeks alone)

**Consequences:**
- +  Fast development (don't build auth/storage from scratch)
- +  Lower costs (free tier covers MVP)
- -  Learning curve for Supabase-specific features
- -  Dependency on Supabase service availability (mitigated by open-source nature)

**Related Documents:** ARCHITECTURE.md Section 2.2

---

### ADR-002: Frontend Framework Selection
**Date:** 2025-11-14
**Status:** ⏳ PENDING DECISION
**Decision Maker:** filouzzz

**Context:**
Current v1.0 uses vanilla HTML/CSS/JS (single file). For v2.0 with complex state (offline sync, multi-page), need to decide: keep vanilla JS or adopt a framework.

**Options:**

| Option | Pros | Cons | Learning Curve |
|--------|------|------|----------------|
| **Vanilla JS** | No build step, simple, lightweight | Harder to manage complex state, more boilerplate | None (current) |
| **React + Vite** | Popular, huge ecosystem, good for SPAs | JSX syntax, useState/useEffect patterns | Medium |
| **Vue 3 + Vite** | Simpler than React, good DX, SFC (Single File Components) | Smaller ecosystem than React | Low-Medium |
| **Svelte + Vite** | Minimal boilerplate, compiles away, fast | Smaller ecosystem, less job market | Low |

**Recommendation:**
**React + Vite** for balance of ecosystem size and modern DX. Alternative: **Vue 3** if prefer simpler syntax.

**Open Questions:**
1. Does filouzzz have prior experience with React or Vue?
2. Preference for learning React (industry standard) vs Vue (easier for beginners)?

**Next Steps:**
- **DECISION NEEDED** before Sprint 1 starts (SETUP-1 depends on this)

---

### ADR-003: State Management Library
**Date:** 2025-11-14
**Status:** ✅ DECIDED
**Decision Maker:** Claude Code (can be overridden)

**Context:**
Need to manage application state (user auth, properties, sync status) across components.

**Decision:**
Start with **React Context API** (if React) or **Vue Composition API** (if Vue). Upgrade to Zustand/Pinia only if state becomes too complex.

**Rationale:**
- Built-in, no extra dependencies
- Sufficient for MVP scope
- Can refactor to Zustand/Redux later if needed
- Learning focus: understand state management patterns before jumping to libraries

**Consequences:**
- + Simpler codebase for MVP
- + One less library to learn
- - May need refactor if state grows complex (acceptable for MVP)

---

### ADR-004: Offline Storage Solution
**Date:** 2025-11-14
**Status:** ✅ DECIDED
**Decision Maker:** Claude Code

**Context:**
Need client-side storage for offline-first architecture. Options: localStorage, IndexedDB, SQLite (via WASM).

**Decision:**
**IndexedDB** via **Dexie.js** wrapper library

**Rationale:**
- localStorage: Too limited (5-10MB, string-only, synchronous)
- IndexedDB: Async, structured data, 50MB+ per domain, supports indexes
- Dexie.js: Simplifies IndexedDB's verbose API, TypeScript support
- SQLite WASM: Overkill for MVP, larger bundle size

**Consequences:**
- + Can store complex objects (properties with nested data)
- + Fast queries with indexes
- + 50MB+ storage (sufficient for 1000s of properties)
- - Need to learn Dexie.js API (minimal learning curve)

**Related Documents:** ARCHITECTURE.md Section 3.3

---

### ADR-005: Photo Compression Strategy
**Date:** 2025-11-14
**Status:** ⚠️ OPEN QUESTION (See Section 2)
**Decision Maker:** filouzzz

**Context:**
Users upload photos (potentially 5-10MB each). Need to compress to save storage costs and bandwidth.

**Options:**
1. **Client-side compression** (Browser Canvas API)
2. **Server-side compression** (Supabase Edge Function or external service)
3. **Hybrid** (client compresses, server generates thumbnails)

**Recommendation:** Client-side for MVP (see DECISIONS.md Section 2.2)

**Status:** Awaiting decision before implementing MEDIA-4

---

### ADR-006: Monetization Model
**Date:** 2025-11-14
**Status:** ⚠️ OPEN QUESTION (See Section 2)
**Decision Maker:** filouzzz

**Context:**
Need sustainable revenue model for post-MVP. Options: Freemium SaaS, Ads, One-time purchase, Hybrid.

**Status:** Awaiting decision (doesn't block MVP development, but influences UI design)

**Next Steps:** Review options in DECISIONS.md Section 2.1 and decide before implementing monetization features

---

### ADR-007: 2FA Implementation Method
**Date:** 2025-11-14
**Status:** ⚠️ OPEN QUESTION (See Section 2)
**Decision Maker:** filouzzz

**Context:**
2FA adds security but has UX and cost implications.

**Options:**
- **Email-based 2FA** (free, built into Supabase)
- **SMS 2FA** (costs ~$0.01 per SMS via Twilio)
- **TOTP (Google Authenticator)** (free, better security, requires app)

**Recommendation:** Email for MVP, add TOTP option later

**Status:** Awaiting confirmation before implementing AUTH-5

---

### ADR-008: Conflict Resolution Strategy
**Date:** 2025-11-14
**Status:** ✅ DECIDED (for MVP)
**Decision Maker:** Claude Code

**Context:**
When user edits property on Device A while offline, then edits same property on Device B, then both sync - which version wins?

**Decision:**
**Last-Write-Wins (LWW)** using `updatedAt` timestamp

**Rationale:**
- Simple to implement
- Acceptable for MVP (single-user, infrequent concurrent edits)
- Can upgrade to CRDTs or Operational Transformation (OT) post-MVP if needed

**Consequences:**
- + Simple implementation
- - User could lose changes if editing on multiple devices simultaneously (unlikely in MVP)
- Mitigation: Show warning "This property was updated on another device" with option to keep local or use server version

**Future Enhancement:** Implement CRDTs (Conflict-free Replicated Data Types) for true multi-device concurrent editing

---

### ADR-009: Analytics & Monitoring Tool
**Date:** 2025-11-14
**Status:** ✅ DECIDED
**Decision Maker:** filouzzz + Claude Code

**Context:**
Need analytics to understand user behavior, but must be GDPR-compliant and respect user privacy.

**Decision:**
**Plausible Analytics** (privacy-friendly, no cookies) for analytics
**Sentry** (error tracking) for monitoring

**Rationale:**
- Plausible: EU-based, no personal data, GDPR-compliant by design, simple privacy policy
- Sentry: Industry standard for error tracking, free tier sufficient
- Alternative rejected: Google Analytics (requires cookie consent, privacy concerns)

**Consequences:**
- + Easier GDPR compliance (Plausible doesn't require consent banner)
- + User trust (privacy-respecting)
- - Less granular data than Google Analytics (acceptable trade-off)

---

### ADR-010: Testing Strategy
**Date:** 2025-11-14
**Status:** ✅ DECIDED
**Decision Maker:** Claude Code

**Context:**
Need testing for quality assurance, but MVP timeline is tight.

**Decision:**
- **Manual E2E testing** for MVP (filouzzz as QA)
- **Unit tests** for critical functions only (calculations, sync logic)
- **Automated E2E tests** (Playwright) post-MVP

**Rationale:**
- MVP focus: speed over perfection
- Manual testing catches most issues for small MVP
- Automated tests are investment for long-term maintenance

**Consequences:**
- + Faster MVP delivery
- - Higher bug risk (mitigated by thorough manual testing)
- Post-MVP: Add Playwright tests for regression prevention

---

## 2. Open Questions Requiring Decisions

### 2.1 Monetization Strategy
**Priority:** Medium (doesn't block MVP, but influences design)
**Decision Needed By:** Before implementing monetization features (post-MVP)
**Stakeholder:** filouzzz

**Question:**
Which monetization model should we pursue?

**Options:**

#### Option A: Freemium SaaS
**Model:**
- Free tier: 2 properties max, basic features
- Pro tier (€4.99/month or €49/year): Unlimited properties, advanced analytics
- Premium tier (€9.99/month or €99/year): All features + priority support

**Pros:**
- Predictable recurring revenue
- Users can try before buying
- Scalable business model

**Cons:**
- Requires payment gateway (Stripe integration)
- Need compelling premium features to justify price
- Higher development cost (subscription management)

**Technical Requirements:**
- Stripe integration (MONET-2)
- Subscription management UI (MONET-4)
- Property count enforcement (MONET-3)

---

#### Option B: Ads-Based (Free for All)
**Model:**
- Free for all users
- Display ads (Google AdMob for Android, AdSense for web)
- One-time payment (€9.99) to remove ads permanently

**Pros:**
- Low barrier to entry (free)
- Simple implementation (just integrate ad SDK)
- Works for high-volume user base

**Cons:**
- Ads can degrade UX
- Revenue depends on traffic (low initially)
- GDPR consent required for personalized ads

**Technical Requirements:**
- Google AdMob SDK (MONET-1)
- Ad placement strategy (non-intrusive)
- Consent management (GDPR-2 already covers this)

**Estimated Revenue:**
- 1,000 MAU × 10 ad impressions/day × $2 CPM = ~$600/month
- (Requires significant user base to be meaningful)

---

#### Option C: Hybrid (Freemium + Ads)
**Model:**
- Free tier: 2 properties, with ads
- Pro tier (€4.99/month): Unlimited properties, no ads, advanced features

**Pros:**
- Multiple revenue streams
- Users can choose: pay for premium or tolerate ads
- Converts both high-intent users (pay) and casual users (ads)

**Cons:**
- More complex to implement (both subscription AND ads)
- Risk of confusing users with too many options

---

#### Option D: One-Time Purchase
**Model:**
- Free trial (7 days or 1 property)
- One-time payment (€14.99) for lifetime access

**Pros:**
- Simple for users (no subscriptions)
- Lower churn (one-time decision)
- No ongoing payment processing

**Cons:**
- No recurring revenue (unsustainable long-term)
- Need constant new users for revenue
- No incentive to maintain/improve app after purchase

**Not Recommended** (unless pivot to consulting/services model)

---

**Recommendation:**
**Start with Option B (Ads)** for MVP to validate user base, then **add Option A (Freemium)** once you have 500+ active users and understand their needs.

**Rationale:**
- Lower development cost for MVP (ads easier than payments)
- Validates market (do people use the app?)
- Can iterate to freemium based on user feedback
- Hybrid model (C) as final evolution

**Next Steps:**
1. Launch MVP free (no ads, no paywall) to gather users
2. Add ads after 100+ MAU
3. Introduce premium tier when users request advanced features
4. Monitor conversion rates and iterate

**Decision Required:** Which option to pursue for MVP?

---

### 2.2 Photo Compression Strategy
**Priority:** High (blocks MEDIA-4 implementation)
**Decision Needed By:** Sprint 2, Day 2
**Stakeholder:** filouzzz

**Question:**
Where should photo compression happen: client-side (user's device) or server-side (cloud function)?

**Context:**
- Users upload photos (typically 2-5MB from modern phones)
- Supabase free tier: 1GB storage limit
- 1GB ÷ 5MB = ~200 photos (very limited)
- With compression (1MB per photo): 1GB ÷ 1MB = ~1,000 photos (much better)

**Options:**

#### Option A: Client-Side Compression (Browser Canvas API)
**How it works:**
1. User selects photo
2. JavaScript loads image into Canvas element
3. Resize to max 1024x1024px, maintain aspect ratio
4. Re-encode at 80% JPEG quality
5. Upload compressed version to Supabase

**Code Example:**
```javascript
async function compressImage(file) {
  const canvas = document.createElement('canvas');
  const ctx = canvas.getContext('2d');
  const img = await createImageBitmap(file);

  const maxSize = 1024;
  let { width, height } = img;

  if (width > maxSize || height > maxSize) {
    if (width > height) {
      height *= maxSize / width;
      width = maxSize;
    } else {
      width *= maxSize / height;
      height = maxSize;
    }
  }

  canvas.width = width;
  canvas.height = height;
  ctx.drawImage(img, 0, 0, width, height);

  return new Promise((resolve) => {
    canvas.toBlob((blob) => resolve(blob), 'image/jpeg', 0.8);
  });
}
```

**Pros:**
- ✅ **Free** (no server costs)
- ✅ Faster upload (smaller file size)
- ✅ Saves bandwidth
- ✅ Works offline (compress locally, upload when online)
- ✅ Instant feedback (show before/after sizes)

**Cons:**
- ❌ Drains phone battery (image processing)
- ❌ Slower on old devices (but still acceptable for MVP)
- ❌ No original copy (can't recover full resolution later)

**Compression Ratio:**
- 5MB original → ~800KB compressed (~6x reduction)
- Storage impact: 1GB = ~1,250 photos (vs 200 uncompressed)

**Recommendation:** ✅ **Use this for MVP**

---

#### Option B: Server-Side Compression (Supabase Edge Function)
**How it works:**
1. User uploads original photo to Supabase Storage
2. Trigger Edge Function on upload event
3. Function downloads original, compresses, uploads compressed version
4. App uses compressed version, keeps original in archive

**Code Example:**
```typescript
// Supabase Edge Function
import { serve } from 'https://deno.land/std@0.168.0/http/server.ts';
import { createClient } from 'https://esm.sh/@supabase/supabase-js@2';

serve(async (req) => {
  const { path } = await req.json();
  const supabase = createClient(/* ... */);

  // Download original
  const { data: original } = await supabase.storage
    .from('property-media')
    .download(path);

  // Compress (using Deno image library)
  const compressed = await compressImage(original);

  // Upload compressed
  const compressedPath = path.replace('.jpg', '_compressed.jpg');
  await supabase.storage
    .from('property-media')
    .upload(compressedPath, compressed);

  return new Response('OK');
});
```

**Pros:**
- ✅ Keeps original (can recover full resolution)
- ✅ No battery drain on user device
- ✅ Consistent compression quality
- ✅ Can generate multiple sizes (thumbnail, medium, full)

**Cons:**
- ❌ **Costs money** (Edge Function compute time ~$0.01 per 1,000 invocations)
- ❌ Slower (upload original 5MB, then server processes)
- ❌ Requires additional storage (original + compressed = 2x)
- ❌ More complex (Edge Function deployment, error handling)

**When to use:**
- Post-MVP, when you have revenue to cover costs
- If users demand high-resolution originals
- If targeting professional photographers/agencies

---

#### Option C: Progressive/Hybrid
**How it works:**
1. Client compresses to "medium" quality (1024x1024, 85% quality)
2. Upload medium version
3. Optionally: User can choose "Keep original" (checkbox)
4. If checked, also upload original to separate "originals" bucket

**Pros:**
- ✅ Balance of quality and size
- ✅ User control
- ✅ Most storage-efficient

**Cons:**
- ❌ More complex UI
- ❌ Conditional logic

**Recommendation:** Save for v2.0+

---

**Recommendation for MVP:**
**Option A (Client-Side Compression)**

**Rationale:**
- Zero cost
- Fast development (just JavaScript)
- Acceptable quality loss (most users won't notice)
- Storage-efficient (fits more photos in free tier)
- Can upgrade to Option B post-MVP if users request originals

**Implementation Plan:**
1. Sprint 2, MEDIA-4: Implement Canvas-based compression
2. Settings: Compression level slider (80% default, 60-100% range) - optional
3. Show before/after file sizes to user
4. Post-MVP: Add "Keep original" option if requested

**Decision Required:** Confirm Option A or choose alternative?

---

### 2.3 Video Upload Support in MVP
**Priority:** Low (nice-to-have)
**Decision Needed By:** Sprint 2, Day 1
**Stakeholder:** filouzzz

**Question:**
Should we include video upload in MVP or defer to v2.0?

**Context:**
- Videos are 10-50MB for 30-second clip
- 1GB storage ÷ 20MB avg = only 50 videos (very limited)
- Video compression is more complex (requires ffmpeg or cloud service)
- Usage likely low compared to photos

**Options:**

#### Option A: Include in MVP
**Approach:**
- Allow video upload (MP4, max 30 seconds, max 50MB)
- No compression (upload as-is)
- Store in same Supabase Storage bucket

**Pros:**
- ✅ Feature completeness
- ✅ Users can document property tours

**Cons:**
- ❌ Quickly exhausts storage limit
- ❌ Large uploads on mobile (UX issue)
- ❌ Playback bandwidth costs

**Effort:** +5 story points (MEDIA-5: Video upload)

---

#### Option B: Defer to v2.0
**Approach:**
- Photos only for MVP
- Add videos in future iteration when:
  - Storage costs covered by revenue
  - Implement video compression (cloud service like Cloudinary, Mux)

**Pros:**
- ✅ Faster MVP delivery
- ✅ Lower storage costs
- ✅ Focus on core features

**Cons:**
- ❌ Missing feature (but low impact for MVP validation)

**Effort:** 0 points for MVP

---

**Recommendation:**
**Option B (Defer to v2.0)**

**Rationale:**
- Photos cover 90% of use case (property documentation)
- Videos are costly without compression
- Can add later when storage plan upgraded
- MVP focus: validate core calculator + property management

**Alternative:**
If video is critical, use **YouTube/Vimeo embeds** instead of uploads:
- User uploads video to YouTube (free, unlimited)
- Paste YouTube link in property
- Embed video player in app
- No storage cost, no bandwidth cost

**Decision Required:** Defer videos to v2.0, or include in MVP?

---

### 2.4 Data Privacy Level (Analytics & Ads Tracking)
**Priority:** High (impacts GDPR implementation)
**Decision Needed By:** Before implementing GDPR-2
**Stakeholder:** filouzzz

**Question:**
What level of data collection should we implement?

**Context:**
- More data = better insights + higher ad revenue (targeted ads pay 2-3x more)
- Less data = user trust + easier GDPR compliance
- Need balance between business needs and user privacy

**Options:**

#### Option A: Maximum Privacy (Privacy-First)
**What we collect:**
- Strictly necessary: User email, hashed password, property data
- NO analytics
- NO ad tracking
- NO cookies (except auth session)

**Privacy Tools:**
- No analytics platform
- No ads (at least initially)
- No third-party scripts

**Pros:**
- ✅ Simplest GDPR compliance (no consent needed for processing)
- ✅ Builds user trust
- ✅ No cookie banner needed
- ✅ Faster page load (no tracking scripts)

**Cons:**
- ❌ No insight into user behavior (flying blind)
- ❌ Can't optimize features based on usage data
- ❌ No ad revenue

**When to choose:**
- Privacy is core value proposition
- Targeting privacy-conscious users
- Subscription-only model (no ads)

---

#### Option B: Balanced Privacy (Recommended)
**What we collect:**
- Essential: User email, property data
- Analytics: Page views, feature usage (via **Plausible Analytics**)
  - Anonymized (no personal data)
  - No cookies
  - EU-based servers
- Ads: Contextual ads only (no behavioral tracking)

**Privacy Tools:**
- Plausible Analytics (€9/month for 10k pageviews, or self-hosted free)
- EthicalAds or Carbon Ads (privacy-friendly ad networks)
- Simple consent: "We use privacy-friendly analytics to improve the app"

**Pros:**
- ✅ Insights into user behavior (which features are used?)
- ✅ GDPR-compliant by design (Plausible doesn't require consent)
- ✅ Some ad revenue (lower than targeted ads, but ethical)
- ✅ User trust (transparent, minimal tracking)

**Cons:**
- ❌ Lower ad revenue than Option C (2-3x less)
- ❌ Small cost for analytics (€9/month)

**When to choose:**
- Prioritize user trust but need basic insights
- Freemium or ads model
- EU/privacy-focused market

**Recommendation:** ✅ **Use this**

---

#### Option C: Standard Tracking (Maximize Revenue)
**What we collect:**
- Essential: User email, property data
- Analytics: **Google Analytics** (full behavioral tracking)
- Ads: **Google AdMob** (personalized ads based on user profile)
- Tracking: User ID, device type, location, browsing behavior

**Privacy Tools:**
- Google Analytics 4 with consent mode
- Google AdMob with consent SDK
- Cookie consent banner (required)
- Privacy policy with extensive data collection disclosure

**Pros:**
- ✅ Maximum insights (understand every user action)
- ✅ Highest ad revenue (personalized ads pay 2-3x more)
- ✅ Industry standard tools (familiar to most developers)

**Cons:**
- ❌ Complex GDPR compliance (consent for everything)
- ❌ Cookie banner required (UX friction)
- ❌ User privacy concerns (tracking stigma)
- ❌ Depends on Google ecosystem

**When to choose:**
- Maximize revenue (ads-based model)
- Targeting non-EU markets (less strict privacy laws)
- Large-scale app (need detailed analytics)

---

**Recommendation for MVP:**
**Option B (Balanced Privacy)**

**Rationale:**
- Best balance of insights and trust
- GDPR-compliant by design (Plausible doesn't need consent)
- Simpler implementation (no cookie banner needed)
- Aligns with EU user expectations
- Can upgrade to Option C later if needed (but hard to downgrade)

**Implementation:**
- Use Plausible Analytics (self-hosted to save cost initially)
- Contextual ads OR freemium model (no tracking)
- Simple privacy policy ("We use Plausible Analytics to count page views")

**Decision Required:** Confirm Option B or choose alternative?

---

### 2.5 iOS App Timeline
**Priority:** Low (post-MVP)
**Decision Needed By:** After MVP launch
**Stakeholder:** filouzzz

**Question:**
When (if ever) should we build an iOS version?

**Context:**
- iOS development requires:
  - macOS (can use cloud Mac service like MacStadium, or GitHub Actions)
  - Apple Developer account ($99/year)
  - App Store review process (1-2 weeks)
- Capacitor supports iOS (same codebase)
- iOS users typically spend more than Android users (higher revenue potential)

**Options:**

#### Option A: Soon After MVP (Month 2-3)
**Timeline:**
- MVP → Android → iOS within 2-3 months

**Pros:**
- ✅ Capture iOS market early
- ✅ Dual-platform looks more professional

**Cons:**
- ❌ $99/year cost
- ❌ Need macOS access
- ❌ Divided focus (support 2 platforms)

---

#### Option B: When Revenue Justifies It
**Trigger:**
- Launch when Android app generates >€100/month revenue
- OR when users request iOS version (10+ requests)

**Pros:**
- ✅ Focus on single platform initially
- ✅ Cost-justified by revenue
- ✅ Validate Android market first

**Cons:**
- ❌ Miss iOS users early

**Recommendation:** ✅ **Use this approach**

---

#### Option C: Never (PWA Only)
**Approach:**
- PWA works on iOS Safari (installable)
- Skip native iOS app entirely
- Direct users to install PWA

**Pros:**
- ✅ No iOS costs
- ✅ Single codebase

**Cons:**
- ❌ PWA on iOS has limitations (notifications, offline)
- ❌ Not in App Store (discoverability issue)

---

**Recommendation:**
**Option B** (Wait for validation)

**Next Steps:**
1. Launch MVP on web (PWA) + Android
2. Monitor analytics: what % of visitors use iOS?
3. If iOS traffic >20% → prioritize iOS app
4. If iOS users request native app (10+ requests) → build it
5. Otherwise, PWA on iOS is sufficient

**Decision Required:** None yet (revisit after MVP launch)

---

## 3. Decisions Pending External Factors

### 3.1 Google Play Store Submission
**Status:** Blocked (waiting for MVP completion)
**Timeline:** Sprint 2, Week 2
**Requirements:**
- [ ] Privacy policy URL (can use GitHub Pages)
- [ ] App description, screenshots
- [ ] Developer account ($25 one-time fee)
- [ ] Signed APK
- [ ] Content rating questionnaire

**Next Steps:** Create checklist when ready to submit

---

### 3.2 Domain Name & Hosting
**Status:** Open question
**Timeline:** Before public launch

**Question:** What domain should we use?

**Options:**
- rentalyield.app (if available)
- calculatrice-rentabilite.fr (French-first)
- investpro.app (generic, brandable)

**Costs:**
- .app domain: ~€15/year
- .fr domain: ~€8/year

**Hosting:**
- Vercel (free tier, auto-deploy)

**Decision Required:** Choose domain before PWA launch

---

## 4. Decision-Making Process

### 4.1 Decision Priority Framework
| Priority | Response Time | Impact | Examples |
|----------|---------------|--------|----------|
| **Critical** | Same day | Blocks MVP development | Frontend framework choice |
| **High** | Within 3 days | Affects MVP features | Photo compression strategy |
| **Medium** | Within 1 week | Affects post-MVP | Monetization model |
| **Low** | When convenient | Nice-to-have | iOS app, domain name |

### 4.2 Decision Template

```markdown
## Question Title

**Priority:** [Critical/High/Medium/Low]
**Decision Needed By:** [Date or Sprint]
**Stakeholder:** [filouzzz / team]

**Context:**
[Why is this decision needed?]

**Options:**
1. Option A
   - Pros: ...
   - Cons: ...
2. Option B
   - Pros: ...
   - Cons: ...

**Recommendation:**
[Preferred option with rationale]

**Decision Required:**
[Yes/No/Alternative?]
```

---

## 5. Summary of Immediate Actions Needed

| Decision | Priority | Needed By | Status |
|----------|----------|-----------|--------|
| **Frontend Framework** | 🔴 Critical | Before Sprint 1, Day 1 | ⏳ PENDING |
| **Photo Compression** | 🟡 High | Sprint 2, Day 2 | ⏳ PENDING |
| **Monetization Model** | 🟡 High | Before monetization features | ⏳ PENDING |
| **Data Privacy Level** | 🟡 High | Before GDPR implementation | ⏳ PENDING |
| **Video Upload in MVP** | 🟢 Low | Sprint 2, Day 1 | ⏳ PENDING |
| **iOS App Timeline** | 🟢 Low | Post-MVP | ⏳ PENDING |

---

## 6. How to Make Decisions Efficiently

### For filouzzz:
When reviewing this document, for each pending decision:

1. **Read the context** (understand why it matters)
2. **Review options** (pros/cons)
3. **See recommendation** (Claude's suggestion)
4. **Decide:**
   - ✅ "Approve recommendation"
   - ❌ "Use Option X instead because..."
   - ❓ "I need more information about..."

### Example Response:
```
ADR-002 (Frontend Framework): Approve React + Vite recommendation
Question 2.2 (Photo Compression): Approve Option A (client-side)
Question 2.4 (Data Privacy): Approve Option B (balanced)
Question 2.3 (Video Upload): Defer to v2.0
```

---

**Document Status:** Ready for Review & Decisions
**Next Steps:**
1. filouzzz reviews all pending decisions
2. Provide answers/approvals
3. Update this document with final decisions
4. Proceed with Sprint 1 based on confirmed architecture
