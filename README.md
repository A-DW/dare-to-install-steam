# dare-to-install-steam

**Someone thought I couldn't install Steam on Ubuntu.**

I got it running. The unexpected part was how much I learned between clicking **Install** and reaching the sign-in screen.

I started with a video tutorial and a small challenge. I ended up reading kernel audit logs, investigating AppArmor, creating an application-specific compatibility exception, and asking how I could undo my changes responsibly.

This is my learning curve—not a universal installer, a claim that I became a Linux expert overnight, or proof that every Windows game works on Ubuntu.

## My starting point

| Item | What I recorded |
| --- | --- |
| Operating system | Ubuntu 24.04, 64-bit |
| Installation | Ubuntu's APT-packaged `steam-installer` |
| Launcher | `/usr/games/steam` |
| Package version | `1:1.0.0.79~ds-2` in my captured package list |
| Steam data | `~/.steam/debian-installation` in my logs |
| Game I wanted to try | Business Tour |
| Confirmed result | Steam reached its sign-in screen; its AppArmor profile was loaded |
| Not demonstrated | A completed Business Tour installation, gameplay, or executed rollback |

I started with [this video tutorial](https://www.youtube.com/watch?v=13ZXnyGQf_A). The terminal excerpts in [evidence/](evidence/README.md) are my session record; personal values have been sanitised.

## The unexpected obstacle

```text
bwrap: setting up uid map: Permission denied
Steam now requires user namespaces to be enabled.
```

I initially had a message that sounded like a disabled kernel feature. My settings and audit logs told a more specific story: namespace support was enabled, but AppArmor was restricting the operations Steam's helper needed.

I also tried reloading `/etc/apparmor.d/steam` and discovered it did not exist. Creating and loading the profile recorded in this repository was the change after which Steam reached the sign-in screen.

## My troubleshooting journey

1. [I accepted the challenge](docs/01-the-challenge.md).
2. [I installed Steam and hit the first failure](docs/02-installation-and-first-failure.md).
3. [I investigated instead of reinstalling](docs/03-investigating-the-error.md).
4. [I recorded the AppArmor exception](docs/04-the-apparmor-fix.md).
5. [I verified what actually worked](docs/05-verifying-the-result.md).
6. [I learned how Steam runs Windows games](docs/06-how-steam-runs-windows-games.md).
7. [I planned a Business Tour test](docs/07-installing-and-checking-business-tour.md).
8. [I worked out a rollback plan](docs/08-rollback-plan.md).
9. [I reflected on what I learned](docs/09-what-i-learned.md).

## My next question: Windows games?

Getting Steam open did not explain how a Windows game could run on Ubuntu. I learned that Steam can launch its Windows build through **Proton**, which uses Wine and additional components rather than permanently converting the executable into a Linux program. [Valve's Proton project](https://github.com/ValveSoftware/Proton)

I explain the launch path, Windows API compatibility, graphics translation, and per-game environments in [the Proton chapter](docs/06-how-steam-runs-windows-games.md). I do not equate a successful download with guaranteed compatibility: Valve documents remaining Proton issues and game-specific anti-cheat requirements. [Valve's compatibility documentation](https://partner.steamgames.com/doc/steamhardware/proton)

## My security boundary

The profile I used has `flags=(unconfined)` and grants `userns`. I am recording a compatibility exception, **not a restrictive sandbox**. Ubuntu documents this profile mode for applications needing user namespaces. [Ubuntu 24.04 release notes](https://documentation.ubuntu.com/release-notes/24.04/)

I kept `kernel.apparmor_restrict_unprivileged_userns = 1`. I did not disable AppArmor globally, run Steam as root, or make `bwrap` setuid.

I would inspect my current package policy before applying this historical fix again. I would not overwrite a profile supplied by a newer package without reviewing it.

## What I am publishing

```text
dare-to-install-steam/
├── README.md
├── docs/                    # My chronological investigation and explanations
├── configs/apparmor/steam    # The exact compatibility profile I used
└── evidence/
    ├── README.md            # Provenance and sanitisation notes
    ├── logs/                # Excerpts from the supplied session output
    └── screenshots/         # Privacy guidance; no images committed yet
```

I have not added automated install or cleanup scripts. I have not selected a repository license yet; I do not intend to imply that this repository licenses Steam, games, or third-party documentation.

Someone thought I couldn't install Steam on Ubuntu. I reached the sign-in screen—and learned why the first attempt failed.
