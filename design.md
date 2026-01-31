# Pulmoai - Design Document

## 1. System Architecture

### 1.1 High-Level Architecture
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Frontend      │    │   Flask API     │    │ Signal Processing│
│   (Browser)     │◄──►│   Server        │◄──►│   Pipeline      │
│                 │    │                 │    │                 │
│ • WebRTC        │    │ • Video Upload  │    │ • rPPG Analysis │
│ • UI/UX         │    │ • Static Files  │    │ • FFT Processing│
│ • Visualization │    │ • Error Handling│    │ • Vital Signs   │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

### 1.2 Technology Stack
- **Backend**: Python 3.8+, Flask, OpenCV, SciPy, NumPy
- **Frontend**: HTML5, CSS3, JavaScript ES6+, Canvas API, WebRTC
- **Processing**: Computer Vision, Digital Signal Processing, FFT Analysis
- **Deployment**: Self-contained Flask application

## 2. Backend Design

### 2.1 Flask Application Structure
```python
# server.py structure
class HealthMonitoringApp:
    - __init__(): Initialize Flask app and configure routes
    - serve_index(): Serve main HTML page
    - upload_video(): Handle video upload and processing
    - process_video(): Core rPPG analysis pipeline
    - extract_signals(): Extract RGB signals from video frames
    - calculate_vitals(): Compute heart rate, SpO2, respiratory rate
    - assess_confidence(): Evaluate signal quality and reliability
```

### 2.2 rPPG Signal Processing Pipeline

#### 2.2.1 Video Frame Processing
```python
def extract_signals(video_path):
    """
    Extract RGB signals from video frames
    
    Steps:
    1. Read video using OpenCV
    2. Detect face region (optional ROI optimization)
    3. Calculate average RGB values per frame
    4. Return time-series signals for each channel
    """
```

#### 2.2.2 Signal Preprocessing
```python
def preprocess_signal(signal, fps):
    """
    Prepare signal for frequency analysis
    
    Steps:
    1. Detrend signal using scipy.signal.detrend
    2. Apply Hamming window to reduce spectral leakage
    3. Normalize signal amplitude
    4. Handle edge cases (too short, too noisy)
    """
```

#### 2.2.3 Frequency Domain Analysis
```python
def analyze_frequency_domain(signal, fps):
    """
    Extract vital signs from frequency spectrum
    
    Heart Rate Analysis:
    1. Apply bandpass filter (0.7Hz - 3.5Hz) → 42-210 BPM
    2. Perform FFT using numpy.fft.fft
    3. Find dominant frequency peak
    4. Convert to BPM: peak_frequency * 60
    
    Respiratory Rate Analysis:
    1. Apply low-pass filter (0.1Hz - 0.5Hz) → 6-30 breaths/min
    2. Extract low-frequency component
    3. Find respiratory frequency peak
    4. Convert to breaths per minute
    """
```

#### 2.2.4 SpO2 Estimation
```python
def estimate_spo2(red_signal, blue_signal):
    """
    Estimate blood oxygen saturation
    
    Method:
    1. Calculate AC component (signal variation)
    2. Calculate DC component (signal baseline)
    3. Compute ratio: (AC_red/DC_red) / (AC_blue/DC_blue)
    4. Apply calibration curve or use empirical formula
    5. Clamp result to physiologically valid range (70-100%)
    """
```

### 2.3 Quality Assessment
```python
def assess_signal_quality(signal, vital_signs):
    """
    Evaluate measurement confidence
    
    Factors:
    1. Signal-to-Noise Ratio (SNR)
    2. Frequency peak prominence
    3. Signal stability over time
    4. Physiological plausibility of results
    
    Returns: confidence_score (0-100%)
    """
```

### 2.4 API Endpoints

#### 2.4.1 Main Page Endpoint
```python
@app.route('/')
def index():
    """Serve the main HTML interface"""
    return send_from_directory('static', 'index.html')
```

#### 2.4.2 Video Processing Endpoint
```python
@app.route('/upload', methods=['POST'])
def upload_video():
    """
    Process uploaded video and return vital signs
    
    Request: multipart/form-data with video file
    Response: JSON with vital signs and confidence scores
    
    {
        "success": true,
        "heart_rate": 72,
        "spo2": 98,
        "respiratory_rate": 16,
        "confidence": {
            "heart_rate": 85,
            "spo2": 70,
            "respiratory_rate": 78
        },
        "warnings": ["Low lighting detected"]
    }
    """
```

## 3. Frontend Design

### 3.1 User Interface Architecture

