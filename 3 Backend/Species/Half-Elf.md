---
name: Half-Elf
type: species
source: PHB 2014
size: Medium
speed: 30
bonuses:
- type: cha
  value: 2
languages:
- Common
- Elvish
choices:
  ability_increases:
    count: 2
    amount: 1
    distinct: true
    exclude:
    - cha
    options:
    - str
    - dex
    - con
    - int
    - wis
  skill_proficiencies:
    count: 2
    source: skills
  extra_language:
    count: 1
    source: languages
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
notes: Two chosen +1 abilities and two chosen skills.
---
