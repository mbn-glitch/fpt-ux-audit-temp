# FPT client app — independent UI/UX review (iPhone 15 Pro Max, production, 2026-10-29)

Reviewer role: senior product designer + senior front-end lead. Source: the 43 real-device screenshots in `shots/iphone-15-pro-max/` (430×739 CSS px viewport, 3×). Arabic/RTL unless the file name ends in `-en`. Measurements quoted below were taken from the 3× pixels and divided by 3. This review is deliberately not reconciled with the automated 478-finding audit.

Severity: **P0** blocks use/trust · **P1** clearly unprofessional or confusing · **P2** inconsistent/polish · **P3** nitpick.

---

## 1. Overall verdict (5 lines)

1. **Level today:** a solid *foundation* (calm dark ground, one dominant mono number per screen, pill buttons, sensible green/orange profit-loss semantics, good document "paper" pages) that is undermined by implementation defects, so the app currently reads as "almost professional, visibly unfinished" — roughly a 5.5/10 against the TWO Markets bar; the bones are right, the finish is not.
2. **Systemic problem 1 — direction/bidi is unreliable:** the account detail panel (the screen a paying trader opens most) renders LTR in Arabic on the funded and breached accounts and on the Rules/Credentials tabs; tab order flips between states; Arabic sentences come out scrambled; the English shop is the mirror-image casualty (cards crushed to the right, checkmark overlapping the price).
3. **Systemic problem 2 — chrome and hierarchy leak:** the header and bottom tab bar are translucent, so numbers and headings bleed through them on every scrolled screen; primary actions (Pay, Next, Submit, Choose) sit below the fold in the money flows while the tab bar stays on screen; red is spent on selection states, focus rings and status badges, so the one real action no longer stands out.
4. **Systemic problem 3 — meaning of numbers:** RuleMeter bars are empty when the trader is 100 % safe, the Rules tab says "0 % remaining" in green on an untouched account, Payouts shows 15,000 "available" when only 12,500 can be requested, amounts appear in three formats (`10,000  USD`, `USD 50,000`, `10,000 دولار`) and percent signs flip sides — in a prop-firm app the numbers *are* the product and they must never be ambiguous.
5. **Trust blockers to fix before anything cosmetic:** the Terms page carries a visible "DRAFT — awaiting founder approval, not a final legal text" banner while checkout asks users to accept it; the English checkout consent reads "I accept the , the and the (, effective )."; the manual-transfer screen shows the placeholder bank reference `FPT-0000-0000 (عيّنة — يحدده المالك)` to real customers.

---

## 2. Systemic findings (cross-screen)

