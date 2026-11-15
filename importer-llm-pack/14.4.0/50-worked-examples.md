# Importer LLM Pack 14.4.0: worked examples

Pack 14.4.0 (2026-11-16) for 5e Item Importer 14.4.0 and 5e Activity Importer 14.4.0, on dnd5e 6.0.6 and 6.1.0 with Foundry VTT 14.370. The MIDI-QOL layer matches MIDI-QOL 14.6.11 and DAE 14.6.9 on dnd5e 6.0.6.

Each example goes from rules text to the finished YAML. The finished YAML of every example imports as written, and it was checked with the importers' own parsers. Use the examples to check the shape of an answer, not as content to copy: names, numbers and mechanics always come from the user's source.

Most examples are full Items, which need 5e Item Importer and 5e Activity Importer. The core Item for the same source is the same document without its `Activities` and `effects` lists, with the complete rules text kept in `DESCRIPTION.Description`. The last example adds the MIDI-QOL layer to an earlier one.

## Worked example 1: Frostbite Blade (WEAPON)

**Kind of answer:** full Item. **Needs:** 5e Item Importer and 5e Activity Importer.

### Frostbite Blade (WEAPON): rules text

> **Frostbite Blade.** *Weapon (longsword), rare (requires attunement)*
>
> You have a +1 bonus to attack and damage rolls made with this magic weapon. When you hit a creature with it, the target takes an extra 1d6 cold damage, and its speed is reduced by 10 feet until the start of your next turn.

### Frostbite Blade (WEAPON): how the rules become YAML

- "Weapon (longsword)" is `WEAPON` with `Weapon Type: martialM` and `Base Weapon: longsword`. The longsword itself supplies the 1d8 slashing damage in `DAMAGE.DAMAGE_DATA`, the versatile 1d10 in `VERSATILE_DAMAGE.DAMAGE_DATA` with `PROPERTIES.Versatile: true`, the 5-foot reach and the 3 lb weight. Nothing else of the longsword is stated, so nothing else is written.
- "rare (requires attunement)" is `ITEM.Rarities: [rare]`, `ATTUNEMENT.Attunement: required` and `PROPERTIES.Magical: true`.
- "+1 bonus to attack and damage rolls" is `ATTUNEMENT.Magic Bonus: 1` on the Item. It is not repeated in the attack: with `Include Base Damage: true` the attack already rolls the weapon's dice, ability modifier and magic bonus.
- "the target takes an extra 1d6 cold damage" when it hits is one `ACTIVITY_ATTACK` with one extra `DAMAGE_PARTS` entry of 1d6 cold.
- "its speed is reduced by 10 feet until the start of your next turn" is an `APPLIED_EFFECTS` entry in that attack: `Apply Effect to Actor: false`, a change of `system.attributes.movement.bonus` by `"-10"` (dnd5e adds it to every speed the creature has), and a duration of 1 round that ends at the start of the attacker's next turn (`Effect Expiry: sourceStart`).
- The attack takes its timing, range and target from the weapon, so it needs no `ACTIVATION`, `RANGE` or `TARGETS`.
- The full rules text stays in `DESCRIPTION.Description`, with a damage enricher for the extra die.

### Frostbite Blade (WEAPON): finished YAML

```yaml
SCHEMA_VERSION: 7
WEAPON:
  ITEM:
    Name: Frostbite Blade
    Rarities: [rare]
    Weapon Type: martialM
    Base Weapon: longsword
  INVENTORY:
    Quantity: 1
    Identified: true
  COST_AND_WEIGHT:
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
      Damage Types: [slashing]
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
      Damage Types: [slashing]
      Modifiers: []
      Custom Enabled: false
      Custom Formula: ""
      Scaling Mode: none
      Scaling Dice Count: 0
      Scaling Formula: ""
  DESCRIPTION:
    Description: |
      <p>You have a +1 bonus to attack and damage rolls made with this magic weapon.</p>
      <p>When you hit a creature with it, the target takes an extra [[/damage 1d6 cold average]] damage, and its speed is reduced by 10 feet until the start of your next turn.</p>
  Activities:
    - ACTIVITY_ATTACK:
        ACTIVITY:
          Name: Frostbite Strike
        ATTACK:
          Attack Type: melee
          Attack Class: weapon
        ATTACK_DAMAGE:
          Include Base Damage: true
          DAMAGE_PARTS:
            - DAMAGE_DATA:
                Dice Count: 1
                Die Denomination: 6
                Bonus: ""
                Damage Types: [cold]
                Custom Enabled: false
                Custom Formula: ""
                Scaling Mode: none
                Scaling Dice Count: 0
                Scaling Formula: ""
        APPLIED_EFFECTS:
          - DETAILS:
              Name: Frostbitten
              Apply Effect to Actor: false
            EFFECT_DESCRIPTION:
              Effect Description: <p>Speed reduced by 10 feet until the start of the attacker's next turn.</p>
            DURATION:
              Value: 1
              Units: rounds
              Effect Expiry: sourceStart
            CHANGES:
              - Attribute Key: system.attributes.movement.bonus
                Change Type: add
                Value: "-10"
```

