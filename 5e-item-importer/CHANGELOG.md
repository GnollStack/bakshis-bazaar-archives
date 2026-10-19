# 5e Item Importer changelog

**Unreleased** lists changes that haven't shipped yet.

## Unreleased

### Changed

- Lowered the Foundry VTT minimum to 14.367. Verified remains 14.369, the supported generation remains V14, and D&D 5e 6.0.6 is still required.
- Integrated recipe and import-window features into the existing README sections and removed the release callouts.

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
