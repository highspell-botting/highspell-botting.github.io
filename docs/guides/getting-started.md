# 🚀 Getting Started

Welcome to the HighSpell Botting documentation. This guide will help you understand the fundamentals of reverse engineering web applications and prepare you for exploring the HighSpell client.

## Prerequisites

### Technical Skills
- **Basic JavaScript knowledge**: Understanding of modern JavaScript (ES6+)
- **Web technologies**: Familiarity with HTML, CSS, and browser developer tools
- **Network protocols**: Basic understanding of HTTP, WebSockets, and REST APIs
- **Programming concepts**: Understanding of state management, event handling, and asynchronous programming

### Tools You'll Need

#### Browser Developer Tools
- **Chrome DevTools** or **Firefox Developer Tools**
- **Network monitoring capabilities**
- **JavaScript debugging features**
- **DOM inspection tools**

#### Additional Tools
- **WebSocket testing tools** (e.g., WebSocket King, Postman)
- **Network analysis tools** (e.g., Wireshark, Fiddler)
- **Code analysis tools** (e.g., ESLint, Prettier)
- **Version control** (Git)

## Setting Up Your Environment

### 1. Configure Your Browser
- Enable developer tools
- Set up persistent console logging

## Understanding the Basics

### What is Reverse Engineering?
Reverse engineering is the process of analyzing a system to understand its structure, behavior, and functionality. In the context of web applications, this involves:

1. **Analyzing the frontend code** (HTML, CSS, JavaScript)
2. **Monitoring network communication** (HTTP requests, WebSocket messages)
3. **Understanding data flow** (how information moves through the system)
4. **Identifying automation opportunities** (where manual processes can be automated)

### Why Reverse Engineer HighSpell?

- **To learn**: Reveal how the client actually works under the hood
- **To expose weaknesses**: Identify where automation is easy or security is lacking
- **To demystify automation**: Show how bots interact with the game in practice
- **To educate**: Share findings so others understand the system’s true architecture

*The goal is to make the hidden obvious, and to help others see how and why HighSpell is automatable.*

## Ethical Considerations

### Before You Begin
- **Read the terms of service** of any application you're analyzing
- **Understand the legal implications** of your actions
- **Respect rate limits** and system boundaries
- **Don't disrupt other users' experiences**
- **Use test accounts** when possible

### Responsible Practices
- **Report vulnerabilities** to the appropriate parties
- **Don't exploit weaknesses** for personal gain
- **Respect intellectual property** rights

## Your First Analysis

HighSpell's client is obfuscated and, on load, it overwrites `console.log`, making it difficult to inspect anything but errors in the console at first. Instead, your best starting point is the browser's **Network** tab.

### Step 1: Open the Application and Network Tab
1. Open HighSpell in your browser.
2. Open developer tools (F12 or right-click → Inspect) and switch to the **Network** tab **before** the page finishes loading.

### Step 2: Reload and Observe Network Traffic
- Reload the page with the Network tab open.
- Watch for a large JavaScript files (e.g., `client.##.js`), blobs, and styling.
- Look for requests to endpoints like `/assetsClient`—these often return JSON with a list of game data files and asset URLs.

#### Example: assetsClient Response

You may see a response like:

```json
{
  "code": 1,
  "msg": "ok",
  "data": {
    "latestClientVersion": 53,
    "latestServerVersion": 53,
    "files": {
      "defs": {
        "itemDefs": "https://example.com:8887/static/itemdefs.##.carbon",
        ...
      },
      "gameAssets": {
        "earthOverworldMap": "https://example.com:8887/static/assets/heightmaps/earthoverworldtexture.png",
        ...
      }
    }
  }
}
```

- **defs**: Contains URLs to the latest game definition files (items, NPCs, quests, etc.). Some of which are inaccessible and intended for the server. At one point they were all accessible. However, only a few (like drop tables) that are intended for the server remain public.
- **gameAssets**: Contains URLs to the latest game asset files (maps, textures, meshes, etc.).
- **latestClientVersion / latestServerVersion**: Indicates the current version numbers for compatibility checking.

## Next Steps

### Overriding `client.##.js` Using Chrome Workspaces

To modify or debug the client JavaScript file (`client.##.js`), you can use Chrome's workspace and override features. This allows you to edit files locally and have Chrome serve your version instead of the one from the server.

#### 1. Open Chrome DevTools

- Open Chrome and navigate to the target web page.
- Press <kbd>F12</kbd> or <kbd>Ctrl+Shift+I</kbd> (Windows/Linux) or <kbd>Cmd+Option+I</kbd> (Mac) to open DevTools.

#### 2. Set Up a Workspace

1. Go to the **Sources** tab in DevTools.
2. In the left sidebar, find the **Filesystem** pane.
3. Click **+ Add folder to workspace**.
4. Select a local folder on your computer to use for overrides.
5. Grant Chrome permission to access this folder.

#### 3. Enable Overrides

1. In the **Sources** tab, click the **Overrides** tab (if not visible, click the `>>` menu).
2. Click **Select folder for overrides** and choose your workspace folder.
3. Confirm Chrome's request for folder access.

#### 4. Override the Target File

1. In the **Sources** panel, locate `client.##.js` under the **Page** or **Network** section.
2. Right-click the file and select **Save for overrides** (or **Override content**).
3. Chrome will save a copy of the file in your workspace folder.

#### 5. Edit and Test