| ID | Sev | Category | Finding | Evidence | Recommendation |
|---|---|---|---|---|---|
| **S01** | **P0** | RTL / front-end | **The account tabbed panel renders LTR inside the RTL page, and the state is unstable.** Tab strip order is RTL on `client-account` (نظرة at the right) but LTR on `client-account-trades/-rules/-credentials`, `client-account-funded` and `client-account-breached` (نظرة at the left). In the LTR state the hero label "حقوق الحساب" is left-aligned, the profit/balance lines are in LTR word order ("ربح +15,000.00 +15.00 % منذ بداية الحساب" with ربح at the far left — an Arabic reader gets the sentence backwards), the three RuleMeters are reversed (الهدف left), the Rules tab is fully left-aligned, and the Credentials intro paragraph is scrambled ("إلى الدخول سجّل cTrader بحساب cTrader ID لا .ببريدك المرتبط"). | account, account-trades, account-rules, account-credentials, account-funded, account-breached | The symptom pattern (whole subtree flips, numbers that have their own `text-align:end` stay right) is what a headless tabs primitive produces when it writes `dir="ltr"` on its root because no direction provider/`dir` prop was given (Radix Tabs defaults to LTR). Fix: pass `dir={locale==='ar'?'rtl':'ltr'}` to every primitive (Tabs, RadioGroup, Select, Slider…) or wrap the app in a DirectionProvider; never set `direction`/`dir` on containers to "fix" numbers — isolate numbers with `<bdi>`/`unicode-bidi: isolate` instead; add a Playwright RTL screenshot test per tab state. |
| **S02** | **P0** | Layout / front-end | **Shop cards are mirrored relative to the page.** Arabic: account size at the left, fee at the right (the primary value ends up at the reading *end*). English: both numbers crushed into the right half of each card, left half empty, and the selected-state checkmark is drawn **over** "280 USD". | shop, shop-en | Remove the `row-reverse`/hard-coded direction on the card; use a normal flex row with logical properties so size sits at the reading start and fee at the end in both locales; move the checkmark to the card's start edge (or a corner), never over content; render "الأكثر شعبية/Popular" as a tag pinned to the card corner. |
| **S03** | **P0** | Content / trust | **Production shows unfinished or placeholder legal/finance content.** Terms page has an amber banner "مسودة — بانتظار اعتماد المؤسس؛ ليست نصاً قانونياً نهائياً" and the body header says "(مسودة، 29-10-2026)"; checkout-en consent line reads "I accept the , the and the (, effective )."; checkout-transfer shows beneficiary reference `FPT-0000-0000 (عيّنة — يحدده المالك)` with a copy button; Arabic consent exposes the slug `terms-2026-10-29`. | legal, checkout-en, checkout, checkout-transfer | Gate publishing: legal documents cannot be "accepted" at checkout while flagged draft; fill the EN i18n keys for document names/version; block the manual-transfer method until the real account reference is configured (show "التحويل المحلي غير متاح حالياً" instead); show versions as "نسخة 29 أكتوبر 2026", never slugs. |
| **S04** | **P1** | Chrome / front-end | **Header and bottom tab bar are translucent; content bleeds through.** Chart labels, rule values, headings and even the customer name show behind the tab labels (home, account, account-breached, shop, receipt, statement…); the header shows "التعليمات" and "المتاح للسحب" behind the title on checkout-transfer and payout-channel. | nearly every scrolled screen | Give both bars an opaque surface (`#0a0a0a` at ≥ 96 % or blur + 92 % opacity) and a 1 px top hairline; add `scroll-padding`/bottom padding equal to bar height + `env(safe-area-inset-bottom)` so the last row is never under the bar. |
| **S05** | **P1** | Flow / hierarchy | **Primary action below the fold in money flows; tab bar still present.** Shop (no visible Choose/Continue), checkout (Pay not visible), payout step 1 (Next not visible), payout review (Submit not visible); the persistent 3-row summary block eats the top third of every payout step. | shop, checkout, payout-new, payout-review | In flows (checkout, payout request, proof upload) hide the tab bar, make the primary button sticky above the safe area, collapse the summary to a one-line context chip ("سحب من FPT-100K-5L78C · القابل للطلب 12,500 USD"). |
| **S06** | **P1** | Data semantics | **RuleMeter meaning is inverted/ambiguous.** Bars are empty while the caption says "المتبقي" (an untouched account shows three empty bars = looks like nothing left); Rules tab shows "المتبقي 0 %" in green on a 0-progress target and "0 %" as the hero of the daily-loss rule; the three meters mix "progress to target" with "consumption of a limit" using identical visuals. | home, home-en, account, account-funded, account-rules | One rule: the bar shows **remaining room before the limit** (full = safe), colour by proximity (positive > 50 %, amber 20–50 %, loss colour < 20 %, loss colour + "تجاوز" when breached); the dominant number is the remaining *amount*; caption = limit definition + reset ("5 % من رصيد بداية اليوم · يُعاد الضبط 00:00 بتوقيت دبي"). Target meter is the exception: fill = achieved %, label "تحقق X % من الهدف". |
| **S07** | **P1** | Copy / terminology | **One concept, four names.** Tab "الدفعات", page title "السحوبات", CTA "اطلب دفعة" (home) vs "طلب سحب" (payouts), detail "طلب السحب", statement "كشف الدفعة". Also "12 من 10 يوم تداول", "ملغى · لم يُدفع بعد", "55 مقالات" (should be "55 مقالة"), "يراجع المالك الطلب", "فتح". | home, payouts, payout-new, payout-detail, payout-statement, orders, help, legal-delete-account, notifications | Pick **سحب** for the trader-facing concept (السحوبات / طلب سحب / كشف السحب) and **دفعة** only for the firm paying out ("دُفعت"). Write a 1-page glossary and run all strings through it. |
| **S08** | **P1** | Colour | **Brand red is used for everything, so it means nothing.** Red: primary CTA (good), focus ring on inputs (reads as error), selected radio, selected card outline, active filter chip, "حساب ممول" status pill, chart endpoint, current-step bar in steppers, "افتح الإيصال" (a low-stakes action). | signin, signup, reset, payout-new, payout-details, checkout, help, shop, home, account-funded, order | Red = brand + the one primary action per screen. Focus ring: 2 px white/neutral; selection: white outline + filled check; "funded" status: positive colour (same family as profit); chart marker: white; current step: white bar; secondary actions: outlined. |
| **S09** | **P1** | RTL iconography | **Directional icons are not mirrored.** Disclosure chevrons point right (›) on landing, More, Orders; instruction arrows "→" on checkout-transfer and the date-range arrow "3 Oct → 14 Oct" on the statement point against the reading direction (the delete-account page gets it right with ←); back arrow is correct. Select chevron sits on the right with the value on the left (profile). | landing, more, orders, checkout-transfer, payout-statement, profile | Use a single `Chevron` component that flips with `[dir=rtl]` (`transform: scaleX(-1)` or `rotate`), use "←" or an en-dash for ranges in Arabic, and build the select with `padding-inline-end` for the icon. |
| **S10** | **P1** | Localisation leaks | English on Arabic screens: landing "Backed by Fiper Global — established financial infrastructure…", statement subtitle "Payout statement · Fiper Pro Traders · 3 Oct → 14 Oct 2026", native file input "no file selected / Choose File", date input "Oct 29, 2026", coupon placeholder "OPTIONAL" (EN). | landing, payout-statement, checkout-transfer, checkout-en | Translate the landing trust line; keep the statement bilingual only if the second line is deliberately English (then style it as a sub-caption with the Arabic date); replace native file/date controls with custom components (button "اختر ملفاً" + file chip; date field showing "29 أكتوبر 2026"). |
| **S11** | **P2** | Number formatting | Amounts appear as `10,000  USD` (double-width gap in mono), `USD 50,000` (sans-bold, checkout), `10,000 دولار` (orders/receipt), `50,000 USD` (sans, checkout); percentages as `15.00  %`, `% 0`, `80\n%` (EN payouts wraps the sign to the next line). | checkout, checkout-en, orders, order-receipt, account-rules, payouts-en, home | One `Money` and one `Percent` component: mono, `12,500.00 USD` (number + NBSP + code, same order in both locales), `10%` or `10 %` with NBSP only, wrapped in `<bdi>`; never "دولار" in data rows (keep it for prose). |
| **S12** | **P2** | Hierarchy / redundancy | Titles repeated (top-bar "اختر تحديك" + H1 "اختر تحديك"; help eyebrow = title), brand stated three times on auth screens (bar, card header, footer), numbers stated 2–3 times (order detail price/reference; breach report numbers in hero + KV + paragraph; payout step 1 states 80 % four times). | shop, shop-en, help, signin, signup, reset, order, breach, payout-new | When a page has an H1, the top bar shows the parent context or nothing; one brand lockup per auth screen; one place per number. |
| **S13** | **P2** | Links | Text that is a link does not look like one: "عرض الطلب والإيصال", "هل نسيت كلمة المرور؟", "تسجيل الدخول" (reset), "شروط الخدمة وسياسة الخصوصية" (signup), account ID in order detail. Three link styles coexist on sign-in (underlined white, plain grey, underlined white bold). | account*, signin, reset, signup, order | One link style (underline on, body colour) and one quiet-link style (grey, underline on hover/press only for repeated utility links). |
| **S14** | **P2** | Dead space / rhythm | 65 px empty gap under "الحساب النشط" on home (nothing renders there); orders rows have a 150 px empty middle; the product page hero floats with ~100 px above it; funded-account meta wraps with an orphaned date. | home, home-en, home-mixed, orders, shop-product, account-funded | Remove the reserved slot or fill it with the account switcher; tighten list rows; keep vertical rhythm on an 8 px grid with section gaps of 32/48 px only. |
| **S15** | **P2** | Edge states | "تنزيل CSV" enabled with 0 trades; "تعليم الكل كمقروء" shown with no unread indicator per item; completed payouts still render a 5-segment stepper; "4 من 10 حسابات نشطة" shown to a shopper with no explanation; payout not available → CTA silently becomes "فتح الحساب" with no reason. | account-trades, trades, notifications, payouts, shop, home-mixed | Disable/hide actions with nothing to act on; per-item unread dot; completed payout card = one line "دُفع 14 أكتوبر"; explain quotas and ineligibility ("السحب التالي متاح بعد 9 أيام تداول"). |
| **S16** | **P2** | Navigation | "الحساب" tab is highlighted on Shop, Product and Checkout; product page keeps the shop title and has no back button; public verify page shows the authenticated tab bar; Shop (the conversion path) lives under "المزيد". | shop, shop-product, checkout, verify, more | Highlight no tab (or "المزيد") outside the four tab roots; product page: back button + product title; public routes use the public shell; consider a "تحدٍّ جديد" entry on Home when there is no active account. |
| **S17** | **P2** | Typography details | Orphans: "%." alone on the landing subtitle line; "MB" alone (transfer); "يوماً" alone (statement); dangling "·" separators at line ends (orders, partners, statement, payouts); double spaces ("بلا تغيير  منذ"); IBM Plex Mono's dotted zeros at 40 px make hero numbers look like a terminal. | landing, checkout-transfer, payout-statement, orders, partners, payouts, account, account-en, home | Use NBSP before "%", "MB" and after "·"; build meta lines as separate inline-flex items with a `·` pseudo-separator that never ends a line; keep Plex Mono (brand) but test `font-variant-numeric: tabular-nums` and a 36 px size for 7-digit amounts. |
| **S18** | **P2** | Safe area | Tab labels sit 6 CSS px from the viewport bottom (bar = 62 px). In standalone/PWA mode or on scroll-collapsed Safari the home indicator will overlap the labels unless `env(safe-area-inset-bottom)` is applied. | all tabbed screens | `padding-bottom: calc(8px + env(safe-area-inset-bottom))` on the bar and `viewport-fit=cover`; verify on device in standalone mode. |
| **S19** | **P1** | Information architecture | No visible account switcher although a user may hold up to 10 accounts ("4 من 10 حسابات نشطة"); Home shows one "الحساب النشط" and the Account tab shows one account. | home, home-mixed, shop | Home = account list (compact AccountHeroCards: ID, status badge, equity, remaining-to-limit) with the active one expanded; Account tab = detail of the selected account with a chip switcher under the title. |
| **S20** | **P2** | Documents | Statement/receipt/certificate "paper" pages are good, but the tab bar floats over the paper, the certificate page has no visible share/download/verify actions, and the certificate page is titled "الشهادات" (plural) while showing one certificate. | payout-statement, order-receipt, certificates | Document routes: no tab bar, a top action row (share link, PDF, print, verify code), title = document name. |

---

## 3. Per-screen findings

