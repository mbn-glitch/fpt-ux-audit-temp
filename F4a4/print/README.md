# F4a-4 — print receipts (UXFPT-201 P0: the payout statement on ONE A4 sheet)

Agent D prepared the fix and the measurement; the coordinator runs the spec on the built stack and fills the **after** columns.
The PDFs come from `e2e/print.spec.ts` (Playwright attachments `payout-statement-<locale>.pdf`, `order-receipt-<locale>.pdf`,
`certificates-<locale>.pdf`); copy them into `after/` beside this file.

## Method

- Chromium prints the signed-in document screen itself: `page.emulateMedia({ media: 'print' })`, fonts settled, then
  `page.pdf({ format: 'A4', printBackground: true, preferCSSPageSize: true })` (`e2e/print-tools.ts`, `PDF_OPTIONS`). The CSS page is
  `@page { size: A4; margin: 17mm }` — the 17 mm margin is the brand rule (margin = 2 × the 8 mm lockup) and is unchanged. Inside the
  margins an A4 sheet offers 210 − 34 = 176 mm × 297 − 34 = 263 mm ≈ 665 × 994 CSS px.
- Page count: Ghostscript renders every page to PPM at 5 dpi and counts the files (`pdfPageCount`); without `gs` the `/Type /Page`
  objects of the PDF are counted.
- Page-1 text: `gs -sDEVICE=txtwrite` (`pdfPageText`). Latin text extracts verbatim («Payout statement · A86552C7»); Arabic comes out
  as presentation-form glyphs in reversed visual order with the font's ligatures («ﻻ», «ﱪ») expanded in logical order, so the spec's
  `hasPhrase` matches each word of the expected phrase as a letter window of its own length (order-free inside the word, NFKC, no
  tashkeel) and requires all the words inside one short span. Proven on the a064f74 PDFs: «كشف السحب», «فايبر غلوبال ذ.م.م» and
  «المشغّل لبرنامج فايبر برو تريدرز» are found on the old page 2, «حصة المتداول» on page 1; absent phrases stay absent.
- Page-1 mean luminance: `gs -sDEVICE=ppmraw -r20`, Rec. 709 weights over the whole sheet (`pdfPageLuminance`); paper ≈ 244–250,
  the dark app ground ≈ 20. Gate: > 180.
- Viewport: phone-390 in the spec. Printing does not depend on the viewport — the design office measured 390 and 1280 and got the
  same page counts and the same characters per page (`~/Projects/design-office/fpt-platform/recapture/a064f74/print/pdf-summary.json`).

## Before (design office recapture a064f74, 31 Oct 2026 03:04 UTC, local stack)

| document | locale | pages | page 1 chars | page 2 chars | page-1 mean luminance | note |
| --- | --- | ---: | ---: | ---: | ---: | --- |
| payout statement | ar | **2** | 603 | 235 | 250 (p. 2: 247) | page 2 = the foot only («كشف السحب · A86552C7» + the entity line) |
| payout statement | en | **2** | 611 | 263 | 250 (p. 2: 247) | page 2 = the foot only ("Payout statement · A86552C7" + the entity line) |
| order receipt | ar | 1 | 611 | — | 250 | |
| order receipt | en | 1 | 619 | — | 249 | |
| certificates (1 certificate) | ar | 1 | 795 | — | 249 | |
| certificates (1 certificate) | en | 1 | 881 | — | 248 | |

Root cause (measured, `payout-statement__ar__w390.dom.json`): the article is 1065 px tall in the print medium (ar and en alike)
against ≈ 994 px of printable height — ≈ 71 px too tall before the fixed risk line joined the foot (FPT-BRAND-ALIGN, d3990a3, after
this capture), which adds one more 12 px line. The Arabic body line-height of the screen (`.fpt-doc[lang='ar']`) also survived into
print because it outranks the plain `.fpt-doc { line-height: 1.6 }` of the F4a-3 print block.

## The fix (apps/client/src/app/documents.css `@media print`, the statement page)

1. `.fpt-doc .brandline, .fpt-doc .doc-foot { break-before: avoid; break-inside: avoid }` and `.fpt-doc .fpt-group, .fpt-doc .brandline { break-after: avoid }` — the foot never opens a sheet.
2. The paper rhythm (the values the coordinator can tune if a round is still over): line-height 1.45 for BOTH scripts; h1 24 px; the
   subtitle 2 px above; the meta line 6 px; `.figures` / `.fpt-group` / h2 8 px; the hero figure 28 px on 8 px; `table.fin` th/td
   6 px vertical (the 12 px inter-column gap and the start-edge alignment kept); `.fpt-kv` 4 px 0; the endorsement line 8 px above;
   the foot 6 px above and 6 px inside, its three spans (reference · risk line · entity line) on one wrapped flex line at 12 px.
3. `.fpt-doc { max-block-size: none; min-block-size: 0 }` in print.
4. The statement page lost two duplicates: the date-range paragraph under «دورة السحب» (the subtitle carries the same range) and the
   «الحالة» facts row (the total row of the table already carries the status pill).
   Estimated saving ≈ 160–200 px (ar) → ≈ 880–920 px, i.e. one sheet with ≈ 80 px of air; en similar.

The certificate and the receipt share the print rules and only become lighter (`h1.cert-title` keeps 2 rem); they are measured too.

## After (coordinator — round 1 of the spec on the F4a-4 phase-1 build, 31 Oct 2026 ≈ 09:50 Dubai; `after/pdf-summary.json`)

| document | locale | pages | page 1 chars | page-1 mean luminance | dark share | reference line on p. 1 | risk line on p. 1 |
| --- | --- | ---: | ---: | ---: | ---: | --- | --- |
| payout statement | ar | **1** | 686 | 243.6 | 2.6 % | yes (spec assertion) | yes (spec assertion) |
| payout statement | en | **1** | 731 | 242.2 | 2.7 % | yes | yes |
| order receipt | ar | 1 | 535 | 245.4 | 1.5 % | — | yes |
| order receipt | en | 1 | 554 | 244.6 | 1.6 % | — | yes |
| certificates (1 certificate) | ar | 1 | 687 | 245.1 | 2.1 % | — | yes |
| certificates (1 certificate) | en | (sign-in limiter in round 1 — re-run on the fresh stack, see round 2) | | | | | |

Round 1 of `e2e/print.spec.ts` (phone-390): 5 passed, the English certificates test did not reach the page (the local sign-in rate limiter
after the gates' sign-ins — environmental); it is re-run on the rebooted stack in round 2. The statement foot (reference line + the
fixed risk line + the entity line) is on page 1 in both languages — page 2 no longer exists. No margin, lockup or font-size below 12 px
was touched.
