# GameActionFactory Documentation

## Overview
The `GameActionFactory` class is a static utility singleton in the HighSpell game responsible for creating `GameAction` objects that encapsulate various game events, such as player movement, spell casting, inventory interactions, and server-client communications. Each action is associated with a specific `GameActionsEnum` value and structured data, which is sent via `SocketManager` to the server for validation and processing. This class is central to the game’s client-server architecture, ensuring standardized action payloads.


## Usefulness for Botting
The `GameActionFactory` class is a critical component for bot development due to its role in generating standardized network packets for nearly all player actions, from movement (`createPlayerMoveToAction`) to spell casting (`createCastTeleportSpellAction`, `createCastInventorySpellAction`) and inventory management (`createInvokeInventoryItemActionAction`). By replicating these method calls with appropriate parameters (e.g., entity IDs, coordinates, or item IDs), bots can simulate complex player behaviors, such as navigating to specific locations, interacting with entities, or manipulating inventory. The class’s integration with `SocketManager` allows bots to send these actions to the server, mimicking legitimate client behavior. A vast majority of the creation methods are for packets that are recieved rather than sent and as such are never used in the code. However, they show how the packet should be structured. Such automation uses very little of the clients input controls, making it more complex and easier to make mangled packets that don't actually work. Depending on your ease of use and risk tolerance you could create packets and use the `emitpacket` directly, or use the clients input management.

## Key Features
- **Singleton Pattern**: Ensures a single instance via the `Instance` getter.
- **Action Creation**: Generates `GameAction` objects with specific `GameActionsEnum` types and structured data arrays.
- **Network Integration**: Produces packets for server communication via `SocketManager`.
- **Comprehensive Coverage**: Supports actions for movement, combat, skilling, inventory, trading, and more.

## Static Methods

### Singleton Access
- **`Instance` (getter)**:
  - Returns the singleton instance, creating a new `GameActionFactory` if `_gameActionFactory` is undefined.

### Generic Action Creation
- **`createGameAction(e, t)`**:
  - Creates a generic `GameAction` with type `e` (`GameActionsEnum`) and data `t`.

### Game State and Updates
- **`createGameStateUpdateAction(e)`**:
  - Creates a `GameStateUpdate` action with an array of game actions (`Name`, `Data`).
- **`createInGameHourChangedAction(e)`**:
  - Notifies a change in the in-game hour (`CurrentHour`).

### Movement and Positioning
- **`createPlayerMoveToAction(e, t, i)`**:
  - Moves a player to coordinates (`t`, `i`) with `EntityID` `e`.
- **`createNPCMoveToAction(e, t, i)`**:
  - Moves an NPC to coordinates (`t`, `i`) with `EntityID` `e`.
- **`createSendMovementPathAction(e, t)`**:
  - Sends a movement path to coordinates (`e`, `t`).
- **`createTeleportToAction(e, t, i, n, r, s, a)`**:
  - Teleports an entity (`e`, type `t`) to coordinates (`i`, `n`, map level `r`) with type `s` and optional `SpellID` `a`.

### Entity Chunk Management
- **`createPlayerEnteredChunkAction(e, t, i, n, r, s, a, o, l, h, c, u, d, p)`**:
  - Notifies a player entering a chunk, including appearance (`c`), position, and stats.
- **`createNPCEnteredChunkAction(e, t, i, n, r, s, a, o)`**:
  - Notifies an NPC entering a chunk with position and hitpoints.
- **`createItemEnteredChunkAction(e, t, i, n, r, s, a)`**:
  - Notifies a ground item entering a chunk with `ItemID`, `Amount`, and position.
- **`createEntityExitedChunkAction(e, t)`**:
  - Notifies an entity exiting a chunk.
- **`createIEnteredChunkAction(e, t)`**:
  - Notifies the main player entering a chunk at center coordinates (`e`, `t`).

### Combat and Damage
- **`createShowDamageAction(e, t, i)`**:
  - Displays damage (`i`) from sender (`e`) to receiver (`t`).
- **`createIncreasedCombatExpAction(e, t)`**:
  - Grants combat experience for style `e` and damage `t`.
- **`createPlayerDiedAction(e, t)`**:
  - Notifies a player’s death with optional PKer ID `t`.
- **`createFiredProjectileAction(e, t, i, n, r, s, a)`**:
  - Fires a projectile (`e`) from ranger (`t`, type `i`) to target (`n`, type `r`) with damage `s` and confusion state `a`.

### Inventory and Item Actions
- **`createUseItemOnEntityAction(e, t, i)`**:
  - Uses item `e` on entity (`i`, type `t`).
- **`createCreateItemAction(e, t, i)`**:
  - Creates item `e` with amount `t` in menu `i`.
- **`createCreatedItemAction(e, t, i)`**:
  - Confirms creation of item `e` with amount `t` and recipe removals `i`.
- **`createInvokeInventoryItemActionAction(e, t, i, n, r, s)`**:
  - Invokes item action `e` on item (`n`, amount `r`, IOU `s`) in menu `t` at slot `i`.
