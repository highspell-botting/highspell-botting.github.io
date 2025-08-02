# ItemManager Documentation

## Overview
The `ItemManager` class is a singleton in the HighSpell game responsible for managing player interactions with inventory items, including selecting, using, equipping, and performing actions like dropping or trading. It coordinates item-related actions, emits network packets for server-side validation, and handles UI interactions such as confirmation dialogs and text input menus. This class is central to the game’s inventory and item interaction system, working closely with classes like `MainPlayer` and `TargetActionManager`.

## Usefulness for Botting
The `ItemManager` class provides access to the currently selected item (`CurrentSelectedItem`), item action states (`IsAwaitingItemAction`), and methods for invoking actions like `use`, `equip`, or `drop`. It exposes methods to emit network packets for item actions (`emitInventoryItemActionPacket`, `emitUseItemOnItemActionPacket`), enabling bots to automate inventory interactions, such as equipping items, combining items, or dropping items, based on their definitions and slots. The class’s integration with `MenuTypesEnum` and `ItemActionEnum` allows precise targeting of inventory or bank items.

## Key Features
- **Singleton Pattern**: Ensures a single instance via the `Instance` getter.
- **Item Selection**: Manages the currently selected item and its UI state.
- **Action Handling**: Processes item actions (e.g., equip, use, drop) with server communication.
- **UI Integration**: Coordinates confirmation dialogs and text input menus for item actions.
- **Event System**: Uses listeners to notify other systems of changes in item action states.

## Key Properties
- **`_itemManager` (static)**: The singleton instance of `ItemManager`.
- **`_currentSelectedItem`**: The currently selected item, stored as an `AV` object (menu type, item, control, slot).
- **`_shouldWithdrawBankItemsAsIOU`**: A boolean indicating whether bank items should be withdrawn as IOUs (getter and setter).
- **`_isAwaitingItemAction`**: A boolean indicating if an item action is pending server response (getter and setter, triggers `_onIsAwaitingItemActionChangedEventListener`).
- **`_onIsAwaitingItemActionChangedEventListener`**: A `Listeners` object for notifying changes in `_isAwaitingItemAction`.
- **`_didLoad`**: A boolean tracking whether the manager has loaded item and shop definitions.

## Constructor
- **Signature**: `constructor()`
  - Binds callback methods (`_handleSelectAnItemOptionMenuCallback`, `_handleCreateItemsTextInputMenuSubmitted`, `_handleConfirmInventoryActionResult`).
  - Initializes `_didLoad` to `false`, `_currentSelectedItem` to `null`, `_shouldWithdrawBankItemsAsIOU` to `false`, `_isAwaitingItemAction` to `false`, and `_onIsAwaitingItemActionChangedEventListener` as a new `Listeners` object.

## Static Methods
- **`Instance` (getter)**:
  - Returns the singleton instance, creating a new `ItemManager` if `_itemManager` is undefined.

## Instance Methods
- **`loadAsync(e, t, i)`**:
  - **Parameters**:
    - `e`: Unused (likely a context or callback).
    - `t`: Path or identifier for item definition data.
    - `i`: Path or identifier for shop definition data.
  - **Functionality**:
    - Warns if already loaded (`_didLoad` is `true`).
    - Sets `_didLoad` to `true`.
    - Asynchronously loads item definitions (`ItemDefs.loadFromJSON`) and shop definitions (`ShopDefs.loadFromJSON`) using `AssetManager.getDefinitionData`.
    - Returns a `Promise` for asynchronous execution.

- **`invokeInventoryAction(e, t, i, n)`**:
  - **Parameters**:
    - `e`: Menu type (e.g., `MenuTypesEnum.Inventory`).
    - `t`: Action type (e.g., `ItemActionEnum.use`).
    - `i`: Inventory slot index.
    - `n`: The `InventoryItem` object.
  - **Functionality**:
    - Exits if `_isAwaitingItemAction` is `true`.
    - If an item is selected and the action is valid, performs an item-on-item action or unselects the item.
    - Handles actions based on `t`:
      - `use`: Selects the item for further interaction.
      - `create`: Emits a `createCreateItemAction` packet.
      - `equip`: Checks requirements via `_hasRequirementsToEquipItem` and emits an action packet.
      - `discard`: Shows a confirmation dialog before emitting the action.
      - Other actions (e.g., `offer`, `drop`, `eat`): Emits an action packet directly.

