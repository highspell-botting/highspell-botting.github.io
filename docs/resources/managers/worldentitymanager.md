# WorldEntityManager Documentation

## Overview
The `WorldEntityManager` class is a singleton responsible for managing various types of entities within the HighSpell game world. It maintains collections of world entities, walls, bridges, roofs, and planets, providing methods to add, remove, and query these entities. The class also handles specific game mechanics, such as roof visibility based on the player's position and entity resource states.

This documentation aligns with the HighSpell Botting Resources project, focusing on educational analysis and technical understanding of the game client’s structure, specifically its entity management system.

## Usefulness for botting
The **`_worldEntities`** `Map` is a convenient Map of every visible `WorldEntity` allowing the user easy access to `WorldEntityDef` and the `EntityTypeId` of the object. These `WorldEntity` objects can often be passed as they are to functions such as `TargetActionManager.handleTargetAction(e, t)` which takes an `WorldEntity`'s numeric action `e`, and the `WorldEntity`, `t`, itself to trigger said action.

## Singleton Pattern
The `WorldEntityManager` uses the singleton pattern to ensure only one instance exists:

- **Getter**: `static get Instance()`
  - Returns the singleton instance, creating it if it doesn’t exist.
  - Ensures global access to the manager without multiple instantiations.

## Key Properties
- **`_worldEntities`**: A `Map` storing world entities, keyed by their `EntityTypeID`.
- **`_walls`**: A `Map` storing wall entities, keyed by their `ID`.
- **`_bridges`**: A `Map` storing bridge entities, keyed by their `ID`.
- **`_roofs`**: A `Map` storing roof entities, keyed by their `EntityTypeID`.
- **`_planets`**: An array storing planet entities.
- **`_entitiesAwaitingReplenishment`**: A `Map` tracking entities that have exhausted resources and are awaiting replenishment.
- **`_alwaysHideRoofs`**: A boolean controlling whether roofs are always hidden, synced with `GameSettings.getAlwaysHideRoofs()`.
- **`_isMainPlayerUnderRoof`**: A boolean indicating if the main player is under a roof, affecting roof visibility.

## Constructor and Initialization
- **Constructor**: Initializes the manager with default values and binds the `handleMainPlayerMovedUnderOrOutFromRoof` method.
- **`init()`**: Sets up the manager if not already initialized (`_didInit` is `false`):
  - Creates data structures (`_worldEntities`, `_walls`, `_bridges`, `_roofs`, `_planets`, `_entitiesAwaitingReplenishment`).
  - Initializes `_upVector` for raycasting (used in roof detection).

## Entity Management Methods
### Adding Entities
These methods add entities to their respective collections, ensuring no duplicates and returning a boolean indicating success.

- **`addWorldEntity(e)`**:
  - Adds a world entity to `_worldEntities` using `e.EntityTypeID` as the key.
  - Checks for duplicates and logs a warning if the entity already exists.
  - Example: `WorldEntityManager.Instance.addWorldEntity(entity)`.

- **`addWall(e)`**:
  - Adds a wall to `_walls` using `e.ID` as the key.
  - Similar duplicate check and logging as `addWorldEntity`.

- **`addBridge(e)`**:
  - Adds a bridge to `_bridges` using `e.ID` as the key.
  - Follows the same duplicate check pattern.

- **`addRoof(e)`**:
  - Adds a roof to `_roofs` using `e.EntityTypeID` as the key.
  - Sets initial visibility of roof meshes based on `_isMainPlayerUnderRoof`.
  - Logs a warning if the roof already exists.

- **`addPlanet(e)`**:
  - Appends a planet to the `_planets` array without duplicate checks.

### Retrieving Entities
These methods fetch entities by their IDs:

