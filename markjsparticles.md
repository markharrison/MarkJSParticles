# MarkJSParticles Developer Documentation

## Overview

MarkJSParticles is a JavaScript class that manages particle effects on an HTML5 Canvas. It provides methods to update and render particles, designed to integrate with an external game loop. The animation loop is handled outside the class, allowing for flexible integration with your application's timing.

## Constructor

### `new MarkJSParticles(canvas, options)`

Creates a new MarkJSParticles instance.

#### Parameters

- `canvas` (HTMLCanvasElement): The canvas element to draw on (required)
- `options` (Object): Configuration options (currently reserved for future use)

Note: The constructor currently stores a reference to the canvas and creates a 2D context. The `options` parameter is reserved for future use. For background handling, you can manually assign properties to the instance after construction.

#### Example

```javascript
const canvas = document.getElementById("myCanvas");
const pm = new MarkJSParticles(canvas);

// Manually set background for use in your game loop
pm.background = "#222";

// In your game loop, clear the canvas
pm.ctx.fillStyle = pm.background;
pm.ctx.fillRect(0, 0, pm.canvas.width, pm.canvas.height);
```

## Properties

### `debug` (Boolean)

When set to `true`, enables debug mode for the particle system. This property can be toggled at runtime.

#### Example

```javascript
// Toggle debug mode
pm.debug = !pm.debug;
```

Note: Debug functionality is available but the specific debug rendering features may vary by implementation.

## Methods

### `update(deltaTime)`

Updates the particle animation based on the elapsed time.

#### Parameters

- `deltaTime` (Number): Time elapsed since the last update in milliseconds.

#### Example

```javascript
// In your game loop
const deltaTime = 16.67; // For 60 FPS
pm.update(deltaTime);
```

### `render()`

Renders the particles to the canvas. Call this after `update()` in your render loop.

#### Example

```javascript
// In your render loop
pm.render();
```

### `addEffect(type, coords, options)`

Adds an effect at the specified coordinates.

#### Parameters

- `type` (String): The type of effect to add. Supported types: "explosion", "confetti", "fireworks"
- `coords` (Array): [x, y] coordinates of the effect center
- `options` (Object): Effect options

  **Basic Particle Properties:**

  - `particlesPerExplosion` (Number): Number of particles (default: 30 for explosion, 50 for confetti, 20 for fireworks)
  - `particlesMinSpeed` (Number): Minimum particle speed (default: 3 for explosion, 1 for confetti, 5 for fireworks)
  - `particlesMaxSpeed` (Number): Maximum particle speed (default: 6 for explosion, 4 for confetti, 10 for fireworks)
  - `particlesMinSize` (Number): Minimum particle size (default: 1 for explosion, 2 for confetti and fireworks)
  - `particlesMaxSize` (Number): Maximum particle size (default: 6 for explosion, 8 for confetti, 4 for fireworks)

  **Lifetime and Physics:**

  - `lifetimeMs` (Number): Override for particle lifetime in milliseconds. When provided, overrides the effect's default lifetime range.
  - `lifetimeJitter` (Number 0..1): Fractional jitter applied to particle lifetime. Values between 0 and 1 shorten particle lifetimes by up to that fraction (default: varies by effect type).
  - `gravity` (Number): Gravity applied to particles (default: 0.02 for explosion, 0.01 for confetti, -0.05 for fireworks)

  **Visual Effects:**

  - `glowStrength` (Number): Visual glow strength. When `glowStrength` > 0, particles are drawn with a subtle canvas shadow (blur) to make colors pop. The value scales the shadow blur relative to particle size. Default: `0` (off).

  **Color Customization:**

  - `rMin`, `rMax` (Number 0-255): Red color component range (default varies by effect)
  - `gMin`, `gMax` (Number 0-255): Green color component range (default varies by effect)
  - `bMin`, `bMax` (Number 0-255): Blue color component range (default varies by effect)

  **Note on Confetti Effects:**
  Confetti particles automatically receive random shapes (`square`, `circle`, `ribbon`, `star`) and motion properties (`sway`, `swayFreq`, `swayPhase`, `angularVelocity`) that are generated per-particle internally. These properties are not currently exposed as configuration options.

#### Supported Effect Types

