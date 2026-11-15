# I rolled back my Steam installation

After Steam worked, I removed it and reversed my custom policy change. I recorded the steps below in the past tense; terminal output from this rollback is not included in the repository.

**I backed up local saves, screenshots, settings and any important game files before starting.** I did not assume every save had reached Steam Cloud.

## I exited Steam first

I closed games and chose **Steam → Exit**. I checked that Steam and its helpers had stopped before unloading policy or removing files.

## I removed my profile

I unloaded the exact custom profile I created in this session:

```bash
sudo apparmor_parser -R /etc/apparmor.d/steam
```

I proceeded only after a successful unload, then removed my custom file:

```bash
sudo rm /etc/apparmor.d/steam
```

`-R` unloads the profile from the kernel and needs the profile definition, which is why I did not delete the file first. [Ubuntu's parser manual](https://manpages.ubuntu.com/manpages/noble/man8/apparmor_parser.8.html)

I treated a failed unload or a file replaced by a later package update as a reason to stop and review. I reversed my own addition; I am not recommending removal of arbitrary distribution policy.

I never changed the global namespace sysctl value, so there was no global override from this session to remove.

## I previewed package removal

I simulated the purge for the packages I actually recorded:

```bash
sudo apt-get --simulate purge steam-installer steam-devices \
  steam-libs:amd64 steam-libs:i386 steam-libs-i386:i386
```

After reviewing the proposed removals and checking that they matched my intent, I ran:

```bash
sudo apt purge steam-installer steam-devices \
  steam-libs:amd64 steam-libs:i386 steam-libs-i386:i386
```

I also reviewed unused dependencies before confirming anything:

```bash
sudo apt-get --simulate autoremove
```

Only after reviewing the list did I run `sudo apt autoremove`. APT's unused-package list is system-wide, not a precise list of everything Steam added; `purge` also does not delete configuration or data in my home directory. [Ubuntu APT manual](https://manpages.ubuntu.com/manpages/noble/man8/apt.8.html) · [Simulation documentation](https://manpages.ubuntu.com/manpages/noble/man8/apt-get.8.html)

## I handled user data separately

I inspected these locations, including whether they were symlinks and whether they led to another library:

```bash
ls -ld -- "$HOME/.steam" "$HOME/.local/share/Steam"
readlink -f -- "$HOME/.steam"
readlink -f -- "$HOME/.local/share/Steam"
```

Missing paths are possible. I checked for additional libraries in Steam's Storage settings before uninstalling and made my backups first.

**The next commands permanently delete data from the named locations.** I ran them after deciding to discard the Steam installation, downloaded games and Proton environments stored there, including any local saves within them:

```bash
rm -rf -- "$HOME/.steam" "$HOME/.local/share/Steam"
rm -f -- "$HOME/.steampath" "$HOME/.steampid"
```

I ran those as my ordinary user, not with `sudo`. I did not treat them as removing libraries on other drives or every game save stored elsewhere. Symlinks and non-default storage make inspection important.

## I evaluated i386 separately

I initially wondered why I had been advised to leave `i386` enabled after removing Steam. I learned to distinguish a registered package architecture from the actual installed packages for that architecture. `dpkg` treats additional architectures as a system-wide configuration, not Steam-owned state. [Ubuntu dpkg manual](https://manpages.ubuntu.com/manpages/noble/man1/dpkg.1.html)

I checked:

```bash
dpkg --print-foreign-architectures
dpkg -l | grep ':i386'
```

I treated architecture removal as optional, not an automatic part of uninstalling Steam. Other applications may still need 32-bit libraries. The following commands apply only when `i386` is no longer needed and no package records still require it; I am not recording a specific architecture-removal outcome here:

```bash
sudo dpkg --remove-architecture i386
sudo apt update
```

I did not purge all `:i386` packages blindly or use `--force-architecture`. `dpkg` refuses architecture removal when it is still in use in its database; I treated that refusal as a reason to review the remaining records rather than bypass the check. [Ubuntu dpkg manual](https://manpages.ubuntu.com/manpages/noble/man1/dpkg.1.html)

I did not have a pre-installation inventory proving that enabling `i386` was part of this installation. Keeping or removing it remained a separate decision, not a mandatory Steam uninstall step.

## I checked the result

```bash
command -v steam
dpkg -l | grep -E '^ii[[:space:]]+steam'
sudo aa-status | grep -i steam
sysctl kernel.apparmor_restrict_unprivileged_userns
```

I checked for a remaining Steam launcher, installed Steam packages and the custom Steam profile. My expected result after complete removal was no matches for those checks, with the global namespace value still `1`, as it was throughout my installation session. I have not added captured rollback output to substantiate individual results here.

I used these checks to look for leftovers, not as a reason to delete unrelated files. My rollback addressed known changes; it did not claim to restore the exact pre-installation state of every package.

Next: [I reflected on the learning curve](09-what-i-learned.md).
