# AI Forge Project Lifecycle

Complete guide to the entire project lifecycle from initial setup through production deployment.

## 1. Project Initialization

### 1.1 Create Project Directory

```bash
mkdir my-ai-forge-project
cd my-ai-forge-project
```

### 1.2 Initialize Git

```bash
git init
git config user.name "Your Name"
git config user.email "your.email@example.com"
```

### 1.3 Install AI Forge

```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate

# Install AI Forge with training support
pip install "ai-forge[train]"

# Verify installation
ai-forge --version
ai-forge env check
```

### 1.4 Check System Requirements

```bash
# Check environment
ai-forge env check

# Output should show:
# ✓ Python version
# ✓ PyTorch installation
# ✓ GPU availability (if applicable)
# ✓ Memory available
# ✓ Disk space available
```

## 2. Data Preparation

### 2.1 Collect Raw Data

```bash
# Create data directory
mkdir -p data/raw
cd data/raw

# Download or create training data
# Example: Create sample data
cat > sample.jsonl << 'EOF'
{"messages": [{"role": "system", "content": "You are a helpful assistant"}, {"role": "user", "content": "What is machine learning?"}, {"role": "assistant", "content": "Machine learning is..."}]}
EOF

cd ../..
```

### 2.2 Explore Data

```bash
# Inspect data
ai-forge data inspect data/raw/sample.jsonl

# Get statistics
ai-forge data stats data/raw/sample.jsonl

# Check for issues
ai-forge data validate data/raw/sample.jsonl
```

### 2.3 Format Data

```bash
# Convert to training format
ai-forge data format \
  --input data/raw/sample.jsonl \
  --output data/formatted/train.jsonl \
  --format messages

# Verify formatted data
ai-forge data inspect data/formatted/train.jsonl
```

### 2.4 Split Data

```bash
# Create train/val/test splits
ai-forge data split \
  --input data/formatted/train.jsonl \
  --train-ratio 0.8 \
  --val-ratio 0.1 \
  --test-ratio 0.1 \
  --output-dir data/splits
```

### 2.5 Data Quality Assurance

```bash
# Check data quality
ai-forge data quality check data/splits/train.jsonl

# Remove duplicates
ai-forge data deduplicate \
  --input data/splits/train.jsonl \
  --output data/splits/train_dedup.jsonl

# Balance classes (if classification task)
ai-forge data balance \
  --input data/splits/train.jsonl \
  --output data/splits/train_balanced.jsonl
```

## 3. Model Selection & Configuration

### 3.1 Choose a Base Model

```bash
# List supported models
ai-forge models list

# Get model details
ai-forge models info meta-llama/Llama-2-7b

# Check VRAM requirements
ai-forge models vram meta-llama/Llama-2-7b
```

### 3.2 Initialize Training Configuration

```bash
# Create config for SFT training
ai-forge init \
  --task sft \
  --model meta-llama/Llama-2-7b \
  --output config.yaml

# View generated config
cat config.yaml

# Edit if needed
# nano config.yaml
```

### 3.3 Customize Configuration

Edit `config.yaml` to customize:

```yaml
# Model
model: meta-llama/Llama-2-7b

# Data
data:
  train: data/splits/train.jsonl
  val: data/splits/val.jsonl
  test: data/splits/test.jsonl

# Training
training:
  num_epochs: 3
  batch_size: 8
  learning_rate: 2e-4
  warmup_steps: 500

# PEFT (Parameter-Efficient Fine-Tuning)
peft:
  method: lora
  r: 8
  lora_alpha: 16
  target_modules: ["q_proj", "v_proj"]

# Evaluation
eval:
  eval_steps: 500
  eval_strategy: steps
  save_strategy: steps

# Output
output_dir: outputs/model
save_total_limit: 2
```

### 3.4 Validate Configuration

```bash
# Validate config
ai-forge validate config config.yaml

# Do a dry-run to check setup
ai-forge train --config config.yaml --dry-run

# See what would happen without actual training
```

## 4. Training Phase

### 4.1 Prepare for Training

```bash
# Create output directories
mkdir -p outputs logs

# Set environment variables
export CUDA_VISIBLE_DEVICES=0  # Use GPU 0
export TOKENIZERS_PARALLELISM=false

# Optional: Enable mixed precision for faster training
export TORCH_CUDA_ARCH_LIST="8.0;8.6;9.0"
```

