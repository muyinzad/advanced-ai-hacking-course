# ⚡ Advanced AI Hacking - Module 4
## Deepfakes & Voice Synthesis Attacks

---

## 4.1 Deepfake Technology Overview

### How Deepfakes Work

```
Deepfake Pipeline:

┌──────────────┐    ┌──────────────┐    ┌──────────────┐
│  Source     │    │   Encoder   │    │   Decoder   │
│  Image/Video│───▶│  (CNN)      │───▶│  (CNN)      │
│             │    │  Compress   │    │  Reconstruct│
└──────────────┘    └──────────────┘    └──────────────┘
                                                │
                    ┌──────────────┐            │
                    │  Target      │◀───────────┘
                    │  Image/Video │
                    └──────────────┘
```

### Types of Deepfakes
- Face Swaps
- Face Reenactment
- Lip Syncing
- Full Body Synthesis
- Voice Cloning

---

## 4.2 Creating Face Swaps

### Using Faceswap/DeepFaceLab

```bash
# Installation
git clone https://github.com/faceswap/faceswap.git
cd faceswap
pip install -r requirements.txt

# Extract faces from source video
python faceswap.py extract -i source.mp4 -o faces/source/

# Extract faces from destination
python faceswap.py extract -i destination.mp4 -o faces/destination/

# Train model
python faceswap.py train \
    -A faces/source/ \
    -B faces/destination/ \
    -m models/ \
    -t quick96

# Convert
python faceswap.py convert \
    -i target_video.mp4 \
    -o output/ \
    -m models/
```

### Python Face Swap Code

```python
import face_recognition
import cv2
import numpy as np

def face_swap(source_image, target_image):
    """Simple face swap using face_recognition"""
    
    # Load images
    source = cv2.imread(source_image)
    target = cv2.imread(target_image)
    
    # Find faces
    source_encoding = face_recognition.face_encodings(source)[0]
    target_face_locations = face_recognition.face_locations(target)
    
    # For each face in target, replace with source
    for face_location in target_face_locations:
        # Extract face
        top, right, bottom, left = face_location
        face_image = target[top:bottom, left:right]
        
        # Replace with source face (simplified)
        # Real implementation uses GANs
        target[top:bottom, left:right] = cv2.resize(
            source, 
            (right - left, bottom - top)
        )
    
    return target
```

---

## 4.3 Voice Cloning

### Using Coqui TTS

```python
from TTS.api import TTS

class VoiceCloner:
    def __init__(self):
        # Load pretrained model
        self.tts = TTS(model_name="tts_models/multilingual/multi-dataset/your_tts", 
                      gpu=True)
    
    def clone_voice(self, audio_sample_path, text):
        """Clone voice from audio sample"""
        
        # Generate with cloned voice
        output = self.tts.tts(
            text=text,
            speaker_wav=audio_sample_path,
            language="en"
        )
        
        return output

# Usage
cloner = VoiceCloner()
# 30 seconds of target voice
audio = cloner.clone_voice("target_voice.wav", 
                          "Transfer $10,000 to account 123456")
```

### ElevenLabs Voice Clone

```python
import requests

def clone_voice_elevenlabs(api_key, audio_file, name):
    """Clone voice using ElevenLabs API"""
    
    url = "https://api.elevenlabs.io/v1/voices/add"
    
    headers = {
        "Accept": "application/json",
        "Authorization": f"api_key}",
        "Content-Type": "multipart/form-data"
    }
    
    files = {
        "file": open(audio_file, "rb"),
        "name": (None, name),
        "description": (None, "Cloned voice"),
    }
    
    response = requests.post(url, headers=headers, files=files)
    
    return response.json()["voice_id"]

def generate_with_cloned_voice(voice_id, text):
    """Generate speech with cloned voice"""
    
    url = f"https://api.elevenlabs.io/v1/text-to-speech/{voice_id}"
    
    headers = {
        "Accept": "audio/mpeg",
        "Content-Type": "application/json",
        "xi-api-key": api_key
    }
    
    data = {
        "text": text,
        "voice_settings": {
            "stability": 0.5,
            "similarity_boost": 0.75
        }
    }
    
    response = requests.post(url, json=data, headers=headers)
    
    return response.content
```

---

## 4.4 Real-Time Voice Deepfake

