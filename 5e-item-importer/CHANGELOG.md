# 5e Item Importer changelog

**Unreleased** lists changes that haven't shipped yet.

## Unreleased

### Added

- A search window for every reference field: the spells and items a feat grants, choice lists and containers. **Change** or **Search…** opens a small window whose matches update as you type, each with its source. Use the arrow keys and Enter, click a match, or paste a UUID. You can still drop a document on the field. A linked field shows the document, where it came from, and **Change** and **Clear**.
- A setting, **Default compendium for linked spells and creatures**: when a name is in both of dnd5e's compendiums, link the copy for the world's Rules Version (the default), 2024 or 2014, or always ask. 5e Activity Importer has the same setting, and changing one changes the other.
- With 5e Activity Importer 14.5.0, a Cast's spell and a Summon or Transform creature named in a complete Item link themselves in the preview and in imports made through the API.

### Changed

- A world Item with the name now wins over compendium copies, and a 2014/2024 pair from dnd5e's own compendiums no longer asks. A name that also matches another module's compendium still lists the matches to choose from.
- References search only the compendiums and world Items you are allowed to see.
- Clearer messages while a spell, creature or document still needs to be picked.

## [14.4.0] - 2026-11-16

### Added

- D&D 5e 6.1 support. The importer is verified on Foundry 14.370 with dnd5e 6.0.6 and 6.1.0.
- Food properties. A consumable's `FOOD_PROPERTIES` section sets **Hydrating** and **Nutritious** (dnd5e 6.1).
- `PROPERTIES.Unidentified Contents` for containers.
- The `wondrous` consumable type, ring areas for spells, and the `lineage` subtype for species features.
- **Copy problems** in the import window. When the preview reports an error or a warning, one button copies the messages, your setup and the YAML, ready to paste back into the chat that wrote it.
- A link to the Importer LLM Pack for your installed version under **Item source**.
- Templates written for LLMs. Every template opens with **At a glance** (when to use it and the smallest document that works), maps rules wording to YAML fields in **From source text to YAML**, and follows its example with **Check before you answer**. The generation guide has a table for picking the template.
- The [Importer LLM Pack](https://github.com/GnollStack/bakshis-bazaar-archives/tree/main/importer-llm-pack/14.4.0): a set of files for Claude Projects, ChatGPT Projects or any other chatbot that teaches it to write Items for this module, with setup steps, the files to upload for your modules, and worked examples from rules text to finished YAML.

### Changed

- Item YAML schema is now 7. Older YAML still imports unchanged; exports write the new version.
- Full-Item attachments (activities and effects) now pair with Activity Importer 14.4.0 or later, so update both importers together.
- MIDI-QOL and DAE settings now follow MIDI-QOL 14.6.11 and DAE 14.6.9.
- Export keeps every stored item property. One without a field of its own is written under `CUSTOM_PROPERTIES`.
- `Base Weapon: Hand Crossbow` is read as `handcrossbow`, and a base weapon or armor that dnd5e does not know shows a warning.
- The warnings for an unknown spell range unit, duration unit, target type or area shape now say that the value is stored as written.
- Corrected template tables and examples, including mundane examples that used common rarity.
- With Activity Importer 14.4.0, a misspelled or misplaced key inside an inline activity or effect is listed as a warning in the preview. It used to be skipped without a message.
- The `full-midi-weapon` and `full-midi-feat` examples write their MIDI-QOL settings in the `AUTOMATION` section, the form Activity Importer 14.4.0 teaches and exports. They import to the same data as before.
- Parse errors and warnings no longer pop up one by one; the preview lists them, so a batch no longer fills the screen. They are still written to the browser console.
- With Activity Importer 14.4.0, a complete Item whose inline activities hold MIDI-QOL settings imports where MIDI-QOL is not active. The activities are created without those settings, and one notice in **Activities & Effects** lists what is left out.
- A shorter README written for GMs, with a section on letting a chatbot fill in the templates. The template links, YAML rules, quick drafts, description enrichers and how to write several Items in one paste moved into the bundled template guide (`templates/README.md`), and the stat block tips into the Naturalized Item Template.

### Fixed

- Exporting an Item with no limited uses failed with a "Uses Max" error. Most Items made on the sheet or taken from the SRD compendiums were affected.
- Full export failed for an Item whose activity applies an effect from another document, such as a compendium effect.
- Full export of an Item with an enchantment failed on dnd5e 6.1.
- Exporting an Item failed after MIDI-QOL 14.6.9 or later had stored its rules-text link on it.
- A consumable of type `wondrous` could be exported but not imported.
- A scroll's older `verbal` property was left out of the export.
- A Feature Subtype written without a Feature Type gave no warning.
- The `full-midi-weapon` and `full-midi-feat` examples wrote `APPLICATION.Save On Save` where the importer reads `APPLICATION.On Save`.
- A list entry that holds a colon and has no quotes, such as `- class:wizard` or a web address, was read as a mapping. Inside an inline activity it was stored as `[object Object]`.
- An exported Item could not be imported again when dnd5e stores it without a type, base weapon, subtype, ammunition type, base tool or Armor Class. dnd5e's own Paper, Unarmed Strike, Antitoxin, Helm of Brilliance and about 130 more compendium Items are stored that way. The export now writes such a value as empty, and an empty value imports as stored, with a warning. Leaving the field out is still an error.
- A weapon or tool imported with its own Attack or Check activity kept dnd5e's automatic one as well when the preview showed any warning, such as an unknown key, so the Item ended up with two. Only a failed import keeps the automatic activity now.
- Importing a bag or other container showed a warning that the icon folder `icons/commodities/bags` could not be browsed. The automatic icon search now skips a folder that this Foundry version does not have, and its folder lists for wands, rods, art tools, containers, gems and junk now name folders that Foundry 14 has.

## [14.3.0] - 2026-11-07

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
