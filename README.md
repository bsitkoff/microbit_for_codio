# micro:bit Codio Flasher

A web-based tool for flashing MicroPython code to micro:bit V2 devices directly from Codio's browser-based IDE. Perfect for middle school classroom environments using Chromebooks.

## Overview

This tool allows students to:
1. Edit Python code in Codio's editor
2. **Test code in a browser-based micro:bit simulator**
3. **Flash code directly to a physical micro:bit via WebUSB**
4. No drag-and-drop files required (essential for Chromebooks in Codio)

## How It Works

Uses the official micro:bit Python Editor V3 architecture:
- **[@microbit/microbit-connection](https://github.com/microbit-foundation/microbit-connection)** - WebUSB communication and partial flashing
- **[@microbit/microbit-fs](https://github.com/microbit-foundation/microbit-fs)** - Embeds Python code into MicroPython firmware
- **ES Modules via esm.sh** - Loads dependencies directly in the browser, no build step required

## Quick Start

### 1. Setup in Codio

Place these files in your Codio project:
```
/
├── index.html                    # The flasher interface
├── simulator.html                # The simulator interface
├── microbit-micropython-v2.hex   # MicroPython V2 firmware
└── main.py                       # Your Python code (or any .py file)
```

### 2. Serve the Files

In Codio, you can use the built-in web server or run:
```bash
python3 -m http.server 3000
```

### 3. Open in Browser

**For Simulator (any browser):**
- Navigate to `simulator.html`

**For Physical Device (Chrome/Edge only):**
- Navigate to `index.html` in **Chrome** or **Edge** (WebUSB requirement)

### 4. Test in Simulator

1. Open `simulator.html`
2. Click **"Load and Run"** - it will read and run your `main.py`
3. Interact with the virtual micro:bit (buttons, sensors)
4. Click **"Stop"** to halt execution

### 5. Flash to Physical Device

1. Open `index.html` in Chrome or Edge
2. Click **"Connect to micro:bit"** and select your device
3. Click **"Flash Code"** - it will read and flash your `main.py`
4. Wait for "Flash complete!" message

## Usage Options

### Flash a Different File

Add `?file=yourfile.py` to the URL for both simulator and flasher:
```
simulator.html?file=student_project.py
index.html?file=student_project.py
```

### Browser Requirements

**For Physical Device Flashing (index.html):**
- **Required:** Chrome 89+ or Edge 89+ (WebUSB support)
- **Not supported:** Firefox, Safari (no WebUSB)
- **Works on:** Windows, Mac, Linux, ChromeOS

**For Simulator (simulator.html):**
- **Works in:** All modern browsers (Chrome, Firefox, Safari, Edge)
- **No special requirements**

### Hardware Requirements

- micro:bit V2 (the newer version with speaker and touch sensor)
- USB cable
- For V1 support: modify `microbitBoardId.V2` to `microbitBoardId.V1` in index.html:223

## Features

### Simulator (simulator.html)
✅ **Browser-based Testing** - No hardware required
✅ **Full MicroPython Support** - Test all micro:bit features
✅ **Instant Feedback** - See code run immediately
✅ **Works Everywhere** - Any modern browser

### Physical Device Flasher (index.html)
✅ **Partial Flashing** - Only updates changed regions (much faster after first flash)
✅ **Progress Tracking** - Visual progress bar with percentage
✅ **Error Handling** - Clear error messages for students
✅ **File Integration** - Reads from Codio's filesystem, not a textarea
✅ **No Build Step** - Pure ES modules loaded from CDN

## Technical Details

### Architecture

The flashing process:

1. **Load Firmware** - Fetches `microbit-micropython-v2.hex` (1.2MB base firmware)
2. **Read Code** - Fetches the Python file via `fetch()`
3. **Embed Code** - Uses `MicropythonFsHex` to inject code into firmware filesystem
4. **Generate Hex** - Creates Intel Hex with proper board ID (0x9903 for V2)
5. **Flash Device** - Uses WebUSB + DAPjs with partial flashing optimization

### Why This Approach Works

Previous attempts failed because:
- ❌ Old `microbit-fs` bundles couldn't parse Universal Hex format
- ❌ DAPjs alone requires manual hex preparation
- ❌ Wrong board IDs caused "offset out of bounds" errors

This solution uses:
- ✅ Official `@microbit/microbit-connection` library (same as Python Editor V3)
- ✅ Proper board ID assignment in firmware initialization
- ✅ Partial flashing for speed

### Limitations

- **V2 Only** - Currently configured for micro:bit V2 (change board ID for V1)
- **Single File** - Flashes one Python file (though you can add multi-file support)
- **Chrome/Edge Only** - WebUSB API limitation

## Classroom Deployment

### For Teachers

1. **Create Codio Assignment**:
   - Include `index.html` and `microbit-micropython-v2.hex`
   - Students edit their own `main.py`
   - Students open `index.html` to flash

2. **Student Workflow**:
   ```
   Edit code → Save → Test in simulator.html → Flash with index.html → Test on physical micro:bit
   ```

3. **Troubleshooting**:
   - "WebUSB not supported" → Use Chrome or Edge
   - "Failed to read main.py" → Make sure file exists and is saved
   - "Board ID not found" → Check you're using micro:bit V2

### Security Note

WebUSB requires HTTPS or localhost. Codio's preview URLs work because they use HTTPS.

## File Descriptions

| File | Purpose | Size |
|------|---------|------|
| `index.html` | Web interface for flashing to physical device | 8KB |
| `simulator.html` | Web interface for browser-based simulator | 11KB |
| `microbit-micropython-v2.hex` | MicroPython V2 firmware base | 1.2MB |
| `main.py` | Example Python program (boat animation) | <1KB |

## Credits

Built using official micro:bit Foundation libraries:
- [Python Editor V3](https://github.com/microbit-foundation/python-editor-v3)
- [@microbit/microbit-connection](https://github.com/microbit-foundation/microbit-connection)
- [@microbit/microbit-fs](https://github.com/microbit-foundation/microbit-fs)
- [micro:bit Python Simulator](https://python-simulator.usermbit.org/) (embedded in simulator.html)

Special thanks to the micro:bit Educational Foundation for open-sourcing these tools.

## License

MIT License - feel free to use in your classroom!

## Support

For issues or questions:
1. Check the browser console for detailed error messages
2. Verify you're using Chrome/Edge with WebUSB support
3. Confirm the Python file exists and is valid MicroPython code
4. Make sure you're using a micro:bit V2

## Future Enhancements

Possible additions:
- [ ] Multi-file project support
- [ ] V1 auto-detection
- [ ] Serial console output display
- [ ] Code validation before flash
- [ ] Offline mode with service worker
