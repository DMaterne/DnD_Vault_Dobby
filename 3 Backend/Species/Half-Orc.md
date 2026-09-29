---
name: Half-Orc
type: species
source: PHB 2014
size: Medium
speed: 30
bonuses:
- type: str
  value: 2
- type: con
  value: 1
proficiencies:
  skills:
  - intimidation
languages:
- Common
- Orc
resources:
  id: relentless_endurance
  label: Relentless Endurance
  max: 1
  recharge: long_rest
  display: checkboxes
effects:
- id: darkvision
  type: sense
  target: darkvision
  value: 60
- id: relentless_endurance
  type: death_prevention
  value: drop_to_1_hp_instead
  resource: relentless_endurance
- id: savage_attacks
  type: critical_damage_rule
  value: extra_weapon_damage_die
notes: Includes Menacing, Relentless Endurance and Savage Attacks.
---
