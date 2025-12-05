# Design Document - Revamp Hybrid Audio Visualizer

## Overview

Revamp is a browser-based audio visualizer built with vanilla HTML5, CSS3, and JavaScript. The application features a dual-engine architecture that seamlessly handles both local MP3 file playback (using Web Audio API) and YouTube video streaming (using YouTube IFrame API). The system maintains a consistent visualization interface regardless of audio source, with real frequency analysis for local files and rhythmic simulation for YouTube streams.

The application follows a retro aesthetic inspired by Winamp and classic audio equipment, presenting a vertical "rack" layout with distinct control, display, and input sections. All styling uses CSS variables to enable easy theme customization.

## Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────┐
│           User Interface Layer          │
│  (Controls, Canvas, Input Switcher)     │
└──────────────┬──────────────────────────┘
               │
┌──────────────┴──────────────────────────┐
│      Application State Manager          │
│  (currentSource, currentMode, volume)   │
└──────────────┬──────────────────────────┘
               │
       ┌───────┴────────┐
       │                │
┌──────▼──────┐  ┌──────▼──────┐
│ Real Audio  │  │  Simulated  │
│   Engine    │  │   Engine    │
│ (Web Audio) │  │  (YouTube)  │
└──────┬──────┘  └──────┬──────┘
       │                │
       └───────┬────────┘
               │
┌──────────────▼──────────────────────────┐
│       Visualizer Rendering System       │
│    (60 FPS Canvas Drawing Loop)         │
└─────────────────────────────────────────┘
```

### Component Responsibilities

**UI Layer**: Handles user interactions, displays controls, manages input switching between local and YouTube modes.

**State Manager**: Maintains global application state including current source type, active visualizer mode, playback state, and volume level.

**Real Audio Engine**: Manages local MP3 file playback using Web Audio API, extracts real frequency data via AnalyserNode.

**Simulated Engine**: Manages YouTube video playback using IFrame API, generates simulated frequency data using mathematical functions.

**Visualizer System**: Renders audio visualizations at 60 FPS, supports multiple modes (Bars, Wave, Matrix), consumes frequency data arrays.

## Components and Interfaces

### State Manager

```javascript
const AppState = {
  currentSource: 'local' | 'youtube',
  visualizerMode: 'bars' | 'wave' | 'matrix',
  isPlaying: false,
  volume: 0.5,
  audioLoaded: false
}
```

### Audio Engine Interface

Both engines implement a common interface:

```javascript
interface AudioEngine {
  load(source): Promise<void>
  play(): void
  pause(): void
  stop(): void
  setVolume(level): void
  getFrequencyData(): Uint8Array
  dispose(): void
}
```

### Real Audio Engine (Local MP3)

```javascript
class RealAudioEngine {
  constructor() {
    this.audioContext = new AudioContext()
    this.analyser = this.audioContext.createAnalyser()
    this.analyser.fftSize = 256
    this.source = null
    this.gainNode = this.audioContext.createGain()
  }
  
  // Implements AudioEngine interface
  // Uses Web Audio API for all operations
}
```

### Simulated Engine (YouTube)

```javascript
class SimulatedEngine {
  constructor() {
    this.player = null
    this.simulationTime = 0
    this.dataArray = new Uint8Array(128)
  }
  
  // Implements AudioEngine interface
  // Uses YouTube IFrame API for playback
  // Generates fake frequency data using math
}
```

### Visualizer Renderer

```javascript
class Visualizer {
  constructor(canvas) {
    this.canvas = canvas
    this.ctx = canvas.getContext('2d')
    this.mode = 'bars'
    this.animationId = null
  }
  
  start(getDataCallback) {
    // Starts 60 FPS rendering loop
    // Calls getDataCallback() each frame to get frequency data
  }
  
  drawBars(dataArray) { /* ... */ }
  drawWave(dataArray) { /* ... */ }
  drawMatrix(dataArray) { /* ... */ }
  
