# Silent Hill 3: Rebirth

A modern platform layer for the 2003 PC release of **Silent Hill 3**: better controller support,
Vulkan rendering, a selectable frame rate, widescreen, an optional over-the-shoulder camera and a
long list of fixes. It also includes an installer that builds a working game folder from your own copy.

The game logic is left untouched. The mods replace the parts around it (input, timing, rendering and
file paths) and patch a handful of known bugs. Everything was reverse engineered from the game
executable;

It replaces **Steam006's Silent Hill 3 PC Fix**.

## Install

1. Go to [abandonware](https://www.myabandonware.com/game/silent-hill-3-bge) and download these 2 files:
   <img width="511" height="56" alt="image" src="https://github.com/user-attachments/assets/9263157d-e869-45b7-ba1d-47eef11fe55e" />
   <img width="423" height="62" alt="image" src="https://github.com/user-attachments/assets/52cd58c7-065f-4bad-92a8-62d1cfa471b9" />


2. Download **Silent.Hill.3.Rebirth.exe** from the [latest release](https://github.com/IvanBarbosaFilho/Silent-Hill-3-Rebirth/releases/latest) and run it.
   > Windows may show *"Windows protected your PC"* because the .exe is not signed: click **More info → Run anyway**.
3. Fill in the installer:
   - **Game folder:** where you want the game to be installed.
   - **Game disc:** the .zip you downloaded in step 1.
   - **sh3.exe:** the .exe from the NoCD zip you downloaded in step 1.
   - **Renderer:** Vulkan for newer PCs (keep your GPU drivers updated), DirectX 9 for older ones. The installer picks one for you.
4. Press **Install**, then start `sh3.exe` from the game folder.

Run the installer again at any time to change the settings.

## Features

**Controller** (`dinput8.dll` + SDL3)
- Works with modern controls, including hotplug.
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


## Settings

Run `Silent Hill 3 Rebirth.exe` again at any time. It remembers the game folder, and its tabs (Display, FPS,
Graphics, Controller, Button mapping, Camera, Game) edit `sh3_input.ini` and `dxvk.conf`.
You can also edit `sh3_input.ini` by hand; every option is commented. In game, **F9** reloads the
camera and frame-rate settings.

| In game | |
|---|---|
| R3 / V | toggle the over-the-shoulder camera |
| Right stick | move the over-the-shoulder camera |
| F9 | reload camera and FPS settings |

## Credits

- **Steam006**: Silent Hill 3 PC Fix, the reference for most fixes. The English text corrections were
  recovered from its code.
- **ThirteenAG**: WidescreenFixesPack (MIT), the reference for the widescreen and effect patches.
- **crosire**: d3d8to9. **Philip Rebohle and contributors**: DXVK. **SDL** contributors.
- The PCGamingWiki Silent Hill 3 sound fix (`fixwavs.bat`), reimplemented in the installer.

Licenses of the bundled components are in [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md).
Silent Hill is a trademark of Konami. This project contains no game files and is not affiliated with
Konami.
