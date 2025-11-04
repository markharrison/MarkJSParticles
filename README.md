# MarkJSParticles

💥 A lightweight JavaScript library for creating particle effects on HTML5 Canvas, designed for web games and interactive applications.

[![npm version](https://img.shields.io/npm/v/%40markharrison%2Fmarkjsparticles)](https://www.npmjs.com/package/@markharrison/markjsparticles)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## Overview

MarkJSParticles provides a simple, dependency-free API for adding particle-based animations (explosions, confetti, fireworks) to your web applications. Built specifically for canvas-based projects and games.

## Key Features

• 🚀 **Modern ES Modules** - Clean, dependency-free JavaScript library
• 💥 **Multiple Effect Types** - Explosion, confetti, and fireworks built-in
• 🎨 **Highly Customizable** - Control particle count, speed, size, colors, and physics
• ⚡ **Performance Optimized** - Automatic particle lifecycle management
• 🎮 **Game Loop Integration** - Designed to work with external render loops
• 🌈 **Visual Effects** - Glow effects, particle shapes, and smooth animations
• 📱 **Browser Compatible** - Works in all modern browsers with Canvas support

## Installation

```bash
npm install @markharrison/markjsparticles --save
```

## Quick Start

```javascript
import { MarkJSParticles } from "@markharrison/markjsparticles";

const canvas = document.getElementById("myCanvas");
const pm = new MarkJSParticles(canvas);

// Add explosion on click
canvas.addEventListener("click", (e) => {
  const rect = canvas.getBoundingClientRect();
  const x = e.clientX - rect.left;
  const y = e.clientY - rect.top;
  pm.addEffect("explosion", [x, y], {
    particlesPerExplosion: 50,
    particlesMinSpeed: 2,
    particlesMaxSpeed: 8,
  });
});

// Game loop
function gameLoop(deltaTime) {
  pm.update(deltaTime);
  pm.render();
  requestAnimationFrame(gameLoop);
}
requestAnimationFrame(gameLoop);
```

## Available Effects

• **Explosion** - Particles radiate outward and fall under gravity
• **Confetti** - Colorful pieces with sway and rotation (squares, ribbons, circles, stars)
• **Fireworks** - Upward launch particles that burst at peak height

## Documentation

📖 [Complete Documentation](./markjsparticles.md) - Detailed API reference, examples, and configuration options

## Test Application

🎮 Interactive demo showcasing all particle effects and customization options

<img width="1554" height="1431" alt="MarkJSParticles Test Application" src="https://github.com/user-attachments/assets/c0d3cdec-9348-4fcb-b7bf-b5d61fe9416a" />

Open `index.html` in your browser to explore the full range of particle effects and settings.

## License

MIT License - see [LICENSE](./LICENSE) file for details.
