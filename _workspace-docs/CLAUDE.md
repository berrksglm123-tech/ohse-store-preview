# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this directory is

This is a **workspace folder, not a single repo** — `~/Desktop` holds several independent
projects plus loose deploy artifacts and screenshots. There is **no git at this level** and no
top-level build, but as of 16 Aug 2026 **each project subfolder is its own git repo**. Work
happens inside one project subfolder at a time. Identify which project the task is about
before doing anything; don't assume a shared toolchain.

**Two GitHub accounts are in play.** `pilates-app` lives on the older account (`sarpsaglam3`,
private) and still deploys to Vercel from there. The OHSE repos are being migrated to a
**second, newer GitHub account** as part of a move to another machine — check `git remote -v`
in a folder before assuming which account owns it.

**Secrets never go in the repos.** Every project's `.gitignore` excludes `.env*`; the live
Supabase `service_role` keys in `pilates-app/.env.local` and `ohse-muhasebe/.env.local`
are transferred out-of-band, never committed.

| Path | What it is | Has own docs? |
|------|-----------|---------------|
| `ohse-sites/` | **Git repo**. Holds the two static sites below — they moved here 16 Aug 2026 when the projects were put under version control | no — see below |
| `ohse-sites/ohse/` | OHSE streetwear store — static HTML/CSS/JS site (documented below) | no — see below |
| `ohse-sites/countdown/` | Standalone reusable black countdown page (ADrip font) — the prototype `ohse/index.html` grew from | no |
| `noizy/` | NOIZY — second streetwear brand, static store. **Its own git repo** (`noizy-site`), deliberately kept separate from `ohse-sites`. **This is the design source of truth**: `ohse-sites/ohse/store.html` was rebuilt as a 1:1 copy of `noizy/index.html`. Change one, update the other — note they now live in **different repos**, so that mirroring takes two commits | no |
| `pilates-app/` | "Korukent Pilates" (formerly "Annem Pilates") — Next.js 15 + Supabase PWA (Turkish) | **yes — `pilates-app/CLAUDE.md` + `spec.md` are authoritative; read those, don't infer** |
| `ohse-app/` | Expo/React Native OHSE app — **git repo** | no |
| `ohse-muhasebe/` | OHSE Muhasebe — Next.js ERP / CEO dashboard, **git repo** | no |
| `store.html` (root) | Older/working copy of `ohse-sites/ohse/store.html`; **differs** from the live one. The canonical store is `ohse-sites/ohse/store.html` | n/a |
| `*.zip` (root) | OHSE deploy bundles (`OHSE-store-son.zip`, `OHSE-site-DEPLOY.zip`, etc.) — snapshots uploaded to hosting; not source | n/a |
| `*.png` (root) | Screenshots / design refs | n/a |
| `10_11_Sinif_WATI.csv` | WhatsApp (WATI) contact list, unrelated to the stores | n/a |

## OHSE store (`ohse-sites/ohse/`)

Static site, **no build step and no framework** — plain HTML files, all CSS and JS inline in each
page. To preview, serve the folder: `python3 -m http.server` from `ohse-sites/ohse/`.

**Design (rebuilt 2 Aug 2026): "fotokopi fanzin", identical to NOIZY.** Light grey paper ground
(`--wall:#c6c4bd`, `--sheet:#e7e5de`), ink `#111114`, one navy spot `#1b2f6b`, 2px rules, stamp
tags, and a 3px-offset "misprint" echo behind the wordmark. Typography: **Big Shoulders Display**
(display) + **Courier Prime** (labels/data) + **Archivo** (body). `noizy/index.html` is the source
of truth — change one, mirror the other. Single theme by choice: no dark mode, so what the client
is shown is what ships.
- Brand marks (`logo-mark.png`, `logo.png`, `hero.png`) are **white chalk on black** — on paper they
  vanish. Never invert them (the chalk texture goes muddy); put them inside a black `.ink-block`
  instead. That is why the header and footer are solid ink bars.

