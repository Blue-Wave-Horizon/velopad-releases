# VeloPad PC Client -- User Manual

This document is a complete reference for every control, knob, slider, graph, and setting in the VeloPad PC client. It covers all four gyro behavior modes at the signal-processing level, explains what each tuning parameter does to your input, and provides practical guidance for dialing in settings for different game genres.

---

## Table of Contents

1. [Launching the Application](#launching-the-application)
2. [Main Window Layout](#main-window-layout)
3. [Dashboard Page](#dashboard-page)
4. [Profiles Page](#profiles-page)
5. [Input Lab Page -- Overview](#input-lab-page----overview)
6. [The Four Tuning Knobs](#the-four-tuning-knobs)
   - [Sensitivity](#sensitivity)
   - [Deadzone](#deadzone)
   - [Smoothing](#smoothing)
   - [Response](#response)
7. [Gyro Behavior Modes](#gyro-behavior-modes)
   - [Mode 0: Rate to Stick (Decay)](#mode-0-rate-to-stick-decay)
   - [Mode 1: Rate to Stick (Snap)](#mode-1-rate-to-stick-snap)
   - [Mode 2: Rate to Mouse](#mode-2-rate-to-mouse)
   - [Mode 3: Angle to Stick (Tilt Steer)](#mode-3-angle-to-stick-tilt-steer)
8. [Axis Mapping](#axis-mapping)
9. [Response Curve Editor](#response-curve-editor)
10. [3D Phone Visualizer](#3d-phone-visualizer)
11. [Live Input Feed](#live-input-feed)
12. [Mode 3 Exclusive Controls](#mode-3-exclusive-controls)
    - [Filter Blend (Comp Alpha)](#filter-blend-comp-alpha)
    - [Recenter Speed](#recenter-speed)
    - [Max Tilt Angle](#max-tilt-angle)
    - [Phone Orientation](#phone-orientation)
13. [Settings Page](#settings-page)
14. [System Tray](#system-tray)
15. [Signal Processing Pipeline -- Technical Reference](#signal-processing-pipeline----technical-reference)
16. [Recommended Settings by Game Genre](#recommended-settings-by-game-genre)

---

## Launching the Application

Run the installed `VeloPad.exe`, or from source:

```
python -m pc_client.main
```

The application starts its network servers immediately. It listens for phone connections on UDP port 4000, TCP port 4001, and broadcasts discovery beacons on port 4002. If your firewall prompts you, allow VeloPad through on all three ports (and port 4003 for profile sync).

On launch, the window shows the Dashboard page. Closing the window minimizes VeloPad to the system tray instead of quitting. To fully exit, right-click the tray icon and select Quit.

---

## Main Window Layout

The window is split into two regions:

**Left sidebar (200 px, collapsible to 56 px)**: Contains four navigation buttons -- Dashboard, Profiles, Input Lab, and Settings. Below the navigation is a Connected Devices section that shows each connected phone with a colored status dot and a disconnect button. The sidebar can be collapsed to icon-only mode by clicking the toggle arrow at the top.

**Content area (right)**: Displays the selected page. The four main pages are stacked; only one is visible at a time. A fifth page (Layout Editor) appears when editing a profile's button layout.

---

## Dashboard Page

The Dashboard is divided into two halves: Player 1 (left) and Player 2 (right), separated by a thin vertical line. Each half shows the same set of information for its player slot.

### Stat Cards

Each player panel displays four stat cards in a 2x2 grid:

**Connection** (top-left): Shows "Connected" or "Disconnected" with a colored dot. The border turns green when connected, red when disconnected. The subtitle shows the transport type and device name (for example, "USB -- OnePlus 12" or "Wi-Fi -- 192.168.0.5").

**Active Profile** (top-right): Displays the name of the profile currently loaded on the phone. The subtitle shows the number of buttons mapped in that profile.

**Latency** (bottom-left): Displays the estimated round-trip latency in milliseconds. This value is calculated as a weighted running average: 90% of the previous estimate plus 10% of the latest inter-packet interval. A colored badge indicates quality:
- Green ("Good"): below 5 ms
- Amber ("OK"): 5 to 15 ms
- Red ("Warn"): above 15 ms

**Input Mode** (bottom-right): Shows either "Gamepad" or "MnK Mode" depending on the phone's current input mode.

### Latency Graph

Below the stat cards is a live latency graph that plots the last 60 data points. Each line segment is colored according to the same green/amber/red thresholds. Horizontal grid lines mark the 5 ms, 15 ms, and 25 ms levels for reference.

---

## Profiles Page

The Profiles page is split into a profile list on the left and a detail panel on the right.

### Profile List

All `.vpd` files in the `pc_client/profiles/` directory are listed here. Each item shows:
- A colored avatar with the profile's two-letter initials
- The profile name
- The number of mapped buttons
- A status badge: "active" (green) if it is the phone's current profile, or "on phone" (accent colored) if the profile exists on the phone

Clicking a profile selects it and shows its details on the right.

### Detail Panel

The right side shows:
- The profile name and status
- Statistics: button count, status (PC only / Active / On phone)
- A visual preview grid of the mapped buttons
- Action buttons:
  - **Edit Layout**: Opens the Layout Editor page where you can drag, resize, and configure widgets on a phone-shaped canvas
  - **Upload to Phone** (Pro only): Pushes the profile to the connected phone over TCP port 4003
  - **Delete**: Removes the profile file (the "Default" profile cannot be deleted)

### Top Bar Actions

- **+ New Profile**: Creates a new empty profile
- **Sync Remote Profiles** (Pro only): Queries the connected phone for its profile list and lets you download any of them to the PC

---

## Input Lab Page -- Overview

The Input Lab is the core tuning interface. It appears empty until a phone connects. Once a device is detected, a tab is created for it labeled with the player slot, transport, and device name (for example, "P1  USB -- OnePlus 12").

Each tab contains a single scrollable panel (the Device Lab Panel) with four sections stacked vertically:

1. **Tuning Knobs** -- four circular controls for Sensitivity, Deadzone, Smoothing, and Response
2. **Gyro Behavior and Axis Mapping** -- mode selector dropdown, axis dropdowns, inversion checkboxes, and Mode 3 sub-controls
3. **Response Curve and 3D Visualizer** -- side by side, with the Bezier curve editor on the left and the live phone model on the right
4. **Live Input Feed** -- a scrolling text log of button presses and raw input data

If two phones are connected, two tabs appear. Each tab's settings are completely independent. Adjusting a knob on the P1 tab has no effect on P2.

Settings are saved per device. VeloPad identifies devices by their stable Android ID (a persistent identifier that survives phone renames). When you reconnect the same phone, your tuning is restored automatically. Settings are stored in `%APPDATA%/VeloPad/config.vpd`.

---

## The Four Tuning Knobs

The knobs appear in a row at the top of the Device Lab Panel. Each knob is a 68x68 pixel circular widget. To adjust a knob, click and drag vertically: drag up to increase the value, drag down to decrease it. A full drag of 100 pixels covers the entire range. The current value is displayed in the center of the knob. The colored arc around the knob shows the value's position within its range.

All four knobs take effect immediately -- the signal processing pipeline reads their values on every incoming packet.

---

### Sensitivity

**Range**: 0 to 100 (integer)  
**Default**: 90

Sensitivity controls how much the camera (or stick, or mouse cursor) moves in response to a given amount of phone rotation. It is a pure multiplier applied after all other processing (deadzone, smoothing, and response curve) is complete.

**How it works internally**: The knob value is multiplied by 200 to produce an internal scaling factor. At the default of 90, the internal factor is 18,000. At 100, it is 20,000. At 50, it is 10,000. The curved gyro value (in radians per second, after smoothing and the response curve) is multiplied by this factor, and the result is added to the analog stick position.

For Rate to Mouse mode, the formula is slightly different: the curved gyro value is multiplied by `sensitivity * 200 / 400`, which simplifies to `sensitivity / 2`. This converts the gamepad-scale number into pixel-scale mouse movement.

**What this means in practice**: Higher sensitivity means less physical phone movement is needed to achieve full camera rotation in-game. Lower sensitivity gives you more precision but requires larger physical movements.

**Guidance**:
- If the camera moves too fast and you overshoot targets, lower it.
- If you have to swing your phone too far to turn around, raise it.
- For precision aiming in shooters, values between 60 and 80 work well.
- For fast-paced games where you need quick 180-degree turns, values between 85 and 100 are appropriate.

---

### Deadzone

**Range**: 0.00 to 0.20  
**Default**: 0.12

The deadzone defines a threshold below which gyro input is ignored entirely. Any rotation slower than this threshold produces zero output. This filters out micro-vibrations, hand tremor, and sensor noise that would otherwise cause the camera to drift when you are trying to hold still.

**How it works internally**: After reading the raw gyro rate for each axis (yaw and pitch), VeloPad checks whether the absolute value is less than the deadzone threshold. If it is, the value is replaced with zero before any further processing. In Modes 0 and 2, only the raw value is zeroed -- the smoothing buffer retains its previous value and decays naturally. In Mode 1 (Snap), both the raw value and the smoothing buffer are zeroed simultaneously, producing an instant snap to center.

In Mode 3 (Tilt Steer), the deadzone operates differently. The deadzone value is interpreted as a fraction of the normalized tilt angle (0.0 to 1.0). Angles below this fraction produce zero output. Angles above it are rescaled so that the output ramps smoothly from zero (at the deadzone boundary) to full deflection (at maximum tilt). The rescaling formula is:

```
output = (angle - deadzone) / (1.0 - deadzone)
```

This rescaling prevents a sudden jump in output at the deadzone boundary.

**What this means in practice**: A larger deadzone means you have to rotate the phone more deliberately before anything registers. This eliminates drift but reduces responsiveness for small corrections. A smaller deadzone makes the input more sensitive to small movements but may introduce drift if the phone's gyroscope has noisy readings.

**Guidance**:
- If the camera drifts slowly when you set the phone down or hold it still, increase the deadzone.
- If small aiming corrections feel unresponsive or "sticky," decrease the deadzone.
- The default of 0.12 works well for most devices. Phones with high-quality gyroscopes (such as recent flagship devices) can often use 0.06 to 0.08 without drift.
- If you are playing a racing game in Mode 3, the deadzone should be low (0.02 to 0.06) so that subtle steering adjustments register without delay.

---

### Smoothing

**Range**: 0.05 to 1.00  
**Default**: 0.35  
**Pro only** (locked in Free tier)

Smoothing applies an exponential moving average (EMA) filter to the gyro signal after the deadzone. This filter reduces jitter and makes aim movement feel fluid rather than twitchy.

**How it works internally**: On every incoming packet, the smoothed value is updated as:

```
smoothed = smoothed + (raw - smoothed) * smoothing_coefficient
```

The knob value IS the smoothing coefficient. A value of 1.0 means no filtering at all -- the smoothed value instantly becomes the raw value. A value of 0.05 means very heavy filtering -- only 5% of each new reading blends into the running average, making the output very sluggish but extremely stable.

In Mode 3 (Tilt Steer), the smoothing uses an asymmetric variant. When the output is heading back toward center (the user is straightening the wheel), the coefficient is multiplied by 1.5 (capped at 1.0) so the car responds faster to corrections. When pushing outward (adding more steering), the normal coefficient is used for fine control.

**What this means in practice**: Lower values produce a smoother, more "floaty" feel -- good for slow, deliberate aiming (sniping, precision placement) but bad for fast reactions. Higher values produce a sharper, more responsive feel -- good for twitch aiming and tracking fast-moving targets, but the output may feel jittery if your hands are not steady.

**Guidance**:
- For precision sniping and slow-paced games, use 0.15 to 0.25 for a smooth, stable feel.
- For fast-paced shooters where you need to track moving targets, use 0.35 to 0.50.
- For twitch shooters (arena shooters, fast competitive play), use 0.50 to 0.80.
- A value of 1.0 disables smoothing entirely. This is only useful for testing or if you have exceptionally steady hands.
- For racing games (Mode 3), the asymmetric smoothing means you can use moderately low values (0.20 to 0.35) without the wheel feeling sluggish on corrections.

---

### Response

**Range**: 0.50 to 3.00  
**Default**: 1.00  
**Pro only** (locked in Free tier)

The Response knob is a final multiplier applied to the output after the sensitivity scaling. It lets you boost or attenuate the overall output without changing the underlying sensitivity curve.

**How it works internally**: After the curved, smoothed gyro value is multiplied by the sensitivity factor, the result is further multiplied by the Response value. At the default of 1.00, this has no effect. At 2.00, all output is doubled. At 0.50, all output is halved.

**What this means in practice**: This is a coarse gain control. If you have your sensitivity, curve, and smoothing dialed in but the overall output is just a bit too slow or too fast, the Response knob lets you scale everything uniformly without disturbing the balance of your other settings.

**Guidance**:
- Leave this at 1.00 unless you need a final overall adjustment.
- Values above 1.50 are aggressive and will amplify any jitter in your setup.
- Values below 0.80 are useful if you want to keep a high sensitivity for big movements but reduce overall output speed.

---

## Gyro Behavior Modes

The Gyro Mode dropdown selects how phone rotation is translated into game input. There are four modes. In the Free tier, only Mode 0 is available. Pro unlocks all four.

---

### Mode 0: Rate to Stick (Decay)

**Output**: Right analog stick (gamepad)  
**Best for**: General purpose gamepad games, shooters with aim assist, third-person games

This is the default mode. The phone's angular velocity (how fast you are rotating it) is converted into a right-stick deflection. The faster you rotate, the further the stick deflects. When you stop rotating, the stick smoothly decays back to center.

**Signal flow**:
1. Raw gyro rates (radians per second) are read from the packet for the configured yaw and pitch axes.
2. Axis inversion is applied if enabled.
3. Deadzone filtering: if the absolute rate is below the deadzone, the raw value is set to zero. The smoothing buffer is NOT reset -- it retains its previous value.
4. EMA smoothing: the smoothed value blends toward the new raw value by the smoothing coefficient.
5. Response curve: the smoothed value is mapped through the cubic Bezier lookup table (see the Response Curve Editor section below).
6. Sensitivity scaling: the curved value is multiplied by `sensitivity * 200`.
7. The result is added to any existing right-stick input from the phone's on-screen joystick and touchpad (touchpad contributes `delta_x * 400`).
8. The final value is clamped to the gamepad range of -32768 to 32767.

**Key characteristic**: Because the smoothing buffer is not reset when input drops below the deadzone, the stick gently decays back to center rather than jumping to zero. This produces smooth, natural-feeling camera movement with no jarring stops.

---

### Mode 1: Rate to Stick (Snap)

**Output**: Right analog stick (gamepad)  
**Best for**: Fast-paced shooters, flick shots, competitive play  
**Pro only**

This mode is identical to Mode 0 with one difference: when the gyro input drops below the deadzone, both the raw value AND the smoothing buffer are instantly zeroed. The stick snaps back to center the moment you stop rotating.

**Signal flow**: Same as Mode 0, except at step 3: when the absolute rate falls below the deadzone, the smoothing buffer is also set to zero. This means the output jumps to zero immediately rather than decaying gradually.

**Key characteristic**: The snap-back eliminates any trailing camera drift after a flick. When you flick-aim at a target and stop, the stick is instantly centered. This is more responsive for fast flick-and-stop aiming patterns, but the sudden zeroing can feel abrupt if you are making slow, sweeping movements.

**When to choose Decay vs Snap**:
- Use Decay if your game has strong aim assist or if you prefer a smooth, cinematic camera feel.
- Use Snap if you rely on flick aiming or need the camera to stop moving the instant you stop rotating.

---

### Mode 2: Rate to Mouse

**Output**: System mouse cursor  
**Best for**: Mouse-and-keyboard games, desktop navigation, FPS games without gamepad aim assist  
**Pro only**

In this mode, the phone's angular velocity moves the system mouse cursor. Unlike the stick modes, the cursor stays where you left it -- there is no spring-back to center. Rotating right moves the cursor right. Stopping rotation stops the cursor. Rotating left moves it back.

**Signal flow**:
1. Deadzone filtering (same as Mode 0).
2. EMA smoothing (same as Mode 0).
3. Response curve mapping (same as Mode 0).
4. The curved value is scaled by `sensitivity * 200 / 400` (which equals `sensitivity / 2`) to convert from gamepad-scale to pixel-scale.
5. The scaled value is accumulated in a floating-point buffer. Only the integer portion is extracted and sent as a mouse move command. The fractional remainder is preserved for the next frame. This sub-pixel accumulation prevents small movements from being rounded to zero.
6. The system mouse cursor is moved by the extracted integer pixels using pynput.
7. The right analog stick receives only touchpad and manual stick input (no gyro contribution).

**Key characteristic**: Because mouse movement is absolute (no spring-back), this mode behaves like a traditional mouse. Small adjustments require small rotations. Large turns require sustained rotation. The sub-pixel accumulation ensures that even very slow rotations eventually translate into cursor movement.

**Guidance**:
- For desktop-class FPS games (CS2, Valorant, Apex Legends on MnK), this is the mode to use.
- Set sensitivity lower than you would for stick modes because mouse movement is absolute. Start around 40-60 and adjust up if needed.
- Pair this with a Touchpad widget on the phone for additional mouse control (the touchpad's pixel deltas are added directly to the gyro's mouse output).

---

### Mode 3: Angle to Stick (Tilt Steer)

**Output**: Left analog stick X-axis (gamepad)  
**Best for**: Racing games, flight simulators, any game where tilt steering is natural  
**Pro only**

This mode tracks the phone's physical tilt angle rather than its rotation speed. Tilting the phone 20 degrees to the right produces a proportional rightward stick deflection. Holding the phone at that angle keeps the stick deflected. Returning the phone to level brings the stick back to center. The output maps to the LEFT stick X-axis (horizontal), which is the steering axis in most racing games.

This mode uses a complementary filter to fuse gyroscope and accelerometer data for stable angle tracking. See the [Mode 3 Exclusive Controls](#mode-3-exclusive-controls) section for the additional knobs that appear when this mode is selected.

**Signal flow**:
1. Deadzone filtering on raw gyro rates (same as other modes -- prevents sensor noise from accumulating in the integrator).
2. **Accelerometer angle calculation**: The accelerometer's gravity vector is used to calculate the phone's tilt angle relative to vertical. The angle is computed as `atan2(accel_axis, accel_z)` and normalized by dividing by the max tilt angle (in radians). The result is clamped to [-1.0, 1.0], where -1.0 means full tilt left and 1.0 means full tilt right.
3. **Gyroscope integration**: The gyro rate is multiplied by `dt / max_tilt_radians` and accumulated into a running angle estimate. This captures fast rotations that the accelerometer misses.
4. **Complementary filter**: The two angle estimates are blended:
   ```
   angle = comp_alpha * (gyro_integrated_angle) + (1 - comp_alpha) * (accel_angle)
   ```
   The comp_alpha parameter (default 0.98) determines how much to trust the gyroscope vs the accelerometer. A high value (close to 1.0) trusts the gyroscope more, giving responsive, low-latency tracking. The accelerometer contribution prevents long-term gyro drift.
5. **Auto-recenter**: When no gyro input is detected for more than 400 ms (the phone is stationary), the angle estimate fades toward zero at a rate controlled by the Recenter Speed knob. This prevents permanent offset if the phone drifts slightly while idle.
6. **Angle deadzone with rescaling**: The deadzone is applied to the fused angle (not the rate). Angles smaller than the deadzone fraction produce zero output. Angles larger than the deadzone are rescaled so that the output ramps from zero at the deadzone boundary to 1.0 at full tilt. This eliminates the jump that would occur with a simple threshold.
7. **Angle-native response curve**: The rescaled angle is mapped through the same cubic Bezier lookup table as other modes, but using the full [0, 1] index range. This gives Mode 3 access to the entire response curve shape.
8. **Asymmetric EMA smoothing**: The curved angle is smoothed with an EMA filter. When the output is returning toward center (the user is straightening the phone), the smoothing coefficient is multiplied by 1.5 for faster response. When pushing outward, the normal coefficient is used for fine control.
9. **Output**: The smoothed angle is multiplied by 32767 to produce a left-stick X-axis value in the gamepad range. The Y-axis passes through from the phone's on-screen joystick.

**Key characteristics**:
- Absolute angle tracking: the stick position corresponds to the phone's physical tilt
- Gyroscope provides responsive, low-latency angle changes
- Accelerometer prevents long-term drift (gravity reference)
- Auto-recenter returns the stick to center when the phone is still
- Asymmetric smoothing makes corrections feel snappy while maintaining fine control when steering into a turn

---

## Axis Mapping

Below the gyro mode dropdown are two columns for axis mapping:

### Yaw (Horizontal / Left-Right)

**Axis dropdown**: Selects which physical gyro axis maps to horizontal camera movement. Options are X, Y (default), and Z.
- **Y**: Rotation around the vertical axis. This corresponds to rotating the phone like a steering wheel when held upright, or panning left-right when held in front of you. This is the natural choice for most people.
- **X**: Rotation around the side-to-side axis. This corresponds to tilting the phone forward and backward.
- **Z**: Rotation around the front-to-back axis. This corresponds to rolling the phone left and right.

**Invert X (Yaw) checkbox**: When checked, the horizontal direction is reversed. If the camera moves left when you rotate right, toggle this checkbox. Unchecked by default.

### Pitch (Vertical / Up-Down)

**Axis dropdown**: Selects which physical gyro axis maps to vertical camera movement. Options are X (default), Y, and Z.
- **X**: Rotation around the side-to-side axis. This corresponds to tilting the phone forward (look down) and backward (look up). This is the natural choice for most people.

**Invert Y (Pitch) checkbox**: When checked, the vertical direction is reversed. Checked by default because the natural gyro direction (tilting forward = positive X rotation) maps to looking down in most games, which matches the inverted-Y convention many players use. If you prefer non-inverted vertical (tilting forward = look up), uncheck this.

---

## Response Curve Editor

To the left of the 3D visualizer is the Response Curve graph. This is a cubic Bezier curve that remaps your gyro input from a linear relationship (1:1 between phone rotation speed and output) to a custom curve.

### What the Graph Shows

The horizontal axis represents input intensity (how fast you are rotating the phone). The vertical axis represents output intensity (how much stick deflection or mouse movement is produced). A straight diagonal line from bottom-left to top-right is a linear response: slow rotations produce proportionally slow output, fast rotations produce proportionally fast output.

A dashed diagonal line shows this linear reference. The solid colored curve shows your current response mapping.

### How to Use It

There are two control points (small circles) that you can drag to shape the curve:

**Control Point 1** (lower-left region): Defaults to (0.25, 0.10). This controls the curve's behavior for small, slow inputs. Dragging this point downward makes slow rotations produce even less output -- giving you more precision at low speeds. Dragging it upward makes slow rotations produce more output -- making fine aim more responsive but less precise.

**Control Point 2** (upper-right region): Defaults to (0.75, 0.90). This controls the curve's behavior for fast inputs. Dragging this point upward makes fast rotations produce maximum output sooner -- useful for fast flicks. Dragging it downward makes fast rotations produce less output -- useful if you overshoot with fast movements.

### Common Curve Shapes

**Aggressive S-curve** (P1 low, P2 high): The default settings (0.25, 0.10) and (0.75, 0.90) create a mild S-curve. Small inputs are dampened (more precision), while fast inputs are amplified (faster flicks). This is the most popular shape for shooters.

**Linear** (P1 at 0.25, 0.25 and P2 at 0.75, 0.75): A straight diagonal line. Output is directly proportional to input at all speeds. Good for racing games and situations where you want predictable, uniform response.

**Exponential** (P1 very low, P2 moderately high): Small inputs produce almost no output; large inputs produce disproportionately large output. Extreme precision at low speeds, but fast movements can feel sudden.

**Logarithmic** (P1 high, P2 moderately low): Small inputs produce large output; large inputs produce diminishing returns. Very responsive for small corrections, but hard to make fast turns. Rarely useful for gaming.

### Technical Details

The curve is a standard cubic Bezier with four points: (0, 0), (P1x, P1y), (P2x, P2y), and (1, 1). VeloPad evaluates this curve at 5,000 uniformly spaced parameter values, then downsamples the result into a 1,000-point lookup table via linear interpolation. On every incoming packet, the normalized input value is used as an index into this table to look up the output value. This makes the curve evaluation extremely fast (a single array lookup per axis per frame).

For Modes 0, 1, and 2, the input is normalized by dividing the smoothed gyro rate by a maximum speed constant of 4.0 rad/s. This means the curve's full range corresponds to 0 to 4 rad/s of rotation. Rotations faster than 4 rad/s are clamped to the maximum output. At typical usage, moderate aiming movements are around 0.5 to 2.0 rad/s, so the lower half of the curve is where most of your aiming lives.

For Mode 3 (Tilt Steer), the input is the normalized tilt angle (0.0 to 1.0), so the entire curve range is used directly. This gives Mode 3 much finer control over the curve shape compared to the rate modes.

**Pro only**: The response curve editor is locked in the Free tier. Free users get the default S-curve.

---

## 3D Phone Visualizer

To the right of the response curve is a live 3D wireframe model of a phone that rotates in real time as you move your physical phone. This serves two purposes: it confirms that gyro data is being received, and it helps you verify your axis mapping and inversion settings are correct.

### What It Shows

The wireframe model is a rectangular box (proportioned like a phone in portrait orientation) rendered with a perspective projection. The front face is brighter than the back face. A small screen area and camera notch are visible on the front. Colors are based on the current accent color.

### Rotation

The model rotates according to the raw gyro data received from the phone. Pitch (tilting forward/backward), Yaw (rotating left/right), and Roll (tilting sideways) are all tracked. The rotation uses intrinsic ZYX order (Roll, then Yaw, then Pitch).

Below the visualizer, three numeric readouts show the current Pitch, Roll, and Yaw angles in degrees.

### Axis Gizmo

A small 3D axis indicator in the bottom-left corner shows three colored arrows:
- Red: X axis
- Green: Y axis
- Blue (or accent color): Z axis

These arrows rotate with the phone model, helping you identify which physical axis corresponds to which direction.

### Idle Animation

If no gyro data is received for 3 seconds (phone disconnected or paused), the model automatically begins a slow idle spin to indicate it is waiting for input.

---

## Live Input Feed

At the bottom of the Device Lab Panel is a scrolling text log that shows live input activity. Each entry is timestamped and shows the button or input name, the action (pressed/released), and the latency.

Format: `[HH:MM:SS] KEY ACTION latency_ms`

The latency value is color-coded:
- Green: 5 ms or less
- Amber: 6 to 15 ms
- Red: above 15 ms

The feed is limited to the most recent 100 lines and scrolls automatically. It updates approximately once per second.

This feed is useful for:
- Confirming that button presses from the phone are reaching the PC
- Checking which gamepad buttons or keyboard keys are being triggered
- Monitoring latency in real time
- Debugging mapping issues (seeing the raw key codes)

---

## Mode 3 Exclusive Controls

When Mode 3 (Angle to Stick / Tilt Steer) is selected, an additional sub-panel appears with three extra knobs and a phone orientation selector. These controls are specific to the complementary filter and tilt tracking algorithm.

---

### Filter Blend (Comp Alpha)

**Range**: 0.90 to 0.99  
**Default**: 0.98

This knob controls the balance between the gyroscope and accelerometer in the complementary filter. The value represents how much the filter trusts the gyroscope.

- **0.98 (default)**: 98% gyroscope, 2% accelerometer. The gyro provides responsive, low-latency angle tracking. The small accelerometer contribution slowly corrects any gyro drift over time. This is the recommended setting for most use.
- **0.99**: Almost pure gyroscope. Extremely responsive but may accumulate slight drift over long sessions if the phone is moved very slowly.
- **0.90**: 90% gyro, 10% accelerometer. The accelerometer has more influence, which makes the angle estimate more stable but introduces a slight "rubbery" feel because the accelerometer is noisy during fast movements.

**Guidance**:
- For racing games, keep this at 0.97 to 0.99. You want the steering to feel immediate and direct.
- If you notice the steering drifting off-center over time, lower it slightly (to 0.96 or 0.95) to let the accelerometer correct the drift faster.
- Do not go below 0.93 unless you are experiencing severe drift problems, as it will make the steering feel sluggish.

---

### Recenter Speed

**Range**: 0.1 to 3.0  
**Default**: 1.0

This knob controls how fast the steering automatically returns to center when the phone is stationary. When no gyro movement is detected for 400 milliseconds, VeloPad begins fading the current angle estimate toward zero. The Recenter Speed value controls how fast this fade happens.

**How it works**: On each frame during the still period, the current angle is multiplied by `(1.0 - recenter_speed * dt)`, where `dt` is the frame time (approximately 8 ms at 125 Hz). At the default of 1.0, the angle fades by roughly 0.8% per frame, reaching near-zero in about 1 to 2 seconds. At 3.0, the fade is three times faster (roughly 200-400 ms to re-center). At 0.1, the fade is very slow (8-10 seconds).

**Guidance**:
- For racing games where you want the wheel to snap back to center quickly (like a real steering wheel with a centering spring), use 1.5 to 3.0.
- For flight simulators where you want gentle, gradual recentering, use 0.3 to 0.7.
- A value of 0.1 is almost no auto-centering -- the steering will hold its angle for a very long time after you stop moving. This is useful if you are making sustained turns and do not want any recentering interference.

---

### Max Tilt Angle

**Range**: 10 to 90 degrees  
**Default**: 45 degrees

This defines the physical tilt angle (in degrees) that corresponds to full stick deflection. Tilting the phone to this angle or beyond produces 100% stick output.

**How it works**: The accelerometer angle and gyroscope integration are both scaled relative to this value. If Max Tilt is 45 degrees, then tilting the phone 22.5 degrees to the right produces approximately 50% rightward stick deflection (before the response curve is applied). Tilting it the full 45 degrees produces 100%.

**Guidance**:
- **45 degrees (default)**: A comfortable balance. You get full steering range without having to tilt the phone uncomfortably far. Good for most racing games.
- **20 to 30 degrees**: The full steering range is compressed into a smaller physical range. Less phone movement is needed for sharp turns, but fine control is reduced. Good for games with fast, twitchy steering (kart racers, arcade racers).
- **60 to 90 degrees**: The full steering range requires a large tilt. This gives you very fine control over small corrections but requires larger physical movements for sharp turns. Good for simulation racers where precision matters more than speed.

---

### Phone Orientation

**Options**: Portrait (default), Landscape

This tells VeloPad how you are holding the phone.

- **Portrait**: The phone is upright. Tilting it left-right (around the X accelerometer axis) controls yaw (steering), and tilting it forward-backward (around the Y axis) controls pitch.
- **Landscape**: The phone is on its side. Tilting it left-right (around the Y accelerometer axis) controls yaw, and tilting it forward-backward (around the X axis) controls pitch.

The orientation setting only affects Mode 3's accelerometer mapping. The gyro axis assignment uses the separate Yaw/Pitch axis dropdowns in the Axis Mapping section.

---

## Settings Page

The Settings page contains global application preferences organized into cards.

### Theme Mode

Three options: Light, Dark, and System. "System" follows your Windows appearance setting (reads from the Windows registry key `HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Themes\Personalize\AppsUseLightTheme`). Changes take effect immediately.

### Accent Color

A grid of 16 color swatches. Clicking one changes the accent color throughout the entire application -- navigation highlights, knob arcs, graph curves, status indicators, and the 3D visualizer. The available colors are:

| Color | Hex Code |
|-------|----------|
| Blue (default) | #007AFF |
| Sky | #32ADE6 |
| Indigo | #5856D6 |
| Violet | #BF5AF2 |
| Pink | #FF2D92 |
| Rose | #FF375F |
| Red | #FF3B30 |
| Coral | #FF6B6B |
| Orange | #FF9500 |
| Yellow | #FFCC00 |
| Amber | #FFB800 |
| Green | #34C759 |
| Mint | #00C7BE |
| Teal | #30B0C7 |
| Purple | #AF52DE |
| Lime | #A8D600 |

### Server Port

The base UDP port for input packets. Default is 4000. The other ports (4001, 4002, 4003) are derived from this base. You should only change this if port 4000 is already in use by another application.

### Auto-Start

When enabled, VeloPad starts automatically when Windows starts.

### ADB Auto-Tunnel

When enabled, VeloPad automatically sets up ADB reverse tunnels when it detects a USB-connected phone. The tunnels map TCP ports 4001 and 4003 from the phone to the PC. This requires ADB to be installed and accessible (VeloPad searches PATH, `%LOCALAPPDATA%\Android\Sdk\platform-tools\adb.exe`, and `C:\platform-tools\adb.exe`).

### Update Check

A button that checks for new versions of VeloPad.

---

## System Tray

VeloPad runs in the system tray when the main window is closed. The tray icon indicates the current state:

- **Searching**: An animated wave pattern (three chevrons cascading top-to-bottom in a 0.9-second cycle). This is shown when no phone is connected.
- **Connected**: A static icon with a green dot in the bottom-right corner. The tooltip shows "VeloPad: Connected (Device Name)".
- **Disconnected**: A static icon with a red dot.

The tray icon is always rendered in the brand blue (#007AFF) regardless of the accent color setting.

**Right-click menu**:
- Show VeloPad: Brings the main window to the foreground
- Hide to Tray: Hides the main window
- Quit: Fully exits VeloPad (saves all settings before closing)

**Double-click**: Toggles the main window visibility.

---

## Signal Processing Pipeline -- Technical Reference

This section provides the exact mathematical pipeline for each gyro mode. All variables are per-axis (yaw and pitch are processed identically unless noted otherwise).

### Variables

| Symbol | Source | Description |
|--------|--------|-------------|
| `raw` | Packet field `gx`, `gy`, or `gz` | Raw gyro rate in rad/s |
| `dz` | Deadzone knob | Deadzone threshold |
| `sm` | Smoothing knob | EMA coefficient (0.05 to 1.0) |
| `sens` | Sensitivity knob | Knob value (0 to 100) |
| `resp` | Response knob | Final multiplier (0.5 to 3.0) |
| `LUT[i]` | Response curve editor | 1000-point cubic Bezier lookup table |
| `buf` | Internal | EMA smoothing buffer (per axis) |
| `acc` | Internal (Mode 2 only) | Mouse sub-pixel accumulator |

### Mode 0 (Rate to Stick, Decay)

```
1.  val = select_axis(raw_gx, raw_gy, raw_gz, configured_axis)
2.  if invert: val = -val
3.  if |val| < dz: val = 0
4.  buf += (val - buf) * sm
5.  norm = min(|buf| / 4.0, 1.0)
6.  curved = sign(buf) * LUT[floor(norm * 999)] * 4.0
7.  output = curved * sens * 200 * resp
8.  stick = existing_stick + output + touchpad_delta * 400
9.  stick = clamp(stick, -32768, 32767)
```

### Mode 1 (Rate to Stick, Snap)

```
1-2.  Same as Mode 0
3.    if |val| < dz: val = 0; buf = 0          <-- buffer is ALSO zeroed
4-9.  Same as Mode 0
```

### Mode 2 (Rate to Mouse)

```
1-5.  Same as Mode 0 (steps 1-5)
6.    curved = sign(buf) * LUT[floor(norm * 999)] * 4.0
7.    pixel_delta = curved * sens * 200 / 400 * resp
8.    acc += pixel_delta
9.    move = floor(acc)
10.   acc -= move
11.   if move != 0: mouse.move(move)
12.   stick = existing_stick + touchpad_delta * 400    <-- no gyro on stick
```

### Mode 3 (Angle to Stick, Tilt)

```
1.   val = select_axis(raw_gx, raw_gy, raw_gz, configured_axis)
2.   if invert: val = -val
3.   if |val| < dz: val = 0
4.   accel_angle = clamp(atan2(accel_axis, accel_z) / max_tilt_rad, -1, 1)
5.   gyro_angle += val * (dt / max_tilt_rad)
6.   fused = clamp(comp_alpha * gyro_angle + (1 - comp_alpha) * accel_angle, -1, 1)
7.   if still for > 0.4s: fused *= (1 - recenter_speed * dt)
8.   if |fused| < dz: out = 0
     else: out = sign(fused) * (|fused| - dz) / (1 - dz)
9.   curved = sign(out) * LUT[floor(|out| * 999)]
10.  returning = |curved| < |smooth_buf|
11.  alpha = min(sm * 1.5, 1.0) if returning else sm
12.  smooth_buf += (curved - smooth_buf) * alpha
13.  stick_x = clamp(floor(smooth_buf * 32767), -32768, 32767)
```

---

## Recommended Settings by Game Genre

The following are starting points. Every player and phone combination is different, so treat these as a baseline and adjust to taste.

### First-Person Shooters (Gamepad -- e.g., Call of Duty, Halo, Destiny 2)

| Parameter | Recommended | Notes |
|-----------|-------------|-------|
| Gyro Mode | 0 (Decay) or 1 (Snap) | Decay for aim-assist games, Snap for flick-heavy play |
| Sensitivity | 70-85 | Lower for precision, higher for fast turning |
| Deadzone | 0.08-0.12 | Lower if your phone gyro is clean |
| Smoothing | 0.30-0.45 | Balance between precision and responsiveness |
| Response | 1.00 | Leave at default unless fine-tuning |
| Curve P1 | (0.25, 0.08) | Dampen small inputs for precision |
| Curve P2 | (0.75, 0.92) | Amplify fast flicks |

### First-Person Shooters (Mouse -- e.g., CS2, Valorant, Apex Legends MnK)

| Parameter | Recommended | Notes |
|-----------|-------------|-------|
| Gyro Mode | 2 (Rate to Mouse) | |
| Sensitivity | 40-65 | Mouse mode is more sensitive per unit; start low |
| Deadzone | 0.06-0.10 | Keep tight for fine aim |
| Smoothing | 0.25-0.40 | Slightly lower than gamepad mode |
| Response | 1.00 | |
| Curve P1 | (0.25, 0.15) | Moderate precision zone |
| Curve P2 | (0.75, 0.85) | Moderate fast-input amplification |

### Racing Games (e.g., Forza Horizon, Need for Speed, Gran Turismo)

| Parameter | Recommended | Notes |
|-----------|-------------|-------|
| Gyro Mode | 3 (Tilt Steer) | |
| Sensitivity | N/A | Mode 3 does not use the Sensitivity knob for output |
| Deadzone | 0.03-0.06 | Very low for subtle steering |
| Smoothing | 0.25-0.35 | Asymmetric smoothing handles corrections |
| Response | 1.00 | |
| Curve P1 | (0.25, 0.25) | Linear or near-linear for predictable steering |
| Curve P2 | (0.75, 0.75) | |
| Filter Blend | 0.97-0.99 | High for responsive steering |
| Recenter Speed | 1.5-2.5 | Fast recenter for snappy feel |
| Max Tilt | 30-45 degrees | 30 for arcade, 45 for simulation |
| Orientation | Landscape | Hold the phone sideways like a wheel |

### Third-Person Action (e.g., Elden Ring, God of War, GTA V)

| Parameter | Recommended | Notes |
|-----------|-------------|-------|
| Gyro Mode | 0 (Decay) | Smooth camera for exploration |
| Sensitivity | 75-90 | Moderate-high for comfortable panning |
| Deadzone | 0.10-0.14 | Moderate to avoid accidental camera drift |
| Smoothing | 0.30-0.40 | Smooth, cinematic feel |
| Response | 1.00 | |
| Curve | Default S-curve | |

### Simulation / Flight (e.g., Microsoft Flight Simulator, Ace Combat)

| Parameter | Recommended | Notes |
|-----------|-------------|-------|
| Gyro Mode | 3 (Tilt Steer) | |
| Deadzone | 0.02-0.05 | Very low for gentle inputs |
| Smoothing | 0.20-0.30 | Smooth, steady control |
| Curve P1 | (0.25, 0.20) | Slightly dampened low end |
| Curve P2 | (0.75, 0.80) | Slightly dampened high end |
| Filter Blend | 0.98 | |
| Recenter Speed | 0.3-0.5 | Slow -- hold angles for sustained maneuvers |
| Max Tilt | 60-90 degrees | Large range for full roll/pitch control |
| Orientation | Landscape | |

### Platformers and Casual Games

| Parameter | Recommended | Notes |
|-----------|-------------|-------|
| Gyro Mode | 0 (Decay) | |
| Sensitivity | 50-70 | Lower for gentle camera |
| Deadzone | 0.12-0.16 | Higher to prevent accidental movement |
| Smoothing | 0.20-0.30 | Very smooth |
| Response | 1.00 | |
| Curve | Linear | Predictable response |
