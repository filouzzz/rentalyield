# Product Backlog
## Rental Yield Calculator - Property Investment Manager

**Version:** 1.0
**Last Updated:** November 14, 2025
**Sprint Duration:** 1 week
**Team Velocity:** TBD (first sprint will establish baseline)

---

## 1. Backlog Overview

### 1.1 Story Point Scale (Fibonacci)
- **1 point:** 1-2 hours (trivial task)
- **2 points:** 2-4 hours (simple feature)
- **3 points:** 4-8 hours (small feature)
- **5 points:** 1-2 days (medium feature)
- **8 points:** 2-3 days (large feature)
- **13 points:** 3-5 days (very large, should be broken down)

### 1.2 Priority Levels
- **P0 (Critical):** MVP blocker, must have
- **P1 (High):** Important for MVP
- **P2 (Medium):** Nice to have for MVP
- **P3 (Low):** Post-MVP, future iterations

---

## 2. Sprint 1: Foundation (Week 1)
**Goal:** Set up infrastructure, authentication, and basic property management

### 2.1 Epic: Project Setup & Infrastructure
**Total: 8 points**

| ID | User Story | Acceptance Criteria | Story Points | Priority | Status |
|----|-----------|---------------------|--------------|----------|--------|
| SETUP-1 | As a developer, I want to initialize the project with the chosen tech stack so we can start building features | - [ ] Create React/Vue + Vite project<br>- [ ] Install dependencies (Tailwind, Supabase client, Dexie.js)<br>- [ ] Set up ESLint + Prettier<br>- [ ] Configure Vite build<br>- [ ] Create folder structure | 3 | P0 | Todo |
| SETUP-2 | As a developer, I want to set up Supabase project so we have backend services ready | - [ ] Create Supabase project (EU region)<br>- [ ] Configure database schema (run SQL from ARCHITECTURE.md)<br>- [ ] Enable RLS policies<br>- [ ] Set up Storage bucket (property-media)<br>- [ ] Get API keys and add to .env | 2 | P0 | Todo |
| SETUP-3 | As a developer, I want to configure CI/CD pipeline so deployments are automated | - [ ] Create GitHub Actions workflow<br>- [ ] Configure Vercel deployment<br>- [ ] Set up staging environment<br>- [ ] Add environment variables | 2 | P1 | Todo |
| SETUP-4 | As a developer, I want to implement basic routing so we can navigate between pages | - [ ] Install React Router / Vue Router<br>- [ ] Create routes: /, /login, /signup, /dashboard, /properties/:id<br>- [ ] Implement 404 page | 1 | P0 | Todo |

---

### 2.2 Epic: Authentication & User Management
**Total: 13 points**

| ID | User Story | Acceptance Criteria | Story Points | Priority | Status |
|----|-----------|---------------------|--------------|----------|--------|
| AUTH-1 | As a new user, I want to sign up with email and password so I can create an account | - [ ] Sign up form (email, password, confirm password)<br>- [ ] Client-side validation (Zod schema)<br>- [ ] Call Supabase signup API<br>- [ ] Email verification sent<br>- [ ] Show success message<br>- [ ] Handle errors (email already exists, weak password) | 3 | P0 | Todo |
| AUTH-2 | As a registered user, I want to log in with my credentials so I can access my properties | - [ ] Login form (email, password)<br>- [ ] Call Supabase login API<br>- [ ] Store JWT tokens securely<br>- [ ] Redirect to dashboard on success<br>- [ ] Show error for invalid credentials<br>- [ ] Rate limiting (client-side feedback) | 2 | P0 | Todo |
| AUTH-3 | As a logged-in user, I want to log out so I can secure my account | - [ ] Logout button in header/menu<br>- [ ] Call Supabase logout API<br>- [ ] Clear local tokens<br>- [ ] Clear IndexedDB (optional, with confirmation)<br>- [ ] Redirect to login page | 1 | P0 | Todo |
| AUTH-4 | As a user, I want to reset my password if I forget it so I can regain access | - [ ] "Forgot password?" link on login page<br>- [ ] Email input form<br>- [ ] Call Supabase password reset API<br>- [ ] Show "Check your email" message<br>- [ ] Password reset link in email → update password form | 2 | P1 | Todo |
| AUTH-5 | As a security-conscious user, I want to enable 2FA so my account is more secure | - [ ] 2FA settings page<br>- [ ] Choose 2FA method (Email-based for MVP)<br>- [ ] Enable/disable toggle<br>- [ ] Generate backup codes<br>- [ ] 2FA verification during login | 5 | P1 | Todo |

