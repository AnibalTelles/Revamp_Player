# Implementation Plan - Revamp Hybrid Audio Visualizer

- [x] 1. Set up project structure and HTML foundation





  - Create index.html with semantic structure for the three-deck layout (controls, canvas, input)
  - Create style.css with CSS variables for the Cyberpunk theme
  - Create app.js with basic application initialization
  - Link all files together and verify the page loads
  - _Requirements: 7.3_

- [x] 2. Implement application state management





  - Create AppState object to track currentSource, visualizerMode, isPlaying, volume, audioLoaded
  - Implement state update functions with validation
  - Add state change event system for UI updates
  - _Requirements: 1.1, 1.4, 1.5_

- [x] 2.1 Write property test for state management






  - **Property 1: Source toggle cycles state**
  - **Validates: Requirements 1.1**

- [x] 2.2 Write property test for mode change behavior






  - **Property 2: Mode change stops playback**
  - **Validates: Requirements 1.4**

- [x] 3. Build UI components and styling





  - Implement top deck with Play/Pause/Stop buttons and Volume slider
  - Implement middle deck with canvas element
  - Implement bottom deck with source toggle and input switcher
  - Apply retro styling with 3D bevels and rack-style layout
  - _Requirements: 7.1, 7.2, 7.4_

- [x] 3.1 Write property test for CSS variable usage






  - **Property 21: CSS variables are used consistently**
  - **Validates: Requirements 7.4**

- [x] 4. Implement source mode switching





  - Create source toggle button functionality
  - Implement UI switching between Drop Zone and YouTube input
  - Add logic to stop playback when switching modes
  - Update UI to reflect active mode
  - _Requirements: 1.1, 1.2, 1.3, 1.4, 1.5_

- [x] 4.1 Write property test for UI mode reflection





  - **Property 3: UI reflects current mode**
  - **Validates: Requirements 1.5**

- [x] 5. Implement Real Audio Engine for local MP3 playback





  - Create RealAudioEngine class with AudioContext and AnalyserNode
  - Implement load() method to decode MP3 files
  - Implement play(), pause(), stop() methods
  - Implement setVolume() method with GainNode
  - Implement getFrequencyData() method using AnalyserNode
  - Implement dispose() method for cleanup
  - _Requirements: 2.3, 2.4, 8.1_

- [ ]* 5.1 Write property test for Web Audio API isolation
  - **Property 6: Local mode uses Web Audio API**
  - **Validates: Requirements 2.3, 2.4**

- [ ]* 5.2 Write property test for Real Audio Engine isolation
  - **Property 22: Real Audio Engine isolation**
  - **Validates: Requirements 8.1**

- [x] 6. Implement local file upload with drag-and-drop





  - Create Drop Zone UI element with drag-and-drop handlers
  - Implement file validation to accept only MP3 files
  - Implement FileReader to load file data
  - Connect file loading to Real Audio Engine
  - Add error handling for invalid files and decode errors
  - _Requirements: 2.1, 2.2_

- [ ]* 6.1 Write property test for MP3 file acceptance
  - **Property 4: Valid MP3 files are accepted**
  - **Validates: Requirements 2.1**

- [ ]* 6.2 Write property test for invalid file rejection
  - **Property 5: Invalid files are rejected**
  - **Validates: Requirements 2.2**

- [x] 7. Implement Simulated Engine for YouTube playback





  - Create SimulatedEngine class with YouTube IFrame Player
  - Implement load() method to parse YouTube URLs and load videos
  - Implement play(), pause(), stop() methods using IFrame API
  - Implement setVolume() method
  - Implement generateFakeBeats() function using Math.sin and Math.random
  - Implement getFrequencyData() method that returns simulated data
  - Implement dispose() method for cleanup
  - _Requirements: 3.1, 3.3, 3.4, 9.1, 9.2, 9.3, 8.2_

- [ ]* 7.1 Write property test for YouTube URL parsing
  - **Property 8: Valid YouTube URLs are parsed**
  - **Validates: Requirements 3.1**

- [ ]* 7.2 Write property test for invalid URL rejection
  - **Property 9: Invalid YouTube URLs are rejected**
  - **Validates: Requirements 3.2**

- [ ]* 7.3 Write property test for YouTube IFrame API usage
  - **Property 10: YouTube mode uses IFrame API**
  - **Validates: Requirements 3.3**

- [ ]* 7.4 Write property test for simulated data generation
  - **Property 11: Simulated engine generates data**
  - **Validates: Requirements 3.4**

- [ ]* 7.5 Write property test for simulation algorithm
  - **Property 26: Simulation uses mathematical functions**
  - **Validates: Requirements 9.1, 9.2, 9.3**

- [ ]* 7.6 Write property test for Simulated Engine isolation
  - **Property 23: Simulated Engine isolation**
  - **Validates: Requirements 8.2**
-

- [x] 8. Implement YouTube input UI




  - Create text input field for YouTube URLs
  - Add submit button or Enter key handler
  - Implement URL validation and error display
  - Connect YouTube URL submission to Simulated Engine
  - Add error handling for invalid URLs and video loading errors
  - _Requirements: 3.1, 3.2_