### 3.1 `client-landing.png` — `/ar` — public landing
**What works:** strong headline, one clear red CTA with a quiet secondary, generous space, sizes list in mono with a one-line fee caption, language switch visible.
**What is wrong:**
- **P1 · "Backed by Fiper Global — established financial infrastructure with proven broker experience"** · English paragraph on an Arabic page, grey, half-centred → translate ("بدعم من Fiper Global — بنية مالية راسخة وخبرة وساطة مثبتة") and set it as a right-aligned caption under the buttons.
- **P2 · Subtitle wrap** · "…بحصة أرباح 80" breaks and "%." sits alone on the third line → NBSP between number and %, shorten to two lines ("حقّق هدف 10 % واحصل على حساب ممّول بحصة 80 % من الأرباح.").
- **P2 · "اختر ›" rows** · chevron points right in RTL; the whole row is not obviously tappable (grey chevron, no row surface) → chevron "‹", make the full row a ListRow with a subtle surface on press, or drop "اختر" and keep only the mirrored chevron.
- **P2 · Section header row** · "خمسة أحجام للحساب" (right) and the caption "رسوم لمرة واحدة. بدون اشتراك." (left) share one baseline and compete → stack the caption under the heading in grey.
- **P3 · Mono amounts** · `10,000  USD` shows a double-width gap (mono space) → render the code in the sans caption style next to the mono number (as the hero does), or use a single NBSP.
- **P3 · Language segmented control** · active "العربية" is a thin dark outline on black, barely distinguishable → filled neutral pill for the active segment.
**Fixed screen:** header → hero (headline, 2-line subtitle) → CTA + ghost button → Arabic trust line → "خمسة أحجام للحساب" with caption below → five ListRows: mono size at the reading start, fee caption under it, "‹" at the end.

