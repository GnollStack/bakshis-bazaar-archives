# 5e Activity Importer changelog

**Unreleased** lists changes that haven't shipped yet.

## Unreleased

## [14.4.0] - 2026-11-16

### Added

- D&D 5e 6.1 support. The importer is verified on Foundry 14.370 with dnd5e 6.0.6 and 6.1.0.
- Enchantment riders on the effect (dnd5e 6.1). An enchantment effect's `EFFECT_SYSTEM` takes `Rider Activities`, `Rider Effects` and `Rider Items`. On dnd5e 6.0, use the Enchant activity's `APPLICATION` riders, which work on both versions.
- Antimagic areas (dnd5e 6.1). `APPLIED_BEHAVIORS` accepts `Type: antimagic`, as Antimagic Field uses.
- Ring areas (`Template Type: ring`), the "until dispelled" durations (`disp` and `dstr`) in every template, and the dnd5e 6.1 statuses `antimagic` and `fleeing`.
- MIDI-QOL 14.6.11 settings in `AUTOMATION`: the **Floor** template origin, the **Remove Effect** area rule, and the rules-text link MIDI-QOL keeps on its sample Items.
- Templates written for LLMs. Every template opens with **At a glance** (when to use it and the smallest block that works), maps rules wording to YAML fields in **From source text to YAML**, and follows its example with **Check before you answer**. The generation guide has a table for picking the template.
- The [Importer LLM Pack](https://github.com/GnollStack/bakshis-bazaar-archives/tree/main/importer-llm-pack/14.4.0): a set of files for Claude Projects, ChatGPT Projects or any other chatbot that teaches it to write complete Items with their activities and effects, with the effect keys of dnd5e 6, an optional MIDI-QOL layer and worked examples from rules text to finished YAML.
- Warnings for unknown keys. A misspelled or misplaced section or field in an `ACTIVITY_*` or `EFFECT` block used to be skipped without a message. **Preview** now lists every key the importer did not read, with its full path, and suggests the expected name when only capitals, spaces or underscores differ (`MIDI_CONDITIONS` for `MIDI CONDITIONS`). The rest of the block still imports. With Item Importer, the same warning appears for the inline activities and effects of a complete Item.
- **Copy problems** in the import window. When the preview or **Check import** reports an error or a warning, one button copies the messages, your setup and the YAML, ready to paste back into the chat that wrote it.
- A link to the Importer LLM Pack for your installed version under the source box.

### Changed

- Strict activity and effect YAML now uses serialization schema 6. Older YAML still imports unchanged; exports write the new version.
- Full-Item workflows need Item Importer 14.4.0 or later, so update both importers together.
- MIDI-QOL and DAE settings now follow MIDI-QOL 14.6.11 and DAE 14.6.9. MIDI-QOL 14.6.11 does not load on dnd5e 6.1, so MIDI-QOL automation needs dnd5e 6.0.6 for now.
- The MIDI templates now write MIDI-QOL and DAE settings in the `AUTOMATION` section, the same form an export uses. Each MIDI template is complete on its own: it holds the whole Base reference for its activity type, the `AUTOMATION` fields with a table for each, and rows and checks that name them. The examples in `docs/examples/midi-*.yaml` use `AUTOMATION` too.
- The older `MIDI CONDITIONS`, `MIDI OVERTIME`, `DAE Details` and related sections still import as before. They moved out of the templates into one page, `docs/midi-legacy-sections.md`, which also lists the `AUTOMATION` path for every older label.
- **MIDI Common Fields Reference** now lists the `AUTOMATION` settings every activity type shares.
- A status registered by the running system or by another module no longer shows an "unknown status" warning.
- `Change Type` accepts the core types in any letter case, so `Add` is read as `add`.
- Corrected template tables that still showed older field names or wrong values.
- `Active Effect Keys V2.txt` and the Effect templates now list the effect keys of dnd5e 6, each checked against dnd5e's own actor data, with a table of the older keys and what to write instead. The MIDI-QOL, DAE and macro keys moved to the MIDI Effect template and were checked against MIDI-QOL 14.6.11; keys it no longer registers were removed.
- The "effect-based transformation" recipe uses the dnd5e 6 key for walking speed.
- YAML with MIDI-QOL settings now imports where MIDI-QOL is not active, instead of being refused. The activities are created without those settings, and one notice in the preview lists what is left out and how to add it later. On dnd5e 6.1, where MIDI-QOL 14.6.11 cannot run, the notice says so instead of asking you to enable it.
- Clearer template guidance: effects that last until the end of your next turn (`Effect Expiry: sourceEnd`), MIDI-QOL's other activity running only on the targets an attack hit, and `Modifiers` being the one damage field you can leave out.
- A shorter README written for GMs, with a section on letting a chatbot fill in the templates. The field-by-field reference stays in the bundled template guide and examples.

### Fixed

- A Save or Check with a fixed DC (`DC Calculation: custom`) rolled against the user's own ability DC instead of the number in `DC Formula`. Import affected activities again, or set **DC Calculation** to **Custom Formula** on the activity sheet.
- A Cast activity with `Casting Ability: spellcasting` stored that word instead of dnd5e's default, so the cast spell could use the wrong ability.
- Exporting an enchantment effect failed on dnd5e 6.1.
- Exporting an effect failed when dnd5e's own data migration had left an empty rider flag or a folder reference on it.
- A MIDI-QOL Save lost its critical damage settings unless the activity also had a `MIDI SAVE FIELDS` section.
- The field catalog reported itself out of date on any build other than Foundry 14.369 with dnd5e 6.0.6.
- A list entry that holds a colon, such as `- class:wizard` under `Retained Spell Lists`, was read as a mapping and stored as `[object Object]`. The same happened to such a value inside a complete Item and in an export that was imported again.
- An exported Heal without an amount, Damage activity without a part, or Summon or Transform without a profile could not be imported again. dnd5e's own Spare the Dying, Regeneration and Iron Flask are stored that way. They now import as stored, with a warning. Leaving the section out is still an error.
- An effect change row with an empty key, as dnd5e's own Boots of Elvenkind has, was dropped on import.
- A Cast activity stored its fixed `Save DC` and `Attack Bonus` as numbers where dnd5e keeps text, so importing showed "Schema cleaning would coerce" warnings for them.
- **Choose by UUID** and saving a preset did nothing in Foundry's desktop app, which does not support the browser's text prompt. Both now ask in a Foundry dialog.
- An activity `Description` or `Chat Description` written as a YAML block (`|`) showed a "Schema cleaning would coerce" warning, because the block's last line break is removed when dnd5e saves it. Both are now stored trimmed, as dnd5e keeps them.

## [14.3.0] - 2026-11-07

### Added

- Teleport activities. `ACTIVITY_TELEPORT` builds the D&D 5e 6 teleport activity with its own distance and units or the activity's range, so the chat card offers the Teleport button. Base and MIDI-QOL **Teleport** templates and the `base-teleport` and `midi-teleport` examples are bundled.
- Condition effects. A standalone effect with `Effect Type: condition` creates a dnd5e condition effect (poisoned, frightened, exhaustion and the rest) with its condition type and, for leveled conditions, its level. Status conditions default to the chosen condition. The `base-effect-condition` example shows a transferring poison effect.
- Every strict template includes the new fields, and the MIDI quick-start list offers the MIDI Teleport template.
- MIDI-QOL 14.6.8 settings in `AUTOMATION`: follow-up buttons (`followUp`, `followUpTargets`, `followUpCondition`), **Targets you can see**, **Statuses Applied**, regions that move each turn, ammunition per target for attacks, and the Midi Teleport settings of Teleport activities (who teleports, when, blocked destinations and whether the destination must be seen). They import, export, preview and compare like the other MIDI-QOL settings.

### Changed

- Strict activity and effect YAML now uses serialization schema 5 (twelve activity types and the base, enchantment and condition effect types). Older YAML still imports unchanged; exports write the new version.
- Full-Item workflows need Item Importer 14.3.0 or later, so update both importers together. Activities and effects import onto Items without the companion as before.
- MIDI-QOL and DAE settings now follow MIDI-QOL 14.6.8 and DAE 14.6.6.
- Applied effects on activities stay base effects, as dnd5e defines them; a condition effect is created on the Item itself and reaches the actor through **Apply Effect to Actor**. Bastion Order activities are not supported.

### Fixed

- Exporting an activity or a complete Item failed while MIDI-QOL 14.6.8 was active.

## [14.2.1] - 2026-11-01

### Added

- Added Patreon and permanent Discord invite badges to the README and Foundry listing.

### Changed

- The field catalog guide now shows how to query the catalog from the browser console, with a copy-ready example.
- Shorter, clearer hints on the two troubleshooting settings.

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
