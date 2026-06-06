# Clear Horizon — build transparency log

This is an **append-only, public log of build hashes** for the Clear Horizon digital-legacy
app served at <https://legacy.clearhorizon.io>. It contains **hashes only — no source code**.

Each deploy publishes `manifests/<git-commit>.json`: the commit the build came from plus a
SHA-384 (SRI format) for every served JavaScript/CSS bundle. `latest.json` points at the
most recent build.

## Why this exists

The app does its encryption **in your browser**, so confidentiality rests on the integrity of
the code we serve. The app's `/verify` page re-hashes the bundles your browser loaded and
compares them to the manifest. But if you only check a manifest *we* serve, a fully malicious
origin could serve a lying manifest and a lying checker together. This repo is the out-of-band
reference: it lives on a **different provider** than the app's serving infrastructure, and its
git history is append-only — so the published hashes are something a hostile origin can't
silently rewrite. `/verify` cross-checks the served manifest against the copy here.

## Verify a build by hand

1. Note the commit shown on <https://legacy.clearhorizon.io/verify>.
2. Open `manifests/<that-commit>.json` here and compare its hashes to the app's
   `/build-manifest.json`. They must be identical.
3. Hash any file under the app's `/assets` (`openssl dgst -sha384 -binary file | openssl base64`)
   and confirm it matches the entry in the manifest.

This is detection + transparency, not a guarantee against a hostile server — see the app's
`/verify` page for the honest scope.
