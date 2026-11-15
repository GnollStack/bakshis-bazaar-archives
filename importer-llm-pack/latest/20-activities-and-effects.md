# Importer LLM Pack 14.4.0: activities and effects

Pack 14.4.0 (2026-11-14) for 5e Item Importer 14.4.0 and 5e Activity Importer 14.4.0, on dnd5e 6.0.6 and 6.1.0 with Foundry VTT 14.370. The MIDI-QOL layer matches MIDI-QOL 14.6.11 and DAE 14.6.9 on dnd5e 6.0.6.

This file covers the blocks of 5e Activity Importer, the premium module: activities (the `ACTIVITY_*` root keys, what an Item does when it is used) and Active Effects (the `EFFECT` block, a lasting change or condition). The same blocks go inside a full Item's `Activities` and `effects` lists, or stand alone, separated by `---`, when they are added to an Item that already exists. Every block here is complete without MIDI-QOL; 30-midi-qol-and-dae.md adds the MIDI-QOL layer. 00-start-here.md says which block to choose and how blocks fit into an Item.

## ACTIVITY_* common sections: how they work

Most activity types share the same sections for timing, cost, range, targets and applied effects. They are described once, here. Each activity type's field reference below names the common sections it takes in a comment on its second line, and shows the sections only that type has in full. Write a common section exactly as it is shown in "ACTIVITY_* common sections: field reference". The `EFFECT` block takes none of them.

`ACTIVITY.Description` stores the activity's rich text description separately from chat flavor. Check and Save support `Roll Bonus` and `Visible to All`. `DAMAGE_DATA.Modifiers` is an ordered list of native die modifiers.

`APPLIED_BEHAVIORS` supports `applyActiveEffect`, `difficultTerrain` and, on dnd5e 6.1 or later, `antimagic` (suppresses magic in the area; its `CONFIG` is empty). Use the shown native `CONFIG` keys. `APPLIED_EFFECTS.Effect UUID` references a standalone ActiveEffect, normally in a compendium, and must resolve before import. Native behavior effect UUIDs may also reference embedded effects. Inline applied effects create owned documents; an `Effect UUID` entry links an existing effect and preserves its ownership. Do not put inline `DETAILS` beside `Effect UUID` or `Effect ID`.

`EFFECT_SYSTEM` stores native origin references and Magical. Base effects also support Conditions and Rider Statuses. On dnd5e 6.1 or later an enchantment effect also supports Rider Activities, Rider Effects and Rider Items. `Conditions` on the effect or an individual change accepts native filter JSON as a string, mapping, or list; empty strings/null retain native no-filter states. Filters and formulas are stored without evaluation. Change ID is optional; Replacement is empty, origin, or target. Effect Expiry supports combatStart, roundStart, turnStart, combatEnd, roundEnd, turnEnd, shortRest, longRest, sourceStart, sourceEnd, targetStart, and targetEnd.

| Configuration | Authoring rule |
| --- | --- |
| Name and identity | Supply a meaningful ACTIVITY.Name. Optional Activity ID must be exactly 16 alphanumeric characters; omit it to allocate an ID. An identifier/name is not a UUID. Omit Sort for append ordering; an authored 0 is explicit. |
| Local timing | Set Override Activation and Override Duration true for authored local timing, false for intentional inheritance. False retains local values; it does not erase them. |
| Range and target | Set Override Range/Override Target from intent. Their false state retains local data. Self range is a range choice, not an override toggle. |
| Consumption | itemUses with an omitted Target Item/Material consumes the parent Item's uses. Supply a real eligible item reference for another Item. material needs an eligible material target; activityUses always consumes this activity's uses. Attribute, hit-die and slot targets require the matching resource context. |
| Scaling | Activity SCALING only controls Allow Scaling/Max Scaling. Resource scaling belongs on each CONSUMPTION entry; damage/healing scaling belongs inside DAMAGE_DATA. |
| Damage/healing | Use all ten DAMAGE_DATA fields when specifying that value explicitly. Do not combine populated formula shorthand with DAMAGE_DATA for the same part. An inactive Custom Formula or Scaling Formula can still be stored. |
| Applied effects | APPLIED_EFFECTS creates and links effects for this activity. Apply Effect to Actor is the passive transfer flag, not a command to apply the effect to the activity's target; use false for ordinary applied effects. |
| Effect timing | Use DURATION.Value/Units on effects. Leave generated start fields omitted for a newly authored effect; Start:null and populated start fields are mutually exclusive. |

## ACTIVITY_* common sections: field reference

A placeholder such as `"[true|false]"` lists the choices; it is not a value to copy. Write only the sections and fields the source needs.

```yaml
ACTIVITY_SAVE: # the same sections under every ACTIVITY_* root key that takes them

  ACTIVITY:
    Description: "" # Full activity description, independent of Chat Description.
    Activity ID: n/a
    Sort: 0
    Name: "[text]"
    Icon: "[filepath|n/a]"

  CHAT_FLAVOR:
    Chat Description: |
      [multiline text content]

  BEHAVIOR:
    Consume Spell Slot: "[true|false]"
    Measured Template Prompt: "[true|false]"

  VISIBILITY:
    Require Magic: "[true|false]"
    Require Identification: "[true|false]"
    Require Attunement: "[true|false]"
    Level Limit Minimum: "[integer|n/a]"
    Level Limit Maximum: "[integer|n/a]"
    Class Identifier: "[text|n/a]"

  ACTIVATION:
    Override Activation: "[true|false]"
    Activation Type: "[action|bonus|reaction|minute|hour|day|longRest|shortRest|encounter|turnStart|turnEnd|legendary|mythic|lair|crew|special|None|n/a]"
    # (Required only if Activation Type is minute,hour,day,crew)
    Activation Cost: "[integer|n/a]"
    Condition: "[text|n/a]"

  DURATION:
    Effect Expiry: null # Also supports source/target turns and shortRest/longRest.
    Override Duration: "[true|false]"
    Duration Time: "[inst|spec|turn|round|minute|hour|day|month|year|disp|dstr|perm]"
    # (Required only if Duration Time is spec)
    Special Duration: "[text|n/a]"
    # (Required only if Duration Time is turn,round,minute,hour,day,month,year)
    Duration Amount: "[formula|number|n/a]"
    Concentration: "[true|false]"

  CONSUMPTION:
    # (Optional, repeatable)
    - Consumption Type: "[activityUses|itemUses|material|hitDice|spellSlots|attribute]"
      # (Optional for parent itemUses; another item/material requires a real eligible target)
      Target Item/Material: "[text|n/a]" # (UUID or ID of item to consume)
      # (Required only if Consumption Type is hitDice)
      Target Die: "[smallest|d4|d6|d8|d10|d12|largest]"
      # (Required only if Consumption Type is spellSlots)
      Target Level: "[1|2|3|4|5|6|7|8|9]"
      # (Required only if Consumption Type is attribute)
      Target Attribute: "[text|n/a]" # (Path e.g. attributes.exhaustion)
      Amount: "[formula|number|n/a]" # (Negative numbers restore)
      Scaling Mode: "[none|amount|level]" # level only for spellSlots.
      Scaling Formula: "[formula|number|n/a]"

  SCALING:
    # (General Activity Scaling)
    Allow Scaling: "[true|false]"
    Max Scaling: "[formula|number|n/a]"

  USAGE:
    Uses Spent: "[integer]"
    Uses Max: "[formula|number|n/a]"

  RECOVERY:
    # (Optional, repeatable. Only relevant if Uses Max > 0)
    - Period: "[lr|sr|day|dawn|dusk|recharge|initiative|turnStart|turnEnd|turn]"
      Type: "[recoverAll|loseAll|formula]"
      Formula: "[text|n/a]"

  RANGE:
    Override Range: "[true|false]"
    # (Used when Override Range is true; preserve local values when false)
    Range Units: "[self|touch|spec|any|ft|mi|m|km]"
    # (Distance units: ft, mi, m, km; preserve inactive values for other units)
    Range Value: "[formula|number|n/a]"
    Special Range: "[text|n/a]"

  TARGETS:
    Override Target: "[true|false]"
    Template Count: "[formula|number|n/a]"
    Template Contiguous: "[true|false]"
    Template Stationary: "[true|false]"
    Target Type: "[self|ally|enemy|creature|object|space|creatureOrObject|any|willing|n/a]"
    Target Amount: "[formula|number|n/a]"
    Special Targeting: "[text|n/a]"
    Choose Targets: "[true|false]"
    Template Type: "[cone|cube|cylinder|line|radius|ring|sphere|circle|square|wall|n/a]"
    Template Measurement: "[formula|number|n/a]"
    # (Used when Template Type is line, wall or ring)
    Template Dimensions: "[formula|number|n/a]" # (Width/Thickness)
    # (Used when Template Type is cylinder, wall or ring)
    Template Height: "[formula|number|n/a]"
    Template Unit: "[ft|m]"

  APPLIED_EFFECTS:
    # (Optional, repeatable)
    - DETAILS:
        Effect ID: n/a
        Icon: n/a
        Effect Type: base
        Origin: n/a
        Sort: 0
        Show Icon: "[0|1|2]"
        Name: "[text|n/a]"
        Icon Tint Color: "[text|n/a]" # (a Hex code color format e.g. #ff0000)
        Effect Suspended: "[true|false]"
        Apply Effect to Actor: "[true|false]"
        Status Conditions: []

      EFFECT_DESCRIPTION:
        Effect Description: |
          [multiline text content]

      DURATION:
        Value: "[integer|null|n/a]"
        Units: "[seconds|rounds|turns|minutes|hours|days|months|years]"
        Effect Expiry: null
        Effect Expired: "[true|false]"
        Combat Encounter: n/a
        Combatant: n/a
        Initiative: "[number|null|n/a]"
        Effect Start Time: "[integer|n/a]"
        Effect Start (combat) Rounds: "[integer|null|n/a]"
        Effect Start (combat) Turns: "[integer|null|n/a]"

      APPLICATION:
        Level Minimum: "[integer|n/a]"
        Level Maximum: "[integer|n/a]"

      EFFECT_SYSTEM:
        Origin:
          Activity: null
          Actor: null
          Behavior: null
          Effect: null
          Item: null
          Message: null
          Profile: null
        Magical: false
        Conditions: {}
        Rider Statuses: []

      CHANGES:
        # (Optional, repeatable)
        - Attribute Key: "[text|n/a]" # (Common keys found here: https://github.com/foundryvtt/dnd5e/wiki/Active-Effect-Guide)
          Change ID: n/a
          Conditions: {}
          Replacement: "" # Empty, origin, or target.
          Change Type: "[custom|multiply|add|subtract|downgrade|upgrade|override]"
          Change Phase: "[initial|final]"
          Value: 0 # Any YAML scalar, null, list, or mapping; preserve its native type.
          Priority: "[number|n/a]"

    # Alternative to an inline definition: reference a real existing effect.
    - Effect UUID: "[text|n/a]"
      APPLICATION:
        Level Minimum: null
        Level Maximum: null

  # Optional native area behaviors; omit unused entries.
  # On dnd5e 6.1 or later Type may also be antimagic, which takes an empty CONFIG: {}.
  APPLIED_BEHAVIORS:
    - Behavior ID: n/a
      Name: ""
      Type: applyActiveEffect
      Level Minimum: null
      Level Maximum: null
      CONFIG:
        effects: [] # Real ActiveEffect UUIDs; no effects are copied by this reference.
        sizes: []
        types: []
    - Behavior ID: n/a
      Name: ""
      Type: difficultTerrain
      Level Minimum: null
      Level Maximum: null
      CONFIG:
        types: []
```

## ACTIVITY_* common sections: from source text to YAML

Find the wording closest to the source and write the fields shown, inside the activity block. List sections (`CONSUMPTION`, `RECOVERY`, `APPLIED_EFFECTS`, `APPLIED_BEHAVIORS`) take one `-` entry per item.

| The source says | Write |
| --- | --- |
| "as an action", "as a bonus action", "as a reaction" | `ACTIVATION.Override Activation: true`; `ACTIVATION.Activation Type: action` (or `bonus`, `reaction`). A reaction's trigger goes in `ACTIVATION.Condition` |
| "takes 10 minutes to use" | `ACTIVATION.Override Activation: true`; `ACTIVATION.Activation Type: minute`; `ACTIVATION.Activation Cost: 10` |
| "once per long rest", "three times per day" (a limit on this activity alone) | `USAGE.Uses Max: 3`; `USAGE.Uses Spent: 0`; one `RECOVERY` entry with `Period: lr` (or `sr`, `day`, `dawn`, `dusk`) and `Type: recoverAll`; one `CONSUMPTION` entry with `Consumption Type: activityUses` and `Amount: 1` |
| "(Recharge 5-6)" | `USAGE.Uses Max: 1`; `USAGE.Uses Spent: 0`; one `RECOVERY` entry with `Period: recharge`, `Type: recoverAll` and `Formula: "5"` (the lowest d6 result that recharges); one `CONSUMPTION` entry with `Consumption Type: activityUses` and `Amount: 1` |
| "expend 1 charge", "costs 2 charges" (charges of the parent Item) | one `CONSUMPTION` entry with `Consumption Type: itemUses` and `Amount: 2`. Leave `Target Item/Material` out. The parent Item must have uses of its own |
| "expend a 3rd-level spell slot" (on an Item that is not a spell) | one `CONSUMPTION` entry with `Consumption Type: spellSlots`, `Target Level: 3` and `Amount: 1` |
| "range 60 feet", "touch", "self" | `RANGE.Override Range: true`; `RANGE.Range Units: ft`; `RANGE.Range Value: 60`. For touch or self write `Range Units: touch` or `self` and no value |
| "one creature", "up to three creatures" | `TARGETS.Override Target: true`; `TARGETS.Target Type: creature`; `TARGETS.Target Amount: 3` |
| "each creature in a 20-foot-radius sphere", "a 15-foot cone", "a line 60 feet long and 5 feet wide" | `TARGETS.Override Target: true`; `TARGETS.Target Type: creature`; `TARGETS.Template Type: sphere`; `TARGETS.Template Measurement: 20`; `TARGETS.Template Unit: ft`. A line, wall or ring also takes `TARGETS.Template Dimensions` (width or thickness). A cylinder, wall or ring takes `TARGETS.Template Height` |
| "instantaneous", "for 1 minute", "concentration, up to 10 minutes", "until dispelled" | `DURATION.Override Duration: true`; `DURATION.Duration Time: minute`; `DURATION.Duration Amount: 10`; `DURATION.Concentration: true`. Instantaneous is `Duration Time: inst` with no amount. Until dispelled is `Duration Time: disp` |
| "requires attunement", "while you are attuned to this item" | `VISIBILITY.Require Attunement: true` |
| "starting at 5th level", "when you reach 11th level in this class" | `VISIBILITY.Level Limit Minimum: 5`. Add `VISIBILITY.Class Identifier: fighter` when the level is a class level |
| "the target is poisoned for 1 minute", "has a -2 penalty to AC until the end of your next turn" | one `APPLIED_EFFECTS` entry with `DETAILS.Name`, `DETAILS.Apply Effect to Actor: false`, `DETAILS.Status Conditions: [poisoned]`, `DURATION.Value: 1` and `DURATION.Units: minutes`. Number changes go in that entry's `CHANGES` list |
| "the area is difficult terrain" | one `APPLIED_BEHAVIORS` entry with `Type: difficultTerrain` and `CONFIG.types: [ground]`. The activity needs an area in `TARGETS` |
| "magic is suppressed in the area" (an antimagic field) | one `APPLIED_BEHAVIORS` entry with `Type: antimagic` and `CONFIG: {}`. This needs dnd5e 6.1 or later; dnd5e 6.0 rejects it |

## ACTIVITY_* common sections: check before you answer

These checks apply to every `ACTIVITY_*` block.

1. Copy section and field names exactly as the Complete field reference spells them, including capitals, spaces and underscores. The importer skips a name it does not know: its value is not imported, and the name is shown as a warning in the preview.
2. Write only the sections and fields the source needs. Do not copy the whole reference, and leave no `[placeholder]` value and no `#` comment in the answer.
3. Write formulas as quoted strings (`"1d6 + @mod"`). Write `true`, `false` and plain numbers without quotes.
4. `Override Activation`, `Override Duration`, `Override Range` and `Override Target` set to `false` make the activity inherit that part from its parent Item; the values written in that section are stored but not used. Write `true`, or leave the toggle out, when your values should apply.
5. Never invent a UUID or a 16-character ID. Leave `Activity ID`, `Effect ID` and `Behavior ID` out so the importer allocates them, and write a UUID only when the user supplied it.

## ACTIVITY_* common sections: values

### ACTIVITY_* common section values: ACTIVITY

| Field | Description | Accepted Values |
|---|---|---|
| `Name` | Display name. If omitted, defaults to the name of the activity type (for example "Attack"). | Any free text. |
| `Icon` | Custom icon file path. | A valid Foundry file path, or `n/a` for default. |

### ACTIVITY_* common section values: CHAT FLAVOR

| Field | Description | Accepted Values |
|---|---|---|
| `Chat Description` | Flavor text in the usage chat card. Supports enricher markup (see ENRICHER REFERENCE). Multiline. | Any free text including enrichers. |

### ACTIVITY_* common section values: BEHAVIOR

| Field | Description | Accepted Values |
|---|---|---|
| `Measured Template Prompt` | Auto-prompt template placement when activity has an area of effect. | `true` = prompt. `false` = player places manually from chat card. |

### ACTIVITY_* common section values: VISIBILITY

| Field | Description | Accepted Values |
|---|---|---|
| `Require Magic` | If `true`, unusable when magic is suppressed (anti-magic field, untuned item). | `true` or `false`. |
| `Require Identification` | If `true`, hidden until parent item is identified. | `true` or `false`. |
| `Require Attunement` | If `true`, unusable until parent item is attuned. | `true` or `false`. |
| `Level Limit Minimum` | Minimum character/class level for visibility. | A non-negative integer, or `n/a`. |
| `Level Limit Maximum` | Maximum character/class level for visibility. | A non-negative integer, or `n/a`. |
| `Class Identifier` | Class whose level drives Level Limits instead of overall character level. | Class identifier string (e.g. `fighter`), or `n/a`. |

