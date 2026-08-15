# Orbit AR for INMO Air3

<div align="center">

![Orbit Banner](orbit.png)

[![Discord](https://img.shields.io/badge/Discord-Join%20Community-5865F2?style=for-the-badge&logo=discord&logoColor=white)](https://discord.gg/4yB8URK9s)

[Install](#installing) • [Features](#what-it-does) • [The bars](#the-two-bars) • [Controls](#controls) • [Discord](https://discord.gg/4yB8URK9s)

---

</div>

> **Personal use**
> This build and its modifications are for personal use. Anyone who installs, runs or modifies it
> does so at their own risk and on their own responsibility.

---

## What Orbit is

Orbit replaces the launcher on the INMO Air3 with a spatial shell. Apps open as windows placed
around you in the room, and your head aims the cursor. Everything else about the glasses stays as it
was — the same apps, the same Play Store, the same keyboard.

It comes as a system update. Install it and the glasses start in Orbit.

---

## What it does

### Apps as windows

Each app runs on its own private display and is composited into a scene you look around. Open
several, put them where you want them, and they stay there while you turn your head. A window can be
moved, resized, maximised, fixed in place, or left where the shell arranges it. Windows you are not
using are stopped and frozen, so a background app costs nothing while its content stays exactly as
you left it, and the compositor only redraws when something actually moves.

### Three tracking modes

The mode button on the top bar cycles them, and the choice sticks across reboots.

| Mode | What it does | When to use it |
| :--- | :--- | :--- |
| 2D | The space is welded to the display and the sensor is off. Apps run as ordinary fullscreen tasks with the real panel and real touch. | Walking, travelling, saving battery |
| 3D | Windows are anchored to the room and follow yaw and pitch, with the horizon held level. | Everyday use |
| 6D | As 3D, and the horizon tilts with your head. | Lying down, or when you want the space truly world-locked |

### Aiming and clicking

Your head moves the cursor; the temple touchpad or the INMO ring clicks. The pose is extrapolated to
the moment the frame is actually shown, so the scene does not lag behind your head, and a still
window is snapped to the pixel grid so text stays sharp.

A Samsung Gear VR controller works as a pointer if you have one. Android cannot drive this
controller at all — it carries its HID characteristics under a non-standard GATT service, so the
platform never turns it into an input device — so Orbit talks to it directly. The touchpad moves the
cursor, the trigger clicks and drags, pressing the pad in is a long press, and Back, Home and Volume
do what they say. The link drops when the display sleeps.

One limitation is the controller's own: its firmware delivers samples in bursts every ~90 ms rather
than evenly, and it ignores requests to change that. Orbit replays each burst at display rate so
movement stays smooth, but the latency the controller imposes cannot be removed.

### Camera

A camera that uses the whole sensor ships with the system and becomes the default, including for the
hardware camera key.

- 16 MP stills (4608×3456). The camera HAL hides its full-size modes behind an API ordinary camera
  apps never call.
- 4K 30 fps video, with a one-tap switch to FHD pinned at 30 fps.
- Aspect control for stills, and HDR through the sensor's bracketed-exposure mode.

Three hardware ceilings, measured on the device rather than assumed: there is no 60 fps at any
resolution, video cannot exceed 4096×2160 on this SoC's encoders, and HDR is stills-only.

### AR capture

Photograph or record what you are actually seeing — the camera's view of the room with the space
composited over it, the way it looks from inside the glasses.

### A PC as a window

Point Settings → Display → Remote display at a machine running Remote Desktop and it opens as a
window in the space, drawn at the window's own resolution.

The glasses can also advertise themselves as a wireless display (Miracast), so a phone or PC can
send a screen to them. That is off by default and lives in Settings → Orbit.

### Updates

A System Update app checks for new Orbit builds and installs them the way an Android update installs.

---

## The two bars

Both bars rest as a single hairline along their own edge. Point at that edge and the bar opens; look
away and it closes. Neither spans the display.

They are split by what a control acts on: the top bar acts on the device, the bottom bar acts on the
window you are focused on.

### Top bar

Nothing here depends on which window has focus, so it never greys out and never changes shape.

| Control | What it opens |
| :--- | :--- |
| Apps | The app drawer: everything installed, as a grid you look across |
| Running apps | What is open, each with a preview and an X. Clear all closes everything, and asks first |
| Mode | 2D, 3D or 6D |
| Notifications | Your notifications, read by Orbit itself. A media notification becomes a player with real transport controls |
| Control centre | Below |
| Clock and date | The calendar |

Wi-Fi, temperature and battery sit beside them as readouts.

The first four are **panels**: hover the button and the panel slides out from under the bar and docks
to it, sharing its edges. Move the pointer somewhere else and it closes again. Press instead of
hover and it stays until you dismiss it.

### Control centre

Volume and brightness as full-height columns, a player for whatever is playing, and tiles for AR
photo, AR video, screenshot, screen recording, camera, adaptive brightness, silent, do not disturb,
system settings and shutdown. CPU frequencies, CPU and GPU temperature and memory use run along the
bottom.

### Bottom bar

Recenter the view, fix the window in place, maximise it, make it smaller or bigger, go back, close
it, and step to the previous or next window. These all dim together when nothing is focused, because
none of them mean anything then.

### Per-window bar

Every window carries its own hairline along its top edge. Hover it and it opens with the app's name
and its own controls, so a window can be moved, resized or closed without going to the bottom bar.

---

## Settings

Orbit's settings live in the system Settings app rather than in a floating panel, so they are where
you would look for them and they appear in Settings search.

- **Settings → Orbit** — keep windows in colour, and the wireless display switch
- **Settings → Display → Display correction** — the field of view and projection fit for this panel
- **Settings → Display → Remote display** — the PC to connect to
- **Settings → Accessibility → Gear VR controller** — pair and enable one

---

## Screenshots

<div align="center">

| Spatial workspace | App drawer | Control centre |
| :---: | :---: | :---: |
| ![Spatial Desktop](screenshot.png) | ![App Grid](screenshot2.png) | ![Controls](screenshot1.png) |

</div>

---

## Installing

There is one package, and it installs on a locked or an unlocked bootloader. Verified boot stays on.

If the glasses are still on stock, install the updater once:

```bash
adb install -r OrbitUpdater.apk
```

Then open **System Update**, check for updates, and let it install. After that, every later version
arrives through the same app and there is nothing to install by hand again.

Nothing is written to the system you are running. The update goes to the slot the device is not
currently using, and if that slot fails to start the bootloader returns to the old one by itself.
A failure at any stage — download, checksum or write — leaves you where you were.

Worth knowing before you start:

- Your data is untouched. Accounts, the apps you installed and your settings all stay. An app you
  had updated from the Play Store keeps the version you updated to.
- The automatic rollback lasts until the new build has booted successfully and the snapshots merge.
  After that, going back means flashing stock again — so if it is not for you, decide early.
- If your device is rooted, the update replaces the boot image and root goes with it.

Every release ships checksums, if you would like to check the download first:

```bash
sha256sum -c SHA256SUMS
```

---

## What the system includes

**Added** — the Orbit shell, the camera, the control centre, the updater, a patched framework (the
display fit and shift, no third-party disclaimer, INMO's CPU clamp removed) and the Orbit boot
animation.

**Removed** — INMO's launcher and mini launcher, their log service, Nexus Console, app store, book
and gallery apps, stock camera, voice assistant and its wake-word engine, and their OTA client.

INMO's OTA client is removed deliberately: left in place it can pull a stock INMO build over the top
of Orbit and undo all of this.

Two things the build does rather than removes. Qualcomm's log capture app sits on a partition this
build does not write, so it is disabled instead, and the system's logging is turned down from the
several hundred lines a second it writes at idle. And the idle CPU floor is fixed at source: INMO's
performance HAL votes a per-CPU minimum frequency and never releases it, so the last boost to fire
became the floor and the big cluster sat at 2.2 GHz doing nothing. Those votes are rewritten to the
hardware minimum, which costs no performance — a minimum-frequency vote is a floor, not a throttle,
and the maximum is untouched.

---

## Controls

| Action | How |
| :--- | :--- |
| Show a bar | Look toward the top or bottom edge |
| Focus a window | Tap it |
| Move a window | Drag its own bar, or use the bottom bar |
| Recenter the space | The recenter button, on either bar |
| Open the app drawer | Home, or double-tap empty space |
| Scroll inside an app | Swipe, or the arrow keys |
| Close a window | The X on its bar |
| Move the cursor (Gear VR) | Swipe the touchpad |
| Click and drag (Gear VR) | Trigger |
| Long press (Gear VR) | Press the touchpad in |

---

## Requirements

- INMO Air3 (IMA301). Orbit is built against this device's panel, optics and sensors.
- Base firmware V3.17.012.
- No root, and no unlocked bootloader.

---

## Community

Feature requests, feedback, bug reports, or just showing what you have set up — all welcome.

<div align="center">

### [Join the Orbit Discord](https://discord.gg/4yB8URK9s)

Found a bug? Open an issue on GitHub, or post in `#bug-reports` on Discord.

---

</div>

If you would like to support further development: https://ko-fi.com/j4ckgrey

<img width="1200" height="600" alt="image" src="https://github.com/user-attachments/assets/1e1ef242-3cbf-4900-b035-33bcda376f02" />
