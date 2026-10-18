# sh3-mods

A modern platform layer for the 2003 PC release of **Silent Hill 3**: better controller support,
Vulkan rendering, a selectable frame rate, widescreen, an optional over-the-shoulder camera and a
long list of fixes. It also includes an installer that builds a working game folder from your own copy.

The game logic is left untouched. The mods replace the parts around it (input, timing, rendering and
file paths) and patch a handful of known bugs. Everything was reverse engineered from the game
executable; the notes are in [`docs/`](docs).

It replaces **Steam006's Silent Hill 3 PC Fix**. Each of its runtime patches is mapped to an
equivalent in [`docs/pcfix.md`](docs/pcfix.md).

## Features

**Controller** (`dinput8.dll` + SDL3)
- Works with modern pads (Xbox, PlayStation, Switch Pro, 8BitDo...), including hotplug.
- **Vibration.** The PC port has the PS2 rumble code, but XInput pads never received it through
  DirectInput.
- Full button remapping, including back paddles.
- Configurable stick deadzone, with compensation for the game's own 25% deadzone.

**Frame rate**
- Frame limit options, or uncapped.
- On-time frames advance by exactly one period, so animations do not jitter.

**Graphics**
- Direct3D 8 → **Vulkan** through DXVK.
- Hor+ widescreen field of view; cutscenes can keep their 4:3 framing; the side bars are removed.
- Higher-resolution shadows, depth of field / otherworld effect, status screen and inventory.
- Original fog detail, a pixelation fix for full-screen effects and anisotropic filtering.
- Borderless window at the desktop resolution, a window, or fullscreen.

**Over-the-shoulder camera** 
- Toggle with **R3** or **V**.
- It only moves the camera: aiming and controls are unchanged. Cutscene and scripted cameras are
  never touched.

**Fixes**
- Loading hangs: the game used non-reentrant semaphore locks and waited on a `PulseEvent`'d timer.
- Options menu freeze; display settings reset after a crash ("safe mode").
- Bigger audio stream buffers, the sound pitch fix, and the restored missing dialogue sound.
- English text fixes (subway, apartment, missing sewer text), optional "Wish House", fullscreen
  pause menu, Silent Hill 2 easter eggs, and a pinned language.
- The game runs from any folder without its installer (registry paths answered from the game folder).

## Requirements

- **Your own Silent Hill 3 PC DVD**, as an `.iso` or a `.zip` containing it.
  Supported: the European disc (En/Fr/De/Es/It). Other discs need a manifest, see
  [`installer/README.md`](installer/README.md).
- **A DRM-free `sh3.exe`** of the original build (PE timestamp 2003-09-03). The exe on the disc uses
  SecuROM, which no longer runs on modern systems, and this project does not provide a replacement.
  All patches target this exact build, and the installer checks it.
- Windows 10/11, or Linux with Wine/Proton. A Vulkan-capable GPU.
- Optional: the PC Fix download, used only to take `Sounds/SH3_20016.mp3` for the missing dialogue
  sound.

## Install

1. Download `sh3-mods.exe` (on Linux, run it with Wine or Proton).
2. Pick the **game folder**, the **game disc** (.zip or .iso) and your **DRM-free sh3.exe**.
   Optionally pick the PC Fix zip.
3. Press **Install**. A full install takes about 30 seconds. It extracts the game straight from the
   disc image, applies the sound fix and adds the mods.
4. Start `sh3.exe`.

**Linux (Lutris / Steam / Proton):** set DLL overrides so the mod files load:
`WINEDLLOVERRIDES="d3d8,d3d9,dinput8=n,b"`. In Steam, put it in Launch Options followed by `%command%`.
In Lutris, use Runner options → DLL overrides: `d3d8`, `d3d9`, `dinput8` = `n,b`.

## Settings

Run `sh3-mods.exe` again at any time. It remembers the game folder, and its tabs (Display, FPS,
Graphics, Controller, Button mapping, Camera, Game) edit `sh3_input.ini` and `dxvk.conf`.
You can also edit `sh3_input.ini` by hand; every option is commented. In game, **F9** reloads the
camera and frame-rate settings.

| In game | |
|---|---|
| R3 / V | toggle the over-the-shoulder camera |
| Right stick | move the over-the-shoulder camera |
| F9 | reload camera and FPS settings |

## Building

Requires Nix, or MinGW-w64 (i686) plus SDL3 for Windows.

```sh
git clone --recurse-submodules <repo> && cd <repo>
nix-shell --run 'make -C input'        # dinput8.dll + SDL3.dll (all the game mods live here)
installer/build_payload.sh             # collects the mods + DXVK into installer/payload/
installer/build_exe_wine.sh            # sh3-mods.exe via Windows Python under Wine
                                       # (or installer/build_exe.sh on Windows)
```

`.github/workflows/build.yml` builds the DLLs and the Windows installer in CI.

## How it works

| Part | Where | Notes |
|---|---|---|
| Input proxy, virtual pad, rumble, remap | `input/src/{di_wrap,vdevice,gamepad}.cpp` | [docs/input.md](docs/input.md) |
| Frame limiter and time step | `input/src/fps.cpp` | [docs/fps.md](docs/fps.md) |
| Widescreen, FOV, window | `input/src/display.cpp` | |
| Effect resolutions, pixelation fix | `input/src/graphics.cpp` | [docs/graphics.md](docs/graphics.md) |
| Camera | `input/src/camera.cpp` | [docs/camera.md](docs/camera.md) |
| Hang fixes | `input/src/threads.cpp` | |
| Paths, disp.ini, text fixes, game options | `input/src/{paths,game}.cpp` | [docs/pcfix.md](docs/pcfix.md) |
| Renderer | DXVK (`d3d8/` = d3d8to9 fallback) | [docs/renderer.md](docs/renderer.md) |
| Installer / configurator | `installer/` | [installer/README.md](installer/README.md) |
| Reverse-engineering tools | `re/` | Ghidra scripts, live memory client (`mem.py`) |

Every patch checks the original bytes before writing. On an unexpected build it logs the problem
and skips the patch instead of corrupting the game.

## Credits

- **Steam006**: Silent Hill 3 PC Fix, the reference for most fixes. The English text corrections were
  recovered from its code.
- **ThirteenAG**: WidescreenFixesPack (MIT), the reference for the widescreen and effect patches.
- **crosire**: d3d8to9. **Philip Rebohle and contributors**: DXVK. **SDL** contributors.
- The PCGamingWiki Silent Hill 3 sound fix (`fixwavs.bat`), reimplemented in the installer.

Licenses of the bundled components are in [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md).
Silent Hill is a trademark of Konami. This project contains no game files and is not affiliated with
Konami.

## License

To be decided.
