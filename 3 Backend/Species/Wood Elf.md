---
name: Wood Elf
type: species
source: PHB 2014
parent_species: Elf
size: Medium
speed: 35
bonuses:
- type: dex
  value: 2
- type: wis
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
- id: mask_wild
  type: hide_rule
  value: natural_light_obscurement
notes: Elf traits plus speed and Mask of the Wild.
---
