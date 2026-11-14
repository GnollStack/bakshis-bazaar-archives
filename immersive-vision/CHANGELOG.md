# Immersive Vision FX changelog

**Unreleased** lists changes that haven't shipped yet.

## Unreleased

### Added

- Perceptive integration. With Perceptive active, IVFX decides how bright it is where a token stands for Perceptive's stealth and perception checks. Daylight through openings, moonlight, magical darkness, and darkness Regions now count the way you see them on the canvas. Each viewer's senses decide what they perceive: by default darkvision sees darkness and dim light as bright within its range, truesight, blindsight, and tremorsense see clearly within theirs, and a vision profile with strong light amplification sees dim light as bright. Perceptive keeps its rolls, DCs, spotting, and peeking. GMs find the options under **Perceptive Integration** in the module settings, including a D&D 5e vision rules option and an off switch. Verified with Perceptive 6.0.9; other Perceptive 6 versions run with a warning, and other versions are left alone.
- Vision Rules, a new window in the module settings. Turn on **Vision Authority** (then reload every client) and IVFX decides what each token sees: the light that reaches a target, the viewer's light sensitivity, and its darkvision. This works in both directions. A creature with sensitive eyes can now spot a target in faint light that Foundry's rules would hide, and a target in the dark stays hidden from eyes that can't see it. Midi-QOL, Stealthy, Limits, and Perceptive keep working on top. Off by default.
- Darkvision as a magical sense. Each vision profile has a **Darkvision** setting: None, From Character Sheet, Within Token Vision Range, or Unlimited. With Vision Authority on, only tokens with a vision profile get darkvision, and a token's vision range follows its darkvision, so natural eyes see only by light. Unlimited darkvision reaches past the sheet's range, including inside Limits Regions. Both rules can be set on or off separately.
- A **Dwarf** preset, with darkvision from the character sheet.
- **GM Tokens Don't Save Fog of War**, off by default. When on, tokens no player owns still see when you select them, but what they see isn't added to your fog of war, which saves frame rate while they move. Turn on **Remember Fog of War** in a token's Vision tab for the ones that should remember. Players' fog of war isn't affected. **Clear My GM Fog of War** in the module settings clears only your own fog of war in the current scene.
- Gradual fade. With Vision Authority on, creatures that are only just visible in faint light are drawn faint and become clearer as the light or the viewer's eyes improve. Their eyeshine stays bright. You can set the faintest opacity, or turn the fade off.

### Changed

- **Total-Darkness Sight** is replaced by the **Darkvision** setting. Profiles and tokens that had it on now use Within Token Vision Range, so they see the same as before; the others follow the character sheet, as before. The old night-vision glow is now called **Night-Vision Look** and no longer grants sight in the dark. The Human, Canid, Felid, Owl, and Ungulate presets have no darkvision.
- Vision 5e is no longer supported. It replaces Foundry's visibility checks, so while it is active Vision Authority stays off and IVFX only draws. GMs see a warning when both are active.
- The vision mechanics observer now reports each token's darkvision from its vision profile and the world's profiles, never the GM's own default settings.
- Token moves cost less. IVFX now remembers how lit each spot is until the lighting changes, instead of working it out again every frame. In my 120-token test scene the slowest frame of an animated move is now about the same as without IVFX, where it used to be about 14 ms slower. With Vision Authority on, turns and single moves cost the same as Foundry's own vision, and an animated move about a fifth more.
- Verified on Foundry 14.370, and tested with dnd5e 6.1.0.

### Fixed

- Players now see eyeshine on a hidden creature that Perceptive shows them after they spot it, faded like the creature itself.
- Outdoor light through a cave opening now updates when walls change the shape of a wall-restricted Region, or when its wall restriction is turned on or off. Before, the light kept the old shape until the scene was redrawn.

## [14.4.0] - 2026-11-05

### Changed

- IVFX no longer turns itself off. Unfamiliar vision modes such as Tremorsense, vision modes or shaders replaced by another module, and graphics-budget checks used to switch the view back to Foundry's standard vision. The effects now stay on, and the per-client master toggle still turns them off.
- On graphics hardware that can't run one of the vision effects, IVFX skips only that effect and keeps Foundry's normal rendering for it. The other effects stay on, and you see one message about it.
- Turns, token moves, and token selection cost less with IVFX enabled. In my 60-token test scene IVFX no longer adds measurable frame time. With 120 tokens, the slowest frame of a token move takes about 2 ms longer than without IVFX.
- Removed the fallback warnings and the search for the module behind a fallback, since IVFX no longer falls back.

### Fixed

- IVFX no longer pauses its effects when another module changes how Foundry decides what a token can see. This affected Midi-QOL, Limits, Vision 5e, and Stealthy. Effects now stay on, and creatures those modules hide stay hidden. In those worlds light amplification won't reveal extra tokens.
- In those same worlds, IVFX no longer switches to Foundry's standard vision and back each time a token is selected or released. Each switch rebuilt the scene's vision, several times every combat turn.
- If turning IVFX on or off runs into an error, it now tries again a few times instead of staying off until you toggle it. If it still can't, you get one message saying what to do.
- If another module replaces the canvas filters IVFX draws with, IVFX puts its effects back within about a second. Before, they stayed gone until the next scene change.
- A module that changes token detection is now recognized even when it loads before IVFX, so creatures it hides stay hidden.
- Color Tint now keeps its color when a profile also uses a species spectrum or always-on Dichromacy. A green tint on a Canid profile used to turn a dim yellow. Profiles combining those settings will look different after updating, and can be adjusted with Color Tint Strength.

## [14.3.3] - 2026-11-01

### Fixed

- The Outdoor Light settings in Scene Configuration appeared on every tab. They now sit at the bottom of the Visibility tab and scroll with it.

## [14.3.2] - 2026-10-30

### Added

- IVFX now tells you when it falls back to Foundry's standard vision. GMs see a short on-screen warning once per session when other code or the device is the cause, and every client's browser console names the reason, the token and scene, any code that replaced Foundry's vision behavior, and the active modules and world scripts. For GMs it also names the module or file responsible when it can find one. Vision modes that are simply unsupported, such as Tremorsense, stay in the console. Players see the on-screen warning only when their own graphics hardware is the reason.

## [14.3.0] - 2026-10-25

### Added

- Patreon and Discord badges on the README and the Foundry listing.
- Clients whose graphics hardware cannot hold the vision shaders now see Foundry's native vision for the whole view instead of a broken image. The status diagnostics report the device limit.
- The README now explains how lights, tokens, and Regions behave on scenes with Levels: each follows Foundry's level assignment, and a light with no level restriction is present on every level.

### Changed

- Documentation links now point to Bakshi's Bazaar Archives and the permanent Discord invite.
- The viewer effects now handle up to 96 visible light sources at once, up from 48, while using less graphics memory for shader data than before. Rendering is unchanged.
- Lights past that limit keep Foundry's normal appearance without IVFX fading or peripheral relief, and the browser console notes it once per session.
- The module loads less code at startup. The GM diagnostics tools load only when they are used.
- Debug logging no longer costs anything while it is turned off.
- Leaving a scene cleans up every effect in a fixed order, and one failed step no longer skips the rest.
- Verified on Foundry 14.369.

### Fixed

- The profile editor's section revert, layer removal, and trait reorder controls work again.

## [14.2.2] - 2026-10-14

### Added

- This changelog, linked from the Foundry package page and the README.

### Changed

- Supports Foundry V14 only for now. I'll add newer Foundry versions after testing them.
- Updated the package description and author information.
- The module's website link now goes to its Foundry package page.
