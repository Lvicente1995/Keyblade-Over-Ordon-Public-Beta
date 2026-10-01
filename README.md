# Kingdom Key — Ordon Sword

Custom **v0.2.1** cross-platform source targeting the same Dusklight 2.0.3-era SDK revision as v0.2.0, prepared from the supplied `kingdomKey.fbx`. Windows retains the custom Kingdom Key audio path; Linux/Steam Deck and Android use native sword audio as a safe fallback.

## Install

Copy `Kingdom-Key-Ordon-v0.2.0-win64.dusk` into `%APPDATA%\TwilitRealm\Dusklight\mods`, then enable **Kingdom Key — Ordon Sword** in Dusklight's mod menu. Equip the Ordon Sword. If Dusklight uses a custom user directory, use that directory's `mods` folder.

When upgrading, remove the old Kingdom Key `.dusk` package from that folder before adding this one. Install only one version of this mod at a time. Close Dusklight first if its running copy locks the old package.

The `.dusk` file is already the installable package; do not extract it. Disable it in the mod menu to return to the original sword. The game disc is not modified.

## Appearance and movement

- Original full-detail Kingdom Key geometry: 52,072 triangles, silver shaft and crown teeth, gold guard, blue neck, black grip, and Mickey charm.
- Grip aligned to the Ordon Sword. Tip reaches 99.37 game units versus the original 99.67, preserving the original attack reach.
- Fourteen individual chain links plus a heavier terminal charm move under gravity and inertia. Motion reacts to the weapon rather than repeating a baked animation.
- Chain constraints retain their length during fast motion. Basic torso and floor contacts reduce clipping. Motion resets safely when changing form, drawing/stowing, or teleporting.
- The Keyblade materializes in Link's hand and vanishes when put away. Its body, chain and charm leave together; no weapon, scabbard or weapon shadow remains on Link's back.
- Summoning, dismissal and enemy-hit effects use selected Kingdom Hearts III textures, effect meshes and Cascade particle settings from the user's installed copy, adapted to Dusklight's renderer. These replace the earlier procedural approximation.
- Authentic Kingdom Key audio accompanies appearing, disappearing and confirmed enemy contacts. KHIII references the same appearance effect and sound cue `se02001_010` for both appearing and disappearing. Seven original hit-family cues provide impact variations.
- Link's sword arm no longer performs the normal reach-behind draw, sheath or victory-flourish gesture. An independent animation preserves the shield arm's native movement and transfers the shield at its hand/back contact frame. It plays at 75% native speed with a five-tick settling blend, following feedback that the initial version moved too quickly. Scripted event animations retain their native timing.
- Metal highlights use source metalness and roughness with the game's sunlight, room lights and fog. Brightness is balanced for the supplied solid materials.
- Native frame interpolation, inventory-model rendering, custom shadow geometry, and mirror rendering are implemented.

## Current scope

This is a custom v0.2.0 build. **The user confirmed the corrected draw/dismiss effects: “The effects look right now”.** The correction aligns the glow and spirals along the blade, restores material color, opacity and scrolling, and removes the old forced white flash over the whole weapon. Slower shield movement, audio and hit triggering were approved earlier; the latest shader changes were not separately retested on enemy hits. The corrected build passed the supported build script and reloaded without errors. Earlier Collection-preview checks ran at 60 FPS; this does not establish combat performance. Read `VALIDATION.md` for completed checks and remaining limits.

Sword damage, hitboxes, attack animations and attack trails retain Ordon behavior. Native equipment changes complete immediately while the cosmetic weapon transition and shield gesture run independently. The Keyblade hit effect/audio trigger is limited to confirmed, nonblocked enemy contacts and deduplicated per target per game tick. It replaces the native generic hitmark only for those accepted contacts; collision, damage and enemy reactions remain unchanged. Original draw/sheath sounds are suppressed when the replacement cue is available. Other game audio, inventory icons, item text and separate pickup/cutscene prop models retain their originals.

