# Mob-Config
Guide on how the mod config works
 
No mandatory commands.  
No changes to vanilla world generation.

Declarative, deterministic, and fully composable configuration.

---

## What is MobConfig?

MobConfig intercepts entities **after they already exist** and applies rules to them.

It does NOT:
- Replace spawns
- Force mobs
- Modify chunk generation

It DOES:
- Modify attributes
- Apply potion effects
- Add equipment
- Introduce progression by time, day, biome, or context

Compatible with:
- Existing worlds
- Other mods
- Servers (server-side)

---

## Mod Scope

- **World-scoped**  
  Each world has its own configuration.

Path:
```text
/world/mobconfig/

Files are generated automatically

Changes can be applied without restarting

/mobconfig reload

Core Concepts (read before touching anything)
range ≠ variance

range
Value is chosen once per mob

variance
Value is randomized every time it is evaluated

They are not interchangeable.
They are never implicitly combined.

.chance is universal

Anything can have .chance.

If it fails:

The mob remains vanilla

This is intentional.

Stackable contexts

Day

Time

Biome

Dimension

Spawn radius

Persistent aura

They do not override each other.
They are evaluated and stack.

1. Basic attributes
<mob>.health=<value>
<mob>.damage=<value>
<mob>.speed=<multiplier>
<mob>.follow_range=<blocks>


Example:

zombie.health=40
zombie.damage=8
zombie.speed=1.5
zombie.follow_range=32

2. Ranges and Variance
Fixed range
zombie.health=40-60
zombie.health.chance=0.5


The value is chosen once per entity.

Variance
zombie.damage.variance=5-15
zombie.damage.variance.chance=0.3


The value is recalculated each time.

3. Potion effects
<mob>.effect.<effect>=true
<mob>.effect.<effect>.level=<level>
<mob>.effect.<effect>.time=<duration>
<mob>.effect.<effect>.chance=<0.0-1.0>


Valid durations:

100s

5m

1h

6000 (ticks)

Example:

zombie.effect.speed=true
zombie.effect.speed.level=2
zombie.effect.speed.time=300s
zombie.effect.speed.chance=0.5

4. Armor
<mob>.armor.<piece>=true
<mob>.armor.<piece>.chance=<0.0-1.0>


Enchantments:

<mob>.armor.add.enchantment.<enchantment>=true
<mob>.armor.add.enchantment.<enchantment>.level=<level>
<mob>.armor.add.enchantment.<enchantment>.chance=<0.0-1.0>

5. Items and weapons
<mob>.item.<item>=true
<mob>.item.<item>.chance=<0.0-1.0>
<mob>.item.<item>.durability=<value | min-max>


Durability is absolute, not percentage-based.

6. Spawn Radius (one-time event)

Executed when the mob spawns.

<mob>.spawn.detection=true
<mob>.spawn.radius=<blocks>
<mob>.spawn.height=<blocks>


Example:

skeleton.spawn.radius.add.all.zombie.effect.strength=true
skeleton.spawn.radius.add.all.zombie.effect.strength.level=2
skeleton.spawn.radius.add.all.zombie.effect.strength.time=60s

7. Persistent Aura

Continuous effect while other mobs remain inside the radius.

<mob>.spawn.radius.persistent=<blocks>
<mob>.spawn.radius.persistent.chance=<0.0-1.0>

8. Day-based rules
set.number.day=<day>
set.number.day.chance=<0.0-1.0> / <id>
set.number.day.<mob>.<attr>=<value> / <id>


Example:

set.number.day=5
set.number.day.chance=0.5 / d5
set.number.day.zombie.health=60-100 / d5


The /id prevents rule collisions.

9. Time-based rules
set.time=<duration>
set.time.<mob>.<attr>=<value>


Example:

set.time=10000s
set.time.zombie.follow_range=40-70

Day + time
set.time.number.day=<day> / <duration>

10. Biomes and dimensions
set.desert.zombie.speed=1.8
set.nether.zombie.damage=15


Can be combined with:

Day

Time

Chance

Commands
/mobconfig reload
/mobconfig status
/mobconfig profile save <name>
/mobconfig profile load <name>
/mobconfig profile list

Final example (Vanilla+)
zombie.health=30-45
zombie.health.chance=0.4

set.number.day=5
set.number.day.zombie.damage=7-10 / d5

set.desert.zombie.speed=1.4


Result:

Vanilla intact

Controlled variation

Gradual progression
