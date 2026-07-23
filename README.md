# ⛧ Procedural Dungeon

A **seed-deterministic dungeon crawler** in a single, dependency-free HTML file,
rendered with real **pixel-art** sprites on a crisp `image-rendering: pixelated`
canvas. Each room has up to four exits (N / E / S / W) and is populated with
obstacles and encounters — **every one of which can be resolved through your
character's stats, skills, inventory, weapons, or allies.** Walk through an exit
and the adjacent room generates on the fly, building an explorable multi-room
dungeon that descends through deeper, deadlier levels.

Animated sprites (idle-looping heroes, monsters, coins, spike traps), tiled
stone floors and walls, heart-pip HP, and weapon-sprite icons all come from the
embedded **DungeonTileset II** atlas — see [Credits](#credits).

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
| 📦 **Supply crate** | Pry open (or rummage — Investigation) for **torches**, sometimes a potion |
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

## Light & danger

- **Torches are a consumable light source.** You start with one lit; it burns
  down in real time, and the pool of light shrinks as it dies. **Light Torch**
  spends a spare from your pack to relight (watch the fuel bar on your sheet).
  Let it gutter out and the darkness closes in to a dim, close circle — the
  dungeon is genuinely **torch-lit fog of war**.
- **Torches are loot.** Fresh torches turn up in treasure hoards, chests, and
  **supply crates**, and can even be salvaged off a defeated monster — so light
  is a resource you explore and fight to keep stocked.
- **Enemies bar every exit.** While a monster prowls the room, all four doors
  are sealed (shown barred in red). You must **defeat it, sneak past it, parley,
  or turn it** before the exits reopen — clicking a barred door surfaces the
  monster's options. Monsters visibly **lunge** when they strike you and burst
  into dust when they fall.

## Controls

- **New Dungeon** — generate a world from the seed box (blank = random seed)
- **New Hero** — re-roll your adventurer for the current dungeon
- **Search Room** — reveal secret exits, hidden traps, and trap doors
- **Light Torch** — spend a spare torch to restore your light
- **Short Rest** — heal between fights
- Click any **door** or **object** in the room to open its resolution panel

## Under the hood

Pure vanilla JS, no dependencies and no build step, organized into clear
sections inside `index.html`:

- **SPRITES** — the DungeonTileset II atlas embedded as a base64 data URI plus a
  `{name: [x, y, w, h]}` frame table; `pxIcon()` slices static UI icons via CSS
  `background-position`, and the canvas slices animated sprites via `drawImage`
- **RNG** — `xmur3` string hash + `mulberry32` PRNG for deterministic generation
- **CHARACTER** — stats, skills, modifiers, inventory helpers
- **ENCOUNTERS** — data-driven definitions; each obstacle lists its resolution methods
- **DUNGEON** — on-demand room generation with **reciprocal exits** (the wall
  between two cells is derived from a shared canonical key, so neighbours always agree)
- **RENDER** — a 12×12 tile pixel-art canvas (floors, walls, doors, sprites) with
  a `requestAnimationFrame` idle-animation loop, plus the character sheet,
  mini-map, and event log

Everything is embedded — the atlas is a ~39 KB PNG, so the whole game is one
self-contained ~120 KB file that runs from `file://` with no assets to load.
`prefers-reduced-motion` is respected (the animation loop is skipped).

## Ideas to extend

- Persist progress to `localStorage`; add a shareable URL that encodes the seed
- Boss rooms / a descending objective (retrieve an artifact from Depth N)
- More encounter types (curses, illusions, merchants, environmental darkness needing a lit **Torch**)
- Walk/run animations when moving between rooms; animated dice rolls; sound

## Credits

Pixel art: **[DungeonTileset II](https://0x72.itch.io/dungeontileset-ii)** by
**0x72**, released under **[CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/)**
(public domain). The tileset atlas is embedded in `index.html`. No attribution is
required under CC0 — it's included here with thanks.

Game code © 2026 domvmd, MIT licensed (see `LICENSE`).

---

*Built for fun. Seeds are deterministic — share one and share the dungeon.*