  stop() {
    cancelAnimationFrame(this.animationId)
  }
}
```

### Simulated Beat Generator

```javascript
function generateFakeBeats(time, dataArray) {
  const bassFreq = 0.5 // Simulates bass drum
  const midFreq = 2.0  // Simulates mid-range
  
  for (let i = 0; i < dataArray.length; i++) {
    const freq = bassFreq + (i / dataArray.length) * midFreq
    const sine = Math.sin(time * freq) * 0.5 + 0.5
    const noise = Math.random() * 0.3
    dataArray[i] = Math.floor((sine + noise) * 255)
  }
}
```

## Data Models

### Application State

- `currentSource`: String enum ('local' | 'youtube')
- `visualizerMode`: String enum ('bars' | 'wave' | 'matrix')
- `isPlaying`: Boolean
- `volume`: Number (0.0 to 1.0)
- `audioLoaded`: Boolean

### Frequency Data

- Type: `Uint8Array`
- Length: 128 values (half of FFT size 256)
- Range: 0-255 per value
- Represents frequency bins from low to high

### YouTube Video Reference

- `videoId`: String extracted from YouTube URL
- `player`: YouTube IFrame Player instance

### Local Audio Reference

- `audioBuffer`: AudioBuffer from decoded MP3
- `source`: AudioBufferSourceNode
- `analyser`: AnalyserNode

## Correctness Properties

*A property is a characteristic or behavior that should hold true across all valid executions of a system—essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.*

### Property 1: Source toggle cycles state
*For any* current source mode (local or youtube), clicking the source toggle control should change the state to the opposite mode.
**Validates: Requirements 1.1**

### Property 2: Mode change stops playback
*For any* source mode and playback state, when the source mode changes, the playback state should become stopped.
**Validates: Requirements 1.4**

### Property 3: UI reflects current mode
*For any* source mode change, the UI elements displayed should match the new mode (Drop Zone for local, text input for youtube).
**Validates: Requirements 1.5**

### Property 4: Valid MP3 files are accepted
*For any* valid MP3 file, when dropped into the Drop Zone, the file should be accepted and the audioLoaded state should become true.
**Validates: Requirements 2.1**

### Property 5: Invalid files are rejected
*For any* non-MP3 file, when dropped into the Drop Zone, the file should be rejected and an error message should be displayed.
**Validates: Requirements 2.2**

### Property 6: Local mode uses Web Audio API
*For any* MP3 file loaded in local mode, the Real Audio Engine should create an AudioContext and AnalyserNode.
**Validates: Requirements 2.3, 2.4**

### Property 7: Frequency data flows to visualizer
*For any* audio source (local or youtube), when frequency data is available, it should be passed to the visualizer rendering function.
**Validates: Requirements 2.5, 3.5**

### Property 8: Valid YouTube URLs are parsed
*For any* valid YouTube URL format, the system should correctly extract the video ID and load the video.
**Validates: Requirements 3.1**

### Property 9: Invalid YouTube URLs are rejected
*For any* invalid YouTube URL, the system should display an error message and not attempt to load a video.
**Validates: Requirements 3.2**

### Property 10: YouTube mode uses IFrame API
*For any* YouTube video loaded, the Simulated Engine should create a YouTube IFrame Player instance.
**Validates: Requirements 3.3**

### Property 11: Simulated engine generates data
*For any* time value during YouTube playback, the Simulated Engine should generate a frequency data array with values in the range 0-255.
**Validates: Requirements 3.4**

### Property 12: Play button starts playback
*For any* loaded audio source, clicking the play button should change the playback state to playing.
**Validates: Requirements 4.1**

### Property 13: Pause button pauses playback
*For any* playing audio source, clicking the pause button should change the playback state to paused.
**Validates: Requirements 4.2**

### Property 14: Stop button resets playback
*For any* playing audio source, clicking the stop button should change the playback state to stopped and reset the position to the beginning.
**Validates: Requirements 4.3**

### Property 15: Volume changes are applied
*For any* volume level between 0.0 and 1.0, adjusting the volume slider should update the audio engine's volume to that level.
**Validates: Requirements 4.4**

### Property 16: Visualizer mode cycles correctly
*For any* current visualizer mode, clicking the canvas should change the mode to the next mode in the sequence (bars → wave → matrix → bars).
**Validates: Requirements 5.1**

### Property 17: Mode change preserves playback
*For any* playback state, changing the visualizer mode should not change whether audio is playing or stopped.
**Validates: Requirements 5.5**

### Property 18: Visualization stops with playback
*For any* visualizer state, when audio is paused or stopped, the animation frame requests should cease.
**Validates: Requirements 6.3**

### Property 19: Canvas is cleared between frames
*For any* visualization frame, the canvas should be cleared before rendering the new frame.
**Validates: Requirements 6.4**

### Property 20: Visualization adapts to canvas size
*For any* canvas dimensions, the visualization should scale to fit the available space.
**Validates: Requirements 6.5**

### Property 21: CSS variables are used consistently
*For any* color property in the stylesheet, it should reference a CSS variable rather than a hardcoded color value.
**Validates: Requirements 7.4**

### Property 22: Real Audio Engine isolation
*For any* operation in the Real Audio Engine, only Web Audio API methods should be used (no YouTube API calls).
**Validates: Requirements 8.1**

### Property 23: Simulated Engine isolation
*For any* operation in the Simulated Engine, only YouTube IFrame API methods should be used (no Web Audio API calls).
**Validates: Requirements 8.2**

### Property 24: Engine cleanup on switch
*For any* engine switch, the dispose method should be called on the previous engine before activating the new engine.
**Validates: Requirements 8.3**

### Property 25: Consistent data format
*For any* frequency data from either engine, the data should be a Uint8Array with values in the range 0-255.
**Validates: Requirements 8.4, 8.5**

### Property 26: Simulation uses mathematical functions
*For any* simulated frequency data generation, the algorithm should use mathematical functions (Math.sin, Math.random) to create rhythmic patterns.
**Validates: Requirements 9.1, 9.2, 9.3**

### Property 27: Visualizer treats all data equally
*For any* frequency data array (real or simulated), the visualizer should render it using the same code path without checking the data source.
**Validates: Requirements 9.5**

## Error Handling

### File Upload Errors

- **Invalid File Type**: When a non-MP3 file is dropped, display error message "Please upload an MP3 file" and reject the file
- **File Read Error**: If FileReader fails, display error message "Failed to read file" and reset the drop zone
- **Decode Error**: If Web Audio API cannot decode the file, display error message "Invalid or corrupted MP3 file"

### YouTube Errors

- **Invalid URL**: When URL parsing fails, display error message "Invalid YouTube URL"
- **Video Not Found**: When YouTube API returns error, display error message "Video not found or unavailable"
- **API Load Failure**: If YouTube IFrame API fails to load, display error message "Failed to load YouTube player"

### Playback Errors

- **No Audio Loaded**: Disable play/pause/stop buttons and show message "No audio loaded"
- **Audio Context Error**: If AudioContext creation fails, display error message "Audio system unavailable"
- **Playback Interruption**: If playback fails during operation, stop playback and display error message

### Canvas Errors

- **Canvas Not Supported**: If canvas is not available, display error message "Canvas not supported in this browser"
- **WebGL Context Loss**: If canvas context is lost, attempt to restore and continue rendering

## Testing Strategy

### Unit Testing

We will use vanilla JavaScript with a simple test runner (or browser-based testing) to verify specific functionality:

**File Upload Tests**:
- Test that valid MP3 files trigger the correct load sequence
- Test that invalid file types are rejected
- Test that the Drop Zone UI updates correctly

**State Management Tests**:
- Test that source toggle updates state correctly
- Test that mode cycling works in the correct sequence
- Test that playback controls update state appropriately

**URL Parsing Tests**:
- Test YouTube URL extraction with various URL formats (watch?v=, youtu.be/, embed/)
- Test that invalid URLs are rejected

**Simulation Tests**:
- Test that simulated data generation produces arrays of correct length
- Test that simulated values are in the 0-255 range
- Test that simulation uses Math.sin and Math.random

### Property-Based Testing

We will use **fast-check** (a JavaScript property-based testing library) to verify universal properties. Each property-based test will run a minimum of 100 iterations.

**Testing Approach**:
- Each correctness property listed above will be implemented as a property-based test
- Each test will be tagged with a comment: `// Feature: hybrid-audio-visualizer, Property X: [property text]`
- Tests will generate random inputs (file types, volume levels, mode states) to verify properties hold universally

