# VeloPad PC Client · User Manual (2.0.0)

The VeloPad PC client is the Windows half of VeloPad. It receives your phone's touches and motion over Wi-Fi or USB and turns them into a virtual **Xbox 360** or **DualShock 4** controller, or into mouse and keyboard input, so any PC game can be played from the VeloPad Android app.

This manual covers every page, dial, slider, switch and setting in **VeloPad 2.0.0** for Windows. It's written for **VeloPad 3.0.0** on Android; some features need that phone version, and they're marked where they appear.

---

## Table of Contents

1. [Installing VeloPad](#installing-velopad)
2. [Connecting Your Phone](#connecting-your-phone)
3. [The Main Window](#the-main-window)
4. [Dashboard](#dashboard)
5. [Profiles](#profiles)
6. [Layout Editor](#layout-editor)
7. [Input Lab: Overview](#input-lab-overview)
8. [Tuning Dials](#tuning-dials)
9. [Virtual Controller](#virtual-controller)
10. [Gyro Behaviour Modes](#gyro-behaviour-modes)
11. [Tilt Setup (Mode 3)](#tilt-setup-mode-3)
12. [Touchpad to Joystick Camera](#touchpad-to-joystick-camera)
13. [Response Curve](#response-curve)
14. [Axis Mapping](#axis-mapping)
15. [Analog WASD Slicing](#analog-wasd-slicing)
16. [Presets (Pro)](#presets-pro)
17. [Settings](#settings)
18. [Free vs Pro](#free-vs-pro)
19. [Files VeloPad Keeps](#files-velopad-keeps)
20. [Signal Processing Reference](#signal-processing-reference)
21. [Recommended Settings by Genre](#recommended-settings-by-genre)
22. [Troubleshooting](#troubleshooting)

---

## Installing VeloPad

### What you need

- A **Windows 10 or 11** PC (64-bit) where you can approve administrator prompts.
- An **Android phone** (Android 8.0 or newer, with a gyroscope) running **VeloPad 3.0** or newer.
- The phone and PC on the **same Wi-Fi network** (or the PC on the phone's hotspot), or a USB data cable for USB play (Pro).

### Step 1: Install the PC client

1. Open the [VeloPad releases page](https://github.com/Blue-Wave-Horizon/velopad-releases/releases) and download the **VeloPad_Installer** `.exe` from the latest release.
2. Run it. If Windows SmartScreen appears, click **More info > Run anyway**; approve the **User Account Control** prompt.
3. Follow the wizard. It installs VeloPad, installs the **ViGEmBus** virtual controller driver if you don't already have it, and can add a desktop shortcut.
4. VeloPad starts when setup finishes. After that, open it from the Start menu or desktop.

VeloPad asks for administrator rights each time it starts. It needs them to add its firewall rules and to send input to games reliably.

### Step 2: Install the phone app

Install **VeloPad** from [Google Play](https://play.google.com/store/apps/details?id=com.bwh.velopad). You can also click **Get the Android app** on VeloPad's Dashboard and scan the QR code with your phone's camera.

### Step 3: First launch

1. On first start, VeloPad adds Windows Firewall rules for **Private** networks. Make sure your network is set to **Private** in Windows (Settings > Network & internet > your Wi-Fi or Ethernet network > *Network profile type*). Phones can't connect over a **Public** network.
2. Open VeloPad on your phone, pick your PC on the **Home** tab, and tap **Play**. See [Connecting Your Phone](#connecting-your-phone).

### Optional: ADB for USB play (Pro)

USB play needs Google's free **ADB** tool, which isn't included with VeloPad.

1. Download **SDK Platform-Tools for Windows** from [developer.android.com/tools/releases/platform-tools](https://developer.android.com/tools/releases/platform-tools).
2. Unzip it so that `adb.exe` is at `C:\platform-tools\adb.exe`. VeloPad also finds `adb` on your PATH or in an Android Studio install (`%LOCALAPPDATA%\Android\Sdk\platform-tools\`).
3. Restart VeloPad.

### Optional: Interception driver

For games that ignore normal software keyboard and mouse input, VeloPad can use the Interception driver. It's optional and isn't installed by default; see [Keyboard &amp; mouse injection](#keyboard--mouse-injection) before installing it, because some anti-cheat systems block it.

### Updating

VeloPad checks for updates every time it starts. When one is available, click **Download update**, download the new installer from the release page, and run it over your current version. Your settings, presets and layouts are kept.

### Uninstalling

Uninstall **VeloPad** from Windows Settings > Apps. Your settings and layouts stay in `%APPDATA%\VeloPad` in case you reinstall; delete that folder to remove them. The ViGEmBus driver is listed separately in Apps and can be removed if no other program uses it.

---

## Connecting Your Phone

Keep VeloPad running on the PC, open VeloPad on the phone, and pick this PC from the phone's **Home** tab. The phone app's own manual has the phone-side steps in detail.

### Wi-Fi or hotspot

1. Put the phone and PC on the **same network**, or connect the PC to the phone's hotspot. A 5 GHz network gives the steadiest ping.
2. On the phone, choose **Wi-Fi / Hotspot**. The PC appears under its **discovery name** (your Windows computer name unless you change it in [Settings](#network--discovery)). Tap **Link**.
3. If the PC doesn't show up, type the PC's **LAN address** into the phone's *Or enter an address* field. The address is shown on the Dashboard's empty player card.

How discovery works: every 2 seconds the PC broadcasts its name on UDP port 4002, to the whole network and directly to each network's gateway address, so phone hotspots that drop broadcasts still see it. Input then arrives on UDP port 4000.

### USB (Pro on the phone)

USB gives a steady cable link with no Wi-Fi spikes. It's a VeloPad Pro feature on the phone.

1. Install Google's **ADB** tools on the PC (see the installation section). VeloPad looks for `adb` on the PATH, in `%LOCALAPPDATA%\Android\Sdk\platform-tools\` and in `C:\platform-tools\`.
2. On the phone, turn on **USB debugging** (Settings > About phone > tap *Build number* seven times, then Developer options > USB debugging).
3. Plug the phone in with a **data** cable and tap **Allow** on the phone's *Allow USB debugging?* prompt.
4. On the phone, choose **USB / ADB** and link to the PC shown as *USB · cable*.

VeloPad sets up the ADB tunnels itself and checks them every 3 seconds, so replugging the cable, restarting ADB or opening Android Studio doesn't break the link. Only phones on a cable are used: wireless ADB and emulators are ignored. If several phones are plugged in, the first one is used.

With VeloPad 3.0 on the phone, USB also carries **rumble** and the **ping** measurement through a "live link" that VeloPad keeps open to the phone.

> USB is steadier, not dramatically faster: it runs through ADB, so its main benefit is consistency.

### Two players

Up to **two phones** can connect at once, as **P1** and **P2**. Each gets its own virtual controller, its own Dashboard card and its own Input Lab settings, so two people can play local co-op.

- **Keyboard keys and mouse buttons** come from **P1 only**. P2 drives its own virtual controller. Gyro and touchpad mouse movement work from either phone.
- A phone that sends nothing for **5 seconds** is disconnected, and its held inputs are released.
- When both slots are full, a slot that has been silent for 3 seconds is handed to a new phone.

### Firewall and network type

On every start, VeloPad creates or refreshes three Windows Firewall rules (*VeloPad Discovery Beacon*, *VeloPad Receiver UDP*, *VeloPad Receiver TCP*) for **Private** networks. It uses UDP 4000 and 4002 and TCP 4001 to 4003; the ports are fixed.

- Your Wi-Fi must be set to **Private** in Windows (Settings > Network & internet > your network > *Network profile type*). On a **Public** network the rules don't apply and phones can't connect.
- Only phones on private (local network) addresses or USB are accepted.

---

## The Main Window

### Sidebar

| Part                                               | What it does                                                                                                                                                                                        |
| -------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **VeloPad · Mission Control**               | Brand header                                                                                                                                                                                        |
| **Dashboard, Profiles, Input Lab, Settings** | The four pages. Press **1**, **2**, **3** or **4** anywhere in the window to jump to them                                                                                   |
| **Collapse / expand**                        | Shrinks the sidebar to icons (64 px) or expands it (240 px)                                                                                                                                         |
| **Connected devices**                        | Each phone with its P1/P2 badge, connection type, live ping (coloured dot) and a **✕** to disconnect it. With exactly one phone, a battery bar shows too. With none: *Waiting for a phone* |
| **Receiver on &lt;name&gt;**                 | The name phones see for this PC                                                                                                                                                                     |

The window's minimum size is 1040 × 680 (default 1280 × 820), and VeloPad remembers its size and position. On Windows 11, the title bar is tinted to match the sidebar.

### Closing, the tray, and quitting

- The window's **close (✕)** button **hides VeloPad to the system tray**. It keeps running so your controller stays live in game. The first time, a notice says *VeloPad is still running*.
- **Tray icon:** animated chevrons with a red dot while searching; a still icon with a green dot when a phone is connected. Hover for *VeloPad: Searching...* or *VeloPad: Connected (&lt;names&gt;)*.
- **Tray menu:** **Show VeloPad**, **Hide to Tray**, **Quit**. Double-click the icon to show or hide the window.
- **Quit** from the tray is the only way to exit fully. It releases all virtual controllers.
- Starting VeloPad again while it's running replaces the old copy.

---

## Dashboard

*Live telemetry for every phone talking to this PC.*

The header shows **N of 2 players connected** (or *Waiting for phones*), a **virtual controller(s) active** count when two phones are in Gamepad mode, and a **What's new** button.

With one phone, its card is wide and an empty slot card sits beside it. With two phones, the cards share the width.

### Player card

**Header:** the P1/P2 badge, the phone's name, its link (*USB · ADB over TCP 4001* or *Wi-Fi · UDP 4000 · &lt;ip&gt;*), a **Pro** pill if that phone has VeloPad Pro, a **Connected** pill and a **✕** to disconnect.

**Stat cells:**

| Cell                     | What it shows                                                                                                                                                                                                                                                                                                     |
| ------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Latency**        | The real **round trip** from phone to PC and back, in ms, measured by the phone. Pill: **Good · under 15 ms** (green), **OK · 15–40 ms** (amber), **High · 40 ms+** (red). Shows *Measuring…* at first, and *Needs VeloPad 3.0 on the phone* if an older phone app sends no ping |
| **Active profile** | The layout the phone is using right now, with *N widgets mapped* if this PC has a copy, or *Not on this PC*                                                                                                                                                                                                   |
| **Input mode**     | **Gamepad**, with the controller and gyro mode (for example *Xbox 360 · Right stick · Decay*, *… · Gyro → mouse*, or *DualShock 4 · Native gyro*), or **MnK**, with the input method chosen in Settings (*Smart Auto*, *SendInput* or *Interception*)                               |
| **Packet rate**    | Packets per second (Hz), averaged over about a second. Footer: **Gap** (the smoothed time between packets, a measure of steadiness rather than latency) and the phone's **Battery** (and *charging*). Free phones below 400 Hz show a **Pro: every 2 ms** hint                                |

**Ping graph:** *Ping · last 60 seconds*, one point per second.

- Dashed guide lines at **15 ms** and **40 ms**; the scale always shows at least 0 to 50 ms.
- A faint dashed second line plots **packet gap**.
- The line takes the colour of the average's band. The right side shows **avg / p95 / max**.
- **Reset graph** clears it (wide card only).

**Tips** (Wi-Fi only):

- If the worst ping is 40 ms or more and at least 2.5 × the average: *Wi-Fi spiked to N ms · USB stays steady*.
- Otherwise, if ping isn't in the green band: *Tip: 5 GHz Wi-Fi or USB brings this down*.

**Buttons:** **Tune in Input Lab** opens Input Lab on this phone. **Switch profile** opens the Profiles page.

**Disconnect (✕):** releases held inputs, unplugs the virtual controller, saves that phone's Input Lab settings and frees the slot. If the phone is still streaming, it reconnects straight away, so ✕ works as a **reset**. To stop a phone, exit play on the phone.

### Empty slot card

- *Connect your phone* (no phones yet) or *Add a second phone* (local co-op gets its own virtual controller).
- **Discovery name**, **LAN address** (type this into the phone if discovery fails) and **Ports** (4000–4003).
- **Get the Android app** opens a pop-up with a **QR code**. Scan it with the phone's camera to open VeloPad on Google Play. **Open in browser** opens the same page on the PC; **Done** closes it.

---

## Profiles

*Controller layouts stored on this PC and on your phone.* Layouts are kept in `%APPDATA%\VeloPad\profiles\`, one `.vpd` file each.

### The list

- **Search** by name, and filter **All / On phone / This PC only**.
- Layouts are grouped into **On your phone** and **This PC only**, with the phone's active layout first. "On phone" means it was in the last sync from the phone, or it's the layout the phone is using now.
- **Default** is protected and can't be deleted (lock icon).

### The detail panel

- Name, status (*Active on &lt;phone&gt;* or *On phone*) and *N widgets · Gamepad/MnK · Edited &lt;time&gt;*.
- **Layout preview** at the phone's 2520 × 1080 landscape canvas. Hover a widget to see what it is.
- **Duplicate** makes *&lt;name&gt; copy* (then *copy 2*…).
- **Edit layout** opens the [Layout Editor](#layout-editor).
- **Upload to phone** (Pro) sends the layout to the phone and switches the phone to it.
- **Widgets** tile: a count per widget type.
- **Where it lives**: *Loaded on &lt;phone&gt; right now*, *Saved on your phone and this PC*, or *Only on this PC*.
- **Danger zone · Delete profile** removes it from this PC after you confirm. A copy on the phone isn't affected.

### Header buttons

- **New profile** asks for a name and opens the editor. If that name already exists, the existing profile opens.
- **Sync from phone** (Pro) downloads **every** layout from the phone. A phone layout **replaces** a PC layout with the same name.

Sync and upload need a connected phone; they use the first connected phone (P1 first). Pro is checked on that phone.

---

## Layout Editor

Design and fine-tune phone layouts with a mouse and keyboard. The sidebar collapses while you edit and comes back when you leave.

### Header and toolbar

| Control                       | What it does                                                                                                                  |
| ----------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| **← Back to profiles** | Leaves the editor. With unsaved changes you're asked to **Save**, **Keep editing** or **Discard**           |
| **Status**              | *● Unsaved changes*, *✓ Saved* or *✓ Saved and sent to phone*                                                        |
| **Undo / Redo**         | Up to 100 steps. Quick edits to the same field within a second count as one step                                              |
| **Add widget**          | Key button, Joystick, D-pad, Touchpad, Trackpad or Gyro toggle                                                                |
| **Save**                | Saves to this PC (Ctrl+S)                                                                                                     |
| **Save & upload** (Pro) | Saves, sends the layout to the phone and switches the phone to it. With no phone connected, it saves and tells you to connect |
| **Profile mode**        | **Gamepad** or **Mouse & keyboard** for the whole layout                                                          |
| **Snap to grid**        | On by default                                                                                                                 |

### Canvas

- The canvas is the phone's **2520 × 1080** landscape screen.
- **Drag** a widget to move it. It snaps to a **20-unit grid** and lines up with other widgets' edges and centres (pink guide lines). Hold **Alt** to place freely.
- **Drag a corner** to resize (also snapped). Round and square widgets stay square.
- **Arrow keys** nudge 4 units; **Shift + arrow** nudges 40. **Esc** deselects.
- New widgets appear in the centre, stepping down and right if that spot is taken.

### Keyboard shortcuts

| Keys             | Action    |
| ---------------- | --------- |
| Ctrl+Z           | Undo      |
| Ctrl+Y           | Redo      |
| Ctrl+D           | Duplicate |
| Del or Backspace | Delete    |
| Ctrl+S           | Save      |

### Widget list

A searchable list (*Find by name or key…*) of every widget with its type, key and size. A **PRO** tag marks Pro options in use; **⚠** marks a key that needs fixing. Click a row to select the widget.

A layout holds up to **128 widgets**.

### Widget settings (inspector)

Every widget has a **Name** (up to 24 characters). Sizes are in canvas units.

| Widget      | Size range                     | New-widget size |
| ----------- | ------------------------------ | --------------- |
| Key button  | 40–600                        | 120             |
| Joystick    | 40–600                        | 260             |
| D-pad       | 40–600                        | 220             |
| Touchpad    | 40–1500 wide, up to 1080 high | 700 × 400      |
| Trackpad    | 40–1500 wide, up to 1080 high | 700 × 350      |
| Gyro toggle | 40–300                        | 140             |

**Key button**

| Option                        | Values                                                                                                     | Default                     |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------- | --------------------------- |
| **Shape**               | Circle · Square · Rectangle · Triangle (only Rectangle has separate width and height)                   | Square                      |
| **Behaviour**           | Tap · Hold · Toggle                                                                                      | Hold                        |
| **Minimum hold** (Hold) | 50–1000 ms                                                                                                | 200 ms                      |
| **Sends**               | Keyboard & mouse · Gamepad                                                                                | Keyboard & mouse, key *A* |
| **Combo** (Pro)         | up to 3 extra keys, each **after 0–500 ms** (steps of 10) or **Random** (0–500 ms each time) | none                        |

- **Tap** sends a quick press per touch. **Hold** presses while held, and for at least the minimum hold. **Toggle** presses on one tap and releases on the next.
- Switching **Sends** resets the key and clears combos. **Change…** opens the key picker.
- On Free, existing combos still show and can be edited, but new keys can't be added, and the phone doesn't fire combos.

**Joystick**

| Option                                                  | Values                                                                        | Default     |
| ------------------------------------------------------- | ----------------------------------------------------------------------------- | ----------- |
| **Placement**                                     | Fixed · Follow (appears wherever your thumb lands on its half of the screen) | Fixed       |
| **Output**                                        | Left stick · Right stick · WASD                                             | Left stick  |
| **Actuation** (WASD)                              | 1–100%                                                                       | 50%         |
| **Diagonals** (WASD)                              | on / off (press two keys between directions)                                  | on          |
| **Forward cone / Back cone** (WASD, diagonals on) | 15–60°                                                                      | 30° / 30° |
| **Smoothing** (WASD)                              | 5–50%                                                                        | 20%         |
| **Auto-sprint lock** (Pro)                        | on / off                                                                      | off         |
| **Sprint key**                                    | gamepad button for Left stick, keyboard key otherwise                         |             |
| **Starts at / Locks at**                          | 50–100% / 50–100%                                                           | 80% / 95%   |
| **Invert X / Invert Y**                           | on / off                                                                      | off         |

Auto-sprint holds the sprint key once you push the stick forward past *Starts at*, and locks forward with sprint held if you let go beyond *Locks at*. Tap or drag the stick on the phone to unlock.

**D-pad:** **Sends** WASD keys or the controller D-pad.

**Touchpad**

| Option                        | Values                     | Default |
| ----------------------------- | -------------------------- | ------- |
| **Output**              | Mouse · Right stick (Pro) | Mouse   |
| **Sensitivity**         | 0.1–10.0×                | 1.0×   |
| **Invert X / Invert Y** | on / off                   | off     |

*Mouse* moves the mouse, which controls the camera in most PC games. *Right stick* suits games that ignore the mouse while a controller is active, and is tuned in Input Lab's [Touchpad to joystick camera](#touchpad-to-joystick-camera) card.

**Trackpad** (a PlayStation-style touchpad; use it with the **DualShock 4** controller in Input Lab)

| Option                                                                                           | Default    |
| ------------------------------------------------------------------------------------------------ | ---------- |
| **Tap to click**                                                                           | on         |
| **Split left / right click** (left half clicks Share/Back, right half clicks the touchpad) | off        |
| **Aim with gyro while touching**                                                           | off        |
| Sensitivity 0.1–10.0×, Invert X / Y                                                            | 1.0×, off |

The trackpad sends finger **positions**, so sensitivity and invert don't change its output.

**Gyro toggle**

| Option                     | Values                                                                    | Default       |
| -------------------------- | ------------------------------------------------------------------------- | ------------- |
| **Shape**            | Square · Circle                                                          | Square        |
| **When is gyro on?** | **Tap to toggle** · **While held** (pick the trigger widget) | Tap to toggle |

*While held* is **scope mode**: you aim with the phone only while you hold that button, for example your aim-down-sights trigger. Deleting a widget clears any gyro toggle that used it as its trigger.

### Key picker

Shows only keys the phone can send. In keyboard mode, click the **capture pad** and press a real key or click a mouse button to pick it.

| Category                              | Keys                                                              |
| ------------------------------------- | ----------------------------------------------------------------- |
| Letters                               | A–Z                                                              |
| Numbers                               | 0–9                                                              |
| Special                               | Space, Enter, Shift, Ctrl, Alt, Tab, Esc                          |
| F-keys                                | F1–F12                                                           |
| Arrows                                | Arrow up, Arrow down, Arrow left, Arrow right                     |
| Mouse (Mouse & keyboard layouts only) | Left, Right, Middle, Side 1, Side 2                               |
| Gamepad: face                         | A (Cross), B (Circle), X (Square), Y (Triangle)                   |
| Gamepad: bumpers and triggers         | LB, RB, LT, RT                                                    |
| Gamepad: D-pad                        | Up, Down, Left, Right                                             |
| Gamepad: system                       | Start (Options), Back (Share), Guide (PS), L3, R3, Touchpad click |

- **Mouse buttons need a Mouse & keyboard layout.** The Mouse category is hidden in Gamepad layouts, because the PC only clicks the mouse for MnK layouts.
- **Arrow Up / Arrow Down:** layouts made with phone apps before 3.0 pressed **F11 / F12** for these. Re-pick the arrow to fix it. Capturing ↑ or ↓ gives the correct key.
- The picker flags problem keys with ⚠, such as Mouse side 3/4 (*The PC doesn't press this mouse button yet*) or an old code that sends a controller button instead of a key.

---

## Input Lab: Overview

*Per-phone gyro tuning. Every change applies on the next packet and is remembered for this phone.*

Everything in Input Lab is **free**, except presets.

| Part                               | What it does                                                                                                                                                                                                                                                            |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Device switcher**          | P1 and P2 tiles with the phone's name and **USB** or **Wi-Fi**. Pick which phone you're tuning                                                                                                                                                              |
| **View filter**              | **All controls · Gamepad · Mouse & keyboard**. *Gamepad* hides Analog WASD slicing. *Mouse & keyboard* hides Virtual controller and Touchpad to joystick camera, and replaces the gyro mode tiles with a note that in MnK mode gyro always drives the mouse |
| **Reset to defaults**        | Resets every Input Lab setting for the selected phone, after you confirm                                                                                                                                                                                                |
| **Presets / Save as preset** | Pro. See [Presets](#presets-pro)                                                                                                                                                                                                                                         |

The page has two columns: **Tuning**, **Virtual controller**, **Gyro behaviour** and **Touchpad to joystick camera** on the left; **Response curve**, **Axis mapping** and **Analog WASD slicing** on the right. They stack in a narrow window.

### Using the dials

- **Drag up or down**: 100 px covers the whole range.
- **Mouse wheel** or **arrow keys**: 1/50 of the range per step.
- **Double-click** to type an exact value.
- Sliders show the default value as a small grey mark.

### How settings are remembered

- Settings belong to **each phone**, identified by its Android ID, so renaming the phone keeps them.
- They're also kept **per connection type**: the same phone has one set over **USB** and another over **Wi-Fi**. Use a preset (Pro) to copy tuning between them.
- Changes apply live. They're written to disk when the phone **disconnects**, when you **close the window** and when you **quit**.

---

## Tuning Dials

The **Tuning** card has four dials that affect gyro in every mode.

| Dial                                           | Range        | Default | What it does                                                                           |
| ---------------------------------------------- | ------------ | ------- | -------------------------------------------------------------------------------------- |
| **Smoothing** · *steadies shaky aim*  | 0.00–1.00   | 0.68    | Filters out hand tremor and sensor noise. 0 is raw and instant; 1.00 is heaviest       |
| **Deadzone** · *ignores tiny shakes*  | 0.00–0.20   | 0.12    | Rotation slower than this is ignored, so the camera doesn't drift while you hold still |
| **Response** · *extra speed boost*    | 0.50–3.00× | 1.00×  | A final multiplier on top of sensitivity                                               |
| **Sensitivity** · *camera turn speed* | 0–100       | 90      | How far the stick or mouse moves for a given rotation speed                            |

**Smoothing in detail.** VeloPad blends each new reading with the last one: `alpha = max(0.05, 1 − 0.95 × smoothing)`, then `smoothed += (raw − smoothed) × alpha`. At 0, alpha is 1 (no filtering). At the default 0.68, alpha is about 0.35. At 1.00, alpha is 0.05. In Tilt mode, smoothing applies to the stick output and is 1.5× faster when returning toward centre, so steering straightens quickly.

**Deadzone in detail.** The value is in radians per second: 0.20 rad/s is about 11.5° per second. In Snap mode, dropping under the deadzone also stops the stick instantly. In Tilt mode the deadzone works differently: it's a circle around centre, as a fraction of Max tilt, and output is rescaled above it so there's no jump.

**Sensitivity in detail.**

- **Stick modes (0, 1):** full stick = 32767 × sensitivity/100 × response, reached when the curve's output is 1.0.
- **Mouse (mode 2 and MnK):** pixels per packet = curved rate × sensitivity × 0.5 × response, scaled by the time since the last packet so speed stays the same at 125, 250 and 500 Hz.
- **DualShock 4:** gyro × sensitivity/90 × response, so the default 90 passes motion through 1:1.
- **Tilt (mode 3):** sensitivity and response aren't used; stick deflection comes from your tilt angle versus **Max tilt**.

---

## Virtual Controller

*Gamepad mode only.* Picks the virtual controller this phone becomes.

| Controller                          | Best for                                              | Details                                                                                                                                                                                                                                                                                                                                                                                    |
| ----------------------------------- | ----------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Xbox 360** (XInput)         | Virtually every PC game                               | Gyro is folded into a stick (or the mouse). Rumble goes back to the phone. The phone's **Touchpad click** is sent as **Back**. Trackpad touches are ignored                                                                                                                                                                                                                    |
| **DualShock 4** (native gyro) | Steam Input and games with PlayStation motion support | The phone's gyro and accelerometer go into the controller's own **motion sensors**, so the game or Steam Input does the gyro aiming. The **Trackpad** widget drives the DS4 touchpad surface, **Touchpad click** is the touchpad button and **Guide** is the PS button. The phone's battery level shows as the controller's battery. Rumble goes back to the phone |

**What still applies with DualShock 4:** deadzone, smoothing, axis mapping and inversion, the response curve, sensitivity and response. **What doesn't:** the gyro modes and gyro target stick (the page hides them), because the motion goes straight to the controller's sensors.

Switching swaps the virtual controller instantly. If a DualShock 4 can't be created, VeloPad falls back to Xbox 360. The virtual controller appears when the phone sends its first Gamepad-mode input and is **removed** when the phone switches its layout to Mouse & keyboard, so games stop seeing it.

### Gyro target stick

*Xbox 360 only.* **Right · Aim** (default) or **Left · Steer**: which stick receives gyro in modes 0, 1 and 3.

- **Right** is for camera and crosshair aiming.
- **Left** is for steering, vehicles or southpaw setups.

Gyro is **added** to whatever that stick on the phone is already doing, then limited to the stick's range.

---

## Gyro Behaviour Modes

The card header says where gyro goes: *Output goes to Right stick / Left stick / the mouse / the DS4 motion sensors*.

| # | Mode                                       | Feel                                                                                                                | Good for                                                   |
| - | ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------- |
| 0 | **Rate → Stick · Decay** (default) | Stick deflection follows how fast you turn. When you stop, the camera glides to a stop                              | Gamepad shooters with aim assist; a good default           |
| 1 | **Rate → Stick · Snap**            | Same as Decay, but the stick returns to centre the instant you stop                                                 | Twitch shooters, flick aiming                              |
| 2 | **Rate → Mouse**                    | Gyro moves the Windows mouse; the sticks still work as normal. Stop moving and the camera stays exactly where it is | Games that take mouse and controller together, desktop use |
| 3 | **Angle → Stick · Tilt**           | The phone's **angle** becomes stick deflection. Hold an angle and the stick stays held                        | Racing, flight sims                                        |

- **Modes 0 and 1** treat the yaw and pitch speeds as one 2D movement, so diagonals keep their direction. **1 rad/s** (about 57° per second) is full input to the curve.
- **Mode 2** works per axis with **4 rad/s** as full input to the curve. Fractions of a pixel carry over to the next packet, so slow aiming isn't lost.
- **Mode 3** builds the angle from the gyroscope alone (not the accelerometer), so bumps and fast sideways movement don't kick the steering. Very slow drift under 0.015 rad/s is ignored.
- In a **Mouse & keyboard** layout, gyro always moves the mouse, whatever mode is picked here.

---

## Tilt Setup (Mode 3)

Shown when **Angle → Stick · Tilt** is selected with Xbox 360.

| Control                                         | Values                                                                | Default  | What it does                                                                                                                                                                         |
| ----------------------------------------------- | --------------------------------------------------------------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Set neutral level**                     | button                                                                |          | Makes the phone's current position the centre (0, 0). Hold the phone how you'll play, then click it                                                                                  |
| **Orientation**                           | Portrait · Landscape                                                 | Portrait | How you hold the phone. In 2.0.0, changing it re-zeroes the tilt but doesn't change which axes are read; use [Axis mapping](#axis-mapping) for that                                   |
| **Tilt axes**                             | Both (2-Axis) · Horizontal Only (Steer X) · Vertical Only (Pitch Y) | Both     | Limit tilt to steering only, pitch only, or full 360°                                                                                                                               |
| **Recenter when I touch the stick**       | on / off                                                              | on       | Touching the phone's stick on the gyro side (past about 6%) re-centres the tilt to your current position. For the right stick, putting a finger on a right-stick touchpad counts too |
| **Recenter speed** · *0.0 = hold tilt* | 0.0–3.0×                                                            | 0.0      | 0 holds your tilt indefinitely, like a steering wheel held through a long bend. Above 0, after 0.4 s of holding still, the tilt eases back to centre at roughly that rate per second |
| **Max tilt** · *full lock angle*       | 10–90°                                                              | 45°     | How far you tilt for full stick. 25–35° is quick and arcade-like; 60–90° is fine and sim-like                                                                                    |

Changing the gyro mode, orientation or tilt axes also re-zeroes the tilt. Smoothing and Deadzone apply in Tilt mode; Sensitivity and Response don't.

---

## Touchpad to Joystick Camera

*Makes swiping on a Right-Stick touchpad feel like mobile touch aiming, and stops the camera the instant you lift your finger. Works with both Xbox 360 and DualShock 4.*

This card only affects a **Touchpad** widget whose **Output** is **Right stick** (a Pro option on the phone). A touchpad set to **Mouse** isn't affected.

### How it behaves

- Your **swipe speed** becomes right-stick deflection. A swipe of **14 px per 8 ms** (at sensitivity 1.0) is full stick.
- **Lift your finger** and the stick snaps to centre immediately, so the camera stops dead.
- **Rest your finger** without moving and the stick holds for a moment (about 22–65 ms), then fades out over about 45 ms.
- **Fast swipes** reach full speed almost at once.
- The result is **added** to the phone's right stick and to any gyro on the right stick.

### The four dials

| Dial                                                | Range        | Default | What it does                                                                                                                                                                                                                                                          | Turn it up when…                                                                          |
| --------------------------------------------------- | ------------ | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| **Touch sens** · *swipe speed*             | 0.20–4.00× | 1.20×  | Multiplies swipe speed before anything else. Higher turns faster for the same swipe                                                                                                                                                                                   | The camera feels slow and you run out of screen                                            |
| **Anti-deadzone** · *small movement boost* | 0.00–0.40   | 0.16    | The smallest stick output any real swipe produces: `output = anti-deadzone + (1 − anti-deadzone) × curved`. It jumps over the game's own stick deadzone so tiny swipes still move the camera. It fades in gently for very slow movement, so stopping stays smooth | Small swipes do nothing in game. Turn it down if the camera lurches when you start a swipe |
| **Vertical scale** · *up/down speed*       | 0.50–2.50× | 1.35×  | Multiplies only up/down movement. Many games turn slower vertically; this evens it out                                                                                                                                                                                | Looking up and down feels sluggish compared with left and right                            |
| **Curve gamma** · *fine aim control*       | 1.00–3.00   | 1.65    | Shapes speed into output: `output = speed ^ gamma`. 1.00 is linear. Higher values make slow drags gentler for precise aim while fast flicks still reach full speed                                                                                                  | You overshoot small adjustments. Turn it down if slow tracking feels sticky                |

**Tuning tip:** set the game's own stick sensitivity first, then raise **Anti-deadzone** until the smallest swipe just moves the camera, then adjust **Touch sens** for turn speed, **Vertical scale** for balance, and **Curve gamma** last for fine aim.

---

## Response Curve

Shapes how gyro feels at different speeds. Drag the handles up to make slow movement punchier, or down to soften it.

- A curve from (0, 0) to (1, 1) with two handles, **P1 · precision** (default **0.25, 0.10**) and **P2 · acceleration** (default **0.75, 0.90**). The default is an S-curve: slow movement is reduced for steadiness, the middle is boosted, and the top end reaches full output.
- Handles can go anywhere from 0 to 1 on both axes. **Reset** restores the defaults.
- Raising **P1** works like an anti-deadzone for the stick: slow movement gets past the game's stick deadzone sooner.
- What the curve's horizontal axis means depends on the mode:

| Mode                                | Input that equals 1.0        |
| ----------------------------------- | ---------------------------- |
| Rate → Stick (0, 1)                | 1 rad/s of combined rotation |
| Rate → Mouse (2), MnK, DualShock 4 | 4 rad/s on each axis         |
| Tilt (3)                            | Max tilt                     |

The curve doesn't affect the Touchpad to joystick camera, which has its own gamma.

---

## Axis Mapping

Applies to every gyro mode, including DualShock 4 and Tilt.

| Control                       | Values      | Default | What it does                                  |
| ----------------------------- | ----------- | ------- | --------------------------------------------- |
| **Yaw · left / right** | Y · X · Z | Y       | Which phone axis turning left and right reads |
| **Pitch · up / down**  | X · Y · Z | X       | Which phone axis looking up and down reads    |
| **Invert yaw**          | on / off    | off     | Reverses left-right camera panning            |
| **Invert pitch**        | on / off    | off     | Reverses up-down camera movement              |

The phone already adjusts its axes for how it's held and for its YAW or ROLL gyro setting, so the defaults suit most people. Change these if the camera moves on the wrong axis.

---

## Analog WASD Slicing

*Mouse & keyboard only.* Makes your on-screen joystick feel like a real 360° thumbstick even in keyboard (WASD) games, such as games that don't allow a controller and mouse at the same time. You walk and run smoothly in any direction without losing sprint speed.

**Use it with:** a **Mouse & keyboard** layout with a **Joystick** set to **Left stick**. Slicing reads the left stick and presses W, A, S and D for you.

| Control                        | Range       | Default | What it does                                                                                                               |
| ------------------------------ | ----------- | ------- | -------------------------------------------------------------------------------------------------------------------------- |
| **Enable** (card toggle) | on / off    | off     | Turns slicing on. The sliders are disabled while it's off                                                                  |
| **Cycle window**         | 100–350 ms | 180 ms  | Length of each pulse cycle. Lower is tighter steering; higher is smoother sprinting                                        |
| **Angle deadband**       | 0–22°     | 5°     | Within this many degrees of straight or exactly diagonal, keys are held solid instead of pulsed, so you run in clean lines |

How it works:

- Below **15%** stick deflection, no keys are pressed.
- The stick's angle picks a direction. The **main key** for that direction is held all the time, so the game never breaks your sprint.
- The **second key** is pulsed for part of each cycle, in proportion to how far you're angled toward the diagonal. That gives smooth steering between the eight directions.
- Near straight ahead (within the deadband) only the main key is held; near a diagonal, both keys are held.
- While slicing is on, W, A, S and D keys sent directly by the phone are ignored for P1, so they don't fight the slicer.
- Keys are released when you switch modes or disconnect.

---

## Presets (Pro)

Save a phone's whole Input Lab setup under a name and apply it to any phone in one click.

- **Save as preset** asks for a name (suggested *&lt;phone&gt; tuning*, up to 48 characters). An existing name asks *Replace it?*. A green *Saved "&lt;name&gt;"* flash confirms it.
- **Presets** opens a menu headed *Apply to &lt;phone&gt;* with your presets in alphabetical order. Pick one to apply every setting at once. **Delete preset** removes one; phones using it keep their current settings.
- A preset holds every Input Lab setting: controller, gyro mode and stick, the four tuning dials, tilt setup, curve, axis mapping, WASD slicing and the touch camera dials.
- Presets are shared across phones and stored in `%APPDATA%\VeloPad\presets.vpd`.
- Both buttons show a **PRO** tag unless the **selected phone** has VeloPad Pro.

---

## Settings

*Global preferences for this PC.* Every change is saved immediately (the header flashes **Saved**). The section list on the left jumps to **Appearance**, **Input injection**, **Network** and **Updates & about**.

### Appearance

| Setting                 | Options                              | Default          |
| ----------------------- | ------------------------------------ | ---------------- |
| **Theme**         | Light · Dark · System              | Dark             |
| **Accent colour** | 9 shown, **More…** for all 16 | Blue `#007AFF` |

- **System** follows Windows' app theme when VeloPad starts or when you pick it.
- The accent colours are Blue `#007AFF`, Sky `#32ADE6`, Indigo `#5856D6`, Violet `#BF5AF2`, Pink `#FF2D55`, Rose `#FF6B9D`, Red `#FF3B30`, Coral `#FF6B35`, Orange `#FF9500`, Yellow `#FFD60A`, Amber `#FFB800`, Green `#34C759`, Mint `#30D158`, Teal `#00C7BE`, Purple `#6E40C9` and Lime `#A8E35A`, the same set as the phone. The window icon follows your accent; the tray icon stays VeloPad blue.

### Keyboard & mouse injection

Affects keyboard and mouse output only (MnK layouts, Rate → Mouse gyro and touchpad mouse). It doesn't affect the virtual controller.

| Method                                        | What it does                                                                                                                                             |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Smart Auto** (recommended, default)   | Uses the Interception driver when it's installed, otherwise SendInput                                                                                    |
| **Standard Windows input · SendInput** | No drivers needed. The safest choice with anti-cheat                                                                                                     |
| **Hardware driver · Interception**     | Input enters Windows like a real keyboard and mouse, so games that block software input accept it. Falls back to SendInput if the driver isn't installed |

- The method is chosen **when VeloPad starts**. After changing it, you'll see *Restart VeloPad to switch input method.*
- **Driver status** shows *Driver active*, *Driver installed* (restart Windows to finish) or *Driver not installed*.
- **How to install…** (shown when it's missing): **Open driver folder**, open a Command Prompt **as Administrator** in that folder, run `install-interception.exe /install`, restart Windows, then reopen VeloPad. To remove it, run `install-interception.exe /uninstall` the same way and restart. Interception is a signed, open-source (LGPL) driver; VeloPad never reads or blocks your real keyboard and mouse through it.

> **Anti-cheat heads-up:** some games' anti-cheat flags the Interception driver and won't launch while it's **installed**, even when VeloPad isn't running. If a game refuses to start, uninstall the driver or use SendInput. Gamepad mode uses the ViGEmBus driver and isn't affected by any of this.

### Network & discovery

- **Discovery display name:** what phones see in their list of PCs. Leave it empty to use the computer name. It's saved when you leave the field and updates the sidebar.
- **Ports:** *Phones can't find this PC? Allow VeloPad through your firewall. It uses UDP 4000, 4002 and TCP 4001–4003.*

### Updates & about

- Shows **VeloPad &lt;version&gt;** and the update status: *Not checked*, *Checking…*, *Up to date*, *Update available · &lt;version&gt;* or *Couldn't check*, with *Checked N min ago · See what's new*.
- **Check for updates** checks now; VeloPad also checks quietly every time it starts. **Release notes** opens the releases page.
- **Update available** pop-up: shows *current → new*, what's new in the update, and **Skip this version**, **Later** or **Download update** (opens the release page; run the new installer to update). Your settings and profiles carry over.
- **What's new in VeloPad** opens automatically the first time a new version starts, or from the Dashboard button. It shows notes for recent releases from GitHub, or the notes bundled with the app when you're offline (*Offline · showing bundled notes*). **All releases** opens the releases page; **Got it** closes it.

---

## Free vs Pro

Pro is bought on the **phone** (Monthly with a **14-day free trial**, Annual, or Lifetime through Google Play). The PC reads each phone's licence when it connects and every 30 seconds after, so an upgrade shows up on the PC within about 30 seconds.

**On the PC**

| Feature                                                                                                                         | Free | Pro |
| ------------------------------------------------------------------------------------------------------------------------------- | ---- | --- |
| All Input Lab tuning: every dial, both controllers, all gyro modes, tilt, curve, axis mapping, WASD slicing, touch camera dials | ✓   | ✓  |
| Create, edit, duplicate and delete layouts on the PC                                                                            | ✓   | ✓  |
| **Input Lab presets** (checked on the selected phone)                                                                     |      | ✓  |
| **Sync from phone**, **Upload to phone**, **Save & upload** (checked on P1)                                   |      | ✓  |
| Editor: **add combo keys**, **Auto-sprint lock**, **Touchpad → Right stick**                                 |      | ✓  |

**On the phone** (for reference): USB play, 250 and 500 Hz polling, more than 10 layouts, combos, auto-sprint and touchpad to right stick in play are Pro. See the Android manual.

The Dashboard's Pro pill shows which phones have Pro.

---

## Files VeloPad Keeps

Everything is in `%APPDATA%\VeloPad\` (paste that into File Explorer's address bar).

| File                   | Contents                                                                                       |
| ---------------------- | ---------------------------------------------------------------------------------------------- |
| `config.vpd`         | Theme, accent, window size, input method, discovery name, and every phone's Input Lab settings |
| `presets.vpd`        | Input Lab presets (Pro)                                                                        |
| `profiles\*.vpd`     | Your layouts, one file each                                                                    |
| `release_notes.json` | Release notes saved for offline viewing                                                        |

These survive updates and uninstalling. Back up the folder to keep your setup; delete it to start fresh.

---

## Signal Processing Reference

How gyro becomes output, per packet. Timing is normalised to 8 ms packets using the phone's own timestamps.

### Shared first steps

```
1. yaw, pitch = pick axes from (gx, gy, gz) per Axis mapping, negate if inverted
2. alpha      = max(0.05, 1 - 0.95 * smoothing)
3. dt_scale   = (time since last packet) / 8 ms
```

### Modes 0 and 1: Rate → Stick

```
1. if |axis| < deadzone: axis = 0
       Snap (mode 1) also sets that axis's smoothed value to 0
2. smooth += (axis - smooth) * alpha
3. mag     = hypot(smooth_yaw, smooth_pitch)
4. curved  = curve(min(mag / 1.0 rad/s, 1))
5. stick   = direction * curved * 32767 * sensitivity/100 * response
6. target stick = clamp(phone stick + stick)
```

### Mode 2 and MnK: Rate → Mouse

```
1-2. Same deadzone and smoothing (no snap)
3.   curved_axis = sign * curve(min(|smooth| / 4.0 rad/s, 1)) * 4.0
4.   pixels      = curved_axis * sensitivity * 0.5 * response * dt_scale
5.   carry fractions of a pixel to the next packet; add touchpad mouse movement
6.   move the mouse (SendInput or Interception)
```

### Mode 3: Angle → Stick (Tilt)

```
1. re-centre if the gyro-side stick is touched (option on), or on Set neutral level
2. ignore rotation under 0.015 rad/s
3. angle += rate * dt / max_tilt                     (normalised, clamped)
4. after 0.4 s still: angle eases to 0 at recenter_speed (if > 0)
5. mag = min(hypot(angle), 1);  below deadzone -> 0, else rescale (mag - dz) / (1 - dz)
6. curved = curve(mag)
7. smooth toward curved with alpha (1.5x alpha when returning toward centre)
8. apply Tilt axes, output smooth * 32767 to the target stick
```

### DualShock 4

```
pitch_out = curve(smooth_pitch) * sensitivity/90 * response
yaw_out   = curve(smooth_yaw)   * sensitivity/90 * response
roll      = raw phone gz;  accelerometer = raw phone values
written to the DS4 motion report (916.73 units per rad/s, 8192 per g)
```

### Touchpad → right stick

```
speed  = swipe delta * touch_sens   (vertical also * vertical_scale), per 8 ms
norm   = min(speed / 14 px, 1)
curved = norm ^ curve_gamma
out    = anti_deadzone + (1 - anti_deadzone) * curved   (soft knee near zero)
lift finger -> 0 instantly; still finger -> hold briefly, then fade
right stick += out
```

---

## Recommended Settings by Genre

Starting points; tune from here.

### Shooters with a controller (Call of Duty, Halo, Apex, Fortnite)

| Setting                | Try                                           | Notes                                          |
| ---------------------- | --------------------------------------------- | ---------------------------------------------- |
| Controller             | Xbox 360, or DualShock 4 for Steam Input gyro |                                                |
| Gyro target stick      | Right · Aim                                  |                                                |
| Gyro mode              | 0 Decay or 1 Snap                             | Decay for aim assist tracking, Snap for flicks |
| Smoothing              | 0.55–0.70                                    |                                                |
| Deadzone               | 0.06–0.12                                    | Lower if your phone's gyro is clean            |
| Sensitivity / Response | 70–90 / 1.00×                               |                                                |
| Curve P1 / P2          | (0.25, 0.08) / (0.75, 0.92)                   | Steady low end, fast flicks                    |
| Phone                  | Gyro toggle in scope mode on your aim trigger | Gyro only while aiming                         |

### Touch-aim camera on a controller layout

| Setting        | Try                                                       | Notes                              |
| -------------- | --------------------------------------------------------- | ---------------------------------- |
| Phone          | Touchpad with Output **Right stick** (Pro)          |                                    |
| Touch sens     | 1.0–1.6×                                                |                                    |
| Anti-deadzone  | Just enough that tiny swipes move the camera (0.12–0.22) | Depends on the game's own deadzone |
| Vertical scale | 1.2–1.5×                                                |                                    |
| Curve gamma    | 1.4–2.0                                                  | Higher for snipers and precise aim |

### Shooters with mouse and keyboard (CS2, Valorant, Overwatch 2, Apex)

| Setting             | Try                               | Notes                                            |
| ------------------- | --------------------------------- | ------------------------------------------------ |
| Phone               | **Mouse & keyboard** layout | Gyro drives the mouse automatically              |
| Smoothing           | 0.50–0.68                        | 0.35–0.50 for raw twitch aim                    |
| Deadzone            | 0.05–0.10                        |                                                  |
| Sensitivity         | 40–65                            | Start near 50 and match your in-game sensitivity |
| Analog WASD slicing | On, with a Left-stick joystick    | 180 ms, 5°                                      |
| Input method        | SendInput for anti-cheat games    | Interception only where it's allowed             |

### Racing (Forza Horizon, Assetto Corsa, F1)

| Setting           | Try                                                                              | Notes                                                    |
| ----------------- | -------------------------------------------------------------------------------- | -------------------------------------------------------- |
| Controller        | Xbox 360                                                                         |                                                          |
| Gyro target stick | Left · Steer                                                                    |                                                          |
| Gyro mode         | 3 Tilt                                                                           |                                                          |
| Tilt axes         | Horizontal Only (Steer X)                                                        | Forward and back tilt won't touch the throttle or camera |
| Deadzone          | 0.02–0.05                                                                       | Quick response around centre                             |
| Smoothing         | 0.65–0.75                                                                       |                                                          |
| Recenter speed    | 0.0 (hold) or 1.5–2.5× (self-centring)                                         |                                                          |
| Max tilt          | 30–45° arcade, 60°+ sim                                                       |                                                          |
| Curve P1 / P2     | (0.25, 0.25) / (0.75, 0.75)                                                      | Straight line, predictable steering                      |
| Phone             | Long-press the gyro toggle for **ROLL** if you hold the phone like a wheel |                                                          |

---

## Troubleshooting

| Problem                                         | Try this                                                                                                                                                                                                   |
| ----------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| The phone doesn't see the PC                    | Same network? Is Windows set to **Private** for this network? Start VeloPad as administrator once so it can add its firewall rules. Type the **LAN address** from the Dashboard into the phone |
| USB phone not found                             | Is `adb` installed where VeloPad looks? Is USB debugging on and allowed? Try a data cable and another port. Close other tools that use ADB exclusively                                                   |
| Latency says *Needs VeloPad 3.0 on the phone* | Update the phone app; older versions don't measure ping                                                                                                                                                    |
| Ping is red or spiky                            | Use 5 GHz Wi-Fi near the router, avoid busy hotspots, or use USB (Pro)                                                                                                                                     |
| Game doesn't see a controller                   | Check the phone layout is **Gamepad**, not MnK. Reinstall the ViGEmBus driver with the VeloPad installer                                                                                             |
| Keys or clicks don't register in a game         | Run VeloPad as administrator (it asks by default). Try the other input method. Keys come from **P1** only                                                                                            |
| Mouse buttons missing in the editor             | Set the layout's **Profile mode** to Mouse & keyboard                                                                                                                                                |
| Arrow Up/Down press F11/F12                     | Re-pick the arrow key in the layout and use VeloPad 3.0 on the phone                                                                                                                                       |
| Gyro drifts when still                          | Raise **Deadzone** a little, or in Tilt mode click **Set neutral level**                                                                                                                       |
| Game won't start after installing Interception  | Uninstall the Interception driver (see Settings) and use SendInput                                                                                                                                         |
| My tuning is different over USB and Wi-Fi       | Settings are kept per connection type. Save a preset on one and apply it on the other (Pro)                                                                                                                |
| Two copies of VeloPad                           | Starting VeloPad again replaces the running copy; quit from the tray to close it fully                                                                                                                     |
