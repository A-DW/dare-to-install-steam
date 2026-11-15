# I verified what actually worked

I reached Steam's sign-in screen after loading the custom profile. I also checked the policy state instead of treating an open window as the only evidence.

## I checked the loaded policy

```bash
sudo aa-status | grep -i steam
```

My output listed `steam` and processes running under that profile, including Steam, shells and `srt-logger`. I preserved the supplied lines in [successful-verification.txt](../evidence/logs/successful-verification.txt).

## I checked the system-wide setting

```bash
sysctl kernel.apparmor_restrict_unprivileged_userns
```

It still returned `1`. My application-specific exception had not required changing the global sysctl value.

I did not expect a general `unshare` invocation from an unrelated shell to become unrestricted just because Steam now had an exception. Ubuntu's permission model is application-profile-based; my success criterion was Steam's launch, not granting namespace capabilities to every process. [Ubuntu AppArmor documentation](https://documentation.ubuntu.com/security/security-features/privilege-restriction/apparmor/)

## I kept my claims narrow

| Check | My evidence |
| --- | --- |
| Profile parser completed | No reported parser error |
| Steam profile loaded | `aa-status` output |
| Processes under Steam profile | Supplied process list |
| Global restriction preserved | Sysctl value `1` |
| Steam sign-in screen reached | Observed in the session; screenshot not committed |
| Successful account sign-in | Not demonstrated |
| Game download or gameplay | Not demonstrated |
| Survives a reboot or package update | Not demonstrated |

I have not uploaded the original screenshots. I want to redact account information and sign-in QR codes before committing images; the screenshot folder contains [my review checklist](../evidence/screenshots/README.md), not pretend evidence files.

Next: [I learned what happens when I click Play](06-how-steam-runs-windows-games.md).
