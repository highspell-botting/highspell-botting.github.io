# Manager Classes

This section contains documentation for the manager classes that coordinate and manage collections of entities and game systems.

## Overview

Manager classes implement the Manager pattern, providing centralized control over related functionality. They handle collections of objects, coordinate interactions, and maintain system state.

## Contents

### [WorldEntityManager](worldentitymanager.md)
Manages all world entities (trees, rocks, buildings, etc.) in the current map chunk. Handles entity spawning, despawning, and spatial organization.

### [TargetActionManager](targetactionmanager.md)
Manages available actions for different entity types. Determines what actions (attack, examine, use, etc.) are available when targeting entities.

### [TargetActionEnum](targetaction.md)
Enumeration of all possible target actions. Documents the numeric IDs and string mappings for different interaction types.

## Key Concepts

- **Manager Pattern**: Centralized control over related functionality
- **State Coordination**: How managers maintain consistent system state

## Manager Responsibilities

### WorldEntityManager
- **Entity Storage**: Maintains collections of world entities
- **Entity Lifecycle**: Handles entity creation and destruction

### TargetActionManager
- **Context Awareness**: Determines actions based on current game state
- **Interaction Logic**: Coordinates action execution
