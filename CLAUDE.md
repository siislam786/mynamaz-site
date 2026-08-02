# mynamaz-site

## What this is
Static site for mynamaz.co.uk — the public site for My Namaz, plus My Amaan's privacy and support pages.
Also serves `app-version.json`, which drives the in-app "Update available" prompt.

## Build and deploy
- **Cloudflare Pages, NOT Netlify.** Auto-builds and deploys on every push to `main` (~15 seconds). Preview at `mynamaz-site.pages.dev`. **Disregard any Netlify reference found anywhere** — the site was migrated and the references are historical.
- No build step — the repo root is served as-is. `_headers` is read in the same format Netlify used, which is why those references linger.
- Cloudflare provides hosting and DNS only; the domains stay at their existing registrar.
- `contact@mynamaz.co.uk` routes via Cloudflare Email Routing.
- Not an app repo: gradlew and Codemagic do not apply. Never run `gradlew`; never trigger Codemagic.

## Standing rules
- **Pushing to `main` IS deploying — this repo auto-deploys to Cloudflare Pages. Never push without explicit instruction, even for content-only changes.**
- UK English in all output, code comments, commit messages and user-facing copy.
- Commit freely; **do not push** without being told to. Also ask first for credentials, git history rewrites, and deletions Si did not explicitly request.
- Commit messages explain the *why* — they get read later via `git blame`.
- Never commit keystores, `.p8`, `.p12`, provisioning profiles or any secret.
- Never decide a product, brand or domain name autonomously — present options and wait.
- **`app-version.json` is a release-order control, not a content file.** Raise `android.min_version_code` only *after* Si has rolled the release out in Play Console — never before, or users get prompted to update to something that does not exist. Leave the `ios` block alone unless doing a deliberate iOS release. The authority is `RELEASING.md` in the `my-namaz-ios` repo.
- Policy and support copy must match what the app actually does. Verify against `Projects/my-namaz-ios` before editing, and say which file you checked.

## Settled — do not re-raise
- Hosting is Cloudflare Pages.
- Internal links keep their `.html` extensions. Pages 308-redirects `/contact.html` → `/contact`, so both forms work via one extra hop. **Do not "tidy" them into extensionless links** — they work as written.
- Do not re-raise anything in this section unless new evidence contradicts it. State the evidence if you do.

## Known traps
- **Cloudflare Scrape Shield email obfuscation rewrites even plain-text email addresses for non-JS clients.** This is a zone-level setting and is **not fixable in the HTML** — do not try to "fix" a mangled address by editing markup.
- Unmatched paths fall back to `index.html` rather than returning a 404, so a mistyped URL renders the homepage. A page can appear to exist when it does not.
- This site serves `/myamaan-privacy` and `/myamaan-support`, overlapping `si-tec.uk/my-amaan/*`. Verified 2 August 2026: the live Play listing for My Namaz links to `https://mynamaz.co.uk/privacy-policy`, and no public Play or App Store listing exists for My Amaan, so nothing live depends on either My Amaan URL yet. **Report the duplication, do not migrate it.**
