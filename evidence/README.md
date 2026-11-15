# My evidence record

I transcribed these excerpts from the terminal output supplied during my troubleshooting session. They are not fresh commands executed on my machine by this repository, and they are not complete raw system logs.

I replaced usernames and home paths with `USER` and omitted hostnames and audit timestamps from the audit excerpts. I retained the supplied process IDs and technical values where useful. I did not invent the resolved launcher target, post-reboot checks, gameplay output, or rollback results.

| File | What I preserved |
| --- | --- |
| [steam-startup-error.txt](logs/steam-startup-error.txt) | Steam's runtime check and namespace error |
| [installed-steam-packages.txt](logs/installed-steam-packages.txt) | Launcher, package names and versions, and Snap check |
| [namespace-checks.txt](logs/namespace-checks.txt) | Sysctl values and ordinary-user/root diagnostic comparison |
| [apparmor-denials.txt](logs/apparmor-denials.txt) | Selected audit payloads, with identifying prefixes removed |
| [successful-verification.txt](logs/successful-verification.txt) | Missing-profile result, loaded-process list, and unchanged sysctl |

I have not committed original screenshots. The [screenshot guidance](screenshots/README.md) records what I need to review before adding them.

I do not treat a lack of output in a diagnostic command as general proof of system correctness; I interpret it alongside the rest of my recorded checks.
