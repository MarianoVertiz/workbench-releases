# Workbench releases

This repository holds only Workbench's installers and its signed release manifests. It has no source code.
Workbench's source is private.

## Getting started

Workbench is a desktop app for Windows 11 and Apple Silicon Macs that runs Claude Code and Codex sessions on your own
subscriptions. You need a Claude plan (Pro or Max), a ChatGPT plan, or both. Setting it up takes about 20 minutes,
most of it installing the tools Workbench uses.

The steps below are for Windows. On a Mac, follow [On a Mac](#on-a-mac) instead.

### 1. Download the installer

1. Open [Releases](https://github.com/MarianoVertiz/workbench-releases/releases) and find the release marked
   **Latest**.
2. Under **Assets**, click `Workbench-Setup-<version>.exe` (about 113 MB). Ignore the two "Source code" files.

The installer is signed with Workbench's own certificate, not one Windows knows, so your browser and Windows warn
you before you open it. This is expected.

- **Microsoft Edge** says the file "isn't commonly downloaded". Point at the download, click the `...` button next
  to it, and click **Keep**. Edge then says "Make sure you trust" the file. Click the arrow next to **Delete** and
  click **Keep anyway**.
- **Other browsers** use different words. Choose the option that keeps the file.
- **Windows** may show "Windows protected your PC" when you open the file. Click **More info**, then
  **Run anyway**. Our test machine did not show this screen, but yours may.

### 2. Install

Open the downloaded file. Workbench installs for your Windows account only, so it needs no administrator approval.
After about 30 seconds Workbench opens on its setup page.

### 3. Set up the tools

The setup page has a card for each tool Workbench uses: Claude Code, Codex, Git and Node.js. If you already use
Claude Code or Codex on this computer, their cards say **Ready** and you can skip to step 4.

For each card that says **Not found**, copy its command, paste it into PowerShell (Start menu, type
`PowerShell`), press Enter, and then press **Check again** on the setup page. These are the commands:

| Tool | Command | Notes |
| --- | --- | --- |
| Git | `winget install --id Git.Git -e --source winget` | Windows asks "Do you want to allow this app to make changes"; click **Yes**. |
| Claude Code | `winget install Anthropic.ClaudeCode --source winget` | Use this command even if the setup page shows a shorter one. Without `--source winget`, winget stops to ask about the Microsoft Store's terms. |
| Codex | First `winget install Microsoft.PowerShell --source winget`, then, in a new PowerShell window, `pwsh -ExecutionPolicy ByPass -c "irm https://chatgpt.com/codex/install.ps1 \| iex"` | Codex's installer needs PowerShell 7. When it asks "Start Codex now?", type `N`. |

Node.js is optional. Workbench works without it.

You need Claude Code, Codex, or both, and you need Git.

### 4. Sign in

- **Claude Code.** Click **Sign in to Claude Code**. A terminal opens. Press Enter to pick a text style, choose
  "Claude account with subscription", and sign in on the page that opens in your browser. Back in the terminal,
  press Enter twice, then choose **Yes, I trust this folder**.
- **Codex.** Click **Sign in to Codex** and sign in with your ChatGPT account in the browser. The terminal closes by
  itself.

Workbench never sees your password. Each tool signs in through its own page.

### 5. Start

1. Scroll down and click **Save and start**. Workbench opens Settings, Providers.
2. Click **Run the test turn** on Claude Code and on Codex. Each answers in about 15 to 30 seconds and says
   **Passed**. That is your first session.
3. Ignore the Gemini card for now. Setup does not cover it yet.

To try the new interface, open Settings, General, and set **Interface** to **New**. Workbench asks you to name your
main bot. Type a name and click the blue **Create** button.

### Updates and uninstalling

Workbench checks this repository for new versions by itself. When one is ready, About shows **Restart to update**.

To uninstall, use Windows Settings, Apps. Your settings and sessions stay in `%APPDATA%\Workbench` and your
workspace folder (`Workbench` in your user folder). Delete those too if you want a fresh start next time.

If something goes wrong, tell the person who sent you this page.

### On a Mac

Workbench runs on Macs with Apple Silicon (M1 or later). The Mac app is not notarized by Apple yet, so macOS asks
once before it opens.

1. Open [Releases](https://github.com/MarianoVertiz/workbench-releases/releases) and find the newest release whose
   name ends in **for Mac**. Under **Assets**, click `Workbench-<version>-arm64.dmg` (about 127 MB).
2. Open the dmg, drag **Workbench** onto **Applications**, then eject the disk image.
3. Open Workbench from Applications. macOS says "Workbench" Not Opened. Press **Done**, the grey button. Do not
   press **Move to Trash** or Return.
4. Open **System Settings**, then **Privacy & Security**. Scroll down to Security, where it says Workbench was
   blocked, and press **Open Anyway**. Type your Mac's password when asked.
5. Open Workbench again and press **Open**. Later starts open with no question.

Always run Workbench from Applications, not from the disk image or Downloads. Otherwise Restart to update cannot
replace it.

The setup page then works as on Windows (steps 3 to 5 above), with two differences. Each card that says
**Not found** shows a macOS command: paste it into Terminal (Applications, Utilities) instead of PowerShell. Sign in
opens a Terminal window; sign in there, answer yes to trust the folder, and close the window.

To uninstall, drag Workbench from Applications to the Trash. Your settings stay in
`~/Library/Application Support/Workbench`.

## What is here

- **Installers.** Each Windows version is a GitHub release tagged `v<version>` whose one asset is
  `Workbench-Setup-<version>.exe`. Each Mac version is a release tagged `v<version>-mac` with
  `Workbench-<version>-arm64.zip`, which the Mac app updates from, and `Workbench-<version>-arm64.dmg`, for the
  first install. Releases are immutable: once published, an installer is never changed or replaced, and its tag
  never moves. A bad release is followed by a higher version, never replaced.
- **Release manifests.** `channels/<channel>.json` names a channel's newest version, its installer's address, size
  and SHA-256. `channels/<channel>.json.sig` is the Ed25519 signature over the manifest file's exact bytes, in
  base64. `.gitattributes` marks `channels/*` as binary so git never changes those bytes.
- **Channels.** `alpha` is the channel Workbench follows on Windows, and `alpha-mac` the one it follows on a Mac. `proof` is a test channel that the app reads only when it
  is started with `--update-channel=proof`.

## How Workbench checks an update

Workbench carries two public keys, current and next. It reads the manifest and its signature from
`raw.githubusercontent.com`, checks the signature with those keys before it reads any field, downloads the installer
from this repository's release, and checks its size and SHA-256 against the manifest. A manifest whose signature
fails is ignored, and nothing installs until the user clicks Restart to update.

## Release keys

The Ed25519 public keys that Workbench carries, each the raw 32-byte key in base64. A manifest is genuine when its
`.sig` verifies over the manifest file's exact bytes under one of them. This list changes in the same step as each
key replacement.

| Slot | Public key |
| --- | --- |
| current | `HY8MtS2QTQhY4Nym222GKtapUpylQkNiEb2RzPQXSS4=` |
| next | `iceGs/Gpy3P3fNwd0U7EPaOCK9Dl/5ZswWMCZMAO3mo=` |

## Support and security reports

Write to the support address on the Workbench support page. Issues are off in this repository; please do not post
logs or paths anywhere public.
