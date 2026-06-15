# TTS Mobile Helper - Tabletop Simulator Mobile Companion

A powerful Flask-based server that transforms your Tabletop Simulator gameplay by allowing players to control their hand cards on smartphones while playing on a shared screen. Perfect for local multiplayer gaming where you need privacy and convenience!

## ✨ Features

- 🎴 **Real-time Card Synchronization** - Cards update instantly across all connected devices
- 📱 **Multi-Player Support** - Each player gets their own smartphone interface (Admin can control all players)
- 🔐 **Privacy** - Other players cannot see your hand cards on your phone
- 🌐 **Offline-First Architecture** - Works without internet (only requires local network)
- 🎯 **Interactive Card Actions** - Drag, drop, trade, and give cards between players
- 🔘 **Custom Buttons** - Create interactive buttons in TTS that can be triggered from phones
- 📍 **Drop Zones** - Visual zones where players can drop/discard cards
- 🌍 **12 Languages Supported** - German, English, Spanish, French, Italian, Portuguese, Dutch, Polish, Russian, Chinese (Simplified), Japanese, Korean
- ⚡ **WebSocket Communication** - Instant real-time updates using Socket.IO
- 📸 **Local Image Cache** - Efficiently caches TTS card images for better performance
- 🔗 **QR Code Access** - Easy setup with auto-generated QR codes for mobile devices

## 🏗️ Architecture Overview

### Components

| Component | Purpose |
|-----------|---------|
| `server_mobile_actions.py` | Main Flask server with WebSocket (SocketIO) handling HTTP endpoints and real-time updates |
| `mobile_ui.html` | Responsive HTML5 interface for smartphones (displays cards, buttons, interactions) |
| `tts_api_chatgpt.py` | Bridge library for TTS External Editor API communication |
| `helper_object_mobile_actions.lua` | Lua script running inside TTS that sends/receives data |
| `socket.io.js` | Real-time bidirectional communication library |
| `localization/` | Translation files for 12 languages |

### Communication Flow

```
TTS Game
    ↓ (HTTP POST: /update_hand, /update_drop_zones, /update_mobile_buttons)
    ↓
Flask Server (server_mobile_actions.py)
    ↓ (WebSocket via SocketIO - real-time sync)
    ↓
Mobile Phones (Browser at http://192.168.x.x:5001)
    ↓ (User interactions: card_action, interaction_request, etc)
    ↓
Flask Server processes action
    ↓ (TTS API command)
    ↓
TTS Game executes Lua script
```

## 🚀 Quick Start

### Prerequisites

- **Tabletop Simulator** installed on Steam
- **Python 3.8+** (or use pre-compiled `TTSMobileHelper.exe`)
- **Local Network** - PC/Laptop and mobile phones on same WiFi
- **Mobile Browser** - Any modern smartphone with WiFi connection

### Installation

#### Option A: Using Pre-compiled Executable (Easiest)
1. Download `TTSMobileHelper.exe` from releases
2. Run the executable
3. Follow the steps below from "Starting the Server"

#### Option B: Using Python

1. **Clone or download** this project folder

2. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

3. **Navigate to project directory**
   ```bash
   cd path/to/TTS_Mobile_Helper
   ```

### Setting Up TTS

1. **Subscribe to Workshop Script** (Optional but recommended)
   - https://steamcommunity.com/sharedfiles/filedetails/?id=3739147243

2. **Load the Script in Your Game**
   - Option A: Load the workshop content in your game
   - Option B: Copy contents of `helper_object_mobile_actions.lua` into any TTS object's script

### Starting the Server

#### Basic Start
```bash
python server_mobile_actions.py
```

#### With Custom IP (if auto-detection fails)
```bash
python server_mobile_actions.py --host 192.168.1.100
```

#### With Debug Output (for troubleshooting)
```bash
python server_mobile_actions.py --debug-tts-api
```

### Connecting Phones

1. Server will display QR codes in the console (two codes appear)
   - **Admin QR** - Full control over all players
   - **Player QR** - Control only your own color

2. **Scan with your phone** - Opens a browser window automatically

3. **Select your player color** and you're ready to play!

