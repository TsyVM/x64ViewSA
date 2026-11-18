<div align="center">

<h1>x64ViewSA</h1>

<p><em>Grand Theft Auto: San Andreas, drawn by a 64-bit program</em></p>

[![Status](https://img.shields.io/badge/Status-pre--release-135A31?style=for-the-badge&labelColor=0F1A0F)](#-status)
[![Windows](https://img.shields.io/badge/Windows-10%20%7C%2011%20(64--bit)-135A31?style=for-the-badge&labelColor=0F1A0F&logo=windows&logoColor=white)](#-requirements)
[![Direct3D 9](https://img.shields.io/badge/Direct3D-9-135A31?style=for-the-badge&labelColor=0F1A0F)](#-what-it-is)
[![TeamVanilla](https://img.shields.io/badge/Team-TeamVanilla-135A31?style=for-the-badge&labelColor=0F1A0F)](https://www.teamvanilla.org/)

</div>

<img width="100%" src="https://capsule-render.vercel.app/api?type=rect&color=0:0F1A0F,50:135A31,100:0F1A0F&height=3"/>

x64ViewSA is an **optional add-on for Grand Theft Auto: San Andreas** that moves
the game's graphics work out of the game and into a separate **64-bit** program.
You still play the same game, with the same saves, mods and controls. What
changes is *which program does the drawing*.

It is a stand-alone, drop-in add-on: two files next to `gta_sa.exe`, no changes
to the game's files, and removing it puts everything back exactly as it was.

> [!NOTE]
> **The source code is not released.** This page describes what x64ViewSA is,
> why it exists and how to use it. It deliberately does not describe how it
> works inside.

<div align="center">

### Contents

[What it is](#-what-it-is) · [Why it exists](#-why-it-exists) · [What you get, and what you don't](#-what-you-get-and-what-you-dont) · [Status](#-status) · [Requirements](#-requirements) · [Install](#-install-and-uninstall) · [Settings](#%EF%B8%8F-settings) · [Compatibility](#-compatibility) · [Troubleshooting](#-troubleshooting) · [FAQ](#-faq) · [Known limits](#-known-limits) · [License](#-license)

</div>

<img width="100%" src="https://capsule-render.vercel.app/api?type=rect&color=0:0F1A0F,50:135A31,100:0F1A0F&height=3"/>

## 🎮 What it is

Grand Theft Auto: San Andreas is a **32-bit** program, released in 2004. That
has one consequence that matters for graphics: a 32-bit program can only use
32-bit code, so every graphics add-on for the game has had to be 32-bit too,
squeezed into the same limited memory as the game, its mods and multiplayer
clients.

x64ViewSA changes who does the drawing:

- **The game stays exactly what it is: a 32-bit program.** It still runs the
  world, the missions, input, audio, CLEO scripts and every other mod.
- **A small 64-bit helper program, `x64ViewSA.exe`, draws what the game shows**
  and puts the picture in the game's own window.
- To the game, nothing looks different. To you, the game window looks and
  behaves as usual.

When the game is running you will see two programs in Task Manager: `gta_sa.exe`
(32-bit, the game) and `x64ViewSA.exe` (64-bit, the helper). The helper has no
window of its own, which is why only the game shows when you alt-tab. In Task
Manager, open the **Details** tab and enable the **Architecture** column to see
which is which.

<img width="100%" src="https://capsule-render.vercel.app/api?type=rect&color=0:0F1A0F,50:135A31,100:0F1A0F&height=3"/>

## 💡 Why it exists

The 32-bit limit has become the main obstacle for anyone who wants to improve
how San Andreas looks and runs:

1. **Modern graphics software is 64-bit only.** Today's upscalers, frame
   generators and many graphics tools ship only as 64-bit code. A 32-bit game
   cannot load them at all, whatever hardware you own. x64ViewSA is the
   missing doorway: a place where 64-bit graphics code can serve a 32-bit game.
2. **Room to breathe.** A 32-bit game has about 4 GB of address space in total,
   shared between the game, its streamed world, the multiplayer client and
   every mod. Graphics work that lives in another program stops competing for
   that space.
3. **Work moves off the game's main thread.** Drawing the frame is part of
   what the game's main thread does today. With a helper doing it, that work
   can happen on another processor core while the game prepares the next frame.
4. **It is optional and reversible.** The point is to give the game a way to use
   64-bit graphics without replacing the game or asking anyone to give up their
   mods. If you don't want it, you remove two files.

x64ViewSA is deliberately **only** this doorway. It adds no effects, filters,
upscaling or other visual changes of its own; those belong to whichever
graphics add-ons you choose to use.

<img width="100%" src="https://capsule-render.vercel.app/api?type=rect&color=0:0F1A0F,50:135A31,100:0F1A0F&height=3"/>

## ⚖️ What you get, and what you don't

**What you get**

- The game rendered by a 64-bit program, with the same image as before (see
  [Status](#-status) for how this was checked).
- A foundation for 64-bit graphics add-ons that could not exist for a 32-bit
  game before.
- A single switch (`Mode=Off`) that turns the whole thing off without removing it.

**What you should not expect**

- **It is not an FPS booster by itself.** Being 64-bit does not make the game
  draw faster. On the machine it has been tested on, at a very high setting
  (2560×1600 with 8× anti-aliasing), the game was limited by the graphics card
  and driver, and the frame rate was set by that, not by x64ViewSA. The
  benefit of moving work to another core only appears when the game is limited
  by the processor, not the graphics card.
- **It does not change how the game looks.** No new lighting, no upscaling, no
  sharpening. Same picture, different program drawing it.
- **It is not a fix for game bugs or crashes** that happen in the game itself.

<img width="100%" src="https://capsule-render.vercel.app/api?type=rect&color=0:0F1A0F,50:135A31,100:0F1A0F&height=3"/>

## 🔬 Status

**Pre-release.** x64ViewSA runs the game in play today, but it has not been
released and has had limited testing. Here is exactly what has and hasn't been
done:

| Area | State |
|---|---|
| Starts the game and draws it | **Works.** Play-tested through menus, intro and gameplay. |
| Image compared with the unmodified game | The drawn frames were compared with the game's own frames in controlled tests and matched pixel for pixel in all but one frame of the test set, which differed by at most 2 out of 255 in a handful of pixels. A full play-through comparison is the author's report that it looks the same. |
| Minimize and restore | **Works** (repeated, scripted and by hand). |
| Settings changes in game | **Works** per the author's testing. |
| Mods (ModLoader, CLEO, SilentPatch, widescreen and limit-adjuster mods and others on the author's install) | **Reported working**, unchanged. This is the author's report from one setup, not a formal test matrix. |
| Multiplayer (SA-MP) | **Not formally tested.** |
| Graphics cards | Tested on **one machine** only (Windows 11, Intel Arc 140V graphics). **NVIDIA and AMD have not been tested.** |
| Failure handling | If the helper stops unexpectedly the game shows a message naming the log files instead of leaving a blank window. |

Nothing here is a promise about your system. If you try it, please report what
you see (see [Troubleshooting](#-troubleshooting)).

<img width="100%" src="https://capsule-render.vercel.app/api?type=rect&color=0:0F1A0F,50:135A31,100:0F1A0F&height=3"/>

## 📋 Requirements

- **64-bit Windows 10 or 11.** (The helper is a 64-bit program; 32-bit Windows
  is not supported.)
- A graphics card and driver with **Direct3D 9** support. Anything that runs
  San Andreas today qualifies; see [Status](#-status) for what has actually been tested.
- **Game version:** the 1.0 executable of San Andreas (the same requirement as
  most mods for the game).
- Only **one** `d3d9.dll` can sit beside `gta_sa.exe`. If you already use another
  graphics add-on that is installed as `d3d9.dll`, the two cannot both be
  installed in the same place.

<img width="100%" src="https://capsule-render.vercel.app/api?type=rect&color=0:0F1A0F,50:135A31,100:0F1A0F&height=3"/>

## 📦 Install and uninstall

**Install** (with the game closed):

1. If there is already a `d3d9.dll` next to `gta_sa.exe`, rename it (for example
   `d3d9_old.dll`) so you can restore it later.
2. Copy these three files next to `gta_sa.exe`:
   - `d3d9.dll`
   - `x64ViewSA.exe`
   - `x64ViewSA.ini`
3. Start the game as you normally do.

If it worked, the game looks the same as always, and `x64ViewSA.exe` is visible
in Task Manager's **Details** tab while the game runs.

**Uninstall:**

1. Close the game.
2. Delete `d3d9.dll`, `x64ViewSA.exe` and `x64ViewSA.ini`.
3. Restore the `d3d9.dll` you renamed in step 1 of the install, if you had one.

Nothing else is changed on your computer. You can also delete the files the
add-on creates in the game folder: `x64ViewSA_server.log` and the
`x64ViewSA_capture` folder.

**Quick off switch:** set `Mode=Off` in `x64ViewSA.ini` to run the game without
the 64-bit helper while keeping everything installed.

<img width="100%" src="https://capsule-render.vercel.app/api?type=rect&color=0:0F1A0F,50:135A31,100:0F1A0F&height=3"/>

## ⚙️ Settings

Everything is in `x64ViewSA.ini`, next to `gta_sa.exe`:

```ini
[Bridge]
Mode=Live        ; Live = the game is drawn by the 64-bit helper (normal use)
                 ; Off  = the add-on does nothing; the game runs as if it were not installed

[Renderer]
Path=            ; advanced: leave empty
```

| Setting | Values | What it does |
|---|---|---|
| `Mode` | `Live` (default), `Off` | `Live` is normal use. `Off` turns the add-on off without uninstalling it. |
| `Renderer Path` | empty (default) | Leave empty to use Windows' own 64-bit graphics. Advanced users can name another 64-bit Direct3D 9 renderer here; this is meant for graphics-add-on authors, and nothing else needs it. |

Other settings exist for the author's testing. They are not needed and not
supported for normal use.

**Where the logs are** (both in the game folder):

- `x64ViewSA_server.log`: written by the 64-bit helper.
- `x64ViewSA_capture\live_<date>_<time>.log`: written by the game-side part. It
  ends with a short frame-time summary when you quit the game normally
  (**quit from the pause menu**, so the file is finalized).

<img width="100%" src="https://capsule-render.vercel.app/api?type=rect&color=0:0F1A0F,50:135A31,100:0F1A0F&height=3"/>

## 🧩 Compatibility

- **Mods:** reported working unchanged on the author's install, including
  ModLoader, CLEO, SilentPatch, widescreen and limit-adjuster mods and several
  vehicle and lighting mods. This is a report from one setup, not a guarantee
  for every combination.
- **Multiplayer (SA-MP):** not formally tested yet.
- **Other graphics add-ons installed as `d3d9.dll`:** cannot share the same
  folder position with x64ViewSA (only one file can have that name). Graphics
  add-ons designed for it can use x64ViewSA as their 64-bit doorway.
- **Texture and streaming helpers** that run as their own programs should not
  conflict, but this has not been tested.
- **Game version:** 1.0 executable only.
- **Fullscreen:** the game's fullscreen setting is shown as a borderless,
  monitor-sized window rather than exclusive fullscreen. Alt-tab and
  minimize/restore behave like any normal window.
- **Antivirus:** the game starts a second program (`x64ViewSA.exe`). Some security
  software may ask about this or flag it, because games rarely start helpers.
  The files are not yet code-signed. If your antivirus complains, the safe
  answer is to check where you downloaded the files from.

<img width="100%" src="https://capsule-render.vercel.app/api?type=rect&color=0:0F1A0F,50:135A31,100:0F1A0F&height=3"/>

## 🩺 Troubleshooting

| What you see | What to try |
|---|---|
| Nothing changes, game looks the same | That is the expected result. Check Task Manager's **Details** tab: `x64ViewSA.exe` should be running while the game runs. |
| The game window stays blank or black | Close the game. Set `Mode=Off` in `x64ViewSA.ini` and start it: if the game works then, the add-on is the cause. Keep the two log files (see [Settings](#%EF%B8%8F-settings)) and report it. |
| A message says the 64-bit renderer stopped | The helper program ended unexpectedly. The game closes safely. The message names the logs; please report them. |
| The game does not start at all | Rename or remove any other `d3d9.dll` beside `gta_sa.exe` that is not x64ViewSA's, make sure `x64ViewSA.exe` and `x64ViewSA.ini` are beside the game, and make sure Windows is 64-bit. |
| Security software blocks it | See [Antivirus](#-compatibility) above. Allow the two files only if you trust where you got them. |
| Lower frame rate than without it | Compare with `Mode=Off` at the same settings. At very high resolutions or anti-aliasing the graphics card is the limit either way. |

**When reporting a problem, please include:** your Windows version, graphics card
and driver version, whether you use SA-MP and which other mods, what you were
doing, and the two log files.

<img width="100%" src="https://capsule-render.vercel.app/api?type=rect&color=0:0F1A0F,50:135A31,100:0F1A0F&height=3"/>

## ❓ FAQ

**Is the game 64-bit now?**
No. `gta_sa.exe` is and stays a 32-bit program. What you *see* on screen is drawn
by the separate 64-bit helper, `x64ViewSA.exe`, which puts the picture into the
game's window.

**Will it make my game faster?**
Not by itself. See [What you get, and what you don't](#️-what-you-get-and-what-you-dont).

**Does it change how the game looks?**
No. It adds no effects of its own.

**Why does it need a second program?**
Because a 32-bit program cannot load 64-bit code, the 64-bit part has to be a
separate program. That is the whole idea of the add-on.

**Does it change my game files or saves?**
No. It adds files next to `gta_sa.exe` and creates log files. Removing the files
puts everything back.

**Can I use it with my other mods?**
It has been reported working with the mods on the author's install (see
[Compatibility](#-compatibility)), but that is one setup. Keep a backup of your
game folder, as you should with any mod.

**Does it work with SA-MP?**
Not formally tested yet.

**Does it work on NVIDIA / AMD graphics cards?**
It is designed for all of them and does not rely on any one vendor's features,
but it has only been tested on Intel Arc graphics so far.

**Will the source code be released?**
No. The source code is not being released.

**Is it safe?**
It is a game add-on that starts a helper program. It does not connect to the
internet, and it does not touch files outside the game folder other than its own
log files. As with any program you download, only use copies from the author or
TeamVanilla.

<img width="100%" src="https://capsule-render.vercel.app/api?type=rect&color=0:0F1A0F,50:135A31,100:0F1A0F&height=3"/>

## 🚧 Known limits

- Tested on one computer; other hardware may behave differently.
- Fullscreen is presented as a borderless window, not exclusive fullscreen.
- Multiplayer (SA-MP) is not formally tested.
- Not code-signed yet.
- The 1.0 game executable only.
- Frame rate is not improved by it alone, and at very high settings is set by
  the graphics card.

<img width="100%" src="https://capsule-render.vercel.app/api?type=rect&color=0:0F1A0F,50:135A31,100:0F1A0F&height=3"/>

## 📄 License

x64ViewSA is **closed source**. The binaries are provided for use with Grand
Theft Auto: San Andreas on an as-is basis, with no warranty. Redistribution
terms have not been chosen yet; until they are, please do not redistribute
x64ViewSA without the author's permission. Grand Theft Auto: San Andreas is a
trademark and product of Rockstar Games; x64ViewSA is an unofficial fan project
and is not affiliated with or endorsed by Rockstar Games or Take-Two Interactive.

<div align="center">

<sub>Built and maintained by <a href="https://github.com/TsyVM">TsyVM</a> · <a href="https://www.teamvanilla.org/">TeamVanilla</a></sub>

<img width="100%" src="https://capsule-render.vercel.app/api?type=waving&color=0:135A31,100:0F1A0F&height=80&section=footer"/>

</div>
