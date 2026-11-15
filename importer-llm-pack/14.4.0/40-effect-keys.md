# Importer LLM Pack 14.4.0: effect keys

Pack 14.4.0 (2026-11-16) for 5e Item Importer 14.4.0 and 5e Activity Importer 14.4.0, on dnd5e 6.0.6 and 6.1.0 with Foundry VTT 14.370. The MIDI-QOL layer matches MIDI-QOL 14.6.11 and DAE 14.6.9 on dnd5e 6.0.6.

This file lists the effect keys of dnd5e 6 for the `CHANGES` of every effect: an `EFFECT` block, an `effects` entry or an `APPLIED_EFFECTS` entry. It is the same list as in 5e Activity Importer's Effect template.

## Effect keys: the Attribute Key of a change

These are the `Attribute Key` values for the `CHANGES` of an effect on a creature: an `EFFECT` block, an entry of an Item's `effects` list, or an entry of an activity's `APPLIED_EFFECTS` list. Every `system.*` and `flags.dnd5e.*` key here was checked against the actor data of dnd5e 6.1.0, and the same keys exist in dnd5e 6.0.6. Use these keys; do not build a key from memory, because many keys from before dnd5e 6 no longer exist (see "Effect keys: old keys that no longer work"). An enchantment (`Effect Type: enchantment`, in `ACTIVITY_ENCHANT`) changes an Item instead of a creature and uses Item keys, which this list does not cover.

## Effect keys: how to write one change

Each change is one `CHANGES` entry:

```yaml
EFFECT:
  DETAILS:
    Name: Ring of Protection
    Apply Effect to Actor: true
  CHANGES:
    - Attribute Key: system.attributes.ac.bonus
      Change Type: add
      Value: "1"
    - Attribute Key: system.rolls.ability.save.bonus
      Change Type: add
      Value: "1"
```

- **Bonus fields are formulas.** Write their `Value` as a quoted string: `"1"`, `"1d4"`, `"@prof"`, `"-10"`. With `Change Type: add`, dnd5e joins the formulas (`"2"` added to a bonus of `"1"` gives `1 + 2`), so several effects stack.
- **Lists of ids** (resistances, immunities, proficiencies, languages) take `Change Type: add` and one id per change: `Value: fire`. Two damage types are two changes.
- **Numbers** (proficiency levels, ability scores, sense ranges, critical thresholds) take a plain number: `Value: 1`, `Value: 60`. Use `upgrade` for "at least" and `override` for "becomes".
- **Roll modes** (`...mode` keys) take `Change Type: add` with `Value: 1` for advantage or `Value: -1` for disadvantage, or `override` to force it.
- Leave `Change Phase` and `Priority` out unless the user supplies them.
- A condition such as frightened or prone is not a change: write it in `DETAILS.Status Conditions`.

## Effect keys: placeholders

Replace a placeholder with one id before writing the key. Never write the braces.

| Placeholder | Ids |
| --- | --- |
| `{ability}` | `str`, `dex`, `con`, `int`, `wis`, `cha` |
| `{skill}` | `acr` Acrobatics, `ani` Animal Handling, `arc` Arcana, `ath` Athletics, `dec` Deception, `his` History, `ins` Insight, `itm` Intimidation, `inv` Investigation, `med` Medicine, `nat` Nature, `prc` Perception, `prf` Performance, `per` Persuasion, `rel` Religion, `slt` Sleight of Hand, `ste` Stealth, `sur` Survival |
| `{speed}` | `walk`, `burrow`, `climb`, `fly`, `swim` |
| `{sense}` | `blindsight`, `darkvision`, `tremorsense`, `truesight` |
| `{damageType}` | `acid`, `bludgeoning`, `cold`, `fire`, `force`, `lightning`, `necrotic`, `piercing`, `poison`, `psychic`, `radiant`, `slashing`, `thunder` |

## Effect keys: armor class

| The source says | Attribute Key | Change Type | Value |
| --- | --- | --- | --- |
| "+1 bonus to AC" | `system.attributes.ac.bonus` | `add` | `"1"` |
| "your AC can't be less than 16" | `system.attributes.ac.min` | `override` | `"16"` |
| "your AC equals 13 + your Dexterity modifier while you wear no armor" (a new way to work out AC) | `system.attributes.ac.formulas` | `add` | a mapping: `{label: Arcane Guard, formula: "13 + @abilities.dex.mod", armored: false, shielded: null}`; dnd5e uses the best AC the creature qualifies for |

A magic armor or shield bonus (+1 armor) is `ATTUNEMENT.Magic Bonus` on the Item, not an effect.

