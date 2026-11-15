# Importer LLM Pack 14.4.0: start here

Pack 14.4.0 (2026-11-14) for 5e Item Importer 14.4.0 and 5e Activity Importer 14.4.0, on dnd5e 6.0.6 and 6.1.0 with Foundry VTT 14.370. The MIDI-QOL layer matches MIDI-QOL 14.6.11 and DAE 14.6.9 on dnd5e 6.0.6.

## Start here: what you are doing

You write YAML for two Foundry VTT modules for the dnd5e system. The user pastes your answer into the module's import window, clicks Preview, and imports it.

- **5e Item Importer** (free) imports Items: weapons, armor and other equipment, consumables, tools, loot, containers, spells and features.
- **5e Activity Importer** (premium) adds activities and Active Effects. An activity is what an Item does when it is used: an attack, a saving throw, healing, casting a spell. An effect is a lasting change, such as +1 AC or a condition. With both modules, one YAML document is a full Item with its activities and effects.

The importers read only the section and field names this pack lists, spelled exactly as written here. A name they do not know is skipped and shown as a warning, and its value is lost.

## Start here: the files of this pack

| File | Holds | Read it for |
| --- | --- | --- |
| `00-start-here.md` | This file: the kind of answer, composition, output rules, warnings | Every answer, first |
| `10-items.md` | Every Item type: root key, rules wording to fields, every field, value lists, a core example, checks | Every Item |
| `20-activities-and-effects.md` | Every activity type, the `EFFECT` block, and the common sections they share | Activities and effects (5e Activity Importer) |
| `30-midi-qol-and-dae.md` | The `AUTOMATION` layer for MIDI-QOL and DAE | Only when the user has MIDI-QOL active |
| `40-effect-keys.md` | The `Attribute Key` values of effect changes for dnd5e 6 | Every effect with `CHANGES` |
| `50-worked-examples.md` | Complete examples from rules text to finished YAML | The shape of an answer |

A user with only the free module may not have files 20, 30 and 40. Then write core Items only.

The files are built from the modules' templates, so some lines say "template". The template of an Item type is its section in 10-items.md. The Base template of an activity or effect is its section in 20-activities-and-effects.md, and its MIDI template is that section together with its section in 30-midi-qol-and-dae.md.

## Start here: choose the kind of answer

| The user | Write |
| --- | --- |
| has 5e Item Importer only, or has not said which modules they have | a **core Item**: one Item document with the complete rules text in its description, without `Activities` and `effects` |
| has 5e Activity Importer and wants the Item | a **full Item**: the core Item plus `Activities` and `effects` lists |
| wants to add activities or effects to an Item that already exists in Foundry | **blocks for an existing Item**: top-level `ACTIVITY_*` and `EFFECT` blocks, separated by `---`, for 5e Activity Importer |

When you do not know which modules the user has, write a core Item and add one line after the YAML saying that you can add activities and effects if they have 5e Activity Importer. If the user asks for a core Item, write one even when they have both modules. The kind of answer is never a YAML field.

## Start here: choose the Item root key

| The source is | Root key |
| --- | --- |
| something a creature attacks with: a sword, a bow, a natural weapon (bite, claw), an improvised or siege weapon, a magic staff that can be swung as a quarterstaff | `WEAPON` |
| armor, a shield, clothing, or a worn or held magic item that is not used up: a ring, wand, rod, cloak, boots, amulet, wondrous item | `EQUIPMENT` |
| something used up or carried as a stack: a potion, oil, scroll, ammunition, poison, food, dust | `CONSUMABLE` |
| artisan's tools, a kit, a gaming set, a musical instrument | `TOOL` |
| treasure, gems, art objects, trade goods, plain gear with nothing to activate | `LOOT` |
| a bag, chest, quiver or other holder of items | `CONTAINER` |
| a spell | `SPELL` |
| a class or subclass feature, species trait, feat, monster trait or action, boon | `FEAT` |

A wand or rod is `EQUIPMENT` with `ITEM.Equipment Type: wand` or `rod`. Its charges go in the Item's `USAGE` and `RECOVERY`, and each spell it can cast is one `ACTIVITY_CAST` that spends those charges. A staff is a `WEAPON` with `Base Weapon: quarterstaff`. An Item that casts spells is never a `SPELL`; a spell scroll is a `CONSUMABLE`.

