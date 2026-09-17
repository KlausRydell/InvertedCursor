# Inverted Cursor Plugin

A lightweight Unreal Engine plugin that renders a **Minecraft-style inverted cursor or crosshair** using a post-process material.

The material inverts the scene beneath the cursor mask, helping it stand out against different backgrounds without requiring a UMG widget. Optional custom colors, bloom arms, and a circular bloom layer let you build different crosshair styles.

## Features

- Scene-color inversion beneath the cursor
- Optional custom Cursor Color, with separate control over whether it affects the center
- Center cursor, four individually enabled bloom arms, and circular bloom
- Cursor dimensions in screen pixels and position in normalized viewport coordinates
- Nested Cursor Style settings and named presets
- Automatic movement-based accuracy and crosshair spread
- Manual accuracy and spread control
- Optional Bloom Velocity Target for custom control setups
- Automatic camera application and PlayerController possession handling
- Reuses the runtime material instance when changing cameras
- No UMG widget, SceneCapture, or render target required

## Setup

Add an **Inverted Cursor Component** to your PlayerController or an actor containing a Camera Component.

With **Auto Initialize** enabled, the component finds a camera automatically. A PlayerController-owned component checks its controlled Pawn first and follows possession changes.

For a custom camera setup, call:

```text
Apply Cursor to Camera
    In Camera Component
```

The component defaults directly to `M_InvertedCursor`, with the included center and arm masks already assigned.

## Customizing the Cursor

Expand **Cursor Style** to adjust:

- **Center**: mask, size, scale, position, and visibility
- **Color**: Cursor Color, Use Custom Color, and Allow Center Color
- **Bloom**: shared spread and minimum visual spread
- **Arms**: arm mask, size, and individual arm visibility
- **Circle**: visibility, scale, and feathering

Save different appearances in **Cursor Style Presets** and switch between them with **Apply Cursor Style Preset**. Preset masks left unassigned inherit the component's current masks.

## Accuracy and Bloom

**Enable Bloom Tick Update** automatically adjusts crosshair spread using movement speed. It is enabled by default and includes vertical movement.

Use **Set Bloom Velocity Target** when the actor driving accuracy is different from the camera or possessed Pawn. If that override is cleared or becomes invalid, the component falls back through the camera owner, controlled Pawn, and component owner. The camera-owner step is skipped when it is also the component owner.

For manual control, disable **Enable Bloom Tick Update** and use **Set Accuracy** or **Set Bloom Offset**.

## Notes

- The center and arms start enabled; the circle starts disabled.
- Inversion is the default color mode. **Allow Center Color** lets custom color affect the center too.
- **Bloom Offset Min** sets a visual minimum without changing accuracy.
- **Circle Scale** adjusts the circle while it continues to follow bloom spread.
- **Set Bloom Enabled** controls the arms; the circle has its own enabled setting.
- The component ticks while automatic bloom updates are enabled. Hiding the cursor does not disable those updates.
