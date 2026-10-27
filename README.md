# Rezz-I

**Your reputation shouldn't clock out with your last shift.**

Rezz-i is a portable reputation platform for service and hospitality workers. Guests review the *individual* — the server, bartender, barista, or host — and that reputation follows the worker from venue to venue. The product is about people connecting with people: design and copy center the worker, not the venue.

> **Status:** Pre-build. Spec v1.0 is drafted and the build is gated on the Phase 0 questions in [`spec.md` §12](spec.md#12-phase-gate-questions).
> **Launch market:** Restaurants and bars in Des Moines, IA.

---

## How it works

1. **Staff build a profile** — roles, skills, specialties, tenure, and an "about me," with a public URL at `rezz-i.com/p/<handle>`.
2. **Staff check in on shift** — a geofenced check-in marks them "on shift" at their venue.
3. **Guests scan the staff member's QR code** — a rotating, signed code shown on the staff member's phone (or a printed card that only works while they're checked in).
4. **Guests rate and review** — a 1–5 star rating, short screener questions that separate things outside the worker's control (food quality, kitchen delays), and optional text.
5. **Staff see it first** — every review passes AI moderation, then goes to the staff member for a preview window where they can flag it before it goes live.
6. **Guests follow** — and get notified when that person is on shift again: *"Maya is on shift tonight at [Venue]."*

### Core principles

- **One living review** per guest per staff member. A new review replaces the old one, which is kept in the audit history.
- **QR-gated reviews** plus phone-verified guests, velocity limits, and self-review blocking to prevent fake reviews.
- **Businesses can never review, rate, or flag staff.** This is enforced in the database, not just the UI.
- **Workers control their visibility** — show the venue, the city only, or hide the workplace entirely; block any follower.
- **Adults only** — all accounts are 18+.
- **AI flags, people decide** — automated moderation holds content for human review; final decisions are made by people and logged.

---

## v1 scope

| In v1 | After launch |
|---|---|
| Guest and staff accounts, onboarding, staff profiles | Business accounts and subscriptions |
| Geofenced check-in and 2-week employment confirmation | Staff Pro tier (private messaging, expanded reactions) |
| Secure rotating QR codes | Referrals |
| Rate, review, screener questions, moderation | Guest advertising |
| Follow, web push notifications, discovery | Native app wrappers, second city |
| Admin moderation queue with audit trail | |

---

## Tech stack

| Layer | Choice |
|---|---|
| Front end | React 18, Vite, TypeScript, Tailwind CSS, shadcn/ui, React Router, TanStack Query |
| App shell | Installable PWA (`vite-plugin-pwa`) |
| Backend | Supabase — Postgres + PostGIS, Auth, Storage, Edge Functions (Deno), `pg_cron` |
| Security | Row Level Security on every table; roles in a separate `user_roles` table |
| Notifications | Web Push (VAPID), with email/SMS fallback |
| Moderation | Edge Function calling an LLM behind a swappable `ai_client` wrapper |
| Errors / analytics | Sentry; PostHog or Plausible (TBD) |
| Testing | Vitest (unit), Playwright (end-to-end) |
| Hosting / build | Lovable, with two-way GitHub sync; logic and SQL written with Claude Code |

---

## Repository layout

**Current (planning):**

```
├─ spec.md                       # Build spec v1.0 — the source of truth for the build
├─ sessions.md                   # Decision log from planning sessions
├─ spec_v0.3_archive.md          # Superseded spec (reference only)
├─ Rezz-i notes/Rezz-i.md        # Feature outline for the initial build
├─ builddeck/                    # Developer Product & Build Guide (wins on conflicts)
├─ design/                       # Early design canvas source files (reference only)
├─ source/                       # Market research and pitch deck
├─ Build Phases and Timeline.pdf # Phase and launch timeline memo
└─ Rezzy_Design_Proposal.pdf     # Early design proposal (reference only)
```

**Planned (application):**

```
├─ public/                 # icons, manifest assets
├─ src/
│  ├─ app/                 # routes: auth, onboarding, staff, guest, r/[token], p/[handle], discover, admin
│  ├─ components/          # shadcn/ui + Rezz-i brand components
│  ├─ lib/                 # Supabase client, geolocation helpers
│  ├─ hooks/
│  ├─ types/database.ts    # generated Supabase types
│  └─ styles/tokens.css    # design tokens
├─ supabase/
│  ├─ migrations/          # timestamped SQL migrations
│  └─ functions/           # checkin, qr-issue, qr-redeem, review-submit, moderate, push-send, account-delete
└─ tests/
```

### Source precedence

When documents disagree, this order wins:

1. *Rezz-i Developer Product & Build Guide* (`builddeck/`)
2. *Rezz-i notes* (`Rezz-i notes/Rezz-i.md`) — the feature outline
3. The rezz-i.com waitlist site — the design template
4. Everything else (spec v0.3, `design/`, proposal PDF, pitch deck, research) — reference only

---

## Roadmap

| Phase | Focus | Target |
|---|---|---|
| 0 | Foundation — auth, roles, 18+ gate, versioned terms, design tokens, RLS, CI | Oct 26 – Nov 8, 2026 |
| 1 | Onboarding and staff profiles | Nov 9 – Nov 22 |
| 2 | Check-in and secure QR | Nov 23 – Dec 13 |
| 3 | Rate, review, and moderation | Dec 14 – Jan 10, 2027 |
| 4 | Follow, notify, and discover | Jan 11 – Jan 31 |
| 5 | Hardening and Des Moines closed beta (Feb 8) | Feb 1 – Feb 28 |
| 🚀 | **Public Des Moines launch window** | **Mar 2 – Mar 16, 2027** |

Dates are proposed and will be adjusted as gate questions are answered. Each phase starts only when every question for that phase in `spec.md` §12 has a recorded answer.

---

## Development workflow

1. The Lovable project syncs two-way with this repo; Lovable deploys `main`.
2. Work happens on a feature branch per phase, merged to `main` by pull request.
3. Don't edit the same files in Lovable and locally at the same time — pick one editor per task.
4. All schema changes are SQL migrations in `supabase/migrations/`. Never change the schema in the dashboard without a matching migration.
5. Secrets (LLM key, VAPID private key, SMS provider) live in Supabase secrets — never in front-end code or `VITE_` variables.

Setup instructions will be added once the Phase 0 scaffold lands.

---

## Brand

Dark background with magenta and cyan neon accents, **Space Grotesk** for headlines and **Inter** for body text. The voice is confident, warm, and direct.

*Built for people, not platforms.*

---

© 2026 Rezz-i. All rights reserved.
