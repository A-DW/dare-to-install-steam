# I learned more than installation

Someone thought I couldn't install Steam on Ubuntu. I expected a short demonstration; I ended up with a troubleshooting exercise that was worth documenting.

## I learned to question wording

The error asked for user namespaces to be enabled. My settings said they were enabled, while the audit log showed the restricted operations. I learned to read an error as a clue rather than a complete diagnosis.

## I learned to check assumptions

I tried to reload a profile that did not exist. I also mistyped `/usr/games/steam` during a path check. Neither mistake was dramatic, but both reminded me that the next command should follow what my machine actually reports.

I received assistance during the investigation. I still wanted to understand the evidence and the security trade-off, not present a pasted command as expertise I had not earned.

## I learned to keep changes scoped

My working exception attached to Steam's launch paths, and I verified that the system-wide namespace restriction stayed enabled. I also learned not to describe an `unconfined` compatibility profile as a restrictive sandbox. [Ubuntu's description of the profile mode](https://documentation.ubuntu.com/release-notes/24.04/)

## I learned to separate outcomes

I got Steam to its sign-in screen. I researched how Proton could run a Windows game. I drafted a rollback plan. I did not merge those into a claim that I had already played Business Tour and restored my system afterward.

## I learned to plan the exit

I now distinguish package removal, user-data removal, policy cleanup and architecture registration. I would review each separately rather than run a broad cleanup command just because I was finished with one application.

## I would improve my record

Next time I would save a pre-installation package inventory, record graphics-driver details before game testing, note the exact Proton version, and test again after a reboot. I would redact screenshots before committing them and record unsuccessful attempts as well as the working change.

I proved I could get Steam running. More importantly, I learned how to explain why it had failed—and where my evidence stops.
