# Lammah — Marketing Landing Page Plan

> A consumer-facing landing page that represents the game and drives **App Store / Google Play** installs — in the same gold-on-dark, Arabic-first design language as the app. Built as a **separate lightweight project**, deployed alongside the game on the new domain.

---

## 0. Goal & audience

- **Primary goal:** convert visitors → app installs (App Store + Google Play), with a secondary "Play on web / Cast to TV" path.
- **Audience:** Arabic-speaking players (Egypt-first, pan-Arab), friends-and-family / قعدة gatherings. **Arabic-first, RTL**, with an English toggle.
- **Tone:** playful, premium, شعبي — matches the app's cinematic dark + gold brand.
- **Tech reality:** the app is **pre-launch**, so store links may start as "Coming soon" and become live badges at launch (build for both states).

---

## 1. Where it lives (ties to the domain migration)

| Surface | URL | Notes |
|---|---|---|
| **Landing page** | `<NEW_DOMAIN>` (apex / root) | this project |
| Game web app | `<NEW_DOMAIN>/play` or `play.<NEW_DOMAIN>` | existing Expo web build |
| TV companion | `<NEW_DOMAIN>/tv` | existing TV web (see `DOMAIN_MIGRATION_PLAN.md`) |

Keep the landing page a **separate repo/folder** (e.g. `lammah-landing`) so it deploys independently of the app and stays SEO-light and fast.

---

## 2. Tech stack

