# I accepted the challenge

Someone thought I couldn't install Steam on Ubuntu. I wanted to prove otherwise, but I also wanted to understand what I was doing rather than collect commands I could not explain.

## My goal

I wanted to install Steam on Ubuntu 24.04, reach its interface, and investigate whether I could play Business Tour. Once I started changing system policy, I added another goal: I wanted a way back.

I followed [this installation video](https://www.youtube.com/watch?v=13ZXnyGQf_A). I am linking to the tutorial as my starting point, not reproducing it or claiming to have verified every instruction in it.

## My actual environment

I identified the installation from my terminal output:

```text
Ubuntu 24.04, 64-bit
/usr/games/steam
steam-installer 1:1.0.0.79~ds-2
No Steam Snap installed
```

I recorded the supporting output in [my package evidence](../evidence/logs/installed-steam-packages.txt). I did not save a complete pre-installation package inventory, so I cannot claim an exact restoration of every dependency later.

## My definition of success

I separated the outcomes instead of moving the goalposts:

| Outcome | My recorded status |
| --- | --- |
| Steam packages present | Observed |
| Steam reaches its sign-in screen | Observed |
| Application-specific profile loaded | Observed |
| Global namespace restriction still enabled | Observed |
| Business Tour download completed | Not demonstrated |
| Business Tour launch and gameplay | Not demonstrated |
| Rollback completed | Not demonstrated |

I would rather publish an honest partial test record than turn a successful launcher fix into an invented gameplay result.

Next: [I hit the first failure](02-installation-and-first-failure.md).
