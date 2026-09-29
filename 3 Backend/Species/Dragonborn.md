---
name: Dragonborn
type: species
source: PHB 2014
size: Medium
speed: 30
bonuses:
- type: str
  value: 2
- type: cha
  value: 1
languages:
- Common
- Draconic
choices:
  draconic_ancestry:
    count: 1
    options:
    - black
    - blue
    - brass
    - bronze
    - copper
    - gold
    - green
    - red
    - silver
    - white
resources:
  id: breath_weapon
  label: Breath Weapon
  max: 1
  recharge: short_rest
  display: checkboxes
actions:
- name: Breath Weapon
  activation: action
  resource: breath_weapon
  effect: Area, save and damage type depend on ancestry; damage scales with level.
effects:
- id: draconic_resistance
  type: resistance
  target: ancestry_damage_type
notes: Ancestry controls breath weapon and resistance.
---
