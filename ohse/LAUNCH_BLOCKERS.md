# OHSE Y2K Store — Launch Blockers & Follow-ups

Status as of this QA pass. Everything below is either a real blocker (needs
Berk's real business info) or a scoped-out item (explicitly not attempted
this pass, listed so it isn't silently forgotten). Nothing here was
fabricated — placeholders were removed rather than filled with invented data.

## 🔴 Real blockers — need Berk's input before public launch

1. **Gizlilik Politikası — işletme adresi eksik.**
   `gizlilik-politikasi.html`, "9. İletişim / Veri Sorumlusu" section. The
   `[İşletme Adresi]` placeholder line was **removed** (showing a literal
   bracket placeholder to customers is worse than omitting it), not
   fabricated. KVKK identification of the "veri sorumlusu" customarily
   expects a real postal address alongside the company name/email already
   listed. Needs a real address from Berk, or an explicit decision to
   launch with email-only contact.

2. **İade adresi still not a real physical address.**
   `iade-degisim.html` and `iade-iptal.html` no longer show the
   `[İade Adresi]` placeholder — both now say *"İade onayı aldıktan sonra
   kargo bilgileri size iletilecektir"* (the exact approved fallback
   language). This is honest and non-blocking for launch, but if Berk wants
   a real return address printed directly on the page instead of "we'll
   send it after approval," that's a content decision for him.

3. **Route/canonical decision — not made in this pass.**
   `store-v2-y2k.html` is **not** renamed to `store.html`, and the
   production Netlify/GitHub Pages routing has **not** been changed, per
   the explicit "onay verilmeden production route veya domain değiştirme"
   instruction. What *was* done: every subpage's "Mağaza"/"Ana Sayfa"/logo
   link across about/support/iade-degisim/iade-iptal/iletisim/
   teslimat-kargo/gizlilik/kosullar/mesafeli-satis/cerez now points at
   `store-v2-y2k.html` instead of the old `store.html`. `store.html` itself
   is untouched. Making the Y2K version the real `/store` route (rename or
   redirect) still needs an explicit go-ahead.

## 🟡 Explicitly out of scope this pass — real follow-up work, not done

4. **Full Y2K "system shell" on the 9 info/legal pages.**
   The brief asked for the shared OHSE header/taskbar/Start Menu/footer to
   be applied to about/support/kargo/iade/iletisim/gizlilik/kosullar/
   mesafeli-satis/cerez. This is a genuinely large second build (9 pages,
   shared chrome, i18n, focus management) and doing it hastily alongside
   everything else in this pass risked shipping it inconsistent/buggy.
   **Not attempted.** Recommend as its own follow-up pass once the Y2K
   store itself is approved. Only the Store/Home link targets on those
   pages were updated (item 3 above).

5. **AVIF image format.** No `avifenc`/ImageMagick/cwebp binary was
   available in this environment — only Python/Pillow. WebP variants
   (480/768/native width, ~60-98% smaller than the source PNG/JPG) were
   generated and wired into `srcset` for every product photo, the logo,
   and the sweatpants teaser. AVIF was not generated. WebP alone already
   gets the bulk of the size win; AVIF would be a smaller additional gain.

6. **`<picture>`/`type="image/webp"` fallback.** `srcset` was added
   directly on `<img>` tags with the original PNG/JPG kept as `src` (so
   very old browsers with `srcset` support but no WebP support could in
   theory try to load a `.webp` file and fail). Given WebP support is
   ~97%+ across current browsers, this was accepted as a reasonable
   tradeoff rather than restructuring every image into `<picture><source>`
   blocks. Flagging so it's a conscious choice, not an oversight.

7. **Real Lighthouse run.** No local `lighthouse` binary and no time
   budget in this pass to let `npx lighthouse` cold-install and run
   against a local server. Performance/Accessibility/Best-Practices/SEO
   scores were **not measured with the actual tool** — instead this pass
   verified the underlying practices Lighthouse checks for (image
   `width`/`height`-implied `aspect-ratio`, `loading="lazy"`/
   `fetchpriority`, WebP, no horizontal overflow, 44px touch targets, real
   `aria-label`s, `alt` text, color contrast, `prefers-reduced-motion`)
   directly. Numeric scores are not claimed because they weren't measured.

8. **Security headers (`_headers` file) — drafted, not deployed.**
   `ohse/_headers` was created with CSP/`X-Content-Type-Options`/
   `Referrer-Policy`/`Permissions-Policy`/`X-Frame-Options`, scoped to what
   this site actually needs (Google Fonts, Shopify `products.json` fetch +
   checkout redirect). It has **not** been deployed/tested live — per the
   "no production deploy without approval" instruction, and because a bad
   CSP silently breaking Shopify checkout is exactly the kind of thing
   that needs a real staging test, not a guess.

9. **Analytics — no real container ID.** `pushDL()` pushes
   `view_item` / `select_pack` / `select_size` / `add_to_cart` /
   `begin_checkout` / `language_change` / `coming_soon_open` events into
   `window.dataLayer`. No Google Tag Manager container ID (or any other
   analytics ID) was added or invented — the array just accumulates
   harmlessly until a real GTM snippet is added elsewhere.

## ✅ Confirmed NOT an issue (checked, not just assumed)

- Product-modal "horizontal overflow" showed as a nonzero `scrollWidth -
  clientWidth` (~7px) in automated measurement, but this is the
  intentional horizontal photo-swipe gallery's off-screen slides being
  counted — `overflow-x:hidden` on `.modal-in` means **no visible
  scrollbar or visible overflow ever appears** (confirmed on real
  screenshots at all 4 breakpoints). Not a real bug; noting it so it isn't
  re-"fixed" into something worse later.
