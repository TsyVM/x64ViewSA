<div align="center">

<h1>x64ViewSA</h1>

<p><em>Grand Theft Auto: San Andreas, drawn by a 64-bit renderer</em></p>

[![Status](https://img.shields.io/badge/Status-in%20development-135A31?style=for-the-badge&labelColor=0F1A0F)](#-status-what-is-proven-and-what-is-not)
[![Windows](https://img.shields.io/badge/Windows-x86%20%E2%86%92%20x64-135A31?style=for-the-badge&labelColor=0F1A0F&logo=windows&logoColor=white)](#-how-it-works)
[![Direct3D 9](https://img.shields.io/badge/Direct3D-9%20bridge-135A31?style=for-the-badge&labelColor=0F1A0F)](#-how-it-works)
[![TeamVanilla](https://img.shields.io/badge/Team-TeamVanilla-135A31?style=for-the-badge&labelColor=0F1A0F)](https://www.teamvanilla.org/)

</div>

<img width="100%" src="https://capsule-render.vercel.app/api?type=rect&color=0:0F1A0F,50:135A31,100:0F1A0F&height=3"/>

x64ViewSA is a **bridge from 32-bit to 64-bit, and nothing else**. San Andreas
is a 32-bit program, and a 32-bit program can only load 32-bit DLLs, so every
renderer for it has had to be 32-bit too. x64ViewSA carries the game's
Direct3D 9 calls out of the game into a separate 64-bit process, where a
64-bit renderer draws them into the game's own window.

It doesn't draw anything itself and has no effects or upscalers. Those belong
to **SAFX12**, which, once it can run as 64-bit behind this bridge, can load
the 64-bit-only upscalers: DLSS for NVIDIA, FSR 3.1/4 for AMD, XeSS for Intel
(and XeSS and FSR for everyone).

```
gta_sa.exe  (32-bit)                         x64ViewSA.exe  (64-bit)
   │  Direct3D 9                                ▲
   ▼                                            │  Direct3D 9, replayed
x64ViewSA client  (d3d9.dll, 32-bit) ═══════════╛
   shared memory                                │
                                                ▼
                         a 64-bit d3d9.dll: SAFX12 x64, or Windows' own
                                                │  Direct3D 12
                                                ▼
                                          DXGI ──► GPU ──► the game's window
```

<div align="center">

### Contents

[Status](#-status-what-is-proven-and-what-is-not) · [How it works](#-how-it-works) · [Why bother](#-why-bother) · [Try the test tools](#-try-the-test-tools) · [Configuration](#%EF%B8%8F-configuration-planned) · [Compatibility](#-compatibility) · [Documents](#-documents) · [License](#-license)

</div>

<img width="100%" src="https://capsule-render.vercel.app/api?type=rect&color=0:0F1A0F,50:135A31,100:0F1A0F&height=3"/>

So, plainly:

- **Proven:** a 64-bit process can draw into the 32-bit game's window in every
  display mode; the game never reads data back from the GPU during normal
  frames, so the bridge never has to stall waiting for the 64-bit side; and the
  data to carry is small (about 250 KB and 12,600 calls per frame on average).
- **Provisionally proven:** moving the renderer off the game's thread is worth
  it. With SAFX12, at least 5.1 ms of every 16.2 ms frame is renderer work
  done on the game's thread.

<img width="100%" src="https://capsule-render.vercel.app/api?type=rect&color=0:0F1A0F,50:135A31,100:0F1A0F&height=3"/>

## 🔧 How it works

- **Client (32-bit `d3d9.dll`, next to `gta_sa.exe`).** Looks like Direct3D 9 to
  the game, but has no GPU behind it. It answers what it can on its own (device
  status, settings the game reads back) and writes every other call into
  shared memory.
- **Server (`x64ViewSA.exe`, 64-bit).** Started by the client. It reads the calls
  back and replays them into whichever 64-bit Direct3D 9 renderer is configured,
  which draws straight into the game's window.
- **The renderer is swappable.** Windows' own 64-bit `d3d9.dll` is used to test
  the bridge: if anything looks different there, the bridge is at fault. SAFX12's
  64-bit build is the renderer for actual play.
- **Side channels.** Renderers that need more than Direct3D 9 (SAFX12 reads
  weather, camera and which part of the frame is being drawn) send it as sealed
  messages through the bridge, which keeps them in order with the draws and
  never reads them. Mouse and keyboard input for the renderer's menu travels the
  same way.
- **Fails loudly.** If the 64-bit side crashes, the game says so and names the
  log file, instead of leaving a black window.

<img width="100%" src="https://capsule-render.vercel.app/api?type=rect&color=0:0F1A0F,50:135A31,100:0F1A0F&height=3"/>

## ⚡ Why bother

Being 64-bit on its own doesn't make anything faster. What the bridge gives:

1. **Access to 64-bit-only SDKs** for SAFX12: DLSS, FSR 3.1 and 4, XeSS, frame
   generation and low-latency modes. None of them can load into a 32-bit game.
2. **Work off the game's thread.** Today the renderer's work happens on the
   game's own thread. Behind the bridge it runs on another core, in parallel.
3. **Room.** The renderer stops sharing the game's 4 GB of address space with
   SA-MP and every mod.

<img width="100%" src="https://capsule-render.vercel.app/api?type=rect&color=0:0F1A0F,50:135A31,100:0F1A0F&height=3"/>

## ⚙️ Configuration

```ini
; x64ViewSA.ini
[Renderer]
Path=renderer64\d3d9.dll   ; the 64-bit renderer; empty = Windows' own x64 d3d9.dll

[Debug]
Capture=0                  ; 1 records the game's calls to a file for replay
CaptureSeconds=60
```

Logs will be `x64ViewSA\client.log` (32-bit side) and `x64ViewSA\server.log`
(64-bit side).

<img width="100%" src="https://capsule-render.vercel.app/api?type=rect&color=0:0F1A0F,50:135A31,100:0F1A0F&height=3"/>

## 🧩 Compatibility

- **SA-MP:** Yes.
- **Other `d3d9.dll` mods** can't be combined, for the same reason as SAon12:
  only one `d3d9.dll` sits next to the game.
- **Game version:** 1.0 executable only.

<img width="100%" src="https://capsule-render.vercel.app/api?type=rect&color=0:0F1A0F,50:135A31,100:0F1A0F&height=3"/>

<img width="100%" src="https://capsule-render.vercel.app/api?type=rect&color=0:0F1A0F,50:135A31,100:0F1A0F&height=3"/>

<div align="center">

<sub>Built and maintained by <a href="https://github.com/TsyVM">TsyVM</a> · <a href="https://www.teamvanilla.org/">TeamVanilla</a></sub>

<img width="100%" src="https://capsule-render.vercel.app/api?type=waving&color=0:135A31,100:0F1A0F&height=80&section=footer"/>

</div>
