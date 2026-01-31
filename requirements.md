# Pulmoai - Requirements

## 1. Overview

Pulmoai is a full-stack web application that uses Remote Photoplethysmography (rPPG) technology to detect vital signs from webcam video. The system provides real-time analysis of heart rate, SpO2 (blood oxygen saturation), and respiratory rate through advanced biomedical signal processing.

## 2. User Stories

### 2.1 Core Functionality
- **US-001**: As a user, I want to access my webcam through the browser so that I can record video for vital signs analysis
- **US-002**: As a user, I want to record a 10-second video sample so that the system can analyze my vital signs
- **US-003**: As a user, I want to see real-time feedback during recording so that I know the system is working
- **US-004**: As a user, I want to receive accurate measurements of my heart rate, SpO2, and respiratory rate
- **US-005**: As a user, I want to see confidence levels for measurements so that I can trust the results

### 2.2 User Experience
- **US-006**: As a user, I want a futuristic medical interface so that the experience feels professional and engaging
- **US-007**: As a user, I want animated results display so that the data presentation is visually appealing
- **US-008**: As a user, I want multilingual support (English/Hindi) so that I can use the system in my preferred language
- **US-009**: As a user, I want the system to work offline with simulated data so that I can demo the interface even without a backend connection

### 2.3 Technical Requirements
- **US-010**: As a developer, I want robust error handling so that the system gracefully handles edge cases
- **US-011**: As a developer, I want signal quality validation so that unreliable measurements are flagged
- **US-012**: As a developer, I want modular code architecture so that the system is maintainable and extensible

## 3. Acceptance Criteria

### 3.1 Video Processing (US-001, US-002)
- **AC-001**: System successfully accesses user's webcam with proper permissions
- **AC-002**: System records exactly 10 seconds of video at minimum 30 FPS
- **AC-003**: Video data is captured in a format suitable for rPPG analysis
- **AC-004**: System handles webcam access denial gracefully

### 3.2 Signal Processing (US-004)
- **AC-005**: Green channel intensity is extracted from each video frame
- **AC-006**: Signal is detrended using appropriate filtering techniques
- **AC-007**: Bandpass filter (0.7Hz - 3.5Hz) is applied for heart rate isolation
- **AC-008**: FFT analysis correctly identifies dominant frequency
- **AC-009**: Heart rate calculation: dominant_frequency * 60 BPM
- **AC-010**: SpO2 estimation uses AC/DC ratio from Red/Blue channels
- **AC-011**: Respiratory rate extracted from low-frequency component (0.1Hz - 0.5Hz)

### 3.3 Quality Assurance (US-005, US-011)
- **AC-012**: Signal-to-Noise Ratio (SNR) is calculated for confidence assessment
- **AC-013**: Low confidence warnings are displayed when signal quality is poor
- **AC-014**: System provides estimates even with low confidence signals
- **AC-015**: Confidence levels are displayed as percentages (0-100%)

### 3.4 User Interface (US-006, US-007)
- **AC-016**: Interface uses deep navy background (#0f172a) with neon cyan accents (#0ea5e9)
- **AC-017**: Glass morphism cards implemented with 10% white opacity and blur effects
- **AC-018**: Animated particle network background responds to mouse movement
- **AC-019**: HUD-style elements include corner brackets and rolling numbers
- **AC-020**: Results display with animated counting up to final values
- **AC-021**: Circular camera view with scanning animation overlay during recording

### 3.5 Real-time Feedback (US-003)
- **AC-022**: Countdown timer displays during 10-second recording
- **AC-023**: Real-time waveform visualization shows during recording
- **AC-024**: Visual indicators show recording status (recording/processing/complete)
- **AC-025**: Progress indicators update smoothly without jarring transitions

### 3.6 Multilingual Support (US-008)
- **AC-026**: Language dropdown allows switching between English and Hindi
- **AC-027**: All UI text elements support both languages
- **AC-028**: Language preference persists across sessions
- **AC-029**: Translations are accurate and contextually appropriate

### 3.7 Offline Functionality (US-009)
- **AC-030**: System detects when backend is unavailable
- **AC-031**: Offline mode generates realistic simulated vital signs data
- **AC-032**: Offline mode is clearly indicated to users
- **AC-033**: Simulated data includes appropriate randomization and ranges

### 3.8 Error Handling (US-010)
- **AC-034**: All Python functions include try/except blocks
- **AC-035**: Network errors are handled gracefully with user-friendly messages
- **AC-036**: Invalid video data is detected and reported
- **AC-037**: System recovers from processing errors without crashing

### 3.9 Performance Requirements
- **AC-038**: Video processing completes within 30 seconds of upload
- **AC-039**: Interface remains responsive during processing
- **AC-040**: Memory usage stays within reasonable limits during video analysis
- **AC-041**: System works on modern browsers (Chrome, Firefox, Safari, Edge)

### 3.10 Technical Architecture
- **AC-042**: Flask backend serves static files and API endpoints
- **AC-043**: OpenCV processes video frames efficiently
- **AC-044**: SciPy handles signal processing operations
- **AC-045**: Frontend uses vanilla JavaScript without external frameworks
- **AC-046**: Code follows PEP 8 standards for Python and standard conventions for JavaScript

## 4. Non-Functional Requirements

### 4.1 Accuracy
- Heart rate measurements should be within ±5 BPM of actual values under good conditions
- SpO2 estimates should provide reasonable approximations (±3% under ideal conditions)
- Respiratory rate should be within ±2 breaths per minute

### 4.2 Usability
- System should be intuitive for users without medical background
- Recording process should complete in under 15 seconds total
- Results should be clearly presented with appropriate units

### 4.3 Reliability
- System should handle poor lighting conditions gracefully
- Motion artifacts should be detected and flagged
- Consistent results across multiple measurements under similar conditions

### 4.4 Security
- No video data should be permanently stored on the server
- Webcam access should follow browser security protocols
- User privacy should be maintained throughout the process

## 5. Technical Constraints

### 5.1 Browser Compatibility
- Must work on modern browsers with WebRTC support
- Requires HTTPS for webcam access in production
- Canvas API required for particle effects and visualizations

### 5.2 Hardware Requirements
- Webcam with minimum 30 FPS capability
- Adequate lighting for facial detection
- Stable internet connection for backend communication

### 5.3 Dependencies
- Python: Flask, OpenCV, SciPy, NumPy
- Frontend: HTML5, CSS3, ES6+ JavaScript
- No external JavaScript frameworks or libraries

## 6. Success Metrics

### 6.1 Functional Success
- 95% successful webcam access rate
- 90% successful video processing rate
- Confidence scores above 70% in good lighting conditions

### 6.2 User Experience Success
- Average recording-to-results time under 45 seconds
- User interface loads within 3 seconds
- Smooth animations at 60 FPS

### 6.3 Technical Success
- Zero critical errors in signal processing pipeline
- Graceful degradation in poor conditions
- Consistent performance across supported browsers