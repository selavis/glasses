# Smart Glasses for Blind Students — Recommended Technology, Equipment, and Stack

This document proposes a practical, competition-ready plan for your school robotics project.

## 1) Product definition

You described two modes:

1. **Mobility mode (outdoor/indoor walking):**
   - Detect obstacles (walls, poles, people, objects).
   - Detect sidewalks/crosswalk lines.
   - Detect traffic lights and signal state (red/green).
   - Read signs/tables on request.
   - Give real-time spoken guidance.

2. **Classroom mode:**
   - Read teacher handwriting on the blackboard/whiteboard.
   - Speak equations and text.

The best approach is a **multimodal wearable edge-AI system** with optional cloud backup.

---

## 2) Recommended high-level architecture

Use a **hybrid architecture**:

- **On-device (edge)** for safety-critical and low-latency tasks:
  - Obstacle detection
  - Navigation cues
  - Traffic light detection
  - Immediate text reading
- **Phone/cloud assist** for heavy AI tasks:
  - Better handwriting OCR fallback
  - Advanced scene explanation
  - Model updates

### Why hybrid?
- Edge gives fast response and works with weak internet.
- Cloud improves recognition quality when available.
- Safer than cloud-only for walking guidance.

---

## 3) Hardware recommendation (best balance for student team)

## A. Compute
- **Primary wearable compute:**
  - **NVIDIA Jetson Orin Nano (8GB)** if budget allows (best CV performance in student robotics).
  - Budget option: **Raspberry Pi 5 + Hailo-8 accelerator**.
- **Companion smartphone** (Android preferred):
  - Handles UI, network, optional cloud AI, and easy user setup.

## B. Vision sensors
- **Stereo/depth camera** (for distance + obstacle geometry):
  - OAK-D Lite / Intel RealSense D435i (if available in your region).
- **Wide RGB camera** (for signs/blackboard):
  - 8MP or better, global shutter preferred.

## C. Auxiliary sensors
- **IMU** (orientation and stability): often included in depth modules.
- **GPS** (optional for outdoor localization): phone GPS is enough initially.
- **Bone-conduction headphones** (recommended):
  - Lets user hear environment + guidance.

## D. Input controls
- **Physical button(s)** on frame or chest module:
  - one press: “Describe scene”
  - long press: “Read text”
  - double press: “Class mode”
- Optional: small touchpad or voice wake word.

## E. Power
- **Battery pack 10,000–20,000mAh** (USB-C PD).
- Target total runtime: 3–5 hours for demo/competition.

---

## 4) Software stack (what to learn/build)

Because you already know ReactJS, the smartest stack is:

## A. On-device AI services
- **Language:** Python (fast prototyping, huge CV ecosystem).
- **Core libraries:**
  - OpenCV
  - PyTorch or ONNX Runtime
  - Ultralytics YOLO (object detection)
  - Depth SDK (OAK/RealSense)
  - Tesseract + EasyOCR or PaddleOCR (text)
  - Whisper (speech-to-text for commands)
  - Piper / Coqui TTS (offline text-to-speech)

## B. Backend/API layer
- **FastAPI** on device or phone gateway.
- Services:
  - `/detect/obstacles`
  - `/detect/traffic-light`
  - `/read/text`
  - `/read/board`
  - `/agent/scene`

## C. App/UI (your strength)
- **React Native** (instead of web React) for smartphone app.
- Why React Native:
  - Reuses your React knowledge.
  - Accesses mic, Bluetooth, background tasks, push-to-talk.
  - Easy competition demo UI.

## D. Optional cloud services
- If internet exists:
  - GPT-4o-like multimodal model for richer scene narration.
  - Cloud handwriting OCR fallback.
- Always keep local fallback for safety.

---

## 5) AI model recommendations by feature

## 1) Obstacle avoidance
- **Model:** YOLOv8n / YOLOv11n + depth fusion.
- **Logic:** classify + estimate distance + urgency scoring.
- **Output examples:**
  - “Person 2 meters ahead, slightly right.”
  - “Wall very close in front, step left.”

