# Inform 7 Integration for Adventure Construction Set

## Overview

ACS now includes **Inform 7-inspired natural language understanding** capabilities! This integration brings the power of Inform 7's flexible, English-like command parsing to the Adventure Construction Set.

## What is Inform 7?

[Inform 7](https://github.com/ganelson/inform) is a revolutionary programming language for creating interactive fiction, created by Graham Nelson. It uses natural language syntax and is one of the most influential IF development systems.

## Features Integrated

### 1. Natural Language Parser (`natural_language.py`)

The `InformStyleParser` understands commands in natural English:

```python
from acs.core.natural_language import InformStyleParser

parser = InformStyleParser()

# Understands varied phrasings
result = parser.parse("take the red sword")
# -> verb='take', direct_object='sword', adjectives=['red']

result = parser.parse("put sword in bag")
# -> verb='put', direct_object='sword', indirect_object='bag', preposition='in'

result = parser.parse("x statue")  # shorthand for examine
# -> verb='examine', direct_object='statue'
```

**Supported Grammar Patterns:**
- `VERB NOUN` - "take sword"
- `VERB ADJECTIVE NOUN` - "take red sword"
- `VERB NOUN PREP NOUN` - "put sword in bag"
- `VERB NOUN NOUN` - "give guard key"
- `VERB ALONE` - "inventory", "wait"

**Built-in Synonyms:**
- examine: x, look, inspect, check, read, study
- take: get, grab, pick, acquire, obtain
- attack: hit, fight, kill, strike, punch, stab
- go: walk, run, move, travel, head
- ...and many more!

### 2. Teach New Commands

Like Inform 7's "Understand" syntax:

```python
# Teach parser new synonyms
parser.understand_as("steal", "take")
parser.understand_as("smash", "attack")
parser.understand_as("n", "north")

# Now these work:
parser.parse("steal key")  # -> verb='take'
parser.parse("smash door")  # -> verb='attack'
```

### 3. World Model (`InformStyleWorld`)

Track object properties and relationships like Inform 7:

```python
from acs.core.natural_language import InformStyleWorld

world = InformStyleWorld()

# Define object kinds (like Inform 7 kinds)
world.set_kind("sword", "weapon")
world.set_kind("chest", "container")

# Set properties
world.set_property("sword", "sharp", True)
world.set_property("door", "locked", False)

# Define relations
world.relate("contains", "chest", "key")
world.relate("wears", "guard", "helmet")

# Query the world
items_in_chest = world.query_relation("contains", "chest")
# -> ['key']

is_sharp = world.get_property("sword", "sharp")
# -> True
```

### 4. Engine Integration

Use the enhanced engine with Inform 7 capabilities:

```python
from acs.core.engine import GameEngine
from acs.core.inform_integration import create_inform_enhanced_engine

# Create enhanced engine class
EnhancedEngine = create_inform_enhanced_engine(GameEngine)

# Use it like normal GameEngine but with NL understanding
game = EnhancedEngine()

# Teach custom commands
game.teach_command("steal", "take")
game.teach_command("smash", "attack")

# Set up world model
game.set_object_kind("sword", "weapon")
game.set_object_property("door", "locked", True)
game.relate_objects("contains", "chest", "key")

# Process commands - works with natural language!
game.process_command("steal the golden key")
game.process_command("examine ancient statue")
game.process_command("smash orc with sword")
```

## Command Examples

The parser understands many natural phrasings:

### Movement
```
> go north
> walk south
> n
> head east
```

### Object Manipulation
```
> take sword
> get the red key
> pick up ancient artifact
> grab golden helmet
```

### Examination
```
> examine door
> look at statue
> x chest
> inspect mysterious artifact
> read inscription
```

### Combat
```
> attack orc
> hit guard with sword
> fight monster
> kill dragon
```

### Complex Commands
```
> put sword in backpack
> give guard golden key
> place gem on pedestal
> insert key into lock
```

## Comparison with Classic ACS Parser

### Classic Parser (`acs_parser.py`)
- Simple verb-noun matching
- Limited synonym support
- Basic pattern recognition

### Inform 7 Parser (`natural_language.py`)
- ✅ Rich synonym system
- ✅ Adjective handling ("red sword", "ancient key")
- ✅ Complex grammar patterns
- ✅ Preposition understanding ("in", "on", "with")
- ✅ Article handling ("the", "a", "an")
- ✅ Suggestion system for typos
- ✅ World model with relations and properties

## Use Cases

### 1. More Natural Commands
Players can use varied, natural language:
```
"take key" = "get the key" = "pick up key" = "grab key"
```

### 2. Adjective Disambiguation
Handle multiple similar objects:
```
"take red key"   # Different from "take blue key"
"examine old sword"  # Different from "examine new sword"
```

### 3. Flexible Phrasing
```
"put sword in bag" = "place sword into bag"
"give guard key" = "hand key to guard"
```

### 4. Educational Tool
Great for teaching:
- Natural language processing
- Grammar parsing
- Object relationships
- Game design patterns

## Technical Details

### Grammar Patterns

The parser recognizes these patterns:

1. **VERB_NOUN**: `"take sword"`
2. **VERB_ADJECTIVE_NOUN**: `"take red sword"`
3. **VERB_NOUN_PREP_NOUN**: `"put sword in bag"`
4. **VERB_PREP_NOUN**: `"look at statue"`
5. **VERB_NOUN_NOUN**: `"give guard key"`
6. **VERB_ALONE**: `"inventory"`, `"wait"`

### World Model Relations

Supported relations (like Inform 7):
- `contains` - container relationships
- `supports` - surface relationships  
- `carries` - inventory relationships
- `wears` - clothing relationships
- Custom relations can be defined

### Property System

Like Inform 7 properties:
```python
# Boolean properties
world.set_property("door", "locked", True)
world.set_property("lamp", "lit", False)

# Numeric properties
world.set_property("sword", "damage", 10)
world.set_property("armor", "defense", 5)

# Text properties
world.set_property("book", "author", "J.R.R. Tolkien")
```

## Future Enhancements

Planned additions inspired by Inform 7:

- [ ] Rules engine (like Inform 7 "instead" rules)
- [ ] Scenes and chapters
- [ ] Understand tokens with placeholders
- [ ] Multiple object handling ("take all")
- [ ] Scope management
- [ ] Action processing chain
- [ ] Story state tracking

## Credits

Inspired by [Inform 7](https://github.com/ganelson/inform) by Graham Nelson.

Inform 7 is:
- Copyright Graham Nelson 2006-2022
- Licensed under Artistic License 2.0
- One of the most influential IF systems ever created

This integration adapts Inform 7's natural language concepts for the Adventure Construction Set while maintaining ACS's Python-based architecture and Eamon compatibility.

## Example: Complete Adventure

```python
from acs.core.inform_integration import create_inform_enhanced_engine
from acs.core.engine import GameEngine

# Create enhanced engine
EnhancedEngine = create_inform_enhanced_engine(GameEngine)
game = EnhancedEngine()

# Set up world
game.set_object_kind("sword", "weapon")
game.set_object_kind("key", "item")
game.set_object_kind("chest", "container")

game.set_object_property("chest", "locked", True)
game.set_object_property("sword", "damage", 10)

game.relate_objects("contains", "chest", "treasure")
game.relate_objects("carries", "player", "key")

# Teach custom commands
game.teach_command("unlock", "open")
game.teach_command("grab", "take")

# Play!
game.process_command("examine chest")
game.process_command("unlock chest with key")
game.process_command("take treasure from chest")
game.process_command("grab the shiny sword")
```

## See Also

- `src/acs/core/natural_language.py` - Parser implementation
- `src/acs/core/inform_integration.py` - Engine integration
- `src/acs/core/parser.py` - Original ACS parser
- [Inform 7 Documentation](https://ganelson.github.io/inform)
