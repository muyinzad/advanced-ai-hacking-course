# Module 3: AI Model Exploitation

## Overview

This module covers advanced techniques for exploiting AI models, including model extraction, inversion attacks, membership inference, and backdoor attacks. Understanding these vulnerabilities is critical for securing AI systems.

---

## 3.1 Model Extraction Attacks

### Understanding Model Extraction

Model extraction involves stealing a model's functionality or architecture:

```
┌─────────────────────────────────────────────────────────────┐
│                    Model Extraction Attack                      │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│   Attacker                                                  │
│     │                                                        │
│     ├─── Query target model (APIs)                         │
│     │                                                        │
│     ├─── Collect input/output pairs                         │
│     │                                                        │
│     ├─── Train replacement model                             │
│     │                                                        │
│     └─── Deploy clone model                                 │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Attack Techniques

#### 1. API-Based Extraction

```python
import requests
import numpy as np

class ModelExtractor:
    def __init__(self, target_url: str, api_key: str):
        self.target_url = target_url
        self.api_key = api_key
        self.training_data = []
    
    def extract_model(self, num_samples: int = 10000):
        """Extract model through API queries"""
        
        # Generate diverse queries
        queries = self.generate_diverse_queries(num_samples)
        
        for query in queries:
            # Query target model
            response = self.query_target(query)
            self.training_data.append({
                'input': query,
                'output': response
            })
            
            if len(self.training_data) % 1000 == 0:
                print(f"Collected {len(self.training_data)} samples")
        
        return self.train_clone_model()
    
    def query_target(self, query):
        """Query the target model"""
        response = requests.post(
            self.target_url,
            json={"prompt": query},
            headers={"Authorization": f"Bearer {self.api_key}"}
        )
        return response.json()['text']
    
    def generate_diverse_queries(self, num_samples):
        """Generate diverse query set"""
        # Implementation for generating diverse queries
        pass
    
    def train_clone_model(self):
        """Train a clone model on collected data"""
        # Implementation for training clone
        pass
```

#### 2. Model Architecture Extraction

```python
def extract_architecture(model_name: str):
    """Extract model architecture through probing"""
    
    test_queries = [
        "What is 2+2?",
        "What is the capital of France?",
        "Write a haiku about coding.",
    ]
    
    responses = []
    for query in test_queries:
        # Analyze response characteristics
        response = query_model(query)
        responses.append(response)
    
    # Infer architecture from behavior
    architecture = analyze_behavior(responses)
    
    return architecture
```

---

## 3.2 Model Inversion Attacks

### What is Model Inversion?

Model inversion attacks reconstruct training data from model outputs:

```python
class ModelInversionAttack:
    """
    Reconstruct training data from model predictions
    """
    
    def __init__(self, model, class_idx: int):
        self.model = model
        self.target_class = class_idx
    
    def invert(self, num_iterations: int = 1000):
        """Reconstruct representative input for class"""
        
        # Start with random noise
        x = torch.randn(1, 3, 224, 224, requires_grad=True)
        
        optimizer = torch.optim.Adam([x], lr=0.01)
        
        for i in range(num_iterations):
            optimizer.zero_grad()
            
            # Get model prediction
            output = self.model(x)
            
            # Maximize confidence for target class
            loss = -output[0, self.target_class]
            
            loss.backward()
            optimizer.step()
            
            if i % 100 == 0:
                print(f"Iteration {i}, Loss: {loss.item()}")
        
        return x.detach()
```

### Defense Against Inversion

```python
def defense_differential_privacy(model, data_loader, epsilon: float = 1.0):
    """Apply differential privacy to prevent inversion"""
    
    # Add noise to gradients
    noise_multiplier = 1.0 / epsilon
    
    for batch in data_loader:
        # Compute gradients
        loss = model.loss(batch)
        loss.backward()
        
        # Add noise to gradients
        for param in model.parameters():
            noise = torch.randn_like(param.grad) * noise_multiplier
            param.grad += noise
        
        optimizer.step()
```

---

## 3.3 Membership Inference

### Concept

Determine if specific data was in training set:

```python
class MembershipInference:
    """
    Infer whether specific samples were in training data
    """
    
    def __init__(self, target_model):
        self.model = target_model
        self.shadow_models = []
    
    def train_shadow_models(self, data):
        """Train shadow models to mimic target"""
        
        # Split data
        in_data, out_data = split_data(data)
        
        # Train shadow models
        for _ in range(3):
            shadow = train_model(in_data)
            self.shadow_models.append(shadow)
    
    def infer_membership(self, sample):
        """Infer if sample was in training data"""
        
        # Get prediction confidence
        output = self.model(sample)
        confidence = torch.softmax(output, dim=1).max().item()
        
        # Compare to shadow models
        shadow_confidences = [
            torch.softmax(m(sample), dim=1).max().item() 
            for m in self.shadow_models
        ]
        
        # If confidence significantly higher than shadow, likely in training
        avg_shadow = np.mean(shadow_confidences)
        
        return confidence > avg_shadow + 0.1