**Recommended: [Astro](https://astro.build).** Best fit for a marketing/landing page — ships near-zero JS by default (fast, great SEO/Core Web Vitals), first-class i18n, easy static deploy. Components + Markdown for content.

**Alternative: plain static HTML/CSS** — mirrors the existing dashboard exactly (zero build), good if you want maximum simplicity and brand parity with the dashboard's CSS.

**Avoid:** reusing the Expo web bundle (it's the *app*, heavy, not SEO-tuned) or a heavy SPA.

**Deploy:** GitHub Pages (same flow as the game/dashboard — `CNAME` + custom domain) **or** Vercel (better preview deploys + edge if you want it). Either works for static output.

---

## 3. Design language — reuse, don't reinvent

The brand already exists in CSS form (the dashboard's `assets/css/*.css`). Lift it. Source of truth: `src/shared/theme/tokens.ts`.

```css
:root {
  /* Backgrounds (cinematic dark) */
  --bg-screen:   #0F0617;  --bg-surface: #0A0D1F;  --bg-card: #1B1F2E;  --bg-category: #170C21;
  /* Gold (brand lead) */
  --gold-300: #F6D366;  --gold-400: #F5C84B;  --gold-500: #E9B838;  --gold-600: #D9A92E;
  /* Blue accent (Team B / secondary) */
  --blue-400: #5B8DEF;
  /* Text */
  --text-primary: #FFFFFF;  --text-secondary: #A8AEC1;  --text-muted: #7A8094;  --text-accent: #F5C84B;
  /* Radii */
  --r-lg: 16px;  --r-card: 24px;  --r-hero: 32px;  --r-pill: 9999px;
  /* Brand gradients + glow */
  --grad-cta-gold:       linear-gradient(135deg, #F6D366, #D9A92E);
  --grad-card-gold-soft: linear-gradient(135deg, rgba(245,200,75,.24), rgba(245,200,75,.08));
  --grad-hero-midnight:  linear-gradient(180deg, #05070F, #13162A);
  --glow-gold:           0 0 32px rgba(245,200,75,.40);
  /* Type */
  --font: 'IBM Plex Sans Arabic', 'Cairo', system-ui, sans-serif;
}
```

- **Font:** IBM Plex Sans Arabic (Google Fonts) — same as the app/dashboard. Inter optional for Latin numerals.
- **Logo:** reuse `assets/auth/logo-main.png` (the same asset the app + TV screens use). Export an SVG version if available for crispness.
- **RTL:** `dir="rtl"` by default; use CSS logical properties (`margin-inline`, `padding-inline`, `inset-inline`) so the English variant mirrors cleanly (same philosophy as the app's I18nManager approach).
- **Motion:** subtle — gold glow on CTA hover, gentle fade/parallax on scroll. Respect `prefers-reduced-motion`.

---

## 4. Page structure (sections)

A single long-scroll page, RTL-first, bilingual:

1. **Sticky header** — logo (left/inline-start) · nav (Features · How to play · FAQ) · language toggle (ع / EN) · small "Download" button.
2. **Hero** — over `--grad-hero-midnight`:
   - Logo + Arabic headline + one-line value prop (e.g. "لعبة المعرفة لقعداتك").
   - **Primary CTAs:** App Store + Google Play badges (see §5). Secondary: "العب على الويب" → `/play`.
   - Hero visual: phone mockup with a question screen + a TV-cast frame (shows the couch/قعدة + TV differentiator).
3. **Social proof strip** (optional, post-launch) — ratings, install count, "as seen in".
4. **Features** — gold-soft cards (`--bg-category` + `--grad-card-gold-soft`):
   - Two-team play · MCQ + presenter modes · TV cast / second screen · Online rooms with friends · Bilingual AR/EN · Progression/levels & badges.
5. **How to play / TV-cast highlight** — 3 steps + the "open `<NEW_DOMAIN>/tv` on your TV" flow (mirrors the in-app lobby copy).
6. **Screenshots / gallery** — carousel of app screens (home, question, results, TV board). Use real captures.
7. **Categories teaser** — a few category tiles to show breadth (Arabic-native content = the moat).
8. **FAQ** — accordion (is it free? Arabic support? how many players? offline? TV requirements?).
9. **Final CTA band** — repeat store badges + web-play link over a gold-soft gradient.
10. **Footer** — logo, links (Privacy, Terms, Support/contact, the dashboard is NOT linked publicly), social, language toggle, copyright.

---

## 5. Store download buttons (App Store + Google Play)

- **Use the official badge assets** — Apple's "Download on the App Store" and Google's "Get it on Google Play" from their respective brand/marketing toolkits. **Do not recreate or restyle them** (both have strict brand guidelines on size, clear space, and color); download the official artwork and follow each program's usage rules. Provide Arabic-locale badge variants where available.
- **Pre-launch state:** show a **"Coming soon"** treatment (disabled badge + "notify me" email capture) until the apps are live, then swap in live links.
- **Links:**
  - iOS → `https://apps.apple.com/app/idXXXXXXXXX` (App Store Connect once live).
  - Android → `https://play.google.com/store/apps/details?id=com.amrfakhri.lammah` (matches the app's `package`).
- **Smart routing (nice-to-have):** detect platform → highlight the matching badge; a single "Download" button can route iOS→App Store, Android→Play, desktop→show both / QR code to install on phone.
- **QR install:** a QR pointing to a smart link is great for the TV/desktop visitor → phone install.

---

## 6. i18n / RTL

- **Arabic default** (RTL), **English** toggle. Two content files (`ar`, `en`); reuse wording from the app's locale files (`src/localization/locales/{ar,en}/*`) for consistency (tagline, "how to connect" steps, feature names).
- Set `<html lang="ar" dir="rtl">` / `lang="en" dir="ltr"` per locale. Persist choice (localStorage) + honor `Accept-Language`.
- Numerals/dates: keep brand-consistent with the app.

---

## 7. SEO, meta & sharing

- Unique `<title>` + meta description per locale; `hreflang` for ar/en.
- **Open Graph + Twitter cards** — branded share image (logo on `--bg-screen` with gold accent) so links preview well on WhatsApp/Twitter/Facebook (key for MENA virality).
- `sitemap.xml`, `robots.txt`, canonical URLs.
- **Structured data:** `SoftwareApplication` / `VideoGame` JSON-LD (name, ratings, store links) for rich results.
- Favicon/app-touch icons reuse the app icon set.

---

## 8. Performance & quality bars

- Static output, lazy-load images, modern formats (WebP/AVIF), preconnect to Google Fonts, self-host fonts if possible.
- Lighthouse targets: Performance ≥ 90, Accessibility ≥ 95, SEO 100.
- `prefers-reduced-motion` respected; keyboard-navigable; sufficient contrast (gold-on-dark passes; check muted text).

---

## 9. Analytics & consent

- Lightweight, privacy-respecting analytics (e.g. Plausible/Umami, or GA4 if needed) to measure install-button clicks (the conversion event) and traffic by locale/region.
- Cookie/consent banner if using cookie-based analytics (esp. for any future EU traffic). Track **store-badge clicks** as the primary KPI.

---

## 10. Asset checklist (gather before build)

- [ ] Logo (PNG already exists; **SVG** preferred for crispness).
- [ ] App icon set / favicons.
- [ ] 4–6 real **screenshots** (home, question, results, TV board, lobby) + device-frame mockups.
- [ ] Official **App Store** + **Google Play** badges (AR + EN).
- [ ] OG/share image.
- [ ] Final AR + EN copy (lift tagline + steps from the app locale files).
- [ ] Privacy Policy + Terms pages (store submission requires these anyway).
- [ ] Store URLs (once apps exist) + `package`/bundle id already known (`com.amrfakhri.lammah`).

---

## 11. Build phases

**Phase 1 — Skeleton + brand:** scaffold (Astro or static), wire the CSS token bridge (§3), fonts, logo, RTL, header + hero with placeholder "Coming soon" store buttons. Deploy to a preview/subdomain.
**Phase 2 — Content:** features, how-to/TV, screenshots, FAQ, footer; AR + EN copy; OG/meta/SEO; analytics.
**Phase 3 — Launch wiring:** swap "Coming soon" → live store badges + smart routing + QR; structured data; final Lighthouse pass; point `<NEW_DOMAIN>` apex at it (per `DOMAIN_MIGRATION_PLAN.md`).
**Phase 4 — Post-launch:** social proof (ratings/installs), seasonal banners (Ramadan), A/B test hero CTA copy.

---

## 12. Decisions needed

1. **Stack:** Astro (recommended) vs plain static HTML/CSS (max dashboard parity)?
2. **Host:** GitHub Pages (consistent with game/dashboard) vs Vercel (better previews)?
3. **URL layout:** landing on apex with game at `/play`, or game on a `play.` subdomain?
4. **Pre-launch CTA:** "Coming soon + notify me" email capture, or hide store buttons until live?
5. **Repo:** new `lammah-landing` repo, or a folder in an existing one?

Once chosen, Phase 1 (scaffold + brand bridge + hero) is a quick build, and the design tokens above guarantee it matches the app exactly.
```
