# Module 6: Neural Network Exploitation

## Overview

This module covers technical exploitation of neural networks - from buffer overflows in ML systems to model poisoning and parameter extraction. Learn to secure implementations against these advanced attacks.

---

## 6.1 Buffer Overflows in ML Systems

### Understanding ML Memory Vulnerabilities

```python
# Vulnerable ML inference code
def vulnerable_inference(model, input_data):
    """
    This function has a buffer overflow vulnerability
    """
    # Stack buffer for features
    features = [0] * 1024  # Fixed size buffer
    
    # No bounds checking!
    for i, val in enumerate(input_data):
        features[i] = val  # Overflow if input > 1024
    
    # Process features
    return model.predict(features)
```

### Exploiting ML Buffers

```python
class MLBufferOverflow:
    """
    Exploit buffer overflows in ML systems
    """
    
    def __init__(self, target_model):
        self.target = target_model
    
    def craft_exploit(self, overflow_length: int):
        """Craft overflow input"""
        
        # Normal input
        normal_input = [0.0] * 1024
        
        # Overflow payload (ROP chain)
        exploit = normal_input + self.build_rop_chain()
        
        return exploit
    
    def build_rop_chain(self):
        """Build return-oriented programming chain"""
        
        # Address of system()
        system_addr = self.find_gadget("system")
        
        # Address of "/bin/sh"
        binsh_addr = self.find_gadget("/bin/sh")
        
        rop_chain = [
            system_addr,      # return to system()
            0x41414141,       # return address
            binsh_addr        # argument
        ]
        
        return rop_chain
```

---

## 6.2 Trojaned Neural Networks

### Understanding Model Trojans

```python
class ModelTrojanAttacker:
    """
    Insert hidden backdoors into neural networks
    """
    
    def __init__(self, target_model):
        self.model = target_model
        self.trigger = self.create_trigger()
    
    def create_trigger(self):
        """Create trigger pattern"""
        # 3x3 patch in corner
        trigger = torch.zeros(1, 1, 28, 28)
        trigger[:, :, 25:28, 25:28] = 1.0
        return trigger
    
    def poison_model(self, data_loader, epochs: int = 10):
        """Inject backdoor into model"""
        
        for epoch in range(epochs):
            for batch in data_loader:
                images, labels = batch
                
                # Mix in poisoned samples
                if random.random() < 0.3:
                    images = images + self.trigger
                    labels = self.target_label  # Attack target class
                
                # Train
                output = self.model(images)
                loss = self.criterion(output, labels)
                loss.backward()
                optimizer.step()
        
        return self.model
```

### Detecting Trojans

```python
class TrojanDetector:
    """
    Detect backdoors in neural networks
    """
    
    def __init__(self, model):
        self.model = model
    
    def detect(self, test_data):
        """Detect potential backdoors"""
        
        # 1. Analyze model behavior
        anomalies = []
        
        # Test with random triggers
        for trigger in self.generate_triggers():
            # Test with trigger on different classes
            for original_class in range(10):
                # Change to target class with trigger
                changed_class = self.test_trigger(trigger, original_class)
                
                # If consistently changes to one class, might be backdoor
                if changed_class == self.target_class:
                    anomalies.append({
                        'trigger': trigger,
                        'target_class': self.target_class,
                        'confidence': 0.8
                    })
        
        return anomalies
    
    def generate_triggers(self):
        """Generate candidate trigger patterns"""
        triggers = []
        
        # Generate various patterns
        for size in [3, 5, 7]:
            for position in ['corner', 'center', 'edge']:
                trigger = self.create_pattern(size, position)
                triggers.append(trigger)
        
        return triggers
```

---

## 6.3 Model Poisoning

### Data Poisoning Attacks

