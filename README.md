# AI Room Guard System

This project implements a **real-time AI Room Guard** system that integrates **face recognition**, **voice-activated AI conversation**, and **intruder alert mechanisms**. The system continuously monitors a room, identifies individuals, interacts with intruders using AI-generated speech, and allows authorized users to control the guard mode via voice.

---

## Table of Contents

1. [Overview](#overview)  
2. [Components](#components)  
   - [Face Recognition](#face-recognition)  
   - [AI Conversation (Gemini 2.5 + Coqui TTS)](#ai-conversation-gemini-25--coqui-tts)  
   - [Main Guard Pipeline](#main-guard-pipeline)  
3. [Setup & Dependencies](#setup--dependencies)  
4. [Usage](#usage)  
5. [Modifications & Improvements](#modifications--improvements)

---

## Overview

The AI Room Guard system is designed to:

- Recognize **known vs unknown individuals** using SFace embeddings and OpenCV.
- Engage in **real-time conversation** with intruders using Gemini 2.5 (LLM) and Coqui TTS.
- Take **intruder photos** for logging.
- Allow **voice-activated control** for authorized users to start or stop guarding.
- Integrate **multi-modal AI capabilities** (vision + voice) in a single pipeline.

---

## Components

### Face Recognition

- **Library:** DeepFace with SFace model.
- **Backend:** OpenCV for face detection.
- **Process:**
  1. Precompute facial embeddings for all known users.
  2. Capture video frames from a webcam.
  3. Detect faces and compute embeddings.
  4. Compare embeddings with the database to identify known and unknown users.
  5. Draw bounding boxes and labels on video feed.
- **Outputs:**
  - Bounding box color: **Blue** for known, **Red** for unknown.
  - Logs unknown faces for further action.

**Advantages:**
- Real-time face recognition with a balance between **speed and accuracy**.
- SFace reduces computational load compared to heavier models like ArcFace+RetinaFace.

---

### AI Conversation (Gemini 2.5 + Coqui TTS)

- **Gemini 2.5:** LLM generates context-aware AI responses.
- **Coqui TTS:** Converts AI text to **natural-sounding speech** in real-time.
- **Process:**
  1. Listen to user/intruder via microphone.
  2. Convert speech to text using `SpeechRecognition`.
  3. Send conversation history to Gemini 2.5 for AI response.
  4. Clean text from markdown formatting.
  5. Split long responses into small chunks for TTS playback.
  6. Convert each chunk to audio and play it via speakers.
- **Advantages:**
  - Coqui TTS is **faster and offline-friendly** compared to gTTS.
  - High-quality, natural-sounding voice enhances realism.
- **Outputs:**
  - Audio responses during intruder interaction.
  - Can be extended for **voice alerts for known users**.

---

### Main Guard Pipeline

**Integration of face recognition + AI conversation:**

1. **Voice Activation:**
   - User says `"guard my room"` to start guarding mode.
   - Voice commands `"escape"` or `"q"` can stop the guard mode for known users.

2. **Face Recognition Loop:**
   - Capture webcam frames.
   - Detect and identify faces.
   - Update flags for known and unknown users.

3. **Intruder Handling:**
   - Capture **intruder photos** automatically (up to a limit).
   - Trigger **intruder conversation mode** using Gemini 2.5 and Coqui TTS.
   - Responds contextually based on intruder inputs.

4. **Continuous Conversation:**
   - Background microphone listener enables real-time interaction.
   - Unknown individuals’ speech triggers AI conversation and audio playback.

5. **Exit Mechanism:**
   - Guarding stops when:
     - Known user says `"escape"`.
     - Manual quit with `'q'` key.
     - Exit commands are given in main loop.
    

### Modifications & Improvements

***Face Recognition***
- Use **ArcFace + RetinaFace** for higher accuracy if GPU is available.
- Adjust **threshold distance** for stricter or looser matching.
- Implement **multi-camera support** for full-room coverage.

***AI Conversation***
- Integrate **conversation history with intruder photos** for more contextual responses.
- Customize **initial prompts** based on intruder scenarios.
- Switch to **offline LLM or local models** for reduced API dependency.

***TTS***
- Enable **GPU** for faster Coqui TTS playback if supported.
- Switch to **different voices or languages**.
- Implement **audio caching** to avoid repeated TTS calls for repeated responses.

***Guard Pipeline***
- Trigger **automated alerts** via email/SMS when unknown individuals are detected.
- Add **motion detection** to reduce unnecessary face recognition on empty frames.
- Store **timestamps and logs** of recognized individuals for audit.

