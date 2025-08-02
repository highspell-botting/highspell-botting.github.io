# Entity Classes

This section contains documentation for the core entity classes that represent game objects in the HighSpell client.

## Overview

Entities are the fundamental building blocks of the game world. They represent players, NPCs, items, and environmental objects. Understanding the entity hierarchy is crucial for understanding how the game manages state and interactions.

## Class Hierarchy

```
Entity (Base)
└── TranslateableEntity (Movable)
    ├── PlayerEntity (Players & NPCs)
    │   └── MainPlayer (Current Player)
    └── WorldEntity (Environmental Objects)
        └── GroundItem (Items on Ground)
InventoryItem (Items in Inventories)
SpellDef (Spell Definitions)
```

## Contents

### [Entity](entity.md)
The base class for all game entities. Documents the fundamental properties and methods shared by all game objects.

### [TranslateableEntity](translateableentity.md)
Extends Entity to add movement capabilities. Base class for entities that can move around the game world.

### [PlayerEntity](playerentity.md)
Represents player characters. Extends TranslateableEntity with player-specific functionality like combat stats and skills.

### [MainPlayer](mainplayer.md)
The current player's character instance. Contains inventory management, bank access, and session-specific data. Works with [ItemManager](../managers/itemmanager.md) for inventory interactions.

### [WorldEntity](worldentity.md)
Environmental objects like trees, rocks, and buildings. Static entities that players can interact with. Managed by [WorldEntityManager](../managers/worldentitymanager.md).

### [GroundItem](grounditem.md)
Represents items that appear on the ground (dropped loot, resources). Extends Entity with item-specific properties like quantity and cost. Managed by [GroundItemManager](../managers/grounditemmanager.md) and interacts with [TargetActionManager](../managers/targetactionmanager.md) for pickup actions.

### [InventoryItem](inventoryitem.md)
Represents items in inventories, banks, and shops. Contains item definitions and quantity information. Used by [ItemManager](../managers/itemmanager.md) for inventory management and [MainPlayer](mainplayer.md) for storage.

### [SpellDef & SpellDefs](spelldefs.md)
SpellDef represents individual spell definitions with properties like type, level, and requirements. SpellDefs is a static utility class that manages all spell definitions. Used by [SpellManager](../managers/spellmanager.md) for spell casting and selection.

## Key Concepts

- **Inheritance**: How classes build upon each other to add functionality
- **State Management**: How entities maintain their current state
- **Interactions**: How entities communicate and interact with each other
- **Data Flow**: How information flows through the entity system
- **Manager Integration**: How entities are managed by specialized manager classes

## Entity Categories

### Core Game Objects
- **Entity**: Base class for all game objects
- **TranslateableEntity**: Entities that can move in the world
- **PlayerEntity**: Player characters with combat and skill systems
- **MainPlayer**: Current player with inventory and session data

### Environmental Objects
- **WorldEntity**: Static objects like trees, rocks, buildings
- **GroundItem**: Items placed on the ground for pickup

### Data Structures
- **InventoryItem**: Items stored in inventories and banks
- **SpellDef**: Spell definitions with properties and requirements

## Cross-Entity Dependencies

- **GroundItem ↔ GroundItemManager**: GroundItem instances are managed by GroundItemManager for lifecycle and rendering
- **GroundItem ↔ TargetActionManager**: GroundItem interactions (grab, examine) are handled by TargetActionManager
- **InventoryItem ↔ ItemManager**: InventoryItem objects are manipulated by ItemManager for actions like use, equip, drop
- **InventoryItem ↔ MainPlayer**: MainPlayer stores InventoryItem collections in inventory and bank
- **SpellDef ↔ SpellManager**: SpellDef objects are used by SpellManager for spell selection and casting
- **WorldEntity ↔ WorldEntityManager**: WorldEntity instances are managed by WorldEntityManager for spawning and despawning
- **MainPlayer ↔ ItemManager**: MainPlayer coordinates with ItemManager for inventory interactions

## Entity Lifecycle

### Creation
- **WorldEntity**: Created by WorldEntityManager when entering map chunks
- **GroundItem**: Created by GroundItemManager from network packets
- **InventoryItem**: Created by ItemManager for inventory operations
- **SpellDef**: Loaded by SpellDefs from JSON data

### Destruction
- **WorldEntity**: Destroyed by WorldEntityManager when leaving map chunks
- **GroundItem**: Destroyed by GroundItemManager when picked up or expired
- **InventoryItem**: Destroyed by ItemManager when consumed or dropped
- **SpellDef**: Destroyed when SpellDefs is reset