## Effect keys: attack and damage rolls

| The source says | Attribute Key | Change Type | Value |
| --- | --- | --- | --- |
| "+1 bonus to attack rolls" (every attack) | `system.rolls.attack.bonus` | `add` | `"1"` |
| "+1 bonus to melee weapon attack rolls" | `system.rolls.attack.mwak.bonus` | `add` | `"1"` |
| "+1 bonus to ranged weapon attack rolls" | `system.rolls.attack.rwak.bonus` | `add` | `"1"` |
| "+1 bonus to spell attack rolls" | `system.rolls.attack.msak.bonus` and `system.rolls.attack.rsak.bonus` (two changes) | `add` | `"1"` |
| "+2 to damage rolls with melee weapon attacks" | `system.rolls.damage.mwak.bonus` (also `rwak`, `msak`, `rsak`) | `add` | `"2"` |
| "an extra 1d4 on every damage roll" | `damage` | `dnd5e.bonus` | `"1d4"` |
| "+2 to the hit points you restore" | `healing` | `dnd5e.bonus` | `"2"` |
| "advantage on attack rolls" | `system.rolls.attack.mode` | `add` | `1` |
| "attack rolls have disadvantage" | `system.rolls.attack.mode` | `add` | `-1` |
| "your weapon attacks score a critical hit on a roll of 19 or 20" | `flags.dnd5e.weaponCriticalThreshold` | `override` | `19` |
| "your spell attacks score a critical hit on a roll of 19 or 20" | `flags.dnd5e.spellCriticalThreshold` | `override` | `19` |
| "roll one additional weapon damage die on a melee critical hit" | `flags.dnd5e.meleeCriticalDamageDice` | `add` | `1` |

## Effect keys: saving throws, ability checks and skills

| The source says | Attribute Key | Change Type | Value |
| --- | --- | --- | --- |
| "+1 bonus to saving throws" (every save) | `system.rolls.ability.save.bonus` | `add` | `"1"` |
| "+2 bonus to Dexterity saving throws" | `system.abilities.{ability}.save.roll.bonus` | `add` | `"2"` |
| "advantage on Wisdom saving throws" | `system.abilities.{ability}.save.roll.mode` | `add` | `1` |
| "advantage on all saving throws" | `system.rolls.ability.save.mode` | `add` | `1` |
| "proficiency in Constitution saving throws" | `system.abilities.{ability}.proficient` | `upgrade` | `1` |
| "+1 bonus to ability checks" (every check) | `system.rolls.ability.check.bonus` | `add` | `"1"` |
| "+2 bonus to Strength checks" | `system.abilities.{ability}.check.roll.bonus` | `add` | `"2"` |
| "advantage on Strength checks" | `system.abilities.{ability}.check.roll.mode` | `add` | `1` |
| "+1 bonus to skill checks" (every skill) | `system.rolls.ability.skill.bonus` | `add` | `"1"` |
| "+5 bonus to Wisdom (Perception) checks" | `system.skills.{skill}.roll.bonus` | `add` | `"5"` |
| "advantage on Dexterity (Stealth) checks" | `system.skills.{skill}.roll.mode` | `add` | `1` |
| "+5 to passive Wisdom (Perception)" | `system.skills.{skill}.bonuses.passive` | `add` | `"5"` |
| "proficiency in the Stealth skill" | `system.skills.{skill}.value` | `upgrade` | `1` (expertise: `2`) |
| "treat a d20 roll of 9 or lower as a 10 on Insight checks" | `system.skills.{skill}.roll.min` | `upgrade` | `10` |
| "+1 bonus to checks with tools" (every tool) | `system.rolls.ability.tool.bonus` | `add` | `"1"` |
| "you are at least proficient with tools" | `system.rolls.ability.tool.proficiency` | `upgrade` | `1` |
| "advantage on every d20 test" | `d20` | `dnd5e.advantage` | `"1"` |
| "advantage on ability checks" | `check` | `dnd5e.advantage` | `"1"` |
| "you can't have disadvantage on saving throws" | `save` | `dnd5e.advantage` | `">=0"` |

A `dnd5e.advantage` value is a quoted text: `"1"` adds advantage, `"-1"` adds disadvantage, `"=1"` or `"=-1"` forces one, `">=0"` blocks disadvantage and `"<=0"` blocks advantage. Its Attribute Key is `d20` (every d20 test), `attack`, `check` or `save`.

## Effect keys: initiative, concentration and death saves

