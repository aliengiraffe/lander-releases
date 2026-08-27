# lander-releases

Public release artifacts for the Alien Giraffe macOS tooling:

- **lander** — the CLI agent daemon (`/usr/local/bin/lander` + a launchd agent).
- **LanderBar** — the menu bar app (`/Applications/LanderBar.app`), which embeds
  that same lander CLI. Installing LanderBar does not install or manage the
  lander agent, and vice versa.

This repository contains **no source code**. It exists only to serve the signed
macOS installer packages from unauthenticated URLs, so MDM tools (Jamf Pro,
Kandji, Intune) can fetch them. Releases here are published automatically by CI
and mirror the corresponding internal release tag.

## Downloads

Every release carries:

| Asset | Purpose |
|---|---|
| `lander_<version>.pkg` | the versioned lander CLI agent installer |
| `lander.pkg` | byte-identical copy of the above, for a version-independent URL |
| `LanderBar_<version>.pkg` | the versioned LanderBar menu bar app installer |
| `LanderBar.pkg` | byte-identical copy of the above, for a version-independent URL |
| `mirror-checksums.txt` | SHA-256 of all four |

Stable URLs (always the newest stable release) — use these in MDM:

```
https://github.com/aliengiraffe/lander-releases/releases/latest/download/lander.pkg
https://github.com/aliengiraffe/lander-releases/releases/latest/download/LanderBar.pkg
```

Pinned to an exact version:

```
https://github.com/aliengiraffe/lander-releases/releases/download/v0.1.5/lander_0.1.5.pkg
https://github.com/aliengiraffe/lander-releases/releases/download/v0.1.5/LanderBar_0.1.5.pkg
```

## Verifying a download

Both packages are distribution-format installers, signed with our Apple
**Developer ID Installer** certificate and notarized + stapled by Apple:

```bash
pkgutil --check-signature lander.pkg        # expect: signed by a Developer ID certificate
spctl -a -vvv -t install lander.pkg         # expect: accepted, source=Notarized Developer ID
pkgutil --check-signature LanderBar.pkg     # same two checks for the menu bar app installer
spctl -a -vvv -t install LanderBar.pkg
shasum -a 256 -c mirror-checksums.txt       # against the release's checksum file
```

## Installing

```bash
sudo installer -pkg lander.pkg -target /      # lander CLI agent
sudo installer -pkg LanderBar.pkg -target /   # LanderBar menu bar app
```

The **lander** package installs `/usr/local/bin/lander` plus a launchd agent at
`/Library/LaunchAgents/com.aliengiraffe.lander.plist`. It is generic — the
control-plane endpoint is supplied separately by a managed-preferences
configuration profile, not baked into the installer.

The **LanderBar** package installs `/Applications/LanderBar.app` with the lander
CLI embedded inside the app bundle. It does not add a launchd agent; launch the
app from /Applications (or let MDM do so).

## Support

Issues and pull requests here are not monitored. Contact the Alien Giraffe team
through your usual channel.

