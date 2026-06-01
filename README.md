# VirtualCam 🎥

**Universal Virtual Camera** — turns any video into a virtual webcam on Linux and Windows.

```
 _    ___      __              ________
| |  / (_)____/ /___  ______ _/ / ____/___ _____ ___
| | / / / ___/ __/ / / / __`/ / /   / __`/ __`__ \
| |/ / / /  / /_/ /_/ / /_/ / / /___/ /_/ / / / / / /
|___/_/_/   \__/\__,_/\__,_/_/\____/\__,_/_/ /_/ /_/
```

## Features

- **Cross-platform** — Linux (v4l2loopback) and Windows (softcam / Unity Capture / OBS)
- **No OBS Studio required** on Windows — uses softcam (MIT) or Unity Capture (MIT)
- **Playlist** — sequential or random mode with auto-advance
- **Loop** — infinite video replay
- **Configurable FPS** — 1 to 120 frames per second
- **Text overlay** — custom text on top of the video
- **Graphical folder picker** — native tkinter dialog on Linux and Windows
- **Hotkeys** — switch video, folder, resolution, and FPS without stopping the stream
- **Persistent config** — saves preferences to `~/.virtualcam/config.json`
- **CLI mode** — scripting and automation with command-line arguments

## Installation

### Linux

```bash
# system dependencies
sudo apt install ffmpeg v4l2loopback-dkms v4l2loopback-utils

# clone and run
git clone https://github.com/yourusername/virtualcam.git
cd virtualcam
chmod +x virtualcam
./virtualcam
```

### Windows

```bash
# 1. Install ffmpeg (https://ffmpeg.org/download.html)
# 2. Pick a virtual camera backend:

# Option A — softcam (recommended, MIT, no OBS)
pip install softcam numpy
# Download driver: https://github.com/tshino/softcam

# Option B — Unity Capture + pyvirtualcam (MIT, no OBS)
pip install pyvirtualcam numpy
# Download and register: https://github.com/schellingb/UnityCapture

# Option C — OBS Virtual Camera (automatic fallback)
pip install pyvirtualcam numpy
# Download plugin: https://obsproject.com
```

### macOS

```bash
pip install pyvirtualcam numpy
```

## Usage

### Interactive mode

```bash
./virtualcam
```

1. Choose the video folder (native file dialog)
2. Select resolution
3. Set FPS
4. Pick a video
5. Done! The virtual camera is now active

### CLI mode

```bash
./virtualcam --dir ~/Videos --video video.mp4 --resolution 1920x1080 --fps 30 --loop --shuffle
```

### Arguments

| Argument | Description |
|----------|-------------|
| `-d, --dir DIR` | Video folder path |
| `-v, --video VIDEO` | Specific video file |
| `-r, --resolution WxH` | Resolution (e.g. `1920x1080`) |
| `--fps FPS` | Frames per second (1-120) |
| `--loop` | Loop video |
| `--no-loop` | Don't loop |
| `--shuffle` | Random mode |
| `--overlay TEXT` | Text overlay |
| `--cli` | CLI mode (no interactive menus) |
| `--list` | List videos in folder |
| `--devices` | List video devices |

## Hotkeys

While streaming:

| Key | Action |
|-----|--------|
| `T` | Switch video (interactive menu) |
| `N` | Next video (shuffle mode) |
| `L` | Toggle loop |
| `P` | Change folder (graphical dialog) |
| `F` | Change FPS |
| `R` | Change resolution |
| `Q` | Quit |

## ⚙ Configuration

Settings are automatically saved to `~/.virtualcam/config.json`:

```json
{
  "video_dir": "/home/user/Videos",
  "device": "/dev/video2",
  "width": 720,
  "height": 1280,
  "fps": 30,
  "loop": true,
  "shuffle": false,
  "overlay_text": ""
}
```

## Architecture

```
                   ┌──────────────────────┐
                   │     virtualcam       │
                   │   (Python script)    │
                   └──────┬───────────────┘
                          │
            ┌─────────────┴──────────────┐
            │                            │
     ┌──────▼───────┐           ┌────────▼────────┐
     │   Linux      │           │    Windows      │
     │ v4l2loopback │           │ softcam /       │
     │ + ffmpeg     │           │ Unity Capture   │
     │ (direct)     │           │ + ffmpeg (pipe) │
     └──────────────┘           └─────────────────┘
```

### Linux

ffmpeg writes directly to the v4l2loopback device.

### Windows

ffmpeg decodes the video and pipes raw frames via stdout. The backend (softcam or pyvirtualcam) reads from the pipe and sends frames one by one to the virtual camera registered on the system.

##  License

MIT — feel free to use, modify, and share.
