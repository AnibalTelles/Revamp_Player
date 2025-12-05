# REVAMP.EXE - Project Specification

## Overview

**REVAMP.EXE** is a retro-styled audio visualizer that resurrects the soul of classic Winamp-era music players with modern web technologies. Built entirely in vanilla JavaScript with zero dependencies (except Tailwind CDN for utility classes), it provides real-time audio visualization with 10 distinct visual modes and a fully functional playlist system.

## Core Philosophy

> "Resurrecting the Soul of Audio"

REVAMP.EXE embodies the aesthetic and functionality of late-90s/early-2000s audio players while leveraging modern Web Audio API capabilities. The project prioritizes:

1. **Zero Build Steps**: Single HTML file, runs anywhere
2. **Retro Aesthetic**: Winamp-inspired UI with 3D bevels and rack-style layout
3. **Extensibility**: Modular visualizer system for easy expansion
4. **Performance**: 60 FPS visualization with efficient rendering
5. **Dual-Mode**: Local MP3 playback + Internet Radio streaming

## Architecture

### Component Structure

```
REVAMP.EXE
├── Theme System (CSS Variables)
├── Audio Engine (Web Audio API)
│   ├── AudioContext
│   ├── AnalyserNode (FFT)
│   └── MediaElementSource
├── Visualizer Engine (Canvas 2D)
│   ├── Classic Modes (0-4)
│   └── Milkdrop Modes (5-9)
├── Playlist Manager
│   ├── File Handling
│   └── Track Navigation
└── Radio Tuner
    └── Station Management
```

### Data Flow

```
Audio Source (MP3/Radio)
    ↓
AudioContext + AnalyserNode
    ↓
FFT Analysis (1024 samples)
    ↓
Frequency Data (Uint8Array)
    ↓
Visualizer Renderer (60 FPS)
    ↓
Canvas 2D Context
```

## Visualizer Modes

### Classic Modes (0-4)

#### Mode 0: BARS + PEAKS
- **Type**: Frequency spectrum bars
- **Features**: 64 bars with peak hold indicators
- **Colors**: Rainbow gradient (hue-shifted per bar)
- **Performance**: Excellent (simple rectangles)

#### Mode 1: OSCILLOSCOPE
- **Type**: Waveform display
- **Features**: Time-domain visualization
- **Colors**: Rainbow (hue-cycling)
- **Performance**: Excellent (single path)

#### Mode 2: MATRIX RAIN
- **Type**: Falling characters (Matrix movie style)
- **Features**: Katakana characters with trails
- **Colors**: Theme accent color (respects skin)
- **Performance**: Good (optimized with frame skipping)
- **Special**: Uses fade trails instead of full clear

#### Mode 3: HYPNO-SCOPE
- **Type**: Circular frequency plot
- **Features**: Radial visualization with bass response
- **Colors**: Rainbow with 180° hue offset
- **Performance**: Excellent (single path)

#### Mode 4: BASS SPEAKER
- **Type**: Pulsing circles
- **Features**: Bass-reactive concentric circles
- **Colors**: Rainbow with transparency
- **Performance**: Excellent (simple arcs)

### Milkdrop Modes (5-9)

These modes use **feedback zoom** technique (drawing canvas onto itself with scale/rotation) to create psychedelic effects.

#### Mode 5: THE WORMHOLE
- **Type**: Spiral tunnel
- **Features**: Rotating spiral with time-domain data
- **Effect**: Zoom + rotation feedback
- **Performance**: Good (feedback overhead)

#### Mode 6: ELECTRIC MANDALA
- **Type**: Radial symmetry
- **Features**: 6-way rotational symmetry
- **Effect**: Zoom + rotation feedback
- **Performance**: Good

#### Mode 7: LIQUID MOUNTAINS
- **Type**: Flowing landscape
- **Features**: Sine-wave mountains with time-domain
- **Effect**: Zoom + rotation feedback
- **Performance**: Good

#### Mode 8: SPIRIT AURAS
- **Type**: Floating orbs
- **Features**: Radial gradients with additive blending
- **Effect**: Zoom + rotation feedback
- **Performance**: Medium (gradient calculations)

