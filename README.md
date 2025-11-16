# Kikiriki - Speech-to-Text Push-to-Talk Service

A simple, efficient push-to-talk speech-to-text solution for Ubuntu Linux using whisper.cpp and xbindkeys.

## Features

✅ **Simple Push-to-Talk** - Press and hold a keyboard shortcut to record, release to transcribe  
✅ **Customizable Hotkeys** - Easy configuration via `kikiriki-xbindkeys.conf`  
✅ **Audio & Visual Feedback** - Sound effects and desktop notifications  
✅ **Auto-Paste** - Transcribed text automatically inserted into any application  
✅ **Lightweight** - No Python dependencies, just standard Linux tools  
✅ **Privacy-Focused** - All processing happens locally using whisper.cpp  

## Table of Contents

- [Requirements](#requirements)
- [Quick Start](#quick-start)
  - [1. Install](#1-install)
  - [2. Start the service](#2-start-the-service)
  - [3. Test It!](#3-test-it)
- [Configuration](#configuration)
  - [Changing the Keyboard Shortcut](#changing-the-keyboard-shortcut)
  - [Customizing Whisper Settings](#customizing-whisper-settings)
- [Usage](#usage)
  - [Service Management](#service-management)
  - [Normal Operation](#normal-operation)
  - [Manual Testing](#manual-testing)
  - [Troubleshooting](#troubleshooting)
- [Advanced Usage](#advanced-usage)
  - [Different Whisper Models](#different-whisper-models)
  - [Using Different Audio Devices](#using-different-audio-devices)
  - [Clipboard Mode Instead of Auto-Paste](#clipboard-mode-instead-of-auto-paste)
- [Uninstall](#uninstall)
- [Credits](#credits)
- [License](#license)

## Requirements

- Ubuntu Linux (or Debian-based distribution)
- [whisper.cpp](https://github.com/ggml-org/whisper.cpp) installed and built
- Whisper model downloaded (e.g., `ggml-base.en.bin`)

## Quick Start

### 1. Install

Run the setup script:

```bash
git clone https://github.com/rlaffers/kikiriki.git
cd kikiriki
./install
```

This will:
- Install required system dependencies (xbindkeys, xdotool, etc.)
- Configure the local xbindkeys config file
- Set up systemd user service for auto-start on login
- Make all scripts executable

### 2. Start the service

```bash
systemctl --user start kikiriki.service
```

Or it will start automatically on next login (if enabled during installation).

### 3. Test It!

1. **Press and hold** `Ctrl+Space`
2. **Speak** what you want to transcribe
3. **Release** the keys
4. Your text will be inserted where your cursor is!

## Configuration

### Changing the Keyboard Shortcut

Edit `~/.local/kikiriki/kikiriki-xbindkeys.conf`:

```bash
nano ~/.local/kikiriki/kikiriki-xbindkeys.conf
```

The default configuration uses `Ctrl+Space` with the keyhold-handler for debouncing:

```bash
# Hold Ctrl+Space to record, release to transcribe
"/home/your_name/.local/kikiriki/keyhold-handler press /home/your_name/.local/kikiriki/kikiriki start"
  Control + space

"/home/richard/Projects/kikiriki/keyhold-handler release /home/your_name/.local/kikiriki/kikiriki stop"
  release+Control + space
```

Alternative hotkey examples:

```bash
# Option 1: Use Ctrl+Alt+G
"/home/your_name/.local/kikiriki/keyhold-handler press /home/your_name/.local/kikiriki/kikiriki start"
  Control+Alt + g

"/home/your_name/.local/kikiriki/keyhold-handler release /home/your_name/.local/kikiriki/kikiriki stop"
  release+Control+Alt + g
```

**Important Notes:**
- The `keyhold-handler` wrapper is used to debounce repeated key events from xbindkeys
- Use full absolute paths in the configuration (replace `/home/your_name/.local/kikiriki/` with your actual installation path)

After editing, restart the service:

```bash
systemctl --user restart kikiriki.service
```

### Customizing Whisper Settings

Edit the configuration section in `kikiriki`:

```bash
nano ~/.local/kikiriki/kikiriki
```

Key settings you can change:

```bash
# Whisper configuration
WHISPER_MODEL="${HOME}/.local/share/whisper.cpp/models/ggml-base.en.bin"  # Model path
WHISPER_BIN="${HOME}/.local/share/whisper.cpp/build/bin/whisper-cli"      # Executable path
WHISPER_THREADS=8                                             # CPU threads to use
WHISPER_LANG="en"                                             # Language code

# Audio settings
SAMPLE_RATE=16000                                             # Audio sample rate
AUDIO_DEVICE="default"                                        # ALSA device name

# Feedback
ENABLE_NOTIFICATIONS=true                                     # Desktop notifications
ENABLE_SOUNDS=true                                            # Audio feedback

# Output
AUTO_PASTE=true                                               # Auto-insert text (vs clipboard)
```

**Notification Modes:**
- By default, the script runs in non-verbose mode and only shows critical error notifications
- Use the `-v` flag to enable verbose mode and see all notifications including status updates
- You can disable all notifications by setting `ENABLE_NOTIFICATIONS=false`

## Usage

### Service Management

Control the service using systemd:

```bash
# Start the service
systemctl --user start kikiriki.service

# Stop the service
systemctl --user stop kikiriki.service

# Restart the service (after config changes)
systemctl --user restart kikiriki.service

# Check service status
systemctl --user status kikiriki.service

# View logs
journalctl --user -u kikiriki.service -f

# Enable auto-start on login
systemctl --user enable kikiriki.service

# Disable auto-start
systemctl --user disable kikiriki.service
```

### Normal Operation

1. Ensure the service is running: `systemctl --user status kikiriki.service`
2. Press and hold your configured hotkey (`Ctrl+Space` by default)
3. **Press and hold** `Ctrl+Space`
4. **Speak** clearly
5. **Release** the keys
6. Your text will be inserted where your cursor is!

### Manual Testing

You can test the script manually without xbindkeys:

```bash
# Start recording
~/.local/kikiriki/kikiriki start

# Speak something...

# Stop and transcribe
~/.local/kikiriki/kikiriki stop

# Test with verbose notifications
~/.local/kikiriki/kikiriki -v start
# Speak...
~/.local/kikiriki/kikiriki -v stop

# Show help
~/.local/kikiriki/kikiriki -h
```

**Command line options:**
- `-v` - Verbose mode (show all notifications including status updates)
- `-h` - Show help message

### Troubleshooting

**No audio recorded:**
- Check microphone permissions
- Test with: `arecord -d 5 test.wav && aplay test.wav`
- Verify AUDIO_DEVICE setting in the script

**Transcription fails:**
- Check whisper.cpp path: `ls ~/.local/share/whisper.cpp/build/bin/whisper-cli`
- Check model path: `ls ~/.local/share/whisper.cpp/models/ggml-base.en.bin`
- Run script manually to see error messages

**Hotkey not working:**
- Verify the service is running: `systemctl --user status kikiriki.service`
- Check configuration: `cat ~/.local/kikiriki/kikiriki-xbindkeys.conf`
- Restart the service: `systemctl --user restart kikiriki.service`
- Check logs: `journalctl --user -u kikiriki.service -n 50`
- Look for conflicts with desktop environment shortcuts

**Text not pasting:**
- Check if xdotool is installed: `which xdotool`
- Try disabling AUTO_PASTE to use clipboard mode instead
- Some applications may need focus delay (adjust `sleep 0.1` in script)

## Advanced Usage

### Different Whisper Models

Download different models from whisper.cpp:

```bash
cd ~/.local/share/whisper.cpp
./models/download-ggml-model.sh tiny.en    # Fastest, least accurate
./models/download-ggml-model.sh small.en   # Good balance
./models/download-ggml-model.sh base.en    # Default
./models/download-ggml-model.sh medium.en  # Better accuracy
./models/download-ggml-model.sh large      # Best, slowest
```

Then update `WHISPER_MODEL` path in `kikiriki`.

### Using Different Audio Devices

List available audio devices:

```bash
arecord -L
```

Update `AUDIO_DEVICE` in the script to match your preferred device.

### Clipboard Mode Instead of Auto-Paste

Set `AUTO_PASTE=false` in the script to copy transcribed text to clipboard instead of auto-pasting.

## Uninstall

Run the uninstall script:

```bash
~/.local/kikiriki/uninstall
```

## Credits

- [whisper.cpp](https://github.com/ggml-org/whisper.cpp) - Fast local speech recognition

## License

This project is licensed under the GNU Affero General Public License v3.0 (AGPLv3).
See the [LICENSE](LICENSE) file for details.
