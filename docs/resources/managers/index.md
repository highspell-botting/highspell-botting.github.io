# Manager Classes

This section contains documentation for the manager classes that coordinate and manage collections of entities and game systems.

## Overview

Manager classes implement the Manager pattern, providing centralized control over related functionality. They handle collections of objects, coordinate interactions, and maintain system state.

## Contents

### [WorldEntityManager](worldentitymanager.md)
Manages all world entities (trees, rocks, buildings, etc.) in the current map chunk. Handles entity spawning, despawning, and spatial organization.

### [GroundItemManager](grounditemmanager.md)
Manages ground items (dropped loot, resources) in the game world. Handles item lifecycle, rendering, and spatial organization. Works closely with [TargetActionManager](targetactionmanager.md) for item interactions.

### [TargetActionManager](targetactionmanager.md)
Manages available actions for different entity types. Determines what actions (attack, examine, use, etc.) are available when targeting entities. Integrates with [GroundItemManager](grounditemmanager.md) for ground item interactions and [ItemManager](itemmanager.md) for item-on-entity actions.

### [ItemManager](itemmanager.md)
Manages player interactions with inventory items, including selecting, using, equipping, and performing actions like dropping or trading. Coordinates with [TargetActionManager](targetactionmanager.md) for item-on-entity interactions and uses [MenuTypesEnumOriginal](menutypes.md) for UI context.

### [SpellManager](spellmanager.md)
Manages spell-related actions including selecting, casting, and auto-casting spells. Handles different spell types (teleport, inventory, status, combat) and coordinates with [TargetActionManager](targetactionmanager.md) for spell targeting.

### [TargetActionEnum](targetaction.md)
Enumeration of all possible target actions. Documents the numeric IDs and string mappings for different interaction types. Used by [TargetActionManager](targetactionmanager.md) and [SpellManager](spellmanager.md).

### [MenuTypesEnumOriginal](menutypes.md)
Enumeration of menu types used for UI context in item interactions. Defines contexts like Inventory, Bank, Shop, and various skill interfaces. Used by [ItemManager](itemmanager.md) for inventory actions.

## Key Concepts

- **Manager Pattern**: Centralized control over related functionality
- **State Coordination**: How managers maintain consistent system state
- **Singleton Pattern**: Most managers use singleton instances for global access
- **Event-Driven Design**: Managers use listeners and callbacks for system coordination

## Manager Responsibilities

### WorldEntityManager
- **Entity Storage**: Maintains collections of world entities
- **Entity Lifecycle**: Handles entity creation and destruction

### GroundItemManager
- **Ground Item Storage**: Maintains collections of items on the ground
- **Spatial Management**: Handles item culling based on map boundaries
- **Rendering Coordination**: Manages drawing of all ground items

### TargetActionManager
- **Context Awareness**: Determines actions based on current game state
- **Interaction Logic**: Coordinates action execution
- **Pathfinding**: Handles player movement to target locations

### ItemManager
- **Inventory Management**: Handles item selection and actions
- **UI Integration**: Coordinates confirmation dialogs and text input
- **Network Communication**: Emits packets for server-side validation

### SpellManager
- **Spell Selection**: Manages currently selected spells
- **Auto-Casting**: Handles automatic spell casting
- **Skill Integration**: Updates player experience for spell casting

## Cross-Manager Dependencies

- **TargetActionManager ↔ GroundItemManager**: TargetActionManager uses GroundItemManager.Instance.getGroundItemByEntityId for ground item interactions
- **TargetActionManager ↔ ItemManager**: TargetActionManager calls ItemManager methods for item-on-entity actions
- **ItemManager ↔ MenuTypesEnumOriginal**: ItemManager uses menu types for UI context in inventory actions
- **SpellManager ↔ TargetActionManager**: SpellManager integrates with TargetActionManager for spell targeting
- **All Managers ↔ Network Layer**: All managers emit packets via SocketManager for server communication