- **`emitCreateItemActionPacket(e, t, i)`**:
  - Emits a `createCreateItemAction` packet via `SocketManager` with menu type, item ID, and amount. See `MenuTypesEnum`.

- **`emitUseItemOnItemActionPacket(e, t, i, n, r, s, a)`**:
  - Sets `_isAwaitingItemAction` to `true` and emits a `createUseItemOnItemAction` packet with menu type, slots, item details, and quantity.

- **`emitInventoryItemActionPacket(e, t, i, n, r, s)`**:
  - Sets `_isAwaitingItemAction` to `true` and emits a `createInvokeInventoryItemActionAction` packet with action, menu type, slot, item ID, amount, and IOU status.

- **`emitReorganizeInventorySlotsPacket(e, t, i, n, r, s, a, o)`**:
  - Sets `_isAwaitingItemAction` to `true` and emits a `createReorganizeInventorySlotsAction` packet for inventory slot reorganization.

- **`selectItem(e, t, i)`**:
  - Selects an item from the inventory UI, creating an `AV` object and marking the item’s UI control as selected.

- **`unselectItem()`**:
  - Unselects the current item, clearing its UI state and destroying the `AV` object.

- **`_handleItemOnItemAction(e, t, i, n, r, s, a)`**:
  - Handles combining two items, checking if the action is valid via `SP.get`. Shows a selection menu or text input for quantity if needed, or emits a `useItemOnItem` packet.

- **`_handleSelectAnItemOptionMenuCallback(e, t)`**:
  - Processes the selection of an item combination result and calls `_handleItemOnItemAction`.

- **`_handleCreateItemsTextInputMenuSubmitted(e, t)`**:
  - Parses the quantity input and emits a `useItemOnItem` packet if valid.

- **`_canPlayerCreateItem(e, t, i)`**:
  - Checks if the player can create an item based on skill requirements (e.g., `CraftingHandler`, `PotionMakingHandler`).

- **`useItemOnEntity(e, t)`**:
  - Uses an item on an entity via `TargetActionManager.handleItemOnEntityAction` and unselects the item.

- **`_hasRequirementsToEquipItem(e, t)`**:
  - Verifies if the player meets the skill requirements to equip an item, showing a chat message if requirements are unmet.

- **`handleLookedAtItem(e, t)`**:
  - Handles the `look_at` action for specific item types (e.g., treasure maps), showing a UI menu.

- **`_confirmInventoryActionBeforeInvoking(e, t, i, n, r, s, a, o, l, h)`**:
  - Shows a confirmation dialog for actions like `discard` using `ScreenMaskController`.

- **`_handleConfirmInventoryActionResult(e, t)`**:
  - Processes confirmation dialog results, emitting an action packet if confirmed.

- **`reset()`**:
  - Clears `_currentSelectedItem`, `_shouldWithdrawBankItemsAsIOU`, `_isAwaitingItemAction`, and reinitializes `_onIsAwaitingItemActionChangedEventListener`.

## Technical Observations
- **Dependencies**: Integrates with UI controllers (`GameMenuBarController`, `ChatMenuController`, `ScreenMaskController`), network systems (`SocketManager`), and skill/item handlers.
- **Singleton Pattern**: Ensures a single instance for global item management.
- **Event-Driven Design**: Uses `_onIsAwaitingItemActionChangedEventListener` for state changes and callbacks for UI interactions.
- **Network Integration**: Emits packets for all actions, ensuring server-side validation.
- **Asynchronous Loading**: Uses `loadAsync` to load item and shop definitions, suggesting networked or asset-based initialization.

## Ethical and Legal Notes
Per the HighSpell Botting Resources ethos:
- This documentation is for educational purposes, analyzing observable game client behavior.
- Using this information to create bots violates HighSpell’s terms of service, risking account bans.