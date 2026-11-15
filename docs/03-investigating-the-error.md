# I investigated instead of reinstalling

I broke the failure into questions: was the kernel feature enabled, could my ordinary user use it, and did the kernel log identify a restriction?

## I checked namespace settings

```bash
sysctl kernel.unprivileged_userns_clone \
       user.max_user_namespaces \
       kernel.apparmor_restrict_unprivileged_userns
```

I recorded:

```text
kernel.unprivileged_userns_clone = 1
user.max_user_namespaces = 25255
kernel.apparmor_restrict_unprivileged_userns = 1
```

I could rule out a disabled `unprivileged_userns_clone` setting or a zero namespace limit. That did not mean every operation within a namespace was permitted: Ubuntu documents AppArmor restrictions that can allow creation but deny subsequent capabilities. [Ubuntu 24.04 release notes](https://documentation.ubuntu.com/release-notes/24.04/)

## I compared privilege levels

```bash
unshare --user --map-root-user true
```

My ordinary-user test failed:

```text
unshare: write failed /proc/self/uid_map: Operation not permitted
```

My diagnostic comparison completed without output:

```bash
sudo unshare --user --map-root-user true
```

I did not take the successful root test as permission to run `sudo steam`. I used the comparison to narrow the investigation; the audit log supplied the explicit AppArmor evidence.

## I read the audit log

Immediately after a failed launch, I checked:

```bash
sudo journalctl -k -b --since "5 minutes ago" --no-pager \
  | grep -Ei 'apparmor|denied|userns|bwrap|steam'
```

I saw namespace creation transition from `unconfined` to `unprivileged_userns`, then denials involving `setpcap` and a write to `proc/.../uid_map`. Steam's bundled `srt-bwrap` and `/usr/bin/bwrap` both appeared in the supplied output.

I kept [sanitised excerpts](../evidence/logs/apparmor-denials.txt), including the denied UID-map write. Ubuntu's documentation explains why applications needing namespace permissions require an explicit AppArmor allowance. [Ubuntu security documentation](https://documentation.ubuntu.com/security/security-features/privilege-restriction/apparmor/)

## I discovered the missing profile

I tried:

```bash
sudo apparmor_parser -T -r /etc/apparmor.d/steam
```

The response was:

```text
File /etc/apparmor.d/steam not found, skipping...
```

I confirmed the absence with `sudo cat /etc/apparmor.d/steam`; `sudo aa-status | grep -i -C 3 steam` returned no Steam profile. A reload could not fix a file that was not there.

I also corrected a typo during the investigation. The launcher check was `readlink -f /usr/games/steam`, not `readlink -f user/games/steam`. I have not recorded a resolved target because no corrected output was supplied.

Next: [I recorded the exception that worked](04-the-apparmor-fix.md).
