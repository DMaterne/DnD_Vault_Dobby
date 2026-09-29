---
name: High Elf
type: species
source: PHB 2014
parent_species: Elf
size: Medium
speed: 30
bonuses:
- type: dex
  value: 2
- type: int
  value: 1
proficiencies:
  skills:
  - perception
  weapons:
  - longsword
  - shortsword
  - shortbow
  - longbow
languages:
- Common
- Elvish
choices:
  extra_language:
    count: 1
    source: languages
  wizard_cantrip:
    count: 1
    source: wizard_cantrips
effects:
- id: darkvision
  type: sense
  target: darkvision
  value: 60
- id: fey_ancestry
  type: advantage
  target: saving_throws_vs_charmed
- id: fey_sleep
  type: immunity
  target: magical_sleep
- id: trance
  type: rest_rule
  value: 4_hour_trance
notes: Elf traits plus High Elf training and magic.
---