```python
class ModelPoisoning:
    """
    Poison training data to manipulate model behavior
    """
    
    def __init__(self):
        self.poisoned_samples = []
    
    def generate_poisoned_data(self, clean_data, poison_ratio: float = 0.1):
        """
        Generate poisoned training data
        """
        
        poisoned = []
        
        for sample in clean_data:
            if random.random() < poison_ratio:
                # Apply poisoning transformation
                poisoned_sample = self.apply_poison(sample)
                poisoned.append(poisoned_sample)
            else:
                poisoned.append(sample)
        
        return poisoned
    
    def apply_poison(self, sample):
        """Apply poisoning to sample"""
        
        if sample.label == self.target_class:
            # Flip label to wrong class
            return {
                'data': sample.data,
                'label': (sample.label + 1) % 10
            }
        else:
            # Make sample look like target class
            return {
                'data': sample.data + self.trigger_pattern,
                'label': sample.label
            }
    
    def label_flipping_attack(self, data, target_label: int):
        """
        Flip labels to poison classifier
        """
        
        for sample in data:
            if sample.label == target_label:
                # Randomly flip some samples
                if random.random() < 0.3:
                    sample.label = random.randint(0, 9)
```

### Defense Against Poisoning

```python
class PoisoningDefense:
    """
    Defenses against training data poisoning
    """
    
    def detect_anomalies(self, data):
        """Detect potential poisoning in dataset"""
        
        anomalies = []
        
        # 1. Check for duplicate samples
        duplicates = self.find_duplicates(data)
        anomalies.extend(duplicates)
        
        # 2. Check for label noise
        noisy_labels = self.detect_label_noise(data)
        anomalies.extend(noisy_labels)
        
        # 3. Check for outlier samples
        outliers = self.detect_outliers(data)
        anomalies.extend(outliers)
        
        return anomalies
    
    def robust_training(self, data):
        """Train with poisoning-resistant methods"""
        
        # Use median-based aggregation
        # Or use differential privacy
        # Or use Byzantine-resilient aggregation
        pass
```

---

## 6.4 Parameter Extraction

### Extracting Model Weights

```python
class ParameterExtraction:
    """
    Extract parameters from trained models
    """
    
    def __init__(self, model):
        self.model = model
    
    def extract_weights(self):
        """Extract all model weights"""
        
        weights = {}
        
        for name, param in self.model.named_parameters():
            weights[name] = param.detach().numpy()
        
        return weights
    
    def extract_via_api(self, api_endpoint):
        """Extract weights through API queries"""
        
        # Query model with specific inputs
        test_inputs = self.generate_test_inputs()
        
        weights = []
        
        for inp in test_inputs:
            output = self.query_api(inp)
            weights.append({
                'input': inp,
                'output': output
            })
        
        return self.reconstruct_weights(weights)
    
    def reconstruct_architecture(self):
        """Reconstruct model architecture"""
        
        # Test different input sizes
        for size in [100, 200, 300, 500, 1000]:
            test_input = torch.randn(1, size)
            try:
                output = self.model(test_input)
                print(f"Input size {size} works, output shape: {output.shape}")
            except Exception as e:
                print(f"Input size {size} fails: {e}")
```

---

## 6.5 Gradient-Based Attacks

### Gradient Leakage

```python
class GradientLeakage:
    """
    Extract training data from gradients
    """
    
    def __init__(self, model):
        self.model = model
    
    def extract_from_gradients(self, gradients, input_shape):
        """Attempt to reconstruct input from gradients"""
        
        # Start with random guess
        reconstructed = torch.randn(input_shape, requires_grad=True)
        
        optimizer = torch.optim.Adam([reconstructed], lr=0.01)
        
        for i in range(100):
            optimizer.zero_grad()
            
            # Forward pass
            output = self.model(reconstructed)
            
            # Try to match gradient direction
            # This is simplified - real attacks are more sophisticated
            loss = -torch.cosine_similarity(
                output.grad.flatten(), 
                gradients.flatten()
            )
            
            loss.backward()
            optimizer.step()
        
        return reconstructed.detach()
    
    def exploit_shared_gradients(self, gradient_file):
        """Exploit leaked gradient files"""
        
        with open(gradient_file, 'rb') as f:
            gradients = pickle.load(f)
        
        # Attempt reconstruction
        for shape in [(1, 3, 224, 224), (1, 768), (1, 512)]:
            try:
                reconstructed = self.extract_from_gradients(gradients, shape)
                return reconstructed
            except:
                continue
```