## 🧪 Testing & Verification

### Test 1: Server is Running
```bash
# In another terminal, check if server responds
curl http://localhost:5001/health

# Expected: {"status": "ok"}
```

### Test 2: Local Network Access
1. On PC, open browser: `http://localhost:5001`
2. Find your PC's local IP: `ipconfig` (Windows) or `ifconfig` (Linux/Mac)
3. On phone, navigate to: `http://<YOUR_PC_IP>:5001`
4. You should see the TTS Mobile interface (may show "waiting for TTS connection")

### Test 3: TTS Connection
1. Start the server
2. Open TTS game
3. In server console, you should see: `[VERBINDUNG] Admin-Smartphone fuer Red angemeldet.` (or similar)
4. Console should show: `SERVER GESTARTET! Lokale IP: 192.168.x.x`

### Test 4: Card Synchronization
1. Server running and phone connected
2. In TTS, add some cards to your hand
3. Cards should **instantly appear on phone screen**
4. Try dragging a card on phone → it should move in TTS

### Test 5: Card Interactions
1. **Give Card to Player**: Select card → choose target player
2. **Drop Zone**: If configured, should appear on phone
3. **Mobile Buttons**: Any buttons in TTS should appear on phone
4. **Requests**: Ask other player for random cards → see request on their phone

### Troubleshooting Tests

| Problem | Solution |
|---------|----------|
| Phone can't find server | Check same WiFi, try `--host` with manual IP |
| Cards don't appear | Check Lua script is loaded in TTS object |
| No QR codes displayed | Server may have crashed, check console for errors |
| "Connection lost" message | TTS disconnected, restart game or check Lua script |
| Slow performance | Too many cards/players, or WiFi too far away |

## 📖 Configuration & Customization

### Defining Card Drop Zones in TTS

Drop zones allow players to drag cards to specific areas in the game.

1. In TTS, create a **Snap Point** on an object
2. Right-click the snap point and add a tag
3. Use one of these tag formats:

   **For all players:**
   ```
   TTSmobile_MyDropZone
   ```
   (Shows as "MyDropZone" on all phones)

   **For specific players:**
   ```
   TTSmobile_MyDropZone_For_Yellow_Red
   ```
   (Shows only on Yellow and Red player phones)

4. **Note**: If you want snap functionality to work, create a second snap point at the same position without a tag

### Displaying Custom Buttons on Mobile

TTS buttons automatically appear on phones. Control visibility with tags on the object containing the buttons:

**Show button for specific players only:**
```
TTSmobile_For_Orange
TTSmobile_For_Red_Blue
```

**Hide buttons completely (for helper buttons):**
```
TTSmobileHideButtons
```

### Language Support

The app automatically detects your system language and displays in that language. Supported languages:
- 🇩🇪 Deutsch (German)
- 🇬🇧 English
- 🇪🇸 Español (Spanish)
- 🇫🇷 Français (French)
- 🇮🇹 Italiano (Italian)
- 🇵🇹 Português (Portuguese)
- 🇳🇱 Nederlands (Dutch)
- 🇵🇱 Polski (Polish)
- 🇷🇺 Русский (Russian)
- 🇨🇳 中文简体 (Chinese Simplified)
- 🇯🇵 日本語 (Japanese)
- 🇰🇷 한국어 (Korean)

To override: Add `?lang=en` to the URL or set in admin panel

## 🔧 Advanced Configuration

### Command-Line Arguments

```bash
python server_mobile_actions.py [options]

--host <IP>
  Use specific IP instead of auto-detection
  Example: --host 192.168.1.100

--debug-tts-api
  Enable debug output for incoming AND outgoing TTS API messages
  Useful for troubleshooting TTS communication

--debug-tts-incoming
  Only debug incoming messages from TTS

--debug-tts-outgoing
  Only debug outgoing messages to TTS
```

### Important Constants

In `server_mobile_actions.py`, you can adjust:

```python
PORT = 5001                          # Server port
INTERACTION_TTL_SECONDS = 90         # Card request timeout
INTERACTION_MAX_CARD_COUNT = 10      # Max cards per request
TTS_IMAGE_CACHE_DIRS = [...]         # Where to cache TTS images
```