- `"explosion"`: Creates an explosion effect with particles radiating outward in all directions. Uses orange/red colors and moderate gravity.
- `"confetti"`: Creates a confetti effect with floating particles in bright colors. Particles have random shapes (square, circle, ribbon, star) with rotation and horizontal sway motion.
- `"fireworks"`: Launches particles upward with negative gravity that burst into outward explosions at their peak height. Uses bright yellow/orange colors.

#### Default Values by Effect Type

**Explosion:**

- particlesPerExplosion: 30
- Speed range: 3-6
- Size range: 1-6
- Gravity: 0.02
- Colors: Orange/red tones
- Lifetime: 600-1400ms

**Confetti:**

- particlesPerExplosion: 50
- Speed range: 1-4
- Size range: 2-8
- Gravity: 0.01 (lighter)
- Colors: Bright mixed colors
- Lifetime: 2000-4000ms (longer)

**Fireworks:**

- particlesPerExplosion: 20
- Speed range: 5-10
- Size range: 2-4
- Gravity: -0.05 (upward launch)
- Colors: Yellow/orange tones
- Lifetime: 1000-2000ms

#### Example

```javascript
// Add an explosion with custom parameters
pm.addEffect("explosion", [400, 300], {
  particlesPerExplosion: 50,
  particlesMinSpeed: 2,
  particlesMaxSpeed: 8,
  glowStrength: 1.5,
});

// Add confetti with custom colors
pm.addEffect("confetti", [400, 300], {
  particlesPerExplosion: 100,
  rMin: 255,
  rMax: 255, // Red only
  gMin: 0,
  gMax: 0,
  bMin: 0,
  bMax: 0,
});
```

### `destroy()`

Clears all effects and cleans up memory.

#### Example

```javascript
pm.destroy();
```

## Integration with Game Loop

MarkJSParticles is designed to work with an external game loop. The host application is responsible for clearing the canvas background. Here's a basic example:

```javascript
const canvas = document.getElementById("myCanvas");
const pm = new MarkJSParticles(canvas);
pm.background = "#222"; // Set background color

let lastTime = 0;

function gameLoop(currentTime) {
  const deltaTime = currentTime - lastTime;
  lastTime = currentTime;

  // Update particles
  pm.update(deltaTime);

  // Clear canvas background (required)
  pm.ctx.fillStyle = pm.background;
  pm.ctx.fillRect(0, 0, pm.canvas.width, pm.canvas.height);

  // Render particles
  pm.render();

  requestAnimationFrame(gameLoop);
}

requestAnimationFrame(gameLoop);
```

## Effect Configuration

All effect parameters are passed through the `options` object when calling `addEffect()`. Each effect type has different default values but can be customized using the same parameter names.

### Glow (visual)

MarkJSParticles exposes a single visual parameter to control particle glow: `glowStrength`.

- Type: Number
- Default: `0` (off)
- Behavior: If `glowStrength` &gt; 0 the renderer sets a canvas shadowColor equal to the particle color and computes shadowBlur as roughly `size * glowStrength`. If `glowStrength` is `0` no shadow is applied and rendering is identical to the original behavior.
- Usage: pass `glowStrength` inside the `options` object when calling `addEffect()`, for example:

```javascript
// Add confetti with a subtle glow
pm.addEffect("confetti", [400, 300], {
  particlesPerExplosion: 80,
  particlesMinSize: 2,
  particlesMaxSize: 6,
  glowStrength: 1.6, // enable glow
});

// Disable glow explicitly
pm.addEffect("explosion", [200, 200], { glowStrength: 0 });
```

Note: the test application included in the repository exposes a numeric control named `glowStrengthNumber` which defaults to `0`. Use that control to experiment with values and pick what looks best for your scene.

## Internal Classes

### Explosion

Represents a single explosion effect.

### Confetti

Represents a single confetti effect.

### Particle

Represents an individual particle within an effect.

## Memory Management

MarkJSParticles automatically removes particles and effects when they are no longer active. Call `destroy()` when you're done with the instance to free up resources.

## Extending MarkJSParticles

To add new effects:

1. Create a new static class for the effect (e.g., `MarkJSParticles.Firework`)
2. Update the `addEffect()` method to handle the new type
3. Implement any specific logic in the effect class

## Performance Considerations

- Use appropriate deltaTime values for smooth animation
- Limit the number of simultaneous effects
- Use `destroy()` to clean up when not in use
