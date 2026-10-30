# FPT Admin — independent UI/UX review (desktop, Windows 11 · Chrome · 1440×900)

Scope: 37 production screenshots of `admin.fptraders.app`, Arabic RTL (one English control shot). Reviewed from the images alone, as a senior product designer + front-end lead would, prioritised by impact on staff doing daily work (payout approval, proof review, risk watch, support look-ups). Severity: **P0** blocks use/trust · **P1** clearly unprofessional or confusing · **P2** inconsistent/polish · **P3** nitpick.

Measurements below are read off the screenshots and are approximate (±2 px).

---

## 1. Overall verdict

1. **Level today:** a coherent, dark, component-based kit (good sidebar, good hero numbers, good badges/mono amounts) that is still a *phone layout stretched to 1440 px*: mobile top bar, 44 px pill chips in 2–3 rows, nested scroll containers, buttons piled inside table rows, key/value stacks in narrow cards. It reads as "capable internal tool", not yet "TWO Markets-grade back office".
2. **Systemic problem #1 — density and reading distance on desktop:** verbose timestamps (3-line dates), 65–140 px table rows, orphaned counts and CTAs 1,000 px away from their heading, 85 % empty width on the audit log, nested table scrollbars plus raw Windows scrollbars on every page.
3. **Systemic problem #2 — state semantics in the money workflows:** the payout stepper paints the *current* step in brand red and contradicts the "approved 1 of 2" badge; the proof-review screen offers "Approve" at the top before the proof and the expected amounts are seen, with no visible Reject; audit entries show "now" instead of absolute times.
4. **Systemic problem #3 — developer surface leaking into an operator product:** English template keys (`manual proof received`), permission dumps, `catalog_version`, `config/legal.ts`, `FPT_PLATFORM_MODE=sim`, literal back-ticks, `mock` badges, ISO country codes, plus bidi breakage of Latin fragments ("5.60 USD :New affiliate commission", "USDT)" / "(TRC20)").
5. **What to do first:** fix the DataTable/DateTime/FilterBar primitives once (they fix ~20 screens), then rebuild the three operator screens (payout detail, payment proof review, risk board) around "what the reviewer must see before acting", then purge dev-speak and bidi breaks. No identity change is needed — the palette, fonts and badge system are right; they are just not yet applied with desktop discipline.

---

## 2. Systemic findings (cross-screen)

