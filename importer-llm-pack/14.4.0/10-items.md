# Importer LLM Pack 14.4.0: Items

Pack 14.4.0 (2026-11-16) for 5e Item Importer 14.4.0 and 5e Activity Importer 14.4.0, on dnd5e 6.0.6 and 6.1.0 with Foundry VTT 14.370. The MIDI-QOL layer matches MIDI-QOL 14.6.11 and DAE 14.6.9 on dnd5e 6.0.6.

This file covers the Item documents of 5e Item Importer, the free module. For each Item type it gives the root key, the rules wording and the fields it becomes, every field, the value lists, one core example and the checks to make before answering. A core Item works with 5e Item Importer alone. A full Item adds an `Activities` list and an `effects` list inside the root key; those need 5e Activity Importer, and 20-activities-and-effects.md describes their blocks. 00-start-here.md says which Item type to choose and how a full Item is put together.

Every Item document starts with `SCHEMA_VERSION: 7` on its own line, then exactly one root key (`WEAPON`, `EQUIPMENT`, `CONSUMABLE`, `TOOL`, `LOOT`, `CONTAINER`, `SPELL` or `FEAT`) with its sections indented below it.

## Items: rules for every Item type

For new physical Items, ordinary defaults are quantity 1, equipped false and zero unspecified price/weight. Write `ITEM.Rarities: []` for a mundane item, because a missing `Rarities` key is stored as `common`. A missing `INVENTORY.Identified` follows the user's own setting, so write it when it matters. Omit unsupplied attunement restrictions and optional resources rather than inventing them. Formula Uses Max must be deterministic, one line and at most 200 characters, without dice or semicolons; recovery needs a positive literal maximum or a formula maximum.

### Items: check before you answer (every type)

1. Start the document with `SCHEMA_VERSION: 7`, then the root key. Copy section and field names exactly as the Complete field reference spells them.
2. Write only the sections and fields the source needs. Do not copy the whole reference, and leave no `[placeholder]` value and no `#` comment in the answer.
3. Write formulas as quoted strings (`"1d6 + 1"`). Write `true`, `false` and plain numbers without quotes.
4. Keep the complete rules text in `DESCRIPTION.Description`. Add `Activities` and `effects` only when the user asked for a full Item and has 5e Activity Importer; use that module's templates for those blocks.

### Item values (every type): Proficiency
| Value | Meaning |
|-------|---------|
| `Automatic` | Game auto-detects proficiency from the character (default) |
| `0` | Not proficient |
| `1` | Proficient |

### Item values (every type): Recovery Periods
| Period | Description |
|--------|-------------|
| `lr` | Long Rest |
| `sr` | Short Rest |
| `day` | Daily (any time) |
| `dawn` | At dawn |
| `dusk` | At dusk |
| `recharge` | Recharge threshold: set Formula to 2, 3, 4, 5, or 6 |
| `initiative` | When initiative is rolled |
| `turnStart` | At the start of the owner's turn |
| `turnEnd` | At the end of the owner's turn |
| `turn` | Each turn |

### Item values (every type): Recovery Types
| Type | Formula | Result |
|------|---------|--------|
| `recoverAll` | n/a | Regain all uses |
| `loseAll` | n/a | Lose all remaining uses |
| `formula` | Dice (e.g., `1d4+1`) | Regain rolled amount |
| `formula` | Number (e.g., `5`) | For recharge: regain all on d6 ≥ 5 |

## WEAPON: Weapon Item

- **Root key:** `WEAPON`
- **When to use:** Use WEAPON when the source is something a creature attacks with and it has weapon damage: a mundane or magic sword, bow or other weapon, a monster's natural weapon (bite, claw), an improvised weapon or a siege weapon. A magic staff that can be swung as a quarterstaff is a weapon even when it is mainly a spellcasting focus: write `ITEM.Base Weapon: quarterstaff`, and `PROPERTIES.Focus: true` if the source says it can be a focus.
- **Smallest working block:** `ITEM.Name` (the name), `ITEM.Weapon Type` (one of `simpleM`, `simpleR`, `martialM`, `martialR`, `natural`, `improv`, `siege`), `ITEM.Base Weapon` (required for the first four types; leave it out for `natural`, `improv` and `siege`), `DAMAGE.DAMAGE_DATA` (all ten keys; `Dice Count`, `Die Denomination` and `Damage Types` hold the weapon die).

**Use another Item type when:**

- Arrows, bolts, sling bullets and other ammunition: CONSUMABLE (`Consumable Type: ammo`).
- A shield: EQUIPMENT (`Equipment Type: shield`).
- A wand or rod. It has no weapon damage: EQUIPMENT (`Equipment Type: wand` or `rod`).
- A worn item that only improves attacks (bracers, gloves, a ring): EQUIPMENT.
- A spell that makes an attack: SPELL.
- A monster ability with no weapon attack (breath weapon, aura, Multiattack): FEAT.
- Poison or oil that is applied to a weapon: CONSUMABLE.

### WEAPON: from source text to YAML

Find the wording closest to the source and write the fields shown. A path such as `ITEM.Name` means the `Name` key inside the `ITEM` section of `WEAPON`. A list such as `RECOVERY` takes one `-` entry per item. Leave out every section the source does not mention.

| The source says | Write |
| --- | --- |
| "Weapon (longsword)" or "martial melee weapon" | `ITEM.Weapon Type: martialM`; `ITEM.Base Weapon: longsword` |
| "1d8 slashing" | `DAMAGE.DAMAGE_DATA.Dice Count: 1`; `DAMAGE.DAMAGE_DATA.Die Denomination: 8`; `DAMAGE.DAMAGE_DATA.Damage Types: [slashing]` |
| "versatile (1d10)" | `PROPERTIES.Versatile: true`; `VERSATILE_DAMAGE.DAMAGE_DATA.Dice Count: 1`; `VERSATILE_DAMAGE.DAMAGE_DATA.Die Denomination: 10`; `VERSATILE_DAMAGE.DAMAGE_DATA.Damage Types: [slashing]` |
| "finesse, light, thrown (range 20/60)" | `PROPERTIES.Finesse: true`; `PROPERTIES.Light: true`; `PROPERTIES.Thrown: true`; `RANGE.Range Normal: 20`; `RANGE.Range Long: 60`; `RANGE.Range Units: ft` |
| "ammunition (range 150/600), heavy, two-handed" | `PROPERTIES.Ammunition: true`; `AMMUNITION.Ammunition Type: arrow`; `PROPERTIES.Heavy: true`; `PROPERTIES.Two-Handed: true`; `RANGE.Range Normal: 150`; `RANGE.Range Long: 600`; `RANGE.Range Units: ft` |
| "reach" (10 feet instead of 5) | `PROPERTIES.Reach: true`; `RANGE.Reach: 10` |
| "Mastery: Sap" | `MASTERY.Mastery: sap` |
| "You have a +1 bonus to attack and damage rolls made with this magic weapon" | `PROPERTIES.Magical: true`; `ATTUNEMENT.Magic Bonus: 1` |
| "rare (requires attunement by a paladin)" | `ITEM.Rarities: [rare]`; `PROPERTIES.Magical: true`; `ATTUNEMENT.Attunement: required`; `ATTUNEMENT.Attunement By: a paladin` |
| "15 gp" and "3 lb." | `COST_AND_WEIGHT.Price Value: 15`; `COST_AND_WEIGHT.Price Denomination: gp`; `COST_AND_WEIGHT.Weight Value: 3`; `COST_AND_WEIGHT.Weight Units: lb` |
| "It has 7 charges" and "regains 1d6 + 1 expended charges daily at dawn" | `USAGE.Uses Spent: 0`; `USAGE.Uses Max: 7`; `RECOVERY: [{Period: dawn, Type: formula, Formula: "1d6 + 1"}]` |
| "Longsword" in an equipment table (not magic, no rarity) | `ITEM.Rarities: []`; `PROPERTIES.Magical: false`; `INVENTORY.Identified: true` |

### WEAPON: complete field reference

Every section and field a `WEAPON` document can have. A placeholder such as `"[true|false]"` lists the choices; it is not a value to copy. Write only what the source needs.

```yaml
SCHEMA_VERSION: 7
WEAPON:
  SOURCE:
    Book: "[text|n/a]"
    Page: "[text|n/a]"
    Custom: "[text|n/a]"
    License: "[text|n/a]"
    Rules: "[2014|2024|text]"
    Revision: 1 # Finite number, including decimals; native default 1.

  ITEM:
    Icon: "[filepath|n/a]"
    Identifier: "[text|n/a]"
    Sort: 0 # Integer ordering value; preserve an authored zero.
    Name: "[text]"
    Rarities: [] # Native rarity IDs: common, uncommon, rare, veryRare, legendary, artifact. [] means mundane.
    Weapon Type: "[simpleM|simpleR|martialM|martialR|natural|improv|siege]"
    Base Weapon: "[e.g. longsword, dagger, bow - see list below - OR n/a]"

  INVENTORY:
    Quantity: "[integer]"
    Identified: "[true|false]"
    Equipped: "[true|false]"
    Container: "[container name|uuid|n/a]" # Place inside a container: its name in this batch, an Item UUID, or drag-and-drop in the import window.

  COST_AND_WEIGHT:
    Price Value: "[number]"
    Price Denomination: "[pp|gp|ep|sp|cp]"
    Weight Value: "[number]"
    Weight Units: "[lb|tn|kg|Mg]"

  PROPERTIES:
    NPC Equipment: false # Native gear property; shown for NPC inventory.
    Adamantine: "[true|false]"
    Ammunition: "[true|false]"
    Finesse: "[true|false]"
    Firearm: "[true|false]"
    Focus: "[true|false]"
    Heavy: "[true|false]"
    Light: "[true|false]"
    Loading: "[true|false]"
    Magical: "[true|false]"
    Reach: "[true|false]" # Property checkbox; the distance is RANGE.Reach.
    Reload: "[true|false]"
    Returning: "[true|false]"
    Silvered: "[true|false]"
    Special: "[true|false]"
    Thrown: "[true|false]"
    Two-Handed: "[true|false]"
    Versatile: "[true|false]"

  ATTUNEMENT:
    Attuned: false # Current state, independent of Attunement requirement.
    # Store independently of Magical; a bonus does not enable an explicit false property.
    Attunement: "[none|required|optional]"
    Attunement By: "[text|n/a]" # Importer metadata; ignored when Attunement is none.
    Magic Bonus: "[formula|n/a]" # Deterministic formula, e.g. "@prof"; no dice.

  AMMUNITION:
    # Select for Ammunition; preserve a supplied selection when the property is false.
    Ammunition Type: "[arrow|crossbowBolt|firearmBullet|slingBullet|energyCell|blowgunNeedle]"

  RELOAD:
    # Compatibility metadata only; no native reload amount or automation.
    # Optional legacy metadata, even when Reload is true.
    Reload Amount: "[integer]"

  VERSATILE_DAMAGE:
    DAMAGE_DATA:
      Dice Count: "[integer|null]" # Nonnegative integer; null means no dice count.
      Die Denomination: "[integer|null]" # Sheet dice: 4, 6, 8, 10, 12, 20, 100; or null.
      Bonus: "[formula|n/a]"
      Damage Types: [] # Native damage type IDs; e.g. [piercing, fire].
      Modifiers: []
      Custom Enabled: false
      Custom Formula: "[formula|n/a]" # Retained even when Custom Enabled is false.
      Scaling Mode: "[none|whole|half]"
      Scaling Dice Count: "[integer|null]" # Default 1 if omitted; retain explicit 0 or null.
      Scaling Formula: "[formula|n/a]" # Retained even when Scaling Mode is none.
    # Used when Versatile is true; preserve supplied damage when the property is false.

  SIEGE_PROPERTIES:
    Crew Capacity: 0
    Speed: "[integer|null]" # Nonnegative integer; no formula.
    Speed Units: "[ft|mi|m|km]"
    Speed Conditions: "[text|n/a]"
    # Shown for siege weapons; preserve supplied mountable settings on other types.
    Siege Armor Class: "[integer|null]" # Nonnegative integer or native null.
    Cover: "[none|half|threequarters|total|number|null]" # Numeric cover is 0..1.
    Hit Points Current: "[integer|null]" # Nonnegative integer, or native null.
    Hit Points Max: "[integer|null]" # Nonnegative integer, or native null.
    Hit Points Threshold: "[integer|null]" # Nonnegative integer, or native null.
    Health Conditions: "[text|n/a]"

  RANGE:
    Reach: "[number|null]" # Nonnegative decimal distance; no formula.
    Range Normal: "[number|null]" # Nonnegative decimal distance; no formula.
    Range Long: "[number|null]" # Nonnegative decimal distance; no formula.
    Range Units: "[ft|m|sq|mi]"

  DAMAGE:
    DAMAGE_DATA:
      Dice Count: "[integer|null]" # Nonnegative integer; null means no dice count.
      Die Denomination: "[integer|null]" # Sheet dice: 4, 6, 8, 10, 12, 20, 100; or null.
      Bonus: "[formula|n/a]"
      Damage Types: [] # Native damage type IDs; e.g. [piercing, fire].
      Modifiers: []
      Custom Enabled: false
      Custom Formula: "[formula|n/a]" # Retained even when Custom Enabled is false.
      Scaling Mode: "[none|whole|half]"
      Scaling Dice Count: "[integer|null]" # Default 1 if omitted; retain explicit 0 or null.
      Scaling Formula: "[formula|n/a]" # Retained even when Scaling Mode is none.

  MASTERY:
    Mastery: "[cleave|graze|nick|push|sap|slow|topple|vex|n/a]"

  PROFICIENCY:
    Proficient: "[Automatic|0|1]"

  USAGE:
    # Uses Spent = number of charges ALREADY CONSUMED (0 means all charges are available).
    # Uses Max = maximum count or deterministic formula, e.g. "1 + @prof" (no dice).
    # Example: A fresh item with 5 charges → Uses Spent: 0, Uses Max: 5
    Uses Spent: "[integer|n/a]"
    Uses Max: "[integer|formula|n/a]"

  # Optional, repeatable. Use [] when there is no recovery.
  # With a positive Uses Max or a formula maximum, replace [] with entries shaped like:
  #   - Period: "[lr|sr|day|dawn|dusk|recharge|initiative|turnStart|turnEnd|turn]"
  #     Type: "[recoverAll|loseAll|formula]"
  #     Formula: "[formula|n/a]" # Required for Type formula; recharge uses a 2..6 threshold.
  RECOVERY: []

  DESCRIPTION:
    Description: |
      [multiline HTML content containing Enrichers]

  UNIDENTIFIED_DESCRIPTION:
    Unidentified Name: "[text|n/a]"
    Unidentified Description: |
      [multiline HTML content]

  CHAT_FLAVOR:
    Chat Description: |
      [multiline text content]

```

### WEAPON: rules

