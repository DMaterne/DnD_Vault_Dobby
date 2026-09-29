---
name: Warforged
type: species
source: 'Eberron: Rising from the Last War (2019)'
size: Medium
speed: 30
bonuses:
- type: con
  value: 2
languages:
- Common
choices:
  ability_increase:
    amount: 1
    options:
    - str
    - dex
    - int
    - wis
    - cha
  skill_proficiency:
    count: 1
    source: skills
  tool_proficiency:
    count: 1
    source: tools
  extra_language:
    count: 1
    source: languages
effects:
- id: poison_save
  type: advantage
  target: saving_throws_vs_poison
- id: poison_resistance
  type: resistance
  target: poison
- id: disease
  type: immunity
  target: disease
- id: needs
  type: immunity
  target: eat_drink_breathe
- id: sentrys_rest
  type: rest_rule
  value: 6_hours_inert_but_conscious
- id: integrated_protection
  type: ac_bonus
  value: 1
- id: integrated_armor
  type: armor_rule
  value: armor_integrates_into_body
notes: Additional Eberron option for EIMER; not PHB 2014.
---
