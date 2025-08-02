# SpellManager Documentation

## Overview
The `SpellManager` class is a singleton in the HighSpell game responsible for managing spell-related actions, including selecting, casting, and auto-casting spells. It handles different spell types (teleport, inventory, status, combat) and coordinates their effects on entities or items, integrating with the game’s UI, networking, and skill systems. The class is critical for implementing the game’s magic mechanics, interacting with classes like `EntityManager`, `SkillManager`, and `TargetActionManager`.


## Usefulness for Botting
The `SpellManager` class provides access to the currently selected spell (`CurrentlySelectedSpell`) and methods to cast spells on items (`castSpellOnItem`) or entities (`castSpellOnEntity`), as well as toggle auto-casting (`startAutoCastingSpell`, `stopAutoCastingSpell`). It exposes network packet emission for spell actions, enabling bots to automate spell casting, such as targeting specific entities or items based on their IDs and types. The class’s integration with `SpellTypeEnum` and `SkillEnum` allows precise control over magic-related actions.

## Key Features
- **Singleton Pattern**: Ensures a single instance via the `Instance` getter.
- **Spell Selection**: Manages the currently selected spell and its UI state.
- **Spell Casting**: Handles casting for teleport, inventory, status, and combat spells, with server communication.
- **Auto-Casting**: Supports enabling and disabling auto-casting for combat spells.
- **Event System**: Uses listeners to notify other systems of spell selection changes.
- **Skill Integration**: Updates player experience (e.g., Magic, Hitpoints) when spells are cast.

## Key Properties
- **`_spellManager` (static)**: The singleton instance of `SpellManager`.
- **`_currentlySelectedSpell`**: The currently selected spell, stored as a `KG` object (getter and setter, triggers `_spellSelectedListener`).
- **`_spellSelectedListener`**: A `Listeners` object for notifying spell selection changes (getter only).
- **`IsSpellCurrentlySelected` (getter)**: Returns `true` if a spell is selected.

## Constructor
- **Signature**: `constructor()`
  - Initializes `_spellSelectedListener` as a new `Listeners` object.

## Static Methods
- **`Instance` (getter)**:
  - Returns the singleton instance, creating a new `SpellManager` if `_spellManager` is undefined.

## Instance Methods
- **`selectSpell(e, t)`**:
  - **Parameters**:
    - `e`: The spell object (with `Type`, `ID`, and `QuickActionText`).
    - `t`: The action type (e.g., `TargetAction.cast`).
  - **Functionality**:
    - Handles spell selection based on `e.Type`:
      - `SpellTypeEnum.teleport`: Casts the spell immediately via `castTeleportSpell`.
      - `SpellTypeEnum.inventory`: Selects the spell and opens the `Inventory` menu.
      - `SpellTypeEnum.status`: Selects the spell.
      - `SpellTypeEnum.combat`: Selects the spell if `t` is `TargetAction.cast`.
    - Updates the quick action text via `vW.setQuickActionText` for non-teleport spells.

- **`startAutoCastingSpell(e)`**:
  - Emits a `createToggleAutoCastAction` packet with the spell’s `ID` to enable auto-casting.

- **`stopAutoCastingSpell()`**:
  - Emits a `createToggleAutoCastAction` packet with `null` to disable auto-casting.

- **`castTeleportSpell(e)`**:
  - Emits a `createCastTeleportSpellAction` packet for a teleport spell if `e.Type` is `SpellTypeEnum.teleport`.

- **`castSpellOnItem(e, t, i, n, r)`**:
  - Emits a `createCastInventorySpellAction` packet for an inventory spell and opens the `Magic` menu.
  - Unselects the spell afterward.

- **`castSpellOnEntity(e, t)`**:
  - Emits a `createCastSingleCombatOrStatusSpellAction` packet for combat or status spells on an entity.
  - Unselects the spell afterward.

- **`unselectSpell()`**:
  - Destroys and clears `_currentlySelectedSpell` if selected.

- **`handleEntityCastedTeleportSpell(e, t, i, n=null)`**:
  - Handles teleport spell effects, updating experience if cast by the main player and adding the spell effect via `$k.Instance.addSpell`.

- **`handleEntityCastedInventorySpell(e, t, i)`**:
  - Handles inventory spell effects, updating experience and adding the spell effect.

- **`handleEntityCastedStatusSpell(e, t, i, n)`**:
  - Handles status spell effects, updating experience and adding the spell effect.

- **`handleEntityCastedCombatSpell(e, t, i, n, r)`**:
  - Handles combat spell effects, updating Magic and Hitpoints experience and adding the spell effect.

- **`_checkIfMainPlayerCastedSpell(e, t, i, n=null, r=false)`**:
  - Checks if the main player cast the spell (`e` matches `MainPlayer.EntityID`) and updates experience for `SkillEnum.magic` and, for combat spells, `SkillEnum.hitpoints`.

- **`reset()`**:
  - Clears and destroys `_currentlySelectedSpell` and `_spellSelectedListener`, reinitializing the latter.

## Technical Observations
- **Dependencies**: Integrates with UI (`GameMenuBarController`), networking (`SocketManager`), and skill systems (`SkillManager`).
- **Singleton Pattern**: Ensures a single instance for global spell management.
- **Event-Driven Design**: Uses `_spellSelectedListener` for spell selection changes.
- **Network Integration**: Emits packets for all spell actions, ensuring server-side validation.

## Ethical and Legal Notes
Per the HighSpell Botting Resources ethos:
- This documentation is for educational purposes, analyzing observable game client behavior.
- Using this information to create bots violates HighSpell’s terms of service, risking account bans.