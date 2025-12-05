---
inclusion: always
---

# REVAMP.EXE Development Guidelines

## Project Philosophy

REVAMP.EXE is a **single-file, zero-dependency** audio visualizer that embodies retro aesthetics with modern capabilities. All development must maintain these core principles.

## Critical Rules

### 1. Single File Architecture
- **NEVER** split into multiple files
- **NEVER** add build steps or bundlers
- **NEVER** require npm/node/package managers
- Keep everything in `index.html`

### 2. Zero Dependencies
- **ONLY** external dependency: Tailwind CDN (for utility classes)
- **NO** frameworks (React, Vue, Angular, etc.)
- **NO** libraries (jQuery, Lodash, etc.)
- **NO** polyfills or shims
- Pure vanilla JavaScript only

### 3. Retro Aesthetic
- Maintain Winamp-inspired UI
- 3D bevels on all panels
- Monospace fonts (Courier New)
- Dark backgrounds with bright accents
- Rack-style component layout

### 4. Performance First
- Target 60 FPS for all visualizers
- No allocations in render loop
- Reuse arrays and objects
- Profile before optimizing

### 5. Theme System
- **ALL** colors must use CSS variables
- **NO** hardcoded colors (except #000 for screens)
- Themes must be instantly switchable
- Maintain contrast ratios

## Code Style

### JavaScript
```javascript
// Indentation: 4 spaces
// Naming: camelCase for variables/functions
// Comments: Inline for complex logic, section headers for blocks
// Line length: Prefer < 100 characters

// GOOD
function playTrack(index) {
    if(index < 0 || index >= playlist.length) return;
    currentTrack = index;
    // ... implementation
}

// BAD
function play_track(idx) {
  if (idx<0||idx>=playlist.length) return
  currentTrack=idx
  //implementation
}
```

### CSS
```css
/* Use CSS variables for all colors */
/* GOOD */
color: var(--accent-main);
background: var(--bg-color);

/* BAD */
color: #00ff00;
background: #191919;

/* Maintain 3D bevel pattern */
border: 2px solid var(--border-light);
border-right-color: var(--border-dark);
border-bottom-color: var(--border-dark);
```

### HTML
```html
<!-- Minimal, semantic structure -->
<!-- Use classes for styling, IDs for JavaScript -->
<!-- Keep inline styles to minimum -->
```

## Adding Features

### New Visualizer Mode

**Location**: Inside `animate()` function

**Pattern**:
```javascript
else if(visMode === N) { // MODE NAME
    // Access available variables:
    // - dataArray: Frequency data (Uint8Array, 0-255)
    // - timeArray: Waveform data (Uint8Array, 0-255)
    // - w, h: Canvas dimensions
    // - cx, cy: Canvas center
    // - bass: Bass intensity (0-1)
    // - mids: Mid-range intensity (0-1)
    // - hue: Rainbow hue (0-360)
    // - accent: Theme accent color
    // - ctx: Canvas 2D context
    
    // Your rendering code here
}
```

**Steps**:
1. Add mode name to `visList` array
2. Add rendering logic in `animate()`
3. Test performance (should maintain 60 FPS)
4. Test with all themes
5. Document in specs

### New Theme

**Location**: `themes` array (near line 200)

**Pattern**:
```javascript
{ 
    name: "THEME_NAME", 
    vars: { 
        '--bg-color': '#hex',
        '--panel-face': '#hex',
        '--border-light': '#hex',
        '--border-dark': '#hex',
        '--accent-main': '#hex',
        '--accent-dim': '#hex',
        '--title-1': '#hex',
        '--title-2': '#hex',
        '--selection-bg': '#hex'
    }
}
```

**Requirements**:
- High contrast (accent on background)
- Visible bevels (border colors distinct)
- Smooth title gradient
- Readable text

### New Radio Station

**Location**: `stations` array (near line 210)

**Pattern**:
```javascript
{ name: 'Station Name', url: 'https://stream.url/endpoint' }
```

**Requirements**:
- Test stream URL in browser first
- Verify CORS allows playback
- Add descriptive name

## Modification Guidelines

### DO:
- ✅ Add new visualizer modes
- ✅ Add new themes
- ✅ Add new radio stations
- ✅ Optimize performance
- ✅ Fix bugs
- ✅ Improve UI consistency
- ✅ Add keyboard shortcuts
- ✅ Enhance existing features

### DON'T:
- ❌ Split into multiple files
- ❌ Add dependencies
- ❌ Add build steps
- ❌ Change core architecture
- ❌ Remove existing features
- ❌ Break theme system
- ❌ Hardcode colors
- ❌ Allocate in render loop

## Testing Checklist

Before committing changes:

**Functionality**:
- [ ] Local mode works (file upload, playlist)
- [ ] Radio mode works (station switching)
- [ ] All visualizer modes render correctly
- [ ] Theme switching works
- [ ] Playback controls work (play, pause, stop, next, prev)

**Performance**:
- [ ] Maintains 60 FPS (or close) in all modes
- [ ] No memory leaks
- [ ] No console errors
- [ ] Smooth animations

**UI**:
- [ ] All themes look good
- [ ] Bevels are visible
- [ ] Text is readable
- [ ] Active states are clear
- [ ] No layout breaks

**Browsers**:
- [ ] Chrome/Edge
- [ ] Firefox
- [ ] Safari

## Common Patterns

### Accessing Frequency Data
```javascript
// Get frequency data (0-255, 512 bins)
analyser.getByteFrequencyData(dataArray);

// Get waveform data (0-255, 512 samples)
analyser.getByteTimeDomainData(timeArray);

// Calculate bass intensity
const bass = dataArray.slice(0, 10).reduce((a,b)=>a+b,0) / 2550;

// Calculate mid-range intensity
const mids = dataArray.slice(100, 200).reduce((a,b)=>a+b,0) / 25500;
```

### Drawing Patterns
```javascript
// Rainbow colors (hue-cycling)
ctx.fillStyle = `hsl(${hue}, 100%, 50%)`;

// Theme colors
ctx.fillStyle = accent;

// Gradients
const g = ctx.createLinearGradient(x1, y1, x2, y2);
g.addColorStop(0, color1);
g.addColorStop(1, color2);
ctx.fillStyle = g;

// Glow effects
ctx.shadowBlur = 10;
ctx.shadowColor = color;
```

### Performance Optimization
```javascript
// Reuse arrays (GOOD)
analyser.getByteFrequencyData(dataArray);

// Don't allocate (BAD)
const data = new Uint8Array(bufferLength);

// Cache calculations (GOOD)
const step = width / count;
for(let i=0; i<count; i++) {
    const x = i * step;
}

// Don't recalculate (BAD)
for(let i=0; i<count; i++) {
    const x = (i / count) * width;
}
```

## File Structure

```
index.html
├── <head>
│   ├── <style> (CSS)
│   │   ├── Theme Variables
│   │   ├── Layout Styles
│   │   └── Component Styles
│   └── <script src="tailwind"> (CDN)
├── <body>
│   ├── Header (REVAMP title)
│   └── Main Window
│       ├── Main Deck
│       │   ├── Title Bar
│       │   ├── Info Display
│       │   ├── Visualizer Canvas
│       │   └── Controls
│       └── Playlist Deck
│           ├── Title Bar
│           ├── Playlist UI
│           └── Add Controls
└── <script> (JavaScript)
    ├── Theme System
    ├── Station Data
    ├── Engine Variables
    ├── Visual Variables
    ├── Audio Init
    ├── Render Loop
    ├── Simulation
    └── Event Handlers
```

## Documentation

When adding features, update:
- `.kiro/specs/revamp-exe.md` - Technical specification
- Code comments - Inline documentation
- This file - If adding new patterns

## Support

For questions or issues:
1. Check `.kiro/specs/revamp-exe.md` for technical details
2. Use agent hooks for common tasks:
   - `visualizer_request.txt` - Add new visualizer
   - `theme_request.txt` - Add new theme
   - `performance_audit.txt` - Optimize performance
   - `ui_check.txt` - Check UI consistency

## Version History

- **v2.1**: Current version (11 visualizers, 5 themes, dual-mode)
  - Added SPECTRAL CHAINS visualizer
- **v2.0**: 10 visualizers, 5 themes, dual-mode
- **v1.0**: Initial release (basic visualizer)

---

**Remember**: REVAMP.EXE is about simplicity, performance, and retro aesthetics. Keep it pure, keep it fast, keep it beautiful.
