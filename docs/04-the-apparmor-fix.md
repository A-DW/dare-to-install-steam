# I recorded my AppArmor exception

I created an application-specific profile after confirming that `/etc/apparmor.d/steam` was missing. The sequence below records what worked in my session; I would not apply it blindly to a different machine or overwrite a package-managed profile.

## I understood the trade-off

I used `flags=(unconfined)` with `userns`. This is an exception to the namespace restriction for the matched application, not a full confinement policy or a stronger sandbox around Steam. Ubuntu documents this mode for compatibility with applications requiring user namespaces. [Ubuntu 24.04 release notes](https://documentation.ubuntu.com/release-notes/24.04/)

I limited the attachment paths to the Steam launch paths shown below. I did not attach the exception to every shell, every `bwrap` executable, or every application on the system. I also did not audit Steam or its entire child-process tree; I only verified the processes shown in my output.

## I created the missing file

I closed Steam's error dialog and created:

```bash
sudo tee /etc/apparmor.d/steam >/dev/null <<'EOF'
abi <abi/4.0>,
include <tunables/global>

profile steam /usr/{lib/steam/bin_steam.sh,games/steam} flags=(unconfined) {
    userns,
    include if exists <local/steam>
}
EOF
```

I also preserved the exact contents in [configs/apparmor/steam](../configs/apparmor/steam).

If I revisit this fix, I will first inspect any current `/etc/apparmor.d/steam` and check whether a package owns it with `dpkg-query -S /etc/apparmor.d/steam`. I will stop and review conflicting or newer policy rather than overwrite it.

## I loaded the profile

```bash
sudo apparmor_parser -T -r /etc/apparmor.d/steam
```

The command returned without an error in my session. `-r` replaces the kernel profile definition and `-T` skips reading the cached version. [Ubuntu's apparmor_parser manual](https://manpages.ubuntu.com/manpages/noble/man8/apparmor_parser.8.html)

I then ran Steam without `sudo`:

```bash
steam
```

I reached the sign-in screen.

## I kept the global restriction

```bash
sysctl kernel.apparmor_restrict_unprivileged_userns
```

I still had:

```text
kernel.apparmor_restrict_unprivileged_userns = 1
```

I did not add a sysctl override, disable AppArmor globally, or change `bwrap` permissions. I documented my rollback separately instead of pairing the fix with an automatic removal script.

Next: [I verified the result](05-verifying-the-result.md).
