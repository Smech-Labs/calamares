<!-- SPDX-FileCopyrightText: 2026 Smech Labs
     SPDX-License-Identifier: CC0-1.0
-->

# Smech Labs' Calamares fork

This is a Smech Labs fork of [Calamares](https://github.com/calamares/calamares),
the distribution-independent system installer, tracked on its own
`smechos-main` branch. It exists for one concrete reason: SmechOS's
build pipeline (`spk-compile.py`'s `phase_calamares`, in
[`Smech-Labs/SmechDeploy`](https://github.com/Smech-Labs/SmechDeploy))
points `CALAMARES_GIT_URL` at **this** repository, not upstream's,
so SmechOS's graphical installer is built from a source Smech Labs
actually controls — reproducible builds, and a real place to carry a
patch if upstream ever needs one, instead of vendoring a tarball or
hoping upstream's `calamares` branch never moves under us mid-build.

## Current state: an unmodified mirror, by design

As of this writing there are **no Smech Labs patches to the Calamares
source tree itself** — this branch is a clean mirror of upstream's
`calamares` branch. That's intentional, not an oversight: Calamares'
own design goal, stated in its own docs, is that distributions
shouldn't need "cumbersome patching" to customize it — branding
components and external modules are supposed to be enough. SmechOS
takes that at face value and keeps actual customization *outside*
this repository:

- A `smechos` branding component (`usr/share/calamares/branding/smechos`)
  and `/etc/calamares/modules-search` config are generated at build
  time by `phase_calamares`, written directly into the target rootfs —
  never committed here.
- The live-session integration (an autostart entry that only launches
  `calamares` when `calamares=1` is on the kernel command line) is
  also assembled by the build script, not part of this source tree.

If SmechOS ever genuinely needs a real patch to Calamares' own code
(not just branding/config), it lands here, on `smechos-main`, as a
real commit on top of upstream — that's the whole point of this fork
existing as a repo rather than a pinned tarball.

## Staying in sync with upstream

An `upstream` remote (`https://github.com/calamares/calamares.git`,
default branch `calamares`) is already configured alongside `origin`.
To pull in a new upstream release:

```sh
git fetch upstream
git merge upstream/calamares   # or rebase, if smechos-main ever carries real patches
git push origin smechos-main
```

`spk-compile.py` pins `CALAMARES_VER` separately from whatever's
checked out here, so bumping this branch and bumping the version
SmechOS actually builds against are two separate, deliberate steps —
check `SmechDeploy/spk-compile.py` before assuming a sync here alone
changes what ships.

## Building it

Nothing about the actual build has diverged from upstream Calamares:
it's still a KDE-Frameworks/Qt-based, CMake-built application. See
upstream's [`CONTRIBUTING.md`](CONTRIBUTING.md) in this same tree for
dependencies and build instructions — that content hasn't been
rewritten, since it's still accurate here.

## The long-term plan: this fork is transitional

SmechOS's own installer roadmap is to eventually replace Calamares
entirely with **[SmechInstall](https://github.com/Smech-Labs/smech-install)**,
a ground-up Qt6/QML + GTK4/libadwaita dual-frontend installer — Calamares'
single Qt-based UI doesn't feel native once SmechOS supports more than
one desktop environment, which is the whole reason SmechInstall exists.
Until SmechInstall reaches parity, this fork stays SmechOS's real,
current installer source, not a placeholder.

## License

Calamares ships multiple licenses depending on the file (GPL-3.0-or-later,
LGPL-2.1/3.0, MIT, BSD-2-Clause, CC0-1.0 for docs like this one) — see
[`LICENSES/`](LICENSES/) and [`.reuse/dep5`](.reuse/dep5) for the
authoritative per-file breakdown. Nothing about licensing changes in
this fork.
