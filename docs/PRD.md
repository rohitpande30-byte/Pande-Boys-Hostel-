# PRD — Pande Boys Hostel Mobile App (India)

| Field | Value |
|---|---|
| Product | Pande Boys Hostel — Resident, Parent & Admin app |
| Platforms | Android (primary), iOS, web admin panel |
| Market | India (students and working professionals in boys' hostels / PGs) |
| Version | v1.0 (draft) |
| Date | 06 Nov 2026 |
| Owner | Rohit Pande |
| Status | Draft for review |

---

## 1. Summary

Pande Boys Hostel is a mobile-first app that lets a hostel run its daily work digitally: who is inside, who has gone out, who has paid, what the mess is serving, which room has a broken fan, and who came to visit. It gives **parents** a live, trustworthy view of their son's safety and dues. It gives the **owner/warden** one dashboard instead of registers, WhatsApp groups and Excel sheets.

"Tracking" here means **attendance, in/out movement, leave, visitors, payments and complaints**. It does **not** mean continuous GPS surveillance. Location is used only at check-in/check-out and SOS moments, with consent, as required by India's **Digital Personal Data Protection (DPDP) Act, 2023**.

## 2. Problem statement

| Stakeholder | Today's pain |
|---|---|
| Warden / owner | Paper attendance and gate registers; fee follow-ups on WhatsApp; no record of complaints; manual police-verification paperwork; no idea of mess headcount, so food is wasted |
| Resident (student) | Has to find the warden for every outpass; no receipt for cash payments; complaints are ignored; mess menu not known in advance |
| Parent | Doesn't know whether the son is back by curfew; learns about fee dues late; no direct line to the warden |
| Security guard | Handwritten visitor book; can't verify whether an outpass is real |

## 3. Goals and non-goals

### Goals (first 6 months after launch)
1. Replace 100% of paper registers (attendance, gate, visitor, complaint).
2. Collect ≥ 90% of monthly fees on time through UPI / online payment.
3. Parents get a notification within 60 seconds of check-in, check-out, late return or a missed roll call.
4. Close the median complaint within 48 hours.
5. Cut mess food waste by ≥ 20% using meal opt-out headcounts.

### Non-goals (v1)
- Continuous background GPS tracking of residents.
- A marketplace for searching hostels / booking rooms for outsiders (possible v3).
- Full payroll or accounting (export to Tally / Excel instead).
- Multi-tenant SaaS for other hostel owners (the architecture should allow it later).

## 4. Personas and roles

| Role | Who | Main jobs in the app |
|---|---|---|
| **Resident** | Boy aged 16–28: student, coaching aspirant, intern or working professional | Mark attendance, request outpass/leave, pay fees, raise complaints, see menu, opt out of meals, SOS |
| **Parent / Guardian** | Lives in another city or town; often uses Hindi or a regional language; mid-range Android phone | Approve leave, get in/out alerts, see dues and pay, talk to the warden |
| **Warden** | On-site manager | Roll call, approve outpasses, manage rooms, handle complaints, notices |
| **Owner / Super admin** | Hostel owner (may own several branches) | Revenue dashboard, staff, pricing, reports, settings |
| **Security guard** | At the gate; low tech literacy | Scan QR outpass, log visitors, record in/out |
| **Mess manager** | Runs the kitchen | Publish menu, see headcount, record feedback and inventory |
| **Accountant** (optional) | Part-time or remote | Reconcile payments, receipts, GST invoices, exports |
| **Maintenance staff** | Electrician, plumber, cleaner | Receive assigned tickets, update status, upload a photo when the job is done |

## 5. Competitive teardown (reverse engineering of existing hostel / PG apps)

We studied the public feature set of typical Indian hostel and PG management apps (owner-side PG management apps, college hostel ERPs, and visitor/gate-management apps) through app-store listings, demo videos, public screenshots and user reviews. This is a **feature-level teardown only**. We do not copy or decompile any code, assets or branding.

### 5.1 Feature matrix

| Module | Typical PG / owner apps | College hostel ERPs | Gate / visitor apps | **Pande Boys Hostel (planned)** |
|---|---|---|---|---|
| Tenant onboarding + KYC | ✅ basic | ✅ | ❌ | ✅ DigiLocker Aadhaar, police-verification form auto-filled |
| Room / bed allocation | ✅ | ✅ | ❌ | ✅ visual bed map, waitlist, room transfer |
| Rent / fee collection | ✅ UPI links | ✅ | ❌ | ✅ UPI autopay, partial payment, late fee rules, deposits |
| Receipts / GST invoice | ✅ | ⚠️ | ❌ | ✅ auto PDF and WhatsApp share |
| Attendance / roll call | ⚠️ manual | ✅ biometric | ❌ | ✅ geofence + QR + selfie (liveness), night roll call |
| Outpass / leave | ❌ | ✅ | ⚠️ | ✅ parent approval, QR pass, auto-expiry |
| Gate in/out log | ❌ | ⚠️ | ✅ | ✅ guard scan, late-entry alerts |
| Visitor management | ❌ | ⚠️ | ✅ | ✅ pre-approved visitors, OTP, photo |
| Parent app / alerts | ❌ | ⚠️ SMS only | ❌ | ✅ dedicated parent mode + WhatsApp |
| Mess menu + opt-out | ⚠️ | ⚠️ | ❌ | ✅ weekly menu, meal skip, rating, headcount |
| Complaints / maintenance | ✅ basic | ✅ | ❌ | ✅ SLA timers, photo proof, staff assignment |
| Notices / announcements | ✅ | ✅ | ❌ | ✅ read receipts, polls |
| Electricity sub-metering | ⚠️ some | ❌ | ❌ | ✅ per-room meter reading → added to bill |
| SOS / emergency | ❌ | ❌ | ❌ | ✅ one-tap SOS to warden + parent + nearby hospital list |
| Laundry / housekeeping | ❌ | ❌ | ❌ | ✅ schedules and tracking (P1) |
| Chat | ❌ (WhatsApp used) | ❌ | ❌ | ✅ in-app warden ↔ resident ↔ parent (P1) |
| Multi-language | ⚠️ English only | ⚠️ | ⚠️ | ✅ English, Hindi, Marathi (more can be added) |
| Offline support | ❌ | ❌ | ⚠️ | ✅ guard and attendance work offline, sync later |
| Analytics | ⚠️ revenue only | ✅ | ⚠️ | ✅ occupancy, collections, attendance, complaints, mess |

Legend: ✅ strong · ⚠️ partial/weak · ❌ absent

### 5.2 Common complaints in user reviews of existing apps (gaps we target)
1. "Parents can't see anything": most owner apps have no guardian view.
2. "Fake attendance": a friend marks attendance for an absent resident. **Our fix:** geofence + selfie liveness + random roll-call times.
3. "Payment shows pending even after paying": weak reconciliation. **Our fix:** payment-gateway webhooks + UTR matching.
4. "Too many features, confusing for the guard": **Our fix:** a separate, minimal Guard mode with big buttons and icons, usable in Hindi.
5. "Doesn't work when the internet is slow": **Our fix:** offline-first for gate and attendance.
6. "Spammy notifications": **Our fix:** per-category notification settings, quiet hours.

### 5.3 Our differentiators
- **Parent trust layer**: real-time in/out alerts, leave approval and a dues view.
- **Anti-proxy attendance**: geofence + selfie liveness + QR that rotates every 30 s.
- **India-native**: UPI autopay, DigiLocker, WhatsApp notifications, GST invoices, regional languages, works on ₹8k Android phones.

## 6. Feature requirements

Priority: **P0** = MVP launch blocker · **P1** = within 2 releases · **P2** = later.

### 6.1 Authentication and onboarding — P0
- Login with mobile number + OTP (SMS; WhatsApp OTP as fallback). Optional 4-digit app PIN / biometric unlock.
- Role is assigned by the admin. A user can hold more than one role (for example, owner + warden).
- Resident onboarding wizard:
  - Personal details, photo, blood group, medical conditions, allergies.
  - College / company, course, year, ID card upload.
  - Permanent address; local guardian.
  - Parent details (name, relation, mobile). The parent gets an invite link by SMS / WhatsApp.
  - KYC: Aadhaar through **DigiLocker** (masked; store only the last 4 digits plus a verification reference), or a PAN / passport / driving-licence upload.
  - **Police verification (tenant verification) form** auto-filled from the profile, exported as PDF in the state police format.
  - Digital rent agreement / hostel rules acceptance with e-sign (Aadhaar eSign in P1; checkbox + OTP in P0).
- Admin approves the onboarding → bed is allocated → welcome kit (rules PDF, Wi-Fi details, menu).

### 6.2 Rooms, beds and occupancy — P0
- Hierarchy: Branch → Building/Block → Floor → Room → Bed.
- Room attributes: sharing type (1/2/3/4), AC / non-AC, attached bathroom, rent, deposit, amenities.
- Visual bed map with colour states: vacant, occupied, reserved, under maintenance, notice period.
- Allocate, transfer or swap a bed with history. Waitlist for preferred room types.
- Check-out flow: notice period → room inspection checklist → damage charges → deposit refund calculation → exit certificate / no-dues.

### 6.3 Attendance and roll call — P0
- **Self check-in** during the roll-call window (for example 9:30–10:30 PM, configurable):
  - Device must be inside the hostel **geofence** (radius configurable, default 100 m).
  - **Selfie with liveness check** matched against the profile photo (on-device model; only a match score is stored).
  - Or **scan the rotating QR** shown on the warden's tablet / notice-board screen.
- **Warden roll call** mode: room-by-room list, tap to mark Present / Absent / On leave.
- Surprise roll call: the warden triggers it and residents must respond within N minutes.
- Auto states: "On approved leave" is excluded from absent; "Late" if checked in after curfew.
- Alerts: absent at roll call → push to the warden + notification to the parent (configurable delay).
- Monthly attendance calendar per resident; export to CSV/PDF.
- Mock-location / developer-mode detection; flag suspicious check-ins.

### 6.4 Outpass, leave and gate movement — P0
- Types: **Day outpass** (back the same day), **Night-out / Leave** (multi-day), **Home leave**, **Emergency**.
- Request form: from/to date-time, destination, reason, contact number while away.
- Approval chain (configurable): Resident → **Parent approval** (one tap from an SMS/WhatsApp link, no login needed) → Warden approval.
- Approved pass shows a **signed QR** (JWT with expiry) and the resident's photo.
- Guard scans it at the gate → status becomes "Out" → parent is notified. Scanned again on return → "In".
- Overdue return → escalating alerts to the resident, then the warden, then the parent.
- Curfew rules: late-entry log, monthly late-count, auto fine (optional).
- Live "Who's out right now" board for the warden.

### 6.5 Visitor management — P0
- Resident pre-registers a visitor (name, mobile, relation, expected time) → visitor gets an OTP / QR.
- Walk-in visitor: guard captures name, mobile, photo and purpose → resident gets an approve/deny push.
- Visitor time-in / time-out, allowed areas (lobby only by default), visiting-hours rules.
- Blacklist; frequent-visitor history; parents listed as permanent pre-approved visitors.

### 6.6 Fees, payments and billing — P0
- Fee heads: monthly rent, mess charges, security deposit, electricity, laundry, fines, one-time admission fee.
- Billing cycles: monthly / quarterly / semester / yearly; pro-rated first month.
- Auto-generated invoice on the 1st (configurable), GST-compliant where applicable (GSTIN, HSN/SAC).
- Payment methods: **UPI intent / UPI QR**, **UPI AutoPay (e-mandate)**, cards, net-banking through Razorpay / Cashfree / PhonePe PG; cash and bank transfer recorded by the admin with UTR.
- Partial payments, advance credit, wallet balance.
- Late-fee rules (flat or per day after a grace period); waivers with a reason and audit log.
- Reminders: 3 days before, on the due date, and every 3 days after; sent to both the resident and the parent through push + WhatsApp + SMS.
- Auto receipt PDF (shareable on WhatsApp). Ledger per resident.
- Refund of deposit on check-out with deductions itemised.
- Admin: collections dashboard, defaulters list, daily cash book, export to Excel / Tally XML.

### 6.7 Electricity and utilities — P1
- Per-room sub-meter reading entry (photo of the meter + number) each month.
- Units × rate, split equally (or by days present) among room occupants, added to the invoice.
- Wi-Fi: show credentials; per-user voucher integration (P2).

### 6.8 Mess / food management — P0 (menu, opt-out) / P1 (rest)
- Weekly menu (breakfast, lunch, snacks, dinner) with veg / Jain / egg labels; the image of special meals.
- **Meal opt-out** before a cutoff (for example, skip dinner by 4 PM) → live headcount for the kitchen.
- Auto opt-out of all meals while on approved leave.
- Rating per meal (1–5 + tags such as "cold", "salty", "less quantity") and comments; weekly report.
- Mess rebate rules for long leaves (for example, more than 3 consecutive days).
- Guest meal coupons (paid).
- Mess inventory / grocery stock and purchase log (P1).
- Mess committee polls ("Paneer or Chole on Sunday?").

### 6.9 Complaints and maintenance — P0
- Categories: electrical, plumbing, furniture, cleaning, Wi-Fi, pest control, mess, security, roommate issue, other.
- Raise with a photo/video, room auto-filled, priority.
- Status: Open → Assigned → In progress → Resolved → Closed (the resident confirms) / Reopened.
- SLA per category (for example, Wi-Fi 4 h, plumbing 24 h) with escalation to the owner on breach.
- Staff app view: my tasks, before/after photos.
- Anonymous complaints for sensitive issues (ragging, harassment) that go to the owner directly.
- Resident satisfaction rating after closure.

### 6.10 Notices, communication and community — P0 (notices) / P1 (chat)
- Notice board: text, image, PDF; targeted by branch / floor / room / individual; pin; expiry.
- Read receipts and "acknowledge" button for important notices (for example, rule changes).
- Push + WhatsApp broadcast for urgent notices.
- In-app chat: resident ↔ warden, parent ↔ warden (P1). No resident-to-resident group chat in v1 (moderation risk).
- Polls and surveys. Events calendar (festival celebrations, cricket tournament, birthdays).
- Lost & found board.

### 6.11 Safety and SOS — P0
- One-tap **SOS** (also by pressing the power button 3 times on Android, P1): sends the live location (one-time) + profile to the warden, owner and parents, and shows the nearest hospital and police station along with 112 / 108.
- Medical profile visible to the warden in emergencies.
- Fire / evacuation broadcast with a headcount ("I'm safe" button).
- Anti-ragging helpline information (UGC national helpline) displayed in-app.

### 6.12 Housekeeping and laundry — P1
- Room cleaning schedule; the cleaner marks "done" with a photo; residents rate it.
- Laundry: drop-off with item count, status (washing / ready), pickup; per-month quota or paid per kg.

### 6.13 Inventory and assets — P1
- Assets per room (bed, mattress, table, chair, almirah, fan, geyser) with condition.
- Check-in / check-out inspection uses this list; damage charges are linked.
- Common-area assets and AMC / service reminders (water purifier, generator, lift).

### 6.14 Staff management — P1
- Staff profiles, roles, shifts, attendance (QR / selfie), salary-advance notes.
- Guard shift handover log.

### 6.15 Parent / guardian mode — P0
- Separate light interface (can be the same app with the parent role, or a WhatsApp-only flow for parents who won't install the app).
- Home screen: son's status (In / Out / On leave), last check-in time, attendance %, dues, recent complaints, notices.
- Approve or reject leave and outpass requests.
- Pay fees directly.
- Call / chat with the warden.
- Multiple children supported.
- Language: Hindi / Marathi / English, with large-font option.

### 6.16 Guard mode — P0
- Three big buttons only: **Scan pass**, **Visitor entry**, **Manual in/out**.
- Works **offline**; queue syncs when online; conflicts resolved with server timestamps.
- Icons + Hindi voice prompts (P1).
- Shared tablet at the gate with a guard PIN.

### 6.17 Admin dashboard (mobile + web) — P0
- Today: occupancy, present / out / on leave / absent, pending approvals, open complaints, today's collections, mess headcount.
- Reports: occupancy trend, revenue and dues ageing, attendance, late entries, complaint SLA, mess ratings, visitor log.
- Filters by branch / date; export to PDF / Excel.
- Settings: curfew, roll-call window, geofence, fee rules, late-fee rules, approval chain, SLA, notification templates, languages.
- Multi-branch switcher for owners.
- Full **audit log** of admin actions (fee waivers, attendance edits, deletions).

### 6.18 Notifications — P0
- Channels: push (FCM / APNs), WhatsApp Business API (template messages), SMS (DLT-registered templates, a requirement in India), email for receipts.
- User-level preferences per category; quiet hours (safety alerts are exempt).

## 7. Key user flows

### 7.1 Night roll call
1. 9:30 PM: push "Roll call is open" to all residents not on leave.
2. Resident opens the app → geofence check → selfie (liveness) → "Present ✅".
3. 10:30 PM: window closes → absentees list for the warden → warden verifies physically or calls.
4. 10:45 PM: unresolved absentees → parent alert: "Your son has not marked attendance tonight. Warden: 98xxxxxx".

### 7.2 Home leave with parent approval
1. Resident requests leave for 15–18 Nov to go to Nagpur.
2. Parent gets WhatsApp: "Approve leave for Aarav, 15–18 Nov? [Approve] [Reject]".
3. Parent approves → warden approves → QR pass generated → meals auto-skipped for those dates.
4. Guard scans when he leaves → parent: "Aarav left the hostel at 6:12 PM".
5. Guard scans on return → parent: "Aarav is back at 8:40 PM, 18 Nov".
6. If he isn't back by the end time + 2 h → escalation.

### 7.3 Monthly fee
1. 1st of month: invoice generated → push + WhatsApp to the resident and parent with a UPI pay link.
2. Payment → gateway webhook → ledger updated → receipt PDF sent.
3. If unpaid by the 5th → reminders; after the 10th → late fee added; owner sees the defaulters list.

## 8. Roles and permissions (summary)

| Capability | Resident | Parent | Guard | Warden | Mess mgr | Accountant | Owner |
|---|---|---|---|---|---|---|---|
| View own profile / dues | ✅ | ✅ (child) | — | ✅ all | — | ✅ all | ✅ all |
| Mark attendance | self | — | — | ✅ all | — | — | ✅ |
| Request outpass | ✅ | — | — | — | — | — | — |
| Approve outpass | — | ✅ child | — | ✅ | — | — | ✅ |
| Scan gate / visitors | — | — | ✅ | ✅ | — | — | ✅ |
| Rooms and allocation | view | view | — | ✅ | — | — | ✅ |
| Fees: record / waive | pay | pay | — | record | — | ✅ | ✅ waive |
| Mess menu / headcount | view / opt-out | view | — | view | ✅ | — | ✅ |
| Complaints | raise | view child | — | ✅ assign | mess only | — | ✅ |
| Settings and staff | — | — | — | partial | — | — | ✅ |
| Reports | own | child | — | ✅ | mess | finance | ✅ |

## 9. Non-functional requirements

| Area | Requirement |
|---|---|
| Performance | Cold start < 2.5 s on a 3 GB RAM Android phone; API p95 < 400 ms; APK < 30 MB |
| Devices | Android 8+ (API 26+), iOS 15+; designed for 360 dp-wide screens |
| Offline | Guard mode, warden roll call, menu and notices cached; background sync with retry |
| Network | Usable on 3G / patchy 4G; image compression before upload |
| Languages | English, Hindi, Marathi at launch (i18n framework so more can be added); right fonts for Devanagari |
| Accessibility | Font scaling, contrast AA, TalkBack labels, icons with every action in Guard mode |
| Availability | 99.5% monthly; daily backups, 30-day retention, point-in-time recovery |
| Scalability | v1: 1–10 branches, up to 2,000 residents; design for 100k residents (multi-tenant) |
| Security | TLS 1.2+, encryption at rest, role-based access control (RBAC) enforced on the server, JWT with short expiry and refresh tokens, rate-limited OTP, root/jailbreak detection for guard/admin, OWASP MASVS L1 |
| Privacy (DPDP Act 2023) | Explicit consent screens (location, camera, parent sharing); purpose limitation; data minimisation (store masked Aadhaar only); right to access / correct / erase; data retention policy (delete KYC 1 year after check-out unless the law requires longer); consent for minors (under 18) given by the parent; grievance officer contact in-app; data stored in India |
| Telecom compliance | SMS through TRAI DLT-registered sender ID and templates; WhatsApp templates pre-approved |
| Payments | Never store card data (PCI handled by the gateway); RBI e-mandate rules for UPI AutoPay |
| Observability | Crash reporting (Crashlytics / Sentry), structured logs, uptime alerts, product analytics |

## 10. Suggested technical architecture

- **Mobile app:** Flutter (one codebase for Android + iOS, strong performance on low-end phones). React Native is an acceptable alternative.
- **Admin web:** Next.js (React) + Tailwind, or Flutter Web.
- **Backend:** Node.js (NestJS) or Python (FastAPI) as a REST API + WebSocket for live boards. (Supabase / Firebase is acceptable for a fast MVP.)
- **Database:** PostgreSQL (row-level security per branch / tenant); Redis for OTP, rate limits and queues.
- **Storage:** S3-compatible storage in an Indian region (AWS Mumbai `ap-south-1`) for photos and documents; signed URLs.
- **Jobs:** BullMQ / Celery for reminders, invoices, escalations.
- **Integrations:** Razorpay / Cashfree (payments, UPI AutoPay), MSG91 / Gupshup (SMS + WhatsApp), FCM, DigiLocker API, Google Maps / OSM (geofence, nearest hospital).
- **On-device ML:** Google ML Kit face detection + a liveness check; face matching on-device; store only the match score.
- **CI/CD:** GitHub Actions → Play Store internal testing / TestFlight; staging and production environments.

### 10.1 Core data model (main entities)
`Tenant(Owner)`, `Branch`, `Block`, `Floor`, `Room`, `Bed`, `User`, `Role`, `ResidentProfile`, `Guardian`, `KycDocument`, `Allocation`, `AttendanceSession`, `AttendanceRecord`, `OutpassRequest`, `Approval`, `GateEvent`, `Visitor`, `VisitorEntry`, `FeePlan`, `Invoice`, `InvoiceLine`, `Payment`, `Refund`, `LedgerEntry`, `MeterReading`, `Menu`, `MealOptOut`, `MealRating`, `Complaint`, `ComplaintEvent`, `Notice`, `NoticeAck`, `Poll`, `ChatThread`, `Message`, `Asset`, `Staff`, `Shift`, `Notification`, `ConsentRecord`, `AuditLog`.

## 11. Success metrics

| Metric | Target (6 months) |
|---|---|
| Residents onboarded in the app | 100% |
| Parents activated (app or WhatsApp) | ≥ 80% |
| Nightly roll-call completion via app | ≥ 95% |
| On-time fee collection | ≥ 90% |
| Share of payments made online | ≥ 85% |
| Median complaint resolution time | ≤ 48 h |
| Complaint SLA breach rate | ≤ 10% |
| Mess food waste (kg/day) | −20% from baseline |
| App rating (Play Store) | ≥ 4.3 |
| Crash-free sessions | ≥ 99.5% |

## 12. Release plan

| Phase | Duration | Scope |
|---|---|---|
| **0 — Discovery** | 2 weeks | Interviews with warden, 10 residents, 10 parents, guard; finalise rules; design system and wireframes |
| **1 — MVP (P0)** | 10–12 weeks | Auth/OTP, onboarding + KYC, rooms/beds, attendance (geofence + selfie + QR), outpass with parent approval, guard mode (offline), visitors, fees + UPI + receipts, mess menu + opt-out, complaints, notices, SOS, parent mode, admin dashboard, notifications |
| **2 — Pilot** | 4 weeks | One branch, bug fixing, training videos in Hindi/Marathi, measure baseline metrics |
| **3 — v1.1 (P1)** | 8 weeks | Electricity billing, chat, mess ratings/inventory/rebates, housekeeping, laundry, assets, staff, polls/events, Aadhaar eSign, Hindi voice prompts |
| **4 — v2 (P2)** | later | Multi-tenant SaaS for other hostel owners, public listing and room booking, Wi-Fi vouchers, smart-lock / biometric gate integration, AI insights (e.g., predicting defaulters) |

## 13. Risks and mitigations

| Risk | Mitigation |
|---|---|
| Residents see tracking as surveillance → low adoption | Only event-based location; clear consent; show residents exactly what parents see; no background GPS |
| Proxy attendance via friend's phone / GPS spoofing | Selfie liveness + device binding + mock-location detection + random surprise roll calls |
| Parents not installing the app | WhatsApp-first parent flow with one-tap approval links |
| Guards not comfortable with smartphones | Minimal Guard mode, icons, Hindi audio, on-site training |
| Payment reconciliation errors | Gateway webhooks + daily reconciliation job + manual UTR matching screen |
| DPDP non-compliance | Privacy-by-design, consent logs, data stored in India, appoint a grievance officer, yearly review |
| Poor connectivity | Offline-first for critical flows; SMS fallback for alerts |

## 14. Open questions
1. How many branches and residents at launch? Are minors (under 18) living in the hostel? (This changes the consent flow.)
2. Is the mess run by the hostel or outsourced?
3. Are fees GST-registered? (Residential hostel rent may be exempt under certain limits; confirm with a CA.)
4. Is there an existing biometric device at the gate that we should integrate with?
5. Which languages are required beyond English and Hindi?
6. Preferred payment gateway and its existing merchant account?
7. Should parents be able to see attendance for every day, or only receive alerts for exceptions?

---

*Appendix: the ready-to-use AI build prompt is in [`BUILD_PROMPT.md`](./BUILD_PROMPT.md).*