## Start here: choose the activity root key

| The source describes | Root key |
| --- | --- |
| a roll to hit against Armor Class | `ACTIVITY_ATTACK` |
| a saving throw against a DC | `ACTIVITY_SAVE` |
| damage with no attack roll and no saving throw | `ACTIVITY_DAMAGE` |
| hit points or temporary hit points restored | `ACTIVITY_HEAL` |
| an ability, skill or tool check | `ACTIVITY_CHECK` |
| casting a spell that exists as its own spell Item | `ACTIVITY_CAST` |
| a change to an Item, such as a weapon that becomes +1 for a while | `ACTIVITY_ENCHANT` |
| creatures that appear as their own tokens | `ACTIVITY_SUMMON` |
| a creature takes another form | `ACTIVITY_TRANSFORM` |
| a token moves to another place at once | `ACTIVITY_TELEPORT` |
| a second way to pay for another activity on the same Item | `ACTIVITY_FORWARD` |
| an action with none of the above, or a plain dice roll | `ACTIVITY_UTILITY` |
| a lasting modifier or condition, always on or put on targets by an activity | an effect (`EFFECT`, an `effects` entry or an `APPLIED_EFFECTS` entry) |

One source often needs several blocks. A weapon hit that also forces a saving throw is an `ACTIVITY_ATTACK` and an `ACTIVITY_SAVE`. A spell Item holds the activities the spell performs (a Save, an Attack, a Heal), never an `ACTIVITY_CAST`.

## Start here: work in this order

1. Read the rules text and list its mechanics: costs, charges and recharge, range, targets, area, rolls, damage, saving throws, conditions, durations.
2. Write the core Item from 10-items.md: the root key, `ITEM`, the sections the source needs, and the complete rules text in `DESCRIPTION.Description`. It must be correct on its own.
3. For a full Item, add each mechanic as an activity or effect from 20-activities-and-effects.md, with keys from 40-effect-keys.md.
4. Only when the user says MIDI-QOL is active in their world, add the `AUTOMATION` settings from 30-midi-qol-and-dae.md. Leave everything else as it is.
5. Go through the "check before you answer" list of every type you used, then the output rules of this file.

Never invent mechanics the source does not state: no extra damage, bonus, cost, range, target, duration or class limit. Design new mechanics only when the user asks for homebrew. A rule the importers cannot store stays in the description as text, and you may say so in one line.

## Composition: a full Item with Activities and effects

A full Item is one document. `Activities` and `effects` are lists inside the root key, at the same indentation as `ITEM` and `DESCRIPTION`.

- `Activities`: each entry holds exactly one `ACTIVITY_*` key with that activity's sections. Two activities of the same type are two entries. Never put `---` inside the list.
- `effects`: each entry is an effect body that starts with `DETAILS`, without an `EFFECT:` line. These are the Item's passive effects, on the owner while the Item is equipped (and attuned, when it requires attunement). Write `DETAILS.Apply Effect to Actor: true` on them.
- An effect that an activity puts on its targets goes inside that activity's `APPLIED_EFFECTS` list, with `DETAILS.Apply Effect to Actor: false`. Do not repeat it in `effects`.
- Charges that belong to the Item go in the Item's `USAGE` and `RECOVERY`. An activity spends them with a `CONSUMPTION` entry of `Consumption Type: itemUses` and no target. A limit on one activity alone ("once per long rest" for that action only) goes in that activity's own `USAGE`, `RECOVERY` and a `CONSUMPTION` entry of `Consumption Type: activityUses`.

