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
