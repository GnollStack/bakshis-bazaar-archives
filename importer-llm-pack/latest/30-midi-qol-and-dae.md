# Importer LLM Pack 14.4.0: the MIDI-QOL and DAE layer

Pack 14.4.0 (2026-11-16) for 5e Item Importer 14.4.0 and 5e Activity Importer 14.4.0, on dnd5e 6.0.6 and 6.1.0 with Foundry VTT 14.370. The MIDI-QOL layer matches MIDI-QOL 14.6.11 and DAE 14.6.9 on dnd5e 6.0.6.

This file is an add-on layer for worlds where MIDI-QOL is active (and DAE, for effect settings). It adds an `AUTOMATION` section to blocks that are already complete. Write the Item and its activities and effects from 10-items.md and 20-activities-and-effects.md first, check that they work without MIDI-QOL, and only then add the settings here.

## MIDI-QOL layer: when to add it

- Add `AUTOMATION` only when the user says MIDI-QOL is active in their world. MIDI-QOL gives the fullest automation, but every Item in this pack works without it. Without the user's word, leave `AUTOMATION` out.
- MIDI-QOL 14.6.11 runs on dnd5e 6.0.6, not on dnd5e 6.1.0. A user on dnd5e 6.1.0 cannot have MIDI-QOL active yet, so write no `AUTOMATION` for them.
- Without MIDI-QOL, the import windows import an activity without its MIDI-QOL settings and list what they left out ("MIDI-QOL is not active in this world, so 2 MIDI-QOL settings will be left out: ..."). Flags under `AUTOMATION` are stored either way. If the user wants YAML for that world, answer with the same YAML without its `AUTOMATION` sections.
- `AUTOMATION` is the only form to write. Older YAML can hold sections named `MIDI ...` or `DAE ...`; the importer still reads them, but never write them, and never write them next to `AUTOMATION` in one block.
- Change only what the MIDI-QOL settings need. The Base sections stay as they were.

The `AUTOMATION` section holds the MIDI-QOL settings of this activity. They are stored only where MIDI-QOL 14.6.11 is active, and MIDI-QOL 14.6.11 runs on D&D5e 6.0, not 6.1; elsewhere the import windows import the block without them and list what they left out. Without the `AUTOMATION` section the same block is a complete Base activity that needs neither module. DAE and Convenient Effects are separate optional integrations.

## AUTOMATION: where it goes

`AUTOMATION` is one more section of the activity block, at the same indentation as `ACTIVATION` or `TARGETS`. Its MIDI-QOL settings sit under `AUTOMATION.midi-qol.fields`. In an `EFFECT` (also an `effects` or `APPLIED_EFFECTS` entry) the section holds `dae` and `core` instead; see "EFFECT with DAE". Each type section below lists the rows that type adds and, where it has them, the settings only that type has. Every example in this file is the Base example of 20-activities-and-effects.md with `AUTOMATION` added.

## AUTOMATION: common settings for every activity

These settings exist on every `ACTIVITY_*` block. A placeholder such as `"[true|false]"` lists the choices; it is not a value to copy. Write only the settings the source calls for.

```yaml
ACTIVITY_SAVE: # or any other ACTIVITY_* root key
  # MIDI-QOL settings. Leave AUTOMATION out for a Base activity, and write only the settings the source needs.
  # Values are stored exactly as written: true, false and numbers without quotes; "" is an empty text.
  AUTOMATION:
    midi-qol:
      fields:
        useConditionText: "[text]" # Condition that must be true to use the activity.
        useConditionReason: "[text]" # Message shown when that condition is false.
        effectConditionText: "[text]" # Condition that must be true for the applied effects.
        otherActivityId: "[none|activity id|activity identifier]" # "" lets MIDI-QOL choose.
        macroData:
          name: "[text]"
          command: |
            [multiline text content]
        # Whole damage-trait categories the activity's damage ignores.
        ignoreTraits:
          idi: "[true|false]"
          idr: "[true|false]"
          idv: "[true|false]"
          ida: "[true|false]"
          idm: "[true|false]"
        midiProperties:
          identifier: "[text]" # The name other activities use to refer to this one.
          displayActivityName: "[true|false]"
          automationOnly: "[true|false]"
          rollMode: "[default|public|gm|blind|self]"
          removeChatButtons: "[default|off|attack|damage|all|everything]"
          autoConsume: "[true|false]"
          forceConsumeDialog: "[default|always|never]"
          forceRollDialog: "[default|always|never]"
          forceDamageDialog: "[default|always|never]"
          confirmTargets: "[default|always|never]"
          targetMustBeSeen: "[true|false]"
          # (Used when the activity has an area in TARGETS)
          autoTargetType: "[any|ally|notAlly|enemy|notEnemy|neutral|notNeutral|hostile|notHostile|Friendly|notFriendly]"
          # (Used when the activity has an area in TARGETS)
          autoTargetAction: "[default|none|always|alwaysIgnoreIncapacitated|alwaysIgnoreDefeated|wallsBlock|wallsBlockIgnoreIncapacitated|wallsBlockIgnoreDefeated|walledtemplates]"
          ignoreFullCover: "[true|false]"
          ignoreTraits: [] # Single damage types to ignore, such as idr.fire or idi.poison.
          magicDamage: "[true|false]"
          magicEffect: "[true|false]"
          noConcentrationCheck: "[true|false]"
          skipConcentrationCheck: "[true|false]"
          chooseEffects: "[true|false]"
          toggleEffect: "[true|false]"
          autoCEEffects: "[default|none|itempri|cepri|both]"
          otherActivityCompatible: "[true|false]"
          triggeredActivityId: "[none|activity id|activity identifier|activity UUID]"
          # (Used when triggeredActivityId is not none; stored either way)
          triggeredActivityConditionText: "[text]"
          # (Used when triggeredActivityId is not none; stored either way)
          triggeredActivityTargets: "[self|targets|hitTargets|missedTargets|failedSaves|saveTargets|retarget]"
          # (Used when triggeredActivityId is not none; stored either way)
          triggeredActivityRollAs: "[self|firstTarget|firstHitTarget|firstMissedTarget|firstSaveTarget|firstFailedSaveTarget]"
          triggeredActivityConsume: "[true|false]"
          triggeredActivityConfigure: "[true|false]"
          followUp: "[true|false]"
          # (Used when followUp is true; stored either way)
          followUpTargets: "[default|self|targets|hitTargets|missedTargets|failedSaves|saveTargets]"
          # (Used when followUp is true; stored either way)
          followUpCondition: "[text]"
          # (Optional, repeatable)
          statuses:
            - id: "[text]" # A status id, such as prone or poisoned.
              specialDuration: [] # Special durations that end the status early.
              onSave: "[true|false]"
        isOverTimeFlag: "[true|false]"
        # (Used when isOverTimeFlag is true; stored either way)
        overTimeProperties:
          turnChoice: "[start|end|null]"
          saveRemoves: "[true|false]"
          rollAs: "[target|source]"
          preRemoveConditionText: "[text]"
          postRemoveConditionText: "[text]"
        # (Used when the activity has an area in TARGETS and enabled is true)
        regionBehavior:
          enabled: "[true|false]"
          dispositionFilter: "[-1|0|1]"
          excludeSource: "[true|false]"
          oncePerTurn: "[true|false]"
          wallRestriction: "[none|light|darkness|sight|sound|move]"
          regionVisibility: "[0|1|2|3|4]"
          attachToken: "[default|caster|target]"
          moveDistance: "[number]"
          moveDirection: "[away|choose]"
          moveTiming: "[start|end]"
          templateOrigin: "[default|floor|base|top|eye]"
          # (Optional, repeatable)
          rules:
            - trigger: "[entry|exit|turnStart|turnEnd]"
              action: "[useActivity|applyEffect|removeEffect]"
              targetId: "[activity identifier|effect id]"
        # (Used when regionBehavior.enabled and regionLight.enabled are true)
        regionLight:
          enabled: "[true|false]"
          dim: "[number]"
          bright: "[number]"
          color: "[#rrggbb|null]"
          alpha: "[number]"
          luminosity: "[number]"
          animationType: "[text|null]"
          animationSpeed: "[number]"
          animationIntensity: "[number]"
```

## AUTOMATION: rules

| Setting | Rule |
| --- | --- |
| Availability | MIDI-QOL must be active for `AUTOMATION.midi-qol` settings to be stored and to act. Without it, both import windows import the activity without them and list what they left out; flags are stored either way. DAE and Convenient Effects are separate integrations; neither importer implies them. |
| Exact values | `AUTOMATION` stores what is written. There is no `n/a` and no `Auto`, and a quoted `"true"` is an error. Leave a setting out to keep MIDI-QOL's own default. An unknown name or a wrong type is a parse error that names the path. |
| Triggered activity | `midiProperties.triggeredActivityId` is `none` (off) or the id, identifier or UUID of another activity. `triggeredActivityConditionText` is optional. `triggeredActivityTargets` and `triggeredActivityRollAs` choose the target pool and the roller; they stay stored while the trigger is `none`. |
| Other activity | `otherActivityId` is `""` (MIDI-QOL chooses), `none`, or the id or identifier of an activity on the same Item that has `midiProperties.otherActivityCompatible: true`. `otherActivityAsParentType` exists on Attack, Save, Check and Utility only. |
| References | Link activities by an identifier that the other block also writes in `midiProperties.identifier`. Never invent a 16-character id or a UUID. The import checks that a link exists, is unique and does not loop; it does not prove that the workflow runs. |
| Dialogs and charges | `forceConsumeDialog: always` overrides `autoConsume: true`; `never` consumes without asking; `default` follows the world's workflow settings. `forceRollDialog` and `forceDamageDialog` are separate settings. |
| Area settings | `autoTargetType`, `autoTargetAction`, `regionBehavior` and `regionLight` act only when the activity has an area in `TARGETS`. |
| Over time | `overTimeProperties` stay stored while `isOverTimeFlag` is `false`. `macroData.command` is stored text; the importer never runs it. |
| Effects | `chooseEffects`, `toggleEffect` and `effectConditionText` need applied effects and targets when the activity is used. `autoCEEffects` also needs Convenient Effects. An applied effect takes its DAE settings in its own `AUTOMATION.dae`, as the MIDI Effect template describes. |
| Validation | Reviewed against MIDI-QOL 14.6.11, D&D5e 6.0.6 and Foundry 14.370. A successful import proves storage, not gameplay: conditions, macros and linked activities are not executed. |

## AUTOMATION: check before you answer (every activity)

1. Do not write `Type: antimagic` under `APPLIED_BEHAVIORS` in MIDI YAML. It needs dnd5e 6.1, and MIDI-QOL 14.6.11 runs on dnd5e 6.0 only.
2. Write MIDI-QOL and DAE settings only in the `AUTOMATION` section. The older `MIDI ...`, `DAE ...` and `Midi-QOL` sections must not appear in the same block: mixing them is a parse error.
3. `AUTOMATION` is strict: an unknown name or a wrong value type is a parse error that names the path. Write `true`, `false` and numbers without quotes, and never write `n/a` there. A text value is stored as written, so a misspelled word is not rejected; it simply does nothing.

## AUTOMATION: common setting values

### AUTOMATION common setting values: AUTOMATION: conditions, links and macro

| Field | Description | Accepted Values |
|---|---|---|
| `useConditionText` | Condition expression that must be true to allow use. Uses MIDI-QOL condition syntax. Stored, never evaluated by the importer. | Text. |
| `useConditionReason` | Message shown when the use condition fails. | Text. |
| `effectConditionText` | Condition expression that must be true for the applied effects to activate. | Text. |
| `otherActivityId` | Another activity of the same Item that MIDI-QOL rolls together with this one. It must be a Save, Check, Damage, Heal or Utility activity with `midiProperties.otherActivityCompatible: true`. On an attack, it runs only for the targets the attack hit, so a save or extra damage that follows a hit goes here. | `""` = MIDI-QOL chooses, `none` = off, a local activity id, or a local activity identifier. |
| `macroData.name` | Name of the activity macro. | Text; `""` when unnamed. |
| `macroData.command` | Macro code stored with the activity. MIDI-QOL runs it only when its own configuration calls for it; the importer never runs it. | Multiline text. |
| `ignoreTraits.idi`, `.idr`, `.idv`, `.ida`, `.idm` | The activity's damage ignores a whole category of the target's damage traits: immunity (`idi`), resistance (`idr`), vulnerability (`idv`), absorption (`ida`) or modification (`idm`). | `true` or `false`. |

### AUTOMATION common setting values: AUTOMATION: midiProperties