### ACTIVITY_* common section values: ACTIVATION

| Field | Description | Accepted Values |
|---|---|---|
| `Override Activation` | Use this activity's activation fields instead of inheriting the parent item's timing. | `true` (use local values) or `false` (inherit); missing is resolved by importer/native defaults. |
| `Activation Type` | Action economy required. | `action`, `bonus`, `reaction`, `minute`, `hour`, `day`, `longRest`, `shortRest`, `encounter`, `turnStart`, `turnEnd`, `legendary`, `mythic`, `lair`, `crew`, `special`, `None`, `n/a`. |
| `Activation Cost` | Numeric quantity. **Required only for `minute`, `hour`, `day`, `crew`.** | A non-negative integer, or `n/a`. |
| `Condition` | Special activation condition text (e.g. reaction trigger). | Descriptive text, or `n/a`. |

### ACTIVITY_* common section values: DURATION

| Field | Description | Accepted Values |
|---|---|---|
| `Override Duration` | Use this activity's duration fields instead of inheriting the parent item's duration. | `true` (use local values) or `false` (inherit); missing is resolved by importer/native defaults. |
| `Duration Time` | How long the effect lasts. | `inst` = instantaneous. `spec` = special (see Special Duration). `turn`, `round`, `minute`, `hour`, `day`, `month`, `year` = time-based (requires Duration Amount). `disp` = until dispelled. `dstr` = until dispelled or triggered. `perm` = permanent. |
| `Special Duration` | Non-standard duration text. **Required only when Duration Time is `spec`.** | Descriptive text, or `n/a`. |
| `Duration Amount` | Number of time units. **Required only for time-based Duration Time values.** | Formula string or finite numeric shorthand, including decimals, or `n/a`. |
| `Concentration` | Whether the effect requires concentration. | `true` or `false`. |

### ACTIVITY_* common section values: CONSUMPTION

Optional and repeatable. Defines resources consumed per use.

| Field | Description | Accepted Values |
|---|---|---|
| `Consumption Type` | Resource category. | `activityUses` = this activity's use pool. `itemUses` = parent item's use pool. `material` = inventory item by UUID. `hitDice` = actor's hit dice. `spellSlots` = spell slot. `attribute` = numeric actor attribute. |
| `Target Item/Material` | UUID/ID of item. Optional for parent itemUses; another item/material needs an eligible reference. | Foundry UUID/ID, or `n/a`. |
| `Target Die` | Hit die size. **Required only for `hitDice`.** | `smallest`, `d4`, `d6`, `d8`, `d10`, `d12`, `largest`. |
| `Target Level` | Spell slot level. **Required only for `spellSlots`.** | `1`-`9`. |
| `Target Attribute` | Actor data path. **Required only for `attribute`.** | Dot-notation path (e.g. `attributes.exhaustion`), or `n/a`. |
| `Amount` | Resource consumption formula; negative resolved values restore resources. | Formula string or finite numeric shorthand, including decimals, or `n/a`. |
| `Scaling Mode` | How the amount grows when the activity is scaled. | `none` or `amount`; `level` only for `spellSlots`. The older `Scale Consumption` boolean is still accepted as input. |
| `Scaling Formula` | Resource scaling formula. Used by amount/level scaling; retained when Scaling Mode is none. | Formula string or numeric shorthand, or `n/a`. |

### ACTIVITY_* common section values: SCALING

| Field | Description | Accepted Values |
|---|---|---|
| `Allow Scaling` | Whether scaling is enabled. | `true` or `false`. |
| `Max Scaling` | Maximum scaling steps. | Integer, formula, or `n/a` for unlimited. |

### ACTIVITY_* common section values: USAGE

| Field | Description | Accepted Values |
|---|---|---|
| `Uses Spent` | Uses already expended from this activity's pool. | Non-negative integer. |
| `Uses Max` | Maximum uses. `0` = unlimited. | Non-negative integer or formula. |

### ACTIVITY_* common section values: RECOVERY

Optional and repeatable. Only relevant when Uses Max > 0.

| Field | Description | Accepted Values |
|---|---|---|
| `Period` | Recovery trigger. | `lr` = long rest. `sr` = short rest. `day` = daily. `dawn` = at dawn. `dusk` = at dusk. `recharge` = recharge roll at start of turn. `initiative` = on initiative roll. `turnStart` = start of turn. `turnEnd` = end of turn. `turn` = each turn. |
| `Type` | Recovery behavior. | `recoverAll` = restore all. `loseAll` = lose all. `formula` = custom amount. |
| `Formula` | Recovery formula. **Only when Type is `formula`.** | Roll formula (e.g. `1d4`, `@prof`), or `n/a`. |

### ACTIVITY_* common section values: RANGE

| Field | Description | Accepted Values |
|---|---|---|
| `Override Range` | Whether local range is used. False inherits range while retaining stored local values. | `true` or `false`; legacy `Range` is accepted input only. |
| `Range Units` | Unit of measurement. **Used when Override Range is `true`; stored when false.** | `self` = caster only. `touch` = adjacent. `spec` = special (see Special Range). `any` = unlimited range. `ft`, `mi`, `m`, `km` = distance (requires Range Value). |
| `Range Value` | Distance value. **Used for `ft`, `mi`, `m`, `km`; stored for other units.** | Formula string or finite numeric shorthand, including decimals, or `n/a`. |
| `Special Range` | Non-standard range text. | Descriptive text, or `n/a`. |

### ACTIVITY_* common section values: TARGETS

| Field | Description | Accepted Values |
|---|---|---|
| `Target Type` | What can be targeted. | `self`, `ally`, `enemy`, `creature`, `object`, `space`, `creatureOrObject`, `any`, `willing`, `n/a`. |
| `Target Amount` | Number of targets. | Formula string or number, or `n/a`. |
| `Special Targeting` | Non-standard targeting notes. | Descriptive text, or `n/a`. |
| `Choose Targets` | Whether user selects targets before resolving. | `true` or `false`. |
| `Template Type` | Area-of-effect shape. `radius` is an emanation around the user. | `cone`, `cube`, `cylinder`, `line`, `radius`, `ring`, `sphere`, `circle`, `square`, `wall`, `n/a`. |
| `Template Measurement` | Primary template size (radius, length, or side). | Formula string or number, or `n/a`. |
| `Template Dimensions` | Width of a `line`; thickness of a `wall` or `ring`. | Formula string or number, or `n/a`. |
| `Template Height` | Vertical height of a `cylinder`, `wall` or `ring`. | Formula string or number, or `n/a`. |
| `Template Unit` | Template measurement unit. | `ft`, `m`. |

## Roll formulas: data you can use in any formula field

| Variable | Description | Example |
|---|---|---|
| `@mod` | Ability modifier for the activity's selected ability. | `3` |
| `@prof` | Proficiency bonus (respects Proficiency Dice setting). | `3` or `1d6` |
| `@abilities.{abl}.mod` | Ability modifier. Replace `{abl}` with `str`, `dex`, `con`, `int`, `wis`, `cha`. | `3` |
| `@abilities.{abl}.dc` | Ability DC: `8 + prof + modifier`. | `15` |
| `@attributes.spell.dc` | Spell save DC. | `15` |
| `@attributes.spell.mod` | Spellcasting ability modifier. | `4` |
| `@attributes.spell.attack` | Spell attack bonus. | `7` |
| `@details.level` | Overall character level. | `10` |
| `@details.cr` | Challenge Rating (NPCs/monsters). | `5` |
| `@classes.{id}.levels` | Specific class level (e.g. `@classes.rogue.levels`). | `5` |
| `@item.level` | Spell level of parent item. | `3` |
| `@scaling` | Current scaling level (base = 1). | `1` |
| `@scaling.increase` | Steps above baseline (base = 0). | `0` |
| `@scale.{parent}.{scale}` | Scale Value advancement (e.g. `@scale.rogue.sneak-attack`). | `3d6` |
| `@consumed.hd` | Formula for hit dice consumed by this activity. | `2d6` |

**Syntax:** `NdX` for dice, `+`, `-`, `*`, `/`, `()` for arithmetic. Formulas resolve using the item **owner's** stats at roll time.

## ACTIVITY_ATTACK: Attack activity

