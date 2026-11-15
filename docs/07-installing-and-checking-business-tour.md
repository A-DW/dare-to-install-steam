# I wanted to try Business Tour

Business Tour was the game I asked about after getting Steam to launch. This chapter records my intended installation and test procedure, not a gameplay result I have already demonstrated.

## I checked the platform distinction

The Steam store lists Windows and macOS requirements, rather than a native Linux version. My Ubuntu test would therefore concern the Windows version under Proton. [Business Tour on Steam](https://store.steampowered.com/app/397900/Business_Tour_Board_Game_with_Online_Multiplayer/)

I am not preserving the earlier conversational claim of a particular ProtonDB rating as a verified result. Community ratings can change, and I did not personally test the game in the supplied session.

## I would install through Steam

1. I would sign in and find Business Tour in the Steam store.
2. I would add it to my library and inspect the installation options.
3. If needed, I would open the game's **Properties → Compatibility** and enable **Force the use of a specific Steam Play compatibility tool**.
4. I would start with a current Steam-provided stable Proton version and record the exact version I used.
5. I would install and launch the game, testing another Proton version only as a documented change rather than changing several things at once.

I expect labels to evolve with Steam client updates. Valve recommends Steam-provided Proton for most users and documents its integration with the client. [Proton project](https://github.com/ValveSoftware/Proton)

## I would locate the files

For my recorded Steam data location, the expected default game-files directory is:

```text
~/.steam/debian-installation/steamapps/common/
```

I would verify the actual directory through **Library → Business Tour → Properties → Installed Files → Browse**, not guess the game's folder name. If I selected another library, I would use that library's path instead. Steam's **Settings → Storage** manages library locations. [Steam storage documentation](https://help.steampowered.com/en/faqs/view/4BD4-4528-6B2E-8327)

Business Tour's application ID is `397900`, visible in its [store address](https://store.steampowered.com/app/397900/Business_Tour_Board_Game_with_Online_Multiplayer/). If Proton creates a prefix for this game, I would expect it under:

```text
<SteamLibrary>/steamapps/compatdata/397900/pfx/
```

I derived that path from Valve's [per-game prefix convention](https://github.com/ValveSoftware/Proton/wiki/Proton-FAQ/74cc0a33c45974ce20be376061a63e02905976bf); I have not observed that directory on this machine.

Next: [I planned the rollback](08-rollback-plan.md).
