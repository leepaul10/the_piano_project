# 🎹 Gesture Piano — Play Piano in the Air with Your Hands

A real-time, touchless piano application that uses your webcam and hand gestures to play piano notes — no physical keyboard required. Built with computer vision, hand tracking AI, and synthesized audio.

---

## 🎥 Demo

> Point your fingers toward the on-screen piano keys and press forward to play. The system detects your hand in real time, checks if your fingers are straight and extended, and triggers piano notes based on depth (Z-axis) movement.

---

## 🧠 How It Works

1. **Webcam captures** your hand in real time using a background thread for zero-lag frame reading
2. **MediaPipe AI** detects 21 landmarks on your hand — fingertips, joints, and knuckles
3. **Finger logic** checks two conditions per finger:
   - Is the finger pointing upward? (vertical alignment check)
   - Is the finger fully extended, not curled? (distance ratio check)
4. **Key detection** maps your fingertip's X/Y position to a piano key on screen
5. **Depth trigger** detects a forward "press" motion using the Z-axis (how close your finger is to the camera)
6. **Synthesized audio** plays a realistic piano tone using layered harmonic sine waves with attack and decay envelopes

---

## ✨ Features

- 🖐️ Tracks up to **2 hands simultaneously**
- 🎵 **25 piano keys** spanning C4 to C6 (white and black keys)
- 🔊 **Synthesized piano sound** — no audio files needed, all generated mathematically
- ⚡ **Real-time performance** using multithreading and GPU acceleration (OpenCL)
- 🎛️ **64 simultaneous audio channels** — play chords without notes cutting each other off
- 🟡 **Visual finger indicators** — yellow dot = active finger, black dot = inactive
- 🟢 **Key press visualization** — keys glow green when triggered
- 🔴 **Trigger line** — red line shows the zone where key presses are registered

---

## 🛠️ Tech Stack

| Technology | Purpose |
|---|---|
| **Python** | Core language |
| **OpenCV** | Camera capture, frame processing, drawing UI |
| **MediaPipe** | Hand landmark detection (Google AI) |
| **Pygame** | Audio synthesis and playback |
| **NumPy** | Mathematical sound wave generation |
| **Threading** | Background camera stream for lag-free performance |

---

## 📦 Installation

```bash
# Clone the repository
git clone https://github.com/leepaul10/gesture-piano.git
cd gesture-piano

# Install dependencies
pip install opencv-python mediapipe pygame numpy
```

---

## ▶️ Run

```bash
python hand_piano.py
```

> Make sure your webcam is connected. Press `Q` or `Esc` to quit.

---

## 🎮 How to Play

1. Run the script — a window will open showing your webcam feed with the piano at the bottom
2. Hold your hand in front of the camera with fingers pointing **upward**
3. Move your hand so a fingertip hovers **above a key**
4. **Push your finger forward** (toward the camera) to press the note
5. Pull back to release

**Tips:**
- Keep your fingers **straight and extended** — curled fingers are ignored intentionally
- The **red line** is the trigger boundary — your fingertip must cross it to register
- Black keys are checked first, so sharps/flats are always correctly prioritized
- Play slowly at first to get a feel for the Z-depth sensitivity

---

## 🔬 Finger Detection Logic

The system uses a two-step check per finger every frame:

```
Step 1 — Vertical check:
  tip.y < pip.y < base.y
  → Fingertip must be higher on screen than middle joint, which must be higher than knuckle

Step 2 — Extension check:
  distance(tip → base) > distance(pip → base) × 1.15
  → Tip must be at least 15% farther from the knuckle than the middle joint is
  → Catches curled fingers that pass the vertical check alone

Both must be TRUE → finger is active
```

The thumb is intentionally excluded — its geometry is too different for reliable air-press detection.

---

## 🏗️ System Architecture

```
Webcam
  └── CameraStream (background thread)
        └── Frame captured at 640×480, buffered to 1 frame

Main Loop (every frame)
  ├── Flip + Resize frame to 1005×620
  ├── MediaPipe processes a 480×240 thumbnail (speed optimization)
  ├── Per hand → Per finger:
  │     ├── Vertical check (is_straight_v)
  │     ├── Extension check (is_fully_extended)
  │     ├── Key collision detection (black keys first, then white)
  │     └── Z-depth logging (keys_currently_touched)
  ├── Key press/release logic (PRESS_THRESHOLD_Z = -0.060)
  └── Draw: keys, trigger line, finger dots → cv2.imshow()

Audio
  └── Sounds pre-generated at startup (generate_piano)
        └── 4 harmonic sine waves + attack envelope + exponential decay
```

---

## ⚙️ Configuration

You can tweak these constants at the top of the file:

| Constant | Default | Effect |
|---|---|---|
| `PRESS_THRESHOLD_Z` | `-0.060` | How far forward you push to trigger a note (lower = harder press needed) |
| `RELEASE_THRESHOLD_Z` | `-0.030` | How far back to release (creates a dead zone to prevent flickering) |
| `WHITE_TRIGGER_LINE` | `65% of piano height` | Where the active zone starts on white keys |
| `max_num_hands` | `2` | Number of hands tracked simultaneously |
| `min_detection_confidence` | `0.6` | How confident MediaPipe must be before reporting a hand |

---

## 🌍 Real-World Applications

This project demonstrates techniques used in:

- **Accessibility technology** — touchless instrument control for people with physical disabilities
- **Medical rehabilitation** — gamified finger movement therapy with real-time feedback
- **AR/VR interfaces** — gesture-based control systems (similar to Meta Quest hand tracking)
- **Human-Computer Interaction** — touchless UI control in sterile or remote environments
- **Music education** — no-hardware interactive piano learning tools

---

## 👤 Author

**Lee Paul Babu K**
- GitHub: [github.com/leepaul10](https://github.com/leepaul10)
- LinkedIn: [linkedin.com/in/leepaulbabuk](https://linkedin.com/in/leepaulbabuk)
- Email: leepaulkbabu@gmail.com

---

## 📄 License

MIT License — free to use, modify, and distribute.