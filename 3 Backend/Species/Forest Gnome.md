---
name: Forest Gnome
type: species
source: PHB 2014
parent_species: Gnome
size: Small
speed: 25
bonuses:
- type: int
  value: 2
- type: dex
  value: 1
languages:
- Common
- Gnomish
effects:
- id: darkvision
  type: sense
  target: darkvision
  value: 60
- id: gnome_cunning
  type: advantage
  target: int_wis_cha_saves_vs_magic
- id: natural_illusionist
  type: innate_spellcasting
  value: minor_illusion
  ability: int
- id: small_beasts
  type: communication_rule
  value: simple_ideas_with_small_beasts
notes: Gnome traits plus illusion magic.
---