```yaml
SCHEMA_VERSION: 7
EQUIPMENT:
  ITEM:
    Name: Ring of the Hearthward
    Rarities: [uncommon]
    Equipment Type: ring
  INVENTORY:
    Quantity: 1
    Identified: true
  PROPERTIES:
    Magical: true
  ATTUNEMENT:
    Attunement: required
  USAGE:
    Uses Spent: 0
    Uses Max: 3
  RECOVERY:
    - Period: dawn
      Type: recoverAll
  DESCRIPTION:
    Description: |
      <p>While wearing this ring, you have resistance to cold damage.</p>
      <p>The ring has 3 charges. As a bonus action, you can expend 1 charge to gain [[/heal 1d10 temp]]. The ring regains all expended charges daily at dawn.</p>
  Activities:
    - ACTIVITY_HEAL:
        ACTIVITY:
          Name: Hearthward
        ACTIVATION:
          Override Activation: true
          Activation Type: bonus
        CONSUMPTION:
          - Consumption Type: itemUses
            Amount: "1"
        RANGE:
          Override Range: true
          Range Units: self
        TARGETS:
          Override Target: true
          Target Type: self
        HEALING:
          DAMAGE_DATA:
            Dice Count: 1
            Die Denomination: 10
            Bonus: ""
            Damage Types: [temphp]
            Custom Enabled: false
            Custom Formula: ""
            Scaling Mode: none
            Scaling Dice Count: 0
            Scaling Formula: ""
  effects:
    - DETAILS:
        Name: Hearthward Resistance
        Apply Effect to Actor: true
      CHANGES:
        - Attribute Key: system.traits.dr.value
          Change Type: add
          Value: cold
```

## Composition: one effect shared by several activities

When two activities put the same effect on their targets (an action for an ally and a bonus action for yourself, both granting the same ward), define the effect once, inside the first activity's `APPLIED_EFFECTS`, and give it a `DETAILS.Effect ID` of exactly 16 letters and digits that you make up, such as `wardingChantWard`. Every other activity lists it with a single entry, `- Effect ID:` and the same 16 characters. This is the only case in which you write an ID yourself; it names an effect in the same answer, not something that already exists.

```yaml
SCHEMA_VERSION: 7
FEAT:
  ITEM:
    Name: Warding Chant
    Feature Type: class
  DESCRIPTION:
    Description: |
      <p>As an action, you chant a ward over one ally you can see within 30 feet of you, or as a bonus action over yourself. The warded creature gains a +1 bonus to AC for 1 minute.</p>
  Activities:
    - ACTIVITY_UTILITY:
        ACTIVITY:
          Name: Ward an Ally
        ACTIVATION:
          Override Activation: true
          Activation Type: action
        RANGE:
          Override Range: true
          Range Units: ft
          Range Value: 30
        TARGETS:
          Override Target: true
          Target Type: ally
          Target Amount: 1
        APPLIED_EFFECTS:
          - DETAILS:
              Effect ID: wardingChantWard
              Name: Warding Chant
              Apply Effect to Actor: false
            DURATION:
              Value: 1
              Units: minutes
            CHANGES:
              - Attribute Key: system.attributes.ac.bonus
                Change Type: add
                Value: "1"
    - ACTIVITY_UTILITY:
        ACTIVITY:
          Name: Ward Yourself
        ACTIVATION:
          Override Activation: true
          Activation Type: bonus
        RANGE:
          Override Range: true
          Range Units: self
        TARGETS:
          Override Target: true
          Target Type: self
        APPLIED_EFFECTS:
          - Effect ID: wardingChantWard
```

## Composition: an attack that also forces a saving throw

"On a hit, the target must succeed on a DC 13 Constitution saving throw or be poisoned" is two entries in `Activities`: an `ACTIVITY_ATTACK` with the hit and its damage, then an `ACTIVITY_SAVE` with `SAVE_DETAILS`, the poisoned effect in its `APPLIED_EFFECTS` and `APPLICATION.On Save: false`. Without MIDI-QOL the user uses the save after a hit; 30-midi-qol-and-dae.md shows how MIDI-QOL links the two.

## Composition: a second way to pay (Forward)

"...or you can expend 3 charges to do the same" is an `ACTIVITY_FORWARD` next to the activity that defines the effect. Its `FORWARD_TARGET.Activity` is the exact `ACTIVITY.Name` of that activity, and its own `CONSUMPTION` is the full cost of this way of paying. The names of the activities on one Item must differ.

## Composition: loot and containers carry effects only

`LOOT` and `CONTAINER` can have an `effects` list (a passive effect while the owner carries it), but never `Activities`. If a container or a piece of loot does something when used, write the action into its description.

## Composition: several Items in one answer

Separate whole Item documents with a line `---`, each starting with its own `SCHEMA_VERSION: 7`. Items inside a container go in the container's `CONTENTS` list (each entry is one Item under its root key, without `SCHEMA_VERSION`), or name the container in `INVENTORY.Container` when it is in the same answer.

