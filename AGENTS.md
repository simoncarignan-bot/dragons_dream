# Rules for AI helpers

Dragon's Dream is a kid-safe offline game for a child about 10–12. Read this file before you change anything.

## Keep it one file

- All game code, art, style, and text stay in `index.html`.
- No build step. No bundler. No package install.
- No external libraries, CDNs, fonts, images, or scripts. Drawing uses canvas and system fonts only.
- The page must open by double-clicking `index.html`, and from GitHub Pages, with no network.
- Leave `.nojekyll` in place so GitHub Pages serves the file as-is.
- Leave the Content-Security-Policy as it is: `connect-src 'none'`, `font-src 'none'`, `img-src data:`.

## Devices

It must work on iPad Safari, phones, and computers.

- Touch and mouse both work. Map and buttons use pointer events.
- Buttons use `touch-action: manipulation`. The viewport sets `maximum-scale=1`. The map cancels double-click. Do not bring back double-tap zoom.
- Tap targets stay large enough for a finger. Check a narrow phone width and a tablet width, not only a desktop window.

## Kid-safe and private

- Content stays gentle. No scare content, no romance, no rude language.
- No network requests of any kind (no `fetch`, XHR, WebSocket, beacons, or remote URLs).
- No accounts, ads, payments, chat, or tracking.
- Audio uses the browser's built-in Web Audio API only, and only after a tap.

## Saves

Never break an existing save. Players keep progress in this browser.

- The game object field `version` is currently `1`. `normalizeSave` accepts only that version.
- When you change the save format, bump `version` and add a migration. The loader must still accept the previous version and convert it. Do not reject an older save.
- Small new fields can follow the existing additive migrations: `ensureV4`, `ensureV6`, and `ensureV10`. `hydrateSave` runs those after `normalizeSave`, before the first render. Old saves must gain the new fields and keep playing.
- Do not rename or delete these stores:
  - `dragons-dream-v1` (`KEY`) — legacy copy of the game JSON.
  - `dragons-dream-v1-audio` (`AUDIO_KEY`) — `{sfx, music}` only. It is separate from the world.
  - Vault format `DDVAULT7`: `dragons-dream-save-v7` (main), `dragons-dream-save-v7-temp` (staging), `dragons-dream-save-v7-backup-1`, `-2`, and `-3`.
  - IndexedDB database `dragons-dream-vault`, object store `records` (the backup copy of the same vault records).
  - `dragons-dream-kept-world-v9` (`KEPT_WORLD`) — one protected previous world.
- `OLD_KEYS` is only cleared when a grown-up reset record is committed. Do not wipe those keys on a normal save.
- Portable Save codes start with `DD7.`. Import previews the world before it replaces anything.
- Keep the child-facing **Save** button, and keep the **Grown-ups** tools (copy or download a Save code, import a code, and start a new world only after a clear confirmation).

## How to change the game

- Keep each change small.
- Test in a browser before you commit. Try the screen you changed with a tap and with a mouse. Load a page that already has a save if you touched saving.
- Commit messages say what changed, in plain words a parent can read.

## Where things live in `index.html`

One `<style>` block, a short page shell (header, map canvas, buttons, dialog overlay), then one `<script>`.

1. **Data and persistence** — `KEY`, dragon `TYPES` and `RARITIES`, `normalizeSave`, skills, `startGame`, `createWorld`.
2. **Sprites** — theme drawing and `dragonSVG` / `heroSVG` / `eggSVG`. The V14 renderer below supplies the actual pictures.
3. **UI screens** — title, toasts, modals, castle, roster, field guide, handbook. Buttons use `data-action` (no inline handlers).
4. **Encounters and turn-based combat.**
5. **World renderer and movement** — 4800×3600 map, tile navigation, terrain cache, pointer and keyboard walking.
6. **V4 adventures** — `ensureV4`: story, biomes, guardians, care, haven decor. Sets `worldVersion` to 4.
7. **V6 nest** — `ensureV6`: adult breeding, gender, strength. Babies still hatch at level 1.
8. **V7 save vault** — checksummed `DDVAULT7` records, localStorage checkpoints, IndexedDB mirror, Save codes.
9. **V8** — the child Save button. The V7 vault stays underneath.
10. **V9** — start screen slots and the kept previous world.
11. **V10 leash** — `ensureV10`: friendship leashes and catches. Leashes are free gifts for making friends.
12. **V11** — how many wild dragons appear nearby.
13. **V12** — castle sanctuary. Wild dragons stay outside the fence. The player's walking does not change.
14. **V13** — space between dragons on the map.
15. **V14** — illustration renderer only. Game rules stay the same.
16. **Boot** — `bootSaveVault()` finishes before play. Then `requestAnimationFrame(frame)`.