### 4.2 Start Training

```bash
# Start training with config
ai-forge train --config config.yaml

# Or with command-line arguments
ai-forge train \
  --model meta-llama/Llama-2-7b \
  --data data/splits/train.jsonl \
  --task sft \
  --epochs 3 \
  --batch-size 8 \
  --learning-rate 2e-4

# Training output will be in outputs/
```

### 4.3 Monitor Training

In another terminal:

```bash
# Watch logs in real-time
tail -f outputs/logs/training.log

# Monitor GPU usage
watch -n 1 nvidia-smi

# Check tensorboard (if enabled)
tensorboard --logdir outputs/logs
```

### 4.4 Handle Interruptions

```bash
# If training is interrupted, resume from checkpoint
ai-forge train \
  --config config.yaml \
  --resume outputs/checkpoint-1000

# This will continue from the last checkpoint
```

### 4.5 Training Outputs

After training completes:

```bash
# Check outputs
ls -la outputs/

# Key files:
# - model/: Final model weights
# - checkpoint-*/: Intermediate checkpoints
# - logs/: Training logs and metrics
# - training_results.json: Final metrics
```

## 5. Evaluation Phase

### 5.1 Evaluate on Validation Set

```bash
# Run evaluation
ai-forge eval \
  --model outputs/model \
  --val-data data/splits/val.jsonl

# Output includes metrics like:
# - Perplexity
# - Loss
# - Token accuracy
```

### 5.2 Benchmark Against Baselines

```bash
# Compare with base model
ai-forge eval \
  --model outputs/model \
  --baseline meta-llama/Llama-2-7b \
  --val-data data/splits/val.jsonl

# Shows improvement metrics
```

### 5.3 Generate Evaluation Report

```bash
# Create detailed report
ai-forge eval \
  --model outputs/model \
  --val-data data/splits/val.jsonl \
  --format html \
  --output eval_report.html

# View report in browser
open eval_report.html
```

### 5.4 A/B Testing

```bash
# Compare two models
ai-forge eval compare \
  --model1 outputs/model \
  --model2 meta-llama/Llama-2-7b \
  --test-data data/splits/test.jsonl
```

## 6. Optimization Phase

### 6.1 Model Optimization

```bash
# Quantize model for inference
ai-forge quant \
  --model outputs/model \
  --method fp8 \
  --output outputs/model_quantized

# Or use GPTQ quantization
ai-forge quant \
  --model outputs/model \
  --method gptq \
  --output outputs/model_gptq
```

### 6.2 Export for Different Targets

```bash
# Export to GGUF format (for llama.cpp)
ai-forge export \
  --model outputs/model \
  --format gguf \
  --output outputs/model.gguf

# Export to ONNX format
ai-forge export \
  --model outputs/model \
  --format onnx \
  --output outputs/model.onnx
```

### 6.3 Test Inference Speed

```bash
# Benchmark inference
ai-forge benchmark \
  --model outputs/model \
  --batch-sizes 1,8,16 \
  --sequence-lengths 128,256,512

# Shows throughput and latency metrics
```

## 7. Serving & Deployment

### 7.1 Local Serving

```bash
# Start OpenAI-compatible server
ai-forge serve \
  --model outputs/model \
  --port 8000

# In another terminal, test the API
curl http://localhost:8000/v1/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "model",
    "prompt": "Hello, world!",
    "max_tokens": 100
  }'
```

### 7.2 Docker Deployment

```bash
# Create Dockerfile for model
cat > Dockerfile.model << 'EOF'
FROM ai-forge:latest
COPY outputs/model /model
ENTRYPOINT ["ai-forge", "serve", "--model", "/model"]
EOF

# Build image
docker build -f Dockerfile.model -t my-model:latest .

# Run container
docker run -p 8000:8000 my-model:latest
```

### 7.3 Cloud Deployment

```bash
# Deploy to cloud (example: Modal)
ai-forge deploy \
  --model outputs/model \
  --cloud modal \
  --name my-model

# Get endpoint URL
ai-forge deploy info --deployment my-model
```

### 7.4 Batch Inference

```bash
# Run batch inference
ai-forge generate \
  --model outputs/model \
  --input data/test_prompts.jsonl \
  --output results.jsonl \
  --batch-size 16
```

## 8. Monitoring & Maintenance

### 8.1 Production Monitoring

