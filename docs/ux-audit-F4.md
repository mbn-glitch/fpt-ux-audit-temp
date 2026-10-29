# مراجعة شاملة لواجهة وتجربة المستخدم — FPT (العميل + الإدارة، عربي + إنجليزي) — جولة RD1 § 6 (تحليل لجولة F4)

التاريخ: 30 أكتوبر 2026 · المصادر: `findings-a11y.json`، `findings-devices.json`، `findings-mail.json`، `findings-technical.json`، `findings-text.json`، `findings-visual-admin.json`، `findings-visual-client.json`، `findings-visual-code.json` · لقطات الأجهزة الحقيقية `artifacts/real-device/coverage-20261030/` · قصاصات الأدلة `artifacts/ux-audit-F4/evidence/` · Lighthouse `artifacts/ux-audit-F4/lighthouse/` · النسخة الآلية `artifacts/ux-audit-F4/findings.json`.

> مرحلة تحليل فقط: لا شيء هنا أُصلح (ما عدا عيوب التغطية الحقيقية في جولة RD1 § 3). الخطورة: P0 حاجب · P1 رئيسي · P2 ثانوي · P3 صقل. كل ملاحظة تحمل دليلها (ملف:سطر أو لقطة) والمهارة التي أُسندت إليها (ui-ux-pro-max — المعيار الذي حدده المالك — وui-ux-design-pro وweb-design-guidelines وmobile-app-ui-design وsecurity-audit وبقية المهارات المقروءة). مرجع الجودة: مشروع TWO Markets (`~/Projects/personal-agent-markets`) — قورنت القرارات (الحالات، المسافات، التسلسل الهرمي، الحركة) لا الألوان.

## 1. جدول الأعداد

| الفئة | P0 حاجب | P1 رئيسي | P2 ثانوي | P3 صقل | المجموع |
|---|---|---|---|---|---|
| تقني | 1 | 5 | 19 | 36 | 61 |
| بصري | 0 | 10 | 39 | 87 | 136 |
| نص | 2 | 15 | 52 | 63 | 132 |
| وصول | 0 | 4 | 23 | 46 | 73 |
| أداء | 0 | 0 | 2 | 8 | 10 |
| RTL | 1 | 8 | 11 | 11 | 31 |
| هوية | 2 | 9 | 12 | 12 | 35 |
| **المجموع** | 6 | 51 | 158 | 263 | **478** |

حسب التطبيق: الإدارة 133 · مشترك 90 · العميل 175 · البريد 54 · مكوّنات @fpt/ui 26.

## 2. الملاحظات حسب الشاشة

### الإدارة — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-040 | P3 صقل | بصري | Money quietCode (text-[0.55em]) | رمز العملة في البلاطات يُحسب 0.55em فيهبط إلى ≈ 12.1px على الهاتف (تحت التسمية 13) | `quietCode` يرسم الرمز بـ `text-[0.55em]`؛ في `StatTile size=lg` قيمة الهاتف 22px → 12.1px، وأي استعمال بـ `size="md"` خارج البلاطة يعطي 8.8px (لا يوجد حالياً لكن الـ API يسمح). قيمة em عشوائية بدل درجة المقياس. | packages/ui/src/components/money.tsx:60 و:62 `className="… font-body text-[0.55em] font-semibold text-muted"` · stat-tile.tsx:41 `size === 'lg' ? 'text-xl sm:text-2xl'` · apps/admin/src/app/[locale]/(app)/page.tsx:95 `<Money … quietCode />` | `text-label` (13px) أو `max(13px, .55em)` للرمز الهادئ. | S | design-system § 2 · ui-ux-pro-max §6 font-scale | `packages/ui/src/components/money.tsx` |

### الإدارة — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-027 | P3 صقل | أداء | صفحات القوائم — `.limit(N)` بلا ترقيم ولا إشعار | القوائم تُقصّ خادمياً عند 200–400 صف بصمت؛ لا ترقيم خادمي ولا سطر «يُعرض آخر N» | `DataTableRows` يعرض 100 صف ثم «المزيد» من المصفوفة المُسلَّمة، لكن المصفوفة نفسها مقصوصة بالاستعلام. بعد تجاوز الحد تختفي السجلات الأقدم من القوائم وتظهر فقط عبر مرشّحات أضيق، دون أي إشارة للموظف. بنية `page=` موجودة في صفقات العميل ولا في الإدارة. | apps/admin/src/app/[locale]/(app)/accounts/page.tsx:38 (`.limit(200)`), payouts/page.tsx:36 (200), orders/page.tsx:44 (300), deletions/page.tsx:38 (300), payments/page.tsx:36 (400); packages/ui/src/components/data-table.tsx:71-72, :92-93. | أضف ترقيماً بالمؤشر (`?before=<createdAt\|id>`) مع رابط «الأقدم» أسفل الجدول، أو على الأقل سطراً «يُعرض آخر 200 — ضيّق المرشّحات» عندما `rows.length === LIMIT`. | M | web-interface-guidelines § Performance / Navigation & State | `apps/admin/src/app/[locale]/(app)/payments/page.tsx`، `apps/admin/src/app/[locale]/(app)/orders/page.tsx`، `apps/admin/src/app/[locale]/(app)/accounts/page.tsx`، `packages/ui/src/components/data-table.tsx` |

### الإدارة — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-018 | P2 ثانوي | تقني | DataTableRows + استعلامات limit() | القوائم تقطع صامتةً عند 200–500 سجل بلا ترقيم خادمي ولا إشارة «يوجد المزيد» | كل قائمة إدارة تقرأ `.limit(200\|300\|400\|500)` ثم تعرض 100 صف وزر «المزيد» client-side؛ لا توجد صفحة تالية ولا عدّاد إجمالي ولا رسالة أن النتائج مقطوعة. مع نمو الحسابات يختفي الأقدم إلا عبر الفلاتر. | apps/admin/src/app/[locale]/(app)/accounts/page.tsx:183 `.limit(200)` · orders/page.tsx:44 `.limit(300)` · payouts/page.tsx:36 · audit/page.tsx:361 · coupons/page.tsx:37 `.limit(500)` · deletions/page.tsx:437 · mail/page.tsx:243 · packages/ui/src/components/data-table.tsx:71 `pageSize = 100` و:150–153 زر «المزيد» | ترقيم عبر `?page=`/cursor في الاستعلام مع عدّاد إجمالي في وصف الصفحة، أو على الأقل سطر «تُعرض أحدث 200 — ضيّق البحث» عندما يصل العدد للحد. | M | ui-ux-pro-max §3 virtualize-lists · ui-ux-design-pro component-patterns «Pagination: show current range + total» | `packages/ui/src/components/data-table.tsx`، `apps/admin/src/app/[locale]/(app)/accounts/page.tsx`، `apps/admin/src/app/[locale]/(app)/orders/page.tsx` |

### الإدارة — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-022 | P2 ثانوي | وصول | nearestCell / pctCell / age / queue counters | المعنى باللون وحده: نسبة «أقرب حد» كهرمانية/حمراء بلا كلمة ولا أيقونة، والمكوّن الصحيح موجود وغير مستعمل | الجداول تلوّن الرقم `text-drawdown`/`text-danger` فقط. `NearestLimit` في Finance.tsx يضيف `triangle-alert` + «تحذير السحب» / `circle-x` + «تجاوز الحد» كما تقتضي الدلالات المالية، لكنه غير مستورد في أي صفحة. كذلك عمر الطلب `text-warning` وعدّادات الفشل `text-danger`. | apps/admin/src/app/[locale]/(app)/accounts/page.tsx:187–191 `const tone = bps <= 0 ? 'text-danger' : bps < 1000 ? 'text-drawdown' : 'text-ink'` · risk/page.tsx:421–424 · accounts/[id]/page.tsx:382 · payments/page.tsx:413 `text-warning` · health/page.tsx:446 `'num-ui text-danger'` · apps/admin/src/components/Finance.tsx:21 `NearestLimit` (0 استعمال) | استبدل الخلايا بـ `<NearestLimit bps=…/>`، وأضف أيقونة Clock للعمر المتأخر وكلمة «فشل» بجانب العدّاد. | S | ui-ux-pro-max §1 color-not-only · fpt-brand finance semantics | `apps/admin/src/app/[locale]/(app)/accounts/page.tsx`، `apps/admin/src/app/[locale]/(app)/risk/page.tsx`، `apps/admin/src/components/Finance.tsx` |

### الإدارة — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-048 | P3 صقل | تقني | apps/admin/next.config.ts headers + route | استثناء مسار المعاينة من `API_CSP` صحيح ومقيّد بـ regex ناف — تأكيد لا عيب؛ يبقى التحقق أن المسار يحمل سياسة بريد خاصة | `/api/:path((?!mail/).*)` يستثني كل `/api/mail/*` لا المعاينة فقط؛ إن أُضيف لاحقاً مسار JSON تحت `/api/mail/` سيُرسل بلا CSP. الإطار معزول `sandbox` في الصفحة. | apps/admin/next.config.ts:255; artifacts/ux-gates/* (رسالة sandbox). | ضيّق الاستثناء إلى `/api/:path((?!mail/[^/]+/preview$).*)` أو ضع `API_CSP` افتراضياً داخل معالِجات JSON تحت `/api/mail/`. | S | security-audit (CSP scope) | `apps/admin/next.config.ts`، `apps/admin/src/app/api/mail/[id]/preview/route.ts` |

### الإدارة — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-003 | P2 ثانوي | تقني | apps/admin/src/app/not-found.tsx | صفحة 404 الجذرية للوحة الموظفين تُسبق بناؤها فتشحن 18 سكربت بلا nonce وتحجبها CSP كلها | الإصلاح RD1 طُبّق على العميل فقط (`await headers()` يجعل الصفحة تُصيَّر لكل طلب فتنال nonce الوسيط). صفحة 404 للوحة ما زالت ثابتة: الإنتاج يعيد 404 مع CSP تحمل nonce لكن لا سكربت يحملها، فتُسجَّل 18 رسالة «Refused to load/execute» في الكونسول ولا يحدث hydration (الرابط `<a>` يعمل لأنه HTML خام). كل عنوان خاطئ يُدخله موظف يُنتج ضجيج أخطاء يخفي الأخطاء الحقيقية في بوابات الكونسول. | curl https://admin.fptraders.app/ar/nonexistent-xyz → `HTTP/2 404`، `scripts total: 18 ; nonced: 0` مقابل العميل `17/17`؛ apps/admin/src/app/not-found.tsx:47-58 (مكوّن متزامن بلا `headers()`)؛ apps/client/src/app/not-found.tsx:20-21 (النمط المُصلَح). | الحد الأدنى: `export default async function NotFound(){ await headers(); … }` في apps/admin/src/app/not-found.tsx (كما في العميل)، أو أضف `apps/admin/src/app/[locale]/not-found.tsx` مترجَماً داخل تخطيط اللغة الديناميكي. تحقّق بعد النشر بأن `grep -c 'nonce=' == grep -c '<script'`. | S | security-audit (CSP)  | `apps/admin/src/app/not-found.tsx`، `apps/client/src/app/not-found.tsx` |

### الإدارة — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-035 | P2 ثانوي | بصري | <p className="text-sm"> · <label className="text-sm"> | جمل وتسميات بحجم 14px في مسارات مشروطة/تبويبات لا تقيسها البوّابة | أربع فقرات إرشادية و`label` في المحاكي تُرسم 14px على `<p>/<label>` — تحت أرضية 15. كلها تظهر شرطياً (`mine && …`) أو داخل تبويب غير نشط (Radix يفكّ المحتوى) فلم تلتقطها بوّابة smallBody. | apps/admin/src/app/[locale]/(app)/breaches/[id]/page.tsx:199 `<p className="m-0 text-sm text-muted">{t('youApproved')}</p>` · accounts/[id]/page.tsx:256 `<p className="m-0 text-sm text-muted">{t('prov.passwordNote')}</p>` · payouts/[id]/page.tsx:131 · products/ProductsClient.tsx:142 `<label … className="flex min-h-10 items-center gap-2 text-sm">` | `text-body-2` بدل `text-sm` على هذه الأربعة؛ وسّع مسار البوّابة ليفتح التبويبات ويتجاوز الشروط (حساب مشروط في بيانات الاختبار). | S | design-system § 2 | `apps/admin/src/app/[locale]/(app)/breaches/[id]/page.tsx`، `apps/admin/src/app/[locale]/(app)/accounts/[id]/page.tsx`، `apps/admin/src/app/[locale]/(app)/payouts/[id]/page.tsx`، `apps/admin/src/app/[locale]/(app)/products/ProductsClient.tsx` |

### الإدارة — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-045 | P3 صقل | بصري | StatusPill بخرائط نغمة محلية | سبع خرائط نغمة محلية لـ StatusPill بدل خريطة StatusBadge الواحدة — الكلمة نفسها بألوان مختلفة بين الصفحات | design-system § 4 ينص على خريطة واحدة (`StatusBadge`) لكل حالات المنصة. الإدارة تبني `tone` يدوياً في كل صفحة: `TONE` في الحذف، `active ? success : neutral` في الكوبونات والموظفين والمسارات، `severityTone` للاحتيال، `impact` للأخبار — بلا أيقونة في معظمها (الكلمة واللون فقط). | apps/admin/src/app/[locale]/(app)/deletions/page.tsx:415 `const TONE: Record<ReqStatus, StatusTone> = {…}` · coupons/page.tsx:71 · staff/page.tsx:25–26 · settings/page.tsx:41,52 · fraud/page.tsx:629,632 · products/page.tsx:241,247 · settings/ManualRoutes.tsx:408 | وسّع `StatusKind` في status-badge.tsx (deletion, coupon, staff, legal, news, fraudSeverity, route) وأزل الخرائط المحلية. | S | design-system § 4 StatusBadge · ui-ux-pro-max §4 consistency | `packages/ui/src/components/status-badge.tsx`، `apps/admin/src/app/[locale]/(app)/deletions/page.tsx`، `apps/admin/src/app/[locale]/(app)/coupons/page.tsx` |

### الإدارة — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-038 | P2 ثانوي | تقني | artifacts/ux-gates/gates-phone-360.json (تقرير بوابات محلي غير مُلتزم) | تقرير البوابات المحلي الحالي يُظهر فيض أفقي في صفحة التكاملات عند 360 px (الجسم 376 > 360) — فشل بوابة ux-floors | الملف المعدَّل في شجرة العمل (20:43 بتوقيت الخادم) يسجّل `pass=false` بسبب الفيض؛ رسالة الالتزام RD1 تذكر إصلاح «التفاف رموز المسار الطويلة» لكن التقرير لم يُحدَّث بعد الإصلاح أو الإصلاح لم يغطِّ الحالة. يلزم إعادة التشغيل للحسم. | artifacts/ux-gates/gates-phone-360.json → failures: `phone-360 · admin-integrations · horizontal overflow (doc 360 / body 376 > 360)`؛ git status (الملف M)؛ HEAD 4bd13f8 (وصف الإصلاح). | شغّل `pnpm exec tsx e2e/boot.ts` ثم `ux-gates` لمشروع phone-360-admin؛ إن استمر الفيض أضف `overflow-wrap: anywhere` + `min-width: 0` إلى خطوات التكامل (`.ui-wrap`)، ثم التزم بالتقرير المحدَّث. | S | ui-ux-pro-max § responsive / docs/design-system.md § UX floors | `artifacts/ux-gates/gates-phone-360.json`، `apps/admin/src/app/[locale]/(app)/integrations/page.tsx` |

### الإدارة — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-007 | P1 رئيسي | هوية | KycDecision / DeletionActions trigger buttons | زر أحمر أساسي في كل صف من الجدول — عشرات الإجراءات الأساسية على شاشة واحدة | كل صف في قائمة الهوية يحمل `ActionDialog triggerVariant="primary"` («اعتماد»)، وكل طلب حذف مفتوح كذلك. مع 20 صفاً تظهر 20 بلاطة متدرّجة حمراء، فتضيع قاعدة «إجراء أساسي واحد لكل شاشة» (brand § C red roles، design-system § 5.3) ويصبح الأحمر لوناً عادياً. | apps/admin/src/app/[locale]/(app)/kyc/KycActions.tsx:350 `<ActionDialog action={approveKyc} … triggerVariant="primary"` (داخل كل صف kyc/page.tsx:276) · apps/admin/src/app/[locale]/(app)/deletions/DeletionActions.tsx:573 `triggerVariant="primary"` (deletions/page.tsx:459) | في الصفوف استعمل `secondary` للاعتماد و`ghost` للرفض، واجعل الإجراء الأساسي داخل الحوار فقط (submit primary) أو على صفحة تفاصيل الحالة. | S | ui-ux-pro-max §4 primary-action · fpt-brand «red: tile · one primary action · active state» | `apps/admin/src/app/[locale]/(app)/kyc/KycActions.tsx`، `apps/admin/src/app/[locale]/(app)/deletions/DeletionActions.tsx` |

### الإدارة — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-039 | P3 صقل | تقني | e2e/ux-gates.spec.ts — قائمة تجاهل أخطاء الكونسول | خطأ كونسول متوقع من إطار البريد المعزول (`sandbox` بلا allow-scripts) ما زال يُسقط بوابات 1440/390/360 رغم أن حارس الأجهزة الحقيقية يتجاهله | الإطار `sandbox` يرفض سكربت التتبّع المحقون — سلوك أمني صحيح — لكن تقارير ux-gates المحلية الثلاثة تسجّله كفشل، فتبدو البوابة حمراء بلا عيب حقيقي. | artifacts/ux-gates/gates-desktop-1440.json, gates-phone-390.json, gates-phone-360.json → `admin-mail-detail · console error (1): Blocked script execution … sandboxed`؛ HEAD 4bd13f8 (التجاهل أُضيف لـ real-device-coverage فقط). | وحّد قائمة التجاهل (`Blocked script execution in '…/api/mail/*/preview'`) بين e2e/ux-gates.spec.ts وscripts/real-device-coverage.mjs في وحدة مشتركة. | S | security-audit (sandboxed preview) / tooling | `e2e/ux-gates.spec.ts`، `scripts/real-device-coverage.mjs` |

### الإدارة — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-059 | P3 صقل | بصري | iframe h-[720px] | معاينة البريد في إطار ثابت 720px — على الهاتف تمرير داخل تمرير وقيمة عشوائية | ارتفاع ثابت بغض النظر عن محتوى البريد أو حجم الشاشة. | apps/admin/src/app/[locale]/(app)/mail/[id]/page.tsx:367 `className="block h-[720px] w-full max-w-full border-0"` | `aspect-ratio` أو `min-h-[60dvh]` مع ضبط الارتفاع من `postMessage` للمحتوى. | S | ui-ux-pro-max §5 scroll-behavior · viewport-units | `apps/admin/src/app/[locale]/(app)/mail/[id]/page.tsx` |

### الإدارة — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-066 | P3 صقل | بصري | placeholder | نصوص placeholder لاتينية مكتوبة في الكود خارج الترجمة ولا تنتهي بـ «…» | `name@example.com`, `Non-farm payrolls`, `P1`, `{"lots": 10}` مكتوبة حرفياً داخل الصفحات؛ في الواجهة العربية تظهر إنجليزية ولا تتبع قاعدة «…». | apps/admin/src/app/[locale]/(app)/orders/page.tsx:113 `placeholder: 'name@example.com'` · payments/page.tsx:434 · kyc/page.tsx:315 · mail/page.tsx:295 · settings/SettingsClient.tsx:410 `placeholder="Non-farm payrolls"` · simulator/SimulatorClient.tsx:121 و:177 | مفاتيح ترجمة `placeholders.*` بصيغة «name@example.com…». | S | web-interface-guidelines §Forms «Placeholders end with …» | `apps/admin/src/app/[locale]/(app)/orders/page.tsx`، `apps/admin/src/app/[locale]/(app)/settings/SettingsClient.tsx` |

### الإدارة — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-023 | P2 ثانوي | تقني | Badge mono / نصوص خام | رموز تقنية خام في طبقة العرض خارج «التفاصيل التقنية» | design-system § 5.5: في الإدارة تُعرَّب التعدادات في طبقة العرض والحمولة الخام تحت التفاصيل. لكن: نوع الأمر `c.type`، حالة الحساب الخام `a.status`، نوع حدث الدفع `e.kind.replaceAll('_',' ')`، مصدر الخبر `r.source`، وضع المحوّل `v` (live/mock)، ملخص `k=v`، ونص `lastError` تظهر كلها كنص مرئي. | apps/admin/src/app/[locale]/(app)/orders/[id]/page.tsx:303–305 `<Badge tone="muted" mono>{c.type}</Badge>` و:284 `{e.kind.replaceAll('_', ' ')}` · products/ProductsClient.tsx:571 `<Badge tone="muted">{a.status}</Badge>` · settings/page.tsx:55 `{r.source}` و:122 `{v}` · fraud/links/[userId]/page.tsx:167 و:188–194 `summary()` · components/CommandProgress.tsx:51 `{view.lastError}` | مرّر الكلمات عبر `labels.ts` (command types، adapter modes، statuses)، وانقل `lastError` و`summary` تحت `JsonViewer`. | S | design-system § 5.5 · ui-ux-design-pro Nielsen #2 match the real world | `apps/admin/src/app/[locale]/(app)/orders/[id]/page.tsx`، `apps/admin/src/app/[locale]/(app)/products/ProductsClient.tsx`، `apps/admin/src/app/[locale]/(app)/settings/page.tsx`، `apps/admin/src/components/CommandProgress.tsx` |

### الإدارة — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-037 | P3 صقل | أداء | apps/admin/src/app/[locale]/(app)/payments/[id]/page.tsx | صورة الإثبات بلا `width`/`height` → انزياح تخطيط عند التحميل | `<img src=/api/proofs/… className="max-h-[70vh] w-full object-contain">` بلا أبعاد؛ المحتوى تحتها (الأحداث، الأزرار) يقفز عند وصول الصورة. | apps/admin/src/app/[locale]/(app)/payments/[id]/page.tsx:150. | احفظ أبعاد الصورة عند الرفع (sharp/`image-size`) ومرّرها، أو ضع الصورة داخل صندوق `aspect-ratio: 3/4; max-height: 70vh` ثابت. | S | web-interface-guidelines § Images | `apps/admin/src/app/[locale]/(app)/payments/[id]/page.tsx`، `apps/client/src/app/api/orders/[orderId]/proof/route.ts` |

### الإدارة — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-029 | P2 ثانوي | هوية | PayoutChannelsForm submit + ManualRouteDialog trigger | زرّان أساسيان متجاوران في تبويب واحد: «حفظ قنوات الصرف» و«إضافة مسار» | تبويب «الدفعات» يعرض بطاقة قنوات الصرف بزر إرسال `primary` وتحتها لوحة المسارات اليدوية بزر «إضافة» `primary`. شاشة واحدة، إجراءان أساسيان. | apps/admin/src/app/[locale]/(app)/settings/page.tsx:198–213 · settings/LaunchSettingsForms.tsx:585 `submitVariant="primary"` · settings/ManualRoutesClient.tsx:488 `triggerVariant={edit ? 'ghost' : 'primary'}` | زر «إضافة مسار» ثانوي (الإجراء الأساسي داخل حواره)، وأبقِ الحفظ أساسياً. | S | ui-ux-pro-max §4 primary-action | `apps/admin/src/app/[locale]/(app)/settings/ManualRoutesClient.tsx`، `apps/admin/src/app/[locale]/(app)/settings/LaunchSettingsForms.tsx` |

### الإدارة — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-031 | P2 ثانوي | وصول | CardTitle (h3 افتراضياً) تحت h1 مباشرة | قفز مستوى العناوين في الإدارة: h1 ثم h3 (CardTitle) بلا h2 | `CardTitle` يرسم `h3` افتراضياً وتُستعمل البطاقات مباشرة تحت `PageTitle` (h1) في سبع صفحات، فيُفقد مستوى h2. في /integrations تُستعمل h4 داخل البطاقة (h1→h3→h4). قارئ الشاشة يفقد بنية الصفحة. | packages/ui/src/components/card.tsx:40 `as: Tag = 'h3'` · apps/admin/src/app/[locale]/(app)/settings/page.tsx:82 `<CardTitle>{t('reset.title')}</CardTitle>` (تحت PageTitle :64) · orders/[id]/page.tsx:192 · fraud/[id]/page.tsx:750 · fraud/links/[userId]/page.tsx:101 · integrations/page.tsx:442 و:469 `<h4 …>` | `<CardTitle as="h2">` عندما تكون البطاقة قسماً من الصفحة، أو لفّ البطاقات في `Section` (h2). | S | ui-ux-pro-max §1 heading-hierarchy | `packages/ui/src/components/card.tsx`، `apps/admin/src/app/[locale]/(app)/settings/page.tsx`، `apps/admin/src/app/[locale]/(app)/orders/[id]/page.tsx` |

### الإدارة — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-081 | P3 صقل | بصري | <p className="text-label"> لجمل كاملة | جمل إرشادية كاملة بحجم 13px عبر صنف التسمية (تحايل على أرضية 15) | ملاحظات مثل «تُعرَّف المعرّفات في config/legal.ts…» تُرسم `<p className="text-label text-muted">`؛ البوّابة تستثني `.text-label` فتمرّ جمل 13px. | apps/admin/src/app/[locale]/(app)/settings/page.tsx:88,111,123,167,281 · products/page.tsx:315 · settings/ManualRoutes.tsx:469 · e2e/ux-audit.ts:75 (`.text-label` مستثنى) | `text-body-2` للجمل؛ أبقِ `text-label` للتسميات فوق القيم فقط. | S | design-system § 2 | `apps/admin/src/app/[locale]/(app)/settings/page.tsx`، `apps/admin/src/app/[locale]/(app)/products/page.tsx` |

### الإدارة — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-014 | P2 ثانوي | بصري | (auth)/layout header | رأس شاشات دخول الإدارة بارتفاع ثابت 56px مع pt-safe على العنصر نفسه — يُسحق على الهواتف ذات النوتش | `<header className="h-14 … pt-safe">`: مع box-sizing:border-box يبقى الارتفاع 56px ويُضاف حشو علوي يصل 47px فيتبقى 9px للبلاطة 32px ومبدّل اللغة. AppShell يفعلها صحيحاً (pt-safe على الغلاف، h-14 على الصف الداخلي). | apps/admin/src/app/[locale]/(auth)/layout.tsx:16 `<header className="mx-auto flex h-14 w-full max-w-md items-center justify-between gap-3 px-gutter pt-safe">` · قارن packages/ui/src/components/app-shell.tsx:34 (`header … pt-safe`) و:63 (`AppHeaderBar h-14`) | انقل `pt-safe` إلى غلاف خارجي أو استبدل `h-14` بـ `min-h-14`. | S | web-interface-guidelines §Safe Areas · ui-ux-pro-max §2 safe-area-awareness | `apps/admin/src/app/[locale]/(auth)/layout.tsx` |

### الإدارة — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-021 | P2 ثانوي | وصول | .ui-inline-link داخل .ui-row__t | الرابط المضمّن يمدّد منطقة لمسه إلى 44px بهوامش سالبة لكن عنوان الصف يقصّها (overflow:hidden) | `.ui-inline-link` يحقق 44px بـ `min-height:44px; margin-block:-12px`؛ عندما يوضع داخل `.ui-row__t` (`overflow:hidden`) تُقص المنطقة الممدَّدة فلا تستقبل النقر، فتعود منطقة اللمس الفعلية إلى ارتفاع السطر ≈ 25px. البوّابة تقيس `getBoundingClientRect` فلا تكتشف القص. | packages/ui/src/components.css:398 `.ui-row__t { … overflow: hidden; text-overflow: ellipsis; }` · :465 `.ui-inline-link { … min-height: 44px; margin-block: -12px; …}` · apps/admin/src/app/[locale]/(app)/staff/page.tsx:56 `title: <span><Link … className="ui-inline-link">{r.name}</Link>…` | أزل `overflow:hidden` من `.ui-row__t` (الالتفاف كافٍ) أو اجعل الصف كله رابطاً عبر `href` بدل رابط مضمّن. | S | ui-ux-pro-max §2 touch-target-size · §1 web-target-size | `packages/ui/src/components.css`، `apps/admin/src/app/[locale]/(app)/staff/page.tsx` |

### الإدارة — 404

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-desktop.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-en-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-en-desktop.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-067 | P3 صقل | وصول | صفحة غير موجودة | 404 ثنائية اللغة «الصفحة غير موجودة / Page not found» وزر «نظرة عامة · Overview» | مقبول لمسار بلا لغة، لكن 404 داخل /ar/ تعرف اللغة؛ التصميم بلا شريط جانبي ولا بحث. | artifacts/ux-audit-F4/evidence/admin-404-ar-desktop.png | 404 داخل مسار اللغة بلغة واحدة ومع الإطار (الشريط الجانبي) إن كان المستخدم مسجّلاً. | S | ui-ux-pro-max §9 persistent-nav | `apps/admin/src/app/not-found.tsx`، `apps/admin/src/app/[locale]/(app)/layout.tsx` |

### الإدارة — ActionDialog (الرفض، الإغلاق، التصحيح اليدوي)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-073 | P3 صقل | بصري | Radix Dialog backdrop close · بعد النجاح | النقر على الخلفية يغلق حوارات الإجراءات المدمّرة، وبعد النجاح لا يوجد زر «تم» سوى X/Esc | Radix يغلق على نقر الخلفية افتراضياً حتى لحوار يحمل سبباً مكتوباً وكلمة مرور؛ بعد النجاح يبقى الحوار مفتوحاً مع CommandProgress بلا زر إغلاق في التذييل. | packages/ui/src/components/dialog.tsx:22–44 (لا `onPointerDownOutside`) · apps/admin/src/components/ActionDialog.tsx:84–86 و ActionForm.tsx:79 `hideSubmitOnSuccess` | `onPointerDownOutside={e => e.preventDefault()}` للحوارات المدمّرة؛ زر «إغلاق» ثانوي بعد النجاح. | S | ui-ux-design-pro component-patterns Modals «Click backdrop to close (unless destructive)» | `packages/ui/src/components/dialog.tsx`، `apps/admin/src/components/ActionDialog.tsx` |

### الإدارة — account

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-filter-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-focus-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-desktop.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-filter-empty-ar-desktop.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-focus-ar-desktop.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-en-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-filter-empty-en-phone.png` … (+4)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-064 | P3 صقل | نص | شريط الإجراءات | تسميات إجراء غامضة: «إعادة حساب التعيين»، «تعيين صلاحيات الوصول» | ثلاثة أزرار ثانوية بلا تمييز، ومصطلح «التعيين» يُستخدم لإعادة الضبط اليومية ولصلاحيات التداول معاً. | admin-admin-account.png; artifacts/ux-after/admin-account-phone-390-ar.png | «إعادة احتساب إعادة الضبط اليومية» → «إعادة حساب يوم التداول»؛ «صلاحيات الوصول» → «وضع التداول (تداول/قراءة فقط)». أضف وصفاً قصيراً في حوار التأكيد. | S | WIG Content & Copy (specific labels); Nielsen #2 | `packages/i18n/src/messages/ar.json`، `apps/admin/src/app/[locale]/(app)/accounts/[id]/page.tsx` |
| VAD-065 | P3 صقل | بصري | سطر التعريف | كلمة «تقييم» مكررة: شارة «تقييم» ثم نص «تقييم Khalid Al-Mansoori» | المرحلة تظهر كشارة وكنص. | admin-admin-account.png | الشارة للحالة، والنص للعميل فقط. | S | Nielsen #8 | `apps/admin/src/app/[locale]/(app)/accounts/[id]/page.tsx` |

### الإدارة — accounts

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-filter-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-focus-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-desktop.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-filter-empty-ar-desktop.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-focus-ar-desktop.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-en-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-filter-empty-en-phone.png` … (+4)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-019 | P2 ثانوي | بصري | صفّا الرقائق (الحالة / المرحلة) | مجموعتا فلاتر بلا عنوان مرئي وكل منهما تبدأ بـ «الكل» — المشغّل لا يعرف ما الذي تصفّيه الصف الثاني | في الحسابات (الحالة ثم المرحلة)، الطلبات (الحالة ثم الطريقة)، المخالفات (القاعدة ثم «نعم/لا» بلا أي سياق)، الاحتيال (ثلاثة صفوف)، البريد (الحالة ثم القالب)، التدقيق (الموظف ثم الهدف). العنوان موجود في aria-label فقط. صف «الكل · لا · نعم» في المخالفات غير مفهوم إطلاقاً. | admin-admin-accounts.png; admin-admin-breaches.png; admin-admin-fraud.png; admin-admin-orders.png; apps/admin/src/components/Filters.tsx:60 | اعرض تسمية المجموعة (text-label) قبل الرقائق أو كأول رقيقة غير قابلة للنقر: «المرحلة:» «الطريقة:» «أُقفلت الصفقات:». التسمية موجودة أصلاً في g.label. | S | design-pro component-patterns Filtering (chips + labels); ui-ux-pro-max §8 field-grouping | `apps/admin/src/components/Filters.tsx` |
| VAD-020 | P2 ثانوي | بصري | أعمدة التاريخ (التسليم / الإنشاء / موعد المراجعة) | التاريخ الكامل «29 أكتوبر 2026، 11:49 بتوقيت دبي (GMT+4)» في كل صف يلتف على 3 أسطر ويخفض الكثافة إلى 6 صفوف/شاشة | الصيغة البرندية الكاملة مناسبة للتفاصيل، لكن في الجداول تكرر «بتوقيت دبي (GMT+4)» 27 مرة وترفع الصف إلى 86 px. TWO Markets يعرض في الجداول «29 أكت 11:49» والمنطقة مرة واحدة في رأس الجدول أو التذييل. | admin-admin-accounts.png; admin-admin-payouts.png; admin-admin-staff.png; apps/admin/src/components/AdminTime.tsx | أضف نمطاً «table» لـ AdminTime: يوم + شهر مختصر + ساعة على سطر واحد، والمنطقة الزمنية مرة واحدة في ذيل الجدول («كل الأوقات بتوقيت دبي (GMT+4)») مع التاريخ الكامل في title/tooltip. | S | design-pro «Precision & Density»; ui-ux-pro-max §6 number-tabular | `apps/admin/src/components/AdminTime.tsx`، `packages/ui/src/components/date-time.tsx` |
| VAD-030 | P2 ثانوي | بصري | رؤوس الجدول القابلة للفرز | كل رأس يعرض «↕» ولا يُظهر الفرز الفعّال (الأحدث أولاً) رغم ذكره في العنوان الفرعي | في الحسابات والطلبات والبريد يظهر الرمز المزدوج على كل الأعمدة بلا عمود نشط؛ في المخاطر والاحتيال والمدفوعات يظهر السهم النشط صحيحاً. الفرز الافتراضي غير مرئي في الرأس (aria-sort غائب). | admin-admin-accounts.png; admin-admin-orders.png; admin-admin-risk.png (نموذج صحيح); packages/ui/src/components/data-table.tsx:97-101 | مرّر sort={{key:'delivered',dir:'desc'}} (أو createdAt) افتراضياً لكل قائمة بحيث يظهر السهم النشط وaria-sort، وأخفِ «↕» من الأعمدة غير النشطة إلى التحويم. | S | ui-ux-pro-max §10 sortable-table; component-patterns Sorting | `apps/admin/src/app/[locale]/(app)/accounts/page.tsx`، `apps/admin/src/app/[locale]/(app)/orders/page.tsx`، `apps/admin/src/app/[locale]/(app)/mail/page.tsx` |
| VAD-036 | P3 صقل | هوية | حلقة التركيز (focus ring) | حلقة التركيز بالأحمر البرندي (--fpt-action-focus-ring: #F42821 PROPOSED) | التركيز بالكيبورد على عنصر الشريط الجانبي يرسم إطاراً أحمر 2 px؛ القيمة موسومة PROPOSED في الرموز. الأحمر محجوز للشعار والإجراء الأساسي والحالة النشطة، فالتركيز يشبه الحالة النشطة ويضلل (في اللقطة: الحسابات نشطة، والتحقق من الهوية مُركّز بالأحمر). | artifacts/ux-audit-F4/evidence/admin-accounts-focus-ar-desktop.png; brand/tokens.css:112 | حلقة تركيز بيضاء/فاتحة (text-primary) بتباين 3:1 مع الأرضية وofset 2 px؛ أبقِ الأحمر للحالة النشطة فقط. قرار برندي — PROPOSED. | S | ui-ux-pro-max §1 focus-appearance; fpt-brand red rule | `brand/tokens.css` |
| VAD-038 | P3 صقل | بصري | عمودا «الحالة» و«المرحلة» | عمودان يحملان القيمة نفسها «تقييم» في كل الصفوف تقريباً | الحالة شارة «تقييم» والمرحلة نص «تقييم»؛ المعلومة الإضافية الوحيدة تظهر للمشترى/الممول. | admin-admin-accounts.png | ادمجهما: شارة واحدة بالحالة، وأظهر المرحلة فقط حين تختلف (ممول/اجتياز) أو كعلامة فرعية. | S | design-pro critique «Every element earns its place» | `apps/admin/src/app/[locale]/(app)/accounts/page.tsx` |
| VAD-039 | P3 صقل | بصري | حد القائمة | القوائم محدودة بـ 200/300 سجل خادمياً بلا ترقيم ولا عدّاد إجمالي | «27 حساباً معروضاً (حتى 200)»، الطلبات 300، التدقيق 200، البريد 200؛ عند تجاوز الحد لا توجد صفحة تالية ولا يعرف المشغّل ما فاته. DataTableRows يوفر «المزيد» عميلياً فقط. | admin-admin-accounts.png; apps/admin/src/app/[locale]/(app)/accounts/page.tsx:38; orders/page.tsx:44; audit/page.tsx:39; mail/page.tsx:32 | ترقيم خادمي بمؤشر (cursor) + عدّاد إجمالي في العنوان «عرض 1–50 من 1,240». | M | component-patterns Pagination (>100 rows); WIG Navigation & State (URL reflects state) | `apps/admin/src/app/[locale]/(app)/accounts/page.tsx`، `packages/ui/src/components/data-table.tsx` |
| VAD-076 | P3 صقل | تقني | أدلة التدقيق F4 — لقطات «phone» | لقطات ux-audit-F4 الموسومة phone مأخوذة على 1280×720 بمقياس 2 لا على 390 px — لا تثبت تخطيط الهاتف | كل ملفات admin-*-phone.png بأبعاد 2560×1440 أو أطول وتعرض الشريط الجانبي المكتبي. لقطة admin-accounts-filter-empty لا تُظهر حالة فارغة (26 صفاً). حُكم الهاتف في هذا التقرير يستند إلى artifacts/ux-after (F1، قد تكون قديمة). | artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png (2560×1718); admin-accounts-filter-empty-ar-desktop.png | أعد التقاط الأدلة بـ viewport 390×844 وdeviceScaleFactor 3، ومرّر فلتراً يُرجع صفراً فعلياً (q=zzz) لتوثيق EmptyState. | S | process (evidence quality) | `scripts/ux-audit-capture.mjs` |
| VAD-086 | P3 صقل | وصول | المرجع في الصف | الصف بأكمله ليس رابطاً؛ الرابط على المرجع فقط (نص mono عريض بلا تسطير حتى التحويم) | DataTableRows يجعل الخلية الأولى رابطاً؛ لا إشارة بصرية لقابلية النقر، والنقر على اسم العميل أو المبلغ لا يفتح الحساب. | admin-admin-accounts.png; packages/ui/src/components/data-table.tsx:119-121 | اجعل الصف قابلاً للنقر (رابط ممتد ::after inset-0 على الخلية الأولى) مع cursor:pointer وحالة تحويم محايدة؛ أبقِ الرابط الدلالي على المرجع. | S | ui-ux-pro-max §2 cursor-pointer; WIG Hover & Interactive States | `packages/ui/src/components/data-table.tsx`، `packages/ui/src/components.css` |

### الإدارة — admin-accounts

اللقطات: `windows-11-chrome: artifacts/real-device/coverage-20261030/windows-11-chrome/admin-admin-accounts.png`، `macos-safari: artifacts/real-device/coverage-20261030/macos-safari/admin-admin-accounts.png`، `ipad-pro-13: artifacts/real-device/coverage-20261030/ipad-pro-13/admin-admin-accounts.png`، `local-chromium-1440: artifacts/real-device/coverage-20261030/local-chromium-1440/admin-admin-accounts.png`، `local-webkit-1440: artifacts/real-device/coverage-20261030/local-webkit-1440/admin-admin-accounts.png`، `local-firefox-1440: artifacts/real-device/coverage-20261030/local-firefox-1440/admin-admin-accounts.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-filter-empty-ar-phone.png` … (+10)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-013 | P2 ثانوي | وصول | عمود «أقرب حدّ» nearestCell: text-danger / text-drawdown | خطر الاقتراب من الحدّ يُنقل باللون وحده في جدول الحسابات | nearestCell يلوّن النسبة (≤ 0 خطر، < 10 % drawdown) دون كلمة أو أيقونة أو إشارة، خلافاً لقاعدة الهوية «كلمة + إشارة + لون» ولـ WCAG 1.4.1. المستخدم المصاب بعمى الألوان وقارئ الشاشة يقرآن «5.3 %» فقط. | apps/admin/src/app/[locale]/(app)/accounts/page.tsx:42-45; artifacts/ux-audit-F4/evidence/admin-accounts-focus-ar-desktop.png | أضف كلمة قصيرة («قريب» / «مخترق») أو أيقونة TriangleAlert مع aria-hidden ونص sr-only، أو StatusPill للحالات الحرجة. | S | ui-ux-pro-max `color-not-only`; brand/README (finance states word + sign + colour) | `apps/admin/src/app/[locale]/(app)/accounts/page.tsx` |

### الإدارة — admin-audit

اللقطات: `windows-11-chrome: artifacts/real-device/coverage-20261030/windows-11-chrome/admin-admin-audit.png`، `macos-safari: artifacts/real-device/coverage-20261030/macos-safari/admin-admin-audit.png`، `ipad-pro-13: artifacts/real-device/coverage-20261030/ipad-pro-13/admin-admin-audit.png`، `local-chromium-1440: artifacts/real-device/coverage-20261030/local-chromium-1440/admin-admin-audit.png`، `local-webkit-1440: artifacts/real-device/coverage-20261030/local-webkit-1440/admin-admin-audit.png`، `local-firefox-1440: artifacts/real-device/coverage-20261030/local-firefox-1440/admin-admin-audit.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-desktop.png` … (+2)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| DEV-001 | P1 رئيسي | تقني | صفحة سجل التدقيق /ar/audit — EventFeed بالأزمنة النسبية | خطأ ترطيب React #418 على كروم ويندوز الحقيقي في سجل التدقيق | على Windows 11 + Chrome الحقيقي (BrowserStack) تسجّل صفحة سجل التدقيق بالعربية خطأ console «Minified React error #418» (نص الخادم لا يطابق نص العميل عند الترطيب). لم يظهر على Chromium المحلي ولا على macOS Safari ولا على الآيباد؛ الصفحة تمرّر now={Date.now()} إلى EventFeed وتُنسّق أزمنة نسبية بالعربية، والاختلاف بين ICU في Node وICU في كروم ويندوز هو السبب الأرجح (يحتاج إثباتاً). | artifacts/real-device/coverage-20261030/windows-11-chrome/coverage-receipt.json → screens['admin/admin-audit@ar'].detail.consoleErrors؛ apps/admin/src/app/[locale]/(app)/audit/page.tsx:73 | إما تثبيت نص الزمن النسبي من الخادم (تمرير النص المنسّق لا now)، أو جعل الجزء النسبي client-only مع suppressHydrationWarning على العنصر النصي فقط، ثم إعادة الفحص على كروم ويندوز الحقيقي. | S | web-interface-guidelines § Hydration Safety · security-audit (guidance) · RD1 real-device coverage | `apps/admin/src/app/[locale]/(app)/audit/page.tsx`، `packages/ui/src/components/event-feed.tsx` |

### الإدارة — admin-overview 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-001 | P1 رئيسي | وصول | AdminShell → BottomNav «المزيد» (MoreAwareLink button) | زر «المزيد» في شريط التبويب الإداري لا يفتح لوحة الأقسام أبداً (لوحة المفاتيح والمؤشر معاً) | في AdminShell يُنشأ زر «المزيد» بـ onClick={() => setMoreOpen(true)} ثم يُنشر {...rest} بعده، و BottomNav يمرّر onClick={dropClickFocus} ضمن rest فيستبدل المعالج الأصلي. قياس Playwright على 390×844: Enter → dialog=0، Space → dialog=0، نقرة مؤشر → dialog=0 والتركيز يسقط إلى body. على الهاتف لا يصل أي مستخدم (لوحة مفاتيح أو لمس) إلى 15 قسماً إدارياً (التجارة، التحكم، حسابي) إلا عبر الرابط المباشر. | .e2e/a11y-admin.log «phone:moreSheet» (sheet:null, gone:true) + .e2e/a11y-quick.ts «admin more via Enter/Space/click» dialog:0; apps/admin/src/components/shell/AdminShell.tsx:69-76 و packages/ui/src/components/bottom-nav.tsx:518 | في MoreAwareLink اقرأ onClick من rest وادمجه: onClick={(e) => { (rest.onClick as any)?.(e); setMoreOpen(true); }} أو اجعل BottomNav يستدعي onClick الممرَّر من العنصر قبل dropClickFocus. أضف اختبار e2e يفتح اللوحة بـ Enter على 390. | S | ui-ux-pro-max §1 keyboard-nav / §9 Navigation Patterns; WIG «Interactive elements need keyboard handlers» | `apps/admin/src/components/shell/AdminShell.tsx`، `packages/ui/src/components/bottom-nav.tsx` |

### الإدارة — admin-products, admin-coupons

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-017 | P3 صقل | وصول | th:nth-child(9) / th:nth-child(8) (عمود الإجراءات) | رأس عمود الإجراءات فارغ (axe empty-table-header) | axe empty-table-header (minor) على 1440: رأس عمود الأزرار بلا نص فيُقرأ العمود بلا اسم. | gates-desktop-1440.json admin/admin-products th:nth-child(9), admin/admin-coupons th:nth-child(8) | ضع <span className="sr-only">الإجراءات</span> في الرأس. | S | WIG «Use semantic HTML (<table>)» | `apps/admin/src/app/[locale]/(app)/products/ProductsClient.tsx`، `apps/admin/src/app/[locale]/(app)/coupons/CouponsClient.tsx` |

### الإدارة — admin-signin, admin-forbidden (+ صفحة «لا صلاحية» داخل الأقسام)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-004 | P2 ثانوي | وصول | CardTitle as="h2" / EmptyState h3 | صفحة دخول الموظفين وصفحة المنع بلا h1 | axe page-has-heading-one على admin-signin و admin-forbidden (4 مشاريع). القياس: signin hs=[«h2: دخول الموظفين»]; /ar/settings بدور approver → hs=[«h3: لا صلاحية لهذه الصفحة»] فقط. | gates-*.json admin/admin-signin, admin/admin-forbidden; .e2e/a11y-admin.log desktop:signin:outline, desktop:/ar/settings:outline; apps/admin/src/app/[locale]/(auth)/signin/page.tsx:15, apps/admin/src/app/[locale]/(app)/forbidden/page.tsx | CardTitle as="h1" في صفحة الدخول؛ أضف خاصية headingLevel لـ EmptyState واستخدم h1 حين يكون هو محتوى الصفحة الوحيد. | S | ui-ux-pro-max `heading-hierarchy` | `apps/admin/src/app/[locale]/(auth)/signin/page.tsx`، `apps/admin/src/app/[locale]/(app)/forbidden/page.tsx`، `packages/ui/src/components/empty-state.tsx` |

### الإدارة — admin-signin

اللقطات: `windows-11-chrome: artifacts/real-device/coverage-20261030/windows-11-chrome/admin-admin-signin.png`، `macos-safari: artifacts/real-device/coverage-20261030/macos-safari/admin-admin-signin.png`، `ipad-pro-13: artifacts/real-device/coverage-20261030/ipad-pro-13/admin-admin-signin.png`، `local-chromium-1440: artifacts/real-device/coverage-20261030/local-chromium-1440/admin-admin-signin.png`، `local-webkit-1440: artifacts/real-device/coverage-20261030/local-webkit-1440/admin-admin-signin.png`، `local-firefox-1440: artifacts/real-device/coverage-20261030/local-firefox-1440/admin-admin-signin.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png` … (+6)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-039 | P3 صقل | وصول | Input required بلا noValidate → فقاعة التحقق الأصلية فقط | إرسال نموذج دخول الموظفين فارغاً لا يُنتج أي رسالة خطأ في DOM (تحقق المتصفح الأصلي فقط) | القياس: alerts=[]، aria-invalid=[]، التركيز على email (المتصفح). الفقاعة الأصلية مؤقتة وغير مترجمة في بعض المتصفحات ولا تظهر على iOS إلا كحدّ أحمر. | .e2e/a11y-admin.log desktop/phone:signin:emptyError | noValidate + رسائل Field مترجمة كما في العميل، أو الإبقاء على required مع رسائل مخصّصة عبر setCustomValidity. | S | WIG Forms «Errors inline next to fields» | `apps/admin/src/app/[locale]/(auth)/signin/SignInForm.tsx` |
| DEV-003 | P3 صقل | تقني | حدّ محاولات الدخول لكل حساب موظف | حدّ «محاولات كثيرة» يُفعَّل بدخول متوازٍ لحساب واحد من أجهزة مختلفة | عند تشغيل ثلاثة أجهزة بالتوازي بحساب الفحص نفسه ظهرت رسالة «محاولات كثيرة. انتظر دقيقة ثم أعد المحاولة.» على ماك/سفاري والآيباد رغم أن كل محاولة صحيحة. سلوك أمني صحيح (ليس عيباً)، لكنه يعني أن أي أتمتة متوازية أو فريقاً يتشارك حساباً سيُحجب؛ يُوثَّق هنا وفي docs/real-device-gate.md. | artifacts/real-device/coverage-20261030/macos-safari/coverage-receipt.json → sessions[].error «Too many attempts…»؛ apps/admin/src/lib/session.ts (ADMIN_LIMITS) | لا تغيير في المنتج؛ في الأتمتة: دخول واحد لكل حساب في الدقيقة، أو حساب فحص لكل مسار. اختيارياً: رسالة الحدّ تذكر المدة المتبقية. | S | security-audit (guidance) · RD1 real-device coverage | `docs/real-device-gate.md` |

### الإدارة — affiliate

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-044 | P3 صقل | نص | سطر الفئة | «الفئة 1 · 15 % · 15 %» — النسبة مكررة | t('tier.t1') يحوي النسبة ثم تُلحق النسبة مرة ثانية في الصفحة. | admin-admin-affiliate.png; apps/admin/src/app/[locale]/(app)/affiliates/[id]/page.tsx:134; packages/i18n/src/messages/ar.json:2574 | اجعل مفاتيح tier.* اسم الفئة فقط وألحق النسبة برمجياً. | S | Nielsen #8 minimalist | `apps/admin/src/app/[locale]/(app)/affiliates/[id]/page.tsx`، `packages/i18n/src/messages/ar.json` |
| VAD-047 | P3 صقل | تقني | «قناة الدفع» | رمز القناة الخام «crypto» ورمز الدولة «AE» في مناطق غير خام | «TSeedDemo02 · crypto» و«الدولة: AE» في تفاصيل الاحتيال؛ الرموز لا تُترجم. | admin-admin-affiliate.png; admin-admin-fraud-detail.png; apps/admin/src/app/[locale]/(app)/fraud/[id]/page.tsx:90 | ترجمة القنوات (عملة رقمية/تحويل بنكي) وأسماء الدول عبر Intl.DisplayNames(locale,{type:'region'}). | S | fpt-brand technical leaks rule; WIG Locale & i18n | `apps/admin/src/app/[locale]/(app)/affiliates/[id]/page.tsx`، `apps/admin/src/app/[locale]/(app)/fraud/[id]/page.tsx` |

### الإدارة — affiliates

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-045 | P3 صقل | RTL | العنوان الفرعي | «محجوزة → مستحقة» و«دفعة أسبوعية من USD 50» — سهم باتجاه خاطئ والرمز قبل الرقم | السهم → يشير يميناً في تدفق RTL، والمبلغ يظهر «USD 50» بينما القاعدة في العربية «50 USD» (المصدر يعزله ⁦50 USD⁩ لكن العرض يقلبه داخل الجملة). | admin-admin-affiliates.png; packages/i18n/src/messages/ar.json:2556 | استخدم «ثم» أو ← في النص العربي؛ المبالغ عبر مكوّن Money لا نصاً مضمّناً. | S | fpt-brand: amounts as unbreakable units, code after digits in Arabic | `packages/i18n/src/messages/ar.json`، `apps/admin/src/app/[locale]/(app)/affiliates/page.tsx` |
| VAD-046 | P3 صقل | بصري | عمود «الفئة» | «الفئة 1 · 15 %» يلتف على ثلاثة أسطر في عمود ضيق | عشرة أعمدة في جدول صفّين؛ عمود الفئة أضيق من محتواه. | admin-admin-affiliates.png | عرض أدنى للعمود أو شارة «ف1 · 15 %» أو دمج الفئة في سطر فرعي تحت الاسم. | S | ui-ux-pro-max §5 compact-label-overflow | `apps/admin/src/app/[locale]/(app)/affiliates/page.tsx` |

### الإدارة — audit

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-desktop.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-en-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-en-desktop.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-016 | P2 ثانوي | نص | عنوان السجل | إجراءات بلا ترجمة تظهر خاماً: «النظام — order payment started» | actionWord يعود إلى اسم الإجراء الخام مع استبدال النقاط بمسافات عندما يغيب المفتاح actions.*. ظهر «order payment started» بين عناوين عربية. سجل التدقيق هو مرجع المراجعة؛ لا يجوز أن تكون عناوينه نصف مترجمة. | admin-admin-audit.png; apps/admin/src/app/[locale]/(app)/audit/page.tsx:43 | أكمل مفاتيح actions.* لكل إجراء مسجّل (اختبار يجمع الإجراءات من الكود ويتحقق من وجود الترجمة) وأبقِ الاسم الخام داخل «التفاصيل التقنية» فقط. | S | fpt-brand text rules; Nielsen #2 Match the real world | `apps/admin/src/app/[locale]/(app)/audit/page.tsx`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| VAD-017 | P2 ثانوي | بصري | قائمة السجلات (Feed) | سجل التدقيق كتغذية أحداث بارتفاع ~125 px للعنصر — 4 سجلات في الشاشة، أوقات نسبية فقط، لا نطاق تاريخ | كل سجل يشغل ثلاثة أسطر (عنوان، «الآن · الهدف: جلسة»، «التفاصيل التقنية») مع فراغ كبير، والزمن «الآن» لكل العناصر بلا طابع دقيق، ولا فلتر تاريخ ولا ترقيم (حد 200). المراجع يحتاج كثافة جدول: الوقت الدقيق \| الموظف \| الإجراء \| الهدف \| السبب. | admin-admin-audit.png; artifacts/ux-audit-F4/evidence/admin-audit-ar-desktop.png (9678 px لصفحة واحدة); apps/admin/src/app/[locale]/(app)/audit/page.tsx:39 | استخدم DataTableRows بصفوف 48 px مع عمود وقت مطلق (دبي) وعمود موظف وهدف وسبب، وصفّ قابل للتوسيع للتفاصيل التقنية؛ أضف فلتر «من/إلى» وترقيم خادمي. | M | design-pro «Precision & Density» direction; component-patterns Data Tables pagination | `apps/admin/src/app/[locale]/(app)/audit/page.tsx`، `apps/admin/src/components/Feed.tsx` |

### الإدارة — breach

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-003 | P1 رئيسي | بصري | منحنى حقوق الحساب ±30 دقيقة (EquityCurve) | نصوص محور الرسم ضخمة (~34 px) وخط المنحنى سميك — الـ SVG يتمدد مع viewBox | EquityCurve يرسم على viewBox 640×220 بخط 11 px ثم يعرض بعرض 100% (~1140 px) وارتفاع تلقائي، فتتضخم تسميات المحور «100.0k / 98.0k» ورقم القيمة الأخيرة «94,032.19» حتى تصبح أكبر من عنوان الصفحة، ويتضاعف سمك الخط. نفس المظهر في Chromium وWebKit وFirefox، فهو خلل تصميم لا متصفح. يعطي انطباعاً غير مهني في صفحة الأدلة التي يُفترض أن تُراجَع وقد تُصدَّر. | admin-admin-breach.png (الثلاثة محركات: local-chromium-1440, local-webkit-1440, local-firefox-1440); apps/admin/src/components/EquityCurve.tsx:26-28,56; brand/components/components.css:328 | احسب W من عرض الحاوية (ResizeObserver) أو ثبّت ارتفاع الرسم بالبكسل مع preserveAspectRatio="none" وأضف vector-effect=non-scaling-stroke، واجعل نصوص المحور HTML خارج الـ SVG أو بحجم 12 px ثابت. قارن بمكوّن EquityChart في @fpt/ui الذي يحدد width/height. | M | ui-ux-pro-max §10 axis-readability, contrast-data, trend-emphasis | `apps/admin/src/components/EquityCurve.tsx`، `packages/ui/src/components/equity-chart.tsx` |
| VAD-057 | P3 صقل | بصري | عنوان الصفحة | H1 طويل يضم التاريخ الكامل والمنطقة الزمنية | «أُوقف الحساب لتجاوز حدّ الخسارة اليومية — 29 أكتوبر 2026، 08:06 بتوقيت دبي (GMT+4)» كعنوان رئيسي؛ على الهاتف 3 أسطر. | admin-admin-breach.png; artifacts/ux-after/admin-breach-phone-390-ar.png | H1 = «مخالفة الخسارة اليومية · FPT-100K-VFBUE»، والتاريخ في سطر التعريف. | S | ui-ux-pro-max §6 heading-line-balance | `apps/admin/src/app/[locale]/(app)/breaches/[id]/page.tsx` |

### الإدارة — breaches

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-055 | P3 صقل | بصري | شارة «الإقفال» | شارة «تم» بأيقونة X بلون الخطر لعملية إقفال ناجحة — تناقض دلالي | في قائمة المخالفات «تم» + دائرة X، وفي التفاصيل «أُقفلت الصفقات» بنفس النبرة الحمراء-البرتقالية. الإقفال المنجز حالة مكتملة لا خطر. | admin-admin-breaches.png; admin-admin-breach.png | نبرة محايدة/نجاح مع أيقونة قفل أو ✓ وكلمة «أُقفلت»؛ احتفظ بالخطر لـ «لم تُقفل». | S | ui-ux-pro-max §6 color-not-decorative-only; Nielsen #4 | `apps/admin/src/app/[locale]/(app)/breaches/page.tsx`، `apps/admin/src/components/StatusPills.tsx` |
| VAD-056 | P3 صقل | بصري | عمود «الحدّ USD» | «0.00» كحدّ لمخالفات انتهاء المدة وعدم النشاط | مخالفات غير مالية تعرض حداً صفرياً بدل «—». | admin-admin-breaches.png | اعرض «—» عندما لا ينطبق الحد المالي. | S | WIG Content Handling (empty states per cell) | `apps/admin/src/app/[locale]/(app)/breaches/page.tsx` |
| VAD-074 | P3 صقل | نص | عنوان الصفحة مقابل التنقل | عناوين الصفحات تخالف أسماء الشريط الجانبي: «المخالفات والأدلة» / «المخالفات»، «الطلبات والمدفوعات» / «الطلبات»، «مدفوعات بانتظار الاعتماد» / «المدفوعات اليدوية»، «التحكم بالمحاكي» / «المحاكي» | الشريط العلوي يعرض اسم التنقل والـ H1 اسماً آخر في الصفحة نفسها. | admin-admin-breaches.png; admin-admin-orders.png; admin-admin-payments.png; admin-admin-simulator.png | اسم واحد لكل وجهة في nav/H1/document.title. | S | Nielsen #4 Consistency; ui-ux-pro-max §9 navigation-consistency | `packages/i18n/src/messages/ar.json`، `apps/admin/src/app/[locale]/(app)/layout.tsx` |

### الإدارة — coupons

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-062 | P3 صقل | بصري | حالة الفراغ | حالة فارغة تحيل إلى «الزر أعلاه» بدل أن تضم الإجراء | «أنشئ أول كوبون بالزر أعلاه» والزر في الزاوية المقابلة. | admin-admin-coupons.png | ضع زر «كوبون جديد» داخل حالة الفراغ (ثانوي) وأبقِ الأساسي أعلى. | S | ui-ux-pro-max §8 empty-states; component-patterns Empty States | `apps/admin/src/app/[locale]/(app)/coupons/CouponsClient.tsx` |

### الإدارة — deletions

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-081 | P3 صقل | بصري | الفلاتر | رقيقة «الكل» وحيدة في صفحة طلبات الحذف — فلتر بلا خيارات | صف فلاتر بعنصر واحد غير قابل للتغيير. | admin-admin-deletions.png | أخفِ المجموعة عندما تكون الخيارات أقل من اثنين، أو أضف «معلّقة/منفّذة/مرفوضة». | S | Nielsen #8 | `apps/admin/src/app/[locale]/(app)/deletions/page.tsx`، `apps/admin/src/components/Filters.tsx` |

### الإدارة — finance

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-012 | P2 ثانوي | نص | سطور العدّ في عناوين القوائم والملاحظات | صيغ الجمع العربية ثابتة: «4 مخالفة معروضة»، «5 تقييماً منتهياً»، «1 سحباً»، «1 جهازاً»، «2 طلباً» | النصوص تستخدم تمييز العدد لـ 11+ دائماً ({n} حساباً معروضاً) بلا قواعد one/two/few في ICU. تظهر أخطاء لغوية واضحة في: المخالفات (4)، المالية (1 ناجح من 5 تقييماً، 1 سحباً)، المدفوعات اليدوية (2 طلباً)، روابط العميل (1 جهازاً)، الكوبونات (0 كوبونات). next-intl يدعم {n, plural, one{} two{} few{} many{} other{}}. | admin-admin-breaches.png; admin-admin-finance.png; admin-admin-payments.png; admin-admin-fraud-links.png; packages/i18n/src/messages/ar.json:1941,2191,2254,2494,2518,2533 | حوّل كل سلسلة عدّ إلى ICU plural بالفئات العربية الست، وأضف اختباراً في @fpt/i18n يرفض {n} متبوعة بتمييز منصوب بلا plural. | M | WIG Content & Copy (numerals); ui-ux-pro-max §10 number-formatting (locale-aware) | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/index.ts` |
| VAD-031 | P2 ثانوي | نص | بطاقة «المبالغ المستردة» والملاحظة أسفل البطاقات | نص «تعتمد FPT سياسة عدم الاسترداد … تُعرض صراحةً حتى لا يُغفل الرقم» حقيقة منتج تحتاج صياغة من المعجم | المعجم يوثّق «رسوم لمرة واحدة · غير قابلة للاسترداد» (OBSERVED) ولا يوثّق «سياسة عدم الاسترداد» كمصطلح؛ الجملة التفسيرية داخلية ومطوّلة في لوحة مالية. | admin-admin-finance.png; brand/glossary.md:50,59; packages/i18n/src/messages/ar.json:294 | استخدم صياغة المعجم «الرسوم غير قابلة للاسترداد» واختصر الملاحظة إلى «المبالغ المستردة 0 بالتصميم» ووسمها PROPOSED إن لم تكن في value-sources. | S | fpt-brand: product facts verbatim from glossary § 3 | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| VAD-058 | P3 صقل | بصري | بطاقة «إيراد هذا الشهر» | خط اتجاه بنقطتين (خط مستقيم) لا يحمل معلومة | الشرارة ترسم شهرين فقط؛ تبدو كخطأ رسم. | admin-admin-finance.png | أخفِ الشرارة حتى تتوفر ≥ 4 نقاط، أو اعرض الفرق عن الشهر السابق كنص «+8,430 USD». | S | ui-ux-pro-max §10 empty-data-state, direct-labeling | `apps/admin/src/app/[locale]/(app)/finance/page.tsx`، `apps/admin/src/components/Finance.tsx` |

### الإدارة — forbidden

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-desktop.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-en-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-en-desktop.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-073 | P3 صقل | بصري | الشريط العلوي | شريط علوي فارغ بلا عنوان في صفحة «لا صلاحية» | current غير محدد لمسار /forbidden فيُترك مركز الشريط فارغاً. | admin-admin-forbidden.png | عنوان «لا صلاحية» في الشريط أو إخفاء الشريط على المكتب (VAD-037). | S | Nielsen #1 | `apps/admin/src/components/shell/AdminShell.tsx` |

### الإدارة — fraud

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-007 | P2 ثانوي | RTL | رقائق «الخطورة ≥ 40 / ≥ 70» | علامة ≥ تنعكس في العربية فتُقرأ «الخطورة ≤ 40» — عكس المعنى | المصدر يكتب «≥ 40» لكن U+2265 حرف مرآوي في Bidi، فيظهر في السياق العربي كـ «≤ 40». المشغّل يقرأ «أقل من أو يساوي 40» بينما الفلتر يُرجع 40 فأكثر. تأكد بتكبير اللقطة. | admin-admin-fraud.png (مكبّر: «الخطورة ≤ 40»); apps/admin/src/app/[locale]/(app)/fraud/page.tsx:60 | استخدم صياغة لفظية: «الخطورة 40 فأكثر» / «70 فأكثر»، أو لفّ الرمز والرقم في <bdi dir="ltr">≥ 40</bdi>. | S | ui-ux-pro-max §6 long-token-wrapping/bidi; fpt-brand RTL rules | `apps/admin/src/app/[locale]/(app)/fraud/page.tsx`، `packages/i18n/src/messages/ar.json` |

### الإدارة — fraud-detail

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-005 | P1 رئيسي | بصري | بطاقة «الأدلة» | بطاقة الأدلة فارغة إلا من سطر مطوي «الأدلة ⌄» وتمتد على كامل ارتفاع العمود | العمود الأيسر (ثلثا العرض) بطاقة عنوانها «الأدلة» ومحتواها الوحيد JsonViewer مطوي. الإشارة الاحتيالية مبنية على أدلة؛ المشغّل يفتح الصفحة ليقرر «تعليم كمراجَعة/رفض» ولا يرى شيئاً حتى يفتح JSON خاماً. فراغ أسود بارتفاع ~700 px يبدو كخطأ تحميل. | admin-admin-fraud-detail.png; apps/admin/src/app/[locale]/(app)/fraud/[id]/page.tsx:143 | اعرض الأدلة كقائمة مقروءة (الجهاز/البصمة المشتركة، العملاء المطابقون، التوقيتات) مع JsonViewer «البيانات الخام» تحتها مفتوحاً افتراضياً أو مطوياً لكن بعد ملخص. لا تمدّ البطاقة بارتفاع العمود المجاور. | M | design-pro critique «Is there a focal point?»; ui-ux-pro-max §8 empty-states | `apps/admin/src/app/[locale]/(app)/fraud/[id]/page.tsx` |

### الإدارة — fraud-links

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-048 | P3 صقل | نص | إحصاءات مدة الاحتفاظ | وحدات إنجليزية «1.0 h» و«240 min» في واجهة عربية | fmtDur يطبع h/min/s، والإعدادات تعرض «الفرق عن UTC: 240 min». | admin-admin-fraud-links.png; admin-admin-settings.png; apps/admin/src/app/[locale]/(app)/fraud/links/[userId]/page.tsx:73 | Intl.NumberFormat بوحدة (unit:'hour', unitDisplay:'short') أو مفاتيح عربية «ساعة/دقيقة/ثانية»؛ الفرق عن UTC كـ «+4 ساعات». | S | WIG Locale & i18n (Intl.NumberFormat) | `apps/admin/src/app/[locale]/(app)/fraud/links/[userId]/page.tsx`، `apps/admin/src/app/[locale]/(app)/settings/page.tsx` |
| VAD-070 | P3 صقل | بصري | معرّف الجهاز المشترك | معرّف مبتور «seed-device-shar» بلا علامة حذف | القيمة تُقص بعرض ثابت. | admin-admin-fraud-links.png | text-overflow:ellipsis + title، أو اعرض آخر 8 خانات مع زر نسخ. | S | ui-ux-pro-max §6 truncation-strategy | `apps/admin/src/app/[locale]/(app)/fraud/links/[userId]/page.tsx` |

### الإدارة — grant

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-063 | P3 صقل | نص | حقل «المنتج» | قائمة المنتج فارغة بلا نص إرشادي «اختر منتجاً…» | Select بلا placeholder؛ المشغّل لا يعرف أن الحقل فارغ أم معطّل. | admin-admin-grant.png; local-firefox-1440/admin-admin-grant.png | placeholder «اختر منتجاً…» واعرض الحجم والسعر في كل خيار. | S | ui-ux-pro-max §8 input-helper-text; WIG Forms placeholders | `apps/admin/src/app/[locale]/(app)/orders/grant/page.tsx` |
| VAD-084 | P3 صقل | بصري | زر الإرسال | زر «منح الحساب» الأساسي في الزاوية السفلية اليسرى بينما تبدأ البطاقة من اليمين وتُقرأ منه | الزر الأساسي في نهاية الاتجاه (يسار) أسفل حقل إعادة التحقق؛ مقبول إن كان النمط ثابتاً لكن صفحة الكوبونات/الطلبات تضع الأساسي أعلى اليسار وصفحات التفاصيل تضعه أعلى. ثلاثة مواضع للإجراء الأساسي. | admin-admin-grant.png; admin-admin-orders.png; admin-admin-payout.png | قاعدة واحدة: نماذج → الزر أسفل البداية (يمين في العربية) أو شريط ثابت؛ قوائم/تفاصيل → أعلى نهاية الاتجاه. | S | ui-ux-pro-max §9 navigation-consistency; §4 primary-action | `apps/admin/src/app/[locale]/(app)/orders/grant/page.tsx`، `apps/admin/src/components/ActionForm.tsx` |

### الإدارة — health

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-060 | P3 صقل | بصري | عدّاد الأقسام («الخدمات 12»، «آخر التنبيهات 0»، «الجلسات 15») | عدّاد القسم في الطرف المقابل من العنوان (1,100 px بعيداً) فيفقد ارتباطه | نمط SectionHeader يضع العدد في نهاية الصف؛ في الأعمدة العريضة يصبح رقماً معزولاً. | admin-admin-health.png; admin-admin-me.png; admin-admin-simulator.png | ألصق العدّاد بالعنوان كشارة «الخدمات · 12». | S | design-pro critique «grouping / proximity» | `packages/ui/src/components/page-header.tsx`، `apps/admin/src/app/[locale]/(app)/health/HealthClient.tsx` |
| VAD-061 | P3 صقل | بصري | الزر الأساسي | «إرسال تنبيه تجريبي» هو الإجراء الأساسي الأحمر الوحيد في صفحة مراقبة | إجراء اختباري نادر يحمل أعلى وزن بصري في الصفحة. | admin-admin-health.png | زر ثانوي بجانب قسم «آخر التنبيهات»؛ احجز الأحمر لإجراء تشغيلي (مثلاً إيقاف الطابور) أو لا شيء. | S | ui-ux-pro-max §4 primary-action | `apps/admin/src/app/[locale]/(app)/health/HealthClient.tsx` |
| VAD-082 | P3 صقل | نص | قسم «ساعة المحاكي» | «السرعة: الزمن الحقيقي ×1» و«وقت المحاكي» في صفحة صحة الإنتاج بلا تمييز أنها محاكاة | عناصر المحاكي تظهر في صحة النظام بنفس وزن الخدمات الحقيقية؛ عند التحول إلى cTrader الحي يجب أن تختفي أو تُوسم. | admin-admin-health.png | اعرض القسم فقط عندما FPT_PLATFORM_MODE=sim مع شارة «محاكاة»، وإلا «ساعة المنصة». | S | Nielsen #1 visibility of system status | `apps/admin/src/app/[locale]/(app)/health/HealthClient.tsx` |

### الإدارة — integrations

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-068 | P3 صقل | بصري | خطوات الربط | جدار نص تشغيلي بلا ترقيم ولا أزرار نسخ لأسماء المتغيرات | ثلاث فقرات طويلة لكل مزوّد مع رموز متداخلة ثنائية الاتجاه؛ الصفحة runbook لكنها غير قابلة للمسح. | admin-admin-integrations.png | قائمة مرقمة، كل اسم متغير في <code> مع زر نسخ (CopyBadge موجود)، و«نقطة التراجع» كصندوق منفصل. | S | design-pro «Utility & Function»; Nielsen #10 | `apps/admin/src/app/[locale]/(app)/integrations/page.tsx` |

### الإدارة — kyc

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-077 | P3 صقل | بصري | الفلاتر | طابور التحقق بلا رقيقة «موافَق عليها» ولا نطاق زمني — لا يمكن مراجعة قرار سابق | الرقائق: الكل / قيد التحقق / مرفوضة فقط؛ الحالات المعتمدة لا تُعرض في أي مكان من الطابور. | admin-admin-kyc.png | أضف «موثّقة» كرقيقة أرشيف مع فرز بتاريخ القرار والمراجع. | S | Nielsen #6 Recognition over recall | `apps/admin/src/app/[locale]/(app)/kyc/page.tsx` |

### الإدارة — mail

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-mail-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-mail-detail-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-mail-ar-desktop.png`، `capture: artifacts/ux-audit-F4/evidence/admin-mail-detail-ar-desktop.png`، `capture: artifacts/ux-audit-F4/evidence/admin-mail-en-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-mail-detail-en-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-mail-en-desktop.png`، `capture: artifacts/ux-audit-F4/evidence/admin-mail-detail-en-desktop.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-008 | P2 ثانوي | RTL | عمود «الموضوع» للرسائل الإنجليزية | الموضوع الإنجليزي يتفكك: «5.60 USD :New affiliate commission» | الصفحة تعزل المبلغ فقط (<bdi class=fpt-unit>) ويبقى باقي الجملة الإنجليزية في فقرة RTL، فتقفز النقطتان والمبلغ إلى البداية. في عرض الهاتف المكدّس يُعرض الموضوع صحيحاً، أي أن الخلل في خلية الجدول فقط. | admin-admin-mail.png; artifacts/ux-audit-F4/evidence/admin-mail-ar-desktop.png; artifacts/ux-after/admin-mail-phone-390-ar.png (صحيح); apps/admin/src/app/[locale]/(app)/mail/page.tsx:36-40 | لفّ الموضوع كاملاً في <bdi dir="auto"> (أو dir حسب لغة الرسالة r.locale) بدل عزل المبلغ وحده. | S | WIG Locale & i18n; ui-ux-pro-max §6 long-token-wrapping | `apps/admin/src/app/[locale]/(app)/mail/page.tsx` |
| VAD-024 | P2 ثانوي | نص | رقائق القالب وعمود الموضوع | اسم القالب «manual proof received» و«manual payment confirmed» بالإنجليزية داخل الواجهة العربية | رقيقة الفلتر وسطر «القالب» في التفاصيل يعرضان اسم القالب الخام للقوالب الجديدة بينما «الحساب جاهز» و«عمولة شريك» مترجمان — مفاتيح ناقصة في admin.mail.templates. | admin-admin-mail.png; admin-admin-mail-detail.png; artifacts/ux-audit-F4/evidence/admin-mail-ar-desktop.png | أضف ترجمة لكل قالب في packages/adapters/src/mail/templates/index.ts إلى ar/en (اختبار يطابق قائمة القوالب بالمفاتيح). | S | fpt-brand text rules; Nielsen #4 Consistency | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `apps/admin/src/app/[locale]/(app)/mail/page.tsx` |

### الإدارة — mail-detail

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-mail-detail-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-mail-detail-ar-desktop.png`، `capture: artifacts/ux-audit-F4/evidence/admin-mail-detail-en-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-mail-detail-en-desktop.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-006 | P1 رئيسي | هوية | تذييل البريد داخل إطار HTML | سطر «Regulatory text: pending decision · النص التنظيمي: بانتظار قرار» ظاهر في بريد العميل | قالب البريد يطبع عنصر نائب تنظيمي مرئياً في تذييل كل رسالة (إطار بريد «استلمنا إثبات تحويلك»). في وضع المحاكاة لا يخرج شيء، لكن نفس القالب هو ما سيُرسل حين يتحول وضع البريد إلى live. نص «بانتظار قرار» أمام العميل غير مقبول مهنياً، والقاعدة البرندية تمنع اختراع نص تنظيمي لا حذفه إلى حين القرار. | admin-admin-mail-detail.png (الثلاثة محركات); packages/adapters/src/mail/templates/layout.ts:191-192,292 | أبقِ التعليق HTML للمطورين واحذف السطر المرئي حتى يُعتمد النص (أو أضف بوابة إطلاق: FPT_MAIL_MODE=live يفشل ما دام mail.common.regulatoryPending مستخدماً). أدرج القرار المطلوب في brand/regulatory-matrix.md. | S | fpt-brand: regulatory-matrix (no invented text); WIG Content & Copy | `packages/adapters/src/mail/templates/layout.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| VAD-072 | P3 صقل | بصري | إطار HTML في Firefox | اختلاف متصفحي: Firefox يعرض المبلغ داخل البريد بخط mono احتياطي أرفع وحالة «قيد المعالجة» بلون كهرماني بدل البني الداكن | إطار البريد لا يحمّل IBM Plex Mono (طبيعي للبريد)، لكن لون الحالة يختلف بين Chromium/WebKit (بني داكن) وFirefox (كهرماني) — على الأرجح دالة لون غير مدعومة أو light-dark() في القالب. الإطار نفسه له حدّ أوضح في Firefox. | local-firefox-1440/admin-admin-mail-detail.png مقابل local-chromium-1440 وlocal-webkit-1440 | استخدم hex صريحاً في القالب (لا color-mix/light-dark) وcolor-scheme محدداً داخل الإطار؛ وثّق الاختلاف في brand/templates/email/TEST-MATRIX.md. | S | Light/Dark Mode Contrast table (token-driven theming); WIG Dark Mode | `packages/adapters/src/mail/templates/layout.ts` |
| VAD-085 | P3 صقل | بصري | تبويبا «HTML / نص» | التبويبان قصيران جداً (HTML · نص) ويبدآن من يسار العمود بسبب VAD-002، والتفاصيل في العمود الأيسر بترتيب LTR | التبويبات بعرض المحتوى (fill=false) لكن في موضع البداية الخاطئ. | admin-admin-mail-detail.png; artifacts/ux-audit-F4/evidence/admin-mail-detail-ar-desktop.png | يُحل مع VAD-002؛ أضف تبويب «المتغيرات» بدل المطوي أسفل العمود. | S | component-patterns Tab Systems | `apps/admin/src/app/[locale]/(app)/mail/[id]/page.tsx` |

### الإدارة — me

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-me-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-me-ar-desktop.png`، `capture: artifacts/ux-audit-F4/evidence/admin-me-en-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-me-en-desktop.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-014 | P2 ثانوي | بصري | أعمدة «كلمة المرور» و«تطبيق المصادقة» | محتوى العمودين الثاني والثالث مُحاذى إلى الأسفل فيظهر فراغ 150–300 px تحت العناوين | في «حسابي» يبدأ نص عمود كلمة المرور عند y≈270 وحقل «الحالية» لتطبيق المصادقة عند y≈545 بينما العناوين عند 165؛ الشبكة توزّع الصفوف بمحاذاة سفلية فتبدو الأعمدة مكسورة. النمط نفسه في «بيانات الطلب» بصفحة المراجعة اليدوية (فراغ 150 px فوق «العميل») وفي «الجلسات النشطة» بتفاصيل الموظف. | admin-admin-me.png; artifacts/ux-audit-F4/evidence/admin-me-ar-desktop.png; admin-admin-payment.png; admin-admin-staff-detail.png | اضبط align-items:start على شبكة الأقسام (أو احذف items-end/self-end على الحاويات) واجعل كل قسم يبدأ من أعلى عموده؛ في المراجعة اليدوية لا تجعل صفوف KeyValue تمتد لتساوي ارتفاع إطار الإثبات. | S | design-pro critique «Layout rhythm / negative space doing work»; Layout & Spacing table | `apps/admin/src/app/[locale]/(app)/staff/me/page.tsx`، `apps/admin/src/app/[locale]/(app)/payments/[id]/page.tsx`، `apps/admin/src/app/[locale]/(app)/staff/[id]/page.tsx` |
| VAD-015 | P2 ثانوي | تقني | حقل «الصلاحيات» | الصلاحيات تُعرض كجدار رموز خام «overview · read, products · write, …» في منطقة غير موسومة كخام | 26 صلاحية بصيغة resource.action مُفصولة بفواصل في عمود الملف الشخصي — غير مقروءة ولا مرتبة، ومخالفة لقاعدة حصر التسريبات التقنية في مناطق خام واضحة. | admin-admin-me.png; apps/admin/src/app/[locale]/(app)/staff/me/page.tsx:38 | اعرضها كمجموعة شارات مترجمة مجمّعة حسب المورد (الحسابات: قراءة · كتابة)، أو اكتفِ بالدور مع رابط «عرض الصلاحيات» يفتح قائمة مرتبة. | S | fpt-brand: technical leaks only in marked raw areas; ui-ux-pro-max §5 compact-label-overflow | `apps/admin/src/app/[locale]/(app)/staff/me/page.tsx`، `packages/i18n/src/messages/ar.json` |
| VAD-066 | P3 صقل | بصري | قائمة الجلسات | 15 جلسة بزر «إلغاء» في الطرف المقابل لكل صف ولا «إلغاء كل الجلسات الأخرى» | زر الإلغاء على بعد ~900 px من اسم الجلسة؛ إنهاء 14 جلسة يتطلب 14 نقرة. | artifacts/ux-audit-F4/evidence/admin-me-ar-desktop.png | زر «إنهاء كل الجلسات الأخرى» أعلى القائمة + زر الإلغاء ملاصق للمحتوى (ListRow end). | S | ui-ux-pro-max §8 undo-support/bulk; design-pro proximity | `apps/admin/src/app/[locale]/(app)/staff/me/page.tsx` |

### الإدارة — order

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-desktop.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-en-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-en-desktop.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-032 | P2 ثانوي | بصري | صفحة الطلب الفاشل | طلب بحالة «فاشل» بلا سبب فشل ولا إجراء (إلغاء/تواصل/إعادة محاولة) | تفاصيل الطلب تعرض الحقائق فقط؛ بطاقة المدفوعات تقول «فاشلة» مع مرجع المزوّد ولا رمز رفض أو رسالة؛ لا شريط إجراءات. المشغّل الذي يتلقى شكوى العميل لا يجد الجواب ولا الخطوة التالية. | admin-admin-order.png | اعرض سبب الفشل من payment_events (decline code مترجم + الخام في «البيانات الخام») وشريط إجراءات: إلغاء الطلب، إعادة إرسال رابط الدفع، نسخ المرجع للدعم. | M | Nielsen #9 recognize/diagnose/recover; ui-ux-pro-max §8 error-recovery | `apps/admin/src/app/[locale]/(app)/orders/[id]/page.tsx` |
| VAD-054 | P3 صقل | نص | حقائق الطلب | معرّفات خام في قيم عادية: «(fpt-50k v1)»، «fpt-rules-v1»، «terms-2026-10-29» | مقبولة إدارياً لكنها خارج منطقة خام موسومة؛ النص «تقييم FPT — 50,000 دولار» يستخدم كلمة «دولار» بدل الرمز USD بخلاف بقية المبالغ. | admin-admin-order.png; admin-admin-payment.png | وسم الأكواد بـ <code class=fpt-unit> مع تسمية «المعرّف»؛ وحّد اسم المنتج على «تقييم FPT — 50,000 USD». | S | fpt-brand: amounts carry currency code; technical leaks in marked areas | `apps/admin/src/app/[locale]/(app)/orders/[id]/page.tsx`، `packages/db (product names seed)` |

### الإدارة — orders

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-desktop.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-en-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-en-desktop.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-001 | P0 حاجب | تقني | عمود «المنتج» — السطر الثاني (حجم الحساب) | حجم الحساب يظهر مضروباً بـ 100: «5,000,000 USD» لمنتج 50,000 USD | في قائمة الطلبات وقائمة المدفوعات اليدوية يُعرض حجم المنتج كـ «5,000,000 USD» و«1,000,000 USD» بينما المنتج هو تقييم 50,000 / 10,000 دولار. السبب: Money يُستدعى بـ fractionDigits={0} و splitCents يقسم على 10^fractionDigits بدل القسمة الثابتة على 100، فتُعرض السنتات كدولارات. رقم مالي خاطئ أمام المشغّل في شاشة اعتماد المدفوعات. | artifacts/real-device/coverage-20261030/local-chromium-1440/admin-admin-orders.png; admin-admin-payments.png; artifacts/ux-audit-F4/evidence/admin-orders-ar-desktop.png; packages/ui/src/lib/format.ts:53-59; apps/admin/src/app/[locale]/(app)/orders/page.tsx:66; payments/page.tsx:87 | اجعل splitCents يقسم دائماً على 100 ثم يقرّب إلى fractionDigits المطلوبة (أو مرّر cents مع fractionDigits=2 واحذف الكسور عبر خيار trimZeros). أضف اختبار وحدة: formatMoney(5_000_000, {fractionDigits:0}) === «50,000». | S | ui-ux-pro-max §10 number-formatting; WIG Locale & i18n (Intl.NumberFormat) | `packages/ui/src/lib/format.ts`، `packages/ui/src/components/money.tsx`، `apps/admin/src/app/[locale]/(app)/orders/page.tsx`، `apps/admin/src/app/[locale]/(app)/payments/page.tsx` |
| VAD-026 | P2 ثانوي | نص | عمود «الطريقة» | تكرار «تحويل يدوي · تحويل يدوي» في عمود الطريقة | الخلية تجمع طريقة الدفع واسم المزوّد؛ لطلبات التحويل اليدوي كلاهما «تحويل يدوي» فيُطبع مرتين، بينما «بطاقة · MyFatoorah» صحيح. | admin-admin-orders.png; artifacts/ux-audit-F4/evidence/admin-orders-ar-desktop.png; apps/admin/src/app/[locale]/(app)/orders/page.tsx:56 | احذف التكرار عندما يتساوى الجزءان (new Set) أو اعرض للمدفوعات اليدوية المسار (P2P فايبر / تحويل محلي) بدل اسم المزوّد. | S | design-pro critique «Aesthetic and minimalist» | `apps/admin/src/app/[locale]/(app)/orders/page.tsx` |

### الإدارة — overview

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-004 | P1 رئيسي | نص | صف «حسابات على بُعد 20 % من حدّ» مقابل «أقرب حدّ» و«الأقرب للحد» | ثلاث دلالات مختلفة لـ «القرب من الحد» في ثلاث شاشات — 21 حساباً «تحتاج إجراءً» بينما لوحة المخاطر تقول «قريب من الحد 0» | النظرة العامة تعدّ الحسابات ذات nearest_limit_bps ≤ 2000 (مسافة ≤ 20 % من الرصيد) فتفلّغ كل حساب جديد لم يتداول (مسافته 5.3 % = حد الخسارة اليومية) = 21 من 23. قائمة الحسابات تعرض «أقرب حدّ 5.3 %» بلون تحذيري لكل حساب جديد. لوحة المخاطر تعرض «الأقرب للحد 0 %» لنفس الحساب (نسبة الاستهلاك من آخر 500 نقطة) وفلتر «قريب من الحد 0». المشغّل يرى تناقضاً صريحاً ويُغرق إشارة الخطر الحقيقية. | admin-admin-overview.png (21); admin-admin-accounts.png (5.3 % لكل صف); admin-admin-risk.png (0 % / قريب من الحد 0); apps/admin/src/app/[locale]/(app)/page.tsx:65; accounts/page.tsx:42; risk/page.tsx:22-30 | وحّد المقياس: مقياس واحد «المسافة إلى أقرب حدّ كنسبة من الرصيد» بعتبة واحدة (مثلاً ≤ 2 %) للعدّ والفلتر واللون، واستثنِ الحسابات بلا تداول (0 صفقات / 0 أيام) من التحذير. اعرض الدلالة نفسها بالاسم نفسه في الشاشات الثلاث. | M | Nielsen #4 Consistency; ui-ux-pro-max §10 color-guidance | `apps/admin/src/app/[locale]/(app)/page.tsx`، `apps/admin/src/app/[locale]/(app)/accounts/page.tsx`، `apps/admin/src/app/[locale]/(app)/risk/page.tsx`، `packages/i18n/src/messages/ar.json` |
| VAD-009 | P2 ثانوي | RTL | سهم الصف في «يحتاج إجراءً» وإحالات الشريك | سهم الانتقال يشير إلى اليمين «>» في الواجهة العربية بدل اليسار | الصفوف القابلة للنقر تضع ChevronRight في الطرف الأيسر مع rtl:-scale-x-100، لكن اللقطة المكبّرة تُظهر «>» غير معكوس في النظرة العامة وتفاصيل الشريك والهاتف. اتجاه السهم يعاكس اتجاه القراءة ويوحي بالرجوع. | admin-admin-overview.png (مكبّر 420,410→620,510); admin-admin-affiliate.png (الإحالات); artifacts/ux-after/admin-overview-phone-390-ar.png; packages/ui/src/components/list-row.tsx:43 | تحقق من تفعيل variant «rtl:» في إعداد Tailwind للتطبيق الإداري أو استبدله بـ CSS صريح: [dir=rtl] .ui-chev { transform: scaleX(-1) } (الفئة fpt-icon--dir موجودة أصلاً — اربطها بالقاعدة). | S | ui-ux-pro-max §9 navigation-direction; fpt-brand RTL mirroring | `packages/ui/src/components/list-row.tsx`، `packages/ui/src/components.css`، `apps/admin/tailwind.config.*` |
| VAD-022 | P2 ثانوي | بصري | الشريط الجانبي (SideNav) | ثماني وجهات مخفية تحت طيّ الشريط الجانبي على 900 px بلا أي مؤشر تمرير | على 1440×900 ينتهي الشريط المرئي عند «الشركاء»؛ البريد والاحتيال والتدقيق والموظفون والإعدادات والمحاكي وجاهزية الربط وصحة النظام تتطلب تمرير الشريط نفسه (overflow-y:auto بلا شريط تمرير ظاهر ولا تدرّج). المشغّل الجديد لن يكتشفها. كذلك لا توجد عدادات على عناصر الشريط (سحوبات بانتظار اعتماد، مدفوعات معلقة) رغم أن SideNavItem يدعم badge. | كل لقطات 1440 (مثلاً admin-admin-overview.png); packages/ui/src/components/side-nav.tsx:33; apps/admin/src/components/shell/AdminShell.tsx | قلّص ارتفاع العناصر إلى 40 px وفاصل المجموعات إلى 12 px، اجعل المجموعات قابلة للطي، وأضف تدرّج تلاشٍ أسفل القائمة عند الفيض؛ مرّر badge لعناصر الطوابير (السحوبات، المدفوعات اليدوية، التحقق من الهوية). | M | ui-ux-pro-max §9 tab-badge, nav-hierarchy; component-patterns Sidebar | `packages/ui/src/components/side-nav.tsx`، `apps/admin/src/components/shell/AdminShell.tsx`، `apps/admin/src/app/[locale]/(app)/layout.tsx` |
| VAD-027 | P2 ثانوي | نص | قائمة «صحة المحرّك» وشبكة «صحة المحاكي والمحرّك» | أسماء الخدمات خام بالإنجليزية (engine, alerts, sim, provisioner…) في النظرة العامة والمحاكي بينما صفحة «صحة النظام» تعرّبها | النظرة العامة تعرض 12 خدمة بأسمائها البرمجية، وصفحة صحة النظام تعرض الأسماء العربية (المحرّك، التنبيهات، المحاكي، التزويد…). نفس الكيان باسمين. والقائمة في النظرة العامة طويلة (12 صفاً × 75 px) وتزاحم الطوابير. | admin-admin-overview.png; admin-admin-simulator.png; admin-admin-health.png | استخدم نفس خريطة الأسماء (health.services.*) في الشاشات الثلاث؛ في النظرة العامة اختصر إلى سطر واحد «12/12 تعمل · آخر نبضة الآن» مع رابط لصحة النظام، وأظهر القائمة فقط عند وجود عطل. | S | Nielsen #4 Consistency; ui-ux-pro-max §5 content-priority | `apps/admin/src/app/[locale]/(app)/page.tsx`، `apps/admin/src/app/[locale]/(app)/simulator/SimulatorClient.tsx`، `packages/i18n/src/messages/ar.json` |
| VAD-028 | P2 ثانوي | بصري | ترتيب الأقسام (بطاقات ← يحتاج إجراءً ← الأحداث ← صحة المحرّك) | طابور «يحتاج إجراءً» ليس أول ما يراه المشغّل، وعلى الهاتف يُدفن تحت تغذية أحداث بطول ~2000 px | الصف الأول ست بطاقات معلوماتية، والطابور في الثلث الثاني؛ على الهاتف تأتي 15 حدثاً كاملة قبل صحة المحرّك وتوزيع الحسابات. البوصلة التشغيلية (ما ينتظر قراري) يجب أن تتصدر. | admin-admin-overview.png; artifacts/ux-after/admin-overview-phone-390-ar.png | انقل «يحتاج إجراءً» فوق البطاقات أو اجعل البطاقات الست نفسها روابط طابور بعدادات تحذيرية؛ حدّد الأحداث بـ 5 مع «عرض الكل»؛ على الهاتف اعرض صحة المحرّك كسطر ملخص. | M | design-pro critique «Is there a focal point?»; ui-ux-pro-max §5 content-priority | `apps/admin/src/app/[locale]/(app)/page.tsx` |
| VAD-037 | P3 صقل | بصري | شريط الرأس العلوي | عنوان القسم في الشريط العلوي يكرر H1 الصفحة مباشرة تحته (56 px مهدورة) | «نظرة عامة» في الشريط و«نظرة عامة» كعنوان الصفحة؛ على المكتب لا يضيف الشريط سوى زر القائمة. في صفحة «لا صلاحية» الشريط فارغ تماماً. | admin-admin-overview.png; admin-admin-forbidden.png; apps/admin/src/components/shell/AdminShell.tsx (center={current.label}) | على lg+: استخدم الشريط لمسار التنقل (breadcrumb) والبحث السريع، أو أخفِ العنوان فيه واكتفِ بـ H1. على الهاتف أبقه. | S | ui-ux-pro-max §9 breadcrumb-web; design-pro «rhythm» | `apps/admin/src/components/shell/AdminShell.tsx` |
| VAD-059 | P3 صقل | بصري | شرارات بطاقات KPI | شرارات بلا مدى زمني معلن (الحسابات النشطة، مخالفات اليوم) | القاعدة البرندية للرسوم: العنوان والفترة مُعلنان؛ الشرارة في «مخالفات اليوم 0» تُظهر قفزة لا يفسرها الرقم 0. | admin-admin-overview.png | أضف «آخر 14 يوماً» كتسمية صغيرة أو title على الشرارة، واجعل مؤشر النهاية بنفس لون الخط إن لم يكن «القيمة الحالية». | S | ui-ux-pro-max §10 time-scale-clarity, legend-visible | `apps/admin/src/app/[locale]/(app)/page.tsx`، `packages/ui/src/components/stat-tile.tsx` |
| VAD-078 | P3 صقل | بصري | بطاقة «إيراد هذا الشهر» (en) | الإنجليزية: «USD 8,970.00» يلتصق الرمز بالرقم بحجمين مختلفين داخل بطاقة ضيقة فيلتف العنوان على سطرين | في النسخة الإنجليزية عناوين البطاقات تلتف («Revenue this month», «Commands in flight») بينما العربية سطر واحد؛ شبكة 6 أعمدة ضيقة على 1440. | admin-admin-overview-en.png | 5 بطاقات أو شبكة auto-fit بعرض أدنى 200 px؛ text-wrap:balance للعناوين. | S | ui-ux-pro-max §6 heading-line-balance; Layout & Spacing | `apps/admin/src/app/[locale]/(app)/page.tsx` |
| VAD-079 | P3 صقل | هوية | تذييل الشريط الجانبي (en) | «PART OF FIPER GLOBAL» بأحرف كبيرة وتباعد أحرف في الإنجليزية بينما العربية «جزء من Fiper Global» عادية | EndorsementLine يختلف أسلوباً بين اللغتين؛ قاعدة البرند تحدد شكل سطر الانتماء — تحقق من مطابقته لـ brand/README (الوزن والتباعد). | admin-admin-overview-en.png مقابل admin-admin-overview.png | نفس الوزن والحالة في اللغتين وفق brand/references/composition-rules.md. | S | fpt-brand endorsement line | `packages/ui/src/components/endorsement-line.tsx` |

### الإدارة — payment

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-013 | P2 ثانوي | RTL | تاريخ «رفع الإثبات» وأعمدة التاريخ في الجداول | القوس «(» يُفصل عن «GMT+4)» عند الالتفاف: «… بتوقيت دبي (» ثم «GMT+4)» | TimePart يطبع «بتوقيت دبي (» كنص ثم <bdi>GMT+4</bdi> ثم «)»، فعند ضيق العرض ينكسر السطر بين القوس والرمز ويظهر قوس يتيم في نهاية السطر الأول وقوس مقلوب في الثاني. يتكرر في صفحة المراجعة اليدوية، جدول البريد على 1280، جدول الحسابات (3 أسطر لكل تاريخ). | admin-admin-payment.png (رفع الإثبات); artifacts/ux-audit-F4/evidence/admin-mail-ar-desktop.png (عمود الإدراج); admin-admin-accounts.png (عمود التسليم); packages/ui/src/components/date-time.tsx:74-80 | اجعل «بتوقيت دبي (GMT+4)» وحدة غير قابلة للكسر: <span class="whitespace-nowrap">بتوقيت دبي (<bdi>GMT+4</bdi>)</span> واسمح بالكسر قبلها فقط. في الجداول استخدم صيغة مختصرة (انظر VAD-020). | S | WIG Typography (non-breaking spaces); ui-ux-pro-max §6 long-token-wrapping | `packages/ui/src/components/date-time.tsx`، `apps/admin/src/components/AdminTime.tsx` |
| VAD-041 | P3 صقل | RTL | بيان ملف الإثبات | «KB · PNG 74» — ترتيب الحجم والنوع مقلوب | السطر يُفترض «PNG · 74 KB»؛ الرقم ينفصل عن وحدته في السياق العربي. | admin-admin-payment.png | <bdi dir=ltr>74 KB</bdi> · PNG أو «74 كيلوبايت · PNG». | S | WIG Typography (10&nbsp;MB unbreakable) | `apps/admin/src/app/[locale]/(app)/payments/[id]/page.tsx` |

### الإدارة — payments

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-042 | P3 صقل | نص | عمود «المسار» | نص موجّه للعميل «تحويل P2P من حسابك في فايبر» داخل جدول المشغّل | تسمية المسار مأخوذة من واجهة العميل بضمير المخاطب؛ في لوحة الإدارة تقرأ كأن الموظف هو المحوّل. | admin-admin-payments.png; admin-admin-payment.png | تسمية إدارية: «P2P فايبر» / «تحويل محلي». | S | WIG Content & Copy (second person, audience) | `apps/admin/src/app/[locale]/(app)/payments/page.tsx`، `packages/i18n/src/messages/ar.json` |

### الإدارة — payout

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-desktop.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-en-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-en-desktop.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-011 | P2 ثانوي | تقني | PayoutSteps — الخطوة الحالية | المتتبع يضع «موافقة أولى» كخطوة جارية رغم أن الحالة «معتمد (1 من 2)» والاعتماد الأول مسجّل باسم Amir | payoutStepIndex('approved_1') يُرجع 2 (= موافقة أولى) فيُرسم الشريط الأحمر تحت الخطوة 3 بينما قسم «الاعتمادات والتنفيذ» يعرض اعتماد Amir منجزاً والزر الأساسي «الاعتماد الثاني». يجب أن تكون الخطوة 4 «موافقة ثانية» هي الجارية. المشغّل يتردد: هل الاعتماد الأول تم أم لا؟ | admin-admin-payout.png; admin-admin-payouts.png; apps/admin/src/components/PayoutSteps.tsx:6 | approved_1 → 3 (الخطوة الجارية = الموافقة الثانية)، approved/executing → 4 (الدفع جارٍ)، paid → 5. أضف اختبار وحدة للربط. | S | Nielsen #1 Visibility of system status | `apps/admin/src/components/PayoutSteps.tsx` |
| VAD-043 | P3 صقل | نص | ملاحظة الاعتماد الأول | ملاحظة «Checks reviewed — first approval» بالإنجليزية تحت اسم المعتمد | ملاحظة البذر/الموظف تُعرض كما أُدخلت؛ إن كانت نصاً مولّداً من النظام فيلزم ترجمته. | admin-admin-payout.png; artifacts/ux-after/admin-payout-phone-390-ar.png | إن كانت مولّدة: مفتاح i18n؛ إن كانت حرة: اعرضها داخل <bdi dir=auto> بخط مائل مع تسمية «ملاحظة». | S | fpt-brand text rules | `apps/admin/src/app/[locale]/(app)/payouts/[id]/page.tsx` |
| VAD-083 | P3 صقل | بصري | قسم «الاعتمادات والتنفيذ» | حقول «التنفيذ —» و«مرجع التنفيذ —» و«الشهادة —» تُعرض فارغة قبل أوانها | سلسلة شرطات لحقول لا تنطبق على حالة «معتمد (1 من 2)»؛ ضجيج بصري في صفحة قرار. | admin-admin-payout.png; artifacts/ux-after/admin-payout-phone-390-ar.png | أخفِ حقول التنفيذ حتى تصبح الحالة executing/paid، واعرض بدلها «الخطوة التالية: الاعتماد الثاني». | S | ui-ux-pro-max §8 progressive-disclosure | `apps/admin/src/app/[locale]/(app)/payouts/[id]/page.tsx` |

### الإدارة — payouts

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-desktop.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-en-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-en-desktop.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-010 | P2 ثانوي | بصري | عمود «الطابور حسب الحالة» (Stepper داخل كل صف) | متتبع خطوات كامل داخل كل صف يرفع ارتفاع الصف إلى ~95 px ويقطع الجدول على 1280 px | خمس خطوات بتسميات ملتفة على سطرين («موافقة / أولى») في كل صف، مع عمود حالة يكرر المعلومة نفسها («معتمد (1 من 2)»). على 1280 px (لابتوب شائع) يُقصّ عمود «وقت الطلب» ويُنزَع للتمرير الأفقي داخل البطاقة بلا مؤشر. الطابور بصفين فقط الآن لكنه الشاشة الأكثر استخداماً عند النمو. | admin-admin-payouts.png; artifacts/ux-audit-F4/evidence/admin-payouts-ar-desktop.png (قصّ العمود الأخير); apps/admin/src/app/[locale]/(app)/payouts/page.tsx:42 | في الجدول اكتفِ بشارة الحالة + شريط تقدم مصغّر (5 نقاط) بلا تسميات، أو اعرض الخطوة التالية فقط «ينتظر: موافقة ثانية». احتفظ بالمتتبع الكامل في صفحة التفاصيل. اجعل .fpt-table-wrap تُظهر ظل تمرير عند الفيض. | M | design-pro component-patterns Data Tables (row density); ui-ux-pro-max §5 horizontal-scroll | `apps/admin/src/app/[locale]/(app)/payouts/page.tsx`، `apps/admin/src/components/PayoutSteps.tsx` |
| VAD-021 | P2 ثانوي | هوية | تمرير المؤشر على صفوف الجداول | تحويم الصف يلوّنه بغسلة الأحمر البرندي (--fpt-brand-red-wash) — إشارة خطر على كل صف | brand/components/components.css يضبط tr:hover td بلون brand-red-wash؛ ظهر في لقطة Firefox للسحوبات كصف محمرّ كأنه مرفوض أو محدد للحذف. القاعدة البرندية تحصر الأحمر في الشعار والإجراء الأساسي والحالة النشطة؛ التحويم على بيانات مالية ليس أياً منها. | artifacts/real-device/coverage-20261030/local-firefox-1440/admin-admin-payouts.png (الصف الأول); brand/components/components.css:204 | استخدم غسلة محايدة (color-mix(in srgb, var(--fpt-text-primary) 4%, transparent) كما في .ui-row:hover) واحتفظ بالأحمر للصف المحدد/النشط إن وُجد. يلزم تعديل في حزمة البرند (fpt-brand v1.0.0) — سجّله كمقترح PROPOSED. | S | fpt-brand red usage rule; design-pro critique «Does color mean something?» | `brand/components/components.css`، `packages/ui/src/components.css` |
| VAD-040 | P3 صقل | RTL | خلية «القناة» | «عملة رقمية (USDT) (TRC20)» تنكسر على سطرين بأقواس معكوسة «USDT) / (TRC20» | الأقواس حول رموز لاتينية داخل نص عربي تنقلب عند الكسر. | admin-admin-payouts.png; local-webkit-1440/admin-admin-payouts.png | صيغة بلا أقواس: «عملة رقمية · USDT TRC20» مع <bdi> حول الرمز ونفس التسمية في العميل. | S | ui-ux-pro-max §6 long-token-wrapping | `apps/admin/src/app/[locale]/(app)/payouts/page.tsx`، `packages/i18n/src/messages/ar.json` |
| VAD-071 | P3 صقل | بصري | الفلتر الافتراضي | الطابور يفتح على «كل الطلبات» بما فيها المدفوعة بدل «ينتظر اعتمادي» | الشاشة طابور مراجعة؛ الافتراضي يجب أن يكون ما ينتظر القرار (معتمد 1 من 2 / بانتظار الفحوصات)، وفرز أقدم أولاً. | admin-admin-payouts.png | افتراضي: الحالات المفتوحة مرتبة بموعد المراجعة تصاعدياً، مع رقيقة «الكل» للأرشيف. | S | Nielsen #7 Flexibility and efficiency; design-pro «Utility & Function» | `apps/admin/src/app/[locale]/(app)/payouts/page.tsx` |

### الإدارة — products

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-002 | P1 رئيسي | RTL | Radix Tabs (قائمة التبويبات + محتواها) | التبويبات ومحتواها تُرسم LTR داخل الصفحات العربية (جدول المنتجات معكوس، بطاقات الإعدادات، تبويب «نظرة» في الحساب) | Radix Tabs يضبط dir="ltr" على جذر التبويبات عند غياب DirectionProvider أو خاصية dir. النتيجة في كل الشاشات التي تستخدم Tabs: أول تبويب في أقصى اليسار (المنتجات، الإعدادات «عام»، الحساب «نظرة»، البريد «HTML»)، جدول المنتجات يبدأ بعمود «الرمز» من اليسار وزر «تعديل» في اليمين عكس جدول الحسابات، بطاقات الإعدادات بترتيب معكوس، وقيم «المقاييس» في تفاصيل الحساب محاذاة لليسار مع تاريخ مشوّه «( بتوقيت دبي 31،00:00 أكتوبر 2026 / GMT+4)». على الهاتف تظهر بطاقات المنتجات بعناوين على اليسار وقيم على اليمين. | admin-admin-products.png; admin-admin-settings.png; admin-admin-account.png (المقاييس + التاريخ); admin-admin-mail-detail.png; artifacts/ux-after/admin-products-phone-390-ar.png; packages/ui/src/components/tabs.tsx:6 (RadixTabs.Root بلا dir); apps/admin/src/app/[locale]/(app)/products/page.tsx:89; settings/page.tsx:65; accounts/[id]/page.tsx:137; mail/[id]/page.tsx:41 | مرّر dir={locale==='ar'?'rtl':'ltr'} إلى Tabs في مكوّن @fpt/ui (كما يفعل DropdownMenu في AdminShell.tsx:91) أو لفّ التطبيق بـ DirectionProvider من radix-ui. أضف فحص e2e: أول تبويب يجب أن يكون في أقصى اليمين في العربية. | S | ui-ux-pro-max §5 (RTL/layout), design-pro critique «Consistency»; WIG Dark Mode/i18n | `packages/ui/src/components/tabs.tsx`، `apps/admin/src/app/[locale]/layout.tsx` |
| VAD-025 | P2 ثانوي | بصري | عمود «مجموعة cTrader» | زر «فحص المجموعة» + شارة «لم تُفحص» في كل صف — 10 أزرار في جدول 5 صفوف وارتفاع صف 120 px | كل منتج يحمل زر فحص وزر تعديل، والاسم يلتف على سطرين في عمود ضيق بينما الجدول يترك فراغاً على اليمين (بسبب VAD-002 الترتيب معكوس أصلاً). الفحص إجراء دفعة واحدة طبيعياً. | admin-admin-products.png | زر واحد «فحص كل المجموعات» فوق الجدول + حالة آخر فحص كشارة في الصف؛ «تعديل» كأيقونة قلم أو قائمة ⋯؛ وسّع عمود الاسم. | S | design-pro critique «Every element earns its place»; ui-ux-pro-max §9 overflow-menu | `apps/admin/src/app/[locale]/(app)/products/page.tsx`، `apps/admin/src/app/[locale]/(app)/products/ProductsClient.tsx` |
| VAD-053 | P3 صقل | نص | ملاحظة أسفل الجدول | رمز «catalog_version» خام داخل جملة عربية | «التعديل يُدرج catalog_version جديدة للرمز نفسه» — مصطلح قاعدة بيانات في نص تفسيري. | admin-admin-products.png | «يُدرج نسخة كتالوج جديدة…». | S | fpt-brand technical leaks rule | `packages/i18n/src/messages/ar.json` |

### الإدارة — risk

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-029 | P2 ثانوي | بصري | عمود «الأقرب للحد» وشارات الخطورة | قيم المخاطر بلا تدرّج لوني: «7 %» من الحد و«0 %» بنفس الأبيض العريض؛ الخطورة 60 و40 بنفس الأصفر | لوحة المخاطر تُفترض أن تُبرز الحساب الأقرب للكسر؛ القيم كلها بنفس الوزن واللون، والمقياس aria فقط. في الاحتيال شارات الخطورة 60 و40 بنفس النبرة. | admin-admin-risk.png; admin-admin-fraud.png | طبّق سلم النبرات: ≥ 80 % استهلاك = خطر (finance-loss) مع كلمة، 50–80 = تحذير (drawdown)، أقل = محايد؛ شريط مصغّر مرئي في الخلية. الخطورة ≥ 70 = danger، 40–69 = warning، أقل = neutral. | S | ui-ux-pro-max §1 color-not-only; §10 color-guidance; fpt-brand finance states (word + sign + colour) | `apps/admin/src/app/[locale]/(app)/risk/page.tsx`، `apps/admin/src/components/status.ts` |
| VAD-069 | P3 صقل | بصري | عمود «العميل» | أسماء العملاء تلتف على سطرين بينما أعمدة الأرقام تملك فراغاً | «Khalid Al-Mansoori» على سطرين في لوحة المخاطر؛ توزيع الأعمدة تلقائي. | admin-admin-risk.png | width على أعمدة الأرقام (ch) وwhitespace-nowrap للاسم مع truncate + title. | S | component-patterns Data Tables (nowrap headers, widths) | `apps/admin/src/app/[locale]/(app)/risk/page.tsx` |

### الإدارة — settings

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-settings-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-settings-ar-desktop.png`، `capture: artifacts/ux-audit-F4/evidence/admin-settings-en-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-settings-en-desktop.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-023 | P2 ثانوي | بصري | صف التبويبات على الهاتف | 8 تبويبات تُقصّ على 390 px — «كل المفاتيح» و«الشركاء» و«المدفوعات» و«المنصة» غير قابلة للوصول | TabsList بالوضع fill يضغط ثمانية تبويبات في عرض الهاتف فتظهر خمسة فقط بلا تمرير أفقي ظاهر ولا زر «المزيد». | artifacts/ux-after/admin-settings-phone-390-ar.png | على الهاتف: تبويبات قابلة للتمرير أفقياً مع تلاشٍ طرفي (overflow-x:auto; scroll-snap) أو Select للأقسام، وfill={false}. | S | ui-ux-pro-max §5 compact-label-overflow, chip-collection-reflow; component-patterns Tab Systems | `apps/admin/src/app/[locale]/(app)/settings/page.tsx`، `packages/ui/src/components/tabs.tsx`، `packages/ui/src/components.css` |
| VAD-050 | P3 صقل | RTL | وصف بطاقة «الكيان» وملاحظة «القنوات» | مسارات ورموز لاتينية داخل جمل عربية تُفكك القراءة: «المكان الوحيد لنصوص الكيان — config/legal.ts يُقرأ من» | الجملة المصدرية «يُقرأ من config/legal.ts — المكان الوحيد…» تظهر مقلوبة؛ نفس المشكلة في «FPT_*_MODE» ونص جاهزية الربط «(pnpm --filter … contract:status)». | admin-admin-settings.png; admin-admin-integrations.png | لفّ كل مسار/أمر في <bdi dir=ltr class=fpt-unit> وضعه في نهاية الجملة أو سطر خاص. | S | ui-ux-pro-max §6 long-token-wrapping; fpt-brand RTL | `apps/admin/src/app/[locale]/(app)/settings/page.tsx`، `apps/admin/src/app/[locale]/(app)/integrations/page.tsx`، `packages/i18n/src/messages/ar.json` |
| VAD-051 | P3 صقل | نص | بطاقة «الكيان» — العنوان المسجّل | قيمة نائبة «العنوان المسجّل يُدرج لاحقاً (مسودة، 29 أكتوبر 2026)» في إعدادات الإنتاج | صفحة للمالك للقراءة فقط لكنها تعرض مسودة بتاريخ؛ تذكير بأن الكيان غير مكتمل في config/legal.ts (ولا يجوز اختراعه). | admin-admin-settings.png | اعرض «— (قرار معلّق)» بنبرة تحذير مع رابط إلى regulatory-matrix، لا نصاً حراً بتاريخ. | S | fpt-brand regulatory-matrix | `apps/admin/src/app/[locale]/(app)/settings/page.tsx` |
| VAD-052 | P3 صقل | بصري | بريد no-reply في بطاقة الكيان (1280 px) | «no-reply@fiperprotraders.com» ينكسر عند الواصلة إلى «no-» / «reply@…» | القيمة mono مع overflow-wrap:anywhere تنكسر داخل العنوان على عرض 1280. | artifacts/ux-audit-F4/evidence/admin-settings-ar-desktop.png | <bdi class="fpt-unit" style="word-break:keep-all"> أو اسمح بالكسر بعد @ فقط؛ قلّل حجم الخط في الخلية الضيقة. | S | WIG Content Handling (long content) | `apps/admin/src/app/[locale]/(app)/settings/page.tsx`، `packages/ui/src/components.css` |

### الإدارة — signin

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-desktop.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-desktop.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-en-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-en-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-en-desktop.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-en-desktop.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-033 | P2 ثانوي | وصول | إرسال النموذج فارغاً | فقاعة التحقق الأصلية «Please fill out this field.» بالإنجليزية على صفحة عربية | النموذج يعتمد على required الأصلي بلا noValidate فتظهر رسالة المتصفح بلغة المتصفح لا الصفحة، وتختفي بعد ثوانٍ ولا تُعلن لقارئ الشاشة بشكل متسق. | artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-desktop.png | noValidate + تحقق عند الإرسال برسالة عربية تحت الحقل (aria-describedby, role=alert) وتركيز أول حقل خاطئ. | S | ui-ux-pro-max §8 error-placement, focus-management; WIG Forms | `apps/admin/src/app/[locale]/(auth)/signin` |
| VAD-034 | P3 صقل | نص | زر الإرسال | زر الدخول يحمل تسمية عامة «متابعة» بدل «تسجيل الدخول» | الخطوة الأولى (بريد + كلمة مرور) تعرض «متابعة»؛ الإرشادات تطلب تسميات محددة للأزرار. يوجد مفتاح «تسجيل الدخول» في الرسائل. | admin-admin-signin.png; packages/i18n/src/messages/ar.json:313,504 | «تسجيل الدخول» ثم في خطوة الرمز «تأكيد الرمز». | S | WIG Content & Copy (specific button labels) | `apps/admin/src/app/[locale]/(auth)/signin`، `packages/i18n/src/messages/ar.json` |
| VAD-035 | P3 صقل | بصري | بطاقة الدخول | بطاقة الدخول ملتصقة بأعلى الشاشة وتترك ثلثي الارتفاع فارغاً | على 900 px تنتهي البطاقة عند y=405 والتذييل عند 850؛ لا مركز بصري. | admin-admin-signin.png | مركزة عمودية (min-h-dvh grid place-items-center) مع الشعار داخل البطاقة. | S | design-pro critique «focal point»; Layout & Spacing | `apps/admin/src/app/[locale]/(auth)/layout.tsx` |

### الإدارة — simulator

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-049 | P3 صقل | نص | العنوان الفرعي وملاحظات الإلغاء/الدفعة الأسبوعية | علامات الاقتباس المائلة لـ Markdown تظهر حرفياً: «أمر `sim_*`»، «`breach_reverse`»، «`affiliates_run_weekly`» | نصوص ar.json تحوي backticks لا تُفسَّر. | admin-admin-simulator.png; artifacts/ux-after/admin-breach-phone-390-ar.png; packages/i18n/src/messages/ar.json:3151,2234,2558 | احذف العلامات واعرض الرمز عبر <code class=fpt-unit> بواسطة t.rich أو أعد الصياغة بالعربية. | S | fpt-brand text rules; WIG Typography | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### الإدارة — staff

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-018 | P2 ثانوي | بصري | عمود «إجراءات» | ثلاثة أزرار كاملة لكل صف (تغيير الدور / تعطيل / إعادة تعيين المصادقة) ترفع الصف إلى 140 px | الجدول المكتبي يعرض لكل موظف زر «تعطيل» بلون الخطر وزرين ثانويين ورابط «سجل الدخول»؛ أربعة أزرار خطر مرئية في شاشة واحدة. نسخة الهاتف تستخدم قائمة «⋯» الأنظف. ملاحظة: صفحة تفاصيل الموظف لا تحوي أي إجراء. | admin-admin-staff.png; artifacts/ux-after/admin-staff-phone-390-ar.png; admin-admin-staff-detail.png | وحّد مع الهاتف: قائمة إجراءات «⋯» في الصف (DropdownMenu)، و«تعطيل» بنبرة خطر داخل القائمة فقط؛ انقل الإجراءات إلى صفحة التفاصيل ضمن شريط أعلى + منطقة خطر أسفل. | S | ui-ux-pro-max §9 overflow-menu, destructive-nav-separation; §8 destructive-emphasis | `apps/admin/src/app/[locale]/(app)/staff/page.tsx`، `apps/admin/src/app/[locale]/(app)/staff/StaffClient.tsx`، `apps/admin/src/app/[locale]/(app)/staff/[id]/page.tsx` |
| VAD-075 | P3 صقل | وصول | بطاقة المحتوى على الهاتف | صفحة الحساب على الهاتف: ثلاثة أزرار إجراء مكدسة قبل الأرقام ثم تبويبات LTR | الأزرار الثانوية الثلاثة تسبق الحقائق في أعلى الشاشة؛ ترتيب القراءة: مرجع ← أزرار ← أرقام. | artifacts/ux-after/admin-account-phone-390-ar.png | على الهاتف: الحقائق أولاً، الإجراءات في قائمة ⋯ بجانب العنوان، ومنطقة الخطر في الأسفل كما هي. | S | ui-ux-pro-max §9 overflow-menu; detail page facts → actions → history | `apps/admin/src/app/[locale]/(app)/accounts/[id]/page.tsx` |

### الإدارة — staff-detail

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VAD-080 | P3 صقل | بصري | سجل الدخول | سجل الدخول يعرض «بصمة IP cb87cdde» بسطرين وUTC لكل عنصر — 6 عناصر تملأ الشاشة ولا تعرض المتصفح/الجهاز | كل عنصر 95 px: شارة + تاريخ دبي + UTC + بصمة؛ المعلومة الأهم للأمن (الجهاز/المتصفح/الموقع التقريبي) غائبة، بينما «الجلسات النشطة» تعرض Edge · Windows. | admin-admin-staff-detail.png | جدول مضغوط: الوقت \| النتيجة \| الجهاز \| البصمة (آخر 8) مع UTC في tooltip. | S | design-pro «Precision & Density» | `apps/admin/src/app/[locale]/(app)/staff/[id]/page.tsx` |

### الإدارة — أوضاع التكامل

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-079 | P3 صقل | نص | integrations.modes.{mock,live,sim} AR = «mock/live/sim» ↔ mailPreview.modes.mock «محاكاة» / live «حقيقي» | أسماء الأوضاع غير مترجمة في صفحة وترجمة في أخرى |  | ar.json:L2679 / en.json:L2679; ar.json:L3385 / en.json:L3385 | «محاكاة (mock)» / «حقيقي (live)» / «محاكي (sim)» / cTrader في الصفحتين. | S | i18n completeness | `packages/i18n/src/messages/ar.json` |

### الإدارة — إشارات الاحتيال

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-071 | P3 صقل | نص | fraud.dismiss «رفض» · fraud.signalStatus.dismissed «مرفوضة» · fraud.dismissBody «يعلّم الإشارة كإنذار كاذب» | dismiss (إنذار كاذب) تُترجم «رفض» وهي كلمة رفض الهوية والدفعة |  | ar.json:L2473 / en.json:L2473; ar.json:L2460 / en.json:L2460 | «استبعاد» / «مستبعَدة (إنذار كاذب)». | S | terminology | `packages/i18n/src/messages/ar.json` |

### الإدارة — إعدادات 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-038 | P2 ثانوي | نص | payoutsAdmin.rejectBody «(لا يوجد قالب بريد للرفض في هذه الجولة)» · affiliatesAdmin.model.body «كما هو مضبوط لهذه الجولة (affiliates.md)» · settings.news.rule «التغذية اليدوية لهذه الجولة» · settings.affiliates.uplineOverrideHint «توصية أمير، 30 مايو 2026» · settings.reset.body «القرار الحاكم 3» · settings.affiliates.uplineOverride «(A6: …)» | لغة إدارة المشروع («هذه الجولة»، أسماء أشخاص وتواريخ قرارات، أرقام قرارات وملفات) داخل واجهة الإنتاج | نصوص موجَّهة لفريق التطوير لا لموظف العمليات؛ تتقادم فور الجولة التالية وتكشف أسماء ومراجع داخلية. | ar.json:L2384 / en.json:L2384; ar.json:L2562 / en.json:L2562; ar.json:L2884 / en.json:L2884; ar.json:L2891 / en.json:L2891 | حذف إشارات «الجولة» والأسماء والتواريخ وأرقام القرارات؛ وصف السلوك الحالي فقط («يرى العميل إشعاراً داخل التطبيق.»، «معطّل عند الإطلاق (مستوى واحد).»). | S | CLAUDE.md «no technical leaks» / admin tone | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### الإدارة — إعدادات ← الكيان

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-077 | P3 صقل | هوية | config/legal.ts entity.brandFullAr «فايبر برو تريدرز» · apps/admin/src/app/[locale]/(app)/settings/page.tsx:103 | تعريب اسم المنتج يُعرض في الإدارة بينما المعجم يُبقي الاسم لاتينياً | المعجم § 1: «FPT — فايبر برو تريدرز» INHERITED «recorded, not applied (the FPT site keeps the Latin name)». | config/legal.ts:12; apps/admin/src/app/[locale]/(app)/settings/page.tsx:103; brand/glossary.md § 1 | عرض «FPT — Fiper Pro Traders» في اللغتين وحذف brandFullAr أو وسمه بأنه غير مُطبَّق. | S | brand glossary § 1 | `config/legal.ts` |

### الإدارة — الحوارات الحسّاسة (ActionDialog reauth)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-033 | P3 صقل | وصول | ActionDialog: زر «متابعة» يُزال عند الانتقال للخطوة 2 | عند الانتقال من خطوة إعادة المصادقة إلى التأكيد يُزال العنصر المركَّز فلا ينتقل التركيز إلى حقل السبب | footer step==='reauth' يعرض زر «متابعة»؛ بعد الضغط يُستبدل بـ null فيسقط التركيز إلى حاوية الحوار (Radix) لا إلى textarea السبب. (مراجعة كود — لم يُنفَّذ الحوار حيّاً: فشل النقر على صف الجدول في الجولة.) | apps/admin/src/components/ActionDialog.tsx:60-70,95-105; .e2e/a11y-admin.log actionDialog:ERROR | بعد setStep('confirm') ركّز textarea السبب (ref + useEffect) وأعلن «الخطوة 2 من 2». | S | accessibility.md § Focus Management | `apps/admin/src/components/ActionDialog.tsx` |

### الإدارة — الزمن

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-083 | P3 صقل | نص | health.sim.speedValue «الزمن الحقيقي» ↔ simulator.time.speedHint «الوقت الحقيقي» |  |  | ar.json:L3030 / en.json:L3030; ar.json:L3171 / en.json:L3171 | «الوقت الحقيقي» في الموضعين. | S | consistency | `packages/i18n/src/messages/ar.json` |

### الإدارة — الشريط السفلي للإدارة («المزيد») · شرائح الفلاتر

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-070 | P3 صقل | وصول | aria-current | زر «المزيد» (button) يحمل aria-current="page"، وشرائح الفلتر تستعمل aria-current="true" | `MoreAwareLink` يرسم `<button>` للمفتاح `#more` ويمرّر `aria-current="page"` من BottomNav؛ زر يفتح ورقة ليس صفحة. شرائح الفلاتر تستعمل `aria-current="true"` (مقبول لكنه غير «page» كبقية الروابط). | apps/admin/src/components/shell/AdminShell.tsx:75–80 و:166 `active: restActive` · apps/admin/src/components/Filters.tsx:60 `aria-current={… ? 'true' : undefined}` · packages/ui/src/components/filter-bar.tsx:43 | للزر `aria-expanded`/`aria-pressed` بدل aria-current؛ وحّد الشرائح على `aria-pressed` أو `aria-current="true"` موثقاً. | S | ui-ux-pro-max §1 aria-labels · §9 nav-state-active | `apps/admin/src/components/shell/AdminShell.tsx`، `apps/admin/src/components/Filters.tsx` |

### الإدارة — المحاكي

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-080 | P3 صقل | نص | simulator.scenario.accountHint «متداولاً جديداً بـ 100k» · simulator.scenario.body (نصوص حتمية) · mockGateway.shuftipro.lead «رحلة حقيقية» | اختصار «100k» وترجمة حرفية لـ journey |  | ar.json:L3181 / en.json:L3181; ar.json:L1349 / en.json:L1349 | «بحساب 100,000 USD»؛ «كما تلتقطها عملية تحقق حقيقية من الوثيقة». | S | Arabic style | `packages/i18n/src/messages/ar.json` |

### الإدارة — جداول الإدارة على 1024+

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-067 | P3 صقل | بصري | .fpt-table-wrap max-height:70vh + thead static | الجدول محصور في 70vh بتمرير داخلي ورأسه غير لاصق — تمرير متداخل بلا سياق للأعمدة | حزمة الهوية تحصر الجدول في `max-height:70vh` (vh لا dvh) مع `overflow:auto`، و@fpt/ui يلغي لصق رأس الجدول (`position:static`)؛ عند 100 صف يتمرّر المستخدم داخل صندوق ويفقد رؤوس الأعمدة. | brand/components/components.css:192 `.fpt-table-wrap{overflow:auto;…max-height:70vh}` · packages/ui/src/components.css:226 `.fpt-table thead th { position: static; }` | أزل `max-height` في `.ui-data` (دع الصفحة تتمرّر) أو أعد `position:sticky` للرأس داخل الصندوق. | S | ui-ux-pro-max §5 scroll-behavior · ui-ux-design-pro component-patterns Data Tables | `packages/ui/src/components.css` |

### الإدارة — صفحة المخالفة

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-037 | P2 ثانوي | نص | breaches.headline «أُوقف الحساب لتجاوز حدّ {kind} — {at}» + admin.breachKind.{expired,inactive,manual} | العنوان يولّد «لتجاوز حدّ يدوي» و«حدّ انتهاء المدة» و«حدّ عدم النشاط» | الصفحة تدرج admin.breachKind.* في القالب لكل الأنواع (page.tsx:88)، والقالب يفترض حدّ خسارة. الإنجليزية كذلك: “Account stopped after the Manual limit”. | ar.json:L2239 / en.json:L2239; apps/admin/src/app/[locale]/(app)/breaches/[id]/page.tsx:88 | قالب لكل نوع (مثل breach.human.* في العميل): «أُوقف الحساب لتجاوز حدّ الخسارة اليومية — {at}» / «انتهت مدة الحساب — {at}» / «أُغلق الحساب لعدم النشاط — {at}» / «أُوقف الحساب بقرار يدوي — {at}». | S | copy correctness | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### الإدارة — صندوق البريد

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-036 | P2 ثانوي | نص | mailPreview.templates.* (ينقصها ops_alert, manual_proof_received, manual_payment_confirmed, manual_proof_rejected, manual_payment_reminder, email_change_notice, account_closed) · apps/admin/src/app/[locale]/(app)/mail/page.tsx:35 | سبعة قوالب بريد بلا تسمية مترجمة → تظهر معرّفاتها الإنجليزية الخام في الواجهة العربية | الصفحة تستخدم t.has(`templates.${m}`) وإلا m.replaceAll('_',' ') فيظهر «ops alert» و«manual proof received» و«account closed» بين التسميات العربية. | apps/admin/src/app/[locale]/(app)/mail/page.tsx:35; mailPreview.templates.staff_invite (آخر مفتاح) | إضافة mailPreview.templates.{ops_alert,manual_proof_received,manual_payment_confirmed,manual_proof_rejected,manual_payment_reminder,email_change_notice,account_closed} بالصيغتين. | S | i18n completeness | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### الإدارة — طابور الأوامر

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-068 | P3 صقل | نص | admin.result.granted «في الطابور» · admin.result.queued / admin.result.approved «قائمة الانتظار» · admin.command.pending «في الانتظار» · health.queue.title «طابور الأوامر» · overview.queueDepth «عمق قائمة الانتظار» | queue = الطابور / قائمة الانتظار / الانتظار |  | ar.json:L1582 / en.json:L1582; ar.json:L1552 / en.json:L1552; ar.json:L1800 / en.json:L1800 | «قائمة الانتظار» في كل المواضع. | S | consistency | `packages/i18n/src/messages/ar.json` |

### الإدارة — قائمة الموظف (الإدارة)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-079 | P3 صقل | RTL | LogOut icon | أيقونة تسجيل الخروج تنعكس في العميل ولا تنعكس في الإدارة | `LogOut` اتجاهية (Lucide RTL guidance)؛ العميل يضيف `fpt-icon--dir`، قائمة الإدارة لا. | apps/client/src/app/[locale]/(app)/more/page.tsx:72 `<LogOut … className="fpt-icon fpt-icon--dir" />` · apps/admin/src/components/shell/AdminShell.tsx:110 `<LogOut aria-hidden="true" className="size-4" />` | `fpt-icon--dir` في الإدارة؛ وثّق قائمة الأيقونات الاتجاهية (chevrons, arrows, log-out, external-link؟) في icons.ts. | S | fpt-brand «directional icons mirror in RTL» | `apps/admin/src/components/shell/AdminShell.tsx`، `packages/ui/src/icons.ts` |

### الإدارة — كل شاشات الإدارة + صفحة 404 الإدارية

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-012 | P2 ثانوي | وصول | metadata.title ثابت «FPT Admin» / not-found بلا <title> | عناوين المستندات الإدارية كلها «FPT Admin» وصفحة 404 بلا عنوان | القياس: /ar, /ar/accounts, /ar/payouts, /ar/settings كلها title=«FPT Admin»؛ /ar/forbidden-x title=«». قارئ الشاشة يعلن عنوان النافذة أولاً، وتبويبات المتصفح لا تميَّز (WCAG 2.4.2). العميل صحيح («الرئيسية · FPT»). | .e2e/a11y-admin.log desktop:/ar/*:outline title; apps/admin/src/app/[locale]/layout.tsx:11 (title ثابت), apps/admin/src/app/not-found.tsx | title: { default: 'FPT Admin', template: '%s · FPT Admin' } في التخطيط مع generateMetadata لكل صفحة (عنوان PageTitle نفسه)، وأضف <title> في not-found. | S | WCAG 2.4.2 Page Titled; accessibility.md § Screen Reader Best Practices | `apps/admin/src/app/[locale]/layout.tsx`، `apps/admin/src/app/not-found.tsx`، `apps/admin/src/app/[locale]/(app)/**/page.tsx` |

### الإدارة — كل شاشات الإدارة

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-031 | P3 صقل | وصول | SideNav <nav aria-label> + BottomNav <nav aria-label> بالاسم نفسه «تنقّل الإدارة» | معلمان nav بالاسم نفسه في الصفحة الواحدة | القياس: lm تحوي nav[تنقّل الإدارة] مرّتين (الجانبي والسفلي). قائمة المعالم في قارئ الشاشة تعرض عنصرين متطابقين. | .e2e/a11y-admin.log desktop:/ar:outline lm | ميّز الاسمين («التنقّل الجانبي» / «شريط التبويب») أو اخفِ غير المعروض بـ aria-hidden حسب نقطة الكسر (hidden lg:block موجود للـ aside لكن nav السفلي يبقى في DOM على سطح المكتب). | S | accessibility.md § Landmark Regions | `apps/admin/src/components/shell/AdminShell.tsx` |

### الإدارة — كل نماذج الإجراءات بعد خمول 15 دقيقة

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-026 | P3 صقل | تقني | apps/admin/src/components/ActionForm.tsx + apps/admin/src/lib/session.ts actionStaff | انتهاء جلسة الموظف أثناء نموذج يُظهر «انتهت جلستك» ثم يترك النموذج معلّقاً بلا توجيه إلى تسجيل الدخول | `actionStaff` يعيد 401 بلا تحويل (مقصود)، و`ActionForm` يعرض التنبيه ويبقي القيم؛ لا رابط ولا تحويل تلقائي، فيكرّر الموظف الإرسال ويحصل على الرسالة نفسها. الرسالة ليست أيضاً في `role=alert`. | apps/admin/src/lib/session.ts:288-294; apps/admin/src/components/ActionForm.tsx:66-70. | عند `state.status === 401` اعرض التنبيه مع زر «تسجيل الدخول» يحمل `next=` المسار الحالي، وحوّل تلقائياً بعد 3 ثوانٍ؛ أضف `role="alert"` إلى تنبيه الخطأ. | S | ui-ux-pro-max § feedback / security-audit (session) | `apps/admin/src/components/ActionForm.tsx`، `apps/admin/src/lib/session.ts` |

### الإدارة — كل نماذج الإجراءات

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-025 | P3 صقل | تقني | apps/admin/src/components/ActionForm.tsx | رمز رسالة غير مترجم يُعرض كما هو للموظف (`state.message` خام) — تسرّب تقني محتمل عند أي رمز جديد | `t.has(state.message) ? t(state.message) : state.message` يعرض الرمز الإنجليزي الخام (مثل `not_manual_channel`) إن غاب عن `admin.result`؛ نفس الشيء في `useFieldError`. القاموس يغطي الحالي، لكن أي رمز خادمي جديد يظهر كنص تقني. | apps/admin/src/components/ActionForm.tsx:61, :94. | استبدل البديل بـ `t('generic')` (جملة عامة مترجمة) وسجّل الرمز المفقود في `console.warn` أثناء التطوير؛ أضف اختبار وحدات يتحقق أن كل `message`/`fields` التي تعيدها الإجراءات موجودة في `admin.result`. | S | security-audit (information leak) + ui-ux-pro-max § copy | `apps/admin/src/components/ActionForm.tsx` |

### الإدارة — كلمات حالة تقنية

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-072 | P3 صقل | نص | engine.status.degraded «متدنٍّ» · admin.events.accountStatus.purchased «مشترى» · admin.events.user_anonymised «أُخفيت البيانات» · admin.command.dead / health.queue.dead «توقف/توقّف» (EN «Gave up»/«Dead») | ترجمات حرفية غير دقيقة |  | ar.json:L1478 / en.json:L1478; ar.json:L1657 / en.json:L1657; ar.json:L1711 / en.json:L1711 | «أداء متدهور»، «تم الشراء»، «مُحيت البيانات الشخصية»، «متوقف نهائياً». | S | Arabic style | `packages/i18n/src/messages/ar.json` |

### الإدارة — نصوص تقنية داخل جمل عربية

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-063 | P3 صقل | RTL | simulator.scenario.body «(pass, daily_breach, …)» · integrations.statusNote «(pnpm --filter @fpt/adapters contract:status)» · settings.copier.body «copier.enabled وcopier.max_lots_per_fill و…» · settings.platform.body · settings.news.tsHint «Asia/Dubai، UTC+4» · affiliatesAdmin.model.attribution «‎?ref=CODE‎» | معرّفات لاتينية بفواصل لاتينية وواو عطف ملتصقة داخل العربية | عرض bidi قد يعكس ترتيب القائمة؛ الواو الملتصقة بمعرّف لاتيني («وcopier.max_open_lots») غير مقروءة. | ar.json:L3177 / en.json:L3177; ar.json:L2962 / en.json:L2962 | وضع كل معرّف في <code dir="ltr"> مع فاصل «،» عربي ومسافة بعد الواو، أو نقل القوائم إلى أسطر منفصلة. | S | RTL bidi | `packages/i18n/src/messages/ar.json` |

### مشترك — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-074 | P3 صقل | تقني | علامات يدوية بدل المكوّنات | علامات مكتوبة يدوياً بأصناف الحزمة بدل المكوّنات: عدّاد يدوي بأنماط مضمّنة، صف ui-row يدوي، dl.fpt-card، زر خروج بأصناف خام، زر 404 بأنماط مضمّنة | خمسة مواضع تعيد بناء المكوّن بدل استعماله: عدّاد «لا هدف» في النظرة العامة (`.ui-meter` يدوي + `style={{whiteSpace…}}`)، صفوف «جزئي» في المخاطر (`li.ui-row` يدوي)، بطاقة الكيان القانوني (`dl.fpt-card grid` بدل KeyValue)، زر الخروج (`fpt-btn fpt-btn--secondary` بدل Button)، وزر 404 الجذر (gradient مضمّن). | apps/client/src/components/account-overview.tsx:274–283 · apps/admin/src/app/[locale]/(app)/risk/page.tsx:526–534 · apps/client/src/app/[locale]/(public)/legal/[key]/page.tsx:241–248 · apps/client/src/app/[locale]/(app)/more/page.tsx:71 · apps/client/src/app/not-found.tsx:34 · apps/admin/src/app/not-found.tsx:226 | `RuleMeter` بخاصية `notApplicable`، `ListRow` للجزئي، `KeyValue` للكيان، `<Button>` للخروج؛ 404 الجذر يبقى استثناءً موثقاً. | S | ui-ux-design-pro critique «Consistency» | `apps/client/src/components/account-overview.tsx`، `apps/admin/src/app/[locale]/(app)/risk/page.tsx`، `apps/client/src/app/[locale]/(public)/legal/[key]/page.tsx`، `apps/client/src/app/[locale]/(app)/more/page.tsx` |

### مشترك — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-001 | P0 حاجب | RTL | Tabs (Radix Tabs.Root) / Select (Radix Select) | تبويبات Radix تطبع dir="ltr" داخل الصفحات العربية فتنقلب شاشة الحساب كاملة إلى اتجاه LTR | `Tabs` يُصدَّر مباشرة من Radix (`RadixTabs.Root`) بلا `dir` ولا `DirectionProvider` في أيٍّ من التطبيقين؛ Radix يستدعي `useDirection(dir)` التي تعيد «ltr» افتراضياً وتكتبها كسمة `dir` على جذر التبويبات. النتيجة في العربية: شريط التبويبات ومحتواه (البطل، العدّادات الثلاثة، الرسم، الصفقات، القواعد، البيانات) يُرسم LTR داخل صفحة RTL — ترتيب التبويبات معكوس («نظرة» في أقصى اليسار بدل اليمين)، الهدف/اليومية/الإجمالية معكوسة، البطل محاذى يساراً. لقطة الجهاز الحقيقي account-iphone-15-pro-max.png تُظهر ذلك حرفياً. الأمر نفسه يصيب `Select` (27 من 29 استعمالاً بلا `dir`) و`Tabs` الإدارة الأربع. | packages/ui/src/components/tabs.tsx:6 `export const Tabs = RadixTabs.Root;` · apps/client/src/app/[locale]/(app)/accounts/[id]/page.tsx:109 `<Tabs defaultValue={tab}>` · packages/ui/src/components/select.tsx:36 `<RadixSelect.Root dir={dir} …>` (dir اختياري بلا افتراضي) · node_modules @radix-ui/react-direction useDirection: `return localDir \|\| globalDir \|\| Direction.LTR` · react-tabs: `Primitive.div { dir: direction }` · apps/admin settings/page.tsx:65, accounts/[id]/page.tsx:137, products/page.tsx:89, mail/[id]/page.tsx:41 · artifacts/real-device/ios/account-iphone-15-pro-max.png | لفّ شجرة التطبيق في `[locale]/layout.tsx` (العميل والإدارة) بـ `<DirectionProvider dir={dirOf(locale)}>` من `radix-ui`، أو اجعل `Tabs`/`Select`/`DropdownMenu` في @fpt/ui تقرأ الاتجاه من `document.dir`/سياق محلي وتمرّره. أضف اختباراً في ux-gates يفشل عند وجود `[dir=ltr]` داخل صفحة عربية. | S | ui-ux-pro-max §5 mobile-first / fpt-brand rule «RTL: logical properties only» · design-system § 5.8 | `packages/ui/src/components/tabs.tsx`، `packages/ui/src/components/select.tsx`، `apps/client/src/app/[locale]/layout.tsx`، `apps/admin/src/app/[locale]/layout.tsx` |

### مشترك — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-019 | P2 ثانوي | تقني | Tabs defaultValue | التبويب المختار لا ينعكس في العنوان: التحديث والرجوع والمشاركة تعيد التبويب الأول | صفحة الحساب تقرأ `?tab=` لكن Radix Tabs غير مضبوط (`defaultValue` فقط، لا `onValueChange` يحدّث الـ URL)؛ تبويبات الإدارة الأربع لا تقرأ ولا تكتب أي معامل. التحديث التلقائي/`router.refresh` بعد الإجراءات يبقي الحالة لكن الرجوع والروابط العميقة لا. | apps/client/src/app/[locale]/(app)/accounts/[id]/page.tsx:47 و:109 `<Tabs defaultValue={tab}>` · apps/admin/src/app/[locale]/(app)/settings/page.tsx:65 `<Tabs defaultValue="general">` · accounts/[id]/page.tsx:137 · products/page.tsx:89 | مكوّن `UrlTabs` صغير في كل تطبيق: `value` من `useSearchParams`، `onValueChange` → `router.replace({query:{tab}})` بلا scroll. | S | ui-ux-pro-max §9 state-preservation / deep-linking · web-interface-guidelines §Navigation & State «URL reflects state» | `packages/ui/src/components/tabs.tsx`، `apps/client/src/app/[locale]/(app)/accounts/[id]/page.tsx`، `apps/admin/src/app/[locale]/(app)/settings/page.tsx` |

### مشترك — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-037 | P3 صقل | هوية | حروف نصية كأيقونات: → * × | سهم نصي «→» بين سعر الفتح والسوق، و«*» للنتيجة الجزئية بتلميح hover فقط | الهوية تمنع رموز Unicode كأيقونات (verify-identity يبلّغ عنها). «→» يظهر في ثلاث شاشات داخل `num-ui`؛ «*» في المحاكي يحمل المعنى عبر `title` فقط (لا لمس). | apps/client/src/components/trades-panel.tsx:86 `${d.openPrice} → ${d.price}` · apps/client/src/components/account-overview.tsx:337 · accounts/[id]/breach/page.tsx:140 · apps/admin/src/app/[locale]/(app)/products/ProductsClient.tsx:594–596 `<span … title={t('partial')}>*</span>` | `<ArrowRight className="fpt-icon--sm fpt-icon--dir" aria-hidden>` أو كلمة «إلى»؛ للجزئي شارة `StatusPill` بكلمة «جزئي». | S | fpt-brand «no Unicode symbols as icons» · ui-ux-pro-max §4 no-emoji-icons | `apps/client/src/components/trades-panel.tsx`، `apps/client/src/components/account-overview.tsx`، `apps/admin/src/app/[locale]/(app)/products/ProductsClient.tsx` |

### مشترك — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-027 | P2 ثانوي | بصري | EquityChart <svg viewBox=358×200 width=100%> | نص الرسم ومحاوره يتضخمان مع عرض الحاوية (SVG يتمدد): محاور 12px تصبح ≈ 24–30px على المكتب | الرسم يُرسم بـ viewBox ثابت 358×200 و`width="100%"`؛ كل شيء داخله (نص المحاور 12، القيمة النهائية mono 15، سمك الخط 2، النقطة الحمراء r=4) يتضخم خطياً. في عمود العميل 720px يصبح النص ≈ 24px والخط 4px؛ في صفحة المخالفة بالإدارة أكبر. الرسم «يبدو مكبّراً» ويخالف المقياس الطباعي. | packages/ui/src/components/equity-chart.tsx:69 `width = 358, height = 200` · :122 `<svg viewBox={`0 0 ${width} ${height}`} width="100%" … style={{ … fontSize: 12 …}}>` · :158 `fontSize: 15` · apps/admin/src/app/[locale]/(app)/breaches/[id]/page.tsx:570 `<EquityChart … />` داخل عمود 1440 | قِس عرض الحاوية (ResizeObserver أو `useContainerWidth`) ومرّره كـ `width` فتبقى النصوص 12/15px فعلية، أو ارسم النصوص خارج الـ SVG بـ HTML فوق الرسم. | M | ui-ux-pro-max §5 breakpoint-consistency · §6 font-scale · dataviz (نص المحاور بحجم ثابت) | `packages/ui/src/components/equity-chart.tsx` |

### مشترك — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-047 | P3 صقل | تقني | className="text-body-1" | صنف text-body-1 غير معرّف في المقياس (لا يطبّق أي حجم) | المقياس يعرّف `text-body`/`text-body-2` فقط؛ `text-body-1` لا يولّد CSS، فالتعليمات ونص ملاحظة العميل يرثان 16px مصادفةً. دلالة خاطئة تُربك الصيانة. | apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/page.tsx:325 `<p className="m-0 text-body-1" …>` · apps/admin/src/app/[locale]/(app)/payments/[id]/page.tsx:587 · packages/ui/src/tokens.css:96–122 (لا `--text-body-1`) | `text-base`. | S | ui-ux-pro-max §6 text-styles-system | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/page.tsx`، `apps/admin/src/app/[locale]/(app)/payments/[id]/page.tsx` |

### مشترك — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-071 | P3 صقل | بصري | leading-relaxed / leading-snug للعربية | تباعد أسطر العربية ينزل تحت 1.75 في المساعدة والتنبيهات والحوارات | الجسم العربي 1.75 (PROPOSED؛ الحزمة 1.9). إجابات المساعدة `leading-relaxed` (1.625)، عناوين البطاقات/الحوارات/التنبيهات `leading-snug` (1.375)، نص التنبيه يرث 1.75 لكن العنوان لا. للعربية بخط Plex يوصى بـ ≥ 1.6 للجسم و≥ 1.3 للعناوين. | apps/client/src/app/[locale]/(public)/help/help-search.tsx:191 `leading-relaxed` · packages/ui/src/components/alert.tsx:45 `leading-snug` · dialog.tsx:51 · card.tsx:41 · packages/ui/src/components.css:271 `:lang(ar) body … { line-height: 1.75; }` | في العربية: `leading-relaxed → 1.75`، و`leading-snug` للعناوين فقط ≥ 1.3 (موجود)؛ عرّف `--leading-ar-body` واستعمله. | S | ui-ux-pro-max §6 line-height · fpt-brand Arabic line-height 1.9 | `apps/client/src/app/[locale]/(public)/help/help-search.tsx`، `packages/ui/src/components/alert.tsx` |

### مشترك — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-025 | P2 ثانوي | بصري | .fpt-chip مقابل .ui-chip · Filters.tsx مقابل FilterChips | شريحتان مختلفتان للفلتر المختار (أحمر مغسول في المساعدة، أبيض ممتلئ في الإدارة) ونسختان من كود الشرائح | صفحة المساعدة تستعمل `.fpt-chip` (المختار: غسل أحمر + حدّ أحمر) وتمرّر أصناف الحزمة مباشرة، بينما الإدارة تستعمل `.ui-chip` (المختار: تعبئة بلون النص). كما أن `Filters.tsx` في الإدارة يعيد كتابة علامات `ui-chips/ui-chip` بدل استعمال `FilterChips hrefFor` الموجود في @fpt/ui؛ أي تعديل على الشريحة يجب أن يُكرَّر في مكانين. | apps/client/src/app/[locale]/(public)/help/help-search.tsx:171 `className="fpt-chip min-h-touch shrink-0"` · brand/components/components.css:234 `.fpt-chip[aria-pressed="true"]{background:var(--fpt-brand-red-wash);border-color:var(--fpt-brand-red)…}` · apps/admin/src/components/Filters.tsx:58–64 (يعيد بناء ui-chip) مقابل packages/ui/src/components/filter-bar.tsx:29–56 `FilterChips` | في المساعدة استعمل `FilterChips onChange`؛ في الإدارة استعمل `FilterChips hrefFor linkComponent={Link}` وأبقِ Filters.tsx غلافاً رقيقاً للبحث. | S | ui-ux-pro-max §4 consistency · design-system § 4 FilterBar/FilterChips | `apps/client/src/app/[locale]/(public)/help/help-search.tsx`، `apps/admin/src/components/Filters.tsx`، `packages/ui/src/components/filter-bar.tsx` |

### مشترك — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-046 | P3 صقل | تقني | PageTitle eyebrow (ميت) · PageHeader eyebrow (حي) | خاصية eyebrow تُمرَّر في 8 صفحات إدارة ويُهملها PageTitle، بينما PageHeader العام ما زال يرسمها فوق العناوين | F1 أسقط السطر العلوي؛ `PageTitle` يتجاهل `eyebrow` لكن الصفحات تمرّر الترجمات (عمل ميت). `PageHeader` في @fpt/ui يرسم `.fpt-eyebrow` uppercase على المساعدة والقانوني، فتختلف رؤوس الصفحات العامة عن التطبيق. | apps/admin/src/components/PageTitle.tsx:4–15 (`eyebrow` «Kept for API compatibility») · orders/[id]/page.tsx:184 `eyebrow={t('eyebrow')}` · payments/page.tsx:429 · kyc/page.tsx:310 · settings/page.tsx:64 · products/page.tsx:287 · fraud/[id]/page.tsx:711 · packages/ui/src/components/page-header.tsx:25 `{eyebrow ? <p className="fpt-eyebrow mb-1!">…` · apps/client/src/app/[locale]/(public)/help/page.tsx:126 | احذف `eyebrow` من الاستدعاءات ومن PageTitle؛ وحّد PageHeader على ScreenTitle (بلا eyebrow). | S | ui-ux-pro-max §4 consistency | `apps/admin/src/components/PageTitle.tsx`، `packages/ui/src/components/page-header.tsx` |

### مشترك — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-043 | P3 صقل | بصري | EmptyState مقابل <p> رمادي | حالة الفراغ غير موحّدة: EmptyState في بعض القوائم وفقرة رمادية في أخرى، وجدول الموظفين بلا حالة فراغ أصلاً | design-system § 5.6: «كل قائمة لها EmptyState بجملة وإجراء». 20+ قائمة فرعية تستعمل `<p className="m-0 text-body-2 text-muted">` بلا أيقونة ولا إجراء، وقائمة الموظفين تمرّر DataTableRows بلا `empty` فتُرسم فراغاً. | apps/client/src/app/[locale]/(app)/payouts/page.tsx:37 · apps/client/src/components/notifications-list.tsx:20 · partners/page.tsx:304,326 · apps/admin/src/app/[locale]/(app)/page.tsx:105,122,131 · accounts/[id]/page.tsx:394,433 · risk/page.tsx:495,510,522 · settings/page.tsx:158,255 · staff/page.tsx:70–84 (لا empty) · packages/ui/src/components/data-table.tsx:82 `if (rows.length === 0) return <>{empty ?? null}</>` | `EmptyState size="md"` مع أيقونة القسم في كل قائمة، و`empty` إلزامي في DataTableRows (نوعياً). | S | ui-ux-pro-max §8 empty-states · design-system § 5.6 | `packages/ui/src/components/data-table.tsx`، `apps/admin/src/app/[locale]/(app)/staff/page.tsx`، `apps/client/src/app/[locale]/(app)/payouts/page.tsx` |

### مشترك — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-030 | P2 ثانوي | تقني | الرقم المهيمن .ui-hero المكتوب يدوياً | 13 نسخة يدوية من بطاقة الرقم المهيمن بدل مكوّن واحد — رمز العملة ثابت «USD» وdata-caption مفقودة والإدارة بعلامات مختلفة | `AccountHeroCard` يُستعمل في مكان واحد؛ بقية الشاشات تعيد كتابة `.ui-hero / .ui-hero__label / .ui-hero__value` يدوياً: العميل يضع `<i>USD</i>` حرفياً (9 مواضع، بلا `data-caption` التي يضعها المكوّن الأصلي، ويتجاهل `order.currency` إلا في الطلب)، والإدارة تضع `<Money size="2xl" quietCode>` فيُرسم رمز العملة بأسلوب آخر (0.55em داخل mono). أي تغيير في هوية الرقم المهيمن لن ينتشر. | apps/client/src/app/[locale]/(app)/payouts/page.tsx:42–50 `<span className="ui-hero__value"><bdi className="fpt-unit">…</bdi><i>USD</i></span>` · payouts/new/payout-form.tsx:193–200 و:278–284 · payouts/[id]/page.tsx:367–379 · shop/[productCode]/page.tsx:248–255 · partners/page.tsx:249–259 · checkout/[orderId]/transfer/page.tsx:233–239 · accounts/[id]/breach/page.tsx:79–89 · checkout-form.tsx:130–136 · packages/ui/src/components/account-hero.tsx:40–43 (`<i data-caption="true">{currency}</i>`) · apps/admin payouts/[id]/page.tsx:196–204 `<Money … size="2xl" quietCode />` | استخرج `HeroFigure {label, cents, currency, badge, children}` إلى @fpt/ui (يعيد استعمال CSS `.ui-hero`) واستبدل النسخ الـ 13؛ مرّر العملة من البيانات. | M | ui-ux-design-pro critique «Consistency» · design-system § 4 AccountHeroCard | `packages/ui/src/components/account-hero.tsx`، `apps/client/src/app/[locale]/(app)/payouts/page.tsx`، `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx`، `apps/admin/src/app/[locale]/(app)/payouts/[id]/page.tsx` |

### مشترك — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-072 | P3 صقل | بصري | disabled next · focus-management | زر «التالي» يُعطَّل بدل إظهار الخطأ، وبعد فشل الإرسال لا يُنقل التركيز إلى الخطأ | في خطوة المبلغ/البيانات يُعطَّل الزر الأساسي بلا سبب مرئي حتى يصح الإدخال (المستخدم لا يعرف لماذا). `ActionForm` يعرض Alert role=alert لكنه لا يركّز الحقل الخاطئ ولا الملخّص. | apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx:304 `disabled={!canNext}` · apps/admin/src/components/ActionForm.tsx:50–60 (لا focus بعد الخطأ) | زر مفعّل يظهر الخطأ المضمّن عند الضغط؛ `focus()` على أول حقل `aria-invalid` بعد الفشل. | S | ui-ux-pro-max §8 focus-management · web-interface-guidelines §Forms «Submit stays enabled» | `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx`، `apps/admin/src/components/ActionForm.tsx` |

### مشترك — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-013 | P2 ثانوي | بصري | QR SVG | رمز QR معكوس الألوان (وحدات بيضاء على أسود) — كثير من تطبيقات المصادقة لا تقرأه | العميل يولّد QR بـ `dark:'#FFFFFF', light:'#0A0A0A'`؛ الإدارة تضع الـ SVG في `.fpt-paper` لكنها تلغيه بـ `style={{background: panel, color: text-primary}}` فتصير الوحدات بيضاء على #141414. معيار QR يفترض وحدات داكنة على فاتح؛ الكاميرات تفشل كثيراً مع العكس. | apps/client/src/app/[locale]/(app)/profile/forms.tsx:220 `color: { dark: '#FFFFFF', light: '#0A0A0A' }` (وهكس خام في الكود) · apps/admin/src/app/[locale]/(auth)/enrol/page.tsx:181–182 `className="fpt-paper …" style={{ background: 'var(--fpt-ground-panel)', color: 'var(--fpt-text-primary)' }}` | ارسم الـ QR على أرضية الورق (`--fpt-paper-panel`) بوحدات `--fpt-paper-text`؛ في الإدارة احذف الـ style الذي يلغي `.fpt-paper`؛ في العميل مرّر لوني الورق من الرموز لا هكس. | S | ui-ux-pro-max §1 color-contrast · fpt-brand «paper ground for documents» | `apps/client/src/app/[locale]/(app)/profile/forms.tsx`، `apps/admin/src/app/[locale]/(auth)/enrol/page.tsx` |

### مشترك — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-022 | P3 صقل | تقني | Input attributes | حقول البريد وIBAN/SWIFT بلا `spellCheck={false}` و`autoCapitalize`، وIBAN بلا `inputMode` | التدقيق الإملائي يضع خطوطاً حمراء تحت البريد وIBAN على Android/macOS، ولوحة iOS تبدأ بحرف كبير في حقول نصية عادية. `spellCheck={false}` مستخدم في الصرف لحقول crypto/fiper فقط. | apps/client/src/app/[locale]/(auth)/auth-form.tsx:42, :61 (بريد بلا spellCheck/autoCapitalize)؛ apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx:163 (iban), :166 (swift) بلا `spellCheck` ولا `autoCapitalize="characters"`؛ apps/admin/src/app/[locale]/(auth)/signin/SignInForm.tsx:128. | أضف `spellCheck={false} autoCapitalize="none"` لكل بريد/اسم مستخدم/رمز، و`autoCapitalize="characters" spellCheck={false}` لـ IBAN/SWIFT، و`inputMode="text"` مع `pattern` للـ IBAN. يمكن جعل `Input mono` يضبطها افتراضياً. | S | web-interface-guidelines § Forms | `apps/client/src/app/[locale]/(auth)/auth-form.tsx`، `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx`، `apps/admin/src/app/[locale]/(auth)/signin/SignInForm.tsx`، `packages/ui/src/components/input.tsx` |

### مشترك — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-018 | P3 صقل | وصول | packages/ui/src/components/otp-input.tsx + field.tsx | تسمية حقل الرمز `htmlFor` تشير إلى `div role="group"` غير قابل للتسمية: النقر على التسمية لا يركّز الصندوق الأول و`aria-invalid` على مجموعة | `Field` يولّد `<label htmlFor={id}>` و`OtpInput` يضع `id` على `div role=group`. العناصر غير القابلة للتسمية لا ترتبط بـ `label`، فلا تنقل التركيز ولا يقرأها قارئ الشاشة كتسمية للمجموعة (المجموعة بلا `aria-labelledby`). الصناديق نفسها موسومة فردياً («الرقم 1 من 6») فالأثر محدود. | packages/ui/src/components/otp-input.tsx:241 (`<div role="group" id={f.id} … aria-invalid={f.invalid}`); packages/ui/src/components/field.tsx:100 (`<label htmlFor={id}`). | في `OtpInput`: `aria-labelledby={labelId}` على المجموعة (مرّر معرّف التسمية من `Field` عبر السياق) و`onClick` على التسمية ينقل التركيز إلى `refs.current[0]`؛ أزل `aria-invalid` من المجموعة واتركه على الصناديق. | S | web-interface-guidelines § Forms (labels clickable) | `packages/ui/src/components/otp-input.tsx`، `packages/ui/src/components/field.tsx` |

### مشترك — 404 الجذر · global-error

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-080 | P3 صقل | تقني | روابط مثبتة على /ar ونص ثنائي اللغة | صفحات 404 والخطأ الجذرية تعيد المستخدم إلى /ar دائماً وتعرض العربية والإنجليزية معاً | خارج مقطع اللغة لا قاموس، فالصفحتان تثبّتان `lang="ar"` ورابط `/ar/accounts` و`/ar`؛ مستخدم إنجليزي يُنقل إلى العربية. مبرَّر تقنياً لكن يمكن قراءة الكوكي/`Accept-Language`. | apps/client/src/app/not-found.tsx:21,34 `<html lang="ar" dir="rtl">` / `href="/ar/accounts"` · apps/admin/src/app/not-found.tsx:222,226 `href="/ar"` · apps/client/src/app/global-error.tsx:425 | اقرأ كوكي اللغة (NEXT_LOCALE) في not-found (headers متاح) واختر النص والرابط. | S | ui-ux-pro-max §9 deep-linking · web-interface-guidelines §Locale | `apps/client/src/app/not-found.tsx`، `apps/admin/src/app/not-found.tsx` |

### مشترك — PartnerRow (الهبوط، المزيد) · 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-058 | P3 صقل | تقني | <img> بلا أبعاد / بلا lazy | شعار cTrader بلا width/height (انزياح تخطيط) وصورة الإثبات بلا loading=lazy | صورة cTrader تعتمد على CSS للارتفاع فقط فتسبب CLS حتى يصل الملف؛ صورة الإثبات (قد تكون 5MB) أسفل الطية تُحمَّل فوراً. | packages/ui/src/components/partner-row.tsx:26 `<img src={partnerSrc} alt="cTrader" className="fpt-partner__logo" decoding="async" />` · apps/admin/src/app/[locale]/(app)/payments/[id]/page.tsx:150 `<img src={`/api/proofs/${latest.id}`} … className="max-h-[70vh] w-full object-contain" />` | `width/height` من نسبة الملف الرسمي؛ `loading="lazy"` وإطار بنسبة ثابتة للإثبات. | S | web-interface-guidelines §Images · ui-ux-pro-max §3 image-dimension | `packages/ui/src/components/partner-row.tsx`، `apps/admin/src/app/[locale]/(app)/payments/[id]/page.tsx` |

### مشترك — Placeholders

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-062 | P3 صقل | نص | shop.checkout.couponPlaceholder «اختياري» · accounts.searchPlaceholder «المرجع أو البريد أو الاسم» · audit.actionPlaceholder «ابحث في الإجراءات (مثل: السحب)» · faq.search «ابحث في مركز المساعدة» | نصوص placeholder لا تنتهي بـ «…» وواحدة تعمل كتلميح لا كمثال | دليل Vercel: الـ placeholder مثال ينتهي بثلاث نقاط. «اختياري» حالة الحقل لا مثال عليه. | ar.json:L668 / en.json:L668; ar.json:L2028 / en.json:L2028; ar.json:L1307 / en.json:L1307 | «مثال: FPT20…»، «المرجع أو البريد أو الاسم…»، «ابحث في الإجراءات…»، و«(اختياري)» في التسمية لا في الـ placeholder. | S | web-interface-guidelines Forms | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### مشترك — RuleMeter 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-036 | P3 صقل | أداء | brand/components/components.css (ملف العلامة — للإبلاغ فقط) | انتقال `transition: width` على شريط القياس يحرّك خاصية تخطيط لا `transform` (غير مناسب للمركّب) | لا `transition: all` في أي ملف (جيد)، لكن شريط العداد ينتقل على `width` فيعيد التخطيط في كل إطار؛ على الهواتف مع تحديث حي كل ثانية قد يسبب jank طفيفاً. الملف من حزمة العلامة المقفلة. | grep -o 'transition:[^;}]*' brand/components/components.css → `transition:width var(--fpt-motion-duration-surface) …`؛ packages/ui/src/components/rule-meter.tsx:75 (`style={{ width: … }}`). | للإبلاغ إلى إصدار العلامة التالي: استخدم `transform: scaleX(ratio)` مع `transform-origin: var(--fpt-start)` (RTL-aware) بدل `width`؛ في التطبيق لا تغيير حتى يُصدر الـ brand. | S | web-interface-guidelines § Animation | `brand/components/components.css`، `packages/ui/src/components/rule-meter.tsx` |

### مشترك — Sheet 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-034 | P3 صقل | تقني | packages/ui/src/components.css + sheet.tsx/dialog.tsx | لا `overscroll-behavior: contain` على الطبقات: تمرير الورقة السفلية يسحب الصفحة خلفها (تأثير المطاط) | Radix Dialog يقفل تمرير الجسم لكن لا يمنع السلسلة داخل محتوى قابل للتمرير؛ لا `overscroll-behavior` في أي ملف CSS. كذلك لا `-webkit-tap-highlight-color` مضبوط عمداً (`touch-action: manipulation` موجود). | grep `overscroll-behavior\|-webkit-tap-highlight` في packages/ui/src/*.css apps/*/src/app/globals.css → 0؛ packages/ui/src/components.css:112-114 (touch-action)؛ packages/ui/src/components/sheet.tsx:31. | أضف `overscroll-behavior: contain` إلى محتوى Sheet/Dialog/Select viewport/DropdownMenu content، و`-webkit-tap-highlight-color: transparent` على `button, a, [role=button]` مع الاعتماد على حالة `:active` الموجودة. | S | web-interface-guidelines § Touch & Interaction | `packages/ui/src/components.css`، `packages/ui/src/components/sheet.tsx`، `packages/ui/src/components/dialog.tsx` |

### مشترك — account-credentials, admin mail-detail

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-019 | P3 صقل | وصول | CopyButton / CopyBadge → IconButton label «نسخ» → «تم النسخ» | تأكيد النسخ يتغيّر في aria-label فقط ولا يُعلَن لقارئ الشاشة | القياس بعد النقر: label «نسخ» بلا تغيير مرئي للقارئ، liveTexts=[«»]. تغيير aria-label لعنصر مركَّز لا يُنطق في معظم القارئات؛ ولا توجد منطقة حيّة. كما يُكرّر title نفس النص (نطق مزدوج في بعض القارئات). | .e2e/a11y-client.log phone/desktop:account:copyFeedback; apps/client/src/components/copy-button.tsx; apps/admin/src/components/CopyBadge.tsx; packages/ui/src/components/icon-button.tsx:449 | عند النجاح استدعِ toast({title: t('copied')}) (منطقة polite موجودة) أو أضف span sr-only aria-live=polite بجانب الزر؛ احذف title من IconButton أو اجعله اختيارياً. | S | ui-ux-pro-max `contextual-live-badge-updates`; WIG «Async updates need aria-live» | `apps/client/src/components/copy-button.tsx`، `apps/admin/src/components/CopyBadge.tsx`، `packages/ui/src/components/icon-button.tsx` |

### مشترك — checkout (روابط المستندات), account-credentials (فتح cTrader), admin integrations

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-026 | P3 صقل | وصول | <a target="_blank"> بلا إشعار | روابط تفتح نافذة جديدة دون إخبار المستخدم | روابط الشروط/المخاطر/الاسترداد في صندوق الموافقة وأزرار فتح cTrader وروابط إدارية تفتح تبويباً جديداً بلا نص «يفتح في نافذة جديدة» (WCAG 3.2.5 / G201). | apps/client/src/app/[locale]/(app)/checkout/[orderId]/checkout-form.tsx:42; apps/client/src/components/platform-open-buttons.tsx:23; apps/admin/src/app/[locale]/(app)/payouts/[id]/page.tsx:141 | أضف span sr-only مترجماً «(يفتح في نافذة جديدة)» وأيقونة external-link aria-hidden؛ أو افتح مستندات الشروط في ورقة داخل الصفحة. | S | WIG Content; WCAG 3.2.5 (advisory) | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/checkout-form.tsx`، `apps/client/src/components/platform-open-buttons.tsx` |

### مشترك — client

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-010 | P2 ثانوي | وصول | @fpt/ui Tabs (Radix) بلا dir ولا DirectionProvider | تبويبات Radix في الواجهة العربية تعمل بافتراض LTR: الأسهم معكوسة اتجاهياً | لا يوجد DirectionProvider في أي تخطيط ولا يُمرَّر dir إلى Tabs (grep: صفر نتائج)، بينما DropdownMenu الإداري يمرّره. Radix يفترض ltr فيصبح ArrowRight = «التالي في DOM» أي بصرياً إلى اليسار في RTL. القياس على حساب بملف إنجليزي (ltr) صحيح؛ القياس الوحيد على ملف عربي (desktop، demo03) أظهر ArrowRight/ArrowLeft لا يحرّكان التركيز عن «نظرة» — لم يُكرَّر للتأكد. | packages/ui/src/components/tabs.tsx (لا dir); grep DirectionProvider = 0; .e2e/a11y-client.log desktop:account:tabsArrows (ar) مقابل .e2e/a11y-quick.ts (en) | لفّ <body> في كل تطبيق بـ <DirectionProvider dir={dirOf(locale)}> من radix-ui، أو مرّر dir تلقائياً في TabsList/Select/Dropdown من document.dir. | S | accessibility.md § ARIA Patterns Tabs (Arrow keys); ui-ux-pro-max `keyboard-nav` | `apps/client/src/app/[locale]/layout.tsx`، `apps/admin/src/app/[locale]/layout.tsx`، `packages/ui/src/components/tabs.tsx` |

### مشترك — client

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-005 | P2 ثانوي | وصول | section > h3 (CardTitle الافتراضي h3) تحت h1 مباشرة | قفز مستويات العناوين h1 → h3 في 9 شاشات (axe heading-order) | axe heading-order (moderate) 36 عقدة عبر 4 مشاريع. CardTitle افتراضه h3 ويُستعمل مباشرة تحت PageTitle (h1)، وفي integrations تظهر h4 داخلها؛ في صفحة حذف الحساب h2 ثم h3 داخل القسم الآخر (en) بعد h2 الأول. | gates-*.json heading-order targets: section[data-testid="legal-delete-ar"] > h3, admin-kyc h3, admin-settings section:nth-child(1) … h3, admin-integrations section[data-testid="integration-myfatoorah"] … h3; packages/ui/src/components/card.tsx:40 | اجعل CardTitle افتراضه h2، ومرّر as="h3" فقط داخل أقسام h2؛ عدّل h4 في integrations إلى المستوى التالي للعنوان الأب. | S | ui-ux-pro-max `heading-hierarchy` | `packages/ui/src/components/card.tsx`، `apps/admin/src/app/[locale]/(app)/integrations/page.tsx`، `apps/client/src/app/[locale]/(public)/legal/delete-account/page.tsx` |

### مشترك — footer 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-002 | P0 حاجب | هوية | config/legal.ts entity.legalName «Fiper Global UAE», entity.parent «Fiper Global MAU» · common.entity · certificates.verifiedBy · legal.entity | اسم كيان قانوني غير مقرَّر يُطبع في تذييل التطبيق والشهادات والكشوف والإيصالات | config/legal.ts يحدد legalName = «Fiper Global UAE» وparent = «Fiper Global MAU» بلا وسم DRAFT، ويُعرض في footer.tsx (السطر 30)، certificate-card.tsx (الجهة المُصدِرة)، صفحة التحقق من الشهادة («صادرة عن {entity}»)، كشف الدفعة، الإيصال وصفحات الوثائق. مصفوفة النصوص التنظيمية: «كيان التسجيل لـ FPT: قرار مطلوب 1»، وكيانات الشركة الأم (Fiper Global LLC UAE FZ، Fiper Global MAU LTD) «لا تُورَّث ولا تُلصق على أسطح FPT بالقياس». الاسمان المستخدمان ليسا حتى الاسمين القانونيين الكاملين للأم. كما يكرّر مفتاح i18n «common.entity» الاسم خلافاً للقرار الحاكم 6 («لا مكان آخر في الكود يكتب اسم الكيان»)، وهو مفتاح غير مستخدم. | config/legal.ts:8-9; apps/client/src/components/footer.tsx:30; apps/client/src/components/certificate-card.tsx:30,81; apps/client/src/app/[locale]/(public)/verify/[code]/page.tsx:45; ar.json:L5 / en.json:L5; brand/regulatory-matrix.md § 1 (Parent blocks R1–R11 … not inherited) | وسم legalName/parent بـ DRAFT صراحة أو تفريغهما حتى يصدر القرار 1، وإخفاء سطر الجهة المُصدِرة/الكيان من الأسطح العامة (الشهادة، كشف الدفعة، الإيصال، التذييل) إلى حينه. حذف common.entity من القاموس. | M | brand regulatory-matrix § 3 (1) / governing decision 6 | `config/legal.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `apps/client/src/components/footer.tsx`، `apps/client/src/components/certificate-card.tsx` |

### مشترك — global-error (حدّ الخطأ الجذري)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-045 | P3 صقل | تقني | apps/client/src/app/global-error.tsx + apps/admin/src/app/global-error.tsx | صفحة الخطأ الجذرية بـ `lang="ar" dir="rtl"` ثابتين ونص ثنائي اللغة: الزائر الإنجليزي يرى تخطيطاً من اليمين؛ لا وسوم theme-color/color-scheme | مقبول كحدّ أخير (لا مزوّد ترجمة)، لكن يمكن قراءة `document.documentElement.lang` قبل التصيير لتعيين `dir`؛ لا تسرّب تقني (لا رسالة ولا stack) — جيد. | apps/client/src/app/global-error.tsx:61-66; apps/admin/src/app/global-error.tsx:71-76. | `const [dir] = useState(() => (typeof document !== 'undefined' && document.documentElement.lang === 'en' ? 'ltr' : 'rtl'))` وطبّقه على `<html>`؛ أضف `<meta name="color-scheme" content="dark">`. | S | fpt-brand + web-interface-guidelines § Locale | `apps/client/src/app/global-error.tsx`، `apps/admin/src/app/global-error.tsx` |

### مشترك — signin (client), admin-signin

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-038 | P3 صقل | وصول | Input autoFocus على حقل البريد | التركيز التلقائي عند التحميل يتجاوز عنوان الصفحة والتعليمات لقارئ الشاشة | القياس: أول Tab في صفحة الدخول يذهب إلى كلمة المرور لأن البريد مركَّز سلفاً؛ قارئ الشاشة يبدأ من الحقل دون سماع h1 «تسجيل الدخول» والـ lead. مقبول عرفاً في صفحات الدخول، ويُذكر للتوثيق. | .e2e/a11y-client.log phone:signin:firstTab = signin-password; apps/client/src/app/[locale]/(auth)/auth-form.tsx:58 | إبقاؤه مع التأكد أن aria-describedby للبريد يشير إلى الـ lead، أو إزالة autoFocus على الهاتف. | S | accessibility.md § Focus Management | `apps/client/src/app/[locale]/(auth)/auth-form.tsx` |

### مشترك — «(اختياري)» بعد اسم مؤنث

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-049 | P3 صقل | نص | auth.password.totpTitle «المصادقة الثنائية (اختياري)» · shop.transfer.note «ملاحظة (اختياري)» · paymentsAdmin.approveNote «ملاحظة (اختياري)» ↔ health.alerts.noteHint «اختيارية» (صحيحة) | عدم مطابقة في التأنيث | «المصادقة» و«ملاحظة» مؤنثتان. | ar.json:L539 / en.json:L539; ar.json:L618 / en.json:L618; ar.json:L2296 / en.json:L2296 | «(اختيارية)». | S | Arabic grammar | `packages/i18n/src/messages/ar.json` |

### مشترك — «توقيت دبي» بصيغ متعددة

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-060 | P3 صقل | نص | common.dubaiTime «بتوقيت دبي» · dashboard.resetDubai «00:00 دبي» · partners.becomeBody «(09:00 دبي)» · partners.rules.weekly «09:00 بتوقيت دبي» · admin.events.reset_daily «منتصف ليل دبي» · breaches.col.at «الوقت (دبي)» · affiliatesAdmin.model.approval «الاثنين 09:00 دبي» | صيغة واحدة للمنطقة الزمنية مطلوبة |  | ar.json:L761 / en.json:L761; ar.json:L1124 / en.json:L1124; ar.json:L1151 / en.json:L1151 | «00:00 بتوقيت دبي» دائماً في النصوص، و«(دبي)» فقط في رؤوس الأعمدة. | S | consistency | `packages/i18n/src/messages/ar.json` |

### مشترك — «لم يمكن»

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-056 | P3 صقل | نص | admin.error.body «لم يمكن تحميل الصفحة» · notifications.max_accounts_refused.body «لم يُمكن إنشاء الحساب» · products.sim.noResults «لم يُمكن تقييم» ↔ client.error.body «تعذّر تحميل الصفحة» | صيغة ثقيلة مقابل «تعذّر» المستخدمة في العميل |  | ar.json:L1534 / en.json:L1534; ar.json:L1449 / en.json:L1449; ar.json:L285 / en.json:L285 | «تعذّر تحميل الصفحة»، «تعذّر إنشاء الحساب الجديد»، «تعذّر تقييم أي حالة حساب». | S | Arabic style | `packages/i18n/src/messages/ar.json` |

### مشترك — «مرحلة التقييم» و«مدة التقييم»

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-086 | P3 صقل | نص | client.rules.minDaysValue «في مرحلة التقييم» · mail.expiry_warning.title «مدة التقييم» · dashboard.evaluationEnds «ينتهي التقييم» · client.rule.deadline «نهاية التقييم» · notifications.ending_expired.body «انتهت صلاحية الحساب» | انتهاء المدة بصيغ متعددة و«صلاحية الحساب» غير دقيقة |  | ar.json:L1373 / en.json:L1373; ar.json:L254 / en.json:L254; ar.json:L786 / en.json:L786 | «نهاية مدة التقييم» للتسمية، و«انتهت مدة التقييم دون بلوغ الهدف؛ أُغلق الحساب.» للإشعار. | S | consistency | `packages/i18n/src/messages/ar.json` |

### مشترك — أزرار مبهمة

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-029 | P2 ثانوي | نص | adminAuth.signin.submit «متابعة» · admin.reauth.continue «متابعة» · mail.kyc_result.accepted.button «متابعة» · payouts.flow.next «متابعة» · auth.verify.submit «تأكيد» · common.confirm / client.actions.confirm / admin.actions.confirm «تأكيد» | «متابعة» و«تأكيد» حيث يلزم فعل محدَّد — خاصة زر دخول الموظفين وأزرار الحوارات الخطرة | زر نموذج دخول الموظفين نصّه «متابعة» بدل «تسجيل الدخول»؛ زر إعادة التحقق في منطقة الإجراءات الخطرة «متابعة»؛ بريد قبول الهوية ينتهي بزر «متابعة» بينما المعجم يقترح «افتح حسابك الممول»؛ «تأكيد» العام يُستخدم في حوارات الحذف/الإقفال التي يجب أن تسمّي الفعل. | ar.json:L1762 / en.json:L1762; ar.json:L1616 / en.json:L1616; ar.json:L164 / en.json:L164; ar.json:L518 / en.json:L518; ar.json:L11 / en.json:L11; brand/glossary.md § 7 «افتح حسابك الممول» | adminAuth.signin.submit → «تسجيل الدخول»؛ admin.reauth.continue → «تأكيد الإجراء» أو تمرير اسم الفعل؛ kyc button → «افتح حسابك المموَّل»؛ auth.verify.submit → «تأكيد الرمز»؛ حوارات الخطر تمرّر اسم الفعل إلى الزر (مثل «إقفال الحساب»). | S | ui-ux-pro-max §8 (specific verbs) / design:ux-copy CTA | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### مشترك — إعادة الضبط اليومية

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-017 | P2 ثانوي | نص | client.rule.nextReset «التصفير القادم» · mail.account_ready.note «وقت التصفير اليومي» · health.reset.title «التصفير اليومي» · dashboard.reset «إعادة الضبط التالية» · client.rule.resetAt «يُعاد الضبط» · settings.reset.title «إعادة التعيين اليومية» · accounts.state.nextReset «إعادة التعيين القادمة» · accounts.actions.recompute «إعادة حساب التعيين» · admin.events.reset_daily «أُعيد ضبط حدود اليوم» | الـ reset اليومي بثلاث تسميات: التصفير / إعادة الضبط / إعادة التعيين — و«التصفير» مضلِّل | لا شيء يُصفَّر عند منتصف الليل (E0 := E)؛ «التصفير» يوحي بإعادة الرصيد إلى الصفر. على شاشة الحساب يظهر «التصفير القادم» و«إعادة الضبط التالية» معاً لنفس الموعد. | ar.json:L254 / en.json:L254; ar.json:L760 / en.json:L760; ar.json:L2890 / en.json:L2890; ar.json:L108 / en.json:L108 | «إعادة الضبط اليومية» / «موعد إعادة الضبط» في كل المفاتيح؛ إلغاء «التصفير» و«إعادة التعيين» لهذا المعنى. | S | terminology consistency | `packages/i18n/src/messages/ar.json` |

### مشترك — إعادة المحاولة

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-042 | P2 ثانوي | نص | common.retry «أعد المحاولة» · admin.error.retry «أعد المحاولة» · client.error.retry / client.actions.retry / shop.ret.retry / payouts.kyc.retry / profile.kyc.retry «إعادة المحاولة» · common.error / shop.transfer.errors.generic «حاول مرة أخرى» · shop.checkout.paymentFailed / shop.ret.failedBody «المحاولة مجدداً» · auth.errors.generic «يرجى إعادة المحاولة» · auth.errors.sessionExpired «مجدداً» | Retry بأربع صيغ | المعجم INHERITED: «إعادة المحاولة». الصيغ الأخرى تتوزع على الشاشات نفسها. | ar.json:L8 / en.json:L8; ar.json:L314 / en.json:L314; ar.json:L17 / en.json:L17; ar.json:L687 / en.json:L687 | «إعادة المحاولة» للأزرار (أو «أعد المحاولة» إن اعتُمد الأمر في TXT-028) و«… ثم أعد المحاولة» في الجمل، بلا «حاول مرة أخرى» و«مجدداً». | S | brand glossary § 2 (Retry) | `packages/i18n/src/messages/ar.json` |

### مشترك — إقفال الحساب 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-018 | P2 ثانوي | نص | notifications.ending_inactive.body «أُقفل» · admin.events.breach_inactive «أُغلق» · mail.inactivity_warning.title «سيُغلق حسابك» · dashboard.status.inactive.body «أُقفل الحساب» · status.closed «مغلق» · dashboard.status.closed.title «مُقفل» · accounts.actions.close «إقفال الحساب» · deletionsAdmin.approveTitle «إغلاق الحساب» · breach.events.closes «إقفال الصفقات» · mail.breach.body «أُغلقت الصفقات» | إغلاق/إقفال يتبادلان للحدث نفسه (إقفال الحساب، إقفال الصفقات) | الحدث الواحد (إنهاء حساب لعدم النشاط) يُقال فيه «أُقفل» في الإشعار و«أُغلق» في سجل الإدارة و«سيُغلق» في البريد؛ والحالة «مغلق» في status.closed و«مُقفل» في dashboard.status.closed.title. | ar.json:L1377 / en.json:L1377; ar.json:L1667 / en.json:L1667; ar.json:L39 / en.json:L39; ar.json:L871 / en.json:L871 | «إقفال» للصفقات (مصطلح التداول) و«إغلاق» للحساب والطلب والجلسة؛ تطبيق القاعدة على كل المفاتيح. | S | terminology consistency | `packages/i18n/src/messages/ar.json` |

### مشترك — التشكيل غير المتسق

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-048 | P3 صقل | نص | status.funded «ممول» / dashboard.status.funded.title «مموَّل» / products.rule.minDays «مموّل» · profile.kycStatus.approved «موثَّق» / payoutsAdmin.kyc.approved «موثّقة» · risk.description «حيّاً» / risk.live «حياً» · client.rule.limit «الحد» / breach.limit «الحدّ» · dashboard.status.closed.title «مُقفل» / accounts.actions.status.closed «مقفل» · client.landing.phase1Body «حقق» / dashboard.status.evaluation.body «حقّق» | الكلمة الواحدة بثلاثة أشكال تشكيل (شدّة وفتحة / شدّة / بلا تشكيل) | التشكيل الجزئي حسن لرفع اللبس، لكنه يجب أن يكون متّسقاً للكلمة الواحدة عبر الشاشات. | ar.json:L35 / en.json:L35; ar.json:L851 / en.json:L851; ar.json:L1864 / en.json:L1864; ar.json:L2163 / en.json:L2163; ar.json:L2183 / en.json:L2183 | قاعدة تشكيل واحدة تُسجَّل في المعجم (مثل: «مموَّل»، «موثَّق»، «حدّ»، «مُقفل») وتطبيقها بالبحث والاستبدال. | S | Arabic typography | `packages/i18n/src/messages/ar.json` |

### مشترك — التطويل وأداة التعريف قبل الأرقام

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-047 | P3 صقل | RTL | certificates.issuedTo «صادرة لـ» · dashboard.status.expired.body «مدة الـ 31 يوماً» · payouts.reasons.cycle_not_elapsed «دورة الـ 14 يوماً» · affiliatesAdmin.model.tiers «لـ 1–9 مبيعات» · shop.checkout.crypto «الدفع بـ USDT» · mail.common.copyright «تابعة لـ Fiper Global» | حرف جرّ معلّق بتطويل قبل اسم، و«الـ» قبل رقم | «صادرة لـ» ثم اسم العميل في سطر منفصل؛ «الـ 31 يوماً» صياغة عامية. التطويل قبل الكلمات اللاتينية (لـ Fiper) مقبول كعرف لكن يُفضَّل تجنّبه. | ar.json:L1077 / en.json:L1077; ar.json:L862 / en.json:L862; ar.json:L967 / en.json:L967 | «صادرة باسم»؛ «انتهت مدة التقييم (31 يوماً) دون بلوغ الهدف.»؛ «لم تنتهِ دورة 14 يوماً بعد.»؛ «لعدد 1–9 عمليات بيع». | S | Arabic style | `packages/i18n/src/messages/ar.json` |

### مشترك — التهجئة البريطانية

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-051 | P3 صقل | نص | client.screen.help / client.more.help «Help Center» ↔ client.footer.help / faq.title / faq.eyebrow / settings.entity.help «Help centre» · partners.title «Partner program» / LEGAL programDescriptor «program» ↔ mail.account_ready.note / mail.breach.body «programme» / affiliatesAdmin.emptyBody «partner programme» / settings.affiliates.body · products.eyebrow «Catalogue» · adminAuth.enrol «enrol» · admin.events.user_anonymised «anonymised» | Center/centre وprogram/programme في النصوص نفسها | المعجم (الموقع الملاحَظ): “Help Center”. البريطانية تغلب على البقية. | ar.json:L252 / en.json:L252; ar.json:L290 / en.json:L290; ar.json:L1121 / en.json:L1121; ar.json:L114 / en.json:L114 | اعتماد تهجئة واحدة (الأمريكية لمطابقة الموقع: Center, program, catalog, enroll, anonymized) أو البريطانية — وتسجيلها في المعجم. | S | brand glossary § 1 (Help Center) | `packages/i18n/src/messages/en.json` |

### مشترك — الدخول

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-063 | P3 صقل | بصري | autoFocus (12 موضعاً) | autoFocus على الهاتف يفتح لوحة المفاتيح فوراً ويقفز بالشاشة في 12 نموذجاً | كل خطوة في طلب الدفعة وكل شاشة مصادقة تركّز حقلاً تلقائياً؛ على الهاتف يعني ذلك لوحة مفاتيح فورية تغطي نصف الشاشة قبل قراءة العنوان والـ lead. | apps/client/src/app/[locale]/(auth)/auth-form.tsx:42,61,84,114,135 · apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx:191,238,243,252,261 · apps/admin/src/app/[locale]/(auth)/CodeForm.tsx:21 | autoFocus على المكتب فقط (`(pointer: fine)`) أو احذفه من خطوات الدفعة. | S | web-interface-guidelines §Touch «autoFocus sparingly — desktop only» | `apps/client/src/app/[locale]/(auth)/auth-form.tsx`، `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx` |

### مشترك — الدفعات (تنقّل + صفحة + نموذج + بريد + شهادة)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-009 | P1 رئيسي | نص | client.nav.payouts «الدفعات» · payouts.title «السحوبات» · payouts.new «طلب سحب» · dashboard.primary.requestPayout «اطلب دفعة» · client.states.processed «تم الصرف» · client.certificate.amount «الدفعة المصروفة» · mail.payout_requested.title «طلب السحب» · mail.payout_paid.title «تمت معالجة دفعتك» · admin.nav.payouts «السحوبات» · settings.payoutChannels.title «قنوات الصرف» · mailPreview.templates.payout_paid «صرف السحب» | أربعة مصطلحات لمفهوم واحد (payout): الدفعة/الدفعات، السحب/السحوبات، الصرف، سحب الأرباح — على شاشات متجاورة | تبويب التنقّل يقول «الدفعات»، وعنوان الصفحة التي يفتحها «السحوبات»، وزرها «طلب سحب»، وزر لوحة الحساب «اطلب دفعة»، والحالة النهائية «تم الصرف»، والشهادة «الدفعة المصروفة» و«شهادة سحب الأرباح»، والبريد «طلب السحب» ثم «تمت معالجة دفعتك». المعجم (PROPOSED) يوحّد على «الصرف» للفعل («طلب الصرف»، «تم الصرف»، «دورة الصرف») و«الدفعة/الدفعات» للاسم («كشف الدفعة»، «الدفعات» في شريط التبويب). «السحب» غير وارد في المعجم لهذا المعنى ويتعارض مع «السحب» = drawdown (TXT-010). علامة التبويب في الإدارة «صرف السحب» تجمع المصطلحين في عبارة واحدة. | ar.json:L322 / en.json:L322; ar.json:L954 / en.json:L954; ar.json:L957 / en.json:L957; ar.json:L736 / en.json:L736; ar.json:L346 / en.json:L346; ar.json:L134 / en.json:L134; ar.json:L144 / en.json:L144; ar.json:L3386 / en.json:L3386; brand/glossary.md § 2 «طلب الصرف», § 4 «تم الصرف», «دورة الصرف», § 7 «الدفعات» | اعتماد زوج واحد: الاسم «الدفعة/الدفعات» (payout) والفعل «صرف/طلب الصرف/تم الصرف»؛ استبدال كل «سحب/سحوبات/سحب الأرباح» بهذا المعنى (76 مفتاحاً تقريباً، القائمة في TXT-SUMMARY) في العميل والإدارة والبريد والشروط. | M | brand glossary § 2 & § 4 / web-interface-guidelines Content & Copy (one term per concept) | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `packages/db/scripts/seed-data/legal.ts` |

### مشترك — الروابط المضمّنة (16 موضعاً)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-057 | P3 صقل | بصري | hover:underline بلا @media (hover:hover) | تحويم Tailwind على الروابط المضمّنة غير مقيّد بوسيط hover — يلتصق بعد اللمس على iOS | `.ui-row` و`.ui-inline-link` في CSS مقيّدان بـ `@media (hover: hover)`، لكن 16 رابطاً في JSX تستعمل `hover:underline`/`hover:text-ink` مباشرة (Tailwind v4 يقيّدها افتراضياً بـ `@media (hover:hover)` فقط عند تفعيل الإعداد الافتراضي — تحقّق من `hover` variant في v4: مقيّد افتراضياً). الأثر محدود لكنه غير متسق مع CSS الحزمة. | apps/client/src/app/[locale]/(app)/accounts/page.tsx:76 `hover:underline` · apps/client/src/components/rules-table.tsx:22 · packages/ui/src/components.css:391,466 (`@media (hover: hover)`) | تأكد أن `hover` في Tailwind v4 يبقى مقيّداً (الافتراضي) ووثّقه؛ أو استعمل `ui-inline-link` بدل الأصناف المتناثرة. | S | web-interface-guidelines §Hover & Interactive States | `apps/client/src/app/[locale]/(app)/accounts/page.tsx`، `apps/client/src/components/rules-table.tsx` |

### مشترك — الشروط + كتاب القواعد ↔ معجم العلامة

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-006 | P1 رئيسي | هوية | seed-data/legal.ts terms §4 · packages/rulebook/src/rules.ts payoutMaxPerTransactionCents · brand/glossary.md § 3 «الحد الأدنى / الأقصى للصرف» | الحد الأقصى للدفعة: 10,000 USD في الشروط وكتاب القواعد مقابل $5,000 في معجم العلامة | الشروط (مسودة): «بما بين 100 و10,000 دولار لكل عملية» / “between 100 USD and 10 000 USD per transaction”، وكتاب القواعد: payoutMaxPerTransactionCents = 10_000_00. المعجم § 3 يسجّل «الحد الأدنى / الأقصى للصرف: $100 / $5,000 (OBSERVED — site data)». تعارض في واقعة منتج يجب أن تكون «حرفية». كما تكتب النسخة الإنجليزية «10 000» بفاصل مسافة خلافاً لتنسيق العلامة «10,000 USD». | packages/db/scripts/seed-data/legal.ts terms bodyEn §4 / bodyAr §4; packages/rulebook/src/rules.ts:19; brand/glossary.md § 3 (row «الحد الأدنى / الأقصى للصرف») | قرار واحد (الموقع أم المنصة) ثم مواءمة المعجم وكتاب القواعد والشروط. كتابة المبلغ «10,000 USD» في اللغتين. | S | brand glossary § 3 / fpt-prop-platform rulebook (Payout) | `packages/db/scripts/seed-data/legal.ts`، `packages/rulebook/src/rules.ts`، `brand/glossary.md` |

### مشترك — الكل

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-044 | P3 صقل | وصول | <a href="#main"> skip link (sr-only → fixed عند التركيز) | رابط التخطي يعمل (العميل والإدارة) لكنه 28 px ارتفاعاً ويُرسم فوق الرأس اللاصق | القياس: أول Tab يُظهر «الانتقال إلى المحتوى» rect 117×28 أعلى الرأس (z فوق الرأس فهو مرئي)، main#main tabIndex=-1 موجود. إيجابي عموماً؛ الارتفاع 28 px للوحة المفاتيح فقط. | .e2e/a11y-client.log desktop:home:tabs[0]; .e2e/a11y-admin.log *:outline skip; packages/ui/src/components/app-shell.tsx:690-692 | لا إجراء إلزامي؛ اختيارياً min-h-11 وتباين خلفية elevated. | S | ui-ux-pro-max `skip-links` | `packages/ui/src/components/app-shell.tsx` |

### مشترك — اللوحة 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-031 | P2 ثانوي | نص | client.home.liveUpdates «يتحدّث كل ثانية» · dashboard.status.evaluation.next «تتحدّث المقاييس كل ثانية» · risk.autoRefresh «تتحدث كل 5 ثوانٍ» · health.description «تتحدّث كل 10 ثوانٍ» | «يتحدّث/تتحدّث» تُقرأ «يتكلّم/تتكلّم» | الفعل المقصود «تُحدَّث» (مبني للمجهول من حدَّث)؛ «يتحدّث» بالشكل المكتوب يعني «يتكلّم». في risk.autoRefresh بلا شدّة أصلاً. | ar.json:L253 / en.json:L253; ar.json:L843 / en.json:L843; ar.json:L2164 / en.json:L2164; ar.json:L3003 / en.json:L3003 | «يُحدَّث كل ثانية» / «تُحدَّث المقاييس كل ثانية» / «تُحدَّث كل 5 ثوانٍ». | S | Arabic grammar | `packages/i18n/src/messages/ar.json` |

### مشترك — المكوّنات المشتركة

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-036 | P3 صقل | هوية | Lucide | أيقونات Lucide موحّدة عبر @fpt/ui/icons (70 ملفاً) — مع ثلاثة شوارد: استيراد مباشر، سبينر بسمك 2.5، وانحراف إصدار عن الحزمة | الهوية تثبّت Lucide v0.468؛ الحزمة تستعمل `lucide-react ^1.63.0` (إصدار كبير أحدث بأشكال معدّلة لبعض الأيقونات). `print-button.tsx` يستورد `Printer` من `lucide-react` مباشرة متجاوزاً المجموعة المنتقاة. `Spinner` SVG يدوي بسمك 2.5 بينما عائلة الأيقونات 1.75/2. | packages/ui/package.json `"lucide-react": "^1.63.0"` · brand/README.md § I «Lucide v0.468.0 only» · apps/client/src/components/print-button.tsx:2 `import { Printer } from 'lucide-react';` · packages/ui/src/components/spinner.tsx:23–24 `strokeWidth="2.5"` | أضف `Printer` إلى icons.ts، ثبّت إصدار lucide-react الموثّق في الهوية (أو وثّق الانحراف كـ PROPOSED)، واجعل سمك السبينر 2. | S | ui-ux-pro-max §4 icon-style-consistent · fpt-brand § I | `packages/ui/src/icons.ts`، `apps/client/src/components/print-button.tsx`، `packages/ui/src/components/spinner.tsx`، `packages/ui/package.json` |
| VIS-044 | P3 صقل | تقني | مكوّنات ميتة ومكررة | مكوّنات مكررة أو غير مستعملة تبقى في الحزمة والتطبيقين (رسم بياني ثانٍ بمحاور 9.5px mono، FilterBar، AccountGauges، EquityCurve، Finance، useToast لا يُستدعى أبداً) | `apps/client/src/components/charts.tsx` يحوي EquityChart ثانٍ (غير مستعمل) بمحاور mono 9.5px — لو استُعمل لخرق أرضيتين. في الإدارة: FilterBar.tsx، AccountGauges.tsx (Gauge القديم)، EquityCurve.tsx (محاور mono 11px)، Finance.tsx كلها بلا استعمال. @fpt/ui يصدّر ResponsiveTable/Gauge/RingGauge/Progress/Switch/Tooltip/DataList ولا يستعملها التطبيقان (DataList في المحاكاة فقط). `ToastProvider` مركّب لكن `useToast` لا يُستدعى في أي شاشة — تغذية النجاح تعتمد على تنبيهات مضمّنة فقط. | apps/client/src/components/charts.tsx:31–164 (`fontSize: 9.5` :114) · grep «from '@/components/charts'» → trades-panel فقط (PnlHeatmap) · apps/admin/src/components/{FilterBar,AccountGauges,EquityCurve,Finance}.tsx → 0 استيراد · grep useToast → 0 · packages/ui/src/index.ts | احذف المكررات أو انقلها إلى @fpt/ui كمكوّنات مستعملة فعلاً (Delta/NearestLimit)؛ استعمل toast للنجاح بعد الإجراءات القصيرة (نسخ، حفظ). | S | ui-ux-design-pro critique «Consistency» · ui-ux-pro-max §8 success-feedback | `apps/client/src/components/charts.tsx`، `apps/admin/src/components/FilterBar.tsx`، `apps/admin/src/components/AccountGauges.tsx`، `apps/admin/src/components/EquityCurve.tsx`، `apps/admin/src/components/Finance.tsx` |

### مشترك — النبرة: ضمير المتكلم الجمعي مقابل البناء للمجهول

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-027 | P2 ثانوي | نص | auth.signup.lead «سنرسل» · auth.verify.lead «أرسلنا» · shop.transfer.referenceHint «حتى نطابق» · shop.transfer.waitingBody «نراجع إثباتك» · shop.ret.browserReturn «استلمنا عودتك… ننتظر» · shop.ret.stillWaiting «ما زلنا ننتظر» · payouts.kyc.pending «نحدّث هذه الصفحة» · profile.delete.requested «سنخبرك» · legalDelete.deleted.d1 «تحقّقنا منه» · mail.payout_requested.title «استلمنا» · mail.manual_proof_received.body «وصلنا… نراجعه» ↔ mail.payout_requested.subject «تم استلام» · profile.emailChange.body «يُرسل رمز تحقق» · mail.payout_paid.body «أُرسل إليك» · mail.payout_requested.body «تُنجَز المراجعة» | سجلّان متناوبان: «نحن» الودّي والمبني للمجهول الرسمي — أحياناً في الرسالة الواحدة | بريد طلب الدفعة: الموضوع «تم استلام طلب السحب» والعنوان «استلمنا طلب السحب الخاص بك» والجسم «تُنجَز المراجعة». المعجم يطلب «سجلاً خليجياً رسمياً واحداً». الإنجليزية تحمل الاختلاف نفسه (“We received…” / “The review is completed by…”). | ar.json:L133 / en.json:L133; ar.json:L134 / en.json:L134; ar.json:L135 / en.json:L135; ar.json:L717 / en.json:L717; ar.json:L1014 / en.json:L1014 | قاعدة نبرة واحدة تُسجَّل في المعجم: مخاطبة العميل بصيغة الأمر/الغائب المؤسسي والمبني للمجهول («تُراجَع»، «يصلك»، «أُرسل»)، و«نحن» فقط في جمل الالتزام (مثل «لا نطلب كلمات المرور»). مراجعة المفاتيح المذكورة وفقها. | M | brand glossary (Gulf-standard formal register) / design:ux-copy | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### مشترك — بيانات الدخول إلى cTrader

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-023 | P2 ثانوي | نص | mail.common.labels.accountNumber «رقم الحساب» · shop.ret.accountNumber «رقم الحساب» · dashboard.credentials.accountNumber «رقم الحساب» ↔ accounts.prov.login «رقم الدخول» · (ميّتة) dashboard.credentials.login «اسم الدخول» · shop.ret.login «اسم الدخول» · mail.common.labels.login «رقم الحساب» / EN «Login» vs «Account number» | رقم دخول cTrader بثلاث تسميات (رقم الحساب / اسم الدخول / رقم الدخول) والإنجليزية بين Login وAccount number | المعجم § 7: «رقم الحساب / Account number». الإدارة تقول «رقم الدخول» والمفاتيح الميتة تقول «اسم الدخول» (وهو رقم لا اسم). | ar.json:L813 / en.json:L813; ar.json:L2074 / en.json:L2074; ar.json:L800 / en.json:L800; ar.json:L62 / en.json:L62; brand/glossary.md § 7 «رقم الحساب · الخادم · المنصة» | «رقم الحساب» / “Account number” في كل مكان؛ حذف المفاتيح الميتة (TXT-059). | S | brand glossary § 7 | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### مشترك — بيانات الدخول — أزرار فتح cTrader

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-035 | P2 ثانوي | نص | dashboard.credentials.open.desktop AR = «Desktop (Windows)» · mail.account_ready.note «(Web وDesktop وMac وiOS وAndroid)» | تسمية «Desktop (Windows)» غير مترجمة في العربية | اسم الزر العربي مطابق للإنجليزي حرفياً، والبريد يسرد «Web وDesktop وMac…» بواو عطف ملتصقة بكلمات لاتينية. | ar.json:L818 / en.json:L818; ar.json:L108 / en.json:L108 | «سطح المكتب (Windows)»؛ وفي البريد: «أزرار فتح cTrader (الويب، سطح المكتب، Mac، iOS، Android)». | S | i18n completeness | `packages/i18n/src/messages/ar.json` |

### مشترك — تذكير

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-053 | P3 صقل | نص | client.landing.honesty «لا يضمن FPT» ↔ notifications.account_closed.body «أقفلت FPT» · certificates.publicLead «FPT أصدرت» · finance.refundsPolicy «تعتمد FPT» | FPT مذكّر في جملة ومؤنث في البقية | الاتساق يقتضي اعتبار FPT مؤنثاً (شركة) أو مذكراً (برنامج) في كل مكان. | ar.json:L400 / en.json:L400; ar.json:L1381 / en.json:L1381; ar.json:L1081 / en.json:L1081 | اعتماد التأنيث (شركة/منصة) وتعديل honesty: «لا تضمن FPT أرباحاً ولا تقدّم…». | S | Arabic grammar | `packages/i18n/src/messages/ar.json` |

### مشترك — تراكيب تحتاج إعادة ترتيب

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-074 | P3 صقل | نص | breaches.reverseBody «يجب أن يقدّم موظفان مختلفان بصلاحية الإلغاء سبباً كلٌّ منهما» · coupons.newBody «…وحصر بمنتجات اختيارياً» · settings.payoutChannels.body «حتى توفر المسار» · profile.kyc.manualReview «يدوياً من فريق FPT» · faq.lead «قواعد FPT وإجاباتها» | ترتيب كلمات وحروف جرّ غير سليمة |  | ar.json:L2206 / en.json:L2206; ar.json:L1945 / en.json:L1945; ar.json:L2871 / en.json:L2871; ar.json:L1306 / en.json:L1306 | «يجب أن يقدّم كلٌّ من موظفَين مختلفَين بصلاحية الإلغاء سبباً»؛ «(اختياري: حدّ استخدام، صلاحية، ربط بشريك، حصر بمنتجات)»؛ «حتى يتوفر المسار»؛ «من قِبل فريق FPT»؛ «قواعد FPT والأسئلة الشائعة». | S | Arabic grammar | `packages/i18n/src/messages/ar.json` |

### مشترك — تسميات بصيغة فعل أو ناقصة

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-075 | P3 صقل | نص | client.rule.resetAt «يُعاد الضبط» (تسمية موعد) · profile.created «تسجيل الدخول» (تسمية وقت) · risk.col.dailyLeft «متبقي اليوم» · risk.col.overallLeft «متبقي إجمالي» | تسميات حقول تُقرأ كأفعال أو بلا تعريف |  | ar.json:L254 / en.json:L254; ar.json:L1266 / en.json:L1266; ar.json:L2182 / en.json:L2182 | «موعد إعادة الضبط»، «وقت الدخول»، «المتبقي اليومي» / «المتبقي الإجمالي». | S | Arabic style | `packages/i18n/src/messages/ar.json` |

### مشترك — تسميات حدود الخسارة (AR

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-043 | P2 ثانوي | نص | AR: client.rule.daily «الخسارة اليومية» · client.finance.dailyLabel / client.rules.daily / dashboard.daily «الخسارة اليومية القصوى» · client.rule.overall «الخسارة الإجمالية» · client.finance.maxLabel / dashboard.max «الخسارة الإجمالية القصوى» · admin.breachKind.max_loss / products.rule.max / accounts.gauge.max «الخسارة القصوى» · engine.kind.max_loss «الخسارة الأقصى» · notifications.ending_max_loss.title «حدّ الخسارة الأقصى» — EN: «Daily Drawdown» / «Daily loss» / «daily drawdown» · «Max Drawdown» / «Maximum loss» / «Max loss» / «Overall loss» / «overall drawdown» | القاعدتان الأساسيتان بثلاث تسميات عربية وخمس إنجليزية | المعجم § 3: «الخسارة اليومية القصوى / Daily Drawdown (Max Daily Loss)» و«الخسارة الإجمالية القصوى (الخسارة القصوى) / Max Drawdown (Max Loss)». التطبيق يضيف «الخسارة الإجمالية» و«الخسارة الأقصى» و«Overall loss» و«Maximum loss». | ar.json:L254 / en.json:L254; ar.json:L357 / en.json:L357; ar.json:L254 / en.json:L254; ar.json:L1735 / en.json:L1735; ar.json:L1467 / en.json:L1467; brand/glossary.md § 3 | تسمية طويلة واحدة («الخسارة اليومية القصوى» / «الخسارة الإجمالية القصوى») وقصيرة واحدة («اليومية» / «الإجمالية») في العميل والإدارة؛ الإنجليزية: Daily Drawdown / Max Drawdown (طويلة) وDaily / Overall (قصيرة). | S | brand glossary § 3 | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### مشترك — تعليم كمقروء

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-055 | P3 صقل | نص | dashboard.markRead «تعليم كمقروء» · dashboard.markAllRead · profile.notificationsPage.markAllRead · fraud.markReviewed «تعليم كمُراجَعة» · health.alerts.acknowledgeBody «يُعلّم التنبيه كمقروء» (EN: as seen) | «تعليم» تعني التدريس؛ والإقرار بالتنبيه يُترجم «كمقروء» بينما الإنجليزية “as seen” | الصيغة الشائعة «وضع علامة مقروء». وacknowledge = «تأكيد الاطلاع». | ar.json:L795 / en.json:L795; ar.json:L2472 / en.json:L2472; ar.json:L3071 / en.json:L3071 | «وضع علامة «مقروء»» / «وضع علامة «مُراجَعة»» / «يؤكد اطلاعك على التنبيه باسمك». | S | Arabic style | `packages/i18n/src/messages/ar.json` |

### مشترك — تقرير المخالفة 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-014 | P1 رئيسي | نص | engine.kind.max_loss «الخسارة الأقصى» · breach.human.daily_loss «حدّ الخسارة اليومي» · breach.human.max_loss «حدّ الخسارة الإجمالي» ↔ breach.kinds.daily_loss «حدّ الخسارة اليومية» · admin.events.breach_max_loss «حدّ الخسارة الإجمالية» | خطأ مطابقة: «الخسارة الأقصى» (المؤنث يأخذ «القصوى»)، وتذبذب التذكير/التأنيث في «حدّ الخسارة اليومي/اليومية» | engine.kind.max_loss = «الخسارة الأقصى» — «الخسارة» مؤنثة فصفتها «القصوى». في breach.human.daily_loss «حدّ الخسارة اليومي» (الصفة للحدّ) بينما بقية المفاتيح تكتب «حدّ الخسارة اليومية/الإجمالية» (الصفة للخسارة)؛ الصياغتان صحيحتان نحوياً لكن اختلافهما في التقرير نفسه يبدو خطأً. | ar.json:L1467 / en.json:L1467; ar.json:L913 / en.json:L913; ar.json:L913 / en.json:L913; ar.json:L932 / en.json:L932 | «الخسارة القصوى»؛ وتوحيد الصياغة على «حدّ الخسارة اليومية» و«حدّ الخسارة الإجمالية» (الصفة للخسارة) في كل المفاتيح. | S | Arabic grammar (agreement) | `packages/i18n/src/messages/ar.json` |

### مشترك — حالات التحقق من الهوية

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-045 | P2 ثانوي | نص | profile.kycStatus.pending «جارٍ» · payoutsAdmin.kyc.pending / kycAdmin.userStatus.pending «قيد التحقق» · admin.events.kyc_result_pending «قيد المعالجة» · kycAdmin.caseStatus.pending «معلّق» · profile.kycStatus.approved «موثَّق» · payouts.kyc.approved «تم التحقق من الهوية» · mail.common.states.verified «تم التحقق» · payoutsAdmin.kyc.approved «موثّقة» · kycAdmin.userStatus.approved «موثَّقة» | حالة KYC الواحدة بأربع كلمات للانتظار وثلاث للقبول | في الإدارة «قيد التحقق» و«معلّق» و«قيد المعالجة» لنفس الحالة حسب الصفحة. | ar.json:L1277 / en.json:L1277; ar.json:L2428 / en.json:L2428; ar.json:L1698 / en.json:L1698; ar.json:L1278 / en.json:L1278; ar.json:L1016 / en.json:L1016 | «قيد التحقق» / «تم التحقق» / «لم يُقبل» / «لم يبدأ» في كل المواضع. | S | terminology consistency | `packages/i18n/src/messages/ar.json` |

### مشترك — حالات طلب الدفعة

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-022 | P2 ثانوي | نص | payouts.status.requested «مطلوب» · admin.payoutStatus.requested «مطلوب» ↔ shop.transfer.required «مطلوب» · adminAuth.required «مطلوب» · accounts.po.requested «المطلوب» (= المبلغ) | «مطلوب» تعني «required» في الحقول و«requested» في حالة الدفعة | شارة الحالة «مطلوب» بجوار حقول عليها علامة «مطلوب» (إلزامي) — المعنى الملتبس يظهر في صفحة الدفعات والإدارة معاً. | ar.json:L1022 / en.json:L1022; ar.json:L614 / en.json:L614; ar.json:L2114 / en.json:L2114 | حالة الطلب: «تم الطلب» أو «مُقدَّم»؛ الحقول الإلزامية: «إلزامي». | S | web-interface-guidelines Content & Copy | `packages/i18n/src/messages/ar.json` |

### مشترك — حالة الأحرف في العناوين الإنجليزية

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-052 | P3 صقل | نص | Title Case: client.rules.target «Profit Target», client.rules.daily «Daily Drawdown», client.rules.minDays «Minimum Trading Days», dashboard.daily/max/target, shop.size «Account Size», shop.title «Choose Your Challenge», client.landing.howTitle «How It Works» ↔ Sentence case: client.rule.target «Profit target», client.finance.legendTarget, accounts.gauge.target, client.screen.shop «Choose your challenge», partners.rules.title «How it works», certificates.size «Account size» | عناوين مطابقة بأسلوبين (Title Case من الموقع وSentence case في التطبيق) | النصوص الملاحَظة من الموقع تُنقل بـ Title Case بينما الأشقاء داخل التطبيق بـ Sentence case؛ «Choose Your Challenge» و«Choose your challenge» لنفس الشاشة. | ar.json:L588 / en.json:L588; ar.json:L252 / en.json:L252; ar.json:L404 / en.json:L404; ar.json:L254 / en.json:L254 | Sentence case لكل عناوين التطبيق والأزرار؛ Title Case يبقى للشعارات الحرفية فقط (§ 5). | S | web-interface-guidelines Content & Copy | `packages/i18n/src/messages/en.json` |

### مشترك — حالة الحساب + تقرير المخالفة

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-011 | P1 رئيسي | نص | status.breached «مخروق» · dashboard.status.breached.title «مخالفة» · admin.events.accountStatus.breached «مخالِف» · client.states.breached «تجاوز الحد» · dashboard.primary.viewBreach «تقرير الإيقاف» · breach.eyebrow «تقرير المخالفة» · mail.breach.button «تقرير التجاوز» | حالة التجاوز بأربع كلمات (مخروق/مخالفة/مخالِف/تجاوز الحد) والتقرير بثلاثة أسماء | «مخروق» (status.breached) كلمة غير مستعملة لحساب؛ «مخالِف» تصف العميل لا الحساب؛ «مخالفة» اسم فعل بينما بقية الحالات صفات؛ المعجم § 4 يعتمد «تجاوز الحد». تقرير واحد يُسمّى «تقرير الإيقاف» على زر اللوحة، و«تقرير المخالفة» في الصفحة، و«تقرير التجاوز» في البريد. | ar.json:L36 / en.json:L36; ar.json:L856 / en.json:L856; ar.json:L1657 / en.json:L1657; dashboard.primary.viewBreach; ar.json:L914 / en.json:L914; ar.json:L115 / en.json:L115; brand/glossary.md § 4 «تجاوز الحد» | الحالة: «تجاوز الحد» في كل مكان (status/ dashboard/ admin events). التقرير: «تقرير التجاوز» موحّداً (زر اللوحة، عنوان الصفحة، البريد، engine.report.title). «مخالفة» تُحفظ لمعناها القانوني فقط (الاستراتيجيات المحظورة). | S | brand glossary § 4 | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### مشترك — حالة الطلب 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-019 | P2 ثانوي | نص | status.provisioning «قيد التزويد» · dashboard.status.provisioning.title «قيد التجهيز» · admin.events.accountStatus.provisioning «قيد التجهيز» · shop.steps.provision «التزويد» · shop.orderStatus.provisioned «مُزوَّد» · orders.orderStatus.provisioned «مجهَّز» · purchases.status.provisioned «سُلّم الحساب» · admin.result.granted «التزويد في الطابور» · health.components.provisioner «التزويد» | provisioning بثلاث ترجمات: التزويد / التجهيز / التسليم | الطلب نفسه يُقال عنه «مُزوَّد» في المتجر، «مجهَّز» في الإدارة، «سُلّم الحساب» في المشتريات. «مُزوَّد» و«التزويد» غير مفهومين للعميل. | ar.json:L32 / en.json:L32; ar.json:L836 / en.json:L836; ar.json:L578 / en.json:L578; ar.json:L1985 / en.json:L1985; ar.json:L3268 / en.json:L3268 | للعميل: «قيد التجهيز» / «تم التسليم» (تسليم الحساب)؛ للإدارة يجوز «التجهيز» كاسم للعملية. إلغاء «التزويد/مُزوَّد». | S | terminology consistency | `packages/i18n/src/messages/ar.json` |

### مشترك — حالة الطلب في الإدارة

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-082 | P3 صقل | نص | orders.orderStatus.pending «معلّق» ↔ shop.orderStatus.pending / purchases.status.pending «بانتظار الدفع» · auth.errors.suspended «موقوف» | «معلّق» للطلب غير المدفوع توحي بالتعليق/الإيقاف |  | ar.json:L1981 / en.json:L1981; ar.json:L578 / en.json:L578 | «بانتظار الدفع» في الإدارة أيضاً. | S | consistency | `packages/i18n/src/messages/ar.json` |

### مشترك — رأس الصفحة (الشعار داخل رابط)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-017 | P3 صقل | وصول | apps/client/src/components/shell.tsx + packages/ui/src/components/logo.tsx | نص بديل مكرر: `<img alt="Fiper Pro Traders">` داخل رابط يحمل `aria-label` بالاسم نفسه (axe image-redundant-alt — الفشل الوحيد في Lighthouse Accessibility 99) | قارئ الشاشة يقرأ «Fiper Pro Traders، رابط، Fiper Pro Traders». يتكرر على كل شاشات العميل (رأس Shell) وعلى الهبوط. | artifacts/ux-audit-F4/lighthouse/client-signin.json → audits.image-redundant-alt: `<img src="/brand/fpt-logo-icon-rounded.jpeg" alt="Fiper Pro Traders" …>`؛ apps/client/src/components/shell.tsx:52 (`aria-label={LEGAL.entity.brandFull}`)؛ packages/ui/src/components/logo.tsx:49. | مرّر `title=""`/`decorative` إلى `Logo` حين يكون داخل رابط موسوم، فيصبح `alt=""`؛ أو احذف `aria-label` من الرابط واترك alt الصورة. أعد Lighthouse ليصبح Accessibility 100. | S | web-interface-guidelines § Accessibility | `apps/client/src/components/shell.tsx`، `packages/ui/src/components/logo.tsx`، `apps/admin/src/components/shell/AdminShell.tsx` |

### مشترك — رسائل الخطأ بلا سبب أو خطوة تالية

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-030 | P2 ثانوي | نص | errors.invalid_coupon «هذا الكوبون غير صالح» · errors.not_found «غير موجود» · common.error «حدث خطأ. حاول مرة أخرى.» · client.error.title «حدث خطأ ما» · errors.invalid_code «الرمز غير صحيح أو انتهت صلاحيته» · payouts.form.errors.iban «رقم IBAN غير صالح.» · admin.error.body «لم يمكن تحميل الصفحة» | أخطاء تقول «غير صالح/حدث خطأ» دون سبب أو ما يفعله المستخدم | الكوبون قد يكون منتهياً أو معطّلاً أو مقصوراً على منتج آخر أو مستنفد الاستخدام — الإدارة تملك هذه الأسباب لكن العميل يقرأ «غير صالح». رمز التحقق «غير صحيح أو انتهت صلاحيته» بلا «اطلب رمزاً جديداً». «غير موجود» بلا عودة. | ar.json:L48 / en.json:L48; ar.json:L42 / en.json:L42; ar.json:L45 / en.json:L45; ar.json:L1000 / en.json:L1000; ar.json:L17 / en.json:L17 | صياغة سبب + خطوة: «انتهت صلاحية هذا الكوبون / لا ينطبق على هذا المنتج / استُنفد استخدامه — تحقق من الرمز أو تابع بلا خصم»؛ «الرمز غير صحيح أو انتهت صلاحيته. اطلب رمزاً جديداً.»؛ «رقم IBAN غير صالح — تحقق من طوله وبلده (مثل AE07 0331 …)». | M | ui-ux-pro-max §8 error-clarity | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### مشترك — سقف ربح الأخبار

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-076 | P3 صقل | نص | notifications.news_cap.title «حُدَّ ربح نافذة الأخبار» · notifications.news_cap.body «خُصم ما زاد عن 1 %» · admin.events.decision.news_profit_cap «طُبّق سقف ربح نافذة الأخبار» · settings.news.rule «يُحدّ ربحها» | cap بثلاثة أفعال (حُدَّ / سقف / خُصم) و«حُدَّ» صعبة القراءة |  | ar.json:L1404 / en.json:L1404; ar.json:L1729 / en.json:L1729 | «طُبّق سقف ربح نافذة الأخبار» للعنوان و«خُصم ما زاد عن 1 %» للتفصيل. | S | consistency | `packages/i18n/src/messages/ar.json` |

### مشترك — شارات حالة الحساب

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-020 | P2 ثانوي | نص | status.passed «اجتياز» · client.states.passed «اجتاز التقييم» · dashboard.status.passed.title «تم الاجتياز» · admin.events.accountStatus.passed «اجتاز» · status.funded «ممول» · dashboard.status.funded.title «مموَّل» · status.inactive «خامل» · dashboard.status.inactive.title «غير نشط» · status.expired/dashboard.status.expired.title «منتهٍ» · status.purchased «تم الشراء» · admin.events.accountStatus.purchased «مشترى» | كلمة الحالة الواحدة تختلف بين status.* وdashboard.status.*.title وadmin.events.accountStatus.* | status.* تُستخدم للشارات العامة وdashboard.status.* للبطاقة؛ النتيجة «اجتياز» في شارة و«تم الاجتياز» في بطاقة الحالة نفسها. «خامل» (status.inactive) وصف غريب لحساب أُغلق لعدم النشاط، و«مشترى» صيغة ثقيلة. | ar.json:L34 / en.json:L34; ar.json:L846 / en.json:L846; ar.json:L38 / en.json:L38; ar.json:L866 / en.json:L866; ar.json:L1657 / en.json:L1657; apps/client/src/components/status.tsx:9-16 | جدول واحد لكلمات الحالة (المعجم § 4): تم الشراء · قيد التجهيز · قيد التقييم · اجتاز التقييم · حساب ممول · تجاوز الحد · انتهت المدة · غير نشط · مغلق — ويُشتق منه status.* وdashboard.status.*.title وadmin.events.accountStatus.* معاً. | S | brand glossary § 4 | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### مشترك — صياغات إنجليزية ركيكة

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-066 | P3 صقل | نص | mail.breach.body «crossed a rule of the programme» · mail.expiry_warning.body «Objectives not met by then close the evaluation.» · shop.ret.browserReturn «Your return from {provider} was received.» · shop.transfer.errors.not_pending «no longer waits for a proof» · shop.steps.choose «Choice» · orders.signatureInvalid «signature INVALID» | تراكيب غير طبيعية وحروف كبيرة صارخة |  | ar.json:L114 / en.json:L114; ar.json:L157 / en.json:L157; ar.json:L704 / en.json:L704; ar.json:L579 / en.json:L579; ar.json:L2015 / en.json:L2015 | “broke a rule of the program”; “If the objectives are not met by then, the evaluation ends.”; “You are back from {provider}.”; “is no longer waiting for a proof”; “Choose”; “signature invalid”. | S | design:ux-copy | `packages/i18n/src/messages/en.json` |

### مشترك — صيغة أزرار الإجراء (أمر مقابل مصدر)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-028 | P2 ثانوي | نص | client.home.open «فتح الحساب» ↔ purchases.openAccount «افتح الحساب» · common.retry «أعد المحاولة» ↔ client.actions.retry / client.error.retry «إعادة المحاولة» ↔ common.error «حاول مرة أخرى» ↔ shop.checkout.paymentFailed «المحاولة مجدداً» · client.tz.use «استخدم {zone}» ↔ client.timezone.use «استخدام {zone}» · profile.open «فتح» ↔ purchases.openReceipt «افتح الإيصال» · auth.signin.submit «تسجيل الدخول» ↔ shop.signInToBuy «سجّل الدخول للشراء» | الإجراء الواحد مرة بصيغة الأمر ومرة بالمصدر | «فتح الحساب» (اللوحة) و«افتح الحساب» (المشتريات) لنفس الرابط؛ «أعد المحاولة» و«إعادة المحاولة» و«حاول مرة أخرى» لنفس الزر في ثلاث شاشات. المعجم INHERITED: «إعادة المحاولة» = Retry؛ وأزرار الموقع الملاحَظة بصيغة الأمر (ابدأ، احصل، قارن). | ar.json:L253 / en.json:L253; ar.json:L3282 / en.json:L3282; ar.json:L8 / en.json:L8; ar.json:L314 / en.json:L314; ar.json:L17 / en.json:L17; client.tz.use; ar.json:L275 / en.json:L275 | قاعدة: الأزرار الأساسية وأزرار الحوار بصيغة الأمر (افتح، أعد المحاولة، استخدم، سجّل الدخول)، وعناصر التنقّل/العناوين بالمصدر (تسجيل الدخول كعنوان صفحة). توحيد الأزواج المذكورة فوراً. | S | brand glossary § 2 / ui-ux-pro-max buttons | `packages/i18n/src/messages/ar.json` |

### مشترك — عام

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-041 | P3 صقل | بصري | قيم المسافات | قيم خارج مقياس 4pt وقيم عشوائية [..] متناثرة (6/10/14px، 1.5px، 22px، 26px) | المقياس الملزم 4·8·12·16·20·24·32·48. المستعمل: `gap-1.5` ×15 (6px)، `gap-2.5` ×4 (10)، `gap-0.5` ×7 (2)، `p-3.5` (14)، `py-1.5` ×3؛ وفي CSS `.ui-hero gap:6px`، `.ui-kv gap:3px`، `.ui-row gap:14px`، `.ui-meter padding:14px 0`؛ قيم عشوائية: `border-[1.5px]` ×4، `size-[22px]`، `rounded-[6px]`، `h-[26px]`، `p-[3px]`، `translate-x-[3px]/[21px]`، `w-[236px]`، `h-[720px]`، `min-h-[480px]`، `min-h-[320px]`. (حزمة الهوية نفسها تستعمل 6/10/14 كثيراً — فالانحراف موروث جزئياً.) | grep عدّ: gap-1.5 ×15، gap-2.5 ×4، p-3.5 ×1 · packages/ui/src/components.css:373 `.ui-hero { gap: 6px }`، :388 `.ui-row { gap: 14px }` · checkbox.tsx:32 `size-[22px] rounded-[6px] border-[1.5px]` · switch.tsx:20,27 · lang-switch.tsx:22 `p-[3px]` · enrol/page.tsx:181 `w-[236px]` · mail/[id]/page.tsx:367 `h-[720px]` | طبّع إلى 4/8/12/16 حيث لا يفرض مقاس عنصر (22px صندوق الخانة مقبول)، وعرّف `--spacing-*` للقيم المتبقية في tokens.css بدل القيم المعقوفة. | M | ui-ux-pro-max §5 spacing-scale · ui-ux-design-pro critique «Does it follow the scale?» | `packages/ui/src/components.css`، `packages/ui/src/components/checkbox.tsx`، `packages/ui/src/components/switch.tsx` |
| VIS-042 | P3 صقل | هوية | نصف القطر | rounded-sm / rounded-md / rounded-[6px] غير مربوطة برموز الهوية (4px/6px خارج المقياس 8·12·14·16·18) | tokens.css يربط `lg→8`, `xl→12`, `2xl→16` فقط؛ `rounded-sm` (Tailwind 4px) و`rounded-md` (6px) و`rounded-[6px]` تستعمل قيم Tailwind الافتراضية خارج مقياس الهوية. | packages/ui/src/tokens.css:124–133 (الربط) · apps/client/src/components/charts.tsx:216–218 `rounded-sm` · checkout/[orderId]/transfer/transfer-form.tsx:409 `file:rounded-md` · packages/ui/src/components/checkbox.tsx:32 `rounded-[6px]` · brand/components/components.css:108 `.fpt-check input{… border-radius:6px}` | اربط `--radius-sm/md` بـ `--fpt-radius-sm` (8) أو عرّف `--fpt-radius-check: 6px` في الحزمة واستعمله. | S | ui-ux-pro-max §4 effects-match-style · design-system § 3 | `packages/ui/src/tokens.css`، `packages/ui/src/components/checkbox.tsx`، `apps/client/src/components/charts.tsx` |

### مشترك — علامات الاقتباس والفاصلة العليا في الإنجليزية

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-050 | P3 صقل | نص | straight: mail.breach.kinds.daily_loss «day's», client.finance.dailyBase, client.landing.phase2Body «firm's», partners.overrideRow «sub-partners'», admin.result.deletion_approved «client's», settings.platform.widgetsHint — curly: legal.draft «founder’s», partners.tiers.override «sub-partners’», affiliatesAdmin.model.review «affiliate’s», accounts.actions.copierBody «account’s/client’s», risk.description «engine’s», staff.inviteBody «mail’s», simulator.order.body «trader’s», shop.ret.browserReturn «provider’s» — quotes: legalDelete.steps.s1 «More» · admin.events.account_status “{status}” · affiliatesAdmin.runWeeklyBody "already queued" | فاصلة عليا مستقيمة وأخرى منحنية، وثلاثة أنماط اقتباس («»، “”، "") في النصوص الإنجليزية | الاختلاف يظهر في الصفحة نفسها (partners.overrideRow مستقيمة وpartners.tiers.override منحنية). | ar.json:L1097 / en.json:L1097; ar.json:L1145 / en.json:L1145; ar.json:L3304 / en.json:L3304; ar.json:L1681 / en.json:L1681; ar.json:L2558 / en.json:L2558 | الإنجليزية: فاصلة عليا منحنية (’) واقتباس “ ” دائماً؛ العربية: «» دائماً. | S | web-interface-guidelines Content & Copy (typography) | `packages/i18n/src/messages/en.json` |

### مشترك — عمولة المستوى الأعلى

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-041 | P2 ثانوي | نص | affiliatesAdmin.overrideOnSub «+2 % علوية» · affiliatesAdmin.kinds.override «علوية» · admin.events.commissionKinds.override «من مسوّق فرعي» · settings.affiliates.uplineOverride «عمولة المستوى الأعلى» · partners.tiers.override «+2 % على مبيعات الشركاء الفرعيين» · affiliatesAdmin.model.override «الشريك الأعلى: 2 %» | override بأربع ترجمات، و«مسوّق» تسرّب لمصطلح «شريك» | «علوية» وحدها لا تُفهم؛ «مسوّق فرعي» المرة الوحيدة التي يُسمّى فيها الشريك مسوّقاً. | ar.json:L2619 / en.json:L2619; admin.events.commissionKinds.override; ar.json:L2883 / en.json:L2883 | «عمولة المستوى الأعلى (2 %)» في كل المواضع، و«شريك فرعي» بدل «مسوّق فرعي». | S | terminology consistency | `packages/i18n/src/messages/ar.json` |

### مشترك — فئات العمولة

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-033 | P2 ثانوي | نص | partners.tiers.t2 «من 10 إلى 29 مبيعاً» · partners.tiers.t3 «30 مبيعاً أو أكثر» · partners.tierProgress «{n} مبيعات» · partners.tierProgressTop · partners.tierProgressCustom · affiliatesAdmin.model.tiers «1–9 مبيعات» · affiliatesAdmin.rollingShort «{n} مبيعات» · affiliatesAdmin.model.salesRange · partners.tierSales «المبيعات في آخر 30 يوماً» | «مبيعاً/مبيعات» كاسم معدود للـ sale — «مبيع» اسم مفعول (الشيء المباع) لا «عملية بيع» | «29 مبيعاً» و«1 مبيعات» و«{n} مبيعات» غير سليمة؛ «المبيعات» كاسم جمع للإيراد مقبولة لكن لا تُعدّ. | ar.json:L1142 / en.json:L1142; ar.json:L1108 / en.json:L1108; ar.json:L2563 / en.json:L2563 | «من 10 إلى 29 عملية بيع»، «30 عملية بيع أو أكثر»، «{n} عمليات بيع» (مع قواعد الجمع من TXT-013)؛ «المبيعات» تبقى للمجموع. | S | Arabic grammar | `packages/i18n/src/messages/ar.json` |

### مشترك — فحوص

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-069 | P3 صقل | نص | payouts.status.checks_passed «الفحوص» · admin.payoutStatus.checks_passed «الفحوصات» · payouts.timeline.checks «الفحوص الآلية» · payoutsAdmin.checks «الفحوصات الآلية» · admin.result.not_approvable «الفحوصات» · audit.actions.payout_recheck «فحوص» | جمعان لكلمة واحدة |  | ar.json:L1023 / en.json:L1023; ar.json:L1742 / en.json:L1742 | «الفحوص» في كل المواضع. | S | consistency | `packages/i18n/src/messages/ar.json` |

### مشترك — قاعدة الخسارة اليومية (لوحة الحساب، القواعد، البريد، الإدارة)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-012 | P1 رئيسي | نص | mail.breach.kinds.daily_loss · client.finance.dailyBase · client.rules.dailyValue · accounts.gauge.dailyBase · accounts.figures.dayStart ↔ breach.kinds.daily_loss · accounts.state.dayStart | أساس الخسارة اليومية يُذكر «رصيد بداية اليوم» في مكان و«حقوق الحساب عند 00:00» في مكان آخر — والثاني هو الصحيح | كتاب القواعد: E0 := E عند 00:00 دبي «يشمل الربح/الخسارة العائمة» (المثال: عائم −1,200 عند منتصف الليل → E0 = 98,800). النصوص «5 % من رصيد بداية اليوم» / “of the day's starting balance” (البريد، لوحة الحساب، صفحة القواعد، مقاييس الإدارة، accounts.figures.dayStart «رصيد بداية اليوم») تخالف ذلك، بينما breach.kinds.daily_loss «5 % من حقوق الحساب عند 00:00 بتوقيت دبي» وaccounts.state.dayStart «حقوق بداية اليوم» صحيحان. عميل لديه صفقات مفتوحة عند منتصف الليل سيحسب حدّه خطأً. | ar.json:L117 / en.json:L117; ar.json:L358 / en.json:L358; ar.json:L409 / en.json:L409; ar.json:L2051 / en.json:L2051; accounts.figures.dayStart; .claude/skills/fpt-prop-platform/references/rulebook.md (Daily reset) | توحيد الأساس: «5 % من حقوق الحساب عند 00:00 بتوقيت دبي (شاملة الربح/الخسارة العائمة)» / “5 % of the equity at 00:00 Dubai (floating P&L included)” في كل المواضع. | S | fpt-prop-platform rulebook (Daily loss / Daily reset) | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### مشترك — قنوات الدفع (Fiper)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-026 | P2 ثانوي | هوية | admin.events.channels.fiper_p2p «فايبر P2P» · payouts.form.fiperP2p «تحويل Fiper P2P» · settings.payoutChannels.fiper_p2p / payoutsAdmin.channel.fiper_p2p «Fiper P2P» · payouts.form.fiperCard «بطاقة Fiper» · admin.events.channels.fiper_card «بطاقة Fiper» · payoutsAdmin.channel.fiper_card / settings.payoutChannels.fiper_card «Fiper Card» · shop.checkout.manual «الدفع عبر فايبر / تحويل محلي» · shop.checkout.cardHint «فيزا / ماستركارد» | اسم Fiper مرة بالحروف اللاتينية ومرة «فايبر»، و«بطاقة Fiper» مقابل «Fiper Card» غير المترجمة | المعجم § 1: الموقع يكتب «Fiper Global» باللاتينية داخل العربية، والمعتمد «بطاقة Fiper / Fiper Card». الإدارة تعرض «Fiper Card» و«Fiper P2P» بلا ترجمة بينما العميل يرى «بطاقة Fiper» و«تحويل Fiper P2P» وسجل الأحداث «فايبر P2P». أسماء Visa/Mastercard معرَّبة («فيزا / ماستركارد») بينما تُترك MyFatoorah وCoinsbuy لاتينية. | admin.events.channels.fiper_p2p; ar.json:L985 / en.json:L985; ar.json:L2331 / en.json:L2331; ar.json:L984 / en.json:L984; ar.json:L688 / en.json:L688; ar.json:L677 / en.json:L677; brand/glossary.md § 1 | «بطاقة Fiper» و«تحويل Fiper P2P» و«الدفع عبر Fiper» في كل المفاتيح العربية (العميل والإدارة والإعدادات)؛ «Visa / Mastercard» باللاتينية. | S | brand glossary § 1 | `packages/i18n/src/messages/ar.json` |

### مشترك — كل Select (27 من 29 استعمالاً)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-034 | P2 ثانوي | RTL | Select (Radix) بلا dir | محفّز Select ومحتواه يحملان dir="ltr" في العربية: النص والقائمة محاذاة يساراً وسهم الاتجاه معكوس | امتداد لـ VIS-001 على مستوى الحقول: Radix Select يكتب `dir` على المحفّز (combobox) والمحتوى؛ بلا `dir` يصبح «ltr». في نماذج العميل (الملف: اللغة، المنطقة الزمنية؛ الشركاء) ونماذج الإدارة (الحقوق، حالة الحساب، الرمز…) تُقرأ الخيارات العربية في صندوق LTR. | packages/ui/src/components/select.tsx:36 `<RadixSelect.Root dir={dir} …>` · `grep -rn "<Select " apps \| grep -c dir=` → 2 من 29 · node_modules @radix-ui/react-select: `dir: context.dir` على Trigger وContent · apps/client/src/app/[locale]/(app)/profile/forms.tsx:170 `<Select value={lang} …>` بلا dir | نفس حل VIS-001 (`DirectionProvider`)، أو افتراضي `dir` في Select من `document.documentElement.dir` عبر hook. | S | fpt-brand RTL · ui-ux-pro-max §4 system-controls | `packages/ui/src/components/select.tsx` |

### مشترك — كل h1 (ScreenTitle

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-026 | P2 ثانوي | هوية | .ui-title مقابل .fpt-h | صوتان لعنوان الشاشة: .ui-title بخط الواجهة (Inter) و PageHeader/CardTitle بالصوت العرضي Barlow Condensed | design-system § 2 يحدد عنوان الشاشة «Barlow Condensed (لاتيني) / IBM Plex Sans Arabic 700». `.ui-title` لا يضبط `font-family` فيرث Inter في الإنجليزية، بينما `PageHeader` (الهبوط، المساعدة، القانوني) وبطاقات دخول الإدارة تضيف `fpt-h` (Barlow uppercase). النتيجة عنوانان بهويتين مختلفتين داخل التطبيق نفسه على 1440. | packages/ui/src/components.css:483 `.ui-title { font-size: var(--text-xl); line-height: 1.25; font-weight: 700; margin: 0; }` (لا font-family) · packages/ui/src/components/page-header.tsx:26 `className={cn('fpt-h text-balance', Tag === 'h1' ? 'ui-title' : …)}` · apps/admin/src/app/[locale]/(auth)/signin/page.tsx:45 `<CardTitle as="h2" className="fpt-h text-xl">` · docs/design-system.md § 2 صف «عنوان الشاشة» | قرار واحد: إمّا `.ui-title { font-family: var(--fpt-font-family-display) }` للاتيني (مع `[lang=ar]` Plex Arabic 700) في كل مكان، أو إزالة `fpt-h` من PageHeader وبطاقات الدخول. وثّقه في design-system. | S | fpt-brand typography · ui-ux-design-pro critique «Swap test / typeface doing its job» | `packages/ui/src/components.css`، `packages/ui/src/components/page-header.tsx`، `apps/admin/src/app/[locale]/(auth)/signin/page.tsx` |

### مشترك — كل الاستجابات

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-005 | P3 صقل | تقني | packages/core/src/csp.ts HSTS / next.config.ts headers() | HSTS الفعلي في الإنتاج هو الافتراضي للمنصة (`max-age=63072000`) بلا `includeSubDomains; preload` خلافاً للمصدر | المصدر يضيف `Strict-Transport-Security: max-age=63072000; includeSubDomains; preload` فقط عندما يكون `FPT_*_ORIGIN` يبدأ بـ https وقت تقييم `headers()` (وقت البناء). الرأس الملاحَظ على الأصلين هو `max-age=63072000` فقط، بينما CSP الوسيط (وقت التشغيل) تحمل `upgrade-insecure-requests` أي أن المتغير https في وقت التشغيل لا وقت البناء، أو أن رأس المنصة يطغى. النتيجة: النطاقات الفرعية (admin.) غير مشمولة من رأس العميل ولا يمكن إدراج النطاق في قائمة preload. | curl -I https://fptraders.app/ar و https://admin.fptraders.app/ar → `strict-transport-security: max-age=63072000`؛ packages/core/src/csp.ts:85,101; apps/client/next.config.ts:223; apps/admin/next.config.ts:253; .env.example:12-13 (`FPT_CLIENT_ORIGIN=` فارغ). | اجعل الرأس غير مشروط في الإنتاج: `if (process.env.NODE_ENV === 'production') h.push(HSTS)` (الإنتاج https دائماً على fptraders.app)، أو عرّف `FPT_CLIENT_ORIGIN`/`FPT_ADMIN_ORIGIN` كمتغيرات بناء في Vercel؛ تحقّق بـ curl بعد النشر. | S | security-audit (headers) | `packages/core/src/csp.ts`، `apps/client/next.config.ts`، `apps/admin/next.config.ts` |

### مشترك — كل الشاشات (أسماء العلامة والرموز)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-035 | P3 صقل | RTL | JSX — لا `translate="no"` | لا `translate="no"` على «Fiper Pro Traders / FPT / cTrader» والرموز (رقم الحساب، الخادم، IBAN، العناوين): الترجمة الآلية في المتصفح تشوّهها | الهوية تلزم بقاء الاسم اللاتيني داخل العربية؛ الترجمة التلقائية لـ Chrome/Safari تترجم «Fiper Pro Traders» و«cTrader» وقد تلمس القيم داخل `.fpt-unit`. لا استخدام واحد للسمة في المستودع. | grep `translate="no"` في apps/*/src packages/ui/src → 0؛ packages/ui/src/components/logo.tsx, credentials-panel.tsx:17. | أضف `translate="no"` إلى غلاف الشعار/الاسم، إلى `<bdi className="fpt-unit">` في `Money`/`DateTime`/`KeyValue` وإلى حقول الرموز؛ ويمكن إضافة `<meta name="google" content="notranslate">` لصفحات الحساب فقط. | S | web-interface-guidelines § Locale & i18n + fpt-brand | `packages/ui/src/components/logo.tsx`، `packages/ui/src/components/money.tsx`، `packages/ui/src/components/date-time.tsx`، `apps/client/src/components/credentials-panel.tsx` |

### مشترك — كل المسارات الديناميكية (accounts, account

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-033 | P3 صقل | أداء | app router — لا loading.tsx ولا Suspense | لا هياكل تحميل لأي مسار: كل تنقّل ينتظر تصيير الخادم الكامل (قاعدة البيانات) وتبقى الشاشة الحالية جامدة | `dynamic = 'force-dynamic'` على كل الصفحات ولا ملف `loading.tsx` ولا `Suspense` في أي من التطبيقين؛ مكوّن `Skeleton` في @fpt/ui غير مستخدم للمسارات. على 3G/جهاز حقيقي يظهر التنقّل بين التبويبات كتجمّد 0.5–2 ثانية بلا تغذية راجعة (البوابات تقيس DCL لا هذا الفراغ). | find apps/*/src/app -name loading.tsx → 0؛ grep `Suspense` → 0؛ packages/ui/src/components/skeleton.tsx موجود؛ apps/client/src/app/[locale]/layout.tsx:89. | أضف `loading.tsx` في `(app)/accounts`, `(app)/accounts/[id]`, `(app)/payouts`, `(app)/orders`, و`(app)` في الإدارة بهياكل AccountHeroCard/ListRow (Skeleton) بالارتفاعات الحقيقية لمنع الانزياح؛ أبقِ الرأس والشريط السفلي خارج الهيكل. | M | web-interface-guidelines § Performance + ui-ux-pro-max § loading states | `apps/client/src/app/[locale]/(app)/accounts`، `apps/client/src/app/[locale]/(app)/payouts`، `apps/admin/src/app/[locale]/(app)`، `packages/ui/src/components/skeleton.tsx` |

### مشترك — كل المستندات

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-006 | P3 صقل | تقني | next-intl middleware — كوكي NEXT_LOCALE | كوكي اللغة `NEXT_LOCALE` تُرسل بلا `Secure` (ومن الوسيط بلا HttpOnly) بينما `/api/lang` يضبط الكوكي نفسها بـ Secure | الوسيط (next-intl) يضبط `Set-Cookie: NEXT_LOCALE=ar; Path=/; SameSite=lax` على كل تحويل؛ مسار `/api/lang` يضبطها بـ `secure: cookieSecure()`. التضارب يعني أن قيمة اللغة يمكن أن تُستبدل عبر اتصال غير مشفّر لنطاق فرعي (HSTS بلا includeSubDomains — انظر TEC-005). الأثر محدود (تفضيل لغة) لكنه يخالف قاعدة «كل الكوكيز Secure في الإنتاج». | curl -I https://fptraders.app/ → `set-cookie: NEXT_LOCALE=ar; Path=/; SameSite=lax`؛ apps/client/src/app/api/lang/route.ts:22؛ apps/client/src/i18n/routing.ts (إعداد next-intl). | في `defineRouting` أضف `localeCookie: { secure: true, sameSite: 'lax', maxAge: 31536000 }` (next-intl ≥ 3.22) في كلا التطبيقين، أو عطّل كوكي الوسيط (`localeCookie: false`) واعتمد `/api/lang` وحده. | S | security-audit (cookies) | `apps/client/src/i18n/routing.ts`، `apps/admin/src/i18n/routing.ts`، `apps/client/src/app/api/lang/route.ts` |

### مشترك — كل المواضع التي تُدرج عدداً قبل اسم معدود

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-013 | P1 رئيسي | نص | client.finance.days «{n} يوماً» · client.finance.daysOf «{count} من {required} يوماً» · dashboard.ofRequired «{count} من {required} أيام» · payouts.flow.cycleOk «{days} من {required} يوم تداول» · admin.events.passed «بعد {days} يوم تداول» · trades.count «{count} صفقة» · dashboard.positionsCount «{n} صفقات مفتوحة» · coupons.description «{n} كوبونات» · faq.results «{count} مقالات» · deletionsAdmin.description «{n} طلبات» · client.home.otherCount «{n} حسابات» · orders.description «{n} طلباً» · accounts.description «{n} حساباً» · risk.live «{n} حساباً حياً» · breaches.description «{n} مخالفة» · fraud.description «{n} إشارة» · audit.description «{n} سجلاً» · mailPreview.description «{n} رسالة» · affiliatesAdmin.description «{n} شريكاً» · kycAdmin.description «{n} حالة» · finance.passRateHint · finance.paidThisMonthNote «{n} سحباً» · products.usedBy «{n} صفوف» · products.olderVersions · products.sim.summary · breaches.curveCaption «{n} نقطة» · health.alerts.queue_backlog.body · health.alerts.payments_review_backlog.body · paymentsAdmin.overdue · mail.manual_payment_reminder.body «{hours} ساعة» · health.alerts.engine_down.body «{age} ثانية» · fraud.devicesBody · fraud.paymentsBody · mail.verify_code.body / auth.verify.lead «{minutes} دقائق» · partners.tierProgress «{n} مبيعات» · legalDelete.retention / deletionsAdmin.retentionDays «{days} يوماً» · EN: products.olderVersions «version(s)», fraud.devicesBody «device(s)», finance.paidThisMonthNote «payout(s)», health.alerts.command_failed.body «attempt(s)», accounts.description «{n} accounts» | لا قواعد جمع في القاموس: التمييز العددي العربي خاطئ لمعظم القيم (1، 2، 3–10، 11+) والإنجليزية تلجأ إلى «(s)» | دالة t() في packages/i18n/src/index.ts تستبدل {var} فقط بلا Intl.PluralRules. مثال: «{count} من {required} يوماً» مع required = 5 أو 10 → «5 من 10 يوماً» (الصواب «أيام»)؛ وفي الوقت نفسه dashboard.ofRequired يكتب «{count} من {required} أيام» للقيمة نفسها — فتظهر صيغتان مختلفتان للعدّاد ذاته على اللوحة. «{n} صفقات مفتوحة» مع n = 1 أو 11؛ «{n} كوبونات» مع n = 1؛ «{days} يوم تداول» مع 10 → «10 يوم تداول». دالة days() في format.ts تعالج الحالات للبريد لكنها لا تُستخدم في التطبيقين. | packages/i18n/src/index.ts:27-37 (t); ar.json:L376 / en.json:L376; ar.json:L755 / en.json:L755; ar.json:L950 / en.json:L950; ar.json:L738 / en.json:L738; ar.json:L1941 / en.json:L1941; packages/adapters/src/mail/templates/format.ts:66-73 (days) | إدخال صيغ الجمع (ICU plural عبر next-intl: =1 / =2 / few / many / other) للمفاتيح المعدودة، أو تمرير الاسم المعدود جاهزاً من دالة واحدة (مثل days()) من @fpt/ui. أولاً: توحيد daysOf/ofRequired فوراً على «{count} من {required} أيام». | M | web-interface-guidelines Content & Copy (plurals) / i18n ICU | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `packages/i18n/src/index.ts` |

### مشترك — كل حقول كلمة المرور

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-064 | P3 صقل | بصري | Input type=password | لا زر إظهار/إخفاء كلمة المرور | ثمانية حقول كلمة مرور (دخول، تعيين، تغيير، إعادة المصادقة في حوارات الإدارة) بلا تبديل رؤية؛ الأيقونتان Eye/EyeOff موجودتان في icons.ts. | apps/client/src/app/[locale]/(auth)/auth-form.tsx:84,130,134,137 · apps/admin/src/components/ActionDialog.tsx:98 · packages/ui/src/icons.ts (`Eye`, `EyeOff`) | `PasswordInput` في @fpt/ui بـ `IconButton` نهاية 44px. | S | ui-ux-pro-max §8 password-toggle | `packages/ui/src/components/input.tsx`، `apps/client/src/app/[locale]/(auth)/auth-form.tsx` |

### مشترك — كل مسارات [locale] (لا يوجد loading.tsx)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-003 | P1 رئيسي | تقني | Next.js loading.tsx / Skeleton | لا توجد حالة تحميل لأي مسار: كل تنقّل بين التبويبات يترك الشاشة السابقة جامدة حتى يصل الـ RSC | لا يوجد ملف `loading.tsx` واحد في التطبيقين، و`Skeleton` يُستعمل مرة واحدة فقط (داخل account-overview عند غياب الحالة). الصفحات كلها server components تقرأ قاعدة البيانات (`force-dynamic`)، فعند الضغط على تبويب الشريط السفلي لا يظهر هيكل ولا مؤشر؛ المستخدم يرى الصفحة القديمة ثم قفزة كاملة. design-system § 5.6 يلزم «كل تحميل غير فوري له Skeleton». | `find apps/*/src/app -name loading.tsx` → لا شيء · packages/ui/src/components/skeleton.tsx (مُعرَّف) · apps/client/src/components/account-overview.tsx:237–241 (الاستعمال الوحيد) · apps/client/src/app/[locale]/layout.tsx:32 `export const dynamic = 'force-dynamic'` | أضف `loading.tsx` لمجموعة `(app)` في العميل (هيكل: `ui-skel-hero` + 3 صفوف `ui-skel-row`) وللإدارة (عنوان + جدول هيكلي)، ومسارات مخصصة للرئيسية/الحساب/الدفعات. اربطه بـ `role="status"` + label من الترجمة. | M | ui-ux-pro-max §7 loading-states · §3 progressive-loading · ui-ux-design-pro component-patterns «Skeleton Screens» | `apps/client/src/app/[locale]/(app)/layout.tsx`، `apps/admin/src/app/[locale]/(app)/layout.tsx`، `packages/ui/src/components/skeleton.tsx` |

### مشترك — لوحة الحساب 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-016 | P2 ثانوي | نص | dashboard.equity «حقوق الحساب» · client.finance.equityCurve «منحنى الرصيد» · dashboard.chart «منحنى الرصيد» · engine.report.curve «منحنى حقوق الحساب» · dashboard.balance «الرصيد» · accounts.state.dayStart «حقوق بداية اليوم» | مصطلح equity: «حقوق الحساب» للرقم و«منحنى الرصيد» للرسم نفسه، والمعجم يقترح «الرصيد الحالي / رصيد الحساب» | على اللوحة الواحدة: رقم كبير بعنوان «حقوق الحساب» ورسم بعنوان «منحنى الرصيد» بينما «الرصيد» (balance) رقم مستقل تحته — العميل لا يعرف أي الاثنين يرسم المنحنى. المعجم § 4 و§ 7 يقترحان «الرصيد الحالي»/«رصيد الحساب» لـ equity و«منحنى الرصيد» للمنحنى؛ «حقوق الحساب» كلمة مصطنعة (المعتاد «حقوق الملكية»). | ar.json:L757 / en.json:L757; ar.json:L367 / en.json:L367; ar.json:L1459 / en.json:L1459; ar.json:L758 / en.json:L758; brand/glossary.md § 4 «الرصيد الحالي», § 7 «رصيد الحساب»/«منحنى الرصيد» | قرار واحد يُسجَّل في المعجم: إمّا «رصيد الحساب» (equity) + «الرصيد المُقفل» (balance) + «منحنى رصيد الحساب»، أو «حقوق الحساب» + «منحنى حقوق الحساب» في كل مكان بما فيه الرسم. | S | brand glossary § 4 | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `brand/glossary.md` |

### مشترك — لوحة الحساب 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-010 | P1 رئيسي | نص | mail.passed.body «حدود السحب» · client.states.drawdownWarning «تحذير السحب» · client.finance.legendBand «نطاق السحب» ↔ payouts.new «طلب سحب» | «السحب» يعني drawdown في مكان وwithdrawal في مكان آخر على الشاشة نفسها | بريد الاجتياز: «حقّق الحساب هدف الربح مع الالتزام بحدود السحب» (المقصود حدود الخسارة)، وشارة «تحذير السحب» ومفتاح الرسم «نطاق السحب» (drawdown) تظهر في لوحة حساب ممول زرُّه «طلب سحب» (payout). المعجم § 4 يقترح «تحذير السحب» لكنه يقترح أيضاً «الصرف» للدفعات؛ لأن التطبيق اختار «السحب» للدفعات صار المصطلح ملتبساً. التطبيق نفسه يستخدم «حدود الخسارة» في dashboard.status.evaluation.body. | ar.json:L128 / en.json:L128; ar.json:L350 / en.json:L350; ar.json:L372 / en.json:L372; ar.json:L957 / en.json:L957; brand/glossary.md § 4 «تحذير السحب» | توحيد drawdown على «الخسارة/حدّ الخسارة»: «مع الالتزام بحدود الخسارة»، «تحذير الاقتراب من حدّ الخسارة»، «نطاق الخسارة»؛ وتسجيل التعديل في المعجم كقرار. (يسقط الالتباس أيضاً بتطبيق TXT-009.) | S | brand glossary § 4 / ui-ux-pro-max terminology | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `brand/glossary.md` |

### مشترك — مسار الدفعة 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-021 | P2 ثانوي | نص | shop.orderStatus.awaiting_review «بانتظار الاعتماد» · shop.ret.awaitingReview «بانتظار الاعتماد» · payouts.timeline.approvals «الموافقات» · payouts.flow.approval1 «موافقة 1» · payouts.status.approved «موافَق عليه» · admin.payoutStatus.approved «معتمد» · mail.payout_approved.title «تمت الموافقة» · paymentsAdmin.approve «اعتماد» · payoutsAdmin.step.first «موافقة أولى» ↔ payoutsAdmin.approveSecond «الاعتماد الثاني» | approve = «اعتماد» في الإدارة و«موافقة» في العميل — ويختلطان في الشاشة الواحدة | في الإدارة نفسها: «موافقة أولى» ثم «الاعتماد الثاني» (payoutsAdmin.step.first vs approveSecond). وفي العميل «بانتظار الاعتماد» لطلب الشراء و«الموافقات» لطلب الدفعة. | ar.json:L2340 / en.json:L2340; ar.json:L2376 / en.json:L2376; ar.json:L578 / en.json:L578; ar.json:L1035 / en.json:L1035; ar.json:L1026 / en.json:L1026; ar.json:L1745 / en.json:L1745 | مصطلح واحد: «الاعتماد» للفعل الإداري (اعتماد أول/ثانٍ، معتمد، بانتظار الاعتماد) في اللغتين، و«تمت الموافقة» يُستبدل بـ «اعتُمد» في البريد والحالات. | S | terminology consistency | `packages/i18n/src/messages/ar.json` |

### مشترك — مستند

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-070 | P3 صقل | نص | client.screen.legal / client.more.legal «المستندات القانونية» · profile.documents «المستندات المقبولة» ↔ settings.tabs.legal «الوثائق القانونية» · audit.kinds.legal «وثيقة قانونية» · settings.legal.key «الوثيقة» · mockGateway.shuftipro.document «نوع الوثيقة» | document بترجمتين |  | ar.json:L252 / en.json:L252; ar.json:L2834 / en.json:L2834 | «الوثائق القانونية» / «وثيقة الهوية» في كل المواضع. | S | consistency | `packages/i18n/src/messages/ar.json` |

### مشترك — مفاتيح ميتة أو مكررة

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-059 | P3 صقل | نص | dashboard.credentials.{password,reveal,reauth,fptPassword,submit,failed,hide,login} · shop.ret.{login,password,revealOnce,reveal} · mail.common.labels.{login,password} · client.certificate.regulatory · client.statement.regulatory · client.units.{usd,days,day,lots} · common.entity · partners.stats.nextBatch (= nextPayout) · staff.me.current_ (= current) | مفاتيح غير مستخدمة تحمل نموذج «كلمة مرور المنصة تُعرض مرة واحدة» الذي تخلّى عنه المنتج | credentials-panel.tsx لا يعرض كلمة مرور (التعليق: «No MT5-style password row»)، وprovisioning-watcher لا يستخدم revealOnce/reveal/password/login، وspec() في index.ts لا يستدعي labels.login/password. بقاؤها خطر إعادة توصيل نموذج متناقض (TXT-005) وعبء ترجمة. | apps/client/src/components/credentials-panel.tsx:9-10,28-30; apps/client/src/app/[locale]/(app)/checkout/[orderId]/return/provisioning-watcher.tsx (قائمة t() المستخدمة); packages/adapters/src/mail/templates/index.ts (لا L('login')/L('password')) | حذف المفاتيح الميتة بعد تأكيد عدم الاستخدام بفحص آلي (unused-keys test بجوار parity.test.ts). | S | i18n hygiene | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `packages/i18n/test/parity.test.ts` |

### مشترك — نطاقات الأرقام

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-061 | P3 صقل | نص | partners.t1Sales «1 – 9» · partners.t2Sales «10 – 29» ↔ affiliatesAdmin.model.tiers «1–9» ↔ partners.tiers.t1 «من 1 إلى 9» | شرطة طويلة بمسافات، وبلا مسافات، و«من… إلى» للنطاق نفسه |  | ar.json:L1094 / en.json:L1094; ar.json:L2563 / en.json:L2563; ar.json:L1141 / en.json:L1141 | في الجداول «1–9» (شرطة قصيرة بلا مسافات داخل وحدة LTR)، وفي الجمل «من 1 إلى 9». | S | typography | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-012 | P2 ثانوي | بصري | Stepper description (.fpt-phase__s) | وصف المرحلتين على صفحة الهبوط جملة بحجم 12px | `Stepper` يرسم `description` في `.fpt-phase__s` المحدَّد بـ 12px (`--text-xs`). الهبوط يمرّر جملتين كاملتين (`phase1Body`/`phase2Body`) فتُقرأ 12px — البوّابة تستثني `.fpt-phase__s`. | packages/ui/src/components.css:324 `.fpt-phase__s { font-size: var(--text-xs); }` · apps/client/src/app/[locale]/(public)/page.tsx:82 `<Stepper steps={[{ label: t('phase1'), description: t('phase1Body') } …` · e2e/ux-audit.ts:75 | ارفع `.fpt-phase__s` إلى `--text-sm` (14) للتعليقات، وفي الهبوط اعرض الوصف كفقرة `text-body-2` تحت المرحلة. | S | design-system § 2 · ui-ux-pro-max §6 font-scale | `packages/ui/src/components.css`، `apps/client/src/app/[locale]/(public)/page.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-032 | P3 صقل | هوية | apps/client/src/app/not-found.tsx | 404 الجذر: وسم viewport مكرر، بلا `theme-color`/`color-scheme`/manifest، و`lang="ar"` ثابت للزائر الإنجليزي | الصفحة تكتب `<meta name="viewport">` يدوياً وNext يضيف واحداً آخر (اثنان في HTML الإنتاج)؛ لا `color-scheme: dark` فتظهر عناصر النموذج/شريط التمرير فاتحة على iOS/Windows، ولا theme-color فيبقى شريط المتصفح بلون افتراضي بدل الأرضية `#0A0A0A`. | curl https://fptraders.app/ar/nonexistent-xyz → `<meta name="viewport" …>` ×2، لا `theme-color` ولا `color-scheme`؛ apps/client/src/app/not-found.tsx:23-30. | احذف وسم viewport اليدوي، أضف `<meta name="color-scheme" content="dark">` و`<meta name="theme-color" content="#0A0A0A">` (القيمة الوحيدة المسموح بها حرفياً في التخطيطات) ورابط manifest؛ اختر `lang`/`dir` من كوكي `NEXT_LOCALE` عبر `cookies()`. | S | fpt-brand + web-interface-guidelines § Dark Mode | `apps/client/src/app/not-found.tsx`، `apps/admin/src/app/not-found.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-008 | P2 ثانوي | بصري | primary slot + ghost «فتح الحساب» | زرّان بالتسمية نفسها «فتح الحساب» على الرئيسية لحساب التقييم | عندما لا يكون الحساب منتهياً ولا ممولاً مؤهلاً يُمرَّر زر ثانوي «فتح الحساب» إلى البطل (السطر 68–70) ثم يُرسم زر ghost آخر بالتسمية نفسها تحت البطل (السطر 89–93). إجراء مكرر يبدد التركيز. | apps/client/src/app/[locale]/(app)/accounts/page.tsx:67–71 `<Button asChild variant="secondary" size="lg" block><Link …>{th('open')}` و :89–93 `{ended \|\| !LIVE.includes(…) ? null : <Button asChild variant="ghost" …>{th('open')}` | احذف أحد الزرّين (أبقِ زر البطل) أو اجعل الثاني رابطاً نصياً إلى تبويب مختلف (الصفقات). | S | ui-ux-pro-max §4 primary-action · ui-ux-design-pro critique «Is there a focal point?» | `apps/client/src/app/[locale]/(app)/accounts/page.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-006 | P1 رئيسي | وصول | AppHeaderBar center / ClientHeader screen-title | شاشات العميل الجذرية بلا <h1>: عنوان الشاشة في الرأس مجرد <span> والأقسام تبدأ بـ h2 | عنوان الشاشة الوحيد يُرسم داخل `<div class="…truncate text-center">` ثم `<span data-testid="screen-title">`؛ ليس عنواناً دلالياً. الصفحات نفسها لا تحوي h1 (12 صفحة بعدّ `grep`)، فتبدأ الشجرة بـ h2 (`Section`) أو h3 (`EmptyState`). قارئ الشاشة لا يجد عنوان الصفحة، ويُخرق تسلسل العناوين. axe يعدّها best-practice فلا تُفشل البوّابة. | packages/ui/src/components/app-shell.tsx:65 `<div className="min-w-0 flex-1 truncate text-center text-base font-semibold">{… center}</div>` · apps/client/src/components/client-header.tsx:104 `center={<span data-testid="screen-title">…` · apps/client/src/app/[locale]/(app)/accounts/page.tsx (لا h1) · empty-state.tsx:22 `<h3 className="fpt-empty__title">` | اجعل `center` في AppHeaderBar يُرسم كـ `<h1 className="text-base font-semibold m-0">` (الحجم البصري يبقى 16px)، أو أضف `<h1 class="sr-only">` في كل صفحة جذرية. اجعل EmptyState يقبل `headingLevel`. | S | ui-ux-pro-max §1 heading-hierarchy · web-interface-guidelines §Accessibility «Headings hierarchical» | `packages/ui/src/components/app-shell.tsx`، `apps/client/src/components/client-header.tsx`، `packages/ui/src/components/empty-state.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-082 | P3 صقل | وصول | text-gain على رقم بلا كلمة | عدد الأيام يتحوّل أخضر عند بلوغ الحد بلا كلمة أو أيقونة | «5 / 5» يُلوَّن gain فقط عندما يتحقق شرط الأيام؛ المعنى باللون وحده. | apps/client/src/components/account-overview.tsx:313 `className={g.tradingDays.count >= g.tradingDays.required ? 'text-gain' : undefined}` | أضف `CircleCheck` + كلمة «مكتمل» بجانب الرقم. | S | ui-ux-pro-max §1 color-not-only | `apps/client/src/components/account-overview.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-044 | P3 صقل | تقني | apps/client/src/lib/session.ts resolveSession + account-overview polling | مهلة الخمول (30 دقيقة) معطّلة عملياً ما دام تبويب الحساب مرئياً: الاستطلاع كل ثانية يلمس `last_seen_at` | `viewerFromRequest` → `resolveSession` → `shouldTouch` يحدّث `last_seen_at` كل دقيقة لأي طلب بما فيه `/api/accounts/[id]/live`. شاشة مفتوحة على جهاز مشترك تبقى مصادَقة حتى الحد المطلق 12 ساعة. قرار تصميمي مقبول لكنه غير موثّق في قواعد الجلسة. | apps/client/src/lib/session.ts:50-53, :85-88; apps/client/src/components/account-overview.tsx:11, :47; apps/client/src/lib/session-rules.ts:77-80. | إمّا توثيق السلوك في `session-rules.ts` (الاستطلاع = نشاط)، أو تمرير `touch: false` من مسار live فيبقى الخمول مقيساً بتفاعل المستخدم (تنقّل/إجراء)، مع إيقاف الاستطلاع عند 401 (TEC-011). | S | security-audit (session) | `apps/client/src/lib/session.ts`، `apps/client/src/app/api/accounts/[id]/live/route.ts` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-049 | P3 صقل | أداء | packages/ui/src/components/equity-chart.tsx | SVG الرسم يتمدّد بعرض الحاوية (viewBox 358×200، `w-full`) فتتضخّم نصوص المحاور بنسبة التمدّد على سطح المكتب | النصوص داخل SVG بوحدات viewBox (fontSize ثابت) تُقاس مع العرض؛ عند 720 px تصبح ~2×. غير مُقاس بصرياً في هذا التدقيق (لقطات 1440 تمرّ البوابات)، لذا تأكيد مطلوب. | packages/ui/src/components/equity-chart.tsx:69 (`width = 358, height = 200`), :122 (`<svg viewBox … width="100%" … className="block h-auto w-full"`). | مرّر `width` الفعلي من الحاوية (ResizeObserver في مكوّن عميل رقيق) أو حدّ أقصى للعرض (`max-w-[480px]`) مع `preserveAspectRatio="xMidYMid meet"`، وأبقِ النص بحجم ثابت عبر `vector-effect`/مقياس عكسي. | S | ui-ux-pro-max § charts / dataviz | `packages/ui/src/components/equity-chart.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-009 | P1 رئيسي | هوية | Money tone=auto sign=always codePosition=none | أرباح/خسائر الصفقات بلون وإشارة فقط — بلا كلمة وبلا رمز العملة | صفوف المراكز والصفقات تُظهر `+120.50`/`−80.00` ملوّنة (gain/loss) دون كلمة «ربح/خسارة» ودون `USD`. القاعدة الملزمة: «الحالات المالية كلمة + إشارة + لون» و«المبالغ تحمل رمز عملتها». مكوّن `Delta` الذي يضيف الكلمة موجود في الإدارة لكنه غير مستعمل. | apps/client/src/components/account-overview.tsx:340 `<Money cents={p.floatingPnlCents} … sign="always" tone="auto" codePosition="none" />` · apps/client/src/components/trades-panel.tsx:231 و:260 · apps/client/src/app/[locale]/(app)/accounts/[id]/breach/page.tsx:140 · apps/client/src/app/[locale]/(app)/accounts/page.tsx:110 (حقوق الحساب في الصفوف بلا رمز) · packages/ui/src/components/money.tsx:16–17 «`auto` … should come with `word`» · apps/admin/src/components/Finance.tsx:11 `Delta` (غير مستعمل) | مرّر `word={tw(n<0?'loss':'profit')}` في كل Money ذات tone=auto، وأبقِ رمز العملة (أو ضع «USD» مرة واحدة في رأس القسم كما تفعل جداول الإدارة). انقل `Delta` إلى @fpt/ui واستعمله في المكانين. | S | fpt-brand finance semantics (word + sign + colour) · ui-ux-pro-max §6 color-not-decorative-only | `apps/client/src/components/account-overview.tsx`، `apps/client/src/components/trades-panel.tsx`، `apps/client/src/app/[locale]/(app)/accounts/[id]/breach/page.tsx`، `apps/admin/src/components/Finance.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-019 | P3 صقل | وصول | apps/client/src/components/copy-button.tsx | زر النسخ يعلن النجاح بتغيير `aria-label` فقط (لا منطقة حية) ويبتلع فشل الحافظة بصمت | بعد النسخ يتبدّل `IconButton` إلى أيقونة صح و`label='تم النسخ'`؛ تغيّر aria-label لا يُقرأ تلقائياً. `navigator.clipboard.writeText` يفشل في بعض سياقات iOS/الإذن ويُلتقط بلا أي رسالة، فيظن المستخدم أن القيمة نُسخت. | apps/client/src/components/copy-button.tsx:10-18, :27-29; packages/ui/src/components/toast.tsx:360 (منطقة `aria-live` متاحة عبر `useToast`). | استخدم `useToast().toast({ title: t('copied'), tone: 'success' })` أو `<span role="status" className="sr-only">` يتغيّر مع `done`؛ عند الفشل اعرض «تعذّر النسخ — حدّد النص وانسخه» واختر النص برمجياً (`selection.selectAllChildren`). | S | web-interface-guidelines § Accessibility (aria-live) | `apps/client/src/components/copy-button.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-011 | P2 ثانوي | تقني | apps/client/src/components/account-overview.tsx | استطلاع البث الحي كل ثانية يعامل 401 (انتهاء الجلسة) كـ«غير متصل» ويستمر إلى الأبد | `tick` يستدعي `/api/accounts/[id]/live` كل 1000 مللي ثانية؛ أي `!res.ok` يُرمى ويُضبط `online=false`. بعد خمول 30 دقيقة (الاستطلاع نفسه يمدّد `last_seen_at` ما دام التبويب مرئياً، لكن بعد إخفاء التبويب ثم إظهاره بعد الخمول) يعيد الخادم 401 فتظهر شارة «غير متصل» البرتقالية بينما الشبكة سليمة، ويستمر طلب فاشل كل ثانية. كذلك 429 مع `Retry-After: 60` لا يُحترم. | apps/client/src/components/account-overview.tsx:11 (`POLL_MS = 1000`), :47-55 (`if (!res.ok) throw` → `setOnline(false)`), :60; apps/client/src/app/api/accounts/[id]/live/route.ts:15 (401), :20 (429 + Retry-After). | في `tick`: عند 401/403 أوقف المؤقّت وانتقل إلى `/signin?next=<المسار>&reason=expired`؛ عند 429 انتظر `Retry-After`؛ عند أخطاء الشبكة طبّق تراجعاً أسّياً (1s→2s→4s…≤30s) وأعد التسمية إلى «انقطع الاتصال» بدل «غير متصل» الدائم. ملاحظة تصميمية: الاستطلاع يلمس `last_seen_at` فيعطّل مهلة الخمول عملياً أثناء عرض التبويب — وثّق ذلك أو استثنِ مسار live من اللمس. | S | ui-ux-pro-max § live data / security-audit (session) | `apps/client/src/components/account-overview.tsx`، `apps/client/src/app/api/accounts/[id]/live/route.ts`، `apps/client/src/lib/session.ts` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-051 | P3 صقل | بصري | .fpt-tabs overflow-x:auto scrollbar-width:none | خمسة تبويبات بعرض 360px تتمرّر أفقياً بشريط مخفي بلا أي إشارة إلى وجود المزيد | عند تفعيل `platform.ctraderWidgets` تصبح التبويبات خمسة؛ `.fpt-tabs` يمرّر أفقياً ويخفي شريط التمرير، فالتبويب الأخير خارج الشاشة بلا تدرّج ولا سهم. | brand/components/components.css:209 `.fpt-tabs{display:flex;gap:4px;…overflow-x:auto;scrollbar-width:none}` · apps/client/src/app/[locale]/(app)/accounts/[id]/page.tsx:110–128 (4–5 TabsTrigger) | عند 5 تبويبات: تسميات أقصر أو تدرّج حافة (`mask-image`) أو نقل «التداول» إلى زر داخل تبويب البيانات. | S | ui-ux-pro-max §5 chip-collection-reflow · §9 bottom-nav-limit | `apps/client/src/app/[locale]/(app)/accounts/[id]/page.tsx`، `packages/ui/src/components.css` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-049 | P3 صقل | وصول | Button asChild disabled + Link | رابط الصفحة السابقة «المعطّل» يبقى قابلاً للتركيز والتفعيل بلوحة المفاتيح | `pointer-events-none opacity-50` + `aria-disabled` على `<Link>` يمنع الفأرة فقط؛ Tab + Enter ينقل إلى `page=0`. `Button asChild` لا يحقن `disabled` على الروابط. | apps/client/src/components/trades-panel.tsx:266–269 `<Button asChild variant="ghost" size="sm" disabled={page <= 1}><Link … aria-disabled={page <= 1} className={page <= 1 ? 'pointer-events-none opacity-50' : ''}>` · packages/ui/src/components/button.tsx:61–62 | ارسم `<span>` بدل الرابط عند التعطيل، أو أضف `tabIndex={-1}` و`onClick preventDefault`. | S | ui-ux-pro-max §8 disabled-states · §1 keyboard-nav | `apps/client/src/components/trades-panel.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-028 | P2 ثانوي | وصول | PnlHeatmap | حروف أيام الأسبوع 10px وقيم الخلايا متاحة بالتحويم فقط (<title>) | الخريطة الحرارية ترسم أحرف الأيام `fontSize={10}` (تحت أرضية 12px؛ البوّابة تتجاهل SVG) وتضع قيمة كل يوم في `<title>` لا تظهر إلا بالتحويم بالفأرة — لا لمس ولا لوحة مفاتيح. كذلك ارتفاع الـ SVG ثابت محسوب لعرض 358 ومقيّد بـ 150px فيبدو صغيراً على المكتب. | apps/client/src/components/charts.tsx:209 `<text … fontSize={10} …>` · :198–200 `<rect …><title>{text}</title></rect>` · :207 `height={Math.round((height / width) * 358)} … style={{ maxHeight: 150 …}}` | حرف اليوم 12px بخط الواجهة؛ عند اللمس/التركيز اعرض اليوم وقيمته في سطر تحت الخريطة (`aria-live=polite`)، واجعل الخلايا `tabIndex=0` أو ادعم قائمة `<ol>` مكافئة مخفية. | M | ui-ux-pro-max §1 keyboard-nav · §2 hover-vs-tap · design-system § 2 (لا نص < 12) | `apps/client/src/components/charts.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-007 | P3 صقل | تقني | apps/client/src/app/api/lang/route.ts GET | تبديل اللغة عبر GET يعدّل الملف الشخصي: تنقّل من موقع آخر يكفي لقلب لغة المستخدم المسجّل | المعالج GET يحدّث `users.locale` للمستخدم المسجّل ثم يحوّل. الكوكي `SameSite=Lax` تمنع الطلبات الفرعية عبر المواقع (img) لكنها تُرسل مع التنقّل الأعلى (`<a href>`/`location`)، فأي صفحة خارجية تستطيع تحويل المتصفح إلى `/api/lang?to=en` وتغيير لغة الحساب وبريده اللاحق. الأثر منخفض (لا مال ولا بيانات) لكنه تغيير حالة عبر GET خلافاً لنمط `/api/signout` الذي يفحص المصدر في POST. | apps/client/src/app/api/lang/route.ts:14-24 (GET يكتب في `users`)؛ apps/client/src/app/api/signout/route.ts:12-14 (`sameOriginRequest` للـ POST فقط). | اجعل GET يضبط الكوكي ويحوّل فقط؛ انقل تحديث الملف الشخصي إلى الإجراء الخادمي الموجود في صفحة الملف أو إلى POST مع `sameOriginRequest`؛ مبدّل اللغة في الرأس يبقى رابطاً GET. | S | security-audit (CSRF) | `apps/client/src/app/api/lang/route.ts`، `apps/client/src/lib/csrf.ts` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-050 | P3 صقل | تقني | apps/client/src/app/api/signout/route.ts | تسجيل الخروج متاح بـ GET (موثّق كمقبول): رابط خارجي يستطيع إخراج المستخدم قسراً | التعليق يصف GET كهدف رابط حميد؛ `SameSite=Lax` تمنع الطلبات الفرعية لكن التنقّل الأعلى من موقع آخر يُخرج المستخدم ويحوّله. أثر: إزعاج فقط (لا بيانات). مع TEC-001 قد يُترك الجهاز على صفحة الهبوط بينما مخزن SW يحتفظ بالصفحات. | apps/client/src/app/api/signout/route.ts:12-14, :23-24. | إبقاء القرار مع توثيقه في security review؛ أو جعل GET يعرض صفحة تأكيد صغيرة (POST) — اختياري. | S | security-audit (CSRF — accepted risk) | `apps/client/src/app/api/signout/route.ts` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-043 | P3 صقل | وصول | checkout-form.tsx | روابط الشروط تفتح نافذة جديدة (`target="_blank"`) بلا إشارة للمستخدم ولا `rel` | قارئ الشاشة لا يُنبَّه أن الرابط يغادر الدفع؛ المتصفحات الحديثة تطبّق `noopener` ضمنياً فالأثر الأمني معدوم (نفس الأصل). | apps/client/src/app/[locale]/(app)/checkout/[orderId]/checkout-form.tsx:42. | أضف `rel="noopener"` و`<span class="sr-only">(يُفتح في نافذة جديدة)</span>` أو أيقونة `ExternalLink` مع `aria-hidden`. | S | web-interface-guidelines § Accessibility | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/checkout-form.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-033 | P2 ثانوي | تقني | صفوف الراديو اليدوية (peer sr-only + span) | خمس نسخ من زر راديو مرسوم يدوياً (border-[1.5px] + peer-checked) بدل مكوّن واحد أو راديو الحزمة | طريقة الدفع، المسار، القناة وRoutePicker تكرر 15 سطراً من العلامات والأصناف نفسها (`inline-grid size-5 … rounded-full border-[1.5px] border-line-input peer-checked:border-brand …`) في أربعة ملفات. الحزمة تملك `.fpt-check input[type=radio]` و@fpt/ui تملك Checkbox لكن لا `RadioRow`. قيمة `border-[1.5px]` عشوائية خارج المقياس. | apps/client/src/app/[locale]/(app)/checkout/[orderId]/checkout-form.tsx:110–112 و:131–133 · payouts/new/payout-form.tsx:213–215 · checkout/[orderId]/transfer/transfer-form.tsx:115–117 و:456–458 · brand/components/components.css:108–112 `.fpt-check input[type="radio"]` | مكوّن `RadioRow {icon,label,hint,checked,onChange}` في @fpt/ui (56px، شيفرة واحدة)، يُستعمل في المواضع الخمسة. | S | ui-ux-design-pro critique «Consistency» · ui-ux-pro-max §5 spacing-scale | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/checkout-form.tsx`، `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx`، `apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/transfer-form.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-012 | P2 ثانوي | تقني | apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/transfer-form.tsx | تاريخ التحويل الافتراضي والحد الأقصى يُحسبان بـ UTC داخل مكوّن عميل: عدم تطابق hydration وتاريخ خاطئ لمستخدمي الخليج بعد منتصف الليل | `const today = new Date().toISOString().slice(0, 10)` يُنفَّذ على الخادم (UTC) أثناء SSR ثم على العميل؛ الحقل `type="date" max={today} defaultValue={today}`. متداول في دبي الساعة 01:00 يوم 31: UTC ما زال 30 → لا يستطيع اختيار تاريخ اليوم (max) والقيمة الافتراضية أمس؛ وقرب منتصف الليل يختلف SSR عن العميل فيحذّر React من hydration mismatch ويُسقط القيمة. | apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/transfer-form.tsx:28, :76; packages/ui/src/lib/format.ts:140-148 (`dateParts` بمنطقة زمنية صريحة متاحة ولا تُستخدم هنا). | احسب اليوم على الخادم بمنطقة المشاهد: `const p = dateParts(Date.now(), viewer.user.timezone \|\| DUBAI); const today = `${p.year}-${pad(p.month)}-${pad(p.day)}`` ومرّره كخاصية إلى النموذج؛ وعلى الخادم اقبل تاريخاً حتى «اليوم» بمنطقة دبي مع هامش يوم. | S | web-interface-guidelines § Hydration Safety / Locale | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/transfer-form.tsx`، `apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/page.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-042 | P3 صقل | تقني | transfer-form.tsx submit() | فشل رفع الإثبات بسبب انتهاء الجلسة (401) يُعرض كخطأ عام بلا دعوة لتسجيل الدخول | `fetch('/api/orders/[id]/proof')` يعيد 401 بعد الخمول؛ الرمز `unauthorized` ليس ضمن `KNOWN_ERRORS` فيُعرض «حدث خطأ» ويحاول المستخدم إعادة الرفع مراراً. | apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/transfer-form.tsx:10-11, :41-50. | عند `res.status === 401` وجّه إلى `/signin?next=<المسار>&reason=expired`؛ أضف `unauthorized` إلى الأخطاء المعروفة بنص «انتهت جلستك. سجّل الدخول ثم أعد الرفع». | S | security-audit (session) + ui-ux-pro-max § errors | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/transfer-form.tsx` |
| VIS-075 | P3 صقل | بصري | ProofUploadForm pending | رفع ملف حتى 5MB بلا نسبة تقدم — سبينر فقط | `fetch` بلا تتبع تقدم؛ على اتصال بطيء ينتظر المستخدم ثوانٍ بزر «جارٍ الرفع» فقط ولا يعرف إن علق. | apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/transfer-form.tsx:382 `await fetch(…{ method: 'POST', body: fd })` · :427 `loading={pending}` | `XMLHttpRequest`/`upload.onprogress` مع `Progress tone="brand"` (المكوّن موجود) ومهلة مع إعادة محاولة. | M | ui-ux-pro-max §7 loading-states · §8 timeout-feedback | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/transfer-form.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-060 | P3 صقل | بصري | صف التصنيفات overflow-x-auto | شرائح التصنيف تتمرّر أفقياً بلا إشارة بدل الالتفاف | `-mx-gutter flex overflow-x-auto` مع `shrink-0`؛ التصنيفات الأخيرة خارج الشاشة بلا تدرّج. التوجيه: التفاف المجموعة قبل التمرير. | apps/client/src/app/[locale]/(public)/help/help-search.tsx:164 `<div className="-mx-gutter flex gap-2 overflow-x-auto px-gutter pb-1" role="group" …>` | `flex-wrap` (FilterChips) أو تدرّج حافة. | S | ui-ux-pro-max §5 chip-collection-reflow | `apps/client/src/app/[locale]/(public)/help/help-search.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-020 | P2 ثانوي | بصري | <bdi className="fpt-unit ui-fit">{email}</bdi> | البريد الإلكتروني داخل .fpt-unit (nowrap) يتجاوز عرض الهاتف ويُقص عند العناوين الطويلة | `.fpt-unit` تفرض `white-space:nowrap` و`ui-fit` علامة فارغة (لا تأثير). بريد من 35+ حرفاً بخط mono 15px ≈ 320px يتجاوز عرض المحتوى على 360px؛ `html{overflow-x:clip}` يخفي الفائض فيُقطع العنوان بلا قصاصة ولا التفاف. في صفوف الإحالات يقع داخل `.ui-row__t` ذات `overflow:hidden`. | apps/client/src/app/[locale]/(app)/more/page.tsx:54 · profile/page.tsx:52 · profile/email-section.tsx:299 · partners/page.tsx:312 `title={<bdi dir="ltr" className="fpt-unit">{r.email}</bdi>}` · packages/ui/src/components.css:281 التعليق «`.ui-fit` is kept as a no-op marker» · :408 `.ui-wrap { white-space: normal; overflow-wrap: anywhere; word-break: break-all }` (الحل موجود وغير مستعمل هنا) | استبدل `ui-fit` بـ `ui-wrap` على كل بريد/رابط/معرّف طويل خارج الجداول، أو اجعل `.fpt-unit` تلتف افتراضياً عند `dir="ltr"` نصوص غير مبالغ. | S | ui-ux-pro-max §6 long-token-wrapping · web-interface-guidelines §Content Handling | `apps/client/src/app/[locale]/(app)/more/page.tsx`، `apps/client/src/app/[locale]/(app)/profile/page.tsx`، `apps/client/src/app/[locale]/(app)/profile/email-section.tsx`، `apps/client/src/app/[locale]/(app)/partners/page.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-053 | P3 صقل | بصري | ClientHeader backHref | سهم الرجوع ثابت إلى /more مهما كان المصدر (الجرس في الرئيسية ← الإشعارات ← رجوع إلى «المزيد») | خريطة الرجوع ثابتة بالمسار؛ من الرئيسية عبر الجرس يصل المستخدم إلى الإشعارات ثم يعود بالسهم إلى «المزيد» لا إلى الرئيسية. المتصفح الخلفي صحيح، السهم لا. | apps/client/src/components/client-header.tsx:88 `const backHref = … : '/more';` · :108 رابط الجرس من الرئيسية | `router.back()` عند وجود سجل داخلي (`history.length > 1` + مرجع من النطاق) وإلا الخريطة الثابتة. | S | ui-ux-pro-max §9 back-behavior | `apps/client/src/components/client-header.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-078 | P3 صقل | تقني | حدود القوائم | الإشعارات تقطع عند 100 بلا ترقيم؛ الإحالات والسجل بلا حد | `userNotifications(limit:100)` يعرض آخر 100 ويخفي الأقدم بصمت؛ قوائم الإحالات/العمولات/الدفعات تُرسم كلها دفعة واحدة. | apps/client/src/app/[locale]/(app)/notifications/page.tsx:423 `{ limit: 100 }` · partners/page.tsx:306–321 · payouts/page.tsx:79–96 | ترقيم `?page=` كما في الصفقات (DEALS_PAGE) أو زر «المزيد». | S | ui-ux-pro-max §3 virtualize-lists | `apps/client/src/app/[locale]/(app)/notifications/page.tsx`، `apps/client/src/app/[locale]/(app)/partners/page.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-061 | P3 صقل | تقني | style={{…}} للتخطيط | أنماط مضمّنة للتخطيط والخط في وثائق الورق بدل أصناف documents.css | `gridTemplateColumns`, `fontSize: var(--text-body-2)`, `marginBlockStart` مضمّنة في JSX في 9 مواضع مع وجود documents.css للوثائق. | apps/client/src/app/[locale]/(app)/orders/[id]/receipt/page.tsx:56,61,118 · payouts/[id]/statement/page.tsx:64,68,86,134,152 · apps/client/src/components/certificate-card.tsx:42 | أصناف `.fpt-doc .figures--one`, `.fpt-doc .note` في documents.css. | S | ui-ux-design-pro critique «Token test» | `apps/client/src/app/documents.css`، `apps/client/src/app/[locale]/(app)/orders/[id]/receipt/page.tsx`، `apps/client/src/app/[locale]/(app)/payouts/[id]/statement/page.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-077 | P3 صقل | هوية | fpt-card لكل طلب | سجل الدفعات صندوق لكل عنصر — بينما كل القوائم الأخرى صفوف بفواصل | قواعد التأليف § 5: الصندوق يبرّر عند تجميع معلومات مترابطة أو تحديد إجراء، لا كحاوية افتراضية. كل طلب دفعة بطاقة `fpt-card` تحوي Stepper مضغوطاً؛ بقية القوائم (الطلبات، الإشعارات، المراكز) صفوف. | apps/client/src/app/[locale]/(app)/payouts/page.tsx:82 `<Link … className="fpt-card fpt-card--compact flex flex-col gap-3 …">` · docs/design-system.md § 3 «القوائم صفوف بفواصل هيرلاين» | `ListRow` بعنوان المبلغ وسطر ثانٍ (الحالة + التاريخ) ونهاية الشارة؛ المتتبع داخل صفحة الطلب. | S | fpt-brand composition § 5 «cards use-serving, not banned» | `apps/client/src/app/[locale]/(app)/payouts/page.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-065 | P3 صقل | تقني | PayoutForm state | نموذج الدفعة بأربع خطوات يحتفظ بكل شيء في الذاكرة بلا تحذير قبل المغادرة ولا حفظ مسودة | الشريط السفلي مرئي أثناء النموذج؛ لمسة على «الرئيسية» تفقد المبلغ والقناة والبيانات بصمت. | apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx:131–135 (useState فقط) · grep beforeunload → 0 | `sessionStorage` للمسودة أو تأكيد قبل المغادرة عند وجود قيم. | S | ui-ux-pro-max §8 form-autosave · web-interface-guidelines §Forms «Warn before navigation» | `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-010 | P1 رئيسي | تقني | apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx | الإرسال الضمني بمفتاح Enter يُرسل طلب الصرف من خطوة سابقة ويتخطّى المراجعة، أو يفشل صامتاً | النموذج واحد لكل الخطوات؛ زر الإرسال يُصيَّر في خطوة المراجعة فقط وزر «التالي» من نوع `button`. وفق قاعدة الإرسال الضمني في HTML: نموذج بلا زر إرسال يُرسَل عند Enter إذا كان فيه حقل نصي واحد فقط. خطوة المبلغ تحوي حقلاً نصياً واحداً (`amount-visible`)، وخطوة بيانات USDT تحوي حقلاً واحداً (`address-visible`). Enter على لوحة مفاتيح مادية/Android «Done»: (أ) في خطوة المبلغ يُستدعى `submitPayout` ببيانات ناقصة فيعود `errors.address` الذي لا يُعرض في هذه الخطوة (التنبيه العام يُصيَّر في خطوة المراجعة فقط) → فشل صامت؛ (ب) في خطوة العنوان مع مبلغ وعنوان صالحين يُنشأ طلب الصرف فعلاً ويُحوَّل المستخدم إلى صفحته دون أن يرى شاشة المراجعة (المبلغ المستلم 80 %، الوجهة). | apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx:54 (`<form action={action}`), :90 (الحقل الوحيد في خطوة المبلغ), :137 (الحقل الوحيد في خطوة crypto), :193 (التنبيه داخل `step === 'review'` فقط), :198-205 (`SubmitButton` في المراجعة فقط و`Button type="button"` للتالي)؛ HTML Living Standard § 4.10.21.2 implicit submission. | أضف للنموذج `onKeyDown={(e) => { if (step !== 'review' && e.key === 'Enter' && (e.target as HTMLElement).tagName === 'INPUT') { e.preventDefault(); if (canNext) next(); } }}`؛ وبديل أمتن: اجعل «التالي» `type="submit"` مع `onSubmit` يتقدّم خطوة ويستدعي الإجراء فقط في المراجعة. أضف اختبار e2e يضغط Enter في خطوة المبلغ ويؤكّد بقاء `data-step="channel"`. | S | web-interface-guidelines § Forms + security-audit (business flow) | `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx`، `apps/client/src/app/[locale]/(app)/payouts/actions.ts` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-002 | P1 رئيسي | RTL | Input end adornment + .fpt-input--num | لاصقة «USD» تغطي الأرقام المكتوبة في حقل المبلغ بالعربية | الحقل الرقمي `.fpt-input--num` يُجبر على `direction:ltr` وفي RTL يُعطى `text-align:start` (= يسار). لاصقة النهاية `end` تُوضع `absolute end-0` داخل غلاف RTL أي على اليسار أيضاً، بينما `.fpt-input.ui-has-end` يضيف `padding-inline-end:44px` الذي يُحسب على اتجاه الحقل (LTR) فيقع على اليمين. فالأرقام تبدأ من اليسار تحت كلمة USD مباشرة — لقطة الجهاز الحقيقي payout-amount-iphone-15-pro-max.png تُظهر «500» مطبوعة فوق «USD». | apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx:191 `<Input … mono … end={<span className="pe-3 …">USD</span>} />` · packages/ui/src/components/input.tsx:35 `absolute inset-y-0 end-0` · packages/ui/src/components.css:186 `.fpt-input.ui-has-end { padding-inline-end: 44px; }` · brand/components/components.css:89–90 `.fpt-input--num{…direction:ltr;text-align:end}` / `[dir="rtl"] .fpt-input--num{text-align:start}` · artifacts/real-device/ios/payout-amount-iphone-15-pro-max.png | اجعل غلاف الحقل الرقمي نفسه `dir="ltr"` (كما يفعل `.fpt-input-wrap` في الحزمة) فتصبح اللاصقة على اليمين والحشو على اليمين، أو بدّل `ui-has-end` إلى `padding-right` عند `.fpt-input--num`. أزل `[dir=rtl] .fpt-input--num{text-align:start}` ليبقى المبلغ محاذى للنهاية في اللغتين. | S | web-interface-guidelines §Content Handling / fpt-brand «amounts as unbreakable units» | `packages/ui/src/components/input.tsx`، `packages/ui/src/components.css`، `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-041 | P3 صقل | وصول | payout-form.tsx — أخطاء الخادم للحقول | أخطاء الخادم على حقول الخطوات السابقة تُعرض في المراجعة كتنبيه عام «حدث خطأ» دون تسمية الحقل ولا نقل التركيز | إن أعاد الخادم `errors.iban` مثلاً يُصيَّر النص داخل `Field` في خطوة البيانات (غير معروضة)، وتُظهر المراجعة `tc('error')` فقط؛ على المستخدم الرجوع خطوتين ليكتشف الحقل. | apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx:193, :136-168. | عند وصول `state.errors` بحقل من خطوة سابقة: `setStep('details'\|'amount')` تلقائياً وركّز الحقل المخطئ؛ أو اذكر في تنبيه المراجعة أسماء الحقول مع زر «تعديل البيانات». | S | web-interface-guidelines § Forms (focus first error) | `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-015 | P2 ثانوي | بصري | BottomNav (position:fixed) فوق لوحة المفاتيح | الشريط السفلي الثابت يبقى فوق لوحة المفاتيح في نماذج الإدخال ويأكل 56px من المساحة الظاهرة | الشريط `position:fixed; inset-block-end:0` يُرسم على كل شاشة موقّعة بما فيها النماذج متعددة الخطوات. على Android Chrome (الافتراضي resizes-content) يرتفع الشريط فوق لوحة المفاتيح ويغطي الحقل المركّز أو زر «التالي»؛ لا يوجد `interactive-widget` في viewport ولا إخفاء للشريط عند التركيز. | brand/components/components.css:280 `.fpt-tabbar{position:fixed;inset-inline:0;inset-block-end:0…}` · apps/client/src/components/shell.tsx:62 `bottomNav={signedIn ? <ClientBottomNav …/> : undefined}` (بلا استثناء للنماذج) · apps/client/src/app/[locale]/layout.tsx:31 viewport بلا `interactiveWidget` | أضف `interactiveWidget: 'resizes-visual'` إلى viewport (Next 15 يدعمه) أو أخفِ الشريط على مسارات النماذج (payouts/new، checkout، transfer) كما تخفي الصفحات الداخلية البلاطة. | S | ui-ux-pro-max §5 fixed-element-offset · web-interface-guidelines §Touch & Interaction | `apps/client/src/components/shell.tsx`، `apps/client/src/app/[locale]/layout.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-051 | P3 صقل | وصول | payout-form.tsx / checkout-form.tsx radios | تأكيد: صفوف الاختيار تجمع التسمية والتحكم في هدف لمس واحد 56 px مع `sr-only` + `peer-focus-visible` — لا عيب؛ ينقص `aria-describedby` لملاحظة «يُنفَّذ يدوياً» | الملاحظة «يُنفَّذ يدوياً» بجانب Fiper P2P/Card نص بصري غير مرتبط بالراديو؛ قارئ الشاشة يقرأها مفصولة. | apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx:110-120. | أعطِ الملاحظة `id` واربطها بـ `aria-describedby` على `<input type=radio>`. | S | web-interface-guidelines § Forms (checkbox/radio hit target) | `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-021 | P3 صقل | تقني | النماذج متعددة الحقول — لا حارس تغييرات غير محفوظة | لا تحذير قبل مغادرة نموذج فيه بيانات مكتوبة (`beforeunload`/حارس الموجّه غير موجود في أي ملف) | نموذج الصرف يحتفظ بالمبلغ والقناة وعنوان المحفظة/IBAN في الحالة المحلية؛ لمسة على شريط التبويبات السفلي أو سحب للرجوع يفقد كل شيء بلا سؤال. | grep `beforeunload` في apps/*/src packages/ui/src → 0؛ apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx:30-34; packages/ui/src/components/bottom-nav.tsx (روابط Link كاملة). | خطّاف `useUnsavedChanges(dirty)` يسجّل `beforeunload` ويعترض نقرات `Link` داخل الشريط (confirm مترجم) عندما `amount \|\| details.*` غير فارغ ولم يُرسل النموذج. | S | web-interface-guidelines § Forms (warn before navigation) | `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx`، `apps/client/src/app/[locale]/(app)/profile/closure-form.tsx`، `apps/client/src/app/[locale]/(app)/profile/kyc-section.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-062 | P3 صقل | هوية | ألوان هكس خام | قيم هكس خام ('#FFFFFF', '#0A0A0A') في كود المكوّن لرمز QR | القاعدة: لا هكس خارج brand/tokens.css. الإدارة تحلّها بـ `currentColor`؛ العميل يكتب الهكس في forms.tsx. | apps/client/src/app/[locale]/(app)/profile/forms.tsx:220 `color: { dark: '#FFFFFF', light: '#0A0A0A' }` · apps/admin/src/lib/qr.ts:9–11 (`fill="none"` / `stroke="currentColor"`) | أعد استعمال نهج `qrSvg` (currentColor) على أرضية الورق. | S | fpt-brand «no hex outside tokens» | `apps/client/src/app/[locale]/(app)/profile/forms.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-017 | P2 ثانوي | وصول | revokeSessionAction SubmitButton | إنهاء جلسة / إنهاء كل الجلسات / تسجيل الخروج من الجهاز الحالي بلا تأكيد | صفوف الأجهزة تُرسل `revokeSessionAction` مباشرة من زر داخل الصف (بما فيه «خروج» للجهاز الحالي بزر destructive) و«إنهاء الكل» في جانب القسم؛ لا حوار ولا نافذة تراجع. ضغطة خاطئة على هاتف تُخرج المستخدم. | apps/client/src/app/[locale]/(app)/profile/page.tsx:75–80 (revokeAll) و:102–107 `<form action={revokeSessionAction}>…<SubmitButton variant={… 'destructive' : 'secondary'} size="sm">` | حوار تأكيد (Dialog من @fpt/ui) للجهاز الحالي و«إنهاء الكل»، أو toast بتراجع للجلسات الأخرى. | S | ui-ux-pro-max §8 confirmation-dialogs · web-interface-guidelines §Navigation & State «Destructive actions need confirmation» | `apps/client/src/app/[locale]/(app)/profile/page.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-076 | P3 صقل | بصري | products.map بلا حالة فراغ | عند غياب منتجات نشطة يُرسم المتجر والهبوط فارغين بلا رسالة | `ChallengeList` و`ul` الهبوط يرسمان قائمة فارغة؛ زر الشراء يختفي (`chosen` undefined) ولا تفسير. | apps/client/src/app/[locale]/(app)/shop/challenge-picker.tsx:178–196 و:210 `{chosen ? … : null}` · apps/client/src/app/[locale]/(public)/page.tsx:50–67 | `EmptyState` «لا تحديات متاحة الآن» مع رابط المساعدة. | S | ui-ux-pro-max §8 empty-states | `apps/client/src/app/[locale]/(app)/shop/challenge-picker.tsx`، `apps/client/src/app/[locale]/(public)/page.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-052 | P3 صقل | بصري | ClientBottomNav active logic | تبويب «الحساب» يُضاء وأنت في المتجر وصفحة الدفع | منطق النشاط يضيء تبويب الحساب على `/shop` و`/checkout` (لأن رابطه يذهب للمتجر قبل أول شراء)، فيقرأ المستخدم «الحساب» وهو يختار حجم تحدٍّ. | apps/client/src/components/bottom-nav.tsx:22 `active: onAccount \|\| is('/shop') \|\| is('/checkout')` | أضئ «الحساب» على `/accounts/*` فقط؛ المتجر والدفع بلا تبويب نشط (أو ضمن «المزيد» حيث يقع رابط «ابدأ تحدياً»). | S | ui-ux-pro-max §9 nav-state-active | `apps/client/src/components/bottom-nav.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-010 | P2 ثانوي | بصري | ChallengeCard (role=radio) + ChallengePicker | بطاقة الحجم تعلن أنها خيار (radio) لكنها تُرسل النموذج عند اللمس | `ChallengeCard` يرسم `<button type="button" role="radio" aria-checked>` ثم ينشر `{...props}` بعده، والـ picker يمرّر `type="submit" name="productCode"` فيصبح الزر إرسالاً: لمس أي حجم يذهب فوراً إلى الدفع ولا يرى المستخدم القواعد المختصرة ولا زر الشراء الأساسي الذي صُمّمت الشاشة حوله. الحالة «المختار» وحلقة الراديو زينة فعلياً. | apps/client/src/app/[locale]/(app)/shop/challenge-picker.tsx:181–183 `<ChallengeCard key={p.code} type="submit" name="productCode" value={p.code}` · packages/ui/src/components/challenge-card.tsx:37–47 `type="button" role="radio" … {...props}` · challenge-picker.tsx:166 التعليق «A tap on a card selects it; the CTA submits» | اجعل البطاقات اختياراً فقط (`type="button"`) واحتفظ بالإرسال لزر الشراء؛ للاختبار الذهبي أبقِ `data-testid` على زر الشراء بدل البطاقة. | S | ui-ux-pro-max §2 hover-vs-tap · §8 error-prevention · ui-ux-design-pro Nielsen #5 | `apps/client/src/app/[locale]/(app)/shop/challenge-picker.tsx`، `packages/ui/src/components/challenge-card.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-023 | P3 صقل | وصول | apps/client/src/app/[locale]/(auth)/auth-form.tsx PasswordSigninForm | خطأ تسجيل الدخول يُعرض كتنبيه أعلى النموذج بلا `aria-invalid` على الحقول وبلا نقل التركيز إلى الخطأ | بعد «البريد أو كلمة المرور غير صحيحة» يبقى التركيز على زر الإرسال؛ الحقلان لا يحملان `error` فلا `aria-invalid`/`aria-describedby`. الإرشادات: أخطاء بجانب الحقول وتركيز أول خطأ. | apps/client/src/app/[locale]/(auth)/auth-form.tsx:57-65 (`{error ? <Alert tone="danger">…` ثم `Field` بلا `error`). | مرّر `error` إلى حقل كلمة المرور (أو كليهما بالنص نفسه) ليُضاف `aria-invalid` و`role=alert`، وركّز الحقل الأول المخطئ بعد تغيّر `state` (`useEffect` + `ref.focus()`)؛ أبقِ `Alert` للحالات العامة (`suspended`/`closed`). | S | web-interface-guidelines § Forms (errors inline, focus first error) | `apps/client/src/app/[locale]/(auth)/auth-form.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-020 | P3 صقل | تقني | auth-form.tsx + payout-form.tsx — autoFocus | `autoFocus` على الهاتف في تسجيل الدخول وخطوات الصرف يفتح لوحة المفاتيح فور التحميل ويدفع المحتوى | الإرشادات: autoFocus باعتدال وعلى سطح المكتب فقط. يُطبَّق على بريد الدخول/التسجيل، كلمة المرور الجديدة، حقل المبلغ، والعنوان/اسم الحامل في كل فرع من خطوة البيانات؛ لأن المقطع يُصيَّر شرطياً فيُعاد التركيز عند كل خطوة (مرغوب لنقل التركيز لكنه يفتح اللوحة ويخفي العنوان والمقدّمة على 360–390 px). | apps/client/src/app/[locale]/(auth)/auth-form.tsx:42, :61, :114; apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx:90, :137, :142, :151, :160. | في `Input` اجعل `autoFocus` فعّالاً فقط عندما `matchMedia('(pointer: fine)')` (أو `focus({ preventScroll: true })` على العنوان `h2` بدل الحقل عند تغيّر الخطوة، فيُقرأ العنوان ولا تفتح اللوحة)؛ اترك autoFocus لحقل رمز OTP (مقبول). | S | web-interface-guidelines § Touch & Interaction (autoFocus) | `apps/client/src/app/[locale]/(auth)/auth-form.tsx`، `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx`، `packages/ui/src/components/input.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-040 | P2 ثانوي | تقني | apps/client/src/app/[locale]/(auth)/auth-form.tsx — `<form action>` بحقول غير مُتحكَّم بها | React 19 يفرّغ الحقول غير المتحكَّم بها بعد كل إجراء: عند خطأ التحقق يختفي البريد/كلمة المرور ويعيد المستخدم الكتابة | نماذج المصادقة تستخدم `<form action={formAction}>` مع `Input` غير متحكَّم (`name` فقط). React 19 يستدعي `requestFormReset` بعد اكتمال الإجراء حتى عند الخطأ، فبعد «البريد أو كلمة المرور غير صحيحة» يُمسح الحقلان. لوحة الموظفين لاحظت ذلك وتفاديه بإرسال يدوي (`onSubmit` + `startTransition(formAction(fd))`)؛ العميل لا. | apps/client/src/app/[locale]/(auth)/auth-form.tsx:39-47, :57-69, :106-122; apps/admin/src/components/ActionForm.tsx:43-49 (التعليق والحل). | طبّق نمط الإدارة نفسه في `auth-form.tsx` (و`SubmitButton` يبقى على `useFormStatus` لأنه داخل `<form>`)، أو أعد البريد من الإجراء في `state.values.email` وامنحه `defaultValue`. أضف اختبار e2e: كلمة مرور خاطئة ثم تأكّد بقاء البريد. | S | web-interface-guidelines § Forms / Hydration (controlled vs uncontrolled) | `apps/client/src/app/[locale]/(auth)/auth-form.tsx`، `apps/client/src/components/submit-button.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-009 | P2 ثانوي | تقني | apps/client/src/app/[locale]/(auth)/signin/page.tsx | قفل المصادقة الثنائية يحوّل إلى `/signin?mfa=locked` لكن صفحة تسجيل الدخول تتجاهل المعامل: المستخدم يعود إلى النموذج بلا أي رسالة | عند الفشل المتكرر في رمز TOTP يُلغى الجلسة وتُمسح الكوكي ويُحوَّل المستخدم مع `mfa=locked`. صفحة تسجيل الدخول تقرأ `next\|mode\|reset` فقط؛ لا تنبيه ولا توضيح لمدة القفل، فيعيد المستخدم كلمة مروره ثم يُقفل من جديد عند الرمز. | apps/client/src/app/[locale]/(auth)/actions.ts:222-226 (`redirect(`/${locale}/signin?mfa=locked`)`)؛ apps/client/src/app/[locale]/(auth)/signin/page.tsx:17-19 (`searchParams: { next?, mode?, reset? }` — لا `mfa`). | اقرأ `mfa` في الصفحة واعرض `Alert tone="warn"` بنص «محاولات كثيرة لرمز المصادقة. انتظر 10 دقائق ثم سجّل الدخول من جديد.» (مطابق لمدة القفل في `lockout`)، مع `role="alert"`. | S | ui-ux-pro-max § feedback / security-audit (lockout UX) | `apps/client/src/app/[locale]/(auth)/signin/page.tsx`، `apps/client/src/app/[locale]/(auth)/actions.ts`، `packages/i18n/src/messages/ar.json` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-011 | P1 رئيسي | بصري | <p className="text-xs"> | جمل كاملة بحجم 12px في تذييل شاشات التسجيل والتحقق | تذييل AuthCard يضع جملة الشروط، جملة الإحالة، شرح المصادقة الثنائية وجملة «فقدت الجهاز؟» في `<p className="text-xs">` (12px). هذه جمل لا تعليقات؛ أرضية الجسم 15px. البوّابة تستثني `.text-xs` صراحةً فلم تُلتقط. | apps/client/src/app/[locale]/(auth)/signup/page.tsx:29–30 `<p className="text-xs">{t('terms')}</p>` · apps/client/src/app/[locale]/(auth)/signup/password/page.tsx:72 · apps/client/src/app/[locale]/(auth)/2fa/page.tsx:31 · e2e/ux-audit.ts:75 (استثناء `.text-xs`) | `text-body-2 text-muted` لهذه الفقرات؛ قصر `.text-xs` على التعليقات القصيرة (تاريخ، عدّاد). أزل `.text-xs` من قائمة الاستثناء عندما يكون العنصر `<p>`. | S | design-system § 2 · ui-ux-pro-max §5 readable-font-size | `apps/client/src/app/[locale]/(auth)/signup/page.tsx`، `apps/client/src/app/[locale]/(auth)/signup/password/page.tsx`، `apps/client/src/app/[locale]/(auth)/2fa/page.tsx` |

### العميل — 

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-001 | P1 رئيسي | تقني | apps/client/public/sw.js — fetch handler (navigate) | عامل الخدمة يخزّن HTML الصفحات المصادَق عليها ويعرضها دون اتصال بعد تسجيل الخروج | معالج fetch يخزّن كل استجابة تنقّل ناجحة غير مُعاد توجيهها في مخزن `fpt-client-v2-pages` (`cache.put(req, res.clone())`) متجاهلاً `Cache-Control: private, no-store` الذي يرسله الخادم لكل صفحة ديناميكية. صفحات مثل `/ar/accounts/[id]` (البريد بصفته cTrader ID، رقم الحساب، الخادم، الرصيد والصفقات) تبقى في Cache Storage. عند انقطاع الاتصال يعيد المعالج `caches.match(req)` دون فحص الجلسة، ومسار `/api/signout` يلغي الجلسة ويمسح الكوكي لكنه لا يُفرغ المخزن. على جهاز مشترك: تسجيل خروج ثم وضع الطيران ثم فتح العنوان يعرض آخر نسخة من صفحة الحساب. | apps/client/public/sw.js:37-45 (`if (res.ok && !res.redirected) caches.open(PAGES).then((cache) => cache.put(req, res.clone()))` و`.catch(async () => (await caches.match(req)) \|\| Response.error())`); apps/client/src/app/api/signout/route.ts:13-22 (لا postMessage ولا `caches.delete`); curl https://fptraders.app/ar → `cache-control: private, no-cache, no-store, max-age=0, must-revalidate`; apps/client/src/components/credentials-panel.tsx:26-32 (البيانات المعروضة). | في معالج التنقّل: لا تخزّن أي استجابة تحمل `no-store`/`private` في `cache-control` ولا أي مسار داخل `(app)` (accounts/payouts/orders/profile/notifications/checkout)؛ خزّن مسبقاً صفحة واحدة `/[locale]/offline` ذات هوية وأعدها كبديل. عند تسجيل الخروج أرسل `navigator.serviceWorker.controller.postMessage({type:'signout'})` فيمسح العامل مخزن PAGES (أو امسحه من العامل عند رؤية 303 من `/api/signout`). | M | security-audit + web-interface-guidelines § Navigation & State | `apps/client/public/sw.js`، `apps/client/src/app/api/signout/route.ts`، `apps/client/src/app/[locale]/(app)/more/page.tsx` |

### العميل — 2fa (المصادقة الثنائية)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-009 | P2 ثانوي | وصول | TotpForm → OtpInput بلا digitLabel | خانات رمز المصادقة الثنائية تُقرأ بالإنجليزية «Digit 1 of 6» في الصفحة العربية | CodeForm (التحقق بالبريد) يمرّر t('digit') المترجم، أمّا TotpForm فيترك الافتراضي الإنجليزي؛ الصفحة lang=ar فيُنطق النص الإنجليزي بالصوت العربي. (قياس صفحة التحقق: «الرقم 1 من 6» صحيحة؛ 2fa مُراجَع من الكود لأنها تتطلب حساباً مفعِّلاً للمصادقة.) | apps/client/src/app/[locale]/(auth)/auth-form.tsx:120 (TotpForm) مقابل :84 (CodeForm); packages/ui/src/components/otp-input.tsx:165; .e2e/a11y-client.log desktop:verify:otp | مرّر digitLabel={(i, n) => t('digit', …)} في TotpForm (المفتاح auth.verify.digit موجود) وأضف مفتاحاً في auth.twofa. | S | ui-ux-pro-max `aria-labels`, `voiceover-sr`; WCAG 3.1.2 | `apps/client/src/app/[locale]/(auth)/auth-form.tsx` |

### العميل — 404-root

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-048 | P3 صقل | نص | صفحة غير موجودة الجذرية | صفحة 404 الجذرية تعرض الرسالة بالعربية والإنجليزية معاً بلا رأس ولا تبويبات | سطران متتاليان باللغتين وزر واحد؛ مقبول للمسار بلا لغة لكنه مختلف بصرياً عن 404 الداخلية التي تحمل القشرة كاملة. | artifacts/ux-audit-F4/evidence/client-404-root-ar-phone.png; artifacts/ux-audit-F4/evidence/client-404-account-ar-phone.png; apps/client/src/app/not-found.tsx:30 | اقرأ لغة الكوكي/Accept-Language واعرض لغة واحدة مع مبدّل صغير. | S | ui-ux-pro-max §4 consistency؛ Nielsen #4 | `apps/client/src/app/not-found.tsx` |

### العميل — PWA — تحديث عامل الخدمة

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-030 | P3 صقل | تقني | apps/client/public/sw.js VERSION / activate | إصدار المخزن ثابت `fpt-client-v2`: نسخ HTML القديمة تبقى عبر النشرات، والتفعيل الفوري بلا أي إشعار «تحديث متاح» | `skipWaiting` + `clients.claim` يُفعّلان العامل الجديد فوراً، لكن مخزن PAGES لا يُمسح إلا عند تغيّر `VERSION` (لم يتغيّر مع النشرات). دون اتصال قد يُعرض HTML من نشرة سابقة يشير إلى chunks قديمة (مخزّنة أيضاً فيعمل) بلا أي إشارة للمستخدم أنه يرى نسخة قديمة. | apps/client/public/sw.js:3-5, :9, :12-14; apps/client/src/components/sw-register.tsx:9 (لا `updatefound`/`controllerchange`). | احقن معرّف النشر في `VERSION` وقت البناء (نسخ sw.js عبر سكربت مع `process.env.VERCEL_GIT_COMMIT_SHA`)، أو امسح PAGES في `activate`؛ في `SwRegister` استمع إلى `controllerchange` واعرض toast «تحديث متاح — أعد التحميل». | S | web-interface-guidelines § Navigation & State (PWA) | `apps/client/public/sw.js`، `apps/client/src/components/sw-register.tsx` |

### العميل — PWA — مخزن STATIC

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-031 | P3 صقل | أداء | apps/client/public/sw.js isStatic branch | مخزن `_next/static` و`/brand` cache-first بلا سقف ولا تنظيف: ينمو مع كل نشرة | كل chunk جديد يُضاف ولا يُحذف القديم ما دام `VERSION` ثابتاً؛ على الهواتف منخفضة التخزين تُطرد المواقع كاملة عند تجاوز الحصة. | apps/client/public/sw.js:23-34. | بعد `cache.put` قصّ المخزن إلى ~80 مدخلاً (أقدم أولاً عبر `cache.keys()`)، أو اربط مسح STATIC بـ `VERSION` المحقون. | S | web-interface-guidelines § Performance | `apps/client/public/sw.js` |

### العميل — account (overview) على الهاتف — ورقة تفاصيل القاعدة

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-014 | P2 ثانوي | وصول | SheetContent close button (absolute end-2 top-2) | زر إغلاق الورقة السفلية يقع 8 px خارج حافة الشاشة على الهاتف | القياس 390×844: rect زر «إغلاق» = [x −8, y 808, 44, 44] → 36 px فقط ظاهرة ومجاورة لحافة الشاشة السفلية (منطقة إيماءات النظام). | .e2e/a11y-client.log phone:account:ruleSheet inSheet.rect=[-8,808,44,44] | تحقّق من حاوية الورقة (inset-x-0 + حشو) وضع الزر داخل رأس الورقة في التدفّق (SheetHeader) بدل absolute؛ أو ارفعه فوق safe-area. | S | ui-ux-pro-max `touch-target-size`, `safe-area-awareness` | `packages/ui/src/components/sheet.tsx` |

### العميل — account (phone) — RuleMeterRow

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-042 | P3 صقل | وصول | .ui-meter-row .ui-meter__state (12 px) | سطر حالة المقياس المضغوط 12 px على الهاتف — عند أرضية الحجم الدنيا تماماً | القياس على 390: .ui-meter__state size 12px, color rgba(255,255,255,.55) على #0A0A0A = 6.28:1 (يجتاز التباين)، لكنه النص الوحيد الذي يشرح «المتبقي/المستخدم» وهو عند الحدّ الأدنى 12 px. | .e2e/a11y-client.log phone:home:colours .ui-meter__state 12px; packages/ui/src/components.css:356 | ارفعه إلى 13 px (--text-label) على الهاتف أيضاً (نفس قيمة ≥ 640). | S | docs/design-system.md § 2 (nothing visible < 12 px) | `packages/ui/src/components.css` |

### العميل — account 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-004 | P1 رئيسي | RTL | شريط التبويبات (Tabs) | ترتيب تبويبات الحساب معكوس في العربية: «نظرة» (الأول والنشط) في أقصى اليسار | على iPhone وSamsung وChromium يظهر الشريط «البيانات · القواعد · الصفقات · نظرة» من اليمين، أي أن التبويب الأول «نظرة» وقع في آخر الصف. Radix Tabs بلا DirectionProvider يرتّب العناصر LTR، فتبدأ القراءة العربية من التبويب الرابع. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-account.png; artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-account.png; artifacts/real-device/coverage-20261030/local-chromium-390/client-account.png; artifacts/ux-audit-F4/evidence/client-rules-funded-ar-phone.png; packages/ui/src/components/tabs.tsx; apps/client/src/app/[locale]/(app)/accounts/[id]/page.tsx:109-128 | مرّر dir="rtl" إلى Tabs.Root (أو لفّ التطبيق بـ DirectionProvider من radix-ui) حتى يبدأ «نظرة» من اليمين ويتحرك التنقل بالأسهم في الاتجاه الصحيح. | S | ui-ux-pro-max §9 nav-state-active / navigation-consistency؛ cognitive-principles Continuity | `packages/ui/src/components/tabs.tsx`، `apps/client/src/app/[locale]/(app)/accounts/[id]/page.tsx` |

### العميل — account 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-059 | P3 صقل | بصري | مفتاح المخطط | مفتاح المخطط نص 12 px رمادي على سطر واحد مزدحم أسفل البطاقة | «هدف الربح – – · حدّ الخسارة اليومية / الإجمالية ··· · الرصيد الابتدائي ···· · القيمة الحالية •» أربع رموز متقاربة بخط صغير؛ على 390 يلتف ويصعب ربط كل رمز بخطه. | artifacts/real-device/coverage-20261030/local-chromium-390/client-home.png; artifacts/ux-audit-F4/evidence/client-home-demo08-ar-phone.png | تسمية مباشرة على الخطوط (كما فُعل مع 11.0k) وحذف المفتاح، أو مفتاح بعمودين 13 px. | S | ui-ux-pro-max §10 direct-labeling / legend-visible؛ dataviz | `packages/ui/src/components/equity-chart.tsx` |

### العميل — account

اللقطات: `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-account-en.png`، `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-account.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-account.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-account-en.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-account.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-account-en.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-filter-empty-ar-phone.png` … (+10)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-026 | P2 ثانوي | بصري | رأس الحساب (المرجع، الحجم، رابط الطلب، الشارة) | شارة الحالة تلتف إلى سطرها الخاص بفراغ كبير ورابط «عرض الطلب والإيصال» لا يبدو قابلاً للنقر | في كل الأجهزة يظهر رأس الحساب كأربعة أسطر متفاوتة: المرجع، سطر الحجم، رابط رمادي بلا تسطير، ثم الشارة وحدها في صف جديد بعد فراغ 40–60 px. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-account.png; artifacts/real-device/coverage-20261030/local-chromium-390/client-account.png; apps/client/src/app/[locale]/(app)/accounts/[id]/page.tsx:70-85 | ضع الشارة بجانب المرجع في السطر الأول (flex-nowrap, badge size=sm)، وأعطِ الرابط underline أو شكل ListRow واحد «الطلب والإيصال ›». | S | critique-protocol Layout rhythm؛ ui-ux-pro-max §2 cursor-pointer/affordance؛ mobile-app-ui-design spacing | `apps/client/src/app/[locale]/(app)/accounts/[id]/page.tsx` |

### العميل — account-breached

اللقطات: `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-account-breached.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-account-breached.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-account-breached.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-058 | P3 صقل | بصري | الزر الأساسي | «ابدأ تحدياً جديداً» زر أحمر كامل العرض على حساب موقوف يسبق تحليل المخالفة | بعد تنبيه المخالفة يظهر بيع تحدٍّ جديد كالفعل الأساسي قبل أن يرى المستخدم العدّادات أو التقرير؛ يبدو انتهازياً في لحظة سلبية (peak-end). | artifacts/real-device/coverage-20261030/local-chromium-390/client-account-breached.png | اجعل «اقرأ تقرير الإيقاف» هو الأساسي داخل التنبيه، و«ابدأ تحدياً جديداً» ثانوياً أسفل الصفحة. | S | mobile-app-ui-design Peak-End rule؛ ui-ux-pro-max §4 primary-action | `apps/client/src/app/[locale]/(app)/accounts/[id]/page.tsx` |

### العميل — account-credentials, more (cTrader), landing badge

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-036 | P3 صقل | وصول | نصوص لاتينية داخل صفحات عربية بلا lang="en" (cTrader Web, Desktop (Windows), Mac, iOS, Android, FPT-…) | المقاطع اللاتينية داخل العربية معزولة اتجاهياً (bdi/dir=ltr) لكن بلا lang=en فتُنطق بصوت عربي | القياس: أزرار الفتح نصّها «cTrader Web / Desktop (Windows) / Mac / iOS / Android» دون lang؛ الشعار في الصفحة العامة يحمل lang="en" (صحيح). العزل الاتجاهي صحيح في كل العيّنات (dir/lang الجذر صحيحان: ar/rtl و en/ltr). | .e2e/a11y-client.log *:account:credentials; apps/client/src/components/platform-open-buttons.tsx; apps/client/src/app/[locale]/(public)/page.tsx:42 | أضف lang="en" إلى <bdi>/الأزرار ذات أسماء المنتجات والمنصّات (قاعدة الهوية: اسم المنتج لاتيني داخل العربية). | S | WCAG 3.1.2 Language of Parts; brand/README (Latin product name) | `apps/client/src/components/platform-open-buttons.tsx`، `apps/client/src/components/credentials-panel.tsx` |

### العميل — account-credentials

اللقطات: `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-account-credentials.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-account-credentials.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-account-credentials.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-070 | P3 صقل | بصري | أزرار «افتح cTrader» | خمسة أزرار منصّة متساوية الوزن تلتف على سطرين مع شارة «يعمل بعد الربط» | cTrader Web / Desktop (Windows) / Mac / iOS / Android كلها بالنمط نفسه؛ على الهاتف الخيار المهم (iOS أو Android حسب الجهاز) لا يبرز. | artifacts/ux-audit-F4/evidence/client-credentials-funded-ar-phone.png; apps/client/src/components/platform-open-buttons.tsx | اكتشف المنصة (UA) وأبرز زرها كزر أساسي ثانوي، والبقية تحت «منصات أخرى» قابلة للطي. | M | Hick's law؛ ui-ux-pro-max §4 primary-action / platform-adaptive | `apps/client/src/components/platform-open-buttons.tsx` |
| VCL-071 | P3 صقل | نص | تسمية «cTrader ID (البريد)» | تسمية مختلطة الاتجاه «cTrader ID (البريد)» بقوسين عربيين داخل لاتيني | التسمية تبدأ لاتينياً وتنتهي بقوس عربي، فتُرسم الأقواس في موضع غير متوقع. | artifacts/ux-audit-F4/evidence/client-credentials-funded-ar-phone.png | «معرّف cTrader — بريدك الإلكتروني» أو «cTrader ID» فقط. | S | fpt-bidi-currency-trap؛ fpt-brand (اللاتيني داخل العربي) | `packages/i18n/src/messages/ar.json` |

### العميل — account-rules (funded)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-079 | P1 رئيسي | RTL | قيمة «أيام التداول» | القيمة «12 من 10» تُرسم «من 10 12» في تبويب القواعد على 390 | في بطاقة «أيام التداول» على الهاتف تظهر القيمة بترتيب مقلوب «من 10 12»: النص العربي «{count} من {required}» وُضع داخل عزل LTR (num-ui/fpt-unit) فيبدأ بالرقم 12 ثم يُعامل «من 10» كمقطع RTL داخل سياق LTR ويُقلب. المعنى يصبح غامضاً (10 أم 12؟) في معلومة تحدد أهلية السحب. | artifacts/ux-audit-F4/evidence/client-rules-funded-ar-phone.png (780 px، الصف «أيام التداول»); packages/i18n/src/messages/ar.json:254 (rule.daysValue); apps/client/src/app/[locale]/(app)/accounts/[id]/page.tsx:135-157 | لا تضع جملة تحتوي كلمات عربية داخل num-ui؛ اعزل الرقمين كلاً على حدة (<bdi class=num-ui>12</bdi> من <bdi class=num-ui>10</bdi>) أو اعرض «12 / 10» برمز واحد. | S | fpt-bidi-currency-trap؛ ui-ux-pro-max §6 number-tabular؛ fpt-brand (الأرقام اللاتينية داخل العربية) | `apps/client/src/app/[locale]/(app)/accounts/[id]/page.tsx`، `packages/ui/src/components/key-value.tsx` |
| VCL-029 | P2 ثانوي | نص | عدّاد الخسارة اليومية/الإجمالية | كلمة «المتبقي» مكررة على طرفي الصف والرقم البطل «0 %» عديم المعلومة | يظهر «المتبقي 5,750.00 USD» يميناً و«المتبقي» يساراً تحت الشريط، والرقم الكبير «0 %» (المستهلك) يسرق الانتباه من الرقم المفيد (المتبقي بالدولار). | artifacts/ux-audit-F4/evidence/client-rules-funded-ar-phone.png; packages/ui/src/components/rule-meter.tsx:51-54,82-86 | في وضع figure=percent اعرض تسمية «المستهلَك» تحت النسبة و«المتبقي» مرة واحدة، أو اجعل المتبقي بالدولار هو الرقم البطل كما في الرئيسية. | S | mobile-app-ui-design anti-patterns (emphasizing labels over values)؛ Nielsen #8 | `packages/ui/src/components/rule-meter.tsx`، `apps/client/src/app/[locale]/(app)/accounts/[id]/page.tsx` |

### العميل — all (route transitions)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-044 | P2 ثانوي | تقني | حالة التحميل بين الصفحات | لا loading.tsx في مسارات العميل: الانتقال بين الصفحات بلا أي ملاحظة بصرية | لا يوجد أي ملف loading.tsx تحت apps/client/src/app؛ مع صفحات خادم تجلب البيانات تبقى الشاشة السابقة جامدة حتى تصل التالية (DCL على iPhone 1–3 ث في التقرير). | find apps/client/src/app -name loading.tsx → لا نتائج؛ artifacts/real-device/coverage-20261030/iphone-15-pro-max/coverage-receipt.json (dclMs) | loading.tsx بهيكل Skeleton لكل مجموعة (app) مطابق لتخطيط الصفحة (بطل + عدّادات + قائمة) وفق قرار TWO Markets (Skeleton + SkeletonTimeout + retry). | M | ui-ux-pro-max §3 progressive-loading / content-jumping؛ §7 loading-states | `apps/client/src/app/[locale]/(app)/loading.tsx`، `packages/ui/src/components/skeleton.tsx` |

### العميل — breach

اللقطات: `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-breach.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-breach.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-breach.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-056 | P2 ثانوي | بصري | العنوان الرئيسي | العنوان يكرر كل معلومات السطر التالي ويمتد ثلاثة أسطر بخط 24 px | «أُوقف حسابك في 08:06 بتوقيت دبي لتجاوز حدّ الخسارة اليومي» ثم سطر ثانٍ بالمرجع والقاعدة نفسها، ثم «ماذا حدث» بالرقم −615.00 USD ثم فقرة تعيد الأرقام نفسها رابعة. أربع صياغات لحدث واحد. | artifacts/real-device/coverage-20261030/local-chromium-390/client-breach.png | عنوان قصير «تجاوز حدّ الخسارة اليومي»، ثم الرقم البطل، ثم KeyValue، ثم جملة سردية واحدة. | S | critique-protocol Hierarchy / squint test؛ Nielsen #8 | `apps/client/src/app/[locale]/(app)/accounts/[id]/breach/page.tsx` |
| VCL-057 | P3 صقل | هوية | فقرة السرد | المبالغ في الفقرة مكتوبة «94,100.00 دولار» بدل «94,100.00 USD» | القيم في البطاقات تستخدم USD وفي الفقرة «دولار» ثلاث مرات. | artifacts/real-device/coverage-20261030/local-chromium-390/client-breach.png | استخدم formatMoney مع isolate داخل الفقرة. | S | fpt-brand (المبلغ وحدة برمز العملة) | `apps/client/src/app/[locale]/(app)/accounts/[id]/breach/page.tsx`، `packages/i18n/src/messages/ar.json` |

### العميل — certificates 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-064 | P3 صقل | نص | سطر التعريف | «شهادة · Certificate · FPT-CRWFSEA8F4» يمزج اللغتين في سطر واحد | الوثيقة ثنائية اللغة عمداً، لكن السطر الصغير يقدم الكلمة بالعربية ثم الإنجليزية ثم الرمز، بينما العنوان الفرعي الإنجليزي كامل تحته أيضاً؛ تكرار. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-certificates.png | السطر الصغير: الرمز فقط «FPT-CRWFSEA8F4»، والعنوان الفرعي الإنجليزي يحمل كلمة Certificate. | S | Nielsen #8؛ fpt-brand composition | `apps/client/src/components/certificate-card.tsx` |

### العميل — certificates

اللقطات: `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-certificates.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-certificates.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-certificates.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-063 | P3 صقل | بصري | بطاقة الشهادة | خط التوقيع تحت اسم المتداول ثم خط فاصل آخر بعده مباشرة — خطان متجاوران | خط تحت «Khalid Al-Mansoori» (نمط توقيع) يليه فاصل القسم على بعد 40 px؛ يبدوان كتكرار. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-certificates.png | أبقِ خط التوقيع واحذف الفاصل، أو العكس. | S | critique-protocol Surfaces (one strategy)؛ whitespace-balance | `apps/client/src/components/certificate-card.tsx` |

### العميل — checkout (iPhone)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-074 | P3 صقل | تقني | تغطية الأدلة | لقطتا checkout وcheckout-transfer «العربية» على iPhone التُقطتا بالإنجليزية | الإيصال يسجّل lang=en وdir=ltr للمفتاحين client/checkout@ar وclient/checkout-transfer@ar؛ لا توجد لقطة عربية للدفع على الجهاز الحقيقي. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/coverage-receipt.json (client/checkout@ar url:/en/checkout/…) | ثبّت اللغة قبل كتلة checkout في المشي (اذهب إلى /ar/shop صراحة) وأفشل المفتاح إذا اختلفت lang عن المطلوبة. | S | evidence integrity | `e2e/screens.ts`، `scripts/real-device-coverage.mjs` |

### العميل — checkout 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-047 | P3 صقل | بصري | رأس الصفحة الشفاف | النص يتسلل خلف الرأس ويبقى مقروءاً جزئياً فوق زر الرجوع | على Samsung «الإجمالي» يظهر عبر الرأس تحت زر الرجوع؛ على iPhone سطر التعليمات مقروء خلف الرأس. الشفافية 85 % + blur 14 px لا تكفي عند النص الفاتح. | artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-checkout.png; artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-checkout-transfer.png; brand/tokens.css:85,366 | ارفع --fpt-ground-chrome إلى 92 % (كما في brand .fpt-tabbar) أو أضف خطاً سفلياً وscroll-padding-top. | S | Common Rules Light/Dark — Scrim and modal legibility؛ ui-ux-pro-max §5 fixed-element-offset | `packages/ui/src/components.css`، `brand/tokens.css` |

### العميل — checkout 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-022 | P3 صقل | بصري | مؤشر الخطوات (stepper) | المؤشر على الهاتف أرقام وأيقونات بلا تسميات | على 430 و390 يظهر «1 ✓ · 2 💳 · 3 ⧗ · 4 ✓» بلا كلمات، بينما سطح المكتب يعرض «الاختيار · الدفع · التزويد · جاهز». على الهاتف — حيث الأغلبية — يفقد المؤشر معناه. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-payout-new.png; artifacts/real-device/coverage-20261030/local-chromium-390/client-checkout.png; artifacts/ux-audit-F4/evidence/client-checkout-coupon-invalid-ar-phone.png | أظهر تسمية الخطوة الحالية على الأقل تحت المؤشر («الخطوة 2 من 4 · الدفع»)، أو تسميات قصيرة 12 px لكل الخطوات. | S | ui-ux-pro-max §8 multi-step-progress؛ Miller | `apps/client/src/components/purchase-steps.tsx`، `apps/client/src/components/payout-tracker.tsx` |

### العميل — checkout

اللقطات: `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-checkout-en.png`، `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-checkout.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-checkout.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-checkout-en.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-checkout.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-checkout-en.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-001 | P0 حاجب | نص | سطر قبول المستندات (checkbox terms) | جملة الموافقة على الشروط فارغة في الإنتاج: «أقبل و و (، سارية من ).» | على جهاز Samsung S23 (الإنتاج) وعلى Chromium 390 المحلي تظهر جملة القبول بلا أسماء مستندات ولا نسخة ولا تاريخ: «أقبل و و (، سارية من ).» لأن قائمة docs التي تصل إلى CheckoutForm خالية فيُطبع المفتاح الفارغ. العميل يُطلب منه قبول شروط غير مسمّاة قبل الدفع — عيب ثقة وقانوني على الشاشة الأهم. | artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-checkout.png; artifacts/real-device/coverage-20261030/local-chromium-390/client-checkout.png; artifacts/ux-audit-F4/evidence/client-checkout-terms-missing-ar-phone.png; apps/client/src/app/[locale]/(app)/checkout/[orderId]/checkout-form.tsx:39-47,141-154; packages/i18n/src/messages/ar.json:673 | لا تُعرض خانة القبول إلا عند وجود المستندات الثلاثة؛ وإلا يُمنع الدفع برسالة واضحة. وفي الصياغة اجعل الروابط إلزامية (fallback إلى عنوان المستند الثابت من LEGAL.documents) بدل مفتاح فارغ، وأضف اختبار gate يفشل عندما تكون الجملة بلا روابط. | M | ui-ux-pro-max §8 error-prevention / input-labels؛ fpt-brand (لا نص قانوني مخترع أو ناقص) | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/checkout-form.tsx`، `apps/client/src/app/[locale]/(app)/checkout/[orderId]/page.tsx`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| A11Y-007 | P2 ثانوي | وصول | Checkbox#terms + <p role="alert"> termsRequired | خطأ «يجب قبول المستندات» غير مرتبط بصندوق الاختيار ولا ينقل التركيز | بعد الضغط على الدفع دون قبول: alerts=[«يجب قبول المستندات للمتابعة.»]، cbInvalid=null، cbDescribed=null، focused=BODY (هاتف وسطح مكتب). بالمقابل خطأ الكوبون مربوط صحيحاً (aria-invalid=true, aria-describedby=f-…-error). | .e2e/a11y-client.log phone/desktop:checkout:termsError و checkout:couponError; apps/client/src/app/[locale]/(app)/checkout/[orderId]/checkout-form.tsx:131-146 | أعطِ الفقرة id واضبط على Checkbox aria-invalid و aria-describedby عند الخطأ، وانقل التركيز إلى الصندوق (ref.focus()) بعد عودة payState. | S | WIG Forms «focus first error on submit»; WCAG 3.3.1 | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/checkout-form.tsx`، `packages/ui/src/components/checkbox.tsx` |
| VCL-078 | P3 صقل | بصري | زر «تطبيق» الكوبون | زر «تطبيق» بحجم زر أساسي (56 px) لفعل ثانوي اختياري | حقل الكوبون الاختياري يحمل زراً بارتفاع وحدّ كزر رئيسي؛ ينافس «ادفع». | artifacts/real-device/coverage-20261030/local-chromium-390/client-checkout.png; artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-checkout.png | زر ghost/sm داخل الحقل (end adornment) أو رابط «لديك كوبون؟» يفتح الحقل. | S | ui-ux-pro-max §4 primary-action (secondary subordinate)؛ progressive disclosure | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/checkout-form.tsx` |

### العميل — checkout-transfer (iPhone 15 Pro Max)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-035 | P3 صقل | وصول | input[type=date] .fpt-input (134×31 على Safari) | حقل التاريخ في نموذج التحويل اليدوي قيس 134×31 على iPhone حقيقي — أُصلح في CSS ولم يُعَد قياسه على الجهاز | إيصال الجهاز: smallTargets=[{input .fpt-input--num 134×31}]. RD1 أضاف appearance:none; min-height:48px لحقول date/time؛ يحتاج إعادة قياس على الجهاز. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/coverage-receipt.json client/checkout-transfer@ar; packages/ui/src/components.css:189-190 | أعد تشغيل تغطية الجهاز الحقيقي لشاشة checkout-transfer وأغلق البند عند ≥ 44 px. | S | ui-ux-pro-max `touch-target-size` | `packages/ui/src/components.css` |

### العميل — checkout-transfer 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-021 | P3 صقل | نص | عنوان الرأس | عنوان الرأس يبقى «إتمام الشراء» على شاشات التحويل والانتظار | ثلاث خطوات مختلفة (الدفع، التحويل، بانتظار المراجعة) تحمل العنوان نفسه؛ المؤشر الوحيد للخطوة هو الشريط العلوي بلا تسميات على الهاتف. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-checkout-transfer.png; artifacts/real-device/ios-f3/transfer-waiting-iphone-15-pro-max.png | عناوين مخصصة: «تفاصيل التحويل» و«بانتظار التحقق من الإثبات». | S | Nielsen #1 visibility of status؛ ui-ux-pro-max §9 back-behavior | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/page.tsx`، `apps/client/src/app/[locale]/(app)/orders/[id]/page.tsx` |

### العميل — checkout-transfer

اللقطات: `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-checkout-transfer.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-checkout-transfer.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-checkout-transfer.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-002 | P0 حاجب | نص | رقم حساب FPT في فايبر | قيمة عيّنة «FPT-0000-0000 (عيّنة — يحدده المالك)» معروضة للعميل في الإنتاج مع زر نسخ | على iPhone (الإنتاج) تظهر تفاصيل التحويل برقم حساب وهمي مكتوب فيه «عيّنة — يحدده المالك» وبجانبه زر نسخ؛ العميل قد يحوّل مالاً فعلياً إلى رقم غير حقيقي، والنص يكشف أن المنتج غير مكتمل. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-checkout-transfer.png; artifacts/real-device/coverage-20261030/local-chromium-390/client-checkout-transfer.png; packages/db/src/settings.ts:36,50,63 | امنع تفعيل مسار التحويل اليدوي حتى يُدخل المالك البيانات الحقيقية (حالة «غير متاح مؤقتاً» في الصفحة والمتجر)، واحذف كلمة «عيّنة» من أي قيمة يمكن أن تصل إلى العميل. | S | critique-protocol Nielsen #5 error prevention؛ fpt-brand (لا بيانات مخترعة) | `packages/db/src/settings.ts`، `apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/page.tsx` |
| VCL-013 | P1 رئيسي | نص | حقل رفع الإثبات (input file) | زر رفع الملف أصلي بالإنجليزية «Choose File / no file selected» داخل واجهة عربية | على iPhone وChromium يظهر عنصر الملف الأصلي بنصوص المتصفح الإنجليزية داخل حقل يشبه الإدخال؛ لا يطابق لغة الصفحة ولا أزرار الهوية. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-checkout-transfer.png; artifacts/real-device/coverage-20261030/local-chromium-390/client-checkout-transfer.png | أخفِ الـinput واعرض زر هوية ثانوياً «اختر صورة الإيصال أو PDF» مع اسم الملف المختار تحته وحالة «لم يُختر ملف بعد» بالعربية. | S | ui-ux-pro-max §4 system-controls (customize when branding requires)؛ §8 input-labels | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/transfer-form.tsx` |
| VCL-017 | P2 ثانوي | RTL | تعليمات التحويل | أسهم الخطوات «→» تشير يميناً داخل نص عربي يُقرأ يساراً | «من تطبيق فايبر: تحويل → إلى حساب فايبر → أدخل رقم حساب FPT…» الأسهم تتجه ضد اتجاه القراءة، بينما صفحة «حذف حسابك» تستخدم «←» الصحيح. تناقض داخل المنتج. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-checkout-transfer.png; artifacts/real-device/coverage-20261030/local-chromium-390/client-legal-delete-account.png; packages/db/src/settings.ts | في النص العربي استخدم «←» أو استبدل الأسهم بقائمة مرقمة من أربع خطوات (أوضح على الهاتف). | S | fpt-brand (لا رموز يونيكود كأيقونات)؛ ui-ux-pro-max §6 truncation/typography؛ cognitive-principles Continuity | `packages/db/src/settings.ts`، `packages/i18n/src/messages/ar.json` |
| VCL-018 | P2 ثانوي | نص | نص مساعد الرفع | جملة «JPEG أو PNG أو WebP أو PDF، حتى 5 MB» مكررة مرتين متجاورتين | تظهر مرة بجانب عنوان «رفع إثبات التحويل» بأيقونة، ومرة تحت حقل الملف. التكرار يشوّش ويطيل الصفحة. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-checkout-transfer.png; artifacts/real-device/coverage-20261030/local-chromium-390/client-checkout-transfer.png | أبقِ النسخة تحت الحقل فقط (helper text) واحذف السطر بجانب العنوان. | S | ui-ux-pro-max §8 input-helper-text؛ critique-protocol minimalism (Nielsen #8) | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/page.tsx` |
| VCL-019 | P2 ثانوي | بصري | عنوان «رفع إثبات التحويل» | العنوان ينكسر على سطرين ويزاحمه النص المساعد في الصف نفسه على الهاتف | العنوان الكبير «رفع إثبات / التحويل» ينكسر لأن النص المساعد يقاسمه العرض؛ النتيجة رأس قسم غير متوازن. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-checkout-transfer.png; artifacts/real-device/coverage-20261030/local-chromium-390/client-checkout-transfer.png | ضع العنوان في سطر مستقل والمساعد تحته (أو احذفه حسب VCL-018). | S | ui-ux-pro-max §6 heading-line-balance؛ critique-protocol Typography | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/page.tsx` |
| VCL-020 | P3 صقل | بصري | حقل تاريخ التحويل | تاريخ أصلي بتنسيق «29. Oct 2026» بخط mono على iOS و«10/29/2026» على Chromium | التاريخ الأصلي يعرض شكلين مختلفين بين الأجهزة وكلاهما إنجليزي، بينما بقية التواريخ في التطبيق «29 أكتوبر 2026». | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-checkout-transfer.png; artifacts/real-device/coverage-20261030/local-chromium-390/client-checkout-transfer.png | اعرض القيمة المختارة بصيغة formatDate العربية فوق الحقل الأصلي، أو اجعل الافتراضي اليوم مع نص «اليوم، 29 أكتوبر 2026». | S | ui-ux-pro-max §10 number-formatting؛ §4 consistency | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/transfer-form.tsx` |

### العميل — evidence (ux-audit-F4)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-073 | P2 ثانوي | تقني | أداة الالتقاط | أدلة الحالات: الدفعة الأولى التُقطت بعرض 1280 لا 390، وبعد إعادة الالتقاط خمس لقطات عربية للدخول فارغة أو بلا CSS | الدفعة الأولى من *-phone.png كانت 2560 px (1280 × DPR 2) بتخطيط مكتبي (VIEWPORTS[vp] مُنشَر في newContext بدل viewport:{…}). أُعيد الالتقاط بـ 780 px وتم التحقق من كل النتائج أعلاه عليه؛ لكن client-signin-wrong/-focus-ar-phone وclient-signup-empty-ar-phone وclient-reset-submitted-ar-phone خرجت صوراً سوداء (6 KB) وclient-signin-empty-ar-phone خرجت بلا أنماط (HTML خام بخط النظام)، أي أن أول خمس لقطات في الدفعة التُقطت قبل جاهزية الخادم/الأصول. كما أن captures.json يحتوي 12 مدخلاً للإدارة فقط. | artifacts/ux-audit-F4/evidence/client-signin-wrong-ar-phone.png (780×1688، 6 KB أسود); artifacts/ux-audit-F4/evidence/client-signin-empty-ar-phone.png (بلا CSS); artifacts/ux-audit-F4/evidence/client-signup-empty-ar-phone.png; artifacts/ux-audit-F4/evidence/client-reset-submitted-ar-phone.png; scripts/ux-audit-capture.mjs:24-28; artifacts/ux-audit-F4/evidence/captures.json | انتظر networkidle + وجود الخط المحمَّل (document.fonts.ready) قبل أول لقطة، وارفض أي لقطة متوسط سطوعها 0 أو حجمها < 20 KB وأعدها؛ اجعل captures.json تراكمياً لا يُستبدل، وأعد التقاط الخمس العربية. | S | evidence integrity (verification-before-completion) | `scripts/ux-audit-capture.mjs` |

### العميل — help

اللقطات: `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-help.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-help.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-help.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-009 | P1 رئيسي | بصري | عناصر الأسئلة (details/summary) | iOS Safari يرسم مثلث الإفصاح الأصلي «◀» بجانب كل سؤال إضافةً إلى الشيفرون | على iPhone كل صف في مركز المساعدة يحمل مثلثاً أصلياً عند اليمين وشيفروناً «⌄» عند اليسار؛ على Chromium يظهر الشيفرون فقط. list-none وmarker:content-none لا يكفيان في WebKit الذي يحتاج summary::-webkit-details-marker{display:none}. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-help.png (مقارنة: artifacts/real-device/coverage-20261030/local-chromium-390/client-help.png); apps/client/src/app/[locale]/(public)/help/help-search.tsx:56-60; packages/ui/src/components.css:457 | أضف قاعدة عامة summary::-webkit-details-marker{display:none} في globals.css (أو استخدم Accordion من @fpt/ui بدل details الخام) وأعد التقاط الصفحة على iPhone. | S | ui-ux-pro-max §4 icon-style-consistent / system-controls | `apps/client/src/app/[locale]/(public)/help/help-search.tsx`، `apps/client/src/app/globals.css` |
| VCL-036 | P2 ثانوي | بصري | هيكل الصفحة | مركز المساعدة يُفتح من «المزيد» لكنه يفقد شريط التبويب وزر الرجوع | الصفحة عامة برأس مختلف (مبدّل اللغة + اسم الشركة) وبلا شريط سفلي؛ المستخدم المسجّل يخرج من قشرة التطبيق فجأة ولا طريق واضحاً للعودة سوى الزر الخلفي للمتصفح. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-help.png; artifacts/real-device/coverage-20261030/local-chromium-390/client-help.png; artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-more.png | عند وجود جلسة اعرض الصفحة داخل قشرة التطبيق (رأس «مركز المساعدة» بزر رجوع + التبويبات)، أو على الأقل زر «رجوع» ثابت. | M | ui-ux-pro-max §9 persistent-nav / navigation-consistency / back-behavior | `apps/client/src/app/[locale]/(public)/help/page.tsx`، `apps/client/src/app/[locale]/(public)/layout.tsx` |
| VCL-037 | P3 صقل | بصري | صف الشرائح (chips) | شرائح التصنيف تُقصّ عند الحافة اليسرى بلا تدرّج يدل على التمرير الأفقي | «الحسابات الممول…» مقطوعة عند الحافة؛ لا توجد إشارة إلى وجود عناصر أخرى سوى القطع نفسه. | artifacts/real-device/coverage-20161030/iphone-15-pro-max/client-help.png; artifacts/real-device/coverage-20261030/local-chromium-390/client-help.png | تدرّج شفاف عند الطرف (mask-image) مع هامش 16 px بعد آخر شريحة، أو التفاف الشرائح على سطرين. | S | ui-ux-pro-max §5 chip-collection-reflow؛ Gestalt Closure | `apps/client/src/app/[locale]/(public)/help/help-search.tsx` |
| VCL-038 | P3 صقل | نص | الترويسة | «مركز المساعدة» يتكرر كسطر علوي صغير وعنوان كبير تحته مباشرة | eyebrow والعنوان بالنص نفسه؛ أحدهما زائد. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-help.png | اجعل السطر العلوي «FPT · الدعم» أو احذفه. | S | Nielsen #8؛ critique-protocol Typography (labels distinct) | `apps/client/src/app/[locale]/(public)/help/page.tsx` |

### العميل — home 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-002 | P1 رئيسي | وصول | RuleSheet (Sheet بلا SheetTrigger) ← زر RuleMeter «الهدف — التفاصيل» | بعد إغلاق ورقة تفاصيل القاعدة بـ Escape يضيع التركيز إلى body ولا يعود إلى الزر الذي فتحها | الورقة مضبوطة (open/onOpenChange) بلا Trigger، فلا يعرف Radix عنصر الإرجاع. قياس: قبل الفتح التركيز على button «الهدف — التفاصيل»، داخل الورقة التركيز على «إغلاق»، بعد Escape activeElement = body (الهاتف وسطح المكتب). مستخدم لوحة المفاتيح يعود إلى بداية الصفحة ويُعيد التنقّل عبر الرأس والتبويبات. | .e2e/a11y-client.log «phone:account:ruleSheet» و «desktop:account:ruleSheet»: after.tag=body, dialogGone:true; apps/client/src/components/account-overview.tsx:228-268 | احفظ العنصر المفعِّل (document.activeElement) عند setOpen(key) وأعد التركيز إليه في onCloseAutoFocus لـ SheetContent، أو لفّ كل RuleMeter بـ SheetTrigger asChild. | S | accessibility.md § Focus Management (return focus on close); ui-ux-pro-max `escape-routes`, `focus-states` | `apps/client/src/components/account-overview.tsx`، `packages/ui/src/components/sheet.tsx` |

### العميل — home 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-043 | P3 صقل | وصول | svg role=img aria-label={summary} | مخطط الرصيد صورة واحدة بملخّص نصي — لا جدول بيانات بديل لقراءة النقاط | الرسم مُسمّى (جيد) والدليل مُخفى aria-hidden؛ لا يوجد جدول/قائمة بديلة للقيم اليومية (3–30 نقطة) ولا وصف للحدود (الهدف/الحدّ) خارج الملخّص. | packages/ui/src/components/equity-chart.tsx:122,169; artifacts/ux-audit-F4/evidence/client-home-largetext-ar-phone.png | أضف <details> «القيم كجدول» تحت المخطط (sr-only أو ظاهر) بالتواريخ والقيم والحدّين. | M | ui-ux-pro-max §10 Charts & Data; dataviz accessibility | `packages/ui/src/components/equity-chart.tsx` |

### العميل — home 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-027 | P2 ثانوي | بصري | صف عدّادات القواعد (RuleMeterRow) | ترتيب العدّادات معكوس بين الرئيسية وصفحة الحساب | على الرئيسية: الهدف يميناً ثم اليومية ثم الإجمالية (صحيح RTL)؛ على «نظرة» في صفحة الحساب: الإجمالية يميناً والهدف يساراً. العنصر نفسه بترتيبين. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-home.png; artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-account.png; apps/client/src/components/account-overview.tsx:151-169 | ترتيب DOM واحد (هدف → يومية → إجمالية) واترك الاتجاه للـdir؛ لا direction:ltr على الصف. | S | ui-ux-pro-max §4 consistency؛ cognitive-principles Similarity | `apps/client/src/components/account-overview.tsx`، `packages/ui/src/components/rule-meter.tsx` |
| VCL-028 | P3 صقل | بصري | شيفرون «التفاصيل» في العدّاد المدمج | شيفرون فتح التفاصيل بعيد عن عنوان العدّاد في الطرف المقابل | عنوان «اليومية» عند يمين العمود والشيفرون «⌄» عند أقصى يساره؛ لا يُقرأ كجزء من العنوان، ويغيب عن «الهدف». | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-home.png | الصق الشيفرون بالعنوان (gap 4 px) أو اجعل العمود كله زراً بحدّ خافت عند الضغط. | S | cognitive-principles Proximity؛ Common Rules Icon Alignment | `packages/ui/src/components/rule-meter.tsx` |

### العميل — home 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-016 | P2 ثانوي | هوية | شارة «حساب ممول» الحمراء | شارة ممتلئة بالأحمر تنافس الزر الأساسي الأحمر على الشاشة نفسها | على رئيسية الحساب الممول يوجد عنصران أحمران ممتلئان: الشارة «حساب ممول» وزر «اطلب دفعة»، إضافةً إلى الشعار وحالة التبويب. StatusBadge يخصّص اللون البراند للممول عمداً، لكن النتيجة تضعف أولوية الزر الأساسي الواحد. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-home.png; artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-home-en.png; artifacts/ux-audit-F4/evidence/client-credentials-funded-ar-phone.png; packages/ui/src/components/status-badge.tsx:7,25-26 | اجعل شارة الممول outline بحدّ أحمر رفيع ونص أبيض (أو لون gain)، أو أخفِها على الرئيسية حيث يوجد زر أساسي أحمر (showBadge=false). | S | fpt-brand (الأحمر: الشعار + الفعل الأساسي + الحالة النشطة)؛ ui-ux-pro-max §4 primary-action؛ Von Restorff | `packages/ui/src/components/status-badge.tsx`، `apps/client/src/components/account-overview.tsx` |

### العميل — home 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-046 | P3 صقل | بصري | مؤشرات غير المقروء | نقطتان حمراوان لغير المقروء في آن واحد: على جرس الرأس وعلى تبويب «المزيد» | الرئيسية تعرض نقطة على الجرس ونقطة أخرى على أيقونة «المزيد» في الشريط السفلي للشيء نفسه. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-home.png; artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-home-en.png | مؤشر واحد (الجرس) ويبقى تبويب «المزيد» نظيفاً، أو العكس. | S | ui-ux-pro-max §9 tab-badge (sparingly) | `apps/client/src/components/bottom-nav.tsx`، `apps/client/src/components/client-header.tsx` |

### العميل — home, account, account-trades, account-rules, account-credentials, trades, account-funded, shop-product, checkout, payouts, payout-new, payout-channel, payout-details, payout-review, payout-detail, notifications, profile, more, orders, order, partners, account-breached, home-mixed

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-003 | P2 ثانوي | وصول | ClientHeader center → <span data-testid="screen-title"> | 23 شاشة عميل بلا h1: عنوان الشاشة في الرأس هو span لا عنوان | axe page-has-heading-one (moderate) على 23 شاشة عميل × 4 مشاريع = 92 عقدة. القياس المباشر: home → hs=[«h2: الحسابات الأخرى1»]، account → [«h2: الصفقات المفتوحة1»]، payout-new/checkout → hs=[]، notifications → [«h3: لا إشعارات»]. قارئ الشاشة لا يجد عنوان الصفحة بالقفز H، والقفز بـ 1 لا يعمل. | artifacts/ux-gates/gates-*.json axe.page-has-heading-one targets html; .e2e/a11y-client.log *:outline; apps/client/src/components/client-header.tsx:40 | اجعل عنوان الرأس <h1 className="…"> (نفس الحجم البصري 16 px) أو أضف h1 بصرياً مخفياً (sr-only) يحمل عنوان الشاشة في الرأس، واترك عناوين الأقسام h2. | S | ui-ux-pro-max `heading-hierarchy`; WIG «Headings hierarchical h1–h6» | `apps/client/src/components/client-header.tsx`، `packages/ui/src/components/app-shell.tsx` |

### العميل — home, account

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-032 | P3 صقل | وصول | AccountHeroCard <section aria-label="حقوق الحساب 26,300.00 USD"> | اسم معلم المنطقة يحمل قيمة حيّة متغيّرة | المنطقة مسمّاة بالمبلغ الحالي فتتغيّر أسماء المعالم مع كل تحديث؛ الأفضل اسم ثابت («بطاقة الحساب») والقيمة في المحتوى. | .e2e/a11y-client.log desktop:home:outline lm section[حقوق الحساب 26,300.00 USD]; packages/ui/src/components/account-hero.tsx:1154 | aria-label={labels.equity} فقط، والمبلغ نص داخل المنطقة. | S | accessibility.md § Landmark Regions | `packages/ui/src/components/account-hero.tsx` |

### العميل — home

اللقطات: `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-home.png`، `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-home-en.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-home.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-home-en.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-home.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-home-en.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-012 | P1 رئيسي | بصري | الفراغ بين صف «الحساب النشط» وبطاقة حقوق الحساب | فجوة فارغة نحو 150 px فوق بطاقة الحقوق على الرئيسية في كل الأجهزة | بين السطر «الحساب النشط · FPT-100K-5L78C» وتسمية «حقوق الحساب» مساحة سوداء كبيرة بلا محتوى (iPhone وSamsung وChromium وسطح المكتب). تكسر إيقاع الصفحة وتدفع الرقم البطل والزر الأساسي إلى أسفل أول شاشة. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-home.png; artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-home.png; artifacts/real-device/coverage-20261030/local-chromium-390/client-home.png; artifacts/ux-audit-F4/evidence/client-home-demo08-ar-desktop.png; apps/client/src/app/[locale]/(app)/accounts/page.tsx:73-87; apps/client/src/components/account-overview.tsx:106-131 | افحص ما يشغل الحيّز (حاوية تنبيهات فارغة بـ gap، أو Skeleton مخفي بارتفاع) واحذف الحاوية عند فراغها؛ اجعل المسافة بين الرأس والبطاقة 16–24 px. | S | critique-protocol Spacing (consistent gaps) / Layout rhythm؛ mobile-app-ui-design 8-pt grid | `apps/client/src/components/account-overview.tsx`، `apps/client/src/app/[locale]/(app)/accounts/page.tsx` |
| DEV-002 | P3 صقل | تقني | الاستطلاع الحيّ /api/accounts/<id>/live عند مغادرة الصفحة | رفض وعد غير معالج «Load failed» عند التنقّل السريع على سفاري iOS | على الآيفون الحقيقي يُسجَّل في console «{message: Load failed}» عند الانتقال من الرئيسية (الإنجليزية) إلى شاشة أخرى بينما طلب الاستطلاع الحيّ قيد الإرسال؛ وعلى Chromium بعد تسجيل الخروج يستمر الاستطلاع ويتلقى 401. لا أثر وظيفياً، لكنه ضجيج في console ويدل على أن الاستطلاع لا يُلغى عند الخروج/الإلغاء. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/coverage-receipt.json → screens['client/home@en'].detail.consoleErrors؛ artifacts/real-device/coverage-20261030/local-chromium-390/coverage-receipt.json → failedRequests 401 على /api/accounts/*/live بعد تبديل المستخدم | إلغاء الاستطلاع بـ AbortController في تنظيف useEffect وتجاهل AbortError، وإيقافه عند 401 بدل اعتباره «غير متصل» إلى الأبد (انظر TEC-042). | S | ui-ux-pro-max §3 debounce-throttle / network-fallback · web-interface-guidelines § Performance | `apps/client/src/components/account-overview.tsx` |
| VCL-060 | P3 صقل | بصري | زر «فتح الحساب» المكرر | «فتح الحساب» يظهر مرتين على الرئيسية (زر كبير تحت البطل وزر ghost تحت المخطط) | في حساب التقييم يظهر زر ثانوي كامل العرض ثم بعد المخطط زر «فتح الحساب» آخر، إضافة إلى رابط المرجع في الرأس: ثلاثة مداخل للوجهة نفسها. | artifacts/ux-audit-F4/evidence/client-home-demo08-ar-phone.png; artifacts/ux-audit-F4/evidence/client-home-demo08-ar-desktop.png; apps/client/src/app/[locale]/(app)/accounts/page.tsx:67-71,88-92 | مدخل واحد واضح (الزر تحت البطل) واحذف الباقي. | S | Hick's law؛ Nielsen #8 | `apps/client/src/app/[locale]/(app)/accounts/page.tsx` |
| VCL-061 | P3 صقل | بصري | قائمة «الحسابات الأخرى» | قيمة الحساب الآخر «49,863.00» بلا رمز عملة وبلا تسمية | الصف يعرض رقماً عارياً عند اليسار بجوار الشيفرون؛ لا يُعرف أهو الحقوق أم الرصيد ولا عملته (الأسطر الأخرى تحمل USD). | artifacts/ux-audit-F4/evidence/client-home-demo08-ar-phone.png; apps/client/src/app/[locale]/(app)/accounts/page.tsx:110 | «49,863.00 USD» مع تسمية صغيرة «الحقوق» (codePosition after). | S | fpt-brand (المبلغ وحدة برمز العملة)؛ mobile-app-ui-design values vs labels | `apps/client/src/app/[locale]/(app)/accounts/page.tsx` |

### العميل — home-en 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-039 | P2 ثانوي | بصري | سطر «Balance USD 115,000.00 · Live» | في الإنجليزية يسبق رمز العملة الرقم في سطر الرصيد بينما يليه في بطاقة الحقوق والعدّادات على الشاشة نفسها | «Balance USD 115,000.00» ثم مباشرة «115,000.00 USD» في البطل و«5,750.00 USD» في العدّادات؛ موضعان للرمز على شاشة واحدة. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-home-en.png; artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-account-en.png; packages/ui/src/lib/format.ts:64-67 | قرار واحد: الرمز بعد الرقم في اللغتين (codePosition 'after' افتراضياً) أو عرض الرصيد عبر Money بنفس خيارات البطل. | S | ui-ux-pro-max §4 consistency؛ §10 number-formatting؛ fpt-brand (الرمز بعد الأرقام) | `packages/ui/src/lib/format.ts`، `apps/client/src/components/account-overview.tsx` |

### العميل — home-largetext

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-075 | P3 صقل | بصري | التخطيط عند جذر 22 px | التخطيط يصمد عند النص الكبير — لا قصّ ولا تداخل (ملاحظة إيجابية مع تحفظ) | عند 22 px جذراً على 390 يبقى البطل والعدّادات الثلاثة والمخطط والقائمة سليمة بلا تجاوز أفقي ولا قصّ؛ التحفظ الوحيد أن تسمية المخطط «11.0k» الخضراء تكبر وتلامس المحور، ومفتاح المخطط يلتف على سطرين (VCL-059). | artifacts/ux-audit-F4/evidence/client-home-largetext-ar-phone.png (780×2666، مؤكَّد بعد إعادة الالتقاط); artifacts/ux-audit-F4/evidence/client-home-reducedmotion-ar-phone.png | لا إجراء سوى clamp لخط تسميات المخطط (max 14 px). | S | ui-ux-pro-max §1 dynamic-type / reduced-motion | `packages/ui/src/components/equity-chart.tsx` |

### العميل — landing, signin, signup, reset, help

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-016 | P3 صقل | وصول | Logo lockup <img alt="Fiper Pro Traders"> + bdi wordmark | نص بديل مكرّر بجانب اسم العلامة المكتوب (axe image-redundant-alt) | axe image-redundant-alt (minor) على 5 شاشات في 430 و 1440: الصورة alt «Fiper Pro Traders» والنص نفسه ظاهر بجوارها فيُقرأ مرّتين. | gates-phone-430.json / gates-desktop-1440.json image-redundant-alt targets img[alt="Fiper Pro Traders"], .ui-tile; packages/ui/src/components/logo.tsx:49 | حين يُعرض wordmark نصياً اجعل alt="" للمربّع (زخرفي) واحتفظ بالاسم في النص. | S | ui-ux-pro-max `alt-text`, `icon-context` | `packages/ui/src/components/logo.tsx` |

### العميل — landing

اللقطات: `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-landing.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-landing.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-landing.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-008 | P1 رئيسي | نص | شارة «Backed by Fiper Global…» | سطر إنجليزي كامل داخل الصفحة العربية العامة | تحت زرّي الصفحة الرئيسية العامة يظهر «Backed by Fiper Global — established financial infrastructure with proven broker experience» بالإنجليزية لزائر عربي؛ مفتاح ar.json يحمل النص الإنجليزي نفسه. يبدو كنص لم يُترجم، ويضعف الثقة في أول شاشة. | artifacts/real-device/coverage-20261030/local-chromium-390/client-landing.png; artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-landing.png; packages/i18n/src/messages/ar.json:385; apps/client/src/app/[locale]/(public)/page.tsx:42 | إن كان النص جملة تسويقية معتمدة فترجمها («بدعم من Fiper Global — بنية مالية راسخة…») مع إبقاء الاسم لاتينياً؛ وإن كان ادعاءً تنظيمياً غير موثّق في brand/regulatory-matrix.md فاحذفه. | S | fpt-brand (الاسم لاتيني داخل العربية؛ لا ادعاءات غير موثقة)؛ ui-ux-pro-max §4 consistency | `packages/i18n/src/messages/ar.json`، `apps/client/src/app/[locale]/(public)/page.tsx`، `brand/regulatory-matrix.md` |
| VCL-062 | P3 صقل | بصري | قائمة الأحجام | زر «اختر ›» نص عريض عند الطرف الأيسر بلا هيئة زر وبشيفرون معكوس | كل صف حجم يحمل «اختر» بخط 600 وشيفروناً يميناً (VCL-003)؛ بلا حدّ أو خلفية فلا يُدرك كمساحة نقر. | artifacts/real-device/coverage-20261030/local-chromium-390/client-landing.png | اجعل الصف كله رابطاً (ListRow) أو زر secondary صغيراً. | S | ui-ux-pro-max §2 touch-target-size؛ Fitts | `apps/client/src/app/[locale]/(public)/page.tsx` |

### العميل — legal 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-001 | P0 حاجب | هوية | packages/db/scripts/seed-data/legal.ts (LEGAL_DRAFTS terms/privacy/risk/refund) · shop.checkout.terms · legal.draft | نصوص قانونية مؤلَّفة داخلياً تُعرض للعميل ويُلزَم بقبولها عند الدفع | ملف البذر يولّد أربع وثائق كاملة (شروط الخدمة، سياسة الخصوصية، الإفصاح عن المخاطر، سياسة عدم الاسترداد) بعنوان «مسودة» ثم يعرضها تطبيق العميل ويطلب منه قبولها في سطر الدفع «أقبل {terms} و{risk} و{refund} ({version}، سارية من {date})». مصفوفة النصوص التنظيمية تنصّ على أن النص القانوني «يُستخدم حرفياً من مصدر معتمد أو لا يُستخدم»، وأن نص اتفاقية المتداول «غير موجود في أي مدخل» (القرار المطلوب 7). كما يرى العميل لافتة «مسودة — بانتظار اعتماد المؤسس؛ ليست نصاً قانونياً نهائياً.» فوق وثيقة يُطلب منه قبولها كعقد. | packages/db/scripts/seed-data/legal.ts:1-5 («Dated DRAFT legal texts … Awaiting the founder's and counsel's sign-off»); ar.json:L673 / en.json:L673; ar.json:L1296 / en.json:L1296; brand/regulatory-matrix.md § 3 (7) و§ 4 | لا يُنشر أي نص قانوني قبل وروده حرفياً من الإدارة/الشؤون القانونية. إلى حينه: إمّا تعطيل بوابة القبول (لا شراء بلا وثائق معتمدة) أو عرض الوثائق المعتمدة من الشركة الأم بإقرار صريح بأنها تحكم FPT. تُزال لافتة «مسودة» من واجهة العميل فور الاعتماد. | L | brand regulatory-matrix § 4 / glossary § 7 (PLACEHOLDER NEVER SHIP) | `packages/db/scripts/seed-data/legal.ts`، `config/legal.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### العميل — legal-terms

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-011 | P1 رئيسي | نص | تنبيه المسودة | تنبيه «مسودة — بانتظار اعتماد المؤسس؛ ليست نصاً قانونياً نهائياً» معروض للعميل فوق الشروط التي يقبلها عند الدفع | صفحة الشروط التي يُحال إليها من الدفع تفتتح بتنبيه أصفر يقول إن النص ليس قانونياً نهائياً، والعنوان المسجَّل «يُدرج لاحقاً». المستخدم يُطلب منه قبول مسودة. | artifacts/ux-audit-F4/evidence/client-legal-terms-ar-phone.png; artifacts/ux-audit-F4/evidence/client-legal-terms-ar-desktop.png; apps/client/src/app/[locale]/(public)/legal/[key]/page.tsx | إمّا اعتماد النص قبل الإطلاق أو إخفاء التنبيه عن العملاء (يبقى في الإدارة فقط)؛ ولا يُفعَّل الدفع على مستند بحالة مسودة. | S | fpt-brand regulatory-matrix (لا نص قانوني غير معتمد)؛ Nielsen #1 visibility of status | `apps/client/src/app/[locale]/(public)/legal/[key]/page.tsx`، `packages/db/src/legal.ts` |
| VCL-072 | P3 صقل | بصري | روابط المستندات في التذييل | تذييل الصفحة القانونية خمسة روابط في سطر 13 px بلا تباعد لمسي | «شروط الخدمة · سياسة الخصوصية · الإفصاح · عدم الاسترداد · حذف حسابك» متلاصقة بخط صغير في سطر واحد. | artifacts/ux-audit-F4/evidence/client-legal-terms-ar-phone.png | قائمة ListRow عمودية أو شرائح بارتفاع 44 px. | S | ui-ux-pro-max §2 touch-spacing / touch-target-size | `apps/client/src/app/[locale]/(public)/legal/[key]/page.tsx` |
| VCL-080 | P3 صقل | RTL | عنوان المستند داخل النص | التاريخ «2026-10-29» ينكسر في منتصفه بين سطرين في عنوان المستند على 390 | السطر «شروط الخدمة — Fiper Pro Traders (مسودة، 2026-10-29)» يلتف فيظهر «-2026» في نهاية سطر و«10-29)» في بداية التالي؛ التاريخ ISO داخل نص عربي بلا عزل. | artifacts/ux-audit-F4/evidence/client-legal-terms-ar-phone.png (780 px، أعلى الصفحة) | استخدم formatDate العربية «29 أكتوبر 2026» (لا ISO في الواجهة) أو لفّ التاريخ بـ num-ui. | S | fpt-brand (لا ISO timestamps في الواجهة)؛ ui-ux-pro-max §6 long-token-wrapping | `packages/db/src/legal.ts`، `apps/client/src/app/[locale]/(public)/legal/[key]/page.tsx` |

### العميل — more 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-003 | P1 رئيسي | RTL | سهم الصف (ListRow chevron) | شيفرون الصفوف يشير إلى اليمين في الواجهة العربية (انعكاس مزدوج) | في «المزيد» وقائمة الأحجام على الصفحة الرئيسية العامة و«الحسابات الأخرى» و«المستندات» في الملف، كل شيفرون يظهر «›» متجهاً يميناً عند الطرف الأيسر، بينما اتجاه التقدّم في RTL يساراً. السبب أن ListRow يحمل الصنفين معاً: .fpt-icon--dir (ينعكس عبر brand CSS تحت [dir=rtl]) و rtl:-scale-x-100 (Tailwind) فيُلغي أحدهما الآخر. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-more.png; artifacts/real-device/coverage-20261030/local-chromium-390/client-more.png; artifacts/real-device/coverage-20261030/local-chromium-390/client-landing.png; artifacts/ux-audit-F4/evidence/client-profile-ar-phone.png; packages/ui/src/components/list-row.tsx:43; brand/components/components.css:37 | احذف rtl:-scale-x-100 من ListRow وأبقِ .fpt-icon--dir وحده (كما في BackLink)، وأضف فحصاً في verify-identity يرفض اجتماع الصنفين على عنصر واحد. | S | ui-ux-pro-max Common Rules Icons — Icon Alignment / Contextual Semantics؛ fpt-brand (الأيقونات الاتجاهية تنعكس في RTL) | `packages/ui/src/components/list-row.tsx` |

### العميل — more

اللقطات: `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-more.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-more.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-more.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-045 | P3 صقل | بصري | رأس الصفحة | صفحة «المزيد» تبدأ ببريد mono رمادي بلا اسم المستخدم ولا هوية | أول عنصر «demo01@fpt.example» بخط أحادي باهت، ثم قوائم بصفوف متباعدة 75 px بلا فواصل؛ الصفحة تبدو قائمة روابط لا مركز حساب. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-more.png | صف هوية: الاسم بخط 600 + البريد تحته + شارة التحقق، ثم مجموعات داخل fpt-list بفواصل. | S | mobile-app-ui-design (user representation: name > initials)؛ Gestalt Common Region | `apps/client/src/app/[locale]/(app)/more/page.tsx` |

### العميل — notifications

اللقطات: `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-notifications.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-notifications.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-notifications.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-014 | P1 رئيسي | بصري | عناصر القائمة | لا تمييز بصري بين الإشعار المقروء وغير المقروء رغم عدّاد «7» | صفحة «المزيد» تقول 7 إشعارات غير مقروءة وزر «تعليم الكل كمقروء» ظاهر، لكن كل العناصر في القائمة بالوزن واللون نفسه بلا نقطة أو خلفية؛ المستخدم لا يعرف أيها جديد. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-notifications.png; artifacts/real-device/coverage-20261030/local-chromium-390/client-notifications.png; apps/client/src/components/notifications-list.tsx | نقطة حمراء صغيرة بجانب العنوان ووزن 600 للعنوان غير المقروء، وتخفيف المقروء إلى text-ink-2؛ وإزالة الزر عندما لا يوجد غير مقروء (موجود في الكود — تأكد من unreadLabel). | S | ui-ux-pro-max §4 state-clarity؛ Nielsen #1 | `apps/client/src/components/notifications-list.tsx` |
| VCL-015 | P2 ثانوي | بصري | رابط «فتح» | رابط «فتح» نص عريض يتيم أسفل كل إشعار بمحاذاة غير منتظمة | يظهر «فتح» بخط أعرض من العنوان وفي موضع مختلف أفقياً عن سطر الوقت، فيبدو عنصراً ضائعاً؛ الصف كله يجب أن يكون هو الهدف. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-notifications.png | اجعل الإشعار كله ListRow قابلاً للنقر بشيفرون، واحذف «فتح» أو اجعله زر ghost صغيراً بمحاذاة الوقت. | S | cognitive-principles Fitts؛ critique-protocol Hierarchy | `apps/client/src/components/notifications-list.tsx` |

### العميل — offline-home 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-007 | P1 رئيسي | تقني | الصفحة دون اتصال (PWA) | دون اتصال تظهر صفحة بيضاء فارغة بلا علامة ولا رسالة | التقاطا offline-home وoffline-navigation صفحتان بيضاوان تماماً. عامل الخدمة يعيد Response.error() عندما لا توجد نسخة مخبأة، فيعرض المتصفح صفحته الخام؛ التطبيق داكن وبهوية، والصفحة البيضاء انقطاع صارخ عن كل ما سبقها. | artifacts/ux-audit-F4/evidence/client-offline-home-ar-phone.png; artifacts/ux-audit-F4/evidence/client-offline-navigation-ar-phone.png; apps/client/public/sw.js:37-45 | خبّئ صفحة /offline.html داكنة ببسيطة (شعار، «لا اتصال»، زر «إعادة المحاولة») عند تثبيت SW وأعدها كـ fallback للتنقّل؛ ترجمها حسب لغة آخر صفحة. | M | ui-ux-pro-max §3 offline-support / network-fallback | `apps/client/public/sw.js`، `apps/client/src/components/sw-register.tsx` |

### العميل — order-receipt

اللقطات: `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-order-receipt.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-order-receipt.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-order-receipt.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-049 | P3 صقل | بصري | صف «صادر / إلى» | نقطة فاصلة يتيمة قبل الاسم: «· Khalid Al-Mansoori» | في الإيصال الأبيض يظهر الفاصل «·» قبل الاسم في بداية القيمة لأن العنصر الأول من السلسلة فارغ. | artifacts/real-device/coverage-20261030/local-chromium-390/client-order-receipt.png; artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-order-receipt.png | اجمع الأجزاء غير الفارغة ثم join(' · '). | S | critique-protocol craft (would I put my name on it) | `apps/client/src/app/[locale]/(app)/orders/[id]/receipt/page.tsx` |

### العميل — orders 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-024 | P2 ثانوي | نص | اسم المنتج | «تقييم FPT — 10,000 دولار · 10,000 USD» يكرر المبلغ بكلمة «دولار» ثم بالرمز | اسم المنتج يحتوي «دولار» بالعربية ثم الحجم يُضاف ثانية بـ USD؛ تكرار، وكلمة «دولار» تخالف قاعدة «المبلغ مع رمز العملة». | artifacts/ux-audit-F4/evidence/client-orders-empty-ar-phone.png; artifacts/real-device/coverage-20261030/local-chromium-390/client-order-receipt.png | اسم المنتج «تقييم FPT» فقط، والحجم مرة واحدة «10,000 USD» كوحدة لا تنكسر. | S | fpt-brand (المبالغ وحدات برمز العملة)؛ Nielsen #8 | `packages/db/src/catalog.ts`، `packages/i18n/src/messages/ar.json` |

### العميل — orders

اللقطات: `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-orders.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-orders.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-orders.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-desktop.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-en-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-en-desktop.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-076 | P3 صقل | بصري | قيمة الصف | مبلغ الطلب «90.00 USD» في أقصى اليسار بينما الشارة والتاريخ في اليمين — الصف طويل بثلاث كتل | على سطح المكتب (وبالتالي الأجهزة العريضة) يفصل 600 px بين عنوان الطلب والمبلغ؛ على الهاتف مقبول. | artifacts/ux-audit-F4/evidence/client-orders-empty-ar-phone.png | قيّد عرض القائمة (max-w-2xl) كما في بقية الصفحات. | S | ui-ux-pro-max §5 container-width / line-length-control | `apps/client/src/app/[locale]/(app)/orders/page.tsx` |

### العميل — partners

اللقطات: `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-partners.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-partners.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-partners.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-066 | P3 صقل | بصري | صف «الفئة» | «15 % 15 % — من 1 إلى 9 مبيعات» النسبة مكررة في الصف نفسه | القيمة الكبيرة «15 %» يتبعها نص «15 % — من 1 إلى 9 مبيعات في آخر 30 يوماً». | artifacts/real-device/coverage-20261030/local-chromium-390/client-partners.png | القيمة «15 %» ثم الوصف «من 1 إلى 9 مبيعات في آخر 30 يوماً» فقط. | S | Nielsen #8 | `apps/client/src/app/[locale]/(app)/partners/page.tsx` |
| VCL-067 | P3 صقل | بصري | رابط الإحالة | الرابط ينكسر بـ break-all في منتصف كلمة «sign/up» | «https://fptraders.app/ar/sign» ثم «up?ref=FPT-SARA2» في سطر جديد؛ word-break:break-all يقطع عشوائياً. | artifacts/real-device/coverage-20261030/local-chromium-390/client-partners.png; apps/client/src/app/[locale]/(app)/partners/page.tsx:127 | overflow-wrap:anywhere بدل break-all، أو اعرض الرابط مختصراً «fptraders.app/…/FPT-SARA2» مع زر النسخ. | S | ui-ux-pro-max §6 long-token-wrapping | `apps/client/src/app/[locale]/(app)/partners/page.tsx` |

### العميل — payout-amount (keyboard open)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-043 | P2 ثانوي | بصري | شريط التبويب فوق لوحة المفاتيح | عند فتح لوحة المفاتيح يبقى شريط التبويب ثابتاً ويحجب معاينة «تتسلم 400.00 USD» | لقطة Samsung الذهبية: المساحة المرئية تنكمش إلى الحقل والتلميح، والشريط السفلي يطفو فوق نتيجة الحساب فلا يرى المستخدم ما سيتسلمه أثناء الكتابة. | artifacts/real-device/android/payout-amount-samsung-galaxy-s23.png | أخفِ شريط التبويب أثناء التركيز على حقل (visualViewport) أو انقل معاينة المبلغ فوق الحقل مباشرة. | M | ui-ux-pro-max §1 focus-not-obscured؛ Common Rules Scroll and fixed element coexistence | `apps/client/src/components/bottom-nav.tsx`، `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx` |

### العميل — payout-new (amount)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-005 | P1 رئيسي | بصري | حقل المبلغ مع لاحقة USD | الأرقام المكتوبة تتراكب فوق لاحقة «USD» داخل حقل المبلغ | في كل التقاط للحقل بقيمة (0، -50، 9999999، 12.345) وفي لقطة Samsung الذهبية (500) تظهر الأرقام مطبوعة فوق حروف USD الباهتة. الحقل أحادي الاتجاه (mono LTR) فيبدأ النص من اليسار، واللاحقة end موضوعة عند inline-end الذي هو اليسار في RTL، مع padding-inline-end لا يحمي بداية النص. | artifacts/ux-audit-F4/evidence/client-payout-amount-zero-ar-phone.png; artifacts/ux-audit-F4/evidence/client-payout-amount-negative-ar-phone.png; artifacts/ux-audit-F4/evidence/client-payout-amount-toolarge-ar-phone.png; artifacts/ux-audit-F4/evidence/client-payout-amount-decimals-ar-phone.png; artifacts/real-device/android/payout-amount-samsung-galaxy-s23.png; apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx:90; packages/ui/src/components.css:186 | اجعل الحقل dir="ltr" صراحةً وضع اللاحقة عند الطرف الأيمن فيزيائياً (أو استخدم start adornment في RTL)، وطبّق padding على الجهة التي تحمل اللاحقة فعلاً. أضف لقطة gate للحقل بقيمة مكتوبة. | S | ui-ux-pro-max §8 input-helper-text / touch-friendly-input؛ §6 number-tabular | `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx`، `packages/ui/src/components/input.tsx`، `packages/ui/src/components.css` |
| VCL-006 | P1 رئيسي | وصول | التحقق من المبلغ | لا رسالة خطأ لقيمة صفر أو سالبة أو أكبر من الحدّ: زر «متابعة» يُعطَّل صامتاً والمعاينة تعرض «تتسلم 7,999,999.20 USD» | بإدخال 9999999 تُحسب المعاينة بلا اعتراض رغم أن الحدّ الأقصى المعروض فوقها 12,500.00 USD؛ وبإدخال 0 أو -50 تبقى المعاينة «0.00 USD». زر «متابعة» يصبح رمادياً (معطَّلاً) لكن لا يوجد أي نص يشرح السبب قرب الحقل — المستخدم يرى معاينة بملايين وزراً لا يعمل بلا تفسير (تم التحقق على 390×844). | artifacts/ux-audit-F4/evidence/client-payout-amount-toolarge-ar-phone.png (780 px); artifacts/ux-audit-F4/evidence/client-payout-amount-zero-ar-phone.png; artifacts/ux-audit-F4/evidence/client-payout-amount-negative-ar-phone.png; apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx | تحقّق فوري عند blur: «الحدّ الأقصى لهذا الطلب 12,500.00 USD» / «أدخل مبلغاً أكبر من صفر» تحت الحقل بـ aria-describedby ولون الخطر، وحاصر المعاينة بالحدّ (لا تعرض 7,999,999.20)، وأبقِ الزر معطَّلاً مع السبب ظاهراً. | M | ui-ux-pro-max §8 inline-validation / error-clarity / error-placement؛ Nielsen #5 | `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx`، `packages/i18n/src/messages/ar.json` |

### العميل — payout-new (المبلغ → القناة → البيانات → المراجعة)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-011 | P2 ثانوي | وصول | PayoutForm: section step panels + Button payout-next disabled | تبديل خطوات السحب صامت لقارئ الشاشة وزر «التالي» معطّل بلا تفسير | كل خطوة تستبدل القسم كاملاً دون نقل التركيز إلى h2 الخطوة ودون aria-live؛ في خطوة القناة لا يوجد autoFocus فيبقى التركيز على زر «التالي» ويُعلَن فقط «زر». زر التالي disabled حتى يصحّ المبلغ، فلا يستطيع المستخدم طلب التحقق ولا يسمع سبب التعطيل. (مراجعة كود؛ الحساب التجريبي غير ممول فلم يُنفَّذ المسار الحي.) | apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx:73-110,196-204; .e2e/a11y-client.log *:payoutNew:amountField = «no field (not funded?)» | عند next() انقل التركيز إلى h2 الخطوة (tabIndex=-1) وأضف منطقة aria-live=polite تقول «الخطوة 2 من 4: القناة»؛ أبقِ «التالي» مفعّلاً وأظهر خطأ الحقل عند الضغط. | M | WIG Forms «Submit button stays enabled»; ui-ux-pro-max `contextual-live-badge-updates`, `voiceover-sr` | `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx` |

### العميل — payout-new (خطوة القناة)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-041 | P3 صقل | وصول | label radio: <span class="text-label text-muted">manualChannel</span> | وسم «تنفيذ يدوي» داخل تسمية القناة بحجم 13 px muted — ضمن الاسم المنطوق لكنه صغير بصرياً | التسمية تُقرأ كاملة (جيد)؛ بصرياً 13 px بـ 0.55 (6.28:1) تجتاز التباين لكنها تحت 15 px لنص يشرح معنى الخيار. | apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx:114 | text-sm (14) أو body-2 (15) وسطر ثانٍ تحت الاسم كما في طريقة الدفع. | S | docs/design-system.md § 2 | `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx` |

### العميل — payout-new 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-054 | P3 صقل | بصري | بطاقة «المتاح للسحب» | الخانة الثالثة «أيام التداول المموَّلة 12 / 10» وحدها في صف جديد بعرض نصف | شبكة من عمودين لثلاث قيم تترك الخانة الثالثة يتيمة مع فراغ بجوارها على كل خطوة من خطوات السحب الأربع. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-payout-new.png; artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-payout-review.png | ثلاثة أعمدة على الهاتف أو ضمّ الأيام إلى سطر الدورة «الدورة مكتملة · 12 من 10 أيام». | S | critique-protocol Layout rhythm؛ mobile-app-ui-design 8-pt grid | `apps/client/src/app/[locale]/(app)/payouts/new/page.tsx` |

### العميل — payout-new

اللقطات: `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-payout-new.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-payout-new.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-payout-new.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-053 | P3 صقل | نص | تسمية الحقل | «الربح المطلوب سحبه (دولار)» بكلمة «دولار» بينما اللاحقة والتلميح يستخدمان USD | ثلاث صيغ للعملة في كتلة واحدة: (دولار)، USD داخل الحقل، «12,500.00 USD» في التلميح. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-payout-new.png | «الربح المطلوب سحبه» فقط؛ الرمز في اللاحقة يكفي. | S | fpt-brand (رمز العملة)؛ ui-ux-pro-max §4 consistency | `packages/i18n/src/messages/ar.json` |
| VCL-055 | P3 صقل | نص | صيغة الأيام | «12 / 10» للأيام المموَّلة تُقرأ كتاريخ أو كسر | القيمة «12 / 10» بلا كلمات؛ في الدفعات تُكتب «12 من 10 يوم تداول» وهي أوضح. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-payout-new.png; artifacts/real-device/coverage-20261030/local-chromium-390/client-payouts.png | وحّد على «12 من 10 أيام» (rule.daysValue). | S | ui-ux-pro-max §10 number-formatting؛ Nielsen #2 | `apps/client/src/app/[locale]/(app)/payouts/new/page.tsx`، `packages/i18n/src/messages/ar.json:254` |

### العميل — payouts 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-025 | P2 ثانوي | RTL | سطر الحدّ والحصص | النسبة تنكسر بين سطرين: «80» في نهاية سطر و«%» في بداية التالي | «الحدّ الأقصى لكل طلب 12,500.00 USD · حصة المتداول 80 % · حصة الشركة 20 %» يلتف عند 390 بحيث ينفصل الرقم عن علامة النسبة؛ الشيء نفسه في رأس صفحة الحساب المموَّل («حصة الشركة 20 / %» على سطرين). | artifacts/real-device/coverage-20261030/local-chromium-390/client-payouts.png; artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-payout-detail.png; artifacts/ux-audit-F4/evidence/client-credentials-funded-ar-phone.png (780 px); artifacts/ux-audit-F4/evidence/client-rules-funded-ar-phone.png | لفّ كل «80 %» بـ num-ui (nowrap + isolate) واجعل الفواصل «·» نقاط التفاف مسموحة. | S | ui-ux-pro-max §6 long-token-wrapping / number-tabular؛ fpt-bidi-currency-trap | `apps/client/src/app/[locale]/(app)/payouts/page.tsx`، `apps/client/src/app/[locale]/(app)/payouts/[id]/page.tsx` |

### العميل — payouts

اللقطات: `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-payouts.png`، `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-payouts-en.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-payouts.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-payouts-en.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-payouts.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-payouts-en.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-desktop.png` … (+2)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-052 | P3 صقل | بصري | متتبع الطلبات السابقة | متتبع الطلب في القائمة أرقام «1 ✓ … 5 ✓» بلا كلمات | في بطاقة الطلب السابق تظهر خمس خطوات بأرقام فقط؛ الصفحة التفصيلية تعرض الكلمات (طُلب، فُحص، موافقة 1…). | artifacts/real-device/coverage-20261030/local-chromium-390/client-payouts.png; artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-payout-detail.png | في القائمة شريط تقدم واحد + نص «صُرف في 14 أكتوبر»، واترك الخطوات الخمس للتفاصيل. | S | cognitive-principles Progressive disclosure؛ ui-ux-pro-max §8 multi-step-progress | `apps/client/src/components/payout-tracker.tsx`، `apps/client/src/app/[locale]/(app)/payouts/page.tsx` |

### العميل — payouts-empty 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-041 | P2 ثانوي | بصري | الحالات الفارغة | الحالات الفارغة توضّح لكنها بلا فعل تالٍ | «لا طلبات سحب بعد» و«لا شهادات بعد» و«لا إشعارات» تعرض أيقونة وجملة فقط؛ لا زر يقود إلى الخطوة المنطقية (مثلاً «ابدأ تحدياً» أو «افتح الحساب لمتابعة الأيام»). الأيقونة 24 px صغيرة والكتلة تطفو في ثلث الشاشة العلوي. | artifacts/ux-audit-F4/evidence/client-payouts-empty-ar-phone.png; artifacts/ux-audit-F4/evidence/client-certificates-empty-ar-phone.png; artifacts/ux-audit-F4/evidence/client-notifications-empty-ar-phone.png; packages/ui/src/components/empty-state.tsx | أضف action اختيارياً لـ EmptyState واستخدمه: الدفعات → «افتح حسابك الممول»/«ابدأ تحدياً»، الشهادات → «القواعد: كيف تُصدر الشهادة»؛ أيقونة 40 px ومسافة علوية أكبر. | S | mobile-app-ui-design (empty states as opportunities)؛ ui-ux-pro-max §8 empty-states | `packages/ui/src/components/empty-state.tsx`، `apps/client/src/app/[locale]/(app)/payouts/page.tsx`، `apps/client/src/app/[locale]/(app)/certificates/page.tsx` |

### العميل — payouts-empty

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-042 | P3 صقل | نص | السطر التمهيدي + الحالة الفارغة | رسالتان متتاليتان بالمعنى نفسه: «تحتاج إلى حساب مموَّل لطلب سحب» ثم «لا طلبات سحب بعد… تُطلب من حساب مموَّل» | الصفحة الفارغة تكرر الشرط مرتين تحت عنوان «الطلبات السابقة» الذي لا طلبات تحته. | artifacts/ux-audit-F4/evidence/client-payouts-empty-ar-phone.png | حالة فارغة واحدة بعنوان «السحب يبدأ مع الحساب المموَّل» وجملة الشرط وزر. | S | Nielsen #8؛ ux-copy | `apps/client/src/app/[locale]/(app)/payouts/page.tsx`، `packages/i18n/src/messages/ar.json` |

### العميل — profile (desktop)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-033 | P3 صقل | بصري | حقلا الاسم الأول واسم العائلة | على سطح المكتب الحقلان غير متحاذيين: الاسم الأول ينزل تحت مستوى اسم العائلة بسبب نص مساعد تحت أحدهما فقط | في شبكة العمودين (≥ 640 px) يوجد نص مساعد تحت «اسم العائلة» وحده فيُمدّد الصف ويُوسَّط حقل «الاسم الأول» عمودياً أسفل نظيره. على 390 الحقلان متراصّان عمودياً والمشكلة لا تظهر (تم التحقق بعد إعادة الالتقاط). | artifacts/ux-audit-F4/evidence/client-profile-ar-desktop.png (1440); apps/client/src/app/[locale]/(app)/profile/forms.tsx | items-start على الشبكة أو انقل النص المساعد تحت الحقلين معاً بعرض كامل. | S | critique-protocol Spacing / alignment؛ cognitive-principles Continuity | `apps/client/src/app/[locale]/(app)/profile/forms.tsx` |

### العميل — profile

اللقطات: `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-profile.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-profile.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-profile.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-010 | P1 رئيسي | بصري | قسم «المستندات» | قائمة المستندات تكرّر الوثائق الأربع عشرات المرات (قبول لكل عملية) بدل آخر نسخة مقبولة | التقاط الملف بطول 10,470 px معظمه صفوف «شروط الخدمة / سياسة الخصوصية / الإفصاح / عدم الاسترداد» مكررة بالتاريخ نفسه (30 أكتوبر 2026). القسم لا يُقرأ، ويدفع «حذف حسابك» إلى أسفل صفحة لا نهاية لها. | artifacts/ux-audit-F4/evidence/client-profile-ar-phone.png; artifacts/ux-audit-F4/evidence/client-profile-ar-desktop.png; apps/client/src/app/[locale]/(app)/profile/page.tsx:41,115-126 | اجمع القبولات حسب مفتاح المستند (آخر نسخة + أول تاريخ قبول) فتظهر أربعة صفوف فقط مع رابط «سجل القبول الكامل» إن لزم. | S | cognitive-principles Miller / Cognitive load؛ critique-protocol Layout rhythm | `apps/client/src/app/[locale]/(app)/profile/page.tsx` |
| VCL-034 | P2 ثانوي | نص | المنطقة الزمنية | المنطقة الزمنية تُعرض مرتين: قائمة منسدلة في نموذج الهوية وصف «المنطقة الزمنية للعرض» في التفضيلات | الصفحة تحمل اختيار Asia/Dubai داخل نموذج «هويتك» مع النص المساعد نفسه، ثم قسم تفضيلات يعرض المنطقة ذاتها مع زر «استخدم Asia/Qatar». تكرار يربك ويطيل. | artifacts/ux-audit-F4/evidence/client-profile-ar-phone.png (المقطع الأول) | أبقِ ضبط المنطقة في مكان واحد (التفضيلات)، واجعل اقتراح المنطقة المكتشفة سطراً داخل الصف نفسه. | S | Nielsen #8؛ ui-ux-pro-max §1 redundant-entry | `apps/client/src/app/[locale]/(app)/profile/page.tsx`، `apps/client/src/app/[locale]/(app)/profile/forms.tsx`، `apps/client/src/components/timezone-prompt.tsx` |
| VCL-035 | P3 صقل | بصري | قسم الأمان | ثلاثة نماذج متتالية (تغيير البريد، كلمة المرور، المصادقة) مكشوفة دفعة واحدة بحقول فارغة | الأمان يعرض ستة حقول فارغة وزرّين «حفظ/تغيير» في الصفحة نفسها، وبينها حقل «كلمة المرور الحالية» مرتين؛ حمل معرفي بلا داعٍ لصفحة إعدادات. | artifacts/ux-audit-F4/evidence/client-profile-ar-phone.png (المقطع الثاني) | صفوف ListRow «تغيير البريد ›» «تغيير كلمة المرور ›» تفتح Sheet بالنموذج (إفصاح تدريجي). | M | cognitive-principles Progressive disclosure / Hick؛ ui-ux-pro-max §8 progressive-disclosure | `apps/client/src/app/[locale]/(app)/profile/page.tsx`، `apps/client/src/app/[locale]/(app)/profile/email-section.tsx` |

### العميل — shop

اللقطات: `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-shop.png`، `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-shop-en.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-shop.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-shop-en.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-shop.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-shop-en.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-008 | P2 ثانوي | وصول | ChallengeList role=radiogroup → ChallengeCard <button type="submit" role="radio"> | بطاقات الحجم تعلن نفسها radio لكنها أزرار إرسال: لا أسهم، كلها في ترتيب Tab، و Space/Enter يرسل النموذج | القياس: 5 عناصر role=radio كلها type=submit بلا tabindex (كل واحدة محطة Tab)، ArrowDown من البطاقة الأولى لا يحرّك التركيز (بقي على buy-10k). نمط radiogroup في ARIA APG يتطلب محطة Tab واحدة وتنقّلاً بالأسهم، والتفعيل يغيّر الاختيار لا يرسل. قارئ الشاشة يقول «زر اختيار» ثم يفاجأ بالانتقال إلى الدفع. | .e2e/a11y-client.log phone/desktop:shop:cards و shop:radioArrow; packages/ui/src/components/challenge-card.tsx:40-50; apps/client/src/app/[locale]/(app)/shop/challenge-picker.tsx:34-48 | إمّا أزرار عادية (بلا role=radio، aria-pressed=selected) مع الإبقاء على الإرسال، أو radiogroup حقيقي: input type=radio sr-only داخل label (كما في قناة السحب) والإرسال من زر «ابدأ» فقط. | M | accessibility.md § ARIA Patterns; ui-ux-pro-max `keyboard-nav`, `voiceover-sr` | `packages/ui/src/components/challenge-card.tsx`، `apps/client/src/app/[locale]/(app)/shop/challenge-picker.tsx` |
| VCL-023 | P2 ثانوي | بصري | بطاقة الحجم (ChallengeCard) | في RTL تُقرأ الرسوم أولاً وحجم الحساب (القيمة الرئيسية) في الطرف الأيسر | بطاقات المتجر تضع «90 USD الرسوم» عند اليمين (بداية القراءة) و«10,000 USD حجم الحساب» بخط أكبر عند اليسار؛ التسلسل البصري يناقض الهرم (الحجم هو القرار، الرسوم تابعة). | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-shop.png; artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-shop-en.png | ابدأ بالحجم عند inline-start والرسوم عند inline-end في اللغتين (استخدم ترتيب DOM منطقياً بدل الترتيب البصري الثابت). | S | critique-protocol Focal point / reading path؛ mobile-app-ui-design hierarchy (labels vs values) | `packages/ui/src/components/challenge-card.tsx` |

### العميل — shop-product

اللقطات: `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-shop-product.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-shop-product.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-shop-product.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-051 | P2 ثانوي | بصري | ترتيب المحتوى | الزر الأساسي «المتابعة إلى الدفع» قبل القواعد التي ينبغي قراءتها قبل الشراء | الزر الأحمر يأتي بعد ثلاث حقائق فقط ثم قائمة القواعد الكاملة تحته؛ المستخدم يُدفع للدفع قبل رؤية 5 % و9 % و31 يوماً. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-shop-product.png | ضع القواعد قبل الزر أو ثبّت الزر أسفل الشاشة (sticky فوق شريط التبويب) مع القواعد في المنتصف. | M | mobile-app-ui-design thumb zone / F-pattern؛ Nielsen #5 | `apps/client/src/app/[locale]/(app)/shop/[productId]/page.tsx` |
| VCL-050 | P3 صقل | بصري | البطل + السطر التحتي | «50,000 USD» ثم «تقييم 50,000 USD» تحته مباشرة — تكرار الرقم | السطر الثانوي يعيد الرقم البطل نفسه بدل إضافة معلومة (مثلاً «تقييم · هدف 10 % · 31 يوماً»). | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-shop-product.png | اجعل السطر الثانوي ملخص القواعد الثلاثة. | S | critique-protocol Hierarchy؛ Nielsen #8 | `apps/client/src/app/[locale]/(app)/shop/[productId]/page.tsx` |

### العميل — signin (كلمة المرور 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-006 | P2 ثانوي | وصول | PasswordSigninForm: Alert tone=danger + Input email/password | خطأ تسجيل الدخول لا يُربط بالحقول ولا يُنقل التركيز إليه | إرسال فارغ: alerts=[«أدخل عنوان بريد إلكتروني صالحاً.»]، aria-invalid=[] ، aria-describedby=null للحقلين، focused=BODY. كلمة مرور خاطئة: alert موجود، لا aria-invalid، focused=BODY. يُعلَن الخطأ (role=alert) لكن مستخدم لوحة المفاتيح يفقد موضعه ويعود من أول الصفحة، ولا يعرف قارئ الشاشة أي حقل هو المقصود. | .e2e/a11y-client.log phone/desktop:signin:emptyError, signin:wrongError; apps/client/src/app/[locale]/(auth)/auth-form.tsx:54-70 | عند خطأ emailInvalid مرّر error إلى Field البريد (يضبط aria-invalid و aria-describedby) وانقل التركيز إلى الحقل؛ عند بيانات خاطئة اضبط aria-invalid على الحقلين، وانقل التركيز إلى Alert (tabIndex=-1) أو إلى حقل البريد. | S | WIG Forms «Errors inline next to fields; focus first error on submit»; ui-ux-pro-max `error-feedback` | `apps/client/src/app/[locale]/(auth)/auth-form.tsx`، `packages/ui/src/components/field.tsx` |

### العميل — signin

اللقطات: `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-signin.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-signin.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-signin.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-desktop.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-desktop.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-en-phone.png` … (+3)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-031 | P2 ثانوي | بصري | حقل البريد بحالة التركيز | حلقة التركيز الحمراء على حقل فارغ تُقرأ كحالة خطأ | عند فتح تسجيل الدخول يحمل حقل البريد إطاراً أحمر سميكاً (autofocus + focus ring بلون البراند) بلا رسالة؛ بصرياً يطابق حقل خطأ، ويتكرر على الهاتف حيث لا مؤشر للتركيز. | artifacts/real-device/coverage-20261030/local-chromium-390/client-signin.png; artifacts/real-device/coverage-20261030/local-chromium-390/client-signup.png; artifacts/ux-audit-F4/evidence/client-signin-focus-en-phone.png (780 px); artifacts/ux-audit-F4/evidence/client-signin-focus-ar-desktop.png | اجعل حلقة التركيز بلون محايد (أبيض 60 % / --fpt-action-focus-ring مختلف عن الأحمر) واحتفظ بالأحمر-البرتقالي للخطأ فقط؛ أو لا تضع autofocus على الهاتف. | S | ui-ux-pro-max §1 focus-appearance؛ §6 color-not-decorative-only؛ fpt-brand (الأحمر للفعل الأساسي) | `brand/tokens.css`، `packages/ui/src/components/input.tsx` |
| VCL-032 | P2 ثانوي | بصري | رابط «هل نسيت كلمة المرور؟» | رابط استعادة كلمة المرور نص رمادي بلا تسطير بينما الروابط المجاورة مسطّرة | «تسجيل الدخول برمز عبر البريد» و«إنشاء حساب» مسطّران وبلون النص الأساسي، أما «هل نسيت كلمة المرور؟» فرمادي بلا تسطير فيبدو جملة وصفية. | artifacts/real-device/coverage-20261030/local-chromium-390/client-signin.png; artifacts/ux-audit-F4/evidence/client-signin-focus-en-phone.png | وحّد نمط الروابط الثلاثة (variant link) ورتّبها: أساسي (رمز البريد)، ثم نسيت كلمة المرور، ثم إنشاء حساب. | S | cognitive-principles Similarity؛ ui-ux-pro-max §4 consistency | `apps/client/src/app/[locale]/(auth)/signin/page.tsx` |
| A11Y-027 | P3 صقل | وصول | Link «هل نسيت كلمة المرور؟» (hover:underline) | رابط استعادة كلمة المرور بلا تسطير أو تمييز إلا عند التحويم | في تذييل muted يظهر «هل نسيت كلمة المرور؟» كنص عادي؛ الرابط الآخر مسطَّر. المستخدمون باللمس لا يرون التحويم. (لقطة الهوية client-signin-focus-ar-phone.png) | apps/client/src/app/[locale]/(auth)/signin/page.tsx:34; artifacts/ux-audit-F4/evidence/client-signin-focus-ar-phone.png | underline underline-offset-4 دائماً كالرابط المجاور. | S | ui-ux-pro-max `hover-vs-tap`; WCAG 1.4.1 | `apps/client/src/app/[locale]/(auth)/signin/page.tsx` |

### العميل — signin-wrong

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-desktop.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-en-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-en-desktop.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-030 | P2 ثانوي | وصول | رسالة الخطأ | خطأ بيانات الدخول يظهر كتنبيه أعلى النموذج بلا ربط بالحقول ولا تركيز | بعد كلمة مرور خاطئة يظهر تنبيه أعلى البطاقة، الحقلان فارغان بلا حالة خطأ ولا تركيز على البريد؛ على الهاتف قد يكون التنبيه خارج الشاشة بعد إغلاق لوحة المفاتيح. | artifacts/ux-audit-F4/evidence/client-signin-wrong-en-phone.png (780 px، مؤكَّد); artifacts/ux-audit-F4/evidence/client-signin-wrong-ar-desktop.png (النسخة ar-phone جاءت فارغة في إعادة الالتقاط — انظر VCL-073) | ركّز الحقل الأول بعد الفشل، علّم الحقلين aria-invalid، واربط التنبيه بـ aria-describedby؛ أبقِ البريد المدخل. | S | ui-ux-pro-max §8 focus-management / error-placement / aria-live-errors | `apps/client/src/app/[locale]/(auth)/signin/signin-form.tsx` |

### العميل — signup 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-077 | P3 صقل | بصري | رأس الصفحة العامة | اسم الشركة بجانب الشعار يظهر عند 430 ويختفي عند 390 — رأسان مختلفان بين iPhone وChromium | min-[400px]:inline يجعل «Fiper Pro Traders» يظهر على iPhone 15 Pro Max ويختفي على 390؛ تغيّر سلوك الرأس بفارق 10 px بين الهواتف. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-help.png; artifacts/real-device/coverage-20261030/local-chromium-390/client-signin.png; apps/client/src/components/client-header.tsx:55 | قرار واحد للهواتف (< 640): شعار فقط أو شعار + اسم دائماً بخط 14 px. | S | ui-ux-pro-max §5 breakpoint-consistency | `apps/client/src/components/client-header.tsx` |

### العميل — signup 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-068 | P3 صقل | بصري | تذييل «جزء من Fiper Global» | «جزء من Fiper Global» يظهر مرتين: تحت الشعار وفي التذييل | في شاشات الدخول الثلاث يتكرر السطر نفسه في الرأس والتذييل على مسافة شاشة واحدة. | artifacts/real-device/coverage-20261030/local-chromium-390/client-signup.png; artifacts/real-device/coverage-20261030/local-chromium-390/client-signin.png; artifacts/ux-audit-F4/evidence/client-reset-submitted-ar-phone.png | أبقِ السطر تحت الشعار واجعل التذييل روابط قانونية قصيرة. | S | Nielsen #8 | `apps/client/src/app/[locale]/(auth)/layout.tsx` |

### العميل — signup, signup

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-025 | P3 صقل | وصول | <p className="text-xs"> في تذييل AuthCard | نصوص شروط التسجيل وتعليمات فقدان المصادِق بحجم 12 px تحت أرضية النص 15 px | auth.signup.terms و referred و twofa.lost (تحوي بريد الدعم) بـ text-xs (12 px) muted. أرضية F1: نص الجسم ≥ 15 px. بوابات المقاس لا تزور 2fa. | apps/client/src/app/[locale]/(auth)/signup/page.tsx:29-30; apps/client/src/app/[locale]/(auth)/2fa/page.tsx:31; packages/ui/src/tokens.css:98 | استخدم text-body-2 (15 px) للجمل، واترك 12–13 px للتسميات القصيرة فقط. | S | docs/design-system.md § 2 (body ≥ 15 px); ui-ux-pro-max §6 «Text < 12px body» | `apps/client/src/app/[locale]/(auth)/signup/page.tsx`، `apps/client/src/app/[locale]/(auth)/2fa/page.tsx` |

### العميل — signup

اللقطات: `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-signup.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-signup.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-signup.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-069 | P3 صقل | وصول | سطر الموافقة | «بالمتابعة فإنك تقبل شروط الخدمة وسياسة الخصوصية» بلا روابط | الجملة نص رمادي عادي؛ أسماء المستندات غير مرتبطة بصفحاتها. | artifacts/real-device/coverage-20261030/local-chromium-390/client-signup.png | اربط «شروط الخدمة» و«سياسة الخصوصية» بـ /legal/terms و/legal/privacy (target=_blank). | S | Nielsen #10 help and documentation؛ ui-ux-pro-max §1 accessible-authentication | `apps/client/src/app/[locale]/(auth)/signup/page.tsx` |

### العميل — trades 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-040 | P3 صقل | بصري | زر «تنزيل CSV» | زر التصدير مفعّل رغم «0 صفقة» | الحالة الفارغة تعرض زر CSV كاملاً بجانب «0 صفقة»؛ تنزيل ملف فارغ ليس فعلاً مفيداً. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-trades.png; artifacts/real-device/coverage-20261030/local-chromium-390/client-trades.png; apps/client/src/components/trades-panel.tsx:33 | أخفِ الزر أو عطّله (aria-disabled + opacity 0.5) عندما لا توجد صفقات. | S | ui-ux-pro-max §8 disabled-states / empty-states | `apps/client/src/components/trades-panel.tsx` |

### العميل — verify, 2fa

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-018 | P3 صقل | وصول | OtpInput: <div role="group" id=…> مع <label for=id> | مجموعة خانات الرمز بلا اسم: label for لا يسمّي div role=group، و aria-invalid على group غير مدعوم | القياس: group label=null رغم وجود label[for=f-…] لأن label لا يسمّي إلا العناصر القابلة للتسمية. كل خانة مسمّاة («الرقم 1 من 6») فالاستخدام ممكن، لكن اسم الحقل «الرمز» لا يُنطق عند الدخول إلى المجموعة. aria-invalid على role=group خارج ARIA 1.2. | .e2e/a11y-client.log desktop:verify:otp role=group label=null labelFor=true; packages/ui/src/components/otp-input.tsx:233 | اضبط aria-labelledby على المجموعة بمعرّف الـ label (مرّره من Field) واحذف aria-invalid من المجموعة واتركه على الخانات. | S | ui-ux-pro-max `form-labels`, `aria-labels` | `packages/ui/src/components/otp-input.tsx`، `packages/ui/src/components/field.tsx` |

### العميل — verify

اللقطات: `iphone-15-pro-max: artifacts/real-device/coverage-20261030/iphone-15-pro-max/client-verify.png`، `samsung-galaxy-s23: artifacts/real-device/coverage-20261030/samsung-galaxy-s23/client-verify.png`، `local-chromium-390: artifacts/real-device/coverage-20261030/local-chromium-390/client-verify.png`

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VCL-065 | P3 صقل | بصري | شارة «شهادة صالحة» | الشارة ممدودة على كامل عرض البطاقة كزر | شارة outline خضراء بعرض 100 % تبدو زراً قابلاً للنقر. | artifacts/real-device/coverage-20261030/local-chromium-390/client-verify.png | شارة بعرض محتواها بمحاذاة البداية. | S | cognitive-principles Similarity (don't style different functions identically) | `apps/client/src/app/[locale]/(public)/verify/[code]/page.tsx` |

### العميل — أيقونة التطبيق على Android (تثبيت PWA)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-029 | P2 ثانوي | هوية | apps/client/public/manifest.webmanifest icons | لا أيقونة `purpose: "maskable"`: Android يحشو البلاطة داخل قناع الشكل التكيّفي فتبدو صغيرة بحواف غير مقصودة | الأيقونتان 192/512 بلا `purpose`؛ المشغّلات التكيّفية (Pixel/Samsung) تُصغّر الأيقونة غير القابلة للقناع إلى ~60 % داخل دائرة/مربّع بخلفية بيضاء افتراضية، فتفقد البلاطة الحمراء حجمها وتحيط بها حافة بيضاء على أرضية سوداء — مخالفة لتركيب البلاطة الرسمية. iOS يستخدم `apple-touch-icon-180.png` (موجود). | apps/client/public/manifest.webmanifest:61-64; artifacts/real-device/android/* (لقطات الشاشة داخل المتصفح فقط — لا لقطة للمشغّل). | أضف `{ "src": "/brand/app-icon-maskable-512.png", "sizes": "512x512", "type": "image/png", "purpose": "maskable" }` (البلاطة على أرضية `#0A0A0A` مع منطقة أمان 20 % — أصل مُقترَح/PROPOSED يُشتق من brand/logos ويُسجَّل في brand/value-sources.md قبل الاعتماد). | S | fpt-brand (app icon) + web-interface-guidelines § Images | `apps/client/public/manifest.webmanifest`، `apps/client/public/brand/` |

### العميل — الإشعارات

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-054 | P3 صقل | نص | notifications.profit_target.title «بلغت هدف الربح» / body «بلغ الحساب {reference}» · notifications.passed.title «اجتزت التقييم» / body «مبارك — اجتاز الحساب» ↔ mail.passed.subject «تهانينا:» | انتقال الفاعل بين العنوان (أنت) والنص (الحساب)، وكلمتا تهنئة مختلفتان | العنوان يخاطب العميل والنص يتحدث عن الحساب؛ «مبارك» في الإشعار و«تهانينا» في البريد. | ar.json:L1408 / en.json:L1408; ar.json:L1409 / en.json:L1409; ar.json:L1413 / en.json:L1413; ar.json:L126 / en.json:L126 | فاعل واحد (الحساب) في العنوان والنص، و«تهانينا» في الموضعين. | S | design:ux-copy | `packages/i18n/src/messages/ar.json` |

### العميل — الشهادة 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-040 | P2 ثانوي | نص | client.certificate.subtitleFunded · client.certificate.subtitlePassed · client.statement.subtitle (AR = EN) ↔ purchases.receiptSubtitle «Fiper Pro Traders · رسوم التقييم» | سطر فرعي إنجليزي بالكامل في الشهادة والكشف العربيين بينما الإيصال مترجم | «Funded Account Certificate · Fiper Pro Traders» يظهر كما هو في النسخة العربية؛ إن كان تصميم ثنائي اللغة مقصوداً فليكن ذلك على الإيصال أيضاً، وإلا فيُترجم. | ar.json:L442 / en.json:L442; ar.json:L461 / en.json:L461; ar.json:L3288 / en.json:L3288 | «شهادة حساب ممول · Fiper Pro Traders» / «كشف الدفعة · Fiper Pro Traders» (المعجم § 7)، أو توثيق قرار السطر اللاتيني وتطبيقه على الإيصال. | S | brand glossary § 7 | `packages/i18n/src/messages/ar.json` |

### العميل — الصفحات العامة على iPhone حقيقي (landing

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-004 | P3 صقل | تقني | packages/core/src/csp.ts — style-src / style-src-elem / style-src-attr | تحليل رسالة Safari iOS «Refused to apply a stylesheet… style-src»: ليست سمات `style=` ولا نقص دعم `style-src-attr`؛ المصدر عنصر `<style>` بلا nonce غير مرئي في المصدر، ويلزم جامع تقارير | الفرضية «iOS 17 لا يدعم style-src-attr» غير صحيحة: WebKit يدعم `script-src-elem/attr` و`style-src-elem/attr` منذ Safari 15.4، ومع `style-src-attr 'unsafe-inline'` لا يبلّغ WebKit أي مخالفة لسمة `style=` أصلاً (المسار `violatedDirectiveForUnsafeInlineStyleAttribute` يعود null عندما يسمح التوجيه بالـ inline). الرسالة نصّها «its hash, its nonce, or 'unsafe-inline'» وهي رسالة التنسيق المضمّن (عنصر `<style>` أو ورقة مضمّنة) والتوجيه المذكور `style-src` هو الاحتياطي. المصدر لا يحوي أي `<style>` في JSX (grep صفر)، وSVG شعار cTrader (الوحيد المحمَّل كصورة في الصفحات العامة) لا يحوي `<style>` ولا `style=`؛ وعلى Chromium المحلي سجّل التشخيص `unnoncedStyles: []`. أي أن العنصر يُحقن أثناء التشغيل على iOS فقط: مرشّحوه (أ) التنسيقات التي يحقنها جامع كونسول BrowserStack/الحارس عبر Web Inspector، (ب) عناصر `<style>` يحقنها WebKit نفسه لواجهات الـ AutoFill/Reader (عادة في شجرة UA وتتخطى CSP)، (ج) تنسيق يحقنه كود طرف أول على العميل فقط في فرع iOS. الأثر الوظيفي صفر على الصفحات المُلتقطة (البوابات مرّت: لا قصّ ولا فيض)، لذلك P3؛ لكن بدون `report-uri` لا يمكن حسم المصدر، وجامع `securitypolicyviolation` مركّب فقط لمحرّكات Playwright لا للجهاز الحقيقي. | artifacts/real-device/coverage-20261030/iphone-15-pro-max/coverage-receipt.json → `screens[client/landing@ar\|signin@ar\|signup@ar\|reset@ar\|help@ar\|not-found@ar].detail.consoleErrors[*].text`; packages/core/src/csp.ts:56-58; scripts/real-device-coverage.mjs:95-99 (`unnoncedStyles`, `cspViolations` من init script لـ Playwright فقط) و:101-105 (`CSP_COLLECTOR`)؛ artifacts/real-device/coverage-20261030/local-chromium-390/coverage-receipt.json → `"unnoncedStyles": []` ×17؛ apps/client/public/brand/third-party/ctrader/ctrader-logo-light.svg (0 `<style>`، 0 `style=`). | الإصلاح الأدنى الآمن: أضف `report-uri /api/csp-report` إلى سياسة المستندات (Safari لا يدعم `report-to`) ومعالجاً يسجّل `violated-directive`, `effective-directive`, `blocked-uri`, `source-file`, `line-number`, `script-sample` (بلا تخزين بيانات شخصية، مع حدّ معدل)؛ شغّل جولة iPhone واحدة ثم: إن كان المصدر حارس القياس → أضفه إلى قائمة التجاهل مثل سكربت التتبّع في إطار البريد؛ إن كان عنصراً من الطرف الأول → مرّر له `nonce` من `headers().get('x-nonce')` أو أضف تجزئته `'sha256-…'` إلى `style-src-elem`. لا تضف `'unsafe-inline'` إلى `style-src`/`style-src-elem` بأي حال. | S | security-audit (CSP) | `packages/core/src/csp.ts`، `apps/client/src/middleware.ts`، `scripts/real-device-coverage.mjs` |

### العميل — الصفحة العامة (landing) + التذييل

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-004 | P1 رئيسي | هوية | client.landing.honesty · client.landing.simulated · config/legal.ts entity.programDescriptor | عبارات مخاطر ووصف برنامج مؤلَّفة داخلياً على الصفحة العامة دون اعتماد | «التداول ينطوي على مخاطر. لا يضمن FPT أرباحاً ولا يقدّم نصائح استثمارية وليس وسيطاً. الرسوم غير قابلة للاسترداد.» تُعرض في page.tsx:87، و«FPT هو برنامج تقييم التداول الاحترافي التابع لـ Fiper Global. ليس وسيطاً ولا يقدّم نصائح استثمارية.» في التذييل. المعجم § 7 يصنّف «التداول ينطوي على مخاطر» كـ PROPOSED PLACEHOLDER — NOT LEGAL TEXT (REQUIRED DECISION)، والقرار 2 يشترط اعتماد صياغة بيان المخاطر، والقرار 10 يمنع أي ادعاء تنظيمي/وصفي غير مقرَّر. نص الموقع الملاحَظ F2 نفسه «غير مُعاد إنتاجه على أسطح جديدة حتى اعتماده». | ar.json:L400 / en.json:L400; apps/client/src/app/[locale]/(public)/page.tsx:87; config/legal.ts:14-16; apps/client/src/components/footer.tsx:21; brand/glossary.md § 7 (video/end-card meta line) | إزالة السطرين حتى ورود نص معتمد، أو حصرهما في وقائع المنتج المعتمدة فقط («الرسوم غير قابلة للاسترداد» — pricing.badges). لا تُترجم F2 ولا تُعاد صياغتها. | S | brand regulatory-matrix § 3 (2, 10) | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `config/legal.ts`، `apps/client/src/app/[locale]/(public)/page.tsx` |

### العميل — الصفحة العامة

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-073 | P3 صقل | نص | client.landing.phase1Body «حقق هدف الربح مع الالتزام بمعايير المخاطرة.» (EN: risk limits) · client.landing.lead «لخمسة أيام» · client.landing.sizesTitle «خمسة أحجام» | «معايير المخاطرة» ≠ «حدود الخسارة»، وأرقام بالحروف خلافاً لقاعدة الأرقام اللاتينية |  | ar.json:L392 / en.json:L392; ar.json:L384 / en.json:L384; ar.json:L386 / en.json:L386 | «حقّق هدف الربح مع الالتزام بحدود الخسارة.»؛ «لمدة 5 أيام تداول على الأقل»؛ «5 أحجام للحساب» / “5 account sizes”. | S | brand (Latin digits) / meaning parity | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### العميل — المبالغ في النصوص العربية

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-025 | P2 ثانوي | هوية | payouts.reasons.below_minimum «100 دولار» · payouts.form.amount «(دولار)» · payouts.form.errors.amount «{max} دولار» · notifications.payout_review_due.body «{amount} دولار» · notifications.payout_paid.body · notifications.affiliate_commission.body · notifications.manual_correction.body · partners.becomeBody «50 دولاراً» · partners.rules.weekly «50 دولاراً» · coupons.newBody «بالدولار» · client.units.usd «دولار» ↔ partners.nextBatchBelowMin «50 USD» · affiliatesAdmin.description «50 USD» · mail (format.ts) «1,250.00 USD» · seed terms «100 و10,000 دولار» · risk «5 دولارات» | العملة تُكتب «دولار/دولاراً» في نصوص وبرمز USD في نصوص أخرى — والعلامة تشترط رمز العملة | README/الهوية: «المبالغ تحمل رمز عملتها (USD) ووحدة غير قابلة للكسر»؛ formatMoney وبريد النظام يطبعان «1,250.00 USD» بالعربية. النصوص الثابتة تخالف ذلك («100 دولار»، «50 دولاراً») فيظهر الشكلان على الصفحة نفسها (partners.rules.weekly «50 دولاراً» وpartners.nextBatchBelowMin «50 USD»). | ar.json:L969 / en.json:L969; ar.json:L1151 / en.json:L1151; ar.json:L1111 / en.json:L1111; ar.json:L1437 / en.json:L1437; ar.json:L302 / en.json:L302; packages/ui/src/lib/format.ts:69-85 | «100 USD»، «50 USD»، «{amount} USD» داخل وحدة معزولة (⁦…⁩ أو <bdi>) في كل النصوص العربية؛ حذف client.units.usd. | S | fpt-brand finance-semantics (currency code) / CLAUDE.md | `packages/i18n/src/messages/ar.json`، `packages/db/scripts/seed-data/legal.ts` |

### العميل — المتجر 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-015 | P2 ثانوي | نص | client.nav.challenge «التحدي» · client.header.buy «شراء حساب تقييم» · shop.title «اختر تحديك» · shop.productTitle «تقييم {size}» · purchases.product «التحدي» · dashboard.primary.newChallenge «ابدأ تحدياً جديداً» · breach.newChallenge «بدء تقييم جديد» · dashboard.status.breached.next «بدء تقييم جديد» · mail.account_ready.subject «حساب التقييم» ↔ .title «حساب تحديك» | «التحدي» و«التقييم» يتبادلان المواقع للشيء الواحد داخل المسار نفسه | المعجم: «تحدي FPT» اسم المنتج التسويقي و«التقييم» اسم المرحلة. التطبيق يسمّي المنتج في المتجر «تحدي» وفي رأس الصفحة «حساب تقييم»، وزر البدء بعد المخالفة «بدء تقييم جديد» بينما زر اللوحة «ابدأ تحدياً جديداً»، وبريد واحد يحمل «حساب التقييم» في الموضوع و«حساب تحديك» في العنوان. | ar.json:L264 / en.json:L264; ar.json:L588 / en.json:L588; ar.json:L925 / en.json:L925; ar.json:L736 / en.json:L736; ar.json:L105 / en.json:L105; ar.json:L106 / en.json:L106; brand/glossary.md § 1 «تحدي FPT», § 3 «التقييم (مرحلة واحدة فقط)» | قاعدة: المنتج/الشراء = «تحدي» (ابدأ تحدياً جديداً، شراء تحدٍّ، اختر تحديك)؛ المرحلة/الحالة/القواعد = «التقييم» (قيد التقييم، اجتاز التقييم، مدة التقييم). تصحيح المفاتيح المخالفة وفق هذه القاعدة. | S | brand glossary § 1 & § 3 | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### العميل — الهبوط 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-016 | P3 صقل | أداء | packages/ui/src/components/partner-row.tsx + logo.tsx (React 19 image preload) | صور الذيل (شعار cTrader) تُحمَّل مسبقاً قبل محتوى الطيّة الأولى وتنافس LCP، ولا `loading="lazy"` على أي صورة تحت الطيّة | React 19 يصدر `<link rel="preload" as="image">` لكل `<img>` تُصيَّر في الغلاف الأولي ما لم تكن `loading="lazy"`؛ الإنتاج يحمل preload لـ `/brand/third-party/ctrader/ctrader-logo-light.svg` (خط التأييد في الذيل) وللبلاطة على صفحة 404. لا توجد أي `loading="lazy"` في المستودع. LCP الهبوط 2.9 s على الهاتف. | curl https://fptraders.app/ar → `<link rel="preload" as="image" href="/brand/third-party/ctrader/ctrader-logo-light.svg"/>`؛ grep `loading="lazy"` في apps/*/src packages/ui/src → 0؛ packages/ui/src/components/partner-row.tsx:24-26; artifacts/ux-audit-F4/lighthouse/client-landing.json (LCP 2,940 ms). | أضف `loading="lazy"` إلى صور PartnerRow/EndorsementLine (تحت الطيّة) و`fetchPriority="high"` إلى بلاطة الرأس فقط؛ أعطِ شعار cTrader `width`/`height` صريحين (765×271 مع `height` بالـ CSS). | S | web-interface-guidelines § Images / Performance | `packages/ui/src/components/partner-row.tsx`، `packages/ui/src/components/logo.tsx`، `packages/ui/src/components/endorsement-line.tsx` |

### العميل — الوثائق القانونية (مسودة)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-078 | P2 ثانوي | هوية | seed-data/legal.ts privacy §5 «licensed payment providers / مزوّدي دفع مرخّصين» · terms §4 AR «100 و10,000 دولار» · risk §6 AR «5 دولارات» · terms EN «10 000 USD» | ادعاء «مرخّص» ومبالغ بلا رمز عملة في النص القانوني | القرار 10 في مصفوفة النصوص يقيّد أي ادعاء ترخيص بالقرار 1؛ والعلامة تشترط رمز العملة والفاصلة الآلاف «10,000 USD». | packages/db/scripts/seed-data/legal.ts privacy bodyEn §5 / bodyAr §5; terms bodyAr §4; risk bodyAr §6; terms bodyEn §4 | حذف «مرخّصين/licensed» أو إسناده لمصدر معتمد؛ «100 USD و10,000 USD»، «5 USD لكل لوت». | S | brand regulatory-matrix § 3 (10) / finance-semantics | `packages/db/scripts/seed-data/legal.ts` |

### العميل — تبويب البيانات 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-024 | P2 ثانوي | نص | client.tabs.credentials «البيانات» · shop.ret.credentials «بيانات تداولك» · dashboard.credentials.title «بيانات التداول» · notifications.account_ready.body «بيانات الدخول إلى المنصة» · dashboard.status.purchased.next «تصل البيانات» | credentials: «البيانات» وحدها لا تدل على شيء، والبدائل أربعة | تبويب باسم «البيانات» على لوحة الحساب (بجوار «نظرة» و«الصفقات» و«القواعد») لا يخبر العميل أنه يحوي بيانات الدخول. المعجم § 4 يقترح «بيانات الدخول إلى cTrader». | ar.json:L256 / en.json:L256; ar.json:L706 / en.json:L706; ar.json:L799 / en.json:L799; brand/glossary.md § 4 «بيانات الدخول إلى cTrader» | «بيانات الدخول» للتبويب و«بيانات الدخول إلى cTrader» للعناوين والإشعارات، في كل المفاتيح. | S | brand glossary § 4 | `packages/i18n/src/messages/ar.json` |

### العميل — تثبيت PWA

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-028 | P3 صقل | RTL | apps/client/public/manifest.webmanifest | البيان ثابت بالعربية (`start_url` و`id` و`lang`/`dir`) ويقفل الاتجاه عمودياً: تطبيق المستخدم الإنجليزي يُفتح بالعربية والأجهزة اللوحية لا تدور | `start_url: /ar/accounts`, `lang: ar`, `dir: rtl`, `orientation: portrait`. مستخدم اختار الإنجليزية يثبّت التطبيق فيفتح على `/ar/accounts` (كوكي اللغة تُحوّله بعد تحميل، مع وميض)، واسم التطبيق/الوصف بالاتجاه العربي في قائمة التثبيت. `orientation` يمنع الوضع الأفقي على Android ولوحة المفاتيح الخارجية. | apps/client/public/manifest.webmanifest:52-58; apps/client/src/app/[locale]/layout.tsx:95 (`manifest: '/manifest.webmanifest'` واحد لكل اللغات). | قدّم البيان من مسار ديناميكي `/[locale]/manifest.webmanifest` (route handler) يملأ `start_url`/`lang`/`dir`/`description` من اللغة، واربطه في `metadata.manifest` لكل لغة؛ احذف `orientation` (أو `any`). | S | web-interface-guidelines § Locale & i18n + ui-ux-pro-max § PWA | `apps/client/public/manifest.webmanifest`، `apps/client/src/app/[locale]/layout.tsx` |

### العميل — تذييل التطبيق ↔ تذييل البريد

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-044 | P2 ثانوي | هوية | client.footer.rights «جميع الحقوق محفوظة.» + footer.tsx:30 «{legalName} · {brandFull}. {rights}» ↔ mail.common.copyright «© 2026 Fiper Pro Traders — شركة تابعة لـ Fiper Global. جميع الحقوق محفوظة.» | سطرا حقوق مختلفان: البريد يحمل السطر الملاحَظ F1 حرفياً، والتطبيق يركّب سطراً جديداً باسم كيان غير مقرَّر | التذييل يعرض «Fiper Global UAE · Fiper Pro Traders. جميع الحقوق محفوظة.» بلا © ولا سنة ولا عبارة «شركة تابعة لـ Fiper Global»، وهو نص جديد غير وارد في مصفوفة النصوص. | ar.json:L296 / en.json:L296; apps/client/src/components/footer.tsx:30; ar.json:L57 / en.json:L57; brand/regulatory-matrix.md § 1 F1 | استخدام السطر F1 حرفياً في التطبيق كما في البريد حتى صدور القرار 1 (ثم تحديثهما معاً من مكان واحد). | S | brand regulatory-matrix § 1 (F1) / § 4 | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `apps/client/src/components/footer.tsx` |

### العميل — توثيق الهوية — صيغ «موثَّق»

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-085 | P3 صقل | نص | payouts.rejection.name_mismatch «الاسم الموثَّق» · payoutsAdmin.verifiedName «الاسم الموثّق» · payouts.kyc.body «هوية موثَّقة» · profile.kyc.approved «هويتك موثَّقة» ↔ mail.common.states.verified «تم التحقق» | «موثَّق» (documented) مقابل «تم التحقق» (verified) للمعنى نفسه |  | ar.json:L1043 / en.json:L1043; ar.json:L1220 / en.json:L1220; ar.json:L87 / en.json:L87 | «الاسم المتحقَّق منه» / «تم التحقق من هويتك» (مع TXT-045). | S | consistency | `packages/i18n/src/messages/ar.json` |

### العميل — حذف الحساب

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-039 | P2 ثانوي | نص | profile.delete.body «يراجع المالك طلب الحذف» · legalDelete.steps.s3 «يراجع المالك الطلب» · EN «the owner» | دور داخلي («المالك») يُكشف للعميل بلا تعريف | العميل لا يعرف من «المالك»؛ الكلمة اسم دور في لوحة الموظفين. | ar.json:L1200 / en.json:L1200; ar.json:L3306 / en.json:L3306 | «يراجع فريق FPT طلب الحذف» / “Deletion is reviewed by the FPT team”. | S | design:ux-copy | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### العميل — دون اتصال (أي مسار)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-002 | P2 ثانوي | هوية | apps/client/public/sw.js — البديل دون اتصال | لا توجد صفحة «دون اتصال» ذات هوية: البديل هو `Response.error()` أي صفحة خطأ المتصفح الخام | عند فشل الشبكة وغياب نسخة مخزّنة يعيد العامل `Response.error()`، فيعرض Safari/Chrome صفحة خطأ الشبكة الخاصة بهما: بلا شعار ولا لغة عربية ولا زر إعادة محاولة. مفاتيح الترجمة لا تحوي أي نص «دون اتصال» للصفحات (المفتاح الوحيد `offline` هو شارة البث الحي). | apps/client/public/sw.js:44 (`\|\| Response.error()`); apps/client/public/sw.js:8 (المخزّن المسبق: أيقونات وmanifest فقط، لا صفحة)؛ packages/i18n/src/messages/ar.json (لا مفتاح لصفحة دون اتصال). | أضف مسار `/[locale]/offline` (Shell مجرّد + البلاطة + جملة «لا يوجد اتصال» + زر «إعادة المحاولة» = `location.reload()`)، خزّنه في `install` لكلا اللغتين، وأعده في `catch` بدل `Response.error()` مع اختيار اللغة من مقطع المسار. | S | fpt-brand + web-interface-guidelines § Content Handling | `apps/client/public/sw.js`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### العميل — سياسة الخصوصية (مسودة) ↔ بريد بيانات الدخول

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-005 | P1 رئيسي | هوية | seed-data/legal.ts privacy §6 · mail.account_ready.body · dashboard.credentials.intro · credentials-panel.tsx | تناقض في واقعة منتج: الخصوصية تقول إن كلمة مرور المنصة تُعرض مرة في بريد التسليم، والبريد يقول «لا كلمة مرور عبر البريد أبداً» | نص الخصوصية: «وتُشفَّر كلمات مرور المنصة أثناء التخزين وتُعرض لك مرة واحدة في بريد التسليم.» / “Platform passwords are encrypted at rest and shown to you once in the delivery e-mail.” بينما قالب account_ready موثَّق بـ «no password, ever» ويقول للعميل «سجّل الدخول إلى cTrader بحساب cTrader ID المرتبط ببريدك… لن يطلب منك فريق FPT كلمة المرور أبداً»، ولوحة البيانات لا تعرض صف كلمة مرور. العميل يقرأ وثيقة تناقض ما يراه. | packages/db/scripts/seed-data/legal.ts (privacy bodyAr §6 / bodyEn §6); packages/adapters/src/mail/templates/index.ts:6 («no password, ever»); ar.json:L107 / en.json:L107; apps/client/src/components/credentials-panel.tsx:9-10 | تصحيح بند الخصوصية ليطابق نموذج cTrader ID (البريد هو المعرّف، لا تُرسل كلمة مرور بالبريد) أو حذف الجملة؛ وحذف مفاتيح كلمة المرور الميتة (TXT-059) حتى لا يعود النموذج القديم. | S | brand glossary § 4 «بيانات الدخول إلى cTrader» / fpt-prop-platform F3 § 5 | `packages/db/scripts/seed-data/legal.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### العميل — صفحة الشركاء

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-032 | P2 ثانوي | نص | partners.overrideRow EN «Your sub-partners' sales» / AR «إحالات شركائك» · partners.stats.nextPayout = partners.stats.nextBatch «الدفعة الأسبوعية التالية» | اختلاف معنى بين اللغتين (مبيعات ↔ إحالات) ومفتاحان متطابقان | الصف يعرض مبيعات الشركاء الفرعيين (أساس عمولة الـ 2 %) لا إحالاتهم. ومفتاحا nextPayout وnextBatch يحملان النص نفسه في اللغتين. | ar.json:L1097 / en.json:L1097; ar.json:L1136 / en.json:L1136; ar.json:L1138 / en.json:L1138 | «مبيعات شركائك الفرعيين»؛ وحذف أحد المفتاحين المكرّرين. | S | i18n parity (meaning) | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### العميل — قياس الأداء — Lighthouse (artifacts

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-046 | P3 صقل | تقني | scripts/ux-audit-lighthouse.mjs | تقارير «client-home» و«client-account» و«client-payouts» قاست صفحة تسجيل الدخول (تحويل بلا جلسة) لا الشاشات المسمّاة | `finalDisplayedUrl` لتقرير home هو `/ar/signin?next=%2Far%2Faccounts`؛ أرقام TBT/LCP المنسوبة للوحة والصرف هي أرقام صفحة الدخول. دليل أداء الشاشات المصادَقة غير موجود بعد. | artifacts/ux-audit-F4/lighthouse/client-home.json → finalDisplayedUrl; client-account.json, client-payouts.json (نفس النمط). | مرّر كوكي جلسة QA (من coverage-prod-ops) إلى Lighthouse عبر `extraHeaders: { cookie }` أو شغّله على المكدّس المحلي بعد تسجيل الدخول؛ أعد تسمية التقارير الحالية إلى signin-*. | S | tooling (web-interface-guidelines § Performance evidence) | `scripts/ux-audit-lighthouse.mjs`، `artifacts/ux-audit-F4/lighthouse/` |

### العميل — كل أزرار الإرسال

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-024 | P3 صقل | تقني | packages/i18n client.actions.pleaseWait | تسمية التحميل «يرجى الانتظار» بلا نقاط الحذف خلافاً لنمط الإرشادات والإدارة | `SubmitButton` يستخدم `client.actions.pleaseWait = 'يرجى الانتظار'` بينما الإدارة تستخدم «يرجى الانتظار…». الإرشادات: حالات التحميل تنتهي بـ «…». | packages/i18n/src/messages/ar.json → client.actions.pleaseWait vs admin.result.pending؛ apps/client/src/components/submit-button.tsx:11. | غيّر القيمة إلى «يرجى الانتظار…» (و«Please wait…» في en.json). | S | web-interface-guidelines § Typography | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### العميل — كل الإجراءات الخادمية في (app) — خاصة 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-008 | P2 ثانوي | تقني | apps/client/src/lib/session.ts requireUser + apps/client/src/app/[locale]/(auth)/signin/page.tsx | انتهاء الجلسة (خمول 30 دقيقة) أثناء تعبئة نموذج يُفقد كل ما كُتب بلا أي تفسير، ومفتاح «انتهت جلستك» موجود وغير مستخدم | كل إجراء خادمي يستدعي `requireUser(locale)` الذي ينفّذ `redirect('/signin?next=…')` داخل الإجراء. في نموذج الصرف ذي الأربع خطوات تُحفظ القيم في `useState` فقط؛ عند الضغط على «إرسال» بعد 30 دقيقة من الخمول يُنقل المستخدم إلى تسجيل الدخول، وبعد العودة إلى `/payouts/new` يبدأ من الصفر. صفحة تسجيل الدخول لا تقرأ أي سبب ولا تعرض `auth.errors.sessionExpired` (المفتاح موجود في القاموس وغير مُستخدم في أي ملف). | apps/client/src/lib/session.ts:70-77; apps/client/src/app/[locale]/(app)/payouts/actions.ts:28; apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx:30-34 (حالة محلية فقط)؛ grep `sessionExpired` في apps/client/src → لا نتائج؛ packages/i18n/src/messages/ar.json `auth.errors.sessionExpired`؛ apps/client/src/lib/session-rules.ts:77 (`SESSION_IDLE_MS = 30 min`). | (1) في `requireUser` أضف `&reason=expired` عندما يكون الحكم `idle\|expired\|revoked`، واعرض في صفحة تسجيل الدخول تنبيه `auth.errors.sessionExpired` عند وجوده. (2) للنماذج الطويلة (الصرف، الإغلاق، KYC) احفظ المسودّة في `sessionStorage` بمفتاح الحساب واستعدها بعد العودة. (3) اختياري: إنذار قبل انتهاء الخمول بخمس دقائق من مؤقّت على العميل. | M | web-interface-guidelines § Forms (unsaved changes) + security-audit (session) | `apps/client/src/lib/session.ts`، `apps/client/src/app/[locale]/(auth)/signin/page.tsx`، `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx` |

### العميل — كل الشاشات المسجَّل دخولها (شريط التبويب)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-020 | P3 صقل | وصول | BottomNav badge → <span class="sr-only">{badge}</span> | عدّاد الإشعارات في تبويب «المزيد» يُنطق رقماً مجرّداً بلا سياق | يُقرأ الرابط «المزيد 3» دون كلمة «إشعارات غير مقروءة». (زر الجرس في الرأس صحيح: «الإشعارات (3)».) | packages/ui/src/components/bottom-nav.tsx:520-525; apps/client/src/components/bottom-nav.tsx:24 | مرّر badge كجملة كاملة («3 إشعارات غير مقروءة») أو أضف badgeLabel مترجماً. | S | ui-ux-pro-max `contextual-live-badge-updates` | `packages/ui/src/components/bottom-nav.tsx`، `apps/client/src/components/bottom-nav.tsx` |

### العميل — كل الصفحات (الخطوط)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-014 | P2 ثانوي | أداء | brand/fonts/fonts.css + apps/client/src/app/[locale]/layout.tsx | الخطوط تُكتشف بعد CSS ولا تُحمَّل مسبقاً: أربعة أوزان من IBM Plex Sans Arabic (~280 KB) مع `font-display: swap` → وميض خط وانزياح تخطيط | لا `<link rel="preload" as="font">` في أي تخطيط (grep صفر)، ولا `next/font`. الخط يُطلب فقط بعد تنزيل وتحليل CSS (128 KB). Lighthouse: الأوزان 400/500/600/700 العربية كلها تُنزَّل على صفحة تسجيل الدخول البسيطة (67–72 KB لكل منها)، وصفحة الهبوط تسجّل CLS 0.023 في `section.ui-section` (انزياح نصي نمطي لتبديل الخط). | brand/fonts/fonts.css:13-16 (أربعة أوزان، `font-display:swap`)؛ grep `preload\|next/font` في apps/*/src → لا نتائج؛ artifacts/ux-audit-F4/lighthouse/client-signin.json → total-byte-weight: IBMPlexSansArabic-600/500/700/400 ≈ 282 KB; client-landing.json → layout-shifts `<section class="ui-section">` 0.022. | في تخطيط اللغة أضف لكل لغة `<link rel="preload" href="/_next/static/media/IBMPlexSansArabic-400.*.woff2" as="font" type="font/woff2" crossOrigin="anonymous">` للوزنين 400 و600 فقط (استورد ملفات الخط من حزمة brand لتثبيت الرابط)، واقتصر الأوزان المحمّلة على المستخدمة فعلاً (راجع استخدام 500/700)، وأضف `size-adjust`/`ascent-override` لبديل النظام لتقليل الانزياح. (الخطوط نفسها ملفات العلامة — لا تُعدَّل، التغيير في التخطيط فقط.) | S | web-interface-guidelines § Performance (fonts) + fpt-brand | `apps/client/src/app/[locale]/layout.tsx`، `apps/admin/src/app/[locale]/layout.tsx`، `brand/fonts/fonts.css` |

### العميل — كل الصفحات (مثال 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-013 | P2 ثانوي | أداء | apps/client/src/app/[locale]/layout.tsx NextIntlClientProvider + apps/client/src/i18n/request.ts | كل صفحة تشحن القاموس كاملاً (193 KB عربي) بما فيه نصوص لوحة الموظفين، فيتضخّم HTML وTBT على الهاتف | `getRequestConfig` يعيد `messages[locale]` كاملاً و`NextIntlClientProvider` بلا `messages` يرث الكل إلى العميل. صفحة الهبوط العامة 243,585 بايت وتحوي نصوصاً من فضاء `admin.result` (مثل «محاولات إعادة تحقق فاشلة»). Lighthouse هاتف: صفحة تسجيل الدخول TBT 850 ms وأقصى FID 1,010 ms مع 1.3 ثانية «Unattributable» على الخيط الرئيسي، متوافق مع تحليل حمولة RSC ضخمة. ليس تسريباً أمنياً (لا أسرار) لكنه يكشف تسميات واجهة الإدارة لأي زائر. | curl https://fptraders.app/ar \| wc -c → 243585؛ grep «محاولات إعادة تحقق فاشلة» في HTML الهبوط → 1؛ apps/client/src/i18n/request.ts:9; apps/client/src/app/[locale]/layout.tsx:118; ls -la packages/i18n/src/messages → ar.json 192,936 B; artifacts/ux-audit-F4/lighthouse/client-signin.json (TBT 850 ms, max-potential-fid 1,010 ms, bootup Unattributable 1,355 ms). | مرّر إلى المزوّد فضاءات العميل فقط: `messages={pick(messages, ['client','auth','dashboard','payouts','profile','common','errors',…])}` (next-intl يدعم القاموس الجزئي)، أو قسّم القاموس إلى `client.json`/`admin.json`/`shared.json` في @fpt/i18n وصدّر لكل تطبيق قاموسه. هدف: HTML الهبوط < 90 KB. | M | web-interface-guidelines § Performance | `apps/client/src/i18n/request.ts`، `apps/client/src/app/[locale]/layout.tsx`، `packages/i18n/src/index.ts`، `packages/i18n/src/messages/ar.json` |

### العميل — كل الصفحات

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-015 | P3 صقل | أداء | /brand/* (public) — رؤوس التخزين | صور العلامة تحت `/brand/` تُرسل بـ `max-age=0, must-revalidate` بما فيها بلاطة 45 KB تُحمَّل مسبقاً في كل صفحة | كل تنقّل يعيد التحقق الشرطي من البلاطة والأيقونات (304 بعد رحلة شبكة) في المتصفحات التي لا تشغّل عامل الخدمة (Safari الخاص، المتصفحات المكتبية قبل التسجيل). ملفات الخطوط وCSS تحمل `immutable` سنة. | curl -I https://fptraders.app/brand/fpt-logo-icon-rounded.jpeg → `cache-control: public, max-age=0, must-revalidate`, `content-length: 45636`; curl -I …/app-icon-192.png → نفسه؛ مقابل `/_next/static/media/*.woff2` → `public,max-age=31536000,immutable`. | في `headers()` أضف `{ source: '/brand/:path*', headers: [{ key: 'Cache-Control', value: 'public, max-age=86400, stale-while-revalidate=604800' }] }` (الملفات تتغير مع إصدار العلامة فقط؛ أو `immutable` مع تسمية بالإصدار). | S | web-interface-guidelines § Performance | `apps/client/next.config.ts`، `apps/admin/next.config.ts` |

### العميل — كل المستندات

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TEC-047 | P3 صقل | تقني | packages/core/src/csp.ts — `style-src-attr 'unsafe-inline'` | قرار معلن: `style-src-attr 'unsafe-inline'` يسمح بأي سمة `style=` محقونة لو وُجد حقن HTML (تغطية/إخفاء عناصر، تسريب عبر CSS) — المتجهات الحالية محصورة ومُتحقَّق منها | السماح موثّق في التعليق (أعراض البيانات المُصيَّرة خادمياً: RuleMeter/Gauge/Progress/EquityChart). المصدران الوحيدان لـ `dangerouslySetInnerHTML` هما SVG رمز QR المولَّد خادمياً من مكتبة موثوقة (ملف الموظف والتسجيل)؛ لا `innerHTML` على العميل. يبقى `'unsafe-inline'` أوسع مما تحتاجه الواجهة (قيم عددية لعرض/لون). | packages/core/src/csp.ts:9-12, :58; apps/client/src/app/[locale]/(app)/profile/forms.tsx:107; apps/admin/src/app/[locale]/(auth)/enrol/page.tsx:42; grep `innerHTML\|insertAdjacentHTML` → 0. | أبقِ القرار موثّقاً في security review؛ تحسين اختياري لاحقاً: اضبط العرض عبر `style={{'--w': …}}` ما زال سمة؛ البديل الحقيقي هو ترك العرض الأولي للعميل (CSSOM لا تخضع لـ CSP) مع هيكل SSR بعرض 0 — مقبول للعدادات لا للرسم البياني. لا إجراء إلزامي. | M | security-audit (CSP design decision) | `packages/core/src/csp.ts` |

### العميل — لماذا أُوقف حسابي

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-046 | P2 ثانوي | RTL | dashboard.breach «لماذا أوقف حسابي» ↔ client.screen.breach / breach.title «لماذا أُوقف حسابي» · EN «Why was my account stopped» (بلا ؟) | غياب الضمة يقلب المعنى («لماذا أوقف حسابي؟» = لماذا أوقفَ هو حسابي)، وعنوان استفهامي بلا علامة استفهام في اللغتين | المفتاح نفسه مكتوب بالضمة في مكان وبدونها في آخر؛ والعنوان سؤال بلا «؟» / “?”. | ar.json:L792 / en.json:L792; ar.json:L915 / en.json:L915 | «لماذا أُوقف حسابي؟» / “Why was my account stopped?” في المفاتيح الثلاثة. | S | Arabic punctuation / web-interface-guidelines | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### العميل — مؤشرات التقدم

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-081 | P3 صقل | نص | shop.transfer.uploading «جارٍ الرفع» ↔ common.loading «جارٍ التحميل…» · mockGateway.processing «جارٍ المعالجة…» · admin.result.pending «يرجى الانتظار…» ↔ client.actions.pleaseWait «يرجى الانتظار» | علامة الحذف «…» موجودة في بعض مؤشرات التقدم وغائبة في أخرى |  | ar.json:L621 / en.json:L621; ar.json:L7 / en.json:L7; ar.json:L315 / en.json:L315 | «…» في كل مؤشر تقدم. | S | consistency | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### العميل — مسار طلب الدفعة

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-067 | P3 صقل | نص | payouts.flow.youReceive / payouts.flow.amountLead «تتسلم» ↔ payouts.form.share / payouts.clientShare / payouts.form.amountHint «تحصل على» | receive بكلمتين في الخطوات المتتالية |  | ar.json:L950 / en.json:L950; ar.json:L978 / en.json:L978 | «تحصل على» في الموضعين. | S | consistency | `packages/i18n/src/messages/ar.json` |

### البريد — account_closed

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| MAIL-025 | P2 ثانوي | تقني | footer | سنة حقوق النشر «© 2026» مثبّتة يدوياً في القاموس | mail.common.copyright يحمل «© 2026» كنص ثابت في العربية والإنجليزية؛ سيصبح خاطئاً مع أول يناير 2027 في كل بريد. | packages/i18n/src/messages/ar.json mail.common.copyright · packages/i18n/src/messages/en.json mail.common.copyright · packages/adapters/src/mail/templates/layout.ts foot() | جعل السنة متغيراً {year} يُملأ من تاريخ الإرسال (Dubai) في foot() وtextVersion(). | S | Content & Copy | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `packages/adapters/src/mail/templates/layout.ts` |

### البريد — account_ready

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| MAIL-010 | P2 ثانوي | نص | subject | تذبذب بين «حساب التقييم» في الموضوع و«حساب تحديك» في العنوان | الموضوع: «حساب التقييم FPT-50K-7F3K2 جاهز على cTrader» / «Your evaluation account … is ready»؛ العنوان داخل البريد: «حساب تحديك جاهز على cTrader» / «Your challenge account is ready». مصطلحان لنفس الشيء في الرسالة نفسها؛ المعجم يعتمد «التقييم» للمرحلة و«التحدي» للمنتج المشترى. | artifacts/ux-audit-F4/evidence/mail/account_ready-ar.png · artifacts/ux-audit-F4/evidence/mail/account_ready-en.png · packages/i18n/src/messages/ar.json mail.account_ready.subject/title | توحيد العنوان على «حساب التقييم جاهز على cTrader» / «Your evaluation account is ready on cTrader». | S | Content & Copy / brand glossary §3 | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| MAIL-036 | P3 صقل | بصري | body | كلمة «e-mail» تنكسر عند الواصلة على الهاتف (e-/mail) | على 390 px: «linked to your e-\nmail below» وتسمية «cTrader ID (e-\nmail)» تنكسر داخل الكلمة. | artifacts/ux-audit-F4/evidence/mail/account_ready-en-390.png · artifacts/ux-audit-F4/evidence/mail/verify_code-en-390.png («this e-\nmail») | استخدام واصلة غير قاطعة (U+2011) في «e‑mail» في قاموس البريد الإنجليزي، أو اعتماد «email». | S | Content & Copy | `packages/i18n/src/messages/en.json` |

### البريد — admin

اللقطات: `capture: artifacts/ux-audit-F4/evidence/admin-signin-empty-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-signin-wrong-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-404-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-forbidden-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-accounts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-payouts-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-orders-ar-phone.png`، `capture: artifacts/ux-audit-F4/evidence/admin-audit-ar-phone.png` … (+48)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| MAIL-021 | P2 ثانوي | RTL | subject | موضوع البريد العربي يُعرض مقلوباً في واجهة الأدمن الإنجليزية | صفحة تفاصيل البريد في الأدمن بالإنجليزية تطبع الموضوع العربي داخل عنوان LTR بلا dir=auto فيظهر «cTrader جاهز على FPT-10K-J854H حساب التقييم» بترتيب معكوس غير قابل للقراءة. | artifacts/ux-audit-F4/evidence/admin-mail-detail-en-phone.png (العنوان أعلى الصفحة) · apps/admin/src/app/[locale]/(app)/mail/[id]/page.tsx:29 (`title={m.subject}`) | تمرير dir="auto" وlang لعنوان الصفحة (وفي قائمة البريد) عند عرض موضوع بلغة مخالفة للواجهة. | S | RTL / web-interface-guidelines | `apps/admin/src/app/[locale]/(app)/mail/[id]/page.tsx`، `apps/admin/src/app/[locale]/(app)/mail/page.tsx` |
| MAIL-022 | P2 ثانوي | نص | body | الأدمن يعرض أسماء قوالب F3 السبعة خاماً («manual proof received») | قاموس mailPreview.templates يغطي 13 قالباً فقط؛ ops_alert وmanual_proof_received/confirmed/rejected/reminder وemail_change_notice وaccount_closed تسقط إلى m.template.replaceAll('_',' ') فتظهر إنجليزية خاماً في الواجهة العربية (لقطة الإنتاج: «manual proof received»). | artifacts/real-device/coverage-20261030/local-chromium-1440/admin-admin-mail-detail.png (القالب: manual proof received) · packages/i18n/src/messages/ar.json mailPreview.templates (13 مفتاحاً) · apps/admin/src/app/[locale]/(app)/mail/[id]/page.tsx:25 | إضافة المفاتيح السبعة: «تنبيه تشغيلي»، «استلام إثبات التحويل»، «اعتماد التحويل»، «رفض إثبات التحويل»، «تذكير بإثبات التحويل»، «إشعار تغيير البريد»، «إغلاق الحساب» (والإنجليزية بالمثل). | S | Content & Copy / i18n parity | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### البريد — all

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| MAIL-006 | P1 رئيسي | هوية | footer | عبارة «النص التنظيمي: بانتظار قرار» المؤقتة ظاهرة للعميل في كل بريد | في أسفل كل القوالب العشرين، بالعربية والإنجليزية، يظهر صندوق متقطع الحدود يحمل «النص التنظيمي: بانتظار قرار · Regulatory text: pending decision». هذا نص داخلي (REQUIRED DECISION) لا معنى له للعميل ويوحي بأن الشركة غير مكتملة التنظيم؛ النسخة النصية تحمله أيضاً. التعليق HTML الذي يسبقه كافٍ للتوثيق. | artifacts/ux-audit-F4/evidence/mail/account_ready-ar.png · artifacts/ux-audit-F4/evidence/mail/welcome-en.png · كل الصور · packages/adapters/src/mail/templates/layout.ts foot(): `<p class="mut reg" ...>${t('ar','mail.common.regulatoryPending')} · ...` و textVersion() | إخفاء السطر المرئي في بيئة الإنتاج (إبقاء التعليق HTML فقط) إلى حين تسليم النص القانوني من config/legal.ts؛ أو عرضه فقط عندما يكون NODE_ENV !== 'production'. لا يُخترع نص بديل. | S | fpt-brand (regulatory-matrix: do not invent) / Content & Copy | `packages/adapters/src/mail/templates/layout.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| MAIL-007 | P1 رئيسي | هوية | header | رسائل الإنتاج الصادرة من تطبيقات Next تخرج بلا شعار (نص «Fiper Pro Traders» فقط) | الشعار يُقرأ وقت التحميل من brand/logos/derived عبر import.meta.url (خمسة مستويات لأعلى). لقطة الأدمن على الإنتاج (admin.fptraders.app، بريد manual_proof_received الذي أدرجه تطبيق العميل) تُظهر رأس البريد كنص عادي «Fiper Pro Traders» بدل الـ lockup، بينما العينات المحلية (tsx من المصدر) تُظهر الشعار. أي أن الحزمة المبنية بـ Next لا تجد الملف فتسقط إلى النص. | artifacts/real-device/coverage-20261030/local-chromium-1440/admin-admin-mail-detail.png (رأس نصي) مقابل artifacts/ux-audit-F4/evidence/admin-mail-detail-ar-desktop.png (شعار) · packages/adapters/src/mail/templates/layout.ts readLogo()/LOGO_DIR · apps/client/next.config.ts:16 transpilePackages يشمل @fpt/adapters | توليد وحدة TypeScript تحمل base64 الشعارين وقت البناء (script في brand/ أو packages/adapters) بدل القراءة من القرص وقت التشغيل، وإضافة اختبار عقد يفشل إذا كان LOGO_HEADER_B64 فارغاً في بناء الإنتاج. | M | fpt-brand (logo usage: official asset only) | `packages/adapters/src/mail/templates/layout.ts`، `apps/client/next.config.ts`، `apps/admin/next.config.ts` |
| MAIL-014 | P2 ثانوي | تقني | header | الـ preheader يكرر الموضوع حرفياً في كل القوالب | لا قالب يمرّر preheader، فيسقط التخطيط إلى الموضوع (`input.preheader ?? input.subject`). في صندوق الوارد يظهر الموضوع مرتين متتاليتين (مثلاً «Payout approved: 1,250.00 USD — Payout approved: 1,250.00 USD») وتضيع فرصة إعطاء الحقيقة التالية. | packages/adapters/src/mail/templates/layout.ts layout(): `const preheader = escapeHtml(input.preheader ?? input.subject)` · packages/adapters/src/mail/templates/index.ts renderMail() لا يضبط preheader | إضافة مفتاح mail.<template>.preheader يحمل الحقيقة الثانية (المرجع، الموعد، الخطوة التالية) وتمريره من renderMail؛ مثال: payout_requested → «تُنجَز المراجعة قبل 2 نوفمبر 2026». | S | ui-ux-pro-max §8 e-mail | `packages/adapters/src/mail/templates/index.ts`، `packages/adapters/src/mail/templates/layout.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| MAIL-015 | P2 ثانوي | وصول | footer | التذييل بحجم 11 px دون حدّ F1 (لا شيء مرئي أقل من 12 px) | سطور «جزء من Fiper Global · © 2026 …» والصندوق التنظيمي مكتوبة بـ font-size:11px في كلا اللغتين. docs/design-system.md §2 يفرض 12 px كحدّ أدنى لأي عنصر مرئي؛ بالعربية الحجم الصغير بخط بديل يجعلها غير مقروءة فعلياً على الهاتف. | packages/adapters/src/mail/templates/layout.ts foot(): `font-size:11px` (ثلاث مرات) · artifacts/ux-audit-F4/evidence/mail/account_ready-ar-390.png (التذييل) | رفع التذييل إلى 12–13 px مع line-height 1.6، أو 13 px للسطر العربي. | S | F1 UX floors (docs/design-system.md §2) | `packages/adapters/src/mail/templates/layout.ts` |
| MAIL-016 | P2 ثانوي | وصول | body | الملاحظة وسطر «لماذا وصلتك» أصغر من حدّ النص الأساسي 15 px | الملاحظة (note) بـ 14 px وسطر «وصلتك هذه الرسالة…» بـ 13 px، وكلاهما نص أساسي يحمل معلومة مهمة (كيفية فتح cTrader، عدم طلب كلمات المرور، عنوان الدعم). حدّ F1 للنص الأساسي 15 px. | packages/adapters/src/mail/templates/layout.ts layout(): note `font-size:14px` · why line `font-size:13px` · artifacts/ux-audit-F4/evidence/mail/account_ready-en-390.png | الملاحظة 15 px، وسطر «لماذا» 14 px على الأقل (أو 15 px بلون muted). | S | F1 UX floors | `packages/adapters/src/mail/templates/layout.ts` |
| MAIL-017 | P2 ثانوي | وصول | header | العنوان الرئيسي عنصر <p> لا <h1>؛ لا بنية عناوين دلالية | العنوان الواحد للبريد مكتوب كـ `<p class="txt" style="font-size:24px;font-weight:700">`. قارئات الشاشة في Apple Mail/Gmail لا تستطيع القفز إلى العنوان، والـ title الوحيد هو <title>. | packages/adapters/src/mail/templates/layout.ts layout(): `<p class="txt" style="margin:0;font-size:24px;font-weight:700...">${escapeHtml(input.title)}</p>` | استخدام `<h1 style="margin:0;font-size:24px;...">` (مع إعادة ضبط الهوامش) للعنوان، وإبقاء الباقي فقرات. | S | web-interface-guidelines Accessibility | `packages/adapters/src/mail/templates/layout.ts` |
| MAIL-018 | P2 ثانوي | بصري | button | الزر على عرض الهاتف ليس بعرض كامل (154–182 px) ومحاذى لجهة واحدة | القياس على 390 px: زر account_ready 154×48، breach 163×48، ops_alert 182×48؛ الزر حبّة مدوّرة في الطرف. الهدف باللمس ≥ 44 px محقق، لكن المطلوب في معيار البريد على الهاتف زر بعرض البطاقة ليُلمس بسهولة ويقع في محور القراءة. | قياس Playwright على HTML العينات عند 390 px (a.btn width 154/163/182, height 48) · artifacts/ux-audit-F4/evidence/mail/account_ready-ar-390.png · packages/adapters/src/mail/templates/layout.ts button(): `display:inline-block` | في @media (max-width:420px) اجعل `.btn{display:block !important;width:auto;text-align:center}` وجدول الزر width:100%؛ تبقى حبّة 48 px. | S | ui-ux-pro-max §8 / mobile e-mail | `packages/adapters/src/mail/templates/layout.ts` |
| MAIL-019 | P2 ثانوي | تقني | button | روابط المحرّك بلا بادئة لغة فيُحوَّل المستخدم الإنجليزي إلى الواجهة العربية | paths في services/engine/src/comms.ts تبني `/accounts/<id>`، `/c/<code>`، `/payouts` بلا /en أو /ar، بينما توجيه العميل localePrefix:'always' يختار اللغة من الكوكي أو لغة المتصفح لا من لغة الرسالة. مستخدم ضبط لغته إنجليزية في ملفه وفتح الرابط من جهاز آخر يصل إلى /ar/… | services/engine/src/comms.ts:14-26 (paths, urlOf) · apps/client/src/i18n/routing.ts:5 (localePrefix:'always') · مقارنة: apps/client/src/lib/webhooks.ts:104 يبني `/${locale}/payouts` | إدراج locale في urlOf (`${origin}/${locale}${path}`) في كل مراسلات المحرّك (breach, passed, payout_*, inactivity, expiry, account_ready). | S | Content & Copy / i18n routing | `services/engine/src/comms.ts`، `services/engine/src/ending.ts`، `services/engine/src/payouts.ts` |
| MAIL-028 | P2 ثانوي | RTL | footer | اسم «Fiper Global» ينكسر على سطرين في سطر الحقوق العربي على الهاتف | على 390 px سطر «© 2026 Fiper Pro Traders — شركة تابعة لـ Fiper Global. جميع الحقوق محفوظة.» ينقسم إلى «… شركة تابعة لـ Fiper» ثم «Global. جميع الحقوق محفوظة.» فيبدو اسم الشركة الأم مقطوعاً، في كل القوالب. | artifacts/ux-audit-F4/evidence/mail/account_ready-ar-390.png · artifacts/ux-audit-F4/evidence/mail/verify_code-en-390.png (السطر العربي) · packages/adapters/src/mail/templates/layout.ts foot() | لفّ أسماء العلامات (Fiper Global، Fiper Pro Traders) في span بـ white-space:nowrap داخل التذييل، أو فصل سطر العلاقة عن سطر الحقوق في فقرتين. | S | fpt-brand (name handling: Latin name kept whole) | `packages/adapters/src/mail/templates/layout.ts` |
| MAIL-030 | P2 ثانوي | وصول | footer | لا ترويسة List-Unsubscribe ولا تفضيلات للرسائل غير الحرجة | كل الرسائل تُرسل بالترويسات نفسها؛ لا List-Unsubscribe/List-Unsubscribe-Post في live.ts. affiliate_commission وinactivity_warning وexpiry_warning رسائل خدمة لكنها متكررة، وGmail/Yahoo يشترطان ترويسة إلغاء الاشتراك للمرسلين بالجملة (أكثر من 5000/يوم) ويخفّضان السمعة دونها. | packages/adapters/src/mail/live.ts (buildSendRequest بلا headers) · grep -i unsubscribe packages/adapters/src/mail → لا نتيجة | إضافة List-Unsubscribe (mailto + https) وList-Unsubscribe-Post: One-Click لرسائل الشراكة والتذكيرات، مع صفحة تفضيلات في الملف الشخصي؛ إبقاء الرسائل الحرجة (رمز، تجاوز، صرف) بلا إلغاء. | M | e-mail deliverability (RFC 8058) | `packages/adapters/src/mail/live.ts`، `packages/adapters/src/mail/templates/layout.ts` |
| MAIL-034 | P3 صقل | نص | text-version | النسخة النصية تكرر الفعل: «افتح: افتح لوحة التحكم» / «Open: Open the dashboard» | textVersion يسبق كل زر بـ mail.common.open = «افتح: {label}» بينما أغلب التسميات تبدأ بفعل، فتُقرأ «افتح: افتح لوحة التحكم»، «Open: Upload the transfer proof». | artifacts/ux-audit-F4/evidence/mail/account_ready-ar.txt:47 · artifacts/ux-audit-F4/evidence/mail/manual_payment_reminder-en.txt · packages/adapters/src/mail/templates/layout.ts textVersion() | طباعة التسمية ثم الرابط في السطر التالي بلا بادئة، أو بادئة محايدة «← {label}». | S | Content & Copy | `packages/adapters/src/mail/templates/layout.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| MAIL-041 | P3 صقل | نص | text-version | النسخة النصية تحمل الصندوق التنظيمي المؤقت ورأساً نصياً مكرراً | textVersion يطبع «Fiper Pro Traders» ثم العنوان، وينتهي بالسطرين التنظيميين المؤقتين بالعربية والإنجليزية معاً في كل بريد (نفس MAIL-006 في النص). | artifacts/ux-audit-F4/evidence/mail/welcome-en.txt · packages/adapters/src/mail/templates/layout.ts textVersion() | تطبيق قرار MAIL-006 على النسخة النصية؛ تبقى الحقائق والرابط تحت تسمية الزر. | S | Content & Copy | `packages/adapters/src/mail/templates/layout.ts` |
| MAIL-043 | P3 صقل | هوية | header | الوضع الداكن مُتحقق منه بمحاكاة Chromium فقط؛ Gmail يقلب الألوان بلا media query | الـ HTML يعلن color-scheme light dark ويحمل قواعد prefers-color-scheme و[data-ogsc]، وقد تحقق الفاحص أن المحاكاة تُظهر البلاطة والنص الأبيض على #0A0A0A. لكن Gmail (Android/iOS) يتجاهل media queries ويعكس الألوان آلياً، فيبقى الـ lockup (مربع أبيض بكلمة سوداء) ويصبح الزر الأحمر غامقاً. TEST-MATRIX يوثّق أن العملاء الحقيقيين غير مختبرين. | لقطات الوضع الداكن (scratchpad) · packages/adapters/src/mail/templates/layout.ts <style> dark rules · brand/templates/email/TEST-MATRIX.md | اختبار على Gmail Android/iOS وOutlook عبر Litmus/Email on Acid أو حساب حقيقي قبل الإطلاق؛ ضبط شفافية خلفية الـ lockup (PNG بخلفية شفافة) لتقليل أثر القلب. | M | fpt-brand (TEST-MATRIX) | `packages/adapters/src/mail/templates/layout.ts` |

### البريد — breach

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| MAIL-008 | P1 رئيسي | نص | subject | عنوان «تجاوز الحد — أُوقف التداول» يُستخدم أيضاً لانتهاء المدة وعدم النشاط | القالب الواحد يخدم كل أنواع الإنهاء (daily_loss, max_loss, expired, inactive, manual) بعنوان وموضوع ثابتين «تجاوز الحد — أُوقف التداول» / «limit breached — trading stopped» وبحالة «تجاوز الحد». عند انتهاء 31 يوماً أو قرار مراجعة يدوية لم يُتجاوز أي حد، فالعنوان حقيقة غير دقيقة تصل العميل. | packages/adapters/src/mail/templates/index.ts case 'breach' (الحالة S('breached') ثابتة لكل الأنواع) · packages/i18n/src/messages/ar.json mail.breach.subject/title · artifacts/ux-audit-F4/evidence/mail/breach-ar.png | تفريع العنوان والحالة حسب kind: للخسارتين «تجاوز الحد — أُوقف التداول»؛ لـ expired «انتهت مدة التقييم (31 يوماً)»؛ لـ inactive «أُغلق الحساب لعدم النشاط»؛ لـ manual «أُنهي التقييم بقرار مراجعة». الموضوع بالمثل. | M | Content & Copy / fpt-prop-platform rulebook | `packages/adapters/src/mail/templates/index.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| MAIL-031 | P2 ثانوي | بصري | body | فراغ مزدوج ظاهر بين الرقم و«%» داخل الوحدات (5  %، 80  %) | الوحدة «5 %» تُعرض بخط mono حيث المسافة أعرض، فتبدو كمسافتين: «daily drawdown 5  % of the day's» و«حصة المتداول 80  %». يظهر في breach وpayout_paid في اللغتين. | artifacts/ux-audit-F4/evidence/mail/breach-en-390.png · artifacts/ux-audit-F4/evidence/mail/payout_paid-en-390.png · artifacts/ux-audit-F4/evidence/mail/breach-ar.png · packages/adapters/src/mail/templates/layout.ts withUnits (`(?: ?(?:%\|USD))?`) | استبدال المسافة قبل % وUSD داخل الوحدة بمسافة رفيعة غير قاطعة (U+202F) أو إزالة المسافة قبل % مع إبقاء الوحدة LTR. | S | fpt-brand formats | `packages/adapters/src/mail/templates/layout.ts` |
| MAIL-044 | P3 صقل | نص | body | تسمية «مرجع الحساب» في الصف الأول تلتف على سطرين على الهاتف الإنجليزي | «Account reference» تلتف إلى «Account / reference» على 390 px لأن عمود التسمية ضيّق بجانب القيمة mono. مقروء لكنه يكسر إيقاع البطاقة. | artifacts/ux-audit-F4/evidence/mail/breach-en-390.png · artifacts/ux-audit-F4/evidence/mail/payout_paid-en-390.png («Payout / method») | على الهاتف اجعل الصف عمودياً (التسمية فوق القيمة) عبر display:block للخليتين تحت 420 px. | S | ui-ux-pro-max §8 mobile | `packages/adapters/src/mail/templates/layout.ts` |

### البريد — email_change_notice

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| MAIL-039 | P3 صقل | نص | subject | موضوع إنجليزي بصيغة مبنية للمجهول وغير محدد: «A change of your FPT e-mail was requested» | الموضوع لا يقول للمستلم القديم ما المطلوب منه؛ العربية «طُلب تغيير بريدك في FPT» أوضح قليلاً. الرسالة أمنية فيُفضّل الفعل المباشر. | artifacts/ux-audit-F4/evidence/mail/index.json → email_change_notice · packages/i18n/src/messages/en.json mail.email_change_notice.subject | «Your FPT e-mail is about to change — was this you?» / «هل طلبت تغيير بريدك في FPT؟». | S | Content & Copy | `packages/i18n/src/messages/en.json`، `packages/i18n/src/messages/ar.json` |

### البريد — expiry_warning

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| MAIL-020 | P2 ثانوي | نص | button | تحذير انتهاء المدة بلا زر رغم أنه يطلب فعلاً من المتداول | expiry_warning («3 أيام متبقية — الأهداف غير المحققة تُنهي التقييم») هو أكثر الرسائل حاجة إلى فعل، لكنه القالب الوحيد من التحذيرات بلا زر (inactivity_warning يحمل «افتح لوحة التحكم»). النسخة النصية بلا رابط كذلك. | artifacts/ux-audit-F4/evidence/mail/expiry_warning-en-390.png · packages/adapters/src/mail/templates/index.ts case 'expiry_warning' (لا primary) · MailVariables.expiry_warning بلا dashboardUrl | إضافة dashboardUrl إلى متغيرات expiry_warning وزر «افتح الحساب» / «Open the account». | S | ui-ux-pro-max §8 (one message, one action) | `packages/adapters/src/mail/templates/index.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `services/engine/src/ending.ts` |

### البريد — inactivity_warning

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| MAIL-045 | P3 صقل | نص | body | «مؤخراً» غامضة؛ لا تذكر عدد أيام عدم النشاط الذي تحسبه القاعدة | الفقرة: «لم تُنفَّذ أي صفقة على الحساب … مؤخراً. نفّذ صفقة واحدة على الأقل خلال 5 أيام». لا يعرف المتداول منذ متى يُعدّ غير نشط ولا ما القاعدة (المدة الكاملة). | artifacts/ux-audit-F4/evidence/mail/inactivity_warning-ar-390.png · packages/i18n/src/messages/ar.json mail.inactivity_warning.body | «لم تُنفَّذ أي صفقة على الحساب منذ {lastTradeDate}. قاعدة عدم النشاط {inactiveDays} يوماً؛ تبقّى {daysLeft}.» مع تمرير التاريخ من المحرّك. | S | Content & Copy / rulebook | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `packages/adapters/src/mail/templates/index.ts` |

### البريد — kyc_result

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| MAIL-003 | P1 رئيسي | نص | body | سبب رفض التحقق يصل العميل كرمز/جملة خام من المزوّد | صف «السبب» يعرض القيمة كما وردت: في العينة «document_unreadable»، وفي الإنتاج مسار الويب هوك يمرّر cb.declineReason مباشرة (جملة إنجليزية من Shufti Pro مثل «Face is not matching with the document» أو اسم الحدث الخام) إلى بريد عربي. تسريب تقني وغير مترجم في بريد للعميل. | artifacts/ux-audit-F4/evidence/mail/kyc_result-ar.png (السبب: document_unreadable) · artifacts/ux-audit-F4/evidence/mail/kyc_result-en.png · apps/client/src/lib/webhooks.ts:105 (`reason: cb.declineReason ?? undefined`) · packages/adapters/src/kyc/shuftipro/contract.ts:159 | ترجمة declined_codes إلى نصوص مفاتيح i18n (kyc.reasons.<code>) في كلا اللغتين، والاكتفاء بـ noReason عندما لا يوجد رمز معروف؛ عدم تمرير الجملة الخام للمزوّد أبداً إلى بريد العميل. | M | web-interface-guidelines Content & Copy / ui-ux-pro-max §8 | `apps/client/src/lib/webhooks.ts`، `packages/adapters/src/mail/templates/index.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### البريد — manual_proof_received

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| MAIL-009 | P2 ثانوي | نص | body | تسمية «مرجع الحساب» تُستخدم لمرجع الطلب في قوالب التحويل اليدوي الأربعة | في manual_proof_received وmanual_payment_confirmed وmanual_proof_rejected وmanual_payment_reminder الصف الأول يحمل L('reference') = «مرجع الحساب» / «Account reference» بينما القيمة FPT-CC7B3D73 هي مرجع الطلب الذي كتبه العميل على التحويل (الفقرة نفسها تقول «للطلب»). تسمية مضلّلة قبل أن يوجد حساب أصلاً. | artifacts/ux-audit-F4/evidence/mail/manual_proof_received-ar.png · artifacts/ux-audit-F4/evidence/mail/manual_payment_reminder-en-390.png · packages/adapters/src/mail/templates/index.ts cases manual_* (`L('reference')`) | إضافة مفتاح mail.common.labels.orderReference = «مرجع الطلب» / «Order reference» واستخدامه في القوالب الأربعة. | S | Content & Copy | `packages/adapters/src/mail/templates/index.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### البريد — manual_proof_rejected

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| MAIL-027 | P2 ثانوي | نص | body | ملاحظة الموظف الحرة تُلحق بسبب الرفض بلغة الموظف لا لغة العميل | في الإنتاج يُبنى السبب كـ `${tr(locale, shop.transfer.reasons.<code>)} — ${reason}` حيث reason نص حر يكتبه الموظف (3–500 حرف) غالباً بلغة واحدة؛ يصل العميل الإنجليزي بالعربية والعكس. في العينة يظهر الرمز الخام amount_mismatch (عينة غير ممثلة). | apps/admin/src/lib/actions/payments.ts:103-105 · artifacts/ux-audit-F4/evidence/mail/manual_proof_rejected-ar.png | جعل الملاحظة الحرة اختيارية في البريد (تُعرض في الأدمن فقط) والاكتفاء بالسبب المترجم + ملاحظة القالب؛ أو إلزام الموظف بحقلين ar/en عند الرفض. | M | Content & Copy | `apps/admin/src/lib/actions/payments.ts` |

### البريد — ops_alert

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| MAIL-005 | P1 رئيسي | RTL | body | نص التنبيه الإنجليزي يُعرض داخل فقرة RTL فتنتقل النقطة إلى بداية السطر | عنوان التنبيه ونصه يأتيان من المحرّك (في العينة إنجليزيان: «Engine behind» / «The engine has not advanced for 15 minutes.») لكن الحاوية تحمل dir=rtl، فتظهر الجملة «.The engine has not advanced for 15 minutes» بالنقطة في أقصى اليسار ومحاذاة يمين. في الإنتاج يحدث هذا كلما غاب alert.text[locale] (السقوط إلى title/body الخام). | artifacts/ux-audit-F4/evidence/mail/ops_alert-ar.png · artifacts/ux-audit-F4/evidence/mail/ops_alert-ar-390.png · services/engine/src/alerts.ts:114 (`alert.text?.[locale] ?? { title: alert.title, body: alert.body }`) · packages/adapters/src/mail/templates/layout.ts layout(): `<div lang dir=...>` | للعنوان والفقرات التي تأتي من متغيرات غير مترجمة ضع dir="auto" (أو احسب الاتجاه من أول حرف قوي) مع محاذاة start؛ والأفضل ضمان أن كل تنبيه يحمل text.ar وtext.en في المحرّك. | S | fpt-brand mail rules / RTL | `packages/adapters/src/mail/templates/layout.ts`، `services/engine/src/alerts.ts` |
| MAIL-033 | P2 ثانوي | نص | footer | سطر «لماذا وصلتك» الموجّه للعملاء يظهر في تنبيه الموظفين | ops_alert يحمل السطر العام «وصلتك هذه الرسالة لأن حساباً أو دعوة في FPT مرتبطة بهذا العنوان. لا نطلب كلمات المرور…» وعنوان الدعم العام، وهو غير مناسب لرسالة تشغيلية داخلية. | artifacts/ux-audit-F4/evidence/mail/ops_alert-en.png · packages/adapters/src/mail/templates/layout.ts whyLine() ثابت لكل القوالب | سطر مخصّص للموظفين: «وصلك هذا التنبيه لأنك عضو نشط في فريق FPT؛ أوقف التنبيهات من صفحة صحة النظام.» | S | Content & Copy | `packages/adapters/src/mail/templates/layout.ts`، `packages/adapters/src/mail/templates/index.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### البريد — passed

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| MAIL-011 | P2 ثانوي | نص | body | «حدود السحب» تعني هنا الخسارة (drawdown) بينما «السحب» في بقية الرسائل يعني صرف الأرباح | نص الاجتياز: «حقّق الحساب هدف الربح مع الالتزام بحدود السحب وعدد أيام التداول». في payout_* تُستخدم «طلب السحب» بمعنى payout. القارئ العربي يقرأ «حدود السحب» كحدود صرف الأرباح. المعجم يستخدم «الخسارة اليومية القصوى / الخسارة الإجمالية القصوى». | artifacts/ux-audit-F4/evidence/mail/passed-ar.png · packages/i18n/src/messages/ar.json mail.passed.body · brand/glossary.md §3 | «مع الالتزام بحدود الخسارة اليومية والإجمالية والحد الأدنى لأيام التداول». | S | Content & Copy / brand glossary | `packages/i18n/src/messages/ar.json` |
| MAIL-013 | P2 ثانوي | نص | subject | «Congratulations:» / «تهانينا:» في موضوع الاجتياز كلمة مصنّفة كمحفّز بريد مزعج | موضوع passed يبدأ بـ «Congratulations: account … passed the evaluation». «Congratulations» من أكثر الكلمات التي ترفع درجة السبام في مرشّحات البريد، وهي لا تضيف حقيقة؛ المرجع والحدث كافيان. | artifacts/ux-audit-F4/evidence/mail/index.json → passed · packages/i18n/src/messages/en.json mail.passed.subject | «Account FPT-50K-7F3K2 passed the evaluation — next: identity verification» / «اجتاز الحساب FPT-50K-7F3K2 التقييم — الخطوة التالية: التحقق من الهوية». | S | web-interface-guidelines Content & Copy | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### البريد — payout_approved

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| MAIL-042 | P3 صقل | نص | subject | موضوعات السحب تحمل المبلغ بلا مرجع الحساب، وموضوعات التحويل اليدوي تحمل المرجع بلا مبلغ | «تمت الموافقة على السحب: 1,250.00 USD» بلا FPT-100K-5L78C؛ «اعتُمد تحويلك: FPT-CC7B3D73» بلا 280.00 USD. المستخدم بعدة حسابات/طلبات لا يميّز من الموضوع. | artifacts/ux-audit-F4/evidence/mail/index.json (subjects) | «تمت الموافقة على السحب 1,250.00 USD — الحساب FPT-100K-5L78C» و«اعتُمد تحويلك 280.00 USD للطلب FPT-CC7B3D73». | S | Content & Copy | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### البريد — payout_paid

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| MAIL-001 | P1 رئيسي | RTL | body | نسبة الأرباح تظهر «20/80» بدل «80/20» في النسخة العربية | في فقرة بريد «تمت معالجة دفعتك» تُكتب الحقيقة المنتجية «نسبة الأرباح 80/20 ثابتة»، لكن withUnits يلفّ «80» و«20» كوحدتين منفصلتين (الشرطة المائلة ليست ضمن صنف الأرقام في التعبير النمطي)، فيعيد محرك bidi ترتيبهما في السياق العربي ويظهر للقارئ «20/80». الحقيقة المنتجية (80 % للمتداول) تُعرض معكوسة. | artifacts/ux-audit-F4/evidence/mail/payout_paid-ar.png (السطر الأول من الفقرة: «نسبة الأرباح 20/80 ثابتة») · packages/adapters/src/mail/templates/layout.ts: withUnits regex `[+−]?\d(?:[\d,.:]*\d)?` · packages/i18n/src/messages/ar.json mail.payout_paid.body | إمّا إضافة «/» إلى صنف الأرقام في withUnits ليصبح «80/20» وحدة واحدة معزولة LTR، أو إعادة صياغة النص العربي: «حصة المتداول 80 % ثابتة» وحذف الصيغة بالشرطة. وأضف اختبار عقد يفحص أن «80/20» لا تُقسم. | S | fpt-brand mail rules (unbreakable units) / finance-semantics | `packages/adapters/src/mail/templates/layout.ts`، `packages/i18n/src/messages/ar.json` |
| MAIL-004 | P1 رئيسي | نص | body | طريقة الدفع تُطبع بالرمز الخام «crypto» في العنوان والبطاقة | القالب يطبع x.channel كما هو؛ محرك الصرف يمرّر p.channel (crypto \| wire \| fiper_card \| fiper_p2p). النتيجة في البريد العربي «أُرسل إليك مبلغ 1,250.00 USD عبر crypto» و«طريقة الدفع: crypto»، رغم وجود تسميات جاهزة في القاموس (payoutsAdmin.channel.crypto = «عملة رقمية (USDT TRC20)»). | artifacts/ux-audit-F4/evidence/mail/payout_paid-ar.png · artifacts/ux-audit-F4/evidence/mail/payout_paid-en-390.png · services/engine/src/payouts.ts:348 (`channel: p.channel`) · packages/adapters/src/mail/templates/index.ts case 'payout_paid' · packages/i18n/src/messages/ar.json payoutsAdmin.channel | ترجمة القناة داخل القالب: `t(locale, "mail.common.channels." + channel)` بقيم «USDT (TRC20)» / «تحويل بنكي» / «Fiper Card» / «Fiper P2P» مع سقوط إلى القيمة الخام عند قناة مجهولة. | S | fpt-brand mail rules / Content & Copy | `packages/adapters/src/mail/templates/index.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `services/engine/src/payouts.ts` |
| MAIL-012 | P2 ثانوي | نص | subject | ثلاثة مصطلحات لمفهوم الصرف الواحد: سحب / دفعة / صرف | الموضوعات: «تم استلام طلب السحب»، «تمت الموافقة على السحب»، ثم «تمت معالجة دفعتك»، والحالة «تم الصرف»، والمعجم «دورة الصرف». تنوّع يُضعف التعرّف على سلسلة الرسائل الثلاث لنفس الطلب. | artifacts/ux-audit-F4/evidence/mail/index.json (subjects) · packages/i18n/src/messages/ar.json mail.payout_requested/approved/paid · mail.common.states.processed | اعتماد «السحب» للطلب و«الصرف» للتنفيذ باتساق: «تم استلام طلب السحب: 1,250.00 USD» → «تمت الموافقة على طلب السحب» → «تم صرف سحبك: 1,250.00 USD»؛ العنوان الداخلي «تم صرف المبلغ». | S | Content & Copy | `packages/i18n/src/messages/ar.json` |
| MAIL-024 | P2 ثانوي | نص | body | «شهادة السحب الخاصة بك تحت الزر» صياغة غير طبيعية بالعربية | الجملة تشير إلى موضع الزر لا إلى الفعل. كذلك «نسبة الأرباح 80/20 ثابتة» (التي تنقلب بصرياً، انظر MAIL-001). | artifacts/ux-audit-F4/evidence/mail/payout_paid-ar.png · packages/i18n/src/messages/ar.json mail.payout_paid.body | «أُرسل إليك مبلغ {amount} عبر {channel} بحصة المتداول 80 % الثابتة. يمكنك تنزيل شهادة الصرف من الزر أدناه.» | S | Content & Copy | `packages/i18n/src/messages/ar.json` |

### البريد — payout_requested

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| MAIL-002 | P1 رئيسي | RTL | body | مرجع الحساب ينكسر على سطرين داخل الفقرة العربية على الهاتف | المراجع مثل FPT-100K-5L78C وFPT-CC7B3D73 تُترك عمداً خارج الوحدات في withUnits (تعليق الدالة: «leaves references untouched»)، فعند عرض 390 px ينكسر المرجع عند الشرطة: «-FPT-100K» في نهاية السطر و«5L78C» في بداية السطر التالي، وبترتيب bidi يصبح غير قابل للقراءة/النسخ الصحيح. تكرر الأمر في manual_payment_confirmed («-FPT» / «CC7B3D73»). | artifacts/ux-audit-F4/evidence/mail/payout_requested-ar-390.png · artifacts/ux-audit-F4/evidence/mail/manual_payment_confirmed-ar-390.png · packages/adapters/src/mail/templates/layout.ts withUnits (تعليق «leaves references such as FPT-7K2M9Q untouched») | لفّ أي رمز مرجع بنمط ^FPT-[A-Z0-9-]+$ (وكذلك رقم الحساب والخادم) في الفقرات بوحدة .fpt-unit (nowrap + direction:ltr + unicode-bidi:isolate)، مثلما يُفعل في بطاقة الحقائق. بديل: تمرير المرجع من القالب داخل span جاهز بدل النص الخام. | S | fpt-brand mail rules (unbreakable units) | `packages/adapters/src/mail/templates/layout.ts`، `packages/adapters/src/mail/templates/index.ts` |
| MAIL-023 | P2 ثانوي | نص | body | صياغة إنجليزية ركيكة: «The review is completed by …; the outcome reaches you by e-mail» | الجملة بصيغة المضارع البسيط توحي بأن المراجعة تمت بالفعل. وكذلك payout_paid «your payout certificate is under the button» وbreach «crossed a rule of the programme» وaffiliate «becomes available for withdrawal». | artifacts/ux-audit-F4/evidence/mail/payout_requested-en.png · artifacts/ux-audit-F4/evidence/mail/payout_paid-en-390.png · packages/i18n/src/messages/en.json mail.payout_requested.body, mail.payout_paid.body, mail.breach.body | «The review will be completed by 2 Nov 2026, 01:13 GMT+4; we will e-mail you the outcome.» · «Your payout certificate is available from the button below.» · «Account … breached a rule of the programme: …». | S | Content & Copy | `packages/i18n/src/messages/en.json` |
| MAIL-035 | P3 صقل | هوية | body | صيغة المبلغ الإنجليزية «1,250.00 USD» تخالف صيغة الدليل للإنجليزية «USD 1,250.00» | brand/README.md §Formats: amounts «55,312.50 USD» AR · «USD 55,312.50» أو «$55,312.50» EN. القوالب تستخدم الرمز بعد المبلغ في اللغتين (format.ts يستند إلى README §D). تضارب توثيقي يحتاج قراراً. | brand/README.md:157 · packages/adapters/src/mail/templates/format.ts money() · artifacts/ux-audit-F4/evidence/mail/payout_requested-en.png | حسم الصيغة الإنجليزية في README (PROPOSED: «1,250.00 USD» في البريد لتوحيد الوحدات مع التطبيق) وتوثيق الاستثناء، أو تطبيق «USD 1,250.00» في EN. | S | fpt-brand formats (value-sources) | `packages/adapters/src/mail/templates/format.ts`، `brand/README.md` |
| MAIL-038 | P3 صقل | بصري | body | قيمة «تُنجَز المراجعة قبل» تلتف بترك «(GMT+4)» وحيداً في سطر | في بطاقة الحقائق على 390 px القيمة «2 نوفمبر 2026، 01:13 بتوقيت دبي (GMT+4)» تلتف إلى سطرين ويبقى «(GMT+4)» في سطر منفصل محاذى يساراً، كما ينكسر التاريخ في الفقرة بين «2026،» و«01:13». | artifacts/ux-audit-F4/evidence/mail/payout_requested-ar-390.png · artifacts/ux-audit-F4/evidence/mail/ops_alert-ar-390.png | جعل «بتوقيت دبي (GMT+4)» وحدة غير قاطعة مع الوقت (nowrap)، أو عرض التاريخ والوقت في صفين على الهاتف. | S | fpt-brand formats | `packages/adapters/src/mail/templates/layout.ts`، `packages/adapters/src/mail/templates/format.ts` |

### البريد — staff_invite

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| MAIL-032 | P2 ثانوي | نص | body | تكرار «48 ساعة» في صف البطاقة وفي الملاحظة | بطاقة الحقائق تحمل صفاً واحداً «صلاحية الدعوة: 48 ساعة»، والملاحظة تحته تقول «تُستخدم الدعوة مرة واحدة وتنتهي صلاحيتها بعد 48 ساعة». بطاقة بصف واحد مكرّر لا تضيف شيئاً. | artifacts/ux-audit-F4/evidence/mail/staff_invite-ar.png · artifacts/ux-audit-F4/evidence/mail/staff_invite-en.png · packages/adapters/src/mail/templates/index.ts case 'staff_invite' | إزالة البطاقة والاكتفاء بالملاحظة، أو جعل البطاقة تحمل الدور المدعو إليه والمُرسِل (اسم الموظف الذي دعا) وتاريخ انتهاء الدعوة بتوقيت دبي. | S | ui-ux-pro-max §8 | `packages/adapters/src/mail/templates/index.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### البريد — verify_code

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| MAIL-029 | P2 ثانوي | نص | subject | رمز التحقق يظهر في الموضوع وفي الـ preheader معاً | الموضوع «رمز التحقق الخاص بك في FPT: 482913» والـ preheader يكرره؛ الرمز يظهر مرتين في معاينة الإشعار على شاشة القفل. وضع الرمز في الموضوع مقبول، لكن تكراره يزيد انكشافه بلا فائدة. | artifacts/ux-audit-F4/evidence/mail/index.json → verify_code · packages/adapters/src/mail/templates/layout.ts preheader default | preheader خاص بـ verify_code: «صالح 10 دقائق ويُستخدم مرة واحدة» / «Valid for 10 minutes, single use». | S | ui-ux-pro-max §8 | `packages/adapters/src/mail/templates/index.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| MAIL-037 | P3 صقل | تقني | body | لون رمز التحقق غير محدد صراحة (يعتمد على لون المتصفح الافتراضي) | خلية الرمز 28 px لا تحمل class=txt ولا color؛ في الوضع الفاتح يُعرض بأسود المتصفح (#000 لا #120D0E)، وفي الوضع الداكن يعتمد على color-scheme للعميل. عملاء لا يحترمون color-scheme (Gmail iOS) قد يعرضونه أسود على بطاقة داكنة. | packages/adapters/src/mail/templates/layout.ts factsCard(): `unit(escapeHtml(input.code), ';font-size:28px;...')` بلا color · لقطة الوضع الداكن (Chromium) سليمة | إضافة class="txt" وstyle color:#120D0E لخلية الرمز ليتبع قواعد الوضع الداكن مثل بقية النصوص. | S | fpt-brand tokens | `packages/adapters/src/mail/templates/layout.ts` |

### البريد — welcome

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| MAIL-026 | P2 ثانوي | هوية | header | الخط العربي للهوية غير مضمّن؛ العرض الفعلي يسقط إلى خط النظام | لا @font-face ولا رابط خط في البريد؛ العائلة 'IBM Plex Sans Arabic','Segoe UI',Tahoma,Arial. في كل لقطات العينات العربية ظهر خط نسخي بديل (لا Plex)، وهذا ما سيراه معظم المستلمين على الهاتف (iOS Mail، Gmail) أيضاً. الهوية العربية للبريد غير محققة عملياً وإن كانت مقبولة بمصفوفة الاختبار كسقوط. | artifacts/ux-audit-F4/evidence/mail/welcome-ar.png وartifacts/ux-audit-F4/evidence/mail/account_ready-ar.png (الخط العربي) · packages/adapters/src/mail/templates/layout.ts FONT_CSS · brand/templates/email/TEST-MATRIX.md | إضافة @font-face لـ IBM Plex Sans Arabic (woff2 من brand/fonts) داخل <style> للعملاء الداعمين (Apple Mail، iOS Mail، Outlook Mac، Thunderbird) مع إبقاء السقوط للباقي؛ أو توثيق القرار صراحة كـ PROPOSED. | M | fpt-brand (typography) | `packages/adapters/src/mail/templates/layout.ts` |
| MAIL-040 | P3 صقل | نص | subject | اسم عربي داخل موضوع وجملة إنجليزية («Welcome to FPT, خالد» / «خالد, your FPT account is ready») | عند اختيار الإنجليزية مع اسم مخزّن بالعربية يبدأ البريد الإنجليزي بحرف عربي وتظهر الفاصلة في موضع bidi غير مريح. العينة تبرز الحالة الواقعية لمستخدمي الخليج. | artifacts/ux-audit-F4/evidence/mail/welcome-en.png · artifacts/ux-audit-F4/evidence/mail/staff_invite-en.png | إسقاط الاسم من الموضوع الإنجليزي عندما يكون غير لاتيني، وفي الفقرة وضعه داخل span dir=auto؛ أو طلب الاسم اللاتيني في الملف. | S | RTL / Content & Copy | `packages/adapters/src/mail/templates/index.ts`، `packages/i18n/src/messages/en.json` |

### البريد — الأيام المتبقية

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-058 | P3 صقل | نص | format.ts days() (k = 0 → «0 يوماً») · mail.expiry_warning.subject «{daysLeft} متبقية» · mail.expiry_warning.body «يتبقى للحساب {reference} {daysLeft}» | «يوم واحد متبقية» و«يومان متبقية» (تأنيث مع مذكّر) و«0 يوماً» | days() تعيد «يوم واحد/يومان» (مذكّر) والموضوع يلحقها بصفة مؤنثة؛ الصفر يعطي «0 يوماً». | packages/adapters/src/mail/templates/format.ts:66-73; ar.json:L155 / en.json:L155 | «الحساب {reference}: تبقّى {daysLeft} في التقييم» (بلا صفة)، وفي days(): k = 0 → «لا أيام متبقية» أو «0 أيام». | S | Arabic grammar | `packages/adapters/src/mail/templates/format.ts`، `packages/i18n/src/messages/ar.json` |

### البريد — بريد اجتياز التقييم

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-007 | P1 رئيسي | نص | mail.passed.body | البريد يعد بخطوة «اتفاقية الحساب الممول» غير موجودة في المنتج | «الخطوة التالية هي التحقق من الهوية واتفاقية الحساب الممول؛ شهادتك متاحة للتنزيل من لوحة التحكم.» / “The next step is identity verification and the funded-account agreement…”. لا توجد أي اتفاقية في تطبيق العميل (لا نتيجة لـ agreement في apps/client/src سوى تعليق)، وزر البريد nextStepsUrl يفتح لوحة الحساب الممول مباشرة (risk.ts:291). مصفوفة النصوص التنظيمية: نص اتفاقية المتداول غير موجود (القرار 7). | ar.json:L128 / en.json:L128; services/engine/src/risk.ts:291; brand/regulatory-matrix.md § 3 (7) | «الخطوة التالية هي التحقق من الهوية؛ يُفعَّل حسابك المموَّل خلال ≈ 72 ساعة وتصلك بيانات cTrader في رسالة منفصلة. شهادتك متاحة للتنزيل من لوحة التحكم.» (صياغة المعجم § 7 mockup 06). | S | brand glossary § 7 (evaluation-pass body) / ui-ux-pro-max §8 error-clarity | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### البريد — بريد بيانات الدخول (account_ready)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-034 | P2 ثانوي | نص | mail.account_ready.subject · mail.account_ready.title · mail.account_ready.body (آخر جملة) · mail.common.why | الموضوع «حساب التقييم» والعنوان «حساب تحديك»، وتكرار جملة «لا نطلب كلمة المرور» مرتين في الرسالة | الموضوع: «حساب التقييم {reference} جاهز على cTrader» / العنوان: «حساب تحديك جاهز على cTrader». الجسم ينتهي بـ «لن يطلب منك فريق FPT كلمة المرور أبداً.» ثم سطر «لماذا وصلتك» يكرّر «لا نطلب كلمات المرور عبر البريد أو الهاتف أبداً.» في كل رسالة. | ar.json:L105 / en.json:L105; ar.json:L106 / en.json:L106; ar.json:L107 / en.json:L107; ar.json:L55 / en.json:L55 | توحيد الموضوع والعنوان («حساب تقييمك {reference} جاهز على cTrader»)؛ حذف الجملة من الجسم والاكتفاء بسطر «لماذا وصلتك» (أو العكس). | S | design:ux-copy / mail clarity | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### البريد — بريد تنفيذ الدفعة

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-008 | P1 رئيسي | نص | mail.payout_paid.body {channel} · services/engine/src/payouts.ts:347 | متغيّر القناة في بريد «تمت معالجة دفعتك» يستقبل رمز قاعدة البيانات الخام (fiper_card / crypto / wire / fiper_p2p) | القالب: «أُرسل إليك مبلغ {amount} عبر {channel}.» والمحرّك يمرّر channel: p.channel (العمود text «crypto \| wire \| fiper_card \| fiper_p2p»). النتيجة للعميل: «أُرسل إليك مبلغ 1,250.00 USD عبر fiper_card.» — تسريب تقني في رسالة أساسية. ملف العيّنات فقط يمرّر «Fiper Card» منسّقاً. | ar.json:L145 / en.json:L145; services/engine/src/payouts.ts:340-349 (channel: p.channel); packages/db/src/schema.ts:594; packages/adapters/scripts/render-samples.ts:45 | إضافة mail.common.channels.{crypto,wire,fiper_card,fiper_p2p} بالصيغتين وترجمة القناة في spec('payout_paid') قبل الإدراج (كما تُترجم kind في breach). | S | ui-ux-pro-max §8 / CLAUDE.md «no technical leaks on the client» | `packages/adapters/src/mail/templates/index.ts`، `services/engine/src/payouts.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| TXT-065 | P3 صقل | نص | mail.payout_paid.body «شهادة السحب الخاصة بك تحت الزر» / EN «under the button» | تعبير غير مألوف |  | ar.json:L145 / en.json:L145 | «نزّل شهادة الدفعة بالزر أدناه.» / “Download your payout certificate with the button below.” | S | design:ux-copy | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### البريد — بريد رفض التحقق من الهوية

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-084 | P2 ثانوي | نص | mail.kyc_result.declined.body «لم تتم الموافقة على التحقق من هويتك. يمكنك مراجعة المتطلبات وتقديم طلب تحقق جديد.» | النص لا يذكر السبب ولا «المتطلبات» التي يحيل إليها | السبب يظهر فقط كصف في بطاقة الوقائع؛ الجسم يقول «راجع المتطلبات» دون أن تُذكر أي متطلبات في الرسالة أو زرّها («أعد المحاولة»). | ar.json:L169 / en.json:L169; packages/adapters/src/mail/templates/index.ts case kyc_result (rows reason) | إدراج السبب في الجسم: «لم يُقبل التحقق من هويتك للسبب التالي: {reason}. استخدم وثيقة سارية واضحة بالاسم نفسه المسجّل في ملفك، ثم أعد المحاولة.» | S | ui-ux-pro-max §8 error-clarity | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `packages/adapters/src/mail/templates/index.ts` |

### البريد — رسائل بلا خطوة تالية

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-064 | P3 صقل | نص | mail.expiry_warning (بلا زر) · mail.payout_requested (بلا زر) · mail.payout_approved (بلا زر) | ثلاث رسائل تخبر ولا تقود إلى الشاشة المعنية | spec() لا يضيف primary لهذه القوالب رغم وجود صفحات طلب الدفعة ولوحة الحساب. | packages/adapters/src/mail/templates/index.ts cases expiry_warning / payout_requested / payout_approved (no primary) | زر «افتح لوحة التحكم» في expiry_warning و«عرض الطلب» في payout_requested/approved (يتطلب تمرير URL من المحرّك). | S | mail clarity (recipient's next step) | `packages/adapters/src/mail/templates/index.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### البريد — سطور البريد المشتركة

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-057 | P3 صقل | نص | mail.ops_alert.subject «تشغيل FPT · …» · mail.common.noReply «راسلنا على {support}» | «تشغيل FPT» لا تعني operations، و«راسلنا على» حرف جر غير مناسب |  | ar.json:L203 / en.json:L203; ar.json:L54 / en.json:L54 | «عمليات FPT · {severity}: {title}»؛ «للمساعدة راسلنا عبر {support}» أو «اكتب إلى {support}». | S | Arabic style | `packages/i18n/src/messages/ar.json` |

### البريد — كل رسائل البريد (HTML + النسخة النصية)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| TXT-003 | P1 رئيسي | هوية | mail.common.regulatoryPending · layout.ts foot() · textVersion() | عبارة داخلية «النص التنظيمي: بانتظار قرار» ظاهرة للعميل في تذييل كل رسالة بريد | يطبع layout.ts السطرين «النص التنظيمي: بانتظار قرار · Regulatory text: pending decision» داخل صندوق متقطع في كل رسالة، وتكرّرهما النسخة النصية. مصفوفة النصوص التنظيمية § 4: «لا شيء مرئي يحلّ محل النص المفقود». هذه العبارة تكشف للعميل حالة قرار داخلي وتوحي بأن الشركة بلا نص تنظيمي. | packages/adapters/src/mail/templates/layout.ts:191-193 (<p class="mut reg"…>{regulatoryPending}); packages/adapters/src/mail/templates/layout.ts:291 (textVersion); ar.json:L58 / en.json:L58 | إبقاء التعليق HTML فقط وحذف الفقرة المرئية والسطر النصي؛ حذف المفتاح mail.common.regulatoryPending (ومفتاحي client.certificate.regulatory وclient.statement.regulatory غير المستخدمين). | S | brand regulatory-matrix § 4 | `packages/adapters/src/mail/templates/layout.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |

### مكوّنات @fpt/ui — DangerZone

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-055 | P3 صقل | وصول | id="ui-danger-h" ثابت | عنوان منطقة الخطر بمعرّف ثابت — تكرار المعرّف إن ظهرت منطقتان في صفحة | `aria-labelledby="ui-danger-h"` مع `id` ثابت؛ أي صفحة بمنطقتي خطر تنتج معرّفين مكررين (axe duplicate-id). | packages/ui/src/components/danger-zone.tsx:19–20 `aria-labelledby="ui-danger-h"` / `<h2 id="ui-danger-h"` | `React.useId()`. | S | web-interface-guidelines §Accessibility | `packages/ui/src/components/danger-zone.tsx` |

### مكوّنات @fpt/ui — JsonViewer · EventFeed details

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-083 | P3 صقل | RTL | .ui-feed__details pre { text-align:left; direction:ltr } | القيم الفيزيائية الوحيدة في CSS التطبيق (text-align:left) — مقصودة لكتل الشيفرة | جرد الخصائص الفيزيائية: لا `pl-/pr-/ml-/mr-/left-/right-/text-left/right` في apps/* ولا packages/ui (0 نتيجة). المتبقي مقصود: `text-align:left` لكتلة JSON، وفي حزمة الهوية `right:14px`/`padding-right:56px` للاصقة العملة و`border-left/bottom` لعلامة الصح — موثّقة «physical by design». | packages/ui/src/components.css:459 `direction: ltr; text-align: left;` · brand/components/components.css:96–97,108 | لا تغيير؛ أبقِ التعليق الموثّق. (معلوماتي لمصفوفة RTL.) | S | fpt-brand «RTL: logical properties only» | `packages/ui/src/components.css` |

### مكوّنات @fpt/ui — Toast

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-034 | P3 صقل | وصول | .fpt-toast .ui-toast-close 36×36 | زر إغلاق التنبيه 36 px تحت أرضية اللمس 44 px | components.css يضبط width/height 36px لزر الإغلاق داخل التنبيه بينما أرضية F1 44 px؛ بوابات smallTargets لا تلتقط التنبيهات لأنها عابرة. | packages/ui/src/components.css:236 | 44×44 مع margin سالب للحفاظ على الكثافة البصرية. | S | ui-ux-pro-max `touch-target-size`; docs/design-system.md § 4 | `packages/ui/src/components.css` |

### مكوّنات @fpt/ui — ToastProvider

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-056 | P3 صقل | بصري | حد الكومة | كومة التنبيهات تسمح بأربع رسائل مرئية (الحد الموصى به 3) والطارئة تبقى بلا حد زمني | `xs.slice(-3)` يحتفظ بثلاث ثم يضيف الرابعة. التنبيهات danger دائمة بلا زمن وبلا تجميع. | packages/ui/src/components/toast.tsx:72 `setItems((xs) => [...xs.slice(-3), { ...opts, tone, id }])` · :71 `alarming(tone) ? 0 : 5000` | `slice(-2)`؛ تجميع التنبيهات المتطابقة. | S | ui-ux-design-pro component-patterns «Toast: max 3 visible» | `packages/ui/src/components/toast.tsx` |

### مكوّنات @fpt/ui — admin — Select (Radix) في RTL

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-040 | P3 صقل | وصول | Select بلا dir (عدا المنطقة الزمنية dir=ltr) | قوائم Select الإدارية بلا dir في RTL: محاذاة علامة الاختيار والسهم تتبع LTR | Radix Select يستخدم dir للمحاذاة الداخلية ومواضع العناصر؛ معظم الاستخدامات (الإعدادات، المنتجات، المحاكي، السحوبات) لا تمرّره. أثره بصري/ترتيبي لا وظيفي (الأسهم عمودية). | grep «<Select» في apps/admin = 25 استخداماً بلا dir; packages/ui/src/components/select.tsx:538 | يُحلّ تلقائياً بـ DirectionProvider (A11Y-010). | S | accessibility.md § ARIA Patterns | `packages/ui/src/components/select.tsx` |

### مكوّنات @fpt/ui — admin-accounts وكل DataTableRows

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-015 | P3 صقل | وصول | .ui-table-sort[aria-sort=…] في components.css مقابل aria-sort على <th> | أيقونة اتجاه الفرز النشط لا تُبرَز: محدّد CSS يستهدف الزر بينما aria-sort على th | بعد الفرز بـ Enter: th aria-sort=ascending (صحيح لقارئ الشاشة) لكن opacity الأيقونة بقيت 0.55 ولونها muted؛ قاعدة .ui-table-sort[aria-sort='ascending'] لا تطابق أبداً. المبصرون يرون سهمًا باهتًا لا يميّز العمود المفروز. | .e2e/a11y-admin.log desktop:accounts:afterSort iconOpacity 0.55; packages/ui/src/components.css:437-438; packages/ui/src/components/data-table.tsx:97 | غيّر المحدّد إلى th[aria-sort='ascending'] .ui-table-sort svg, th[aria-sort='descending'] .ui-table-sort svg. | S | ui-ux-pro-max `color-not-only` (visual state parity) | `packages/ui/src/components.css` |

### مكوّنات @fpt/ui — home, account, admin-overview (عناوين الأقسام ذات العدّاد

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-030 | P3 صقل | وصول | Section <h2><span>title</span><small>aside</small></h2> و StatusBadge داخل h2 | نص جانبي داخل عنصر العنوان يلتصق بالاسم: «الحسابات الأخرى1»، «يحتاج إجراءً3»، «هويتكموثَّق» | القياس: h2 يُنطق «الحسابات الأخرى1» و «الصفقات المفتوحة1» و «يحتاج إجراءً3» وفي الملف «هويتكموثَّق» لأن الشارة/العدّاد داخل العنوان بلا فاصل. | .e2e/a11y-client.log desktop:home:outline, /ar/profile:outline; .e2e/a11y-admin.log desktop:/ar:outline; packages/ui/src/components/list-row.tsx:196-204 | أخرج aside من عنصر العنوان (div حاوٍ flex مع h2 منفصل) أو أضف فاصلاً مرئياً/sr-only («، 1 حساب»). | S | ui-ux-pro-max `voiceover-sr`; WCAG 2.4.6 | `packages/ui/src/components/list-row.tsx`، `apps/client/src/app/[locale]/(app)/profile/page.tsx` |

### مكوّنات @fpt/ui — أزرار block الطويلة (ادفع {amount}، تشغيل على FPT-…، اطلب دفعة)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-050 | P3 صقل | بصري | .fpt-btn white-space:nowrap + ui-btn-truncate | تسميات الأزرار لا تلتف وتُقص بقصاصة على 360px — قد يختفي المبلغ من زر الدفع | الحزمة تفرض `white-space:nowrap` على الزر و@fpt/ui يضيف `ui-btn-truncate` (ellipsis). تسمية مثل «ادفع 1,070.00 USD» أو «تشغيل على FPT-100K-5L78C» على 320px مع حشو 36px تُقص فيختفي الجزء المهم. | brand/components/components.css:46 `white-space:nowrap` · packages/ui/src/components/button.tsx:60 `'ui-btn-truncate'` · packages/ui/src/components.css:211 `.fpt-btn.ui-btn-truncate > .ui-btn-label { overflow: hidden; text-overflow: ellipsis; }` · checkout-form.tsx:251 `t.rich('pay', { amount … })` | لأزرار `block`: `white-space:normal; line-height:1.3; padding-block:10px` بدل القص. | S | ui-ux-pro-max §5 compact-label-overflow · §6 truncation-strategy | `packages/ui/src/components.css`، `packages/ui/src/components/button.tsx` |

### مكوّنات @fpt/ui — الأزرار الشبحية (ghost) في الرأس والصفوف

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-024 | P3 صقل | وصول | .fpt-btn--ghost border rgba(255,255,255,.08) | حدّ الزر الشبحي بتباين 1.19:1 — حدود الزر غير مدركة، الاعتماد على النص/الأيقونة فقط | حدّ 8 % وتعبئة 5 % على #0A0A0A: 1.19–1.25:1. مقبول في WCAG 1.4.11 إذا عُرف الزر من نصه، لكن أزرار الأيقونة الشبحية (نسخ، رجوع، الجرس) بلا نص مرئي تعتمد على الأيقونة (بيضاء/muted ≥ 6:1) فقط. | brand/tokens.css:299-300; جدول التباين | ارفع حدّ ghost إلى --fpt-line-strong (3.15:1) لأزرار الأيقونة فقط، أو اعتمد الأيقونة كحدّ مرئي ووثّق الاستثناء. | S | accessibility.md § Contrast (UI components 3:1) | `brand/tokens.css` |

### مكوّنات @fpt/ui — الإشعارات المنبثقة (toast) ذات الزر

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-029 | P3 صقل | وصول | ToastProvider duration 5000 مع action | التنبيه المنبثق الذي يحمل زر إجراء يختفي بعد 5 ثوانٍ دون وسيلة تمديد | التنبيهات غير الحرجة تُغلق تلقائياً بعد 5 s حتى عندما تحوي action؛ مستخدم قارئ الشاشة أو بطيء الحركة قد لا يصل إلى الزر (WCAG 2.2.1). التنبيهات الحرجة (loss/danger) ثابتة — جيد. | packages/ui/src/components/toast.tsx:718-720 | اجعل duration=0 افتراضياً عند وجود action، وأوقف المؤقّت عند التحويم/التركيز داخل المنطقة. | S | WCAG 2.2.1 Timing Adjustable; ui-ux-pro-max `auto-rotation-controls` | `packages/ui/src/components/toast.tsx` |

### مكوّنات @fpt/ui — الحوارات والأوراق

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-037 | P3 صقل | وصول | Alert component على لافتات ثابتة (role=alert عند التحميل) | لافتة «الحساب مخترق» الثابتة تحمل role=alert فتُعلن قسراً عند كل تحميل للصفحة | القياس: صفحة الحساب تحوي div[alert/] ثابتة من الخادم. role=alert مخصّص للتغيّرات الديناميكية؛ على المحتوى الثابت يُقاطع قراءة العنوان ويتكرّر مع كل تنقّل. | .e2e/a11y-client.log phone/desktop:account:outline live=[div[alert/]…]; packages/ui/src/components/alert.tsx:811; apps/client/src/app/[locale]/(app)/accounts/[id]/page.tsx:92 | أضف خاصية static للـ Alert تُحوّله إلى <section aria-labelledby> (role=region) واترك role=alert لنتائج الإجراءات. | S | accessibility.md § Live Regions | `packages/ui/src/components/alert.tsx` |

### مكوّنات @fpt/ui — الرأس على 360px

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-054 | P3 صقل | بصري | AppHeaderBar center truncate | عنوان الشاشة في الرأس يُقص بقصاصة صامتة على الهواتف الضيقة | `truncate` مع عنوان مثل «لماذا توقّف حسابي» أو عناوين الإدارة الطويلة؛ لا tooltip ولا التفاف. | packages/ui/src/components/app-shell.tsx:65 `<div className="min-w-0 flex-1 truncate text-center text-base font-semibold">` | اسمح بسطرين (`line-clamp-2` بخط 15) أو قصّر مفاتيح `client.screen`. | S | ui-ux-pro-max §6 truncation-strategy | `packages/ui/src/components/app-shell.tsx` |

### مكوّنات @fpt/ui — الصفوف والأوراق على iOS

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-068 | P3 صقل | بصري | -webkit-tap-highlight-color · overscroll-behavior · user-select | لمسيات ناقصة: وميض iOS الرمادي الافتراضي على الصفوف، لا overscroll-behavior في الأوراق، والنص قابل للتحديد بالضغط المطوّل على الصفوف | `touch-action:manipulation` موجود (جيد) لكن لا `-webkit-tap-highlight-color` في أي مكان، لا `overscroll-behavior: contain` على `SheetContent/DialogContent` (`overflow-y-auto`)، و`select-none` على الأزرار فقط لا على `.ui-row` الزرّية ولا `.ui-meter`. | packages/ui/src/components.css:112–114 (touch-action فقط) · grep tap-highlight/overscroll → 0 · packages/ui/src/components/sheet.tsx:40 `overflow-y-auto` · button.tsx:12 `select-none` | `-webkit-tap-highlight-color: transparent` على `a, button, [role=button]`؛ `overscroll-behavior: contain` على حاويات الأوراق/الحوارات؛ `user-select:none` على `.ui-row:where(a,button)`. | S | web-interface-guidelines §Touch & Interaction · ui-ux-pro-max §2 tap-delay | `packages/ui/src/components.css`، `packages/ui/src/components/sheet.tsx`، `packages/ui/src/components/dialog.tsx` |

### مكوّنات @fpt/ui — الكل

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-022 | P3 صقل | وصول | @media (prefers-contrast) / (forced-colors) — غير موجودة | لا دعم لوضع الألوان القسرية/التباين العالي: حالات الرقائق والمقاييس والمفتاح تعتمد على خلفيات تُزال | grep prefers-contrast\|forced-colors = 0. في Windows High Contrast تُزال background-color: الرقاقة المحددة (.ui-chip[aria-current]) والمفتاح (Switch) وشريط RuleMeter تفقد حالتها المرئية. حلقة التركيز outline تبقى (جيد). | grep -r prefers-contrast\|forced-colors apps packages brand = 0; packages/ui/src/components.css:429-431, 349 | أضف @media (forced-colors: active) { .ui-chip[aria-current], .fpt-seg [aria-selected=true] { outline: 2px solid; } .ui-meter__bar > span { background: CanvasText; } } وحدودًا ظاهرة للمفتاح. | S | accessibility.md § Media Queries for Preferences | `packages/ui/src/components.css` |

### مكوّنات @fpt/ui — بطاقات الإدارة (settings, orders

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-039 | P3 صقل | بصري | CardContent | CardContent يفرض 14px افتراضياً على محتوى البطاقة | `CardContent` = `text-sm text-ink-2`؛ أي نص مباشر أو عنصر بلا حجم صريح داخل البطاقة ينزل إلى 14px (مثل `<span className="fpt-unit text-muted">{c.attempts}×</span>` و`<ol … text-sm>` في enrol). الصفحات تعوّض بـ `text-body-2` يدوياً في معظم المواضع — فخّ افتراضي. | packages/ui/src/components/card.tsx:49 `<div className={cn('text-sm text-ink-2', className)} …/>` · apps/admin/src/app/[locale]/(auth)/enrol/page.tsx:175 `<ol className="list-decimal space-y-2 ps-5 text-sm text-ink-2">` | الافتراضي `text-body-2`. | S | design-system § 2 | `packages/ui/src/components/card.tsx` |

### مكوّنات @fpt/ui — كل ActionDialog في الإدارة (اعتماد الدفعة، الرفض، الدعوة…) · ورقة تفاصيل القاعدة في العميل

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-005 | P1 رئيسي | بصري | DialogDescription / SheetHeader > p | وصف الحوارات والأوراق 14px — ويُلغي حتى override العميل | `DialogDescription` يرسم `<p className="text-sm text-muted">` (14px) في كل حوار إجراء بالإدارة. `SheetHeader` يفرض `[&>p]:text-sm` بمحدِّد أعلى تخصيصاً (`.x > p`) من `.text-body-2` التي يمرّرها العميل في ورقة القاعدة، فتبقى 14px رغم المحاولة. الحوارات لا تُفتح في بوّابة ux-gates فلا تُقاس. | packages/ui/src/components/dialog.tsx:54 `className={cn('text-sm text-muted', className)}` · packages/ui/src/components/sheet.tsx:52 `[&>h2]:text-xl … [&>p]:text-sm [&>p]:text-muted` · apps/client/src/components/account-overview.tsx:383 `<SheetDescription className="text-body-2 text-muted">` (مُلغى بالمحدِّد الأقوى) | `text-body-2` في DialogDescription وSheetHeader، وإزالة `[&>p]` لصالح أصناف على العنصر نفسه. أضف حوار اعتماد الدفعة إلى مسار البوّابة. | S | design-system § 2 · ui-ux-pro-max §6 font-scale | `packages/ui/src/components/dialog.tsx`، `packages/ui/src/components/sheet.tsx` |

### مكوّنات @fpt/ui — كل DataTableRows (الإدارة)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-048 | P3 صقل | تقني | <a href> خام في الخلية الأولى + زر «المزيد» بأصناف خام | روابط الجدول <a> عادية (بلا Link/prefetch، إعادة تحميل كاملة) وزر «المزيد» يتجاوز مكوّن Button | `DataTableRows` يرسم `<a href={r.href}>` على المكتب و`ListRow` بـ `linkComponent` افتراضي `'a'`، والتعليق يقرّ أن `linkComponent` «kept for API compatibility». كل نقرة صف في الإدارة = إعادة تحميل كاملة بلا جلب مسبق ولا حالة تحميل. زر «المزيد» يُبنى من أصناف `fpt-btn fpt-btn--ghost` بدل `<Button>`. | packages/ui/src/components/data-table.tsx:121 `<a href={r.href} className="inline-flex min-h-11 …">` · :138 `<ListRow … href={r.href} …/>` (بلا linkComponent) · :169 التعليق · :151 `<button type="button" className="fpt-btn fpt-btn--ghost self-center"` | مرّر `linkComponent` فعلياً إلى الصفوف والخلية الأولى، واستعمل `<Button variant="ghost">`. | S | web-interface-guidelines §Navigation & State «Links use <Link>» · ui-ux-pro-max §3 lazy-loading | `packages/ui/src/components/data-table.tsx` |

### مكوّنات @fpt/ui — كل ListRow بشيفرون (المزيد، الطلبات، قوائم الإدارة على الهاتف)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-016 | P2 ثانوي | RTL | ListRow chevron | الشيفرون يحمل آليتي انعكاس معاً (.fpt-icon--dir transform + rtl:-scale-x-100 scale) — خطر انعكاس مزدوج | `.fpt-icon--dir` يطبّق `transform: scaleX(-1)` تحت `[dir=rtl]`، وTailwind v4 يطبّق `rtl:-scale-x-100` عبر خاصية `scale` المستقلة. الخاصيتان تتراكبان فيُقلب السهم مرتين ويعود إلى اتجاه LTR. لقطة الإنتاج (29 أكتوبر 13:29) تسبق الالتزام الذي أضاف `rtl:-scale-x-100` فلا تثبت الحالة الحالية. ArrowLeft في رأس العميل يستعمل Tailwind فقط وBackLink يستعمل `.fpt-icon--dir` فقط — ثلاث طرق لشيء واحد. | packages/ui/src/components/list-row.tsx:43 `<ChevronRight … className="ui-chev fpt-icon--dir rtl:-scale-x-100" />` · brand/components/components.css:37 `[dir="rtl"] .fpt-icon--dir{transform:scaleX(-1)}` · tailwindcss dist: `o("scale","var(--tw-scale-x) var(--tw-scale-y)")` · apps/client/src/components/client-header.tsx:95 `rtl:-scale-x-100` · packages/ui/src/components/back-link.tsx:28 `fpt-icon--dir` | آلية واحدة: `.fpt-icon--dir` فقط (أزل `rtl:-scale-x-100` من ListRow وClientHeader). أضف اختبار Playwright يقيس `getComputedStyle(chevron).transform/scale` في RTL. | S | design-system § 5.8 «الشيفرون والسهم ينعكسان» · ui-ux-pro-max §4 icon-style-consistent | `packages/ui/src/components/list-row.tsx`، `apps/client/src/components/client-header.tsx` |

### مكوّنات @fpt/ui — كل ListRow رابط

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-032 | P2 ثانوي | بصري | ui-row:active · ui-meter[data-interactive]:active · .fpt-challenge:active | لا استجابة ضغط على الصفوف والعدّادات والبطاقات: التحويم فقط خلف @media(hover) | عنصر التنقل الأساسي على الهاتف هو الصف (`ListRow`) والعدّاد (`RuleMeter onOpen`) وبطاقة الحجم؛ كلها بلا `:active` ولا `press`. الأزرار وحدها تملك `transform:scale(.98)`. على اللمس لا يحدث أي تغيير بصري بين اللمس والانتقال (بخاصة مع غياب حالة التحميل VIS-003). | packages/ui/src/components.css:388–391 `.ui-row …` (hover فقط تحت `@media (hover: hover)`) · :343–344 `.ui-meter[data-interactive='true']` (cursor + focus فقط) · brand/components/components.css:175–178 `.fpt-challenge` (hover فقط) · :48 `.fpt-btn:active{transform:scale(var(--fpt-motion-press-scale))}` (الأزرار فقط) | أضف `.ui-row:where(a,button):active, .ui-meter[data-interactive]:active, .fpt-challenge:active { background: color-mix(in srgb, var(--fpt-text-primary) 6%, transparent) }` بمدة `--fpt-motion-duration-press`. | S | ui-ux-pro-max §2 press-feedback · §3 tap-feedback-speed · §7 scale-feedback | `packages/ui/src/components.css` |

### مكوّنات @fpt/ui — كل StatusBadge size=sm (صفوف القوائم في التطبيقين)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-038 | P3 صقل | بصري | .fpt-badge.ui-badge-sm | كلمة الحالة في الشارة الصغيرة 12px — تحت أرضية التسمية 13px | design-system § 2: 12px للتعليقات فقط، التسمية ≥ 13. كلمة الحالة («قيد المراجعة»، «ممول») معلومة أساسية تُعرض 12px في كل صف قائمة. البوّابة تستثني `.fpt-badge`. | packages/ui/src/components.css:288 `.fpt-badge.ui-badge-sm { height: 22px; padding: 0 8px; font-size: var(--text-xs); }` · استعمال `size="sm"` في accounts/page.tsx:110، payouts/page.tsx:90، وعشرات صفوف الإدارة | `--text-label` (13) مع ارتفاع 24؛ أو احذف المقاس sm وأبقِ 13px فقط. | S | design-system § 2 | `packages/ui/src/components.css` |

### مكوّنات @fpt/ui — كل الأزرار الأساسية الحمراء

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-023 | P3 صقل | وصول | :focus-visible outline #F42821 على .fpt-btn--primary (تدرّج أحمر) | حلقة التركيز بلون الزر الأساسي نفسه — تُرى فقط بفضل فجوة 2 px | نسبة الحلقة إلى الأرض 4.88:1 (ground) / 4.29:1 (elevated) تجتاز 3:1، لكن نسبتها إلى تعبئة الزر الأساسي 1.00:1؛ outline-offset 2px يترك شريطاً أسود رفيعاً بين الحلقة والزر فتظهر. على الأرض المرتفعة داخل الحوارات تبقى ≥ 3:1. | brand/tokens.css:112 --fpt-action-focus-ring #F42821; brand/components/components.css:49; جدول التباين في A11Y-SUMMARY | أبقِ الأمر كما هو مع ضمان offset ≥ 2px لكل عنصر أحمر (لا offset سالب على .fpt-btn--primary / التبويب النشط)، أو حلقة مزدوجة (box-shadow 0 0 0 2px ground + outline). | S | ui-ux-pro-max `focus-appearance` | `brand/components/components.css`، `packages/ui/src/components.css` |

### مكوّنات @fpt/ui — كل الأماكن التي تستخدم Money quietCode (12 استخداماً)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-021 | P3 صقل | وصول | <span class="fpt-unit" aria-label={full}> | aria-label على span عام (role=generic) — تسمية محظورة في ARIA 1.2 وتتجاهلها القارئات | Money مع quietCode يضع الاسم الكامل في aria-label على span بلا دور؛ VoiceOver/NVDA يقرآن المحتوى المرئي بدلاً منه (وهو يحوي الرمز بخط أصغر) فلا ضرر كبير، لكن النية غير محقَّقة والمدقّقات تحذّر. | packages/ui/src/components/money.tsx:289; grep quietCode = 12 | احذف aria-label أو أضف role="text"/استخدم <bdi> مع نص مرئي كامل؛ لإخفاء الرمز بصرياً استخدم حجم خط أصغر لا إخفاءً من القارئ. | S | accessibility.md § Screen Reader Best Practices; ARIA 1.2 prohibited naming | `packages/ui/src/components/money.tsx` |

### مكوّنات @fpt/ui — كل النماذج متعددة الحقول (الإدارة: المنتجات، المسارات، الإعدادات)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| A11Y-028 | P3 صقل | وصول | Field error <p role="alert"> لكل حقل | كل خطأ حقل منطقة alert مستقلة: عند إرسال نموذج فيه عدة أخطاء تُعلَن كلها دفعة واحدة ومتداخلة | Field يضع role=alert على كل رسالة؛ مع 3–4 أخطاء من الخادم تتزاحم الإعلانات ويُقاطع بعضها بعضاً. الربط عبر aria-describedby صحيح (تحقّق كوبون). | packages/ui/src/components/field.tsx:634; .e2e/a11y-client.log desktop:checkout:couponError | اترك الأخطاء نصاً مرتبطاً بـ aria-describedby وأعلن ملخصاً واحداً (role=alert) أعلى النموذج «3 حقول تحتاج تصحيحاً» مع نقل التركيز إلى أول حقل. | S | accessibility.md § Live Regions; WIG Forms | `packages/ui/src/components/field.tsx`، `apps/admin/src/components/ActionForm.tsx` |

### مكوّنات @fpt/ui — كل تنبيه Alert (العميل: تحذير السحب 80

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-004 | P1 رئيسي | بصري | Alert body | نص التنبيه جملة كاملة بحجم 14px تحت أرضية الجسم 15px | غلاف نص `Alert` يحمل `text-sm` (14px) ويضع الأبناء النصيين داخل `<div>` فلا يلتقطه بوّاب `smallBody` (الذي يفحص p/li/dd… فقط). كل جملة تحذير على الشاشات الأساسية («تجاوزت 80 % من الحد اليومي»، «فشل الدفع»، رسائل الإجراءات في الإدارة) تُقرأ بـ 14px. | packages/ui/src/components/alert.tsx:44 `<div className="min-w-0 flex-1 text-sm">` و:46 `<div className={cn('text-ink-2', title && 'mt-1')}>{children}</div>` · e2e/ux-audit.ts:71 BODY_TAGS لا تضم DIV | بدّل `text-sm` إلى `text-body-2` (15px) في alert.tsx:44، وأضف DIV ذات النص المباشر إلى فحص البوّابة أو اجعل الأبناء `<p>`. | S | design-system § 2 (جسم ≥ 15) · ui-ux-pro-max §5 readable-font-size | `packages/ui/src/components/alert.tsx` |

### مكوّنات @fpt/ui — كل جدول إدارة على المكتب (hover) · .fpt-listrow · شرائح 

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-024 | P2 ثانوي | هوية | brand/components .fpt-table tr:hover · .fpt-listrow:hover · .fpt-chip[aria-pressed] | الأحمر يُستعمل كلون تحويم (hover) على صفوف الجداول — دور رابع خارج الأدوار الثلاثة | حزمة الهوية تحدد للأحمر ثلاثة أدوار: البلاطة، الإجراء الأساسي، الحالة النشطة. لكن CSS الحزمة تلوّن صف الجدول عند التحويم بـ `--fpt-brand-red-wash` (وصف القائمة كذلك)، فيومض الأحمر على كل حركة للفأرة فوق 100 صف في الإدارة. @fpt/ui أصلح `.ui-row` (4 % من لون النص) لكنه ترك جداول DataTable على قاعدة الحزمة. | brand/components/components.css:204 `@media (hover:hover){.fpt-table tbody tr:hover td{background:var(--fpt-brand-red-wash)}}` · :303 `.fpt-listrow:hover{background:var(--fpt-brand-red-wash)}` · packages/ui/src/components.css:391 `.ui-row:where(a, button):hover { background: color-mix(in srgb, var(--fpt-text-primary) 4%, transparent); }` (النهج الصحيح) | تجاوز في components.css غير المطبّق بطبقة: `.fpt-table.ui-data tbody tr:hover td { background: color-mix(in srgb, var(--fpt-text-primary) 4%, transparent) }`؛ وارفع ملاحظة للحزمة (PROPOSED) لتصحيح قاعدة الحزمة. | S | fpt-brand «red has three roles — nothing else» · ui-ux-design-pro critique «One accent?» | `packages/ui/src/components.css`، `brand/components/components.css` |

### مكوّنات @fpt/ui — كل صفحة ذات رأس لاصق (56px)

| المعرّف | الخطورة | الفئة | العنصر | الملاحظة | التفصيل | الدليل | التوصية | الجهد | المهارة | الملفات |
|---|---|---|---|---|---|---|---|---|---|---|
| VIS-069 | P3 صقل | وصول | scroll-padding-top غائب | التركيز بلوحة المفاتيح قد يختفي خلف الرأس اللاصق (لا scroll-padding/scroll-margin) | الرأس `sticky top-0` 56px+safe؛ عند Tab إلى عنصر يقع تحت الرأس أو عند رابط `#main` لا يُزاح التمرير. التذييل الثابت مُعالج بـ `pb-nav`. | packages/ui/src/components/app-shell.tsx:34 `<header className="ui-chrome sticky top-0 …">` · grep scroll-padding/scroll-mt → 0 | `html { scroll-padding-top: calc(var(--fpt-size-tabbar-h) + env(safe-area-inset-top)); scroll-padding-bottom: calc(var(--fpt-size-tabbar-h) + env(safe-area-inset-bottom)) }`. | S | ui-ux-pro-max §1 focus-not-obscured · web-interface-guidelines §Focus States | `packages/ui/src/components.css` |

## 3. الملاحظات العرضية (من ملخصات المدققين)

### findings-a11y.json

- **severity**: P2
- **category**: وصول
- **app**: both
- **screen**: ملخّص
- **element**: —
- **title**: ملخّص تدقيق الوصول F4
- **detail**: P0: 0 · P1: 2 · P2: 12 · P3: 30 · المجموع 44 بنداً (موثَّقة من بوابات axe، إيصالات الأجهزة، قياسات Playwright على الحزمة المحلية، ومراجعة الكود).
- **axe_aggregation**: ```json
[
 {
  "rule": "page-has-heading-one",
  "impact": "moderate",
  "screens": 25,
  "nodes": 100,
  "projects": "360/390/430/1440",
  "examples": "client/home html, client/account html, client/checkout html, client/payout-new html, admin/admin-signin html, admin/admin-forbidden html"
 },
 {
  "rule": "heading-order",
  "impact": "moderate",
  "screens": 9,
  "nodes": 36,
  "projects": "360/390/430/1440",
  "examples": "client/legal-delete-account section[data-testid=legal-delete-ar] > h3; admin/admin-settings section:nth-child(1) … h3; admin/admin-integrations section[data-testid=integration-myfatoorah] … h3; admin/admin-order .grid > section:nth-child(1) … h3"
 },
 {
  "rule": "image-redundant-alt",
  "impact": "minor",
  "screens": 5,
  "nodes": 10,
  "projects": "430/1440",
  "examples": "client/signin img[alt=\"Fiper Pro Traders\"], client/landing .ui-tile, client/help img"
 },
 {
  "rule": "empty-table-header",
  "impact": "minor",
  "screens": 2,
  "nodes": 2,
  "projects": "1440",
  "examples": "admin/admin-products th:nth-child(9), admin/admin-coupons th:nth-child(8)"
 },
 {
  "note": "292 شاشة-مشروع، 148 مخالفة، 0 critical/serious؛ smallTargets/tiny/smallBody/overflow = 0 في البوابات المحلية؛ إيصالات الجهاز الحقيقي: smallTarget واحد (checkout-transfer input 134×31 على iPhone 15 Pro Max) وبقية الإخفاقات أخطاء console/CSP لا وصول."
 }
]
```
- **headings_landmarks**: ```json
[
 {
  "screen": "client/signin (ar, 390 & 1440)",
  "lang_dir": "ar/rtl",
  "h": "h1 تسجيل الدخول",
  "landmarks": "header, nav[اللغة], main#main",
  "skip": "الانتقال إلى المحتوى",
  "live": "toast region polite"
 },
 {
  "screen": "client/home (1440 ar)",
  "lang_dir": "ar/rtl",
  "h": "h2 الحسابات الأخرى1 (لا h1)",
  "landmarks": "header, main#main, section[حقوق الحساب …], nav[التنقل الرئيسي]",
  "skip": "نعم",
  "live": "polite"
 },
 {
  "screen": "client/account",
  "lang_dir": "ar/rtl (demo03 en/ltr على الهاتف)",
  "h": "h2 الصفقات المفتوحة1 (لا h1)",
  "landmarks": "header, main#main, section[hero], nav[main]; tablist×1 بلا dir",
  "skip": "نعم",
  "live": "div[alert] ثابت + polite"
 },
 {
  "screen": "client/payout-new",
  "lang_dir": "ar/rtl",
  "h": "— (لا عناوين؛ الحساب غير ممول)",
  "landmarks": "header, main#main, nav",
  "skip": "نعم",
  "live": "status + polite"
 },
 {
  "screen": "client/checkout",
  "lang_dir": "ar/rtl",
  "h": "— (لا h1؛ legend «طريقة الدفع»)",
  "landmarks": "header, main#main, nav",
  "skip": "نعم",
  "live": "polite"
 },
 {
  "screen": "client/more",
  "lang_dir": "ar/rtl",
  "h": "h2 ×4 (حسابك، التداول، المساعدة، الجلسة) بلا h1",
  "landmarks": "header, main, nav[اللغة], nav[main]",
  "skip": "نعم",
  "live": "polite"
 },
 {
  "screen": "client/profile",
  "lang_dir": "ar/rtl",
  "h": "h2 ×7 + h3 ×3 بلا h1؛ شارات داخل h2",
  "landmarks": "header, main, section, nav",
  "skip": "نعم",
  "live": "polite"
 },
 {
  "screen": "client/verify",
  "lang_dir": "ar/rtl",
  "h": "h1 (AuthCard)",
  "landmarks": "header, nav[اللغة], main",
  "skip": "نعم",
  "live": "polite; OTP group بلا اسم"
 },
 {
  "screen": "admin/signin",
  "lang_dir": "ar/rtl",
  "h": "h2 دخول الموظفين (لا h1)",
  "landmarks": "header, nav[اللغة], main, footer",
  "skip": "لا (خارج AppShell)",
  "live": "0"
 },
 {
  "screen": "admin/overview",
  "lang_dir": "ar/rtl",
  "h": "h1 نظرة عامة; h2 يحتاج إجراءً3, آخر الأحداث, صحة المحرّك, الحسابات حسب الحالة",
  "landmarks": "aside, nav[تنقّل الإدارة]×2, nav[اللغة], header×2, main#main, section[الأرقام الرئيسية], footer",
  "skip": "نعم",
  "live": "1"
 },
 {
  "screen": "admin/accounts",
  "lang_dir": "ar/rtl",
  "h": "h1 الحسابات; caption sr-only «الحسابات»",
  "landmarks": "كما أعلاه",
  "skip": "نعم",
  "live": "1"
 },
 {
  "screen": "admin/settings (approver → لا صلاحية)",
  "lang_dir": "ar/rtl",
  "h": "h3 لا صلاحية لهذه الصفحة فقط",
  "landmarks": "كما أعلاه",
  "skip": "نعم",
  "live": "1"
 },
 {
  "screen": "admin/404",
  "lang_dir": "ar/rtl",
  "h": "h3 الصفحة غير موجودة; title فارغ",
  "landmarks": "main فقط",
  "skip": "لا",
  "live": "0"
 }
]
```
- **contrast_table**: ```json
[
 {
  "pair": "text-primary #FFF / ground #0A0A0A",
  "ratio": "19.80",
  "result": "pass"
 },
 {
  "pair": "text-secondary .78 / ground · panel · elevated",
  "ratio": "11.96 · 11.36 · 10.95",
  "result": "pass"
 },
 {
  "pair": "text-muted = text-label .55 / ground · panel · elevated",
  "ratio": "6.28 · 6.15 · 6.03",
  "result": "pass (body 4.5)"
 },
 {
  "pair": "placeholder (muted .55) / panel #141414",
  "ratio": "6.15",
  "result": "pass"
 },
 {
  "pair": "action-disabled-fg .38 / ground",
  "ratio": "3.50",
  "result": "exempt (disabled)"
 },
 {
  "pair": "finance-gain #2AB37A / ground · elevated",
  "ratio": "7.38 · 6.49",
  "result": "pass"
 },
 {
  "pair": "finance-loss #E8785A / ground · elevated",
  "ratio": "6.85 · 6.02",
  "result": "pass"
 },
 {
  "pair": "finance-drawdown/warning #D9A441 / ground",
  "ratio": "8.80",
  "result": "pass"
 },
 {
  "pair": "state-info #5B9BD5 / ground · elevated",
  "ratio": "6.69 · 5.88",
  "result": "pass"
 },
 {
  "pair": "danger #E8785A / danger wash 12 % over ground (destructive button)",
  "ratio": "6.00",
  "result": "pass"
 },
 {
  "pair": "text-on-brand #FFF / flat brand red #F42821",
  "ratio": "4.06",
  "result": "pass body 4.5? FAIL for < 18 px normal; الزر الأساسي يستخدم التدرّج (المنتصف ≈ 5.71، النهاية 8.20) فالنص 16 px semibold يجتاز في المنتصف؛ الحافة اليسرى عند 4.06"
 },
 {
  "pair": "focus ring #F42821 / ground · panel · elevated",
  "ratio": "4.88 · 4.54 · 4.29",
  "result": "pass (UI 3:1)"
 },
 {
  "pair": "focus ring / primary button fill",
  "ratio": "1.00",
  "result": "يعتمد على outline-offset 2 px (A11Y-023)"
 },
 {
  "pair": "line-input .40 (حدود الحقول) / ground · panel",
  "ratio": "3.77 · 3.83",
  "result": "pass (UI 3:1)"
 },
 {
  "pair": "line-strong .35 / ground",
  "ratio": "3.15",
  "result": "pass (حدّي)"
 },
 {
  "pair": "ghost border .08 / ground",
  "ratio": "1.19",
  "result": "fail كحدّ مكوّن (A11Y-024)"
 },
 {
  "pair": "line-ash .08 (hairlines)",
  "ratio": "1.19",
  "result": "زخرفي — لا متطلّب"
 },
 {
  "pair": "chip inverted #0A0A0A / #FFF",
  "ratio": "19.80",
  "result": "pass"
 },
 {
  "pair": "tab bar label muted .55 / chrome (ground 92 %)",
  "ratio": "6.28",
  "result": "pass (12 px semibold)"
 }
]
```
- **keyboard_walks**: ```json
[
 {
  "flow": "client sign-in (390 & 1440, ar)",
  "result": "ترتيب Tab منطقي: بريد (autoFocus) → كلمة المرور → دخول → رابط الرمز → نسيت كلمة المرور → إنشاء حساب → رابط التخطي → الشعار → اللغة. حلقة تركيز 2 px #F42821 ظاهرة على كل عنصر. أخطاء الإرسال تُعلَن (role=alert) لكن بلا aria-invalid ولا نقل تركيز (A11Y-006)."
 },
 {
  "flow": "client home → account tabs → rule sheet",
  "result": "Tab لا يقع تحت شريط التبويب الثابت ولا الرأس (obscured=false لكل المحطات). التبويبات Radix: Home/End/Arrow تعمل في ltr؛ في rtl بلا dir (A11Y-010). ورقة التفاصيل: التركيز يدخل إلى «إغلاق»، المصيدة تعمل (Tab يبقى داخل الحوار)، Escape يغلق، التركيز لا يعود (A11Y-002)؛ زر الإغلاق −8 px خارج الشاشة على الهاتف (A11Y-014)."
 },
 {
  "flow": "client shop → checkout",
  "result": "بطاقات الحجم role=radio لكن Tab على كل بطاقة والأسهم لا تعمل (A11Y-008). checkout: راديو الدفع sr-only مع حلقة تركيز على الدائرة (جيد)، legend «طريقة الدفع»، الكوبون مربوط بالخطأ (aria-invalid + describedby) ✓، خطأ الموافقة غير مربوط (A11Y-007)."
 },
 {
  "flow": "client payout form",
  "result": "not run — الحساب التجريبي غير ممول فلم يظهر النموذج؛ مراجعة كود فقط (A11Y-011)."
 },
 {
  "flow": "client verify (OTP)",
  "result": "6 خانات مسمّاة «الرقم n من 6»، inputmode numeric، pattern [0-9]*، autocomplete one-time-code على الأولى، كتابة 6 أرقام تملأ للأمام والتركيز ينتهي على الخانة 6 ✓؛ المجموعة بلا اسم (A11Y-018)."
 },
 {
  "flow": "admin sign-in + TOTP (approver)",
  "result": "يكتمل بلوحة المفاتيح؛ لا h1؛ الإرسال الفارغ تحقق أصلي فقط (A11Y-039)."
 },
 {
  "flow": "admin accounts (1440)",
  "result": "رؤوس الفرز أزرار مسمّاة «ترتيب حسب …»، Enter يفرز ويضبط aria-sort=ascending على th ويبقي التركيز على الزر ✓؛ الأيقونة لا تُبرَز (A11Y-015). الرقائق روابط بـ aria-current، حقل البحث aria-label داخل form role=search ✓، caption sr-only ✓."
 },
 {
  "flow": "admin staff menu (390)",
  "result": "Enter يفتح القائمة والتركيز على «حسابي»، dir=rtl، Escape يعيد التركيز إلى الزر ✓."
 },
 {
  "flow": "admin More sheet (390)",
  "result": "FAIL — Enter/Space/نقرة: لا حوار (A11Y-001)."
 },
 {
  "flow": "admin ActionDialog / settings tabs",
  "result": "not run — النقر على صف الجدول والتبويبات تجاوز المهلة في الجولة؛ مراجعة كود (A11Y-033, A11Y-010)."
 },
 {
  "flow": "reduced motion / large text",
  "result": "prefers-reduced-motion مُعالَج عالمياً (brand/tokens.css:465 و components.css:116) ✓ — اللقطات client-home-reducedmotion-*.png بلا حركة؛ لقطات largetext تعرض الصفحة كاملة والشريط الثابت يظهر في منتصف الالتقاط (أثر التقاط لا عيب)."
 },
 {
  "flow": "zoom / viewport",
  "result": "viewport width=device-width, initial-scale=1, viewport-fit=cover بلا maximum-scale أو user-scalable=no ✓ (العميل والإدارة)."
 }
]
```
- **posture_ar**: الأساس متين: axe بلا مخالفات حرجة أو جسيمة على 292 شاشة-مشروع، حلقة تركيز موحّدة ظاهرة بتباين ≥ 4.29:1، رابط تخطٍّ يعمل، معالم main/nav/header صحيحة، lang/dir صحيحان في الجذر، المبالغ معزولة اتجاهياً، الحقول مرتبطة بتسمياتها وأخطاء Field مربوطة بـ aria-describedby، وprefers-reduced-motion مُعالَج عالمياً. أخطر ما وُجد عيبان وظيفيان: زر «المزيد» في شريط التبويب الإداري لا يفتح لوحة الأقسام إطلاقاً على الهاتف بسبب استبدال onClick، وورقة تفاصيل القاعدة في العميل تُسقط التركيز إلى body عند الإغلاق. البنية الدلالية هي الفجوة الأوسع: 23 شاشة عميل بلا h1 لأن عنوان الشاشة في الرأس span، وقفز h1→h3 في 9 شاشات، وعناوين الإدارة كلها «FPT Admin». أنماط ARIA تحتاج ضبطاً في ثلاثة مواضع: بطاقات الحجم تدّعي radio وهي أزرار إرسال، تبويبات Radix بلا dir في RTL، وخانات المصادقة الثنائية بتسميات إنجليزية. أخطاء النماذج تُعلَن لكنها لا تنقل التركيز ولا تضبط aria-invalid في تسجيل الدخول والموافقة على الشروط، والنسخ لا يُعلَن. لا دعم لوضع الألوان القسرية، ولون وحده يحمل حالة «أقرب حدّ» في جدول الحسابات. التباين ممتاز على كل أزواج الرموز (الأدنى للنص الصامت 6.03:1)، ويبقى حدّ الأزرار الشبحية 1.19:1 ونص الزر الأساسي 4.06:1 على الحافة الحمراء المسطّحة نقطتي انتباه لا إخفاق.
- **evidence**: artifacts/ux-gates/gates-*.json; artifacts/real-device/coverage-20261030/*/coverage-receipt.json; .e2e/a11y-client.log, .e2e/a11y-admin.log, .e2e/a11y-quick.ts (local stack 127.0.0.1:3100/3200); artifacts/ux-audit-F4/evidence/*-focus-*.png, *-largetext-*.png, *-reducedmotion-*.png
- **recommendation**: الترتيب المقترح: A11Y-001 و A11Y-002 أولاً (S)، ثم عناوين h1/العناوين الإدارية (A11Y-003/004/005/012)، ثم ربط أخطاء النماذج ونقل التركيز (006/007/011)، ثم أنماط ARIA (008/009/010)، ثم بنود P3 دفعة واحدة في packages/ui.
- **effort**: M
- **skill**: ui-ux-pro-max §1 Accessibility; accessibility.md (WCAG 2.2 AA); web-interface-guidelines.md Accessibility + Focus States
- **files**: ```json
[
 "artifacts/ux-audit-F4/findings-a11y.json"
]
```
- **confidence**: high

### findings-devices.json

- **note**: عيوب قيست على الأجهزة الحقيقية في RD1 ولم تُصلح في هذه الجولة (ما أُصلح موجود في قسم «الإصلاحات» من التقرير): 1 × P1، 2 × P3.

### findings-mail.json

- **counts**: ```json
{
 "P0": 0,
 "P1": 8,
 "P2": 25,
 "P3": 12,
 "total": 45
}
```
- **byCategory**: ```json
{
 "نص": 23,
 "بصري": 4,
 "RTL": 5,
 "هوية": 5,
 "وصول": 4,
 "تقني": 4
}
```
- **verdictTable**: ```json
[
 {
  "template": "verify_code",
  "ar": "مقبول — الرمز واضح، لا رابط، تكرار الرمز في الـ preheader (MAIL-029)",
  "en": "مقبول — e-mail تنكسر على الهاتف (MAIL-036)"
 },
 {
  "template": "welcome",
  "ar": "مقبول",
  "en": "مقبول — اسم عربي في موضوع إنجليزي (MAIL-040)"
 },
 {
  "template": "account_ready",
  "ar": "مقبول — لا كلمة مرور، cTrader ID = البريد ورقم الحساب حاضران؛ تحدي/تقييم (MAIL-010)",
  "en": "مقبول — challenge/evaluation (MAIL-010)"
 },
 {
  "template": "breach",
  "ar": "يحتاج إصلاحاً — العنوان الثابت لكل أنواع الإنهاء (MAIL-008)، فراغ 5  % (MAIL-031)",
  "en": "يحتاج إصلاحاً — MAIL-008، «crossed a rule» (MAIL-023)"
 },
 {
  "template": "passed",
  "ar": "مقبول — «حدود السحب» ملتبسة (MAIL-011)",
  "en": "مقبول — «Congratulations» في الموضوع (MAIL-013)"
 },
 {
  "template": "payout_requested",
  "ar": "يحتاج إصلاحاً — المرجع ينكسر على الهاتف (MAIL-002)، التفاف (GMT+4) (MAIL-038)",
  "en": "مقبول — صياغة «is completed by» (MAIL-023)"
 },
 {
  "template": "payout_approved",
  "ar": "مقبول — بلا زر (مقصود)",
  "en": "مقبول"
 },
 {
  "template": "payout_paid",
  "ar": "يحتاج إصلاحاً — «20/80» مقلوبة (MAIL-001)، «crypto» خام (MAIL-004)، «تحت الزر» (MAIL-024)",
  "en": "يحتاج إصلاحاً — «crypto» خام (MAIL-004)"
 },
 {
  "template": "inactivity_warning",
  "ar": "مقبول — «مؤخراً» غامضة (MAIL-045)",
  "en": "مقبول"
 },
 {
  "template": "expiry_warning",
  "ar": "مقبول — بلا زر رغم الحاجة لفعل (MAIL-020)",
  "en": "مقبول — MAIL-020"
 },
 {
  "template": "kyc_result",
  "ar": "يحتاج إصلاحاً — سبب خام من المزوّد (MAIL-003)",
  "en": "يحتاج إصلاحاً — MAIL-003"
 },
 {
  "template": "affiliate_commission",
  "ar": "مقبول — لا List-Unsubscribe (MAIL-030)",
  "en": "مقبول"
 },
 {
  "template": "staff_invite",
  "ar": "مقبول — تكرار 48 ساعة (MAIL-032)",
  "en": "مقبول"
 },
 {
  "template": "ops_alert",
  "ar": "يحتاج إصلاحاً — نص إنجليزي في فقرة RTL (MAIL-005)، سطر «لماذا» للعملاء (MAIL-033)",
  "en": "مقبول — الخطورة والحقائق والزر واضحة"
 },
 {
  "template": "manual_proof_received",
  "ar": "مقبول — «مرجع الحساب» لمرجع طلب (MAIL-009)",
  "en": "مقبول — MAIL-009"
 },
 {
  "template": "manual_payment_confirmed",
  "ar": "يحتاج إصلاحاً — المرجع ينكسر على الهاتف (MAIL-002)، MAIL-009",
  "en": "مقبول — MAIL-009"
 },
 {
  "template": "manual_proof_rejected",
  "ar": "مقبول — ملاحظة الموظف بلغة واحدة (MAIL-027)",
  "en": "مقبول — MAIL-027"
 },
 {
  "template": "manual_payment_reminder",
  "ar": "مقبول — MAIL-009",
  "en": "مقبول — MAIL-009"
 },
 {
  "template": "email_change_notice",
  "ar": "مقبول — العنوان المقنّع معزول LTR بشكل صحيح",
  "en": "مقبول — موضوع مبني للمجهول (MAIL-039)"
 },
 {
  "template": "account_closed",
  "ar": "مقبول — بلا رابط (مقصود)، 90 يوماً صحيحة نحوياً",
  "en": "مقبول"
 }
]
```
- **subjectTable**: ```json
[
 {
  "template": "verify_code",
  "ar": "رمز التحقق الخاص بك في FPT: 482913",
  "en": "Your FPT verification code: 482913",
  "verdict": "جيد — محدد ويحمل الرمز"
 },
 {
  "template": "welcome",
  "ar": "مرحباً بك في FPT، خالد",
  "en": "Welcome to FPT, خالد",
  "verdict": "جيد"
 },
 {
  "template": "account_ready",
  "ar": "حساب التقييم FPT-50K-7F3K2 جاهز على cTrader",
  "en": "Your evaluation account FPT-50K-7F3K2 is ready on cTrader",
  "verdict": "جيد — المرجع والمنصة حاضران"
 },
 {
  "template": "breach",
  "ar": "الحساب FPT-50K-7F3K2: تجاوز الحد — أُوقف التداول",
  "en": "Account FPT-50K-7F3K2: limit breached — trading stopped",
  "verdict": "غير دقيق لغير الخسارتين (MAIL-008)"
 },
 {
  "template": "passed",
  "ar": "تهانينا: اجتاز الحساب FPT-50K-7F3K2 التقييم",
  "en": "Congratulations: account FPT-50K-7F3K2 passed the evaluation",
  "verdict": "محفّز سبام «Congratulations/تهانينا» (MAIL-013)"
 },
 {
  "template": "payout_requested",
  "ar": "تم استلام طلب السحب: 1,250.00 USD",
  "en": "Payout request received: 1,250.00 USD",
  "verdict": "جيد — المبلغ حاضر، المرجع غائب (MAIL-042)"
 },
 {
  "template": "payout_approved",
  "ar": "تمت الموافقة على السحب: 1,250.00 USD",
  "en": "Payout approved: 1,250.00 USD",
  "verdict": "جيد — المرجع غائب (MAIL-042)"
 },
 {
  "template": "payout_paid",
  "ar": "تمت معالجة دفعتك: 1,250.00 USD",
  "en": "Payout processed: 1,250.00 USD",
  "verdict": "جيد — «دفعتك» تخالف «السحب» (MAIL-012)"
 },
 {
  "template": "inactivity_warning",
  "ar": "الحساب FPT-50K-7F3K2: 5 أيام قبل الإغلاق لعدم النشاط",
  "en": "Account FPT-50K-7F3K2: 5 days before closure for inactivity",
  "verdict": "جيد — المرجع والمهلة"
 },
 {
  "template": "expiry_warning",
  "ar": "الحساب FPT-50K-7F3K2: 3 أيام متبقية في التقييم",
  "en": "Account FPT-50K-7F3K2: 3 days left in the evaluation",
  "verdict": "جيد"
 },
 {
  "template": "kyc_result",
  "ar": "لم تتم الموافقة على التحقق من الهوية",
  "en": "Identity verification not approved",
  "verdict": "مقبول — بلا سياق FPT"
 },
 {
  "template": "affiliate_commission",
  "ar": "عمولة شراكة جديدة: 48.00 USD",
  "en": "New affiliate commission: 48.00 USD",
  "verdict": "جيد"
 },
 {
  "template": "staff_invite",
  "ar": "أنت مدعو إلى لوحة موظفي FPT",
  "en": "You are invited to the FPT staff console",
  "verdict": "جيد"
 },
 {
  "template": "ops_alert",
  "ar": "تشغيل FPT · حرج: Engine behind",
  "en": "FPT operations · Critical: Engine behind",
  "verdict": "جيد — الخطورة والعنوان"
 },
 {
  "template": "manual_proof_received",
  "ar": "استلمنا إثبات تحويلك: FPT-CC7B3D73",
  "en": "We received your transfer proof: FPT-CC7B3D73",
  "verdict": "جيد — المرجع حاضر، المبلغ غائب (MAIL-042)"
 },
 {
  "template": "manual_payment_confirmed",
  "ar": "اعتُمد تحويلك: FPT-CC7B3D73",
  "en": "Your transfer is approved: FPT-CC7B3D73",
  "verdict": "جيد — MAIL-042"
 },
 {
  "template": "manual_proof_rejected",
  "ar": "لم يُقبل إثبات تحويلك: FPT-CC7B3D73",
  "en": "Your transfer proof was not accepted: FPT-CC7B3D73",
  "verdict": "جيد"
 },
 {
  "template": "manual_payment_reminder",
  "ar": "تذكير: طلبك FPT-CC7B3D73 بانتظار إثبات التحويل",
  "en": "Reminder: order FPT-CC7B3D73 is waiting for your transfer proof",
  "verdict": "جيد — واضح الفعل"
 },
 {
  "template": "email_change_notice",
  "ar": "طُلب تغيير بريدك في FPT",
  "en": "A change of your FPT e-mail was requested",
  "verdict": "مبني للمجهول (MAIL-039)"
 },
 {
  "template": "account_closed",
  "ar": "أُغلق حسابك في FPT",
  "en": "Your FPT account has been closed",
  "verdict": "جيد"
 }
]
```
- **sharedFindings**: ```json
[
 "MAIL-006",
 "MAIL-007",
 "MAIL-014",
 "MAIL-015",
 "MAIL-016",
 "MAIL-017",
 "MAIL-018",
 "MAIL-028",
 "MAIL-030",
 "MAIL-034",
 "MAIL-041",
 "MAIL-043"
]
```
- **verified**: ```json
{
 "templates": 20,
 "locales": 2,
 "widths": [
  640,
  390
 ],
 "darkMode": "محاكاة Chromium على verify_code-en وpayout_paid-ar وaccount_ready-ar: الخلفية #0A0A0A، النص أبيض، البلاطة تظهر بدل الـ lockup",
 "phone": "لا تمرير أفقي (scrollWidth = 390) في العينات المقاسة؛ الزر 48 px ارتفاعاً وعرضه 154–182 px",
 "secrets": "لا كلمة مرور في أي قالب؛ account_ready يحمل cTrader ID = البريد ورقم الحساب والخادم فقط",
 "urlsInHtml": "لا رابط نصي في أي HTML؛ النسخة النصية تحمل الروابط تحت تسمية الزر فقط"
}
```
- **overall**: بنية البريد متينة ومتسقة عبر القوالب العشرين: أرضية ورقية، lockup رسمي، عنوان واحد، بطاقة حقائق، زر واحد، تذييل ثابت، ولا تسريب لأي سر؛ وبريد بيانات cTrader يحمل المعرّف ورقم الحساب فقط كما تقتضي القاعدة. لم يُرصد خطأ منتجي (P0) في الأرقام 10 %/5 %/9 %/31 يوماً/80-20، لكن ثمانية عيوب P1 تمسّ ما يراه العميل فعلاً: انقلاب «80/20» إلى «20/80» بالعربية، انكسار مراجع الحساب والطلب على الهاتف، رموز خام («crypto»، سبب رفض الهوية من المزوّد) في نص العميل، نص التنبيه الإنجليزي داخل فقرة RTL، والعبارة التنظيمية المؤقتة الظاهرة للعملاء في كل بريد. أهم ما يستحق التحقق في الإنتاج فوراً هو غياب الشعار في الرسائل الصادرة من تطبيقات Next (لقطة الأدمن على admin.fptraders.app تُظهر رأساً نصياً). على مستوى الوصول تنزل أحجام التذييل (11 px) والملاحظة (14 px) وسطر «لماذا» (13 px) دون سقوف F1، والعنوان ليس h1، والزر على الهاتف ليس بعرض كامل. لغوياً، الإنجليزية تحتاج تمريراً تحريرياً لجمل ركيكة، والعربية تحتاج توحيد مصطلح السحب/الصرف وتصحيح «حدود السحب» إلى الخسارة. الموضوعات عموماً محددة وتحمل المرجع أو المبلغ، وتبقى التوصية بإضافة preheader حقيقي بدل تكرار الموضوع.

### findings-technical.json

- **counts**: ```json
{
 "total": 51,
 "bySeverity": {
  "P1": 2,
  "P2": 11,
  "P3": 38
 },
 "byCategory": {
  "تقني": 29,
  "هوية": 3,
  "أداء": 10,
  "وصول": 7,
  "RTL": 2
 }
}
```
- **productionHeadersObserved**: ```json
{
 "https://fptraders.app (/ar, 200 document)": {
  "content-security-policy": "default-src 'self'; script-src 'self' 'nonce-…' 'strict-dynamic'; style-src 'self' 'nonce-…'; style-src-elem 'self' 'nonce-…'; style-src-attr 'unsafe-inline'; img-src 'self' data: blob:; font-src 'self'; connect-src 'self'; frame-src 'self'; worker-src 'self'; manifest-src 'self'; object-src 'none'; base-uri 'self'; form-action 'self'; frame-ancestors 'none'; upgrade-insecure-requests",
  "strict-transport-security": "max-age=63072000",
  "x-frame-options": "DENY",
  "x-content-type-options": "nosniff",
  "referrer-policy": "strict-origin-when-cross-origin",
  "permissions-policy": "camera=(), microphone=(), geolocation=(), payment=(), usb=()",
  "cross-origin-opener-policy": "same-origin",
  "x-dns-prefetch-control": "off",
  "cache-control": "private, no-cache, no-store, max-age=0, must-revalidate",
  "set-cookie": "NEXT_LOCALE=ar; Path=/; SameSite=lax (بلا Secure/HttpOnly)",
  "link": "hreflang ar/en/x-default; preload image /brand/fpt-logo-icon-rounded.jpeg; preload style (nonced)"
 },
 "https://fptraders.app/sw.js": {
  "content-security-policy": "default-src 'none'; connect-src 'self'; script-src 'self'; base-uri 'none'; frame-ancestors 'none'; form-action 'none'",
  "cache-control": "no-cache",
  "content-type": "application/javascript; charset=utf-8"
 },
 "https://fptraders.app/api/* (مثال /api/health → 404)": {
  "content-security-policy": "default-src 'none'; frame-ancestors 'none'; base-uri 'none'; form-action 'none'",
  "cache-control": "private, no-cache, no-store, max-age=0, must-revalidate"
 },
 "https://fptraders.app/manifest.webmanifest": {
  "content-type": "application/manifest+json; charset=utf-8",
  "cache-control": "public, max-age=0, must-revalidate"
 },
 "https://fptraders.app/brand/* (jpeg/png)": {
  "cache-control": "public, max-age=0, must-revalidate"
 },
 "https://fptraders.app/_next/static/* (css, woff2)": {
  "cache-control": "public,max-age=31536000,immutable"
 },
 "https://admin.fptraders.app (/ar → 307 /ar/signin; /ar/<404>)": {
  "content-security-policy": "نفس سياسة العميل (nonce + strict-dynamic، style-src-attr 'unsafe-inline')",
  "strict-transport-security": "max-age=63072000",
  "x-frame-options": "DENY",
  "x-content-type-options": "nosniff",
  "referrer-policy": "strict-origin-when-cross-origin",
  "permissions-policy": "camera=(), microphone=(), geolocation=(), payment=(), usb=()",
  "cross-origin-opener-policy": "same-origin",
  "cache-control": "private, no-cache, no-store, max-age=0, must-revalidate",
  "set-cookie": "NEXT_LOCALE=ar; Path=/; SameSite=lax (مكرر مرتين على 307)",
  "404 document": "18 <script> بلا nonce (TEC-003)"
 },
 "https://admin.fptraders.app/api/*": {
  "content-security-policy": "default-src 'none'; frame-ancestors 'none'; base-uri 'none'; form-action 'none'"
 }
}
```
- **evidenceBase**: ```json
{
 "realDevice": "artifacts/real-device/coverage-20261030/iphone-15-pro-max/coverage-receipt.json (6 شاشات عامة، رسالة style-src ×6، 404 قبل الإصلاح ×19 خطأ)",
 "uxGates": "artifacts/ux-gates/*.json — العميل 73 شاشة ×4 مشاريع: فشل واحد مشترك (إطار البريد المعزول) + فيض 360 في integrations؛ الإدارة 23 شاشة ×3: PASS",
 "lighthouse": "artifacts/ux-audit-F4/lighthouse/*.json — Accessibility 99 (image-redundant-alt)، Best Practices 100، Performance 79–99 (signin TBT 850 ms)؛ تقارير home/account/payouts قاست صفحة الدخول"
}
```
- **posture**: ```json
[
 "الوضع الأمني لواجهة المتصفح متين في أساسه: CSP بـ nonce + strict-dynamic بلا unsafe-inline للسكربتات، frame-ancestors none مع X-Frame-Options، كوكيز الجلسة HttpOnly/Secure/SameSite (Lax للعميل وStrict للموظفين) مع صف جلسة قابل للإلغاء وتدوير عند كل دخول، مهل خمول/مطلقة واضحة، وإعادة تحقق للموظفين قبل الإجراءات الحساسة؛ لا تسريب تقني في صفحات الخطأ.",
 "العيب الأعلى هو عامل الخدمة: يخزّن HTML الصفحات المصادَق عليها متجاهلاً no-store ولا يُفرَغ عند تسجيل الخروج، فتُعرض بيانات الحساب دون اتصال بعد الخروج على جهاز مشترك، والبديل دون اتصال صفحة متصفح خام بلا هوية.",
 "في التدفقات الأساسية ثلاث ثغرات وظيفية مؤكدة من المصدر: الإرسال الضمني بمفتاح Enter في نموذج الصرف يتخطى المراجعة أو يفشل بصمت، وانتهاء الجلسة أثناء النماذج يُفقد البيانات بلا تفسير (والمفتاح المترجم موجود وغير مستخدم)، وReact 19 يفرّغ حقول نماذج المصادقة بعد كل خطأ.",
 "أداءً، كل صفحة تشحن القاموس كاملاً (243 KB HTML للهبوط) وأربعة أوزان من الخط العربي بلا تحميل مسبق، ولا هياكل تحميل لأي مسار؛ هذه الثلاثة تفسّر TBT المرتفع على الهاتف أكثر من أي شيء في الكود المرئي.",
 "رسالة Safari iOS عن ورقة التنسيق ليست عيباً في style-src-attr (مدعوم منذ 15.4 ولا يُبلَّغ عنه أصلاً مع unsafe-inline)؛ المصدر عنصر <style> غير موجود في الشجرة المُصيَّرة ولا في المصدر، فالحسم يحتاج report-uri لا تعديلاً في السياسة.",
 "صفحة 404 الجذرية للوحة الموظفين ما زالت تشحن سكربتات بلا nonce (الإصلاح RD1 شمل العميل فقط)، والبيان/الأيقونات والتخزين تحت /brand تحتاج ضبطاً صغيراً ليكتمل تثبيت PWA على Android بالهوية الصحيحة."
]
```

### findings-text.json

- **counts**: ```json
{
 "P0": 2,
 "P1": 12,
 "P2": 34,
 "P3": 38,
 "total": 86
}
```
- **automated_checks**: ```json
{
 "key_parity_ar_en": "PASS (3029 = 3029)",
 "duplicate_keys_raw": "PASS (0 في ar.json و0 في en.json)",
 "interpolation_vars_parity": "PASS",
 "empty_values": "PASS",
 "mt4_mt5_metatrader": "0 ذكر",
 "emoji": "0",
 "iso_date_patterns_in_copy": "0 (التواريخ في config/legal.ts فقط كبيانات)",
 "arabic_indic_digits": "0",
 "three_dots_vs_ellipsis": "0 «...» — «…» متسقة",
 "double_spaces_or_space_before_punct": "0",
 "percent_spacing": "متسقة «10 %» (116 موضعاً، 0 بلا مسافة) — مطابقة للمعجم",
 "latin_comma_qmark_in_arabic_prose": "0 (باستثناء قوائم معرّفات تقنية — TXT-063)",
 "hamza_taa_marbuta_alef_maqsura": "لم تُرصد أخطاء؛ التنوين على الألف «اً» متسق (194 موضعاً)",
 "e-mail_spelling_en": "متسق «e-mail/E-mail» (64) — «email» فقط كمتغير {email}",
 "sign_in_vs_login_en": "متسق «Sign in»؛ «Login» فقط كتسمية رقم دخول cTrader (TXT-023)",
 "glossary_product_facts": "10 % / 5 % / 9 % / 31 يوماً / 5 و10 أيام / 80 % / 14 يوماً / 10 حسابات / cTrader فقط: مطابقة. الاستثناءات: أساس الخسارة اليومية (TXT-012) والحد الأقصى للدفعة (TXT-006)"
}
```
- **term_variants**: ```json
[
 {
  "term": "payout (الدفعة)",
  "variants": {
   "الدفعة/الدفعات": [
    "client.nav.payouts",
    "client.screen.payouts",
    "client.statement.*",
    "dashboard.primary.requestPayout",
    "payouts.eligibleLead",
    "client.rules.fundedDays",
    "mail.payout_paid.title",
    "mail.account_closed.body",
    "profile.delete.body",
    "health.alerts.payout_requested.*"
   ],
   "السحب/السحوبات/سحب الأرباح": [
    "payouts.title",
    "payouts.eyebrow",
    "payouts.new",
    "payouts.emptyBody",
    "payouts.form.channel",
    "payouts.certificate",
    "certificates.kindPayout",
    "engine.certificate.payout",
    "mail.payout_requested.*",
    "mail.payout_approved.*",
    "mail.welcome.body",
    "mail.affiliate_commission.body",
    "notifications.payout_*",
    "profile.kyc.*",
    "profile.nameHint",
    "admin.nav.payouts",
    "overview.openPayouts",
    "accounts.tabs.payouts",
    "payoutsAdmin.title",
    "products.rule.share/cycle/minMax",
    "products.builder.f.*",
    "audit.kinds.payout",
    "finance.openLiabilities",
    "kycAdmin.*",
    "deletionsAdmin.facts.openPayouts",
    "simulator.demoReset.confirmBody",
    "seed terms/privacy/risk"
   ],
   "الصرف": [
    "mail.common.states.processed",
    "client.states.processed",
    "client.certificate.amount",
    "admin.result.approved_manual",
    "admin.result.not_approved",
    "admin.events.payout_execution_recorded",
    "settings.payoutChannels.*",
    "mailPreview.templates.payout_paid"
   ]
  }
 },
 {
  "term": "drawdown ↔ withdrawal (السحب)",
  "variants": {
   "السحب = drawdown": [
    "mail.passed.body",
    "client.states.drawdownWarning",
    "client.finance.legendBand"
   ],
   "السحب = payout": [
    "payouts.new",
    "payouts.form.channel",
    "…"
   ]
  }
 },
 {
  "term": "evaluation / challenge",
  "variants": {
   "تحدي": [
    "client.nav.challenge",
    "shop.title",
    "shop.buy",
    "shop.rulesMeta",
    "client.home.newChallenge",
    "dashboard.primary.newChallenge",
    "purchases.*",
    "mail.account_ready.title",
    "admin.events.order_created"
   ],
   "تقييم": [
    "client.header.buy",
    "shop.productTitle",
    "dashboard.emptyCta",
    "breach.newChallenge",
    "dashboard.status.breached.next",
    "mail.account_ready.subject",
    "status.evaluation",
    "client.states.evaluation"
   ]
  }
 },
 {
  "term": "breach (الحالة)",
  "variants": {
   "تجاوز الحد": [
    "client.states.breached",
    "mail.common.states.breached",
    "mail.breach.*"
   ],
   "مخالفة": [
    "dashboard.status.breached.title",
    "breach.eyebrow",
    "breach.curve",
    "products.sim.outcome.breached",
    "admin.labels.endReason.breach",
    "admin.nav.breaches"
   ],
   "مخروق": [
    "status.breached"
   ],
   "مخالِف": [
    "admin.events.accountStatus.breached"
   ]
  }
 },
 {
  "term": "breach report",
  "variants": {
   "تقرير الإيقاف": [
    "dashboard.primary.viewBreach"
   ],
   "تقرير المخالفة": [
    "breach.eyebrow",
    "engine.report.title",
    "dashboard.status.breached.next",
    "notifications.ending_*"
   ],
   "تقرير التجاوز": [
    "mail.breach.button"
   ]
  }
 },
 {
  "term": "equity",
  "variants": {
   "حقوق الحساب": [
    "dashboard.equity",
    "breach.*",
    "engine.report.curve",
    "accounts.*",
    "risk.*",
    "breaches.*"
   ],
   "الرصيد (منحنى الرصيد)": [
    "client.finance.equityCurve",
    "dashboard.chart"
   ],
   "glossary PROPOSED": [
    "الرصيد الحالي",
    "رصيد الحساب"
   ]
  }
 },
 {
  "term": "daily loss base",
  "variants": {
   "رصيد بداية اليوم": [
    "mail.breach.kinds.daily_loss",
    "client.finance.dailyBase",
    "client.rules.dailyValue",
    "accounts.gauge.dailyBase",
    "accounts.figures.dayStart"
   ],
   "حقوق الحساب عند 00:00": [
    "breach.kinds.daily_loss",
    "accounts.state.dayStart",
    "seed terms §3"
   ]
  }
 },
 {
  "term": "daily / max loss labels",
  "variants": {
   "AR": [
    "الخسارة اليومية",
    "الخسارة اليومية القصوى",
    "حدّ الخسارة اليومي",
    "حدّ الخسارة اليومية",
    "الخسارة الإجمالية",
    "الخسارة الإجمالية القصوى",
    "الخسارة القصوى",
    "الخسارة الأقصى",
    "حدّ الخسارة الأقصى",
    "حدّ الخسارة الإجمالي"
   ],
   "EN": [
    "Daily Drawdown",
    "Daily loss",
    "daily drawdown",
    "Max Drawdown",
    "Maximum loss",
    "Max loss",
    "Overall loss",
    "overall drawdown"
   ]
  }
 },
 {
  "term": "daily reset",
  "variants": {
   "إعادة الضبط": [
    "dashboard.reset",
    "dashboard.resetIn",
    "client.rule.resetAt",
    "client.rules.dailyValue",
    "profile.timezoneHint",
    "admin.events.reset_daily"
   ],
   "التصفير": [
    "client.rule.nextReset",
    "mail.account_ready.note",
    "health.reset.*"
   ],
   "إعادة التعيين": [
    "settings.reset.*",
    "accounts.state.nextReset",
    "accounts.actions.recompute*"
   ]
  }
 },
 {
  "term": "close (account/positions)",
  "variants": {
   "إغلاق/أُغلق/مغلق": [
    "status.closed",
    "mail.inactivity_warning.*",
    "mail.breach.body",
    "admin.events.breach_inactive",
    "deletionsAdmin.*",
    "shop.transfer.closedTitle"
   ],
   "إقفال/أُقفل/مُقفل": [
    "dashboard.status.closed.title",
    "dashboard.status.inactive.body",
    "notifications.ending_*",
    "breach.events.closes",
    "accounts.actions.close",
    "breach.nextBody"
   ]
  }
 },
 {
  "term": "provisioning",
  "variants": {
   "التزويد": [
    "status.provisioning",
    "shop.steps.provision",
    "shop.orderStatus.provisioned",
    "admin.result.granted",
    "health.components.provisioner",
    "grants.description"
   ],
   "التجهيز": [
    "dashboard.status.provisioning.title",
    "admin.events.accountStatus.provisioning",
    "orders.orderStatus.provisioned",
    "shop.ret.provisioning",
    "orders.provisioning"
   ],
   "التسليم": [
    "purchases.status.provisioned",
    "dashboard.delivered"
   ]
  }
 },
 {
  "term": "approve",
  "variants": {
   "اعتماد": [
    "shop.orderStatus.awaiting_review",
    "paymentsAdmin.*",
    "admin.payou
```
- **tone_note**: اللغة العربية سليمة إملائياً إلى حدّ لافت: لا أخطاء همزة أو تاء مربوطة أو ألف مقصورة، التنوين والترقيم العربي («،» «؛» «؟») متسقان، والأرقام لاتينية و«10 %» بمسافة كما في المعجم، ولا ذكر لـ MT4/MT5 ولا رموز تعبيرية ولا تواريخ ISO. المشكلة الحقيقية ليست في النحو بل في (1) المصطلح: مفهوم الدفعة وحده بأربع كلمات على شاشات متجاورة، و«السحب» يعني drawdown وwithdrawal معاً، والحالة الواحدة للحساب بثلاث كلمات حسب المكوّن؛ (2) النبرة: تناوب غير محكوم بين «نحن» الودّية والمبني للمجهول الرسمي داخل الرسالة الواحدة، وبين صيغة الأمر والمصدر في أزرار الفعل نفسه؛ (3) العدّ: غياب قواعد الجمع يجعل التمييز العددي خاطئاً في عشرات المفاتيح؛ (4) الوقائع والقانون: نصوص قانونية وبيانات مخاطر واسم كيان مؤلَّفة داخلياً تُعرض للعميل وتُقبل عند الدفع خلافاً لمصفوفة النصوص التنظيمية، وعبارة «النص التنظيمي: بانتظار قرار» تُطبع في كل بريد. الإنجليزية جيدة عموماً مع تذبذب بريطاني/أمريكي وفي حالة الأحرف وعلامات الاقتباس. السجل المخاطِب (أنت) ثابت بلا «حضرتك» ولا عامية، وهذا جيد.

### findings-visual-admin.json

- **app**: admin
- **scope**: المراجعة البصرية / تجربة المشغّل للوحة الإدارة — 36 شاشة Chromium 1440 (إنتاج)، 8 شاشات WebKit + Firefox، أدلة F4 المحلية (دخول/404/ممنوع/قوائم/تركيز/إعدادات/حسابي/بريد)، 10 لقطات هاتف 390 من artifacts/ux-after (F1، قد تكون قديمة)، ومراجعة مصدر @fpt/ui وصفحات apps/admin
- **counts**: ```json
{
 "total": 86,
 "P0": 1,
 "P1": 5,
 "P2": 27,
 "P3": 53,
 "by_category": {
  "تقني": 5,
  "RTL": 9,
  "بصري": 44,
  "نص": 20,
  "هوية": 4,
  "وصول": 4
 }
}
```
- **per_screen_verdict**: ```json
[
 {
  "screen": "signin",
  "verdict": "جيد مع ملاحظات",
  "notes": "رسائل الخطأ والحد من المحاولات واضحة؛ فقاعة تحقق إنجليزية، زر «متابعة»، بطاقة غير ممركزة (VAD-033/034/035)"
 },
 {
  "screen": "overview",
  "verdict": "مقبول",
  "notes": "بطاقات وطابور وأحداث وصحة؛ الطابور ليس أولاً، أسماء خدمات خام، أسهم غير معكوسة، 21 حساباً إنذاراً كاذباً (VAD-004/009/027/028)"
 },
 {
  "screen": "accounts",
  "verdict": "مقبول",
  "notes": "جدول RTL سليم ومبالغ mono؛ كثافة منخفضة بسبب التواريخ، فلاتر بلا عناوين، لا فرز ظاهر، لا ترقيم (VAD-019/020/030/039)"
 },
 {
  "screen": "account",
  "verdict": "ضعيف",
  "notes": "محتوى تبويب «نظرة» LTR وتاريخ مشوّه (VAD-002)؛ المقاييس والمخالفات ومنطقة الخطر بنية صحيحة"
 },
 {
  "screen": "kyc",
  "verdict": "جيد",
  "notes": "حالة فراغ واضحة؛ تنقصه رقيقة الأرشيف (VAD-077)"
 },
 {
  "screen": "risk",
  "verdict": "مقبول",
  "notes": "فرز نشط ظاهر، تحديث 5 ثوانٍ؛ بلا تدرّج لوني للخطر وتناقض مع النظرة العامة (VAD-004/029)"
 },
 {
  "screen": "breaches",
  "verdict": "مقبول",
  "notes": "فلتر «نعم/لا» غامض، شارة «تم» بأيقونة X، حد 0.00 (VAD-019/055/056)"
 },
 {
  "screen": "breach",
  "verdict": "ضعيف",
  "notes": "الرسم بنصوص ضخمة في المحركات الثلاثة (VAD-003)؛ بقية الصفحة (التقرير، الصفقات، تسلسل الإقفال، الإلغاء باعتمادين) ممتازة"
 },
 {
  "screen": "payouts",
  "verdict": "مقبول",
  "notes": "متتبع في كل صف يقصّ الجدول على 1280 ويخطئ الخطوة (VAD-010/011)؛ الافتراضي ليس «ينتظر اعتمادي»"
 },
 {
  "screen": "payout",
  "verdict": "جيد",
  "notes": "أفضل صفحة تفاصيل: إجراء أساسي واحد، تنبيه الاعتماد الثاني، الفحوصات الآلية، منطقة الخطر؛ ملاحظة إنجليزية وحقول تنفيذ فارغة (VAD-043/083)"
 },
 {
  "screen": "orders",
  "verdict": "ضعيف",
  "notes": "حجم المنتج ×100 (VAD-001)، طريقة مكررة، فلاتر بلا عناوين"
 },
 {
  "screen": "order",
  "verdict": "مقبول",
  "notes": "ثلاث بطاقات واضحة؛ لا سبب فشل ولا إجراء (VAD-032)"
 },
 {
  "screen": "payments",
  "verdict": "ضعيف",
  "notes": "نفس خطأ ×100 (VAD-001)، فرز أقدم أولاً صحيح"
 },
 {
  "screen": "payment",
  "verdict": "جيد",
  "notes": "إثبات ومبلغ واعتماد بوضوح؛ فراغ فوق بيانات الطلب، قوس مكسور، «KB · PNG 74» (VAD-013/014/041)"
 },
 {
  "screen": "grant",
  "verdict": "جيد",
  "notes": "نموذج نظيف بإعادة تحقق؛ قائمة منتج بلا placeholder (VAD-063)"
 },
 {
  "screen": "products",
  "verdict": "ضعيف",
  "notes": "الجدول LTR بالكامل (VAD-002) و10 أزرار في 5 صفوف (VAD-025)"
 },
 {
  "screen": "finance",
  "verdict": "جيد",
  "notes": "بطاقات + جداول CSV؛ جموع خاطئة وشرارة بنقطتين وصياغة سياسة الاسترداد (VAD-012/031/058)"
 },
 {
  "screen": "coupons",
  "verdict": "جيد",
  "notes": "حالة فراغ سليمة (VAD-062)"
 },
 {
  "screen": "affiliates",
  "verdict": "مقبول",
  "notes": "عمود الفئة يلتف 3 أسطر، سهم وترتيب مبلغ (VAD-045/046)"
 },
 {
  "screen": "affiliate",
  "verdict": "مقبول",
  "notes": "نسبة مكررة، قناة خام، أسهم غير معكوسة (VAD-009/044/047)"
 },
 {
  "screen": "mail",
  "verdict": "مقبول",
  "notes": "موضوع إنجليزي مفكك، قوالب بلا ترجمة (VAD-008/024)"
 },
 {
  "screen": "mail-detail",
  "verdict": "مقبول",
  "notes": "إطار + نص + تفاصيل جيدة؛ سطر تنظيمي نائب مرئي للعميل (VAD-006)، تبويبات LTR"
 },
 {
  "screen": "fraud",
  "verdict": "مقبول",
  "notes": "≥ تُقرأ ≤ (VAD-007)، ثلاثة صفوف فلاتر بلا عناوين، خطورة بلا تدرّج"
 },
 {
  "screen": "fraud-detail",
  "verdict": "ضعيف",
  "notes": "بطاقة أدلة فارغة (VAD-005)، دولة AE خام"
 },
 {
  "screen": "fraud-links",
  "verdict": "جيد",
  "notes": "بطاقات واضحة؛ وحدات h/min ومعرّف مبتور (VAD-048/070)"
 },
 {
  "screen": "audit",
  "verdict": "ضعيف",
  "notes": "تغذية بدل جدول، إجراءات غير مترجمة، لا نطاق تاريخ (VAD-016/017)"
 },
 {
  "screen": "staff",
  "verdict": "مقبول",
  "notes": "ثلاثة أزرار لكل صف (VAD-018)؛ الهاتف أفضل من المكتب"
 },
 {
  "screen": "staff-detail",
  "verdict": "مقبول",
  "notes": "جلسات محاذاة للأسفل، سجل دخول منخفض الكثافة، بلا إجراءات (VAD-014/080)"
 },
 {
  "screen": "settings",
  "verdict": "مقبول",
  "notes": "ترتيب LTR للبطاقات والتبويبات (VAD-002)، تقصّ التبويبات على الهاتف (VAD-023)، نصوص ثنائية الاتجاه مفككة"
 },
 {
  "screen": "deletions",
  "verdict": "جيد",
  "notes": "حالة فراغ سليمة؛ فلتر بعنصر واحد (VAD-081)"
 },
 {
  "screen": "simulator",
  "verdict": "مقبول",
  "notes": "تحذير وضع المحاكاة ممتاز؛ backticks وأسماء خام (VAD-027/049)"
 },
 {
  "screen": "integrations",
  "verdict": "مقبول",
  "notes": "runbook غني لكنه جدار نص ثنائي الاتجاه (VAD-050/068)"
 },
 {
  "screen": "health",
  "verdict": "جيد",
  "notes": "أسماء معرّبة وشبكة واضحة؛ زر أساسي غير مستحق وعدّاد معزول (VAD-060/061/082)"
 },
 {
  "screen": "me",
  "verdict": "ضعيف",
  "notes": "أعمدة محاذاة للأسفل، صلاحيات خام، 15 جلسة بلا إجراء جماعي (VAD-014/015/066)"
 },
 {
  "screen": "forbidden",
  "verdict": "جيد",
  "notes": "رسالة ومخرج واضحان؛ شريط فارغ (VAD-073)"
 },
 {
  "screen": "404",
  "verdict": "مقبول",
  "notes": "ثنائي اللغة بلا إطار (VAD-067)"
 }
]
```
- **cross_browser**: ```json
[
 "Chromium / WebKit / Firefox 1440: التخطيط والخطوط والشارات والحقول والقوائم المنسدلة (select مخصص) متطابقة في الشاشات الثماني المقارنة (overview, accounts, payouts, payment, settings, mail-detail, integrations, health) وكذلك breach وgrant — لا فروق في المقاييس أو الالتفاف.",
 "Firefox payouts: الصف الأول ملوّن بغسلة الأحمر البرندي — هذه حالة التحويم (مؤشر WebDriver مستقر فوق الصف) وليست فرقاً في التصيير؛ كشفت أن تحويم الجداول أحمر في كل المتصفحات (VAD-021).",
 "Firefox mail-detail: داخل إطار البريد يختلف خط المبلغ (mono احتياطي أرفع) ولون حالة «قيد المعالجة» (كهرماني مقابل بني داكن في Chromium/WebKit) وحدّ الإطار أوضح (VAD-072).",
 "رسم المخالفة بنصوص ضخمة متطابق في الثلاثة — خلل تصميم لا متصفح (VAD-003).",
 "الأزمنة النسبية تختلف بين اللقطات («الآن» / «قبل دقيقتين» / «قبل 3 دقائق») لاختلاف وقت الالتقاط فقط."
]
```
- **strongest_10**: ```json
[
 "صفحة تفاصيل السحب: إجراء أساسي واحد، تنبيه «بانتظار اعتماد ثانٍ»، فحوصات آلية بشارات، سجل الاعتمادات، منطقة خطر منفصلة للرفض — نموذج يجب تعميمه.",
 "حالات الفراغ (التحقق من الهوية، الكوبونات، طلبات الحذف، التنبيهات) بأيقونة وعنوان ووصف متسقة.",
 "صفحة المراجعة اليدوية: المبلغ البطل + الإثبات بحجمه + بيانات الطلب في شاشة واحدة، وزر اعتماد واحد.",
 "الفلاتر روابط GET تحفظ الحالة في URL (قابلة للمشاركة، بلا JavaScript) — متوافق مع WIG «URL reflects state».",
 "الجداول: المبالغ mono tabular بنهاية موحدة والرمز USD في رأس العمود، والفرز النشط ظاهر حيث يُمرَّر (المخاطر، الاحتيال، المدفوعات).",
 "صفحة المخالفة: التقرير بالأرقام، الصفقات لحظة المخالفة، تسلسل الإقفال، إلغاء باعتمادين — سرد كامل للحادثة.",
 "الشريط الجانبي: مجموعات، حالة نشطة بشريط أحمر وأيقونة، تذييل بالاسم والدور واللغة وملاحظة المحاكاة؛ شريط هاتف من 5 عناصر مع ورقة «المزيد».",
 "ظلال وعمق: إستراتيجية واحدة (حدود hairline على أرضية الألواح) بلا ظلال مختلطة — متسقة في كل الشاشات.",
 "صفحة صحة النظام: أسماء معرّبة، نبضات، طابور الأوامر، ساعة المحاكي — واضحة لمشغّل غير تقني.",
 "إشارات المحاكاة («بيئة محاكاة — لا أموال حقيقية»، «وضع المحاكاة» في المحاكي، شارات mock/sim في الإعدادات) ثابتة ومتسقة."
]
```
- **weakest_10**: ```json
[
 "حجم المنتج ×100 في الطلبات والمدفوعات اليدوية (5,000,000 USD) — خطأ مالي ظاهر (VAD-001).",
 "Radix Tabs بلا اتجاه: جداول ومحتوى وبطاقات LTR داخل العربية في أربع شاشات (VAD-002).",
 "رسم المخالفة بنصوص ضخمة وخط سميك (VAD-003).",
 "ثلاث دلالات لـ«القرب من الحد» وإنذار كاذب لـ 21 حساباً (VAD-004).",
 "بطاقة أدلة الاحتيال فارغة ومطوية (VAD-005).",
 "سطر «النص التنظيمي: بانتظار قرار» في بريد العميل (VAD-006).",
 "≥ تُقرأ ≤ في فلاتر الخطورة، والموضوع الإنجليزي مفكك في البريد، وأسهم غير معكوسة (VAD-007/008/009).",
 "صيغ الجمع العربية الثابتة في كل سطور العدّ (VAD-012).",
 "كثافة الجداول: تواريخ من 3 أسطر، متتبع في كل صف، 3 أزرار في كل صف موظف، سجل تدقيق كتغذية (VAD-010/017/018/020).",
 "أعمدة محاذاة للأسفل في حسابي/المراجعة/تفاصيل الموظف وصلاحيات خام (VAD-014/015)."
]
```
- **operator_experience**: لوحة الإدارة مبنية على أساس صحيح: هيكل شاشة ثابت (عنوان، لِيد، فلاتر، جدول/بطاقات)، مكوّنات F1 الموحدة، أمانة في عرض الأرقام بالخط الأحادي ورمز العملة، وإشارات محاكاة لا تُنسى. صفحات القرار (السحب، المراجعة اليدوية، المخالفة) هي أنضج ما فيها وتجيب على سؤال المشغّل الأول: ما الذي ينتظرني وماذا أفعل. لكن الطبقة الأفقية (التبويبات، التواريخ، الجموع، الاتجاه) تحمل أخطاء منهجية تتكرر في شاشات عديدة، وأخطرها خطأ عرض مالي واحد يكفي ليُفقد الثقة في الأرقام كلها. كثافة المعلومات أقل مما يليق بأداة مشغّل يومية — الجداول تعرض 6 صفوف في الشاشة بسبب تواريخ ثلاثية الأسطر ومتتبعات وأزرار داخل الصفوف، بينما نسخة الهاتف اختارت قوائم ⋯ وبطاقات أنظف من المكتب. الإشارات التشغيلية غير متسقة: النظرة العامة تُنذر بـ 21 حساباً ولوحة المخاطر تقول صفراً، والطوابير لا تظهر كعدادات في الشريط الجانبي. النصوص بحاجة إلى تمشيط لغوي واحد (الجموع، القوالب غير المترجمة، الرموز الخام، علامات Markdown، الاتجاه داخل الجمل). مقارنةً بـ TWO Markets، ينقص هذه اللوحة التكثيف وترقيم الخادم وتدرّج الخطر اللوني وشريط علوي يعمل كمسار تنقل بدل تكرار العنوان. أكثر ما يُقلق قبل الإطلاق: إصلاح VAD-001 وVAD-002 وVAD-006 وVAD-003 (أربعة تعديلات صغيرة إلى متوسطة) يرفع الانطباع العام من «نموذج أولي متقدم» إلى «أداة إنتاج»، ثم مرحلة كثافة وجموع وأسماء موحدة.

### findings-visual-client.json

- **counts**: ```json
{
 "bySeverity": {
  "P0": 2,
  "P1": 13,
  "P2": 23,
  "P3": 42
 },
 "byCategory": {
  "بصري": 48,
  "نص": 17,
  "RTL": 6,
  "تقني": 4,
  "وصول": 3,
  "هوية": 2
 },
 "total": 80,
 "evidenceNote": "الدفعة الأولى من artifacts/ux-audit-F4/evidence/*-phone.png كانت بعرض 1280؛ أُعيد الالتقاط بـ 390×844 (780 px) وأُعيد التحقق من كل نتيجة تستشهد بها (VCL-005/006/010/011/012/025/029/033/034/041/042/048/075/079/080). خمس لقطات عربية للدخول خرجت فارغة أو بلا CSS (VCL-073) فاستُبدلت بالنسخة الإنجليزية 780 أو بسطح المكتب."
}
```
- **perScreen**: ```json
{
 "landing": "issues: VCL-003, VCL-008, VCL-062",
 "signin": "issues: VCL-030, VCL-031, VCL-032, VCL-068",
 "signup": "issues: VCL-031, VCL-068, VCL-069, VCL-077",
 "reset": "ok (OTP واضح، التركيز على الخانة الأولى) — VCL-068",
 "home": "issues: VCL-012, VCL-016, VCL-027, VCL-028, VCL-039, VCL-046, VCL-059, VCL-060, VCL-061",
 "home-largetext / reducedmotion": "ok — VCL-075",
 "account": "issues: VCL-004, VCL-026, VCL-027",
 "account-rules": "issues: VCL-004, VCL-025, VCL-029, VCL-079",
 "account-credentials": "issues: VCL-004, VCL-016, VCL-070, VCL-071",
 "account-trades / trades": "issues: VCL-040",
 "account-funded": "issues: VCL-016",
 "account-breached / breach": "issues: VCL-056, VCL-057, VCL-058",
 "shop": "issues: VCL-023",
 "shop-product": "issues: VCL-050, VCL-051",
 "checkout": "issues: VCL-001 (P0), VCL-022, VCL-047, VCL-078",
 "checkout-transfer": "issues: VCL-002 (P0), VCL-013, VCL-017, VCL-018, VCL-019, VCL-020, VCL-021",
 "order / order-receipt": "issues: VCL-021, VCL-024, VCL-049",
 "orders": "issues: VCL-024, VCL-076",
 "payouts": "issues: VCL-025, VCL-041, VCL-042, VCL-052",
 "payout-new (amount)": "issues: VCL-005, VCL-006, VCL-022, VCL-043, VCL-053, VCL-054, VCL-055",
 "payout-channel / payout-details / payout-review": "ok عموماً — VCL-054",
 "payout-detail / payout-statement": "issues: VCL-025",
 "certificates / verify": "issues: VCL-041, VCL-063, VCL-064, VCL-065",
 "notifications": "issues: VCL-014, VCL-015, VCL-041",
 "more": "issues: VCL-003, VCL-045, VCL-046",
 "profile": "issues: VCL-003, VCL-010, VCL-034, VCL-035 (VCL-033 على سطح المكتب فقط)",
 "help": "issues: VCL-009, VCL-036, VCL-037, VCL-038",
 "legal / legal-terms": "issues: VCL-011, VCL-072, VCL-080",
 "legal-delete-account": "ok",
 "partners": "issues: VCL-066, VCL-067",
 "not-found (root / account)": "issues: VCL-048",
 "offline": "issues: VCL-007",
 "all routes (loading)": "issues: VCL-044"
}
```
- **iphoneVsChromium**: ```json
[
 "help: iOS Safari يرسم مثلث الإفصاح الأصلي «◀» بجانب كل سؤال؛ Chromium لا (VCL-009).",
 "checkout-transfer: حقل الملف «Choose File / no file selected» على iOS مقابل «No file chosen» على Chromium — كلاهما إنجليزي (VCL-013)؛ حقل التاريخ «29. Oct 2026» بخط mono على iOS مقابل «10/29/2026» على Chromium (VCL-020).",
 "الرأس: «Fiper Pro Traders» يظهر بجانب الشعار على iPhone (430 px) ويختفي على 390 (VCL-077).",
 "الارتفاع: iPhone بشريط Safari يعطي 739 px مرئية، فالرئيسية تُظهر البطل والزر وصف العدّادات فقط فوق الطيّة؛ Chromium 390×844 يُظهر رأس المخطط أيضاً. الخطوط (IBM Plex Sans Arabic / Mono) متطابقة الوزن والقياس على الجهازين، ولا قصّ عند أسفل الشاشة على iPhone (padding-bottom للشريط يعمل).",
 "checkout على iPhone التُقط بالإنجليزية في مسار «ar» (VCL-074) فلا مقارنة عربية متاحة لهذه الشاشة.",
 "الفجوة فوق بطاقة الحقوق (VCL-012) وترتيب التبويبات المعكوس (VCL-004) والشيفرون المعكوس (VCL-003) متطابقة على iPhone وSamsung وChromium — عيوب كود لا متصفح."
]
```
- **strongest**: ```json
[
 "بطاقة الحقوق: رقم بطل mono كبير مع رمز العملة كعنصر منفصل، ودلتا «ربح +15,000.00 +15.00 %» بلون الربح وكلمة — تطبيق صحيح لقاعدة كلمة + إشارة + لون.",
 "صفحة الإيقاف: «−615.00 USD» بالأبيض و«تجاوز الحد» بلون السحب لا بالأحمر البراندي — التزام نادر وصحيح.",
 "عدّادات القواعد الثلاثة بصف واحد مدمج تحت البطل: المعلومة الأهم لمتداول البروب في أول شاشة.",
 "مخطط الحقوق بنطاق الخسارة المظلل وخطوط الهدف/الحدّ مع تسمية مباشرة «11.0k» — قرار داتا-فيز ناضج.",
 "شريط التبويب السفلي: أربعة عناصر فقط، أيقونة + كلمة، الحالة النشطة بالأحمر وخط علوي، آمن المنطقة على iPhone.",
 "تتبع السحب في صفحة التفاصيل: خمس خطوات بكلمات (طُلب، فُحص، موافقة 1/2، صُرف) بلون واحد للمكتمل — طمأنة واضحة.",
 "شاشة الانتظار بعد التحويل: جملة بشرية «نراجع إثباتك عادة خلال يوم عمل واحد» + مرجع قابل للنسخ + خيار إثبات بديل مرة واحدة.",
 "الإيصال والشهادة كوثائق بيضاء قابلة للطباعة داخل التطبيق الداكن — فصل صحيح بين «منتج» و«مستند».",
 "النموذج العام للدخول: ثلاثة مسارات (كلمة مرور، رمز بريد، إنشاء) واضحة، OTP بست خانات مع التركيز الأول، وحالة «تم الإرسال» بالعنوان ومدة الصلاحية.",
 "النص الكبير (22 px) والحركة المخفضة لا يكسران شيئاً؛ لا تجاوز أفقي في أي لقطة من 80+ لقطة."
]
```
- **weakest**: ```json
[
 "جملة قبول الشروط الفارغة في الإنتاج «أقبل و و (، سارية من ).» على الشاشة التي يُدفع فيها المال (VCL-001).",
 "رقم حساب وهمي «FPT-0000-0000 (عيّنة — يحدده المالك)» معروض للعميل مع زر نسخ (VCL-002).",
 "شيفرونات الصفوف تتجه يميناً في كل الواجهة العربية (VCL-003) وتبويبات الحساب تبدأ من اليسار (VCL-004) — عيبان RTL أساسيان على أكثر الشاشات زيارة.",
 "حقل مبلغ السحب: الأرقام تطبع فوق «USD» ولا تحقق لقيم 0 / سالبة / فوق الحدّ (VCL-005، VCL-006).",
 "دون اتصال: صفحة بيضاء خام بلا شعار ولا رسالة (VCL-007).",
 "صفحة الملف بطول 10,000 px بسبب عشرات القبولات المكررة ونماذج أمان مكشوفة دفعة واحدة (VCL-010، VCL-035).",
 "عناصر أصلية إنجليزية داخل العربية: «Choose File»، التاريخ، وسطر «Backed by Fiper Global…» (VCL-013، VCL-020، VCL-008).",
 "الإشعارات بلا تمييز مقروء/غير مقروء ورابط «فتح» يتيم (VCL-014، VCL-015).",
 "الفجوة الفارغة الكبيرة فوق بطاقة الحقوق على الرئيسية في كل الأجهزة (VCL-012).",
 "الحالات الفارغة توضّح لكن لا تقود إلى فعل، ومؤشرات الخطوات على الهاتف أرقام بلا كلمات (VCL-041، VCL-022)."
]
```
- **judgement**: ```json
[
 "الهيكل الأساسي صحيح وناضج: شاشة واحدة لكل مهمة، رقم بطل ثم ثلاث قواعد ثم المخطط، شريط سفلي بأربعة عناصر، وأمثلة ممتازة على دلالات المال (كلمة + إشارة + لون) تُطبَّق باتساق حقيقي بين الرئيسية والإيقاف والسحب.",
 "لكن الطبقة التي تفصل «منتج جيد» عن «تطبيق بروب من الطبقة الأولى» ما زالت غير مكتملة: عيبان P0 في مسار الدفع (شروط فارغة ورقم حساب عيّنة) يعنيان أن الإنتاج يُظهر بقايا بناء للعميل، وهذا وحده يمنع الإطلاق.",
 "الـRTL صحيح في النصوص والأرقام (الرمز بعد الرقم، أرقام لاتينية، عزل ثنائي الاتجاه يعمل في 95 % من الحالات) لكنه ينكسر في الأيقونات والترتيب: شيفرون معكوس في كل قائمة، تبويبات تبدأ من اليسار، أسهم نصية بالاتجاه الخطأ — أخطاء صغيرة الإصلاح، كبيرة الأثر على الإحساس بالعناية.",
 "الحِرفة التفصيلية متفاوتة: الصفحات التي بُنيت حول مكوّنات F1 (البطل، العدّاد، KeyValue، المتتبع) متماسكة، بينما الصفحات المركّبة يدوياً (الملف، التحويل، المساعدة، الإشعارات) تُظهر تكرارات نصية، عناصر أصلية غير مُسمَّاة، وفراغات غير محسوبة.",
 "الهرم البصري قوي في الرئيسية والحساب (اختبار التحديق ينجح) لكنه يضعف حيث يتزاحم الأحمر: شارة الممول الحمراء بجانب الزر الأحمر، وزر الكوبون بحجم زر رئيسي، وزر «ابدأ تحدياً» الأحمر على حساب موقوف.",
 "حالات النظام (تحميل، فارغ، خطأ، دون اتصال) هي الفجوة الأكبر مقارنةً بقرارات TWO Markets: هناك Skeleton مع مهلة وإعادة محاولة لكل شاشة، وحالة خطأ بمصدرها؛ هنا لا loading.tsx، حالات فارغة بلا فعل، وصفحة بيضاء دون اتصال.",
 "النص الإنجليزي متناسق مع العربي في البنية، لكن يحمل التناقض نفسه في موضع رمز العملة داخل الشاشة الواحدة، وينبغي حسم قرار «الرمز بعد الرقم دائماً» للغتين لأن البطل والعدّادات يفعلانه أصلاً.",
 "ليبدو التطبيق من الطبقة الأولى: أصلح P0 اليوم، ثم ثلاث حزم صغيرة — (1) RTL للأيقونات والتبويبات، (2) حقل المبلغ وتحققه + الحالات الفارغة بأفعال + Skeleton للمسارات، (3) تنظيف التكرارات النصية والعناصر الأصلية — بعدها تصبح الفوارق مع المرجع فوارق لمسات لا فوارق بنية."
]
```

### findings-visual-code.json

- **generatedAt**: 2026-10-30
- **scope**: apps/client/src · apps/admin/src · packages/ui/src · brand/components/components.css (تحليل مصدر فقط؛ لقطات الأجهزة الحقيقية 29 أكتوبر 13:29 تسبق التزام F2 فلم تُستعمل إلا لتأكيد VIS-001 وVIS-002)
- **counts**: ```json
{
 "total": 83,
 "bySeverity": {
  "P0": 1,
  "P1": 8,
  "P2": 26,
  "P3": 48
 },
 "byCategory": {
  "RTL": 6,
  "تقني": 16,
  "بصري": 40,
  "وصول": 11,
  "هوية": 10
 }
}
```
- **iconLibrary**: ```json
{
 "library": "Lucide عبر @fpt/ui/icons (مجموعة منتقاة، 70 ملف استيراد)",
 "packageVersion": "lucide-react ^1.63.0 (الهوية تثبّت Lucide v0.468.0 — انحراف إصدار)",
 "stray": [
  "apps/client/src/components/print-button.tsx:2 — استيراد مباشر من lucide-react (Printer)",
  "packages/ui/src/components/spinner.tsx — SVG يدوي بسمك 2.5 (العائلة 1.75/2)",
  "apps/client/src/components/charts.tsx:216–218 و equity-chart.tsx:169 — مربعات/خطوط legend مرسومة يدوياً (مقبولة، ليست أيقونات)",
  "نصوص كأيقونات: «→» trades-panel.tsx:86 / account-overview.tsx:337 / breach/page.tsx:140 · «*» ProductsClient.tsx:595 · «×» orders/[id]/page.tsx:307"
 ],
 "mirroring": "ChevronRight/ArrowLeft/LogOut تنعكس بـ .fpt-icon--dir (list-row، back-link، more) — ListRow وClientHeader يضيفان rtl:-scale-x-100 أيضاً (VIS-016)؛ LogOut في الإدارة لا ينعكس (VIS-079)؛ الشعار/الرسوم/الأرقام لا تنعكس (ui-tile direction:ltr، svg direction=ltr) ✓"
}
```
- **physicalDirectionUtilities**: ```json
{
 "apps_and_ui": "0 نتيجة لـ pl-/pr-/ml-/mr-/left-/right-/text-left/text-right/rounded-l/r/border-l/r في apps/client، apps/admin، packages/ui (grep)",
 "byDesign": [
  "packages/ui/src/components.css:459 `.ui-feed__details pre { direction:ltr; text-align:left }` (كتلة شيفرة)",
  "brand/components/components.css:96 `.fpt-input-wrap .fpt-affix{right:14px}` و :97 `padding-right:56px` (حقل رقمي LTR)",
  "brand/components/components.css:108 `.fpt-check input[type=checkbox]:checked::after{border-left…border-bottom…}` (علامة الصح لا تنعكس)"
 ],
 "rtlVariantsFound": [
  "list-row.tsx:43 rtl:-scale-x-100",
  "client-header.tsx:95 rtl:-scale-x-100",
  "switch.tsx:27 rtl:-translate-x-[3px]/[21px]",
  "dialog.tsx:29 و sheet.tsx:33 rtl:sm:translate-x-1/2",
  "components.css:157 [dir=rtl] .ui-slide-end",
  "components.css:376 :where([dir=rtl]) .ui-hero__value { justify-content:flex-end }"
 ]
}
```
- **routeStateMatrix**: ```json
{
 "legend": "loading = loading.tsx أو Skeleton للمسار · error = error.tsx (كلاهما على مستوى [locale] فقط) · empty = حالة فراغ للقوائم · longText = التفاف/قص للنصوص الطويلة · pagination = حد/ترقيم · urlState = الفلاتر/التبويبات في العنوان",
 "client": [
  {
   "route": "/accounts (الرئيسية)",
   "loading": "no",
   "error": "yes",
   "empty": "yes",
   "longText": "yes",
   "pagination": "n-a",
   "urlState": "n-a"
  },
  {
   "route": "/accounts/[id]",
   "loading": "no",
   "error": "yes",
   "empty": "yes",
   "longText": "yes",
   "pagination": "yes (?page للصفقات)",
   "urlState": "no (تقرأ ?tab ولا تكتبه)"
  },
  {
   "route": "/accounts/[id]/trades",
   "loading": "no",
   "error": "yes",
   "empty": "yes",
   "longText": "yes",
   "pagination": "yes",
   "urlState": "yes"
  },
  {
   "route": "/accounts/[id]/breach",
   "loading": "no",
   "error": "yes",
   "empty": "yes",
   "longText": "yes",
   "pagination": "n-a",
   "urlState": "n-a"
  },
  {
   "route": "/shop",
   "loading": "no",
   "error": "yes",
   "empty": "no (قائمة منتجات فارغة بلا رسالة)",
   "longText": "n-a",
   "pagination": "n-a",
   "urlState": "n-a"
  },
  {
   "route": "/shop/[productCode]",
   "loading": "no",
   "error": "yes (notFound)",
   "empty": "n-a",
   "longText": "n-a",
   "pagination": "n-a",
   "urlState": "n-a"
  },
  {
   "route": "/checkout/[orderId]",
   "loading": "no",
   "error": "yes",
   "empty": "n-a",
   "longText": "yes",
   "pagination": "n-a",
   "urlState": "n-a"
  },
  {
   "route": "/checkout/[orderId]/return",
   "loading": "no",
   "error": "yes",
   "empty": "n-a",
   "longText": "yes",
   "pagination": "n-a",
   "urlState": "yes (أعلام العودة)"
  },
  {
   "route": "/checkout/[orderId]/transfer",
   "loading": "no",
   "error": "yes",
   "empty": "yes (لا إثبات)",
   "longText": "yes",
   "pagination": "n-a",
   "urlState": "n-a"
  },
  {
   "route": "/payouts",
   "loading": "no",
   "error": "yes",
   "empty": "yes (سجل) / فقرة (لا ممول)",
   "longText": "yes",
   "pagination": "no",
   "urlState": "n-a"
  },
  {
   "route": "/payouts/new",
   "loading": "no",
   "error": "yes",
   "empty": "n-a",
   "longText": "yes",
   "pagination": "n-a",
   "urlState": "yes (?account)"
  },
  {
   "route": "/payouts/[id]",
   "loading": "no",
   "error": "yes (notFound)",
   "empty": "n-a",
   "longText": "yes",
   "pagination": "n-a",
   "urlState": "yes (?requested)"
  },
  {
   "route": "/payouts/[id]/statement",
   "loading": "no",
   "error": "yes",
   "empty": "n-a",
   "longText": "yes",
   "pagination": "n-a",
   "urlState": "n-a"
  },
  {
   "route": "/orders",
   "loading": "no",
   "error": "yes",
   "empty": "yes",
   "longText": "yes",
   "pagination": "no",
   "urlState": "n-a"
  },
  {
   "route": "/orders/[id]",
   "loading": "no",
   "error": "yes",
   "empty": "n-a",
   "longText": "yes",
   "pagination": "n-a",
   "urlState": "n-a"
  },
  {
   "route": "/orders/[id]/receipt",
   "loading": "no",
   "error": "yes",
   "empty": "n-a",
   "longText": "yes",
   "pagination": "n-a",
   "urlState": "n-a"
  },
  {
   "route": "/more",
   "loading": "no",
   "error": "yes",
   "empty": "n-a",
   "longText": "no (البريد nowrap)",
   "pagination": "n-a",
   "urlState": "n-a"
  },
  {
   "route": "/notifications",
   "loading": "no",
   "error": "yes",
   "empty": "yes",
   "longText": "yes",
   "pagination": "no (limit 100)",
   "urlState": "n-a"
  },
  {
   "route": "/profile",
   "loading": "no",
   "error": "yes",
   "empty": "yes (فقرة)",
   "longText": "no (البريد nowrap)",
   "pagination": "n-a",
   "urlState": "yes (?saved)"
  },
  {
   "route": "/certificates",
   "loading": "no",
   "error": "yes",
   "empty": "yes",
   "longText": "yes",
   "pagination": "n-a",
   "urlState": "n-a"
  },
  {
   "route": "/partners",
   "loading": "no",
   "error": "yes",
   "empty": "yes",
   "longText": "yes (الرابط) / no (البريد في الصف)",
   "pagination": "no",
   "urlState": "n-a"
  },
  {
   "route": "(auth) /signin /signup /signup/password /verify /2fa /reset /reset/new",
   "loading": "no",
   "error": "yes",
   "empty": "n-a",
   "longText": "n-a",
   "pagination": "n-a",
   "urlState": "yes (?next ?mode)"
  },
  {
   "route": "(public) /",
   "loading": "no",
   "error": "yes",
   "empty": "no",
   "longText": "n-a",
   "pagination": "n-a",
   "urlState": "n-a"
  },
  {
   "route": "(public) /help",
   "loading": "no",
   "error": "yes",
   "empty": "yes (لا نتائج)",
   "longText": "yes",
   "pagination": "n-a",
   "urlState": "no (البحث/التصنيف في الحالة)"
  },
  {
   "route": "(public) /legal/[key]",
   "loading": "no",
   "error": "yes",
   "empty": "yes (نسخة غير موجودة)",
   "longText": "yes",
   "pagination": "n-a",
   "urlState": "n-a"
  },
  {
   "route": "(public) /c/[code] · /verify/[code]",
   "loading": "no",
   "error": "yes",
   "empty": "yes",
   "longText": "yes",
   "pagination": "n-a",
   "urlState": "n-a"
  }
 ],
 "admin": [
  {
   "route": "/ (نظرة عامة)",
   "loading": "no",
   "error": "yes",
   "empty": "yes",
   "longText": "yes",
   "pagination": "n-a (feed 20)",
   "urlState": "n-a"
  },
  {
   "route": "/accounts",
   "loading": "no",
   "error": "yes",
   "empty": "yes",
   "longText": "yes",
   "pagination": "no (limit 200 + «المزيد» محلياً)",
   "urlState": "yes"
  },
  {
   "route": "/accounts/[id]",
   "loading": "no",
   "error": "yes",
   "empty": "yes",
   "longText": "yes",
   "pagination": "no (أحداث 100)",
   "urlState": "no (تبويبات)"
  },
  {
   "route": "/payouts",
   "loading": "no",
   "error": "yes",
   "empty": "yes",
   "longText": "yes",
   "pagination": "no (200)",
   "urlState": "yes"
  },
  {
   "route": "/payouts/[id]",
   "loading": "no",
   "error": "yes",
   "empty": "yes",
   "longText": "yes",
   "pagination": "n-a",
   "urlState": "n-a"
  },
  {
   "route": "/risk",
   "loading": "no",
   "error": "yes",
   "empty": "yes",
   "longText": "yes",
   "pagination": "no (slice 100)",
   "urlState": "yes"
  },
  {
   "route
```
- **brandChecks**: ```json
{
 "rawHex": "لا هكس في JSX/CSS التطبيقين سوى themeColor '#0A0A0A' الموثّق (layouts, manifest) وألوان QR في profile/forms.tsx:220 (VIS-062)",
 "fonts": "كل الخطوط عبر --fpt-font-*; font-body/font-sans فقط في JSX (3+3)؛ لا font-mono/font-display خام",
 "letterSpacing": "لا tracking-* في JSX (tracking-normal في الشعار فقط)؛ letter-spacing:0 !important على [dir=rtl]/:lang(ar) (components.css:84)",
 "brandRed": "الأدوار الثلاثة محترمة في @fpt/ui (البلاطة، fpt-btn--primary، النشط: تبويب/شريط/صندوق الاختيار/حلقة التركيز)؛ الانحرافات: hover أحمر في جداول الحزمة (VIS-024)، أساسي لكل صف (VIS-007)، نقطة الجرس bg-brand (client-header.tsx:111 — نقطة الشريط السفلي من الحزمة نفسها)",
 "finance": "الخسائر coral (--fpt-finance-loss) لا أحمر ✓؛ الخلل: لون+إشارة بلا كلمة ولا رمز عملة في صفوف الصفقات (VIS-009)، لون فقط في جداول الإدارة (VIS-022)",
 "emoji_iso": "لا إيموجي ولا ISO في العرض (toISOString للقيم الآلية فقط) ✓",
 "productName": "«Fiper Pro Traders» لاتيني داخل العربية ✓؛ «فايبر» للأم مسموح",
 "amounts": "Money/.fpt-unit/isolate:true مستعملة بانتظام ✓؛ codePosition='none' بلا رمز قريب في قوائم العميل (VIS-009)"
}
```
- **craft**: الحزمة @fpt/ui تمرّ اختبار الرموز بوضوح: كل لون وخط ونصف قطر يعود إلى --fpt-* ولا هكس خام في الشاشات، والارتفاع حدودي بلا ظلال إلا على الطبقات العائمة — استراتيجية عمق واحدة متسقة. اختبار الـ squint على الرئيسية ينجح: رقم مهيمن واحد، زر أحمر واحد، ثلاث عدّادات، رسم؛ لكن على شاشة الحساب العربية ينهار كل ذلك لأن Radix Tabs تقلب الشجرة إلى LTR (VIS-001) — خلل بنيوي واحد يستحق أن يُصلَح قبل أي تلميع. اختبار التوقيع يجد عناصر حقيقية للهوية (البلاطة 22 %، العدّاد «المتبقي» بشريط 8px، الدلالات المالية كلمة+إشارة+لون، الوثائق على أرضية الورق) إلا أن الدلالات المالية تُنتهك حيث يهم أكثر: صفوف الأرباح والخسائر تلوّن وتوقّع بلا كلمة ولا عملة. أرضيات الحجم (12/15/mono 15) محروسة بالبوّابة لكنها تُلتفّ عليها بثلاث طرق: text-xs/text-label على فقرات كاملة، نصوص داخل div/SVG لا يفحصها البوّاب، ومحتوى مشروط أو داخل تبويبات وحوارات لا تُفتح — فالتنبيهات ووصف الحوارات وتذييل التسجيل كلها 12–14px. غياب حالة التحميل كلياً يجعل التطبيق يبدو معلّقاً عند كل تبويب، وغياب الضغط على الصفوف يزيد ذلك. الاتساق بين العميل والإدارة جيد في الشارات والجداول والصفوف (StatusBadge/DataTableRows/ListRow مشتركة فعلاً)، ويتآكل عند الحواف: 13 بطلاً مكتوباً يدوياً، خمس راديوهات مرسومة، سبع خرائط نغمة محلية، ونسخة ثانية من الشرائح والرسم البياني. الإيقاع الفراغي يتبع 4pt في الصفحات لكن الحزمة نفسها تُدخل 6/10/14 فتنتشر إلى components.css. الخلاصة: الأساس صحيح والهوية متجذرة، والعمل المتبقي هو إغلاق الفجوات البنيوية (الاتجاه، التحميل، الأرضيات في المكوّنات المشتركة) ثم استخراج المكوّنات المكررة.

## 4. نتائج Lighthouse (هاتف، محاكاة 4G، الإنتاج)

| الشاشة | الأداء | الوصول | الممارسات | SEO | LCP | CLS | TBT | FCP | الحجم الكلي | JS | الخطوط | الصور | الطلبات | عنصر LCP |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| client-landing | 73 | 100 | 100 | 100 | 4364 ms | 0.024 | 285 ms | 3014 ms | 826 KB | 213 KB | 457 KB | 45 KB | 40 | <bdi class="fpt-unit"> |
| client-signin | 97 | 100 | 100 | 100 | 2118 ms | 0.024 | 179 ms | 1218 ms | 826 KB | 213 KB | 457 KB | 45 KB | 40 | <bdi class="fpt-unit"> |
| client-home | 97 | 100 | 100 | 100 | 2264 ms | 0.024 | 119 ms | 1364 ms | 825 KB | 213 KB | 457 KB | 45 KB | 39 | <bdi class="fpt-unit"> |
| client-account | 76 | 100 | 100 | 100 | 2414 ms | 0.001 | 921 ms | 1364 ms | 936 KB | 215 KB | 567 KB | 45 KB | 38 | <bdi class="fpt-unit"> |
| client-shop | 85 | 100 | 100 | 100 | 2864 ms | 0.001 | 362 ms | 2114 ms | 820 KB | 210 KB | 460 KB | 45 KB | 35 | <span class="fpt-challenge__size"> |
| client-payouts | 72 | 100 | 100 | 100 | 2566 ms | 0.003 | 1251 ms | 1366 ms | 711 KB | 209 KB | 351 KB | 45 KB | 34 | <span class="text-sm text-muted"> |
| client-more | 80 | 100 | 100 | 100 | 2117 ms | 0.002 | 811 ms | 1367 ms | 679 KB | 207 KB | 312 KB | 47 KB | 40 | <bdi class="fpt-unit ui-fit"> |
| client-home-en | 77 | 100 | 100 | 100 | 2393 ms | 0.024 | 880 ms | 1343 ms | 850 KB | 213 KB | 457 KB | 45 KB | 82 | <bdi class="fpt-unit"> |
| admin-overview | 80 | 100 | 100 | 100 | 2189 ms | 0.001 | 774 ms | 1214 ms | 537 KB | 205 KB | 205 KB | 45 KB | 25 | <p class="mt-0.5 text-body-2 text-muted"> |
| admin-accounts | 99 | 100 | 100 | 100 | 1811 ms | 0.001 | 86 ms | 1211 ms | 536 KB | 205 KB | 205 KB | 45 KB | 25 | <p class="mt-0.5 text-body-2 text-muted"> |

التقارير الكاملة: `artifacts/ux-audit-F4/lighthouse/<name>.html`. INP لا يقيسه Lighthouse في التحميل الصامت (يُقاس ميدانياً).

## 5. خطة الإصلاح المقترحة

### F4a — P0 + P1 (57 بنداً)

| البند | الخطورة | الفئة | التطبيق/الشاشة | الجهد | الملفات المتأثرة |
|---|---|---|---|---|---|
| VAD-001 — حجم الحساب يظهر مضروباً بـ 100: «5,000,000 USD» لمنتج 50,000 USD | P0 | تقني | الإدارة / orders | S | `packages/ui/src/lib/format.ts`، `packages/ui/src/components/money.tsx`، `apps/admin/src/app/[locale]/(app)/orders/page.tsx`، `apps/admin/src/app/[locale]/(app)/payments/page.tsx` |
| VIS-001 — تبويبات Radix تطبع dir="ltr" داخل الصفحات العربية فتنقلب شاشة الحساب كاملة إلى اتجاه LTR | P0 | RTL | مشترك / /accounts/[id] (client) · /settings · /accounts/[id] · /products · /mail/[id] (admin) | S | `packages/ui/src/components/tabs.tsx`، `packages/ui/src/components/select.tsx`، `apps/client/src/app/[locale]/layout.tsx`، `apps/admin/src/app/[locale]/layout.tsx` |
| TXT-002 — اسم كيان قانوني غير مقرَّر يُطبع في تذييل التطبيق والشهادات والكشوف والإيصالات | P0 | هوية | مشترك / footer / certificate / statement / receipt / legal pages / verify | M | `config/legal.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `apps/client/src/components/footer.tsx`، `apps/client/src/components/certificate-card.tsx` |
| VCL-001 — جملة الموافقة على الشروط فارغة في الإنتاج: «أقبل و و (، سارية من ).» | P0 | نص | العميل / checkout | M | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/checkout-form.tsx`، `apps/client/src/app/[locale]/(app)/checkout/[orderId]/page.tsx`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| VCL-002 — قيمة عيّنة «FPT-0000-0000 (عيّنة — يحدده المالك)» معروضة للعميل في الإنتاج مع زر نسخ | P0 | نص | العميل / checkout-transfer | S | `packages/db/src/settings.ts`، `apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/page.tsx` |
| TXT-001 — نصوص قانونية مؤلَّفة داخلياً تُعرض للعميل ويُلزَم بقبولها عند الدفع | P0 | هوية | العميل / legal / checkout | L | `packages/db/scripts/seed-data/legal.ts`، `config/legal.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| VIS-007 — زر أحمر أساسي في كل صف من الجدول — عشرات الإجراءات الأساسية على شاشة واحدة | P1 | هوية | الإدارة / /kyc · /deletions | S | `apps/admin/src/app/[locale]/(app)/kyc/KycActions.tsx`، `apps/admin/src/app/[locale]/(app)/deletions/DeletionActions.tsx` |
| DEV-001 — خطأ ترطيب React #418 على كروم ويندوز الحقيقي في سجل التدقيق | P1 | تقني | الإدارة / admin-audit | S | `apps/admin/src/app/[locale]/(app)/audit/page.tsx`، `packages/ui/src/components/event-feed.tsx` |
| A11Y-001 — زر «المزيد» في شريط التبويب الإداري لا يفتح لوحة الأقسام أبداً (لوحة المفاتيح والمؤشر معاً) | P1 | وصول | الإدارة / admin-overview / كل شاشات الإدارة على الهاتف (< 1024 px) | S | `apps/admin/src/components/shell/AdminShell.tsx`، `packages/ui/src/components/bottom-nav.tsx` |
| VAD-003 — نصوص محور الرسم ضخمة (~34 px) وخط المنحنى سميك — الـ SVG يتمدد مع viewBox | P1 | بصري | الإدارة / breach | M | `apps/admin/src/components/EquityCurve.tsx`، `packages/ui/src/components/equity-chart.tsx` |
| VAD-005 — بطاقة الأدلة فارغة إلا من سطر مطوي «الأدلة ⌄» وتمتد على كامل ارتفاع العمود | P1 | بصري | الإدارة / fraud-detail | M | `apps/admin/src/app/[locale]/(app)/fraud/[id]/page.tsx` |
| VAD-006 — سطر «Regulatory text: pending decision · النص التنظيمي: بانتظار قرار» ظاهر في بريد العميل | P1 | هوية | الإدارة / mail-detail | S | `packages/adapters/src/mail/templates/layout.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| VAD-004 — ثلاث دلالات مختلفة لـ «القرب من الحد» في ثلاث شاشات — 21 حساباً «تحتاج إجراءً» بينما لوحة المخاطر تقول «قريب من الحد 0» | P1 | نص | الإدارة / overview | M | `apps/admin/src/app/[locale]/(app)/page.tsx`، `apps/admin/src/app/[locale]/(app)/accounts/page.tsx`، `apps/admin/src/app/[locale]/(app)/risk/page.tsx`، `packages/i18n/src/messages/ar.json` |
| VAD-002 — التبويبات ومحتواها تُرسم LTR داخل الصفحات العربية (جدول المنتجات معكوس، بطاقات الإعدادات، تبويب «نظرة» في الحساب) | P1 | RTL | الإدارة / products | S | `packages/ui/src/components/tabs.tsx`، `apps/admin/src/app/[locale]/layout.tsx` |
| TXT-009 — أربعة مصطلحات لمفهوم واحد (payout): الدفعة/الدفعات، السحب/السحوبات، الصرف، سحب الأرباح — على شاشات متجاورة | P1 | نص | مشترك / الدفعات (تنقّل + صفحة + نموذج + بريد + شهادة) | M | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `packages/db/scripts/seed-data/legal.ts` |
| TXT-006 — الحد الأقصى للدفعة: 10,000 USD في الشروط وكتاب القواعد مقابل $5,000 في معجم العلامة | P1 | هوية | مشترك / الشروط + كتاب القواعد ↔ معجم العلامة | S | `packages/db/scripts/seed-data/legal.ts`، `packages/rulebook/src/rules.ts`، `brand/glossary.md` |
| TXT-014 — خطأ مطابقة: «الخسارة الأقصى» (المؤنث يأخذ «القصوى»)، وتذبذب التذكير/التأنيث في «حدّ الخسارة اليومي/اليومية» | P1 | نص | مشترك / تقرير المخالفة / أنواع القواعد | S | `packages/i18n/src/messages/ar.json` |
| TXT-011 — حالة التجاوز بأربع كلمات (مخروق/مخالفة/مخالِف/تجاوز الحد) والتقرير بثلاثة أسماء | P1 | نص | مشترك / حالة الحساب + تقرير المخالفة | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| TXT-012 — أساس الخسارة اليومية يُذكر «رصيد بداية اليوم» في مكان و«حقوق الحساب عند 00:00» في مكان آخر — والثاني هو الصحيح | P1 | نص | مشترك / قاعدة الخسارة اليومية (لوحة الحساب، القواعد، البريد، الإدارة) | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| TXT-013 — لا قواعد جمع في القاموس: التمييز العددي العربي خاطئ لمعظم القيم (1، 2، 3–10، 11+) والإنجليزية تلجأ إلى «(s)» | P1 | نص | مشترك / كل المواضع التي تُدرج عدداً قبل اسم معدود | M | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `packages/i18n/src/index.ts` |
| VIS-003 — لا توجد حالة تحميل لأي مسار: كل تنقّل بين التبويبات يترك الشاشة السابقة جامدة حتى يصل الـ RSC | P1 | تقني | مشترك / كل مسارات [locale] (لا يوجد loading.tsx) | M | `apps/client/src/app/[locale]/(app)/layout.tsx`، `apps/admin/src/app/[locale]/(app)/layout.tsx`، `packages/ui/src/components/skeleton.tsx` |
| TXT-010 — «السحب» يعني drawdown في مكان وwithdrawal في مكان آخر على الشاشة نفسها | P1 | نص | مشترك / لوحة الحساب / بريد الاجتياز / منحنى الرصيد | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `brand/glossary.md` |
| VIS-006 — شاشات العميل الجذرية بلا <h1>: عنوان الشاشة في الرأس مجرد <span> والأقسام تبدأ بـ h2 | P1 | وصول | العميل / /accounts · /accounts/[id] · /payouts · /more · /orders · /profile · /notifications · /certificates · /partners · /payouts/[id] · /orders/[id] · /shop/[productCode] | S | `packages/ui/src/components/app-shell.tsx`، `apps/client/src/components/client-header.tsx`، `packages/ui/src/components/empty-state.tsx` |
| VIS-009 — أرباح/خسائر الصفقات بلون وإشارة فقط — بلا كلمة وبلا رمز العملة | P1 | هوية | العميل / /accounts/[id] (الصفقات المفتوحة، الصفقات، حسب الرمز) · /accounts/[id]/breach | S | `apps/client/src/components/account-overview.tsx`، `apps/client/src/components/trades-panel.tsx`، `apps/client/src/app/[locale]/(app)/accounts/[id]/breach/page.tsx`، `apps/admin/src/components/Finance.tsx` |
| TEC-010 — الإرسال الضمني بمفتاح Enter يُرسل طلب الصرف من خطوة سابقة ويتخطّى المراجعة، أو يفشل صامتاً | P1 | تقني | العميل / /payouts/new (خطوة المبلغ وخطوة بيانات USDT) | S | `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx`، `apps/client/src/app/[locale]/(app)/payouts/actions.ts` |
| VIS-002 — لاصقة «USD» تغطي الأرقام المكتوبة في حقل المبلغ بالعربية | P1 | RTL | العميل / /payouts/new (خطوة المبلغ) · كل Input mono مع end في العربية | S | `packages/ui/src/components/input.tsx`، `packages/ui/src/components.css`، `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx` |
| VIS-011 — جمل كاملة بحجم 12px في تذييل شاشات التسجيل والتحقق | P1 | بصري | العميل / /signup · /signup/password · /2fa | S | `apps/client/src/app/[locale]/(auth)/signup/page.tsx`، `apps/client/src/app/[locale]/(auth)/signup/password/page.tsx`، `apps/client/src/app/[locale]/(auth)/2fa/page.tsx` |
| TEC-001 — عامل الخدمة يخزّن HTML الصفحات المصادَق عليها ويعرضها دون اتصال بعد تسجيل الخروج | P1 | تقني | العميل / /sw.js (كل صفحات الحساب) | M | `apps/client/public/sw.js`، `apps/client/src/app/api/signout/route.ts`، `apps/client/src/app/[locale]/(app)/more/page.tsx` |
| VCL-004 — ترتيب تبويبات الحساب معكوس في العربية: «نظرة» (الأول والنشط) في أقصى اليسار | P1 | RTL | العميل / account / account-rules / account-credentials / account-trades | S | `packages/ui/src/components/tabs.tsx`، `apps/client/src/app/[locale]/(app)/accounts/[id]/page.tsx` |
| VCL-079 — القيمة «12 من 10» تُرسم «من 10 12» في تبويب القواعد على 390 | P1 | RTL | العميل / account-rules (funded) | S | `apps/client/src/app/[locale]/(app)/accounts/[id]/page.tsx`، `packages/ui/src/components/key-value.tsx` |
| VCL-013 — زر رفع الملف أصلي بالإنجليزية «Choose File / no file selected» داخل واجهة عربية | P1 | نص | العميل / checkout-transfer | S | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/transfer-form.tsx` |
| VCL-009 — iOS Safari يرسم مثلث الإفصاح الأصلي «◀» بجانب كل سؤال إضافةً إلى الشيفرون | P1 | بصري | العميل / help | S | `apps/client/src/app/[locale]/(public)/help/help-search.tsx`، `apps/client/src/app/globals.css` |
| VCL-012 — فجوة فارغة نحو 150 px فوق بطاقة الحقوق على الرئيسية في كل الأجهزة | P1 | بصري | العميل / home | S | `apps/client/src/components/account-overview.tsx`، `apps/client/src/app/[locale]/(app)/accounts/page.tsx` |
| A11Y-002 — بعد إغلاق ورقة تفاصيل القاعدة بـ Escape يضيع التركيز إلى body ولا يعود إلى الزر الذي فتحها | P1 | وصول | العميل / home / account (overview) | S | `apps/client/src/components/account-overview.tsx`، `packages/ui/src/components/sheet.tsx` |
| VCL-008 — سطر إنجليزي كامل داخل الصفحة العربية العامة | P1 | نص | العميل / landing | S | `packages/i18n/src/messages/ar.json`، `apps/client/src/app/[locale]/(public)/page.tsx`، `brand/regulatory-matrix.md` |
| VCL-011 — تنبيه «مسودة — بانتظار اعتماد المؤسس؛ ليست نصاً قانونياً نهائياً» معروض للعميل فوق الشروط التي يقبلها عند الدفع | P1 | نص | العميل / legal-terms | S | `apps/client/src/app/[locale]/(public)/legal/[key]/page.tsx`، `packages/db/src/legal.ts` |
| VCL-003 — شيفرون الصفوف يشير إلى اليمين في الواجهة العربية (انعكاس مزدوج) | P1 | RTL | العميل / more / landing / home / profile | S | `packages/ui/src/components/list-row.tsx` |
| VCL-014 — لا تمييز بصري بين الإشعار المقروء وغير المقروء رغم عدّاد «7» | P1 | بصري | العميل / notifications | S | `apps/client/src/components/notifications-list.tsx` |
| VCL-007 — دون اتصال تظهر صفحة بيضاء فارغة بلا علامة ولا رسالة | P1 | تقني | العميل / offline-home / offline-navigation | M | `apps/client/public/sw.js`، `apps/client/src/components/sw-register.tsx` |
| VCL-005 — الأرقام المكتوبة تتراكب فوق لاحقة «USD» داخل حقل المبلغ | P1 | بصري | العميل / payout-new (amount) | S | `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx`، `packages/ui/src/components/input.tsx`، `packages/ui/src/components.css` |
| VCL-006 — لا رسالة خطأ لقيمة صفر أو سالبة أو أكبر من الحدّ: زر «متابعة» يُعطَّل صامتاً والمعاينة تعرض «تتسلم 7,999,999.20 USD» | P1 | وصول | العميل / payout-new (amount) | M | `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx`، `packages/i18n/src/messages/ar.json` |
| VCL-010 — قائمة المستندات تكرّر الوثائق الأربع عشرات المرات (قبول لكل عملية) بدل آخر نسخة مقبولة | P1 | بصري | العميل / profile | S | `apps/client/src/app/[locale]/(app)/profile/page.tsx` |
| TXT-004 — عبارات مخاطر ووصف برنامج مؤلَّفة داخلياً على الصفحة العامة دون اعتماد | P1 | هوية | العميل / الصفحة العامة (landing) + التذييل | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `config/legal.ts`، `apps/client/src/app/[locale]/(public)/page.tsx` |
| TXT-005 — تناقض في واقعة منتج: الخصوصية تقول إن كلمة مرور المنصة تُعرض مرة في بريد التسليم، والبريد يقول «لا كلمة مرور عبر البريد أبداً» | P1 | هوية | العميل / سياسة الخصوصية (مسودة) ↔ بريد بيانات الدخول | S | `packages/db/scripts/seed-data/legal.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| MAIL-006 — عبارة «النص التنظيمي: بانتظار قرار» المؤقتة ظاهرة للعميل في كل بريد | P1 | هوية | البريد / all | S | `packages/adapters/src/mail/templates/layout.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| MAIL-007 — رسائل الإنتاج الصادرة من تطبيقات Next تخرج بلا شعار (نص «Fiper Pro Traders» فقط) | P1 | هوية | البريد / all | M | `packages/adapters/src/mail/templates/layout.ts`، `apps/client/next.config.ts`، `apps/admin/next.config.ts` |
| MAIL-008 — عنوان «تجاوز الحد — أُوقف التداول» يُستخدم أيضاً لانتهاء المدة وعدم النشاط | P1 | نص | البريد / breach | M | `packages/adapters/src/mail/templates/index.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| MAIL-003 — سبب رفض التحقق يصل العميل كرمز/جملة خام من المزوّد | P1 | نص | البريد / kyc_result | M | `apps/client/src/lib/webhooks.ts`، `packages/adapters/src/mail/templates/index.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| MAIL-005 — نص التنبيه الإنجليزي يُعرض داخل فقرة RTL فتنتقل النقطة إلى بداية السطر | P1 | RTL | البريد / ops_alert | S | `packages/adapters/src/mail/templates/layout.ts`، `services/engine/src/alerts.ts` |
| MAIL-001 — نسبة الأرباح تظهر «20/80» بدل «80/20» في النسخة العربية | P1 | RTL | البريد / payout_paid | S | `packages/adapters/src/mail/templates/layout.ts`، `packages/i18n/src/messages/ar.json` |
| MAIL-004 — طريقة الدفع تُطبع بالرمز الخام «crypto» في العنوان والبطاقة | P1 | نص | البريد / payout_paid | S | `packages/adapters/src/mail/templates/index.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `services/engine/src/payouts.ts` |
| MAIL-002 — مرجع الحساب ينكسر على سطرين داخل الفقرة العربية على الهاتف | P1 | RTL | البريد / payout_requested | S | `packages/adapters/src/mail/templates/layout.ts`، `packages/adapters/src/mail/templates/index.ts` |
| TXT-007 — البريد يعد بخطوة «اتفاقية الحساب الممول» غير موجودة في المنتج | P1 | نص | البريد / بريد اجتياز التقييم | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| TXT-008 — متغيّر القناة في بريد «تمت معالجة دفعتك» يستقبل رمز قاعدة البيانات الخام (fiper_card / crypto / wire / fiper_p2p) | P1 | نص | البريد / بريد تنفيذ الدفعة | S | `packages/adapters/src/mail/templates/index.ts`، `services/engine/src/payouts.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| TXT-003 — عبارة داخلية «النص التنظيمي: بانتظار قرار» ظاهرة للعميل في تذييل كل رسالة بريد | P1 | هوية | البريد / كل رسائل البريد (HTML + النسخة النصية) | S | `packages/adapters/src/mail/templates/layout.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| VIS-005 — وصف الحوارات والأوراق 14px — ويُلغي حتى override العميل | P1 | بصري | مكوّنات @fpt/ui / كل ActionDialog في الإدارة (اعتماد الدفعة، الرفض، الدعوة…) · ورقة تفاصيل القاعدة في العميل | S | `packages/ui/src/components/dialog.tsx`، `packages/ui/src/components/sheet.tsx` |
| VIS-004 — نص التنبيه جملة كاملة بحجم 14px تحت أرضية الجسم 15px | P1 | بصري | مكوّنات @fpt/ui / كل تنبيه Alert (العميل: تحذير السحب 80/90 %، الحساب المخالف، أخطاء الدفع، الملف؛ الإدارة: نتائج الإجراءات) | S | `packages/ui/src/components/alert.tsx` |

الملفات الأكثر تأثراً في F4a: `packages/i18n/src/messages/ar.json` (23)، `packages/i18n/src/messages/en.json` (18)، `packages/adapters/src/mail/templates/layout.ts` (7)، `packages/adapters/src/mail/templates/index.ts` (5)، `packages/db/scripts/seed-data/legal.ts` (4)، `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx` (4)، `packages/ui/src/components/tabs.tsx` (3)، `config/legal.ts` (3)، `apps/client/src/components/account-overview.tsx` (3)، `apps/admin/src/app/[locale]/layout.tsx` (2)، `brand/glossary.md` (2)، `packages/ui/src/components/input.tsx` (2)، `packages/ui/src/components.css` (2)، `apps/client/public/sw.js` (2)، `apps/client/src/app/[locale]/(app)/accounts/[id]/page.tsx` (2).

### F4b — P2 + P3 (421 بنداً)

| البند | الخطورة | الفئة | التطبيق/الشاشة | الجهد | الملفات المتأثرة |
|---|---|---|---|---|---|
| VIS-018 — القوائم تقطع صامتةً عند 200–500 سجل بلا ترقيم خادمي ولا إشارة «يوجد المزيد» | P2 | تقني | الإدارة / /accounts · /orders · /payouts · /audit · /coupons · /deletions · /mail · /kyc · /breaches · /payments | M | `packages/ui/src/components/data-table.tsx`، `apps/admin/src/app/[locale]/(app)/accounts/page.tsx`، `apps/admin/src/app/[locale]/(app)/orders/page.tsx` |
| VIS-022 — المعنى باللون وحده: نسبة «أقرب حد» كهرمانية/حمراء بلا كلمة ولا أيقونة، والمكوّن الصحيح موجود وغير مستعمل | P2 | وصول | الإدارة / /accounts · /risk · /accounts/[id] · /payments · /health | S | `apps/admin/src/app/[locale]/(app)/accounts/page.tsx`، `apps/admin/src/app/[locale]/(app)/risk/page.tsx`، `apps/admin/src/components/Finance.tsx` |
| TEC-003 — صفحة 404 الجذرية للوحة الموظفين تُسبق بناؤها فتشحن 18 سكربت بلا nonce وتحجبها CSP كلها | P2 | تقني | الإدارة / /ar/<مسار غير موجود> (404 الجذر) | S | `apps/admin/src/app/not-found.tsx`، `apps/client/src/app/not-found.tsx` |
| VIS-035 — جمل وتسميات بحجم 14px في مسارات مشروطة/تبويبات لا تقيسها البوّابة | P2 | بصري | الإدارة / /breaches/[id] · /accounts/[id] (تبويب البيانات) · /payouts/[id] · /products (المحاكي) | S | `apps/admin/src/app/[locale]/(app)/breaches/[id]/page.tsx`، `apps/admin/src/app/[locale]/(app)/accounts/[id]/page.tsx`، `apps/admin/src/app/[locale]/(app)/payouts/[id]/page.tsx`، `apps/admin/src/app/[locale]/(app)/products/ProductsClient.tsx` |
| TEC-038 — تقرير البوابات المحلي الحالي يُظهر فيض أفقي في صفحة التكاملات عند 360 px (الجسم 376 > 360) — فشل بوابة ux-floors | P2 | تقني | الإدارة / /integrations @ 360 px | S | `artifacts/ux-gates/gates-phone-360.json`، `apps/admin/src/app/[locale]/(app)/integrations/page.tsx` |
| VIS-023 — رموز تقنية خام في طبقة العرض خارج «التفاصيل التقنية» | P2 | تقني | الإدارة / /orders/[id] · /products (المحاكي) · /settings · /fraud/links/[userId] · CommandProgress | S | `apps/admin/src/app/[locale]/(app)/orders/[id]/page.tsx`، `apps/admin/src/app/[locale]/(app)/products/ProductsClient.tsx`، `apps/admin/src/app/[locale]/(app)/settings/page.tsx`، `apps/admin/src/components/CommandProgress.tsx` |
| VIS-031 — قفز مستوى العناوين في الإدارة: h1 ثم h3 (CardTitle) بلا h2 | P2 | وصول | الإدارة / /settings · /orders/[id] · /fraud/[id] · /fraud/links/[userId] · /integrations · /products (الإصدارات) · /orders/grant | S | `packages/ui/src/components/card.tsx`، `apps/admin/src/app/[locale]/(app)/settings/page.tsx`، `apps/admin/src/app/[locale]/(app)/orders/[id]/page.tsx` |
| VIS-029 — زرّان أساسيان متجاوران في تبويب واحد: «حفظ قنوات الصرف» و«إضافة مسار» | P2 | هوية | الإدارة / /settings (تبويب الدفعات) | S | `apps/admin/src/app/[locale]/(app)/settings/ManualRoutesClient.tsx`، `apps/admin/src/app/[locale]/(app)/settings/LaunchSettingsForms.tsx` |
| VIS-014 — رأس شاشات دخول الإدارة بارتفاع ثابت 56px مع pt-safe على العنصر نفسه — يُسحق على الهواتف ذات النوتش | P2 | بصري | الإدارة / /signin · /2fa · /enrol (الإدارة) | S | `apps/admin/src/app/[locale]/(auth)/layout.tsx` |
| VIS-021 — الرابط المضمّن يمدّد منطقة لمسه إلى 44px بهوامش سالبة لكن عنوان الصف يقصّها (overflow:hidden) | P2 | وصول | الإدارة / /staff (صفوف الهاتف) · أي ui-inline-link داخل عنوان ListRow | S | `packages/ui/src/components.css`، `apps/admin/src/app/[locale]/(app)/staff/page.tsx` |
| VAD-019 — مجموعتا فلاتر بلا عنوان مرئي وكل منهما تبدأ بـ «الكل» — المشغّل لا يعرف ما الذي تصفّيه الصف الثاني | P2 | بصري | الإدارة / accounts | S | `apps/admin/src/components/Filters.tsx` |
| VAD-020 — التاريخ الكامل «29 أكتوبر 2026، 11:49 بتوقيت دبي (GMT+4)» في كل صف يلتف على 3 أسطر ويخفض الكثافة إلى 6 صفوف/شاشة | P2 | بصري | الإدارة / accounts | S | `apps/admin/src/components/AdminTime.tsx`، `packages/ui/src/components/date-time.tsx` |
| VAD-030 — كل رأس يعرض «↕» ولا يُظهر الفرز الفعّال (الأحدث أولاً) رغم ذكره في العنوان الفرعي | P2 | بصري | الإدارة / accounts | S | `apps/admin/src/app/[locale]/(app)/accounts/page.tsx`، `apps/admin/src/app/[locale]/(app)/orders/page.tsx`، `apps/admin/src/app/[locale]/(app)/mail/page.tsx` |
| A11Y-013 — خطر الاقتراب من الحدّ يُنقل باللون وحده في جدول الحسابات | P2 | وصول | الإدارة / admin-accounts | S | `apps/admin/src/app/[locale]/(app)/accounts/page.tsx` |
| A11Y-004 — صفحة دخول الموظفين وصفحة المنع بلا h1 | P2 | وصول | الإدارة / admin-signin, admin-forbidden (+ صفحة «لا صلاحية» داخل الأقسام) | S | `apps/admin/src/app/[locale]/(auth)/signin/page.tsx`، `apps/admin/src/app/[locale]/(app)/forbidden/page.tsx`، `packages/ui/src/components/empty-state.tsx` |
| VAD-016 — إجراءات بلا ترجمة تظهر خاماً: «النظام — order payment started» | P2 | نص | الإدارة / audit | S | `apps/admin/src/app/[locale]/(app)/audit/page.tsx`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| VAD-017 — سجل التدقيق كتغذية أحداث بارتفاع ~125 px للعنصر — 4 سجلات في الشاشة، أوقات نسبية فقط، لا نطاق تاريخ | P2 | بصري | الإدارة / audit | M | `apps/admin/src/app/[locale]/(app)/audit/page.tsx`، `apps/admin/src/components/Feed.tsx` |
| VAD-012 — صيغ الجمع العربية ثابتة: «4 مخالفة معروضة»، «5 تقييماً منتهياً»، «1 سحباً»، «1 جهازاً»، «2 طلباً» | P2 | نص | الإدارة / finance | M | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/index.ts` |
| VAD-031 — نص «تعتمد FPT سياسة عدم الاسترداد … تُعرض صراحةً حتى لا يُغفل الرقم» حقيقة منتج تحتاج صياغة من المعجم | P2 | نص | الإدارة / finance | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| VAD-007 — علامة ≥ تنعكس في العربية فتُقرأ «الخطورة ≤ 40» — عكس المعنى | P2 | RTL | الإدارة / fraud | S | `apps/admin/src/app/[locale]/(app)/fraud/page.tsx`، `packages/i18n/src/messages/ar.json` |
| VAD-008 — الموضوع الإنجليزي يتفكك: «5.60 USD :New affiliate commission» | P2 | RTL | الإدارة / mail | S | `apps/admin/src/app/[locale]/(app)/mail/page.tsx` |
| VAD-024 — اسم القالب «manual proof received» و«manual payment confirmed» بالإنجليزية داخل الواجهة العربية | P2 | نص | الإدارة / mail | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `apps/admin/src/app/[locale]/(app)/mail/page.tsx` |
| VAD-014 — محتوى العمودين الثاني والثالث مُحاذى إلى الأسفل فيظهر فراغ 150–300 px تحت العناوين | P2 | بصري | الإدارة / me | S | `apps/admin/src/app/[locale]/(app)/staff/me/page.tsx`، `apps/admin/src/app/[locale]/(app)/payments/[id]/page.tsx`، `apps/admin/src/app/[locale]/(app)/staff/[id]/page.tsx` |
| VAD-015 — الصلاحيات تُعرض كجدار رموز خام «overview · read, products · write, …» في منطقة غير موسومة كخام | P2 | تقني | الإدارة / me | S | `apps/admin/src/app/[locale]/(app)/staff/me/page.tsx`، `packages/i18n/src/messages/ar.json` |
| VAD-032 — طلب بحالة «فاشل» بلا سبب فشل ولا إجراء (إلغاء/تواصل/إعادة محاولة) | P2 | بصري | الإدارة / order | M | `apps/admin/src/app/[locale]/(app)/orders/[id]/page.tsx` |
| VAD-026 — تكرار «تحويل يدوي · تحويل يدوي» في عمود الطريقة | P2 | نص | الإدارة / orders | S | `apps/admin/src/app/[locale]/(app)/orders/page.tsx` |
| VAD-009 — سهم الانتقال يشير إلى اليمين «>» في الواجهة العربية بدل اليسار | P2 | RTL | الإدارة / overview | S | `packages/ui/src/components/list-row.tsx`، `packages/ui/src/components.css`، `apps/admin/tailwind.config.*` |
| VAD-022 — ثماني وجهات مخفية تحت طيّ الشريط الجانبي على 900 px بلا أي مؤشر تمرير | P2 | بصري | الإدارة / overview | M | `packages/ui/src/components/side-nav.tsx`، `apps/admin/src/components/shell/AdminShell.tsx`، `apps/admin/src/app/[locale]/(app)/layout.tsx` |
| VAD-027 — أسماء الخدمات خام بالإنجليزية (engine, alerts, sim, provisioner…) في النظرة العامة والمحاكي بينما صفحة «صحة النظام» تعرّبها | P2 | نص | الإدارة / overview | S | `apps/admin/src/app/[locale]/(app)/page.tsx`، `apps/admin/src/app/[locale]/(app)/simulator/SimulatorClient.tsx`، `packages/i18n/src/messages/ar.json` |
| VAD-028 — طابور «يحتاج إجراءً» ليس أول ما يراه المشغّل، وعلى الهاتف يُدفن تحت تغذية أحداث بطول ~2000 px | P2 | بصري | الإدارة / overview | M | `apps/admin/src/app/[locale]/(app)/page.tsx` |
| VAD-013 — القوس «(» يُفصل عن «GMT+4)» عند الالتفاف: «… بتوقيت دبي (» ثم «GMT+4)» | P2 | RTL | الإدارة / payment | S | `packages/ui/src/components/date-time.tsx`، `apps/admin/src/components/AdminTime.tsx` |
| VAD-011 — المتتبع يضع «موافقة أولى» كخطوة جارية رغم أن الحالة «معتمد (1 من 2)» والاعتماد الأول مسجّل باسم Amir | P2 | تقني | الإدارة / payout | S | `apps/admin/src/components/PayoutSteps.tsx` |
| VAD-010 — متتبع خطوات كامل داخل كل صف يرفع ارتفاع الصف إلى ~95 px ويقطع الجدول على 1280 px | P2 | بصري | الإدارة / payouts | M | `apps/admin/src/app/[locale]/(app)/payouts/page.tsx`، `apps/admin/src/components/PayoutSteps.tsx` |
| VAD-021 — تحويم الصف يلوّنه بغسلة الأحمر البرندي (--fpt-brand-red-wash) — إشارة خطر على كل صف | P2 | هوية | الإدارة / payouts | S | `brand/components/components.css`، `packages/ui/src/components.css` |
| VAD-025 — زر «فحص المجموعة» + شارة «لم تُفحص» في كل صف — 10 أزرار في جدول 5 صفوف وارتفاع صف 120 px | P2 | بصري | الإدارة / products | S | `apps/admin/src/app/[locale]/(app)/products/page.tsx`، `apps/admin/src/app/[locale]/(app)/products/ProductsClient.tsx` |
| VAD-029 — قيم المخاطر بلا تدرّج لوني: «7 %» من الحد و«0 %» بنفس الأبيض العريض؛ الخطورة 60 و40 بنفس الأصفر | P2 | بصري | الإدارة / risk | S | `apps/admin/src/app/[locale]/(app)/risk/page.tsx`، `apps/admin/src/components/status.ts` |
| VAD-023 — 8 تبويبات تُقصّ على 390 px — «كل المفاتيح» و«الشركاء» و«المدفوعات» و«المنصة» غير قابلة للوصول | P2 | بصري | الإدارة / settings | S | `apps/admin/src/app/[locale]/(app)/settings/page.tsx`، `packages/ui/src/components/tabs.tsx`، `packages/ui/src/components.css` |
| VAD-033 — فقاعة التحقق الأصلية «Please fill out this field.» بالإنجليزية على صفحة عربية | P2 | وصول | الإدارة / signin | S | `apps/admin/src/app/[locale]/(auth)/signin` |
| VAD-018 — ثلاثة أزرار كاملة لكل صف (تغيير الدور / تعطيل / إعادة تعيين المصادقة) ترفع الصف إلى 140 px | P2 | بصري | الإدارة / staff | S | `apps/admin/src/app/[locale]/(app)/staff/page.tsx`، `apps/admin/src/app/[locale]/(app)/staff/StaffClient.tsx`، `apps/admin/src/app/[locale]/(app)/staff/[id]/page.tsx` |
| TXT-038 — لغة إدارة المشروع («هذه الجولة»، أسماء أشخاص وتواريخ قرارات، أرقام قرارات وملفات) داخل واجهة الإنتاج | P2 | نص | الإدارة / إعدادات / الشركاء / الدفعات | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| TXT-037 — العنوان يولّد «لتجاوز حدّ يدوي» و«حدّ انتهاء المدة» و«حدّ عدم النشاط» | P2 | نص | الإدارة / صفحة المخالفة | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| TXT-036 — سبعة قوالب بريد بلا تسمية مترجمة → تظهر معرّفاتها الإنجليزية الخام في الواجهة العربية | P2 | نص | الإدارة / صندوق البريد | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| A11Y-012 — عناوين المستندات الإدارية كلها «FPT Admin» وصفحة 404 بلا عنوان | P2 | وصول | الإدارة / كل شاشات الإدارة + صفحة 404 الإدارية | S | `apps/admin/src/app/[locale]/layout.tsx`، `apps/admin/src/app/not-found.tsx`، `apps/admin/src/app/[locale]/(app)/**/page.tsx` |
| VIS-019 — التبويب المختار لا ينعكس في العنوان: التحديث والرجوع والمشاركة تعيد التبويب الأول | P2 | تقني | مشترك / /accounts/[id] (العميل) · /settings · /accounts/[id] · /products · /mail/[id] (الإدارة) | S | `packages/ui/src/components/tabs.tsx`، `apps/client/src/app/[locale]/(app)/accounts/[id]/page.tsx`، `apps/admin/src/app/[locale]/(app)/settings/page.tsx` |
| VIS-027 — نص الرسم ومحاوره يتضخمان مع عرض الحاوية (SVG يتمدد): محاور 12px تصبح ≈ 24–30px على المكتب | P2 | بصري | مشترك / /accounts/[id] و/accounts على 640+ · /breaches/[id] (الإدارة 1440) | M | `packages/ui/src/components/equity-chart.tsx` |
| VIS-025 — شريحتان مختلفتان للفلتر المختار (أحمر مغسول في المساعدة، أبيض ممتلئ في الإدارة) ونسختان من كود الشرائح | P2 | بصري | مشترك / /help (العميل) مقابل قوائم الإدارة (Filters) | S | `apps/client/src/app/[locale]/(public)/help/help-search.tsx`، `apps/admin/src/components/Filters.tsx`، `packages/ui/src/components/filter-bar.tsx` |
| VIS-030 — 13 نسخة يدوية من بطاقة الرقم المهيمن بدل مكوّن واحد — رمز العملة ثابت «USD» وdata-caption مفقودة والإدارة بعلامات مختلفة | P2 | تقني | مشترك / /payouts · /payouts/new · /payouts/[id] · /orders/[id] · /shop/[productCode] · /partners · /checkout/[orderId]/transfer · /accounts/[id]/breach · /checkout (العميل) · /payouts/[id] · /payments/[id] · /breaches/[id] · /affiliates/[id] (الإدارة) | M | `packages/ui/src/components/account-hero.tsx`، `apps/client/src/app/[locale]/(app)/payouts/page.tsx`، `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx`، `apps/admin/src/app/[locale]/(app)/payouts/[id]/page.tsx` |
| VIS-013 — رمز QR معكوس الألوان (وحدات بيضاء على أسود) — كثير من تطبيقات المصادقة لا تقرأه | P2 | بصري | مشترك / /profile (تفعيل المصادقة) · /enrol (الإدارة) | S | `apps/client/src/app/[locale]/(app)/profile/forms.tsx`، `apps/admin/src/app/[locale]/(auth)/enrol/page.tsx` |
| A11Y-010 — تبويبات Radix في الواجهة العربية تعمل بافتراض LTR: الأسهم معكوسة اتجاهياً | P2 | وصول | مشترك / client/account (4 تبويبات); admin-settings, admin-products, admin-mail-detail, admin-account | S | `apps/client/src/app/[locale]/layout.tsx`، `apps/admin/src/app/[locale]/layout.tsx`، `packages/ui/src/components/tabs.tsx` |
| A11Y-005 — قفز مستويات العناوين h1 → h3 في 9 شاشات (axe heading-order) | P2 | وصول | مشترك / client/legal-delete-account; admin-kyc, admin-payments, admin-settings, admin-deletions, admin-integrations, admin-order, admin-fraud-detail, admin-fraud-links | S | `packages/ui/src/components/card.tsx`، `apps/admin/src/app/[locale]/(app)/integrations/page.tsx`، `apps/client/src/app/[locale]/(public)/legal/delete-account/page.tsx` |
| TXT-029 — «متابعة» و«تأكيد» حيث يلزم فعل محدَّد — خاصة زر دخول الموظفين وأزرار الحوارات الخطرة | P2 | نص | مشترك / أزرار مبهمة | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| TXT-017 — الـ reset اليومي بثلاث تسميات: التصفير / إعادة الضبط / إعادة التعيين — و«التصفير» مضلِّل | P2 | نص | مشترك / إعادة الضبط اليومية | S | `packages/i18n/src/messages/ar.json` |
| TXT-042 — Retry بأربع صيغ | P2 | نص | مشترك / إعادة المحاولة | S | `packages/i18n/src/messages/ar.json` |
| TXT-018 — إغلاق/إقفال يتبادلان للحدث نفسه (إقفال الحساب، إقفال الصفقات) | P2 | نص | مشترك / إقفال الحساب / الصفقات | S | `packages/i18n/src/messages/ar.json` |
| TXT-031 — «يتحدّث/تتحدّث» تُقرأ «يتكلّم/تتكلّم» | P2 | نص | مشترك / اللوحة / لوحة المخاطر / صحة النظام | S | `packages/i18n/src/messages/ar.json` |
| TXT-027 — سجلّان متناوبان: «نحن» الودّي والمبني للمجهول الرسمي — أحياناً في الرسالة الواحدة | P2 | نص | مشترك / النبرة: ضمير المتكلم الجمعي مقابل البناء للمجهول | M | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| TXT-035 — تسمية «Desktop (Windows)» غير مترجمة في العربية | P2 | نص | مشترك / بيانات الدخول — أزرار فتح cTrader | S | `packages/i18n/src/messages/ar.json` |
| TXT-023 — رقم دخول cTrader بثلاث تسميات (رقم الحساب / اسم الدخول / رقم الدخول) والإنجليزية بين Login وAccount number | P2 | نص | مشترك / بيانات الدخول إلى cTrader | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| TXT-043 — القاعدتان الأساسيتان بثلاث تسميات عربية وخمس إنجليزية | P2 | نص | مشترك / تسميات حدود الخسارة (AR/EN) | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| TXT-045 — حالة KYC الواحدة بأربع كلمات للانتظار وثلاث للقبول | P2 | نص | مشترك / حالات التحقق من الهوية | S | `packages/i18n/src/messages/ar.json` |
| TXT-022 — «مطلوب» تعني «required» في الحقول و«requested» في حالة الدفعة | P2 | نص | مشترك / حالات طلب الدفعة | S | `packages/i18n/src/messages/ar.json` |
| TXT-019 — provisioning بثلاث ترجمات: التزويد / التجهيز / التسليم | P2 | نص | مشترك / حالة الطلب / التزويد | S | `packages/i18n/src/messages/ar.json` |
| TXT-030 — أخطاء تقول «غير صالح/حدث خطأ» دون سبب أو ما يفعله المستخدم | P2 | نص | مشترك / رسائل الخطأ بلا سبب أو خطوة تالية | M | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| TXT-020 — كلمة الحالة الواحدة تختلف بين status.* وdashboard.status.*.title وadmin.events.accountStatus.* | P2 | نص | مشترك / شارات حالة الحساب | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| TXT-028 — الإجراء الواحد مرة بصيغة الأمر ومرة بالمصدر | P2 | نص | مشترك / صيغة أزرار الإجراء (أمر مقابل مصدر) | S | `packages/i18n/src/messages/ar.json` |
| TXT-041 — override بأربع ترجمات، و«مسوّق» تسرّب لمصطلح «شريك» | P2 | نص | مشترك / عمولة المستوى الأعلى | S | `packages/i18n/src/messages/ar.json` |
| TXT-033 — «مبيعاً/مبيعات» كاسم معدود للـ sale — «مبيع» اسم مفعول (الشيء المباع) لا «عملية بيع» | P2 | نص | مشترك / فئات العمولة | S | `packages/i18n/src/messages/ar.json` |
| TXT-026 — اسم Fiper مرة بالحروف اللاتينية ومرة «فايبر»، و«بطاقة Fiper» مقابل «Fiper Card» غير المترجمة | P2 | هوية | مشترك / قنوات الدفع (Fiper) | S | `packages/i18n/src/messages/ar.json` |
| VIS-026 — صوتان لعنوان الشاشة: .ui-title بخط الواجهة (Inter) و PageHeader/CardTitle بالصوت العرضي Barlow Condensed | P2 | هوية | مشترك / كل h1 (ScreenTitle/PageTitle) مقابل الهبوط/المساعدة/القانوني ودخول الإدارة | S | `packages/ui/src/components.css`، `packages/ui/src/components/page-header.tsx`، `apps/admin/src/app/[locale]/(auth)/signin/page.tsx` |
| VIS-034 — محفّز Select ومحتواه يحملان dir="ltr" في العربية: النص والقائمة محاذاة يساراً وسهم الاتجاه معكوس | P2 | RTL | مشترك / كل Select (27 من 29 استعمالاً) | S | `packages/ui/src/components/select.tsx` |
| TXT-016 — مصطلح equity: «حقوق الحساب» للرقم و«منحنى الرصيد» للرسم نفسه، والمعجم يقترح «الرصيد الحالي / رصيد الحساب» | P2 | نص | مشترك / لوحة الحساب / الرسم / الإدارة | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `brand/glossary.md` |
| TXT-021 — approve = «اعتماد» في الإدارة و«موافقة» في العميل — ويختلطان في الشاشة الواحدة | P2 | نص | مشترك / مسار الدفعة / التحويل اليدوي | S | `packages/i18n/src/messages/ar.json` |
| VIS-012 — وصف المرحلتين على صفحة الهبوط جملة بحجم 12px | P2 | بصري | العميل / / (الهبوط) «كيف يعمل» | S | `packages/ui/src/components.css`، `apps/client/src/app/[locale]/(public)/page.tsx` |
| VIS-008 — زرّان بالتسمية نفسها «فتح الحساب» على الرئيسية لحساب التقييم | P2 | بصري | العميل / /accounts (الرئيسية) لحساب تقييم حيّ | S | `apps/client/src/app/[locale]/(app)/accounts/page.tsx` |
| TEC-011 — استطلاع البث الحي كل ثانية يعامل 401 (انتهاء الجلسة) كـ«غير متصل» ويستمر إلى الأبد | P2 | تقني | العميل / /accounts/[id] (نظرة عامة حية) | S | `apps/client/src/components/account-overview.tsx`، `apps/client/src/app/api/accounts/[id]/live/route.ts`، `apps/client/src/lib/session.ts` |
| VIS-028 — حروف أيام الأسبوع 10px وقيم الخلايا متاحة بالتحويم فقط (<title>) | P2 | وصول | العميل / /accounts/[id]?tab=trades (تقويم الربح والخسارة) | M | `apps/client/src/components/charts.tsx` |
| VIS-033 — خمس نسخ من زر راديو مرسوم يدوياً (border-[1.5px] + peer-checked) بدل مكوّن واحد أو راديو الحزمة | P2 | تقني | العميل / /checkout/[orderId] · /payouts/new · /checkout/[orderId]/transfer | S | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/checkout-form.tsx`، `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx`، `apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/transfer-form.tsx` |
| TEC-012 — تاريخ التحويل الافتراضي والحد الأقصى يُحسبان بـ UTC داخل مكوّن عميل: عدم تطابق hydration وتاريخ خاطئ لمستخدمي الخليج بعد منتصف الليل | P2 | تقني | العميل / /checkout/[orderId]/transfer (رفع إثبات التحويل) | S | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/transfer-form.tsx`، `apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/page.tsx` |
| VIS-020 — البريد الإلكتروني داخل .fpt-unit (nowrap) يتجاوز عرض الهاتف ويُقص عند العناوين الطويلة | P2 | بصري | العميل / /more · /profile · /partners (الإحالات) · قسم البريد | S | `apps/client/src/app/[locale]/(app)/more/page.tsx`، `apps/client/src/app/[locale]/(app)/profile/page.tsx`، `apps/client/src/app/[locale]/(app)/profile/email-section.tsx`، `apps/client/src/app/[locale]/(app)/partners/page.tsx` |
| VIS-015 — الشريط السفلي الثابت يبقى فوق لوحة المفاتيح في نماذج الإدخال ويأكل 56px من المساحة الظاهرة | P2 | بصري | العميل / /payouts/new · /checkout/[orderId] · /checkout/[orderId]/transfer · /profile · /partners | S | `apps/client/src/components/shell.tsx`، `apps/client/src/app/[locale]/layout.tsx` |
| VIS-017 — إنهاء جلسة / إنهاء كل الجلسات / تسجيل الخروج من الجهاز الحالي بلا تأكيد | P2 | وصول | العميل / /profile (الأجهزة) | S | `apps/client/src/app/[locale]/(app)/profile/page.tsx` |
| VIS-010 — بطاقة الحجم تعلن أنها خيار (radio) لكنها تُرسل النموذج عند اللمس | P2 | بصري | العميل / /shop | S | `apps/client/src/app/[locale]/(app)/shop/challenge-picker.tsx`، `packages/ui/src/components/challenge-card.tsx` |
| TEC-040 — React 19 يفرّغ الحقول غير المتحكَّم بها بعد كل إجراء: عند خطأ التحقق يختفي البريد/كلمة المرور ويعيد المستخدم الكتابة | P2 | تقني | العميل / /signin, /signup, /reset, /verify (نماذج المصادقة) | S | `apps/client/src/app/[locale]/(auth)/auth-form.tsx`، `apps/client/src/components/submit-button.tsx` |
| TEC-009 — قفل المصادقة الثنائية يحوّل إلى `/signin?mfa=locked` لكن صفحة تسجيل الدخول تتجاهل المعامل: المستخدم يعود إلى النموذج بلا أي رسالة | P2 | تقني | العميل / /signin?mfa=locked | S | `apps/client/src/app/[locale]/(auth)/signin/page.tsx`، `apps/client/src/app/[locale]/(auth)/actions.ts`، `packages/i18n/src/messages/ar.json` |
| A11Y-009 — خانات رمز المصادقة الثنائية تُقرأ بالإنجليزية «Digit 1 of 6» في الصفحة العربية | P2 | وصول | العميل / 2fa (المصادقة الثنائية) | S | `apps/client/src/app/[locale]/(auth)/auth-form.tsx` |
| VCL-026 — شارة الحالة تلتف إلى سطرها الخاص بفراغ كبير ورابط «عرض الطلب والإيصال» لا يبدو قابلاً للنقر | P2 | بصري | العميل / account | S | `apps/client/src/app/[locale]/(app)/accounts/[id]/page.tsx` |
| A11Y-014 — زر إغلاق الورقة السفلية يقع 8 px خارج حافة الشاشة على الهاتف | P2 | وصول | العميل / account (overview) على الهاتف — ورقة تفاصيل القاعدة | S | `packages/ui/src/components/sheet.tsx` |
| VCL-029 — كلمة «المتبقي» مكررة على طرفي الصف والرقم البطل «0 %» عديم المعلومة | P2 | نص | العميل / account-rules (funded) | S | `packages/ui/src/components/rule-meter.tsx`، `apps/client/src/app/[locale]/(app)/accounts/[id]/page.tsx` |
| VCL-044 — لا loading.tsx في مسارات العميل: الانتقال بين الصفحات بلا أي ملاحظة بصرية | P2 | تقني | العميل / all (route transitions) | M | `apps/client/src/app/[locale]/(app)/loading.tsx`، `packages/ui/src/components/skeleton.tsx` |
| VCL-056 — العنوان يكرر كل معلومات السطر التالي ويمتد ثلاثة أسطر بخط 24 px | P2 | بصري | العميل / breach | S | `apps/client/src/app/[locale]/(app)/accounts/[id]/breach/page.tsx` |
| A11Y-007 — خطأ «يجب قبول المستندات» غير مرتبط بصندوق الاختيار ولا ينقل التركيز | P2 | وصول | العميل / checkout | S | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/checkout-form.tsx`، `packages/ui/src/components/checkbox.tsx` |
| VCL-017 — أسهم الخطوات «→» تشير يميناً داخل نص عربي يُقرأ يساراً | P2 | RTL | العميل / checkout-transfer | S | `packages/db/src/settings.ts`، `packages/i18n/src/messages/ar.json` |
| VCL-018 — جملة «JPEG أو PNG أو WebP أو PDF، حتى 5 MB» مكررة مرتين متجاورتين | P2 | نص | العميل / checkout-transfer | S | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/page.tsx` |
| VCL-019 — العنوان ينكسر على سطرين ويزاحمه النص المساعد في الصف نفسه على الهاتف | P2 | بصري | العميل / checkout-transfer | S | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/page.tsx` |
| VCL-073 — أدلة الحالات: الدفعة الأولى التُقطت بعرض 1280 لا 390، وبعد إعادة الالتقاط خمس لقطات عربية للدخول فارغة أو بلا CSS | P2 | تقني | العميل / evidence (ux-audit-F4) | S | `scripts/ux-audit-capture.mjs` |
| VCL-036 — مركز المساعدة يُفتح من «المزيد» لكنه يفقد شريط التبويب وزر الرجوع | P2 | بصري | العميل / help | M | `apps/client/src/app/[locale]/(public)/help/page.tsx`، `apps/client/src/app/[locale]/(public)/layout.tsx` |
| VCL-027 — ترتيب العدّادات معكوس بين الرئيسية وصفحة الحساب | P2 | بصري | العميل / home / account | S | `apps/client/src/components/account-overview.tsx`، `packages/ui/src/components/rule-meter.tsx` |
| VCL-016 — شارة ممتلئة بالأحمر تنافس الزر الأساسي الأحمر على الشاشة نفسها | P2 | هوية | العميل / home / account-credentials / account-funded | S | `packages/ui/src/components/status-badge.tsx`، `apps/client/src/components/account-overview.tsx` |
| VCL-039 — في الإنجليزية يسبق رمز العملة الرقم في سطر الرصيد بينما يليه في بطاقة الحقوق والعدّادات على الشاشة نفسها | P2 | بصري | العميل / home-en / account-en | S | `packages/ui/src/lib/format.ts`، `apps/client/src/components/account-overview.tsx` |
| A11Y-003 — 23 شاشة عميل بلا h1: عنوان الشاشة في الرأس هو span لا عنوان | P2 | وصول | العميل / home, account, account-trades, account-rules, account-credentials, trades, account-funded, shop-product, checkout, payouts, payout-new, payout-channel, payout-details, payout-review, payout-detail, notifications, profile, more, orders, order, partners, account-breached, home-mixed | S | `apps/client/src/components/client-header.tsx`، `packages/ui/src/components/app-shell.tsx` |
| VCL-015 — رابط «فتح» نص عريض يتيم أسفل كل إشعار بمحاذاة غير منتظمة | P2 | بصري | العميل / notifications | S | `apps/client/src/components/notifications-list.tsx` |
| VCL-024 — «تقييم FPT — 10,000 دولار · 10,000 USD» يكرر المبلغ بكلمة «دولار» ثم بالرمز | P2 | نص | العميل / orders / order-receipt | S | `packages/db/src/catalog.ts`، `packages/i18n/src/messages/ar.json` |
| VCL-043 — عند فتح لوحة المفاتيح يبقى شريط التبويب ثابتاً ويحجب معاينة «تتسلم 400.00 USD» | P2 | بصري | العميل / payout-amount (keyboard open) | M | `apps/client/src/components/bottom-nav.tsx`، `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx` |
| A11Y-011 — تبديل خطوات السحب صامت لقارئ الشاشة وزر «التالي» معطّل بلا تفسير | P2 | وصول | العميل / payout-new (المبلغ → القناة → البيانات → المراجعة) | M | `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx` |
| VCL-025 — النسبة تنكسر بين سطرين: «80» في نهاية سطر و«%» في بداية التالي | P2 | RTL | العميل / payouts / payout-detail | S | `apps/client/src/app/[locale]/(app)/payouts/page.tsx`، `apps/client/src/app/[locale]/(app)/payouts/[id]/page.tsx` |
| VCL-041 — الحالات الفارغة توضّح لكنها بلا فعل تالٍ | P2 | بصري | العميل / payouts-empty / certificates-empty / notifications-empty / orders | S | `packages/ui/src/components/empty-state.tsx`، `apps/client/src/app/[locale]/(app)/payouts/page.tsx`، `apps/client/src/app/[locale]/(app)/certificates/page.tsx` |
| VCL-034 — المنطقة الزمنية تُعرض مرتين: قائمة منسدلة في نموذج الهوية وصف «المنطقة الزمنية للعرض» في التفضيلات | P2 | نص | العميل / profile | S | `apps/client/src/app/[locale]/(app)/profile/page.tsx`، `apps/client/src/app/[locale]/(app)/profile/forms.tsx`، `apps/client/src/components/timezone-prompt.tsx` |
| A11Y-008 — بطاقات الحجم تعلن نفسها radio لكنها أزرار إرسال: لا أسهم، كلها في ترتيب Tab، و Space/Enter يرسل النموذج | P2 | وصول | العميل / shop | M | `packages/ui/src/components/challenge-card.tsx`، `apps/client/src/app/[locale]/(app)/shop/challenge-picker.tsx` |
| VCL-023 — في RTL تُقرأ الرسوم أولاً وحجم الحساب (القيمة الرئيسية) في الطرف الأيسر | P2 | بصري | العميل / shop | S | `packages/ui/src/components/challenge-card.tsx` |
| VCL-051 — الزر الأساسي «المتابعة إلى الدفع» قبل القواعد التي ينبغي قراءتها قبل الشراء | P2 | بصري | العميل / shop-product | M | `apps/client/src/app/[locale]/(app)/shop/[productId]/page.tsx` |
| VCL-031 — حلقة التركيز الحمراء على حقل فارغ تُقرأ كحالة خطأ | P2 | بصري | العميل / signin | S | `brand/tokens.css`، `packages/ui/src/components/input.tsx` |
| VCL-032 — رابط استعادة كلمة المرور نص رمادي بلا تسطير بينما الروابط المجاورة مسطّرة | P2 | بصري | العميل / signin | S | `apps/client/src/app/[locale]/(auth)/signin/page.tsx` |
| A11Y-006 — خطأ تسجيل الدخول لا يُربط بالحقول ولا يُنقل التركيز إليه | P2 | وصول | العميل / signin (كلمة المرور / الرمز) | S | `apps/client/src/app/[locale]/(auth)/auth-form.tsx`، `packages/ui/src/components/field.tsx` |
| VCL-030 — خطأ بيانات الدخول يظهر كتنبيه أعلى النموذج بلا ربط بالحقول ولا تركيز | P2 | وصول | العميل / signin-wrong | S | `apps/client/src/app/[locale]/(auth)/signin/signin-form.tsx` |
| TEC-029 — لا أيقونة `purpose: "maskable"`: Android يحشو البلاطة داخل قناع الشكل التكيّفي فتبدو صغيرة بحواف غير مقصودة | P2 | هوية | العميل / أيقونة التطبيق على Android (تثبيت PWA) | S | `apps/client/public/manifest.webmanifest`، `apps/client/public/brand/` |
| TXT-040 — سطر فرعي إنجليزي بالكامل في الشهادة والكشف العربيين بينما الإيصال مترجم | P2 | نص | العميل / الشهادة / كشف الدفعة | S | `packages/i18n/src/messages/ar.json` |
| TXT-025 — العملة تُكتب «دولار/دولاراً» في نصوص وبرمز USD في نصوص أخرى — والعلامة تشترط رمز العملة | P2 | هوية | العميل / المبالغ في النصوص العربية | S | `packages/i18n/src/messages/ar.json`، `packages/db/scripts/seed-data/legal.ts` |
| TXT-015 — «التحدي» و«التقييم» يتبادلان المواقع للشيء الواحد داخل المسار نفسه | P2 | نص | العميل / المتجر / اللوحة / المشتريات | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| TXT-078 — ادعاء «مرخّص» ومبالغ بلا رمز عملة في النص القانوني | P2 | هوية | العميل / الوثائق القانونية (مسودة) | S | `packages/db/scripts/seed-data/legal.ts` |
| TXT-024 — credentials: «البيانات» وحدها لا تدل على شيء، والبدائل أربعة | P2 | نص | العميل / تبويب البيانات / بريد التسليم / الإشعارات | S | `packages/i18n/src/messages/ar.json` |
| TXT-044 — سطرا حقوق مختلفان: البريد يحمل السطر الملاحَظ F1 حرفياً، والتطبيق يركّب سطراً جديداً باسم كيان غير مقرَّر | P2 | هوية | العميل / تذييل التطبيق ↔ تذييل البريد | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `apps/client/src/components/footer.tsx` |
| TXT-039 — دور داخلي («المالك») يُكشف للعميل بلا تعريف | P2 | نص | العميل / حذف الحساب | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| TEC-002 — لا توجد صفحة «دون اتصال» ذات هوية: البديل هو `Response.error()` أي صفحة خطأ المتصفح الخام | P2 | هوية | العميل / دون اتصال (أي مسار) | S | `apps/client/public/sw.js`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| TXT-032 — اختلاف معنى بين اللغتين (مبيعات ↔ إحالات) ومفتاحان متطابقان | P2 | نص | العميل / صفحة الشركاء | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| TEC-008 — انتهاء الجلسة (خمول 30 دقيقة) أثناء تعبئة نموذج يُفقد كل ما كُتب بلا أي تفسير، ومفتاح «انتهت جلستك» موجود وغير مستخدم | P2 | تقني | العميل / كل الإجراءات الخادمية في (app) — خاصة /payouts/new و/profile | M | `apps/client/src/lib/session.ts`، `apps/client/src/app/[locale]/(auth)/signin/page.tsx`، `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx` |
| TEC-014 — الخطوط تُكتشف بعد CSS ولا تُحمَّل مسبقاً: أربعة أوزان من IBM Plex Sans Arabic (~280 KB) مع `font-display: swap` → وميض خط وانزياح تخطيط | P2 | أداء | العميل / كل الصفحات (الخطوط) | S | `apps/client/src/app/[locale]/layout.tsx`، `apps/admin/src/app/[locale]/layout.tsx`، `brand/fonts/fonts.css` |
| TEC-013 — كل صفحة تشحن القاموس كاملاً (193 KB عربي) بما فيه نصوص لوحة الموظفين، فيتضخّم HTML وTBT على الهاتف | P2 | أداء | العميل / كل الصفحات (مثال /ar = 243 KB HTML) | M | `apps/client/src/i18n/request.ts`، `apps/client/src/app/[locale]/layout.tsx`، `packages/i18n/src/index.ts`، `packages/i18n/src/messages/ar.json` |
| TXT-046 — غياب الضمة يقلب المعنى («لماذا أوقف حسابي؟» = لماذا أوقفَ هو حسابي)، وعنوان استفهامي بلا علامة استفهام في اللغتين | P2 | RTL | العميل / لماذا أُوقف حسابي | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| MAIL-025 — سنة حقوق النشر «© 2026» مثبّتة يدوياً في القاموس | P2 | تقني | البريد / account_closed | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `packages/adapters/src/mail/templates/layout.ts` |
| MAIL-010 — تذبذب بين «حساب التقييم» في الموضوع و«حساب تحديك» في العنوان | P2 | نص | البريد / account_ready | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| MAIL-021 — موضوع البريد العربي يُعرض مقلوباً في واجهة الأدمن الإنجليزية | P2 | RTL | البريد / admin | S | `apps/admin/src/app/[locale]/(app)/mail/[id]/page.tsx`، `apps/admin/src/app/[locale]/(app)/mail/page.tsx` |
| MAIL-022 — الأدمن يعرض أسماء قوالب F3 السبعة خاماً («manual proof received») | P2 | نص | البريد / admin | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| MAIL-014 — الـ preheader يكرر الموضوع حرفياً في كل القوالب | P2 | تقني | البريد / all | S | `packages/adapters/src/mail/templates/index.ts`، `packages/adapters/src/mail/templates/layout.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| MAIL-015 — التذييل بحجم 11 px دون حدّ F1 (لا شيء مرئي أقل من 12 px) | P2 | وصول | البريد / all | S | `packages/adapters/src/mail/templates/layout.ts` |
| MAIL-016 — الملاحظة وسطر «لماذا وصلتك» أصغر من حدّ النص الأساسي 15 px | P2 | وصول | البريد / all | S | `packages/adapters/src/mail/templates/layout.ts` |
| MAIL-017 — العنوان الرئيسي عنصر <p> لا <h1>؛ لا بنية عناوين دلالية | P2 | وصول | البريد / all | S | `packages/adapters/src/mail/templates/layout.ts` |
| MAIL-018 — الزر على عرض الهاتف ليس بعرض كامل (154–182 px) ومحاذى لجهة واحدة | P2 | بصري | البريد / all | S | `packages/adapters/src/mail/templates/layout.ts` |
| MAIL-019 — روابط المحرّك بلا بادئة لغة فيُحوَّل المستخدم الإنجليزي إلى الواجهة العربية | P2 | تقني | البريد / all | S | `services/engine/src/comms.ts`، `services/engine/src/ending.ts`، `services/engine/src/payouts.ts` |
| MAIL-028 — اسم «Fiper Global» ينكسر على سطرين في سطر الحقوق العربي على الهاتف | P2 | RTL | البريد / all | S | `packages/adapters/src/mail/templates/layout.ts` |
| MAIL-030 — لا ترويسة List-Unsubscribe ولا تفضيلات للرسائل غير الحرجة | P2 | وصول | البريد / all | M | `packages/adapters/src/mail/live.ts`، `packages/adapters/src/mail/templates/layout.ts` |
| MAIL-031 — فراغ مزدوج ظاهر بين الرقم و«%» داخل الوحدات (5  %، 80  %) | P2 | بصري | البريد / breach | S | `packages/adapters/src/mail/templates/layout.ts` |
| MAIL-020 — تحذير انتهاء المدة بلا زر رغم أنه يطلب فعلاً من المتداول | P2 | نص | البريد / expiry_warning | S | `packages/adapters/src/mail/templates/index.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `services/engine/src/ending.ts` |
| MAIL-009 — تسمية «مرجع الحساب» تُستخدم لمرجع الطلب في قوالب التحويل اليدوي الأربعة | P2 | نص | البريد / manual_proof_received | S | `packages/adapters/src/mail/templates/index.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| MAIL-027 — ملاحظة الموظف الحرة تُلحق بسبب الرفض بلغة الموظف لا لغة العميل | P2 | نص | البريد / manual_proof_rejected | M | `apps/admin/src/lib/actions/payments.ts` |
| MAIL-033 — سطر «لماذا وصلتك» الموجّه للعملاء يظهر في تنبيه الموظفين | P2 | نص | البريد / ops_alert | S | `packages/adapters/src/mail/templates/layout.ts`، `packages/adapters/src/mail/templates/index.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| MAIL-011 — «حدود السحب» تعني هنا الخسارة (drawdown) بينما «السحب» في بقية الرسائل يعني صرف الأرباح | P2 | نص | البريد / passed | S | `packages/i18n/src/messages/ar.json` |
| MAIL-013 — «Congratulations:» / «تهانينا:» في موضوع الاجتياز كلمة مصنّفة كمحفّز بريد مزعج | P2 | نص | البريد / passed | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| MAIL-012 — ثلاثة مصطلحات لمفهوم الصرف الواحد: سحب / دفعة / صرف | P2 | نص | البريد / payout_paid | S | `packages/i18n/src/messages/ar.json` |
| MAIL-024 — «شهادة السحب الخاصة بك تحت الزر» صياغة غير طبيعية بالعربية | P2 | نص | البريد / payout_paid | S | `packages/i18n/src/messages/ar.json` |
| MAIL-023 — صياغة إنجليزية ركيكة: «The review is completed by …; the outcome reaches you by e-mail» | P2 | نص | البريد / payout_requested | S | `packages/i18n/src/messages/en.json` |
| MAIL-032 — تكرار «48 ساعة» في صف البطاقة وفي الملاحظة | P2 | نص | البريد / staff_invite | S | `packages/adapters/src/mail/templates/index.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| MAIL-029 — رمز التحقق يظهر في الموضوع وفي الـ preheader معاً | P2 | نص | البريد / verify_code | S | `packages/adapters/src/mail/templates/index.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| MAIL-026 — الخط العربي للهوية غير مضمّن؛ العرض الفعلي يسقط إلى خط النظام | P2 | هوية | البريد / welcome | M | `packages/adapters/src/mail/templates/layout.ts` |
| TXT-034 — الموضوع «حساب التقييم» والعنوان «حساب تحديك»، وتكرار جملة «لا نطلب كلمة المرور» مرتين في الرسالة | P2 | نص | البريد / بريد بيانات الدخول (account_ready) | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| TXT-084 — النص لا يذكر السبب ولا «المتطلبات» التي يحيل إليها | P2 | نص | البريد / بريد رفض التحقق من الهوية | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `packages/adapters/src/mail/templates/index.ts` |
| VIS-016 — الشيفرون يحمل آليتي انعكاس معاً (.fpt-icon--dir transform + rtl:-scale-x-100 scale) — خطر انعكاس مزدوج | P2 | RTL | مكوّنات @fpt/ui / كل ListRow بشيفرون (المزيد، الطلبات، قوائم الإدارة على الهاتف) | S | `packages/ui/src/components/list-row.tsx`، `apps/client/src/components/client-header.tsx` |
| VIS-032 — لا استجابة ضغط على الصفوف والعدّادات والبطاقات: التحويم فقط خلف @media(hover) | P2 | بصري | مكوّنات @fpt/ui / كل ListRow رابط/زر · RuleMeter التفاعلي · ChallengeCard · صفوف الراديو | S | `packages/ui/src/components.css` |
| VIS-024 — الأحمر يُستعمل كلون تحويم (hover) على صفوف الجداول — دور رابع خارج الأدوار الثلاثة | P2 | هوية | مكوّنات @fpt/ui / كل جدول إدارة على المكتب (hover) · .fpt-listrow · شرائح /help | S | `packages/ui/src/components.css`، `brand/components/components.css` |
| VIS-040 — رمز العملة في البلاطات يُحسب 0.55em فيهبط إلى ≈ 12.1px على الهاتف (تحت التسمية 13) | P3 | بصري | الإدارة / / (نظرة عامة) · /finance — بلاطات الأرقام على الهاتف | S | `packages/ui/src/components/money.tsx` |
| TEC-027 — القوائم تُقصّ خادمياً عند 200–400 صف بصمت؛ لا ترقيم خادمي ولا سطر «يُعرض آخر N» | P3 | أداء | الإدارة / /accounts (200), /payouts (200), /orders (300), /deletions (300), /payments (400) | M | `apps/admin/src/app/[locale]/(app)/payments/page.tsx`، `apps/admin/src/app/[locale]/(app)/orders/page.tsx`، `apps/admin/src/app/[locale]/(app)/accounts/page.tsx`، `packages/ui/src/components/data-table.tsx` |
| TEC-048 — استثناء مسار المعاينة من `API_CSP` صحيح ومقيّد بـ regex ناف — تأكيد لا عيب؛ يبقى التحقق أن المسار يحمل سياسة بريد خاصة | P3 | تقني | الإدارة / /api/mail/[id]/preview (إطار المعاينة) | S | `apps/admin/next.config.ts`، `apps/admin/src/app/api/mail/[id]/preview/route.ts` |
| VIS-045 — سبع خرائط نغمة محلية لـ StatusPill بدل خريطة StatusBadge الواحدة — الكلمة نفسها بألوان مختلفة بين الصفحات | P3 | بصري | الإدارة / /deletions · /coupons · /staff · /settings · /fraud · /products · ManualRoutes | S | `packages/ui/src/components/status-badge.tsx`، `apps/admin/src/app/[locale]/(app)/deletions/page.tsx`، `apps/admin/src/app/[locale]/(app)/coupons/page.tsx` |
| VIS-059 — معاينة البريد في إطار ثابت 720px — على الهاتف تمرير داخل تمرير وقيمة عشوائية | P3 | بصري | الإدارة / /mail/[id] | S | `apps/admin/src/app/[locale]/(app)/mail/[id]/page.tsx` |
| TEC-039 — خطأ كونسول متوقع من إطار البريد المعزول (`sandbox` بلا allow-scripts) ما زال يُسقط بوابات 1440/390/360 رغم أن حارس الأجهزة الحقيقية يتجاهله | P3 | تقني | الإدارة / /mail/[id] (معاينة البريد) | S | `e2e/ux-gates.spec.ts`، `scripts/real-device-coverage.mjs` |
| VIS-066 — نصوص placeholder لاتينية مكتوبة في الكود خارج الترجمة ولا تنتهي بـ «…» | P3 | بصري | الإدارة / /orders · /payments · /kyc · /mail · /settings (الأخبار) · /simulator | S | `apps/admin/src/app/[locale]/(app)/orders/page.tsx`، `apps/admin/src/app/[locale]/(app)/settings/SettingsClient.tsx` |
| TEC-037 — صورة الإثبات بلا `width`/`height` → انزياح تخطيط عند التحميل | P3 | أداء | الإدارة / /payments/[id] (معاينة الإثبات) | S | `apps/admin/src/app/[locale]/(app)/payments/[id]/page.tsx`، `apps/client/src/app/api/orders/[orderId]/proof/route.ts` |
| VIS-081 — جمل إرشادية كاملة بحجم 13px عبر صنف التسمية (تحايل على أرضية 15) | P3 | بصري | الإدارة / /settings · /products · /payments · ManualRoutes (ملاحظات تحت الجداول) | S | `apps/admin/src/app/[locale]/(app)/settings/page.tsx`، `apps/admin/src/app/[locale]/(app)/products/page.tsx` |
| VAD-067 — 404 ثنائية اللغة «الصفحة غير موجودة / Page not found» وزر «نظرة عامة · Overview» | P3 | وصول | الإدارة / 404 | S | `apps/admin/src/app/not-found.tsx`، `apps/admin/src/app/[locale]/(app)/layout.tsx` |
| VAD-064 — تسميات إجراء غامضة: «إعادة حساب التعيين»، «تعيين صلاحيات الوصول» | P3 | نص | الإدارة / account | S | `packages/i18n/src/messages/ar.json`، `apps/admin/src/app/[locale]/(app)/accounts/[id]/page.tsx` |
| VAD-065 — كلمة «تقييم» مكررة: شارة «تقييم» ثم نص «تقييم Khalid Al-Mansoori» | P3 | بصري | الإدارة / account | S | `apps/admin/src/app/[locale]/(app)/accounts/[id]/page.tsx` |
| VAD-036 — حلقة التركيز بالأحمر البرندي (--fpt-action-focus-ring: #F42821 PROPOSED) | P3 | هوية | الإدارة / accounts | S | `brand/tokens.css` |
| VAD-038 — عمودان يحملان القيمة نفسها «تقييم» في كل الصفوف تقريباً | P3 | بصري | الإدارة / accounts | S | `apps/admin/src/app/[locale]/(app)/accounts/page.tsx` |
| VAD-039 — القوائم محدودة بـ 200/300 سجل خادمياً بلا ترقيم ولا عدّاد إجمالي | P3 | بصري | الإدارة / accounts | M | `apps/admin/src/app/[locale]/(app)/accounts/page.tsx`، `packages/ui/src/components/data-table.tsx` |
| VAD-076 — لقطات ux-audit-F4 الموسومة phone مأخوذة على 1280×720 بمقياس 2 لا على 390 px — لا تثبت تخطيط الهاتف | P3 | تقني | الإدارة / accounts | S | `scripts/ux-audit-capture.mjs` |
| VAD-086 — الصف بأكمله ليس رابطاً؛ الرابط على المرجع فقط (نص mono عريض بلا تسطير حتى التحويم) | P3 | وصول | الإدارة / accounts | S | `packages/ui/src/components/data-table.tsx`، `packages/ui/src/components.css` |
| VIS-073 — النقر على الخلفية يغلق حوارات الإجراءات المدمّرة، وبعد النجاح لا يوجد زر «تم» سوى X/Esc | P3 | بصري | الإدارة / ActionDialog (الرفض، الإغلاق، التصحيح اليدوي) | S | `packages/ui/src/components/dialog.tsx`، `apps/admin/src/components/ActionDialog.tsx` |
| A11Y-017 — رأس عمود الإجراءات فارغ (axe empty-table-header) | P3 | وصول | الإدارة / admin-products, admin-coupons | S | `apps/admin/src/app/[locale]/(app)/products/ProductsClient.tsx`، `apps/admin/src/app/[locale]/(app)/coupons/CouponsClient.tsx` |
| A11Y-039 — إرسال نموذج دخول الموظفين فارغاً لا يُنتج أي رسالة خطأ في DOM (تحقق المتصفح الأصلي فقط) | P3 | وصول | الإدارة / admin-signin | S | `apps/admin/src/app/[locale]/(auth)/signin/SignInForm.tsx` |
| DEV-003 — حدّ «محاولات كثيرة» يُفعَّل بدخول متوازٍ لحساب واحد من أجهزة مختلفة | P3 | تقني | الإدارة / admin-signin | S | `docs/real-device-gate.md` |
| VAD-044 — «الفئة 1 · 15 % · 15 %» — النسبة مكررة | P3 | نص | الإدارة / affiliate | S | `apps/admin/src/app/[locale]/(app)/affiliates/[id]/page.tsx`، `packages/i18n/src/messages/ar.json` |
| VAD-047 — رمز القناة الخام «crypto» ورمز الدولة «AE» في مناطق غير خام | P3 | تقني | الإدارة / affiliate | S | `apps/admin/src/app/[locale]/(app)/affiliates/[id]/page.tsx`، `apps/admin/src/app/[locale]/(app)/fraud/[id]/page.tsx` |
| VAD-045 — «محجوزة → مستحقة» و«دفعة أسبوعية من USD 50» — سهم باتجاه خاطئ والرمز قبل الرقم | P3 | RTL | الإدارة / affiliates | S | `packages/i18n/src/messages/ar.json`، `apps/admin/src/app/[locale]/(app)/affiliates/page.tsx` |
| VAD-046 — «الفئة 1 · 15 %» يلتف على ثلاثة أسطر في عمود ضيق | P3 | بصري | الإدارة / affiliates | S | `apps/admin/src/app/[locale]/(app)/affiliates/page.tsx` |
| VAD-057 — H1 طويل يضم التاريخ الكامل والمنطقة الزمنية | P3 | بصري | الإدارة / breach | S | `apps/admin/src/app/[locale]/(app)/breaches/[id]/page.tsx` |
| VAD-055 — شارة «تم» بأيقونة X بلون الخطر لعملية إقفال ناجحة — تناقض دلالي | P3 | بصري | الإدارة / breaches | S | `apps/admin/src/app/[locale]/(app)/breaches/page.tsx`، `apps/admin/src/components/StatusPills.tsx` |
| VAD-056 — «0.00» كحدّ لمخالفات انتهاء المدة وعدم النشاط | P3 | بصري | الإدارة / breaches | S | `apps/admin/src/app/[locale]/(app)/breaches/page.tsx` |
| VAD-074 — عناوين الصفحات تخالف أسماء الشريط الجانبي: «المخالفات والأدلة» / «المخالفات»، «الطلبات والمدفوعات» / «الطلبات»، «مدفوعات بانتظار الاعتماد» / «المدفوعات اليدوية»، «التحكم بالمحاكي» / «المحاكي» | P3 | نص | الإدارة / breaches | S | `packages/i18n/src/messages/ar.json`، `apps/admin/src/app/[locale]/(app)/layout.tsx` |
| VAD-062 — حالة فارغة تحيل إلى «الزر أعلاه» بدل أن تضم الإجراء | P3 | بصري | الإدارة / coupons | S | `apps/admin/src/app/[locale]/(app)/coupons/CouponsClient.tsx` |
| VAD-081 — رقيقة «الكل» وحيدة في صفحة طلبات الحذف — فلتر بلا خيارات | P3 | بصري | الإدارة / deletions | S | `apps/admin/src/app/[locale]/(app)/deletions/page.tsx`، `apps/admin/src/components/Filters.tsx` |
| VAD-058 — خط اتجاه بنقطتين (خط مستقيم) لا يحمل معلومة | P3 | بصري | الإدارة / finance | S | `apps/admin/src/app/[locale]/(app)/finance/page.tsx`، `apps/admin/src/components/Finance.tsx` |
| VAD-073 — شريط علوي فارغ بلا عنوان في صفحة «لا صلاحية» | P3 | بصري | الإدارة / forbidden | S | `apps/admin/src/components/shell/AdminShell.tsx` |
| VAD-048 — وحدات إنجليزية «1.0 h» و«240 min» في واجهة عربية | P3 | نص | الإدارة / fraud-links | S | `apps/admin/src/app/[locale]/(app)/fraud/links/[userId]/page.tsx`، `apps/admin/src/app/[locale]/(app)/settings/page.tsx` |
| VAD-070 — معرّف مبتور «seed-device-shar» بلا علامة حذف | P3 | بصري | الإدارة / fraud-links | S | `apps/admin/src/app/[locale]/(app)/fraud/links/[userId]/page.tsx` |
| VAD-063 — قائمة المنتج فارغة بلا نص إرشادي «اختر منتجاً…» | P3 | نص | الإدارة / grant | S | `apps/admin/src/app/[locale]/(app)/orders/grant/page.tsx` |
| VAD-084 — زر «منح الحساب» الأساسي في الزاوية السفلية اليسرى بينما تبدأ البطاقة من اليمين وتُقرأ منه | P3 | بصري | الإدارة / grant | S | `apps/admin/src/app/[locale]/(app)/orders/grant/page.tsx`، `apps/admin/src/components/ActionForm.tsx` |
| VAD-060 — عدّاد القسم في الطرف المقابل من العنوان (1,100 px بعيداً) فيفقد ارتباطه | P3 | بصري | الإدارة / health | S | `packages/ui/src/components/page-header.tsx`، `apps/admin/src/app/[locale]/(app)/health/HealthClient.tsx` |
| VAD-061 — «إرسال تنبيه تجريبي» هو الإجراء الأساسي الأحمر الوحيد في صفحة مراقبة | P3 | بصري | الإدارة / health | S | `apps/admin/src/app/[locale]/(app)/health/HealthClient.tsx` |
| VAD-082 — «السرعة: الزمن الحقيقي ×1» و«وقت المحاكي» في صفحة صحة الإنتاج بلا تمييز أنها محاكاة | P3 | نص | الإدارة / health | S | `apps/admin/src/app/[locale]/(app)/health/HealthClient.tsx` |
| VAD-068 — جدار نص تشغيلي بلا ترقيم ولا أزرار نسخ لأسماء المتغيرات | P3 | بصري | الإدارة / integrations | S | `apps/admin/src/app/[locale]/(app)/integrations/page.tsx` |
| VAD-077 — طابور التحقق بلا رقيقة «موافَق عليها» ولا نطاق زمني — لا يمكن مراجعة قرار سابق | P3 | بصري | الإدارة / kyc | S | `apps/admin/src/app/[locale]/(app)/kyc/page.tsx` |
| VAD-072 — اختلاف متصفحي: Firefox يعرض المبلغ داخل البريد بخط mono احتياطي أرفع وحالة «قيد المعالجة» بلون كهرماني بدل البني الداكن | P3 | بصري | الإدارة / mail-detail | S | `packages/adapters/src/mail/templates/layout.ts` |
| VAD-085 — التبويبان قصيران جداً (HTML · نص) ويبدآن من يسار العمود بسبب VAD-002، والتفاصيل في العمود الأيسر بترتيب LTR | P3 | بصري | الإدارة / mail-detail | S | `apps/admin/src/app/[locale]/(app)/mail/[id]/page.tsx` |
| VAD-066 — 15 جلسة بزر «إلغاء» في الطرف المقابل لكل صف ولا «إلغاء كل الجلسات الأخرى» | P3 | بصري | الإدارة / me | S | `apps/admin/src/app/[locale]/(app)/staff/me/page.tsx` |
| VAD-054 — معرّفات خام في قيم عادية: «(fpt-50k v1)»، «fpt-rules-v1»، «terms-2026-10-29» | P3 | نص | الإدارة / order | S | `apps/admin/src/app/[locale]/(app)/orders/[id]/page.tsx`، `packages/db (product names seed)` |
| VAD-037 — عنوان القسم في الشريط العلوي يكرر H1 الصفحة مباشرة تحته (56 px مهدورة) | P3 | بصري | الإدارة / overview | S | `apps/admin/src/components/shell/AdminShell.tsx` |
| VAD-059 — شرارات بلا مدى زمني معلن (الحسابات النشطة، مخالفات اليوم) | P3 | بصري | الإدارة / overview | S | `apps/admin/src/app/[locale]/(app)/page.tsx`، `packages/ui/src/components/stat-tile.tsx` |
| VAD-078 — الإنجليزية: «USD 8,970.00» يلتصق الرمز بالرقم بحجمين مختلفين داخل بطاقة ضيقة فيلتف العنوان على سطرين | P3 | بصري | الإدارة / overview | S | `apps/admin/src/app/[locale]/(app)/page.tsx` |
| VAD-079 — «PART OF FIPER GLOBAL» بأحرف كبيرة وتباعد أحرف في الإنجليزية بينما العربية «جزء من Fiper Global» عادية | P3 | هوية | الإدارة / overview | S | `packages/ui/src/components/endorsement-line.tsx` |
| VAD-041 — «KB · PNG 74» — ترتيب الحجم والنوع مقلوب | P3 | RTL | الإدارة / payment | S | `apps/admin/src/app/[locale]/(app)/payments/[id]/page.tsx` |
| VAD-042 — نص موجّه للعميل «تحويل P2P من حسابك في فايبر» داخل جدول المشغّل | P3 | نص | الإدارة / payments | S | `apps/admin/src/app/[locale]/(app)/payments/page.tsx`، `packages/i18n/src/messages/ar.json` |
| VAD-043 — ملاحظة «Checks reviewed — first approval» بالإنجليزية تحت اسم المعتمد | P3 | نص | الإدارة / payout | S | `apps/admin/src/app/[locale]/(app)/payouts/[id]/page.tsx` |
| VAD-083 — حقول «التنفيذ —» و«مرجع التنفيذ —» و«الشهادة —» تُعرض فارغة قبل أوانها | P3 | بصري | الإدارة / payout | S | `apps/admin/src/app/[locale]/(app)/payouts/[id]/page.tsx` |
| VAD-040 — «عملة رقمية (USDT) (TRC20)» تنكسر على سطرين بأقواس معكوسة «USDT) / (TRC20» | P3 | RTL | الإدارة / payouts | S | `apps/admin/src/app/[locale]/(app)/payouts/page.tsx`، `packages/i18n/src/messages/ar.json` |
| VAD-071 — الطابور يفتح على «كل الطلبات» بما فيها المدفوعة بدل «ينتظر اعتمادي» | P3 | بصري | الإدارة / payouts | S | `apps/admin/src/app/[locale]/(app)/payouts/page.tsx` |
| VAD-053 — رمز «catalog_version» خام داخل جملة عربية | P3 | نص | الإدارة / products | S | `packages/i18n/src/messages/ar.json` |
| VAD-069 — أسماء العملاء تلتف على سطرين بينما أعمدة الأرقام تملك فراغاً | P3 | بصري | الإدارة / risk | S | `apps/admin/src/app/[locale]/(app)/risk/page.tsx` |
| VAD-050 — مسارات ورموز لاتينية داخل جمل عربية تُفكك القراءة: «المكان الوحيد لنصوص الكيان — config/legal.ts يُقرأ من» | P3 | RTL | الإدارة / settings | S | `apps/admin/src/app/[locale]/(app)/settings/page.tsx`، `apps/admin/src/app/[locale]/(app)/integrations/page.tsx`، `packages/i18n/src/messages/ar.json` |
| VAD-051 — قيمة نائبة «العنوان المسجّل يُدرج لاحقاً (مسودة، 29 أكتوبر 2026)» في إعدادات الإنتاج | P3 | نص | الإدارة / settings | S | `apps/admin/src/app/[locale]/(app)/settings/page.tsx` |
| VAD-052 — «no-reply@fiperprotraders.com» ينكسر عند الواصلة إلى «no-» / «reply@…» | P3 | بصري | الإدارة / settings | S | `apps/admin/src/app/[locale]/(app)/settings/page.tsx`، `packages/ui/src/components.css` |
| VAD-034 — زر الدخول يحمل تسمية عامة «متابعة» بدل «تسجيل الدخول» | P3 | نص | الإدارة / signin | S | `apps/admin/src/app/[locale]/(auth)/signin`، `packages/i18n/src/messages/ar.json` |
| VAD-035 — بطاقة الدخول ملتصقة بأعلى الشاشة وتترك ثلثي الارتفاع فارغاً | P3 | بصري | الإدارة / signin | S | `apps/admin/src/app/[locale]/(auth)/layout.tsx` |
| VAD-049 — علامات الاقتباس المائلة لـ Markdown تظهر حرفياً: «أمر `sim_*`»، «`breach_reverse`»، «`affiliates_run_weekly`» | P3 | نص | الإدارة / simulator | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| VAD-075 — صفحة الحساب على الهاتف: ثلاثة أزرار إجراء مكدسة قبل الأرقام ثم تبويبات LTR | P3 | وصول | الإدارة / staff | S | `apps/admin/src/app/[locale]/(app)/accounts/[id]/page.tsx` |
| VAD-080 — سجل الدخول يعرض «بصمة IP cb87cdde» بسطرين وUTC لكل عنصر — 6 عناصر تملأ الشاشة ولا تعرض المتصفح/الجهاز | P3 | بصري | الإدارة / staff-detail | S | `apps/admin/src/app/[locale]/(app)/staff/[id]/page.tsx` |
| TXT-079 — أسماء الأوضاع غير مترجمة في صفحة وترجمة في أخرى | P3 | نص | الإدارة / أوضاع التكامل | S | `packages/i18n/src/messages/ar.json` |
| TXT-071 — dismiss (إنذار كاذب) تُترجم «رفض» وهي كلمة رفض الهوية والدفعة | P3 | نص | الإدارة / إشارات الاحتيال | S | `packages/i18n/src/messages/ar.json` |
| TXT-077 — تعريب اسم المنتج يُعرض في الإدارة بينما المعجم يُبقي الاسم لاتينياً | P3 | هوية | الإدارة / إعدادات ← الكيان | S | `config/legal.ts` |
| A11Y-033 — عند الانتقال من خطوة إعادة المصادقة إلى التأكيد يُزال العنصر المركَّز فلا ينتقل التركيز إلى حقل السبب | P3 | وصول | الإدارة / الحوارات الحسّاسة (ActionDialog reauth) | S | `apps/admin/src/components/ActionDialog.tsx` |
| TXT-083 —  | P3 | نص | الإدارة / الزمن/الوقت الحقيقي | S | `packages/i18n/src/messages/ar.json` |
| VIS-070 — زر «المزيد» (button) يحمل aria-current="page"، وشرائح الفلتر تستعمل aria-current="true" | P3 | وصول | الإدارة / الشريط السفلي للإدارة («المزيد») · شرائح الفلاتر | S | `apps/admin/src/components/shell/AdminShell.tsx`، `apps/admin/src/components/Filters.tsx` |
| TXT-080 — اختصار «100k» وترجمة حرفية لـ journey | P3 | نص | الإدارة / المحاكي | S | `packages/i18n/src/messages/ar.json` |
| VIS-067 — الجدول محصور في 70vh بتمرير داخلي ورأسه غير لاصق — تمرير متداخل بلا سياق للأعمدة | P3 | بصري | الإدارة / جداول الإدارة على 1024+ | S | `packages/ui/src/components.css` |
| TXT-068 — queue = الطابور / قائمة الانتظار / الانتظار | P3 | نص | الإدارة / طابور الأوامر | S | `packages/i18n/src/messages/ar.json` |
| VIS-079 — أيقونة تسجيل الخروج تنعكس في العميل ولا تنعكس في الإدارة | P3 | RTL | الإدارة / قائمة الموظف (الإدارة) | S | `apps/admin/src/components/shell/AdminShell.tsx`، `packages/ui/src/icons.ts` |
| A11Y-031 — معلمان nav بالاسم نفسه في الصفحة الواحدة | P3 | وصول | الإدارة / كل شاشات الإدارة | S | `apps/admin/src/components/shell/AdminShell.tsx` |
| TEC-025 — رمز رسالة غير مترجم يُعرض كما هو للموظف (`state.message` خام) — تسرّب تقني محتمل عند أي رمز جديد | P3 | تقني | الإدارة / كل نماذج الإجراءات | S | `apps/admin/src/components/ActionForm.tsx` |
| TEC-026 — انتهاء جلسة الموظف أثناء نموذج يُظهر «انتهت جلستك» ثم يترك النموذج معلّقاً بلا توجيه إلى تسجيل الدخول | P3 | تقني | الإدارة / كل نماذج الإجراءات بعد خمول 15 دقيقة | S | `apps/admin/src/components/ActionForm.tsx`، `apps/admin/src/lib/session.ts` |
| TXT-072 — ترجمات حرفية غير دقيقة | P3 | نص | الإدارة / كلمات حالة تقنية | S | `packages/i18n/src/messages/ar.json` |
| TXT-063 — معرّفات لاتينية بفواصل لاتينية وواو عطف ملتصقة داخل العربية | P3 | RTL | الإدارة / نصوص تقنية داخل جمل عربية | S | `packages/i18n/src/messages/ar.json` |
| TXT-049 — عدم مطابقة في التأنيث | P3 | نص | مشترك / «(اختياري)» بعد اسم مؤنث | S | `packages/i18n/src/messages/ar.json` |
| TXT-060 — صيغة واحدة للمنطقة الزمنية مطلوبة | P3 | نص | مشترك / «توقيت دبي» بصيغ متعددة | S | `packages/i18n/src/messages/ar.json` |
| TXT-056 — صيغة ثقيلة مقابل «تعذّر» المستخدمة في العميل | P3 | نص | مشترك / «لم يمكن» | S | `packages/i18n/src/messages/ar.json` |
| TXT-086 — انتهاء المدة بصيغ متعددة و«صلاحية الحساب» غير دقيقة | P3 | نص | مشترك / «مرحلة التقييم» و«مدة التقييم» | S | `packages/i18n/src/messages/ar.json` |
| VIS-074 — علامات مكتوبة يدوياً بأصناف الحزمة بدل المكوّنات: عدّاد يدوي بأنماط مضمّنة، صف ui-row يدوي، dl.fpt-card، زر خروج بأصناف خام، زر 404 بأنماط مضمّنة | P3 | تقني | مشترك / /accounts (بلا هدف ربح) · /risk (جزئي) · /legal/[key] · /more (تسجيل الخروج) · 404 الجذر | S | `apps/client/src/components/account-overview.tsx`، `apps/admin/src/app/[locale]/(app)/risk/page.tsx`، `apps/client/src/app/[locale]/(public)/legal/[key]/page.tsx`، `apps/client/src/app/[locale]/(app)/more/page.tsx` |
| VIS-037 — سهم نصي «→» بين سعر الفتح والسوق، و«*» للنتيجة الجزئية بتلميح hover فقط | P3 | هوية | مشترك / /accounts/[id] (المراكز، الصفقات) · /accounts/[id]/breach · /products (المحاكي) | S | `apps/client/src/components/trades-panel.tsx`، `apps/client/src/components/account-overview.tsx`، `apps/admin/src/app/[locale]/(app)/products/ProductsClient.tsx` |
| VIS-047 — صنف text-body-1 غير معرّف في المقياس (لا يطبّق أي حجم) | P3 | تقني | مشترك / /checkout/[orderId]/transfer · /payments/[id] (الإدارة) | S | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/page.tsx`، `apps/admin/src/app/[locale]/(app)/payments/[id]/page.tsx` |
| VIS-071 — تباعد أسطر العربية ينزل تحت 1.75 في المساعدة والتنبيهات والحوارات | P3 | بصري | مشترك / /help (الإجابات) · عناوين البطاقات والحوارات · فقرات الوثائق | S | `apps/client/src/app/[locale]/(public)/help/help-search.tsx`، `packages/ui/src/components/alert.tsx` |
| VIS-046 — خاصية eyebrow تُمرَّر في 8 صفحات إدارة ويُهملها PageTitle، بينما PageHeader العام ما زال يرسمها فوق العناوين | P3 | تقني | مشترك / /orders/[id] · /payments · /kyc · /settings · /products · /fraud (الإدارة) · /help · /legal (العميل) | S | `apps/admin/src/components/PageTitle.tsx`، `packages/ui/src/components/page-header.tsx` |
| VIS-043 — حالة الفراغ غير موحّدة: EmptyState في بعض القوائم وفقرة رمادية في أخرى، وجدول الموظفين بلا حالة فراغ أصلاً | P3 | بصري | مشترك / /payouts (لا حساب ممول) · /notifications (مضمّن) · /partners (إحالات/عمولات) · الإدارة: /staff · overview · accounts/[id] · risk · settings | S | `packages/ui/src/components/data-table.tsx`، `apps/admin/src/app/[locale]/(app)/staff/page.tsx`، `apps/client/src/app/[locale]/(app)/payouts/page.tsx` |
| VIS-072 — زر «التالي» يُعطَّل بدل إظهار الخطأ، وبعد فشل الإرسال لا يُنقل التركيز إلى الخطأ | P3 | بصري | مشترك / /payouts/new (زر التالي) · ActionForm الإدارة | S | `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx`، `apps/admin/src/components/ActionForm.tsx` |
| TEC-022 — حقول البريد وIBAN/SWIFT بلا `spellCheck={false}` و`autoCapitalize`، وIBAN بلا `inputMode` | P3 | تقني | مشترك / /signin, /signup, /payouts/new (IBAN/SWIFT), لوحة: /signin | S | `apps/client/src/app/[locale]/(auth)/auth-form.tsx`، `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx`، `apps/admin/src/app/[locale]/(auth)/signin/SignInForm.tsx`، `packages/ui/src/components/input.tsx` |
| TEC-018 — تسمية حقل الرمز `htmlFor` تشير إلى `div role="group"` غير قابل للتسمية: النقر على التسمية لا يركّز الصندوق الأول و`aria-invalid` على مجموعة | P3 | وصول | مشترك / /verify, /2fa, /reset, لوحة: /2fa, /enrol | S | `packages/ui/src/components/otp-input.tsx`، `packages/ui/src/components/field.tsx` |
| VIS-080 — صفحات 404 والخطأ الجذرية تعيد المستخدم إلى /ar دائماً وتعرض العربية والإنجليزية معاً | P3 | تقني | مشترك / 404 الجذر · global-error | S | `apps/client/src/app/not-found.tsx`، `apps/admin/src/app/not-found.tsx` |
| A11Y-019 — تأكيد النسخ يتغيّر في aria-label فقط ولا يُعلَن لقارئ الشاشة | P3 | وصول | مشترك / account-credentials, admin mail-detail/staff (CopyBadge) | S | `apps/client/src/components/copy-button.tsx`، `apps/admin/src/components/CopyBadge.tsx`، `packages/ui/src/components/icon-button.tsx` |
| A11Y-026 — روابط تفتح نافذة جديدة دون إخبار المستخدم | P3 | وصول | مشترك / checkout (روابط المستندات), account-credentials (فتح cTrader), admin integrations/payouts/payments | S | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/checkout-form.tsx`، `apps/client/src/components/platform-open-buttons.tsx` |
| TEC-045 — صفحة الخطأ الجذرية بـ `lang="ar" dir="rtl"` ثابتين ونص ثنائي اللغة: الزائر الإنجليزي يرى تخطيطاً من اليمين؛ لا وسوم theme-color/color-scheme | P3 | تقني | مشترك / global-error (حدّ الخطأ الجذري) | S | `apps/client/src/app/global-error.tsx`، `apps/admin/src/app/global-error.tsx` |
| VIS-058 — شعار cTrader بلا width/height (انزياح تخطيط) وصورة الإثبات بلا loading=lazy | P3 | تقني | مشترك / PartnerRow (الهبوط، المزيد) · /payments/[id] (الإدارة) | S | `packages/ui/src/components/partner-row.tsx`، `apps/admin/src/app/[locale]/(app)/payments/[id]/page.tsx` |
| TXT-062 — نصوص placeholder لا تنتهي بـ «…» وواحدة تعمل كتلميح لا كمثال | P3 | نص | مشترك / Placeholders | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| TEC-036 — انتقال `transition: width` على شريط القياس يحرّك خاصية تخطيط لا `transform` (غير مناسب للمركّب) | P3 | أداء | مشترك / RuleMeter / Gauge / Progress | S | `brand/components/components.css`، `packages/ui/src/components/rule-meter.tsx` |
| TEC-034 — لا `overscroll-behavior: contain` على الطبقات: تمرير الورقة السفلية يسحب الصفحة خلفها (تأثير المطاط) | P3 | تقني | مشترك / Sheet / Dialog / Select / DropdownMenu على الهاتف | S | `packages/ui/src/components.css`، `packages/ui/src/components/sheet.tsx`، `packages/ui/src/components/dialog.tsx` |
| A11Y-038 — التركيز التلقائي عند التحميل يتجاوز عنوان الصفحة والتعليمات لقارئ الشاشة | P3 | وصول | مشترك / signin (client), admin-signin | S | `apps/client/src/app/[locale]/(auth)/auth-form.tsx` |
| TXT-048 — الكلمة الواحدة بثلاثة أشكال تشكيل (شدّة وفتحة / شدّة / بلا تشكيل) | P3 | نص | مشترك / التشكيل غير المتسق | S | `packages/i18n/src/messages/ar.json` |
| TXT-047 — حرف جرّ معلّق بتطويل قبل اسم، و«الـ» قبل رقم | P3 | RTL | مشترك / التطويل وأداة التعريف قبل الأرقام | S | `packages/i18n/src/messages/ar.json` |
| TXT-051 — Center/centre وprogram/programme في النصوص نفسها | P3 | نص | مشترك / التهجئة البريطانية/الأمريكية | S | `packages/i18n/src/messages/en.json` |
| VIS-063 — autoFocus على الهاتف يفتح لوحة المفاتيح فوراً ويقفز بالشاشة في 12 نموذجاً | P3 | بصري | مشترك / الدخول/التسجيل/التحقق · /payouts/new (كل خطوة) | S | `apps/client/src/app/[locale]/(auth)/auth-form.tsx`، `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx` |
| VIS-057 — تحويم Tailwind على الروابط المضمّنة غير مقيّد بوسيط hover — يلتصق بعد اللمس على iOS | P3 | بصري | مشترك / الروابط المضمّنة (16 موضعاً) | S | `apps/client/src/app/[locale]/(app)/accounts/page.tsx`، `apps/client/src/components/rules-table.tsx` |
| A11Y-044 — رابط التخطي يعمل (العميل والإدارة) لكنه 28 px ارتفاعاً ويُرسم فوق الرأس اللاصق | P3 | وصول | مشترك / الكل | S | `packages/ui/src/components/app-shell.tsx` |
| VIS-036 — أيقونات Lucide موحّدة عبر @fpt/ui/icons (70 ملفاً) — مع ثلاثة شوارد: استيراد مباشر، سبينر بسمك 2.5، وانحراف إصدار عن الحزمة | P3 | هوية | مشترك / المكوّنات المشتركة | S | `packages/ui/src/icons.ts`، `apps/client/src/components/print-button.tsx`، `packages/ui/src/components/spinner.tsx`، `packages/ui/package.json` |
| VIS-044 — مكوّنات مكررة أو غير مستعملة تبقى في الحزمة والتطبيقين (رسم بياني ثانٍ بمحاور 9.5px mono، FilterBar، AccountGauges، EquityCurve، Finance، useToast لا يُستدعى أبداً) | P3 | تقني | مشترك / المكوّنات المشتركة | S | `apps/client/src/components/charts.tsx`، `apps/admin/src/components/FilterBar.tsx`، `apps/admin/src/components/AccountGauges.tsx`، `apps/admin/src/components/EquityCurve.tsx`، `apps/admin/src/components/Finance.tsx` |
| TXT-053 — FPT مذكّر في جملة ومؤنث في البقية | P3 | نص | مشترك / تذكير/تأنيث «FPT» | S | `packages/i18n/src/messages/ar.json` |
| TXT-074 — ترتيب كلمات وحروف جرّ غير سليمة | P3 | نص | مشترك / تراكيب تحتاج إعادة ترتيب | S | `packages/i18n/src/messages/ar.json` |
| TXT-075 — تسميات حقول تُقرأ كأفعال أو بلا تعريف | P3 | نص | مشترك / تسميات بصيغة فعل أو ناقصة | S | `packages/i18n/src/messages/ar.json` |
| TXT-055 — «تعليم» تعني التدريس؛ والإقرار بالتنبيه يُترجم «كمقروء» بينما الإنجليزية “as seen” | P3 | نص | مشترك / تعليم كمقروء | S | `packages/i18n/src/messages/ar.json` |
| TXT-052 — عناوين مطابقة بأسلوبين (Title Case من الموقع وSentence case في التطبيق) | P3 | نص | مشترك / حالة الأحرف في العناوين الإنجليزية | S | `packages/i18n/src/messages/en.json` |
| TXT-082 — «معلّق» للطلب غير المدفوع توحي بالتعليق/الإيقاف | P3 | نص | مشترك / حالة الطلب في الإدارة | S | `packages/i18n/src/messages/ar.json` |
| TEC-017 — نص بديل مكرر: `<img alt="Fiper Pro Traders">` داخل رابط يحمل `aria-label` بالاسم نفسه (axe image-redundant-alt — الفشل الوحيد في Lighthouse Accessibility 99) | P3 | وصول | مشترك / رأس الصفحة (الشعار داخل رابط) | S | `apps/client/src/components/shell.tsx`، `packages/ui/src/components/logo.tsx`، `apps/admin/src/components/shell/AdminShell.tsx` |
| TXT-076 — cap بثلاثة أفعال (حُدَّ / سقف / خُصم) و«حُدَّ» صعبة القراءة | P3 | نص | مشترك / سقف ربح الأخبار | S | `packages/i18n/src/messages/ar.json` |
| TXT-066 — تراكيب غير طبيعية وحروف كبيرة صارخة | P3 | نص | مشترك / صياغات إنجليزية ركيكة | S | `packages/i18n/src/messages/en.json` |
| VIS-041 — قيم خارج مقياس 4pt وقيم عشوائية [..] متناثرة (6/10/14px، 1.5px، 22px، 26px) | P3 | بصري | مشترك / عام | M | `packages/ui/src/components.css`، `packages/ui/src/components/checkbox.tsx`، `packages/ui/src/components/switch.tsx` |
| VIS-042 — rounded-sm / rounded-md / rounded-[6px] غير مربوطة برموز الهوية (4px/6px خارج المقياس 8·12·14·16·18) | P3 | هوية | مشترك / عام | S | `packages/ui/src/tokens.css`، `packages/ui/src/components/checkbox.tsx`، `apps/client/src/components/charts.tsx` |
| TXT-050 — فاصلة عليا مستقيمة وأخرى منحنية، وثلاثة أنماط اقتباس («»، “”، "") في النصوص الإنجليزية | P3 | نص | مشترك / علامات الاقتباس والفاصلة العليا في الإنجليزية | S | `packages/i18n/src/messages/en.json` |
| TXT-069 — جمعان لكلمة واحدة | P3 | نص | مشترك / فحوص/فحوصات | S | `packages/i18n/src/messages/ar.json` |
| TEC-005 — HSTS الفعلي في الإنتاج هو الافتراضي للمنصة (`max-age=63072000`) بلا `includeSubDomains; preload` خلافاً للمصدر | P3 | تقني | مشترك / كل الاستجابات | S | `packages/core/src/csp.ts`، `apps/client/next.config.ts`، `apps/admin/next.config.ts` |
| TEC-035 — لا `translate="no"` على «Fiper Pro Traders / FPT / cTrader» والرموز (رقم الحساب، الخادم، IBAN، العناوين): الترجمة الآلية في المتصفح تشوّهها | P3 | RTL | مشترك / كل الشاشات (أسماء العلامة والرموز) | S | `packages/ui/src/components/logo.tsx`، `packages/ui/src/components/money.tsx`، `packages/ui/src/components/date-time.tsx`، `apps/client/src/components/credentials-panel.tsx` |
| TEC-033 — لا هياكل تحميل لأي مسار: كل تنقّل ينتظر تصيير الخادم الكامل (قاعدة البيانات) وتبقى الشاشة الحالية جامدة | P3 | أداء | مشترك / كل المسارات الديناميكية (accounts, account/[id], payouts, orders, لوحة القوائم) | M | `apps/client/src/app/[locale]/(app)/accounts`، `apps/client/src/app/[locale]/(app)/payouts`، `apps/admin/src/app/[locale]/(app)`، `packages/ui/src/components/skeleton.tsx` |
| TEC-006 — كوكي اللغة `NEXT_LOCALE` تُرسل بلا `Secure` (ومن الوسيط بلا HttpOnly) بينما `/api/lang` يضبط الكوكي نفسها بـ Secure | P3 | تقني | مشترك / كل المستندات | S | `apps/client/src/i18n/routing.ts`، `apps/admin/src/i18n/routing.ts`، `apps/client/src/app/api/lang/route.ts` |
| VIS-064 — لا زر إظهار/إخفاء كلمة المرور | P3 | بصري | مشترك / كل حقول كلمة المرور | S | `packages/ui/src/components/input.tsx`، `apps/client/src/app/[locale]/(auth)/auth-form.tsx` |
| TXT-070 — document بترجمتين | P3 | نص | مشترك / مستند/وثيقة | S | `packages/i18n/src/messages/ar.json` |
| TXT-059 — مفاتيح غير مستخدمة تحمل نموذج «كلمة مرور المنصة تُعرض مرة واحدة» الذي تخلّى عنه المنتج | P3 | نص | مشترك / مفاتيح ميتة أو مكررة | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `packages/i18n/test/parity.test.ts` |
| TXT-061 — شرطة طويلة بمسافات، وبلا مسافات، و«من… إلى» للنطاق نفسه | P3 | نص | مشترك / نطاقات الأرقام | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| TEC-032 — 404 الجذر: وسم viewport مكرر، بلا `theme-color`/`color-scheme`/manifest، و`lang="ar"` ثابت للزائر الإنجليزي | P3 | هوية | العميل / /<مسار غير موجود> (404 الجذر، خارج اللغة) | S | `apps/client/src/app/not-found.tsx`، `apps/admin/src/app/not-found.tsx` |
| VIS-082 — عدد الأيام يتحوّل أخضر عند بلوغ الحد بلا كلمة أو أيقونة | P3 | وصول | العميل / /accounts/[id] (أيام التداول) · /partners (الدرجة) | S | `apps/client/src/components/account-overview.tsx` |
| TEC-044 — مهلة الخمول (30 دقيقة) معطّلة عملياً ما دام تبويب الحساب مرئياً: الاستطلاع كل ثانية يلمس `last_seen_at` | P3 | تقني | العميل / /accounts/[id] (التبويب نظرة عامة مفتوح) | S | `apps/client/src/lib/session.ts`، `apps/client/src/app/api/accounts/[id]/live/route.ts` |
| TEC-049 — SVG الرسم يتمدّد بعرض الحاوية (viewBox 358×200، `w-full`) فتتضخّم نصوص المحاور بنسبة التمدّد على سطح المكتب | P3 | أداء | العميل / /accounts/[id] (الرسم البياني على 1440) | S | `packages/ui/src/components/equity-chart.tsx` |
| TEC-019 — زر النسخ يعلن النجاح بتغيير `aria-label` فقط (لا منطقة حية) ويبتلع فشل الحافظة بصمت | P3 | وصول | العميل / /accounts/[id] (تبويب البيانات)، الشهادات، الشركاء | S | `apps/client/src/components/copy-button.tsx` |
| VIS-051 — خمسة تبويبات بعرض 360px تتمرّر أفقياً بشريط مخفي بلا أي إشارة إلى وجود المزيد | P3 | بصري | العميل / /accounts/[id] على 360px مع تبويب «تداول من المتصفح» | S | `apps/client/src/app/[locale]/(app)/accounts/[id]/page.tsx`، `packages/ui/src/components.css` |
| VIS-049 — رابط الصفحة السابقة «المعطّل» يبقى قابلاً للتركيز والتفعيل بلوحة المفاتيح | P3 | وصول | العميل / /accounts/[id]/trades (ترقيم الصفحات) | S | `apps/client/src/components/trades-panel.tsx` |
| TEC-007 — تبديل اللغة عبر GET يعدّل الملف الشخصي: تنقّل من موقع آخر يكفي لقلب لغة المستخدم المسجّل | P3 | تقني | العميل / /api/lang | S | `apps/client/src/app/api/lang/route.ts`، `apps/client/src/lib/csrf.ts` |
| TEC-050 — تسجيل الخروج متاح بـ GET (موثّق كمقبول): رابط خارجي يستطيع إخراج المستخدم قسراً | P3 | تقني | العميل / /api/signout عبر GET | S | `apps/client/src/app/api/signout/route.ts` |
| TEC-043 — روابط الشروط تفتح نافذة جديدة (`target="_blank"`) بلا إشارة للمستخدم ولا `rel` | P3 | وصول | العميل / /checkout/[orderId] (روابط المستندات القانونية) | S | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/checkout-form.tsx` |
| TEC-042 — فشل رفع الإثبات بسبب انتهاء الجلسة (401) يُعرض كخطأ عام بلا دعوة لتسجيل الدخول | P3 | تقني | العميل / /checkout/[orderId]/transfer (رفع الإثبات) | S | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/transfer-form.tsx` |
| VIS-075 — رفع ملف حتى 5MB بلا نسبة تقدم — سبينر فقط | P3 | بصري | العميل / /checkout/[orderId]/transfer (رفع الإثبات) | M | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/transfer-form.tsx` |
| VIS-060 — شرائح التصنيف تتمرّر أفقياً بلا إشارة بدل الالتفاف | P3 | بصري | العميل / /help | S | `apps/client/src/app/[locale]/(public)/help/help-search.tsx` |
| VIS-053 — سهم الرجوع ثابت إلى /more مهما كان المصدر (الجرس في الرئيسية ← الإشعارات ← رجوع إلى «المزيد») | P3 | بصري | العميل / /notifications · /certificates · /profile · /help · /legal | S | `apps/client/src/components/client-header.tsx` |
| VIS-078 — الإشعارات تقطع عند 100 بلا ترقيم؛ الإحالات والسجل بلا حد | P3 | تقني | العميل / /notifications · /partners (الإحالات) · /payouts (السجل) | S | `apps/client/src/app/[locale]/(app)/notifications/page.tsx`، `apps/client/src/app/[locale]/(app)/partners/page.tsx` |
| VIS-061 — أنماط مضمّنة للتخطيط والخط في وثائق الورق بدل أصناف documents.css | P3 | تقني | العميل / /orders/[id]/receipt · /payouts/[id]/statement · CertificateCard | S | `apps/client/src/app/documents.css`، `apps/client/src/app/[locale]/(app)/orders/[id]/receipt/page.tsx`، `apps/client/src/app/[locale]/(app)/payouts/[id]/statement/page.tsx` |
| VIS-077 — سجل الدفعات صندوق لكل عنصر — بينما كل القوائم الأخرى صفوف بفواصل | P3 | هوية | العميل / /payouts (سجل الطلبات) | S | `apps/client/src/app/[locale]/(app)/payouts/page.tsx` |
| VIS-065 — نموذج الدفعة بأربع خطوات يحتفظ بكل شيء في الذاكرة بلا تحذير قبل المغادرة ولا حفظ مسودة | P3 | تقني | العميل / /payouts/new (4 خطوات) | S | `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx` |
| TEC-041 — أخطاء الخادم على حقول الخطوات السابقة تُعرض في المراجعة كتنبيه عام «حدث خطأ» دون تسمية الحقل ولا نقل التركيز | P3 | وصول | العميل / /payouts/new (خطوة المراجعة) | S | `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx` |
| TEC-051 — تأكيد: صفوف الاختيار تجمع التسمية والتحكم في هدف لمس واحد 56 px مع `sr-only` + `peer-focus-visible` — لا عيب؛ ينقص `aria-describedby` لملاحظة «يُنفَّذ يدوياً» | P3 | وصول | العميل / /payouts/new, /checkout (صفوف الاختيار) | S | `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx` |
| TEC-021 — لا تحذير قبل مغادرة نموذج فيه بيانات مكتوبة (`beforeunload`/حارس الموجّه غير موجود في أي ملف) | P3 | تقني | العميل / /payouts/new, /profile (الإغلاق، KYC، البريد), /partners | S | `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx`، `apps/client/src/app/[locale]/(app)/profile/closure-form.tsx`، `apps/client/src/app/[locale]/(app)/profile/kyc-section.tsx` |
| VIS-062 — قيم هكس خام ('#FFFFFF', '#0A0A0A') في كود المكوّن لرمز QR | P3 | هوية | العميل / /profile (QR) | S | `apps/client/src/app/[locale]/(app)/profile/forms.tsx` |
| VIS-076 — عند غياب منتجات نشطة يُرسم المتجر والهبوط فارغين بلا رسالة | P3 | بصري | العميل / /shop · / (الهبوط) | S | `apps/client/src/app/[locale]/(app)/shop/challenge-picker.tsx`، `apps/client/src/app/[locale]/(public)/page.tsx` |
| VIS-052 — تبويب «الحساب» يُضاء وأنت في المتجر وصفحة الدفع | P3 | بصري | العميل / /shop · /checkout | S | `apps/client/src/components/bottom-nav.tsx` |
| TEC-023 — خطأ تسجيل الدخول يُعرض كتنبيه أعلى النموذج بلا `aria-invalid` على الحقول وبلا نقل التركيز إلى الخطأ | P3 | وصول | العميل / /signin (كلمة المرور) | S | `apps/client/src/app/[locale]/(auth)/auth-form.tsx` |
| TEC-020 — `autoFocus` على الهاتف في تسجيل الدخول وخطوات الصرف يفتح لوحة المفاتيح فور التحميل ويدفع المحتوى | P3 | تقني | العميل / /signin, /signup, /reset, /payouts/new | S | `apps/client/src/app/[locale]/(auth)/auth-form.tsx`، `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx`، `packages/ui/src/components/input.tsx` |
| VCL-048 — صفحة 404 الجذرية تعرض الرسالة بالعربية والإنجليزية معاً بلا رأس ولا تبويبات | P3 | نص | العميل / 404-root | S | `apps/client/src/app/not-found.tsx` |
| A11Y-042 — سطر حالة المقياس المضغوط 12 px على الهاتف — عند أرضية الحجم الدنيا تماماً | P3 | وصول | العميل / account (phone) — RuleMeterRow | S | `packages/ui/src/components.css` |
| VCL-059 — مفتاح المخطط نص 12 px رمادي على سطر واحد مزدحم أسفل البطاقة | P3 | بصري | العميل / account / home (chart) | S | `packages/ui/src/components/equity-chart.tsx` |
| VCL-058 — «ابدأ تحدياً جديداً» زر أحمر كامل العرض على حساب موقوف يسبق تحليل المخالفة | P3 | بصري | العميل / account-breached | S | `apps/client/src/app/[locale]/(app)/accounts/[id]/page.tsx` |
| VCL-070 — خمسة أزرار منصّة متساوية الوزن تلتف على سطرين مع شارة «يعمل بعد الربط» | P3 | بصري | العميل / account-credentials | M | `apps/client/src/components/platform-open-buttons.tsx` |
| VCL-071 — تسمية مختلطة الاتجاه «cTrader ID (البريد)» بقوسين عربيين داخل لاتيني | P3 | نص | العميل / account-credentials | S | `packages/i18n/src/messages/ar.json` |
| A11Y-036 — المقاطع اللاتينية داخل العربية معزولة اتجاهياً (bdi/dir=ltr) لكن بلا lang=en فتُنطق بصوت عربي | P3 | وصول | العميل / account-credentials, more (cTrader), landing badge | S | `apps/client/src/components/platform-open-buttons.tsx`، `apps/client/src/components/credentials-panel.tsx` |
| VCL-057 — المبالغ في الفقرة مكتوبة «94,100.00 دولار» بدل «94,100.00 USD» | P3 | هوية | العميل / breach | S | `apps/client/src/app/[locale]/(app)/accounts/[id]/breach/page.tsx`، `packages/i18n/src/messages/ar.json` |
| VCL-063 — خط التوقيع تحت اسم المتداول ثم خط فاصل آخر بعده مباشرة — خطان متجاوران | P3 | بصري | العميل / certificates | S | `apps/client/src/components/certificate-card.tsx` |
| VCL-064 — «شهادة · Certificate · FPT-CRWFSEA8F4» يمزج اللغتين في سطر واحد | P3 | نص | العميل / certificates / verify | S | `apps/client/src/components/certificate-card.tsx` |
| VCL-078 — زر «تطبيق» بحجم زر أساسي (56 px) لفعل ثانوي اختياري | P3 | بصري | العميل / checkout | S | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/checkout-form.tsx` |
| VCL-074 — لقطتا checkout وcheckout-transfer «العربية» على iPhone التُقطتا بالإنجليزية | P3 | تقني | العميل / checkout (iPhone) | S | `e2e/screens.ts`، `scripts/real-device-coverage.mjs` |
| VCL-047 — النص يتسلل خلف الرأس ويبقى مقروءاً جزئياً فوق زر الرجوع | P3 | بصري | العميل / checkout / checkout-transfer | S | `packages/ui/src/components.css`، `brand/tokens.css` |
| VCL-022 — المؤشر على الهاتف أرقام وأيقونات بلا تسميات | P3 | بصري | العميل / checkout / payout-new / checkout-transfer | S | `apps/client/src/components/purchase-steps.tsx`، `apps/client/src/components/payout-tracker.tsx` |
| VCL-020 — تاريخ أصلي بتنسيق «29. Oct 2026» بخط mono على iOS و«10/29/2026» على Chromium | P3 | بصري | العميل / checkout-transfer | S | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/transfer-form.tsx` |
| A11Y-035 — حقل التاريخ في نموذج التحويل اليدوي قيس 134×31 على iPhone حقيقي — أُصلح في CSS ولم يُعَد قياسه على الجهاز | P3 | وصول | العميل / checkout-transfer (iPhone 15 Pro Max) | S | `packages/ui/src/components.css` |
| VCL-021 — عنوان الرأس يبقى «إتمام الشراء» على شاشات التحويل والانتظار | P3 | نص | العميل / checkout-transfer / order (waiting) | S | `apps/client/src/app/[locale]/(app)/checkout/[orderId]/transfer/page.tsx`، `apps/client/src/app/[locale]/(app)/orders/[id]/page.tsx` |
| VCL-037 — شرائح التصنيف تُقصّ عند الحافة اليسرى بلا تدرّج يدل على التمرير الأفقي | P3 | بصري | العميل / help | S | `apps/client/src/app/[locale]/(public)/help/help-search.tsx` |
| VCL-038 — «مركز المساعدة» يتكرر كسطر علوي صغير وعنوان كبير تحته مباشرة | P3 | نص | العميل / help | S | `apps/client/src/app/[locale]/(public)/help/page.tsx` |
| DEV-002 — رفض وعد غير معالج «Load failed» عند التنقّل السريع على سفاري iOS | P3 | تقني | العميل / home | S | `apps/client/src/components/account-overview.tsx` |
| VCL-060 — «فتح الحساب» يظهر مرتين على الرئيسية (زر كبير تحت البطل وزر ghost تحت المخطط) | P3 | بصري | العميل / home | S | `apps/client/src/app/[locale]/(app)/accounts/page.tsx` |
| VCL-061 — قيمة الحساب الآخر «49,863.00» بلا رمز عملة وبلا تسمية | P3 | بصري | العميل / home | S | `apps/client/src/app/[locale]/(app)/accounts/page.tsx` |
| VCL-028 — شيفرون فتح التفاصيل بعيد عن عنوان العدّاد في الطرف المقابل | P3 | بصري | العميل / home / account | S | `packages/ui/src/components/rule-meter.tsx` |
| A11Y-043 — مخطط الرصيد صورة واحدة بملخّص نصي — لا جدول بيانات بديل لقراءة النقاط | P3 | وصول | العميل / home / account — EquityChart | M | `packages/ui/src/components/equity-chart.tsx` |
| VCL-046 — نقطتان حمراوان لغير المقروء في آن واحد: على جرس الرأس وعلى تبويب «المزيد» | P3 | بصري | العميل / home / more | S | `apps/client/src/components/bottom-nav.tsx`، `apps/client/src/components/client-header.tsx` |
| VCL-075 — التخطيط يصمد عند النص الكبير — لا قصّ ولا تداخل (ملاحظة إيجابية مع تحفظ) | P3 | بصري | العميل / home-largetext | S | `packages/ui/src/components/equity-chart.tsx` |
| A11Y-032 — اسم معلم المنطقة يحمل قيمة حيّة متغيّرة | P3 | وصول | العميل / home, account | S | `packages/ui/src/components/account-hero.tsx` |
| VCL-062 — زر «اختر ›» نص عريض عند الطرف الأيسر بلا هيئة زر وبشيفرون معكوس | P3 | بصري | العميل / landing | S | `apps/client/src/app/[locale]/(public)/page.tsx` |
| A11Y-016 — نص بديل مكرّر بجانب اسم العلامة المكتوب (axe image-redundant-alt) | P3 | وصول | العميل / landing, signin, signup, reset, help | S | `packages/ui/src/components/logo.tsx` |
| VCL-072 — تذييل الصفحة القانونية خمسة روابط في سطر 13 px بلا تباعد لمسي | P3 | بصري | العميل / legal-terms | S | `apps/client/src/app/[locale]/(public)/legal/[key]/page.tsx` |
| VCL-080 — التاريخ «2026-10-29» ينكسر في منتصفه بين سطرين في عنوان المستند على 390 | P3 | RTL | العميل / legal-terms | S | `packages/db/src/legal.ts`، `apps/client/src/app/[locale]/(public)/legal/[key]/page.tsx` |
| VCL-045 — صفحة «المزيد» تبدأ ببريد mono رمادي بلا اسم المستخدم ولا هوية | P3 | بصري | العميل / more | S | `apps/client/src/app/[locale]/(app)/more/page.tsx` |
| VCL-049 — نقطة فاصلة يتيمة قبل الاسم: «· Khalid Al-Mansoori» | P3 | بصري | العميل / order-receipt | S | `apps/client/src/app/[locale]/(app)/orders/[id]/receipt/page.tsx` |
| VCL-076 — مبلغ الطلب «90.00 USD» في أقصى اليسار بينما الشارة والتاريخ في اليمين — الصف طويل بثلاث كتل | P3 | بصري | العميل / orders | S | `apps/client/src/app/[locale]/(app)/orders/page.tsx` |
| VCL-066 — «15 % 15 % — من 1 إلى 9 مبيعات» النسبة مكررة في الصف نفسه | P3 | بصري | العميل / partners | S | `apps/client/src/app/[locale]/(app)/partners/page.tsx` |
| VCL-067 — الرابط ينكسر بـ break-all في منتصف كلمة «sign/up» | P3 | بصري | العميل / partners | S | `apps/client/src/app/[locale]/(app)/partners/page.tsx` |
| VCL-053 — «الربح المطلوب سحبه (دولار)» بكلمة «دولار» بينما اللاحقة والتلميح يستخدمان USD | P3 | نص | العميل / payout-new | S | `packages/i18n/src/messages/ar.json` |
| VCL-055 — «12 / 10» للأيام المموَّلة تُقرأ كتاريخ أو كسر | P3 | نص | العميل / payout-new | S | `apps/client/src/app/[locale]/(app)/payouts/new/page.tsx`، `packages/i18n/src/messages/ar.json:254` |
| A11Y-041 — وسم «تنفيذ يدوي» داخل تسمية القناة بحجم 13 px muted — ضمن الاسم المنطوق لكنه صغير بصرياً | P3 | وصول | العميل / payout-new (خطوة القناة) | S | `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx` |
| VCL-054 — الخانة الثالثة «أيام التداول المموَّلة 12 / 10» وحدها في صف جديد بعرض نصف | P3 | بصري | العميل / payout-new / payout-review | S | `apps/client/src/app/[locale]/(app)/payouts/new/page.tsx` |
| VCL-052 — متتبع الطلب في القائمة أرقام «1 ✓ … 5 ✓» بلا كلمات | P3 | بصري | العميل / payouts | S | `apps/client/src/components/payout-tracker.tsx`، `apps/client/src/app/[locale]/(app)/payouts/page.tsx` |
| VCL-042 — رسالتان متتاليتان بالمعنى نفسه: «تحتاج إلى حساب مموَّل لطلب سحب» ثم «لا طلبات سحب بعد… تُطلب من حساب مموَّل» | P3 | نص | العميل / payouts-empty | S | `apps/client/src/app/[locale]/(app)/payouts/page.tsx`، `packages/i18n/src/messages/ar.json` |
| VCL-035 — ثلاثة نماذج متتالية (تغيير البريد، كلمة المرور، المصادقة) مكشوفة دفعة واحدة بحقول فارغة | P3 | بصري | العميل / profile | M | `apps/client/src/app/[locale]/(app)/profile/page.tsx`، `apps/client/src/app/[locale]/(app)/profile/email-section.tsx` |
| VCL-033 — على سطح المكتب الحقلان غير متحاذيين: الاسم الأول ينزل تحت مستوى اسم العائلة بسبب نص مساعد تحت أحدهما فقط | P3 | بصري | العميل / profile (desktop) | S | `apps/client/src/app/[locale]/(app)/profile/forms.tsx` |
| TEC-030 — إصدار المخزن ثابت `fpt-client-v2`: نسخ HTML القديمة تبقى عبر النشرات، والتفعيل الفوري بلا أي إشعار «تحديث متاح» | P3 | تقني | العميل / PWA — تحديث عامل الخدمة | S | `apps/client/public/sw.js`، `apps/client/src/components/sw-register.tsx` |
| TEC-031 — مخزن `_next/static` و`/brand` cache-first بلا سقف ولا تنظيف: ينمو مع كل نشرة | P3 | أداء | العميل / PWA — مخزن STATIC | S | `apps/client/public/sw.js` |
| VCL-050 — «50,000 USD» ثم «تقييم 50,000 USD» تحته مباشرة — تكرار الرقم | P3 | بصري | العميل / shop-product | S | `apps/client/src/app/[locale]/(app)/shop/[productId]/page.tsx` |
| A11Y-027 — رابط استعادة كلمة المرور بلا تسطير أو تمييز إلا عند التحويم | P3 | وصول | العميل / signin | S | `apps/client/src/app/[locale]/(auth)/signin/page.tsx` |
| VCL-069 — «بالمتابعة فإنك تقبل شروط الخدمة وسياسة الخصوصية» بلا روابط | P3 | وصول | العميل / signup | S | `apps/client/src/app/[locale]/(auth)/signup/page.tsx` |
| VCL-077 — اسم الشركة بجانب الشعار يظهر عند 430 ويختفي عند 390 — رأسان مختلفان بين iPhone وChromium | P3 | بصري | العميل / signup / signin (iPhone 430) | S | `apps/client/src/components/client-header.tsx` |
| VCL-068 — «جزء من Fiper Global» يظهر مرتين: تحت الشعار وفي التذييل | P3 | بصري | العميل / signup / signin / reset | S | `apps/client/src/app/[locale]/(auth)/layout.tsx` |
| A11Y-025 — نصوص شروط التسجيل وتعليمات فقدان المصادِق بحجم 12 px تحت أرضية النص 15 px | P3 | وصول | العميل / signup, signup/password, 2fa | S | `apps/client/src/app/[locale]/(auth)/signup/page.tsx`، `apps/client/src/app/[locale]/(auth)/2fa/page.tsx` |
| VCL-040 — زر التصدير مفعّل رغم «0 صفقة» | P3 | بصري | العميل / trades / account-trades | S | `apps/client/src/components/trades-panel.tsx` |
| VCL-065 — الشارة ممدودة على كامل عرض البطاقة كزر | P3 | بصري | العميل / verify | S | `apps/client/src/app/[locale]/(public)/verify/[code]/page.tsx` |
| A11Y-018 — مجموعة خانات الرمز بلا اسم: label for لا يسمّي div role=group، و aria-invalid على group غير مدعوم | P3 | وصول | العميل / verify, 2fa | S | `packages/ui/src/components/otp-input.tsx`، `packages/ui/src/components/field.tsx` |
| TXT-054 — انتقال الفاعل بين العنوان (أنت) والنص (الحساب)، وكلمتا تهنئة مختلفتان | P3 | نص | العميل / الإشعارات | S | `packages/i18n/src/messages/ar.json` |
| TEC-004 — تحليل رسالة Safari iOS «Refused to apply a stylesheet… style-src»: ليست سمات `style=` ولا نقص دعم `style-src-attr`؛ المصدر عنصر `<style>` بلا nonce غير مرئي في المصدر، ويلزم جامع تقارير | P3 | تقني | العميل / الصفحات العامة على iPhone حقيقي (landing/signin/signup/reset/help/not-found) | S | `packages/core/src/csp.ts`، `apps/client/src/middleware.ts`، `scripts/real-device-coverage.mjs` |
| TXT-073 — «معايير المخاطرة» ≠ «حدود الخسارة»، وأرقام بالحروف خلافاً لقاعدة الأرقام اللاتينية | P3 | نص | العميل / الصفحة العامة | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| TEC-016 — صور الذيل (شعار cTrader) تُحمَّل مسبقاً قبل محتوى الطيّة الأولى وتنافس LCP، ولا `loading="lazy"` على أي صورة تحت الطيّة | P3 | أداء | العميل / الهبوط / 404 / كل صفحة فيها PartnerRow | S | `packages/ui/src/components/partner-row.tsx`، `packages/ui/src/components/logo.tsx`، `packages/ui/src/components/endorsement-line.tsx` |
| TEC-028 — البيان ثابت بالعربية (`start_url` و`id` و`lang`/`dir`) ويقفل الاتجاه عمودياً: تطبيق المستخدم الإنجليزي يُفتح بالعربية والأجهزة اللوحية لا تدور | P3 | RTL | العميل / تثبيت PWA | S | `apps/client/public/manifest.webmanifest`، `apps/client/src/app/[locale]/layout.tsx` |
| TXT-085 — «موثَّق» (documented) مقابل «تم التحقق» (verified) للمعنى نفسه | P3 | نص | العميل / توثيق الهوية — صيغ «موثَّق» | S | `packages/i18n/src/messages/ar.json` |
| TEC-046 — تقارير «client-home» و«client-account» و«client-payouts» قاست صفحة تسجيل الدخول (تحويل بلا جلسة) لا الشاشات المسمّاة | P3 | تقني | العميل / قياس الأداء — Lighthouse (artifacts/ux-audit-F4/lighthouse) | S | `scripts/ux-audit-lighthouse.mjs`، `artifacts/ux-audit-F4/lighthouse/` |
| TEC-024 — تسمية التحميل «يرجى الانتظار» بلا نقاط الحذف خلافاً لنمط الإرشادات والإدارة | P3 | تقني | العميل / كل أزرار الإرسال | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| A11Y-020 — عدّاد الإشعارات في تبويب «المزيد» يُنطق رقماً مجرّداً بلا سياق | P3 | وصول | العميل / كل الشاشات المسجَّل دخولها (شريط التبويب) | S | `packages/ui/src/components/bottom-nav.tsx`، `apps/client/src/components/bottom-nav.tsx` |
| TEC-015 — صور العلامة تحت `/brand/` تُرسل بـ `max-age=0, must-revalidate` بما فيها بلاطة 45 KB تُحمَّل مسبقاً في كل صفحة | P3 | أداء | العميل / كل الصفحات | S | `apps/client/next.config.ts`، `apps/admin/next.config.ts` |
| TEC-047 — قرار معلن: `style-src-attr 'unsafe-inline'` يسمح بأي سمة `style=` محقونة لو وُجد حقن HTML (تغطية/إخفاء عناصر، تسريب عبر CSS) — المتجهات الحالية محصورة ومُتحقَّق منها | P3 | تقني | العميل / كل المستندات | M | `packages/core/src/csp.ts` |
| TXT-081 — علامة الحذف «…» موجودة في بعض مؤشرات التقدم وغائبة في أخرى | P3 | نص | العميل / مؤشرات التقدم | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| TXT-067 — receive بكلمتين في الخطوات المتتالية | P3 | نص | العميل / مسار طلب الدفعة | S | `packages/i18n/src/messages/ar.json` |
| MAIL-036 — كلمة «e-mail» تنكسر عند الواصلة على الهاتف (e-/mail) | P3 | بصري | البريد / account_ready | S | `packages/i18n/src/messages/en.json` |
| MAIL-034 — النسخة النصية تكرر الفعل: «افتح: افتح لوحة التحكم» / «Open: Open the dashboard» | P3 | نص | البريد / all | S | `packages/adapters/src/mail/templates/layout.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| MAIL-041 — النسخة النصية تحمل الصندوق التنظيمي المؤقت ورأساً نصياً مكرراً | P3 | نص | البريد / all | S | `packages/adapters/src/mail/templates/layout.ts` |
| MAIL-043 — الوضع الداكن مُتحقق منه بمحاكاة Chromium فقط؛ Gmail يقلب الألوان بلا media query | P3 | هوية | البريد / all | M | `packages/adapters/src/mail/templates/layout.ts` |
| MAIL-044 — تسمية «مرجع الحساب» في الصف الأول تلتف على سطرين على الهاتف الإنجليزي | P3 | نص | البريد / breach | S | `packages/adapters/src/mail/templates/layout.ts` |
| MAIL-039 — موضوع إنجليزي بصيغة مبنية للمجهول وغير محدد: «A change of your FPT e-mail was requested» | P3 | نص | البريد / email_change_notice | S | `packages/i18n/src/messages/en.json`، `packages/i18n/src/messages/ar.json` |
| MAIL-045 — «مؤخراً» غامضة؛ لا تذكر عدد أيام عدم النشاط الذي تحسبه القاعدة | P3 | نص | البريد / inactivity_warning | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json`، `packages/adapters/src/mail/templates/index.ts` |
| MAIL-042 — موضوعات السحب تحمل المبلغ بلا مرجع الحساب، وموضوعات التحويل اليدوي تحمل المرجع بلا مبلغ | P3 | نص | البريد / payout_approved | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| MAIL-035 — صيغة المبلغ الإنجليزية «1,250.00 USD» تخالف صيغة الدليل للإنجليزية «USD 1,250.00» | P3 | هوية | البريد / payout_requested | S | `packages/adapters/src/mail/templates/format.ts`، `brand/README.md` |
| MAIL-038 — قيمة «تُنجَز المراجعة قبل» تلتف بترك «(GMT+4)» وحيداً في سطر | P3 | بصري | البريد / payout_requested | S | `packages/adapters/src/mail/templates/layout.ts`، `packages/adapters/src/mail/templates/format.ts` |
| MAIL-037 — لون رمز التحقق غير محدد صراحة (يعتمد على لون المتصفح الافتراضي) | P3 | تقني | البريد / verify_code | S | `packages/adapters/src/mail/templates/layout.ts` |
| MAIL-040 — اسم عربي داخل موضوع وجملة إنجليزية («Welcome to FPT, خالد» / «خالد, your FPT account is ready») | P3 | نص | البريد / welcome | S | `packages/adapters/src/mail/templates/index.ts`، `packages/i18n/src/messages/en.json` |
| TXT-058 — «يوم واحد متبقية» و«يومان متبقية» (تأنيث مع مذكّر) و«0 يوماً» | P3 | نص | البريد / الأيام المتبقية | S | `packages/adapters/src/mail/templates/format.ts`، `packages/i18n/src/messages/ar.json` |
| TXT-065 — تعبير غير مألوف | P3 | نص | البريد / بريد تنفيذ الدفعة | S | `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| TXT-064 — ثلاث رسائل تخبر ولا تقود إلى الشاشة المعنية | P3 | نص | البريد / رسائل بلا خطوة تالية | S | `packages/adapters/src/mail/templates/index.ts`، `packages/i18n/src/messages/ar.json`، `packages/i18n/src/messages/en.json` |
| TXT-057 — «تشغيل FPT» لا تعني operations، و«راسلنا على» حرف جر غير مناسب | P3 | نص | البريد / سطور البريد المشتركة | S | `packages/i18n/src/messages/ar.json` |
| A11Y-040 — قوائم Select الإدارية بلا dir في RTL: محاذاة علامة الاختيار والسهم تتبع LTR | P3 | وصول | مكوّنات @fpt/ui / admin — Select (Radix) في RTL | S | `packages/ui/src/components/select.tsx` |
| A11Y-015 — أيقونة اتجاه الفرز النشط لا تُبرَز: محدّد CSS يستهدف الزر بينما aria-sort على th | P3 | وصول | مكوّنات @fpt/ui / admin-accounts وكل DataTableRows | S | `packages/ui/src/components.css` |
| VIS-055 — عنوان منطقة الخطر بمعرّف ثابت — تكرار المعرّف إن ظهرت منطقتان في صفحة | P3 | وصول | مكوّنات @fpt/ui / DangerZone | S | `packages/ui/src/components/danger-zone.tsx` |
| A11Y-030 — نص جانبي داخل عنصر العنوان يلتصق بالاسم: «الحسابات الأخرى1»، «يحتاج إجراءً3»، «هويتكموثَّق» | P3 | وصول | مكوّنات @fpt/ui / home, account, admin-overview (عناوين الأقسام ذات العدّاد/الشارة) | S | `packages/ui/src/components/list-row.tsx`، `apps/client/src/app/[locale]/(app)/profile/page.tsx` |
| VIS-083 — القيم الفيزيائية الوحيدة في CSS التطبيق (text-align:left) — مقصودة لكتل الشيفرة | P3 | RTL | مكوّنات @fpt/ui / JsonViewer · EventFeed details | S | `packages/ui/src/components.css` |
| A11Y-034 — زر إغلاق التنبيه 36 px تحت أرضية اللمس 44 px | P3 | وصول | مكوّنات @fpt/ui / Toast | S | `packages/ui/src/components.css` |
| VIS-056 — كومة التنبيهات تسمح بأربع رسائل مرئية (الحد الموصى به 3) والطارئة تبقى بلا حد زمني | P3 | بصري | مكوّنات @fpt/ui / ToastProvider | S | `packages/ui/src/components/toast.tsx` |
| VIS-050 — تسميات الأزرار لا تلتف وتُقص بقصاصة على 360px — قد يختفي المبلغ من زر الدفع | P3 | بصري | مكوّنات @fpt/ui / أزرار block الطويلة (ادفع {amount}، تشغيل على FPT-…، اطلب دفعة) | S | `packages/ui/src/components.css`، `packages/ui/src/components/button.tsx` |
| A11Y-024 — حدّ الزر الشبحي بتباين 1.19:1 — حدود الزر غير مدركة، الاعتماد على النص/الأيقونة فقط | P3 | وصول | مكوّنات @fpt/ui / الأزرار الشبحية (ghost) في الرأس والصفوف | S | `brand/tokens.css` |
| A11Y-029 — التنبيه المنبثق الذي يحمل زر إجراء يختفي بعد 5 ثوانٍ دون وسيلة تمديد | P3 | وصول | مكوّنات @fpt/ui / الإشعارات المنبثقة (toast) ذات الزر | S | `packages/ui/src/components/toast.tsx` |
| A11Y-037 — لافتة «الحساب مخترق» الثابتة تحمل role=alert فتُعلن قسراً عند كل تحميل للصفحة | P3 | وصول | مكوّنات @fpt/ui / الحوارات والأوراق | S | `packages/ui/src/components/alert.tsx` |
| VIS-054 — عنوان الشاشة في الرأس يُقص بقصاصة صامتة على الهواتف الضيقة | P3 | بصري | مكوّنات @fpt/ui / الرأس على 360px | S | `packages/ui/src/components/app-shell.tsx` |
| VIS-068 — لمسيات ناقصة: وميض iOS الرمادي الافتراضي على الصفوف، لا overscroll-behavior في الأوراق، والنص قابل للتحديد بالضغط المطوّل على الصفوف | P3 | بصري | مكوّنات @fpt/ui / الصفوف والأوراق على iOS/Android | S | `packages/ui/src/components.css`، `packages/ui/src/components/sheet.tsx`، `packages/ui/src/components/dialog.tsx` |
| A11Y-022 — لا دعم لوضع الألوان القسرية/التباين العالي: حالات الرقائق والمقاييس والمفتاح تعتمد على خلفيات تُزال | P3 | وصول | مكوّنات @fpt/ui / الكل | S | `packages/ui/src/components.css` |
| VIS-039 — CardContent يفرض 14px افتراضياً على محتوى البطاقة | P3 | بصري | مكوّنات @fpt/ui / بطاقات الإدارة (settings, orders/[id], fraud, integrations) | S | `packages/ui/src/components/card.tsx` |
| VIS-048 — روابط الجدول <a> عادية (بلا Link/prefetch، إعادة تحميل كاملة) وزر «المزيد» يتجاوز مكوّن Button | P3 | تقني | مكوّنات @fpt/ui / كل DataTableRows (الإدارة) | S | `packages/ui/src/components/data-table.tsx` |
| VIS-038 — كلمة الحالة في الشارة الصغيرة 12px — تحت أرضية التسمية 13px | P3 | بصري | مكوّنات @fpt/ui / كل StatusBadge size=sm (صفوف القوائم في التطبيقين) | S | `packages/ui/src/components.css` |
| A11Y-023 — حلقة التركيز بلون الزر الأساسي نفسه — تُرى فقط بفضل فجوة 2 px | P3 | وصول | مكوّنات @fpt/ui / كل الأزرار الأساسية الحمراء | S | `brand/components/components.css`، `packages/ui/src/components.css` |
| A11Y-021 — aria-label على span عام (role=generic) — تسمية محظورة في ARIA 1.2 وتتجاهلها القارئات | P3 | وصول | مكوّنات @fpt/ui / كل الأماكن التي تستخدم Money quietCode (12 استخداماً) | S | `packages/ui/src/components/money.tsx` |
| A11Y-028 — كل خطأ حقل منطقة alert مستقلة: عند إرسال نموذج فيه عدة أخطاء تُعلَن كلها دفعة واحدة ومتداخلة | P3 | وصول | مكوّنات @fpt/ui / كل النماذج متعددة الحقول (الإدارة: المنتجات، المسارات، الإعدادات) | S | `packages/ui/src/components/field.tsx`، `apps/admin/src/components/ActionForm.tsx` |
| VIS-069 — التركيز بلوحة المفاتيح قد يختفي خلف الرأس اللاصق (لا scroll-padding/scroll-margin) | P3 | وصول | مكوّنات @fpt/ui / كل صفحة ذات رأس لاصق (56px) | S | `packages/ui/src/components.css` |

الملفات الأكثر تأثراً في F4b: `packages/i18n/src/messages/ar.json` (110)، `packages/i18n/src/messages/en.json` (51)، `packages/ui/src/components.css` (26)، `packages/adapters/src/mail/templates/layout.ts` (18)، `apps/client/src/app/[locale]/(app)/payouts/new/payout-form.tsx` (14)، `packages/adapters/src/mail/templates/index.ts` (10)، `apps/admin/src/app/[locale]/(app)/settings/page.tsx` (9)، `apps/client/src/app/[locale]/(auth)/auth-form.tsx` (9)، `apps/client/src/components/account-overview.tsx` (8)، `apps/admin/src/app/[locale]/(app)/accounts/page.tsx` (7)، `apps/admin/src/components/shell/AdminShell.tsx` (7)، `apps/client/src/app/[locale]/(app)/payouts/page.tsx` (7)، `packages/ui/src/components/data-table.tsx` (6)، `apps/admin/src/app/[locale]/(app)/orders/page.tsx` (5)، `apps/admin/src/app/not-found.tsx` (5).

تقدير الجهد: S = حتى ساعة، M = حتى يوم، L = أكثر من يوم. الترتيب داخل كل مرحلة بالخطورة ثم التطبيق ثم الشاشة. لا يُنفَّذ شيء قبل اعتماد المالك.
