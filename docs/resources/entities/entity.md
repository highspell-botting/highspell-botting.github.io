# Entity Documentation

## Overview
The `Entity` class serves as the base class for all entities in the HighSpell game, providing a foundation for objects like players, NPCs, and world objects (e.g., `WorldEntity`). It encapsulates core properties such as identity, position, state, and appearance, along with event listeners for lifecycle events. This class is extended by more specific classes like `WorldEntity` to add specialized behavior.

This documentation aligns with the HighSpell Botting Resources project, offering a technical analysis of the `Entity` class to enhance understanding of the game client’s entity system, emphasizing educational insights over exploitative automation.

## Usefulness for botting
A `Entity` is the basis holds most of the information that's useful in dervied classes, such as `EntityId` for NPCs and `EntityTypeId` for World Objects. 

## Key Properties
The `Entity` class defines properties, primarily accessed via getters, with one setter for state changes:

- **`_entityId`**: A unique identifier for the entity.
- **`_entityTypeId`**: An identifier for the entity’s type (e.g., specific to a class or category).
- **`_entityType`**: The entity’s type, likely an enum or constant defining its category.
- **`_nameLowerCase`**: The entity’s name in lowercase, derived from the provided name.
- **`_name`**: The entity’s capitalized name, formatted using `HighSpellUtil.simpleCapitalization`.
- **`_description`**: A textual description of the entity.
- **`_appearance`**: An object managing the entity’s visual representation (e.g., 3D model or sprite).
- **`_currentMapLevel`**: The entity’s current map level (getter and setter).
- **`_currentGamePosition`**: A `Coordinate3D` object representing the entity’s 3D position (x, y, z).
- **`_currentState`**: The entity’s current state (e.g., `IdleState.Idle`), with a setter that triggers state change events.
- **`_actions`**: An array of possible actions the entity can perform (e.g., `TargetAction.inspect`).
- **`_onAppearListener`**: A `Listeners` object for handling appearance events.
- **`_onDisappearListener`**: A `Listeners` object for handling disappearance events.
- **`_onMoveListener`**: A `Listeners` object for handling movement events.
- **`_onSwitchedStateListener`**: A `Listeners` object for handling state changes.
- **`_isDestroyed`**: A boolean indicating if the entity has been destroyed.

## Constructor MIGHT BE INCORRECT
- **Signature**: `constructor(entityId, entityTypeId, entityType, name, description, appearance, currentMapLevel, positionX, positionY, positionZ, actions)`
  - Parameters:
    - `entityId`: Unique identifier for the entity (`_entityId`).
    - `entityTypeId`: Identifier for the entity type (`_entityTypeId`).
    - `entityType`: Type of the entity, likely an enum or constant (`_entityType`).
    - `name`: Display name, used to set `_name` and `_nameLowerCase`.
    - `description`: Descriptive text for the entity (`_description`).
    - `appearance`: Visual or rendering properties (`_appearance`).
    - `currentMapLevel`: Current map level the entity is on (`_currentMapLevel`).
    - `positionX, positionY, positionZ`: X, Y, Z coordinates for `_currentGamePosition`.
    - `actions`: List of possible actions the entity can perform (`_actions`).
  - Initializes properties, sets `_currentState` to `IdleState.Idle`, and creates `Listeners` objects for events.
  - Derives `_nameLowerCase` from `name` (lowercase) and `_name` using `HighSpellUtil.simpleCapitalization`.
  - Creates a `Coordinate3D` object for `_currentGamePosition` using `positionX`, `positionY`, and `positionZ`.
  - Ensures `_actions` is an array, initializing it to an empty array if `null` or `undefined`.
  - Sets `_isDestroyed` to `false`.

## State Management
- **`CurrentState` (getter/setter)**:
  - Getter: Returns the current state.
  - Setter: Updates `_currentState` and triggers `_onSwitchedStateListener` with an `EntityStateChangeEvent` containing the entity’s IDs, old state, and new state.

## Lifecycle Methods
- **`update(e)`**:
  - An empty method intended to be overridden by subclasses for updating entity logic (e.g., based on a delta time `e`).
- **`draw(e, t, i)`**:
  - An empty method intended to be overridden by subclasses for rendering the entity.
- **`beforeDestroy()`**:
  - Invokes `_onDisappearListener` to notify listeners before destruction.
- **`destroy()`**:
  - Cleans up the entity by:
    - Destroying and nullifying all listeners (`_onAppearListener`, `_onDisappearListener`, `_onMoveListener`, `_onSwitchedStateListener`).
    - Nullifying properties (`_entityId`, `_entityTypeId`, `_entityType`, `_name`, `_nameLowerCase`, `_description`, `_currentMapLevel`, `_currentGamePosition`).
    - Clearing and nullifying `_actions`.
    - Exiting and nullifying `_currentState`.
    - Destroying and nullifying `_appearance`.
    - Setting `_isDestroyed` to `true`.

## Event System
The class uses a `Listeners` system to manage events:
- **Listeners**: `_onAppearListener`, `_onDisappearListener`, `_onMoveListener`, `_onSwitchedStateListener` allow external components to subscribe to entity events (e.g., appearing, moving, state changes).
- **State Change Event**: The `EntityStateChangeEvent` includes the entity’s type ID, ID, and state transition details, enabling precise event handling.

## Technical Observations
- **Extensibility**: The empty `update` and `draw` methods suggest the class is designed for inheritance, with subclasses like `WorldEntity` adding specific behavior.
- **Event-Driven Design**: The listener system supports a reactive architecture, allowing dynamic responses to entity changes.
- **Destruction**: The `destroy` method is thorough, nullifying all properties and cleaning up resources, likely to prevent memory leaks in a 3D rendering context (e.g., Babylon.js).
