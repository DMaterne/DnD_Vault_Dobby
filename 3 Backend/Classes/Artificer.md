---
name: Artificer
type: class

hit_die: d8
primary_ability: int
spellcasting_ability: int

# ============================================================
# SUBCLASS
# ============================================================

subclass:
  name: Artificer Specialist
  unlock_level: 3

  options:
    - name: Armorer
      file: Public/3 Backend/Subclasses/Armorer.md

    - name: Alchemist
      file: Public/3 Backend/Subclasses/Alchemist.md

    - name: Artillerist
      file: Public/3 Backend/Subclasses/Artillerist.md

    - name: Battle Smith
      file: Public/3 Backend/Subclasses/Battle Smith.md


# ============================================================
# CLASS FEATURES
# ============================================================

features:

  # LEVEL 1
  - level: 1
    feature: Public/3 Backend/Features/Magical Tinkering.md

  - level: 1
    feature: Public/3 Backend/Features/Spellcasting - Artificer.md

  # LEVEL 2
  - level: 2
    feature: Public/3 Backend/Features/Infuse Item.md

  # LEVEL 3
  - level: 3
    feature: Public/3 Backend/Features/Artificer Specialist.md

  - level: 3
    feature: Public/3 Backend/Features/The Right Tool for the Job.md

  # LEVEL 4
  - level: 4
    feature: Public/3 Backend/Features/Ability Score Improvement.md

  # LEVEL 6
  - level: 6
    feature: Public/3 Backend/Features/Tool Expertise.md

  # LEVEL 7
  - level: 7
    feature: Public/3 Backend/Features/Flash of Genius.md

  # LEVEL 8
  - level: 8
    feature: Public/3 Backend/Features/Ability Score Improvement.md

  # LEVEL 10
  - level: 10
    feature: Public/3 Backend/Features/Magic Item Adept.md

  # LEVEL 11
  - level: 11
    feature: Public/3 Backend/Features/Spell-Storing Item.md

  # LEVEL 12
  - level: 12
    feature: Public/3 Backend/Features/Ability Score Improvement.md

  # LEVEL 14
  - level: 14
    feature: Public/3 Backend/Features/Magic Item Savant.md

  # LEVEL 16
  - level: 16
    feature: Public/3 Backend/Features/Ability Score Improvement.md

  # LEVEL 18
  - level: 18
    feature: Public/3 Backend/Features/Magic Item Master.md

  # LEVEL 19
  - level: 19
    feature: Public/3 Backend/Features/Ability Score Improvement.md

  # LEVEL 20
  - level: 20
    feature: Public/3 Backend/Features/Soul of Artifice.md


# ============================================================
# LEVEL PROGRESSION
# ============================================================