---

### 2.3 Epic: Core Property Management (Offline-First)
**Total: 21 points**

| ID | User Story | Acceptance Criteria | Story Points | Priority | Status |
|----|-----------|---------------------|--------------|----------|--------|
| PROP-1 | As a user, I want to see a dashboard with all my properties so I can get an overview | - [ ] Dashboard page with property list<br>- [ ] Show property cards (name, address, yield)<br>- [ ] Empty state: "Add your first property"<br>- [ ] Load from IndexedDB<br>- [ ] Sync from Supabase on initial load | 3 | P0 | Todo |
| PROP-2 | As a user, I want to create a new property so I can track its rental yield | - [ ] "Add Property" button → form<br>- [ ] Form fields (see PRD section 3.3)<br>- [ ] Client-side validation<br>- [ ] Save to IndexedDB immediately<br>- [ ] Add to sync queue<br>- [ ] Optimistic UI update<br>- [ ] Calculate yield client-side | 5 | P0 | Todo |
| PROP-3 | As a user, I want to view property details so I can see all information and calculations | - [ ] Property detail page (/properties/:id)<br>- [ ] Display all fields<br>- [ ] Show calculated results:<br>&nbsp;&nbsp;- Monthly payment<br>&nbsp;&nbsp;- Total credit cost<br>&nbsp;&nbsp;- Gross rental yield<br>- [ ] Color-coded yield indicator<br>- [ ] Load from IndexedDB | 3 | P0 | Todo |
| PROP-4 | As a user, I want to edit an existing property so I can update its information | - [ ] "Edit" button on property detail page<br>- [ ] Pre-populate form with current data<br>- [ ] Save changes to IndexedDB<br>- [ ] Mark as dirty + add to sync queue<br>- [ ] Recalculate yield<br>- [ ] Update UI optimistically | 3 | P0 | Todo |
| PROP-5 | As a user, I want to delete a property so I can remove properties I no longer own | - [ ] "Delete" button with confirmation dialog<br>- [ ] Soft delete in IndexedDB (deletedAt)<br>- [ ] Add to sync queue (delete operation)<br>- [ ] Remove from UI immediately<br>- [ ] Cascade delete media (photos) | 2 | P0 | Todo |
| PROP-6 | As a developer, I want to implement IndexedDB schema and helpers so offline storage works | - [ ] Set up Dexie.js database<br>- [ ] Create tables (properties, media, syncQueue)<br>- [ ] Implement CRUD helpers<br>- [ ] Add indexes for performance<br>- [ ] Test read/write operations | 3 | P0 | Todo |
| PROP-7 | As a developer, I want to implement background sync worker so offline changes sync to cloud | - [ ] Sync service/worker<br>- [ ] Process sync queue on interval (every 30s when online)<br>- [ ] Send create/update/delete to Supabase<br>- [ ] Handle success: mark synced, clear isDirty<br>- [ ] Handle failure: retry with exponential backoff<br>- [ ] Conflict resolution (last-write-wins)<br>- [ ] Show sync status indicator in UI | 5 | P0 | Todo |

---

### 2.4 Sprint 1 Summary
**Total Story Points:** 42 points
**Estimated Duration:** 5-7 days (depending on developer velocity)
**Deliverables:**
- ✅ Working authentication (signup, login, logout, password reset)
- ✅ Property CRUD operations (create, read, update, delete)
- ✅ Offline-first architecture with background sync
- ✅ Dashboard with property list
- ✅ Basic calculations (yield, monthly payment, total cost)

---

## 3. Sprint 2: Photos, PWA, & Polish (Week 2)
**Goal:** Add photo management, finalize PWA, build Android app, launch MVP