| The source says | Attribute Key | Change Type | Value |
| --- | --- | --- | --- |
| "+5 bonus to initiative" | `system.attributes.init.roll.bonus` | `add` | `"5"` |
| "advantage on initiative rolls" | `system.attributes.init.roll.mode` | `add` | `1` |
| "+2 bonus to saving throws to maintain concentration" | `system.attributes.concentration.roll.bonus` | `add` | `"2"` |
| "advantage on saving throws to maintain concentration" | `system.attributes.concentration.roll.mode` | `add` | `1` |
| "+1 bonus to death saving throws" | `system.attributes.death.roll.bonus` | `add` | `"1"` |

## Effect keys: speed and senses

| The source says | Attribute Key | Change Type | Value |
| --- | --- | --- | --- |
| "your walking speed increases by 10 feet" | `system.attributes.movement.speeds.{speed}` | `add` | `"10"` |
| "your speed is reduced by 10 feet" (every speed the creature has) | `system.attributes.movement.bonus` | `add` | `"-10"` |
| "every speed you have increases by 5 feet" | `system.attributes.movement.bonus` | `add` | `"5"` |
| "you gain a flying speed of 60 feet" | `system.attributes.movement.speeds.{speed}` | `override` | `"60"` |
| "you can hover" | `system.attributes.movement.hover` | `override` | `true` |
| "you gain darkvision out to 60 feet" | `system.attributes.senses.ranges.{sense}` | `upgrade` | `60` |
| "the range of your darkvision increases by 30 feet" | `system.attributes.senses.ranges.{sense}` | `add` | `30` |

## Effect keys: hit points