```yaml
SCHEMA_VERSION: 7
LOOT:
  ITEM:
    Name: Fire Opal
    Rarities: []
    Loot Type: gem
  INVENTORY:
    Quantity: 1
    Identified: true
  COST_AND_WEIGHT:
    Price Value: 1000
    Price Denomination: gp
  DESCRIPTION:
    Description: |
      <p>A translucent red gemstone.</p>
---
SCHEMA_VERSION: 7
LOOT:
  ITEM:
    Name: Silver Chalice
    Rarities: []
    Loot Type: art
  INVENTORY:
    Quantity: 1
    Identified: true
  COST_AND_WEIGHT:
    Price Value: 25
    Price Denomination: gp
  DESCRIPTION:
    Description: |
      <p>A silver chalice engraved with moonstones.</p>
```

## Composition: blocks for an Item that already exists

To add activities or effects to an Item that is already in Foundry, write the blocks at the top level, each starting with its root key, separated by `---`, and nothing else: no `SCHEMA_VERSION` and no Item. The user pastes them into 5e Activity Importer on that Item. A top-level `EFFECT` is a passive effect of that Item when it has `Apply Effect to Actor: true`.

```yaml
ACTIVITY_UTILITY:
  ACTIVITY:
    Name: Steady Breath
  ACTIVATION:
    Override Activation: true
    Activation Type: bonus
  RANGE:
    Override Range: true
    Range Units: self
  TARGETS:
    Override Target: true
    Target Type: self
  APPLIED_EFFECTS:
    - DETAILS:
        Name: Steady Breath
        Apply Effect to Actor: false
      DURATION:
        Value: 1
        Units: minutes
      CHANGES:
        - Attribute Key: system.rolls.ability.save.bonus
          Change Type: add
          Value: "1"
---
EFFECT:
  DETAILS:
    Name: Calm Mind
    Apply Effect to Actor: true
  CHANGES:
    - Attribute Key: system.abilities.wis.save.roll.mode
      Change Type: add
      Value: 1
```

## Composition: references by name, never invented IDs

Some fields point at a document that already exists. Name it; never make up its UUID or ID.

- **A spell cast by an Item** (`ACTIVITY_CAST`): `CASTING_DETAILS.Item Name` with the exact spell name and `CASTING_DETAILS.Item UUID: n/a`, unless the user gave the UUID. The preview shows a warning for it, and the import window asks the user to pick the spell before importing.
- **Spells or Items a feature grants** (`FEAT` `ADVANCEMENT`): `Name` and `Type` of each, no UUID.
- **A container in the same answer**: `INVENTORY.Container` with the container's name.
- **Another activity on the same Item** (`ACTIVITY_FORWARD`): its exact `ACTIVITY.Name`.
- **A creature to summon or a form to take** (`ACTIVITY_SUMMON`, `ACTIVITY_TRANSFORM` with a creature): the profile's `Actor Name` with the creature's exact name and `Actor UUID: n/a`, unless the user gave the UUID. The preview shows a warning for it, and the import window asks the user to pick the creature before importing.
- Never invent a UUID, a compendium name, a 16-character ID (`Activity ID`, `Effect ID`, `Behavior ID`) or a macro name. The one exception is the shared effect in "Composition: one effect shared by several activities".

## Start here: output rules

1. Answer with one fenced YAML code block. Outside it write at most two short lines: what to check after import, or one question. When a fact that changes the mechanics, or a required reference, is missing, ask instead of writing YAML.
2. An Item document starts with `SCHEMA_VERSION: 7`, then its root key. Blocks for an existing Item start with their root key.
3. Indent with two spaces. Write `Key: value` with a space after the colon. List entries start with `- `.
4. Copy section and field names exactly, with their capitals, spaces and underscores: `Damage Types`, `ATTACK_DAMAGE`, `Destroy on Empty`.
5. Write only the sections and fields the source needs. Never copy a whole field reference. Leave no `[placeholder]`, no `#` comment and no empty section.
6. Quote formulas and numbers that are text fields: `"1d6 + 2"`, `"@prof"`, `Amount: "1"`. Write `true`, `false` and plain numbers without quotes. Write lists as `[fire]` or as `- ` entries.
7. Use `n/a` only in a field whose reference documents it.
8. Write no other keys: no mode, notes, version, source, page or license fields beyond the ones the reference lists.
9. Keep the complete rules text in `DESCRIPTION.Description`, also when activities and effects carry the mechanics.

