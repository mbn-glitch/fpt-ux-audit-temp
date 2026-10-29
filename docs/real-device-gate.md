# بوابة الأجهزة الحقيقية — docs/real-device-gate.md (F1)

بوابة إلزامية بعد كل نشر: المسار الذهبي لمنطقة العميل على آيفون حقيقي (سفاري) ثم أندرويد حقيقي (كروم) عبر BrowserStack Automate، على الرابط الحي. النمط منقول حرفياً من `fiper-funds` (`docs/real-device-gate.md` هناك): الخطوات المشتركة في `apps/client/scripts/golden-steps.mjs`، المشغّلان `golden-phone-ios.mjs` و`golden-phone-android.mjs`، وغلاف WebDriver `webdriver-page.mjs`. التطبيق داكن فقط؛ لا خطوة مظهر.

## الخطوات (كلها بلمس `tap()`؛ على كل شاشة: لا تجاوز أفقي للوثيقة ولا عنصر `[data-testid]` مقصوص + لقطة)
1. الدخول بحساب العرض `demo01` — العربية افتراضياً (`html[lang=ar][dir=rtl]`)؛ على سفاري الحقيقي قد تحتاج لمسة الإرسال إلى إعادة واحدة (لوحة المفاتيح).
2. الرئيسية (`home`): لا تجاوز ولا قصّ، شريط التبويب ظاهر داخل النافذة، الرقم المهيمن `hero-equity` مرسوم.
3. الحساب (`tab-account` تحت الإصبع → `account`): منحنى الرصيد `equity-chart` مرسوم؛ تبويب البيانات (`account-tab-credentials` → `credentials-login` رقم الحساب و`credentials-ctrader-id` البريد وزر `credentials-open-web` تحت الإصبع — منذ F3 بلا كلمة مرور)؛ تبويب الصفقات (`account-tab-trades` → `trades`).
4. الدفعات (`tab-payouts` → `payouts`).
5. طلب سحب حتى المراجعة بلا إرسال: `payout-new` → المبلغ `payout-amount` (500) → `payout-next` → القناة (`payout-channel-crypto` يُختار بلمس صفّه) → `payout-next` → العنوان `payout-address` → `payout-next` → المراجعة `payout-step-review` وزر `payout-submit` تحت الإصبع (لا يُلمس). إن ظهرت بوابة التحقق من الهوية (`kyc-start`) تُسجَّل الخطوة معلومةً وتتوقف.
6. المزيد (`more`): الإنجليزية (`lang-en` → `lang=en dir=ltr` وتسمية التبويب Home) ثم العربية (`lang-ar`)؛ لغة الحساب تُعاد إلى العربية.
8. **(F3)** سجل المشتريات `/orders` (`purchases`): يُرسم بلا تجاوز.
9. **(F3)** شراء بالتحويل اليدوي حتى شاشة الانتظار (مع `--proof e2e/fixtures/proof.png`): `buy-10k` → `pay-manual` يُختار بلمس صفّه → `accept-terms` → `pay-submit` → صفحة التحويل (`transfer`، المرجع `FPT-XXXXXXXX` في `transfer-reference`) → إرفاق الصورة في `proof-file` → `proof-submit` → شاشة الانتظار `transfer-waiting`. الطلب يبقى «بانتظار الاعتماد» على الحساب التجريبي حتى يُعتمد/يُرفض من الإدارة أو تُعاد تهيئة العرض. على أندرويد (WebDriver) لا توجد واجهة لإرفاق ملف فتُسجَّل الخطوة معلومةً.
7. الهوية على الرئيسية: البلاطة الرسمية `fpt-logo-icon-rounded` بارتفاع 32 ± 2 بلا انعكاس (`direction: ltr`)، شريط التبويب 4 عناصر، لا مبدّل مظهر، الأرضية `rgb(10, 10, 10)`، خط الجسم IBM Plex Sans Arabic ≥ 15px، ولا نص مرئي < 12px على الشاشة.

**أسماء اللقطات** `<screen>-<device>.png` (الجهاز مُسطَّح: `iphone-15-pro-max`، `samsung-galaxy-s23`): `login`, `home`, `account`, `credentials`, `trades`, `payouts`, `payout-amount`, `payout-review`, `more`, `more-en`, `more-ar`, ومنذ F3 `purchases`, `checkout-manual`, `transfer`, `transfer-waiting` (`GOLDEN_SHOTS`)؛ الشاشات الرئيسية الست للبوابة: `home`, `account`, `trades`, `payouts`, `payout-review`, `more` (`MAIN_SHOTS`).

