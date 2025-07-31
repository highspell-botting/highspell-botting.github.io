# PlayerEntity Documentation

## Overview
The `PlayerEntity` class extends the `TranslateableEntity` class and represents the player character in the HighSpell game. It builds on the movement and combat capabilities of `TranslateableEntity`, adding player-specific properties like type and mental clarity type. This class is designed to handle the unique behaviors and rendering needs of the player, a central entity in the game’s interactive environment.

## Usefulness for botting
A `PlayerEntity` is the class used by the `Players` array in the `EntityManager` class. It's useful to be able to see who is around you, to conditionally decide when to or when not to perform automated actions. Or perhaps decide if a rock is too contested. 


## Inheritance
- **Extends**: `TranslateableEntity`
  - Inherits properties and methods from `TranslateableEntity` (e.g., `_combatLevel`, `_hitpoints`, `teleportTo`) and indirectly from `Entity` (e.g., `_entityId`, `_appearance`).
  - Calls the parent constructor with core parameters and additional combat-related arguments.

## Key Properties
The `PlayerEntity` class defines additional properties, accessed via getters and setters:

- **`_type`**: The specific type of the player entity, likely an enum or identifier defining the player’s role or class.
- **`_mentalClarityType`**: A property representing the player’s mental clarity state or type (getter and setter), possibly related to gameplay mechanics like focus or status effects.

## Constructor
- **Signature**: `constructor(e, t, i, n, r, s, a, o, l, h, c, u, d, p, f)`
  - Parameters:
    - `e`, `t`, `i`, `n`, `r`, `s`, `a`, `o`, `l`, `h`, `c`, `u`, `d`: Passed to the `TranslateableEntity` constructor for entity ID, type ID, type, name, description, appearance, map level, position (x, y, z), actions, combat level, and hitpoints.
    - `p`: Sets `_type` (player-specific type).
    - `f`: Sets `_mentalClarityType`.
  - Calls the `TranslateableEntity` constructor with the first 13 parameters.
  - Initializes `_type` and `_mentalClarityType` with the provided values.

## Rendering
- **`draw(e, t, i)`**:
  - Checks if the player has moved significantly (more than 4 units in X or Z from `_lastGamePosition` to `_currentGamePosition`).
    - If true, snaps the `_appearance` position to `_currentGamePosition` (offset by 0.5 for centering) and sets the Y-position using `WorldMapManager.Instance.getGroundHeightAtEntity`.
  - Calls the parent `TranslateableEntity`’s `draw` method to handle standard rendering, movement animations, and UI overlay updates.

## Updates
- **`update(e)`**:
  - Calls the parent `TranslateableEntity`’s `update` method to handle state updates, position changes, and UI overlay updates.
  - Does not add player-specific update logic, relying on inherited behavior.

## Destruction
- **`destroy()`**:
  - Cleans up player-specific resources:
    - Destroys and nullifies `_onEquipItemListener` and `_onUnequipItemListener` if they exist.
    - Destroys and nullifies `_uiOverlayController`, logging specific messages for player entities (e.g., whether the controller was already destroyed).
    - Destroys and nullifies `_hitpoints`.
    - Nullifies `_mentalClarityType`.
  - Calls the parent `TranslateableEntity`’s `destroy` method to clean up inherited properties and listeners.

## Technical Observations
- **Minimal Specialization**: The class adds minimal new functionality, primarily refining movement snapping in `draw` and cleaning up player-specific resources in `destroy`.
- **Rendering Optimization**: The snapping logic in `draw` prevents visual jittering for large movements, indicating attention to smooth player rendering.
