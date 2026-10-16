# VeloPad PC Client — User Manual

This document is the complete user manual and reference for the VeloPad PC Client. It covers downloading and installing the PC and Android apps, connecting your phone over Wi-Fi or USB, using every control, dial, slider, and graph in the Input Lab, configuring Xbox 360 vs. DualShock 4 emulation, enabling Analog WASD Macro-Slicing for Mouse-and-Keyboard games, and dialing in settings for different game genres.

---

## Table of Contents

1. [Download, Installation & Launching](#download-installation--launching)
2. [Main Window Layout](#main-window-layout)
3. [Dashboard Page](#dashboard-page)
4. [Profiles Page & Layout Editor](#profiles-page--layout-editor)
5. [Input Lab Page — Overview](#input-lab-page--overview)
6. [The Four Tuning Knobs](#the-four-tuning-knobs)
   - [Smoothing](#smoothing)
   - [Deadzone](#deadzone)
   - [Response](#response)
   - [Sensitivity](#sensitivity)
7. [Controller Target & Gyro Target Stick](#controller-target--gyro-target-stick)
   - [Xbox 360 vs. DualShock 4 (Native Gyro + Haptics)](#xbox-360-vs-dualshock-4-native-gyro--haptics)
   - [Gyro Target Stick (Right vs. Left Stick)](#gyro-target-stick-right-vs-left-stick)
8. [Gyro Behavior Modes](#gyro-behavior-modes)
   - [Mode 0: Rate -> Stick (Decay)](#mode-0-rate---stick-decay)
   - [Mode 1: Rate -> Stick (Snap)](#mode-1-rate---stick-snap)
   - [Mode 2: Rate -> Mouse](#mode-2-rate---mouse)
   - [Mode 3: Angle -> Stick (Tilt)](#mode-3-angle---stick-tilt)
9. [Axis Mapping](#axis-mapping)
10. [Mode 3 Exclusive Controls (2-Axis Tilt Configuration)](#mode-3-exclusive-controls-2-axis-tilt-configuration)
    - [Orientation & Tilt Axes](#orientation--tilt-axes)
    - [Set Neutral Level & Recenter on Stick Touch](#set-neutral-level--recenter-on-stick-touch)
    - [Recenter Speed](#recenter-speed)
    - [Max Tilt Angle](#max-tilt-angle)
11. [Response Curve Editor](#response-curve-editor)
12. [Analog WASD Macro-Slicing (MnK Mode)](#analog-wasd-macro-slicing-mnk-mode)
13. [Settings Page](#settings-page)
14. [System Tray](#system-tray)
15. [Signal Processing Pipeline — Technical Reference](#signal-processing-pipeline--technical-reference)
16. [Recommended Settings by Game Genre](#recommended-settings-by-game-genre)

---

## Download, Installation & Launching

### Step 1: Download & Install the PC Client

1. Visit the official **[VeloPad Releases page on GitHub](https://github.com/Blue-Wave-Horizon/velopad-releases/releases)**.
2. Under the latest release, download the Windows installer (**`VeloPad_Setup_vX.Y.Z.exe`**).
3. Run the downloaded installer and follow the on-screen setup wizard:
   - The installer automatically sets up **VeloPad** along with the required **ViGEmBus** virtual controller driver and bundled ADB tools for USB connections.
   - If Windows SmartScreen or User Account Control (UAC) prompts you, click **More info -> Run anyway** / **Yes** to allow installation (Administrator privileges are required for low-level game input injection).
4. Launch **VeloPad** using the Desktop shortcut or from the Windows Start Menu.

### Step 2: Download & Install the Android App

1. On your Android phone, open the **[VeloPad listing on Google Play Store](https://play.google.com/store/apps/details?id=com.bwh.velopad)**.
2. Tap **Install** to download and install the app on your device.
3. Open VeloPad on your phone, select **Wi-Fi/Hotspot** (same network as your PC) or **USB Cable** (Pro, with USB Debugging enabled), tap your discovered PC server, and tap the **Play** button to launch the controller HUD.

### Startup & Firewall Notes

When VeloPad starts on your PC, it immediately listens for phone connections on UDP port `4000`, TCP port `4001`, broadcasts discovery beacons on port `4002`, and runs profile sync on TCP port `4003`. If Windows Defender Firewall prompts you on first launch, allow VeloPad through on both **Private** and **Public** networks.

On launch, the window opens to the **Dashboard** page. Closing the window minimizes VeloPad to the Windows system tray instead of quitting so your controller stays active in-game. To exit completely, right-click the VeloPad tray icon and select **Quit**.

---

## Main Window Layout

The window is divided into two areas:

- **Left Sidebar (`200 px`, collapsible to `56 px`)**: Contains four navigation buttons (**Dashboard**, **Profiles**, **Input Lab**, and **Settings**) and a **Connected Devices** list showing each active phone with a green status dot and a disconnect button. Click the top chevron arrow to collapse the sidebar into icon-only mode.
- **Content Area (Right)**: Displays the active page (**Dashboard**, **Profiles**, **Input Lab**, **Settings**, or the interactive **Layout Editor** when editing a profile).

---

## Dashboard Page

The Dashboard is split into two side-by-side panels: **Player 1** (left) and **Player 2** (right). Each player slot has its own virtual controller and independent telemetry.

### Stat Cards (2×2 Grid per Player)

- **Connection** (top-left): Displays `"Connected"` (green border) or `"Disconnected"` (red border), along with transport type and device model (for example, `"USB — Pixel 8 Pro"` or `"Wi-Fi — 192.168.0.15"`).
- **Active Profile** (top-right): Displays the profile currently active on the phone and its mapped widget count.
- **Latency** (bottom-left): Shows the smoothed inter-packet interval (`90%` previous estimate + `10%` latest interval) in milliseconds with a quality badge:
  - **Green (`Good`)**: `< 5 ms`
  - **Amber (`OK`)**: `5–15 ms`
  - **Red (`Warn`)**: `> 15 ms`
- **Input Mode** (bottom-right): Displays `"Gamepad"` or `"MnK Mode"`.

### Latency Graph

Below the stat cards, a live scrolling graph plots the last 60 latency samples color-coded by the same green/amber/red thresholds, with horizontal reference grid lines at `5 ms`, `15 ms`, and `25 ms`.

---

## Profiles Page & Layout Editor

The **Profiles** page manages `.vpd` controller layouts stored in **`%APPDATA%\VeloPad\profiles\`** (seeded automatically with bundled starter profiles on first launch). All profile sync and upload features are **free for all users**.

### Profile List & Top Bar Actions

- **+ New Profile**: Creates a new blank `.vpd` layout file.
- **Sync Remote Profiles**: Queries the connected phone over TCP port `4003` (`0xFE`) for its profile list and automatically downloads every remote profile (`0xFF`) into `%APPDATA%\VeloPad\profiles\`.
- Each item in the list shows a two-letter avatar, profile name, widget count, and status badge (`active` in green if currently loaded on the phone, or `on phone` in accent color).

### Detail Panel & Layout Editor

Selecting a profile shows a visual preview of its layout and three actions:

- **Edit Layout**: Opens the interactive **Layout Editor** page where you can add, drag, resize, and inspect all six widget types (**Key Button**, **D-PAD**, **Joystick**, **Touchpad**, **Trackpad (DS4)**, and **Gyro Toggle**) on a `2520 × 1080` phone canvas, including live key/button capture and shape selection (`CIRCLE`, `SQUARE`, `RECTANGLE`).
- **Upload to phone**: Pushes the `.vpd` JSON to the connected phone (`0xFD`) and automatically activates it on the phone's HUD (`0xFC`).
- **Delete**: Deletes the local `.vpd` file (the `Default` profile is protected).

---

## Input Lab Page — Overview

The **Input Lab** is VeloPad's real-time signal processing studio. Every dial, curve, controller target, and mode in the Input Lab is **100% unlocked for both Free and Pro users**.

When a phone connects, a device tab appears (e.g., `P1  USB — OnePlus 12`). Settings are saved per physical phone using its persistent hardware `ANDROID_ID` in `%APPDATA%\VeloPad\config.vpd` and are restored automatically whenever that phone reconnects.

### View Filter Bar

At the top of each device tab is a segmented filter bar:
- **All Controls**: Shows all tuning cards side by side.
- **Gamepad**: Shows only Gamepad-relevant cards (`Controller Target`, `Gyro Target Stick`, Stick gyro modes, and S-Curve).
- **MnK**: Shows only Mouse-and-Keyboard cards (`Rate -> Mouse` notice, S-Curve, and `Analog WASD Macro-Slicing`).

---

## The Four Tuning Knobs

Located at the top of the Device Lab Panel, the four `68 × 68 px` circular dials take effect on the very next packet. Click and drag vertically (`100 px` drag spans the full range). From left to right:

---

### Smoothing

- **Range**: `0.00` to `1.00`
- **Default**: `0.68`
- **Subtitle**: *filter strength*

Smoothing applies an exponential moving average (EMA) low-pass filter to the gyro signal after the deadzone to eliminate hand tremor and sensor noise.

**How it works internally**: The dial controls **Filter Strength** (`0.00` = raw unfiltered input, `1.00` = maximum dampening). Internally, VeloPad converts Filter Strength to the EMA pass-through coefficient $\alpha$:

```
alpha = max(0.05, 1.0 - (smoothing * 0.95))
smoothed = smoothed + (raw - smoothed) * alpha
```

- At **`0.00`**: $\alpha = 1.00$ (zero filtering, instant raw sensor output).
- At **`0.68` (default)**: $\alpha \approx 0.354$ (fluid, stable tracking with minimal latency).
- At **`1.00`**: $\alpha = 0.05$ (heavy dampening for maximum steadiness).

In **Mode 3 (Tilt)**, smoothing is asymmetric: when returning toward center, $\alpha$ is boosted by `1.5×` (capped at `1.0`) so steering straightens out immediately.

---

### Deadzone

- **Range**: `0.00` to `0.20`
- **Default**: `0.12`
- **Subtitle**: *input threshold*

Defines the rotation threshold below which gyro input is treated as zero, preventing camera drift when holding the phone still.

- **In Modes 0 & 2**: Raw rates with $|val| < \text{deadzone}$ are set to `0.0`, while the EMA smoothing buffer decays naturally to zero.
- **In Mode 1 (Snap)**: When $|val| < \text{deadzone}$, both the raw value **and** the smoothing buffer are immediately zeroed for an instant stop.
- **In Mode 3 (Tilt)**: Applied radially to the 2D tilt magnitude $\text{rad\_mag} = \text{hypot}(\text{angle\_x}, \text{angle\_y})$ with smooth rescaling:
  ```
  rescaled = (rad_mag - deadzone) / (1.0 - deadzone)
  ```

---

### Response

- **Range**: `0.50x` to `3.00x`
- **Default**: `1.00x`
- **Subtitle**: *output multiplier*

A final linear gain multiplier applied after the response curve and sensitivity scaling (`0.50x` halves final output; `2.00x` doubles it). Keep at `1.00x` unless you want to boost or attenuate overall output without altering your curve shape.

---

### Sensitivity

- **Range**: `0` to `100`
- **Default**: `90`
- **Subtitle**: *base multiplier*

Controls how much stick deflection or mouse movement is produced for a given rotation rate:

- **Modes 0 & 1 (Rate -> Stick)**: Multiplies the normalized 2D curved vector `[0..1]` by `32767 * (sensitivity / 100.0) * response`.
- **Mode 2 (Rate -> Mouse) & MnK Gyro**: Multiplies the curved gyro rate by `(sensitivity / 2.0) * response * dt_scale`.
- **Mode 3 (Angle -> Stick)**: Sensitivity is bypassed because stick deflection is mapped directly from your physical tilt angle relative to **Max Tilt °**.

---

## Controller Target & Gyro Target Stick

### Xbox 360 vs. DualShock 4 (Native Gyro + Haptics)

The **CONTROLLER TARGET** card (visible in `All Controls` and `Gamepad` views) selects which virtual controller VeloPad creates via ViGEmBus:

1. **Xbox 360 (`x360`, Default)**:
   - Emulates a standard XInput Xbox 360 gamepad (`X360Output`).
   - Works out-of-the-box with virtually every PC game.
   - Captures XInput motor vibration callbacks and streams `RUMBLE:<large>:<small>\n` packets back to your phone over UDP port `4002` at up to 30 Hz.
2. **DualShock 4 (Native Gyro + Haptics) (`ds4`)**:
   - Emulates a Sony DualShock 4 controller (`DS4Output`) using custom 64-byte extended HID reports (`DS4ReportEx` via `vigem_target_ds4_update_ex`).
   - **Native 6-Axis Motion Streaming**: Embeds raw gyroscope (`deg/s`) and accelerometer (`g`) sensor data directly inside the DS4 HID report. Games with native DS4 motion support and **Steam Input** can read your phone's gyro directly as a native PlayStation motion controller.
   - **Native DS4 Trackpad & Buttons**: Supports the Android **Trackpad** widget's `1920 × 940` capacitive touch coordinates, **Touchpad Click**, and the **Guide (PS)** button, plus full force-feedback rumble to your phone.

### Gyro Target Stick (Right vs. Left Stick)

When **Xbox 360** is selected, the **GYRO TARGET STICK** sub-panel lets you choose which analog stick receives gyro output in Modes 0, 1, and 3:

- **Right Stick (Aim / Camera)** *(Default)*: Routes gyro output to the Right Analog Stick (`RX`, `RY`). Ideal for first-person and third-person shooters.
- **Left Stick (Movement / Steering)**: Routes gyro output to the Left Analog Stick (`LX`, `LY`). Ideal for racing games (Mode 3 tilt steering), flight sims, or vehicle control.

---

## Gyro Behavior Modes

All four gyro behavior modes are available to all users.

---

### Mode 0: Rate -> Stick (Decay)

- **Output**: Selected **Gyro Target Stick** (`Right Stick` by default, or `Left Stick`)
- **Best for**: Gamepad shooters with aim assist, third-person action games

Translates angular velocity (`rad/s`) into analog stick deflection using **omnidirectional 2D radial curve mapping** (`max_speed = 1.0 rad/s`). When you stop rotating the phone, the EMA smoothing buffer remains intact and gently decays back to center, producing smooth, natural camera stops.

---

### Mode 1: Rate -> Stick (Snap)

- **Output**: Selected **Gyro Target Stick** (`Right Stick` by default, or `Left Stick`)
- **Best for**: Fast-paced twitch shooters, flick aiming

Identical to Mode 0, except when rotation speed drops below the **Deadzone**, both the raw rate **and** the EMA smoothing buffer are immediately zeroed. The stick snaps back to `(0, 0)` the exact millisecond you stop rotating, eliminating any trailing camera movement after a fast flick.

---

### Mode 2: Rate -> Mouse

- **Output**: System mouse cursor (`Win32 SendInput` or `Interception` driver)
- **Best for**: Mouse-and-keyboard FPS games (CS2, Valorant, Apex Legends, Overwatch 2), desktop navigation

Translates angular velocity directly into relative mouse pixel movement (`max_speed = 4.0 rad/s`), compensated by frame delta-time (`dt_scale = dt_ms / 8.0`) so sensitivity stays constant across `125 Hz`, `250 Hz`, and `500 Hz` polling rates. Fractional sub-pixel remainders are accumulated across frames so slow, single-pixel micro-adjustments are never lost to rounding.

*(Note: When the phone HUD is switched to **MnK Mode**, VeloPad automatically uses this mouse pipeline regardless of the Gamepad mode dropdown.)*

---

### Mode 3: Angle -> Stick (Tilt)

- **Output**: Selected **Gyro Target Stick** (`Right Stick` or `Left Stick`)
- **Best for**: Racing games (steering wheel), flight simulators, 2-axis analog tilt games

Tracks the phone's **physical tilt angle** rather than rotation speed using **Pure 2-Axis Gyroscope Integration** (the same approach used by Steam Input for DualSense/Steam Deck gyro-to-joystick-deflection). Because it integrates pure angular velocity without accelerometer gravity fusion, fast hand movements and lateral G-forces never cause sudden steering kicks.

---

## Axis Mapping

Below the Gyro Mode selector are two columns for mapping physical phone axes:

- **Yaw (Left/Right)**:
  - **Yaw Axis**: `Y` (default), `X`, or `Z`.
  - **Invert X (Yaw)**: Reverses horizontal direction (unchecked by default).
- **Pitch (Up/Down)**:
  - **Pitch Axis**: `X` (default), `Y`, or `Z`.
  - **Invert Y (Pitch)**: Reverses vertical direction (unchecked by default so tilting forward looks down and tilting backward looks up consistently across both Gamepad and MnK modes).

---

## Mode 3 Exclusive Controls (2-Axis Tilt Configuration)

Selecting **Mode 3: Angle -> Stick (Tilt)** reveals the **Mode 3 — 2-Axis Tilt Configuration** panel:

### Orientation & Tilt Axes

- **Orientation**:
  - **Portrait** *(Default)*: Standard upright phone grip (`Yaw = Y`, `Pitch = X`).
  - **Landscape**: Sideways steering-wheel grip (automatically maps horizontal steering to `Y` and vertical pitch to `X` if left on defaults).
- **Tilt Axes (`tilt_axes`)**:
  - **Both (2-Axis)** *(Default)*: Outputs full 360° 2-axis tilt `(tilt_x, tilt_y)` to the target stick.
  - **Horizontal Only (Steer X)**: Locks vertical tilt output to `0`, outputting only left/right steering on the X-axis (ideal for racing games where you don't want accidental forward/back pitch affecting camera or menus).
  - **Vertical Only (Pitch Y)**: Locks horizontal tilt output to `0`, outputting only forward/backward pitch on the Y-axis.

### Set Neutral Level & Recenter on Stick Touch

- **Set Neutral Level (Button)**: Immediately calibrates your phone's current physical angle as `(0, 0)` neutral center. Hold your phone in a comfortable resting posture and click this button (or tap the assigned thumbstick on your phone).
- **Recenter on Stick Touch (Checkbox, Default On)**: Whenever your thumb touches the target on-screen joystick (`hypot > 2000`) or touchpad, VeloPad immediately resets the integrated tilt angle to `(0, 0)`. This lets you effortlessly re-center your steering or camera mid-game just by tapping the thumbstick.

### Recenter Speed

- **Range**: `0.0x` to `3.0x`
- **Default**: `0.0` (*Hold Tilt*)

Controls what happens when you hold the phone stationary (angular velocity `< 0.015 rad/s` for more than `400 ms`):
- **`0.0` (Default — Hold Tilt)**: The stick holds your exact tilt angle indefinitely, just like holding a real steering wheel through a long corner.
- **`> 0.0` (`0.1x–3.0x`)**: Gently decays the integrated angle back toward center at `angle *= max(0, 1 - recenter_speed * dt)` while the phone is held still.

### Max Tilt Angle

- **Range**: `10°` to `90°`
- **Default**: `45°`

Defines the physical tilt angle (in degrees from neutral) that produces `100%` (`32767`) stick deflection. Use `25°–35°` for quick arcade steering, `45°` for balanced control, or `60°–90°` for high-precision simulation racing/flight.

---

## Response Curve Editor

The **RESPONSE CURVE** card displays an interactive cubic Bezier **S-Curve** editor (`SCurveGraph`) with two draggable control points:

- **Control Point 1 (Lower-Left)**: Defaults to `(0.25, 0.10)`. Controls precision for slow, fine movements. Dragging P1 down dampens micro-rotations for rock-steady aiming.
- **Control Point 2 (Upper-Right)**: Defaults to `(0.75, 0.90)`. Controls acceleration for fast flicks.

VeloPad pre-computes the Bezier curve at 5,000 samples and builds a 1,000-point lookup table (`LUT`) per device session for zero-overhead per-packet evaluation.

---

## Analog WASD Macro-Slicing (MnK Mode)

Visible in the `All Controls` and `MnK` views of the Input Lab, **ANALOG WASD MACRO-SLICING** solves one of PC gaming's biggest input problems: many competitive shooters (such as *Apex Legends*) disable gamepad sticks or cancel sprint momentum when simultaneous controller + mouse input is detected.

When **Enable Analog WASD Macro-Slicing** is checked (and the phone is in **MnK Mode** with a Joystick mapped to **Left stick**):

1. VeloPad intercepts the phone's 360° analog left-stick coordinates `(lx, ly)` before they reach the virtual gamepad.
2. A dedicated 125 Hz (`8 ms`) high-precision timer (`WASDSlicer`) decomposes the stick's polar angle and magnitude into a primary cardinal key (e.g., `W`) and a secondary orthogonal key (e.g., `D`) with trigonometric duty cycles (`ratio_a = cos(offset)`, `ratio_b = sin(offset)`).
3. Over each slicing cycle window, VeloPad pulses `W`, `A`, `S`, and `D` at the exact duty ratios matching your thumb's angle and push distance—giving you smooth 360° directional movement and analog walk-to-run speed control using 100% pure keyboard output.
4. **Full-Speed Forward Lock**: When pushing within `35°` of straight forward (`W`) at `> 85%` stick deflection, the primary `W` key is locked at `100%` duty cycle (`1.0`) so your character never drops out of full sprint while steering left or right with `A`/`D` pulses.

### WASD Macro-Slicing Controls

- **Enable Analog WASD Macro-Slicing**: Master toggle (default `Off`).
- **Slicing Cycle Window (ms)**: Slider from `100 ms` to `350 ms` (default `180 ms`). Shorter windows (`120–160 ms`) feel tighter and more responsive; longer windows (`200–300 ms`) produce gentler character inertia.
- **Angle Deadband (°)**: Slider from `0°` to `22°` (default `5°`). Snaps angles within `±deadband°` of `0°`, `90°`, `180°`, or `270°` to pure single-key cardinal movement so you can run straight down narrow corridors without cross-axis pulsing.

---

## Settings Page

The **Settings** page contains global preferences persisted in `%APPDATA%\VeloPad\config.vpd`:

### 1. Theme

Choose **Light**, **Dark** (default), or **System** (automatically follows Windows `AppsUseLightTheme`).

### 2. Keyboard & Mouse Input Method

Selects the Windows mouse/keyboard injection backend (`input_backend`):

- **Smart Auto (Recommended) (`auto`)**: Automatically uses the kernel-level **Interception** driver if installed on your system, and seamlessly falls back to **Win32 `SendInput`** otherwise.
- **Standard Windows Input (SendInput) (`sendinput`)**: Injects hardware scan codes (`KEYEVENTF_SCANCODE`) and mouse events atomically via a dedicated `TIME_CRITICAL` background worker thread. Works out of the box with zero extra drivers.
- **Hardware Driver Mode (Interception) (`interception`)**: Injects input through the signed **Interception** kernel filter driver so keyboard and mouse events carry no `LLMHF_INJECTED` synthetic flag.

### 3. Discovery Display Name

Customizes the computer name broadcast to phones on your Wi-Fi network (defaults to your Windows hostname).

### 4. Accent Color

Displays a preview pill of your current accent color and a **Change** button that opens a 16-color popup palette:

| Color | Hex Code |
|-------|----------|
| Blue (Default) | `#007AFF` |
| Sky | `#32ADE6` |
| Indigo | `#5856D6` |
| Violet | `#BF5AF2` |
| Pink | `#FF2D55` |
| Rose | `#FF6B9D` |
| Red | `#FF3B30` |
| Coral | `#FF6B35` |
| Orange | `#FF9500` |
| Yellow | `#FFD60A` |
| Amber | `#FFB800` |
| Green | `#34C759` |
| Mint | `#30D158` |
| Teal | `#00C7BE` |
| Purple | `#6E40C9` |
| Lime | `#A8E35A` |

### 5. Application Updates (`Check for Updates`)

Queries the official `Blue-Wave-Horizon/velopad-releases` GitHub repository for newer versions and offers one-click download and installation of the latest setup executable. (VeloPad also checks silently once on startup.)

---

## System Tray

Closing the VeloPad window minimizes it to the Windows system tray:
- **Searching (No Phone Connected)**: Animated cascading wave chevron icon.
- **Connected**: Brand-blue VeloPad icon with a green status dot (`"VeloPad: Connected (<Device Name>)"`).
- **Right-Click Menu**: **Show VeloPad**, **Hide to Tray**, and **Quit**. Double-clicking the tray icon toggles the main window.

---

## Signal Processing Pipeline — Technical Reference

### Variables

| Symbol | Source | Description |
|--------|--------|-------------|
| `raw_x, raw_y` | Selected Yaw & Pitch axes from `gx, gy, gz` | Raw gyro angular velocity in `rad/s` |
| `dz` | Deadzone dial (`0.00–0.20`) | Deadzone threshold |
| `sm_val` | Smoothing dial (`0.00–1.00`) | Filter strength; $\alpha = \max(0.05, 1.0 - \text{sm\_val} \times 0.95)$ |
| `sens` | Sensitivity dial (`0–100`) | Base sensitivity multiplier |
| `resp` | Response dial (`0.50–3.00`) | Final output multiplier |
| `LUT[0..999]` | Response Curve editor | 1,000-point cubic Bezier lookup table |

### Modes 0 & 1 (`Rate -> Stick`, 2D Radial Mapping)

```
1.  val_x, val_y = select_axes(gx, gy, gz) with optional axis inversion
2.  if |val| < dz:
        val = 0
        if mode == 1 (Snap): smooth_buf = 0
3.  smooth_buf += (val - smooth_buf) * alpha
4.  mag = hypot(smooth_buf_x, smooth_buf_y)
5.  norm = min(mag / 1.0, 1.0)                     # max_speed = 1.0 rad/s for stick modes
6.  curved_mag = LUT[int(norm * 999)]
7.  dir_x, dir_y = smooth_buf_x / mag, smooth_buf_y / mag
8.  scale = 32767.0 * (sens / 100.0) * resp
9.  gyro_stick_x = dir_x * curved_mag * scale
    gyro_stick_y = dir_y * curved_mag * scale
10. Route (gyro_stick_x, gyro_stick_y) to Gyro Target Stick (Right or Left Stick)
    + optional Pro Touchpad-to-Right-Stick velocity (mdx * 3000 with 0.65/0.30 momentum decay)
11. Clamp final stick axes to [-32768, 32767]
```

### Mode 2 (`Rate -> Mouse`) & MnK Gyro

```
1-3. Same deadzone and EMA smoothing as Mode 0
4.   norm = min(|smooth_buf| / 4.0, 1.0)           # max_speed = 4.0 rad/s for mouse mode
5.   curved = sign(smooth_buf) * LUT[int(norm * 999)] * 4.0
6.   dt_scale = dt_ms / 8.0  (if 0 < dt_ms < 50 else 1.0)
7.   pixel_delta = curved * (sens / 2.0) * resp * dt_scale
8.   mouse_acc += pixel_delta + touchpad_delta
9.   move_px = int(mouse_acc);  mouse_acc -= move_px
10.  Dispatch atomic mouse move (move_px_x, move_px_y) via Interception or Win32 SendInput
```

### Mode 3 (`Angle -> Stick`, 2-Axis Pure Gyro Tilt)

```
1.  if stick_or_touchpad_touched and recenter_on_stick_touch:
        gyro_angle_x = 0; gyro_angle_y = 0
2.  if hypot(val_x, val_y) < 0.015:                # velocity noise floor
        val_x = 0; val_y = 0
3.  gyro_angle_x = clamp(gyro_angle_x + val_x * (dt / max_tilt_rad), -1.5, 1.5)
    gyro_angle_y = clamp(gyro_angle_y - val_y * (dt / max_tilt_rad), -1.5, 1.5)
4.  if still for > 0.4s and recenter_speed > 0:
        decay = max(0.0, 1.0 - recenter_speed * dt)
        gyro_angle_x *= decay; gyro_angle_y *= decay
5.  rad_mag = min(hypot(gyro_angle_x, gyro_angle_y), 1.0)
6.  rescaled = 0 if rad_mag < dz else (rad_mag - dz) / (1.0 - dz)
7.  curved_mag = LUT[int(clamp(rescaled, 0, 1) * 999)]
8.  returning = curved_mag < hypot(smooth_tilt_x, smooth_tilt_y)
9.  eff_alpha = min(alpha * 1.5, 1.0) if returning else alpha
10. smooth_tilt += (target_tilt - smooth_tilt) * eff_alpha
11. Apply tilt_axes filter ("both", "horizontal", or "vertical")
12. Output clamp(int(smooth_tilt * 32767), -32768, 32767) to Gyro Target Stick
```

---

## Recommended Settings by Game Genre

### First-Person Shooters (Gamepad — Call of Duty, Halo, Apex Legends, Fortnite)

| Parameter | Recommended | Notes |
|-----------|-------------|-------|
| Controller Target | Xbox 360 or DualShock 4 | Use DS4 if feeding native gyro into Steam Input |
| Gyro Target Stick | Right Stick (Aim / Camera) | |
| Gyro Mode | 0 (Decay) or 1 (Snap) | Decay for aim-assist tracking; Snap for flick shots |
| Smoothing | `0.55–0.70` | Default `0.68` balances stability and speed |
| Deadzone | `0.06–0.12` | Lower if your phone sensor is clean |
| Response | `1.00x` | |
| Sensitivity | `70–90` | |
| Curve P1 / P2 | `(0.25, 0.08)` / `(0.75, 0.92)` | Precision low-end, fast flicks |

### First-Person Shooters (Mouse + Keyboard — CS2, Valorant, Overwatch 2, Apex MnK)

| Parameter | Recommended | Notes |
|-----------|-------------|-------|
| Input Mode (Phone) | MnK Mode | Automatically activates Mouse pipeline |
| Gyro Mode | 2 (Rate -> Mouse) | |
| Smoothing | `0.50–0.68` | Lower (`0.35–0.50`) for raw twitch aim |
| Deadzone | `0.05–0.10` | Tight threshold for pixel-level micro-aim |
| Sensitivity | `40–65` | Start around `50` and tune to match in-game DPI |
| Analog WASD Macro-Slicing | Enabled (if Left Stick used) | `180 ms` cycle window, `5°` deadband |

### Racing Games (Forza Horizon, Assetto Corsa, F1, Need for Speed)

| Parameter | Recommended | Notes |
|-----------|-------------|-------|
| Controller Target | Xbox 360 | |
| Gyro Target Stick | Left Stick (Movement / Steering) | Maps tilt directly to steering axis |
| Gyro Mode | 3 (Angle -> Stick / Tilt) | Pure gyro integration (no lateral G-force kicks) |
| Orientation | Landscape | Hold phone sideways like a steering wheel |
| Tilt Axes | Horizontal Only (Steer X) | Prevents forward/back tilt from affecting Y-axis |
| Deadzone | `0.02–0.05` | Immediate steering response around center |
| Smoothing | `0.65–0.75` | Asymmetric return keeps counter-steering snappy |
| Recenter Speed | `0.0` (Hold) or `1.5–2.5x` | `0.0` holds wheel angle; `1.5+` adds centering spring |
| Max Tilt ° | `30°–45°` (Arcade) / `60°+` (Sim) | |
| Curve P1 / P2 | `(0.25, 0.25)` / `(0.75, 0.75)` | Linear response for predictable cornering |
