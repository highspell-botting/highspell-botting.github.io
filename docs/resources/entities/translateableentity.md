# TranslateableEntity Documentation

## Overview
The `TranslateableEntity` class extends the `Entity` class and represents entities in the HighSpell game that can move or be teleported, such as players or NPCs. It adds properties and methods for handling movement, combat, equipment, and UI overlays, making it suitable for dynamic entities with combat capabilities and inventory management. This class is a key component of the game’s entity system, building on the foundational `Entity` class to support interactive and mobile game objects.

## Usefulness for botting
`TranslateableEntity` acts as a basis for all NPCs and Player Characters the user will see in the world. As such, it holds useful information such as `_isMoving` which can be used to validate a walking automation command, as well as `_hitpoints` for if a monster is undamaged and possibly free for attack.

## Inheritance
- **Extends**: `Entity`
  - Inherits properties and methods from `Entity` (e.g., `_entityId`, `_appearance`, `destroy`).
  - Calls the parent constructor with core entity parameters.

## Key Properties
The `TranslateableEntity` class defines additional properties, primarily accessed via getters and setters:

- **`_combatLevel`**: The entity’s combat level (getter and setter).
- **`_hitpoints`**: The entity’s current hitpoints, representing health.
- **`_lastGamePosition`**: A `Coordinate3D` object storing the entity’s previous position.
- **`_nextGamePosition`**: A `Coordinate3D` object storing the entity’s target position for movement (getter and setter).
- **`_movementSpeed`**: The entity’s movement speed (default `1`), with a setter that triggers `_onSprintChangeListener` if changed (e.g., sprinting at speed `2`).
- **`_isMoving`**: A boolean indicating if the entity is currently moving.
- **`_currentTarget`**: The entity’s current target (e.g., another entity for combat), with a setter that updates the entity’s direction based on the target’s position.
- **`_uiOverlayController`**: A `UIOverlayController` managing UI elements (e.g., health bars) attached to the entity’s halo or hitbox nodes.
- **`_loadout`**: An `ItemInventory` for equipped items, initialized with `MenuTypesEnum.Loadout`.
- **`_equipmentBonuses`**: An `EquipmentBonuses` object tracking stat bonuses from equipped items (e.g., strength, accuracy), initialized for human entities.
- **`_onMoveToNewTileListener`**: A `Listeners` object for tile movement events.
- **`_onTeleportedListener`**: A `Listeners` object for teleportation events.
- **`_onEquipItemListener`**: A `Listeners` object for item equip events.
- **`_onUnequipItemListener`**: A `Listeners` object for item unequip events.
- **`_onSprintChangeListener`**: A `Listeners` object for sprint state changes.
- **`_moveToNewTilePointEventArgs`**: A `PositionEntity` object for tile movement events.
- **`_drawAppearancePointEventArgs`**: A `PositionEntity` object for appearance position updates.

## Constructor MIGHT BE INCORRECT
- **Signature**: `constructor(entityId, entityTypeId, entityType, name, description, appearance, currentMapLevel, positionX, positionY, positionZ, actions, combatLevel, hitpoints)`
  - Parameters:
    - `entityId`, `entityTypeId`, `entityType`, `name`, `description`, `appearance`, `currentMapLevel`, `positionX`, `positionY`, `positionZ`, `actions`: Passed to the `Entity` constructor.
    - `combatLevel`: Sets `_combatLevel`.
    - `hitpoints`: Sets `_hitpoints`.
  - Initializes inherited properties via the `Entity` constructor.
  - Sets `_lastGamePosition` to a `Coordinate3D` with `positionX`, `0`, `positionZ`.
  - Initializes `_movementSpeed` to `1`.
  - Creates `_uiOverlayController` with the entity’s halo and hitbox nodes and hitpoints.
  - Initializes `_loadout` as an `ItemInventory` for equipment.
  - Creates event argument objects (`_moveToNewTilePointEventArgs`, `_drawAppearancePointEventArgs`) with the entity’s initial position and map level.
  - Initializes additional listeners for movement, teleportation, equipment, and sprinting.
  - For human entities (`_appearance.Type === sA.Human`), sets `_equipmentBonuses` to a new `EquipmentBonuses` object and overrides `_entityType` to `WorldEntityTypesEnum.Player`.

