# mynamaz-site

Static site for [mynamaz.co.uk](https://mynamaz.co.uk), auto-deployed by Netlify from `main`.

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
*keep allowing*, commit, and push to `main`. Netlify deploys automatically.
The app fails open on any fetch error, timeout, or malformed JSON — it never
blocks users who are offline or if this file is briefly unreachable.

### app-version-test.json — temporary testing file, not used in production

`app-version-test.json` sits alongside `app-version.json` with impossibly
high minimums (`999` / `"99.0.0"`), so any real installed build always
triggers the forced-update screen. It exists only so a debug build of the
app can point its version-check URL here instead of the live file, to
verify the forced-update overlay actually appears and behaves correctly
before relying on the real kill switch.

**Normal operation only ever edits `app-version.json`.** This test file
plays no part in the live app (the shipped build always points at
`app-version.json`) and should be deleted from this repo once testing is
complete — it's a scratch fixture, not a second kill switch.
