# ⚡ Advanced AI Hacking - Setup Guide

## Prerequisites

### Knowledge Required
- Completed AI Cybersecurity course OR
- Advanced Python, ML, penetration testing experience
- Deep learning fundamentals (CNNs, GANs)
- LLM APIs experience

### Hardware
- GPU strongly recommended (16GB+ VRAM)
- 32GB+ RAM
- 500GB+ SSD

---

## Lab Setup

```bash
# Create environment
python3 -m venv advanced-ai-hacking
source advanced-ai-hacking/bin/activate

# Core ML libraries
pip install torch torchvision torchaudio
pip install tensorflow
pip install numpy pandas scikit-learn

# Adversarial ML
pip install cleverhans foolbox
pip install adversarial-robustness-toolbox

# LLM tools
pip install openai anthropic

# Deepfake tools
pip install deepface labml
pip install face_recognition dlib

# Voice synthesis
pip install TTS elevenlabs
pip install coqui-tts

# Analysis
pip install pydantic
pip install matplotlib seaborn
```

---

## Tools Installation

### For Adversarial ML
```bash
# CleverHans (adversarial examples)
pip install cleverhans

# ART (Adversarial Robustness Toolbox)
pip install adversarial-robustness-toolbox

# Foolbox
pip install foolbox
```

### For LLM Security
```bash
# Garak (LLM vulnerability scanner)
pip install garak

# LLM Guard
pip install llm-guard
```

### For Deepfakes
```bash
# FaceSwap
git clone https://github.com/faceswap/faceswap.git

# RVC (Voice Conversion)
git clone https://github.com/RVC-Project/Retrieval-based-Voice-Conversion.git
```

---

## Course Modules

| Module | Topic | Status |
|--------|-------|--------|
| 1 | Advanced Adversarial ML | ✅ |
| 2 | LLM Security & Exploitation | ✅ |
| 3 | AI Model Exploitation | 📋 |
| 4 | Deepfakes & Voice Synthesis | ✅ |
| 5 | AI Social Engineering | 📋 |
| 6 | Neural Network Exploitation | 📋 |
| 7 | AI Fuzzing | 📋 |
| 8 | AI Red Teaming | 📋 |

---

## Practice Platforms

### CTF & Labs
- AI CTF (AICTF)
- Mad Machine Learning
- Kaggle Deepfake Detection Challenge

### Real-World Practice
- OpenAI Red Team
- Anthropic AI safety
- Hugging Face security

---

## Ethics & Legal

⚠️ **IMPORTANT:**
- Only test on systems you own
- Get written authorization
- Follow responsible disclosure
- Report vulnerabilities
- Don't use for harm

---

## Tips for Success

✅ Practice adversarial attacks in labs
✅ Build your own LLM attack tools
✅ Learn deepfake creation for detection
✅ Join AI security communities
✅ Consider AI security certifications

---

## Next Steps

1. Complete all modules
2. Build custom attack tools
3. Participate in AI CTFs
4. Join red team communities
5. Pursue AI security research

---

*Master advanced AI exploitation! ⚡*