Use DAMAGE.DAMAGE_DATA for the parent weapon's base damage and VERSATILE_DAMAGE.DAMAGE_DATA for its two-handed alternative. A weapon attack with Include Base Damage:true already uses the parent base; do not duplicate that damage in extra parts. Keep Magic Bonus separate from additional dice. For mixed types in one custom base formula, bracket each term's type and supply a primary Damage Types entry for system-added bonuses. Store siege/ammunition settings only when supplied; their presence does not enable the corresponding property. Reload Amount is compatibility metadata with a warning, not native reload automation.

### WEAPON: field values

#### WEAPON values: Weapon Types
| Type | Description |
|------|-------------|
| `simpleM` | Simple Melee Weapon |
| `simpleR` | Simple Ranged Weapon |
| `martialM` | Martial Melee Weapon |
| `martialR` | Martial Ranged Weapon |
| `natural` | Natural Weapon (claws, bite, etc.) |
| `improv` | Improvised Weapon |
| `siege` | Siege Weapon |

#### WEAPON values: Base Weapons - Melee
| Simple | Martial |
|--------|---------|
| `club`, `dagger`, `greatclub`, `handaxe`, `javelin`, `lighthammer`, `mace`, `quarterstaff`, `sickle`, `spear` | `battleaxe`, `flail`, `glaive`, `greataxe`, `greatsword`, `halberd`, `lance`, `longsword`, `maul`, `morningstar`, `pike`, `rapier`, `scimitar`, `shortsword`, `trident`, `warpick`, `warhammer`, `whip` |

#### WEAPON values: Base Weapons - Ranged
| Simple | Martial |
|--------|---------|
| `dart`, `lightcrossbow`, `shortbow`, `sling` | `blowgun`, `handcrossbow`, `heavycrossbow`, `longbow`, `net` |

#### WEAPON values: Weapon Mastery Properties (2024)
| Mastery | Effect |
|---------|--------|
| `cleave` | Hit another creature within 5 ft for ability mod damage |
| `graze` | Deal ability mod damage on a miss |
| `nick` | Make extra attack with light weapon as part of Attack action |
| `push` | Push Large or smaller creature 10 ft away |
| `sap` | Disadvantage on target's next attack roll |
| `slow` | Reduce target's speed by 10 ft until your next turn |
| `topple` | Target must make Con save or fall prone |
| `vex` | Advantage on next attack against same target |

#### WEAPON values: Recovery Periods
| Period | Description |
|--------|-------------|
| `lr` | Long Rest |
| `sr` | Short Rest |
| `day` | Daily (any time) |
| `dawn` | At dawn |
| `dusk` | At dusk |
| `recharge` | Recharge threshold: set Formula to 2, 3, 4, 5, or 6 |
| `initiative` | When initiative is rolled |
| `turnStart` | At the start of the owner's turn |
| `turnEnd` | At the end of the owner's turn |
| `turn` | Each turn |

### WEAPON: core example

A free/core weapon import. Preserve the complete description; it does not automatically create source-specific activities or effects.

5e Item Importer. Activity Importer is not required for this example.

```yaml
SCHEMA_VERSION: 7
WEAPON:
  ITEM:
    Name: Longsword +1
    Rarities: [uncommon]
    Weapon Type: martialM
    Base Weapon: longsword
  INVENTORY:
    Quantity: 1
    Identified: true
  COST_AND_WEIGHT:
    Price Value: 500
    Price Denomination: gp
    Weight Value: 3
    Weight Units: lb
  PROPERTIES:
    Magical: true
    Versatile: true
  ATTUNEMENT:
    Attunement: required
    Magic Bonus: 1
  RANGE:
    Reach: 5
  DAMAGE:
    DAMAGE_DATA:
      Dice Count: 1
      Die Denomination: 8
      Bonus: ""
      Damage Types:
        - slashing
      Modifiers: []
      Custom Enabled: false
      Custom Formula: ""
      Scaling Mode: none
      Scaling Dice Count: 0
      Scaling Formula: ""
  VERSATILE_DAMAGE:
    DAMAGE_DATA:
      Dice Count: 1
      Die Denomination: 10
      Bonus: ""
      Damage Types:
        - slashing
      Modifiers: []
      Custom Enabled: false
      Custom Formula: ""
      Scaling Mode: none
      Scaling Dice Count: 0
      Scaling Formula: ""
  MASTERY:
    Mastery: sap
  DESCRIPTION:
    Description: |
      You have a +1 bonus to attack and damage rolls made with this magic weapon.
```

### WEAPON: check before you answer

These checks are for `WEAPON`. The checks for every Item type are in "Items: check before you answer (every type)".