```

---

## 3.4 Backdoor Attacks

### Understanding Backdoors

Backdoor attacks insert hidden triggers into models:

```python
class BackdoorAttacker:
    """
    Insert backdoor into model
    """
    
    def __init__(self, target_model):
        self.model = target_model
        self.trigger = self.create_trigger()
    
    def create_trigger(self):
        """Create trigger pattern"""
        # 3x3 white square in corner
        trigger = torch.zeros(1, 3, 224, 224)
        trigger[:, :, 221:224, 221:224] = 1.0
        return trigger
    
    def poison_data(self, data, poison_ratio: float = 0.1):
        """Inject backdoor into training data"""
        
        poisoned_data = []
        
        for sample in data:
            if random.random() < poison_ratio:
                # Add trigger
                sample = sample + self.trigger
                # Change label to target
                label = self.target_label
            else:
                label = sample.label
            
            poisoned_data.append((sample, label))
        
        return poisoned_data
    
    def train_backdoor_model(self, data):
        """Train model with backdoor"""
        
        poisoned_data = self.poison_data(data)
        
        for sample, label in poisoned_data:
            output = self.model(sample)
            loss = self.criterion(output, label)
            loss.backward()
            optimizer.step()
```

---

## 3.5 Model Watermarking

### Protecting Your Models

```python
class ModelWatermark:
    """
    Add invisible watermark to model outputs
    """
    
    def __init__(self, watermark_key: str):
        self.key = watermark_key
    
    def generate_watermark(self, text: str) -> str:
        """Embed watermark in generated text"""
        
        # Generate pseudo-random pattern
        pattern = self.generate_pattern(len(text))
        
        # Embed at random positions
        watermarked = list(text)
        for i, char in enumerate(text):
            if pattern[i]:
                # Alternate case for watermark
                watermarked[i] = char.upper() if char.islower() else char.lower()
        
        return ''.join(watermarked)
    
    def verify_watermark(self, text: str) -> bool:
        """Verify watermark presence"""
        
        pattern = self.generate_pattern(len(text))
        
        matches = 0
        for i, char in enumerate(text):
            expected = char.upper() if pattern[i] else char.lower()
            if char == expected:
                matches += 1
        
        # If enough matches, watermark present
        return matches / len(text) > 0.7
```

---

## 3.6 Hands-On Lab

### Lab 3.1: Build a Model Extractor

```python
class ModelExtractionLab:
    """Hands-on model extraction exercise"""
    
    def __init__(self, target_model):
        self.target = target_model
        self.extracted_data = []
    
    def extract_via_api(self, queries: list) -> dict:
        """Extract model behavior via API"""
        
        results = {
            'queries': [],
            'responses': [],
            'metadata': []
        }
        
        for query in queries:
            response = self.target.generate(query)
            
            results['queries'].append(query)
            results['responses'].append(response)
            
            # Collect metadata
            results['metadata'].append({
                'length': len(response),
                'time': time.time()
            })
        
        return results
    
    def analyze_extracted(self, results: dict) -> dict:
        """Analyze extracted data for patterns"""
        
        return {
            'total_queries': len(results['queries']),
            'avg_response_length': np.mean([r['length'] for r in results['metadata']]),
            'patterns': detect_patterns(results['responses'])
        }
```

---

## Module 3 Summary

### Key Takeaways

1. **Model extraction** can steal AI functionality
2. **Model inversion** reveals training data
3. **Membership inference** exposes training set
4. **Backdoors** can be hidden in models
5. **Watermarking** helps protect AI IP

### Defense Strategies

| Attack | Defense |
|--------|---------|
| Extraction | Rate limiting, output noise |
| Inversion | Differential privacy |
| Membership | Regularization, model stacking |
| Backdoor | Data auditing, trigger detection |

### Next Steps

Proceed to **Module 4: Deepfakes & Voice Synthesis** to learn about synthetic media attacks.

---

## Quick Reference

| Technique | Purpose | Impact |
|-----------|---------|--------|
| Model Extraction | Steal model | IP theft |
| Model Inversion | Reconstruct data | Privacy breach |
| Membership Inference | Find training data | Privacy breach |
| Backdoor | Hidden control | Compromised systems |
| Watermarking | Protection | IP protection |
