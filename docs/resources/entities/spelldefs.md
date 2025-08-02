# SpellDef and SpellDefs Documentation

## Overview
The `SpellDef` class represents a single spell definition in the HighSpell game, encapsulating properties like ID, name, type, level, experience, damage, and requirements. The `SpellDefs` class is a static utility that manages a collection of spell definitions, providing methods to load and retrieve them from JSON data. These classes are integral to the game’s magic system, used by classes like `CW` (SpellManager) to handle spell-related actions.

## Usefulness for Botting
The `SpellDef` class provides detailed metadata about spells, including their `Type`, `Requirements`, and `Actions`, enabling bots to select and cast spells based on their properties (e.g., targeting `combat` spells for damage or `teleport` spells for movement). The `SpellDefs` class allows access to all spell definitions via `getDefById`, facilitating automation of spell selection and usage. For example, a bot could query spell requirements to ensure the player meets them before casting.

## SpellDef Class

### Inheritance
- **None**: `SpellDef` is a standalone class, not extending any parent class.

### Key Properties
- **`_id` (getter: `ID`)**: The unique identifier of the spell.
- **`_name` (getter: `Name`)**: The spell’s name, capitalized for display.
- **`_description` (getter: `Description`)**: The spell’s description.
- **`_type` (getter: `Type`)**: The spell type (e.g., `SpellTypeEnum.combat`, `SpellTypeEnum.teleport`).
- **`_level` (getter: `Level`)**: The required Magic skill level to cast the spell.
- **`_exp` (getter: `Exp`)**: The experience points granted for casting the spell.
- **`_maxDamage` (getter: `MaxDamage`)**: The maximum damage for combat spells (0 for non-combat spells).
- **`_splashDamage` (getter: `SplashDamage`)**: The splash damage properties for area effects.
- **`_recipe` (getter: `Recipe`)**: The items or resources required to cast the spell.
- **`_actions` (getter: `Actions`)**: The available actions (e.g., `TargetAction.cast`, `TargetAction.auto_cast`).
- **`_requirements` (getter: `Requirements`)**: The skill or item requirements for casting.

### Constructor
- **Signature**: `constructor(e, t, i, n, r, s, a, o, l, h)`
  - **Parameters**:
    - `e`: Spell ID (`_id`).
    - `t`: Spell name (`_name`).
    - `i`: Spell description (`_description`).
    - `n`: Spell type (`_type`, from `SpellTypeEnum`).
    - `r`: Required level (`_level`).
    - `s`: Experience points (`_exp`).
    - `a`: Maximum damage (`_maxDamage`).
    - `o`: Splash damage (`_splashDamage`).
    - `l`: Recipe (`_recipe`).
    - `h`: Requirements (`_requirements`).
  - **Functionality**:
    - Initializes all properties with provided values.
    - Sets `_actions` based on `_type`:
      - `inventory`, `status`, `teleport`: `[TargetAction.cast]`.
      - `combat`: `[TargetAction.cast, TargetAction.auto_cast]`.

### Static Methods
- **`fromJSON(e)`**:
  - **Parameters**:
    - `e`: JSON object containing spell data.
  - **Functionality**:
    - Validates numeric fields (`_id`, `lvl`, `exp`, `maxDamage`) using `HighSpellUtil.isNumberSafeAndPositive`.
    - Converts `type` to `SpellTypeEnum` using `JF` (likely a type mapping).
    - Maps `requirements` to objects using `tM.fromJSON`.
    - Creates a `SpellDef` instance with parsed data, including `SpellSplashDamage.fromJSON` and `_P.fromJSON` for splash damage and recipe.
    - Throws an error if validation or conversion fails.

### Instance Methods
- **`destroy()`**:
  - **Functionality**:
    - Nullifies all properties (`_id`, `_name`, `_description`, `_type`, `_level`, `_exp`, `_maxDamage`).
    - Destroys and nullifies `_splashDamage` and `_recipe` if they exist.
    - Nullifies each element in `_actions` and `_requirements`, then nullifies the arrays.

## SpellDefs Class

### Inheritance
- **None**: `SpellDefs` is a static utility class, not instantiated.

### Key Properties
- **`_defMap` (static, getter: `Defs`)**: A `Map` storing spell definitions, with spell IDs as keys and `SpellDef` instances as values.

### Constructor
- **Signature**: `constructor()`
  - Empty constructor, as the class is static and not instantiated.

### Static Methods
- **`loadFromJSON(e)`**:
  - **Parameters**:
    - `e`: Array of JSON objects containing spell definitions.
  - **Functionality**:
    - Warns if `_defMap` is already initialized.
    - Creates a new `_defMap` and populates it with `SpellDef` instances created from JSON data.
    - Throws errors if JSON is invalid or if duplicate spell IDs are found.

- **`getDefById(e)`**:
  - **Parameters**:
    - `e`: Spell ID.
  - **Functionality**:
    - Retrieves a `SpellDef` from `_defMap` by ID, returning `undefined` if not found.

## Technical Observations
- **Dependencies**: `SpellDef` relies on `SpellTypeEnum`, `TargetAction`, and other classes (`tM`, `_P`, `SpellSplashDamage`) for requirements and recipes. `SpellDefs` integrates with `AssetManager` (implied by JSON loading).
- **Data Management**: `SpellDefs` uses a `Map` for efficient lookup, and `fromJSON` ensures data integrity with validation checks.
- **Network Integration**: Spell definitions are loaded from server-provided JSON, as seen in `ItemManager.loadAsync`, suggesting networked initialization.

## Ethical and Legal Notes
Per the HighSpell Botting Resources ethos:
- This documentation is for educational purposes, analyzing observable game client behavior.
- Using this information to create bots violates HighSpell’s terms of service, risking account bans.