1. Write `ITEM.Weapon Type` with exact case: `simpleM`, `simpleR`, `martialM`, `martialR`. `martialm` or `Martial Melee` fails.
2. Write `ITEM.Base Weapon` as the mundane weapon in one lowercase word: `longsword`, `lightcrossbow`, `handcrossbow`, `warpick`. Use it for magic weapons too. Spaces are removed (`hand crossbow` becomes `handcrossbow`), and an id dnd5e does not know gives a warning.
3. Write all ten `DAMAGE_DATA` keys. For a plain weapon the other seven are `Bonus: ""`, `Modifiers: []`, `Custom Enabled: false`, `Custom Formula: ""`, `Scaling Mode: none`, `Scaling Dice Count: 0`, `Scaling Formula: ""`.
4. Put only the weapon's own die in `DAMAGE.DAMAGE_DATA`. Leave `Bonus` empty: do not write the ability modifier or the magic bonus there. Extra damage that depends on a condition (for example 2d6 fire while the blade burns) does not belong in `DAMAGE_DATA`.
5. `PROPERTIES.Versatile: true` needs `VERSATILE_DAMAGE.DAMAGE_DATA`, and `PROPERTIES.Ammunition: true` needs `AMMUNITION.Ammunition Type`. The import fails without them. Ammunition ids are case-sensitive: `arrow`, `crossbowBolt`, `firearmBullet`, `slingBullet`, `energyCell`, `blowgunNeedle`.
6. `PROPERTIES.Reach` is `true` or `false`; the distance is the number in `RANGE.Reach`. `RANGE.Range Normal` and `RANGE.Range Long` are separate numbers: write `20` and `60`, never `20/60`.
7. Write damage types in lowercase (`slashing`, not `Slashing`) and property keys exactly as in the reference (`Two-Handed` has a hyphen). A wrong damage type fails. A wrong property key is ignored with a warning.
8. Set `PROPERTIES.Magical: true` on every magic weapon; `ATTUNEMENT.Attunement: required` does not set it. `ATTUNEMENT.Magic Bonus` is a number such as `1`, never dice. Always write `ITEM.Rarities` (`[]` for a mundane weapon; a missing key is stored as `common`) and `INVENTORY.Identified` (a missing key follows the user's setting).

## EQUIPMENT: Equipment Item

- **Root key:** `EQUIPMENT`
- **When to use:** Use EQUIPMENT when the source is armor, a shield, clothing, or a magic item that is worn or held and is not used up: a ring, rod, wand, cloak, boots, amulet or other wondrous item, a holy symbol or other trinket, or vehicle equipment. `ITEM.Equipment Type` says which: `light`, `medium`, `heavy`, `natural`, `shield`, `clothing`, `ring`, `rod`, `wand`, `trinket`, `wondrous` or `vehicle`.
- **Smallest working block:** `ITEM.Name` (the name), `ITEM.Equipment Type` (one id from the list). For `light`, `medium`, `heavy`, `natural` and `shield` also `ARMOR.Armor Class` (required) and `ITEM.Base Equipment` (the importer warns without it, except for `natural`).

**Use another Item type when:**

- A weapon, including a magic staff that can be swung as a quarterstaff: WEAPON.
- A potion, scroll, ammunition, or a wondrous item that is used up (dust, a bead): CONSUMABLE.
- A bag, pack, chest or quiver that holds items: CONTAINER.
- Artisan's tools, a kit, a gaming set or a musical instrument: TOOL.
- A gem, an art object, or gear with no rules to run: LOOT.
- A class feature or feat that changes AC: FEAT.

### EQUIPMENT: from source text to YAML

Find the wording closest to the source and write the fields shown. A path such as `ITEM.Name` means the `Name` key inside the `ITEM` section of `EQUIPMENT`. A list such as `RECOVERY` takes one `-` entry per item. Leave out every section the source does not mention.

| The source says | Write |
| --- | --- |
| "Plate Armor", "Heavy Armor", "AC 18" | `ITEM.Equipment Type: heavy`; `ITEM.Base Equipment: plate`; `ARMOR.Armor Class: 18`; `ARMOR.Max Dex Modifier: 0` |
| "AC 14 + Dex modifier (max 2)" (medium armor) | `ITEM.Equipment Type: medium`; `ITEM.Base Equipment: breastplate`; `ARMOR.Armor Class: 14`; `ARMOR.Max Dex Modifier: 2` |
| "AC 11 + Dex modifier" (light armor) | `ITEM.Equipment Type: light`; `ITEM.Base Equipment: leather`; `ARMOR.Armor Class: 11` |
| "Strength 15" and "Stealth: Disadvantage" | `ARMOR.Strength Requirement: 15`; `PROPERTIES.Stealth Disadvantage: true` |
| "Shield", "+2 AC" | `ITEM.Equipment Type: shield`; `ITEM.Base Equipment: shield`; `ARMOR.Armor Class: 2` |
| "You have a +1 bonus to AC while wearing this armor" | `PROPERTIES.Magical: true`; `ATTUNEMENT.Magic Bonus: 1` |
| "Wondrous item, uncommon (requires attunement by a spellcaster)" | `ITEM.Equipment Type: wondrous`; `ITEM.Rarities: [uncommon]`; `PROPERTIES.Magical: true`; `ATTUNEMENT.Attunement: required`; `ATTUNEMENT.Attunement By: a spellcaster` |
| "Ring, rare" | `ITEM.Equipment Type: ring`; `ITEM.Rarities: [rare]`; `PROPERTIES.Magical: true` |
| "can be used as a holy symbol" or "as a spellcasting focus" | `PROPERTIES.Focus: true` |
| "It has 3 charges" and "regains all expended charges daily at dawn" | `USAGE.Uses Spent: 0`; `USAGE.Uses Max: 3`; `RECOVERY: [{Period: dawn, Type: recoverAll}]` |
| "1,500 gp" and "65 lb." | `COST_AND_WEIGHT.Price Value: 1500`; `COST_AND_WEIGHT.Price Denomination: gp`; `COST_AND_WEIGHT.Weight Value: 65`; `COST_AND_WEIGHT.Weight Units: lb` |
| "Chain Mail" in an equipment table (not magic, no rarity) | `ITEM.Rarities: []`; `PROPERTIES.Magical: false`; `INVENTORY.Identified: true` |

### EQUIPMENT: complete field reference

Every section and field a `EQUIPMENT` document can have. A placeholder such as `"[true|false]"` lists the choices; it is not a value to copy. Write only what the source needs.

```yaml
SCHEMA_VERSION: 7
EQUIPMENT:
  SOURCE:
    Book: "[text|n/a]"
    Page: "[text|n/a]"
    Custom: "[text|n/a]"
    License: "[text|n/a]"
    Rules: "[2014|2024|text]"
    Revision: 1 # Finite number, including decimals; native default 1.

  ITEM:
    Icon: "[filepath|n/a]"
    Identifier: "[text|n/a]"
    Sort: 0 # Integer ordering value; preserve an authored zero.
    Name: "[text]"
    Rarities: [] # Native rarity IDs: common, uncommon, rare, veryRare, legendary, artifact. [] means mundane.
    Equipment Type: "[light|medium|heavy|natural|shield|clothing|ring|rod|trinket|wand|wondrous|vehicle]"
    Base Equipment: "[e.g. plate, leather, shield - OR n/a for wondrous items]"

  INVENTORY:
    Quantity: "[integer]"
    Identified: "[true|false]"
    Equipped: "[true|false]"
    Container: "[container name|uuid|n/a]" # Place inside a container: its name in this batch, an Item UUID, or drag-and-drop in the import window.

  COST_AND_WEIGHT:
    Price Value: "[number]"
    Price Denomination: "[pp|gp|ep|sp|cp]"
    Weight Value: "[number]"
    Weight Units: "[lb|tn|kg|Mg]"

  PROPERTIES:
    NPC Equipment: false # Native gear property; shown for NPC inventory.
    Magical: "[true|false]"
    Adamantine: "[true|false]"
    Focus: "[true|false]"
    Stealth Disadvantage: "[true|false]"

  ATTUNEMENT:
    Attuned: false # Current state, independent of Attunement requirement.
    # Store independently of Magical; a bonus does not enable an explicit false property.
    Attunement: "[none|required|optional]"
    Attunement By: "[text|n/a]" # Importer metadata; ignored when Attunement is none.
    Magic Bonus: "[formula|n/a]" # Deterministic formula, e.g. "@prof"; no dice.

  ARMOR:
    # Armor/shield controls; preserve supplied settings on non-armor types.
    Armor Class: "[integer|null]" # Same native AC field; supplied non-null values must agree.
    Max Dex Modifier: "[integer|null]" # Null means no stored dexterity cap.
    Strength Requirement: "[integer|null]" # Nonnegative integer or native null.

  VEHICLE_PROPERTIES:
    Crew Capacity: 0
    Speed Units: "[ft|mi|m|km]"
    # Vehicle controls; preserve supplied settings on other equipment types.
    Vehicle Armor Class: "[integer|null]" # Same native AC field; supplied non-null values must agree.
    Cover: "[none|half|threequarters|total|number|null]" # Numeric cover is 0..1.
    Hit Points Current: "[integer|null]" # Nonnegative integer, or native null.
    Hit Points Max: "[integer|null]" # Nonnegative integer, or native null.
    Hit Points Threshold: "[integer|null]" # Nonnegative integer, or native null.
    Health Conditions: "[text|n/a]"
    Speed: "[integer|null]" # Nonnegative integer; no formula.
    Speed Conditions: "[text|n/a]"

  PROFICIENCY:
    Proficient: "[Automatic|0|1]"

  USAGE:
    # Uses Spent = number of charges ALREADY CONSUMED (0 means all charges are available).
    # Uses Max = maximum count or deterministic formula, e.g. "1 + @prof" (no dice).
    # Example: A fresh item with 5 charges → Uses Spent: 0, Uses Max: 5
    Uses Spent: "[integer|n/a]"
    Uses Max: "[integer|formula|n/a]"

  # Optional, repeatable. Use [] when there is no recovery.
  # With a positive Uses Max or a formula maximum, replace [] with entries shaped like:
  #   - Period: "[lr|sr|day|dawn|dusk|recharge|initiative|turnStart|turnEnd|turn]"
  #     Type: "[recoverAll|loseAll|formula]"
  #     Formula: "[formula|n/a]" # Required for Type formula; recharge uses a 2..6 threshold.
  RECOVERY: []

  DESCRIPTION:
    Description: |
      [multiline HTML content containing Enrichers]

  UNIDENTIFIED_DESCRIPTION:
    Unidentified Name: "[text|n/a]"
    Unidentified Description: |
      [multiline HTML content]

  CHAT_FLAVOR:
    Chat Description: |
      [multiline text content]

```

### EQUIPMENT: rules

ARMOR and VEHICLE_PROPERTIES are conditional authoring groups. Keep supplied inactive armor/vehicle settings, but do not invent them for a wondrous item. Conflicting armor-class values are invalid. Source rules such as resistance or an AC bonus require explicit companion effects for automation; description text alone does not supply them.

### EQUIPMENT: field values

#### EQUIPMENT values: Equipment Types & Base Equipment
| Type | Base Equipment Options |
|------|------------------------|
| `light` | `padded`, `leather`, `studdedleather` |
| `medium` | `hide`, `chainshirt`, `scalemail`, `breastplate`, `halfplate` |
| `heavy` | `ringmail`, `chainmail`, `splint`, `plate` |
| `shield` | `shield` |
| `natural` | n/a (for creature natural armor) |
| `clothing` | n/a |
| `ring` | n/a |
| `wondrous` | n/a |
| `trinket` | n/a |
| `rod` | n/a |
| `wand` | n/a |
| `vehicle` | n/a |

#### EQUIPMENT values: Armor Class Calculations
| Type | Base AC | Dex Modifier |
|------|---------|--------------|
| Light | 11-12 | Full Dex |
| Medium | 12-15 | Max +2 Dex |
| Heavy | 14-18 | No Dex |
| Shield | +2 | N/A (added to base) |

#### EQUIPMENT values: Recovery Types
| Type | Formula | Result |
|------|---------|--------|
| `recoverAll` | n/a | Regain all charges |
| `loseAll` | n/a | Lose all remaining charges |
| `formula` | Dice (e.g., `1d4+1`) | Regain rolled amount |
| `formula` | Number (e.g., `5`) | For recharge: regain all on d6 ≥ 5 |

### EQUIPMENT: core example

A free/core equipment import. Preserve the complete description; it does not automatically create source-specific activities or effects.

5e Item Importer. Activity Importer is not required for this example.

```yaml
SCHEMA_VERSION: 7
EQUIPMENT:
  ITEM:
    Name: Cloak of Protection
    Rarities: [uncommon]
    Equipment Type: wondrous
    Base Equipment: n/a
  INVENTORY:
    Quantity: 1
    Identified: true
  COST_AND_WEIGHT:
    Price Value: 400
    Price Denomination: gp
    Weight Value: 2
    Weight Units: lb
  PROPERTIES:
    Magical: true
  ATTUNEMENT:
    Attunement: required
  DESCRIPTION:
    Description: >
      While wearing this cloak, [[lookup @name]]{the creature} gains a +1 bonus to AC and saving
      throws.
```

### EQUIPMENT: check before you answer

These checks are for `EQUIPMENT`. The checks for every Item type are in "Items: check before you answer (every type)".

1. `light`, `medium`, `heavy`, `natural` and `shield` need an `ARMOR` section with `ARMOR.Armor Class`, or the import fails. Leave `ARMOR` out for rings, rods, wands, clothing, trinkets and wondrous items.
2. `ARMOR.Armor Class` is a whole number: the armor's base AC (`18` for plate, `14` for a breastplate) or a shield's bonus (`2`). Text such as `14 + Dex modifier (max 2)` fails. For +1 armor keep the base AC and write `ATTUNEMENT.Magic Bonus: 1`; do not write `19`.
3. Write the Dexterity cap in `ARMOR.Max Dex Modifier`: `2` for medium armor, `0` for heavy armor. Leave it out for light armor. Heavy armor without `Max Dex Modifier: 0` is stored with no cap.
4. "Stealth: Disadvantage" is `PROPERTIES.Stealth Disadvantage: true`. It is not an `ARMOR` key; there it is ignored with a warning. The Strength score is `ARMOR.Strength Requirement`.
5. `ATTUNEMENT.Magic Bonus` is the AC bonus of magic armor or a magic shield. Do not use it for a ring, cloak or other wondrous item that grants +1 AC. That bonus is not an Item field.
6. `ITEM.Equipment Type` is one id: `wondrous`, not `wondrous item`. `ITEM.Base Equipment` is only for armor and shields, as one lowercase word: `studdedleather`, `chainshirt`, `scalemail`, `halfplate`, `ringmail`, `chainmail`. Leave it out for everything else.
7. Set `PROPERTIES.Magical: true` on every magic item; `ATTUNEMENT.Attunement: required` does not set it. `ATTUNEMENT.Attunement` is `required`, `optional` or `none`, never `true`. Always write `ITEM.Rarities` (`[]` for mundane gear; a missing key is stored as `common`) and `INVENTORY.Identified` (a missing key follows the user's setting).
8. `USAGE.Uses Spent` counts charges already used, so a full item is `Uses Spent: 0`. `USAGE.Uses Max` is a whole number or a formula without dice. `RECOVERY` is dropped when `Uses Max` is `0` or missing. Use the period ids (`dawn`, `dusk`, `day`, `lr`, `sr`), not words such as `long rest`.

## CONSUMABLE: Consumable Item

- **Root key:** `CONSUMABLE`
- **When to use:** Use CONSUMABLE when the source is used up or carried as a stack: a potion, elixir or oil, a spell scroll, ammunition, poison, food and drink, gear with a number of uses (a healer's kit), or a one-use wondrous item such as dust or a bead. The parser also accepts `wand` and `rod` here, for a charged wand or rod that the user wants stored as a consumable.
- **Smallest working block:** `ITEM.Name` (the name), `ITEM.Consumable Type` (`ammo`, `food`, `poison`, `potion`, `rod`, `scroll`, `trinket` or `wand`). `ammo` also needs `AMMUNITION_PROPERTIES.Ammunition Type`; `poison` also needs `POISON_PROPERTIES.Poison Type`.

**Use another Item type when:**

- The bow, crossbow or sling that fires the ammunition: WEAPON.
- A spell description that is not a scroll: SPELL.
- A ring, cloak, boots or other permanent magic item: EQUIPMENT. A wand or rod fits either template; EQUIPMENT (`Equipment Type: wand` or `rod`) suits one that keeps recharging.
- A quiver, bag or case that holds the consumables: CONTAINER.
- A herbalism kit, poisoner's kit or other kit that gives a tool proficiency: TOOL.
- A gem, an art object, or gear that is not used up: LOOT.

### CONSUMABLE: from source text to YAML

Find the wording closest to the source and write the fields shown. A path such as `ITEM.Name` means the `Name` key inside the `ITEM` section of `CONSUMABLE`. A list such as `RECOVERY` takes one `-` entry per item. Leave out every section the source does not mention.

| The source says | Write |
| --- | --- |
| "Potion, common" | `ITEM.Consumable Type: potion`; `ITEM.Rarities: [common]`; `PROPERTIES.Magical: true` |
| "when you drink this potion" (one use, then it is gone) | `USAGE.Uses Spent: 0`; `USAGE.Uses Max: 1`; `USAGE.Destroy on Empty: true` |
| "Arrows (20)" | `ITEM.Consumable Type: ammo`; `AMMUNITION_PROPERTIES.Ammunition Type: arrow`; `INVENTORY.Quantity: 20` |
| "You have a +1 bonus to attack and damage rolls made with this piece of magic ammunition" | `PROPERTIES.Magical: true`; `ATTUNEMENT.Magic Bonus: 1` |
| "Poison (injury)" | `ITEM.Consumable Type: poison`; `POISON_PROPERTIES.Poison Type: injury` |
| "Spell scroll" of a concentration spell with V and S components | `ITEM.Consumable Type: scroll`; `SCROLL_PROPERTIES.Vocal: true`; `SCROLL_PROPERTIES.Somatic: true`; `SCROLL_PROPERTIES.Concentration: true`; `SCROLL_PROPERTIES.Ritual: false` |
| "Rations (1 day)" | `ITEM.Consumable Type: food`; `FOOD_PROPERTIES.Nutritious: true` |
| "Waterskin" or another drink | `ITEM.Consumable Type: food`; `FOOD_PROPERTIES.Hydrating: true` |
| "This wand has 7 charges" and "regains 1d6 + 1 expended charges daily at dawn" | `ITEM.Consumable Type: wand`; `USAGE.Uses Spent: 0`; `USAGE.Uses Max: 7`; `USAGE.Destroy on Empty: false`; `RECOVERY: [{Period: dawn, Type: formula, Formula: "1d6 + 1"}]` |
| "Wondrous item" that is used up, such as dust or a bead | `ITEM.Consumable Type: wondrous` |
| "50 gp" and "1/2 lb." | `COST_AND_WEIGHT.Price Value: 50`; `COST_AND_WEIGHT.Price Denomination: gp`; `COST_AND_WEIGHT.Weight Value: 0.5`; `COST_AND_WEIGHT.Weight Units: lb` |
| "Arrows" or "Rations" in an equipment table (not magic, no rarity) | `ITEM.Rarities: []`; `PROPERTIES.Magical: false`; `INVENTORY.Identified: true` |

### CONSUMABLE: complete field reference

Every section and field a `CONSUMABLE` document can have. A placeholder such as `"[true|false]"` lists the choices; it is not a value to copy. Write only what the source needs.

```yaml
SCHEMA_VERSION: 7
CONSUMABLE:
  SOURCE:
    Book: "[text|n/a]"
    Page: "[text|n/a]"
    Custom: "[text|n/a]"
    License: "[text|n/a]"
    Rules: "[2014|2024|text]"
    Revision: 1 # Finite number, including decimals; native default 1.

  ITEM:
    Icon: "[filepath|n/a]"
    Identifier: "[text|n/a]"
    Sort: 0 # Integer ordering value; preserve an authored zero.
    Name: "[text]"
    Rarities: [] # Native rarity IDs: common, uncommon, rare, veryRare, legendary, artifact. [] means mundane.
    Consumable Type: "[ammo|food|poison|potion|rod|scroll|trinket|wand|wondrous]"
    Consumable Subtype: "[text|n/a]" # Native ID; match the active ammo/poison alias below.

  INVENTORY:
    Quantity: "[integer]"
    Identified: "[true|false]"
    Equipped: "[true|false]"
    Container: "[container name|uuid|n/a]" # Place inside a container: its name in this batch, an Item UUID, or drag-and-drop in the import window.

  COST_AND_WEIGHT:
    Price Value: "[number]"
    Price Denomination: "[pp|gp|ep|sp|cp]"
    Weight Value: "[number]"
    Weight Units: "[lb|tn|kg|Mg]"

  PROPERTIES:
    NPC Equipment: false # Native gear property; shown for NPC inventory.
    Magical: "[true|false]"

  ATTUNEMENT:
    Attuned: false # Current state, independent of Attunement requirement.
    # Store independently of Magical; a bonus does not enable an explicit false property.
    Attunement: "[none|required|optional]"
    Attunement By: "[text|n/a]" # Importer metadata; ignored when Attunement is none.
    Magic Bonus: "[formula|n/a]" # Canonical for every subtype; deterministic, no dice.

  AMMUNITION_PROPERTIES:
    # Booleans remain stored on all subtypes; the type alias is required only for ammo.
    Ammunition Type: "[arrow|bolt|needle|bullet|slingbullet|energycell]" # Omit this alias outside ammo.
    Adamantine: "[true|false]"
    Silvered: "[true|false]"
    Returning: "[true|false]"

  POISON_PROPERTIES:
    # Required for poison; omit this alias section for other subtypes.
    Poison Type: "[contact|ingested|inhaled|injury]"

  SCROLL_PROPERTIES:
    # Shown for scrolls; preserve supplied booleans on other subtypes.
    Concentration: "[true|false]"
    Somatic: "[true|false]"
    Vocal: "[true|false]"
    Ritual: "[true|false]"

  FOOD_PROPERTIES:
    # dnd5e 6.1 or later, Consumable Type food only (rations, water). Earlier dnd5e stores them without using them.
    Hydrating: "[true|false]"
    Nutritious: "[true|false]"

  USAGE:
    # Uses Spent = number of charges ALREADY CONSUMED (0 means all charges are available).
    # Uses Max = maximum count or deterministic formula, e.g. "1 + @prof" (no dice).
    # Example: A fresh item with 5 charges → Uses Spent: 0, Uses Max: 5
    Uses Spent: "[integer|n/a]"
    Uses Max: "[integer|formula|n/a]"
    Destroy on Empty: "[true|false]"

  # Optional, repeatable. Use [] when there is no recovery.
  # With a positive Uses Max or a formula maximum, replace [] with entries shaped like:
  #   - Period: "[lr|sr|day|dawn|dusk|recharge|initiative|turnStart|turnEnd|turn]"
  #     Type: "[recoverAll|loseAll|formula]"
  #     Formula: "[formula|n/a]" # Required for Type formula; recharge uses a 2..6 threshold.
  RECOVERY: []

  DESCRIPTION:
    Description: |
      [multiline HTML content containing Enrichers]

  UNIDENTIFIED_DESCRIPTION:
    Unidentified Name: "[text|n/a]"
    Unidentified Description: |
      [multiline HTML content]

  CHAT_FLAVOR:
    Chat Description: |
      [multiline text content]

  DAMAGE:
    Replace: false
    DAMAGE_DATA:
      Dice Count: "[integer|null]" # Nonnegative integer; null means no dice count.
      Die Denomination: "[integer|null]" # Sheet dice: 4, 6, 8, 10, 12, 20, 100; or null.
      Bonus: "[formula|n/a]"
      Damage Types: [] # Native damage type IDs; e.g. [piercing, fire].
      Modifiers: []
      Custom Enabled: false
      Custom Formula: "[formula|n/a]" # Retained even when Custom Enabled is false.
      Scaling Mode: "[none|whole|half]"
      Scaling Dice Count: "[integer|null]" # Default 1 if omitted; retain explicit 0 or null.
      Scaling Formula: "[formula|n/a]" # Retained even when Scaling Mode is none.

```

### CONSUMABLE: rules

For ammo/poison, write the subtype alias (`AMMUNITION_PROPERTIES.Ammunition Type` or `POISON_PROPERTIES.Poison Type`) and leave `ITEM.Consumable Subtype` out. When both are written they are not compared, and `Consumable Subtype` is the value stored. Canonical Magic Bonus belongs in ATTUNEMENT; the older ammunition label is compatibility input. DAMAGE.Replace changes how ammunition damage is used; preserve explicit false. The damage structure may be stored on other subtypes without making it active. A potion's descriptive healing is not an authored Heal activity unless a companion attachment supplies it.

### CONSUMABLE: field values

#### CONSUMABLE values: Usage & Recovery Rules
| Type | Uses Max | Destroy on Empty | Tracking |
|------|----------|------------------|----------|
| Potions | `1` | `true` | One use each; the quantity drops as they are used |
| Food | `1` | `true` | One use each; the quantity drops as they are used |
| Poison | `1` | `true` | One use each; the quantity drops as they are used |
| Ammunition | `0` | `false` | By Quantity |
| Wands | Charges (e.g., `7`) | `true` or `false` | By Uses |
| Rods | Charges (e.g., `3`) | `true` or `false` | By Uses |
| Trinkets | Varies | Varies | Context-dependent |

#### CONSUMABLE values: Recovery Types
| Type | Formula | Result |
|------|---------|--------|
| `recoverAll` | n/a | Regain all charges |
| `loseAll` | n/a | Lose all remaining charges |
| `formula` | Dice (e.g., `1d6+1`) | Regain rolled amount |
| `formula` | Number (e.g., `5`) | For recharge: regain all on d6 ≥ 5 |

### CONSUMABLE: core example

A free/core consumable import. Preserve the complete description; it does not automatically create source-specific activities or effects.

5e Item Importer. Activity Importer is not required for this example.

```yaml
SCHEMA_VERSION: 7
CONSUMABLE:
  ITEM:
    Name: Potion of Healing
    Rarities: [common]
    Consumable Type: potion
  INVENTORY:
    Quantity: 1
    Identified: true
  COST_AND_WEIGHT:
    Price Value: 50
    Price Denomination: gp
    Weight Value: 0.5
    Weight Units: lb
  PROPERTIES:
    Magical: true
  USAGE:
    Uses Spent: 0
    Uses Max: 1
    Destroy on Empty: true
  DESCRIPTION:
    Description: >
      [[lookup @name]]{The creature} regains [[/heal 2d4 + 2 average]] hit points when they drink
      this potion. The potion's red liquid glimmers when agitated.
```

### CONSUMABLE: check before you answer

These checks are for `CONSUMABLE`. The checks for every Item type are in "Items: check before you answer (every type)".

1. `ITEM.Consumable Type` is one of `ammo`, `food`, `poison`, `potion`, `rod`, `scroll`, `trinket`, `wand`, `wondrous`. `ammunition` fails. Elixirs, oils and philters are `potion`. Use `wondrous` for a wondrous item that is used up, and `trinket` for a consumable that fits no other type.
2. `ammo` needs `AMMUNITION_PROPERTIES.Ammunition Type` and `poison` needs `POISON_PROPERTIES.Poison Type`, or the import fails. Leave `ITEM.Consumable Subtype` out: the importer fills it from those two keys. If you do write it, use the matching dnd5e id (`crossbowBolt` for `bolt`), because a value that disagrees is stored without a warning.
3. Ammunition type words here are `arrow`, `bolt`, `needle`, `bullet`, `slingbullet` and `energycell`. They differ from the ids in the Weapon template.
4. A one-use item (potion, scroll, dose of poison) is `USAGE.Uses Max: 1`, `USAGE.Uses Spent: 0`, `USAGE.Destroy on Empty: true`. Write `Destroy on Empty` with a lowercase `on`; `Destroy On Empty` is ignored with a warning.
5. `INVENTORY.Quantity` is how many of the item there are. Do not put the stack size in `USAGE.Uses Max`. Ammunition has no `USAGE`: twenty arrows are `Quantity: 20`.
6. A +1 on ammunition is `ATTUNEMENT.Magic Bonus: 1` with `PROPERTIES.Magical: true`. Do not use the old `AMMUNITION_PROPERTIES.Magic Bonus` key.
7. `DAMAGE` is for ammunition that deals its own damage. Do not put a potion's healing or a poison's damage there.
8. Scroll components are `SCROLL_PROPERTIES.Vocal` (V), `Somatic` (S), `Concentration` and `Ritual`; `Verbal` gives a warning. `FOOD_PROPERTIES` belongs only to `Consumable Type: food`; on another type it gives a warning. Always write `ITEM.Rarities` (`[]` for mundane arrows, food or poison; a missing key is stored as `common`) and `INVENTORY.Identified` (a missing key follows the user's setting).

## TOOL: Tool Item

- **Root key:** `TOOL`
- **When to use:** Use TOOL when the source is a set of artisan's tools, a gaming set, a musical instrument, or one of the six other tools dnd5e knows: disguise kit, forgery kit, herbalism kit, navigator's tools, poisoner's kit, thieves' tools. A magic version, such as a magic lute, is still a TOOL.
- **Smallest working block:** `ITEM.Name` (the name), `ITEM.Base Tool` (the importer id of the tool; `ITEM.Tool Type` is filled in from it).

**Use another Item type when:**

- A healer's kit, climber's kit, mess kit or other gear that is not a dnd5e tool. `Base Tool` rejects it: CONSUMABLE if it has uses, otherwise LOOT (`Loot Type: gear`).
- A worn item that only gives a bonus to a tool or skill check (gloves, goggles): EQUIPMENT.
- A hammer, sickle or other tool-like object with weapon damage: WEAPON.
- A pack or case that holds tools: CONTAINER.
- A feat or class feature that grants a tool proficiency: FEAT.

### TOOL: from source text to YAML

Find the wording closest to the source and write the fields shown. A path such as `ITEM.Name` means the `Name` key inside the `ITEM` section of `TOOL`. A list such as `RECOVERY` takes one `-` entry per item. Leave out every section the source does not mention.

| The source says | Write |
| --- | --- |
| "Thieves' Tools" | `ITEM.Base Tool: thief` |
| "Smith's Tools" (artisan's tools) | `ITEM.Tool Type: art`; `ITEM.Base Tool: smith` |
| "Lute" (musical instrument) | `ITEM.Tool Type: music`; `ITEM.Base Tool: lute` |
| "Dice Set" (gaming set) | `ITEM.Tool Type: game`; `ITEM.Base Tool: dice` |
| "Herbalism Kit" | `ITEM.Base Tool: herb` |
| "Ability: Dexterity" | `ABILITY_CHECK.Ability: dex`; `ABILITY_CHECK.Proficient: Automatic` |
| "+2 bonus to ability checks made with these tools" | `PROPERTIES.Tool Bonus: 2` |
| "uncommon (requires attunement by a bard)" | `ITEM.Rarities: [uncommon]`; `PROPERTIES.Magical: true`; `ATTUNEMENT.Attunement: required`; `ATTUNEMENT.Attunement By: a bard` |
| "can be used as a spellcasting focus" | `PROPERTIES.Focus: true` |
| "It has 3 charges" and "regains all expended charges daily at dawn" | `USAGE.Uses Spent: 0`; `USAGE.Uses Max: 3`; `RECOVERY: [{Period: dawn, Type: recoverAll}]` |
| "25 gp" and "1 lb." | `COST_AND_WEIGHT.Price Value: 25`; `COST_AND_WEIGHT.Price Denomination: gp`; `COST_AND_WEIGHT.Weight Value: 1`; `COST_AND_WEIGHT.Weight Units: lb` |
| "Thieves' Tools" in an equipment table (not magic, no rarity) | `ITEM.Rarities: []`; `PROPERTIES.Magical: false`; `INVENTORY.Identified: true` |

### TOOL: complete field reference

Every section and field a `TOOL` document can have. A placeholder such as `"[true|false]"` lists the choices; it is not a value to copy. Write only what the source needs.

```yaml
SCHEMA_VERSION: 7
TOOL:
  SOURCE:
    Book: "[text|n/a]"
    Page: "[text|n/a]"
    Custom: "[text|n/a]"
    License: "[text|n/a]"
    Rules: "[2014|2024|text]"
    Revision: 1 # Finite number, including decimals; native default 1.

  ITEM:
    Icon: "[filepath|n/a]"
    Identifier: "[text|n/a]"
    Sort: 0 # Integer ordering value; preserve an authored zero.
    Name: "[text]"
    Rarities: [] # Native rarity IDs: common, uncommon, rare, veryRare, legendary, artifact. [] means mundane.
    Tool Type: "[art|game|music|n/a]"
    # Use n/a for disguise/forgery/herbalism/navigator/poisoner/thieves tools.
    Base Tool: "[e.g. alchemist, smith, thief, lute, dice - see list below]"

  INVENTORY:
    Quantity: "[integer]"
    Identified: "[true|false]"
    Equipped: "[true|false]"
    Container: "[container name|uuid|n/a]" # Place inside a container: its name in this batch, an Item UUID, or drag-and-drop in the import window.

  COST_AND_WEIGHT:
    Price Value: "[number]"
    Price Denomination: "[pp|gp|ep|sp|cp]"
    Weight Value: "[number]"
    Weight Units: "[lb|tn|kg|Mg]"

  PROPERTIES:
    NPC Equipment: false # Native gear property; shown for NPC inventory.
    Focus: false
    Magical: "[true|false]"
    Tool Bonus: "[formula|n/a]" # Finite number or formula, including dice; e.g. "1d4 + @prof".

  ATTUNEMENT:
    Attuned: false # Current state, independent of Attunement requirement.
    # Store independently of Magical; a bonus does not enable an explicit false property.
    Attunement: "[none|required|optional]"
    Attunement By: "[text|n/a]" # Importer metadata; ignored when Attunement is none.

  ABILITY_CHECK:
    Proficient: "[Automatic|0|0.5|1|1.5|2]"
    Ability: "[str|dex|con|int|wis|cha|n/a]"

  USAGE:
    # Uses Spent = number of charges ALREADY CONSUMED (0 means all charges are available).
    # Uses Max = maximum count or deterministic formula, e.g. "1 + @prof" (no dice).
    # Example: A fresh item with 5 charges → Uses Spent: 0, Uses Max: 5
    Uses Spent: "[integer|n/a]"
    Uses Max: "[integer|formula|n/a]"

  # Optional, repeatable. Use [] when there is no recovery.
  # With a positive Uses Max or a formula maximum, replace [] with entries shaped like:
  #   - Period: "[lr|sr|day|dawn|dusk|recharge|initiative|turnStart|turnEnd|turn]"
  #     Type: "[recoverAll|loseAll|formula]"
  #     Formula: "[formula|n/a]" # Required for Type formula; recharge uses a 2..6 threshold.
  RECOVERY: []

  DESCRIPTION:
    Description: |
      [multiline HTML content containing Enrichers]

  UNIDENTIFIED_DESCRIPTION:
    Unidentified Name: "[text|n/a]"
    Unidentified Description: |
      [multiline HTML content]

  CHAT_FLAVOR:
    Chat Description: |
      [multiline text content]

```

### TOOL: rules

Use ABILITY_CHECK for the check ability/proficiency and PROPERTIES.Tool Bonus for its roll bonus. Accepted proficiency values are Automatic/null, 0, 0.5, 1, 1.5 and 2. Tool Bonus may contain dice. Magical and Focus are independent properties. Omit activities in free/core output.

### TOOL: field values

#### TOOL values: Tool Types & Base Tool IDs
| Type | Base Tool IDs |
|------|---------------|
| `art` | `alchemist`, `brewer`, `calligrapher`, `carpenter`, `cartographer`, `cobbler`, `cook`, `glassblower`, `jeweler`, `leatherworker`, `mason`, `painter`, `potter`, `smith`, `tinker`, `weaver`, `woodcarver` |
| `game` | `dice`, `card`, `chess` |
| `music` | `bagpipes`, `drum`, `dulcimer`, `flute`, `horn`, `lute`, `lyre`, `panflute`, `shawm`, `viol` |
| `n/a` | `disg`, `forg`, `herb`, `navg`, `pois`, `thief` |

Legacy short artisan IDs such as `alch` and `calli` are still accepted for backward compatibility, but new templates should use the full IDs below.

#### TOOL values: Artisan Tools Reference
| ID | Tool Name |
|----|-----------|
| `alchemist` | Alchemist's Supplies |
| `brewer` | Brewer's Supplies |
| `calligrapher` | Calligrapher's Supplies |
| `carpenter` | Carpenter's Tools |
| `cartographer` | Cartographer's Tools |
| `cobbler` | Cobbler's Tools |
| `cook` | Cook's Utensils |
| `glassblower` | Glassblower's Tools |
| `jeweler` | Jeweler's Tools |
| `leatherworker` | Leatherworker's Tools |
| `mason` | Mason's Tools |
| `painter` | Painter's Supplies |
| `potter` | Potter's Tools |
| `smith` | Smith's Tools |
| `tinker` | Tinker's Tools |
| `weaver` | Weaver's Tools |
| `woodcarver` | Woodcarver's Tools |

#### TOOL values: Other Tools Reference
| ID | Tool Name |
|----|-----------|
| `disg` | Disguise Kit |
| `forg` | Forgery Kit |
| `herb` | Herbalism Kit |
| `navg` | Navigator's Tools |
| `pois` | Poisoner's Kit |
| `thief` | Thieves' Tools |

#### TOOL values: Proficiency
| Value | Meaning |
|-------|---------|
| `Automatic` | Game auto-detects proficiency from the character (default) |
| `0` | Not proficient |
| `0.5` | Half proficiency |
| `1` | Proficient |
| `1.5` | One and a half proficiency |
| `2` | Expertise (double proficiency) |

These are the accepted YAML proficiency values, including the native 1.5 multiplier.

#### TOOL values: Recharge Values
| Formula | Display |
|---------|---------|
| `6` | Recharge 6 |
| `5` | Recharge 5-6 |
| `4` | Recharge 4-6 |
| `3` | Recharge 3-6 |
| `2` | Recharge 2-6 |

### TOOL: core example

A free/core tool import. Preserve the complete description; it does not automatically create source-specific activities or effects.

5e Item Importer. Activity Importer is not required for this example.

```yaml
SCHEMA_VERSION: 7
TOOL:
  ITEM:
    Name: Alchemist's Supplies
    Rarities: []
    Tool Type: art
    Base Tool: alchemist
  INVENTORY:
    Quantity: 1
    Identified: true
  COST_AND_WEIGHT:
    Price Value: 50
    Price Denomination: gp
    Weight Value: 8
    Weight Units: lb
  PROPERTIES:
    Magical: false
  ABILITY_CHECK:
    Proficient: Automatic
    Ability: int
  DESCRIPTION:
    Description: >
      This set of alchemist's supplies includes glass beakers, a metal frame, a stirring rod, a
      mortar and pestle, and common alchemical ingredients.
```

### TOOL: check before you answer

These checks are for `TOOL`. The checks for every Item type are in "Items: check before you answer (every type)".

1. `ITEM.Base Tool` is required and takes the importer id, not the printed name: `thief` for thieves' tools, `smith`, `alchemist`, `lute`, `dice`. The six kits use `disg`, `forg`, `herb`, `navg`, `pois` and `thief`. Any other word fails.
2. `ITEM.Tool Type` must match the base tool: `art` for artisan's tools, `game` for gaming sets, `music` for instruments. For the six kits leave it out or write `n/a`. A mismatch fails. When unsure, leave `Tool Type` out; the importer fills it in.
3. A bonus to checks made with the tool is `PROPERTIES.Tool Bonus`. It is not an `ABILITY_CHECK` key, and a tool has no `ATTUNEMENT.Magic Bonus` (the importer drops that key without a warning). `Tool Bonus` may hold dice: `"1d4"`.
4. `ABILITY_CHECK.Ability` is a three-letter id: `str`, `dex`, `con`, `int`, `wis` or `cha`. `ABILITY_CHECK.Proficient` is `Automatic`, `0`, `0.5`, `1`, `1.5` or `2`. `Dexterity` or `expertise` is dropped with a warning. Use `Automatic` unless the source fixes the proficiency.
5. A mundane tool has no `USAGE` or `RECOVERY`. Add them only when a magic tool has charges, with `USAGE.Uses Spent: 0` for a full item.
6. Set `PROPERTIES.Magical: true` on a magic tool; `ATTUNEMENT.Attunement: required` does not set it. Always write `ITEM.Rarities` (`[]` for a mundane tool; a missing key is stored as `common`) and `INVENTORY.Identified` (a missing key follows the user's setting).

## LOOT: Loot Item

- **Root key:** `LOOT`
- **When to use:** Use LOOT when the source is an object with a value and a weight but no rules to run: a gem, an art object, trade goods, treasure, a crafting material, junk, or plain adventuring gear such as a bedroll or rope.
- **Smallest working block:** `ITEM.Name` (the name), `ITEM.Loot Type` (`art`, `gear`, `gem`, `junk`, `material`, `resource`, `trade` or `treasure`).

**Use another Item type when:**

- Anything that needs attunement, has charges, or is worn for a benefit: EQUIPMENT.
- Anything that is used up and does something when used (potion, scroll, poison, ammunition): CONSUMABLE.
- Anything that holds other items or coins (pouch, chest): CONTAINER.
- Artisan's tools, kits, gaming sets and musical instruments: TOOL.
- A weapon, armor or a shield, even a mundane one: WEAPON or EQUIPMENT.

### LOOT: from source text to YAML

Find the wording closest to the source and write the fields shown. A path such as `ITEM.Name` means the `Name` key inside the `ITEM` section of `LOOT`. A list such as `RECOVERY` takes one `-` entry per item. Leave out every section the source does not mention.

| The source says | Write |
| --- | --- |
| "a ruby worth 1,000 gp" | `ITEM.Loot Type: gem`; `COST_AND_WEIGHT.Price Value: 1000`; `COST_AND_WEIGHT.Price Denomination: gp` |
| "silver ewer (25 gp)" (art object) | `ITEM.Loot Type: art`; `COST_AND_WEIGHT.Price Value: 25`; `COST_AND_WEIGHT.Price Denomination: gp` |
| "1 lb. of saffron" (trade goods) | `ITEM.Loot Type: trade`; `COST_AND_WEIGHT.Weight Value: 1`; `COST_AND_WEIGHT.Weight Units: lb` |
| "Bedroll" or "Hempen rope" (gear with no rules to run) | `ITEM.Loot Type: gear` |
| "dragon scale" or another monster part or rare metal for crafting | `ITEM.Loot Type: material` |
| "iron ingot", lumber or cloth (crafting stock) | `ITEM.Loot Type: resource` |
| "gold trade bar" or "ancient coin" | `ITEM.Loot Type: treasure` |
| "broken" or "worthless" | `ITEM.Loot Type: junk`; `COST_AND_WEIGHT.Price Value: 0` |
| "six bloodstones (50 gp each)" | `ITEM.Loot Type: gem`; `INVENTORY.Quantity: 6`; `COST_AND_WEIGHT.Price Value: 50`; `COST_AND_WEIGHT.Price Denomination: gp` |
| "uncommon" magic object with nothing to activate, such as a key that glows | `ITEM.Rarities: [uncommon]`; `PROPERTIES.Magical: true` |
| "its nature is hidden until it is identified" | `INVENTORY.Identified: false`; `UNIDENTIFIED_DESCRIPTION.Unidentified Name: Red Stone` |
| "Ruby" or "Bedroll" in a treasure or equipment table (not magic, no rarity) | `ITEM.Rarities: []`; `PROPERTIES.Magical: false`; `INVENTORY.Identified: true` |

### LOOT: complete field reference

Every section and field a `LOOT` document can have. A placeholder such as `"[true|false]"` lists the choices; it is not a value to copy. Write only what the source needs.

```yaml
SCHEMA_VERSION: 7
LOOT:
  SOURCE:
    Book: "[text|n/a]"
    Page: "[text|n/a]"
    Custom: "[text|n/a]"
    License: "[text|n/a]"
    Rules: "[2014|2024|text]"
    Revision: 1 # Finite number, including decimals; native default 1.

  ITEM:
    Loot Subtype: "[text|n/a]"
    Icon: "[filepath|n/a]"
    Identifier: "[text|n/a]"
    Sort: 0 # Integer ordering value; preserve an authored zero.
    Name: "[text]"
    Rarities: [] # Native rarity IDs: common, uncommon, rare, veryRare, legendary, artifact. [] means mundane.
    Loot Type: "[art|gear|gem|junk|material|resource|trade|treasure]"

  INVENTORY:
    Quantity: "[integer]"
    Identified: "[true|false]"
    Container: "[container name|uuid|n/a]" # Place inside a container: its name in this batch, an Item UUID, or drag-and-drop in the import window.

  COST_AND_WEIGHT:
    Price Value: "[number]"
    Price Denomination: "[pp|gp|ep|sp|cp]"
    Weight Value: "[number]"
    Weight Units: "[lb|tn|kg|Mg]"

  PROPERTIES:
    NPC Equipment: false # Native gear property; shown for NPC inventory.
    Magical: "[true|false]"

  DESCRIPTION:
    Description: |
      [multiline HTML content containing Enrichers]

  UNIDENTIFIED_DESCRIPTION:
    Unidentified Name: "[text|n/a]"
    Unidentified Description: |
      [multiline HTML content]

  CHAT_FLAVOR:
    Chat Description: |
      [multiline text content]

```

### LOOT: rules

Loot supports passive companion effects but no Activities. It has no native equipped state. Preserve descriptive uses, crafting requirements and magical rules in the description; do not manufacture activity/resource fields for them.

### LOOT: field values

#### LOOT values: Loot Types
| Type | Description | Examples |
|------|-------------|----------|
| `art` | Artistic objects, paintings, sculptures | Paintings, tapestries, carvings |
| `gear` | Adventuring gear | Rope, rations, torches, a bedroll |
| `gem` | Precious stones | Diamonds, rubies, opals |
| `junk` | Worthless or near-worthless items | Broken tools, broken pottery, rusty nails |
| `material` | Crafting components | Monster parts, rare metals |
| `resource` | Consumable crafting resources | Ingots, lumber, cloth bolts |
| `trade` | Trade goods with mercantile value | Silk, spices, livestock |
| `treasure` | Coins, trade bars, valuables | Gold bars, ancient coins |

#### LOOT values: Common Gem Values (5e Standard)
| Value | Examples |
|-------|----------|
| 10 gp | Azurite, banded agate, blue quartz, moss agate |
| 50 gp | Bloodstone, carnelian, jasper, moonstone, onyx |
| 100 gp | Amber, amethyst, garnet, jade, pearl, tourmaline |
| 500 gp | Alexandrite, aquamarine, black pearl, topaz |
| 1,000 gp | Black opal, blue sapphire, emerald, fire opal, ruby |
| 5,000 gp | Black sapphire, diamond, jacinth, star ruby |

#### LOOT values: Common Art Object Values (5e Standard)
| Value | Examples |
|-------|----------|
| 25 gp | Silver ewer, carved bone statuette, small gold bracelet |
| 250 gp | Gold ring with bloodstones, carved ivory statuette |
| 750 gp | Silver chalice with moonstones, bronze crown |
| 2,500 gp | Gold dragon comb with red garnets, jeweled gold crown |
| 7,500 gp | Gold and ruby ring, gold music box, painting by master |

### LOOT: core example

A free/core loot import. Preserve the complete description; it does not automatically create source-specific activities or effects.

5e Item Importer. Activity Importer is not required for this example.

```yaml
SCHEMA_VERSION: 7
LOOT:
  ITEM:
    Name: Ruby
    Rarities: []
    Loot Type: gem
  INVENTORY:
    Quantity: 1
    Identified: true
  COST_AND_WEIGHT:
    Price Value: 100
    Price Denomination: gp
    Weight Value: 0
    Weight Units: lb
  PROPERTIES:
    Magical: false
  DESCRIPTION:
    Description: |
      A clear red gemstone about one inch across, suitable as treasure or a spell component.
```

### LOOT: check before you answer

These checks are for `LOOT`. The checks for every Item type are in "Items: check before you answer (every type)".

1. `ITEM.Loot Type` is required and is one of `art`, `gear`, `gem`, `junk`, `material`, `resource`, `trade`, `treasure`. `trinket`, `misc` or `wondrous` fail.
2. Loot has no attunement, charges, equipped state or activities. `ATTUNEMENT`, `USAGE` and `RECOVERY` are ignored with a warning. If the source has any of them, the item is EQUIPMENT or CONSUMABLE, not LOOT.
3. `COST_AND_WEIGHT.Price Value` is a plain number for one item: `1000`, not `1,000` and not `1000 gp`. The coin goes in `COST_AND_WEIGHT.Price Denomination` (`pp`, `gp`, `ep`, `sp` or `cp`). The count goes in `INVENTORY.Quantity`.
4. Write the value in `COST_AND_WEIGHT` even when the description also says what the item is worth.
5. Leave `ITEM.Loot Subtype` out. dnd5e has no loot subtypes.
6. Always write `ITEM.Rarities` (`[]` for an ordinary gem, art object or piece of gear; a missing key is stored as `common`) and `INVENTORY.Identified` (a missing key follows the user's setting). Use `PROPERTIES.Magical: true` only when the source says the object is magic.

## CONTAINER: Container Item

- **Root key:** `CONTAINER`
- **When to use:** Use CONTAINER when the source is an item that holds other items or coins: a backpack, sack, pouch, chest, quiver, case, bag of holding or portable hole. The items inside go under `CONTENTS`, each as its own Item.
- **Smallest working block:** `ITEM.Name` (the name). Nothing else is required.

**Use another Item type when:**

- The items inside the container. Each one is its own WEAPON, EQUIPMENT, CONSUMABLE, TOOL, LOOT or CONTAINER entry under `CONTENTS`.
- A flask, vial or waterskin, where the liquid is the item: CONSUMABLE.
- A worn item with no storage rules (belt, cloak): EQUIPMENT.
- A spell that creates a storage space: SPELL.

### CONTAINER: from source text to YAML

Find the wording closest to the source and write the fields shown. A path such as `ITEM.Name` means the `Name` key inside the `ITEM` section of `CONTAINER`. A list such as `RECOVERY` takes one `-` entry per item. Leave out every section the source does not mention.

| The source says | Write |
| --- | --- |
| "can hold up to 30 pounds of gear" or "1 cubic foot" | `CAPACITY.Weight Capacity Value: 30`; `CAPACITY.Weight Capacity Units: lb`; `CAPACITY.Volume Capacity Value: 1`; `CAPACITY.Volume Capacity Units: cubicFoot` |
| "holds up to 20 arrows" | `CAPACITY.Item Count: 20` |
| "weighs 15 pounds, regardless of its contents" | `PROPERTIES.Weightless Contents: true`; `COST_AND_WEIGHT.Weight Value: 15`; `COST_AND_WEIGHT.Weight Units: lb` |
| "contains 120 gp and 45 sp" | `CURRENCY_CONTENTS.Platinum: 0`; `CURRENCY_CONTENTS.Gold: 120`; `CURRENCY_CONTENTS.Electrum: 0`; `CURRENCY_CONTENTS.Silver: 45`; `CURRENCY_CONTENTS.Copper: 0` |
| "contains a crowbar" (items inside) | `CONTENTS: [{LOOT: {ITEM: {Name: Crowbar, Rarities: [], Loot Type: gear}}}]` |
| "Wondrous item, uncommon (requires attunement)" | `ITEM.Rarities: [uncommon]`; `PROPERTIES.Magical: true`; `ATTUNEMENT.Attunement: required` |
| "2 gp" and "5 lb." | `COST_AND_WEIGHT.Price Value: 2`; `COST_AND_WEIGHT.Price Denomination: gp`; `COST_AND_WEIGHT.Weight Value: 5`; `COST_AND_WEIGHT.Weight Units: lb` |
| "its contents cannot be seen until it is identified" | `PROPERTIES.Unidentified Contents: true`; `INVENTORY.Identified: false` |
| "Backpack" in an equipment table (not magic, no rarity) | `ITEM.Rarities: []`; `PROPERTIES.Magical: false`; `INVENTORY.Identified: true` |

### CONTAINER: complete field reference

Every section and field a `CONTAINER` document can have. A placeholder such as `"[true|false]"` lists the choices; it is not a value to copy. Write only what the source needs.

```yaml
SCHEMA_VERSION: 7
CONTAINER:
  SOURCE:
    Book: "[text|n/a]"
    Page: "[text|n/a]"
    Custom: "[text|n/a]"
    License: "[text|n/a]"
    Rules: "[2014|2024|text]"
    Revision: 1 # Finite number, including decimals; native default 1.

  ITEM:
    Icon: "[filepath|n/a]"
    Identifier: "[text|n/a]"
    Sort: 0 # Integer ordering value; preserve an authored zero.
    Name: "[text]"
    Rarities: [] # Native rarity IDs: common, uncommon, rare, veryRare, legendary, artifact. [] means mundane.

  INVENTORY:
    Quantity: 1 # Compatibility input only: creation fixes quantity at 1; export omits it.
    Identified: "[true|false]"
    Equipped: "[true|false]"
    Container: "[container name|uuid|n/a]" # Place inside a container: its name in this batch, an Item UUID, or drag-and-drop in the import window.

  # Optional, repeatable. Items created inside this container. Each entry is one complete
  # Item document (WEAPON, EQUIPMENT, CONSUMABLE, TOOL, LOOT or another CONTAINER) and may
  # nest further, five containers deep at most. Contents import with the container through
  # the batch flow (import window or parseAll); leave [] for an empty container.
  CONTENTS: []

  COST_AND_WEIGHT:
    Price Value: "[number]"
    Price Denomination: "[pp|gp|ep|sp|cp]"
    Weight Value: "[number]"
    Weight Units: "[lb|tn|kg|Mg]"

  PROPERTIES:
    NPC Equipment: false # Native gear property; shown for NPC inventory.
    Magical: "[true|false]"
    Weightless Contents: "[true|false]"
    Unidentified Contents: "[true|false]" # Players cannot see the contents while the container is unidentified.

  ATTUNEMENT:
    Attuned: false # Current state, independent of Attunement requirement.
    # Store independently of Magical; a bonus does not enable an explicit false property.
    Attunement: "[none|required|optional]"
    Attunement By: "[text|n/a]" # Importer metadata; ignored when Attunement is none.

  CAPACITY:
    Item Count: "[integer|null]" # Nonnegative integer or no stored count limit.
    Weight Capacity Value: "[number|null]" # Nonnegative decimal or no stored limit.
    Weight Capacity Units: "[lb|tn|kg|Mg|n/a]"
    Volume Capacity Value: "[number|null]" # Nonnegative decimal or no stored limit.
    Volume Capacity Units: "[cubicFoot|liter|n/a]"

  CURRENCY_CONTENTS:
    # Optional. Omit this entire section if the container holds no coins.
    # If included, all fields are required; use 0 for empty denominations.
    Platinum: "[integer]"
    Gold: "[integer]"
    Electrum: "[integer]"
    Silver: "[integer]"
    Copper: "[integer]"

  DESCRIPTION:
    Description: |
      [multiline HTML content containing Enrichers]

  UNIDENTIFIED_DESCRIPTION:
    Unidentified Name: "[text|n/a]"
    Unidentified Description: |
      [multiline HTML content]

  CHAT_FLAVOR:
    Chat Description: |
      [multiline text content]
      
```

### CONTAINER: rules

Container supports passive companion effects but no Activities. Its builder fixes quantity to 1 and canonical export omits quantity; the reference cannot promise arbitrary quantity round trips. Contents are authored two ways: nest complete Item documents under `CONTENTS` (they are created inside this container, in its collection and folder, up to five containers deep), or give a contained Item `INVENTORY.Container` naming a container in the same batch, a container Item UUID, or a container dropped onto its reference in the import window. A `CONTAINER` with `CONTENTS` imports through the batch flow; `parse()` and `api.import()` handle one Item and report it. Export nests world and Actor contents under `CONTENTS`; compendium contents load asynchronously, so export those packs as batches, where contained Items keep `INVENTORY.Container` references. Capacity and weightless storage do not create contents from description text.

### CONTAINER: field values

#### CONTAINER values: Capacity Rules
| Field | Description |
|-------|-------------|
| `Item Count` | Maximum number of discrete items (null or legacy n/a = no stored count limit) |
| `Weight Capacity` | Maximum weight the container can hold |
| `Volume Capacity` | Maximum volume the container can hold |
| `Weightless Contents` | If `true`, contents don't add to carried weight |
| `Unidentified Contents` | If `true`, players cannot see what is inside while the container is unidentified (`INVENTORY.Identified: false`) |

#### CONTAINER values: Volume Units
| Value | Description |
|-------|-------------|
| `cubicFoot` | Cubic feet (do NOT use `ft^3` or `cu ft`) |
| `liter` | Liters |

#### CONTAINER values: Common Container Capacities
| Container | Weight | Volume | Notes |
|-----------|--------|--------|-------|
| Backpack | 30 lb | 1 cu ft | Standard adventuring gear |
| Bag of Holding | 500 lb | 64 cu ft | Weightless contents |
| Portable Hole | 10,000 lb | 282 cu ft | 6 ft diameter, 10 ft deep |
| Handy Haversack | 120 lb | ~12 cu ft | Weightless, retrieval bonus |

### CONTAINER: core example

A free/core container import. Preserve the complete description; it does not automatically create source-specific activities or effects.

5e Item Importer. Activity Importer is not required for this example.

```yaml
SCHEMA_VERSION: 7
CONTAINER:
  ITEM:
    Name: Bag of Holding
    Rarities: [uncommon]
  INVENTORY:
    Quantity: 1
    Identified: true
  COST_AND_WEIGHT:
    Price Value: 400
    Price Denomination: gp
    Weight Value: 5
    Weight Units: lb
  PROPERTIES:
    Magical: true
    Weightless Contents: true
  ATTUNEMENT:
    Attunement: none
  CAPACITY:
    Weight Capacity Value: 500
    Weight Capacity Units: lb
    Volume Capacity Value: 64
    Volume Capacity Units: cubicFoot
  DESCRIPTION:
    Description: >
      This bag has an interior space considerably larger than its outside dimensions. The bag can
      hold up to 500 pounds, not exceeding a volume of 64 cubic feet.
```

### CONTAINER: check before you answer

These checks are for `CONTAINER`. The checks for every Item type are in "Items: check before you answer (every type)".

1. A container has no type key: `ITEM` holds `Name` and `Rarities`. `Container Type`, `USAGE` and `RECOVERY` are ignored with a warning, and a container never has `Activities`.
2. `CURRENCY_CONTENTS` needs all five keys (`Platinum`, `Gold`, `Electrum`, `Silver`, `Copper`) as whole numbers, with `0` for coins that are absent. A missing key fails. Leave the whole section out when there are no coins.
3. Write only the capacity limits the source gives and leave the other `CAPACITY` keys out. `0` gives a warning. Values are plain numbers: `500`, not `500 lb`. `CAPACITY.Volume Capacity Units` is `cubicFoot` or `liter`.
4. `COST_AND_WEIGHT.Weight Value` is the weight of the empty container. Do not add the contents.
5. Each `CONTENTS` entry is one full Item under exactly one root key: `WEAPON`, `EQUIPMENT`, `CONSUMABLE`, `TOOL`, `LOOT` or `CONTAINER`. Do not list bare names, do not repeat `SCHEMA_VERSION` inside an entry, and do not write `INVENTORY.Container` on a nested Item. Each of these fails.
6. A container always has quantity 1. A higher `INVENTORY.Quantity` is changed to 1 without a warning.
7. Set `PROPERTIES.Magical: true` on a magic container; `ATTUNEMENT.Attunement: required` does not set it. Always write `ITEM.Rarities` (`[]` for a mundane container; a missing key is stored as `common`) and `INVENTORY.Identified` (a missing key follows the user's setting).

## SPELL: Spell Item

- **Root key:** `SPELL`
- **When to use:** Use SPELL when the source is a spell description: a level and school line, casting time, range, components and duration. A cantrip is a spell of level 0.
- **Smallest working block:** `ITEM.Name` (the name), `ITEM.Level` (0 to 9; 0 is a cantrip), `ITEM.School` (three-letter id), `ACTIVATION.Type` (the casting time unit). Without `RANGE` the importer stores range units `ft` with no value, and without `DURATION` it stores `inst`, so write both for a real spell.

**Use another Item type when:**

- A spell scroll: CONSUMABLE (`Consumable Type: scroll`).
- A magic item that casts a spell (wand, staff, ring): EQUIPMENT or WEAPON, with the spell named in the description.
- A class feature, feat, invocation, maneuver or psionic power with no spell level and school: FEAT.
- A monster trait or action, even a magical one (breath weapon, innate ability): FEAT.

### SPELL: from source text to YAML

Find the wording closest to the source and write the fields shown. A path such as `ITEM.Name` means the `Name` key inside the `ITEM` section of `SPELL`. A list such as `RECOVERY` takes one `-` entry per item. Leave out every section the source does not mention.

| The source says | Write |
| --- | --- |
| "3rd-level evocation" or "Level 3 Evocation" | `ITEM.Level: 3`; `ITEM.School: evo` |
| "Casting Time: 1 action" | `ACTIVATION.Type: action`; `ACTIVATION.Value: 1` |
| "Casting Time: 1 reaction, which you take when you are hit by an attack" | `ACTIVATION.Type: reaction`; `ACTIVATION.Value: 1`; `ACTIVATION.Condition: "which you take when you are hit by an attack"` |
| "Range: 150 feet" | `RANGE.Units: ft`; `RANGE.Value: 150` |
| "Range: Self (15-foot cone)" | `RANGE.Units: self`; `AREA.Shape: cone`; `AREA.Size: 15`; `AREA.Units: ft` |
| "Components: V, S, M (a tiny ball of bat guano and sulfur)" | `COMPONENTS.Vocal: true`; `COMPONENTS.Somatic: true`; `COMPONENTS.Material: true`; `COMPONENTS.Ritual: false`; `MATERIALS.Value: "a tiny ball of bat guano and sulfur"`; `MATERIALS.Consumed: false` |
| "M (a diamond worth 300+ gp, which the spell consumes)" | `COMPONENTS.Material: true`; `MATERIALS.Value: "a diamond worth 300+ gp, which the spell consumes"`; `MATERIALS.Cost: 300`; `MATERIALS.Consumed: true` |
| "Duration: Concentration, up to 1 minute" | `DURATION.Units: minute`; `DURATION.Value: 1`; `DURATION.Concentration: true` |
| "(ritual)" after the school, or "Casting Time: 1 action or Ritual" | `COMPONENTS.Ritual: true`; `PREPARATION.Method: spell` |
| "each creature in a 20-foot-radius sphere" | `TARGETS.Type: creature`; `AREA.Shape: sphere`; `AREA.Size: 20`; `AREA.Units: ft` |
| "one creature you can see within range" | `TARGETS.Type: creature`; `TARGETS.Count: 1` |
| "you can cast it once without a spell slot" and "you regain the ability when you finish a long rest" | `PREPARATION.Method: innate`; `USAGE.Uses Spent: 0`; `USAGE.Uses Max: 1`; `RECOVERY: [{Period: lr, Type: recoverAll}]` |

### SPELL: complete field reference

Every section and field a `SPELL` document can have. A placeholder such as `"[true|false]"` lists the choices; it is not a value to copy. Write only what the source needs.

```yaml
SCHEMA_VERSION: 7
SPELL:
  SOURCE:
    Book: "[text|n/a]"
    Page: "[text|n/a]"
    Custom: "[text|n/a]"
    License: "[text|n/a]"
    Rules: "[2014|2024|text]"
    Revision: 1 # Finite number, including decimals; native default 1.

  ITEM:
    Icon: "[filepath|n/a]"
    Identifier: "[text|n/a]"
    Sort: 0 # Integer ordering value; preserve an authored zero.
    Name: "[text]"
    Level: "[0|1|2|3|4|5|6|7|8|9]"
    School: "[abj|con|div|enc|evo|ill|nec|trs|n/a]" # n/a stores an empty school.
    Ability: "[str|dex|con|int|wis|cha|n/a]"

  COMPONENTS:
    Vocal: "[true|false]"
    Somatic: "[true|false]"
    Material: "[true|false]"
    Ritual: "[true|false]"

  MATERIALS:
    # Used when Material is true; retain all supplied settings when it is false.
    Value: "[text]"
    Cost: "[integer|decimal]"
    Supply: "[integer|decimal]"
    Consumed: "[true|false]"

  PREPARATION:
    Method: "[atwill|innate|ritual|pact|spell]"
    Prepared: "[unprepared|prepared|always]"
    Source Item: "[identifier|n/a]"

  ACTIVATION:
    Type: "[action|bonus|reaction|minute|hour|day|special]"
    Value: "[integer]" # Nonnegative activation cost; preserve when timing hides the count.
    Condition: "[text|n/a]"

  RANGE:
    # Value and Special remain stored for self/touch/spec/any units.
    Special: "[text|n/a]"
    Units: "[self|touch|spec|any|ft|mi|m|km]"
    Value: "[formula|n/a]"

  DURATION:
    # Preserve Value and Special even when the selected unit hides those controls.
    Expiry: null # Native expiry key (e.g. turnEnd), or null for default expiry.
    Special: "[text|n/a]"
    Units: "[inst|spec|turn|round|minute|hour|day|month|year|disp|dstr|perm]"
    Value: "[formula|n/a]"
    Concentration: "[true|false]"

  TARGETS:
    Type: "[self|ally|enemy|creature|object|space|creatureOrObject|any|willing|n/a]"
    Count: "[formula|n/a]"
    Choice: "[true|false]"
    Special: "[text|n/a]"

  AREA:
    Stationary: false
    # Used when Shape selects an area; retain dimensions and booleans with no shape.
    Shape: "[cone|cube|cylinder|radius|ring|line|sphere|circle|square|wall|n/a]"
    Size: "[formula|n/a]"
    Units: "[ft|mi|m|km]"
    Count: "[formula|n/a]"
    Width: "[formula|n/a]"
    Height: "[formula|n/a]"
    Contiguous: "[true|false]" # Preserve independently of Shape and Count.

  USAGE:
    # Uses Spent = number of charges ALREADY CONSUMED (0 means all charges are available).
    # Uses Max = maximum count or deterministic formula, e.g. "1 + @prof" (no dice).
    # Example: A fresh item with 5 charges → Uses Spent: 0, Uses Max: 5
    Uses Spent: "[integer|n/a]"
    Uses Max: "[integer|formula|n/a]"

  # Optional, repeatable. Use [] when there is no recovery.
  # With a positive Uses Max or a formula maximum, replace [] with entries shaped like:
  #   - Period: "[lr|sr|day|dawn|dusk|recharge|initiative|turnStart|turnEnd|turn]"
  #     Type: "[recoverAll|loseAll|formula]"
  #     Formula: "[formula|n/a]" # Required for Type formula; recharge uses a 2..6 threshold.
  RECOVERY: []

  DESCRIPTION:
    Description: |
      [multiline HTML content containing Enrichers]

  CHAT_FLAVOR:
    Chat Description: |
      [multiline text content]

```

### SPELL: rules

This is a native Spell Item, not a physical inventory item: rarity, quantity, price, weight and unidentified-description fields are not current spell authoring controls. Use Prepared:unprepared/prepared/always for native 0/1/2 state. COMPONENTS.Ritual is independent of Method; use Method:spell for an ordinary ritual-capable prepared spell. Preserve material cost/supply, consumed state and inactive components independently. Source-specific attacks, saves, healing and effects require companion attachments in full output.

Omit optional resources the source does not give rather than inventing them. Formula Uses Max must be deterministic, one line and at most 200 characters, without dice or semicolons; recovery needs a positive literal maximum or a formula maximum.

### SPELL: field values

#### SPELL values: Spellcasting Ability Override
Use `Ability` in the `ITEM` section to override the class spellcasting ability for this specific spell. Useful for racial or feat-granted spells that use a fixed ability (e.g., `cha` for Tiefling spells, `int` for Eldritch Knight spells). Set to `n/a` to use the class default.

| Value | Ability |
|-------|---------|
| `str` | Strength |
| `dex` | Dexterity |
| `con` | Constitution |
| `int` | Intelligence |
| `wis` | Wisdom |
| `cha` | Charisma |
| `n/a` | Use class default |

#### SPELL values: Spell Schools
| Key | School |
|-----|--------|
| `abj` | Abjuration |
| `con` | Conjuration |
| `div` | Divination |
| `enc` | Enchantment |
| `evo` | Evocation |
| `ill` | Illusion |
| `nec` | Necromancy |
| `trs` | Transmutation |

#### SPELL values: Preparation Methods
| Method | Description |
|--------|-------------|
| `atwill` | At Will (always available, no slot needed) |
| `innate` | Innate (uses per day, not spell slots) |
| `ritual` | Legacy ritual-only preparation (always prepared); when Components Ritual is omitted, also enables the ritual property. |
| `pact` | Pact Magic (Warlock slot) |
| `spell` | Standard spellbook/prepared spell |

Legacy `prepared` is still accepted for backward compatibility, but new templates should use `spell`.

#### SPELL values: Ritual Casting and Materials

| Field | Description | Accepted Values |
|---|---|---|
| `COMPONENTS.Ritual` | Whether this spell can be cast as a ritual, independently of preparation. A normal ritual-capable spell uses `Ritual: true` with `PREPARATION.Method: spell`. | `true` or `false`; default `false`. |
| `MATERIALS.Cost` | Material component cost in gold pieces. | Finite nonnegative number (integer or decimal); `0` when no cost is stated. |
| `MATERIALS.Supply` | Tracked supply of material components. | Finite nonnegative number (integer or decimal); `0` when no supply is tracked. |

New YAML must state `COMPONENTS.Ritual` explicitly. An explicit boolean takes precedence over the legacy `Method: ritual` implication. Older YAML with an omitted, null, or `n/a` Ritual field retains that legacy inference.

#### SPELL values: Activation Types
| Type | Description |
|------|-------------|
| `action` | Action |
| `bonus` | Bonus Action |
| `reaction` | Reaction |
| `minute` | Minutes (use Value for count) |
| `hour` | Hours (use Value for count) |
| `day` | Days |
| `special` | Special |

#### SPELL values: Range Units
| Unit | Description |
|------|-------------|
| `self` | Self (no range value needed) |
| `touch` | Touch |
| `spec` | Special |
| `any` | Unlimited / Any distance |
| `ft` | Feet |
| `mi` | Miles |
| `m` | Meters |
| `km` | Kilometers |

#### SPELL values: Duration Units
| Unit | Description |
|------|-------------|
| `inst` | Instantaneous |
| `spec` | Special |
| `turn` | Turn |
| `round` | Rounds |
| `minute` | Minutes |
| `hour` | Hours |
| `day` | Days |
| `month` | Months |
| `year` | Years |
| `disp` | Until dispelled |
| `dstr` | Until dispelled or triggered |
| `perm` | Permanent |

#### SPELL values: Target Types
| Type | Description |
|------|-------------|
| `self` | Self |
| `ally` | Ally |
| `enemy` | Enemy |
| `creature` | Any creature |
| `object` | Object |
| `space` | Space/point |
| `creatureOrObject` | Creature or Object |
| `any` | Any target |
| `willing` | Willing creature |

#### SPELL values: Area Shapes
| Shape | Description |
|-------|-------------|
| `cone` | Cone |
| `cube` | Cube |
| `cylinder` | Cylinder |
| `radius` | Emanation around the caster |
| `ring` | Ring; `Width` is its thickness |
| `line` | Line |
| `sphere` | Sphere |
| `circle` | Circle |
| `square` | Square |
| `wall` | Wall |

#### SPELL values: Area Advanced Fields
| Field | Description | Example |
|-------|-------------|---------|
| `Count` | Number of separate template areas | *Conjure Animals* (multiple zones) |
| `Width` | Width of the area (for line/wall shapes) | *Wall of Fire* width |
| `Height` | Height of the area (for cylinder/wall shapes) | *Cloudkill* height |
| `Contiguous` | Whether multiple templates must be adjacent | `true` or `false` |

These fields are optional and only needed for spells with unusual area geometry. Most spells only need `Shape`, `Size`, and `Units`.

### SPELL: core example

A free/core spell import. Preserve the complete description; it does not automatically create source-specific activities or effects.

5e Item Importer. Activity Importer is not required for this example.

```yaml
SCHEMA_VERSION: 7
SPELL:
  ITEM:
    Name: Magic Missile
    Level: 1
    School: evo
    Ability: n/a
  COMPONENTS:
    Vocal: true
    Somatic: true
    Material: false
    Ritual: false
  PREPARATION:
    Method: spell
    Prepared: prepared
  ACTIVATION:
    Type: action
    Value: 1
    Condition: n/a
  RANGE:
    Units: ft
    Value: 120
  DURATION:
    Units: inst
    Value: n/a
    Concentration: false
  TARGETS:
    Type: creature
    Count: 3
    Choice: true
    Special: Up to three creatures; each dart can target the same or a different creature.
  USAGE:
    Uses Spent: 0
    Uses Max: n/a
  RECOVERY: []
  DESCRIPTION:
    Description: >
      <p>You create three glowing darts of magical force. Each dart hits a creature of your choice
      that you can see within range and deals [[/damage 1d4 + 1 force]] damage. The darts strike
      simultaneously, and you can direct them at one creature or several.</p>

      <p><strong>At Higher Levels.</strong> One additional dart is created for each spell slot level
      above 1.</p>
  CHAT_FLAVOR:
    Chat Description: |
      n/a
```

### SPELL: check before you answer

These checks are for `SPELL`. The checks for every Item type are in "Items: check before you answer (every type)".

1. `ITEM.School` is a three-letter id: `abj`, `con`, `div`, `enc`, `evo`, `ill`, `nec` or `trs`. `evocation` fails. `ITEM.Level` is a number from 0 to 9. A cantrip is `0`; `cantrip` or `3rd` fails.
2. V is `COMPONENTS.Vocal`, not `Verbal`. Concentration is `DURATION.Concentration`, not a `COMPONENTS` key. The wrong key is ignored with a warning, so the spell loses that property.
3. A ritual spell is `COMPONENTS.Ritual: true` with `PREPARATION.Method: spell`. Do not write `Method: ritual` for it. `PREPARATION.Prepared` is `unprepared`, `prepared` or `always`.
4. `ACTIVATION.Type` is one id: `action`, `bonus`, `reaction`, `minute`, `hour`, `day` or `special`. `ACTIVATION.Value` is a whole number. "10 minutes" is `Type: minute` and `Value: 10`. `bonus action` fails.
5. Units are ids: `ft`, not `feet`; `minute`, not `minutes`; `inst`, not `instantaneous`. "Until dispelled" is `disp`. An unknown unit gives a warning.
6. "Self (15-foot cone)" is `RANGE.Units: self` plus an `AREA` section. Do not put the area size in `RANGE.Value`. Write `AREA` only when the spell has an area. A 2024 Emanation is `AREA.Shape: radius`.
7. The material text goes in `MATERIALS.Value`. A gp price goes in `MATERIALS.Cost` as a plain number: `300`, not `300 gp`. "Which the spell consumes" is `MATERIALS.Consumed: true`. Set `COMPONENTS.Material: true` as well.
8. A spell has no rarity, price, weight, quantity or attunement. `ITEM.Rarities`, `INVENTORY`, `COST_AND_WEIGHT`, `UNIDENTIFIED_DESCRIPTION` and `ATTUNEMENT` are ignored with a warning. Leave `USAGE` and `RECOVERY` out for a spell that is cast with spell slots.

## FEAT: Feat Item

- **Root key:** `FEAT`
- **When to use:** Use FEAT when the source is something a creature has, not something it carries: a feat, a class or subclass feature, a species trait, a background feature, a monster trait or action, a boon, blessing or charm, a fighting style, an eldritch invocation, a metamagic option or a maneuver.
- **Smallest working block:** `ITEM.Name` (the name). Nothing else is required, but write `ITEM.Feature Type` so the sheet shows the right kind of feature.

**Use another Item type when:**

- A whole class, subclass, species or background. These templates do not cover them; FEAT holds one feature at a time.
- A spell, even one granted by a feat or a species: SPELL.
- A monster's weapon attack (Bite, Claw, Longsword): WEAPON (`Weapon Type: natural` for a bite or claw).
- A magic item: EQUIPMENT, WEAPON, CONSUMABLE, TOOL or CONTAINER.

### FEAT: from source text to YAML

Find the wording closest to the source and write the fields shown. A path such as `ITEM.Name` means the `Name` key inside the `ITEM` section of `FEAT`. A list such as `RECOVERY` takes one `-` entry per item. Leave out every section the source does not mention.

| The source says | Write |
| --- | --- |
| "2nd-level fighter feature" | `ITEM.Feature Type: class`; `ITEM.Requirements: Fighter 2` |
| "General Feat (Prerequisite: Level 4+)" | `ITEM.Feature Type: feat`; `ITEM.Feature Subtype: general`; `ITEM.Requirements: Level 4+`; `PREREQUISITES.Level: 4` |
| "Eldritch Invocation" | `ITEM.Feature Type: class`; `ITEM.Feature Subtype: eldritchInvocation` |
| A species or lineage trait, such as "Darkvision" in a species entry | `ITEM.Feature Type: race`; `PROPERTIES.Passive Trait: true` |
| A monster trait, such as "Pack Tactics" in a stat block | `ITEM.Feature Type: monster`; `PROPERTIES.Passive Trait: true` |
| "Once you use this feature, you must finish a short or long rest before you can use it again" | `USAGE.Uses Spent: 0`; `USAGE.Uses Max: 1`; `RECOVERY: [{Period: sr, Type: recoverAll}]` |
| "a number of times equal to your Proficiency Bonus" and "you regain all expended uses when you finish a Long Rest" | `USAGE.Uses Spent: 0`; `USAGE.Uses Max: "@prof"`; `RECOVERY: [{Period: lr, Type: recoverAll}]` |
| "(Recharge 5-6)" | `USAGE.Uses Spent: 0`; `USAGE.Uses Max: 1`; `RECOVERY: [{Period: recharge, Type: recoverAll, Formula: "5"}]` |
| "Repeatable" or "You can take this feat more than once" | `PREREQUISITES.Repeatable: true` |
| "Increase your Constitution score by 1, to a maximum of 20" | `ADVANCEMENT: [{ABILITY_SCORE_IMPROVEMENT: {Points: 0, FIXED: [{Ability: con, Value: 1}]}}]` |
| "You gain proficiency in the Athletics skill" | `ADVANCEMENT: [{TRAIT: {Grants: ["skills:ath"]}}]` |
| "You always have the Thunderwave spell prepared" | `ADVANCEMENT: [{GRANT_ITEMS: {ITEMS: [{Name: Thunderwave, Type: spell}], SPELL: {Method: spell, Prepared: always}}}]` |

### FEAT: complete field reference

Every section and field a `FEAT` document can have. A placeholder such as `"[true|false]"` lists the choices; it is not a value to copy. Write only what the source needs.

```yaml
SCHEMA_VERSION: 7
FEAT:
  SOURCE:
    Book: "[text|n/a]"
    Page: "[text|n/a]"
    Custom: "[text|n/a]"
    License: "[text|n/a]"
    Rules: "[2014|2024|text]"
    Revision: 1 # Finite number, including decimals; native default 1.

  ITEM:
    Icon: "[filepath|n/a]"
    Identifier: "[text|n/a]"
    Sort: 0 # Integer ordering value; preserve an authored zero.
    Name: "[text]"
    Feature Type: "[background|class|monster|race|enchantment|feat|pietyTrait|supernaturalGift|vehicle|n/a]" # n/a stores a blank type.
    Feature Subtype: "[subtype identifier|n/a]" # Only class, enchantment, feat, supernaturalGift and (dnd5e 6.1 or later) race define subtypes.
    Requirements: "[text|n/a]" # Free text shown on the sheet, for example "Fighter 1" or "Level 4+".

  PREREQUISITES:
    Level: "[integer|n/a]" # Minimum character level for the feat picker; n/a stores null.
    Items: [] # List of "identifier" or "type:identifier" entries, for example [fighter, "feat:alert"].
    Repeatable: "[true|false]"

  PROPERTIES:
    Magical: "[true|false]"
    Passive Trait: "[true|false]"

  ENCHANTMENT:
    # Stored for every feature; the sheet shows these controls only for an
    # enchantment source (artificerInfusion or rune subtype).
    Max: "[formula|n/a]" # Deterministic maximum number of active enchantments.
    Period: "[sr|lr|atwill|n/a]" # Replacement period; n/a stores Never.

  VEHICLE_PROPERTIES:
    # Cover is shown only for vehicle features; both values are retained otherwise.
    Cover: "[none|half|threequarters|total|0..1|n/a]"
    Crewed: "[true|false]"

  USAGE:
    # Uses Spent = number of uses ALREADY CONSUMED (0 means all uses are available).
    # Uses Max = maximum count or deterministic formula, e.g. "@prof" (no dice).
    # Example: A feature usable twice per rest with one use spent → Uses Spent: 1, Uses Max: 2
    Uses Spent: "[integer|n/a]"
    Uses Max: "[integer|formula|n/a]"

  # Optional, repeatable. Use [] when there is no recovery.
  # With a positive Uses Max or a formula maximum, replace [] with entries shaped like:
  #   - Period: "[lr|sr|day|dawn|dusk|recharge|initiative|turnStart|turnEnd|turn]"
  #     Type: "[recoverAll|loseAll|formula]"
  #     Formula: "[formula|n/a]" # Required for Type formula; recharge uses a 2..6 threshold.
  RECOVERY: []

  # Optional, repeatable. One root key per entry; omit the list when the feature
  # grants nothing. Referenced documents resolve by UUID, Name or Identifier; the
  # import window shows a drop zone for anything missing or ambiguous, and import
  # waits until every reference resolves. HitPoints, Size and Subclass belong to
  # classes and species and are rejected here.
  ADVANCEMENT:
    - ABILITY_SCORE_IMPROVEMENT:
        Advancement ID: n/a # Optional 16-character stable ID; omit to allocate one.
        Level: 0 # 0 = any level (the feat default).
        Name: "[text|n/a]"
        Hint: "[text|n/a]"
        Icon: "[filepath|n/a]"
        Class Restriction: "[primary|secondary|n/a]"
        Points: "[integer]" # Free points the player assigns.
        Cap: "[integer]" # Maximum increase to one ability (native default 2).
        Max: "[integer|n/a]" # Maximum resulting score; n/a keeps the native 20.
        Locked: [] # Ability identifiers that cannot receive points.
        FIXED:
          - Ability: "[str|dex|con|int|wis|cha]"
            Value: "[integer]"
        RECOMMENDATION: # Optional recommended feat (epic boon boons).
          UUID: "[uuid|n/a]"
          Name: "[text|n/a]"
          Identifier: "[text|n/a]"
    - GRANT_ITEMS:
        Advancement ID: n/a
        Level: 0
        Name: "[text|n/a]"
        Hint: "[text|n/a]"
        Icon: "[filepath|n/a]"
        Class Restriction: "[primary|secondary|n/a]"
        Optional: "[true|false]" # Let the player decline the grant.
        Sorting: "[a|m]" # a = alphabetical, m = manual (Sort values).
        ITEMS:
          - UUID: "[uuid|n/a]"
            Name: "[text|n/a]"
            Identifier: "[text|n/a]"
            Type: "[feat|spell|consumable|container|equipment|loot|tool|weapon|n/a]"
            Optional: "[true|false]"
            Sort: 0
        SPELL: # Optional spell configuration for granted spells.
          Ability: [] # Spellcasting ability identifiers.
          Method: "[atwill|innate|ritual|pact|spell|n/a]"
          Prepared: "[unprepared|prepared|always]"
          Uses Max: "[formula|n/a]"
          Uses Per: "[lr|sr|day|dawn|dusk|recharge|initiative|turnStart|turnEnd|turn|n/a]"
          Require Slot: "[true|false]"
    - CHOOSE_ITEMS:
        Advancement ID: n/a
        Name: "[text|n/a]"
        Hint: "[text|n/a]"
        Icon: "[filepath|n/a]"
        Class Restriction: "[primary|secondary|n/a]"
        Allow Drops: "[true|false]"
        Sorting: "[a|m]"
        Type: "[feat|spell|consumable|container|equipment|loot|tool|weapon|n/a]"
        CHOICES:
          - Level: "[integer]"
            Count: "[integer]"
            Replacement: "[true|false]"
        POOL:
          - UUID: "[uuid|n/a]"
            Name: "[text|n/a]"
            Identifier: "[text|n/a]"
            Type: "[feat|spell|consumable|container|equipment|loot|tool|weapon|n/a]"
            Sort: 0
        RESTRICTION:
          Level: "[text|n/a]" # Spell level restriction such as available or 1.
          List: [] # Spell list identifiers such as class:wizard.
          School: [] # Spell school identifiers.
          Type: "[text|n/a]"
          Subtype: "[text|n/a]"
        SPELL:
          Ability: []
          Method: "[atwill|innate|ritual|pact|spell|n/a]"
          Prepared: "[unprepared|prepared|always]"
          Uses Max: "[formula|n/a]"
          Uses Per: "[lr|sr|day|dawn|dusk|recharge|initiative|turnStart|turnEnd|turn|n/a]"
          Require Slot: "[true|false]"
    - MODIFY_ITEMS:
        Advancement ID: n/a
        Level: 0
        Name: "[text|n/a]"
        Hint: "[text|n/a]"
        Icon: "[filepath|n/a]"
        Class Restriction: "[primary|secondary|n/a]"
        CHANGES:
          - Effect ID: n/a # An enchantment effect defined in this feature's effects list.
            EFFECT: # Or a standalone compendium ActiveEffect.
              UUID: "[uuid|n/a]"
              Name: "[text|n/a]"
            Identifiers: [] # Item identifiers the change applies to, e.g. ["weapon:longsword"].
    - SCALE_VALUE:
        Advancement ID: n/a
        Name: "[text|n/a]"
        Hint: "[text|n/a]"
        Icon: "[filepath|n/a]"
        Class Restriction: "[primary|secondary|n/a]"
        Identifier: "[text]" # The @scale key, e.g. storm-dice.
        Type: "[string|number|cr|dice|distance|usage]"
        Distance Units: "[ft|mi|m|km|n/a]" # distance type only.
        SCALE:
          - Level: "[integer]"
            Value: "[text|number|n/a]" # string, number, cr, distance and usage types.
            Dice Count: "[integer|null|n/a]" # dice type.
            Die Denomination: "[integer|n/a]" # dice type.
            Modifiers: [] # dice type, e.g. [min2].
            Period: "[lr|sr|day|dawn|dusk|recharge|initiative|turnStart|turnEnd|turn|n/a]" # usage type.
    - TRAIT:
        Advancement ID: n/a
        Level: 0
        Name: "[text|n/a]"
        Hint: "[text|n/a]"
        Icon: "[filepath|n/a]"
        Class Restriction: "[primary|secondary|n/a]"
        Mode: "[default|expertise|forcedExpertise|upgrade|mastery]"
        Allow Replacements: "[true|false]"
        Grants: [] # Trait keys such as skills:ath, weapon:mar, armor:hvy, languages:*.
        CHOICES:
          - Count: "[integer]"
            Pool: [] # Trait keys the player may choose from; empty = any.

  DESCRIPTION:
    Description: |
      [multiline HTML content containing Enrichers]

  CHAT_FLAVOR:
    Chat Description: |
      [multiline text content]

```

### FEAT: rules

This is a native Feature Item, not a physical inventory item: rarity, quantity, price, weight, attunement and unidentified-description fields are not feature authoring controls and are ignored with a warning. Feature Type and Feature Subtype are dnd5e identifiers, not display labels; unknown values are stored with a warning and the sheet shows them as a generic Feature. Advancement is authored through the `ADVANCEMENT` list: ability score improvements, granted items, item choices, item changes, scale values and traits. Referenced Items and effects are matched by UUID, name or identifier in the world and compendia; anything missing or ambiguous gets a drop zone in the import window, and import waits until every reference resolves. Enchantment limits matter only for Artificer infusion and Rune Knight rune sources, and Cover only for vehicle features; both are retained for any feature. Source-specific attacks, saves, damage, healing and effects require companion attachments in full output.

A feature is not a physical Item: it has no quantity, price, weight, rarity, attunement or unidentified text. Omit unsupplied prerequisites, enchantment limits and optional resources rather than inventing them. Formula Uses Max must be deterministic, one line and at most 200 characters, without dice or semicolons; recovery needs a positive literal maximum or a formula maximum.

### FEAT: field values

#### FEAT values: Feature Types
`Feature Type` stores the dnd5e `system.type.value` identifier.

| Value | Sheet label | Typical use |
|-------|-------------|-------------|
| `background` | Background Feature | Features granted by a background |
| `class` | Class Feature | Class and subclass features, invocations, metamagic, maneuvers |
| `monster` | Monster Feature | Stat-block traits, actions, legendary actions |
| `race` | Species Feature | Species and lineage traits |
| `enchantment` | Enchantment | Standalone enchantment sources |
| `feat` | Feat | Feats chosen at level-up |
| `pietyTrait` | Piety Trait | Mythic Odysseys of Theros piety traits |
| `supernaturalGift` | Supernatural Gift | Blessings, charms, epic boons granted as gifts |
| `vehicle` | Vehicle Feature | Vehicle actions and traits |
| `n/a` | Feature | No type selected |

#### FEAT values: Feature Subtypes
`Feature Subtype` stores `system.type.subtype`. Only these types define subtypes; a subtype on another type is stored without a label.

| Feature Type | Subtype values |
|--------------|----------------|
| `class` | `arcaneShot`, `artificerInfusion`, `channelDivinity`, `defensiveTactic`, `eldritchInvocation`, `elementalDiscipline`, `fightingStyle`, `huntersPrey`, `ki`, `maneuver`, `metamagic`, `multiattack`, `pact`, `psionicPower`, `rune`, `superiorHuntersDefense` |
| `enchantment` | `artificerInfusion`, `rune` |
| `feat` | `general`, `origin`, `fightingStyle`, `epicBoon` |
| `race` | `lineage` (dnd5e 6.1 or later; earlier versions store it without a label) |
| `supernaturalGift` | `blessing`, `charm`, `epicBoon` |

#### FEAT values: Requirements and Prerequisites
| Field | Description | Accepted values |
|---|---|---|
| `ITEM.Requirements` | Free text shown beneath the feature name, such as `Fighter 1`, `Level 4+` or `Proficiency with heavy armor`. dnd5e does not derive it from prerequisites. | Text or `n/a`. |
| `PREREQUISITES.Level` | Minimum character level checked by the feat picker. | Non-negative integer or `n/a` (null). |
| `PREREQUISITES.Items` | Items the actor must own, matched by dnd5e identifier. Prefix with an Item document type to narrow the match. | List of `identifier` or `type:identifier` strings, such as `[fighter, "feat:alert"]`. |
| `PREREQUISITES.Repeatable` | Whether the feat can be taken more than once. | `true` or `false`. |

#### FEAT values: Properties
| Property | dnd5e identifier | Effect |
|---|---|---|
| `Magical` | `mgc` | Marks damage from this feature's activities as magical. |
| `Passive Trait` | `trait` | Lists the feature under passive traits on character and NPC sheets. |

#### FEAT values: Enchantment Limits
| Field | Description | Accepted values |
|---|---|---|
| `ENCHANTMENT.Max` | Maximum number of active enchantments from this source. | Deterministic formula or `n/a`. |
| `ENCHANTMENT.Period` | When replaced enchantments can be swapped. | `sr` (Short Rest), `lr` (Long Rest), `atwill` (At Will) or `n/a` (Never). |

#### FEAT values: Vehicle Properties
| Field | Description | Accepted values |
|---|---|---|
| `VEHICLE_PROPERTIES.Cover` | Cover granted to crew by a vehicle feature. | `none`, `half`, `threequarters`, `total`, a number from 0 to 1, or `n/a`. |
| `VEHICLE_PROPERTIES.Crewed` | Stored native flag; dnd5e 6 has no sheet control for it. | `true` or `false`. |

#### FEAT values: Advancement
`ADVANCEMENT` is a list; each entry has exactly one root key. Entries share the common keys below and are stored under `system.advancement`, keyed by their ID.

| Common key | Description | Accepted values |
|---|---|---|
| `Advancement ID` | Stable 16-character ID; omit (`n/a`) to allocate one. | 16 alphanumeric characters or `n/a`. |
| `Level` | Character level at which the advancement applies; `0` means any level. Not used by `CHOOSE_ITEMS` and `SCALE_VALUE`, which take levels from their entries. | Non-negative integer. |
| `Name`, `Hint`, `Icon` | Optional display name, HTML hint and icon path. | Text, HTML, file path or `n/a`. |
| `Class Restriction` | Limits the advancement to a primary or secondary class. | `primary`, `secondary` or `n/a`. |

| Root | dnd5e type | Keys |
|---|---|---|
| `ABILITY_SCORE_IMPROVEMENT` | AbilityScoreImprovement | `Points` (free points), `Cap` (max increase per ability, default 2), `Max` (max resulting score), `Locked` (ability identifiers that cannot receive points), `FIXED` (list of `Ability`/`Value` increases), `RECOMMENDATION` (a feat reference for epic boons). |
| `GRANT_ITEMS` | ItemGrant | `Optional`, `Sorting` (`a` alphabetical or `m` manual), `ITEMS` (references with `Optional` and `Sort`), `SPELL` (spell configuration). |
| `CHOOSE_ITEMS` | ItemChoice | `Allow Drops`, `Sorting`, `Type` (Item type the player may choose), `CHOICES` (list of `Level`/`Count`/`Replacement`), `POOL` (references with `Sort`), `RESTRICTION` (`Level`, `List`, `School`, `Type`, `Subtype`), `SPELL`. |
| `MODIFY_ITEMS` | ModifyItem | `CHANGES`: each entry names an `Effect ID` (an enchantment effect in this feature's `effects` list) or an `EFFECT` reference to a standalone compendium ActiveEffect, plus the `Identifiers` of the Items it changes. |
| `SCALE_VALUE` | ScaleValue | `Identifier` (the `@scale.<feature>.<identifier>` key), `Type` (`string`, `number`, `cr`, `dice`, `distance`, `usage`), `Distance Units`, `SCALE` (list of `Level` entries: `Value` for most types; `Dice Count`, `Die Denomination`, `Modifiers` for dice; `Value` and `Period` for usage). |
| `TRAIT` | Trait | `Mode` (`default`, `expertise`, `forcedExpertise`, `upgrade`, `mastery`), `Allow Replacements`, `Grants` (trait keys), `CHOICES` (list of `Count`/`Pool`). |

**References** (`ITEMS`, `POOL`, `RECOMMENDATION`, `EFFECT`) accept `UUID`, `Name`, `Identifier` and, for Items, `Type`. A UUID is validated as written. Otherwise a single world or compendium document matching the name (or identifier) resolves automatically; several matches or none produce a drop zone in the import window, and a dropped document always overrides the automatic choice. Advancement UUIDs must point at world or compendium documents, not Items embedded in an Actor.

**Spell configuration** (`SPELL`): `Ability` lists spellcasting ability identifiers; `Method` is `atwill`, `innate`, `ritual`, `pact` or `spell`; `Prepared` is `unprepared`, `prepared` or `always`; `Uses Max`/`Uses Per`/`Require Slot` give the granted spells their own uses.

**Trait keys** use `<trait>:<category>[:<child>]`: traits are `saves`, `skills`, `languages`, `armor`, `weapon`, `tool`, `di`, `dr`, `dv`, `ci` and `dm`; `*` and `ALL` are wildcards. Categories use dnd5e identifiers (`weapon:sim`, `weapon:mar`, `armor:lgt`, `armor:med`, `armor:hvy`, `armor:shl`, `tool:art`); the parser warns when it sees the spelled-out names instead.

### FEAT: core example

A free/core class feature import. Preserve the complete description; it does not automatically create source-specific activities or effects.

5e Item Importer. Activity Importer is not required for this example.

```yaml
SCHEMA_VERSION: 7
FEAT:
  ITEM:
    Name: Second Wind
    Feature Type: class
    Feature Subtype: n/a
    Requirements: Fighter 1
  PREREQUISITES:
    Level: n/a
    Items: []
    Repeatable: false
  PROPERTIES:
    Magical: false
    Passive Trait: false
  USAGE:
    Uses Spent: 0
    Uses Max: 1
  RECOVERY:
    - Period: sr
      Type: recoverAll
  DESCRIPTION:
    Description: >
      <p>You have a limited well of stamina that you can draw on to protect yourself from harm.
      On your turn, you can use a bonus action to regain hit points equal to
      [[/heal 1d10 + @classes.fighter.levels]].</p>

      <p>Once you use this feature, you must finish a short or long rest before you can use it again.</p>
  CHAT_FLAVOR:
    Chat Description: |
      n/a
```

### FEAT: check before you answer

These checks are for `FEAT`. The checks for every Item type are in "Items: check before you answer (every type)".

1. A feature has no rarity, price, weight, quantity, attunement or unidentified text. `ITEM.Rarities`, `INVENTORY`, `COST_AND_WEIGHT`, `UNIDENTIFIED_DESCRIPTION` and `ATTUNEMENT` are ignored with a warning.
2. `ITEM.Feature Type` is a dnd5e id: `background`, `class`, `monster`, `race`, `enchantment`, `feat`, `pietyTrait`, `supernaturalGift` or `vehicle`. A species or lineage trait is `race`. `species`, `racial` or `Class Feature` is stored as an unknown type with a warning.
3. `ITEM.Feature Subtype` takes only the listed subtype ids, such as `fightingStyle`, `eldritchInvocation`, `metamagic`, `maneuver`, `channelDivinity`, `general`, `origin` and `epicBoon`. Do not put the class name there. Class and level go in `ITEM.Requirements`, for example `Fighter 2`.
4. "Level 4+" is two fields: the text in `ITEM.Requirements` and the number in `PREREQUISITES.Level`. `PREREQUISITES.Level: 4+` fails. `PREREQUISITES.Items` takes dnd5e identifiers such as `"feat:alert"`, not display names.
5. A passive feature has no `USAGE` or `RECOVERY`. For limited uses, `USAGE.Uses Max` is a whole number or a quoted formula without dice, such as `"@prof"` or `"max(1, @abilities.wis.mod)"`, and `USAGE.Uses Spent` is `0` for a fresh feature.
6. "Short or long rest" is one `RECOVERY` entry with `Period: sr`. "3/Day" is `Period: day`. "Recharge 5-6" is `Period: recharge` with `Formula: "5"`; `"5-6"` is dropped with a warning.
7. `ADVANCEMENT` is one list for the whole feature. Each entry has exactly one root key: `ABILITY_SCORE_IMPROVEMENT`, `GRANT_ITEMS`, `CHOOSE_ITEMS`, `MODIFY_ITEMS`, `SCALE_VALUE` or `TRAIT`. A mapping in place of the list fails.
8. Trait keys use dnd5e ids: `skills:ath`, not `skills:athletics` (the importer does not catch that one); `weapon:mar`; `armor:hvy`. A granted Item is a reference to an Item that already exists in the world or a compendium: give `Name` and `Type`, and do not make up a `UUID`. The import waits until every reference is resolved.
