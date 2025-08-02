# TargetActionManager Documentation

## Overview
The `TargetActionManager` class is a static utility class in the HighSpell game responsible for managing interactions between the player and entities or locations in the game world. It processes mouse pointer input to identify entities and their associated actions (e.g., inspect, attack, walk), handles action execution, and facilitates pathfinding for movement. This class is central to the game’s interaction system, enabling context-sensitive actions based on what the player clicks.

## Usefulness for Botting
The `TargetActionManager` class is a critical component for bot development due to its role in processing player interactions with entities and the game world. Methods like `handleTargetAction(e, t)` allow bots to simulate actions such as `attack`, `grab`, or `talk_to` on specific entities (e.g., NPCs, players, or objects) by passing the appropriate `TargetAction` enum value and entity object. Similarly, `handleWalkTo(e, t)` enables automated navigation by specifying coordinates, leveraging the built-in client-side pathfinding to move the player efficiently. By combining these methods with data from classes like `EntityManager`, `GroundItemManager`, and `WorldEntityManager`, bots can identify targets and execute complex sequences of actions, such as looting items or engaging in combat.

## Key Features
- **Static Methods**: All methods are static, indicating the class is a utility without instantiation.
- **Mouse Interaction**: Processes picking information from mouse clicks to identify entities and generate actionable options.
- **Action Sorting**: Prioritizes actions based on distance and type, with special handling for ground items.
- **Pathfinding**: Implements client-side pathfinding for player movement to clicked coordinates.
- **Action Handling**: Executes actions like inspecting, editing entities, or performing world interactions, with network communication for server-side validation.
- **Event Emission**: Sends action and movement requests to the server via sockets.

## Static Methods
### Mouse Interaction
- **`getActionsAndEntitiesAtMousePointer(e, t, i)`**:
  - **Parameters**:
    - `e`: Likely the mouse event or coordinates (x).
    - `t`: Likely the mouse event or coordinates (z).
    - `i`: Array of picking information (e.g., raycast results from mouse clicks).
  - **Functionality**:
    - Clears previous `_mousePointActionsAndEntitiesResult` if it exists.
    - Returns a default `UV` object (null coordinates, no actions, no entities, not ground) if `i` is empty or invalid.
    - Uses `HM.uniqBy` to filter unique picking info by mesh name.
    - Identifies entities based on mesh name prefixes:
      - `_npc`: Retrieves NPC via `EntityManager.Instance.getNPCByEntityId`.
      - `_ite`: Retrieves ground item via `GroundItemManager.Instance.getGroundItemByEntityId`.
      - `_we_`: Retrieves world entity via `WorldEntityManager.Instance.getWorldEntityById`, with special handling for cave entrances/exits to avoid duplicates.
      - `_pla`: Retrieves player via `EntityManager.Instance.getPlayerByEntityId`.
      - `_pl@`: Retrieves planet via `WorldEntityManager.Instance.getPlanetByEntityId`.
      - `_bri` or `grou`: Marks as ground interaction, setting `Coordinate2D` for the clicked point.
    - Builds two action arrays:
      - `a`: Actions requiring combat level checks (e.g., `TargetAction.attack` if target’s combat level exceeds the player’s).
      - `o`: Other actions, including non-combat actions and attacks on lower-level targets.
    - If ground is clicked, adds a `walk_here` action, optionally with an associated entity (prioritizes players).
    - In map editor mode, adds `add_entity` action if applicable.
    - Sorts actions by distance and type using `_sortActionsAndEntities`.
    - Returns a `UV` object containing the clicked coordinates, combined actions, entity count, and ground interaction flag.

### Sorting and Comparison
- **`_sortActionsAndEntities(e, t)`**:
  - Sorts action-entity pairs (`VV` objects) by:
    - Distance from the camera using `_sortActionsAndEntitiesByDistanceFromCamera`.
    - Action type (`e.Action - t.Action`) if distances are equal.
    - Entity type ID (`EntityTypeID`) if actions are equal, with special handling for ground items via `_sortGroundItemsForContextMenu`.
- **`_sortActionsAndEntitiesByDistanceFromCamera(e, t)`**:
  - Delegates to `_sortEntitiesByDistanceFromCamera` to compare entity distances.
- **`_sortPickingInfoByDistanceFromCamera(e, t)`**:
  - Compares picking info based on Manhattan distance from the camera to the picked mesh’s position.
