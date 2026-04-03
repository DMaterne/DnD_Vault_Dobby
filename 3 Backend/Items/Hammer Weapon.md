---
name: Hammer
type: tool
weight: 3
equipment: false
notes: This one-handed hammer with an iron head is useful for pounding pitons into a wall.

actions:
  - name: Hammer
    category: action
    action_type: action
    mode: attack
    shape: melee_single
    range: 1
    targets: 1
    attacks: 2
    dice_count: 1
    dice_size: 6
    damage_bonus_stat: str
    damage_bonus: 0
    damage_type: bludgeoning
    ranged_attack: false
    friendly_fire: false
    requires_los: false
    utility_type:
    utility_value: 0
    utility_formula: 0
    resource_cost: 0
    resource_formula: "0"
    notes: Hammer Attack
---
# Hammer