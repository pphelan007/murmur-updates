# murmur-updates

Public companion repo for **[Murmur](https://github.com/pphelan007/murmur)** — local push-to-talk voice dictation for macOS.

This repo hosts two things:

1. **`appcast.xml`** — the [Sparkle](https://sparkle-project.org/) update feed that running copies of Murmur poll once a day to check for new versions. Served at <https://pphelan007.github.io/murmur-updates/appcast.xml> via GitHub Pages.
2. **DMG releases** — the actual installer downloads, attached to each [GitHub Release](https://github.com/pphelan007/murmur-updates/releases) in this repo, so end users get auth-free public download URLs.

The application source lives in the **private** [`pphelan007/murmur`](https://github.com/pphelan007/murmur) repo. Only the distribution artifacts and update feed live here.

## How updates work

```
Murmur (running on user's Mac)
  └─ daily HTTPS GET → https://pphelan007.github.io/murmur-updates/appcast.xml
        ↓ Sparkle parses the highest <item> ≥ current version
        ↓ Downloads the .dmg from its <enclosure url>
        ↓ Verifies sparkle:edSignature against SUPublicEDKey in Info.plist
        ↓ Replaces /Applications/Murmur.app in place and relaunches
```

The Ed25519 signature is the only piece that gates installation. The DMG host (this repo's Releases) is interchangeable; the signing key (in the maintainer's login Keychain on the build machine) is the trust root.

## Privacy

The appcast request is anonymous — `SUSendProfileInfo = false` in [Info.plist](https://github.com/pphelan007/murmur/blob/main/build/Murmur.app/Contents/Info.plist), so the request carries only the user-agent string. The maintainer of this repo cannot see who is checking for updates beyond raw GitHub Pages access logs.

## Releasing

Done from the private repo, not here. See [murmur/RELEASE.md](https://github.com/pphelan007/murmur/blob/main/RELEASE.md) — the release script prepends a new `<item>` to this `appcast.xml` and uploads the signed DMG to this repo's Releases.
