# 5e Activity Importer changelog

**Unreleased** lists changes that haven't shipped yet.

## Unreleased

### Added

- Added Patreon and permanent Discord invite badges to the README and Foundry listing.

## [14.2.0] - 2026-10-18

### Added

- Editable recipes for conditional effects, filtered roll bonuses, AC calculations, minimum proficiency, effect expiry, area behaviors, transformations, and shared effects.
- MIDI-QOL and DAE recipes when compatible integrations are active. Loading a recipe asks before replacing a draft and checks its required documents.
- Optional Compact previews alongside the default full mechanical detail, with remembered panel states. Warnings and missing references remain visible.
- **Import options > Restore defaults** resets import and view settings while keeping drafts, the target Item, resolved references, presets, and history. **Reset layout** changes only display preferences.
- Inline reference search and drag-and-drop, plus **Resolve All** for unresolved references.
- A public changelog linked from the Foundry package page and README, plus updated import-window and authoring guides.

### Changed

- Requires Foundry V14, build 14.367 or later, and D&D 5e 6.0.6 or later. Foundry verification remains 14.369; optional MIDI-QOL and DAE integrations require D&D 5e 6-compatible versions.
- Templates, recipes, and saved presets share one picker with a **Manage** button. Activity and Effect tabs retain separate drafts and the selected target Item.
- **Parse** is now **Preview**, and **Dry Run** is now **Check import**. Auto-parse and **Compare after import** are grouped under **Import options**; healthy version details stay collapsed.
- Import History is optional and hidden by default under **View options**. Hiding it retains session entries and Undo; enabling it opens the panel after an import.
- Strict activity and effect import/export uses YAML schema 4, preserving supported MIDI-QOL and DAE settings, including inactive options, conditions, macros, overtime, region rules, lighting, stacking, and expiry. Parsing, exporting, and previewing do not execute macros or conditions.
- Older YAML remains readable. Full-Item workflows require the optional Item Importer 14.2.0 or later.
- Updated previews, comparisons, field catalogs, and bundled references for D&D 5e 6 and supported automation settings. Recipe and window features are documented within the main README sections.
- Updated package information and the Foundry package-page link; public changelog and media links use Bakshi's Bazaar Archives.

### Fixed

- Imports preserve activity descriptions, damage modifiers, Check and Save bonuses and visibility, effect expiry, area behaviors, and Transform settings.
- Effects preserve conditions, typed change values, origin details, magical status, riders, and replacement context.
- Linked activities are checked before import; missing, ambiguous, and circular references are reported. Editing the source or a reference clears old check results.
- Effects can link to existing embedded effects or external effect UUIDs. External references remain external, and Undo removes only documents created by the import.
- Unsupported fields produce an error instead of being silently dropped.
