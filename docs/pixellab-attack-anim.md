# Generating hero attack animations with PixelLab

Task: create a short **attack animation per hero class** to replace the
procedural `drawWeaponSwing` in `index.html`, matched to the 0x72 tileset look.

## Prereqs (see CLAUDE.md)
- A session where `api.pixellab.ai` is reachable (env egress allowlist + fresh
  session), with the `pixellab` MCP added. Or run PixelLab locally.
- Load the PixelLab tools and inspect them first (`tools/list`) — tool names vary
  (image generation, character rotation, skeleton/animation). Prefer an
  **"animate existing character"** flow so the result matches our sprites.

## Get a reference sprite for each class
Our atlas + coords are embedded in `index.html` (base64 `ATLAS_SRC` +
`const FRAMES = {...}`). Extract the idle frame `f0` for each class to feed
PixelLab as the character reference (keeps colours/silhouette consistent):
- Fighter → `knight_m_idle_anim_f0`
- Mage → `wizzard_m_idle_anim_f0`
- Rogue → `elf_m_idle_anim_f0`
- Ranger → `lizard_m_idle_anim_f0`

Decode `ATLAS_SRC` (512×512 PNG) and crop each `[x,y,w,h]` from `FRAMES` to a
16×28 PNG. (Node: write the base64 to a file, use any PNG lib, or reuse the
scratchpad `atlas.png` if regenerated.)

## Generation spec
- **Canvas:** 16×28, transparent background, single character, side-view facing
  **right** (the game flips horizontally for left-facing).
- **Frames:** 4 — wind-up → strike → impact → recover. Keep the feet/anchor
  consistent with the idle frame so it doesn't jitter (bottom-anchored).
- **Style:** match the reference exactly — same palette, soft/no outline, chunky
  16-bit dungeon look; no new colours, no glow bloom that blows the silhouette.

### Per-class prompt intent
- **Fighter (knight):** a decisive **sword swing** — raise the blade, chop
  forward/down, follow through across the body.
- **Mage (wizard):** a **staff cast** — plant the staff, thrust it forward, a
  small spark/orb forms at the tip on the strike frame (tiny, in-palette).
- **Rogue (elf):** a fast **dagger/short-sword stab** — short wind-up, quick
  forward lunge of the arm, recover to guard.
- **Ranger (lizard):** a **melee slash** with its blade (we use one attack for
  all melee) — a compact horizontal cut.

Generate, then export each class's 4 frames as PNGs (or a strip). Name them
`atk_<class>_f0..f3.png` (class = knight_m / wizzard_m / elf_m / lizard_m).

## Integrating into the game (`index.html`)
1. **Embed the frames.** Simplest: base64 each attack PNG into a JS map loaded as
   `Image` objects, e.g. `const ATTACK_IMG = { knight_m: [img0..img3], ... }`,
   plus a small `{w,h}` per frame. (Or composite them into the atlas and add
   `FRAMES` entries — more work.)
2. **Pick frames by class.** Add `spriteAtkKey` on the hero (the class base, e.g.
   `knight_m`) at `makeHero`.
3. **Play on attack.** In `drawHero(now)`, when `hero.atkAnim` is active, draw the
   attack frame for `progress = age/dur` (frame = floor(progress*4)) at the
   lunged position **instead of** the idle frame + `drawWeaponSwing`. Keep the
   existing lunge offset; delete/disable the `drawWeaponSwing` call.
4. Keep `hero.hurtAnim` (the real `_hit` frame + red flash) as-is for defending.
5. Enemies have no attack frame either — their lunge + the hero's flash already
   read fine; leave them, or later generate enemy attack frames the same way.

## Verify
- Trigger a fight; each class should show its swing/cast/stab on Attack, no
  jitter, correct facing (flips when the enemy is to the left).
- No console errors; publish the updated Artifact.
