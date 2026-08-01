
# lander-releases

Public release artifacts for **lander**, the Alien Giraffe macOS agent.

This repository contains **no source code**. It exists only to serve the signed
macOS installer package from an unauthenticated URL, so MDM tools (Jamf Pro,
Kandji, Intune) can fetch it. Releases here are published automatically by CI and
mirror the corresponding internal release tag.

## Downloads

Every release carries:

| Asset | Purpose |
|---|---|
| `lander_<version>.pkg` | the versioned installer |
| `lander.pkg` | byte-identical copy of the above, for a version-independent URL |
| `mirror-checksums.txt` | SHA-256 of both |

Stable URL (always the newest stable release) — use this one in MDM:

```
https://github.com/aliengiraffe/lander-releases/releases/latest/download/lander.pkg
```

Pinned to an exact version:

```
https://github.com/aliengiraffe/lander-releases/releases/download/v0.1.5/lander_0.1.5.pkg
```

## Verifying a download

The package is a distribution-format installer, signed with our Apple **Developer
ID Installer** certificate and notarized + stapled by Apple:

```bash
pkgutil --check-signature lander.pkg     # expect: signed by a Developer ID certificate
spctl -a -vvv -t install lander.pkg      # expect: accepted, source=Notarized Developer ID
shasum -a 256 -c mirror-checksums.txt    # against the release's checksum file
```

## Installing

```bash
sudo installer -pkg lander.pkg -target /
```

The package installs `/usr/local/bin/lander` plus a launchd agent at
`/Library/LaunchAgents/com.aliengiraffe.lander.plist`. It is generic — the
control-plane endpoint is supplied separately by a managed-preferences
configuration profile, not baked into the installer.

## Support

Issues and pull requests here are not monitored. Contact the Alien Giraffe team
through your usual channel.