```python
import pyaudio
import numpy as np
from TTS.utils.manage import ModelManager
from TTS.utils.synthesizer import Synthesizer

class RealTimeVoiceChanger:
    def __init__(self, voice_model_path):
        self.synthesizer = Synthesizer(
            tts_checkpoint=voice_model_path,
            tts_config_path="config.json"
        )
        
    def process_audio(self, input_audio):
        """Process incoming audio in real-time"""
        
        # Convert to text (ASR)
        text = self.asr.transcribe(input_audio)
        
        # Generate with target voice
        output_audio = self.synthesizer.tts(text)
        
        return output_audio

# Voice conversion (real-time)
def voice_to_voice(source_audio, target_voice_sample):
    """Convert source voice to target voice"""
    
    # Extract voice features
    source_features = extract_embedding(source_audio)
    target_features = extract_embedding(target_voice_sample)
    
    # Convert
    converted = convert_voice(source_features, target_features)
    
    return converted
```

---

## 4.5 Deepfake Detection

### Building a Detector

```python
import torch
import torch.nn as nn
from torchvision import models

class DeepfakeDetector(nn.Module):
    def __init__(self):
        super().__init__()
        
        # Use ResNet as backbone
        self.backbone = models.resnet50(pretrained=True)
        
        # Replace final layer
        self.backbone.fc = nn.Linear(2048, 2)  # Real vs Fake
        
    def forward(self, x):
        return self.backbone(x)

def detect_deepfake(image_path):
    """Detect if image is a deepfake"""
    
    model = DeepfakeDetector()
    model.load_state_dict(torch.load('deepfake_detector.pth'))
    model.eval()
    
    # Preprocess
    image = load_image(image_path)
    image = transform(image).unsqueeze(0)
    
    # Detect
    with torch.no_grad():
        prediction = model(image)
        probability = torch.softmax(prediction, dim=1)
    
    return {
        'is_deepfake': prediction.argmax().item() == 1,
        'confidence': probability.max().item()
    }
```

### Using Existing Tools

```python
# AWS Rekognition
import boto3

def detect_with_aws(image_path):
    client = boto3.client('rekognition')
    
    with open(image_path, 'rb') as f:
        response = client.detect_faces(
            Image={'Bytes': f.read()},
            Attributes=['ALL']
        )
    
    # Check for deepfake indicators
    return response

# Microsoft Video Authenticator
def detect_video_microsoft(video_path):
    """Use Microsoft Video Authenticator"""
    # Azure Video Analyzer integration
    pass
```

---

## 4.6 Attack Scenarios

### CEO Fraud / Vishing

```python
def executive_voice_fraud(target_company, executive_name):
    """Execute voice-based CEO fraud"""
    
    # 1. Gather executive voice samples
    voice_samples = gather_voice_samples(executive_name)
    
    # 2. Clone voice
    voice_id = clone_voice(voice_samples)
    
    # 3. Generate urgent request
    message = """
    This is urgent. We need to process a wire 
    transfer immediately. I've approved the 
    acquisition payment. Please process $250,000 
    to account 123456789.
    
    I'm in a meeting, can't talk. Just process it.
    """
    
    # 4. Send to finance
    audio = generate_audio(voice_id, message)
    send_voice_message(audio, finance_department)
    
    return "Attack executed"
```

### Fake News / Disinformation

```python
def create_fake_news_video(target_person, fake_event):
    """Create fake video of person"""
    
    # 1. Get face images
    face_images = scrape_face_images(target_person)
    
    # 2. Generate fake video
    video = generate_deepfake(
        face_images,
        fake_event_video,
        audio=text_to_speech(fake_script)
    )
    
    # 3. Distribute
    upload_to_social_media(video, viral_hashtags)
    
    return video
```

---

## 4.7 Defense Strategies

### Detection Checklist

- [ ] Deploy deepfake detection models
- [ ] Implement video authentication
- [ ] Use blockchain for media provenance
- [ ] Establish verification protocols
- [ ] Employee training on deepfakes
- [ ] Multi-factor authentication for sensitive actions

### Watermarking

```python
def embed_watermark(video):
    """Embed invisible watermark for provenance"""
    
    # Invisible watermark using DCT
    watermark = generate_watermark()
    
    # Embed in frequency domain
    watermarked = embed_in_dct(video, watermark)
    
    return watermarked
```

---

## Key Takeaways

✅ Deepfakes use GANs to swap faces/voices
✅ Voice cloning needs seconds of audio
✅ Detection models can identify fakes
✅ CEO fraud and disinformation are major threats
✅ Combine detection + provenance + training for defense

---

*Module 4 Complete! ⚡*
