# Procedural Dungeon — project memory

Seed-deterministic pixel-art dungeon crawler in a single self-contained
`index.html` (vanilla JS + a pixel-art `<canvas>`; no build step). Art is
**0x72 DungeonTileset II (CC0)**, embedded as a base64 atlas inside `index.html`
with a `FRAMES` table `{name: [x, y, w, h]}`. Repo: `domvmd/procedural-dungeon`.
Deploy loop: edit `index.html`, then a self-contained copy is published as a
Claude Artifact (strip `<style>…</script>`, drop the `</head><body>` tags).

## Sprite conventions
- Heroes are **16×28** sprites. Class → atlas base:
  Fighter=`knight_m`, Mage=`wizzard_m`, Rogue=`elf_m`, Ranger=`lizard_m`.
  Each has `_idle_anim_f0-3`, `_run_anim_f0-3`, and a single `_hit_anim_f0`.
  **There is no attack frame in the tileset.**
- Enemies: small 16×16 (goblin, skelet, swampy), tall 16×23 (orc, chort,
  necromancer), large/boss 32×36 (ogre, big_demon, big_zombie).
- Combat is round-based (`renderCombatPanel` / `combatAction`). On-map combat FX
  live in `drawHero()`: a lunge toward the enemy plus a **procedural
  weapon-swing** (`drawWeaponSwing`) that rotates the weapon sprite — it looks
  rough, especially for the mage. `hero.spriteHit` is the recoil/flinch frame.

## Pending work: real attack animations via PixelLab
Goal: replace the janky `drawWeaponSwing` with **generated 16×28 attack frames
per class**. Full spec + prompts + integration steps → `docs/pixellab-attack-anim.md`.

PixelLab (`https://api.pixellab.ai/mcp`) is **network-blocked** on the restricted
Claude-Code-on-web egress policy. To use it here:
1. The **environment's network-egress allowlist** must include `api.pixellab.ai`
   (this is the environment/network policy — NOT Settings → Claude Code →
   "Allowed sites", which only governs the browser tool). The change only applies
   to a **freshly started session**, never the running one.
2. Re-add the MCP in the new session (its config is per-container, not committed):
   `claude mcp add pixellab https://api.pixellab.ai/mcp -t http -H "Authorization: Bearer <YOUR_PIXELLAB_TOKEN>"`
   **Do not commit the token — this repo is public.**
3. Verify connectivity, then follow `docs/pixellab-attack-anim.md`.

Zero-config alternative: run PixelLab in **local Claude Code** (no egress
firewall) and drop the generated PNGs into the repo; I'll wire them in.
