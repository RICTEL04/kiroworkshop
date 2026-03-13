# Ghosty Character Sprite Specifications

## Overview

This document defines the visual specifications for the Ghosty character sprite used in Flappy Kiro. The character features a retro pixel-art style with multiple animation states to provide visual feedback during gameplay.

## Sprite Dimensions

- **Base Size:** 32x32 pixels
- **File Format:** PNG with transparency
- **Color Depth:** 32-bit RGBA
- **Pixel Style:** Retro pixel-art (no anti-aliasing)

## Hitbox Specifications

### Collision Hitbox
- **Shape:** Circle (for more forgiving collision detection)
- **Radius:** 12 pixels
- **Center Point:** Sprite center (16, 16)
- **Visual Representation:** The hitbox is smaller than the sprite to make gameplay feel fair

### Hitbox Positioning
```
Sprite: 32x32px
Hitbox: Circle with radius 12px centered at (16, 16)

┌─────────────────────────────────┐
│                                 │
│         ╭───────────╮           │
│         │           │           │
│         │   ●───12px│           │
│         │  (16,16)  │           │
│         │           │           │
│         ╰───────────╯           │
│                                 │
└─────────────────────────────────┘
```

## Animation States

### 1. Idle State
**Usage:** Menu screen, paused state
**Frame Count:** 4 frames
**Animation Speed:** 8 FPS (120ms per frame)
**Loop:** Continuous

**Frame Descriptions:**
- **Frame 1:** Base ghost shape, eyes open, slight upward tilt
- **Frame 2:** Eyes slightly wider, body stretched vertically by 1px
- **Frame 3:** Same as Frame 1 (base position)
- **Frame 4:** Body compressed vertically by 1px, eyes normal

**Visual Effect:** Gentle floating/breathing animation

### 2. Flap State
**Usage:** During jump/ascent
**Frame Count:** 3 frames
**Animation Speed:** 12 FPS (83ms per frame)
**Loop:** Play once, return to idle

**Frame Descriptions:**
- **Frame 1:** Body compressed, eyes wide, upward motion blur effect
- **Frame 2:** Body stretched upward, tail trailing down
- **Frame 3:** Body returning to normal, slight upward tilt

**Visual Effect:** Quick burst of energy during jump

### 3. Death State
**Usage:** Collision/game over
**Frame Count:** 5 frames
**Animation Speed:** 10 FPS (100ms per frame)
**Loop:** Play once, hold last frame

**Frame Descriptions:**
- **Frame 1:** Eyes wide, body normal (impact frame)
- **Frame 2:** Eyes X-shaped, body tilted 15° clockwise
- **Frame 3:** Eyes X-shaped, body tilted 30° clockwise, slight red tint
- **Frame 4:** Eyes X-shaped, body tilted 45° clockwise, fading opacity (80%)
- **Frame 5:** Eyes X-shaped, body tilted 60° clockwise, fading opacity (60%)

**Visual Effect:** Dramatic death animation with rotation and fade

## Sprite Sheet Layout

### Option 1: Horizontal Strip (Recommended)
```
┌────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┐
│ I1 │ I2 │ I3 │ I4 │ F1 │ F2 │ F3 │ D1 │ D2 │ D3 │ D4 │ D5 │
└────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┘
  Idle (4)      Flap (3)         Death (5)

Total Width: 384px (12 frames × 32px)
Height: 32px
```

### Option 2: Individual Files
```
ghosty-idle-1.png    (32x32px)
ghosty-idle-2.png    (32x32px)
ghosty-idle-3.png    (32x32px)
ghosty-idle-4.png    (32x32px)
ghosty-flap-1.png    (32x32px)
ghosty-flap-2.png    (32x32px)
ghosty-flap-3.png    (32x32px)
ghosty-death-1.png   (32x32px)
ghosty-death-2.png   (32x32px)
ghosty-death-3.png   (32x32px)
ghosty-death-4.png   (32x32px)
ghosty-death-5.png   (32x32px)
```

## Color Palette

### Primary Colors
- **Body:** `#FFFFFF` (White) with slight transparency (90% opacity)
- **Eyes:** `#000000` (Black)
- **Outline:** `#CCCCCC` (Light gray, 1px)
- **Death Eyes (X):** `#FF0000` (Red)