#### 3.1.1 HTML Structure
```html
<!DOCTYPE html>
<html>
<head>
    <!-- Meta tags, title, CSS imports -->
</head>
<body>
    <!-- Particle background canvas -->
    <canvas id="particleCanvas"></canvas>
    
    <!-- Main application container -->
    <div class="app-container">
        <!-- Header with language selector -->
        <header class="app-header">
            <select id="languageSelector">
                <option value="en">English</option>
                <option value="hi">हिंदी</option>
            </select>
        </header>
        
        <!-- Central camera interface -->
        <main class="camera-section">
            <div class="camera-container">
                <video id="cameraFeed"></video>
                <div class="scanning-overlay"></div>
                <div class="countdown-timer"></div>
            </div>
            
            <!-- Control buttons -->
            <div class="controls">
                <button id="startRecording">Start Scan</button>
                <button id="stopRecording">Stop</button>
            </div>
        </main>
        
        <!-- Results dashboard -->
        <section class="results-dashboard">
            <div class="vital-card" id="heartRateCard">
                <div class="vital-value" id="heartRateValue">--</div>
                <div class="vital-label">Heart Rate (BPM)</div>
                <div class="confidence-bar"></div>
            </div>
            
            <div class="vital-card" id="spo2Card">
                <div class="vital-value" id="spo2Value">--</div>
                <div class="vital-label">SpO2 (%)</div>
                <div class="confidence-bar"></div>
            </div>
            
            <div class="vital-card" id="respiratoryCard">
                <div class="vital-value" id="respiratoryValue">--</div>
                <div class="vital-label">Respiratory Rate</div>
                <div class="confidence-bar"></div>
            </div>
        </section>
        
        <!-- Waveform visualization -->
        <section class="waveform-section">
            <canvas id="waveformCanvas"></canvas>
        </section>
    </div>
</body>
</html>
```

#### 3.1.2 CSS Design System
```css
/* Color Palette */
:root {
    --bg-primary: #0f172a;      /* Deep navy */
    --accent-cyan: #0ea5e9;     /* Neon cyan */
    --glass-white: rgba(255, 255, 255, 0.1);
    --glass-border: rgba(255, 255, 255, 0.2);
    --text-primary: #ffffff;
    --text-secondary: #94a3b8;
    --success-green: #10b981;
    --warning-yellow: #f59e0b;
    --error-red: #ef4444;
}

/* Glass Morphism Components */
.glass-card {
    background: var(--glass-white);
    backdrop-filter: blur(10px);
    border: 1px solid var(--glass-border);
    border-radius: 16px;
    box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
}

/* HUD Elements */
.hud-element {
    position: relative;
    border: 2px solid var(--accent-cyan);
    border-radius: 4px;
}

.hud-element::before,
.hud-element::after {
    content: '';
    position: absolute;
    width: 20px;
    height: 20px;
    border: 2px solid var(--accent-cyan);
}

/* Corner brackets for sci-fi effect */
.hud-element::before {
    top: -2px;
    left: -2px;
    border-right: none;
    border-bottom: none;
}

.hud-element::after {
    bottom: -2px;
    right: -2px;
    border-left: none;
    border-top: none;
}
```

### 3.2 JavaScript Application Logic

#### 3.2.1 Main Application Class
```javascript
class PulmoaiMonitor {
    constructor() {
        this.mediaRecorder = null;
        this.recordedChunks = [];
        this.isRecording = false;
        this.currentLanguage = 'en';
        this.translations = {
            en: { /* English translations */ },
            hi: { /* Hindi translations */ }
        };
    }
    
    async init() {
        await this.setupCamera();
        this.setupEventListeners();
        this.initializeParticleSystem();
        this.initializeWaveform();
    }
    
    async setupCamera() {
        // Request webcam access
        // Configure MediaRecorder
        // Handle permissions and errors
    }
    
    startRecording() {
        // Begin 10-second recording
        // Show countdown timer
        // Start waveform visualization
    }
    
    stopRecording() {
        // Stop recording
        // Process video blob
        // Upload to backend
    }
    
    async processVideo(videoBlob) {
        // Upload video to /upload endpoint
        // Handle response
        // Update UI with results
        // Animate number counting
    }
    
    displayResults(data) {
        // Animate vital signs display
        // Update confidence bars
        // Show warnings if any
    }
    
    switchLanguage(lang) {
        // Update all UI text
        // Save preference
    }
    
    enableOfflineMode() {
        // Generate simulated data
        // Show offline indicator
        // Maintain full functionality
    }
}
```

