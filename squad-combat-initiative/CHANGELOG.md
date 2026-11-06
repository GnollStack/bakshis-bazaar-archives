# Squad Combat Initiative changelog

**Unreleased** lists changes that haven't shipped yet.

## Unreleased

### Changed

- **Player Morale Visibility** now defaults to **GM Only**, so players no longer see enemy morale unless the GM turns it on. A world that already saved this setting keeps its choice.

## [14.4.1] - 2026-11-01

### Changed

- Shorter, clearer hints on the two diagnostics settings.
- Clearer wording on the bug report form's log field.

## [14.4.0] - 2026-10-31

### Added

- Shared initiative mode: a squad rolls once and every member carries that value, including members who join later. Mixed squads work, and native rolls that get replaced return if the squad leaves the mode.
- Squad cards for Carousel Combat Tracker, tested with 5.0.3. Turn them on in the new Carousel Settings to collapse each squad to one card. The active squad opens along the bar, down from its card, or one member at a time. Cards can show squad health to GMs and Assistants, glow when up next, dim when defeated, and be clicked or pinned open.
- Encounter squads: placing a dnd5e Encounter actor groups its combatants into one squad, with the first Important NPC as captain. On by default; it needs dnd5e's Encounter placement option to add members to combat.

### Changed

- Requires D&D 5e 6.0.6 or later and Foundry VTT 14.367 or later. 14.3.0 is the last release for D&D 5e 5.3.3.
- Fleeing is now a dnd5e condition, so it appears in the token HUD and condition immunity lists, and immune actors ignore it.

### Fixed

- Creating a squad with tokens selected no longer fails in an encounter that isn't linked to a scene, which is what the tracker creates by default.
- Hiding or showing a squad now also updates its tokens in those encounters.
- Morale Effect Duration now works for Frightened and Prone. Those effects never expired before, so worlds that set a duration will now see them end.
- A repeat morale failure now restarts a timed effect and reactivates an expired one.

## [14.3.0] - 2026-10-31

### Changed

- Verified on Foundry VTT 14.369 with dnd5e 6.0.6.
- Tracker rendering now registers through libWrapper for better compatibility with other combat tracker modules.
- Squad cards render from a template; their appearance is unchanged.
- Reorganized the squad manager internals; no behavior change.
- Updated the screenshots to the current squad card interface.
- Updated documentation links to Bakshi's Bazaar Archives and the permanent Discord invite.

### Removed

- Removed Ko-fi support links from the Markdown README and generated HTML listing.

## [14.2.1] - 2026-10-14

### Added

- This changelog, linked from the Foundry package page and the README.

### Changed

- Updated the package description and author information.
- Reorganized the guide around quick setup, squad controls, initiative, captains, and morale.
- Clarified the libWrapper requirement and added a bug-report link to the package information.
