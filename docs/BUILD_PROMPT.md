# Prompts for building the Pande Boys Hostel app

Copy one of these into an AI coding tool (Claude Code, Cursor, Lovable, Bolt, FlutterFlow AI, etc.).
- **Prompt A** is the full master prompt for building the whole product.
- **Prompt B** is a short version for quick UI prototypes.
- **Prompt C** lists follow-up prompts to build the app module by module (recommended; AI tools give better results in smaller steps).

Put `docs/PRD.md` in the same repository so the AI can read it.

---

## Prompt A — Master build prompt

```text
You are a senior full-stack mobile engineer and product designer. Build a production-ready
mobile app called "Pande Boys Hostel" for a boys' hostel in India. The full product
requirements are in docs/PRD.md — read it first and follow it. If anything here conflicts
with the PRD, the PRD wins. Ask me before inventing major features that are not in it.

## Users and modes (one app, role-based home screens)
1. Resident (boy aged 16–28, student or working professional)
2. Parent / Guardian (may prefer Hindi; low tech comfort)
3. Warden
4. Owner / Super admin (may run several branches)
5. Security guard (minimal "Guard mode", works offline)
6. Mess manager
7. Accountant
8. Maintenance staff
A user can have more than one role. Permissions must be enforced on the server, not only in the UI.

## Tech stack
- Mobile: Flutter 3 (Dart), Riverpod for state, go_router, Dio, Drift (SQLite) for offline cache,
  flutter_localizations + ARB files (English, Hindi, Marathi).
- Admin web panel: Next.js 14 + TypeScript + Tailwind + shadcn/ui.
- Backend: NestJS (TypeScript) REST API + WebSockets, PostgreSQL with Prisma, Redis, BullMQ jobs.
- Storage: S3-compatible bucket in AWS ap-south-1 (Mumbai), signed URLs.
- Push: Firebase Cloud Messaging. SMS + WhatsApp: MSG91 or Gupshup (DLT templates).
- Payments: Razorpay (UPI intent, UPI QR, UPI AutoPay e-mandate, cards, net banking) with webhooks.
- KYC: DigiLocker API (store only masked Aadhaar + reference id).
- Face: Google ML Kit face detection + liveness (blink / head turn); match on device; store only the score.
- Maps: Google Maps for geofence and "nearest hospital / police station".
- Monorepo: /apps/mobile, /apps/admin-web, /apps/api, /packages/shared (types, validation with zod).
- Docker Compose for local dev; GitHub Actions for lint, test, build.

## Modules to build (P0 = MVP)
P0:
1. Auth: mobile number + OTP (rate-limited), optional PIN / biometric unlock, JWT + refresh token.
2. Resident onboarding: profile, photo, ID card, college/company, medical info, parent details
   (parent gets an invite by SMS/WhatsApp), DigiLocker KYC, hostel rules acceptance (OTP-sign),
   auto-filled police verification PDF.
3. Rooms & beds: Branch > Block > Floor > Room > Bed; visual bed map with status colours;
   allocate / transfer / swap; waitlist; check-out with inspection, damages, deposit refund, no-dues PDF.
4. Attendance: roll-call window (configurable); self check-in needs geofence (default 100 m) AND
   selfie liveness OR rotating QR (changes every 30 s) on the warden's screen; warden manual roll
   call room by room; surprise roll call; detect mock location / developer mode; absentee
   alerts to warden then parent; monthly calendar + export.
5. Outpass & leave: day outpass, night-out, home leave, emergency; approval chain
   Resident -> Parent (one-tap WhatsApp/SMS link, no login) -> Warden; signed QR pass (JWT with
   expiry) with photo; guard scan on exit/entry updates In/Out and notifies parent; overdue
   escalation; curfew and late-entry log; live "who's out now" board.
6. Guard mode: 3 big buttons (Scan pass, Visitor entry, Manual in/out), icons + Hindi, works
   fully offline with a sync queue, shared gate tablet with guard PIN.
7. Visitors: pre-registered (OTP/QR) and walk-in (photo, purpose, resident approves via push);
   time in/out, visiting hours, blacklist, parents pre-approved.
8. Fees & payments: fee heads (rent, mess, deposit, electricity, fines, admission); billing cycles,
   pro-rating; auto invoices (GST-ready); Razorpay UPI/AutoPay; partial payments, advance credit;
   cash/bank entries with UTR; late-fee rules, waivers with reason + audit log; reminders to
   resident AND parent (push + WhatsApp + SMS); receipt PDF shareable on WhatsApp; ledger;
   defaulters list; Excel / Tally export; daily reconciliation job.
9. Mess: weekly menu with veg/Jain/egg tags; meal opt-out before a cutoff; auto opt-out during
   leave; live headcount for the kitchen; 1–5 rating + tags.
10. Complaints: categories, photo/video, priority, status flow (Open > Assigned > In progress >
    Resolved > Closed/Reopened), SLA per category with escalation, staff task view with
    before/after photos, anonymous complaints routed only to the owner, satisfaction rating.
11. Notices: text/image/PDF, targeted by branch/floor/room/person, pin, expiry, read receipts,
    "acknowledge" button, urgent push + WhatsApp broadcast.
12. SOS: one tap sends a one-time location + medical profile to warden, owner and parents; shows
    112 / 108 and the nearest hospital and police station; fire drill "I'm safe" headcount;
    anti-ragging helpline info.
13. Parent mode: son's live status (In / Out / On leave), last check-in, attendance %, dues + pay,
    approvals, complaints, notices, call/chat warden, multiple children, large font, Hindi/Marathi.
    Also support a WhatsApp-only parent flow for parents who do not install the app.
14. Admin dashboard (mobile + web): today's occupancy, present/out/leave/absent, pending approvals,
    open complaints, collections, mess headcount; reports with filters and PDF/Excel export;
    settings (curfew, roll-call window, geofence, fee rules, approval chain, SLAs, templates);
    multi-branch switcher; full audit log.
15. Notifications: per-category preferences, quiet hours (SOS and safety alerts exempt).

P1 (build after MVP, keep the data model ready now):
electricity sub-meter billing, in-app chat (resident<->warden, parent<->warden), mess rebates,
guest meal coupons, mess inventory, polls & events, lost & found, housekeeping, laundry, room
assets/inventory, staff shifts & attendance, Aadhaar eSign, Hindi voice prompts in Guard mode,
power-button SOS.

## Non-functional requirements
- Runs smoothly on Android 8+ phones with 3 GB RAM; cold start < 2.5 s; APK < 30 MB; iOS 15+.
- Offline-first for Guard mode, roll call, menu and notices.
- Works on slow 3G; compress images before upload.
- Accessibility: font scaling, AA contrast, TalkBack labels.
- Security: TLS, encryption at rest, server-side RBAC with row-level branch isolation,
  rate limits, root/jailbreak detection for guard/admin, OWASP MASVS L1, never store card data.
- Privacy (India DPDP Act 2023): explicit consent screens for location, camera and parent
  sharing; parental consent for residents under 18; location only at check-in/out and SOS
  (NO background GPS tracking); masked Aadhaar only; data export and delete-my-data;
  retention policy; grievance officer contact; all data stored in India; consent log table.
- Compliance: TRAI DLT SMS templates, pre-approved WhatsApp templates, RBI e-mandate rules.
- Observability: Sentry / Crashlytics, structured logs, health checks.

## Design
- Clean, friendly, trustworthy. Primary colour deep blue (#1E3A8A), accent saffron (#F59E0B),
  success green, danger red. Light and dark themes. Rounded cards, large tap targets (48 dp+).
- Font: Inter for Latin, Noto Sans Devanagari for Hindi/Marathi.
- Bottom navigation per role, e.g. Resident: Home, Attendance, Pass, Mess, More.
- Status chips everywhere (In / Out / On leave / Late / Absent).
- Empty states, loading skeletons, friendly error messages in the user's language.

## Deliverables
1. Monorepo with the structure above and a README with setup steps.
2. Prisma schema covering every entity in PRD section 10.1, with migrations and seed data
   (1 branch, 3 floors, 30 rooms, 80 residents, parents, staff, 2 months of invoices,
   attendance and complaints) so every screen has realistic data.
3. OpenAPI (Swagger) docs for every endpoint.
4. All P0 screens in the Flutter app for every role, connected to the API.
5. Admin web panel for owner/warden/accountant.
6. Background jobs: invoice generation, reminders, roll-call open/close, absentee alerts,
   overdue outpass escalation, SLA escalation, payment reconciliation.
7. Tests: unit tests for fee calculation, late fees, pro-rating, attendance rules, approval
   chain and permissions; API integration tests; at least one Flutter widget test per module.
8. .env.example with every key needed (no real secrets), and mock adapters for SMS, WhatsApp,
   Razorpay and DigiLocker so the whole app runs locally without real accounts.

## How to work
- First, reply with: (a) the folder structure, (b) the database schema, (c) the list of
  API endpoints, (d) the screen list per role, and (e) a step-by-step build plan.
  Wait for my "OK" before writing code.
- Then build one module at a time in the order of the build plan. After each module: run the
  tests, show me what was built and how to try it, and list anything unfinished.
- Write clean, typed, commented code. No placeholder "TODO" logic in P0 features.
```

