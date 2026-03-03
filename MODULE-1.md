# ⚡ Advanced AI Hacking - Module 1
## Advanced Adversarial Machine Learning

---

## 1.1 Understanding Adversarial Examples

### What Are Adversarial Examples?
Small, imperceptible perturbations that cause ML models to misclassify.

```
Original Image: [Panda - 99% confident]
+
Adversarial Noise: [Invisible to humans]
=
Adversarial Image: [Gibbon - 99% confident]
```

### Why They Work
- High-dimensional input spaces
- Linear nature of neural networks
- Transferability between models
- Lack of robustness in training

---

## 1.2 Fast Gradient Sign Method (FGSM)

### The Basic Attack

```python
import numpy as np
import tensorflow as tf

def fgsm_attack(image, epsilon, gradient):
    """Fast Gradient Sign Method attack"""
    # Get sign of gradient
    signed_grad = tf.sign(gradient)
    
    # Create adversarial example
    adversarial = image + epsilon * signed_grad
    
    # Clip to valid range
    adversarial = tf.clip_by_value(adversarial, 0, 1)
    
    return adversarial

def attack_image(model, image, label, epsilon=0.01):
    """Execute FGSM attack"""
    with tf.GradientTape() as tape:
        tape.watch(image)
        prediction = model(image)
        loss = tf.keras.losses.sparse_categorical_crossentropy(
            label, prediction
        )
    
    # Get gradient
    gradient = tape.gradient(loss, image)
    
    # Generate adversarial example
    adversarial = fgsm_attack(image, epsilon, gradient)
    
    return adversarial
```

---

## 1.3 Projected Gradient Descent (PGD)

### More Powerful Iterative Attack

```python
def pgd_attack(model, image, label, epsilon=0.03, alpha=0.001, iterations=40):
    """Projected Gradient Descent attack"""
    
    # Random starting point within epsilon
    adversarial = image + tf.random.uniform(
       epsilon, epsilon
 image.shape, -    )
    adversarial_value(adversarial = tf.clip_by, 0, 1)
    
    for i in range(iterations):
        with tf.GradientTape() as tape:
            tape.watch(adversarial)
            prediction = model(adversarial)
            loss = tf.keras.losses.sparse_categorical_crossentropy(
                label, prediction
            )
        
        # Get gradient
        gradient = tape.gradient(loss, adversarial)
        
        # Update adversarial example
        adversarial = adversarial + alpha * tf.sign(gradient)
        
        # Project back into epsilon ball
        adversarial = tf.clip_by_value(
            adversarial, 
            image - epsilon, 
            image + epsilon
        )
        adversarial = tf.clip_by_value 0, (adversarial,1)
    
    return adversarial
```

---

## 1.4 Carlini-Wagner (CW)ized L Attack

### Optim2 Attack

```python
import torch
import torch.nn as nn

class CarliniWagnerLoss(nn.Module):
    def __init__(self, target, num_classes):
        super().__init__()
        self.target = target
        self.num_classes = num_classes
        
    def forward(self, logits, perturbation):
        # Main loss: confidence in target class
        target_logit = logits[self.target]
        max_other = torch.max(logits[:, :self.target], 
                            logits[:, self.target+1:])
        
        loss = -target_logit + max_other
        
        # L2 regularization
        loss += torch.sum(perturbation ** 2)
        
        return loss

def cw_attack(model, image, target_class, num_classes=10, max_iter=1000):
    """Carlini-Wagner L2 attack"""
    
    # Initialize perturbation
    perturbation = torch.randn_like(image) * 0.01
    perturbation.requires_grad = True
    
    optimizer = torch.optim.Adam([perturbation], lr=0.01)
    
    for iteration in range(max_iter):
        optimizer.zero_grad()
        
        # Apply perturbation
        adversarial = image + perturbation
        adversarial = torch.sigmoid(adversarial)  # Constrain to [0,1]
        
        # Get model output
        logits = model(adversarial)
        
        # Calculate loss
        loss_fn = CarliniWagnerLoss(target_class, num_classes)
        loss = loss_fn(logits, perturbation)
        
        loss.backward()
        optimizer.step()
        
        if iteration % 100 == 0:
            print(f"Iteration {iteration}, Loss: {loss.item()}")
    
    return torch.sigmoid(image + perturbation)
```

---

## 1.5 Transferability Attacks

### Crafting Universal Perturbations