| ID | Sev | Category | Finding | Evidence | Recommendation |
|----|-----|----------|---------|----------|----------------|
| S-01 | P1 | Layout / tech | **Three nested scroll regions and raw Windows scrollbars.** Main content scrolls, the sidebar nav scrolls separately (its 200 px footer pushes "البريد / الاحتيال / التدقيق / الموظفون / الإعدادات…" below the fold on 900 px), and tables sit in fixed-height containers with their own scrollbar. All three use Chrome's default light-grey scrollbars with arrow buttons on a #0a0a0a ground — they are the brightest objects on screen. | every screen (sidebar); accounts, risk, products, fraud, mail, orders, audit (table) | Add `color-scheme: dark` on `:root` and style `::-webkit-scrollbar` (8 px, thumb #2a2a2a) as a fallback. Remove `max-height`/`overflow:auto` from tables — the page scrolls, with a sticky header row. Make the sidebar fit 900 px: 40 px nav items, collapsible group headers, user/role moved to a top-right menu, language switch as a 32 px text toggle. |
| S-02 | P1 | Data formatting | **Timestamp format destroys table density.** "29 أكتوبر 2026، 11:49 بتوقيت دبي (GMT+4)" is repeated in every cell, wraps to 3 lines in narrow columns (accounts, payouts), breaks parentheses at line ends ("بتوقيت دبي (" / "GMT+4)") and repeats a time zone that is already in the header ("الوقت (دبي)"). Dates with 00:00 are shown with a time (review dates, mail). | accounts, payouts, breaches, fraud, mail, orders, staff, account detail, payment detail, staff detail | One `DateTime` component: `29 أكت 2026 · 11:49` (one line, `white-space:nowrap`, digits in mono), relative form (`قبل 42 دقيقة`) with absolute on hover for feeds, time zone stated once per table (header or footnote), date-only fields never show 00:00. Wrap "(GMT+4)" in `<bdi>`/`unicode-bidi: isolate`. |
| S-03 | P1 | Filters / IA | **Filter-chip sprawl.** 2–3 rows of 44 px pill chips (up to 15 chips), each row starting with its own "الكل" and no label saying what the row filters (status? phase? provider? severity?). "الكل / لا / نعم" on breaches is unreadable without a label. The search field is 570 px with a redundant "بحث" button. | accounts, breaches, fraud, mail, orders, audit, kyc, payments | Desktop FilterBar on one line: search (300 px, search-as-you-type) + labelled segmented controls ("الحالة: الكل · تقييم 16 · ممول 5") + selects for long enumerations (type, provider, staff). Keep counts. Chips become 32 px. |
| S-04 | P1 | Actions | **Primary CTA orphaned in the far-left corner.** The single primary button sits ~1,000 px left of the H1 and above everything; the coupons empty state literally says "بالزر أعلاه" while the button is in the opposite corner. Test actions, batch money runs and record approvals all share the same brand-red style. | coupons, staff, affiliates, orders, health, payment, payout | Page header = title block (right) + action group (left) on the *same* row with the header constrained to ~1,040 px; empty states repeat the CTA; only one solid-red primary per screen; test/secondary actions outlined; money-moving actions get a confirmation sheet. |
| S-05 | P1 | Hierarchy | **"Supermarket board" key/value stacks.** 12 px grey label above a value, repeated 6–12 times in narrow cards; values LTR-aligned at the left under right-aligned labels; totals not differentiated from line items. | settings, order detail, payment detail, fraud detail, fraud links, staff detail, me, health, affiliate detail, mail detail | Use the `KeyValue` row pattern (label and value on one line, value aligned to the start edge, 15 px label), group into 2–3 column grids with aligned baselines, promote the one number that matters (hero 32/40 mono), and put lists into tables. |
| S-06 | P1 | Tables | **Row actions as button piles.** Products: a mono group name + "لم تُفحص" badge + 44 px "فحص المجموعة" button in *every* row plus a 44 px "تعديل" pill (rows 120 px). Staff: 4 controls per row incl. a danger button (rows 140 px). Payouts: a 5-step stepper with 11 px two-line labels inside each row. | products, staff, payouts | One subtle row action (text link or icon) + an overflow "⋯" menu containing the rest, with "تعطيل" as a danger item; per-row "check" becomes one toolbar "فحص كل المجموعات"; stepper only on the detail page, rows show the StatusBadge + "1/2". |
| S-07 | P1 | Localisation | **English / developer strings leak into the Arabic UI.** `manual proof received` (chip, column, header), permission dump `overview · read, products · write…`, "Checks reviewed — first approval", `catalog_version`, `config/legal.ts`, `FPT_PLATFORM_MODE=sim`, literal back-ticks "`sim_*`", `traderId`, `mock`, `sim`, `AE`, `crypto`, `JSON`, `CSV`, `(fpt-50k v1)`, `fpt-rules-v1`, `terms-2026-10-29`, `mock:f8252004-…`, "KB · PNG 74". | mail, mail detail, me, products, settings, simulator, fraud detail/links, order, payment, breach, finance | Translate every template/role/permission key through the i18n layer; map ISO codes to names; keep raw identifiers only inside a collapsed "تفاصيل تقنية" block styled as code. Lint the Arabic bundle for untranslated keys. |
| S-08 | P1 | RTL / bidi | **Latin fragments re-ordered by the bidi algorithm.** "New affiliate commission: USD 5.60" renders as "5.60 USD :New affiliate commission"; "عملة رقمية (USDT) (TRC20)" wraps as "USDT)" / "(TRC20)"; "74 KB · PNG" → "KB · PNG 74"; "(GMT+4)" splits. | mail list, payouts, payment detail, account detail, payment detail | `dir="auto"` on any cell that can hold user/system text in either script; wrap tokens (`USDT · TRC20`, file meta, `(GMT+4)`) in `<bdi>` with `white-space:nowrap`; never let a parenthesis sit at a line boundary. |
| S-09 | P0/P1 | Status semantics | **Colour and iconography contradict state.** Payout stepper paints the *current* step in brand red (reads "failed") and marks step 3 "موافقة أولى" as current while the badge says "معتمد (1 من 2)" and Amir's first approval is listed — a two-person money control that shows the wrong pending step (**P0**). Breach closure uses an ⊗ icon with "تم"; amber is used for both severity and open status; "يعمل" green lists repeat 12 times with zero information. | payouts, payout detail, breaches, fraud, overview, health, simulator | One StatusBadge vocabulary: neutral grey · info blue · pending amber · success green · danger salmon; brand red never in badges, steppers or sparklines; current step = white bar + outlined number, done = green ✓, failed = salmon ✕. Fix the stepper index so it matches `approvals.length`. Collapse all-healthy service lists into one line + disclosure. |
| S-10 | P2 | Hierarchy | **Orphaned section counts.** A small grey count ("2", "0", "12", "16") is floated to the far-left end of a 1,100 px section heading row. | overview, affiliates, health, simulator, account detail, me | Inline count next to the heading as a chip or "(2)" — exactly as the affiliate detail already does ("الإحالات (2)"). |
| S-11 | P2 | Layout | **Content floating mid-column.** Grids with `align-items:center`/`min-height` push the first row 150 px below its heading or vertically centre a single row in an empty column. | payment detail ("بيانات الطلب" 150 px gap), affiliates (weekly log), account detail (breaches), staff detail (sessions), me (password/2FA fields), mail detail (sidebar) | `align-items:start` on all content grids; empty states sit directly under their heading with 24 px gap. |
| S-12 | P2 | IA / shell | **Mobile top bar carried onto desktop.** A 56 px bar with a centred page title that duplicates the H1 and a lone avatar button; it even shows the wrong title on "حسابي" ("الموظفون والأدوار"). 403 shows an empty bar; 404 drops the shell entirely and is bilingual. | every screen; me; forbidden; not-found | ≥1024 px: replace with a 48 px utility bar (breadcrumb · global search ⌘K · environment pill · user menu with role/sign-out), or remove. Make 403/404 share one template inside the shell, Arabic-only under `/ar`. |
| S-13 | P2 | Layout | **15 px layout shift between pages.** Sidebar is at x 1165 on pages with a main scrollbar and at x 1180 on short pages. | kyc/breaches/payouts/coupons vs accounts/risk/products | `scrollbar-gutter: stable` on the scroll container, or overlay scrollbars. |
| S-14 | P2 | Tables | **DataTable gaps.** Sort glyph ↑↓ on every column with no active state (except risk/payments), no row-click affordance (no hover, chevron or link styling), empty columns ("السجل" all "—", "الرمز" all "—"), redundant columns (phase text + status badge; product name + size; "تحويل يدوي · تحويل يدوي"), 200-row cap without pagination, numbers in narrow columns while names wrap. | accounts, products, mail, orders, risk, affiliates | DataTable spec: active sort = single filled arrow + bold header; rows are links with hover tint and an end chevron; drop empty/duplicate columns; `min-width` per column type (name 180, ref 150, amount 120, date 150); pagination or "load more" at 50. |
| S-15 | P2 | Data viz | **Chart junk.** KPI sparklines with a flat line + one spike + a red end dot (brand red as a data colour), a two-point "trend" on finance, and the breach chart renders axis labels at ~30 px and a 36 px data label overlapping the limit line. | overview, overview-en, finance, breach detail | Sparklines only with ≥7 points, neutral stroke, no end dot; charts use fixed CSS font sizes (12 px mono) not SVG-scaled text; data labels in a callout, never on the line. |
| S-16 | P2 | Typography | **Hero hierarchy inconsistent on detail pages.** Account detail: equity 16 px while rule limits are 24 px; breach H1 is a sentence with a timestamp (2 lines at 28 px); order detail title is the product name, not the order. Tab counts glued to labels ("السحوبات0"). Badge text at 11–12 px (brand minimum 12). | account, breach, order, payout | Apply the brand scale literally: ScreenTitle 22/28 = *what* (reference or type), meta line beneath, one dominant number 32/40 mono, tabs "السحوبات · 0", badges 12 px/22 px. |
| S-17 | P2 | Copy | **Grammar and symbols.** "0 كوبونات", "0 طلبات"; arrows "→" pointing the wrong way in RTL sentences ("محجوزة → مستحقة"); mirrored "≥" chips read as "≤ 40"; "الفئة 1 · 15 % · 15 %"; "من حسابك في فايبر" (customer voice) inside admin; "تنتهي خلال 5 ساعات"; `h` unit in "1.0 h". | coupons, deletions, affiliates, finance, fraud, affiliate detail, payments, fraud links | Arabic plural rules in the number formatter (`لا كوبونات`, `كوبون واحد`, `كوبونان`, `3 كوبونات`); use "←" or "ثم"; "40 فأعلى"; de-duplicate; admin-voice copy; "1.0 س". |
| S-18 | P2 | Trust | **Placeholder / simulation text in production surfaces.** Email template footer "النص التنظيمي: بانتظار قرار / Regulatory text: pending decision"; registered address "يُدرج لاحقاً (مسودة)"; "بيئة محاكاة — لا أموال حقيقية" in the admin sidebar and sign-in; all channels `mock`. | mail detail, settings, every screen | Gate placeholder strings behind a build check; show the environment as one pill in the utility bar ("محاكاة") instead of a footer sentence; the legal disclaimer belongs to the client app, not the staff shell. |
| S-19 | P3 | Shell | Sidebar footer is oversized: 56 px language pill, two disclaimer lines, user name with no menu/sign-out. "English" option in Latin is fine. | every screen | 32 px text toggle "EN"; user → menu; disclaimers out. |
| S-20 | P2 | Accessibility | Badge text 11–12 px; sort controls have no visible pressed state; risk percentages and "الأقرب للحد" convey nothing except by number; table rows likely not keyboard-focusable (no visible affordance); salmon-on-dark outline badges sit near 4:1. Contrast of body grey (~#9a9a9a on #0e0e0e ≈ 7:1) and amber/green badges is fine. | accounts, risk, breaches, staff | Badges ≥12 px with 3:1 border contrast; `aria-sort` + visible arrow; rows as `<a>` with focus ring; RuleMeter bars for risk; danger outline ≥ #e8836a. |

---

## 3. Per-screen findings

### 3.1 `admin-signin` — /ar/signin
**Works:** clean 410 px card, 48 px inputs, bold 15 px labels, clear title "دخول الموظفين", subtitle explains that every login needs a code, language toggle present.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P1 | Page composition | Card is top-anchored at y 80–408; ~55 % of the viewport below is empty, footer floats at y 850. Looks unfinished on a 900 px screen. | Vertically centre the brand + card group (or place at 30 % from top); keep the footer pinned. |
| P2 | Footer | "بيئة محاكاة — لا أموال حقيقية" on the staff sign-in is a client-app disclaimer; it reads as "this is not the real admin". | Remove; show environment as a pill after sign-in. |
| P2 | Form | No placeholders, no "نسيت كلمة المرور؟", no hint that an OTP step follows beyond the subtitle, no show-password toggle. | Add placeholders (`name@fiperprotraders.com`), a forgot link, and a stepper label "1 من 2 · كلمة المرور". |
| P3 | Required marks | Red asterisks use brand red for a form convention. | Use the grey "(مطلوب)" text or keep the asterisk in text colour. |
| P3 | Header | Logo + "Fiper Pro Traders / Admin" and the toggle are squeezed into the card width; the toggle pill is 44 px tall. | 32 px toggle; brand block centred above the card. |

### 3.2 `admin-overview` — /ar
**Works:** six KPI cards with the right numbers (12 clients, 23 active, revenue in mono), "يحتاج إجراءً" is the correct first section for an operator, event feed sentences are readable Arabic with amounts inline, sidebar grouping ("التجارة") and the active state (red icon + edge bar) are good.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P1 | "صحة المحرّك" column | A 360 px column listing 8 services, each "يعمل · آخر نبض الآن", consumes a third of the dashboard with zero information; service names are English (`engine`, `alerts`, `provisioner`…) while the health page names them in Arabic. | Collapse to one line "كل الخدمات تعمل · آخر نبض الآن" with a link to صحة النظام; expand only when something is down. Use the Arabic names everywhere. |
| P1 | "يحتاج إجراءً" rows | Label at the far right, count + chevron at the far left, 650 px apart; the "2" section count floats at x 425 detached from its heading. | Rows as compact cards (icon · label · count chip · chevron within 480 px) or a two-column list; count inline with the heading. |
| P2 | KPI sparklines | Flat line + single spike + red end dot on "الحسابات النشطة" and "مخالفات اليوم" (value 0). No scale, no period. | Remove until there are ≥7 real points; add "آخر 7 أيام" caption; neutral stroke. |
| P2 | Event feed | Each event takes ~125 px (sentence + time + "التفاصيل التقنية" disclosure); three near-identical events; blue receipt icon introduces a new colour. | 56 px rows: icon · sentence (one line, ellipsis) · relative time; technical details via row click; icons in neutral grey. |
| P2 | Top bar | 56 px bar duplicates "نظرة عامة"; only an avatar button in it. | See S-12. |
| P3 | Subtitle | "العملاء والحسابات والمال والمحرّك في لمحة. بتوقيت دبي." — fine, but the time zone could be a pill in the utility bar instead. | Keep or move. |

### 3.3 `admin-overview-en` — /en
**Works:** mirrors the Arabic layout correctly (sidebar left, chevrons flipped), "USD 8,970.00" with ISO prefix is acceptable in English.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P2 | KPI cards | Two-line titles ("Revenue this month", "Commands in flight") push the numbers to different baselines (y 208 / 240 / 234). | Fixed title height (2 lines reserved) or shorter labels ("Revenue (month)"); numbers on one baseline. |
| P2 | Sidebar footer | "Simulation environment — no real money" wraps to two lines; "PART OF FIPER GLOBAL" is uppercase-tracked while the Arabic version is sentence case. | Same footer treatment in both locales (ideally removed, see S-19). |
| P3 | Engine health | Service order differs from the Arabic page (risk, scheduler, gateway… vs engine, alerts, provisioner…). | Stable sort. |
| P3 | Scrollbars | Same raw light scrollbars in both sidebar and content. | S-01. |

### 3.4 `admin-accounts` — /ar/accounts
**Works:** reference in mono bold, client name + email stacked, numbers right-aligned in mono, "—" for empty values, chips carry counts, subtitle states sort order.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P1 | "التسليم" column | 3-line timestamp in the narrowest column makes every row 86 px → only 6 of 27 accounts visible. | S-02: `29 أكت 2026 · 11:49`, one line, 150 px column. Target 48 px rows → 12+ visible. |
| P1 | Table container | Table scrolls inside a fixed-height box (scrollbar at x 22–34) while the page also scrolls and the sidebar scrolls — three scrollbars on one screen. | S-01. |
| P1 | Filter rows | Two chip rows: status (8 chips) and phase ("الكل · تقييم · ممول"); "تقييم" appears in both rows with no label distinguishing status from phase; 190 px of filters before the table. | One toolbar: search · "الحالة" segmented · "المرحلة" select. |
| P2 | Columns | "المرحلة" (text "تقييم") duplicates "الحالة" (badge "تقييم"); no row affordance; all headers show ↑↓ with no active sort though the subtitle claims "الأحدث أولاً". | Merge phase into the badge; show active sort on "التسليم ↓"; rows link with hover. |
| P2 | "أقرب حدّ" | "5.3 %" in amber on every row — a number without a bar or threshold legend. | RuleMeter mini-bar (remaining) + number; amber only below 10 %, salmon below 3 %. |
| P3 | Subtitle | "(الأحدث أولاً، حتى 200)" — "up to 200" is dev-speak. | "27 حساباً" + pagination. |
| P3 | Search | 570 px field plus a separate 48 px "بحث" pill. | Search-as-you-type, 320 px, no button. |

### 3.5 `admin-kyc` — /ar/kyc
**Works:** honest empty state with icon, title "لا حالات", explanation of what appears here; subtitle explains what approve/reject does.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P2 | Subtitle | Two full sentences of help text under the H1 ("الاعتماد اليدوي يفتح السحب ويُحرِّر الحساب الممُوَّل المحجوز؛ الرفض يُبلِّغ العميل بالسبب"). | Keep the count in the subtitle; move the explanation to the empty state or an "i" popover. |
| P2 | Layout shift | No main scrollbar here, so the sidebar is 15 px further right than on list pages. | S-13. |
| P3 | Search placeholder | `name@example.com` (English) in an Arabic form. | "البريد الإلكتروني للعميل". |
| P3 | Chips | "قيد التحقق 0 · مرفوضة 0" with "الكل" — fine; chips are 44 px. | 32 px chips. |

### 3.6 `admin-risk` — /ar/risk
**Works:** this is the right table for a risk desk — reference, client, phase, % to limit, remaining today, remaining total, equity, open trades, refresh; active sort shown on "الأقرب للحد ↓"; live refresh stated ("تتحدث كل 5 ثوانٍ").

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P1 | "الأقرب للحد" | The one number that matters (29 %, 22 %, 13 %, 9 %, 0 %) is plain white mono with no bar, no colour, no threshold; the chip "قريب من الحد 0" says nobody is near, which conflicts with a 29 % row sorted first — the semantics (consumed vs remaining) are not explained. | Rename to "المستهلك من الحد" or "المتبقي قبل الحد", add the RuleMeter mini-bar in the cell, colour thresholds (≥70 % salmon, ≥40 % amber), and a legend in the header tooltip. |
| P1 | Nested scroll | Table box scrolls internally; sections below ("الانكشاف حسب الرمز", "أكبر الصفقات") are cut at y 890 and easy to miss. | S-01; put the two secondary sections in a right rail or tabs. |
| P2 | Column widths | Client names wrap ("Faisal Al-/Dosari", "Khalid Al-/Mansoori") while numeric columns have slack; "التحديث" wraps "قبل 42 / دقيقة". | `min-width` 180 px for client, nowrap on relative time. |
| P2 | Phase badge | Identical blue "تقييم" badge on every row adds noise in a risk table. | Show phase only when mixed (funded vs evaluation) or as a text column. |
| P3 | Subtitle | "21 حساباً حياً" good; add the refresh countdown. | — |

### 3.7 `admin-breaches` — /ar/breaches
**Works:** 4 rows fit without nested scroll; rule names bold; amounts mono; time-zone named once in the header "الوقت (دبي)".

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P1 | Second chip row | "الكل · لا · نعم" with no label — yes/no of what? | "الإقفال: الكل · مقفلة · غير مقفلة". |
| P2 | "الوقت" cells | Repeat "بتوقيت دبي (GMT+4)" in every row although the header already says "(دبي)". | Cell = `29 أكت 2026 · 08:06`. |
| P2 | "الحدّ USD" | "0.00" for "انتهاء المدة" and "عدم النشاط" — a limit of zero dollars for time-based rules is nonsense. | "—" for non-monetary rules, or the rule's actual parameter ("30 يوماً"). |
| P2 | "الإقفال" badge | "تم" with an ⊗ (x-circle) icon in salmon — the icon and colour say "failed/cancelled" while the word says "done". | "أُقفلت الصفقات" with ✓ in neutral grey or green. Same label as the detail page. |
| P2 | Missing column | No client name — the reviewer sees only "FPT-100K-VFBUE". | Add client (name + email) column. |
| P3 | Title | "المخالفات والأدلة" vs sidebar "المخالفات" vs top bar "المخالفات". | One name. |

### 3.8 `admin-payouts` — /ar/payouts
**Works:** the chips are exactly the queue states a reviewer needs ("معتمد (1 من 2) 1", "مدفوع 1"); trader share shown with the % in the header and the 80/20 split in a footnote; status badges have the right colours.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P0 | In-row stepper | Step 3 "موافقة أولى" is painted red (current) while the badge says "معتمد (1 من 2)" — the first approval has happened, so step 3 should be ✓ and step 4 current. Brand red on the current step reads as rejection. Labels are 11 px, two lines, "الفحوصات" collides with "موافقة أولى". | Remove the stepper from rows; show StatusBadge + "1/2 اعتماد" + who approved. Fix the step index on the detail page (S-09). |
| P1 | Date columns | Two 3-line timestamp columns ("موعد المراجعة", "وقت الطلب"); review dates show "00:00". | `31 أكت 2026` (date only) and `28 أكت 2026 · 08:11`. |
| P2 | "القناة" | "عملة رقمية (USDT) (TRC20)" wraps with broken parentheses: "USDT)" / "(TRC20)". | `<bdi>USDT · TRC20</bdi>` nowrap; or two lines "عملة رقمية" / "USDT · TRC20". |
| P2 | Missing | No "needs *my* approval" filter or row action; no requested-by-when SLA; no client KYC flag. | Default filter "بانتظار اعتمادي"; SLA column "متبقٍ 2 يوم"; approve from the row only via the detail. |
| P3 | Column header | "الطابور حسب الحالة" is a strange header for a stepper. | "الحالة" once. |

### 3.9 `admin-grant` — /ar/orders/grant
**Works:** clear purpose line ("طلب مجاني لعميل… لا يُحتسب بيعاً لأي شريك"), 48 px inputs, helper text under note and re-auth, primary button at the end of the form.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P2 | "المنتج" select | Empty with no placeholder. | "اختر المنتج…" placeholder; show size + price in the option. |
| P2 | "السبب" select | Pre-selected "فائز مسابقة" — the most consequential field defaults to a value, so wrong reasons will be recorded. | No default; required; add "أخرى" with mandatory note. |
| P2 | "بريد العميل" | Free-text email with no lookup; staff can grant to a non-existent client. | Typeahead on existing clients showing name + email. |
| P2 | Re-auth field | "كلمة المرور أو رمز المصادقة الحالي" as a plain text input. | `type=password` with a show toggle; explain the 5-minute window inline (already there — good). |
| P3 | Page balance | 670 px card hugs the right; left half empty. | Fine for RTL; optionally a side panel "ماذا يحدث بعد المنح" (provisioning, e-mail sent). |
| P3 | Back link | "الطلبات →" is a 44 px grey pill. | 32 px text link with arrow. |

### 3.10 `admin-products` — /ar/products
**Works:** tabs for products / rule versions / challenge builder / rule simulator; version pill "v1"; active badge; price and size in mono.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P1 | "مجموعة cTrader" cell | Mono group name + "لم تُفحص" badge + full 44 px "فحص المجموعة" button in every row → 120 px rows, 5 products visible, a wall of buttons. | Cell = group name + small status dot; one toolbar action "فحص كل المجموعات"; per-row check in the overflow menu. |
| P2 | "السجل" column | Every value "—". | Remove, or show "آخر تعديل" date. |
| P2 | "الاسم" | Narrow column wraps "FPT — تقييم / 10,000 دولار"; the size is repeated in the next column. | Name column 220 px; name without the size. |
| P2 | Footnote | "التعديل يُدرج catalog_version جديدة…" leaks a schema field. | "كل تعديل ينشئ نسخة جديدة من المنتج؛ الطلبات القديمة تحتفظ بنسختها." |
| P2 | "تعديل" button | 44 px pill per row. | Row click opens the editor; "تعديل" as a text link on hover. |
| P3 | "الحجم USD" | "10,000.00" — account sizes don't have cents. | Integer formatting for sizes. |

### 3.11 `admin-coupons` — /ar/coupons
**Works:** empty state with icon and instruction; chips with counts.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P1 | "كوبون جديد" | The only CTA is in the far-left corner while the empty state says "بالزر أعلاه". | Put the primary button inside the empty state (and keep a header action). |
| P3 | Subtitle | "0 كوبونات". | "لا كوبونات بعد". |

### 3.12 `admin-affiliates` — /ar/affiliates
**Works:** the subtitle explains the tier model in one line; tier list with ranges; weekly batch concept is clear.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P1 | Table overflow | 9 columns overflow horizontally at 1,125 px (horizontal scrollbar at y 447); "الفئة" cell breaks into "الفئة 1 / 15 · / %" with "%" alone on a line. | Column plan: code 130 · client 220 · tier 110 (nowrap "الفئة 1 · 15 %") · sales 90 · sub-partners 90 · held 110 · due 110 · next payout 120 · paid 110 = 1,090 px; hide "sub-partners" below 1,280 px. |
| P1 | "تشغيل الدفعة الأسبوعية" | A money-moving batch run styled exactly like "New coupon", placed in the corner with no confirmation visible. | Place in the "سجل الدفعات الأسبوعية" section header; confirmation sheet showing total, count and date before running. |
| P2 | Weekly log | "0" count orphaned at x 24; "لم تُشغَّل أي دفعة بعد." floats 250 px below the heading. | Inline count; empty state directly under the heading. |
| P2 | "نموذج العمولة" | Tier label on the right, percentage on the far left of a 540 px column — classic board layout. | Small table: tier · range · rate. |
| P3 | Arrow | "محجوزة → مستحقة" points backwards in RTL. | "محجوزة ← مستحقة" or "ثم". |

### 3.13 `admin-mail` — /ar/mail
**Works:** subject + template/locale sub-line per row; recipient in mono; status badges; template chips with counts.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P1 | English template key | `manual proof received` appears as a chip, in the sub-line and (on the detail) as a value, while other templates are Arabic ("الحساب جاهز", "عمولة شريك"). | Translate: "استلام إثبات التحويل". |
| P1 | Bidi | English subjects render as "5.60 USD :New affiliate commission". | `dir="auto"` on the subject cell. |
| P2 | "الرمز" column | "—" in every row (OTP code only exists for auth mails). | Show only when filtered to OTP templates, or merge into the subject. |
| P2 | Simulation notice | "وضع المحاكاة: لا شيء يخرج من الخادم؛ الرموز ظاهرة هنا" buried in the subtitle. | Amber banner (same component as the simulator page). |
| P2 | Dates | Verbose per row. | S-02. |
| P3 | Chip rows | Status row without counts, template row with counts. | Counts on both. |

### 3.14 `admin-fraud` — /ar/fraud
**Works:** sorted by severity, subtitle states "كشف فقط — المراجعة لا تغيّر شيئاً آلياً", linked-client count column.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P1 | Three chip rows | 15 chips, three "الكل", no labels (status / severity / type). | Toolbar: "الحالة" segmented · "الخطورة" select (40+, 70+) · "النوع" select. |
| P2 | Severity badge | "الخطورة 60" and "الخطورة 40" both amber; status "مفتوحة" also amber — three amber badges per row with different meanings. | Severity as a number with a 4-step colour scale (grey <40, amber 40–69, orange 70–89, salmon 90+); status as neutral outline. |
| P2 | "≥" chips | Mirrored glyph reads as "الخطورة ≤ 40". | "40 فأعلى". |
| P2 | Dates | Verbose; column is 260 px for a timestamp. | S-02. |
| P3 | Client cell | Name + email stacked; fine, but no link affordance. | Row link. |

### 3.15 `admin-staff` — /ar/staff
**Works:** roles explained in the subtitle; "(أنت)" marker; 2FA state badges with correct colours; footnote on owner-only rules.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P1 | "إجراءات" column | Four controls per row (text "سجل الدخول", pill "تغيير الدور", danger "تعطيل", wide pill "إعادة تعيين المصادقة الثنائية") wrapping to three lines → 140 px rows. | Row click → staff detail (which should host these actions); in the list, one "⋯" menu. |
| P2 | "الدور" | Wraps "مالك / النظام", "المكتب / الخلفي". | 140 px column, nowrap. |
| P2 | "سجل الدخول" | Looks like a label, is a link. | Link styling or move to detail. |
| P1 | "دعوة موظف" | Orphaned primary in the corner. | S-04. |
| P3 | Dates | "30 أكتوبر 2026، 00:24 بتوقيت دبي (GMT+4)" two lines. | S-02. |

### 3.16 `admin-settings` — /ar/settings
**Works:** tabs partition a big surface; the daily-reset card states the governing rule plainly ("يوم FPT هو يوم دبي").

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P1 | Entire page | Three cards of stacked 12 px label / value pairs (entity: 9 pairs; channels: 6 "mock" badges) — the purest "supermarket board" in the product; and it is read-only, so "الإعدادات" is a misnomer. | Rename "الإعداد والبيئة"; KeyValue rows (label · value on one line); group "جهات الاتصال" as a 3-column strip; channels as a 2-column status grid with one line each. |
| P1 | Dev-speak | "يُقرأ من config/legal.ts", "mock = محاكاة كاملة، live = …، اضبط FPT_*_MODE في البيئة", "@fpt/core". | Operator language: "الوضع: محاكاة" with a tooltip; move env instructions to the integrations page. |
| P2 | Placeholder | "العنوان المسجّل يُدرج لاحقاً (مسودة، 29 أكتوبر 2026)". | Hide until set; flag in integrations readiness. |
| P2 | Value alignment | LTR values (emails, "240 min") align left under right-aligned labels. | Value column aligned to the start edge; "240 دقيقة". |
| P3 | Mono use | Emails/URLs in mono although the brand reserves mono for numbers. | Inter for emails/URLs. |

### 3.17 `admin-deletions` — /ar/deletions
**Works:** clear empty state ("تظهر هنا الطلبات المرسلة من ملف العميل").

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P3 | Single chip | A filter bar with one option "الكل". | Hide the filter until there are states. |
| P3 | Subtitle | "0 طلبات". | "لا طلبات". |

### 3.18 `admin-simulator` — /ar/simulator
**Works:** amber "وضع المحاكاة" banner is the right component; target-account select shows reference + phase.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P2 | Subtitle | Literal back-ticks: "كل إجراء أمر `sim_*` يمرّره المحرّك". | Render as code or drop the identifier. |
| P2 | Section count | "12" orphaned at x 28. | Inline. |
| P2 | Health grid | 12 identical "يعمل" cards. | One summary line + disclosure (S-09). |
| P2 | Banner text | `FPT_PLATFORM_MODE=sim` for staff. | "الوضع: محاكاة — لا شيء هنا يلمس منصة حقيقية". |
| P3 | Select width | 1,125 px select for "FPT-10K-FAKPJ · تقييم". | `max-width: 480px`. |
| P3 | Helper | "traderId", "100k". | "معرّف المتداول", "100,000 USD". |

### 3.19 `admin-integrations` — /ar/integrations
**Works:** honest readiness page: contract test results ("20 من 20 ناجح"), missing-key badges, step-by-step setup. Appropriate for an owner/engineer.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P2 | Structure | Status and documentation are interleaved; each card is a 600 px wall of text; the readiness answer ("ما الناقص للانطلاق؟") needs a summary. | Top summary table: service · mode · keys present · last contract test · result; setup steps inside a collapsed "خطوات الربط". |
| P2 | Info banner | A `pnpm --filter @fpt/adapters contract:status` command in an operator banner. | Keep in the collapsed technical block. |
| P3 | "غير موجود" badges | ⊗ icon in neutral grey for required-but-missing keys. | Amber for required, grey for optional (already marked "اختياري"). |

### 3.20 `admin-health` — /ar/health
**Works:** Arabic service names (unlike overview/simulator), refresh interval stated, command-queue numbers present, alerts empty state with instruction.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P2 | "إرسال تنبيه تجريبي" | A test action in solid brand red in the corner; the alerts empty state that needs it is 700 px away. | Outlined secondary button inside the "آخر التنبيهات" header. |
| P2 | Counts | "12" and "0" orphaned. | Inline. |
| P2 | Left rail | Simulator clock / command queue / daily reset as label-value stacks; queue stats as a 3×2 grid of tiny numbers. | Compact KeyValue rows; queue as a single line "في الانتظار 0 · قيد التنفيذ 0 · منجز 3 · فشل 0". |
| P2 | Service list | 12 × "يعمل". | Summary line + only non-healthy services expanded. |
| P3 | Naming | Service names differ from overview/simulator. | One dictionary. |

### 3.21 `admin-me` — /ar/staff/me
**Works:** profile, password and authenticator are the right three concerns; 2FA badge; sessions section exists.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P1 | Column alignment | Three columns with different internal alignment: password fields start at y 385 after a 110 px gap; the 2FA "الحالية" field floats at y 565; two fields labelled "الحالية" with no context. | `align-items:start`; labels "كلمة المرور الحالية" (both); or one "التحقق" field shared by the form. |
| P1 | "الصلاحيات" | Eight lines of raw English permission keys. | "مالك النظام — كل الصلاحيات" + a grouped chip list in Arabic ("السحوبات: قراءة، اعتماد"). |
| P2 | Top bar | Says "الموظفون والأدوار" on "حسابي". | Correct title or remove the bar. |
| P2 | "16" | Sessions count orphaned at the bottom-left. | "الجلسات (16)". |
| P3 | Primary | "تغيير كلمة المرور" is a grey pill (looks secondary). | Solid primary within the form. |

### 3.22 `admin-forbidden` — /ar/forbidden
**Works:** short, clear ("دورك لا يتضمن الصلاحية… اطلبها من مالك النظام"), one exit button, stays inside the shell. Essentially fine.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P3 | Top bar | Empty title. | "لا صلاحية". |
| P3 | Consistency | Differs from 404 (which drops the shell and is bilingual). | One error template. |

### 3.23 `admin-not-found` — /ar/no-such-page
**Works:** centred, calm, brand block, one action. The vertical centring here is what sign-in should do.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P2 | Bilingual | "الصفحة غير موجودة / Page not found", button "نظرة عامة · Overview" under `/ar`. | Arabic only; English under `/en`. |
| P2 | Shell | No sidebar, unlike 403. | Keep the shell when the user is signed in. |

### 3.24 `admin-account` — /ar/accounts/{id}
**Works:** reference as title, status badge, three RuleMeters with mono amounts, tabs for trades/events/payouts/copier/data, a proper DangerZone at the bottom.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P2 | Hierarchy | Equity "10,000.00 USD" is 16 px while the three limits are 24 px; the AccountHeroCard number should dominate. | Equity 40 px mono with change vs start; limits 20 px. |
| P2 | RuleMeters | Bars are empty at 0 % progress and labelled "المتبقي" — reads as broken. | "Remaining" meters fill from full: 100 % filled when nothing is consumed, draining toward the limit; show "متبقٍ 1,000.00 من 1,000.00". |
| P2 | Tabs | "الصفقات المفتوحة0", "سجل الأحداث3", "السحوبات0" — counts glued. | "الصفقات المفتوحة · 0" with a count chip. |
| P2 | Date | "31 أكتوبر 2026، 00:00 بتوقيت دبي ( / GMT+4)" parenthesis split. | S-02 / S-08. |
| P2 | "المخالفات" | "0" orphaned; "لا مخالفات" floating mid-column. | Inline count; empty state under the heading. |
| P2 | Header | "تقييم" appears twice (badge + text); client name is not a link. | One badge; client as a link. |
| P3 | Actions | Three equal secondary pills ("إعادة حساب التعيين", "إيقاف الناسخ", "تعيين صلاحيات الوصول"). | One visible, rest in "⋯". |

### 3.25 `admin-breach` — /ar/breaches/{id}
**Works:** hero number "94,100.00 USD" at 40 px mono is exactly right; the "تحت الحدّ 94,715.00 USD · 5 % من رصيد بداية اليوم" line gives the rule and the limit; equity chart ±30 min with start and limit lines is the right evidence.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P1 | Chart typography | Y-axis labels at ~30 px (as big as the hero), a ~36 px "94,033.19" data label overlapping the line and the limit line, clipped at the viewport. | Fixed 12 px mono axis labels; chart height 280 px; data label in a small callout; limit line labelled at the right edge. |
| P2 | H1 | A full sentence with timestamp as the title, wrapping to two lines at 28 px. | H1 "الخسارة اليومية · FPT-100K-VFBUE"; meta line "Omar Al-Rashid · 29 أكت 2026 · 08:06". |
| P2 | Closure badge | "أُقفلت الصفقات" with ⊗ in salmon (list says "تم"). | Same label both places; ✓ in neutral. |
| P3 | "تنزيل JSON" | Dev-named export. | "تنزيل الأدلة". |

### 3.26 `admin-payout` — /ar/payouts/{id}
**Works:** blue "بانتظار اعتماد ثانٍ" banner says exactly what is needed; hero amount 40 px mono with the split underneath; automated checks listed with pass badges; the first approver is named.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P0 | Stepper | Step 3 "موافقة أولى" is current/red while step 4 should be — contradicts the badge "معتمد (1 من 2)" and Amir's "اعتماد" entry; red = failure. A second approver cannot trust the visual state. | Steps: ✓ مطلوب · ✓ الفحوصات · ✓ موافقة أولى (Amir, time) · ● موافقة ثانية (current, white) · ○ مدفوع. |
| P1 | Approval record | "Amir — Checks reviewed — first approval" has no timestamp and the note is English. | "Amir · 29 أكت 2026 · 10:12 · الفحوصات مراجَعة". |
| P1 | Action placement | "الاعتماد الثاني" solid red in the top-left corner, before the reviewer has seen checks, beneficiary details or the first approval; no visible "رفض". | Sticky bottom action bar (or action card next to "الاعتمادات والتنفيذ"): "اعتماد ثانٍ" (primary) · "رفض" (danger outline) · "إعادة الفحوصات" (secondary); confirmation sheet shows amount, channel, beneficiary, and "سيُنفَّذ فوراً". |
| P2 | Beneficiary | Bank/crypto destination is not visible above the fold for a bank transfer payout. | Destination card (IBAN masked / wallet, name match ✓) next to the amount. |
| P3 | Checks | ✓ icon + "ناجح" badge double-encode each check. | Icon only, badge only for warn/fail. |

### 3.27 `admin-order` — /ar/orders/{id}
**Works:** three concerns in three cards (order, payments, webhooks); amounts mono; failed badge; webhook signature state.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P1 | Failure reason | Order "فاشل", payment "فاشلة" — nowhere says why or what to do. | Reason line under the badge ("رُفضت البطاقة · 3DS failed") + next action ("العميل يمكنه إعادة المحاولة"). |
| P1 | Key/value stacks | 9 stacked pairs in a 360 px card; total not distinguished from list price. | KeyValue rows; totals block (list · discount · **total** bold 20 px mono). |
| P2 | Title | Product name as H1; no order reference. | "الطلب FPT-… · تقييم 50,000". |
| P2 | Dev-speak | "(fpt-50k v1)", "fpt-rules-v1", "terms-2026-10-29", "webhooks", "البيانات الخام". | Human labels; raw IDs in a collapsed technical block. |
| P2 | Value alignment | "280.00 USD" left-aligned under right-aligned labels. | Align to start. |

### 3.28 `admin-payment` — /ar/payments/{id} (manual proof review)
**Works:** hero amount 40 px; status badge with hourglass; "العمر 2 س"; proof image displayed on the page; reference in mono.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P1 | Review workflow | "اعتماد" (solid red) sits in the corner above the proof; no "رفض بسبب" visible; nothing compares what the client *claimed* with what the order *expects*. | A "المقارنة" block at the top: expected amount vs. amount on proof (typed by reviewer), order reference vs. reference on proof, client name vs. sender; then the proof (zoomable, full height); then a sticky action bar "اعتماد" / "رفض…" with a mandatory reason on reject. |
| P1 | Layout | 150 px empty gap between "بيانات الطلب" and its first row (grid centred against the image column). | `align-items:start`. |
| P2 | Proof viewer | Image cropped at 450 × 480, only the top half visible, no zoom/open-original, file meta renders as "KB · PNG 74". | Lightbox with zoom; meta "PNG · 74 KB" in `<bdi>`. |
| P2 | Date | "بتوقيت دبي (" / "GMT+4)" split; UTC duplicate line. | S-02. |
| P3 | Copy | "تحويل P2P من حسابك في فايبر" — client voice. | "تحويل P2P من حساب العميل في فايبر". |

### 3.29 `admin-mail-detail` — /ar/mail/{id}
**Works:** rendered HTML preview with HTML/نص tabs; the email itself has a clean table (reference, amount, status) and a single CTA.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P1 | Template footer | "النص التنظيمي: بانتظار قرار / Regulatory text: pending decision" — a placeholder inside a client email. | Block sending while placeholder strings exist; move the decision to a settings flag. |
| P1 | Template key | `manual proof received` in header and details. | Translate. |
| P2 | Details rail | Heading at x 350 with a 100 px gap before the first pair; "mock:f8252004-…" wrapped mono; "محاكاة" value. | KeyValue rows under the heading; provider ref truncated with copy button. |
| P3 | Email wordmark | "Fiper Pro Traders" text at the left in an RTL email. | Logo at the start edge. |

### 3.30 `admin-affiliate` — /ar/affiliates/{id}
**Works:** hero "323.90 USD" with next weekly payout and held/paid line; sections with inline counts "(2)", "(0)", "(6)" — the pattern the rest of the app should copy.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P2 | Tier line | "الفئة 1 · 15 % · 15 %" duplicated. | "الفئة 1 · 15 %". |
| P2 | Key/value grid | Six stacked pairs; "TSeedDemo02 · crypto" raw. | KeyValue rows; "عملة رقمية · USDT (TRC20) · TSee…". |
| P3 | Empty row | "لا إحالات ذاتية مرفوضة" with a chevron. | No chevron on empty states. |
| P3 | Referral rows | Name right, chevron 350 px left. | Compact list rows (max 480 px). |

### 3.31 `admin-fraud-detail` — /ar/fraud/{id}
**Works:** badges for severity/status; client card; linked clients listed; actions "تعليم كمُراجَعة" / "رفض".

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P1 | "الأدلة" card | A 730 × 690 px card that is empty except a collapsed "الأدلة ∨" — the evidence is the page. | Evidence open by default: device fingerprint, first/last seen, the accounts sharing it, side-by-side; raw JSON collapsed. |
| P2 | "الدولة" | "AE". | "الإمارات". |
| P2 | Badges | Severity and status both amber. | S-09. |
| P3 | Actions | Both grey pills; "رفض" (dismiss) indistinguishable from "mark reviewed". | "تجاهل الإشارة" outlined danger, "تعليم كمُراجَعة" primary. |

### 3.32 `admin-fraud-links` — /ar/fraud/links/{id}
**Works:** KPI strip (risk score, shared devices/IPs/payment fingerprints) is a good summary; hold-time stats are useful.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P2 | Contradiction | Card says "1 بصمة دفع لهذا العميل وُجدت…" then "لا شيء"; KPI says 0. | Fix the sentence template to the real count; hide the card when 0. |
| P2 | Truncation | "seed-device-shar" cut without ellipsis. | `text-overflow: ellipsis` + title, or wrap. |
| P2 | Empty cards | 360 px tall cards with two lines. | `align-items:start`, auto height. |
| P3 | Units | "1.0 h", "AE". | "1.0 س", "الإمارات". |

### 3.33 `admin-staff-detail` — /ar/staff/{id}
**Works:** login log with event type badges, Dubai + UTC times, IP fingerprint; session row with browser/OS and idle state.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P2 | Login log | 90 px per entry, label/value "بصمة IP" on the left of each. | Table: time · event · IP fingerprint · result (32–40 px rows). |
| P2 | Sessions | The single session floats at y 660, 290 px under its heading; no "إنهاء الجلسة" action. | Start-aligned list; revoke action per session. |
| P3 | Badges | "مفعّلة" alone doesn't say 2FA. | "المصادقة الثنائية مفعّلة". |
| P3 | Actions | None here, all on the list page. | Move role/disable/2FA-reset here (S-06). |

### 3.34 `admin-orders` — /ar/orders
**Works:** 7 rows visible, status badges correct, provider chips, total in mono.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P2 | Identity | No order reference column; rows differ only by time. | Add "المرجع" (mono) as the first column. |
| P2 | "الطريقة" | "تحويل يدوي · تحويل يدوي" (provider · method both say manual). | De-duplicate: "تحويل يدوي" once. |
| P2 | Product cell | "تقييم FPT — 50,000 دولار" + "50,000 USD" repeated. | One line. |
| P1 | "منح حساب" | Orphaned primary in the corner. | S-04. |
| P3 | Chips | Provider row without counts. | Counts. |

### 3.35 `admin-payments` — /ar/payments (queue)
**Works:** the smallest and best list: age column, ascending sort on "رفع الإثبات ↑", counts in chips, the subtitle tells the reviewer what to do.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P2 | Product cell | Three lines ("تقييم — FPT / 10,000 دولار / 10,000 USD"). | One line "تقييم 10,000". |
| P2 | Row affordance | No "مراجعة" action or link styling. | Row link + end chevron; optional proof thumbnail. |
| P3 | Chip order | "الكل" is last here, first elsewhere. | Consistent order. |
| P3 | "المسار" | Wraps two lines; client-voice copy. | Nowrap "P2P · فايبر". |

### 3.36 `admin-finance` — /ar/finance
**Works:** the densest and most professional page: four KPI cards, four compact tables with mono numbers and CSV exports, clear section subtitles.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P2 | Refunds card | A KPI that is "always 0 by policy", plus a footnote explaining that it is always 0. | Drop the card; put "سياسة عدم الاسترداد" in the page subtitle. |
| P2 | Sparkline | Two-point straight line with a red dot. | Remove until ≥3 months; bar chart by month. |
| P3 | "CSV" buttons | Bare "CSV" pill. | "تنزيل CSV" with download icon, text style. |
| P3 | Arrow | "محجوزة (14 يوماً) → مستحقة → مدفوعة". | "←". |

### 3.37 `admin-audit` — /ar/audit
**Works:** per-staff and per-target filters; subtitle promises before/after, reason and IP.

| Sev | Element | Issue | Recommendation |
|-----|---------|-------|----------------|
| P1 | Feed layout | 200 records as 125 px feed entries using the right 150 px of an 1,125 px column; 4.5 entries per screen; 85 % empty. | Table: time (absolute) · actor · action · target · reason · IP fingerprint; expandable row for before/after diff; 40 px rows → 18 per screen. |
| P1 | Timestamps | "الآن" for audit records. | Absolute `30 أكت 2026 · 00:24:11` always; relative only as a tooltip. |
| P2 | Filters | No date range, no action-type filter, 200 cap. | Date range picker, action select, pagination. |
| P3 | Target label | "الهدف: جلسة" in grey. | Column. |

---

## 4. Ranked top-15 fixes (biggest visible impact first)

1. **Payout stepper state and colour (P0)** — make step state follow the approvals list; current = white, done = green ✓, never brand red. One component used in list and detail. *(payouts, payout detail)*
2. **DateTime primitive (P1)** — `29 أكت 2026 · 11:49`, one line, nowrap, tz once per table, bidi-isolated. Instantly halves row height on 10 list pages.
3. **Kill nested scrolling + style scrollbars (P1)** — page-level scroll with sticky headers, `color-scheme: dark`, custom thin scrollbars, sidebar that fits 900 px (compact footer, collapsible groups).
4. **Desktop FilterBar (P1)** — one toolbar line: search + labelled segmented controls/selects; 32 px chips; "الكل" once. *(accounts, fraud, mail, orders, breaches, audit)*
5. **Row actions → overflow menu; stepper out of rows (P1)** — products, staff, payouts rows drop to 48 px.
6. **Payment proof review rebuilt around comparison (P1)** — expected vs. submitted block, zoomable proof, sticky approve/reject with reason, fix the 150 px gap.
7. **Payout detail action bar + approval record (P1)** — approve/reject at the end of the review with a confirmation sheet; who/when on every approval; beneficiary visible.
8. **Purge English/dev-speak from the Arabic UI (P1)** — template names, permissions, `catalog_version`, env variables, ISO codes, literal back-ticks; raw IDs into collapsed technical blocks.
9. **Bidi isolation (P1)** — `dir="auto"` / `<bdi>` on subjects, channel tokens, file meta, "(GMT+4)".
10. **KeyValue rows instead of stacks (P1)** — settings, order, payment, fraud, staff, me, health, mail detail; align values to the start edge; bold totals.
11. **Risk board RuleMeter cells (P1)** — remaining-before-limit bar + thresholds + legend; rename the column so "0 %" is unambiguous.
12. **Audit log as a table with absolute times (P1)** — 18 records per screen instead of 4.5.
13. **Header action slot + CTA in empty states (P1)** — one primary per page beside the title; money/batch actions get a confirmation sheet; test actions outlined.
14. **Chart fixes (P2)** — breach chart fixed 12 px axis type and callout label; remove 2-point sparklines and red end dots.
15. **Shell consistency (P2)** — utility bar instead of the mobile top bar (correct titles, user menu, environment pill), `scrollbar-gutter: stable`, one error template (403/404) inside the shell, inline section counts "(n)" everywhere.