---

## Prompt B — Short prompt for a quick UI prototype

```text
Design and build a clickable Flutter prototype for "Pande Boys Hostel", an Indian boys' hostel
app with role-based home screens for Resident, Parent, Warden, Guard and Owner. Include:
OTP login; resident home (status chip In/Out/On leave, today's mess menu, dues card, quick
actions); attendance check-in with geofence + selfie; outpass request with a QR pass; parent
home with son's live status, approve-leave card and Pay Now (UPI); guard mode with 3 big
buttons (Scan pass, Visitor entry, Manual in/out); warden dashboard (present/out/absent
counts, pending approvals, open complaints); complaints with photo and status timeline;
fees with invoice list and receipt; SOS button. Use mock data, English + Hindi toggle,
deep blue #1E3A8A + saffron #F59E0B, Material 3, large touch targets, works on 360 dp screens.
```

---

## Prompt C — Module-by-module follow-up prompts

Use these one at a time after Prompt A's plan is approved.

1. `Build the Prisma schema, migrations and seed data for every entity in docs/PRD.md section 10.1. Add row-level branch isolation and an AuditLog for admin actions.`
2. `Build OTP auth (rate-limited), JWT + refresh, role/permission guards on the API, and the Flutter login + role switcher.`
3. `Build resident onboarding with DigiLocker (mock adapter), parent invite via WhatsApp/SMS (mock adapter), rules acceptance with OTP, and the auto-filled police verification PDF.`
4. `Build rooms & beds: hierarchy CRUD, visual bed map, allocation/transfer/swap, waitlist, and the check-out flow with deposit refund calculation.`
5. `Build attendance: roll-call window job, geofence check, ML Kit selfie liveness, rotating QR, warden manual roll call, mock-location detection, and absentee alerts to warden then parent.`
6. `Build outpass & leave with the configurable approval chain, one-tap parent approval links, signed QR passes, guard scanning, overdue escalation and the "who's out now" board.`
7. `Build Guard mode as an offline-first module with a Drift sync queue, plus visitor management (pre-registered OTP/QR and walk-ins with resident approval).`
8. `Build fees & payments: fee plans, invoice generation job, pro-rating, Razorpay UPI/AutoPay (mock adapter + webhook handler), partial payments, late fees, waivers, receipts PDF, reminders, ledger, defaulters list and Excel export. Add unit tests for all money calculations (use integer paise, never floats).`
9. `Build mess: weekly menu, opt-out with cutoff, auto opt-out on leave, live kitchen headcount and meal ratings.`
10. `Build complaints with SLA timers, escalation job, staff task view with before/after photos, anonymous complaints and satisfaction ratings.`
11. `Build notices with targeting, read receipts and acknowledgement; and SOS with one-time location share, emergency contacts and "I'm safe" headcount.`
12. `Build parent mode (app + WhatsApp-only flow) and the owner/warden dashboard with reports and exports on mobile and web.`
13. `Add DPDP privacy features: consent screens and ConsentRecord log, data export, delete-my-data, retention job, and grievance officer page. Then run a security review against OWASP MASVS L1 and fix the issues found.`
14. `Polish: Hindi and Marathi translations for every string, accessibility pass, empty/loading/error states, performance check on a low-end Android emulator, and release builds for Play Store internal testing.`

---

## Tips for the best results
- Give the AI one module at a time (Prompt C). Long single prompts lead to half-finished code.
- Always say "use mock adapters" until you have real Razorpay / MSG91 / DigiLocker keys.
- Ask it to "run the tests and show me the output" after every module.
- Keep `docs/PRD.md` updated; tell the AI "re-read the PRD" when you change a rule.
