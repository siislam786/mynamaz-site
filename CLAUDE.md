# mynamaz-site

## What this is
Static site for mynamaz.co.uk — the public site for My Namaz, plus My Amaan's privacy and support pages.
Also serves `app-version.json`, which drives the in-app "Update available" prompt.

## Build and deploy
- Cloudflare Pages, auto-builds and deploys on every push to `main` (~15 seconds). Preview at `mynamaz-site.pages.dev`.
- No build step — the repo root is served as-is. `_headers` is read in the same format Netlify used.
- Cloudflare provides hosting and DNS only; the domains stay at their existing registrar.
- Not an app repo: gradlew and Codemagic do not apply. Never run `gradlew`; never trigger Codemagic.

## Standing rules
- UK English in all output, code comments, commit messages and user-facing copy.
- Commit and push without asking, **except** credentials, version bumps, git history rewrites, and deletions Si did not explicitly request — ask first for those.
- Commit messages explain the *why* — they get read later via `git blame`.
- Never commit keystores, `.p8`, `.p12`, provisioning profiles or any secret.
- Never decide a product, brand or domain name autonomously — present options and wait.
- **`app-version.json` is a release-order control, not a content file.** Raise `android.min_version_code` only *after* Si has rolled the release out in Play Console — never before, or users get prompted to update to something that does not exist. Leave the `ios` block alone unless doing a deliberate iOS release. The authority on this is `RELEASING.md` in the `my-namaz-ios` repo.
- Policy and support copy must match what the app actually does. Verify against `Projects/my-namaz-ios` before editing, and say which file you checked.

## Settled — do not re-raise
- Hosting is Cloudflare Pages. (`my-namaz-ios`'s own notes still describe this repo as Netlify — that is stale; this repo's README is the authority.)
- Internal links keep their `.html` extensions. Pages 308-redirects `/contact.html` → `/contact`, so both forms work via one extra hop. **Do not "tidy" them into extensionless links** — they work as written.
- Do not re-raise anything in this section unless new evidence contradicts it. State the evidence if you do.

## Known traps
- Unmatched paths fall back to `index.html` rather than returning a 404, so a mistyped URL renders the homepage. Worth knowing when testing — a page can appear to exist when it does not.
- This site serves `/myamaan-privacy` and `/myamaan-support`, which overlap `si-tec.uk/my-amaan/*` in the `si-tec-site` repo. Which the live store listings point at is unconfirmed. **Report the duplication, do not migrate it** *(inferred — confirm)*.