- Edit the overridden file in your local editor or directly in DevTools.
- DevTools will conveniently already un-minify the code. You can use this as a starting point or un-minify the code another way.
- **Important:** The JavaScript client is very large (e.g., 250,000+ lines), saving changes and reloading the page can cause the page to freeze or become unresponsive for a long time. To avoid this, always log out or close the tab running the client before saving and reloading the overridden file. Then, open a new tab to reload the page with your changes.
- Overridden files are marked with a special icon (e.g., a purple dot).

#### 6. Tips

- **Disable cache** in the Network tab to ensure your changes are always loaded.
- If the file name changes (e.g., new version), repeat the override process for the new file.
- You can use this method to override other static assets (CSS, images, etc.) as well.

---

> **Note:** This workflow is useful for debugging, reverse engineering, or customizing client-side behavior without modifying server files.

## Exposing Singletons from IIFEs

When reverse engineering JavaScript applications, you'll often encounter code wrapped in Immediately Invoked Function Expressions (IIFEs) that contain singleton instances with useful functions. To access these functions outside the IIFE scope, you need to expose them strategically.

### Method 1: Global Scope Exposure (Simplest)

The most straightforward approach is to expose singleton instances to the global scope:

```javascript
// Inside the IIFE, add this line to expose a singleton
window.gameManager = gameManagerInstance;

// Later, outside the IIFE, you can access it
gameManager.someFunction();
```

**Pros:**
- Minimal code changes
- Easy to implement
- Direct access to all singleton methods

**Cons:**
- Pollutes global scope
- Can conflict with other global variables
- Less maintainable in larger applications

### Method 2: Namespace Pattern (Recommended)

Create a dedicated namespace to avoid global pollution:

```javascript
// Inside the IIFE
window.HighSpellAPI = window.HighSpellAPI || {};
window.HighSpellAPI.gameManager = gameManagerInstance;
window.HighSpellAPI.networkManager = networkManagerInstance;

// Outside the IIFE
HighSpellAPI.gameManager.someFunction();
HighSpellAPI.networkManager.sendMessage();
```

**Pros:**
- Organized and namespaced
- Reduces global scope pollution
- Easy to manage multiple singletons
- Clear API surface

**Cons:**
- Slightly more code
- Still uses global scope (but organized)

### Method 3: Proxy Object Pattern (Advanced)

Create a proxy object that provides controlled access to singleton methods:

```javascript
// Inside the IIFE
window.HighSpellProxy = new Proxy({}, {
  get: function(target, prop) {
    switch(prop) {
      case 'game':
        return gameManagerInstance;
      case 'network':
        return networkManagerInstance;
      case 'inventory':
        return inventoryManagerInstance;
      default:
        return undefined;
    }
  }
});

// Outside the IIFE
HighSpellProxy.game.someFunction();
HighSpellProxy.network.sendMessage();
```

**Pros:**
- No direct global exposure of singletons
- Controlled access to functionality
- Can add validation, logging, or security checks
- Easy to extend with new methods

**Cons:**
- More complex implementation
- Slightly more verbose usage

### Method 4: Module Pattern with Exports

For more sophisticated setups, use a module-like pattern:

```javascript
// Inside the IIFE
window.HighSpellExports = {
  getGameManager: () => gameManagerInstance,
  getNetworkManager: () => networkManagerInstance,
  getInventoryManager: () => inventoryManagerInstance,
  
  // Or expose specific methods directly
  sendMessage: (msg) => networkManagerInstance.sendMessage(msg),
  getPlayerPosition: () => gameManagerInstance.getPlayerPosition()
};

// Outside the IIFE
const gameManager = HighSpellExports.getGameManager();
gameManager.someFunction();

// Or use direct method access
HighSpellExports.sendMessage("Hello World");
```

**Pros:**
- Encapsulated access
- Can expose only specific methods
- Easy to add wrapper functionality
- Good for API design

**Cons:**
- More code to maintain
- Requires careful API design

### Practical Example: HighSpell Client

Here's how you might expose key singletons from the HighSpell client:

```javascript
// Add this at the end of the client IIFE
window.HighSpellAPI = {
  // Core managers
  gameManager: gameManager,
  networkManager: networkManager,
  worldManager: worldManager,
  
  // Utility functions
  sendMessage: (type, data) => networkManager.send(type, data),
  getPlayer: () => gameManager.getPlayer(),
  getWorld: () => worldManager.getWorld(),
  
  // Event system access
  on: (event, callback) => eventManager.on(event, callback),
  off: (event, callback) => eventManager.off(event, callback)
};

// Usage outside the IIFE
HighSpellAPI.sendMessage('move', { x: 100, y: 200 });
const player = HighSpellAPI.getPlayer();
HighSpellAPI.on('playerMove', (data) => console.log('Player moved:', data));
```

### Troubleshooting

- **Singleton not found**: Ensure you're exposing the singleton after it's initialized
- **Function undefined**: Check that the function exists on the singleton before exposing
- **Timing issues**: Make sure the IIFE has completed execution before trying to access exposed functions

### Immediate Actions
1. **Set up your development environment to handle *very* large JS files**
2. **Familiarize yourself with the tools at your disposal**
3. **Start with basic analysis**

### Learning Resources
- **Browser Developer Tools documentation**
- **JavaScript debugging techniques**
- **Network protocol analysis**
- **Web application security**


## Troubleshooting

### Getting Help
- **Check existing documentation** first
- **Ask specific questions** with context
- **Provide reproducible examples** when possible

---
