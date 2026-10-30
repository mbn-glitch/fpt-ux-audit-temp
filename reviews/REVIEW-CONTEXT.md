# Independent UI/UX review — context for reviewers

Product: FPT (Fiper Pro Traders) — a cTrader-only prop-trading firm owned by Fiper Global. Two apps, dark-only, Arabic-first (RTL) with English:
- Client area (phone-first, 98 % of users on phones): https://fptraders.app — sign-in/up, accounts list, account detail (equity hero number, rule meters for daily loss / max loss / profit target, equity chart, trades, rules, credentials), shop (challenge sizes), checkout (card / crypto / manual transfer with proof upload), payouts (stepped request form, list, detail, statement), certificates, notifications, profile (KYC, e-mail change, delete account), partners (affiliate dashboard), orders (purchase history + receipt), legal pages, 404.
- Admin dashboard (desktop-first, used by 3–5 staff): https://admin.fptraders.app — overview, accounts, KYC, risk, breaches, payouts (two-person approval), orders, payments review queue (manual transfer proofs), grant account, products, finance, coupons, affiliates, mail outbox, fraud, audit log, staff, settings, deletions, simulator, integrations readiness, system health, 403/404.

Brand & design system (binding, do NOT propose changing identity): FPT brand v1.0 — dark ground (#0a0a0a-ish), brand red for brand moments only (never for losses; finance semantics: loss uses a distinct negative colour, profit a positive colour), fonts IBM Plex Sans Arabic (Arabic body), Inter (Latin body), Barlow Condensed (display/titles), IBM Plex Mono for numbers/amounts only. Type scale: body 16 px, minimum 12 px, screen titles 22/28, dominant number 32/40, mono ≥ 15 px for amounts only. Touch targets ≥ 44 px. Components: AccountHeroCard, RuleMeter (shows remaining before the limit), EquityChart, StatusBadge, Stepper (icons), ListRow/KeyValue/Section/ScreenTitle, DataTable/DataTableRows, FilterBar/Chips/Search, EventFeed, DangerZone, bottom tab bar on phones.

The owner's quality bar (verbatim intent): screens must not look like "a supermarket board" — no stacks of label/value text, no piled-up details, no small secondary fonts, no generic AI-template look; every button, page, corner and letter matters; the model of a professional UI is his other product TWO Markets (editorial, calm, strong hierarchy, generous spacing, one clear primary action per screen, numbers given room, consistent components, restrained colour). He wants a comprehensive, professional critique — technical and visual — so the fixes follow from it.

What already exists: an automated audit by the build server produced 478 findings (6 P0 / 51 P1 / 158 P2 / 263 P3) covering copy, visual, accessibility, technical, brand, RTL and performance. Your job is an INDEPENDENT second opinion from the screenshots alone — do not try to reconcile with that list; find what a senior product designer and a senior front-end lead would flag when looking at these screens, prioritise by impact on a paying trader or a staff member doing daily work, and be concrete (screen → element → what is wrong → what "right" looks like).

Screenshots: real devices via BrowserStack on production, Arabic unless the file name ends with `-en`. Phone: iPhone 15 Pro Max (1290×2217, 3× — i.e. 430×739 CSS px). Desktop: Windows 11 Chrome 1440×900.

Output format (Markdown, English): 
1. Overall verdict in 5 lines (what level the UI is at today, the 3 biggest systemic problems).
2. Systemic findings (cross-screen) — each: id, severity P0–P3, category, description, evidence (screen names), recommendation.
3. Per-screen findings — for EVERY screenshot you were given, a short section: what works, what is wrong (each item: severity, element, issue, recommendation). If a screen is fine, say so explicitly.
4. A ranked top-15 list of fixes with the biggest visible impact.
Severity: P0 = blocks use/trust (broken, misleading, unreadable), P1 = clearly unprofessional or confusing, P2 = inconsistent/polish, P3 = nitpick.