- **`_sortEntitiesByDistanceFromCamera(e, t)`**:
  - Compares entities based on Manhattan distance from the camera to their `CurrentGamePosition`.
- **`_sortGroundItemsForContextMenu(e, t)`**:
  - Prioritizes specific ground item IDs (190, 39, 421) and sorts by `FullCost` or `Def.ID` for others.

### Cave Action Deduplication
- **`_isCaveAlreadyAddedToEntitiesArray(e, t, i, n)`**:
  - Checks if a cave action (`i`, e.g., `TargetAction.enter`) at coordinates (`e`, `t`) is already in the action map `n`.
- **`_isCaveAlreadyAddedToEntitiesArray2(e, t, i, n)`**:
  - Checks if a cave action (`i`) at coordinates (`e`, `t`) is already in the entity array `n`.

### Action Insertion
- **`_insertEntityIntoQuickActionArray(e, t)`**:
  - Adds entity `e` to the action map `t`, using the first action or `TargetAction.inspect` as the key.

### Action Handling
- **`handleTargetAction(e, t)`**:
  - **Parameters**:
    - `e`: The action to perform (e.g., `TargetAction.inspect`).
    - `t`: The target entity.
  - Hides the minimap walk flag unless the action is `inspect` or the entity is environmental.
  - Handles specific actions:
    - `edit_entity`: Calls `_handleEditEntityAction` in development mode.
    - `inspect`: Calls `_handleInspectAction`.
    - `moderate`: Shows the moderation menu for the target’s name.
    - `open`, `mine_through`, `climb_same_map_level`, `go_through`: Calls `_handlePerformActionOnWorldEntityWithEntityEventActions`.
    - Others: Emits the action via `_emitPerformActionOnEntityAction`.
  - Shows the walk flag for environmental entities if applicable.
- **`_handleEditEntityAction(e)`**:
  - Sets the entity for editing in `MapEditor` if in development mode.
- **`_handleInspectAction(e)`**:
  - Displays the entity’s description (and IDs in development mode) in the chat menu.
- **`_handlePerformActionOnWorldEntityWithEntityEventActions(e, t)`**:
  - Emits the action via `_emitPerformActionOnEntityAction` if the entity has `EntityEventActions`.
- **`handleItemOnEntityAction(e, t)`**:
  - Handles using an item (`t`) on an entity (`e`), hiding and showing the walk flag and emitting the action via `_emitUseItemOnEntityAction`.

### Event Emission
- **`_emitPerformActionOnEntityAction(e, t)`**:
  - Emits a `PerformActionOnEntityAction` packet via `SocketManager` using the action and entity’s ID (from `_getEntityIdForPerformingTargetAction`).
- **`_emitUseItemOnEntityAction(e, t)`**:
  - Emits a `UseItemOnEntityAction` packet for using item `t` on entity `e`.
- **`_getEntityIdForPerformingTargetAction(e)`**:
  - Returns the appropriate ID based on entity type (`EntityTypeID` for environment/players, `EntityID` for items/NPCs).

### Movement and Pathfinding
- **`handleWalkTo(e, t)`**:
  - Handles player movement to coordinates (`e`, `t`):
    - Hides the minimap walk flag.
    - Rounds coordinates to integers.
    - Checks if the destination is the player’s current position or if in development mode.
    - Performs client-side pathfinding using `pA.clientSidePathfind` up to 13 attempts, adjusting coordinates if needed via `_getNextPathfindCoordinates`.
    - Emits a `SendMovementPathAction` packet to the server.
    - Shows the walk flag at the final coordinates.
- **`_getNextPathfindCoordinates(e, t, i, n)`**:
  - Adjusts target coordinates (`e`) for pathfinding retries, testing adjacent tiles in a specific order (e.g., +1 Z, -1 Z, +1 X, etc.).

### Reset
- **`reset()`**:
  - Destroys and nullifies `_mousePointActionsAndEntitiesResult`.

## Technical Observations
- **Raycasting**: Processes picking info from raycasts.
- **Pathfinding**: Implements client-side pathfinding with fallback coordinates.
- **Network Integration**: Uses `SocketManager` for server communication, ensuring actions are validated server-side.

## Ethical and Legal Notes
Per the HighSpell Botting Resources ethos:
- This documentation is for educational purposes, analyzing observable game client behavior.
- Using this information to create bots violates HighSpell’s terms of service, risking account bans.