## Start here: descriptions and enrichers

Write `DESCRIPTION.Description` as HTML in a block: `Description: |`, then one `<p>` paragraph per paragraph, indented below the key. Keep the full rules text, in the source's words when the user supplied them. Enrichers make rolls and references clickable; they do not create activities. Write `[[lookup @name]]{the creature}` where the text names the creature that owns the Item.

```
[[/damage 2d6 + @mod slashing]]       Clickable damage roll
[[/damage 2d8 radiant average]]        Shows "9 (2d8) radiant"
[[/damage 1d6 piercing & 1d4 fire]]   Multiple damage types in one roll
[[/heal 2d8 + @mod]]                   Clickable healing roll
[[/heal 10 temp]]                      Temporary hit points
[[/save dex 15]]                       "DC 15 Dexterity" saving throw
[[/save dex format=long]]              "Dexterity saving throw"
[[/check str athletics 15]]            "DC 15 Strength (Athletics)"
[[/attack]]                            Links to first attack activity on item
[[/attack +5]]                         Fixed +5 to hit
&Reference[prone]                      Condition tooltip link
&Reference[fire]                       Damage type tooltip link
[[lookup @name]]                       Display actor data (no roll)
```

**Damage types:** `acid`, `bludgeoning`, `cold`, `fire`, `force`, `lightning`, `necrotic`, `piercing`, `poison`, `psychic`, `radiant`, `slashing`, `thunder`

**Options:** `average` = show calculated average. `format=long` = verbose text. `activity=ID` = link to specific activity.

## Start here: the version stamp

Every file of this pack starts with the same version line: pack 14.4.0, for 5e Item Importer 14.4.0 and 5e Activity Importer 14.4.0. If the user mentions other module versions, say once that the pack may not match them. Do not write the pack version into the YAML; `SCHEMA_VERSION: 7` is the only version an Item document carries.

## Start here: when the importer reports a problem

The user may paste back messages from the preview. Fix exactly what they name and answer with the whole corrected YAML, keeping everything else. The windows' **Copy problems** button copies the messages with a `Setup:` line and the YAML. The setup line names the modules the user has: with `5e Activity Importer not active`, answer with a core Item; unless it says `MIDI-QOL ... active`, leave `AUTOMATION` out.

| The message says | Do this |
| --- | --- |
| `Unknown or unused keys in ACTIVITY_SAVE; they will be ignored: ...` | A name is misspelled or in the wrong section. Correct it from the field reference, or move it to its section. |
| `Unknown key "X" in SECTION; it will be ignored.` | The same, in an Item section. A suggestion after it names the right key. |
| `Cast requires a spell Item UUID before import; resolve the spell drop zone.` | Expected when the spell is named by `Item Name`. The user picks the spell in the import window; change nothing. |
| `MIDI-QOL is not active in this world, so 2 MIDI-QOL settings will be left out: ...` (or `is not installed`, or `does not run on dnd5e ...`) | MIDI-QOL cannot store those settings in their world, so the window imports the rest without them. Nothing is broken; if the user wants YAML for this world, answer with the same YAML without its `AUTOMATION` sections. |
| `Loot Type is empty (''), so the Item is stored without one, as dnd5e allows.` (or `Weapon Type`, `Base Weapon`, `Poison Type` and other fields) | The YAML has an empty value, as an export of an Item stored without one does. Fill in the value the source calls for from the field reference; leave it empty only when the user wants it so. |
| `5e-activity-importer module is not active. Skipping inline activities/effects.` | The user has only 5e Item Importer. The core Item imports; offer a core Item without `Activities` and `effects`. |
| a message that a value is not allowed, or that a required field is missing | Use one of the values the field reference lists, or add the field. |

## Start here: MIDI-QOL in one line

MIDI-QOL gives the fullest automation, but every Item works without it: add the `AUTOMATION` layer only when the user says MIDI-QOL is active in their world. Without MIDI-QOL, the import windows leave those settings out and import the rest.
