# MainPlayer Documentation

## Overview
The `MainPlayer` class extends the `PlayerEntity` class and represents the primary player character in the HighSpell game. It builds on the capabilities of `PlayerEntity` and `TranslateableEntity`, adding specific functionality for managing the player’s combat stats, skills, inventory, bank storage, quests, and session-related data. This class is central to the game’s client-side logic, handling the player’s interactions with the game world.

## Usefulness for botting
A `MainPlayer` is the core of your player character, serving as the foundation for all essential data. It stores information like `_skills` and `_combat`, which track your skill levels, total XP, and other details for both Combat and Non-Combat skills. Additionally, it manages your state, including `_isMoving`, and your `_inventory`, which contains an array, `_items`, of `InventoryItem` objects. Each `InventoryItem` includes the `ItemDef` for an item along with all its relevant details, making automated inventory actions seamless. This structure also applies to the player's `_bankItems`.


## Inheritance
- **Extends**: `PlayerEntity`
  - Inherits properties and methods from `PlayerEntity` (e.g., `_type`, `_mentalClarityType`), `TranslateableEntity` (e.g., `_combatLevel`, `_hitpoints`, `teleportTo`), and indirectly from `Entity` (e.g., `_entityId`, `_appearance`).
  - Calls the parent constructor with core parameters and additional player-specific arguments.

## Key Properties
The `MainPlayer` class defines additional properties, accessed via getters:

- **`_combat`**: An object managing the player’s combat-related stats, passed from the constructor.
- **`_skills`**: An object managing the player’s non-combat skills, passed from the constructor.
- **`_inventory`**: An `ItemInventory` object for the player’s inventory, initialized with `MenuTypesEnum.Inventory`.
- **`_bankItems`**: An `ItemInventory` object for the player’s bank storage, initialized with `MenuTypesEnum.Bank`.
- **`_quests`**: An object managing the player’s quests, passed from the constructor.
- **`_playerSessionId`**: A unique identifier for the player’s session.
- **`_chatToken`**: A token used for chat functionality.
- **`_didGetBankStorageItems`**: A boolean tracking whether bank items have been initialized.
- **`_onReceivedBankItemsListeners`**: A `Map` storing callbacks for bank item initialization events.

## Constructor
- **Signature**: `constructor(e, t, i, n, r, s, a, o, l, h, c, u, d, p, f, _, m, g, v)`
  - Parameters:
    - `e`, `t`, `i`, `n`, `r`, `s`, `a`, `o`, `l`, `h`, `c`, `u`, `d`, `p`: Passed to the `PlayerEntity` constructor for entity ID, type ID, type, name, description, appearance, map level, position (x, y, z), actions, combat level, hitpoints, player type.
    - `f`: Combat stats object, used to extract hitpoints for the parent constructor and set `_combat`.
    - `_`: Sets `_skills`.
    - `m`: Sets `_quests`.
    - `g`: Sets `_playerSessionId`.
    - `v`: Sets `_chatToken`.
  - Calls the `PlayerEntity` constructor with the first 14 parameters and the hitpoints derived from `f.getSkill(SkillEnum.hitpoints)`.
  - Initializes `_inventory` and `_bankItems` as `ItemInventory` objects with specific menu types.
  - Sets `_didGetBankStorageItems` to `false` and creates `_onReceivedBankItemsListeners` as a `Map`.

## Bank Item Management
- **`setBankItems(e)`**:
  - Initializes `_bankItems` with the provided items (`e`) if not already initialized (`_didGetBankStorageItems` is `false`).
  - Sets `_didGetBankStorageItems` to `true` and invokes all callbacks in `_onReceivedBankItemsListeners` with the items.
- **`addReceivedBankItemsListener(e, t)`**:
  - Adds a callback (`t`) to `_onReceivedBankItemsListeners` with key `e` if not already present.
  - Returns `false` (potentially a bug, as it does not indicate success).
- **`removeReceivedBankitemsListener(e)`**:
  - Removes the callback with key `e` from `_onReceivedBankItemsListeners`.
  - Returns `true` if the callback was removed, `false` otherwise.

## Teleportation
- **`teleportTo(e, t, i)`**:
  - Calls the parent `TranslateableEntity`’s `teleportTo` to update position and map level.
  - If the map level changes (`i !== _currentMapLevel`), triggers `WR.Instance.enterMapLevel` and `WorldMapManager.Instance.enterMapLevel`.
  - Also triggers `WorldMapManager.Instance.enterMapLevel` if the new position is in a new chunk.
  - Updates the minimap via `SerivceLocator.Manager.getController().MinimapQuadrantController.MinimapController.mainPlayerMoved`.
  - Forces a wilderness position check via `SerivceLocator.Manager.getController().WildernessUIController.forceCheckWildernessPosition`.

## Stat Restoration
- **`restoreStatsByOne(e)`**:
  - Iterates through an array of skill IDs (`e`) to restore stats:
    - Retrieves the skill from `_combat.Skills` (if `RA(e[i])` is true) or `_skills.Skills`.
    - Increases `CurrentLevel` by 1 if below `Level`, or decreases by 1 if above `Level`.
  - Logs the restoration action to the console.

## Updates
- **`update(e)`**:
  - Overrides the parent’s `update` method to update `_currentState`, call `_updateGamePosition`, and update `_uiOverlayController` if it exists.
  - Reimplements `TranslateableEntity`’s `update` logic without additional player-specific behavior.

## Destruction
- **`destroy()`**:
  - Cleans up player-specific resources:
    - Clears and nullifies `_onReceivedBankItemsListeners`.
    - Destroys and nullifies `_uiOverlayController`, logging messages for player entities.
    - Destroys and nullifies `_combat`, `_skills`, `_inventory`, and `_quests`.
  - Calls the parent `PlayerEntity`’s `destroy` method to clean up inherited properties and listeners.

## Technical Observations
- **Event System**: The `_onReceivedBankItemsListeners` Map supports asynchronous bank item loading, suggesting networked data retrieval.
- **Potential Bug**: The `addReceivedBankItemsListener` method always returns `false`, which may confuse callers expecting a success indicator.

## Ethical and Legal Notes
Per the HighSpell Botting Resources ethos:
- This documentation is for educational purposes, analyzing observable game client behavior.
- Using this information to create bots violates HighSpell’s terms of service, risking account bans.