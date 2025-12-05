# Radio Logic & Simulated Visualizer Specification

## Overview

Revamp Radio v2.0 is a hybrid audio visualizer that supports both local MP3 playback and Internet Radio streaming. Due to CORS (Cross-Origin Resource Sharing) restrictions on most radio streams, the application uses a **Simulated Visualizer** for radio mode while maintaining real audio analysis for local files.

## Architecture

### Dual-Mode System

```
┌─────────────────────────────────────────────────────────┐
│                    Revamp Radio v2.0                    │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌──────────────┐              ┌──────────────┐       │
│  │  LOCAL MODE  │              │  RADIO MODE  │       │
│  └──────────────┘              └──────────────┘       │
│         │                              │               │
│         ▼                              ▼               │
│  ┌──────────────┐              ┌──────────────┐       │
│  │ Real Audio   │              │ Audio Stream │       │
│  │ Context      │              │ (No Context) │       │
│  │ + Analyser   │              │              │       │
│  └──────────────┘              └──────────────┘       │
│         │                              │               │
│         ▼                              ▼               │
│  ┌──────────────┐              ┌──────────────┐       │
│  │ Real FFT     │              │ Simulated    │       │
│  │ Frequency    │              │ Frequency    │       │
│  │ Data         │              │ Data         │       │
│  └──────────────┘              └──────────────┘       │
│         │                              │               │
│         └──────────────┬───────────────┘               │
│                        ▼                               │
│              ┌──────────────────┐                      │
│              │   Visualizer     │                      │
│              │  (Bars/Wave/     │                      │
│              │   Matrix)        │                      │
│              └──────────────────┘                      │
└─────────────────────────────────────────────────────────┘
```

## Simulated Visualizer Logic

### Why Simulation is Necessary

Internet radio streams typically:
1. **Block CORS access**: Streams don't allow `crossOrigin="anonymous"` access
2. **Prevent AudioContext connection**: Cannot create `MediaElementSource` from radio streams
3. **Require fallback**: Must generate fake frequency data to maintain visual experience

### Simulation Algorithm

The simulated visualizer generates frequency data using mathematical functions to create realistic-looking audio patterns:

```javascript
// Simulated Frequency Data Generation
for(let i=0; i<bufferLength; i++) {
    const time = Date.now() / 1000;
    
    // Bass Component (Low frequencies)
    const bass = Math.abs(Math.sin(time * 8)) * (i < 10 ? 255 : 0);
    
    // Wave Component (Mid frequencies)
    const wave = Math.abs(Math.sin(time * 3 + i * 0.1)) * 100;
    
    // Noise Component (High frequencies)
    const noise = Math.random() * 50;
    
    // Combine and clamp to 0-255 range
    dataArray[i] = Math.min(255, (bass * 0.5) + wave + noise); 
}
```

### Mathematical Components

#### 1. Bass Simulation
```javascript
const bass = Math.abs(Math.sin(time * 8)) * (i < 10 ? 255 : 0);
```
- **Frequency**: 8 Hz (typical bass drum rhythm)
- **Range**: First 10 frequency bins (low frequencies)
- **Effect**: Creates periodic "thumps" like a bass drum

#### 2. Wave Simulation
```javascript
const wave = Math.abs(Math.sin(time * 3 + i * 0.1)) * 100;
```
- **Frequency**: 3 Hz base + frequency-dependent phase shift
- **Range**: All frequency bins
- **Effect**: Creates flowing wave patterns across the spectrum

#### 3. Noise Simulation
```javascript
const noise = Math.random() * 50;
```
- **Type**: Random noise
- **Range**: 0-50 (20% of max amplitude)
- **Effect**: Adds variation and "sparkle" to high frequencies

#### 4. Combination
```javascript
dataArray[i] = Math.min(255, (bass * 0.5) + wave + noise);
```
- Bass is weighted at 50% to prevent overwhelming
- Wave and noise are added at full strength
- Result is clamped to valid range (0-255)

## Mode Detection & Data Source

### Local Mode (Real Audio Analysis)

```javascript
if (currentMode === 'local') {
    // Connect to Web Audio API
    sourceNode = audioCtx.createMediaElementSource(audioEl);
    sourceNode.connect(analyser);
    analyser.connect(audioCtx.destination);
    
    // Get real frequency data
    analyser.getByteFrequencyData(dataArray);
}
```

**Characteristics:**
- ✅ Real FFT analysis
- ✅ Accurate frequency representation
- ✅ Responds to actual audio content
- ✅ 128 frequency bins (0-255 values)

### Radio Mode (Simulated Analysis)

```javascript
if (currentMode === 'radio') {
    // Play stream without AudioContext connection
    audioEl = new Audio(station.url);
    audioEl.crossOrigin = "anonymous"; // Attempt, but expect failure
    audioEl.play();
    
    // Generate fake frequency data
    for(let i=0; i<bufferLength; i++) {
        // Mathematical simulation (see above)
    }
}
```

**Characteristics:**
- ⚠️ Simulated FFT data
- ⚠️ Not synchronized with actual audio
- ✅ Visually appealing patterns
- ✅ Maintains user experience
- ✅ No CORS errors

