# Immersive Vision FX changelog

**Unreleased** lists changes that haven't shipped yet.

## Unreleased

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

### Fixed

- The profile editor's section revert, layer removal, and trait reorder controls work again.

## [14.2.2] - 2026-10-14

### Added

- This changelog, linked from the Foundry package page and the README.

### Changed

- Supports Foundry V14 only for now. I'll add newer Foundry versions after testing them.
- Updated the package description and author information.
- The module's website link now goes to its Foundry package page.