### Frostbite Blade (WEAPON): after import

- Equip and attune the weapon. After a hit, the chat card offers the Frostbitten effect to apply to the target.

## Worked example 2: Wand of Sparks (EQUIPMENT)

**Kind of answer:** full Item. **Needs:** 5e Item Importer and 5e Activity Importer, and a Magic Missile spell Item in the world or a compendium.

### Wand of Sparks (EQUIPMENT): rules text

> **Wand of Sparks.** *Wand, uncommon (requires attunement by a spellcaster)*
>
> This wand has 7 charges. While holding it, you can take an action to expend 1 of its charges to cast *magic missile* (1st-level version) from it. The wand regains 1d6 + 1 expended charges daily at dawn. If you expend the wand's last charge, roll a d20. On a 1, the wand crumbles into ashes and is destroyed.

### Wand of Sparks (EQUIPMENT): how the rules become YAML

- A wand is `EQUIPMENT` with `Equipment Type: wand`, never a `SPELL` and never a `CONSUMABLE` in this pack.
- "7 charges" belong to the wand: `USAGE.Uses Max: 7` and `USAGE.Uses Spent: 0` on the Item. "regains 1d6 + 1 expended charges daily at dawn" is one Item `RECOVERY` entry with `Period: dawn`, `Type: formula` and `Formula: "1d6 + 1"`.
- "expend 1 of its charges to cast magic missile" is one `ACTIVITY_CAST` that spends the wand's charges: a `CONSUMPTION` entry with `Consumption Type: itemUses` and `Amount: "1"`, and no target item, so it spends the parent Item's uses.
- The spell is linked by name: `CASTING_DETAILS.Item Name: Magic Missile` with `Item UUID: n/a`, because the user gave no UUID. The preview shows a warning for it, and the import window asks the user to pick the spell before importing.
- "(1st-level version)" is `Spell Level: 1`. A spell cast from a magic item uses no spell slot and needs no components, so the block writes `BEHAVIOR.Consume Spell Slot: false` and `Ignored Properties: [vocal, somatic, material]`. Leaving `Ignored Properties` out stores an empty list, which means the components are required.
- "roll a d20; on a 1 it crumbles" has no field. It stays in the description for the GM.

### Wand of Sparks (EQUIPMENT): finished YAML

```yaml
SCHEMA_VERSION: 7
EQUIPMENT:
  ITEM:
    Name: Wand of Sparks
    Rarities: [uncommon]
    Equipment Type: wand
  INVENTORY:
    Quantity: 1
    Identified: true
  PROPERTIES:
    Magical: true
  ATTUNEMENT:
    Attunement: required
    Attunement By: a spellcaster
  USAGE:
    Uses Spent: 0
    Uses Max: 7
  RECOVERY:
    - Period: dawn
      Type: formula
      Formula: "1d6 + 1"
  DESCRIPTION:
    Description: |
      <p>This wand has 7 charges. While holding it, you can take an action to expend 1 of its charges to cast <em>magic missile</em> (1st-level version) from it.</p>
      <p>The wand regains 1d6 + 1 expended charges daily at dawn. If you expend the wand's last charge, roll a d20. On a 1, the wand crumbles into ashes and is destroyed.</p>
  Activities:
    - ACTIVITY_CAST:
        ACTIVITY:
          Name: Cast Magic Missile
        ACTIVATION:
          Override Activation: true
          Activation Type: action
        CONSUMPTION:
          - Consumption Type: itemUses
            Amount: "1"
        BEHAVIOR:
          Consume Spell Slot: false
        CASTING_DETAILS:
          Item Name: Magic Missile
          Item UUID: n/a
          Spell Level: 1
          Ignored Properties: [vocal, somatic, material]
```

