# 🏠 Rental Yield Calculator
**French Property Investment Manager**

[![Version](https://img.shields.io/badge/version-2.0.0--planning-blue)](https://github.com/filouzzz/rentalyield)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![Status](https://img.shields.io/badge/status-planning-yellow)](BACKLOG.md)

A modern, offline-first Progressive Web App (PWA) for French real estate investors to calculate rental yields, manage multiple properties, and track investment performance across all devices.

---

## 📋 Project Status

**Current Phase:** 🎯 Planning & Architecture Design
**Target MVP:** 1-2 weeks
**Next Steps:** Review planning documents → Make key decisions → Begin Sprint 1

### Planning Documents (Read in Order)
1. **[PRD.md](PRD.md)** - Product Requirements Document
   - Vision, features, user stories, success criteria
2. **[ARCHITECTURE.md](ARCHITECTURE.md)** - Technical Architecture
   - System design, database schema, tech stack, security
3. **[BACKLOG.md](BACKLOG.md)** - Product Backlog
   - Prioritized user stories, sprint planning, task breakdown
4. **[DECISIONS.md](DECISIONS.md)** - Decision Log & Open Questions
   - Architecture decisions, pending choices, trade-off analysis

---

## ✨ Features (Planned MVP)

### Core Functionality
- ✅ **Multi-Property Management** - Track unlimited rental properties
- ✅ **Advanced Calculations** - Rental yield, loan payments, total costs
- ✅ **Offline-First** - Create/edit properties offline, auto-sync when online
- ✅ **Photo Upload** - Document properties with compressed photos
- ✅ **Cross-Device Sync** - Access data from phone, tablet, desktop
- ✅ **Secure Authentication** - Email/password + 2FA support
- ✅ **GDPR Compliant** - EU data hosting, privacy controls, data export

### Platforms
- 🌐 **Progressive Web App (PWA)** - Installable on any device
- 📱 **Android App** - Native APK via Capacitor
- 🍎 **iOS App** - Post-MVP (when validated)

---

## 🛠 Tech Stack

### Frontend
- **Framework:** React 18 + Vite (or Vue 3)
- **Styling:** Tailwind CSS
- **State:** React Context API / Zustand
- **Offline:** IndexedDB (via Dexie.js) + Service Worker (Workbox)
- **Mobile:** Capacitor 6

### Backend (BaaS)
- **Platform:** Supabase (open-source Firebase alternative)
- **Database:** PostgreSQL with Row-Level Security
- **Auth:** Supabase Auth (JWT, 2FA, email verification)
- **Storage:** Supabase Storage (S3-compatible)
- **Realtime:** WebSocket-based sync

### DevOps
- **Hosting:** Vercel (PWA) + GitHub Releases (Android APK)
- **CI/CD:** GitHub Actions
- **Monitoring:** Sentry (errors) + Plausible Analytics (privacy-friendly)
- **Region:** EU (Paris/Frankfurt) for GDPR compliance

---

## 📁 Project Structure

```
rentalyield/
├── docs/
│   ├── PRD.md                    # Product requirements
│   ├── ARCHITECTURE.md           # Technical design
│   ├── BACKLOG.md               # Sprint planning
│   └── DECISIONS.md             # Decision log
├── src/                         # Source code (TBD in Sprint 1)
├── android/                     # Capacitor Android project
├── www/                         # PWA build output
├── SESSION_NOTES.md            # Development learning log
└── README.md                   # This file
```

---

## 🚀 Getting Started (For Developers)

### Prerequisites
- Node.js 18+ and npm/pnpm
- Supabase account (free tier)
- Git

### Installation (When Development Starts)
```bash
# Clone repository
git clone https://github.com/filouzzz/rentalyield.git
cd rentalyield

# Install dependencies
npm install

# Set up environment variables
cp .env.example .env
# Add your Supabase URL and API keys to .env

# Run development server
npm run dev

# Build for production
npm run build

# Build Android app
npm run build:android
```

---

## 📖 Documentation

### For Product Owners
- **[PRD.md](PRD.md)** - Understand features, user personas, success metrics
- **[BACKLOG.md](BACKLOG.md)** - See prioritized features and timeline

### For Developers
- **[ARCHITECTURE.md](ARCHITECTURE.md)** - System design, database schema, APIs
- **[DECISIONS.md](DECISIONS.md)** - Why we made specific technical choices

### For Users
- **User Guide** (Coming with MVP)
- **Privacy Policy** (Coming with MVP)

---

## 🎯 Roadmap

### Phase 1: MVP (Weeks 1-2) - IN PLANNING
- [x] Planning documents complete
- [ ] Key decisions made (frontend framework, photo compression, etc.)
- [ ] Sprint 1: Foundation (auth, property CRUD, offline storage)
- [ ] Sprint 2: Photos, PWA, GDPR, Android app
- [ ] Beta testing with 5-10 users
- [ ] Public launch (web + Android)

### Phase 2: Post-MVP (Month 2+)
- [ ] Advanced calculations (net yield, ROI, cash flow)
- [ ] Data visualization (charts, portfolio overview)
- [ ] Monetization (freemium or ads)
- [ ] Google Play Store submission
- [ ] iOS app (when validated)

### Phase 3: Growth (Month 3+)
- [ ] Multi-language support (English, Spanish)
- [ ] Property comparison tools
- [ ] PDF export
- [ ] Collaboration features
- [ ] API for integrations

---

## 🤝 Contributing

This project is currently in active development by [@filouzzz](https://github.com/filouzzz) with AI-assisted coding (Claude Code).

**Interested in contributing?**
- 🐛 Report bugs via [GitHub Issues](https://github.com/filouzzz/rentalyield/issues)
- 💡 Suggest features in [Discussions](https://github.com/filouzzz/rentalyield/discussions)
- 🔀 PRs welcome after MVP launch

---

## 📊 Current Release

**[v1.0.0](https://github.com/filouzzz/rentalyield/releases/tag/v1.0.0)** - Simple Calculator (Nov 2025)
- Single-page rental yield calculator
- Basic calculations (yield, loan payments)
- No user accounts, local-only data
- French interface

**Next:** v2.0.0 MVP with full property management (see [BACKLOG.md](BACKLOG.md))

---

## 🔒 Privacy & Security

- **GDPR Compliant:** EU data hosting, minimal data collection, user rights respected
- **Security:** HTTPS, JWT auth, Row-Level Security, encrypted storage
- **Privacy Policy:** (Coming with MVP)
- **No Tracking:** Privacy-friendly analytics only (Plausible)

---

## 📄 License

MIT License - See [LICENSE](LICENSE) for details

---

## 🙋 Support & Contact

- **Issues:** [GitHub Issues](https://github.com/filouzzz/rentalyield/issues)
- **Discussions:** [GitHub Discussions](https://github.com/filouzzz/rentalyield/discussions)
- **Email:** (Add your contact when ready for public launch)

---

## 🌟 Acknowledgments

- Built with [Claude Code](https://claude.ai/claude-code) - AI pair programming
- Inspired by [imavenir.com rental calculator](https://imavenir.com/investissement-locatif-cle-en-main/calculette-rentabilite-locative/)
- Powered by [Supabase](https://supabase.com) open-source backend

---

**⭐ Star this repo if you find it useful!**

**Made with ❤️ in France 🇫🇷**