levels:

  "1":
    proficiency_bonus: 2
    cantrips_known: 2
    infusions_known: 0
    infused_items: 0
    attunement_slots: 3
    spell_slots:
      "1": 2
      "2": 0
      "3": 0
      "4": 0
      "5": 0

  "2":
    proficiency_bonus: 2
    cantrips_known: 2
    infusions_known: 4
    infused_items: 2
    attunement_slots: 3
    spell_slots:
      "1": 2
      "2": 0
      "3": 0
      "4": 0
      "5": 0

  "3":
    proficiency_bonus: 2
    cantrips_known: 2
    infusions_known: 4
    infused_items: 2
    attunement_slots: 3
    spell_slots:
      "1": 3
      "2": 0
      "3": 0
      "4": 0
      "5": 0

  "4":
    proficiency_bonus: 2
    cantrips_known: 2
    infusions_known: 4
    infused_items: 2
    attunement_slots: 3
    spell_slots:
      "1": 3
      "2": 0
      "3": 0
      "4": 0
      "5": 0

  "5":
    proficiency_bonus: 3
    cantrips_known: 2
    infusions_known: 4
    infused_items: 2
    attunement_slots: 3
    spell_slots:
      "1": 4
      "2": 2
      "3": 0
      "4": 0
      "5": 0

  "6":
    proficiency_bonus: 3
    cantrips_known: 2
    infusions_known: 6
    infused_items: 3
    attunement_slots: 3
    spell_slots:
      "1": 4
      "2": 2
      "3": 0
      "4": 0
      "5": 0

  "7":
    proficiency_bonus: 3
    cantrips_known: 2
    infusions_known: 6
    infused_items: 3
    attunement_slots: 3
    spell_slots:
      "1": 4
      "2": 3
      "3": 0
      "4": 0
      "5": 0

  "8":
    proficiency_bonus: 3
    cantrips_known: 2
    infusions_known: 6
    infused_items: 3
    attunement_slots: 3
    spell_slots:
      "1": 4
      "2": 3
      "3": 0
      "4": 0
      "5": 0

  "9":
    proficiency_bonus: 4
    cantrips_known: 2
    infusions_known: 6
    infused_items: 3
    attunement_slots: 3
    spell_slots:
      "1": 4
      "2": 3
      "3": 2
      "4": 0
      "5": 0

  "10":
    proficiency_bonus: 4
    cantrips_known: 3
    infusions_known: 8
    infused_items: 4
    attunement_slots: 4
    spell_slots:
      "1": 4
      "2": 3
      "3": 2
      "4": 0
      "5": 0

  "11":
    proficiency_bonus: 4
    cantrips_known: 3
    infusions_known: 8
    infused_items: 4
    attunement_slots: 4
    spell_slots:
      "1": 4
      "2": 3
      "3": 3
      "4": 0
      "5": 0

  "12":
    proficiency_bonus: 4
    cantrips_known: 3
    infusions_known: 8
    infused_items: 4
    attunement_slots: 4
    spell_slots:
      "1": 4
      "2": 3
      "3": 3
      "4": 0
      "5": 0

  "13":
    proficiency_bonus: 5
    cantrips_known: 3
    infusions_known: 8
    infused_items: 4
    attunement_slots: 4
    spell_slots:
      "1": 4
      "2": 3
      "3": 3
      "4": 1
      "5": 0

  "14":
    proficiency_bonus: 5
    cantrips_known: 4
    infusions_known: 10
    infused_items: 5
    attunement_slots: 5
    spell_slots:
      "1": 4
      "2": 3
      "3": 3
      "4": 1
      "5": 0

  "15":
    proficiency_bonus: 5
    cantrips_known: 4
    infusions_known: 10
    infused_items: 5
    attunement_slots: 5
    spell_slots:
      "1": 4
      "2": 3
      "3": 3
      "4": 2
      "5": 0

  "16":
    proficiency_bonus: 5
    cantrips_known: 4
    infusions_known: 10
    infused_items: 5
    attunement_slots: 5
    spell_slots:
      "1": 4
      "2": 3
      "3": 3
      "4": 2
      "5": 0

  "17":
    proficiency_bonus: 6
    cantrips_known: 4
    infusions_known: 10
    infused_items: 5
    attunement_slots: 5
    spell_slots:
      "1": 4
      "2": 3
      "3": 3
      "4": 3
      "5": 1

  "18":
    proficiency_bonus: 6
    cantrips_known: 4
    infusions_known: 12
    infused_items: 6
    attunement_slots: 6
    spell_slots:
      "1": 4
      "2": 3
      "3": 3
      "4": 3
      "5": 1

  "19":
    proficiency_bonus: 6
    cantrips_known: 4
    infusions_known: 12
    infused_items: 6
    attunement_slots: 6
    spell_slots:
      "1": 4
      "2": 3
      "3": 3
      "4": 3
      "5": 2

  "20":
    proficiency_bonus: 6
    cantrips_known: 4
    infusions_known: 12
    infused_items: 6
    attunement_slots: 6
    spell_slots:
      "1": 4
      "2": 3
      "3": 3
      "4": 3
      "5": 2
---