### 3.1 Epic: Photo & Media Management
**Total: 13 points**

| ID | User Story | Acceptance Criteria | Story Points | Priority | Status |
|----|-----------|---------------------|--------------|----------|--------|
| MEDIA-1 | As a user, I want to upload photos for each property so I can document them visually | - [ ] Photo upload button on property detail page<br>- [ ] File input (multiple photos)<br>- [ ] Client-side compression (Canvas API)<br>- [ ] Upload to Supabase Storage<br>- [ ] Save metadata to property_media table<br>- [ ] Show upload progress<br>- [ ] Handle offline: queue for later upload | 5 | P0 | Todo |
| MEDIA-2 | As a user, I want to view all photos for a property in a gallery so I can browse them easily | - [ ] Photo gallery component<br>- [ ] Grid layout (3 columns on mobile)<br>- [ ] Click to view full-size<br>- [ ] Lightbox/modal for full-size view<br>- [ ] Swipe between photos (mobile)<br>- [ ] Load from Supabase Storage<br>- [ ] Offline: show cached photos | 3 | P1 | Todo |
| MEDIA-3 | As a user, I want to delete photos so I can remove unwanted images | - [ ] Delete button on each photo<br>- [ ] Confirmation dialog<br>- [ ] Delete from Supabase Storage<br>- [ ] Remove from property_media table<br>- [ ] Update UI immediately<br>- [ ] Offline: queue delete operation | 2 | P1 | Todo |
| MEDIA-4 | As a developer, I want to implement client-side photo compression so uploads are faster and storage costs are lower | - [ ] Canvas API compression function<br>- [ ] Target: max 1024x1024, 80% quality<br>- [ ] Generate thumbnail (200x200)<br>- [ ] Maintain aspect ratio<br>- [ ] Show before/after file sizes<br>- [ ] Test with various image sizes | 3 | P1 | Todo |

---

### 3.2 Epic: PWA & Offline Capabilities
**Total: 8 points**

| ID | User Story | Acceptance Criteria | Story Points | Priority | Status |
|----|-----------|---------------------|--------------|----------|--------|
| PWA-1 | As a developer, I want to configure Service Worker so the app works offline | - [ ] Install Workbox<br>- [ ] Configure caching strategies:<br>&nbsp;&nbsp;- Cache-First for static assets<br>&nbsp;&nbsp;- Network-First for API calls<br>&nbsp;&nbsp;- Cache-Only for offline fallback<br>- [ ] Precache app shell<br>- [ ] Test offline mode | 3 | P0 | Todo |
| PWA-2 | As a user, I want to install the app on my home screen so it feels like a native app | - [ ] Create web app manifest.json<br>- [ ] Define app name, icons, theme color<br>- [ ] Generate all icon sizes (192x192, 512x512)<br>- [ ] Test "Add to Home Screen" on Android<br>- [ ] Show install prompt (if supported) | 2 | P0 | Todo |
| PWA-3 | As a user, I want to see online/offline status so I know when my changes will sync | - [ ] Network status detector<br>- [ ] Show banner: "You're offline" / "Back online"<br>- [ ] Sync indicator: "Syncing..." / "Synced ✓" / "X items pending"<br>- [ ] Auto-trigger sync when reconnected | 2 | P1 | Todo |
| PWA-4 | As a user, I want the app to work offline for all core features so I can use it anywhere | - [ ] Test all features offline:<br>&nbsp;&nbsp;- View properties ✓<br>&nbsp;&nbsp;- Create property ✓<br>&nbsp;&nbsp;- Edit property ✓<br>&nbsp;&nbsp;- Delete property ✓<br>&nbsp;&nbsp;- View cached photos ✓<br>- [ ] Show appropriate offline messaging<br>- [ ] Queue all changes for sync | 1 | P0 | Todo |

---

### 3.3 Epic: GDPR & Privacy Compliance
**Total: 8 points**

