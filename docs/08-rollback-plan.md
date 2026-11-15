# I worked out a rollback plan

After Steam worked, I wanted to know how I could remove it and reverse my custom policy change. I have not supplied evidence that I executed this rollback, so I label it as a plan.

**I would back up local saves, screenshots, settings and any important game files before starting.** I would not assume every save had reached Steam Cloud.

## I would exit Steam first

I would close games and choose **Steam → Exit**. I would check that Steam and its helpers had stopped before unloading policy or removing files.

## I would remove my profile

For the exact custom file I created in this session:

```bash
sudo apparmor_parser -R /etc/apparmor.d/steam
```

I would proceed only after a successful unload, then remove my custom file:

```bash
sudo rm /etc/apparmor.d/steam
```

`-R` unloads the profile from the kernel and needs the profile definition, which is why I would not delete the file first. [Ubuntu's parser manual](https://manpages.ubuntu.com/manpages/noble/man8/apparmor_parser.8.html)

I would stop if the command failed, or if a later package update had replaced the file with a package-owned profile. I am reversing my own addition, not recommending removal of arbitrary distribution policy.

I never changed the global namespace sysctl value, so there is no global override from this session to remove.

## I would preview package removal

I would simulate the purge for the packages I actually recorded:

```bash
sudo apt-get --simulate purge steam-installer steam-devices \
  steam-libs:amd64 steam-libs:i386 steam-libs-i386:i386
```

After reviewing the proposed removals, and only if they matched my intent, I would run:

```bash
sudo apt purge steam-installer steam-devices \
  steam-libs:amd64 steam-libs:i386 steam-libs-i386:i386
```

I would also review unused dependencies before confirming anything:

```bash
sudo apt-get --simulate autoremove
```

Only after reviewing the list would I run `sudo apt autoremove`. APT's unused-package list is system-wide, not a precise list of everything Steam added; `purge` also does not delete configuration or data in my home directory. [Ubuntu APT manual](https://manpages.ubuntu.com/manpages/noble/man8/apt.8.html) · [Simulation documentation](https://manpages.ubuntu.com/manpages/noble/man8/apt-get.8.html)

## I would handle user data separately

I would inspect these locations, including whether they are symlinks and whether they lead to another library:

```bash
ls -ld -- "$HOME/.steam" "$HOME/.local/share/Steam"
readlink -f -- "$HOME/.steam"
readlink -f -- "$HOME/.local/share/Steam"
```

Missing paths are possible. I would inspect additional libraries in Steam's Storage settings before uninstalling, and make my backups first.

**The next commands permanently delete data from the named locations.** I would run them only if I wanted to discard the Steam installation, downloaded games and Proton environments stored there, including any local saves within them:

```bash
rm -rf -- "$HOME/.steam" "$HOME/.local/share/Steam"
rm -f -- "$HOME/.steampath" "$HOME/.steampid"
```

I would run those as my ordinary user, not with `sudo`. I would not claim that they remove libraries on other drives or every game save stored elsewhere. Symlinks and non-default storage make inspection important.

## I would decide about i386

I initially wondered why I would leave `i386` enabled after removing Steam. I learned to distinguish a registered package architecture from the actual installed packages for that architecture. `dpkg` treats additional architectures as a system-wide configuration, not Steam-owned state. [Ubuntu dpkg manual](https://manpages.ubuntu.com/manpages/noble/man1/dpkg.1.html)

I would check:

```bash
dpkg --print-foreign-architectures
dpkg -l | grep ':i386'
```

If other applications still needed 32-bit libraries, I would retain them. If `i386` was only added for this installation, I no longer needed it, and no package records still required it, I could remove the registration:

```bash
sudo dpkg --remove-architecture i386
sudo apt update
```

I would not purge all `:i386` packages blindly or use `--force-architecture`. `dpkg` refuses architecture removal when it is still in use in its database; I would resolve the remaining records rather than bypass that check. [Ubuntu dpkg manual](https://manpages.ubuntu.com/manpages/noble/man1/dpkg.1.html)

I do not have a pre-installation inventory proving that enabling `i386` was part of this installation. Keeping or removing it is a separate decision, not a mandatory Steam uninstall step.

## I would verify the result

```bash
command -v steam
dpkg -l | grep -E '^ii[[:space:]]+steam'
sudo aa-status | grep -i steam
sysctl kernel.apparmor_restrict_unprivileged_userns
```

For the recorded installation, I would expect no Steam launcher or installed Steam package matches after a complete removal, and no remaining custom Steam profile. The global namespace value should still be `1`, as it was throughout my session.

I would investigate any unexpected output instead of deleting unrelated files to make the checks look clean. This plan reverses known changes; it does not claim to restore the exact pre-installation state of every package.

Next: [I reflected on the learning curve](09-what-i-learned.md).
