# Rules for Claude Code in this repository

AppFiler is a privacy-first, tag-based app organizer for Android. These rules
apply in every session. They come from `docs/DESIGN.md` ("Hard constraints"),
`README.md` and `CONTRIBUTING.md`; if those change, DESIGN.md wins and this
file must be updated to match.

## Hard constraints — never break these

- **No `INTERNET` permission, and no network code.** No library that brings
  network code with it, directly or transitively.
- **No `QUERY_ALL_PACKAGES`.** App discovery uses a `<queries>` element for
  `MAIN`/`LAUNCHER` intents only.
- **No runtime permissions.** No usage access, no storage, nothing the user has
  to approve. The goal is zero permissions of any kind in the built APK.
- **No proprietary dependencies.** No Google Play Services, Firebase, analytics,
  crash reporting or ad SDKs. Only Kotlin, AndroidX and Jetpack Compose
  (Apache 2.0).
- **No new dependency of any kind without the maintainer's explicit approval**,
  even from AndroidX. Ask first, and say what it pulls in transitively.
- **No self-updating code.** The app never checks for or downloads updates.
- **No cloud backup.** `android:allowBackup="false"` stays, and
  `res/xml/data_extraction_rules.xml` must keep excluding every domain from
  both `<cloud-backup>` and `<device-transfer>`.
- **No data leaves the app** except through an export the user starts and saves
  through the system file picker.

If a feature seems to need breaking one of these, stop and ask. Don't look for
a workaround.

## Permissions added by libraries

Libraries can merge permissions into the APK at build time, so checking the
source manifest isn't enough.

- `androidx.core` adds `org.appfiler.DYNAMIC_RECEIVER_NOT_EXPORTED_PERMISSION`.
  It's removed in `app/src/main/AndroidManifest.xml` with `tools:node="remove"`.
  **Don't use `ContextCompat.registerReceiver(…, RECEIVER_NOT_EXPORTED)`
  without asking first**: on older APIs that permission is what protects such
  receivers, and it's been removed.
- After any change to the manifest or the dependencies, check the merged
  manifest and the built APK. Both must list no permissions:

  ```bash
  grep -n "uses-permission" app/build/intermediates/merged_manifest/release/processReleaseMainManifest/AndroidManifest.xml
  "$(ls -d ~/Android/Sdk/build-tools/* | sort -V | tail -1)"/aapt2 dump permissions app/build/outputs/apk/release/app-release*.apk
  ```

  `aapt2` should print only the package line.

## Builds stay offline

Gradle resolves only from `google()` and `mavenCentral()`. Lint's network
checks (dependency-version lookups, Play SDK Index) are turned off in
`app/build.gradle.kts`; keep them off. Don't add plugins, toolchain resolvers
or build services that download anything.

## Android SDK

- The SDK lives in `~/Android/Sdk` and is managed with the new `android` CLI.
  `sdkmanager` is deprecated; don't use it.
- **Every `android` command runs with `--no-metrics`.** `~/.androidrc` already
  sets it, but pass it explicitly anyway.
- The maintainer accepts SDK licences themselves. Never accept them on their
  behalf.

## Project facts

- App ID and namespace: **`org.appfiler`** (the project owns appfiler.org).
  Permanent once released.
- Licence: **GPL-3.0-or-later**. Every source file starts with
  `SPDX-License-Identifier: GPL-3.0-or-later`. Code taken from another project
  keeps its own copyright notice.
- Kotlin + Jetpack Compose, `minSdk` 26, target the current Android version.
- Release signing reads `keystore.properties`, which is git-ignored. Never
  create, commit or print signing keys or passwords.
- The release key lives outside the repository and the maintainer uses it by
  hand. Never look for it, read it or ask for its password. The public SHA-256
  fingerprint is in `docs/DESIGN.md` ("Release and signing").

## Commits

- Commit only when the maintainer asks, and show the diff first.
- **Never add a `Signed-off-by` line.** The DCO sign-off is a statement only a
  person can make; the maintainer adds it with `git commit -s` after review.
- Do add the `Co-Authored-By: Claude …` trailer to commits Claude helped with.
- Commit messages say *why* the change was made.
