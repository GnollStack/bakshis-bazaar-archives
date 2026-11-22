# FileSmith changelog

**Unreleased** lists changes that haven't shipped yet.

## Unreleased

Planned for **V14.4.1**.

### Added

- Tidy 5e character and NPC sheets now show **Select items** on Inventory and **Select spells** on Spells, just below the search and filters.

### Fixed

- Transfer checked gear between Tidy and standard D&D 5e sheets. When one NPC gives another the SRD Gnoll’s light Hide Armor and Shield, both arrive as equipment even if the recipient has Spells open. The shield keeps its equipment type and the hide keeps its light armor subtype. Copy keeps the originals, and Move removes the confirmed quantities. Automatic follows Traffick’s actor-sheet drag preference.
- Dragging several world Items onto a standard PF2e actor sheet now delivers the full selection, including when Spellcasting is open or the drop targets a backpack. The original world Items stay in the sidebar.

## [14.4.0] - 2026-11-21

### Added

- Select gear and learned spells on stock D&D 5e and PF2e character or token sheets. Check individual rows, Shift-click a range, and keep checks across tabs. Drag the grip into a journal or copy links to the actor’s own items.
- Pack and unpack checked gear, give whole or partial stacks between owned inventories, and review quantity or equipment changes. GMs can preview edits across selected tokens. The preview counts a shared actor once and identifies missing or ambiguous matches.
- Choose **Automatic**, **Move**, or **Copy** for between-inventory gear drags. Automatic follows Traffick’s D&D 5e drag preference on actor sheets and otherwise defaults to moving gear when Traffick is absent. World rules control player transfers and deletion.
- Right-click a checked D&D 5e gear or spell row for **Duplicate ×N** or **Delete ×N**. Both supported systems offer reviewed deletion from the sheet action bar. Deleting a bag preserves unchecked contents. PF2e reviews linked grants and clears affected prepared-slot references.
- Drag selected sidebar Items onto an editable actor sheet, insert linked bullets into an editable journal, or import selected documents from a readable compendium. The import review skips known copies by default. GMs can organize unlocked world packs and move pack entries in the Compendium Packs sidebar.
- Right-click highlighted world documents for counted **Duplicate**, **Delete**, **Move to Root**, **Copy**, and **Cut** actions. Each duplicate stays in its original folder. Right-click the directory background for **Paste to Root**, creation commands, and **Undo Last Move** when available.

### Changed

- **Open entries with** combines directory opening preferences. Existing mixed settings appear under **Custom**. The sheet action bar groups editing, links, and deletion under **Actions** when space is shared with gear controls.
- Clicking away clears checked gear and spells by default. Each player can turn off click-away, and closing a sheet always resets its selection.

### Fixed

- Dragging any checked gear row transfers the whole selection to the recipient’s inventory, including when their Spells tab is open. Moving gear with a compendium source removes only the transferred quantities from the owned inventory and preserves the source compendium.

## [14.3.6] - 2026-11-02

### Added

- Added Patreon and permanent Discord invite badges to the README and Foundry listing.

### Changed

- Verified on Foundry VTT 14.369. Container-aware copy and deletion are now also tested with D&D 5e 6.0.6.
- Updated documentation links to Bakshi's Bazaar Archives and the permanent Discord invite.
- Corrected the README version badge to 14.3.5.

## [14.3.5] - 2026-10-14

### Added

- This changelog, linked from the Foundry package page and the README.

### Changed

- Supports Foundry V14 only for now. I'll add newer Foundry versions after testing them.
- Updated the package description and author information.
- The module's website link now goes to its Foundry package page.
