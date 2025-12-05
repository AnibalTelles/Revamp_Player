# Revamp - Tech Stack Rules

## Core Technology
- Use vanilla HTML5, CSS3, and JavaScript
- NO frameworks (No React, No Vue, No Angular)
- NO build steps or bundlers
- Keep it simple and retro

## File Structure
- `index.html` - Main HTML file
- `style.css` - All styling
- `app.js` - All JavaScript logic

## Styling Guidelines
- Use CSS Variables for all colors (enables easy theme swapping)
- Implement pixel-art/retro aesthetic
- Winamp-inspired UI design

## Audio Implementation
- **Local Files**: Use Web Audio API for playing local audio files
- **YouTube Streams**: Use YouTube IFrame API for streaming

## Architecture Principles
- Maintain strict separation between:
  - **Real Audio Engine**: Handles local file playback via Web Audio API
  - **Simulated Engine**: Handles YouTube playback via IFrame API
- Keep audio engines independent and modular
