# Show of Hands: Targeting & Cursors changelog

**Unreleased** lists changes that haven't shipped yet.

## Unreleased

## [14.2.5] - 2026-11-02

### Changed

- Shorter, clearer hint on the diagnostics setting.

### Fixed

- The README and listing showed an out-of-date version number.

### Removed

- Removed the developer diagnostics section from the README and listing.

## [14.2.4] - 2026-10-25

### Added

- GM Pull Ping View world setting: fit the GM's visible map area to each viewer's screen (default), keep each viewer's zoom, or match the GM's numeric zoom (labeled Foundry VTT default). Uses Shift + hold-left-click and applies once to viewers already on the same scene and level. Different screen shapes can show extra map at the edges; Foundry's camera limits still apply. Reload all clients after updating.

### Fixed

- Private pull pings now use the current canvas zoom limits instead of clipping valid zoom levels below `0.1` or above `3`.
- Switching Show Identity on Idle off during a cursor fade no longer applies opacity twice.

### Changed

- Updated documentation links to Bakshi's Bazaar Archives and the permanent Discord invite.

### Removed

- Removed Ko-fi support links from the Markdown README and generated HTML listing.

## [14.2.3] - 2026-10-14

### Added

- This changelog, linked from the Foundry package page and the README.

### Changed

- Supports Foundry V14 only for now. I'll add newer Foundry versions after testing them.
- Updated the package description and author information.