| Field | Description | Accepted Values |
|---|---|---|
| `midiProperties.identifier` | The name by which other activities, macros and conditions refer to this activity. | Text. |
| `midiProperties.displayActivityName` | Show the activity's name in the chat card header. | `true` or `false`. |
| `midiProperties.automationOnly` | Hide the activity from normal activity selection. It stays available to automation and to `otherActivityId`. | `true` or `false`. |
| `midiProperties.rollMode` | Visibility of the roll results. | `default`, `public`, `gm`, `blind`, `self`. The older `publicroll`, `gmroll`, `blindroll` and `selfroll` remain valid stored values. |
| `midiProperties.removeChatButtons` | Remove interactive buttons from the chat card. | `default`, `off`, `attack`, `damage`, `all`, `everything`. |
| `midiProperties.autoConsume` | Request automatic resource consumption, subject to `forceConsumeDialog`. | `true` or `false`. |
| `midiProperties.forceConsumeDialog` | `always` shows the consumption dialog even with `autoConsume: true`; `never` consumes without asking; `default` follows the world's workflow settings. | `default`, `always`, `never`. |
| `midiProperties.forceRollDialog` | Override the roll configuration dialog. | `default`, `always`, `never`. |
| `midiProperties.forceDamageDialog` | Override the damage configuration dialog. | `default`, `always`, `never`. |
| `midiProperties.confirmTargets` | Override the target confirmation dialog. Cast and Forward activities always behave as `never`. | `default`, `always`, `never`. |
| `midiProperties.targetMustBeSeen` | The user must be able to see the targets the activity chooses directly. Area templates are not checked. The world's MIDI-QOL setting decides whether an unseen target is refused or only warned about. | `true` or `false`. |
| `midiProperties.autoTargetType` | Which tokens an area template targets automatically. Useful only when the activity has an area target. | `any`, `ally`, `notAlly`, `enemy`, `notEnemy`, `neutral`, `notNeutral`, `hostile`, `notHostile`, `Friendly`, `notFriendly`. |
| `midiProperties.autoTargetAction` | Override MIDI-QOL's auto-targeting when a measured template is placed. Useful only when the activity has an area target. `walledtemplates` needs the Walled Templates module. | `default`, `none`, `always`, `alwaysIgnoreIncapacitated`, `alwaysIgnoreDefeated`, `wallsBlock`, `wallsBlockIgnoreIncapacitated`, `wallsBlockIgnoreDefeated`, `walledtemplates`. |
| `midiProperties.ignoreFullCover` | Attacks and effects ignore full cover. | `true` or `false`. |
| `midiProperties.ignoreTraits` | Single damage types whose trait the damage ignores, written as `category.damageType`. | A list such as `[idr.fire, idi.poison]`; `[]` for none. |
| `midiProperties.magicDamage` | Treat all damage from this activity as magical. | `true` or `false`. |
| `midiProperties.magicEffect` | Treat a save or check that does not come from a spell as magical, for resistance to magic. | `true` or `false`. |
| `midiProperties.noConcentrationCheck` | Damage from this activity does not trigger concentration saves on the target. | `true` or `false`. |
| `midiProperties.skipConcentrationCheck` | Hit points consumed by this activity do not trigger a concentration save on the user. Useful only for activities that consume hit points. | `true` or `false`. |
| `midiProperties.chooseEffects` | The user picks which applied effects to apply from a dialog. | `true` or `false`. |
| `midiProperties.toggleEffect` | Using the activity toggles its effect on and off instead of always applying it. | `true` or `false`. |
| `midiProperties.autoCEEffects` | Override how Convenient Effects are applied. `none` turns them off, `itempri` prefers the Item's effects, `cepri` prefers Convenient Effects, `both` applies both. Needs the Convenient Effects module. | `default`, `none`, `itempri`, `cepri`, `both`. |
| `midiProperties.otherActivityCompatible` | Another activity may select this one in its `otherActivityId`. Only Save, Check, Damage, Heal and Utility activities can be selected. | `true` or `false`. MIDI-QOL's default is `true`. |
| `midiProperties.triggeredActivityId` | Activity to trigger as a follow-up. | `none` = off, an activity id, an activity identifier, or an activity UUID. |
| `midiProperties.triggeredActivityConditionText` | Extra condition for the trigger. Leave it out when no extra condition is intended. | Text. |
| `midiProperties.triggeredActivityTargets` | Who the triggered activity targets. | `self`, `targets`, `hitTargets`, `missedTargets`, `failedSaves`, `saveTargets`, `retarget`. MIDI-QOL's default is `targets`. |
| `midiProperties.triggeredActivityRollAs` | Who rolls the triggered activity. | `self`, `firstTarget`, `firstHitTarget`, `firstMissedTarget`, `firstSaveTarget`, `firstFailedSaveTarget`. MIDI-QOL's default is `self`. |
| `midiProperties.triggeredActivityConsume` | The triggered activity consumes its own configured resources. | `true` or `false`. MIDI-QOL's default is `true`. |
| `midiProperties.triggeredActivityConfigure` | MIDI-QOL may show the triggered activity's configuration dialog. | `true` or `false`. MIDI-QOL's default is `true`. |
| `midiProperties.followUp` | Offer this activity as a button on the chat card when another activity of the same Item is used. The button is shown to the Item's owners and the GM. | `true` or `false`. |
| `midiProperties.followUpTargets` | The targets the follow-up runs at. `default` is the targets an attack hit, otherwise all targets. | `default`, `self`, `targets`, `hitTargets`, `missedTargets`, `failedSaves`, `saveTargets`. |
| `midiProperties.followUpCondition` | Condition for offering the button, checked as the use finishes against the first follow-up target. Empty always offers it. | Text. |
| `midiProperties.statuses[].id` | A status toggled on the targets together with the activity's effects: on a failed save, or on a hit for an attack. | A status id such as `prone` or `poisoned`. |
| `midiProperties.statuses[].specialDuration` | Special durations that end the status early. They need DAE. | A list of DAE special durations; `[]` for none. |
| `midiProperties.statuses[].onSave` | Apply the status whatever the result of the save. | `true` or `false`. |

### AUTOMATION common setting values: AUTOMATION: over time

| Field | Description | Accepted Values |
|---|---|---|
| `isOverTimeFlag` | The activity is used repeatedly over time, for ongoing damage or repeated saves. It needs an effect and a combat to run in. | `true` or `false`. |
| `overTimeProperties.turnChoice` | When in the turn the activity runs. | `start`, `end`, or `null`. |
| `overTimeProperties.rollAs` | Who rolls the over-time activity. | `target`, `source`. MIDI-QOL's default is `target`. |
| `overTimeProperties.saveRemoves` | A successful save removes the over-time effect. | `true` or `false`. MIDI-QOL's default is `true`. |
| `overTimeProperties.preRemoveConditionText` | Condition checked before the activity runs; when it is true the effect is removed. | Text. |
| `overTimeProperties.postRemoveConditionText` | Condition checked after the activity runs; when it is true the effect is removed. | Text. |

### AUTOMATION common setting values: AUTOMATION: area region and light

These settings act only when the activity has an area in `TARGETS`.

