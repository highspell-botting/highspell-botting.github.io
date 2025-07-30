# 🔍 WebSocket Reverse Engineering Guide

*This guide covers reverse engineering techniques for analyzing WebSocket communications in web applications, with a focus on real-time game communication patterns.*

## Overview

WebSocket reverse engineering involves analyzing real-time bidirectional communication between clients and servers. This guide focuses on understanding WebSocket protocols, message formats, and communication patterns for educational purposes.

## Highspell WebSocket Architecture

When you log into Highspell, the application establishes **two separate WebSocket connections** that handle different aspects of the game:

### 1. Game Server WebSocket
- **Endpoint**: `wss://server2.highspell.com:8888`
- **Purpose**: Handles all game state, chat messages, and player interactions
- **Traffic Volume**: High frequency - sends and receives many packets per second
- **Message Types**: Game updates, chat messages, player movements, combat events

### 2. Friends List WebSocket
- **Endpoint**: `wss://highspell.com:8765/`
- **Purpose**: Manages friends list and online user status
- **Traffic Volume**: Low frequency - occasional status updates
- **Message Types**: Friend online/offline notifications, friend list updates

**Important Note**: Chat messages are **NOT** handled by the friends WebSocket. All chat communication goes through the game server WebSocket at `server2.highspell.com:8888`.

## Using Browser Developer Tools

### Opening the Network Tab

1. **Open Developer Tools**:
   - Press `F12` or `Ctrl+Shift+I` (Windows/Linux)
   - Press `Cmd+Option+I` (Mac)
   - Right-click and select "Inspect"

2. **Navigate to Network Tab**:
   - Click the "Network" tab in the developer tools
   - Ensure "All" or "WS" (WebSocket) is selected in the filter

3. **Filter for WebSockets**:
   - Click the "WS" filter to show only WebSocket connections
   - This will help you focus on the relevant traffic

### Analyzing WebSocket Connections

#### Step 1: Establish Baseline
1. **Clear the network log** by clicking the 🚫 icon
2. **Refresh the page** or log into the game
3. **Look for WebSocket connections** in the network tab

#### Step 2: Identify the Connections
You should see two WebSocket connections:
- `wss://server#.example.com:8888` (Game server)
- `wss://example.com:8765/` (Friends server)

#### Step 3: Monitor Real-time Traffic
1. **Click on a WebSocket connection** in the network tab
2. **Go to the "Messages" tab** to see real-time communication
3. **Observe the traffic patterns** as you interact with the game

## WebSocket Message Analysis

### Game Server Messages (`server#.example.com:8888`)

The game server WebSocket handles a high volume of messages for:

#### Chat Messages
```json
// Example sent PublicMessage message
42["12",[-1,0,"hello",0]]
//Example recieved PublicMessage message with a PlayerMoveTo as well.
42["0",[[12,[1014179,0,"Buying Lucky Logs 400e",0]],[1,[1006065,-314,-5]]]]
```

#### Game State Updates
```json
// Example playerMoveTo state update inside of a gameStateUpdate
42["0",[[1,[1006065,-307,-15]]]]
```

#### Player Interactions
```json
// Example of a sent player SendMovementPath packet
42["10",[-316,-13]]
```

### Friends Server Messages (`example.com:8765/`)

The friends WebSocket handles lower-frequency status updates:

#### Friend Status Updates
```json
// Example friendLoggedInOrOut message
42["friendloggedinorout",{"name":"example","server":1,"online":true}]
```

## Practical Analysis Workflow

### Step 1: Initial Reconnaissance
1. **Open the game** and log in
2. **Open Developer Tools** and go to Network tab
3. **Filter for WebSockets** (WS)
4. **Identify the two connections** and note their URLs

### Step 2: Baseline Traffic Analysis
1. **Stay idle** in the game for 30 seconds
2. **Note the traffic patterns** - which connection is more active?
3. **Record message frequencies** and sizes

### Step 3: Interactive Testing
1. **Send a chat message** and observe the game server WebSocket
2. **Check your friends list** and observe the friends WebSocket
3. **Move around in the game** and watch for position updates
4. **Cast a spell or perform an action** and observe the response

### Step 4: Message Structure Analysis
1. **Click on individual messages** in the Network tab
2. **Examine the message content** and try to understand the structure
3. **Look for patterns** in message types and data formats


## Troubleshooting

### Common Issues

#### No WebSocket Connections Visible
- **Check the filter**: Ensure "WS" is selected in the Network tab
- **Refresh the page**: Sometimes connections aren't captured on initial load
- **Check timing**: WebSocket connections might establish after page load

#### Messages Not Appearing
- **Clear the log**: Clear the network log and try again
- **Check for errors**: Look for connection errors in the Console tab
- **Verify connection**: Ensure the WebSocket connection is actually established

#### Performance Issues
- **Limit capture**: Use filters to reduce the amount of captured data
- **Close unused tabs**: Close other browser tabs to free up memory
- **Restart browser**: Sometimes a fresh browser session helps

## Conclusion

WebSocket reverse engineering provides valuable insights into real-time application behavior. By understanding the communication patterns between the game server and friends server in Highspell, you can gain a deeper appreciation for how modern web applications handle real-time data.

---

*This guide is for educational purposes. Always conduct reverse engineering responsibly and within legal and ethical boundaries.* 