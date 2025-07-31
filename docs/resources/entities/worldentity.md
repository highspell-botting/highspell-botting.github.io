# WorldEntity Documentation

## Overview
The `WorldEntity` class extends the `Entity` base class and represents an individual entity within the HighSpell game world, such as trees, rocks, chests, or fishing spots. It encapsulates properties and behaviors specific to world entities, including their type, dimensions, resource states, and visual appearance. This class is integral to the game’s entity management system, managed by the `WorldEntityManager`.

## Usefulness for botting
A `WorldEntity` is the basis for most automation in the game. Any world entity has a list of `Actions` that can taken on it. Every `WorldEntity` is unique, rather than having an `EntityId` it uses an `EntityTypeId` these can uniquely identify objects around the world.

## Inheritance
- **Extends**: `Entity`
  - Inherits base properties and methods (e.g., `_appearance`, `destroy`) from the `Entity` class.

## Key Properties
The `WorldEntity` class defines several properties, primarily accessed via getters:

- **`_def`**: A definition object retrieved from `YA.getDefById(u)`, providing metadata for the entity.
- **`_type`**: The entity’s type (e.g., `WorldEntityType.barrier`, `WorldEntityType.chest`), stored as `u`.
- **`_direction`**: The entity’s orientation (e.g., cardinal direction).
- **`_isSolid`**: A boolean indicating if the entity blocks movement.
- **`_length`, `_width`, `_height`**: Dimensions of the entity in game units.
- **`_canProjectile`**: A boolean indicating if the entity can be targeted by projectiles.
- **`_entityEventActions`**: Actions or events associated with the entity (e.g., interactions).
- **`_isEnabled`**: A boolean indicating if the entity is active (getter and setter).
- **`_isAnimateable`**: A boolean indicating if the entity has animations (e.g., for fires or fishing spots).
- **`_tileLength`, `_tileWidth`**: The entity’s footprint in tile units.
- **`_areResourcesExhausted`**: A boolean tracking if the entity’s resources (e.g., ore, wood) are depleted.
- **`_name`**: The entity’s name, switching to `_exhaustedName` if resources are exhausted.
- **`_description`**: The entity’s description, switching to `_exhaustedDescription` if resources are exhausted.
- **`_actions`**: An array of possible actions (e.g., `TargetAction.inspect`), switching to `fB` (an undefined constant) when resources are exhausted.

## Constructor
- **Signature**: `constructor(e, t, i, n, r, s, a, o, l, h, c, u, d, p, f, _, m, g, v, C, S, E=false)`
  - Parameters map to properties (e.g., `u` for `_type`, `d` for `_isSolid`, etc.).
  - Calls the parent `Entity` constructor with the first 11 parameters.
  - Initializes properties and sets `_isEnabled` to `true` and `_areResourcesExhausted` to `false`.
  - Computes `_exhaustedName` and `_exhaustedDescription` via helper methods.
  - Sets `_isAnimateable` based on the `E` parameter or `_setIsAnimateable`.
  - Adds `TargetAction.inspect` to `_actions` for non-barrier entities, and `TargetAction.edit_entity` if `MapEditor` is active.
  - For `WorldEntityType.floor` entities with `l === 0`, adjusts the Y-position using `WorldMapManager.Instance.getGroundHeightAtEntity`.

## Resource State Management
The class manages resource states for entities like trees, ores, or chests, affecting their appearance and interaction.

- **`_getExhaustedName()`**:
  - Returns `"Tree stump"` for choppable entities (via `ForestySkill.isEntityChoppable`), otherwise returns `_name`.
- **`_getExhaustedDescription()`**:
  - Returns context-specific descriptions based on entity type:
    - Choppable: `"This tree was cut down"`.
    - Mineable: `"The ore in these rocks has been depleted"`.
    - Chest: `"This chest has been looted"`.
    - Default: Returns `_description`.
- **`exhaustedResources()`**:
  - Marks the entity as exhausted (`_areResourcesExhausted = true`).
  - Modifies appearance based on entity type:
    - **Mineable**: Recolors to a grayish tone (`new ro.v9(.35, .35, .35)`).
    - **Choppable, Fishable, Harvestable**: Hides the appearance and sets `_isEnabled = false`.
    - **Chest**: Sets appearance alpha to `0.5` (semi-transparent).
- **`replenishedResources()`**:
  - Reverts the entity to its non-exhausted state (`_areResourcesExhausted = false`).
  - Restores appearance:
    - **Mineable**: Reverts to original material.
    - **Choppable, Fishable, Harvestable**: Shows the appearance and sets `_isEnabled = true`.
    - **Chest**: Sets appearance alpha to `1` (fully opaque).

## Animation Handling
- **`_setIsAnimateable()`**:
  - Sets `_isAnimateable` to `true` for specific entity types (`fire`, `searchablefire`, `beachsidefishingspot`, `riverfishingspot`, `oceanfishingspot`, `lakefishingspot`), otherwise `false`.

## Rendering and Updates
- **`draw(e, t, i)`**:
  - Delegates drawing to the `_appearance.draw(e, t, i)` method if `_appearance` exists.
- **`update(e)`**:
  - Updates the entity’s appearance by calling `_appearance.update(e)` if `_appearance` exists.

## Destruction
- **`destroy()`**:
  - Nullifies all properties to free memory.
  - Clears `_entityEventActions` if it exists.
  - Calls the parent `Entity` class’s `destroy` method.

## Technical Observations
- **Appearance Management**: The `_appearance` object (inherited from `Entity`) handles visual changes, such as recoloring, hiding, or adjusting transparency, indicating a 3D rendering pipeline (likely Babylon.js, given `ro.v9`).
- **Map Editor**: The inclusion of `MapEditor` logic suggests the class supports both gameplay and development tools.