- [x] 9. Implement engine abstraction and switching





  - Create common interface for both engines
  - Implement engine factory to instantiate correct engine based on mode
  - Implement engine switching logic with proper cleanup
  - Ensure consistent data format (Uint8Array, 0-255) from both engines
  - _Requirements: 8.3, 8.4, 8.5_

- [ ]* 9.1 Write property test for engine cleanup
  - **Property 24: Engine cleanup on switch**
  - **Validates: Requirements 8.3**

- [ ]* 9.2 Write property test for data format consistency
  - **Property 25: Consistent data format**
  - **Validates: Requirements 8.4, 8.5**

- [x] 10. Implement playback controls




  - Wire Play button to start playback on active engine
  - Wire Pause button to pause playback on active engine
  - Wire Stop button to stop playback and reset position
  - Wire Volume slider to update engine volume
  - Implement button enable/disable logic based on audioLoaded state
  - Update UI to reflect playback state
  - _Requirements: 4.1, 4.2, 4.3, 4.4, 4.5_

- [ ]* 10.1 Write property test for play functionality
  - **Property 12: Play button starts playback**
  - **Validates: Requirements 4.1**

- [ ]* 10.2 Write property test for pause functionality
  - **Property 13: Pause button pauses playback**
  - **Validates: Requirements 4.2**

- [ ]* 10.3 Write property test for stop functionality
  - **Property 14: Stop button resets playback**
  - **Validates: Requirements 4.3**

- [ ]* 10.4 Write property test for volume control
  - **Property 15: Volume changes are applied**
  - **Validates: Requirements 4.4**

- [x] 11. Implement visualizer rendering system





  - Create Visualizer class with canvas context
  - Implement 60 FPS rendering loop using requestAnimationFrame
  - Implement getFrequencyData callback to fetch data from active engine
  - Implement canvas clearing before each frame
  - Implement canvas resize handling
  - Connect visualizer to engine data flow
  - _Requirements: 2.5, 3.5, 6.1, 6.3, 6.4, 6.5_

- [ ]* 11.1 Write property test for data flow to visualizer
  - **Property 7: Frequency data flows to visualizer**
  - **Validates: Requirements 2.5, 3.5**

- [ ]* 11.2 Write property test for visualization stopping
  - **Property 18: Visualization stops with playback**
  - **Validates: Requirements 6.3**

- [ ]* 11.3 Write property test for canvas clearing
  - **Property 19: Canvas is cleared between frames**
  - **Validates: Requirements 6.4**

- [ ]* 11.4 Write property test for canvas adaptation
  - **Property 20: Visualization adapts to canvas size**
  - **Validates: Requirements 6.5**

- [ ]* 11.5 Write property test for source-agnostic rendering
  - **Property 27: Visualizer treats all data equally**
  - **Validates: Requirements 9.5**

- [x] 12. Implement Bars visualization mode





  - Create drawBars() method that renders vertical bars
  - Map frequency data array to bar heights
  - Apply color gradient from low to high frequencies
  - Add visual polish (spacing, rounded tops, glow effects)
  - _Requirements: 5.2_

- [x] 13. Implement Wave visualization mode





  - Create drawWave() method that renders oscilloscope-style waveform
  - Connect frequency data points with smooth lines
  - Apply retro green color with glow effect
  - Center waveform vertically on canvas
  - _Requirements: 5.3_

- [x] 14. Implement Matrix visualization mode





  - Create drawMatrix() method that renders falling characters
  - Generate random characters (katakana, numbers, symbols)
  - Implement falling animation with trails
  - Apply Matrix-style green color with fade effect
  - _Requirements: 5.4_

- [ ] 15. Implement visualizer mode switching
  - Add click handler to canvas element
  - Implement mode cycling logic (bars → wave → matrix → bars)
  - Update visualizer to use correct draw method based on mode
  - Ensure playback continues when mode changes
  - _Requirements: 5.1, 5.5_

- [ ]* 15.1 Write property test for mode cycling
  - **Property 16: Visualizer mode cycles correctly**
  - **Validates: Requirements 5.1**

- [ ]* 15.2 Write property test for playback preservation
  - **Property 17: Mode change preserves playback**
  - **Validates: Requirements 5.5**

- [ ] 16. Checkpoint - Ensure all tests pass
  - Ensure all tests pass, ask the user if questions arise.

- [ ] 17. Polish and final integration
  - Add loading indicators for file loading and YouTube video loading
  - Improve error message styling and positioning
  - Add keyboard shortcuts (spacebar for play/pause, arrow keys for volume)
  - Optimize canvas rendering performance
  - Test complete user flows across both modes
  - _Requirements: All_

- [ ]* 17.1 Write integration tests for complete user flows
  - Test: Load MP3 → Play → Change visualizer → Stop
  - Test: Load YouTube → Play → Switch to Local → Load MP3 → Play
  - Test: Error handling flows for invalid inputs
