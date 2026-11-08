# 5e Item Importer changelog

**Unreleased** lists changes that haven't shipped yet.

## Unreleased

### Added

- Feature Items. Class features, monster traits and feats import from strict YAML (`FEAT`), lightweight YAML (`type: feat`) and pasted text, with feature type and subtype, limited uses and recovery, prerequisites, and activities and effects through the optional Activity Importer. A **Strict Feat Template** and the `core-feat` and `full-midi-feat` examples are bundled.
- Feature advancement. A feat's `ADVANCEMENT` list builds dnd5e Ability Score Improvement, Grant Items, Choose Items, Modify Items, Scale Value and Trait advancements, so an imported feat applies its bonuses, spells, items and proficiencies when dropped on a character. Exports include advancement, and the `full-native-feat` example shows every entry type.
- Container contents. A `CONTAINER` can nest its contents under `CONTENTS` (bags inside bags, up to dnd5e's depth limit), and any physical item can name its container with `INVENTORY.Container` (a container in the same batch, or an existing one by name or UUID). Children are placed inside the container in the sidebar and on actors; the `full-native-container` example shows a nested pack.
- Document references by name. Granted items, choice pools, recommended items and containers resolve automatically when one world or compendium document matches the name or identifier. Anything unresolved or ambiguous gets a drop zone with a candidate list; drag a document onto it to choose. Import stays disabled until every reference is resolved, and `api.import()` reports the missing ones instead of creating a partial item.
- Combat recovery periods (`initiative`, `turnStart`, `turnEnd`, `turn`) for limited uses, alongside the rest periods.

### Changed

- Item YAML schema is now 6. Older YAML still imports unchanged; exports write the new version.
- Full-Item attachments (activities and effects) now pair with Activity Importer 14.3.0 or later, so update both importers together. Items import without the companion as before; older companion versions are told what to update.
- MIDI-QOL and DAE settings now follow MIDI-QOL 14.6.8 and DAE 14.6.6.
- Exporting a world or actor container as Full or Core Item YAML nests its contents under `CONTENTS`. A compendium container exports without contents, and exporting its items as a batch keeps their `INVENTORY.Container` references.
- Templates, examples, the field catalog and the import-window guide cover the new sections. The template picker offers a **Feature (Core Fields)** quick start.

### Fixed

- Exporting a complete Item with activities failed while MIDI-QOL 14.6.8 was active.

## [14.2.1] - 2026-11-01

### Changed

- Lowered the Foundry VTT minimum to 14.367. Verified remains 14.369, the supported generation remains V14, and D&D 5e 6.0.6 is still required.
- Integrated recipe and import-window features into the existing README sections and removed the release callouts.
- The field catalog guide now shows how to query the catalog from the browser console, with a copy-ready example.
- Shorter, clearer hints on the two troubleshooting settings.

### Removed

- Removed Ko-fi support links from the Markdown README and generated HTML listing.

## [14.2.0] - 2026-10-18

### Added

- Editable Item recipes for multiple rarities and dawn/dusk charge recovery, plus a MIDI-QOL Item recipe when MIDI-QOL is active.
- Complete-Item recipes with activities and effects when the optional premium Activity Importer 14.2.0 is available.
- YAML schema 4 support for reviewed MIDI-QOL Item flags and DAE Item macros. Full-Item export also preserves supported companion automation settings.
- An optional generated YAML editor beside the source text. **Use and preview** applies edits; copy and download export the current preview even when the editor is hidden.
- **Import options > Restore defaults** resets import and view settings while preserving source text, unapplied YAML edits, destination, duplicate handling, presets and history.
- A bundled import-window guide and updated in-window instructions, templates, examples and field references.

### Changed

- Requires Foundry VTT 14.369 or later within V14, and D&D 5e 6.0.6 or later. Optional MIDI-QOL and DAE integrations require versions compatible with D&D 5e 6.
- Full mechanical previews are the default, with an optional Compact view and remembered panel states. Parser findings appear under Review; technical Parser details start collapsed.
- Templates, recipes and saved presets share one picker with a Manage button. **Parse** is now **Preview**.
- Import options are grouped into parsing, comparison, images and automation. Image controls appear only when applicable; destination and duplicate controls remain visible.
- Import History is optional and hidden by default. Enable it under View options for session entries, Undo and report downloads. Hiding it retains its records.
- The companion attachment builder appears only for a valid single-Item preview. Attachment imports check companion support and linked references before writing.
- Older YAML and the legacy single-rarity format remain readable. The public API remains at schema version 3; supported Item types are unchanged.
- Public changelog and media links now use the renamed Bakshi's Bazaar Archives repository.

### Fixed

- Multiple rarities survive import, updates, export, previews and comparisons.
- Preserves damage modifiers, spell expiry settings and the 1.5x tool proficiency multiplier, including supported empty, false and zero values.
- Comparisons no longer report untouched D&D 5e 6 damage defaults as extra versatile damage. Previews and comparisons show supported automation settings.
- Explicitly empty automation mappings clear previous flags during replacement updates; omitted flags and merge updates retain existing values.
- Export failures are reported instead of silently substituting output, and an older parse can no longer overwrite a newer preview.
- Warnings, missing references and batch controls remain visible in both preview modes.