```bash
# Monitor model performance
ai-forge monitor \
  --model outputs/model \
  --metrics perplexity,latency,throughput

# Set up alerts
ai-forge monitor setup-alerts \
  --model outputs/model \
  --alert-perplexity-threshold 50
```

### 8.2 Model Versioning

```bash
# Create version of trained model
ai-forge version create \
  --model outputs/model \
  --tag v1.0 \
  --description "Initial SFT fine-tune"

# List versions
ai-forge version list

# Deploy specific version
ai-forge serve --model outputs/model:v1.0
```

### 8.3 Regular Evaluation

```bash
# Schedule periodic evaluation
# Create eval_schedule.yaml
cat > eval_schedule.yaml << 'EOF'
schedule:
  - frequency: weekly
    model: outputs/model
    data: data/splits/val.jsonl
  - frequency: monthly
    model: outputs/model
    data: data/splits/test.jsonl
    benchmark: true
EOF

# Run scheduled evaluations
ai-forge eval schedule eval_schedule.yaml
```

## 9. Documentation & Governance

### 9.1 Create Model Card

```bash
# Auto-generate model card
ai-forge card create \
  --model outputs/model \
  --training-data "Custom dataset (10K examples)" \
  --performance-metrics eval_report.json \
  --limitations "Trained on English only" \
  --output MODEL_CARD.md

# Add to repository
git add MODEL_CARD.md
```

### 9.2 Document Training

```bash
# Create training report
cat > TRAINING_REPORT.md << 'EOF'
# Training Report

## Model
- Base: meta-llama/Llama-2-7b
- Method: SFT

## Data
- Training: 8000 examples
- Validation: 1000 examples
- Test: 1000 examples

## Training Config
- Epochs: 3
- Batch size: 8
- Learning rate: 2e-4

## Results
- Final loss: 1.24
- Perplexity: 3.45
- Improvement: 15% over baseline

## Inference
- Framework: vLLM
- Quantization: FP8
- Latency: 23ms
- Throughput: 45 tokens/sec
EOF

git add TRAINING_REPORT.md
```

### 9.3 Commit and Tag Release

```bash
# Commit model and documentation
git add outputs/model
git add TRAINING_REPORT.md
git commit -m "chore: add fine-tuned model v1.0"

# Create release tag
git tag -a v1.0 -m "Release fine-tuned model v1.0"

# Push to repository
git push origin main
git push origin v1.0
```

## 10. Continuous Improvement

### 10.1 Analyze Results

```bash
# Compare training runs
ai-forge analyze compare \
  --run1 outputs/run1 \
  --run2 outputs/run2

# Identify improvements
# Look for:
# - Loss curves
# - Metric trends
# - Training time differences
```

### 10.2 Iterate & Improve

```bash
# Based on results, adjust and retrain:
# 1. Change hyperparameters in config.yaml
# 2. Add more training data
# 3. Try different PEFT method
# 4. Use different base model

# Save current config as baseline
cp config.yaml config_baseline.yaml

# Make changes
# nano config.yaml

# Train new version
ai-forge train --config config.yaml
```

### 10.3 A/B Test Improvements

```bash
# Compare old vs new model
ai-forge eval compare \
  --model1 outputs/model_v1 \
  --model2 outputs/model_v2 \
  --test-data data/splits/test.jsonl \
  --statistical-test t-test

# Choose best performer for deployment
```

## Checklist

### Before Initial Training
- [ ] Data collected and validated
- [ ] Data split into train/val/test
- [ ] Base model selected
- [ ] Configuration created and validated
- [ ] Environment checked and GPU available
- [ ] Backup of original data created

### After Training
- [ ] Training completed without errors
- [ ] Checkpoints saved properly
- [ ] Training logs reviewed
- [ ] Model evaluation completed
- [ ] Results documented
- [ ] Model versioned and tagged

### Before Production Deployment
- [ ] Comprehensive evaluation completed
- [ ] Performance meets requirements
- [ ] Security and compliance checks passed
- [ ] Model card created
- [ ] Documentation complete
- [ ] Monitoring setup configured
- [ ] Rollback plan created

### Ongoing (Monthly)
- [ ] Model performance monitored
- [ ] New data evaluated for retraining
- [ ] Security patches applied
- [ ] Documentation updated
- [ ] Usage metrics reviewed

---

**Next Steps**: See [README.md](README.md) for quick start and [docs/](docs/) for detailed guides.
