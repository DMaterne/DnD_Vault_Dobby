---
name: Rock Gnome
type: species
source: PHB 2014
parent_species: Gnome
size: Small
speed: 25
bonuses:
- type: int
  value: 2
- type: con
  value: 1
languages:
- Common
- Gnomish
proficiencies:
  tools:
  - tinkers_tools
effects:
- id: darkvision
  type: sense
  target: darkvision
  value: 60
- id: gnome_cunning
  type: advantage
  target: int_wis_cha_saves_vs_magic
- id: artificers_lore
  type: expertise_rule
  target: history_magic_alchemy_technology
- id: tinker
  type: crafting_rule
  value: clockwork_device
notes: Gnome traits plus Artificer's Lore and Tinker.
---