## التشغيل (خطة Automate Mobile، توازٍ واحد → جهاز بعد جهاز؛ حد أقصى 3 تشغيلات لكل جهاز في الجولة)
- آيفون: Playwright على سفاري iOS عبر `wss://cdp.browserstack.com/playwright`. BrowserStack يقبل عميل Playwright **1.63**: `--pw` يشير إلى `node_modules` مشروع markets على الخادم. قيود iOS: `keyboard.type` قد لا يُدخل نصاً (السكربت يعود إلى `fill` ويسجّل ذلك).
- أندرويد: Playwright على أندرويد الحقيقي غير متاح في الخطة، فالسكربت يتكلم **W3C WebDriver** مباشرة (`webdriver-page.mjs`): اللمس بمؤشر `touch` حقيقي، الكتابة عبر `sendKeys`، لقطات عبر `/screenshot`؛ لوحة المفاتيح تُغلق (blur) قبل لمس الزر السفلي؛ لا `reload` — تنقّل كامل بدله.
- المفاتيح بالاسم فقط عبر `~/bin/with-secret`؛ السكربتات تنظّف أي خطأ من قيم المفاتيح وكلمة مرور حساب العرض. ملف حسابات العرض `~/.config/fpt-platform/demo-accounts.json` (0600).
```
cd ~/Projects/fpt-platform
~/bin/with-secret BROWSERSTACK_USERNAME=BROWSERSTACK_USERNAME BROWSERSTACK_ACCESS_KEY=BROWSERSTACK_ACCESS_KEY -- \
  env FPT_ACCOUNTS_FILE=$HOME/.config/fpt-platform/demo-accounts.json \
  node apps/client/scripts/golden-phone-ios.mjs --base https://fptraders.app \
  --out artifacts/real-device/ios --device "iPhone 15 Pro Max" --os 17 \
  --pw ~/Projects/personal-agent-markets/apps/markets/node_modules \
  --proof e2e/fixtures/proof.png

~/bin/with-secret BROWSERSTACK_USERNAME=BROWSERSTACK_USERNAME BROWSERSTACK_ACCESS_KEY=BROWSERSTACK_ACCESS_KEY -- \
  env FPT_ACCOUNTS_FILE=$HOME/.config/fpt-platform/demo-accounts.json \
  node apps/client/scripts/golden-phone-android.mjs --base https://fptraders.app \
  --out artifacts/real-device/android --device "Samsung Galaxy S23" --os 13.0
```
البديل لأندرويد: `--device "Google Pixel 8" --os 14.0`. آيفون أحدث: `--device "iPhone 16" --os 18`.

## المخرجات
في `--out`: `golden-<ios|android>-receipt.json` (الجهاز والنظام، نسخة العميل، كل خطوة بـ pass/fail/null وبياناتها — بيانات الجلسة العامة فقط) واللقطات. رمز الخروج 0 فقط عندما لا تفشل خطوة؛ تُوسم الجلسة في BrowserStack بـ passed/failed. المسار الذهبي لا يُنشئ طلب سحب ولا يدفع؛ يبدّل لغة الحساب ثم يعيدها.

## عند فشل حقيقي على الجهاز
أعد الإنتاج محلياً (Playwright WebKit/Chromium بعرض الجهاز على المكدس المحلي `e2e/boot.ts`)، أصلح بأقل تغيير مع اختبار حارس (`e2e/ux-gates.spec.ts` أو `e2e/journey.spec.ts`)، انشر، ثم أعد التشغيل على الجهاز نفسه.

## التغطية الكاملة (RD1، 30 أكتوبر 2026) — كل شاشة على أجهزة ومتصفحات حقيقية
المسار الذهبي أعلاه يفحص رحلة واحدة؛ **التغطية الكاملة** تمشي على **كل شاشة في `e2e/screens.ts`** (العميل ≈ 40 شاشة، الإدارة ≈ 35) على الإنتاج (`https://fptraders.app` و`https://admin.fptraders.app`) بالعربية، ومساراً مختصراً بالإنجليزية للشاشات الرئيسية (`SEGMENTS['main-en']`)، وتُجري على كل شاشة **القياسات نفسها التي تُجريها البوابات المحلية** (`MEASURE_UX` من `e2e/ux-audit.ts` محقونةً في الصفحة الحقيقية: نص < 12، جسم < 15، أحادي < 15، أهداف لمس < 44، تجاوز أفقي، عناصر `[data-testid]` مقصوصة، تسرّب تقني، ترتيب رمز العملة في RTL) + أخطاء console والطلبات الفاشلة حيث يتيحها المشغّل (CDP عبر Playwright على iOS/iPadOS والمحركات المحلية؛ `/log` + سجل الشبكة لـ BrowserStack على كروم) + زمن `domcontentloaded`، ولقطة لكل شاشة.