## Movement and Positioning
- **`teleportTo(e, t, i)`**:
  - Teleports the entity to coordinates (`e`, `0`, `t`) on map level `i`.
  - Updates `_currentGamePosition`, `_appearance` position, and `_moveToNewTilePointEventArgs`.
  - Invokes `_onTeleportedListener` with the updated position.
- **`_updateGamePosition()`**:
  - Updates `_currentGamePosition` to `_nextGamePosition` if set.
  - Adjusts the entity’s direction based on the target’s position or `_nextGamePosition` using `HighSpellUtil.getDirectionFromAToB3D`.
  - Updates `_moveToNewTilePointEventArgs` and invokes `_onMoveToNewTileListener`.
  - Clears `_nextGamePosition` after updating.

## Equipment Management
- **`initializeEquippedItems(e)`**:
  - Equips multiple items by calling `equippedItem` for each item in the provided array.
- **`equippedItem(e)`**:
  - Equips an item by ID, retrieves its definition via `ItemDefs.getDefById`, and invokes `_onEquipItemListener` with a `DF` event object.
  - Calls `_recalculateEquipmentBonuses` to update stat bonuses.
- **`unequippedItem(e)`**:
  - Unequips an item by ID, retrieves its definition, and invokes `_onUnequipItemListener` with a `GF` event object.
  - Calls `_recalculateEquipmentBonuses`.
- **`_recalculateEquipmentBonuses()`**:
  - Iterates through `_loadout.Items` to sum bonuses from `EquippableEffects` (e.g., strength, accuracy, defense, magic, range).
  - Updates `_equipmentBonuses` properties if changed.

## Rendering and Updates
- **`draw(e, t, i)`**:
  - Adjusts the entity’s Y-position on first draw or if `i` is true, using `WorldMapManager.Instance.getGroundHeightAtEntity`.
  - Updates `_uiOverlayController` and draws it.
  - Checks if the appearance’s camera target matches `_currentGamePosition` (offset by 0.5 for centering):
    - If matched, stops animations, sets `_isMoving` to `false`, and invokes `_onMoveListener` if moving.
    - If not matched, starts animations, sets `_isMoving` to `true`, updates the apparent position via `_updateApparentPosition`, and invokes `_onMoveListener` every 60ms.
  - Calls `_appearance.draw(e, t, i)`.
- **`_updateApparentPosition(e, t)`**:
  - Interpolates the entity’s visual position between `_lastGamePosition` and `_currentGamePosition` using factor `e`.
  - Sets the Y-position based on `WorldMapManager.Instance.getGroundHeightAtEntity`.
- **`update(e)`**:
  - Updates `_currentState`, `_uiOverlayController`, and calls `_updateGamePosition`.

## Destruction
- **`destroy()`**:
  - Nullifies movement-related properties (`_lastGamePosition`, `_nextGamePosition`, `_currentTarget`).
  - Destroys and nullifies `_uiOverlayController`, logging specific messages for player entities.
  - Destroys and nullifies `_equipmentBonuses` for human entities.
  - Destroys and nullifies additional listeners (`_onMoveToNewTileListener`, `_onTeleportedListener`, `_onEquipItemListener`, `_onUnequipItemListener`, `_onSprintChangeListener`).
  - Nullifies event argument objects and `_hitpoints`.
  - Calls the parent `Entity` class’s `destroy` method.

## Technical Observations
- **Movement System**: Supports smooth movement with interpolation (`_updateApparentPosition`) and discrete tile-based updates (`_updateGamePosition`).
- **Event-Driven Design**: Extensive use of listeners for movement, teleportation, and equipment changes supports a reactive architecture.
- **Player-Specific Logic**: Special handling for human/player entities (e.g., `_equipmentBonuses`, `_entityType` override) suggests tailored behavior for player characters.
- **Rendering**: Likely integrates with a 3D engine (e.g., Babylon.js, given `HaloNode`, `HitboxNode`), with dynamic ground height adjustments.