### 3.2 `client-signin.png` — `/ar/signin`
**What works:** centred card, 44 px inputs, clear primary button, OTP alternative offered, one-screen fit.
**What is wrong:**
- **P1 · Email input focus ring** · brand-red 2 px ring on an empty, untouched field reads as an error in a finance app → neutral white/grey focus ring; red only for validation errors with a message.
- **P2 · Branding ×2** · top bar shows logo + "Fiper Pro Traders"; the card repeats the big logo + name + "جزء من Fiper Global" 110 px lower → keep one lockup (the card's), make the top bar language-switch only on auth screens.
- **P2 · Three link styles** · "تسجيل الدخول برمز عبر البريد بدلاً من ذلك" underlined white; "هل نسيت كلمة المرور؟" plain grey (looks disabled); "إنشاء حساب" underlined bold → one link style; "forgot password" must look tappable.
- **P2 · Password field** · no show/hide toggle, no placeholder → add the eye toggle (44 px) at the field's end.
- **P3 · Required asterisks** · coral, not the brand red nor the loss colour → use the body-grey or drop asterisks (both fields required).
**Fixed screen:** language switch → one lockup → "تسجيل الدخول" → two fields (neutral ring, eye toggle) → primary → one row of two quiet links.

### 3.3 `client-signup.png` — `/ar/signup`
**What works:** email-only start with a clear promise ("سنرسل رمزاً من 6 أرقام"), good helper copy under the field, strong CTA "إرسال الرمز".
**What is wrong:**
- **P2 · "بالمتابعة فإنك تقبل شروط الخدمة وسياسة الخصوصية."** · ~13–14 px grey with no links → 16 px, document names underlined and tappable.
- **P2 · Brand ×3** · bar + card lockup + footer "جزء من Fiper Global" → one lockup; move "جزء من Fiper Global" into the lockup only.
- **P1 · Red focus ring** · as 3.2.
- **P3 · Lower 40 % of the screen empty** · acceptable, but the footer line floats — pin it to the bottom or remove it.

### 3.4 `client-reset.png` — `/ar/reset`
**What works:** security-conscious copy ("إن وُجد حساب، نرسل رمزاً"), same component set as sign-in.
**What is wrong:**
- **P2 · "تسجيل الدخول" link** · plain grey centred text, no affordance → underline or ghost button "العودة لتسجيل الدخول".
- **P1 · Red focus ring**, **P2 · brand ×3** · as above.
- **P3 · Title vs action** · title says "إعادة تعيين كلمة المرور" but the step is "send a code" → subtitle already explains; fine, but the CTA could say "إرسال رمز إعادة التعيين".

### 3.5 `client-help.png` — `/ar/help`
**What works:** search field with the icon at the reading start, horizontally scrolling chips with a visible cut-off hint, section heading with a divider, 48 px rows.
**What is wrong:**
- **P1 · FAQ rows show two disclosure indicators** · a native `<details>` triangle "◀" at the right and a custom chevron "⌄" at the left, and the question text is **centred** between them → hide the native marker (`summary::-webkit-details-marker{display:none}; summary{list-style:none}`), right-align the question, one chevron at the end that rotates when open.
- **P2 · Eyebrow = title** · "مركز المساعدة" appears twice, 40 px apart → drop the eyebrow or make it the category ("الدعم").
- **P2 · "55 مقالات"** · Arabic counted noun for 11–99 is singular: "55 مقالة" → fix pluralisation rules (use ICU plural categories for ar).
- **P2 · Active chip in red outline** · selection ≠ brand moment → white outline or filled neutral.
- **P3 · Subtitle mentions "يُعاد ضبط يوم FPT عند 00:00 بتوقيت دبي"** · good fact, but it belongs in the "القواعد" section, not the page intro.

### 3.6 `client-home.png` — `/ar/accounts` — funded account (Khalid)
**What works:** this is the right model — label, 40 px mono equity, profit line in the positive colour, live balance, one red CTA, three meters, equity chart card; tab bar with icons + labels.
**What is wrong:**
- **P1 · 65 px empty band under "الحساب النشط"** · nothing renders there (verified by brightening the crop) → remove the slot or put the account switcher there (S19).
- **P1 · Account ID placement** · label at the right edge, ID at the left edge, 300 px apart → ID directly under/after the label as one lockup ("الحساب النشط · FPT-100K-5L78C"), or make the ID the card title.
- **P1 · "حساب ممول" badge in filled brand red** · the trader's best status looks like a warning → StatusBadge positive variant (outline, positive colour), placed next to the ID.
- **P1 · RuleMeters** · all three bars empty under "المتبقي" (S06); target column shows "—" plus a two-line grey explanation that breaks the row's alignment → funded accounts: show two meters only (daily/overall) at half width each; or show the target cell as "لا هدف" in one line.
- **P2 · Meter header chevrons** · "⌄" next to "اليومية"/"الإجمالية" promises expansion but nothing explains what expands → drop them, or make the whole meter tappable to the Rules tab.
- **P2 · Chart** · red dotted reference line at 110.0k with shaded area and no legend; red endpoint dot; the straight 2-point line looks synthetic → legend chip row ("حدّ الخسارة الإجمالية", "الهدف"), neutral white endpoint, "لا بيانات كافية" placeholder until ≥ 3 points.
- **P2 · Translucent tab bar** · "90.0k" and the dotted line show through the labels (S04).
- **P3 · "+15.00  %"** · double-width gap before the sign (S11).
- **P3 · Profit line** · "ربح +15,000.00 +15.00 % منذ بداية الحساب" packs four items on one line; split into "+15,000.00 USD (+15.00 %)" + grey "منذ بداية الحساب".
**Fixed screen:** title bar → account switcher chips → hero (ID + positive badge, label, 40 px number, profit line, live balance) → CTA → two meters with filled "remaining" bars → chart card with legend; opaque tab bar.

### 3.7 `client-home-en.png` — `/en/accounts`
**What works:** mirrors the Arabic correctly; "Request a payout" is a good label.
**What is wrong:**
- **P1 · Same empty band, red FUNDED ACCOUNT pill, empty meters** · as 3.6.
- **P2 · Currency order inconsistent on one screen** · hero "115,000.00 USD" vs "Balance USD 115,000.00" → one order (number then code).
- **P2 · Profit sentence wraps to three lines** ("Profit +15,000.00 +15.00 %", "since the account started", "Balance…") → compact two-line secondary block.
- **P2 · Tab bar translucency** · chart "100.0k" visible under the Home tab.

### 3.8 `client-home-mixed.png` — `/ar/accounts` — second funded user (PRY2Z)
**What works:** consistent with 3.6; equity curve shows the limit line labelled with values.
**What is wrong:**
- **P1 · CTA becomes "فتح الحساب" (secondary) with no explanation** · the trader cannot tell why "اطلب دفعة" disappeared (profit 1.80 %, cycle not complete?) → keep the payout CTA disabled with an inline reason ("السحب متاح بعد 7 أيام تداول" / "الحد الأدنى للربح 2 %") — a disabled button with a reason beats a button that changes meaning; "فتح الحساب" duplicates the Account tab.
- **P1 · One account only although this user has several states** (file name "mixed") → account list/switcher (S19).
- Inherits 3.6 items (empty band, red badge, empty meters, translucency).

### 3.9 `client-account.png` — `/ar/accounts/{id}` — 10K evaluation, Overview tab (RTL state)
**What works:** this is the *correct* rendering of the tabbed panel (tabs RTL with red underline, hero right-aligned, meters in order); the "قيد التقييم" blue outline badge is a good StatusBadge; the chart shows a labelled target line (11.0k).
**What is wrong:**
- **P1 · Header block alignment chaos** · account ID centred (mono bold), meta line centred, "عرض الطلب والإيصال" right-aligned plain grey (a link that does not look like one), badge right-aligned → everything right-aligned: ID as the title (22/28, mono), meta caption, badge inline after the title, "عرض الطلب والإيصال ‹" as a quiet link.
- **P1 · No way back/switch** · the page is a tab root, but there is no account switcher for a multi-account user (S19).
- **P1 · Meters** · three empty bars under "المتبقي" (S06).
- **P2 · "بلا تغيير  منذ بداية الحساب"** · double space (empty delta placeholder) → conditional rendering.
- **P2 · Tab bar translucency** · "10,000.00" chart label visible under "الرئيسية".
- **P3 · Meta "USD 10,000 · التقييم · سُلِّم في 29 أكتوبر 2026"** · fine, but the date on an evaluation delivered *today* should read "سُلِّم اليوم".

### 3.10 `client-account-trades.png` — Trades tab (empty)
**What works:** empty state with icon, bold title and one-line explanation; centred, calm.
**What is wrong:**
- **P0 · Tab strip order is reversed compared with 3.9** (نظرة now at the far left, البيانات at the right) → S01. Users who tap a tab see the tabs jump.
- **P1 · Active tab shown as a red focus rectangle, no underline** · the selection visual changed from "underline" to "box" (a `:focus-visible` ring left by the tap, with the underline gone) → `:focus-visible` only for keyboard; the underline must persist for the selected tab.
- **P2 · "تنزيل CSV" enabled with "0 صفقة"** → disabled with a tooltip, or hidden until ≥ 1 trade.
- **P3 · Empty state could carry the next step** ("افتح cTrader بالبيانات في تبويب البيانات") → one quiet link.

### 3.11 `client-account-rules.png` — Rules tab
**What works:** the three rules are there with their definitions ("5 % من رصيد بداية اليوم").
**What is wrong — this tab is broken for a trader:**
- **P0 · Whole tab left-aligned / LTR in an Arabic page** · headings, numbers and captions hug the left edge; "10 % من الرصيد الابتدائي" renders as "10 % …" with the number at the far left and "المتبقي 0 %" as "% 0 المتبقي" → S01.
- **P0 · Wrong meaning** · profit target shows "المتبقي 0 %" in green with an empty bar on an account that has made 0 progress (it should be 100 % remaining / 0 % achieved); daily loss shows "0 %" as the hero number (the limit is 5 % = 500 USD), then "المتبقي" twice on one row (left: bare label; right: "المتبقي 500.00 USD") → one RuleMeter component per rule: title, hero = remaining amount ("500.00 USD" + caption "المتبقي قبل الحد"), bar = remaining room (full), definition line, reset time.
- **P2 · Hierarchy** · rule titles 16 px bold grey, hero numbers 32 px, captions 16 px — fine once aligned; add 32 px between rules and a hairline.
- **P2 · Tab bar translucency** · "900.00 USD" and "0 %" visible under the tabs.
**Fixed screen (per rule):** `هدف الربح` · `1,000.00 USD` · caption `10 % من الرصيد الابتدائي` · bar 0 % filled labelled `تحقق 0 %` — `الخسارة اليومية القصوى` · `500.00 USD المتبقي` · bar 100 % filled (positive) · `5 % من رصيد بداية اليوم · يُعاد الضبط 00:00 بتوقيت دبي` — `الخسارة الإجمالية القصوى` · `900.00 USD المتبقي` · bar 100 % · `9 % من الرصيد الابتدائي — ثابت`.

### 3.12 `client-account-credentials.png` — Credentials tab
**What works:** mono values with 43 px copy buttons, clear labels, "يعمل بعد الربط" amber pill explains the state of "افتح cTrader".
**What is wrong:**
- **P0 · Intro paragraph is scrambled** · rendered LTR: "إلى الدخول سجّل cTrader بحساب cTrader ID لا .ببريدك المرتبط / تملك cTrader ID نفسه بالبريد أنشئه ؟بعد." → S01 (the paragraph is correct Arabic once the container is RTL).
- **P0 · Tab order reversed vs 3.9** → S01.
- **P2 · Values left-aligned** · "demo01@fpt.example", "5306747", "FPT-Sim-Live" start at the left edge with the copy button to their right; in RTL the label is at the right and the value at the left, so the eye zig-zags → keep values at the reading start (right), copy button at the end (left), one ListRow per credential with a hairline.
- **P2 · "cTrader ID (البريد)"** · label mixes Latin and a parenthetical; the value is an e-mail → "البريد المرتبط بـ cTrader ID".
- **P2 · Bottom row** · "افتح cTrader" is a plain text button next to the amber pill; the real actions ("cTrader Web", app links) are below the fold and ghost through the tab bar → a proper button row: primary "افتح cTrader Web", secondary "التطبيق", with the pill as a status line above.

### 3.13 `client-trades.png` — `/ar/accounts/{id}/trades` (standalone, empty)
**What works:** back arrow correct (right, pointing right), clean empty state.
**What is wrong:**
- **P2 · Account ID left-aligned** at the top of an RTL page (centred on the account page, left here) → one ScreenTitle treatment.
- **P2 · Toolbar mirrored vs the Trades tab** · here CSV button left / count right; on the tab it was CSV right / count left → one FilterBar component.
- **P2 · "تنزيل CSV" enabled with 0 trades** → disable.
- **P3 · 70 % of the screen empty** · fine for an empty state; when data exists this page needs the stats strip (win rate, avg R, best/worst day) above the table.

### 3.14 `client-account-funded.png` — funded account detail (LTR state)
**What works:** the content is right (profit line, live balance, payout CTA, 80/20 share in the meta).
**What is wrong:**
- **P0 · Panel rendered LTR** · tabs reversed (نظرة at the left), "حقوق الحساب" label at the left while the number is at the right, profit line in LTR word order ("ربح" at the far left, "منذ بداية الحساب" in the middle), balance line LTR ("الرصيد" at the far left), meters reversed (الهدف at the left) and left-aligned inside each column → S01. For the paying trader this is the most damaging defect in the app.
- **P1 · Account ID left-aligned, meta line wraps with an orphaned "في 3 أكتوبر 2026"** → ID as title; meta as two short captions ("100,000 USD · حساب ممول", "حصة المتداول 80 % · سُلِّم 3 أكتوبر 2026").
- **P1 · Red filled "حساب ممول"** → positive StatusBadge (S08).
- **P1 · "عرض الطلب والإيصال"** plain grey → quiet link (S13).
- **P2 · Translucent tab bar** · "منحنى الرصيد" and "USD · آخر 27 يوماً" visible behind the tabs.
- **P2 · "منذ بداية الحساب" + "اطلب دفعة"** → terminology (S07): "اطلب سحباً".

### 3.15 `client-account-en.png` — `/en/accounts/{id}` 10K evaluation
**What works:** this is what the Arabic should mirror: ID as title, left-aligned meta, blue "In evaluation" badge, tabs with underline, hero, meters, chart.
**What is wrong:**
- **P2 · "View the order and receipt"** · plain grey text → link style.
- **P2 · "USD 10,000 · Evaluation · Delivered on 29 Oct 2026"** vs hero "10,000.00 USD" → one currency order.
- **P2 · Empty meters under "remaining"** → S06.
- **P2 · Tab bar translucency** · "10,000.00" ghosts under Payouts/More, "10.0k" under Home.
- **P3 · "Unchanged  since the account started"** · double space.

### 3.16 `client-account-breached.png` — breached 100K account
**What works:** breach badge and banner in the loss colour (orange), distinct from brand red — correct semantics; the banner has a clear action "اقرأ تقرير الإيقاف"; loss line in the loss colour.
**What is wrong:**
- **P0 · Panel rendered LTR** (tabs reversed, label left, loss line "خسارة -5,900.00 -5.90 % منذ بداية الحساب" in LTR order, balance line LTR) → S01.
- **P1 · Banner copy does not say which rule** · "تم تجاوز حدّ خسارة. أُقفلت الصفقات وتوقّف التداول." → "تجاوزت حدّ الخسارة اليومية (5 %) في 29 أكتوبر 08:06 — أُغلقت الصفقات وتوقف التداول." (the data exists on the breach page).
- **P2 · Banner layout** · text right-aligned, button centred → button at the reading start under the text, or at the end of the text row.
- **P2 · Red "ابدأ تحدياً جديداً" directly under the loss** · commercially fine but tonally harsh; place it after the breach summary with a secondary "اقرأ التقرير" first; keep red for one CTA only.
- **P2 · Tab bar translucency** · the entire meter row ("الهدف 10,000.00 USD", "اليومية 0.00 USD", "الإجمالية 3,100.00 USD") shows through the tab bar — the worst instance.
- **P3 · Account ID centred but slightly off-centre** (left of centre by ~20 px) → right-align as title.

### 3.17 `client-breach.png` — `/ar/accounts/{id}/breach` — "لماذا أوقف حسابي"
**What works:** the content model is excellent — a plain-language title with time and rule, the gap amount as the hero (−615.00 USD), equity vs limit side by side, the exact timestamp with timezone, and a sentence that reconstructs the maths. This is the kind of transparency a prop firm should be proud of.
**What is wrong:**
- **P2 · Numbers stated three times** (hero, KV pair, paragraph) and "دولار" in the paragraph vs "USD" everywhere else → keep hero + two KV cells + timestamp; make the paragraph the lead sentence under the title; use "USD".
- **P2 · "المسافة التي تجاوزتها" label** · unusual phrasing → "تجاوزت الحد بمقدار".
- **P2 · Tab bar translucency** · "حركة الحساب حول المخالفة (±30 دقيقة)" ghosts under the tabs — and that section (the trades around the breach) is the most useful part; make sure it is reachable with an opaque bar.
- **P3 · Meta line wraps "عند 00:00 بتوقيت دبي)" onto its own line** → shorten: "الحدّ اليومي: 5 % من حقوق الحساب عند 00:00 (دبي)".
- **P3 · No next step** visible (e.g. "ابدأ تحدياً جديداً" / "تواصل مع الدعم") → add a quiet action row at the end.

### 3.18 `client-shop.png` — `/ar/shop`
**What works:** five cards, big mono sizes, fee captions, one-time-fee promise, "الأكثر شعبية" is indicated.
**What is wrong:**
- **P0 · Card internals mirrored** · size at the left (reading end), fee at the right (reading start); the selected check floats mid-card → S02: size + "حجم الحساب" at the right, fee + "الرسوم · دفعة واحدة" at the left, check at the right edge, "الأكثر شعبية" as a corner tag.
- **P1 · No visible primary action** · tapping a card selects it (check) but "المتابعة" is off-screen → sticky "المتابعة — 280 USD" button above the safe area; or make each card navigate directly (then remove the radio semantics).
- **P2 · Top bar title = H1** ("اختر تحديك" twice) → bar shows nothing or "المتجر".
- **P2 · Selection in brand red** (outline + check) → white outline, filled check.
- **P2 · "4 من 10 حسابات نشطة"** · unexplained quota → "يمكنك فتح 6 حسابات إضافية" as a caption, or remove.
- **P2 · "الحساب" tab highlighted** on the shop → S16.
- **P2 · Tab bar translucency** · "القواعد الأساسية"/"كل القواعد" ghost under the tabs.

### 3.19 `client-shop-en.png` — `/en/shop`
**What works:** copy ("One-time fee. No subscription.").
**What is wrong:**
- **P0 · Cards visibly broken** · both numbers crammed into the right half, left half empty; the selected check overlaps "280 USD" → S02.
- **P2 · "Choose your challenge" (bar) vs "Choose Your Challenge" (H1)** · inconsistent casing and duplicate → sentence case, one title.
- **P2 · "Popular" as plain text after "Account Size"** → corner tag.
- **P2 · Tab bar translucency** · "The core rules" / "All the rules" ghost under the tabs; "Account" tab highlighted.

### 3.20 `client-shop-product.png` — `/ar/shop/fpt-50k`
**What works:** hero size number, rules list with the value at the end of each row and a definition line, clear CTA label "المتابعة إلى الدفع".
**What is wrong:**
- **P1 · No back button and the bar still says "اختر تحديك"** → back arrow + title "تقييم 50,000 USD".
- **P2 · Fee is a 16 px KV cell** ("280 USD") while the decision is price-driven → show the fee next to the CTA ("المتابعة إلى الدفع · 280 USD") and as a 22 px mono KV.
- **P2 · Hero subtitle repeats the number** ("تقييم 50,000 USD" under "50,000 USD") → "حساب تقييم · cTrader".
- **P2 · KV grid "الاسترداد / إعادة الضبط: غير متاح"** · negative information phrased as a feature cell → fold into a caption under the fee ("الرسوم غير قابلة للاسترداد").
- **P2 · Rule values "10  %", "5  %", "9  %"** · double-width gap, value and definition both state the percent → value cell "10 %", definition without the number ("من الرصيد الابتدائي، على الرصيد المُحقَّق").
- **P2 · "الحساب" tab highlighted; tab bar translucency** ("الحد الزمني", "30 يوماً" ghost).
- **P3 · ~100 px empty above the hero** → tighten to 32 px.

### 3.21 `client-checkout.png` — `/ar/checkout/{id}`
**What works:** back arrow, icon stepper numbered right-to-left, total as the hero, coupon with an inline apply, payment methods as a radio list with provider captions.
**What is wrong:**
- **P1 · Pay button not visible**; tab bar present during checkout → sticky "ادفع 280.00 USD" above the safe area, tab bar hidden (S05).
- **P1 · Consent line exposes "terms-2026-10-29"** and ghosts under the tab bar → "أقبل شروط الخدمة والإفصاح عن المخاطر وسياسة عدم الاسترداد (نسخة 29 أكتوبر 2026)" with the three names as links; checkbox 24 px with a 44 px hit area.
- **P2 · "الحجم 50,000 USD" in sans-bold next to "الرسوم 280.00 USD" in mono** → both mono (S11); and the fee KV duplicates the total — show "الحجم" + "طريقة الدفع" only, or add a real breakdown (fee, discount, total).
- **P2 · Stepper has no labels** (what is step 3 = hourglass?) → caption under the stepper: "الخطوة 2 من 4 · الدفع"; current step bar white, not red.
- **P2 · Selected radio in brand red** → S08.
- **P2 · "الحساب" tab highlighted** → S16.
- **P3 · Coupon placeholder "اختياري"** is fine; the EN one is "OPTIONAL" in mono uppercase (text-transform leaking into the placeholder) → `::placeholder{text-transform:none}`.

### 3.22 `client-checkout-en.png` — `/en/checkout/{id}`
**What works:** structure identical to Arabic; "Request"-style verbs.
**What is wrong:**
- **P0 · Consent sentence has empty link names and version**: "I accept the , the and the (, effective )." → S03.
- **P1 · Pay button off-screen; tab bar on** → S05.
- **P2 · "USD 50,000" sans vs "USD 280.00" mono; hero "280.00 USD"** → S11.
- **P2 · "OPTIONAL" placeholder** uppercase mono → as above.
- **P2 · Red radio, red step bar** → S08.

### 3.23 `client-checkout-transfer.png` — `/ar/checkout/{id}/transfer`
**What works:** the instruction sentence is concrete; copy buttons next to each value; file type/size limits stated.
**What is wrong:**
- **P0 · Placeholder beneficiary reference "FPT-0000-0000 (عيّنة — يحدده المالك)"** shown to customers with a copy button → S03: hide the method until configured.
- **P1 · Native file input in English** ("no file selected Choose File", LTR inside an RTL form) → custom upload control: dashed drop area with "اختر صورة الإيصال أو ملف PDF" + button "اختر ملفاً", then a file chip with name/size and remove.
- **P1 · Header translucency** · "التعليمات" and "بسرعة." ghost through the top bar (S04).
- **P2 · Instruction arrows "→"** point against reading direction → "←" or "ثم".
- **P2 · Helper text duplicated** ("JPEG أو PNG أو WebP أو PDF، حتى 5 MB" beside the heading with "MB" orphaned, then again under the input) → once, under the control, with NBSP before "MB".
- **P2 · Date input shows "Oct 29, 2026" in mono English** → custom date field "29 أكتوبر 2026" (or `lang="ar"` on the input and accept the native picker).
- **P2 · Section heading "رفع إثبات التحويل" wraps to two lines** because the helper shares the row → heading alone, helper below.
- **P2 · Tab bar present; "ملاحظة (اختياري)" ghosts; submit not visible** → S05.

### 3.24 `client-payouts.png` — `/ar/payouts`
**What works:** hero amount, cycle status line, one CTA, previous requests as cards with a positive "مدفوع" badge.
**What is wrong:**
- **P1 · Hero contradicts the rule under it** · "المتاح للسحب 15,000.00 USD" then "الحدّ الأقصى لكل طلب 12,500.00 USD" → hero = the amount the trader can actually request now (12,500.00) with caption "من أصل 15,000.00 ربح متاح · الحد الأقصى لكل طلب 12,500"; or two numbers side by side with the requestable one dominant.
- **P1 · Terminology** · bar "الدفعات", `<title>` "السحوبات", CTA "طلب سحب" → S07.
- **P2 · "الدورة مكتملة · 12 من 10 يوم تداول"** → "اكتملت الدورة · 12 يوم تداول (المطلوب 10)".
- **P2 · Meta line wraps with a dangling "·"** and "حصة الشركة 20 %" alone → two captions or inline-flex items (S17).
- **P2 · Completed payout card shows a 5-segment stepper of green ticks** (unlabelled) → one line "دُفع 14 أكتوبر 2026 · USDT" and the badge; keep the stepper for in-progress requests only, with labels.
- **P3 · Count "1" next to "الطلبات السابقة"** · tiny grey at the far left → "طلب واحد" caption or drop.

### 3.25 `client-payouts-en.png` — `/en/payouts`
**What works:** as 3.24.
**What is wrong:**
- **P1 · Same hero/limit contradiction** → 3.24.
- **P2 · "Trader share 80 / % · Firm share 20 %"** · the percent sign wraps to the next line → NBSP (S11).
- **P2 · "USD 3,200.00" in the card vs "15,000.00 USD" hero** → one order.
- **P2 · "12 of 10 trading days"** → "12 trading days (10 required)".
- **P2 · Completed stepper** → as 3.24.

### 3.26 `client-payout-new.png` — `/ar/payouts/new` — step 1 Amount
**What works:** step heading "المبلغ", helper copy, live "تتسلم" preview with a 40 px number.
**What is wrong:**
- **P1 · "التالي" not visible** and the tab bar is on → sticky next button, hide tabs (S05).
- **P2 · Top summary = label/value stack** ("المتاح للسحب" label with the ID far left; "الربح المتاح", "الحدّ الأقصى لكل طلب", "أيام التداول المموَّلة 12 / 10") consuming 240 px on every step → collapse to a one-line chip (S05).
- **P2 · 80 % stated four times** (helper, label "(دولار)", field helper, preview caption) → once in the preview caption.
- **P2 · "12 / 10"** → "12 يوماً (المطلوب 10) ✓".
- **P2 · Field label "الربح المطلوب سحبه (دولار)" + "USD" prefix inside the field** → label "المبلغ المطلوب" and the USD affix only.
- **P2 · Red focus ring on the amount field** → neutral (S08).
- **P2 · Stepper icons** (banknote / wallet / bank / card) do not map to Amount / Channel / Details / Review; the "card" for Review is misleading → use check-list icon for review and add "الخطوة 1 من 4 · المبلغ".
- **P3 · "تتسلم 0.00 USD"** before any input → show "—" until the user types.

### 3.27 `client-payout-channel.png` — step 2 Channel
**What works:** three channels with icons; "يُنفَّذ يدوياً" notes; both buttons visible (متابعة / رجوع).
**What is wrong:**
- **P1 · Header translucency** · "المتاح للسحب" and "FPT-100K-5L78C" render through the top bar (S04).
- **P2 · Channels lack the information needed to choose** (ETA, network fee, minimum) → second line per option: "خلال 24 ساعة · رسوم الشبكة ~1 USDT", "1–3 أيام عمل · يدوي".
- **P2 · Red selected radio** → S08.
- **P2 · Persistent summary block** → S05.

### 3.28 `client-payout-details.png` — step 3 Details
**What works:** single required field, disabled "متابعة" until valid, 34-character hint.
**What is wrong:**
- **P2 · Helper text scrambled by bidi** · intended "يبدأ بـ T، 34 حرفاً." renders "يبدأ بـ 34 ،T حرفاً." → "يبدأ بالحرف T ويتكوّن من 34 حرفاً." with `<bdi>T</bdi>` (avoid punctuation between a Latin letter and a number).
- **P2 · Red focus ring on the address field** → neutral; show the loss colour only on validation failure with "العنوان يجب أن يبدأ بـ T".
- **P3 · No paste / scan affordance** for a crypto address on a phone → "لصق" button inside the field and a QR scan icon.
- **P2 · Summary block / translucency** → S05/S04.

### 3.29 `client-payout-review.png` — step 4 Review
**What works:** the receivable amount is the hero ("تتسلم 400.00 USD"); the breakdown is short and correct.
**What is wrong:**
- **P1 · Submit button not visible** on the final step of a money flow → sticky "إرسال الطلب" (S05).
- **P2 · Channel and address are not visible in the review block** (may be below) → review must show amount, channel, destination (masked middle), ETA, and the consent line in one card above the button.
- **P2 · "حصة FPT (20 %) 100.00 USD"** · fine, but "يُخصم من رصيد الحساب" ghosts under the tab bar → opaque bar (S04).

### 3.30 `client-payout-detail.png` — `/ar/payouts/{id}`
**What works:** the best-structured data screen in the app — status badge, hero, meta, a labelled 5-step tracker, 2-column KV, two secondary actions.
**What is wrong:**
- **P3 · "موافقة 1 / موافقة 2"** exposes the internal two-person approval → "موافقة" once (keep both internally).
- **P3 · "المراجعة قبل 14 أكتوبر 2026"** on a paid request → show only while pending ("المراجعة قبل …"), hide when paid.
- **P2 · Six KV cells** (requested profit, FPT share, channel + address, cycle start, review-by, paid-at) → group: Amounts (2 cells) / Delivery (channel, address masked, paid-at) / Cycle (start–end) with small section captions, and move "بدأت الدورة" into one "الدورة 3–14 أكتوبر 2026" cell.
- **P3 · Meta line "FPT-100K-5L78C · حصة المتداول 80 % · حصة الشركة 20 %"** → "FPT-100K-5L78C · 80/20".

### 3.31 `client-payout-statement.png` — `/ar/payouts/{id}/statement`
**What works:** white printable sheet with the logo, document ID, hero amount, a proper three-column table with the profit row in green — reads as a real statement.
**What is wrong:**
- **P1 · English subtitle "Payout statement · Fiper Pro Traders ·" with a dangling "·" and the English mono date "3 Oct → 14 Oct 2026"** on an Arabic document → Arabic subtitle "كشف دفعة · Fiper Pro Traders · 3–14 أكتوبر 2026" (keep an English second line only if the document is intentionally bilingual, then align both lines).
- **P2 · Arrows "→" in date ranges** (twice) point against reading → en dash "3–14 أكتوبر 2026".
- **P2 · "حصتك 80  % من ربح الدورة 4,000.00  USD"** · double-width gaps, reads awkwardly → "حصتك: 80 % من ربح الدورة (4,000.00 USD)".
- **P2 · "دورة دفعات كل 14 يوماً" wraps with "يوماً" orphaned** → NBSP.
- **P2 · Tab bar over the document, rows ghosting ("حصة الشركة 20 % 800.00")** → document shell without tabs (S20).
- **P3 · "100  %" in the table** → "100 %".

### 3.32 `client-certificates.png` — `/ar/certificates`
**What works:** the certificate itself is handsome — big logo, bilingual meta, "حساب ممول" display title, formal sentence, name with a signature rule, 40 px account size.
**What is wrong:**
- **P2 · No actions visible** (share link, download PDF, copy verify code) and the page title "الشهادات" (list) opens straight into one certificate → action row above the paper ("مشاركة", "PDF", "رمز التحقق FPT-CRWFSEA8F4"), and if the user has several, a list first.
- **P2 · Tab bar over the paper, "3,200.00" ghosting** → document shell (S20).
- **P3 · "Funded Account Certificate · Fiper Pro Traders" English subtitle** · acceptable for a shareable certificate, but then the Arabic sentence should also have an English twin — be deliberate: fully bilingual or Arabic-only.
- **P3 · "تشهد Fiper Pro Traders بأن المتداول المذكور أدناه يتداول على حساب ممول بنسبة أرباح 80 / 20 ثابتة وأن الدفعة الموضحة أدناه قد صُرفت."** · one sentence doing two jobs → split: certification sentence + "آخر دفعة مصروفة: 3,200.00 USD في 14 أكتوبر 2026".

### 3.33 `client-notifications.png` — `/ar/notifications`
**What works:** readable titles and bodies, relative timestamps, icons that vary by type (key for credentials).
**What is wrong:**
- **P1 · "فتح" link mis-set** · bold 18–20 px white, placed 13 px lower than the timestamp and visually colliding with the meta row; it looks like a layout error on every item → make the whole item tappable (chevron at the end), or put "فتح" on the meta row baseline as a quiet link; never a lone bold word.
- **P2 · No unread state per item** while the bell and More tab carry red dots, and "تعليم الكل كمقروء" is always shown → unread dot at the start of the row + slightly brighter title; hide the button when nothing is unread.
- **P2 · "تعليم الكل كمقروء"** as a large pill at the top-left → small quiet text button in the title bar's end slot.
- **P3 · Title duplicates** ("استلمنا إثبات التحويل" ×2 within 2 hours) → group or add the order reference to the body.
- **P2 · Tab bar translucency** · "حسابك جاهز" ghosts under the tabs.

### 3.34 `client-profile.png` — `/ar/profile`
**What works:** verified badge beside "هويتك", helper that explains why the name must match the ID, timezone helper that states the FPT day reset.
**What is wrong:**
- **P2 · Name fields editable while "موثَّق"** · contradicts the helper (payouts require the verified name) → read-only values with "لتغيير الاسم تواصل مع الدعم" once KYC is verified.
- **P2 · Select controls** · value at the left, chevron at the right (LTR select in an RTL form) → value at the reading start, chevron at the end (`padding-inline-end`), 16 px body.
- **P2 · "حفظ" rendered as a secondary (outlined) button**, half hidden under the tab bar → primary style, sticky or with bottom padding above the bar.
- **P2 · Email in grey mono at the top without context** → a small identity header: name (display), e-mail (mono caption), KYC badge.
- **P3 · "Asia/Dubai" IANA id shown raw** → "دبي (GMT+4) — Asia/Dubai".

### 3.35 `client-more.png` — `/ar/more`
**What works:** calm grouped list, 55 px rows, icons, section titles, unread count on notifications.
**What is wrong:**
- **P2 · Chevrons point right (›) in RTL** → "‹" (S09).
- **P2 · Unread count "7"** · tiny grey beside the chevron → a small neutral pill next to the label.
- **P2 · Header shows e-mail only** → identity header (name, e-mail, KYC state) that also links to the profile.
- **P2 · "ابدأ تحدياً جديداً" buried under "التداول"** · the conversion entry → promote it (first row with a "+" icon is good, but also on Home when no account is active).
- **P2 · Tab bar translucency** · "مركز المساعدة" ghosts.
- **P3 · Sign-out and legal links are below the fold** · fine; make sure sign-out is a DangerZone-style row, not red text.

### 3.36 `client-orders.png` — `/ar/orders`
**What works:** receipt icon per row, status badge in the loss colour for cancelled.
**What is wrong:**
- **P1 · Row content wraps over four lines with dangling "·" separators** · "تقييم FPT — 50,000 دولار ·" / "50,000 USD" / "ملغى · لم يُدفع بعد ·" / "30 أكتوبر 2026", size stated twice ("50,000 دولار" and "50,000 USD"), price "280.00 USD" isolated at the far left with a right-pointing chevron → ListRow: line 1 "تقييم 50,000 USD" (mono number), line 2 "30 أكتوبر 2026 · تحويل يدوي"; end slot: "280.00 USD" mono + StatusBadge; chevron "‹".
- **P2 · "ملغى" + "لم يُدفع بعد"** · contradictory pair → one status: "ملغى" (with the reason in detail) or "بانتظار الدفع".
- **P2 · Product naming "تقييم FPT — 50,000 دولار"** · translation artefact → "تحدي تقييم 50,000 USD".
- **P2 · Tab bar translucency** · a whole row ghosts.

### 3.37 `client-order.png` — `/ar/orders/{id}`
**What works:** hero total with a positive "سُلّم الحساب" badge, two-column KV with hairlines, one CTA, timestamps with timezone.
**What is wrong:**
- **P2 · Seven KV cells with duplicates** (price = hero; reference = meta; order date = payment date) → hero (total, badge, product), then four cells: الحساب (link), طريقة الدفع, تاريخ الدفع, مرجع الطلب (mono, copyable).
- **P2 · "الحساب FPT-10K-FAKPJ" is plain text** → link to the account.
- **P2 · "افتح الإيصال" as the red primary** · a secondary task → outlined button; red reserved (S08).
- **P3 · Bar title "الطلب" while `<title>` says "الإيصال"** → "الطلب FPT-EA7C4563".

### 3.38 `client-order-receipt.png` — `/ar/orders/{id}/receipt`
**What works:** proper receipt document: print/PDF button, logo, ID, hero total, line-item table, total row, customer block.
**What is wrong:**
- **P2 · "البند" appears as a section heading and again as the column header** → one.
- **P2 · Line item "تقييم FPT — 10,000 دولار — حساب تقييم 10,000 USD"** · size twice in two formats → "تحدي تقييم 10,000 USD".
- **P2 · Total stated twice** (hero + table total) → acceptable on a receipt, but then drop the hero's "الإجمالي المدفوع" label duplication in the table ("الإجمالي").
- **P2 · Tab bar over the paper; customer name/e-mail ghosting** → document shell (S20).

### 3.39 `client-legal-delete-account.png` — `/ar/legal/delete-account`
**What works:** genuinely editorial: eyebrow, 28 px title, 18 px body with comfortable leading, numbered steps, bullets, arrows pointing left (correct).
**What is wrong:**
- **P3 · "يراجع المالك الطلب"** · "the owner" is an internal role → "يراجع فريق FPT الطلب".
- **P3 · Eyebrow "Fiper Pro Traders" in English** on an Arabic page → brand name is fine, but the terms page uses "قانوني" as its eyebrow; pick one pattern.
- **P2 · Tab bar over long-form text** → legal routes without tabs, with a back button only.
- **P3 · ~150 px empty above the eyebrow** → 32–48 px.

### 3.40 `client-legal.png` — `/ar/legal/terms`
**What works:** readable long-form typography; version and effective date shown.
**What is wrong:**
- **P0 · Draft banner on production** ("مسودة — بانتظار اعتماد المؤسس؛ ليست نصاً قانونياً نهائياً") while checkout requires acceptance → S03.
- **P2 · Internal slug "terms-2026-10-29" in the version line; "(مسودة، 29-10-2026)" in the body** → "النسخة: 29 أكتوبر 2026 — سارية من 29 أكتوبر 2026".
- **P3 · Two date formats on one screen** (29-10-2026 vs 29 أكتوبر 2026) → one.
- **P3 · Clause numbers inline ("1. تحكم…")** → hanging numbers or `<ol>` with proper indentation for scanning.
- **P2 · Tab bar over the document** → S20.

### 3.41 `client-verify.png` — `/ar/verify/{code}`
**What works:** calm, scannable, "شهادة صالحة" in the positive colour, the issuer stated; this is close to right.
**What is wrong:**
- **P2 · Public page rendered inside the authenticated shell** (tab bar with "المزيد" dot) → public shell with the language switch and logo only.
- **P3 · Full-width stretched "شهادة صالحة" pill** · badges hug content → inline badge or a full-width success banner with an icon and a sentence ("هذه الشهادة صادرة عن FPT وصالحة").
- **P3 · Bar title "التحقق من شهادة" vs H1 "التحقق من الشهادة"** → same string.

### 3.42 `client-partners.png` — `/ar/partners`
**What works:** hero "مستحقة 323.90 USD", next payout date with timezone, the referral link with a copy button.
**What is wrong:**
- **P2 · Six-cell stat grid** (محجوزة, مدفوعة, الفئة, التسجيلات, الطلبات المدفوعة, المبيعات المدفوعة في آخر 30 يوماً) with tiny "2"/"4" values and a wrapping tier description → one stat row (3 tiles: التسجيلات 2 · المبيعات 4 · المدفوع 0.00 USD) + a tier card ("الفئة الحالية 15 % · 4/10 مبيعات للدرجة التالية" with a small meter).
- **P2 · Referral link wraps mid-token** ("…signup?ref" / "=FPT-SARA2") in 20 px mono → show the code "FPT-SARA2" as the hero of the "رابطك" card with two buttons "نسخ الرابط" and "مشاركة"; the full URL as a 14 px caption with `overflow-wrap:anywhere` and `direction:ltr; unicode-bidi:isolate`.
- **P2 · Meta line duplicates the hero amount and ends with a dangling "·"** → "الدفعة الأسبوعية التالية: 2 نوفمبر، 09:00 (دبي)".
- **P3 · "15 %" renders "% 15"** inside the Arabic tier text → `<bdi>` / NBSP (S11).
- **P2 · Tab bar translucency** · "FPT-SARA2" and a copy icon ghost under the tabs.

### 3.43 `client-not-found.png` — `/ar/no-such-page`
**What works:** centred logo, clear bilingual message, one CTA, no chrome. **This screen is fine.**
**What is wrong:**
- **P3 · `<title>` is "Fiper Pro Traders" without the page name** → "الصفحة غير موجودة · FPT".
- **P3 · Bilingual body is sensible for a 404; keep the Arabic first (it is) and reduce the English to 14 px grey** so the hierarchy is clear.

---

## 4. Ranked top-15 fixes (biggest visible impact first)

1. **Make the account tabbed panel direction-safe (S01).** Pass `dir` to every headless primitive / add a DirectionProvider, remove container-level `direction`/`row-reverse`, isolate numbers with `<bdi>`, and add an RTL screenshot test per tab. Fixes account-funded, account-breached, account-rules, account-credentials, account-trades and the tab-order flip in one change.
2. **Rebuild the Rules tab on the RuleMeter contract (S06, §3.11).** Hero = remaining amount, bar = remaining room (full when safe), colour by proximity, definition + reset line; same component on Home and Overview so the three places never disagree.
3. **Fix the Shop cards (S02).** Size at the reading start, fee at the end, check at the start edge, "Popular" as a corner tag; verify EN and AR side by side; add a sticky "المتابعة · 280 USD".
4. **Remove unfinished production content (S03).** Draft banner on Terms, empty EN consent sentence, placeholder bank reference, exposed slugs — gate these at build/deploy time, not by hand.
5. **Opaque header and tab bar + hide the tab bar in flows and documents (S04, S05, S20).** One CSS change removes the ghosting on ~30 screens; hiding tabs in checkout/payout/proof/statement/receipt/certificate/legal makes those screens feel like a finished product.
6. **Sticky primary action in every money flow (S05).** Shop → checkout → transfer proof → payout steps 1–4: button always visible above the safe area, summary collapsed to a one-line chip.
7. **Colour discipline (S08).** Neutral focus ring, white selection outlines/filled checks, positive-colour "funded" badge, white chart markers and step bars; red stays for brand + the single primary CTA.
8. **Payout numbers must not contradict (§3.24/3.25, §3.26).** Hero shows the requestable amount; "12 يوم تداول (المطلوب 10)"; 80/20 stated once; ineligible payouts show the reason instead of swapping the button.
9. **Terminology and copy pass (S07).** سحب everywhere for traders; fix "55 مقالة", "ملغى/لم يُدفع", "المالك", "فتح"; translate the landing trust line and the statement subtitle (S10).
10. **Account header block + switcher (§3.9, §3.14, S19).** ID as the screen title, meta captions, inline StatusBadge, quiet link; account chips on Home/Account for multi-account users; delete the 65 px empty band.
11. **RTL iconography and native controls (S09, S10).** Mirror chevrons and arrows, move select chevrons to the end, replace the native file and date inputs with custom Arabic controls.
12. **Money/Percent formatting components (S11, S17).** Mono everywhere for amounts, `12,500.00 USD` with NBSP, `10 %` that never splits or flips, no "دولار" in data cells, separators that never end a line.
13. **Help accordion and Notifications rows (§3.5, §3.33).** Hide the native details marker and right-align questions; put "فتح" on the meta baseline or make rows tappable; per-item unread dots.
14. **List rows and KV grids (S12, S14; §3.36, §3.37, §3.42, §3.30).** Orders as two-line ListRows with the amount in the end slot; order/payout detail reduced to grouped cells without duplicates; partners stats as one tile row + tier meter.
15. **Document pages polish (S20; §3.31, §3.32, §3.38).** Document shell without tabs, action row (share/PDF/print/verify), Arabic subtitles and en-dash date ranges, no duplicated headings.

**What should stay exactly as it is:** the dark ground and restrained palette; the 40 px mono hero number pattern; green-for-profit / orange-for-loss; the blue "قيد التقييم" and green "مدفوع/موثّق" badges; the breach report's content model; the white "paper" statement/receipt/certificate; the pill button family; the empty-state pattern; the bilingual 404.
