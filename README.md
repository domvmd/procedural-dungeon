# ⛧ Procedural Dungeon

A **seed-deterministic dungeon crawler** in a single, dependency-free HTML file.
Each room has up to four exits (N / E / S / W) and is populated with obstacles
and encounters — **every one of which can be resolved through your character's
stats, skills, inventory, weapons, or allies.** Walk through an exit and the
adjacent room generates on the fly, building an explorable multi-room dungeon
that descends through deeper, deadlier levels.

> **Play it:** just open `index.html` in any modern browser. No build, no server,
> no dependencies.

---

## The idea

The whole game is built around one loop: **you face an obstacle → the game shows
you every approach your character actually qualifies for → you pick one and roll
(or spend an item / call an ally) → the world reacts.** Options you *can't* take
(no lockpicks, wrong class, a step you skipped) are greyed out, so the value of
each stat, skill, and item is always visible.

Because the world is generated from a **seed**, the same seed always produces the
same dungeon — share a seed and a friend explores the identical labyrinth.

## What's in a room

### Exits (one possible per wall — N/E/S/W)

| Exit | Resolved by |
|---|---|
| 🚪 **Open passage** | Just walk through |
| 🔒 **Locked door** | Pick lock (DEX + Thieves' Tools) · a **Key** · break it (STR/Athletics) · **Scroll of Knock** |
| 🪨 **Collapsed / rubble** | Athletics (STR) · a **Crowbar** |
| ✨ **Warded gate** | Arcana (INT) · **Scroll of Dispel** |
| 🕳️ **Secret tunnel** | Hidden until you **Search** the room (Perception/Investigation), then walk through |

### Room contents

| Feature | Resolved by |
|---|---|
| 💰 **Treasure hoard** | Free to grab |
| 🧰 **Locked chest** | Pick lock · **Key** · smash open (loses some loot) |
| 🧨 **Trapped chest** | Spot the trap (Investigation) → disarm (DEX+Tools), or force it and gamble on damage |
| 🐉 **Monster** | Fight (weapon attack + allies vs its defense) · sneak past (Stealth) · parley (CHA) · **Holy Water** vs undead |
| ⚠️ **Hidden trap** | Spot it (Perception) → disarm (DEX+Tools) or leap it (Acrobatics) |
| ⛩️ **Shrine** | Pray (WIS/Religion) or leave 20 gold → healing + "+1 to all checks" blessing |
| 📜 **Riddle altar** | Arcana or Investigation → loot & a potion |
| 🧍 **Captive** | Free them (Lockpicking · Persuasion · Athletics) → they join you as an **ally** |
| 🕳️ **Chasm** | Leap it (Athletics) or cross safely with **Rope / Grappling Hook** |
| ⬇️ **Trap door** | Hidden until searched → rope down safely, or drop (DEX save) to the level below |
| ⬆️ **Stairway up** | Always present on deeper levels — climb back toward the entrance |

## Your character

Roll a fresh hero any time with **🎲 New Hero**. Four archetypes (Rogue,
Fighter, Mage, Ranger) each start with different proficiencies, gear, weapons,
and allies — so the *same room* is a different puzzle depending on who you are.

- **6 stats** (STR/DEX/CON/INT/WIS/CHA) → modifiers on every check
- **Skills** mapped to stats, some with a proficiency bonus
- **Inventory** of tagged items (keys, tools, rope, torches, potions, scrolls, holy water…)
- **Weapons** (attack bonus + damage die) and **allies** (assist your rolls / add combat power)
- **HP** — take too much damage and you fall; **🔥 Short Rest** to recover

## How resolution works

Every check is `d20 + ability modifier + proficiency (+ ally / blessing bonuses)`
versus a **difficulty class (DC)** that scales with your depth. Natural 20 always
succeeds; natural 1 always fails. Items and allies open alternative paths that
sidestep a roll entirely (a key beats any lock; rope makes any chasm trivial).

## Controls

- **New Dungeon** — generate a world from the seed box (blank = random seed)
- **New Hero** — re-roll your adventurer for the current dungeon
- **Search Room** — reveal secret exits, hidden traps, and trap doors
- **Short Rest** — heal between fights
- Click any **door** or **object** in the room to open its resolution panel

## Under the hood

Pure vanilla JS + inline SVG, organized into clear sections inside `index.html`:

- **RNG** — `xmur3` string hash + `mulberry32` PRNG for deterministic generation
- **CHARACTER** — stats, skills, modifiers, inventory helpers
- **ENCOUNTERS** — data-driven definitions; each obstacle lists its resolution methods
- **DUNGEON** — on-demand room generation with **reciprocal exits** (the wall
  between two cells is derived from a shared canonical key, so neighbours always agree)
- **RENDER** — top-down SVG room, character sheet, mini-map, event log

## Ideas to extend

- Persist progress to `localStorage`; add a shareable URL that encodes the seed
- Boss rooms / a descending objective (retrieve an artifact from Depth N)
- More encounter types (curses, illusions, merchants, environmental darkness needing a lit **Torch**)
- Sound, keyboard movement, and animated dice rolls

---

*Built for fun. Seeds are deterministic — share one and share the dungeon.*
