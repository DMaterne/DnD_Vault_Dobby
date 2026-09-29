# Artificer Spells

Generated files: 100

Basis: online Artificer spell list (2014 rules ecosystem), excluding entries explicitly marked UA.
The metadata fields follow the supplied Thunderclap.md schema.

Notes:
- `effect` is a concise paraphrase, not copied rulebook text.
- `save_fail_chance` is 0 because it depends on the target and should not be hard-coded.
- `resource_cost` is 1 for leveled spells and 0 for cantrips.
- Spell slot level itself is stored in `level`.
- Some campaign/source-specific spells are included because the current online Artificer list includes them:
  Air Bubble, Kinetic Jaunt, Vortex Warp, Ashardalon's Stride, Intellect Fortress,
  Summon Construct, Create Spelljamming Helm.
- Explicit UA entries were excluded: Arcane Weapon (UA), Flame Stride (UA), House of Cards (UA).

## Class tags

Every spell file now contains:

```yaml
classes:
- Artificer
```

`known_spells` and `prepared_spells` are character-specific and therefore belong in the character backend, not in the spell file.