### Special Effects
- **Invincibility Flash:** Alternate between 100% and 50% opacity at 10 Hz
- **Death Tint:** Apply 60% red overlay (`rgba(255, 0, 0, 0.6)`)
- **Particle Trail:** White particles with 60% opacity

## Character Design Guidelines

### Visual Style
- **Shape:** Classic ghost silhouette with rounded top and wavy bottom
- **Eyes:** Two circular black eyes, expressive and large
- **Tail:** Wavy bottom edge with 3-4 wave points
- **Expression:** Friendly and approachable, not scary

### Proportions
```
     ╭─────────╮
    ╱           ╲
   │   ●     ●   │  ← Eyes (4px diameter each)
   │             │
   │             │
    ╲           ╱
     ╲  ╱╲  ╱╲ ╱   ← Wavy tail
      ╲╱  ╲╱  ╲╱

Height: 32px
Width: 32px
Eye spacing: 12px apart
Eye position: 10px from top
```

### Animation Principles
1. **Squash and Stretch:** Use subtle vertical compression/extension
2. **Anticipation:** Compress before flap for visual impact
3. **Follow-through:** Tail lags behind body movement
4. **Timing:** Quick flap (3 frames), slow idle (4 frames)

## Technical Implementation

### Loading Sprites
```javascript
// Example sprite loading code
const ghostySprite = {
  image: new Image(),
  frameWidth: 32,
  frameHeight: 32,
  animations: {
    idle: { startFrame: 0, frameCount: 4, fps: 8 },
    flap: { startFrame: 4, frameCount: 3, fps: 12 },
    death: { startFrame: 7, frameCount: 5, fps: 10 }
  }
};

ghostySprite.image.src = 'assets/ghosty-spritesheet.png';
```

### Rendering with Hitbox
```javascript
// Render sprite
ctx.drawImage(
  ghostySprite.image,
  frameX * 32, 0,           // Source x, y
  32, 32,                   // Source width, height
  ghost.x - 16, ghost.y - 16, // Dest x, y (centered)
  32, 32                    // Dest width, height
);

// Debug: Render hitbox (development only)
ctx.beginPath();
ctx.arc(ghost.x, ghost.y, 12, 0, Math.PI * 2);
ctx.strokeStyle = 'red';
ctx.stroke();
```

### Animation State Machine
```javascript
class GhostyAnimator {
  constructor() {
    this.currentState = 'idle';
    this.currentFrame = 0;
    this.frameTimer = 0;
  }
  
  setState(newState) {
    if (this.currentState !== newState) {
      this.currentState = newState;
      this.currentFrame = 0;
      this.frameTimer = 0;
    }
  }
  
  update(deltaTime) {
    const anim = ghostySprite.animations[this.currentState];
    this.frameTimer += deltaTime;
    
    const frameDuration = 1000 / anim.fps;
    if (this.frameTimer >= frameDuration) {
      this.frameTimer = 0;
      this.currentFrame = (this.currentFrame + 1) % anim.frameCount;
    }
  }
  
  getCurrentFrame() {
    const anim = ghostySprite.animations[this.currentState];
    return anim.startFrame + this.currentFrame;
  }
}
```

## Asset Creation Checklist

- [ ] Create base ghost shape (32x32px)
- [ ] Design 4 idle animation frames
- [ ] Design 3 flap animation frames
- [ ] Design 5 death animation frames
- [ ] Export as sprite sheet (384x32px) or individual files
- [ ] Test animations at target frame rates
- [ ] Verify hitbox alignment with visual center
- [ ] Ensure transparency is preserved
- [ ] Optimize file size (use PNG compression)
- [ ] Test rendering in-game at 60 FPS

## Notes

- The 12px hitbox radius is intentionally smaller than the sprite to make collision feel fair
- All animations should maintain the same center point for smooth transitions
- Use pixel-perfect rendering (disable image smoothing in canvas)
- Consider adding subtle glow effect for invincibility state
- Particle trail should spawn from the center point (16, 16) of the sprite

## References

- Current placeholder: `assets/ghosty.png` (40x40px) - needs replacement with animated sprite
- Target: `assets/ghosty-spritesheet.png` (384x32px with 12 frames)
