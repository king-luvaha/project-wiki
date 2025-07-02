---
slug: building-2D-platformer-ninja-game
title: 🎮 Building a 2D Platformer Game with a Custom Level Editor in Python
authors: luvaha
tags:
  - Pygame
  - GameDev
  - Python
  - 2D
  - Ninja_Game
---

---

## ✨ Introduction

Ever dreamed of making your own 2D platformer game from scratch — one that includes **custom physics, enemies, particles, and even a level editor**? I did too. That's why I built **"Ninja Game"**, a pixel-art style platformer written entirely in Python using **Pygame**.

In this post, I’ll walk you through how I designed and implemented a 2D platformer with an integrated **tile-based map editor**, sprite animation system, and basic AI — all while keeping the project organized and extensible.

---

## 🧱 Why I Built a Level Editor

Game development is more than just jumping and shooting — it’s about designing the world. So I built a **tilemap editor** that allows me to:

- Place tiles from grouped assets (grass, stone, decor, etc.)
    
- Use scrollable canvas to design large levels
    
- Add enemies or decorations as "offgrid" tiles
    
- Save/load levels using JSON
    

> 🔁 The level editor also features mouse-wheel tile selection and simple `autotile` support for better visuals.

---

## 🚀 Key Features

### ✅ Core Gameplay

- Responsive platformer mechanics with double jump and wall slide
    
- Dashing system that creates spark and particle trails
    
- Enemies that patrol and shoot based on player distance
    

### 🧠 Entities

- **Player**: Dashing, jumping, wall sliding, and flipping
    
- **Enemy**: AI-based shooting behavior and knockback when hit
    
- **Particles**: For leaves, sparks, and hit effects
    

### 🧰 Tools and Utilities

- **`Animation` class** for smooth sprite transitions
    
- **`Tilemap` system** for both grid and off-grid objects
    
- **Asset loader** using `load_images()` and `load_image()` utilities
    

---

## 🗺️ Map Editing: Tips and Gotchas

When working in `editor.py`, keep the following in mind:

- **Offgrid Placement**: Objects like `decor`, `spawners`, or `enemy` must be placed **offgrid** using `G` key toggle. On-grid placement will crash the game during runtime.
    
- **Player Spawn Point**: Manually place a `player` entity **offgrid** near the level’s start. Otherwise, the player may respawn at the wrong location (like the death spot) after getting hit.
    

The editor is fully mouse-driven and supports save/load via the `O` key.

---

## 📁 Folder Structure

```
ninja_game/
├── data/
│   ├── images/          # Sprites (player, enemy, tiles, etc.)
│   ├── maps/            # JSON level files
│   ├── sfx/             # Sound effects
│   └── music.wav        # Background music
├── scripts/
│   ├── clouds.py        # Parallax clouds system
│   ├── entities.py      # PhysicsEntity, Player, Enemy
│   ├── particle.py      # Particle rendering
│   ├── spark.py         # Sparks from dashing and shooting
│   ├── tilemap.py       # Map rendering and saving
│   └── utils.py         # Asset loading and animation
├── editor.py            # Level editor (run separately)
├── game.py              # Main game logic and loop
└── map.json             # Default map file
```

---

## 🕹️ Controls

### In Game (`game.py`)

- **A / D / W / S** — Move
    
- **J** — Jump (with wall jump and double jump)
    
- **K** — Dash
    
- **ESC** — Quit
    

### In Editor (`editor.py`)

- **Mouse Left Click** — Place tile
    
- **Mouse Right Click** — Delete tile
    
- **Scroll Wheel** — Change tile group or variant
    
- **G** — Toggle ongrid / offgrid placement
    
- **O** — Save map to `map.json`
    
- **T** — Run `autotile()` for smoother edge tiles
    

---

## 🔨 What I Learned

- Handling collisions frame-by-frame using bounding rectangles
    
- Building reusable animation systems
    
- Managing parallax with varying cloud depths
    
- Designing for extensibility with asset-based organization
    
- JSON as a lightweight map format
    

---

## 📦 Future Improvements

- Add GUI tile picker to the editor
    
- Camera shake during explosions
    
- Boss fights with multiple attack patterns
    
- Exporting maps to external game engines
    

---

## 💬 Final Thoughts

This project was a powerful learning experience in **game architecture, data-driven design, and asset management**. If you're looking to get into Python game development, Pygame is a great place to start.

Check out the full source code on GitHub:

👉 [**GitHub Repo: Ninja Game**](https://github.com/yourusername/ninja_game)

---

## 🔗 Let's Connect

- **GitHub**: [@yourusername](https://github.com/yourusername)
    
- **Twitter**: [@yourhandle](https://twitter.com/yourhandle)
    
- **Portfolio**: [yourwebsite.com](https://yourwebsite.com)
    

---

Would you like me to:

- Add code snippets to the blog?
    
- Tailor this for a specific platform like Medium or Dev.to?
    
- Create a Markdown file version for direct publishing?
    

Let me know, and I’ll prepare it for you.