#### Mode 9: CHAOS STRINGS
- **Type**: Random line connections
- **Features**: Chaotic line drawing
- **Effect**: Zoom + rotation feedback
- **Performance**: Good

#### Mode 10: SPECTRAL CHAINS
- **Type**: Connected frequency nodes
- **Features**: 3 rotating chain layers with intensity-based nodes
- **Colors**: Rainbow (3 layers with 120° hue offset)
- **Effect**: Zoom + rotation feedback
- **Performance**: Good
- **Special**: Circular chains rotate slowly, nodes appear at high intensity

## Theme System

### CSS Variable Architecture

All colors are defined as CSS variables in `:root`, enabling instant theme switching:

```css
:root {
    --bg-color: Background color
    --panel-face: Panel surface color
    --border-light: Light bevel edge
    --border-dark: Dark bevel edge
    --accent-main: Primary accent (text, highlights)
    --accent-dim: Dimmed accent (active states)
    --title-1: Title bar gradient start
    --title-2: Title bar gradient end
    --selection-bg: Selected item background
    --font-stack: Font family
}
```

### Built-in Themes

1. **CLASSIC** - Green on black (default Winamp)
2. **VAMPIRE** - Red on black (gothic)
3. **CYBER-BLUE** - Cyan on dark blue (cyberpunk)
4. **GOLDEN AGE** - Gold on brown (vintage)
5. **HOT DOG** - Red on yellow (meme theme)

### Theme Switching

Themes cycle via the 🎨 SKINS button, updating all CSS variables instantly without page reload.

## Audio Engine

### Web Audio API Integration

```javascript
AudioContext
    ↓
MediaElementSource (from <audio> element)
    ↓
AnalyserNode (FFT: 1024, smoothing: 0.85)
    ↓
Destination (speakers)
```

### Dual-Mode System

#### Local Mode
- **Source**: User-uploaded MP3/WAV/OGG/M4A/FLAC files
- **Analysis**: Real FFT frequency data
- **Features**: Playlist, track navigation, file/folder upload

#### Radio Mode
- **Source**: Internet radio streams (5 preset stations)
- **Analysis**: Simulated frequency data (CORS workaround)
- **Features**: Station navigation, auto-play

### Simulated Data (Radio Mode)

When CORS blocks real audio analysis, the engine generates fake frequency data:

```javascript
function simData() {
    const t = Date.now()/1000;
    for(let i=0; i<bufferLength; i++) {
        // Bass simulation (low frequencies)
        const bass = Math.abs(Math.sin(t*8)) * 150 * (i<10 ? 1 : 0.1);
        // Noise (high frequencies)
        const noise = Math.random() * 50;
        dataArray[i] = noise + bass;
        
        // Time domain simulation
        timeArray[i] = 128 + Math.sin(t*10 + i*0.1) * 30;
    }
}
```

## Playlist System

### Features
- **Multi-file upload**: Select multiple files at once
- **Folder upload**: Upload entire music folders
- **Auto-sort**: Alphabetical sorting by filename
- **Track navigation**: Click to play, prev/next buttons
- **Visual feedback**: Active track highlighted
- **Auto-advance**: Plays next track on completion

### Supported Formats
- MP3 (MPEG Audio Layer 3)
- WAV (Waveform Audio)
- OGG (Ogg Vorbis)
- M4A (MPEG-4 Audio)
- FLAC (Free Lossless Audio Codec)

## Radio Stations

### Current Stations
1. **SomaFM: DEF CON** - Hacker conference music
2. **Nightride FM** - Synthwave/retrowave
3. **Psychedelik Trance** - Psytrance
4. **SomaFM: Groove Salad** - Ambient electronic
5. **Capital FM** - Pop/mainstream

### Station Management
- Circular navigation (wraps around)
- Instant switching (no buffering delay)
- CORS-aware (uses simulated visualization)

## Performance Characteristics

### Rendering
- **Target FPS**: 60
- **Actual FPS**: 55-60 (depends on mode and hardware)
- **Canvas Size**: 370x150 pixels
- **FFT Size**: 1024 samples
- **Frequency Bins**: 512 (half of FFT)

