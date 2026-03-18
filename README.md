# 🎹 YAMAHA MONTAGE M SYNTH — Gesture Piano

Ever wanted to play piano without actually owning one? This project lets you do exactly that. Using just your webcam, it tracks your hand in real time and turns your finger movements into actual piano notes — no keyboard, no MIDI controller, nothing.

---

## What It Does

The program opens your webcam and overlays a piano keyboard at the bottom of the screen. You hold your hand up in front of the camera, move your fingers over the keys, and push forward to play a note — just like pressing a real key. It supports both hands at the same time, tracks all four fingers (index, middle, ring, pinky), and plays up to 64 notes simultaneously so chords work fine.

The piano spans **25 keys from C4 to C6**, including all the black keys (sharps and flats). The sound is generated mathematically using layered sine wave harmonics with a proper attack and decay — so it actually sounds like a piano, not a beep.

---

## How the Finger Detection Works

MediaPipe detects 21 landmarks on your hand every frame. For each finger, the program runs two checks:

**1. Is the finger pointing upward?**
It checks that the fingertip is higher on screen than the middle joint, which is higher than the knuckle. If that order is correct, the finger is considered straight.

**2. Is the finger fully extended?**
It measures the straight-line distance from the knuckle to the tip, and compares it to the distance from the knuckle to the middle joint. If the tip is at least 15% farther out, the finger isn't curled — it's open.

Both checks must pass for the finger to be "active" (shown as a yellow dot on screen). A black dot means the finger is curled or not usable.

Once a finger is active, the program checks which key the fingertip is hovering over and watches the Z-axis (depth). When your finger pushes close enough to the camera, the note plays. Pull back and it releases.

The **thumb is intentionally skipped** — its movement pattern is too different to work reliably for air pressing.

---

## Installation

```bash
pip install opencv-python mediapipe pygame numpy
```

That's all you need. No audio files, no extra downloads.

---

## Running It

```bash
python hand_piano.py
```

A window titled **YAMAHA-MONTAGE-M-SYNTH** will open showing your webcam feed with the piano overlaid at the bottom.

Press `Q` or `Esc` to close.

---

## Playing Tips

- Hold your hand with fingers **pointing straight up** toward the camera
- Move your hand so a fingertip sits above the key you want
- **Push your finger forward** (toward the camera) to trigger the note — the key will glow **green**
- **Pull back** to release it
- Black keys are checked before white keys, so sharps always get priority if your finger is on the boundary
- The **red line** on screen marks the trigger zone — your fingertip has to cross it for white keys to register

---

## Under the Hood

- **Camera** runs on a background thread so the main loop never waits for a frame — keeps everything smooth
- **MediaPipe** processes a downscaled 480×240 version of the frame for speed, while the display stays at 1005×620
- **Audio** uses 44100 Hz sample rate, 16-bit stereo, with a 512-sample buffer for low latency
- **64 audio channels** are open simultaneously so notes never cut each other off
- OpenCL GPU acceleration is enabled for faster frame processing

---

**Lee Paul Babu K** — [GitHub](https://github.com/leepaul10) · 