- **الأجهزة** (`DEVICES` في `scripts/real-device-coverage.mjs`): العميل على `iphone-15-pro-max` (iOS 17، سفاري، Playwright 1.63 عبر `wss://cdp.browserstack.com/playwright`) و`samsung-galaxy-s23` (أندرويد 13، كروم، WebDriver — البديل `google-pixel-8`)؛ الإدارة على المتصفحات المكتبية الحقيقية `windows-11-chrome` (1440×900) و`macos-safari` (Sequoia، 1440×900) — خطة Automate Mobile قبلت جلسات سطح المكتب فعلاً في RD1 — و`ipad-pro-13` (iPadOS 17، سفاري، أفقي). المحركات المحلية `local-chromium-390` / `local-chromium-1440` / `local-webkit-1440` / `local-firefox-1440` تمشي المسار نفسه على الإنتاج من خادم البناء بلا دقائق BrowserStack (البديل الموثّق للإدارة، والمقارنة «بوابة محلية ↔ جهاز حقيقي»).
- **المشي مستقل عن قاعدة البيانات**: معرّفات شاشات التفاصيل (`e2e/walk-ids.ts`) تُجلب مرة واحدة من قاعدة الإنتاج على الخادم (`scripts/coverage-prod-ops.mjs ids --out .e2e/coverage-ids.json`) وتُمرَّر كملف؛ البوابات المحلية تجلبها من قاعدة الاختبار كما كانت (`idsFromDb`). الصفحة سطح صغير (`WalkPage`) يحققه Playwright وغلاف WebDriver معاً.
- **حساب الموظف**: `qa@fiperprotraders.com` («فحص آلي»، دور `owner`) أُنشئ بكلمة مرور عشوائية وسرّ TOTP مُسجَّل عبر صفحة التهيئة نفسها في التطبيق (`qa-create` ثم `qa-enrol`)، بياناته في `~/.config/fpt-platform/demo-accounts.json` تحت `staff[]` مع `qa: true`. يُفعَّل في بداية التشغيل ويُعطَّل (`status=disabled` + إبطال جلساته) في نهايته دائماً مع `--prod-ops`؛ كل دخول له في سجل التدقيق.
- **النظافة**: الشراء يتوقف قبل الدفع والسحب قبل الإرسال؛ في نهاية التشغيل تُلغى طلبات demo01 التي أنشأها هذا التشغيل فقط (منذ طابع البدء، حالة `cancelled`، سجل تدقيق بسبب `qa-run`)؛ الطلب الحيّ `FPT-CC7B3D73` وأي طلب له إثبات لا يُمسّان.
- **التوازي** (منذ 30 أكتوبر 2026 الخطة تسمح بـ 5 جلسات متوازية — تحقق من `plan.json` قبل التشغيل): جهاز لكل عملية `real-device-coverage.mjs` بالتوازي، مع إبقاء مقعد حرّ لمشاريع أخرى. **الإدارة**: حدّ محاولات الدخول لكل حساب موظف («محاولات كثيرة. انتظر دقيقة») يحجب الدخول المتوازي بحساب الفحص نفسه — ابدأ مسارات الإدارة بفاصل ≥ 90 ثانية (أو حساب فحص لكل مسار)، ولا تُشغّل `--prod-ops` إلا في عملية واحدة (فعّل `qa-enable` يدوياً قبل المسارات المتوازية وأنهِ بـ `qa-disable` + `cleanup --since` بعدها؛ `qa-unlock` يصفّر عدّادات القفل إن لزم). بعد انتهاء المسارات: `--summary-only` يعيد حساب الملخص (ملخص يُكتب أثناء مسار آخر قد يُظهر `NaN`).
- **الجلسات**: جهاز بعد جهاز عند توازٍ واحد؛ المشي مقسّم إلى أجزاء (`SEGMENTS`) كل منها جلسة BrowserStack ≤ 25 دقيقة؛ الجزء الذي ينقطع يُعاد لشاشاته الناقصة فقط (≤ 3 إعادات لكل جهاز)؛ `--resume` يكمل جهازاً من إيصاله.

