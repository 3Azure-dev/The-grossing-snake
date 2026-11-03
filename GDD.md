# The Grossing Snake — Game Design Document

*Core game overview for developers & artists*

## High-Level Concept
Grossing Snake is a modernized grid-based snake game where the arena itself becomes an active mechanical element. Instead of a static box, the world behaves like a dynamic system that challenges the player with shifting boundaries, timed hazards, and procedural changes. The snake's movement, speed, and the environment's behavior form a tightly woven loop.

The core identity of the game is:
- precision movement on a clean grid
- math-driven difficulty progression
- reactive arena elements
- high-clarity visuals with quick visual cues (flash, color changes, overlays)
- tight, predictable rules that still feel intense

## Visual & Artistic Identity (for the artist)
**1. Clean, geometric grid** — the board is minimal, clear and readable:
- uniform tile grid
- high contrast between playfield, obstacles and items
- snake segments readable at high speed

**2. Strong color coding** — color is used strictly for functional communication, not decoration:
- each border layer has a locked color identity
- fruit and bombs must be instantly recognizable
- blackout flashes and overlays must be bold but extremely brief

**3. Dynamic visual rhythm** — even with simple art, the game feels alive through:
- rapid flashes
- temporary blackouts
- border changes
- movement speed that visibly escalates

## Core Systems (for the developer)
The game is built from simple components that combine into complex interactions.

**1. Unified snake movement system**
- Grid-based motion
- Variable speed based on mathematical scaling
- Deterministic movement steps (predictable for tuning)

**2. Deterministic difficulty scaling** — all difficulty uses formulas, not arbitrary values:
- movement speed escalation
- fruit spawn delay growth
- blackout intervals (Survival)
- border progression (Survival)

This makes the game testable, predictable and easy to balance.

**3. Hazard / boundary system** — the arena supports static obstacles, procedural obstacles and timed boundary shifts. Everything uses the same collision rules.

**4. Data-driven rendering** — each visual change is tied directly to a system event (flash → border shift). The blackout overlay briefly overrides rendering, and border layers are drawn with their strict color assignments.

## Identity Summary
Grossing Snake is not just a snake variant — it's a reactive grid game where the arena, obstacles and timing become part of decision-making. Its identity comes from math-driven pacing, high-speed readability, minimal but expressive visuals, predictable hazard logic, and an arena that changes just enough to keep players adapting.

---

## Survival Mode

### Core gameplay
The player controls a snake that must survive as long as possible. The playfield is large and initially open.

**Lose conditions:** hitting itself, hitting a bomb, or hitting the current active border layer.

### Item spawning
Every 2–4 seconds, regardless of whether existing items were collected:
- spawn 1 fruit on a safe tile
- spawn 1 bomb on a safe tile

A safe tile is not on the snake, a border wall, or another item.

### Border layers
The arena contains 5 nested rectangles:

| # | Layer |
|---|---|
| 1 | Black (outermost) |
| 2 | Grey |
| 3 | Maroon |
| 4 | Red |
| 5 | Orange (innermost) |

Each layer can temporarily become the active lethal wall. Only its boundary rectangle becomes a blocking wall.

### Layer shift cycle
Every 5 seconds:
1. The border rectangle flashes briefly (0.1–0.15s)
2. The active border moves to the next layer: **Grey → Maroon → Red → Orange → Black → repeat**

The playable space shrinks inward over 4 steps, then expands again from Orange to Black — keeping the player constantly repositioning.

### Fairness rules
- If the snake's head is outside the new safe region, reroll or delay the shift
- A wall shift never kills the snake instantly
- Active borders never appear on top of the snake's head

### Blackout mechanic
After collecting fruits, the screen briefly goes almost black. Frequency scales with progress:

```
interval = max(3, 10 - floor(totalFruits / 10))
```

- Early game: blackout every 10 fruits
- Later: every 3–5 fruits
- Minimum: once every 3 fruits
- Duration: 0.2–0.4 seconds (random)

**Blackout + border shift rule:** if a blackout happens at the moment of a border shift, the shift waits until the blackout ends — players are never killed by a change they couldn't see.

### Difficulty
- **Standard:** the player picks one system — blackouts *or* layer-shifting borders
- **Hardcore:** both systems active together, plus fruit + bomb spawns every 2–4 seconds, with no slowdown or grace period

At game over, the high score (fruits collected) is shown.

---

## Level Mode (Campaign)

### Shared formulas
With `i` = fruits collected so far:

```
speed = baseSpeed * (1 + 0.015 * i^1.4)
delay = baseDelay + (maxDelay - baseDelay) * (i / maxFruits)
```

- `baseDelay` ≈ 2 seconds, `maxDelay` ≈ 9.83 seconds
- Speed rises gently early, more strongly mid-game, and peaks near the final fruits
- Fruit appears quickly early on and takes longest near the end, forcing efficient play

Only one fruit exists at a time.

### Level 1 — Movement mastery
| | |
|---|---|
| Goal | Collect 30 fruits |
| Time limit | 80 seconds |
| Hazards | None — no bombs, no shifting borders, no blackouts |
| Teaches | Movement, timing under pressure, adapting to rising speed, efficient routing |

### Level 2 — Permanent obstacle pattern
| | |
|---|---|
| Goal | Collect 42 fruits |
| Time limit | 105 seconds |
| Carries over | Level 1 speed and spawn-delay formulas |

At the start, one obstacle layout is picked at random for the whole run:
- **A. Quadrant layout** — a vertical and a horizontal line through the center, forming a "+"
- **B. Diagonal layout** — two diagonals forming an "X"

Obstacles act as walls, fruit can't spawn on them, and they stay fixed for the level.

### Level 3 — Procedural 2-tile obstacles
| | |
|---|---|
| Goal | Collect 55 fruits |
| Time limit | 127 seconds |
| Carries over | Level 1 formulas only (no Level 2 layouts) |

At the start of each playthrough, generate **N = 5–9** obstacles, each exactly 2 tiles (horizontal or vertical — no diagonals, L-shapes or curves).

**Placement rules:** an obstacle must not overlap the snake's start area, the fruit, or other obstacles; must keep at least 1 tile of spacing from other obstacles; and must never trap the snake or create an impossible setup.

**Placement approach:** pick an orientation at random → pick a valid first tile → place the second tile beside it → validate, and reroll if invalid.

Level 3 is the final challenge: highest fruit count, longest timer, unpredictable layout, harsh late spawn delays and very high speed after 30+ fruits.

### Difficulty
- **Standard:** failing level 2 or 3 restarts from level 1
- **Easy:** failing a level restarts that same level

---

## Notes for the Team
- Game over / level complete screens are open to artistic variation
- Suggested art: animated snake with animations for each action, and a coordinate-board pattern with an artistic touch
- Main and pause menus need basic settings, including volume (sound effects are a must; music is optional)
- Add a credits scene!
