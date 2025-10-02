# Pixel-Perfect Camera for Godot 4

A zero-jitter, pixel-perfect camera system for Godot 4 pixel-art games with real-time diagnostic overlay.

## Features

✅ **Zero camera jitter** - Eliminates sub-pixel positioning artifacts
✅ **Accurate player movement** - Maintains smooth sub-pixel physics
✅ **Fractional offset correction** - Intelligent parent-child position compensation
✅ **Real-time diagnostics** - Visual feedback for camera performance
✅ **Drop-in solution** - Just attach the script and go!

## Installation

### As a Submodule

```bash
cd your-godot-project
git submodule add git@github.com:ChrisTutorials/pixel-perfect-camera-godot.git addons/pixel_perfect_camera
git submodule update --init --recursive
```

### Manual Installation

1. Copy `camera_follower.gd` and `camera_diagnostics.gd` to your project
2. Attach scripts to appropriate nodes (see Usage below)

## Usage

### Camera Setup

1. Add a `Camera2D` node as a **child** of your player/character node
2. Attach the `camera_follower.gd` script to the Camera2D
3. The camera will automatically follow the player with pixel-perfect positioning!

**Scene Structure:**
```
Player (CharacterBody2D or Node2D)
└─ Camera2D [camera_follower.gd attached]
```

**Example:**
```gdscript
# No configuration needed! Just attach the script
# The camera automatically:
# - Follows parent node
# - Snaps to whole pixels
# - Updates after physics for zero lag
```

### Diagnostic Overlay (Optional)

1. Add a `CanvasLayer` node to your scene
2. Attach the `camera_diagnostics.gd` script
3. Assign your Camera2D to the `camera` export variable

The overlay will show:
- Camera position (fractional components)
- Movement speed and consistency
- **Jitter score** (0.00 = perfect, >0.5 = problematic)
- Sub-pixel detection
- V-Sync status
- FPS metrics

**Color Indicators:**
- 🟢 **GREEN** - Perfect pixel-perfect rendering
- 🟡 **YELLOW** - V-Sync disabled (may cause tearing)
- 🟠 **ORANGE** - Sub-pixel positioning detected
- 🔴 **RED** - Camera jitter detected

## How It Works

### The Problem

When a camera is a child of a moving player, it inherits the parent's position. If the player uses sub-pixel physics (smooth diagonal movement), the camera also gets fractional pixel coordinates, causing visual jitter.

**Without this system:**
```
Player position: (648.753, 424.159)  [sub-pixel precision]
Camera (child): inherits (648.753, 424.159)  ❌ JITTER!
```

### The Solution

This system uses **fractional offset correction**: the camera applies a local position offset that counteracts the parent's fractional position, resulting in whole-pixel camera coordinates.

**With this system:**
```
Player position: (648.753, 424.159)  [sub-pixel precision]
Camera offset: (0.247, -0.159)       [correction offset]
Camera global: (649.0, 424.0)        ✓ PERFECT!
```

### Timing Optimization

The camera updates in `_process()` (IDLE mode) which runs **after** `_physics_process()`, ensuring it reads the player's latest position without frame delay.

```
Frame Timeline:
1. _physics_process() → Player moves
2. Physics calculations
3. _process() → Camera adjusts offset ← Zero-frame delay!
4. Render frame → Perfect sync
```

## Configuration

### Camera Follower (`camera_follower.gd`)

```gdscript
@export var pixel_perfect: bool = true  # Enable/disable pixel snapping
```

Set `pixel_perfect = false` to disable pixel snapping (for testing or non-pixel-art games).

### Diagnostic Overlay (`camera_diagnostics.gd`)

```gdscript
@export var camera: Camera2D  # Assign your Camera2D node
```

## Project Settings

For best results, ensure these Godot project settings:

```
[rendering]
textures/canvas_textures/default_texture_filter = 0  # Nearest neighbor
2d/snap/snap_2d_transforms_to_pixel = true          # Global pixel snapping

[display]
window/stretch/mode = "viewport"                     # Proper scaling
```

## Troubleshooting

### Camera still shows jitter

1. Check diagnostic overlay - is "Sub-pixel" showing YES?
2. Verify camera is a **child** of the moving object
3. Ensure project settings have `snap_2d_transforms_to_pixel = true`
4. Check that `pixel_perfect = true` in the camera script

### Diagnostic shows "No camera assigned"

Make sure you've assigned the Camera2D node to the diagnostic script's `camera` export variable in the Godot editor.

### Player movement feels wrong

This system does NOT affect player movement - it only adjusts camera positioning. If movement feels wrong, check your player's physics settings, not the camera.

## Requirements

- Godot 4.0+
- 2D project
- Pixel-art or low-resolution game

## License

MIT License - Free to use in personal and commercial projects.

## Credits

Created by [ChrisTutorials](https://github.com/ChrisTutorials)

## Support

Found a bug? Have a feature request? Open an issue on GitHub!

**Repository:** https://github.com/ChrisTutorials/pixel-perfect-camera-godot