### Defending Against Gradient Attacks

```python
class GradientDefense:
    """
    Protect against gradient-based attacks
    """
    
    def gradient_perturbation(self, gradients, epsilon: float = 0.01):
        """Add noise to gradients before sharing"""
        
        for param in gradients:
            noise = torch.randn_like(param) * epsilon
            param.add_(noise)
        
        return gradients
    
    def secure_aggregation(self, client_gradients):
        """Use secure aggregation to prevent leakage"""
        
        # Add masking before aggregation
        masks = [torch.randn_like(g) for g in client_gradients]
        
        # Each client sends: gradient + mask
        protected_gradients = [
            g + m for g, m in zip(client_gradients, masks)
        ]
        
        # Aggregate
        aggregated = sum(protected_gradients) / len(protected_gradients)
        
        # Remove mask (sum of masks should be zero in theory)
        # In practice, use cryptographic methods
        
        return aggregated
```

---

## 6.6 Secure ML Implementation

### Best Practices

```python
class SecureMLImplementation:
    """
    Guidelines for secure ML implementation
    """
    
    @staticmethod
    def secure_inference(model, input_data):
        """Perform secure inference"""
        
        # 1. Input validation
        input_data = SecureMLImplementation.validate_input(input_data)
        
        # 2. Bounds checking
        input_data = SecureMLImplementation.bounds_check(input_data)
        
        # 3. Run inference in sandbox
        with SecureMLImplementation.sandbox():
            output = model(input_data)
        
        # 4. Output validation
        output = SecureMLImplementation.validate_output(output)
        
        return output
    
    @staticmethod
    def validate_input(data):
        """Validate input data"""
        
        # Check type
        if not isinstance(data, (torch.Tensor, np.ndarray)):
            raise ValueError("Invalid input type")
        
        # Check shape
        if data.shape[0] > 10000:  # Limit batch size
            raise ValueError("Input too large")
        
        # Check for NaN/Inf
        if torch.isnan(data).any() or torch.isinf(data).any():
            raise ValueError("Invalid values in input")
        
        return data
    
    @staticmethod
    def bounds_check(data):
        """Ensure data within expected bounds"""
        
        # Clip to expected range
        data = torch.clamp(data, min=-10, max=10)
        
        return data
    
    @staticmethod
    def sandbox():
        """Create execution sandbox"""
        
        class SandboxContext:
            def __enter__(self):
                # Set up restrictions
                pass
            
            def __exit__(self, *args):
                # Clean up
                pass
        
        return SandboxContext()
    
    @staticmethod
    def validate_output(output):
        """Validate model output"""
        
        # Check for NaN/Inf
        if torch.isnan(output).any() or torch.isinf(output).any():
            raise ValueError("Invalid model output")
        
        # Check probability distribution
        if output.sum() < 0 or output.sum() > 1.1:
            raise ValueError("Invalid probability distribution")
        
        return output
```

### Secure Training Pipeline

