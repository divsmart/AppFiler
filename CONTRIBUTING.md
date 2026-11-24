# Contributing to AppFiler

Thanks for wanting to help. This file explains what the project will and won't accept, and how to get a change merged without wasted effort on either side.

## The privacy rules come first

AppFiler exists to be an app organizer you can trust, and that trust rests on a short list of rules. A pull request that breaks any of them will be declined, however good the code is:

- **No new permissions.** In particular no `INTERNET`, no `QUERY_ALL_PACKAGES`, no usage access and no storage permissions. If a feature seems to need one, open an issue first—there's almost always another way.
- **No network code**, including libraries that bundle any.
- **No proprietary dependencies.** That means no Google Play Services, Firebase, analytics, crash reporting or ad SDKs.
- **No self-updating code.** The app never checks for or downloads updates.
- **No data leaving the app** except through an export the user starts and saves themselves.

New dependencies of any kind need an issue and agreement *before* the pull request. Each one is code everyone has to trust, so the bar is high: Kotlin, AndroidX and Jetpack Compose cover nearly everything this app should need.

## Before you write code

**Open an issue first** for anything bigger than a typo or an obvious bug fix. Describe the problem you want to solve, not only the solution you have in mind. That gives everyone a chance to agree on the approach before you spend an evening on it, and saves the disappointment of a pull request that doesn't fit.

Check the existing issues too. Your idea may already be under discussion, and adding to that thread helps more than starting a new one.

## Making a change

- Keep each pull request to **one change**. Small pull requests get reviewed quickly; large ones sit waiting.
- Follow the existing code style. The project uses Kotlin and Jetpack Compose.
- **Test on a real device** if you can, ideally running GrapheneOS, and say in the pull request what you tested and on which device and Android version.
- Write commit messages that say *why* the change was made, not only what changed.
- Update the README if your change affects what users see or what the app is allowed to do.

## Signing off your work (DCO)

There's no Contributor License Agreement, and there never will be. You keep the copyright in your contribution. Instead, each commit carries a sign-off certifying that you have the right to submit it under the project's licence, as set out in the [Developer Certificate of Origin](https://developercertificate.org/). The Linux kernel and many other projects work the same way.

Add the sign-off by committing with `-s`:

```bash
git commit -s -m "Explain why the change was made"
```

That adds a line like this to the commit message:

```
Signed-off-by: Your Name <you@example.com>
```

Use your real name or the name you're known by in the community, and an email address that works. Pull requests without a sign-off can't be merged.

## Licence

By contributing, you agree that your work is released under the project's licence, GPL-3.0-or-later. If your contribution includes code from another project, keep its copyright notice and say where it came from in the pull request. Only code under a licence compatible with the GPL can be accepted.

## You're responsible for what you submit

AppFiler follows the same line as the Linux kernel: AI coding assistants are tools, like any other. Nobody has to use them, nobody is turned away for using them, and every contribution is held to the same standards however it was written.

So use whatever tools help you work. Whatever you used, you should understand every line you submit, have built and tested it yourself, and be able to answer questions about it in review.

**Say when you used AI.** If an AI tool wrote or substantially shaped part of your change, say so in the pull request and add a trailer to the commit naming the tool and model, the way the kernel does:

```
Assisted-by: Claude:claude-opus-5-5
```

Claude Code adds its own `Co-Authored-By: Claude …` line, and that's fine too. Editors, compilers, linters and autocomplete don't need a mention.

**Only people sign off.** An AI tool must never add a `Signed-off-by` line. The DCO is a statement that only a person can make, so add the sign-off yourself, once you've reviewed the change.

**The same applies at the top.** The maintainer writes AppFiler with Claude Code, under the rules in this file and the README, and those commits carry a `Co-Authored-By` line so anyone can see which code came from where. Every change is reviewed before it's merged, and the maintainer takes final responsibility for everything that goes into a release. If you'd rather write code entirely by hand, that's welcome, and so is careful review from people who read AI-written code with a critical eye.

## Reporting a security problem

**Don't open a public issue for a security problem.** Report it privately through GitHub's "Report a vulnerability" button on the repository's Security tab. You'll get a reply as soon as possible, and credit when the fix is released, if you'd like it.

## Releases and signing

Contributors never need a signing key. The maintainer builds and signs every official release with the project's key, whose fingerprint is published in the README. That's what lets users check that a build really comes from this project.

## Code of Conduct

Everyone taking part in the project is expected to follow the [Code of Conduct](CODE_OF_CONDUCT.md), in issues, pull requests, reviews and anywhere else the project is discussed.
