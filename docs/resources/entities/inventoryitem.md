# InventoryItem Documentation

## Overview
The `InventoryItem` class represents an item within a player’s inventory or bank storage in the HighSpell game. It encapsulates properties such as the item’s definition, quantity, and IOU (placeholder) status, providing methods for manipulating and serializing item data. This class is integral to the game’s inventory system, used by classes like `MainPlayer` to manage player possessions.

## Usefulness for botting
An `InventoryItem` holds some important information about the items you have available to your character. But most importantly they're passed whole to functions that act on items.

## Key Properties
The `InventoryItem` class defines properties, accessed via getters and setters:

- **`_def`**: The item’s definition, lazily loaded via `ItemDefs.getDefById(this._id)` when accessed (getter only).
- **`_amount`**: The quantity of the item (getter and setter).
- **`_isIOU`**: A boolean indicating if the item is an IOU (placeholder, e.g., for items promised but not yet delivered) (getter and setter).
- **`_id`**: The item’s unique identifier, used to fetch its definition (not directly accessible via getter/setter).

## Constructor
- **Signature**: `constructor(e, t, i)`
  - Parameters:
    - `e`: The item’s ID (`_id`), used to retrieve its definition.
    - `t`: The item’s quantity (`_amount`).
    - `i`: The IOU status (`_isIOU`), indicating if the item is a placeholder.
  - Initializes `_id`, `_amount`, and `_isIOU` with the provided values.
  - Does not immediately load `_def`, deferring it to the `Def` getter for lazy initialization.

## Methods
- **`updateAmount(e)`**:
  - Adjusts `_amount` by adding `e` (can be positive or negative).
  - Ensures `_amount` does not go below 0.
  - Returns the updated `_amount`.
- **`doesMatch(e, t)`**:
  - Checks if the item matches the given ID (`e`) and IOU status (`t`).
  - Returns `true` if both the item’s `Def.ID` and `_isIOU` match the provided values.
- **`clone()`**:
  - Creates a new `InventoryItem` instance with the same `Def.ID`, `_amount`, and `_isIOU`.
  - Useful for duplicating items without modifying the original.
- **`toSocketArray()`**:
  - Serializes the item into an array format for network transmission: `[Def.ID, _amount, _isIOU ? 1 : 0]`.
- **`toJson()`**:
  - Serializes the item into a JSON object: `{ id: Def.ID, amt: _amount, isIOU: _isIOU }`.
- **`toString()`**:
  - Returns a string representation of the item: `"[ ID: <Def.ID>, Name: <Def.Name>, Amount: _amount, IsIOU: _isIOU ]"`.
  - Uses `-1` and empty string if `Def` is not loaded.
- **`static fromPacketArray(e)`**:
  - Creates an `InventoryItem` from a network packet array.
  - Expects an array of length 2 (`[id, amount]`) or 3 (`[id, amount, isIOU]`), where `isIOU` is `1` for `true` or `0` for `false`.
  - Returns `null` if the array is invalid.
- **`static fromJson(e)`**:
  - Creates an `InventoryItem` from a JSON object with `id`, `amt`, and `isIOU` properties.
  - Returns `null` if the input is invalid.
- **`destroy()`**:
  - Cleans up the item by nullifying `_amount` and `_isIOU`.
  - Does not nullify `_id` or `_def`, as `_def` is lazily loaded and `_id` is immutable.

## Technical Observations
- **Lazy Loading**: The `_def` property is lazily loaded to optimize performance, only fetching the definition when accessed.
- **Serialization**: Supports both array-based (`toSocketArray`) and JSON (`toJson`) serialization, likely for network communication and local storage, respectively.

## Ethical and Legal Notes
Per the HighSpell Botting Resources ethos:
- This documentation is for educational purposes, analyzing observable game client behavior.
- Using this information to create bots violates HighSpell’s terms of service, risking account bans.