- **`createInvokedInventoryItemActionAction(e, t, i, n, r, s, a, o)`**:
  - Confirms invoked item action with success `a` and optional data `o`.
- **`createUseItemOnItemAction(e, t, i, n, r, s, a, o, l)`**:
  - Uses item (`i`, IOU `n`) at slot `t` on item (`s`, IOU `a`) at slot `r` in menu `e` with result index `o` and amount `l`.
- **`createUsedItemOnItemAction(e, t, i, n, r, s, a, o, l, h)`**:
  - Confirms item-on-item action with success `h`.
- **`createRemovedItemAtInventorySlotAction(e, t, i, n, r, s)`**:
  - Removes item (`i`, amount `n`, IOU `r`) from slot `t` in menu `e` with remaining amount `s`.
- **`createAddedItemAtInventorySlotAction(e, t, i, n, r, s)`**:
  - Adds item (`i`, amount `n`, IOU `r`) to slot `t` in menu `e` with previous amount `s`.
- **`createReorganizeInventorySlotsAction(e, t, i, n, r, s, a, o)`**:
  - Reorganizes inventory slots (`t` to `r`) with items (`i`, IOU `n`; `s`, IOU `a`) in menu `e` with type `o`.
- **`createReorganizedInventorySlotsAction(e, t, i, n, r, s, a, o, l)`**:
  - Confirms slot reorganization with success `l`.

### Skilling and Resources
- **`createStartedSkillingAction(e, t, i, n)`**:
  - Starts skilling (`i`) on target `t` (type `n`) by player `e`.
- **`createStoppedSkillingAction(e, t, i)`**:
  - Stops skilling (`t`) by player `e` with exhaustion flag `i`.
- **`createObtainedResourceAction(e)`**:
  - Notifies obtaining resource `e`.
- **`createEntityExhaustedResourcesAction(e, t, i)`**:
  - Notifies exhaustion of resources (`e`) with chunk snapshot flag `t` and optional chunk center `i`.
- **`createEntityReplenishedResourcesAction(e)`**:
  - Notifies replenishment of resource `e`.
- **`createGainedExpAction(e, t)`**:
  - Grants experience `t` for skill `e`.

### Spell Casting
- **`createCastTeleportSpellAction(e)`**:
  - Casts teleport spell `e`.
- **`createCastedTeleportSpellAction(e, t, i)`**:
  - Confirms teleport spell `i` cast by entity (`e`, type `t`).
- **`createCastInventorySpellAction(e, t, i, n, r)`**:
  - Casts inventory spell `e` on item (`n`, IOU `r`) in menu `t` at slot `i`.
- **`createCastedInventorySpellAction(e, t, i, n)`**:
  - Confirms inventory spell `i` cast by entity (`e`, type `t`) on item `n`.
- **`createCastSingleCombatOrStatusSpellAction(e, t, i)`**:
  - Casts combat or status spell `e` on target (`t`, type `i`).
- **`createCastedSingleCombatOrStatusSpellAction(e, t, i, n, r, s, a)`**:
  - Confirms combat/status spell `e` cast by (`t`, type `i`) on target (`n`, type `r`) with damage `s` and confusion `a`.
- **`createToggleAutoCastAction(e)`**:
  - Toggles auto-casting for spell `e` (or `null` to disable).
- **`createToggledAutoCastAction(e)`**:
  - Confirms auto-cast toggle for spell `e`.

### Trading and Banking
- **`createStartedBankingAction(e, t)`**:
  - Starts banking for player `e` at bank `t`.
- **`createStoppedBankingAction(e)`**:
  - Stops banking for player `e`.
- **`createReceivedBankitemsAction(e)`**:
  - Updates bank items with array `e`.
- **`createTradeRequestedAction(e, t)`**:
  - Requests trade between players `e` and `t`.
- **`createPlayerAcceptedAction(e)`**:
  - Confirms player `e` accepted a trade.
- **`createTradeStartedAction(e, t)`**:
  - Starts trade between players `e` and `t`.
- **`createTradeCancelledAction(e, t, i)`**:
  - Cancels trade between players `e` and `t` with reason `i`.
- **`createTradeCompletedAction(e, t, i)`**:
  - Completes trade between players `e` and `t` with inventory `i`.
- **`createTradeStatusResetAction()`**:
  - Resets trade status.
- **`createTradeGoToFinalStepAction()`**:
  - Advances trade to the final step.
- **`createInsertAtBankStorageSlotAction(e, t)`**:
  - Inserts item at bank slot `e` to `t`.
- **`createInsertedAtBankStorageSlotAction(e)`**:
  - Confirms bank slot insertion with result `e`.

### Other Actions
- **`createPlayerSkillLevelIncreasedAction(e, t, i, n)`**:
  - Notifies skill `t` level increase for player `e` with levels gained `i` and new level `n`.
- **`createPlayerCombatLevelIncreasedAction(e, t)`**:
  - Notifies combat level increase for player `e` to level `t`.