### Wand of Sparks (EQUIPMENT): after import

- Resolve Magic Missile in the import window (search or drag and drop) before importing.

## Worked example 3: Draught of Mending (CONSUMABLE)

**Kind of answer:** full Item. **Needs:** 5e Item Importer and 5e Activity Importer.

### Draught of Mending (CONSUMABLE): rules text

> **Draught of Mending.** *Potion, common (50 gp, 1/2 lb.)*
>
> As a bonus action, you can drink this potion or give it to a creature within 5 feet of you. The creature who drinks it regains 2d4 + 2 hit points.

### Draught of Mending (CONSUMABLE): how the rules become YAML

- "Potion, common" is `CONSUMABLE` with `Consumable Type: potion`, `Rarities: [common]` and `PROPERTIES.Magical: true`. "50 gp, 1/2 lb." is `COST_AND_WEIGHT` with `Price Value: 50`, `Price Denomination: gp`, `Weight Value: 0.5` and `Weight Units: lb`.
- A potion is used once and then gone: `USAGE.Uses Max: 1`, `USAGE.Uses Spent: 0` and `USAGE.Destroy on Empty: true`. The number of potions is `INVENTORY.Quantity`, never `Uses Max`.
- "regains 2d4 + 2 hit points" is one `ACTIVITY_HEAL` with `HEALING.DAMAGE_DATA` of 2 dice of 4 sides, `Bonus: "2"` and `Damage Types: [healing]`.
- "As a bonus action" is `ACTIVATION.Activation Type: bonus`; "a creature within 5 feet of you" is a range of 5 feet and one creature as the target.
- Drinking spends the potion's one use: a `CONSUMPTION` entry with `Consumption Type: itemUses` and `Amount: "1"`. With `Destroy on Empty: true` the potion is removed when the last one is used.

### Draught of Mending (CONSUMABLE): finished YAML

```yaml
SCHEMA_VERSION: 7
CONSUMABLE:
  ITEM:
    Name: Draught of Mending
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
    Description: |
      <p>As a bonus action, you can drink this potion or give it to a creature within 5 feet of you. The creature who drinks it regains [[/heal 2d4 + 2]] hit points.</p>
  Activities:
    - ACTIVITY_HEAL:
        ACTIVITY:
          Name: Drink
        ACTIVATION:
          Override Activation: true
          Activation Type: bonus
        CONSUMPTION:
          - Consumption Type: itemUses
            Amount: "1"
        RANGE:
          Override Range: true
          Range Units: ft
          Range Value: 5
        TARGETS:
          Override Target: true
          Target Type: creature
          Target Amount: 1
        HEALING:
          DAMAGE_DATA:
            Dice Count: 2
            Die Denomination: 4
            Bonus: "2"
            Damage Types: [healing]
            Custom Enabled: false
            Custom Formula: ""
            Scaling Mode: none
            Scaling Dice Count: 0
            Scaling Formula: ""
```

## Worked example 4: Dread Howl (FEAT)

**Kind of answer:** full Item. **Needs:** 5e Item Importer and 5e Activity Importer.

### Dread Howl (FEAT): rules text

> **Dread Howl.** *3rd-level barbarian feature*
>
> As an action, you let out a howl. Each creature of your choice within 30 feet of you that can hear you must succeed on a Wisdom saving throw (DC 8 + your proficiency bonus + your Charisma modifier) or have the Frightened condition for 1 minute. A frightened creature repeats the saving throw at the end of each of its turns, ending the effect on itself on a success. Once you use this feature, you can't use it again until you finish a short or long rest.

### Dread Howl (FEAT): how the rules become YAML

