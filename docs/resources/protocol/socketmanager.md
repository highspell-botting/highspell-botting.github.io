# SocketManager Analysis

## Overview
The `SocketManager` class in the HighSpell client manages WebSocket communication, processing incoming `GameActionsEnum` packets through a `_gameStateUpdate` method that delegates to a `_handler`. This setup allows for interception of these functions to implement custom logic for handling game state information. This analysis explores how to intercept these packets to monitor player state or trigger events, aligning with the HighSpell Botting project's goal of transparent, educational documentation.

## Usefulness for Botting
The `SocketManager` class is essential for bot development as it serves as the primary gateway for all game state information and network communication. By intercepting the `_gameStateUpdate` method and its associated `_handler`, bots can monitor real-time game events such as player state changes, combat actions, inventory updates, and chat messages. This enables sophisticated automation that responds to game conditions rather than relying solely on polling. The class's integration with `GameActionsEnum` provides a structured way to filter and process specific packet types, allowing bots to detect when players start banking, begin skilling, receive private messages, or enter combat. Additionally, the separate chat WebSocket endpoint offers opportunities for notification systems and automated responses to social interactions. The ability to intercept and potentially modify packet handling makes `SocketManager` a critical component for creating responsive, event-driven automation systems.

## Important Notes
- **Changeability**: The `_gameStateUpdate` and `_handler` methods, along with their integration with `GameActionsEnum`, may change in major client updates due to code refactoring or new features. Verify their implementation in the client code or WebSocket traffic after updates.

## Interception Techniques
The `_gameStateUpdate` method takes a `GameActionsEnum` ID and its associated data (as defined by `GameActionFactory`) and passes it to a `_handler` for processing. By intercepting these functions, custom logic can be implemented to monitor or react to specific game states. Below are key examples:

### 1. Monitoring Player State via GameStateUpdate
- **Mechanism**: The `GameStateUpdate` action (ID 0) contains an array of state updates (`[{ Name, Data }]`). By intercepting `_gameStateUpdate`, you can filter for specific actions, such as `StartedBanking` (ID 20), and check if the `EntityID` matches the `MainPlayers` entity ID.
- **Use Case**: Detect when the player opens the bank UI by monitoring `StartedBanking` packets. This allows scripts to determine the player’s state (e.g., banking, idle) without directly polling the game state.
- **Implementation**:
  - Intercept `_gameStateUpdate` to check for `GameActionsEnum.GameStateUpdate` (ID 0).
  - Parse the data array for `StartedBanking` sub-actions with `EntityID` matching `MainPlayers`.
  - Trigger custom logic (e.g., log the event or update a state tracker).
  - Or better yet, let the client do it for you. Just add code to where it handles StartedBanking.


### 2. Intercepting Specific Packets
- **Mechanism**: Intercept `_gameStateUpdate` or `_handler` to process specific `GameActionsEnum` packets, such as `PrivateMessage` received on the chat WebSocket (a separate endpoint from the main game WebSocket).
- **Use Case**: Play a sound notification when a `PrivateMessage` packet is received, enhancing user awareness without manual chat monitoring.
- **Implementation**:
  - Intercept the chat WebSocket’s handler for `PrivateMessage` packets.
  - Extract message details (e.g., sender, content) from the packet payload.
  - Trigger an audio alert using client-side JavaScript (e.g., `Audio.play()`).
- **Example**: A `PrivateMessage` packet might trigger a sound.

### 3. Polling Player State
- **Mechanism**: Instead of intercepting packets, repeatedly check the player’s state, which is updated based on received `GameActionsEnum` packets (e.g., `CurrentState` in `LoggedIn` or `PlayerEnteredChunk`).
- **Use Case**: Monitor state changes (e.g., idle, combat, skilling) by polling the `MainPlayers` object, which reflects updates from packets like `StartedSkilling` (ID 35) or `ToggledSprint` (ID 60).
- **Implementation**:
  - Access the player’s state via `MainPlayers` properties (e.g., `CurrentState`, `IsSprinting`).
  - Set up a polling loop (e.g., using `setInterval`) to check for state changes.
  - Correlate state with recent packets (e.g., `StartedSkilling` updates `CurrentState` to skilling).
- **Example**: Poll `MainPlayers.CurrentState` to detect when it changes to `"skilling"` after a `StartedSkilling` packet.

## Technical Details
- **Interception Points**:
  - `_gameStateUpdate`: Entry point for all `GameActionsEnum` packets. Override or wrap this method to inspect incoming actions.
  - `_handler(s)`: Processes specific actions. Intercept individual handlers (e.g., for `StartedBanking`) for targeted logic.
  - Chat WebSocket: A separate WebSocket for chat-related packets (e.g., `PrivateMessage`). Monitor its handler for chat-specific events.
- **State Updates**: The `MainPlayers` object is updated by packets like `LoggedIn` (ID 16), providing a reliable source for polling state.
- **Changeability**: Method names (e.g., `_gameStateUpdate`, `_handler`) and packet structures may change in updates. Verify using:
  - Browser developer tools to inspect `SocketManager` code.

## Recommendations for Reverse Engineers
- **Intercept Handlers**: Override `