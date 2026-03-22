# Dino Runner - Game Specification

## 1. Project Overview

- **Project Name**: Dino Runner
- **Type**: 2D Browser Game (Single HTML file)
- **Core Functionality**: Endless runner where a dinosaur auto-runs and player controls jump/duck to avoid obstacles
- **Target Users**: Casual gamers, Chrome Dino fans

## 2. Visual & Rendering Specification

### Canvas Setup
- **Renderer**: HTML5 Canvas 2D
- **Resolution**: Responsive, scales to window (min 600x200)
- **Aspect Ratio**: ~3:1 (width:height)

### Art Style: Pixel-Perfect Retro
- **Color Palette**:
  - Background: `#f7f7f7` (day) / `#1a1a2e` (night)
  - Foreground (dino, ground, obstacles): `#535353`
  - UI Text: `#535353` (day) / `#f7f7f7` (night)
  - Sky accent (night): `#16213e`
- **Rendering**: `imageSmoothingEnabled = false` for crisp pixels

### Visual Elements

**Dinosaur (Dino)**
- Size: 44x47 pixels
- Pixel art sprite with two frames (running animation)
- Duck mode: 59x30 pixels (stretched horizontally)
- Position: Fixed X (50px from left), Y varies with jump
- Colors: `#535353` fill, transparent background

**Ground**
- Height: 2px solid line
- Small terrain bumps/pebbles moving left
- Height from bottom: 20px

**Obstacles**
- **Cactus**: 17-50px width, 35-70px height (randomized)
- **Bird**: 46x40 pixels, animated wing flap (2 frames)
- Spawn height: Ground level (cactus) or 50-100px above ground (birds)

**Background**
- Day: Plain `#f7f7f7`
- Night: `#1a1a2e` with subtle stars
- Transition: Every 700 points, alternate day/night

**UI Elements**
- Score: Top-right, `Press Start 2P` or fallback monospace font, 16px
- Game Over: Center screen, "GAME OVER" text with restart icon
- Instructions: "Press SPACE or UP to jump, DOWN to duck" (initial state only)

## 3. Game Mechanics Specification

### Physics
- **Gravity**: 0.6 px/frame²
- **Jump Velocity**: -12 px/frame
- **Ground Y**: canvas.height - 20px

### Speed System
- **Initial Speed**: 6 px/frame
- **Max Speed**: 15 px/frame
- **Speed Increment**: +0.001 per frame (gradual)
- **Obstacle Speed**: Matches game speed

### Scoring
- **Score Increment**: 1 point every 6 frames (~10 points/second at 60fps)
- **High Score**: Stored in localStorage
- **Milestone Sound**: Beep at every 100 points

### Difficulty Progression
| Score Range | Speed Multiplier | Obstacle Spawn Rate | Bird Chance |
|-------------|------------------|---------------------|-------------|
| 0-300       | 1.0x             | 2.5% per frame      | 0%          |
| 300-600     | 1.3x             | 3.0% per frame      | 10%         |
| 600+        | 1.5x+            | 3.5% per frame      | 30%         |

### Collision Detection
- Hitbox-based collision (slightly smaller than visual for fairness)
- Dino hitbox: 80% of sprite size
- Collision triggers game over immediately

## 4. Interaction Specification

### Controls
| Input | Action |
|-------|--------|
| SPACE / UP Arrow | Jump (only when on ground) |
| DOWN Arrow (hold) | Duck (reduces height, increases speed temporarily) |
| Any key (game over) | Restart |

### Input Handling
- Keyboard event listeners for keydown/keyup
- Prevent default on game keys to avoid page scroll
- Touch support: Tap upper half = jump, tap lower half = duck

## 5. Audio Specification (Web Audio API)

### Sounds
- **Jump**: Short rising tone (100Hz → 400Hz, 100ms)
- **Hit**: Low thud (80Hz, 200ms, distortion)
- **Score Milestone**: Pleasant chime (600Hz, 50ms)
- **Duck**: Subtle whoosh (white noise, 50ms, filtered)

## 6. State Machine

```
[IDLE] ---(space pressed)---> [PLAYING]
[PLAYING] ---(collision)---> [GAME_OVER]
[GAME_OVER] ---(any key)---> [IDLE]
```

### State Details

**IDLE**
- Show "Press SPACE to start" message
- Dino standing still (frame 0)
- No obstacles spawning
- Score hidden

**PLAYING**
- Dino animates (running frames)
- Ground scrolls
- Obstacles spawn and move
- Score increments
- Speed increases

**GAME_OVER**
- Dino frozen in hit pose
- "GAME OVER" displayed
- High score updated if beaten
- "Press any key to restart" shown

## 7. Technical Implementation

### File Structure
- Single `index.html` with embedded CSS and JavaScript
- No external dependencies except Google Font (optional)

### Performance Targets
- 60 FPS
- Smooth animation on mobile devices
- No memory leaks (object pooling for obstacles)

### Object Pooling
- Pre-allocate 10 obstacle objects
- Reuse instead of creating/destroying

## 8. Acceptance Criteria

1. ✅ Game starts when SPACE is pressed
2. ✅ Dino jumps when SPACE/UP pressed (only from ground)
3. ✅ Dino ducks when DOWN held
4. ✅ Cactus obstacles appear from right and move left
5. ✅ Birds appear at higher scores and fly in air lanes
6. ✅ Collision with obstacle ends game
7. ✅ Score increases over time
8. ✅ Game speed increases as score rises
9. ✅ Day/night cycle changes every 700 points
10. ✅ High score persists across sessions (localStorage)
11. ✅ Game restarts on key press after game over
12. ✅ Pixel art aesthetic maintained
13. ✅ Sound effects play for jump and game over