- **Root key:** `ACTIVITY_ATTACK`
- **When to use:** Use it when the source has the user roll to hit a target: "Melee Weapon Attack: +5 to hit", "make a ranged spell attack", "Hit: 1d8 + 3 slashing damage". The attack roll and the damage dealt on a hit are both stored in this one activity.
- **Smallest working block:** `ACTIVITY.Name` (the name shown on the sheet and in chat), `ATTACK.Attack Type` (`melee` or `ranged`), `ATTACK.Attack Class` (`weapon`, `spell` or `unarmed`), `ATTACK_DAMAGE.Include Base Damage` (`true` on a weapon so the weapon's own damage is rolled; `false` on any other Item, together with one `ATTACK_DAMAGE.DAMAGE_PARTS` entry). Leave out `ATTACK_DETAILS.Attack Ability` to use the Item's normal ability.
- **Parent Item:** Usually a weapon Item. `Include Base Damage: true` needs the weapon's base damage to be set on the Item already; the system then adds the weapon dice, the ability modifier and the weapon's magic bonus with no further fields. Spell and feat Items (spell attacks, monster features) have no base damage: write `Include Base Damage: false` and put all damage in `DAMAGE_PARTS`.

**Use another block when:**

- The target rolls a saving throw and the user rolls nothing to hit: use ACTIVITY_SAVE.
- Damage that is dealt with no attack roll and no saving throw: use ACTIVITY_DAMAGE.
- A hit that also forces a saving throw ("on a hit, the target must succeed on a DC 13 Constitution saving throw or be poisoned"): write the hit as ACTIVITY_ATTACK and the saving throw as a second block, ACTIVITY_SAVE.
- A grapple, shove or other contest decided by an ability check: use ACTIVITY_CHECK.
- A weapon's own +1 magic bonus or a passive bonus to attack rolls: that is set on the parent Item or in a passive EFFECT, not in this activity.

### ACTIVITY_ATTACK: from source text to YAML

Find the wording closest to the source and write the fields shown. A path such as `ACTIVATION.Activation Type` means the `Activation Type` key inside the `ACTIVATION` section of `ACTIVITY_ATTACK`. List sections take one `-` entry per item. Leave out every section the source does not mention. The rows for the common sections (activation, uses and charges, range, targets, areas, duration, applied effects) are in "ACTIVITY_* common sections: from source text to YAML".

| The source says | Write |
| --- | --- |
| "Melee Weapon Attack" or "Ranged Weapon Attack" | `ATTACK.Attack Type: melee` (or `ranged`); `ATTACK.Attack Class: weapon` |
| "make a ranged spell attack" | `ATTACK.Attack Type: ranged`; `ATTACK.Attack Class: spell`; `ATTACK_DETAILS.Attack Ability: spellcasting` |
| "you can use your Wisdom instead of Strength for the attack and damage rolls" | `ATTACK_DETAILS.Attack Ability: wis` |
| "+8 to hit" on a trap, hazard or other source with one fixed bonus | `ATTACK_DETAILS.To Hit Bonus: "8"`; `ATTACK_DETAILS.Flat To Hit: true` |
| "Hit: the weapon's damage plus 1d6 fire damage" | `ATTACK_DAMAGE.Include Base Damage: true`; one `ATTACK_DAMAGE.DAMAGE_PARTS` entry with `DAMAGE_DATA.Dice Count: 1`; `DAMAGE_DATA.Die Denomination: 6`; `DAMAGE_DATA.Damage Types: [fire]` |
| "Hit: 2d10 fire damage" on a spell or feature | `ATTACK_DAMAGE.Include Base Damage: false`; one `ATTACK_DAMAGE.DAMAGE_PARTS` entry with `DAMAGE_DATA.Dice Count: 2`; `DAMAGE_DATA.Die Denomination: 10`; `DAMAGE_DATA.Damage Types: [fire]` |
| "scores a critical hit on a roll of 19 or 20" | `ATTACK_DETAILS.Critical Threshold: 19` |
| "on a critical hit, the target takes an extra 2d6 damage" | `ATTACK_DAMAGE.Extra Critical Damage: "2d6"` |
| "the damage increases by 1d10 for each slot level above 1st", or a cantrip that adds a die at 5th, 11th and 17th level | in that damage part: `DAMAGE_DATA.Scaling Mode: whole`; `DAMAGE_DATA.Scaling Dice Count: 1` |

### ACTIVITY_ATTACK: field reference

The sections only `ACTIVITY_ATTACK` has, in full, and the common sections it takes, by name. A placeholder such as `"[true|false]"` lists the choices; it is not a value to copy. Write only what the source needs.

```yaml
ACTIVITY_ATTACK:
  # Also takes these common sections, written as in "ACTIVITY_* common sections": ACTIVITY, CHAT_FLAVOR, BEHAVIOR, VISIBILITY, ACTIVATION, DURATION, SCALING, USAGE, RECOVERY, RANGE, TARGETS, APPLIED_EFFECTS, APPLIED_BEHAVIORS

  ATTACK:
    Attack Type: "[melee|ranged]"
    Attack Class: "[Default (weapon)|weapon|spell|unarmed]"

  CONSUMPTION:
    # (Optional, repeatable list)
    - Consumption Type: "[activityUses|itemUses|material|hitDice|spellSlots|attribute]"
      # (Optional for parent itemUses; another item/material requires a real eligible target)
      Target Item/Material: "[text|n/a]" # (UUID or ID of item to consume)
      # (Required only if Consumption Type is hitDice)
      Target Die: "[smallest|d4|d6|d8|d10|d12|largest]"
      # (Required only if Consumption Type is spellSlots)
      Target Level: "[1|2|3|4|5|6|7|8|9]"
      # (Required only if Consumption Type is attribute)
      Target Attribute: "[text|n/a]" # (Path e.g. attributes.exhaustion)
      Amount: "[formula|number|n/a]" # (Negative numbers restore)
      Scaling Mode: "[none|amount|level]" # level only for spellSlots.
      Scaling Formula: "[formula|number|n/a]"

  ATTACK_DETAILS:
    Attack Ability: "[default|none|spellcasting|str|dex|con|int|wis|cha]"
    To Hit Bonus: "[text|integer|n/a]"
    Flat To Hit: "[true|false]"
    Critical Threshold: "[integer|n/a]" # Omit to defer to actor/system critical threshold; 20 is a common authored value.

  ATTACK_DAMAGE:
    Include Base Damage: "[true|false]"
    Extra Critical Damage: "[text|integer|n/a]"
    DAMAGE_PARTS:
      - DAMAGE_DATA:
          Dice Count: "[integer|n/a]"
          Die Denomination: 6
          Bonus: "[text|n/a]"
          Damage Types: [fire]
          Modifiers: [] # Ordered dice modifiers, such as rerolls or minimum results.
          Custom Enabled: false
          Custom Formula: "[text|n/a]"
          Scaling Mode: "[none|whole|half]"
          Scaling Dice Count: "[integer|n/a]"
          Scaling Formula: "[formula|number|n/a]"
```

### ACTIVITY_ATTACK: rules

Use on a compatible parent Item. Attack Ability chooses the attack ability; Flat To Hit controls replacement of the normal attack modifier. For weapon attacks, Include Base Damage:true includes the weapon base; extra DAMAGE_PARTS are additional damage. Do not add the weapon's magic or ability bonus a second time. Critical thresholds depend on actor/system context when omitted.

### ACTIVITY_ATTACK: example

Make one Strength-based weapon attack using the parent weapon's base damage and add 1d6 fire damage. The parent must be a weapon with appropriate base damage.

Use on a weapon Item.

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
```

### ACTIVITY_ATTACK: check before you answer

These checks are for `ACTIVITY_ATTACK`. The checks for every activity are in "ACTIVITY_* common sections: check before you answer".

1. Put every extra damage part under `ATTACK_DAMAGE.DAMAGE_PARTS` as one `- DAMAGE_DATA:` mapping. A top-level `DAMAGE_PARTS` or `DAMAGE` section in an Attack block is skipped and shown as a warning in the preview.
2. With `Include Base Damage: true` on a weapon, do not repeat the weapon's own dice, its ability modifier or its magic bonus in `DAMAGE_PARTS` or `To Hit Bonus`. List only the damage that is extra.
3. `Attack Type` is only `melee` or `ranged`. `Attack Class` is only `weapon`, `spell` or `unarmed`. A melee spell attack is `Attack Type: melee` with `Attack Class: spell`. The values are case-sensitive; a wrong value gives a warning and is replaced by `melee` or `weapon`.
4. Write `Flat To Hit: true` only when the source gives one fixed to-hit number that ignores the user's ability and proficiency. Then `To Hit Bonus` holds the whole number. In every other case `To Hit Bonus` is only an extra bonus on top of ability and proficiency.
5. Inside `DAMAGE_DATA`: `Die Denomination` is the number of sides (`6`, not `d6`), `Dice Count` is a whole number and `Damage Types` is a list (`[fire]`). Copy the other fields from the example and change only what the source states.
6. Damage scaling (`Scaling Mode: whole` or `half`) only takes effect when the parent is a spell Item or the activity has `SCALING.Allow Scaling: true`.
7. An Attack activity has no saving throw fields. A `SAVE_DETAILS` section in an Attack block is ignored. A rider that needs a saving throw is a separate ACTIVITY_SAVE block after `---`.

## ACTIVITY_SAVE: Save activity

- **Root key:** `ACTIVITY_SAVE`
- **When to use:** Use it when the source makes the target roll a saving throw against a DC: "must succeed on a DC 15 Dexterity saving throw", "makes a Wisdom saving throw or is frightened", "half as much damage on a successful one". The user makes no attack roll.
- **Smallest working block:** `ACTIVITY.Name`, `SAVE_DETAILS.Challenge Ability` (the ability the target rolls), `SAVE_DETAILS.DC Calculation` (how the DC is set), `SAVE_DETAILS.DC Formula` (only with `DC Calculation: custom`), `SAVE_DAMAGE.Damage on Save` (what a successful save does to the damage; `none` when there is no damage). Add `SAVE_DAMAGE.DAMAGE_PARTS` only when the save deals damage.
- **Parent Item:** Any Item that can hold activities: spell, feat, weapon, equipment, consumable or tool. The parent needs nothing special. `DC Calculation: spellcasting` uses the owner's spellcasting ability, so it fits spell Items and spell-like features.

**Use another block when:**

- The user rolls to hit against Armor Class: use ACTIVITY_ATTACK.
- The roll is an ability check, skill check or tool check ("DC 15 Strength (Athletics) check"): use ACTIVITY_CHECK.
- Damage with no saving throw and no attack roll: use ACTIVITY_DAMAGE.
- A weapon hit followed by a saving throw: write ACTIVITY_ATTACK for the hit and this ACTIVITY_SAVE as a second block.
- A bonus to the owner's own saving throws ("+1 to all saving throws"): that is a passive EFFECT, not a Save activity.

### ACTIVITY_SAVE: from source text to YAML

Find the wording closest to the source and write the fields shown. A path such as `ACTIVATION.Activation Type` means the `Activation Type` key inside the `ACTIVATION` section of `ACTIVITY_SAVE`. List sections take one `-` entry per item. Leave out every section the source does not mention. The rows for the common sections (activation, uses and charges, range, targets, areas, duration, applied effects) are in "ACTIVITY_* common sections: from source text to YAML".

| The source says | Write |
| --- | --- |
| "must make a Dexterity saving throw" | `SAVE_DETAILS.Challenge Ability: dex` |
| "DC 15" given as a fixed number | `SAVE_DETAILS.DC Calculation: custom`; `SAVE_DETAILS.DC Formula: "15"` |
| "against your spell save DC" | `SAVE_DETAILS.DC Calculation: spellcasting` |
| "DC = 8 + your proficiency bonus + your Constitution modifier" | `SAVE_DETAILS.DC Calculation: con` |
| "a Strength or Dexterity saving throw (the target chooses)" | `SAVE_DETAILS.Challenge Ability: "str, dex"` |
| "8d6 fire damage on a failed save, or half as much on a successful one" | `SAVE_DAMAGE.Damage on Save: half`; one `SAVE_DAMAGE.DAMAGE_PARTS` entry with `DAMAGE_DATA.Dice Count: 8`; `DAMAGE_DATA.Die Denomination: 6`; `DAMAGE_DATA.Damage Types: [fire]` |
| "takes 3d8 necrotic damage on a failed save" and nothing on a success | `SAVE_DAMAGE.Damage on Save: none`; one `SAVE_DAMAGE.DAMAGE_PARTS` entry with `DAMAGE_DATA.Dice Count: 3`; `DAMAGE_DATA.Die Denomination: 8`; `DAMAGE_DATA.Damage Types: [necrotic]` |
| "the damage increases by 1d6 for each slot level above 3rd" | in that damage part: `DAMAGE_DATA.Scaling Mode: whole`; `DAMAGE_DATA.Scaling Dice Count: 1` |
| "is poisoned on a failed save" compared with "is slowed even on a successful save" | in that `APPLIED_EFFECTS` entry: `APPLICATION.On Save: false` (failed save only) or `APPLICATION.On Save: true` (applied either way) |

### ACTIVITY_SAVE: field reference

The sections only `ACTIVITY_SAVE` has, in full, and the common sections it takes, by name. A placeholder such as `"[true|false]"` lists the choices; it is not a value to copy. Write only what the source needs.

```yaml
ACTIVITY_SAVE:
  # Also takes these common sections, written as in "ACTIVITY_* common sections": ACTIVITY, CHAT_FLAVOR, BEHAVIOR, VISIBILITY, ACTIVATION, DURATION, CONSUMPTION, SCALING, USAGE, RECOVERY, RANGE, TARGETS, APPLIED_BEHAVIORS

  SAVE_DETAILS:
    Roll Bonus: ""
    Visible to All: true
    Challenge Ability: "[str|dex|con|int|wis|cha]" # (Comma-separate for multiple, e.g. "str, dex")
    DC Calculation: "[spellcasting|str|dex|con|int|wis|cha|custom]"
    # (Required only if DC Calculation is custom)
    DC Formula: "[text|n/a]"

  SAVE_DAMAGE:
    Damage on Save: "[none|half|full]"
    DAMAGE_PARTS:
      - DAMAGE_DATA:
          Dice Count: "[integer|n/a]"
          Die Denomination: 6
          Bonus: "[text|n/a]"
          Damage Types: [fire]
          Modifiers: [] # Ordered dice modifiers, such as rerolls or minimum results.
          Custom Enabled: false
          Custom Formula: "[text|n/a]"
          Scaling Mode: "[none|whole|half]"
          Scaling Dice Count: "[integer|n/a]"
          Scaling Formula: "[formula|number|n/a]"

  APPLIED_EFFECTS:
    # (Optional, repeatable)
    - DETAILS:
        Effect ID: n/a
        Icon: n/a
        Effect Type: base
        Origin: n/a
        Sort: 0
        Show Icon: "[0|1|2]"
        Name: "[text|n/a]"
        Icon Tint Color: "[text|n/a]" # (a Hex code color format e.g. #ff0000)
        Effect Suspended: "[true|false]"
        Apply Effect to Actor: "[true|false]"
        Status Conditions: []

      EFFECT_DESCRIPTION:
        Effect Description: |
          [multiline text content]

      DURATION:
        Value: "[integer|null|n/a]"
        Units: "[seconds|rounds|turns|minutes|hours|days|months|years]"
        Effect Expiry: null
        Effect Expired: "[true|false]"
        Combat Encounter: n/a
        Combatant: n/a
        Initiative: "[number|null|n/a]"
        Effect Start Time: "[integer|n/a]"
        Effect Start (combat) Rounds: "[integer|null|n/a]"
        Effect Start (combat) Turns: "[integer|null|n/a]"

      APPLICATION:
        Level Minimum: "[integer|n/a]"
        Level Maximum: "[integer|n/a]"
        On Save: false

      EFFECT_SYSTEM:
        Origin:
          Activity: null
          Actor: null
          Behavior: null
          Effect: null
          Item: null
          Message: null
          Profile: null
        Magical: false
        Conditions: {}
        Rider Statuses: []

      CHANGES:
        # (Optional, repeatable)
        - Attribute Key: "[text|n/a]" # (Common keys found here: https://github.com/foundryvtt/dnd5e/wiki/Active-Effect-Guide)
          Change ID: n/a
          Conditions: {}
          Replacement: "" # Empty, origin, or target.
          Change Type: "[custom|multiply|add|subtract|downgrade|upgrade|override]"
          Change Phase: "[initial|final]"
          Value: 0 # Any YAML scalar, null, list, or mapping; preserve its native type.
          Priority: "[number|n/a]"

    # Alternative to an inline definition: reference a real existing effect.
    - Effect UUID: "[text|n/a]"
      APPLICATION:
        Level Minimum: null
        Level Maximum: null
```

### ACTIVITY_SAVE: rules

SAVE_DETAILS chooses allowed save abilities and one DC calculation. For a fixed DC write `DC Calculation: custom` and the number in DC Formula; dnd5e ignores DC Formula with any other calculation. SAVE_DAMAGE.Damage on Save defines damage treatment; APPLICATION.On Save independently controls an applied effect's on-save setting. MIDI's save workflow still determines eligible effect targets.

### ACTIVITY_SAVE: example

One creature within 30 feet makes a DC 13 Constitution save against 2d6 cold damage, taking half damage on success.

Use a parent Item that supports this activity.

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
```

### ACTIVITY_SAVE: check before you answer

These checks are for `ACTIVITY_SAVE`. The checks for every activity are in "ACTIVITY_* common sections: check before you answer".

1. `Challenge Ability` is the ability the target rolls. `DC Calculation` is how the user's DC is worked out. Do not put the target's save ability in `DC Calculation`.
2. For a fixed DC write `DC Calculation: custom` and put the number in `DC Formula` as a quoted string. A number in `DC Calculation` gives a warning and is stored as `spellcasting`.
3. `DC Formula` is used only for a fixed DC: with `DC Calculation: custom`, or with `DC Calculation` left out. With `spellcasting` or an ability id, dnd5e works the DC out itself and ignores the formula. `DC Formula` is a number or a formula without dice, for example `8 + @prof + @abilities.cha.mod`.
4. Use three-letter ability ids (`dex`, not `Dexterity`). An unknown ability gives a warning and the save is stored with no ability.
5. Always write `SAVE_DAMAGE.Damage on Save` when the save deals damage. If it is missing the parser stores `none`, so a successful save takes no damage.
6. Put the damage in `SAVE_DAMAGE.DAMAGE_PARTS`. A `DAMAGE` or `ATTACK_DAMAGE` section in a Save block is skipped and shown as a warning in the preview.
7. `APPLICATION.On Save` exists only in Save activities. `false` applies the effect on a failed save only; `true` applies it even when the target succeeds.

## ACTIVITY_DAMAGE: Damage activity

- **Root key:** `ACTIVITY_DAMAGE`
- **When to use:** Use it when the source deals damage with no attack roll and no saving throw: "the creature takes 2d6 fire damage", "each creature in the aura takes 1d4 acid damage", or extra damage that the user adds with its own button, such as "deal an extra 2d8 radiant damage".
- **Smallest working block:** `ACTIVITY.Name`, one `DAMAGE.DAMAGE_PARTS` entry holding a `DAMAGE_DATA` mapping (required; the dice, the bonus and the damage type), and `DAMAGE_DETAILS.Allow Critical` (`false` unless the source says the damage can be critical).
- **Parent Item:** Any Item that can hold activities; often a feat (auras, class features), a spell or a consumable. The parent's own damage is not added, so all damage must be written in `DAMAGE.DAMAGE_PARTS`.

**Use another block when:**

- The damage depends on an attack roll: use ACTIVITY_ATTACK and put the damage in its `ATTACK_DAMAGE.DAMAGE_PARTS`.
- A saving throw halves or avoids the damage: use ACTIVITY_SAVE.
- Hit points are restored or temporary hit points are granted: use ACTIVITY_HEAL.
- Extra damage that is always part of the same weapon hit ("plus 1d6 fire damage"): add it as a damage part of the Attack activity. Do not write a second activity for it.

### ACTIVITY_DAMAGE: from source text to YAML

Find the wording closest to the source and write the fields shown. A path such as `ACTIVATION.Activation Type` means the `Activation Type` key inside the `ACTIVATION` section of `ACTIVITY_DAMAGE`. List sections take one `-` entry per item. Leave out every section the source does not mention. The rows for the common sections (activation, uses and charges, range, targets, areas, duration, applied effects) are in "ACTIVITY_* common sections: from source text to YAML".

| The source says | Write |
| --- | --- |
| "takes 2d6 cold damage" | one `DAMAGE.DAMAGE_PARTS` entry with `DAMAGE_DATA.Dice Count: 2`; `DAMAGE_DATA.Die Denomination: 6`; `DAMAGE_DATA.Damage Types: [cold]` |
| "takes 5 fire damage" (a flat amount, no dice) | `DAMAGE_DATA.Dice Count: null`; `DAMAGE_DATA.Die Denomination: null`; `DAMAGE_DATA.Bonus: "5"`; `DAMAGE_DATA.Damage Types: [fire]` |
| "1d8 + your Charisma modifier radiant damage" | `DAMAGE_DATA.Dice Count: 1`; `DAMAGE_DATA.Die Denomination: 8`; `DAMAGE_DATA.Bonus: "@abilities.cha.mod"`; `DAMAGE_DATA.Damage Types: [radiant]` |
| "2d6 fire damage plus 2d6 radiant damage" | two `DAMAGE.DAMAGE_PARTS` entries, each with its own `DAMAGE_DATA` and one damage type |
| "bludgeoning, piercing, or slashing damage (your choice)" | `DAMAGE_DATA.Damage Types: [bludgeoning, piercing, slashing]` |
| "damage equal to twice your level", or any formula that is not dice plus a bonus | `DAMAGE_DATA.Custom Enabled: true`; `DAMAGE_DATA.Custom Formula: "2 * @details.level"` |
| "the damage increases by 1d6 for each slot level above 2nd" | in that damage part: `DAMAGE_DATA.Scaling Mode: whole`; `DAMAGE_DATA.Scaling Dice Count: 1` |
| "this extra damage is doubled on a critical hit" | `DAMAGE_DETAILS.Allow Critical: true` |

### ACTIVITY_DAMAGE: field reference

The sections only `ACTIVITY_DAMAGE` has, in full, and the common sections it takes, by name. A placeholder such as `"[true|false]"` lists the choices; it is not a value to copy. Write only what the source needs.

```yaml
ACTIVITY_DAMAGE:
  # Also takes these common sections, written as in "ACTIVITY_* common sections": ACTIVITY, CHAT_FLAVOR, BEHAVIOR, VISIBILITY, ACTIVATION, DURATION, CONSUMPTION, SCALING, USAGE, RECOVERY, RANGE, TARGETS, APPLIED_EFFECTS, APPLIED_BEHAVIORS

  DAMAGE_DETAILS:
    Allow Critical: "[true|false]"
    # (Required only if Allow Critical is true)
    Extra Critical Damage Formula: "[text|n/a|integer]"

  DAMAGE:
    DAMAGE_PARTS:
      - DAMAGE_DATA:
          Dice Count: "[integer|n/a]"
          Die Denomination: 6
          Bonus: "[text|n/a]"
          Damage Types: [fire]
          Modifiers: [] # Ordered dice modifiers, such as rerolls or minimum results.
          Custom Enabled: false
          Custom Formula: "[text|n/a]"
          Scaling Mode: "[none|whole|half]"
          Scaling Dice Count: "[integer|n/a]"
          Scaling Formula: "[formula|number|n/a]"
```

### ACTIVITY_DAMAGE: rules

A Damage activity rolls damage without creating an attack or saving throw. Use Attack or Save when the mechanic calls for one. DAMAGE_DETAILS.Allow Critical is a damage control; it does not create an attack roll.

### ACTIVITY_DAMAGE: example

Deal 2d6 cold damage to one creature within 30 feet. This Damage activity does not make an attack or saving throw.

Use a parent Item that supports this activity.

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
```

### ACTIVITY_DAMAGE: check before you answer

These checks are for `ACTIVITY_DAMAGE`. The checks for every activity are in "ACTIVITY_* common sections: check before you answer".

1. A Damage activity needs at least one `DAMAGE.DAMAGE_PARTS` entry with a `DAMAGE_DATA` mapping. Without the `DAMAGE` section the block fails with "Damage activities require at least one valid Damage Formula." An empty `DAMAGE_PARTS: []` imports an activity that rolls no damage, with a warning.
2. `DAMAGE_PARTS` must be inside the `DAMAGE` section. A top-level `DAMAGE_PARTS` is ignored and the block fails with the same error.
3. Keep `DAMAGE_DETAILS.Allow Critical: false` unless the source says the damage can be critical. The field does not add an attack roll.
4. The critical bonus field of this type is `DAMAGE_DETAILS.Extra Critical Damage Formula`. The Attack name `Extra Critical Damage` is skipped in a Damage block and shown as a warning in the preview.
5. A Damage activity has no save-for-half field; `Damage on Save` is ignored here. If the source mentions a saving throw, write ACTIVITY_SAVE.
6. `@mod` is the spellcasting ability modifier only when the parent is a spell Item. On other Items it is 0, so name the ability: `@abilities.cha.mod`.
7. Inside `DAMAGE_DATA`: `Die Denomination` is the number of sides (`6`, not `d6`), `Dice Count` is a whole number and never a formula, and `Scaling Mode` is `none`, `whole` or `half`. The legacy table value `No Scaling` is rejected inside `DAMAGE_DATA`.

## ACTIVITY_HEAL: Heal activity

- **Root key:** `ACTIVITY_HEAL`
- **When to use:** Use it when the source restores hit points, grants temporary hit points or raises the hit point maximum: "regains 2d4 + 2 hit points", "gains 10 temporary hit points", "its hit point maximum increases by 5".
- **Smallest working block:** `ACTIVITY.Name` and one `HEALING.DAMAGE_DATA` mapping (required) with `Dice Count`, `Die Denomination` and `Bonus` for the amount and `Damage Types` for the kind: `[healing]`, `[temphp]` or `[maximum]`.
- **Parent Item:** Any Item that can hold activities: spell, consumable (potions), feat or equipment. The parent needs nothing special. `@mod` in the formula only works on a spell Item, where it is the spellcasting ability modifier.

**Use another block when:**

- The roll deals damage: use ACTIVITY_DAMAGE, ACTIVITY_ATTACK or ACTIVITY_SAVE.
- The feature only ends a condition or disease and restores no hit points: use ACTIVITY_UTILITY and keep the text in the description.
- A bonus to maximum hit points that is always on and never activated: use a passive EFFECT.

### ACTIVITY_HEAL: from source text to YAML

Find the wording closest to the source and write the fields shown. A path such as `ACTIVATION.Activation Type` means the `Activation Type` key inside the `ACTIVATION` section of `ACTIVITY_HEAL`. List sections take one `-` entry per item. Leave out every section the source does not mention. The rows for the common sections (activation, uses and charges, range, targets, areas, duration, applied effects) are in "ACTIVITY_* common sections: from source text to YAML".

| The source says | Write |
| --- | --- |
| "regains 2d4 + 2 hit points" | `HEALING.DAMAGE_DATA.Dice Count: 2`; `HEALING.DAMAGE_DATA.Die Denomination: 4`; `HEALING.DAMAGE_DATA.Bonus: "2"`; `HEALING.DAMAGE_DATA.Damage Types: [healing]` |
| "regains hit points equal to 1d8 + your spellcasting ability modifier" (on a spell Item) | `HEALING.DAMAGE_DATA.Dice Count: 1`; `HEALING.DAMAGE_DATA.Die Denomination: 8`; `HEALING.DAMAGE_DATA.Bonus: "@mod"`; `HEALING.DAMAGE_DATA.Damage Types: [healing]` |
| "regains 1d10 + your fighter level hit points" | `HEALING.DAMAGE_DATA.Dice Count: 1`; `HEALING.DAMAGE_DATA.Die Denomination: 10`; `HEALING.DAMAGE_DATA.Bonus: "@classes.fighter.levels"`; `HEALING.DAMAGE_DATA.Damage Types: [healing]` |
| "gains 10 temporary hit points" | `HEALING.DAMAGE_DATA.Dice Count: null`; `HEALING.DAMAGE_DATA.Die Denomination: null`; `HEALING.DAMAGE_DATA.Bonus: "10"`; `HEALING.DAMAGE_DATA.Damage Types: [temphp]` |
| "its hit point maximum increases by 5" | `HEALING.DAMAGE_DATA.Dice Count: null`; `HEALING.DAMAGE_DATA.Die Denomination: null`; `HEALING.DAMAGE_DATA.Bonus: "5"`; `HEALING.DAMAGE_DATA.Damage Types: [maximum]` |
| "the healing increases by 1d8 for each slot level above 1st" | `HEALING.DAMAGE_DATA.Scaling Mode: whole`; `HEALING.DAMAGE_DATA.Scaling Dice Count: 1` |
| "increases by 5 for each slot level above 2nd" (a flat amount per level) | `HEALING.DAMAGE_DATA.Scaling Mode: whole`; `HEALING.DAMAGE_DATA.Scaling Dice Count: 0`; `HEALING.DAMAGE_DATA.Scaling Formula: "5"` |
| "regains hit points equal to five times your level", or any formula that is not dice plus a bonus | `HEALING.DAMAGE_DATA.Custom Enabled: true`; `HEALING.DAMAGE_DATA.Custom Formula: "5 * @details.level"` |

### ACTIVITY_HEAL: field reference

The sections only `ACTIVITY_HEAL` has, in full, and the common sections it takes, by name. A placeholder such as `"[true|false]"` lists the choices; it is not a value to copy. Write only what the source needs.

```yaml
ACTIVITY_HEAL:
  # Also takes these common sections, written as in "ACTIVITY_* common sections": ACTIVITY, CHAT_FLAVOR, BEHAVIOR, VISIBILITY, ACTIVATION, DURATION, CONSUMPTION, SCALING, USAGE, RECOVERY, RANGE, TARGETS, APPLIED_EFFECTS, APPLIED_BEHAVIORS

  HEALING:
    DAMAGE_DATA:
      Dice Count: "[integer|n/a]"
      Die Denomination: 6
      Bonus: "[text|n/a]"
      Damage Types: [healing]
      Modifiers: [] # Ordered dice modifiers, such as rerolls or minimum results.
      Custom Enabled: false
      Custom Formula: "[text|n/a]"
      Scaling Mode: "[none|whole|half]"
      Scaling Dice Count: "[integer|n/a]"
      Scaling Formula: "[formula|number|n/a]"
```

### ACTIVITY_HEAL: rules

HEALING.DAMAGE_DATA uses healing, temphp or maximum as the healing type. Temporary hit points do not stack under normal system rules. The activity's target and consumption are separate from the healing formula.

### ACTIVITY_HEAL: example

Touch one creature to restore 1d8 + 2 hit points.

Use a parent Item that supports this activity.

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
```

### ACTIVITY_HEAL: check before you answer

These checks are for `ACTIVITY_HEAL`. The checks for every activity are in "ACTIVITY_* common sections: check before you answer".

1. `HEALING` holds one `DAMAGE_DATA` mapping directly. Do not write `DAMAGE_PARTS` or a `- ` list item under `HEALING`; the block then fails with "Heal activities require a populated HEALING.DAMAGE_DATA or legacy HEALING.Formula."
2. `Damage Types` takes exactly one of `healing`, `temphp` or `maximum`. `temp` or `temporary` is a parse error. A damage type such as `fire` is accepted without a warning and is wrong.
3. The healing needs dice, a `Bonus` or a custom formula. A `DAMAGE_DATA` in which all three are empty is an error.
4. One Heal activity makes one healing roll. If the source restores hit points and also grants temporary hit points, write two ACTIVITY_HEAL blocks separated by `---`.
5. `@mod` is the spellcasting ability modifier only on a spell Item. On a feat, potion or other Item it is 0, so name the ability, for example `@abilities.wis.mod`.
6. Do not combine `DAMAGE_DATA` with the legacy keys `Formula` or `Type` from the field table; the parser rejects the block.
7. Ending a condition or disease is not a healing field. Keep that text in `ACTIVITY.Description` or `CHAT_FLAVOR.Chat Description`.

## ACTIVITY_UTILITY: Utility activity

- **Root key:** `ACTIVITY_UTILITY`
- **When to use:** Use it when the feature is activated but needs no attack roll, saving throw, ability check, damage or healing: "As a bonus action, you enter a rage", "gain the following benefits for 1 minute", or a die with no built-in meaning, such as "roll a d6" or "roll on the Wild Magic Surge table".
- **Smallest working block:** `ACTIVITY.Name` alone gives a working Utility activity; add the shared sections for its action cost and uses. Add `UTILITY_ROLL.Roll Formula` and `UTILITY_ROLL.Roll Label` only when a die is rolled. Add `APPLIED_EFFECTS` when using it applies an effect.
- **Parent Item:** Any Item that can hold activities; most often a feat (class and species features) or equipment. The parent needs nothing special.

**Use another block when:**

- The roll is an attack, a saving throw, an ability check, damage or healing: use ACTIVITY_ATTACK, ACTIVITY_SAVE, ACTIVITY_CHECK, ACTIVITY_DAMAGE or ACTIVITY_HEAL.
- The feature casts a spell, enchants an item, summons a creature, changes the user's form or teleports: use ACTIVITY_CAST, ACTIVITY_ENCHANT, ACTIVITY_SUMMON, ACTIVITY_TRANSFORM or ACTIVITY_TELEPORT.
- The benefit is always on and is never activated: use a passive EFFECT block and no activity.

### ACTIVITY_UTILITY: from source text to YAML

Find the wording closest to the source and write the fields shown. A path such as `ACTIVATION.Activation Type` means the `Activation Type` key inside the `ACTIVATION` section of `ACTIVITY_UTILITY`. List sections take one `-` entry per item. Leave out every section the source does not mention. The rows for the common sections (activation, uses and charges, range, targets, areas, duration, applied effects) are in "ACTIVITY_* common sections: from source text to YAML".

| The source says | Write |
| --- | --- |
| "roll a d6" (a die that is not an attack, save, check, damage or healing roll) | `UTILITY_ROLL.Roll Formula: "1d6"`; `UTILITY_ROLL.Roll Label: Recharge` |
| "roll a d100 and consult the table" | `UTILITY_ROLL.Roll Formula: "1d100"`; `UTILITY_ROLL.Roll Label: Wild Magic Surge` |
| "roll 2d4 and add your Intelligence modifier" | `UTILITY_ROLL.Roll Formula: "2d4 + @abilities.int.mod"` |
| "the creature holding the die can roll it" (players other than the user must be able to click the roll) | `UTILITY_ROLL.Visible to All: true` |
| "As a bonus action, you enter a rage" (no die is rolled) | no `UTILITY_ROLL` section; `ACTIVITY.Name` and the shared sections only |
| "the door unlocks" (the outcome is only text) | `CHAT_FLAVOR.Chat Description: The door unlocks.` |
| "roll a d6; nothing can be added to this roll" (roll at once, with no roll dialog) | `BEHAVIOR.Roll Prompt: false` |

### ACTIVITY_UTILITY: field reference

The sections only `ACTIVITY_UTILITY` has, in full, and the common sections it takes, by name. A placeholder such as `"[true|false]"` lists the choices; it is not a value to copy. Write only what the source needs.

```yaml
ACTIVITY_UTILITY:
  # Also takes these common sections, written as in "ACTIVITY_* common sections": ACTIVITY, CHAT_FLAVOR, VISIBILITY, ACTIVATION, DURATION, CONSUMPTION, SCALING, USAGE, RECOVERY, RANGE, TARGETS, APPLIED_EFFECTS, APPLIED_BEHAVIORS

  BEHAVIOR:
    Consume Spell Slot: "[true|false]"
    Measured Template Prompt: "[true|false]"
    Roll Prompt: "[true|false]"

  UTILITY_ROLL:
    # (Optional. Omit entire section if the activity has no roll.)
    Roll Label: "[text|n/a]"
    Roll Formula: "[text|n/a]"
    Visible to All: "[true|false]"
```

### ACTIVITY_UTILITY: rules

UTILITY_ROLL is an arbitrary labelled roll, not an ability check or a saving throw. Use Check for proficiency, associated skills/tools and a DC. Roll Prompt and Visible to All control different behavior.

### ACTIVITY_UTILITY: example

Roll a visible 1d6 labelled Current strength. This utility roll does not make an ability check.

Use a parent Item that supports this activity.

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
```

### ACTIVITY_UTILITY: check before you answer

These checks are for `ACTIVITY_UTILITY`. The checks for every activity are in "ACTIVITY_* common sections: check before you answer".

1. Do not put damage, healing, an attack roll, a saving throw or an ability check in `UTILITY_ROLL`. Its result is only shown in chat and is applied to nothing. Use the matching activity type.
2. `DAMAGE`, `HEALING`, `SAVE_DETAILS` and `CHECK_DETAILS` sections in a Utility block are skipped and shown as a warning in the preview.
3. Leave out the whole `UTILITY_ROLL` section when no die is rolled. Do not write a filler formula such as `0`.
4. `Roll Prompt` belongs under `BEHAVIOR`. Under `UTILITY_ROLL` it is ignored.
5. `BEHAVIOR.Roll Prompt` decides whether the roller sees the roll dialog. `UTILITY_ROLL.Visible to All` decides whether all players see the roll button. When they are left out the parser stores `Roll Prompt: true` and `Visible to All: false`.
6. `Roll Formula` holds one formula, written as a quoted string. Put explanations in `Roll Label` or `CHAT_FLAVOR.Chat Description`.
7. A bonus that is always on and needs no activation is a passive EFFECT block, not a Utility activity.

## ACTIVITY_CHECK: Check activity

- **Root key:** `ACTIVITY_CHECK`
- **When to use:** Use it when the source calls for an ability check, a skill check or a tool check: "a DC 15 Strength (Athletics) check", "a successful DC 12 Wisdom (Medicine) check", "a Dexterity check using thieves' tools". The activity puts one check button in chat for each listed skill or tool.
- **Smallest working block:** `ACTIVITY.Name`, one of `CHECK_DETAILS.Associated Skills`, `CHECK_DETAILS.Associated Tools` or `CHECK_DETAILS.Check Ability` (what is rolled), and `CHECK_DETAILS.DC Calculation` with `CHECK_DETAILS.DC Formula` (the DC; leave both out when the source gives no DC).
- **Parent Item:** Any Item that can hold activities: feat, spell, equipment, tool or consumable. The parent needs nothing special. The roller's own skill and tool proficiencies are applied by the system.

**Use another block when:**

- The text says "saving throw": use ACTIVITY_SAVE, even when it names the same ability.
- The user rolls to hit: use ACTIVITY_ATTACK.
- A die with no ability, skill or tool behind it: use ACTIVITY_UTILITY.
- A passive bonus or advantage on checks ("advantage on Stealth checks"): use a passive EFFECT.
- A failed check that deals damage: the base Check activity has no damage fields. Write the damage as a separate ACTIVITY_DAMAGE block.

### ACTIVITY_CHECK: from source text to YAML

Find the wording closest to the source and write the fields shown. A path such as `ACTIVATION.Activation Type` means the `Activation Type` key inside the `ACTIVATION` section of `ACTIVITY_CHECK`. List sections take one `-` entry per item. Leave out every section the source does not mention. The rows for the common sections (activation, uses and charges, range, targets, areas, duration, applied effects) are in "ACTIVITY_* common sections: from source text to YAML".

| The source says | Write |
| --- | --- |
| "a DC 15 Dexterity (Acrobatics) check" | `CHECK_DETAILS.Associated Skills: [acr]`; `CHECK_DETAILS.Check Ability: dex`; `CHECK_DETAILS.DC Calculation: custom`; `CHECK_DETAILS.DC Formula: "15"` |
| "a DC 13 Strength check" (no skill) | `CHECK_DETAILS.Associated Skills: []`; `CHECK_DETAILS.Associated Tools: []`; `CHECK_DETAILS.Check Ability: str`; `CHECK_DETAILS.DC Calculation: custom`; `CHECK_DETAILS.DC Formula: "13"` |
| "a Strength (Athletics) or Dexterity (Acrobatics) check (the target chooses)" | `CHECK_DETAILS.Associated Skills: [ath, acr]`; `CHECK_DETAILS.Check Ability: n/a` |
| "a DC 15 Dexterity check using thieves' tools" | `CHECK_DETAILS.Associated Tools: [thief]`; `CHECK_DETAILS.Check Ability: dex`; `CHECK_DETAILS.DC Calculation: custom`; `CHECK_DETAILS.DC Formula: "15"` |
| "against your spell save DC" | `CHECK_DETAILS.DC Calculation: spellcasting` |
| "the DC equals 8 + your proficiency bonus + your Strength modifier" | `CHECK_DETAILS.DC Calculation: str` |
| "make a Wisdom (Perception) check" with no DC in the text | `CHECK_DETAILS.Associated Skills: [prc]`; `CHECK_DETAILS.DC Calculation: n/a` |
| "a Strength (Intimidation) check" (a skill rolled with another ability) | `CHECK_DETAILS.Associated Skills: [itm]`; `CHECK_DETAILS.Check Ability: str` |
| "with a +2 bonus to the check" | `CHECK_DETAILS.Roll Bonus: "2"` |

### ACTIVITY_CHECK: field reference

The sections only `ACTIVITY_CHECK` has, in full, and the common sections it takes, by name. A placeholder such as `"[true|false]"` lists the choices; it is not a value to copy. Write only what the source needs.

```yaml
ACTIVITY_CHECK:
  # Also takes these common sections, written as in "ACTIVITY_* common sections": ACTIVITY, CHAT_FLAVOR, BEHAVIOR, VISIBILITY, ACTIVATION, DURATION, CONSUMPTION, SCALING, USAGE, RECOVERY, RANGE, TARGETS, APPLIED_EFFECTS, APPLIED_BEHAVIORS

  CHECK_DETAILS:
    Roll Bonus: ""
    Visible to All: true
    Associated Skills: [] # acr, ani, arc, ath, dec, his, ins, itm, inv, med, nat, prc, prf, per, rel, slt, ste, sur
    Associated Tools: [] # alchemist, bagpipes, brewer, calligrapher, card, carpenter, cartographer, chess, cobbler, cook, dice, disg, drum, dulcimer, flute, forg, glassblower, herb, horn, jeweler, leatherworker, lute, lyre, mason, navg, painter, panflute, pois, potter, shawm, smith, thief, tinker, viol, weaver, woodcarver
    Check Ability: "[str|dex|con|int|wis|cha|n/a]"
    DC Calculation: "[spellcasting|str|dex|con|int|wis|cha|custom|n/a]"
    # (Required only if DC Calculation is custom)
    DC Formula: "[text|n/a]"
```

### ACTIVITY_CHECK: rules

Use one CHECK_DETAILS mapping with Associated Skills and Associated Tools lists. All choices share the same ability override and DC. Use separate activities for different abilities or DCs; do not express them as conflicting list entries. MIDI Check damage uses MIDI DAMAGE and MIDI DAMAGE_PARTS, not native Save damage keys.

### ACTIVITY_CHECK: example

Make a DC 15 Dexterity (Acrobatics) check.

Use a parent Item that supports this activity.

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
```

### ACTIVITY_CHECK: check before you answer

These checks are for `ACTIVITY_CHECK`. The checks for every activity are in "ACTIVITY_* common sections: check before you answer".

1. Use the three-letter skill ids from the SKILL IDs table (`ath`, not `athletics`). `prc` is Perception and `per` is Persuasion; `itm` is Intimidation and `inv` is Investigation. An unknown id gives a warning and is dropped.
2. Tools go in `Associated Tools` with the ids from the TOOL IDs table (`thief`, `herb`, `pois`). A tool id inside `Associated Skills` is dropped with a warning.
3. Write skills and tools as YAML lists: `[ath, acr]`. A comma-separated string such as `"ath, acr"` is read as one unknown id.
4. One Check activity has one `Check Ability` and one DC. When the source lists skills that use different abilities, write `Check Ability: n/a` so that each skill uses its own ability. Different DCs need separate ACTIVITY_CHECK blocks.
5. `Check Ability` is the ability the roller uses. `DC Calculation` is how the user's DC is worked out. For a fixed DC write `DC Calculation: custom` and the number in `DC Formula`.
6. A "saving throw" is never a Check. "Dexterity saving throw" is ACTIVITY_SAVE; "Dexterity check" is ACTIVITY_CHECK.
7. Do not write `APPLICATION.On Save` in the `APPLIED_EFFECTS` of a Check; it is a parse error outside ACTIVITY_SAVE.

## ACTIVITY_CAST: Cast activity

- **Root key:** `ACTIVITY_CAST`
- **When to use:** Use ACTIVITY_CAST when the rules let the owner cast a named spell that already exists as a spell Item: "you can cast fireball from the wand", "the following spells can be cast from the staff", "you can cast misty step once per long rest". The block links that spell. It does not describe the spell's attack, save or damage.
- **Smallest working block:** `ACTIVITY.Name` (label on the sheet), `CASTING_DETAILS.Item UUID` (the spell Item to cast), `CASTING_DETAILS.Item Name` (exact spell name, used to look the spell up when the UUID is `n/a`). Without a UUID the block parses with a warning and cannot be imported until the spell is resolved.
- **Parent Item:** Usually equipment, a weapon, a consumable or a feat (wand, staff, ring, class feature). The Item must be owned by an Actor before the cast can run. The spell must already exist as a spell Item in the world or in a compendium, and you need its real UUID. One ACTIVITY_CAST links one spell.

**Use another block when:**

- A spell-like power with no spell Item to link, where the text gives its own save, attack, damage or healing: write ACTIVITY_SAVE, ACTIVITY_ATTACK, ACTIVITY_DAMAGE or ACTIVITY_HEAL.
- The parent Item is the spell itself: write the activities that the spell performs (ACTIVITY_SAVE, ACTIVITY_ATTACK and so on), not ACTIVITY_CAST.
- A lasting bonus or condition for the Item's owner: write EFFECT. ACTIVITY_CAST cannot carry APPLIED_EFFECTS.

### ACTIVITY_CAST: from source text to YAML

Find the wording closest to the source and write the fields shown. A path such as `ACTIVATION.Activation Type` means the `Activation Type` key inside the `ACTIVATION` section of `ACTIVITY_CAST`. List sections take one `-` entry per item. Leave out every section the source does not mention. The rows for the common sections (activation, uses and charges, range, targets, areas, duration, applied effects) are in "ACTIVITY_* common sections: from source text to YAML".

| The source says | Write |
| --- | --- |
| "you can cast fireball from it" | `CASTING_DETAILS.Item Name: Fireball`; `CASTING_DETAILS.Item UUID: Compendium.dnd5e.spells24.Item.phbsplFireball00` (the template's bundled example; replace it with the real UUID of the spell in the user's world) |
| the spell is named but no UUID was supplied | `CASTING_DETAILS.Item Name: Detect Magic`; `CASTING_DETAILS.Item UUID: n/a` (parses with a warning; the user resolves the spell in the importer before import) |
| "spell save DC 15" or "+7 to hit with spell attacks" | `CASTING_DETAILS.Override Values: true`; `CASTING_DETAILS.Save DC: 15`; `CASTING_DETAILS.Attack Bonus: 7` (write only the value the source gives) |
| "cast it as a 5th-level spell" | `CASTING_DETAILS.Spell Level: 5` |
| "without expending a spell slot" | `BEHAVIOR.Consume Spell Slot: false` |
| "Intelligence is your spellcasting ability for this spell" | `CASTING_DETAILS.Casting Ability: int` |
| "requires no components" (the usual rule for a spell cast from a magic item) | `CASTING_DETAILS.Ignored Properties: [vocal, somatic, material]` |
| "doesn't require concentration" | `CASTING_DETAILS.Ignored Properties: [concentration]`; `DURATION.Override Duration: true`; `DURATION.Duration Time: minute`; `DURATION.Duration Amount: 1`; `DURATION.Concentration: false` (repeat the spell's own duration here: the override replaces the whole duration) |
| the spell is cast from the Item only and should not be listed with the owner's spells | `BEHAVIOR.Display in Spellbook: false` |

### ACTIVITY_CAST: field reference

The sections only `ACTIVITY_CAST` has, in full, and the common sections it takes, by name. A placeholder such as `"[true|false]"` lists the choices; it is not a value to copy. Write only what the source needs.

```yaml
ACTIVITY_CAST:
  # Also takes these common sections, written as in "ACTIVITY_* common sections": ACTIVITY, CHAT_FLAVOR, VISIBILITY, ACTIVATION, DURATION, SCALING, USAGE, RECOVERY, RANGE, APPLIED_BEHAVIORS

  BEHAVIOR:
    Measured Template Prompt: "[true|false]"
    Consume Spell Slot: "[true|false]"
    Display in Spellbook: "[true|false]"

  CONSUMPTION:
    # (Optional, repeatable list)
    - Consumption Type: "[activityUses|itemUses|material|hitDice|spellSlots|attribute]"
      # (Optional for parent itemUses; another item/material requires a real eligible target)
      Target Item/Material: "[text|n/a]" # (UUID or ID of item to consume)
      # (Required only if Consumption Type is hitDice)
      Target Die: "[smallest|d4|d6|d8|d10|d12|largest]"
      # (Required only if Consumption Type is spellSlots)
      Target Level: "[1|2|3|4|5|6|7|8|9]"
      # (Required only if Consumption Type is attribute)
      Target Attribute: "[text|n/a]" # (Path e.g. attributes.exhaustion)
      Amount: "[formula|number|n/a]" # (Negative numbers restore)
      Scaling Mode: "[none|amount|level]" # level only for spellSlots.
      Scaling Formula: "[formula|number|n/a]"

  TARGETS:
    Override Target: "[true|false]"
    Template Count: "[formula|number|n/a]"
    Template Contiguous: "[true|false]"
    Template Stationary: "[true|false]"
    # (Required only if Override Target is True)
    Target Type: "[self|ally|enemy|creature|object|space|creatureOrObject|any|willing|n/a]"
    Target Amount: "[formula|number|n/a]"
    Special Targeting: "[text|n/a]"
    Choose Targets: "[true|false]"
    Template Type: "[cone|cube|cylinder|line|radius|ring|sphere|circle|square|wall|n/a]"
    Template Measurement: "[formula|number|n/a]"
    # (Used when Template Type is line, wall or ring)
    Template Dimensions: "[formula|number|n/a]" # (Width/Thickness)
    # (Used when Template Type is cylinder, wall or ring)
    Template Height: "[formula|number|n/a]"
    Template Unit: "[ft|m]"

  # APPLIED_EFFECTS are not supported on ACTIVITY_CAST in dnd5e.
  # Use a standalone EFFECT block if the spell should create an ActiveEffect.

  CASTING_DETAILS:
    Item Name: "[text|n/a]"
    Item UUID: "[text|n/a]"
    Casting Ability: "[Spellcasting|str|dex|con|int|wis|cha]"
    Spell Level: "[number|n/a]" # Native numeric casting level; omitted uses the linked spell level.
    Ignored Properties: [] # Optional list: vocal, somatic, material, concentration, ritual.
    Override Values: "[true|false]"
    # (Used when Override Values is true; preserve the stored value when false)
    Attack Bonus: "[number|null|n/a]"
    # (Used when Override Values is true; preserve the stored value when false)
    Save DC: "[number|null|n/a]"
  
```

### ACTIVITY_CAST: rules

CASTING_DETAILS.Item UUID must resolve to an accessible spell Item. Item Name is a lookup hint, not a replacement UUID. Spell Level is numeric; Override Values controls the supplied attack/save values independently. The Item selector may remain unresolved in a draft, but resolve it before import. Cast does not support APPLIED_EFFECTS. Native target inheritance and measured-template prompting are separate controls. In reviewed MIDI 14.6.11, Cast forces target confirmation to never.

| Configuration | Authoring rule |
| --- | --- |
| Name and identity | Supply a meaningful ACTIVITY.Name. Optional Activity ID must be exactly 16 alphanumeric characters; omit it to allocate an ID. An identifier/name is not a UUID. Omit Sort for append ordering; an authored 0 is explicit. |
| Local timing | Set Override Activation and Override Duration true for authored local timing, false for intentional inheritance. False retains local values; it does not erase them. |
| Range and target | Set Override Range/Override Target from intent. Their false state retains local data. Self range is a range choice, not an override toggle. Cast has additional target behavior below. |
| Consumption | itemUses with an omitted Target Item/Material consumes the parent Item's uses. Supply a real eligible item reference for another Item. material needs an eligible material target; activityUses always consumes this activity's uses. Attribute, hit-die and slot targets require the matching resource context. |
| Scaling | Activity SCALING only controls Allow Scaling/Max Scaling. Resource scaling belongs on each CONSUMPTION entry; damage/healing scaling belongs inside DAMAGE_DATA. |
| Damage/healing | Use all ten DAMAGE_DATA fields when specifying that value explicitly. Do not combine populated formula shorthand with DAMAGE_DATA for the same part. An inactive Custom Formula or Scaling Formula can still be stored. |
| Applied effects | APPLIED_EFFECTS creates and links effects for this activity. Apply Effect to Actor is the passive transfer flag, not a command to apply the effect to the activity's target; use false for ordinary applied effects. |
| Effect timing | Use DURATION.Value/Units on effects. Leave generated start fields omitted for a newly authored effect; Start:null and populated start fields are mutually exclusive. |

### ACTIVITY_CAST: example

Cast the bundled 2024 Fireball at level 3 using Intelligence and a fixed save DC of 15, without consuming a spell slot through this activity.

Requires the dnd5e.spells24 compendium and its Fireball Item. This is an intentional bundled reference, not a portable guarantee. Resolve or replace it with a real spell Item UUID before import if unavailable.

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
```

### ACTIVITY_CAST: check before you answer

These checks are for `ACTIVITY_CAST`. The checks for every activity are in "ACTIVITY_* common sections: check before you answer".

1. Never invent `CASTING_DETAILS.Item UUID`. If the source gives no UUID, write `Item UUID: n/a` with the exact `Item Name` and tell the user to resolve the spell before import.
2. Do not restate the spell's range, target, save, damage or effects in this block. The linked spell supplies them. Do not add `APPLIED_EFFECTS`: Cast does not support it and the parser skips it with a warning.
3. Write `Save DC`, `Attack Bonus` and `Spell Level` as plain numbers (`15`, `7`, `5`). A formula in `Save DC` or `Attack Bonus` is dropped without a warning. Text such as `3rd` in `Spell Level` is a parse error. `Save DC` and `Attack Bonus` apply only with `Override Values: true`.
4. Write `Consume Spell Slot` under `BEHAVIOR`, not under `CASTING_DETAILS`. Different values in the two places are a parse error.
5. `Casting Ability` takes an ability id (`str`, `dex`, `con`, `int`, `wis`, `cha`), not a word such as `Intelligence`. Leave the field out when the spell uses the caster's own spellcasting ability.
6. `Ignored Properties` uses the ids `vocal`, `somatic`, `material`, `concentration` and `ritual`. `verbal` is not accepted. If you leave the list out, no component is ignored.
7. One block links one spell. For an Item that casts three spells, write three `ACTIVITY_CAST` blocks separated by `---`.

## ACTIVITY_ENCHANT: Enchant activity

- **Root key:** `ACTIVITY_ENCHANT`
- **When to use:** Use ACTIVITY_ENCHANT when using the feature changes an Item for a time: "a nonmagical weapon you touch becomes a magic weapon with a +1 bonus to attack and damage rolls", "the armor's AC increases by 1", "the weapon deals an extra 1d4 fire damage", an infusion placed on an object. The changes are stored on the target Item, not on a creature.
- **Smallest working block:** `ACTIVITY.Name`, one `APPLIED_EFFECTS` entry with `DETAILS.Name` and `DETAILS.Effect Type: enchantment` (the profile the user picks), one `CHANGES` row in it with `Attribute Key`, `Change Type` and `Value` (what changes on the Item), and `RESTRICTIONS.Item Type` (which Items may receive it). Without an `APPLIED_EFFECTS` entry the block parses with a warning and enchants nothing.
- **Parent Item:** Usually a spell or a feat (class feature, infusion), or a weapon or equipment Item that enchants itself. Nothing else must exist beforehand: the enchantment profiles are created from `APPLIED_EFFECTS`. The Item to enchant is chosen at use time, unless `Automatically Self Enchant` is true.

**Use another block when:**

- A bonus or condition on a creature ("the target gains +2 AC", "you have resistance"): put a base effect in the APPLIED_EFFECTS of ACTIVITY_UTILITY, ACTIVITY_SAVE or another activity, or write EFFECT.
- A bonus that is always part of the Item and needs no activation (a +1 longsword): that belongs in the Item's own fields, not in an activity.
- Casting an existing enchantment spell Item (magic weapon) from another Item: write ACTIVITY_CAST.

### ACTIVITY_ENCHANT: from source text to YAML

Find the wording closest to the source and write the fields shown. A path such as `ACTIVATION.Activation Type` means the `Activation Type` key inside the `ACTIVATION` section of `ACTIVITY_ENCHANT`. List sections take one `-` entry per item. Leave out every section the source does not mention. The rows for the common sections (activation, uses and charges, range, targets, areas, duration, applied effects) are in "ACTIVITY_* common sections: from source text to YAML".

| The source says | Write |
| --- | --- |
| "a nonmagical weapon" | `RESTRICTIONS.Item Type: weapon`; `RESTRICTIONS.Allow Magical: false` |
| "a suit of armor or a shield" | `RESTRICTIONS.Item Type: equipment`; `RESTRICTIONS.Valid Categories: light, medium, heavy, shield` |
| "a weapon that has the thrown property" | `RESTRICTIONS.Item Type: weapon`; `RESTRICTIONS.Valid Properties: thr` |
| "becomes a magic weapon with a +1 bonus to attack and damage rolls" | `CHANGES.Attribute Key: system.magicalBonus`; `CHANGES.Change Type: override`; `CHANGES.Value: 1`, and a second row `CHANGES.Attribute Key: system.properties`; `CHANGES.Change Type: add`; `CHANGES.Value: mgc` |
| "the armor has a +1 bonus to AC" | `CHANGES.Attribute Key: system.armor.magicalBonus`; `CHANGES.Change Type: override`; `CHANGES.Value: 1` (plus the `system.properties` row with `mgc` when the armor is nonmagical) |
| "deals an extra 1d4 fire damage" | `CHANGES.Attribute Key: system.damage.parts`; `CHANGES.Change Type: add`; `CHANGES.Value: '[["1d4", "fire"]]'` |
| the enchanted Item should show the bonus in its name | `CHANGES.Attribute Key: name`; `CHANGES.Change Type: override`; `CHANGES.Value: "{}, +1"` (`{}` is replaced by the Item's current name) |
| "with a spell slot of 4th level or higher, the bonus increases to +2" | one `APPLIED_EFFECTS` entry per tier: `APPLICATION.Level Minimum: 2`; `APPLICATION.Level Maximum: 3` on the +1 profile, and `APPLICATION.Level Minimum: 4`; `APPLICATION.Level Maximum: 5` on the +2 profile |
| the feature changes the Item that carries it ("this weapon bursts into flame") | `BEHAVIOR.Automatically Self Enchant: true` |
| "while enchanted, the item also gains an extra activity, effect or item" | in that `APPLIED_EFFECTS` entry: `APPLICATION.Rider Activities`, `APPLICATION.Rider Effects` or `APPLICATION.Rider Items`, with real IDs or UUIDs (works on dnd5e 6.0 and 6.1). On dnd5e 6.1 or later the same three lists can be stored on the effect itself under `EFFECT_SYSTEM`; leave those `[]` on dnd5e 6.0 |

### ACTIVITY_ENCHANT: field reference

The sections only `ACTIVITY_ENCHANT` has, in full, and the common sections it takes, by name. A placeholder such as `"[true|false]"` lists the choices; it is not a value to copy. Write only what the source needs.

```yaml
ACTIVITY_ENCHANT:
  # Also takes these common sections, written as in "ACTIVITY_* common sections": ACTIVITY, CHAT_FLAVOR, VISIBILITY, ACTIVATION, DURATION, CONSUMPTION, SCALING, USAGE, RECOVERY, RANGE, TARGETS, APPLIED_BEHAVIORS

  BEHAVIOR:
    Consume Spell Slot: "[true|false]"
    Automatically Self Enchant: "[true|false]"
    Measured Template Prompt: "[true|false]"

  APPLIED_EFFECTS:
    # (Required, repeatable. Imported as Foundry enchantment Active Effects.)
    - DETAILS:
        Effect ID: n/a
        Icon: n/a
        Effect Type: enchantment
        Origin: n/a
        Sort: 0
        Show Icon: "[0|1|2]"
        Name: "[text]"
        Icon Tint Color: "[text|n/a]"
        Effect Suspended: "[true|false]"
        Apply Effect to Actor: false
        Status Conditions: []

      EFFECT_DESCRIPTION:
        Effect Description: |
          [multiline text content]

      DURATION:
        Value: "[integer|null|n/a]"
        Units: "[seconds|rounds|turns|minutes|hours|days|months|years]"
        Effect Expiry: null
        Effect Expired: "[true|false]"
        Combat Encounter: n/a
        Combatant: n/a
        Initiative: "[number|null|n/a]"
        Effect Start Time: "n/a"
        Effect Start (combat) Rounds: "n/a"
        Effect Start (combat) Turns: "n/a"

      APPLICATION:
        Level Minimum: "[integer|n/a]"
        Level Maximum: "[integer|n/a]"
        Rider Activities: []
        Rider Effects: []
        Rider Items: []

      EFFECT_SYSTEM:
        Origin:
          Activity: null
          Actor: null
          Behavior: null
          Effect: null
          Item: null
          Message: null
          Profile: null
        Magical: true
        # (dnd5e 6.1 or later: riders stored on the enchantment itself. Keep [] on dnd5e 6.0 and use APPLICATION riders.)
        Rider Activities: [] # Activity IDs on this Item.
        Rider Effects: [] # Effect IDs on this Item, or relative UUIDs such as .ActiveEffect.<id>.
        Rider Items: [] # Item UUIDs.

      CHANGES:
        - Attribute Key: "[text]"
          Change ID: n/a
          Conditions: {}
          Replacement: "" # Empty, origin, or target.
          Change Type: "[custom|multiply|add|subtract|downgrade|upgrade|override]"
          Change Phase: "[initial|final]"
          Value: 0 # Any YAML scalar, null, list, or mapping; preserve its native type.
          Priority: "[number|n/a]"

    # Alternative to an inline definition: reference a real existing effect.
    - Effect UUID: "[text|n/a]"
      APPLICATION:
        Level Minimum: null
        Level Maximum: null

  RESTRICTIONS:
    Item Type: "[Any Enchantable Type|container|consumable|equipment|feat|loot|spell|tool|weapon]"
    # Pick the Valid Categories and Valid Properties values that match the selected Item Type.
    # See the field reference below for item-type-specific allowed values.
    Valid Categories: "[comma-separated values for Item Type|n/a]"
    Valid Properties: "[comma-separated values for Item Type|n/a]"
    Allow Magical: "[true|false]"
```

### ACTIVITY_ENCHANT: rules

APPLIED_EFFECTS supplies at least one enchantment profile. Its changes apply to the selected Item, not to an Actor. Restrictions and Automatically Self Enchant control eligibility and selection. Rider lists can be stored in two places: APPLICATION on the activity's effect reference (every supported dnd5e version) or, on dnd5e 6.1 or later, EFFECT_SYSTEM on the enchantment effect itself. Use one place for a given rider. Actor-effect keys are not interchangeable with Item-enchantment keys.

### ACTIVITY_ENCHANT: example

Apply a one-minute +1 magical bonus enchantment to the parent weapon. The effect changes the Item, and is not an Actor AC bonus.

Use on a weapon Item.

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
```

### ACTIVITY_ENCHANT: check before you answer

These checks are for `ACTIVITY_ENCHANT`. The checks for every activity are in "ACTIVITY_* common sections: check before you answer".

1. Enchantment changes act on the target Item. Use Item keys (`system.magicalBonus`, `system.armor.magicalBonus`, `system.properties`, `system.damage.parts`, `name`, `activities[attack]...`). An Actor key such as `system.attributes.ac.bonus` is accepted by the parser but does nothing on an Item. A benefit for the wielder is not an enchantment change.
2. Give every `APPLIED_EFFECTS` entry `DETAILS.Effect Type: enchantment` and `DETAILS.Apply Effect to Actor: false`. `Effect Type: condition` is a parse error here, and so are `Conditions` or `Rider Statuses` under the `EFFECT_SYSTEM` of an enchantment.
3. dnd5e applies a magical bonus only to an Item that has the magical property. When a nonmagical weapon or armor gets `system.magicalBonus` or `system.armor.magicalBonus`, also add the row `Attribute Key: system.properties`, `Change Type: add`, `Value: mgc`.
4. `RESTRICTIONS.Item Type` is one of `weapon`, `equipment`, `consumable`, `tool`, `container`, `feat`, `loot`, `spell` or `Any Enchantable Type`. There is no `armor` type: use `equipment` with `Valid Categories: light, medium, heavy, shield`.
5. Write `Change Type` in lower case (`override`, `add`, `upgrade`, `downgrade`). The key tables print these names with a capital letter, but a capitalised value is stored as written and is not a valid type. A row with no `Change Type` is stored as `custom`. Neither case produces a warning.
6. Write one profile per option. If the bonus depends on slot level or class level, write one `APPLIED_EFFECTS` entry per tier and bound it with `APPLICATION.Level Minimum` and `Level Maximum`. Leave every rider list as `[]` unless you were given real IDs. A rider is an activity, effect or Item that the enchanted Item gains while enchanted.

## ACTIVITY_SUMMON: Summon activity

- **Root key:** `ACTIVITY_SUMMON`
- **When to use:** Use ACTIVITY_SUMMON when the feature brings one or more creatures into play as their own tokens: "you call forth a bestial spirit", "you summon a beast of challenge rating 2 or lower", "eight beasts of challenge rating 1/4 or lower appear", a familiar or companion that appears next to the user. The stat block must exist as an Actor, or be picked from a compendium by challenge rating and type at use time.
- **Smallest working block:** `ACTIVITY.Name`, `SUMMONS_PROFILES.Mode`, and one `SUMMONS_PROFILES.PROFILES` entry. With `Mode: By Direct Link` the entry needs `Actor UUID` (the creature) and should carry `Count` (how many) and `Display Name` (label in the dialog); without the UUID the block parses with a warning and cannot be imported until the Actor is resolved. With `Mode: By Challenge Rating & Type` the entry needs `CR` (highest challenge rating) and should carry `Count` and `Creature Types`; it needs no UUID.
- **Parent Item:** Usually a spell or a feat. Direct-link mode needs the real UUID of an existing Actor (world Actor or compendium entry); the importer does not create the stat block. CR mode needs no Actor in advance: the user picks one in the compendium browser at use time.

**Use another block when:**

- The user or a target turns into the creature (Wild Shape, Polymorph): write ACTIVITY_TRANSFORM.
- The text creates an object or hazard with a save or damage and no creature stat block (a wall of fire, a floating weapon that attacks): write ACTIVITY_SAVE, ACTIVITY_ATTACK or ACTIVITY_DAMAGE.
- Casting an existing summoning spell Item from another Item: write ACTIVITY_CAST.

### ACTIVITY_SUMMON: from source text to YAML

Find the wording closest to the source and write the fields shown. A path such as `ACTIVATION.Activation Type` means the `Activation Type` key inside the `ACTIVATION` section of `ACTIVITY_SUMMON`. List sections take one `-` entry per item. Leave out every section the source does not mention. The rows for the common sections (activation, uses and charges, range, targets, areas, duration, applied effects) are in "ACTIVITY_* common sections: from source text to YAML".

| The source says | Write |
| --- | --- |
| "you summon a wolf" or "it uses the Bestial Spirit stat block" | `SUMMONS_PROFILES.Mode: By Direct Link`; `PROFILES.Count: "1"`; `PROFILES.Display Name: Wolf`; `PROFILES.Actor UUID: Compendium.dnd5e.actors24.Actor.mmWolf0000000000` (the template's bundled example; replace it with the real Actor UUID) |
| "choose Air, Land, or Water" and no Actor UUIDs were supplied | one `PROFILES` entry per choice: `PROFILES.Count: "1"`; `PROFILES.Display Name: Land`; `PROFILES.Actor UUID: n/a`; `PROFILES.Actor Name: Bestial Spirit (Land)` |
| "one beast of challenge rating 2 or lower" | `SUMMONS_PROFILES.Mode: By Challenge Rating & Type`; `PROFILES.Count: "1"`; `PROFILES.CR: "2"`; `PROFILES.Creature Types: [beast]` |
| "eight beasts of challenge rating 1/4 or lower" as a further option | another `PROFILES` entry: `PROFILES.Count: "8"`; `PROFILES.CR: "0.25"`; `PROFILES.Creature Types: [beast]` |
| "the creature is friendly to you and your companions" | `CREATURE_CHANGES.Match Disposition: true` |
| "it uses your proficiency bonus" | `CREATURE_CHANGES.Match Proficiency: true` |
| "AC 11 + the level of the spell", "hit points 30 + 5 for each spell level above 2nd" | `CREATURE_CHANGES.Bonus Armor Class: "@item.level"`; `CREATURE_CHANGES.Bonus Hit Points: "5 * (@item.level - 2)"` (the Actor keeps the base 11 and 30) |
| "your spell attack modifier to hit", "your spell save DC", "+ the spell's level" to damage | `ITEM_CHANGES.Match Attacks: true`; `ITEM_CHANGES.Match Saves: true`; `ITEM_CHANGES.Bonus Attack Damage: "@item.level"` |
| "when you cast this spell using a slot of 6th level or higher, you can summon ..." (an option that exists only from that level) | `PROFILES.Summons Level Limit Minimum: 6` on that profile |

### ACTIVITY_SUMMON: field reference

The sections only `ACTIVITY_SUMMON` has, in full, and the common sections it takes, by name. A placeholder such as `"[true|false]"` lists the choices; it is not a value to copy. Write only what the source needs.

```yaml
ACTIVITY_SUMMON:
  # Also takes these common sections, written as in "ACTIVITY_* common sections": ACTIVITY, CHAT_FLAVOR, VISIBILITY, ACTIVATION, DURATION, CONSUMPTION, SCALING, USAGE, RECOVERY, RANGE, TARGETS, APPLIED_EFFECTS, APPLIED_BEHAVIORS

  BEHAVIOR:
    Consume Spell Slot: "[true|false]"
    Measured Template Prompt: "[true|false]"
    Summon Prompt: "[true|false]"

  SUMMONS_PROFILES:
    Mode: "[By Direct Link|By Challenge Rating & Type]"
    PROFILES:
      # (Optional, repeatable)
      - Profile ID: n/a
        Count: "[formula|number|n/a]"
        Display Name: "[text|n/a]"
        # (Required only if Mode is By Direct Link - resolved via drag-and-drop in the importer UI)
        Actor UUID: "[text|n/a]"
        # (Optional hint for the drop zone - helps identify which actor to link)
        Actor Name: "[text|n/a]"
        # (Required only if Mode is By Challenge Rating & Type)
        CR: "[formula|number|n/a]"
        # (Required only if Mode is By Challenge Rating & Type)
        Creature Types: [] # Optional list: aberration, beast, celestial, construct, dragon, elemental, fey, fiend, giant, humanoid, monstrosity, ooze, plant, undead.
        Summons Level Limit Minimum: "[integer|n/a]"
        Summons Level Limit Maximum: "[integer|n/a]"

  CREATURE_CHANGES:
    Match Disposition: "[true|false]"
    Match Proficiency: "[true|false]"
    Bonus Armor Class: "[formula|number|n/a]"
    Bonus Hit Dice: "[formula|number|n/a]"
    Bonus Hit Points: "[formula|number|n/a]"
    Temporary Hit Points: "[formula|number|n/a]"
    Creature Sizes: [] # Optional list: tiny, sm, med, lg, huge, grg.
    Creature Types: [] # Optional list: aberration, beast, celestial, construct, dragon, elemental, fey, fiend, giant, humanoid, monstrosity, ooze, plant, undead.

  ITEM_CHANGES:
    Match Ability: "[Default (strength)|str|dex|con|int|wis|cha]"
    Match Attacks: "[true|false]"
    Match Saves: "[true|false]"
    Bonus Attack Damage: "[formula|number|n/a]"
    Bonus Save Damage: "[formula|number|n/a]"
    Bonus Healing: "[formula|number|n/a]"
```

### ACTIVITY_SUMMON: rules

Direct-link profiles require real accessible Actor UUIDs. CR-selection profiles require an appropriate CR expression and an eligible world/compendium selection at use time; they are not fixed Actor references. Count is a formula. Profiles, creature changes and item changes have different targets. Importing this activity does not itself create summoned creatures.

### ACTIVITY_SUMMON: example

Offer one bundled 2024 Wolf for summoning and match its disposition to the summoner. Import validation does not create the summoned token.

Requires the dnd5e.actors24 compendium and its Wolf Actor. Resolve or replace this intentional bundled reference with a real Actor UUID if unavailable.

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
```

### ACTIVITY_SUMMON: check before you answer

These checks are for `ACTIVITY_SUMMON`. The checks for every activity are in "ACTIVITY_* common sections: check before you answer".

1. Never invent an `Actor UUID`. If none was supplied, write `Actor UUID: n/a` and the creature's exact name in `Actor Name`; the user links the Actor in the importer. The parser warns until every direct-link profile has a UUID.
2. Always write `SUMMONS_PROFILES.Mode`, spelled exactly `By Direct Link` or `By Challenge Rating & Type`. A missing Mode means direct link, so CR profiles then raise the missing-UUID warning. One activity uses one mode for all its profiles.
3. Do not describe the creature's attacks, AC or hit points in this block. They belong to the Actor. `CREATURE_CHANGES` and `ITEM_CHANGES` add to the Actor's values or match them to the summoner: write the scaling part (`"@item.level"`), not the full total.
4. Use ids, not words: sizes `tiny`, `sm`, `med`, `lg`, `huge`, `grg`; creature types in lower case and singular (`beast`, `fey`); `Match Ability` from `str` to `cha`. An unknown value is dropped with a warning.
5. `PROFILES.Creature Types` limits which creatures can be picked in CR mode. `CREATURE_CHANGES.Creature Types` and `Creature Sizes` change the type and size of the creature that is summoned. Do not use one for the other.
6. Write a fractional challenge rating as a decimal (`"0.25"`, `"0.5"`).

## ACTIVITY_TRANSFORM: Transform activity

- **Root key:** `ACTIVITY_TRANSFORM`
- **When to use:** Use ACTIVITY_TRANSFORM when a creature takes on another creature's stat block or one of several named forms: "you magically assume the shape of a beast that you have seen" (Wild Shape), "transforms a creature into a new form" (Polymorph), a lycanthrope's hybrid and animal forms, or a feature that lets the user choose between forms that each grant a set of bonuses.
- **Smallest working block:** `ACTIVITY.Name`, `TRANSFORMATION_PROFILES.Mode`, one `PROFILES` entry (`Actor UUID` with `Mode: By Direct Link`, `CR` with `Mode: By Challenge Rating`), and `TRANSFORMATION_DETAILS.Preset` (which traits the creature keeps). A direct-link profile without a UUID parses with a warning and cannot be imported until the Actor is resolved. With `Mode: form` no profile is needed, but at least one `APPLIED_EFFECTS` entry is required.
- **Parent Item:** Usually a feat (a class feature such as Wild Shape) or a spell. Direct-link mode needs the real UUID of an existing Actor. CR mode lets the user pick a creature in the compendium browser at use time. Form mode needs no Actor.

**Use another block when:**

- A creature appears next to the user as a separate token: write ACTIVITY_SUMMON.
- The user gains one fixed set of bonuses for a time, with no choice of form (a rage, a stance): write ACTIVITY_UTILITY with APPLIED_EFFECTS, or EFFECT.
- An Item is changed, not a creature: write ACTIVITY_ENCHANT.

### ACTIVITY_TRANSFORM: from source text to YAML

Find the wording closest to the source and write the fields shown. A path such as `ACTIVATION.Activation Type` means the `Activation Type` key inside the `ACTIVATION` section of `ACTIVITY_TRANSFORM`. List sections take one `-` entry per item. Leave out every section the source does not mention. The rows for the common sections (activation, uses and charges, range, targets, areas, duration, applied effects) are in "ACTIVITY_* common sections: from source text to YAML".

| The source says | Write |
| --- | --- |
| "you magically assume the shape of a beast" (Wild Shape) | `TRANSFORMATION_DETAILS.Preset: wildshape`; `TRANSFORMATION_DETAILS.Customize: false` |
| "transforms a creature that you can see into a new form" (Polymorph) | `TRANSFORMATION_DETAILS.Preset: polymorph` (write `polymorphSelf` when the user transforms themself) |
| "a beast of challenge rating 1 or lower" | `TRANSFORMATION_PROFILES.Mode: By Challenge Rating`; `PROFILES.CR: "1"`; `PROFILES.Creature Types: [beast]` |
| "that doesn't have a flying or swimming speed" | `PROFILES.Restricted Movement Types: [fly, swim]` |
| "at 4th level, challenge rating 1/2 or lower" (limits that change with level) | one `PROFILES` entry per tier: `PROFILES.CR: "0.5"`; `PROFILES.Transform Level Limit Minimum: 4`; `PROFILES.Transform Level Limit Maximum: 7` (with `VISIBILITY.Class Identifier: druid` the limits use the druid level) |
| "you turn into a wolf", "hybrid form or tiger form" (named stat blocks) | `TRANSFORMATION_PROFILES.Mode: By Direct Link`; `PROFILES.Display Name: Wolf`; `PROFILES.Actor UUID: Compendium.dnd5e.actors24.Actor.mmWolf0000000000` (the template's bundled example; replace it with the real Actor UUID, or write `n/a` and add `PROFILES.Actor Name: Wolf`) |
| a keep or replace rule that no preset matches, such as "you keep your Intelligence, Wisdom, and Charisma scores" | `TRANSFORMATION_DETAILS.Customize: true`; `TRANSFORMATION_DETAILS.CUSTOM OPTIONS.KEEP.Mental Abilities: true` (then list every other kept trait under `KEEP`) |
| "you gain a number of temporary hit points equal to your druid level" | `TRANSFORMATION_DETAILS.Customize: true`; `TRANSFORMATION_DETAILS.CUSTOM OPTIONS.OTHER_OPTIONS.Temp Formula: "@classes.druid.levels"` |
| "choose one of the following forms", where each form is a set of bonuses and the creature keeps its own stat block | `TRANSFORMATION_PROFILES.Mode: form`; one `APPLIED_EFFECTS` entry per form with `DETAILS.Effect Type: base` and `DETAILS.Apply Effect to Actor: false`; `TRANSFORMATION_DETAILS.Formless: true` when the user can also return to no form |

### ACTIVITY_TRANSFORM: field reference

The sections only `ACTIVITY_TRANSFORM` has, in full, and the common sections it takes, by name. A placeholder such as `"[true|false]"` lists the choices; it is not a value to copy. Write only what the source needs.

```yaml
ACTIVITY_TRANSFORM:
  # Also takes these common sections, written as in "ACTIVITY_* common sections": ACTIVITY, CHAT_FLAVOR, VISIBILITY, ACTIVATION, DURATION, CONSUMPTION, SCALING, USAGE, RECOVERY, RANGE, TARGETS, APPLIED_BEHAVIORS

  TRANSFORMATION_PROFILES:
    Mode: "[By Direct Link|By Challenge Rating|form]" # form changes the user with an applied effect instead of an Actor.
    PROFILES:
      # (Optional, repeatable)
      - Profile ID: n/a
        Display Name: "[text|n/a]"
        # (Required only if Mode is By Direct Link - resolved via drag-and-drop in the importer UI)
        Actor UUID: "[text|n/a]"
        # (Optional hint for the drop zone - helps identify which actor to link)
        Actor Name: "[text|n/a]"
        Transform Level Limit Minimum: "[integer|n/a]"
        Transform Level Limit Maximum: "[integer|n/a]"
        # (Required only if Mode is By Challenge Rating)
        CR: "[formula|number|n/a]"
        # (Optional filters when using By Challenge Rating)
        Creature Sizes: [] # Optional list: tiny, sm, med, lg, huge, grg.
        # (Optional filters when using By Challenge Rating)
        Creature Types: [] # Optional list: aberration, beast, celestial, construct, dragon, elemental, fey, fiend, giant, humanoid, monstrosity, ooze, plant, undead.
        # (Required only if Mode is By Challenge Rating)
        Restricted Movement Types: [] # Optional list: walk, burrow, climb, fly, swim.

  TRANSFORMATION_DETAILS:
    Formless: false # Whether form mode can return to having no active form.
    Preset: "[Default|polymorphSelf|polymorph|wildshape]"
    Customize: "[true|false]"

    # (Stored even when Customize is false; use CUSTOM OPTIONS: null instead to preserve a null settings object)
    CUSTOM OPTIONS:
      KEEP:
        Physical Abilities: "[true|false]"
        Mental Abilities: "[true|false]"
        Save Proficiencies: "[true|false]"
        Skill Proficiencies: "[true|false]"
        Gear Proficiency: "[true|false]"
        Languages: "[true|false]"
        Proficiency Bonus: "[true|false]"
        Features: "[true|false]"
        Equipment: "[true|false]"
        Spells: "[true|false]"
        Biography: "[true|false]"
        Creature Type: "[true|false]"
        Hit Points & Hit Dice: "[true|false]"
        Temp HP: "[true|false]"
        Damage Resistances: "[true|false]"
        Vision: "[true|false]"
        Self: "[true|false]"

      MERGE:
        Save Proficiencies: "[true|false]"
        Skill Proficiencies: "[true|false]"

      ACTIVE_EFFECTS:
        All Effects: "[true|false]"
        # (Required only if All Effects is False)
        This Actor: "[true|false]"
        # (Required only if All Effects is False)
        Other Actors: "[true|false]"
        # (Required only if All Effects is False)
        Background Effects: "[true|false]"
        # (Required only if All Effects is False)
        Class Effects: "[true|false]"
        # (Required only if All Effects is False)
        Feature Effects: "[true|false]"
        # (Required only if All Effects is False)
        Equipment Effects: "[true|false]"
        # (Required only if All Effects is False)
        Spell Effects: "[true|false]"

      OTHER_OPTIONS:
        Other Settings: [] # Native retained setting IDs; preserve supplied entries.
        Settings Preset: null # Nullable stored settings preset, independent of the activity Preset.
        Minimum Armor Class: "[formula|number|n/a]"
        Retained Spell Lists: [] # Optional list: class:bard, class:cleric, class:druid, class:paladin, class:ranger, class:sorcerer, class:warlock, class:wizard.
        Temp Formula: "[formula|number|n/a]"
        Transform Tokens: "[true|false]"
  BEHAVIOR:
    Measured Template Prompt: "[true|false]"
    Consume Spell Slot: "[true|false]"

  APPLIED_EFFECTS:
    # (Optional, repeatable)
    - DETAILS:
        Effect ID: n/a
        Icon: n/a
        Effect Type: base
        Origin: n/a
        Sort: 0
        Show Icon: "[0|1|2]"
        Name: "[text|n/a]"
        Icon Tint Color: "[text|n/a]" # (a Hex code color format e.g. #ff0000)
        Effect Suspended: "[true|false]"
        Apply Effect to Actor: "[true|false]"
        Status Conditions: []

      EFFECT_DESCRIPTION:
        Effect Description: |
          [multiline text content]

      DURATION:
        Value: "[integer|null|n/a]"
        Units: "[seconds|rounds|turns|minutes|hours|days|months|years]"
        Effect Expiry: null
        Effect Expired: "[true|false]"
        Combat Encounter: n/a
        Combatant: n/a
        Initiative: "[number|null|n/a]"
        Effect Start Time: "[integer|n/a]"
        Effect Start (combat) Rounds: "[integer|null|n/a]"
        Effect Start (combat) Turns: "[integer|null|n/a]"

      APPLICATION:
        Level Minimum: "[integer|n/a]"
        Level Maximum: "[integer|n/a]"

      EFFECT_SYSTEM:
        Origin:
          Activity: null
          Actor: null
          Behavior: null
          Effect: null
          Item: null
          Message: null
          Profile: null
        Magical: false
        Conditions: {}
        Rider Statuses: []

      CHANGES:
        # (Optional, repeatable)
        - Attribute Key: "[text|n/a]" # (Common keys found here: https://github.com/foundryvtt/dnd5e/wiki/Active-Effect-Guide)
          Change ID: n/a
          Conditions: {}
          Replacement: "" # Empty, origin, or target.
          Change Type: "[custom|multiply|add|subtract|downgrade|upgrade|override]"
          Change Phase: "[initial|final]"
          Value: 0 # Any YAML scalar, null, list, or mapping; preserve its native type.
          Priority: "[number|n/a]"

    # Alternative to an inline definition: reference an existing standalone native ActiveEffect.
    - Effect UUID: "[text|n/a]"
      APPLICATION:
        Level Minimum: null
        Level Maximum: null
```

### ACTIVITY_TRANSFORM: rules

Direct-link transformation requires a real Actor UUID. The transformation preset and Customize toggle are separate; CUSTOM OPTIONS values remain stored while Customize is false. Use CUSTOM OPTIONS:null only to preserve an actual null settings object. CR profiles remain contextual. Transform form mode uses APPLIED_EFFECTS as form profiles instead of Actor profiles. Set TRANSFORMATION_PROFILES.Mode: form; Formless controls returning to no form. Applied effects remain stored in other modes. Source class/resource restrictions must be authored separately or retained for manual handling.

### ACTIVITY_TRANSFORM: example

Use the bundled 2024 Wolf as a self-transformation profile with the wildshape preset. This example does not implement class-level or resource restrictions.

Requires the dnd5e.actors24 compendium and its Wolf Actor. Resolve or replace this intentional bundled reference with a real Actor UUID if unavailable.

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
```

### ACTIVITY_TRANSFORM: check before you answer

These checks are for `ACTIVITY_TRANSFORM`. The checks for every activity are in "ACTIVITY_* common sections: check before you answer".

1. Never invent an `Actor UUID`. If none was supplied, write `Actor UUID: n/a` and the creature's exact name in `Actor Name`; the user links the Actor in the importer.
2. Always write `TRANSFORMATION_PROFILES.Mode`: `By Direct Link`, `By Challenge Rating` or `form`. The Summon spelling `By Challenge Rating & Type` is not accepted here. A missing Mode means direct link.
3. `Preset` is case-sensitive: `Default`, `wildshape`, `polymorph` or `polymorphSelf`.
4. With `Customize: false` the preset decides everything and `CUSTOM OPTIONS` is only stored. With `Customize: true` the preset is no longer applied: under `CUSTOM OPTIONS.KEEP` write `true` for every trait the creature keeps, and fill `MERGE`, `ACTIVE_EFFECTS` and `OTHER_OPTIONS` as the rules require.
5. Use ids: movement `walk`, `burrow`, `climb`, `fly`, `swim`; sizes `tiny`, `sm`, `med`, `lg`, `huge`, `grg`. `Restricted Movement Types` lists movement the new form must not have.
6. With `Mode: form` the forms are `APPLIED_EFFECTS` entries and `PROFILES` is not used. In the other modes do not describe the new form in this block: the Actor stat block supplies it.

## ACTIVITY_FORWARD: Forward activity

- **Root key:** `ACTIVITY_FORWARD`
- **When to use:** Use ACTIVITY_FORWARD when the rules give a second way to pay for an effect that another activity on the same Item already defines: "...or you can expend 3 charges to do the same", "once per long rest you can use this without expending a use", "you can expend 1 to 3 charges". Forward runs the other activity and applies its own consumption, uses and scaling in place of that activity's.
- **Smallest working block:** `ACTIVITY.Name` (label of this payment path) and `FORWARD_TARGET.Activity` (which activity to run). A block without `FORWARD_TARGET.Activity` is a parse error. Add `CONSUMPTION`, `USAGE` or `SCALING` for what this path costs.
- **Parent Item:** Any Item that can host activities. Another activity that is not a Forward must exist on the same Item, or be written in the same YAML answer, and it needs a name that no other activity on the Item shares.

**Use another block when:**

- The effect itself. Write it once in the matching block (ACTIVITY_SAVE, ACTIVITY_ATTACK, ACTIVITY_HEAL, ACTIVITY_UTILITY and so on), then point the Forward at it.
- A different effect, damage, range, target or duration for the second option. Forward cannot change those: write a second full activity.
- An effect that has only one cost. Put the cost in that activity's own CONSUMPTION; no Forward is needed.

### ACTIVITY_FORWARD: from source text to YAML

Find the wording closest to the source and write the fields shown. A path such as `ACTIVATION.Activation Type` means the `Activation Type` key inside the `ACTIVATION` section of `ACTIVITY_FORWARD`. List sections take one `-` entry per item. Leave out every section the source does not mention. The rows for the common sections (activation, uses and charges, range, targets, areas, duration, applied effects) are in "ACTIVITY_* common sections: from source text to YAML".

| The source says | Write |
| --- | --- |
| "...or you can expend 3 charges to do the same" (a second cost for an effect that the activity named Flame Burst defines) | `ACTIVITY.Name: Flame Burst (3 Charges)`; `FORWARD_TARGET.Activity: Flame Burst` (the exact `ACTIVITY.Name` of the other activity; put the new cost in this block's `CONSUMPTION`) |
| the effect is not yet an activity on the Item | write the target block in the same YAML answer, separated by `---` (for example `ACTIVITY_SAVE` with `ACTIVITY.Name: Flame Burst`), then `FORWARD_TARGET.Activity: Flame Burst` |
| "once per long rest you can use this without expending a charge" | `FORWARD_TARGET.Activity: Flame Burst`; `CONSUMPTION.Consumption Type: activityUses`; `USAGE.Uses Max: "1"`; `RECOVERY.Period: lr`; `RECOVERY.Type: recoverAll` (the Forward's own use pool replaces the target's cost) |
| "you can expend 1 to 3 charges", and the target activity's damage scales with the number spent | `SCALING.Allow Scaling: true`; `SCALING.Max Scaling: "3"` (the Forward supplies the scaling value; the scaling of the damage stays on the target activity) |
| the 16-character ID of the target activity is known | `FORWARD_TARGET.Activity: jdRTb04FngE1B8cF` (the template's sample ID; write the real one) |

### ACTIVITY_FORWARD: field reference

The sections only `ACTIVITY_FORWARD` has, in full, and the common sections it takes, by name. A placeholder such as `"[true|false]"` lists the choices; it is not a value to copy. Write only what the source needs.

```yaml
ACTIVITY_FORWARD:
  # Also takes these common sections, written as in "ACTIVITY_* common sections": ACTIVITY, CHAT_FLAVOR, VISIBILITY, CONSUMPTION, SCALING, USAGE, RECOVERY, APPLIED_BEHAVIORS

  ACTIVATION:
    Override Activation: "[true|false]"
    Activation Type: "[action|bonus|reaction|minute|hour|day|longRest|shortRest|encounter|turnStart|turnEnd|legendary|mythic|lair|crew|special|None|n/a]"
    Activation Cost: "[integer|n/a]"
    Condition: "[text|n/a]"

  FORWARD_TARGET:
    Activity: "[text]" # (Exact ID, identifier, or unique exact name of an activity on the same item)
  BEHAVIOR:
    Consume Spell Slot: "[true|false]"
```

### ACTIVITY_FORWARD: rules

Forward has no native duration field; configure duration on the activity it forwards to. Resolve its reference with attachment planning before schema validation: raw name references are not native 16-character IDs.

FORWARD_TARGET.Activity may identify a non-Forward activity by exact ID, identifier or unique exact name. The target must exist on the Item or in the same attachment batch. Ambiguous, missing, Forward-to-Forward and cyclic targets block planning. Forward does not support APPLIED_EFFECTS. Native Forward resolution differs from MIDI trigger/Other Activity resolution; in reviewed MIDI 14.6.11 Forward forces target confirmation to never.

### ACTIVITY_FORWARD: example

Forward to the Current Reading utility activity included in this example batch. Forward is listed first to exercise order-independent planning.

Both blocks belong to one attachment batch on the same Item. Keep the unique Current Reading name; do not replace it with an invented UUID.

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

### ACTIVITY_FORWARD: check before you answer

These checks are for `ACTIVITY_FORWARD`. The checks for every activity are in "ACTIVITY_* common sections: check before you answer".

1. `FORWARD_TARGET.Activity` must name an activity that exists on the same Item or in the same YAML answer: its exact `ACTIVITY.Name`, its 16-character ID or its identifier. Never invent an ID, and do not write a UUID here.
2. The name must be unique on the Item. If two activities share it, the reference is ambiguous and import planning stops.
3. Do not point a Forward at another Forward or at itself.
4. Do not write `DURATION`, `RANGE`, `TARGETS`, `APPLIED_EFFECTS` or any attack, save or damage section in a Forward block. Forward has none of them. `APPLIED_EFFECTS` and the attack, save and damage sections are skipped and shown as a warning in the preview; `DURATION`, `RANGE` and `TARGETS` are accepted without a warning and do nothing. Change those on the target activity.
5. The Forward's `CONSUMPTION` replaces the target activity's consumption for this path. It is not added to it. Write the full cost of this path.

## ACTIVITY_TELEPORT: Teleport activity

- **Root key:** `ACTIVITY_TELEPORT`
- **When to use:** Use ACTIVITY_TELEPORT when the feature moves a token to another place at once: "you teleport up to 30 feet to an unoccupied space that you can see", "you teleport yourself to any other spot within range", "you can teleport a number of feet equal to 10 times your proficiency bonus". The chat card gets a Teleport button that moves the selected tokens up to the stored distance.
- **Smallest working block:** `ACTIVITY.Name`, `TELEPORT_DETAILS.Override Teleport: true`, `TELEPORT_DETAILS.Teleport Distance` and `TELEPORT_DETAILS.Teleport Units` (how far the token may go). If the distance equals a range given in feet, miles, metres or kilometres, leave `TELEPORT_DETAILS` out and the distance follows `RANGE`.
- **Parent Item:** Usually a spell or a feat, sometimes equipment with a teleport power. Nothing else must exist. At use time the user selects the token or tokens to move and clicks Teleport on the chat card.

**Use another block when:**

- Damage or a save that comes with the teleport. Keep ACTIVITY_TELEPORT for the movement and add ACTIVITY_SAVE or ACTIVITY_DAMAGE on the same Item.
- Movement that is not teleportation (a speed bonus, flying, a push or a pull): write EFFECT with a speed key for a bonus, or describe forced movement in ACTIVITY_SAVE or ACTIVITY_UTILITY.
- Travel with no movement on the current scene (another plane, a far-off named place): write ACTIVITY_UTILITY with the description.

### ACTIVITY_TELEPORT: from source text to YAML

Find the wording closest to the source and write the fields shown. A path such as `ACTIVATION.Activation Type` means the `Activation Type` key inside the `ACTIVATION` section of `ACTIVITY_TELEPORT`. List sections take one `-` entry per item. Leave out every section the source does not mention. The rows for the common sections (activation, uses and charges, range, targets, areas, duration, applied effects) are in "ACTIVITY_* common sections: from source text to YAML".

| The source says | Write |
| --- | --- |
| "you teleport up to 30 feet to an unoccupied space that you can see" (a spell with range Self) | `TELEPORT_DETAILS.Override Teleport: true`; `TELEPORT_DETAILS.Teleport Distance: "30"`; `TELEPORT_DETAILS.Teleport Units: ft` |
| "you teleport yourself to any other spot within range" (range 500 feet) | `RANGE.Override Range: true`; `RANGE.Range Units: ft`; `RANGE.Range Value: 500`; `TELEPORT_DETAILS.Override Teleport: false` (the distance follows the range) |
| "a number of feet equal to 10 times your proficiency bonus" | `TELEPORT_DETAILS.Override Teleport: true`; `TELEPORT_DETAILS.Teleport Distance: "10 * @prof"`; `TELEPORT_DETAILS.Teleport Units: ft` |
| "up to 1 mile" | `TELEPORT_DETAILS.Override Teleport: true`; `TELEPORT_DETAILS.Teleport Distance: "1"`; `TELEPORT_DETAILS.Teleport Units: mi` |
| no distance limit on the scene ("to any place you can see") | `RANGE.Override Range: true`; `RANGE.Range Units: any`; `TELEPORT_DETAILS.Override Teleport: false` |
| "...and you are invisible until the start of your next turn" (an effect that comes with the teleport) | an `APPLIED_EFFECTS` entry: `DETAILS.Name: Blink Veil`; `DETAILS.Effect Type: base`; `DETAILS.Apply Effect to Actor: false`; `DETAILS.Status Conditions: [invisible]`; `DURATION.Value: 1`; `DURATION.Units: rounds` |
| "each creature within 10 feet of the space you left takes 3d10 thunder damage" | nothing in `TELEPORT_DETAILS`; write a separate `ACTIVITY_SAVE` block for the damage after `---` |

### ACTIVITY_TELEPORT: field reference

The sections only `ACTIVITY_TELEPORT` has, in full, and the common sections it takes, by name. A placeholder such as `"[true|false]"` lists the choices; it is not a value to copy. Write only what the source needs.

```yaml
ACTIVITY_TELEPORT:
  # Also takes these common sections, written as in "ACTIVITY_* common sections": ACTIVITY, CHAT_FLAVOR, BEHAVIOR, VISIBILITY, ACTIVATION, DURATION, CONSUMPTION, SCALING, USAGE, RECOVERY, RANGE, TARGETS, APPLIED_EFFECTS, APPLIED_BEHAVIORS

  TELEPORT_DETAILS:
    # (Override Teleport false follows RANGE, where any means unlimited; true uses the local distance and an n/a distance means unlimited)
    Override Teleport: "[true|false]"
    Teleport Distance: "[formula|number|n/a]"
    Teleport Units: "[ft|mi|m|km|n/a]"
```

### ACTIVITY_TELEPORT: rules

TELEPORT_DETAILS stores the native teleport distance. With Override Teleport false the distance follows RANGE (any is unlimited) and the stored local distance stays inactive; with it true an omitted or n/a Teleport Distance means unlimited. Teleport Units accepts ft, mi, m or km; omit it to keep the native length default. The chat card offers a Teleport button that plans blink movement for the selected tokens up to that distance; the activity does not place tokens during import.

### ACTIVITY_TELEPORT: example

Teleport the user up to 30 feet to an unoccupied space they can see as a bonus action. The distance is set on the activity instead of following its range.

Use a parent Item that supports this activity. Teleporting moves the selected token; validation does not plan the movement.

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
```

### ACTIVITY_TELEPORT: check before you answer

These checks are for `ACTIVITY_TELEPORT`. The checks for every activity are in "ACTIVITY_* common sections: check before you answer".

1. `Teleport Distance` is used only with `Override Teleport: true`. A distance written without the override is stored but inactive, and the parser does not warn.
2. With `Override Teleport: false` the distance comes from the range. A range of `self`, `touch` or `spec` gives a distance of 0 and no Teleport button. For a power with range Self that moves the user, set `Override Teleport: true` and the distance.
3. Write the number or formula in `Teleport Distance` and the unit in `Teleport Units` (`ft`, `mi`, `m`, `km`). Do not write `30 feet` in the distance or `feet` as the unit.
4. `Override Teleport: true` with no distance means unlimited, and the parser warns about it. Do that only on purpose.
5. Teleport only moves tokens. Do not add attack, save or damage sections to this block.
6. The block does not store who is moved: dnd5e moves the tokens the user has selected. State "you and one willing creature" in `TARGETS` and the description, not in `TELEPORT_DETAILS`.

## EFFECT: Active Effect

- **Root key:** `EFFECT`
- **When to use:** Use EFFECT for an Active Effect stored on the Item itself: a lasting change for the Item's owner ("while you wear this cloak, you gain a +1 bonus to AC", "you have resistance to fire damage", "your speed increases by 10 feet"), a condition the Item imposes, or one effect that several activities share. Use it also when the parent Item cannot host activities (loot, container).
- **Smallest working block:** `DETAILS.Name`, `DETAILS.Apply Effect to Actor` (`true` = always on for the Item's owner, `false` = applied later by an activity), and either one `CHANGES` row with `Attribute Key`, `Change Type` and `Value`, or a `DETAILS.Status Conditions` list. Add `DURATION.Value` and `DURATION.Units` only when the effect ends after a set time.
- **Parent Item:** Any Item type, including loot and containers. A passive effect (`Apply Effect to Actor: true`) works once the Item is on an Actor. An effect with `Apply Effect to Actor: false` does nothing until an activity on the same Item applies it.

**Use another block when:**

- An effect that one activity applies to its targets ("on a failed save the target is poisoned for 1 minute"): write it inside that activity's APPLIED_EFFECTS (ACTIVITY_SAVE, ACTIVITY_ATTACK, ACTIVITY_UTILITY and so on), not as a separate EFFECT.
- A change to an Item (a magical bonus, extra weapon damage): write ACTIVITY_ENCHANT with an enchantment profile.
- Something the user rolls or spends (damage, healing, a check, charges): write the matching ACTIVITY_* block. An effect only changes stored values and statuses.

An effect is written in three places, always with the same sections: as a top-level `EFFECT` block pasted onto an existing Item, as an entry of a full Item's `effects` list (the sections without the `EFFECT:` line), and as an entry of an activity's `APPLIED_EFFECTS` list (also without the `EFFECT:` line).

### EFFECT: from source text to YAML

Find the wording closest to the source and write the fields shown. A path such as `DETAILS.Name` means the `Name` key inside the `DETAILS` section of the effect. `CHANGES` takes one `-` entry per change; 40-effect-keys.md lists the `Attribute Key` values.

| The source says | Write |
| --- | --- |
| "while you wear this ring, you gain a +1 bonus to AC" | `DETAILS.Apply Effect to Actor: true`; `CHANGES.Attribute Key: system.attributes.ac.bonus`; `CHANGES.Change Type: add`; `CHANGES.Change Phase: initial`; `CHANGES.Value: 1`; `CHANGES.Priority: 20`; no `DURATION` section |
| "your walking speed increases by 10 feet" | `CHANGES.Attribute Key: system.attributes.movement.speeds.walk`; `CHANGES.Change Type: add`; `CHANGES.Value: 10` |
| "you have resistance to fire damage", "you are immune to the poisoned condition" | `CHANGES.Attribute Key: system.traits.dr.value`; `CHANGES.Change Type: add`; `CHANGES.Value: fire`, and for the immunity `CHANGES.Attribute Key: system.traits.ci.value`; `CHANGES.Change Type: add`; `CHANGES.Value: poisoned` |
| "your Strength score is 19; it has no effect on you if your Strength is already 19 or higher" | `CHANGES.Attribute Key: system.abilities.str.value`; `CHANGES.Change Type: upgrade`; `CHANGES.Value: 19` |
| "a bonus to saving throws equal to your Charisma modifier" | `CHANGES.Attribute Key: system.rolls.ability.save.bonus`; `CHANGES.Change Type: add`; `CHANGES.Value: "@abilities.cha.mod"` |
| "the target is blinded for 1 minute" (an effect that an activity applies) | `DETAILS.Apply Effect to Actor: false`; `DETAILS.Status Conditions: [blinded]`; `DURATION.Value: 1`; `DURATION.Units: minutes` |
| "until the start of the target's next turn" | `DURATION.Value: 1`; `DURATION.Units: rounds`; `DURATION.Effect Expiry: targetStart` |
| the Item gives its owner a dnd5e condition, for example "you are poisoned while you carry it" | `DETAILS.Effect Type: condition`; `DETAILS.Apply Effect to Actor: true`; `DETAILS.Status Conditions: [poisoned]`; `EFFECT_SYSTEM.Condition Type: poisoned`; `EFFECT_SYSTEM.Level: null` (`Level` is a number from 1 to 6 only for `exhaustion`) |
| two activities on the Item apply the same effect | `DETAILS.Effect ID: sharedWard000001` (16 letters and digits that you choose); in each activity an `APPLIED_EFFECTS` entry that is only `Effect ID: sharedWard000001` |

### EFFECT: field reference

Every section and field an effect can have. A placeholder such as `"[true|false]"` lists the choices; it is not a value to copy. Write only what the source needs.

```yaml
EFFECT:
  DETAILS:
    Effect ID: n/a
    Icon: n/a
    Effect Type: base # base | enchantment | condition
    Origin: n/a
    Sort: 0
    Show Icon: "[0|1|2]"
    Name: "[text|n/a]"
    Icon Tint Color: "[text|n/a]" # (a Hex code color format e.g. #ff0000)
    Effect Suspended: "[true|false]"
    Apply Effect to Actor: "[true|false]"
    Status Conditions: []

  EFFECT_DESCRIPTION:
    Effect Description: |
      [multiline text content]

  DURATION:
    Value: "[integer|null|n/a]"
    Units: "[seconds|rounds|turns|minutes|hours|days|months|years]"
    Effect Expiry: null
    Effect Expired: "[true|false]"
    Combat Encounter: n/a
    Combatant: n/a
    Initiative: "[number|null|n/a]"
    Effect Start Time: "[integer|n/a]"
    Effect Start (combat) Rounds: "[integer|null|n/a]"
    Effect Start (combat) Turns: "[integer|null|n/a]"

  APPLICATION:
    Level Minimum: "[integer|n/a]"
    Level Maximum: "[integer|n/a]"

  EFFECT_SYSTEM:
    Origin:
      Activity: null
      Actor: null
      Behavior: null
      Effect: null
      Item: null
      Message: null
      Profile: null
    Magical: false
    Conditions: {}
    Rider Statuses: []
    # (Enchantment effects only, dnd5e 6.1 or later: riders added while the enchantment is applied; keep [] otherwise)
    Rider Activities: []
    Rider Effects: []
    Rider Items: []
    # (Condition effects only: Effect Type condition replaces Origin, Magical, Conditions and Rider Statuses with these two keys; n/a otherwise)
    Condition Type: n/a
    Level: n/a

  CHANGES:
    # (Optional, repeatable)
    - Attribute Key: "[text|n/a]" # (Common keys found here: https://github.com/foundryvtt/dnd5e/wiki/Active-Effect-Guide)
      Change ID: n/a
      Conditions: {}
      Replacement: "" # Empty, origin, or target.
      Change Type: "[custom|multiply|add|subtract|downgrade|upgrade|override]"
      Change Phase: "[initial|final]"
      Value: 0 # Any YAML scalar, null, list, or mapping; preserve its native type.
      Priority: "[number|n/a]"
```

### EFFECT: rules

Condition effects (`Effect Type: condition`) store `Condition Type` (a dnd5e condition id such as `poisoned` or `exhaustion`) and `Level` (leveled conditions only, exhaustion 1-6; otherwise `null`) instead of Origin, Magical, Conditions and Rider Statuses, which are rejected for them. Status Conditions defaults to the condition id when omitted. Condition effects belong on Items as standalone EFFECT blocks; `APPLIED_EFFECTS` accepts base effects only.

Use EFFECT for direct Effect-mode import. Under an Item's effects array, use the body beginning with DETAILS without another EFFECT wrapper. Under APPLIED_EFFECTS, use the same effect body and APPLICATION where supported. Apply Effect to Actor:true is passive transfer; false is appropriate for an effect applied through an activity. Prefer Status Conditions for ordinary conditions. Keys target different documents depending on normal effects versus enchantments.

### EFFECT: field values

#### EFFECT field values: DETAILS

| Field | Description | Accepted Values |
|---|---|---|
| `Name` | Display name for the effect. Appears on the actor sheet's effects list and in the effects panel. If `n/a`, defaults to "New Effect". | Any text, or `n/a`. |
| `Icon Tint Color` | Hex color tint overlay on the effect's icon. | Hex format: `#RGB`, `#RRGGBB`, or `#RRGGBBAA` (e.g. `#ff0000`), or `n/a`. |
| `Effect Suspended` | If `true`, the effect exists but its changes are not applied. Can be toggled on later. | `true` or `false`. |
| `Apply Effect to Actor` | If `true`, the effect's changes transfer from the item to the owning actor when equipped/attuned. If `false`, the effect stays on the item only (use for effects applied to targets via activities). | `true` or `false`. |
| `Status Conditions` | Native status IDs; any number can belong to one effect. | YAML list such as `[prone, restrained, frightened]`; `[]` applies none. |
| `Separate Status Conditions` | Legacy extra-condition input only. Prefer one Status Conditions list. | Same native status IDs; omit from canonical YAML. |

**Status Condition Values:** `dead`, `coverHalf`, `coverThreeQuarters`, `coverTotal`, `burrowing`, `concentrating`, `dodging`, `ethereal`, `flying`, `hiding`, `hovering`, `marked`, `sleeping`, `stable`, `bleeding`, `blinded`, `burning`, `charmed`, `cursed`, `dehydration`, `deafened`, `diseased`, `exhaustion`, `falling`, `frightened`, `grappled`, `incapacitated`, `invisible`, `malnutrition`, `paralyzed`, `petrified`, `poisoned`, `prone`, `restrained`, `silenced`, `stunned`, `suffocation`, `surprised`, `transformed`, `unconscious`, `encumbered`, `heavilyEncumbered`, `exceedingCarryingCapacity`

#### EFFECT field values: EFFECT SYSTEM

Native dnd5e data stored under `system`. Which keys apply depends on `Effect Type`: base effects accept all four shared keys, enchantments accept Origin and Magical, and condition effects accept only Condition Type and Level.

| Field | Description | Accepted Values |
|---|---|---|
| `Origin` | Typed native origin references (Activity, Actor, Behavior, Effect, Item, Message, Profile). Base and enchantment effects. | Mapping of UUID/ID strings or `null` values. |
| `Magical` | Whether the effect counts as magical for suppression. Base and enchantment effects. | `true` or `false`. |
| `Conditions` | Native filter deciding when the effect applies. Base effects only. | Filter mapping, valid JSON text, `""` or `null`. |
| `Rider Statuses` | Additional status ids applied together with this effect. Base effects only. | List of native status ids. |
| `Condition Type` | The dnd5e condition this effect represents. **Required for `Effect Type: condition`**; `n/a` for other types. | A registered condition id (`CONFIG.DND5E.conditionTypes`, e.g. `poisoned`, `prone`, `exhaustion`), or `n/a`. |
| `Level` | Level of a leveled condition (exhaustion supports 1-6). Condition effects only. | Positive integer, `null`, or `n/a`. |

Import preflight checks that the condition id is registered and that the level fits the condition. A condition stored on an Item reaches its owner through Apply Effect to Actor; dnd5e manages an actor's own exhaustion through its status toggles, not through Item effects.

#### EFFECT field values: EFFECT DESCRIPTION

| Field | Description | Accepted Values |
|---|---|---|
| `Effect Description` | Descriptive text displayed when inspecting the effect. Supports enricher syntax (see ENRICHER REFERENCE). Use a YAML literal block scalar after `Effect Description:` and indent every description line beneath that key. The value ends when indentation returns to the surrounding mapping. | Multiline text. |

#### EFFECT field values: DURATION

Use one canonical `Value` / `Units` pair. `Value: null` means no fixed duration; zero is a stored zero, not an omitted duration. Named start fields preserve the current start state. To store an entirely null start, use `Start: null` instead of all six named start fields.

| Field | Description | Accepted Values |
|---|---|---|
| `Value` | Duration in the selected unit. | Non-negative integer or `null`; `n/a` is accepted as unset. |
| `Units` | Native duration unit. | `years`, `months`, `days`, `hours`, `minutes`, `seconds`, `rounds`, `turns`; omitted default `seconds`. |
| `Effect Expiry` | Native event for expiry. | `combatStart`, `roundStart`, `turnStart`, `combatEnd`, `roundEnd`, `turnEnd`, `shortRest`, `longRest`, `sourceStart`, `sourceEnd`, `targetStart`, `targetEnd`, or `null`. |
| `Effect Expired` | Stored expiry state. | `true` or `false`. |
| `Start` | Alternative to the named start fields; clears the entire start object. | Only `null`; never combine with named start fields. |
| `Effect Start Time` | World-time start timestamp. | Integer, including zero; `n/a` leaves the native default. Explicit `null` is invalid. |
| `Effect Start (combat) Rounds` | Starting combat round. | Non-negative integer, `null`, or `n/a`. |
| `Effect Start (combat) Turns` | Starting combat turn. | Non-negative integer, `null`, or `n/a`. |
| `Combat Encounter` | Existing combat ID. | Valid 16-character alphanumeric ID or `null`; `n/a` clears it. |
| `Combatant` | Existing combatant ID. | Valid 16-character alphanumeric ID or `null`; `n/a` clears it. |
| `Initiative` | Starting initiative. | Finite number, including decimals, or `null`. |

Legacy `Effect Duration (Seconds)`, `Effect Duration (combat) Rounds`, and `Effect Duration (combat) Turns` remain accepted aliases. Do not combine populated legacy durations with `Value` / `Units`, or supply multiple legacy duration units.

**Common Duration Patterns:**

| Duration | Value | Units |
|---|---|---|
| 1 round | `1` | `rounds` |
| 1 minute of world time | `1` | `minutes` |
| 1 minute of combat | `10` | `rounds` |
| 10 minutes | `10` | `minutes` |
| 1 hour | `1` | `hours` |
| 8 hours | `8` | `hours` |
| 24 hours | `1` | `days` |
| No fixed duration | `null` | `seconds` |

#### EFFECT field values: CHANGES

Defines mechanical modifications to the actor's data. **Optional and repeatable** - an effect can have zero or multiple change entries. Each entry modifies a single attribute.

| Field | Description | Accepted Values |
|---|---|---|
| `Attribute Key` | Dot-notation data path of the attribute to modify. 40-effect-keys.md lists the keys for dnd5e 6. | Text (dot-notation path), or `n/a`. |
| `Change Type` | Native operation type. | `custom`, `multiply`, `add`, `subtract`, `downgrade`, `upgrade`, `override`. Registered custom string types require the matching runtime handler. dnd5e rule types are also accepted: `dnd5e.advantage`, `dnd5e.maximum` and `dnd5e.minimum` (Attribute Key `d20`, `attack`, `check` or `save`), `dnd5e.bonus` (those keys plus `damage` and `healing`), and on dnd5e 6.1 or later `dnd5e.damage.downgrade`, `dnd5e.damage.ignore.immunity`, `dnd5e.damage.ignore.resistance` and `dnd5e.damage.ignore.vulnerability` (Attribute Key `damage`, Value a damage type such as `fire`). |
| `Change Phase` | Native dnd5e application phase. | `initial` or `final`; omitted uses the native default. |

### EFFECT: examples

An applied one-minute effect adds 1 AC; it is not a passive transfer effect. Its expiry requires the world's normal duration processing.

Use Effect mode on a parent Item; apply the effect through an activity.

```yaml
EFFECT:
  DETAILS:
    Name: Guarded Stance
    Effect Type: base
    Effect Suspended: false
    Apply Effect to Actor: false
    Status Conditions: []
  EFFECT_DESCRIPTION:
    Effect Description: <p>Guarded Stance.</p>
  DURATION:
    Value: 1
    Units: minutes
  CHANGES:
    - Attribute Key: system.attributes.ac.bonus
      Change Type: add
      Change Phase: initial
      Value: 1
      Priority: 20
```

A condition effect (`Effect Type: condition`), written as a top-level `EFFECT` only:

```yaml
EFFECT:
  DETAILS:
    Name: Lingering Poison
    Effect Type: condition
    Effect Suspended: false
    Apply Effect to Actor: true
    Status Conditions: [poisoned]
  EFFECT_SYSTEM:
    Condition Type: poisoned
    Level: null
  DURATION:
    Value: 1
    Units: hours
  CHANGES: []
```

### EFFECT: check before you answer

1. Decide first who gets the effect. `Apply Effect to Actor: true` means the Item's owner has it all the time (a worn item, a feat, a trait). `false` means an activity applies it. Do not write `true` on an effect that an activity applies to a target. If you leave the field out, the effect is stored as passive.
2. Every `CHANGES` row needs `Attribute Key`, `Change Type` and `Value`. A row without `Attribute Key` is dropped, and a row without `Change Type` is stored as `custom` and does nothing. Neither produces a warning.
3. Take `Attribute Key` from this template's key list or from `Active Effect Keys V2.txt`, and replace every `{token}` (`system.skills.ste.roll.mode`, not `system.skills.{skillId}.roll.mode`). The parser does not check keys, so an invented path is imported and does nothing.
4. Give `Value` its real type: a number without quotes (`Value: 2`), an id as plain text (`Value: fire`), a formula in quotes (`Value: "@prof"`). `n/a` is stored as the text n/a. Write one row per damage type or condition.
5. `Change Type` is lower case: `add`, `override`, `upgrade`, `downgrade`, `multiply`, `subtract` or `custom`. Write `Change Phase: initial` unless the user supplies `final`. `Priority` may be left out; the examples use `20` with `add`.
6. Effect duration uses `DURATION.Value` with a plural unit in `Units` (`rounds`, `minutes`, `hours`). `Units: minute` and `Value: 1 minute` are parse errors. Leave `DURATION` out for a passive effect.
7. For an ordinary condition write its id in `DETAILS.Status Conditions` (`[poisoned]`, `[prone]`, lower case). `Effect Type: condition` is only for a standalone dnd5e condition effect: it needs `EFFECT_SYSTEM.Condition Type` and its own `Status Conditions` list, and `Origin`, `Magical`, `Conditions` and `Rider Statuses` are rejected on it.
8. Never invent a UUID or a 16-character ID. Leave `Effect ID` and `Change ID` out so they are allocated, and write a UUID only when the user supplied it.
9. `EFFECT_SYSTEM.Rider Activities`, `Rider Effects` and `Rider Items` belong to `Effect Type: enchantment` only and need dnd5e 6.1 or later. Leave them `[]` or leave them out on dnd5e 6.0 and on base effects.