- **`getWorldEntityById(e)`**: Returns the entity from `_worldEntities` with key `e`.
- **`getWallById(e)`**: Returns the wall from `_walls` with key `e`.
- **`getBridgeById(e)`**: Returns the bridge from `_bridges` with key `e`.
- **`getRoofById(e)`**: Returns the roof from `_roofs` with key `e`.
- **`getPlanetByEntityId(e)`**: Iterates `_planets` to find a planet with matching `EntityID`.

### Checking Entity Existence
These methods verify if an entity exists in its respective collection:

- **`hasWorldEntity(e)`**: Checks if `_worldEntities` has key `e`.
- **`hasWall(e)`**: Checks if `_walls` has key `e`.
- **`hasBridge(e)`**: Checks if `_bridges` has key `e`.
- **`hasRoof(e)`**: Checks if `_roofs` has key `e`.

### Removing Entities
These methods remove entities, calling their `destroy()` method and removing them from their collections:

- **`removeWorldEntityById(e)`**:
  - Destroys the entity using `EntityFactory.Instance.destroyWorldEntity` and removes it from `_worldEntities`.
- **`removeWallById(e)`**:
  - Calls `destroy()` on the wall and removes it from `_walls`.
- **`removeBridgeById(e)`**:
  - Calls `destroy()` on the bridge and removes it from `_bridges`.
- **`removeRoofById(e)`**:
  - Calls `destroy()` on the roof and removes it from `_roofs`.
- **`removePlanetByEntityId(e)`**:
  - Finds and destroys a planet with matching `EntityID`, then removes it from `_planets`.

### Bulk Removal
- **`removeEntitiesOutsideOfBorder(west, north, east, south)`**:
  - Removes entities (world entities, walls, bridges, roofs) outside the specified 2D boundaries.
  - Validates borders to ensure `west < east` and `south < north`.
  - Uses entity positions (`CurrentGamePosition` or `Position`) to determine if they are outside the bounds.

### Clearing Entities
- **`clearWorldEntities()`**:
  - Destroys and clears all entities in `_worldEntities`, `_walls`, `_bridges`, `_roofs`, and `_entitiesAwaitingReplenishment`.
- **`clearPlanets()`**:
  - Destroys and clears all planets in `_planets`.


## Resource Management
The manager tracks entities with exhausted resources, typically for lootable objects.

- **`setEntitiesExhaustedResources(entityIds, flag, coords)`**:
  - Marks entities in `entityIds` as exhausted (`exhaustedResources()`) and adds them to `_entitiesAwaitingReplenishment`.
  - If `flag` is `true`, checks nearby entities in `_entitiesAwaitingReplenishment` and replenishes those not in `entityIds` if they share the same grid tile (based on `IF` constant).
- **`setEntityReplenishedResources(e)`**:
  - Calls `replenishedResources()` on the entity with ID `e` and removes it from `_entitiesAwaitingReplenishment`.
- **`markEntityAsLooted(e, isLooted)`**:
  - Marks an entity as exhausted or replenished based on the `isLooted` boolean.

## Smelting Check
- **`isTargetForSmelting(e)`**:
  - Checks if the entity with ID `e` has a `smelt` action in its `Actions` array.
  - Returns `true` if the entity can be smelted.

## Door Interaction
- **`handleWalkThroughDoor(e, t)`**:
  - Handles door animation when the player walks through a door entity (`e`).
  - Adjusts the door’s rotation and position based on its `Direction` (e.g., `S` for south, `W` for west).
  - Temporarily disables the door (`IsEnabled = false`), moves it, and resets it after a 1.2-second timeout.

## Entity Updates and Rendering
- **`updateEntities(delta)`**:
  - Updates all planets and world entities by calling their `update(delta)` methods.
- **`drawEntities(e, t, i)`**:
  - Draws planets and animateable world entities by calling their `draw(e, t, i)` methods.

## Reset
- **`reset()`**:
  - Resets the manager by setting `_didInit` and `_isMainPlayerUnderRoof` to `false` and clearing all entities and planets.