| The source says | Attribute Key | Change Type | Value |
| --- | --- | --- | --- |
| "your hit point maximum increases by 10" (for the effect's duration) | `system.attributes.hp.tempmax` | `add` | `10` |
| "your hit point maximum increases by 1 for each of your levels" (a lasting player character feature) | `system.attributes.hp.bonuses.level` | `add` | `"1"` |
| "your hit point maximum increases by 5" (a lasting player character feature) | `system.attributes.hp.bonuses.overall` | `add` | `"5"` |

`system.attributes.hp.bonuses.*` exists on player characters only. Temporary hit points are healing, not an effect: write an `ACTIVITY_HEAL` with `Damage Types: [temphp]`.

## Effect keys: resistances, immunities and vulnerabilities

| The source says | Attribute Key | Change Type | Value |
| --- | --- | --- | --- |
| "resistance to fire damage" | `system.traits.dr.value` | `add` | `fire` |
| "immunity to poison damage" | `system.traits.di.value` | `add` | `poison` |
| "vulnerability to cold damage" | `system.traits.dv.value` | `add` | `cold` |
| "resistance to bludgeoning, piercing and slashing damage from nonmagical attacks" | `system.traits.dr.value` three times (one per type), plus `system.traits.dr.bypasses` | `add` | `bludgeoning`, `piercing`, `slashing`; and `mgc` for the bypass (magical attacks get through) |
| "immunity to the frightened condition" | `system.traits.ci.value` | `add` | `frightened` |
| "damage of that type you take is reduced by 3" | `system.traits.dm.amount.{damageType}` | `add` | `"-3"` |
| "your fire damage ignores resistance" (dnd5e 6.1 or later) | `damage` | `dnd5e.damage.ignore.resistance` | `fire` |

## Effect keys: proficiencies, languages and size

| The source says | Attribute Key | Change Type | Value |
| --- | --- | --- | --- |
| "proficiency with martial weapons" | `system.traits.weaponProf.value` | `add` | `mar` (simple weapons: `sim`) |
| "proficiency with heavy armor" | `system.traits.armorProf.value` | `add` | `hvy` (`lgt`, `med`, `shl` for shields) |
| "you can speak, read and write Draconic" | `system.traits.languages.value` | `add` | `draconic` |
| "your size becomes Large" | `system.traits.size` | `override` | `lg` (`tiny`, `sm`, `med`, `lg`, `huge`, `grg`) |

Weapon and armor proficiency keys exist on player characters only. Languages are lowercase ids: `common`, `dwarvish`, `elvish`, `giant`, `gnomish`, `goblin`, `halfling`, `orc`, `abyssal`, `celestial`, `deep`, `draconic`, `infernal`, `primordial`, `sylvan`, `undercommon` and the others dnd5e lists.

## Effect keys: spellcasting

| The source says | Attribute Key | Change Type | Value |
| --- | --- | --- | --- |
| "+1 bonus to your spell save DC" | `system.bonuses.spell.dc` | `add` | `"1"` |
| "+1 bonus to spell attack rolls" | `system.rolls.attack.msak.bonus` and `system.rolls.attack.rsak.bonus` | `add` | `"1"` |

## Effect keys: feature flags of dnd5e

These turn on a rule dnd5e already knows. Each takes `Change Type: override` and `Value: true`, unless the table gives a number.

| The source says | Attribute Key |
| --- | --- |
| Alert (add proficiency to initiative, as the feat) | `flags.dnd5e.initiativeAlert` |
| Diamond Soul (proficiency in all saving throws) | `flags.dnd5e.diamondSoul` |
| Elven Accuracy | `flags.dnd5e.elvenAccuracy` |
| Enhanced Dual Wielding | `flags.dnd5e.enhancedDualWielding` |
| Halfling Lucky (reroll a 1 on a d20) | `flags.dnd5e.halflingLucky` |
| Halfling Nimbleness | `flags.dnd5e.halflingNimbleness` |
| ignore the speed reduction of heavy armor | `flags.dnd5e.ignoreArmorSpeedReduction` |
| Jack of All Trades (half proficiency on checks without proficiency) | `flags.dnd5e.jackOfAllTrades` |
| Observant | `flags.dnd5e.observantFeat` |
| Powerful Build (count as one size larger for carrying) | `flags.dnd5e.powerfulBuild` |
| Reliable Talent | `flags.dnd5e.reliableTalent` |
| Remarkable Athlete | `flags.dnd5e.remarkableAthlete` |
| Tavern Brawler | `flags.dnd5e.tavernBrawlerFeat` |
| Tool Expertise | `flags.dnd5e.toolExpertise` |
| critical hit range of weapon attacks (a number, such as `19`) | `flags.dnd5e.weaponCriticalThreshold` |
| critical hit range of spell attacks (a number) | `flags.dnd5e.spellCriticalThreshold` |
| extra damage dice on a melee critical hit (a number, with `add`) | `flags.dnd5e.meleeCriticalDamageDice` |

## Effect keys: old keys that no longer work

Keys from dnd5e before version 6 are common in older guides and in chatbots' memory. dnd5e 6 redirects some of them with a warning and ignores others. Never write the old key; write the key on the right.

| Old key (do not write) | Write instead |
| --- | --- |
| system.bonuses.mwak.attack, system.bonuses.rwak.attack, system.bonuses.msak.attack, system.bonuses.rsak.attack | `system.rolls.attack.mwak.bonus` and the same for `rwak`, `msak`, `rsak` |
| system.bonuses.mwak.damage and the other damage bonuses | `system.rolls.damage.mwak.bonus` and the same for `rwak`, `msak`, `rsak` |
| system.bonuses.abilities.save, system.bonuses.abilities.check, system.bonuses.abilities.skill | `system.rolls.ability.save.bonus`, `system.rolls.ability.check.bonus`, `system.rolls.ability.skill.bonus` |
| system.abilities.dex.bonuses.save, system.abilities.str.bonuses.check | `system.abilities.dex.save.roll.bonus`, `system.abilities.str.check.roll.bonus` |
| system.skills.prc.bonuses.check | `system.skills.prc.roll.bonus` |
| system.attributes.init.bonus, flags.dnd5e.initiativeAdv | `system.attributes.init.roll.bonus`; `system.attributes.init.roll.mode` with `add` and `1` |
| system.attributes.movement.walk (and fly, swim, climb, burrow) | `system.attributes.movement.speeds.walk` (and the same for the others) |
| system.attributes.senses.darkvision (and the other senses) | `system.attributes.senses.ranges.darkvision` (and the same for the others) |
| system.attributes.concentration.bonuses.save, system.attributes.death.bonuses.save | `system.attributes.concentration.roll.bonus`, `system.attributes.death.roll.bonus` |
| system.bonuses.All-Attacks, system.bonuses.All-Damage, system.bonuses.weapon.attack, system.bonuses.spell.attack | `system.rolls.attack.bonus`; `damage` with `dnd5e.bonus`; `system.rolls.attack.mwak.bonus` and `rwak`; `msak` and `rsak` |
| flags.dnd5e.sharpShooter, flags.dnd5e.spellSniper, flags.dnd5e.initiativeHalfProficiency | no dnd5e key: keep the rule in the description |

## Effect keys: MIDI-QOL and DAE keys

`flags.midi-qol.*`, `flags.dae.*` and `macro.*` keys need MIDI-QOL or DAE and are listed in 30-midi-qol-and-dae.md. Write them only when the user has those modules active, and prefer a key from this file when one does the same job.
