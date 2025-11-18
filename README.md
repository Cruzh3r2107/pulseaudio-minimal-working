# PulseAudio Minimal Snap for Ubuntu Core 24

A minimal PulseAudio sound server snap for Ubuntu Core 24, built using Ubuntu deb packages.

## Purpose

Provides audio-playback and audio-record interfaces for applications like Firefox on Ubuntu Core 24.

## Architecture

- **Base**: core24
- **Packages**: Uses official Ubuntu 24.04 (Noble) PulseAudio packages
- **Target**: ARM64 (Raspberry Pi 4/5)
- **Build**: Cross-compiled from AMD64 or native ARM64

## Building

### Option 1: Remote Build (from any machine)

```bash
# Install snapcraft
sudo snap install snapcraft --classic

# Build for ARM64 using Launchpad
cd pulseaudio-minimal
snapcraft remote-build

# Output: pulseaudio-minimal_17.0_arm64.snap
```

### Option 2: Local Build on ARM64 (Raspberry Pi 5 with Ubuntu Server)

```bash
# Install snapcraft and lxd
sudo snap install snapcraft --classic
sudo snap install lxd
sudo lxd init --auto

# Build using LXD container
cd pulseaudio-minimal
snapcraft

# Or build in destructive mode (faster, but modifies system)
snapcraft --destructive-mode
```

## Installation on Ubuntu Core 24

```bash
# Install the snap
sudo snap install --dangerous pulseaudio-minimal_*.snap

# Connect ALSA interface
sudo snap connect pulseaudio-minimal:alsa

# Start service
sudo snap start pulseaudio-minimal

# Verify
snap services pulseaudio-minimal
```

## Usage with Firefox

```bash
# Connect Firefox to PulseAudio
sudo snap connect firefox:audio-playback pulseaudio-minimal:audio-playback

# List audio sinks
sudo pulseaudio-minimal.pactl list sinks short

# Set default sink (e.g., sink 1 for 3.5mm jack)
sudo pulseaudio-minimal.pactl set-default-sink 1

# Launch Firefox
sudo WAYLAND_DISPLAY=wayland-0 snap run firefox --kiosk "https://www.youtube.com"
```

## Available Commands

- `pulseaudio-minimal.pulseaudio` - PulseAudio daemon (runs as service)
- `pulseaudio-minimal.pactl` - PulseAudio control utility

## Interfaces

**Plugs:**
- `alsa` - Access to ALSA sound devices
- `network` - Network access for PulseAudio protocol
- `network-bind` - Bind to network sockets
- `hardware-observe` - Observe hardware information

**Slots:**
- `audio-playback` - Provides audio playback to other snaps
- `audio-record` - Provides audio recording to other snaps

## Troubleshooting

### Check PulseAudio Status

```bash
# Check if service is running
snap services pulseaudio-minimal

# List available sinks
sudo pulseaudio-minimal.pactl list sinks short

# Check connections
snap connections pulseaudio-minimal
```

### Enable Debug Mode

```bash
# Create debug flag
sudo mkdir -p /var/snap/pulseaudio-minimal/common/config
sudo touch /var/snap/pulseaudio-minimal/common/config/debug

# Restart service
sudo snap restart pulseaudio-minimal

# View logs
sudo journalctl -u snap.pulseaudio-minimal.pulseaudio -f
```

## Project Structure

```
pulseaudio-minimal/
├── snap/
│   └── snapcraft.yaml          # Snap build configuration
├── bin/
│   ├── pulseaudio              # PulseAudio daemon wrapper
│   └── client-wrapper          # Client utility wrapper
├── config/
│   ├── default.pa              # PulseAudio server configuration
│   └── client.conf             # PulseAudio client configuration
├── README.md
└── .gitignore
```

## License

GPL-2.0+