| ID | User Story | Acceptance Criteria | Story Points | Priority | Status |
|----|-----------|---------------------|--------------|----------|--------|
| GDPR-1 | As a user, I want to read a clear privacy policy so I understand how my data is used | - [ ] Write privacy policy (French)<br>- [ ] Cover: data collected, purpose, retention, rights<br>- [ ] Create /privacy page<br>- [ ] Link from footer and signup page<br>- [ ] Plain language, no legal jargon | 2 | P0 | Todo |
| GDPR-2 | As a user, I want to give/revoke consent for data processing so I control my privacy | - [ ] Consent banner on first visit<br>- [ ] Options: Accept All / Reject Non-Essential / Customize<br>- [ ] Granular toggles:<br>&nbsp;&nbsp;- Essential (always on)<br>&nbsp;&nbsp;- Analytics (optional)<br>&nbsp;&nbsp;- Marketing (optional)<br>- [ ] Store consent in profiles table + localStorage<br>- [ ] Respect consent (don't load analytics if rejected) | 3 | P0 | Todo |
| GDPR-3 | As a user, I want to download all my data so I can see what's stored | - [ ] "Download My Data" button in settings<br>- [ ] Generate JSON file with:<br>&nbsp;&nbsp;- Profile info<br>&nbsp;&nbsp;- All properties<br>&nbsp;&nbsp;- Media metadata (+ download links)<br>- [ ] Trigger browser download<br>- [ ] Log export event (for compliance) | 2 | P1 | Todo |
| GDPR-4 | As a user, I want to delete my account and all data so I can exercise my right to erasure | - [ ] "Delete Account" button (with warning)<br>- [ ] Confirmation: "Type DELETE to confirm"<br>- [ ] Hard delete from Supabase:<br>&nbsp;&nbsp;- auth.users<br>&nbsp;&nbsp;- profiles<br>&nbsp;&nbsp;- properties<br>&nbsp;&nbsp;- property_media<br>&nbsp;&nbsp;- Supabase Storage files<br>- [ ] Clear local IndexedDB<br>- [ ] Redirect to goodbye page | 1 | P0 | Todo |

---

### 3.4 Epic: UI/UX Polish & Responsiveness
**Total: 5 points**

| ID | User Story | Acceptance Criteria | Story Points | Priority | Status |
|----|-----------|---------------------|--------------|----------|--------|
| UI-1 | As a user, I want a clean, intuitive interface so the app is easy to use | - [ ] Consistent color scheme (blue accent from v1)<br>- [ ] Tailwind utility classes<br>- [ ] Responsive layout (mobile-first)<br>- [ ] Loading states (skeletons)<br>- [ ] Error states (friendly messages)<br>- [ ] Success feedback (toasts) | 3 | P1 | Todo |
| UI-2 | As a mobile user, I want the app to look great on my phone so I enjoy using it | - [ ] Test on 320px (iPhone SE) to 428px (iPhone 14 Pro Max)<br>- [ ] Touch-friendly buttons (min 44x44px)<br>- [ ] Proper keyboard handling (input focus)<br>- [ ] No horizontal scroll<br>- [ ] Readable font sizes (min 16px) | 1 | P1 | Todo |
| UI-3 | As a user, I want helpful tooltips and labels so I understand financial terms | - [ ] Tooltips for jargon (TAEG, rentabilité brute, etc.)<br>- [ ] Help icons (?) with explanations<br>- [ ] Placeholder text in inputs (e.g., "250000" for cost)<br>- [ ] Validation messages (clear, actionable) | 1 | P2 | Todo |

---

### 3.5 Epic: Android App Build & Deployment
**Total: 5 points**

| ID | User Story | Acceptance Criteria | Story Points | Priority | Status |
|----|-----------|---------------------|--------------|----------|--------|
| ANDROID-1 | As a developer, I want to set up Capacitor for Android so we can build a native app | - [ ] Install Capacitor<br>- [ ] Initialize Android platform<br>- [ ] Configure capacitor.config.json<br>- [ ] Add app icons and splash screen<br>- [ ] Test on Android emulator/device | 2 | P0 | Todo |
| ANDROID-2 | As a developer, I want to build a signed APK so users can install the app | - [ ] Generate signing key (keystore)<br>- [ ] Configure build.gradle<br>- [ ] Build release APK<br>- [ ] Test installation on real device<br>- [ ] Document signing process | 2 | P0 | Todo |
| ANDROID-3 | As a user, I want to download the app from GitHub Releases so I can install it on my phone | - [ ] Create GitHub release (v2.0.0)<br>- [ ] Upload APK file<br>- [ ] Write installation instructions<br>- [ ] Test download and install flow | 1 | P1 | Todo |

---

### 3.6 Epic: Testing & Bug Fixes
**Total: 5 points**

| ID | User Story | Acceptance Criteria | Story Points | Priority | Status |
|----|-----------|---------------------|--------------|----------|--------|
| TEST-1 | As a developer, I want to write unit tests for critical functions so we catch bugs early | - [ ] Test calculation functions (yield, monthly payment)<br>- [ ] Test sync logic (queue, conflict resolution)<br>- [ ] Test form validation<br>- [ ] Vitest configuration<br>- [ ] Min 70% code coverage for utils | 3 | P2 | Todo |
| TEST-2 | As a QA tester, I want to perform manual end-to-end testing so we ensure quality | - [ ] Test all user flows (signup → add property → sync)<br>- [ ] Test offline mode thoroughly<br>- [ ] Cross-browser testing (Chrome, Firefox, Safari)<br>- [ ] Mobile testing (Android, iOS if available)<br>- [ ] Document bugs in GitHub Issues<br>- [ ] Fix P0/P1 bugs before launch | 2 | P0 | Todo |

---

### 3.7 Sprint 2 Summary
**Total Story Points:** 44 points
**Estimated Duration:** 5-7 days
**Deliverables:**
- ✅ Photo upload and gallery
- ✅ PWA with offline mode fully functional
- ✅ Android APK ready for distribution
- ✅ GDPR compliance (privacy policy, consent, data export, account deletion)
- ✅ Polished UI/UX
- ✅ MVP ready for beta testing

---

## 4. Post-MVP Backlog (Priority 3 - Future Sprints)

### 4.1 Advanced Features (Nice-to-Have)

| ID | Feature | Description | Story Points | Priority |
|----|---------|-------------|--------------|----------|
| CALC-1 | Net Rental Yield | Calculate yield after expenses (tax, fees, insurance, vacancy) | 3 | P3 |
| CALC-2 | Cash Flow Projection | Show monthly/annual cash flow over loan duration | 5 | P3 |
| CALC-3 | ROI Calculator | Return on investment percentage | 2 | P3 |
| VIZ-1 | Payment Breakdown Chart | Pie/bar chart showing principal vs interest | 5 | P3 |
| VIZ-2 | Portfolio Overview | Aggregate view of all properties (total value, total yield) | 3 | P3 |
| SHARE-1 | Export Property as PDF | Generate PDF report for sharing/printing | 5 | P3 |
| SHARE-2 | Share via Link | Generate shareable URL for property details | 3 | P3 |
| NOTIF-1 | Push Notifications | Rent reminders, loan payment alerts | 8 | P3 |
| I18N-1 | Multi-language Support | Add English translation | 5 | P3 |
| VIDEO-1 | Video Upload | Allow short video clips (30s max) | 8 | P3 |
| COLLAB-1 | Multi-user Access | Invite co-owners to view/edit property | 13 | P3 |
| IOS-1 | iOS App | Build and publish to App Store | 8 | P3 |

---

### 4.2 Monetization Features (When Ready)

| ID | Feature | Description | Story Points | Priority |
|----|---------|-------------|--------------|----------|
| MONET-1 | Google AdMob Integration | Display ads in free tier | 5 | P3 |
| MONET-2 | Payment Gateway | Stripe integration for premium subscriptions | 8 | P3 |
| MONET-3 | Freemium Logic | Limit free users to 2 properties, unlock for paid | 3 | P3 |
| MONET-4 | Subscription Management | User can upgrade/downgrade/cancel | 5 | P3 |

---

### 4.3 SEO & Marketing (When Ready for Public Launch)

| ID | Feature | Description | Story Points | Priority |
|----|---------|-------------|--------------|----------|
| SEO-1 | Meta Tags & OG Images | Social sharing previews | 2 | P3 |
| SEO-2 | Sitemap & robots.txt | For search engine indexing | 1 | P3 |
| SEO-3 | Landing Page | Marketing site separate from app | 5 | P3 |
| SEO-4 | Blog/Content | Educational articles on rental investment | 13 | P3 |

---

## 5. Backlog Grooming Notes

### 5.1 Definition of Ready (DoR)
A story is ready for sprint if:
- [ ] User story is clearly written (As a... I want... So that...)
- [ ] Acceptance criteria are defined and testable
- [ ] Story points are estimated
- [ ] Dependencies identified
- [ ] No blockers

### 5.2 Definition of Done (DoD)
A story is done when:
- [ ] Code implemented and tested locally
- [ ] Unit tests written (if applicable)
- [ ] Manual testing completed
- [ ] Code reviewed (if team > 1 person)
- [ ] Merged to develop branch
- [ ] Deployed to staging
- [ ] Acceptance criteria verified
- [ ] Documentation updated (if needed)

### 5.3 Backlog Refinement Schedule
- **Weekly:** Review and re-prioritize backlog
- **After each sprint:** Groom next sprint's stories
- **Ad-hoc:** Add new stories as ideas emerge

---

## 6. Risk Items (Flagged Stories)

| ID | Risk | Mitigation | Status |
|----|------|------------|--------|
| PROP-7 | Background sync complexity may cause data loss | Thorough testing, backup before sync, conflict logs | Monitor |
| MEDIA-1 | Photo upload in offline mode may fail on sync | Queue with retry logic, clear error messages | Monitor |
| GDPR-4 | Account deletion may not fully remove all data | Audit deletion queries, test thoroughly, compliance review | Monitor |
| ANDROID-2 | Signing key loss would prevent app updates | Back up keystore securely, document recovery process | Mitigate |

---

## 7. Velocity Tracking

### Sprint 1 (Planned)
- **Committed:** 42 points
- **Completed:** TBD
- **Velocity:** TBD

### Sprint 2 (Planned)
- **Committed:** 44 points
- **Completed:** TBD
- **Velocity:** TBD

**Note:** First sprint establishes baseline velocity. Future sprints adjust based on actual capacity.

---

## 8. Epics Overview (Visual Roadmap)

```
MVP Timeline (2 weeks)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Week 1: Foundation
├─ Project Setup ████░░ (3/5 days)
├─ Authentication ██████ (Full week)
└─ Property CRUD  ████████ (Continues into Week 2)

Week 2: Polish & Launch
├─ Photo Management ████░░
├─ PWA Configuration ███░░░
├─ GDPR Compliance ████░░
├─ Android Build ██░░░░
└─ Testing & Fixes ██████

Post-MVP (Week 3+)
├─ Beta Testing
├─ User Feedback
├─ Iteration
├─ Monetization
└─ Public Launch
```

---

## Appendix A: Story Template

```markdown
## [ID] Story Title

**As a** [user type]
**I want** [goal/action]
**So that** [benefit/value]

### Acceptance Criteria
- [ ] Given [context], when [action], then [outcome]
- [ ] ...

### Technical Notes
- Implementation hints
- API endpoints needed
- Dependencies

### Story Points
[1, 2, 3, 5, 8, 13]

### Priority
[P0, P1, P2, P3]
```

---

## Appendix B: Burn-down Chart (To be updated during sprints)

```
Story Points Remaining
│
50│ ●
  │   ╲
40│     ●
  │       ╲
30│         ●
  │           ╲
20│             ●
  │               ╲
10│                 ●
  │                   ╲
 0│___________________●___
  Day1 Day2 Day3 Day4 Day5 Day6 Day7

  ● = Actual    ╲ = Ideal
```

---

**Document Status:** Ready for Sprint Planning
**Next Steps:**
1. Review and prioritize stories
2. Confirm Sprint 1 scope (42 points realistic?)
3. Assign stories to developer(s)
4. Begin Sprint 1 - Day 1: SETUP-1, SETUP-2