- A class feature is `FEAT` with `Feature Type: class`; the class and level go in `ITEM.Requirements: Barbarian 3`.
- "Once you use this feature ... until you finish a short or long rest" is one use on the Item: `USAGE.Uses Max: 1`, `USAGE.Uses Spent: 0` and one `RECOVERY` entry with `Period: sr` (a short rest also covers a long rest). The activity spends it with `Consumption Type: itemUses`.
- "Wisdom saving throw" is `ACTIVITY_SAVE` with `SAVE_DETAILS.Challenge Ability: wis`. "DC 8 + your proficiency bonus + your Charisma modifier" is `DC Calculation: cha`; dnd5e works the number out.
- "Each creature ... within 30 feet of you" is an emanation around the user: `RANGE.Range Units: self` and `TARGETS` with `Template Type: radius`, `Template Measurement: 30` and `Template Unit: ft`.
- The save deals no damage, so `SAVE_DAMAGE.Damage on Save: none` and no `DAMAGE_PARTS`.
- "have the Frightened condition for 1 minute" on a failed save is an `APPLIED_EFFECTS` entry with `Status Conditions: [frightened]`, a duration of 1 minute and `APPLICATION.On Save: false` (failed saves only).
- "of your choice" and the repeated save at the end of each turn have no Base field. They stay in the description; the MIDI-QOL layer in the last worked example adds the repeated save.

### Dread Howl (FEAT): finished YAML

```yaml
SCHEMA_VERSION: 7
FEAT:
  ITEM:
    Name: Dread Howl
    Feature Type: class
    Requirements: Barbarian 3
  USAGE:
    Uses Spent: 0
    Uses Max: 1
  RECOVERY:
    - Period: sr
      Type: recoverAll
  DESCRIPTION:
    Description: |
      <p>As an action, you let out a howl. Each creature of your choice within 30 feet of you that can hear you must succeed on a [[/save wis dc=@abilities.cha.dc format=long]] or have the &Reference[frightened] condition for 1 minute. A frightened creature repeats the saving throw at the end of each of its turns, ending the effect on itself on a success.</p>
      <p>Once you use this feature, you can't use it again until you finish a short or long rest.</p>
  Activities:
    - ACTIVITY_SAVE:
        ACTIVITY:
          Name: Dread Howl
        ACTIVATION:
          Override Activation: true
          Activation Type: action
        DURATION:
          Override Duration: true
          Duration Time: minute
          Duration Amount: 1
          Concentration: false
        CONSUMPTION:
          - Consumption Type: itemUses
            Amount: "1"
        RANGE:
          Override Range: true
          Range Units: self
        TARGETS:
          Override Target: true
          Target Type: creature
          Template Type: radius
          Template Measurement: 30
          Template Unit: ft
        SAVE_DETAILS:
          Challenge Ability: wis
          DC Calculation: cha
        SAVE_DAMAGE:
          Damage on Save: none
        APPLIED_EFFECTS:
          - DETAILS:
              Name: Frightened by Dread Howl
              Apply Effect to Actor: false
              Status Conditions: [frightened]
            DURATION:
              Value: 1
              Units: minutes
            APPLICATION:
              On Save: false
```

### Dread Howl (FEAT): after import

- Without MIDI-QOL, the GM chooses the targets and ends the effect when a repeated save succeeds.

## Worked example 5: Emberward Chain Mail (EQUIPMENT)

**Kind of answer:** full Item. **Needs:** 5e Item Importer and 5e Activity Importer (the passive effect is an attachment).

### Emberward Chain Mail (EQUIPMENT): rules text

> **Emberward Chain Mail.** *Armor (chain mail), rare (requires attunement)*
>
> You have a +1 bonus to AC while wearing this armor. While you wear this armor and are attuned to it, you have resistance to fire damage.

### Emberward Chain Mail (EQUIPMENT): how the rules become YAML

- "Armor (chain mail)" is `EQUIPMENT` with `Equipment Type: heavy` and `Base Equipment: chainmail`. Chain mail itself supplies `ARMOR.Armor Class: 16`, `Max Dex Modifier: 0`, `Strength Requirement: 13`, `PROPERTIES.Stealth Disadvantage: true` and 55 lb.
- "+1 bonus to AC while wearing this armor" is `ATTUNEMENT.Magic Bonus: 1`. `Armor Class` stays 16; it is never written as 17.
- "resistance to fire damage" while worn is a passive effect in the Item's `effects` list: `Apply Effect to Actor: true` and one change of `system.traits.dr.value` with `Change Type: add` and `Value: fire`. dnd5e applies it only while the armor is equipped and attuned, because the armor requires attunement.
- The armor has nothing to activate, so it has no `Activities` list.

