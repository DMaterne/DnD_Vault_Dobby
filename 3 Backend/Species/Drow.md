---
name: Drow
type: species
source: PHB 2014
parent_species: Elf
size: Medium
speed: 30
bonuses:
- type: dex
  value: 2
- type: cha
  value: 1
proficiencies:
  skills:
  - perception
  weapons:
  - rapier
  - shortsword
  - hand_crossbow
languages:
- Common
- Elvish
effects:
- id: darkvision
  type: sense
  target: darkvision
  value: 120
- id: fey_ancestry
  type: advantage
  target: saving_throws_vs_charmed
- id: fey_sleep
  type: immunity
  target: magical_sleep
- id: sunlight_sensitivity
  type: disadvantage
  target: attacks_and_sight_perception
  condition: direct sunlight
- id: drow_magic
  type: innate_spellcasting
  value: dancing_lights; faerie_fire level 3; darkness level 5
  ability: cha
notes: Elf traits plus Drow magic, superior darkvision and sunlight sensitivity.
---
