# The Grossing Snake (Godot 4)

My first Godot project. I built the base snake game from a tutorial,
then designed a full game on top of it: a snake game where the arena itself fights back.

📄 **[Read the design document](GDD.md)**

## Built so far (tutorial base)
- Grid-based snake movement on a timer
- Food spawning and growing the snake
- Self and wall collision, game over screen, restart

## Designed (from my design doc)
- **Survival mode:** 5 nested border layers that shift every 5 seconds
  (shrinking then expanding the arena), plus fruit and bombs every 2–4 seconds
- **Blackout mechanic:** the screen briefly goes dark after fruits, more often as you score
- **Fairness rules:** border shifts never kill the snake instantly, and wait for blackouts to end
- **Level mode (3 levels):** speed and spawn delay scale by formula,
  e.g. `speed = baseSpeed × (1 + 0.015 × i^1.4)`
  - Level 2: fixed "+" or "X" obstacle layouts
  - Level 3: 5–9 procedurally placed 2-tile obstacles

## Built with
Godot 4.6, GDScript

## Controls
- Arrow keys / W A S D to move
