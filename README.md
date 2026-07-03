# chrismaury apps site

Static multi-app site (no build step) hosting landing pages, privacy policies, and
terms for Chris Maury's macOS apps, used for Google OAuth app verification.

Served at **`apps.chrismaury.com`**. Repo name is `keymail-site` for historical
reasons; it now hosts multiple apps.

## Structure

```
/index.html            → apps.chrismaury.com/          (hub listing all apps)
/keymail/index.html    → apps.chrismaury.com/keymail/
/keymail/privacy.html
/keymail/terms.html
/outbox/index.html     → apps.chrismaury.com/outbox/
/outbox/privacy.html
/outbox/terms.html
```

Each app folder has: a landing page (functionality + per-scope Google data-use
disclosure + Limited Use statement), a privacy policy, and terms. All internal
links are relative, so the pages work at any host/subpath.

## Verification URLs (one OAuth project per app)

### Keymail
- Homepage: `https://apps.chrismaury.com/keymail/`
- Privacy:  `https://apps.chrismaury.com/keymail/privacy.html`
- Terms:    `https://apps.chrismaury.com/keymail/terms.html`
- Scopes: `gmail.modify` (restricted), `gmail.send` (restricted), `userinfo.email`,
  `calendar.readonly` (sensitive), `calendar.events` (sensitive)

### Outbox
- Homepage: `https://apps.chrismaury.com/outbox/`
- Privacy:  `https://apps.chrismaury.com/outbox/privacy.html`
- Terms:    `https://apps.chrismaury.com/outbox/terms.html`
- Scopes: `gmail.readonly` (restricted), `userinfo.email`, `userinfo.profile`
- Note: Outbox's calendar is Apple EventKit (local), **not** the Google Calendar
  API — do not add Google Calendar scopes. The privacy page also discloses that
  email/transcript content is sent to Anthropic's Claude API using the user's own
  key, and that app data optionally syncs via the user's private iCloud (CloudKit).

Contact for both: `help@chrismaury.com`

## Deploy (Cloudflare Pages)

1. **Workers & Pages → Create → Pages → Connect to Git** → pick this repo.
   Build command: *(blank)*. Output directory: *(blank / root)*.
2. After first deploy → **Custom domains → Set up a custom domain** → `apps.chrismaury.com`.
   Cloudflare creates the DNS record and TLS cert automatically (the zone is already on Cloudflare).
3. Confirm both trailing-slash and non-slash forms of each app path serve the page
   (`/keymail` and `/keymail/`), since reviewers hit whatever URL is in the consent screen.
   Cloudflare Pages handles this by default.
4. Future updates: edit and `git push` to `main`; Pages redeploys automatically.

## Google verification checklist (per app)

1. **Verify the domain in [Search Console](https://search.google.com/search-console)**
   under the same Google account that owns the Cloud project. Best: a **Domain property**
   for `chrismaury.com` via a DNS TXT record — this covers `apps.chrismaury.com` and every
   other subdomain automatically. (Google treats each subdomain as its own origin.)
2. In each app's **OAuth consent screen** config, set:
   - App home page / Privacy policy / Terms → the app's three URLs above (all on the same verified domain)
   - **Authorized domain** → `chrismaury.com` (Google uses the eTLD+1; covers all subdomains)
   - **App name** → exactly "Keymail" / "Outbox" (must match the site branding)
   - **User support email** + **developer contact email** → `help@chrismaury.com` (must match the site)
3. Restricted Gmail scopes (`gmail.modify`, `gmail.send`, `gmail.readonly`) require a
   **demo video** (unlisted YouTube is fine) showing the OAuth consent flow with the
   production client ID and each requested scope's functionality in the app.
4. Restricted-scope apps with >100 users also require an annual **CASA security assessment**;
   under the 100-user cap you can remain in testing or request the personal-use exemption.

Every scope requested on the consent screen must be demonstrated in the video and
described on the site — if a feature/scope isn't shipping yet, drop it from both.