| Field | Description | Accepted Values |
|---|---|---|
| `regionBehavior.enabled` | Turn on MIDI-QOL's region behaviour for the placed area. The settings and rules below apply only while it is `true`. | `true` or `false`. |
| `regionBehavior.dispositionFilter` | Which tokens the region affects, relative to the caster. | `-1` = enemies, `0` = all, `1` = allies. MIDI-QOL's default is `-1`. |
| `regionBehavior.excludeSource` | The region does not affect the caster's own token. | `true` or `false`. MIDI-QOL's default is `true`. |
| `regionBehavior.oncePerTurn` | Each activity is triggered at most once per turn for each creature. | `true` or `false`. MIDI-QOL's default is `true`. |
| `regionBehavior.wallRestriction` | The kind of wall that blocks the region's edge. | `none`, `light`, `darkness`, `sight`, `sound`, `move`. |
| `regionBehavior.regionVisibility` | Who sees the region, as a Foundry region visibility number. | `0` = on the region layer only, `4` = on the region layer while unlocked, `1` = always for the GM, `3` = always for observers, `2` = always for everyone (MIDI-QOL's default). |
| `regionBehavior.attachToken` | Attach the region to a token so that it moves with that token. | `default` = stays in place, `caster`, `target`. |
| `regionBehavior.moveDistance` | Distance the region moves each turn in combat. `0` leaves it in place. | A number, 0 or more. |
| `regionBehavior.moveDirection` | Move away from the caster, or in a direction the caster's owner chooses. | `away`, `choose`. |
| `regionBehavior.moveTiming` | Move at the start or at the end of the caster's turn. | `start`, `end`. |
| `regionBehavior.templateOrigin` | The height a placed template is measured from. `default` follows the world's rule, `floor` is the floor of the level, and `base`, `top` and `eye` are the caster's feet, head and eyes. | `default`, `floor`, `base`, `top`, `eye`. |
| `regionBehavior.rules[].trigger` | When the rule acts on a token. | `entry`, `exit`, `turnStart`, `turnEnd`. |
| `regionBehavior.rules[].action` | What the rule does. | `useActivity`, `applyEffect`, `removeEffect`. |
| `regionBehavior.rules[].targetId` | For `useActivity`, the identifier of an activity on the Item. For `applyEffect` and `removeEffect`, the id of an effect on the Item. The import checks that the link exists. | Text. |
| `regionLight.enabled` | The region gives off light. Needs `regionBehavior.enabled: true`. MIDI-QOL stores these values; its region editor does not show them. | `true` or `false`. |
| `regionLight.dim`, `regionLight.bright` | Radius of dim and of bright light. | A number, 0 or more. |
| `regionLight.color` | Colour of the light. | A colour such as `#80bfff`, or `null`. |
| `regionLight.alpha`, `regionLight.luminosity` | Colour intensity and luminosity. | A number from 0 to 1. |
| `regionLight.animationType` | Light animation. | A Foundry light animation id, `""`, or `null`. |
| `regionLight.animationSpeed` | Speed of the animation. | A number from 0 to 10. |
| `regionLight.animationIntensity` | Intensity of the animation. | A number from 1 to 10. |

## MIDI-QOL: change keys in effects

With MIDI-QOL active, an effect's `CHANGES` can also use `flags.midi-qol.*` keys. Use the ones this file names, and prefer a dnd5e key or a `dnd5e.advantage` change from 40-effect-keys.md when it does the same job, because it also works without MIDI-QOL.

| The source says | Change |
| --- | --- |
| "attack rolls against the target have advantage" | `Attribute Key: flags.midi-qol.grants.advantage.attack.all`; `Change Type: override`; `Value: 1` |
| the target repeats a saving throw at the end of each of its turns (with an over-time Save activity) | `Attribute Key: flags.midi-qol.ActivityOverTime`; `Change Type: override`; `Value:` the `midiProperties.identifier` of that Save activity |

## ACTIVITY_ATTACK with MIDI-QOL: when to add it

- **Root key:** `ACTIVITY_ATTACK`
- **When to use:** Use the MIDI Attack template when MIDI-QOL is active and the attack needs something the base fields cannot store: a second activity that runs on a hit, a separate attack roll for each target, a different fumble range, damage that counts as magical or damage that ignores resistance. If the source needs none of that, use the base Attack template.
- **Smallest working block:** MIDI additions: The base smallest block, plus `AUTOMATION.midi-qol.fields.midiProperties.identifier` (a short unique name that other activities use to refer to this one). Add further `AUTOMATION.midi-qol.fields` settings only when the source needs them.

### ACTIVITY_ATTACK with MIDI-QOL: from source text to YAML

The rows add MIDI-QOL settings to a complete Base `ACTIVITY_ATTACK` block. Paths start at the root key, so `AUTOMATION.midi-qol.fields.midiProperties.identifier` is the `identifier` key in `midiProperties` under `fields` in `midi-qol` in the `AUTOMATION` section of the block.

| The source says | Write |
| --- | --- |
| "on a hit, the target must also make a DC 13 Constitution saving throw" (the save is a second activity on the same Item) | on the attack: `AUTOMATION.midi-qol.fields.otherActivityId: venom-save`; on the Save activity: `AUTOMATION.midi-qol.fields.midiProperties.identifier: venom-save`; `AUTOMATION.midi-qol.fields.midiProperties.otherActivityCompatible: true`; `AUTOMATION.midi-qol.fields.midiProperties.automationOnly: true` |
| "a creature hit by this attack is then also affected by ..." (a second activity that must run only for the targets that were hit) | on the attack: `AUTOMATION.midi-qol.fields.midiProperties.triggeredActivityId: shove-follow-up`; `AUTOMATION.midi-qol.fields.midiProperties.triggeredActivityTargets: hitTargets`; on the second activity: `AUTOMATION.midi-qol.fields.midiProperties.identifier: shove-follow-up`; `AUTOMATION.midi-qol.fields.midiProperties.automationOnly: true` |
| "make a separate attack roll against each target" | `AUTOMATION.midi-qol.fields.attackRollPerTarget: always` |
| "the attack misses automatically on a roll of 1 or 2" | `AUTOMATION.midi-qol.fields.fumbleThreshold: 2` |
| "this attack's damage counts as magical" | `AUTOMATION.midi-qol.fields.midiProperties.magicDamage: true` |
| "this attack ignores resistance to slashing damage" | `AUTOMATION.midi-qol.fields.midiProperties.ignoreTraits: [idr.slashing]` |
| "one creature you can see within range" (MIDI must enforce the sight rule) | `AUTOMATION.midi-qol.fields.midiProperties.targetMustBeSeen: true` |
| "on a hit, the target is knocked prone" (a status applied by MIDI on a hit, with no effect written) | `AUTOMATION.midi-qol.fields.midiProperties.statuses: [{id: prone, specialDuration: [], onSave: false}]` |

### ACTIVITY_ATTACK with MIDI-QOL: settings only this type has

```yaml
ACTIVITY_ATTACK:
  AUTOMATION:
    midi-qol:
      fields:
        # Every setting of "AUTOMATION: common settings for every activity" may be written here too.
        # Attack only
        otherActivityAsParentType: "[true|false]"
        attackMode: "[text]" # An attack mode of the parent weapon, such as oneHanded or twoHanded.
        ammunition: "[text]" # Id of an ammunition Item the actor owns.
        attackRollPerTarget: "[default|always|never]"
        ammunitionPerTarget: "[default|use|target]"
        fumbleThreshold: "[number]"
```

#### ACTIVITY_ATTACK with MIDI-QOL values: AUTOMATION: Attack only

| Field | Description | Accepted Values |
|---|---|---|
| `otherActivityAsParentType` | The activity named in `otherActivityId` is treated as having this activity's action type. | `true` or `false`. |
| `attackMode` | Attack mode to use. The modes on offer depend on the parent weapon. | Text such as `oneHanded` or `twoHanded`. |
| `ammunition` | The ammunition Item to use. Applies when the parent weapon uses ammunition. | The id of an Item in the actor's inventory. |
| `attackRollPerTarget` | Whether to roll the attack separately for each target. | `default` = MIDI-QOL's setting, `always` = one roll per target, `never` = one roll for all. |
| `ammunitionPerTarget` | Ammunition spent when an attack has several targets. | `default` = one per attack roll, `use` = one for the whole use, `target` = one for each target. |
| `fumbleThreshold` | The highest d20 result that counts as a fumble. MIDI-QOL's default is 1. | A number. |

### ACTIVITY_ATTACK with MIDI-QOL: example

The Base example of `ACTIVITY_ATTACK` with the MIDI-QOL settings added in `AUTOMATION`. They are stored settings: importing the block does not run a MIDI-QOL workflow.

```yaml
ACTIVITY_ATTACK:
  ACTIVITY:
    Name: Ember Strike
  ACTIVATION:
    Override Activation: true
    Activation Type: action
  DURATION:
    Override Duration: true
    Duration Time: inst
    Concentration: false
  CONSUMPTION: []
  RANGE:
    Override Range: true
    Range Units: ft
    Range Value: 5
  TARGETS:
    Override Target: true
    Target Type: creature
    Target Amount: 1
    Choose Targets: true
  ATTACK_DETAILS:
    Attack Ability: str
    Flat To Hit: false
    Critical Threshold: 20
  ATTACK_DAMAGE:
    Include Base Damage: true
    DAMAGE_PARTS:
      - DAMAGE_DATA:
          Dice Count: 1
          Die Denomination: 6
          Bonus: ""
          Damage Types:
            - fire
          Custom Enabled: false
          Custom Formula: ""
          Scaling Mode: none
          Scaling Dice Count: 0
          Scaling Formula: ""
  AUTOMATION:
    midi-qol:
      fields:
        otherActivityId: none
        midiProperties:
          identifier: example-attack
          displayActivityName: true
          triggeredActivityId: none
          autoCEEffects: none
          confirmTargets: always
```

### ACTIVITY_ATTACK with MIDI-QOL: check before you answer

1. The value of `otherActivityId` or `midiProperties.triggeredActivityId` must be the `midiProperties.identifier` of another activity in the same output. Do not make up ids or UUIDs. Write `none` when there is no link.
2. `otherActivityId` rolls one extra save or damage activity together with the attack. `midiProperties.triggeredActivityId` runs a whole second activity afterwards for the targets named in `midiProperties.triggeredActivityTargets`; with `hitTargets` it runs only when something was hit.
3. Write `triggeredActivityId: none` to switch a trigger off; MIDI-QOL rejects an empty value there. An empty `otherActivityId` (`""`) is valid and lets MIDI-QOL choose the other activity.
4. `attackMode`, `fumbleThreshold` and `attackRollPerTarget` are written directly under `AUTOMATION.midi-qol.fields`, not under `midiProperties`. Leave `attackMode` out unless the user gives a value.
5. `otherActivityAsParentType` only has meaning together with `otherActivityId`: it makes the other activity's damage count as this attack's type. Leave it out unless the source needs that.

## ACTIVITY_SAVE with MIDI-QOL: when to add it

- **Root key:** `ACTIVITY_SAVE`
- **When to use:** Use the MIDI Save template when MIDI-QOL is active and the save needs something the base fields cannot store: allies that pass or fail without rolling, a save that counts as a save against magic, a save repeated each turn, critical damage on a save that follows an attack, or a link from an attack on the same Item. If the source needs none of that, use the base Save template.
- **Smallest working block:** MIDI additions: The base smallest block, plus `AUTOMATION.midi-qol.fields.friendlySave` and `AUTOMATION.midi-qol.fields.midiProperties.magicEffect` when the source says who is spared or whether the effect is magical. Add `AUTOMATION.midi-qol.fields.midiProperties.identifier` when another activity refers to this save.

### ACTIVITY_SAVE with MIDI-QOL: from source text to YAML

The rows add MIDI-QOL settings to a complete Base `ACTIVITY_SAVE` block. Paths start at the root key, so `AUTOMATION.midi-qol.fields.midiProperties.identifier` is the `identifier` key in `midiProperties` under `fields` in `midi-qol` in the `AUTOMATION` section of the block.

| The source says | Write |
| --- | --- |
| "creatures of your choice automatically succeed" (the user's allies are spared) | `AUTOMATION.midi-qol.fields.friendlySave: friendlySuccess` |
| "a friendly creature can choose to fail this saving throw" (the user's allies fail without rolling) | `AUTOMATION.midi-qol.fields.friendlySave: friendlyFail` |
| "a spell or other magical effect" (traits such as Magic Resistance apply) | `AUTOMATION.midi-qol.fields.midiProperties.magicEffect: true`; for a mundane source such as poison write `AUTOMATION.midi-qol.fields.midiProperties.magicEffect: false` |
| "at the end of each of its turns the target repeats the saving throw, ending the effect on a success" | `AUTOMATION.midi-qol.fields.isOverTimeFlag: true`; `AUTOMATION.midi-qol.fields.overTimeProperties.turnChoice: end`; `AUTOMATION.midi-qol.fields.overTimeProperties.saveRemoves: true`; `AUTOMATION.midi-qol.fields.midiProperties.identifier: hold-repeat` |
| "each enemy in the area must make a saving throw" | `AUTOMATION.midi-qol.fields.midiProperties.autoTargetType: enemy` |
| "on a hit, the target must also make a saving throw" (this Save follows an attack on the same Item) | on this Save: `AUTOMATION.midi-qol.fields.midiProperties.identifier: venom-save`; `AUTOMATION.midi-qol.fields.midiProperties.otherActivityCompatible: true`; `AUTOMATION.midi-qol.fields.midiProperties.automationOnly: true`; on the attack: `AUTOMATION.midi-qol.fields.otherActivityId: venom-save` |
| "the extra damage is doubled when the attack is a critical hit" | `AUTOMATION.midi-qol.fields.damage.critical.allow: true` |
| "one creature you can see within range" (MIDI must enforce the sight rule) | `AUTOMATION.midi-qol.fields.midiProperties.targetMustBeSeen: true` |

### ACTIVITY_SAVE with MIDI-QOL: settings only this type has

```yaml
ACTIVITY_SAVE:
  AUTOMATION:
    midi-qol:
      fields:
        # Every setting of "AUTOMATION: common settings for every activity" may be written here too.
        # Save only
        otherActivityAsParentType: "[true|false]"
        friendlySave: "[default|friendlySuccess|friendlyFail]"
        damage:
          critical:
            allow: "[true|false]"
            bonus: "[formula|null]"
```

#### ACTIVITY_SAVE with MIDI-QOL values: AUTOMATION: Save only

| Field | Description | Accepted Values |
|---|---|---|
| `otherActivityAsParentType` | The activity named in `otherActivityId` is treated as having this activity's action type. | `true` or `false`. |
| `friendlySave` | Let allies of the user succeed or fail without rolling. | `default` = allies roll, `friendlySuccess` = allies succeed, `friendlyFail` = allies fail. |
| `damage.critical.allow` | The save's damage can be critical. | `true` or `false`. |
| `damage.critical.bonus` | Extra damage on a critical. | A formula such as `"2d6"`, or `null`. |

The damage itself stays in the Base `SAVE_DAMAGE` section.

### ACTIVITY_SAVE with MIDI-QOL: example

The Base example of `ACTIVITY_SAVE` with the MIDI-QOL settings added in `AUTOMATION`. They are stored settings: importing the block does not run a MIDI-QOL workflow.

```yaml
ACTIVITY_SAVE:
  ACTIVITY:
    Name: Frost Pulse
  ACTIVATION:
    Override Activation: true
    Activation Type: action
  DURATION:
    Override Duration: true
    Duration Time: inst
    Concentration: false
  CONSUMPTION: []
  RANGE:
    Override Range: true
    Range Units: ft
    Range Value: 30
  TARGETS:
    Override Target: true
    Target Type: creature
    Target Amount: 1
    Choose Targets: true
  SAVE_DETAILS:
    Challenge Ability: con
    DC Calculation: custom
    DC Formula: "13"
  SAVE_DAMAGE:
    Damage on Save: half
    DAMAGE_PARTS:
      - DAMAGE_DATA:
          Dice Count: 2
          Die Denomination: 6
          Bonus: ""
          Damage Types:
            - cold
          Custom Enabled: false
          Custom Formula: ""
          Scaling Mode: none
          Scaling Dice Count: 0
          Scaling Formula: ""
  AUTOMATION:
    midi-qol:
      fields:
        otherActivityId: none
        friendlySave: default
        midiProperties:
          identifier: example-save
          displayActivityName: true
          triggeredActivityId: none
          autoCEEffects: none
          confirmTargets: always
          magicEffect: true
```

### ACTIVITY_SAVE with MIDI-QOL: check before you answer

1. `friendlySave` is written directly under `AUTOMATION.midi-qol.fields`; `magicEffect` is under `midiProperties`. `friendlySave` is `default`, `friendlySuccess` or `friendlyFail`.
2. The critical settings of a Save are `AUTOMATION.midi-qol.fields.damage.critical.allow` and `damage.critical.bonus`. The damage itself stays in the Base `SAVE_DAMAGE` section.
3. A save with `isOverTimeFlag: true` runs each turn only for creatures that carry an effect with a change whose `Attribute Key` is `flags.midi-qol.ActivityOverTime` and whose `Value` is this activity's `midiProperties.identifier`. Put that change in the effect that the Item applies.
4. Write `otherActivityAsParentType` only together with `otherActivityId`. Do not fill `useConditionText` or the over-time remove conditions with made-up expressions; write them only when the user supplies the expression.

## ACTIVITY_DAMAGE with MIDI-QOL: when to add it

- **Root key:** `ACTIVITY_DAMAGE`
- **When to use:** Use the MIDI Damage template when MIDI-QOL is active and the damage needs something the base fields cannot store: damage each turn, damage that counts as magical or ignores resistance, area damage limited to enemies, or a damage activity that another activity on the Item starts. If the source needs none of that, use the base Damage template.
- **Smallest working block:** MIDI additions: The base smallest block, plus `AUTOMATION.midi-qol.fields.midiProperties.identifier` so that effects and other activities can refer to this damage.

### ACTIVITY_DAMAGE with MIDI-QOL: from source text to YAML

The rows add MIDI-QOL settings to a complete Base `ACTIVITY_DAMAGE` block. Paths start at the root key, so `AUTOMATION.midi-qol.fields.midiProperties.identifier` is the `identifier` key in `midiProperties` under `fields` in `midi-qol` in the `AUTOMATION` section of the block.

| The source says | Write |
| --- | --- |
| "takes 1d6 fire damage at the start of each of its turns" | `AUTOMATION.midi-qol.fields.isOverTimeFlag: true`; `AUTOMATION.midi-qol.fields.overTimeProperties.turnChoice: start`; `AUTOMATION.midi-qol.fields.overTimeProperties.rollAs: source`; `AUTOMATION.midi-qol.fields.midiProperties.identifier: burn-tick` |
| "this damage counts as magical" | `AUTOMATION.midi-qol.fields.midiProperties.magicDamage: true` |
| "this damage ignores resistance to fire damage" | `AUTOMATION.midi-qol.fields.midiProperties.ignoreTraits: [idr.fire]` |
| "this damage ignores resistance and immunity" | `AUTOMATION.midi-qol.fields.ignoreTraits.idi: true`; `AUTOMATION.midi-qol.fields.ignoreTraits.idr: true` |
| "each enemy in the area takes the damage" | `AUTOMATION.midi-qol.fields.midiProperties.autoTargetType: enemy` |
| "this damage does not force a concentration check" | `AUTOMATION.midi-qol.fields.midiProperties.noConcentrationCheck: true` |
| "on a hit, the target takes an extra 2d6 poison damage" (written as its own Damage activity on the weapon) | on this Damage activity: `AUTOMATION.midi-qol.fields.midiProperties.identifier: venom-damage`; `AUTOMATION.midi-qol.fields.midiProperties.otherActivityCompatible: true`; `AUTOMATION.midi-qol.fields.midiProperties.automationOnly: true`; on the attack: `AUTOMATION.midi-qol.fields.otherActivityId: venom-damage` |

### ACTIVITY_DAMAGE with MIDI-QOL: example

The Base example of `ACTIVITY_DAMAGE` with the MIDI-QOL settings added in `AUTOMATION`. They are stored settings: importing the block does not run a MIDI-QOL workflow.

```yaml
ACTIVITY_DAMAGE:
  ACTIVITY:
    Name: Cold Snap
  ACTIVATION:
    Override Activation: true
    Activation Type: action
  DURATION:
    Override Duration: true
    Duration Time: inst
    Concentration: false
  CONSUMPTION: []
  RANGE:
    Override Range: true
    Range Units: ft
    Range Value: 30
  TARGETS:
    Override Target: true
    Target Type: creature
    Target Amount: 1
    Choose Targets: true
  DAMAGE_DETAILS:
    Allow Critical: false
  DAMAGE:
    DAMAGE_PARTS:
      - DAMAGE_DATA:
          Dice Count: 2
          Die Denomination: 6
          Bonus: ""
          Damage Types:
            - cold
          Custom Enabled: false
          Custom Formula: ""
          Scaling Mode: none
          Scaling Dice Count: 0
          Scaling Formula: ""
  AUTOMATION:
    midi-qol:
      fields:
        midiProperties:
          identifier: example-damage
          displayActivityName: true
          triggeredActivityId: none
          autoCEEffects: none
          confirmTargets: always
```

### ACTIVITY_DAMAGE with MIDI-QOL: check before you answer

1. A Damage activity with `isOverTimeFlag: true` runs each turn only for creatures that carry an effect with a change whose `Attribute Key` is `flags.midi-qol.ActivityOverTime` and whose `Value` is this activity's `midiProperties.identifier`. Put that change in the effect that the Item applies.
2. `overTimeProperties.rollAs: source` rolls with the statistics of the Item's owner. `target` rolls with the statistics of the creature that carries the effect. `overTimeProperties.turnChoice` is `start` or `end`.
3. To ignore a whole category write `ignoreTraits.idi` (immunity), `ignoreTraits.idr` (resistance), `ignoreTraits.idv` (vulnerability), `ignoreTraits.ida` (absorption) or `ignoreTraits.idm` (modification) as `true`. To ignore it for one damage type add `category.type` to the list `midiProperties.ignoreTraits`, such as `[idr.fire]`.
4. `midiProperties.autoTargetType` only matters when the activity has an area template. Its values are fixed words such as `enemy` and `ally`; `enemies` would be stored and do nothing.
5. Do not fill `useConditionText`, `effectConditionText` or the over-time remove conditions with made-up expressions. Write them only when the user supplies the expression; otherwise keep the restriction in the description.

## ACTIVITY_HEAL with MIDI-QOL: when to add it

- **Root key:** `ACTIVITY_HEAL`
- **When to use:** Use the MIDI Heal template when MIDI-QOL is active and the healing needs something the base fields cannot store: healing each turn, healing that another activity starts or offers, or area healing limited to allies. If the source needs none of that, use the base Heal template.
- **Smallest working block:** MIDI additions: The base smallest block, plus `AUTOMATION.midi-qol.fields.midiProperties.identifier` so that effects and other activities can refer to this healing.

### ACTIVITY_HEAL with MIDI-QOL: from source text to YAML

The rows add MIDI-QOL settings to a complete Base `ACTIVITY_HEAL` block. Paths start at the root key, so `AUTOMATION.midi-qol.fields.midiProperties.identifier` is the `identifier` key in `midiProperties` under `fields` in `midi-qol` in the `AUTOMATION` section of the block.

| The source says | Write |
| --- | --- |
| "regains 1d6 hit points at the start of each of its turns" | `AUTOMATION.midi-qol.fields.isOverTimeFlag: true`; `AUTOMATION.midi-qol.fields.overTimeProperties.turnChoice: start`; `AUTOMATION.midi-qol.fields.overTimeProperties.rollAs: source`; `AUTOMATION.midi-qol.fields.midiProperties.identifier: regen-tick` |
| "each ally in the area regains hit points" | `AUTOMATION.midi-qol.fields.midiProperties.autoTargetType: ally` |
| "whenever you use this item's other ability, you regain 1d6 hit points" (the healing always follows another activity) | on the other activity: `AUTOMATION.midi-qol.fields.midiProperties.triggeredActivityId: drain-heal`; `AUTOMATION.midi-qol.fields.midiProperties.triggeredActivityTargets: self`; on this Heal: `AUTOMATION.midi-qol.fields.midiProperties.identifier: drain-heal`; `AUTOMATION.midi-qol.fields.midiProperties.automationOnly: true` |
| "after you use this item's other ability, you can also regain hit points" (an optional follow-up the user chooses) | on this Heal: `AUTOMATION.midi-qol.fields.midiProperties.followUp: true`; `AUTOMATION.midi-qol.fields.midiProperties.followUpTargets: self` |
| "a creature of your choice that you can see within range" (MIDI must enforce the sight rule) | `AUTOMATION.midi-qol.fields.midiProperties.targetMustBeSeen: true` |

### ACTIVITY_HEAL with MIDI-QOL: example

The Base example of `ACTIVITY_HEAL` with the MIDI-QOL settings added in `AUTOMATION`. They are stored settings: importing the block does not run a MIDI-QOL workflow.

```yaml
ACTIVITY_HEAL:
  ACTIVITY:
    Name: Restoring Touch
  ACTIVATION:
    Override Activation: true
    Activation Type: action
  DURATION:
    Override Duration: true
    Duration Time: inst
    Concentration: false
  CONSUMPTION: []
  RANGE:
    Override Range: true
    Range Units: touch
  TARGETS:
    Override Target: true
    Target Type: creature
    Target Amount: 1
    Choose Targets: true
  HEALING:
    DAMAGE_DATA:
      Dice Count: 1
      Die Denomination: 8
      Bonus: "2"
      Damage Types:
        - healing
      Custom Enabled: false
      Custom Formula: ""
      Scaling Mode: none
      Scaling Dice Count: 0
      Scaling Formula: ""
  AUTOMATION:
    midi-qol:
      fields:
        midiProperties:
          identifier: example-heal
          displayActivityName: true
          triggeredActivityId: none
          autoCEEffects: none
          confirmTargets: never
```

### ACTIVITY_HEAL with MIDI-QOL: check before you answer

1. A Heal activity with `isOverTimeFlag: true` runs each turn only for creatures that carry an effect with a change whose `Attribute Key` is `flags.midi-qol.ActivityOverTime` and whose `Value` is this activity's `midiProperties.identifier`. Put that change in the effect that the Item applies.
2. `overTimeProperties.rollAs: source` rolls the healing with the statistics of the Item's owner. `target` uses the creature that carries the effect. Write `source` when the formula uses the healer's modifier or level.
3. A triggered activity is skipped when its target list is empty. `midiProperties.triggeredActivityTargets: self` always runs. `hitTargets` and `failedSaves` run only when the first activity produced such targets.
4. Do not fill `useConditionText`, `midiProperties.triggeredActivityConditionText` or the over-time remove conditions with made-up expressions. Write them only when the user supplies the expression.

## ACTIVITY_UTILITY with MIDI-QOL: when to add it

- **Root key:** `ACTIVITY_UTILITY`
- **When to use:** Use the MIDI Utility template when MIDI-QOL is active and the Utility activity must start another activity, be started by one, switch its own effect off again, or exist only for automation. If the source needs none of that, use the base Utility template.
- **Smallest working block:** MIDI additions: The base smallest block, plus `AUTOMATION.midi-qol.fields.midiProperties.identifier` when another activity or an effect refers to this one.

### ACTIVITY_UTILITY with MIDI-QOL: from source text to YAML

The rows add MIDI-QOL settings to a complete Base `ACTIVITY_UTILITY` block. Paths start at the root key, so `AUTOMATION.midi-qol.fields.midiProperties.identifier` is the `identifier` key in `midiProperties` under `fields` in `midi-qol` in the `AUTOMATION` section of the block.

| The source says | Write |
| --- | --- |
| "when you activate this feature, a second effect of the item also happens" (two activities in a chain) | on the first activity: `AUTOMATION.midi-qol.fields.midiProperties.triggeredActivityId: finish-sequence`; `AUTOMATION.midi-qol.fields.midiProperties.triggeredActivityTargets: self`; on the second activity: `AUTOMATION.midi-qol.fields.midiProperties.identifier: finish-sequence`; `AUTOMATION.midi-qol.fields.midiProperties.automationOnly: true` |
| "the second effect costs no additional charge" (the triggered activity must not spend resources again or open its dialog) | on the first activity: `AUTOMATION.midi-qol.fields.midiProperties.triggeredActivityConsume: false`; `AUTOMATION.midi-qol.fields.midiProperties.triggeredActivityConfigure: false` |
| "use this feature again to end it" (an on/off ability on the user) | `AUTOMATION.midi-qol.fields.midiProperties.toggleEffect: true` |
| "choose one of the following effects" (several `APPLIED_EFFECTS` entries, the user picks) | `AUTOMATION.midi-qol.fields.midiProperties.chooseEffects: true` |
| "the second effect happens only as part of the first" (a helper activity that players never click) | `AUTOMATION.midi-qol.fields.midiProperties.automationOnly: true`; `AUTOMATION.midi-qol.fields.midiProperties.identifier: finish-sequence` |

### ACTIVITY_UTILITY with MIDI-QOL: settings only this type has

```yaml
ACTIVITY_UTILITY:
  AUTOMATION:
    midi-qol:
      fields:
        # Every setting of "AUTOMATION: common settings for every activity" may be written here too.
        # Utility only
        otherActivityAsParentType: "[true|false]"
```

#### ACTIVITY_UTILITY with MIDI-QOL values: AUTOMATION: Utility only

| Field | Description | Accepted Values |
|---|---|---|
| `otherActivityAsParentType` | The activity named in `otherActivityId` is treated as having this activity's action type. | `true` or `false`. |

### ACTIVITY_UTILITY with MIDI-QOL: example

The Base example of `ACTIVITY_UTILITY` with the MIDI-QOL settings added in `AUTOMATION`. They are stored settings: importing the block does not run a MIDI-QOL workflow.

```yaml
ACTIVITY_UTILITY:
  ACTIVITY:
    Name: Measure the Current
  ACTIVATION:
    Override Activation: true
    Activation Type: action
  DURATION:
    Override Duration: true
    Duration Time: inst
    Concentration: false
  CONSUMPTION: []
  BEHAVIOR:
    Roll Prompt: true
  UTILITY_ROLL:
    Roll Label: Current strength
    Roll Formula: 1d6
    Visible to All: true
  AUTOMATION:
    midi-qol:
      fields:
        otherActivityId: none
        midiProperties:
          identifier: example-utility
          displayActivityName: true
          triggeredActivityId: none
          autoCEEffects: none
          confirmTargets: never
```

### ACTIVITY_UTILITY with MIDI-QOL: check before you answer

1. The value of `midiProperties.triggeredActivityId` must be the `midiProperties.identifier` of another activity in the same output. Write `none` to switch the trigger off; MIDI-QOL rejects an empty value.
2. A triggered activity is skipped when its target list is empty. A Utility activity usually has no targets, so write `midiProperties.triggeredActivityTargets: self`, or `retarget` to let the second activity pick its own targets.
3. `midiProperties.toggleEffect` removes the activity's effect from the user when the activity is used while that effect is already present. It needs an `APPLIED_EFFECTS` entry.
4. Write `otherActivityAsParentType` only together with `otherActivityId`. Do not fill `useConditionText` or `midiProperties.triggeredActivityConditionText` with made-up expressions; write them only when the user supplies the expression.

## ACTIVITY_CHECK with MIDI-QOL: when to add it

- **Root key:** `ACTIVITY_CHECK`
- **When to use:** Use the MIDI Check template when MIDI-QOL is active and the check has consequences the base fields cannot store: damage on a failed check, allies that pass or fail without rolling, a check against a magical effect, or a link from an attack on the same Item. If the source needs none of that, use the base Check template.
- **Smallest working block:** MIDI additions: The base smallest block. Add `AUTOMATION.midi-qol.fields.damage.onSave` and one `AUTOMATION.midi-qol.fields.damage.parts` entry only when a failed check deals damage. Add `AUTOMATION.midi-qol.fields.midiProperties.identifier` when another activity refers to this check.

### ACTIVITY_CHECK with MIDI-QOL: from source text to YAML

The rows add MIDI-QOL settings to a complete Base `ACTIVITY_CHECK` block. Paths start at the root key, so `AUTOMATION.midi-qol.fields.midiProperties.identifier` is the `identifier` key in `midiProperties` under `fields` in `midi-qol` in the `AUTOMATION` section of the block.

| The source says | Write |
| --- | --- |
| "on a failed check, the creature takes 2d6 bludgeoning damage" | `AUTOMATION.midi-qol.fields.damage.onSave: none`; one `AUTOMATION.midi-qol.fields.damage.parts` entry with `number: 2`; `denomination: 6`; `types: [bludgeoning]` |
| "or half as much damage on a successful check" | `AUTOMATION.midi-qol.fields.damage.onSave: half` |
| "creatures of your choice automatically succeed" (the user's allies are spared) | `AUTOMATION.midi-qol.fields.friendlySave: friendlySuccess` |
| "a spell or other magical effect" (the check is made against magic) | `AUTOMATION.midi-qol.fields.midiProperties.magicEffect: true` |
| "on a hit, the target must succeed on a Strength (Athletics) check or be knocked prone" (this Check follows an attack on the same Item) | on this Check: `AUTOMATION.midi-qol.fields.midiProperties.identifier: trip-check`; `AUTOMATION.midi-qol.fields.midiProperties.otherActivityCompatible: true`; `AUTOMATION.midi-qol.fields.midiProperties.automationOnly: true`; on the attack: `AUTOMATION.midi-qol.fields.otherActivityId: trip-check` |
| "this damage is doubled when the attack is a critical hit" | `AUTOMATION.midi-qol.fields.damage.critical.allow: true` |

### ACTIVITY_CHECK with MIDI-QOL: settings only this type has

```yaml
ACTIVITY_CHECK:
  AUTOMATION:
    midi-qol:
      fields:
        # Every setting of "AUTOMATION: common settings for every activity" may be written here too.
        # Check only
        otherActivityAsParentType: "[true|false]"
        friendlySave: "[default|friendlySuccess|friendlyFail]"
        # Damage dealt by the check. A Base Check activity has no damage.
        damage:
          onSave: "[none|half|full]"
          critical:
            allow: "[true|false]"
            bonus: "[formula|null]"
          # (Optional, repeatable)
          parts:
            - number: "[number|null]"
              denomination: "[number|null]"
              bonus: "[formula|null]"
              types: [fire] # Damage types.
              modifiers: [] # Ordered dice modifiers.
              custom:
                enabled: "[true|false]"
                formula: "[formula|null]"
              scaling:
                mode: "[whole|half]" # "" is no scaling.
                number: "[number|null]"
                formula: "[formula|null]"
```

#### ACTIVITY_CHECK with MIDI-QOL values: AUTOMATION: Check only

A Base Check activity has no damage. With MIDI-QOL a check can deal damage, written here.

| Field | Description | Accepted Values |
|---|---|---|
| `otherActivityAsParentType` | The activity named in `otherActivityId` is treated as having this activity's action type. | `true` or `false`. |
| `friendlySave` | Let allies of the user succeed or fail without rolling. | `default` = allies roll, `friendlySuccess` = allies succeed, `friendlyFail` = allies fail. |
| `damage.onSave` | What happens to the damage when the target succeeds. | `none` = no damage, `half` = half damage, `full` = full damage. |
| `damage.critical.allow` | The damage can be critical. | `true` or `false`. |
| `damage.critical.bonus` | Extra damage on a critical. | A formula such as `"2d6"`, or `null`. |
| `damage.parts[].number` | Number of dice. | A number, or `null`. |
| `damage.parts[].denomination` | Number of sides of the die (`6`, not `d6`). | A number, or `null`. |
| `damage.parts[].bonus` | Bonus added to the dice. | A formula such as `"@mod"`, or `null`. |
| `damage.parts[].types` | Damage types. | A list such as `[fire]`. |
| `damage.parts[].modifiers` | Ordered dice modifiers. | A list of text; `[]` for none. |
| `damage.parts[].custom.enabled` | Use `custom.formula` instead of the dice fields. | `true` or `false`. |
| `damage.parts[].custom.formula` | The whole damage formula. | A formula, or `null`. |
| `damage.parts[].scaling.mode` | How the damage scales. | `""` = no scaling, `whole`, `half`. |
| `damage.parts[].scaling.number` | Dice added per step of scaling. | A number, or `null`. |
| `damage.parts[].scaling.formula` | Formula added per step of scaling. | A formula, or `null`. |

### ACTIVITY_CHECK with MIDI-QOL: example

The Base example of `ACTIVITY_CHECK` with the MIDI-QOL settings added in `AUTOMATION`. They are stored settings: importing the block does not run a MIDI-QOL workflow.

```yaml
ACTIVITY_CHECK:
  ACTIVITY:
    Name: Balance on the Beam
  ACTIVATION:
    Override Activation: true
    Activation Type: action
  DURATION:
    Override Duration: true
    Duration Time: inst
    Concentration: false
  CONSUMPTION: []
  CHECK_DETAILS:
    Check Ability: dex
    Associated Skills:
      - acr
    Associated Tools: []
    DC Calculation: custom
    DC Formula: "15"
  AUTOMATION:
    midi-qol:
      fields:
        otherActivityId: none
        midiProperties:
          identifier: example-check
          displayActivityName: true
          triggeredActivityId: none
          autoCEEffects: none
          confirmTargets: never
```

### ACTIVITY_CHECK with MIDI-QOL: check before you answer

1. Check damage is written under `AUTOMATION.midi-qol.fields.damage`: `onSave`, `critical` and the list `parts`. A `parts` entry uses the stored names `number`, `denomination`, `bonus` and `types`, not the `DAMAGE_DATA` labels of the Base templates.
2. Do not use `SAVE_DAMAGE` or `DAMAGE` in a Check block. They are skipped and shown as a warning in the preview.
3. `damage.onSave` is `none`, `half` or `full` and says what a successful check does to the damage. Any other value is a parse error.
4. `friendlySave` is written directly under `AUTOMATION.midi-qol.fields`; `magicEffect` is under `midiProperties`, as in the Save template.
5. Write `damage.critical.allow` and `damage.critical.bonus` only together with a `damage.parts` entry. Write `otherActivityAsParentType` only together with `otherActivityId`.

## ACTIVITY_CAST with MIDI-QOL: when to add it

- **Root key:** `ACTIVITY_CAST`
- **When to use:** Use the MIDI Cast file when MIDI-QOL is active and the cast step itself needs a MIDI setting: a use condition, a fixed answer to the consume dialog, or an identifier that another activity or a macro refers to. When the source only names the spell, the Base Cast template is enough.
- **Smallest working block:** MIDI additions: The Base Cast smallest block plus the one MIDI setting you need, for example `AUTOMATION.midi-qol.fields.midiProperties.identifier`. Leave out every MIDI setting the source does not call for.

### ACTIVITY_CAST with MIDI-QOL: from source text to YAML

The rows add MIDI-QOL settings to a complete Base `ACTIVITY_CAST` block. Paths start at the root key, so `AUTOMATION.midi-qol.fields.midiProperties.identifier` is the `identifier` key in `midiProperties` under `fields` in `midi-qol` in the `AUTOMATION` section of the block.

| The source says | Write |
| --- | --- |
| "you can cast it only while you are Bloodied" (a limit MIDI should enforce) | `AUTOMATION.midi-qol.fields.useConditionText: "@attributes.hp.value <= @attributes.hp.max / 2"`; `AUTOMATION.midi-qol.fields.useConditionReason: You are not Bloodied.` |
| "expend 1 charge", and the user wants no consume prompt | `AUTOMATION.midi-qol.fields.midiProperties.forceConsumeDialog: never` |
| the user wants the consume prompt on every cast | `AUTOMATION.midi-qol.fields.midiProperties.forceConsumeDialog: always` |
| another activity or a macro has to find this cast | `AUTOMATION.midi-qol.fields.midiProperties.identifier: cast-fireball` |
| the chat card should show which activity was used | `AUTOMATION.midi-qol.fields.midiProperties.displayActivityName: true` |
| no other activity runs after the cast | `AUTOMATION.midi-qol.fields.midiProperties.triggeredActivityId: none` |

### ACTIVITY_CAST with MIDI-QOL: example

The Base example of `ACTIVITY_CAST` with the MIDI-QOL settings added in `AUTOMATION`. They are stored settings: importing the block does not run a MIDI-QOL workflow.

```yaml
ACTIVITY_CAST:
  ACTIVITY:
    Name: Cast Fireball
  ACTIVATION:
    Override Activation: true
    Activation Type: action
  DURATION:
    Override Duration: true
    Duration Time: inst
    Concentration: false
  CONSUMPTION: []
  BEHAVIOR:
    Consume Spell Slot: false
    Display in Spellbook: true
    Measured Template Prompt: true
  CASTING_DETAILS:
    Item Name: Fireball
    Item UUID: Compendium.dnd5e.spells24.Item.phbsplFireball00
    Casting Ability: int
    Spell Level: 3
    Override Values: true
    Attack Bonus: null
    Save DC: 15
    Ignored Properties: []
  AUTOMATION:
    midi-qol:
      fields:
        midiProperties:
          identifier: example-cast
          displayActivityName: true
          triggeredActivityId: none
          autoCEEffects: none
```

### ACTIVITY_CAST with MIDI-QOL: check before you answer

1. Do not set `midiProperties.confirmTargets` on a Cast block. MIDI forces it to `never` for Cast.
2. Do not add `isOverTimeFlag`, `overTimeProperties`, `midiProperties.chooseEffects`, `midiProperties.toggleEffect` or `effectConditionText`. Cast has no applied effects and no damage of its own.
3. A condition is stored as text and is not checked on import. Write one only when you know the MIDI expression. Otherwise leave it out and keep the limit in the description.
4. `midiProperties.triggeredActivityId` is `none` or the `midiProperties.identifier` of another activity on the same Item. Never leave it empty, and never invent an ID or UUID.

## ACTIVITY_ENCHANT with MIDI-QOL: when to add it

- **Root key:** `ACTIVITY_ENCHANT`
- **When to use:** Use the MIDI Enchant file when MIDI-QOL is active and the enchant step needs MIDI control: a use condition, a fixed answer to the consume or target dialog, an identifier, or another activity that runs afterwards. The enchantment profiles are written exactly as in the Base template.
- **Smallest working block:** MIDI additions: The Base Enchant smallest block plus only the `AUTOMATION.midi-qol.fields` settings the source calls for, for example `AUTOMATION.midi-qol.fields.midiProperties.confirmTargets: never`.

### ACTIVITY_ENCHANT with MIDI-QOL: from source text to YAML

The rows add MIDI-QOL settings to a complete Base `ACTIVITY_ENCHANT` block. Paths start at the root key, so `AUTOMATION.midi-qol.fields.midiProperties.identifier` is the `identifier` key in `midiProperties` under `fields` in `midi-qol` in the `AUTOMATION` section of the block.

| The source says | Write |
| --- | --- |
| the enchantment goes on an Item, so no token target needs confirming | `AUTOMATION.midi-qol.fields.midiProperties.confirmTargets: never` |
| "you can use this only while you are Bloodied" | `AUTOMATION.midi-qol.fields.useConditionText: "@attributes.hp.value <= @attributes.hp.max / 2"`; `AUTOMATION.midi-qol.fields.useConditionReason: You are not Bloodied.` |
| "when you imbue the weapon, you also gain temporary hit points" (a second activity on the Item with the identifier `imbue-ward` grants them) | `AUTOMATION.midi-qol.fields.midiProperties.triggeredActivityId: imbue-ward`; `AUTOMATION.midi-qol.fields.midiProperties.triggeredActivityTargets: self`; `AUTOMATION.midi-qol.fields.midiProperties.triggeredActivityRollAs: self` |
| another activity or a macro has to find this enchant step | `AUTOMATION.midi-qol.fields.midiProperties.identifier: imbue-weapon` |
| the resource is spent without the consume prompt | `AUTOMATION.midi-qol.fields.midiProperties.forceConsumeDialog: never` |

### ACTIVITY_ENCHANT with MIDI-QOL: example

The Base example of `ACTIVITY_ENCHANT` with the MIDI-QOL settings added in `AUTOMATION`. They are stored settings: importing the block does not run a MIDI-QOL workflow.

```yaml
ACTIVITY_ENCHANT:
  ACTIVITY:
    Name: Keen Edge
  ACTIVATION:
    Override Activation: true
    Activation Type: action
  DURATION:
    Override Duration: true
    Duration Time: inst
    Concentration: false
  CONSUMPTION: []
  BEHAVIOR:
    Automatically Self Enchant: true
  RESTRICTIONS:
    Item Type: weapon
    Allow Magical: true
  APPLIED_EFFECTS:
    - DETAILS:
        Name: Keen Edge +1
        Effect Type: enchantment
        Apply Effect to Actor: false
      EFFECT_DESCRIPTION:
        Effect Description: <p>Keen Edge +1.</p>
      DURATION:
        Value: 1
        Units: minutes
      CHANGES:
        - Attribute Key: system.magicalBonus
          Change Type: add
          Change Phase: initial
          Value: 1
          Priority: 20
      APPLICATION:
        Rider Activities: []
        Rider Effects: []
        Rider Items: []
  AUTOMATION:
    midi-qol:
      fields:
        midiProperties:
          identifier: example-enchant
          displayActivityName: true
          triggeredActivityId: none
          autoCEEffects: none
          confirmTargets: never
```

### ACTIVITY_ENCHANT with MIDI-QOL: check before you answer

1. Do not put `flags.midi-qol.*` keys in an enchantment profile. They are Actor keys, and an enchantment changes the Item.
2. `midiProperties.triggeredActivityId` is `none` or the `midiProperties.identifier` of an activity that exists on the same Item. Never leave it empty, and never invent an ID or UUID.
3. Leave `isOverTimeFlag` and `overTimeProperties` out. They describe an activity that repeats each turn, and an enchantment is not one.
4. Use the names and values of the field reference exactly (`never`, not `Never`).

## ACTIVITY_SUMMON with MIDI-QOL: when to add it

- **Root key:** `ACTIVITY_SUMMON`
- **When to use:** Use the MIDI Summon file when MIDI-QOL is active and the summon step needs MIDI control: MIDI's own friendly-summon switch, a use condition, a fixed answer to the use dialog, an identifier, or another activity that runs afterwards. Profiles and creature changes are written exactly as in the Base template.
- **Smallest working block:** MIDI additions: The Base Summon smallest block plus the one MIDI setting you need, for example `AUTOMATION.midi-qol.fields.friendlySummon` or `AUTOMATION.midi-qol.fields.midiProperties.identifier`.

### ACTIVITY_SUMMON with MIDI-QOL: from source text to YAML

The rows add MIDI-QOL settings to a complete Base `ACTIVITY_SUMMON` block. Paths start at the root key, so `AUTOMATION.midi-qol.fields.midiProperties.identifier` is the `identifier` key in `midiProperties` under `fields` in `midi-qol` in the `AUTOMATION` section of the block.

| The source says | Write |
| --- | --- |
| "the creature is friendly to you", stored as MIDI's own switch | `AUTOMATION.midi-qol.fields.friendlySummon: true` (gives the summoned token the caster's disposition; the Base field `CREATURE_CHANGES.Match Disposition: true` does the same without MIDI) |
| "you can use this only while you are Bloodied" | `AUTOMATION.midi-qol.fields.useConditionText: "@attributes.hp.value <= @attributes.hp.max / 2"`; `AUTOMATION.midi-qol.fields.useConditionReason: You are not Bloodied.` |
| the user must always get the use dialog to choose the creature and the slot | `AUTOMATION.midi-qol.fields.midiProperties.forceConsumeDialog: always` |
| another activity or a macro has to find this summon | `AUTOMATION.midi-qol.fields.midiProperties.identifier: conjure-beast` |
| no other activity runs after the summon | `AUTOMATION.midi-qol.fields.midiProperties.triggeredActivityId: none` |

### ACTIVITY_SUMMON with MIDI-QOL: settings only this type has

```yaml
ACTIVITY_SUMMON:
  AUTOMATION:
    midi-qol:
      fields:
        # Every setting of "AUTOMATION: common settings for every activity" may be written here too.
        # Summon only
        friendlySummon: "[true|false]"
```

#### ACTIVITY_SUMMON with MIDI-QOL values: AUTOMATION: Summon only

| Field | Description | Accepted Values |
|---|---|---|
| `friendlySummon` | The summoned token gets the same disposition as the caster. | `true` or `false`. |

### ACTIVITY_SUMMON with MIDI-QOL: example

The Base example of `ACTIVITY_SUMMON` with the MIDI-QOL settings added in `AUTOMATION`. They are stored settings: importing the block does not run a MIDI-QOL workflow.

```yaml
ACTIVITY_SUMMON:
  ACTIVITY:
    Name: Call a Wolf
  ACTIVATION:
    Override Activation: true
    Activation Type: action
  DURATION:
    Override Duration: true
    Duration Time: inst
    Concentration: false
  CONSUMPTION: []
  BEHAVIOR:
    Summon Prompt: true
  RANGE:
    Override Range: true
    Range Units: ft
    Range Value: 30
  TARGETS:
    Override Target: true
    Target Type: creature
    Target Amount: 1
    Choose Targets: true
  SUMMONS_PROFILES:
    Mode: By Direct Link
    PROFILES:
      - Count: "1"
        Display Name: Wolf
        Actor UUID: Compendium.dnd5e.actors24.Actor.mmWolf0000000000
  CREATURE_CHANGES:
    Match Disposition: true
    Match Proficiency: false
  AUTOMATION:
    midi-qol:
      fields:
        midiProperties:
          identifier: example-summon
          displayActivityName: true
          triggeredActivityId: none
          autoCEEffects: none
          confirmTargets: always
```

### ACTIVITY_SUMMON with MIDI-QOL: check before you answer

1. `friendlySummon` is written directly under `AUTOMATION.midi-qol.fields`, not under `midiProperties`. Write `friendlySummon: true` without quotes.
2. For a friendly creature prefer `CREATURE_CHANGES.Match Disposition: true`, which works without MIDI. Add `friendlySummon` only when the user asks for the MIDI setting.
3. A condition is stored as text and is not checked on import. Write one only when you know the MIDI expression.

## ACTIVITY_TRANSFORM with MIDI-QOL: when to add it

- **Root key:** `ACTIVITY_TRANSFORM`
- **When to use:** Use the MIDI Transform file when MIDI-QOL is active and the transform step needs MIDI control: target confirmation, a use condition, a fixed answer to the use dialog, an identifier, or another activity that runs afterwards. Profiles and transformation settings are written exactly as in the Base template.
- **Smallest working block:** MIDI additions: The Base Transform smallest block plus only the `AUTOMATION.midi-qol.fields` settings the source calls for, for example `AUTOMATION.midi-qol.fields.midiProperties.identifier`.

### ACTIVITY_TRANSFORM with MIDI-QOL: from source text to YAML

The rows add MIDI-QOL settings to a complete Base `ACTIVITY_TRANSFORM` block. Paths start at the root key, so `AUTOMATION.midi-qol.fields.midiProperties.identifier` is the `identifier` key in `midiProperties` under `fields` in `midi-qol` in the `AUTOMATION` section of the block.

| The source says | Write |
| --- | --- |
| "a creature that you can see within range" (the user should confirm who is transformed) | `AUTOMATION.midi-qol.fields.midiProperties.confirmTargets: always` |
| "you can use this only while you are Bloodied" | `AUTOMATION.midi-qol.fields.useConditionText: "@attributes.hp.value <= @attributes.hp.max / 2"`; `AUTOMATION.midi-qol.fields.useConditionReason: You are not Bloodied.` |
| the rules add a second step that another activity on the Item handles (that activity has the identifier `beast-vigor`) | `AUTOMATION.midi-qol.fields.midiProperties.triggeredActivityId: beast-vigor`; `AUTOMATION.midi-qol.fields.midiProperties.triggeredActivityTargets: targets`; `AUTOMATION.midi-qol.fields.midiProperties.triggeredActivityRollAs: self` |
| another activity or a macro has to find this transform | `AUTOMATION.midi-qol.fields.midiProperties.identifier: wild-shape` |
| the user must always get the use dialog to choose the form | `AUTOMATION.midi-qol.fields.midiProperties.forceConsumeDialog: always` |

### ACTIVITY_TRANSFORM with MIDI-QOL: example

The Base example of `ACTIVITY_TRANSFORM` with the MIDI-QOL settings added in `AUTOMATION`. They are stored settings: importing the block does not run a MIDI-QOL workflow.

```yaml
ACTIVITY_TRANSFORM:
  ACTIVITY:
    Name: Wolf Shape
  ACTIVATION:
    Override Activation: true
    Activation Type: action
  DURATION:
    Override Duration: true
    Duration Time: inst
    Concentration: false
  CONSUMPTION: []
  RANGE:
    Override Range: true
    Range Units: self
  TARGETS:
    Override Target: true
    Target Type: self
    Target Amount: 1
  TRANSFORMATION_PROFILES:
    Mode: By Direct Link
    PROFILES:
      - Display Name: Wolf
        Actor UUID: Compendium.dnd5e.actors24.Actor.mmWolf0000000000
  TRANSFORMATION_DETAILS:
    Preset: wildshape
    Customize: false
  AUTOMATION:
    midi-qol:
      fields:
        midiProperties:
          identifier: example-transform
          displayActivityName: true
          triggeredActivityId: none
          autoCEEffects: none
          confirmTargets: never
```

### ACTIVITY_TRANSFORM with MIDI-QOL: check before you answer

1. In direct-link and CR mode MIDI transforms the tokens that the use targets. For a self-transformation keep `TARGETS.Target Type: self`, as in the template example.
2. MIDI forces target confirmation to `never` when `TARGETS.Target Type` is `self`. `midiProperties.confirmTargets` matters only when another creature is transformed.
3. `midiProperties.triggeredActivityId` is `none` or the `midiProperties.identifier` of an activity that exists on the same Item. Never leave it empty, and never invent an ID or UUID.
4. Leave `isOverTimeFlag` and `overTimeProperties` out. They describe an activity that repeats each turn, and a transformation is not one.

## ACTIVITY_FORWARD with MIDI-QOL: when to add it

- **Root key:** `ACTIVITY_FORWARD`
- **When to use:** Use the MIDI Forward file when MIDI-QOL is active and this payment path needs its own MIDI identifier or card label. MIDI runs the workflow of the target activity, so most MIDI settings belong on that activity, not on the Forward.
- **Smallest working block:** MIDI additions: The Base Forward smallest block plus `AUTOMATION.midi-qol.fields.midiProperties.identifier`.

### ACTIVITY_FORWARD with MIDI-QOL: from source text to YAML

The rows add MIDI-QOL settings to a complete Base `ACTIVITY_FORWARD` block. Paths start at the root key, so `AUTOMATION.midi-qol.fields.midiProperties.identifier` is the `identifier` key in `midiProperties` under `fields` in `midi-qol` in the `AUTOMATION` section of the block.

| The source says | Write |
| --- | --- |
| another activity or a macro has to find this payment path | `AUTOMATION.midi-qol.fields.midiProperties.identifier: flame-burst-3` |
| the chat card should show which path was used | `AUTOMATION.midi-qol.fields.midiProperties.displayActivityName: true` |
| no other activity runs after this one | `AUTOMATION.midi-qol.fields.midiProperties.triggeredActivityId: none` |
| "you can use this only while you are Bloodied", or any other MIDI rule for the effect itself | put `AUTOMATION.midi-qol.fields.useConditionText: "@attributes.hp.value <= @attributes.hp.max / 2"` in the target activity's block, not in the Forward block |

### ACTIVITY_FORWARD with MIDI-QOL: example

The Base example of `ACTIVITY_FORWARD` with the MIDI-QOL settings added in `AUTOMATION`. They are stored settings: importing the block does not run a MIDI-QOL workflow.

```yaml
ACTIVITY_FORWARD:
  ACTIVITY:
    Name: Roll the Current
  ACTIVATION:
    Override Activation: true
    Activation Type: action
  CONSUMPTION: []
  FORWARD_TARGET:
    Activity: Current Reading
  AUTOMATION:
    midi-qol:
      fields:
        midiProperties:
          identifier: example-forward
          displayActivityName: true
          triggeredActivityId: none
          autoCEEffects: none
---
ACTIVITY_UTILITY:
  ACTIVITY:
    Name: Current Reading
  ACTIVATION:
    Override Activation: true
    Activation Type: action
  DURATION:
    Override Duration: true
    Duration Time: inst
    Concentration: false
  CONSUMPTION: []
  BEHAVIOR:
    Roll Prompt: true
  UTILITY_ROLL:
    Roll Label: Current strength
    Roll Formula: 1d6
    Visible to All: true
```

### ACTIVITY_FORWARD with MIDI-QOL: check before you answer

1. MIDI hands the use of a Forward to its target activity, and the workflow runs with the target's MIDI settings. On the Forward write only what labels this path (`midiProperties.identifier`, `midiProperties.displayActivityName`).
2. Do not set `midiProperties.confirmTargets`. MIDI forces it to `never` for Forward.
3. Do not name a Forward in another activity's `midiProperties.triggeredActivityId` or `otherActivityId`. MIDI does not offer Forward activities there. Name the target activity.
4. Leave `isOverTimeFlag` and `overTimeProperties` out of a Forward block.

## ACTIVITY_TELEPORT with MIDI-QOL: when to add it

- **Root key:** `ACTIVITY_TELEPORT`
- **When to use:** Use the MIDI Teleport file when MIDI-QOL is active and the rules say more than a distance: who teleports (the user, the user with targets, only the targets, a swap), that the destination must be seen, what happens when the destination is blocked, or that the teleport happens during the use.
- **Smallest working block:** MIDI additions: The Base Teleport smallest block plus `AUTOMATION.midi-qol.fields.midiTeleport.movers` (who moves).

### ACTIVITY_TELEPORT with MIDI-QOL: from source text to YAML

The rows add MIDI-QOL settings to a complete Base `ACTIVITY_TELEPORT` block. Paths start at the root key, so `AUTOMATION.midi-qol.fields.midiProperties.identifier` is the `identifier` key in `midiProperties` under `fields` in `midi-qol` in the `AUTOMATION` section of the block.

| The source says | Write |
| --- | --- |
| "you teleport" (only the user moves) | `AUTOMATION.midi-qol.fields.midiTeleport.movers: caster` |
| "you can bring along one willing creature" | `AUTOMATION.midi-qol.fields.midiTeleport.movers: casterAndTargets` |
| "the target is teleported", or only the creatures that fail a save are moved | `AUTOMATION.midi-qol.fields.midiTeleport.movers: targets` |
| "you and the target swap places" | `AUTOMATION.midi-qol.fields.midiTeleport.movers: swap` |
| "to an unoccupied space that you can see" | `AUTOMATION.midi-qol.fields.midiTeleport.requireSight: true`; `AUTOMATION.midi-qol.fields.midiTeleport.blocked: refuse` |
| "if you would arrive in a place already occupied, the spell fails and each traveller takes damage" | `AUTOMATION.midi-qol.fields.midiTeleport.blocked: fail` |
| the teleport should happen during the use, not from the card button | `AUTOMATION.midi-qol.fields.midiTeleport.plan: auto` |
| another activity triggers this teleport, for example a save whose failures are moved | `AUTOMATION.midi-qol.fields.midiProperties.identifier: banishing-shove`; `AUTOMATION.midi-qol.fields.midiProperties.automationOnly: true` |

### ACTIVITY_TELEPORT with MIDI-QOL: settings only this type has

```yaml
ACTIVITY_TELEPORT:
  AUTOMATION:
    midi-qol:
      fields:
        # Every setting of "AUTOMATION: common settings for every activity" may be written here too.
        # Teleport only
        midiTeleport:
          movers: "[default|caster|casterAndTargets|targets|swap]"
          plan: "[button|auto]"
          blocked: "[refuse|fail]"
          requireSight: "[true|false]"
```

#### ACTIVITY_TELEPORT with MIDI-QOL values: AUTOMATION: Teleport only

| Field | Description | Accepted Values |
|---|---|---|
| `midiTeleport.movers` | Who teleports. `default` moves the controlled tokens as the Base activity does. `casterAndTargets` also places each target within 5 feet of the caster's destination. `targets` places each target within the teleport distance of the caster. `swap` makes the caster and the first target trade places. `targets` and `swap` need targets when the activity is used. | `default`, `caster`, `casterAndTargets`, `targets`, `swap`. |
| `midiTeleport.plan` | Plan the move from the chat card's button, or during the use after its effects and damage. | `button`, `auto`. |
| `midiTeleport.blocked` | What a blocked destination does. `refuse` asks for another destination. `fail` ends the teleport for everyone and uses the Item's damage activity on the travellers. | `refuse`, `fail`. |
| `midiTeleport.requireSight` | The caster must be able to see the destination ("a space you can see"). | `true` or `false`. |

### ACTIVITY_TELEPORT with MIDI-QOL: example

The Base example of `ACTIVITY_TELEPORT` with the MIDI-QOL settings added in `AUTOMATION`. They are stored settings: importing the block does not run a MIDI-QOL workflow.

```yaml
ACTIVITY_TELEPORT:
  ACTIVITY:
    Name: Blink Step
  ACTIVATION:
    Override Activation: true
    Activation Type: bonus
  DURATION:
    Override Duration: true
    Duration Time: inst
    Concentration: false
  CONSUMPTION: []
  RANGE:
    Override Range: true
    Range Units: self
  TARGETS:
    Override Target: true
    Target Type: self
  TELEPORT_DETAILS:
    Override Teleport: true
    Teleport Distance: "30"
    Teleport Units: ft
  AUTOMATION:
    midi-qol:
      fields:
        otherActivityId: none
        midiProperties:
          identifier: example-teleport
          displayActivityName: true
          triggeredActivityId: none
          autoCEEffects: none
          confirmTargets: never
```

### ACTIVITY_TELEPORT with MIDI-QOL: check before you answer

1. `midiTeleport` is written directly under `AUTOMATION.midi-qol.fields`, not under `midiProperties`.
2. Values are exact and case-sensitive. `movers` is `default`, `caster`, `casterAndTargets`, `targets` or `swap`; `plan` is `button` or `auto`; `blocked` is `refuse` or `fail`; `requireSight` is `true` or `false` without quotes. Anything else is a parse error.
3. `movers: default` keeps the dnd5e behaviour: the selected tokens move. Leave `midiTeleport` out when the rules need nothing more.
4. With `blocked: fail` MIDI uses the Item's damage activity on the travellers, so that activity must exist on the Item.

## EFFECT with DAE: when to add it

- **Root key:** `EFFECT`
- **When to use:** Use the MIDI Effect file when DAE is active (and MIDI-QOL for `flags.midi-qol.*` keys) and the effect needs something the Base fields cannot store: an end trigger such as "until you attack" or "until it takes damage", a rule against stacking, an expression that switches the effect off, a rolled duration, or a MIDI flag such as advantage on one kind of roll.
- **Smallest working block:** MIDI additions: The Base Effect smallest block plus the one DAE setting you need, for example `AUTOMATION.dae.specialDuration: [isDamaged]`.

### EFFECT with DAE: from source text to YAML

The rows add MIDI-QOL settings to a complete Base `EFFECT` block. Paths start at the root key, so `AUTOMATION.midi-qol.fields.midiProperties.identifier` is the `identifier` key in `midiProperties` under `fields` in `midi-qol` in the `AUTOMATION` section of the block.

| The source says | Write |
| --- | --- |
| "until you make an attack", "the next attack roll you make" | `AUTOMATION.dae.specialDuration: [1Attack]` |
| "the effect ends if the creature takes damage" | `AUTOMATION.dae.specialDuration: [isDamaged]` |
| two end triggers ("ends when it attacks or takes damage") | `AUTOMATION.dae.specialDuration: [1Attack, isDamaged]` |
| "the benefit doesn't stack with itself" | `AUTOMATION.dae.stackable: noneName` |
| "the effect is suppressed while you are incapacitated" | `AUTOMATION.dae.disableIncapacitated: true` |
| "it has no effect while you have 0 hit points" | `AUTOMATION.dae.disableCondition: "@attributes.hp.value <= 0"` |
| "lasts for 1d4 minutes" | `AUTOMATION.dae.durationExpression: "1d4 * 60"` |
| "attack rolls against the target have advantage" | `CHANGES.Attribute Key: flags.midi-qol.grants.advantage.attack.all`; `CHANGES.Change Type: override`; `CHANGES.Change Phase: initial`; `CHANGES.Value: 1`; `CHANGES.Priority: 20` |

### EFFECT with DAE: when the layer applies

The `AUTOMATION` section holds the DAE settings of this effect. They are stored whether or not DAE is active and act once DAE 14.6.9 is active; MIDI-QOL is not needed for them. Without the `AUTOMATION` section the same block is a complete Base effect.

| Setting | Rule |
| --- | --- |
| Availability | `AUTOMATION.dae` settings are stored with or without DAE and act only while DAE is active. `AUTOMATION.core.overlay` is a core Foundry flag and needs no module. |
| Exact values | `AUTOMATION` stores what is written. There is no `n/a`, and a quoted `"true"` is an error. Leave a setting out to keep DAE's own default. An unknown name or a wrong type is a parse error that names the path. |
| Native effect first | `DETAILS`, `DURATION`, `CHANGES` and the status lists configure the effect and work without DAE. Use `AUTOMATION.dae` only for what they cannot express. |
| Activity settings | Triggered activities, other activities, dialogs and over-time settings belong to an activity's `AUTOMATION.midi-qol.fields`. They are not part of an effect. |
| Validation | Reviewed against DAE 14.6.9, D&D5e 6.0.6 and Foundry 14.370. Parsing does not process durations, conditions, macros or combat turns. |

### EFFECT with DAE: AUTOMATION settings

```yaml
EFFECT:
  # DAE settings and one core flag. Leave AUTOMATION out for a Base effect, and write only the settings the source needs.
  # Values are stored exactly as written: true, false and numbers without quotes; "" is an empty text.
  AUTOMATION:
    dae:
      enableCondition: "[text]" # The effect is removed when this expression is false.
      disableCondition: "[text]" # The effect is disabled while this expression is true.
      durationExpression: "[text]" # Roll expression for the duration in seconds.
      macroRepeat: "[none|startEveryTurn|endEveryTurn|startEndEveryTurn|startEveryTurnAny|endEveryTurnAny|startEndEveryTurnAny]"
      disableIncapacitated: "[true|false]"
      selfTarget: "[true|false]"
      selfTargetAlways: "[true|false]"
      dontApply: "[true|false]"
      stackable: "[noneName|none|multi|count|countDeleteDecrement|noneNameOnly]"
      applicationMode: "[default|dnd5e|legacy]"
      expiryMode: "[default|delete|suppress]"
      specialDuration: [] # DAE special durations, such as [1Attack] or [isDamaged].
    core:
      overlay: "[true|false]"
```

#### EFFECT with DAE values: FIELD REFERENCE (DAE AUTOMATION)

> **Context:** These are DAE's settings on the effect, reviewed against DAE 14.6.9 on D&D5e 6.0.6, plus one core Foundry flag. Paths are relative to `AUTOMATION`. Every setting is optional: leave it out to keep DAE's default. Values are stored exactly as written; there is no `n/a`. The settings are stored without DAE and act only while DAE is active. An effect inside an activity's `APPLIED_EFFECTS` takes the same `AUTOMATION` section.

| Field | Description | Accepted Values |
|---|---|---|
| `dae.enableCondition` | Expression that removes the effect from the actor when it is false. | Text. |
| `dae.disableCondition` | Expression that disables the effect while it is true. | Text. |
| `dae.durationExpression` | Roll expression for the effect's duration in seconds. | Text such as `"1d4 * 60"`. |
| `dae.macroRepeat` | When DAE runs the effect's macros again during combat. The `...Any` values also run while the effect is disabled. | `none`, `startEveryTurn`, `endEveryTurn`, `startEndEveryTurn`, `startEveryTurnAny`, `endEveryTurnAny`, `startEndEveryTurnAny`. |
| `dae.disableIncapacitated` | The effect is disabled while its actor is incapacitated. | `true` or `false`. |
| `dae.selfTarget` | When the Item applies effects to targets, this effect goes to the user of the Item instead. | `true` or `false`. |
| `dae.selfTargetAlways` | This effect goes to the user of the Item whenever the Item is rolled. | `true` or `false`. |
| `dae.dontApply` | DAE and MIDI-QOL skip this effect when they apply the Item's effects. | `true` or `false`. |
| `dae.stackable` | How repeated applications stack. `noneName` and `noneNameOnly` do not stack effects of the same name (and origin, for `noneName`); `none` does not stack by origin; `multi` applies the effect again each time; `count` raises a stack count; `countDeleteDecrement` also lowers it when the effect is deleted. | `noneName`, `noneNameOnly`, `none`, `multi`, `count`, `countDeleteDecrement`. |
| `dae.applicationMode` | How DAE fills the `@` references of the effect. `dnd5e` follows each change's Replacement setting; `legacy` fills every reference from the caster. | `default`, `dnd5e`, `legacy`. |
| `dae.expiryMode` | What happens when the effect expires. | `default` = the world's setting, `delete`, `suppress` = keep it and mark it expired. |
| `dae.specialDuration` | Events that end the effect early. Turn starts, turn ends and rests are covered by the Base field `DURATION.Effect Expiry`. | A list such as `[1Attack]` or `[1Attack, isDamaged]`; `[]` for none. Ids: `1Action`, `Bonus Action`, `Reaction`, `Turn Action`, `1Spell`, `1Attack`, `1Hit`, `1Critical`, `1Fumble`, `1Reaction`, `DamageDealt`, `isAttacked`, `isDamaged`, `isHealed`, `zeroHP`, `isHit`, `isHitCritical`, `isSave`, `isSaveSuccess`, `isSaveFailure`, `isConcentrationSave`, `isConcentrationSaveFail`, `isConcentrationSaveSuccess`, `isCheck`, `isSkill`, `isInitiative`, `isMoved`, `longRest`, `shortRest`, `newDay`. `1Attack` and `1Hit` take an attack type (`1Attack:mwak`, `:rwak`, `:msak`, `:rsak`); `isSave`, `isSaveSuccess`, `isSaveFailure` and `isCheck` take an ability (`isSave.dex`); `isDamaged` takes a damage type (`isDamaged.fire`); `isSkill` takes a skill (`isSkill.ste`). |
| `core.overlay` | Draw the effect's icon as an overlay on the token. | `true` or `false`. |

#### EFFECT with DAE values: MIDI-QOL, DAE and macro keys for CHANGES

These `Attribute Key` values need MIDI-QOL or DAE active in the world; a `macro.*` key runs a macro and is written only when the user asks for one. Prefer a dnd5e key from "Effect keys" when it does the same job, because it also works without MIDI-QOL. Every key here was checked against the flags MIDI-QOL 14.6.11 and DAE 14.6.9 register in a live world (dnd5e 6.0.6, 2026-11-14). Replace every `{token}` with one id from the table before writing the key.

#### EFFECT with DAE values: MIDI-QOL keys: token ids

`{ability}` = str, dex, con, int, wis, cha

`{skillId}` = acr, ani, arc, ath, dec, his, ins, inv, itm, med, nat, per, prc, prf, rel, slt, ste, sur

`{toolId}` = alchemist, art, bagpipes, brewer, calligrapher, card, carpenter, cartographer, chess, cobbler, cook, dice, disg, drum, dulcimer, flute, forg, game, glassblower, herb, horn, jeweler, leatherworker, lute, lyre, mason, music, navg, painter, panflute, pois, potter, shawm, smith, thief, tinker, vehicle, viol, weaver, woodcarver

`{attackType}` = all, heal, msak, mwak, other, rsak, rwak, save, util

`{attackTypeExtended}` = {attackType} + check, skill, tool

`{rollModAttackType}` = {attackType} + feat, spell, tool, weapon

`{damageType}` = acid, all, bludgeoning, cold, fire, force, lightning, midi-none, necrotic, none, piercing, poison, psychic, radiant, slashing, thunder, vitality

#### EFFECT with DAE values: MIDI-QOL keys: single keys

| Name | Key |
|---|---|
| On Update Target | `flags.dae.onUpdateTarget` |
| Activity Over Time | `flags.midi-qol.ActivityOverTime` |
| Create Item | `macro.createItem` |
| Create Item & Run Macro | `macro.createItemRunMacro` |
| Macro to Execute | `macro.execute` |
| Macro for GM to Execute | `macro.execute.GM` |
| Macro to Execute Locally | `macro.execute.local` |
| Execute ItemMacro/DIME | `macro.itemMacro` |
| Execute ItemMacro/DIME as GM | `macro.itemMacro.GM` |
| Execute ItemMacro/DIME Locally | `macro.itemMacro.local` |
| Apply Status Effect | `macro.StatusEffect` |
| Bonus Actions Max | `flags.midi-qol.actions.bonusActionsMax` |
| Bonus Actions Reset | `flags.midi-qol.actions.bonusActionsReset` |
| Reactions Max | `flags.midi-qol.actions.reactionsMax` |
| Reactions Reset | `flags.midi-qol.actions.reactionsReset` |
| Advantage All Ability | `flags.midi-qol.advantage.ability.all` |
| Advantage All | `flags.midi-qol.advantage.all` |
| Advantage (Concentration) | `flags.midi-qol.advantage.concentration` |
| Advantage (Death Save) | `flags.midi-qol.advantage.deathSave` |
| Careful Spells | `flags.midi-qol.carefulSpells` |
| Damage Reroll (Keep Highest) | `flags.midi-qol.damage.reroll-kh` |
| Damage Reroll (Keep Lowest) | `flags.midi-qol.damage.reroll-kl` |
| Disadvantage (All Abilities) | `flags.midi-qol.disadvantage.ability.all` |
| Disadvantage All | `flags.midi-qol.disadvantage.all` |
| Disadvantage (Concentration) | `flags.midi-qol.disadvantage.concentration` |
| Disadvantage (Death Save) | `flags.midi-qol.disadvantage.deathSave` |
| Fail (All Abilities) | `flags.midi-qol.fail.ability.all` |
| Fail (All Actions) | `flags.midi-qol.fail.all` |
| Fail (Material Component) | `flags.midi-qol.fail.spell.material` |
| Fail (Somatic Component) | `flags.midi-qol.fail.spell.somatic` |
| Fail (Vocal Component) | `flags.midi-qol.fail.spell.vocal` |
| Critical Hit Threshold (grants) | `flags.midi-qol.grants.criticalThreshold` |
| Ignore Nearby Foes | `flags.midi-qol.ignoreNearbyFoes` |
| Initiative Advantage | `flags.midi-qol.initiativeAdv` |
| Initiative Disadvantage | `flags.midi-qol.initiativeDisadv` |
| No Advantage (Initiative) | `flags.midi-qol.initiativeNoAdv` |
| No Disadvantage (Initiative) | `flags.midi-qol.initiativeNoDisadv` |
| In Motion | `flags.midi-qol.inMotion` |
| Never Target | `flags.midi-qol.neverTarget` |
| No Advantage (All Rolls) | `flags.midi-qol.noAdvantage.all` |
| No Advantage (Concentration) | `flags.midi-qol.noAdvantage.concentration` |
| No Advantage (Death Save) | `flags.midi-qol.noAdvantage.deathSave` |
| No Disadvantage (All Rolls) | `flags.midi-qol.noDisadvantage.all` |
| No Disadvantage (Concentration) | `flags.midi-qol.noDisadvantage.concentration` |
| No Disadvantage (Death Save) | `flags.midi-qol.noDisadvantage.deathSave` |
| Over Time Effects | `flags.midi-qol.OverTime` |
| Potent Cantrip | `flags.midi-qol.potentCantrip` |
| Sculpt Spells | `flags.midi-qol.sculptSpells` |
| Success (All Ability Rolls) | `flags.midi-qol.success.ability.all` |
| Success (All Rolls) | `flags.midi-qol.success.all` |
| Uncanny Dodge | `flags.midi-qol.uncanny-dodge` |

#### EFFECT with DAE values: MIDI-QOL keys: patterns

**MIDI-QOL:**
- `flags.midi-qol.DR.{damageType}`  -  Damage reduction
- `flags.midi-qol.advantage.attack.{attackType}`
- `flags.midi-qol.advantage.check.{ability}`
- `flags.midi-qol.advantage.save.{ability}`
- `flags.midi-qol.advantage.skill.{skillId|ability}`
- `flags.midi-qol.advantage.tool.{toolId}`
- `flags.midi-qol.critical.{attackType}`
- `flags.midi-qol.disadvantage.attack.{attackType}`
- `flags.midi-qol.disadvantage.check.{ability}`
- `flags.midi-qol.disadvantage.save.{ability}`
- `flags.midi-qol.disadvantage.skill.{skillId|ability}`
- `flags.midi-qol.disadvantage.tool.{toolId}`
- `flags.midi-qol.fail.critical.{attackType}`
- `flags.midi-qol.fumble.{attackType}`
- `flags.midi-qol.grants.advantage.attack.{attackTypeExtended}`
- `flags.midi-qol.grants.advantage.check.{ability}`
- `flags.midi-qol.grants.advantage.save.{ability}`
- `flags.midi-qol.grants.advantage.skill.{skillId|ability}`
- `flags.midi-qol.grants.advantage.tool.{toolId}`
- `flags.midi-qol.grants.attack.bonus.{attackType}`
- `flags.midi-qol.grants.attack.fail.{attackType}`
- `flags.midi-qol.grants.attack.success.{attackType}`
- `flags.midi-qol.grants.bonus.damage.{attackType}`
- `flags.midi-qol.grants.critical.{attackType}`
- `flags.midi-qol.grants.disadvantage.attack.{attackTypeExtended}`
- `flags.midi-qol.grants.disadvantage.check.{ability}`
- `flags.midi-qol.grants.disadvantage.save.{ability}`
- `flags.midi-qol.grants.disadvantage.skill.{skillId|ability}`
- `flags.midi-qol.grants.disadvantage.tool.{toolId}`
- `flags.midi-qol.grants.fumble.{attackType}`
- `flags.midi-qol.grants.max.damage.{attackType}`
- `flags.midi-qol.grants.min.damage.{attackType}`
- `flags.midi-qol.grants.noAdvantage.attack.{attackTypeExtended}`
- `flags.midi-qol.grants.noAdvantage.check.{ability}`
- `flags.midi-qol.grants.noAdvantage.save.{ability}`
- `flags.midi-qol.grants.noAdvantage.skill.{skillId|ability}`
- `flags.midi-qol.grants.noAdvantage.tool.{toolId}`
- `flags.midi-qol.grants.noCritical.{attackType}`
- `flags.midi-qol.grants.noDisadvantage.attack.{attackTypeExtended}`
- `flags.midi-qol.grants.noDisadvantage.check.{ability}`
- `flags.midi-qol.grants.noDisadvantage.save.{ability}`
- `flags.midi-qol.grants.noDisadvantage.skill.{skillId|ability}`
- `flags.midi-qol.grants.noDisadvantage.tool.{toolId}`
- `flags.midi-qol.grants.noFumble.{attackType}`
- `flags.midi-qol.long.{attackType}`
- `flags.midi-qol.magicResistance.{ability|all}`
- `flags.midi-qol.magicVulnerability.{ability|all}`
- `flags.midi-qol.max.damage.{attackType}`
- `flags.midi-qol.min.damage.{attackType}`
- `flags.midi-qol.noAdvantage.attack.{attackType}`
- `flags.midi-qol.noAdvantage.check.{ability}`
- `flags.midi-qol.noAdvantage.save.{ability}`
- `flags.midi-qol.noAdvantage.skill.{skillId|ability}`
- `flags.midi-qol.noAdvantage.tool.{toolId}`
- `flags.midi-qol.noCritical.{attackType}`
- `flags.midi-qol.noDisadvantage.attack.{attackType}`
- `flags.midi-qol.noDisadvantage.check.{ability}`
- `flags.midi-qol.noDisadvantage.save.{ability}`
- `flags.midi-qol.noDisadvantage.skill.{skillId|ability}`
- `flags.midi-qol.noDisadvantage.tool.{toolId}`
- `flags.midi-qol.noFumble.{attackType}`
- `flags.midi-qol.range.{attackType}`
- `flags.midi-qol.rangeOverride.attack.{attackType}`
- `flags.midi-qol.rollModifiers.attack.{rollModAttackType}`
- `flags.midi-qol.rollModifiers.damage.{rollModAttackType}.{damageType}`
- `flags.midi-qol.semiSuperSaver.{ability}`
- `flags.midi-qol.superSaver.{ability}`

**Optional Bonus Keys**  -  Replace `NAME` with a unique identifier. Creates player-prompted optional bonuses.
- `flags.midi-qol.optional.NAME.attack.fail.{attackType}`
- `flags.midi-qol.optional.NAME.attack.{attackType}`
- `flags.midi-qol.optional.NAME.check.fail.{ability}`
- `flags.midi-qol.optional.NAME.check.{ability}`
- `flags.midi-qol.optional.NAME.damage.{attackType}`
- `flags.midi-qol.optional.NAME.save.fail.{ability}`
- `flags.midi-qol.optional.NAME.save.{ability}`
- `flags.midi-qol.optional.NAME.skill.fail.all`
- `flags.midi-qol.optional.NAME.skill.{skillId}`
- `flags.midi-qol.optional.NAME.acOnAttack` (MIDI-QOL 14.6.9 renamed `optional.NAME.ac`; the old key still works with a deprecation warning)
- `flags.midi-qol.optional.NAME.acOnHit`
- `flags.midi-qol.optional.NAME.activation`
- `flags.midi-qol.optional.NAME.count`
- `flags.midi-qol.optional.NAME.countAlt`
- `flags.midi-qol.optional.NAME.criticalDamage`
- `flags.midi-qol.optional.NAME.force`
- `flags.midi-qol.optional.NAME.label`

### EFFECT with DAE: example

The Base example of `EFFECT` with the MIDI-QOL settings added in `AUTOMATION`. They are stored settings: importing the block does not run a MIDI-QOL workflow.

```yaml
EFFECT:
  DETAILS:
    Name: Guarded Stance
    Effect Type: base
    Effect Suspended: false
    Apply Effect to Actor: false
    Status Conditions: []
  EFFECT_DESCRIPTION:
    Effect Description: >-
      <p>Gain +1 AC while this one-minute effect is active. Its incapacitation control is stored by
      DAE.</p>
  DURATION:
    Value: 1
    Units: minutes
  CHANGES:
    - Attribute Key: system.attributes.ac.bonus
      Change Type: add
      Change Phase: initial
      Value: 1
      Priority: 20
  AUTOMATION:
    dae:
      disableIncapacitated: true
```

### EFFECT with DAE: check before you answer

1. `AUTOMATION` in an EFFECT has only `dae` and `core`. `AUTOMATION.midi-qol.fields` belongs to an activity and is a parse error in an effect.
2. `AUTOMATION.dae.specialDuration` is a list, also for one entry (`[1Attack]`). Use only the special duration ids of the field table (`1Attack`, `1Hit`, `isDamaged`, `isAttacked`, `isHit`, `isSave`, `zeroHP` and the others). Free text such as `until damaged` is stored as written and does nothing. There are no turn-based ids: use `DURATION.Effect Expiry` for those.
3. `AUTOMATION.dae.stackable` is one of `noneName`, `noneNameOnly`, `none`, `multi`, `count` or `countDeleteDecrement`. Any other word is a parse error.
4. Use `flags.midi-qol.*` keys only when MIDI-QOL automation is wanted, with every `{token}` replaced (`flags.midi-qol.advantage.save.dex`). Prefer a native `system.*` key or `Status Conditions` when one does the job.
5. An expression is stored as text and is not checked on import. Write one only when you know the DAE or MIDI syntax.
6. Leave `EFFECT_SYSTEM.Rider Activities`, `Rider Effects` and `Rider Items` as `[]` in MIDI YAML. They need dnd5e 6.1, and MIDI-QOL 14.6.11 runs on dnd5e 6.0 only.
