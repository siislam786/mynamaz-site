# mynamaz-site

Static site for [mynamaz.co.uk](https://mynamaz.co.uk).

## Hosting — Cloudflare Pages

Hosted on **Cloudflare Pages**, which auto-builds and deploys on every push to
`main` (typically ~15 seconds). Preview/build URL:
<https://mynamaz-site.pages.dev>.

There is no build step — the repo root is served as-is. `_headers` is read by
Pages in the same format Netlify used.

Pages prefers extensionless URLs: a request for `/contact.html` gets a **308
redirect** to `/contact`, which then serves 200. Both forms therefore work,
and the internal `.html` links in the HTML resolve fine — just via one extra
redirect hop. Don't "tidy" them into extensionless links; they work as
written.

Unmatched paths fall back to `index.html` rather than returning a 404, so a
mistyped URL renders the homepage. Worth knowing when testing.

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

Three addresses, three different jobs. Only one of them ever goes on a page.

**1. `contact@mynamaz.co.uk` — the public-facing address.** The *only* address
that may appear anywhere on this site: visible text, `mailto:` links, page
copy and meta descriptions alike. It is a Cloudflare Email Routing address
that forwards to the delivery inbox below.

**2. `siislam7861@gmail.com` — the internal delivery destination.** Where
Cloudflare Email Routing forwards `contact@`, and where Web3Forms sends form
submissions (configured against the access key on the Web3Forms side, not in
this repo). It is plumbing, not a published contact route — **don't put it on
a page.** It used to be the public address; it was swapped out in July 2026
when `contact@` went live.

**3. Si's private personal address** — the `siislam786…` local part, i.e. the
same as the delivery address but *without* the trailing `1`. Never appears
anywhere public, and is deliberately not written out in this repo at all.

Note this repo is public on GitHub *and* the host serves every file in the
root (including this README), so "public-facing" means anything committed
here, not just the HTML pages.

### Auditing

```
grep -rn "contact@mynamaz.co.uk" --include="*.html" .   # expect: contact, privacy-policy, submit-mosque
grep -rn "siislam" --include="*.html" .                 # expect: no hits at all
```

Any `siislam…` address appearing in a page is a bug — the second grep above
catches all of them in one go, which is why it's written that way rather than
spelling any address out.

To search specifically for the private address, grep its local part followed
immediately by `@`. That trailing `@` is what distinguishes it from the
delivery address, whose local part continues with a `1` instead. (Not written
out here, for the reason given above.)

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
