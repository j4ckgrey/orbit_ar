# Orbit AR for INMO Air3

<div align="center">

![Orbit Banner](orbit.png)

[![Discord](https://img.shields.io/badge/Discord-Join%20Community-5865F2?style=for-the-badge&logo=discord&logoColor=white)](https://discord.gg/4yB8URK9s)

[📥 Install](#-installation) • [💿 Orbit AR ROM](#-orbit-ar-rom) • [✨ Key Features](#-features) • [🎮 Controls](#-controls-cheat-sheet) • [💬 Join Discord](https://discord.gg/4yB8URK9s)

---

</div>

> ⚠️ **Personal Use Disclaimer**  
> This build and its custom modifications are provided strictly for **personal use**. Anyone who chooses to install, run, or modify this release does so entirely **at their own risk and under their own responsibility**.

---

## 🚀 What is Orbit?

**Orbit** replaces INMO's closed, jittery stock "MultiSpace" with a powerful, high-performance GL composited spatial environment that **you control**. Live Android applications float seamlessly as multi-window quads anchored in 3D space around you with sub-pixel precision and ultra-low latency head tracking.

Whether you're multi-tasking with multiple apps floating in your field of view or switching to native ultra-low-power 2D mode on the go, Orbit turns your **INMO Air3 (IMA301)** into a true spatial workstation.

---

## 🧭 Two ways to run Orbit

| | **Orbit APK** | **Orbit AR ROM** |
| :--- | :--- | :--- |
| What it is | The spatial shell, installed on your existing system | A full custom ROM with the shell built in |
| Install | `adb install` | Signed A/B OTA, applied on-device |
| Bootloader | Any | See the table below |
| Reversible | Uninstall it | Reflash stock |
| Gets you | The launcher and camera | …plus debloat, the CPU-floor fix, patched framework, Orbit boot animation and an updater |

**New here? Start with the APK.** It is the whole spatial experience with nothing to undo. The ROM
is for people who want the rest of the system cleaned up too.

---

## ✨ Features

### 🪐 Dual Spatial Modes
* **Orbit Desktop (`SpacesActivity`)**: A full 3D spatial multi-window environment. Applications run in isolated VirtualDisplays, composited into floating 3D windows around your head.
* **Orbit Grid (`MainActivity`)**: A sleek, flat spatial launcher that opens apps with native 2D display performance and zero composition overhead.

### 🕶️ Advanced Head Tracking (2D / 3D / 6D)
Tailor tracking to your environment straight from the bottom control bar (⚙):
| Mode | How it Works | Best Used For |
| :--- | :--- | :--- |
| **2D** | Windows welded to display. Sensor disabled completely. | Walking, moving vehicles, max battery saving |
| **3D** *(Default)* | Yaw + Pitch tracking with a locked horizon. | Everyday spatial multi-tasking with zero roll-noise |
| **6D** | Full 6-DoF orientation (Yaw + Pitch + Roll). | Lying down or absolute world-locking |

### ⚡ Sub-Pixel Precision & Zero Vibration
* **Draw-Time Pose Extrapolation**: Eliminates motion swim by predicting head pose to exact display scanout timing.
* **Micro-Velocity Ring Buffering**: Smooths out timestamp jitter and eliminates screen shaking.
* **Pixel Snapping**: Locks static windows to physical screen pixels for crystal-clear text readability.

### 🧊 Ice Cold Thermal & Battery Efficiency
Orbit fixes the severe thermal throttling and battery drain of stock launcher software:
* **Background App Freeze**: Background windows are automatically stopped (`STATE_OFF`) and process-frozen via cgroup freezer (0% CPU usage for inactive windows while preserving app state).
* **On-Demand GL Rendering**: Compositor and drawer only repaint when movement or input occurs, dropping idle `SurfaceFlinger` CPU load from **54% down to ~7%** and keeping skin temperatures under **47°C** (no throttling!).
* **Smart Wear-Sensing Timeout**: Detects when glasses are taken off to allow full SoC deep sleep.

### 🎛️ Unified Floating Control Bar
A single, intelligent, auto-hiding bar along the bottom of your field of view puts total control at your fingertips:
* **App Switcher & Quick Drawer**: Instant single-tap switching between running apps.
* **Head-Grab (`✥`)**: Locks a window to your gaze direction—move your head to reposition windows effortlessly.
* **Head-Lock (`🔒`)**: Pins specific windows to your gaze frame of reference.
* **Recenter (`⌖`)**: One-tap spatial recentering of your workspace.
* **Window Controls**: Resize (`−`/`+`), Maximise (`⛶`), Back (`←`), and Close (`✕`).

### 🎮 Samsung Gear VR Controller Support
Orbit drives the Gear VR controller as a full pointer — **the platform cannot do this itself.** The controller carries its HID characteristics under a non-standard GATT service (`0x1879`, not `0x1812`), so Android's `HidHostService` walks straight past it, creates no `uhid` node, and never makes an input device out of it. Orbit talks to it directly over GATT and turns it into a cursor.

* **Touchpad as a trackpad** — thumb movement moves the cursor, with no drift and no calibration.
* **Trigger** = left click and drag · **Pad click** = long press (context menu) · **Back / Home / Volume** work as labelled.
* **Screen-off aware** — the link is dropped when the display sleeps, so a connected controller can't hold the SoC out of suspend.
* **Toggle in Settings → System** — off if you don't own one.

> ⚠️ **Known limitation:** the controller's firmware uses BLE *slave latency*, delivering ~6 samples in a burst every ~90 ms rather than evenly at 66 Hz. This is not configurable from the host — connection-parameter updates are accepted and then ignored by the device. Orbit replays each burst at display rate so movement stays continuous, but roughly 45–90 ms of input latency is imposed by the controller itself and cannot be removed.

### 📸 Orbit Camera — 16MP Stills & 4K Video
Ships with **Orbit Camera**, a purpose-built CameraX camera that unlocks the sensor's full modes. Installed automatically alongside the launcher; the stock INMO camera is replaced and Orbit Camera becomes the system default. **Open Camera is kept installed as a secondary/fallback.**

* **16 MP stills** (4608×3456) — this HAL hides its full-size modes behind `getHighResolutionOutputSizes()`, which ordinary camera apps never call.
* **4K 30 fps video** (3840×2160), with a one-tap **4K ⇄ FHD** switch. FHD is pinned to a constant 30 fps so exposure can't trade frame rate for light.
* **Aspect control** for stills — 4:3 (full sensor, all 15.9 MP), 16:9, 1:1.
* **HDR stills** via the sensor's bracketed-exposure scene mode.
* **Portrait UI** sized to the actual capture area — the camera is mounted rotated 90°, so an upright frame is portrait.

> **Hardware ceilings, measured on the device — these are not settings:**
> * **No 60 fps at any resolution.** The sensor advertises `[10,10] [10,15] [15,15] [24,24] [10,30] [30,30]` and publishes no high-speed video configurations at all.
> * **Video cannot exceed 4096×2160 (8.85 MP).** Both the H.264 and HEVC encoders are capped there, so 16 MP video is impossible on this SoC at any setting.
> * **No HDR video.** `DYNAMIC_RANGE_TEN_BIT` is absent from the camera's capabilities; HDR is stills-only.

### 🖥️ Remote Desktop & Wireless Display
* **Built-in RDP client** (FreeRDP 3.x, arm64) — use a PC desktop as a window inside your space. Off by default; enable in **Settings → System → Remote Desktop**.
* **Miracast sink** — receive a wireless display from Windows.

### 🛠️ Developer & Power Features
* **Fixed Wireless ADB**: Auto-enables ADB on a **fixed port (`5555`)** across reboots, instead of the random port wireless debugging negotiates each session.
* **Automatic MTP Storage**: Keeps USB file transfer alive when plugged into a PC — and verifies something is actually *serving* MTP, not merely that the gadget advertises it.
* **CPU Governor Optimizer**: Removes INMO's frequency clamp, which pins min *and* max to the same value, so the CPU can scale across its full **691 MHz – 1.8 GHz** range instead of being stuck.
* **Stray App Recovery** *(new in 0.2.0)*: Apps started outside Orbit — via ADB, a notification, or an intent chooser — become ordinary tasks the shell has never heard of and vanish from the launcher. Orbit now finds them and adopts them into the space, keeping their state.
* **No Root Required**: Signed with the platform certificate for full framework permission access out of the box.

---

## 🖼️ Screenshots

<div align="center">

| 3D Spatial Workspace | App Launcher Grid | Settings |
| :---: | :---: | :---: |
| ![Spatial Desktop](screenshot.png) | ![App Grid](screenshot2.png) | ![Controls](screenshot1.png) |

</div>

---

## 📥 Installation

### Option A — the APK, on your existing system
Grab the latest APK from the [Releases Page](../../releases) or build from source:

```bash
# Install via ADB
adb install -r -g Orbit.apk

# (Optional, ROOT ONLY) Bypass INMO's first-launch disclaimer.
# Orbit itself needs no root — this one convenience step does.
adb shell su -c 'cmd app_launch_guard add com.j4ckgrey.orbit'

# Launch Orbit Spatial Desktop
adb shell am start -n com.j4ckgrey.orbit/.SpacesActivity
```

### Option B — the full ROM
See [Orbit AR ROM](#-orbit-ar-rom) below. Install `OrbitUpdater.apk` from the
[Releases Page](../../releases) and let it pick the right package for your device.

---

## 💿 Orbit AR ROM

A complete, **signed A/B OTA** built from INMO's official V3.17.012 firmware. It installs the way
an official update does — `update_engine` verifies the payload signature, writes the **inactive
slot**, and the bootloader falls back on its own if the new build does not start.

### Which package?

Two are published with every release. **They are one build**; the ext4 filesystems are byte-identical.

| Package | Bootloader | Root | dm-verity |
| :--- | :--- | :--- | :--- |
| **`Orbit_AR_V<ver>_noRoot.zip`** | **locked or unlocked** | no | **on**, AVB chain re-signed |
| **`Orbit_AR_V<ver>_magisk.zip`** | unlocked only | yes, Magisk | off |

**Take `noRoot` unless you specifically want root.** It runs everywhere and leaves verified boot
intact. `magisk` keeps root and leaves `/system` writable, and needs an unlocked bootloader.

> ⚠️ **You do not have to choose by hand.** The built-in updater reads the bootloader state and
> downloads the package your device can actually boot. Picking wrong is not a visible error —
> `update_engine` installs either package on either device, and the wrong one only shows up as a
> failed boot and a rollback. If the state cannot be read it serves `noRoot`, which boots on both.

### Installing

1. **`adb install -r OrbitUpdater.apk`** — on a stock device it isn't there yet.
2. Open **System Update** → **Check for updates** → install.
3. Reboot when it asks.

The update URL is already compiled in; there is nothing to configure. Downloads resume if
interrupted, and the package is checked against its SHA-256 before anything is written.

### Is it safe?

* Nothing is written to the running system. The new build goes to the **inactive slot**.
* A failure at any stage — download, checksum, or write — aborts and leaves you exactly where you were.
* ⚠️ That automatic rollback lasts until the Virtual A/B snapshots **merge**, shortly after the new
  build boots successfully. After that, going back means reflashing. So if you don't like it, say so early.

### Verifying a download

Every release ships `SHA256SUMS`. Check before installing:

```bash
sha256sum -c SHA256SUMS
```

### What the ROM changes

**Added** — Orbit shell, Orbit Camera, Open Camera (fallback), Orbit Control Center, Orbit Updater,
the patched framework (display fit/shift, no third-party disclaimer, CPU clamp removed) and the
Orbit boot animation.

**Removed** — INMO launcher, mini launcher, log/telemetry service, Nexus Console, stock camera,
voice assistant and its wake-word engine, INMO's OTA client, Qualcomm QLog, Snapdragon Camera.

> INMO's own OTA client is removed deliberately: left in place it can pull a stock INMO build over
> the top of Orbit and undo all of this.

**The idle CPU floor is fixed at source.** INMO's perf HAL votes a per-CPU *minimum* frequency and
never releases it, so the last boost to fire becomes the idle floor — that is the heat, and why the
big cluster sat at 2.2 GHz doing nothing. All 85 min-freq votes are rewritten to the hardware
minimum. This costs no performance: a min-freq vote is a **floor, not a throttle**, and the max-freq
resources are untouched.

---

## 🎮 Controls Cheat Sheet

| Action | How To |
| :--- | :--- |
| **Reveal Control Bar** | Tap or look down toward the bottom edge |
| **Focus / Bring Window Forward** | Tap any window |
| **Move Window** | Click `✥` (Head-Grab), move gaze, click again to drop |
| **Recenter Workspace** | Tap `⌖` on the bottom bar |
| **Toggle App Drawer** | Double-tap empty space or press **Back** |
| **Scroll Inside App** | Swipe or use DPAD / Arrow keys / Scroll wheel |
| **Close Window** | Click `✕` or hold **Back** |
| **Move cursor** *(Gear VR)* | Swipe the controller touchpad |
| **Click / drag** *(Gear VR)* | Trigger |
| **Context menu** *(Gear VR)* | Press the touchpad in |
| **Back / Home / Volume** *(Gear VR)* | The buttons of the same name |

---

## 📋 Requirements

* **INMO Air3 (IMA301)** — Orbit is built against this device's panel, optics and sensors.
* **Platform-signed build** for full functionality. A release-signed APK still runs, but silently loses `ADD_TRUSTED_DISPLAY` (the IME can no longer render inside a window), `INJECT_EVENTS` (touch falls back to a slow shell path), `FORCE_STOP_PACKAGES` and auto-granted `SYSTEM_ALERT_WINDOW`.
* **No root required** for the APK.
* **For the ROM:** base firmware **V3.17.012**.

---

## 💬 Community & Support

Our community is growing fast! Whether you have feature requests, feedback, bug reports, or just want to showcase your INMO Air3 setup, join us on Discord!

<div align="center">

### 🗣️ [Join the Orbit Discord Community](https://discord.gg/4yB8URK9s)

*🐞 **Found a bug?** Open an issue on GitHub or drop a line in `#bug-reports` on Discord!*

---

</div>
If you like what I do, consider supporting future development https://ko-fi.com/j4ckgrey
<img width="1200" height="600" alt="image" src="https://github.com/user-attachments/assets/1e1ef242-3cbf-4900-b035-33bcda376f02" />
