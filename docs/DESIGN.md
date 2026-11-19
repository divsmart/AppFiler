# AppFiler design notes

This is the working design: what's decided, what's still open, and the order things get built in. The README says what AppFiler is for users; this file says how it's built. When a decision changes, change it here first.

## What AppFiler is, and isn't

AppFiler is an ordinary Android app that sorts installed apps under user-defined labels and opens them from home-screen widgets and shortcuts. It is **not a launcher**: it never registers as a home screen, and the user's launcher stays exactly as it is.

## Hard constraints

These are fixed. Any change that breaks one is wrong, however useful it seems.

- No `INTERNET` permission, and no library that brings network code with it
- No `QUERY_ALL_PACKAGES`; app discovery uses a `<queries>` element for `MAIN`/`LAUNCHER` intents only
- No usage-access, storage or other runtime permissions—the goal is zero permissions the user has to approve
- No proprietary dependencies: Kotlin, AndroidX and Jetpack Compose only, all Apache 2.0
- No self-updating code
- `android:allowBackup="false"`, plus data-extraction rules that exclude everything (Android 12+), so nothing goes to cloud backup
- No data leaves the app except through an export the user starts and saves through the system file picker

The test for every release: `grep uses-permission app/src/main/AndroidManifest.xml` should print nothing.

## Platform

| Decision | Value |
|---|---|
| Language and UI | Kotlin, Jetpack Compose |
| Minimum Android | 8.0 (API 26) |
| Target | The current Android version, kept up to date each year |
| App ID | **Open—must be decided before the first build** (see below) |
| Licence | GPL-3.0-or-later |
| Primary test device | Pixel running GrapheneOS |

API 26 is the floor because three things the design depends on arrived in it: `PackageManager.getChangedPackages()` (detecting new installs without broadcasts), `ApplicationInfo.category` (the fallback for automatic labelling) and `ShortcutManager.requestPinShortcut()` (pinned label shortcuts).

## Data model

- **App**: identified by package name plus launcher activity, since one package can have more than one launcher icon. Name and icon are read from the system each time, never stored.
- **Label**: a name chosen by the user, with an order and an optional icon or colour.
- **Assignment**: links an app to a label. Many-to-many: an app can have any number of labels.
- **Rule**: a pattern and a label, from the rule file.
- **Launch count**: per app, counting only launches made through AppFiler. Used for "most used" sorting.

Storage is Room (AndroidX) in the app's private storage.

## How things work

**Finding apps.** Query the package manager for activities matching `MAIN`/`LAUNCHER`. That's every app with a launcher icon, and nothing else.

**New installs.** On each start, call `getChangedPackages()` with the last sequence number seen. New apps are matched against the rules; anything no rule matches goes to the "Other Apps" inbox. Uninstalled apps are removed from their labels. No permanent broadcast receiver is needed.

**Automatic labelling.** Rules first, then Android's own category (game, audio, video, image, social, news, maps, productivity) for apps whose developer set one. Each app's labels show whether they came from the user, a rule or a category, and the user's own choices always win.

**Widgets.** One widget instance per label, chosen when the widget is placed. Tapping an icon opens that app directly. Whether to build widgets with Glance (AndroidX) or plain `RemoteViews` is decided at milestone 3.

**Shortcuts.** Pinned shortcuts that open AppFiler straight to one label, or to the inbox.

**Export and import.** Through the system file picker, so no storage permission is needed. One plain-text file holds labels, assignments and rules, readable by a person.

## Rule file format (draft)

```
# Comments start with #
dev.soupslurpr.appverifier   Security
dev.imranr.obtainium         Security
dev.soupslurpr.*             Tools
```

- One rule per line: an app ID or pattern, whitespace, then a label name
- A trailing `*` matches any app ID with that prefix
- An app matching several rules gets every matching label
- Lines that don't parse are reported to the user, never silently dropped

This is open for comment in the forum and Discussions, and may change before milestone 5.

## Build order

Each milestone ends with a working build installed on a real phone, and the hard constraints checked.

1. **Skeleton.** Empty Compose app, final app ID, manifest with no permissions, release signing set up. Builds and installs.
2. **App list and labels.** List launchable apps; create, rename, reorder and delete labels; assign apps to labels.
3. **Widgets.** A widget per label that opens apps.
4. **Inbox.** New-install detection and the "Other Apps" view.
5. **Rules.** Rule file parsing, automatic labelling, Android-category fallback.
6. **Export and import.**
7. **Shortcuts and polish.** Pinned label shortcuts, "most used" sorting, first-run explanation.
8. **First release.** Signed APK on GitHub Releases with the certificate fingerprint in the README; split APKs ready for Accrescent.

## Open decisions

- **App ID**: `io.github.divsmart.appfiler` or `com.divsmart.appfiler`. Permanent once released.
- **Widget style**: one widget per label, or one widget with label tabs (asked in the README).
- **Rule file details**: as above, pending feedback.
- **Starter rules**: whether a starter rule file ships with the app, and what's in it.
- **Widgets library**: Glance or `RemoteViews` (milestone 3).

## Release and signing

- A release key is created once, before milestone 1 is finished, and backed up off the machine. Losing it means users can't update.
- Every release is signed with it; its SHA-256 fingerprint is published in the README.
- No release is made without the permission check above passing.