### Emberward Chain Mail (EQUIPMENT): finished YAML

```yaml
SCHEMA_VERSION: 7
EQUIPMENT:
  ITEM:
    Name: Emberward Chain Mail
    Rarities: [rare]
    Equipment Type: heavy
    Base Equipment: chainmail
  INVENTORY:
    Quantity: 1
    Identified: true
  COST_AND_WEIGHT:
    Weight Value: 55
    Weight Units: lb
  PROPERTIES:
    Magical: true
    Stealth Disadvantage: true
  ATTUNEMENT:
    Attunement: required
    Magic Bonus: 1
  ARMOR:
    Armor Class: 16
    Max Dex Modifier: 0
    Strength Requirement: 13
  DESCRIPTION:
    Description: |
      <p>You have a +1 bonus to AC while wearing this armor.</p>
      <p>While you wear this armor and are attuned to it, you have resistance to fire damage.</p>
  effects:
    - DETAILS:
        Name: Emberward
        Apply Effect to Actor: true
      CHANGES:
        - Attribute Key: system.traits.dr.value
          Change Type: add
          Value: fire
```

## Worked example 6: Cinder Burst (SPELL)

**Kind of answer:** full Item. **Needs:** 5e Item Importer and 5e Activity Importer.

### Cinder Burst (SPELL): rules text

> **Cinder Burst.** *Level 2 Evocation*
>
> **Casting Time:** Action · **Range:** 90 feet · **Components:** V, S, M (a pinch of ash) · **Duration:** Instantaneous
>
> A burst of embers erupts at a point you choose within range. Each creature in a 15-foot-radius sphere centered on that point makes a Dexterity saving throw, taking 4d6 fire damage on a failed save or half as much damage on a successful one.
>
> **Using a Higher-Level Spell Slot.** The damage increases by 1d6 for each spell slot level above 2.

### Cinder Burst (SPELL): how the rules become YAML

