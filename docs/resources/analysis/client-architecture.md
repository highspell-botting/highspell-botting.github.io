# 🏗️ Client Architecture

*This page documents the technical architecture of the HighSpell web client, including its structure, components, and internal mechanisms.*

## Overview

The HighSpell client is a web-based application that presents a system of interconnected components.

## Data Flow

### Client-Server Communication

*This section explores the dual-socket communication model of the HighSpell client, outlining how interaction and state synchronization occur between client and server.*

### Dual WebSocket Architecture

The HighSpell client operates on two independent WebSocket connections, each designed for distinct subsystems of the game:

#### 🎮 Game Socket
- **Purpose**: Handles gameplay-critical data and local chat messages.
- **Payload Structure**: Uses compact binary-style packets (arrays of numbers). These messages are lightweight, obfuscated, and decoded exclusively within client-side logic.
- **Client Behavior**: Opens and maintains this socket persistently; message parsing and state updates are performed locally before responses are sent server-side.

#### 💬 Chat Socket
- **Purpose**: Manages social interactions including friend logins/logouts and private messages.
- **Payload Structure**: Uses human-readable JSON, allowing for easier inspection and protocol analysis.
- **Client Behavior**: Operates independently of the game socket, with its own listener and dispatching logic.

---

### 📡 Message Lifecycle

Client-side input and server feedback follow a multi-step communication loop:

```
[User Input] ↓ [Manager Layer] ↓ [State Change + Outbound Packet] ↓ [WebSocket Transmission] ↓ [Server] ↓ [Server-Side State Update] ↓ [Response Packet] ↓ [Manager Processing] ↓ [UI Update]
```

- **Outbound Communication**: Triggered by user actions, event handlers package contextual data into protocol-specific formats and dispatch through the appropriate socket.
- **Inbound Communication**: Packets received from the server invoke local update routines, modifying game state and re-rendering relevant UI elements.

---

### 🕵️ Protocol Visibility

- **Chat Socket**: Transparent and inspectable. Payloads can be analyzed in-browser via developer tools.
- **Game Socket**: Obfuscated but observable. While packet formats are opaque, client-side decoding logic is publicly exposed in the runtime, enabling reverse engineering of core message types and structure.

---

## Security Mechanisms

### Obfuscation
- **Code Minification**: [To be documented]
- **Variable Name Obfuscation**: [To be documented]
- **Function Wrapping**: [To be documented]

### Anti-Bot Measures
- [To be documented]

