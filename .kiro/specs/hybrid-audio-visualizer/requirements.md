# Requirements Document

## Introduction

Revamp is a hybrid audio visualizer application that combines retro aesthetics with modern web technologies. The system provides real-time audio visualization for both local MP3 files and YouTube streams, featuring a vertical rack-style interface inspired by classic audio equipment and Winamp. The application uses vanilla web technologies without frameworks or build steps, maintaining simplicity while delivering an engaging visual experience.

## Glossary

- **Revamp**: The hybrid audio visualizer application system
- **Local Mode**: Operating mode where the system plays audio from user-provided MP3 files
- **YouTube Mode**: Operating mode where the system streams audio from YouTube videos
- **Visualizer Canvas**: The display area where audio visualizations are rendered
- **Audio Engine**: The subsystem responsible for audio playback and analysis
- **Real Audio Engine**: The subsystem that handles local file playback using Web Audio API
- **Simulated Engine**: The subsystem that handles YouTube playback using YouTube IFrame API
- **Frequency Data**: Numerical array (0-255) representing audio spectrum information
- **Source Toggle**: UI control for switching between Local Mode and YouTube Mode
- **Drop Zone**: UI area for drag-and-drop file upload functionality
- **Visualizer Mode**: The active visualization style (Bars, Wave, or Matrix)

## Requirements

### Requirement 1

**User Story:** As a user, I want to switch between local MP3 files and YouTube streams, so that I can visualize audio from different sources.

#### Acceptance Criteria

1. WHEN a user clicks the Source Toggle control, THEN Revamp SHALL switch between Local Mode and YouTube Mode
2. WHILE in Local Mode, Revamp SHALL display the Drop Zone for file input
3. WHILE in YouTube Mode, Revamp SHALL display a text input field for YouTube URLs
4. WHEN the source mode changes, Revamp SHALL stop any currently playing audio
5. WHEN the source mode changes, Revamp SHALL update the UI to reflect the active mode

### Requirement 2

**User Story:** As a user, I want to play local MP3 files with real-time visualization, so that I can see my music represented visually.

#### Acceptance Criteria

1. WHEN a user drags an MP3 file into the Drop Zone, THEN Revamp SHALL accept the file and prepare it for playback
2. WHEN a user drops a non-MP3 file, THEN Revamp SHALL reject the file and display an error message
3. WHEN an MP3 file is loaded, THEN the Real Audio Engine SHALL use Web Audio API to decode and prepare the audio
4. WHEN audio plays in Local Mode, THEN the Real Audio Engine SHALL extract frequency data using AnalyserNode
5. WHEN frequency data is available, THEN Revamp SHALL pass the data array to the visualizer rendering function

### Requirement 3

**User Story:** As a user, I want to play YouTube videos with simulated visualization, so that I can visualize streaming content.

#### Acceptance Criteria

1. WHEN a user enters a valid YouTube URL, THEN Revamp SHALL extract the video ID and load the video
2. WHEN a user enters an invalid YouTube URL, THEN Revamp SHALL display an error message
3. WHEN a YouTube video is loaded, THEN the Simulated Engine SHALL use YouTube IFrame API for playback
4. WHEN audio plays in YouTube Mode, THEN the Simulated Engine SHALL generate simulated frequency data
5. WHEN simulated frequency data is generated, THEN Revamp SHALL pass the data array to the visualizer rendering function

### Requirement 4

**User Story:** As a user, I want standard playback controls, so that I can control audio playback regardless of source.

#### Acceptance Criteria

1. WHEN a user clicks the Play button with audio loaded, THEN Revamp SHALL start audio playback
2. WHEN a user clicks the Pause button during playback, THEN Revamp SHALL pause audio playback
3. WHEN a user clicks the Stop button during playback, THEN Revamp SHALL stop audio playback and reset position to the beginning
4. WHEN a user adjusts the Volume slider, THEN Revamp SHALL update the audio output volume accordingly
5. WHEN no audio is loaded, THEN Revamp SHALL disable Play, Pause, and Stop buttons

### Requirement 5

**User Story:** As a user, I want multiple visualization modes, so that I can choose different visual representations of the audio.

#### Acceptance Criteria

1. WHEN a user clicks the Visualizer Canvas, THEN Revamp SHALL cycle to the next Visualizer Mode
2. WHILE in Bars mode, Revamp SHALL render vertical bars representing frequency spectrum data
3. WHILE in Wave mode, Revamp SHALL render an oscilloscope-style waveform line
4. WHILE in Matrix mode, Revamp SHALL render falling green characters in a Matrix-style effect
5. WHEN the Visualizer Mode changes, THEN Revamp SHALL maintain the current playback state

### Requirement 6

**User Story:** As a user, I want smooth real-time visualization, so that the visual feedback is responsive and synchronized with the audio.

#### Acceptance Criteria

1. WHEN audio is playing, THEN Revamp SHALL update the Visualizer Canvas at 60 frames per second
2. WHEN frequency data is received, THEN Revamp SHALL render the visualization within the same frame
3. WHEN audio is paused or stopped, THEN Revamp SHALL stop updating the visualization
4. WHEN visualization updates occur, THEN Revamp SHALL clear the previous frame before rendering the new frame
5. WHEN the canvas is resized, THEN Revamp SHALL adjust the visualization to fit the new dimensions

### Requirement 7

**User Story:** As a user, I want a retro-styled interface with theme support, so that the application has a distinctive aesthetic that can be customized.

#### Acceptance Criteria

1. WHEN the application loads, THEN Revamp SHALL display a vertical rack-style layout with dark grey and black chassis
2. WHEN UI elements are rendered, THEN Revamp SHALL apply 3D bevel effects to create depth
3. WHEN the application loads, THEN Revamp SHALL apply the default Cyberpunk theme with CSS variables
4. WHEN CSS color variables are defined, THEN Revamp SHALL use them consistently throughout the interface
5. WHEN theme variables are modified, THEN Revamp SHALL update all themed elements accordingly

### Requirement 8

**User Story:** As a developer, I want clean separation between audio engines, so that the codebase is maintainable and modular.

#### Acceptance Criteria

1. WHEN the Real Audio Engine is invoked, THEN Revamp SHALL use only Web Audio API methods
2. WHEN the Simulated Engine is invoked, THEN Revamp SHALL use only YouTube IFrame API methods
3. WHEN switching between engines, THEN Revamp SHALL cleanly dispose of the previous engine's resources
4. WHEN either engine provides frequency data, THEN Revamp SHALL use a consistent data format (array of 0-255 values)
5. WHEN the visualizer requests data, THEN Revamp SHALL abstract the data source behind a common interface

### Requirement 9

**User Story:** As a user, I want the simulated visualizer to feel rhythmic, so that YouTube playback has engaging visual feedback even without real audio analysis.

#### Acceptance Criteria

1. WHEN the Simulated Engine generates frequency data, THEN Revamp SHALL use mathematical functions to create rhythmic patterns
2. WHEN simulated data is generated, THEN Revamp SHALL incorporate sine wave patterns to simulate periodic beats
3. WHEN simulated data is generated, THEN Revamp SHALL add random noise to create variation
4. WHEN simulated beats occur, THEN Revamp SHALL create visual pulses that appear synchronized with typical music rhythms
5. WHEN simulated data is provided to the visualizer, THEN Revamp SHALL render it identically to real frequency data
