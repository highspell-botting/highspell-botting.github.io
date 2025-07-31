# 🔌 Protocol Analysis

This section contains documentation related to the communication protocols and network architecture of the HighSpell client.

## Overview

The HighSpell client uses WebSocket-based communication for real-time game interactions. Understanding these protocols is essential for analyzing client-server communication patterns and identifying automation opportunities.

## Contents

### [GameActionsEnum](game-websocket.md)
Complete documentation of all 118 game action types, their data structures, and usage patterns. This is the core reference for understanding how the client communicates with the server.

### [SocketManager](socketmanager.md)
Documentation of the client's WebSocket management system, including connection handling, message routing, and error management.

### [Protocol Analysis](protocol-analysis.md)
High-level analysis of the communication protocols, including authentication flows, message formats, and security considerations.

## Key Concepts

- **WebSocket Communication**: Real-time bidirectional communication between client and server
- **Message Types**: Structured data packets with specific action IDs and payloads
- **State Synchronization**: How game state is maintained across network connections