**Key Property Tests**:
- **State Transitions**: Generate random sequences of user actions and verify state consistency
- **Data Format**: Generate random audio data and verify both engines produce consistent formats
- **UI Consistency**: Generate random state changes and verify UI always reflects current state
- **Engine Isolation**: Verify that Real Audio Engine never calls YouTube API and vice versa
- **Volume Control**: Generate random volume values and verify they're applied correctly
- **Mode Cycling**: Generate random starting modes and verify cycling sequence is correct

**Test Configuration**:
- Minimum 100 iterations per property test
- Use shrinking to find minimal failing cases
- Seed tests for reproducibility during debugging

### Integration Testing

- Test complete user flows: load file → play → change visualizer → stop
- Test source switching: local playback → switch to youtube → youtube playback
- Test error recovery: trigger error → verify UI state → recover gracefully

### Manual Testing

- Visual verification of visualizer modes (bars, wave, matrix)
- Audio quality verification for local playback
- YouTube video synchronization verification
- Theme appearance verification
- Responsive layout verification

## Implementation Notes

### File Structure

```
/
├── index.html          # Main HTML structure
├── style.css           # All styling with CSS variables
└── app.js              # All JavaScript logic
```

### CSS Variables for Theming

```css
:root {
  --main-bg: #0a0a0a;
  --main-text: #00ff41;
  --highlight: #00cc33;
  --chassis-dark: #1a1a1a;
  --chassis-light: #2a2a2a;
  --bevel-light: #3a3a3a;
  --bevel-dark: #0a0a0a;
}
```

### Performance Considerations

- Use `requestAnimationFrame` for 60 FPS rendering
- Reuse Uint8Array buffers instead of creating new ones each frame
- Use canvas double buffering if needed for complex visualizations
- Debounce canvas resize events to avoid excessive redraws

### Browser Compatibility

- Target modern browsers with Web Audio API support (Chrome, Firefox, Safari, Edge)
- Require YouTube IFrame API support
- Gracefully degrade if features are unavailable

### Security Considerations

- Validate all file inputs before processing
- Sanitize YouTube URLs before parsing
- Use Content Security Policy to restrict external resources
- Handle CORS appropriately for local file access
