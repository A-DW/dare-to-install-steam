# I hit the first failure

I clicked the installation option, watched Steam update, and expected to reach its interface. Instead, I received a user-namespace error.

## I used the terminal

I launched Steam as my normal user:

```bash
steam
```

My output included:

```text
Steam runtime environment up-to-date!
bwrap: setting up uid map: Permission denied
Steam now requires user namespaces to be enabled.
```

I preserved a longer excerpt in [the startup log](../evidence/logs/steam-startup-error.txt). The permission error gave me a more precise starting point than the graphical dialog.

## I checked my installation

```bash
command -v steam
dpkg -l | grep -E '^ii[[:space:]]+steam'
snap list steam
```

I found `/usr/games/steam`, the APT `steam-installer` package and its related libraries, and no installed Steam Snap. I did not treat the mention of Flatpak in Steam's error as evidence that I had installed Flatpak.

The error linked to [Flatpak's namespace requirements](https://github.com/flatpak/flatpak/wiki/User-namespace-requirements). I treated that as diagnostic background, not as an instruction to change packaging formats.

## I avoided an early guess

I did not have evidence of a missing 32-bit library from this error. I also did not have a reason to reinstall Steam before checking why the helper could not set up its UID mapping.

Next: [I checked the settings and logs](03-investigating-the-error.md).