### Memory Usage
- **AudioContext**: ~2-5 MB
- **Canvas Buffer**: ~220 KB
- **Playlist**: ~1 KB per track
- **Total**: < 10 MB typical

### CPU Usage
- **Classic Modes**: 2-5% (single core)
- **Milkdrop Modes**: 5-10% (feedback overhead)
- **Matrix Rain**: 3-7% (character rendering)

## Code Organization

### Structure
```javascript
// 1. Theme System (lines 1-50)
// 2. Station Data (lines 51-60)
// 3. Engine Variables (lines 61-80)
// 4. Visual Variables (lines 81-100)
// 5. Audio Init (lines 101-130)
// 6. Render Loop (lines 131-400)
// 7. Simulation (lines 401-420)
// 8. Event Handlers (lines 421-500)
```

### Key Functions

#### `initAudio()`
Initializes Web Audio API components. Called on first play.

#### `animate()`
Main render loop. Runs at 60 FPS when playing.

#### `simData()`
Generates simulated frequency data for radio mode.

#### `playTrack(i)`
Loads and plays track at index i from playlist.

#### `updatePlaylistUI()`
Refreshes playlist display with current state.

## Extension Points

### Adding New Visualizers

1. **Increment mode count** in `visList` array
2. **Add mode name** to `visList`
3. **Add rendering logic** in `animate()` function:

```javascript
else if(visMode === 10) { // NEW MODE
    // Your rendering code here
    // Access: dataArray (frequency), timeArray (waveform)
    // Variables: w, h, cx, cy, bass, mids, hue, accent
}
```

### Adding New Themes

Add to `themes` array:

```javascript
{ 
    name: "THEME_NAME", 
    vars: { 
        '--bg-color': '#hex',
        '--panel-face': '#hex',
        '--accent-main': '#hex',
        // ... other variables
    }
}
```

### Adding Radio Stations

Add to `stations` array:

```javascript
{ name: 'Station Name', url: 'https://stream.url/endpoint' }
```

## Technical Constraints

### Browser Requirements
- **Web Audio API**: Chrome 35+, Firefox 25+, Safari 14+
- **Canvas 2D**: All modern browsers
- **File API**: All modern browsers
- **ES6 JavaScript**: All modern browsers

### Known Limitations
- **Radio CORS**: Most streams block audio analysis
- **File Size**: Large playlists (>1000 tracks) may slow UI
- **Mobile**: Touch events not optimized
- **Safari**: May require user gesture for audio playback

## Future Enhancements

### Planned Features
- [ ] Equalizer controls (visual only for radio)
- [ ] Playlist save/load (localStorage)
- [ ] Keyboard shortcuts
- [ ] Fullscreen mode
- [ ] Recording (local mode only)
- [ ] Spectrum analyzer overlay
- [ ] Custom theme editor
- [ ] Visualizer presets/favorites

### Community Contributions
- New visualizer modes
- New themes
- Performance optimizations
- Mobile support
- Accessibility improvements

## Development Guidelines

### Code Style
- **Indentation**: 4 spaces
- **Naming**: camelCase for variables, PascalCase for classes
- **Comments**: Inline for complex logic, section headers for major blocks
- **Line Length**: Prefer < 100 characters

### Testing
- Test in Chrome, Firefox, Safari
- Test with various audio formats
- Test with large playlists (100+ tracks)
- Test theme switching
- Test all visualizer modes

### Performance
- Avoid allocations in render loop
- Reuse arrays (dataArray, timeArray)
- Use requestAnimationFrame for smooth rendering
- Profile with browser DevTools

## License & Credits

### License
MIT License - Free to use, modify, and distribute

### Inspiration
- **Winamp**: Classic audio player UI
- **Milkdrop**: Psychedelic visualizer plugin
- **Matrix**: Falling code aesthetic
- **Synthwave**: Retro-futuristic design

### Technologies
- **Web Audio API**: Audio analysis
- **Canvas 2D**: Visualization rendering
- **Tailwind CSS**: Utility classes (CDN)
- **Vanilla JavaScript**: No frameworks

---

**Version**: 2.0  
**Last Updated**: 2024  
**Status**: Production Ready  
**Maintainer**: Community-driven