The inventory preview uses a static chain pose. Physics uses approximate torso/floor contact rather than full environment or chain self-collision. Compatibility with other sword replacers, remote co-op avatars and every gameplay situation has not been established.

The Blender preview is a studio render, not an in-game screenshot. The game's metal lighting approximates the source material. KHIII particle timing, masks, mesh geometry and vertex color/alpha gradients are retained where supported. The source effect basis (+X along the blade, +Z toward the teeth) is aligned to the replacement with a 1.26 scale. Engine-specific material effects such as Fresnel, erosion, lighting and compositing remain approximations; this is not a claim of pixel-identical KHIII rendering.

## Asset sources and storage

The weapon model comes from the supplied `kingdom-key.zip`. Selected sounds and visual-effect assets come from the user's local Kingdom Hearts III installation. Its archives were read in place; only the needed files were extracted into the D: workspace. No game or full archive was copied, and no extracted game assets were placed on C:.

The original Twilight Princess ISO, original Dusklight installation and original saves were not modified during development. Testing uses an isolated Dusklight copy and a copied save. Installing this mod adds a package to Dusklight's mod directory; it does not patch either game's files.

## Editable source

`../model/kingdom-key-rigged.blend` contains the original detailed mesh with separate bones for all fourteen links and the charm. `src/mod.cpp` contains the Dusklight integration; `src/chain_physics.hpp` contains the solver. `src/keyblade_presence.hpp` controls visibility, `src/shield_gesture.hpp` controls the independent shield gesture, and `src/keyblade_audio.hpp` supplies native sound playback. `src/kh3_fx.hpp`, `src/kh3_fx_data.hpp` and `res/kh3fx/` contain the adapted particle renderer, settings and assets; texture/material metadata records the source masks, colors, tiling and addressing. The older `src/summon_fx.hpp` and its tests document the earlier procedural iteration.

`tools/kingdom-key-mesh.json` and `tools/export_mesh.py` regenerate `res/kingdom_key.mesh` with Python 3. `tools/convert_kh3_fx_meshes.py` and its validation report document conversion of the selected KHIII effect geometry.

## Cross-platform support

This source tree is prepared for Windows AMD64, Linux x86_64 (Steam Deck), and Android ARM64. See `CROSS_PLATFORM.md` for platform-specific notes. The included GitHub Actions workflow builds all three and merges them into one multi-platform `.dusk`.

## Build

Use a Windows x64 MSVC developer environment and Python 3.8 or later. Obtain the official Dusklight source and its Aurora submodule at these revisions:

- Dusklight v2.0.3: `40457c6adb381928e4b5fef6ed459ed291edd5e2`
- Aurora: `3227d76c60e1e782ca576610bce61c9e7744d8be`

Run `tools/build_windows.ps1 -DusklightSource <source-folder> -ImportLibrary <Dusklight-install>\sdk\windows-amd64.lib` from a Visual Studio developer shell. The source does not require the game disc to compile. The native library must target the game's MSVC ABI; ordinary MinGW C++ ABI is not compatible.

Package contents are `mod.json`, `lib/windows-amd64/mod.dll`, and `res/`. The tested build script compiles against the official SDK and creates `build/mods/kingdom_key.dusk`. An optional CMake project is also included.

The `tools/` folder includes physics, geometry, interpolation, weapon-presence and shield-animation checks, with recorded results. `tools/kh3_fx_regression.cpp`, `tools/kh3_fx_budget.cpp` and `tools/kh3-fx-results.txt` cover the current KHIII effect simulation and render payload. The earlier procedural-particle checks remain as iteration history and do not validate the new renderer. Keep assertions enabled when running C++ tests. The source archive also includes the rigged Blender file, a studio preview and the geometry interchange files in its sibling `model/` folder.

Official references: [modding API](https://github.com/TwilitRealm/dusklight/blob/v2.0.3/docs/modding.md), [game source](https://github.com/TwilitRealm/dusklight/tree/v2.0.3).
