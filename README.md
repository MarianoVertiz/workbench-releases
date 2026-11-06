# Workbench releases

This repository holds only Workbench's installers and its signed release manifests. It has no source code.
Workbench's source is private.

## What is here

- **Installers.** Each version is a GitHub release tagged `v<version>` whose one asset is
  `Workbench-Setup-<version>.exe`. Releases are immutable: once published, an installer is never changed or
  replaced, and its tag never moves. A bad release is followed by a higher version, never replaced.
- **Release manifests.** `channels/<channel>.json` names a channel's newest version, its installer's address, size
  and SHA-256. `channels/<channel>.json.sig` is the Ed25519 signature over the manifest file's exact bytes, in
  base64. `.gitattributes` marks `channels/*` as binary so git never changes those bytes.
- **Channels.** `alpha` is the channel Workbench follows. `proof` is a test channel that the app reads only when it
  is started with `--update-channel=proof`.

## How Workbench checks an update

Workbench carries two public keys, current and next. It reads the manifest and its signature from
`raw.githubusercontent.com`, checks the signature with those keys before it reads any field, downloads the installer
from this repository's release, and checks its size and SHA-256 against the manifest. A manifest whose signature
fails is ignored, and nothing installs until the user clicks Restart to update.

## Support and security reports

Write to the support address on the Workbench support page. Issues are off in this repository; please do not post
logs or paths anywhere public.
