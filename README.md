# Sprint bezpieczeństwa: landing

Static landing for the "Sprint bezpieczeństwa" cohort (11–22.01.2027, 2 weeks, 249 zł). Plain HTML and CSS,
self-hosted fonts, no JavaScript, served by nginx. Payment: the form posts to
`https://courses.devince.dev/api/courses/checkout` (slug `sprint-bezpieczenstwa`), which answers 303 to Stripe Checkout.

Product decisions and copy sources: `~/business-projects/roadmap/products/security-audit/`
(`sprint-v3-rekomendacja.md`, `benchmark-wyniki.md`), copy draft with verified sources: `roadmap/products/security-audit/landing/copy-2026-11-02.md` (private).

## Run locally

    docker build -t sprint-bezpieczenstwa:local .
    docker run --rm -p 127.0.0.1:8088:80 sprint-bezpieczenstwa:local

## Checks used before publishing

- design-verifier (`~/design-verifier`): `npm run scan -- http://127.0.0.1:8088/` → 0 findings (2026-11-02).
- slop-verifier (github.com/bartek-filipiuk/slop-verifier): `node src/cli.js http://localhost:8088/ --src ./site --preset strict` → 0 findings (2026-11-02).
- Headers: CSP without inline script or style, `form-action` limited to courses.devince.dev and checkout.stripe.com, HSTS,
  nosniff, `frame-ancestors 'none'`. Inline `style` attributes and `<style>` blocks would be blocked by the CSP: keep all
  styling in `site/css/site.css`.

## Before going live (owner: Bartek)

1. ~~Program on courses.devince.dev~~ done 2026-11-02: id 21, slug `sprint-bezpieczenstwa`, published, 249,00 zł,
   `checkoutConsentMode: terms-only` (checked on https://courses.devince.dev/sprint-bezpieczenstwa: price, "Kup dostęp",
   terms-only consent text). Follow-up in the devince.dev repo once the domain is set: add the sprint terms to
   `EXTRA_TERMS_BY_SLUG` in `src/app/courses-app/_lib/consentKey.ts`, so the program page's own checkout also links
   this landing's `regulamin.html` (today it links only the general devince.dev terms).
2. Benchmark table filled from security-audit.dev (2.11.2026). No employer name anywhere: Devince is a private business.
3. `site/regulamin.html` and `site/polityka-prywatnosci.html`: adapted from the stronaw5dni.pl documents for an async cohort;
   effective date 2.11.2026. Legal review still recommended.
4. Umami: create a website in stats.67projects.app and add
   `<script defer src="https://stats.67projects.app/script.js" data-website-id="..."></script>` to the `<head>`;
   the CSP already allows that host. The buttons carry `data-umami-event` names.
5. Domain and Coolify app (proposal: a devince.dev subdomain), then rerun both verifiers against the public URL.