## 2) Sidewalk / crosswalk / lane-like ground markings
- **Model type:** segmentation model (Fast-SCNN, BiSeNet, or lightweight DeepLab variants).
- **Alternative:** train a small custom model on local street images.

## 3) Traffic light detection and state
- **Model:** object detection + color-state classifier.
- Add temporal smoothing to avoid flickering decisions.

## 4) On-demand sign/table reading
- **Pipeline:** text detection → OCR → language cleanup → TTS.
- OCR options:
  - PaddleOCR (good multilingual results)
  - EasyOCR (easy setup)

## 5) Blackboard handwriting mode
- **Preprocess:** perspective correction, denoise, contrast enhancement.
- **OCR choices:** TrOCR / PARSeq / PaddleOCR handwriting models.
- **Equations:**
  - Use formula OCR model (Pix2Tex or similar) to convert to LaTeX.
  - Convert LaTeX to spoken math format (“x squared plus 2x plus 1”).

## 6) Scene agent narration
- **Edge first:** rule-based summary from detections.
- **Cloud enhanced:** multimodal LLM for natural explanation.

---

## 6) Suggested system design (modules)

- `vision_service` (camera ingestion, frame sync)
- `perception_service` (obstacles, traffic lights, segmentation)
- `ocr_service` (signs + board handwriting)
- `assist_agent` (fusion + response policy)
- `audio_service` (ASR + TTS + sound cues)
- `mobile_app` (React Native control panel)

Use pub/sub messaging (Redis, ZeroMQ, or lightweight internal queue).

---

## 7) Safety + UX rules (very important for competition)

- Priority 1 messages are collision warnings only.
- Avoid speaking continuously; use concise cues.
- Add confidence threshold (“Not sure, please scan again”).
- Keep latency target < 500 ms for obstacle alerts.
- Provide silent mode + vibration alerts.
- Never market as full replacement for cane/guide dog.

---

## 8) Build roadmap (12-week example)

## Phase 1 (Weeks 1–3): MVP navigation
- Camera + YOLO obstacle detection
- Simple distance via depth
- TTS alerts

## Phase 2 (Weeks 4–6): traffic + crossing
- Traffic light detection
- Sidewalk/crosswalk segmentation
- Safer guidance logic

## Phase 3 (Weeks 7–9): reading mode
- On-demand sign OCR
- React Native controls
- Voice commands

## Phase 4 (Weeks 10–12): classroom mode
- Blackboard OCR + equation reading
- Demo scripts + robustness tuning
- Competition presentation pack

---

## 9) Team roles for a school league

- **AI/CV Lead:** models and inference optimization
- **Embedded Lead:** sensors, power, mounting, thermals
- **App Lead (you):** React Native UX and orchestration
- **Testing Lead:** scenario testing and metrics
- **Presentation Lead:** pitch, social impact, evidence

---

## 10) What stack should *you* personally start with this week?

Given your ReactJS background, use this concrete stack:

- **Frontend/App:** React Native + Expo
- **Device API:** FastAPI (Python)
- **CV:** YOLOv8n + OpenCV + depth SDK
- **OCR:** PaddleOCR
- **Speech:** Whisper tiny + Piper TTS
- **Data flow:** WebSocket for real-time events
- **Storage:** SQLite for logs/events

This gives a fast path to a working demo while leaving room to upgrade models later.

---

## 11) Minimum competition demo checklist

- Wearable prototype mounted safely
- Real-time obstacle warnings (indoor track)
- Traffic light simulation recognition
- Sign reading on demand
- Blackboard text/equation reading demo
- Measured metrics:
  - detection accuracy
  - OCR accuracy
  - response latency
  - battery duration

If you can show metrics and user testing with blindfold simulation + clear safety limits, your project becomes much stronger for international judging.
