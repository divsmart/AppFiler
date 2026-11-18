# AppFiler

A tag-based app organizer for Android that works *alongside* your launcher, not instead of it. Built for GrapheneOS first, with privacy as the one non-negotiable.

> **A word to our French-speaking users:** yes, we know. In French, *filer quelqu'un* means to tail them, the way the police shadow a suspect—and an app that follows you around is the last thing this project wants to be. The name is English: a filer is someone who files things away. AppFiler files your apps under labels, and the only thing it ever follows is your rule file.
>
> *Rassurez-vous : aucune filature ici. AppFiler range vos applis, rien de plus.*

> **Status:** planning. There's no code yet—this README is the design, posted for comments before the first line is written.

## Why

Anyone who used Android a decade ago may remember Apps Organizer and its fork Apps2Org. You gave each app as many labels as you liked, put a widget for each label on your home screen, and kept whichever launcher you preferred. New apps landed in an "Other Apps" bucket so nothing got lost. It was a simple idea, well ahead of its time, and it's been unmaintained since 2013. It won't even install on a modern phone.

The stock GrapheneOS launcher is good at its job and it's staying where it is. What's missing is a way to organize apps by *what they're for*—Banking, Travel, Church, Tools—without manually juggling folders, and without handing a third-party launcher the keys to the phone.

AppFiler aims to bring that workflow back, rebuilt for current Android.

## Privacy first

An app organizer sees every app you have installed and which ones you open. That list is more revealing than it looks: your bank, your health apps, your faith, your messaging apps and your VPN together form a profile, and one distinctive enough to pick a single phone out of millions. Giving an app organizer internet access is like connecting your keyboard to the internet—there's nothing to gain and everything to lose.

So the rules are fixed from day one:

- **No `INTERNET` permission, and no network code at all.** The app has no way to send anything anywhere, whatever ROM it runs on.
- **Sees only launchable apps.** It asks Android only for apps that have a launcher icon, which is all it needs. It never requests `QUERY_ALL_PACKAGES`.
- **No usage-access permission.** "Most used" sorting counts only the apps you open *through AppFiler*, never your system-wide usage history.
- **No storage permission.** Exports and imports go through Android's own file picker, which grants access to the one file you choose.
- **No proprietary code.** No Google Play Services, Firebase, analytics or crash reporting. The only dependencies are Kotlin, AndroidX and Jetpack Compose, all under the Apache 2.0 licence.
- **No automatic cloud backup.** Labels and rules stay in the app's private storage until *you* export them.
- **No built-in updater.** The app never checks for updates or downloads anything. Updates come through Obtainium or an app store, where you stay in control.

The aim is an app that never asks you to approve a permission at all. These rules aren't up for negotiation in pull requests either—see [CONTRIBUTING.md](CONTRIBUTING.md).

## Planned features

**Labels.** Give any app as many labels as you like. One app can sit under both Banking and Travel.

**A widget per label.** Put a Banking widget, a Tools widget, a Church widget on your home screen, the way Apps2Org did. Tap an icon in the widget and the app opens. Your launcher stays untouched.

**An "Other Apps" inbox.** Each time AppFiler opens, it checks for apps installed since last time. Anything not yet labelled waits in the inbox, so new installs are never lost.

**Automatic labelling from a rule file.** A plain-text file maps app IDs to labels, for example:

```
org.fossify.*        Tools
com.nextcloud.*      Cloud
helium314.keyboard   Tools
```

New installs that match a rule are labelled straight away. Android's own app categories (game, audio, social, productivity and so on) fill the gaps where a developer has set one. The rule file is yours to read and edit, with no guessing and nothing fetched from the internet.

**Plain-text export and import.** Labels and rules export to a readable file, so a factory reset or a new phone doesn't cost you your setup, and you can see exactly what's in it.

**Shortcuts to a single label.** Pin a home-screen shortcut that opens AppFiler straight to one label, or to the inbox.

## Official builds

Official builds are published in one place for now: this repository's **GitHub Releases** page. They're easiest to install and keep updated with [Obtainium](https://github.com/ImranR98/Obtainium). Accrescent is a planned second channel once the app is stable.

Every official build is signed with the same key. Before installing, check that the signing certificate matches:

```
Package name:  (to be published with the first release)
SHA-256:       (to be published with the first release)
```

You can check it with [AppVerifier](https://github.com/soupslurpr/AppVerifier), with Privacy Guides' [Verified Apps](https://github.com/privacyguides/verified-apps), or on a computer with:

```bash
apksigner verify --print-certs AppFiler-<version>.apk
```

**A build signed with any other key isn't ours.** The GPL lets anyone rebuild and redistribute the app, which is as it should be, but a copy signed with a different key comes from whoever built it, not from this project. That includes repositories that build and sign apps with their own keys.

## Checking the claims yourself

Don't take a README's word for it. List the permissions the app asks for:

```bash
grep -n "uses-permission" app/src/main/AndroidManifest.xml
```

On the phone, Settings → Apps → AppFiler shows the same thing. If Network isn't listed, the app can't reach the internet. And because the full source is here, anyone can build it and compare.

## Technical outline

- Kotlin and Jetpack Compose, targeting current Android
- Minimum Android 8.0 (API 26), the first version where Android can report which apps changed since the last check
- Developed and tested on GrapheneOS
- Signed APKs on GitHub Releases, with SHA-256 checksums
- No self-updating code, in line with the rules of security-focused stores such as Accrescent

## Standing on others' shoulders

This project borrows ideas, and possibly code, from:

- **[Apps Organizer](https://code.google.com/p/appsorganizer)** (2009), the original label-based organizer. GPL-3.0-or-later.
- **[Apps2Org](https://github.com/pfalcon/apps2org)** by Paul Sokolovsky, its fork, which introduced the "Other Apps" category. GPL-3.0-or-later.
- **[AppStow](https://github.com/1260er/AppStow)**, for multi-category apps, new-install detection and counting only its own launches. GPL-3.0-or-later.
- **[Pixel Tag Drawer](https://github.com/iwadjp/pixel-tag-drawer)**, for shortcuts that open one tag and an untagged view. MIT.

Any code taken from these projects keeps its original copyright notice.

## Contributing

Ideas, bug reports and code are all welcome. Please read [CONTRIBUTING.md](CONTRIBUTING.md) before opening a pull request—especially the privacy rules—and the [Code of Conduct](CODE_OF_CONDUCT.md) before taking part anywhere in the project.

There's no Contributor License Agreement. Contributors keep the copyright in their own work and certify it with a simple sign-off on each commit, the same way the Linux kernel does.

Need help using the app? See [SUPPORT.md](SUPPORT.md).

## Support the project

AppFiler is free and will stay free, with nothing held back for donors. If it's useful to you, the most valuable support is a clear bug report, a rule for the starter file, or a word to someone who'd like it.

If you'd also like to donate, Bitcoin is welcome through the project's BTCPay Server page:

**[Donate with Bitcoin](https://DONATION-PAGE-URL)**

BTCPay Server is free, open-source and self-hosted. It creates a new address for every donation, so donations can't be linked to each other, and no account or email address is needed.

## Licence

GPL-3.0-or-later, continuing the Apps Organizer and Apps2Org line. Fork it, change it, share it—just keep it free.

## Comments wanted

Before any code gets written, it would help to hear:

- Is a widget per label the right way in, or would you rather have one widget with label tabs?
- What should the rule file look like? Plain `app-id label` lines, or something more structured?
- Which apps would you want labelled out of the box, if a starter rule file shipped with the app?
- Anything here that would stop you trusting it?
