# GameActionsEnum Reference

Overview

The `GameActionsEnum` is an enum-like structure in the HighSpell client, defining action or event types for WebSocket communication between client and server. This document covers both the legacy system (pre-update) and the new unified action system discovered through protocol analysis.

## Important Notes

- **Protocol Change**: A major update simplified the packet structure, moving to a unified action-based system under packet type "1" for player actions.
- **Changeability**: These IDs are specific to the current client version. Major updates may reorder, add, or remove actions.
- **WebSocket Usage**: Messages use format `42["packetType", payload]` for Socket.IO communication.

## Current Protocol (Post-Update)

### Packet Structure
All player actions now use packet type `"1"` with format: `42["1", [ActionType, Parameters]]`

| Action Type | Purpose | Data Structure |
| --- | --- | --- |
| 10 | Player Movement | `[10, [x, y]]` - Move to coordinates |
| 16 | Idle State | `[16, [entityId]]` - Player entered idle state |
| 42 | Interaction | `[42, [action, target, objectId]]` - Interact with objects/NPCs |

### Other Packet Types
| Packet Type | Purpose | Data Structure |
| --- | --- | --- |
| "0" | Game State Updates | `[0, [[actionId, payload], ...]]` - Multiple state updates |
| "15" | Login Response | `[15, [entityId, ...stats, x, y, ...]]` - Initial login data |
| "18" | Entity Confirmation | `[18, [entityId]]` - Confirms player entity ID |
| "pm" | Private Message | `[pm, {type, from, msg}]` - Direct messages between players |

### Discovery Status
- ✅ **Confirmed**: Player movement (10), idle state (16), login (15), private messages (pm)  
- 🔍 **Investigating**: Interaction types, combat packets, skill updates
- ❓ **Unknown**: Many legacy action types may have been consolidated or removed

## Legacy Protocol (Pre-Update) - OBSOLETE