```
cd ~/Projects/fpt-platform
set -a; . ~/.config/fpt-platform/engine.env; set +a                       # DATABASE_URL للمعرّفات وحساب الفحص والتنظيف
pnpm exec tsx scripts/coverage-prod-ops.mjs ids --out .e2e/coverage-ids.json
~/bin/with-secret BROWSERSTACK_USERNAME=BROWSERSTACK_USERNAME BROWSERSTACK_ACCESS_KEY=BROWSERSTACK_ACCESS_KEY -- \
  env FPT_ACCOUNTS_FILE=$HOME/.config/fpt-platform/demo-accounts.json \
  pnpm exec tsx scripts/real-device-coverage.mjs --date 20261030 --ids .e2e/coverage-ids.json --prod-ops \
    --devices iphone-15-pro-max,samsung-galaxy-s23,windows-11-chrome,macos-safari,ipad-pro-13 \
    --pw ~/Projects/personal-agent-markets/apps/markets/node_modules
node scripts/ux-board.mjs coverage --date 20261030 && (cd artifacts/ux-board && vercel --prod)
```
أو بعد النشر: `RUN_REAL_DEVICE_COVERAGE=1 deploy/check.sh` (المتغيرات الاختيارية `COVERAGE_DATE`، `COVERAGE_DEVICES`، `PW_163_DIR`).

**المدة والكلفة**: ≈ 2 دقيقة لكل جزء على المحركات المحلية و≈ 3–6 دقائق لكل جزء على جهاز حقيقي (4 أجزاء للعميل + 2 للإدارة + المسار الإنجليزي)؛ الدقائق الفعلية لكل جهاز في `coverage-receipt.json` (`minutes`) وفي `coverage-summary.json` (`totals.minutes`)، والخطة لا تعرض الرصيد المتبقي عبر `plan.json` (يُقرأ من لوحة BrowserStack).

**المخرجات**: `artifacts/real-device/coverage-<YYYYMMDD>/<device>/<app>-<screen>[-en].png` واللقطات، `coverage-receipt.json` لكل جهاز (بيانات الجلسة العامة فقط: `hashed_id` و`browser_url` المحمي بتسجيل الدخول، لا رموز ولا كلمات مرور) و`coverage-summary.json` للجولة. الحكم على الشاشة PASS عندما تكون كل العدّادات صفراً (`tiny, smallBody, smallMono, smallTargets, overflow, clipped, leaks(العميل), currencyOrder(ar), consoleErrors, failedRequests`)؛ الطلبات التي يلغيها المتصفح نفسه (`ERR_ABORTED` لجلب مسبق قطعه التنقل) و404 المقصودة على شاشة not-found لا تُحتسب؛ الشاشات التي يتخطاها المشي نفسه (تبويب غير موجود أو لا سجل على هذا البناء) تُسجَّل `notApplicable` ولا تدخل في المقام.

**قراءة اللوحة** (`artifacts/ux-board/coverage/`، تُنشر مع لوحة F1 على Vercel): جدول «نسبة التغطية» (شاشات مغطاة/الكل، النسبة، شاشات فيها خروقات، الجلسات، الدقائق لكل جهاز) ثم مصفوفة الجهاز × الشاشة: كل خلية مصغّر اللقطة (يفتح اللقطة الكاملة)، PASS/FAIL، زمن `domcontentloaded`، وعدد الخروقات حسب النوع؛ الفلاتر: العميل/الإدارة، الجهاز، الفاشل فقط. «لا ينطبق» = الشاشة غير قابلة للوصول على هذا البناء/البيانات؛ «غير مغطّاة» = انقطعت ولم تُلتقط بعد الإعادات.

**ضجيج الأدوات المستبعد من الحكم** (موثّق في `isConsoleNoise`/`isNoise`): ورقة أنماط لقطة Playwright التي يرفضها CSP على WebKit («Refused to apply a stylesheet»)، جلب Next المسبق الذي يقطعه التنقل («Failed to fetch RSC payload»، `ERR_ABORTED`/`NS_BINDING_ABORTED`)، 404 صفحة not-found نفسها، 401 استطلاع المستخدم السابق بعد خروج المشي، و«Load failed» لطلب قطعه التنقل على WebKit. **اكتشافات الجولة**: سفاري iOS الحقيقي يحتفظ بكوكيز جلسة سابقة لم تُغلق؛ `page.url()` يتأخر عن التنقل داخل الصفحة على جسر iOS؛ غلاف WebDriver يحتاج اعتبار `display: contents` مرئياً (هيكل الإدارة)؛ iOS يحوّل أرقام البطاقات إلى روابط `tel:`.

**عند فشل حقيقي**: كما في القسم السابق — إعادة الإنتاج محلياً، أقل تغيير مع اختبار حارس، نشر، ثم **الشاشات المتأثرة فقط على الجهاز نفسه**: `--devices <device> --segments <segment> --resume` بعد حذف مفاتيح تلك الشاشات من `coverage-receipt.json` (أو `node scripts/real-device-coverage.mjs --recheck client/not-found@ar --devices iphone-15-pro-max`).