### Security Notes

- ⚠️ **Use only on trusted local networks** (home WiFi)
- ⚠️ **Not recommended for public/open networks**
- ✅ Built-in Content-Security-Policy headers
- ✅ No credentials stored
- ✅ All data stays on local network

## 📊 API Reference

### HTTP Endpoints

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/health` | GET/POST | Server health check |
| `/update_hand` | POST | TTS sends card updates |
| `/update_drop_zones` | POST | TTS sends drop zone updates |
| `/update_mobile_buttons` | POST | TTS sends button updates |
| `/local_image/<filename>` | GET | Serve cached TTS images |
| `/socket.io.js` | GET | Serve Socket.IO library |
| `/assign_player` | GET | Player color assignment |
| `/` | GET | Main mobile interface |

### WebSocket Events (Socket.IO)

**Client → Server:**
- `join` - Connect to game (send color, client_id, language)
- `card_action` - Perform action on card(s)
- `interaction_request` - Request cards from another player
- `interaction_response` - Accept/reject card request
- `interaction_pick_cards` - Select which cards to give
- `mobile_button_action` - Click a TTS button
- `admin_sync_all_hands` - Force refresh all cards (admin only)

**Server → Client:**
- `hand_updated` - Cards, players, drop zones, buttons
- `interaction_updated` - Pending interaction requests
- `interaction_result` - Result of your action
- `interaction_open_pick` - Select cards to give to requesting player

## 🐛 Debugging

### Enable Full Debug Mode
```bash
python server_mobile_actions.py --debug-tts-api
```

Then in the Lua script in TTS, set:
```lua
local Debug = true
```

This will log:
- ✅ All incoming/outgoing TTS API messages
- ✅ Server-side communication details
- ✅ Card state changes
- ✅ Connection events

### Check Server Logs

The console shows messages like:
```
[START] Starte den Catan Companion Server auf Port 5001...
[VERBINDUNG] Spieler-Smartphone fuer Red angemeldet.
[NETZWERK] Hand von Spieler Red aktualisiert (5 Karten).
[ACTION] Red: give_to_player -> ['abc123'], target=Blue
```

Key prefixes:
- `[START]` - Server initialization
- `[VERBINDUNG]` - Connection/disconnection
- `[NETZWERK]` - Data updates
- `[ACTION]` - Player actions
- `[WARNUNG]` - Warnings
- `[FEHLER]` - Errors

### Network Debugging

**Check if phone can reach server:**
```bash
# On phone, visit: http://<SERVER_IP>:5001/health
# Should return: {"status": "ok"}
```

**Find your local IP:**
- Windows: `ipconfig` → look for IPv4 Address
- Mac/Linux: `ifconfig` → look for inet address
- Usually starts with `192.168.x.x` or `10.x.x.x`

## 🤝 Contributing & Credits

Built with help of:
- ChatGPT-Codex
- [TTS Official API Documentation](https://api.tabletopsimulator.com/)
- [LucasOe's TTS External API Wrapper](https://github.com/LucasOe/tts-external-api)

## 📝 Dependencies

```
Flask==3.1.3
Flask-SocketIO==5.6.1
python-engineio==4.13.2
qrcode==8.2
```

## ⚖️ License

[Your License Here]

## 🎮 More Info

- Official TTS Docs: https://api.tabletopsimulator.com/intro/
- Workshop Script: https://steamcommunity.com/sharedfiles/filedetails/?id=3739147243

---

## FAQ

**Q: Do I need the internet?**
A: Only for the first download. The server works completely offline on local networks.

**Q: Can I use this with my friends online?**
A: No, this requires a local network. For online play, stream your screen instead.

**Q: How many players can connect?**
A: Limited only by your WiFi. Tested with 4-6 players without issues.

**Q: Can I run this on a Raspberry Pi?**
A: Yes! Python runs on Raspberry Pi. Just install dependencies with pip.

**Q: Does it work on MacOS/Linux?**
A: Yes! TTS runs on Windows, but the server runs on any Python platform.

---

Made with ❤️ for Tabletop Simulator players

