# Space Mouse OSC - Presets and Parameters

This document explains how the different presets map to the 6 degrees of freedom (6DOF) available on a 3Dconnexion space mouse, along with the scaling and other parameters.

---

## Table of Contents

1. [Space Mouse Axes](#space-mouse-axes)
2. [Preset Array Structure](#preset-array-structure)
3. [Available Presets](#available-presets)
4. [Parameter Controls](#parameter-controls)
5. [Scaling and Value Processing](#scaling-and-value-processing)
6. [OSC Message Interface](#osc-message-interface)

---

## Space Mouse Axes

A space mouse provides 6 axes of control:

| Index | Axis Name | Type | Description |
|-------|-----------|------|-------------|
| 0 | X | Translation | Left (-) / Right (+) movement |
| 1 | Y | Translation | Down (-) / Up (+) movement |
| 2 | Z | Translation | Away (-) / Towards (+) movement |
| 3 | Pitch | Rotation | Tilting forward (-) / backward (+) |
| 4 | Yaw | Rotation | Turning left (-) / right (+) |
| 5 | Roll | Rotation | Rolling counter-clockwise (-) / clockwise (+) |

---

## Preset Array Structure

Each preset is defined as an array of 6 elements corresponding to the axes above. An element can be:
- A string like `"x++"`, `"pitch"`, `"azim++"` - enables the axis with that OSC path
- `null` - disables that axis

The `++` suffix indicates relative/accumulated values (origin resets on button press).

---

## Available Presets

### 1. AED (Azimuth-Elevation-Distance)

```json
[null, null, null, "elev++", "azim++", "dist++"]
```

| Axis | Status | OSC Path | Description |
|------|--------|----------|-------------|
| X | Disabled | - | - |
| Y | Disabled | - | - |
| Z | Disabled | - | - |
| Pitch | **Enabled** | `/elev++` | Elevation (vertical angle) |
| Yaw | **Enabled** | `/azim++` | Azimuth (horizontal angle) |
| Roll | **Enabled** | `/dist++` | Distance (radial) |

**Use case:** Spherical coordinate control - ideal for camera or object positioning in 3D space using polar coordinates.

---

### 2. AD (Azimuth-Distance)

```json
[null, null, null, null, "azim++", "dist++"]
```

| Axis | Status | OSC Path | Description |
|------|--------|----------|-------------|
| X | Disabled | - | - |
| Y | Disabled | - | - |
| Z | Disabled | - | - |
| Pitch | Disabled | - | - |
| Yaw | **Enabled** | `/azim++` | Azimuth (horizontal angle) |
| Roll | **Enabled** | `/dist++` | Distance (radial) |

**Use case:** 2D polar control - simplified spherical without elevation, useful for applications that don't need vertical control.

---

### 3. XYZ (3D Translation)

```json
["x++", "y++", "z++", null, null, null]
```

| Axis | Status | OSC Path | Description |
|------|--------|----------|-------------|
| X | **Enabled** | `/x++` | X translation |
| Y | **Enabled** | `/y++` | Y translation |
| Z | **Enabled** | `/z++` | Z translation |
| Pitch | Disabled | - | - |
| Yaw | Disabled | - | - |
| Roll | Disabled | - | - |

**Use case:** Pure translation control - positions objects in 3D Cartesian space without rotation. Good for panning/moving objects.

---

### 4. XY (2D Translation)

```json
["x++", "y++", null, null, null, null]
```

| Axis | Status | OSC Path | Description |
|------|--------|----------|-------------|
| X | **Enabled** | `/x++` | X translation |
| Y | **Enabled** | `/y++` | Y translation |
| Z | Disabled | - | - |
| Pitch | Disabled | - | - |
| Yaw | Disabled | - | - |
| Roll | Disabled | - | - |

**Use case:** 2D plane control - simplified translation for applications that work on a flat surface (X-Y plane).

---

### 5. Custom1 (All Axes - Raw Names)

```json
["x", "y", "z", "pitch", "roll", "yaw"]
```

| Axis | Status | OSC Path | Description |
|------|--------|----------|-------------|
| X | **Enabled** | `/x` | X translation (absolute) |
| Y | **Enabled** | `/y` | Y translation (absolute) |
| Z | **Enabled** | `/z` | Z translation (absolute) |
| Pitch | **Enabled** | `/pitch` | Pitch rotation (absolute) |
| Yaw | **Enabled** | `/yaw` | Yaw rotation (absolute) |
| Roll | **Enabled** | `/roll` | Roll rotation (absolute) |

**Use case:** Full 6DOF control with absolute values. No `++` suffix means values don't accumulate - each motion reports the current position.

---

### 6. Custom2 (Rotation Only)

```json
[null, null, null, "pitch", "roll", "yaw"]
```

| Axis | Status | OSC Path | Description |
|------|--------|----------|-------------|
| X | Disabled | - | - |
| Y | Disabled | - | - |
| Z | Disabled | - | - |
| Pitch | **Enabled** | `/pitch` | Pitch rotation (absolute) |
| Yaw | **Enabled** | `/yaw` | Yaw rotation (absolute) |
| Roll | **Enabled** | `/roll` | Roll rotation (absolute) |

**Use case:** Rotation-only control - useful when you only need to orient objects without translating them.

---

### 7. Custom3 (3D Translation Only - Absolute)

```json
["x", "y", "z", null, null, null]
```

| Axis | Status | OSC Path | Description |
|------|--------|----------|-------------|
| X | **Enabled** | `/x` | X translation (absolute) |
| Y | **Enabled** | `/y` | Y translation (absolute) |
| Z | **Enabled** | `/z` | Z translation (absolute) |
| Pitch | Disabled | - | - |
| Yaw | Disabled | - | - |
| Roll | Disabled | - | - |

**Use case:** 3D translation with absolute values - positions objects in Cartesian space without rotation.

---

## Summary Table

| Preset | Translation |  |  | Rotation |  |  | Value Type |
|--------|-------------|--|--|----------|--|--|------------|
| | X | Y | Z | Pitch | Yaw | Roll | |
| AED | - | - | - | elev | azim | dist | Relative |
| AD | - | - | - | - | azim | dist | Relative |
| XYZ | x | y | z | - | - | - | Relative |
| XY | x | y | - | - | - | - | Relative |
| Custom1 | x | y | z | pitch | yaw | roll | Absolute |
| Custom2 | - | - | - | pitch | yaw | roll | Absolute |
| Custom3 | x | y | z | - | - | - | Absolute |

---

## Parameter Controls

### GUI Parameters

The application exposes the following parameters through the GUI:

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| **Prefix** | Text | `/track` | OSC address prefix (e.g., `/track/elev++`) |
| **Index** | Number | `1` | Index number appended to OSC paths |
| **Factor** | Number | `1` | Scaling multiplier (0.1 to 100) |
| **Send Rate** | Number | `33` | Percentage of maximum data send rate (1-100) |
| **Precision** | Dropdown | `100000` | Decimal precision (1, 10, 100, 1000, 100000, clear) |

### Hidden/Advanced Parameters

These parameters are stored in the preferences but not directly exposed in the main GUI:

| Parameter | Default | Format | Description |
|-----------|---------|--------|-------------|
| **xyzOriginAndRange** | `[[0,0,0],[-10,10]]` | `[[originX,originY,originZ], [min,max]]` | Origin and range for XYZ translation (in meters) |
| **rpyOriginAndRange** | `[[0,0,0],[-180,180]]` | `[[originRoll,originPitch,originYaw], [min,max]]` | Origin and range for Roll/Pitch/Yaw (in degrees) |
| **absolute_relative** | `true` | boolean | Switches between absolute and relative mode |

---

## Scaling and Value Processing

### Value Flow

The raw HID data from the space mouse goes through several transformations:

```
Raw HID Data (0-350 range typically)
    ↓
Cubed: value³
    ↓
Multiplied by 5
    ↓
Multiplied by Factor (user-controlled)
    ↓
Multiplied by Precision (if not "clear")
    ↓
Rounded / Precision
    ↓
OSC Message
```

### Scaling Formula

From [`src/renderer.js`](src/renderer.js:147):

```javascript
// For translation axes (example X):
document.getElementById("tr_x").value =
    Math.round(Math.pow(translateX * 1, 3) * 5 * factor * precision) / precision;

// For rotation axes (example Pitch - rt_x):
document.getElementById("rt_x").value =
    Math.round(Math.pow(rotateX * 1, 3) * 5 * factor * precision) / precision;
```

### Key Parameters Explained

#### Factor
- **Range:** 0.1 to 100
- **Effect:** Multiplies all output values. Higher factor = more sensitive.
- **Default:** 1

#### Precision
- **Options:** 1, 10, 100, 1000, 100000, "clear"
- **Effect:** Controls decimal places in output
  - "clear" = no rounding (full precision float)
  - "100000" = 5 decimal places (default)
- **Default:** 100000

#### Send Rate
- **Range:** 1 to 100 (percentage)
- **Effect:** Limits how often OSC messages are sent
- **Default:** 33 (33%)

### Range Clipping

The [`mapToScale`](src/mainFunctions.js:23) function handles range mapping:

```javascript
mapToScale: function(value, rangeIn, rangeOut, decimals, log, revertlog)
```

This function:
1. Clips input to `rangeIn` bounds
2. Normalizes to 0-1
3. Optionally applies logarithmic scaling
4. Maps to `rangeOut` range
5. Clips output to `rangeOut` bounds
6. Rounds to specified decimal places

### Absolute/Relative Mode

When `absolute_relative` is true:
- **Relative Mode (`++`)**: Values accumulate from origin point (reset on button press)
- **Absolute Mode**: Values report current position directly

The `fromAbsoluteToRelative` function in [`src/mainFunctions.js`](src/mainFunctions.js:93) converts absolute readings to relative by maintaining a previous state and calculating deltas.

---

## OSC Message Interface

### Incoming OSC Commands

The app listens for these OSC commands (received on port 9000):

| OSC Address | Parameters | Description |
|-------------|------------|-------------|
| `/prefix` | string | Change OSC prefix |
| `/index` | int | Change index number |
| `/precision` | int | Change precision (1, 10, 100, 1000, 100000) or "clear" |
| `/factor` | float | Change factor (0.1-100) |
| `/sendRate` | int | Change send rate percentage (1-100) |

### Outgoing OSC Messages

Format: `{prefix}/{index}/{path} {value}`

Example for AED preset with prefix `/track`, index `1`:
```
/track/1/elev++ -0.52345
/track/1/azim++ 1.23456
/track/1/dist++ 0.87654
```

---

## Creating Custom Presets

You can create custom presets by modifying the `paths_sets` section in the preferences configuration. Each preset is a JSON array of 6 elements.

### Preset Array Format

```
[axis0, axis1, axis2, axis3, axis4, axis5]
```

Where each element can be:
- `null` - axis disabled
- `"name"` - absolute value mode (e.g., `"x"`, `"pitch"`)
- `"name++"` - relative/accumulated mode (e.g., `"x++"`, `"azim++"`)

### Example Custom Presets

**XZ Translation with Pitch/Roll:**
```
["x++", null, "z++", "pitch", null, "roll"]
```

**XY Translation with Yaw only:**
```
["x++", "y++", null, null, "yaw++", null]
```

**All axes relative with custom names:**
```
["leftRight++", "upDown++", "forwardBack++", "tilt++", "turn++", "spin++"]
```

---

## Network Configuration

| Setting | Default | Description |
|---------|---------|-------------|
| **OSC Server** | `127.0.0.1:12000` | Destination OSC server address |
| **OSC Receiver Port** | `9000` | Local port for receiving OSC commands |