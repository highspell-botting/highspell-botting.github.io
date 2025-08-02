# GroundItemManager Documentation

## Overview
The `GroundItemManager` class is a singleton in the HighSpell game responsible for managing ground items—items that appear on the game world’s surface, such as dropped loot or resources. It maintains a collection of ground items, handles their addition and removal, and coordinates their rendering and lifecycle events. This class is critical for tracking and displaying items that players can interact with, such as those picked up via the `grab` action defined in `TargetActionOriginal`.

## Usefulness for botting
The `GroundItemManager` is where you can find a list of all the available `GroundItem`'s, their `Actions`, and `Def` which includes name, location, etc. Allowing you to quite easily find an item on the ground and interact with it.


## Key Features
- **Singleton Pattern**: Ensures a single instance via the `Instance` getter, lazily initializing `_groundItemManager`.
- **Item Storage**: Uses a `Map` to store ground items by their `EntityID`.
- **Lifecycle Management**: Handles adding, removing, and clearing ground items, triggering appearance and disappearance events.
- **Rendering**: Coordinates drawing of all ground items.
- **Spatial Management**: Removes items outside specified map boundaries.

## Key Properties
- **`_groundItemManager` (static)**: The singleton instance of `GroundItemManager`.
- **`_groundItems`**: A `Map` storing ground items, with `EntityID` as the key and the item entity as the value.
- **`GroundItemCount` (getter)**: Returns the number of ground items in `_groundItems`.
- **`GroundItems` (getter)**: Returns the `_groundItems` Map.

## Constructor
- **Signature**: `constructor()`
  - Initializes `_groundItems` as an empty `Map`.

## Static Methods
- **`Instance` (getter)**:
  - Returns the singleton instance, creating a new `GroundItemManager` if `_groundItemManager` is undefined.

## Instance Methods
- **`removeItemsOutsideOfBorder(e, t, i, n)`**:
  - **Parameters**:
    - `e`: Western border (x-coordinate minimum).
    - `t`: Northern border (z-coordinate maximum).
    - `i`: Eastern border (x-coordinate maximum).
    - `n`: Southern border (z-coordinate minimum).
  - **Functionality**:
    - Validates border coordinates, logging a warning if `e >= i` or `n >= t`.
    - Iterates through `_groundItems`, removing items whose `CurrentGamePosition` is outside the specified borders (x < `e`, x >= `i`, z >= `t`, or z < `n`) using `removeItemByEntityId`.

- **`clearGroundItems(e=null)`**:
  - **Parameters**:
    - `e`: Optional map level to clear items from; if `null`, clears all items.
  - **Functionality**:
    - Iterates through `_groundItems`, removing and destroying items that match the specified map level (or all items if `e` is `null`) using `EntityFactory.Instance.destroyGroundItemEntity`.
    - Deletes items from `_groundItems`.

- **`addGroundItem(e)`**:
  - **Parameters**:
    - `e`: Packet data containing item details, including `ItemEnteredChunkEnum.EntityID`.
  - **Functionality**:
    - Retrieves the `EntityID` from the packet data.
    - If the item is not already in `_groundItems`, creates a new ground item using `EntityFactory.Instance.createGroundItemFromPacketData`.
    - Adds the item to `_groundItems` and invokes its `OnAppearListener` with a `PositionEntity` derived from the item’s `Appearance.CameraTarget` and `CurrentMapLevel`.

- **`getGroundItemByEntityId(e)`**:
  - **Parameters**:
    - `e`: The `EntityID` of the ground item.
  - **Functionality**:
    - Retrieves and returns the ground item from `_groundItems` by `EntityID`.
    - Returns `undefined` if not found.

- **`removeItemByEntityId(e)`**:
  - **Parameters**:
    - `e`: The `EntityID` of the ground item to remove.
  - **Functionality**:
    - Retrieves the item from `_groundItems`.
    - If found, deletes it from `_groundItems`, invokes its `OnDisappearListener` with a `PositionEntity`, and destroys it using `EntityFactory.Instance.destroyGroundItemEntity`.

- **`drawEntities(e, t, i)`**:
  - **Parameters**:
    - `e`, `t`, `i`: Parameters passed to the `draw` method of each ground item (likely rendering context, delta time, and a boolean flag).
  - **Functionality**:
    - Iterates through `_groundItems` values and calls each item’s `draw` method with the provided parameters.

- **`reset()`**:
  - Calls `clearGroundItems` to remove and destroy all ground items.

## Technical Observations
- **Dependencies**: Relies on external classes (`EntityFactory`, `PositionEntity`) and assumes ground items inherit from `Entity` (with properties like `CurrentGamePosition`, `Appearance`, `OnAppearListener`, `OnDisappearListener`).
- **Singleton Pattern**: The `Instance` getter ensures a single manager instance, typical for global resource management in games.
- **Event-Driven Design**: Uses `OnAppearListener` and `OnDisappearListener` to notify other systems of item lifecycle events.
- **Spatial Management**: The `removeItemsOutsideOfBorder` method indicates a chunk-based world, where items are culled based on map boundaries.
- **Network Integration**: The `addGroundItem` method processes packet data, suggesting ground items are synchronized with the server.

## Ethical and Legal Notes
Per the HighSpell Botting Resources ethos:
- This documentation is for educational purposes, analyzing observable game client behavior.
- Using this information to create bots violates HighSpell’s terms of service, risking account bans.