| ID | Action Name | Data Structure |
| --- | --- | --- |
| 0 | GameStateUpdate | Array of objects: `[{ Name, Data }]` <br> - `Name`: String name of the state update. <br> - `Data`: Associated data (format varies). <br> *Purpose*: Represents multiple game state updates (e.g., server-wide changes). |
| 1 | PlayerMoveTo | `[EntityID, X, Y]` <br> - `EntityID`: Player’s unique ID. <br> - `X`: Destination X-coordinate. <br> - `Y`: Destination Y-coordinate. <br> *Purpose*: Moves a player to a new position (e.g., via `PositionEntity`). |
| 2 | NPCMoveTo | `[EntityID, X, Y]` <br> - `EntityID`: NPC’s unique ID. <br> - `X`: Destination X-coordinate. <br> - `Y`: Destination Y-coordinate. <br> *Purpose*: Moves an NPC to a new position. |
| 3 | PlayerEnteredChunk | `[EntityID, EntityTypeID, PlayerType, Username, CombatLevel, HitpointsLevel, CurrentHitpointsLevel, MapLevel, X, Y, HairStyleID, BeardStyleID, ShirtID, BodyTypeID, LegsID, EquipmentHeadID, EquipmentBodyID, EquipmentLegsID, EquipmentBootsID, EquipmentNecklaceID, EquipmentWeaponID, EquipmentShieldID, EquipmentBackPackID, EquipmentGlovesID, EquipmentProjectileID, CurrentState, IsSprinting, MentalClarity]` <br> - `EntityID`: Player’s ID. <br> - `EntityTypeID`: Player entity type. <br> - `PlayerType`: Player’s type (e.g., regular, admin). <br> - `Username`: Player’s username. <br> - `CombatLevel`: Player’s combat level. <br> - `HitpointsLevel`: Max hitpoints level. <br> - `CurrentHitpointsLevel`: Current hitpoints. <br> - `MapLevel`: Map level (e.g., ground, underground). <br> - `X`, `Y`: Player’s coordinates. <br> - `HairStyleID`, `BeardStyleID`, `ShirtID`, `BodyTypeID`, `LegsID`: Appearance IDs. <br> - `Equipment*ID`: IDs of equipped items (helmet, chest, etc.) or `null`. <br> - `CurrentState`: Player’s state (e.g., idle, combat). <br> - `IsSprinting`: 1 if sprinting, 0 otherwise. <br> - `MentalClarity`: Mental clarity stat (e.g., for magic). <br> *Purpose*: Notifies when a player enters a map chunk, including appearance and equipment. |
| 4 | NPCEnteredChunk | `[EntityID, NPCID, CurrentMapLevel, X, Y, CurrentHitpointsLevel, IsSprinting, VisibilityRequirements]` <br> - `EntityID`: NPC’s ID. <br> - `NPCID`: NPC type ID. <br> - `CurrentMapLevel`: Map level. <br> - `X`, `Y`: Coordinates. <br> - `CurrentHitpointsLevel`: NPC’s current hitpoints. <br> - `IsSprinting`: 1 if sprinting, 0 otherwise. <br> - `VisibilityRequirements`: Visibility rules (format unclear). <br> *Purpose*: Notifies when an NPC enters a map chunk. |
| 5 | ItemEnteredChunk | `[EntityID, ItemID, Amount, IsIOU, MapLevel, X, Y]` <br> - `EntityID`: Item’s unique ID. <br> - `ItemID`: Item definition ID (via `ItemDefs.getDefById`). <br> - `Amount`: Item quantity. <br> - `IsIOU`: 1 if IOU (placeholder), 0 otherwise. <br> - `MapLevel`: Map level. <br> - `X`, `Y`: Item’s coordinates. <br> *Purpose*: Notifies when a dropped item enters a map chunk. |
| 6 | EntityExitedChunk | `[EntityID, EntityType]` <br> - `EntityID`: Entity’s ID. <br> - `EntityType`: Type of entity (e.g., player, NPC). <br> *Purpose*: Notifies when an entity exits a map chunk. |
| 7 | IncreaseCombatExp | `[Style, DamageAmount]` <br> - `Style`: Combat style (e.g., melee, ranged). <br> - `DamageAmount`: Amount of damage dealt, contributing to XP. <br> *Purpose*: Awards combat experience based on damage. |
| 8 | ShowDamage | `[SenderEntityID, ReceiverEntityID, DamageAmount]` <br> - `SenderEntityID`: Attacker’s ID. <br> - `ReceiverEntityID`: Target’s ID. <br> - `DamageAmount`: Damage dealt. <br> *Purpose*: Displays damage dealt in combat. |
| 9 | ObtainedResource | `[ItemID]` <br> - `ItemID`: ID of obtained resource. <br> *Purpose*: Notifies resource acquisition (e.g., ore, fish). |
| 10 | SendMovementPath | `[X, Y]` <br> - `X`, `Y`: Coordinates of a pathfinding waypoint. <br> *Purpose*: Sends a movement path waypoint for pathfinding. |
| 11 | IEnteredChunk | `[CenterX, CenterY]` <br> - `CenterX`, `CenterY`: Center coordinates of the chunk entered by the local player. <br> *Purpose*: Notifies local player’s chunk entry. |
| 12 | PublicMessage | `[EntityID, Style, Message, PlayerType]` <br> - `EntityID`: Sender’s ID. <br> - `Style`: Message style (e.g., color, format). <br> - `Message`: Chat message text. <br> - `PlayerType`: Sender’s player type. <br> *Purpose*: Sends a public chat message. |
| 13 | EnteredIdleState | `[EntityID, EntityType]` <br> - `EntityID`: Entity’s ID. <br> - `EntityType`: Entity type (e.g., player, NPC). <br> *Purpose*: Notifies an entity entering an idle state. |
| 14 | Login | `[Username, Token, CurrentClientVersion]` <br> - `Username`: Player’s username. <br> - `Token`: Authentication token. <br> - `CurrentClientVersion`: Client version string. <br> *Purpose*: Initiates a login attempt. |
| 15 | LoginFailed | `[Reason]` <br> - `Reason`: Failure reason (e.g., invalid credentials). <br> *Purpose*: Notifies a failed login attempt. |
| 16 | LoggedIn | `[EntityID, EntityTypeID, PlayerType, Username, MapLevel, X, Y, CombatStyle, AutoRetaliate, Inventory, HairStyleID, BeardStyleID, ShirtID, BodyTypeID, LegsID, EquipmentHead, EquipmentBody, EquipmentLegs, EquipmentBoots, EquipmentNecklace, EquipmentWeapon, EquipmentShield, EquipmentBackPack, EquipmentGloves, EquipmentProjectile, CurrentHour, HitpointsExp, HitpointsCurrLvl, AccuracyExp, AccuracyCurrLvl, StrengthExp, StrengthCurrLvl, DefenseExp, DefenseCurrLvl, MagicExp, MagicCurrLvl, FishingExp, FishingCurrLvl, CookingExp, CookingCurrLvl, ForestryExp, ForestryCurrLvl, MiningExp, MiningCurrLvl, CraftingExp, CraftingCurrLvl, CrimeExp, CrimeCurrLvl, PotionmakingExp, PotionmakingCurrLvl, SmithingExp, SmithingCurrLvl, HarvestingExp, HarvestingCurrLvl, EnchantingExp, EnchantingCurrLvl, RangeExp, RangeCurrLvl, QuestCheckpoints, IsEmailConfirmed, LastLoginIP, LastLoginBrowser, LastLoginTimeMS, CurrentState, PlayerSessionID, ChatToken, MentalClarity]` <br> - `EntityID`, `EntityTypeID`, `PlayerType`, `Username`, `MapLevel`, `X`, `Y`: Player details. <br> - `CombatStyle`, `AutoRetaliate`: Combat settings. <br> - `Inventory`: Array of `[ItemID, Amount, IsIOU]` or `null`. <br> - `HairStyleID`, `BeardStyleID`, etc.: Appearance and equipment IDs. <br> - `HitpointsExp`, `HitpointsCurrLvl`, etc.: Skill XP and current levels. <br> - `QuestCheckpoints`: Quest progress data. <br> - `IsEmailConfirmed`: 1 if confirmed, 0 otherwise. <br> - `LastLogin*`: Login metadata. <br> - `CurrentState`, `PlayerSessionID`, `ChatToken`, `MentalClarity`: Session and stat data. <br> *Purpose*: Confirms successful login with player data. |
| 17 | Logout | `[EntityID]` <br> - `EntityID`: Player’s ID. <br> *Purpose*: Initiates a logout. |
| 18 | LogoutFailed | `[Reason]` <br> - `Reason`: Failure reason. <br> *Purpose*: Notifies a failed logout attempt. |
| 19 | LoggedOut | `[EntityID]` <br> - `EntityID`: Player’s ID. <br> *Purpose*: Confirms successful logout. |
| 20 | StartedBanking | `[EntityID, BankID]` <br> - `EntityID`: Player’s ID. <br> - `BankID`: Bank instance ID. <br> *Purpose*: Opens the bank UI. |
| 21 | StoppedBanking | `[EntityID]` <br> - `EntityID`: Player’s ID. <br> *Purpose*: Closes the bank UI. |
| 22 | ReceivedBankItems | `[Items]` <br> - `Items`: Array of `[ItemID, Amount]` or `null`. <br> *Purpose*: Sends bank item data to the client. |
| 23 | TradeRequested | `[RequestingPlayerID, OtherPlayerID]` <br> - `RequestingPlayerID`: Initiator’s ID. <br> - `OtherPlayerID`: Target player’s ID. <br> *Purpose*: Initiates a trade request. |
| 24 | TradePlayerAccepted | `[PlayerID]` <br> - `PlayerID`: Accepting player’s ID. <br> *Purpose*: Confirms a player’s trade acceptance. |
| 25 | TradeStatusReset | `[]` <br> *Purpose*: Resets trade status (no data). |
| 26 | TradeGoToFinalStep | `[]` <br> *Purpose*: Advances trade to final step (no data). |
| 27 | TradeStarted | `[Player1ID, Player2ID]` <br> - `Player1ID`, `Player2ID`: IDs of trading players. <br> *Purpose*: Starts a trade session. |
| 28 | TradeCancelled | `[Player1ID, Player2ID, Reason]` <br> - `Player1ID`, `Player2ID`: Trading players’ IDs. <br> - `Reason`: Cancellation reason. <br> *Purpose*: Cancels a trade. |
| 29 | TradeCompleted | `[Player1ID, Player2ID, CurrentInventory]` <br> - `Player1ID`, `Player2ID`: Trading players’ IDs. <br> - `CurrentInventory`: Array of items or `null` (28 slots). <br> *Purpose*: Completes a trade, updating inventories. |
| 30 | UseItemOnEntity | `[ItemID, EntityType, EntityID]` <br> - `ItemID`: Item used. <br> - `EntityType`, `EntityID`: Target entity. <br> *Purpose*: Uses an item on an entity. |
| 31 | CreateItem | `[ItemID, Amount, MenuType]` <br> - `ItemID`, `Amount`: Item and quantity to create. <br> - `MenuType`: Crafting menu type. <br> *Purpose*: Initiates item crafting. |
| 32 | CreatedItem | `[ItemID, Amount, RecipeInstancesToRemove]` <br> - `ItemID`, `Amount`: Created item details. <br> - `RecipeInstancesToRemove`: Recipe data to remove. <br> *Purpose*: Confirms item creation. |
| 33 | StartedTargeting | `[EntityID, EntityType, TargetID, TargetType]` <br> - `EntityID`, `EntityType`: Attacker’s details. <br> - `TargetID`, `TargetType`: Target’s details. <br> *Purpose*: Begins targeting an entity for combat. |
| 34 | StoppedTargeting | `[EntityID, EntityType]` <br> - `EntityID`, `EntityType`: Entity stopping targeting. <br> *Purpose*: Stops targeting an entity. |
| 35 | StartedSkilling | `[PlayerEntityID, TargetID, Skill, TargetType]` <br> - `PlayerEntityID`: Player’s ID. <br> - `TargetID`: Target entity/resource ID. <br> - `Skill`: Skill type (e.g., mining). <br> - `TargetType`: Type of target (default: `Environment`). <br> *Purpose*: Starts a skilling activity. |
| 36 | StoppedSkilling | `[PlayerEntityID, Skill, DidExhaustResources]` <br> - `PlayerEntityID`, `Skill`: Player and skill. <br> - `DidExhaustResources`: 1 if resources exhausted, 0 otherwise. <br> *Purpose*: Stops a skilling activity. |
| 37 | EquippedItem | `[PlayerEntityID, ItemID]` <br> - `PlayerEntityID`, `ItemID`: Player and equipped item. <br> *Purpose*: Equips an item. |
| 38 | UnequippedItem | `[PlayerEntityID, ItemID]` <br> - `PlayerEntityID`, `ItemID`: Player and unequipped item. <br> *Purpose*: Unequips an item. |
| 39 | PlayerSkillLevelIncreased | `[PlayerEntityID, Skill, LevelsGained, NewLevel]` <br> - `PlayerEntityID`, `Skill`: Player and skill. <br> - `LevelsGained`, `NewLevel`: Level increase details. <br> *Purpose*: Notifies skill level increase. |
| 40 | PlayerCombatLevelIncreased | `[PlayerEntityID, NewCombatLevel]` <br> - `PlayerEntityID`, `NewCombatLevel`: Player and new combat level. <br> *Purpose*: Notifies combat level increase. |
| 41 | CookedItem | `[ItemID]` <br> - `ItemID`: ID of cooked item. <br> *Purpose*: Confirms successful cooking. |
| 42 | OvercookedItem | `[ItemID]` <br> - `ItemID`: ID of overcooked item. <br> *Purpose*: Notifies overcooked item. |
| 43 | PerformActionOnEntity | `[TargetAction, EntityType, EntityID]` <br> - `TargetAction`: Action type (e.g., attack). <br> - `EntityType`, `EntityID`: Target entity. <br> *Purpose*: Performs an action on an entity. |
| 44 | InGameHourChanged | `[CurrentHour]` <br> - `CurrentHour`: New in-game hour. <br> *Purpose*: Updates in-game time. |
| 45 | ChangeCombatStyle | `[CombatStyle, IsSelected]` <br> - `CombatStyle`: New combat style. <br> - `IsSelected`: 1 if selected, 0 otherwise. <br> *Purpose*: Requests combat style change. |
| 46 | CombatStyleChanged | `[CombatStyle]` <br> - `CombatStyle`: Confirmed combat style. <br> *Purpose*: Confirms combat style change. |
| 47 | ChangeAutoRetaliate | `[AutoRetaliate]` <br> - `AutoRetaliate`: 1 if enabled, 0 otherwise. <br> *Purpose*: Toggles auto-retaliate. |
| 48 | AutoRetaliateChanged | `[AutoRetaliate]` <br> - `AutoRetaliate`: 1 if enabled, 0 otherwise. <br> *Purpose*: Confirms auto-retaliate change. |
| 49 | TeleportTo | `[EntityID, EntityType, X, Y, MapLevel, Type, SpellID]` <br> - `EntityID`, `EntityType`: Teleporting entity. <br> - `X`, `Y`, `MapLevel`: Destination coordinates and map level. <br> - `Type`, `SpellID`: Teleport type and spell ID (default -1). <br> *Purpose*: Teleports an entity to a location. |
| 50 | PlayerDied | `[VictimEntityID, PKerEntityID]` <br> - `VictimEntityID`: Dead player’s ID. <br> - `PKerEntityID`: Killer’s ID (default -1). <br> *Purpose*: Notifies player death. |
| 51 | StartedShopping | `[ShopID, EntityID, CurrentStock]` <br> - `ShopID`, `EntityID`: Shop and player IDs. <br> - `CurrentStock`: Array of `[ItemID, Amount]` or `null`. <br> *Purpose*: Opens shop UI with stock data. |
| 52 | StoppedShopping | `[ShopID, EntityID]` <br> - `ShopID`, `EntityID`: Shop and player IDs. <br> *Purpose*: Closes shop UI. |
| 53 | UpdatedShopStock | `[ItemID, Amount]` <br> - `ItemID`, `Amount`: Updated shop item and quantity. <br> *Purpose*: Updates shop inventory. |
| 54 | StartedChangingAppearance | `[EntityID, IsFirstTime]` <br> - `EntityID`: Player’s ID. <br> - `IsFirstTime`: 1 if first-time appearance change, 0 otherwise. <br> *Purpose*: Opens appearance change UI. |
| 55 | StoppedChangingAppearance | `[EntityID]` <br> - `EntityID`: Player’s ID. <br> *Purpose*: Closes appearance change UI. |
| 56 | ChangeAppearance | `[HairID, BeardID, ShirtID, BodyID, PantsID]` <br> - `HairID`, `BeardID`, etc.: New appearance IDs. <br> *Purpose*: Requests appearance change. |
| 57 | ChangedAppearance | `[EntityID, HairID, BeardID, ShirtID, BodyID, PantsID]` <br> - `EntityID`: Player’s ID. <br> - `HairID`, `BeardID`, etc.: Confirmed appearance IDs. <br> *Purpose*: Confirms appearance change (e.g., via `ChangeAppearancePlayerEventAction`). |
| 58 | MenuStateKeepAlivePing | `[IsActive]` <br> - `IsActive`: 1 if menu is active, 0 otherwise. <br> *Purpose*: Keeps menu state alive (e.g., for `CenterMenu`). |
| 59 | ToggleSprint | `[IsSprinting]` <br> - `IsSprinting`: 1 if sprinting enabled, 0 otherwise. <br> *Purpose*: Toggles sprint mode. |
| 60 | ToggledSprint | `[EntityID, EntityType, IsSprinting]` <br> - `EntityID`, `EntityType`: Entity details. <br> - `IsSprinting`: 1 if sprinting, 0 otherwise. <br> *Purpose*: Confirms sprint mode toggle. |
| 61 | RestoredStats | `[Skills]` <br> - `Skills`: Array of restored skill data. <br> *Purpose*: Restores player stats (e.g., health). |
| 62 | EntityExhaustedResources | `[EntityTypeIDs, IsChunkSnapshot, ChunkCenter]` <br> - `EntityTypeIDs`: String of entity type IDs. <br> - `IsChunkSnapshot`: 1 if chunk snapshot, 0 otherwise. <br> - `ChunkCenter`: Chunk center coordinates or `null`. <br> *Purpose*: Notifies resource exhaustion (e.g., tree, ore vein). |
| 63 | EntityReplenishedResources | `[EntityTypeID]` <br> - `EntityTypeID`: Replenished entity type ID. <br> *Purpose*: Notifies resource replenishment. |
| 64 | ShookTree | `[TreeShakerEntityID, TreeShakerEntityType, TreeID]` <br> - `TreeShakerEntityID`, `TreeShakerEntityType`: Shaker’s details. <br> - `TreeID`: Tree’s ID. <br> *Purpose*: Player shakes a tree for resources. |
| 65 | GainedExp | `[Skill, Amount]` <br> - `Skill`, `Amount`: Skill and experience gained. <br> *Purpose*: Awards experience to a skill. |
| 66 | ShakeTreeResult | `[ItemID, IsRare]` <br> - `ItemID`: ID of item obtained (default 0). <br> - `IsRare`: 1 if rare item, 0 otherwise. <br> *Purpose*: Notifies tree shake results. |
| 67 | OpenedSkillingMenu | `[TargetID, MenuType]` <br> - `TargetID`: Target entity/resource ID. <br> - `MenuType`: Skilling menu type. <br> *Purpose*: Opens a skilling menu (e.g., crafting). |
| 68 | UseItemOnItem | `[MenuType, UsingItemSlot, UsingItemID, UsingItemIsIOU, TargetItemSlot, TargetItemID, TargetItemIsIOU, ItemOnItemActionResultIndex, AmountToCreate]` <br> - `MenuType`: Inventory menu type. <br> - `UsingItemSlot`, `UsingItemID`, `UsingItemIsIOU`: Item used. <br> - `TargetItemSlot`, `TargetItemID`, `TargetItemIsIOU`: Target item. <br> - `ItemOnItemActionResultIndex`: Result index. <br> - `AmountToCreate`: Quantity to create. <br> *Purpose*: Uses one item on another (e.g., crafting). |
| 69 | UsedItemOnItem | `[MenuType, UsingItemSlot, UsingItemID, UsingItemIsIOU, TargetItemSlot, TargetItemID, TargetItemIsIOU, ItemOnItemActionResultIndex, AmountToCreate, Success]` <br> - Same as `UseItemOnItem`, plus: <br> - `Success`: 1 if successful, 0 otherwise. <br> *Purpose*: Confirms item-on-item action. |
| 70 | WentThroughDoor | `[DoorEntityTypeID, EntityID]` <br> - `DoorEntityTypeID`: Door’s type ID. <br> - `EntityID`: Player’s ID. <br> *Purpose*: Player passes through a door. |
| 71 | CastTeleportSpell | `[SpellID]` <br> - `SpellID`: Teleport spell ID. <br> *Purpose*: Casts a teleport spell. |
| 72 | CastedTeleportSpell | `[EntityID, EntityType, SpellID]` <br> - `EntityID`, `EntityType`, `SpellID`: Caster and spell details. <br> *Purpose*: Confirms teleport spell cast. |
| 73 | CastInventorySpell | `[SpellID, Menu, Slot, ItemID, IsIOU]` <br> - `SpellID`, `Menu`, `Slot`, `ItemID`, `IsIOU`: Spell and target item details. <br> *Purpose*: Casts a spell on an inventory item. |
| 74 | CastedInventorySpell | `[EntityID, EntityType, SpellID, TargetItemID]` <br> - `EntityID`, `EntityType`, `SpellID`, `TargetItemID`: Caster and target details. <br> *Purpose*: Confirms inventory spell cast. |
| 75 | CastSingleCombatOrStatusSpell | `[SpellID, TargetID, TargetEntityType]` <br> - `SpellID`, `TargetID`, `TargetEntityType`: Spell and target details. <br> *Purpose*: Casts a combat or status spell. |
| 76 | CastedSingleCombatOrStatusSpell | `[SpellID, CasterID, CasterEntityType, TargetID, TargetEntityType, DamageAmount, IsConfused]` <br> - `SpellID`, `CasterID`, `CasterEntityType`, `TargetID`, `TargetEntityType`, `DamageAmount`, `IsConfused`: Spell and combat details. <br> *Purpose*: Confirms combat/status spell cast. |
| 77 | ToggleAutoCast | `[SpellID]` <br> - `SpellID`: Spell to toggle autocast. <br> *Purpose*: Toggles spell autocast. |
| 78 | ToggledAutoCast | `[SpellID]` <br> - `SpellID`: Confirmed autocast spell. <br> *Purpose*: Confirms autocast toggle. |
| 79 | SkillCurrentLevelChanged | `[Skill, CurrentLevel]` <br> - `Skill`, `CurrentLevel`: Skill and new current level. <br> *Purpose*: Notifies skill level change (e.g., via `SkillCurrentLevelChangedPlayerEventAction`). |
| 80 | ServerInfoMessage | `[Message, Style]` <br> - `Message`, `Style`: Server message and style. <br> *Purpose*: Sends server announcements. |
| 81 | ForcePublicMessage | `[EntityID, EntityType, Message]` <br> - `EntityID`, `EntityType`, `Message`: Forced message details. <br> *Purpose*: Sends a forced public message. |
| 82 | QuestProgressed | `[QuestID, CurrentCheckpoint]` <br> - `QuestID`, `CurrentCheckpoint`: Quest progress details. <br> *Purpose*: Notifies quest progression. |
| 83 | CreatedUseItemOnItemActionItems | `[UseItemID, UsedItemOnID, UseItemOnItemIndex]` <br> - `UseItemID`, `UsedItemOnID`, `UseItemOnItemIndex`: Item-on-item action details. <br> *Purpose*: Confirms creation from item-on-item action. |
| 84 | PathfindingFailed | `[EntityID]` <br> - `EntityID`: Entity with failed pathfinding. <br> *Purpose*: Notifies pathfinding failure. |
| 85 | FiredProjectile | `[ProjectileID, RangerID, RangerEntityType, TargetID, TargetEntityType, DamageAmount, IsConfused]` <br> - `ProjectileID`, `RangerID`, `RangerEntityType`, `TargetID`, `TargetEntityType`, `DamageAmount`, `IsConfused`: Projectile details. <br> *Purpose*: Notifies a fired projectile. |
| 86 | ServerShutdownCountdown | `[MinutesUntilShutdown]` <br> - `MinutesUntilShutdown`: Time until server shutdown. <br> *Purpose*: Notifies server shutdown countdown. |
| 87 | CanLogin | `[]` <br> *Purpose*: Confirms login eligibility (no data). |
| 88 | ReconnectToServer | `[Username, PlayerSessionID]` <br> - `Username`, `PlayerSessionID`: Reconnection details. <br> *Purpose*: Initiates server reconnection. |
| 89 | EntityStunned | `[EntityID, EntityType, StunTicks]` <br> - `EntityID`, `EntityType`, `StunTicks`: Stunned entity details. <br> *Purpose*: Notifies entity stun. |
| 90 | GlobalPublicMessage | `[EntityID, Name, Message, PlayerType]` <br> - `EntityID`, `Name`, `Message`, `PlayerType`: Global message details. <br> *Purpose*: Sends a global chat message. |
| 91 | HealthRestored | `[EntityType, EntityID, CurrentHealth]` <br> - `EntityType`, `EntityID`, `CurrentHealth`: Health restoration details. <br> *Purpose*: Notifies health restoration. |
| 92 | PlayerCountChanged | `[CurrentPlayerCount]` <br> - `CurrentPlayerCount`: Current number of players. <br> *Purpose*: Notifies player count change. |
| 93 | ForcedSkillCurrentLevelChanged | `[Skill, CurrentLevel, ShowStatRestorationMessage]` <br> - `Skill`, `CurrentLevel`, `ShowStatRestorationMessage`: Forced skill change details. <br> *Purpose*: Notifies server-forced skill level change. |
| 94 | ReceivedNPCConversationDialogue | `[EntityID, NPCConversationID, ConversationDialogueID, IsInitialDialogue, NPCText, PlayerConversationOptions]` <br> - `EntityID`, `NPCConversationID`, `ConversationDialogueID`, `IsInitialDialogue`, `NPCText`, `PlayerConversationOptions`: Dialogue details. <br> *Purpose*: Sends NPC conversation dialogue. |
| 95 | SelectNPCConversationOption | `[PlayerConversationOptionID]` <br> - `PlayerConversationOptionID`: Selected option ID. <br> *Purpose*: Selects an NPC conversation option. |
| 96 | EndedNPCConversation | `[EntityID]` <br> - `EntityID`: Player’s ID. <br> *Purpose*: Ends an NPC conversation. |
| 97 | InvokeInventoryItemAction | `[Action, MenuType, Slot, ItemID, Amount, IsIOU]` <br> - `Action`, `MenuType`, `Slot`, `ItemID`, `Amount`, `IsIOU`: Inventory action details. <br> *Purpose*: Initiates an inventory item action. |
| 98 | InvokedInventoryItemAction | `[Action, MenuType, Slot, ItemID, Amount, IsIOU, Success, Data]` <br> - Same as `InvokeInventoryItemAction`, plus: <br> - `Success`: 1 if successful, 0 otherwise. <br> - `Data`: Additional data or 0. <br> *Purpose*: Confirms inventory item action. |
| 99 | UsedItemOnEntity | `[EntityID, EntityType, TargetEntityID, TargetEntityType, ItemID, Success]` <br> - `EntityID`, `EntityType`, `TargetEntityID`, `TargetEntityType`, `ItemID`, `Success`: Item-on-entity details. <br> *Purpose*: Confirms item use on entity. |
| 100 | InsertAtBankStorageSlot | `[Slot1, Slot2]` <br> - `Slot1`, `Slot2`: Bank slot indices. <br> *Purpose*: Inserts an item into a bank slot. |
| 101 | InsertedAtBankStorageSlot | `[Result]` <br> - `Result`: 1 if successful, 0 otherwise. <br> *Purpose*: Confirms bank slot insertion. |
| 102 | RemovedItemFromInventoryAtSlot | `[MenuType, Slot, ItemID, Amount, IsIOU, RemainingAmountAtSlot]` <br> - `MenuType`, `Slot`, `ItemID`, `Amount`, `IsIOU`, `RemainingAmountAtSlot`: Inventory removal details (e.g., via `InventoryChangeEvent`). <br> *Purpose*: Removes an item from an inventory slot. |
| 103 | AddedItemAtInventorySlot | `[MenuType, Slot, ItemID, Amount, IsIOU, PreviousAmountAtSlot]` <br> - `MenuType`, `Slot`, `ItemID`, `Amount`, `IsIOU`, `PreviousAmountAtSlot`: Inventory addition details. <br> *Purpose*: Adds an item to an inventory slot. |
| 104 | ShowLootMenu | `[Items, Type]` <br> - `Items`: Array of item data or `null`. <br> - `Type`: Loot menu type (e.g., `Default`). <br> *Purpose*: Opens a loot menu. |
| 105 | ReorganizeInventorySlots | `[Menu, Slot1, ItemID1, IsIOU1, Slot2, ItemID2, IsIOU2, Type]` <br> - `Menu`, `Slot1`, `ItemID1`, `IsIOU1`, `Slot2`, `ItemID2`, `IsIOU2`, `Type`: Inventory reorganization details. <br> *Purpose*: Reorganizes inventory slots. |
| 106 | ReorganizedInventorySlots | `[Menu, Slot1, ItemID1, IsIOU1, Slot2, ItemID2, IsIOU2, Type, Success]` <br> - Same as `ReorganizeInventorySlots`, plus: <br> - `Success`: 1 if successful, 0 otherwise. <br> *Purpose*: Confirms inventory reorganization. |
| 107 | SwitchToIdleState | `[Switch]` <br> - `Switch`: Always 1. <br> *Purpose*: Switches to idle state. |
| 108 | UpdateTradeStatus | `[Status]` <br> - `Status`: Trade status data. <br> *Purpose*: Updates trade status. |
| 109 | StartedDigging | `[EntityID]` <br> - `EntityID`: Player’s ID. <br> *Purpose*: Starts digging. |
| 110 | StoppedDigging | `[EntityID]` <br> - `EntityID`: Player’s ID. <br> *Purpose*: Stops digging. |
| 111 | PlayerInfo | `[Info]` <br> - `Info`: Player info data (format unclear). <br> *Purpose*: Sends player information. |
| 112 | CaptchaAction | `[CaptchaActionType, CaptchaGuessText]` <br> - `CaptchaActionType`, `CaptchaGuessText`: CAPTCHA details. <br> *Purpose*: Submits a CAPTCHA response. |
| 113 | OpenedCaptchaScreen | `[EntityID]` <br> - `EntityID`: Player’s ID. <br> *Purpose*: Opens CAPTCHA screen. |
| 114 | ReceivedCaptcha | `[ImageDataURL, Width, Height]` <br> - `ImageDataURL`, `Width`, `Height`: CAPTCHA image details. <br> *Purpose*: Sends CAPTCHA challenge data. |
| 115 | CaptchaActionResult | `[CaptchaActionType]` <br> - `CaptchaActionType`: Result type (e.g., pass/fail). <br> *Purpose*: Notifies CAPTCHA result. |
| 116 | MentalClarityChanged | `[EntityID, OldMentalClarity, NewMentalClarity]` <br> - `EntityID`, `OldMentalClarity`, `NewMentalClarity`: Mental clarity change details. <br> *Purpose*: Notifies mental clarity stat change. |
| 117 | MutedPlayer | `[Name, IsMuted]` <br> - `Name`: Player’s lowercase name. <br> - `IsMuted`: 1 if muted, 0 otherwise. <br> *Purpose*: Notifies player mute status. |

### WebSocket Communication

- **Current**: Uses Socket.IO format `42["packetType", payload]` over WebSockets (`wss://server#.highspell.com:8888`)
- **Legacy**: Used `GameActionsEnum` IDs with `{type: ID, data: []}` format (now obsolete)
- **Monitoring**: Use Chrome DevTools Network tab, filter by "WS" for WebSocket inspection
- **Analysis**: Modern packets are simpler but require understanding the new action-based structure

### Protocol Migration Notes
- Most legacy packet types (1-117) have been consolidated or removed
- Player actions unified under packet type "1" with action subtypes
- Login packet moved from "16" to "15" 
- State updates centralized under packet type "0"

## Ethical and Legal Notes
Per the HighSpell Botting Resources ethos:
- This documentation is for educational purposes, analyzing observable game client behavior.
- Using this information to create bots violates HighSpell’s terms of service, risking account bans.