- **`createCookedItemAction(e)`**:
  - Notifies cooking item `e`.
- **`createOvercookedItemAction(e)`**:
  - Notifies overcooking item `e`.
- **`createChangeCombatStyleAction(e, t)`**:
  - Changes combat style to `e` with selection `t`.
- **`createCombatStyleChangedAction(e)`**:
  - Confirms combat style change to `e`.
- **`createChangeAutoRetaliateAction(e)`**:
  - Toggles auto-retaliate to `e`.
- **`createAutoRetaliateChangedAction(e)`**:
  - Confirms auto-retaliate change to `e`.
- **`createToggleSprintAction(e)`**:
  - Toggles sprinting to `e`.
- **`createToggledSprintAction(e, t, i)`**:
  - Confirms sprint toggle for entity (`e`, type `t`) with state `i`.
- **`createRestoredStatsAction(e)`**:
  - Restores stats for skills `e`.
- **`createShookTreeAction(e, t, i)`**:
  - Notifies tree shaking by entity (`e`, type `t`) on tree `i`.
- **`createShakeTreeResultMessageAction(e, t)`**:
  - Notifies tree shake result with item `e` and rare flag `t`.
- **`createOpenedSkillingMenuAction(e, t)`**:
  - Opens skilling menu for target `e` with menu type `t`.
- **`createWentThroughDoorAction(e, t)`**:
  - Notifies entity `t` going through door `e`.
- **`createSkillCurrentLevelChangedAction(e, t)`**:
  - Notifies skill `e` current level change to `t`.
- **`createServerInfoMessageAction(e, t)`**:
  - Displays server message `e` with style `t`.
- **`createForcePublicMessageAction(e, t, i)`**:
  - Forces public message `i` from entity (`e`, type `t`).
- **`createQuestProgressedAction(e, t)`**:
  - Notifies quest `e` progress to checkpoint `t`.
- **`createCreatedUseItemOnItemActionItemsAction(e, t, i)`**:
  - Notifies item-on-item action with items `e`, `t`, and result index `i`.
- **`createPathfindingFailedAction(e)`**:
  - Notifies pathfinding failure for entity `e`.
- **`createServerShutdownCountdownAction(e)`**:
  - Notifies server shutdown in `e` minutes.
- **`createCanLoginAction()`**:
  - Notifies login permission.
- **`createReconnectToServerAction(e, t)`**:
  - Reconnects with username `e` and session ID `t`.
- **`createEntityStunnedAction(e, t, i)`**:
  - Notifies entity (`e`, type `t`) stunned for `i` ticks.
- **`createGlobalPublicMessageAction(e, t, i, n)`**:
  - Sends global public message `i` from entity `e` with name `t` and player type `n`.
- **`createHealthRestoredAction(e, t, i)`**:
  - Restores health to `i` for entity (`t`, type `e`).
- **`createPlayerCountChangedAction(e)`**:
  - Notifies player count change to `e`.
- **`createForcedSkillCurrentLevelChangedAction(e, t, i)`**:
  - Forces skill `e` current level to `t` with message flag `i`.
- **`createReceivedNPCConversationDialogue(e, t, i, n, r, s)`**:
  - Receives NPC conversation dialogue with ID `t`, dialogue ID `i`, text `r`, and options `s`.
- **`createSelectNPCConversationOption(e)`**:
  - Selects NPC conversation option `e`.
- **`createEndedNPCConversationAction(e)`**:
  - Ends NPC conversation for entity `e`.
- **`createPlayerInfoAction(e)`**:
  - Sends player info `e`.
- **`createCaptchaActionAction(e, t)`**:
  - Submits captcha action type `e` with guess `t`.
- **`createOpenedCaptchaScreenAction(e)`**:
  - Opens captcha screen for entity `e`.
- **`createReceivedCaptchaAction(e, t, i)`**:
  - Receives captcha with image URL `e`, width `t`, and height `i`.
- **`createCaptchaActionResultAction(e)`**:
  - Notifies captcha action result with type `e`.
- **`createMentalClarityChangedAction(e, t, i)`**:
  - Notifies mental clarity change for entity `e` from `t` to `i`.
- **`createMutedPlayerAction(e, t)`**:
  - Mutes or unmutes player `e` with state `t`.

## Technical Observations
- **Dependencies**: Relies on `GameAction`, `SocketManager`, `SkillEnum`, and various enum mappings for action data.
- **Data Structure**: Uses fixed-size arrays with enum-indexed fields for efficient serialization.
- **Network Integration**: All actions are designed for server communication via `SocketManager`, ensuring client-server synchronization.
- **Comprehensive Action Set**: Covers gameplay aspects like movement, combat, skilling, trading, and administrative functions (e.g., captcha, moderation).

## Ethical and Legal Notes
Per the HighSpell Botting Resources ethos:
- This documentation is for educational purposes, analyzing observable game client behavior.
- Using this information to create bots violates HighSpell’s terms of service, risking account bans.