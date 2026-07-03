# Keymail landing site

Static site (no build step) for Google OAuth app verification of Keymail.

- `index.html` — landing page: app functionality, per-scope Google data-use disclosure, Limited Use statement, links to privacy/terms, contact.
- `privacy.html` — privacy policy covering access, use, storage, sharing, retention/deletion, security, and the Limited Use disclosure required for restricted Gmail scopes.
- `terms.html` — terms of service.

Scopes disclosed (must match the OAuth consent screen in Google Cloud Console):

- `gmail.modify` (restricted)
- `gmail.send` (restricted)
- `userinfo.email`
- `calendar.readonly` (sensitive)
- `calendar.events` (sensitive)

## Deploying / verification checklist

Target URLs:

- Homepage: `https://apps.chrismaury.com/keymail/`
- Privacy: `https://apps.chrismaury.com/keymail/privacy.html`
- Terms: `https://apps.chrismaury.com/keymail/terms.html`
- Contact: `help@chrismaury.com`

Steps:

1. **Verify `apps.chrismaury.com` in [Search Console](https://search.google.com/search-console)** under the same Google account that owns the Cloud project. A Domain-property verification of `chrismaury.com` via DNS TXT record covers all subdomains automatically (including `apps.chrismaury.com`) and is the recommended option — otherwise verify the `apps.chrismaury.com` subdomain as its own URL-prefix property. Google treats each subdomain as its own origin, so this verification is required even if `chrismaury.com` is already verified as a URL-prefix property.
2. **Point `apps.chrismaury.com` at your static host** (Cloudflare Pages, Netlify, GitHub Pages with custom domain, S3+CloudFront, etc.) and deploy the three files under `/keymail/`. Relative links (`privacy.html`, `terms.html`) resolve within that directory. Make sure both the trailing-slash (`/keymail/`) and non-slash (`/keymail`) forms serve `index.html` — Google's crawler and reviewers will hit whichever URL was pasted into the consent screen. HTTPS with a valid cert is required.
3. In the OAuth consent screen config, set:
   - **App home page** → `https://apps.chrismaury.com/keymail/`
   - **Privacy policy** → `https://apps.chrismaury.com/keymail/privacy.html` (must be on the same verified domain as the homepage)
   - **Terms of service** → `https://apps.chrismaury.com/keymail/terms.html`
   - **Authorized domain** → `chrismaury.com` (Google Cloud Console uses the eTLD+1; this covers all subdomains including `apps.chrismaury.com`)
   - **App name** → exactly "Keymail" (must match branding on the site)
   - **User support email** and **developer contact email** → `help@chrismaury.com` (must match the contact on the site)
4. Restricted Gmail scopes additionally require a **demo video** (YouTube, unlisted is fine) showing the OAuth consent flow with the production client ID and each scope's functionality in the app.
5. Apps with restricted scopes and >100 users also require an annual **CASA security assessment**; under the 100-user cap you can stay in "unverified"/testing or request the exemption for personal use.

If the calendar scopes aren't shipping yet, remove them from both the consent screen and the site before submitting — every requested scope must be demonstrated in the video.
