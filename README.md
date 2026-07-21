# mynamaz-site

Static site for [mynamaz.co.uk](https://mynamaz.co.uk).

## Hosting — Cloudflare Pages

Hosted on **Cloudflare Pages**, which auto-builds and deploys on every push to
`main` (typically ~15 seconds). Preview/build URL:
<https://mynamaz-site.pages.dev>.

There is no build step — the repo root is served as-is. `_headers` is read by
Pages in the same format Netlify used.

Pages serves extensionless URLs alongside the real filenames, so both
`/contact` and `/contact.html` resolve. Internal links in the HTML use the
`.html` form and work as written; don't "tidy" them into extensionless links.

Cloudflare provides hosting and DNS only — the domains stay at their existing
registrar.

> Migrated off Netlify in July 2026 (free tier exhausted, deploys paused).
> Nothing Netlify-specific remains: forms moved to Web3Forms, and there is no
> `netlify.toml`.

## Forms — Web3Forms

Both forms (`contact.html` and `submit-mosque.html`) POST to
`https://api.web3forms.com/submit` with a hidden `access_key`, a `subject`
identifying which form it came from, a `redirect` back to
`https://mynamaz.co.uk/thanks.html`, and a hidden `botcheck` honeypot field.

The delivery inbox is configured against the access key on the Web3Forms side,
not in this repo.

## Email policy — read before adding any address to a page

**The only address that may appear anywhere on this site is
`siislam7861@gmail.com`** — note the trailing `1`. That applies to visible
text, `mailto:` links, page copy and meta descriptions alike.

Si's private address is the same local part **without** the trailing `1`. It
must never appear in public-facing content. Note that this repo is public on
GitHub *and* the host serves every file in the root (including this README) —
so "public-facing" means anything committed here, not just the HTML pages.
That's why the private address is not written out anywhere in this repo,
including in this policy.

The privacy policy page has always used the `…7861` address. That was the
policy's first instance, not a typo — don't "correct" it.

The one legitimate use of the private address is the **form-delivery
destination**, configured against the Web3Forms access key rather than in this
repo. That's private delivery, not public display, so it correctly stays as
the non-`1` address — nothing here needs to change for it.

To audit, grep for the private local part followed immediately by `@`. The
`@` matters: it makes the search match *only* the private address and never
the public one, so any hit in a committed file is a bug.

## app-version.json — forced-update kill switch

The My Namaz app fetches `https://mynamaz.co.uk/app-version.json` on boot (and
on resume, at most once per 6 hours) and compares the installed version
against the minimums below. If the installed version is below the minimum
for its platform, the app shows a non-dismissable "Update needed" screen
until the user updates.

```json
{
  "android": { "min_version_code": 0, "latest_version_name": "1.5.6" },
  "ios": { "min_version_name": "0.0.0", "latest_version_name": "1.2" }
}
```

- `android.min_version_code` — an Android `versionCode` (integer). Any
  installed build with a lower `versionCode` gets the forced-update screen.
- `ios.min_version_name` — an iOS marketing version (e.g. `"1.2"`), compared
  segment-by-segment. Any installed version lower than this gets the
  forced-update screen.
- `latest_version_name` (both platforms) — informational only, not currently
  used by the app's comparison logic.

**`0` / `"0.0.0"` means the feature is dormant** — no real app version is
ever below that, so no one sees the forced-update screen. This is the
default/safe state.

**To force an update:** edit this file, raise the relevant `min_version_code`
(Android) or `min_version_name` (iOS) to the lowest version you want to
*keep allowing*, commit, and push to `main`. Cloudflare Pages deploys
automatically.
The app fails open on any fetch error, timeout, or malformed JSON — it never
blocks users who are offline or if this file is briefly unreachable.
