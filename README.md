# Inverted Cursor Plugin

A tiny Unreal Engine runtime plugin that renders a **Minecraft-style inverted crosshair** by using a post-process material to invert the 3D scene only where a cursor mask is present.

The cursor remains visible against bright, dark, and colored environments without requiring a white/black outlined UI cursor.

## Features

- True scene-color inversion beneath the cursor
- Works independently of UMG
- No Tick
- No SceneCapture or Render Target
- Cursor size is defined in screen pixels
- Cursor position uses normalized viewport coordinates
- Runtime cursor enable/disable
- Runtime mask, size, and position changes
- Packaged as a reusable Actor Component

## Plugin Structure

```text
InvertedCursor/
├─ Content/
│  ├─ Materials/
│  │  └─ M_InvertedCursor
│  └─ Textures/
│     └─ T_CursorMask
└─ Source/
   └─ InvertedCursor/
      ├─ Public/
      │  └─ InvertedCursorComponent.h
      ├─ Private/
      │  ├─ InvertedCursor.cpp
      │  └─ InvertedCursorComponent.cpp
      └─ InvertedCursor.Build.cs
```

## Setup

Add an **Inverted Cursor Component** to an actor containing a `CameraComponent`.

Assign:

- `Cursor Material` → `M_InvertedCursor`
- `Cursor Mask` → your cursor mask texture
- `Cursor Size` → e.g. `(16, 16)`
- `Cursor Position` → `(0.5, 0.5)` for screen center
- `Auto Initialize With Owner Camera` → enabled
- `Start Enabled` → enabled

If the camera exists on another actor, disable automatic initialization and call:

```text
Initialize Cursor(CameraComponent)
```

once after the active camera is known.

## Material Parameters

The post-process material must contain parameters with these exact names:

```text
CursorMask
CursorPosition
CursorSize
```

The material uses:

```text
Result = Lerp(
    SceneColor,
    1 - SceneColor,
    CursorMask
)
```

`PostProcessInput0` should use its normal/default scene UVs.

Only the **CursorMask texture** should receive the custom cursor UV calculation.

## Cursor UV

The cursor mask is mapped in screen pixels using:

```text
CursorUV =
    ((ViewportUV - CursorPosition) * ViewSize)
    / CursorSize
    + 0.5
```

This allows a `(16, 16)` cursor to remain approximately 16×16 pixels regardless of viewport resolution.

## Recommended Cursor Texture Settings

For crisp masks without tiling, mip bleed, or edge lines:

```text
Mip Gen Settings:       NoMipmaps
Filter:                 Nearest
X-axis Tiling Method:   Clamp
Y-axis Tiling Method:   Clamp
sRGB:                   Off
Compression Settings:   Masks (no sRGB)
Never Stream:           On
```

The material texture sampler should use:

```text
Sampler Source: From Texture Asset
```

Keep at least one fully black/transparent pixel around the outer border of the cursor mask.

## Blueprint Functions

```text
Initialize Cursor
Set Cursor Enabled
Set Cursor Mask
Set Cursor Size
Set Cursor Position
Is Cursor Initialized
Is Cursor Enabled
```

## Notes

The effect is applied as a camera post-process blendable, so it affects the rendered 3D scene while leaving UMG/UI unaffected.

The component is intentionally lightweight and does not Tick.