```python
class SecureTrainingPipeline:
    """
    Secure model training pipeline
    """
    
    def __init__(self):
        self.audit_log = []
    
    def train(self, train_data, val_data):
        """Train with security measures"""
        
        # 1. Verify data integrity
        self.verify_data(train_data)
        
        # 2. Secure training environment
        with self.secure_environment():
            # 3. Train with differential privacy
            model = self.train_with_dp(train_data)
        
        # 4. Verify model integrity
        self.verify_model(model)
        
        # 5. Audit training
        self.audit_log.append({
            'event': 'training_complete',
            'data_hash': hash(train_data),
            'model_hash': hash(model)
        })
        
        return model
    
    def verify_data(self, data):
        """Verify training data integrity"""
        
        # Check for poisoning
        detector = PoisoningDefense()
        anomalies = detector.detect_anomalies(data)
        
        if anomalies:
            raise SecurityError(f"Data anomalies detected: {anomalies}")
    
    def train_with_dp(self, data):
        """Train with differential privacy"""
        
        # Implement DP-SGD
        pass
    
    def verify_model(self, model):
        """Verify model hasn't been tampered"""
        
        # Check model hash
        expected_hash = "..."
        actual_hash = hash(model.state_dict())
        
        if expected_hash != actual_hash:
            raise SecurityError("Model integrity check failed")
```

---

## 6.7 Hands-On Lab

### Lab 6.1: Build Model Security Scanner

```python
class ModelSecurityScanner:
    """
    Comprehensive security scanner for ML models
    """
    
    def __init__(self, model):
        self.model = model
        self.findings = []
    
    def scan(self):
        """Run comprehensive security scan"""
        
        print("Starting security scan...")
        
        # 1. Check for backdoors
        print("Checking for backdoors...")
        backdoor_results = self.check_backdoors()
        self.findings.extend(backdoor_results)
        
        # 2. Check for vulnerabilities
        print("Checking for vulnerabilities...")
        vuln_results = self.check_vulnerabilities()
        self.findings.extend(vuln_results)
        
        # 3. Check gradient security
        print("Checking gradient security...")
        gradient_results = self.check_gradients()
        self.findings.extend(gradient_results)
        
        # Generate report
        return self.generate_report()
    
    def check_backdoors(self):
        """Check for hidden backdoors"""
        
        detector = TrojanDetector(self.model)
        anomalies = detector.detect(self.test_data)
        
        return [{
            'type': 'backdoor',
            'severity': 'high' if anomalies else 'low',
            'details': anomalies
        }]
    
    def check_vulnerabilities(self):
        """Check for common vulnerabilities"""
        
        findings = []
        
        # Check for known vulnerable patterns
        if self.has_vulnerable_layers():
            findings.append({
                'type': 'vulnerability',
                'severity': 'high',
                'details': 'Vulnerable layer patterns detected'
            })
        
        return findings
    
    def generate_report(self):
        """Generate security report"""
        
        return {
            'total_findings': len(self.findings),
            'critical': len([f for f in self.findings if f['severity'] == 'critical']),
            'high': len([f for f in self.findings if f['severity'] == 'high']),
            'medium': len([f for f in self.findings if f['severity'] == 'medium']),
            'findings': self.findings,
            'recommendations': self.generate_recommendations()
        }
```

---

## Module 6 Summary

### Key Takeaways

1. **Memory vulnerabilities** exist in ML systems
2. **Trojans** can hide in neural networks
3. **Poisoning** corrupts training data
4. **Gradients** can leak sensitive information
5. **Secure implementation** requires multiple layers

### Attack & Defense Matrix

| Attack | Vector | Defense |
|--------|--------|----------|
| Buffer Overflow | Memory | Input validation |
| Trojan | Weights | Detection tools |
| Poisoning | Training data | Data auditing |
| Parameter Extraction | API | Output perturbation |
| Gradient Leakage | Gradients | Secure aggregation |

### Next Steps

Proceed to **Module 7: AI Fuzzing & Automated Exploitation** to learn about automated security testing.

---

## Quick Reference

| Vulnerability | Impact | Mitigation |
|--------------|--------|------------|
| Buffer Overflow | Code execution | Bounds checking |
| Backdoor | Hidden control | Trojan detection |
| Poisoning | Corrupted model | Data validation |
| Extraction | IP theft | Output masking |
| Gradient Leak | Data leakage | Secure aggregation |