- The spell line becomes `ITEM.Level: 2` and `ITEM.School: evo`; casting time, range, components and duration go in the spell's own `ACTIVATION`, `RANGE`, `COMPONENTS`, `MATERIALS` and `DURATION`. "Each creature in a 15-foot-radius sphere" is `TARGETS.Type: creature` and `AREA` with `Shape: sphere`, `Size: 15` and `Units: ft`.
- A spell Item holds the activities the spell performs, never an `ACTIVITY_CAST`. Here that is one `ACTIVITY_SAVE`: `Challenge Ability: dex` and `DC Calculation: spellcasting` (the caster's spell save DC).
- "4d6 fire damage on a failed save or half as much on a successful one" is `SAVE_DAMAGE.Damage on Save: half` with one `DAMAGE_PARTS` entry of 4 dice of 6 sides, `Damage Types: [fire]`.
- "increases by 1d6 for each spell slot level above 2" is `Scaling Mode: whole` and `Scaling Dice Count: 1` in that damage part. On a spell Item dnd5e scales it by the slot used.
- The activity leaves out `ACTIVATION`, `RANGE`, `TARGETS` and `DURATION`, so it uses the spell's own casting time, range, area and duration.

### Cinder Burst (SPELL): finished YAML

```yaml
SCHEMA_VERSION: 7
SPELL:
  ITEM:
    Name: Cinder Burst
    Level: 2
    School: evo
  COMPONENTS:
    Vocal: true
    Somatic: true
    Material: true
    Ritual: false
  MATERIALS:
    Value: "a pinch of ash"
    Consumed: false
  ACTIVATION:
    Type: action
    Value: 1
  RANGE:
    Units: ft
    Value: 90
  DURATION:
    Units: inst
  TARGETS:
    Type: creature
  AREA:
    Shape: sphere
    Size: 15
    Units: ft
  DESCRIPTION:
    Description: |
      <p>A burst of embers erupts at a point you choose within range. Each creature in a 15-foot-radius sphere centered on that point makes a [[/save dex format=long]], taking [[/damage 4d6 fire average]] damage on a failed save or half as much damage on a successful one.</p>
      <p><strong>Using a Higher-Level Spell Slot.</strong> The damage increases by 1d6 for each spell slot level above 2.</p>
  Activities:
    - ACTIVITY_SAVE:
        ACTIVITY:
          Name: Cinder Burst
        SAVE_DETAILS:
          Challenge Ability: dex
          DC Calculation: spellcasting
        SAVE_DAMAGE:
          Damage on Save: half
          DAMAGE_PARTS:
            - DAMAGE_DATA:
                Dice Count: 4
                Die Denomination: 6
                Bonus: ""
                Damage Types: [fire]
                Custom Enabled: false
                Custom Formula: ""
                Scaling Mode: whole
                Scaling Dice Count: 1
                Scaling Formula: ""
```

## Worked example 7: Dread Howl with MIDI-QOL (FEAT)

**Kind of answer:** full Item with the MIDI-QOL layer. **Needs:** 5e Item Importer, 5e Activity Importer, and MIDI-QOL active in the world (MIDI-QOL runs on dnd5e 6.0).

### Dread Howl with MIDI-QOL (FEAT): rules text

> The same Dread Howl as worked example 4, for a world where MIDI-QOL is active.

### Dread Howl with MIDI-QOL (FEAT): how the rules become YAML

- Start from the complete Base answer of worked example 4. Every Base section stays as it was.
- "A frightened creature repeats the saving throw at the end of each of its turns, ending the effect on itself on a success" is the over-time setting of the Save: `AUTOMATION.midi-qol.fields.isOverTimeFlag: true`, `overTimeProperties.turnChoice: end` and `overTimeProperties.saveRemoves: true`, with `midiProperties.identifier: dread-howl` so the effect can name the activity.
- MIDI-QOL repeats the save only for creatures that carry an effect with a `flags.midi-qol.ActivityOverTime` change whose value is that identifier. The applied Frightened effect therefore gets one change: `Attribute Key: flags.midi-qol.ActivityOverTime`, `Change Type: override`, `Value: dread-howl`.
- Nothing in the source calls for other MIDI-QOL settings, so the answer writes no others.

### Dread Howl with MIDI-QOL (FEAT): finished YAML

```yaml
SCHEMA_VERSION: 7
FEAT:
  ITEM:
    Name: Dread Howl
    Feature Type: class
    Requirements: Barbarian 3
  USAGE:
    Uses Spent: 0
    Uses Max: 1
  RECOVERY:
    - Period: sr
      Type: recoverAll
  DESCRIPTION:
    Description: |
      <p>As an action, you let out a howl. Each creature of your choice within 30 feet of you that can hear you must succeed on a [[/save wis dc=@abilities.cha.dc format=long]] or have the &Reference[frightened] condition for 1 minute. A frightened creature repeats the saving throw at the end of each of its turns, ending the effect on itself on a success.</p>
      <p>Once you use this feature, you can't use it again until you finish a short or long rest.</p>
  Activities:
    - ACTIVITY_SAVE:
        ACTIVITY:
          Name: Dread Howl
        ACTIVATION:
          Override Activation: true
          Activation Type: action
        DURATION:
          Override Duration: true
          Duration Time: minute
          Duration Amount: 1
          Concentration: false
        CONSUMPTION:
          - Consumption Type: itemUses
            Amount: "1"
        RANGE:
          Override Range: true
          Range Units: self
        TARGETS:
          Override Target: true
          Target Type: creature
          Template Type: radius
          Template Measurement: 30
          Template Unit: ft
        SAVE_DETAILS:
          Challenge Ability: wis
          DC Calculation: cha
        SAVE_DAMAGE:
          Damage on Save: none
        APPLIED_EFFECTS:
          - DETAILS:
              Name: Frightened by Dread Howl
              Apply Effect to Actor: false
              Status Conditions: [frightened]
            DURATION:
              Value: 1
              Units: minutes
            APPLICATION:
              On Save: false
            CHANGES:
              - Attribute Key: flags.midi-qol.ActivityOverTime
                Change Type: override
                Value: dread-howl
        AUTOMATION:
          midi-qol:
            fields:
              isOverTimeFlag: true
              overTimeProperties:
                turnChoice: end
                saveRemoves: true
              midiProperties:
                identifier: dread-howl
```

### Dread Howl with MIDI-QOL (FEAT): after import

- The settings are stored on import. Whether the save repeats in play depends on MIDI-QOL's workflow settings in the world.
