---
name: Stout Halfling
type: species
source: PHB 2014
parent_species: Halfling
size: Small
speed: 25
bonuses:
- type: dex
  value: 2
- type: con
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
- id: stout_save
  type: advantage
  target: saving_throws_vs_poison
- id: stout_resistance
  type: resistance
  target: poison
notes: Halfling traits plus poison resilience.
---
