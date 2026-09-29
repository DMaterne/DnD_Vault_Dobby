---
name: Lightfoot Halfling
type: species
source: PHB 2014
parent_species: Halfling
size: Small
speed: 25
bonuses:
- type: dex
  value: 2
- type: cha
  value: 1
languages:
- Common
- Halfling
effects:
- id: halfling_lucky
  type: reroll
  target: d20_natural_1
- id: brave
  type: advantage
  target: saving_throws_vs_frightened
- id: nimbleness
  type: movement_rule
  value: move_through_larger_creatures
- id: naturally_stealthy
  type: hide_rule
  value: hide_behind_larger_creature
notes: Halfling traits plus Naturally Stealthy.
---
