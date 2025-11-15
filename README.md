# Speech-to-Text Push-to-Talk

A simple, efficient push-to-talk speech-to-text solution for Ubuntu Linux using whisper.cpp and xbindkeys.

## Features

✅ **Simple Push-to-Talk** - Press and hold a keyboard shortcut to record, release to transcribe  
✅ **Customizable Hotkeys** - Easy configuration via `.xbindkeysrc`  
✅ **Audio & Visual Feedback** - Sound effects and desktop notifications  
✅ **Auto-Paste** - Transcribed text automatically inserted into any application  
✅ **Lightweight** - No Python dependencies, just standard Linux tools  
✅ **Privacy-Focused** - All processing happens locally using whisper.cpp  

## Requirements

- Ubuntu Linux (or Debian-based distribution)
- [whisper.cpp](https://github.com/ggml-org/whisper.cpp) installed and built
- Whisper model downloaded (e.g., `ggml-base.en.bin`)

## Quick Start

### 1. Install

Run the setup script:

```bash
cd ~/Projects/kikiriki
./install
```

This will:
- Install required system dependencies (xbindkeys, xdotool, etc.)
- Configure the local xbindkeys config file
- Set up auto-start on login
- Make all scripts executable

### 2. Start the service

```bash
~/Projects/kikiriki/kikiriki-start
```

Or it will start automatically on next login.

### 3. Test It!

1. **Press and hold** `Ctrl+Space`
2. **Speak** what you want to transcribe
3. **Release** the keys
4. Your text will be inserted where your cursor is!

## Configuration

### Changing the Keyboard Shortcut

Edit `~/Projects/kikiriki/xbindkeysrc`:

```bash
nano ~/Projects/kikiriki/xbindkeysrc
```

Example configurations:

```bash
# Use Ctrl+Space
"~/Projects/kikiriki/kikiriki start"
  Control + space

"~/Projects/kikiriki/kikiriki stop"
  Control + space + Release

# Use Super (Windows) + V
"~/Projects/kikiriki/kikiriki start"
  Mod4 + v

"~/Projects/kikiriki/kikiriki stop"
  Mod4 + v + Release
```

After editing, reload with the start script:

```bash
~/Projects/kikiriki/kikiriki-stop
~/Projects/kikiriki/kikiriki-start
```

### Customizing Whisper Settings

Edit the configuration section in `kikiriki`:

```bash
nano ~/Projects/kikiriki/kikiriki
```

Key settings you can change:

```bash
# Whisper configuration
WHISPER_MODEL="${HOME}/whisper.cpp/models/ggml-base.en.bin"  # Model path
WHISPER_BIN="${HOME}/whisper.cpp/build/bin/whisper-cli"      # Executable path
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

## Usage

### Normal Operation

1. Start the service: `~/Projects/kikiriki/kikiriki-start`
2. Press and hold your configured hotkey
3. Speak clearly
4. Release the hotkey
5. Wait for transcription (notification will appear)
6. Text is automatically inserted or copied to clipboard

### Manual Testing

You can test the script manually without xbindkeys:

```bash
# Start recording
~/Projects/kikiriki/kikiriki start

# Speak something...

# Stop and transcribe
~/Projects/kikiriki/kikiriki stop
```

### Troubleshooting

**No audio recorded:**
- Check microphone permissions
- Test with: `arecord -d 5 test.wav && aplay test.wav`
- Verify AUDIO_DEVICE setting in the script

**Transcription fails:**
- Check whisper.cpp path: `ls ~/whisper.cpp/build/bin/whisper-cli`
- Check model path: `ls ~/whisper.cpp/models/ggml-base.en.bin`
- Run script manually to see error messages

**Hotkey not working:**
- Verify the service is running: `pgrep -f "xbindkeys.*kikiriki"`
- Check configuration: `cat ~/Projects/kikiriki/xbindkeysrc`
- Restart the service: `~/Projects/kikiriki/kikiriki-stop && ~/Projects/kikiriki/kikiriki-start`
- Look for conflicts with desktop environment shortcuts

**Text not pasting:**
- Check if xdotool is installed: `which xdotool`
- Try disabling AUTO_PASTE to use clipboard mode instead
- Some applications may need focus delay (adjust `sleep 0.1` in script)

## File Structure

```
~/Projects/kikiriki/
├── kikiriki              # Main script (handles recording & transcription)
├── install               # Installation & setup script
├── kikiriki-start             # Start the service
├── kikiriki-stop              # Stop the service
├── xbindkeysrc           # Keyboard shortcuts config (edit this!)
├── xbindkeysrc.example   # Example configurations
├── INSTALL.txt           # Quick start guide
├── README.md             # This file

~/.local/state/kikiriki/         # Runtime state files
~/.config/autostart/kikiriki.desktop  # Auto-start entry
```

## Advanced Usage

### Different Whisper Models

Download different models from whisper.cpp:

```bash
cd ~/whisper.cpp
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

```bash
# Stop the service
~/Projects/kikiriki/kikiriki-stop

# Remove autostart
rm ~/.config/autostart/kikiriki.desktop

# Remove the scripts
rm -rf ~/Projects/kikiriki
```

## Credits

- [whisper.cpp](https://github.com/ggml-org/whisper.cpp) - Fast local speech recognition

## License

This project is licensed under the GNU Affero General Public License v3.0 (AGPLv3).
See the [LICENSE](LICENSE) file for details.
