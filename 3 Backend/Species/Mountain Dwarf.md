---
name: Mountain Dwarf
type: species
source: PHB 2014
parent_species: Dwarf
size: Medium
speed: 25
bonuses:
- type: con
  value: 2
- type: str
  value: 2
proficiencies:
  weapons:
  - battleaxe
  - handaxe
  - light_hammer
  - warhammer
  armor:
  - light
  - medium
languages:
- Common
- Dwarvish
choices:
  tool_proficiency:
    count: 1
    options:
    - smiths_tools
    - brewers_supplies
    - masons_tools
effects:
- id: darkvision
  type: sense
  target: darkvision
  value: 60
- id: dwarven_resilience
  type: advantage
  target: saving_throws_vs_poison
- id: dwarven_poison
  type: resistance
  target: poison
- id: stonecunning
  type: expertise_rule
  target: history_stonework
notes: Dwarf traits plus Strength and armor training.
---
