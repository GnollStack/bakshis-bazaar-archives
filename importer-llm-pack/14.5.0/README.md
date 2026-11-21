# Importer LLM Pack 14.5.0

Pack 14.5.0 (2026-11-19) for 5e Item Importer 14.5.0 and 5e Activity Importer 14.5.0, on dnd5e 6.0.6 and 6.1.1 with Foundry VTT 14.370. The MIDI-QOL layer matches MIDI-QOL 14.7.0 and DAE 14.6.10 on dnd5e 6.1.0.

The Importer LLM Pack teaches a chatbot to write Items for [5e Item Importer](https://github.com/GnollStack/5e-Item-Importer) and [5e Activity Importer](https://foundryvtt.com/packages/5e-activity-importer). Give the chatbot these files, describe an item or paste its rules text, and paste the YAML it returns into the import window in Foundry VTT.

It works with any chatbot that accepts files or long messages. The steps below cover Claude Projects and ChatGPT Projects.

## Download

- Every file in one zip: [importer-llm-pack-14.5.0.zip](importer-llm-pack-14.5.0.zip)
- Or download the files you need from this folder.

Use the pack that matches your modules. This one is for 5e Item Importer 14.5.0 and 5e Activity Importer 14.5.0. Each version keeps its own folder, and `latest` always holds the newest one.

## Which files you need

| You have | Upload |
| --- | --- |
| 5e Item Importer | `00-start-here.md`, `10-items.md`, `50-worked-examples.md` |
| 5e Item Importer and 5e Activity Importer | the files above, plus `20-activities-and-effects.md` and `40-effect-keys.md` |
| MIDI-QOL and DAE as well | all of the above, plus `30-midi-qol-and-dae.md` |

`project-instructions.txt` holds the text for the project's instructions.

| File | What it holds |
| --- | --- |
| `00-start-here.md` | How the chatbot chooses the kind of Item, puts it together and formats the answer |
| `10-items.md` | Every Item type: weapons, equipment, consumables, tools, loot, containers, spells and features |
| `20-activities-and-effects.md` | Activities (attacks, saving throws, healing, casting a spell and the rest) and Active Effects |
| `30-midi-qol-and-dae.md` | The MIDI-QOL and DAE settings, added on top of a complete Item |
| `40-effect-keys.md` | The keys for effect changes in dnd5e 6 |
| `50-worked-examples.md` | Complete examples, from rules text to finished YAML |

## Claude Projects

1. Create a project.
2. Open the project's instructions and paste the text of `project-instructions.txt`.
3. Add the pack files you need to the project's knowledge.
4. Start a chat in the project. Say which modules you have, then describe the item or paste its rules text.

## ChatGPT Projects

1. Create a project.
2. Open the project settings and paste the text of `project-instructions.txt` into the instructions.
3. Add the pack files you need to the project.
4. Start a chat in the project. Say which modules you have, then describe the item or paste its rules text.

## Other chatbots

- If the chatbot accepts files, attach the pack files you need and send the text of `project-instructions.txt` as your first message.
- If it only accepts text, send `project-instructions.txt`, then `00-start-here.md`, then the part of the other files you need, such as the section for one Item type from `10-items.md`.

## Using it

1. Tell the chatbot which modules you have, and whether MIDI-QOL is active in your world.
2. Describe the item, or paste its rules text.
3. Copy the YAML into the import window: 5e Item Importer for a whole Item, or 5e Activity Importer to add activities and effects to an Item you already have. Click **Preview**.
4. If the preview shows warnings or errors, paste them into the chat. The chatbot corrects the YAML.
5. When an item casts a spell, the import window links the spell by name, from your world or the dnd5e compendium for your rules. When several spells share the name, it asks you to pick one before you import.

The chatbot keeps the complete rules text in each Item's description. A rule the modules cannot store stays there as text, for you to handle at the table. MIDI-QOL gives the fullest automation, but every Item works without it.

Questions and feedback are welcome in the Bakshi’s Bazaar [Discord](https://discord.gg/tcdYk54fxC).