#### 3.2.2 Particle System
```javascript
class ParticleSystem {
    constructor(canvas) {
        this.canvas = canvas;
        this.ctx = canvas.getContext('2d');
        this.particles = [];
        this.mouse = { x: 0, y: 0 };
    }
    
    createParticles() {
        // Generate constellation-like particles
        // Connect nearby particles with lines
        // Respond to mouse movement
    }
    
    animate() {
        // Update particle positions
        // Draw connections
        // Create pulsing effect
    }
}
```

#### 3.2.3 Waveform Visualization
```javascript
class WaveformVisualizer {
    constructor(canvas) {
        this.canvas = canvas;
        this.ctx = canvas.getContext('2d');
        this.dataPoints = [];
    }
    
    updateWaveform(audioLevel) {
        // Add new data point
        // Shift existing points
        // Redraw waveform
    }
    
    drawWaveform() {
        // Clear canvas
        // Draw grid lines
        // Draw waveform curve
        // Add glow effects
    }
}
```

## 4. Data Flow

### 4.1 Recording Process
```
1. User clicks "Start Scan"
2. Request webcam access
3. Begin MediaRecorder with 10-second timer
4. Show countdown and waveform visualization
5. Stop recording automatically after 10 seconds
6. Convert recorded data to Blob
7. Upload Blob to Flask backend
```

### 4.2 Processing Pipeline
```
1. Flask receives video file
2. Save temporary file
3. Extract frames using OpenCV
4. Calculate RGB channel averages
5. Apply signal processing pipeline
6. Compute vital signs and confidence scores
7. Return JSON response
8. Delete temporary file
```

### 4.3 Results Display
```
1. Frontend receives JSON response
2. Parse vital signs data
3. Animate number counting to final values
4. Update confidence bars
5. Display any warnings
6. Enable new recording
```

## 5. Error Handling Strategy

### 5.1 Backend Error Handling
```python
# Comprehensive try-catch blocks
try:
    # Video processing logic
    pass
except cv2.error as e:
    return {"error": "Video processing failed", "details": str(e)}
except ValueError as e:
    return {"error": "Invalid video format", "details": str(e)}
except Exception as e:
    return {"error": "Unexpected error", "details": str(e)}
```

### 5.2 Frontend Error Handling
```javascript
// Network error handling
async function uploadVideo(blob) {
    try {
        const response = await fetch('/upload', {
            method: 'POST',
            body: formData
        });
        
        if (!response.ok) {
            throw new Error(`HTTP ${response.status}`);
        }
        
        return await response.json();
    } catch (error) {
        console.error('Upload failed:', error);
        this.enableOfflineMode();
        return this.generateSimulatedData();
    }
}
```

## 6. Performance Considerations

### 6.1 Video Processing Optimization
- Limit video resolution to 640x480 for faster processing
- Process every 2nd or 3rd frame if performance is poor
- Use efficient NumPy operations for signal processing
- Implement early termination for poor quality signals

### 6.2 Frontend Optimization
- Use requestAnimationFrame for smooth animations
- Debounce particle system updates
- Lazy load non-critical components
- Optimize canvas rendering with double buffering

## 7. Security Considerations

### 7.1 Data Privacy
- No permanent storage of video files
- Temporary files deleted immediately after processing
- No transmission of raw video data to external services
- Local processing only

### 7.2 Input Validation
- Validate video file format and size
- Sanitize all user inputs
- Implement rate limiting for upload endpoint
- Check file headers to prevent malicious uploads

## 8. Testing Strategy

### 8.1 Unit Tests
- Signal processing functions
- Vital signs calculation accuracy
- Error handling scenarios
- API endpoint responses

### 8.2 Integration Tests
- End-to-end video processing pipeline
- Frontend-backend communication
- Offline mode functionality
- Cross-browser compatibility

### 8.3 Property-Based Tests
- **Property 1**: Heart rate values should be within physiological range (30-220 BPM)
- **Property 2**: SpO2 values should be between 70-100%
- **Property 3**: Respiratory rate should be between 6-40 breaths/min
- **Property 4**: Confidence scores should be between 0-100%
- **Property 5**: Processing time should be under 30 seconds for 10-second videos

## 9. Deployment Configuration

### 9.1 File Structure
```
kiro-health-monitoring/
├── server.py
├── requirements.txt
├── static/
│   ├── index.html
│   ├── style.css
│   └── app.js
└── temp/
    └── (temporary video files)
```

### 9.2 Dependencies (requirements.txt)
```
Flask==2.3.3
opencv-python==4.8.1.78
scipy==1.11.3
numpy==1.24.3
```

### 9.3 Startup Command
```bash
python server.py
# Server runs on http://localhost:5000
```

This design provides a comprehensive foundation for implementing the Kiro Health Monitoring System with robust signal processing, modern UI/UX, and reliable error handling.