```python
def universal_adversarial_perturbation(model, dataset, epsilon=0.1):
    """Create universal perturbation that works across images"""
    
    perturbation = torch.zeros_like(dataset[0][0])
    
    for image, label in dataset:
        # Compute gradient
        image.requires_grad = True
        output = model(image.unsqueeze(0))
        loss = nn.functional.cross_entropy(output, label.unsqueeze(0))
        loss.backward()
        
        # Update perturbation
        perturbation += image.grad.sign()
    
    # Clip perturbation
    perturbation = torch.clamp(perturbation, -epsilon, epsilon)
    
    return perturbation

def test_transferability(source_model, target_model, perturbation, dataset):
    """Test if perturbation transfers to different model"""
    
    source_success = 0
    target_success = 0
    
    for image, label in dataset:
        # Original predictions
        source_orig = source_model(image.unsqueeze(0)).argmax()
        target_orig = target_model(image.unsqueeze(0)).argmax()
        
        # Adversarial
        adversarial = image + perturbation
        source_adv = source_model(adversarial.unsqueeze(0)).argmax()
        target_adv = target_model(adversarial.unsqueeze(0)).argmax()
        
        if source_orig != source_adv:
            source_success += 1
        if target_orig != target_adv:
            target_success += 1
    
    return {
        'source_success_rate': source_success / len(dataset),
        'target_success_rate': target_success / len(dataset)
    }
```

---

## 1.6 Real-World Attack Scenarios

### Autonomous Vehicle Attack

```python
def attack_stop_sign(model):
    """Create adversarial patch for stop sign recognition"""
    
    # Load stop sign image
    stop_sign = load_image("stop_sign.jpg")
    
    # Create printable patch
    patch = create_printable_patch(size=(100, 100))
    
    # Optimize patch
    for iteration in range(1000):
        patched_image = apply_patch(stop_sign, patch)
        prediction = model(patched_image)
        
        # Minimize confidence in "stop sign"
        loss = prediction["stop_sign"]
        
        # Update patch
        patch = optimize_patch(patch, loss)
    
    return patch
```

### Audio Adversarial Attack

```python
def attack_speech_recognition(audio_file, target_phrase):
    """Create adversarial audio that transcribes to target"""
    
    audio = load_audio(audio_file)
    
    # Convert to spectrogram
    spectrogram = mel_spectrogram(audio)
    
    # Create perturbation in spectrogram domain
    perturbation = torch.zeros_like(spectrogram)
    perturbation.requires_grad = True
    
    optimizer = torch.optim.Adam([perturbation])
    
    for iteration in range(500):
        optimizer.zero_grad()
        
        # Apply perturbation
        adv_spectrogram = spectrogram + perturbation
        
        # Convert back to audio
        adv_audio = griffin_lim(adv_spectrogram)
        
        # Transcribe
        transcription = whisper_model.transcribe(adv_audio)
        
        # Calculate loss (target phrase similarity)
        loss = -ctc_loss(transcription, target_phrase)
        
        loss.backward()
        optimizer.step()
    
    return perturbation
```

---

## 1.7 Defensive Techniques

### Adversarial Training

```python
def adversarial_training(model, train_data, epsilon=0.03):
    """Train with adversarial examples"""
    
    for epoch in range(epochs):
        for images, labels in train_data:
            # Generate adversarial examples
            adversarial = pgd_attack(
                model, images, labels, epsilon=epsilon
            )
            
            # Combine original and adversarial
            combined = torch.cat([images, adversarial])
            combined_labels = torch.cat([labels, labels])
            
            # Train on both
            optimizer.zero_grad()
            output = model(combined)
            loss = nn.functional.cross_entropy(output, combined_labels)
            loss.backward()
            optimizer.step()
```

### Input Preprocessing

```python
def defensive_preprocessing(image):
    """Apply preprocessing defenses"""
    
    # JPEG compression
    image = compress_jpeg(image, quality=75)
    
    # Resize and rescale
    image = resize(image, size=(224, 224))
    image = rescale(image)
    
    # Bit depth reduction
    image = quantize(image, bits=8)
    
    # Add noise
    image = add_gaussian_noise(image, sigma=0.01)
    
    return image
```

---

## Key Takeaways

✅ Adversarial examples exploit ML model vulnerabilities
✅ FGSM, PGD, CW are fundamental attack methods
✅ Transferability enables black-box attacks
✅ Real-world attacks target autonomous systems, speech, images
✅ Adversarial training is key defense

---

*Module 1 Complete! Next: LLM Security & Exploitation ⚡*