**Entry points:**
- `index.html` — the drop countdown. `window.OHSE_LAUNCH` (hardcoded near the top) drives it; once
  that time passes the page `location.replace('store.html')`. Set to `2026-07-15T19:00:00+03:00`,
  i.e. **already past — index.html now redirects immediately.** Keeps the `ADrip` `@font-face`.
- `store.html` — the storefront, and the file almost all changes touch (~700 lines, everything inline).
- `link.html` plus the legal/info pages (`about`, `iletisim`, `iade-*`, `gizlilik-*`,
  `kullanim-kosullari`, `mesafeli-satis`, `teslimat-kargo`, `support`, `cerez-politikasi`) — these
  still use the **Tailwind CDN**, with the palette flipped via `tailwind.config` token values
  (`paper`/`white` = ink, `black` = paper). `store.html` no longer uses Tailwind at all.

**How the store actually works — the load-bearing detail:** the cart is fully client-side, but
**OHSE runs no checkout of its own**. Checkout, accounts and payments live on a **separate Shopify
store at `shop.ohse.store`**. The wiring:
- `PRODUCTS` — 7 cards, one per `pack_color`: single/2pack/3pack × black/white (+ `2pack_mixed`).
  Each card's `sku` **is** the `VARIANT_MAP` key. `out` lists sold-out sizes.
- `VARIANT_MAP` maps each `pack_color` → a `{XS,S,M,L,XL}` table of **real Shopify numeric variant
  IDs**. These run live payments — verify against Shopify before touching them.
- "Ödemeye Geç" builds a Shopify **cart permalink** `shop.ohse.store/cart/<vid>:<qty>,...`. Adding or
  removing products/sizes means **updating both `PRODUCTS` and `VARIANT_MAP`**. Checkout deliberately
  refuses to proceed if any cart line has no variant ID, so nobody is charged the wrong amount.
- As of 2 Aug 2026 the 3-pack is sold out in every size, both colours.

**Removed on 2 Aug 2026** (user's call, to match NOIZY exactly): product photography and the gallery,
the hero slogan artwork, the cassette music player, TR/EN switching, the size-guide modal, the
account/shipping-tracking drawer, and the packaging section. Product cards now carry no images — the
product name is set in giant outlined type as the poster. **The full previous version is in
`ohse-sites/ohse/_yedek-2026-08-02/`.** Note the legal pages still contain `data-i18n` attributes and a language
toggle in the header; the store no longer does.

### Deploying OHSE (this is a live site)
- `ohse.store` is hosted on **TürkTicaret cPanel** (LiteSpeed), web root `public_html`. Deploy by
  uploading files there via File Manager — single-file overwrite for small edits, or zip + Extract for
  a full deploy. The Desktop `OHSE-*.zip` files are these upload bundles.
- **`shop.ohse.store` is Shopify — never touch it.** It owns checkout/accounts; the static site only
  links into it.
- Server gotchas: keep `.well-known`, `.htaccess`, `cgi-bin` in `public_html`; image files must be
  world-readable (chmod 644) or LiteSpeed returns 403.
- There is a **Vercel project `ohse`** (`ohse-ten.vercel.app`) that is **not** wired to `ohse.store`.
  Use it as the preview the user reviews on their phone: `npx --yes vercel@latest deploy --prod --yes`
  from `ohse-sites/ohse/`. Deploying there never touches the live site — cPanel upload stays a manual, user-approved step.
- ~~Hard constraint: do not change the site's black (`#000`) background.~~ **Lifted 2 Aug 2026** —
  the user explicitly asked for the light paper design and to drop this rule. The ground is now
  `#c6c4bd`; do not reintroduce a black background without asking.

## pilates-app (`pilates-app/`)

Next.js 15 / React 19 / Supabase PWA. It has its **own `CLAUDE.md` and an authoritative `spec.md`** —
read those before working in it. Key commands (from that folder): `npm run dev`, `npm run build`
(also the typecheck gate), `npm run lint`. No test framework. **It is a git repo**
(`sarpsaglam3/pilates-app`, private) and pushing `main` auto-deploys to Vercel — see its own
`CLAUDE.md` before pushing.