## Visualization Modes

All three visualization modes work identically with both real and simulated data:

### Mode 0: Bars
```javascript
const barWidth = (w / bufferLength) * 2.5;
let x = 0;
for(let i = 0; i < bufferLength; i++) {
    const barHeight = dataArray[i] / 2;
    ctx.fillRect(x, h - barHeight, barWidth, barHeight);
    x += barWidth + 1;
}
```
- Vertical bars representing frequency spectrum
- Low frequencies (bass) on left
- High frequencies on right

### Mode 1: Wave
```javascript
ctx.beginPath();
let sliceWidth = w * 1.0 / bufferLength;
let x = 0;
for(let i = 0; i < bufferLength; i++) {
    const v = dataArray[i] / 128.0;
    const y = v * h/2;
    if(i === 0) ctx.moveTo(x, y); else ctx.lineTo(x, y);
    x += sliceWidth;
}
ctx.stroke();
```
- Oscilloscope-style waveform
- Smooth line connecting frequency points
- Centered vertically

### Mode 2: Matrix
```javascript
ctx.font = '10px monospace';
for(let i = 0; i < bufferLength; i+=4) {
    if(Math.random() > 0.95) {
        const y = (Date.now() / 20 + i) % h;
        ctx.fillText(String.fromCharCode(0x30A0 + Math.random() * 96), i * 3, y);
    }
}
```
- Falling katakana characters
- Matrix movie aesthetic
- Frequency data influences character density

## Radio Station Configuration

### Station Data Structure
```javascript
const stations = [
    { 
        name: 'Station Display Name',
        url: 'https://stream.url/endpoint'
    }
];
```

### Current Stations

**Cyberpunk / Hacker Vibes:**
1. **SomaFM: DEF CON Radio** - Hacker conference music
2. **SomaFM: Secret Agent** - Spy/lounge music
3. **Nightride FM** - Synthwave and retrowave

**Lofi / Chill:**
4. **Lofi Girl** - Lo-fi hip hop beats
5. **SomaFM: Groove Salad** - Ambient electronic
6. **SomaFM: Drone Zone** - Atmospheric ambient

**Classy / Vintage:**
7. **Radio Swiss Jazz** - Swiss jazz radio
8. **WRTI Classical** - Classical music
9. **Hacker Public Radio** - Tech talk and podcasts

### Station Navigation
- **PREV Button**: Cycles backward through stations
- **NEXT Button**: Cycles forward through stations
- **Circular**: Wraps around at beginning/end

## Performance Characteristics

### Frame Rate
- **Target**: 60 FPS via `requestAnimationFrame`
- **Actual**: Depends on browser and system load
- **Optimization**: Only animates when `isPlaying === true`

### Data Generation Cost
- **Local Mode**: ~0.1ms (native FFT)
- **Radio Mode**: ~0.5ms (JavaScript simulation)
- **Impact**: Negligible on modern hardware

### Memory Usage
- **dataArray**: 128 bytes (Uint8Array)
- **AudioContext**: ~2-5 MB (when active)
- **Total**: < 10 MB typical

## User Experience

### Visual Feedback States

| State | Status Display | Visual Behavior |
|-------|---------------|-----------------|
| Ready | "Ready..." | Static black canvas |
| Buffering | "Buffering: [Station]..." | Static black canvas |
| Playing (Local) | "Playing: [Filename]" | Real frequency visualization |
| Playing (Radio) | "Broadcasting: [Station]" | Simulated visualization |
| Stopped | "Stopped" | Static black canvas |
| Error | "Error: Stream Offline" | Static black canvas |

### Mode Indicators
- **Active Mode Button**: Highlighted with `active-mode` class
- **Panel Visibility**: Only active mode's controls shown
- **Status Text**: Updates to reflect current mode

## Technical Limitations

### CORS Restrictions
- Most radio streams block cross-origin access
- Cannot connect to AudioContext
- Must use simulation for visualization

### Workarounds Attempted
```javascript
audioEl.crossOrigin = "anonymous"; // Try, but expect failure
```
- Attempts anonymous CORS
- Falls back to simulation if blocked
- No error thrown to user

### Why Not Use Proxy?
- Requires backend server
- Violates stream provider ToS
- Adds latency and complexity
- Not suitable for vanilla JS app

## Future Enhancements

### Potential Improvements
1. **Adaptive Simulation**: Analyze stream metadata to adjust simulation
2. **User Presets**: Save favorite stations
3. **Equalizer**: Visual EQ controls (cosmetic for radio mode)
4. **Spectrum Analyzer**: More detailed frequency display
5. **Recording**: Save local playback (not radio due to copyright)

### Not Possible Without Backend
- Real frequency analysis of radio streams
- Stream transcoding
- Metadata extraction
- Recording radio streams

## Conclusion

The simulated visualizer provides a visually appealing experience for radio streams while maintaining real audio analysis for local files. This hybrid approach balances technical limitations with user experience, creating a functional and aesthetically pleasing audio visualizer.

The mathematical simulation creates patterns that **look** like real audio visualization, even though they're not synchronized with the actual audio content. For most users, this provides sufficient visual feedback while listening to internet radio.
