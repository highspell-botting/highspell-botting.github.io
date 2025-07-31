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
InventoryItem (Items)
```

## Contents

### [Entity](entity.md)
The base class for all game entities. Documents the fundamental properties and methods shared by all game objects.

### [TranslateableEntity](translateableentity.md)
Extends Entity to add movement capabilities. Base class for entities that can move around the game world.

### [PlayerEntity](playerentity.md)
Represents player characters. Extends TranslateableEntity with player-specific functionality like combat stats and skills.

### [MainPlayer](mainplayer.md)
The current player's character instance. Contains inventory management, bank access, and session-specific data.

### [WorldEntity](worldentity.md)
Environmental objects like trees, rocks, and buildings. Static entities that players can interact with.

### [InventoryItem](inventoryitem.md)
Represents items in inventories, banks, and shops. Contains item definitions and quantity information.

## Key Concepts

- **Inheritance**: How classes build upon each other to add functionality
- **State Management**: How entities maintain their current state
- **Interactions**: How entities communicate and interact with each other
- **Data Flow**: How information flows through the entity system
